---
title: Analyzátory pro jazykovou a zpracování textu – Azure Search
description: Přiřazení analyzátorům prohledávatelná textová pole v indexu nahradit výchozí standardní Lucene s alternativami vlastní, předdefinovaný nebo konkrétní jazyk.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: f76d944f614f07a4428d4e4100f6a08a375d96dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65795801"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Analyzátory pro zpracování ve službě Azure Search textu

*Analyzátor* je součástí [fulltextový vyhledávací modul](search-lucene-query-architecture.md) zodpovědná za zpracování textu v řetězci dotazu a indexovaných dokumentů. Různé analyzátory práci s textem různými způsoby v závislosti na scénáři. Analyzátory jazyka zpracovávat text pomocí jazyková pravidla za účelem zlepšení kvality hledání při dalších analyzátorů provádět další základní úkoly, jako je například převod znaků na malá písmena. 

Analyzátory jazyka jsou nejčastěji používané a je výchozí analyzátor jazyka přiřazená každé prohledávatelná pole v indexu Azure Search. Následující transformace jazyka jsou typické během analýzy textu:

+ Bez nezbytné slova (stopword) a interpunkční znaménka se odeberou.
+ Fráze a slovech jsou rozdělené do součásti.
+ Slov velká písmena jsou malá malými a velkými písmeny.
+ Slova jsou zmenšeny na kořenové formulářů tak, aby bez ohledu na čas může být nalezena shoda.

Jazykové analyzátory převést textový vstup do primitivních nebo kořenový formuláře, které jsou efektivní informace o ukládání a načítání. Převod vyvolá se při indexování, při vytváření indexu a pak znovu během hledání, pokud je index pro čtení. Pravděpodobněji k získání požadovaných výsledků hledání očekáváte, že pokud použijete stejný analyzátor pro obě operace.

## <a name="default-analyzer"></a>Výchozí analyzátor  

Používá služba Azure Search [Apache Lucene standardní analyzer (standardní lucene)](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) jako výchozí, která rozdělí text na prvky, které následují ["Segmentace Unicode Text"](https://unicode.org/reports/tr29/) pravidla. Kromě toho standardní analyzátor převede všechny znaky do svého formuláře malými písmeny. Indexovaných dokumentů a hledané termíny procházet analýzy při indexování a zpracování dotazů.  

Automaticky se používá na každé prohledávatelná pole. Můžete přepsat výchozí na základě pole pomocí pole. Může být alternativní analyzátory [analyzátor jazyka](index-add-language-analyzers.md), [vlastní analyzátor](index-add-custom-analyzers.md), nebo předdefinované analyzátor z [seznam dostupných analyzátory](index-add-custom-analyzers.md#AnalyzerTable).


## <a name="types-of-analyzers"></a>Typy analyzátory

Následující seznam popisuje, jaké analyzátory jsou k dispozici ve službě Azure Search.

| Category | Popis |
|----------|-------------|
| [Standardní analyzátor Lucene](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Default (Výchozí). Vyžaduje se žádné specifikace nebo konfigurace. Tento analyzátor pro obecné účely provádí dobře pro většinu scénářů a jazyky.|
| Předdefinované analyzátory | Nabízí jak finální produkt je určen pro použití jako-je. <br/>Existují dva typy: specializované a jazyk. Co je kvůli tomu je "předdefinovaný" můžete odkazovat pomocí názvu, bez konfigurace nebo přizpůsobení. <br/><br/>[Specializované (jazykově nezávislé) analyzátory](index-add-custom-analyzers.md#AnalyzerTable) se používají při textovými vstupy vyžadují speciální zpracování a minimální zpracování. Zahrnout předdefinované non jazykové analyzátory **Asciifolding**, **– klíčové slovo**, **vzor**, **jednoduché**, **Zastavit**, **Prázdné znaky**.<br/><br/>[Analyzátory jazyka](index-add-language-analyzers.md) se používají, když budete potřebovat bohatou jazykovou podporu pro jednotlivé jazyky. Služba Azure Search podporuje 35 Lucene jazykové analyzátory a 50 analyzátory zpracování přirozeného jazyka Microsoft. |
|[Vlastní analyzátory](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Odkazuje na uživatelem definované konfigurace z kombinace stávající elementy skládající se z jednoho tokenizátor (povinné) a volitelné filtry (char nebo token).|

Několik předdefinovaných analyzátory, jako například **vzor** nebo **Zastavit**, podporují omezená sada možností konfigurace. Pokud chcete nastavit tyto možnosti, efektivně vytvoříte vlastní analyzátor, skládající se z předdefinovaných analyzer a jeden alternativní možnosti uvedené v [předdefinovaný odkaz analyzátoru](index-add-custom-analyzers.md#AnalyzerTable). Jak pro vlastní konfigurace, zadejte nové konfigurace s názvem, jako třeba *myPatternAnalyzer* ho odlišuje od analyzátor Lucene vzor.

## <a name="how-to-specify-analyzers"></a>Určení analyzátory

1. (pro vlastní analyzátory jenom) Vytvořit pojmenovanou **analyzátor** části v definici indexu. Další informace najdete v tématu [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) a také [přidat vlastní analyzátory](index-add-custom-analyzers.md).

2. Na [pole definice](https://docs.microsoft.com/rest/api/searchservice/create-index) v indexu, nastavte pole **analyzátor** vlastnost na název cílového analyzer (například `"analyzer" = "keyword"`. Platné hodnoty jsou název předdefinovaného analyzátoru, analyzátor jazyka nebo vlastní analyzátor také definováno ve schématu indexu. Naplánujte přiřazování analyzátor ve fázi definici indexu před vytvořením indexu ve službě.

3. Volitelně můžete místo jednoho **analyzátor** vlastnost můžete nastavit různé analyzátory pro indexování a dotazování pomocí **indexAnalyzer** a **searchAnalyzer** pole Parametry. Můžete využít různé analyzátory pro přípravu dat a načítání jednu z těchto aktivit podle potřeby konkrétní transformace nevyžaduje druhé.

Přiřazení **analyzátor** nebo **indexAnalyzer** na pole, které již byly fyzicky vytvořil není povolený. Pokud některý z to není jasné, přečtěte si následující tabulce najdete výčet z nich akce vyžadují opětovné sestavení a proč.
 
 | Scénář | Dopad | Kroky |
 |----------|--------|-------|
 | Přidání nového pole | Minimální | Pokud pole ještě neexistuje ve schématu, neexistuje žádné pole revize provést, protože pole ještě nemá fyzickou přítomnost v indexu. Můžete použít [aktualizaci indexu](https://docs.microsoft.com/rest/api/searchservice/update-index) přidání nového pole do stávajícího indexu, a [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ho naplnit.|
 | Přidat **analyzátor** nebo **indexAnalyzer** na stávající indexovaného pole. | [Opětovné sestavení](search-howto-reindex.md) | Obráceným index pro toto pole musí znovu vytvořit od počátku a musí být přeindexovány obsah těchto polí. <br/> <br/>Pro indexy aktivně vyvíjí [odstranit](https://docs.microsoft.com/rest/api/searchservice/delete-index) a [vytvořit](https://docs.microsoft.com/rest/api/searchservice/create-index) index ke sbírání nová definice pole. <br/> <br/>Pro indexy v produkčním prostředí můžete odložit opětovné sestavení tak, že vytvoříte nové pole zadejte definici upravená a začít používat místo starý. Použití [aktualizaci indexu](https://docs.microsoft.com/rest/api/searchservice/update-index) začlenit nové pole a [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) ho naplnit. Později, v rámci index plánované údržby můžete vyčistit index odebrat zastaralé pole. |

## <a name="when-to-add-analyzers"></a>Přidání analyzátory

Nejvhodnější čas k přidání a přiřazení analyzátory je při aktivním vývoji při vyřadit a znovu vytvořit indexy je rutina.

Definice indexu ztuhne, můžete přidat nové konstruktory analýzy do indexu, ale je potřeba předat **allowIndexDowntime** příznak, který [aktualizaci indexu](https://docs.microsoft.com/rest/api/searchservice/update-index) Pokud budete chtít vyhnout se této chybě:

*"Index aktualizace není povolená, protože by to způsobilo výpadek. Pokud chcete přidat nové analyzátory, tokenizátory, token filtry nebo znak filtry do existujícího indexu, nastavte parametr dotazu 'allowIndexDowntime' na 'true' v žádosti o aktualizaci indexu. Všimněte si, že tato operace zařadí indexu v režimu offline pro alespoň několik sekund, způsobuje vaše indexování a k selhání požadavků na dotazy. Výkon a zápis dostupnost indexu může být narušena několik minut, po aktualizaci indexu, nebo delší dobu velmi velký indexy."*

To samé platí při přiřazování analyzátor pro pole. Analyzátor je nedílnou součástí definice pole, takže můžete přidat pouze ji při vytvoření pole. Pokud chcete přidat analyzátory pro existující pole, budete muset [vyřaďte a znovu sestavte](search-howto-reindex.md) indexu, nebo přidat nové pole pomocí analyzátoru chcete.

Jak je uvedeno, je výjimka **searchAnalyzer** variant. Ze tří způsobů, jak určit analyzátory (**analyzátor**, **indexAnalyzer**, **searchAnalyzer**), pouze **searchAnalyzer** atribut můžete změnit na existující pole.

## <a name="recommendations-for-working-with-analyzers"></a>Doporučení pro práci s analyzátory

Tato část nabízí Rady o tom, jak pracovat s analyzátory.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Jeden analyzátor pro čtení i zápis Pokud nemáte konkrétní požadavky

Služba Azure Search umožňuje určit různé analyzátory pro indexování a vyhledávání prostřednictvím dalších **indexAnalyzer** a **searchAnalyzer** pole parametrů. Pokud tento parametr zadán, analyzátor sadu s **analyzátor** vlastnost se používá pro indexování a vyhledávání. Pokud `analyzer` je tento parametr zadán, výchozí analyzátor Lucene standardní se používá.

Obecně je použití stejné analyzátoru pro indexování a dotazování, není-li konkrétní požadavky stanovují jinak. Třeba důkladně otestovat. Při zpracování textu se liší v hledání a indexování čas, vystavujete se riziku Neshoda mezi indexované termínů, pokud nejsou zarovnány hledání a indexování analyzátor konfigurace a dotaz.

### <a name="test-during-active-development"></a>Test při aktivním vývoji

Přepsání standardního analyzátor vyžaduje opětovné sestavení indexu. Rozhodněte, pokud je to možné, na které analyzátory pro použití při aktivním vývoji, před distribucí indexu do produkčního prostředí.

### <a name="inspect-tokenized-terms"></a>Kontrola tokenizovaná podmínky

Pokud se hledání nezdaří vrátit očekávané výsledky, je nejpravděpodobnější scénář token nesrovnalosti mezi termín vstupů v dotazu a tokenizovaná podmínky v indexu. Pokud tokeny nejsou stejné, odpovídá nepodaří sloučit. Chcete-li prověřit tokenizátor výstup, doporučujeme použít [analyzovat rozhraní API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) jako nástroj pro zkoumání. Odpověď se skládá z tokenů, generovaná konkrétní Analyzer.

<a name="examples"></a>

## <a name="rest-examples"></a>Příklady REST

Následující příklady ukazují analyzátor definice pro několik klíčových scénářů.

+ [Příklad vlastního analyzátoru](#Custom-analyzer-example)
+ [Pole, například přiřadit analyzátory](#Per-field-analyzer-assignment-example)
+ [Kombinování analyzátory pro indexování a vyhledávání](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Příklad analyzátoru jazyka](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Příklad vlastního analyzátoru

Tento příklad ukazuje definici analyzátoru s vlastními možnostmi. Vlastní možnosti pro filtry char, tokenizátory a token filtry se zadávají samostatně jako pojmenované konstrukcí a pak odkazuje v definici analyzátor. Předdefinované elementy se používají jako-je a jednoduše odkazovat podle názvu.

Provede v tomto příkladu:

* Analyzátory jsou vlastnosti třídy pole pro prohledávatelné pole.
* Vlastní analyzátor je součástí definice indexu. Pravděpodobně je lehce přizpůsobit (například přizpůsobení jednu možnost v jednom filtru) nebo přizpůsobit na více místech.
* V takovém případě je vlastní analyzátor "my_analyzer", která dále používá vlastní standardní tokenizátor "my_standard_tokenizer" a dva filtry token: malá písmena a přizpůsobené asciifolding filtr "my_asciifolding".
* Definuje také 2 vlastní char filtry "map_dash" a "remove_whitespace". První z nich nahradí všechny pomlčky podtržítky, zatímco druhá odebere všechny mezery. Mezery musí být v pravidlech mapování kódování UTF-8. Char filtry se použijí před Tokenizace a bude mít vliv na použitím výsledných tokenů (standardní tokenizátor zalomení na pomlčky a mezery, ale ne na podtržítko).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Příklad přiřazení analyzátor na pole

Výchozím nastavením je standardní analyzátor. Předpokládejme, že budete chtít nahradit výchozí pomocí různých předdefinovaných analyzátoru, jako je model analyzátoru. Pokud si nejste možnosti vlastního nastavení, stačí určit podle názvu v definici pole.

Prvek "analyzátor" přepisuje standardní analyzátor na základě pole pomocí pole. Neexistuje žádné globální přepsání. V tomto příkladu `text1` používá model analyzátoru a `text2`, která neurčuje analyzátor, používá výchozí.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Kombinování analyzátory pro operace indexování a vyhledávání

Rozhraní API obsahuje další index atributy pro určení jiné analyzátory pro indexování a vyhledávání. **SearchAnalyzer** a **indexAnalyzer** atributy musí být zadán jako dvojice, nahrazení jedné **analyzátor** atribut.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Příklad analyzátoru jazyka

Pole obsahující řetězce v různých jazycích můžete použít analyzátor jazyka, zatímco ostatní pole ponechte výchozí hodnotu (nebo pomocí některé jiné předdefinovaným nebo vlastním analyzátoru). Pokud používáte analyzátor jazyka, musí být použit pro operace indexování a vyhledávání. Pole, která lze použít analyzátor jazyka nemůže mít různé analyzátory pro indexování a vyhledávání.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="c-examples"></a>C#Příklady

Pokud používáte ukázky kódu .NET SDK, můžete přidat tyto příklady použít nebo nakonfigurovat analyzátory.

+ [Přiřazení předdefinované analyzátoru](#Assign-a-language-analyzer)
+ [Analyzátor konfigurace](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Přiřadit analyzátor jazyka

Žádné analyzátor, který se používá jako-je bez konfigurace, je zadána v definici pole. Neexistuje žádný požadavek pro vytváření konstrukce analyzátoru. 

Tento příklad přiřadí pole Popis analyzátory English Microsoft a francouzštinu. Je fragment kódu z větší definici indexu hotelů vytváření pomocí třídy hotelu v souboru hotels.cs [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) vzorku.

Volání [analyzátor](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), zadání [AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) typ poskytuje text analyzátor nepodporuje ve službě Azure Search.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Definovat vlastní analyzátor

Při přizpůsobení a konfigurace je vyžadováno, je potřeba přidat analyzátoru konstrukce do indexu. Jakmile je definujete, můžete přidat jeho definici pole jak je uvedeno v předchozím příkladu.

Vytvoření [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) objektu. Další příklady najdete v tématu [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/src/SDKs/Search/DataPlane/Search.Tests/Tests/CustomAnalyzerTests.cs).

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>Další postup

+ Projděte si naše komplexní vysvětlení [jak funguje fulltextové vyhledávání ve službě Azure Search](search-lucene-query-architecture.md). V tomto článku se používají příklady který vysvětluje chování, která se může zdát counter-intuitive na povrchu.

+ Zkuste syntaxi dalšího dotazu [vyhledávání dokumentů](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) vzorový oddíl nebo z [jednoduchá syntaxe dotazů](query-simple-syntax.md) v Průzkumníku služby Search na portálu.

+ Zjistěte, jak použít [specifické pro jazyk lexikální analyzátory](index-add-language-analyzers.md).

+ [Konfigurace vlastní analyzátory](index-add-custom-analyzers.md) pro minimální zpracování nebo speciální zpracování pro jednotlivá pole.

## <a name="see-also"></a>Další informace najdete v tématech

 [Hledání dokumentů rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Jednoduchá syntaxe dotazů](query-simple-syntax.md) 

 [Úplná syntaxe dotazů Lucene](query-lucene-syntax.md) 
 
 [Zpracování výsledků vyhledávání](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
