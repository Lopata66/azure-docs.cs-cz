---
title: 'Neuronové Network: odkaz na modul'
titleSuffix: Azure Machine Learning
description: Naučte se, jak pomocí modulu neuronové Network Module v Azure Machine Learning vytvořit model sítě neuronové, který se dá použít k předpovědi cíle, který má více hodnot.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 653b12ddd54c5ec9e4e7dd23a323f34460daa962
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920412"
---
# <a name="multiclass-neural-network-module"></a>Neuronové síťový modul pro více tříd

Tento článek popisuje modul v Návrháři Azure Machine Learning (Preview).

Tento modul použijte k vytvoření neuronové síťového modelu, který se dá použít k předpovědi cíle, který má více hodnot. 

Například neuronové sítě tohoto druhu se můžou používat ve složitých úlohách počítačové vize, jako je například rozpoznávání číslic nebo písmen, klasifikace dokumentů a rozpoznávání vzorků.

Klasifikace pomocí sítě neuronové je metoda učení pod dohledem a proto vyžaduje *tagované datové sady* , která obsahuje sloupec popisku.

Model můžete proškolit poskytnutím modelu a tagované datové sady jako vstupu pro [vlakový model](./train-model.md). K předpovědi hodnot pro nové příklady vstupu je pak možné použít trained model.  

## <a name="about-neural-networks"></a>O sítích neuronové

Neuronové síť je sada vzájemně propojených vrstev. Vstupy jsou první vrstva a jsou připojeny ke výstupní vrstvě pomocí grafu acyklického, který se skládá z váženého okraje a uzlů.

Mezi vstupní a výstupní vrstvou můžete vložit více skrytých vrstev. Většinu prediktivních úloh je možné snadno dosáhnout pouze pomocí jedné nebo několika skrytých vrstev. Poslední výzkum ale ukázal, že rozsáhlé sítě neuronové (DNN) s mnoha vrstvami můžou být efektivní v složitých úlohách, jako je například rozpoznávání obrázků nebo řeči. Po sobě jdoucí vrstvy se používají k modelování rostoucí úrovně sémantické hloubky.

Vztah mezi vstupy a výstupy se od školení sítě neuronové ke vstupním datům dozvěděl. Směr grafu pokračuje ze vstupů přes skrytou vrstvu a do výstupní vrstvy. Všechny uzly ve vrstvě jsou propojeny váženou hranou k uzlům v další vrstvě.

Chcete-li vypočítat výstup sítě pro konkrétní vstup, hodnota je vypočítána na každém uzlu ve skrytých vrstvách a ve výstupní vrstvě. Hodnota je nastavena výpočtem váženého součtu hodnot uzlů z předchozí vrstvy. Na tento vážená suma se pak aplikuje aktivační funkce.

## <a name="configure-multiclass-neural-network"></a>Nakonfigurovat neuronové síť s více třídami

1. Přidejte do kanálu neuronové síťový modul s více **třídami** v návrháři. Tento modul můžete najít v části **Machine Learning**, **Initialize**, v kategorii **klasifikace** .

2. **Vytvořit režim Trainer**: pomocí této možnosti určíte, jak chcete model vyškolet:

    - **Jeden parametr**: tuto možnost vyberte, pokud už víte, jak chcete model nakonfigurovat.

    - **Rozsah parametrů**: tuto možnost vyberte, pokud si nejste jisti nejlepšími parametry a chcete spustit mazání parametrů. Vyberte rozsah hodnot, na které se mají iterovat, a s [parametry modelu ladění](tune-model-hyperparameters.md) prochází všechny možné kombinace nastavení, které jste zadali, abyste určili, jaké parametry jsou výsledkem optimálních výsledků.  

3. **Skrytá specifikace vrstvy**: Vyberte typ síťové architektury, která se má vytvořit.

    - **Plně připojené případy**: tuto možnost vyberte, pokud chcete vytvořit model s použitím výchozí neuronové síťové architektury. Pro modely neuronové sítě s více třídami jsou výchozí hodnoty následující:

        - Jedna skrytá vrstva
        - Výstupní vrstva je plně připojená ke skryté vrstvě.
        - Skrytá vrstva je plně připojená ke vstupní vrstvě.
        - Počet uzlů ve vstupní vrstvě je určený počtem funkcí v školicích datech.
        - Počet uzlů ve skryté vrstvě může nastavit uživatel. Výchozí hodnota je 100.
        - Počet uzlů ve výstupní vrstvě závisí na počtu tříd.
  
   

5. **Počet skrytých uzlů**: Tato možnost umožňuje přizpůsobit počet skrytých uzlů ve výchozí architektuře. Zadejte počet skrytých uzlů. Výchozím nastavením je jedna skrytá vrstva s 100 uzly.

6. **Rychlost učení**: Definujte velikost kroku provedeného v každé iteraci před opravou. Větší hodnota pro studijní kurzy může způsobit, že se model konverguje rychleji, ale může vyhodnotit místní minima.

7. **Počet iterací učení**: Určete maximální počet pokusů, kolikrát by měl algoritmus zpracovat školicí případy.

8. **Průměr počátečních vah pro studium**: Určete tloušťky uzlů na začátku procesu učení.

9. **Potenciál**: zadejte váhu, která se má použít při učení pro uzly z předchozích iterací.
  
11. **Náhodně – příklady**: tuto možnost vyberte, pokud chcete mezi iteracemi přemíchat případy.

    Pokud zrušíte výběr této možnosti, jsou případy zpracovávány přesně stejným způsobem pokaždé, když spustíte kanál.

12. **Počáteční číslo počáteční**hodnoty: zadejte hodnotu, která se má použít jako počáteční hodnota, pokud chcete zajistit opakování v rámci spuštění stejného kanálu.

14. Připojte datovou sadu školení a jeden z [školicích modulů](module-reference.md): 

    - Pokud nastavíte **režim vytvořit Trainer** na **jeden parametr**, použijete [model výuky](train-model.md).  
  

## <a name="results"></a>Výsledky

Po dokončení školení:

- Pokud chcete uložit snímek výukového modelu, vyberte kartu **výstupy** na pravém panelu modulu **výuka modelu** . Výběrem ikony **Registrovat datovou sadu** uložte model jako opakovaně použitelný modul.

## <a name="next-steps"></a>Další kroky

Podívejte se na [sadu modulů, které jsou k dispozici](module-reference.md) pro Azure Machine Learning. 