---
title: Postup vytvoření zásad konfigurace hostů pro Linux
description: Naučte se vytvářet Azure Policy zásady konfigurace hostů pro Linux.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 219b38bd81cae8d16241d1ee16cfdd2f400ae91e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024978"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Postup vytvoření zásad konfigurace hostů pro Linux

Než začnete vytvářet vlastní zásady, přečtěte si informace v přehledu [Azure Policy konfiguraci hostů](../concepts/guest-configuration.md).
 
Další informace o vytváření zásad konfigurace hostů pro Windows najdete na stránce [Postup vytvoření zásad konfigurace hostů pro Windows](./guest-configuration-create.md) .

Při auditování systému [Linux používá konfigurace](https://www.inspec.io/)hosta nespecifikované. Profil INSPEC definuje podmínku, ve které by měl být počítač. Pokud se konfigurace nezdařila, je aktivován efekt zásad **auditIfNotExists** a počítač se považuje za **nevyhovující**.

[Konfiguraci hosta Azure Policy](../concepts/guest-configuration.md) můžete použít jenom k auditování nastavení v počítačích. Náprava nastavení v počítačích ještě není k dispozici.

Pomocí následujících akcí můžete vytvořit vlastní konfiguraci pro ověření stavu počítače s Azure nebo mimo Azure.

> [!IMPORTANT]
> Vlastní zásady s konfigurací hosta jsou funkcí verze Preview.
>
> K provádění auditů na virtuálních počítačích Azure se vyžaduje rozšíření konfigurace hosta.
> Pokud chcete nasadit rozšíření v rámci všech počítačů se systémem Linux, přiřaďte následující definici zásady:
>   - [Nasaďte požadavky pro povolení zásad konfigurace hostů na virtuálních počítačích se systémem Linux.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

## <a name="install-the-powershell-module"></a>Instalace modulu PowerShellu

Vytvořením artefaktu konfigurace hosta, automatizovaného testování artefaktu, vytvořením definice zásady a publikováním této zásady je výhradně automatizováno pomocí modulu Konfigurace hosta v prostředí PowerShell. Modul lze nainstalovat do počítače se systémem Windows, macOS nebo Linux s prostředím PowerShell 6,2 nebo novějším spuštěným místně nebo pomocí [Azure Cloud Shell](https://shell.azure.com)nebo s [imagí Azure PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> V systému Linux není kompilace konfigurací podporována.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Artefakty konfigurace hosta a zásady pro Linux

I v prostředích systému Linux používá konfigurace hosta konfiguraci požadovaného stavu jako abstrakci jazyka. Implementace je založena na nativním kódu (C++), takže nevyžaduje načítání PowerShellu. Nicméně vyžaduje konfiguraci MOF popisující podrobnosti o prostředí. DSC slouží jako obálka pro nespecifikované informace pro standardizaci způsobu jejich spuštění, způsobu zadání parametrů a způsobu, jakým se výstup vrátí službě. Při práci s vlastním netechnickým obsahem se vyžaduje jenom krátké znalosti DSC.

#### <a name="configuration-requirements"></a>Požadavky na konfiguraci

Název vlastní konfigurace musí být konzistentní všude. Název souboru. zip pro balíček obsahu, název konfigurace v souboru MOF a název přiřazení hosta v šabloně Správce prostředků musí být stejný.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Konfigurace vlastní konfigurace hosta v systému Linux

Konfigurace hosta v systému Linux `ChefInSpecResource` používá prostředek k poskytnutí modulu s názvem [INSPEC Profile](https://www.inspec.io/docs/reference/profiles/). **Název** je jediná požadovaná vlastnost prostředku. Vytvořte soubor YaML a soubor skriptu Ruby, jak je popsáno níže.

Nejprve vytvořte soubor YaML používaný nespecifikací. Soubor poskytuje základní informace o prostředí. Příklad je uveden níže:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Uložte tento soubor s názvem `inspec.yml` do složky s názvem `linux-path` v adresáři projektu.

Pak vytvořte soubor Ruby s abstrakcí jazyka INSPEC, který se používá k auditování počítače.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Uložte tento soubor s názvem `linux-path.rb` do nové složky s názvem `controls` v `linux-path` adresáři.

Nakonec vytvořte konfiguraci, importujte modul prostředků **PSDesiredStateConfiguration** a zkompilujte konfiguraci.

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
import-module PSDesiredStateConfiguration
AuditFilePathExists -out ./Config
```

Uložte tento soubor s názvem `config.ps1` do složky projektu. Spusťte ji v PowerShellu tak `./config.ps1` , že ji spustíte v terminálu. Vytvoří se nový soubor MOF.

`Node AuditFilePathExists` Příkaz není technicky vyžadován, ale vytváří soubor s názvem `AuditFilePathExists.mof` , `localhost.mof`nikoli jako výchozí. Pokud má název souboru. mof postupovat podle konfigurace, usnadňuje uspořádání mnoha souborů při škálování.



Nyní byste měli mít strukturu projektu, jak je uvedeno níže:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

Podpůrné soubory musí být zabaleny dohromady. Dokončený balíček používá konfigurace hosta k vytvoření Azure Policych definic.

`New-GuestConfigurationPackage` Rutina vytvoří balíček. Parametry `New-GuestConfigurationPackage` rutiny při vytváření obsahu pro Linux:

- **Název**: název konfiguračního balíčku hosta.
- **Konfigurace**: úplná cesta k kompilované konfiguraci dokumentu.
- **Cesta**: cesta ke výstupní složce. Tento parametr je volitelný. Pokud není zadaný, balíček se vytvoří v aktuálním adresáři.
- **ChefProfilePath**: úplná cesta k profilu INSPEC. Tento parametr je podporován pouze při vytváření obsahu pro audit systému Linux.

Spuštěním následujícího příkazu vytvořte balíček pomocí konfigurace uvedené v předchozím kroku:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

Po vytvoření konfiguračního balíčku, ale před jeho publikováním do Azure, můžete balíček otestovat z prostředí pracovní stanice nebo CI/CD. Rutina `Test-GuestConfigurationPackage` GuestConfiguration zahrnuje stejného agenta ve vývojovém prostředí, které se používá v počítačích Azure. Pomocí tohoto řešení můžete provádět testování integrací místně před vydáním do fakturovaných cloudových prostředí.

Vzhledem k tomu, že agent ve skutečnosti vyhodnocuje místní prostředí, ve většině případů je třeba spustit rutinu test-rutiny na stejné platformě operačního systému, jakou máte v plánu auditovat.

Parametry `Test-GuestConfigurationPackage` rutiny:

- **Název**: název zásad konfigurace hostů.
- **Parametr**: parametry zásad, které jsou k dispozici ve formátu zatřiďovací tabulky.
- **Cesta**: úplná cesta k balíčku pro konfiguraci hosta.

Spusťte následující příkaz, který otestuje balíček vytvořený předchozím krokem:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

Rutina podporuje také vstup z kanálu PowerShellu. Přesměrování výstupu `New-GuestConfigurationPackage` rutiny do `Test-GuestConfigurationPackage` rutiny.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
```
Po vytvoření a nahrání balíčku vlastní zásady konfigurace hosta se vytvoří definice zásady konfigurace hosta. `New-GuestConfigurationPolicy` Rutina převezme vlastní balíček zásad a vytvoří definici zásady.

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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Následující soubory vytvořil `New-GuestConfigurationPolicy`:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Iniciativa. JSON**

Výstup rutiny vrátí objekt, který obsahuje zobrazovaný název iniciativy a cestu k souborům zásad.

Nakonec publikujte definice zásad pomocí `Publish-GuestConfigurationPolicy` rutiny.
Rutina má pouze parametr **path** , který odkazuje na umístění souborů JSON, které vytvořil `New-GuestConfigurationPolicy`.

K provedení příkazu Publikovat budete potřebovat přístup k vytváření zásad v Azure. Konkrétní autorizační požadavky jsou zdokumentovány na stránce [přehled Azure Policy](../overview.md) . Nejlepší integrovanou rolí je **Přispěvatel zásad prostředků**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 `Publish-GuestConfigurationPolicy` Rutina akceptuje cestu z kanálu PowerShellu. Tato funkce znamená, že můžete vytvořit soubory zásad a publikovat je v jedné sadě příkazů s použitím kanálu.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

V rámci zásad vytvořených v Azure je posledním krokem přiřazení iniciativy. Podívejte se, jak přiřadit iniciativu k [portálu](../assign-policy-portal.md), rozhraní příkazového [řádku Azure](../assign-policy-azurecli.md)a [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Zásady konfigurace hosta se musí **vždy** přiřadit pomocí iniciativy, která kombinuje zásady _AuditIfNotExists_ a _DeployIfNotExists_ . Pokud je přiřazena pouze zásada _AuditIfNotExists_ , požadavky nejsou nasazeny a zásady vždy ukazují, že jsou servery "0" kompatibilní.

Přiřazení definice zásady s efektem _DeployIfNotExists_ vyžaduje další úroveň přístupu. Chcete-li udělit nejnižší oprávnění, můžete vytvořit vlastní definici role, která rozšiřuje **přispěvatele zásad prostředků**. V následujícím příkladu se vytvoří role s názvem **Přispěvatel zásad prostředků Rewards** s dodatečným oprávněním _Microsoft. Authorization/roleAssignments/Write_.

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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Použití parametrů ve vlastních zásadách konfigurace hostů

Konfigurace hosta podporuje přepsání vlastností konfigurace v době běhu. Tato funkce znamená, že hodnoty v souboru MOF v balíčku není nutné považovat za statické. Hodnoty přepsání jsou poskytovány prostřednictvím Azure Policy a neovlivňují způsob, jakým jsou vytvořeny nebo kompilovány konfigurace.

S neurčenými specifikacemi jsou parametry obvykle zpracovávány jako vstup buď za běhu, nebo jako kód pomocí atributů. Konfigurace hosta zaznamená tento proces, takže zadání lze zadat při přiřazení zásady. Soubor atributů se automaticky vytvoří v rámci počítače. Nemusíte vytvářet a přidávat soubory do projektu. Existují dva kroky pro přidání parametrů do projektu auditu systému Linux.

V souboru Ruby definujte vstup, ve kterém chcete skriptovat, co se má na počítači auditovat. Příklad je uveden níže.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Rutiny `New-GuestConfigurationPolicy` a `Test-GuestConfigurationPolicyPackage` zahrnují parametr s názvem **Parameters**. Tento parametr přebírá zatřiďovací tabulku, včetně všech podrobností o jednotlivých parametrech, a automaticky vytvoří všechny požadované oddíly souborů, pomocí kterých se vytvoří každá definice Azure Policy.

Následující příklad vytvoří definici zásady pro audit cesty k souboru, kde uživatel zadá cestu v době přiřazení zásady.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

V případě zásad pro Linux zahrňte vlastnost **AttributesYmlContent** do konfigurace a podle potřeby hodnoty přepište. Agent konfigurace hosta automaticky vytvoří soubor YAML používaný nespecifikací k ukládání atributů. Viz následující příklad.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>Životní cyklus zásad

K vydání aktualizace definice zásady existují dvě pole, která vyžadují pozornost.

- **Verze**: když spustíte `New-GuestConfigurationPolicy` rutinu, musíte zadat číslo verze, které je větší než aktuálně publikované. Vlastnost aktualizuje verzi přiřazení konfigurace hosta, aby agent rozpoznal aktualizovaný balíček.
- **contentHash**: Tato vlastnost je automaticky aktualizována `New-GuestConfigurationPolicy` rutinou. Jedná se o hodnotu hash balíčku, kterou `New-GuestConfigurationPackage`vytvořil. Vlastnost musí být správná pro `.zip` soubor, který publikujete. Pokud se aktualizuje jenom vlastnost **contentUri** , rozšíření nepřijme balíček obsahu.

Nejjednodušším způsobem, jak vydat aktualizovaný balíček, je opakovat postup popsaný v tomto článku a zadat aktualizované číslo verze. Tento proces zaručuje, že všechny vlastnosti jsou správně aktualizované.

## <a name="optional-signing-guest-configuration-packages"></a>Volitelné: podepisování balíčků konfigurace hosta

Vlastní zásady konfigurace hosta používají SHA256 hash k ověření, že se balíček zásad nezměnil.
V případě potřeby mohou zákazníci také použít certifikát k podepisování balíčků a vynucení rozšíření konfigurace hosta pouze k povolení podepsaného obsahu.

Chcete-li povolit tento scénář, je třeba provést dva kroky. Spusťte rutinu pro podepsání balíčku obsahu a přidejte značku do počítačů, které by měly vyžadovat podepsání kódu.

Chcete-li použít funkci ověřování podpisem, `Protect-GuestConfigurationPackage` spusťte rutinu pro podepsání balíčku před jeho publikováním. Tato rutina vyžaduje certifikát pro podepsání kódu.

Parametry `Protect-GuestConfigurationPackage` rutiny:

- **Cesta**: úplná cesta k balíčku pro konfiguraci hosta.
- **PublicGpgKeyPath**: cesta k veřejnému klíči GPG. Tento parametr je podporován pouze při podepisování obsahu pro Linux.

Dobrá Reference k vytváření GPG klíčů pro použití s počítači se systémem Linux je poskytována článkem na GitHubu, který [generuje nový klíč GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Agent GuestConfiguration očekává, že se veřejný klíč certifikátu nachází v cestě `/usr/local/share/ca-certificates/extra` k počítačům se systémem Linux. Aby mohl uzel ověřit podepsaný obsah, nainstalujte na počítači veřejný klíč certifikátu a pak použijte vlastní zásady. Tento proces se dá provést pomocí libovolné techniky uvnitř virtuálního počítače nebo pomocí Azure Policy. [Tady je uvedena](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)Ukázková šablona.
Zásady přístupu Key Vault musí umožňovat poskytovateli výpočetních prostředků přístup k certifikátům během nasazení. Podrobný postup najdete v tématu [nastavení Key Vault pro virtuální počítače v Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Po publikování obsahu přidejte značku s názvem `GuestConfigPolicyCertificateValidation` a hodnotou `enabled` do všech virtuálních počítačů, kde by mělo být požadováno podepisování kódu. Podívejte se na [ukázky značek](../samples/built-in-policies.md#tags) , jak mohou být značky doručovány ve velkém rozsahu pomocí Azure Policy. Jakmile je tato značka nastavená, definice zásady vytvořená pomocí `New-GuestConfigurationPolicy` rutiny povolí požadavek prostřednictvím rozšíření konfigurace hosta.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Řešení potíží s přiřazením zásad konfigurace hosta (Preview)

Nástroj je k dispozici ve verzi Preview, který vám pomůže při řešení potíží s Azure Policy přiřazení konfigurace hostů. Nástroj je ve verzi Preview a byl publikován do Galerie prostředí PowerShell jako název modulu [Poradce při potížích s konfigurací hosta](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Další informace o rutinách v tomto nástroji získáte pomocí příkazu Get-Help v prostředí PowerShell k zobrazení integrovaných pokynů. Jak nástroj načítá časté aktualizace, což je nejlepší způsob, jak získat nejnovější informace.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o auditování virtuálních počítačů pomocí [Konfigurace hostů](../concepts/guest-configuration.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](get-compliance-data.md).
