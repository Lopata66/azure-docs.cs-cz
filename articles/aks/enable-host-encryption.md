---
title: Povolení šifrování založeného na hostiteli ve službě Azure Kubernetes Service (AKS)
description: Naučte se konfigurovat šifrování založené na hostiteli v clusteru AKS (Azure Kubernetes Service).
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 6f145750ac9158c0184ac9d4d6c303828ee5bdf4
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225148"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Šifrování založené na hostiteli ve službě Azure Kubernetes (AKS) (Preview)

Při šifrování založeném na hostiteli jsou data uložená v hostitelích virtuálních počítačů na virtuálních počítačích uzlů agentů AKS v klidovém stavu a toky se šifrují do služby úložiště. To znamená, že dočasné disky jsou v klidovém stavu zašifrované pomocí klíčů spravovaných platformou. Mezipaměť operačního systému a datových disků je v klidovém stavu šifrovaná s použitím klíčů spravovaných platformou nebo klíčů spravovaných zákazníkem v závislosti na typu šifrování nastaveném na těchto discích. Ve výchozím nastavení platí, že pokud používáte AKS, operační systém a datové disky jsou v klidovém stavu šifrované pomocí klíčů spravovaných platformou, což znamená, že mezipaměti pro tyto disky jsou také standardně zašifrované v klidovém stavu pomocí klíčů spravovaných platformou.  [Pomocí disků Azure ve službě Azure Kubernetes](azure-disk-customer-managed-keys.md)můžete zadat vlastní spravované klíče, které následují za sebou vlastní klíče (BYOK). Mezipaměť pro tyto disky se pak zašifruje taky pomocí klíče, který zadáte v tomto kroku.


## <a name="before-you-begin"></a>Než začnete

Tato funkce se dá nastavit jenom při vytváření clusteru nebo při vytváření fondu uzlů.

> [!NOTE]
> Šifrování založené na hostiteli je dostupné v [oblastech Azure][supported-regions] , které podporují šifrování na straně serveru Azure Managed disks a jenom s konkrétními [podporovanými velikostmi virtuálních počítačů][supported-sizes].

### <a name="prerequisites"></a>Předpoklady

- Ujistěte se, že máte `aks-preview` nainstalovanou příponu CLI v 0.4.55 nebo novější verzi.
- Ujistěte se, že je `EncryptionAtHost` v části `Microsoft.Compute` povoleno příznak funkce.
- Ujistěte se, že je `EncryptionAtHost` v části `Microsoft.ContainerService` povoleno příznak funkce.

### <a name="register-encryptionathost--preview-features"></a>Registrace `EncryptionAtHost` funkcí ve verzi Preview

Pokud chcete vytvořit cluster AKS, který používá šifrování založené na hostiteli, musíte `EncryptionAtHost` u svého předplatného povolit příznak funkce.

Zaregistrujte `EncryptionAtHost` příznak funkce pomocí příkazu [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --namespace "Microsoft.Compute" --name "EncryptionAtHost"

az feature register --namespace "Microsoft.ContainerService"  --name "EncryptionAtHost"
```

Zobrazení stavu v *registraci*trvá několik minut. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Compute/EncryptionAtHost')].{Name:name,State:properties.state}"

az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EncryptionAtHost')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci `Microsoft.ContainerService` `Microsoft.Compute` zprostředkovatelů prostředků a pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.Compute

az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné přihlašovací. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace o tom, jak se zaregistrují, najdete v následujících článcích podpory:
>
> - [Zásady podpory AKS](support-policies.md)
> - [Nejčastější dotazy k podpoře Azure](faq.md)

### <a name="install-aks-preview-cli-extension"></a>Nainstalovat rozšíření CLI AKS-Preview

Chcete-li vytvořit cluster AKS, který je hostitelem šifrování, budete potřebovat nejnovější rozšíření rozhraní *příkazového řádku AKS-Preview* . Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] nebo vyhledejte všechny dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Omezení

- Dá se povolit jenom pro nové fondy uzlů nebo nové clustery.
- Dá se povolit jenom v [oblastech Azure][supported-regions] , které podporují šifrování Azure Managed disks na straně serveru a jenom s konkrétními [podporovanými velikostmi virtuálních počítačů][supported-sizes].
- Vyžaduje cluster AKS a fond uzlů v závislosti na Virtual Machine Scale Sets (VMSS) jako *typ sady virtuálních počítačů*.

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>Použití šifrování založeného na hostiteli pro nové clustery (Preview)

Nakonfigurujte uzly agenta clusteru tak, aby při vytvoření clusteru používaly šifrování založené na hostiteli. `--aks-custom-headers`K nastavení záhlaví použijte příznak `EnableEncryptionAtHost` .

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

Pokud chcete vytvořit clustery bez šifrování založeného na hostiteli, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` parametr.

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>Použití šifrování založeného na hostiteli na existujících clusterech (Preview)

V existujících clusterech můžete povolit šifrování založené na hostiteli přidáním nového fondu uzlů do clusteru. Nakonfigurujte nový fond uzlů pro použití šifrování založeného na hostiteli pomocí `--aks-custom-headers` příznaku.

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

Pokud chcete vytvořit nové fondy uzlů bez funkce šifrování založené na hostiteli, můžete to udělat tak, že vynecháte vlastní `--aks-custom-headers` parametr.

## <a name="next-steps"></a>Další kroky

Projděte si [osvědčené postupy zabezpečení clusteru AKS][best-practices-security] . Další informace o [šifrování na základě hostitele](/azure/virtual-machines/linux/disk-encryption#host-based-encryption).


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[supported-regions]: /azure/virtual-machines/linux/disk-encryption#supported-regions
[supported-sizes]: /azure/virtual-machines/linux/disk-encryption#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
