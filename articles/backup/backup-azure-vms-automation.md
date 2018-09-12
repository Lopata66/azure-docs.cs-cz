---
title: Nasazení a správa zálohování virtuálních počítačů nasazených Resource Managerem pomocí PowerShellu
description: Použití Powershellu k nasazení a správa záloh v Azure u virtuálních počítačů nasazených Resource Managerem
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/06/2018
ms.author: markgal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: faa0908f9317c10713c41d06a22e9251998fe3c7
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378444"
---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>Použití rutin AzureRM.RecoveryServices.Backup k zálohování virtuálních počítačů

V tomto článku se dozvíte, jak pomocí rutin Azure Powershellu k zálohování a obnovení Azure virtuálního počítače (VM) z trezoru služby Recovery Services. Trezor služby Recovery Services je prostředek Azure Resource Manageru a slouží k ochraně dat a assetů ve službě Azure Backup a Azure Site Recovery services. Trezor služby Recovery Services můžete použít k ochraně virtuálních počítačů nasazených Azure Service Manager a virtuálních počítačů nasazených Azure Resource Manageru.

> [!NOTE]
> Azure obsahuje dva modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../azure-resource-manager/resource-manager-deployment-model.md). Tento článek je určený pro použití s virtuálními počítači vytvořené pomocí modelu Resource Manageru.
>
>

Tento článek vás provede pomocí prostředí PowerShell k ochraně virtuálního počítače a obnovení dat z bodu obnovení.

## <a name="concepts"></a>Koncepty
Pokud nejste obeznámeni se službou Azure Backup, získáte přehled o službě, přečtěte si [co je Azure Backup?](backup-introduction-to-azure-backup.md) Než začnete, ujistěte se, že jste zahrnuli Základy o součásti potřebné pro práci s Azure Backup a omezení aktuálního řešení zálohování virtuálního počítače.

Jak efektivně pomocí prostředí PowerShell, je nezbytné pro zjištění hierarchie objektů a ze které se mají spustit.

![Hierarchie objektů Recovery Services](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Reference k rutinám Powershellu AzureRm.RecoveryServices.Backup, naleznete v tématu [Azure Backup – rutiny služby zotavení](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) v knihovně Azure.

## <a name="setup-and-registration"></a>Instalace a registrace
Chcete-li začít:

1. [Stáhněte si nejnovější verzi prostředí PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (minimální požadovaná verze je: 1.4.0)

2. Vyhledání dostupných rutin Powershellu pro zálohování Azure tak, že zadáte následující příkaz:
    ```PS
    PS C:\> Get-Command *azurermrecoveryservices*
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
    Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
    Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
    Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
    Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
    Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
    Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
    Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
    Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
    ```
3. Účet přihlášení pro aplikaci Azure pomocí **Connect-AzureRmAccount**. Tato rutina se vyvolá na webové stránce vás vyzve k zadání přihlašovacích údajů k účtu: 
    - Alternativně můžete zahrnout přihlašovacích údajů k účtu jako parametr v **Connect-AzureRmAccount** rutiny, pomocí **-Credential** parametr.
    - Pokud jste partner CSP, který spolupracuje jménem klienta, určení zákazníka jako tenant, pomocí názvu primární doména tenanta nebo ID Tenanta. Příklad: **Connect-AzureRmAccount-Tenant "fabrikam.com"**
4. Přidružte předplatné, které chcete používat s účtem, protože účet může mít několik předplatných:

    ```PS
    PS C:\> Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Pokud používáte Azure Backup poprvé, je nutné použít **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** rutiny zaregistrujte zprostředkovatele služby Azure Recovery s vaším předplatným.

    ```PS
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Můžete ověřit, že zprostředkovatele úspěšně zaregistrován, pomocí následujících příkazů:
    ```PS
    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ``` 
Ve výstupu tohoto příkazu **RegistrationState** měli nastavit na **registrované**. Pokud ne, stačí znovu spustit **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** výše uvedenou rutinu.

Tyto úlohy je možné automatizovat pomocí prostředí PowerShell:

* [Vytvoření trezoru služby Recovery Services](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Zálohování virtuálních počítačů Azure](backup-azure-vms-automation.md#back-up-azure-vms)
* [Aktivace úlohy zálohování](backup-azure-vms-automation.md#trigger-a-backup-job)
* [Monitorování úlohy zálohování](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Obnovení virtuálního počítače Azure](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru služby Recovery Services

Následující kroky vás provedou vytvořením trezor služby Recovery Services. Trezor služby Recovery Services se liší od trezoru služby Backup.

1. Trezor služby Recovery Services je prostředek Resource Manageru, proto musíte umístit do skupiny prostředků. Můžete použít existující skupinu prostředků nebo vytvořte skupinu prostředků pomocí **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** rutiny. Při vytváření skupiny prostředků, zadejte název a umístění pro skupinu prostředků.  

    ```PS
    PS C:\> New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Použití **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** rutina pro vytvoření trezoru služby Recovery Services. Ujistěte se, k určení stejného umístění trezoru, protože byl použit pro skupinu prostředků.

    ```PS
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Zadejte typ redundance úložiště se použije. můžete použít [místně redundantní úložiště (LRS)](../storage/common/storage-redundancy-lrs.md) nebo [geograficky redundantního úložiště (GRS)](../storage/common/storage-redundancy-grs.md). Následující příklad ukazuje, že možnost - BackupStorageRedundancy pro testvault nastavená na GeoRedundant.

    ```PS
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Řada rutin služby Azure Backup vyžaduje jako vstup objekt trezoru služby Recovery Services. Z tohoto důvodu je vhodné uložit objekt trezoru služby Recovery Services do proměnné.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Zobrazit tyto trezory v rámci předplatného
Použití **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)** zobrazíte seznam všech trezorů v rámci aktuálního předplatného. Tento příkaz můžete použít ke kontrole, zda byl vytvořen nový trezor, nebo můžete zobrazit dostupné trezorů v předplatném.

Spuštěním příkazu Get-AzureRmRecoveryServicesVault, chcete-li zobrazit všechny trezorů v předplatném. Následující příklad ukazuje informace zobrazené pro každý trezor.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Zálohování virtuálních počítačů Azure
Ochrana virtuálních počítačů pomocí trezoru služby Recovery Services. Před použitím ochrany nastavte kontext trezoru (typ chráněných dat v trezoru) a ověřte zásady ochrany. Plán spouštění úloh zálohování a jak dlouho se uchovávají každý snímek zálohy, jsou zásady ochrany.

### <a name="set-vault-context"></a>Kontext trezoru set
Než povolíte ochranu virtuálního počítače, použijte **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** nastavit kontext trezoru. Po nastavení se kontext trezoru použije pro všechny další rutiny. Následující příklad nastaví kontext trezoru pro trezor, *testvault*.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Vytvořit zásady ochrany.
Při vytváření trezoru služby Recovery Services se vytvoří i výchozí zásady ochrany a uchovávání informací. Výchozí zásady ochrany aktivují úlohu zálohování každý den v určenou dobu. Výchozí zásady uchovávání informací uchovávají denní bod obnovení po dobu 30 dnů. Výchozí zásady můžete použít k rychlému zajištění ochrany vašeho virtuálního počítače a upravovat zásady později pomocí různých údajů.

Použití **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** pro zobrazení zásad ochrany v trezoru. Načte konkrétní zásady, nebo pro zobrazení zásad přidružený k typu úlohy, můžete použít tuto rutinu. Následující příklad získá zásady pro typ úlohy, AzureVM.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Časové pásmo BackupTime pole v prostředí PowerShell je čas UTC. Když čas zálohování se zobrazí na webu Azure Portal, čas upraveny na místní časové pásmo.
>
>

Zásady zálohování ochrany je přidružená aspoň jednu zásadu uchovávání informací. Zásady uchovávání informací Určuje, jak dlouho bod obnovení je zachována před odstraněním. Použití **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** zobrazení výchozí zásady uchovávání informací.  Podobně můžete použít **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** získat výchozí plán zásady. **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** rutina vytvoří objekt prostředí PowerShell, který obsahuje informace o zásadách zálohování. Objekty zásad plán a uchovávání se používají jako vstupy **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** rutiny. Následující příklad ukládá v proměnné plán zásady a zásady uchovávání informací. V příkladu se používá k definici parametrů při vytváření zásad ochrany těchto proměnných *NewPolicy*.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```


### <a name="enable-protection"></a>Povolení ochrany
Po definování zásad zálohování ochrany stále musíte povolit zásady pro položku. Použití **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** povolit ochranu. Povolení ochrany vyžaduje dva objekty - položka a zásady. Jakmile zásadu přidružená k trezoru, pracovního postupu zálohování se aktivuje v době definovaný v plánu zásady.

Následující příklad povolí ochranu pro položku V2VM, pomocí zásad NewPolicy. Povolit ochranu v nešifrované virtuální počítače Resource Manageru

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Pokud chcete povolit ochranu šifrovaných virtuálních počítačů (šifrované pomocí klíče BEK a KEK), je třeba sdělit Azure Backup service oprávnění ke čtení klíče a tajné kódy z trezoru klíčů.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Povolení ochrany na šifrovaných virtuálních počítačů (šifrované pomocí klíče BEK pouze), budete muset udělit oprávnění služby Azure Backup pro čtení tajných klíčů z trezoru klíčů.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Pokud používáte cloud Azure Government, použijte pro parametr hodnotu ff281ffe-705c-4f53-9f37-a40e6f2c68f3 **- ServicePrincipalName** v [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) rutiny.
>
>

Pro klasické virtuální počítače

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>Upravit zásady ochrany.
Chcete-li upravit zásady ochrany, použijte [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) upravit objekty SchedulePolicy nebo parametru RetentionPolicy.

Následující příklad změní až 365 dnů uchování bodu obnovení.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Aktivujte zálohování
Můžete použít **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** k aktivaci úlohy zálohování. Pokud se jedná o prvotní zálohování, je úplná záloha. Následné zálohy trvat, než přírůstková kopie. Nezapomeňte použít **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** nastavit kontext trezoru před aktivací úlohy zálohování. V následujícím příkladu se předpokládá, že byl nastaven kontext trezoru.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> Časové pásmo polí StartTime a EndTime v prostředí PowerShell je čas UTC. Když čas se zobrazí na webu Azure Portal, čas upraveny na místní časové pásmo.
>
>

## <a name="monitoring-a-backup-job"></a>Monitorování úlohy zálohování
Dlouhotrvající operace, jako je například úlohy zálohování, můžete sledovat bez použití webu Azure portal. Chcete-li zjistit stav probíhající úlohy, použijte **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** rutiny. Tato rutina načte úlohy zálohování pro konkrétní trezor a tento trezor je zadán v kontext trezoru. Následující příklad získá stav probíhající úlohy jako pole a uloží stav $joblist proměnné.

```
PS C:\> $joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
PS C:\> $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Namísto dotazování těchto úloh pro dokončení – což je zbytečné další kód – použít **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** rutiny. Tato rutina pozastaví provádění až do dokončení úlohy nebo nebude dosaženo hodnoty zadaný časový limit.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Obnovení virtuálního počítače Azure
Je klíčovým rozdílem mezi obnovení virtuálního počítače pomocí webu Azure portal a obnovení virtuálního počítače pomocí Powershellu. Pomocí Powershellu operace obnovení byla dokončena, jakmile disky a informace o konfiguraci z bodu obnovení se vytvoří. Pokud chcete pro obnovení několika souborů ze zálohy virtuálního počítače Azure, přečtěte si [sekce obnovení souboru](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)

> [!NOTE]
> Operace obnovení není vytvoření virtuálního počítače.
>
>

K vytvoření virtuálního počítače z disku, naleznete v části [vytvořit virtuální počítač z obnovených disků](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Toto jsou základní kroky k obnovení virtuálního počítače Azure:

* Vyberte virtuální počítač
* Zvolte bod obnovení
* Obnovit disky
* Vytvoření virtuálního počítače z uložené disků

Následující obrázek znázorňuje hierarchii objektů z RecoveryServicesVault dolů BackupRecoveryPoint.

![Hierarchie objektů služby zotavení zobrazující BackupContainer](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Chcete-li obnovit zálohovaná data, identifikujte zálohovaná položka a bod obnovení, který obsahuje data bodu v čase. Použití **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** rutiny k obnovení dat z úložiště do účtu zákazníka.

### <a name="select-the-vm"></a>Vyberte virtuální počítač
Chcete-li získat objekt prostředí PowerShell, který identifikuje přímo zálohovaná položka, spusťte z kontejneru v trezoru a nahlíželi hierarchií objektu. Chcete-li vybrat kontejner, který představuje virtuální počítač, použijte **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** rutiny a kanálem, který se **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** rutiny.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Zvolte bod obnovení
Použití **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** rutiny pro zobrazení seznamu všech bodů obnovení zálohované položky. Zvolte bod obnovení pro obnovení. Pokud si nejste jistí, který bod obnovení používat, je vhodné zvolte nejnovější RecoveryPointType = AppConsistent bod v seznamu.

V následujícím skriptu, proměnné, **$rp**, je pole bodů obnovení pro vybrané záložní položky z posledních sedmi dnů. Pole je v obráceném pořadí řazení čas nabízí nejnovější bod obnovení na pozici 0. Použijte standardní indexování pole prostředí PowerShell a vyberte bod obnovení. V tomto příkladu $rp [0] Vybere poslední bod obnovení.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```



### <a name="restore-the-disks"></a>Obnovit disky
Použití **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** rutiny obnovit data zálohovaná položka a konfigurace bodu obnovení. Po zjištění bod obnovení, použijte jako hodnotu **- RecoveryPoint** parametru. V předchozí ukázce kódu **$rp [0]** byl bod obnovení použít. V následujícím ukázkovém kódu **$rp [0]** je bod obnovení pro obnovení disku.

Chcete-li obnovit disky a konfigurační informace:

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Použití **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** rutiny čekat na dokončení úlohy obnovení.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Po dokončení úlohy obnovení, použijte **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** rutiny zobrazíte podrobnosti o operaci obnovení. Vlastnost JobDetails obsahuje informace potřebné k opětovnému sestavení virtuálního počítače.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Po obnovení disků, přejděte k další části, chcete-li vytvořit virtuální počítač.

## <a name="create-a-vm-from-restored-disks"></a>Vytvořte virtuální počítač z obnovených disků
Po obnovení disků pomocí těchto kroků můžete vytvořit a nakonfigurovat virtuální počítač z disku.

> [!NOTE]
> Vytvořit z obnovených disků šifrovaných virtuálních počítačů, Azure role musí mít oprávnění k provedení akce, **Microsoft.KeyVault/vaults/deploy/action**. Pokud vaše role toto oprávnění nemá, vytvořte vlastní roli s touto akcí. Další informace najdete v tématu [vlastní role Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

1. Dotaz na vlastnosti obnoveného disku pro podrobnosti o úloze.

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $configBlobName = $properties["Config Blob Name"]
  ```

2. Nastavit kontext úložiště Azure a obnovení konfiguračního souboru JSON.

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. Použití konfiguračního souboru JSON pro vytvoření konfigurace virtuálního počítače.

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
    ```

4. Připojte disk s operačním systémem a datové disky. V závislosti na konfiguraci vašich virtuálních počítačů najdete v příslušné části zobrazíte příslušné rutiny:

    #### <a name="non-managed-non-encrypted-vms"></a>Nespravovaná, bez šifrování virtuálních počítačů

    Použijte následující ukázku pro nespravované, bez šifrování virtuálních počítačů.

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
    PS C:\> foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
    {
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="non-managed-encrypted-vms-bek-only"></a>Nespravovaná a šifrované virtuální počítače (pouze klíče BEK)

    Pro jiné spravované, šifrované virtuální počítače (šifrované pomocí klíče BEK pouze) budete muset obnovit tajný kód k trezoru klíčů, než disky můžete připojit. Další informace najdete v článku [obnovení šifrovaných virtuálních počítačů z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Následující příklad ukazuje, jak připojit operačního systému a datové disky pro šifrované virtuální počítače.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    ```
    
 Při nastavování disk s operačním systémem, ujistěte se, že je uvedena odpovídající typ operačního systému   
    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```
    
Šifrování disků dat musí být povolené ručně, pomocí následujícího příkazu.

    ```
    Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -VolumeType Data
    ```
    
   #### <a name="non-managed-encrypted-vms-bek-and-kek"></a>Nespravovaná a šifrované virtuální počítače (klíče BEK a KEK)

   Pro jiné spravované, šifrované virtuální počítače (šifrované pomocí klíče BEK a KEK) budete muset obnovit klíč a tajný kód trezoru klíčů než disky můžete připojit. Další informace najdete v článku [obnovení šifrovaných virtuálních počítačů z bodu obnovení Azure Backup](backup-azure-restore-key-secret.md). Následující příklad ukazuje, jak připojit operačního systému a datové disky pro šifrované virtuální počítače.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

Šifrování disků dat musí být povolené ručně, pomocí následujícího příkazu.

    ```
    Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
    ```
    
   #### <a name="managed-non-encrypted-vms"></a>Managed-šifrovaných virtuálních počítačů

   Pro spravované nešifrované virtuální počítače budete muset vytvoření spravovaných disků z úložiště objektů blob a pak připojte disky. Podrobné informace najdete v článku, [připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md). Následující ukázkový kód ukazuje, jak připojit datové disky pro virtuální počítače spravované bez šifrování.

    ```
    PS C:\> $storageType = "Standard_LRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

   #### <a name="managed-encrypted-vms-bek-only"></a>Spravované, šifrované virtuální počítače (pouze klíče BEK)

   Pro spravované šifrované virtuální počítače (šifrované pomocí klíče BEK pouze) budete muset vytvoření spravovaných disků z úložiště objektů blob a pak připojte disky. Podrobné informace najdete v článku, [připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md). Následující ukázkový kód ukazuje, jak připojit datové disky pro spravovaných šifrovaných virtuálních počítačů.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "Standard_LRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

Šifrování disků dat musí být povolené ručně, pomocí následujícího příkazu.

    ```
    Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
    ```
    
   #### <a name="managed-encrypted-vms-bek-and-kek"></a>Spravované, šifrované virtuální počítače (klíče BEK a KEK)

   Pro spravované šifrované virtuální počítače (šifrované pomocí klíče BEK a KEK) budete muset vytvoření spravovaných disků z úložiště objektů blob a pak připojte disky. Podrobné informace najdete v článku, [připojení datového disku k virtuálnímu počítači s Windows pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md). Následující ukázkový kód ukazuje, jak připojit datové disky pro spravovaných šifrovaných virtuálních počítačů.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "Standard_LRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```
Šifrování disků dat musí být povolené ručně, pomocí následujícího příkazu.

    ```
    Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
    ```
    
5. Nastavení sítě.

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    PS C:\> $virtualNetwork | Set-AzureRmVirtualNetwork
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $subnetindex=0
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Vytvořte virtuální počítač.

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Obnovení souborů ze zálohy virtuálního počítače Azure

Kromě obnovení disků, můžete také obnovit jednotlivé soubory ze zálohy virtuálního počítače Azure. Funkce obnovení souborů poskytuje přístup ke všem souborům v bodu obnovení a budete moci spravovat přes Průzkumníka souborů a jako byste to udělali pro normální soubory.

Toto jsou základní kroky obnovení souboru ze zálohy virtuálního počítače Azure:

* Vyberte virtuální počítač
* Zvolte bod obnovení
* Připojte disky bod obnovení
* Zkopírujte požadované soubory
* Odpojení disku


### <a name="select-the-vm"></a>Vyberte virtuální počítač
Chcete-li získat objekt prostředí PowerShell, který identifikuje přímo zálohovaná položka, spusťte z kontejneru v trezoru a nahlíželi hierarchií objektu. Chcete-li vybrat kontejner, který představuje virtuální počítač, použijte **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** rutiny a kanálem, který se **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** rutiny.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Zvolte bod obnovení
Použití **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** rutiny pro zobrazení seznamu všech bodů obnovení zálohované položky. Zvolte bod obnovení pro obnovení. Pokud si nejste jistí, který bod obnovení používat, je vhodné zvolte nejnovější RecoveryPointType = AppConsistent bod v seznamu.

V následujícím skriptu, proměnné, **$rp**, je pole bodů obnovení pro vybrané záložní položky z posledních sedmi dnů. Pole je v obráceném pořadí řazení čas nabízí nejnovější bod obnovení na pozici 0. Použijte standardní indexování pole prostředí PowerShell a vyberte bod obnovení. V tomto příkladu $rp [0] Vybere poslední bod obnovení.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Připojte disky bod obnovení

Použití **[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** rutiny pro získání skript, který připojí všechny disky bodu obnovení.

> [!NOTE]
> Disky jsou připojené jako disky připojené přes iSCSI k počítači, ve kterém se skript spouští. Proto je téměř okamžité a nejsou spojené žádné poplatky
>
>

```
PS C:\> Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]

OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```
Spusťte skript na počítači, kam chcete soubory obnovit. Budete muset zadat heslo uvedené nahoře a spustit skript. Po disky jsou připojené, projděte si nové svazky a souborů pomocí Průzkumníka souborů Windows. Další informace najdete [soubor dokumentace k obnovení](backup-azure-restore-files-from-vm.md)

### <a name="unmount-the-disks"></a>Odpojení disků
Po zkopírování souborů nutných odpojení disků pomocí **[zakázat AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** rutiny. Se důrazně doporučuje jako zajišťuje, že je odebrán přístup k souborům bod obnovení

```
PS C:\> Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```


## <a name="next-steps"></a>Další postup
Pokud chcete spolupracovat s prostředky Azure pomocí Powershellu, najdete v článku prostředí PowerShell, [nasazení a Správa zálohování pro Windows Server](backup-client-automation.md). Pokud budete spravovat zálohy aplikace DPM, najdete v článku, [nasazení a Správa zálohování aplikace DPM](backup-dpm-automation.md). Z těchto článků mají verze pro nasazení Resource Manager a klasickými nasazeními.  
