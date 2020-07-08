---
title: Postup vytváření zásad konfigurace hosta pro Windows
description: Naučte se vytvářet Azure Policy zásady konfigurace hostů pro Windows.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: b53c8ec8189516305de8b0b8c05b2be8ea49f7f2
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045123"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Postup vytváření zásad konfigurace hosta pro Windows

Než začnete vytvářet vlastní definice zásad, je vhodné si přečíst informace o koncepčním přehledu na stránce [Azure Policy konfiguraci hostů](../concepts/guest-configuration.md).
 
Další informace o vytváření zásad konfigurace hostů pro Linux najdete na stránce [Postup vytvoření zásad konfigurace hostů pro Linux](./guest-configuration-create-linux.md) .

Při auditování používá konfigurace hosta k vytvoření konfiguračního souboru modul prostředku [Konfigurace požadovaného stavu](/powershell/scripting/dsc/overview/overview) (DSC). Konfigurace DSC definuje stav, ve kterém má být počítač.
Pokud se konfigurace nezdařila, je aktivován efekt zásad **auditIfNotExists** a počítač se považuje za **nevyhovující**.

[Konfiguraci hosta Azure Policy](../concepts/guest-configuration.md) můžete použít jenom k auditování nastavení v počítačích. Náprava nastavení v počítačích ještě není k dispozici.

Pomocí následujících akcí můžete vytvořit vlastní konfiguraci pro ověření stavu počítače s Azure nebo mimo Azure.

> [!IMPORTANT]
> Vlastní zásady s konfigurací hosta jsou funkcí verze Preview.
>
> K provádění auditů na virtuálních počítačích Azure se vyžaduje rozšíření konfigurace hosta.
> Pokud chcete nasadit rozšíření v celém počítači s Windows, přiřaďte následující definice zásad:
>   - [Nasaďte požadavky pro povolení zásad konfigurace hostů na virtuálních počítačích s Windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

## <a name="install-the-powershell-module"></a>Instalace modulu PowerShellu

Modul konfigurace hosta automatizuje proces vytváření vlastního obsahu, včetně:

- Vytváření artefaktu obsahu konfigurace hosta (. zip)
- Automatizované testování artefaktu
- Vytvoření definice zásady
- Publikování zásady

Modul lze nainstalovat do počítače se systémem Windows, macOS nebo Linux s prostředím PowerShell 6,2 nebo novějším spuštěným místně nebo pomocí [Azure Cloud Shell](https://shell.azure.com)nebo s [imagí Azure PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Kompilace konfigurací zatím není v systému Linux podporována.

### <a name="base-requirements"></a>Základní požadavky

Operační systémy, ve kterých se modul dá nainstalovat:

- Linux
- macOS
- Windows

Modul prostředků konfigurace hosta vyžaduje následující software:

- PowerShell 6,2 nebo novější. Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 nebo vyšší. Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/powershell/azure/install-az-ps).
  - Vyžadují se jenom moduly AZ. Accounts a AZ. Resources.

### <a name="install-the-module"></a>Nainstalovat modul

Postup instalace modulu **GuestConfiguration** do PowerShellu:

1. Z příkazového řádku PowerShellu spusťte následující příkaz:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Ověřte, že se modul importoval:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Artefakty konfigurace hosta a zásady pro Windows

Konfigurace hosta používá konfiguraci požadovaného stavu PowerShellu jako abstrakci jazyka pro psaní, co auditovat v systému Windows. Agent načte samostatnou instanci PowerShellu 6,2, takže nedochází ke konfliktu s využitím prostředí PowerShell DSC v prostředí Windows PowerShell 5,1 a neexistuje žádný požadavek na předběžnou instalaci PowerShellu 6,2 nebo novější.

Přehled konceptů a terminologie DSC najdete v tématu [Přehled prostředí POWERSHELL DSC](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Způsob, jakým se v modulech konfigurace hosta liší od modulů Windows PowerShell DSC

Když Audituje konfigurace hosta počítač, sekvence událostí se liší od rozhraní Windows PowerShell DSC.

1. Agent se nejdřív spustí `Test-TargetResource` , aby zjistil, jestli je konfigurace ve správném stavu.
1. Logická hodnota vrácená funkcí určuje, zda má být stav Azure Resource Manager pro přiřazení hostů kompatibilní/nekompatibilní.
1. Zprostředkovatel spustí, `Get-TargetResource` aby vrátil aktuální stav každého nastavení, takže podrobnosti jsou k dispozici jak k tomu, proč počítač nedodržuje předpisy, a ověřil, zda je aktuální stav kompatibilní.

Parametry v Azure Policy, které předávají hodnoty přiřazení konfigurace hosta musí být typu _řetězec_ .
Není možné předat pole pomocí parametrů, i když prostředek DSC podporuje pole.

### <a name="get-targetresource-requirements"></a>Požadavky GET-TargetResource

Funkce `Get-TargetResource` má zvláštní požadavky na konfiguraci hosta, která není potřebná pro konfiguraci požadovaného stavu Windows.

- Vrácená zatřiďovací tabulka musí zahrnovat vlastnost s názvem **důvody**.
- Vlastnost důvody musí být pole.
- Každá položka v poli musí být zatřiďovací tabulka s klíči s názvem **Code** a **frází**.

Vlastnost důvody používá služba ke standardizaci způsobu, jakým jsou informace zobrazeny, když je počítač nekompatibilní. Jednotlivé položky si můžete představit z důvodů, proč prostředek není kompatibilní. Vlastnost je pole, protože prostředek může být nekompatibilní s více než jedním důvodem.

Služba očekává **kód** a **frázi** vlastností. Při vytváření vlastního prostředku nastavte text (obvykle STDOUT), který chcete zobrazit jako důvod, proč prostředek není kompatibilní jako hodnota **fráze**. **Kód** má specifické požadavky na formátování, takže hlášení může jasně zobrazit informace o prostředku, který se používá k provedení auditu. Toto řešení zajišťuje rozšiřitelnou konfiguraci hostů. Libovolný příkaz lze spustit, dokud bude výstup vrácen jako řetězcová hodnota pro vlastnost **fráze** .

- **Code** (String): název prostředku, opakuje a pak krátký název bez mezer jako identifikátor z důvodu. Tyto tři hodnoty by měly být odděleny dvojtečkami bez mezer.
  - Příkladem může být`registry:registry:keynotpresent`
- **Fráze** (String): text čitelný lidmi pro vysvětlení, proč nastavení není vyhovující.
  - Příkladem může být`The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

Vlastnost důvody musí být také přidána do souboru MOF schématu pro prostředek jako vložená třída.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>Požadavky na konfiguraci

Název vlastní konfigurace musí být konzistentní všude. Název souboru. zip pro balíček obsahu, název konfigurace v souboru MOF a název přiřazení hosta v šabloně Azure Resource Manager (šablona ARM) musí být stejné.

### <a name="scaffolding-a-guest-configuration-project"></a>Generování uživatelského rozhraní projektu konfigurace hosta

Vývojáři, kteří chtějí urychlit proces Začínáme a práci z ukázkového kódu, mohou nainstalovat projekt komunity s názvem **projekt konfigurace hosta**. Projekt nainstaluje šablonu pro modul PowerShellu pro [sádru](https://github.com/powershell/plaster) . Tento nástroj lze použít k vytvoření uživatelského rozhraní projektu, včetně pracovní konfigurace a ukázkového prostředku, a sady testů [platformy pester](https://github.com/pester/pester) pro ověření projektu. Šablona obsahuje také Spouštěče úloh pro Visual Studio Code pro automatizaci vytváření a ověřování konfiguračního balíčku hosta. Další informace najdete v [projektu konfigurace hosta](https://github.com/microsoft/guestconfigurationproject)projektu GitHubu.

Další informace o práci s konfiguracemi obecně naleznete v tématu [Write, Compile a Apply Configuration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Očekával se obsah artefaktu konfigurace hosta.

Dokončený balíček používá konfigurace hosta k vytvoření Azure Policych definic. Balíček se skládá z těchto součástí:

- Kompilovaná konfigurace DSC jako MOF
- Složka modulů
  - Modul GuestConfiguration
  - Modul DscNativeResources
  - Systému Moduly prostředků DSC vyžadované MOF

Rutiny prostředí PowerShell pomáhají při vytváření balíčku.
Není požadována žádná složka kořenové úrovně ani složka verze.
Formát balíčku musí být soubor. zip.

### <a name="storing-guest-configuration-artifacts"></a>Ukládání artefaktů konfigurace hosta

Balíček. zip musí být uložený v umístění, ke kterému mají přístup spravované virtuální počítače.
Mezi příklady patří úložiště GitHub, úložiště Azure nebo Azure Storage. Pokud nechcete, aby balíček byl veřejný, můžete do adresy URL přidat [token SAS](../../../storage/common/storage-sas-overview.md) .
Můžete také implementovat [koncový bod služby](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) pro počítače v privátní síti, i když tato konfigurace platí pouze pro přístup k balíčku a nekomunikuje se službou.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Krok za krokem – vytvoření vlastní zásady auditu konfigurace hosta pro Windows

Vytvořte konfiguraci DSC pro auditování nastavení. Následující příklad skriptu PowerShellu vytvoří konfiguraci s názvem **AuditBitLocker**, importuje modul prostředků **PsDscResources** a použije `Service` prostředek k auditování spuštěné služby. Konfigurační skript se dá spustit z počítače s Windows nebo macOS.

```powershell
# Add PSDscResources module to environment
Install-Module 'PSDscResources'

# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker ./Config
```

Uložte tento soubor s názvem `config.ps1` do složky projektu. Spusťte ji v PowerShellu tak, že ji spustíte `./config.ps1` v terminálu. Vytvoří se nový soubor MOF.

`Node AuditBitlocker`Příkaz není technicky vyžadován, ale vytváří soubor s názvem `AuditBitlocker.mof` , nikoli jako výchozí `localhost.mof` . Pokud má název souboru. mof postupovat podle konfigurace, usnadňuje uspořádání mnoha souborů při škálování.

Jakmile je soubor MOF zkompilován, podpůrné soubory musí být zabaleny dohromady. Dokončený balíček používá konfigurace hosta k vytvoření Azure Policych definic.

`New-GuestConfigurationPackage`Rutina vytvoří balíček. Moduly, které jsou potřebné pro konfiguraci, musí být k dispozici v `$Env:PSModulePath` . Parametry `New-GuestConfigurationPackage` rutiny při vytváření obsahu Windows:

- **Název**: název konfiguračního balíčku hosta.
- **Konfigurace**: kompilovaná úplná cesta dokumentu konfigurace DSC.
- **Cesta**: cesta ke výstupní složce. Tento parametr je volitelný. Pokud není zadaný, balíček se vytvoří v aktuálním adresáři.

Spuštěním následujícího příkazu vytvořte balíček pomocí konfigurace uvedené v předchozím kroku:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Po vytvoření konfiguračního balíčku, ale před jeho publikováním do Azure, můžete balíček otestovat z prostředí pracovní stanice nebo CI/CD. Rutina GuestConfiguration `Test-GuestConfigurationPackage` zahrnuje stejného agenta ve vývojovém prostředí, které se používá v počítačích Azure. Pomocí tohoto řešení můžete provádět testování integrací místně před vydáním do fakturovaných cloudových prostředí.

Vzhledem k tomu, že agent ve skutečnosti vyhodnocuje místní prostředí, ve většině případů je třeba spustit rutinu test-rutiny na stejné platformě operačního systému, jakou máte v plánu auditovat. Test používá pouze moduly, které jsou součástí balíčku obsahu.

Parametry `Test-GuestConfigurationPackage` rutiny:

- **Název**: název zásad konfigurace hostů.
- **Parametr**: parametry zásad, které jsou k dispozici ve formátu zatřiďovací tabulky.
- **Cesta**: úplná cesta k balíčku pro konfiguraci hosta.

Spusťte následující příkaz, který otestuje balíček vytvořený předchozím krokem:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Rutina podporuje také vstup z kanálu PowerShellu. Přesměrování výstupu `New-GuestConfigurationPackage` rutiny do `Test-GuestConfigurationPackage` rutiny.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

Dalším krokem je publikování souboru do úložiště objektů BLOB. Následující skript obsahuje funkci, kterou můžete použít k automatizaci této úlohy. Příkazy používané ve `publish` funkci vyžadují `Az.Storage` modul.

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

Po vytvoření a nahrání balíčku vlastní zásady konfigurace hosta se vytvoří definice zásady konfigurace hosta. `New-GuestConfigurationPolicy`Rutina převezme vlastní balíček zásad a vytvoří definici zásady.

Parametry `New-GuestConfigurationPolicy` rutiny:

- **ContentUri**: veřejné http (s) URI balíčku obsahu konfigurace hosta.
- **DisplayName**: zobrazovaný název zásad.
- **Popis**: popis zásady.
- **Parametr**: parametry zásad, které jsou k dispozici ve formátu zatřiďovací tabulky.
- **Verze**: verze zásad
- **Cesta**: cílová cesta, kde jsou vytvořeny definice zásad.
- **Platforma**: cílová platforma (Windows/Linux) pro zásady konfigurace hosta a balíček obsahu.

Následující příklad vytvoří definice zásad v zadané cestě z vlastního balíčku zásad:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

Následující soubory vytvořil `New-GuestConfigurationPolicy` :

- **auditIfNotExists.jsna**
- **deployIfNotExists.jsna**
- **Initiative.jsna**

Výstup rutiny vrátí objekt, který obsahuje zobrazovaný název iniciativy a cestu k souborům zásad.

> [!Note]
> Nejnovější modul konfigurace hosta obsahuje nové parametry:
> - **Tag** přidá do definice zásady jeden nebo více filtrů značek.
>   - Viz část [filtrování zásad konfigurace hostů pomocí značek](#filtering-guest-configuration-policies-using-tags).
> - **Kategorie** nastaví pole metadata kategorie v definici zásady.
>   - Pokud parametr není zahrnutý, kategorie se nastaví jako výchozí konfigurace hostů.
> Tyto funkce jsou ve verzi Preview a vyžadují modul konfigurace hosta verze 1.20.1, který se dá nainstalovat pomocí nástroje `Install-Module GuestConfiguration -AllowPrerelease` .

Nakonec publikujte definice zásad pomocí `Publish-GuestConfigurationPolicy` rutiny. Rutina má pouze parametr **path** , který odkazuje na umístění souborů JSON, které vytvořil `New-GuestConfigurationPolicy` .

K provedení příkazu Publikovat budete potřebovat přístup k vytváření zásad v Azure. Konkrétní autorizační požadavky jsou zdokumentovány na stránce [přehled Azure Policy](../overview.md) . Nejlepší integrovanou rolí je **Přispěvatel zásad prostředků**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

`Publish-GuestConfigurationPolicy`Rutina akceptuje cestu z kanálu PowerShellu. Tato funkce znamená, že můžete vytvořit soubory zásad a publikovat je v jedné sadě příkazů s použitím kanálu.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

V rámci zásad vytvořených v Azure je posledním krokem přiřazení iniciativy. Podívejte se, jak přiřadit iniciativu k [portálu](../assign-policy-portal.md), rozhraní příkazového [řádku Azure](../assign-policy-azurecli.md)a [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Zásady konfigurace hosta se musí **vždy** přiřadit pomocí iniciativy, která kombinuje zásady _AuditIfNotExists_ a _DeployIfNotExists_ . Pokud je přiřazena pouze zásada _AuditIfNotExists_ , požadavky nejsou nasazeny a zásady vždy ukazují, že jsou servery "0" kompatibilní.

Přiřazení definice zásady s _DeployIfNotExists_ účinkem vyžaduje další úroveň přístupu. Chcete-li udělit nejnižší oprávnění, můžete vytvořit vlastní definici role, která rozšiřuje **přispěvatele zásad prostředků**. V následujícím příkladu se vytvoří role s názvem **Přispěvatel zásad prostředků Rewards** s dodatečným oprávněním _Microsoft. Authorization/roleAssignments/Write_.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrování zásad konfigurace hostů pomocí značek

> [!Note]
> Tato funkce je ve verzi Preview a vyžaduje modul konfigurace hosta verze 1.20.1, který se dá nainstalovat pomocí nástroje `Install-Module GuestConfiguration -AllowPrerelease` .

Definice zásad vytvořené rutinami v modulu Konfigurace hosta můžou volitelně zahrnovat filtr pro značky. Parametr **značky** pro `New-GuestConfigurationPolicy` podporuje pole zatřiďovacími tabulkami obsahující jednotlivá označení celého značky. Značky se přidají do `If` oddílu definice zásady a nedají se upravit přiřazením zásad.

Příklad fragmentu definice zásady, která filtruje značky, je uveden níže.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content
    }
  ]
}
```

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Použití parametrů ve vlastních definicích zásad konfigurace hostů

Konfigurace hosta podporuje přepsání vlastností konfigurace v době běhu. Tato funkce znamená, že hodnoty v souboru MOF v balíčku není nutné považovat za statické. Hodnoty přepsání jsou poskytovány prostřednictvím Azure Policy a neovlivňují způsob, jakým jsou vytvořeny nebo kompilovány konfigurace.

Rutiny `New-GuestConfigurationPolicy` a `Test-GuestConfigurationPolicyPackage` zahrnují parametr pojmenovaný **parametr**. Tento parametr převezme definici zatřiďovací tabulky včetně všech podrobností o jednotlivých parametrech a vytvoří požadované oddíly každého souboru používaného pro definici Azure Policy.

Následující příklad vytvoří definici zásady pro audit služby, kde uživatel vybere ze seznamu v okamžiku přiřazení zásady.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>Rozšíření konfigurace hostů pomocí nástrojů třetích stran

> [!Note]
> Tato funkce je ve verzi Preview a vyžaduje modul konfigurace hosta verze 1.20.3, který se dá nainstalovat pomocí nástroje `Install-Module GuestConfiguration -AllowPrerelease` .
> Ve verzi 1.20.3 je tato funkce dostupná jenom pro definice zásad, které auditují počítače s Windows.

Balíčky artefaktů pro konfiguraci hosta se dají rozšířit tak, aby zahrnovaly nástroje třetích stran.
Rozšíření konfigurace hosta vyžaduje vývoj dvou součástí.

- Prostředek konfigurace požadovaného stavu, který zpracovává všechny aktivity související se správou nástroje třetí strany.
  - Instalace
  - Invoke
  - Převést výstup
- Obsah ve správném formátu, který nástroj nativně spotřebovává

Pokud řešení komunity ještě neexistuje, vyžaduje prostředek DSC vlastní vývoj.
Řešení komunity je možné zjistit vyhledáním Galerie prostředí PowerShell pro tag [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22).

> [!Note]
> Rozšiřitelnost konfigurace hosta je scénář "Přineste si vlastní licenci". Před použitím se ujistěte, že jste splnili podmínky a ujednání všech nástrojů třetích stran.

Po instalaci prostředku DSC do vývojového prostředí použijte parametr **FilesToInclude** pro `New-GuestConfigurationPackage` zahrnutí obsahu pro platformu třetí strany v artefaktu obsahu.

### <a name="step-by-step-creating-a-content-artifact-that-uses-third-party-tools"></a>Krok za krokem, vytvoření artefaktu obsahu, který používá nástroje třetích stran

Pouze `New-GuestConfigurationPackage` rutina vyžaduje změnu z podrobných pokynů pro artefakty obsahu DSC. V tomto příkladu použijte `gcInSpec` modul k prodloužení konfigurace hosta pro audit počítačů s Windows pomocí INSPEC Platform a nikoli integrovaného modulu používaného v systému Linux. Modul komunity je udržován jako [Open source projekt v GitHubu](https://github.com/microsoft/gcinspec).

Nainstalujte požadované moduly do vývojového prostředí:

```azurepowershell-interactive
# Update PowerShellGet if needed to allow installing PreRelease versions of modules
Install-Module PowerShellGet -Force

# Install GuestConfiguration module prerelease version
Install-Module GuestConfiguration -allowprerelease

# Install commmunity supported gcInSpec module
Install-Module gcInSpec
```

Nejprve vytvořte soubor YaML používaný nespecifikací. Soubor poskytuje základní informace o prostředí. Příklad je uveden níže:

```YaML
name: wmi_service
title: Verify WMI service is running
maintainer: Microsoft Corporation
summary: Validates that the Windows Service 'winmgmt' is running
copyright: Microsoft Corporation
license: MIT
version: 1.0.0
supports:
  - os-family: windows
```

Uložte tento soubor s názvem `wmi_service.yml` do složky s názvem `wmi_service` v adresáři projektu.

Pak vytvořte soubor Ruby s abstrakcí jazyka INSPEC, který se používá k auditování počítače.

```Ruby
control 'wmi_service' do
  impact 1.0
  title 'Verify windows service: winmgmt'
  desc 'Validates that the service, is installed, enabled, and running'

  describe service('winmgmt') do
    it { should be_installed }
    it { should be_enabled }
    it { should be_running }
  end
end

```

Uložte tento soubor `wmi_service.rb` do nové složky s názvem v `controls` `wmi_service` adresáři.

Nakonec vytvořte konfiguraci, importujte modul prostředků **GuestConfiguration** a pomocí `gcInSpec` prostředku nastavte název INSPEC Profile.

```powershell
# Define the configuration and import GuestConfiguration
Configuration wmi_service
{
    Import-DSCResource -Module @{ModuleName = 'gcInSpec'; ModuleVersion = '2.1.0'}
    node 'wmi_service'
    {
        gcInSpec wmi_service
        {
            InSpecProfileName       = 'wmi_service'
            InSpecVersion           = '3.9.3'
            WindowsServerVersion    = '2016'
        }
    }
}

# Compile the configuration to create the MOF files
wmi_service -out ./Config
```

Nyní byste měli mít strukturu projektu, jak je uvedeno níže:

```file
/ wmi_service
    / Config
        wmi_service.mof
    / wmi_service
        wmi_service.yml
        / controls
            wmi_service.rb 
```

Podpůrné soubory musí být zabaleny dohromady. Dokončený balíček používá konfigurace hosta k vytvoření Azure Policych definic.

`New-GuestConfigurationPackage`Rutina vytvoří balíček. Pro obsah třetích stran přidejte do balíčku obsah INSPEC pomocí parametru **FilesToInclude** . Nemusíte zadávat **ChefProfilePath** jako balíčky pro Linux.

- **Název**: název konfiguračního balíčku hosta.
- **Konfigurace**: úplná cesta k kompilované konfiguraci dokumentu.
- **Cesta**: cesta ke výstupní složce. Tento parametr je volitelný. Pokud není zadaný, balíček se vytvoří v aktuálním adresáři.
- **FilesoInclude**: úplná cesta k profilu INSPEC.

Spuštěním následujícího příkazu vytvořte balíček pomocí konfigurace uvedené v předchozím kroku:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'wmi_service' `
  -Configuration './Config/wmi_service.mof' `
  -FilesToInclude './wmi_service'  `
  -Path './package' 
```

## <a name="policy-lifecycle"></a>Životní cyklus zásad

Pokud chcete vydat aktualizaci zásady, existují dvě pole, která vyžadují pozornost.

- **Verze**: když spustíte `New-GuestConfigurationPolicy` rutinu, musíte zadat číslo verze, které je větší než aktuálně publikované. Vlastnost aktualizuje verzi přiřazení konfigurace hosta, aby agent rozpoznal aktualizovaný balíček.
- **contentHash**: Tato vlastnost je automaticky aktualizována `New-GuestConfigurationPolicy` rutinou. Jedná se o hodnotu hash balíčku, kterou vytvořil `New-GuestConfigurationPackage` . Vlastnost musí být správná pro `.zip` soubor, který publikujete. Pokud se aktualizuje jenom vlastnost **contentUri** , rozšíření nepřijme balíček obsahu.

Nejjednodušším způsobem, jak vydat aktualizovaný balíček, je opakovat postup popsaný v tomto článku a zadat aktualizované číslo verze. Tento proces zaručuje, že všechny vlastnosti jsou správně aktualizované.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Převod obsahu Windows Zásady skupiny na Azure Policy konfiguraci hosta

Konfigurace hosta, při auditování počítačů s Windows, je implementovaná syntaxe konfigurace požadovaného stavu prostředí PowerShell. Komunita DSC zveřejnila nástroje pro převod exportovaných šablon Zásady skupiny do formátu DSC. Pomocí tohoto nástroje spolu s rutinami konfigurace hosta, které jsou popsané výše, můžete převést Windows Zásady skupiny obsah a balíček/publikovat pro Azure Policy k auditování. Podrobnosti o používání tohoto nástroje najdete v článku [rychlý Start: převod zásady skupiny do DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
Po převodu obsahu můžete pomocí kroků uvedených v tomto postupu vytvořit balíček a publikovat ho jako Azure Policy jsou stejné jako u jakéhokoli obsahu DSC.

## <a name="optional-signing-guest-configuration-packages"></a>Volitelné: podepisování balíčků konfigurace hosta

Vlastní zásady konfigurace hosta používají SHA256 hash k ověření, že se balíček zásad nezměnil.
V případě potřeby mohou zákazníci také použít certifikát k podepisování balíčků a vynucení rozšíření konfigurace hosta pouze k povolení podepsaného obsahu.

Chcete-li povolit tento scénář, je třeba provést dva kroky. Spusťte rutinu pro podepsání balíčku obsahu a přidejte značku do počítačů, které by měly vyžadovat podepsání kódu.

Chcete-li použít funkci ověřování podpisem, spusťte `Protect-GuestConfigurationPackage` rutinu pro podepsání balíčku před jeho publikováním. Tato rutina vyžaduje certifikát pro podepsání kódu.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametry `Protect-GuestConfigurationPackage` rutiny:

- **Cesta**: úplná cesta k balíčku pro konfiguraci hosta.
- **Certifikát**: certifikát pro podepsání kódu pro podepsání balíčku. Tento parametr je podporován pouze při podepisování obsahu pro systém Windows.

Agent GuestConfiguration očekává, že se veřejný klíč certifikátu nachází v počítačích se systémem Windows v části Důvěryhodné kořenové certifikační autority a v cestě `/usr/local/share/ca-certificates/extra` k počítačům se systémem Linux. Aby mohl uzel ověřit podepsaný obsah, nainstalujte na počítači veřejný klíč certifikátu a pak použijte vlastní zásady. Tento proces se dá provést pomocí jakékoli techniky v rámci virtuálního počítače nebo pomocí Azure Policy. [Tady je uvedena](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)Ukázková šablona.
Zásady přístupu Key Vault musí umožňovat poskytovateli výpočetních prostředků přístup k certifikátům během nasazení. Podrobný postup najdete v tématu [nastavení Key Vault pro virtuální počítače v Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Následuje příklad exportu veřejného klíče z podpisového certifikátu pro import do počítače.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Po publikování obsahu přidejte značku s názvem `GuestConfigPolicyCertificateValidation` a hodnotou `enabled` do všech virtuálních počítačů, kde by mělo být požadováno podepisování kódu. Podívejte se na [ukázky značek](../samples/built-in-policies.md#tags) , jak mohou být značky doručovány ve velkém rozsahu pomocí Azure Policy. Jakmile je tato značka nastavená, definice zásady vytvořená pomocí `New-GuestConfigurationPolicy` rutiny povolí požadavek prostřednictvím rozšíření konfigurace hosta.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Řešení potíží s přiřazením zásad konfigurace hosta (Preview)

Nástroj je k dispozici ve verzi Preview, který vám pomůže při řešení potíží s Azure Policy přiřazení konfigurace hostů. Nástroj je ve verzi Preview a byl publikován do Galerie prostředí PowerShell jako název modulu [Poradce při potížích s konfigurací hosta](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Další informace o rutinách v tomto nástroji získáte pomocí příkazu Get-Help v prostředí PowerShell k zobrazení integrovaných pokynů. Jak nástroj načítá časté aktualizace, což je nejlepší způsob, jak získat nejnovější informace.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o auditování virtuálních počítačů pomocí [Konfigurace hostů](../concepts/guest-configuration.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](get-compliance-data.md).
