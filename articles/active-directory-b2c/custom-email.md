---
title: Vlastní ověřování e-mailů
titleSuffix: Azure AD B2C
description: Naučte se, jak přizpůsobit ověřovací e-mail odesílaný zákazníkům při registraci k používání aplikací s podporou Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6cc0508a63f26b955ac5e0ebf3ef58a184a35997
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78671633"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Ověření vlastního e-mailu v Azure Active Directory B2C

Pomocí vlastního e-mailu v Azure Active Directory B2C (Azure AD B2C) můžete posílat přizpůsobené e-maily uživatelům, kteří se zaregistrují k používání vašich aplikací. Pomocí [DisplayControls](display-controls.md) (aktuálně ve verzi Preview) a poskytovatele e-mailu jiného výrobce můžete použít vlastní e-mailovou šablonu a z těchto umístění *:* adresa a předmět, stejně jako možnost lokalizace a vlastní nastavení JEDNORÁZOVého hesla (jednorázové heslo).

Vlastní ověřování e-mailu vyžaduje použití poskytovatele e-mailu jiného výrobce, jako je [SendGrid](https://sendgrid.com) nebo [SparkPost](https://sparkpost.com), vlastní REST API nebo libovolného poskytovatele e-mailu založeného na protokolu HTTP (včetně vaší vlastní). Tento článek popisuje nastavení řešení, které používá SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid

Pokud ho ještě nemáte, začněte nastavením účtu SendGrid (zákazníci Azure můžou každý měsíc odemknout 25 000 bezplatných e-mailů). Pokyny k instalaci najdete v části [Vytvoření účtu SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) tématu [Jak odesílat e-maily pomocí SendGrid s Azure](../sendgrid-dotnet-how-to-send-email.md).

Ujistěte se, že jste dokončili oddíl, ve kterém [vytvoříte klíč rozhraní SendGrid API](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Zaznamenejte klíč rozhraní API pro použití v pozdějším kroku.

## <a name="create-azure-ad-b2c-policy-key"></a>Vytvořit klíč zásad Azure AD B2C

V dalším kroku uložte klíč rozhraní API SendGrid do klíče zásad Azure AD B2C, aby se zásady odkazovaly na.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti**vyberte `Manual`možnost.
1. Zadejte **název** klíče zásad. Například, `SendGridSecret`. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
1. Do **tajného klíče**zadejte tajný klíč klienta, který jste předtím nahráli.
1. Pro **použití klíče**vyberte `Signature`.
1. Vyberte **Vytvořit**.

## <a name="create-sendgrid-template"></a>Vytvořit šablonu SendGrid

Když jste vytvořili účet SendGrid a klíč rozhraní SendGrid API uložený v klíči zásad Azure AD B2C, vytvořte [šablonu dynamické transakce](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)SendGrid.

1. Na webu SendGrid otevřete stránku [transakční šablony](https://sendgrid.com/dynamic_templates) a vyberte **vytvořit šablonu**.
1. Zadejte jedinečný název šablony, jako `Verification email` je, a pak vyberte **Uložit**.
1. Pokud chcete začít upravovat novou šablonu, vyberte **Přidat verzi**.
1. Vyberte **Editor kódu** a pak **pokračovat**.
1. V editoru HTML vložte následující šablonu HTML nebo použijte vlastní. Parametry `{{otp}}` a `{{email}}` budou dynamicky nahrazeny hodnotou jednorázového hesla a e-mailovou adresou uživatele.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Rozbalte **Nastavení** vlevo a pro **Předmět e-mailu**zadejte `{{subject}}`.
1. Vyberte **Uložit šablonu**.
1. Vraťte se na stránku **transakční šablony** výběrem šipky zpět.
1. Poznamenejte si **ID** šablony, kterou jste vytvořili pro použití v pozdějším kroku. Například, `d-989077fbba9746e89f3f6411f596fb96`. Toto ID můžete zadat při [přidávání transformace deklarací identity](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Přidat Azure AD B2C typy deklarací identity

V zásadách přidejte následující typy deklarací identity do `<ClaimsSchema>` prvku v rámci. `<BuildingBlocks>`

Tyto typy deklarací identity jsou nezbytné k vygenerování a ověření e-mailové adresy pomocí kódu jednorázového hesla (jednorázového hesla).

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>Přidání transformace deklarací identity

V dalším kroku budete potřebovat transformaci deklarací identity pro výstup deklarace řetězce JSON, který bude podléhat textu žádosti odeslané do SendGrid.

Struktura objektu JSON je definována ID v desítkovém zápisu vstupními parametry a TransformationClaimTypesem InputClaims. Čísla v zápisu teček implikují pole. Hodnoty pocházejí z hodnot InputClaims a vstupní hodnoty vlastností value. Další informace o transformacích deklarací JSON najdete v tématu [transformace deklarací JSON](json-transformations.md).

Přidejte následující transformaci deklarací identity do `<ClaimsTransformations>` elementu v `<BuildingBlocks>`rámci. Proveďte následující aktualizace XML transformace deklarací identity:

* Aktualizujte `template_id` hodnotu INPUTPARAMETER s ID transakční šablony SendGrid, kterou jste vytvořili dříve v části [vytvořit šablonu SendGrid](#create-sendgrid-template).
* Aktualizujte `from.email` hodnotu adresy. Pomocí platné e-mailové adresy můžete zabránit tomu, aby byl ověřovací e-mail označený jako spam.
* Aktualizujte hodnotu vstupního parametru `personalizations.0.dynamic_template_data.subject` řádku předmětu s řádkem předmětu, který je vhodný pro vaši organizaci.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Přidat definici obsahu DataUri

Pod transformací deklarací identity v `<BuildingBlocks>`rámci přidejte následující [ContentDefinition](contentdefinitions.md) , které odkazují na identifikátor URI dat 2.0.0 verze:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Vytvoření zobrazitelné ovládací prvek

K ověření e-mailové adresy s ověřovacím kódem, který se pošle uživateli, se použije ovládací prvek zobrazení ověřování.

Tento příklad ovládacího prvku zobrazení je nakonfigurován na:

1. Shromažďovat typ `email` deklarace adres od uživatele
1. Počkejte, až uživatel poskytne typ `verificationCode` deklarace s kódem odesílaným uživateli.
1. Vraťte se `email` zpět do technického profilu s vlastním uplatněním, který obsahuje odkaz na tento ovládací prvek zobrazení.
1. Pomocí `SendCode` akce vygenerujte kód pro jednorázové heslo a odešlete e-mail s kódem jednorázového hesla uživateli.

![Akce poslat e-mail s ověřovacím kódem](media/custom-email/display-control-verification-email-action-01.png)

V části definice obsahu dál `<BuildingBlocks>`přidejte do zásady následující [ovládací prvek](display-controls.md) typu [VerificationControl](display-control-verification.md) .

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Přidat technické profily jednorázového hesla

`GenerateOtp` Technický profil generuje kód pro e-mailovou adresu. `VerifyOtp` Technický profil ověří kód přidružený k e-mailové adrese. Můžete změnit konfiguraci formátu a vypršení platnosti jednorázového hesla. Další informace o technických profilech pro jednorázové heslo najdete v tématu [definice technického profilu s](one-time-password-technical-profile.md)jednorázovým heslem.

Do `<ClaimsProviders>` prvku přidejte následující technické profily.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Přidat profil REST API Technical

Tento REST API technický profil generuje obsah e-mailu (pomocí formátu SendGrid). Další informace o technických profilech RESTful najdete v tématu [definice technického profilu RESTful](restful-technical-profile.md).

Stejně jako u technických profilů jednorázového hesla přidejte do `<ClaimsProviders>` elementu následující technické profily.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Vytvořit odkaz na ovládací prvek zobrazit

V posledním kroku přidejte odkaz na ovládací prvek, který jste vytvořili. Pokud jste použili `LocalAccountSignUpWithLogonEmail` starší verzi Azure AD B2C zásad, nahraďte stávající technický profil svým držitelem pomocí následujících pokynů. Tento technický profil používá `DisplayClaims` s odkazem na ovládací prvek.

Další informace najdete v tématu [technický profil s vlastním kontrolním](restful-technical-profile.md) výrazem a [Zobrazit](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
        
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>Volitelné Lokalizace e-mailu

Chcete-li lokalizovat e-mail, je nutné odeslat lokalizované řetězce do SendGrid nebo poskytovatele e-mailu. Například pro lokalizaci předmětu e-mailu, textu, zprávy kódu nebo podpisu e-mailu. K tomu můžete použít transformaci deklarací [GetLocalizedStringsTransformation](string-transformations.md) ke kopírování lokalizovaných řetězců do typů deklarací. V transformaci `GenerateSendGridRequestBody` deklarací, která generuje datovou část JSON, používá vstupní deklarace identity, které obsahují lokalizované řetězce.

1. V zásadách definujte následující deklarace řetězců: předmět, zpráva, codeIntro a podpis.
1. Definujte transformaci deklarací [GetLocalizedStringsTransformation](string-transformations.md) k nahrazení lokalizovaných hodnot řetězce v deklaracích z kroku 1.
1. Změňte transformaci `GenerateSendGridRequestBody` deklarací identity tak, aby používala vstupní deklarace identity s následujícím FRAGMENTEM kódu XML.
1. Aktualizujte šablonu SendGrind tak, aby používala dynamické parametry místo všech řetězců, které budou lokalizovány Azure AD B2C.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
    <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
    <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
    <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="next-steps"></a>Další kroky

Příklad vlastní zásady ověřování e-mailu najdete na GitHubu:

[Ověření vlastního e-mailu – DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Informace o použití vlastního REST API nebo kteréhokoli poskytovatele e-mailové adresy SMTP založeného na protokolu HTTP najdete v tématu [definice RESTful Technical Profile ve vlastních zásadách Azure AD B2C](restful-technical-profile.md).
