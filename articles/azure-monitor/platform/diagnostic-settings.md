---
title: Pomocí nastavení diagnostiky Shromážděte metriky a protokoly platforem a v Azure.
description: Pomocí nastavení diagnostiky můžete posílat metriky a protokoly platformy Azure Monitor do protokolů Azure Monitor, úložiště Azure nebo Azure Event Hubs.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681182"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik prostředků v Azure

[Protokoly platforem](platform-logs-overview.md) v Azure, včetně protokolů aktivit Azure a protokolů prostředků, poskytují podrobné informace o diagnostice a auditování pro prostředky Azure a platformu Azure, na které jsou závislé. [Metriky platformy](data-platform-metrics.md) se ve výchozím nastavení shromažďují a obvykle se ukládají do databáze Azure monitor metrik.

Tento článek poskytuje podrobné informace o vytváření a konfiguraci nastavení diagnostiky pro odesílání metrik platforem a protokolů platforem do různých umístění.

> [!IMPORTANT]
> Před vytvořením nastavení diagnostiky ke shromáždění protokolu aktivit byste nejdřív měli zakázat všechny starší konfigurace. Podrobnosti najdete v tématu [shromáždění protokolu aktivit Azure se staršími nastaveními](diagnostic-settings-legacy.md) .

Každý prostředek Azure vyžaduje vlastní nastavení diagnostiky, které definuje následující kritéria:

- Kategorie protokolů a data metriky, které jsou odesílány do cílů definovaných v nastavení. Dostupné kategorie se budou lišit pro různé typy prostředků.
- Jeden nebo více cílů pro odeslání protokolů. Aktuální cíle zahrnují Log Analytics pracovní prostor, Event Hubs a Azure Storage.

Jedno diagnostické nastavení může definovat více než jedno z cílů. Pokud chcete odesílat data do více než jednoho konkrétního cílového typu (například dvou různých Log Analytics pracovních prostorů), vytvořte více nastavení. Každý prostředek může mít až 5 nastavení diagnostiky.

> [!NOTE]
> [Metriky platformy](metrics-supported.md) se shromažďují automaticky, aby se [Azure monitor metriky](data-platform-metrics.md). Nastavení diagnostiky lze použít ke shromáždění metrik pro určité služby Azure do protokolů Azure Monitor k analýze s ostatními daty monitorování pomocí [dotazů protokolu](../log-query/log-query-overview.md).

## <a name="destinations"></a>Cíle

Protokoly a metriky platformy je možné odeslat do cílových umístění v následující tabulce. Podrobnosti o odesílání dat do tohoto cíle získáte podle každého odkazu v následující tabulce.

| Cíl | Popis |
|:---|:---|
| [Pracovní prostor služby Log Analytics](resource-logs-collect-workspace.md) | Shromažďování protokolů a metrik do pracovního prostoru Log Analytics umožňuje jejich analýzu s dalšími daty monitorování shromážděnými pomocí Azure Monitor pomocí výkonných dotazů protokolu a také k využití dalších Azure Monitorch funkcí, jako jsou výstrahy a vizualizace. |
| [Centra událostí](resource-logs-stream-event-hubs.md) | Odesílání protokolů a metrik do Event Hubs umožňuje streamování dat do externích systémů, jako jsou systémů Siem třetích stran a další řešení Log Analytics. |
| [Účet služby Azure Storage](resource-logs-collect-storage.md) | Archivace protokolů a metrik do účtu služby Azure Storage je užitečná pro audit, statickou analýzu nebo zálohování. V porovnání s protokoly Azure Monitor a pracovním prostorem Log Analytics je úložiště Azure levnější a protokoly se můžou uchovávat po neomezenou dobu. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Vytvořit nastavení diagnostiky v Azure Portal

Nastavení diagnostiky můžete nakonfigurovat v Azure Portal, a to buď z nabídky Azure Monitor, nebo z nabídky pro daný prostředek.

1. Pokud konfigurujete nastavení diagnostiky v Azure Portal závisí na prostředku.

   - U jednoho prostředku klikněte v nabídce prostředku na položku **nastavení diagnostiky** v části **monitorování** .

        ![Nastavení diagnostiky](media/diagnostic-settings/menu-resource.png)

   - Pro jeden nebo více prostředků klikněte na **nastavení diagnostiky** v části **nastavení** v nabídce Azure monitor a potom klikněte na prostředek.

      ![Nastavení diagnostiky](media/diagnostic-settings/menu-monitor.png)

   - V části Protokol aktivit klikněte na **Protokol aktivit** v nabídce **Azure monitor** a pak na **nastavení diagnostiky**. Ujistěte se, že jste zakázali všechny starší konfigurace protokolu aktivit. Podrobnosti najdete v tématu [zakázání existujících nastavení](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) .

        ![Nastavení diagnostiky](media/diagnostic-settings/menu-activity-log.png)

2. Pokud u vybraného prostředku neexistuje žádné nastavení, zobrazí se výzva k vytvoření nastavení. Klikněte na **Přidat nastavení diagnostiky**.

   ![Přidat nastavení diagnostiky – žádná existující nastavení](media/diagnostic-settings/add-setting.png)

   Pokud je u prostředku k dispozici existující nastavení, zobrazí se seznam nastavení, která jsou už nakonfigurovaná. Kliknutím na **Přidat nastavení diagnostiky** můžete přidat nové nastavení nebo **Upravit nastavení** a upravit existující. Každé nastavení nemůže mít více než jeden z cílových typů.

   ![Přidat nastavení diagnostiky – existující nastavení](media/diagnostic-settings/edit-setting.png)

3. Dejte nastavení název, pokud ho ještě nikdo nemá.

    ![Přidat nastavení diagnostiky](media/diagnostic-settings/setting-new-blank.png)

4. **Podrobnosti kategorie (co směrovat)** – zaškrtněte políčko u každé kategorie dat, která chcete odeslat do cílových umístění určených později. Seznam kategorií se u jednotlivých služeb Azure liší.

     - **AllMetrics** směruje metriky platforem prostředku do úložiště protokolů Azure, ale ve formuláři protokolu. Tyto metriky jsou obvykle odesílány pouze do databáze časových řad Azure Monitor metriky. Posílání do úložiště protokolů Azure Monitor (které lze prohledávat prostřednictvím Log Analytics) k jejich integraci do dotazů, které hledají v jiných protokolech. Tato možnost nemusí být k dispozici pro všechny typy prostředků. Pokud je tato podpora podporovaná, [Azure monitor podporované metriky](metrics-supported.md) uvádí, jaké metriky se shromažďují pro typy prostředků.

       > [!NOTE]
       > Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
       >
       > *Například*: metrika ' IOReadBytes ' na blockchain lze prozkoumat a vytvořit graf na úrovni jednotlivých uzlů. Při exportu pomocí nastavení diagnostiky ale vyexportovaná metrika představuje všechny bajty čtení pro všechny uzly.

     - V **protokolech** jsou uvedeny různé kategorie, které jsou k dispozici v závislosti na typu prostředku. Podívejte se na všechny kategorie, které byste chtěli směrovat do cílového umístění.

5. **Podrobnosti o cíli** – zaškrtněte políčko pro všechny cíle. Když zaškrtnete každé políčko, zobrazí se možnosti, které vám umožní přidat další informace.

      ![Odeslání do Log Analytics nebo Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** – zadejte předplatné a pracovní prostor.  Pokud nemáte pracovní prostor, budete [ho muset před pokračováním vytvořit](../learn/quick-create-workspace.md).

    1. **Centra událostí** – zadejte následující kritéria:
       - Předplatné, jehož součástí je centrum událostí
       - Obor názvů centra událostí – Pokud ho ještě nemáte, budete ho muset [vytvořit](../../event-hubs/event-hubs-create.md) .
       - Název centra událostí (volitelné), do kterého se mají posílat všechna data Pokud název nezadáte, vytvoří se v každé kategorii protokolu centrum událostí. Pokud posíláte více kategorií, možná budete chtít zadat název pro omezení počtu vytvořených Center událostí. Podrobnosti najdete v tématu [kvóty a omezení pro Azure Event Hubs](../../event-hubs/event-hubs-quotas.md) .
       - Zásada centra událostí (volitelné) zásada definuje oprávnění, která má mechanismus streamování. Další informace najdete v části [Event-Center-Features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Úložiště** – vyberte předplatné, účet úložiště a zásady uchovávání informací.

        ![Odeslat do úložiště](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Zvažte nastavení zásad uchovávání na 0 a ruční mazání dat z úložiště pomocí naplánované úlohy, aby se předešlo případnému nejasnostem v budoucnu.
        >
        > Pokud používáte úložiště k archivaci, obecně budete chtít, aby vaše data byla delší než 365 dní. Pokud zvolíte zásadu uchovávání informací, která je větší než 0, datum vypršení platnosti se připojí k protokolům v době úložiště. Po uložení se data pro tyto protokoly nedají změnit.
        >
        > Pokud jste například nastavili zásady uchovávání informací pro *aktivity WorkflowRuntime* na 180 dní a pak 24 hodin později nastavíte na 365 dní, budou se protokoly uložené během těchto prvních 24 hodin automaticky odstraňovat po 180 dnů, zatímco všechny další protokoly tohoto typu se po 365 dnech automaticky odstraní. Pokud později změníte zásady uchovávání informací, nevytvoří se prvních 24 hodin protokolů po dobu 365 dnů.

6. Klikněte na **Uložit**.

Po chvíli se nové nastavení objeví v seznamu nastavení tohoto prostředku a protokoly se streamují do zadaných cílů, protože se generují nová data události. Může trvat až 15 minut, než se událost vygeneruje, a když [se zobrazí v pracovním prostoru Log Analytics](data-ingestion-time.md).

## <a name="create-diagnostic-settings-using-powershell"></a>Vytvoření nastavení diagnostiky pomocí PowerShellu

Pomocí rutiny [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) vytvořte nastavení diagnostiky s [Azure PowerShell](powershell-quickstart-samples.md). Popis jeho parametrů naleznete v dokumentaci k této rutině.

> [!IMPORTANT]
> Tuto metodu nelze použít pro protokol aktivit Azure. Místo toho použijte [Vytvoření nastavení diagnostiky v Azure monitor pomocí šablony Správce prostředků](diagnostic-settings-template.md) k vytvoření šablony Správce prostředků a jejím nasazením pomocí prostředí PowerShell.

Následuje příklad rutiny prostředí PowerShell pro vytvoření nastavení diagnostiky pomocí všech tří cílů.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-diagnostic-settings-using-azure-cli"></a>Vytvoření nastavení diagnostiky pomocí Azure CLI

Pomocí příkazu [AZ monitor Diagnostic-Settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) vytvořte nastavení diagnostiky pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Popis jeho parametrů naleznete v dokumentaci k tomuto příkazu.

> [!IMPORTANT]
> Tuto metodu nelze použít pro protokol aktivit Azure. Místo toho použijte možnost [vytvořit nastavení diagnostiky v Azure monitor pomocí šablony Správce prostředků](diagnostic-settings-template.md) k vytvoření šablony Správce prostředků a nasaďte ji pomocí rozhraní příkazového řádku.

Následuje příklad příkazu CLI pro vytvoření nastavení diagnostiky pomocí všech tří cílů.

```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Konfigurace nastavení diagnostiky pomocí REST API

Pokud chcete vytvořit nebo aktualizovat nastavení diagnostiky pomocí [REST API Azure monitor](https://docs.microsoft.com/rest/api/monitor/), přečtěte si téma [nastavení diagnostiky](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) .

### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Konfigurace nastavení diagnostiky pomocí šablony Správce prostředků

V tématu [Vytvoření nastavení diagnostiky v Azure monitor pomocí šablony Správce prostředků](diagnostic-settings-template.md) můžete vytvořit nebo aktualizovat nastavení diagnostiky pomocí šablony Správce prostředků.

## <a name="next-steps"></a>Další kroky

- [Další informace o protokolech platforem Azure](platform-logs-overview.md)
