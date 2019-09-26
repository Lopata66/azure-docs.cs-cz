---
title: Co je Azure Scheduler? | Dokumenty Microsoft
description: Zjistěte, jak vytvářet, plánovat a spouštět automatizované úlohy volající služby v i mimo Azure.
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 2f418a78f80d65cbb784685804a4cc6790c28b99
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300909"
---
# <a name="what-is-azure-scheduler"></a>Co je Azure Scheduler?

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje [vyřazení](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)Azure Scheduleru. Pokud chcete pokračovat v práci s úlohami, které jste nastavili v plánovači, [migrujte prosím na Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) co nejdříve.

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/) pomáhá vytvářet [úlohy](../scheduler/scheduler-concepts-terms.md) spouštěné v cloudu tím, že umožňuje deklarativně popisovat akce. Služba pak tyto akce automaticky plánuje a spouští. Můžete například volat služby v i mimo Azure, například koncové body HTTP nebo HTTPS, a také odesílat zprávy do front služby Azure Storage a front nebo témat služby Azure Service Bus. Úlohy můžete spouštět okamžitě nebo později. Scheduler bez problémů podporuje [komplexní plánování a pokročilé opakování](../scheduler/scheduler-advanced-complexity.md). Scheduler určuje, kdy se mají úlohy spouštět, uchovává historii výsledků úloh, kterou můžete procházet, a pak předvídatelně a spolehlivě plánuje úlohy, které se mají spustit.

I když můžete pomocí služby Scheduler vytvářet, spravovat a spouštět plánované úlohy, Scheduler úlohy nehostuje ani nespouští kód. Tato služba pouze *volá* služby nebo kód hostované jinde, například v Azure, v místním prostředí nebo u jiného poskytovatele. Scheduler může provádět volání přes HTTP, HTTPS, frontu úložiště, frontu služby Service Bus nebo téma služby Service Bus. K vytváření, správě a plánování úloh můžete použít [Azure Portal](../scheduler/scheduler-get-started-portal.md), kód, [rozhraní REST API služby Scheduler](https://docs.microsoft.com/rest/api/scheduler/) nebo [rutiny PowerShellu pro službu Azure Scheduler](scheduler-powershell-reference.md). Prostřednictvím kódu programu například můžete vytvářet, zobrazovat, aktualizovat, spravovat nebo odstraňovat úlohy a [kolekce úloh](../scheduler/scheduler-concepts-terms.md) pomocí skriptů a na webu Azure Portal.

Službu Scheduler používají na pozadí také další plánovací nástroje Azure, například [Azure WebJobs](../app-service/webjobs-create.md), což je funkce [Web Apps](https://azure.microsoft.com/services/app-service/web/) ve službě Azure App Service. Komunikaci pro tyto akce můžete spravovat pomocí [rozhraní REST API služby Scheduler](https://docs.microsoft.com/rest/api/scheduler/). Toto rozhraní pomáhá spravovat komunikaci pro tyto akce.

Tady je několik scénářů, ve kterých vám Scheduler může pomoct:

* **Spustit opakující se akce aplikace**: Například pravidelně Shromážděte data z Twitteru do informačního kanálu.

* **Provést každodenní údržbu**: Například vyřazení protokolů denně, provádění záloh a další úlohy údržby. 

  Jako správce například můžete chtít každý den v 1:00 po dobu dalších devíti měsíců zálohovat databázi.

## <a name="next-steps"></a>Další kroky

* [Začínáme se službou Scheduler na webu Azure Portal](scheduler-get-started-portal.md)
* Další informace o [plánech a fakturaci služby Azure Scheduler](scheduler-plans-billing.md)
* Informace o [sestavování komplexních plánů a pokročilého opakování ve službě Azure Scheduler](scheduler-advanced-complexity.md)