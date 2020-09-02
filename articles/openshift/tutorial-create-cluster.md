---
title: Kurz – vytvoření clusteru Azure Red Hat OpenShift 4
description: Zjistěte, jak vytvořit cluster Microsoft Azure Red Hat OpenShift pomocí Azure CLI.
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: a581678fdd05dade336f7ca9fcbcf5ad4c92d49a
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89300166"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Kurz: Vytvoření clusteru Azure Red Hat OpenShift 4

V tomto kurzu, který je první částí tři, připravíte své prostředí, aby se vytvořil cluster Azure Red Hat OpenShift se systémem OpenShift 4, a vytvořte cluster. V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nastavení požadavků a vytvoření požadované virtuální sítě a podsítí
> * Nasazení clusteru

## <a name="before-you-begin"></a>Než začnete

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít spuštěnou verzi Azure CLI 2.6.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Pro vytvoření a spuštění clusteru OpenShift vyžaduje Azure Red Hat OpenShift minimálně 40 jader. Výchozí kvóta prostředků Azure pro nové předplatné Azure nesplňuje tento požadavek. Pokud chcete požádat o zvýšení limitu prostředků, přečtěte si část [standardní kvóta: zvýšení limitů podle řady virtuálních počítačů](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).

### <a name="verify-your-permissions"></a>Ověření oprávnění

Pokud chcete vytvořit cluster Azure Red Hat OpenShift, ověřte následující oprávnění k vašemu předplatnému Azure, Azure Active Directory uživateli nebo instančnímu objektu:

|Oprávnění|Skupina prostředků, která obsahuje virtuální síť|Provádění uživatelem `az aro create`|Instanční objekt se předal jako `–client-id`|
|----|:----:|:----:|:----:|
|**Správce uživatelského přístupu**|X|X| |
|**Přispěvatel**|X|X|X|

### <a name="register-the-resource-provider"></a>Registrace poskytovatele prostředků

Dál je potřeba zaregistrovat `Microsoft.RedHatOpenShift` poskytovatele prostředků ve vašem předplatném.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

### <a name="get-a-red-hat-pull-secret-optional"></a>Získání tajného kódu pro vyžádání Red Hat (volitelné)

Tajný kód pro stažení Red Hat umožňuje vašemu clusteru přístup k registrům kontejnerů Red Hat spolu s dalším obsahem. Tento krok je nepovinný, ale doporučuje se.

1. **[Přejděte na portál Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) a přihlaste se.**

   Budete se muset přihlásit k účtu Red Hat nebo vytvořit nový účet Red Hat pomocí podnikového e-mailu a přijmout podmínky a ujednání.

2. Pokud vytvoříte cluster poprvé, přejdete na [**stránku produktu OpenShift**](https://developers.redhat.com/products/codeready-containers) . Po registraci přejděte na [**stránku Správce clusteru Red Hat OpenShift**](https://cloud.redhat.com/openshift/), kde můžete kliknout na **Stáhnout tajný kód pro získání** a stáhnout tajný klíč pro použití s clusterem ARO.

Uložte si uložený `pull-secret.txt` soubor někam v bezpečí. Tento soubor se použije při vytváření clusteru, pokud potřebujete vytvořit cluster, který obsahuje ukázky nebo operátory pro Red Hat nebo certifikované partnery.

Při spuštění `az aro create` příkazu můžete na svůj tajný kód pro vyžádání obsahu odkazovat pomocí `--pull-secret @pull-secret.txt` parametru. Spusťte `az aro create` z adresáře, kam jste uložili `pull-secret.txt` soubor. V opačném případě nahraďte parametrem `@pull-secret.txt` `@<path-to-my-pull-secret-file>` .

Pokud kopírujete tajný kód pro vyžádání obsahu nebo na něj odkazujete v jiných skriptech, měl by váš tajný klíč pro vyžádání formátu obsahovat platný řetězec JSON.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Příprava vlastní domény pro cluster (volitelné)

Při spuštění `az aro create` příkazu můžete zadat vlastní doménu pro svůj cluster pomocí `--domain foo.example.com` parametru.

Pokud pro svůj cluster zadáte vlastní doménu, Všimněte si následujících bodů:

* Po vytvoření clusteru je nutné na serveru DNS vytvořit dva záznamy DNS A pro `--domain` zadané:
    * **rozhraní API** – ukázání na server rozhraní API
    * ** \* . Apps** – přechod na příchozí přenos dat
    * Tyto hodnoty načtete spuštěním následujícího příkazu: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'` .

* Konzola OpenShift bude k dispozici na adrese URL, jako `https://console-openshift-console.apps.foo.example.com` je místo předdefinované domény `https://console-openshift-console.apps.<random>.<location>.aroapp.io` .

* Ve výchozím nastavení používá OpenShift certifikáty podepsané svým držitelem pro všechny trasy vytvořené v nástroji `*.apps.<random>.<location>.aroapp.io` .  Pokud se po připojení ke clusteru rozhodnete použít vlastní DNS, budete muset postupovat podle dokumentace OpenShift a [nakonfigurovat vlastní CA pro váš kontroler](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) příchozího přístupu a [vlastní CA pro váš Server API](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Vytvoření virtuální sítě obsahující dvě prázdné podsítě

V dalším kroku vytvoříte virtuální síť obsahující dvě prázdné podsítě.

1. **Nastavte následující proměnné v prostředí prostředí, ve kterém budete provádět `az` příkazy.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

1. **Vytvořte skupinu prostředků.**

    Skupina prostředků Azure je logická skupina, ve které se nasazují a spravují prostředky Azure. Při vytváření skupiny prostředků se zobrazí výzva k zadání umístění. V tomto umístění se ukládají metadata skupin prostředků, a to i v případě, že se vaše prostředky spouštějí v Azure, pokud při vytváření prostředků nezadáte jinou oblast. Vytvořte skupinu prostředků pomocí příkazu [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) .
    
> [!NOTE]
> Azure Red Hat OpenShift není k dispozici ve všech oblastech, kde je možné vytvořit skupinu prostředků Azure. Informace o tom, kde se podporuje Azure Red Hat OpenShift, najdete v části [dostupné oblasti](https://docs.openshift.com/aro/4/welcome/index.html#available-regions) .

    ```azurecli-interactive
    az group create --name $RESOURCEGROUP --location $LOCATION
    ```

    The following example output shows the resource group created successfully:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

2. **Vytvořte virtuální síť.**

    Clustery Azure Red Hat OpenShift se systémem OpenShift 4 vyžadují pro hlavní a pracovní uzly virtuální síť se dvěma prázdnými podsítěmi.

    Vytvořte novou virtuální síť ve stejné skupině prostředků, kterou jste vytvořili dříve:

    ```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22
    ```

    Následující příklad výstupu ukazuje, že virtuální síť byla úspěšně vytvořena:

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/22"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

3. **Přidejte prázdnou podsíť pro hlavní uzly.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

4. **Přidejte prázdnou podsíť pro pracovní uzly.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **[Zakažte zásady privátního koncového bodu podsítě](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) v hlavní podsíti.** To je nutné, aby bylo možné připojit a spravovat cluster.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## <a name="create-the-cluster"></a>Vytvoření clusteru

Spuštěním následujícího příkazu vytvořte cluster. Pokud se rozhodnete použít některou z následujících možností, upravte příkaz odpovídajícím způsobem:
* Volitelně můžete [předat tajný klíč pro vyžádání Red Hat](#get-a-red-hat-pull-secret-optional) , který umožňuje vašemu clusteru přístup k registrům kontejnerů Red Hat spolu s dalším obsahem. Přidejte `--pull-secret @pull-secret.txt` argument do příkazu.
* Volitelně můžete [použít vlastní doménu](#prepare-a-custom-domain-for-your-cluster-optional). Přidejte `--domain foo.example.com` argument do příkazu a nahraďte `foo.example.com` ho vlastní doménou.

> [!NOTE]
> Pokud přidáváte do příkazu nějaké volitelné argumenty, nezapomeňte uzavřít argument na předchozím řádku příkazu s koncovým zpětným lomítkem.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

Po provedení `az aro create` příkazu bude normálně trvat přibližně 35 minut, než se cluster vytvoří.

## <a name="next-steps"></a>Další kroky

V této části kurzu jste se naučili:
> [!div class="checklist"]
> * Nastavení požadavků a vytvoření požadované virtuální sítě a podsítí
> * Nasazení clusteru

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Připojení ke clusteru Azure Red Hat OpenShift](tutorial-connect-cluster.md)
