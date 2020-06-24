---
title: Resetování přihlašovacích údajů pro cluster
titleSuffix: Azure Kubernetes Service
description: Přečtěte si, jak aktualizovat nebo resetovat přihlašovací údaje pro instanční objekt nebo přihlašovací údaje aplikace AAD pro cluster Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 914e043e2c0cf39c18480b5ca5e34332398806f4
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905370"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Aktualizace nebo otočení přihlašovacích údajů pro službu Azure Kubernetes (AKS)

Ve výchozím nastavení se clustery AKS vytvářejí s instančním objektem, který má dobu platnosti v jednom roce. Až se blížíte k datu vypršení platnosti, můžete resetovat přihlašovací údaje, aby se instanční objekt po dalších časových obdobích rozšířil. Přihlašovací údaje můžete také aktualizovat nebo otočit v rámci definovaných zásad zabezpečení. Tento článek podrobně popisuje, jak aktualizovat tyto přihlašovací údaje pro cluster AKS.

Cluster AKS můžete také [integrovat s Azure Active Directory][aad-integration]a použít ho jako poskytovatele ověřování pro váš cluster. V takovém případě budete mít pro svůj cluster vytvořenou 2 více identit, aplikaci AAD Server a klientskou aplikaci AAD, můžete také resetovat tato pověření.

Alternativně můžete místo instančního objektu použít spravovanou identitu pro oprávnění. Spravované identity je snazší spravovat než instanční objekty a nevyžadují aktualizace a rotace. Další informace najdete v tématu [použití spravovaných identit](use-managed-identity.md).

## <a name="before-you-begin"></a>Než začnete

Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.65 nebo novější.  `az --version`Verzi zjistíte spuštěním. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Aktualizace nebo vytvoření nového instančního objektu pro cluster AKS

Pokud chcete aktualizovat přihlašovací údaje pro cluster AKS, můžete se rozhodnout:

* Aktualizujte přihlašovací údaje pro existující instanční objekt používaný clusterem nebo
* Vytvořte instanční objekt a aktualizujte cluster tak, aby používal tyto nové přihlašovací údaje.

### <a name="reset-existing-service-principal-credential"></a>Resetovat existující pověření instančního objektu

Pokud chcete aktualizovat přihlašovací údaje pro existující instanční objekt, Získejte ID objektu služby vašeho clusteru pomocí příkazu [AZ AKS show][az-aks-show] . Následující příklad získá ID pro cluster s názvem *myAKSCluster* ve skupině prostředků *myResourceGroup* . ID instančního objektu se nastaví jako proměnná s názvem *SP_ID* pro použití v dalším příkazu. Tyto příkazy používají syntaxi bash.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Pomocí sady proměnných, která obsahuje ID instančního objektu, teď resetujte přihlašovací údaje pomocí příkazového okna [AZ AD SP resetování][az-ad-sp-credential-reset]přihlašovacích údajů. Následující příklad umožňuje platformě Azure vygenerovat nový zabezpečený tajný klíč pro instanční objekt. Tento nový zabezpečený tajný klíč je také uložen jako proměnná.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Teď pokračujte a [aktualizujte cluster AKS pomocí nových přihlašovacích údajů instančního objektu](#update-aks-cluster-with-new-service-principal-credentials). Tento krok je nezbytný, aby se instanční objekt změnil v závislosti na clusteru AKS.

### <a name="create-a-new-service-principal"></a>Vytvořit nový instanční objekt

Pokud se rozhodnete aktualizovat existující přihlašovací údaje instančního objektu v předchozí části, tento krok přeskočte. Pokračujte [v aktualizaci clusteru AKS s použitím nových přihlašovacích údajů instančního objektu](#update-aks-cluster-with-new-service-principal-credentials).

Pokud chcete vytvořit instanční objekt a potom aktualizovat cluster AKS, aby používal tyto nové přihlašovací údaje, použijte příkaz [AZ AD SP Create-for-RBAC][az-ad-sp-create] . V následujícím příkladu parametr `--skip-assignment` zakazuje jakékoli další přiřazení výchozích přiřazení:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Výstup se podobá následujícímu příkladu. Poznamenejte si sami `appId` a `password`. Tyto hodnoty se používají v dalším kroku.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Nyní Definujte proměnné pro ID instančního objektu a tajný klíč klienta pomocí výstupu z vlastního příkazu [AZ AD SP Create-for-RBAC][az-ad-sp-create] , jak je znázorněno v následujícím příkladu. *SP_ID* je vaše *appId*a *SP_SECRET* je vaše *heslo*:

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Teď pokračujte a [aktualizujte cluster AKS pomocí nových přihlašovacích údajů instančního objektu](#update-aks-cluster-with-new-service-principal-credentials). Tento krok je nezbytný, aby se instanční objekt změnil v závislosti na clusteru AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Aktualizovat cluster AKS pomocí nových přihlašovacích údajů instančního objektu

Bez ohledu na to, jestli jste se rozhodli aktualizovat přihlašovací údaje pro existující instanční objekt nebo vytvořit instanční objekt, teď cluster AKS aktualizujete pomocí nových přihlašovacích údajů pomocí příkazu [AZ AKS Update-Credentials][az-aks-update-credentials] . Používají se proměnné pro *--Service-Principal* a *--Client-tajné* :

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Aktualizace přihlašovacích údajů instančního objektu ve službě AKS chvíli trvá.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Aktualizovat cluster AKS s novými přihlašovacími údaji aplikace AAD

Pomocí [kroků integrace AAD][create-aad-app]můžete vytvořit nové servery a klientské aplikace AAD. Nebo obnovte stávající aplikace AAD [podle stejné metody jako u resetování instančního objektu](#reset-existing-service-principal-credential). Až budete muset aktualizovat přihlašovací údaje vaší aplikace AAD clusteru pomocí stejného příkazu [AZ AKS Update-Credentials][az-aks-update-credentials] , ale použijte proměnné *--reset-AAD* .

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Další kroky

V tomto článku se aktualizoval instanční objekt pro samotný cluster AKS a aplikace pro integraci AAD. Další informace o tom, jak spravovat identitu pro úlohy v rámci clusteru, najdete v tématu [osvědčené postupy pro ověřování a autorizaci v AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
