---
title: ResNet
titleSuffix: Azure Machine Learning
description: Naučte se vytvářet model klasifikace obrázků pomocí algoritmu ResNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: ea8c13e134eceeb27bd064e794d46d711092a867
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84450728"
---
# <a name="resnet"></a>ResNet

Tento článek popisuje, jak pomocí modulu **ResNet** v Návrháři Azure Machine Learning (Preview) vytvořit model klasifikace obrázků pomocí algoritmu ResNet..  

Tento algoritmus klasifikace je metoda učení pod dohledem a vyžaduje datovou sadu s popiskem. Další informace o tom, jak získat adresář obrázků s popiskem, najdete v tématu [převod na modul adresáře imagí](convert-to-image-directory.md) . Model můžete vytvořit tak, že poskytnete model a s popiskem adresář obrázků jako vstupy pro [výuku modelu Pytorch](train-pytorch-model.md). Školený model se pak dá použít k předpovědi hodnot pro nové vstupní příklady pomocí [modelu obrázku skóre](score-image-model.md).

### <a name="more-about-resnet"></a>Další informace o ResNet

Další podrobnosti o ResNet najdete v [tomto dokumentu](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d) .

## <a name="how-to-configure-resnet"></a>Jak nakonfigurovat ResNet

1.  Přidejte modul **ResNet** do kanálu v návrháři.  

2.  V části **název modelu**zadejte název určité struktury ResNet a můžete vybrat z podporovaných ResNet: ' resnet18 ', ' resnet34 ', ' resnet50 ', ' resnet101 ', ' resnet152 ', ' resnet152 ', ' resnext50 \_ 32x4d ', ' resnext101 32x8d ', ' \_ wide_resnet50 \_ 2 ', ' wide_resnet101 \_ 2 '.

3.  Pro **předvlaky**určete, jestli se má používat model předběžně vyškolený v ImageNet. Pokud je tato možnost vybraná, můžete model vyladit podle vybraného předem připraveného modelu. Pokud je tento výběr nevybraný, můžete vytvářet sestavy od začátku.

4.  Propojte výstup modulu **DenseNet** , školení a sady dat image ověřování v [modelu Pytorch pro analýzu](train-pytorch-model.md). 

5. Odešlete kanál.

## <a name="results"></a>Výsledky

Po dokončení běhu kanálu můžete použít model pro bodování, připojit [model Pytorch](train-pytorch-model.md) pro [vyhodnocení modelu obrázku](score-image-model.md)a předpovídat hodnoty pro nové vstupní příklady.

## <a name="technical-notes"></a>Technické poznámky  

###  <a name="module-parameters"></a>Parametry modulu  

| Name       | Rozsah | Typ    | Výchozí           | Description                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Název modelu | Všechny   | Mode    | resnext101 \_ 32x8d | Název určité struktury ResNet       |
| Předvlakované | Všechny   | Logická hodnota | True              | Jestli se má používat model předučený na ImageNet |
|            |       |         |                   |                                          |

###  <a name="output"></a>Výstup  

| Name            | Typ                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Nevlakový model | UntrainedModelDirectory | Nevlakový model ResNet, který se dá připojit ke Pytorch modelu výuky. |

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 