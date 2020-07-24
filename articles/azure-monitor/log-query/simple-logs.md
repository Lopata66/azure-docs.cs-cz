---
title: Prostředí Simple Logs v Azure Monitor (Preview) | Microsoft Docs
description: Rozhraní Simple log umožňuje vytvářet základní dotazy v Azure Monitor bez přímého působení KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 7bdb0ae813600f53df44ee1a61a4cbbb56bb0bfe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87067500"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Prostředí Simple Logs v Azure Monitor (Preview)
Azure Monitor poskytuje [bohatou zkušenost](get-started-portal.md) pro vytváření [dotazů protokolu](log-query-overview.md) pomocí jazyka KQL. Nemusíte vyžadovat celou sílu KQL, ale a upřednostnit zjednodušené prostředí pro základní požadavky na dotazy. Funkce jednoduchého protokolování umožňuje vytvářet základní dotazy bez přímé interakce s KQL. Jednoduché protokoly můžete použít také jako výukový nástroj pro KQL, protože budete potřebovat složitější dotazy.

> [!NOTE]
> Jednoduché protokoly jsou nyní implementovány jako test pouze pro Cosmos DB a trezory klíčů. Sdílejte prosím své zkušenosti s Microsoftem prostřednictvím [uživatelského hlasu](https://feedback.azure.com/forums/913690-azure-monitor) , abychom zjistili, jestli tuto funkci rozbalíme a uvolníte.


## <a name="scope"></a>Rozsah
Rozhraní Simple log načítá data z tabulky *AzureDiagnostics*, *AzureMetrics*a *AzureActivity* pro vybraný prostředek. 

## <a name="using-simple-logs"></a>Používání jednoduchých protokolů
V předplatném Azure přejděte do libovolného Cosmos DB nebo Key Vault s [diagnostickým nastavením nakonfigurovaným pro shromažďování protokolů v pracovním prostoru Log Analytics](../platform/resource-logs.md#send-to-azure-storage). V nabídce **monitorování** klikněte na **protokoly** a otevřete tak jednoduché možnosti protokolování.

![Nabídka](media/simple-logs/menu.png)

Vyberte **pole** a **operátor** a zadejte **hodnotu** pro porovnání. Kliknutím **+** a zadáním **a/nebo** přidáte další kritéria.

![Kritéria](media/simple-logs/criteria.png)

Kliknutím na tlačítko **Spustit** zobrazíte výsledky dotazu.

## <a name="view-and-edit-kql"></a>Zobrazení a úprava KQL
Vyberte **Editor dotazů** a otevřete tak KQL generované dotazy Simple Logs v úplném prostředí Log Analytics. 

![Editor dotazů](media/simple-logs/query-editor.png)

Můžete přímo upravit KQL a používat další funkce v Log Analytics jako filtry k dalšímu upřesnění výsledků.

![Upravit KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Další kroky

- Dokončete kurz [použití Log Analytics v Azure Portal](get-started-portal.md).
- Dokončete kurz pro [zápis dotazů protokolu](get-started-portal.md).
