---
title: ClaimsProviders – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte element ClaimsProvider vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2d862d07a65c3fb28b49a82692ea575f787b9750
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60313847"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Zprostředkovatel deklarací identity obsahuje sadu [technické profily](technicalprofiles.md). Každý zprostředkovatele deklarací identity, musí mít nejmíň jeden technické profily, které určují koncových bodů a protokolů potřebných ke komunikaci se zprostředkovateli deklarací identity. Zprostředkovatel deklarací může mít více technické profily. Například více technické profily mohou být definovány, protože zprostředkovatele deklarací identity podporuje více protokolů, různé koncové body s jinou možností nebo uvolní různé deklarace na různé záruky úrovní. Může být přijatelný uvolnit citlivých deklarací identity v cestě jednoho uživatele, ale ne v jiném.

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

**ClaimsProviders** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1: n | Poskytovatele akreditované deklarace identity, který můžete využít v různých cest uživatele. |

## <a name="claimsprovider"></a>ClaimsProvider

**ClaimsProvider** prvek obsahuje následujících podřízených elementů:

| Element | Výskyty | Popis |
| ------- | ---------- | ----------- |
| Domain (Doména) | 0:1 | Řetězec, který obsahuje název domény pro poskytovatele deklarací identity. Například pokud poskytovatele deklarací identity obsahuje technický profil na Facebooku, název domény je Facebook.com. Tento název domény se používá pro všechny technické profily definovanými v zprostředkovatele deklarací identity, pokud není přepsán technický profil. Název domény můžete také odkazovat **domain_hint**. Další informace najdete v tématu **přesměrování přihlášení k poskytovateli sociální** část [nastavit přímé přihlášení pomocí Azure Active Directory B2C](direct-signin.md). |
| Zobrazovaný název | 0:1 | Řetězec, který obsahuje název, který může být zobrazen uživatelům. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Sada technické profily, které podporuje zprostředkovatel deklarací identity |

**ClaimsProvider** slouží k uspořádání vztah technické profily k zprostředkovatele deklarací identity. Následující příklad ukazuje zprostředkovatele deklarací identity Azure Active Directory s Azure Active Directory technické profily:

```XML
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Následující příklad ukazuje zprostředkovatele deklarací identity Facebook s **OAUTH pro Facebook** technický profil.

```XML
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
