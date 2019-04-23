---
title: Jak používat balíček obsahu Azure Active Directory Power BI Content Pack | Dokumentace Microsoftu
description: Naučte se používat balíček obsahu Azure Active Directory Power BI Content Pack.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 934562147fedcc81b16fd1ad2534af5662ef4b78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437599"
---
# <a name="how-to-use-the-azure-active-directory-power-bi-content-pack"></a>Jak používat balíček obsahu Azure Active Directory Power BI

|  |
|--|
|V současné době balíček obsahu Azure AD Power BI používá rozhraní Azure AD Graph API k načtení dat z vašeho tenanta Azure AD. V důsledku toho můžete zaznamenat určitý nesoulad mezi daty, která jsou k dispozici v balíčku obsahu, a daty načtenými pomocí [rozhraní Microsoft Graph API pro generování sestav](concept-reporting-api.md). |
|  |

Power BI content pack pro službu Azure Active Directory (Azure AD) obsahuje předdefinované sestavy, které vám pomohou pochopit, jakým způsobem uživatelé přijímají a používat funkce služby Azure AD. To umožňuje získat přehled o všech aktivitách v adresáři, pomocí prostředí bohatých funkcí vizualizace v Power BI. Můžete také vytvořit vlastní řídicí panel a sdílet ho s kýmkoli ve vaší organizaci. 

## <a name="prerequisites"></a>Požadavky

Potřebujete licenci Azure AD premium (P1/P2) pro použití balíčku obsahu. Zobrazit [Začínáme se službou Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) upgradovat edici Azure Active Directory.

## <a name="install-the-content-pack"></a>Nainstalujte balíček obsahu

Podívejte se [rychlý Start](quickstart-install-power-bi-content-pack.md) k instalaci balíčku obsahu Azure AD Power BI.

### <a name="reports-included-in-this-version-of-azure-ad-logs-content-pack"></a>Balíčku obsahu protokolů sestavy zahrnuté v této verzi služby Azure AD

Tyto sestavy jsou součástí balíčku obsahu Azure AD Power BI. Sestavy obsahují data z **posledních 30 dní**.

**Sestava využití aplikace a trendy**:  Tato sestava poskytuje informace o aplikacích používaných ve vaší organizaci. Můžete získat seznam nejoblíbenějších aplikací nebo pochopit, jak se používá aplikace, které se nedávno zavedli ve vaší organizaci. To umožňuje sledovat a vylepšit využití v průběhu času.

**Přihlášení podle umístění a uživatelů**: Tato sestava poskytuje data o všech přihlášeních provedených pomocí Azure Identity. U této sestavy může se ponořit až individuální přihlášení a odpovězte na otázky jako:

- Kde tento uživatel přihlásit z?
- Který uživatel má nejvíce přihlášení a odkud se přihlašuje? 
- Bylo přihlášení úspěšné?  
 
Výsledky můžete také filtrovat tak, že vyberete konkrétní datum nebo umístění.

**Jedineční uživatelé na aplikaci**:  Tato sestava poskytuje přehled všech jedinečných uživatelů, kteří danou aplikaci. Zahrnuje pouze uživatelé, kteří mají "*úspěšně*" přihlášení do aplikace.

**Přihlášení zařízení**: Tato sestava vám pomůže pochopit různé profily zařízení používané ve vaší organizaci a určit zásady zařízení na základě využití. Poskytuje daty o typ operačního systému a používaných pro přihlášení k aplikacím, spolu s podrobnými informacemi o uživatelích, včetně prohlížečů:

- Uživatelské jméno
- IP adresa
- Location 
- Stav přihlášení 

**Trychtýř SSPR**: Tato sestava pomáhá pochopit, jak se používá nástroj pro samoobslužné resetování HESLA ve vaší organizaci. Můžete zobrazit, kolik o resetování hesla bylo prostřednictvím nástroje SSPR a kolik z nich byly úspěšné. Můžete také podrobnější informace o selhání resetování hesel a pochopit, proč k některým chybám došlo. 

## <a name="customize-azure-ad-activity-content-pack"></a>Přizpůsobení balíčku obsahu aktivit služby Azure AD

**Změna vizualizace**:  Vizualizaci sestavy můžete změnit kliknutím **upravit sestavu** a vyberte požadované vizualizace.
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/09.png) 
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/10.png) 

**Zahrnutí dalších polí**:  Můžete přidat pole do sestavy nebo z ní odebrat výběrem vizuálu, do které chcete přidat nebo odebrat pole. Můžete například přidat pole "stav přihlášení" do zobrazení tabulky, jak je znázorněno níže. 
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/11.png) 

**Připnutí vizualizace na řídicí panel**:  Řídicí panel můžete přizpůsobit tak, že včetně vaší vlastní vizualizace do sestavy a Připnutí na řídicí panel. 

![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/13.png) 
 
**Sdílení řídicího panelu**: Řídicí panel můžete také sdílet s uživateli ve vaší organizaci. Jakmile sestavu sdílíte, uživatelé mohou vidět vybraných v sestavě polí.
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/14.png) 

## <a name="schedule-a-daily-refresh-of-your-power-bi-report"></a>Naplánovat každodenní aktualizaci sestavy Power BI

Pokud chcete naplánovat každodenní aktualizaci sestavy Power BI, přejděte na **datových sad** > **nastavení** > **naplánovat aktualizaci** a nastavte ji podle příkladu níže.
 
![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/15.png) 

## <a name="update-to-newer-version-of-content-pack"></a>Aktualizovat na novější verzi balíčku obsahu

Pokud chcete aktualizovat svůj balíček obsahu na novější verzi:

- Stáhněte si nový balíček obsahu a nastavení podle pokynů v tomto článku.

- Jakmile nastavíte ho, přejděte na **zdroj dat** > **nastavení** > **přihlašovací údaje ke zdroji dat** a znovu zadejte svoje přihlašovací údaje.

    ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/howto-power-bi-content-pack/16.png) 

Jakmile ověříte, že nová verze balíčku obsahu funguje podle očekávání, můžete odebrat starou verzi v případě potřeby odstraněním podkladových sestav a datové sady přidružené k tomuto balíčku obsahu.

## <a name="troubleshoot-content-pack-errors"></a>Řešení potíží s chybami balíčku obsahu

Při práci s balíčkem obsahu, je možné, že narazíte na následující chyby: 

- [Aktualizace se nezdařila.](#refresh-failed) 
- [Nepovedlo se aktualizovat přihlašovací údaje ke zdroji dat](#failed-to-update-data-source-credentials) 
- [Import dat trvá moc dlouho](#data-import-is-too-slow) 

Obecnou nápovědu k Power BI najdete v těchto [článcích nápovědy](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

### <a name="refresh-failed"></a>Aktualizace se nezdařila 
 
**Jak se zobrazí tato chyba**: E-mailu z Power BI nebo stavu selhání v historii aktualizace. 


| Příčina | K vyřešení |
| ---   | ---        |
| Aktualizujte chyby, které chyby může být způsobena přihlašovacích údajů uživatele, připojení k balíčku obsahu byly obnovit, ale není aktualizovaná. v nastavení připojení balíčku obsahu. | V Power BI, vyhledejte sadu dat odpovídající panel protokolů aktivity služby Azure AD (**protokoly aktivit Azure Active Directory**), zvolte naplánovat aktualizaci a pak zadejte svoje přihlašovací údaje Azure AD. |
| Obnovení může selhat kvůli problémům s daty v základní balíčku obsahu. | [Lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
### <a name="failed-to-update-data-source-credentials"></a>Nepovedlo se aktualizovat přihlašovací údaje ke zdroji dat 
 
**Jak se zobrazí tato chyba**: V Power BI, když se připojíte k balíčku obsahu protokolů aktivit Azure AD. 

| Příčina | K vyřešení |
| ---   | ---        |
| Je připojující se uživatel není globální správce nebo čtenáře zabezpečení nebo správce zabezpečení. | Použijte účet, který je globálním správcem nebo Čtenář zabezpečení nebo správce zabezpečení pro přístup k obsahu sady. |
| Váš tenant není Premium tenanta nebo nemá alespoň jeden uživatel s licencí Premium souboru. | [Lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
### <a name="data-import-is-too-slow"></a>Import dat je pomalý 
 
**Jak se zobrazí tato chyba**: V Power BI připojíte balíček obsahu, proces importu dat spustí Příprava řídicí panel aktivit Azure AD protokoly. Zobrazí se tato zpráva: **Importují se data...**  bez jakékoli další krok.  

| Příčina | K vyřešení |
| ---   | ---        |
| V závislosti na velikosti tenanta tento krok může trvat pár minut až 30 minut. | Pokud zprávu nezmění na zobrazení řídicího panelu do jedné hodiny, [lístek podpory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
  
## <a name="next-steps"></a>Další postup

* [Nainstalujte balíček obsahu Power BI](quickstart-install-power-bi-content-pack.md).
* [Co jsou sestavy Azure AD? ](overview-reports.md).
