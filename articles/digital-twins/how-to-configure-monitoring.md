---
title: Jak nakonfigurovat monitorování v Azure digitální dvojče | Dokumentace Microsoftu
description: Jak nakonfigurovat monitorování v Azure digitální dvojče.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 23759a6c3d920e2b791a10ddd5ac5c5285ed1889
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926611"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Jak nakonfigurovat monitorování v Azure digitální dvojče

Azure digitální dvojče podporuje robustní protokolování, monitorování a analýzy. Řešení mohou vývojáři protokoly Azure monitoru, diagnostické protokoly, protokolování aktivit a dalších služeb pro podporu komplexní monitorování potřeb aplikace IoT. Možnosti protokolování můžete kombinovat, zadat dotaz nebo zobrazení záznamů napříč několika službami a poskytuje podrobné protokolování pokrytí u řady služeb.

Tento článek shrnuje protokolování a monitorování možnosti a zkombinujte je způsoby specifické pro digitální dvojče Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Zkontrolujte protokoly aktivit

Azure [protokoly aktivit](../azure-monitor/platform/activity-logs-overview.md) poskytují rychlé přehledy o vašich Historie událostí a operace úroveň předplatného pro každou instanci služby Azure.

Událostí na úrovni předplatného patří:

* Vytvoření prostředku
* Odstranění prostředků
* Vytvoření tajných kódů aplikace
* Integrace s jinými službami

Protokolování aktivit pro digitální dvojče Azure je ve výchozím nastavení povolené a najdete na webu Azure portal podle:

1. Výběr vaší instance digitální dvojče Azure.
1. Výběr **protokolu aktivit** zobrazíte panel zobrazení:

    ![Protokol aktivit][1]

Pro pokročilé protokolování aktivit:

1. Vyberte **protokoly** možnosti se zobrazí **přehled Activity Log Analytics**:

    ![Výběr][2]

1. **Přehled Activity Log Analytics** sumarizuje data protokolu základní aktivity:

    ![Přehled Activity log analytics][3]

>[!TIP]
>Použití **protokoly aktivit** rychlé přehledy o vašich událostí na úrovni předplatného.

## <a name="enable-customer-diagnostic-logs"></a>Povolení diagnostických protokolů zákazníka

Azure [nastavení diagnostiky](../azure-monitor/platform/diagnostic-logs-overview.md) lze nastavit pro každou instanci Azure k doplnění protokolování aktivit. Když protokoly aktivit se týkají událostí na úrovni předplatného, protokolování diagnostiky poskytuje přehled o provozní historie tyto prostředky.

Příklady protokolování diagnostiky:

* Čas spuštění pro uživatelem definované funkce
* Stavový kód odpovědi úspěšného požadavku rozhraní API
* Načítání klíče aplikace z trezoru

Povolení diagnostických protokolů pro instanci:

1. Otevřete prostředek na webu Azure portal.
1. Klikněte na tlačítko **nastavení diagnostiky**:

    ![Nastavení diagnostiky jeden][4]

1. Klikněte na tlačítko **zapnout diagnostiku** ke shromažďování dat (pokud už dříve povolené).
1. Vyplňte požadovaná pole a vyberte, jak a kde se uloží data:

    ![Dvě nastavení diagnostiky][5]

    Diagnostické protokoly jsou často ukládají pomocí [Azure File Storage](../storage/files/storage-files-deployment-guide.md) a sdílené s [protokoly Azure monitoru](../azure-monitor/log-query/get-started-portal.md). Obě možnosti se dají vybrat.

>[!TIP]
>Použití **diagnostické protokoly** postřehy operací s prostředky.

## <a name="azure-monitor-and-log-analytics"></a>Azure monitor a log analytics

Aplikace IoT seskupení různorodých zdrojů, zařízení, umístění a dat do jednoho. Podrobné protokolování poskytuje podrobné informace o každé konkrétní, služba nebo komponenta architektury aplikací, ale jednotný přehled, je často nutné na údržbu a ladění.

Azure Monitor zahrnuje služby výkonné log analytics, která umožňuje protokolování zdrojů a zobrazit a analyzovat na jednom místě. Azure Monitor je proto velmi užitečné pro analýzu protokolů v rámci sofistikovaných aplikací IoT.

Příklady použití:

* Dotazování více diagnostických protokolů historie
* Zobrazení protokolů pro několik uživatelsky definovaných funkcí
* Zobrazení protokolů pro dvě nebo více služeb v určitém časovém limitu

Dotazování na celý protokol je poskytována prostřednictvím [protokoly Azure monitoru](../azure-monitor/log-query/log-query-overview.md). Výkonné funkce, které tato nastavení:

1. Vyhledejte **Log Analytics** na webu Azure Portal.
1. Uvidí vaši dostupný **pracovní prostor Log Analytics** instancí. Vyberte jednu a vyberte **protokoly** dotazu:

    ![Log Analytics][6]

1. Pokud ještě nemáte **pracovní prostor Log Analytics** instance, můžete vytvořit pracovní prostor kliknutím **přidat** tlačítka:

    ![Vytváření OMS][7]

Jednou vaše **pracovní prostor Log Analytics** zřídí instance, můžete použít zadávat efektivní dotazy na hledání v protokolech násobky položky nebo pomocí určitých kritérií pomocí **Správa protokolů**:

   ![Správa protokolů][8]

Další informace o operacích výkonný dotazovací najdete v tématu [Začínáme s dotazy](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Může dojít ke zpoždění 5 minut při odesílání událostí do **pracovní prostor Log Analytics** poprvé.

Protokoly Azure monitoru také poskytuje výkonné chyb a oznámení výstrah služeb, které můžete zobrazit kliknutím **diagnostikovat a řešit problémy**:

   ![Oznámení o upozornění a chyby][9]

>[!TIP]
>Použití **pracovní prostor Log Analytics** do historie protokolu dotazu více funkce aplikace, předplatných a služeb.

## <a name="other-options"></a>Další možnosti

Azure digitální dvojče také podporuje specifické pro aplikaci protokolování a auditování zabezpečení. Důkladný přehled k vaší instanci Azure digitální dvojče k dispozici všechny možnosti protokolování Azure, najdete v článku [Azure protokolu auditu](../security/azure-log-audit.md) článku.

## <a name="next-steps"></a>Další postup

- Další informace o Azure [protokoly aktivit](../azure-monitor/platform/activity-logs-overview.md).

- Ponořte se hlouběji do nastavení diagnostiky služby Azure ve čtení [přehled diagnostických protokolů](../azure-monitor/platform/diagnostic-logs-overview.md).

- Další informace o [protokoly Azure monitoru](../azure-monitor/log-query/get-started-portal.md).

<!-- Images -->
[1]: media/how-to-configure-monitoring/activity-log.png
[2]: media/how-to-configure-monitoring/activity-log-select.png
[3]: media/how-to-configure-monitoring/log-analytics-overview.png
[4]: media/how-to-configure-monitoring/diagnostic-settings-one.png
[5]: media/how-to-configure-monitoring/diagnostic-settings-two.png
[6]: media/how-to-configure-monitoring/log-analytics.png
[7]: media/how-to-configure-monitoring/log-analytics-oms.png
[8]: media/how-to-configure-monitoring/log-analytics-management.png
[9]: media/how-to-configure-monitoring/log-analytics-notifications.png
