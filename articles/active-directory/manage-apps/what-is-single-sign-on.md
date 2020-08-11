---
title: Co je jednotné přihlašování Azure (SSO)?
description: Přečtěte si, jak jednotné přihlašování (SSO) funguje s Azure Active Directory. Použijte jednotné přihlašování, aby uživatelé nemuseli pamatovat hesla pro každou aplikaci. K zjednodušení správy správy účtů taky používejte jednotné přihlašování.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5446d945a81d2630d788a866bda00eb118382b93
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053775"
---
# <a name="what-is-single-sign-on-sso"></a>Co je jednotné přihlašování (SSO)?

Jednotné přihlašování znamená, že se uživatel nemusí přihlašovat ke každé aplikaci, kterou používají. Uživatel se přihlásí jednou a přihlašovací údaje se používají i pro jiné aplikace.

Pokud jste koncový uživatel, pravděpodobně nezáleží na podrobnostech jednotného přihlašování. Chcete používat jenom aplikace, které vám pomůžou být produktivní bez nutnosti zadávat heslo. Své aplikace můžete najít na adrese: https://myapps.microsoft.com .
 
Pokud jste správcem nebo IT specialistem, přečtěte si článek, kde najdete další informace o tom, jak je jednotné přihlašování v Azure implementované.

## <a name="single-sign-on-basics"></a>Základy jednotného přihlašování
Jednotné přihlašování poskytuje obří přestupnější informace o tom, jak se uživatelé přihlásí a používají aplikace. Systémy ověřování založené na jednotném přihlašování se často nazývají moderní ověřování. Pokud chcete pochopit, co je možné jednotné přihlašování, podívejte se na toto video.
> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]


## <a name="choosing-a-single-sign-on-method"></a>Výběr metody jednotného přihlašování

- **S jednotným přihlašováním**se uživatelé přihlásí jednou z účtu pro přístup k zařízením připojeným k doméně, prostředkům společnosti, aplikacím SaaS (software jako služba) a webovým aplikacím. Po přihlášení může uživatel spouštět aplikace z portálu Office 365 nebo z přístupového panelu Azure AD Mojeapl. Správci mohou centralizovat správu uživatelských účtů a automaticky přidávat nebo odebírat přístup uživatelů k aplikacím na základě členství ve skupinách.

- **Bez jednotného přihlašování**si uživatelé musí pamatovat hesla pro konkrétní aplikace a přihlašovat se ke každé aplikaci. Pracovníci IT potřebují vytvořit a aktualizovat uživatelské účty pro každou aplikaci, například Office 365, box a Salesforce. Uživatelé si musí pamatovat hesla a zasílat čas na přihlášení ke každé aplikaci.

Existuje několik způsobů, jak nakonfigurovat aplikaci pro jednotné přihlašování. Výběr metody jednotného přihlašování závisí na tom, jak je aplikace nakonfigurovaná pro ověřování.

- Cloudové aplikace můžou používat metody OpenID Connect, OAuth, SAML, založené na heslech, propojené nebo zakázané pro jednotné přihlašování. 
- Místní aplikace můžou používat metody jednotného přihlašování založené na heslech, integrovaném ověřování systému Windows, na základě hlaviček, propojených nebo zakázaných metod. Místní volby fungují, když jsou aplikace nakonfigurované pro proxy aplikace.

Tento vývojový diagram vám pomůže určit, která metoda jednotného přihlašování je pro vaši situaci nejvhodnější.

![Rozhodovací vývojový diagram pro metodu jednotného přihlašování](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

Následující tabulka shrnuje metody jednotného přihlašování a odkazy na další podrobnosti.

| Metoda jednotného přihlašování | Typy aplikací | Kdy je použít |
| :------ | :------- | :----- |
| [OpenID Connect a OAuth](#openid-connect-and-oauth) | Cloud a místní | Při vývoji nové aplikace použijte OpenID Connect a OAuth. Tento protokol zjednodušuje konfiguraci aplikace, nabízí snadno použitelné sady SDK a umožňuje vaší aplikaci používat MS Graph.
| [SAML](#saml-sso) | Cloud a místní | Pokud je to možné, vyberte pro existující aplikace, které nepoužívají OpenID Connect nebo OAuth, možnost SAML. SAML funguje pro aplikace, které se ověřují pomocí některého z protokolů SAML.|
| [Založené na heslech](#password-based-sso) | Cloud a místní | Pokud se aplikace ověřuje pomocí uživatelského jména a hesla, vyberte možnost založené na hesle. Jednotné přihlašování založené na heslech umožňuje zabezpečené ukládání hesel aplikací a přehrávání pomocí rozšíření webového prohlížeče nebo mobilní aplikace. Tato metoda používá existující proces přihlášení, který je k dispozici v aplikaci, ale umožňuje správcům spravovat hesla. |
| [Spojeného](#linked-sign-on) | Cloud a místní | Vyberte možnost propojené přihlašování, pokud je aplikace nakonfigurována pro jednotné přihlašování v jiné službě zprostředkovatele identity. Tato možnost nepřidá do aplikace jednotné přihlašování. Aplikace však může již mít jednotné přihlašování implementované pomocí jiné služby, například Active Directory Federation Services (AD FS).|
| [Disabled](#disabled-sso) (Zakázáno) | Cloud a místní | Vyberte zakázané jednotné přihlašování, když aplikace není připravená na konfiguraci jednotného přihlašování. Tento režim je při vytváření aplikace výchozí.|
| [Integrované ověřování systému Windows (IWA)](#integrated-windows-authentication-iwa-sso) | pouze místní | Pro aplikace, které používají [integrované ověřování systému Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)nebo aplikace pracující s deklaracemi, vyberte IWA jednotné přihlašování. Pro IWA konektory proxy aplikací používají k ověřování uživatelů v aplikaci omezené delegování (KCD) protokolu Kerberos. |
| [Na základě hlaviček](#header-based-sso) | pouze místní | Použijte jednotné přihlašování založené na hlavičkách, když aplikace používá hlavičky pro ověřování. Jednotné přihlašování založené na hlavičkách vyžaduje PingAccess pro Azure AD. Proxy aplikací používá k ověření uživatele službu Azure AD a předává přenos prostřednictvím služby konektoru.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect a OAuth

Při vývoji nových aplikací použijte moderní protokoly jako OpenID Connect a OAuth, abyste dosáhli nejlepšího jednotného přihlašování pro vaši aplikaci napříč různými platformami zařízení. OAuth umožňuje uživatelům nebo správcům [udělit souhlas](configure-user-consent.md) s chráněnými prostředky, jako je [Microsoft Graph](/graph/overview). Nabízíme snadnou přípravu [sad SDK](../develop/reference-v2-libraries.md) pro vaši aplikaci a navíc je vaše aplikace připravená k použití [Microsoft Graph](/graph/overview).

Další informace:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Příručka pro vývojáře Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>SAML SSO

Pomocí **jednotného přihlašování SAML**Azure AD ověřuje aplikace pomocí účtu Azure AD uživatele. Azure AD komunikuje s přihlašovacími informacemi k aplikaci prostřednictvím protokolu připojení. Pomocí jednotného přihlašování založené na SAML můžete mapovat uživatele na konkrétní aplikační role na základě pravidel, která definujete v deklaracích SAML.

Vyberte jednotné přihlašování založené na SAML, když je aplikace podporuje.

Jednotné přihlašování založené na SAML je podporované u aplikací, které používají některý z těchto protokolů:

- SAML 2.0
- WS-Federation

Konfigurace aplikace SaaS pro jednotné přihlašování založené na SAML najdete v tématu [Konfigurace jednotného přihlašování založené na SAML](configure-saml-single-sign-on.md). Mnoho aplikací SaaS (software jako služba) navíc obsahuje [kurz specifický pro aplikaci](../saas-apps/tutorial-list.md) , který vás provede konfigurací jednotného přihlašování založeného na SAML.

Pokud chcete nakonfigurovat aplikaci pro WS-Federation, postupujte podle stejných pokynů ke konfiguraci aplikace pro jednotné přihlašování založené na SAML. V kroku konfigurace aplikace tak, aby používala Azure AD, budete muset nahradit přihlašovací adresu URL Azure AD pro koncový bod WS-Federation `https://login.microsoftonline.com/<tenant-ID>/wsfed` .

Pokud chcete nakonfigurovat místní aplikaci pro jednotné přihlašování založené na SAML, přečtěte si téma [jednotné přihlašování SAML pro místní aplikace s proxy aplikací](application-proxy-configure-single-sign-on-on-premises-apps.md).

Další informace o protokolu SAML najdete v tématu [protokol SAML jednotného přihlašování](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Jednotné přihlašování založené na heslech

Při přihlašování založeném na hesle se uživatelé přihlásí k aplikaci pomocí uživatelského jména a hesla při prvním přístupu k ní. Po prvním přihlášení Azure AD zadá uživatelské jméno a heslo k aplikaci.

Jednotné přihlašování založené na heslech používá stávající proces ověřování, který je k dispozici v aplikaci. Když pro aplikaci povolíte jednotné přihlašování k heslům, Azure AD shromáždí a bezpečně ukládá uživatelská jména a hesla pro aplikaci. Přihlašovací údaje uživatele jsou uloženy v zašifrovaném stavu v adresáři.

Vyberte jednotné přihlašování založené na heslech, když:

- Aplikace nepodporuje protokol jednotného přihlašování SAML.
- Aplikace se ověřuje pomocí uživatelského jména a hesla místo přístupových tokenů a hlaviček.

>[!NOTE]
>U jednotného přihlašování založeného na heslech nemůžete použít zásady podmíněného přístupu ani službu Multi-Factor Authentication.

Jednotné přihlašování založené na heslech se podporuje pro všechny cloudové aplikace, které mají přihlašovací stránku založenou na jazyce HTML. Uživatel může použít kterýkoli z následujících prohlížečů:

- Internet Explorer 11 v systému Windows 7 nebo novějším
   > [!NOTE]
   > Internet Explorer má omezené podpory a už nepřijímá nové aktualizace softwaru. Microsoft Edge je doporučený prohlížeč.

- Microsoft Edge ve Windows 10 výročí Edition nebo novějších verzích
- Microsoft Edge pro iOS a Android
- Intune Managed Browser
- Chrome ve Windows 7 nebo novějším a na macOS X nebo novějším
- Firefox 26,0 nebo novější v systému Windows XP SP2 nebo novějším a na macOS X 10,6 nebo novějším

Pokud chcete nakonfigurovat cloudovou aplikaci pro jednotné přihlašování založené na heslech, přečtěte si téma [Konfigurace jednotného přihlašování pomocí hesla](configure-password-single-sign-on-non-gallery-applications.md).

Pokud chcete nakonfigurovat místní aplikaci pro jednotné přihlašování prostřednictvím proxy aplikací, přečtěte si téma [trezor hesel pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-password-vaulting.md) .

### <a name="how-authentication-works-for-password-based-sso"></a>Jak ověřování funguje pro jednotné přihlašování založené na heslech

K ověření uživatele v aplikaci Azure AD načte přihlašovací údaje uživatele z adresáře a zadá je do přihlašovací stránky aplikace.  Azure AD bezpečně předává přihlašovací údaje uživatele prostřednictvím rozšíření webového prohlížeče nebo mobilní aplikace. Tento proces umožňuje správci spravovat přihlašovací údaje uživatele a nepožaduje, aby si uživatelé zapamatovali své heslo.

> [!IMPORTANT]
> Přihlašovací údaje jsou při automatizovaném procesu přihlašování zakódovány uživatelem. Přihlašovací údaje se ale zjišťují pomocí nástrojů pro ladění na webu. Uživatelé a správci musí dodržovat stejné zásady zabezpečení, jako kdyby zadali pověření přímo uživatelem.

### <a name="managing-credentials-for-password-based-sso"></a>Správa přihlašovacích údajů pro jednotné přihlašování založené na heslech

Hesla pro jednotlivé aplikace může spravovat správce Azure AD nebo uživatelé.

Když správce Azure AD spravuje přihlašovací údaje:  

- Uživatel není muset resetovat ani pamatovat uživatelské jméno a heslo. Uživatel má přístup k aplikaci kliknutím na panel přístupového panelu nebo prostřednictvím poskytnutého odkazu.
- Správce může provádět úlohy správy s přihlašovacími údaji. Správce může například aktualizovat přístup k aplikaci podle členství ve skupině uživatelů a podle stavu zaměstnanců.
- Správce může použít přihlašovací údaje správce k poskytnutí přístupu k aplikacím sdíleným mezi mnoha uživateli. Správce může například udělit všem uživatelům, kteří mají přístup k aplikaci, aby měli přístup k aplikaci pro sdílení přes sociální média nebo dokumentů.

Když koncový uživatel spravuje přihlašovací údaje:

- Uživatelé mohou spravovat svá hesla jejich aktualizací nebo jejich odstraněním podle potřeby.
- Správci stále můžou pro aplikaci nastavit nové přihlašovací údaje.

## <a name="linked-sign-on"></a>Propojené přihlašování
Díky připojenému přihlašování může Azure AD poskytovat jednotné přihlašování k aplikaci, která je už nakonfigurovaná pro jednotné přihlašování v jiné službě. Propojená aplikace se může zdát koncovým uživatelům na portálu Office 365 nebo na portálu Azure AD Mojeapl. Uživatel může třeba spustit aplikaci, která je nakonfigurovaná pro jednotné přihlašování v Active Directory Federation Services (AD FS) 2,0 (AD FS) na portálu Office 365. K dispozici jsou také další sestavy pro propojené aplikace spouštěné z portálu Office 365 nebo z portálu Azure AD Mojeapl. Pokud chcete nakonfigurovat aplikaci pro propojené přihlašování, přečtěte si téma [Konfigurace propojených přihlášení](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Propojené přihlašování pro migraci aplikace

Díky připojenému přihlašování můžete zajistit konzistentní uživatelské prostředí při migraci aplikací v časovém intervalu. Pokud migrujete aplikace na Azure Active Directory, můžete k rychlému publikování odkazů na všechny aplikace, které máte v úmyslu migrovat, použít propojené přihlašování.  Uživatelé můžou najít všechny odkazy na [portálu MyApp](../user-help/active-directory-saas-access-panel-introduction.md) nebo ve [spouštěči aplikací Office 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Uživatelé nebudou znát přístup k propojené aplikaci nebo migrované aplikaci.  

Jakmile se uživatel s propojenou aplikací ověří, musí se vytvořit záznam účtu, aby byl koncovým uživatelům poskytnutý přístup s jednotným přihlašováním. K tomu, aby se tento záznam účtu mohl vyskytnout automaticky, nebo ho může vyskytnout správce ručně.

>[!NOTE]
>Pro propojenou aplikaci nemůžete použít zásady podmíněného přístupu ani službu Multi-Factor Authentication. Je to proto, že propojená aplikace neposkytuje možnosti jednotného přihlašování prostřednictvím služby Azure AD. Když nakonfigurujete propojenou aplikaci, stačí přidat odkaz, který se zobrazí ve Spouštěči aplikací nebo v portálu Mojeapl. 

## <a name="disabled-sso"></a>Zakázané jednotné přihlašování

Režim zakázané znamená, že pro aplikaci se nepoužívá jednotné přihlašování. Když je jednotné přihlašování zakázané, můžou se uživatelé muset dvakrát ověřit. Nejdřív se uživatelé ověřují ve službě Azure AD a pak se přihlásí k aplikaci.

Použít zakázaný režim jednotného přihlašování:

- Pokud nejste připraveni k integraci této aplikace s jednotným přihlašováním Azure AD nebo
- Pokud testujete jiné aspekty aplikace nebo
- Jako úroveň zabezpečení pro místní aplikaci, která nevyžaduje ověřování uživatelů. V případě zakázání je uživatel musí ověřit.

Všimněte si, že pokud jste nakonfigurovali aplikaci pro jednotné přihlašování založené na SAML pomocí protokolu SAML a změníte režim jednotného přihlašování (SSO), zabráníte uživatelům v přihlašování k aplikaci mimo portál MyApp. Chcete-li toho dosáhnout, je nutné [zakázat možnost přihlášení uživatelů](disable-user-sign-in-portal.md) .

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrované ověřování systému Windows (IWA) SSO

[Proxy aplikací](application-proxy.md) poskytuje jednotné přihlašování (SSO) k aplikacím, které používají [integrované ověřování systému Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)nebo aplikace pracující s deklaracemi. Pokud vaše aplikace používá IWA, proxy aplikací se ověří v aplikaci pomocí omezeného delegování protokolu Kerberos (KCD). U aplikací pracujících s deklaracemi, které důvěřují Azure Active Directory jednotné přihlašování, protože uživatel byl už ověřený pomocí Azure AD.

Vyberte možnost integrovaný režim jednotného přihlašování Windows, abyste zajistili jednotné přihlašování k místní aplikaci, která se ověřuje pomocí IWA.

Pokud chcete nakonfigurovat místní aplikaci pro IWA, přečtěte si téma [omezené delegování protokolu Kerberos pro jednotné přihlašování k vašim aplikacím pomocí proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Jak jednotné přihlašování pomocí KCD funguje
Tento diagram vysvětluje tok, když uživatel přistupuje k místní aplikaci, která používá IWA.

![Diagram toku Microsoft Azure AD ověřování](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Uživatel zadá adresu URL pro přístup k místní aplikaci prostřednictvím proxy aplikace.
1. Proxy aplikace přesměruje požadavek na služby ověřování Azure AD na předběžné ověření. V tomto okamžiku Azure AD aplikuje jakékoli použitelné zásady ověřování a autorizace, jako je například vícefaktorové ověřování. Pokud je uživatel ověřený, Azure AD vytvoří token a odešle ho uživateli.
1. Uživatel předává token do proxy aplikace.
1. Proxy aplikace ověří token a načte hlavní název uživatele (UPN) z tokenu. Pak odešle požadavek, hlavní název uživatele (UPN) a hlavní název služby (SPN) do konektoru prostřednictvím dvojího ověřeného zabezpečeného kanálu.
1. Konektor používá vyjednávání omezeného delegování protokolu Kerberos (KCD) s místní službou AD a zosobňuje uživatele k získání tokenu protokolu Kerberos do aplikace.
1. Služba Active Directory odesílá token protokolu Kerberos pro aplikaci konektoru.
1. Konektor odešle původní požadavek na aplikační server pomocí tokenu protokolu Kerberos, který přijal ze služby AD.
1. Aplikace pošle odpověď konektoru, která se pak vrátí službě proxy aplikací a nakonec uživateli.

## <a name="header-based-sso"></a>Jednotné přihlašování založené na hlavičkách

Jednotné přihlašování založené na hlavičkách funguje pro aplikace, které k ověřování používají hlavičky protokolu HTTP. Tato metoda přihlašování používá službu ověřování třetí strany s názvem PingAccess. Uživatel se musí pouze ověřit ve službě Azure AD.

V případě, že je pro aplikaci nakonfigurován proxy aplikace a PingAccess, vyberte jednotné přihlašování založené na hlavičkách.

Pokud chcete nakonfigurovat ověřování na základě hlaviček, přečtěte si téma [ověřování na základě hlaviček pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-with-ping-access.md).

### <a name="what-is-pingaccess-for-azure-ad"></a>Co je PingAccess pro Azure AD?

Uživatelé můžou pomocí PingAccess pro Azure AD získat přístup k aplikacím, které k ověřování používají hlavičky, a jejich jednotné přihlašování. Proxy aplikace tyto aplikace považují za jiné, a to pomocí Azure AD k ověřování přístupu a následnému předávání provozu prostřednictvím služby konektoru. Po ověření dojde v PingAccess službě k přenosu tokenu služby Azure AD Access do formátu hlaviček, který je odeslán do aplikace.

Pokud se přihlásíte k používání podnikových aplikací, nebudou si uživatelé moci všimnout žádné jiné. Můžou pořád pracovat odkudkoli na jakémkoli zařízení. Konektor proxy aplikací směruje vzdálený provoz do všech aplikací a bude pokračovat ve vyrovnávání zatížení automaticky.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Návody získat licenci pro PingAccess?

Vzhledem k tomu, že tento scénář je nabízený prostřednictvím partnerství mezi Azure AD a PingAccess, potřebujete licence pro obě služby. Azure AD Premium předplatných ale zahrnuje základní licenci PingAccess, která zahrnuje až 20 aplikací. Pokud potřebujete publikovat více než 20 aplikací založených na hlavičkách, můžete získat další licenci od PingAccess.

Další informace najdete v tématu [Azure Active Directory edice](../fundamentals/active-directory-whatis.md).

## <a name="next-steps"></a>Další kroky
* [Série rychlý Start při správě aplikací](view-applications-portal.md)
