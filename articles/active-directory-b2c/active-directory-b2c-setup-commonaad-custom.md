---
title: Nastavení přihlášení pro více tenantů Azure AD pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Přidejte poskytovatele identit Azure AD s více klienty pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: abac9152ff71c8fbf4ed53bfd663d29616382cf9
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947774"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavení přihlášení pro více tenantů Azure Active Directory používání vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení uživatelům pomocí koncového bodu s více klienty pro Azure Active Directory (Azure AD) pomocí [vlastních zásad](active-directory-b2c-overview-custom.md) v Azure AD B2C. To umožňuje uživatelům z více tenantů Azure AD přihlásit se pomocí Azure AD B2C, aniž byste museli konfigurovat poskytovatele identity pro každého tenanta. Hostující členové v některém z těchto tenantů **se** však nebudou moci přihlásit. V takovém případě musíte [každého tenanta nakonfigurovat samostatně](active-directory-b2c-setup-aad-custom.md).

## <a name="prerequisites"></a>Předpoklady

Proveďte kroky v části Začínáme [s vlastními zásadami v Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registrace aplikace

Pokud chcete povolit přihlášení pro uživatele z konkrétní organizace Azure AD, musíte zaregistrovat aplikaci v rámci tenanta organizace Azure AD.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje váš tenant organizace Azure AD (například contoso.com). V horní nabídce vyberte **Filtr adresář + odběr** a pak zvolte adresář, který obsahuje vašeho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
1. Vyberte **Nová registrace**.
1. Zadejte **název** vaší aplikace. Například, `Azure AD B2C App`.
1. Pro tuto aplikaci vyberte **účty v libovolném organizačním adresáři** .
1. Pro **identifikátor URI přesměrování**přijměte hodnotu **Web**a zadejte následující adresu URL do všech malých písmen, kde se `your-B2C-tenant-name` nahradí názvem vašeho tenanta Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Například, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Vyberte **Zaregistrovat**. Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku.
1. Vyberte **certifikáty & tajných**kódů a pak vyberte **nový tajný klíč klienta**.
1. Zadejte **Popis** tajného kódu, vyberte vypršení platnosti a pak vyberte **Přidat**. Poznamenejte si **hodnotu** tajného kódu pro použití v pozdějším kroku.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Je potřeba uložit klíč aplikace, který jste vytvořili v tenantovi Azure AD B2C.

1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte **Filtr adresář + odběr** a pak zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady**vyberte **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti**vyberte možnost `Manual`.
1. Zadejte **název** klíče zásad. Například, `AADAppSecret`.  `B2C_1A_` předpony se automaticky přidají do názvu klíče při jeho vytvoření, takže jeho odkaz v XML v následující části je *B2C_1A_AADAppSecret*.
1. Do **tajného klíče**zadejte tajný klíč klienta, který jste si poznamenali dříve.
1. Pro **použití klíče**vyberte `Signature`.
1. Vyberte **Create** (Vytvořit).

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete, aby se uživatelé přihlásili pomocí Azure AD, je třeba definovat Azure AD jako zprostředkovatele deklarací identity, se kterým Azure AD B2C může komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Službu Azure AD můžete definovat jako zprostředkovatele deklarací přidáním služby Azure AD do prvku **ClaimsProvider** v souboru rozšíření zásady.

1. Otevřete soubor *TrustFrameworkExtensions. XML* .
1. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
1. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. V rámci elementu **ClaimsProvider** aktualizujte hodnotu pro **doménu** na jedinečnou hodnotu, která se dá použít k odlišení od jiných zprostředkovatelů identity.
1. V rámci elementu **TechnicalProfile** aktualizujte hodnotu **displayname**, například `Contoso Employee`. Tato hodnota se zobrazí na přihlašovací stránce na tlačítku pro přihlášení.
1. Nastavte **client_id** na ID aplikace víceklientské aplikace Azure AD, kterou jste si zaregistrovali dříve.
1. V části **CryptographicKeys**aktualizujte hodnotu **StorageReferenceId** na název klíče zásad, který jste vytvořili dříve. Například, `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Omezení přístupu

> [!NOTE]
> Použití `https://login.microsoftonline.com/` jako hodnota **ValidTokenIssuerPrefixes** umožňuje všem uživatelům Azure AD přihlašovat se k vaší aplikaci.

Musíte aktualizovat seznam platných vystavitelů tokenů a omezit přístup k určitému seznamu uživatelů klienta služby Azure AD, kteří se můžou přihlásit.

Pokud chcete získat hodnoty, podívejte se na metadata zjišťování OpenID Connect pro každé klienty Azure AD, ze kterých se chcete přihlašovat pomocí uživatelů. Formát adresy URL metadat je podobný `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`, kde `your-tenant` je váš název tenanta Azure AD. Například:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

U každého tenanta Azure AD, který by se měl použít k přihlášení, proveďte tyto kroky:

1. Otevřete prohlížeč a pro tenanta použijte adresu URL metadat OpenID Connect. Vyhledejte objekt **vystavitele** a zaznamenejte jeho hodnotu. Měl by vypadat podobně jako `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`.
1. Zkopírujte a vložte hodnotu do klíče **ValidTokenIssuerPrefixes** . Více vystavitelů oddělte čárkou. Příklad se dvěma vystaviteli se zobrazí v předchozí ukázce `ClaimsProvider` XML.

### <a name="upload-the-extension-file-for-verification"></a>Nahrajte soubor rozšíření pro ověření.

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C vědět, jak komunikovat s adresáři služby Azure AD. Zkuste nahrát soubor s příponou zásady jenom tak, aby se ověřilo, že zatím nemá žádné problémy.

1. Na stránce **vlastní zásady** ve vašem tenantovi Azure AD B2C vyberte **Odeslat zásadu**.
2. Pokud existuje, zapněte **zásadu přepsat**a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions. XML* .
3. Vyberte **Nahrát**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli je poskytovatel identity nastavený, ale není k dispozici na žádném z obrazovek pro registraci a přihlašování. Aby byl k dispozici, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte, aby měl také poskytovatele identity služby Azure AD.

1. Otevřete soubor *TrustFrameworkBase. XML* z úvodní sady.
2. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"`.
3. Otevřete *soubor TrustFrameworkExtensions. XML* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
4. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
5. Přejmenujte ID cesty pro uživatele. Například, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Zobrazit tlačítko

Element **claimsproviderselection.** se podobá tlačítku poskytovatele identity na obrazovce pro registraci a přihlašování. Pokud přidáte element **claimsproviderselection.** pro Azure AD, zobrazí se nové tlačítko, když se uživatel na stránce zařadí.

1. Vyhledejte element **OrchestrationStep** , který obsahuje `Order="1"` v cestě uživatele, kterou jste vytvořili v *souboru TrustFrameworkExtensions. XML*.
1. Pod **ClaimsProviderSelects**přidejte následující element. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojit tlačítko s akcí

Teď, když máte tlačítko na místě, musíte ho propojit s akcí. Tato akce je v tomto případě určena pro Azure AD B2C ke komunikaci se službou Azure AD za účelem získání tokenu. Propojte tlačítko s akcí tak, že propojíte technický profil pro poskytovatele deklarací identity Azure AD.

1. Najděte **OrchestrationStep** , který obsahuje `Order="2"` v cestě uživatele.
2. Přidejte následující prvek **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro **ID** , které jste použili pro **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na **ID** technického profilu, který jste vytvořili dříve. Například, `Common-AAD`.

3. Uložte soubor *TrustFrameworkExtensions. XML* a znovu ho nahrajte k ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete v tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést, chcete-li vytvořit testovací aplikaci, pokud jste tak již neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili:

1. Vytvořte kopii *SignUpOrSignIn. XML* v pracovním adresáři a přejmenujte ji. Přejmenujte ho například na *SignUpSignContoso. XML*.
1. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInContoso`.
1. Aktualizujte hodnotu **PUBLICPOLICYURI** identifikátorem URI pro zásadu. Například, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídala ID cesty uživatele, kterou jste vytvořili dříve. Například *SignUpSignInContoso*.
1. Uložte změny a odešlete soubor.
1. V části **vlastní zásady**vyberte v seznamu novou zásadu.
1. V rozevíracím seznamu **Vybrat aplikaci** vyberte aplikaci Azure AD B2C, kterou jste vytvořili dříve. Například *testapp1*.
1. Zkopírujte **koncový bod spustit nyní** a otevřete ho v privátním okně prohlížeče, například v anonymním režimu v Google Chrome nebo v okně InPrivate na Microsoft Edge. Otevření v privátním okně prohlížeče vám umožní testovat celou cestu uživatele, protože nepoužívá žádná z aktuálně uložených přihlašovacích údajů Azure AD.
1. Vyberte tlačítko Přihlásit se k Azure AD, například *Zaměstnanec společnosti Contoso*, a potom zadejte přihlašovací údaje uživatele v jednom z vašich tenantů organizace Azure AD. Zobrazí se výzva k autorizaci aplikace a zadání informací pro váš profil.

Pokud je proces přihlášení úspěšný, je váš prohlížeč přesměrován na `https://jwt.ms`, který zobrazuje obsah tokenu vráceného Azure AD B2C.

Pokud chcete otestovat možnost přihlašování s více klienty, proveďte poslední dva kroky pomocí přihlašovacích údajů uživatele, který existuje v jiném tenantovi služby Azure AD.

## <a name="next-steps"></a>Další kroky

Při práci s vlastními zásadami můžete někdy potřebovat další informace při řešení potíží se zásadami během jejího vývoje.

Abychom vám pomohli diagnostikovat problémy, můžete dočasně umístit zásady do režimu pro vývojáře a shromažďovat protokoly pomocí Application Insights Azure. Zjistěte, jak [Azure Active Directory B2C: shromažďování protokolů](active-directory-b2c-troubleshoot-custom.md).
