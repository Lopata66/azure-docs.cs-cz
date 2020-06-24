---
title: Služby Azure, které podporují Azure Data Lake Storage Gen2 | Microsoft Docs
description: Informace o tom, které služby Azure se integrují s Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 1854c6f86e1a3578b6314b072f104174032344e1
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84907580"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Služby Azure, které podporují Azure Data Lake Storage Gen2

Služby Azure můžete použít k ingestování dat, provádění analýz a vytváření vizuálních reprezentace. Tento článek obsahuje seznam podporovaných služeb Azure, zveřejňuje jejich úroveň podpory a poskytuje odkazy na články, které vám pomůžou tyto služby používat s Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Podporované služby Azure

V této tabulce jsou uvedené služby Azure, které můžete používat s Azure Data Lake Storage Gen2. Položky, které se zobrazí v těchto tabulkách, se v průběhu času změní, dokud je podpora stále rozbalená.

> [!NOTE]
> Úroveň podpory odkazuje jenom na to, jak je služba podporovaná s Data Lake Storage Gen 2.

|Služba Azure |Úroveň podpory |Související články |
|---------------|-------------------|---|
|Azure Data Factory|Obecná dostupnost|[Načtení dat do Azure Data Lake Storage Gen2 s využitím Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Obecná dostupnost|[Použití se službou Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Rychlý Start: Analýza dat v Azure Data Lake Storage Gen2 pomocí Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Kurz: extrakce, transformace a načtení dat pomocí Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Kurz: přístup k datům Data Lake Storage Gen2 pomocí Azure Databricks pomocí Sparku](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hub|Obecná dostupnost|[Zachycení událostí prostřednictvím Azure Event Hubs v Azure Blob Storage nebo Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Event Grid|Obecná dostupnost|[Kurz: implementace vzoru Data Lake Capture pro aktualizaci rozdílové tabulky datacihly](data-lake-storage-events.md)|
|Azure Logic Apps|Obecná dostupnost|[Přehled – co je Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Obecná dostupnost|[Přístup k datům ve službě Azure Storage](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Obecná dostupnost|[Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Odchozí přenos dat do Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Obecná dostupnost|[Pomocí Azure Data Box migrujte data z místního úložiště HDFS do Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Obecná dostupnost|[Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Použití rozhraní příkazového řádku HDFS s Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Kurz: extrakce, transformace a načtení dat pomocí Apache Hive ve službě Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |Obecná dostupnost|[Použití směrování zpráv IoT Hub k posílání zpráv ze zařízení do cloudu do různých koncových bodů](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Obecná dostupnost|[Analyzovat data v Data Lake Storage Gen2 pomocí Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (dříve SQL Data Warehouse)|Obecná dostupnost|[Použití s Azure synapse Analytics (dříve SQL Data Warehouse)](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Obecná dostupnost|[Správce připojení Azure Storage](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search|Preview|[Index a hledání Azure Data Lake Storage Gen2ch dokumentů (Preview)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Průzkumník dat Azure|Preview|[Dotazování dat v Azure Data Lake pomocí Azure Průzkumník dat](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure Content Delivery Network|Zatím nepodporováno|[Index a hledání Azure Data Lake Storage Gen2ch dokumentů (Preview)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Viz také

- [Známé problémy s Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Funkce BLOB Storage dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Open Source platformy, které podporují Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Přístup k více protokolům na Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)