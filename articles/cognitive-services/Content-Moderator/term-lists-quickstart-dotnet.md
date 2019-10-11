---
title: Kontrolovat text proti vlastnímu seznamu termínů C# v Content moderator
titleSuffix: Azure Cognitive Services
description: Způsob, jakým se má pro aplikace použít Content Moderator sada SDK pro C#střední text s vlastními seznamy termínů
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: sajagtap
ms.openlocfilehash: d1c2f8b06d333be23f25a2d150c23269bf84cd2e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242827"
---
# <a name="check-text-against-a-custom-term-list-in-c"></a>Kontrolovat text proti vlastnímu seznamu podmínek vC#

Výchozí globální seznam podmínek v Azure Content Moderator je postačující pro většinu potřeb Moderování obsahu. Může se ale stát, že se budete muset setkat s podmínkami, které jsou specifické pro vaši organizaci. Můžete například chtít označit názvy konkurence pro další revize. 

[Sadu Content moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) můžete použít k vytvoření vlastních seznamů podmínek pro použití s rozhraním API pro moderování textu.

Tento článek poskytuje informace a ukázky kódu, které vám pomohou začít používat sadu Content Moderator SDK pro .NET k těmto akcím:
- Vytvoří seznam.
- Přidejte do seznamu podmínek.
- Obrazovky s podmínkami v seznamu.
- Odstraní výrazy ze seznamu.
- Odstraní seznam.
- Upravit informace o seznamu.
- Aktualizuje index, aby byly změny v seznamu zahrnuty do nové kontroly.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="sign-up-for-content-moderator-services"></a>Zaregistrujte se do služby Content Moderator Services.

Předtím, než budete moci používat Content Moderator služby prostřednictvím REST API nebo sady SDK, budete potřebovat klíč předplatného. Přihlaste se k odběru služby Content Moderator v [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) , abyste ji mohli získat.

## <a name="create-your-visual-studio-project"></a>Vytvoření projektu sady Visual Studio

1. Přidejte do svého řešení nový projekt **konzolové aplikace (.NET Framework)** .

1. Pojmenujte projekt **TermLists**. Vyberte tento projekt jako jeden spouštěný projekt pro řešení.

### <a name="install-required-packages"></a>Nainstalovat požadované balíčky

Pro projekt TermLists nainstalujte následující balíčky NuGet:

- Microsoft. Azure. Cognitiveservices Account. ContentModerator
- Microsoft. REST. ClientRuntime
- Microsoft. REST. ClientRuntime. Azure
- Newtonsoft. JSON

### <a name="update-the-programs-using-statements"></a>Aktualizovat příkazy using programu

Přidejte následující příkazy `using`.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Vytvoření klienta Content Moderator

Přidejte následující kód, který vytvoří klienta Content Moderator pro vaše předplatné.

> [!IMPORTANT]
> Aktualizujte pole **a** a **CMSubscriptionKey** hodnotami identifikátoru vaší oblasti a klíče předplatného.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this 
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The region/location for your Content Moderator account, 
    /// for example, westus.
    /// </summary>
    private static readonly string AzureRegion = "YOUR API REGION";

    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureBaseURL =
        $"https://{AzureRegion}.api.cognitive.microsoft.com";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureBaseURL;
        return client;
    }
}
```

### <a name="add-private-properties"></a>Přidat soukromé vlastnosti

Přidejte následující soukromé vlastnosti do oboru názvů TermLists, program třídy.

```csharp
/// <summary>
/// The language of the terms in the term lists.
/// </summary>
private const string lang = "eng";

/// <summary>
/// The minimum amount of time, in milliseconds, to wait between calls
/// to the Content Moderator APIs.
/// </summary>
private const int throttleRate = 3000;

/// <summary>
/// The number of minutes to delay after updating the search index before
/// performing image match operations against the list.
/// </summary>
private const double latencyDelay = 0.5;
```

## <a name="create-a-term-list"></a>Vytvoření seznamu termínů

Vytvoříte seznam termínů pomocí **ContentModeratorClient. ListManagementTermLists. Create**. První parametr, který se má **vytvořit** , je řetězec, který obsahuje typ MIME, který by měl být "Application/JSON". Další informace najdete v referenčních informacích k [rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). Druhý parametr je **tělo** objektu, který obsahuje název a popis pro nový seznam termínů.

> [!NOTE]
> Každý seznam obsahuje maximálně **5 seznamů termínů** , které **nepřekračují 10 000 podmínek**.

Přidejte následující definici metody do oboru názvů TermLists, program třídy.

> [!NOTE]
> Váš klíč služby Content Moderator má omezení četnosti požadavků za sekundu (RPS). Pokud tento limit překročíte, sada SDK vyvolá výjimku s kódem chyby 429. Klíč bezplatné úrovně má RPS limit četnosti.

```csharp
/// <summary>
/// Creates a new term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The term list ID.</returns>
static string CreateTermList (ContentModeratorClient client)
{
    Console.WriteLine("Creating term list.");

    Body body = new Body("Term list name", "Term list description");
    TermList list = client.ListManagementTermLists.Create("application/json", body);
    if (false == list.Id.HasValue)
    {
        throw new Exception("TermList.Id value missing.");
    }
    else
    {
        string list_id = list.Id.Value.ToString();
        Console.WriteLine("Term list created. ID: {0}.", list_id);
        Thread.Sleep(throttleRate);
        return list_id;
    }
}
```

## <a name="update-term-list-name-and-description"></a>Aktualizovat název a popis seznamu termínů

Informace o seznamu termínů se aktualizují pomocí **ContentModeratorClient. ListManagementTermLists. Update**. První parametr, který se má **aktualizovat** , je ID seznamu termínů. Druhý parametr je typ MIME, který by měl být "Application/JSON". Další informace najdete v referenčních informacích k [rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685). Třetí parametr je **tělo** objektu, který obsahuje nový název a popis.

Přidejte následující definici metody do oboru názvů TermLists, program třídy.

```csharp
/// <summary>
/// Update the information for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="name">The new name for the term list.</param>
/// <param name="description">The new description for the term list.</param>
static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
{
    Console.WriteLine("Updating information for term list with ID {0}.", list_id);
    Body body = new Body(name, description);
    client.ListManagementTermLists.Update(list_id, "application/json", body);
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-term-to-a-term-list"></a>Přidat termín do seznamu termínů

Přidejte následující definici metody do oboru názvů TermLists, program třídy.

```csharp
/// <summary>
/// Add a term to the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="term">The term to add to the term list.</param>
static void AddTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
    client.ListManagementTerm.AddTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

## <a name="get-all-terms-in-a-term-list"></a>Získat všechny podmínky v seznamu termínů

Přidejte následující definici metody do oboru názvů TermLists, program třídy.

```csharp
/// <summary>
/// Get all terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to get all terms.</param>
static void GetAllTerms(ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
    Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
    TermsData data = terms.Data;
    foreach (TermsInList term in data.Terms)
    {
        Console.WriteLine(term.Term);
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="add-code-to-refresh-the-search-index"></a>Přidání kódu pro aktualizaci indexu hledání

Po provedení změn v seznamu termínů aktualizujete index vyhledávání, aby se změny projevily při příštím použití seznamu termínů k zadání textu obrazovky. To se podobá tomu, jak vyhledávací modul na ploše (Pokud je povolen) nebo webový vyhledávací modul průběžně aktualizuje svůj index, aby zahrnoval nové soubory nebo stránky.

Index vyhledávání seznamu termínů aktualizujete pomocí **ContentModeratorClient. ListManagementTermLists. RefreshIndexMethod**.

Přidejte následující definici metody do oboru názvů TermLists, program třídy.

```csharp
/// <summary>
/// Refresh the search index for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to refresh.</param>
static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
    client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
    Thread.Sleep((int)(latencyDelay * 60 * 1000));
}
```

## <a name="screen-text-using-a-term-list"></a>Text obrazovky používající seznam termínů

Zobrazíte text na obrazovce pomocí seznamu termínů s **ContentModeratorClient. TextModeration. ScreenText**, který přebírá následující parametry.

- Jazyk podmínek v seznamu termínů.
- Typ MIME, který může být "text/HTML", "text/XML", "text/Markdownu" nebo "text/prostý".
- Text na obrazovku
- Logická hodnota. Nastavte toto pole na **hodnotu true** , pokud chcete text před zablokováním vymezit.
- Logická hodnota. Nastavte toto pole na **true** , aby se v textu zjistily osobní identifikovatelné údaje (PII).
- ID seznamu termínů.

Další informace najdete v referenčních informacích k [rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f).

**ScreenText** vrátí objekt **obrazovky** , který má vlastnost **terms** , která obsahuje seznam podmínek, které Content moderator zjistila v prověřování. Všimněte si, že pokud Content Moderator nerozpoznal žádné výrazy během prověřování, má vlastnost **terms** hodnotu **null**.

Přidejte následující definici metody do oboru názvů TermLists, program třídy.

```csharp
/// <summary>
/// Screen the indicated text for terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to use to screen the text.</param>
/// <param name="text">The text to screen.</param>
static void ScreenText (ContentModeratorClient client, string list_id, string text)
{
    Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
    Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
    if (null == screen.Terms)
    {
        Console.WriteLine("No terms from the term list were detected in the text.");
    }
    else
    {
        foreach (DetectedTerms term in screen.Terms)
        {
            Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
        }
    }
    read.Sleep(throttleRate);
}
```

## <a name="delete-terms-and-lists"></a>Odstranění podmínek a seznamů

Odstranění termínu nebo seznamu je jednoduché. Pomocí sady SDK můžete provádět následující úlohy:

- Odstraňte termín. (**ContentModeratorClient. ListManagementTerm. DeleteTerm**)
- Odstraní všechny výrazy v seznamu bez odstranění seznamu. (**ContentModeratorClient. ListManagementTerm. DeleteAllTerms**)
- Odstraní seznam a veškerý jeho obsah. (**ContentModeratorClient. ListManagementTermLists. Delete**)

### <a name="delete-a-term"></a>Odstranit termín

Přidejte následující definici metody do oboru názvů TermLists, program třídy.

```csharp
/// <summary>
/// Delete a term from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete the term.</param>
/// <param name="term">The term to delete.</param>
static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
    client.ListManagementTerm.DeleteTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-all-terms-in-a-term-list"></a>Odstranit všechny podmínky v seznamu termínů

Přidejte následující definici metody do oboru názvů TermLists, program třídy.

```csharp
/// <summary>
/// Delete all terms from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete all terms.</param>
static void DeleteAllTerms (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
    client.ListManagementTerm.DeleteAllTerms(list_id, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-a-term-list"></a>Odstranit seznam termínů

Přidejte následující definici metody do oboru názvů TermLists, program třídy.

```csharp
/// <summary>
/// Delete the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to delete.</param>
static void DeleteTermList (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Deleting term list with ID {0}.", list_id);
    client.ListManagementTermLists.Delete(list_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="compose-the-main-method"></a>Vytvoření metody Main

Přidejte definici **Main** metody do oboru názvů **TermLists**, **program**třídy. Nakonec zavřete třídu **program** a obor názvů **TermLists** .

```csharp
static void Main(string[] args)
{
    using (var client = Clients.NewClient())
    {
        string list_id = CreateTermList(client);

        UpdateTermList(client, list_id, "name", "description");
        AddTerm(client, list_id, "term1");
        AddTerm(client, list_id, "term2");

        GetAllTerms(client, list_id);

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        string text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteTerm(client, list_id, "term1");

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteAllTerms(client, list_id);
        DeleteTermList(client, list_id);

        Console.WriteLine("Press ENTER to close the application.");
        Console.ReadLine();
    }
}
```

## <a name="run-the-application-to-see-the-output"></a>Spusťte aplikaci, aby se zobrazil výstup

Výstup vaší konzoly bude vypadat následovně:

```console
Creating term list.
Term list created. ID: 252.
Updating information for term list with ID 252.

Adding term "term1" to term list with ID 252.
Adding term "term2" to term list with ID 252.

Getting terms in term list with ID 252.
term1
term2

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term1" from list ID 252 at index 32.
Found term: "term2" from list ID 252 at index 46.

Removed term "term1" from term list with ID 252.

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term2" from list ID 252 at index 46.

Removing all terms from term list with ID 252.
Deleting term list with ID 252.
Press ENTER to close the application.
```

## <a name="next-steps"></a>Další kroky

Získejte [Content moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a řešení sady [Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) pro tuto a další Content moderator rychlý Start pro .NET a začněte s integrací.
