---
title: Automatizované výběr algoritmů ML a laděním
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak služba Azure Machine Learning můžete automaticky vyberte algoritmus pro vás a generovat model z něj šetřit čas pomocí parametrů a kritéria, které poskytnete vybrat nejlepší algoritmus pro model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: bf010e33a5ef77fcfde2506bfef9760a09667a9d
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867248"
---
# <a name="what-is-automated-machine-learning"></a>Co je automatické machine learning?

Automatizované machine learning je proces trvá trénovacích dat pomocí funkce definovanému cíli a provede iterace přes kombinace algoritmy a výběry funkcí automaticky vybrat nejlepší model pro data podle skóre školení. Tradiční strojového učení proces vývoje modelu je velmi náročná a vyžaduje investice významné domény znalostní báze a čas ke spuštění a porovnávat výsledky desítky modely. Automatizované strojového učení zjednodušuje tento proces vygenerováním modelů, která je vyladěná z cíle a omezení, které jste definovali pro experiment, jako je čas potřebný pro experiment ke spuštění nebo které modelů blokovaných.

## <a name="how-it-works"></a>Jak to funguje

1. Nakonfigurujete typ problému machine learning, který se snažíte vyřešit. Kategorie učení se podporují:
   + Klasifikace
   + Regrese
   + Prognózování

   Při automatizované machine learning je obecně dostupná, **prognóz funkce je stále ve verzi public preview.**

   Zobrazit [seznamu modelů](how-to-configure-auto-train.md#select-your-experiment-type) Azure Machine Learning můžete zkusit při cvičení.

1. Můžete zadat zdroj a formát pro trénovací data. Data musí být označeny a mohou být uloženy ve svém vývojovém prostředí nebo ve službě Azure Blob Storage. Pokud jsou data uložená ve svém vývojovém prostředí, musí být ve stejném adresáři jako trénovací skripty. Tento adresář je zkopírován do cílové výpočetní prostředí, které vyberete pro vzdělávání.

    Ve skriptu školení lze data načíst do Numpy pole nebo Pandas dataframe.

    Můžete provést konfiguraci možností rozdělení pro výběr školení a ověření dat, nebo můžete zadat samostatné školení a ověřování datových sad.

1. Konfigurace [cílové výpočetní prostředí](how-to-set-up-training-targets.md) , který se používá pro trénování modelu.

1. Nakonfigurujte konfiguraci automatizované machine learning. Tato volba určuje parametry používané jako Azure Machine Learning Iteruje přes různé modely hyperparameter nastavení, a které metriky se podívat na při určení nejvhodnějšího modelu

1. Odešlete školení spustit.

Při školení, vytvoří službu Azure Machine Learning počet kanálů, které se pokoušejí různé algoritmy a parametry. Zastaví se po volání počtu iterací, které poskytnete, nebo když dosáhne cílové hodnoty pro metriku, kterou zadáte.

[![Automatizované Machine learning](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Můžete si prohlédnout protokolu spuštění informace, které obsahuje metriky shromážděné za běhu. Spuštění trénovací také vytvoří objekt serializován Python (`.pkl` souboru), který obsahuje model a předzpracování dat.

## <a name="model-explainability"></a>Model explainability

Běžné trávení automatizované machine Learning je nemožnost zobrazíte-celým procesem. Azure Machine Learning umožňuje zobrazit podrobné informace o modelech pro zvýšení transparentnosti do co běží na back endu. Některé modely, jako je lineární regrese, jsou považovány za poměrně jednoznačné a proto lze snadno porozumět. Můžete však přidat další funkce a používat složitější modelů strojového učení, získá princip je mnohem obtížnější. Existují dva klíčové aspekty transparentně ve službě machine learning:

1. Sledování strojového učení kanálu a všechny kroky zahrnuta, včetně předzpracování dat a snadné a hyperparameter hodnot.
1. Principy vztah mezi vstupních proměnných (označované také jako "funkce") a výstup modelu.  Znalost, velikosti a směr dopadu jednotlivých funkcí v předpovězené hodnoty pomůže lépe pochopit a popisují modelu. To se označuje jako funkce význam.

Můžete povolit globální funkce význam příspěvek na vyžádání, školení pro kanál podle vašeho výběru, nebo ji povolit pro všechny kanály, jako součást automatizovaného machine learning školení. V výrazně regulované oborech, třeba zdravotní péče a bankovnictví je to nezbytné pro dosažení souladu s předpisy a osvědčené postupy.  Tady je několik scénářů reálného světa pro ilustraci:

1. Výrobní společnosti pomocí strojového učení předpovídá budoucí nástroje selhání, takže mohou proaktivně provádět činnosti údržby. Jakmile budete vědět, že nástroj dojde k selhání, co s největší pravděpodobností bude tak preventivní údržba proběhnout rychle?
1. Finanční instituce pomocí machine learning k procesu půjčky nebo aplikací platební karty. Jak budete vědět, pokud model je dělá správnou věc, a pokud zákazník požádá o další podrobnosti na důvod, proč jejich aplikace byla zamítnuta, bude reakce na ně?
1. Online prodejce nebo zprostředkovatele nezávislé výrobce softwaru pomocí machine learning předvídat výpovědi zákazníků. Co jsou klíčových přispěvatelů zákazníkovi změn dat a jak můžete zabránit zákazníkům odchází?

Toto je funkce ve verzi preview a My budeme pokračovat s investicemi v bohatší informace, které vám pomůže lépe pochopit vašich modelů strojového učení. Použít tento [ukázkový poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/model-explanation/auto-ml-model-explanation.ipynb) můžete experimentovat s vysvětlením modelu ve službě Azure Machine Learning.

## <a name="next-steps"></a>Další postup

Podívejte se na příklady a další informace o vytváření modelů pomocí automatizované Machine Learning:
+ [Ukázky: Použití poznámkových bloků Jupyter a prozkoumejte službu Azure Machine Learning](samples-notebooks.md#automated-ml-setup)

+ [Kurz: Automaticky vyškolíme model klasifikace pomocí automatizované Azure Machine Learning](tutorial-auto-train-models.md)

+ [Použít automatické školení na vzdálený prostředek](how-to-auto-train-remote.md)

+ [Konfigurace nastavení pro automatické školení](how-to-configure-auto-train.md)
