---
title: Použití Azure AD ve službě Azure Kubernetes
description: Naučte se používat Azure AD ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 7f6cf503a459175e3109a515b666bbeaa3a25b4d
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549995"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integrace Azure Active Directory spravovaná v AKS

Integrace služby Azure AD spravovaná pomocí AKS je navržená tak, aby zjednodušila integrační prostředí Azure AD, kde se předtím vyžadovalo vytvoření klientské aplikace, serverové aplikace a požadovaného tenanta Azure AD pro udělení oprávnění ke čtení adresáře. V nové verzi poskytovatel prostředků AKS spravuje klientské a serverové aplikace za vás.

## <a name="azure-ad-authentication-overview"></a>Přehled ověřování Azure AD

Správci clusteru můžou nakonfigurovat řízení přístupu na základě role Kubernetes (Kubernetes RBAC) na základě identity uživatele nebo členství ve skupině adresáře. Ověřování Azure AD je k dispozici pro clustery AKS s OpenID Connect. OpenID Connect je vrstva identity postavená nad protokolem OAuth 2,0. Další informace o OpenID připojení najdete v dokumentaci k [otevřenému ID Connect][open-id-connect].

Přečtěte si další informace o službě Azure AD Integration flow v [dokumentaci k koncepcím Azure Active Directory Integration](concepts-identity.md#azure-active-directory-integration).

## <a name="limitations"></a>Omezení 

* Integraci služby Azure AD spravovanou v AKS nejde zakázat.
* clustery s povolenými neKubernetesmi RBAC se nepodporují pro integraci Azure AD spravovaná AKS.
* Změna tenanta Azure AD přidruženého k integraci Azure AD spravované v AKS se nepodporuje.

## <a name="prerequisites"></a>Požadavky

* Azure CLI verze 2.11.0 nebo novější
* Kubectl s minimální verzí [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) nebo [kubelogin](https://github.com/Azure/kubelogin)
* Pokud používáte [Helm](https://github.com/helm/helm), minimální verze Helm 3,3.

> [!Important]
> Je nutné použít Kubectl s minimální verzí 1.18.1 nebo kubelogin. Pokud nepoužíváte správnou verzi, všimnete si potíží s ověřováním.

K instalaci kubectl a kubelogin použijte následující příkazy:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

[Tyto pokyny](https://kubernetes.io/docs/tasks/tools/install-kubectl/) použijte pro jiné operační systémy.

## <a name="before-you-begin"></a>Než začnete

Pro váš cluster potřebujete skupinu Azure AD. Tato skupina je potřeba, jako skupina správců, aby cluster udělil oprávnění správce clusteru. Můžete použít existující skupinu Azure AD nebo vytvořit novou. Poznamenejte si ID objektu vaší skupiny Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Pokud chcete pro správce clusteru vytvořit novou skupinu Azure AD, použijte následující příkaz:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Vytvoření clusteru AKS s povolenou službou Azure AD

Pomocí následujících příkazů rozhraní příkazového řádku vytvořte cluster AKS.

Vytvořte skupinu prostředků Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Vytvoření clusteru AKS a povolení přístupu pro správu pro skupinu Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Úspěšné vytvoření clusteru Azure AD spravovaného AKS má následující část v těle odpovědi.
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Jakmile je cluster vytvořený, můžete k němu začít přistupovat.

## <a name="access-an-azure-ad-enabled-cluster"></a>Přístup ke clusteru s podporou Azure AD

K provedení následujících kroků budete potřebovat integrovanou roli [uživatele clusteru Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) .

Získat přihlašovací údaje uživatele pro přístup ke clusteru:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Pokud se chcete přihlásit, postupujte podle pokynů.

K zobrazení uzlů v clusteru použijte příkaz kubectl Get Nodes:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Nakonfigurujte [řízení přístupu na základě role v Azure (Azure RBAC)](./azure-ad-rbac.md) a nakonfigurujte další skupiny zabezpečení pro clustery.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Řešení potíží s přístupem k Azure AD

> [!Important]
> Následující kroky popisují normální ověřování skupiny služby Azure AD. Používejte je pouze v naléhavém případě.

Pokud jste trvale zablokovali aplikaci, která nemá přístup k platné skupině Azure AD s přístupem ke clusteru, můžete i nadále získat přihlašovací údaje správce pro přímý přístup ke clusteru.

K provedení těchto kroků budete potřebovat přístup k předdefinované roli [Správce clusteru služby Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Povolit integraci služby Azure AD spravovanou v AKS na vašem existujícím clusteru

Integraci služby Azure AD spravovanou v AKS můžete povolit na svém existujícím clusteru s povoleným Kubernetes RBAC. Ujistěte se, že jste nastavili skupinu pro správu tak, aby zůstala v clusteru přístup.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Úspěšná aktivace clusteru Azure AD spravovaného AKS má následující oddíl v těle odpovědi.

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Stáhněte si znovu přihlašovací údaje [uživatele, abyste][access-cluster]měli přístup ke clusteru pomocí následujících kroků.

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Upgrade na integraci služby Azure AD spravované na AKS

Pokud váš cluster používá starší integraci služby Azure AD, můžete upgradovat na integraci služby Azure AD spravovanou v AKS.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Úspěšná migrace clusteru Azure AD spravovaného AKS má následující část v těle odpovědi.

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Pokud chcete získat přístup ke clusteru, postupujte podle kroků uvedených [tady][access-cluster].

## <a name="non-interactive-sign-in-with-kubelogin"></a>Neinteraktivní přihlášení pomocí kubelogin

Existují některé neinteraktivní scénáře, jako jsou kanály průběžné integrace, které nejsou aktuálně k dispozici v kubectl. Můžete použít [`kubelogin`](https://github.com/Azure/kubelogin) pro přístup ke clusteru pomocí neinteraktivního přihlašování instančního objektu.

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Použití podmíněného přístupu s Azure AD a AKS

Při integraci Azure AD s clusterem AKS můžete také použít [podmíněný přístup][aad-conditional-access] k řízení přístupu ke clusteru.

> [!NOTE]
> Podmíněný přístup Azure AD je Azure AD Premium funkce.

Pokud chcete vytvořit ukázkovou zásadu podmíněného přístupu pro použití s AKS, proveďte následující kroky:

1. V horní části Azure Portal vyhledejte a vyberte Azure Active Directory.
1. V nabídce pro Azure Active Directory na levé straně vyberte *podnikové aplikace*.
1. V nabídce u podnikových aplikací na levé straně vyberte *podmíněný přístup*.
1. V nabídce pro podmíněný přístup na levé straně vyberte *zásady* a pak *nové zásady*.
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Přidání zásady podmíněného přístupu":::
1. Zadejte název zásady, jako je například *AKS-Policy*.
1. Vyberte *Uživatelé a skupiny* a pak v části *Zahrnout* vyberte *Vybrat uživatele a skupiny*. Vyberte uživatele a skupiny, u kterých chcete zásady použít. V tomto příkladu vyberte stejnou skupinu Azure AD, která má k vašemu clusteru přístup pro správu.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Výběr uživatelů nebo skupin pro uplatnění zásad podmíněného přístupu":::
1. Vyberte *cloudové aplikace nebo akce* a potom v části *Zahrnout* vyberte *vybrat aplikace*. Vyhledejte *službu Azure Kubernetes* a vyberte *Azure Kubernetes Service AAD Server*.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Výběr serveru AD služby Azure Kubernetes pro použití zásad podmíněného přístupu":::
1. V části *Ovládací prvky přístupu* zvolte *Udělení*. Vyberte *udělit přístup* , pak *vyžadovat, aby zařízení bylo označené jako vyhovující*.
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Výběr pro povolení zásad podmíněného přístupu pro zařízení splňující předpisy":::
1. V části *Povolit zásadu* vyberte *zapnuto* a pak *vytvořit*.
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Povolení zásad podmíněného přístupu":::

Získejte přihlašovací údaje uživatele pro přístup ke clusteru, například:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Pokud se chcete přihlásit, postupujte podle pokynů.

Pomocí `kubectl get nodes` příkazu Zobrazte uzly v clusteru:

```azurecli-interactive
kubectl get nodes
```

Znovu se přihlaste podle pokynů. Všimněte si, že je k dispozici chybová zpráva s oznámením, že jste úspěšně přihlášeni, ale váš správce vyžaduje, aby zařízení, které žádá o přístup, bylo spravováno službou Azure AD pro přístup k prostředku.

V Azure Portal přejděte na Azure Active Directory, vyberte *podnikové aplikace* a potom v části *aktivita* vyberte *přihlášení*. Všimněte si, že záznam v horní části se *stavem* *selhalo* a *podmíněný přístup* *úspěch*. Vyberte položku a pak v *podrobnostech* vyberte *podmíněný přístup* . Všimněte si, že jsou uvedené vaše zásady podmíněného přístupu.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Neúspěšná položka přihlášení kvůli zásadám podmíněného přístupu":::

## <a name="next-steps"></a>Další kroky

* Další informace o [integraci Azure RBAC pro autorizaci Kubernetes][azure-rbac-integration]
* Přečtěte si o [integraci Azure AD s KUBERNETES RBAC][azure-ad-rbac].
* Pomocí [kubelogin](https://github.com/Azure/kubelogin) můžete získat přístup k funkcím pro ověřování Azure, které nejsou dostupné v kubectl.
* Přečtěte si další informace o [konceptech identit AKS a Kubernetes][aks-concepts-identity].
* Pomocí [šablon Azure Resource Manager (ARM) ][aks-arm-template] můžete vytvářet clustery s podporou Azure AD s povolenou správou AKS.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
