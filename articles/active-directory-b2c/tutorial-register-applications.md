---
title: 'Kurz: registrace aplikace'
titleSuffix: Azure AD B2C
description: Naučte se, jak zaregistrovat webovou aplikaci v Azure Active Directory B2C pomocí Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c3be9136f260d73647e144d4d00bb58ce5e6774d
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84672608"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>Kurz: registrace webové aplikace v Azure Active Directory B2C

Než můžou vaše [aplikace](application-types.md) pracovat s Azure Active Directory B2C (Azure AD B2C), musí být zaregistrované v tenantovi, které spravujete. V tomto kurzu se dozvíte, jak zaregistrovat webovou aplikaci pomocí Azure Portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Registrace webové aplikace
> * Vytvoření tajného klíče klienta

Pokud místo toho používáte nativní aplikaci (například iOS, Android, Mobile & Desktop), zjistěte, [jak zaregistrovat nativní klientskou aplikaci](add-native-application.md).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

Pokud jste ještě nevytvořili vlastního [tenanta Azure AD B2C](tutorial-create-tenant.md), vytvořte ho hned teď. Můžete použít stávajícího klienta Azure AD B2C.

## <a name="register-a-web-application"></a>Registrace webové aplikace

K registraci aplikace ve vašem tenantovi Azure AD B2C můžete využít nové jednotné prostředí pro **Registrace aplikací** nebo naše starší verze **aplikací (zastaralé)** . [Další informace o novém prostředí](https://aka.ms/b2cappregtraining).

#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/)

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací**a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *WebApp1*.
1. V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři nebo jakémkoli poskytovateli identity. Pro ověřování uživatelů pomocí Azure AD B2C**.
1. V části **identifikátor URI pro přesměrování**vyberte **Web**a potom zadejte `https://jwt.ms` do textového pole Adresa URL.

    Identifikátor URI přesměrování je koncový bod, na který se uživatel pošle prostřednictvím autorizačního serveru (Azure AD B2C, v tomto případě) po dokončení jeho interakce s uživatelem a ke kterému se po úspěšné autorizaci pošle přístupový token nebo autorizační kód. V produkční aplikaci je obvykle veřejně přístupný koncový bod, ve kterém je vaše aplikace spuštěná, třeba `https://contoso.com/auth-response` . Pro účely testování, jako je tento kurz, můžete ho nastavit na `https://jwt.ms` , webovou aplikaci ve vlastnictví společnosti Microsoft, která zobrazuje Dekódovatelné obsah tokenu (obsah tokenu nikdy nezůstane v prohlížeči). Během vývoje aplikace můžete přidat koncový bod, ve kterém vaše aplikace naslouchá místně, například `https://localhost:5000` . V registrovaných aplikacích můžete kdykoli přidat a změnit identifikátory URI pro přesměrování.

    Pro identifikátory URI přesměrování platí následující omezení:

    * Adresa URL odpovědi musí začínat schématem `https` .
    * Adresa URL odpovědi rozlišuje velká a malá písmena. Jeho velikost se musí shodovat s písmenem adresy URL vaší běžící aplikace. Například pokud vaše aplikace obsahuje jako součást cesty `.../abc/response-oidc` , nezadávejte `.../ABC/response-oidc` v adrese URL odpovědi. Vzhledem k tomu, že webový prohlížeč považuje cesty jako rozlišování velkých a malých písmen, mohou být soubory cookie přidružené k `.../abc/response-oidc` vyloučeny při přesměrování na neshodnou `.../ABC/response-oidc` adresu URL.

1. V části **oprávnění**zaškrtněte políčko *udělit souhlas správcům oprávnění OpenID a offline_access* .
1. Vyberte **Zaregistrovat**.

Po dokončení registrace aplikace povolte tok implicitního udělení:

1. V části **Spravovat**vyberte **ověřování**.
1. V části **implicitní udělení**vyberte zaškrtávací políčka **přístupové tokeny** i **tokeny ID** .
1. Vyberte **Uložit**.

#### <a name="applications-legacy"></a>[Aplikace (starší verze)](#tab/applications-legacy/)

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikace (starší verze)** a pak vyberte **Přidat**.
1. Zadejte název aplikace. Například *WebApp1*.
1. V případě **zahrnutí webové aplikace/webového rozhraní API** a **Povolení implicitního toku**vyberte **Ano**.
1. V případě **adresy URL odpovědi**Zadejte koncový bod, kde Azure AD B2C by měl vracet jakékoli tokeny, které vaše aplikace požaduje. Například můžete nastavit, aby naslouchal místně na `https://localhost:44316` . Pokud číslo portu ještě neznáte, můžete zadat zástupnou hodnotu a později ji změnit.

    Pro účely testování, jako je tento kurz, ho můžete nastavit na, `https://jwt.ms` který zobrazuje obsah tokenu pro kontrolu. Pro tento kurz nastavte **adresu URL odpovědi** na `https://jwt.ms` .

    Pro adresy URL odpovědí platí následující omezení:

    * Adresa URL odpovědi musí začínat schématem `https` .
    * Adresa URL odpovědi rozlišuje velká a malá písmena. Jeho velikost se musí shodovat s písmenem adresy URL vaší běžící aplikace. Například pokud vaše aplikace obsahuje jako součást cesty `.../abc/response-oidc` , nezadávejte `.../ABC/response-oidc` v adrese URL odpovědi. Vzhledem k tomu, že webový prohlížeč považuje cesty jako rozlišování velkých a malých písmen, mohou být soubory cookie přidružené k `.../abc/response-oidc` vyloučeny při přesměrování na neshodnou `.../ABC/response-oidc` adresu URL.

1. Výběrem **vytvořit** dokončete registraci aplikace.

* * *

## <a name="create-a-client-secret"></a>Vytvoření tajného klíče klienta

Pokud vaše aplikace vyměňuje autorizační kód pro přístupový token, je nutné vytvořit tajný klíč aplikace.


#### <a name="app-registrations"></a>[Registrace aplikací](#tab/app-reg-ga/)

1. Na stránce **Azure AD B2C-registrace aplikací** vyberte aplikaci, kterou jste vytvořili, například *WebApp1*.
1. V části **Spravovat**vyberte **certifikáty & tajných**kódů.
1. Vyberte **Nový tajný klíč klienta**.
1. Do pole **Popis** zadejte popis tajného klíče klienta. Například *clientsecret1*.
1. Pod položkou **konec platnosti**vyberte dobu, po kterou je tajný kód platný, a pak vyberte **Přidat**.
1. Poznamenejte si **hodnotu**tajného klíče. Tuto hodnotu použijete jako tajný klíč aplikace v kódu vaší aplikace.

#### <a name="applications-legacy"></a>[Aplikace (starší verze)](#tab/applications-legacy/)

1. Na stránce **Azure AD B2C-aplikace** vyberte aplikaci, kterou jste vytvořili, například *WebApp1*.
1. Vyberte **klíče** a pak vyberte **vygenerovat klíč**.
1. Vyberte **Save (Uložit** ) a zobrazte si klíč. Poznamenejte si hodnotu **Klíč aplikace**. Tuto hodnotu použijete jako tajný klíč aplikace v kódu vaší aplikace.

* * *

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Registrace webové aplikace
> * Vytvoření tajného klíče klienta

V dalším kroku se dozvíte, jak vytvořit toky uživatelů, aby se uživatelé mohli zaregistrovat, přihlašovat a spravovat své profily.

> [!div class="nextstepaction"]
> [Vytváření toků uživatelů v Azure Active Directory B2C >](tutorial-create-user-flows.md)
