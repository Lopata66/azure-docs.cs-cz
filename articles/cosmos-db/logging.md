---
title: Protokolování diagnostiky ve službě Azure Cosmos DB
description: Další informace o různých způsobech protokolování a monitorování dat uložených ve službě Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 8839d7ea93bcb205b1900e63d3ab98394e72cd75
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "58904861"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Protokolování diagnostiky ve službě Azure Cosmos DB 

Jakmile začnete používat jednu nebo více databází Azure Cosmos DB, můžete chtít sledovat jak a kdy jsou vaše databáze přístupné. Tento článek poskytuje přehled o protokoly, které jsou k dispozici na platformě Azure. Informace o povolení protokolování diagnostiky pro účely odeslat protokoly do monitorování [služby Azure Storage](https://azure.microsoft.com/services/storage/), jak streamování protokolů do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)a jak exportovat protokoly [protokolyAzuremonitoru](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Protokoly, které jsou k dispozici v Azure

Před mluvíme o tom, jak sledovat účtu služby Azure Cosmos DB, můžeme vysvětlit pár věcí o protokolování a monitorování. Existují různé typy protokolů na platformě Azure. Existují [protokolů aktivit Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [diagnostické protokoly Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [metriky Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), události, monitorování prezenčních signálů, provozních protokolů a tak dále. Existuje množství protokoly. Můžete zobrazit úplný seznam protokolů v [protokoly Azure monitoru](https://azure.microsoft.com/services/log-analytics/) na webu Azure Portal. 

Na následujícím obrázku je jiný druh protokoly Azure, které jsou k dispozici:

![Různé druhy protokoly Azure](./media/logging/azurelogging.png)

Na obrázku **výpočetní prostředky** představují prostředky Azure, pro které můžete přístup k Microsoft hostovaného operačního systému. Například virtuální počítače Azure, virtuálního počítače škálovací sady, Azure Container Service a atd., jsou považovány za výpočetní prostředky. Výpočetní prostředky generovat protokoly aktivit, diagnostické protokoly a protokoly aplikací. Další informace najdete [zdroje dat v Azure monitorování](../azure-monitor/platform/data-sources.md) článku.

**Non-výpočetní prostředky** prostředků, ve kterých nelze přístup k podkladovému operačnímu systému a pracovat přímo s prostředkem. Například skupiny zabezpečení sítě, Logic Apps a tak dále. Azure Cosmos DB je prostředek rozsáhlých výpočetních prostředků. Můžete zobrazit protokoly pro prostředky rozsáhlých výpočetních prostředků v protokolu aktivit nebo povolit možnost diagnostické protokoly na portálu. Další informace najdete [zdroje dat ve službě Azure Monitor](../azure-monitor/platform/data-sources.md) článku.

Protokol aktivit zaznamenává operace na úrovni předplatného pro službu Azure Cosmos DB. Operace jako klíče Listkey, zápis DatabaseAccounts a další jsou protokolovány. Diagnostické protokoly poskytují podrobnější protokolování a umožňují DataPlaneRequests (vytvoření, čtení, dotazování a tak dále) a MongoRequests protokolu.


V tomto článku se zaměříme na protokol aktivit Azure, Azure diagnostické protokoly a metriky Azure. Jaký je rozdíl mezi tyto tři protokoly? 

### <a name="azure-activity-log"></a>Protokol aktivit Azure

Protokol aktivit Azure je předplatné protokol, který poskytuje podrobné informace o události na úrovni předplatného, ke kterým došlo v Azure. Protokol aktivit hlásí události rovina řízení pro vaše předplatná v rámci administrativní kategorie. Protokol aktivit můžete použít k určení "co, kdo a kdy" pro jakékoli zápisu (PUT, POST, DELETE) operace s prostředky ve vašem předplatném. Můžete také zjištění stavu operace a další relevantní vlastnosti. 

Protokol aktivit se liší od diagnostické protokoly. Poskytuje data o operacích v prostředku z vnějšku protokolu aktivit ( _rovina řízení_). Rovina řízení, které zahrnují operace v rámci služby Azure Cosmos DB vytvořit kontejner, seznam klíčů, odstranění klíče, seznam databáze a tak dále. Diagnostické protokoly jsou emitovány prostředek a poskytnout informace o fungování tohoto prostředku ( _rovina dat_). Mezi příklady operace roviny dat v diagnostických protokolů patří Delete, Insert a ReadFeed.

Protokoly aktivit (operace roviny řízení) může být širší ze své podstaty a může obsahovat úplnou e-mailovou adresu volajícího, IP adresy volajícího, název prostředku, název operace, TenantId a další. Protokol aktivit obsahuje několik [kategorie](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) data. Všechny podrobnosti o schémat z těchto kategorií naleznete v tématu [schéma událostí protokolu aktivit Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). Diagnostické protokoly však může být omezující ze své podstaty jako osobní údaje je často odebrána z těchto protokolů. Můžete mít IP adresy volajícího, ale je odebrán poslední octant.

### <a name="azure-metrics"></a>Metriky Azure

[Metriky Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) nejdůležitější typu Azure telemetrická data (také nazývané _čítače výkonu_), který je vygenerován prostředky většinu služeb Azure. Metriky umožňují zobrazit informace o propustnosti, úložiště, konzistencí, dostupností a latencí vašich prostředků Azure Cosmos DB. Další informace najdete v tématu [monitorování a ladění s využitím metrik ve službě Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Diagnostické protokoly Azure

Diagnostické protokoly Azure jsou emitovány prostředek a poskytují bohaté a časté informace o fungování tohoto prostředku. Obsah tyto protokoly se liší podle typu prostředku. Diagnostické protokoly na úrovni prostředků se také liší od diagnostické protokoly na úrovni operačního systému hosta. Diagnostické protokoly operačního systému hosta byly shromážděny sadou agenta, na kterém běží uvnitř virtuálního počítače nebo jiné nepodporuje typ prostředku. Diagnostické protokoly na úrovni prostředků vyžadovat žádná data specifické podle prostředků agenta a zachycení samotné platformy Azure. Diagnostické protokoly úrovni operačního systému hosta zachytávat data z operačního systému a aplikací, které jsou spuštěné na virtuálním počítači.

![Protokolování diagnostiky pro protokoly úložiště služby Event Hubs a Azure Monitor](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Co je protokolováno podle diagnostické protokoly Azure?

* Protokolují se všechny ověřené back-endu požadavky (TCP/REST) na všechna rozhraní API, včetně neúspěšné požadavky v důsledku oprávnění k přístupu, systémových chyb nebo chybných požadavků. Podpora pro uživatelem iniciované grafu, Cassandra a Table API požadavky nejsou aktuálně k dispozici.
* Operace databáze, mezi které patří operace CRUD s dokumenty, kontejnerů a databáze.
* Operace nad klíči účtu, mezi které patří vytvoření, úprava nebo odstranění klíče.
* Neověřené požadavky, které skončí odpovědí 401 – Neoprávněno. Například požadavky, které nemají nosný token, nebo jsou poškozené nebo jejichž platnost vypršela nebo mají neplatný token.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Zapnutí protokolování na portálu Azure portal

Pokud chcete povolit protokolování diagnostiky, musí mít následující prostředky:

* Existující služby Azure Cosmos DB účet, databázi a kontejner. Pokyny k vytvoření těchto prostředků najdete v tématu [pomocí webu Azure portal vytvořit účet databáze](create-sql-api-dotnet.md#create-account), [ukázky v Azure CLI](cli-samples.md), nebo [ukázky Powershellu](powershell-samples.md).

Pokud chcete povolit protokolování diagnostiky na portálu Azure portal, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com), ve vaší službě Azure Cosmos DB účtu, vyberte **diagnostické protokoly** v levém navigačním panelu a pak vyberte **zapnout diagnostiku**.

    ![Zapnutí protokolování diagnostiky pro Azure Cosmos DB na webu Azure Portal](./media/logging/turn-on-portal-logging.png)

2. V **nastavení diagnostiky** stránce, proveďte následující kroky: 

    * **Název**: Zadejte název pro protokoly a vytvořit.

    * **Archivovat do účtu úložiště**: Pokud chcete použít tuto možnost, musíte se připojit k existující účet úložiště. Chcete-li vytvořit nový účet úložiště na portálu, najdete v článku [vytvořit účet úložiště](../storage/common/storage-create-storage-account.md) a postupujte podle pokynů a vytvořte Azure Resource Manageru, účet pro obecné účely. Pak se vraťte k této stránce na portálu a vyberte svůj účet úložiště. Může trvat několik minut, než se nově vytvořené účty se zobrazí v rozevírací nabídce.
    * **Stream do centra událostí**: Pokud chcete použít tuto možnost, budete potřebovat existující služby Event Hubs oboru názvů a Centrum událostí se připojit k. Pokud chcete vytvořit obor názvů služby Event Hubs, najdete v článku [vytvořit obor názvů služby Event Hubs a centra událostí pomocí webu Azure portal](../event-hubs/event-hubs-create.md). Pak se vraťte k této stránce portálu vyberte název služby Event Hubs oboru názvů a zásad.
    * **Odeslání do Log Analytics**: Pokud chcete použít tuto možnost, použít stávající pracovní prostor nebo vytvořit nový pracovní prostor Log Analytics pomocí následujících kroků na [vytvořit nový pracovní prostor](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) na portálu. Další informace o prohlížení protokolů v protokoly Azure monitoru najdete v protokolech zobrazení protokoly Azure monitoru.
    * **Protokolovat DataPlaneRequests**: Vyberte tuto možnost, chcete-li protokolovat požadavky na back-end z podkladové služby Azure Cosmos DB distribuovanou platformu pro účty SQL, graf, MongoDB, Cassandra a Table API. Pokud jste archivovat do účtu úložiště, můžete vybrat dobu uchování diagnostických protokolů. Protokoly jsou automaticky odstraněna po vypršení platnosti doby uchování.
    * **Protokolovat MongoRequests**: Vyberte tuto možnost, chcete-li protokolovat požadavky zahájená uživatelem z front-endu služby Azure Cosmos DB pro poskytování Cosmos účty konfigurované pomocí rozhraní API služby Azure Cosmos DB pro MongoDB. Pokud jste archivovat do účtu úložiště, můžete vybrat dobu uchování diagnostických protokolů. Protokoly jsou automaticky odstraněna po vypršení platnosti doby uchování.
    * **Metrika žádosti**: Tuto možnost použijte k ukládání podrobné údaje v [metriky Azure](../azure-monitor/platform/metrics-supported.md). Pokud jste archivovat do účtu úložiště, můžete vybrat dobu uchování diagnostických protokolů. Protokoly jsou automaticky odstraněna po vypršení platnosti doby uchování.

3. Vyberte **Uložit**.

    Pokud se zobrazí chybová zpráva "nepovedlo se aktualizovat diagnostiku pro \<název pracovního prostoru >. Předplatné \<id předplatného > není zaregistrované k používání microsoft.insights, "postupujte [Poradce při potížích s Azure Diagnostics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) pokyny k registraci účtu a opakujte tento postup.

    Pokud chcete změnit, jak diagnostické protokoly jsou uloženy v libovolném okamžiku v budoucnu, vraťte se do této stránky můžete upravit nastavení diagnostického protokolu pro váš účet.

## <a name="turn-on-logging-by-using-azure-cli"></a>Zapnutí protokolování pomocí rozhraní příkazového řádku Azure

Povolit protokolování diagnostiky a metriky pomocí Azure CLI, použijte následující příkazy:

- Pokud chcete povolit ukládání diagnostických protokolů v účtu úložiště, použijte tento příkaz:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   `resource` Je název účtu služby Azure Cosmos DB. Prostředek je ve formátu "/subscriptions/`<subscriptionId>`/resourceGroups/`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/ < Azure_Cosmos_account_name >" `storage-account` je název účtu úložiště, do které jste Chcete odeslat protokoly. Další protokoly se můžete přihlásit prostřednictvím aktualizace hodnoty parametru kategorie "MongoRequests" nebo "DataPlaneRequests". 

- Pokud chcete povolit streamování diagnostických protokolů do centra událostí, použijte tento příkaz:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   `resource` Je název účtu služby Azure Cosmos DB. `event-hub-rule` Je ID event hub pravidlo. 

- Pokud chcete povolit odesílání diagnostických protokolů do pracovního prostoru Log Analytics, použijte tento příkaz:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

Tyto parametry pro povolení více možností výstupu můžete kombinovat.

## <a name="turn-on-logging-by-using-powershell"></a>Zapnutí protokolování s využitím Powershellu

Zapnutí protokolování diagnostiky pomocí Powershellu, musíte na minimální verzi 1.0.1 prostředí Azure Powershell.

Chcete-li nainstalovat Azure PowerShell a přidružit ho ke svému předplatnému Azure, prohlédněte si téma [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

Pokud jste již nainstalovali Azure PowerShell a neznáte verzi, z konzoly typu Powershellu `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Připojení k předplatným
Spusťte relaci Azure PowerShellu a přihlaste se k účtu Azure pomocí následujícího příkazu:  

```powershell
Connect-AzAccount
```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Prostředí Azure PowerShell získá všechna předplatná přidružená k tomuto účtu a ve výchozím nastavení použije první předplatné.

Pokud máte více než jedno předplatné, možná budete muset zadat konkrétní předplatné, která byla použita k vytvoření trezoru klíčů Azure. Pokud chcete zobrazit předplatná vašeho účtu, zadejte následující příkaz:

```powershell
Get-AzSubscription
```

Chcete-li specifikovat předplatné přidružené k účtu Azure Cosmos DB, který jste protokolování, zadejte následující příkaz:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Pokud máte více než jedno předplatné, který je spojen s vaším účtem, je potřeba zadat předplatné, pro kterou chcete použít.
>
>

Další informace o tom, jak nakonfigurovat prostředí Azure PowerShell najdete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

### <a id="storage"></a>Vytvoření nového účtu úložiště pro protokoly
I když používáte existující účet úložiště pro protokoly, v tomto kurzu, vytvoříme nový účet úložiště, který je vyhrazený pro protokoly služby Azure Cosmos DB. Pro usnadnění práce jsme ukládat podrobnosti o účtu úložiště do proměnné s názvem **sa**.

Pro další usnadnění správy v tomto kurzu používáme stejnou skupinu prostředků jako ten, který obsahuje databázi Azure Cosmos DB. Dosaďte svoje hodnoty **ContosoResourceGroup**, **contosocosmosdblogs**, a **střed USA – sever** parametry, podle potřeby:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Pokud se rozhodnete použít existující účet úložiště, musí tento účet používat stejné předplatné jako má vaše předplatné služby Azure Cosmos DB. Účet musíte použít také v modelu nasazení Resource Manager, nikoli modelu nasazení classic.
>
>

### <a id="identify"></a>Identifikace účtu služby Azure Cosmos DB pro svoje protokoly
Nastavte název účtu služby Azure Cosmos DB do proměnné s názvem **účet**, kde **ResourceName** je název účtu služby Azure Cosmos DB.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Povolení protokolování
Chcete-li povolit protokolování pro službu Azure Cosmos DB, použijte `Set-AzDiagnosticSetting` rutinu s proměnné pro nový účet úložiště, účet služby Azure Cosmos DB a kategorii, kterou chcete povolit pro protokolování. Spusťte následující příkaz a nastavit **-povoleno** příznak **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

Výstup příkazu by měl vypadat takto:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

Výstup z příkazu potvrdí, že teď je povoleno protokolování pro vaši databázi a informace se ukládají do vašeho účtu úložiště.

Volitelně můžete také nastavit zásady uchovávání informací pro svoje protokoly tak, aby se starší protokoly automaticky odstraní. Například nastavit zásady uchovávání informací se **- RetentionEnabled** příznak nastaven na **$true**. Nastavte **- RetentionInDays** parametr **90** tak, aby protokoly starší než 90 dnů automaticky odstraněny.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Přístup k protokolům
Protokoly služby Azure Cosmos DB **DataPlaneRequests** kategorií jsou uloženy v **insights protokoly data roviny – požadavky** kontejneru v účtu úložiště, které jste zadali. 

Nejprve vytvořte proměnnou pro název kontejneru. Proměnná se používá v rámci návodu.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Chcete-li vypsat všechny objekty BLOB v tomto kontejneru, zadejte:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Výstup příkazu by měl vypadat takto:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Jak vidíte z tohoto výstupu, objekty BLOB dodržovat zásadu vytváření názvů: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Hodnoty data a času používají UTC.

Protože se dají používat stejný účet úložiště pro shromažďování protokolů u několika prostředků, můžete použít plně kvalifikované ID prostředku v názvu objektu blob přístup ke stažení konkrétních objektů BLOB, které potřebujete. Než to, jak stáhnout všechny objekty BLOB probereme.

Nejprve vytvořte složku, kam stáhnete objekty blob. Příklad:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Potom Získejte seznam všech objektů blob:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Předejte tento seznam prostřednictvím `Get-AzStorageBlobContent` příkaz pro stažení objektů BLOB do cílové složky:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Když spustíte tento druhý příkaz **/** oddělovač v názvech objektů blob vytvoří úplnou strukturu složek v cílové složce. Tato struktura složky se používá ke stažení a uložení objektů BLOB jako soubory.

Chcete-li stahovat objekty blob selektivně, použijte zástupné znaky. Příklad:

* Pokud máte více databází a chcete ke stažení protokolů pro pouze jednu databázi s názvem **CONTOSOCOSMOSDB3**, použijte příkaz:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Pokud máte více skupin prostředků a chcete stáhnout protokoly pro skupinu pouze jeden prostředek, použijte příkaz `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Pokud budete chtít stáhnout všechny protokoly pro měsíc. července 2017, použijte příkaz `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Můžete také spustit následující příkazy:

* Dotaz na stav nastavení diagnostiky pro prostředek databáze, použijte příkaz `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Chcete-li zakázat protokolování **DataPlaneRequests** kategorie pro prostředek účtu databáze, použijte příkaz `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Objekty BLOB, které jsou vráceny v každé z těchto dotazů jsou uložená jako text a formátovaný jako objekt blob JSON, jak je znázorněno v následujícím kódu:

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Další informace o data v jednotlivých objektů blob JSON najdete v tématu [interpretace protokolů služby Azure Cosmos DB](#interpret).

## <a name="manage-your-logs"></a>Správa protokolů

Diagnostické protokoly jsou k dispozici ve vašem účtu po dobu dvou hodin od času, která byla provedena operace služby Azure Cosmos DB. Správa protokolů v účtu úložiště je pouze na vás:

* Použití standardních metod řízení přístupu Azure Zabezpečte protokoly a omezit, kteří k nim přistupovat.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.
* Doba uchování pro požadavky roviny dat, které jsou archivovat do účtu úložiště je nakonfigurovaný na portálu při **protokolu DataPlaneRequests** je vybráno nastavení. Chcete-li změnit toto nastavení, [zapnout protokolování na portálu Azure portal](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Zobrazení protokolů v protokoly Azure monitoru

Pokud jste vybrali **odesílat do Log Analytics** možnost při zapnutí protokolování diagnostiky, diagnostických dat z vašeho kontejneru protokoly Azure monitoru předá do dvou hodin. Když se podíváte na protokoly Azure monitoru ihned po zapnutí protokolování, nezobrazí se žádná data. Právě počkejte po dobu dvou hodin a zkuste to znovu. 

Předtím, než můžete zobrazit protokoly, zkontrolujte a zobrazit, pokud váš pracovní prostor Log Analytics má byla upgradována na nový dotazovací jazyk Kusto. Chcete-li zkontrolovat, otevřete [webu Azure portal](https://portal.azure.com)vyberte **pracovních prostorů Log Analytics** úplně vlevo, pak vyberte název pracovního prostoru, jak je vidět na dalším obrázku. **Pracovní prostor Log Analytics** se zobrazí stránka:

![Azure Monitor protokolů na webu Azure Portal](./media/logging/azure-portal.png)

>[!NOTE]
>Pracovní prostory OMS se teď označují jako pracovní prostory Log Analytics.  

Pokud se zobrazí následující zpráva na **pracovní prostor Log Analytics** stránky, váš pracovní prostor nebyl byla upgradována na nový jazyk. Další informace o tom, jak upgradovat na nový dotazovací jazyk, v tématu [Upgrade pracovního prostoru Azure Log Analytics na nové prohledávání protokolů](../log-analytics/log-analytics-log-search-upgrade.md). 

![Protokoly Azure monitoru upgrade zprávy](./media/logging/upgrade-notification.png)

Chcete-li zobrazit diagnostická data v protokolech Azure Monitor, otevřete **prohledávání protokolů** stránky v levé nabídce nebo **správu** oblasti na stránce, jak je znázorněno na následujícím obrázku:

![Možnosti protokolu hledání na webu Azure Portal](./media/logging/log-analytics-open-log-search.png)

Teď, když jste povolili shromažďování dat, spusťte následující příklad prohledávání protokolu pomocí nového dotazovacího jazyka naleznete v protokolech 10 nejnovější `AzureDiagnostics | take 10`.

![Ukázky prohledávání protokolů pro 10 poslední protokoly](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Dotazy

Tady jsou některé další dotazy, které můžete zadat do **prohledávání protokolů** pole, které vám pomohou monitorovat kontejnery služby Azure Cosmos DB. Tyto dotazy pracovat [nový jazyk](../log-analytics/log-analytics-log-search-upgrade.md). 

Další informace o význam data, která je vrácený každou prohledávání protokolů, najdete v článku [interpretace protokolů služby Azure Cosmos DB](#interpret).

* Dotaz pro všechny diagnostických protokolů ze služby Azure Cosmos DB pro zadané časové období:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Dotaz na 10 nejvíce nedávno protokolovaných událostí:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Dotaz pro všechny operace, seskupené podle typu operace:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Dotaz pro všechny operace, seskupené podle **prostředků**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Dotaz pro všechny aktivity uživatelů, seskupené podle prostředků:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Tento příkaz je pro protokol aktivit, diagnostický protokol.

* Dotaz, pro kterou trvá déle než 3 milisekund operace:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Dotaz, který Agent běží operace:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Dotaz pro, pokud byly provedeny dlouho běžící operace:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Další informace o tom, jak používat nový jazyk prohledávání protokolů, najdete v části [Principy prohledávání protokolů v protokoly Azure monitoru](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretujte svoje protokoly

Diagnostická data ukládaná ve službě Azure Storage a Azure Monitor protokoly používá podobné schématu. 

Následující tabulka popisuje obsah každé položky protokolu.

| Azure Storage pole nebo vlastnost | Vlastnost protokoly Azure monitoru | Popis |
| --- | --- | --- |
| **čas** | **TimeGenerated** | Datum a čas (UTC), kdy došlo k chybě operace. |
| **ID prostředku** | **Prostředek** | Účet Azure Cosmos DB, pro kterou jsou povolené protokoly.|
| **Kategorie** | **Kategorie** | Pro protokoly služby Azure Cosmos DB **DataPlaneRequests** je k dispozici pouze hodnota. |
| **OperationName** | **OperationName** | Název operace Tato hodnota může být některý z následujících operací: Vytvoření, aktualizaci, čtení, ReadFeed, odstranění, nahrazení, spusťte, SqlQuery, dotaz, JSQuery, Head, HeadFeed nebo Upsert.   |
| **Vlastnosti** | neuvedeno | Obsah tohoto pole jsou popsány v řádcích, které následují. |
| **ID aktivity** | **activityId_g** | Jedinečný identifikátor GUID pro protokolovaných operací. |
| **userAgent** | **userAgent_s** | Řetězec, který určuje uživatelský agent klienta, který provádí požadavek. Formát je {uživatelské jméno agenta} / {version}.|
| **requestResourceType** | **requestResourceType_s** | Typ prostředku, u níž. Tato hodnota může být některý z těchto typů prostředků: Databáze, kontejner, dokument, přílohy, uživatele, oprávnění, uložené procedury StoredProcedure, aktivační událost, UserDefinedFunction nebo nabídky. |
| **statusCode** | **statusCode_s** | Stav odpovědi operace. |
| **requestResourceId** | **ID prostředku** | ID prostředku, které se vztahují k požadavku. Hodnota může odkazovat na databaseRid, collectionRid nebo documentRid v závislosti na operaci provést.|
| **clientIpAddress** | **clientIpAddress_s** | IP adresa klienta. |
| **requestCharge** | **requestCharge_s** | Počet rezervovaných jednotek, které jsou používány operace |
| **collectionRid** | **collectionId_s** | Jedinečný Identifikátor pro kolekci.|
| **Doba trvání** | **duration_s** | Doba trvání operace v impulzech. |
| **requestLength** | **requestLength_s** | Délka požadavku v bajtech. |
| **responseLength** | **responseLength_s** | Délka odpovědi v bytech.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Tato hodnota je prázdný, když [tokenech prostředků](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) se používají pro ověřování. Hodnota odkazuje na ID prostředku uživatele. |

## <a name="next-steps"></a>Další postup

- Pochopit postup při povolování protokolování a metrik a protokolů kategorií, které podporují různé služby Azure, přečtěte si i [přehled metrik v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) a [přehled o diagnostické protokoly Azure ](../azure-monitor/platform/diagnostic-logs-overview.md) článků.
- Přečtěte si tyto články Další informace o službě event hubs:
   - [Co je Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Čtení [stáhnout metrik a diagnostických protokolů ze služby Azure Storage](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Čtení [Principy prohledávání protokolů v protokoly Azure monitoru](../log-analytics/log-analytics-log-search-new.md).
