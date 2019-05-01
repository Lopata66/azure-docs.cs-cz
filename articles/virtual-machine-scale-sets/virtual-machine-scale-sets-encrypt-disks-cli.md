---
title: Šifrování disků pro Azure škálovací sady pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak používat rozhraní příkazového řádku Azure k šifrování instancí virtuálních počítačů a připojených disků ve škálovací sadě virtuálních počítačů Linux
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: cynthn
ms.openlocfilehash: 1264c7e4ebaf5e948e624fa49dc5fb0b4cdb31f0
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64869060"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Šifrování operačního systému a připojené datové disky ve virtuálním počítači škálovací sadě pomocí Azure CLI

Pro ochranu dat v klidovém stavu pomocí standardní šifrovací technologie, podporují škálovací sady virtuálních počítačů Azure Disk Encryption (ADE). Šifrování je možné povolit pro systémy Linux a Windows virtuálního počítače škálovací sady. Další informace najdete v tématu [Azure Disk Encryption pro systémy Linux a Windows](../security/azure-security-disk-encryption.md).

Azure disk encryption je podporována:
- pro škálovací sady se spravovanými disky vytvořené a není podporováno pro nativní (nebo nespravované) disku škálovací sady.
- pro operační systém a datové svazky ve Windows škálovacích sadách. Zakázat šifrování je podporováno pro operační systém a datové svazky pro Windows škálovací sady.
- pro datové svazky v Linuxu škálovací sady. Šifrování disku operačního systému není podporován pro Linux škálovací sady.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento kurz vyžaduje použití Azure CLI verze 2.0.31 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-scale-set"></a>Vytvoření škálovací sady

Než vytvoříte škálovací sadu, vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teď vytvořte škálovací sadu virtuálních počítačů pomocí příkazu [az vmss create](/cli/azure/vmss). Následující příklad vytvoří škálovací sadu *myScaleSet* nastavenou tak, aby se při provedení změn automaticky aktualizovala, a vygeneruje klíče SSH v adresáři *~/.ssh/id_rsa*, pokud ještě neexistují. 32Gb datový disk je připojena k jednotlivých instancí virtuálních počítačů a Azure [rozšíření vlastních skriptů](../virtual-machines/linux/extensions-customscript.md) slouží k přípravě datových disků s [az vmss extension set](/cli/azure/vmss/extension):

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Vytvořit trezor klíčů Azure nepovoluje šifrování disku

Azure Key Vault umí ukládat klíče, tajné kódy nebo hesla, které umožňují bezpečný je implementovat ve svých aplikacích a službách. Kryptografické klíče jsou uložené ve službě Azure Key Vault software ochrany, nebo můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM) certifikovaných podle standardů FIPS 140-2 úrovně 2 standardů. Tyto klíče se používají k šifrování a dešifrování virtuální disky připojené k virtuálnímu počítači. Uchování kontroly nad těmito kryptografické klíče a můžete kontrolovat jejich použití.

Definovat vlastní jedinečné *keyvault_name*. Vytvořte trezor klíčů s [az keyvault vytvořit](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) ve stejném předplatném a oblasti jako škálovací nastavit a nastavit *– povolené pro šifrování disků* zásady přístupu.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Použít existující služby Key Vault

Tento krok je jenom nutné, pokud máte existující Key Vault, kterou chcete použít šifrování disku. Tento krok přeskočte, pokud jste vytvořili službu Key Vault v předchozí části.

Definovat vlastní jedinečné *keyvault_name*. Potom aktualizovat váš trezor klíčů s [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) a nastavit *– povolené pro šifrování disků* zásady přístupu.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Povolit šifrování

K šifrování instancí virtuálních počítačů ve škálovací sadě, nejdřív získejte některé informace o ID prostředku Key Vault s [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Tyto proměnné se používají k spusťte proces šifrování s [povolit šifrování az vmss](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Může trvat minutu nebo dvě spuštění se proces šifrování.

Jako škálovací sada je zásad upgradu na škálovací sadu vytvořenou v předchozím kroku je nastavena na *automatické*, instance virtuálních počítačů automaticky spustit proces šifrování. Na škálovací sady, ve kterém je zásad upgradu na ruční, spusťte zásady šifrování v instancích virtuálních počítačů s [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Povolit šifrování pomocí KEK zabalit klíč

Můžete také použít klíčového šifrovacího klíče pro přidání zabezpečení při šifrování škálovací sadu virtuálních počítačů.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  Syntaxe pro hodnotu parametru disk šifrování – trezor klíčů je úplný identifikátor řetězce:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> Syntaxe pro hodnoty parametru klíč šifrovacího klíče je úplný identifikátor URI klíče KEK jako v:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Kontrola průběhu šifrování

Chcete-li zkontrolovat stav šifrování disku, použijte [az vmss šifrování zobrazit](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Když instance virtuálních počítačů jsou zašifrované, stavový kód zprávy *EncryptionState/šifrované*, jak je znázorněno v následujícím příkladu výstupu:

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Zakázat šifrování

Pokud již nechcete používat šifrovanými disky instancí virtuálních počítačů, můžete zakázat šifrování s [zakázat šifrování az vmss](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) následujícím způsobem:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Další postup

- V tomto článku se používá rozhraní příkazového řádku Azure k šifrování škálovací sadu virtuálních počítačů. Můžete také použít [prostředí Azure PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md) nebo šablony pro [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) nebo [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
- Pokud chcete použít Azure Disk Encryption po zřízení jinou příponu, můžete použít [nastavení posloupnosti rozšíření](virtual-machine-scale-sets-extension-sequencing.md). Můžete použít [tyto ukázky](../security/azure-security-disk-encryption-extension-sequencing.md#sample-azure-templates) začít.
- Příklad souboru začátku do konce dávky pro Linux škálovací sady dat šifrování disku najdete [tady](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Tento příklad vytvoří skupinu prostředků, škálovací sady pro Linux, připojí disk 5 GB dat a šifruje škálovací sadu virtuálních počítačů.
