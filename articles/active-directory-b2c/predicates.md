---
title: Predikáty a PredicateValidations – Azure Active Directory B2C | Dokumentace Microsoftu
description: Účtu na sociální síti deklaraci příklady transformaci identita prostředí Framework schéma z Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6163f1cbf878f4d4678b2b66829522b0dd16ae22
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835632"
---
# <a name="predicates-and-predicatevalidations"></a>Predikáty a PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Predikáty** a **PredicateValidations** prvky umožňují provádět proces ověření k zajištění, že se zadá jenom správně vytvořená data do vašeho tenanta Azure Active Directory (Azure AD) B2C .

Následující diagram znázorňuje vztah mezi elementy:

![Diagram znázorňující vztah predikáty a predikátu ověření](./media/predicates/predicates.png)

## <a name="predicates"></a>Predikáty

**Predikátu** element definuje základního ověřování pro kontrolu hodnoty typu deklarace identity a vrátí `true` nebo `false`. Ověření se provádí pomocí zadaného **metoda** elementu a sadu **parametr** prvky, které jsou relevantní pro metodu. Například predikát můžete zkontrolovat, jestli délka řetězcovou hodnotu deklarace identity je v rozsahu minimální a maximální parametry zadané, nebo určuje, zda obsahuje hodnotu deklarace identity řetězce znakovou sadu. **UserHelpText** prvek obsahuje chybovou zprávu pro uživatele, pokud kontrola selže. Hodnota **UserHelpText** element může být lokalizovány pomocí [přizpůsobení jazyka](localization.md).

**Predikáty** prvek obsahuje následující element:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Predikát | 1: n | Seznam predikáty. |

**Predikátu** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor, který se používá pro predikát. Další prvky můžete pomocí tohoto identifikátoru v zásadách. |
| Metoda | Ano | Typ metody pro účely ověření. Možné hodnoty: **IsLengthRange**, **MatchesRegex**, **IncludesCharacters**, nebo **IsDateRange**. **IsLengthRange** hodnotu zkontroluje, zda je délka řetězcovou hodnotu deklarace identity v rozsahu minimální a maximální parametry zadané. **MatchesRegex** hodnotu kontroluje, zda řetězcovou hodnotu deklarace identity odpovídá regulárnímu výrazu. **IncludesCharacters** hodnotu kontroluje, zda obsahuje hodnotu deklarace identity řetězce znakovou sadu. **IsDateRange** hodnotu zkontroluje, zda hodnota deklarace identity data mezi širokou škálou minimální a maximální parametry zadané. |

**Predikátu** prvek obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | Chybová zpráva pro uživatele, pokud kontrola selže. Tento řetězec může být lokalizovány pomocí [přizpůsobení jazyka](localization.md) |
| Parametry | 1:1 | Parametry pro typ metody ověření řetězce. |

**Parametry** prvek obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Parametr | 1: n | Parametry pro typ metody ověření řetězce. |

**Parametr** prvek obsahuje následující atributy:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Id | 1:1 | Identifikátor parametru. |

Následující příklad ukazuje `IsLengthRange` metody s parametry `Minimum` a `Maximum` , které určují rozsah délky řetězce:

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

Následující příklad ukazuje `MatchesRegex` metody s parametrem `RegularExpression` , který určuje regulární výraz:

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

Následující příklad ukazuje `IncludesCharacters` metody s parametrem `CharacterSet` , který určuje sadu znaků:

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

Následující příklad ukazuje `IsDateRange` metody s parametry `Minimum` a `Maximum` , zadejte rozsah dat ve formátu `yyyy-MM-dd` a `Today`.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

Při ověřování do kontroly oproti typu deklarace identity, definujte predikáty **PredicateValidations** seskupily sadu predikáty formu ověřování uživatelského vstupu, který můžete použít pro typ deklarace identity. Každý **PredicateValidation** prvek obsahuje sadu **PredicateGroup** prvky, které obsahují sadu **PredicateReference** prvky, které odkazuje **Predikátu**. Předat ověřování, by měla hodnota deklarace identity projít všemi testy žádné predikátu ve všech zadaných **PredicateGroup** s jejich sadu **PredicateReference** elementy.

```XML
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

**PredicateValidations** prvek obsahuje následující element:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| PredicateValidation | 1: n | Seznam predikátu ověření. |

**PredicateValidation** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor, který se používá pro ověřování predikátu. **Typu deklarace identity** element můžete pomocí tohoto identifikátoru v zásadách. |

**PredicateValidation** prvek obsahuje následující element:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| PredicateGroups | 1: n | Seznam skupin predikátu. |

**PredicateGroups** prvek obsahuje následující element:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| PredicateGroup | 1: n | Seznam predikáty. |

**PredicateGroup** prvek obsahuje následující atribut:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor, který se používá pro skupinu predikátu.  |

**PredicateGroup** prvek obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  Popis predikátu, které vám pomůžou uživatelům vědět, jaká hodnota by měla zadání. |
| PredicateReferences | 1: n | Seznam odkazy na predikát. |

**PredicateReferences** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| MatchAtLeast | Ne | Určuje, že tato hodnota shodovala alespoň, že mnoho predikátu definice pro vstup na přijetí. |

**PredicateReferences** prvek obsahuje následující prvky:

| Prvek | Výskyty | Popis |
| ------- | ----------- | ----------- |
| PredicateReference | 1: n | Odkaz na predikát. |

**PredicateReference** prvek obsahuje následující atributy:

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor, který se používá pro ověřování predikátu.  |


## <a name="configure-password-complexity"></a>Nakonfigurujte složitost hesla

S **predikáty** a **PredicateValidationsInput** můžete řídit požadavky na složitost hesel poskytnutých uživatelem, při vytváření účtu. Ve výchozím nastavení používá silná hesla Azure AD B2C. Azure AD B2C podporuje také možnosti konfigurace pro řízení složitosti hesla, které můžou zákazníci využívat. Složitost hesla můžete definovat pomocí těchto predikátu prvky:

- **IsLengthBetween8And64** pomocí `IsLengthRange` metoda, ověří, že heslo musí být dlouhé 8 až 64 znaků.
- **Malá písmena** pomocí `IncludesCharacters` metoda, ověřuje, zda heslo obsahuje malé písmeno.
- **Velká písmena** pomocí `IncludesCharacters` metoda, ověřuje, zda heslo obsahuje velké písmeno.
- **Číslo** pomocí `IncludesCharacters` metoda, ověřuje, zda heslo obsahuje číslici.
- **Symbol** pomocí `IncludesCharacters` metoda, ověřuje, zda heslo obsahuje jeden z těchto symbolů `@#$%^&*\-_+=[]{}|\:',?/~"();!`
- **PIN kód** pomocí `MatchesRegex` metoda, ověřuje, zda heslo obsahuje pouze čísla.
- **AllowedAADCharacters** pomocí `MatchesRegex` metoda, ověří, zda byl zadán pouze neplatný znak hesla.
- **DisallowedWhitespace** pomocí `MatchesRegex` metoda, ověří, že heslo není začínat ani končit prázdným znakem.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
    <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters">
    <UserHelpText>a lowercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters">
    <UserHelpText>an uppercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters">
    <UserHelpText>a digit</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters">
    <UserHelpText>a symbol</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\:',?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex">
    <UserHelpText>The password must be numbers only.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex">
    <UserHelpText>An invalid character was provided.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex">
    <UserHelpText>The password must not begin or end with a whitespace character.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Po definování základní ověření můžete kombinovat společně a vytvořit zásady pro hesla, které používáte ve svojí zásadě:

- **SimplePassword** ověří DisallowedWhitespace, AllowedAADCharacters a IsLengthBetween8And64
- **StrongPassword** IsLengthBetween8And64 DisallowedWhitespace AllowedAADCharacters, ověří. Poslední skupinu `CharacterClasses` spustí další sadu predikáty s `MatchAtLeast` nastaven na hodnotu 3. Uživatelské heslo musí být dlouhé 8 až 16 znaků a tři z následujících znaků: Malá písmena, velká písmena, čísla nebo Symbol.
- **CustomPassword** ověří pouze DisallowedWhitespace, AllowedAADCharacters. Uživatel tedy můžete zadat jakékoli heslo o libovolné délce znaky jsou platné.

```XML
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Do vašeho typu deklarace identity, přidejte **PredicateValidationReference** elementu a určit identifikátor jako jeden predikátu neúspěšná, jako je například SimplePassword StrongPassword či CustomPassword.

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Následující obrázek znázorňuje uspořádání prvků při Azure AD B2C zobrazí chybová zpráva:

![Diagram predikát a PredicateGroup příklad složitost hesla](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Konfigurovat rozsah dat

S **predikáty** a **PredicateValidations** prvky můžete určit datum minimální a maximální hodnoty **UserInputType** pomocí `DateTimeDropdown`. K tomuto účelu vytvořte **predikátu** s `IsDateRange` metoda a zadat minimální a maximální parametry.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange">
    <UserHelpText>The date must be between 01-01-1980 and today.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Přidat **PredicateValidation** s odkazem na `DateRange` predikátu.

```XML
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

Do vašeho typu deklarace identity, přidejte **PredicateValidationReference** elementu a určit identifikátor jako `CustomDateRange`.

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```
