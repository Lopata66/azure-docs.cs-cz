---
title: Vytvoření účtu služeb Cognitive Services na webu Azure Portal
titlesuffix: Azure Cognitive Services
description: Jak vytvořit účet Azure API služeb Cognitive Services na webu Azure Portal.
services: cognitive-services
author: garyericson
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: garye
ms.openlocfilehash: 831f1d22c4da215bed3ed55b659332aa3b57472b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145948"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Rychlý start: Vytvoření účtu služeb Cognitive Services na webu Azure Portal

V tomto rychlém startu se budete dozvíte, jak se zaregistrovat pro Azure Cognitive Services a vytvoření odběru služby jednoho nebo víc služeb. Tyto služby jsou reprezentované prostřednictvím Azure [prostředky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), které umožňují připojení k jedné či více rozhraní API kognitivních služeb Azure.

## <a name="prerequisites"></a>Požadavky

* Platné předplatné Azure. [Vytvoření účtu](https://azure.microsoft.com/free/) zdarma.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Vytvoření a přihlášení k odběru k prostředku Azure Cognitive Services

Než začnete, je důležité vědět, že existují dva typy předplatných Azure Cognitive Services. První je předplatné do jediné služby, jako je například pro počítačové zpracování obrazu nebo hlasových služeb. Předplatné jednoúčelovou je omezen na tento prostředek. Druhým je víc služeb předplatného pro Azure Cognitive Services. Toto předplatné umožňuje používat v rámci jednoho předplatného pro většinu služeb Azure Cognitive Services. Tato možnost také konsoliduje fakturace. Zobrazit [ceny služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) pro další informace.

>[!WARNING]
> V tuto chvíli tyto služby **není** podporovat víc služeb klíče: Nástroj QnA Maker, hlasových služeb, Custom Vision a detekce anomálií.

Následující části vás provede vytvořením jedné nebo víc služeb předplatného.


### <a name="multi-service-subscription"></a>Víc služeb předplatného

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a klikněte na tlačítko **+ vytvořit prostředek**.

    ![Vyberte rozhraní API služeb Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Na panelu hledání vyhledejte a zadejte: **Služby cognitive Services**.

    ![Vyhledejte Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

3. Vyberte **služeb Cognitive Services**.

    ![Vyberte služby Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplaceMulti.png)

3. Na **vytvořit** stránky, zadejte následující informace:

    |    |    |
    |--|--|
    | **Název** | Popisný název pro váš prostředek služeb cognitive services. Doporučujeme použít popisný název, například *MyCognitiveServicesAccount*. |
    | **Předplatné** | Vyberte jednu z dostupných předplatných Azure. |
    | **Umístění** | Umístění vaší instanci služby cognitive Services. Různá umístění mohou zavést latence, ale mít vliv na běhovou dostupnost vašeho prostředku. |
    | **Cenová úroveň** | Náklady na váš účet služeb Cognitive Services závisí na zvolené možnosti a využití. Další informace najdete v tématu rozhraní API [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Skupina prostředků** | [Skupiny prostředků Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) , která bude obsahovat váš prostředek služeb Cognitive Services. Můžete vytvořit novou skupinu nebo ho přidat do existující skupiny. |

    ![Vytvoření obrazovky prostředků](media/cognitive-services-apis-create-account/resource_create_screen_multi.png)

### <a name="single-service-subscription"></a>Jednoúčelovou předplatného

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a klikněte na tlačítko **+ vytvořit prostředek**.

    ![Vyberte rozhraní API služeb Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. V části webu Azure Marketplace vyberte **AI a strojové učení**. Pokud nevidíte službu, která vás zajímá, klikněte na **zobrazit všechny** Chcete-li zobrazit celý katalog rozhraní API služeb Cognitive Services.

    ![Vyberte rozhraní API služeb Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Na **vytvořit** stránky, zadejte následující informace:

    |    |    |
    |--|--|
    | **Název** | Popisný název pro váš prostředek služeb cognitive services. Doporučujeme použít popisný název, například *MyNameFaceAPIAccount*. |
    | **Předplatné** | Vyberte jednu z dostupných předplatných Azure. |
    | **Umístění** | Umístění vaší instanci služby cognitive Services. Různá umístění mohou zavést latence, ale mít vliv na běhovou dostupnost vašeho prostředku. |
    | **Cenová úroveň** | Náklady na váš účet služeb Cognitive Services závisí na zvolené možnosti a využití. Další informace najdete v tématu rozhraní API [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Skupina prostředků** | [Skupiny prostředků Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) , která bude obsahovat váš prostředek služeb Cognitive Services. Můžete vytvořit novou skupinu nebo ho přidat do existující skupiny. |

    ![Vytvoření obrazovky prostředků](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>Přístup k prostředku

> [!NOTE]
> Vlastníci předplatného můžete zakázat vytváření účtů služeb Cognitive Services pro skupiny prostředků a předplatná použitím [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)přiřazení definice zásady "není povolené typy prostředků" a určení **Microsoft.CognitiveServices/accounts** jako typ cílového prostředku.

Po vytvoření prostředku můžete přistupovat ji z řídicího panelu Azure Pokud je připnutý. V opačném případě se nachází v **skupiny prostředků**.

V prostředku služeb Cognitive Services, můžete použít adresu URL koncového bodu a klíče **přehled** části a začněte s vytvářením rozhraní API volá ve svých aplikacích.

![Obrazovka prostředky](media/cognitive-services-apis-create-account/resourceScreen.png)

Poznamenejte si umístění a klíče. Klíče můžete získat tak, že vyberete **klíče** pod **správy prostředků**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ověření požadavků ve službě Azure Cognitive Services](authentication.md)

## <a name="see-also"></a>Další informace najdete v tématech

* [Rychlé zprovoznění: Extrahujte rukou psaný text z obrázku](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Kurz: Vytvoření aplikace pro zjišťování a rámečku tváří v obrázku](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Vytvořit webovou stránku pro vlastní vyhledávání](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Integrace s využitím botu pomocí rozhraní Bot Framework Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
