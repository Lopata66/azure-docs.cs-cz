---
title: Konfigurace výstrah – Azure Portal-Azure Database for PostgreSQL-flexibilní Server
description: Tento článek popisuje, jak nakonfigurovat a přistupovat k výstrahám metrik pro Azure Database for PostgreSQL flexibilní Server z Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: ac252c3898eb014885bf9a6bf6bdedb7db74fb62
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545833"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---flexible-server"></a>Použití Azure Portal k nastavení výstrah pro metriky pro Azure Database for PostgreSQL-flexibilní Server

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

V tomto článku se dozvíte, jak nastavit výstrahy Azure Database for PostgreSQL pomocí Azure Portal. Můžete obdržet upozornění na základě metrik monitorování vašich služeb Azure.

Výstraha se aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte. Tato výstraha se aktivuje při prvním splnění podmínky a následně v případě, že se už podmínka nesplní.

Můžete nakonfigurovat výstrahu, která provede následující akce při triggerech:

* Odesílat e-mailová oznámení správcům služby a spolusprávcům.
* Odešlete e-mail na další e-maily, které zadáte.
* Zavolejte Webhook.

Můžete nakonfigurovat a získat informace o pravidlech výstrah pomocí:

* [Azure Portal](../../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Rozhraní REST API služby Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Vytvoření pravidla výstrahy na metrikě z Azure Portal

1. V [Azure Portal](https://portal.azure.com/)vyberte server Azure Database for PostgreSQL, který chcete monitorovat.

2. V části **monitorování** na bočním panelu vyberte **výstrahy** , jak je znázorněno níže:

   :::image type="content" source="./media/howto-alert-on-metrics/2-alert-rules.png" alt-text="Vybrat pravidla výstrah":::

3. Vyberte **Přidat upozornění metriky** (+ ikona).

4. Otevře se stránka **vytvořit pravidlo** , jak je znázorněno níže. Vyplňte požadované informace:

   :::image type="content" source="./media/howto-alert-on-metrics/4-add-rule-form.png" alt-text="Vybrat pravidla výstrah":::

5. V části **Podmínka** vyberte **Přidat podmínku** .

6. Vyberte metriku ze seznamu signálů, na kterých se má upozornit. V tomto příkladu vyberte "úložiště v procentech".

   :::image type="content" source="./media/howto-alert-on-metrics/6-configure-signal-logic.png" alt-text="Vybrat pravidla výstrah":::

7. Nakonfigurujte logiku výstrahy včetně **podmínky** (např. "Větší než"), **prahová hodnota** (ex. 85 procent), **Časová agregace** , časový **interval** , po který musí být pravidlo metriky splněno před triggery výstrahy (např. Za posledních 30 minut a **frekvence** .

   Po dokončení vyberte **Hotovo** .

   :::image type="content" source="./media/howto-alert-on-metrics/7-set-threshold-time.png" alt-text="Vybrat pravidla výstrah":::

8. V části **skupiny akcí** vyberte **vytvořit novou** a vytvořte novou skupinu pro příjem oznámení o výstraze.

9. Vyplňte formulář přidat skupinu akcí s názvem, krátkým názvem, předplatným a skupinou prostředků.

10. Nakonfigurujte typ akce **e-mail/SMS/Push/Voice** .

    1. Zvolte možnost Azure Resource Manager role e-mailu a vyberte vlastníky, přispěvatele a čtenáři předplatného, kteří budou dostávat oznámení.

    2. V případě potřeby zadejte do pole **Webhooku** platný identifikátor URI, pokud chcete, aby byla vyvolána, když se výstraha aktivuje.

    3. Po dokončení vyberte **OK** .

    :::image type="content" source="./media/howto-alert-on-metrics/10-action-group-type.png" alt-text="Vybrat pravidla výstrah":::

11. Zadejte název, popis a závažnost pravidla výstrahy.

    :::image type="content" source="./media/howto-alert-on-metrics/11-name-description-severity.png" alt-text="Vybrat pravidla výstrah"::: 

12. Vyberte **vytvořit pravidlo výstrahy** a vytvořte výstrahu.

    Během několika minut je výstraha aktivní a triggery, jak je popsáno výše.

## <a name="manage-your-alerts"></a>Správa výstrah

Jakmile vytvoříte výstrahu, můžete ji vybrat a provést následující akce:

* Zobrazení grafu znázorňujícího prahovou hodnotu metriky a skutečné hodnoty z předchozího dne, které se týkají této výstrahy.
* **Upravte** nebo **odstraňte** pravidlo výstrahy.
* Pokud chcete dočasně zastavit nebo obnovit přijímání oznámení, **zakažte** nebo **Povolte** výstrahu.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [konfiguraci webhooků v upozorněních](../../azure-monitor/platform/alerts-webhooks.md).
* Získejte [Přehled o kolekci metrik](../../azure-monitor/platform/data-platform.md) , abyste měli jistotu, že je vaše služba dostupná a reaguje.