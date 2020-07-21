---
title: Analýza uživatele, relace a události v Azure Application Insights
description: Demografická analýza uživatelů vaší webové aplikace
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0d605286df70988852d1650be58cfb2c395de814
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539801"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Uživatelé, relace a analýza událostí v Application Insights

Zjistěte, kdy uživatelé používají vaši webovou aplikaci, jaké stránky mají nejvíc zajímat, kde se nacházejí vaši uživatelé a jaké prohlížeče a operační systémy používají. Analýza firemních a využití telemetrie pomocí [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

![Snímek obrazovky Application Insights uživatelů](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Začínáme

Pokud se ještě nezobrazuje data v okně uživatelé, relace nebo události na portálu Application Insights, [Přečtěte si, jak začít s nástroji pro používání](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Nástroj pro segmentaci uživatelů, relací a událostí

Tři okna použití používají stejný nástroj k řezání telemetrie z vaší webové aplikace ze tří perspektiv. Filtrováním a rozdělením dat můžete odhalit přehledy o relativním používání různých stránek a funkcí.

* **Nástroj pro uživatele**: kolik lidí používalo vaši aplikaci a její funkce.  Uživatelům se počítá pomocí anonymních ID uložených v souborech cookie prohlížeče. Jedna osoba, která používá jiné prohlížeče nebo počítače, se bude počítat jako více než jeden uživatel.
* **Session Tool**: kolik relací aktivity uživatelů zahrnovalo určité stránky a funkce vaší aplikace. Relace se počítá po půl hodiny neaktivity uživatele nebo po 24 hodinách nepřetržitém používání.
* **Nástroj Events (události**): jak často se používají určité stránky a funkce vaší aplikace. Zobrazení stránky se počítá, když prohlížeč načte stránku z vaší aplikace za předpokladu, že jste [ho instrumentoval](../../azure-monitor/app/javascript.md). 

    Vlastní událost představuje jeden výskyt něčeho, co se děje v aplikaci, často se jedná o interakci uživatele, jako je kliknutí na tlačítko nebo dokončení některé úlohy. Do své aplikace vložíte kód, abyste mohli [generovat vlastní události](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Dotazování na konkrétní uživatele

Prozkoumejte různé skupiny uživatelů tak, že upravíte možnosti dotazu v horní části nástroje Uživatelé:

* Zobrazit: vyberte kohorta uživatele, které chcete analyzovat.
* Kdo používá: vyberte vlastní události a zobrazení stránek.
* Během: vyberte časový rozsah.
* Podle: Vyberte způsob, jakým se mají data zablokovat, a to buď v časovém intervalu, nebo jinou vlastností, jako je například prohlížeč nebo město.
* Rozdělit podle: vyberte vlastnost, podle které chcete data rozdělit nebo segmentovat. 
* Přidat filtry: Omezte dotaz na konkrétní uživatele, relace nebo události na základě jejich vlastností, jako je například prohlížeč nebo město. 
 
## <a name="saving-and-sharing-reports"></a>Ukládání a sdílení sestav 
Můžete ukládat sestavy uživatelů, které jsou v části Mé sestavy, buď soukromé, nebo sdílené se všemi ostatními s přístupem k tomuto Application Insights prostředku v části sdílené sestavy.

Sdílení odkazu na sestavu uživatelé, relace nebo události; na panelu nástrojů klikněte na **sdílet** a potom odkaz zkopírujte.

Chcete-li sdílet kopii dat v sestavě uživatelé, relace nebo události; na panelu nástrojů klikněte na **sdílet** a potom kliknutím na **ikonu Wordu** vytvořte wordový dokument s daty. Případně klikněte na **ikonu slova** nad hlavním grafem.

## <a name="meet-your-users"></a>Vyhovět vašim uživatelům

V části **splnění uživatelů** se zobrazí informace o pěti ukázkových uživatelích odpovídajících aktuálním dotazům. Zvážení a zkoumání chování jednotlivců, kromě agregací, může poskytovat přehled o tom, jak uživatelé vaši aplikaci skutečně používají.

## <a name="next-steps"></a>Další kroky

- Chcete-li povolit prostředí používání, začněte odesílat [vlastní události](./api-custom-events-metrics.md#trackevent) nebo [zobrazení stránek](./api-custom-events-metrics.md#page-views).
- Pokud jste už odeslali vlastní události nebo zobrazení stránky, prozkoumejte nástroje využití a zjistěte, jak uživatelé používají vaši službu.
    - [Trychtýře](usage-funnels.md)
    - [Uchovávání](usage-retention.md)
    - [Toky uživatele](usage-flows.md)
    - [Sešity](../../azure-monitor/platform/workbooks-overview.md)
    - [Přidat kontext uživatele](usage-send-user-context.md)
