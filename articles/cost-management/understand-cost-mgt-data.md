---
title: Pochopení dat Azure Cost Management | Dokumentace Microsoftu
description: Tento článek vám pomůže líp pochopit jaká data je zahrnutá ve službě Azure Cost Management a jak často byl zpracován, shromážděná, zobrazené a uzavřené.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 0defb1a8572cc3adad48dbcdb27a04e366fc3e60
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046782"
---
# <a name="understand-cost-management-data"></a>Vysvětlení dat služby Cost Management

Tento článek pomůže lépe pochopit, jaká data budou zahrnuta ve službě Azure Cost Management. A vysvětluje, jak často zpracování dat jsou shromažďovány, zobrazené a uzavřené. Bude se vám účtovat využití Azure každý měsíc. Typ vašeho předplatného Azure určuje po fakturačního měsíce ukončení. Jak často Cost Management přijímá využití dat se liší na základě různých faktorů. Tyto faktory zahrnují jak dlouho trvá zpracování dat a jak často služeb Azure generování informací o využití a fakturace systému.

## <a name="supported-microsoft-offers"></a>Podporované nabídky Microsoftu

Tyto informace zobrazí aktuálně podporované [Microsoft Azure nabízí](https://azure.microsoft.com/support/legal/offer-details/) ve službě Azure Cost Management.  Nabídky Azure je typ, který máte předplatné Azure.

| Kategorie  | **Název nabídky** | **ID kvóty** | **Číslo nabídky** |
| --- | --- | --- | --- |
| **Azure Germany** | [Azure Germany – Průběžné platby](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P |
| **Smlouva Enterprise (EA)** | Enterprise pro vývoj/testování                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P |
| **Smlouva Enterprise (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P |
| **Smlouva se zákazníkem Microsoftu** | [Plán Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017G |
| **Smlouva se zákazníkem Microsoftu** | [Plán Microsoft Azure pro Vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148g)  | MSDNDevTest_2014-09-01 | MS-AZR-0148G |
| **Microsoft Developer Network (MSDN)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p) | MSDN_2014-09-01 | MS-AZR-0062P |
| **Průběžné platby** | [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p)                       | PayAsYouGo_2014-09-01 | MS-AZR-0003P |
| **Průběžné platby** | [Průběžné platby dle aktuálního využití pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0023p)              | MSDNDevTest_2014-09-01 | MS-AZR-0023P |
| **Průběžné platby** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)           | MPN_2014-09-01 | MS-AZR-0025P |
| **Průběžné platby** | [Bezplatná zkušební verze](https://azure.microsoft.com/offers/ms-azr-0044p)                          | FreeTrial_2014-09-01 | MS-AZR-0044P |
| **Průběžné platby** | [Azure v rámci licenčního programu Open](https://azure.microsoft.com/offers/ms-azr-0111p)                       | AzureInOpen_2014-09-01 | MS-AZR-0111P |
| **Průběžné platby** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)                  | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Průběžné platby** | Azure Pass                                                                             | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)      | MPN_2014-09-01 | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)          | MSDN_2014-09-01 | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)     | MSDNDevTest_2014-09-01 | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)            | MSDN_2014-09-01 | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)  | MSDN_2014-09-01 | MS-AZR-0064P |

Následující tabulka uvádí nepodporované nabídky.

| Kategorie  | **Název nabídky** | **ID kvóty** | **Číslo nabídky** |
| --- | --- | --- | --- |
| **Cloud Solution Provider (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Cloud Solution Provider (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Cloud Solution Provider (CSP)** | Azure Germany v CSP pro Microsoft Cloud Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Průběžné platby**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Průběžné platby**                 | [Sponzorství Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Plány podpory** | Standard Support                    | Default_2014-09-01 | MS-AZR-0041P |
| **Plány podpory** | Plán podpory Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Plány podpory** | Plán Developer support                   | Default_2014-09-01 | MS-AZR-0043P |
| **Plány podpory** | Plán podpory Německo                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Plány podpory** | Azure Government Standard Support   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Plány podpory** | Azure Government pro-Direct Support | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Plány podpory** | Azure Government Developer Support.  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

Pro zákazníky s kategorií nabídky s průběžnými platbami, MSDN a Visual Studio data jsou k dispozici ve službě Cost Management od 10/02/2018. Pro přístup k datům pro vaše předplatné před 10/02/2018, můžete použít [centra účtů Azure](https://account.azure.com/subscriptions) stáhnout využití podrobnosti v souboru CSV, nebo můžete použít [podrobnosti o použití rozhraní API](/rest/api/consumption/usagedetails).

## <a name="determine-your-offer-type"></a>Určení svůj typ nabídky
Pokud se vám nezobrazují žádná data k předplatnému a k určení, pokud vaše předplatné spadá pod podporované nabídky, můžete ověřit, že je vaše předplatné nepodporuje. Chcete-li ověřit, že předplatné Azure se podporuje, přihlaste se do [webu Azure portal](https://portal.azure.com). Potom vyberte **všechny služby** na panelu nabídky vlevo. V seznamu služeb vyberte **předplatná**. V nabídce předplatné seznam klikněte na předplatné, pro kterou chcete ověřit. Vaše předplatné se zobrazí na kartě Přehled a zobrazí se **nabízejí** a **ID nabídky**. Příklad ukazuje následující obrázek.

![Příklad na kartě Přehled předplatného, nabídky a ID nabídky](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Náklady na zahrnuté ve službě Cost Management

Následující tabulky popisují data, která je součástí nebo není ve službě Cost Management. Všechny náklady se odhadují, dokud se vygeneruje faktury. Náklady na uvedené nezahrnují zdarma a předplacené kredity.

**Data o využití a nákladů**

| **Zahrnuje** | **Nezahrnuje** |
| --- | --- |
| Využití služby Azure<sup>1</sup> | Nákup rezervace – Další informace najdete v tématu [rozhraní API pro automatizaci Azure rezervace](../billing/billing-reservation-apis.md). |
| Použití nabídek na webu Marketplace | Nákupy na Marketplace – Další informace najdete v tématu [poplatků za služby třetích stran](../billing/billing-understand-your-azure-marketplace-charges.md). |
|   | Podpora poplatky – Další informace naleznete v tématu [fakturovat podmínky je vysvětleno](../billing/billing-understand-your-invoice.md). |
|   | Daně – Další informace najdete v tématu [fakturovat podmínky je vysvětleno](../billing/billing-understand-your-invoice.md). |
|   | Kredity – Další informace najdete v tématu [fakturovat podmínky je vysvětleno](../billing/billing-understand-your-invoice.md). |

<sup>1</sup> využití služeb azure je založen na rezervaci a Vyjednaný ceny.

**Metadata**

| **Zahrnuje** | **Nezahrnuje** |
| --- | --- |
| Značky prostředků<sup>2</sup> | značky skupiny prostředků |

<sup>2</sup> značky prostředku se použijí podle využití je vygenerován z každé služby a nejsou k dispozici zpětně historických údajů o využití.

## <a name="rated-usage-data-refresh-schedule"></a>Plán aktualizace dat používání hodnocení

Data o využití a nákladů je k dispozici v Správa nákladů a fakturace na webu Azure Portal a [podpora rozhraní API](index.yml). Při kontrole nákladů, mít na paměti následující body:

- Odhadované náklady pro aktuální fakturační období se aktualizují šestkrát za den.
- Odhadované náklady pro aktuální fakturační období může změnit, protože se vám účtovat využití.
- Každá aktualizace je kumulativní a obsahuje všechny položky řádku a informace z předchozí aktualizace.
- Azure dokončí nebo _zavře_ aktuálního fakturačního období až 72 hodin (tři kalendářních dnů) po fakturačního období skončí.

Následující příklady znázorňují, jak fakturačních obdobích, která by mohla ukončit.

Předplatná se smlouvou Enterprise (EA) – Pokud daný fakturační měsíc končí 31. března odhadované poplatky jsou aktualizovány až 72 hodin. V tomto příkladu ve 4. dubna půlnoc (UTC).

Předplatná s průběžnými platbami – Pokud daný fakturační měsíc končí 15. května, pak odhadovaných poplatků můžou aktualizaci až 72 hodin. V tomto příkladu podle dne 19 půlnoc (UTC).

### <a name="rerated-data"></a>Rerated dat

Ať už používáte [rozhraní API pro správu nákladů](https://aka.ms/costmgmt/docs), Power BI nebo na webu Azure portal načte data, očekávat aktuálního fakturačního období poplatky ohodnoceny znovu a proto změnit, dokud není zavřena faktury.

## <a name="usage-data-update-frequency-varies"></a>Četnost aktualizace dat používání se liší.

Dostupnost dat vzniklých využití ve službě Cost Management, závisí na několika faktory, včetně:

- Služby Azure (jako jsou úložiště, výpočetní prostředky, CDN a SQL) jak často Generovat využití.
- Čas potřebný k prostřednictvím modulu hodnocení zpracování dat o využití a nákladů kanály správy.

Některé služby generování využití častěji než jiné. Ano může se zobrazit data ve službě Cost Management pro některé služby dřív než jiné služby, které generují data méně často. Využití služby obvykle trvá 8-24 hodin se zobrazí ve službě Cost Management. Mějte data pro open měsíc se aktualizuje a jak se vám účtovat žádné další využití, protože jsou kumulativní aktualizace.

## <a name="see-also"></a>Další informace najdete v tématech

- Pokud jste ještě nedokončili první tohoto rychlého startu Cost Management, přečtěte si ho na [začít analýza nákladů](quick-acm-cost-analysis.md).
