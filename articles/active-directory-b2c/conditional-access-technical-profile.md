---
title: Technické profily podmíněného přístupu ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Referenční informace o vlastních zásadách pro technické profily podmíněného přístupu v Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc811ab3cab4b79b81b16dd94a2c72225046e35a
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488274"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu podmíněného přístupu v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Podmíněný přístup Azure Active Directory (Azure AD) je nástroj používaný Azure AD B2C k spojování signálů, rozhodování a prosazování zásad organizace. Automatizace hodnocení rizik pomocí podmínek zásad znamená, že jsou rizikové přihlašování zjištěné a napravované nebo blokované.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `Proprietary` . Atribut **obslužné rutiny** musí obsahovat plně kvalifikovaný název sestavení obslužné rutiny protokolu, které je používáno Azure AD B2C:

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Následující příklad ukazuje technický profil podmíněného přístupu:

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>Vyhodnocení podmíněného přístupu

Pro každé přihlášení Azure AD B2C vyhodnotí všechny zásady a před udělením přístupu uživatele zajistí splnění všech požadavků. Možnost blokovat přístup přepíše všechna ostatní nastavení konfigurace. Režim **vyhodnocení** technického profilu podmíněného přístupu vyhodnocuje signály shromážděné Azure AD B2C během přihlašování pomocí místního účtu. Výsledkem technického profilu podmíněného přístupu je sada deklarací identity, které jsou výsledkem vyhodnocení podmíněného přístupu. Zásady Azure AD B2C používají tyto deklarace v dalším kroku orchestrace k provedení akce, jako je například blokování uživatele nebo odvolání uživatele pomocí služby Multi-Factor Authentication. Pro tento režim lze nakonfigurovat následující možnosti.

### <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Typ operace | Yes | Musí být **vyhodnocena**.  |

### <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací pro odeslání do podmíněného přístupu. Název vaší deklarace identity můžete také namapovat na název definovaný v technickém profilu podmíněného přístupu.

| ClaimReferenceId | Vyžadováno | Typ dat | Popis |
| --------- | -------- | ----------- |----------- |
| UserId | Yes | řetězec | Identifikátor uživatele, který se přihlásí. |
| AuthenticationMethodsUsed | Yes |Třída StringCollection | Seznam metod, které uživatel použil k přihlášení Možné hodnoty: `Password` , a `OneTimePasscode` . |
| Federované | Yes |boolean | Označuje, jestli se uživatel přihlásil pomocí federovaného účtu. Hodnota musí být `false` . |
| IsMfaRegistered | Yes |boolean | Označuje, zda již uživatel zaregistroval telefonní číslo pro službu Multi-Factor Authentication. |


Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových objektů před jejich odesláním do služby podmíněného přístupu.

### <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací generovaných ConditionalAccessProtocolProvider. Název vaší deklarace identity můžete také namapovat na název definovaný níže.

| ClaimReferenceId | Vyžadováno | Typ dat | Popis |
| --------- | -------- | ----------- |----------- |
| Výzvy | Yes |Třída StringCollection | Seznam akcí pro nápravu identifikované hrozby. Možné hodnoty: `block` |
| MultiConditionalAccessStatus | Yes | Třída StringCollection |  |

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

### <a name="example-evaluation"></a>Příklad: vyhodnocení

Následující příklad ukazuje technický profil podmíněného přístupu, který se používá k vyhodnocení hrozby přihlášení.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>Náprava

Režim **oprav** pro technický profil podmíněného přístupu informuje Azure AD B2C, že se zjištěná hrozba pro přihlášení napravuje. Pro opravný režim lze nakonfigurovat následující možnosti.

### <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Typ operace | Yes | Je nutné provést **nápravu**.  |

### <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** obsahuje seznam deklarací pro odeslání do podmíněného přístupu. Název vaší deklarace identity můžete také namapovat na název definovaný v technickém profilu podmíněného přístupu.

| ClaimReferenceId | Vyžadováno | Typ dat | Popis |
| --------- | -------- | ----------- |----------- |
| ChallengesSatisfied | Yes | Třída StringCollection| Seznam uspokojivých výzev k nápravě identifikované hrozby jako návrat z režimu vyhodnocení, s nárokem na výzvy.|


Element **InputClaimsTransformations** může obsahovat kolekci prvků **InputClaimsTransformation** , které se používají k úpravě vstupních deklarací identity nebo k vygenerování nových před voláním služby podmíněného přístupu.

### <a name="output-claims"></a>Deklarace výstupů

Zprostředkovatel protokolu podmíněného přístupu nevrací žádné **OutputClaims** , takže není nutné zadávat deklarace výstupů. Můžete ale zahrnout deklarace identity, které nevrací poskytovatel protokolu podmíněného přístupu, pokud nastavíte `DefaultValue` atribut.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.


### <a name="example-remediation"></a>Příklad: náprava

Následující příklad ukazuje technický profil podmíněného přístupu, který se používá k nápravě identifikované hrozby:

```XML
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

Chcete-li získat příklad práce, přidejte deklarace uvedené v následujícím příkladu do oddílu ClaimsSchema v souboru rozšíření:

```XML
<!-- Conditional Access claims  -->
   <ClaimType Id="conditionalAccessClaimCollection">
      <DisplayName>Conditional Access claims</DisplayName>
      <DataType>stringCollection</DataType>
      <AdminHelpText>The list of claims which are the result of CA check</AdminHelpText>
   </ClaimType>
   <ClaimType Id="ConditionalAccessStatus">
      <DisplayName>The status of CA evaluation</DisplayName>
      <DataType>stringCollection</DataType>
      <AdminHelpText>The status of CA evaluation</AdminHelpText>
    </ClaimType>
    <ClaimType Id="AuthenticationMethodsUsed">
      <DisplayName>Authentication methods used</DisplayName>
      <DataType>stringCollection</DataType>
      <AdminHelpText>The list of authentication methods used</AdminHelpText>
    </ClaimType>
    <ClaimType Id="AuthenticationMethodUsed">
      <DisplayName>Authentication method used</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>The authentication method used</AdminHelpText>
    </ClaimType>
    <ClaimType Id="IsFederated">
      <DisplayName>IsFederated</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Is user authenticated via an external identity provider</AdminHelpText>
    </ClaimType>
    <ClaimType Id="IsMfaRegistered">
      <DisplayName>IsMfaRegistered</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Is user registered for MFA</AdminHelpText>
    </ClaimType> 
    <ClaimType Id="CAChallengeIsMfa">
      <DisplayName>CAChallengeIsMfa</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="CAChallengeIsChgPwd">
      <DisplayName>CAChallengeIsChgPwd</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="CAChallengeIsBlock">
      <DisplayName>CAChallengeIsBlock</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="responseMsg">
      <DisplayName>responseMsg</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
      <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
      <UserInputType>Paragraph</UserInputType>
    </ClaimType>
<!-- End of Conditional Access claims -->
```

Přidejte následující transformace deklarací identity:

```xml
<!--Conditional Access Transformations-->
  <ClaimsTransformation Id="AddToAuthenticationMethodsUsed" TransformationMethod="AddItemToStringCollection">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="AuthenticationMethodUsed" TransformationClaimType="item" />
      <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="CreatePasswordAuthenticationMethodClaim" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="Password" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AuthenticationMethodUsed" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="CreateOneTimePasscodeAuthenticationMethodClaim" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="OneTimePasscode" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AuthenticationMethodUsed" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsMfaRegisteredCT" TransformationMethod="DoesClaimExist">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="IsMfaRegistered" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="SetCAChallengeIsMfa" TransformationMethod="StringCollectionContains">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" TransformationClaimType="inputClaim" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="item" DataType="string" Value="mfa" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="CAChallengeIsMfa" TransformationClaimType="outputClaim" />
    </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="SetCAChallengeIsChgPwd" TransformationMethod="StringCollectionContains">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" TransformationClaimType="inputClaim" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="item" DataType="string" Value="chg_pwd" />
        <InputParameter Id="ignoreCase" DataType="string" Value="true" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsChgPwd" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="SetCAChallengeIsBlock" TransformationMethod="StringCollectionContains">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" TransformationClaimType="inputClaim" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="item" DataType="string" Value="block" />
        <InputParameter Id="ignoreCase" DataType="string" Value="true" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsBlock" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
```

Přidejte následující technický profil:

```xml
<TechnicalProfile Id="GenerateCAClaimFlags">
  <DisplayName>GenerateCAClaimFlags</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="CAChallengeIsMfa" />
    <OutputClaim ClaimTypeReferenceId="CAChallengeIsChgPwd" />
    <OutputClaim ClaimTypeReferenceId="CAChallengeIsBlock" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="SetCAChallengeIsMfa" />
    <OutputClaimsTransformation ReferenceId="SetCAChallengeIsChgPwd" />
    <OutputClaimsTransformation ReferenceId="SetCAChallengeIsBlock" />
  </OutputClaimsTransformations>
</TechnicalProfile>

<!--This is a self-asserted technical profile that we will use to show a block screen-->
<TechnicalProfile Id="ShowBlockPage">
  <DisplayName>Show Block message</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
    <Item Key="TokenLifeTimeInSeconds">3600</Item>
    <Item Key="AllowGenerationOfClaimsWithNullValues">true</Item>
    <Item Key="setting.showContinueButton">false</Item>
    <Item Key="setting.showCancelButton">false</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseMsg" DefaultValue="The user is blocked due to conditional access check." />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
  <EnabledForUserJourneys>Always</EnabledForUserJourneys>
</TechnicalProfile>

```

Do prvku TrustFrameworkPolicy přidejte tyto dílčí cesty, jak je znázorněno v následujícím příkladu:

```xml
  <SubJourneys>
    <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>conditionalAccessClaimCollection</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
          </ClaimsExchanges>
        </OrchestrationStep>
      </OrchestrationSteps>
    </SubJourney>

    <SubJourney Id="ConditionalAccess_Remediation" Type="Call">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>conditionalAccessClaimCollection</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="ConditionalAccessRemediation" TechnicalProfileReferenceId="ConditionalAccessRemediation" />
          </ClaimsExchanges>
        </OrchestrationStep>
      </OrchestrationSteps>
    </SubJourney>
  </SubJourneys>

```

Přidejte cestu uživatele, která používá nové deklarace identity, jak je znázorněno v následujícím příkladu:

```xml
  <UserJourneys>
    <UserJourney Id="SignUpOrSignInWithCA">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />

          </ClaimsProviderSelections>
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <!-- Check if the user has selected to sign in using one of the social providers -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
              <Value>objectId</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <!-- For social IDP authentication, attempt to find the user account in the directory. -->
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>authenticationSource</Value>
              <Value>socialIdpAuthentication</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <OrchestrationStep Order="4" Type="InvokeSubJourney">
          <JourneyList>
            <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
          </JourneyList>
        </OrchestrationStep>

        <!--MFA based on Conditional Access-->
        <OrchestrationStep Order="5" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>CAChallengeIsMfa</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>CAChallengeIsMfa</Value>
              <Value>False</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <!--Save MFA phone number: The precondition verifies whether the user provided a new number in the previous step. If so, the phone number is stored in the directory for future authentication requests.-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>newPhoneNumberEntered</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserWriteWithObjectId" TechnicalProfileReferenceId="AAD-UserWritePhoneNumberUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <OrchestrationStep Order="7" Type="ClaimsExchange" >
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>CAChallengeIsBlock</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
              <Value>CAChallengeIsBlock</Value>
              <Value>True</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="ShowBlockPage" TechnicalProfileReferenceId="ShowBlockPage" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <!--If a user has reached this point, this means a remediation was applied-->
        <!--  You can add a precondition here to call remediation only if a Conditional Access challenge was issued-->
        <OrchestrationStep Order="8" Type="InvokeSubJourney">
          <JourneyList>
            <Candidate SubJourneyReferenceId="ConditionalAccess_Remediation" />
          </JourneyList>
        </OrchestrationStep>
        <OrchestrationStep Order="9" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>

```

Následuje příklad souboru předávající strany, který odkazuje na tento UserJourney:

```xml
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
                      xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
                      xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
                      PolicySchemaVersion="0.3.0.0" TenantId="contoso.onmicrosoft.com" 
                      PolicyId="ha-sam-signup_signin-CA" 
                      PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_CA"
                      DeploymentMode="Development"
                      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignInWithCA" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>604800</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="<add your app insights instrumentation key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="signInName" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsMfa" />
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsBlock" />
        <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="next-steps"></a>Další kroky

- Příklad zásad podmíněného přístupu na [GitHubu](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access)najdete v části.
