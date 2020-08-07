---
title: Ladění & řešení potíží s kanály ML
titleSuffix: Azure Machine Learning
description: Ladění kanálů Azure Machine Learning v Pythonu Seznamte se s běžnými nástrah pro vývoj kanálů a tipů, které vám pomůžou ladit skripty před a během vzdáleného spouštění. Naučte se používat Visual Studio Code k interaktivnímu ladění kanálů strojového učení.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.topic: conceptual
ms.custom: troubleshooting, devx-track-python
ms.openlocfilehash: ac8896bae4b3bf36ee6e943581bbf6791401c821
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904645"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Ladění kanálů strojového učení a řešení souvisejících potíží
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto článku se dozvíte, jak ladit a řešit potíže s [kanály strojového učení](concept-ml-pipelines.md) v sadě [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) a v [Návrháři Azure Machine Learning (Preview)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Informace jsou k dispozici v tématu Postupy:

* Ladění pomocí sady Azure Machine Learning SDK
* Ladění pomocí návrháře Azure Machine Learning
* Ladění pomocí Application Insights
* Interaktivní ladění pomocí Visual Studio Code (VS Code) a Python Tools for Visual Studio (PTVSD)

## <a name="azure-machine-learning-sdk"></a>Azure Machine Learning SDK
Následující části poskytují přehled běžných nástrah při vytváření kanálů a různé strategie pro ladění kódu, který běží v kanálu. Následující tipy použijte, pokud máte potíže se spuštěním kanálu podle očekávání.

### <a name="testing-scripts-locally"></a>Místní testování skriptů

Jedním z nejběžnějších chyb v kanálu je to, že připojený skript (skript pro čištění dat, skript bodování atd.) neběží tak, jak je zamýšlený, nebo obsahuje běhové chyby ve vzdáleném výpočetním kontextu, které se v Azure Machine Learning Studiu obtížně ladí ve vašem pracovním prostoru. 

Samotné kanály se nedají spouštět místně, ale spuštěné skripty v izolaci na místním počítači vám umožní ladit rychleji, protože nemusíte čekat na proces sestavení výpočtů a prostředí. K tomu je potřeba nějaká vývojová práce:

* Pokud jsou vaše data v cloudovém úložišti dat, budete muset stáhnout data a zpřístupnit je vašemu skriptu. Použití malého vzorku vašich dat je dobrým způsobem, jak vyjímat modul runtime a rychle získat zpětnou vazbu k chování skriptu.
* Pokud se pokoušíte simulovat krok zprostředkujícího kanálu, možná budete muset ručně vytvořit typy objektů, které konkrétní skript očekává od předchozího kroku.
* Budete také muset definovat vlastní prostředí a replikovat závislosti definované ve vzdáleném výpočetním prostředí.

Jakmile budete mít Instalační program skriptu spuštěný v místním prostředí, je mnohem jednodušší provádět úlohy ladění, jako je:

* Připojení vlastní konfigurace ladění
* Pozastavení provádění a kontrola stavu objektu
* Zachycení typu nebo logických chyb, které se nezveřejňují do doby běhu

> [!TIP] 
> Jakmile ověříte, že je váš skript spuštěný podle očekávání, dobrým dalším krokem je spuštění skriptu v kanálu s jedním krokem předtím, než se pokusíte spustit v kanálu s více kroky.

### <a name="debugging-scripts-from-remote-context"></a>Ladění skriptů ze vzdáleného kontextu

Místní testování skriptů je skvělým způsobem, jak ladit hlavní fragmenty kódu a složitou logiku předtím, než začnete sestavovat kanál, ale v některých případech bude pravděpodobně nutné ladit skripty během samotného spuštění kanálu, zejména při diagnostice chování, ke kterému dojde během interakce mezi jednotlivými kroky kanálu. Doporučujeme, `print()` abyste ve svých skriptech použili možnost použití příkazů, abyste viděli stav objektu a očekávané hodnoty při vzdáleném spuštění, podobně jako při ladění kódu JavaScriptu.

Soubor protokolu `70_driver_log.txt` obsahuje: 

* Všechny tištěné příkazy během provádění skriptu
* Trasování zásobníku pro skript 

Chcete-li najít tento a další soubory protokolu na portálu, nejprve klikněte na spuštění kanálu ve vašem pracovním prostoru.

![Stránka seznamu spuštění kanálu](./media/how-to-debug-pipelines/pipelinerun-01.png)

Přejděte na stránku s podrobnostmi o spuštění kanálu.

![Stránka s podrobnostmi o spuštění kanálu](./media/how-to-debug-pipelines/pipelinerun-02.png)

Pro konkrétní krok klikněte na modul. Přejděte na kartu **protokoly** . Další protokoly obsahují informace o procesu sestavení image prostředí a kroku přípravy skriptu.

![Karta protokol stránky podrobností spuštění kanálu](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Spuštění *publikovaných kanálů* najdete na kartě **koncové body** v pracovním prostoru. Spuštění pro *nepublikované kanály* se dá najít v **experimentech** nebo **kanálech**.

### <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Následující tabulka obsahuje běžné problémy při vývoji kanálů s potenciálními řešeními.

| Problém | Možné řešení |
|--|--|
| Nejde předat data do `PipelineData` adresáře. | Ujistěte se, že jste ve skriptu vytvořili adresář, který odpovídá tomu, kde váš kanál očekává výstupní data kroku. Ve většině případů vstupní argument definuje výstupní adresář a pak adresář vytvoří explicitně. Použijte `os.makedirs(args.output_dir, exist_ok=True)` k vytvoření výstupního adresáře. V tomto [kurzu](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script) najdete příklad ukázkového skriptu, který ukazuje tento vzor návrhu. |
| Chyby závislostí | Pokud jste vytvořili a otestovali skripty lokálně, ale při spuštění ve vzdálené výpočetní službě v kanálu zjistíte problémy se závislostmi, ujistěte se, že vaše závislosti a verze prostředí COMPUTE odpovídají vašemu testovacímu prostředí. (Viz [sestavování prostředí, ukládání do mezipaměti a opakované použití](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse)|
| Dvojznačné chyby s cíli výpočtů | Odstranění a opětovné vytváření výpočetních cílů může vyřešit určité problémy s cíli výpočtů. |
| Kanál nepoužívá znovu postup | Použití tohoto kroku je ve výchozím nastavení povolené, ale ujistěte se, že jste ho neaktivovali v kroku kanálu. Pokud je opětovné použití zakázané, `allow_reuse` parametr v kroku se nastaví na `False` . |
| Nenutně funguje kanál. | Aby se zajistilo, že se kroky spustí znovu jenom v případě, že se změní jejich podkladová data nebo skripty, oddělte adresáře pro každý krok. Pokud používáte stejný zdrojový adresář pro více kroků, může docházet k zbytečnému opakovanému spuštění. Použijte `source_directory` parametr v objektu kroku kanálu, který odkazuje na izolovaný adresář pro daný krok, a ujistěte se, že nepoužíváte stejnou `source_directory` cestu pro více kroků. |

### <a name="logging-options-and-behavior"></a>Možnosti a chování protokolování

Následující tabulka poskytuje informace o různých možnostech ladění pro kanály. Nejedná se o vyčerpávající seznam, protože další možnosti existují kromě pouze Azure Machine Learning, Pythonu a OpenCensus, které jsou zde uvedeny.

| Knihovna                    | Typ   | Příklad                                                          | Cíl                                  | Zdroje a prostředky                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Machine Learning SDK | Metrika | `run.log(name, val)`                                             | Uživatelské rozhraní portálu Azure Machine Learning             | [Jak sledovat experimenty](how-to-track-experiments.md)<br>[AzureML. Core. Run – třída](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Tisk/protokolování v Pythonu    | Protokol    | `print(val)`<br>`logging.info(message)`                          | Protokoly ovladačů, Návrhář Azure Machine Learning | [Jak sledovat experimenty](how-to-track-experiments.md)<br><br>[Protokolování Pythonu](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus Python          | Protokol    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights – trasování                | [Ladění kanálů ve službě Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Nástroje pro export OpenCensus pro Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Kuchařka protokolování Pythonu](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Příklad možností protokolování

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="azure-machine-learning-designer-preview"></a>Návrhář Azure Machine Learning (Preview)

Tato část poskytuje přehled o řešení potíží s kanály v návrháři. Pro kanály vytvořené v Návrháři můžete soubor **70_driver_log** najít na stránce pro vytváření obsahu nebo na stránce s podrobnostmi o spuštění kanálu.

### <a name="enable-logging-for-real-time-endpoints"></a>Povolit protokolování pro koncové body v reálném čase

Aby bylo možné řešit a ladit koncové body v reálném čase v návrháři, je nutné povolit protokolování Application Insight pomocí sady SDK. Protokolování umožňuje řešit a ladit nasazení modelu a problémy s využitím. Další informace najdete v tématu [protokolování pro nasazené modely](how-to-enable-logging.md#logging-for-deployed-models). 

### <a name="get-logs-from-the-authoring-page"></a>Získat protokoly ze stránky pro vytváření obsahu

Když odešlete spuštění kanálu a zůstanete na stránce vytváření obsahu, můžete najít soubory protokolu vygenerované pro každý modul, když se každý modul dokončí.

1. Vyberte modul, který se dokončil na plátně pro tvorbu.
1. V pravém podokně modulu otevřete kartu **výstupy + protokoly** .
1. Rozbalte pravé podokno a vyberte **70_driver_log.txt** pro zobrazení souboru v prohlížeči. Protokoly také můžete stahovat místně.

    ![Rozšířené podokno výstup v Návrháři](./media/how-to-debug-pipelines/designer-logs.png)

### <a name="get-logs-from-pipeline-runs"></a>Získání protokolů z spuštění kanálu

Soubory protokolů pro konkrétní spuštění můžete najít na stránce s podrobnostmi o spuštění kanálu, kterou najdete v části **kanály** nebo **experimenty** v nástroji Studio.

1. Vyberte běh kanálu vytvořený v návrháři.

    ![Stránka spuštění kanálu](./media/how-to-debug-pipelines/designer-pipelines.png)

1. Vyberte modul v podokně náhledu.
1. V pravém podokně modulu otevřete kartu **výstupy + protokoly** .
1. Rozbalením pravého podokna zobrazte soubor **70_driver_log.txt** v prohlížeči nebo vyberte soubor pro místní stažení protokolů.

> [!IMPORTANT]
> Chcete-li aktualizovat kanál na stránce s podrobnostmi o spuštění kanálu, je nutné **naklonovat** spuštění kanálu do nové konceptu kanálu. Spuštění kanálu je snímek kanálu. Je podobný souboru protokolu a nedá se změnit. 

## <a name="application-insights"></a>Application Insights
Další informace o použití knihovny Pythonu OpenCensus tímto způsobem najdete v této příručce: [ladění a řešení potíží s kanály strojového učení v Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="visual-studio-code"></a>Visual Studio Code

V některých případech možná budete muset interaktivně ladit kód Pythonu, který se používá v kanálu ML. Pomocí Visual Studio Code (VS Code) a debugpy se můžete připojit ke kódu při jeho spuštění ve školicím prostředí. Další informace najdete [v příručce k interaktivnímu ladění v vs Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines).

## <a name="next-steps"></a>Další kroky

* Nápovědu k balíčku [AzureML-Pipelines-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) a balíčku [AzureML-Pipelines-Steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) najdete v referenčních informacích k sadě SDK.

* Podívejte se na seznam [výjimek návrháře a kódů chyb](algorithm-module-reference/designer-error-codes.md).
