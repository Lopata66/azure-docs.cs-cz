---
title: Vložení image Docker do privátního služby Azure Container Registry
description: Nahrání a stažení imagí Dockeru do soukromého registru kontejnerů v Azure pomocí rozhraní příkazového řádku Dockeru
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 6944755619ea5e8e63af04b9b3bca6f7376e29a9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309443"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Nahrání první image do soukromého registru kontejnerů Dockeru pomocí rozhraní příkazového řádku Dockeru

Registr kontejnerů Azure uchovává a spravuje privátní image kontejnerů [Dockeru](https://hub.docker.com) podobným způsobem, jakým [Docker Hub](https://hub.docker.com/) uchovává veřejné image Dockeru. K [přihlášení](https://docs.docker.com/engine/reference/commandline/login/), [vložení](https://docs.docker.com/engine/reference/commandline/push/), [vyžádání](https://docs.docker.com/engine/reference/commandline/pull/)a dalším operacím v registru kontejneru můžete použít [rozhraní příkazového řádku Docker](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI).

V následujících krocích stáhnete oficiální [Image Nginx](https://store.docker.com/images/nginx) z registru veřejného Docker Hub, označíte ji pro privátní službu Azure Container Registry, nahrajte ji do registru a pak ji nahrajte z registru.

## <a name="prerequisites"></a>Požadavky

* **Registr kontejnerů Azure** – Vytvořte registr kontejnerů ve svém předplatném Azure. Použijte například [Azure Portal](container-registry-get-started-portal.md) nebo rozhraní příkazového [řádku Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI** – musíte mít taky místně nainstalovaný Docker. Docker poskytuje balíčky, které snadno konfigurují Docker na jakémkoli systému [MacOS][docker-mac], [Windows][docker-windows]nebo [Linux][docker-linux] .

## <a name="log-in-to-a-registry"></a>Přihlášení k registru

Existuje [několik způsobů](container-registry-authentication.md) , jak ověřit do privátního registru kontejneru. Doporučeným způsobem při práci na příkazovém řádku je příkaz Azure CLI [AZ ACR Login](/cli/azure/acr?view=azure-cli-latest#az-acr-login). Například pro přihlášení k registru s názvem *myregistry*:

```azurecli
az acr login --name myregistry
```

Můžete se také přihlásit pomocí [přihlášení](https://docs.docker.com/engine/reference/commandline/login/)k Docker. Například jste mohli k registru [přiřadit instanční objekt](container-registry-authentication.md#service-principal) pro scénář automatizace. Když spustíte následující příkaz, po zobrazení výzvy interaktivně poskytněte identifikátor appID (Username) instančního objektu (Username) a heslo. Osvědčené postupy pro správu přihlašovacích údajů najdete v tématu Reference k příkazům [Docker Login](https://docs.docker.com/engine/reference/commandline/login/) :

```
docker login myregistry.azurecr.io
```

Oba příkazy se `Login Succeeded` vrátí po dokončení.

> [!TIP]
> Při použití `docker login` a při označování obrázků pro doručování do registru vždy zadejte plně kvalifikovaný název registru (všechna malá písmena). V příkladech v tomto článku je plně kvalifikovaný název *myregistry.azurecr.IO*.

## <a name="pull-the-official-nginx-image"></a>Stažení oficiální image Nginx

Nejdřív Stáhněte image Public Nginx do svého místního počítače.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Místní spuštění kontejneru

Spusťte následující příkaz [Docker Run](https://docs.docker.com/engine/reference/run/) , který spustí místní instanci Nginx kontejneru interaktivně (`-it`) na portu 8080. `--rm` Argument určuje, zda má být kontejner při zastavení odebrán.

```
docker run -it --rm -p 8080:80 nginx
```

Přejděte k `http://localhost:8080` zobrazení výchozí webové stránky, kterou obsluhuje Nginx, na běžícím kontejneru. Měla by se zobrazit stránka podobná této:

![Server Nginx na místním počítači](./media/container-registry-get-started-docker-cli/nginx.png)

Vzhledem k tomu `-it`, že jste kontejner spustili interaktivně, můžete na příkazovém řádku zobrazit výstup serveru Nginx po přechodu do něj v prohlížeči.

Chcete-li zastavit a odebrat kontejner, `Control`stiskněte klávesu + `C`.

## <a name="create-an-alias-of-the-image"></a>Vytvoření aliasu obrázku

Pomocí [značky Docker](https://docs.docker.com/engine/reference/commandline/tag/) vytvořte alias image s plně kvalifikovanou cestou k vašemu registru. V tomto příkladu se určí obor názvů `samples`, aby se zabránilo nepořádku v kořenovém adresáři registru.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Další informace o označování pomocí oborů názvů najdete v části [obory názvů úložiště](container-registry-best-practices.md#repository-namespaces) v tématu [osvědčené postupy pro Azure Container Registry](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Vložení image do registru

Teď, když jste označili image s plně kvalifikovanou cestou k vašemu privátnímu registru, můžete ji do registru vložit pomocí [Docker push](https://docs.docker.com/engine/reference/commandline/push/):

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Stažení Image z registru

K načtení image z registru použijte příkaz [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) :

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Spuštění kontejneru Nginx

Pomocí příkazu [Docker Run](https://docs.docker.com/engine/reference/run/) spusťte bitovou kopii, kterou jste si vyžádali z registru:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Přejděte k `http://localhost:8080` zobrazení běžícího kontejneru.

Chcete-li zastavit a odebrat kontejner, `Control`stiskněte klávesu + `C`.

## <a name="remove-the-image-optional"></a>Odebrat bitovou kopii (volitelné)

Pokud už image Nginx nepotřebujete, můžete ji místně odstranit pomocí příkazu Docker [RMI](https://docs.docker.com/engine/reference/commandline/rmi/) .

```
docker rmi myregistry.azurecr.io/samples/nginx
```

K odebrání imagí z Azure Container Registry můžete použít příkaz Azure CLI [AZ ACR úložiště Delete](/cli/azure/acr/repository#az-acr-repository-delete). Například následující příkaz odstraní manifest, na který odkazuje `samples/nginx:latest` tag, jakákoli jedinečná data vrstvy a všechny ostatní značky odkazující na manifest.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Další postup

Teď, když znáte základy, jste připraveni začít používat svůj registr! Nasaďte například Image kontejneru z registru do:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Volitelně můžete nainstalovat [rozšíření Docker pro Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) a rozšíření [účtu Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) pro práci se službou Azure Container Registry. Vyhrajte a nahrajte image do služby Azure Container registry nebo spouštějte ACR úlohy, a to všechno v rámci Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
