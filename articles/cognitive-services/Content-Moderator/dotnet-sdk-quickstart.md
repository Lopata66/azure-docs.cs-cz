---
title: 'Rychlý Start: Klientská knihovna Content Moderator pro .NET'
titleSuffix: Azure Cognitive Services
description: Začněte s tímto rychlým startem pomocí klientské knihovny Content Moderator pro .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: dccd2ebb6ac2c11e19e986d39eabda5f0ab6a8fc
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774293"
---
# <a name="quickstart-content-moderator-client-library-for-net"></a>Rychlý Start: Klientská knihovna Content Moderator pro .NET

Začněte s klientskou knihovnou Content Moderator pro .NET. Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy. Content Moderator je služba rozpoznávání, která kontroluje text, obrázky a obsah videa pro materiál, který je potenciálně urážlivý, rizikové nebo jinak nežádoucí. Pokud se takový materiál najde, použije služba pro obsah odpovídající popisky (příznaky). Vaše aplikace pak může zpracovávat obsah označený příznakem v dodržování předpisů nebo spravovat zamýšlené prostředí pro uživatele.

Pomocí klientské knihovny Content Moderator pro .NET:

* [Střední text](#moderate-text)
* [Střední obrázky](#moderate-images)
* [Vytvořit recenzi](#create-a-review)

[Referenční dokumentace](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-dotnet) | [Ukázka](https://docs.microsoft.com/azure/cognitive-services/content-moderator/samples-dotnet) | [ukázka zdrojového kódu knihovny](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [balíčků (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* Aktuální verze [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-content-moderator-azure-resource"></a>Vytvoření prostředku Azure Content Moderator

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro Content Moderator pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači. Můžete také:

* Získejte [zkušební klíč](https://azure.microsoft.com/try/cognitive-services/#decision) platný po dobu sedmi dnů zdarma. Po registraci bude k dispozici na [webu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Zobrazení prostředku na [Azure Portal](https://portal.azure.com/)

Až dostanete klíč ze zkušebního předplatného nebo prostředku, [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro klíčovou adresu URL klíče a koncového bodu s názvem `CONTENT_MODERATOR_SUBSCRIPTION_KEY` a `CONTENT_MODERATOR_ENDPOINT`v uvedeném pořadí.

### <a name="create-a-new-c-application"></a>Vytvoření nové C# aplikace

Vytvořte novou aplikaci .NET Core v upřednostňovaném textovém editoru nebo v integrovaném vývojovém prostředí. 

V okně konzoly (například cmd, PowerShell nebo bash) použijte příkaz `dotnet new` a vytvořte novou konzolovou aplikaci s názvem `content-moderator-quickstart`. Tento příkaz vytvoří jednoduchý projekt "Hello World" C# s jedním zdrojovým souborem: *program.cs*.

```console
dotnet new console -n content-moderator-quickstart
```

Změňte adresář na nově vytvořenou složku aplikace. Aplikaci můžete vytvořit pomocí:

```console
dotnet build
```

Výstup sestavení by neměl obsahovat žádná upozornění ani chyby. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

V adresáři projektu otevřete soubor *program.cs* v preferovaném editoru nebo integrovaném vývojovém prostředí (IDE). Přidejte následující příkazy `using`:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_using)]

Ve třídě **program** vytvořte proměnné pro umístění a klíč koncového bodu prostředku jako proměnné prostředí.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_creds)]

> [!NOTE]
> Pokud jste po spuštění aplikace vytvořili proměnné prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k proměnným.

### <a name="install-the-client-library"></a>Instalace klientské knihovny

V adresáři aplikace nainstalujte Content Moderator klientskou knihovnu pro .NET pomocí následujícího příkazu:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

Pokud používáte integrované vývojové prostředí (IDE) sady Visual Studio, je knihovna klienta k dispozici jako balíček NuGet ke stažení.

## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají některé hlavní funkce sady Content Moderator .NET SDK.

|Name (Název)|Popis|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient?view=azure-dotnet)|Tato třída je potřebná pro všechny funkce Content Moderator. Vytvoří se jeho instance s informacemi o předplatném a Vy ho použijete k vytvoření instancí jiných tříd.|
|[ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet)|Tato třída poskytuje funkce pro analýzu obrázků pro obsah pro dospělé, osobní údaje nebo lidské obličeje.|
|[TextModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation?view=azure-dotnet)|Tato třída poskytuje funkce pro analýzu textu pro jazyk, vulgární výrazy, chyby a osobní údaje.|
|[Revize](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet)|Tato třída poskytuje funkce v rozhraních API pro revize, včetně metod vytváření úloh, vlastních pracovních postupů a lidských recenzí.|

## <a name="code-examples"></a>Příklady kódu


Tyto fragmenty kódu ukazují, jak provádět následující úlohy pomocí klientské knihovny Content Moderator pro .NET:

* [Ověření klienta](#authenticate-the-client)
* [Střední text](#moderate-text)
* [Střední obrázky](#moderate-images)
* [Vytvořit recenzi](#create-a-review)

## <a name="authenticate-the-client"></a>Ověření klienta

V nové metodě vytvořte instanci objektů klienta s vaším koncovým bodem a klíčem. Nepotřebujete pro každý scénář jiného klienta, ale může vám to usnadnit uspořádání kódu.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_client)]

## <a name="moderate-text"></a>Střední text

Následující kód používá klienta Content Moderator k analýze textu a k vytištění výsledků do konzoly. V kořenovém adresáři třídy **programu** definujte vstupní a výstupní soubory:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_text_vars)]

Potom v kořenovém adresáři projektu a přidejte soubor *textfile. txt* . Přidejte do tohoto souboru vlastní text nebo použijte následující vzorový text:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Do metody `Main` přidejte následující volání metody:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

Pak definujte metodu moderování textu někde ve vaší třídě **programu** :

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Střední obrázky

Následující kód používá klienta Content Moderator společně s objektem [ImageModeration](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation?view=azure-dotnet) k analýze vzdálených imagí pro dospělé a pikantní obsah.

> [!NOTE]
> Můžete také analyzovat obsah místní image. V [referenční dokumentaci](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) najdete metody a operace, které fungují s místními imagemi.

### <a name="get-sample-images"></a>Získat ukázkové obrázky

Zadejte vstupní a výstupní soubory:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_image_vars)]

Pak vytvořte vstupní soubor *ImageFiles. txt*v kořenovém adresáři projektu. V tomto souboru přidáte adresy URL obrázků k analýze&mdash;jednu adresu URL na každém řádku. Můžete použít následující ukázkové obrázky:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

Předejte vstupní a výstupní soubory do následujícího volání metody v metodě `Main`. Tuto metodu definujete v pozdějším kroku.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_textmod_call)]

### <a name="define-helper-class"></a>Definovat pomocnou třídu

Do třídy **program** přidejte následující definici třídy. Tato vnitřní třída zpracuje výsledky moderování obrázku.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>Definovat metodu moderování obrázku

Následující metoda prochází pomocí adres URL obrázků v textovém souboru, vytvoří instanci **EvaluationData** a analyzuje obrázek pro dospělé/pikantní obsah, text a lidské obličeje. Pak přidá poslední instanci **EvaluationData** do seznamu a zapíše úplný seznam vrácených dat do konzoly.

#### <a name="iterate-through-image-urls"></a>Iterace pomocí adres URL imagí

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Analýza obsahu

Další informace o atributech obrázků, které Content Moderator obrazovky pro, najdete v příručce [Koncepty pro moderování imagí](./image-moderation-api.md) .

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Zapsat výsledky moderování do souboru

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Vytvořit recenzi

Sadu Content Moderator .NET SDK můžete použít k zakládání obsahu na [Nástroj pro revize](https://contentmoderator.cognitive.microsoft.com) , aby ho lidé mohli zkontrolovat. Další informace o nástroji pro kontrolu najdete v [koncepční příručce k nástroji pro kontrolu](./review-tool-user-guide/human-in-the-loop.md).

Metoda v tomto oddílu používá třídu [Reviews](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews?view=azure-dotnet) k vytvoření revize, načtení jeho ID a kontrolu jeho podrobností po přijetí lidského vstupu prostřednictvím webového portálu nástroje pro kontrolu. Protokoluje všechny tyto informace ve výstupním textovém souboru. Zavolejte metodu z vaší `Main` metody:

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_call)]

### <a name="get-sample-images"></a>Získat ukázkové obrázky

Deklarujete následující pole v kořenovém adresáři vaší třídy **programu** . Tato proměnná odkazuje na vzorový obrázek, který se má použít k vytvoření revize.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Získat pověření pro kontrolu

Přihlaste se k [nástroji pro revizi](https://contentmoderator.cognitive.microsoft.com) a načtěte název svého týmu. Pak ji přiřaďte příslušné proměnné ve třídě **program** . Volitelně můžete nastavit koncový bod zpětného volání pro příjem aktualizací aktivity revize.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Definovat pomocnou třídu

Do třídy **programu** přidejte následující definici třídy. Tato třída bude sloužit k reprezentaci jedné instance revize, která je odeslána do nástroje pro revize.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Definovat pomocnou metodu

Do třídy **Program** přidejte následující metodu. Tato metoda zapíše výsledky kontroly dotazů do výstupního textového souboru.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>Definice metody vytváření Revize

Nyní jste připraveni definovat metodu, která bude zpracovávat vytváření a dotazování na revizi. Přidejte novou metodu, **CreateReviews**a definujte následující místní proměnné.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Zveřejnění revizí nástroje pro kontrolu

Pak přidejte následující kód pro iteraci pomocí daných ukázkových imagí, přidejte metadata a odešlete je do nástroje pro kontrolu v rámci jedné dávky. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_create)]

Objekt vrácený voláním rozhraní API bude obsahovat jedinečné hodnoty ID pro každý nahraný obrázek. Následující kód analyzuje tato ID a pak je používá k dotazování Content Moderator pro stav jednotlivých imagí v dávce.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Získat podrobnosti o kontrole

Následující kód způsobí, že program čeká na vstup uživatele. Když do tohoto kroku přejdete za běhu, můžete přejít na nástroj pro [kontrolu](https://contentmoderator.cognitive.microsoft.com) sami, ověřit, jestli se ukázková image nahrála, a s ní pracovat. Informace o tom, jak pracovat s revizí, najdete v [příručce](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images)k recenzi. Až budete hotovi, můžete stisknutím libovolné klávesy pokračovat v programu a načíst výsledky procesu kontroly.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/ContentModerator/Program.cs?name=snippet_createreview_results)]

Pokud jste v tomto scénáři použili koncový bod zpětného volání, měla by se zobrazit událost v tomto formátu:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci z adresáře aplikace pomocí příkazu `dotnet run`.

```dotnet
dotnet run 
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak pomocí knihovny Content Moderator .NET provádět úlohy moderování. V dalším kroku se dozvíte další informace o moderování imagí nebo jiných médií. Přečtěte si koncepční průvodce.

> [!div class="nextstepaction"]
> [Koncepty moderování obrázků](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Co je Azure Content Moderator?](./overview.md)
* Zdrojový kód pro tuto ukázku najdete na [GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/ContentModerator/Program.cs)
