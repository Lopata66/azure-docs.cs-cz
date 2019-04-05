---
title: Koncepty zařízení pro neukončil pro Azure IoT Hub Device Provisioning Service | Dokumentace Microsoftu
description: Popisuje zařízení neukončil koncepty pro Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: fa8cb29f145c7658227f93d08a990c98563a0cfc
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050845"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Koncepty neukončil zařízení centra IoT

Během životního cyklu řešení IoT se běžně přesouvat zařízení mezi IoT huby. Důvody pro tento krok může zahrnovat následující scénáře:

* **Informace o zeměpisné poloze / GeoLatency**: Při přesunu zařízení mezi umístěními, zlepší se latence sítě tím, že zařízení migrovat do blíže služby IoT hub.

* **Víceklientská architektura**: Zařízení může používat ve stejném řešení IoT a přiřadit k novým zákazníkům nebo Web zákazníka. Tento nový zákazník může údržba používání různých služby IoT hub.

* **Změna řešení**: Zařízení může přesunout do nové nebo aktualizované řešení IoT. Této změně přiřazení může vyžadovat, aby zařízení ke komunikaci s nového centra IoT, která je připojena k jiné komponenty back-end.

* **Karantény**: Podobně jako řešení změnit. Zařízení, které nepracuje správně, dojde k ohrožení bezpečnosti nebo zastaralý může být přeřazen do služby IoT hub, která se dá jenom aktualizovat a získat zpět v dodržování předpisů. Když zařízení pracuje správně, je potom migrovat zpět na jeho hlavní centra.

Neukončil podpory v rámci služby Device Provisioning adresy těchto potřeb. Zařízení lze automaticky přiřadit nový na základě reprovisioning zásad, který je nakonfigurovaný na položku registrace zařízení služby IoT hub.

## <a name="device-state-data"></a>Data o stavu zařízení

Data o stavu zařízení se skládá z [dvojče zařízení](../iot-hub/iot-hub-devguide-device-twins.md) a možnosti zařízení. Tato data uložená v instanci služby Device Provisioning a centrem IoT, přiřazená zařízení.

![Zřizování s Device Provisioning Service](./media/concepts-device-reprovisioning/dps-provisioning.png)

Když v zařízení je zpočátku zřízené instanci služby Device Provisioning, následující kroky se provádějí:

1. Zařízení odešle žádost o zřízení instance služby Device Provisioning. Instance služby ověřuje identitu zařízení podle položku registrace a vytvoří počáteční konfiguraci dat o stavu zařízení. Instance služby přiřadí zařízení do služby IoT hub na základě konfigurace registrace a vrátí přiřazení IoT hub do zařízení.

2. Zřízení instance služby poskytuje kopii žádná data stavu počáteční zařízení k přiřazené Centrum IoT. Zařízení se připojí k přiřazené Centrum IoT a zahájí operace.

V průběhu času může aktualizovat data o stavu zařízení ve službě IoT hub [operace zařízení](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) a [back endové operace](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). Informace o stavu počáteční zařízení uložené v instanci služby Device Provisioning zůstává beze změny. Tato data zařízení beze změny stavu je počáteční konfigurace.

![Zřizování s Device Provisioning Service](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

V závislosti na scénáři při přesunu zařízení mezi IoT huby, může také být potřeba migrovat stav zařízení aktualizovat na předchozí služby IoT hub přes na novou službu IoT hub. Tato migrace podporuje neukončil zásady ve službě Device Provisioning.

## <a name="reprovisioning-policies"></a>Neukončil zásady

V závislosti na scénáři zařízení obvykle odešle požadavek na instanci služby zřizování při restartování. Podporuje také metodu k ruční aktivaci zřizování na vyžádání. Reprovisioning zásad na položku registrace Určuje, jak zařízení zřizování instance služby zpracovává tyto žádosti o zřízení. Zásady také určuje, zda mají být migrovány data o stavu zařízení během neukončil. Jsou dostupné pro jednotlivé registrace a skupin registrací stejné zásady:

* **Znovu zřídit a migraci dat**: Tato zásada je výchozí pro nové záznamy registrace. Tato zásada pořizuje akce, když zařízení přidružená k položce registrace odešlete novou žádost (1). V závislosti na konfiguraci položku registrace zařízení může být přeřazen jiné služby IoT hub. Pokud zařízení se mění centra IoT hub, odebere se registrace zařízení s počáteční služby IoT hub. Informace o stavu aktualizace zařízení z tohoto počátečního služby IoT hub se budou migrovat do nového centra IoT (2). Během migrace, bude hlásit stav tohoto zařízení **přiřazování**.

    ![Zřizování s Device Provisioning Service](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Znovu zřídit a resetování na počáteční konfigurace**: Tato zásada pořizuje akce, když zařízení přidružená k položce registrace odešlete novou žádost o zřízení (1). V závislosti na konfiguraci položku registrace zařízení může být přeřazen jiné služby IoT hub. Pokud zařízení se mění centra IoT hub, odebere se registrace zařízení s počáteční služby IoT hub. Počáteční konfigurace data, která zřizování instance služby přijal při zřizování zařízení je k dispozici na novou službu IoT hub (2). Během migrace, bude hlásit stav tohoto zařízení **přiřazování**.

    Tato zásada se často používá pro výrobního beze změny centra IoT hub.

    ![Zřizování s Device Provisioning Service](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Nikdy znovu zřídit**: Zařízení je nikdy přiřazena k jinému rozbočovači. Tato zásada je k dispozici pro správu zpětné kompatibility.

### <a name="managing-backwards-compatibility"></a>Správa zpětné kompatibility

Přiřazení zařízení do IoT hubů před. září 2018, měli vždy navrchu chování. Když zařízení zpět do procesu zřizování, by přiřadit jenom zpět do stejné služby IoT hub.

Pro řešení, které mají závislosti na toto chování zřizovací služba zahrnuje zpětné kompatibility. V současné době toto chování je zachován z důvodu zařízení podle následujících kritérií:

1. Připojit zařízení s verzí rozhraní API před dostupnost nativní podporu neukončil ve službě Device Provisioning. Najdete v následující tabulce rozhraní API.

2. Položky registrace zařízení nemá reprovisioning zásadám nastaveným na ně.

Tato kompatibilita zajišťuje, který již dříve nasadili prostředí zařízení stejné chování, která je k dispozici během počátečního testování. Pokud chcete zachovat předchozí chování, neukládat reprovisioning zásady pro tyto registrace. Pokud je nastavena reprovisioning zásad, reprovisioning zásad má přednost před chování. Tím, že reprovisioning zásada přednost, můžete zákazníky aktualizovat chování zařízení bez nutnosti znovu připojit bitovou kopii zařízení.

Následující vývojový diagram umožňuje zobrazit, když je k dispozici chování:

![zpětné kompatibility vývojový diagram](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

Verze rozhraní API před dostupnost nativní podporu neukončil ve službě Device Provisioning v následující tabulce:

| REST API | C SDK | Python SDK |  Node SDK | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 a starší](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 a starší](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 a starší](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 nebo starší](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 nebo starší](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 nebo dřívější](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Tyto hodnoty a odkazy se budou pravděpodobně měnit. Toto je pouze požadavku k určení, kde můžete určit verze ze strany zákazníka a očekávaná verze, které je budou na zástupný symbol.

## <a name="next-steps"></a>Další postup

* [Jak na bázi zařízení](how-to-reprovision.md)
