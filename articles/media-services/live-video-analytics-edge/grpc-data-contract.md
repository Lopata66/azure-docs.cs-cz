---
title: kontrakt dat rozšíření gRPC – Azure
description: V tomto článku se dozvíte, jak pomocí protokolu gRPC posílat zprávy mezi modulem Live video Analytics a vlastním rozšířením AI nebo CV.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: f2d93fb062ab3d6963177e7ece15df6bfe5b42cb
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421488"
---
# <a name="grpc-extension-data-contract"></a>kontrakt dat rozšíření gRPC

V tomto článku se dozvíte, jak pomocí protokolu gRPC posílat zprávy mezi modulem Live video Analytics a vlastním rozšířením AI nebo CV.

gRPC je moderní, open source a vysoce výkonné rozhraní RPC, které běží v jakémkoli prostředí. Služba gRPC Transport používá obousměrné streamování HTTP/2 mezi:

* klient gRPC (Live video Analytics v modulu IoT Edge) a 
* Server gRPC (vaše vlastní rozšíření).

Relace gRPC je jediné připojení od klienta gRPC k serveru gRPC přes port TCP/TLS. 

V jedné relaci: klient pošle popisovač streamování médií následovaný snímky videa na server jako zprávu [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) prostřednictvím relace gRPC streamu. Server ověří popisovač streamu, analyzuje snímek videa a vrátí výsledky odvození jako protobuf zprávu.

![kontrakt rozšíření gRPC](./media/data-contracts/grpc.png)

## <a name="implementing-grpc-protocol"></a>Implementace protokolu gRPC

### <a name="creating-a-grpc-connection"></a>Vytváření připojení gRPC

Vlastní rozšíření musí implementovat tuto službu gRPC:

```
service MediaGraphExtension {
  rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
}
```

Když se tato akce zavolá, otevře se obousměrný datový proud pro zprávy, které se budou přenášet mezi rozšířením gRPC a grafem Live video Analytics. První zpráva odeslaná v tomto datovém proudu každou stranou bude obsahovat MediaStreamDescriptor, který definuje, jaké informace se budou odesílat v následujících MediaSamples.

Například může rozšíření grafu odeslat zprávu (vyjádřenou ve formátu JSON) a označit tak, že bude odesílat 416x416 zakódované Rgb24 bloky vložené do zpráv gRPC do vlastního rozšíření.

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": {
        "graph_identifier": {
            "media_services_arm_id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/microsoft.media/mediaservices/mediaAccountName",
            "graph_instance_name": "mediaGraphName",
            "graph_node_name": "grpcExtension"
        },
        "media_descriptor": {
            "timescale": 90000,
            "video_frame_sample_format": {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

Vlastní rozšíření by v reakci poslalo následující zprávu, aby označovala, že vrací pouze odvození.

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": {
        "extension_identifier": "customExtensionName"    }
}
```

Teď, když se na obou stranách vyměnily popisovače médií, začne dynamická analýza videa zasílat do tohoto rozšíření snímky.

### <a name="sequence-numbers"></a>Pořadová čísla

Uzel rozšíření gRPC a vlastní rozšíření uchovávají samostatnou sadu pořadových čísel, která jsou přiřazena ke svým zprávám. Tato pořadová čísla by se měla rovnoměrně zvětšující zvýšit od 1. `ack_sequence_number` může být ignorováno, pokud není potvrzena žádná zpráva, což může nastat při odeslání první zprávy.

Po úplném zpracování odpovídající zprávy musí být žádost potvrzena. V případě přenosu sdílené paměti indikuje toto potvrzení, že odesílatel může uvolnit sdílenou paměť a příjemce k ní již nebude přistupovat. Odesílatel musí uchovávat veškerou sdílenou paměť, dokud nebyla potvrzena.

### <a name="reading-embedded-content"></a>Čtení vloženého obsahu

Vložený obsah se může číst přímo ze `MediaSample` zprávy prostřednictvím `content_byte` pole s. Data budou kódována podle `MediaDescriptor` .

### <a name="reading-content-from-shared-memory"></a>Čtení obsahu ze sdílené paměti

Při přenosu obsahu přes sdílenou paměť odesílatel zahrne `SharedMemoryBufferTransferProperties` do svého `MediaStreamDescriptor` prvního navázání relace. Může to vypadat takto (vyjádřeno ve formátu JSON):

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

Příjemce pak soubor otevře `/dev/shm/inference_client_share_memory_2146989006636459346` . Odesílatel pošle `MediaSample` zprávu, která obsahuje `ContentReference` odkaz na konkrétní místo v tomto souboru.

```
{
    "timestamp": 143598615750000,
    "content_reference": {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

Příjemce pak přečte data z tohoto umístění v souboru.

Aby kontejner Live video Analytics mohl komunikovat přes sdílenou paměť, musí být správně nakonfigurován režim IPC. To lze provést mnoha způsoby, ale tady je několik doporučených konfigurací.

* Při komunikaci s modulem Inferencing gRPC běžícím na hostitelském zařízení by měl být režim IPC nastavený na Host.
* Při komunikaci se serverem gRPC spuštěným v jiném modulu IoT Edge by měl být režim IPC nastavený na sdílení pro modul Live video Analytics a `container:liveVideoAnalytics` pro vlastní rozšíření, kde `liveVideoAnalytics` je název modulu Live video Analytics.

V takovém případě se to může podobat tomu, že se zařízení bude líbit pomocí první možnosti výše.

```
"liveVideoAnalytics": {
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": {
    "image": "mcr.microsoft.com/media/live-video-analytics:1",
    "createOptions": 
      "HostConfig": {
        "IpcMode": "host"
      }
    }
  }
}
```

Další informace o režimech IPC naleznete v tématu https://docs.docker.com/engine/reference/run/#ipc-settings---ipc .

## <a name="media-graph-grpc-extension-contract-definitions"></a>Definice kontraktu rozšíření gRPC Media graphu

Tato část definuje kontrakt gRPC definující tok dat.

### <a name="protocol-messages"></a>Zprávy protokolu

![Zprávy protokolu](./media/data-contracts/grpc2.png)
 
### <a name="client-authentication"></a>Ověření klienta

Implementátori vlastních rozšíření můžou ověřit pravost příchozích připojení gRPC, abyste měli jistotu, že pocházejí z uzlu rozšíření gRPC. Uzel poskytne záznam v hlavičce požadavku k ověření proti.

K tomu můžete použít přihlašovací údaje uživatelského jména a hesla. Při vytváření uzlu rozšíření gRPC se přihlašovací údaje poskytují jako níže:

```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "tcp://customExtension:8081",
    "credentials": {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

Po odeslání žádosti gRPC bude v metadatech žádosti mimicking základní ověřování HTTP, které obsahuje následující hlavičku.

`x-ms-authentication: Basic (Base64 Encoded username:password)`

## <a name="using-grpc-over-tls"></a>Použití gRPC přes TLS

Připojení gRPC používané pro Inferencing může být zabezpečené přes TLS. To je užitečné v situacích, kdy není možné zaručit zabezpečení sítě mezi živou analýzou videa a modulem Inferencing. TLS zašifruje veškerý obsah vložený do gRPC zpráv, což způsobí zvýšení režie procesoru při přenášení snímků s vysokou mírou.

Možnosti ověřování IgnoreHostname a IgnoreSignature nejsou podporovány serverem gRPC, takže certifikát serveru, který modul Inferencing prezentuje, musí obsahovat CN, který přesně odpovídá IP adrese nebo názvu hostitele v adrese URL koncového bodu uzlu rozšíření gRPC.

## <a name="next-steps"></a>Další kroky

[Další informace o schématu odvozených metadat](inference-metadata-schema.md)
