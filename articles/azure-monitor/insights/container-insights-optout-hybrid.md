---
title: Postup zastavení monitorování hybridního clusteru Kubernetes | Microsoft Docs
description: Tento článek popisuje, jak můžete zastavit monitorování clusteru hybridního Kubernetes pomocí Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 8369c82b83cfbaa7128383c6203aaf584916cae9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091194"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>Postup zastavení monitorování hybridního clusteru

Po povolení monitorování clusteru Kubernetes můžete zastavit monitorování clusteru pomocí Azure Monitor pro kontejnery, pokud se rozhodnete, že už ho nechcete monitorovat. Tento článek ukazuje, jak to provést v následujících prostředích:

- AKS Engine v Azure a Azure Stack
- OpenShift verze 4 a vyšší
- Kubernetes s podporou Azure Arc (Preview)

## <a name="how-to-stop-monitoring-using-helm"></a>Postup zastavení monitorování pomocí Helm

Následující postup platí pro následující prostředí:

- AKS Engine v Azure a Azure Stack
- OpenShift verze 4 a vyšší

1. Abyste nejdřív identifikovali Azure Monitor pro vydanou verzi grafu Helm pro kontejnery nainstalované na clusteru, spusťte následující příkaz Helm.

    ```
    helm list
    ```

    Výstup bude vypadat přibližně takto:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-Containers-Release-1* představuje verzi grafu helm pro Azure monitor kontejnerů.

2. Chcete-li odstranit vydanou verzi grafu, spusťte následující příkaz Helm.

    `helm delete <releaseName>`

    Příklad:

    `helm delete azmon-containers-release-1`

    Tato akce odebere verzi z clusteru. Ověřit můžete spuštěním `helm list` příkazu:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Dokončení změny konfigurace může trvat několik minut. Vzhledem k tomu, že Helm sleduje vaše verze, i když je odstraníte, můžete auditovat historii clusteru a dokonce zrušit odstranění verze pomocí `helm rollback` .

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>Postup zastavení monitorování Kubernetes s povoleným ARC

### <a name="using-powershell"></a>Pomocí prostředí PowerShell

1. Stáhněte a uložte skript do místní složky, která konfiguruje cluster pomocí doplňku monitorování pomocí následujících příkazů:

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. Nakonfigurujte `$azureArcClusterResourceId` proměnnou tak, že nastavíte odpovídající hodnoty `subscriptionId` pro `resourceGroupName` a `clusterName` zastupujete ID prostředku vašeho prostředku clusteru Kubernetes s podporou ARC Azure.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `$kubeContext`Pomocí příkazu nakonfigurujte proměnnou pomocí **Kube kontextu** clusteru `kubectl config get-contexts` . Pokud chcete použít aktuální kontext, nastavte hodnotu na `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Spusťte následující příkaz, který zastaví monitorování clusteru.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

### <a name="using-bash"></a>Použití bash

1. Stáhněte a uložte skript do místní složky, která konfiguruje cluster pomocí doplňku monitorování pomocí následujících příkazů:

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. Nakonfigurujte `azureArcClusterResourceId` proměnnou tak, že nastavíte odpovídající hodnoty `subscriptionId` pro `resourceGroupName` a `clusterName` zastupujete ID prostředku vašeho prostředku clusteru Kubernetes s podporou ARC Azure.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. `kubeContext`Pomocí příkazu nakonfigurujte proměnnou pomocí **Kube kontextu** clusteru `kubectl config get-contexts` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Pokud chcete zastavit monitorování clusteru, jsou na základě scénáře nasazení k dispozici různé příkazy.

    Spusťte následující příkaz, který zastaví monitorování clusteru pomocí aktuálního kontextu.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Spusťte následující příkaz, který zastaví monitorování clusteru zadáním kontextu.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

## <a name="next-steps"></a>Další kroky

Pokud se pracovní prostor Log Analytics vytvořil jenom pro podporu monitorování clusteru a už ho nepotřebujete, musíte ho ručně odstranit. Pokud nejste obeznámeni s tím, jak pracovní prostor odstranit, přečtěte si téma [odstranění pracovního prostoru Azure Log Analytics](../platform/delete-workspace.md).
