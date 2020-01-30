---
title: Omezení přístupu k kubeconfig ve službě Azure Kubernetes (AKS)
description: Naučte se řídit přístup ke konfiguračnímu souboru Kubernetes (kubeconfig) pro správce clusteru a uživatele clusteru.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/28/2020
ms.author: mlearned
ms.openlocfilehash: d1fdd17b0f6b8ed91d4496f7e9e5a578e53556fe
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845231"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Použití řízení přístupu na základě role v Azure k definování přístupu ke konfiguračnímu souboru Kubernetes ve službě Azure Kubernetes Service (AKS)

Pomocí nástroje `kubectl` můžete komunikovat s clustery s Kubernetes. Rozhraní příkazového řádku Azure nabízí snadný způsob, jak získat přihlašovací údaje a informace o konfiguraci pro připojení k AKS clusterům pomocí `kubectl`. Pokud chcete omezit, kdo může získat informace o konfiguraci Kubernetes (*kubeconfig*) a omezit oprávnění, která pak mají, můžete použít řízení přístupu na základě role (RBAC) v Azure.

V tomto článku se dozvíte, jak přiřadit role RBAC, které omezují, kdo může získat informace o konfiguraci pro cluster AKS.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

Tento článek také vyžaduje, abyste spustili Azure CLI verze 2.0.65 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Oprávnění k dispozici pro role clusteru

Při interakci s clusterem AKS pomocí nástroje `kubectl` se používá konfigurační soubor, který definuje informace o připojení ke clusteru. Tento konfigurační soubor je obvykle uložený v souboru *~/.Kube/config*. V tomto souboru *kubeconfig* lze definovat více clusterů. Mezi clustery přepínáte pomocí příkazu [kubectl config use-Context][kubectl-config-use-context] .

Příkaz [AZ AKS Get-Credentials][az-aks-get-credentials] vám umožní získat přihlašovací údaje pro cluster AKS a sloučí je do souboru *kubeconfig* . K řízení přístupu k těmto přihlašovacím údajům můžete použít řízení přístupu na základě role (RBAC) Azure. Tyto role Azure RBAC umožňují definovat, kdo může načíst soubor *kubeconfig* a jaká oprávnění mají v rámci clusteru.

Tyto dvě předdefinované role:

* **Role Správce clusteru služby Azure Kubernetes**  
  * Umožňuje přístup k volání rozhraní API *Microsoft. ContainerService/managedClusters/listClusterAdminCredential/Action* API. Toto volání rozhraní API [zobrazí seznam přihlašovacích údajů správce clusteru][api-cluster-admin].
  * Stáhne *kubeconfig* pro roli *clusterAdmin* .
* **Role uživatele clusteru služby Azure Kubernetes**
  * Umožňuje přístup k volání rozhraní API *Microsoft. ContainerService/managedClusters/listClusterUserCredential/Action* API. Toto volání rozhraní API [zobrazí seznam přihlašovacích údajů uživatele clusteru][api-cluster-user].
  * Stáhne *kubeconfig* pro roli *clusterUser* .

Tyto role RBAC se dají použít pro uživatele nebo skupinu Azure Active Directory (AD).

> ! ZNAČTE V clusterech používajících službu Azure AD mají uživatelé s rolí *clusterUser* prázdný soubor *kubeconfig* , který vyzývá přihlášení. Po přihlášení uživatelé mají přístup na základě nastavení uživatele nebo skupiny Azure AD. Uživatelé s rolí *clusterAdmin* mají přístup správce.
>
> Clustery, které nepoužívají Azure AD, používají jenom roli *clusterAdmin* .

## <a name="assign-role-permissions-to-a-user-or-group"></a>Přiřazení oprávnění role uživateli nebo skupině

Pokud chcete přiřadit jednu z dostupných rolí, potřebujete získat ID prostředku clusteru AKS a ID účtu uživatele nebo skupiny Azure AD. Následující příklady příkazů:

* Pomocí příkazu [AZ AKS show][az-aks-show] pro cluster s názvem *myAKSCluster* ve skupině prostředků *myResourceGroup* Získejte ID prostředku clusteru. Podle potřeby zadejte vlastní cluster a název skupiny prostředků.
* K získání ID uživatele použijte příkaz [AZ Account show][az-account-show] a [AZ AD User show][az-ad-user-show] .
* Nakonec přiřaďte roli pomocí příkazu [AZ role Assignment Create][az-role-assignment-create] .

Následující příklad přiřadí *roli Správce clusteru služby Azure Kubernetes* k jednotlivému uživatelskému účtu:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --upn-or-object-id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Pokud chcete přiřadit oprávnění ke skupině Azure AD, aktualizujte parametr `--assignee` uvedený v předchozím příkladu s ID objektu pro *skupinu* namísto *uživatele*. Chcete-li získat ID objektu pro skupinu, použijte příkaz [AZ AD Group show][az-ad-group-show] . Následující příklad získá ID objektu pro skupinu Azure AD s názvem *appdev*: `az ad group show --group appdev --query objectId -o tsv`

V případě potřeby můžete změnit předchozí přiřazení na *roli uživatele clusteru* .

Následující příklad výstupu ukazuje, že přiřazení role bylo úspěšně vytvořeno:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Získat a ověřit informace o konfiguraci

S přiřazenými rolemi RBAC pomocí příkazu [AZ AKS Get-Credentials][az-aks-get-credentials] získáte definici *kubeconfig* pro váš cluster AKS. Následující příklad získá přihlašovací údaje *správce* , které fungují správně, pokud byl uživateli udělena *role Správce clusteru*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Pak můžete použít příkaz [zobrazení konfigurace kubectl][kubectl-config-view] a ověřit, zda *kontext* pro cluster ukazuje, že byly použity informace o konfiguraci správce:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Odebrat oprávnění role

Chcete-li odebrat přiřazení rolí, použijte příkaz [AZ role Assignment Delete][az-role-assignment-delete] . Zadejte ID účtu a ID prostředku clusteru, jak Získá předchozí příkazy. Pokud jste roli přiřadili skupině místo uživatele, zadejte ID objektu skupiny, místo ID objektu účtu pro parametr `--assignee`:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Další kroky

Pro zvýšení zabezpečení přístupu ke clusterům AKS [Integrujte ověřování Azure Active Directory][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
