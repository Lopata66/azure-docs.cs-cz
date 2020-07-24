---
title: Spravovaná virtuální síť & spravované privátní koncové body
description: Přečtěte si o spravovaných virtuálních sítích a spravovaných privátních koncových bodech v Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/26/2020
ms.openlocfilehash: f6868e3a77cbc4b262180b47ed3b387840062969
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096673"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory spravované Virtual Network (Preview)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto článku se vysvětlují spravované Virtual Network a spravované privátní koncové body v Azure Data Factory.


## <a name="managed-virtual-network"></a>Spravovaná virtuální síť

Když vytváříte Azure Integration Runtime (IR) v rámci služby Azure Data Factory Managed Virtual Network (VNET), modul runtime integrace se zřídí se spravovaným Virtual Networkem a použije privátní koncové body pro zabezpečené připojení k podporovaným úložištím dat. 

Vytváření Azure IR v rámci spravované Virtual Network zajišťuje, že proces integrace dat je izolovaný a bezpečný. 

Výhody použití spravovaných Virtual Network:

- Pomocí spravovaného Virtual Network můžete přesměrovat zátěž při správě Virtual Network na Azure Data Factory. Nemusíte vytvářet podsíť pro Azure Integration Runtime, která by mohla z vašeho Virtual Network použít spoustu privátních IP adres a měla by to vyžadovat předchozí plánování síťové infrastruktury. 
- K bezpečné integraci dat nepotřebuje rozsáhlá síťová znalost Azure. Místo toho, abyste mohli začít používat zabezpečenou ETL, je pro odborníky na data mnohem zjednodušená. 
- Spravované Virtual Network společně se spravovanými soukromými koncovými body chrání před exfiltrace dat. 

> [!IMPORTANT]
>V současné době je spravovaná síť VNet podporovaná jenom ve stejné oblasti jako Azure Data Factory oblast.
 

![Architektura Virtual Network spravovaná přes ADF](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>Spravované privátní koncové body

Spravované privátní koncové body jsou privátní koncové body vytvořené v Azure Data Factory spravované Virtual Network vytvoření privátního odkazu na prostředky Azure. Azure Data Factory tyto privátní koncové body spravuje vaším jménem. 

![Nový spravovaný privátní koncový bod](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory podporuje privátní odkazy. Privátní odkaz vám umožní přístup ke službám Azure (PaaS) (například Azure Storage, Azure Cosmos DB, Azure SQL Data Warehouse).

Když použijete privátní propojení, přenosy mezi úložišti dat a spravovaným Virtual Network procházejí výhradně přes páteřní síť Microsoftu. Soukromé propojení chrání před riziky exfiltrace dat. Vytvořením privátního koncového bodu vytvoříte privátní odkaz na prostředek.

Privátní koncový bod používá privátní IP adresu ve spravovaném Virtual Network k účinnému uvedení služby do IT. Soukromé koncové body jsou namapované na konkrétní prostředek v Azure, a ne na celou službu. Zákazníci můžou omezit připojení ke konkrétnímu prostředku schválenému jeho organizací. Přečtěte si další informace o [privátních odkazech a soukromých koncových bodech](https://docs.microsoft.com/azure/private-link/).

> [!NOTE]
> Doporučuje se vytvořit spravované privátní koncové body pro připojení ke všem zdrojům dat Azure. 
 
> [!WARNING]
> Pokud je v úložišti dat PaaS (objekt blob, ADLS Gen2, SQL DW) již vytvořený privátní koncový bod, a to i v případě, že umožňuje přístup ze všech sítí, může k němu přistupovat pomocí spravovaného privátního koncového bodu. Ujistěte se, že v takových scénářích vytvoříte privátní koncový bod. 

Při vytváření spravovaného privátního koncového bodu v Azure Data Factory se připojení privátního koncového bodu vytvoří ve stavu čeká na vyřízení. Byl zahájen pracovní postup schválení. Vlastník prostředku privátního propojení zodpovídá za schválení nebo odmítnutí připojení.

![Správa privátního koncového bodu](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

Pokud vlastník připojení schválí, vytvoří se privátní odkaz. V opačném případě se privátní odkaz nevytvoří. V obou případech se spravovaný privátní koncový bod aktualizuje se stavem připojení.

![Schválení spravovaného privátního koncového bodu](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

Pouze spravovaný privátní koncový bod ve schváleném stavu může odesílat provoz do daného prostředku privátního propojení.

## <a name="limitations-and-known-issues"></a>Omezení a známé problémy
### <a name="supported-data-sources"></a>Podporované zdroje dat
Níže jsou podporovány zdroje dat pro připojení prostřednictvím privátního odkazu ze Virtual Network spravovaných pomocí ADF.
- Azure Blob Storage
- Azure Table Storage
- Azure Files
- Azure Data Lake Gen2
- Azure SQL Database (nezahrnuje spravovanou instanci SQL Azure)
- Azure SQL Data Warehouse
- Azure CosmosDB SQL
- Azure Key Vault

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>Odchozí komunikace prostřednictvím veřejného koncového bodu ze spravovaného ADF Virtual Network
- Pro odchozí komunikaci je otevřen pouze port 443.
- Azure Storage a Azure Data Lake Gen2 se nepodporují připojení prostřednictvím veřejného koncového bodu ze spravovaného Virtual Network ADF.

### <a name="other-known-issues"></a>Další známé problémy
Ladění běhu pro připojení CosmosDB nefunguje, včetně ladění toku dat a ladění kanálu.


## <a name="next-steps"></a>Další kroky

- Kurz: [sestavení kanálu kopírování pomocí spravovaných Virtual Network a privátních koncových bodů](tutorial-copy-data-portal-private.md) 
- Kurz: [Vytvoření mapování kanálu toku dat pomocí spravovaných Virtual Network a privátních koncových bodů](tutorial-data-flow-private.md)
