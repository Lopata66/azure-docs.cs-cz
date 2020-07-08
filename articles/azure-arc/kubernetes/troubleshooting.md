---
title: Řešení běžných problémů Kubernetes s podporou ARC Azure (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Řešení běžných problémů s Kubernetes clustery s podporou ARC.
keywords: Kubernetes, oblouk, Azure, kontejnery
ms.openlocfilehash: 1527f8d4ca06c2deaf4ce18b73bfdb515dcadc63
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83725580"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>Řešení potíží s Kubernetesem v Azure ARC (Preview)

Tento dokument popisuje některé běžné scénáře řešení potíží s připojením, oprávněními a agenty.

## <a name="general-troubleshooting"></a>Obecné řešení potíží

### <a name="azure-cli-set-up"></a>Nastavení Azure CLI
Než použijete příkaz AZ connectedk8s nebo AZ k8sconfiguration CLI, zajistěte, aby byl AZ nastavený na práci se správným předplatným Azure.

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>agenti Azure – ARC
Všichni agenti pro Kubernetes s povoleným ARC Azure se v oboru názvů nasazují jako lusky `azure-arc` . V rámci normálních operací by měly být spuštěné všechny lusky a musí se předávat kontroly stavu.

Nejdřív ověřte verzi Helm ARC Azure:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Pokud se Helm verze nenajde nebo chybí, zkuste cluster znovu připojit.

Pokud je k dispozici verze Helm a `STATUS: deployed` Určete stav agentů pomocí `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Všechny lusky by `STATUS` se měly zobrazovat jako `Running` a `READY` by měly být buď `3/3` nebo `2/2` . Načte protokoly a popište lusky, které vrací `Error` nebo `CrashLoopBackOff` .

## <a name="unable-to-connect-my-kubernetes-cluster-to-azure"></a>Nepovedlo se připojit svůj cluster Kubernetes k Azure.

Připojení clusterů k Azure vyžaduje přístup k předplatnému Azure a `cluster-admin` přístup k cílovému clusteru. Pokud cluster není dostupný nebo má nedostatečná oprávnění k registraci, dojde k chybě.

### <a name="insufficient-cluster-permissions"></a>Nedostatečná oprávnění clusteru

Pokud zadaný soubor kubeconfig nemá dostatečná oprávnění k instalaci agentů Azure ARC, příkaz Azure CLI vrátí při pokusu o volání rozhraní API Kubernetes chybu.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

Vlastník clusteru by měl používat uživatele Kubernetes s oprávněními správce clusteru.

### <a name="installation-timeouts"></a>Vypršení časových limitů instalace

Instalace agenta Azure ARC vyžaduje spuštění sady kontejnerů v cílovém clusteru. Pokud cluster běží přes pomalé připojení k Internetu, může převzetí služeb při selhání image kontejneru trvat déle než vypršení časových limitů Azure CLI.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

There was a problem with connect-agent deployment. Please run 'kubectl -n azure-arc logs -l app.kubernetes.io/component=connect-agent -c connect-agent' to debug the error.
```

## <a name="configuration-management"></a>Správa konfigurace

### <a name="general"></a>Obecné
Chcete-li pomoct řešit problémy s konfigurací správy zdrojového kódu, spusťte příkaz AZ Commands with--Debug.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>Vytvořit konfiguraci správy zdrojového kódu
Role Přispěvatel v prostředku Microsoft. Kubernetes/connectedCluster je nezbytná a dostatečná pro vytvoření prostředku Microsoft. KubernetesConfiguration/sourceControlConfiguration.

### <a name="configuration-remains-pending"></a>Konfigurace zůstává`Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
