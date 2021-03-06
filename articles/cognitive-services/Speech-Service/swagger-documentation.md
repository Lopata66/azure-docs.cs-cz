---
title: Dokumentace k Swagger – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Dokumentaci Swagger lze použít k automatickému generování sad SDK pro řadu programovacích jazyků. Swagger podporuje všechny operace v naší službě.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 6bb50e427fa85a170c5ad23a63d67c01e898a17d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665680"
---
# <a name="swagger-documentation"></a>Dokumentace ke Swaggeru

Služba rozpoznávání řeči nabízí specifikace Swagger pro interakci s několik rozhraní REST API, která se používají k importu dat, vytváření modelů, přesnosti testovacích modelů, vytváření vlastních koncových bodů, zařazování přepisů dávek a správě předplatných. Většinu operací, které jsou dostupné prostřednictvím portálu Custom Speech, se dají pomocí těchto rozhraní API dokončit programově.

> [!NOTE]
> Operace převodu řeči na text a převod textu na řeč jsou podporované jako rozhraní REST API, která jsou dále popsána ve specifikaci Swagger.

## <a name="generating-code-from-the-swagger-specification"></a>Generování kódu ze specifikace Swagger

[Specifikace Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) obsahuje možnosti, které umožňují rychlé testování různých cest. Někdy je však žádoucí vygenerovat kód pro všechny cesty a vytvořit jedinou knihovnu volání, na kterých můžete založit budoucí řešení. Pojďme se podívat na proces generování knihovny Pythonu.

Musíte nastavit Swagger na stejnou oblast jako vaše předplatné služby Speech. Oblast můžete v Azure Portal ověřit pod prostředkem služby Speech Service. Úplný seznam podporovaných oblastí najdete v tématu [oblasti](regions.md).

1. V prohlížeči přejdete na specifikaci Swagger pro vaši oblast:  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Na této stránce klikněte na možnost **definice rozhraní API**a klikněte na **Swagger**. Zkopírujte adresu URL stránky, která se zobrazí.
1. V novém prohlížeči, přejít na https://editor.swagger.io
1. Klikněte na možnost **soubor**, klikněte na položku **importovat adresu URL**, vložte adresu URL a klikněte na tlačítko **OK**.
1. Klikněte na **vygenerovat klienta** a vyberte **Python**. Klientská knihovna se stáhne do vašeho počítače v `.zip` souboru.
1. Extrahujte vše ze staženého souboru. Můžete použít `tar -xf` k extrakci všeho.
1. Do prostředí Pythonu nainstalujte extrahovaný modul:  
       `pip install path/to/package/python-client`
1. Instalovaný balíček je pojmenován `swagger_client` . Ověřte, že instalace byla úspěšná:  
       `python -c "import swagger_client"`

Můžete použít knihovnu Python, kterou jste vygenerovali pomocí [ukázek služby pro rozpoznávání řeči na GitHubu](https://aka.ms/csspeech/samples).

## <a name="reference-docs"></a>Referenční dokumenty

* [REST (Swagger): dávkový přepis a přizpůsobení](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [REST API: Převod řeči na text](rest-speech-to-text.md)
* [REST API: převod textu na řeč](rest-text-to-speech.md)

## <a name="next-steps"></a>Další kroky

* [Ukázky služby Speech na GitHubu](https://aka.ms/csspeech/samples).
* [Získejte zdarma klíč předplatného služby Speech](overview.md#try-the-speech-service-for-free)
