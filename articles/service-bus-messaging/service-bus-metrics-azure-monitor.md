---
title: Azure Service Bus metriky v Azure Monitor | Microsoft Docs
description: Tento článek vysvětluje, jak pomocí Azure Monitor monitorovat Service Bus entit (fronty, témata a odběry).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 57b791e67157908447956a14fae99545843f3bc0
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85340288"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Service Bus metriky v Azure Monitor

Service Bus metriky poskytují stav prostředků ve vašem předplatném Azure. S bohatou sadou dat metrik můžete posoudit celkový stav svých Service Busch prostředků, nejen na úrovni oboru názvů, ale také na úrovni entity. Tyto statistiky můžou být důležité, protože vám pomohou monitorovat stav Service Bus. Metriky taky můžou pomoct řešit problémy s hlavními příčinami, aniž byste museli kontaktovat podporu Azure.

Azure Monitor poskytuje jednotná uživatelská rozhraní pro monitorování napříč různými službami Azure. Další informace najdete v tématu [monitorování v Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) a na webu GitHub [s ukázkou načítání Azure monitor metriky s rozhraním .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) Sample.

> [!IMPORTANT]
> Pokud nedošlo k žádné interakci s entitou 2 hodiny, metrika se začne zobrazovat jako hodnota 0, dokud entita nebude nečinná.

## <a name="access-metrics"></a>Metriky přístupu

Azure Monitor poskytuje více způsobů přístupu k metrikám. Můžete buď přistupovat ke metrikám prostřednictvím [Azure Portal](https://portal.azure.com), nebo použít rozhraní API Azure monitor (Rest a .NET) a analytická řešení, jako jsou Azure monitor protokoly a Event Hubs. Další informace najdete v tématu [metriky v Azure monitor](../azure-monitor/platform/data-platform-metrics.md).

Metriky jsou ve výchozím nastavení povolené a můžete získat přístup k nejnovějším 30 dnům dat. Pokud potřebujete zachovat data po delší dobu, můžete archivovat data metrik na účet Azure Storage. Tato hodnota je nakonfigurována v [nastavení diagnostiky](../azure-monitor/platform/diagnostic-settings.md) v Azure monitor.

## <a name="access-metrics-in-the-portal"></a>Přístup k metrikám na portálu

Metriky můžete monitorovat v průběhu času v [Azure Portal](https://portal.azure.com). Následující příklad ukazuje, jak zobrazit úspěšné požadavky a příchozí požadavky na úrovni účtu:

![][1]

Metriky můžete také přistupovat přímo přes obor názvů. Provedete to tak, že vyberete svůj obor názvů a kliknete na **metriky**. Chcete-li zobrazit metriky filtrované v oboru entity, vyberte entitu a klikněte na možnost **metriky**.

![][2]

Pro metriky podporující dimenze je nutné filtrovat pomocí požadované hodnoty dimenze.

## <a name="billing"></a>Fakturace

Metriky a výstrahy na Azure Monitor se účtují podle jednotlivých výstrah. Tyto poplatky by měly být k dispozici na portálu, když je nastavená výstraha a předtím, než se uloží. 

Další řešení, která ingestují data metrik, se účtují přímo pomocí těchto řešení. Například se fakturuje Azure Storage, Pokud archivujete data metriky na účet Azure Storage. Účtuje se taky Log Analytics, pokud streamuje data metrik pro Log Analytics pro pokročilou analýzu.

Následující metriky poskytují přehled o stavu služby. 

> [!NOTE]
> Nepoužíváme několik metrik, protože se přesunou pod jiným názvem. To může vyžadovat, abyste aktualizovali odkazy. Metriky označené klíčovým slovem "zastaralé" nebudou při přeposílání podporovány.

Všechny hodnoty metrik jsou odesílány do Azure Monitor každou minutu. Časové rozlišení definuje časový interval, pro který jsou prezentovány hodnoty metrik. Podporovaný časový interval pro všechny metriky Service Bus je 1 minuta.

## <a name="request-metrics"></a>Metriky žádostí

Spočítá počet požadavků na data a operace správy.

| Název metriky | Popis |
| ------------------- | ----------------- |
| Příchozí žádosti| Počet požadavků provedených ve službě Service Bus v zadaném období. <br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Úspěšné požadavky|Počet úspěšných požadavků provedených ve službě Service Bus v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Chyby serveru|Počet požadavků nezpracovaných z důvodu chyby ve službě Service Bus v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Chyby uživatelů (viz následující pododdíl)|Počet požadavků nezpracovaných z důvodu chyb uživatele v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Omezené žádosti|Počet požadavků, které byly omezeny, protože použití bylo překročeno.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|

### <a name="user-errors"></a>Chyby uživatele

Následující dva typy chyb jsou klasifikovány jako chyby uživatele:

1. Chyby na straně klienta (v HTTP, které by 400 chyby).
2. Chyby, ke kterým dochází při zpracování zpráv, například [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Metriky zpráv

| Název metriky | Popis |
| ------------------- | ----------------- |
|Příchozí zprávy|Počet událostí nebo zpráv odeslaných do Service Bus v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Odchozí zprávy|Počet událostí nebo zpráv přijatých z Service Bus v zadaném období.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
| Zprávy| Počet zpráv ve frontě nebo tématu. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Dimenze: EntityName |
| ActiveMessages| Počet aktivních zpráv ve frontě nebo tématu. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Dimenze: EntityName |
| Nedoručené zprávy| Počet nedoručených zpráv ve frontě nebo tématu <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/>Dimenze: EntityName |
| Naplánované zprávy| Počet naplánovaných zpráv ve frontě nebo tématu. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr  <br/> Dimenze: EntityName |

> [!NOTE]
> Hodnoty pro následující metriky jsou hodnoty v daném časovém okamžiku. Příchozí zprávy, které byly spotřebovány ihned po tomto okamžiku, se nemusí v těchto metrikách odrazit. 
> - Zprávy
> - Aktivní zprávy 
> - Nedoručené zprávy 
> - Naplánované zprávy 

## <a name="connection-metrics"></a>Metriky připojení

| Název metriky | Popis |
| ------------------- | ----------------- |
|ActiveConnections|Počet aktivních připojení v oboru názvů i u entity.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Otevřená připojení |Počet otevřených připojení.<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|
|Uzavřená připojení |Počet uzavřených připojení<br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Dimenze: EntityName|

## <a name="resource-usage-metrics"></a>Metriky využití prostředků

> [!NOTE] 
> Následující metriky jsou k dispozici pouze s úrovní **Premium** . 
> 
> Důležité metriky, které je třeba monitorovat pro výpadky úrovně Premium: **využití CPU na obor názvů** a **velikost paměti na obor názvů**. [Nastavte výstrahy](../azure-monitor/platform/alerts-metric.md) pro tyto metriky pomocí Azure monitor.
> 
> Další metrikou, kterou můžete monitorovat: **omezené požadavky**. Nemělo by se jednat o problém, dokud obor názvů zůstane v rámci omezení paměti, procesoru a zprostředkovaných připojení. Další informace najdete v tématu [omezování v Azure Service Bus úrovně Premium](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier) .

| Název metriky | Popis |
| ------------------- | ----------------- |
|Využití CPU na obor názvů|Procento využití procesoru oboru názvů.<br/><br/> Jednotka: procenta <br/> Typ agregace: maximum <br/> Dimenze: EntityName|
|Využití velikosti paměti na obor názvů|Procento využití paměti oboru názvů.<br/><br/> Jednotka: procenta <br/> Typ agregace: maximum <br/> Dimenze: EntityName|

## <a name="metrics-dimensions"></a>Dimenze metrik

Azure Service Bus podporuje následující dimenze pro metriky v Azure Monitor. Přidávání dimenzí do metrik je volitelné. Pokud dimenze nepřidáte, jsou metriky zadány na úrovni oboru názvů. 

|Název dimenze|Popis|
| ------------------- | ----------------- |
|EntityName| Service Bus podporuje entity zasílání zpráv pod oborem názvů.|

## <a name="set-up-alerts-on-metrics"></a>Nastavení upozornění na metriky

1. Na kartě **metriky** na stránce **Service Bus obor názvů** vyberte **konfigurovat výstrahy**. 

    ![Stránka metriky – konfigurace nabídky výstrahy](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Vyberte možnost **vybrat cíl** a na stránce **Vybrat prostředek** proveďte následující akce: 
    1. V poli **Filtr podle typu prostředku** vyberte **Service Bus obory názvů** . 
    2. Vyberte své předplatné pro pole **filtrovat podle předplatného** .
    3. V seznamu vyberte **obor názvů služby Service Bus** . 
    4. Vyberte **Done** (Hotovo). 
    
        ![Výběr oboru názvů](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Vyberte **Přidat kritéria**a na stránce **Konfigurovat logiku signálu** proveďte následující akce:
    1. Vyberte **metriky** pro **typ signálu**. 
    2. Vyberte signál. Například: **chyby služby**. 

        ![Vybrat chyby serveru](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Vyberte možnost **větší než** pro **podmínku**.
    2. Vyberte **celkem** pro **agregaci času**. 
    3. Jako **prahovou hodnotu**zadejte **5** . 
    4. Vyberte **Done** (Hotovo).    

        ![Zadat podmínku](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Na stránce **vytvořit pravidlo** rozbalte možnost **definovat podrobnosti výstrahy**a proveďte následující akce:
    1. Zadejte **název** výstrahy. 
    2. Zadejte **Popis** výstrahy.
    3. Vyberte **závažnost** výstrahy. 

        ![Podrobnosti upozornění](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Na stránce **vytvořit pravidlo** rozbalte položku **definovat skupinu akcí**, vyberte možnost **Nová skupina akcí**a na **stránce Přidat skupinu akcí**proveďte následující akce. 
    1. Zadejte název skupiny akcí.
    2. Zadejte krátký název skupiny akcí. 
    3. Vyberte své předplatné. 
    4. Vyberte skupinu prostředků. 
    5. Pro tento návod zadejte pro **název akce** **Odeslat e-mail** .
    6. Pro **typ akce**vyberte **e-mail/SMS/nabízený/hlas** . 
    7. Vyberte **Upravit podrobnosti**. 
    8. Na stránce **e-mail/SMS/Push/Voice** proveďte následující akce:
        1. Vyberte **e-mail**. 
        2. Zadejte **e-mailovou adresu**. 
        3. Vyberte **OK**.

            ![Podrobnosti upozornění](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Na stránce **Přidat skupinu akcí** vyberte **OK**. 
1. Na stránce **vytvořit pravidlo** vyberte **vytvořit pravidlo výstrahy**. 

    ![Tlačítko vytvořit pravidlo upozornění](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Další kroky

Podívejte se na [přehled Azure monitor](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


