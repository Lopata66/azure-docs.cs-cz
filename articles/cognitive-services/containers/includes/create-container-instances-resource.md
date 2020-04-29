---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak vytvořit prostředek Azure Container instance.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876403"
---
## <a name="create-an-azure-container-instance-resource"></a>Vytvoření prostředku Azure Container instance

1. Pro Container Instances přejít na stránku [vytvořit](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) .

2. Na kartě **základy** zadejte následující podrobnosti:

    |Nastavení|Hodnota|
    |--|--|
    |Předplatné|Vyberte své předplatné.|
    |Skupina prostředků|Vyberte dostupnou skupinu prostředků nebo vytvořte novou, například `cognitive-services`.|
    |Název kontejneru|Zadejte název, například `cognitive-container-instance`. Název musí být malými písmeny.|
    |Umístění|Vyberte oblast pro nasazení.|
    |Typ image|Pokud je vaše image kontejneru uložená v registru kontejneru, který nevyžaduje přihlašovací údaje, vyberte `Public`. Pokud přístup k imagi kontejneru vyžaduje přihlašovací údaje, `Private`vyberte. Podrobnosti o tom, zda je `Public` nebo není bitová kopie kontejneru nebo `Private` ("Public Preview"), naleznete v [úložištích kontejnerů a obrázcích](../../cognitive-services-container-support.md#container-repositories-and-images) . |
    |Název image|Zadejte umístění kontejneru Cognitive Services. Umístění se používá jako argument `docker pull` příkazu. Podívejte se na [úložiště kontejnerů a obrázky](../../cognitive-services-container-support.md#container-repositories-and-images) pro dostupné názvy imagí a jejich odpovídající úložiště.<br><br>Název Image musí být plně kvalifikovaný, aby se zadaly tři části. Nejprve registr kontejneru, potom úložiště, nakonec název bitové kopie: `<container-registry>/<repository>/<image-name>`.<br><br>Tady je příklad, `mcr.microsoft.com/azure-cognitive-services/keyphrase` který představuje extrakce klíčových frází image v Microsoft Container Registry v úložišti Azure Cognitive Services. Dalším příkladem je `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` , který představuje řeč k textovému obrázku v úložišti Microsoft úložiště kontejneru kontejnerů ve verzi Preview kontejneru. |
    |Typ operačního systému|`Linux`|
    |Velikost|Změňte velikost na navrhovaná doporučení pro konkrétní kontejner služby pro rozpoznávání:<br>2 jádra procesoru<br>4 GB

3. Na kartě **sítě** zadejte následující podrobnosti:

    |Nastavení|Hodnota|
    |--|--|
    |Porty|Nastavte port TCP na `5000`. Zpřístupňuje kontejner na portu 5000.|

4. Na kartě **Upřesnit** zadejte požadované **proměnné prostředí** pro nastavení fakturace kontejneru prostředku Azure Container instance:

    | Key | Hodnota |
    |--|--|
    |`apikey`|Zkopírováno ze stránky **klíče** v prostředku. Je to 32 alfanumerický znak řetězce bez mezer nebo pomlček, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Zkopírováno ze stránky **Přehled** prostředku.|
    |`eula`|`accept`|

5. Klikněte na tlačítko **zkontrolovat a vytvořit**
6. Po úspěšném ověření kliknutím na **vytvořit** dokončete proces vytváření.
7. Po úspěšném nasazení prostředku je jeho připravenost
