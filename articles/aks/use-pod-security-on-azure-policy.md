---
title: Zabezpečení lusků pomocí Azure Policy ve službě Azure Kubernetes (AKS)
description: Naučte se zabezpečit lusky pomocí Azure Policy ve službě Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 8a5107b9ba3c05c92a06753b2cb30bcfc2896d91
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86090897"
---
# <a name="secure-pods-with-azure-policy-preview"></a>Zabezpečení lusků pomocí Azure Policy (Preview)

Pokud chcete zlepšit zabezpečení clusteru AKS, můžete určit, jaké funkce se mají přidělovat, a pokud cokoli běží na zásadách společnosti. Tento přístup je definovaný prostřednictvím předdefinovaných zásad, které poskytuje [doplněk Azure Policy pro AKS][kubernetes-policy-reference]. Díky lepší kontrole nad aspekty zabezpečení specifikace vašeho zařízení, jako jsou například oprávnění ke kořenu, umožňuje přísnější dodržování zabezpečení a přehled o tom, co je ve vašem clusteru nasazené. Pokud parametr pod nesplňuje podmínky zadané v zásadě, Azure Policy může zakázat ovládacímu poli pod, aby mohl spustit nebo označit porušení. V tomto článku se dozvíte, jak pomocí Azure Policy omezit nasazení lusků v AKS.

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné přihlašovací. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

### <a name="install-the-azure-policy-add-on-for-aks"></a>Instalace doplňku Azure Policy pro AKS

Chcete-li zabezpečit AKS po Azure Policy, je nutné nainstalovat Azure Policy doplněk pro AKS do clusteru AKS. Pomocí těchto [kroků nainstalujte doplněk Azure Policy](../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks).

V tomto dokumentu se předpokládá, že máte následující ty, které jsou nasazené v předchozím odkazovaném procházení.

* Zaregistrovali `Microsoft.ContainerService` jste `Microsoft.PolicyInsights` poskytovatele prostředků a pomocí`az provider register`
* Zaregistrovali jste `AKS-AzurePolicyAutoApprove` příznak funkce Preview pomocí`az feature register`
* Rozhraní příkazového řádku Azure, které je nainstalované s `aks-preview` rozšířením verze 0.4.53 nebo novější
* Cluster AKS s podporovanou verzí 1,15 nebo vyšší instalovanou s doplňkem Azure Policy

## <a name="overview-of-securing-pods-with-azure-policy-for-aks-preview"></a>Přehled zabezpečení lusků pomocí Azure Policy pro AKS (Preview)

>[!NOTE]
> Tento dokument popisuje, jak použít Azure Policy k zabezpečení lusků, což je nástupce [funkce zásad zabezpečení Kubernetes pod ve verzi Preview](use-pod-security-policies.md).
> **Obě zásady zabezpečení (Preview) a Azure Policy doplňku pro AKS nelze povolit současně.**
> 
> Pokud instalujete doplněk Azure Policy do clusteru s povolenými zásadami zabezpečení pod, [vypněte pomocí těchto kroků zásady zabezpečení pod tímto postupem](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster).

V clusteru AKS se k zachycení požadavků na server rozhraní API používá řadič pro přístup, když se prostředek vytvoří a aktualizuje. Řadič pro přijímání pak může *ověřit* požadavek prostředku na základě sady pravidel podle toho, jestli se má vytvořit.

Dříve byla tato funkce [(Preview)](use-pod-security-policies.md) povolena prostřednictvím projektu Kubernetes, aby se omezilo, které z nich je možné nasadit. Tato funkce už není aktivním vývojem z projektu Kubernetes.

Pomocí Azure Policyho doplňku může cluster AKS používat integrované zásady Azure, které v předchozích zásadách zabezpečení a dalších Kubernetes prostředků podobné zásadám zabezpečení. Doplněk Azure Policy pro AKS nainstaluje spravovanou instanci [serveru gatekeeper](https://github.com/open-policy-agent/gatekeeper)a ověří řadič přístupu. Azure Policy pro Kubernetes je postavená na Open Source otevřeném agentem zásad, který spoléhá na [Jazyk zásad Rego](../governance/policy/concepts/policy-for-kubernetes.md#policy-language).

Tento dokument popisuje, jak použít Azure Policy k zabezpečení lusků v clusteru AKS a pokyny k migraci ze zásad zabezpečení (Preview).

## <a name="limitations"></a>Omezení

* Během období Preview se může v jednom clusteru spouštět omezení 200 až 20 Azure Policy pro zásady Kubernetes.
* [Některé obory názvů systému](#namespace-exclusion) obsahující AKS spravované lusky jsou vyloučené z vyhodnocení zásad.
* Windows lusky [nepodporují kontexty zabezpečení](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods), takže mnoho zásad Azure se vztahuje jenom na lusky Linux, jako je třeba zrušení povolení kořenových oprávnění, která se nedají předávat v oknech Windows.
* Zásady zabezpečení a Azure Policy doplňku pro AKS nelze povolit současně. Pokud instalujete doplněk Azure Policy do clusteru se zapnutou zásadou zabezpečení pod, vypněte zásadu zabezpečení pod [následujícími pokyny](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster).

## <a name="azure-policies-to-secure-kubernetes-pods"></a>Zásady Azure pro zabezpečení Kubernetes lusky

Po instalaci doplňku Azure Policy se ve výchozím nastavení neuplatní žádné zásady.

Existují čtrnáct (14) předdefinovaných individuálních zásad Azure a dvou (2) integrovaných iniciativ, které v clusteru AKS konkrétně zabezpečují lusky.
Jednotlivé zásady je možné přizpůsobit pomocí efektu. [Tady je uvedený úplný seznam zásad AKS a jejich podporovaných efektů][policy-samples]. Přečtěte si další informace o [Azure Policych efektech](../governance/policy/concepts/effects.md).

Zásady Azure je možné použít na úrovni skupiny pro správu, předplatného nebo skupiny prostředků. Když přiřadíte zásadu na úrovni skupiny prostředků, ujistěte se, že je v oboru zásad vybraná skupina prostředků cílového clusteru AKS. Každý cluster v přiřazeném oboru s nainstalovaným doplňkem Azure Policy je v oboru pro zásady.

Pokud používáte [zásadu zabezpečení pod (Preview)](use-pod-security-policies.md), zjistěte, jak [migrovat na Azure Policy a o dalších rozdílech v různých chováních](#migrate-from-kubernetes-pod-security-policy-to-azure-policy).

### <a name="built-in-policy-initiatives"></a>Integrované iniciativy zásad

Iniciativa v Azure Policy je kolekce definic zásad, které jsou přizpůsobené pro dosažení cíle přeplňování jednotného přihlašování. Použití iniciativ může zjednodušit správu a přiřazování zásad napříč AKS clustery. Iniciativa existuje jako jeden objekt, další informace o [Azure Policy iniciativách](../governance/policy/overview.md#initiative-definition).

Azure Policy pro Kubernetes nabízí dvě integrované iniciativy, které zabezpečují standardní lusky, [základní](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) a [omezené](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00).

Obě předdefinované iniciativy jsou sestavené z definic používaných v [pod zásadami zabezpečení z Kubernetes](https://github.com/kubernetes/website/blob/master/content/en/examples/policy/baseline-psp.yaml).

|[Řízení zásad zabezpečení pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Odkaz na definici Azure Policy| Základní iniciativa | Omezená iniciativa |
|---|---|---|---|
|Zakázat spouštění privilegovaných kontejnerů|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4)| Ano | Ano
|Zakázat sdílené použití oborů názvů hostitele|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8)| Ano | Ano
|Omezení využití hostitelských sítí a portů na známý seznam|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe)| Ano | Ano
|Omezit využití hostitelského systému souborů|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75)| Ano | Ano
|Přidávání možností pro Linux mimo [výchozí sadu](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) | Ano | Ano
|Omezit využití definovaných typů svazků|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16697877-1118-4fb1-9b65-9898ec2509ec)| - | Yes
|Eskalace oprávnění do kořenového adresáře|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) | - | Yes |
|Omezení ID uživatelů a skupin kontejneru|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Yes |
|Omezte přidělení FSGroup, které vlastní svazky pod.|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Yes |
|Vyžaduje použití profilu seccomp|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F975ce327-682c-4f2e-aa46-b9598289b86c) | - | - |
|Omezení sysctl profilu používaného kontejnery|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56d0a13f-712f-466b-8416-56fb354fb823) | - | - |
|Výchozí typy připojení procedury jsou definované tak, aby se snížila plocha pro útoky.|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff85eb0dd-92ee-40e9-8a76-db25a507d6d3) | - | - |
|Omezit na konkrétní ovladače FlexVolume|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4a8fce0-2dd5-4c21-9a36-8f0ec809d663) | - | - |
|Povoluje připojení, která nejsou jen pro čtení.|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) | - | - |
|Definování vlastního kontextu SELinux kontejneru|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e6c427-07d9-46ab-9689-bfa85431e636) | - | - |
|Definování profilu AppArmor používaného kontejnery|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) | - | - |

<!---
# Removing until custom initiatives are supported the week after preview

#### Custom initiative

If the built-in initiatives to address pod security do not match your requirements, you can choose your own policies to exist in a custom initiative. Read more about [building custom initatives in Azure Policy](../governance/policy/tutorials/create-and-manage#create-and-assign-an-initiative-definition).

--->

### <a name="namespace-exclusion"></a>Vyloučení oboru názvů

> [!WARNING]
> Lusky v názvových prostorech správců, jako je Kube-System, musí běžet, aby cluster zůstal v pořádku. odebrání požadovaného oboru názvů ze seznamu výchozích vyloučených oborů názvů může vystavit porušení zásad z důvodu požadovaného systému pod.

AKS vyžaduje, aby se systémové lusky spouštěly na clusteru a poskytovaly důležité služby, jako je například překlad DNS. Zásady, které omezují funkčnost, můžou ovlivnit stabilitu systému pod. V důsledku toho jsou tyto obory názvů **vyloučené z vyhodnocení zásad během žádosti o přijetí během vytváření, aktualizace a auditování zásad**. To vynutí, aby se nová nasazení do těchto oborů názvů vyloučila ze zásad Azure.

1. Kube – systém
1. Server Gatekeeper
1. Azure – ARC
1. AKS – Periscope

Další vlastní obory názvů je možné z vyhodnocení vyloučit během vytváření, aktualizace a auditu. To by mělo být použito, pokud máte specializované lusky, které běží ve schváleném oboru názvů a chcete se vyhnout aktivaci auditu narušení.

## <a name="apply-the-baseline-initiative"></a>Použití základní iniciativy

> [!TIP]
> Všechny zásady se ve výchozím nastavení projeví pro audit. Účinky lze kdykoli aktualizovat na odepřít Azure Policy.

Aby bylo možné použít směrný plán, můžeme přiřazovat prostřednictvím Azure Portal.
<!--
1. Navigate to the Policy service in Azure portal
1. In the left pane of the Azure Policy page, select **Definitions**
1. Search for "Baseline Profile" on the search pane to the right of the page
1. Select `Kubernetes Pod Security Standards Baseline Profile for Linux-based workloads` from the `Kubernetes` category
-->
1. Pomocí [tohoto odkazu na Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) si Projděte základní iniciativu zabezpečení pod.
1. Nastavte **Rozsah** na úroveň předplatného nebo jenom skupinu prostředků obsahující clustery AKS s povoleným doplňkem Azure Policy.
1. Vyberte stránku **parametry** a aktualizujte **efekt** z `audit` na na `deny` , pokud chcete zablokovat nová nasazení porušující základní iniciativu.
1. Přidání dalších oborů názvů, které se mají vyloučit z vyhodnocení během vytváření, aktualizace a auditu, [se některé obory názvů vyloučí z vyhodnocení zásad.](#namespace-exclusion) 
 ![ aktualizovat efekt](media/use-pod-security-on-azure-policy/update-effect.png)
1. Vyberte možnost **zkontrolovat + vytvořit** a odešlete zásady.

Potvrzení zásad se aplikují na cluster spuštěním `kubectl get constrainttemplates` .

> [!NOTE]
> Synchronizace zásad může trvat [až 20 minut, než se synchronizuje](../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition) do každého clusteru.

Výstup by měl vypadat přibližně takto:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              30m
k8sazureblockhostnamespace               30m
k8sazurecontainernoprivilege             30m
k8sazurehostfilesystem                   30m
k8sazurehostnetworkingports              30m
```

## <a name="validate-rejection-of-a-privileged-pod"></a>Ověření odmítnutí privilegovaného pod

Pojďme nejdřív otestovat, co se stane, když naplánujete pod, pomocí kontextu zabezpečení `privileged: true` . Tento kontext zabezpečení přestupňování oprávnění pod. Základní iniciativa nepovoluje privilegované lusky, takže požadavek bude odepřen v případě zamítnutí nasazení.

Vytvořte soubor s názvem `nginx-privileged.yaml` a vložte následující YAML manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx
      securityContext:
        privileged: true
```

Vytvořte pod příkazem [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f nginx-privileged.yaml
```

Podle očekávání se nepovedlo naplánovat na, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

V poli se nedosáhnou fáze plánování, takže před přesunutím na neexistují žádné prostředky, které by bylo možné odstranit.

## <a name="test-creation-of-an-unprivileged-pod"></a>Vytvoření testu neprivilegovaného pod

V předchozím příkladu se image kontejneru automaticky pokusila použít kořen k navázání NGINX na port 80. Tuto žádost zamítla iniciativa zásad směrného plánu, takže se pod ní nepovede spustit. Zkusíme teď spustit stejný NGINX pod bez privilegovaného přístupu.

Vytvořte soubor s názvem `nginx-unprivileged.yaml` a vložte následující YAML manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx
```

Vytvořte pod pomocí příkazu [kubectl Applu][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f nginx-unprivileged.yaml
```

V části se úspěšně naplánovalo. Když zkontrolujete stav pod, pomocí příkazu [kubectl Get lusky][kubectl-get] je *spuštěný*:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Tento příklad ukazuje základní iniciativu ovlivňující jenom nasazení, která porušují zásady v kolekci. Povolená nasazení budou nadále fungovat.

Pomocí příkazu [kubectl Delete][kubectl-delete] odstraňte Nginx s neprivilegovaným příkazem a zadejte název vašeho manifestu YAML:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-policies-and-the-azure-policy-add-on"></a>Zakázat zásady a doplněk Azure Policy

Odebrání základní iniciativy:

1. Přejděte do podokna zásady na Azure Portal
1. V levém podokně vyberte **přiřazení** .
1. Klikněte na "..." tlačítko vedle profilu standardních hodnot
1. Vyberte Odstranit přiřazení.

![Odstranit přiřazení](media/use-pod-security-on-azure-policy/delete-assignment.png)

Doplněk Azure Policy zakážete pomocí příkazu [AZ AKS Disable-addons][az-aks-disable-addons] .

```azure-cli
az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
```

Naučte se, jak odebrat [doplněk Azure Policy z Azure Portal](../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks).

## <a name="migrate-from-kubernetes-pod-security-policy-to-azure-policy"></a>Migrace ze zásad zabezpečení Kubernetes pod na Azure Policy

Chcete-li provést migraci ze zásad zabezpečení v systému, je třeba provést následující akce v clusteru.

1. [Zakázat zásadu zabezpečení pod](use-pod-security-policies.md#clean-up-resources) v clusteru
1. Povolení [doplňku Azure Policy][kubernetes-policy-reference]
1. Povolit požadované zásady Azure z [dostupných integrovaných zásad][policy-samples]

Níže je souhrn změn chování mezi zásadami zabezpečení a Azure Policy.

|Scénář| Zásady zabezpečení pod | Azure Policy |
|---|---|---|
|Instalace|Funkce zásady zabezpečení Povolit pod |Povolit Azure Policy doplněk
|Nasadit zásady| Prostředek nasazení pod zásadou zabezpečení| Přiřaďte zásady Azure k oboru skupiny prostředků nebo předplatnému. Pro aplikace prostředků Kubernetes je vyžadován doplněk Azure Policy.
| Výchozí zásady | Když je v AKS povolené zásady zabezpečení, aplikují se výchozí privilegované a neomezená zásada. | Povolením doplňku Azure Policy nepoužijete žádné výchozí zásady. Zásady musíte explicitně povolit v Azure Policy.
| Kdo může vytvářet a přiřazovat zásady | Správce clusteru vytvoří prostředek zásad zabezpečení pod. | Uživatelé musí mít ve skupině prostředků clusteru AKS minimální roli oprávnění "vlastník" nebo "Přispěvatel zásad prostředků". -Prostřednictvím rozhraní API můžou uživatelé přiřazovat zásady v oboru prostředků clusteru AKS. Uživatel by měl mít minimálně oprávnění "vlastník" nebo "Přispěvatel zásad prostředků" na prostředku clusteru AKS. – Na portálu Azure Portal je možné přiřazovat zásady na úrovni skupiny pro správu nebo předplatného nebo skupiny prostředků.
| Autorizace zásad| Uživatelé a účty služeb vyžadují explicitní oprávnění k používání zásad zabezpečení pod. | K autorizaci zásad není nutné žádné další přiřazení. Až se zásady přiřadí v Azure, můžou tyto zásady používat všichni uživatelé clusteru.
| Použitelnost zásad | Uživatel s rolí správce obchází vynucování zásad zabezpečení pod. | Všichni uživatelé (Správci & nepoužívají správce) uvidí stejné zásady. Na základě uživatelů neexistují žádná speciální velká písmena. Aplikaci zásad lze vyloučit na úrovni oboru názvů.
| Rozsah zásad | Zásady zabezpečení pod oborem názvů nejsou. | Šablony omezení používané Azure Policy nejsou obor názvů.
| Akce odepřít/audit/mutace | Zásady zabezpečení pod podporují jenom akce Deny. Pro žádosti o vytvoření se dá provést další provedení s výchozími hodnotami. Ověřování lze provést během požadavků na aktualizaci.| Azure Policy podporuje akce zakázat &. Mutace se ještě nepodporují, ale byly plánované.
| Dodržování předpisů zásad zabezpečení pod | Neexistují žádné informace o dodržování předpisů lusky, které existovaly před povolením zásad zabezpečení. Neodpovídající lusky vytvořené po povolení zásad zabezpečení v případě odepření. | Neodpovídající lusky, které existovaly před použitím zásad Azure, se budou zobrazovat v porušení zásad. Neodpovídající lusky vytvořené po povolení zásad Azure se odepře, pokud jsou zásady nastavené s použitím efektu odepření.
| Postup zobrazení zásad v clusteru | `kubectl get psp` | `kubectl get constrainttemplate`– Vrátí se všechny zásady.
| Pod standardem zásady zabezpečení – privilegované | Při povolení této funkce se ve výchozím nastavení vytvoří prostředek zásad zabezpečení s oprávněním pod. | Privilegovaný režim nezahrnuje žádné omezení. Výsledkem je, že nemusíte mít žádné Azure Policy přiřazení.
| [Standard zásad zabezpečení/Standardní – Standardní hodnota](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | Uživatel nainstaluje základní zdroj zásad zabezpečení. | Azure Policy poskytuje [integrovanou iniciativu podle směrného plánu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) , která se mapuje na základní zásady zabezpečení podle směrného plánu.
| [V případě zásad zabezpečení s omezením úrovně Standard](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | Uživatel nainstaluje prostředek pod omezením zásad zabezpečení. | Azure Policy poskytuje [integrovaný s omezenou iniciativou](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) , která se mapuje na zásadu zabezpečení s omezením pod.

## <a name="next-steps"></a>Další kroky

Tento článek ukazuje, jak použít zásadu Azure, která omezuje nasazování privilegovaných lusků, aby nedocházelo k použití privilegovaného přístupu. Existuje mnoho zásad, které je možné použít, například ty, které omezují použití svazků. Další informace o dostupných možnostech najdete v článku [Azure Policy pro referenční dokumenty Kubernetes][kubernetes-policy-reference].

Další informace o omezování síťového provozu najdete v tématu [zabezpečení provozu mezi lusky pomocí zásad sítě v AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-samples]: policy-samples.md#microsoftcontainerservice
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-disable-addons]: /cli/azure/aks#az-aks-disable-addons
[az-policy-assignment-delete]: /cli/azure/policy/assignment#az-policy-assignment-delete