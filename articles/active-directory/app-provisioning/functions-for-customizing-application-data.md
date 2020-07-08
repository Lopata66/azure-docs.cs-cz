---
title: Zapsat výrazy pro mapování atributů v Azure Active Directory
description: Naučte se používat mapování výrazů k transformaci hodnot atributů do přijatelného formátu při automatizovaném zřizování objektů aplikací SaaS v Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 02/05/2020
ms.author: kenwith
ms.openlocfilehash: 47f0502226e4227c6b94920da6f040004beb41f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84781663"
---
# <a name="how-to-write-expressions-for-attribute-mappings-in-azure-ad"></a>Postupy: zápis výrazů pro mapování atributů v Azure AD

Při konfiguraci zřizování pro aplikaci SaaS je jedním z typů mapování atributů, které lze zadat, mapování výrazu. V takovém případě musíte napsat výraz podobný skriptům, který umožňuje transformovat data uživatelů na formáty, které jsou pro aplikaci SaaS přijatelné.

## <a name="syntax-overview"></a>Přehled syntaxe

Syntaxe výrazů pro mapování atributů je Reminiscent funkcí jazyk Visual Basic for Application (VBA).

* Celý výraz musí být definován z funkcí Functions, který se skládá z názvu následovaného argumenty v závorkách: <br>
  *Functions ( `<<argument 1>>` , `<<argument N>>` )*
* Do sebe můžete vnořovat funkce. Příklad: <br> *FunctionOne (FunctionTwo ( `<<argument1>>` ))*
* Do funkcí můžete předat tři různé typy argumentů:
  
  1. Atributy, které musí být uzavřeny do hranatých závorek. Příklad: [attributeName]
  2. Řetězcové konstanty, které musí být uzavřeny do dvojitých uvozovek. Například: "USA"
  3. Další funkce. Příklad: FunctionOne ( `<<argument1>>` , FunctionTwo ( `<<argument2>>` ))
* Pro řetězcové konstanty, pokud v řetězci potřebujete zpětné lomítko (\) nebo uvozovky ("), musí být uvozen znakem zpětného lomítka (\). Příklad: "název společnosti: \\ " contoso \\ ""

## <a name="list-of-functions"></a>Seznam funkcí

[Append](#append) &nbsp; &nbsp; Připojit &nbsp; &nbsp; [BitAnd](#bitand) &nbsp; &nbsp; BitAnd &nbsp; &nbsp; [CBool](#cbool) &nbsp; &nbsp; CBool &nbsp; &nbsp; [Coalesce](#coalesce) &nbsp; &nbsp; COALESCE &nbsp; &nbsp; [ConvertToBase64](#converttobase64) &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; &nbsp; [Count](#count) &nbsp; &nbsp; Počet &nbsp; &nbsp; [CStr](#cstr) &nbsp; &nbsp; CStr &nbsp; &nbsp; [DateFromNum](#datefromnum) &nbsp; [FormatDateTime](#formatdatetime) &nbsp; &nbsp; FormatDateTime &nbsp; &nbsp; [Identifikátor](#guid) &nbsp; &nbsp; GUID &nbsp; &nbsp; [IIF](#iif) &nbsp; &nbsp; IIf &nbsp; &nbsp; [InStr](#instr) &nbsp; &nbsp; InStr &nbsp; &nbsp; [IsNull](#isnull) &nbsp; &nbsp; IsNull &nbsp; &nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp; &nbsp; IsNullOrEmpty &nbsp; &nbsp; K [dispozici](#ispresent) &nbsp; &nbsp; &nbsp; &nbsp; [IsString](#isstring) &nbsp; &nbsp; Řetězec &nbsp; &nbsp; [Item](#item) &nbsp; &nbsp; Položka &nbsp; &nbsp; [Připojit](#join) &nbsp; &nbsp; se &nbsp; &nbsp; [Left](#left) &nbsp; &nbsp; Vlevo &nbsp; &nbsp; [Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp; &nbsp; &nbsp; &nbsp; [RemoveDuplicates –](#removeduplicates) &nbsp; &nbsp; &nbsp; &nbsp; [Replace](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [rozdělit](#split) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Switch](#switch) &nbsp; &nbsp; &nbsp; &nbsp; [ToLower](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper) &nbsp; &nbsp; &nbsp; &nbsp; [Word](#word)

---
### <a name="append"></a>Připojit

**Slouží**<br> Připojit (zdroj, přípona)

**Popis:**<br> Převezme hodnotu zdrojového řetězce a připojí příponu na konec IT.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **auditování** |Vyžadováno |Řetězec |Řetězec, který chcete připojit ke konci zdrojové hodnoty. |

---
### <a name="bitand"></a>BitAnd
**Slouží**<br> BitAnd (hodnota1, hodnota2)

**Popis:**<br> Tato funkce převede oba parametry do binární reprezentace a nastaví bit na:

0 – Pokud má jedna nebo obě z odpovídajících bitů v hodnota1 a hodnota2 hodnotu 0                                                  
1 – Pokud jsou obě odpovídající bity 1.                                    

Jinými slovy, vrátí 0 ve všech případech s výjimkou toho, že odpovídající bity obou parametrů jsou 1.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Hodnota1** |Vyžadováno |num |Číselná hodnota, která by měla být AND'ed s hodnota2|
| **Argument** |Vyžadováno |num |Číselná hodnota, která má být AND'ed s hodnota1|

**Příklad:**<br>
BitAnd (&HF, &HF7)                                                                                
11110111 a 00000111 = 00000111, takže BitAnd vrátí hodnotu 7, binární hodnota 00000111

---
### <a name="cbool"></a>CBool
**Slouží**<br> CBool (výraz)

**Popis:**<br> Funkce CBool vrátí logickou hodnotu založenou na vyhodnoceném výrazu. Pokud je výraz vyhodnocen jako nenulová hodnota, pak funkce CBool vrátí hodnotu true, jinak vrátí hodnotu false..

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **vyjádření** |Vyžadováno | expression | Libovolný platný výraz |

**Příklad:**<br>
CBool ([attribute1] = [attribute2])                                                                    
Vrátí hodnotu true, pokud mají oba atributy stejnou hodnotu.

---
### <a name="coalesce"></a>Coalesce
**Slouží**<br> COALESCE (source1; SOURCE2;...; defaultValue)

**Popis:**<br> Vrátí první hodnotu zdroje, která není NULL. Pokud jsou všechny argumenty NULL a jsou přítomny hodnoty defaultValue, vrátí se hodnota defaultValue. Pokud jsou všechny argumenty NULL a vlastnost defaultValue není k dispozici, funkce coalesce vrátí hodnotu NULL.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **source1 ... sourceN** | Vyžadováno | Řetězec |Povinný, proměnlivý počet opakování. Obvykle název atributu ze zdrojového objektu. |
| **Hodnot** | Volitelné | Řetězec | Výchozí hodnota, která se má použít, pokud jsou všechny zdrojové hodnoty NULL. Může být prázdný řetězec ("").

---
### <a name="converttobase64"></a>ConvertToBase64
**Slouží**<br> ConvertToBase64 (zdroj)

**Popis:**<br> Funkce ConvertToBase64 převede řetězec na řetězec Unicode base64.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Řetězec, který má být převeden na základní 64|

**Příklad:**<br>
ConvertToBase64 ("Hello World!")                                                                                                        
Vrátí "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Slouží**<br> ConvertToUTF8Hex (zdroj)

**Popis:**<br> Funkce ConvertToUTF8Hex převede řetězec na šestnáctkovou hodnotu v kódování UTF8.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Řetězec, který se má převést na UTF8 hex|

**Příklad:**<br>
ConvertToUTF8Hex ("Hello World!")                                                                                                         
Vrátí 48656C6C6F20776F726C6421

---
### <a name="count"></a>Počet
**Slouží**<br> Count (atribut)

**Popis:**<br> Funkce Count vrátí počet prvků v vícehodnotovém atributu.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **přidělen** |Vyžadováno |– atribut |Vícehodnotový atribut, který bude obsahovat elementy počítané|

---
### <a name="cstr"></a>CStr
**Slouží**<br> CStr (hodnota)

**Popis:**<br> Funkce CStr převede hodnotu na datový typ String.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **osa** |Vyžadováno | Číselná, referenční nebo logická hodnota | Může to být číselná hodnota, odkazový atribut nebo logická hodnota. |

**Příklad:**<br>
CStr ([DN])                                                            
Vrátí "CN = Jan, DC = contoso, DC = com"

---
### <a name="datefromnum"></a>DateFromNum
**Slouží**<br> DateFromNum (hodnota)

**Popis:**<br> Funkce DateFromNum převede hodnotu ve formátu data AD na typ DateTime.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **osa** |Vyžadováno | Datum | Datum reklamy, které má být převedeno na typ DateTime |

**Příklad:**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
Vrátí hodnotu DateTime představující 2012-01-01 23:00:00.

---
### <a name="formatdatetime"></a>FormatDateTime
**Slouží**<br> FormatDateTime (zdroj, inputFormat, outputFormat)

**Popis:**<br> Vezme řetězec data z jednoho formátu a převede ho do jiného formátu.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **inputFormat** |Vyžadováno |Řetězec |Byl očekáván formát zdrojové hodnoty. Podporované formáty naleznete v tématu [https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) . |
| **outputFormat** |Vyžadováno |Řetězec |Formát data výstupu. |

---
### <a name="guid"></a>Identifikátor GUID
**Slouží**<br> GUID ()

**Popis:**<br> Identifikátor GUID funkce vygeneruje nový náhodný identifikátor GUID.

---
### <a name="iif"></a>IIF
**Slouží**<br> IIF (podmínka, valueIfTrue, valueIfFalse)

**Popis:**<br> Funkce IIF vrátí jednu ze sady možných hodnot na základě zadané podmínky.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **pomocné** |Vyžadováno |Proměnná nebo výraz |Libovolná hodnota nebo výraz, který lze vyhodnotit na hodnotu true nebo false. |
| **valueIfTrue** |Vyžadováno |Proměnná nebo řetězec | Pokud je podmínka vyhodnocena jako true, vrácená hodnota. |
| **valueIfFalse** |Vyžadováno |Proměnná nebo řetězec |Pokud je podmínka vyhodnocena jako false, vrácená hodnota.|

**Příklad:**<br>
IIF ([Country] = "USA"; [Country]; [Department])

---
### <a name="instr"></a>InStr
**Slouží**<br> InStr (hodnota1, hodnota2, Start, compareType)

**Popis:**<br> Funkce InStr vyhledá první výskyt podřetězce v řetězci.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Hodnota1** |Vyžadováno |Řetězec |Řetězec, který má být prohledán |
| **Argument** |Vyžadováno |Řetězec |Řetězec, který se má najít |
| **Čína** |Volitelné |Integer |Počáteční pozice pro vyhledání podřetězce|
| **compareType** |Volitelné |Výčet |Může být vbTextCompare nebo vbBinaryCompare |

**Příklad:**<br>
InStr ("Rychlá hnědá Fox", "Rychlá")                                                                             
Evalues na 5

InStr ("opakuje", "e"; 3; vbBinaryCompare)                                                                                  
Vyhodnotí na 7

---
### <a name="isnull"></a>IsNull
**Slouží**<br> IsNull (výraz)

**Popis:**<br> Pokud je výraz vyhodnocen jako null, funkce IsNull vrátí hodnotu true. U atributu je hodnota null vyjádřena nepřítomností atributu.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **vyjádření** |Vyžadováno |expression |Výraz, který se má vyhodnotit |

**Příklad:**<br>
IsNull ([DisplayName])                                                                                                
Vrátí hodnotu true, pokud atribut není přítomen.

---
### <a name="isnullorempty"></a>IsNullorEmpty
**Slouží**<br> IsNullOrEmpty (výraz)

**Popis:**<br> Pokud má výraz hodnotu null nebo je prázdný řetězec, vrátí funkce IsNullOrEmpty hodnotu true. U atributu by se to mělo vyhodnotit na hodnotu true, pokud atribut chybí nebo je přítomen, ale je to prázdný řetězec.
Inverzní část této funkce je pojmenována jako.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **vyjádření** |Vyžadováno |expression |Výraz, který se má vyhodnotit |

**Příklad:**<br>
IsNullOrEmpty ([DisplayName])                                               
Vrátí hodnotu true, pokud atribut není přítomen, nebo je prázdný řetězec.

---
### <a name="ispresent"></a>K dispozici
**Slouží**<br> K dispozici (výraz)

**Popis:**<br> Pokud se výraz vyhodnotí jako řetězec, který není null a není prázdný, vrátí funkce vracející hodnotu true. Inverzní funkce k této funkci má název IsNullOrEmpty.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **vyjádření** |Vyžadováno |expression |Výraz, který se má vyhodnotit |

**Příklad:**<br>
Switch (přítomen ([directManager]); [directManager]; ([skiplevelManager]); [skiplevelManager]; ([režisér]); [Director])

---
### <a name="isstring"></a>Řetězec
**Slouží**<br> Řetězec (výraz)

**Popis:**<br> Pokud je možné výraz vyhodnotit na typ řetězce, pak je funkce typu String vyhodnocena jako true.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **vyjádření** |Vyžadováno |expression |Výraz, který se má vyhodnotit |

---
### <a name="item"></a>Položka
**Slouží**<br> Item (atribut; index)

**Popis:**<br> Funkce Item vrátí jednu položku z vícehodnotového řetězce nebo atributu.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **přidělen** |Vyžadováno |Atribut |Vícehodnotový atribut, který má být prohledán |
| **indexovacím** |Vyžadováno |Integer | Index položky v řetězci s více hodnotami|

**Příklad:**<br>
Item ([proxyAddresses]; 1)

---
### <a name="join"></a>Spojit
**Slouží**<br> Join (oddělovač, source1, SOURCE2,...)

**Popis:**<br> Join () je podobný jako Append (), s tím rozdílem, že může zkombinovat více hodnot **zdrojového** řetězce do jednoho řetězce a každá hodnota bude oddělena řetězcem **oddělovače** .

Pokud je jednou ze zdrojových hodnot atribut s více hodnotami, pak se všechny hodnoty v tomto atributu spojí dohromady, oddělené hodnotou oddělovače.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **oddělování** |Vyžadováno |Řetězec |Řetězec, který se používá k oddělení zdrojových hodnot při zřetězení do jednoho řetězce. Může být "", pokud není vyžadován oddělovač. |
| **source1 ... sourceN** |Povinný, proměnlivý počet pokusů |Řetězec |Řetězcové hodnoty, které se mají spojit dohromady. |

---
### <a name="left"></a>Left
**Slouží**<br> Left (řetězec; NumChars)

**Popis:**<br> Funkce Left vrátí zadaný počet znaků nalevo od řetězce. Pokud numChars = 0, vrátí prázdný řetězec.
Pokud numChars < 0, vrátí se vstupní řetězec.
Pokud má řetězec hodnotu null, vrátí prázdný řetězec.
Pokud řetězec obsahuje méně znaků než číslo zadané v numChars, vrátí se řetězec shodný s řetězcem (tj. obsahující všechny znaky v parametru 1).

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Řetězec** |Vyžadováno |Atribut | Řetězec, ze kterého se mají vracet znaky |
| **NumChars** |Vyžadováno |Integer | Číslo určující počet znaků, které mají být vráceny od začátku (vlevo) řetězce|

**Příklad:**<br>
Vlevo ("Jan Novák"; 3)                                                            
Vrátí "Joh"

---
### <a name="mid"></a>Mid
**Slouží**<br> Mid (zdroj, začátek, délka)

**Popis:**<br> Vrátí podřetězec zdrojové hodnoty. Podřetězec je řetězec, který obsahuje pouze některé znaky ze zdrojového řetězce.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu. |
| **Čína** |Vyžadováno |celé číslo |Index ve **zdrojovém** řetězci, ve kterém by měl být spuštěný dílčí řetězec První znak v řetězci bude mít index 1, druhý znak bude mít index 2 atd. |
| **časový** |Vyžadováno |celé číslo |Délka podřetězce Pokud délka končí mimo **zdrojový** řetězec, funkce vrátí podřetězec z **počátečního** indexu do konce **zdrojového** řetězce. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**Slouží**<br> NormalizeDiacritics (zdroj)

**Popis:**<br> Vyžaduje jeden řetězcový argument. Vrátí řetězec, ale se všemi diakritikami nahrazenými ekvivalentními nediakritikou znaků. Obvykle slouží k převodu křestních jmen a příjmení obsahujících diakritická znaménka (značky akcentů) na platné hodnoty, které lze použít v různých uživatelských identifikátorech, jako jsou hlavní názvy uživatelů, názvy účtů SAM a e-mailové adresy.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec | Obvykle se jedná o křestní jméno nebo název atributu příjmení. |

---
### <a name="not"></a>Not
**Slouží**<br> Nepoužívá se (zdroj)

**Popis:**<br> Převrátí logickou hodnotu **zdroje**. Pokud je hodnota **zdroje** "*true*", vrátí "*false*". V opačném případě vrátí "*true*".

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Logický řetězec |Očekávané **zdrojové** hodnoty jsou "true" nebo "false". |

---
### <a name="numfromdate"></a>NumFromDate
**Slouží**<br> NumFromDate (hodnota)

**Popis:**<br> Funkce NumFromDate převede hodnotu DateTime na formát služby Active Directory, který je vyžadován pro nastavení atributů jako [accountExpires](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires). Pomocí této funkce můžete převést hodnoty DateTime obdržené z cloudových aplikací pro HR, jako je třeba Workday a SuccessFactors, do jejich ekvivalentu reklamy. 

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **osa** |Vyžadováno | Řetězec | Řetězec data a času v podporovaném formátu. Podporované formáty naleznete v tématu https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx . |

**Příklad:**<br>
* Příklad pracovního dne <br>
  Za předpokladu, že chcete mapovat atribut *ContractEndDate* z Workday, který je ve formátu *2020-12-31-08:00* až *accountExpires* pole ve službě AD, zde je postup, jak můžete tuto funkci použít a změnit posun časového pásma tak, aby odpovídal vašemu národnímu prostředí. 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* Příklad SuccessFactors <br>
  Za předpokladu, že chcete mapovat atribut *EndDate* z SuccessFactors, který je ve formátu *M/d/rrrr hh: mm: SS TT* na *accountExpires* pole ve službě AD, zde je postup použití této funkce a změna posunu časového pásma tak, aby odpovídala vašemu národnímu prostředí.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates –
**Slouží**<br> RemoveDuplicates – (atribut)

**Popis:**<br> Funkce RemoveDuplicates – přebírá řetězec s více hodnotami a ověří, zda jsou všechny hodnoty jedinečné.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **přidělen** |Vyžadováno |Vícehodnotový atribut |Vícehodnotový atribut, který bude mít odebrané duplicity|

**Příklad:**<br>
RemoveDuplicates – ([proxyAddresses])                                                                                                       
Vrátí upravený atribut proxyAddress, ve kterém se odebraly všechny duplicitní hodnoty.

---
### <a name="replace"></a>Nahradit
**Slouží**<br> Replace (Source, oldValue, vzor Regex, regexGroupName, replacementValue, replacementAttributeName, Template)

**Popis:**<br>
Nahradí hodnoty v rámci řetězce. Funguje různě v závislosti na zadaných parametrech:

* Když jsou k dispozici **OldValue** a **replacementValue** :
  
  * Nahradí všechny výskyty **OldValue** ve **zdroji** pomocí **replacementValue** .
* Pokud jsou zadány **OldValue** a **Šablona** :
  
  * Nahradí všechny výskyty **OldValue** v **šabloně** **zdrojovou** hodnotou.
* Když jsou k dispozici **vzor Regex** a **replacementValue** :

  * Funkce použije **vzor Regex** na **zdrojový** řetězec a můžete použít názvy skupin Regex k sestavení řetězce pro **replacementValue**
* Když jsou k dispozici **vzor Regex**, **regexGroupName**, **replacementValue** :
  
  * Funkce použije **vzor Regex** na **zdrojový** řetězec a nahradí všechny hodnoty, které odpovídají **regexGroupName** , s **replacementValue**
* Když jsou k dispozici **vzor Regex**, **regexGroupName**, **replacementAttributeName** :
  
  * Pokud **zdroj** nemá žádnou hodnotu, vrátí se **zdroj** .
  * Pokud má **zdroj** hodnotu, funkce použije **vzor Regex** na **zdrojový** řetězec a nahradí všechny hodnoty odpovídající **regexGroupName** hodnotou přidruženou k **replacementAttributeName** .

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze **zdrojového** objektu. |
| **oldValue** |Volitelné |Řetězec |Hodnota, která má být nahrazena ve **zdroji** nebo **šabloně**. |
| **Vzor Regex** |Volitelné |Řetězec |Vzor regulárního výrazu pro hodnotu, která má být nahrazena **zdrojem**. Nebo, pokud se používá **replacementPropertyName** , vzorek pro extrakci hodnoty z **replacementPropertyName**. |
| **regexGroupName** |Volitelné |Řetězec |Název skupiny uvnitř **vzor Regex** Jenom v případě, že se používá **replacementPropertyName** , extrahujeme hodnotu této skupiny jako **replacementValue** z **replacementPropertyName**. |
| **replacementValue** |Volitelné |Řetězec |Nová hodnota, kterou chcete nahradit starou. |
| **replacementAttributeName** |Volitelné |Řetězec |Název atributu, který se má použít k nahrazení hodnoty |
| **vzhledu** |Volitelné |Řetězec |Když se zadá hodnota **šablony** , budeme v šabloně Hledat text **OldValue** a nahradit ho **zdrojovou** hodnotou. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**Slouží**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, ...)

**Popis:**<br> Vyžaduje minimálně dva argumenty, což jsou jedinečná pravidla generování hodnot definovaná pomocí výrazů. Funkce vyhodnocuje všechna pravidla a potom kontroluje hodnotu vygenerovanou pro jedinečnost v cílové aplikaci/adresáři. První nalezená jedinečná hodnota bude vrácena. Pokud všechny hodnoty již v cíli existují, bude položka získat uloží a důvod se zaznamená do protokolu auditu. Není k dispozici horní mez počtu argumentů, které by bylo možné poskytnout.


 - Toto je funkce nejvyšší úrovně, nemůže být vnořená.
 - Tuto funkci nelze použít pro atributy, které mají odpovídající prioritu.   
 - Tato funkce je určena pouze pro vytváření záznamů. Při použití s atributem nastavte vlastnost **použít mapování** na **pouze při vytváření objektu**.
 - Tato funkce je momentálně podporovaná jenom pro zřizování uživatelů z Workday do služby Active Directory. Nedá se použít s jinými zřizovacími aplikacemi. 


**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **uniqueValueRule1 ... uniqueValueRuleN** |Vyžaduje se aspoň 2, bez horní meze. |Řetězec | Seznam pravidel generování jedinečných hodnot, které se mají vyhodnotit |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**Slouží**<br> SingleAppRoleAssignment ([appRoleAssignments])

**Popis:**<br> Vrátí jeden appRoleAssignment ze seznamu všech appRoleAssignments přiřazených uživateli pro danou aplikaci. Tato funkce je nutná k převedení objektu appRoleAssignments na jeden řetězec názvu role. Doporučujeme, abyste zajistili, že se jednomu uživateli přiřadí pouze jedna appRoleAssignment a pokud je přiřazeno více rolí, vrácený řetězec role nemusí být předvídatelný. 

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **AppRoleAssignments** |Vyžadováno |Řetězec |objekt **[appRoleAssignments]** . |

---
### <a name="split"></a>Rozdělení
**Slouží**<br> Split (Source, oddělovač)

**Popis:**<br> Rozdělí řetězec na pole s více hodnotami pomocí zadaného oddělovače.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |**zdrojová** hodnota, která se má aktualizovat |
| **oddělovač** |Vyžadováno |Řetězec |Určuje znak, který bude použit k rozdělení řetězce (například: ","). |

---
### <a name="stripspaces"></a>StripSpaces
**Slouží**<br> StripSpaces (zdroj)

**Popis:**<br> Odebere ze zdrojového řetězce všechny znaky ("").

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |**zdrojová** hodnota, která se má aktualizovat |

---
### <a name="switch"></a>Přepínač
**Slouží**<br> Switch (zdroj; defaultValue; klíč1; hodnota1; key2; hodnota2;...)

**Popis:**<br> Když hodnota **zdroje** odpovídá **klíči**, vrátí **hodnotu** pro tento **klíč**. Pokud **zdrojová** hodnota neodpovídá žádným klíčům, vrátí hodnotu **DefaultValue**.  Parametry **klíče** a **hodnoty** se musí vždycky nacházet ve dvojicích. Funkce vždycky očekává sudý počet parametrů. Funkce by neměla být používána pro referenční atributy, jako je například správce. 

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |**Zdrojová** hodnota, která se má aktualizovat |
| **Hodnot** |Volitelné |Řetězec |Výchozí hodnota, která se má použít, pokud zdroj neodpovídá žádným klíčům Může být prázdný řetězec (""). |
| **zkrat** |Vyžadováno |Řetězec |**Klíč** pro porovnání **zdrojové** hodnoty s. |
| **osa** |Vyžadováno |Řetězec |Nahrazující hodnota pro **zdroj** , který odpovídá klíči. |

---
### <a name="tolower"></a>ToLower
**Slouží**<br> ToLower (zdroj, jazyková verze)

**Popis:**<br> Převezme hodnotu *zdrojového* řetězce a převede ji na malý případ pomocí pravidel jazykové verze, které jsou určeny. Pokud nejsou zadány žádné informace o *jazykové verzi* , pak použije invariantní jazykovou verzi.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze zdrojového objektu |
| **jazykových** |Volitelné |Řetězec |Formát pro název jazykové verze založený na RFC 4646 je *languagecode2-Country/regioncode2*, kde *languagecode2* je kód jazyka dvou písmen a *země/regioncode2* je kód subjazykové verze se dvěma písmeny. Mezi příklady patří ja-JP pro japonštinu (Japonsko) a EN-US pro angličtinu (USA). V případech, kdy kód jazyka se dvěma písmeny není k dispozici, je použit kód o třech písmenech odvozený z ISO 639-2.|

---
### <a name="toupper"></a>ToUpper
**Slouží**<br> ToUpper (zdroj, jazyková verze)

**Popis:**<br> Převezme hodnotu *zdrojového* řetězce a převede ji na velká písmena pomocí pravidel jazykové verze, které jsou určeny. Pokud nejsou zadány žádné informace o *jazykové verzi* , pak použije invariantní jazykovou verzi.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Zdrojová** |Vyžadováno |Řetězec |Obvykle název atributu ze zdrojového objektu. |
| **jazykových** |Volitelné |Řetězec |Formát pro název jazykové verze založený na RFC 4646 je *languagecode2-Country/regioncode2*, kde *languagecode2* je kód jazyka dvou písmen a *země/regioncode2* je kód subjazykové verze se dvěma písmeny. Mezi příklady patří ja-JP pro japonštinu (Japonsko) a EN-US pro angličtinu (USA). V případech, kdy kód jazyka se dvěma písmeny není k dispozici, je použit kód o třech písmenech odvozený z ISO 639-2.|

---
### <a name="word"></a>Word
**Slouží**<br> Word (řetězec, WordNumber, oddělovače)

**Popis:**<br> Funkce Word vrátí slovo obsažené v řetězci na základě parametrů popisujících oddělovače, které se mají použít, a číslo slova, které se má vrátit. Každý řetězec znaků v řetězci, oddělený jedním ze znaků v oddělovačích, je identifikován jako slova:

Pokud číslo < 1, vrátí prázdný řetězec.
Pokud má řetězec hodnotu null, vrátí prázdný řetězec.
Pokud řetězec obsahuje méně než čísla slov nebo řetězec neobsahuje žádná slova identifikovaná oddělovači, je vrácen prázdný řetězec.

**Ukazatelů**<br> 

| Name | Požadováno/opakování | Typ | Poznámky |
| --- | --- | --- | --- |
| **Řetězec** |Vyžadováno |Vícehodnotový atribut |Řetězec, ze kterého se má vrátit slovo|
| **WordNumber** |Vyžadováno | Integer | Číslo určující, které číslo slova se má vrátit|
| **oddělovače** |Vyžadováno |Řetězec| Řetězec představující oddělovače, které se mají použít k identifikaci slov|

**Příklad:**<br>
Word ("Rychlá hnědá Fox", 3, "")                                                                                       
Vrátí "Brown"

Word ("This, String! má&mnoho oddělovačů", 3, ",! & #").                                                                       
Vrátí "has"

---

## <a name="examples"></a>Příklady
### <a name="strip-known-domain-name"></a>Název známé domény
Aby bylo možné získat uživatelské jméno, je nutné odstranit známý název domény z e-mailu uživatele. <br>
Pokud je doména například "contoso.com", můžete použít následující výraz:

**Vyjádření** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Vzorový vstup/výstup:** <br>

* **Vstup** (pošta): " john.doe@contoso.com "
* **Výstup**: Jan. Chvojková

### <a name="append-constant-suffix-to-user-name"></a>Připojit konstantní příponu k uživatelskému jménu
Pokud používáte izolovaný prostor Salesforce, možná budete muset před synchronizací přidat další příponu všem vašim uživatelským jménům.

**Vyjádření** <br>
`Append([userPrincipalName], ".test")`

**Vzorový vstup/výstup:** <br>

* **Vstup**: (userPrincipalName): " John.Doe@contoso.com "
* **Výstup**: " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Generovat alias uživatele zřetězením částí jména a příjmení
Alias uživatele musíte vygenerovat zadáním prvních 3 písmen křestního jména uživatele a prvních 5 písmen příjmení uživatele.

**Vyjádření** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Vzorový vstup/výstup:** <br>

* **Vstup** (křestní jméno): "Jan"
* **Vstup** (příjmení): "Chvojková"
* **Výstup**: "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>Odebrat diakritická znaménka z řetězce
Je nutné nahradit znaky obsahující diakritická znaménka stejnými znaky, které neobsahují značky akcentů.

**Vyjádření** <br>
NormalizeDiacritics ([křestní jméno])

**Vzorový vstup/výstup:** <br>

* **Vstup** (křestní jméno): "Zoë"
* **Výstup**: "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>Rozdělit řetězec do pole s více hodnotami
Musíte vzít seznam řetězců oddělených čárkami a rozdělit je do pole, které se dá zapojit do vícehodnotového atributu, jako je atribut PermissionSet služby Salesforce. V tomto příkladu se v extensionAttribute5 ve službě Azure AD nastavil seznam sad oprávnění.

**Vyjádření** <br>
Split ([extensionAttribute5]; ";")

**Vzorový vstup/výstup:** <br>

* **Vstup** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **Výstup**: ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>Datum výstupu jako řetězec v určitém formátu
Chcete odesílat data do aplikace SaaS v určitém formátu. <br>
Například chcete formátovat data pro ServiceNow.

**Vyjádření** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Vzorový vstup/výstup:**

* **Vstup** (extensionAttribute1): "20150123105347.1 z"
* **Výstup**: "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Nahraďte hodnotu na základě předdefinované sady možností.

Je potřeba definovat časové pásmo uživatele na základě kódu stavu uloženého ve službě Azure AD. <br>
Pokud kód stavu neodpovídá žádné z předdefinovaných možností, použijte výchozí hodnotu "Austrálie/Sydney".

**Vyjádření** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Vzorový vstup/výstup:**

* **Vstup** (stav): "QLD"
* **Výstup**: "Austrálie/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>Nahrazení znaků pomocí regulárního výrazu
Je nutné najít znaky, které odpovídají hodnotě regulárního výrazu, a odebrat je.

**Vyjádření** <br>

Replace ([mailNickname];; "[a-zA-Z_] *",, "",,)

**Vzorový vstup/výstup:**

* **Vstup** (mailnickname: "john_doe72"
* **Výstup**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>Převést generovanou hodnotu userPrincipalName (UPN) na malá písmena
V následujícím příkladu je hodnota hlavního názvu uživatele generována zřetězením zdrojových polí PreferredFirstName a PreferredLastName a funkce ToLower funguje na vygenerovaném řetězci pro převod všech znaků na malá písmena. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**Vzorový vstup/výstup:**

* **Vstup** (PreferredFirstName): "Jan"
* **Vstup** (PreferredLastName): "Smith"
* **Výstup**: " john.smith@contoso.com "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>Generovat jedinečnou hodnotu pro atribut userPrincipalName (UPN)
Na základě křestního jména a jména uživatele a jména a příjmení je potřeba vygenerovat hodnotu pro atribut hlavního názvu uživatele (UPN) a před přiřazením hodnoty k atributu hlavního názvu uživatele vyhledat jeho jedinečnost v cílovém adresáři služby AD.

**Vyjádření** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**Vzorový vstup/výstup:**

* **Vstup** (PreferredFirstName): "Jan"
* **Vstup** (PreferredLastName): "Smith"
* **Výstup**: " John.Smith@contoso.com ", pokud hodnota hlavního názvu uživatele (UPN) John.Smith@contoso.com ještě v adresáři neexistuje
* **Výstup**: " J.Smith@contoso.com ", pokud John.Smith@contoso.com v adresáři již existuje hodnota hlavního názvu uživatele (UPN).
* **Výstup**: " Jo.Smith@contoso.com ", pokud výše uvedené dvě hodnoty UPN v adresáři již existují

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>Hodnota pro poštu flowu, pokud není NULL, jinak Flow userPrincipalName
Chcete-li, aby se atribut mail nacházel, je-li k dispozici. Pokud není, chcete místo toho Flow použít hodnotu userPrincipalName.

**Vyjádření** <br>
`Coalesce([mail],[userPrincipalName])`

**Vzorový vstup/výstup:** <br>

* **Vstup** (mail): null
* **Vstup** (userPrincipalName): " John.Doe@contoso.com "
* **Výstup**: " John.Doe@contoso.com "

## <a name="related-articles"></a>Související články
* [Automatizace zřizování a rušení zřizování uživatelů pro aplikace SaaS](../app-provisioning/user-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](../app-provisioning/customize-application-attributes.md)
* [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
* [Pomocí SCIM zapněte automatické zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Oznámení o zřizování účtů](../app-provisioning/user-provisioning.md)
* [Seznam kurzů o integraci aplikací SaaS](../saas-apps/tutorial-list.md)
