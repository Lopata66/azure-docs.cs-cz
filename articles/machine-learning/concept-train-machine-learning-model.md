---
title: Sestavování &ch výukových modelů
titleSuffix: Azure Machine Learning
description: Seznamte se s různými metodami, které můžete použít k proškolování modelu pomocí Azure Machine Learning. Odhady poskytují snadný způsob, jak pracovat s oblíbenými rozhraními, jako jsou Scikit-učení, TensorFlow, Keras, PyTorch a chainer. Machine Learning kanály usnadňují plánování bezobslužného spouštění, používání heterogenních výpočetních prostředí a používání částí pracovního postupu. A konfigurace spuštění poskytují podrobnou kontrolu nad výpočetními cíli, na kterých běží školicí proces.
services: machine-learning
ms.service: machine-learning
author: Blackmist
ms.author: larryfr
ms.subservice: core
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c75c41012928b7bffb61a00a73f314e4c372b154
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792339"
---
# <a name="train-models-with-azure-machine-learning"></a>Výuka modelů pomocí Azure Machine Learning

Azure Machine Learning poskytuje několik způsobů, jak proškolit vaše modely, od řešení Code First pomocí sady SDK přes řešení s nízkým kódem, jako je například automatizované Machine Learning a vizuální Návrhář. Pomocí následujícího seznamu určete, která metoda školení je pro vás nejvhodnější:

+ [Azure Machine Learning SDK pro Python](#python-sdk): sada Python SDK nabízí několik způsobů výuky modelů, z nichž každá má různé možnosti.

    | Způsob školení | Popis |
    | ----- | ----- |
    | [Konfigurace spuštění](#run-configuration) | **Obecným způsobem, jak naučit modely** , je použít školicí skript a spustit konfiguraci. Konfigurace spuštění poskytuje informace potřebné ke konfiguraci školicího prostředí používaného pro výuku modelu. Můžete provést konfiguraci spuštění, školicí skript a cíl služby COMPUTE (školicí prostředí) a spustit školicí úlohu. |
    | [Automatizované strojové učení](#automated-machine-learning) | Automatizované Machine Learning umožňuje **výukové modely bez rozsáhlých vědeckých znalostí nebo programování**. Pro lidi s datovou vědy a programováním na pozadí poskytuje způsob, jak ušetřit čas a prostředky díky automatizaci výběru algoritmu a ladění parametrů. Při použití automatizovaného strojového učení se nemusíte starat o definování konfigurace spuštění. |
    | [Odhady](#estimators) | Třídy Estimator usnadňují **výukové modely založené na oblíbených architekturách strojového učení**. K dispozici jsou třídy Estimator pro **Scikit-učit**, **PyTorch**, **TensorFlow**a **chainer**. K dispozici je také obecná Estimator, která může být použita s architekturami, které ještě nemají vyhrazenou třídu Estimator. Při použití odhady se nemusíte starat o definování konfigurace spuštění. |
    | [Kanál strojového učení](#machine-learning-pipeline) | Kanály nejsou jinou výukovou metodou, ale **způsob definování pracovního postupu pomocí modulárních a opakovaně použitelných kroků**, které můžou zahrnovat školení jako součást pracovního postupu. Kanály strojového učení podporují pomocí automatizovaného strojového učení, odhady a konfigurace spouštění pro výukové modely. Vzhledem k tomu, že se kanály nezaměřují konkrétně na školení, jsou důvody pro použití kanálu různorodější než jiné metody školení. Obecně platí, že můžete použít kanál v těchto případech:<br>* Chcete **naplánovat bezobslužné procesy** , jako je například dlouho běžící školicí úlohy nebo Příprava dat.<br>* Použijte **více kroků** , které jsou koordinovány napříč heterogenními výpočetními prostředky a umístěními úložiště.<br>* Kanál použijte jako **opakovaně použitelnou šablonu** pro konkrétní scénáře, jako je například přeškolení nebo dávkové vyhodnocování.<br>* **Sledování a verze zdrojů dat, vstupů a výstupů** pro váš pracovní postup.<br>* Pracovní postup je **implementován různými týmy, které pracují podle konkrétních kroků nezávisle**. Kroky je pak možné spojit společně v kanálu pro implementaci pracovního postupu. |

+ [Azure Machine Learning SDK pro Python](#r-sdk): sada SDK používá balíček reticulate k vytvoření vazby na sadu SDK Azure Machine Learning Pythonu. To umožňuje přístup k základním objektům a metodám implementovaným v sadě Python SDK z jakéhokoli prostředí jazyka R.

+ **Návrhář**: Azure Machine Learning Designer (Preview) poskytuje snadný vstupní bod do strojového učení pro vytváření důkazů konceptů nebo pro uživatele s malým prostředím kódování. Umožňuje naučit modely pomocí webového uživatelského rozhraní přetažení. V rámci návrhu můžete použít kód Pythonu nebo modely výuky bez psaní kódu.

+ **CLI**: rozhraní příkazového řádku Machine Learning poskytuje příkazy pro běžné úlohy s Azure Machine Learning a často se používá pro **skriptování a automatizaci úloh**. Když jste například vytvořili školicí skript nebo kanál, můžete použít rozhraní příkazového řádku ke spuštění školicího programu podle plánu nebo při aktualizaci datových souborů použitých pro školení. Pro školicí modely poskytuje příkazy, které odesílají školicí úlohy. Může odesílat úlohy pomocí konfigurací spuštění nebo kanálů.

Každá z těchto metod školení může pro školení použít různé typy výpočetních prostředků. Souhrnně se tyto prostředky označují jako [__výpočetní cíle__](concept-azure-machine-learning-architecture.md#compute-targets). Cílem výpočetní služby může být místní počítač nebo cloudový prostředek, jako je Azure Machine Learning COMPUTE, Azure HDInsight nebo vzdálený virtuální počítač.

## <a name="python-sdk"></a>Python SDK

Sada SDK Azure Machine Learning pro Python umožňuje sestavovat a spouštět pracovní postupy strojového učení s Azure Machine Learning. Můžete komunikovat se službou z interaktivní relace Pythonu, poznámkových bloků Jupyter, Visual Studio Code nebo jiného integrovaného vývojového prostředí (IDE).

* [Co je sada SDK Azure Machine Learning pro Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Instalace/aktualizace sady SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
* [Konfigurace vývojového prostředí pro Azure Machine Learning](how-to-configure-environment.md)

### <a name="run-configuration"></a>Konfigurace spuštění

Úlohu obecného školení s Azure Machine Learning lze definovat pomocí [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py). Pak se použije konfigurace spuštění společně se školicími skripty pro výuku modelu na výpočetním cíli.

Můžete začít s konfigurací spuštění pro místní počítač a pak v případě potřeby přejít na jeden pro cloudový cíl výpočtů. Když měníte cíl výpočtů, změníte jenom konfiguraci spuštění, kterou použijete. Spuštění také zaznamená informace o úloze školení, jako jsou vstupy, výstupy a protokoly.

* [Co je konfigurace spuštění?](concept-azure-machine-learning-architecture.md#run-configurations)
* [Kurz: analýza prvního modelu ML](tutorial-1st-experiment-sdk-train.md)
* [Příklady: Jupyter Notebook příklady modelů školení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training)
* [Postupy: nastavení a použití výpočetních cílů pro školení modelů](how-to-set-up-training-targets.md)

### <a name="automated-machine-learning"></a>Automatizované strojové učení

Definujte iterace, nastavení parametrů, featurization a další nastavení. Během školení Azure Machine Learning pokusy o různé algoritmy a parametry paralelně. Školení se zastaví, jakmile narazí na kritéria ukončení, která jste definovali. Při použití odhady se nemusíte starat o definování konfigurace spuštění.

> [!TIP]
> Kromě sady Python SDK můžete také používat automatizované ML prostřednictvím [Azure Machine Learning studia](https://ml.azure.com).

* [Co je automatizované strojové učení?](concept-automated-ml.md)
* [Kurz: vytvoření prvního modelu klasifikace pomocí automatizovaného strojového učení](tutorial-first-experiment-automated-ml.md)
* [Kurz: Použití automatizovaného strojového učení k předvídání taxislužby tarifů](tutorial-auto-train-models.md)
* [Příklady: příklady Jupyter Notebook pro automatizované strojové učení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
* [Postupy: Konfigurace automatizovaných experimentů ML v Pythonu](how-to-configure-auto-train.md)
* [Postupy: autovýuka modelu prognózy časových řad](how-to-auto-train-forecast.md)
* [Postupy: vytváření, zkoumání a nasazení automatických experimentů strojového učení pomocí nástroje Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

### <a name="estimators"></a>Odhady

Odhady usnadňuje výukové modely pomocí oblíbených ML architektur. Pokud používáte **Scikit-učení**, **PyTorch**, **TensorFlow**nebo **chainer**, měli byste zvážit použití Estimator pro školení. K dispozici je také obecná Estimator, která může být použita s architekturami, které ještě nemají vyhrazenou třídu Estimator. Při použití odhady se nemusíte starat o definování konfigurace spuštění.

* [Co jsou odhady?](concept-azure-machine-learning-architecture.md#estimators)
* [Kurz: analýza modelů klasifikace imagí pomocí MNIST ručně zapsaných dat a scikit – Naučte se pomocí Azure Machine Learning](tutorial-train-models-with-aml.md)
* [Příklady: Jupyter Notebook příklady použití odhady](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)
* [Postupy: vytváření odhady ve školeních](how-to-train-ml-models.md)

### <a name="machine-learning-pipeline"></a>Kanál strojového učení

Kanály strojového učení můžou používat dřív zmíněné metody školení (spuštění konfigurace, odhady a automatizovaného strojového učení). Kanály jsou více o vytváření pracovního postupu, takže zahrnují více než jenom školení modelů. V kanálu můžete model vytvořit pomocí automatizovaného strojového učení, odhady nebo konfigurací spouštění.

* [Co jsou kanály ML v Azure Machine Learning?](concept-ml-pipelines.md)
* [Vytvoření a spuštění kanálů strojového učení s Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
* [Kurz: použití kanálů Azure Machine Learning pro dávkové vyhodnocování](tutorial-pipeline-batch-scoring-classification.md)
* [Příklady: příklady Jupyter Notebook pro kanály strojového učení](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
* [Příklady: kanál s automatickým strojovým učením](https://aka.ms/pl-automl)
* [Příklady: kanál s odhady](https://aka.ms/pl-estimator)

## <a name="r-sdk"></a>R SDK

Sada R SDK umožňuje používat jazyk R s Azure Machine Learning. Sada SDK používá balíček reticulate k navázání Azure Machine Learning Python SDK. To umožňuje přístup k základním objektům a metodám implementovaným v sadě Python SDK z jakéhokoli prostředí jazyka R.

Další informace najdete v těchto článcích:

* [Kurz: vytvoření modelu logistické regrese](tutorial-1st-r-experiment.md)
* [Referenční informace o Azure Machine Learning SDK pro R](https://azure.github.io/azureml-sdk-for-r/index.html)

## <a name="azure-machine-learning-designer"></a>Návrhář Azure Machine Learning

Návrhář vám umožní naučit modely pomocí rozhraní přetažení ve webovém prohlížeči.

+ [Co je Návrhář?](concept-designer.md)
+ [Kurz: předpověď ceny automobilu](tutorial-designer-automobile-price-train-score.md)
+ [Regrese: předpověď ceny](how-to-designer-sample-regression-automobile-price-basic.md)
+ [Klasifikace: předpověď výnosů](how-to-designer-sample-classification-predict-income.md)
+ [Klasifikace: předpověď změn, appetencyí a prodejů](how-to-designer-sample-classification-churn.md)
+ [Klasifikace s vlastním skriptem R: předpověď zpoždění letů](how-to-designer-sample-classification-flight-delay.md)
+ [Klasifikace textu: Wikipedii sada dat SP 500](how-to-designer-sample-text-classification.md)

## <a name="cli"></a>Rozhraní příkazového řádku

Machine Learning CLI je rozšířením rozhraní příkazového řádku Azure CLI. Poskytuje příkazy rozhraní příkazového řádku pro různé platformy pro práci s Azure Machine Learning. Typicky k automatizaci úloh, jako je například školení modelu strojového učení, použijete rozhraní příkazového řádku.

* [Pro Azure Machine Learning použít rozšíření CLI](reference-azure-machine-learning-cli.md)
* [MLOps v Azure](https://github.com/microsoft/MLOps)

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [nastavit školicí prostředí](how-to-set-up-training-targets.md).
