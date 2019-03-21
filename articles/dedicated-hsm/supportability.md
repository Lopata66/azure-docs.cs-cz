---
title: Možnosti podpory – vyhrazené Azure HSM | Dokumentace Microsoftu
description: Možnosti podpory a oblasti zodpovědnosti pro vyhrazené modulu hardwarového zabezpečení Azure v různých scénářích
services: dedicated-hsm
author: johndaw
manager: barbkess
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/18/2019
ms.author: barclayn
ms.openlocfilehash: 8e4bbe3a5e2de8ba919cf0b641ee4e6776c5e8d4
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225548"
---
# <a name="azure-dedicated-hsm-supportability"></a>Možnosti podpory Azure vyhrazených HSM

HSM služby Azure Dedicated fyzické zařízení pro použití výhradně zákazník poskytuje kompletní správu ovládacího prvku a správu odpovědnost. Je zařízení k dispozici [Gemalto SafeNet Luna 7 HSM modelu A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Microsoft bude mít přístup žádný správce jednou zřízené ze strany zákazníka, nad rámec fyzické sériového portu přílohy monitorování roli.  Bez přístupu Microsoft může mít žádné probíhající softwaru úrovně údržby nebo systém správy odpovědnosti. V důsledku toho zodpovědností zákazníků typické provozní činnosti.
Zákazníci odpovídají plně pro aplikace, které využívají moduly hardwarového zabezpečení a by měla fungovat s Gemalto podpory nebo na základě consulting o pomoc. Z důvodu rozsahu zákazníka vlastnictví provozní kontrolu není možné pro společnost Microsoft nabízí jakýkoli druh se zárukou vysoké dostupnosti pro tuto službu. Je zodpovědností zákazníka k zajištění jejich aplikace jsou správně nakonfigurované k dosažení vysoké dostupnosti. Microsoft bude sledovat a udržovat zařízení stavu a připojení k síti.

## <a name="getting-support"></a>Získání podpory

Zákaznickou podporu pro vyhrazené HSM se podílí Gemalto a Microsoftu. Žádné problémy s hardwarem nebo problémů se sítí cesta vyřešíme společností Microsoft a cokoli, co dělat s skutečné hardwarového zabezpečení, jako je například konfigurace, softwaru, firmwaru a vývoj aplikací, bude ji adresovat podle Gemalto. Tento model podpory zajišťuje nejrychlejší cestu nejúčinnější podpory. Pokud máte pochybnosti s konkrétním problémem, žádost o podporu u Microsoftu a jsme se ujistěte se, že jsou správně směrované. Společnost Microsoft bude zůstat zahrnuta ve všech scénářích podpory a snažte se o nejlepší prostředí podpory pro naše zákazníky.

## <a name="gemalto-support"></a>Podpora Gemalto

Zákazníci, kteří používají službu HSM vyhrazené nárok na podporu – od Gemalto podle jejich Plus plán podpory. Tento postup vyžaduje právě proces registrace pomocí portálu pro podporu Gemalto. ID zákazníka a pokyny, poskytneme vám to jako součást počáteční zapojení s Microsoftem a získat přístup ke službě vyhrazené modulu hardwarového zabezpečení. Mechanismus pro získání podpory od Gemalto je prostřednictvím jejich [zákaznického portálu služeb podpory](https://supportportal.gemalto.com/csm/).
Zásadním aspektem ze Poznámka je, že se veškerý software a dokumentace muset použít modul hardwarového zabezpečení (třeba software pro klientský přístup a sad SDK) poskytují Gemalto přes stahování na zákaznický portál podpory.

### <a name="software-components"></a>Softwarové komponenty

V konfiguraci modulu hardwarového zabezpečení zařízení se používají různé softwarové součásti:

* Klientský software
* Sada SDK
* Nástroje

### <a name="guidance"></a>Doprovodné materiály

Gemalto je k dispozici správu a konfiguraci pokyny prostřednictvím [zákaznického portálu služeb podpory](https://supportportal.gemalto.com/csm/). Po přihlášení pomocí ID zákazníka platné tyto dokumenty jsou k dispozici ke stažení. Gemalto také obsahuje sadu integračních příručkách k zákazníkům usnadní různé scénáře a integrace softwaru. Další informace najdete v tématu [Gemalto web partnera Microsoftu](https://safenet.gemalto.com/partners/microsoft/).

### <a name="support"></a>Podpora

Mělo by se řešit jakékoli dotaz ve vztahu k pomocí moduly hardwarového zabezpečení jako součást služby vyhrazené HSM nebo software úrovně problém k Gemalto podporu přímo. Všechny softwarové komponenty, které jsou uvedené výše a jakékoli vlastní konfigurace modulu hardwarového zabezpečení, která je po zřízení, bude řešit Gemalto. Další informace najdete v tématu [Gemalto zákaznického portálu služeb podpory](https://supportportal.gemalto.com/csm/).

### <a name="consulting-services"></a>Konzultační služby

Pro všechny pomoc při navrhování, vývoj a nasazení vlastních aplikací, které používají modul hardwarového zabezpečení obraťte se na váš obchodní zástupce Gemalto.

## <a name="microsoft-support"></a>Podpora Microsoftu

Microsoft se ujistěte se, že fyzické zařízení HSM se síť přístupný a provozní stav pro výhradní použití jediného zákazníka. Zákazníci odpovídají za konfiguraci, správu a správu zařízení. Odpovědnosti Microsoftu patří:

* Zařízení musí napájení a chladicí
* Údržba provozního stavu modulu hardwarového zabezpečení (například scénáře řešení potíží)
* Zařízení je dostupný v síti.

Společnosti Microsoft by se měly hlásit problémy, jako je následující:

* Chyb na úrovni součástí
* Selhání celé zařízení
* Potíže s přístupem k síti
* Problémy zřizování a zrušení zřizování.

Microsoft má fyzické sériového portu přístup k zařízení pomocí sledování role (tedy ne pro správu role), která umožňuje základní stav telemetrie.  To vám umožní Microsoftu poskytují proaktivní oznámení o problémy zákazníkovi, pokud zákazník rozhodne omezit toto oprávnění. 

### <a name="provisioning-and-decommissioning"></a>Zřizování a vyřazování z provozu

Poté, co zákazník má schválené registrace pro službu vyhrazené modulu hardwarového zabezpečení, bude moct vytvářet prostředky modulu hardwarového zabezpečení (aktuálně prostřednictvím Powershellu nebo rozhraní příkazového řádku a ne na webu Azure portal). Prostředek prochází o přidělení proces, který se mapuje fyzické zařízení v zadané oblasti, na základě předem definované virtuální síť (VNet). Jakmile viditelné ve virtuální síti, může zákazník přístupu k zařízení a nakonfigurovat dále podle požadavků. Zákazníkům přístup k jejich dedikovaných modulů hardwarového zabezpečení pomocí Gemalto klientský software a nástroje. Společnost Microsoft podporuje proces vytváření prostředků. Vlastní konfigurace pro zpracování a novější podporuje Gemalto. (viz Gemalto podporují výše). Když zákazník dokončí, pomocí modulu hardwarového zabezpečení, se musí být resetování (nebo zeroized) k zajištění žádné trvalosti dat. Postup resetování zařízení odebere všechny vlastní konfigurace a data. Microsoft zruší přidělení zařízení a vrátí ji do fondu ve pristine stavu. To znamená, že když se zařízení vrátí do fondu, že není žádná doklad o předchozí aktivitě zákazníka. 

### <a name="hardware-issues"></a>Problémy s hardwarem

Zařízení HSM má redundantní a nahraditelné napájení a ventilátor jednotek.  Ale odebrání jednotky ventilátor způsobí stále proti události. Pokud dojde k selhání součásti, Microsoft použije nejvhodnější k vyřešení problému úrovni komponenty způsobem, který způsobí, že nejnižší rizika na dostupnost služby našich zákazníků a minimálním přerušením.
Toto zařízení teď nahrazuje čerstvé jedním z volného fondu způsobí žádné závažnější selhání zařízení. Zákazník jednoduše obsahuje nové zařízení v existující pár HA pro ni k synchronizaci a vraťte se do plné provozní stav. Selhání zařízení bude mít svá data s zařízení odebrán a skartovány na webu v datovém centru. Pouze skříni se vrátí do Gemalto recyklace.


### <a name="networking-issues"></a>Problémy sítě

Pokud zákazníci síťové potíže s přístupem k modulu hardwarového zabezpečení zařízení, měli byste kontaktovat podporu Microsoftu. Jednoduchý test pro síťový přístup je pomocí SSH se připojte k zařízení HSM. Pokud to nepomůže, obraťte se na podporu Microsoftu.

## <a name="service-level-expectations-for-support"></a>Očekávání úrovni služby pro podporu

Úrovně služeb podpory společnosti Microsoft, najdete [plán podpory Azure](https://azure.microsoft.com/support/plans/).
Úrovně služeb pro podporu Gemalto, najdete [Gemalto podporují Essentials](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Další postup

Doporučuje se, že klíčových konceptů, jako je vysoká dostupnost a zabezpečení jsou dobře pochopitelné i před zřizování zařízení a návrh aplikace nebo nasazení.

* [Architektura nasazení](deployment-architecture.md)
* [Vysoká dostupnost](high-availability.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)
* [Monitorování](monitoring.md)