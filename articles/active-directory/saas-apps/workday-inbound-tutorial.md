---
title: 'Kurz: Konfigurace pracovního dne pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů do Workday.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 298c99d44328dc79db1722b450ad74c3929d0c12
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114415"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Kurz: Konfigurace pracovního dne pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést při importu profilů pracovních procesů z Workday do služby Active Directory a Azure Active Directory, s volitelným zpětným zápisem e-mailové adresy a uživatelského jména do Workday.

## <a name="overview"></a>Přehled

[Služba zřizování uživatelů Azure Active Directory](../app-provisioning/user-provisioning.md) se integruje s [rozhraním API Workday pro lidské zdroje](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) , aby bylo možné zřídit uživatelské účty. Azure AD pomocí tohoto připojení povolí následující pracovní postupy zřizování uživatelů:

* **Zřizování uživatelů ke službě Active Directory** – zřídí vybrané sady uživatelů z Workday do jedné nebo víc domén služby Active Directory.

* **Zřizování pouze cloudových uživatelů k Azure Active Directory** scénářů, ve kterých se nepoužívá místní služba Active Directory, je možné uživatele zřídit přímo z Workday a Azure Active Directory pomocí služby zřizování uživatelů Azure AD.

* **Zápis e-mailové adresy a uživatelského jména do Workday** – služba zřizování uživatelů Azure AD může napsat e-mailové adresy a uživatelské jméno z Azure AD zpátky do Workday.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Jaké scénáře lidských zdrojů pokrývá?

Pracovní postupy zřizování uživatelů Workday, které podporuje služba zřizování uživatelů Azure AD, umožňují automatizaci následujících scénářů lidských zdrojů a životního cyklu identit:

* Připravují se **noví zaměstnanci** – když se do Workday přidá nový zaměstnanec, automaticky se vytvoří uživatelský účet ve službě Active Directory, Azure Active Directory a volitelně Office 365 a [Další aplikace SaaS, které Azure AD podporuje](../app-provisioning/user-provisioning.md), s zpětným zápisem e-mailové adresy do Workday.

* **Aktualizace atributů a profilů zaměstnanců** – když se v Workday aktualizuje záznam zaměstnance (například jeho jméno, název nebo manažer), automaticky se aktualizuje jeho uživatelský účet ve službě Active Directory, Azure Active Directory a volitelně na Office 365 a [jiné aplikace SaaS podporované službou Azure AD](../app-provisioning/user-provisioning.md).

* **Ukončení zaměstnanců** – když se zaměstnanec v Workday ukončí, jejich uživatelský účet je automaticky zakázaný ve službě Active Directory, Azure Active Directory a volitelně Office 365 a [Další aplikace SaaS podporované službou Azure AD](../app-provisioning/user-provisioning.md).

* **Pracovní zařazení zaměstnanců** – když se zaměstnanec v Workday odkoupí, jeho starý účet se dá automaticky znovu aktivovat nebo znovu zřídit (v závislosti na vaší preferenci) pro Active Directory, Azure Active Directory a volitelně Office 365 a [Další aplikace SaaS podporované službou Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Na koho se toto řešení pro zřizování uživatelů nejlépe hodí?

Toto řešení pro zřizování uživatelů v Workday je ideální pro:

* Organizace, které chtějí předem připravené cloudové řešení pro zřizování uživatelů v Workday

* Organizace, které vyžadují přímé zřizování uživatelů z Workday do služby Active Directory nebo Azure Active Directory

* Organizace, které vyžadují zřízení uživatelů pomocí dat získaných z modulu Workday HCM (viz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizace, které vyžadují, aby se do jedné nebo více doménových struktur služby Active Directory, domén a organizačních jednotek připojovaly k synchronizaci s jednou nebo více doménovými strukturami, a to na základě informací o změně zjištěných v modulu HCM (viz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizace, které používají Office 365 k e-mailu

## <a name="solution-architecture"></a>Architektura řešení

Tato část popisuje kompletní architekturu řešení zřizování uživatelů pro běžná hybridní prostředí. Existují dva související toky:

* **Autoritativní tok dat o lidských přenosech – od Workday po místní službu Active Directory:** V těchto událostech pracovního procesu toku (například nových zaměstnanců, přenosů, ukončení) se nejprve v tenantovi cloudového ochrany před únikem informací a následně data událostí najdou do místní služby Active Directory prostřednictvím služby Azure AD a zřizovacího agenta. V závislosti na události to může vést k vytváření, aktualizaci, povolování a zakazování operací ve službě AD.
* **Tok zpětného zápisu e-mailu a uživatelského jména – od místní služby Active Directory až po pracovní den:** Jakmile se účet vytvoří ve službě Active Directory, synchronizuje se s Azure AD prostřednictvím Azure AD Connect a atribut email a username se dá zapsat zpátky do Workday.

![Přehled](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Koncový tok dat uživatele

1. Tým pro personální oddělení provádí pracovní transakce (připojení a stěhovací pracovníky/Leaversy nebo nová přijetí/převody/ukončení) ve Workday HCM
2. Služba zřizování Azure AD spouští naplánované synchronizace identit z pracovního dne a identifikuje změny, které je potřeba zpracovat pro synchronizaci s místní službou Active Directory.
3. Služba zřizování Azure AD vyvolá místního agenta pro zřizování Azure AD Connect s datovou částí požadavku obsahující účet služby AD, který obsahuje operace vytvořit/aktualizovat/povolit/zakázat.
4. Agent zřizování Azure AD Connect používá účet služby k přidání nebo aktualizaci dat účtu AD.
5. Modul Azure AD Connect/AD Sync spouští rozdílovou synchronizaci pro vyžádání aktualizací ve službě AD.
6. Aktualizace služby Active Directory se synchronizují s Azure Active Directory.
7. Pokud je konektor pro zpětný zápis do pracovního dne nakonfigurovaný, zapisuje zpátky atribut e-mailu a uživatelské jméno do pracovního dne v závislosti na použitém atributu odpovídajícího.

## <a name="planning-your-deployment"></a>Plánování nasazení

Než začnete s integrací do pracovního dne, Projděte si níže uvedené požadavky a přečtěte si následující pokyny, jak porovnat aktuální architekturu služby Active Directory a požadavky na zřizování uživatelů s řešeními, která poskytuje Azure Active Directory. K dispozici je také kompletní [plán nasazení](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) s plánovacími listy, který vám pomůže při spolupráci s vaším partnerem pro integraci pracovního dne a zúčastněnými stranami lidských zdrojů.

Tato část se zabývá následujícími aspekty plánování:

* [Požadované součásti](#prerequisites)
* [Výběr aplikací zřizovacích konektorů k nasazení](#selecting-provisioning-connector-apps-to-deploy)
* [Plánování nasazení agenta zřizování Azure AD Connect](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integrace s více doménami služby Active Directory](#integrating-with-multiple-active-directory-domains)
* [Plánování mapování a transformací atributů uživatele z Workday na službu Active Directory](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Požadované součásti

Scénář popsaný v tomto kurzu předpokládá, že už máte následující položky:

* Platná licence předplatného Azure AD Premium P1 nebo vyšší pro každého uživatele, který bude vytvořen z Workday a zřízený do místní služby Active Directory nebo Azure Active Directory.
* Přístup globálního správce služby Azure AD ke konfiguraci zřizovacího agenta
* Tenant implementace pracovního dne pro účely testování a integrace
* Oprávnění správce v Workday k vytvoření uživatele pro integraci systému a provádění změn v datech testu zaměstnanců pro účely testování
* Pro hostování [místního zřizovacího agenta](https://go.microsoft.com/fwlink/?linkid=847801) se pro zřizování uživatelů pro službu Active Directory vyžaduje server se systémem Windows Server 2012 nebo vyšší s modulem runtime .NET 4.7.1 +.
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) synchronizace uživatelů mezi službou Active Directory a službou Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Výběr aplikací zřizovacích konektorů k nasazení

Pro usnadnění zřizování pracovních postupů mezi Workday a službou Active Directory poskytuje Azure AD několik aplikací konektoru pro zřizování, které můžete přidat z Galerie aplikací Azure AD:

![Galerie Aplikace Azure AD](./media/workday-inbound-tutorial/wd_gallery.png)

* **Zřizování uživatelů z Workday do služby Active Directory** – Tato aplikace usnadňuje zřizování uživatelských účtů z Workday do jedné domény služby Active Directory. Pokud máte více domén, můžete pro každou doménu Active Directory, pro kterou potřebujete zřídit, přidat jednu instanci této aplikace z Galerie aplikací Azure AD.

* **Zřizování uživatelů z Workday do Azure AD** – když Azure AD Connect je nástroj, který by se měl použít k synchronizaci uživatelů Active Directory s Azure Active Directory, tato aplikace se dá použít k usnadnění zřizování pouze cloudových uživatelů z Workday do jednoho Azure Active Directory tenanta.

* **Zpětný zápis do Workday** – Tato aplikace usnadňuje zpětný zápis e-mailových adres uživatele z Azure Active Directory do Workday.

> [!TIP]
> Běžná aplikace "Workday" se používá k nastavení jednotného přihlašování mezi Workday a Azure Active Directory.

Pomocí níže uvedeného diagramu rozhodnutí určete, které aplikace pro zřizování Workday jsou relevantní pro váš scénář.
    ![Vývojový diagram rozhodování](./media/workday-inbound-tutorial/wday_app_flowchart.png "Vývojový diagram rozhodování")

Pomocí obsahu můžete přejít k příslušné části tohoto kurzu.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Plánování nasazení agenta zřizování Azure AD Connect

> [!NOTE]
> Tato část je relevantní jenom v případě, že plánujete nasadit pracovní den do aplikace služby Active Directory pro zřizování uživatelů. Tuto možnost můžete přeskočit, pokud nasazujete službu pro zápis do Workday nebo Workday do služby Azure AD pro zřizování uživatelů.

Řešení pro zřizování uživatelů z Workday do AD vyžaduje nasazení jednoho nebo více agentů zřizování na serverech se systémem Windows 2012 R2 nebo vyšším s minimálním počtem 4 GB paměti RAM a .NET 4.7.1 + runtime. Před instalací agenta zřizování je třeba vzít v úvahu následující skutečnosti:

* Ujistěte se, že hostitelský server, na kterém je spuštěný agent zřizování, má síťový přístup k cílové doméně AD.
* Průvodce konfigurací agenta zřizování registruje agenta u vašeho tenanta Azure AD a proces registrace vyžaduje přístup k *. msappproxy.net přes port TLS 443. Zajistěte, aby byla k dismístě odchozí pravidla brány firewall, která tuto komunikaci povolují. Agent podporuje [konfiguraci proxy serveru HTTPS](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* Agent zřizování používá účet služby ke komunikaci s místními doménami služby AD. Před instalací agenta doporučujeme vytvořit účet služby s oprávněními správce domény a heslem, jehož platnost nevyprší.  
* Během konfigurace agenta zřizování můžete vybrat řadiče domény, které by měly zpracovávat požadavky zřizování. Pokud máte několik geograficky distribuovaných řadičů domény, nainstalujte agenta zřizování ve stejné lokalitě jako vaše preferované řadiče domény, aby se zlepšila spolehlivost a výkon kompletního řešení.
* Pro zajištění vysoké dostupnosti můžete nasadit víc než jednoho zřizovacího agenta a zaregistrovat ho pro zpracování stejné sady místních domén AD.

> [!IMPORTANT]
> V produkčních prostředích Microsoft doporučuje, abyste měli k dispozici minimálně 3 zřizovacích agentů nakonfigurovaných pro vašeho tenanta Azure AD, aby se dosáhlo vysoké dostupnosti.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integrace s více doménami služby Active Directory

> [!NOTE]
> Tato část je relevantní jenom v případě, že plánujete nasadit pracovní den do aplikace služby Active Directory pro zřizování uživatelů. Tuto možnost můžete přeskočit, pokud nasazujete službu pro zápis do Workday nebo Workday do služby Azure AD pro zřizování uživatelů.

V závislosti na topologii služby Active Directory budete muset určit počet aplikací konektoru pro zřizování uživatelů a počet agentů zřizování, které se mají nakonfigurovat. Níže jsou uvedené některé z běžných vzorů nasazení, na které můžete při plánování nasazení odkazovat.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Scénář nasazení #1: tenant s jedním Workday – > jedna doména AD

V tomto scénáři máte jednoho tenanta Workday a chcete zřídit uživatele na jednu cílovou doménu AD. Tady je doporučená produkční konfigurace pro toto nasazení.

|   |   |
| - | - |
| Ne. zřizování agentů pro místní nasazení | 3 (pro zajištění vysoké dostupnosti a převzetí služeb při selhání) |
| Ne. Aplikace pro zřizování uživatelů z Workday do služby AD pro konfiguraci v Azure Portal | 1 |

  ![Scénář 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Scénář nasazení #2: tenant s jedním Workday – > více podřízených domén AD

Tento scénář zahrnuje zřizování uživatelů z Workday do několika cílových doménových podřízených objektů AD v doménové struktuře. Tady je doporučená produkční konfigurace pro toto nasazení.

|   |   |
| - | - |
| Ne. zřizování agentů pro místní nasazení | 3 (pro zajištění vysoké dostupnosti a převzetí služeb při selhání) |
| Ne. Aplikace pro zřizování uživatelů z Workday do služby AD pro konfiguraci v Azure Portal | Jedna aplikace na podřízenou doménu |

  ![Scénář 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Scénář nasazení #3: jeden tenant tenanta – > nesouvislé doménové struktury AD

Tento scénář zahrnuje zřizování uživatelů z Workday do domén v nesouvislých doménových strukturách AD. Tady je doporučená produkční konfigurace pro toto nasazení.

|   |   |
| - | - |
| Ne. zřizování agentů pro místní nasazení | 3 na jednu nesouvislou doménovou strukturu služby AD |
| Ne. Aplikace pro zřizování uživatelů z Workday do služby AD pro konfiguraci v Azure Portal | Jedna aplikace na podřízenou doménu |

  ![Scénář 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Plánování mapování a transformací atributů uživatele z Workday na službu Active Directory

> [!NOTE]
> Tato část je relevantní jenom v případě, že plánujete nasadit pracovní den do aplikace služby Active Directory pro zřizování uživatelů. Tuto možnost můžete přeskočit, pokud nasazujete službu pro zápis do Workday nebo Workday do služby Azure AD pro zřizování uživatelů.

Před konfigurací zřizování uživatelů pro doménu služby Active Directory Vezměte v úvahu následující otázky. Odpovědi na tyto otázky určují, jak se musí nastavit filtry oboru a mapování atributů.

* **Jaké uživatele v Workday je potřeba zřídit pro tuto doménovou strukturu služby Active Directory?**

  * *Příklad: uživatelé, kde má pracovní den v pracovním poli "společnost" hodnotu "contoso" a atribut "Worker_Type" obsahuje "regular".*

* **Jak jsou uživatelé směrováni do různých organizačních jednotek (OU)?**

  * *Příklad: uživatelé jsou směrováni do organizačních jednotek, které odpovídají umístění kanceláře, jak jsou definovány v poli "obec" a "Country_Region_Reference" atributy.*

* **Jak by měly být ve službě Active Directory naplněny následující atributy?**

  * Běžný název (CN)
    * *Příklad: použijte hodnotu User_ID pracovní den, jak nastaví lidské zdroje.*

  * ID zaměstnance (ČísloZaměstnance)
    * *Příklad: použití hodnoty Worker_ID Workday*

  * Název účtu SAM (sAMAccountName)
    * *Příklad: použijte hodnotu User_ID Workday filtrovanou pomocí výrazu zřizování Azure AD a odeberte nepovolené znaky.*

  * Hlavní název uživatele (userPrincipalName)
    * *Příklad: použití hodnoty User_ID Workday s výrazem zřizování Azure AD k připojení názvu domény*

* **Jak se mají uživatelé shodovat mezi Workday a službou Active Directory?**

  * *Příklad: uživatelé s konkrétní hodnotou Workday "Worker_ID" se shodují s uživateli služby Active Directory, kde "ČísloZaměstnance" má stejnou hodnotu. Pokud hodnota Worker_ID nebyla nalezena ve službě Active Directory, vytvořte nového uživatele.*
  
* **Obsahuje doménová struktura služby Active Directory již ID uživatelů, která jsou nutná k fungování vyhovující logiky?**

  * *Příklad: Pokud se jedná o nové nasazení v Workday, doporučuje se, aby služba Active Directory byla předem naplněná o správné hodnoty Worker_ID pracovního dne (nebo hodnotu jedinečné ID, která je zvolená), aby byla odpovídající logika co nejjednodušší.*

Jak nastavit a nakonfigurovat tyto speciální aplikace zřizovacích konektorů jsou předmětem zbývajících částí tohoto kurzu. Které aplikace, které chcete konfigurovat, budou záviset na tom, jaké systémy potřebujete zřídit, a kolik domén služby Active Directory a tenantů Azure AD jsou ve vašem prostředí.

## <a name="configure-integration-system-user-in-workday"></a>Konfigurace uživatele integračního systému v Workday

Běžným požadavkem na všechny konektory zřizování Workday je to, že vyžadují přihlašovací údaje uživatele systému pro integraci Workday, aby se připojili k rozhraní API Workday pro lidské zdroje. Tato část popisuje, jak vytvořit uživatele integračního systému v Workday a obsahuje následující části:

* [Vytvoření uživatele integračního systému](#creating-an-integration-system-user)
* [Vytvoření skupiny zabezpečení integrace](#creating-an-integration-security-group)
* [Konfigurace oprávnění zásad zabezpečení domény](#configuring-domain-security-policy-permissions)
* [Konfigurace oprávnění zásad zabezpečení obchodních procesů](#configuring-business-process-security-policy-permissions)
* [Aktivují se změny zásad zabezpečení.](#activating-security-policy-changes)

> [!NOTE]
> Tento postup je možné obejít a místo toho použít účet globálního správce Workday jako účet System Integration. To může pro ukázky fungovat správně, ale nedoporučuje se pro produkční nasazení.

### <a name="creating-an-integration-system-user"></a>Vytvoření uživatele integračního systému

**Postup vytvoření uživatele Integration System:**

1. Přihlaste se ke svému tenantovi Workday pomocí účtu správce. V **aplikaci Workday**zadejte do vyhledávacího pole vytvořit uživatele a pak klikněte na **vytvořit uživatele Integration System**.

   ![Vytvořit uživatele](./media/workday-inbound-tutorial/wd_isu_01.png "Vytvořit uživatele")
2. Dokončete úkol **vytvořit uživatel Integration System** zadáním uživatelského jména a hesla pro nového uživatele integračního systému.  
  
   * Nechejte možnost **vyžadovat nové heslo při příštím přihlášení** nezaškrtnutou, protože se tento uživatel přihlašuje programově.
   * Ponechte **časový limit relace** s výchozí hodnotou 0, což zabrání předčasně vypršení relace uživatele.
   * Vyberte možnost **Nepovolit relace uživatelského rozhraní** , protože poskytuje přidanou vrstvu zabezpečení, která brání uživateli s heslem systému Integration v přihlášení do Workday.

   ![Vytvořit uživatele Integration System](./media/workday-inbound-tutorial/wd_isu_02.png "Vytvořit uživatele Integration System")

### <a name="creating-an-integration-security-group"></a>Vytvoření skupiny zabezpečení integrace

V tomto kroku vytvoříte v Workday skupinu zabezpečení systému bez omezení nebo omezeného integrování a přiřadíte uživatele integračního systému, který jste vytvořili v předchozím kroku, do této skupiny.

**Vytvoření skupiny zabezpečení:**

1. Do vyhledávacího pole zadejte vytvořit skupinu zabezpečení a pak klikněte na **vytvořit skupinu zabezpečení**.

    ![Skupina CreateSecurity](./media/workday-inbound-tutorial/wd_isu_03.png "Skupina CreateSecurity")
2. Dokončete úlohu **vytvořit skupinu zabezpečení** . 

   * V Workday existují dva typy skupin zabezpečení:
     * Bez **omezení:** Všichni členové skupiny zabezpečení mají přístup ke všem instancím dat zabezpečeným skupinou zabezpečení.
     * **Omezeno:** Všichni členové skupiny zabezpečení mají kontextový přístup k podmnožině datových instancí (řádků), ke kterým má skupina zabezpečení přístup.
   * Pokud chcete pro integraci vybrat odpovídající typ skupiny zabezpečení, obraťte se prosím na svého partnera pro integraci pracovního dne.
   * Jakmile znáte typ skupiny, vyberte možnost **Skupina zabezpečení systému integrace (neomezená)** nebo **Skupina zabezpečení systému Integration System (s omezením)** z nabídky Typ rozevíracího seznamu **Skupina zabezpečení klienta** .

     ![Skupina CreateSecurity](./media/workday-inbound-tutorial/wd_isu_04.png "Skupina CreateSecurity")

3. Po úspěšném vytvoření skupiny zabezpečení se zobrazí stránka, kde můžete přiřadit členy ke skupině zabezpečení. Do této skupiny zabezpečení přidejte nového uživatele integračního systému, který jste vytvořili v předchozím kroku. Pokud používáte *omezené* skupiny zabezpečení, budete také muset vybrat příslušný obor organizace.

    ![Upravit skupinu zabezpečení](./media/workday-inbound-tutorial/wd_isu_05.png "Upravit skupinu zabezpečení")

### <a name="configuring-domain-security-policy-permissions"></a>Konfigurace oprávnění zásad zabezpečení domény

V tomto kroku udělíte skupině zabezpečení oprávnění zásady zabezpečení domény pro data pracovního procesu.

**Konfigurace oprávnění zásad zabezpečení domény:**

1. Do vyhledávacího pole zadejte **Konfigurace zabezpečení domény** a potom klikněte na **sestavu konfigurace zabezpečení domény**propojit.  

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_06.png "Zásady zabezpečení domény")  
2. V textovém poli **doména** vyhledejte následující domény a přidejte je do filtru jednu po jedné.  
   * *Zřizování externího účtu*
   * *Data pracovního procesu: pracovní procesy*
   * *Data pracovního procesu: sestavy veřejného pracovního procesu*
   * *Data osob: pracovní kontaktní informace*
   * *Data pracovního procesu: všechny pozice*
   * *Data pracovního procesu: aktuální personální informace*
   * *Data pracovního procesu: obchodní titul v profilu pracovního procesu*
   * *Účty Workday*
   
     ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_07.png "Zásady zabezpečení domény")  

     ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_08.png "Zásady zabezpečení domény") 

     Klikněte na tlačítko **OK**.

3. V sestavě, která se zobrazí, vyberte tři tečky (...), které se zobrazí vedle **zřizování externího účtu** , a klikněte na možnost nabídky **doména-> upravit oprávnění zásady zabezpečení** .

    ![Zásady zabezpečení domény](./media/workday-inbound-tutorial/wd_isu_09.png "Zásady zabezpečení domény")  

4. Na stránce **Upravit oprávnění zásad zabezpečení domény** přejděte dolů k části **oprávnění k integraci**. Kliknutím na symbol "+" přidejte skupinu integračních systémů do seznamu skupin zabezpečení s oprávněním **získat** a **Vložit** integraci.

    ![Upravit oprávnění](./media/workday-inbound-tutorial/wd_isu_10.png "Upravit oprávnění")  

5. Kliknutím na symbol "+" přidejte skupinu integračních systémů do seznamu skupin zabezpečení s oprávněním **získat** a **Vložit** integraci.

    ![Upravit oprávnění](./media/workday-inbound-tutorial/wd_isu_11.png "Upravit oprávnění")  

6. Opakujte kroky 3-5 výše pro každý z těchto zbývajících zásad zabezpečení:

   | Operace | Zásady zabezpečení domény |
   | ---------- | ---------- |
   | Získat a umístit | Data pracovního procesu: sestavy veřejného pracovního procesu |
   | Získat a umístit | Data osob: pracovní kontaktní informace |
   | Získat | Data pracovního procesu: pracovní procesy |
   | Získat | Data pracovního procesu: všechny pozice |
   | Získat | Data pracovního procesu: aktuální personální informace |
   | Získat | Data pracovního procesu: obchodní titul v profilu pracovního procesu |
   | Získat a umístit | Účty Workday |

### <a name="configuring-business-process-security-policy-permissions"></a>Konfigurace oprávnění zásad zabezpečení obchodních procesů

V tomto kroku udělíte oprávnění "zabezpečení obchodních procesů" pro data pracovního procesu do skupiny zabezpečení. Tento krok je nutný pro nastavení konektoru aplikace zpětného zápisu do Workday.

**Konfigurace oprávnění zásad zabezpečení obchodního procesu:**

1. Do vyhledávacího pole zadejte **zásady obchodního procesu** a pak klikněte na úlohu **upravit zásady zabezpečení obchodních procesů** .  

    ![Zásady zabezpečení obchodních procesů](./media/workday-inbound-tutorial/wd_isu_12.png "Zásady zabezpečení obchodních procesů")  

2. V textovém poli **typ obchodního procesu** vyhledejte *kontakt* a vyberte **pracovní kontakt změnit** obchodní proces a klikněte na **OK**.

    ![Zásady zabezpečení obchodních procesů](./media/workday-inbound-tutorial/wd_isu_13.png "Zásady zabezpečení obchodních procesů")  

3. Na stránce **upravit zásady zabezpečení obchodního procesu** se posuňte do části **Změna pracovních kontaktních informací (webová služba)** .
    

4. Vyberte a přidejte novou skupinu zabezpečení systému Integration Service do seznamu skupin zabezpečení, které mohou iniciovat požadavek webové služby. 

    ![Zásady zabezpečení obchodních procesů](./media/workday-inbound-tutorial/wd_isu_15.png "Zásady zabezpečení obchodních procesů")  

5. Klikněte na **Hotovo**. 

### <a name="activating-security-policy-changes"></a>Aktivují se změny zásad zabezpečení.

**Postup aktivace změn zásad zabezpečení:**

1. Do vyhledávacího pole zadejte Activate a potom klikněte na odkaz **aktivovat nedokončené změny zásad zabezpečení**.

    ![Aktivací](./media/workday-inbound-tutorial/wd_isu_16.png "Aktivovat")

1. Zahajte úkol aktivovat změny zásad zabezpečení zadáním komentáře pro účely auditování a pak klikněte na tlačítko **OK**.
1. Dokončete úkol na další obrazovce zaškrtnutím políčka **Potvrdit**a potom klikněte na tlačítko **OK**.

    ![Aktivovat nedokončené zabezpečení](./media/workday-inbound-tutorial/wd_isu_18.png "Aktivovat nedokončené zabezpečení")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfigurace zřizování uživatelů z Workday na službu Active Directory

Tato část popisuje kroky pro zřizování uživatelských účtů z Workday do jednotlivých domén služby Active Directory v rámci rozsahu integrace.

* [Přidání aplikace zřizovacího konektoru a stažení agenta zřizování](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalace a konfigurace místních agentů zřizování](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Konfigurace připojení k Workday a službě Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Konfigurace mapování atributů](#part-4-configure-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Část 1: Přidání aplikace konektoru pro zřizování a stažení agenta zřizování

**Konfigurace pracovního dne pro zřizování služby Active Directory:**

1. Přejděte do části <https://portal.azure.com> (Soubor > Nový > Jiné).

2. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.

3. Vyberte **podnikové aplikace**a pak **všechny aplikace**.

4. Vyberte **Přidat aplikaci**a vyberte kategorii **vše** .

5. Vyhledejte ve **službě Active Directory zřizování Workday**a přidejte tuto aplikaci z galerie.

6. Až se aplikace přidá a zobrazí se obrazovka s podrobnostmi aplikace, vyberte **zřizování**.

7. Změňte režim **zřizování** **Mode** na **automaticky**.

8. Pokud chcete stáhnout agenta pro zřizování, klikněte na zobrazený informační banner. 

   ![Stáhnout agenta](./media/workday-inbound-tutorial/pa-download-agent.png "Stáhnout agenta – obrazovka")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Část 2: instalace a konfigurace místních agentů zřizování

Aby bylo možné zřídit místní službu Active Directory, musí být agent zřizování nainstalován na serveru, který má .NET 4.7.1 + Framework a síťový přístup k požadovaným doménám služby Active Directory.

> [!TIP]
> Verzi rozhraní .NET Framework na serveru můžete ověřit podle [zde](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)uvedených pokynů.
> Pokud server nemá nainstalované rozhraní .NET 4.7.1 nebo vyšší, můžete si ho stáhnout [odsud.](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows)  

Přeneste stažený instalační program agenta na hostitele serveru a podle níže uvedených kroků dokončete konfiguraci agenta.

1. Přihlaste se k systému Windows Server, na který chcete nainstalovat nového agenta.

1. Spusťte instalační program agenta zřizování, vyjádřete podmínky a klikněte na tlačítko **nainstalovat** .

   ![Instalační obrazovka](./media/workday-inbound-tutorial/pa_install_screen_1.png "Instalační obrazovka")
   
1. Po dokončení instalace se Průvodce spustí a zobrazí se obrazovka **připojit Azure AD** . Kliknutím na tlačítko **ověřit** se připojte k instanci Azure AD.

   ![Připojení Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Připojení Azure AD")
   
1. Ověřte se u vaší instance Azure AD pomocí přihlašovacích údajů globálního správce.

   ![Ověřování správce](./media/workday-inbound-tutorial/pa_install_screen_3.png "Ověřování správce")

   > [!NOTE]
   > Přihlašovací údaje správce Azure AD se používají jenom pro připojení k vašemu tenantovi Azure AD. Agent neukládá pověření místně na serveru.

1. Po úspěšném ověření pomocí služby Azure AD se zobrazí obrazovka **připojit ke službě Active Directory** . V tomto kroku zadejte název domény služby Active Directory a klikněte na tlačítko **Přidat adresář** .

   ![Přidat adresář](./media/workday-inbound-tutorial/pa_install_screen_4.png "Přidat adresář")
  
1. Nyní budete vyzváni k zadání přihlašovacích údajů požadovaných pro připojení k doméně služby AD. Na stejné obrazovce můžete použít **prioritu vybrat řadič domény** a zadat řadiče domény, které by měl agent použít pro odesílání žádostí o zřízení.

   ![Přihlašovací údaje domény](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Po nakonfigurování domény instalační program zobrazí seznam nakonfigurovaných domén. Na této obrazovce můžete opakovat krok #5 a #6 přidat další domény, kliknutím na tlačítko **Další** pokračujte v registraci agenta.

   ![Nakonfigurované domény](./media/workday-inbound-tutorial/pa_install_screen_6.png "Nakonfigurované domény")

   > [!NOTE]
   > Pokud máte více domén služby AD (například na.contoso.com, emea.contoso.com), přidejte každou doménu do seznamu jednotlivě.
   > Pouze přidání nadřazené domény (např. contoso.com) není dostačující. Musíte zaregistrovat každou podřízenou doménu s agentem.
   
1. Zkontrolujte podrobnosti o konfiguraci a kliknutím na **Potvrdit** agenta zaregistrujte.
  
   ![Potvrdit obrazovku](./media/workday-inbound-tutorial/pa_install_screen_7.png "Potvrdit obrazovku")
   
1. Průvodce konfigurací zobrazí průběh registrace agenta.
  
   ![Registrace agenta](./media/workday-inbound-tutorial/pa_install_screen_8.png "Registrace agenta")
   
1. Po úspěšné registraci agenta můžete kliknutím na tlačítko **ukončit** průvodce ukončit.
  
   ![Ukončit obrazovku](./media/workday-inbound-tutorial/pa_install_screen_9.png "Ukončit obrazovku")
   
1. Ověřte instalaci agenta a ujistěte se, že je spuštěný, a to tak, že otevřete modul snap-in služby a vyhledáte službu s názvem "Microsoft Azure AD připojit zřizování agent".
  
   ![Služby](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Část 3: v aplikaci pro zřizování nakonfigurujte připojení k Workday a službě Active Directory.
V tomto kroku navážeme připojení k Workday a službě Active Directory v Azure Portal. 

1. V Azure Portal se vraťte do pracovního dne do aplikace Active Directory pro zřizování uživatelů vytvořené v [části 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) .

1. Dokončete část **přihlašovací údaje správce** následujícím způsobem:

   * **Workday username** – zadejte uživatelské jméno účtu Integration System v Workday s názvem domény tenanta, který je připojený. Měl by vypadat nějak takto: **uživatelské\@jméno tenant_name**

   * **Heslo pracovního dne –** Zadejte heslo účtu systému pro integraci Workday.

   * **Adresa URL rozhraní API webových služeb Workday –** Zadejte adresu URL koncového bodu webových služeb Workday pro vašeho tenanta. Tato hodnota by měla vypadat takto https://wd3-impl-services1.workday.com/ccx/service/contoso4:, kde *contoso4* nahrazuje správný název tenanta a *WD3-impl* se nahradí správným řetězcem prostředí.

     > [!NOTE]
     > Ve výchozím nastavení aplikace používá službu Workday Web Services (WWS) v 21.1, pokud v adrese URL nejsou zadány žádné informace o verzi. Pokud chcete použít konkrétní verzi rozhraní WWS API, použijte prosím formát adresy URL:https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# <br>
     > Příklad: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v31.0 <br>
     
     > [!NOTE]
     > Pokud používáte rozhraní API WWS v 30.0 a vyšší, než zapnete úlohu zřizování, aktualizujte prosím **výrazy rozhraní API XPath** v části **mapování atributů – > upřesnit možnosti – > upravit seznam atributů pro Workday** , který odkazuje na oddíl [Správa konfigurace](#managing-your-configuration) a [referenčních atributů Workday](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  

   * **Doménová struktura služby Active Directory –** Název domény služby Active Directory, jak je zaregistrován u agenta. Pomocí rozevírací nabídky vyberte cílovou doménu pro zřizování. Tato hodnota je obvykle řetězec jako: *contoso.com*

   * **Kontejner služby Active Directory –** Zadejte rozlišující název kontejneru, kde by měl agent ve výchozím nastavení vytvářet uživatelské účty.
        Příklad: *ou = Standard Users, OU = Users, DC = contoso, DC = test*
        
     > [!NOTE]
     > Toto nastavení se dá přehrát jenom pro vytváření uživatelských účtů, pokud není atribut *parentDistinguishedName* nakonfigurovaný v mapování atributů. Toto nastavení se nepoužívá pro operace vyhledávání a aktualizace uživatelů. Celý podstrom domény spadá do rozsahu operace hledání.

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k chybě.

     > [!NOTE]
     > Služba zřizování Azure AD pošle e-mailové oznámení, pokud úloha zřizování přejde do stavu [karantény](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) .

   * Klikněte na tlačítko **Testovat připojení** . Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** v horní části. Pokud se to nepovede, dvakrát Ověřte platnost přihlašovacích údajů pracovního dne a přihlašovacích údajů služby AD nakonfigurovaných v instalaci agenta.

     ![portál Azure](./media/workday-inbound-tutorial/wd_1.png)

   * Po úspěšném uložení přihlašovacích údajů se v oddílu **mapování** zobrazí výchozí mapování **synchronizace pracovních procesů v místní službě Active Directory** .

### <a name="part-4-configure-attribute-mappings"></a>4. část: Konfigurace mapování atributů

V této části nakonfigurujete způsob, jakým budou data uživatelů z Workday natékat do služby Active Directory.

1. Na kartě zřizování v části **mapování**klikněte na **synchronizace pracovních procesů do místní služby Active Directory**.

1. V poli **obor zdrojového objektu** můžete vybrat, které sady uživatelů v Workday by měly být v oboru pro zřizování AD, definováním sady filtrů založených na atributech. Výchozí obor je "Všichni uživatelé v Workday". Příklady filtrů:

   * Příklad: určení oboru pro uživatele s ID pracovních procesů mezi 1000000 a 2000000 (s výjimkou 2000000)

      * Atribut: WorkerID

      * Operátor: shoda regulárního výrazu

      * Hodnota: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Příklad: pouze zaměstnanci a ne saminí pracovníci

      * Atribut: ČísloZaměstnance

      * Operátor: není NULL

   > [!TIP]
   > Při první konfiguraci zřizovací aplikace budete muset otestovat a ověřit mapování atributů a výrazy, abyste se ujistili, že vám poskytne požadovaný výsledek. Microsoft doporučuje pomocí filtrů oborů v **oboru zdrojového objektu** testovat mapování s několika testovacími uživateli z Workday. Jakmile ověříte, že mapování funguje, můžete buď odebrat filtr, nebo ho postupně rozšířit, aby zahrnoval více uživatelů.

   > [!CAUTION] 
   > Výchozím chováním modulu zřizování je zakázat nebo odstranit uživatele, kteří se přestanou přidělovat z oboru. To nemusí být žádoucí pro integraci vaší pracovní doby do AD. Pokud chcete přepsat toto výchozí chování, přečtěte si článek [přeskočení odstranění uživatelských účtů, které se nacházejí mimo rozsah](../app-provisioning/skip-out-of-scope-deletions.md) .
  
1. V poli **Akce cílového objektu** můžete globálně filtrovat akce prováděné ve službě Active Directory. **Vytváření** a **aktualizace** jsou nejběžnější.

1. V části **mapování atributů** můžete definovat, jak se mají jednotlivé atributy Workday mapovat na atributy služby Active Directory.

1. Kliknutím na existující mapování atributů ho aktualizujte nebo kliknutím na **Přidat nové mapování** v dolní části obrazovky přidejte nová mapování. Jednotlivé mapování atributů podporují tyto vlastnosti:

      * **Typ mapování**

         * **Direct** – zapíše hodnotu atributu Workday do atributu AD bez jakýchkoli změn.

         * **Konstanta** – zapsat statickou konstantní hodnotu řetězce na atribut AD

         * **Výraz** – umožňuje napsat vlastní hodnotu atributu AD na základě jednoho nebo více atributů Workday. [Další informace najdete v tomto článku o výrazech](../app-provisioning/functions-for-customizing-application-data.md).

      * **Zdrojový atribut** – atribut uživatele z pracovního dne. Pokud atribut, který hledáte, není k dispozici, přečtěte si téma [přizpůsobení seznamu atributů uživatele Workday](#customizing-the-list-of-workday-user-attributes).

      * **Výchozí hodnota** – volitelné. Pokud zdrojový atribut má prázdnou hodnotu, mapování místo toho zapíše tuto hodnotu.
            Nejběžnější konfigurací je ponecháno toto prázdné.

      * **Cílový atribut** – atribut uživatele ve službě Active Directory.

      * **Porovnává objekty pomocí tohoto atributu** – bez ohledu na to, jestli se má toto mapování použít k jednoznačné identifikaci uživatelů mezi Workday a službou Active Directory. Tato hodnota je obvykle nastavena v poli ID pracovního procesu pro pracovní den, což je obvykle namapováno na jeden z atributů ID zaměstnance ve službě Active Directory.

      * **Priorita porovnání** – lze nastavit více vyhovujících atributů. Pokud existuje více, vyhodnotí se v pořadí definovaném tímto polem. Jakmile se najde shoda, nevyhodnocují se žádné další odpovídající atributy.

      * **Použít toto mapování**

         * **Vždycky** – použít toto mapování na akce vytvoření a aktualizace uživatele

         * **Pouze během vytváření** – použít toto mapování pouze při akcích vytvoření uživatele

1. Pokud chcete uložit mapování, klikněte na **Uložit** v horní části oddílu mapování atributů.

   ![portál Azure](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Níže jsou uvedeny příklady mapování atributů mezi Workday a službou Active Directory s některými běžnými výrazy.

* Výraz, který je namapován na atribut *parentDistinguishedName* , slouží k zřízení uživatele pro různé organizační jednotky na základě jednoho nebo více atributů zdrojového pracovního dne. Tento příklad uvádí uživatele v různých organizačních jednotkách na základě města, ve kterém jsou.

* Atribut *userPrincipalName* ve službě Active Directory je generován pomocí funkce de-dupliking [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) , která kontroluje existenci vygenerované hodnoty v cílové doméně služby AD a nastavuje ji pouze v případě, že je jedinečná.  

* [Tady najdete dokumentaci k psaní výrazů](../app-provisioning/functions-for-customizing-application-data.md). Tato část obsahuje příklady odebrání speciálních znaků.

| ATRIBUT WORKDAY | ATRIBUT SLUŽBY ACTIVE DIRECTORY |  ID SPÁROVÁNÍ? | VYTVOŘIT NEBO AKTUALIZOVAT |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Ano** | Zapsáno pouze při vytvoření |
| **PreferredNameData**    |  CN    |   |   Zapsáno pouze při vytvoření |
| **SelectUniqueValue (Join ("\@"; Join (".", \[FirstName\], \[LastName\]), "contoso.com"), Join ("\@", Join (".", Mid (\[FirstName\], 1, 1), \[LastName\]), "contoso.com"), Join ("\@", Join ("", Mid\[(FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName (Hlavní název uživatele)     |     | Zapsáno pouze při vytvoření 
| **Replace\[(Mid (Replace\](UserID;;\[\\\\/\\\\\\\\\\\\\[\\"\\(\\\\ :\]\\\\\\ \\\|\\\\=\\\\,\\\\+\\\\\*\\\\? \\\\\\) ",,",,), 1, 20),, "([.) \\ &lt; \\ \\ &gt; \] \*File:///\\ \$.) *$)", , "", , )**      |    sAMAccountName            |     |         Zapsáno pouze při vytvoření |
| **Switch (\[Active\];; "0"; "true"; "1"; "false")** |  accountDisabled      |     | Vytvořit a aktualizovat |
| **FirstName**   | givenName       |     |    Vytvořit a aktualizovat |
| **Polím**   |   sn   |     |  Vytvořit a aktualizovat |
| **PreferredNameData**  |  displayName |     |   Vytvořit a aktualizovat |
| **Společnost**         | company   |     |  Vytvořit a aktualizovat |
| **SupervisoryOrganization**  | Oddělení  |     |  Vytvořit a aktualizovat |
| **ManagerReference**   | manager  |     |  Vytvořit a aktualizovat |
| **BusinessTitle**   |  title     |     |  Vytvořit a aktualizovat | 
| **AddressLineData**    |  streetAddress  |     |   Vytvořit a aktualizovat |
| **Úřad**   |   l   |     | Vytvořit a aktualizovat |
| **CountryReferenceTwoLetter**      |   společném |     |   Vytvořit a aktualizovat |
| **CountryReferenceTwoLetter**    |  c  |     |         Vytvořit a aktualizovat |
| **CountryRegionReference** |  st     |     | Vytvořit a aktualizovat |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Vytvořit a aktualizovat |
| **Ovládacím**  |   Ovládacím  |     | Vytvořit a aktualizovat |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Vytvořit a aktualizovat |
| **Fax**      | facsimileTelephoneNumber     |     |    Vytvořit a aktualizovat |
| **mobilních**  |    mobil       |     |       Vytvořit a aktualizovat |
| **LocalReference** |  preferredLanguage  |     |  Vytvořit a aktualizovat |                                               
| **– Přepínač\[(\]obec, "ou = standardní uživatelé, OU = Uživatelé, OU = výchozí, OU = umístění, DC = contoso, DC = com,," Praha "," ou = Standard Users, OU = Users, OU = Praha, OU = Locations = contoso, DC = com "," Austin "," ou = Standard Users, OU = Users, OU = Austin, OU = umístění, DC = contoso, DC = com "," Seattle "," ou = standardní uživatelé, OU = Uživatelé, OU = Seattle, OU = umístění, DC = contoso, DC = com "," Londýn "," ou = contoso, DC = com ")**  | parentDistinguishedName     |     |  Vytvořit a aktualizovat |

Po dokončení konfigurace mapování atributů teď můžete [Povolit a spustit službu zřizování uživatelů](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Konfiguruje se zřizování uživatelů v Azure AD.

Následující části popisují kroky pro konfiguraci zřizování uživatelů z Workday do Azure AD pro cloudová nasazení.

* [Přidává se aplikace konektoru zřizování Azure AD a vytvoření připojení k Workday.](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurace služby Workday a mapování atributů Azure AD](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Postupujte pouze podle níže uvedeného postupu, pokud máte pouze cloudové uživatele, které je třeba zřídit ve službě Azure AD, nikoli v místní službě Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání aplikace konektoru zřizování Azure AD a vytvoření připojení k Workday

**Konfigurace pracovního dne pro Azure Active Directory zřizování jenom pro cloudové uživatele:**

1. Přejděte do části <https://portal.azure.com> (Soubor > Nový > Jiné).

2. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.

3. Vyberte **podnikové aplikace**a pak **všechny aplikace**.

4. Vyberte **Přidat aplikaci**a pak vyberte kategorii **vše** .

5. Vyhledejte v **Workday zřizování služby Azure AD**a přidejte tuto aplikaci z galerie.

6. Až se aplikace přidá a zobrazí se obrazovka s podrobnostmi aplikace, vyberte **zřizování**.

7. Změňte režim **zřizování** **Mode** na **automaticky**.

8. Dokončete část **přihlašovací údaje správce** následujícím způsobem:

   * **Workday username** – zadejte uživatelské jméno účtu Integration System v Workday s názvem domény tenanta, který je připojený. By měl vypadat nějak takto:username@contoso4

   * **Heslo pracovního dne –** Zadejte heslo účtu systému pro integraci Workday.

   * **Adresa URL rozhraní API webových služeb Workday –** Zadejte adresu URL koncového bodu webových služeb Workday pro vašeho tenanta. Tato hodnota by měla vypadat takto https://wd3-impl-services1.workday.com/ccx/service/contoso4:, kde *contoso4* nahrazuje správný název tenanta a *WD3-impl* se nahradí správným řetězcem prostředí. Pokud tato adresa URL není známá, obraťte se prosím na svého partnera pro integraci pracovního dne nebo zástupce podpory a určete správnou adresu URL, která se má použít.

     > [!NOTE]
     > Ve výchozím nastavení aplikace používá webové služby Workday v 21.1, pokud v adrese URL nejsou zadány žádné informace o verzi. Pokud chcete použít konkrétní verzi rozhraní API webových služeb Workday, použijte prosím formát adresy URL:https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# <br>
     > Příklad: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v31.0


   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k chybě.

   * Klikněte na tlačítko **Testovat připojení** .

   * Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** v horní části. Pokud se to nepovede, poklikejte na to, že adresa URL a přihlašovací údaje pracovního dne jsou platné v Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Část 2: Konfigurace mapování atributů Workday a Azure AD

V této části nakonfigurujete způsob, jakým budou data uživatelů z Workday Azure Active Directory jenom pro uživatele, kteří jsou jenom pro Cloud.

1. Na kartě zřizování v části **mapování**klikněte na **synchronizovat pracovní procesy s Azure AD**.

2. V poli **obor zdrojového objektu** můžete vybrat, které sady uživatelů v Workday by měly být v oboru pro zřizování do Azure AD, a to definováním sady filtrů založených na atributech. Výchozí obor je "Všichni uživatelé v Workday". Příklady filtrů:

   * Příklad: určení oboru pro uživatele s ID pracovních procesů mezi 1000000 a 2000000

      * Atribut: WorkerID

      * Operátor: shoda regulárního výrazu

      * Hodnota: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Příklad: pouze podprocesní pracovníci a ne běžné zaměstnance

      * Atribut: ContingentID

      * Operátor: není NULL

3. V poli **Akce cílového objektu** můžete globálně filtrovat akce prováděné v Azure AD. **Vytváření** a **aktualizace** jsou nejběžnější.

4. V části **mapování atributů** můžete definovat, jak se mají jednotlivé atributy Workday mapovat na atributy služby Active Directory.

5. Kliknutím na existující mapování atributů ho aktualizujte nebo kliknutím na **Přidat nové mapování** v dolní části obrazovky přidejte nová mapování. Jednotlivé mapování atributů podporují tyto vlastnosti:

   * **Typ mapování**

      * **Direct** – zapíše hodnotu atributu Workday do atributu AD bez jakýchkoli změn.

      * **Konstanta** – zapsat statickou konstantní hodnotu řetězce na atribut AD

      * **Výraz** – umožňuje napsat vlastní hodnotu atributu AD na základě jednoho nebo více atributů Workday. [Další informace najdete v tomto článku o výrazech](../app-provisioning/functions-for-customizing-application-data.md).

   * **Zdrojový atribut** – atribut uživatele z pracovního dne. Pokud atribut, který hledáte, není k dispozici, přečtěte si téma [přizpůsobení seznamu atributů uživatele Workday](#customizing-the-list-of-workday-user-attributes).

   * **Výchozí hodnota** – volitelné. Pokud zdrojový atribut má prázdnou hodnotu, mapování místo toho zapíše tuto hodnotu.
            Nejběžnější konfigurací je ponecháno toto prázdné.

   * **Atribut target** – atribut uživatele v Azure AD.

   * **Porovnává objekty pomocí tohoto atributu** – bez ohledu na to, jestli se má tento atribut použít k jednoznačné identifikaci uživatelů mezi Workday a Azure AD. Tato hodnota je obvykle nastavena v poli ID pracovního procesu pro Workday, což je obvykle namapováno na atribut ID zaměstnance (nový) nebo na atribut rozšíření v Azure AD.

   * **Priorita porovnání** – lze nastavit více vyhovujících atributů. Pokud existuje více, vyhodnotí se v pořadí definovaném tímto polem. Jakmile se najde shoda, nevyhodnocují se žádné další odpovídající atributy.

   * **Použít toto mapování**

     * **Vždycky** – použít toto mapování na akce vytvoření a aktualizace uživatele

     * **Pouze během vytváření** – použít toto mapování pouze při akcích vytvoření uživatele

6. Pokud chcete uložit mapování, klikněte na **Uložit** v horní části oddílu mapování atributů.

Po dokončení konfigurace mapování atributů teď můžete [Povolit a spustit službu zřizování uživatelů](#enable-and-launch-user-provisioning).

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Konfigurace zpětného zápisu atributů Azure AD na Workday

Podle těchto pokynů nakonfigurujte zpětný zápis e-mailových adres a uživatelského jména uživatele z Azure Active Directory na Workday.

* [Přidání aplikace konektoru zpětného zápisu a vytvoření připojení k Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Konfigurovat mapování atributů zpětného zápisu](#part-2-configure-writeback-attribute-mappings)
* [Povolení a spuštění zřizování uživatelů](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Část 1: Přidání aplikace konektoru pro zpětný zápis a vytvoření připojení k Workday

**Konfigurace konektoru pro zápis do Workday:**

1. Přejděte do části <https://portal.azure.com> (Soubor > Nový > Jiné).

2. V Azure Portal vyhledejte a vyberte **Azure Active Directory**.

3. Vyberte **podnikové aplikace**a pak **všechny aplikace**.

4. Vyberte **Přidat aplikaci**a pak vyberte kategorii **vše** .

5. Vyhledejte **zpětný zápis do Workday**a přidejte tuto aplikaci z galerie.

6. Až se aplikace přidá a zobrazí se obrazovka s podrobnostmi aplikace, vyberte **zřizování**.

7. Změňte režim **zřizování** **Mode** na **automaticky**.

8. Dokončete část **přihlašovací údaje správce** následujícím způsobem:

   * **Uživatelské jméno správce** – zadejte uživatelské jméno účtu Integration System v Workday s názvem domény tenanta, který je připojený. By měl vypadat nějak takto *:\@username contoso4*

   * **Heslo správce –** Zadejte heslo účtu systému pro integraci Workday.

   * **Adresa URL tenanta –** Zadejte adresu URL koncového bodu webových služeb Workday pro vašeho tenanta. Tato hodnota by měla vypadat takto https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources:, kde *contoso4* nahrazuje správný název tenanta a *WD3-impl* se nahradí správným řetězcem prostředí (v případě potřeby).

   * **E-mail s oznámením –** Zadejte svou e-mailovou adresu a zaškrtněte políčko Odeslat e-mail, pokud dojde k chybě.

   * Klikněte na tlačítko **Testovat připojení** . Pokud je test připojení úspěšný, klikněte na tlačítko **Uložit** v horní části. Pokud se to nepovede, poklikejte na to, že adresa URL a přihlašovací údaje pracovního dne jsou platné v Workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Část 2: Konfigurace mapování atributů zpětného zápisu

V této části nakonfigurujete, jak atributy zpětného zápisu z Azure AD do Workday budou. Konektor v současnosti podporuje zpětný zápis e-mailové adresy a uživatelského jména do Workday.

1. Na kartě zřizování v části **mapování**klikněte na **synchronizovat Azure Active Directory uživatele do pracovního**dne.

2. V poli **obor zdrojového objektu** můžete volitelně filtrovat, které sady uživatelů v Azure Active Directory mají mít své e-mailové adresy, které se vrátí zpět do pracovního dne. Výchozí obor je "Všichni uživatelé v Azure AD".

3. V části **mapování atributů** aktualizujte ID odpovídajícího identifikátoru tak, aby označoval atribut v Azure Active Directory, kde je uloženo ID pracovního procesu Workday nebo ID zaměstnance. Oblíbenou metodou porovnávání je synchronizace ID pracovního procesu Workday nebo ID zaměstnance ve extensionAttribute1-15 ve službě Azure AD a pak použijte tento atribut ve službě Azure AD tak, aby odpovídal uživatelům zpátky v Workday.

4. Obvykle namapujete atribut pro Azure AD *userPrincipalName* na atribut Workday *UserID* a namapujete atribut *mail* služby Azure AD na atribut *EmailAddress* pracovního dne. Pokud chcete uložit mapování, klikněte na **Uložit** v horní části oddílu mapování atributů.

Po dokončení konfigurace mapování atributů teď můžete [Povolit a spustit službu zřizování uživatelů](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Povolení a spuštění zřizování uživatelů

Po dokončení konfigurace aplikace pro zřizování Workday můžete službu zřizování zapnout v Azure Portal.

> [!TIP]
> Ve výchozím nastavení se při zapnutí služby zřizování spustí operace zřizování pro všechny uživatele v oboru. Pokud dojde k chybám při mapování nebo při potížích s daty Workday, úloha zřizování může selhat a přejít do stavu karantény. Aby k tomu nedocházelo, doporučujeme nakonfigurovat filtr **oboru zdrojového objektu** a otestovat mapování atributů s několika testovacími uživateli před spuštěním úplné synchronizace pro všechny uživatele. Jakmile ověříte, že mapování funguje a poskytuje požadované výsledky, můžete buď odebrat filtr, nebo ho postupně rozšířit, aby zahrnoval více uživatelů.

1. Na kartě **zřizování** nastavte **stav zřizování** na **zapnuto**.

2. Klikněte na **Uložit**.

3. Tato operace spustí počáteční synchronizaci, což může trvat proměnlivý počet hodin v závislosti na tom, kolik uživatelů je v tenantovi pracovního dne. 

4. Na kartě **protokoly auditu** v Azure Portal můžete kdykoli zjistit, jaké akce služba zřizování provedla. Protokoly auditu vypíše všechny jednotlivé události synchronizace prováděné službou zřizování, například které uživatele se čtou z pracovního dne a následně se přidají nebo aktualizují ve službě Active Directory. Pokyny, jak zkontrolovat protokoly auditu a opravit chyby zřizování, najdete v části věnované řešení potíží.

5. Po dokončení počáteční synchronizace bude na kartě **zřizování** napsána Sestava souhrnu auditu, jak je znázorněno níže.

   ![portál Azure](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Časté otázky

* **Otázky k schopnostem řešení**
  * [Jak řešení při zpracování nového pronájmu z Workday nastavilo heslo pro nový uživatelský účet ve službě Active Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Podporuje řešení odesílání e-mailových oznámení po dokončení operací zřízení?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Návody spravovat doručování hesel pro nové zaměstnance a bezpečně poskytnout mechanismus pro resetování hesla?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Ukládá se do mezipaměti řešení profily uživatelů Workday v cloudu Azure AD nebo ve vrstvě agenta zřizování?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Je řešení přiřazování místních skupin AD k uživateli?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Která rozhraní API Workday používá řešení k dotazování a aktualizaci profilů pracovních procesů Workday?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Můžu nakonfigurovat svého tenanta Workday HCM se dvěma klienty Azure AD?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Proč se aplikace pro zřizování uživatelů z Workday do Azure AD nepodporuje, pokud jsme nasadili Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Návody navrhovat vylepšení nebo žádat o nové funkce související s integrací Workday a Azure AD?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Otázky k zřizování agentů**
  * [Jaká je verze modulu zřizování agenta pro GA?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Návody znát verzi mého zřizovacího agenta?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Nabízí Microsoft automatické nabízení agentů zřizování?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Můžu agenta zřizování nainstalovat na stejný server, na kterém běží Azure AD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Návody nakonfigurovat agenta zřizování pro použití proxy server pro odchozí komunikaci HTTP?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Návody zajistěte, aby agent zřizování mohl komunikovat s tenant Azure AD, a žádné brány firewall neblokují porty vyžadované agentem?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Návody zrušit registraci domény přidružené k mému agentovi zřizování?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Návody odinstalovat zřizovacího agenta?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Mapování atributů z Workday na AD a na konfigurační otázky**
  * [Návody zálohovat nebo exportovat pracovní kopii mapování a schématu zřizování Workday?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Mám vlastní atributy v Workday i ve službě Active Directory. Návody nakonfigurovat řešení tak, aby fungovalo s vlastními atributy?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Můžu z pracovního dne zřídit fotografii uživatele do služby Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Návody synchronizovat mobilní čísla z Workday na základě souhlasu uživatele s veřejným využitím?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Návody formátování zobrazovaných názvů ve službě AD na základě atributů oddělení/země/města uživatele a zpracování místních odchylek?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Jak můžu použít SelectUniqueValue k vygenerování jedinečných hodnot pro atribut samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Návody odebrat znaky s diakritikou a převést je na normální anglické abecedy?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Otázky k schopnostem řešení

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Jak řešení při zpracování nového pronájmu z Workday nastavilo heslo pro nový uživatelský účet ve službě Active Directory?

Když místní zřizovací agent Získá požadavek na vytvoření nového účtu AD, automaticky vytvoří složité náhodné heslo navržené tak, aby splňovalo požadavky na složitost hesla definované serverem AD, a nastaví tento objekt uživatele. Toto heslo není protokolováno kdekoli.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Podporuje řešení odesílání e-mailových oznámení po dokončení operací zřízení?

Ne, odesílání e-mailových oznámení po dokončení operací zřizování není v aktuální verzi podporováno.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Návody spravovat doručování hesel pro nové zaměstnance a bezpečně poskytnout mechanismus pro resetování hesla?

Jedním z konečných kroků, které jsou součástí nového zřizování účtu AD, je doručování dočasného hesla přiřazeného účtu AD uživatele. Řada podniků stále používá tradiční přístup k dodávání dočasného hesla vedoucímu uživatele, který potom heslo zajímá k novému zaměstnanci nebo podmíněným pracovníkům. Tento proces má chybnou bezpečnostní chybu a existuje možnost, která je k dispozici pro implementaci lepšího přístupu pomocí možností Azure AD.

V rámci procesu náboru týmy HR většinou spouštějí kontrolu na pozadí a VET mobilní číslo nového pronájmu. Díky integraci s zřizováním uživatelů v rámci služby AD můžete sestavit tuto skutečnost a zavést funkci samoobslužného resetování hesla pro uživatele 1. den. To se provádí šířením atributu "mobilní číslo" nového pronájmu z Workday do AD a následným ze služby AD až po Azure AD pomocí Azure AD Connect. Jakmile se v Azure AD zobrazí "mobilní číslo", můžete pro účet uživatele povolit [Samoobslužné resetování hesla (SSPR)](../authentication/howto-sspr-authenticationdata.md) , takže první pronájem může k ověřování použít registrované a ověřené mobilní číslo.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Ukládá se do mezipaměti řešení profily uživatelů Workday v cloudu Azure AD nebo ve vrstvě agenta zřizování?

Ne, řešení neudržuje mezipaměť uživatelských profilů. Služba zřizování Azure AD jednoduše funguje jako datový procesor, čte data z Workday a zapisuje do cílové služby Active Directory nebo Azure AD. Podrobnosti týkající se ochrany osobních údajů uživatelů a uchovávání dat najdete v části [Správa osobních údajů](#managing-personal-data) .

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Je řešení přiřazování místních skupin AD k uživateli?

Tato funkce se momentálně nepodporuje. Doporučený postup je nasadit skript PowerShellu, který se dotáže na koncový bod rozhraní API Microsoft Graph pro [data protokolu auditu](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-beta) a použít ho ke spuštění scénářů, jako je přiřazení skupiny. Tento skript PowerShellu se dá připojit ke Plánovači úloh a nasadit do stejného pole, ve kterém je spuštěný agent zřizování.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Která rozhraní API Workday používá řešení k dotazování a aktualizaci profilů pracovních procesů Workday?

Řešení aktuálně používá následující rozhraní API Workday:

* Formát **adresy URL rozhraní API webových služeb Workday** , který se používá v části **přihlašovací údaje správce** , určuje verzi rozhraní api použitou pro Get_Workers
  * Pokud je formát adresy URL: https://\#\#\#\#\.Workday\.com/CCX/Service/tenant, použije se rozhraní API v 21.1. 
  * Pokud je formát adresy URL: https://\#\#\#\#\.Workday\.com/CCX/Service/tenant/lidské\_zdroje, použije se rozhraní API v 21.1. 
  * Pokud je formát adresy URL: https://\#\#\#\#\.Workday\.com/CCX/Service\_/tenant/lidské zdroje/v\# \# \. \# , použije se zadaná verze rozhraní API. (Příklad: Pokud je zadáno v 34.0, pak se používá.)  
   
* Funkce zpětného zápisu e-mailu Workday používá Change_Work_Contact_Information (v 30.0) 
* Funkce zpětného zápisu uživatelského jména v Workday používá Update_Workday_Account (v 31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Můžu nakonfigurovat svého tenanta Workday HCM se dvěma klienty Azure AD?

Ano, tato konfigurace je podporována. Tady je postup vysoké úrovně pro konfiguraci tohoto scénáře:

* Nasaďte zřizovacího agenta #1 a zaregistrujte ho pomocí #1 tenanta Azure AD.
* Nasaďte zřizovacího agenta #2 a zaregistrujte ho pomocí #2 tenanta Azure AD.
* V závislosti na podřízených doménách, které má každý Agent zřizování spravovat, nakonfigurujte každého agenta s těmito doménami. Jeden agent může zpracovávat více domén.
* V Azure Portal nastavte pracovní den do aplikace AD zřizování aplikací v každém tenantovi a nakonfigurujte ji s příslušnými doménami.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Proč se aplikace pro zřizování uživatelů z Workday do Azure AD nepodporuje, pokud jsme nasadili Azure AD Connect?

Pokud se Azure AD používá v hybridním režimu (kde obsahuje kombinaci cloudu a místních uživatelů), je důležité mít jasně definici "zdroje autority". Obvykle hybridní scénáře vyžadují nasazení Azure AD Connect. Při nasazení Azure AD Connect je místní služba AD zdrojem autority. Zavedení pracovního dne do služby Azure AD Connector do kombinace může vést k situaci, kdy hodnoty atributu Workday mohou přepsat hodnoty nastavené Azure AD Connect. Pokud je povolená Azure AD Connect, použití zřizování "Workday do Azure AD" se proto nepodporuje. V takových situacích doporučujeme pro získání uživatelů do místní služby AD použít aplikaci pro zřizování "Workday to AD User" a pak je synchronizovat do Azure AD pomocí Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Návody navrhovat vylepšení nebo žádat o nové funkce související s integrací Workday a Azure AD?

Vaše zpětná vazba je vysoce ohodnocená, protože nám pomáhá nastavit směr budoucích verzí a vylepšení. Vítejte na všech zpětných vazbách a pomůžeme vám poslat návrh nápadu nebo vylepšení na [fóru o zpětné vazbě služby Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory). Pokud chcete najít konkrétní zpětnou vazbu související s integrací k Workday, vyberte kategorii *aplikace SaaS* a vyhledejte existující zpětnou vazbu související s Workday *pomocí klíčových slov* .

![Aplikace UserVoice SaaS](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![UserVoice – Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Při návrhu nové myšlenky prosím zkontrolujte, jestli už někdo jiný navrhl podobnou funkci. V takovém případě můžete hlasovat o funkci nebo žádosti o vylepšení. Komentář ke konkrétnímu případu použití si můžete také nechat zobrazit tak, aby se zobrazila vaše podpora pro svůj nápad, a Představte si, jak vám bude tato funkce pro vás užitečná.

### <a name="provisioning-agent-questions"></a>Otázky k zřizování agentů

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Jaká je verze modulu zřizování agenta pro GA?

* Verze tohoto agenta zřizování je 1.1.30 a vyšší.
* Pokud je verze vašeho agenta menší než 1.1.30, používáte verzi Public Preview a automaticky se aktualizuje na verzi GA, pokud má server, který hostuje agenta, modul runtime .NET 4.7.1.
  * Můžete [kontrolovat verzi rozhraní .NET](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) nainstalovanou na serveru. Pokud na serveru neběží .NET 4.7.1, můžete [si stáhnout a nainstalovat .NET 4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). Po instalaci .NET 4.7.1 se Váš agent zřizování automaticky aktualizuje na verzi GA.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Návody znát verzi mého zřizovacího agenta?

* Přihlaste se k systému Windows Server, kde je nainstalován agent zřizování.
* Přejít na **ovládací panel** -> **Odinstalovat nebo změnit nabídku programu**
* Vyhledejte verzi odpovídající položce **Microsoft Azure AD připojení zřizování agent** .

  ![portál Azure](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Nabízí Microsoft automatické nabízení agentů zřizování?

Ano, Microsoft automaticky aktualizuje agenta zřizování. Automatické aktualizace můžete zakázat tak, že zastavíte službu Windows Service **Microsoft Azure AD Connect agent**Update.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Můžu agenta zřizování nainstalovat na stejný server, na kterém běží Azure AD Connect?

Ano, agent zřizování můžete nainstalovat na stejný server, na kterém běží Azure AD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>V okamžiku konfigurace se agent zřizování vyzve k zadání přihlašovacích údajů správce Azure AD. Ukládá agent místně přihlašovací údaje na serveru?

Během konfigurace se agent zřizování vyzve k zadání přihlašovacích údajů správce Azure AD jenom pro připojení k vašemu tenantovi Azure AD. Přihlašovací údaje nejsou místně uloženy na serveru. Ale uchová přihlašovací údaje, které se používají pro připojení k místní *doméně služby Active Directory* v úložišti hesel místního systému Windows.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Návody nakonfigurovat agenta zřizování pro použití proxy server pro odchozí komunikaci HTTP?

Agent zřizování podporuje použití odchozího proxy serveru. Můžete ji nakonfigurovat úpravou konfiguračního souboru agenta **C:\Program Files\Microsoft Azure AD Connect zřizování Agent\AADConnectProvisioningAgent.exe.config**. Do sebe přidejte následující řádky, a to na konec souboru těsně před uzavírací `</configuration>` značku.
Nahraďte proměnné [proxy-server] a [proxy-port] svým názvem proxy server a hodnotami portů.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Návody zajistěte, aby agent zřizování mohl komunikovat s tenant Azure AD, a žádné brány firewall neblokují porty vyžadované agentem?

Můžete taky zkontrolovat, jestli máte všechny požadované porty otevřené, a to tak, že otevřete [Nástroj pro testování portů konektoru](https://aadap-portcheck.connectorporttest.msappproxy.net/) z místní sítě. Více zelených značek znamená větší odolnost.

Abyste se ujistili, že vám nástroj poskytne správné výsledky, nezapomeňte:

* Otevřete nástroj v prohlížeči ze serveru, na který jste nainstalovali agenta zřizování.
* Zajistěte, aby se na této stránce používaly taky všechny proxy servery nebo brány firewall, které platí pro vašeho agenta zřizování. To lze provést v aplikaci Internet Explorer tak, že na **nastavení > možnosti Internetu – > připojení – > nastavení sítě LAN**. Na této stránce se zobrazí pole "použít proxy server pro vaši síť LAN". Zaškrtněte toto políčko a adresu proxy umístěte do pole adresa.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Je možné nakonfigurovat jednoho agenta zřizování pro zřizování více domén služby AD?

Ano, jeden zřizovací Agent se dá nakonfigurovat tak, aby zpracovával víc domén služby AD, pokud má agent na příslušných řadičích domény dohled. Microsoft doporučuje zřídit skupinu 3 zřizovacích agentů, které zajišťují stejnou sadu domén AD, aby zajistili vysokou dostupnost a poskytovaly podporu při převzetí služeb při selhání.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Návody zrušit registraci domény přidružené k mému agentovi zřizování?

* Z Azure Portal Získejte *ID tenanta* vašeho TENANTA Azure AD.
* Přihlaste se k Windows serveru, na kterém je spuštěný agent zřizování.
* Otevřete PowerShell jako správce systému Windows.
* Přejděte do adresáře obsahujícího registrační skripty a spusťte následující příkazy, které nahradí \[parametr ID\] tenanta hodnotou ID vašeho tenanta.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Ze seznamu zobrazených agentů – Zkopírujte hodnotu `id` pole z tohoto prostředku, jehož název domény se *resourceName* rovná názvu domény služby AD.
* Vložte hodnotu ID do tohoto příkazu a spusťte příkaz v PowerShellu.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Spusťte Průvodce konfigurací agenta znovu.
* Všechny ostatní agenty, které byly dříve přiřazeny k této doméně, bude nutné překonfigurovat.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Návody odinstalovat zřizovacího agenta?

* Přihlaste se k systému Windows Server, kde je nainstalován agent zřizování.
* Přejít na **ovládací panel** -> **Odinstalovat nebo změnit nabídku programu**
* Odinstalujte následující programy:
  * Agent zřizování pro Microsoft Azure AD připojení
  * Aktualizace agenta Microsoft Azure AD Connect
  * Balíček zřizovacího agenta Microsoft Azure AD Connect

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Mapování atributů z Workday na AD a na konfigurační otázky

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Návody zálohovat nebo exportovat pracovní kopii mapování a schématu zřizování Workday?

K exportu konfigurace zřizování uživatelů v Workday můžete použít rozhraní Microsoft Graph API. Podrobnosti najdete v části věnované [exportu a importování konfigurace mapování atributů zřizování uživatelů v Workday](#exporting-and-importing-your-configuration) .

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Mám vlastní atributy v Workday i ve službě Active Directory. Návody nakonfigurovat řešení tak, aby fungovalo s vlastními atributy?

Řešení podporuje vlastní atributy pracovního dne a služby Active Directory. Chcete-li přidat vlastní atributy do schématu mapování, otevřete okno **mapování atributů** a posuňte se dolů a rozbalte oddíl **Zobrazit upřesňující možnosti**. 

![Upravit seznam atributů](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Chcete-li přidat vlastní atributy pracovního dne, vyberte možnost *Upravit seznam atributů pro pracovní den* a přidejte vlastní atributy služby AD. Vyberte možnost *Upravit seznam atributů pro místní službu Active Directory*.

Viz také:

* [Přizpůsobení seznamu atributů uživatele Workday](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Návody nakonfigurovat řešení tak, aby se aktualizovaly jenom atributy ve službě AD na základě změn Workday a nevytvořily žádné nové účty AD?

Tuto konfiguraci je možné dosáhnout nastavením **akcí cílového objektu** v okně **mapování atributů** , jak je znázorněno níže:

![Aktualizovat akci](./media/workday-inbound-tutorial/wd_target_update_only.png)

Zaškrtnutím políčka Update (aktualizovat) proveďte pouze operace aktualizace pro tok z Workday do AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Můžu z pracovního dne zřídit fotografii uživatele do služby Active Directory?

Řešení v současné době nepodporuje nastavení binárních atributů, jako je *thumbnailPhoto* a *JpegPhoto* ve službě Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Návody synchronizovat mobilní čísla z Workday na základě souhlasu uživatele s veřejným využitím?

* Projděte si okno zřizování vaší aplikace pro zřizování Workday.
* Klikněte na mapování atributů. 
* V části **mapování**vyberte možnost **synchronizovat pracovní procesy Workday do místní služby Active Directory** (nebo **synchronizovat pracovní procesy**v rámci služby Azure AD).
* Na stránce mapování atributů přejděte dolů a zaškrtněte políčko Zobrazit rozšířené možnosti.  Klikněte na **Upravit seznam atributů pro Workday** .
* V okně, které se otevře, vyhledejte atribut Mobile a klikněte na řádek, abyste mohli upravit ![ **výraz rozhraní API** Mobile GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Nahraďte **výraz rozhraní API** následujícím novým výrazem, který načte mobilní číslo pracovníka pouze v případě, že je v Workday nastaven příznak "veřejné použití" na hodnotu "true".

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Uložte seznam atributů.
* Uložte mapování atributů.
* Vymaže aktuální stav a restartuje úplnou synchronizaci.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Návody formátování zobrazovaných názvů ve službě AD na základě atributů oddělení/země/města uživatele a zpracování místních odchylek?

Je to běžný požadavek na konfiguraci atributu *DisplayName* ve službě AD, aby poskytoval také informace o oddělení uživatele a zemi nebo oblasti. Například pokud Jan Novák funguje v marketingovém oddělení v USA, můžete chtít, aby se jeho *Zobrazovaný* název zobrazoval jako *Novák, Jan (marketing-US)*.

Tady je postup, jak můžete zvládnout tyto požadavky pro vytváření *CN* nebo *DisplayName* k zahrnutí atributů, jako je například společnost, obchodní jednotka, město nebo země/oblast.

* Každý atribut Workday se načte pomocí podkladového výrazu XPATH API, který se dá nakonfigurovat v **mapování atributů – > Upřesnit oddíl > upravit seznam atributů pro Workday**. Tady je výchozí výraz rozhraní API XPATH pro atributy Workday *PreferredFirstName*, *PreferredLastName*, *Company* a *SupervisoryOrganization* .

     | Atribut Workday | Výraz XPATH rozhraní API |
     | ----------------- | -------------------- |
     | PreferredFirstName | nepracovní: Work/FORMED: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: First_Name/text () |
     | PreferredLastName | nepracovní: Work/FORMED: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: Last_Name/text () |
     | Společnost | /WD: Work/FORMED: Worker_Data: Organization_Data/WD: Worker_Organization_Data Organization_Data [/WD: Organization_Type_Reference/WD: ID [@wd:type= ' Organization_Type_ID '] = ' Company ']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | /WD: Work/FORMED: Worker_Data: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [\: Organization_Type_Reference/WD:@wd:typeID [= ' Organization_Type_ID '] = ' dohledu ']/wd: Organization_Name/text () |
  
   Potvrďte u svého pracovního týmu, že výše uvedený výraz rozhraní API je platný pro vaši konfiguraci tenanta Workday. V případě potřeby je můžete upravit, jak je popsáno v části [přizpůsobení seznamu atributů uživatele Workday](#customizing-the-list-of-workday-user-attributes).

* Podobně informace o zemi přítomné v Workday se načítají pomocí následujícího výrazu XPATH:/WD: *Work//WD: Worker_Data: Employment_Data: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference*

     V oddílu seznam atributů Workday jsou k dispozici 5 atributů souvisejících se zeměmi.

     | Atribut Workday | Výraz XPATH rozhraní API |
     | ----------------- | -------------------- |
     | CountryReference | /WD: Work/průchozí: Worker_Data: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [@wd:type= ' ISO_3166-1_Alpha-3_Code ']/text () |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | /WD: Work/průchozí: Worker_Data: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [@wd:type= ' ISO_3166-1_Numeric-3_Code ']/text () |
     | CountryReferenceTwoLetter | /WD: Work/průchozí: Worker_Data: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [@wd:type= ' ISO_3166-1_Alpha-2_Code ']/text () |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Potvrďte u svého pracovního týmu, že výše uvedené výrazy rozhraní API jsou platné pro vaši konfiguraci tenanta Workday. V případě potřeby je můžete upravit, jak je popsáno v části [přizpůsobení seznamu atributů uživatele Workday](#customizing-the-list-of-workday-user-attributes).

* Chcete-li vytvořit správný výraz mapování atributů, určete, který atribut Workday "autoritativně" představuje jméno uživatele, příjmení, zemi/oblast a oddělení. Řekněme, že atributy jsou *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* a *SupervisoryOrganization* . Pomocí tohoto postupu můžete vytvořit výraz pro atribut AD *DisplayName* následujícím způsobem a získat tak zobrazované jméno, jako je například *Smith, Jan (marketing-US)*.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Jakmile budete mít správný výraz, upravte tabulku mapování atributů a upravte mapování atributu *DisplayName* , jak je znázorněno níže: ![mapování DisplayName](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Po rozšíření výše uvedeného příkladu řekněme, že chcete převést názvy měst z Workday na zkratky hodnot a pak je použít k sestavení zobrazovaných názvů, jako je například *Smith, Jan (chí)* nebo *Chvojková, Jana (NYC)*, pak tento výsledek lze dosáhnout pomocí výrazu Switch *s atributem* pracovního dne v podobě proměnné determinant.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Viz také:
  * [Switch – syntaxe funkce](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Join – syntaxe funkce](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Připojit syntaxi funkce](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Jak můžu použít SelectUniqueValue k vygenerování jedinečných hodnot pro atribut samAccountName?

Řekněme, že chcete generovat jedinečné hodnoty pro atribut *sAMAccountName* pomocí kombinace atributů *FirstName* a *LastName* z pracovního dne. Níže je uveden výraz, který můžete začít s:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Jak výše uvedený výraz funguje: Pokud je uživatel Jan Novák, pokusí se nejprve vygenerovat JSmith, pokud JSmith už existuje, a vygeneruje JoSmith, pokud existuje, generuje JohSmith. Výraz také zajišťuje, že generovaná hodnota splňuje omezení délky a speciální omezení znaků přidružené k *účtu sAMAccountName*.

Viz také:

* [Střední syntaxe funkce](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Nahradit syntaxi funkce](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [Syntaxe funkce SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Návody odebrat znaky s diakritikou a převést je na normální anglické abecedy?

Použijte funkci [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) k odebrání speciálních znaků v křestním jménu a příjmení uživatele při vytváření e-mailové adresy nebo hodnoty CN pro uživatele.

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

V této části najdete konkrétní pokyny, jak řešit problémy s zřizováním pomocí integrace Workday pomocí protokolů auditu Azure AD a protokolů Prohlížeč událostí Windows serveru. Sestavuje se na základě obecných kroků řešení potíží a konceptů zaznamenaných v tomto [kurzu: vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md)

Tato část se zabývá následujícími aspekty řešení potíží:

* [Nastavení Prohlížeč událostí Windows pro řešení potíží s agentem](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Nastavení protokolů auditu Azure Portal pro řešení potíží se službou](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Porozumění protokolům pro operace vytvoření účtu uživatele služby AD](#understanding-logs-for-ad-user-account-create-operations)
* [Principy protokolů pro operace aktualizace pro správce](#understanding-logs-for-manager-update-operations)
* [Řešení běžně zjištěných chyb](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Nastavení Prohlížeč událostí Windows pro řešení potíží s agentem

* Přihlaste se k počítači se systémem Windows Server, kde je nasazen agent zřizování.
* Otevřete **Windows Server Prohlížeč událostí** desktopovou aplikaci.
* Vyberte **protokoly Windows > aplikaci**.
* Použít **Filtr aktuální protokol...** možnost zobrazení všech událostí protokolovaných ve zdrojovém **AAD. Připojte. ProvisioningAgent** a vylučte události s ID události "5" zadáním filtru "-5", jak je znázorněno níže.

  ![Prohlížeč událostí Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Klikněte na tlačítko **OK** a seřaďte zobrazení výsledků podle sloupce **data a čas** .

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Nastavení protokolů auditu Azure Portal pro řešení potíží se službou

* Spusťte [Azure Portal](https://portal.azure.com)a přejděte do části **protokoly auditu** vaší aplikace pro zřizování Workday.
* Pomocí tlačítka **sloupce** na stránce protokoly auditu zobrazíte v zobrazení pouze následující sloupce (datum, aktivita, stav, důvod stavu). Tato konfigurace zajišťuje, že se zaměříte jenom na data, která jsou relevantní pro řešení potíží.

  ![Sloupce protokolu auditu](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* K filtrování zobrazení použijte parametry dotazu **target** a **Date Range** . 
  * Nastavte parametr pro **cílový** dotaz na ID pracovního procesu nebo ID zaměstnance objektu pracovního procesu Workday.
  * Nastavte **Rozsah kalendářních dat** na příslušné časové období, ve kterém chcete prozkoumat chyby nebo problémy se zřizováním.

  ![Filtry protokolu auditu](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Porozumění protokolům pro operace vytvoření účtu uživatele služby AD

Když se zjistí nové přijetí v Workday (řekněme, že s ID *21023*) se služba zřizování Azure AD pokusí vytvořit nový uživatelský účet služby AD pro pracovní proces a v procesu vytvoří 4 záznamy protokolu auditu, jak je popsáno níže:

  [![Operace vytvoření protokolu auditu](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Když kliknete na některý ze záznamů protokolu auditu, otevře se stránka **Podrobnosti o aktivitě** . Zde je zobrazená stránka s **podrobnostmi o aktivitách** pro každý typ záznamu protokolu.

* Záznam **importu Workday** : Tento záznam protokolu zobrazuje informace o pracovních procesech načtených z pracovního dne. Informace v části *Další podrobnosti* v záznamu protokolu použijte k řešení potíží s načtením dat z Workday. Ukázkový záznam je zobrazen níže spolu s ukazateli, jak interpretovat jednotlivá pole.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* Záznam **importu AD** : Tento záznam protokolu zobrazuje informace o účtu načtené ze služby AD. Stejně jako při počátečním vytváření uživatele není k dispozici žádný účet služby AD, *Důvod stavu aktivity* bude znamenat, že ve službě Active Directory nebyl nalezen žádný účet s odpovídající hodnotou atributu ID. Informace v části *Další podrobnosti* v záznamu protokolu použijte k řešení potíží s načtením dat z Workday. Ukázkový záznam je zobrazen níže spolu s ukazateli, jak interpretovat jednotlivá pole.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Chcete-li zjistit záznamy protokolu zřizovacího agenta, které odpovídají této operaci importu služby AD, otevřete protokoly Windows Prohlížeč událostí a použijte příkaz **Najít...** možnost nabídky k vyhledání položek protokolu, které obsahují ID a spojovací hodnotu atributu vlastnosti (v tomto případě *21023*).

  ![Vyhledávání](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Vyhledejte položku s *ID události = 9*, které vám poskytne filtr hledání LDAP používaný agentem k načtení účtu AD. Můžete ověřit, zda se jedná o správný vyhledávací filtr pro načtení jedinečných uživatelských záznamů.

  ![Hledání LDAP](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  Záznam, který hned za ním následuje s *ID události = 2* , zachycuje výsledek operace hledání a vrátí výsledky.

  ![Výsledky LDAP](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* Záznam **Akce synchronizačního pravidla** : Tento záznam protokolu zobrazuje výsledky pravidel mapování atributů a nakonfigurované filtry oborů spolu s akcí zřizování, která bude provedena pro zpracování příchozí události Workday. Pomocí informací v části *Další podrobnosti* v záznamu protokolu můžete řešit problémy s akcí synchronizace. Ukázkový záznam je zobrazen níže spolu s ukazateli, jak interpretovat jednotlivá pole.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Pokud se ve výrazech mapování atributů vyskytnou problémy nebo jsou k dispozici problémy s příchozími daty pracovního dne (například: prázdná nebo hodnota null pro požadované atributy), pak se v této fázi zobrazí chyba s informacemi o chybě v této fázi s kódem chyby.

* Záznam **exportu AD** : Tento záznam protokolu zobrazuje výsledek operace vytvoření účtu služby AD společně s hodnotami atributů, které byly nastaveny v procesu. Pomocí informací v části *Další podrobnosti* v záznamu protokolu můžete řešit problémy s operací vytvoření účtu. Ukázkový záznam je zobrazen níže spolu s ukazateli, jak interpretovat jednotlivá pole. V části Další podrobnosti je "EventName" nastaven na "EntryExportAdd", "JoiningProperty" je nastavena na hodnotu atributu odpovídajícího ID, je "SourceAnchor" nastaven na WorkdayID (WID) přidružený k záznamu a "TargetAnchor" je nastavena na hodnotu atributu AD "ObjectGuid" nově vytvořeného uživatele. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Chcete-li najít záznamy protokolu zřizovacího agenta, které odpovídají této operaci exportu služby AD, otevřete protokoly Windows Prohlížeč událostí a použijte příkaz **Najít...** možnost nabídky k vyhledání položek protokolu, které obsahují ID a spojovací hodnotu atributu vlastnosti (v tomto případě *21023*).  

  Vyhledejte záznam HTTP POST odpovídající časovému razítku operace exportu s *ID události = 2*. Tento záznam bude obsahovat hodnoty atributů odesílané zřizovací službou agentovi zřizování.

  [![SCIM přidat](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Hned po výše uvedené události by měla existovat jiná událost, která zachycuje odpověď na operaci vytvoření účtu služby AD. Tato událost vrátí nový identifikátor objectGuid vytvořený ve službě AD a ve službě zřizování se nastaví jako atribut TargetAnchor.

  [![SCIM přidat](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Principy protokolů pro operace aktualizace pro správce

Atribut Manager je atributem reference ve službě AD. Služba zřizování nenastavuje atribut Manager jako součást operace vytvoření uživatele. Místo toho, když se pro uživatele vytvoří účet služby AD, se atribut správce nastaví jako součást operace *aktualizace* . Rozšiřování výše uvedeného příkladu řekněme, že se v Workday aktivuje nové zařazení s ID zaměstnance 21451 a správce nového zařazení (*21023*) už má účet AD. V tomto scénáři se při hledání v protokolech auditu pro uživatele 21451 zobrazí pět položek.

  [![Aktualizace správce](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Prvních 4 záznamy se podobají těm, které jsme prozkoumali v rámci operace vytvoření uživatele. 5. záznam je export přidružený k aktualizaci atributu správce. Záznam protokolu zobrazí výsledek operace aktualizace správce účtů služby AD, která se provádí pomocí atributu *objectGUID* správce.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Řešení běžně zjištěných chyb

Tato část se zabývá často zaznamenanými chybami při zřizování uživatelů Workday a způsobu jejich řešení. Chyby jsou seskupené následujícím způsobem:

* [Chyby agenta zřizování](#provisioning-agent-errors)
* [Chyby připojení](#connectivity-errors)
* [Chyby při vytváření uživatelských účtů služby AD](#ad-user-account-creation-errors)
* [Chyby aktualizace účtu uživatele služby AD](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Chyby agenta zřizování

|#|Chybový scénář |Pravděpodobné příčiny|Doporučené řešení|
|--|---|---|---|
|1.| Při instalaci agenta zřizování došlo k chybě. chybová zpráva: *spuštění služby Microsoft Azure AD Connect zřizování Agent (AADConnectProvisioningAgent) se nezdařilo. Ověřte, zda máte dostatečná oprávnění ke spuštění systému.* | Tato chyba se obvykle zobrazuje, pokud se pokoušíte nainstalovat agenta zřizování na řadič domény a zásady skupiny zabraňují spuštění služby.  Zobrazuje se také v případě, že máte spuštěnou předchozí verzi agenta a před zahájením nové instalace jste ho nenainstalovali.| Nainstalujte agenta zřizování na server, který není řadičem domény. Před instalací nového agenta zajistěte, aby byly předchozí verze agenta odinstalovány.|
|2.| Služba Windows Microsoft Azure AD Connect zřizuje Agent je ve *výchozím* stavu a nepřepne na *běžící* stav. | V rámci instalace vytvoří průvodce agentem na serveru místní účet (**NT Service\\AADConnectProvisioningAgent**) a jedná se o účet pro přihlášení, který se používá ke spuštění služby. Pokud zásady zabezpečení na Windows serveru zabrání místním účtům ve spouštění služeb, dojde k této chybě. | Otevřete *konzolu služby*. Klikněte pravým tlačítkem na službu Windows Microsoft Azure AD připojit zřizovací agent a na kartě přihlášení zadejte účet správce domény, ve kterém chcete službu spustit. Restartujte službu. |
|3.| Při konfiguraci zřizovacího agenta s doménou služby AD v kroku *připojení služby Active Directory*trvá Průvodce dlouhou dobu pokusu o načtení schématu AD a nakonec vyprší časový limit. | K této chybě obvykle dojde v případě, že se průvodce kvůli problémům s bránou firewall nemůže spojit se serverem řadiče domény AD. | Při zadání přihlašovacích údajů k doméně služby Active Directory na obrazovce průvodce *připojením služby Active Directory* existuje možnost s názvem *Vybrat prioritu řadiče domény*. Tuto možnost použijte, pokud chcete vybrat řadič domény, který je ve stejné lokalitě jako server agenta, a zajistit, aby komunikace neblokovala žádná pravidla brány firewall. |

#### <a name="connectivity-errors"></a>Chyby připojení

Pokud se služba zřizování nemůže připojit k Workday nebo službě Active Directory, může to způsobit, že zřizování přejde do stavu v karanténě. Pomocí následující tabulky můžete řešit problémy s připojením.

|#|Chybový scénář |Pravděpodobné příčiny|Doporučené řešení|
|--|---|---|---|
|1.| Když kliknete na **Test připojení**, zobrazí se chybová zpráva: došlo k *chybě při připojování ke službě Active Directory. Ujistěte se prosím, že místní zřizovací Agent běží a že je nakonfigurovaný se správnou doménou služby Active Directory.* | Tato chyba se obvykle zobrazuje, pokud Agent zřizování není spuštěný nebo že brána firewall blokuje komunikaci mezi službou Azure AD a agentem zřizování. Tato chyba se může zobrazit i v případě, že doména není nakonfigurovaná v průvodci agentem. | Otevřete konzolu *služby* na Windows serveru a potvrďte, že je agent spuštěný. Otevřete Průvodce zřizovacím agentem a potvrďte, že je správná doména zaregistrovaná u agenta.  |
|2.| Úloha zřizování přejde do karantény po víkendech (Pá-SAT) a pošle vám e-mailové oznámení, že při synchronizaci dojde k chybě. | Jednou z běžných příčin této chyby je plánovaná údržba aplikace Workday. Pokud používáte tenanta implementace Workday, mějte na paměti, že Workday má naplánované výpadky tenantů implementace na víkendy (obvykle od pátečního večera do sobotního rána) a během této doby můžou aplikace zřizování Workday přejít do stavu karantény, protože se k Workday nemůžou připojit. Jakmile bude tenant implementace Workday opět online, vrátí se zpět do normálního stavu. Ve výjimečných případech k této chybě může dojít také v případě, že se kvůli aktualizaci tenanta změní heslo uživatele systému integrace nebo pokud dojde k uzamčení nebo vypršení platnosti účtu. | Zjistěte od svého správce Workday nebo partnera pro integraci období plánovaných výpadků Workday, abyste během výpadků mohli ignorovat upozornění a zkontrolovat dostupnost, jakmile bude instance Workday opět online.  |


#### <a name="ad-user-account-creation-errors"></a>Chyby při vytváření uživatelských účtů služby AD

|#|Chybový scénář |Pravděpodobné příčiny|Doporučené řešení|
|--|---|---|---|
|1.| V protokolu auditu došlo k chybě operace exportu s *chybou zprávy: OperationsError-SvcErr: došlo k chybě operace. Pro adresářovou službu nebyl nakonfigurován žádný nadřízený odkaz. Adresářová služba proto nemůže vydat odkazy na objekty mimo tuto doménovou strukturu.* | Tato chyba se obvykle zobrazuje, pokud není správně nastavena adresářová jednotka *služby Active Directory* nebo pokud dochází k problémům s mapováním výrazu použitým pro *parentDistinguishedName*. | Pro překlepy ověřte parametr organizační jednotky *kontejneru služby Active Directory* . Pokud v mapování atributů používáte *parentDistinguishedName*, ujistěte se, že se vždy vyhodnotí jako známý kontejner v rámci domény AD. Chcete-li zobrazit generovanou hodnotu, zkontrolujte událost *exportu* v protokolech auditu. |
|2.| Selhání operace exportu v protokolu auditu s kódem chyby: *SystemForCrossDomainIdentityManagementBadResponse* a chyba zprávy *: ConstraintViolation-atrerr: hodnota v požadavku je neplatná. Hodnota pro atribut nebyla v přijatelném rozsahu hodnot. \nError podrobnosti: CONSTRAINT_ATT_TYPE – společnost*. | I když je tato chyba specifická pro atribut *Company* , může se tato chyba zobrazit i u jiných atributů, jako je například *CN* . Tato chyba se zobrazí kvůli omezení schématu AD vynucované. Ve výchozím nastavení mají atributy jako *Společnost* a *CN* ve službě AD horní limit 64 znaků. Pokud hodnota přicházející z Workday je delší než 64 znaků, zobrazí se tato chybová zpráva. | Zkontrolujte událost *exportu* v protokolech auditu a podívejte se na hodnotu atributu hlášené v chybové zprávě. Zvažte zkrácení hodnoty přicházející z Workday pomocí funkce [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) nebo změna mapování na atribut AD, který nemá podobná omezení délky.  |

#### <a name="ad-user-account-update-errors"></a>Chyby aktualizace účtu uživatele služby AD

Během procesu aktualizace uživatelského účtu AD služba zřizování načte informace z Workday i AD, spustí pravidla mapování atributů a určí, jestli se musí projevit jakákoli změna. Tím se aktivuje událost aktualizace. Pokud některý z těchto kroků narazí na chybu, zaprotokoluje se v protokolech auditu. Pomocí následující tabulky můžete řešit běžné chyby aktualizace.

|#|Chybový scénář |Pravděpodobné příčiny|Doporučené řešení|
|--|---|---|---|
|1.| Selhání akce synchronizačního pravidla v protokolu auditu se zprávou *EventName = EntrySynchronizationError a ErrorCode = EndpointUnavailable*. | Tato chyba se zobrazí, pokud služba zřizování nemůže načíst data profilu uživatele ze služby Active Directory kvůli chybě zpracování, kterou zjistil místní zřizovací agent. | Ověřte, že agent zřizování Prohlížeč událostí protokoly pro události chyb, které indikují problémy s operací čtení (Filter by ID události #2). |
|2.| Atribut správce ve službě AD se pro určité uživatele ve službě AD neaktualizuje. | Nejpravděpodobnější příčinou této chyby je, pokud používáte pravidla vytváření oboru a správce uživatele není součástí oboru. K tomuto problému může dojít také v případě, že atribut ID odpovídajícího identifikátoru (např. EmployeeID) nebyl nalezen v cílové doméně služby AD nebo není nastaven na správnou hodnotu. | Zkontrolujte filtr oboru a přidejte uživatele správce do rozsahu. Zkontrolujte profil manažera ve službě AD a ujistěte se, že existuje hodnota pro odpovídající atribut ID. |

## <a name="managing-your-configuration"></a>Správa konfigurace

Tato část popisuje, jak můžete dál rozšířit, přizpůsobit a spravovat konfiguraci zřizování uživatelů založenou na Workday. Obsahuje následující témata:

* [Přizpůsobení seznamu atributů uživatele Workday](#customizing-the-list-of-workday-user-attributes)  
* [Export a import vaší konfigurace](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Přizpůsobení seznamu atributů uživatele Workday

Aplikace pro zřizování Workday pro Active Directory a Azure AD zahrnují výchozí seznam atributů uživatele v Workday, ze kterých můžete vybírat. Tyto seznamy nejsou ale vyčerpávající. Workday podporuje mnoho stovek možných uživatelských atributů, které můžou být pro tenanta Workday buď standardní, nebo jedinečné.

Služba zřizování Azure AD podporuje možnost přizpůsobení vašeho seznamu nebo atributu Workday tak, aby zahrnovaly všechny atributy zveřejněné v [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) provozu rozhraní API pro lidské zdroje.

Chcete-li provést tuto změnu, je nutné použít [Workday Studio](https://community.workday.com/studio-download) k extrakci výrazů XPath, které reprezentují atributy, které chcete použít, a pak je přidat do vaší konfigurace zřizování pomocí rozšířeného editoru atributů v Azure Portal.

**Načtení výrazu XPath pro atribut uživatele Workday:**

1. Stáhněte a nainstalujte si [Workday Studio](https://community.workday.com/studio-download). Pro přístup k instalačnímu programu budete potřebovat účet komunity Workday.

2. Stáhněte si soubor **Human_Resources** WSDL pro Workday, který je určený pro verzi rozhraní API WWS, kterou plánujete použít, z [adresáře webové služby Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/index.html) .

3. Spusťte Workday.

4. Na panelu příkazů vyberte **v možnosti Tester možnost testování webové služby Workday >** .

5. Vyberte **externí**a vyberte soubor WSDL Human_Resources, který jste stáhli v kroku 2.

    ![Pracovní den v studiu](./media/workday-inbound-tutorial/wdstudio1.png)

6. Nastavte pole **umístění** na `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`, ale nahraďte "impl-CC" skutečným typem instance a "TENANT" s vaším skutečným názvem tenanta.

7. Nastavit **operaci** na **Get_Workers**

8.  Kliknutím na odkaz malé **Konfigurace** pod podokny žádosti a odpověď nastavte přihlašovací údaje pracovního dne. Zkontrolujte **ověřování**a potom zadejte uživatelské jméno a heslo pro účet systému pro integraci Workday. Ujistěte se, že jste naformátujete uživatelské\@jméno jako název tenanta, a ponecháte vybranou možnost **WS-Security UsernameToken** .

    ![Pracovní den v studiu](./media/workday-inbound-tutorial/wdstudio2.png)

9. Vyberte **OK**.

10. Do podokna **žádosti** vložte následující kód XML. Nastavte **Employee_ID** na ID zaměstnance reálného uživatele v tenantovi Workday. Nastavte **WWS verze na** verzi, kterou plánujete použít. Vyberte uživatele, který má atribut naplněný, který chcete extrahovat.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. Kliknutím na **Odeslat žádost** (zelenou šipku) spusťte příkaz. V případě úspěchu by se měla odpověď zobrazit v podokně **odpovědi** . Zkontrolujte odpověď, abyste se ujistili, že obsahuje data ID uživatele, která jste zadali, a ne chybu.

12. V případě úspěchu zkopírujte kód XML z podokna **odpovědi** a uložte ho jako soubor XML.

13. Na příkazovém řádku v nabídce Workday Studio vyberte **soubor > otevřít soubor...** a otevřete soubor XML, který jste uložili. Tato akce otevře soubor v editoru XML Workday Studio.

    ![Pracovní den v studiu](./media/workday-inbound-tutorial/wdstudio3.png)

14. Ve stromu souborů přejděte přes **/ENV: obálka > ENV: tělo > průchozí: Get_Workers_Response > průchozí: Response_Data > průchozí: Worker** to Find data vašeho uživatele.

15. V části **nepracovní proces**, najděte atribut, který chcete přidat, a vyberte ho.

16. Zkopírujte výraz XPath pro vybraný atribut z pole **cesta dokumentu** .

17. Z kopírovaného výrazu odeberte **/ENV: obálky/ENV: body/přeformátování: Get_Workers_Response/WD: Response_Data/** .

18. Pokud je poslední položka v kopírovaném výrazu uzel (příklad: "/WD: Birth_Date"), pak na konci výrazu přidejte **/text ()** . To není nutné, pokud je poslední položkou atribut (příklad: "/@wd: Type").

19. Výsledek by měl být podobný `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Tato hodnota se zkopíruje do Azure Portal.

**Přidání vlastního atributu pracovního uživatele Workday do vaší konfigurace zřizování:**

1. Spusťte [Azure Portal](https://portal.azure.com)a přejděte do části zřizování vaší aplikace pro zřizování Workday, jak je popsáno výše v tomto kurzu.

2. Nastavte **stav zřizování** hodnotu **vypnuto**a vyberte **Uložit**. Tento krok vám pomůže zajistit, aby se změny projevily až po tom, co jste připraveni.

3. V části **mapování**vyberte možnost **synchronizovat pracovní procesy Workday do místní služby Active Directory** (nebo **synchronizovat pracovní procesy**v rámci služby Azure AD).

4. Posuňte se k dolnímu okraji další obrazovky a vyberte **Zobrazit upřesňující možnosti**.

5. Vyberte **Upravit seznam atributů pro Workday**.

    ![Pracovní den v studiu](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Posuňte se do dolní části seznamu atributů na místo, kde jsou vstupní pole.

7. Jako **název**zadejte zobrazovaný název atributu.

8. Jako **typ**vyberte typ, který odpovídající vašemu atributu (**řetězec** je nejčastěji).

9. Pro **výraz rozhraní API**zadejte výraz XPath, který jste zkopírovali z Workday Studio. Příklad: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Vyberte **Přidat atribut**.

    ![Pracovní den v studiu](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Vyberte **Uložit** výše a pak **Ano** pro dialog. Zavřete obrazovku mapování atributů, pokud je stále otevřená.

12. Zpátky na hlavní kartě **zřizování** vyberte možnost **synchronizovat pracovní procesy Workday do místní služby Active Directory** (nebo **synchronizovat pracovní procesy s Azure AD**).

13. Vyberte **Přidat nové mapování**.

14. Nový atribut by se teď měl zobrazit v seznamu **zdrojového atributu** .

15. Přidejte mapování pro nový atribut podle potřeby.

16. Až budete hotovi, nezapomeňte nastavit **stav zřizování** zpět na **zapnuto** a uložit.

### <a name="exporting-and-importing-your-configuration"></a>Export a import vaší konfigurace

Přečtěte si článek o [exportu a importu konfigurace zřizování](../app-provisioning/export-import-provisioning-configuration.md) .

## <a name="managing-personal-data"></a>Správa osobních údajů

Řešení zřizování pracovních pracovních postupů pro službu Active Directory vyžaduje, aby byl agent zřizování nainstalován na místní Windows Server a tento agent vytváří protokoly v protokolu událostí systému Windows, které mohou obsahovat osobní údaje v závislosti na mapování atributů z Workday na AD. Abyste dosáhli dodržování závazků uživatelů na ochranu osobních údajů, můžete zajistit, aby se v protokolech událostí přesahujících 48 hodin zachovala žádná data, a to nastavením naplánované úlohy Windows pro vymazání protokolu událostí.

Služba zřizování Azure AD spadá do kategorie **procesor dat** klasifikace GDPR. Služba jako kanál datového procesoru poskytuje služby pro zpracování dat pro klíčové partnery a koncové zákazníky. Služba zřizování Azure AD negeneruje uživatelská data a nemá žádnou nezávislou kontrolu nad tím, jaké osobní údaje se shromažďují a jak se používají. Načítání, agregace, analýza a vytváření sestav ve službě Azure AD Provisioning jsou založená na stávajících podnikových datech.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

S ohledem na uchovávání dat služba zřizování Azure AD negeneruje sestavy, provádějí analýzy ani neposkytuje přehledy po dobu 30 dnů. Proto služba zřizování Azure AD neukládá, zpracovává ani neuchovává žádná data déle než 30 dnů. Tento návrh dodržuje předpisy GDPR, nařízení o dodržování předpisů Microsoftu a zásady uchovávání dat v Azure AD.

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy pro aktivitu zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
* [Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Workday a Azure Active Directory](workday-tutorial.md)
* [Naučte se integrovat další aplikace SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Naučte se používat rozhraní Microsoft Graph API ke správě konfigurací zřizování.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
