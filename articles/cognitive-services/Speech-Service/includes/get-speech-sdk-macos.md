---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: e7714dcc3e14e3659ca1943507d55d20dfb28fa6
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213896"
---
Při vývoji pro macOS jsou k dispozici tři sady Speech SDK.

- Sada SDK pro rozpoznávání řeči typu cíl-C je nativně k dispozici jako balíček CocoaPod.
- Sada .NET Speech SDK se dá použít s **Xamarin. Mac** , protože implementuje .NET Standard 2,0.
- Sada Python Speech SDK je k dispozici jako modul PyPI.

> [!TIP]
> Podrobné informace o použití sady SDK pro rozpoznávání řeči v cíli-C s SWIFT naleznete v tématu <a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">importování cíle – C do SWIFT <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

### <a name="system-requirements"></a>Požadavky na systém

- MacOS verze 10,13 nebo novější

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        Balíček CocoaPod macOS je k dispozici ke stažení a použití s integrovaným vývojovým prostředím (IDE) <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 (nebo novějším) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . Nejdřív <a href="https://aka.ms/csspeech/macosbinary" target="_blank">Stáhněte binární CocoaPod <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Rozbalte pole pod ve stejném adresáři pro zamýšlené použití, vytvořte *souboru podfile* a seznam `pod` jako `target` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.15.0'
end
```

# <a name="xamarinmac"></a>[Xamarin.Mac](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.Mac zpřístupňuje vývojářům .NET kompletní sadu macOS SDK pro vytváření nativních aplikací pro Mac pomocí jazyka C#. Další informace najdete v tématu <a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin. Mac <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>Další zdroje informací

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS Speech pro rychlý start – cíl – zdrojový kód C <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS Speech SDK pro rychlý Start – zdrojový kód SWIFT <span class="docon docon-navigate-external x-hidden-focus"></span></a>