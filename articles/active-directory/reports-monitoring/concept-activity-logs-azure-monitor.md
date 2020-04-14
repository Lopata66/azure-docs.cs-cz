---
title: Protokoly aktivit služby Azure Active Directory ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Úvod do protokolů aktivit služby Azure Active Directory v Azure Monitoru
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
ms.date: 04/09/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0822bdd886a9a29f2cdb6843d3dc4404d7360f32
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261019"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Protokoly aktivit Azure AD ve službě Azure Monitor

Protokoly aktivit služby Azure Active Directory (Azure AD) můžete směrovat do několika koncových bodů pro dlouhodobé uchovávání informací a přehledy dat. Tato funkce umožňuje:

* Archivujte protokoly aktivit Azure AD do účtu úložiště Azure, chcete-li data uchovávat po dlouhou dobu.
* Streamujte protokoly aktivit Azure AD do centra událostí Azure pro analýzy pomocí oblíbených nástrojů pro správu informací o zabezpečení a událostí (SIEM), jako je Splunk a QRadar.
* Integrujte protokoly aktivit Azure AD s vlastními řešeními protokolů tak, že je převezete do centra událostí.
* Odesílejte protokoly aktivit Azure AD do protokolů Azure Monitoru, abyste povolili bohaté vizualizace, monitorování a upozorňování na připojená data.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Podporované sestavy

Pomocí této funkce můžete směrovat protokoly auditu azure a přihlášení a protokoly přihlášení do svého účtu úložiště Azure, centra událostí, protokolů Azure Monitoru nebo vlastního řešení. 

* **Protokoly auditu:**[Sestava aktivit protokolů auditu](concept-audit-logs.md) poskytuje přístup k historii každé úlohy provedené ve vašem tenantovi.
* **Protokoly přihlašování:** Se [sestavou aktivit přihlašování](concept-sign-ins.md) můžete určit, kdo provedl úlohy hlášené v protokolech auditu.

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
* Pracovní prostor Azure Log Analytics pro odesílání protokolů do protokolů Azure Monitor.

## <a name="cost-considerations"></a>Důležité informace o nákladech

Pokud už máte licenci Azure AD, potřebujete k vytvoření účtu úložiště a centra událostí předplatné Azure. Předplatné Azure je zdarma, ale platíte za využívání prostředků Azure, včetně účtu úložiště, který používáte k archivaci, a centra událostí, které používáte ke streamování. Množství dat a tedy i související náklady se můžou výrazně lišit v závislosti na velikosti tenanta. 

### <a name="storage-size-for-activity-logs"></a>Velikost úložiště pro protokoly aktivit

Každá událost protokolu auditu zabere v úložišti dat asi 2 kB. Protokoly událostí přihlášení jsou asi 4 KB úložiště dat. V případě tenanta se 100 000 uživateli, ve kterém dojde asi k 1,5 milionu událostí denně, byste za den spotřebovali asi 3 GB úložiště. Vzhledem k tomu, že k zápisům dochází v dávkách asi po pěti minutách, můžete očekávat asi 9 000 operací zápisu za měsíc. 


Následující tabulka obsahuje odhad nákladů na účet úložiště pro obecné účely v2 v oblasti USA – západ s dobou uchování minimálně jeden rok, v závislosti na velikosti tenanta. Přesnější odhad objemu dat, který můžete ve své aplikaci očekávat, vám poskytne [cenová kalkulačka služby Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).


| Kategorie protokolu | Počet uživatelů | Počet událostí za den | Objem dat za měsíc (odhad) | Náklady za měsíc (odhad) | Náklady za rok (odhad) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Auditování | 100 000 | 1,5&nbsp;milionu | 90 GB | 1,93 USD | 23,12 USD |
| Auditování | 1 000 | 15 000 | 900 MB | 0,02 USD | 0,24 USD |
| Přihlášení | 1 000 | 34 800 | 4 GB | 0,13 USD | 1,56 USD |
| Přihlášení | 100 000 | 15&nbsp;milionů | 1,7 TB | 35,41 USD | 424,92 USD |
 









### <a name="event-hub-messages-for-activity-logs"></a>Zprávy centra událostí pro protokoly aktivit

Události se seskupují do dávek asi po pětiminutových intervalech a odesílají se jako jedna zpráva obsahující všechny události za dané období. Zpráva v centru událostí má maximální velikost 256 kB, a pokud velikost všech zpráv za dané období překročí tento objem, odešle se několik zpráv. 

Například u velkého tenanta s více než 100 000 uživateli běžně dochází k přibližně 18 událostem za sekundu, což odpovídá 5 400 událostem za každých pět minut. Vzhledem k tomu, že protokoly auditu pro každou událost mají velikost přibližně 2 kB, odpovídá toto množství 10,8 MB dat. Proto se v takovém pětiminutovém intervalu do centra událostí odešle 43 zpráv. 

Následující tabulka obsahuje odhadované náklady za měsíc pro základní centrum událostí v západní USA, v závislosti na objemu dat událostí, které se mohou lišit od tenanta k tenantovi podle mnoha faktorů, jako je chování při přihlašování uživatele atd. Chcete-li vypočítat přesný odhad objemu dat, který očekáváte pro vaši aplikaci, použijte [cenovou kalkulačku Centra událostí](https://azure.microsoft.com/pricing/details/event-hubs/).

| Kategorie protokolu | Počet uživatelů | Počet událostí za sekundu | Počet událostí za pětiminutový interval | Objem za interval | Počet zpráv za interval | Počet zpráv za měsíc | Náklady za měsíc (odhad) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Auditování | 100 000 | 18 | 5 400 | 10,8 MB | 43 | 371 520 | 10,83 USD |
| Auditování | 1 000 | 0.1 | 52 | 104 kB | 1 | 8 640 | 10,80 USD |
| Přihlášení | 100 000 | 18000 | 5,400,000 | 10,8 GB | 42188 | 364,504,320 | 23,9 dolarů |  
| Přihlášení | 1 000 | 178 | 53 400 | 106,8&nbsp;MB | 418 | 3 611 520 | 11,06 USD |  

### <a name="azure-monitor-logs-cost-considerations"></a>Azure Monitor zaznamenává aspekty nákladů



| Kategorie protokolu       | Počet uživatelů | Počet událostí za den | Události za měsíc (30 dní) | Cena za měsíc v USD (est.) |
| :--                | ---             | ---            | ---                        | --:                          |
| Audit a přihlášení | 100 000         | 16,500,000     | 495,000,000                |  1093,00 dolarů                       |
| Auditování              | 100 000         | 1,500,000      | 45,000,000                 |  246,66 dolarů                     |
| Přihlášení           | 100 000         | 15,000,000     | 450,000,000                |  847,28 dolarů                     |










Informace o nákladech souvisejících se správou protokolů Azure Monitoru najdete [v tématu Správa nákladů řízením objemu dat a uchováváním informací v protokolech Azure Monitoru](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-cost-storage).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

V této části najdete odpovědi na nejčastější dotazy a popis známých problémů s protokoly Azure AD ve službě Azure Monitor.

**Otázka: Jaké protokoly jsou zahrnuté?**

**Odpověď:** Pomocí této funkce se dají směrovat jak protokoly aktivit přihlašování, tak protokoly auditu, i když události auditu související s B2C se momentálně nepodporují. Informace o tom, jaké typy protokolů a jaké protokoly podle funkcí se momentálně podporují, najdete v článcích o [schématu protokolů auditu](reference-azure-monitor-audit-log-schema.md) a [schématu protokolů přihlašování](reference-azure-monitor-sign-ins-log-schema.md). 

---

**Otázka: Za jak dlouho se v centru událostí zobrazí odpovídající protokoly?**

**Odpověď:** Protokoly by se v centru událostí měly zobrazit během dvou až pěti minut od provedení akce. Další informace o službě Event Hubs najdete v tématu [Co je Azure Event Hubs?](../../event-hubs/event-hubs-about.md).

---

**Otázka: Za jak dlouho po akci se odpovídající protokoly zobrazí v mém účtu úložiště?**

**Odpověď:** V případě účtů úložiště Azure se latence pohybuje mezi 5 až 15 minutami od provedení akce.

---

**Otázka: Co se stane, když správce změní dobu uchování nastavení diagnostiky?**

**A**: Nové zásady uchovávání informací budou použity pro protokoly shromážděné po změně. Protokoly shromážděné před změnou zásad nebudou ovlivněny.

---

**Otázka: Kolik bude uložení mých dat stát?**

**Odpověď:** Náklady na úložiště závisí na velikosti protokolů a také na vybrané době uchování. Seznam odhadovaných nákladů na tenanty, které závisí na objemu generovaných protokolů, najdete v části [Velikost úložiště pro protokoly aktivit](#storage-size-for-activity-logs).

---

**Otázka: Kolik stojí streamování dat do centra událostí?**

**Odpověď:** Náklady na streamování závisí na počtu přijatých zpráv za minutu. Tento článek popisuje způsob výpočtu nákladů a uvádí odhady nákladů na základě počtu zpráv. 

---

**Otázka: Jak je možné integrovat protokoly aktivit Azure AD se systémem SIEM?**

**Odpověď:** Můžete to provést dvěma způsoby:

- Pomocí služeb Azure Monitor a Event Hubs můžete protokoly streamovat do systému SIEM. Nejprve nastavte [streamování protokolů do centra událostí](tutorial-azure-monitor-stream-logs-to-event-hub.md) a pak [nastavte nástroj SIEM](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub) s použitím nakonfigurovaného centra událostí. 

- Pomocí rozhraní [Graph API pro vytváření sestav](concept-reporting-api.md) můžete získat přístup k datům a pomocí vlastních skriptů je odesílat do systému SIEM.

---

**Otázka: Jaké nástroje SIEM se aktuálně podporují?** 

**A**: **A**: V současné době Azure Monitor je podporován [Splunk](tutorial-integrate-activity-logs-with-splunk.md), IBM QRadar, [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory), [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight), LogRhythm a Logz.io. Další informace o fungování konektorů najdete v tématu [Streamování dat monitorování Azure do centra událostí, aby je mohl používat externí nástroj](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

---

**Otázka: Jak je možné integrovat protokoly aktivit Azure AD s instancí nástroje Splunk?**

**Odpověď:** Nejprve nastavte [směrování protokolů aktivit Azure AD do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md) a pak postupujte podle pokynů k [integraci protokolů aktivit s nástrojem Splunk](tutorial-integrate-activity-logs-with-splunk.md).

---

**Otázka: Jak je možné integrovat protokoly aktivit Azure AD s nástrojem Sumo Logic?** 

**Odpověď:** Nejprve nastavte [směrování protokolů aktivit Azure AD do centra událostí](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory) a pak postupujte podle pokynů k [instalaci aplikace Azure AD a zobrazení řídicích panelů v nástroji Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

---

**Otázka: Můžu získat přístup k datům z centra událostí bez použití externího nástroje SIEM?** 

**A:** Ano. Pro přístup k protokolům z vlastní aplikace můžete použít [rozhraní API služby Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md). 

---


## <a name="next-steps"></a>Další kroky

* [Archivace protokolů aktivit do účtu úložiště](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Směrování protokolů aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [Integrace protokolů aktivit pomocí Azure Monitoru](howto-integrate-activity-logs-with-log-analytics.md)
