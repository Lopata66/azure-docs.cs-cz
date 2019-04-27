---
title: Integrace služby Azure Active Directory s Azure Kubernetes Service
description: Jak vytvářet clustery s podporou Azure Active Directory Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/09/2018
ms.author: iainfou
ms.openlocfilehash: db92526bd02ba55be5df7ce6999e3099e72b8fa5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116769"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrace služby Azure Active Directory s Azure Kubernetes Service

Azure Kubernetes Service (AKS) je nakonfigurovat pro ověřování uživatelů pomocí Azure Active Directory (AD). V této konfiguraci můžete se přihlásit k cluster AKS pomocí tokenu ověřování Azure Active Directory. Kromě toho Správce clusterů budou moct konfigurovat Kubernetes řízení přístupu na základě rolí (RBAC) na základě členství ve skupině uživatelské identity nebo adresáře.

V tomto článku se dozvíte, jak nasadit požadavky pro Azure AD a AKS a pak Nasaďte cluster Azure AD povolené a vytvořte základní role RBAC v clusteru AKS.

Platí následující omezení:

- Azure AD jde Povolit jenom při vytváření nové, RBAC s podporou clusteru. Nejde povolit Azure AD v existujícím clusteru AKS.
- *Host* uživatelů ve službě Azure AD, například jako v případě, že používáte federované přihlašování z jiného adresáře, nejsou podporovány.

## <a name="authentication-details"></a>Podrobnosti o ověřování

Ověřování Azure AD je k dispozici do AKS clusterů s OpenID Connect. OpenID Connect se vrstvu identit postavené na protokol OAuth 2.0. Další informace o OpenID Connect, najdete v článku [Open ID connect dokumentaci][open-id-connect].

Z v rámci clusteru Kubernetes, ověřování pomocí tokenu Webhooku slouží k ověření ověřovacích tokenů. Ověřování pomocí tokenu Webhooku je nakonfigurovat a spravovat jako součást clusteru AKS. Další informace o ověřování pomocí tokenu Webhooku, najdete v článku [dokumentace ověřování webhooku][kubernetes-webhook].

> [!NOTE]
> Při konfiguraci Azure AD pro ověřování AKS, jsou nakonfigurovány dvě aplikace Azure AD. Tuto operaci musíte dokončit microsoftem nebo správcem tenanta Azure.

## <a name="create-server-application"></a>Vytvoření serverové aplikace

První aplikaci Azure AD slouží k získání členství ve skupině uživatelů Azure AD.

1. Vyberte položky **Azure Active Directory** > **Registrace aplikací** > **Registrace nové aplikace**.

   Pojmenujte aplikaci, vyberte **webovou aplikaci nebo API** pro typ aplikace a zadejte libovolnou hodnotu ve formátu identifikátoru URI pro **přihlašovací adresa URL**. Vyberte **vytvořit** až budete hotovi.

   ![Vytvoření registrace služby Azure AD](media/aad-integration/app-registration.png)

2. Vyberte **Manifest** a upravit `groupMembershipClaims` hodnota, která se `"All"`.

   Aktualizace po dokončení uložte.

   ![Aktualizovat členství ve skupině pro všechny](media/aad-integration/edit-manifest.png)

3. Zpět v aplikaci Azure AD, vyberte **nastavení** > **klíče**.

   Přidat popis klíče, vyberte termín vypršení platnosti a vyberte **Uložit**. Poznamenejte si hodnotu klíče. Při nasazení Azure AD povolené clusteru AKS, tato hodnota se označuje jako `Server application secret`.

   ![Získání privátní klíč aplikace](media/aad-integration/application-key.png)

4. Vraťte se do aplikace Azure AD, vyberte **nastavení** > **požadovaná oprávnění** > **přidat**  >   **Vyberte rozhraní API** > **Microsoft Graphu** > **vyberte**.

   ![Vyberte rozhraní graph API](media/aad-integration/graph-api.png)

5. V části **oprávnění aplikace** přidejte zaškrtnutí vedle **čtení dat adresáře**.

   ![Nastavte oprávnění ke graphu aplikace](media/aad-integration/read-directory.png)

6. V části **DELEGOVANÁ oprávnění**, přidejte zaškrtnutí vedle **přihlášení a čtení profilu uživatele** a **čtení dat adresáře**. Uložte změny, až to bude hotové.

   ![Nastavte oprávnění ke graphu aplikace](media/aad-integration/delegated-permissions.png)

   Vyberte **Done** (Hotovo).

7. Zvolte *Microsoft Graphu* ze seznamu rozhraní API, vyberte **udělit oprávnění**. Tento krok selže, pokud není aktuální účet správce tenanta.

   ![Nastavte oprávnění ke graphu aplikace](media/aad-integration/grant-permissions.png)

   Když úspěšně udělena oprávnění se zobrazí následující oznámení na portálu:

   ![Oznámení o úspěšném oprávnění udělená](media/aad-integration/permissions-granted.png)

8. Vraťte se do aplikace a poznamenejte si **ID aplikace**. Při nasazování clusteru služby Azure AD povolené AKS, tato hodnota se označuje jako `Server application ID`.

   ![Získání ID aplikace](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>Vytvořit klientskou aplikaci

Druhá aplikace Azure AD se používá při přihlášení s využitím rozhraní příkazového řádku Kubernetes (kubectl).

1. Vyberte položky **Azure Active Directory** > **Registrace aplikací** > **Registrace nové aplikace**.

   Pojmenujte aplikaci, vyberte **nativní** pro typ aplikace a zadejte libovolnou hodnotu ve formátu identifikátoru URI pro **identifikátor URI pro přesměrování**. Vyberte **vytvořit** až budete hotovi.

   ![Vytvoření registrace AAD](media/aad-integration/app-registration-client.png)

2. V aplikaci Azure AD, vyberte **nastavení** > **požadovaná oprávnění** > **přidat** > **vybrat Rozhraní API** a vyhledávání pro název serveru aplikace vytvořené v předchozím kroku tohoto dokumentu.

   ![Konfigurace oprávnění aplikace](media/aad-integration/select-api.png)

3. Zaškrtněte políčko vedle aplikace a klikněte na tlačítko **vyberte**.

   ![Vyberte koncový bod aplikace AKS AAD serveru](media/aad-integration/select-server-app.png)

   Vyberte **Hotovo**

4. Vyberte svůj server API ze seznamu a klikněte na tlačítko **udělit oprávnění**:

   ![Udělení oprávnění](media/aad-integration/grant-permissions-client.png)

5. Zpět na aplikace AD, poznamenejte si **ID aplikace**. Při nasazování clusteru služby Azure AD povolené AKS, tato hodnota se označuje jako `Client application ID`.

   ![Získání ID aplikace](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>Získání ID tenanta

A konečně Získejte ID vašeho tenanta Azure. Tato hodnota se také používá při nasazování clusteru AKS.

Na webu Azure Portal, vyberte **Azure Active Directory** > **vlastnosti** a poznamenejte si **ID adresáře**. Při nasazování clusteru služby Azure AD povolené AKS, tato hodnota se označuje jako `Tenant ID`.

![Získání ID tenanta Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>Nasazení clusteru

Použití [vytvořit skupiny az] [ az-group-create] příkazu vytvořte skupinu prostředků pro AKS clusteru.

```azurecli
az group create --name myResourceGroup --location eastus
```

Nasazení clusteru pomocí [az aks vytvořit] [ az-aks-create] příkazu. Hodnoty v následující ukázkový příkaz nahraďte hodnoty shromážděné při vytváření aplikací v Azure AD.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

## <a name="create-rbac-binding"></a>Vytvoření vazby RBAC

Předtím, než účet služby Azure Active Directory je možné s clusterem AKS, role vazby nebo vazby role clusteru je potřeba vytvořit. *Role* definovat oprávnění udělit, a *vazby* platí pro požadovaného uživatele. Tato přiřazení můžete použít k daném oboru názvů nebo přes celý cluster. Další informace najdete v tématu [pomocí RBAC se podařilo Autorizovat][rbac-authorization].

Nejprve [az aks get-credentials] [ az-aks-get-credentials] příkazů `--admin` argument pro přihlášení ke clusteru s přístupem správce.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Dále vytvořte ClusterRoleBinding pro účet služby Azure AD, že chcete udělit přístup ke clusteru AKS. Následující příklad poskytuje úplný přístup k účtu na všechny obory názvů v clusteru.

- Pokud uživatel udělíte, že se vazba RBAC pro stejného tenanta služby Azure AD, přiřaďte oprávnění podle principu hlavní název uživatele (UPN). Přesunout na krok k vytvoření manifestu YAML pro ClusterRuleBinding.

- Pokud se uživatel nachází v jiné službě Azure AD tenanta, dotázat a použít *objectId* vlastnost místo. V případě potřeby získat *objectId* požadovaného uživatele účtu pomocí [az ad uživateli zobrazit] [ az-ad-user-show] příkazu. Zadejte hlavní název uživatele (UPN) požadovaný účet:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Vytvoření souboru, například *rbac-aad-user.yaml*a vložte následující obsah. Na posledním řádku, nahraďte *userPrincipalName_or_objectId* s ID (UPN) nebo objekt podle toho, pokud je uživatel stejného tenanta služby Azure AD, nebo ne.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Použít pomocí vazby [použití kubectl] [ kubectl-apply] příkaz, jak je znázorněno v následujícím příkladu:

```console
kubectl apply -f rbac-aad-user.yaml
```

Vazba role můžete také vytvořit pro všechny členy skupiny Azure AD. Skupiny Azure AD jsou určeny pomocí ID objektu skupiny, jak je znázorněno v následujícím příkladu. Vytvoření souboru, například *rbac-aad-group.yaml*a vložte následující obsah. Aktualizace ID objektu skupiny s jedním z vašeho tenanta Azure AD:

 ```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Použít pomocí vazby [použití kubectl] [ kubectl-apply] příkaz, jak je znázorněno v následujícím příkladu:

```console
kubectl apply -f rbac-aad-group.yaml
```

Další informace o zabezpečení clusteru Kubernetes pomocí RBAC najdete v tématu [pomocí RBAC se podařilo Autorizovat][rbac-authorization].

## <a name="access-cluster-with-azure-ad"></a>Přístup k clusteru se službou Azure AD

V dalším kroku o přijetí změn kontextu pro uživatele bez oprávnění správce s využitím [az aks get-credentials] [ az-aks-get-credentials] příkazu.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Po spuštění libovolného příkazu kubectl, budou vyzváni k ověření pomocí Azure. Použijte na obrazovce pokyny.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.9
```

Jakmile budete hotovi, je uložit do mezipaměti ověřovací token. Jsou pouze získat k přihlášení při tokenu vypršela nebo znovu vytvořit konfigurační soubor Kubernetes.

Pokud po úspěšném přihlášení se zobrazuje zprávy o chybě autorizace, zkontrolujte, zda:
1. Uživatel se přihlašujete jako není hostované v instanci Azure AD (to se často stává případě Pokud používáte federované přihlašování z jiného adresáře).
2. Uživatel není členem více než 200 skupin.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>Další postup

Řízení přístupu k prostředkům clusteru pomocí Azure AD Uživatelé a skupiny, najdete v článku [řízení přístupu k prostředkům clusteru pomocí Azure AD identity a řízení přístupu na základě role ve službě AKS][azure-ad-rbac].

Další informace o tom, jak zabezpečit clustery Kubernetes najdete v tématu [možnosti přístupu a identit pro AKS)][rbac-authorization].

Osvědčené postupy na řízení prostředků a identit, naleznete v tématu [osvědčené postupy pro ověřování a autorizace ve službě AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
