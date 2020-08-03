---
title: Vytvoření a šifrování virtuálního počítače s Linuxem s využitím Azure CLI
description: V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového řádku Azure vytvořit a zašifrovat virtuální počítač se systémem Linux.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: b07ddd3ec98e2094eb3a463501f42d65e15370e6
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502354"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Rychlý Start: vytvoření a šifrování virtuálního počítače se systémem Linux pomocí Azure CLI

Azure CLI slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového řádku Azure vytvořit a zašifrovat virtuální počítač (VM) pro Linux.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku Azure CLI místně, musíte mít spuštěnou verzi Azure CLI 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus* :

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create). Následující příklad vytvoří virtuální počítač s názvem *myVM*.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

Vytvoření virtuálního počítače a podpůrných prostředků trvá několik minut. Následující příklad ukazuje, že operace vytvoření virtuálního počítače byla úspěšná.

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Vytvoření Key Vault nakonfigurovaného pro šifrovací klíče

Azure Disk Encryption ukládá šifrovací klíč do Azure Key Vault. Vytvořte Key Vault pomocí [AZ datatrezor Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Pokud chcete povolit Key Vault ukládání šifrovacích klíčů, použijte parametr--Enabled-pro-Disk-Encryption.

> [!Important]
> Každý Trezor klíčů musí mít název, který je jedinečný v rámci Azure. V níže uvedených příkladech nahraďte <název a jedinečné úložiště klíčů> názvem, který zvolíte.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Zašifrovat virtuální počítač

Pomocí příkaz [AZ VM Encryption](/cli/azure/vm/encryption?view=azure-cli-latest)Zašifrujte virtuální počítač zadáním jedinečného názvu Key Vault parametru--Disk-Encryption-File trezor.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

Po okamžiku, kdy se proces vrátí, "požadavek na šifrování byl přijat. K monitorování průběhu použijte prosím příkaz show. Příkaz show je [AZ VM show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm show --name "myVM" -g "MyResourceGroup"
```

Pokud je povolené šifrování, ve vráceném výstupu se zobrazí následující:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, virtuálního počítače a Key Vault použít příkaz [AZ Group Delete](/cli/azure/group) . 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili virtuální počítač, vytvořili Key Vault, která byla povolená pro šifrovací klíče, a zašifroval virtuální počítač.  V dalším článku se dozvíte víc o dalších Azure Disk Encryption pro virtuální počítače se systémem Linux.

> [!div class="nextstepaction"]
> [Přehled Azure Disk Encryption](disk-encryption-overview.md)
