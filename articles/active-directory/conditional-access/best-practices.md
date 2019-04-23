---
title: Osvědčené postupy pro podmíněný přístup ve službě Azure Active Directory | Dokumentace Microsoftu
description: Další informace o co byste měli vědět a co to je, že při konfiguraci zásad podmíněného přístupu, měli byste se vyhnout tím.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11d4d319fa31dd2493810dc7293d415554f79d94
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60411712"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Osvědčené postupy pro podmíněný přístup v Azure Active Directory

S [podmíněného přístupu Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), jak ověřeného přístupu uživatele můžete řídit vašich cloudových aplikacích. Tento článek poskytuje informace o:

- Co byste měli vědět 
- Co je byste se měli vyhnout postupu při konfiguraci zásad podmíněného přístupu. 

Tento článek předpokládá, že jste obeznámeni s koncepty a terminologie uvedených v [co je podmíněný přístup v Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)



## <a name="whats-required-to-make-a-policy-work"></a>Co potřebná k tomu, aby zásada fungovat?

Když vytvoříte novou zásadu, nejsou žádní uživatelé, skupiny, aplikace ani vybraných ovládacích prvků přístupu.

![Cloudové aplikace](./media/best-practices/02.png)


Aby vaše zásady fungují, je nutné nakonfigurovat:


| Co           | Jak                                  | Proč |
| :--            | :--                                  | :-- |
| **Cloudové aplikace** |Vyberte jednu nebo víc aplikací.  | Cílem zásad podmíněného přístupu je vám umožňují řídit jak Autorizovaní uživatelé můžou používat cloudové aplikace.|
| **Uživatelé a skupiny** | Vyberte alespoň jeden uživatel nebo skupina, která má oprávnění pro přístup k vybrané cloudové aplikace. | Zásady podmíněného přístupu, který nemá žádné uživatelé a skupiny přiřazení, je neaktivní. |
| **Řízení přístupu** | Vyberte aspoň jedno přístupu ovládací prvek. | Jestli vaše podmínky splněny, procesor zásad je potřeba vědět, co dělat. |




## <a name="what-you-should-know"></a>Co byste měli vědět



### <a name="how-are-conditional-access-policies-applied"></a>Jak se používají zásady podmíněného přístupu?

Více než jedny zásady podmíněného přístupu uplatnit při přístupu k cloudovou aplikaci. V takovém případě musí být splněny všechny zásady, které se vztahují. Například pokud jedna zásada vyžaduje vícefaktorové ověřování a druhá vyhovující zařízení, musíte projít MFA a použijte vyhovující zařízení. 

Všechny zásady vynucují ve dvou fázích:

- V **první** fázi, jsou vyhodnoceny všechny zásady a jsou shromážděné všechny ovládací prvky přístupu, které nejsou splněné. 

- V **druhý** fáze, zobrazí se výzva k splňují požadavky nebyly splněny. Pokud některou ze zásad blokování přístupu, se zablokuje a nechcete zobrazit výzvu k splňovat další ovládací prvky zásad. Pokud žádná z zásad zablokuje vás, zobrazí se výzva splňovat další ovládací prvky zásad v následujícím pořadí:

    ![Objednání](./media/best-practices/06.png)
    
    Externí zprostředkovatelé vícefaktorového ověřování a podmínky použití pocházejí Další.



### <a name="how-are-assignments-evaluated"></a>Jak se vyhodnocují přiřazení?

Všechna přiřazení jsou logicky **spojeny**. Pokud máte více než jedno přiřazení nakonfigurovaná všechna přiřazení musí být splněny pro aktivaci zásad.  

Pokud je potřeba nakonfigurovat umístění podmínky, která platí pro všechna připojení z mimo síť ve vaší organizaci:

- Zahrnout **všechna místa**
- Vyloučit **všechny důvěryhodné IP adresy**


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Co dělat, když jsou zamčené z portálu pro správu Azure AD?

Pokud jsou zablokována na portálu Azure AD kvůli nesprávnému nastavení zásad podmíněného přístupu:

- Zkontrolujte je, že nejsou ostatními správci ve vaší organizaci, které ještě nejsou blokované. Správce s přístupem k webu Azure portal můžete zakázat zásadu, která je dopadu na vaše přihlášení. 

- Pokud žádná z správci ve vaší organizaci můžete aktualizovat zásady, musíte odeslat žádost o podporu. Podpora společnosti Microsoft můžete zkontrolovat a aktualizovat zásady podmíněného přístupu, které brání přístupu.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Co se stane, když máte na portálu Azure classic a webu Azure portal nakonfigurovat zásady?  

Obě zásady vynucují v Azure Active Directory a uživatel získá přístup pouze v případě, že jsou splněny všechny požadavky.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Co se stane, když máte zásady na portálu Silverlight pro Intune a na webu Azure portal?

Obě zásady vynucují v Azure Active Directory a uživatel získá přístup pouze v případě, že jsou splněny všechny požadavky.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Co se stane, když mám víc zásad pro stejného uživatele nakonfigurovaná?  

Pro každé přihlášení Azure Active Directory vyhodnotí všechny zásady a zajistí, že jsou splněny všechny požadavky před udělil přístup pro uživatele. Blokovat přístup trumps všechna ostatní nastavení konfigurace. 


### <a name="does-conditional-access-work-with-exchange-activesync"></a>Podmíněný přístup funguje s Exchange ActiveSync?

Ano, pomocí protokolu Exchange ActiveSync v zásadách podmíněného přístupu s některými [omezení](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/conditional-access-for-exo-and-spo#exchange-activesync). 

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Jak byste měli nakonfigurovat podmíněný přístup aplikací Office 365?

Vzhledem k tomu, že jsou propojeny aplikací Office 365, doporučujeme, abyste přiřazení běžně používané aplikace společně při vytváření zásad.

Běžné propojené aplikace obsahují Microsoft Flow, aplikace Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online a Office 365 na Yammeru.

Je důležité pro zásady, které vyžadují interakcí s uživateli, jako je ověřování službou Multi-Factor Authentication, když ke kontrole přístupu slouží na začátku relace nebo úkolu. Pokud to neuděláte, uživatelé nebudou moct provádět některé úlohy v rámci aplikace. Například pokud budete vyžadovat vícefaktorové ověřování na nespravovaných zařízeních přístup k Sharepointu, ale ne k e-mailu, uživatelé, kteří pracují v e-mailu nebudete moci připojit soubory služby SharePoint na zprávu. Další informace najdete v článku, [co jsou závislosti služby v Azure Active Directory podmíněného přístupu?](service-dependencies.md).





## <a name="what-you-should-avoid-doing"></a>Co byste se měli vyhnout tím

Rozhraní podmíněný přístup vám poskytuje flexibilitu skvělé konfigurace. Velkou flexibilitu, ale také znamená, že byste měli pečlivě zkontrolovat každou zásadu konfigurace před uvolněním, abyste se vyhnuli dalším nežádoucí výsledky. V tomto kontextu, musí věnovat zvláštní pozornost přiřazení, jako by to ovlivnilo kompletní sady **všech uživatelů / skupin / cloudové aplikace**.

Ve vašem prostředí měli byste se vyhnout následující konfigurace:


**Pro všechny uživatele všechny cloudové aplikace:**

- **Blokovat přístup** – tato konfigurace zablokuje celou organizaci, které určitě není vhodné.

- **Vyžadovat vyhovující zařízení** – pro uživatele, který nezaregistrovali svoje zařízení, ale tato zásada blokuje veškerý přístup, včetně přístupu k portálu Intune. Pokud jste správce bez registrovaných zařízení, zablokuje vás tato zásada získat zpět na web Azure Portal ke změně zásady.

- **Vyžadovat připojení k doméně** – tento blok zásad přístupu má také potenciál k blokování přístupu pro všechny uživatele ve vaší organizaci, pokud ještě nemáte zařízení připojených k doméně.

- **Vyžadovat zásady ochrany aplikací** – tento blok zásad přístupu má také potenciál k blokování přístupu pro všechny uživatele ve vaší organizaci, pokud nemáte k dispozici zásady Intune. Pokud jste správce bez klientskou aplikaci, která má zásady ochrany aplikací Intune, zablokuje vás tyto zásady z návrat do portály, jako jsou Intune a Azure.

**Pro všechny uživatele, všechny cloudové aplikace, všechny platformy zařízení:**

- **Blokovat přístup** – tato konfigurace zablokuje celou organizaci, které určitě není vhodné.


## <a name="how-should-you-deploy-a-new-policy"></a>Jak můžete nasadit nové zásady?

Jako první krok, by se měl vyhodnotit zásadu pomocí [co když nástroj](what-if-tool.md).

Jakmile jsou připravené pro vaše prostředí nové zásady, můžete je nasadíte ve fázích:

1. Použít zásady pro malou skupinu uživatelů a ověřte, že se chová podle očekávání. 

2.  Po rozbalení zásadu, která zahrnují více uživatelů. Vyloučit všechny správce z této zásady zajistíte, že jsou stále máte přístup a zásady můžete aktualizovat, pokud je požadována změna i nadále.

3. Zásady platí pro všechny uživatele, pouze v případě potřeby. 

Jako osvědčený postup vytvoření uživatelského účtu, který je:

- Vyhrazený pro správu zásad 
- Vyloučené ze všech zásad


## <a name="policy-migration"></a>Migrace zásad

Vezměte v úvahu migrace zásad, které jste ještě nevytvořili na webu Azure Portal, protože:

- Nyní můžete řešit situace, které nelze zpracovat před.

- Můžete snížit počet zásad, které je nutné spravovat jejich sloučení.   

- Můžete spravovat všechny zásady podmíněného přístupu v jednom centrálním místě.

- Na portálu Azure classic je vyřazený.   


Další informace najdete v tématu [migrace klasických zásad na webu Azure Portal](policy-migration.md).


## <a name="next-steps"></a>Další postup

Pokud budete chtít vědět:

- Jak nakonfigurovat zásady podmíněného přístupu, [vyžadovat vícefaktorové ověřování pro konkrétní aplikace s podmíněným přístupem Azure Active Directory](app-based-mfa.md).
- Plánování zásad podmíněného přístupu, naleznete v tématu [postup plánování vašeho nasazení podmíněného přístupu v Azure Active Directory](plan-conditional-access.md).
