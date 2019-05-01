---
title: Protokoly aktivit v Azure Active Directory ve službě Azure Monitor | Dokumentace Microsoftu
description: Úvod do služby Azure Active Directory aktivit protokolů ve službě Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/22/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b924746c00a438ec4ac81dacc02905565adf30e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682109"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Protokoly aktivit v Azure AD ve službě Azure Monitor

Protokoly aktivit Azure Active Directory (Azure AD) můžete provést směrování na několik koncových bodů pro dlouho období uchovávání dat a dat přehledy. Tato funkce umožňuje:

* Protokoly aktivit archiv služby Azure AD pro účet úložiště Azure pro data uchovávat po delší dobu.
* Protokoly aktivit Stream služby Azure AD do služby Azure event hub Analytics pomocí oblíbených nástrojů informace o zabezpečení a správu událostí (SIEM), jako je například Splunk a QRadar.
* Integrace Azure AD protokolů aktivit se svoje vlastní řešení vlastního protokolu pomocí streamování do centra událostí.
* Protokoly aktivit odeslat Azure AD umožňují přehledné vizualizace, monitorování a upozorňování na připojených dat protokoly Azure monitoru.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Podporované sestavy

Můžete směrování Azure AD auditovat protokoly a protokoly přihlášení k účtu úložiště Azure, Centrum událostí, protokoly Azure monitoru nebo vlastní řešení s použitím této funkce. 

* **Protokoly auditu**: [Sestava aktivit protokolů auditu](concept-audit-logs.md) dává vám přístup k historii každé úlohy, které se provádí ve vašem tenantovi.
* **Protokoly přihlášení**: S [sestavy aktivit přihlašování](concept-sign-ins.md), můžete určit, kdo provedl úlohy, které jsou hlášeny v protokolech auditování.

> [!NOTE]
> Zatím není dostupná podpora protokolů aktivit auditu a přihlašování souvisejících s B2C.
>

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:

* Předplatné Azure. Pokud předplatné Azure nemáte, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* [Licence](https://azure.microsoft.com/pricing/details/active-directory/) Azure AD Free, Basic, Premium 1 nebo Premium 2 pro přístup k protokolům auditu Azure AD na webu Azure Portal. 
* Tenanta Azure AD.
* Uživatele, který je **globálním správcem** nebo **správcem zabezpečení** pro tohoto tenanta Azure AD.
* [Licence](https://azure.microsoft.com/pricing/details/active-directory/) Azure AD Premium 1 nebo Premium 2 pro přístup k protokolům přihlášení Azure AD na webu Azure Portal. 

Podle toho, kam chcete směrovat data protokolů auditu, potřebujete některou z následujících položek:

* Účet úložiště Azure, pro který máte oprávnění *ListKeys*. Doporučujeme použít obecný účet úložiště, ne účet úložiště objektů blob. Informace o cenách úložiště najdete v [cenové kalkulačce služby Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Obor názvů služby Azure Event Hubs pro integraci s řešeními třetích stran.
* K odeslání protokolů s protokoly Azure monitoru pracovnímu prostoru Azure Log Analytics.

## <a name="cost-considerations"></a>Důležité informace o nákladech

Pokud už máte licenci Azure AD, potřebujete k vytvoření účtu úložiště a centra událostí předplatné Azure. Předplatné Azure je zdarma, ale platíte za využívání prostředků Azure, včetně účtu úložiště, který používáte k archivaci, a centra událostí, které používáte ke streamování. Množství dat a tedy i související náklady se můžou výrazně lišit v závislosti na velikosti tenanta. 

### <a name="storage-size-for-activity-logs"></a>Velikost úložiště pro protokoly aktivit

Každá událost protokolu auditu zabere v úložišti dat asi 2 kB. V případě tenanta se 100 000 uživateli, ve kterém dojde asi k 1,5 milionu událostí denně, byste za den spotřebovali asi 3 GB úložiště. Vzhledem k tomu, že k zápisům dochází v dávkách asi po pěti minutách, můžete očekávat asi 9 000 operací zápisu za měsíc. 


Následující tabulka obsahuje odhad nákladů na účet úložiště pro obecné účely v2 v oblasti Západní USA s dobou uchování minimálně jeden rok, v závislosti na velikosti tenanta. Přesnější odhad objemu dat, který můžete ve své aplikaci očekávat, vám poskytne [cenová kalkulačka služby Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).


| Kategorie protokolu | Počet uživatelů | Počet událostí za den | Objem dat za měsíc (odhad) | Náklady za měsíc (odhad) | Náklady za rok (odhad) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Auditování | 100 000 | 1,5&nbsp;milionu | 90 GB | 1,93 USD | 23,12 USD |
| Auditování | 1 000 | 15 000 | 900 MB | 0,02 USD | 0,24 USD |
| Přihlášení | 1 000 | 34 800 | 4 GB | 0,13 USD | 1,56 USD |
| Přihlášení | 100 000 | 15&nbsp;milionů | 1,7 TB | 35,41 USD | 424,92 USD |
 









### <a name="event-hub-messages-for-activity-logs"></a>Zprávy centra událostí pro protokoly aktivit

Události se seskupují do dávek asi po pětiminutových intervalech a odesílají se jako jedna zpráva obsahující všechny události za dané období. Zpráva v centru událostí má maximální velikost 256 kB, a pokud velikost všech zpráv za dané období překročí tento objem, odešle se několik zpráv. 

Například u velkého tenanta s více než 100 000 uživateli běžně dochází k přibližně 18 událostem za sekundu, což odpovídá 5 400 událostem za každých pět minut. Vzhledem k tomu, že protokoly auditu pro každou událost mají velikost přibližně 2 kB, odpovídá toto množství 10,8 MB dat. Proto se v takovém pětiminutovém intervalu do centra událostí odešle 43 zpráv. 

Následující tabulka obsahuje odhad měsíčních nákladů na základní centrum událostí v oblasti Západní USA v závislosti na objemu dat událostí. Přesnější odhad objemu dat, který můžete ve své aplikaci očekávat, vám poskytne [cenová kalkulačka služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Kategorie protokolu | Počet uživatelů | Počet událostí za sekundu | Počet událostí za pětiminutový interval | Objem za interval | Počet zpráv za interval | Počet zpráv za měsíc | Náklady za měsíc (odhad) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Auditování | 100 000 | 18 | 5 400 | 10,8 MB | 43 | 371 520 | 10,83 USD |
| Auditování | 1 000 | 0.1 | 52 | 104 kB | 1 | 8 640 | 10,80 USD |
| Přihlášení | 1 000 | 178 | 53 400 | 106,8&nbsp;MB | 418 | 3 611 520 | 11,06 USD |  

### <a name="azure-monitor-logs-cost-considerations"></a>Aspekty náklady na protokoly Azure monitoru

Náklady související se správou protokoly Azure monitoru najdete v tématu [spravovat náklady pomocí řízení objemu dat a uchovávání protokolů Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-cost-storage).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

V této části najdete odpovědi na nejčastější dotazy a popis známých problémů s protokoly Azure AD ve službě Azure Monitor.

**Otázka: Protokoly, které jsou zahrnuty?**

**A**: Protokoly auditu a protokolů aktivit přihlašování jsou k dispozici pro směrování přes tuto funkci, i když události auditu související s B2C nejsou aktuálně součástí. Informace o tom, jaké typy protokolů a jaké protokoly podle funkcí se momentálně podporují, najdete v článcích o [schématu protokolů auditu](reference-azure-monitor-audit-log-schema.md) a [schématu protokolů přihlašování](reference-azure-monitor-sign-ins-log-schema.md). 

-----

**Otázka: Jak krátce po akci se odpovídající protokoly zobrazí v Centru událostí?**

**A**: Protokoly se měla zobrazit v Centru událostí během přibližně 2 až 5 minut po provedení akce. Další informace o službě Event Hubs najdete v tématu [Co je Azure Event Hubs?](../../event-hubs/event-hubs-about.md).

-----

**Otázka: Jak krátce po akci bude odpovídající protokoly objeví ve svém účtu úložiště?**

**A**: Pro účty Azure storage latence je kdekoli z 5 na 15 minut po provedení akce.

-----

**Otázka: Co se stane, pokud správce změní doba uchování nastavení diagnostiky?**

**A**: Nové zásady uchovávání informací se použijí k po provedení změny shromažďovaných protokolů. Shromážděné protokoly předtím, než se změna zásady bude tato akce vliv.

-----

**Otázka: Jaké budou náklady na uložení Moje data?**

**A**: Náklady na úložiště závisí na velikosti protokolů a dobu uchování, kterou zvolíte. Seznam odhadovaných nákladů na tenanty, které závisí na objemu generovaných protokolů, najdete v části [Velikost úložiště pro protokoly aktivit](#storage-size-for-activity-logs).

-----

**Otázka: Kolik vás to bude stát Streamovat data do centra událostí?**

**A**: Náklady na streamování závisí na počtu zpráv, který jste dostali za minutu. Tento článek popisuje způsob výpočtu nákladů a uvádí odhady nákladů na základě počtu zpráv. 

-----

**Otázka: Jak můžu integrovat Azure AD protokoly aktivit se systémem SIEM?**

**A**: Můžete to provést dvěma způsoby:

- Pomocí služeb Azure Monitor a Event Hubs můžete protokoly streamovat do systému SIEM. Nejprve nastavte [streamování protokolů do centra událostí](tutorial-azure-monitor-stream-logs-to-event-hub.md) a pak [nastavte nástroj SIEM](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub) s použitím nakonfigurovaného centra událostí. 

- Pomocí rozhraní [Graph API pro vytváření sestav](concept-reporting-api.md) můžete získat přístup k datům a pomocí vlastních skriptů je odesílat do systému SIEM.

-----

**Otázka: Jaké nástroje SIEM se aktuálně podporují?** 

**A**: V současné době podporuje monitorování Azure [Splunk](tutorial-integrate-activity-logs-with-splunk.md), QRadar, a [Sumo logiky](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory). Další informace o fungování konektorů najdete v tématu [Streamování dat monitorování Azure do centra událostí, aby je mohl používat externí nástroj](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

-----

**Otázka: Jak můžu integrovat Azure AD protokolů aktivit se Moje Splunk instance?**

**A**: Nejprve je potřeba [trasy Azure AD protokolů aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md), postupujte podle pokynů k [integrovat protokoly aktivit Splunk](tutorial-integrate-activity-logs-with-splunk.md).

-----

**Otázka: Jak můžu integrovat Azure AD protokolů aktivit se Sumo logiky?** 

**A**: Nejprve je potřeba [trasy Azure AD protokolů aktivit do centra událostí](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory), postupujte podle pokynů k [nainstalovat aplikaci Azure AD a zobrazit řídicí panely v SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

-----

**Otázka: Mám přístup k datům z centra událostí bez použití externího nástroje SIEM?** 

**A**: Ano. Pro přístup k protokolům z vlastní aplikace můžete použít [rozhraní API služby Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md). 

-----


## <a name="next-steps"></a>Další postup

* [Archivace protokolů aktivit do účtu úložiště](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Směrování protokolů aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [Integrovat protokoly aktivit Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)