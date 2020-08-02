---
title: Škálování Azure Cosmos DB podle plánu pomocí Azure Functions časovače
description: Naučte se škálovat změny v propustnosti v Azure Cosmos DB pomocí PowerShellu a Azure Functions.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 01/13/2020
ms.author: mjbrown
ms.openlocfilehash: 670a38b48ee89930078078dc4a8ac1a2876648e2
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503731"
---
# <a name="scale-azure-cosmos-db-throughput-by-using-azure-functions-timer-trigger"></a>Škálování Azure Cosmos DB propustnosti pomocí triggeru časovače Azure Functions

Výkon účtu Azure Cosmos vychází z množství zřízené propustnosti vyjádřené v jednotkách žádosti za sekundu (RU/s). Zřizování je druhé členitosti a účtuje se na základě nejvyšších RU/s za hodinu. Tento model zřízené kapacity umožňuje službě poskytovat předvídatelné a konzistentní propustnost, zaručenou nízkou latenci a vysokou dostupnost. Většina produkčních úloh tyto funkce. Ve vývojových a testovacích prostředích, kde se Azure Cosmos DB používá jenom během pracovní doby, si ale můžete horizontální navýšení kapacity škálovat a až do hodin večer po pracovní době škálovat zpátky.

Propustnost můžete nastavit prostřednictvím [šablon Azure Resource Manager](resource-manager-samples.md), [Azure CLI](cli-samples.md)a [PowerShellu](powershell-samples.md)pro Core (SQL) účty rozhraní API nebo pomocí sad SDK pro Azure Cosmos DB konkrétní jazyk. Výhodou použití šablon Správce prostředků, Azure CLI nebo PowerShellu je to, že podporují všechna rozhraní API Azure Cosmos DB modelů.

## <a name="throughput-scheduler-sample-project"></a>Ukázkový projekt plánovače propustnosti

Pro zjednodušení procesu škálování Azure Cosmos DB podle plánu jsme vytvořili ukázkový projekt s názvem [Plánovač propustnosti Azure Cosmos](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler). Tento projekt je Azure Functions aplikace se dvěma aktivačními událostmi časovače – "ScaleUpTrigger" a "ScaleDownTrigger". Aktivační události spouštějí skript prostředí PowerShell, který nastaví propustnost každého prostředku podle definice v `resources.json` souboru v každé aktivační události. ScaleUpTrigger je nakonfigurována tak, aby běžela v rozmezí času UTC, a ScaleDownTrigger je nakonfigurován tak, aby běžel v 6. UTC a tyto časy lze snadno aktualizovat v rámci `function.json` souboru pro každou Trigger.

Tento projekt můžete klonovat místně, upravit ho a zadat Azure Cosmos DB prostředky pro horizontální navýšení a snížení kapacity a plán, který se má spustit. Později je můžete nasadit do předplatného Azure a zabezpečit ho pomocí identity spravované služby s oprávněními [Access Control na základě rolí](role-based-access-control.md) (RBAC) s rolí "Azure Cosmos DB operator" k nastavení propustnosti pro účty Azure Cosmos.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace a Stáhněte si ukázku z [Azure Cosmos DB Scheduler propustnosti](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler).
