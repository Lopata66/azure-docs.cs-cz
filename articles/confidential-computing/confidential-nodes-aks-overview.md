---
title: Důvěrné výpočetní uzly ve službě Azure Kubernetes Service (AKS) Public Preview
description: Důvěrné výpočetní uzly na AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 1b945ac9f656a227bcc3335cb0ec995626f98f77
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564170"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service-public-preview"></a>Důvěrné výpočetní uzly ve službě Azure Kubernetes (Public Preview)

[Důvěrné výpočetní prostředí Azure](overview.md) umožňuje chránit citlivá data, když se používají. Základní infrastruktura chrání tato data od ostatních aplikací, správců a poskytovatelů cloudu pomocí zálohovaných kontejnerů důvěryhodných spuštění s využitím hardwaru.

## <a name="overview"></a>Přehled

Služba Azure Kubernetes Service (AKS) podporuje přidávání [DCsv2 důvěrných výpočetních uzlů](confidential-computing-enclaves.md) využívajících technologii Intel SGX. Tyto uzly můžou spouštět citlivé úlohy v rámci důvěryhodného spuštění prostředí (TEE) založeného na hardwaru, protože umožňují přidělovat privátní oblasti paměti pomocí kódu na úrovni uživatele. Tyto oblasti soukromé paměti se nazývají enclaves. Enclaves jsou určeny k ochraně kódu a dat z procesů spuštěných s vyšším oprávněním. Model spuštění SGX odebere mezilehlé vrstvy hostovaného operačního systému, hostitelského operačního systému a hypervisoru. *Hardware založený na modelu spouštění izolovaného kontejneru* umožňuje aplikacím přímé spouštění s využitím procesoru, přičemž se přitom udržuje speciální blok paměti zašifrovaný. Důvěrné výpočetní uzly vám pomůžou s celkovým zabezpečením stav aplikací kontejnerů na AKS a s velkou doplněním strategie pro důkladnou hloubkovou strategii kontejnerů. 

![Přehled uzlu SGX](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>Funkce AKSch důvěrných uzlů

- Izolace kontejneru na základě hardwaru a úrovně procesu prostřednictvím prostředí SGX Trusted Execution Environment (TEE) 
- Clustery fondu uzlů heterogenní (kombinace důvěrných a nedůvěrných fondů uzlů)
- Plánování založené na paměti šifrované stránky (EPC)
- Předinstalované ovladače SGX DCAP
- Předem nainstalovaná oprava Intel FSGS
- Podporuje automatické škálování a automatické škálování v závislosti na PROCESORech na základě horizontálního škálování pod.
- Pomocník pro ověření identity mimo proc prostřednictvím AKS daemonset
- Podpora kontejnerů pro Linux prostřednictvím pracovních uzlů virtuálních počítačů Ubuntu 18,04 Gen 2

## <a name="aks-provided-daemon-sets-addon"></a>AKS zadané sady démonů (doplněk)

#### <a name="sgx-device-plugin"></a>Modul plug-in zařízení SGX <a id="sgx-plugin"></a>

Modul plug-in zařízení SGX implementuje rozhraní modulu plug-in zařízení Kubernetes pro paměť EPC. Tento modul plug-in zajišťuje v Kubernetes paměť EPC další typ prostředku. Uživatelé můžou u tohoto prostředku zadat omezení stejně jako na jiné prostředky. Kromě funkce plánování pomáhá modul plug-in zařízení přiřazovat SGX k důvěrným kontejnerům úloh oprávnění ovladačů zařízení. Ukázková implementace ukázky nasazení na bázi paměti EPC `kubernetes.azure.com/sgx_epc_mem_in_MiB` je [tady](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml) .

#### <a name="sgx-quote-helper-service"></a>Pomocná služba SGX Quota <a id="sgx-quote"></a>

Enklávy aplikace, které provádějí vzdálené ověření identity, musí vygenerovat citát. Tato nabídka poskytuje kryptografický důkaz identity a stavu aplikace a prostředí, ve kterém je spuštěný enklávy. Generování uvozovek spoléhá na určité důvěryhodné softwarové komponenty od společnosti Intel, které jsou součástí komponent SGX Platform Software Components (PSW/DCAP). Tento PSW je zabalen jako sada démonů, která běží na jeden uzel. Dá se využít při žádosti o CITÁTy ověření z aplikací enklávy. Použití poskytnuté služby AKS vám pomůže zajistit lepší kompatibilitu mezi PSW a dalšími SW SW v hostiteli. [Další](confidential-nodes-out-of-proc-attestation.md) informace najdete v podrobnostech o využití a funkcích.

## <a name="programming--application-models"></a>Programování & modelů aplikací

### <a name="confidential-containers"></a>Důvěrné kontejnery

[Důvěrné kontejnery](confidential-containers.md) spouštějí stávající programy a nejběžnější modul runtime **programovacího jazyka** (Python, Node, Java atd.) spolu s jejich stávajícími závislostmi knihoven bez jakýchkoli úprav zdrojového kódu nebo rekompilace. Tento model je nejrychlejší model pro zajištění důvěrnosti povolený prostřednictvím open source projektů & partnerů Azure. Image kontejnerů, které jsou připravené k běhu v zabezpečených enclaves, se označují jako důvěrné kontejnery.

### <a name="enclave-aware-containers"></a>Kontejnery s podporou enklávy

AKS podporuje aplikace, které jsou naprogramované pro spouštění na důvěrných uzlech a využívají **speciální sadu instrukcí** , která je k dispozici prostřednictvím sad SDK a platforem. Tento model aplikace poskytuje maximální kontrolu nad vašimi aplikacemi s nejnižším důvěryhodným výpočetním základem (TCB). [Přečtěte si další](enclave-aware-containers.md) informace o kontejnerech s podporou enklávy.

## <a name="next-steps"></a>Další kroky

[Nasazení clusteru AKS s důvěrnými výpočetními uzly](./confidential-nodes-aks-get-started.md)

[Stručné ukázky kontejneru důvěrného Starter](https://github.com/Azure-Samples/confidential-container-samples)

[Seznam SKU DCsv2](../virtual-machines/dcv2-series.md)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions