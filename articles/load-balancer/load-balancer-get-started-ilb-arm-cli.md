---
title: Vytvoření interního základního Load Balancer – Azure CLI
titleSuffix: Azure Load Balancer
description: V tomto článku se dozvíte, jak vytvořit interní nástroj pro vyrovnávání zatížení pomocí Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/27/2018
ms.author: allensu
ms.openlocfilehash: 51df1936e5d8725b2243e7c0084973370139c540
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79457007"
---
# <a name="create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Vytvoření interního nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure CLI

V tomto článku se dozvíte, jak vytvořit interní nástroj pro vyrovnávání zatížení virtuálních počítačů. K otestování nástroje pro vyrovnávání zatížení nasadíte dva virtuální počítače se serverem Ubuntu, které budou vyrovnávat zatížení webové aplikace.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků *myResourceGroupILB* v umístění *eastus*:

```azurecli-interactive
  az group create \
    --name myResourceGroupILB \
    --location eastus
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Pomocí příkazu [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) vytvořte virtuální síť *myVnet* s podsítí *mySubnet* ve skupině prostředků *myResourceGroup*.

```azurecli-interactive
  az network vnet create \
    --name myVnet \
    --resource-group myResourceGroupILB \
    --location eastus \
    --subnet-name mySubnet
```

## <a name="create-basic-load-balancer"></a>Vytvoření Load Balanceru úrovně Basic

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:
  - Konfigurace front-endových IP adres, které přijímají příchozí síťový provoz do nástroje pro vyrovnávání zatížení.
  - Back-endový fond IP adres, kam front-endový fond odesílá síťový provoz s vyrovnáváním zatížení.
  - Sonda stavu, která určuje stav back-endových instancí virtuálních počítačů.
  - Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-the-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte interní Load Balancer pomocí [AZ Network](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) Create s názvem **myLoadBalancer** , který obsahuje konfiguraci protokolu IP front-endu s názvem **myFrontEnd**, fondu back-end s názvem **myBackEndPool** , který je přidružený k privátní IP adrese * * 10.0.0.7.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupILB \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEnd \
    --private-ip-address 10.0.0.7 \
    --backend-pool-name myBackEndPool \
    --vnet-name myVnet \
    --subnet mySubnet      
```

### <a name="create-the-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních počítačů a ověřuje, že můžou přijímat síťový provoz. Instance virtuálního počítače, u níž se kontroly testu nezdaří, se odebere z nástroje pro vyrovnávání zatížení do té doby, než znovu přejde do režimu online a kontrola testu určí, že je v pořádku. Pomocí příkazu [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) vytvořte sondu stavu pro monitorování stavu virtuálních počítačů. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje konfiguraci front-endových IP adres pro příchozí provoz, back-endový fond IP adres pro příjem provozu a také požadovaný zdrojový a cílový port. Pomocí příkazu [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) vytvořte pravidlo nástroje pro vyrovnávání zatížení *myHTTPRule* pro naslouchání na portu 80 ve front-endovém fondu *myFrontEnd* a odesílání síťového provozu s vyrovnáváním zatížení do back-endového fondu adres *myBackEndPool*, a to taky na portu 80. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="create-servers-for-the-backend-address-pool"></a>Vytvoření serverů pro back-endový fond adres

Než nasadíte několik virtuálních počítačů a budete moci otestovat svůj nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

### <a name="create-nics"></a>Vytvoření síťových rozhraní

Pomocí příkazu [az network nic create](/cli/azure/network/nic#az-network-nic-create) vytvořte dvě síťová rozhraní a přiřaďte je k privátní IP adrese. 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupILB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V tomto příkladu vytvoříte dva virtuální počítače, které se použijí jako servery back-end pro nástroj pro vyrovnávání zatížení. K ověření úspěšného vytvoření nástroje pro vyrovnávání zatížení také na virtuální počítače nainstalujete server NGINX.

### <a name="create-an-availability-set"></a>Vytvoření skupiny dostupnosti

Vytvořte skupinu dostupnosti pomocí příkazu [az vm availabilityset create](/cli/azure/network/nic).

```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupILB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>Vytvoření dvou virtuálních počítačů

K instalaci serveru NGINX a spuštění aplikace Hello World v Node.js na virtuálním počítači s Linuxem můžete použít konfigurační soubor cloud-init. V aktuálním prostředí vytvořte soubor cloud-init.txt a zkopírujte následující konfiguraci a vložte ji do prostředí. Ujistěte se, že správně kopírujete celý soubor cloud-init, zejména první řádek:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Vytvořte virtuální počítače pomocí příkazu [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupILB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    done
```

Nasazení virtuálních počítačů může několik minut trvat.

### <a name="create-a-vm-for-testing-the-load-balancer"></a>Vytvoření virtuálního počítače pro otestování nástroje pro vyrovnávání zatížení

Pokud chcete nástroj pro vyrovnávání zatížení otestovat, vytvořte virtuální počítač *myVMTest* a přidružte ho k síťovému rozhraní *myNic3*.

```azurecli-interactive
 az vm create \
    --resource-group myResourceGroupILB \
    --name myVMTest \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword123456!
```

## <a name="test-the-internal-load-balancer"></a>Test interního nástroje pro vyrovnávání zatížení

Abyste mohli nástroj pro vyrovnávání zatížení otestovat, musíte nejprve získat jeho privátní IP adresu. Pak se přihlaste k virtuálnímu počítači myVMTest a na něm zadejte privátní IP adresu do adresního řádku webového prohlížeče.

Privátní IP adresu nástroje pro vyrovnávání zatížení získáte pomocí příkazu [az network lb show](/cli/azure/network/public-ip). Zkopírujte privátní IP adresu a vložte ji do adresního řádku webového prohlížeče na svém virtuálním počítači *myVMTest*.

```azurecli-interactive
  az network lb show \
    --name myLoadBalancer \
    --resource-group myResourceGroupILB
```

![Test nástroje pro vyrovnávání zatížení](./media/load-balancer-get-started-ilb-arm-cli/load-balancer-test.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
  az group delete --name myResourceGroupILB
```


## <a name="next-steps"></a>Další kroky
V rámci tohoto článku jste vytvořili interní Load Balancer úrovně Basic, připojili jste k němu virtuální počítače, nakonfigurovali jste pravidlo provozu nástroje pro vyrovnávání zatížení a sondu stavu a pak jste nástroj pro vyrovnávání zatížení otestovali. Další informace o nástrojích pro vyrovnávání zatížení a jejich souvisejících prostředcích najdete v dalších článcích s postupy.
