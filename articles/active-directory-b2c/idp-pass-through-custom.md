---
title: Předat token přístupu pomocí vlastních zásad do vaší aplikace v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak lze předat přístupového tokenu pro zprostředkovatele identity OAuth 2.0 jako deklaraci identity pomocí vlastních zásad do vaší aplikace v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d752af1860d76e59df045907c11d16b4e1d20b0c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702938"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Předat token přístupu pomocí vlastních zásad do vaší aplikace v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A [vlastní zásady](active-directory-b2c-get-started-custom.md) v Azure Active Directory (Azure AD) B2C poskytuje uživatelům vaší aplikace příležitost k registraci nebo přihlášení pomocí zprostředkovatele identity. Pokud k tomu dojde, obdrží Azure AD B2C [přístupový token](active-directory-b2c-reference-tokens.md) od zprostředkovatele identity. Azure AD B2C používá tento token k načtení informací o uživateli. Můžete přidat že typ deklarace identity a výstupní deklaraci identity pro vaše vlastní zásada vypadat předat token prostřednictvím aplikace, které zaregistrujete v Azure AD B2C. 

Azure AD B2C podporuje předávání přístupový token [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) a [OpenID Connect](active-directory-b2c-reference-oidc.md) zprostředkovatelé identity. Pro všechny ostatní poskytovatele identity se vrátí prázdná deklarace identity.

## <a name="prerequisites"></a>Požadavky

- Vlastní zásady je nakonfigurovaný pomocí zprostředkovatele identity OAuth 2.0 nebo OpenID Connect.

## <a name="add-the-claim-elements"></a>Přidání prvků deklarace identity 

1. Otevřete váš *TrustframeworkExtensions.xml* soubor a přidejte následující **typu deklarace identity** element s identifikátorem `identityProviderAccessToken` k **ClaimsSchema** element:

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Přidat **OutputClaim** elementu **technický profil** – element pro každý poskytovatel identit OAuth 2.0, kterou byste uvítali přístupový token pro. Následující příklad ukazuje prvek přidán na technický profil na Facebooku:

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Uložit *TrustframeworkExtensions.xml* souboru.
4. Otevření souboru zásad předávající strany, jako *SignUpOrSignIn.xml*a přidejte **OutputClaim** elementu **technický profil**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Uložte soubor zásad.

## <a name="test-your-policy"></a>Vaše zásada testování

Při testování aplikací v Azure AD B2C, může být užitečné mít tokenu Azure AD B2C vrátí `https://jwt.ms` být schopni zkontrolovat deklarace identity v ní.

### <a name="upload-the-files"></a>Nahrání souborů

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **architekturu rozhraní identit**.
5. Na stránce vlastní zásady, klikněte na tlačítko **nahrát zásady**.
6. Vyberte **přepsat zásady, pokud existuje**a poté vyhledejte a vyberte *TrustframeworkExtensions.xml* souboru.
7. Klikněte na **Odeslat**.
8. Opakujte kroky 5 až 7 pro souboru předávající strany, jako například *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Spustit zásady

1. Otevřete zásadu, kterou jste změnili. Například *B2C_1A_signup_signin*.
2. Pro **aplikace**, vyberte aplikace, které jste dříve zaregistrovali. Chcete-li zobrazit token v následujícím příkladu **adresy URL odpovědi** by se zobrazit `https://jwt.ms`.
3. Klikněte na **Spustit**.

    By měl vypadat podobně jako v následujícím příkladu:

    ![Dekódovaný token](./media/idp-pass-through-custom/idp-pass-through-custom-token.png)

## <a name="next-steps"></a>Další postup

Další informace o tokeny v [odkaz tokenu Azure Active Directory](active-directory-b2c-reference-tokens.md).





