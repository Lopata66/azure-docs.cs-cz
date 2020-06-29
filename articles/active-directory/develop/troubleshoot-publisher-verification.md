---
title: Řešení potíží s ověřováním vydavatele – Microsoft Identity Platform | Azure
description: Popisuje, jak řešit potíže s ověřováním vydavatele (Preview) pro platformu Microsoft Identity voláním Microsoft Graph rozhraní API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: dcdce6ee6683c5770f97f5f3dc20e1c9b409ead0
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2020
ms.locfileid: "85477035"
---
# <a name="troubleshoot-publisher-verification-preview"></a>Řešení potíží s ověřováním vydavatele (Preview)
Pokud se vám nedaří dokončit proces nebo došlo k neočekávanému chování při [ověřování vydavatele (Preview)](publisher-verification-overview.md), měli byste začít následujícími kroky, pokud obdržíte chyby nebo neočekávané chování: 

1. Zkontrolujte [požadavky](publisher-verification-overview.md#requirements) a zajistěte, aby byly splněné.

1. Přečtěte si pokyny, jak [Označit aplikaci jako Publisher jako ověřený](mark-app-as-publisher-verified.md) a zda byly všechny kroky úspěšně provedeny.

1. Projděte si seznam [běžných problémů](#common-issues).

1. Reprodukování požadavku pomocí [Průzkumníka graphu](#making-microsoft-graph-api-calls) získáte další informace a vyloučíte všechny problémy v uživatelském rozhraní.

## <a name="common-issues"></a>Běžné problémy
Níže jsou uvedeny některé běžné problémy, ke kterým může dojít během procesu. 

- **Nevím, Microsoft Partner Network ID (MPN ID), nebo neznáte, že je primárním kontaktem na účet** 
    1. Přejít na [stránku registrace MPN](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. Přihlaste se pomocí uživatelského účtu v primárním tenantovi Azure AD organizace. 
    1. Pokud účet MPN již existuje, bude rozpoznán a bude přidán k účtu. 
    1. Přejděte na [stránku profil partnera](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile) , kde se zobrazí kontakt s ID MPN a primárním účtem.

- **Nevím, kdo má globální správce Azure AD (taky označovaný jako správce společnosti nebo správce tenanta), jak je najít? Co správce aplikace nebo jiná role správce?**
    1. Přihlaste se k [portálu Azure AD](https://aad.portal.azure.com) pomocí uživatelského účtu v primárním tenantovi vaší organizace.
    1. Přejít ke [správě rolí](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. Klikněte na globální správce nebo na požadovanou roli správce.
    1. Zobrazí se seznam uživatelů s přiřazenou rolí.

- Nevím **, kdo jsou správci (e) pro svůj účet MPN** Přejděte na [stránku správy uživatelů MPN](https://partner.microsoft.com/en-us/pcv/users) a vyfiltrujte si seznam uživatelů, abyste viděli, kteří uživatelé jsou v různých rolích správce.

- **Zobrazuje se chyba oznamující, že ID programu MPN je neplatné nebo ke kterému nemám přístup.**
    1. Přejít na svůj [partnerský profil](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile) a ověřit, zda: 
        - ID MPN je správné. 
        - Neexistují žádné chyby nebo se zobrazuje zpráva "čeká na vyřízení akce" a stav ověření v souladu s platným obchodním profilem a informacemi o partnerovi znamená "autorizováno" nebo "úspěch".
    1. Přejdete na [stránku správy tenanta MPN](https://partner.microsoft.com/en-us/dashboard/account/v3/tenantmanagement) a ověřte, že je tenant, ve kterém je aplikace registrovaná, a že se k němu přihlašujete pomocí uživatelského účtu, který je na seznamu přidružených tenantů.
    1. Navštivte [stránku Správa uživatelů MPN](https://partner.microsoft.com/en-us/pcv/users) a potvrďte uživatele, ke kterému se přihlašujete, protože je to buď globální správce, správce programu MPN nebo správce účtů.

- **Když se přihlásím k portálu Azure AD, nevidím žádné registrované aplikace. Proč?** 
    Registrace aplikací mohla být vytvořena pomocí jiného uživatelského účtu nebo v jiném tenantovi. Ujistěte se, že jste přihlášeni pomocí správného účtu v tenantovi, ve kterém byly vytvořeny registrace aplikací.

- **Návody víte, kdo je vlastníkem registrace aplikace v Azure AD?** 
    Když se přihlásíte ke klientovi, ve kterém je aplikace zaregistrovaná, přejděte do okna registrace aplikací, klikněte na aplikaci a pak klikněte na vlastníci.

## <a name="making-microsoft-graph-api-calls"></a>Vytváření volání rozhraní API Microsoft Graph 

Pokud máte problém, ale nemůžete pochopit, proč na základě toho, co vidíte v uživatelském rozhraní, může být užitečné provést další řešení potíží pomocí Microsoft Graph volání k provádění stejných operací, které můžete provádět na portálu pro registraci aplikací. Během fáze Preview budou tato rozhraní API k dispozici pouze v/betam koncovém bodu služby Microsoft Graph.  

Nejjednodušší způsob, jak tyto požadavky udělat, je použití [Průzkumníka graphu](https://developer.microsoft.com/graph/graph-explorer). Můžete také zvážit další možnosti, jako je použití [post](https://www.postman.com/), nebo použití PowerShellu k [vyvolání webové žádosti](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7).  

Pomocí Microsoft Graph můžete nastavit a zrušit ověření vydavatele aplikace a po provedení jedné z těchto operací zkontrolovat výsledek. Výsledek se může zobrazit jak pro [aplikační](/graph/api/resources/application?view=graph-rest-beta) objekt, který odpovídá registraci vaší aplikace, a jakýmkoli [instančním objektům](/graph/api/resources/serviceprincipal?view=graph-rest-beta) , které z této aplikace vytvořily instance. Další informace o vztahu mezi těmito objekty naleznete v tématu: [aplikace a objekty zabezpečení služby v Azure Active Directory](app-objects-and-service-principals.md).  

Tady jsou příklady některých užitečných požadavků:  

### <a name="set-verified-publisher"></a>Nastavit ověřeného vydavatele 

Žádost

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Odpověď 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* je vaše ID MPN. 

### <a name="unset-verified-publisher"></a>Zrušit stav ověřeného vydavatele 

Požadavek:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Odpověď 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Získat ověřené informace o vydavateli z aplikace 
 
```
GET https://graph.microsoft.com/beta/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Získat informace o ověřeném vydavateli z instančního objektu 
```
GET https://graph.microsoft.com/beta/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Odkaz na chybu 

Následuje seznam možných chybových kódů, které můžete obdržet, při řešení potíží s Microsoft Graph nebo v průběhu procesu na portálu pro registraci aplikací.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess     

Zadané ID MPN ( <MPNID> ) neexistuje nebo k němu nemáte přístup. Zadejte platné ID MPN a zkuste to znovu. 

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

Zadané ID MPN ( <MPNID> ) je neplatné. Zadejte platné ID MPN a zkuste to znovu. 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

Zadané ID MPN ( <MPNID> ) je neplatné. Zadejte platné ID MPN a zkuste to znovu. 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

ID MPN ( <MPNID> ), které jste zadali, nedokončilo proces dozvíte ČSFD. Tento proces dokončete v partnerském centru a zkuste to znovu. 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

Zadané ID MPN ( <MPNID> ) je neplatné. Zadejte platné ID MPN a zkuste to znovu. 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

Zadané ID MPN ( <MPNID> ) je neplatné. Zadejte platné ID MPN a zkuste to znovu. 

### <a name="applicationnotfound"></a>ApplicationNotFound  

Cílovou aplikaci ( <AppId> ) nelze nalézt. Zadejte platné ID aplikace a zkuste to znovu. 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

Tato funkce není v tenantovi Azure AD B2C podporována. 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

Tato funkce není podporována v tenantovi ověřeném e-mailem. 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

Cílová aplikace ( <AppId> ) musí mít sadu domény vydavatele. Nastavte doménu vydavatele a zkuste to znovu. 

### <a name="publisherdomainisnotdnsverified"></a>PublisherDomainIsNotDNSVerified  

Doména vydavatele cílové aplikace () není <publisherDomain> ověřená doména v tomto tenantovi. Ověřte doménu tenanta pomocí ověřování DNS a zkuste to znovu. 

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

Doména vydavatele cílové aplikace () se <publisherDomain> neshoduje s doménou použitou k ověřování e-mailů v partnerském centru ( <pcDomain> ). Zajistěte, aby tyto domény odpovídaly, a zkuste to znovu. 

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

Nemáte oprávnění k nastavení vlastnosti ověřený vydavatel v aplikaci ( <AppId> ). 

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

ID MPN nebylo v textu žádosti zadáno nebo typ obsahu požadavku nebyl "Application/JSON". 

### <a name="msanotsupported"></a>MSANotSupported  

Tato funkce není u zákaznických účtů Microsoftu podporována. Podporují se jenom aplikace zaregistrované ve službě Azure AD pro uživatele Azure AD. 

## <a name="next-steps"></a>Další kroky

Pokud jste zkontrolovali všechny předchozí informace a stále dochází k chybě z Microsoft Graph, Shromážděte co nejvíc z následujících informací, které souvisejí s chybnou žádostí a [kontaktujte podporu Microsoftu](developer-support-help-options.md#open-a-support-request).

- Časové razítko 
- CorrelationId 
- ObjectID nebo UserPrincipalName přihlášeného uživatele 
- Identifikátor ObjectId cílové aplikace
- Identifikátor AppId cílové aplikace
- TenantId, kde je aplikace zaregistrovaná
- ID MPN
- Probíhají žádosti REST 
- Kód chyby a zpráva vracené 
