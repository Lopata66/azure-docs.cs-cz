---
title: Vytvoření a šifrování virtuálního počítače s Windows s využitím Azure PowerShellu
description: V tomto rychlém startu se dozvíte, jak pomocí Azure PowerShell vytvořit a zašifrovat virtuální počítač s Windows.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a50b62a7c6064672dfbf7d609b6053d7be6fdb77
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079485"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Rychlý Start: vytvoření a šifrování virtuálního počítače s Windows v Azure pomocí PowerShellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. V tomto rychlém startu se dozvíte, jak pomocí modulu Azure PowerShell vytvořit virtuální počítač s Windows, vytvořit Key Vault pro ukládání šifrovacích klíčů a zašifrovat virtuální počítač. 

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač Azure pomocí [New-AzVM](/powershell/module/az.compute/new-azvm). Do rutiny musíte zadat přihlašovací údaje. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

Nasazení virtuálního počítače bude několik minut trvat. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Vytvoření Key Vault nakonfigurovaného pro šifrovací klíče

Azure Disk Encryption ukládá šifrovací klíč do Azure Key Vault. Vytvořte Key Vault pomocí [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Pokud chcete povolit Key Vault ukládání šifrovacích klíčů, použijte parametr-EnabledForDiskEncryption.

> [!Important]
> Každý Key Vault musí mít jedinečný název. Následující příklad vytvoří Key Vault s názvem *myKV*, ale je nutné, abyste si pojmenovali něco jiného.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Zašifrovat virtuální počítač

Zašifrujte virtuální počítač pomocí [set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Příkaz set-AzVmDiskEncryptionExtension vyžaduje některé hodnoty z vašeho objektu Key Vault. Tyto hodnoty můžete získat předáním jedinečného názvu trezoru klíčů pro [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Po několika minutách bude proces vracet následující:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

Proces šifrování můžete ověřit spuštěním [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Pokud je povolené šifrování, ve vráceném výstupu se zobrazí následující:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a všech souvisejících prostředků použít rutinu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) :

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili virtuální počítač, vytvořili Key Vault, která byla povolená pro šifrovací klíče, a zašifroval virtuální počítač.  V dalším článku najdete další informace o požadavcích na službu Azure Disk Encryption pro virtuální počítače IaaS.

> [!div class="nextstepaction"]
> [Přehled Azure Disk Encryption](disk-encryption-overview.md)
