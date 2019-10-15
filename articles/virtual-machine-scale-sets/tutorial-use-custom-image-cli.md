---
title: Kurz – Použití vlastní image virtuálního počítače ve škálovací sadě pomocí Azure CLI | Microsoft Docs
description: Zjistěte, jak pomocí Azure CLI vytvořit vlastní image virtuálního počítače, kterou můžete použít k nasazení škálovací sady virtuálních počítačů.
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
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6368a36eaa2e6832f22a13f20bc35d66c4425b4b
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311574"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Kurz: Vytvoření a použití vlastní image pro škálovací sady virtuálních počítačů pomocí Azure CLI
Při vytváření škálovací sady zadáte image, která se použije při nasazení instancí virtuálních počítačů. Pokud chcete snížit počet úloh po nasazení instancí virtuálních počítačů, můžete použít vlastní image virtuálního počítače. Tato vlastní image virtuálního počítače obsahuje instalace a konfigurace všech požadovaných aplikací. Všechny instance virtuálních počítačů vytvořené ve škálovací sadě používají vlastní image virtuálního počítače a jsou připravené k obsluze provozu aplikace. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření a přizpůsobení virtuálního počítače
> * Zrušení zřízení a generalizace virtuálního počítače
> * Vytvoření vlastní image virtuálního počítače
> * Nasazení škálovací sady využívající vlastní image virtuálního počítače

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.29 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).


## <a name="create-and-configure-a-source-vm"></a>Vytvoření a konfigurace zdrojového virtuálního počítače

>[!NOTE]
> Tento kurz vás provede procesem vytvoření a použití image generalizovaného virtuálního počítače. Vytvoření škálovací sady z image specializovaného virtuálního počítače se nepodporuje.

Nejprve vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group) a pak vytvořte virtuální počítač pomocí příkazu [az vm create](/cli/azure/vm). Tento virtuální počítač se použije jako zdroj pro vlastní image virtuálního počítače. Následující příklad vytvoří virtuální počítač *myVM* ve skupině prostředků *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

Veřejná IP adresa virtuálního počítače se zobrazí ve výstupu příkazu [az vm create](/cli/azure/vm). Následujícím způsobem se připojte přes SSH k veřejné IP adrese virtuálního počítače:

```azurecli-interactive
ssh azureuser@<publicIpAddress>
```

Teď vlastní virtuální počítač přizpůsobte nainstalováním základního webového serveru. Instance virtuálního počítače po nasazení ve škálovací sadě bude obsahovat všechny požadované balíčky ke spuštění webové aplikace. Následujícím způsobem pomocí příkazu `apt-get` nainstalujte *NGINX*:

```bash
sudo apt-get install -y nginx
```

Posledním krokem přípravy virtuálního počítače pro použití jako vlastní image je zrušení jeho zřízení. Tento krok z virtuálního počítače odebere informace specifické pro počítač a umožní nasazení mnoha virtuálních počítačů z jedné image. Po zrušení zřízení virtuálního počítače se název hostitele resetuje na *localhost.localdomain*. Odstraní se také klíče hostitele SSH, konfigurace názvového serveru, kořenové heslo a zapůjčení DHCP uložená v mezipaměti.

Ke zrušení zřízení virtuálního počítače použijte agenta virtuálního počítače Azure (*waagent*). Agent virtuálního počítače Azure je nainstalovaný na všech virtuálních počítačích a slouží ke komunikaci s platformou Azure. Parametr `-force` říká agentovi, aby přijímal výzvy k resetování informací specifických pro počítač.

```bash
sudo waagent -deprovision+user -force
```

Ukončete připojení SSH k virtuálnímu počítači:

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Vytvoření vlastní image virtuálního počítače ze zdrojového virtuálního počítače
Zdrojový virtuální počítač je teď přizpůsobený a obsahuje nainstalovaný webový server NGINX. Teď vytvoříme vlastní image virtuálního počítače pro použití se škálovací sadou.

Abyste mohli vytvořit image, virtuální počítač musí být uvolněný. Uvolněte virtuální počítač pomocí příkazu [az vm deallocate](/cli//azure/vm). Pak pomocí příkazu [az vm generalize](/cli//azure/vm) nastavte virtuální počítač do generalizovaného stavu, aby platforma Azure věděla, že je virtuální počítač připravený k použití pro vlastní image. Image můžete vytvořit pouze z generalizovaného počítače:


```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

Uvolnění a generalizace virtuálního počítače může několik minut trvat.

Teď vytvořte image virtuálního počítače pomocí příkazu [az image create](/cli//azure/image). Následující příklad z vašeho virtuálního počítače vytvoří image *myImage*:

> ZNAČTE Pokud se jedná o jiné umístění skupiny prostředků a virtuálního počítače, můžete do následujících příkazů přidat parametr `--location` a určit umístění zdrojového virtuálního počítače, který se používá k vytvoření image. 

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Vytvoření škálovací sady z vlastní image virtuálního počítače
Vytvořte škálovací sadu pomocí příkazu [az vmss create](/cli/azure/vmss#az-vmss-create). Místo image platformy, jako je například *UbuntuLTS* nebo *CentOS*, zadejte název své vlastní image virtuálního počítače. Následující příklad vytvoří škálovací sadu *myScaleSet*, která používá vlastní image *myImage* z předchozího kroku:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

Vytvoření a konfigurace všech prostředků škálovací sady a virtuálních počítačů trvá několik minut.


## <a name="test-your-scale-set"></a>Test škálovací sady
Pokud chcete škálovací sadě povolit příjem provozu a ověřit správné fungování webového serveru, vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí příkazu [az network lb rule create](/cli/azure/network/lb/rule). Následující příklad vytvoří pravidlo *myLoadBalancerRuleWeb* povolující provoz na portu *TCP* *80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Pokud chcete vidět svou škálovací sadu v akci, získejte veřejnou IP adresu vašeho nástroje pro vyrovnávání zatížení pomocí příkazu [az network public-ip show](/cli/azure/network/public-ip). Následující příklad získá IP adresu *myScaleSetLBPublicIP* vytvořenou jako součást škálovací sady:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Zadejte veřejnou IP adresu do svého webového prohlížeče. Zobrazí se výchozí webová stránka serveru NGINX, jak je znázorněno v následujícím příkladu:

![Server NGINX spuštěný z vlastní image virtuálního počítače](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete odebrat škálovací sadu a další prostředky, odstraňte skupinu prostředků a všechny její prostředky pomocí příkazu [az group delete](/cli/azure/group). Parametr `--no-wait` vrátí řízení na příkazový řádek bez čekání na dokončení operace. Parametr `--yes` potvrdí, že chcete prostředky odstranit, aniž by se na to zobrazoval další dotaz.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak vytvořit a použít vlastní image virtuálního počítače pro škálovací sady pomocí Azure CLI:

> [!div class="checklist"]
> * Vytvoření a přizpůsobení virtuálního počítače
> * Zrušení zřízení a generalizace virtuálního počítače
> * Vytvoření vlastní image virtuálního počítače
> * Nasazení škálovací sady využívající vlastní image virtuálního počítače

V dalším kurzu se dozvíte, jak do škálovací sady nasadit aplikace.

> [!div class="nextstepaction"]
> [Nasazování aplikací do škálovacích sad](tutorial-install-apps-cli.md)
