---
title: Co je migrace zásad v Azure Active Directory podmíněného přístupu? | Dokumenty Microsoft
description: Zjistěte, co potřebujete vědět o migrace klasických zásad na webu Azure Portal.
services: active-directory
keywords: Podmíněný přístup k aplikacím, podmíněný přístup s Azure AD, zabezpečený přístup k prostředkům společnosti, zásady podmíněného přístupu
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
ms.date: 07/24/2018
ms.author: joflore
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25161a6317392274ccce8865f7cc0071f0ec89b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112187"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>Co je migrace zásad v Azure Active Directory podmíněného přístupu? 


[Podmíněný přístup](../active-directory-conditional-access-azure-portal.md) je funkce Azure Active directory (Azure AD), která umožňuje řídit jak Autorizovaní uživatelé přistupovat ke vašim cloudovým aplikacím. Když účel zůstává stejný, vydání nového portálu Azure přináší významná vylepšení na tom, jak podmíněný přístup funguje.

Migrace zásad, které jste ještě nevytvořili na webu Azure Portal, protože byste měli zvážit:

- Nyní můžete řešit situace, které nelze zpracovat před.

- Můžete snížit počet zásad, které je nutné spravovat jejich sloučení.   

- Můžete spravovat všechny zásady podmíněného přístupu v jednom centrálním místě.

- Na portálu Azure classic se vyřadí z provozu.   

Tento článek vysvětluje, co potřebujete vědět o migraci stávající zásady podmíněného přístupu na nový rámec.
 
## <a name="classic-policies"></a>Klasické zásady

V [webu Azure portal](https://portal.azure.com), [podmíněného přístupu – zásady](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) je stránka zásady podmíněného přístupu vaším vstupním bodem. Ve vašem prostředí, může však také mít zásady podmíněného přístupu, které nebyly vytvořené pomocí této stránky. Tyto zásady jsou označovány jako *klasické zásady*. Klasické zásady se zásady podmíněného přístupu, které jste vytvořili v:

- Na portálu Azure classic
- Na klasickém portálu Intune
- Na portálu Intune App Protection


Na **podmíněného přístupu** stránky, klasické zásady se zpřístupní po kliknutí [ **klasické zásady (preview)** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) v **spravovat** oddíl. 


![Azure Active Directory](./media/policy-migration/71.png)


**Klasické zásady** zobrazení vám poskytuje možnost:

- Filtrovat klasické zásady.
 
    ![Azure Active Directory](./media/policy-migration/72.png)

- Zakážete klasické zásady.

    ![Azure Active Directory](./media/policy-migration/73.png)
   
- Zkontrolujte nastavení, klasické zásady (a pro jeho zakázání).

    ![Azure Active Directory](./media/policy-migration/74.png)


Pokud jste zakázali klasické zásady, nemůže už vrátit tento krok. To je důvod, proč můžete upravit členství ve skupině do pomocí klasické zásady **podrobnosti** zobrazení. 

![Azure Active Directory](./media/policy-migration/75.png)

Změna vybrané skupiny nebo vyloučení konkrétních skupin můžete otestovat efekt zakázané klasické zásady pro několik testovacích uživatelů před vypnutím zásad pro všechna zahrnutí uživatelé a skupiny. 



## <a name="azure-ad-conditional-access-policies"></a>Zásady Azure AD podmíněného přístupu

Pomocí podmíněného přístupu na webu Azure Portal můžete spravovat všechny zásady v jednom centrálním místě. Protože provádění jak významně změnil podmíněného přístupu, které byste se seznámit se základními koncepcemi před migrací klasické zásady.

Další informace:

- [Co je podmíněný přístup v Azure Active Directory](../active-directory-conditional-access-azure-portal.md) Další informace o základních konceptech a terminologii.

- [Osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md) chcete získat nějaké pokyny k nasazení podmíněného přístupu ve vaší organizaci.

- [Vyžadovat vícefaktorové ověřování pro konkrétní aplikace pomocí Azure Active Directory podmíněného přístupu](app-based-mfa.md) Seznamte se s uživatelským rozhraním webu Azure Portal.


 
## <a name="migration-considerations"></a>Požadavky na migraci

V tomto článku, zásady podmíněného přístupu Azure AD jsou také označovány jako *nové zásady*.
Klasické zásady dál fungovat společně s vaší nové zásady, dokud zakázat nebo odstranit. 

Tyto aspekty jsou důležité v kontextu sloučení zásad:

- Zatímco klasické zásady jsou vázané na konkrétní cloudové aplikaci, můžete vybrat podle musíte v nových zásad cloudových aplikací.

- Ovládací prvky klasické zásady a nové zásady pro cloudové aplikace vyžadují všechny ovládací prvky (*a*) musí být splněny. 


- V nové zásady můžete:
 
    - Pokud to váš scénář vyžaduje zkombinovat více podmínek. 

    - Vyberte několik udělit požadavky na přístup, řízení a zkombinujte je se logické *nebo* (vyžadovat jeden z vybraných ovládacích prvků) nebo s logickým *a* (vyžadovat všechny vybrané ovládací prvky).

        ![Azure Active Directory](./media/policy-migration/25.png)




### <a name="office-365-exchange-online"></a>Office 365 Exchange online

Pokud chcete migrovat klasické zásady pro **Office 365 Exchange online** , které zahrnují **protokolu Exchange Active Sync** jako stav klienta aplikace, nemusí být možné sloučit do jedné nové zásady. 

To platí, například pokud chcete podporovat všechny typy klientských aplikací. V nové zásady, který má **protokolu Exchange Active Sync** jako stav klienta aplikace, nelze vybrat jiné klientské aplikace.

![Azure Active Directory](./media/policy-migration/64.png)

Konsolidaci do jedné nové zásady není možné, pokud klasické zásady obsahují několik podmínek. Novou zásadu, která má **protokolu Exchange Active Sync** jako klientských aplikací nepodporuje podmínky nakonfigurované další podmínky:   

![Azure Active Directory](./media/policy-migration/08.png)

Pokud máte novou zásadu, která má **protokolu Exchange Active Sync** jako klientské aplikace podmínky nakonfigurované, musíte zajistit, aby nebyly nakonfigurovány všechny ostatní podmínky. 

![Azure Active Directory](./media/policy-migration/16.png)
 

[Na základě aplikace](technical-reference.md#approved-client-app-requirement) klasické zásady pro Office 365 Exchange Online, které obsahují **protokolu Exchange Active Sync** jako stav klienta aplikace povolit **podporované** a **nepodporované** [platformy zařízení](technical-reference.md#device-platform-condition). Zatímco nelze konfigurovat jednotlivá zařízení platformy v související nové zásady, můžete omezit podporu o [podporované platformy zařízení](technical-reference.md#device-platform-condition) pouze. 

![Azure Active Directory](./media/policy-migration/65.png)

Můžete konsolidovat více klasické zásady, které obsahují **protokolu Exchange Active Sync** jako podmínku aplikace klienta, pokud mají:

- Pouze **protokolu Exchange Active Sync** jako podmínku 

- Několik požadavků pro udělení přístupu nakonfigurované

Jeden běžný scénář je konsolidovaná:

- Klasické zásady z portálu Azure classic podle zařízení 
- Základě aplikace klasické zásady na portálu Intune app protection 
 
V takovém případě můžete konsolidovat klasické zásady do jedné nové zásady, který má i požadavky na vybrané.

![Azure Active Directory](./media/policy-migration/62.png)



### <a name="device-platforms"></a>Platformy zařízení

Klasické zásady se [ovládacích prvků na základě aplikace](technical-reference.md#approved-client-app-requirement) předem nakonfigurovaným programem zařízení s iOS a Android jako [podmínku platformy zařízení](technical-reference.md#device-platform-condition). 

V nové zásady, budete muset vybrat [platformy zařízení](technical-reference.md#device-platform-condition) chcete zajistit podporu jednotlivě.

![Azure Active Directory](./media/policy-migration/41.png)



 
 


## <a name="next-steps"></a>Další postup

- Pokud chcete vědět, jak nakonfigurovat zásady podmíněného přístupu najdete v tématu [vyžadovat vícefaktorové ověřování pro konkrétní aplikace, pomocí Azure Active Directory podmíněného přístupu](app-based-mfa.md).

- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu pro vaše prostředí, najdete v článku [osvědčené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md). 
