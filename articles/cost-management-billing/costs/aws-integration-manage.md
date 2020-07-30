---
title: Správa nákladů a využití AWS ve službě Azure Cost Management
description: V tomto článku se dozvíte, jak můžete analýzu nákladů a rozpočty ve službě Cost Management používat ke správě nákladů a využití AWS.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.reviewer: matrive
ms.custom: ''
ms.openlocfilehash: d62a2aecc787d23750023def65eda7bf096b34f5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290870"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Správa nákladů a využití AWS v Azure

Potom, co si nastavíte a nakonfigurujete integraci sestavy nákladů a využití AWS pro Azure Cost Management, můžete začít se správou nákladů a využití AWS. V tomto článku se dozvíte, jak můžete analýzu nákladů a rozpočty ve službě Cost Management používat ke správě nákladů a využití AWS.

Pokud jste tuto integraci ještě nenakonfigurovali, přečtěte si článek [Nastavení a konfigurace integrace sestavy využití AWS](aws-integration-set-up-configure.md).

_Než začnete:_ Pokud nejste obeznámeni s analýzou nákladů, přečtěte si rychlý start [Prozkoumání a analýza nákladů pomocí analýzy nákladů](quick-acm-cost-analysis.md). Pokud nejste obeznámeni s rozpočty v Azure, přečtěte si kurz [Vytváření a správa rozpočtů Azure](tutorial-acm-create-budgets.md).

## <a name="view-aws-costs-in-cost-analysis"></a>Zobrazení nákladů AWS v analýze nákladů

Náklady AWS jsou v analýze nákladů dostupné v následujících oborech:

- Propojené účty AWS ve skupině pro správu
- Náklady propojených účtů AWS
- Náklady konsolidovaných účtů AWS

V dalších částech je popsáno, jak tyto obory používat k zobrazení jejich nákladů a využití.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Zobrazení propojených účtů AWS ve skupině pro správu

Zobrazení nákladů pomocí oboru skupiny pro správu je jediným způsobem, jak zobrazit agregované náklady pocházející z různých předplatných a propojených účtů. Použití skupiny pro správu umožňuje zobrazení napříč cloudy.

V analýze nákladů otevřete výběr oboru a vyberte skupinu pro správu, která obsahuje propojené účty AWS. Zde je obrázek s příkladem webu Azure Portal:

![Příklad zobrazení pro výběr oboru](./media/aws-integration-manage/select-scope01.png)



Zde je příklad znázorňující náklady skupiny pro správu v analýze nákladů seskupené podle poskytovatele (Azure a AWS).

![Příklad znázorňující náklady na Azure a AWS za čtvrtletí v analýze nákladů](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Zobrazení nákladů propojených účtů AWS

Pokud chcete zobrazit náklady propojeného účtu AWS, otevřete výběr oboru a vyberte propojený účet AWS. Propojené účty jsou přidružené ke skupině pro správu, jak je definováno v konektoru AWS.

Zde je příklad, který ukazuje výběr oboru propojeného účtu AWS.

![Příklad zobrazení pro výběr oboru](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Zobrazení nákladů konsolidovaných účtů AWS

Pokud chcete zobrazit náklady konsolidovaných účtů AWS, otevřete výběr oboru a vyberte konsolidovaný účet AWS. Zde je příklad, který ukazuje výběr oboru konsolidovaného účtu AWS.

![Příklad zobrazení pro výběr oboru](./media/aws-integration-manage/select-scope03.png)



Tento obor poskytuje agregované zobrazení všech propojených účtů AWS přidružených ke konsolidovanému účtu AWS. Zde je příklad znázorňující náklady konsolidovaného účtu AWS seskupené podle názvu služby.

![Příklad znázorňující náklady konsolidovaného účtu AWS v analýze nákladů](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimenze dostupné pro filtrování a seskupování

Následující tabulka popisuje dimenze, podle kterých lze v analýze nákladů seskupovat a filtrovat.

| Dimenze | Záhlaví sestavy nákladů a využití Amazon | Obory | Komentáře |
| --- | --- | --- | --- |
| Zóna dostupnosti | lineitem/AvailabilityZone | Vše |   |
| Umístění | product/Region | Vše |   |
| Měřič |   | Vše |   |
| Kategorie měřiče | lineItem/ProductCode | Vše |   |
| Podkategorie měřiče | lineitem/UsageType | Vše |   |
| Operace | lineItem/Operation | Vše |   |
| Prostředek | lineItem/ResourceId | Vše |   |
| Typ prostředku | product/instanceType | Vše | Pokud má product/instanceType hodnotu null, použije se lineItem/UsageType. |
| ResourceGuid | – | Vše | Identifikátor GUID měřiče Azure |
| Název služby | product/ProductName | Vše | Pokud má product/ProductName hodnotu null, použije se lineItem/ProductCode. |
| Úroveň služeb |   |   |   |
| ID předplatného | lineItem/UsageAccountId | Konsolidovaný účet a skupina pro správu |   |
| Název předplatného | – | Konsolidovaný účet a skupina pro správu | Názvy účtů se shromažďují pomocí rozhraní AWS Organization API. |
| Značka | resourceTags/\* | Vše | Kvůli povolení značek různých cloudů je předpona _user:_ odebrána z uživatelem definovaných značek. Předpona _aws:_ zůstane nedotčena. |
| ID fakturačního účtu | bill/PayerAccountId | Skupina pro správu |   |
| Název fakturačního účtu | – | Skupina pro správu | Názvy účtů se shromažďují pomocí rozhraní AWS Organization API. |
| Poskytovatel | – | Skupina pro správu | Buď AWS, nebo Azure |

## <a name="set-budgets-on-aws-scopes"></a>Nastavení rozpočtů v oborech AWS

Rozpočty slouží k proaktivní správě nákladů a posílení zodpovědnosti ve vaší organizaci. Rozpočty se nastavují v oborech konsolidovaného účtu AWS a propojeného účtu AWS. Zde je příklad rozpočtů pro konsolidovaný účet AWS, který se zobrazuje ve službě Cost Management:

![Příklad znázorňující rozpočty pro konsolidovaný účet AWS](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Proces shromažďování dat AWS

Po nastavení konektoru AWS se spustí procesy shromažďování a zjišťování dat. Shromáždění všech dat o využití může trvat několik hodin. Doba trvání závisí na:

- Době potřebné ke zpracování souborů sestav nákladů a využití, které jsou v kontejneru AWS S3
- Době potřebné k vytvoření oborů konsolidovaného účtu AWS a propojeného účtu AWS
- Době a četnosti, s jakou AWS zapisuje soubory sestav nákladů a využití v kontejneru S3

## <a name="aws-integration-pricing"></a>Ceny integrace AWS

Každý konektor AWS nabízí 90denní bezplatnou zkušební verzi. Během verze Public Preview se neúčtují žádné poplatky.

Ceníková cena je 1 % měsíčních nákladů na AWS. Každý měsíc bude účtován na základě fakturovaných nákladů z předchozího měsíce.

Při přístupu k rozhraním API služby AWS mohou nabíhat další náklady.

## <a name="aws-integration-limitations"></a>Omezení integrace AWS

- Cost Management nepodporuje sestavy nákladů, které obsahují více typů měn. Pokud vyberete obor, který má více měn, zobrazí se chybová zpráva.
- Cloudové konektory nepodporují AWS GovCloud (US), AWS Gov ani AWS China.
- Ve službě Cost Management se zobrazují jen _náklady na využití_ AWS. Daň, podpora, refundace, rezervované instance, kredity nebo jiné typy poplatků zatím nejsou podporovány.

## <a name="troubleshooting-aws-integration"></a>Řešení problémů s integrací AWS

Následující informace můžete použít k řešení běžných problémů.

### <a name="no-permission-to-aws-linked-accounts"></a>Chybějící oprávnění k propojeným účtům AWS

**Kód chyby:** _Unauthorized_

Oprávnění pro přístup k nákladům propojených účtů AWS můžete získat dvěma způsoby:

- Získejte přístup ke skupině pro správu, která obsahuje propojené účty AWS.
- Požádejte někoho, aby vám udělil oprávnění k propojenému účtu AWS.

Autor konektoru AWS je standardně vlastníkem všech objektů, které tento konektor vytvořil. Sem patří také konsolidovaný účet AWS a propojený účet AWS.

Aby bylo možné ověřit nastavení konektoru, budete potřebovat alespoň roli přispěvatel; čtenář nemůže nastavení konektoru ověřit.

### <a name="collection-failed-with-assumerole"></a>Shromažďování selhalo s chybou AssumeRole

**Kód chyby:** _FailedToAssumeRole_

Tato chyba znamená, že služba Cost Management nemůže volat rozhraní AWS AssumeRole API. K tomu může dojít kvůli nějakému problému s definicí role. Ověřte, jestli platí následující podmínky:

- Externí ID je stejné jako ID v definici role a definici konektoru.
- Typ role je nastavený na **Another AWS account Belonging to you or 3rd party** (Jiný účet AWS, který patří vám nebo třetí straně).
- Políčko **Require MFA** (Vyžadovat vícefaktorové ověřování) není zaškrtnuté.
- Důvěryhodný účet AWS v této roli AWS je _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Shromažďování selhalo s chybou odepření přístupu – definice sestavy nákladů a využití

**Kód chyby:** _AccessDeniedReportDefinitions_

Tato chyba znamená, že služba Cost Management nevidí definice sestavy nákladů a využití. Toto oprávnění slouží k ověření, že sestava nákladů a využití je definována tak, jako to služba Azure Cost Management očekává. Viz [Vytvoření sestavy nákladů a využití v AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Shromažďování selhalo s chybou odepření přístupu – výpis sestav

**Kód chyby:** _AccessDeniedListReports_

Tato chyba znamená, že služba Cost Management nemůže vypsat objekt v kontejneru S3, kde se nachází sestava nákladů a využití. Zásada AWS IAM vyžaduje oprávnění k tomuto kontejneru a objektům v tomto kontejneru. Viz [Vytvoření role a zásady v AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Shromažďování selhalo s chybou odepření přístupu – stažení sestavy

**Kód chyby:** _AccessDeniedDownloadReport_

Tato chyba znamená, že služba Cost Management nemůže zpřístupnit a stáhnout soubory sestav nákladů a využití uložené v kontejneru Amazon S3. Ověřte, že se zásada AWS JSON připojená k dané roli podobá příkladu, který se zobrazuje dole v části [Vytvoření role a zásady v AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Shromažďování selhalo, protože se nepodařilo najít sestavu nákladů a využití

**Kód chyby:** _FailedToFindReport_

Tato chyba znamená, že služba Cost Management nemůže najít sestavu nákladů a využití, která byla definována v konektoru. Ověřte, že není odstraněna a že se zásada AWS JSON připojená k dané roli podobá příkladu, který se zobrazuje dole v části [Vytvoření role a zásady v AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Konektor nelze vytvořit nebo ověřit kvůli neshodě definic sestavy nákladů na využití

**Kód chyby:** _ReportIsNotValid_

Tato chyba souvisí s definicí sestavy nákladů a využití AWS. Pro tuto sestavu se vyžaduje specifické nastavení, viz požadavky v článku [Vytvoření sestavy nákladů a využití v AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

## <a name="next-steps"></a>Další kroky

- Pokud jste v prostředí Azure ještě nenakonfigurovali skupiny pro správu, přečtěte si článek [Počáteční nastavení skupin pro správu](../../governance/management-groups/overview.md#initial-setup-of-management-groups).
