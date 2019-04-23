---
title: Nastavení zotavení po havárii virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality pomocí Azure Site Recovery a Powershellu | Dokumentace Microsoftu
description: Popisuje, jak nastavit zotavení po havárii virtuálních počítačů Hyper-V v cloudech VMM do sekundární lokality VMM s využitím Azure Site Recovery a prostředí PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 78bd077b5491b093510b9c55bf7b5a42ee9cb578
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787594"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Nastavení zotavení po havárii virtuálních počítačů Hyper-V do sekundární lokality pomocí prostředí PowerShell (Resource Manager)

Tento článek popisuje, jak automatizovat kroky pro replikaci virtuálních počítačů Hyper-V v cloudech System Center Virtual Machine Manager cloud Virtual Machine Manager v sekundární místní lokality pomocí [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

- Prostudujte si [architekturu a komponenty scénáře](hyper-v-vmm-architecture.md).
- Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-to-sec-site.md) pro všechny komponenty.
- Ujistěte se, že servery Virtual Machine Manager a hostitelů Hyper-V souladu s [požadavky na podporu](site-recovery-support-matrix-to-sec-site.md).
- Zkontrolujte, jestli virtuální počítače, které chcete replikovat v souladu s [počítač podporu replikovaných](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>Příprava mapování sítě

[Mapování sítě](hyper-v-vmm-network-mapping.md) mapování mezi místní sítí virtuálních počítačů Virtual Machine Manager v zdrojovým a cílovým cloudem. Mapování provádí následující:

- Po převzetí služeb při selhání připojí virtuální počítače k odpovídajícím cílovým sítím virtuálních počítačů. 
- Optimálně umístí repliky virtuálních počítačů na cílové hostitelské servery Hyper-V. 
- Pokud nenakonfigurujete mapování sítě, virtuální počítače replik nebude připojen k síti virtuálních počítačů po převzetí služeb při selhání.

Příprava Virtual Machine Manager následujícím způsobem:

* Ujistěte se, že máte [logických sítí Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/network-logical) ve zdrojové i cílové servery Virtual Machine Manager:

    - Logická síť na zdrojovém serveru musí být přidružená ke zdrojovému cloudu, ve kterém se nacházejí hostitelé Hyper-V.
    - Logická síť na cílovém serveru musí být přidružená k cílovému cloudu.
* Ujistěte se, že máte [sítě virtuálních počítačů](https://docs.microsoft.com/system-center/vmm/network-virtual) ve zdrojové i cílové servery Virtual Machine Manager. Sítě virtuálních počítačů musí být v obou umístěních propojené s logickou sítí.
* Připojte virtuální počítače na zdrojových hostitelích Hyper-V ke zdrojové síti virtuálních počítačů. 

## <a name="prepare-for-powershell"></a>Příprava pro prostředí PowerShell

Ujistěte se, že máte Azure PowerShell, jste připravení začít:

- Pokud už používáte PowerShell, upgradujte na verzi 0.8.10 nebo novější. [Další informace](/powershell/azureps-cmdlets-docs) o tom, jak nastavit prostředí PowerShell.
- Po nastavení a konfigurace prostředí PowerShell, zkontrolujte [služby rutiny](/powershell/azure/overview).
- Další informace o tom, jak používat hodnoty parametrů, vstupy a výstupy v prostředí PowerShell najdete v článku [Začínáme](/powershell/azure/get-started-azureps) průvodce.

## <a name="set-up-a-subscription"></a>Nastavení předplatného
1. Z prostředí PowerShell Přihlaste se ke svému účtu Azure.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzAccount #-Credential $Cred
2. Načte seznam vašich předplatných, s ID předplatného. Poznamenejte si ID předplatného, ve kterém chcete vytvořit trezor služby Recovery Services. 

        Get-AzSubscription
3. Nastavte předplatné pro trezor.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení
1. Pokud ho nemáte, vytvořte skupinu prostředků Azure Resource Manageru.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Vytvořte nový trezor služby Recovery Services. Uložte objekt trezoru v proměnné pro pozdější použití. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    Po vytvoření pomocí rutiny Get-AzRecoveryServicesVault můžete pak získat objekt trezoru.

## <a name="set-the-vault-context"></a>Nastavte kontext trezoru
1. Načte existující trezor.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Nastavte kontext trezoru.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>Instalace zprostředkovatele služby Site Recovery
1. Na počítači Virtual Machine Manager vytvořte adresář spuštěním následujícího příkazu:

       New-Item c:\ASR -type directory
2. Extrahujte soubory s použitím instalační soubor stažený zprostředkovatele.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Nainstalujte zprostředkovatele a počkejte na dokončení instalace.

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. Zaregistrujte server v trezoru.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Vytvořit a přidružit zásady replikace
1. Vytvoření zásady replikace, v tomto případě pro Hyper-V 2012 R2, následujícím způsobem:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > Virtual Machine Manager cloud obsahovat hostitele Hyper-V s různými verzemi Windows serveru, ale je zásada replikace pro konkrétní verze operačního systému. Pokud máte na různých hostitelích, které běží na různých operačních systémů, vytvořte zásady samostatné replikace u každého systému. Například pokud máte pět hostitelů, které běží na Windows Server 2012 a tři hostitele se systémem Windows Server 2012 R2 vytvoříte dvě zásady replikace. Můžete vytvořit pro každý typ operačního systému.

2. Načíst primární ochranu kontejneru (primární Virtual Machine Manager cloud) a obnovení kontejneru ochrany (obnovení Virtual Machine Manager cloud).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Načtěte zásadu replikace, kterou jste vytvořili pomocí popisný název.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Začněte přidružení kontejneru ochrany (Virtual Machine Manager cloud) k zásadě replikace.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Počkejte na dokončení úlohy přidružení zásad. Pokud chcete zkontrolovat, pokud je úloha dokončena, použijte následující fragment kódu Powershellu:

       $job = Get-AzSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. Jakmile se úlohy dokončí zpracování, spusťte následující příkaz:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

Pokud chcete zkontrolovat dokončení operace, postupujte podle kroků v [sledovat činnost](#monitor-activity).

##  <a name="configure-network-mapping"></a>Konfigurace mapování sítě
1. Tento příkaz k načtení serverů pro aktuální úložiště. Tento příkaz uloží do proměnné pole $Servers servery Site Recovery.

        $Servers = Get-AzSiteRecoveryServer
2. Spusťte tento příkaz k načtení sítě pro Virtual Machine Manager na zdrojovém a cílovém serveru Virtual Machine Manager.

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > Na zdrojovém serveru Virtual Machine Manager může být ta první nebo druhé pole serveru. Zkontrolovat názvy serveru Virtual Machine Manager a načíst sítě odpovídajícím způsobem.


3. Tato rutina vytvoří mapování mezi primární síť a síť pro obnovení. Určuje primární síť jako prvního prvku $PrimaryNetworks. Jako první prvek $RecoveryNetworks určuje síť pro obnovení.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Povolit ochranu pro virtuální počítače
Po serverů, cloudů a sítí jsou nakonfigurovány správně, povolte ochranu pro virtuální počítače v cloudu.

1. Pokud chcete povolit ochranu, spusťte následující příkaz, který načte kontejner ochrany:

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Entita ochrany (VM), získáte takto:

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Povolení replikace pro virtuální počítač.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

K otestování nasazení, spuštění testovací převzetí služeb při selhání pro jeden virtuální počítač. Také můžete vytvořit plán obnovení, který obsahuje několik virtuálních počítačů a testovací převzetí služeb při selhání pro plán. Testovací převzetí služeb při selhání simuluje váš mechanismus převzetí služeb při selhání a zotavení v izolované síti.

1. Načte virtuální počítač, do kterého virtuální počítače se převzetí služeb při selhání.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Proveďte testovací převzetí služeb.

   Pro jeden virtuální počítač:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   Pro plán obnovení:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

Pokud chcete zkontrolovat dokončení operace, postupujte podle kroků v [sledovat činnost](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Spusťte plánované a neplánované převzetí služeb při selhání

1. Proveďte plánované převzetí služeb při selhání.

   Pro jeden virtuální počítač:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Pro plán obnovení:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Provedení neplánovaného převzetí služeb při selhání.

   Pro jeden virtuální počítač:
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Pro plán obnovení:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Monitorování aktivity
Monitorovat aktivitu převzetí služeb při selhání, použijte následující příkazy. Počkejte na dokončení mezi úloh zpracování.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Další postup

[Další informace](/powershell/module/az.recoveryservices) o službě Site Recovery pomocí rutin Powershellu pro Resource Manager.
