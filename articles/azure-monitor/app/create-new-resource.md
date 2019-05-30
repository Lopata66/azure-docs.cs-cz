---
title: Vytvořit nový prostředek služby Azure Application Insights | Dokumentace Microsoftu
description: Ručně nastavte monitorování pomocí Application Insights pro nové živé aplikace.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: mbullwin
ms.openlocfilehash: 712004a1ae8a2a72854b7b2332449a019c0820c3
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66256245"
---
# <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights
Azure Application Insights zobrazí data o vaší aplikaci v Microsoft Azure *prostředků*. Vytváří se nový prostředek je proto součástí [nastavení Application Insights pro monitorování nové aplikace][start]. V mnoha případech vytvoření prostředku můžete udělat automaticky integrovaným vývojovým prostředím. Ale v některých případech můžete vytvořit prostředek ručně – například mít samostatné prostředky pro vývoj a produkčním prostředí sestavení vaší aplikace.

Po vytvoření prostředku, získejte svůj Instrumentační klíč a použít ke konfiguraci sady SDK v aplikaci. Klíč prostředku propojí telemetrická data do prostředku.

## <a name="sign-up-to-microsoft-azure"></a>Zaregistrovat do služby Microsoft Azure
Pokud jste ještě se dostali [Microsoft account, získejte ji nyní](https://live.com). (Pokud používáte služby jako Outlook.com, OneDrive, Windows Phone nebo XBox Live, již máte účet Microsoft.)

Budete potřebovat předplatné [Microsoft Azure](https://azure.com). Pokud váš tým nebo organizace má předplatné Azure, vlastník vás může přidat do něj pomocí účtu Windows Live ID. Je jenom za využité. Výchozí základní plán umožňuje určitou dobu bezplatně experimentální použití.

Když máte přístup k předplatnému, přihlaste se k Application Insights na [ https://portal.azure.com ](https://portal.azure.com)a použijte účet Live ID se můžete přihlásit.

## <a name="create-an-application-insights-resource"></a>Vytvořte prostředek Application Insights
V [portal.azure.com](https://portal.azure.com), přidejte prostředek Application Insights:

![Klikněte na tlačítko Nový, Application Insights](./media/create-new-resource/01-new.png)

* **Typ aplikace** ovlivňuje, co se zobrazí na kartě s přehledem a k dispozici ve vlastnosti [Průzkumník metrik][metrics]. Pokud se váš typ aplikace, vyberte Obecné.
* **Předplatné** je váš účet platby v Azure.
* **Skupina prostředků** je usnadnění pro správu vlastností, jako je řízení přístupu. Pokud jste již vytvořili další prostředky Azure, můžete vložit tento nový prostředek ve stejné skupině.
* **Umístění** je, kde jsme zachovejte si svá data.
* **Připnout na řídicí panel** umístí rychlý přístup k dlaždici pro váš prostředek Azure domovskou stránku. Doporučené.

Po vytvoření aplikace se otevře nové okno. V tomto okně je, kde se zobrazí data o využití a výkonu o vaší aplikaci. 

Chcete-li vrátit zpět do ji při příštím přihlášení k Azure, vyhledejte vaší aplikace rychlý start dlaždici na úvodní panel (domovské obrazovky). Nebo klikněte na tlačítko Procházet a vyhledejte jej.

## <a name="copy-the-instrumentation-key"></a>Zkopírujte klíč instrumentace
Instrumentační klíč identifikuje prostředek, který jste vytvořili. Potřebujete ji k sadě SDK.

![Klikněte na tlačítko Essentials, klikněte na klíč instrumentace, CTRL + C](./media/create-new-resource/02-props.png)

## <a name="install-the-sdk-in-your-app"></a>Instalace sady SDK do vaší aplikace
Nainstalujte službu Application Insights SDK ve vaší aplikaci. Tento krok závisí do značné míry na typu aplikace. 

Použijte Instrumentační klíč ke konfiguraci [sady SDK, který nainstalujete v aplikaci][start].

Sada SDK obsahuje standardní moduly, které odesílají telemetrii, aniž byste museli psát jakýkoli kód. Sledovat uživatelské akce nebo diagnostikovat problémy ve více podrobností, [pomocí rozhraní API] [ api] k odesílání vlastní telemetrie.

## <a name="monitor"></a>Prohlédnout telemetrická data
Toto okno zavřete, rychlý start se vraťte do okna vaší aplikace na webu Azure Portal.

Klikněte na tlačítko hledání dlaždici zobrazíte [diagnostické vyhledávání][diagnostic], kde se zobrazí u prvních událostí. 

Pokud očekáváte více dat, klikněte na tlačítko **aktualizovat** po pár sekundách.

## <a name="creating-a-resource-automatically"></a>Vytvoření prostředku automaticky
Můžete napsat [skript prostředí PowerShell](../../azure-monitor/app/powershell.md) automaticky vytvořit prostředek.

## <a name="next-steps"></a>Další postup
* [Diagnostické vyhledávání](../../azure-monitor/app/diagnostic-search.md)
* [Zkoumání metrik](../../azure-monitor/app/metrics-explorer.md)
* [Psaní analytických dotazů](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md

