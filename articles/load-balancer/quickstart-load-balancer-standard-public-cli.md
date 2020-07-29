---
title: 'Rychlý Start: vytvoření veřejného nástroje pro vyrovnávání zatížení – Azure CLI'
titleSuffix: Azure Load Balancer
description: Tento rychlý start ukazuje, jak vytvořit veřejný nástroj pro vyrovnávání zatížení pomocí rozhraní Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: aa0d29c5c2a4cf8eebbf530b42a25d8924e031bc
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290273"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-cli"></a>Rychlý start: Vytvoření veřejného nástroje pro vyrovnávání zatížení virtuálních počítačů pomocí Azure CLI

Začínáme s Azure Load Balancer pomocí rozhraní příkazového řádku Azure k vytvoření veřejného nástroje pro vyrovnávání zatížení a tří virtuálních počítačů.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Rozhraní příkazového řádku Azure je nainstalované místně nebo Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte použít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Vytvořte skupinu prostředků pomocí [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create):

* S názvem **myResourceGroupLB**. 
* V umístění **eastus** .

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```
---

# <a name="option-1-default-create-a-load-balancer-standard-sku"></a>[Možnost 1 (výchozí): Vytvoření nástroje pro vyrovnávání zatížení (standardní SKU)](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU. Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.


## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pokud chcete mít k webové aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. 

Použijte [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) to:

* Vytvořte záložní veřejnou IP adresu zóny Standard s názvem **myPublicIP**.
* V **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard
```

Vytvoření redundantní veřejné IP adresy v Zóna 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Standard \
    --zone 1
```

## <a name="create-standard-load-balancer"></a>Vytvořit službu Load Balancer úrovně Standard

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:

  * Fond IP adres front-endu, který přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
  * Back-end fond IP adres, ve kterém front-endu odesílá síťový provoz s vyrovnáváním zatížení.
  * Sonda stavu, která určuje stav instancí virtuálních počítačů back-endu.
  * Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-the-load-balancer-resource"></a>Vytvoření prostředku nástroje pro vyrovnávání zatížení

Vytvoření veřejného nástroje pro vyrovnávání zatížení pomocí [AZ Network](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create)diskont Create:

* S názvem **myLoadBalancer**.
* Front-endového fondu s názvem **myFrontEnd**.
* Back-end fond s názvem **myBackEndPool**.
* Přidruženo k **myPublicIP** veřejné IP adresy, kterou jste vytvořili v předchozím kroku. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních počítačů, aby bylo zajištěno, že budou moci odesílat síťový provoz. 

Z nástroje pro vyrovnávání zatížení se odebere virtuální počítač s neúspěšnou kontrolou testu. Po vyřešení chyby se virtuální počítač do nástroje pro vyrovnávání zatížení přidá zpátky.

Vytvořte sondu stavu pomocí [AZ Network disprobe test Create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Monitoruje stav virtuálních počítačů.
* S názvem **myHealthProbe**.
* Protokol **TCP**.
* **Port monitorování 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje:

* Konfigurace IP adresy front-endu pro příchozí provoz.
* Fond IP adres back-endu pro příjem provozu.
* Požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [AZ Network diskont Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* Pojmenovaný **myHTTPRule**
* Naslouchat na **portu 80** ve fondu front-endu **myFrontEnd**.
* Odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres **myBackEndPool** pomocí **portu 80**. 
* Pomocí **myHealthProbe**sondy stavu.
* Protokol **TCP**.
* Povolte překlad odchozích adres zdrojové sítě (SNAT) pomocí IP adresy front-endu.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true 
```

## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě

Než nasadíte virtuální počítače a otestujete Nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [AZ Network VNet Create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* S názvem **myVNet**.
* Podsíť s názvem **myBackendSubnet**.
* Ve skupině prostředků **myResourceGroupLB** .
* Umístění **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --subnet-name myBackendSubnet
```

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

U standardního nástroje pro vyrovnávání zatížení musí být virtuální počítače na back-endové adrese k dispozici pro síťová rozhraní, která patří do skupiny zabezpečení sítě. 

Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* S názvem **myNSG**.
* Ve skupině prostředků **myResourceGroupLB**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

Vytvořte pravidlo skupiny zabezpečení sítě pomocí [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* S názvem **myNSGRuleHTTP**.
* Ve skupině zabezpečení sítě, kterou jste vytvořili v předchozím kroku, **myNSG**.
* Ve skupině prostředků **myResourceGroupLB**.
* Protokol **TCP**.
* Směr **příchozí**.
* Zdroj **(*)**.
* Cíl **(*)**.
* Cílový port portu **80**.
* Přístup **Povolte**.
* Priorita **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Vytvoření síťových rozhraní pro virtuální počítače

Vytvořte tři síťová rozhraní pomocí [AZ Network nic Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* S názvem **myNicVM1**.
* Ve skupině prostředků **myResourceGroupLB**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet**podsíti.
* Ve skupině zabezpečení sítě **myNSG**.
* Připojeno k **myLoadBalancer** nástroje pro vyrovnávání zatížení v **myBackEndPool**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```
#### <a name="vm2"></a>VM2

* S názvem **myNicVM2**.
* Ve skupině prostředků **myResourceGroupLB**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet**podsíti.
* Ve skupině zabezpečení sítě **myNSG**.
* Připojeno k **myLoadBalancer** nástroje pro vyrovnávání zatížení v **myBackEndPool**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```
#### <a name="vm3"></a>VM3

* S názvem **myNicVM3**.
* Ve skupině prostředků **myResourceGroupLB**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet**podsíti.
* Ve skupině zabezpečení sítě **myNSG**.
* Připojeno k **myLoadBalancer** nástroje pro vyrovnávání zatížení v **myBackEndPool**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvoříte:

* Konfigurační soubor cloudu s názvem **cloud-init.txt** pro konfiguraci serveru.
* Tři virtuální počítače, které se mají použít jako servery back-end pro nástroj pro vyrovnávání zatížení.

### <a name="create-cloud-init-configuration-file"></a>Vytvoření konfiguračního souboru Cloud-init

K instalaci NGINX a spuštění aplikace Node.js Hello World na virtuálním počítači se systémem Linux použijte konfigurační soubor Cloud-init. 

V aktuální prostředí vytvořte soubor s názvem cloud-init.txt. Zkopírujte a vložte do prostředí následující konfiguraci. Ujistěte se, že jste správně zkopírovali celý soubor Cloud-init, zejména první řádek:

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
### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte virtuální počítače pomocí [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* S názvem **myVM1**.
* Ve skupině prostředků **myResourceGroupLB**.
* Připojeno k síťovému rozhraní **myNicVM1**.
* **UbuntuLTS**image virtuálního počítače.
* Konfigurační soubor **cloud-init.txt** , který jste vytvořili v kroku výše.
* V **zóna 1**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* S názvem **myVM2**.
* Ve skupině prostředků **myResourceGroupLB**.
* Připojeno k síťovému rozhraní **myNicVM2**.
* **UbuntuLTS**image virtuálního počítače.
* Konfigurační soubor **cloud-init.txt** , který jste vytvořili v kroku výše.
* V **zóna 2**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

#### <a name="vm3"></a>VM3
* S názvem **myVM3**.
* Ve skupině prostředků **myResourceGroupLB**.
* Připojeno k síťovému rozhraní **myNicVM3**.
* **UbuntuLTS**image virtuálního počítače.
* Konfigurační soubor **cloud-init.txt** , který jste vytvořili v kroku výše.
* V **zóna 3**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 3 \
    --no-wait
```
Nasazení virtuálních počítačů může trvat několik minut.

## <a name="create-outbound-rule-configuration"></a>Vytvořit konfiguraci odchozího pravidla
Odchozí pravidla nástroje pro vyrovnávání zatížení konfigurují odchozí SNAT pro virtuální počítače ve fondu back-endu. 

Další informace o odchozích připojeních najdete v tématu [odchozí připojení v Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-public-ip-address-or-public-ip-prefix"></a>Vytvořte odchozí veřejnou IP adresu nebo předponu veřejné IP adresy.

Pomocí [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) vytvořte jednu IP adresu pro odchozí připojení.  

Pomocí [AZ Network Public-IP prefix Create](https://docs.microsoft.com/cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-create) vytvořte předponu veřejné IP adresy pro odchozí připojení.

Další informace o škálování odchozího NAT a odchozího připojení najdete v tématu [škálování odchozího překladu adres (NAT) s několika IP adresami](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#scale).

#### <a name="public-ip"></a>Veřejná IP adresa

* S názvem **myPublicIPOutbound**.
* V **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard
```

Vytvoření redundantní veřejné IP adresy v Zóna 1:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIPOutbound \
    --sku Standard \
    --zone 1
```
#### <a name="public-ip-prefix"></a>Předpona veřejné IP adresy

* S názvem **myPublicIPPrefixOutbound**.
* V **myResourceGroupLB**.
* Délka předpony **28**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28
```
Postup při vytváření redundantní předpony veřejné IP adresy pro oblast v Zóna 1:

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupLB \
    --name myPublicIPPrefixOutbound \
    --length 28 \
    --zone 1
```

### <a name="create-outbound-frontend-ip-configuration"></a>Vytvoření odchozí konfigurace IP adresy front-endu

Vytvořte novou konfiguraci IP adresy front-endu pomocí [AZ Network disendu-IP Create ](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create):

Vyberte příkazy veřejné IP adresy nebo předpony veřejných IP adres na základě rozhodnutí v předchozím kroku.

#### <a name="public-ip"></a>Veřejná IP adresa

* S názvem **myFrontEndOutbound**.
* Ve skupině prostředků **myResourceGroupLB**.
* Přidruženo k veřejné IP adrese **myPublicIPOutbound**.
* Přidruženo k **myLoadBalancer**nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIPOutbound 
```

#### <a name="public-ip-prefix"></a>Předpona veřejné IP adresy

* S názvem **myFrontEndOutbound**.
* Ve skupině prostředků **myResourceGroupLB**.
* Přidruženo k **myPublicIPPrefixOutbound**předpony veřejných IP adres.
* Přidruženo k **myLoadBalancer**nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myResourceGroupLB \
    --name myFrontEndOutbound \
    --lb-name myLoadBalancer \
    --public-ip-prefix myPublicIPPrefixOutbound 
```

### <a name="create-outbound-pool"></a>Vytvořit odchozí fond

Vytvořte nový odchozí fond pomocí [AZ Network Address-Pool Create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create):

* S názvem **myBackEndPoolOutbound**.
* Ve skupině prostředků **myResourceGroupLB**.
* Přidruženo k **myLoadBalancer**nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myBackendPoolOutbound
```
### <a name="create-outbound-rule"></a>Vytvořit odchozí pravidlo

Vytvořte nové odchozí pravidlo pro odchozí back-end fond pomocí [AZ Network Outbound-rule Create](https://docs.microsoft.com/cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-create):

* S názvem **myOutboundRule**.
* Ve skupině prostředků **myResourceGroupLB**.
* Přidruženo k **myLoadBalancer** nástroje pro vyrovnávání zatížení
* Přidruženo k front-endu **myFrontEndOutbound**.
* Protokol **vše**.
* Časový limit nečinnosti **15**.
* porty **10000** odchozí.
* Přidruženo k **myBackEndPoolOutbound**fondu back-endu.

```azurecli-interactive
  az network lb outbound-rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myOutboundRule \
    --frontend-ip-configs myFrontEndOutbound \
    --protocol All \
    --idle-timeout 15 \
    --outbound-ports 10000 \
    --address-pool myBackEndPoolOutbound
```
### <a name="add-virtual-machines-to-outbound-pool"></a>Přidat virtuální počítače do odchozího fondu

Přidejte virtuální počítače do odchozího fondu pomocí [AZ Network nic IP-config Address-Pool Add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* Ve fondu back-end adres **myBackEndPoolOutbound**.
* Ve skupině prostředků **myResourceGroupLB**.
* Přidruženo k síťovému rozhraní **myNicVM1** a **ipconfig1**.
* Přidruženo k **myLoadBalancer**nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* Ve fondu back-end adres **myBackEndPoolOutbound**.
* Ve skupině prostředků **myResourceGroupLB**.
* Přidruženo k síťovému rozhraní **myNicVM2** a **ipconfig1**.
* Přidruženo k **myLoadBalancer**nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm3"></a>VM3
* Ve fondu back-end adres **myBackEndPoolOutbound**.
* Ve skupině prostředků **myResourceGroupLB**.
* Přidruženo k síťovému rozhraní **myNicVM3** a **ipconfig1**.
* Přidruženo k **myLoadBalancer**nástroje pro vyrovnávání zatížení.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPoolOutbound \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM3 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

# <a name="option-2-create-a-load-balancer-basic-sku"></a>[Možnost 2: Vytvoření nástroje pro vyrovnávání zatížení (základní skladová položka)](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Pro produkční úlohy se doporučuje používat nástroj pro vyrovnávání zatížení Standard SKU. Další informace o SKU najdete v tématu **[Azure Load Balancer SKU](skus.md)**.


## <a name="create-a-public-ip-address"></a>Vytvoření veřejné IP adresy

Pokud chcete mít k webové aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. 

Použijte [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) to:

* Vytvořte záložní veřejnou IP adresu zóny Standard s názvem **myPublicIP**.
* V **myResourceGroupLB**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --sku Basic
```

## <a name="create-basic-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Basic

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:

  * Fond IP adres front-endu, který přijímá příchozí síťový provoz v nástroji pro vyrovnávání zatížení.
  * Back-end fond IP adres, ve kterém front-endu odesílá síťový provoz s vyrovnáváním zatížení.
  * Sonda stavu, která určuje stav instancí virtuálních počítačů back-endu.
  * Pravidlo nástroje pro vyrovnávání zatížení, které definuje způsob distribuce provozu do virtuálních počítačů.

### <a name="create-the-load-balancer-resource"></a>Vytvoření prostředku nástroje pro vyrovnávání zatížení

Vytvoření veřejného nástroje pro vyrovnávání zatížení pomocí [AZ Network](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create)diskont Create:

* S názvem **myLoadBalancer**.
* Front-endového fondu s názvem **myFrontEnd**.
* Back-end fond s názvem **myBackEndPool**.
* Přidruženo k **myPublicIP** veřejné IP adresy, kterou jste vytvořili v předchozím kroku. 

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Basic \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Vytvoření sondy stavu

Sonda stavu kontroluje všechny instance virtuálních počítačů, aby bylo zajištěno, že budou moci odesílat síťový provoz. 

Z nástroje pro vyrovnávání zatížení se odebere virtuální počítač s neúspěšnou kontrolou testu. Po vyřešení chyby se virtuální počítač do nástroje pro vyrovnávání zatížení přidá zpátky.

Vytvořte sondu stavu pomocí [AZ Network disprobe test Create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Monitoruje stav virtuálních počítačů.
* S názvem **myHealthProbe**.
* Protokol **TCP**.
* **Port monitorování 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Vytvoření pravidla nástroje pro vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje:

* Konfigurace IP adresy front-endu pro příchozí provoz.
* Fond IP adres back-endu pro příjem provozu.
* Požadovaný zdrojový a cílový port. 

Vytvořte pravidlo nástroje pro vyrovnávání zatížení pomocí [AZ Network diskont Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* Pojmenovaný **myHTTPRule**
* Naslouchat na **portu 80** ve fondu front-endu **myFrontEnd**.
* Odesílání síťového provozu s vyrovnáváním zatížení do fondu back-end adres **myBackEndPool** pomocí **portu 80**. 
* Pomocí **myHealthProbe**sondy stavu.
* Protokol **TCP**.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>Konfigurace virtuální sítě

Než nasadíte virtuální počítače a otestujete Nástroj pro vyrovnávání zatížení, vytvořte podpůrné prostředky virtuální sítě.

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [AZ Network VNet Create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* S názvem **myVNet**.
* Podsíť s názvem **myBackendSubnet**.
* Ve skupině prostředků **myResourceGroupLB** .
* Umístění **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --subnet-name myBackendSubnet
```

### <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

U standardního nástroje pro vyrovnávání zatížení musí být virtuální počítače na back-endové adrese k dispozici pro síťová rozhraní, která patří do skupiny zabezpečení sítě. 

Vytvořte skupinu zabezpečení sítě pomocí [AZ Network NSG Create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* S názvem **myNSG**.
* Ve skupině prostředků **myResourceGroupLB**.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

Vytvořte pravidlo skupiny zabezpečení sítě pomocí [AZ Network NSG Rule Create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* S názvem **myNSGRuleHTTP**.
* Ve skupině zabezpečení sítě, kterou jste vytvořili v předchozím kroku, **myNSG**.
* Ve skupině prostředků **myResourceGroupLB**.
* Protokol **TCP**.
* Směr **příchozí**.
* Zdroj **(*)**.
* Cíl **(*)**.
* Cílový port portu **80**.
* Přístup **Povolte**.
* Priorita **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Vytvoření síťových rozhraní pro virtuální počítače

Vytvořte tři síťová rozhraní pomocí [AZ Network nic Create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* S názvem **myNicVM1**.
* Ve skupině prostředků **myResourceGroupLB**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet**podsíti.
* Ve skupině zabezpečení sítě **myNSG**.
* Připojeno k **myLoadBalancer** nástroje pro vyrovnávání zatížení v **myBackEndPool**.

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```
#### <a name="vm2"></a>VM2

* S názvem **myNicVM2**.
* Ve skupině prostředků **myResourceGroupLB**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet**podsíti.
* Ve skupině zabezpečení sítě **myNSG**.
* Připojeno k **myLoadBalancer** nástroje pro vyrovnávání zatížení v **myBackEndPool**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```
#### <a name="vm3"></a>VM3

* S názvem **myNicVM3**.
* Ve skupině prostředků **myResourceGroupLB**.
* Ve virtuální síti **myVNet**.
* V **myBackendSubnet**podsíti.
* Ve skupině zabezpečení sítě **myNSG**.
* Připojeno k **myLoadBalancer** nástroje pro vyrovnávání zatížení v **myBackEndPool**.

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
```

## <a name="create-backend-servers"></a>Vytvoření serverů back-end

V této části vytvoříte:

* Konfigurační soubor cloudu s názvem **cloud-init.txt** pro konfiguraci serveru. 
* Skupina dostupnosti pro virtuální počítače
* Tři virtuální počítače, které se mají použít jako servery back-end pro nástroj pro vyrovnávání zatížení.


Chcete-li ověřit, zda byl nástroj pro vyrovnávání zatížení úspěšně vytvořen, nainstalujte NGINX na virtuální počítače.

### <a name="create-cloud-init-configuration-file"></a>Vytvoření konfiguračního souboru Cloud-init

K instalaci NGINX a spuštění aplikace Node.js Hello World na virtuálním počítači se systémem Linux použijte konfigurační soubor Cloud-init. 

V aktuální prostředí vytvořte soubor s názvem cloud-init.txt. Zkopírujte a vložte do prostředí následující konfiguraci. Ujistěte se, že jste správně zkopírovali celý soubor Cloud-init, zejména první řádek:

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
### <a name="create-availability-set-for-virtual-machines"></a>Vytvořit skupinu dostupnosti pro virtuální počítače

Vytvořte skupinu dostupnosti pomocí [AZ VM Availability-set Create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create):

* S názvem **myAvSet**.
* Ve skupině prostředků **myResourceGroupLB**.
* Umístění **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group myResourceGroupLB \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Vytvořte virtuální počítače pomocí [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* S názvem **myVM1**.
* Ve skupině prostředků **myResourceGroupLB**.
* Připojeno k síťovému rozhraní **myNicVM1**.
* **UbuntuLTS**image virtuálního počítače.
* Konfigurační soubor **cloud-init.txt** , který jste vytvořili v kroku výše.
* V **myAvSet**skupině dostupnosti.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* S názvem **myVM2**.
* Ve skupině prostředků **myResourceGroupLB**.
* Připojeno k síťovému rozhraní **myNicVM2**.
* **UbuntuLTS**image virtuálního počítače.
* Konfigurační soubor **cloud-init.txt** , který jste vytvořili v kroku výše.
* V **zóna 2**.

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

#### <a name="vm3"></a>VM3
* S názvem **myVM3**.
* Ve skupině prostředků **myResourceGroupLB**.
* Připojeno k síťovému rozhraní **myNicVM3**.
* **UbuntuLTS**image virtuálního počítače.
* Konfigurační soubor **cloud-init.txt** , který jste vytvořili v kroku výše.
* V **zóna 3**.

```azurecli-interactive
   az vm create \
    --resource-group myResourceGroupLB \
    --name myVM3 \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```
Nasazení virtuálních počítačů může trvat několik minut.

---

## <a name="test-the-load-balancer"></a>Testování Load Balanceru

K získání veřejné IP adresy nástroje pro vyrovnávání zatížení použijte příkaz [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show). 

Zkopírujte veřejnou IP adresu a pak ji vložte do adresního řádku svého prohlížeče.

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```
:::image type="content" source="./media/load-balancer-standard-public-cli/running-nodejs-app.png" alt-text="Testování Load Balanceru" border="true":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odeberte skupinu prostředků, nástroj pro vyrovnávání zatížení a všechny související prostředky pomocí příkazu [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) .

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Další kroky
V tomto rychlém startu

* Vytvořili jste standardní nebo veřejný Nástroj pro vyrovnávání zatížení.
* Připojené virtuální počítače. 
* Nakonfigurovali jste pravidlo provozu nástroje pro vyrovnávání zatížení a sondu stavu.
* Otestování nástroje pro vyrovnávání zatížení.

Chcete-li získat další informace o Azure Load Balancer, pokračujte v [Azure Load Balancer?](load-balancer-overview.md) a [Load Balancer Nejčastější dotazy](load-balancer-faqs.md).

Přečtěte si další informace o [Load Balancer a zónách dostupnosti](load-balancer-standard-availability-zones.md).