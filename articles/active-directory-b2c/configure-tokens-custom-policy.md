---
title: Správa jednotného přihlašování a přizpůsobení tokenů pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Přečtěte si o správě jednotného přihlašování a přizpůsobení tokenů pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0b460d5a3c5535b74e349fa46c6a2ad55fc3a8d8
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82966568"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Správa jednotného přihlašování a přizpůsobení tokenů pomocí vlastních zásad v Azure Active Directory B2C

Tento článek poskytuje informace o tom, jak můžete spravovat konfigurace tokenu, relace a jednotného přihlašování pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="jtw-token-lifetimes-and-claims-configuration"></a>Životnost tokenů JTW a konfigurace deklarací identity

Pokud chcete změnit nastavení pro životnosti tokenů, přidejte do souboru předávající strany zásad, které chcete ovlivnit, element [ClaimsProviders](claimsproviders.md) .  Element **ClaimsProviders** je podřízeným prvkem elementu [TrustFrameworkPolicy](trustframeworkpolicy.md) .

Vložte element ClaimsProviders mezi element BasePolicy a element RelyingParty souboru předávající strany.

V rámci aplikace budete muset umístit informace, které mají vliv na životnost tokenů. KÓD XML vypadá jako v tomto příkladu:

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

V předchozím příkladu jsou nastaveny následující hodnoty:

- **Životnosti přístupového tokenu** – hodnota životnosti přístupového tokenu je nastavená s **token_lifetime_secs** položkou metadat. Výchozí hodnota je 3600 sekund (60 minut).
- **Doba platnosti tokenu ID** – hodnota životnosti tokenu ID je nastavená s **id_token_lifetime_secs** položkou metadat. Výchozí hodnota je 3600 sekund (60 minut).
- **Doba platnosti tokenu** – hodnota životnosti obnovovacího tokenu je nastavená pomocí položky metadat **refresh_token_lifetime_secs** . Výchozí hodnota je 1209600 sekund (14 dní).
- **Interval posuvných oken obnovovacího tokenu** – Pokud byste chtěli nastavit dobu trvání posuvných oken na obnovovací token, nastavte hodnotu **rolling_refresh_token_lifetime_secs** položky metadat. Výchozí hodnota je 7776000 (90 dní). Pokud nechcete vymáhat dobu trvání posuvných oken, nahraďte tuto položku `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`parametrem.
- **Deklarace vystavitele** – deklarace identity vystavitele (ISS) se nastaví pomocí položky metadat **IssuanceClaimPattern** . Platné hodnoty jsou `AuthorityAndTenantGuid` a `AuthorityWithTfp`.
- **Nastavení deklarace identity představující ID zásady** – možnosti pro nastavení této hodnoty jsou `TFP` (zásady pro vztah důvěryhodnosti) `ACR` a (odkaz na kontext ověřování). `TFP`je doporučená hodnota. Nastavte **AuthenticationContextReferenceClaimPattern** s hodnotou `None`.

    Do elementu **ClaimsSchema** přidejte tento element:

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Do prvku **OutputClaims** přidejte tento element:

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Pro ACR odeberte položku **AuthenticationContextReferenceClaimPattern** .

- **Deklarace subjektu (sub)** – Tato možnost je ve výchozím nastavení nastavena na hodnotu ObjectId, pokud byste chtěli toto `Not Supported`nastavení přepnout na, nahraďte tento řádek:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    s tímto řádkem:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure AD B2C relaci](session-overview.md).
- Naučte se [Konfigurovat chování relace ve vlastních zásadách](session-behavior-custom-policy.md).
- Referenční informace: [JwtIssuer](jwt-issuer-technical-profile.md).