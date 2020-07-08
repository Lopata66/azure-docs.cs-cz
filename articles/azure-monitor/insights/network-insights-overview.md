---
title: Azure Monitor pro sítě (Preview)
description: Rychlý přehled Azure Monitor pro síť, který poskytuje komplexní přehled o stavu a metrikách pro všechny nasazené síťové prostředky bez jakékoli konfigurace.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77654864"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor pro sítě (Preview)
Azure Monitor pro síť poskytuje komplexní přehled o stavu a metrikách pro všechny nasazené síťové prostředky bez jakékoli konfigurace. Rozšířené možnosti vyhledávání pomáhají identifikovat závislosti prostředků, jako je například identifikace prostředků, které hostují web, pouhým hledáním názvu hostovaného webu.

Stránka **přehled** Azure monitor pro sítě nabízí snadnou představu o inventáři síťových prostředků spolu se stavem prostředků a výstrahami. Je rozdělen na čtyři klíčové oblasti:

- Hledání a filtrování
- Resource Health a metriky
- Výstrahy 
- Zobrazení závislostí

![Stránka Přehled](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Hledání a filtrování
Zobrazení stavu prostředku a upozornění je možné přizpůsobit pomocí filtrů, jako je **předplatné**, **Skupina prostředků** a **typ prostředku**. Vyhledávací pole poskytuje schopnost vyhledávat prostřednictvím vlastností prostředku.

Vyhledávací pole lze použít k vyhledání prostředků a přidružených prostředků. Například veřejná IP adresa je přidružená k Application Gateway. Při hledání názvu DNS veřejných IP adres se určí jak veřejná, tak přidružená Application Gateway.

![Search](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Resource Health a metrika
Každá dlaždice představuje typ prostředku s počtem instancí nasazených ve všech předplatných vybraných společně se stavem prostředku. V následujícím příkladu jsou nasazena 105 ER a VPN připojení, 103 jsou v pořádku a 2 nedostupné.

![Stav prostředků](media/network-insights-overview/resource-health.png)

Kliknutím na dvě nedostupná připojení ER a VPN se spustí zobrazení metriky. 

![Zobrazení metriky](media/network-insights-overview/metric-view.png)

V zobrazení mřížky můžete kliknout na jednotlivé prvky. Kliknutím na ikonu stav můžete přesměrovat na stav prostředků pro toto připojení. Klikněte na výstrahy a přesměrujte na stránku výstrahy a metriky pro toto připojení. 

## <a name="alerts"></a>Výstrahy
Mřížka **výstrahy** na pravé straně poskytuje zobrazení všech výstrah vygenerovaných pro vybrané prostředky ve všech předplatných. Klikněte na tlačítko počty výstrah a přejděte na stránku podrobné výstrahy.

## <a name="dependency-view"></a>Zobrazení závislostí
Zobrazení **závislostí** pomáhá vizualizovat způsob konfigurace prostředku. Zobrazení v současné době se podporuje jenom pro Application Gateway. Zobrazení závislostí lze zobrazit kliknutím na název prostředku Application Gateway v zobrazení mřížky metrik.

![Zobrazení Application Gateway](media/network-insights-overview/application-gateway.png)

Zobrazení **závislostí** pro Application Gateway poskytuje zjednodušený přehled o tom, jak jsou IP adresy front-endu připojené ke posluchačům, pravidlům a back-endovému fondu. Připojené okraje jsou barevně kódované a poskytují další podrobnosti na základě stavu fondu back-endu. Zobrazení také nabízí podrobné zobrazení Application Gateway metrik a metrik pro všechny související back-end fondy, jako jsou VMSS a instance virtuálních počítačů.

![Zobrazení závislostí](media/network-insights-overview/dependency-view.png)

Graf závislosti umožňuje snadnou navigaci na nastavení konfigurace. Kliknutím pravým tlačítkem na back-end fond získáte přístup k dalším funkcím. Pokud je například back-end fond virtuálním počítačem, můžete k řešení problémů s připojením získat přímý přístup k VIRTUÁLNÍm počítačům s Network Watcher a k potížím s připojením.

![Nabídka zobrazení závislostí](media/network-insights-overview/dependency-view-menu.png)

Panel hledání a filtrování v zobrazení závislostí nabízí snadnou možnost vyhledávání v grafu. Například hledání *AppGWTestRule* v následujícím příkladu omezí grafické zobrazení na všechny uzly připojené prostřednictvím *AppGWTestRule*. 

![Příklad hledání](media/network-insights-overview/search-example.png)

Různé filtry poskytují lepší informace o konkrétní cestě a stavu. Například vyberte v rozevíracím seznamu **stav** pouze není v *pořádku* , aby se zobrazily všechny hrany, ve kterých stav není v *pořádku*.

Kliknutím na **podrobné zobrazení metriky** spustíte předem nakonfigurovaný sešit s podrobnými metrikami pro aplikační bránu, všechny prostředky back-end fondu a IP adresy front-endu. 

## <a name="next-steps"></a>Další kroky 

- Přečtěte si další informace o monitorování sítě v tom, co je [Azure Network Watcher?](/azure/network-watcher/network-watcher-monitoring-overview).
