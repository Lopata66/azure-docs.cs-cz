---
title: Akcelerátor řešení vzdálené monitorování – nejčastější dotazy | Dokumentace Microsoftu
description: Nejčastější dotazy k akcelerátoru řešení vzdáleného monitorování
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 59b52163ba7f5abd53c60264f10f5352e3e4f6cd
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49067784"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Nejčastější dotazy k akcelerátoru řešení vzdáleného monitorování

Viz také Obecné [nejčastější dotazy k](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Kolik stojí zřídit nové řešení vzdáleného monitorování?

Nový akcelerátor řešení nabízí dvě možnosti nasazení:

* A *základní* možnost navržené pro vývojáře hledající nižší náklady na vývoj nebo zákazníci, kteří chtějí vytvořit ukázku nebo testování konceptu.
* A *standardní* možnost určená pro podniky, které se chce k nasazení infrastruktury připravené pro produkční prostředí.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Jak můžete zajistit, že mám snížili Moje náklady při vyvíjet Moje řešení?

Kromě toho, že dvě odlišné nasazení nové řešení vzdáleného monitorování obsahují nastavení, které povolí nebo zakáže všechna Simulovaná zařízení, na vyžádání. Data přijatá v řešení a tím i celkové náklady na zakázání simulace snižuje.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Jaký je rozdíl mezi možnostmi nasazení basic a standard? Jak se mám rozhodnout mezi možnostmi dvě nasazení?

Jednotlivé možnosti nasazení jsou reaguje na různé potřeby. Základní nasazení slouží k zahájení práce a vývoj PoC a Windows. Poskytuje zjednodušenou architektura s minimální potřebné prostředky a s nižšími náklady. Standardní nasazení slouží k vytváření a přizpůsobení řešení připravené pro produkční prostředí a umožňuje nasazení s nezbytné prvky, které mají Pamatujte si, že. Spolehlivost a škálování mikroslužby aplikací jsou vytvořené jako kontejnery Dockeru a nasazují pomocí orchestrátoru (Kubernetes ve výchozím nastavení). Orchestrátor zodpovídá za nasazení, škálování a Správa aplikace. Měli byste zvolit možnost podle aktuální potřeby. Můžete použít jednu, druhý nebo kombinace obou v závislosti na fázi vašeho projektu.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Jak konfigurovat dynamickou mapu na řídicím panelu

Další informace najdete v tématu [klíč upgradu mapě zobrazíte zařízení na mapě dynamické](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Kde najdu informace o předchozí verzi řešení vzdáleného monitorování

Předchozí verze akcelerátor řešení vzdálené monitorování se označuje jako řešení vzdáleného monitorování IoT Suite předkonfigurované. Můžete najít Archivovaná dokumentace na [ https://docs.microsoft.com/previous-versions/azure/iot-suite/ ](https://docs.microsoft.com/previous-versions/azure/iot-suite/).

### <a name="next-steps"></a>Další postup

Můžete si také prostudovat některé další funkce a možnosti akcelerátorů řešení IoT:

* [Prozkoumejte možnosti akcelerátor řešení vzdálené monitorování](quickstart-remote-monitoring-deploy.md)
* [Přehled akcelerátorů řešení prediktivní údržby](iot-accelerators-predictive-overview.md)
* [Nasazení akcelerátoru řešení připojená továrna](quickstart-connected-factory-deploy.md)
* [Zabezpečení IoT od počátku](/azure/iot-fundamentals/iot-security-ground-up)
