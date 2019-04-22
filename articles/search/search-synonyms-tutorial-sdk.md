---
title: Synonyma C# příklad – Azure Search
description: V tomto C# například informace o postupu přidání funkce synonym do indexu ve službě Azure Search. Mapa synonym je seznam ekvivalentní termíny. Pole s podporou synonymum rozbalte dotazy obsahovat uživatelem zadaný výraz a všech souvisejících synonym.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f9cadfcf0c027f4aec4f9d4928872709ee7d3e99
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59008965"
---
# <a name="example-add-synonyms-for-azure-search-in-c"></a>Příklad: Přidání synonym pro službu Azure Search vC#

Synonyma rozšiřují dotazy hledáním shody s termíny, které jsou považované za sémantické ekvivalenty vstupního výrazu. Chcete třeba, aby položce auto odpovídaly i dokumenty obsahující termíny automobil a vozidlo. 

Ve službě Azure Search se synonyma definují v *mapě synonym*, pomocí *pravidel mapování*, která přidružují ekvivalentní termíny. Tento příklad popisuje základní kroky pro přidání a synonyma pomocí existujícího indexu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolení synonym vytvořením a odesláním pravidel mapování 
> * Odkázání na mapu synonym v řetězci dotazu

Můžete vytvořit několik map synonym, zveřejnit je jako prostředky na úrovni služby dostupné pro všechny indexy a potom určit, který se má použít na úrovni pole. V době zpracování dotazu služba Azure Search nejenom prohledá index, ale pokud některé z polí využitých v tomto dotazu má mapu synonym, prohledá i tuto mapu.

> [!NOTE]
> Synonyma jsou podporovaná v nejnovější verzi rozhraní API a sady SDK (verze api=2017-11-11, verze SDK 5.0.0). Podpora webu Azure Portal se v současnosti neposkytuje. Pokud byste uvítali podporu synonym na portálu Azure Portal, sdělte nám svůj názor na webu [UserVoice](https://feedback.azure.com/forums/263029-azure-search)

## <a name="prerequisites"></a>Požadavky

Požadavky kurzu zahrnují tyto položky:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Služba Azure Search](search-create-service-portal.md)
* [Knihovna Microsoft.Azure.Search .NET](https://aka.ms/search-sdk)
* [Jak používat Azure Search z aplikace .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Přehled

Dotazy před a po ukazují význam použití synonym. V tomto příkladu použijte ukázkovou aplikaci, která spustí dotazy a vrátí výsledky v ukázkovém indexu. Ukázková aplikace vytvoří malý index s názvem hotels naplněný dvěma dokumenty. Aplikace spustí vyhledávací dotazy s využitím termínů a frází, které nejsou v indexu uvedené, povolí funkci synonym a potom znovu provede stejné hledání. Uvedený kód ukazuje celkový tok.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
Postup vytvoření a naplnění ukázkového indexu jsou vysvětlené v tématu [Jak používat Azure Search z aplikace .NET](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

## <a name="before-queries"></a>Dotazy „před“

V `RunQueriesWithNonExistentTermsInIndex` uveďte vyhledávací dotazy pro „five star“, „internet“ a „economy AND hotel“.
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Ani jeden z indexovaných dokumentů tyto termíny neobsahuje, proto z prvního volání `RunQueriesWithNonExistentTermsInIndex` dostáváme tento výstup.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Povolení synonym

Povolení synonyma je dvoustupňový proces. Nejdřív nadefinujeme a nahrajeme pravidla synonym a potom nakonfigurujeme pole pro jejich použití. Proces je popsaný v `UploadSynonyms` a `EnableSynonymsInHotelsIndex`.

1. Přidejte mapu synonym k vaší vyhledávací službě. V `UploadSynonyms` definujeme čtyři pravidla v mapě synonym desc-synonymmap a nahrajeme je do služby.
   ```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
   ```
   Mapa synonym musí odpovídat opensourcovému standardnímu formátu `solr`. Tento formát je vysvětlený v tématu [Synonyma ve službě Azure Search](search-synonyms.md) v části `Apache Solr synonym format`.

2. Nakonfigurujte prohledávatelná pole tak, aby používala mapu synonym v definici indexu. V `EnableSynonymsInHotelsIndex` povolíme synonyma u dvou polí `category` a `tags` nastavením vlastnosti `synonymMaps` na název nově nahrané mapy synonym.
   ```csharp
   Index index = serviceClient.Indexes.Get("hotels");
   index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
   index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

   serviceClient.Indexes.CreateOrUpdate(index);
   ```
   Po přidání mapy synonym není potřeba znovu sestavovat index. Mapu synonymum můžete přidat ke službě a potom změnit stávající definice pole v libovolném indexu tak, aby tuto novou mapu synonym používala. Přidání nových atributů nemá žádný vliv na dostupnost indexu. To samé platí i pro zákaz synonym u pole. Stačí nastavit vlastnost `synonymMaps` na prázdný seznam.
   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
   ```

## <a name="after-queries"></a>Dotazy „po“

Po nahrání mapy synonym a aktualizaci indexu druhé volání `RunQueriesWithNonExistentTermsInIndex` vrátí tento výstup:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
První dotaz najde dokument z pravidla `five star=>luxury`. Druhý dotaz rozšíří vyhledávání pomocí `internet,wifi` a třetí použije k vyhledání shody v dokumentech `hotel, motel` i `economy,inexpensive=>budget`.

Přidání synonym úplně mění možnosti vyhledávání. V tomto příkladu se nepodařilo vrátit smysluplné výsledky, i když dokumenty v indexu byly relevantní původním dotazům. Povolením synonym můžeme rozšířit index tak, aby zahrnoval běžně používané termíny, a nemusíme přitom nijak upravovat podkladová data v indexu.

## <a name="sample-application-source-code"></a>Zdrojový kód ukázkové aplikace
Úplný zdrojový kód ukázkové aplikace použité v tomto názorném postupu najdete na [Githubu](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nejrychlejší způsob, jak vyčistit po příkladem je tak, že odstraníte skupinu prostředků obsahující službu Azure Search. Odstraněním skupiny prostředků teď můžete trvale odstranit všechno, co se v ní nachází. Název příslušné skupiny prostředků najdete na portálu na stránce Přehled služby Azure Search.

## <a name="next-steps"></a>Další postup

V tomto příkladu jsme vám ukázali [rozhraní REST API pro synonyma](https://aka.ms/rgm6rq) v C# kód k vytvoření a odesílání pravidla mapování a poté zavolejte mapu synonym v dotazu. Další informace najdete v referenční dokumentaci [sady .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) a [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/).

> [!div class="nextstepaction"]
> [Synonyma ve službě Azure Search](search-synonyms.md)
