---
title: Informace o službách Cognitive Services řeči SDK
description: Přehled sady SDK k dispozici pro službu rozpoznávání řeči.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 09/24/2018
ms.author: v-jerkin
ms.openlocfilehash: 169d421ddccf33ac239b69ab78ca7dca0f0b8261
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958409"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Informace o službách Cognitive Services řeči SDK

Cognitive Services řeči Software Development Kit (SDK) poskytuje vaše aplikace nativní přístup k funkcím služby řeči, což usnadňuje vývoj softwaru. V současné době sada SDK poskytuje přístup k **převod řeči na Text**, **překlad řeči**, a **rozpoznávání záměru**.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Získání sady SDK

### <a name="windows"></a>Windows

Pro Windows podporuje následující jazyky:

* C# jazyka C++ (UPW a .NET): lze odkazovat a využívat nejnovější verzi naší balíček NuGet sady SDK pro řeč.
  Balíček obsahuje 32bitové a 64bitové klientské knihovny, jakož i spravované knihovny (.NET).
  Sada SDK lze nainstalovat v sadě Visual Studio pomocí nástroje NuGet; jednoduše vyhledejte `Microsoft.CognitiveServices.Speech`.

* Java: Můžete odkazovat a využívat nejnovější verzi naší řeči SDK Maven balíček, který podporuje pouze Windows x64.
  V projektu Maven, přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a odkaz `com.microsoft.cognitiveservices.speech:client-sdk:1.0.0` jako závislost. 

### <a name="linux"></a>Linux

> [!NOTE]
> V současné době podporujeme jenom Ubuntu 16.04 na počítači (x86 nebo x64 pro vývoj v jazyce C++, x64 pro jazyk Java a .NET Core).

Ujistěte se, že máte požadované kompilátor a knihovny instalaci spuštěním následujících příkazů prostředí:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: Můžete odkazovat a používat nejnovější verzi naší balíček NuGet sady SDK pro řeč.
  Pokud chcete odkazují na sadu SDK, přidejte do projektu následující odkaz na balíček:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.0.0" />
  ```

* Java: Můžete odkazovat a využívat nejnovější verzi naší řeči SDK Maven balíček.
  V projektu Maven, přidejte `https://csspeechstorage.blob.core.windows.net/maven/` jako další úložiště a odkaz `com.microsoft.cognitiveservices.speech:client-sdk:1.0.0` jako závislost. 

* Jazyk C++: Stáhněte si sadu SDK jako [instalačního balíčku .tar](https://aka.ms/csspeech/linuxbinary) a rozbalte soubory do adresáře podle vašeho výběru. V následující tabulce jsou uvedeny strukturu složek sady SDK.

  |Cesta|Popis|
  |-|-|
  |`license.md`|Licence|
  |`ThirdPartyNotices.md`|Oznámení třetích stran|
  |`include`|Soubory hlaviček pro jazyky C a C++|
  |`lib/x64`|Nativní x64 knihovna pro propojení s vaší aplikací|
  |`lib/x86`|Nativní x86 knihovna pro propojení s vaší aplikací|

  K vytvoření aplikace, zkopírovat nebo přesunout požadované binární soubory (a knihovny) do svého vývojového prostředí a zahrnout je podle potřeby do procesu sestavení.

### <a name="android"></a>Android

Sady Java SDK pro Android je zabalena jako [AAR (knihovna pro Android)](https://developer.android.com/studio/projects/android-library), který obsahuje potřebné knihovny, stejně jako požadovaná Android oprávnění k jeho používání.
Je hostován v úložiště Maven v `https://csspeechstorage.blob.core.windows.net/maven/` jako balíček `com.microsoft.cognitiveservices.speech:client-sdk:1.0.0`.
Chcete-li využívají balíček z vašeho projektu Android Studio, proveďte následující změny:

* Na úrovni projektu `build.gradle` přidejte následující kód do `repository` části:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* V úrovni modulu `build.gradle` přidejte následující kód do `dependencies` části:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.0.0'
  ```

Sada Java SDK je také součástí [sadou SDK pro řeč zařízení](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Zjistěte, jak rozpoznávat řeč v jazyce C#](quickstart-csharp-dotnet-windows.md)
