---
title: Vytvoření řadiče příchozího přenosu dat pro interní síť ve službě Azure Kubernetes Service (AKS)
description: Zjistěte, jak nainstalovat a nakonfigurovat řadič NGINX příchozího přenosu dat pro interní privátních sítí v clusteru služby Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: iainfou
ms.openlocfilehash: 27d93f963003cfb30b8827d45c0472405b0ed0a6
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392663"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Vytvoření řadiče příchozího přenosu dat k interní virtuální síti ve službě Azure Kubernetes Service (AKS)

Řadič služby příchozího přenosu dat je část softwaru, která poskytuje reverzní proxy server, směrování provozu konfigurovatelné a ukončení protokolu TLS pro služby Kubernetes. Prostředky Kubernetesu příchozího přenosu dat se používají ke konfiguraci pravidla příchozího přenosu dat a trasy pro jednotlivé služby Kubernetes. Použití kontroler příchozího přenosu dat a pravidla příchozího přenosu dat, jednu IP adresu je možné směrovat provoz do více služeb v clusteru Kubernetes.

V tomto článku se dozvíte, jak nasadit [kontroler příchozího přenosu dat NGINX] [ nginx-ingress] v clusteru služby Azure Kubernetes Service (AKS). Kontroler příchozího přenosu dat nastavená na interní privátní virtuální sítě a IP adresu. Je povolen žádný externí přístup. Pak dvě aplikace běží v clusteru AKS, z nichž každý je přístupný přes jednu IP adresu.

Můžete také:

- [Vytvoření základního příchozího přenosu dat řadiče s externí síťové připojení.][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, která používá vlastní certifikáty TLS][aks-ingress-own-tls]
- Vytvoření řadiče příchozího přenosu dat, která používá umožňuje šifrovat automaticky generovat certifikáty TLS [s dynamické veřejné IP adresy] [ aks-ingress-tls] nebo [se statickou veřejnou IP adresu][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Než začnete

Tento článek používá Helm k instalaci serveru NGINX kontroler příchozího přenosu dat, správce certifikátů a ukázkovou webovou aplikaci. Musíte mít Helm inicializován v rámci clusteru AKS a pomocí účtu služby pro Tiller. Další informace o konfiguraci a použití Helm, naleznete v tématu [instalace aplikací s nástrojem Helm ve službě Azure Kubernetes Service (AKS)][use-helm].

Tento článek také vyžaduje, že používáte Azure CLI verze 2.0.64 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Vytvoření řadiče příchozího přenosu dat

Ve výchozím nastavení se vytvoří kontroler příchozího přenosu dat serveru NGINX s dynamické přiřazení veřejné adresy IP. Běžné požadavky konfigurace je použít soukromé, interní sítě a IP adresu. Tento přístup umožňuje omezení přístupu k vašim službám pro interní uživatele bez externího přístupu.

Vytvořte soubor s názvem *interní ingress.yaml* pomocí následující ukázkový soubor manifestu. Tento příklad přiřadí *10.240.0.42* k *loadBalancerIP* prostředků. Zadejte vlastní interní IP adresa pro použití s kontroleru příchozího přenosu. Ujistěte se, že tato IP adresa není již používán v rámci vaší virtuální sítě.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Nyní nasaďte *nginx příchozího přenosu dat* grafu s nástrojem Helm. Chcete-li použít soubor manifestu vytvořili v předchozím kroku, přidejte `-f internal-ingress.yaml` parametru. Pro přidání redundance dvě repliky řadiče příchozího přenosu dat NGINX nasazení se používají `--set controller.replicaCount` parametru. Pokud chcete naplno využívat s replikami kontroler příchozího přenosu dat, ujistěte se, že existuje více než jeden uzel v clusteru AKS.

Kontroler příchozího přenosu dat musí také naplánovat na uzlu systému Linux. Kontroler příchozího přenosu dat se nemůže spouštět uzly Windows serveru (aktuálně ve verzi preview ve službě AKS). Výběr uzlu je zadán pomocí `--set nodeSelector` parametr říct Kubernetes plánovač spustit kontroler příchozího přenosu dat NGINX na uzlech založených na Linuxu.

> [!TIP]
> Následující příklad vytvoří Kubernetes obor názvů pro prostředky příchozího přenosu dat s názvem *příchozího přenosu dat basic*. Podle potřeby zadejte obor názvů pro konkrétní prostředí. Pokud váš cluster AKS není povoleno RBAC, přidejte `--set rbac.create=false` k příkazům Helm.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Když se pro kontroler příchozího přenosu dat NGINX služby Vyrovnávání zatížení Kubernetes, interní IP adresa se přiřadí, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Žádná pravidla příchozího přenosu dat dosud nebyly vytvořeny, takže pokud přejdete na interní IP adresa, zobrazí se stránka 404 výchozí kontroler příchozího přenosu dat serveru NGINX. Pravidla příchozího přenosu dat se konfigurují v následujících krocích.

## <a name="run-demo-applications"></a>Spuštění ukázkové aplikace

Pokud chcete zobrazit kontroler příchozího přenosu dat v akci, můžeme spustit dvě ukázkové aplikace ve vašem clusteru AKS. V tomto příkladu Helm umožňuje nasadit dvě instance aplikace jednoduchý "Hello world".

Před instalací ukázkové grafy Helm, přidejte úložiště ukázky v Azure následujícím způsobem pro vaše prostředí helmu:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Vytvoření první ukázkové aplikace z grafu helmu pomocí následujícího příkazu:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Nyní instalaci druhé instance ukázkové aplikace. Pro druhou instanci zadejte nový název tak, že dvě aplikace jsou vizuálně distinct. Můžete také zadat jedinečný název služby:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Vytvoření trasy příchozího přenosu dat

Obě aplikace běží v clusteru Kubernetes. Pokud chcete směrovat provoz na každou aplikaci, vytvořte prostředek příchozího přenosu dat Kubernetes. Prostředek příchozího přenosu dat nakonfiguruje pravidla, která směrovat provoz mezi těmito dvěma aplikacemi.

V následujícím příkladu, provoz na adresu `http://10.240.0.42/` se směruje na službu s názvem `aks-helloworld`. Provoz na adresu `http://10.240.0.42/hello-world-two` směrován `ingress-demo` služby.

Vytvořte soubor s názvem `hello-world-ingress.yaml` a kopie v následujícím příkladu YAML.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Vytvoření prostředků pomocí příchozího přenosu dat `kubectl apply -f hello-world-ingress.yaml` příkazu.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testovací kontroler příchozího přenosu dat

K otestování trasy pro kontroler příchozího přenosu dat, přejděte na dvě aplikace pomocí webového klienta. V případě potřeby můžete rychle otestovat tuto funkci pouze interní v podu na clusteru AKS. Vytvoření testů pod a připojit se k němu Terminálové relaci:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Nainstalujte `curl` v podu pomocí `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Nyní přístup k adrese kontroleru příchozího přenosu dat Kubernetes pomocí `curl`, jako například *http://10.240.0.42* . Zadejte že vlastní interní IP adresa zadaná při nasazení kontroler příchozího přenosu dat v prvním kroku v tomto článku.

```console
curl -L http://10.240.0.42
```

Žádné další cesty byla zadaná s adresou, takže kontroler příchozího přenosu dat výchozí hodnota */* trasy. Vrátí první ukázkové aplikace, jak je znázorněno v následujícím výstupu zhuštěnému příkladu:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Nyní přidejte */hello-world-two* cestě na adresu, jako například *http://10.240.0.42/hello-world-two* . Vrátí druhou ukázkové aplikace s vlastní název, jak je znázorněno v následujícím výstupu zhuštěnému příkladu:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento článek používá Helm k instalaci součásti příchozího přenosu dat a ukázkové aplikace. Při nasazování grafu helmu vytvoří řada prostředky Kubernetesu. Tyto prostředky zahrnují podů, nasazení a služby. K uvolnění těchto prostředků, můžete buď odstranit obor názvů celé ukázkové nebo jednotlivé prostředky.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Odstranit obor názvů vzorku a všechny prostředky

Chcete-li odstranit obor názvů celého vzorku, použijte `kubectl delete` příkaz a zadejte název vašeho oboru názvů. Se odstraní všechny prostředky v oboru názvů.

```console
kubectl delete namespace ingress-basic
```

Potom odeberte Helm úložiště pro aplikace hello world AKS:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Odstranit prostředky jednotlivě

Podrobnější přístupem je můžete také odstranit jednotlivé prostředky vytvoří. Seznam Helm vydává se `helm list` příkazu. Vyhledejte grafy s názvem *nginx příchozího přenosu dat* a *aks-helloworld*, jak je znázorněno v následujícím příkladu výstupu:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Odstranit vydané verze s `helm delete` příkazu. Následující příklad odstraní nasazení serveru NGINX příchozího přenosu dat a dvě ukázkové AKS hello world aplikace.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

V dalším kroku odeberte Helm úložiště pro aplikace hello world AKS:

```console
helm repo remove azure-samples
```

Odeberte trasu příchozího přenosu dat, která přesměruje přenosy do ukázkové aplikace:

```console
kubectl delete -f hello-world-ingress.yaml
```

Nakonec můžete odstranit samotný oboru názvů. Použití `kubectl delete` příkaz a zadejte název vašeho oboru názvů:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Další postup

Tento článek zahrnuty některé externí součásti pro AKS. Další informace o těchto komponent, naleznete na následujících stránkách projektu:

- [Helm CLI][helm-cli]
- [Kontroler příchozího přenosu dat serveru NGINX][nginx-ingress]

Můžete také:

- [Vytvoření základního příchozího přenosu dat řadiče s externí síťové připojení.][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření řadič příchozího přenosu dat s dynamické veřejné IP adresy a konfigurace umožňuje šifrovat automaticky generovat certifikáty TLS][aks-ingress-tls]
- [Vytvoření řadiče příchozího přenosu dat se statickou veřejnou IP adresu a nakonfigurovat šifrování teď automaticky generovat certifikáty TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
