---
title: Vytvoření pracovního prostoru Log Analytics pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit pracovní prostor Log Analytics umožňuje správu řešení a shromažďování dat z vašich cloudových a místních prostředích pomocí Azure CLI.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: magoedte
ms.openlocfilehash: 4be33b809ee2e620a565c9907a5b77833a279567
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66130385"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Vytvoření pracovního prostoru Log Analytics pomocí Azure CLI 2.0

Azure CLI 2.0 slouží k vytváření a správě prostředků Azure z příkazového řádku nebo ve skriptech. V tomto rychlém startu se dozvíte, jak pomocí Azure CLI 2.0 k nasazení pracovního prostoru Log Analytics ve službě Azure Monitor. Pracovní prostor Log Analytics je jedinečný prostředí pro data protokolů Azure Monitor. Každý pracovní prostor má své vlastní úložiště dat a konfigurace a konfigurace zdroje dat a řešení pro ukládání dat v konkrétním pracovním prostoru. Budete potřebovat pracovní prostor Log Analytics, pokud máte v úmyslu na shromažďování dat z těchto zdrojů:

* Prostředky ve vašem předplatném Azure  
* Místní služba System Center Operations Manager monitorovat počítače  
* Kolekce zařízení ze System Center Configuration Manager  
* Diagnostika nebo protokolování dat z úložiště Azure  
 
U jiných zdrojů, jako jsou virtuální počítače Azure a Windows nebo virtuální počítače s Linuxem ve vašem prostředí naleznete v následujících tématech:

* [Shromažďování dat z virtuálních počítačů Azure](../learn/quick-collect-azurevm.md)
* [Shromažďování dat z počítače s Linuxem hybridní](../learn/quick-collect-linux-computer.md)
* [Shromažďování dat z počítače Windows hybridní](quick-collect-windows-computer.md)

Pokud ještě nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) předtím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0.30 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
Vytvoření pracovního prostoru s [vytvořit nasazení skupiny pro az](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). Následující příklad vytvoří pracovní prostor s názvem *TestWorkspace* ve skupině prostředků *Lab* v *eastus* pomocí šablony Resource Manageru z vašeho místního umístění počítač. Šablona JSON je nakonfigurován pouze s výzvou k zadání názvu pracovního prostoru a určí výchozí hodnotu pro parametry, které se pravděpodobně použije jako standardní konfigurace ve vašem prostředí. Nebo můžete šablonu uložte v účtu úložiště Azure pro zajištění sdíleného přístupu ve vaší organizaci. Další informace o práci se šablonami najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Informace o oblasti, které podporuje, najdete v části [Log Analytics je dostupná v oblastech](https://azure.microsoft.com/regions/services/) a vyhledejte Azure monitoru a z **hledání produktu** pole. 

Následující parametry nastavení výchozí hodnoty:

* umístění – výchozí hodnota je USA – východ
* Skladová položka – výchozí hodnota je novou cenovou úroveň Per GB vydáno v dubnu 2018 cenový model

>[!WARNING]
>Pokud vytváříte nebo konfigurace pracovního prostoru Log Analytics v rámci předplatného, který je zapojen do nové platný od dubna 2018 cenový model, platné pouze v Log Analytics cenová úroveň je **PerGB2018**. 
>

### <a name="create-and-deploy-template"></a>Vytvoření a nasazení šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Upravte šablonu podle svých požadavků. Kontrola [Microsoft.OperationalInsights/workspaces šablony](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) referenční dokumentace se dozvíte, jaké vlastnosti a hodnoty jsou podporovány. 
3. Uložte soubor jako **deploylaworkspacetemplate.json** do místní složky.   
4. Jste připraveni k nasazení této šablony. Ze složky obsahující šablonu použijte následující příkazy:

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, který obsahuje výsledek:

![Příklad výsledků po dokončení nasazení](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Další postup
Teď, když máte k dispozici pracovní prostor, konfigurace shromažďování dat monitorování telemetrických dat a spustit prohledávání protokolů pro analýzu těchto dat přidat řešení správy, které poskytují další data a analytické přehledy.  

* Pokud chcete povolit shromažďování dat z prostředků Azure pomocí diagnostiky Azure nebo do úložiště Azure, najdete v článku [metriky pro použití v Log Analytics a Azure shromažďovat protokoly služby](../platform/collect-azure-metrics-logs.md).  
* Přidat [System Center Operations Manager jako zdroj dat](../platform/om-agents.md) shromažďovat data z agenty posílající sestavy skupině pro správu nástroje Operations Manager a uloží je v pracovním prostoru Log Analytics.  
* Připojit [nástroje Configuration Manager](../platform/collect-sccm.md) importovat počítače, které jsou členy kolekce v hierarchii.  
* Zkontrolujte [řešení monitorování](../insights/solutions.md) k dispozici a jak přidat nebo odebrat některé řešení z pracovního prostoru.
