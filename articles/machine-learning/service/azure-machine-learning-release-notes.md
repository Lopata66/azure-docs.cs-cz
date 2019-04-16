---
title: Co je nového ve verzi?
titleSuffix: Azure Machine Learning service
description: Další informace o nejnovějších aktualizacích služby Azure Machine Learning a strojové učení a pro přípravu dat sady SDK pro Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: Blackmist
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: e58205e847dbfdae8a114221f9bd56102555eeef
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579151"
---
# <a name="azure-machine-learning-service-release-notes"></a>Zpráva k vydání verze služby Azure Machine Learning

V tomto článku najdete další informace o vydaných verzích služby Azure Machine Learning.  Úplný popis jednotlivých sadách SDK navštivte referenční dokumenty pro:
+ Azure Machine Learning [ **hlavní sada SDK pro Python**](https://aka.ms/aml-sdk)
+ Azure Machine Learning [ **sady SDK pro přípravu dat**](https://aka.ms/data-prep-sdk)

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Můžete teď znovu odeslat existující skript spustit v existujícím clusteru vzdálený výpočetní. 
  + Nyní můžete spustit publikované kanálu o nové parametry na kartě kanály. 
  + Podrobnosti o spuštění teď podporuje nový prohlížeč snímku souboru. Snímek adresáře můžete zobrazit při odeslání konkrétního spuštění. Můžete také stáhnout Poznámkový blok, který byl odeslán na spuštění testu.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning sady SDK pro Python v1.0.23

+ **Nové funkce**
  + Azure Machine Learning SDK teď podporuje Python 3.7.
  + Azure Machine Learning DNN odhady teď poskytuje integrovanou podporu více verzí. Například `TensorFlow`  estimator nyní přijímá `framework_version` parametr a uživatele můžete určit verzi "1.10" nebo "1.12". Seznam verzí podporuje vaší aktuální verzi sady SDK, volání `get_supported_versions()` ve třídě požadované rozhraní (třeba `TensorFlow.get_supported_versions()`).
  Seznam verzí podporuje nejnovější vydání sady SDK, najdete v článku [DNN Estimator dokumentaci](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Sada SDK verze 1.1.1 pro přípravu dat Azure Machine Learning

+ **Nové funkce**
  + Můžete si přečíst více zdrojů úložiště dat/DataPath/odkaz DataReference použití read_ * transformací.
  + Můžete provádět následující operace na sloupce, které chcete vytvořit nový sloupec: dělení, floor, modulo napájení, délka.
  + Příprava dat je teď součástí sady diagnostiky Azure ML a ve výchozím nastavení se budou protokolovat diagnostické informace.
    + Chcete-li to vypnout, nastavte tuto proměnnou prostředí na hodnotu true: DISABLE_DPREP_LOGGER

+ **Opravy chyb a vylepšení**
  + Dokumentace k vylepšení kódu pro běžně používané třídy a funkce.
  + Oprava chyby v auto_read_file, která se nepodařilo přečíst Excelové soubory.
  + Přidání možnost přepsání na složku ve read_pandas_dataframe.
  + Vylepšení výkonu dotnetcore2 instalace závislostí a přidání podpory pro Fedora 27/28 a Ubuntu 1804.
  + Vyšší výkon čtení z objektů BLOB Azure.
  + Rozpoznání typu sloupce teď podporuje sloupce typu Long.
  + Je opravená chyba, ve kterém byly některé hodnoty data zobrazení jako časová razítka namísto Pythonu objekty data a času.
  + Je opravená chyba, pokud byly některé počtem typu zobrazení jako hodnot datového typu Double místo celých čísel.

  
## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning sady SDK pro Python v1.0.21

+ **Nové funkce**
  + *Azureml.core.Run.create_children* metoda umožňuje vytváření s nízkou latencí více podřízených spouští pomocí jediného volání.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Sada SDK v1.1.0 pro přípravu dat Azure Machine Learning

+ **Rozbíjející změny v**
  + Koncept balíček Data Prep je zastaralá a už není podporovaná. Namísto uchování více toků v rámci jednoho balíčku, je možné zachovat Dataflows jednotlivě.
    + Postupy: Průvodce: [Otevírání a ukládání Dataflows poznámkového bloku](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nové funkce**
  + Data Prep nyní dokáže rozpoznat sloupce, které odpovídají konkrétní sémantický typ. a odpovídajícím způsobem rozdělení. Zahrnout STypes v tuto chvíli nepodporuje: e-mailová adresa, zeměpisných souřadnic (zeměpisná šířka a zeměpisná délka), adresy IPv4 a IPv6, americké telefonní číslo a PSČ USA.
    + Postupy: Průvodce: [Sémantické typy poznámkového bloku](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Příprava dat teď podporuje následující operace ke generování výsledné sloupce ze dvou číselných sloupcích: odečítání, násobení, dělení a modulo.
  + Můžete volat `verify_has_data()` na toku dat ke kontrole, jestli toku dat vyprodukuje záznamy, pokud se spouští.

+ **Opravy chyb a vylepšení**
  + Nyní můžete zadat počet intervalů pro použití v histogramu číselný sloupec profilů.
  + `read_pandas_dataframe` Transformace nyní vyžaduje datový rámec mít řetězec - nebo bajt zadané názvy sloupců.
  + Oprava chyby v `fill_nulls` transformace, kde hodnoty nebyly zadány správně if sloupec nebyl nalezen.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning sady SDK pro Python v1.0.18

 + **Změny**
   + Balíček azureml tensorboard nahradí azureml. contrib tensorboard.
   + V této verzi můžete nastavit uživatelský účet na spravované výpočetního clusteru (amlcompute), při jejím vytváření. To můžete udělat jednoduše předáním tyto vlastnosti v konfiguraci zřizování. Můžete najít další podrobnosti najdete [referenční dokumentaci k sadě SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none-).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Sada SDK v1.0.17 pro přípravu dat Azure Machine Learning

+ **Nové funkce**
  + Nyní podporuje přidávání dvě číselné sloupce pro generování výsledné sloupce pomocí výrazu jazyka.

+ **Opravy chyb a vylepšení**
  + Vylepšené dokumentace a kontroly parametrů pro random_split.
  
## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Sada SDK v1.0.16 pro přípravu dat Azure Machine Learning

+ **Oprava chyby**
  + Oprava potíží s ověřováním, která byla výsledkem hlavního názvu služby změnou rozhraní API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning sady SDK pro Python v1.0.17

+ **Nové funkce**

  + Azure Machine Learning teď nabízí prvotřídní podporu pro oblíbené DNN framework Chainer. Pomocí [ `Chainer` ](https://docs.microsoft.com/en-us/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) třídy uživatelů můžete snadno trénovat a nasazovat modely Chainer.
    + Zjistěte, jak [spouštět distribuované trénování s ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Zjistěte, jak [spustit hyperparametrů s využitím HyperDrive Chainer](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning kanály přidat aktivační možnost změny úložiště dat na základě spuštění kanálu. Kanál [Poznámkový blok plán](https://aka.ms/pl-schedule) se aktualizuje a představte tuto funkci.

+ **Opravy chyb a vylepšení**
  + Přidali jsme podporu Azure Machine Learning kanály pro nastavení vlastnosti source_directory_data_store na požadované úložiště dat (jako je například úložiště objektů blob) na [RunConfigurations](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) , které jsou předány [ PythonScriptStep](https://docs.microsoft.com/en-us/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Kroky ve výchozím nastavení používají jako základní úložiště dat, který se může spustit do omezování problémy, když jsou současně spouštěny velký počet kroků úložiště souborů Azure.

### <a name="azure-portal"></a>portál Azure

+ **Nové funkce**
  + Nové přetažení tabulky prostředí editoru sestav. Uživatelé můžou z vrtu přetáhnout sloupec na oblast tabulky, ve kterém se zobrazí náhled tabulky. Můžete změnit jejich uspořádání sloupců.
  + Nový prohlížeč souborů protokolů
  + Odkazy na experimentování spuštění, výpočetních, modely, Image a nasazení z karty aktivity

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Sada SDK v1.0.15 pro přípravu dat Azure Machine Learning

+ **Nové funkce**
  + Teď podporuje zápis souborů datových proudů toku dat pro přípravu dat. Také poskytuje možnost manipulace s názvy souborů datového proudu k vytvoření nové názvy souborů.
    + Postupy: Průvodce: [Práce s datové proudy souborů poznámkového bloku](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Opravy chyb a vylepšení**
  + Vylepšení výkonu t-Digest na velkých datových sad.
  + Příprava dat teď podporuje čtení dat z DataPath.
  + Horké kódování teď funguje pro logické a číselné sloupce.
  + Ostatní různé opravy chyb.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Azure Machine Learning sady SDK pro Python v1.0.15

+ **Nové funkce**
  + Azure Machine Learning kanály přidali AzureBatchStep ([Poznámkový blok](https://aka.ms/pl-azbatch)), HyperDriveStep ([Poznámkový blok](https://aka.ms/pl-hyperdrive)) a podle času, funkci plánování ([Poznámkový blok](https://aka.ms/pl-schedule)).
  +  Aktualizovat DataTranferStep pro práci s Azure SQL Server a Azure database for PostgreSQL ([Poznámkový blok](https://aka.ms/pl-data-trans)).

+ **Změny**
  + Zastaralé `PublishedPipeline.get_published_pipeline` nahrazený `PublishedPipeline.get`.
  + Zastaralé `Schedule.get_schedule` nahrazený `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Sada SDK v1.0.12 pro přípravu dat Azure Machine Learning

+ **Nové funkce**
  + Teď podporuje čtení ze služby Azure SQL database s využitím úložiště dat pro přípravu dat.
 
+ **Změny**
  + Výkon paměti určitých operací velkých objemů dat je výrazně vyšší.
  + `read_pandas_dataframe()` nyní vyžaduje `temp_folder` zadání.
  + `name` Vlastnost `ColumnProfile` bylo zastaralé - použít `column_name` místo.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Azure Machine Learning sady SDK pro Python v1.0.10

+ **Změny**: 
  + Azure ML SDK není k dispozici balíčky azure-cli jako závislost. Konkrétně závislosti core v rozhraní příkazového řádku azure a azure-cli profilu byly odebrány z azureml jádry. Jedná se o uživatele vliv na změny:
    + Pokud provádění "az login" a pak pomocí Azure ml sdk, SDK provede prohlížeče nebo přihlášení zařízení kód ještě jednou. Jakýkoli stav přihlašovací údaje vytvořené "az login" jej nebude používat.
    + Ověřování příkazového řádku Azure, jako je třeba použití "az login", použijte _azureml.core.authentication.AzureCliAuthentication_ třídy. Ověřování příkazového řádku Azure, proveďte _pip nainstalovat rozhraní příkazového řádku azure_ v prostředí Pythonu, kterého jste nainstalovali azureml sdk.
    + Pokud provádíte "az login" pomocí hlavního názvu služby pro službu automation, doporučujeme použít _azureml.core.authentication.ServicePrincipalAuthentication_ třídy, jak Azure ml sdk nebude používat stavu přihlašovací údaje vytvořené pomocí azure CLI. 

+ **Opravy chyb**: Tato verze obsahuje většinou menšími opravami chyb

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Sada SDK v1.0.8 pro přípravu dat Azure Machine Learning

+ **Opravy chyb**
  + Výrazně vylepšený výkon načítání dat profilů.
  + Oprava drobných chyb, které jsou spojené s vytvářením sestav chyb.
  
### <a name="azure-portal-new-features"></a>Azure portal: nové funkce
+ Nové přetažení prostředí pro vytváření grafů pro sestavy. Uživatelům můžete přetáhnout atribut nebo sloupce z vrtu k oblasti grafu, kde systém automaticky vybere odpovídající grafy pro uživatele na základě typu dat. Uživatelům můžete změnit typ grafu na jiné typy příslušné nebo přidat další atributy.

    Podporované typy grafů:
    - Spojnicový graf
    - Histogram
    - Skládaný pruhový graf
    - Krabicový graf
    - Bodový
    - Bublinový graf
+ Na portálu teď dynamicky generuje sestavy pro experimentů. Když uživatel odešle na spuštění experimentu, automaticky se vygeneruje sestava s zaznamenané metriky a grafy umožňující porovnání během různých spuštění. 

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Azure Machine Learning sady SDK pro Python v1.0.8

+ **Opravy chyb**: Tato verze obsahuje většinou menšími opravami chyb

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Sada SDK v1.0.7 pro přípravu dat Azure Machine Learning

+ **Nové funkce**
  + Vylepšení úložiště dat (popsané v [úložiště dat k postupy – průvodce](https://aka.ms/aml-data-prep-datastore-nb))
    + Přidání možnosti číst z a zapisovat do sdílené složky Azure a úložiště ADLS v vertikálně navýšit kapacitu.
    + Při použití úložiště, přípravy dat pomocí ověřování instančních objektů místo interaktivní ověřování teď podporuje.
    + Přidání podpory pro wasb a wasbs adresy URL.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Sada SDK v1.0.6 pro přípravu dat Azure Machine Learning

+ **Opravy chyb**
  + Oprava chyby čtení z veřejných kontejnerů čitelné objektů Blob v Azure ve Sparku

## <a name="2018-12-20"></a>2018-12-20 

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Azure Machine Learning sady SDK pro Python v1.0.6
+ **Opravy chyb**: Tato verze obsahuje většinou menšími opravami chyb

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Verzi v1.0.4 sady SDK pro přípravu dat Azure Machine Learning

+ **Nové funkce**
  + `to_bool` Funkce teď umožňuje neodpovídající hodnot má být převeden na chybové hodnoty. Toto je nový neshoda výchozí chování pro `to_bool` a `set_column_types`, že bylo předchozí výchozí chování pro převod neodpovídající hodnoty na False.
  + Při volání metody `to_pandas_dataframe`, je nová možnost interpretace hodnotu null nebo chybějící hodnoty v číselné sloupce jako NaN.
  + Přidání možnosti zkontrolujte návratový typ některé výrazy k zajištění konzistence typu a selhat co nejdřív.
  + Nyní můžete volat `parse_json` k parsování hodnot ve sloupci jako objekty JSON a rozšířit do více sloupců.

+ **Opravy chyb**
  + Je opravená chyba, ke které došlo k chybě `set_column_types` v Python verze 3.5.2.
  + Je opravená chyba, ke které došlo k chybě při připojování k úložišti dat pomocí AML image.

+ **Aktualizace**
  * [Příklad poznámkových bloků](https://aka.ms/aml-data-prep-notebooks) získávají začít kurzy, případové studie a Průvodce postupy.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Všeobecná dostupnost

Služba Azure Machine Learning je teď obecně dostupná.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning Compute
V této verzi nové spravované výpočetní prostředí pomocí oznamujeme [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute). Tato cílové výpočetní prostředí nahradí výpočetní prostředky Azure Batch AI pro Azure Machine Learning. 

Tato cílového výpočetního prostředí:
+ Slouží k odvozování model školení a batch
+ Je výpočetní single - do více - node
+ Správa clusteru a úlohy plánování pro uživatele
+ Pravidla automatického škálování provedou ve výchozím nastavení
+ Podpora pro prostředky procesoru a GPU 
+ Umožňuje použití virtuálních počítačů s nízkou prioritou pro snížení nákladů

Azure Machine Learning Compute je možné vytvořit v Pythonu pomocí webu Azure portal nebo rozhraní příkazového řádku. Musí být vytvořené v oblasti vašeho pracovního prostoru a nemůže být připojen k jinému pracovnímu prostoru. Tato cílové výpočetní prostředí pomocí kontejneru Dockeru pro spuštění a balíčky závislostí replikovat stejné prostředí napříč všemi uzly.

> [!Warning]
> Doporučujeme vytvořit nový pracovní prostor používat Azure Machine Learning Compute. Může se vzdálená stát, které uživatelům pokusu o vytvoření aplikace Azure Machine Learning Compute z existujícího pracovního prostoru může zobrazit chyba. By měla fungovat to neovlivní existující výpočetních prostředků ve vašem pracovním prostoru.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Azure Machine Learning sady SDK pro Python v1.0.2
+ **Rozbíjející změny v**
  + V této verzi jsme se odebrání podpory pro vytvoření virtuálního počítače ze služby Azure Machine Learning. Stále můžete připojit existující cloud virtuálního počítače nebo na vzdáleném místním serverem. 
  + Podpora pro BatchAI, které by měl být podporovány prostřednictvím Azure Machine Learning Compute nyní také odebíráme.

+ **Nový**
  + Pro strojové učení kanály:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Aktualizovat**
  + Pro strojové učení kanály:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) nyní přijímá runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) nyní zkopíruje do a ze zdroje dat SQL
    + Naplánovat funkce v sadě SDK vytvářet a aktualizovat plány pro spouštění publikovaných kanály

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Sada SDK v0.5.2 pro přípravu dat Azure Machine Learning
+ **Rozbíjející změny v** 
  * `SummaryFunction.N` byla přejmenována na `SummaryFunction.Count`.
  
+ **Opravy chyb**
  * Použijte nejnovější AML spustit Token při čtení a zápisu do úložiště o vzdálené spuštění. Dříve Pokud AML spustit Token se aktualizuje v Pythonu, modul runtime přípravy dat nebude aktualizována aktualizované AML spustit Token.
  * Další jasnější chybové zprávy
  * to_spark_dataframe() už havaruje při Spark využívá `Kryo` serializace
  * Počet hodnot inspektoru teď můžete zobrazit více než 1 000 jedinečných hodnot
  * Náhodné rozdělení už selže, pokud původní tok dat nemá název  

+ **Další informace**
  * [Sada SDK pro přípravu dat Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumentace a poznámkové bloky
+ Kanály ML
  + Nové a aktualizované poznámkových bloků pro zahájení práce s kanály, rozsahu služby batch a styl přenos příklady: https://aka.ms/aml-pipeline-notebooks
  + Zjistěte, jak [vytvořit svůj první kanál](how-to-create-your-first-pipeline.md)
  + Zjistěte, jak [spouštění predikcí služby batch pomocí kanálů](how-to-run-batch-predictions.md)
+ Cílové výpočetní prostředí Azure Machine Learning
  + [Ukázkové poznámkové bloky](https://aka.ms/aml-notebooks) jsou nyní aktualizované na používání nové spravované výpočetní prostředky.
  + [Další informace o tomto výpočetní](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Azure portal: nové funkce
+ Vytvoření a správa [Azure Machine Learning Compute](how-to-set-up-training-targets.md#amlcompute) typy na portálu.
+ Monitorování využití kvóty a [žádost o kvótu](how-to-manage-quotas.md) pro Azure Machine Learning Compute.
+ Zobrazit stav clusteru Azure Machine Learning Compute v reálném čase.
+ Vytvoření Azure Machine Learning Compute a službě Azure Kubernetes Service byla přidána podpora virtuální sítě.
+ Znovu spusťte publikované kanálů s existující parametry.
+ Nové [automatizované machine learning grafy](how-to-track-experiments.md#auto) modelů klasifikace (výtah, zisky, kalibrací, funkce význam graf s modelem explainability) a regresních modelů (zbytky a funkce význam grafu s modelem explainability). 
+ Kanály lze zobrazit na webu Azure portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Azure Machine Learning sady SDK pro Python v0.1.80

+ **Rozbíjející změny v** 
  * *azureml.Train.widgets* obor názvů se přesunula do *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* zastarání následující třídy – *azureml.core.compute.BatchAICompute* a *azureml.core.compute.DSVMCompute*. Tato třída se odebere v dalších verzích. Třída AmlCompute má nyní jednodušší definici a jednoduše potřebuje vm_size a max_nodes a automatické škálování clusteru od 0 do max_nodes, když je úloha odeslána. Naše [ukázkové poznámkové bloky](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) byly aktualizovány s těmito informacemi a by vám měl dát příklady použití. Věříme, že je třeba toto zjednodušení a spoustu další zajímavé funkce, které chcete se dělí na novější verzi!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Sada SDK v0.5.1 pro přípravu dat Azure Machine Learning 

Další informace o sadě SDK pro Data Prep načtením [referenční dokumentace](https://aka.ms/data-prep-sdk).
+ **Nové funkce**
   * Vytvoření nového příkazového řádku přípravy a spouštění balíčků přípravy a zobrazení data profilu pro datovou sadu nebo toku dat
   * Přepracované rozhraní API SetColumnType použitelnosti
   * Přejmenované smart_read_file k auto_read_file
   * Nyní zahrnuje Nerovnoměrná distribuce a míra fluktuace v profil dat
   * Vzorkovat s povoleným vzorkováním vrstveného
   * Může číst ze souborů zip, které obsahují soubory CSV
   * Můžete rozdělit datovým sadám row-wise pomocí náhodného dělení (například do sady testů regrese)
   * Můžete získat všechny sloupce datové typy toku dat nebo profil dat voláním `.dtypes`
   * Můžete získat počet řádků od toku dat nebo profil dat voláním `.row_count`

+ **Opravy chyb**
   * Pevně long double převodu 
   * Oprava vyhodnocení po přidání sloupce 
   * Opravili jsme problém s FuzzyGrouping, kde ji nebude skupiny v některých případech rozpoznat
   * Opravené seřadit funkce dodržovat pořadí řazení více sloupců
   * Oprava a/nebo výrazy, které se podobá postupu `pandas` je zpracovává
   * Oprava čtení z dbfs cesty
   * Provedené pomohou lépe porozumět chybové zprávy 
   * Teď už selže při čtení na cílové vzdálené výpočetní prostředí pomocí tokenu AML
   * Teď už na Linuxu DSVM selže
   * Teď už dojde k chybě hodnoty jiné než řetězec jsou v predikátech řetězec
   * Nyní zpracovává kontrolního výrazu chyby při by měl správně selhání toku dat
   * Teď podporuje umístění dbutils připojené úložiště v Azure Databricks

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>portál Azure 
Na webu Azure portal pro službu Azure Machine Learning má následující aktualizace:
  * Nový **kanály** kartu pro publikované kanály.
  * Přidání podpory pro připojení k existujícímu clusteru HDInsight jako cílové výpočetní prostředí.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Azure Machine Learning sady SDK pro Python v0.1.74

+ **Rozbíjející změny v** 
  * * Workspace.compute_targets, úložišť, experimenty, obrázky, modely a *webservices* jsou vlastnosti namísto metod. Nahraďte třeba *Workspace.compute_targets()* s *Workspace.compute_targets*.
  * *Run.get_context* zastarání *Run.get_submitted_run*. Druhá metoda odebere v dalších verzích.
  * *PipelineData* třídy nyní očekává, že objekt úložiště dat jako parametr spíše než datastore_name. Obdobně *kanálu* přijímá default_datastore spíše než default_datastore_name.

+ **Nové funkce**
  * Kanály Azure Machine Learning [ukázkový poznámkový blok](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) teď používá MPI kroky.
  * RunDetails widget pro poznámkové bloky Jupyter se aktualizuje a zobrazí vizualizaci kanálu.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Sada SDK v0.4.0 pro přípravu dat Azure Machine Learning 
 
+ **Nové funkce**
  * Počet typů přidat Data profilu 
  * Počet hodnot a Histogram je nyní k dispozici
  * Další percentily v profil dat
  * Je k dispozici v Summarize Medián
  * Python 3.7 se teď podporuje.
  * Při uložení toku dat, která obsahuje úložišť do balíčku přípravy informace úložiště dat bude trvalé jako součást balíčku přípravy
  * Zápis do úložiště dat se teď podporuje. 
        
+ **Chyba opravená**
  * 64bitové celé číslo bez znaménka přetečení jsou teď správně zpracovány v Linuxu
  * Oprava nesprávné textový popisek pro soubory ve formátu prostého textu v smart_read
  * Typ sloupce řetězec se zobrazí v zobrazení metrik
  * Počet typů je teď pevně zobrazíte ValueKinds namapován na jeden typ pole namísto jednotlivé značky
  * Write_to_csv už selže, pokud je poskytnuta cesta jako řetězec
  * Při použití nahradit, byste museli opustit "najít" prázdné už neselže 

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Azure Machine Learning sady SDK pro Python v0.1.68

+ **Nové funkce**
  * Podpora více tenantů při vytváření nového pracovního prostoru.

+ **Opravy chyb**
  * Už nemusíte připnout verzi knihovny pynacl při nasazení webové služby.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Sada SDK v0.3.0 pro přípravu dat Azure Machine Learning

+ **Nové funkce**
  * Přidat metodu transform_partition_with_file(script_path), což umožňuje uživatelům a zajistěte tak předání cestu k souboru Pythonu ke spuštění

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Azure Machine Learning sady SDK pro Python v0.1.65
[Verze 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) obsahuje nové funkce, další dokumentaci, opravy chyb a další [ukázkové poznámkové bloky](https://aka.ms/aml-notebooks).

Zobrazit [seznam známých problémů](resource-known-issues.md) Další informace o známých chyb a jejich řešení.

+ **Rozbíjející změny v**
  * Workspace.experiments Workspace.models, Workspace.compute_targets, Workspace.images Workspace.web_services návratový slovníku. Tím se předtím vrátila seznam. Zobrazit [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) dokumentace k rozhraní API.

  * Automatizované Machine Learning odebrán směrodatná normalizované chyba primární metriky.

+ **HyperDrive**
  * Různé opravy chyb HyperDrive Bayesova, vylepšení výkonu pro získání metriky volání. 
  * Tensorflow 1.10 upgrade z 1.9 
  * Optimalizovat image dockeru pro úplné spuštění. 
  * Úlohy teď oznamují správný stav, i když opustí s kódem chyby než 0. 
  * RunConfig atributu validation v sadě SDK. 
  * HyperDrive spustit objekt podporuje Storno podobný regulární spuštění: nemusíte předávat žádné parametry. 
  * Widget vylepšení pro udržování stavu rozevírací seznam hodnot pro distribuované spuštění a HyperDrive spuštění. 
  * TensorBoard a dalších protokolů, které podporují soubory vyřešili serveru parametr. 
  * Podpora MPI Intel(R) na straně služby. 
  * Opravu parametr ladění pro distribuované spuštění oprava během ověřování v BatchAI. 
  * Správce kontextu nyní identifikuje primární instance. 

+ **Azure portal ještě neznáte**
  * log_table() a log_row() jsou podporovány v podrobnostech o spuštění. 
  * Automaticky vytvořte grafy pro tabulky a řádky s 1, 2 nebo 3 číselné sloupce a volitelný sloupec zařazené do kategorií.

+ **Automatizované Machine Learning**
  * Vylepšené zpracování chyb a dokumentace 
  * Načtení vlastnosti spuštění opravili problémy s výkonem. 
  * Oprava pokračovat v běhu problém. 
  * Opravili jsme ensembling iterace problémů.
  * Oprava školení Změ chyb v systému MAC OS.
  * Převzorkování – makro průměrné žádosti o přijetí změn a křivka ROC ve scénáři vlastní ověřování.
  * Odebrat další index logiku.
  * Odebrali jsme filtr, z get_output rozhraní API.

+ **Kanály**
  * Přidat metodu Pipeline.publish() do kanálu publikovat přímo, bez nutnosti spuštění se spouští jako první.   
  * Přidat metodu PipelineRun.get_pipeline_runs() načíst spuštěním kanálu, které byly generovány z publikovaných kanálu.

+ **Project Brainwave**
  * Aktualizovanou podporu nových modelů AI k dispozici na FPGA.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Sada SDK v0.2.0 pro přípravu dat Azure Machine Learning
[Verze 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) zahrnuje následující funkce a opravy chyb:

+ **Nové funkce**
  * Podpora pro jeden horkou kódování
  * Podpora pro transformaci quantile
   
+ **Chyba opravená:**
  * Funguje s jinou verzí tornádu není nutné přejít na nižší verzi tornádu verzi
  * Četnost hodnot pro všechny hodnoty, ne jenom první tři

## <a name="2018-09-public-preview-refresh"></a>2018-09 (aktualizace verze public preview)

Nové aktualizace verze služby Azure Machine Learning: Další informace o této vydané verzi: https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Další postup

Přehled pro [služby Azure Machine Learning](../service/overview-what-is-azure-ml.md).
