---
title: Moderování textu – Content Moderator
description: Moderování textu pomocí možné nežádoucího textu, identifikovatelné osobní údaje, a vlastní seznamy termínů.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 5a1007f2408b48c96f5eeaf585b94c8caa7ceb45
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607132"
---
# <a name="learn-text-moderation-concepts"></a>Další koncepty moderování textu

Použít moderování textu s podporou počítače Content Moderator a [recenze prováděné lidmi](Review-Tool-User-Guide/human-in-the-loop.md) možnosti Moderovat obsah textu.

Můžete blokovat, schválit nebo zkontrolovat obsah na základě prahových hodnot a zásad. Použijte ho k rozšiřují lidské moderování prostředí, kde partnerům, zaměstnancům a spotřebitelé generují textový obsah. Patří mezi ně chatovací místnosti, diskusní vývěsky, chatovacích, katalogů elektronického obchodování a dokumenty. 

Odpověď služby obsahuje následující informace:

- Vulgárních výrazů: výraz na základě vzorů s předdefinovaného seznamu neslušná podmínky v různých jazycích
- Klasifikace: klasifikace s podporou počítače do tří kategorií
- Osobní údaje
- Automaticky opravila text
- Původní text
- Jazyk

## <a name="profanity"></a>Vulgárních výrazů

Pokud rozhraní API zjistí neslušná podmínek v některém z [podporované jazyky](Text-Moderation-API-Languages.md), tyto podmínky jsou zahrnuty v odpovědi. Odpověď obsahuje také jejich umístění (`Index`) v původní text. `ListId` v následujícím příkladu JSON odkazuje na podmínky můžete najít v [seznamy termínů vlastní](try-terms-list-api.md) Pokud je k dispozici.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Pro **jazyk** parametr, přiřaďte `eng` nebo toto podokno nechat prázdné najdete v článku s počítače **klasifikace** odpovědí (funkce preview). **Tato funkce podporuje pouze angličtinu**.
>
> Pro **vulgárních výrazů podmínek** detekce, použijte [kód ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) podporovaných jazyků uvedených v tomto článku, nebo nechte prázdné.

## <a name="classification"></a>Klasifikace

Content Moderator uživatele s podporou počítače **funkce klasifikace textu** podporuje **jen v angličtině**, a pomáhá zjišťovat potenciálně nežádoucí obsah. Označené příznakem obsah může vyhodnoceny jako nevhodný v závislosti na kontextu. Přenáší pravděpodobnost, že každá kategorie a může doporučit kontrolu člověkem. Funkce používá k identifikaci možných urážlivý nebo uráží diskriminační jazyk trénovaného modelu. To zahrnuje slang, zkrácený slova, urážlivé a záměrně chybně napsaná slova ke kontrole. 

Následující výpis v JSON extract ukazuje příklad výstupu:

    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
            },
        "Category2": {
            "Score": 0.12747249007225037
            },
        "Category3": {
            "Score": 0.98799997568130493
        }
    }

### <a name="explanation"></a>Vysvětlení

- `Category1` označuje potenciální přítomnost jazyk, který se dá považovat za sexuálně explicitní nebo pro dospělé v určitých situacích.
- `Category2` označuje potenciální přítomnost jazyk, který se dá považovat za sexuálně sugestivní nebo až po zralé v určitých situacích.
- `Category3` označuje potenciální přítomnost jazyk, který se dá považovat za urážlivé v určitých situacích.
- `Score` je mezi 0 a 1. Čím více bodů vyšší model je predikce kategorii lze použít. Tato funkce závisí na statistických modelů a nikoli ručně kódovaný výsledků. Doporučujeme ve Visual Basicu s vlastní obsah k určení, jak každou kategorii odpovídá vašim požadavkům.
- `ReviewRecommended` je PRAVDA nebo NEPRAVDA v závislosti na interní skóre prahové hodnoty. Zákazníci by měli zhodnotit, zda chcete použít tuto hodnotu nebo při rozhodování o vlastní prahové hodnoty na základě jejich obsahu zásad.

## <a name="personal-data"></a>Osobní údaje

Funkce identifikovatelné osobní údaje zjistí potenciální přítomnost tyto informace:

- E-mailová adresa
- Poštovní adresa USA
- IP adresa
- Telefonní číslo USA
- Spojené království telefonní číslo
- Sociálního pojištění USA (SSN)

Následující příklad ukazuje ukázkové odpovědi:

    "PII": {
        "Email": [{
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 32
            }],
        "IPA": [{
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 72
            }],
        "Phone": [{
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 56
            }, {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
            }],
        "Address": [{
            "Text": "1 Microsoft Way, Redmond, WA 98052",
            "Index": 89
            }],
        "SSN": [{
            "Text": "999999999",
            "Index": 56
            }, {
            "Text": "999-99-9999",
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>Automatické opravy

Předpokládejme, že se vstupní text ("lzay" a "f0x" jsou záměr):

    The qu!ck brown f0x jumps over the lzay dog.

Když požadujete třeba automatické opravy, odpověď obsahuje opravenou verzi text:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Vytváření a správě vlastních seznamů podmínek

Zatímco výchozí, globální seznam termínů dobře funguje pro většinu případů, můžete chtít obrazovky s výrazy, které jsou specifické pro vaše obchodní potřeby. Můžete například vyfiltrovat všechny konkurenční názvy z příspěvků uživateli.

> [!NOTE]
> Limit je maximálně **5 seznamů výrazů** a v každém seznamu může být **maximálně 10 000 výrazů**.
>

Následující příklad ukazuje odpovídající ID seznamu:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

Content Moderator poskytuje [rozhraní API seznam termínů](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) s operacemi správy vlastní termín seznamy. Začněte [výraz obsahuje rozhraní API konzoly](try-terms-list-api.md) a používat rozhraní REST API ukázky kódu. Podívejte se také [rychlý úvod k .NET obsahuje seznam termínů](term-lists-quickstart-dotnet.md) Pokud jste se seznámili s Visual Studio a C#.

## <a name="next-steps"></a>Další postup

Testovací verze [konzole rozhraní API pro moderování textu](try-text-api.md) a používat rozhraní REST API ukázky kódu. Podívejte se také [rychlý úvod k .NET pro moderování textu](text-moderation-quickstart-dotnet.md) Pokud jste obeznámeni s Visual Studio a C#.
