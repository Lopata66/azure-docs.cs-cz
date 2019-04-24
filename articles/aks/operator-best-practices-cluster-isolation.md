---
title: Operátor osvědčené postupy – izolace clusteru ve službě Azure Kubernetes služby (AKS)
description: Podívejte se na clusteru – operátor osvědčené postupy pro izolaci ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 94aaa72497a8a5f171d6b42f59a3c5b507c71492
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465277"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro zajištění izolace clusteru ve službě Azure Kubernetes Service (AKS)

Jak budete spravovat clustery ve službě Azure Kubernetes Service (AKS), často potřebujete oddělit týmy a úlohy. AKS nabízí flexibilitu v tom, jak můžete spustit clustery s více tenanty a izolovat prostředky. Tyto funkce pro více tenantů a izolace by měl k maximalizaci vaší investice do Kubernetes, porozuměl jsem jim a implementovat.

Tento článek o osvědčených postupech se zaměřuje na izolaci pro operátory clusteru. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Plán pro clustery s více tenanty a oddělení prostředků
> * Použijte fyzické nebo logické izolace v clusterech služby AKS

## <a name="design-clusters-for-multi-tenancy"></a>Clustery návrhu pro víceklientské architektury

Kubernetes poskytuje funkce, které logicky oddělit týmy a úlohy ve stejném clusteru. Cíl by měl být k poskytování nejmenší počet oprávnění, omezená na prostředky, každý tým potřebuje. A [Namespace] [ k8s-namespaces] v Kubernetes vytvoří hranici logickou izolaci. Kubernetes další funkce a požadavky na izolaci a víceklientská architektura zahrnovat tyto oblasti:

* **Plánování** zahrnuje základní funkce, jako je kvóty prostředků a rozpočet pod přerušení. Další informace o těchto funkcích najdete v tématu [osvědčené postupy pro Plánovač základní funkce ve službě AKS][aks-best-practices-scheduler].
  * Pokročilejší funkce plánovače zahrnovat poskvrnění a tolerations, uzel selektory a spřažení uzlu a pod nebo proti spřažení. Další informace o těchto funkcích najdete v tématu [osvědčené postupy pro Plánovač pokročilé funkce ve službě AKS][aks-best-practices-advanced-scheduler].
* **Sítě** zahrnuje použití zásad sítě k řízení toku provozu dovnitř a ven z pody.
* **Ověřování a autorizace** zahrnovat uživatele řízení přístupu na základě role (RBAC) a integrace Azure Active Directory (AD), pod identity a tajné kódy ve službě Azure Key Vault. Další informace o těchto funkcích najdete v tématu [osvědčené postupy pro ověřování a autorizace ve službě AKS][aks-best-practices-identity].
* **Kontejnery** patří pod zásady zabezpečení, pod kontextu zabezpečení, vyhledávání obrázků a moduly runtime ohrožení zabezpečení. Je také použít aplikaci Armor nebo Seccomp (výpočetní Secure) k omezení přístupu ke kontejneru na základní uzel.

## <a name="logically-isolate-clusters"></a>Logicky izolování clusterů

**Osvědčené postupy pro moduly** -použít k oddělení týmům a projektům logickou izolaci. Měli snažit minimalizovat počet fyzických clusterů AKS nasadíte do izolovat týmy nebo aplikace.

Logické izolace jeden cluster AKS je možné pro více úloh, týmy nebo prostředí. Kubernetes [obory názvů] [ k8s-namespaces] tvoří logické oddělovací hranice pro úlohy a prostředky.

![Logické izolace cluster Kubernetes v AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logické rozdělení clustery obvykle zajišťuje vyšší hustota pod než fyzicky izolované clustery. Doba nečinnosti v clusteru je méně nadbytečnou kapacitu výpočetních, který je umístěný. V kombinaci s automatického škálování clusteru Kubernetes, můžete škálovat počet uzlů nahoru nebo dolů musí splňovat požadavky. Tento doporučený postup ke automatického škálování vám umožní spustit pouze počet uzlů požadovaných a minimalizuje náklady.

Prostředí Kubernetes v AKS nebo jinde, nejsou zcela bezpečný pro použití v nehostinném prostředí více tenantů. Další bezpečnostní funkce, jako *zásady zabezpečení Pod* a další prvky velice přesně kontrolovat přístup na základě rolí (RBAC) pro uzly ztížit zneužití. True zabezpečení při spouštění úloh v nehostinném prostředí více tenantů, je hypervisor pouze úroveň zabezpečení, které byste měli věřit. Domény zabezpečení pro Kubernetes se změní celý cluster, nikoli jednotlivých uzlů. Pro tyto typy úloh nehostinném prostředí více tenantů měli byste použít fyzicky izolované clustery.

## <a name="physically-isolate-clusters"></a>Fyzicky izolování clusterů

**Osvědčené postupy pro moduly** -minimalizovat použití fyzické izolace pro každý samostatný tým nebo nasazení aplikace. Místo toho použijte *logické* izolace, jak je popsáno v předchozí části.

Běžným přístupem k izolaci clusteru je pomocí fyzicky oddělená clusteru AKS. V tomto modelu izolace úloh nebo týmy jsou přiřazeny vlastní clusteru AKS. Tento přístup se často vypadá jako nejjednodušší způsob, jak izolaci úloh nebo týmy, ale přidá další správa a finanční režijní náklady. Nyní nutné udržovat těchto více clusterů a mít jednotlivě poskytnout přístup a přiřadit oprávnění. Bude se vám účtovat také pro jednotlivé uzly.

![Clustery fyzické izolace jednotlivé platformy kubernetes v AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fyzicky oddělená clustery mají obvykle pod nízkou hustotu. Každý tým nebo úloha má své vlastní cluster AKS, je často over-pass-the zřízeny výpočetní prostředky clusteru. Malý počet podů, často je naplánovaná na těchto uzlech. Nevyužité kapacity u uzly nelze použít pro aplikace nebo služby ve vývoji jinými týmy. Tyto prostředky nadbytečné přispívat do dodatečné náklady ve fyzicky oddělených clusterech.

## <a name="next-steps"></a>Další postup

Tento článek se zaměřuje na clusteru izolace. Další informace o operacích clusteru ve službě AKS najdete v následující osvědčené postupy:

* [Základní funkce plánovače Kubernetes][aks-best-practices-scheduler]
* [Pokročilé funkce plánovače Kubernetes][aks-best-practices-advanced-scheduler]
* [Ověřování a autorizace][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
