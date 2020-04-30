---
title: Přidání automatického dokončování a návrhů do vyhledávacího pole
titleSuffix: Azure Cognitive Search
description: Povolte v Azure Kognitivní hledání akce dotazování typu hledání jako, které umožňují navrhovat a formulují žádosti, které automaticky vyplní vyhledávací pole s dokončenými podmínkami nebo frázemi. Můžete také vrátit navrhované shody.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 60e9a435d705ee0fee6509e92cdcb056ac7ab609
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758110"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>Přidání automatického dokončování a návrhů do klientských aplikací

Hledání jako typ je běžná technika pro zlepšení produktivity dotazů inicializovaných uživatelem. V Azure Kognitivní hledání se toto prostředí podporuje prostřednictvím *automatického dokončování*, které dokončuje termín nebo frázi na základě částečného vstupu ("mikro" s "Microsoft"). Další forma je *návrhy*: krátký seznam odpovídajících dokumentů (pro vracení titulů knih s ID, aby bylo možné propojit stránku s podrobnostmi). Automatické dokončování i návrhy jsou u shody v indexu predikátem. Služba nebude nabízet dotazy, které vracejí žádné výsledky.

K implementaci těchto prostředí v Azure Kognitivní hledání budete potřebovat:

+ Modul pro *návrhy* na back-endu.
+ *Dotaz* určující rozhraní API pro [Automatické dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) nebo [návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions) v žádosti.
+ *Ovládací prvek uživatelského rozhraní* , který v klientské aplikaci zpracovává interakce typu hledání podle vás. Pro tento účel doporučujeme použít existující knihovnu JavaScriptu.

V Azure Kognitivní hledání jsou automatické dokončováné dotazy a navrhované výsledky načteny z indexu hledání z vybraných polí, která jste zaregistrovali v modulu pro návrhy. Modul pro návrhy je součástí indexu a určuje, která pole budou poskytovat obsah, který buď dokončí dotaz, navrhne výsledek nebo provede obojí. Když se index vytvoří a načte, vytvoří se interně struktura dat modulu pro ukládání předpon používaných pro porovnání částečných dotazů. V případě návrhů zvolte vhodná pole, která jsou jedinečná nebo alespoň neopakující, je základem pro prostředí. Další informace najdete v tématu [Vytvoření](index-add-suggesters.md)modulu pro návrhy.

Zbývající část tohoto článku se zaměřuje na dotazy a kód klienta. K ilustraci klíčových bodů používá JavaScript a C#. K stručnému zobrazení jednotlivých operací se používají příklady REST API. Odkazy na komplexní ukázky kódu najdete v části [Další kroky](#next-steps).

## <a name="set-up-a-request"></a>Nastavení žádosti

Mezi prvky požadavku patří jedno z rozhraní API pro hledání podle typu, částečný dotaz a modul pro návrhy. Následující skript ilustruje komponenty požadavku pomocí automatického dokončování REST API jako příklad.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**SuggesterName** poskytuje pole s možností použití pro navrhování, která se používají k dokončení podmínek nebo návrhů. Pro konkrétní návrhy se seznam polí musí skládat z těch, které nabízí jasné možnosti výběru mezi odpovídajícími výsledky. V lokalitě, která prodává počítačové hry, může být polem název hry.

Parametr **Search** poskytuje částečný dotaz, kde jsou znaky podávány do žádosti dotazu prostřednictvím ovládacího prvku jQuery automatického dokončování. Ve výše uvedeném příkladu je "minecraf" statická ilustrace toho, co ovládací prvek mohl předávat.

Rozhraní API neukládají požadavky na minimální délku na částečný dotaz. může to být jen jeden znak. Automatické dokončování jQuery ale poskytuje minimální délku. Typický je minimálně dva nebo tři znaky.

Shody jsou na začátku termínu kdekoli ve vstupním řetězci. V případě "rychlé hnědého Fox" se obě funkce automatického dokončování i návrhy shodují na částečných verzích "The", "Quick", "Brown" nebo "Fox", ale ne na částečných vponych výrazech, jako je například "řádku" nebo "Ox". Kromě toho každá shoda nastaví obor pro rozšíření pro příjem dat. Částečný dotaz "Rychlá BR" se bude shodovat s "rychlým hnědá" nebo "rychlým chlebem", ale ani "hnědá" nebo "chléb" by se shodovali s tím, že před nimi "Rychlá".

### <a name="apis-for-search-as-you-type"></a>Rozhraní API pro hledání podle vašeho typu

Pro referenční stránky REST a .NET SDK použijte tyto odkazy:

+ [REST API návrhů](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [REST API automatického dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [Metoda SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Metoda AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>Strukturování odpovědi

Odpovědi na automatické dokončování a návrhy jsou to, co byste pro vzor mohli očekávat: [funkce automatického dokončování](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response) vrátí seznam podmínek, [návrhy](https://docs.microsoft.com/rest/api/searchservice/suggestions#response) vrátí výrazy a ID dokumentu, abyste mohli načíst dokument (pomocí rozhraní API pro [vyhledání dokumentu](https://docs.microsoft.com/rest/api/searchservice/lookup-document) načíst konkrétní dokument pro stránku s podrobnostmi).

Odpovědi jsou ve tvaru podle parametrů v žádosti. Pro automatické dokončování nastavte [**autocompleteMode**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) , abyste zjistili, jestli se při dokončování textu vyskytuje jedna nebo dvě slova. U návrhů se pole, které zvolíte, určí obsah odpovědi.

V případě návrhů byste měli dál vylepšit odpověď, aby nedocházelo k duplicitám nebo co se jeví jako nesouvisející výsledky. Pro řízení výsledků přidejte do žádosti více parametrů. Následující parametry se vztahují na automatické dokončování i návrhy, ale mohou být vhodnější pro návrhy, zejména v případě, že modul pro návrh obsahuje více polí.

| Parametr | Využití |
|-----------|-------|
| **$select** | Máte-li v nástroji pro návrh více **sourceFields** , pomocí **$Select** vyberte, které pole přispívá k hodnotám (`$select=GameTitle`). |
| **searchFields** | Omezí dotaz na konkrétní pole. |
| **$filter** | Použijte kritéria shody pro sadu výsledků dotazu (`$filter=Category eq 'ActionAdventure'`). |
| **$top** | Omezí výsledky na konkrétní číslo (`$top=5`).|

## <a name="add-user-interaction-code"></a>Přidat kód interakce uživatele

Automatické vyplňování dotazovacího termínu nebo vyřazení seznamu vyhovujících odkazů vyžaduje kód interakce s uživatelem, obvykle JavaScript, který může spotřebovávat žádosti z externích zdrojů, jako jsou například automatické dokončování nebo návrhy dotazů na Azure Searchho indexu rozpoznávání.

I když byste tento kód mohli nativně psát, je mnohem jednodušší použít funkce z existující knihovny JavaScriptu. Tento článek ukazuje dva, jeden pro návrhy a další pro automatické dokončování. 

+ V příkladu návrhu se používá [widget pro automatické dokončování (rozhraní jQuery)](https://jqueryui.com/autocomplete/) . Můžete vytvořit vyhledávací pole a pak na něj odkazovat ve funkci JavaScriptu, která používá widget automatického dokončování. Vlastnosti pomůcky nastaví zdroj (funkci automatického dokončování nebo návrhů), minimální délku vstupních znaků před provedením akce a umístění.

+ [Modul plug-in XDSoft AutoComplete](https://xdsoft.net/jqplugins/autocomplete/) slouží jako příklad automatického dokončování.

Tyto knihovny používáme k sestavení vyhledávacího pole podporujícího návrhy i automatické dokončování. Vstupy shromážděné do vyhledávacího pole se spárují s návrhy a akcemi automatického dokončování.

## <a name="suggestions"></a>Návrhy

V této části se seznámíte s implementací navrhovaných výsledků od definice vyhledávacího pole. Ukazuje také, jak a skript vyvolá první knihovnu automatického dokončování JavaScriptu, na kterou odkazuje tento článek.

### <a name="create-a-search-box"></a>Vytvoření vyhledávacího pole

Za předpokladu, že [Knihovna automatického dokončování uživatelského rozhraní jQuery](https://jqueryui.com/autocomplete/) a projekt MVC v jazyce C#, můžete definovat vyhledávací pole pomocí JavaScriptu v souboru **index. cshtml** . Knihovna přidá do vyhledávacího pole interakci prohledat jako typ tak, že provede asynchronní volání kontroleru MVC k načtení návrhů.

V **indexu. cshtml** ve složce \Views\Home může být čára pro vytvoření vyhledávacího pole následující:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

V tomto příkladu je jednoduché vstupní textové pole s třídou pro stylování, na ID, na které se odkazuje pomocí JavaScriptu, a zástupný text.  

V rámci stejného souboru vložte JavaScript, který odkazuje na vyhledávací pole. Následující funkce volá rozhraní API pro návrhy, které vyžádá navrhovaný dokument podle dílčích vstupů:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Obsahuje `source` informace o funkci automatického dokončování uživatelského rozhraní jQuery, kde získat seznam položek, které se mají zobrazit v poli hledání. Vzhledem k tomu, že tento projekt je projekt MVC, volá funkci **navrhnout** v **HomeController.cs** , která obsahuje logiku pro vracení návrhů dotazů. Tato funkce také předá několik parametrů pro řízení světel, přibližné spárování a termínu. Rozhraní JavaScript API pro automatické dokončování přidá parametr term.

Tím `minLength: 3` zajistíte, aby se doporučení zobrazovala pouze v případě, že pole hledání obsahuje alespoň tři znaky.

### <a name="enable-fuzzy-matching"></a>Povolit přibližné porovnání

Vyhledávání přibližných shod umožňuje získat výsledky na základě blízké shody i v případě, že uživatel ve vyhledávacím poli udělá chybu ve slově. Vzdálenost úprav je 1, což znamená, že mezi uživatelským vstupem a shodou může být maximálně jeden znak. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Povolit zvýrazňování

Zvýrazňování aplikuje styl písma na znaky ve výsledku, které odpovídají vstupu. Pokud je například částečný vstup "mikro", výsledek by se **zobrazil jako**mikroměkký, **mikroskop Scope a**tak dále. Zvýrazňování je založeno na parametrech HighlightPreTag a HighlightPostTag, který je definovaný jako vložený s funkcí návrh.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Navrhnout funkci

Pokud používáte jazyk C# a aplikaci MVC, soubor **HomeController.cs** v adresáři Controllers je místo, kde můžete vytvořit třídu pro navrhované výsledky. V rozhraní .NET je funkce navrhovat založená na [metodě DocumentsOperationsExtensions. navrhuje](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

`InitSearch` Metoda vytvoří ověřeného klienta http indexu pro službu Azure kognitivní hledání. Další informace o sadě .NET SDK najdete v tématu [Jak používat Azure kognitivní hledání z aplikace .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        Select = HotelName,
        SearchFields = HotelName,
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Funkce Suggest přebírá dva parametry, které určují, jestli se mají vracet zvýrazněné shody nebo jestli se má kromě vstupního hledaného výrazu použít i přibližná shoda. Metoda vytvoří [objekt SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), který je poté předán rozhraní API navrhnout. Výsledek se pak převede do formátu JSON, aby ho bylo možné zobrazit v klientovi.

## <a name="autocomplete"></a>Automatické dokončování

V tomto případě byl kód uživatelského rozhraní hledání na základě návrhů na střed. Další blok kódu zobrazuje automatické dokončování pomocí funkce automatického dokončování uživatelského rozhraní XDSoft jQuery, která předá požadavek na automatické dokončování Azure Kognitivní hledání. Stejně jako u návrhů v aplikaci jazyka C#, kód, který podporuje interakci s uživatelem, přechází do **indexu. cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Funkce AutoComplete

Automatické dokončování je založené na [metodě DocumentsOperationsExtensions. AutoComplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet). Stejně jako u návrhů by tento blok kódu byl v souboru **HomeController.cs** .

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Funkce automatického dokončování přijímá vstup hledaného termínu. Metoda vytvoří [objekt AutoCompleteParameters](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Výsledek se pak převede do formátu JSON, aby ho bylo možné zobrazit v klientovi.

## <a name="next-steps"></a>Další kroky

Pomocí těchto odkazů můžete najít ucelené pokyny nebo kód, který demonstruje prostředí hledání s možností vyhledávání. Mezi příklady kódu patří hybridní implementace návrhů a automatické dokončování.

+ [Kurz: Vytvoření první aplikace v jazyce C# (lekce 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [Ukázka kódu C#: Azure-Search-dotnet-Samples/Create-First-App/3-Add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [Ukázka C# a JavaScriptu se ZBYTKem kódu pro souběžné navýšení](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)