---
title: Řešení potíží s Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, jak můžete řešit problémy a řešit problémy s Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: bc4105dc23445c29364961501f93e42f8c3b683d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800439"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Řešení potíží s Azure Monitor pro kontejnery

Když nakonfigurujete monitorování clusteru Azure Kubernetes Service (AKS) s Azure Monitor pro kontejnery, může dojít k potížím s ochranou stavu shromažďování dat nebo vytváření sestav. Tento článek podrobně popisuje některé běžné problémy a postup řešení potíží.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Chyba autorizace během připojování nebo operace aktualizace

Když povolíte Azure Monitor pro kontejnery nebo aktualizujete cluster tak, aby podporoval shromažďování metrik, může se zobrazit chyba podobná následujícímu – *klient <Identity> s ID objektu <uživatele objectId> nemá autorizaci k provedení akce Microsoft. Authorization/roleAssignments/Write v rámci oboru* .

Při připojování nebo aktualizaci se u prostředku clusteru pokusy o přiřazení role **vydavatele metrik monitorování** . Uživatel iniciující proces povolení Azure Monitor pro kontejnery nebo aktualizace pro podporu kolekce metrik musí mít přístup k oprávněním **Microsoft. Authorization/roleAssignments/Write** v oboru prostředků clusteru AKS. Přístup k tomuto oprávnění mají udělit jenom členové předdefinovaných rolí **vlastník** a **Správce přístupu uživatelů** . Pokud vaše zásady zabezpečení vyžadují přiřazení podrobných oprávnění na úrovni, doporučujeme zobrazit [vlastní role](../../role-based-access-control/custom-roles.md) a přiřadit je uživatelům, kteří je potřebují.

Tuto roli můžete také ručně udělit z Azure Portal provedením následujících kroků:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Kubernetes**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Azure Kubernetes**.
3. V seznamu clusterů Kubernetes vyberte jeden ze seznamu.
2. V nabídce na levé straně klikněte na **řízení přístupu (IAM)**.
3. Vyberte **+ Přidat** , chcete-li přidat přiřazení role a vybrat roli **vydavatele metrik monitorování** a v poli **Vybrat** **AKS** zadejte příkaz pro filtrování výsledků jenom v clusterových objektech definovaných v předplatném. Vyberte jednu ze seznamu, která je specifická pro daný cluster.
4. Kliknutím na **Uložit** dokončete přiřazení role.

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Azure Monitor pro kontejnery jsou povolené, ale nehlásí žádné informace.

Pokud je Azure Monitor pro kontejnery úspěšně zapnuté a nakonfigurované, ale nemůžete zobrazit informace o stavu nebo nejsou vráceny žádné výsledky z dotazu protokolu, Diagnostikujte problém pomocí následujících kroků:

1. Ověřte stav agenta spuštěním příkazu:

    `kubectl get ds omsagent --namespace=kube-system`

    Výstup by měl vypadat podobně jako v následujícím příkladu, což znamená, že byl správně nasazen:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Pokud máte uzly Windows serveru, zkontrolujte stav agenta spuštěním příkazu:

    `kubectl get ds omsagent-win --namespace=kube-system`

    Výstup by měl vypadat podobně jako v následujícím příkladu, což znamená, že byl správně nasazen:

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. Pomocí příkazu ověřte stav nasazení pomocí agenta verze *06072018* nebo novějšího:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Výstup by měl vypadat podobně jako v následujícím příkladu, což znamená, že byl správně nasazen:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Zkontrolujte stav pod, abyste ověřili, že je spuštěný, pomocí příkazu:`kubectl get pods --namespace=kube-system`

    Výstup by měl vypadat jako v následujícím příkladu se stavem *spuštěným* pro omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

5. Ověřte protokoly agenta. Po nasazení kontejnerového agenta se spustí rychlá kontroly spuštěním příkazů OMI a zobrazí se verze agenta a poskytovatele.

6. Chcete-li ověřit, zda byl agent úspěšně nasazen, spusťte příkaz:`kubectl logs omsagent-484hw --namespace=kube-system`

    Stav by měl vypadat podobně jako v následujícím příkladu:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>Chybové zprávy

Následující tabulka shrnuje známé chyby, se kterými se můžete setkat při používání Azure Monitor pro kontejnery.

| Chybové zprávy  | Akce |
| ---- | --- |
| Chybová zpráva`No data for selected filters`  | Vytvoření toku dat monitorování pro nově vytvořené clustery může nějakou dobu trvat. Pro zobrazení dat pro váš cluster povolte aspoň 10 až 15 minut. |
| Chybová zpráva`Error retrieving data` | I když je cluster služby Azure Kubernetes nastaven pro monitorování stavu a výkonu, připojení mezi clusterem a pracovním prostorem Azure Log Analytics je navázáno. Pracovní prostor Log Analytics slouží k ukládání všech dat monitorování pro váš cluster. K této chybě může dojít, když byl pracovní prostor Log Analytics odstraněn. Kontrola, zda byl pracovní prostor odstraněn a v případě potřeby bude nutné znovu povolit monitorování clusteru pomocí Azure Monitor pro kontejnery a zadat existující nebo vytvořit nový pracovní prostor. Pokud ho chcete znovu povolit, budete muset [Zakázat](container-insights-optout.md) monitorování clusteru a [Povolit](container-insights-enable-new-cluster.md) Azure monitor pro kontejnery znovu. |
| `Error retrieving data`Po přidání Azure Monitor pro kontejnery pomocí AZ AKS CLI | Pokud povolíte monitorování pomocí `az aks cli` , Azure monitor pro kontejnery pravděpodobně nebudou správně nasazeny. Ověřte, zda je řešení nasazeno. Provedete to tak, že přejdete do svého pracovního prostoru Log Analytics a zjistíte, jestli je řešení dostupné, a to tak, že v podokně na levé straně vyberete **řešení** . Chcete-li tento problém vyřešit, budete muset řešení znovu nasadit podle pokynů pro [nasazení Azure monitor pro kontejnery](container-insights-onboard.md) . |

Abychom vám pomohli diagnostikovat problém, máme [tady](https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/troubleshoot/TroubleshootError_nonAzureK8s.ps1)k dispozici skript pro řešení potíží.

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Azure Monitor pro kontejnery agentů ReplicaSet se neplánují na Kubernetes cluster mimo Azure.

Azure Monitor for Containers agent ReplicaSet lusky má závislost na následujících selektorech uzlů v uzlech pracovního procesu (nebo agenta) pro plánování:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Pokud k vašim pracovním uzlům nejsou připojené popisky uzlů, pak se agent ReplicaSet neplánuje. Pokyny k připojení popisku najdete v tématu [Kubernetes Assign pro selektory Label](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) .

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Grafy výkonu nezobrazují PROCESORy nebo paměť uzlů a kontejnerů v clusteru mimo Azure.

Azure Monitor for Containers agent lusky pomocí koncového bodu cAdvisor v agentovi uzlu shromažďují metriky výkonu. Ověřte, že je agent s kontejnerem na uzlu nakonfigurovaný tak, aby umožňoval `cAdvisor port: 10255` jeho otevírání na všech uzlech v clusteru za účelem shromažďování metrik výkonu.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Cluster s Kubernetes, který není v Azure, se v Azure Monitor pro kontejnery nezobrazuje.

Pokud chcete zobrazit cluster Kubernetes mimo Azure v Azure Monitor for Containers, vyžaduje se v pracovním prostoru Log Analytics přístup pro čtení, který podporuje tento přehled a v ContainerInsights prostředků řešení Container Insights **(*pracovní prostor*)**.

## <a name="next-steps"></a>Další kroky

Díky monitorování, které povoluje zachycení metrik stavu pro uzly clusteru AKS a lusky, jsou tyto metriky stavu dostupné v Azure Portal. Informace o tom, jak používat Azure Monitor pro kontejnery, najdete v tématu [zobrazení stavu služby Azure Kubernetes](container-insights-analyze.md).
