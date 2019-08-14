---
title: Doporučené postupy pro obsluhu – připojení k síti ve službě Azure Kubernetes Services (AKS)
description: Seznamte se s osvědčenými postupy pro používání virtuálních síťových prostředků a připojení ve službě Azure Kubernetes (AKS) pro provozovatele clusteru.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: mlearned
ms.openlocfilehash: d1bc865b38b52c8a7c3ac6ec4dab6408a1d0430c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614753"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro připojení k síti a zabezpečení ve službě Azure Kubernetes Service (AKS)

Když vytváříte a spravujete clustery ve službě Azure Kubernetes Service (AKS), zadáváte pro své uzly a aplikace síťové připojení. Mezi tyto síťové prostředky patří rozsahy IP adres, nástroje pro vyrovnávání zatížení a řadiče pro příchozí přenosy dat. Chcete-li udržet vysoce kvalitní službu pro vaše aplikace, je třeba naplánovat a následně nakonfigurovat tyto prostředky.

Tento článek o osvědčených postupech se zaměřuje na připojení k síti a zabezpečení pro operátory clusterů. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Porovnání režimů kubenet a Azure CNI Network v AKS
> * Plánování požadovaného přidělování IP adres a připojení
> * Distribuce provozu pomocí nástrojů pro vyrovnávání zatížení, řadičů příchozího přenosu dat nebo firewallu webových aplikací (WAF)
> * Zabezpečené připojení k uzlům clusteru

## <a name="choose-the-appropriate-network-model"></a>Zvolit příslušný model sítě

**Doprovodné** materiály k osvědčeným postupům – pro integraci se stávajícími virtuálními sítěmi nebo místními sítěmi použijte Azure CNI Networking v AKS. Tento model sítě také umožňuje větší oddělení prostředků a ovládacích prvků v podnikovém prostředí.

Virtuální sítě poskytují základní konektivitu pro uzly AKS a zákazníkům, kteří budou mít přístup k vašim aplikacím. Existují dva různé způsoby, jak nasadit clustery AKS do virtuálních sítí:

* **Kubenet Networking** – Azure spravuje prostředky virtuální sítě při nasazení clusteru a používá modul plug-in [Kubenet][kubenet] Kubernetes.
* **Azure CNI Networking** – nasazuje se do existující virtuální sítě a používá modul plug-in [Azure Container Networking Interface (CNI)][cni-networking] Kubernetes. Lusky přijímají jednotlivé IP adresy, které se můžou směrovat na jiné síťové služby nebo místní prostředky.

Rozhraní CNI (Container Network Interface) je nezávislý na dodavateli, který umožňuje modulu runtime kontejneru vydávat požadavky poskytovateli sítě. Azure CNI přiřazuje IP adresy do lusků a uzlů a poskytuje funkce správy IP adres (IPAM) jako připojení ke stávajícím virtuálním sítím Azure. Každý uzel a prostředek pod obdrží IP adresu ve virtuální síti Azure a žádné další směrování není potřeba ke komunikaci s dalšími prostředky a službami.

![Diagram znázorňující dva uzly s mostem připojujícím se každé virtuální síti Azure](media/operator-best-practices-network/advanced-networking-diagram.png)

Pro většinu produkčních nasazení byste měli použít Azure CNI Networking. Tento model sítě umožňuje oddělení řízení a správy prostředků. Z hlediska zabezpečení často budete chtít, aby tyto prostředky spravovali a zabezpečili různí týmy. Služba Azure CNI Networking umožňuje připojit se k existujícím prostředkům Azure, místním prostředkům nebo jiným službám přímo prostřednictvím IP adres přiřazených ke každému pod.

Pokud používáte síť Azure CNI, je prostředek virtuální sítě v samostatné skupině prostředků do clusteru AKS. Delegování oprávnění pro objekt služby AKS pro přístup k těmto prostředkům a jejich správu. Instanční objekt používaný clusterem AKS musí mít alespoň oprávnění [Přispěvatel sítě](../role-based-access-control/built-in-roles.md#network-contributor) v podsíti v rámci vaší virtuální sítě. Pokud chcete místo používání předdefinované role přispěvatele sítě definovat [vlastní roli](../role-based-access-control/custom-roles.md) , vyžadují se následující oprávnění:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Další informace o delegování instančního objektu služby AKS najdete v tématu delegování [přístupu k jiným prostředkům Azure][sp-delegation].

Když každý uzel a pod obdrží svou vlastní IP adresu, naplánujte rozsahy adres pro podsítě AKS. Podsíť musí být dostatečně velká, aby poskytovala IP adresy pro každý uzel, lusky a síťové prostředky, které nasadíte. Každý cluster AKS musí být umístěný ve vlastní podsíti. Pokud chcete povolit připojení k místním nebo partnerským sítím v Azure, nepoužívejte rozsahy IP adres, které se překrývají se stávajícími síťovými prostředky. Existují výchozí omezení počtu lusků, které každý uzel spouští s kubenet i s využitím sítě Azure CNI. Aby bylo možné zpracovávat události horizontálního navýšení kapacity nebo upgrady clusterů, potřebujete další IP adresy, které jsou k dispozici pro použití v přiřazené podsíti. Tento další adresní prostor je obzvláště důležitý, pokud používáte kontejnery Windows serveru (aktuálně ve verzi Preview v AKS), protože tyto fondy uzlů vyžadují upgrade na použití nejnovějších oprav zabezpečení. Další informace o uzlech Windows serveru najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

Pokud chcete vypočítat požadovanou IP adresu, přečtěte si téma [Konfigurace sítě Azure CNI v AKS][advanced-networking].

### <a name="kubenet-networking"></a>Kubenet sítě

I když kubenet nevyžaduje, abyste nastavili virtuální sítě před nasazením clusteru, existují nevýhody:

* Uzly a lusky jsou umístěné v různých podsítích IP adres. Směrování definované uživatelem (UDR) a předávání IP se používá ke směrování provozu mezi lusky a uzly. Tento další postup může snížit výkon sítě.
* Připojení k existujícím místním sítím nebo partnerským vztahům k jiným virtuálním sítím Azure můžou být složitá.

Kubenet je vhodný pro malé vývojové nebo testovací úlohy, protože nemusíte vytvářet virtuální síť a podsítě odděleně od clusteru AKS. Jednoduché weby s nízkým provozem nebo přenesené a přesunuté úlohy do kontejnerů můžou také těžit z jednoduchosti AKS clusterů nasazených pomocí sítě kubenet. U většiny nasazení v produkčním prostředí byste měli naplánovat a použít službu Azure CNI Networking. [Pomocí kubenet můžete také nakonfigurovat vlastní rozsahy IP adres a virtuální sítě][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuce příchozího provozu

**Doprovodné** materiály k osvědčeným postupům k distribuci přenosů http nebo https do aplikací, použijte prostředky a řadiče příchozího přenosu dat. Řadiče příchozího přenosu dat poskytují další funkce přes běžný nástroj pro vyrovnávání zatížení Azure a dají se spravovat jako nativní prostředky Kubernetes.

Nástroj pro vyrovnávání zatížení Azure může distribuovat provoz zákazníků do aplikací v clusteru AKS, ale je omezený v tom, co tento provoz chápe. Prostředek nástroje pro vyrovnávání zatížení pracuje ve vrstvě 4 a distribuuje provoz na základě protokolu nebo portů. Většina webových aplikací, které používají protokol HTTP nebo HTTPS, by měly používat Kuberenetes prostředky a řadiče příchozího přenosu dat, které fungují ve vrstvě 7. Příchozí přenos dat může distribuovat provoz na základě adresy URL aplikace a zpracovat ukončení protokolu TLS/SSL. Tato možnost také snižuje počet IP adres, které zveřejňujete a namapujete. U nástroje pro vyrovnávání zatížení každá aplikace obvykle potřebuje veřejnou IP adresu přiřazenou a namapovanou na službu v clusteru AKS. Pomocí prostředku příchozího přenosu dat může jedna IP adresa distribuovat provoz do více aplikací.

![Diagram znázorňující tok příchozího provozu v clusteru AKS](media/operator-best-practices-network/aks-ingress.png)

 Pro příchozí přenos dat jsou k dispozici dvě komponenty:

 * *Prostředek*příchozího přenosu dat a
 * *Kontroler* příchozího přenosu dat

Prostředek příchozího přenosu dat je YAML manifest `kind: Ingress` , který definuje hostitele, certifikáty a pravidla pro směrování provozu do služeb, které běží v clusteru AKS. Následující ukázkový manifest YAML by distribuuje provoz pro *MyApp.com* do jedné ze dvou služeb, *blogservice* nebo *StoreService*. Zákazník je směrován na jednu službu nebo druhý na základě adresy URL, které má přístup.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

Kontroler příchozího přenosu dat je démon, který běží na uzlu AKS a sleduje příchozí požadavky. Provoz se pak distribuuje na základě pravidel definovaných v prostředku příchozího přenosu dat. Nejběžnější kontroler příchozího přenosu dat vychází z [Nginx]. AKS vás neomezuje na konkrétní kontroler, takže můžete použít jiné řadiče, jako je například [rozvrh][contour], [HAProxy][haproxy]nebo [Traefik][traefik].

Řadiče příchozího přenosu dat musí být naplánované na uzlu Linux. Uzly Windows serveru (v současné době ve verzi Preview v AKS) by neměli spustit kontroler příchozího přenosu dat. Pomocí voliče uzlů v rámci YAML manifestu nebo nasazení grafu Helm určete, že prostředek by měl běžet na uzlu se systémem Linux. Další informace najdete v tématu [použití selektorů uzlů k řízení, kde se v AKS][concepts-node-selectors]naplánovala lusky.

Pro příchozí přenos dat existuje mnoho scénářů, včetně následujících průvodců:

* [Vytvoření základního kontroleru příchozího přenosu dat s připojením k externí síti][aks-ingress-basic]
* [Vytvoření kontroleru příchozího přenosu dat, který používá interní privátní síť a IP adresu][aks-ingress-internal]
* [Vytvoření kontroleru příchozího přenosu dat, který používá vaše vlastní certifikáty TLS][aks-ingress-own-tls]
* Vytvořte kontroler příchozího přenosu dat, který pomocí šifry umožňuje automatické generování certifikátů TLS [s dynamickou veřejnou IP adresou][aks-ingress-tls] nebo [statickou veřejnou IP adresou][aks-ingress-static-tls] .

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Zabezpečení provozu pomocí firewallu webových aplikací (WAF)

**Doprovodné** materiály k osvědčeným postupům – pro kontrolu příchozích přenosů na potenciální útoky použijte Firewall webových aplikací (WAF), jako je například [Barracuda WAF pro azure][barracuda-waf] nebo Azure Application Gateway. Tyto pokročilejší síťové prostředky můžou směrovat provoz i přes připojení HTTP a HTTPS nebo základní ukončení protokolu SSL.

Kontroler příchozího přenosu dat, který distribuuje provoz do služeb a aplikací, je obvykle prostředkem Kubernetes v clusteru AKS. Kontroler se spouští jako démon na uzlu AKS a spotřebovává některé prostředky uzlu, jako je například CPU, paměť a šířka pásma sítě. Ve větších prostředích budete často chtít přesměrovat některé z těchto směrování provozu nebo ukončení protokolu TLS na síťový prostředek mimo cluster AKS. Chcete také kontrolovat příchozí přenosy na potenciální útoky.

![Firewall webových aplikací (WAF), například Azure App Gateway, může chránit a distribuovat provoz pro cluster AKS.](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

Firewall webových aplikací (WAF) poskytuje další úroveň zabezpečení pomocí filtrování příchozího provozu. Projekt OWASP (Open Web Application Security) poskytuje sadu pravidel pro sledování útoků, jako jsou skriptování mezi weby nebo poškození souborů cookie. [Application Gateway Azure][app-gateway] (v současnosti ve verzi Preview v AKS) je WAF, který se dá integrovat s clustery AKS pro poskytování těchto funkcí zabezpečení před tím, než přenosy dosáhne vašeho clusteru a aplikací AKS. Tato funkce také provádí jiná řešení třetích stran, takže můžete nadále používat stávající investice nebo odbornost v daném produktu.

Nástroj pro vyrovnávání zatížení nebo prostředky příchozího přenosu dál běží v clusteru AKS, aby bylo možné dále upřesnit distribuci provozu. Aplikační brána se dá centrálně spravovat jako kontroler příchozího přenosu s definicí prostředků. Začněte tím, [že vytvoříte řadič Application Gateway příchozího][app-gateway-ingress]přenosu dat.

## <a name="control-traffic-flow-with-network-policies"></a>Řízení toku provozu pomocí zásad sítě

**Doprovodné** materiály k osvědčeným postupům – pomocí zásad sítě můžete povolit nebo odepřít provoz do lusků. Ve výchozím nastavení je povolen veškerý provoz mezi lusky v rámci clusteru. Pro lepší zabezpečení definujte pravidla, která omezují komunikaci pod.

Zásada sítě je funkce Kubernetes, která umožňuje řídit tok přenosů mezi lusky. Můžete povolit nebo zamítnout provoz na základě nastavení, jako jsou například přiřazené popisky, obor názvů nebo přenosový port. Použití zásad sítě poskytuje způsob, jak řídit tok přenosů z cloudu. V případě, že se v clusteru AKS dynamicky vytvářejí lusky, je možné automaticky použít požadované zásady sítě. Nepoužívejte skupiny zabezpečení sítě Azure k řízení provozu na základě, použijte zásady sítě.

Pokud chcete použít zásady sítě, musí být tato funkce povolená při vytváření clusteru AKS. V existujícím clusteru AKS nemůžete povolit síťové zásady. Plánujte předem a ujistěte se, že jste v clusterech povolili síťové zásady a můžou je podle potřeby používat. Zásady sítě by se měly používat jenom pro uzly se systémem Linux a lusky v AKS.

Zásada sítě se vytvoří jako prostředek Kubernetes pomocí manifestu YAML. Zásady se aplikují na definovaná lusky, pravidla příchozího přenosu dat nebo výstup definují, jak může tok provozu. Následující příklad aplikuje zásady sítě na lusky s *aplikací: back-end –* na ně se aplikuje jmenovka. Pravidlo příchozího přenosu dat povoluje pouze provoz z lusků s *aplikací:* popisek s front-endu:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

Informace o tom, jak začít se zásadami, najdete v tématu [zabezpečení provozu mezi lusky pomocí zásad sítě ve službě Azure Kubernetes Service (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Zabezpečené připojení k uzlům prostřednictvím hostitele bastionu

**Doprovodné** materiály k osvědčeným postupům – nezveřejňujte vzdálené připojení k uzlům AKS. Ve virtuální síti pro správu vytvořte hostitele bastionu nebo pole s odkazem. Použijte hostitele bastionu k bezpečnému směrování provozu do vašeho clusteru AKS na úlohy vzdálené správy.

Většinu operací v AKS můžete dokončit pomocí nástrojů pro správu Azure nebo prostřednictvím serveru Kubernetes API. Uzly AKS nejsou připojené k veřejnému Internetu a jsou dostupné jenom v privátní síti. Pokud se chcete připojit k uzlům a provádět údržbu nebo řešit problémy, směrujte připojení prostřednictvím hostitele bastionu nebo pole s odkazem. Tento hostitel by měl být v samostatné virtuální síti pro správu, která je bezpečně partnerského vztahu k virtuální síti clusteru AKS.

![Připojení k uzlům AKS pomocí hostitele bastionu nebo pole s odkazem](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Síť pro správu pro hostitele bastionu by měla být zabezpečená. Použijte [Azure ExpressRoute][expressroute] nebo [VPN Gateway][vpn-gateway] pro připojení k místní síti a řízení přístupu pomocí skupin zabezpečení sítě.

## <a name="next-steps"></a>Další postup

Tento článek se zaměřuje na připojení a zabezpečení sítě. Další informace o základech sítě v Kubernetes najdete v tématu [Koncepty sítě pro aplikace ve službě Azure Kubernetes Service (AKS)][aks-concepts-network] .

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[Nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool