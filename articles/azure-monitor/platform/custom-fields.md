---
title: Vlastní pole ve službě Azure Monitor | Dokumentace Microsoftu
description: Funkci vlastních polí služby Azure Monitor umožňuje vytvořit vlastní prohledávatelná pole ze záznamů v pracovním prostoru Log Analytics, které aplikacím dodávají vlastnosti shromážděných záznamu.  Tento článek popisuje postup, jak vytvořit vlastní pole a poskytuje podrobný návod s událost vzorku.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: bwren
ms.openlocfilehash: eebf3709657382eb403041e6637e32e5f5d43b15
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793336"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor"></a>Vytvoření vlastních polí v pracovním prostoru Log Analytics ve službě Azure Monitor

> [!NOTE]
> Tento článek popisuje, jak analyzovat textová data v pracovním prostoru Log Analytics, jako jsou shromažďovány. Existují výhody analýzy textová data v dotazu, jakmile se shromáždí, jak je popsáno v [Parsování textových dat ve službě Azure Monitor](../log-query/parse-text.md).

**Vlastní pole** funkce služby Azure Monitor umožňuje rozšířit existující záznamy v pracovním prostoru Log Analytics tak, že přidáte vlastní prohledávatelná pole.  Vlastní pole se vyplní automaticky z dat extrahují z dalších vlastností ve stejném záznamu.

![Přehled](media/custom-fields/overview.png)

Například následující ukázka záznam obsahuje užitečné údaje schovaný v popisu události. Extrahování dat do samostatných vlastnost zpřístupňuje je pro činnosti, jako je filtrování a řazení.

![Ukázka extrahovat](media/custom-fields/sample-extract.png)

> [!NOTE]
> Ve verzi Preview platí omezení na 100 vlastních polí v pracovním prostoru.  Toto omezení se rozbalí, pokud tuto funkci ve fázi obecné dostupnosti.

## <a name="creating-a-custom-field"></a>Vytváření vlastního pole
Při vytváření vlastního pole Log Analytics musí pochopit, která data se mají použít k naplnění jeho hodnotu.  Používá technologii od Microsoft Research volá FlashExtract rychle identifikovat tato data.  Namísto toho, aby vám poskytují explicitní pokyny, Azure Monitor se blíže Seznamuje data, která mají být extrahovány z příkladů, které zadáte.

Postup pro vytvoření vlastní pole v následujících částech.  V dolní části tohoto článku je návod k extrakci vzorku.

> [!NOTE]
> Vlastní pole se vyplní podle záznamy odpovídající zadaným kritériím se přidají do pracovního prostoru Log Analytics, takže se zobrazí pouze na záznamy shromážděné po vytvoření vlastního pole.  Vlastní pole se nepřidá záznamy, které jsou již v úložišti dat při jeho vytvoření.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Krok 1 – identifikace záznamy, které budou mít vlastní pole
Prvním krokem je identifikace záznamy, které vám pomůžou vlastní pole.  Začínáte [standardním protokolu dotazu](../log-query/log-query-overview.md) a pak vyberte záznam tak, aby fungoval jako model, který Azure Monitor se dozvíte z.  Pokud určíte, že budete extrahovat data do vlastního pole **Průvodce extrakce pole** se otevře, kde můžete ověřit a zpřesnit kritéria.

1. Přejděte na **protokoly** a použít [dotaz pro načtení záznamů](../log-query/log-query-overview.md) , která bude mít vlastní pole.
2. Vyberte záznam, který bude používat Log Analytics tak, aby fungoval jako model pro extrahování dat k vyplnění pole vlastní.  Bude identifikovat data, která mají být extrahovány z tohoto záznamu a Log Analytics bude tyto informace slouží k určení logiku k naplnění vlastní pole pro všechny podobné záznamy.
3. Rozbalte vlastnosti záznamu, klikněte na tlačítko se třemi tečkami nalevo od hlavní vlastnosti záznamu a vyberte **extrahovat pole z**.
4. **Průvodce extrakce pole** se otevře, a zobrazí se v záznamu, který jste vybrali **hlavním příkladem** sloupce.  Vlastní pole bude potřeba definovat pro ty záznamy pomocí stejných hodnot ve vlastnostech, které jsou vybrány.  
5. Pokud je výběr přesně požadujete, vyberte další pole, chcete-li zúžit kritéria.  Chcete-li změnit hodnoty pro kritéria polí, musíte zrušit a vybrat jiný záznam odpovídající kritériím, která potřebujete.

### <a name="step-2---perform-initial-extract"></a>Krok 2: provést počáteční extrakce.
Jakmile identifikujete záznamy, které budou mít vlastní pole, identifikovat data, která mají být extrahovány.  Log Analytics bude tyto informace použít k identifikaci podobné vzory v podobných záznamů.  V kroku po to bude možné ověřit výsledky a poskytnout další podrobnosti ke službě Log Analytics pro použití v jejich analýzu.

1. Zvýrazněte text ve vzorku záznam, který má naplnit vlastní pole.  Pak zobrazí se dialogové okno, zadejte název a datový typ pro pole a provést počáteční extrakce.  Znaky  **\_CF** se automaticky připojí.
2. Klikněte na tlačítko **extrahovat** provádět analýzu shromážděných záznamů.  
3. **Souhrn** a **výsledky hledání** části zobrazení výsledků extract, takže si můžete prohlédnout její přesnost.  **Souhrn** zobrazí kritéria použít k identifikaci a počet záznamů pro všechny hodnoty data identifikovat.  **Výsledky hledání** poskytuje podrobný seznam záznamů, které odpovídají kritériím.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Krok 3 – ověřte správnost extract a vytvořit vlastní pole
Po provedení počátečního extract, Log Analytics se zobrazí její výsledky na základě dat, která již byla shromážděna.  Pokud výsledky vypadají přesné můžete vytvořit vlastní pole se žádná další práce.  Pokud ne, pak můžete upřesnit výsledky tak, aby Log Analytics můžete zlepšit svou logikou.

1. Pokud se všechny hodnoty v počáteční extract nejsou správné, klikněte **upravit** ikonu vedle nepřesné záznamu a vyberte **upravit toto zvýraznění** k úpravě výběru.
2. Položka je zkopírován do **Další příklady** části pod **hlavním příkladem**.  Zvýraznění Tady můžete upravit na pomoct pochopit, výběr, který by měl mít provedené Log Analytics.
3. Klikněte na tlačítko **extrahovat** do této nové informace slouží k vyhodnocení všechny existující záznamy.  Výsledky mohou být upraveny pro záznamy než ten, který jste právě upravili založené na této nové informace.
4. Dál přidejte opravy, dokud se všechny záznamy v extract správně identifikovat dat k vyplnění pole nové vlastní.
5. Klikněte na tlačítko **uložit extrakci** až budete s výsledky spokojeni.  Vlastní pole je teď definovaný, ale zatím nebude přidána k žádné záznamy.
6. Počkejte pro nové záznamy odpovídající zadaným kritériím shromažďování a poté znovu spusťte prohledávání protokolů. Nové záznamy by měl mít vlastní pole.
7. Použijte vlastní pole, stejně jako jakoukoli jinou vlastnosti záznamu.  Můžete použít k datům agregují a skupiny a dokonce využít k vytvoření nové poznatky.

## <a name="viewing-custom-fields"></a>Zobrazení vlastních polí
Můžete zobrazit seznam všech vlastních polí ve skupině pro správu z **Upřesnit nastavení** nabídce pracovního prostoru Log Analytics na portálu Azure portal.  Vyberte **Data** a potom **vlastní pole** seznam všech vlastních polí v pracovním prostoru.  

![Vlastní pole](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Odebrání vlastní pole
Existují dva způsoby, jak odstranit vlastní pole.  První je **odebrat** možnost pro každé pole při prohlížení seznamu dokončení, jak je popsáno výše.  Další metodou je načíst záznam a klikněte na tlačítko vlevo od pole.  V nabídce bude mít možnost odstranit vlastní pole.

## <a name="sample-walkthrough"></a>Ukázkový názorný postup
Následující části vás provede úplný příklad vytvoření vlastního pole.  V tomto příkladu získává název služby v události Windows, které označují mění se stav služby.  To závisí na události vytvořené ve správci řízení služeb v systému znaménko počítačích s Windows.  Pokud chcete postupovat podle tohoto příkladu, musí být [shromažďování událostí informace v systémovém protokolu](data-sources-windows-events.md).

Jsme zadejte následující dotaz, který vrátí všechny události ze Správce řízení služeb, které mají ID události z 7036, což je událost, která znamená spouštění nebo zastavování služby.

![Dotaz](media/custom-fields/query.png)

My pak vyberte a rozbalte libovolný záznam s událostí ID 7036.

![Zdrojový záznam](media/custom-fields/source-record.png)

Kliknutím na tři tečky vedle hlavní vlastnosti definujeme vlastních polí.

![Rozbalte pole](media/custom-fields/extract-fields.png)

**Průvodce extrakce pole** se otevře a **EventLog** a **EventID** pole jsou vybrána v **hlavním příkladem** sloupce.  To znamená, že vlastní pole bude definici pro události v systémovém protokolu s ID události 7036.  To je dostatečná, proto nepotřebujeme můžete vybrat jiné pole.

![Hlavní příklad](media/custom-fields/main-example.png)

Jsme zvýrazněte název služby v **RenderedDescription** vlastnost a použití **služby** k identifikaci názvu služby.  Vlastní pole bude volána **Service_CF**. Typ pole v tomto případě je řetězec, proto jsme můžete nechat, beze změny.

![Název pole](media/custom-fields/field-title.png)

Vidíme, že název služby je správně identifikovat pro některé záznamy, ale ne pro ostatní uživatele.   **Výsledky hledání** zobrazit část názvu **adaptér výkonu služby WMI** nebyla vybrána.  **Souhrn** zobrazuje tento jeden záznam identifikovat **instalační služba modulů** místo **Instalační služby systému Windows moduly**.  

![Výsledky hledání](media/custom-fields/search-results-01.png)

Začneme s **adaptér výkonu služby WMI** záznamu.  Kliknete na jeho ikonu a pak **upravit toto zvýraznění**.  

![Upravit zvýraznění](media/custom-fields/modify-highlight.png)

Doporučujeme zvýšit zvýraznění zahrnout slovo **WMI** a pak znovu spusťte extract.  

![Další příklad](media/custom-fields/additional-example-01.png)

Můžeme vidět, které položky pro **adaptér výkonu služby WMI** byly opraveny, a Log Analytics také používají tyto informace k odstranění záznamů pro **instalační program modulu Windows**.

![Výsledky hledání](media/custom-fields/search-results-02.png)

Teď můžeme spustit dotaz, který ověřuje **Service_CF** se vytvoří, ale zatím není přidán do žádné záznamy. Důvodem je skutečnost, že vlastní pole nebude fungovat proti existující záznamy, proto budeme muset počkat, než pro nové záznamy, které se mají shromažďovat.

![Počáteční počet](media/custom-fields/initial-count.png)

Po nějakou dobu tak nové události se shromažďují, vidíme, **Service_CF** pole teď přidáte na záznamy, které odpovídají našimi kritérii.

![Konečných výsledků](media/custom-fields/final-results.png)

Teď můžeme použít vlastní pole, stejně jako jakoukoli jinou vlastnosti záznamu.  Pro znázornění, se nám vytvořit dotaz, který se seskupí podle nového **Service_CF** pole, které chcete zkontrolovat, které služby jsou nejaktivnější.

![Seskupit podle dotazu](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Další postup
* Další informace o [protokolu dotazy](../log-query/log-query-overview.md) sestavování dotazů pomocí vlastních polí pro kritéria.
* Monitorování [vlastního protokolu souborů](data-sources-custom-logs.md) , která můžete analyzovat pomocí vlastní pole.

