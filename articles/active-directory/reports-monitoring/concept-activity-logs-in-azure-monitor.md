---
title: Protokoly aktivit Azure Active Directory ve službě Azure Monitor (Preview) | Microsoft Docs
description: Přehled protokolů aktivit Azure Active Directory ve službě Azure Monitor (Preview)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: concept
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 0c59cac666dec00e02f21ba20c0608b6b8142ba4
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45740842"
---
# <a name="azure-ad-activity-logs-in-azure-monitor-preview"></a>Protokoly aktivit Azure AD ve službě Azure Monitor (Preview)

Teď můžete pomocí služby Azure Monitor směrovat protokoly aktivit Azure Active Directory (Azure AD) do vlastního účtu úložiště nebo centra událostí. Verze Public Preview protokolů Azure Active Directory ve službě Azure Monitor umožňuje:

* Archivovat protokoly auditu pro určitý účet úložiště Azure, abyste mohli uchovávat data po dlouhou dobu.
* Streamovat protokoly auditu do centra událostí Azure pro účely analýzy pomocí oblíbených nástrojů pro správu akcí a informací o zabezpečení (SIEM), jako jsou Splunk a QRadar.
* Integrovat protokoly auditu s vlastními řešeními protokolů tím, že je budete streamovat do centra událostí.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>Podporované sestavy

Pomocí této funkce můžete směrovat protokoly aktivit auditu a přihlašování do svého účtu úložiště Azure, centra událostí nebo vlastního řešení. 

* **Protokoly auditu:** [Sestava aktivit protokolů auditu](concept-audit-logs.md) poskytuje přístup k historii každé úlohy provedené ve vašem tenantovi.
* **Protokoly přihlašování:** Se [sestavou aktivit přihlašování](concept-sign-ins.md) můžete určit, kdo provedl úlohy hlášené v protokolech auditu.

> [!NOTE]
> Zatím není dostupná podpora protokolů aktivit auditu a přihlašování souvisejících s B2C.
>

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:

* Předplatné Azure. Pokud předplatné Azure nemáte, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* [Licence](https://azure.microsoft.com/pricing/details/active-directory/) Azure AD Free, Basic, Premium 1 nebo Premium 2 pro přístup k protokolům auditu Azure AD na webu Azure Portal. 
* [Licence](https://azure.microsoft.com/pricing/details/active-directory/) Azure AD Premium 1 nebo Premium 2 pro přístup k protokolům přihlášení Azure AD na webu Azure Portal. 

Podle toho, kam chcete směrovat data protokolů auditu, potřebujete některou z následujících položek:

* Účet úložiště Azure, pro který máte oprávnění *ListKeys*. Doporučujeme použít obecný účet úložiště, ne účet úložiště objektů blob. Informace o cenách úložiště najdete v [cenové kalkulačce služby Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Obor názvů služby Azure Event Hubs pro integraci s řešeními třetích stran.

> [!NOTE]
> Pokud chcete získat přístup k protokolům aktivit Azure AD, potřebujete mít v tenantovi Azure AD roli *Globální správce* nebo *Správce zabezpečení*.
>

## <a name="cost-considerations"></a>Důležité informace o nákladech

Pokud už máte licenci Azure AD, potřebujete k vytvoření účtu úložiště a centra událostí předplatné Azure. Předplatné Azure je zdarma, ale platíte za využívání prostředků Azure, včetně účtu úložiště, který používáte k archivaci, a centra událostí, které používáte ke streamování. Množství dat a tedy i související náklady se můžou výrazně lišit v závislosti na velikosti tenanta. 

### <a name="storage-size-for-activity-logs"></a>Velikost úložiště pro protokoly aktivit

Každá událost protokolu auditu zabere v úložišti dat asi 2 kB. V případě tenanta se 100 000 uživateli, ve kterém dojde asi k 1,5 milionu událostí denně, byste za den spotřebovali asi 3 GB úložiště. Vzhledem k tomu, že k zápisům dochází v dávkách asi po pěti minutách, můžete očekávat asi 9 000 operací zápisu za měsíc. 

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

Následující tabulka obsahuje odhad měsíčních nákladů na základní centrum událostí v oblasti USA – západ v závislosti na objemu dat událostí. Přesnější odhad objemu dat, který můžete ve své aplikaci očekávat, vám poskytne [cenová kalkulačka služby Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Kategorie protokolu | Počet uživatelů | Počet událostí za sekundu | Počet událostí za pětiminutový interval | Objem za interval | Počet zpráv za interval | Počet zpráv za měsíc | Náklady za měsíc (odhad) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Auditování | 100 000 | 18 | 5 400 | 10,8 MB | 43 | 371 520 | 10,83 USD |
| Auditování | 1 000 | 0.1 | 52 | 104 kB | 1 | 8 640 | 10,80 USD |
| Přihlášení | 1 000 | 178 | 53 400 | 106,8&nbsp;MB | 418 | 3 611 520 | 11,06 USD |  


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

V této části najdete odpovědi na nejčastější dotazy a popis známých problémů s protokoly Azure AD ve službě Azure Monitor.

**Otázka: Kde mám začít?** 

**Odpověď:** Tento článek popisuje, co potřebujete k nasazení této funkce. Jakmile splníte požadavky, přejděte ke kurzům, které vám pomůžou nakonfigurovat protokoly a směrovat je do centra událostí.

---

**Otázka: Jaké protokoly jsou zahrnuté?**

**Odpověď:** Pomocí této funkce se dají směrovat jak protokoly aktivit přihlašování, tak protokoly auditu, i když události auditu související s B2C se momentálně nepodporují. Informace o tom, jaké typy protokolů a jaké protokoly podle funkcí se momentálně podporují, najdete v článcích o [schématu protokolů auditu](reference-azure-monitor-audit-log-schema.md) a [schématu protokolů přihlašování](reference-azure-monitor-sign-ins-log-schema.md). 

---

**Otázka: Jak brzy po akci se v centrech událostí zobrazí odpovídající protokoly?**

**Odpověď:** Protokoly by se v centru událostí měly zobrazit během dvou až pěti minut od provedení akce. Další informace o službě Event Hubs najdete v tématu [Co je Azure Event Hubs?](../../event-hubs/event-hubs-about.md).

---

**Otázka: Jak brzy po akci se v účtech úložiště zobrazí odpovídající protokoly?**

**Odpověď:** V případě účtů úložiště Azure se latence pohybuje mezi 5 až 15 minutami od provedení akce.

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

**Odpověď:** V současné době podporují službu Azure Monitor nástroje [Splunk](tutorial-integrate-activity-logs-with-splunk.md), QRadar a [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory). Další informace o fungování konektorů najdete v tématu [Streamování dat monitorování Azure do centra událostí, aby je mohl používat externí nástroj](../../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

---

**Otázka: Jak je možné integrovat protokoly aktivit Azure AD s instancí nástroje Splunk?**

**Odpověď:** Nejprve nastavte [směrování protokolů aktivit Azure AD do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md) a pak postupujte podle pokynů k [integraci protokolů aktivit s nástrojem Splunk](tutorial-integrate-activity-logs-with-splunk.md).

---

**Otázka: Jak je možné integrovat protokoly aktivit Azure AD s nástrojem Sumo Logic?** 

**Odpověď:** Nejprve nastavte [směrování protokolů aktivit Azure AD do centra událostí](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory) a pak postupujte podle pokynů k [instalaci aplikace Azure AD a zobrazení řídicích panelů v nástroji Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

---

**Otázka: Můžu získat přístup k datům z centra událostí bez použití externího nástroje SIEM?** 

**Odpověď:** Ano. Pro přístup k protokolům z vlastní aplikace můžete použít [rozhraní API služby Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md). 

---


## <a name="next-steps"></a>Další postup

* [Archivace protokolů aktivit do účtu úložiště](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Směrování protokolů aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [Další informace o diagnostických protokolech Azure](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
