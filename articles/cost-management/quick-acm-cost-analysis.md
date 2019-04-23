---
title: Rychlý start – Prozkoumání nákladů na Azure pomocí analýzy nákladů | Microsoft Docs
description: Tento rychlý start vám pomůže prozkoumat a analyzovat náklady organizace na Azure pomocí analýzy nákladů.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 55407ec1846a0fe2eb037756dc2e97d8b05e7330
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60312257"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Rychlý start: Zkoumání a analýzy náklady pomocí Cost analysis

Než budete moci správně řídit a optimalizovat náklady na Azure, musíte porozumět tomu, kde se náklady ve vaší organizaci generují. Je také užitečné znát, kolik vás stojí služby a podpora prostředí a systémů. Náhled do celého spektra nákladů je důležitý, abyste přesně porozuměli výdajovým schématům organizace. Výdajová schémata mohou sloužit k prosazování mechanismů řízení nákladů, jako jsou rozpočty.

V tomto rychlém startu použijete analýzu nákladů a prozkoumáte a analyzujete náklady organizace. Můžete se podívat na agregované náklady na úrovni organizace, abyste porozuměli tomu, kde se náklady v průběhu času generují, a mohli identifikovat trendy útrat. Můžete si také zobrazit kumulované náklady v průběhu času, abyste mohli odhadnout měsíční, čtvrtletní a dokonce i roční trendy nákladů oproti rozpočtu. Rozpočet vám pomůže dodržet finanční omezení. Slouží také k zobrazení denních nebo měsíčních nákladů, abyste mohli izolovat nepravidelnosti v útratách. Dále si můžete stáhnout data aktuální sestavy, abyste je mohli dále analyzovat nebo použít v externím systému.

V tomto rychlém startu se naučíte:

- Kontrola nákladů v analýze nákladů
- Přizpůsobit zobrazení nákladů
- Stažení dat analýzy nákladů


## <a name="prerequisites"></a>Požadavky

Analýza nákladů, podporuje různé typy účtů Azure. Chcete-li zobrazit úplný seznam typů podporovaných účtů, najdete v článku [datům Cost managementu pochopit](understand-cost-mgt-data.md). K zobrazení dat na náklady, potřebujete alespoň přístup čtení ke svému účtu Azure.

Pro [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) zákazníků, můžete musí mít alespoň čtení přístup k jednomu nebo více z následujících oborů zobrazení datům služby cost.

- Fakturační účet
- Oddělení
- Registrační účet
- Skupina pro správu
- Předplatné
- Skupina prostředků

Další informace o přiřazování přístupu k datům služby Cost Management najdete v tématu [přiřazení přístupu k datům](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Kontrola nákladů v analýze nákladů

Ke kontrole nákladů v analýzy nákladů, otevřete v Azure portal a vyberte požadovaný obor **analýza nákladů** v nabídce. Například, přejděte na **předplatná**, vyberte předplatné, ze seznamu a pak vyberte **analýza nákladů** v nabídce. Použití **oboru** obranné přepnout do jiného oboru v analýzy nákladů. Další informace o oborech najdete v tématu [pochopení a práci s obory](understand-work-scopes.md).

Vybraný obor se použije v celé službě Cost Management k poskytování konsolidace dat a k řízení přístupu k informacím o nákladech. Při použití oborů nevybíráte více oborů. Místo toho vybrat větší rozsah, který ostatní až vrátit a pak je filtr seznamu na co chcete. To je důležité pochopit, protože někteří uživatelé neměli mít přístup, který podřízené obory vrátit až nadřazený obor.

Klikněte na **Otevřít analýzu nákladů**.

Počáteční zobrazení analýzy nákladů zahrnuje tyto oblasti:

**Celkem** – zobrazuje celkové náklady za aktuální měsíc.

**Rozpočet** – zobrazuje plánovaný limit útraty pro vybraný obor (pokud je k dispozici).

**Kumulované náklady** – zobrazuje celkovou denní útratu od začátku měsíce. Po [vytvoření rozpočtu](tutorial-acm-create-budgets.md) pro fakturační účet nebo předplatné můžete rychle zobrazit trend útraty oproti rozpočtu. Po najetí myší na datum se zobrazí kumulované náklady pro daný den.

**Kontingenční (prstencové) grafy** – poskytují dynamické pivoty a rozdělení nákladů podle společné sady standardních vlastností. Zobrazují nejaktuálnější náklady pro aktuální měsíc. Kontingenční grafy můžete kdykoli změnit výběrem jiného pivotu. Náklady jsou standardně zařazeny do těchto kategorií: služba (kategorie měřiče), umístění (oblast) a podřízený obor. Například registrační účty spadají pod fakturační účty, skupiny prostředků pod předplatná a prostředky pod skupiny prostředků.

![Počáteční zobrazení analýzu nákladů na webu Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Přizpůsobení zobrazení nákladů

Výchozí zobrazení poskytuje rychlé odpovědi na běžné otázky, jako jsou:

- Kolik jsem utratil(a)?
- Dodržím rozpočet?

Existuje ale mnoho případů, kdy budete potřebovat podrobnější analýzu. Přizpůsobení začíná nahoře na stránce, kde můžete vybrat datum.

Analýza nákladů zobrazuje standardně data pro aktuální měsíc. Pomocí výběru data můžete rychle přepnout na minulý měsíc, tento měsíc, toto kalendářní čtvrtletí, tento kalendářní rok, ale můžete i vybrat vlastní rozsah kalendářní dat. Výběr minulého měsíce představuje nejrychlejší způsob analýzy nejnovější faktury Azure a snadného odsouhlasení poplatků. Možnosti aktuálního čtvrtletí a roku usnadňují sledování nákladů oproti dlouhodobým rozpočtům. Můžete také vybrat jiný rozsah kalendářních dat. Můžete například vybrat jeden den, posledních sedm dnů nebo jakékoli období až rok do minulosti od aktuálního měsíce.

![Výběr data zobrazen výběr příklad pro tento měsíc](./media/quick-acm-cost-analysis/date-selector.png)

Ve výchozím nastavení analýza nákladů zobrazuje **kumulované** náklady. Kumulované náklady zahrnují všechny náklady za každý den plus předchozí dny pro neustále rostoucí zobrazení denně generovaných nákladů. Toto zobrazení je optimalizované tak, aby zobrazilo váš trend oproti rozpočtu za vybraný časový rozsah.

K dipozici máte také **denní** zobrazení, abyste se mohli podívat na náklady v jednotlivých dnech. Denní zobrazení nezobrazuje trend růstu. Toto zobrazení je navrženo tak, aby zobrazovalo nepravidelnosti, protože ze dne na den dochází k prudkému nárůstu nebo poklesu nákladů. Pokud jste vybrali rozpočet, denní zobrazení také zobrazuje odhad toho, jak může vypadat váš denní rozpočet. Pokud vaše denní náklady neustále překračují odhadovaný denní rozpočet, můžete očekávat, že překročíte měsíční rozpočet. Odhadovaný denní rozpočet jednoduše představuje prostředek, který vám pomůže vizualizovat rozpočet na nižší úrovni. Pokud denní náklady kolísají, je srovnání odhadovaného denního rozpočtu s měsíčním rozpočtem méně přesné.

Obecně můžete očekávat zobrazíte data nebo oznámení o spotřebovaných prostředků do osmi hodin.

![Denní zobrazení příklad denních nákladů pro aktuální měsíc](./media/quick-acm-cost-analysis/daily-view.png)

Výběrem možnosti **Seskupit podle** můžete vybrat kategorii skupiny a změnit data zobrazená v horním celkovém plošném grafu. Seskupení umožňuje rychle zjistíte, jak vaší útraty jsou rozdělené do kategorií podle společné vlastnosti prostředku a využití, jako jsou skupiny prostředků nebo značky prostředku. Seskupit podle značky, vyberte klíč značky, které chcete seskupit. Zobrazí se vám náklady porušena každá hodnota značky, další segment pro prostředky, které nemají dané klíčové slovo použít.

Většina [prostředky Azure podporují označování](../azure-resource-manager/tag-support.md), ale některé značky nejsou k dispozici ve službě Cost Management a fakturace. Kromě toho skupiny značky prostředku se nepodporují. Služba Cost Management podporuje pouze značky prostředků ode dne, kdy jsou značky použít přímo na prostředek.

Tady je zobrazení nákladů na služby Azure pro zobrazení minulého měsíce.

![Seskupené denní souhrnné zobrazení příklad náklady na služby Azure za poslední měsíc](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Kontingenční grafy v rámci hlavní grafu zobrazit různé seskupení abyste získali širší představu o celkové náklady pro vybrané časové období a filtry. Vyberte vlastnost nebo značek zobrazíte souhrnné náklady podle dimenzí.


![Úplná data pro aktuální zobrazení zobrazuje názvy skupin prostředků](./media/quick-acm-cost-analysis/full-data-set.png)

Na předchozím obrázku jsou znázorněny názvy skupin prostředků. Zatímco můžete seskupit podle značky, chcete-li zobrazit celkové náklady pro jednotlivé značky, zobrazení všech značek na prostředek nebo skupina prostředků není k dispozici v zobrazení analýzy nákladů.

Při seskupování nákladů podle konkrétního atributu je zobrazeno deset nejvýznamnějších přispěvatelů k nákladům, od nejvyššího k nejnižšímu. Pokud existuje více než deset skupin, jsou uvedeny nejčastější devět nákladům přispívá. Také je jako **ostatní** skupiny, které zahrnuje všechny zbývající skupiny společně. Při seskupování podle klíčových slov, může se zobrazit také **Untagged** pro náklady, které nemají značku klíč použít. **Neoznačených** je vždy poslední, i když nejsou více neoznačených nákladů než příznakem náklady. Pokud existují deset nebo více hodnot značek, neoznačených nákladů bude součástí **ostatní**.

*Klasické* virtuálních počítačů (Azure Service Management nebo ASM), sítě a prostředky úložiště Nesdílejte podrobné fakturačních údajů. Při sloučení jako **klasické služby** při seskupování náklady.

Zobrazí se kompletní data pro všechna zobrazení. Jakékoli možnosti nebo filtry, které použijete vliv data zobrazí. Úplná sada dat zobrazíte kliknutím **typ grafu** seznamu a potom klikněte na tlačítko **tabulky** zobrazení.

![Data pro aktuální zobrazení v tabulkovém zobrazení](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="download-cost-analysis-data"></a>Stažení dat analýzy nákladů

Informace z analýzy dat si můžete **stáhnout** a vygenerovat tak soubor CSV pro veškerá data aktuálně zobrazená na webu Azure Portal. Všechny použité filtry a seskupení jsou v souboru zahrnuty. Do souboru CSV se zahrnou i podkladová data pro horní celkový graf, která nejsou aktivně zobrazena.

## <a name="next-steps"></a>Další postup

Pokračujte prvním kurzem, ve kterém se dozvíte, jak vytvořit a spravovat rozpočty.

> [!div class="nextstepaction"]
> [Vytváření a správa rozpočtů](tutorial-acm-create-budgets.md)
