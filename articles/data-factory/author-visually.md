---
title: Vizuální vytváření obsahu v Azure Data Factory | Dokumentace Microsoftu
description: Další informace o použití vizuálního vytváření ve službě Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: b32e4abebdfb93312c60a25ca8b483f071e5e73c
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507821"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Vizuální vytváření obsahu v Azure Data Factory
Azure Data Factory uživatelské rozhraní rozhraní (UX) umožňuje vizuálně vytvoříte a nasadíte prostředky pro službu data factory bez nutnosti psát jakýkoli kód. Můžete přetáhnout aktivity na plátno kanálu, provádějte testovací běhy, využívejte iterativní ladění a nasadit a monitorovat spuštění kanálů. Existují dvě metody pro provádění vizuálního vytváření pomocí uživatelského rozhraní:

- Autor přímo ve službě Data Factory.
- Autor s integrací Azure úložišť Git spolupráci, správu zdrojového kódu a správy verzí.

## <a name="author-directly-with-the-data-factory-service"></a>Autor přímo ve službě Data Factory
Vizuální vytváření obsahu pomocí služby Data Factory se liší od vizuálním vytváření s integrace Gitu dvěma způsoby:

- Služba Data Factory neobsahuje úložiště pro ukládání entity JSON pro vaše změny.
- Služba Data Factory není optimalizovaná pro spolupráci a správu verzí.

![Konfigurace služby Data Factory](media/author-visually/configure-data-factory.png)

Při použití uživatelského rozhraní **plátno pro vytváření obsahu** vytvořit přímo ve službě Data Factory, pouze **Publikovat vše** režim je k dispozici. Všechny změny, které provedete, se publikují přímo do služby Data Factory.

![Režim publikování](media/author-visually/data-factory-publish.png)

## <a name="author-with-azure-repos-git-integration"></a>Autor s integrací Azure úložišť Git
Vizuálním vytváření s integrace Gitu úložiště Azure podporuje správu zdrojového kódu a spolupráci pro práci na vaše kanály data factory. Datové továrny můžete přidružit úložiště Git v úložišti Azure organizace správu zdrojového kódu, spolupráci, správu verzí a tak dále. Jedna organizace úložiště Git v Azure může mít více úložišť, ale může být přidružený pouze jeden objekt pro vytváření dat úložiště Git v úložišti Azure. Pokud nemáte Azure úložiště organizace nebo úložiště, postupujte podle [tyto pokyny](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) k vytvoření vašich prostředků.

> [!NOTE]
> Skript a datových souborů můžete uložit v úložišti Git v úložišti Azure. Ale budete muset ručně nahrání souborů do služby Azure Storage. Kanál služby Data Factory není automaticky odeslat soubory skriptu nebo data uložená v úložišti Git v úložišti Azure do služby Azure Storage.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Konfigurace úložiště Git v úložišti Azure pomocí Azure Data Factory
Úložiště Git v úložišti Azure pomocí služby data factory můžete nakonfigurovat pomocí dvou metod.

#### <a name="method1"></a> Konfigurace metody 1 (úložiště Git v úložišti Azure): Stránka Začínáme

Ve službě Azure Data Factory, přejděte **pusťme se do práce** stránky. Vyberte **konfigurace úložiště kódu**:

![Konfigurace úložiště kódu úložišti Azure](media/author-visually/configure-repo.png)

**Nastavení úložiště** otevře se podokno konfigurace:

![Konfigurovat nastavení úložiště kódu](media/author-visually/repo-settings.png)

V podokně se zobrazí následující kód úložišť Azure nastavení úložiště:

| Nastavení | Popis | Value |
|:--- |:--- |:--- |
| **Typ úložiště** | Typ úložiště kódu úložiště Azure.<br/> | Azure úložišť Git |
| **Azure Active Directory** | Název tenanta Azure AD. | `<your tenant name>` |
| **Organizace úložiště Azure** | Název organizace vašich úložišť Azure. Můžete vyhledat název organizace vašich úložišť Azure na `https://{organization name}.visualstudio.com`. Je možné [Přihlaste se k vaší organizaci Azure úložišť](https://www.visualstudio.com/team-services/git/) pro přístup k profilu Visual Studio a zobrazit projekty a úložiště. | `<your organization name>` |
| **ProjectName** | Název projektu úložiště Azure. Můžete vyhledat název projektu úložiště Azure na `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **RepositoryName** | Název úložiště kódu vašeho úložiště Azure. Projekty Azure úložiště obsahovat úložiště Git pro správu zdrojového kódu, jak se projekt rozrůstá. Můžete vytvořit nové úložiště nebo použít existující úložiště, který je již ve vašem projektu. | `<your Azure Repos code repository name>` |
| **Spolupráce větve** | Větvi úložiště Azure spolupráci, které slouží k publikování. Ve výchozím nastavení je to `master`. Toto nastavení změňte, v případě, že chcete publikovat prostředky z jiné větve. | `<your collaboration branch name>` |
| **Kořenová složka** | Kořenové složky ve vaší větvi spolupráci úložiště Azure. | `<your root folder name>` |
| **Importovat do úložiště stávající prostředky Data Factory** | Určuje, jestli se má naimportovat stávající prostředky data factory z uživatelského rozhraní **plátno pro vytváření obsahu** do úložiště Git v úložišti Azure. Vyberte pole pro import prostředky data factory do přidružené úložiště Git ve formátu JSON. Tato akce exportuje každého prostředku zvlášť (to znamená, propojené služby a datové sady se exportují do samostatných JSONs). Když toto políčko není zaškrtnuto, nenaimportují se existující prostředky. | Vybrané (výchozí) |

#### <a name="configuration-method-2-azure-repos-git-repo-ux-authoring-canvas"></a>Metoda konfigurace 2 (úložiště Git v úložišti Azure): UX plátno pro vytváření obsahu
V uživatelském prostředí Azure Data Factory **plátno pro vytváření obsahu**, vyhledejte svou datovou továrnu. Vyberte **služby Data Factory** rozevírací nabídky a pak vyberte **konfigurace úložiště kódu**.

Otevře se podokno konfigurace. Podrobnosti o nastavení konfigurace najdete v popisech v <a href="#method1">metody konfigurace 1</a>.

![Konfigurovat nastavení úložiště kódu pro vytváření uživatelského rozhraní](media/author-visually/configure-repo-2.png)

### <a name="use-a-different-azure-active-directory-tenant"></a>Použít na jiného tenanta Azure Active Directory

Úložišti Git v úložišti Azure můžete vytvořit v jiném tenantovi Azure Active Directory. Pokud chcete zadat jinou tenanta Azure AD, musíte mít oprávnění správce pro předplatné Azure, které používáte.

### <a name="use-your-personal-microsoft-account"></a>Použití osobního účtu Microsoft

Použití osobního účtu Microsoft k integraci Gitu, můžete propojit s osobním úložišti Azure Active Directory vaší organizace.

1. Přidejte svůj osobní účet Microsoft do služby Active Directory vaší organizace v roli hosta. Další informace najdete v tématu [uživatele přidat Azure Active Directory s B2B spolupráce na webu Azure Portal](../active-directory/b2b/add-users-administrator.md).

2. Přihlaste se k webu Azure portal pomocí osobního účtu Microsoft. Přepněte se do služby Active Directory vaší organizace.

3. Přejděte do části Azure DevOps, kde uvidíte vaše osobní úložiště. Vyberte úložiště a připojte se službou Active Directory.

Po provedení těchto kroků konfigurace při nastavování integrace Gitu v Uživatelském rozhraní služby Data Factory je dostupná osobní úložiště.

Další informace o připojení úložiště Azure do služby Active Directory vaší organizace najdete v tématu [organizaci Azure DevOps se připojit ke službě Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

### <a name="switch-to-a-different-git-repo"></a>Přepnout na jiné úložiště Git

Přepnout na jiné úložiště Git, vyhledejte ikonu v pravém horním rohu na stránce Přehled služby Data Factory, jak je znázorněno na následujícím snímku obrazovky. Pokud nevidíte ikonu, vymažte mezipaměť místní prohlížeče. Vyberte ikonu a odebere přidružení aktuální úložiště.

Po odebrání přidružení k aktuální úložiště, můžete nakonfigurovat nastavení Git použít jiné úložiště. Pak můžete importovat stávající prostředky Data Factory do nového úložiště.

![Odebere přidružení aktuálního úložiště Git](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>Správa verzí
Systémy správy verzí (označované také jako _správy zdrojového kódu_) umožňují vývojářům spolupráce na kódu a sledování změn provedených na kód základní. Správy zdrojového kódu je to důležitý nástroj pro vývojáře více projekty.

Každé úložiště Git v Azure úložiště, který je spojen s data factory má větev spolupráci. (`master` je výchozím nastavení spolupráci větev). Uživatelé mohou také vytvářet větve funkcí kliknutím **+ novou větev** a vývoj v větve funkcí.

![Změnit kód synchronizace nebo publikování](media/author-visually/sync-publish.png)

Jakmile budete připraveni s vývojem pro funkce ve vaší větvi funkce, můžete kliknout na **vytvořit žádost o přijetí změn**. Tato akce se provede Azure úložišť Git, kde může vyvolat žádosti o přijetí změn, revizemi kódu a sloučit změny do větve spolupráci. (`master` je výchozí nastavení). Jsou povoleny pouze pro publikování do služby Data Factory ze své větve spolupráci. 

![Vytvořte novou žádost o přijetí změn](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurace nastavení publikování

Konfigurace publikování větve – to znamená, větev, ve kterém se uloží šablon Resource Manageru – přidání `publish_config.json` soubor do kořenové složky ve větvi spolupráci. Data Factory čtení tohoto souboru, vyhledá pole `publishBranch`a vytvoří novou větev s zadaná hodnota (pokud ještě neexistuje). Pak uloží všechny šablony Resource Manageru do zadaného umístění. Příklad:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Při publikování z režimu Git můžete potvrdit, že Data Factory používá publikování větve, které jste očekávali, jak je znázorněno na následujícím snímku obrazovky:

![Potvrďte správné publikování větve](media/author-visually/configure-publish-branch.png)

Pokud chcete zadat novou větev publikovat, nedojde k odstranění předchozí větev publikovat služby Data Factory. Pokud chcete na vzdálený počítač předchozí publikování větve, odstraňte jej ručně.

Pouze načte datovou továrnu `publish_config.json` souboru, když načte objekt pro vytváření. Pokud už máte příslušný objekt pro vytváření načíst na portálu, aktualizujte prohlížeč, aby provedené změny projevily.

### <a name="publish-code-changes"></a>Publikování změn kódu
Poté, co mají sloučit změny do větve spolupráce (`master` je výchozí nastavení), vyberte **publikovat** ručního publikování změn kódu v hlavní větvi do služby Data Factory.

![Publikujte změny do služby Data Factory](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> Hlavní větev se nemusí shodovat s co je nasazená ve službě Data Factory. V hlavní větvi *musí* ručně publikovat do služby Data Factory.

### <a name="advantages-of-git-integration"></a>Výhody integrace Gitu

-   **Správa zdrojového kódu**. Objekt pro vytváření úloh data jsou důležitá, byste to svým objektem pro vytváření integrovat Git využívat několik výhod zdrojový ovládací prvek vypadat asi takto:
    -   Umožňuje sledovat a auditovat změny.
    -   Možnost vrácení změn, které zavedeny chyby.
-   **Částečné uloží**. Při provádění velké množství změn ve výrobě, bude Uvědomte si, že v pravidelných ŽIVÉM režimu, nelze uložit změny jako koncept, protože nejste připraveni nebo nechcete přijít o provedené změny v případě, že dojde k chybě v počítači. Integrace Gitu můžete pokračovat v ukládání změny postupně a pouze v případě, že jste připraveni publikovat k objektu pro vytváření. Git funguje jako pracovní místo, kde vaši práci, dokud jste otestovali změny vašich potřeb.
-   **Spolupráce a ovládací prvek**. Pokud máte více členy týmu podílejících se na stejný objekt pro vytváření, můžete chtít nechat vašeho týmu spolupracovat s kolegy přes proces revize kódu. Svým objektem pro vytváření můžete také nastavit tak, že ne každý Přispěvatel k objektu pro vytváření nemá oprávnění k nasazení pro vytváření. Členové týmu mohou být pouze povoleno provádět změny přes Git, ale pouze určité osoby v týmu můžou "Publikovat" změny k objektu pro vytváření.
-   **Zobrazuje rozdíly**. Režim Git, získáte zobrazíte nice diff datové části, která se týká chcete publikovat pro vytváření. Tento rozdíl ukazuje všechny prostředky a entity, které máte změnit/přidat/odstranit od posledního, kterou jste publikovali objektem pro vytváření. Podle tohoto rozdílu, můžete pokračovat dále s publikování, nebo přejděte zpět a zkontrolujte provedené změny a pak se vraťte později.
-   **Lepší CI/CD**. Pokud používáte režim Git, můžete nakonfigurovat kanál pro vydávání verzí pro co nejdříve spustit automaticky, protože jsou všechny změny provedené v objektu pro vytváření vývoj. Získáte také přizpůsobit vlastnosti ve výrobě, které jsou k dispozici jako parametry v šabloně Resource Manageru. Může být užitečné zachovat pouze požadované sadu vlastností, které jako parametry a mít vše, co jinak pevné programového.
-   **Vyšší výkon**. Objekt pro vytváření průměrné načte časy 10 x rychlejší režim Git než v pravidelných ŽIVÉM režimu, protože prostředky se stáhnou přes Git.

### <a name="best-practices-for-git-integration"></a>Osvědčené postupy pro integraci Gitu

-   **Oprávnění**. Obvykle nechcete, aby všichni členové týmu mít oprávnění k aktualizaci objekt pro vytváření.
    -   Všichni členové týmu by měl mít oprávnění ke čtení s datovou továrnou.
    -   Jenom vybraná sada uživatelů má povolený pro publikování na objekt pro vytváření a pro, musí se jednat o část role "Přispěvatel Data Factory" na objekt pro vytváření.
    -   Jedním z osvědčených postupů správy zdrojového kódu je také nepovoluje přímou vrácení se změnami do větve spolupráci. Tento požadavek brání chyby při každém vrácení se změnami prochází proces žádosti o přijetí změn.
-   **Přepínání režimů**.
    -    Až se režim Git, nedoporučujeme vám přepínat do živého režimu primárně, protože všechny změny provedené v ŽIVÉM režimu, se projeví, když přepnete zpět do Gitu. Pokuste se provést změny v Gitu režimu samotné a publikujte je prostřednictvím uživatelského rozhraní.
    -   Podobně nepoužívejte rutiny prostředí powershell objekt pro vytváření dat, protože, stejného výsledku dosáhnout přímo při použití zadané změn živé objekt pro vytváření.
-   **Používat hesla ze služby Azure Key Vault**.
    -   Důrazně doporučujeme používat AzureKeyVault pro uložení všech připojovací řetězce nebo hesla pro propojené služby DataFactory.
    -   Neukládáme těchto tajných informací v úložišti Git (z bezpečnostních důvodů), takže všechny změny propojené služby jsou publikovány hned za objekt pro vytváření. Tato okamžité publikování někdy nežádoucí, protože nemusí mohli otestovat změny, které poráží účel Git.
    -   Všechny tyto tajné klíče v důsledku toho musí být získána z propojené služby, která používá na základě služby Azure Key Vault.
    -   Některé další výhody používání služby Key Vault je, že to usnadňuje CICD, tím, že zajišťují těchto tajných kódů správce prostředků během nasazování šablony.

## <a name="author-with-github-integration"></a>Autor integrace Githubu

Pro práci na vaše kanály data factory vizuálním vytváření s integraci Githubu podporuje správu zdrojového kódu a spolupráci. Datové továrny můžete přidružit účet úložiště GitHub pro správy zdrojového kódu, spolupráci, správu verzí. Jeden účet GitHub může mít více úložišť, ale úložiště GitHub může být přidružený pouze jeden datové továrny. Pokud nemáte účet GitHub nebo úložiště, postupujte podle [tyto pokyny](https://github.com/join) k vytvoření vašich prostředků.

Integrace GitHub pomocí služby Data Factory podporuje obě veřejné Githubu (to znamená [ https://github.com ](https://github.com)) a Githubu Enterprise. Pomocí služby Data Factory můžete použít veřejná i soukromá úložiště GitHub, jak dlouho budete mít ke čtení a oprávnění k zápisu do úložiště v Githubu.

Konfigurace úložiště GitHub, musíte mít oprávnění správce pro předplatné Azure, které používáte.

Pro zavedení devět po minutách a ukázku této funkce z následujícího videa:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="limitations"></a>Omezení

- Skript a datových souborů můžete uložit v úložišti GitHub. Ale budete muset ručně nahrání souborů do služby Azure Storage. Kanál služby Data Factory není automaticky odeslat soubory skriptu nebo data uložená v úložišti GitHub do služby Azure Storage.

- GitHub Enterprise s verzí starší než 2.14.0 nefunguje v prohlížeči Microsoft Edge.

- Integrace Githubu s nástroji visual pro vytváření dat faktor funguje jenom v obecně dostupné verzi Data Factory.

### <a name="configure-a-public-github-repository-with-azure-data-factory"></a>Konfigurace veřejného úložiště GitHub s Azure Data Factory

Úložiště GitHub pomocí služby data factory můžete nakonfigurovat pomocí dvou metod.

**Konfigurace metody 1 (veřejné úložiště): Stránka Začínáme**

Ve službě Azure Data Factory, přejděte **pusťme se do práce** stránky. Vyberte **konfigurace úložiště kódu**:

![Stránka Začínáme objekt pro vytváření dat](media/author-visually/github-integration-image1.png)

 **Nastavení úložiště** otevře se podokno konfigurace:

![Nastavení úložiště GitHub](media/author-visually/github-integration-image2.png)

V podokně se zobrazí následující kód úložišť Azure nastavení úložiště:

| **Nastavení**                                              | **Popis**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Hodnota**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Typ úložiště**                                      | Typ úložiště kódu úložiště Azure.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Účet GitHub**                                       | Název účtu GitHub. Tento název najdete v protokolu https:\//github.com/{account název} / {název úložiště}. Přejdete na tuto stránku vyzve k zadání přihlašovacích údajů Githubu OAuth ke svému účtu GitHub.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | Název úložiště GitHub kódu. Účtů GitHub obsahovat úložiště Git pro správu zdrojového kódu. Můžete vytvořit nové úložiště nebo použít existující úložiště, který je již ve vašem účtu.                                                                                                                                                                                                                              |                    |
| **Spolupráce větve**                                 | Větvi Githubu spolupráci, které slouží k publikování. Ve výchozím nastavení je hlavní. Toto nastavení změňte, v případě, že chcete publikovat prostředky z jiné větve.                                                                                                                                                                                                                                                               |                    |
| **Kořenová složka**                                          | Kořenové složky ve vaší větvi spolupráci Githubu.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Importovat do úložiště stávající prostředky Data Factory** | Určuje, jestli se má naimportovat stávající prostředky data factory z uživatelského rozhraní **plátno pro vytváření obsahu** do úložiště GitHub. Vyberte pole pro import prostředky data factory do přidružené úložiště Git ve formátu JSON. Tato akce exportuje každého prostředku zvlášť (to znamená, propojené služby a datové sady se exportují do samostatných JSONs). Když toto políčko není zaškrtnuto, nenaimportují se existující prostředky. | Vybrané (výchozí) |
| **Větev se má importovat prostředky do**                       | Určuje, do které větve se importují prostředky data factory (kanály, datové sady, propojených služeb atd.). Prostředky můžete importovat do jednoho z následujících větví:. Spolupráce b. Vytvořte nový c. Použít existující                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-public-repo-ux-authoring-canvas"></a>Metoda konfigurace 2 (veřejné úložiště): UX plátno pro vytváření obsahu

V uživatelském prostředí Azure Data Factory **plátno pro vytváření obsahu**, vyhledejte svou datovou továrnu. Vyberte **služby Data Factory** rozevírací nabídky a pak vyberte **konfigurace úložiště kódu**.

Otevře se podokno konfigurace. Podrobnosti o nastavení konfigurace najdete v popisech v *metody konfigurace 1* výše.

### <a name="configure-a-github-enterprise-repository-with-azure-data-factory"></a>Nakonfigurujte úložiště Githubu Enterprise s Azure Data Factory

Úložiště GitHub Enterprise můžete nakonfigurovat pomocí služby data factory pomocí dvou metod.

#### <a name="configuration-method-1-enterprise-repo-lets-get-started-page"></a>Konfigurace metody 1 (Enterprise úložiště): Stránka Začínáme

Ve službě Azure Data Factory, přejděte **pusťme se do práce** stránky. Vyberte **konfigurace úložiště kódu**:

![Stránka Začínáme objekt pro vytváření dat](media/author-visually/github-integration-image1.png)

 **Nastavení úložiště** otevře se podokno konfigurace:

![Nastavení úložiště GitHub](media/author-visually/github-integration-image3.png)

V podokně se zobrazí následující kód úložišť Azure nastavení úložiště:

| **Nastavení**                                              | **Popis**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Hodnota**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Typ úložiště**                                      | Typ úložiště kódu úložiště Azure.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Používání Githubu Enterprise**                                | Pokud chcete vybrat Githubu Enterprise                                                                                                                                                                                                                                                                                                                                                                                              |                    |
| **Adresa URL Githubu Enterprise**                                | Adresa URL kořenového Githubu Enterprise. Příklad: https://github.mydomain.com                                                                                                                                                                                                                                                                                                                                                          |                    |
| **Účet GitHub**                                       | Název účtu GitHub. Tento název najdete v protokolu https:\//github.com/{account název} / {název úložiště}. Přejdete na tuto stránku vyzve k zadání přihlašovacích údajů Githubu OAuth ke svému účtu GitHub.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | Název úložiště GitHub kódu. Účtů GitHub obsahovat úložiště Git pro správu zdrojového kódu. Můžete vytvořit nové úložiště nebo použít existující úložiště, který je již ve vašem účtu.                                                                                                                                                                                                                              |                    |
| **Spolupráce větve**                                 | Větvi Githubu spolupráci, které slouží k publikování. Ve výchozím nastavení je hlavní. Toto nastavení změňte, v případě, že chcete publikovat prostředky z jiné větve.                                                                                                                                                                                                                                                               |                    |
| **Kořenová složka**                                          | Kořenové složky ve vaší větvi spolupráci Githubu.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Importovat do úložiště stávající prostředky Data Factory** | Určuje, jestli se má naimportovat stávající prostředky data factory z uživatelského rozhraní **plátno pro vytváření obsahu** do úložiště GitHub. Vyberte pole pro import prostředky data factory do přidružené úložiště Git ve formátu JSON. Tato akce exportuje každého prostředku zvlášť (to znamená, propojené služby a datové sady se exportují do samostatných JSONs). Když toto políčko není zaškrtnuto, nenaimportují se existující prostředky. | Vybrané (výchozí) |
| **Větev se má importovat prostředky do**                       | Určuje, do které větve se importují prostředky data factory (kanály, datové sady, propojených služeb atd.). Prostředky můžete importovat do jednoho z následujících větví:. Spolupráce b. Vytvořte nový c. Použít existující                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-enterprise-repo-ux-authoring-canvas"></a>Metoda konfigurace 2 (Enterprise úložiště): UX plátno pro vytváření obsahu

V uživatelském prostředí Azure Data Factory **plátno pro vytváření obsahu**, vyhledejte svou datovou továrnu. Vyberte **služby Data Factory** rozevírací nabídky a pak vyberte **konfigurace úložiště kódu**.

Otevře se podokno konfigurace. Podrobnosti o nastavení konfigurace najdete v popisech v *metody konfigurace 1* výše.

## <a name="use-the-expression-language"></a>Použijte jazyk výrazů
Výrazy pro hodnoty vlastnosti můžete určit pomocí výrazů jazyka, který je podporovaný službou Azure Data Factory.

Zadejte výrazy pro hodnoty vlastnosti tak, že vyberete **Přidání dynamického obsahu**:

![Použijte jazyk výrazů](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>Pomocí služby functions a parametry

Můžete použít funkce nebo zadejte parametry pro kanálů a datových sad v datové továrně **Tvůrce výrazů**:

Informace o podporovaných výrazů naleznete v tématu [výrazům a funkcím ve službě Azure Data Factory](control-flow-expression-language-functions.md).

![Přidat dynamický obsah](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>Poslat názor
Vyberte **zpětnou vazbu** komentovat funkcí nebo oznámení o problémech s nástrojem Microsoft:

![Zpětná vazba](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Další postup
Další informace o monitorování a Správa kanálů najdete v tématu [monitorování a Správa kanálů prostřednictvím kódu programu](monitor-programmatically.md).
