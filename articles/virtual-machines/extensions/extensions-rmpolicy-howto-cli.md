---
title: K omezení instalace rozšíření virtuálního počítače pomocí služby Azure Policy | Dokumentace Microsoftu
description: Chcete-li omezit nasazení rozšíření virtuálních počítačů pomocí služby Azure Policy.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: 1f71276c25e3ec1e5791d9b35f89aa95190c6afd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543253"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>K omezení instalace rozšíření na virtuální počítače s Linuxem pomocí služby Azure Policy

Pokud chcete zabránit použití nebo instalace některých rozšíření virtuálních počítačů s Linuxem, můžete vytvořit zásady služby Azure pomocí rozhraní příkazového řádku k omezení rozšíření pro virtuální počítače v rámci skupiny prostředků. 

Tento kurz používá rozhraní příkazového řádku ve službě Azure Cloud Shell, která se neustále aktualizuje na nejnovější verzi. Pokud chcete použít Azure CLI místně, musíte nainstalovat verzi 2.0.26 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Vytvoření souboru pravidel

Pokud chcete omezit, jaká rozšíření je možné nainstalovat, je potřeba mít [pravidlo](../../governance/policy/concepts/definition-structure.md#policy-rule) k poskytování logiky pro identifikaci rozšíření.

Tento příklad ukazuje, jak zakázat instalaci rozšíření publikovaná "Microsoft.OSTCExtensions" tak, že vytvoříte soubor pravidel ve službě Azure Cloud Shell, ale při práci v rozhraní příkazového řádku místně, můžete také vytvořit místního souboru a cesty (~/clouddrive) nahraďte cestou k místní soubor na svůj počítač.

V [bash Cloud Shell](https://shell.azure.com/bash), typ:

```azurecli-interactive 
vim ~/clouddrive/azurepolicy.rules.json
```

Zkopírujte a vložte následující .json do souboru.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Jakmile budete hotovi, klikněte **Esc** klíče a pak zadejte **: QW** uložte a zavřete soubor.


## <a name="create-a-parameters-file"></a>Vytvoření souboru parametrů

Budete také potřebovat [parametry](../../governance/policy/concepts/definition-structure.md#parameters) soubor, který vytvoří strukturu, můžete použít k předání v seznamu rozšíření blokování. 

Tento příklad ukazuje, jak vytvořit soubor parametrů pro virtuální počítače s Linuxem ve službě Cloud Shell, ale při práci v rozhraní příkazového řádku místně, můžete také vytvořit místního souboru a cestu (~/clouddrive) nahraďte cestou do místního souboru na vašem počítači.

V [bash Cloud Shell](https://shell.azure.com/bash), typ:

```azurecli-interactive
vim ~/clouddrive/azurepolicy.parameters.json
```

Zkopírujte a vložte následující .json do souboru.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

Jakmile budete hotovi, klikněte **Esc** klíče a pak zadejte **: QW** uložte a zavřete soubor.

## <a name="create-the-policy"></a>Vytvoření zásad

Definice zásad není objekt použitý k uložení konfigurace, který chcete použít. Definice zásady používá k definování zásady souborů pravidel a parametry. Vytvořit definici zásady pomocí [vytvoření definice zásady az](/cli/azure/role/assignment?view=azure-cli-latest).

V tomto příkladu jsou pravidla a parametry soubory vytvořeny a uloženy jako soubory .json ve službě cloud shell.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Přiřazení zásad

Tento příklad přiřadí zásady skupiny prostředků pomocí [vytvoření přiřazení zásady az](/cli/azure/policy/assignment). Jakýkoli virtuální počítač vytvořený v **myResourceGroup** skupinu prostředků, nebude možné nainstalovat přístup k virtuálnímu počítači Linux nebo rozšíření vlastních skriptů pro Linux. Skupina prostředků musí existovat, abyste mohli přiřadit zásady.

Použít [seznam účtů az](/cli/azure/account?view=azure-cli-latest) získat ID vašeho předplatného oznamujícím v příkladu.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>Testování zásad

Zásady testování vytvořením nového virtuálního počítače a chcete přidat nového uživatele.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Při pokusu o vytvoření nového uživatele s názvem **myNewUser** pomocí rozšíření přístupu virtuálních počítačů.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Odebrat přiřazení

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Odebrání zásady

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [Azure Policy](../../governance/policy/overview.md).