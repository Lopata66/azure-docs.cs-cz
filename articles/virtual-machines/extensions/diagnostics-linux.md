---
title: Diagnostické rozšíření pro Azure COMPUTE – Linux
description: Jak nakonfigurovat diagnostické rozšíření Azure Linux (LAD) pro shromažďování metrik a protokolování událostí z virtuálních počítačů se systémem Linux spuštěných v Azure.
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh
ms.openlocfilehash: d9375d09219d2655bd9947c0953557f4a1bf8f3c
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199610"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Monitorování metrik a protokolů pomocí diagnostického rozšíření systému Linux

Tento dokument popisuje verzi 3,0 a novější diagnostické rozšíření pro Linux.

> [!IMPORTANT]
> Informace o verzi 2,3 a starší najdete v [tomto dokumentu](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Úvod

Diagnostické rozšíření pro Linux pomáhá uživateli monitorovat stav virtuálního počítače se systémem Linux běžícího na Microsoft Azure. Má následující možnosti:

* Shromažďuje z virtuálního počítače metriky výkonu systému a ukládá je do konkrétní tabulky v určeném účtu úložiště.
* Načte události protokolu z syslog a uloží je do konkrétní tabulky v určeném účtu úložiště.
* Umožňuje uživatelům přizpůsobit metriky dat, které se shromáždí a nahrají.
* Umožňuje uživatelům přizpůsobit zařízení syslog a úrovně závažnosti shromažďovaných a odesílaných událostí.
* Umožňuje uživatelům odeslat zadané soubory protokolu do určené tabulky úložiště.
* Podporuje odesílání metrik a protokolování událostí do libovolných koncových bodů EventHub a objektů BLOB ve formátu JSON v určeném účtu úložiště.

Toto rozšíření funguje v obou modelech nasazení Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalace rozšíření na virtuálním počítači

Toto rozšíření můžete povolit pomocí rutin Azure PowerShell, skriptů Azure CLI, šablon ARM nebo Azure Portal. Další informace najdete v tématu [funkce rozšíření](features-linux.md).

Tyto pokyny k instalaci a [Ukázková konfigurace ke stažení](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) nakonfigurují lad 3,0 na:

* Zachyťte a uložte stejné metriky, které poskytla služba LAD 2,3;
* zachytit užitečnou sadu metrik systému souborů, která je novinkou LAD 3,0;
* zachytit výchozí kolekci syslog povolenou LAD 2,3;
* Umožněte Azure Portal prostředí pro vytváření grafů a upozorňování na metriky virtuálních počítačů.

Konfigurace ke stažení je pouze příklad. upravte ji tak, aby vyhovovala vašim potřebám.

### <a name="prerequisites"></a>Předpoklady

* **Agent Azure Linux verze 2.2.0 nebo novější**. Většina imagí z Galerie virtuálních počítačů Azure pro Linux zahrnuje verzi 2.2.7 nebo novější. Spuštěním `/usr/sbin/waagent -version` potvrďte verzi nainstalovanou na virtuálním počítači. Pokud na virtuálním počítači běží starší verze agenta hosta, aktualizujte ho podle [těchto pokynů](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) .
* **Azure CLI**. Nastavte na svém počítači prostředí [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) .
* Příkaz wget, pokud ho ještě nemáte: Spusťte `sudo apt-get install wget`.
* Existující předplatné Azure a existující účet úložiště v rámci něj pro ukládání dat.
* Seznam podporovaných distribucí systému Linux je https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Ukázková instalace

Před spuštěním Vyplňte správné hodnoty proměnných v první části:

```bash
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

Adresa URL ukázkové konfigurace a její obsah se mohou změnit. Stáhněte si kopii souboru JSON s nastavením portálu a přizpůsobte si ho podle svých potřeb. Všechny šablony nebo automatizace, které vytvoříte, by měly používat vlastní kopii místo toho, aby tuto adresu URL stáhli pokaždé.

#### <a name="powershell-sample"></a>Ukázka PowerShellu

```Powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>Aktualizace nastavení rozšíření

Po změně chráněných nebo veřejných nastavení je můžete nasadit do virtuálního počítače spuštěním stejného příkazu. Pokud se v nastavení změní nějaké změny, do rozšíření se pošle aktualizované nastavení. LAD znovu načte konfiguraci a sám se restartuje.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migrace z předchozích verzí rozšíření

Nejnovější verze rozšíření je **3,0**. **Všechny staré verze (2. x) jsou zastaralé a mohou být publikovány od 31. července 2018 nebo po ní**.

> [!IMPORTANT]
> Toto rozšíření přináší zásadní změny v konfiguraci rozšíření. Tato změna byla provedena za účelem zlepšení zabezpečení rozšíření. v důsledku toho nelze zachovat zpětnou kompatibilitu s 2. x. Také Vydavatel rozšíření pro toto rozšíření je jiný než Vydavatel pro verze 2. x.
>
> Chcete-li provést migraci z 2. x na tuto novou verzi rozšíření, je nutné odinstalovat starou příponu (pod starým názvem vydavatele) a pak nainstalovat verzi 3 rozšíření.

Doporučit

* Nainstalujte rozšíření s povoleným automatickým upgradem dílčí verze.
  * V případě virtuálních počítačů modelu nasazení Classic zadejte jako verzi hodnotu 3. *, pokud chcete rozšíření instalovat prostřednictvím Azure XPLAT CLI nebo PowerShellu.
  * V Azure Resource Manager virtuálních počítačů modelu nasazení přidejte do šablony nasazení virtuálního počítače "" autoUpgradeMinorVersion ": true.
* Pro LAD 3,0 použijte nový nebo jiný účet úložiště. Mezi LAD 2,3 a LAD 3,0 dochází k několika malým nekompatibilitám, které sdílejí účet komplikované:
  * LAD 3,0 ukládá události syslog do tabulky s jiným názvem.
  * CounterSpecifier řetězce pro metriky `builtin` se liší v LAD 3,0.

## <a name="protected-settings"></a>Chráněná nastavení

Tato sada informací o konfiguraci obsahuje citlivé informace, které by měly být chráněny před veřejným zobrazením, například přihlašovací údaje úložiště. Tato nastavení se přenášejí do a ukládají rozšíření v šifrované podobě.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Název | Hodnota
---- | -----
storageAccountName | Název účtu úložiště, ve kterém se má rozšíření zapsat data
storageAccountEndPoint | volitelné Koncový bod identifikující Cloud, ve kterém existuje účet úložiště. Pokud toto nastavení chybí, LAD ve výchozím nastavení veřejný cloud Azure, `https://core.windows.net`. Pokud chcete použít účet úložiště v Azure Německo, Azure Government nebo Azure Čína, nastavte tuto hodnotu odpovídajícím způsobem.
storageAccountSasToken | [Token SAS účtu](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) pro služby BLOB a Table services (`ss='bt'`), který se vztahuje na kontejnery a objekty (`srt='co'`), což uděluje oprávnění k přidávání, vytváření, výpisům, aktualizaci a zápisu (`sp='acluw'`). Nezahrnujte *úvodní* otazník (?).
mdsdHttpProxy | volitelné Informace o proxy serveru HTTP potřebné k povolení rozšíření pro připojení k zadanému účtu úložiště a koncovému bodu
sinksConfig | volitelné Podrobnosti o alternativních umístěních, na které se dají doručovat metriky a události Konkrétní podrobnosti o jednotlivých datových jímkach podporovaných rozšířením jsou uvedené v následujících oddílech.

K získání tokenu SAS v rámci šablony Správce prostředků použijte funkci **listAccountSas** . Příklad šablony naleznete v tématu [Příklad funkce list](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Požadovaný token SAS můžete snadno vytvořit prostřednictvím Azure Portal.

1. Vyberte účet úložiště pro obecné účely, na který chcete rozšíření zapisovat.
1. V části nastavení v nabídce vlevo vyberte Shared Access Signature (sdílený přístupový podpis).
1. Proveďte příslušné oddíly, jak je popsáno výše.
1. Klikněte na tlačítko generovat SAS.

![image](./media/diagnostics-linux/make_sas.png)

Zkopírujte vygenerované SAS do pole storageAccountSasToken; Odeberte úvodní otazník (?).

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

Tento volitelný oddíl definuje další cíle, do kterých rozšíření odesílá informace, které shromažďuje. Pole "jímka" obsahuje objekt pro každou další datovou jímku. Atribut Type určuje ostatní atributy v objektu.

Prvek | Hodnota
------- | -----
jméno | Řetězec, který se používá k odkazování na tuto jímku na jiné místo v konfiguraci rozšíření.
type | Typ definované jímky. Určuje další hodnoty (pokud existují) v instancích tohoto typu.

Diagnostické rozšíření pro Linux verze 3,0 podporuje dva typy jímky: EventHub a JsonBlob.

#### <a name="the-eventhub-sink"></a>Jímka EventHub

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

Položka "sasURL" obsahuje úplnou adresu URL, včetně tokenu SAS, pro centrum událostí, do kterého se mají data publikovat. LAD vyžaduje, aby pojmenování SAS vyžadovalo zásadu, která povoluje nárok na odeslání. Příklad:

* Vytvořte obor názvů Event Hubs s názvem `contosohub`
* Vytvořte centrum událostí v oboru názvů s názvem `syslogmsgs`
* Vytvořte zásadu sdíleného přístupu v centru událostí s názvem `writer`, která umožňuje odeslat deklaraci identity.

Pokud jste vytvořili SAS vhodným až do půlnoci UTC od 1. ledna 2018, může být sasURL hodnota:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Další informace o generování a načítání informací o tokenech SAS pro Event Hubs naleznete na [této webové stránce](https://docs.microsoft.com/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="the-jsonblob-sink"></a>Jímka JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Data směrované do jímky JsonBlob se ukládají v objektech blob ve službě Azure Storage. Každá instance LAD vytváří objekt BLOB každou hodinu pro každý název jímky. Každý objekt BLOB vždy obsahuje syntakticky platné pole JSON objektu. Nové položky jsou atomicky přidány do pole. Objekty blob jsou uloženy v kontejneru se stejným názvem, jako má jímka. Pravidla úložiště Azure pro názvy kontejnerů objektů BLOB se vztahují na názvy jímky JsonBlob: mezi 3 a 63 malými alfanumerickými znaky ASCII nebo pomlčkami.

## <a name="public-settings"></a>Veřejné nastavení

Tato struktura obsahuje různé bloky nastavení, které řídí informace shromažďované rozšířením. Každé nastavení je volitelné. Zadáte-li `ladCfg`, je nutné zadat také `StorageAccount`.

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Prvek | Hodnota
------- | -----
StorageAccount | Název účtu úložiště, ve kterém se má rozšíření zapsat data Musí se jednat o stejný název, jako je zadaný v [Nastavení Protected](#protected-settings).
mdsdHttpProxy | volitelné Stejné jako v [chráněných nastaveních](#protected-settings). Veřejná hodnota je přepsána soukromou hodnotou, pokud je nastavena. V [chráněných nastaveních](#protected-settings)umístěte nastavení proxy serveru, jako je třeba heslo.

Zbývající prvky jsou podrobně popsány v následujících oddílech.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

Tato volitelná struktura ovládá shromažďování metrik a protokolů pro doručování do služby Azure metrik a dalších datových jímka. Je nutné zadat buď `performanceCounters`, nebo `syslogEvents` nebo obojí. Je nutné zadat strukturu `metrics`.

Prvek | Hodnota
------- | -----
eventVolume | volitelné Určuje počet oddílů vytvořených v rámci tabulky úložiště. Musí se jednat o jednu z `"Large"`, `"Medium"`nebo `"Small"`. Pokud není zadaný, použije se výchozí hodnota `"Medium"`.
sampleRateInSeconds | volitelné Výchozí interval mezi kolekcemi nezpracovaných (neagregovaných) metrik. Nejmenší podporovaná vzorkovací frekvence je 15 sekund. Pokud není zadaný, použije se výchozí hodnota `15`.

#### <a name="metrics"></a>Průzkumníku metrik

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Prvek | Hodnota
------- | -----
resourceId | ID prostředku Azure Resource Manager virtuálního počítače nebo sady škálování virtuálního počítače, do které virtuální počítač patří. Toto nastavení musí být zadáno také v případě, že se v konfiguraci používá jakákoli jímka JsonBlob.
scheduledTransferPeriod | Frekvence, s jakou se mají vypočítat agregované metriky a jejich přenos do metrik Azure, vyjádřené jako časový interval 8601. Nejmenší perioda přenosu je 60 sekund, tj. PT1M. Je nutné zadat alespoň jeden scheduledTransferPeriod.

Ukázky metrik zadané v části čítače výkonu se shromažďují každých 15 sekund nebo vzorkovací frekvence, která je explicitně definovaná pro čítač. Pokud se zobrazí více scheduledTransferPeriod frekvencí (jako v příkladu), každá agregace je vypočítána nezávisle.

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

Tento volitelný oddíl řídí kolekci metrik. Nezpracované vzorky jsou agregované pro každý [scheduledTransferPeriod](#metrics) , aby se vytvořily tyto hodnoty:

* střední hodnotu
* minimum
* maximum
* Poslední shromážděná hodnota
* počet nezpracovaných vzorků používaných k výpočtu agregace

Prvek | Hodnota
------- | -----
jímky | volitelné Čárkami oddělený seznam názvů umyvadel, na které LAD odesílá agregované výsledky metriky. Všechny agregované metriky jsou publikovány v každé uvedené jímky. Viz [sinksConfig](#sinksconfig). Příklad: `"EHsink1, myjsonsink"`.
type | Určuje skutečného poskytovatele metriky.
Třída | Společně s "čítač" identifikuje konkrétní metriku v oboru názvů poskytovatele.
counter | Společně s "Class" identifikuje konkrétní metriku v oboru názvů poskytovatele.
counterSpecifier | Identifikuje konkrétní metriku v oboru názvů metrik Azure.
condition | volitelné Vybere konkrétní instanci objektu, na kterou metrika aplikuje, nebo vybere agregaci napříč všemi instancemi daného objektu. Další informace najdete v tématu `builtin` – definice metriky.
sampleRate | JE 8601 interval, který nastavuje rychlost shromažďování nezpracovaných vzorků pro tuto metriku. Pokud není nastaven, interval shromažďování je nastaven hodnotou [sampleRateInSeconds](#ladcfg). Nejkratší podporovaná vzorkovací frekvence je 15 sekund (PT15S).
unit | Mělo by se jednat o jeden z těchto řetězců: "Count", "bytes", "Seconds", "PERCENT", "CountPerSecond", "BytesPerSecond", "milisekund". Definuje jednotku pro metriku. Spotřebitelé shromážděných dat očekávají, že hodnoty shromážděných dat odpovídají této jednotce. LAD ignoruje toto pole.
displayName | Popisek (v jazyce určeném pomocí přidruženého nastavení národního prostředí), který se má připojit k těmto datům v Azure metrik. LAD ignoruje toto pole.

CounterSpecifier je libovolný identifikátor. Příjemci metrik, jako je například funkce Azure Portaling a upozorňování, používají counterSpecifier jako klíč, který identifikuje metriku nebo instanci metriky. U `builtin`ch metrik doporučujeme používat counterSpecifier hodnoty, které začínají na `/builtin/`. Pokud shromažďujete konkrétní instanci metriky, doporučujeme připojit identifikátor instance k hodnotě counterSpecifier. Několik příkladů:

* `/builtin/Processor/PercentIdleTime` – Průměrná doba nečinnosti v rámci všech vCPU
* `/builtin/Disk/FreeSpace(/mnt)` – volné místo pro systém souborů/mnt
* `/builtin/Disk/FreeSpace` – průměrně volné místo ve všech připojených systémech souborů

LAD ani Azure Portal neočekává, že counterSpecifier hodnota odpovídá jakémukoli vzoru. Být konzistentní při sestavování hodnot counterSpecifier.

Když zadáte `performanceCounters`, LAD vždycky zapisuje data do tabulky ve službě Azure Storage. Můžete mít stejná data zapsaná do objektů BLOB JSON nebo Event Hubs, ale nemůžete zakázat ukládání dat do tabulky. Všechny instance diagnostického rozšíření nakonfigurovaného tak, aby používaly stejný název a koncový bod účtu úložiště, přidají ke stejné tabulce své metriky a protokoly. Pokud je do stejného oddílu tabulky zapisováno příliš mnoho virtuálních počítačů, může Azure omezit zápisy do tohoto oddílu. Nastavení eventVolume způsobí, že položky budou rozloženy mezi 1 (malá), 10 (střední) nebo 100 (velké) různé oddíly. Obvykle je "střední" dostačující, aby se zajistilo, že provoz není omezený. Funkce metrik Azure v Azure Portal používá data v této tabulce k tvorbě grafů nebo k aktivaci výstrah. Název tabulky je zřetězení těchto řetězců:

* `WADMetrics`
* "ScheduledTransferPeriod" pro agregované hodnoty uložené v tabulce
* `P10DV2S`
* Datum ve formátu "RRRRMMDD", které se mění každých 10 dní

Příklady zahrnují `WADMetricsPT1HP10DV2S20170410` a `WADMetricsPT1MP10DV2S20170609`.

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

Tento volitelný oddíl řídí shromažďování událostí protokolu z syslog. Pokud je oddíl vynechán, události syslog nejsou zachyceny vůbec.

Kolekce syslogEventConfiguration má jednu položku pro každé zařízení syslog, které vás zajímá. Pokud minSeverity je "NONE" pro konkrétní zařízení, nebo pokud se toto zařízení nezobrazí v prvku vůbec, nebudou zachyceny žádné události z tohoto zařízení.

Prvek | Hodnota
------- | -----
jímky | Čárkami oddělený seznam názvů umyvadel, na které se jednotlivé události protokolu publikují. Všechny události protokolu, které odpovídají omezením v syslogEventConfiguration, se publikují do každé uvedené jímky. Příklad: "EHforsyslog"
facilityName | Název zařízení syslog (například "LOG\_USER" nebo "LOG\_LOCAL0"). Úplný seznam najdete v části "zařízení" na [stránce zachycení služby SYSLOG](http://man7.org/linux/man-pages/man3/syslog.3.html) .
minSeverity | Úroveň závažnosti syslog (například LOG\_ERR nebo "LOG\_INFO"). Úplný seznam najdete v části "úroveň" [stránky zachycení služby SYSLOG](http://man7.org/linux/man-pages/man3/syslog.3.html) . Rozšíření zachytí události odesílané do zařízení na zadané úrovni nebo nad ní.

Když zadáte `syslogEvents`, LAD vždycky zapisuje data do tabulky ve službě Azure Storage. Můžete mít stejná data zapsaná do objektů BLOB JSON nebo Event Hubs, ale nemůžete zakázat ukládání dat do tabulky. Chování dělení této tabulky je stejné, jak je popsáno `performanceCounters`. Název tabulky je zřetězení těchto řetězců:

* `LinuxSyslog`
* Datum ve formátu "RRRRMMDD", které se mění každých 10 dní

Příklady zahrnují `LinuxSyslog20170410` a `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Tento volitelný oddíl řídí provádění libovolných dotazů [OMI](https://github.com/Microsoft/omi) .

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Prvek | Hodnota
------- | -----
Obor názvů | volitelné Obor názvů OMI, ve kterém má být dotaz proveden. Je-li tento parametr zadán, je použita výchozí hodnota "root/SCX", kterou implementuje [poskytovatelé služeb System Center pro různé platformy](https://github.com/Microsoft/SCXcore).
query | Dotaz OMI, který se má spustit.
table | volitelné Tabulka úložiště Azure v určeném účtu úložiště (viz [Nastavení chráněná](#protected-settings)).
frequency | volitelné Počet sekund mezi provedením dotazu. Výchozí hodnota je 300 (5 minut); minimální hodnota je 15 sekund.
jímky | volitelné Čárkami oddělený seznam názvů dalších umyvadel, na které by měly být publikovány nezpracované ukázkové výsledky metriky. Žádná agregace těchto nezpracovaných vzorků se počítá rozšířením nebo metrikami Azure.

Je třeba zadat buď Table, nebo "jímky", nebo obojí.

### <a name="filelogs"></a>fileLogs

Řídí zachycení souborů protokolu. LAD zachycuje nové textové řádky při zápisu do souboru a zapisuje je do řádků tabulky nebo do všech zadaných umyvadel (JsonBlob nebo EventHub).

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Prvek | Hodnota
------- | -----
file | Úplná cesta k souboru protokolu, který má být sledován a zachycen. Cesta musí pojmenovat jeden soubor. nemůže obsahovat název adresáře ani zástupné znaky.
table | volitelné Tabulka úložiště Azure v určeném účtu úložiště (jak je uvedeno v chráněných konfiguracích), do kterého se zapisují nové řádky z "koncového" souboru.
jímky | volitelné Čárkami oddělený seznam názvů dalších umyvadel, na které se odesílají řádky protokolu.

Je třeba zadat buď Table, nebo "jímky", nebo obojí.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metriky podporované zprostředkovatelem Builtin

Předdefinovaná zprostředkovatel metriky je zdrojem metrik, které jsou zajímavé pro širokou škálu uživatelů. Tyto metriky spadají do pěti širších tříd:

* Procesor
* Memory (Paměť)
* Síť
* systém souborů
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>předdefinované metriky pro třídu procesoru

Třída procesoru metrik nabízí informace o využití procesoru ve virtuálním počítači. Při agregaci procent je výsledkem průměr ve všech procesorech. V případě vCPU virtuálního počítače, pokud byl jeden vCPU 100% zaneprázdněný a druhý byl 100% nečinný, nahlášený PercentIdleTime by byl 50. Pokud by každý vCPU byl 50% zaneprázdněný pro stejné období, nahlášený výsledek by byl také 50. Ve vCPUm virtuálním počítači, který má zaneprázdněný vCPU 100% a jiné nečinné, nahlášený PercentIdleTime by byl 75.

counter | Význam
------- | -------
PercentIdleTime | Procento času během okna agregace, které procesory prováděly nečinný cyklus jádra
percentProcessorTime | Procento času spuštění vlákna, které není nečinné
PercentIOWaitTime | Procento času čekání na dokončení vstupně-výstupních operací
PercentInterruptTime | Procento času provádění hardwarových a softwarových přerušení a DPC (odložená volání procedur)
PercentUserTime | Doba nečinnosti v průběhu okna agregace, procento času stráveného uživatelem s normální prioritou
PercentNiceTime | Nečinný čas, procento strávené za sníženou prioritou (Nice)
PercentPrivilegedTime | Nečinný čas, procento strávené v privilegovaném režimu (kernel)

První čtyři čítače by měly být v součtu 100%. Poslední tři čítače jsou také celkem 100%; rozdělují součet hodnot PercentProcessorTime, PercentIOWaitTime a PercentInterruptTime.

Pro získání jedné metriky agregované napříč všemi procesory nastavte `"condition": "IsAggregate=TRUE"`. Pokud chcete získat metriku pro konkrétní procesor, například druhý logický procesor se čtyřmi vCPU virtuálními počítači, nastavte `"condition": "Name=\\"1\\""`. Čísla logických procesorů jsou v rozsahu `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>předdefinované metriky pro třídu paměti

Třída Memory metriky poskytuje informace o využití paměti, stránkování a prohození.

counter | Význam
------- | -------
AvailableMemory | Dostupná fyzická paměť v databázi MiB
PercentAvailableMemory | Dostupná fyzická paměť jako procento z celkové paměti
UsedMemory | Využití fyzické paměti (MiB)
PercentUsedMemory | Použít fyzickou paměť jako procento z celkové paměti
PagesPerSec | Celkový počet stránkování (čtení a zápis)
PagesReadPerSec | Stránky čtené ze záložního úložiště (odkládací soubor, soubor programu, mapovaný soubor atd.)
PagesWrittenPerSec | Stránky zapsané do záložního úložiště (odkládací soubor, mapovaný soubor atd.)
AvailableSwap | Nevyužité místo odkládacího souboru (MiB)
PercentAvailableSwap | Nevyužité místo odkládacího souboru jako procento z celkového zahození
UsedSwap | Odkládací místo (MiB) v aplikaci
PercentUsedSwap | Použít místo odkládacího souboru jako procento z celkového zahození

Tato třída metrik má pouze jednu instanci. Atribut Condition nemá žádná užitečná nastavení a měla by být vynechána.

### <a name="builtin-metrics-for-the-network-class"></a>předdefinované metriky pro třídu sítě

Třída Network metriky poskytuje informace o aktivitě sítě v jednotlivých síťových rozhraních od spuštění. LAD nevystavuje metriky šířky pásma, které se dají načíst z metrik hostitelů.

counter | Význam
------- | -------
BytesTransmitted | Celkový počet odeslaných bajtů od spuštění
BytesReceived | Celkový počet přijatých bajtů od spuštění
BytesTotal | Celkový počet odeslaných nebo přijatých bajtů od spuštění
PacketsTransmitted | Celkový počet odeslaných paketů od spuštění
PacketsReceived | Celkový počet přijatých paketů od spuštění
TotalRxErrors | Počet chyb přijetí od spuštění
TotalTxErrors | Počet chyb při odesílání od spuštění
TotalCollisions | Počet kolizí hlášených síťovými porty od spuštění

 I když je tato třída instance, LAD nepodporuje zachycení síťových metrik agregovaných napříč všemi síťovými zařízeními. Pokud chcete získat metriky pro určité rozhraní, například eth0, nastavte `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>předdefinované metriky pro třídu FileSystem

Třída FileSystem metrik poskytuje informace o využití systému souborů. Absolutní a procentuální hodnoty jsou hlášeny tak, jak by se zobrazily běžnému uživateli (ne root).

counter | Význam
------- | -------
FreeSpace | Volné místo na disku v bajtech
UsedSpace | Využité místo na disku v bajtech
PercentFreeSpace | Procento volného místa
PercentUsedSpace | Procento využitého místa
PercentFreeInodes | Procento nepoužívaných uzlů inode
PercentUsedInodes | Procentuální podíl přiděleného (používaného) uzlů inode ve všech systémech souborů
BytesReadPerSecond | Přečtené bajty za sekundu
BytesWrittenPerSecond | Zapsané bajty za sekundu
BytesPerSecond | Přečtené nebo zapsané bajty za sekundu
ReadsPerSecond | Operace čtení za sekundu
WritesPerSecond | Operace zápisu za sekundu
TransfersPerSecond | Operace čtení nebo zápisu za sekundu

Agregované hodnoty napříč všemi systémy souborů lze získat nastavením `"condition": "IsAggregate=True"`. Hodnoty pro konkrétní připojený systém souborů, jako je například "/mnt", lze získat nastavením `"condition": 'Name="/mnt"'`. 

**Poznámka**: Pokud místo JSON použijete portál Azure Portal, bude správným formulářem pole podmínka název = '/mnt '.

### <a name="builtin-metrics-for-the-disk-class"></a>předdefinované metriky pro třídu disku

Disková třída metrik nabízí informace o využití diskového zařízení. Tyto statistiky se vztahují na celou jednotku. V případě, že je v zařízení více systémů souborů, jsou čítače pro toto zařízení efektivně agregované napříč všemi.

counter | Význam
------- | -------
ReadsPerSecond | Operace čtení za sekundu
WritesPerSecond | Operace zápisu za sekundu
TransfersPerSecond | Celkem operací za sekundu
AverageReadTime | Průměrný počet sekund na operaci čtení
AverageWriteTime | Průměrný počet sekund na operaci zápisu
AverageTransferTime | Průměrný počet sekund na operaci
AverageDiskQueueLength | Průměrný počet operací disku zařazených do fronty
ReadBytesPerSecond | Počet přečtených bajtů za sekundu
WriteBytesPerSecond | Počet zapsaných bajtů za sekundu
BytesPerSecond | Počet přečtených nebo zapsaných bajtů za sekundu

Agregované hodnoty ve všech discích lze získat nastavením `"condition": "IsAggregate=True"`. Chcete-li získat informace pro konkrétní zařízení (například/dev/sdf1), nastavte `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Instalace a konfigurace LAD 3,0 přes rozhraní příkazového řádku

Za předpokladu, že vaše chráněná nastavení jsou v souboru PrivateConfig. JSON a informace o veřejné konfiguraci jsou v souboru PublicConfig. JSON, spusťte tento příkaz:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

V příkazu se předpokládá, že používáte Azure CLI v režimu správy prostředků Azure (ARM). Pokud chcete nakonfigurovat LAD pro virtuální počítače ASM (Classic Deployment model), přepněte do režimu ASM (`azure config mode asm`) a vynechejte název skupiny prostředků v příkazu. Další informace najdete v dokumentaci k rozhraní příkazového [řádku pro více platforem](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Příklad konfigurace LAD 3,0

V závislosti na předchozích definicích najdete ukázkovou konfiguraci rozšíření LAD 3,0 s některými vysvětleními. Pokud chcete tuto ukázku použít pro váš případ, měli byste použít vlastní název účtu úložiště, token SAS účtu a EventHubs tokeny SAS.

### <a name="privateconfigjson"></a>PrivateConfig.json

Konfigurace těchto privátních nastavení:

* Účet úložiště
* token SAS odpovídajícího účtu
* několik umyvadel (JsonBlob nebo EventHubs s tokeny SAS)

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="publicconfigjson"></a>PublicConfig.json

Tato veřejná nastavení způsobí, že LAD:

* Nahrajte metriku% času procesoru a využitého místa na disku do tabulky `WADMetrics*`.
* Nahrajte zprávy ze zařízení syslog "User" and závažnost "info" do tabulky `LinuxSyslog*`
* Nahrání nezpracovaných výsledků dotazu OMI (PercentProcessorTime a PercentIdleTime) do pojmenované tabulky `LinuxCPU`
* Nahrát připojené řádky do souboru `/var/log/myladtestlog` do tabulky `MyLadTestLog`

V každém případě se data nahrají taky do:

* Úložiště objektů BLOB v Azure (název kontejneru je definovaný v jímky JsonBlob)
* Koncový bod EventHubs (zadaný v jímky EventHubs)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

`resourceId` v konfiguraci se musí shodovat s konfigurací virtuálního počítače nebo sady škálování virtuálních počítačů.

* Grafy metrik a výstrahy na platformě Azure ví o resourceId virtuálního počítače, na kterém právě pracujete. Očekává, že se data pro virtuální počítač vyhledají pomocí vyhledávacího klíče resourceId.
* Pokud používáte automatické škálování Azure, musí resourceId v konfiguraci automatického škálování odpovídat ID resourceId, které používá LAD.
* ResourceId je integrováno do názvů JsonBlobs zapsaných pomocí LAD.

## <a name="view-your-data"></a>Zobrazení dat

Pomocí Azure Portal zobrazte data výkonu nebo nastavte výstrahy:

![image](./media/diagnostics-linux/graph_metrics.png)

Data `performanceCounters` jsou vždy uložena v Azure Storage tabulce. Rozhraní API pro Azure Storage jsou k dispozici pro mnoho jazyků a platforem.

Data odesílaná do jímky JsonBlob se ukládají v objektech blob v účtu úložiště s názvem v části [chráněná nastavení](#protected-settings). Data objektů blob můžete využívat pomocí libovolných rozhraní API Azure Blob Storage.

Kromě toho můžete použít tyto nástroje uživatelského rozhraní pro přístup k datům v Azure Storage:

* Průzkumník serveru sady Visual Studio.
* [Průzkumníka služby Microsoft Azure Storage](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Tento snímek relace Průzkumník služby Microsoft Azure Storage zobrazuje vygenerované Azure Storage tabulky a kontejnery ze správně nakonfigurovaného rozšíření LAD 3,0 na testovacím virtuálním počítači. Obrázek se přesně neshoduje s [ukázkovou konfigurací LAD 3,0](#an-example-lad-30-configuration).

![image](./media/diagnostics-linux/stg_explorer.png)

V příslušné [dokumentaci k EventHubs](../../event-hubs/event-hubs-what-is-event-hubs.md) se dozvíte, jak využívat zprávy publikované do koncového bodu EventHubs.

## <a name="next-steps"></a>Další kroky

* Vytvořte výstrahy metriky v [Azure monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) pro metriky, které shromažďujete.
* Vytvořte [grafy monitorování](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) pro vaše metriky.
* Naučte se [vytvořit sadu škálování virtuálního počítače](../linux/tutorial-create-vmss.md) pomocí vašich metrik k řízení automatického škálování.
