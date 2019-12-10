---
title: Přidání AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Nastavení ADFS 2016 s použitím protokolu SAML a vlastních zásad v Azure Active Directory B2C
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 202c3bfb8e9818437ff35454a1eafce008cdb00c
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948670"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Přidejte AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení k uživatelskému účtu ADFS pomocí [vlastních zásad](active-directory-b2c-overview-custom.md) v Azure Active Directory B2C (Azure AD B2C). Přihlášení povolíte přidáním [technického profilu SAML](saml-technical-profile.md) do vlastních zásad.

## <a name="prerequisites"></a>Předpoklady

- Proveďte kroky v části Začínáme [s vlastními zásadami v Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Ujistěte se, že máte přístup k souboru certifikátu. pfx s privátním klíčem. Můžete vygenerovat vlastní podepsaný certifikát a odeslat ho do Azure AD B2C. Azure AD B2C používá tento certifikát k podepsání žádosti SAML odeslané poskytovateli identity SAML.
- Aby mohl Azure přijmout heslo k souboru. pfx, musí být heslo zašifrované s možností TripleDES-SHA1 v nástroji pro export certifikátů Windows, a to na rozdíl od AES256-SHA256.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Certifikát musíte uložit do svého tenanta Azure AD B2C.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
5. Vyberte **klíče zásad** a pak vyberte **Přidat**.
6. Pro **Možnosti**vyberte možnost `Upload`.
7. Zadejte **název** klíče zásad. Například, `SamlCert`. `B2C_1A_` předpony se automaticky přidají do názvu vašeho klíče.
8. Vyhledejte a vyberte soubor Certificate. pfx s privátním klíčem.
9. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete, aby se uživatelé přihlásili pomocí účtu ADFS, musíte účet definovat jako zprostředkovatele deklarací identity, se kterým Azure AD B2C můžou komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Účet ADFS můžete definovat jako zprostředkovatele deklarací tak, že ho přidáte do prvku **ClaimsProviders** v souboru rozšíření zásady.

1. Otevřete *soubor TrustFrameworkExtensions. XML*.
2. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
3. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Nahraďte `your-ADFS-domain` názvem vaší domény ADFS a nahraďte hodnotu výstupní deklarace **identityProvider** svým DNS (libovolná hodnota, která označuje vaši doménu).
5. Uložte soubor.

### <a name="upload-the-extension-file-for-verification"></a>Nahrajte soubor rozšíření pro ověření.

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C vědět, jak komunikovat s účtem ADFS. Zkuste nahrát soubor s příponou zásady jenom tak, aby se ověřilo, že zatím nemá žádné problémy.

1. Na stránce **vlastní zásady** ve vašem tenantovi Azure AD B2C vyberte **Odeslat zásadu**.
2. Pokud existuje, zapněte **zásadu přepsat**a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions. XML* .
3. Klikněte na **Odeslat**.

> [!NOTE]
> Rozšíření Visual Studio Code B2C Extension používá "socialIdpUserId". Pro službu AD FS se taky vyžadují zásady pro sociální sítě.
>

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli je poskytovatel identity nastavený, ale není k dispozici na žádném z přihlašovacích obrazovek pro registraci nebo přihlášení. Aby byl k dispozici, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte, aby měl také poskytovatele identity ADFS.

1. Otevřete soubor *TrustFrameworkBase. XML* z úvodní sady.
2. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"`.
3. Otevřete *soubor TrustFrameworkExtensions. XML* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
4. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
5. Přejmenujte ID cesty pro uživatele. Například, `SignUpSignInADFS`.

### <a name="display-the-button"></a>Zobrazit tlačítko

Element **claimsproviderselection.** se podobá tlačítku poskytovatele identity na obrazovce pro registraci nebo přihlášení. Pokud přidáte pro účet ADFS element **claimsproviderselection.** , zobrazí se nové tlačítko, když se uživatel na stránce zařadí.

1. Vyhledejte element **OrchestrationStep** , který obsahuje `Order="1"` v cestě uživatele, kterou jste vytvořili.
2. Pod **ClaimsProviderSelections**přidejte následující element. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojit tlačítko s akcí

Teď, když máte tlačítko na místě, musíte ho propojit s akcí. Tato akce je v tomto případě určena pro Azure AD B2C ke komunikaci s účtem ADFS pro příjem tokenu.

1. Najděte **OrchestrationStep** , který obsahuje `Order="2"` v cestě uživatele.
2. Přidejte následující prvek **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na ID technického profilu, který jste vytvořili dříve. Například, `Contoso-SAML2`.

3. Uložte soubor *TrustFrameworkExtensions. XML* a znovu ho nahrajte k ověření.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurace vztahu důvěryhodnosti předávající strany služby AD FS

Pokud chcete jako poskytovatele identity v Azure AD B2C používat službu AD FS, musíte vytvořit vztah důvěryhodnosti předávající strany služby AD FS s Azure AD B2C metadaty SAML. Následující příklad ukazuje adresu URL k metadatům SAML Azure AD B2C technického profilu:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Nahraďte následující hodnoty:

- **váš tenant** s názvem vašeho tenanta, například Your-tenant.onmicrosoft.com.
- **vaše zásady** s názvem zásady. Například B2C_1A_signup_signin_adfs.
- **váš** technický profil s názvem vašeho technického profilu zprostředkovatele identity SAML. Například contoso-typu Saml2.

Otevřete prohlížeč a přejděte na adresu URL. Ujistěte se, že jste zadali správnou adresu URL a máte přístup k souboru metadat XML. Pokud chcete přidat nový vztah důvěryhodnosti předávající strany pomocí modulu snap-in Správa služby AD FS a ručně nakonfigurovat nastavení, proveďte na federačním serveru následující postup. Tento postup **mohou provést pouze členové skupiny Administrators nebo uživatelé** s ekvivalentními oprávněními na místním počítači.

1. V Správce serveru vyberte **nástroje**a pak vyberte Správa služby **AD FS**.
2. Vyberte **Přidat vztah důvěryhodnosti předávající strany**.
3. Na **úvodní** stránce Zvolte možnost **vědomosti deklarací identity**a potom klikněte na tlačítko **Spustit**.
4. Na stránce **Vybrat zdroj dat** vyberte možnost **importovat data o předávající straně publikování online nebo v místní síti**, zadejte adresu URL metadat Azure AD B2C a potom klikněte na **Další**.
5. Na stránce **zadat zobrazovaný název** zadejte **Zobrazovaný název**v části **poznámky**, zadejte popis pro tento vztah důvěryhodnosti předávající strany a klikněte na **Další**.
6. Na stránce **Zvolte zásady Access Control** vyberte zásadu a pak klikněte na **Další**.
7. Na stránce **připraveno k přidání vztahu důvěryhodnosti** zkontrolujte nastavení a potom kliknutím na tlačítko **Další** uložte informace o vztahu důvěryhodnosti předávající strany.
8. Na stránce **Dokončit** klikněte na **Zavřít**. Tato akce automaticky zobrazí dialogové okno **upravit pravidla deklarací identity** .
9. Vyberte **Přidat pravidlo**.
10. V **šabloně pravidla deklarace identity**vyberte **Odeslat atributy LDAP jako deklarace identity**.
11. Zadejte **název pravidla deklarace identity**. V případě **úložiště atributů**vyberte **možnost vybrat službu Active Directory**, přidejte následující deklarace identity a klikněte na tlačítko **Dokončit** a **OK**.

    | Atribut LDAP | Typ odchozí deklarace |
    | -------------- | ------------------- |
    | Hlavní název uživatele | userPrincipalName (Hlavní název uživatele) |
    | příjmení | family_name |
    | Křestní jméno | given_name |
    | e-mailová adresa | e-mail |
    | Zobrazované jméno | jméno |

    Všimněte si, že tyto názvy se nezobrazí v rozevíracím seznamu typ odchozí deklarace. Je nutné je ručně zadat v. (Rozevírací seznam je skutečně upravitelný).

12.  V závislosti na typu certifikátu možná budete muset nastavit algoritmus HASH. V okně Vlastnosti vztahu důvěryhodnosti předávající strany (B2C demo) vyberte kartu **Upřesnit** a změňte **algoritmus Secure Hash** na `SHA-256`a klikněte na **OK**.
13. V Správce serveru vyberte **nástroje**a pak vyberte Správa služby **AD FS**.
14. Vyberte vztah důvěryhodnosti předávající strany, který jste vytvořili, vyberte **aktualizovat z federačních metadat**a pak klikněte na **aktualizovat**.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete v tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést, chcete-li vytvořit testovací aplikaci, pokud jste tak již neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn. XML* v pracovním adresáři a přejmenujte ji. Přejmenujte ho například na *SignUpSignInADFS. XML*.
2. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInADFS`.
3. Aktualizujte hodnotu **PUBLICPOLICYURI** identifikátorem URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídala ID nové cesty uživatele, kterou jste vytvořili (SignUpSignInADFS).
5. Uložte změny, nahrajte soubor a pak v seznamu vyberte novou zásadu.
6. Ujistěte se, že je vybrána možnost Azure AD B2C aplikace, kterou jste vytvořili v poli **Vybrat aplikaci** , a poté ji otestujte kliknutím na tlačítko **Spustit nyní**.

