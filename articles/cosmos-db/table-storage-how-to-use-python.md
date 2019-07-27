---
title: Začínáme se službou Azure Table Storage a rozhraním Table API služby Azure Cosmos DB pomocí Pythonu
description: Ukládejte si strukturovaná data v cloudu pomocí služby Azure Table Storage nebo rozhraní Table API služby Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 04/05/2018
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 0f0acc721fd8888953d80976234b431943985ebf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68356267"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Začínáme se službou Azure Table Storage a rozhraním Table API služby Azure Cosmos DB pomocí Pythonu

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Azure Table Storage a Azure Cosmos DB jsou služby, které ukládají strukturovaná data NoSQL do cloudu a poskytují úložiště klíčů/atributů s návrhem bez použití schématu. Vzhledem k tomu, že jsou služby Table Storage a Azure Cosmos DB bez schématu, je snadné data přizpůsobovat měnícím se potřebám vaší aplikace. Přístup k datům služby Table Storage a rozhraní Table API je pro mnoho typů aplikací rychlý a nákladově efektivní a pro podobné objemy dat obvykle znamená nižší náklady než tradiční SQL.

Table Storage nebo Azure Cosmos DB můžete používat k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení nebo dalších typů metadat, které vaše služba vyžaduje. V tabulce můžete uložit libovolný počet entit a účet úložiště může obsahovat libovolný počet tabulek, až do limitu kapacity účtu úložiště.

### <a name="about-this-sample"></a>O této ukázce
Tato ukázka předvádí použití [sady Table SDK služby Azure Cosmos DB pro Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) v běžných scénářích služby Azure Table Storage. Název sady SDK značí, že je určená k použití se službou Azure Cosmos DB, ale stejně tak funguje i se službou Azure Table Storage, obě služby mají pouze jiný koncový bod. Tyto scénáře se zkoumají na příkladech v Pythonu a znázorňují následující postupy:
* Vytváření a odstraňování tabulek
* Vkládání a dotazování entit
* Úpravy entit

Při procházení scénářů v této ukázce můžete využít [referenční informace k sadě SDK služby Azure Cosmos DB pro rozhraní Python API](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python).

## <a name="prerequisites"></a>Požadavky

Pro úspěšné dokončení této ukázky potřebujete následující položky:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5, nebo 3.6
- [Sada Table SDK služby Azure Cosmos DB pro Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Tato sada SDK umožňuje připojení ke službě Azure Table Storage i k rozhraní Table API služby Azure Cosmos DB.
- [Účet služby Azure Storage](../storage/common/storage-quickstart-create-account.md) nebo [účet služby Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

## <a name="create-an-azure-service-account"></a>Vytvoření účtu služby Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu rozhraní Table API služby Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Instalace sady Table SDK služby Azure Cosmos DB pro Python

Po vytvoření účtu služby Storage je dalším krokem instalace [sady Table SDK služby Microsoft Azure Cosmos DB pro Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Podrobnosti o instalaci sady SDK najdete v souboru [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) v úložišti sady Table SDK služby Cosmos DB pro Python na GitHubu.

## <a name="import-the-tableservice-and-entity-classes"></a>Import tříd TableService a Entity

Chcete-li pracovat s entitami v Azure Table service v Pythonu, použijte třídy [TableService][py_TableService] a [entity][py_Entity] . Importujte obě třídy přidáním tohoto kódu na začátek souboru Pythonu:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Připojení ke službě Azure Table Storage

Pokud se chcete připojit ke službě Azure Table Storage, vytvořte objekt [TableService][py_TableService] a předejte do něj název a klíč vašeho účtu služby Storage. Nahraďte `myaccount` a `mykey` názvem a klíčem vašeho účtu.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Připojení ke službě Azure Cosmos DB

Pokud se chcete připojit ke službě Azure Cosmos DB, zkopírujte z webu Azure Portal primární připojovací řetězec a s jeho použitím vytvořte objekt [TableService][py_TableService]:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>Vytvoření tabulky

K vytvoření tabulky zavolejte [CREATE_TABLE][py_create_table] .

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Přidání entity do tabulky

Chcete-li přidat entitu, nejprve vytvořte objekt, který reprezentuje vaši entitu, a pak objekt předejte [metodě TableService. insert_entity][py_TableService]. Objekt entity může být slovníkem nebo objektem typu [entity][py_Entity]a definuje názvy a hodnoty vlastností vaší entity. Každá entita musí kromě případných dalších vlastností, které pro ni definujete, obsahovat i požadované vlastnosti [PartitionKey a RowKey](#partitionkey-and-rowkey).

Tento příklad vytvoří objekt Dictionary reprezentující entitu a poté předá metodu [insert_entity][py_insert_entity] , aby ji přidal do tabulky:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the trash', 'priority': 200}
table_service.insert_entity('tasktable', task)
```

Tento příklad vytvoří objekt [entity][py_Entity] a pak ho předá metodě [insert_entity][py_insert_entity] k přidání do tabulky:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey a RowKey

Pro každou entitu musíte zadat vlastnost **PartitionKey** i **RowKey**. Jedná se o jedinečné identifikátory entit a společně tvoří primární klíč entity. Pomocí těchto hodnot můžete provádět mnohem rychlejší dotazování než na jakékoli ostatní vlastnosti entit, protože pouze tyto vlastnosti se indexují.

Table Storage používá **PartitionKey** k inteligentní distribuci entit tabulky napříč uzly úložiště. Entity se stejnou hodnotou **PartitionKey** se ukládají na stejném uzlu. **RowKey** je jedinečné ID entity v rámci oddílu, do kterého patří.

## <a name="update-an-entity"></a>Aktualizace entity

Chcete-li aktualizovat všechny hodnoty vlastností entity, zavolejte metodu [update_entity][py_update_entity] . Tento příklad ukazuje nahrazení stávající entity její aktualizovanou verzí:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage', 'priority': 250}
table_service.update_entity('tasktable', task)
```

Pokud aktualizovaná entita ještě neexistuje, operace aktualizace selže. Pokud chcete entitu Uložit, ať už existuje, nebo ne, použijte [insert_or_replace_entity][py_insert_or_replace_entity]. V následujícím příkladu první volání nahradí stávající entitu. Druhé volání vloží novou entitu, protože v tabulce neexistuje žádná entita se zadanými hodnotami PartitionKey a RowKey.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage again', 'priority': 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003',
        'description': 'Buy detergent', 'priority': 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> Metoda [update_entity][py_update_entity] nahrazuje všechny vlastnosti a hodnoty existující entity, které můžete použít také k odebrání vlastností z existující entity. Pomocí metody [merge_entity][py_merge_entity] můžete aktualizovat existující entitu s novými nebo upravenými hodnotami vlastností, aniž byste entitu zcela nahradili.

## <a name="modify-multiple-entities"></a>Úpravy více entit

Pokud chcete zajistit atomické zpracování požadavku službou Table Storage, můžete odeslat více operací společně v dávce. Nejprve použijte třídu [TableBatch][py_TableBatch] k přidání více operací do jedné dávky. Dále zavolejte [TableService][py_TableService]. [commit_batch][py_commit_batch] k odeslání operací v atomické operaci. Všechny entity, které se mají v dávce upravit, musí být ve stejném oddílu.

Tento příklad v dávce přidá dvě entity najednou:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004',
           'description': 'Go grocery shopping', 'priority': 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005',
           'description': 'Clean the bathroom', 'priority': 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Dávky je možné použít také se syntaxí správce kontextu:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006',
           'description': 'Go grocery shopping', 'priority': 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007',
           'description': 'Clean the bathroom', 'priority': 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Dotaz na entitu

Chcete-li zadat dotaz na entitu v tabulce, předejte jí PartitionKey a RowKey na [TableService][py_TableService]. Metoda [get_entity][py_get_entity]

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Dotazování sady entit

Dotaz na sadu entit můžete zadat uvedením řetězce filtru v parametru **filter**. Tento příklad vyhledá všechny úlohy v Seattlu použitím filtru hodnoty PartitionKey:

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Dotaz na podmnožinu vlastností entity

U každé entity v dotazu také můžete omezit, jaké vlastnosti se vrátí. Tato technika, označovaná jako *projekce*, snižuje šířku pásma a může zlepšit výkon dotazů, zejména u velkých entit nebo sad výsledků. Použijte parametr **select** a předejte názvy vlastností, které chcete vrátit do klienta.

Dotaz v následujícím kódu vrátí pouze popisy entit v tabulce.

> [!NOTE]
> Následující fragment kódu funguje pouze pro službu Azure Storage. Emulátor úložiště ho nepodporuje.

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Odstranění entity

Odstraňte entitu předáním svého **PartitionKey** a **RowKey** metody [delete_entity][py_delete_entity] .

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Odstranění tabulky

Pokud již nepotřebujete tabulku ani žádnou entitu, zavolejte metodu [delete_table][py_delete_table] , aby se tabulka trvale odstranila z Azure Storage.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Další kroky

* [Nejčastější dotazy – Vývoj pomocí rozhraní Table API](https://docs.microsoft.com/azure/cosmos-db/faq)
* [Referenční informace k sadě SDK služby Azure Cosmos DB pro rozhraní Python API](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python)
* [Středisko pro vývojáře programující v Pythonu](https://azure.microsoft.com/develop/python/)
* [Průzkumník služby Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md): Bezplatná aplikace pro různé platformy pro práci vizuálně s Azure Storagemi daty v systémech Windows, macOS a Linux.
* [Práce s Pythonem v sadě Visual Studio (Windows)](https://docs.microsoft.com/visualstudio/python/overview-of-python-tools-for-visual-studio)


[py_commit_batch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_create_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_get_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_or_replace_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_Entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity?view=azure-python
[py_merge_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_update_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableService]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
