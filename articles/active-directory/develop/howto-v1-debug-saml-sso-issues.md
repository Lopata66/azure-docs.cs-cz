---
title: Ladění jednotného přihlašování založeného na SAML Azure Active Directory | Microsoft Docs
description: Ladění jednotného přihlašování založeného na SAML pro aplikace v Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: luleon, hirsin, paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c3502567df7776e106ae9301aa7ba315cc12cc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917603"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Ladění jednotného přihlašování založeného na SAML pro aplikace v Azure Active Directory

Naučte se najít a opravit problémy s [jednotným přihlašováním](../manage-apps/what-is-single-sign-on.md) pro aplikace v Azure Active Directory (Azure AD), které podporují [Security Assertion Markup Language (SAML) 2,0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language). 

## <a name="before-you-begin"></a>Než začnete

Doporučujeme, abyste nainstalovali [rozšíření pro zabezpečené přihlašování k aplikacím](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Toto rozšíření prohlížeče usnadňuje shromažďování požadavků SAML a odpovědí SAML, které potřebujete k řešení problémů s jednotným přihlašováním. V případě, že nemůžete nainstalovat rozšíření, v tomto článku se dozvíte, jak vyřešit problémy s nainstalovaným i bez rozšíření.

Pokud chcete stáhnout a nainstalovat zabezpečené přihlašování pro moje aplikace, použijte jeden z následujících odkazů.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testování jednotného přihlašování založeného na SAML

Testování jednotného přihlašování založeného na SAML mezi Azure AD a cílovou aplikací:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce nebo jiného správce, který je autorizován pro správu aplikací.
1. V levém okně vyberte **Azure Active Directory**a pak vyberte **podnikové aplikace**. 
1. V seznamu podnikových aplikací vyberte aplikaci, pro kterou chcete testovat jednotné přihlašování, a potom z možností na levém panelu vyberte **jednotné přihlašování**.
1. Chcete-li otevřít prostředí pro testování jednotného přihlašování založené na SAML, použijte **test jednotného přihlašování** (krok 5). Pokud je tlačítko **test** šedé, je nutné nejprve vyplnit a uložit požadované atributy v **základním oddílu konfigurace SAML** .
1. V okně **test jednotného přihlašování** pomocí podnikových přihlašovacích údajů se přihlaste k cílové aplikaci. Můžete se přihlásit jako aktuální uživatel nebo jako jiný uživatel. Pokud se přihlásíte jako jiný uživatel, zobrazí se výzva k ověření.

    ![Snímek obrazovky se stránkou zkušebního přihlášení SAML](./media/howto-v1-debug-saml-sso-issues/test-single-sign-on.png)

Pokud jste se úspěšně přihlásili, test byl úspěšný. V tomto případě služba Azure AD vystavila token odpovědi SAML do aplikace. Aplikace, se kterou se úspěšně přihlásil token SAML

Pokud máte chybu na přihlašovací stránce společnosti nebo na stránce aplikace, vyřešte tuto chybu pomocí jedné z dalších částí.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Řešení chyby přihlášení na přihlašovací stránce vaší společnosti

Při pokusu o přihlášení se může zobrazit chyba na přihlašovací stránce vaší společnosti, která je podobná následujícímu příkladu.

![Příklad znázorňující chybu na přihlašovací stránce společnosti](./media/howto-v1-debug-saml-sso-issues/error.png)

Chcete-li tuto chybu ladit, budete potřebovat chybovou zprávu a požadavek SAML. Rozšíření pro zabezpečené přihlašování k aplikacím tyto informace automaticky shromáždí a zobrazí pokyny k řešení v Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Chcete-li vyřešit chybu přihlášení pomocí nainstalovaného rozšíření pro zabezpečené přihlašování k aplikacím

1. Pokud dojde k chybě, rozšíření vás přesměruje zpět do okna pro **jednotné přihlašování** Azure AD test.
1. V okně **test jednotného přihlašování** vyberte **Stáhnout požadavek SAML**.
1. Na základě chyby a hodnot v požadavku SAML byste měli vidět konkrétní pokyny k řešení.
1. K vyřešení tohoto problému se zobrazí tlačítko **opravit** pro automatickou aktualizaci konfigurace ve službě Azure AD. Pokud toto tlačítko nevidíte, problém s přihlášením není kvůli nekonfiguraci služby Azure AD v důsledku chyby.

Pokud není pro chybu přihlášení k dispozici žádné řešení, doporučujeme vám, abyste nám informovali textové pole pro zpětnou vazbu.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Řešení chyby bez instalace zabezpečeného přihlašovacího rozhraní pro moje aplikace

1. Zkopírujte chybovou zprávu v pravém dolním rohu stránky. Chybová zpráva obsahuje:
    - ID korelace a časové razítko. Tyto hodnoty jsou důležité při vytváření případu podpory u Microsoftu, protože pomohou technikům identifikovat váš problém a poskytnout přesné řešení problému.
    - Příkaz, který identifikuje hlavní příčinu problému.
1. Vraťte se do Azure AD a najděte okno **test jednotného přihlašování** .
1. V textovém poli nahoře **získat pokyny k řešení**vložte chybovou zprávu.
1. Kliknutím na **získat pokyny k řešení** můžete zobrazit kroky pro řešení tohoto problému. Doprovodné materiály mohou vyžadovat informace od žádosti SAML nebo odpovědi SAML. Pokud nepoužíváte rozšíření pro zabezpečené přihlašování k aplikacím, možná budete potřebovat nástroj, například [Fiddler](https://www.telerik.com/fiddler) , který načte požadavek a odpověď SAML.
1. Ověřte, že cíl v žádosti SAML odpovídá adrese URL služby jednotného přihlašování SAML získané z Azure AD.
1. Ověřte, že Vystavitel v požadavku SAML je stejný identifikátor, který jste nakonfigurovali pro aplikaci ve službě Azure AD. Azure AD používá vystavitele k vyhledání aplikace ve vašem adresáři.
1. Ověřte, že AssertionConsumerServiceURL očekává, že aplikace získá token SAML z Azure AD. Tuto hodnotu můžete nakonfigurovat v Azure AD, není to ale povinné, pokud je součástí žádosti SAML.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Řešení chyby přihlášení na stránce aplikace

Je možné, že se úspěšně přihlásíte a pak na stránce aplikace vidíte chybu. K tomu dochází, když Azure AD vystavil do aplikace token, ale aplikace tuto odpověď nepřijme.

Chcete-li chybu vyřešit, postupujte takto:

1. Pokud je aplikace v galerii Azure AD, ověřte, že jste postupovali podle všech kroků pro integraci aplikace se službou Azure AD. Pokyny pro integraci aplikace najdete v [seznamu kurzů pro integraci aplikací SaaS](../saas-apps/tutorial-list.md).
1. Načtěte odpověď SAML.
    - Pokud je nainstalované rozšíření pro zabezpečené přihlašování k aplikacím, v okně **test jednotného přihlašování** klikněte na **Stáhnout odpověď SAML**.
    - Pokud rozšíření není nainstalované, načtěte odpověď SAML pomocí nástroje, jako je například [Fiddler](https://www.telerik.com/fiddler) .
1. Všimněte si těchto prvků v tokenu odpovědi SAML:
   - Jedinečný identifikátor NameID hodnoty a formátu pro uživatele
   - Deklarace identity vystavené v tokenu
   - Certifikát použitý k podepsání tokenu

     Další informace o odpovědi SAML najdete v tématu [protokol SAML jednotného přihlašování](single-sign-on-saml-protocol.md).

1. Teď, když jste zkontrolovali odpověď SAML, najdete informace o tom, jak tento problém vyřešit, v části [Chyba na stránce aplikace po přihlášení](../manage-apps/application-sign-in-problem-application-error.md) . 
1. Pokud se stále nemůžete přihlásit úspěšně, můžete požádat dodavatele aplikace, aby v odpovědi SAML chyběl.

## <a name="next-steps"></a>Další kroky

Teď, když se v aplikaci pracuje s jednotným přihlašováním, můžete [automatizovat zřizování uživatelů a zrušení zřizování pro SaaS aplikace](../manage-apps/user-provisioning.md) nebo začít [s podmíněným přístupem](../conditional-access/app-based-conditional-access.md).
