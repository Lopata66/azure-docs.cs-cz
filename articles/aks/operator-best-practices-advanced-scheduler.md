---
title: Doporučené postupy pro obsluhu – pokročilé funkce plánovače ve službě Azure Kubernetes Services (AKS)
description: Seznamte se s osvědčenými postupy pro použití pokročilých funkcí Scheduleru, jako jsou například chuti a tolerovánosti, selektory uzlů a spřažení nebo spřažení a spřažení ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 546c1d6ae25a33c6df93469ccf8c230b4b1c474b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79252893"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro pokročilé funkce plánovače ve službě Azure Kubernetes Service (AKS)

Při správě clusterů ve službě Azure Kubernetes (AKS) je často potřeba izolovat týmy a úlohy. Plánovač Kubernetes poskytuje pokročilé funkce, které vám umožní řídit, které lusky se můžou naplánovaly na určitých uzlech, nebo jak můžou být aplikace pro víc pod clusterem vhodně distribuované napříč clusterem. 

Tento článek o osvědčených postupech se zaměřuje na pokročilé funkce plánování Kubernetes pro operátory clusterů. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použití chuti a tolerování k omezení, které lusky je možné plánovat na uzlech
> * Dává přednost rozchodu do lusků na určitých uzlech se selektory uzlů nebo spřažením uzlů.
> * Rozdělení nebo seskupení v kombinaci s použitím spřažení nebo proti spřažení

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Poskytování vyhrazených uzlů pomocí chuti a tolerovánosti

**Doprovodné materiály k osvědčeným postupům** – omezení přístupu k aplikacím náročným na prostředky, jako jsou například řadiče příchozího přenosu dat, na konkrétní uzly. Udržujte prostředky uzlů k dispozici pro úlohy, které je vyžadují, a nepovolujte plánování dalších úloh v uzlech.

Při vytváření clusteru AKS můžete nasadit uzly s podporou GPU nebo velkým množstvím výkonných procesorů. Tyto uzly se často používají pro úlohy velkých objemů dat, jako je Machine Learning (ML) nebo umělá Intelligence (AI). Vzhledem k tomu, že tento typ hardwaru je obvykle náročný prostředek uzlu k nasazení, omezte zatížení, které lze na těchto uzlech naplánovat. Místo toho můžete chtít vyhradit některé uzly v clusteru, aby běžely příchozí služby, a zabránit dalším úlohám.

Tato podpora pro různé uzly je poskytována pomocí více fondů uzlů. Cluster AKS poskytuje jeden nebo více fondů uzlů.

Plánovač Kubernetes může pomocí chuti a omezení omezit, jaké úlohy je možné spouštět na uzlech.

* Pro uzel, který indikuje, že je možné naplánovat pouze určité lusky, se použije značka **chuti** .
* **Tolerování** se pak použije na uzel pod, který umožňuje *tolerovat* chuti v uzlu.

Když nasadíte uzel pod do clusteru AKS, Kubernetes pouze plánuje na uzly, kde je dovoleno sjednocení s příchodem. Předpokládejme například, že máte ve svém clusteru AKS fond uzlů pro uzly s podporou GPU. Definujte název, jako je například *GPU*, a pak hodnotu pro plánování. Pokud tuto hodnotu nastavíte na *plán*, Plánovač Kubernetes nemůže naplánovat lusky na uzlu, pokud uzel pod nedefinuje příslušnou tolerovánost.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Po použití chuti na uzlech můžete definovat tolerování ve specifikaci pod, která umožňuje plánování na uzlech. Následující příklad definuje `sku: gpu` a `effect: NoSchedule` k tolerování chuti použité na uzel v předchozím kroku:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Když je nasazený tento uzel pod, jako je například použití `kubectl apply -f gpu-toleration.yaml`, může Kubernetes úspěšně naplánovat uzel pod použitým objektem pro použití. Tato logická izolace umožňuje řídit přístup k prostředkům v rámci clusteru.

Když použijete chuti, pracujte s vývojáři vaší aplikace a vlastníky, abyste jim umožnili definovat požadovaná tolerovánost v jejich nasazeních.

Další informace o chuti a jejich tolerovánosti najdete v tématu [použití chuti a tolerovánosti][k8s-taints-tolerations].

Další informace o použití více fondů uzlů v AKS najdete v tématu [Vytvoření a Správa fondů více uzlů pro cluster v AKS][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Chování chuti a jejich tolerovánosti v AKS

Když upgradujete fond uzlů v AKS, příchuti a tolerovánosti se řídí vzorem, který se použije pro nové uzly:

- **Výchozí clustery, které používají Virtual Machine Scale Sets**
  - Předpokládejme, že máte dva uzly clusteru – *Uzel1* a *Uzel2*. Upgradujete fond uzlů.
  - Vytvoří se dva další uzly, *Uzel3* a *Uzel4*a v uvedeném pořadí se přenesou příchuti.
  - Původní *Uzel1* a *Uzel2* se odstraní.

- **Clustery bez podpory sady škálování virtuálních počítačů**
  - Pak Předpokládejme, že máte dva uzly cluster- *Uzel1* a *Uzel2*. Při upgradu se vytvoří další uzel (*Uzel3*).
  - Od *Uzel1* se aplikují příchuti na *Uzel3*, *Uzel1* se pak odstraní.
  - Vytvoří se další nový uzel (s názvem *Uzel1*, protože předchozí *Uzel1* byl odstraněn) a na nový *Uzel1*se aplikují *uzel2é* chuti. Pak se *Uzel2* odstraní.
  - V podstatě *Uzel1* se bude *Uzel3*a *Uzel2* se bude *Uzel1*.

Při horizontálním navýšení kapacity fondu uzlů v AKS se neprovádí návrh.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Řízení pod plánováním pomocí selektorů uzlů a spřažení

**Doprovodné materiály k osvědčeným postupům** – řízení plánování lusků na uzlech pomocí selektorů uzlů, spřažení uzlů nebo spřažení mezi uzly. Tato nastavení umožňují, aby Plánovač Kubernetes logicky izoluje úlohy, jako je například hardware v uzlu.

K logické izolaci prostředků s pevným vyjmutím se používají chuti a omezení, pokud je v uzlu nepovoluje, není naplánovaná na uzlu. Alternativním přístupem je použití selektorů uzlů. Označíte uzly, například k označení místně připojeného úložiště SSD nebo velkého množství paměti a pak definování ve specifikaci pod modulem pro výběr uzlu. Kubernetes pak tyto lusky naplánuje na vyhovujícím uzlu. Na rozdíl od tolerování je možné naplánovat lusky bez odpovídajícího voliče uzlů v uzlech s popisky. Toto chování umožňuje nevyužité prostředky na uzlech využívat, ale má prioritu pro lusky, které definují odpovídající selektor uzlů.

Pojďme se podívat na příklad uzlů s velkým množstvím paměti. Tyto uzly můžou mít přednost pro lusky, které vyžadují vysoké množství paměti. Aby se zajistilo, že se prostředky nečinný, umožňují také spuštění ostatních lusků.

```console
kubectl label node aks-nodepool1 hardware:highmem
```

Specifikace pod pak přidá vlastnost `nodeSelector` k definování voliče uzlu, který odpovídá sadě popisku na uzlu:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
    nodeSelector:
      hardware: highmem
```

Když použijete tyto možnosti plánovače, spolupracujte s vývojáři vaší aplikace a vlastníky a umožněte jejich správnému definování specifikace pod.

Další informace o používání selektorů uzlů najdete v tématu [přiřazení lusků k uzlům][k8s-node-selector].

### <a name="node-affinity"></a>Spřažení uzlů

Selektor uzlů je základní způsob, jak přiřadit lusky k danému uzlu. K dispozici je větší flexibilita s použitím *spřažení uzlů*. U spřažení uzlů definujete, co se stane, pokud se pod nedá spárovat s uzlem. Můžete *požadovat* , aby Plánovač Kubernetes odpovídal znaku pod pod názvem hostitele. Nebo můžete *preferovat* shodu, ale pokud není k dispozici, nechejte možnost naplánovaná na jiném hostiteli.

Následující příklad nastaví spřažení uzlu na *requiredDuringSchedulingIgnoredDuringExecution*. Tento spřažení vyžaduje, aby plán Kubernetes používal uzel se shodným popiskem. Pokud není k dispozici žádný uzel, musí čekat na pokračování plánování. Chcete-li nechat naplánovaná hodnota v poli pod jiným uzlem, můžete místo toho nastavit hodnotu na *preferredDuringScheduledIgnoreDuringExecution*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

*IgnoredDuringExecution* část nastavení znamená, že pokud se popisky uzlů změní, nesmí být v uzlu vyřazena. Plánovač Kubernetes používá pouze aktualizované popisky uzlů pro naplánování nových lusků, nikoli již v uzlech.

Další informace najdete v tématu [spřažení a anti-spřažení][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Spřažení a proti spřažení

Jeden z konečných přístupů pro Kubernetes Scheduler pro logickou izolaci úloh používá spřažení nebo proti spřažení. Nastavení definuje, že lusky by *neměly* být naplánovány na uzlu, který má existující odpovídající příkaz pod nebo *by měl* být naplánován. Ve výchozím nastavení se Plánovač Kubernetes pokusí naplánovat více lusků v sadě replik napříč uzly. Kolem tohoto chování můžete definovat více specifických pravidel.

Dobrým příkladem je webová aplikace, která používá také službu Azure cache pro Redis. Pravidla ochrany proti spřažení můžete použít k vyžádání, že Plánovač Kubernetes distribuuje repliky napříč uzly. Pak můžete použít pravidla spřažení a zajistit, aby byla každá součást webové aplikace naplánována na stejném hostiteli jako příslušná mezipaměť. Rozdělení lusků mezi uzly vypadá jako v následujícím příkladu:

| **Uzel 1** | **Uzel 2** | **Uzel 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Tento příklad je složitější nasazení než použití selektorů uzlů nebo spřažení uzlů. Nasazení vám umožní řídit, jak se Kubernetes plány v luskech uzlů a můžou logicky izolovat prostředky. Úplný příklad této webové aplikace s mezipamětí Azure cache pro Redis najdete v tématu [společné umístění jednotlivých uzlů na stejný uzel][k8s-pod-affinity].

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na pokročilé funkce plánovače Kubernetes. Další informace o operacích clusteru v AKS najdete v následujících osvědčených postupech:

* [Víceklientská architektura a izolace clusteru][aks-best-practices-scheduler]
* [Základní funkce plánovače Kubernetes][aks-best-practices-scheduler]
* [Ověřování a autorizace][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
