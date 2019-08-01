---
title: Protokolování Analýza úložiště Azure
description: Naučte se protokolovat podrobnosti o požadavcích provedených proti Azure Storage.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: e46064076fb5d38fbde94bd4bb7e5dfbcff7e3b4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68556162"
---
# <a name="azure-storage-analytics-logging"></a>Protokolování analýzy Azure Storage

Analýza úložiště protokoluje podrobné informace o úspěšných a neúspěšných požadavcích službě úložiště. Tyto informace lze použít k monitorování jednotlivých požadavků a k diagnostice problémů se službou úložiště. Požadavky jsou protokolovány na základě nejlepší úsilí.

 Protokolování Analýza úložiště není pro váš účet úložiště standardně povolené. Můžete ji povolit v [Azure Portal](https://portal.azure.com/). Podrobnosti najdete v tématu [monitorování účtu úložiště v Azure Portal](/azure/storage/storage-monitor-storage-account). Analýza úložiště můžete také povolit programově prostřednictvím REST API nebo klientské knihovny. K povolení Analýza úložiště pro každou službu použijte vlastnosti [získat službu BLOB](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)Service, [získat vlastnosti služby front](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)a [získat operace vlastností služby Table](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) Service.

 Položky protokolu jsou vytvořeny pouze v případě, že jsou zadány požadavky na koncový bod služby. Pokud například účet úložiště obsahuje aktivitu v koncovém bodu objektu blob, ale ne ve svých koncových bodech tabulky nebo fronty, vytvoří se jenom protokoly týkající se Blob service.

> [!NOTE]
>  Protokolování Analýza úložiště je aktuálně k dispozici pouze pro služby objektů blob, Queue a Table. Účet Premium Storage se ale nepodporuje.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="requests-logged-in-logging"></a>Požadavky zaznamenávané v protokolování
### <a name="logging-authenticated-requests"></a>Protokolování ověřených požadavků

 Protokolují se následující typy ověřených požadavků:

- Úspěšné požadavky
- Neúspěšné požadavky, včetně časového limitu, omezování, sítě, autorizace a dalších chyb
- Žádosti s použitím sdíleného přístupového podpisu (SAS) nebo OAuth, včetně neúspěšných a úspěšných požadavků
- Požadavky na analytické údaje

  Požadavky vytvořené Analýza úložiště samotné, například vytvoření nebo odstranění protokolu, nejsou protokolovány. Úplný seznam protokolovaných dat je popsán v tématech [Analýza úložiště protokolovaných operací a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [Analýza úložiště formátu protokolu](/rest/api/storageservices/storage-analytics-log-format) .

### <a name="logging-anonymous-requests"></a>Protokolování anonymních požadavků

 Protokolují se tyto typy anonymních požadavků:

- Úspěšné požadavky
- Chyby serveru
- Chyby časového limitu pro klienta i server
- Neúspěšné žádosti o získání s kódem chyby 304 (nezměněno)

  Všechny ostatní neúspěšné anonymní požadavky nejsou protokolovány. Úplný seznam protokolovaných dat je popsán v tématech [Analýza úložiště protokolovaných operací a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a [Analýza úložiště formátu protokolu](/rest/api/storageservices/storage-analytics-log-format) .

## <a name="how-logs-are-stored"></a>Způsob ukládání protokolů

Všechny protokoly se ukládají v objektech blob bloku v kontejneru `$logs`s názvem, který se automaticky vytvoří, když je povolený analýza úložiště pro účet úložiště. Kontejner se nachází v oboru názvů objektu BLOB účtu úložiště, například: `http://<accountname>.blob.core.windows.net/$logs`. `$logs` Po povolení Analýza úložiště nelze tento kontejner odstranit, i když jeho obsah lze odstranit. Pokud použijete nástroj pro procházení úložiště k přímému přechodu do kontejneru, zobrazí se všechny objekty blob, které obsahují vaše data protokolování.

> [!NOTE]
>  `$logs` Kontejner se nezobrazuje, když je provedena operace výpisu kontejneru, jako je například operace Container lists. K němu je potřeba získat přímý pøístup. Můžete například použít operaci list BLOBs pro přístup k objektům blob v `$logs` kontejneru.

Jak jsou požadavky protokolovány, Analýza úložiště odešle mezilehlé výsledky jako bloky. Analýza úložiště tyto bloky pravidelně zařadí a zpřístupní je jako objekt BLOB. Může trvat až hodinu, než se data protokolu objeví v objektech blob v kontejneru **$logs** , protože frekvence, s jakou služba úložiště vyprázdní zapisovače protokolů. Pro protokoly vytvořené ve stejnou hodinu můžou existovat duplicitní záznamy. Zaškrtnutím čísla **ID** a **operace** můžete určit, jestli je záznam duplicitní.

Pokud máte velké množství dat protokolu s více soubory pro každou hodinu, pak můžete použít metadata objektu BLOB k určení dat, která protokol obsahuje, prozkoumáním polí metadat objektů BLOB. To je užitečné také v případě, že při zápisu dat do souborů protokolu může dojít k prodlevě: metadata objektů BLOB poskytují přesnější indikaci obsahu objektu blob, než je název objektu BLOB.

Většina nástrojů pro procházení úložiště vám umožní zobrazit metadata objektů BLOB. Tyto informace můžete přečíst také pomocí PowerShellu nebo prostřednictvím kódu programu. Následující fragment kódu prostředí PowerShell představuje příklad filtrování seznamu objektů BLOB protokolu podle názvu pro určení času a metadaty k identifikaci pouze těch protokolů, které obsahují operace **zápisu** .  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Informace o tom, jak programově vypsat objekty blob, najdete v tématech [vytváření výčtu prostředků objektů BLOB](https://msdn.microsoft.com/library/azure/hh452233.aspx) a [nastavení a načítání vlastností a metadat pro prostředky objektů BLOB](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Zásady vytváření názvů protokolů

 Každý protokol bude napsaný v následujícím formátu:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 V následující tabulce jsou popsány jednotlivé atributy v názvu protokolu:

|Atribut|Popis|
|---------------|-----------------|
|`<service-name>`|Název služby úložiště Například: `blob`, `table`, nebo`queue`|
|`YYYY`|Rok čtyř číslice pro protokol. Příklad: `2011`|
|`MM`|Dva číslice měsíce pro protokol. Příklad: `07`|
|`DD`|Dva číselné dny pro protokol. Příklad: `31`|
|`hh`|Dvě číslice hodiny, která označuje počáteční hodinu pro protokoly ve formátu UTC (24 hodin). Příklad: `18`|
|`mm`|Dva číselné číslo, které označuje počáteční minutu pro protokoly. **Poznámka:**  Tato hodnota není v aktuální verzi Analýza úložiště podporována a její hodnota bude vždy `00`.|
|`<counter>`|Čítač založený na nule se šesti číslicemi, který indikuje počet objektů BLOB protokolu generovaných pro službu úložiště v časovém intervalu. Tento čítač začíná na `000000`. Příklad: `000001`|

 Následuje úplný název ukázkového protokolu, který kombinuje výše uvedené příklady:

 `blob/2011/07/31/1800/000001.log`

 Následuje ukázkový identifikátor URI, který lze použít pro přístup k výše uvedenému protokolu:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Po zaznamenání žádosti o úložiště se výsledný název protokolu koreluje do hodiny, kdy se požadovaná operace dokončila. Pokud se například požadavek getblob dokončil v 6:17:30 v 7/31/2011, protokol se zapíše s následující předponou:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadata protokolu

 Všechny objekty blob protokolu jsou uložené s metadaty, která se dají použít k identifikaci dat protokolování, která objekt BLOB obsahuje. Následující tabulka popisuje jednotlivé atributy metadat:

|Atribut|Popis|
|---------------|-----------------|
|`LogType`|Popisuje, jestli protokol obsahuje informace týkající se operací čtení, zápisu nebo odstranění. Tato hodnota může obsahovat jeden typ nebo kombinaci všech tří, oddělených čárkami.<br /><br /> Příklad 1: `write`<br /><br /> Příklad 2: `read,write`<br /><br /> Příklad 3:`read,write,delete`|
|`StartTime`|Čas nejdřívějšího záznamu v protokolu ve formě `YYYY-MM-DDThh:mm:ssZ`. Příklad: `2011-07-31T18:21:46Z`|
|`EndTime`|Poslední čas záznamu v protokolu ve formě `YYYY-MM-DDThh:mm:ssZ`. Příklad: `2011-07-31T18:22:09Z`|
|`LogVersion`|Verze formátu protokolu|

 Následující seznam obsahuje kompletní ukázková metadata pomocí výše uvedených příkladů:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Povolit protokolování úložiště

Protokolování úložiště můžete povolit pomocí Azure Portal, PowerShellu a sad Storage SDK.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Povolení protokolování úložiště pomocí Azure Portal  

V Azure Portal použijte okno **nastavení diagnostiky (Classic)** k řízení protokolování úložiště, které je dostupné v části **monitorování (Classic)** v okně **nabídky**účtu úložiště.

Můžete zadat služby úložiště, které chcete protokolovat, a dobu uchování (ve dnech) pro zaznamenaná data.  

### <a name="enable-storage-logging-using-powershell"></a>Povolení protokolování úložiště pomocí prostředí PowerShell  

 Pomocí prostředí PowerShell na místním počítači můžete nakonfigurovat protokolování úložiště ve vašem účtu úložiště pomocí rutiny Azure PowerShell rutiny **Get-AzureStorageServiceLoggingProperty** pro načtení aktuálních nastavení a rutiny **. Set-AzureStorageServiceLoggingProperty** pro změnu aktuálního nastavení.  

 Rutiny, které řídí protokolování úložiště, používají parametr **LoggingOperations** , který je řetězec obsahující seznam typů požadavků oddělených čárkami, které se mají protokolovat. Tři možné typy požadavků jsou **čtení**, **zápis**a **odstranění**. Chcete-li přepnout protokolování, použijte hodnotu **none** pro parametr **LoggingOperations** .  

 Následující příkaz přepne při protokolování žádostí o čtení, zápis a odstranění v Služba front ve vašem výchozím účtu úložiště s možností uchování na pět dní:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 Následující příkaz odpíná protokolování služby Table Service ve vašem výchozím účtu úložiště:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Informace o tom, jak nakonfigurovat rutiny Azure PowerShell pro práci s předplatným Azure a jak vybrat výchozí účet úložiště, který se má použít, najdete v těchto tématech: [Jak nainstalovat a nakonfigurovat Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Povolení protokolování úložiště prostřednictvím kódu programu  

 Kromě použití Azure Portal nebo rutin Azure PowerShell k řízení protokolování úložiště můžete použít také jedno z rozhraní API pro Azure Storage. Pokud například používáte jazyk .NET, můžete použít knihovnu klienta úložiště.  

 Třídy **CloudBlobClient**, **CloudQueueClient**a **cloudtableclient vám** mají metody jako **SetServiceProperties** a **SetServicePropertiesAsync** , které přijímají objekt **ServiceProperties** jako ukazatele. K nakonfigurování protokolování úložiště můžete použít objekt **ServiceProperties** . Například následující C# fragment kódu ukazuje, jak změnit protokol, který je protokolován, a dobu uchování pro protokolování fronty:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Další informace o použití jazyka .NET ke konfiguraci protokolování úložiště najdete v tématu [Reference k klientské knihovně pro úložiště](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Obecné informace o konfiguraci protokolování úložiště pomocí REST API najdete v tématu [povolení a konfigurace analýza úložiště](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Stáhnout data protokolu protokolování úložiště

 Pokud chcete zobrazit a analyzovat data protokolu, měli byste si stáhnout objekty blob, které obsahují data protokolu, která vás zajímají, do místního počítače. Mnoho nástrojů pro procházení úložiště vám umožní stahovat objekty BLOB z účtu úložiště. k stažení dat protokolu můžete Azure Storage použít také AzCopy tým dodaný do příkazového řádku Azure Copy Tool ().  

 Abyste se ujistili, že jste si stáhli data protokolu, která vás zajímají, a nestahovat stejná data protokolu více než jednou:  

-   Pomocí konvence pojmenování data a času pro objekty BLOB obsahující data protokolu můžete sledovat, které objekty blob jste už stáhli k analýze, abyste se vyhnuli opakovanému stažení stejných dat více než jednou.  

-   Použijte metadata v objektech blob obsahující data protokolu k identifikaci konkrétního období, pro které objekt BLOB uchovává data protokolu k identifikaci přesného objektu blob, který potřebujete stáhnout.  

> [!NOTE]
>  AzCopy je součástí sady Azure SDK, ale nejnovější verzi si můžete kdykoli stáhnout z [https://aka.ms/AzCopy](https://aka.ms/AzCopy). Ve výchozím nastavení se AzCopy nainstaluje do složky **C:\Program Files (x86) \Microsoft SDKs\Windows Azure\AzCopy**a tuto složku byste měli přidat do své cesty předtím, než se pokusíte spustit nástroj v příkazovém řádku nebo v okně PowerShellu.  

 Následující příklad ukazuje, jak můžete stáhnout data protokolu pro službu fronty po dobu od 09 do 12:00, od 10 DOP. a 11 AM 20. května 2014. Parametr **/s** způsobí, že AzCopy sestaví strukturu místních složek na základě dat a časů v názvech souborů protokolu; parametr **/v** způsobí, že AzCopy vyprodukuje podrobný výstup; parametr **/y** způsobí, že AzCopy přepíše všechny místní soubory. Nahraďte **<\> yourstorageaccount** názvem vašeho účtu úložiště a nahraďte **< yourstoragekey\>**  klíčem účtu úložiště.  

```
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 AzCopy má také několik užitečných parametrů, které řídí, jak nastaví čas poslední změny ve stažených souborech a zda se pokusí stáhnout soubory starší nebo novější než soubory, které již existují na místním počítači. Můžete ho také spustit v režimu s možno startem. Úplné podrobnosti najdete v nápovědě spuštěním **AzCopy/?** . systému.  

 Příklad toho, jak programově stahovat data protokolu, najdete v blogu Azure Storage v příspěvku [Windows: Pomocí protokolů Sledujte požadavky](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) na úložiště a vyhledejte slovo "DumpLogs" na stránce.  

 Po stažení dat protokolu můžete zobrazit položky protokolu v souborech. Tyto soubory protokolu používají textový formát s oddělovači, který dokáže analyzovat mnoho nástrojů pro čtení protokolů, včetně Microsoft Message Analyzer (Další informace najdete v tématu [monitorování, diagnostika a řešení potíží s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). Různé nástroje mají různá zařízení pro formátování, filtrování, řazení a reklamu při hledání obsahu souborů protokolu. Další informace o formátu a obsahu souboru protokolu protokolování úložiště najdete v článku [Formát protokolu analýza úložiště](/rest/api/storageservices/storage-analytics-log-format) a [Analýza úložiště protokolované operace a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Další postup

* [Formát protokolu Analýza úložiště](/rest/api/storageservices/storage-analytics-log-format)
* [Analýza úložiště protokolované operace a stavové zprávy](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Analýza úložiště metriky (klasické)](storage-analytics-metrics.md)
