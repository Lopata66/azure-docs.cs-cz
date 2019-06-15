---
title: Brány pro příjem dat zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Vytvoření transparentní, neprůhledné nebo proxy zařízení brány, který odesílá data z více podřízených zařízení do cloudu nebo místně procesů pomocí Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c3a49c4333838652f7063d6a89cfd8cceace1cf8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054200"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Použití zařízení IoT Edge jako brána

Brány v řešeních pro IoT Edge poskytují hraniční analýzu, do zařízení IoT, které by jinak mají tyto funkce a připojení zařízení. Azure IoT Edge umožňuje splňovat všechny požadavky pro brány IoT bez ohledu na to, zda se vztahují k připojení, identity nebo hraniční analýzu. Vzory brány v tomto článku odkazovat pouze na vlastnosti připojení zařízení příjem dat a identity zařízení, jak se zpracovávají data zařízení v bráně.

## <a name="patterns"></a>Vzory

Existují tři vzory pro používání zařízení IoT Edge jako brána: transparentní, protokol, překlad a překladu identity:
* **Transparentní** – zařízení, která teoreticky by se mohl připojit ke službě IoT Hub můžete místo toho připojit k zařízení brány. Příjem dat zařízení mají své vlastní identity služby IoT Hub a používáte žádný protokol MQTT, AMQP nebo HTTP. Brána jednoduše předává komunikaci mezi zařízením a službou IoT Hub. Zařízení neberou v úvahu, že budou komunikovat s cloudem pomocí brány a uživatelské interakce s zařízení ve službě IoT Hub nebude vědět o zařízení zprostředkující brány. Proto je transparentní brány. Odkazovat na [vytvoření transparentní brány](how-to-create-transparent-gateway.md) pro konkrétní použití zařízení IoT Edge jako transparentní brána.
* **Protokol překladu** – taky známé jako model neprůhledné brány, zařízení, které nepodporují protokol MQTT, AMQP nebo HTTP můžete použít zařízení brány k odesílání dat do služby IoT Hub jejich jménem. Brána rozumí protokol používaný podřízené zařízeními a je jenom zařízení, které má identitu ve službě IoT Hub. Informace o veškerém pravděpodobně pochází z jednoho zařízení brány. Příjem dat zařízení musíte vložit další identifikační informace ve zprávách, pokud cloudové aplikace, aby k analýze dat na každé zařízení zvlášť. Kromě toho služby IoT Hub primitiv, jako jsou dvojčata a metody jsou dostupné jenom pro zařízení brány, nejsou podřízené zařízení.
* **Překlad identity** – zařízení, která se nemůže připojit ke službě IoT Hub můžete místo toho připojit k zařízení brány. Brána zajišťuje identity a protokolu překladu jménem podřízené zařízení služby IoT Hub. Brána je dostatečně inteligentní, aby pochopit protokol používá podřízené zařízení, je poskytovat identitu a překládat primitiv služby IoT Hub. Příjem dat zařízení objeví ve službě IoT Hub jako první kategorie zařízení pomocí dvojčat a metody. Uživatel může komunikovat se zařízeními ve službě IoT Hub a nebude vědět o zařízení zprostředkující brány.

![Diagram – transparentní, protokol a vzory brány identity](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Případy použití
Všechny vzory gateway poskytuje následující výhody:
* **Analytics na hraničních zařízeních** – AI pomocí služeb místně ke zpracování dat přicházejících z podřízené zařízení bez odesílání telemetrie Plnohodnotná do cloudu. Najít a reagovat na přehledy místně a odesílat pouze podmnožinu dat do služby IoT Hub. 
* **Příjem dat zařízení izolace** – zařízení brány můžete chránit všechna zařízení příjem dat z vystavení k Internetu. To může být umístěn souběžně mezi OT sítě, která nemá připojení a IT síť, která poskytuje přístup k webu. 
* **Připojení multiplexing** – všechna zařízení připojující se ke službě IoT Hub pomocí brány IoT Edge stejné základní připojení.
* **Vyhlazení provoz** -zařízení hraničních zařízeních IoT se automaticky implementovat exponenciálního omezení rychlosti, pokud služby IoT Hub omezuje provoz, při trvalém ukládání zpráv místně. Tuto výhodu provede řešení odolné špičkami v provozu.
* **Podporu offline režimu** – zařízení brány ukládá zprávy a aktualizace dvojčete nelze doručit do služby IoT Hub.

Bránu, která překlad protokolu. můžete také provádět hraniční analýzu, izolace zařízení, vyhlazování provoz a podporu offline režimu stávající zařízení a nová zařízení, která jsou omezené prostředků. Mnoho existujících zařízení vytvářejí data, která může power byznysu; však obvykle nebyly navrženy pomocí připojení ke cloudu v úvahu. Neprůhledný brány umožní tato data k odemknutí a použít v řešení IoT.

Bránu, která provádí překlad identity přináší výhody překlad protokolu a kromě toho umožňuje úplné možnosti správy zařízení pro příjem dat z cloudu. Všechna zařízení v řešení IoT se zobrazí ve službě IoT Hub bez ohledu na to, které používají protokol.

## <a name="cheat-sheet"></a>Tahák
Tady je rychlý tahák, která porovnává primitiv služby IoT Hub, při použití transparentní, neprůhledné (protocol) a proxy server brány.

| &nbsp; | Transparentní brány | Překlad protokolu | Překlad identity |
|--------|-------------|--------|--------|
| Identity, které jsou uložené v registru identit služby IoT Hub | Identity všechna připojená zařízení | Pouze identity zařízení brány | Identity všechna připojená zařízení |
| Dvojče zařízení | Každého připojeného zařízení má svůj vlastní dvojče zařízení | Jenom brána má dvojčata zařízení a modul | Každého připojeného zařízení má svůj vlastní dvojče zařízení |
| Přímé metody a zprávy typu cloud zařízení | Cloud jednotlivě adresu každého připojeného zařízení | Cloud může vyřešit pouze zařízení brány | Cloud jednotlivě adresu každého připojeného zařízení |
| [IoT Hub omezení a kvóty](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Platí pro každé zařízení | Platí pro zařízení brány | Platí pro každé zařízení |

Při použití model neprůhledné brány (překlad protokolu), všechna zařízení, připojení přes tuto bránu sdílet stejné fronty typu cloud zařízení, která může obsahovat maximálně 50 zprávy. Vyplývá, že model neprůhledné brány by měla sloužit pouze v případě, že se několik zařízení připojují prostřednictvím brány každé pole a je malý provoz jejich typu cloud zařízení.

## <a name="next-steps"></a>Další postup

Naučíte se nastavit transparentní brána: 

* [Konfigurace zařízení tak, aby fungoval jako transparentní brána IoT Edge](how-to-create-transparent-gateway.md)
* [Ověření podřízené zařízení do služby Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Připojte zařízení za příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md)