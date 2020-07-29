---
title: Zabezpečení přístupu k datům v cloudu
titleSuffix: Azure Machine Learning
description: Naučte se zabezpečeně připojit k datům z Azure Machine Learning a jak používat datové sady a úložiště dat pro úlohy ML. Úložiště dat může ukládat data z objektu blob Azure, Azure Data Lake 1. generace & 2, SQL DB, datacihly,...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/24/2020
ms.custom: tracking-python
ms.openlocfilehash: 15cf4aa6adda26991e76ec8a5e7378766fe2a21f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84552646"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Zabezpečení přístupu k datům v Azure Machine Learning

Azure Machine Learning usnadňuje připojení k datům v cloudu.  Poskytuje abstraktní vrstvu nad základní službou úložiště, takže můžete bezpečně přistupovat k datům a pracovat s nimi, aniž byste museli psát kód, který je specifický pro váš typ úložiště. Azure Machine Learning také nabízí následující možnosti dat:

*    Správa verzí a sledování datových linií
*    Popisky dat 
*    Monitorování odchylek dat
*    Interoperabilita s datovými rámečky PANDAS a Spark

## <a name="data-workflow"></a>Pracovní postup dat

Až budete připraveni použít data v cloudovém řešení úložiště, doporučujeme následující pracovní postup doručování dat. Tento pracovní postup předpokládá, že máte [účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) a data v cloudové službě úložiště v Azure. 

1. Vytvořte [úložiště dat Azure Machine Learning](#datastores) , abyste mohli ukládat informace o připojení do služby Azure Storage.

2. Z tohoto úložiště [dat vytvořte Azure Machine Learning datovou sadu](#datasets) , aby odkazovala na konkrétní soubory ve vašem základním úložišti. 

3. Pokud chcete tuto datovou sadu použít ve vašem experimentu machine learningu, můžete buď
    1. Připojte ho ke výpočetnímu cíli experimentu pro školení modelů.

        **ANI** 

    1. Využijte ji přímo v Azure Machine Learning řešení, jako je automatické spouštění strojového učení (automatizované ML), kanály strojového učení nebo [návrhář Azure Machine Learning](concept-designer.md).

4. Vytvořte [monitorování datových sad](#data-drift) pro výstupní datovou sadu modelu pro detekci posunu dat. 

5. Pokud se zjistí posun dat, aktualizujte vstupní datovou sadu a odpovídajícím způsobem svůj model znovu nahlaste.

Následující diagram nabízí vizuální ukázku tohoto doporučeného pracovního postupu.

![Data-koncept-diagram](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>Úložiště dat

Azure Machine Learning úložiště dat bezpečně udržují informace o připojení do služby Azure Storage, takže je nemusíte zakódovat ve svých skriptech. [Zaregistrujte a vytvořte úložiště dat](how-to-access-data.md) , abyste se mohli snadno připojit k účtu úložiště a přistupovat k datům v základní službě Azure Storage. 

Podporované cloudové služby úložiště v Azure, které se dají registrovat jako úložiště dat:

+ Kontejner objektů blob Azure
+ Sdílená složka Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ Azure Database for PostgreSQL
+ Systém souborů Databricks
+ Azure Database for MySQL

## <a name="datasets"></a>Datové sady

Azure Machine Learning datové sady jsou odkazy, které odkazují na data ve vaší službě úložiště. Nejedná se o kopie vašich dat, takže se neúčtují žádné dodatečné náklady na úložiště. Pokud chcete s daty v úložišti pracovat, [Vytvořte datovou sadu](how-to-create-register-datasets.md) , která zabalí data do spotřebního objektu pro úlohy strojového učení. Zaregistrujte datovou sadu do svého pracovního prostoru, abyste ji mohli sdílet a opakovaně používat v různých experimentech bez složitosti příjmu dat.

Datové sady je možné vytvořit z místních souborů, veřejných adres URL, [otevřených datových sad Azure](https://azure.microsoft.com/services/open-datasets/)nebo služeb úložiště Azure prostřednictvím úložiště dat. Chcete-li vytvořit datovou sadu z datového rámce v paměti PANDAS, zapište data do místního souboru, jako je například Parquet, a vytvořte datovou sadu z tohoto souboru.  

Podporujeme 2 typy datových sad: 
+ [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) představuje data v tabulkovém formátu tak, že analyzuje zadaný soubor nebo seznam souborů. Můžete načíst TabularDataset do datového rámce PANDAS nebo Spark pro další manipulaci a čištění. Úplný seznam formátů dat, ze kterých můžete vytvořit TabularDatasets, najdete v tématu [Třída TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

+ [Datová sada](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) souborů odkazuje na jeden nebo více souborů v úložišti dat nebo veřejných adresách URL. Do cíle výpočetního prostředí můžete [Stáhnout nebo připojit soubory](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) , na které odkazují datové sady.

Další možnosti datových sad najdete v následující dokumentaci:

+ [Verze a sledování](how-to-version-track-datasets.md) řádků datové sady
+ [Monitorujte datovou sadu](how-to-monitor-datasets.md) , abyste usnadnili detekci přenosů dat.    

## <a name="work-with-your-data"></a>Práce s daty

S datovými sadami můžete provádět řadu úloh strojového učení prostřednictvím bezproblémové integrace s funkcemi Azure Machine Learning. 

+ Vytvořte [projekt pro popisky dat](#label).
+ Naučte se modely strojového učení:
     + [automatizované experimenty ML](how-to-use-automated-ml-for-ml-models.md)
     + [Návrhář](tutorial-designer-automobile-price-train-score.md#import-data)
     + [poznámkových bloků](how-to-train-with-datasets.md)
     + [Azure Machine Learning kanály](how-to-create-your-first-pipeline.md)
+ Přístup k datovým sadám pro bodování pomocí [dávkového odvozování](how-to-use-parallel-run-step.md) v [kanálech strojového učení](how-to-create-your-first-pipeline.md).
+ Nastavte monitorování datových sad pro detekci [posunu dat](#drift) .

<a name="label"></a>

## <a name="data-labeling"></a>Popisky dat

Popisování velkých objemů dat je často starostí v projektech strojového učení. Ty, které mají součást počítačové vize, jako je například klasifikace obrázku nebo detekce objektu, obvykle vyžadují tisíce imagí a odpovídající popisky.

Azure Machine Learning poskytuje centrální umístění pro vytváření, správu a sledování projektů označování. Označování projektů usnadňuje koordinaci dat, popisků a členů týmu, což vám umožní efektivněji spravovat úlohy označování. Aktuálně podporované úlohy jsou klasifikace obrázku, a to buď pomocí ohraničených polí, nebo s více třídami, nebo s identifikací objektů.

Vytvořte [projekt pro označování dat a vypište](how-to-create-labeling-projects.md)datovou sadu pro použití v experimentech Machine Learning.

<a name="drift"></a>

## <a name="data-drift"></a>Posun dat

V kontextu strojového učení je posun dat změnou v modelu vstupní data, která vede k modelování snížení výkonu. Je jedním z hlavních důvodů, které se v průběhu času snižují přesností, a díky tomu sledování posunu dat pomáhá detekovat problémy s výkonem modelu.

Další informace o tom, jak detekovat a upozorňovat na data pro nová data v datové sadě, najdete v článku o [vytvoření objektu pro monitorování datových sad](how-to-monitor-datasets.md) .

## <a name="next-steps"></a>Další kroky 

+ [Pomocí těchto kroků](how-to-create-register-datasets.md) Vytvořte datovou sadu v Azure Machine Learning studiu nebo v sadě Python SDK.
+ Vyzkoušejte si příklady školení datových sad pomocí našich [ukázkových poznámkových bloků](https://aka.ms/dataset-tutorial).
+ Příklady posunu dat najdete v tomto [kurzu pro posun](https://aka.ms/datadrift-notebook)dat.
