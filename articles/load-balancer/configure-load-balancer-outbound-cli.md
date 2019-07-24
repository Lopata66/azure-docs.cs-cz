---
title: Konfigurace vyrovnávání zatížení a odchozích pravidel pomocí Azure CLI
titlesuffix: Azure Load Balancer
description: Tento článek ukazuje postup při konfiguraci pravidla odchozí a vyrovnávání zatížení v Standard pro vyrovnávání zatížení pomocí Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 837df78ea76451c7dc5e16efde0e90b780b6ee50
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275707"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Konfigurace vyrovnávání zatížení i pravidla odchozího v Load balanceru úrovně Standard pomocí Azure CLI

Tento rychlý start ukazuje, jak nakonfigurovat odchozí pravidla v Load balanceru úrovně Standard pomocí Azure CLI.  

Jakmile budete hotovi, prostředek nástroje pro vyrovnávání zatížení obsahuje dva front-endů a přidružená pravidla: jeden pro příchozí a druhý pro odchozí.  Každá front-endu obsahuje odkaz na veřejné IP adresy a tento scénář používá jinou veřejnou IP adresu pro příchozí a odchozí.   Pravidlo Vyrovnávání zatížení poskytuje Vyrovnávání zatížení pouze příchozí a odchozí pravidlo určuje odchozí NAT pro virtuální počítač k dispozici.  Tento rychlý Start používá dva samostatné back-end fondy, jeden pro příchozí a jeden pro odchozí, pro ilustraci možností a umožňuje flexibilitu pro tento scénář.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.28 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myresourcegroupoutbound* v *eastus2* umístění:

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Vytvoření virtuální sítě
Vytvoření virtuální sítě s názvem *myvnetoutbound* s podsítí *mysubnetoutbound* v *myresourcegroupoutbound* pomocí [az network vnet vytvoření](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Vytvořte příchozí veřejná IP adresa 

Pokud chcete mít k webové aplikaci přístup přes internet, potřebujete pro nástroj pro vyrovnávání zatížení veřejnou IP adresu. Load Balancer úrovně Standard podporuje pouze standardní veřejné IP adresy. Použití [az network public-ip vytvořit](https://docs.microsoft.com/cli/azure/network/public-ip) vytvořte standardní veřejnou IP adresu s názvem *mypublicipinbound* v *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Vytvoření výstupní veřejné IP adresy 

Vytvořit standardní IP adresu pro nástroje Load Balancer front-endová konfigurace odchozího pomocí [az network public-ip vytvořit](https://docs.microsoft.com/cli/azure/network/public-ip).

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení Azure

Tato část podrobně popisuje vytvoření a konfiguraci následujících komponent nástroje pro vyrovnávání zatížení:
  - IP adresa front-endu, který přijímá příchozí síťový provoz do nástroje pro vyrovnávání zatížení.
  - Back-end fond, ve kterém front-end IP odesílá síťový provoz s vyrovnáváním zatížení.
  - Back-end fond pro odchozí připojení. 
  - Sonda stavu, která určuje stav back-endových instancí virtuálních počítačů.
  - Příchozí pravidlo služby load balancer, který definuje způsob distribuce provozu do virtuálních počítačů.
  - Odchozí pravidlo služby load balancer, který definuje způsob distribuce provozu z virtuálních počítačů.

### <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

Vytvořte Load Balancer s příchozí IP adresou pomocí [AZ Network](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) Create s názvem  disendy, který zahrnuje příchozí IP adresu front-endu a back-end *bepoolinbound* fondu, který je přidružený k veřejné IP adrese *. mypublicipinbound* , kterou jste vytvořili v předchozím kroku.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>Vytvořit odchozí fond

Vytvořte další fond back-end adres pro definování odchozího připojení pro fond virtuálních počítačů pomocí [AZ Network disendu Address-Pool Create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) s názvem *bepooloutbound*.  Vytvoření samostatného odchozího fondu poskytuje maximální flexibilitu, ale tento krok můžete vynechat a zároveň použít jenom příchozí *bepoolinbound* .

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Vytvoření front-endu odchozí IP adresy
Vytvořte konfiguraci IP adresy odchozí front-endu nástroje pro vyrovnávání zatížení s [az sítě lb frontend-ip vytvořit](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) , který obsahuje a výstupních front-endové konfigurace IP adresy s názvem *myfrontendoutbound* , který je přidružený k veřejné IP adresy *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Vytvoření sondy stavu

Test stavu kontroluje všechny instance virtuálních počítačů a ověřuje, že mohou posílat síťový provoz. Instance virtuálního počítače, u níž se kontroly testu nezdaří, se odebere z nástroje pro vyrovnávání zatížení do té doby, než znovu přejde do režimu online a kontrola testu určí, že je v pořádku. Pomocí příkazu [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) vytvořte sondu stavu pro monitorování stavu virtuálních počítačů. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Vytvořit pravidlo Vyrovnávání zatížení

Pravidlo nástroje pro vyrovnávání zatížení definuje konfiguraci front-endová IP adres pro příchozí provoz a back-endový fond adres pro příjem provozu a také požadovaný zdrojový a cílový port. Vytvořte pravidlo nástroje pro vyrovnávání zatížení *myinboundlbrule* s [az network lb pravidlo vytvořte](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) pro naslouchání na portu 80 ve front-endový fond *myfrontendinbound* a odesílání síťový provoz s vyrovnáváním zatížení do back-endový fond adres *bepool* rovněž na portu 80. 

>[!NOTE]
>Toto pravidlo Vyrovnávání zatížení zakáže automatické odchozí (S) NAT v důsledku toto pravidlo s parametrem--zakázat odchozí snat. Odchozí NAT se poskytuje jenom odchozí pravidlo.

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepoolinbound \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Vytvoření odchozí pravidla

Odchozí pravidlo definuje veřejnou IP adresu front-endu, reprezentovaný front-endu *myfrontendoutbound*, který se bude používat pro veškerý odchozí provoz NAT, jakož i back-endový fond, do které bude použito toto pravidlo.  Vytvořit pravidlo odchozí *myoutboundrule* pro odchozí síťové překlad všechny virtuální počítače (Konfigurace IP adresy NIC) ve *bepool* back-endový fond.  Níže uvedený příkaz také odchozí časový limit nečinnosti se změní z 4 až 15 minut a přiděluje 10000 SNAT porty místo 1024.  Kontrola [odchozí pravidla](https://aka.ms/lboutboundrules) další podrobnosti.

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepooloutbound
```

Pokud nechcete používat samostatný odchozí fond, můžete místo toho v předchozím příkazu změnit argument fondu adres, aby bylo možné zadat *bepoolinbound* .  Pro zajištění flexibility a čitelnosti výsledné konfigurace doporučujeme použít samostatné fondy.

V tuto chvíli můžete pokračovat přidáním svého virtuálního počítače do back-end fondu *bepoolinbound* __a__ *bepooloutbound* tak, že aktualizujete konfiguraci IP adres příslušných prostředků nic pomocí [AZ Network nic IP-config Address-Pool Add](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, nástroje pro vyrovnávání zatížení a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Další postup
V tomto článku se vytvoří Load balanceru úrovně Standard, konfiguraci provozu pravidla nástroje pro vyrovnávání zatížení příchozích, nakonfigurovat a sondu stavu pro virtuální počítače v back-endový fond. Další informace o službě Azure Load Balancer najdete v kurzech týkajících se služby Azure Load Balancer.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
