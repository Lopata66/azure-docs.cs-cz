---
title: ZASTARALÉ Rychlý Start – cluster Azure Docker CE pro Linux
description: Rychle se naučíte, jak pomocí Azure CLI vytvořit cluster Docker CE pro kontejnery Linuxu ve službě Azure Container Service.
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: iainfou
ms.custom: ''
ms.openlocfilehash: 5f492dd2bd270d3f067c05c1dc2235d54e481847
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274880"
---
# <a name="deprecated-deploy-docker-ce-cluster"></a>ZASTARALÉ Nasazení clusteru Docker CE

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto rychlém startu se nasadí cluster Docker CE pomocí Azure CLI. Následně se na tomto clusteru nasadí a spustí vícekontejnerová aplikace skládající se z webu front-end a instance Redis. Po dokončení bude aplikace přístupná přes internet.

Docker CE ve službě Azure Container Service je ve verzi Preview a **neměl by se používat pro produkční úlohy**.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít rozhraní příkazového řádku Azure ve verzi 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az-group-create). Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure.

Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *westus2* .

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Výstup:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "westus2",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-docker-swarm-cluster"></a>Vytvoření clusteru Docker Swarm

Vytvořte cluster Docker CE ve službě Azure Container Service pomocí příkazu [az acs create](/cli/azure/acs#az-acs-create). Informace o oblasti availaiblity Docker CE najdete v tématu [oblasti ACS pro Docker CE](https://github.com/Azure/ACS/blob/master/announcements/2017-08-04_additional_regions.md) .

Následující příklad vytvoří cluster *mySwarmCluster* s jedním hlavním linuxovým uzlem a třemi agentskými linuxovými uzly.

```azurecli-interactive
az acs create --name mySwarmCluster --orchestrator-type dockerce --resource-group myResourceGroup --generate-ssh-keys
```

V některých případech, například s omezenou zkušební verzí, má předplatné Azure omezený přístup k prostředkům Azure. Pokud se nasazení nezdaří kvůli omezenému počtu dostupných jader, snižte výchozí počet agentů přidáním možnosti `--agent-count 1` do příkazu [az acs create](/cli/azure/acs#az-acs-create). 

Po několika minutách se příkaz dokončí a vrátí informace o clusteru ve formátu JSON.

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

V průběhu tohoto rychlého startu budete potřebovat plně kvalifikovaný název domény hlavního uzlu Dockeru Swarm i fondu agentských uzlů Dockeru. Spusťte následující příkaz, který vrátí plně kvalifikované názvy domén hlavního uzlu i agentských uzlů.


```bash
az acs list --resource-group myResourceGroup --query '[*].{Master:masterProfile.fqdn,Agent:agentPoolProfiles[0].fqdn}' -o table
```

Výstup:

```bash
Master                                                               Agent
-------------------------------------------------------------------  --------------------------------------------------------------------
myswarmcluster-myresourcegroup-d5b9d4mgmt.ukwest.cloudapp.azure.com  myswarmcluster-myresourcegroup-d5b9d4agent.ukwest.cloudapp.azure.com
```

Vytvořte tunel SSH k hlavnímu uzlu Swarm. Nahraďte `MasterFQDN` adresou plně kvalifikovaného názvu domény hlavního uzlu Swarm.

```bash
ssh -p 2200 -fNL localhost:2374:/var/run/docker.sock azureuser@MasterFQDN
```

Nastavte proměnnou prostředí `DOCKER_HOST`. To vám umožní spouštět příkazy Dockeru pro Docker Swarm, aniž byste museli zadávat název hostitele.

```bash
export DOCKER_HOST=localhost:2374
```

Nyní jste připraveni spustit služby Dockeru v Dockeru Swarm.


## <a name="run-the-application"></a>Spuštění aplikace

Vytvořte soubor `azure-vote.yaml` a zkopírujte do něj následující obsah.


```yaml
version: '3'
  azure-vote-back:
    image: redis
    ports:
        - "6379:6379"

  azure-vote-front:
    image: microsoft/azure-vote-front:v1
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Spuštěním příkazu [docker stack deploy](https://docs.docker.com/engine/reference/commandline/stack_deploy/) vytvořte službu Azure Vote.

```bash
docker stack deploy azure-vote --compose-file azure-vote.yaml
```

Výstup:

```bash
Creating network azure-vote_default
Creating service azure-vote_azure-vote-back
Creating service azure-vote_azure-vote-front
```

Použijte příkaz [docker stack ps](https://docs.docker.com/engine/reference/commandline/stack_ps/), který vrátí stav nasazení aplikace.

```bash
docker stack ps azure-vote
```

Aplikace bude připravena, jakmile bude mít `CURRENT STATE` každé služby hodnotu `Running`.

```bash
ID                  NAME                            IMAGE                                 NODE                               DESIRED STATE       CURRENT STATE                ERROR               PORTS
tnklkv3ogu3i        azure-vote_azure-vote-front.1   microsoft/azure-vote-front:v1   swarmm-agentpool0-66066781000004   Running             Running 5 seconds ago                            
lg99i4hy68r9        azure-vote_azure-vote-back.1    redis:latest                          swarmm-agentpool0-66066781000002   Running             Running about a minute ago
```

## <a name="test-the-application"></a>Testování aplikace

Přejděte na plně kvalifikovaný název domény fondu agentských uzlů Swarm a otestujte aplikaci Azure Vote.

![Obrázek přechodu na aplikaci Azure Vote](media/container-service-docker-swarm-mode-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>Odstranění clusteru
Pokud už cluster nepotřebujete, můžete k odebrání skupiny prostředků, služby kontejneru a všech souvisejících prostředků použít příkaz [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>Získat kód

V tomto rychlém startu se k vytvoření služby Docker použily předem vytvořené image kontejneru. Související kód aplikace, soubor Dockerfile a soubor Compose jsou k dispozici na GitHubu.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili cluster Docker Swarm a do něj jste nasadili vícekontejnerovou aplikaci.

Další informace o integraci Docker Swarm s Azure DevOps najdete v části CI/CD s Docker Swarm a Azure DevOps.

> [!div class="nextstepaction"]
> [CI/CD s Docker Swarm a Azure DevOps](./container-service-docker-swarm-setup-ci-cd.md)
