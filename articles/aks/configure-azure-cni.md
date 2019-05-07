---
title: Konfigurace sítí Azure CNI s ve službě Azure Kubernetes Service (AKS)
description: Informace o konfiguraci sítě Azure CNI (rozšířené) ve službě Azure Kubernetes Service (AKS), včetně nasazení clusteru AKS na existující virtuální síť a podsíť.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/11/2018
ms.author: iainfou
ms.openlocfilehash: 39e0547421c446c1ee48b93b30487ccb9358de02
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192077"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Konfigurace sítí Azure CNI s ve službě Azure Kubernetes Service (AKS)

Ve výchozím nastavení, AKS clusterů použijte [kubenet][kubenet], a virtuální síť a podsíť se vytvoří za vás. S *kubenet*, uzly získat IP adresu z podsítě virtuální sítě. Překlad síťových adres (NAT) je nakonfigurovaný na uzlech a podů, dostanou IP adresu "skrytá" za IP adresu uzlu. Tento přístup snižuje počet IP adres, které je nutné rezervovat v prostoru podů použití vaší sítě.

S [služby Azure Container síťové rozhraní (CNI)][cni-networking], každý pod získá IP adresu z podsítě a je přístupný přímo. Tyto IP adresy musí být jedinečný v rámci prostor vaší sítě a je třeba se připravit předem. Každý uzel má parametr konfigurace pro maximální počet podů, které podporuje. Ekvivalentní počet IP adres na jedno uzlu jsou pak ještě před zahájením vyhrazené pro tento uzel. Tento přístup vyžaduje více plánování a často vede k vyčerpání IP adresu nebo nutnosti znovu sestavovat clusterů ve větší podsíť, jak rostou vaše požadavky aplikace růst.

V tomto článku se dozvíte, jak používat *Azure CNI* sítě a vytvořit podsítě virtuální sítě pro AKS cluster. Další informace o možnostech sítě a důležité informace najdete v tématu [síť koncepty pro Kubernetes a AKS][aks-network-concepts].

## <a name="prerequisites"></a>Požadavky

* Virtuální síť pro AKS cluster, musíte povolit odchozí připojení k Internetu.
* Nevytvářejte více než jeden cluster AKS ve stejné podsíti.
* Nesmíte používat AKS clustery `169.254.0.0/16`, `172.30.0.0/16`, nebo `172.31.0.0/16` pro Kubernetes service rozsah adres.
* Instanční objekt používané clusterem AKS musí mít minimálně [Přispěvatel sítě](../role-based-access-control/built-in-roles.md#network-contributor) oprávnění na podsítě v rámci vaší virtuální sítě. Pokud chcete definovat [vlastní roli](../role-based-access-control/custom-roles.md) nemusíte používat předdefinovaná role Přispěvatel sítě, se vyžadují následující oprávnění:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>Naplánujte IP adresování pro váš cluster

Clusterech nakonfigurovaných s Azure CNI sítě vyžadovat další plánování. Velikost vaší virtuální sítě a podsítě musí podle počtu podů, které máte v úmyslu spustit a počet uzlů clusteru.

IP adresy pro uzly clusteru a podů přidělují v zadané podsíti ve virtuální síti. Každý uzel je nakonfigurovaný pomocí primární IP adresu. Ve výchozím nastavení 30 dalších IP adres předem nakonfigurovat podle Azure CNI, které jsou přiřazeny k podů naplánované na uzlu. Při horizontálním navýšením kapacity vašeho clusteru má každý uzel podobně nakonfigurovanou IP adresu z podsítě. Můžete také zobrazit [maximální podů na uzel](#maximum-pods-per-node).

> [!IMPORTANT]
> Počet IP adres vyžaduje by měl obsahovat důležité informace o upgradu a operace škálování. Pokud nastavíte rozsah IP adres pro podporu pouze pevný počet uzlů, nelze upgradovat nebo škálování clusteru.
>
> - Pokud jste **upgradovat** clusteru AKS, nový uzel se nasadí do clusteru. Služby a úlohy začíná běžet na novém uzlu a starší uzel odebrán z clusteru. Tento proces postupného upgradu vyžaduje minimálně jeden další blok IP adres k dispozici. Počet vašich uzlu je pak `n + 1`.
>
> - Pokud jste **škálování** cluster AKS, nový uzel se nasadí do clusteru. Služby a úlohy začíná běžet na novém uzlu. Váš rozsah IP adres je potřeba zohlednit důležité informace, jak chcete vertikálně navýšit kapacitu počtu uzlů a podů, které cluster může podporovat. Jeden další uzel pro upgrade operace by měla být zahrnuty. Počet vašich uzlu je pak `n + number-of-additional-scaled-nodes-you-anticipate + 1`.

Pokud očekáváte, že uzly pro maximální počet podů, spouštění a pravidelně zničit a nasazování podů, je potřeba zvážit, v některých dalších IP adres na jeden uzel. Tyto další IP adresy vzít v úvahu, může trvat několik sekund pro službu odstranit, a IP adresa se uvolní pro nové služby nasadit, a získat adresu.

Plán IP adres pro AKS cluster se skládá z virtuální sítě, alespoň jednu podsíť pro uzly a podů a rozsah adres služby Kubernetes.

| Rozsah adres / Azure resource | Omezení a změna velikosti |
| --------- | ------------- |
| Virtuální síť | Virtuální síť Azure můžou být velké až /8, ale je omezená na 65 536 nakonfigurovaných IP adres. |
| Podsíť | Musí být dostatečně velký, aby uzly, podů a všechny Kubernetes a Azure prostředky, které může být zřízené ve vašem clusteru. Například pokud nasadíte interní Azure Load Balancer, jeho front-endových IP adres se přidělují z podsítě clusteru, není veřejné IP adresy. Velikost podsítě musí rovněž brát v úvahu upgradu operace účtů nebo budoucích potřeb škálování.<p />Chcete-li vypočítat *minimální* velikost podsítě, včetně dalšího uzlu pro operace upgradu: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Příklad pro cluster s 50 uzly: `(51) + (51  * 30 (default)) = 1,581` (/ 21, nebo větší)<p/>Příklad 50 uzlu clusteru, který také zahrnuje ustanovení vertikálně navýšit kapacitu dalších 10 uzlů: `(61) + (61 * 30 (default)) = 1,891` (/ 21, nebo větší)<p>Pokud nechcete zadat maximální počet podů na uzlu při vytváření clusteru, maximální počet podů na uzel nastavena na *30*. Minimální počet IP adres, vyžaduje se podle této hodnoty. Pokud vypočítáte vaše minimální požadavky IP adres na jinou maximální hodnotu, přečtěte si téma [konfigurace maximální počet podů na uzel](#configure-maximum---new-clusters) na nastavte tuto hodnotu při nasazování clusteru. |
| Rozsah adres služby Kubernetes | Tento rozsah by neměly používat libovolný prvek sítě na nebo připojení k této virtuální síti. Adresa služby CIDR musí být menší než /12. |
| IP adresa služby DNS Kubernetes | IP adresu v rámci rozhraní Kubernetes služby rozsah adres, který bude používat zjišťování služby cluster (kube-dns). Nepoužívejte první IP adresu ve vaší rozsah adres, jako je například.1. První adresou v rozsahu vaší podsítě se používá pro *kubernetes.default.svc.cluster.local* adresu. |
| Adresa mostu docker | IP adresa (v notaci CIDR) použít jako Docker bridge IP adresu na uzlech. Výchozí 172.17.0.1/16. |

## <a name="maximum-pods-per-node"></a>Maximální podů na jeden uzel

Maximální počet podů na jeden uzel v clusteru AKS je 110. *Výchozí* maximální počet podů na uzel se pohybuje mezi *kubenet* a *Azure CNI* sítě a metody nasazení clusteru.

| Metoda nasazení | Výchozí Kubenet | Azure CNI výchozí | Možnost konfigurace během nasazení |
| -- | :--: | :--: | -- |
| Azure CLI | 110 | 30 | Ano (až 250) |
| Šablona Resource Manageru | 110 | 30 | Ano (až 250) |
| Portál | 110 | 30 | Ne |

### <a name="configure-maximum---new-clusters"></a>Nakonfigurujte maximální - nových clusterů

Budete moct nakonfigurovat maximální počet podů na uzel *pouze v době nasazení clusteru*. Pokud provádíte nasazení pomocí rozhraní příkazového řádku Azure nebo pomocí šablony Resource Manageru, můžete nastavit maximální podů každý uzel hodnotu až 250.

* **Azure CLI**: Zadejte `--max-pods` argument při nasazování clusteru s [az aks vytvořit] [ az-aks-create] příkazu. Maximální hodnota je 250.
* **Šablony Resource Manageru**: Zadejte `maxPods` vlastnost [ManagedClusterAgentPoolProfile] objektu při nasazování clusteru pomocí šablony Resource Manageru. Maximální hodnota je 250.
* **Azure portal**: Maximální počet podů na uzel nelze změnit, při nasazování clusteru pomocí webu Azure portal. Azure CNI sítě clusterů jsou omezená na 30 podů podle počtu uzlů, při nasazení pomocí webu Azure portal.

### <a name="configure-maximum---existing-clusters"></a>Nakonfigurujte maximální - stávajících clusterů

Nelze změnit maximální podů na jeden uzel v existujícím clusteru AKS. Číslo můžete upravit pouze v případě, že počáteční nasazení clusteru.

## <a name="deployment-parameters"></a>Parametry nasazení

Při vytváření clusteru AKS, se dají konfigurovat pro sítě Azure CNI následující parametry:

**Virtuální síť**: Virtuální síť, do které chcete nasadit Kubernetes cluster. Pokud chcete vytvořit novou virtuální síť pro cluster, vyberte *vytvořit nový* a postupujte podle pokynů *vytvořit virtuální síť* oddílu. Informace o omezení a kvóty pro virtuální síť Azure, najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Podsíť**: Podsítě v rámci virtuální sítě, ve které chcete nasadit cluster. Pokud chcete vytvořit novou podsíť ve virtuální síti pro váš cluster, vyberte *vytvořit nový* a postupujte podle pokynů *vytvořit podsíť* části. Hybridní připojení se nesmí překrývat rozsah adres s jinými virtuálními sítěmi ve vašem prostředí.

**Rozsah adres služby Kubernetes**: Toto je sada virtuálních IP adres, který přiřazuje Kubernetes interních [služby] [ services] ve vašem clusteru. Můžete použít libovolný rozsah privátních adres, který splňuje následující požadavky:

* Nesmí být v rozsahu IP adres virtuální sítě clusteru
* Se nesmí překrývat s jinými virtuálními sítěmi, které vytvoří partnerskou virtuální síť s clustery
* Se nesmí překrývat s každé místní IP adresy
* Nesmí být v rozsahu `169.254.0.0/16`, `172.30.0.0/16`, nebo `172.31.0.0/16`

I když je technicky možné určit rozsah adres služby v rámci stejné virtuální síti jako cluster, tím proto nedoporučujeme. Pokud překrývající se rozsahy IP adres se používají může způsobit nepředvídatelné chování. Další informace najdete v tématu [nejčastější dotazy k](#frequently-asked-questions) části tohoto článku. Další informace o službách Kubernetes najdete v části [služby] [ services] v dokumentaci ke Kubernetes.

**IP adresa služby Kubernetes DNS**:  IP adresa pro službu DNS clusteru. Tato adresa musí být v rámci *rozsah adres služby Kubernetes*. Nepoužívejte první IP adresu ve vaší rozsah adres, jako je například.1. První adresou v rozsahu vaší podsítě se používá pro *kubernetes.default.svc.cluster.local* adresu.

**Adresa mostu docker**: IP adresa a síťová maska přiřadit mostu Docker. Docker Bridge umožňuje komunikovat s základní platformy pro správu uzlů AKS. Tato IP adresa nesmí být v rozsahu IP adres virtuální sítě clusteru a nesmí překrývat s rozsahy jiných adres používané ve vaší síti.

## <a name="configure-networking---cli"></a>Konfigurace sítě – rozhraní příkazového řádku

Při vytváření clusteru AKS pomocí Azure CLI můžete také nakonfigurovat Azure CNI sítě. Pomocí následujících příkazů vytvořte nový cluster AKS pomocí Azure CNI sítě povolené.

Nejprve Získejte ID prostředku podsítě pro existující podsíť, do které budou připojeny clusteru AKS:

```console
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query [].id --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Použití [az aks vytvořit] [ az-aks-create] příkazů `--network-plugin azure` argument k vytvoření clusteru pomocí rozšířeného sítě. Aktualizace `--vnet-subnet-id` hodnotu s ID podsítě shromážděných v předchozím kroku:

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Konfigurace sítě – portál

Na následujícím snímku obrazovky z portálu Azure portal ukazuje příklad konfigurace tato nastavení při vytváření clusteru AKS:

![Upřesnit konfiguraci sítě na webu Azure Portal][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Platí následující dotazy a odpovědi k **Azure CNI** konfiguraci sítě.

* *Můžete nasadit virtuální počítače v podsíti Moje clusteru?*

  Ne. Nasazení virtuálních počítačů v podsíti používají svůj cluster Kubernetes se nepodporuje. Virtuální počítače jde nasadit ve stejné virtuální síti, ale v jiné podsíti.

* *Můžete nakonfigurovat zásady sítě než pod?*

  Ano, zásady sítě Kubernetes je k dispozici ve službě AKS. Abyste mohli začít, najdete v článku [zabezpečení přenosu mezi pody pomocí zásady sítě ve službě AKS][network-policy].

* *Maximální počet podů je nasadit do uzlu konfigurovatelné?*

  Ano, pokud nasadíte cluster pomocí šablony Resource Manageru nebo rozhraní příkazového řádku Azure. Zobrazit [maximální počet podů na uzel](#maximum-pods-per-node).

  Nelze změnit maximální počet podů na jeden uzel v existujícím clusteru.

* *Jak nakonfigurovat další vlastnosti pro podsíť, která jsem vytvořil při vytváření clusteru AKS Například koncových bodů služby.*

  Úplný seznam vlastností pro virtuální síť a podsítě, které jste vytvořili při vytváření clusteru AKS můžete nakonfigurovat na stránce konfigurace standardní virtuální sítě na webu Azure Portal.

* *Můžete použít jiné podsíti v rámci virtuální sítě pro tento cluster* **rozsah adres služby Kubernetes**?

  Není doporučeno, ale tato konfigurace je možné. Rozsah adres služby je sada virtuální IP adresy (VIP), který přiřazuje interních služeb v clusteru Kubernetes. Sítě Azure nemá žádný vhled do rozsahu IP služby clusteru Kubernetes. Z důvodu nedostatku vhled do rozsahu adres služby clusteru je možné později vytvořit novou podsíť ve virtuální síti clusteru, který se překrývá s rozsahem adres služby. Pokud dojde k takové překrývají, Kubernetes přiřadit služby integrační balíček, který je již používán jiným prostředkem v podsíti, způsobit nepředvídatelné chování nebo selhání. Tím zajistíte, že používáte rozsah adres mimo virtuální síť clusteru, se můžete vyhnout toto riziko překrývají.

## <a name="next-steps"></a>Další postup

### <a name="networking-in-aks"></a>Správa sítě v AKS

Další informace o možnostech sítě v AKS v následujících článcích:

- [Statické IP adresy pomocí nástroje pro vyrovnávání zatížení Azure Kubernetes Service (AKS)](static-ip.md)
- [Použití interního nástroje Azure Container Service (AKS)](internal-lb.md)

- [Vytvoření základního příchozího přenosu dat řadiče s externí síťové připojení.][aks-ingress-basic]
- [Povolit doplněk směrování aplikace HTTP][aks-http-app-routing]
- [Vytvoření řadiče příchozího přenosu dat, který se používá soukromé, interní sítě a IP adresy][aks-ingress-internal]
- [Vytvoření řadič příchozího přenosu dat s dynamické veřejné IP adresy a konfigurace umožňuje šifrovat automaticky generovat certifikáty TLS][aks-ingress-tls]
- [Vytvoření kontroler příchozího přenosu dat se statickou veřejnou IP Adresou a konfigurace umožňuje šifrovat automaticky generovat certifikáty TLS][aks-ingress-static-tls]

### <a name="aks-engine"></a>Modul AKS

[Modul Azure Kubernetes Service (AKS stroj)] [ aks-engine] je opensourcový projekt, který generuje šablon Azure Resource Manageru můžete použít k nasazení clusterů Kubernetes v Azure.

Clustery Kubernetes vytvořili pomocí modulu AKS podporovat [kubenet] [ kubenet] a [Azure CNI] [ cni-networking] moduly plug-in. V důsledku toho AKS modul podporuje obě síťové scénáře.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
