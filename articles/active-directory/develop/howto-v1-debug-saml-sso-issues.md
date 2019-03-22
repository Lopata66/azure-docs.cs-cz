---
title: Ladění založené na SAML jednotného přihlašování – Azure Active Directory | Dokumentace Microsoftu
description: Ladění založené na SAML jednotného přihlašování k aplikacím v Azure Active Directory.
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: luleon, hirsin, smalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fcc6cb40d83c06a1c9f0a97c72565464e74e655
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336066"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Ladění založené na SAML jednotného přihlašování k aplikacím v Azure Active Directory

Zjistěte, jak najít a opravit [jednotného přihlašování](../manage-apps/what-is-single-sign-on.md) problémy pro aplikace v Azure Active Directory (Azure AD), které podporují [zabezpečení kontrolního výrazu SAML (Markup Language) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Před zahájením

Doporučujeme vám však nainstalovat [Moje aplikace zabezpečené přihlašování rozšíření](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Toto rozšíření prohlížeče umožňuje snadno získat požadavek SAML a informace odpověď SAML, které potřebujete k řešení problémů s jednotným přihlašováním. V případě, že nemůžete instalovat rozšíření, tento článek ukazuje, jak vyřešit problémy s i bez nainstalované rozšíření.

Stáhnout a nainstalovat rozšíření Moje aplikace zabezpečené přihlašování, použijte jednu z následujících odkazů.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>Testování založené na SAML jednotného přihlašování

Testování založené na SAML jednotného přihlašování mezi Azure AD a cílová aplikace:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako globální správce nebo jiný správce, který má oprávnění ke správě aplikací.
1. V levém podokně vyberte **Azure Active Directory**a pak vyberte **podnikové aplikace**. 
1. V seznamu podnikových aplikací, vyberte aplikaci, pro který chcete otestovat jednotné přihlašování a potom z možností v levém vyberte **jednotného přihlašování**.
1. Chcete-li otevřít založené na SAML jednotné přihlašování – testovací prostředí, přejděte na **otestovat jednotné přihlašování** (krok 5). Pokud **testovací** tlačítko zašedlé, budete muset nejprve vyplňte a uložte povinné atributy **základní konfiguraci SAML** oddílu.
1. V **otestovat jednotné přihlašování** okně používat své podnikové přihlašovací údaje pro přihlášení k cílové aplikaci. Můžete přihlásit jako aktuální uživatel nebo jako jiný uživatel. Pokud se přihlásit jako jiný uživatel výzvu vás vyzve k ověření.

    ![Zkušební stránku SAML](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)


Pokud jste úspěšně přihlášení, má test byl dokončen. V tomto případě Azure AD vystaví token SAML odpovědi do aplikace. Aplikace používá pro úspěšné přihlášení tokenu SAML.

Pokud máte chybu na přihlašovací stránce společnosti nebo aplikace, použijte jednu z následujících částech tuto chybu napravíme.


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Vyřešte chyby přihlášení na přihlašovací stránku vaší společnosti

Při pokusu o přihlášení může zobrazit chyba ve vaší společnosti přihlašovací stránku, která se podobá následujícímu příkladu.

![Chyby přihlášení](./media/howto-v1-debug-saml-sso-issues/error.png)

Chcete-li ladit tuto chybu, budete potřebovat chybovou zprávu a požadavku SAML. Rozšíření Moje aplikace zabezpečené přihlášení automaticky shromažďuje tyto informace a zobrazí pokyny k řešení v Azure AD. 

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Chcete-li vyřešit chyby přihlášení s příponou Moje aplikace zabezpečené přihlašování nainstalovaná

1. Pokud dojde k chybě, rozšíření vás přesměruje zpátky do služby Azure AD **otestovat jednotné přihlašování** okno. 
1. Na **otestovat jednotné přihlašování** okně vyberte **stáhnout si požadavek SAML**. 
1. Měli byste vidět konkrétní řešení pokyny na základě chybu a hodnoty v požadavku SAML.
1. Zobrazí se **opravit** tlačítko automaticky aktualizovat konfigurace ve službě Azure AD k vyřešení daného problému. Pokud toto tlačítko nevidíte, pak problém přihlášení není kvůli chybné konfiguraci v Azure AD.

Není-li žádné řešení pro chyby přihlášení, doporučujeme používat textové pole zpětné vazby na nás informujte.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Chcete-li vyřešit chybu bez instalace rozšíření Moje aplikace zabezpečené přihlášení

1. Zkopírujte chybovou zprávu v pravém dolním rohu stránky. Chybová zpráva obsahuje:
    - ID korelace a časové razítko. Tyto hodnoty jsou důležité při vytváření případu podpory s Microsoftem, protože pomáhají rychle, aby váš problém identifikovat a zadat přesné řešení vašeho problému.
    - Příkaz, který identifikuje původní příčinu problému.
1. Vraťte se do služby Azure AD a najít **otestovat jednotné přihlašování** okno.
1. V textovém poli nahoře **pomoc rozlišení**, vložte chybové zprávě.
1. Klikněte na tlačítko **pomoc rozlišení** zobrazíte kroky vedoucí k vyřešení problému. Pokyny mohou vyžadovat informace z požadavku SAML nebo odpověď SAML. Pokud nepoužíváte rozšíření Moje aplikace zabezpečené přihlašování, budete nejspíš muset nástroj, jako [Fiddler](https://www.telerik.com/fiddler) získat požadavek SAML a odpovědi.
1. Ověřte, že cíl v požadavku SAML odpovídá SAML jednotné přihlašování služby Adresa URL získaná z Azure AD.
1. Ověřte, že stejný identifikátor, který jste nakonfigurovali pro aplikaci ve službě Azure AD je issuer v požadavku SAML. K vyhledání aplikace ve vašem adresáři používá Azure AD vystavitele.
1. Ověřte, že AssertionConsumerServiceURL je, kde se očekává, že aplikace přijímat tokenu SAML ze služby Azure AD. Tuto hodnotu můžete nakonfigurovat ve službě Azure AD, ale není povinné, pokud se jedná o požadavek SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Vyřešte chyby přihlášení na stránce aplikace

Může úspěšně přihlásit a pak na stránce aplikace zobrazí chyba. K tomu dojde, když Azure AD vystaví token do aplikace, ale aplikace nepřijímá žádné odpovědi.   

Chcete-li chybu vyřešit, postupujte takto:

1. Pokud je aplikace v galerii Azure AD, ověřte, že jste provedli všechny kroky pro integraci aplikace s Azure AD. Pokyny k integraci vaší aplikace najdete v tématu [seznamu kurzů integrace aplikací SaaS](../saas-apps/tutorial-list.md).
1. Načte odpověď SAML.
    - Pokud je nainstalovaná rozšíření Moje aplikace zabezpečené přihlašování, z **otestovat jednotné přihlašování** okna, klikněte na tlačítko **stáhnout odpověď SAML**.
    - Pokud není nainstalovaná rozšíření, použijte nástroj, jako [Fiddler](https://www.telerik.com/fiddler) načíst odpověď SAML. 
1. Všimněte si, že tyto prvky v tokenu SAML odpovědi:
   - Jedinečný identifikátor uživatele NameID hodnota a formátu
   - Deklarací identity vystavených v tokenu
   - Certifikát použitý k podpisu tokenu. 

     Další informace o odpověď SAML, naleznete v tématu [protokol jednotné přihlašování SAML](single-sign-on-saml-protocol.md).

1. Teď, když si přečtete odpověď SAML, naleznete v tématu [chyba na stránce aplikace po přihlášení](../manage-apps/application-sign-in-problem-application-error.md) pokyny o tom, aby problém pomohl vyřešit. 
1. Pokud jste pořád nejste schopni úspěšně se přihlásit, požádejte dodavatele aplikace co chybí odpověď SAML.


## <a name="next-steps"></a>Další postup

Teď, když jednotné přihlašování funguje pro aplikace, může [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS](../manage-apps/user-provisioning.md) nebo [Začínáme s podmíněným přístupem](../conditional-access/app-based-conditional-access.md).
