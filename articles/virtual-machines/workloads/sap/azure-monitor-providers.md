---
title: Azure Monitor pro poskytovatele řešení SAP | Microsoft Docs
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se služby Azure monitor pro poskytovatele řešení SAP.
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 056eba8694d1727350809121f763181e3cdbdc64
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968600"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Azure monitor pro poskytovatele řešení SAP (Preview)

## <a name="overview"></a>Přehled  

V kontextu Azure Monitor pro řešení SAP odkazuje *Typ poskytovatele* na konkrétního *poskytovatele*. Například *SAP HANA*, který je nakonfigurován pro konkrétní součást v rámci technologie SAP na šířku, jako je SAP HANA databáze. Poskytovatel obsahuje informace o připojení pro odpovídající komponentu a pomáhá shromažďovat data telemetrie z této součásti. Jeden Azure Monitor pro prostředek řešení SAP (označovaný také jako prostředek monitorování SAP) je možné nakonfigurovat s více zprostředkovateli stejného typu nebo několika zprostředkovateli s více typy zprostředkovatelů.
   
Zákazníci si můžou zvolit konfiguraci různých typů zprostředkovatelů, které umožní shromažďování dat z odpovídající komponenty v jejich prostředí SAP. Zákazníci můžou například nakonfigurovat jednoho poskytovatele pro typ poskytovatele SAP HANA, jiného poskytovatele pro typ poskytovatele clusteru s vysokou dostupností a tak dále.  

Zákazníci si také můžou zvolit, že mají nakonfigurovat více poskytovatelů určitého typu zprostředkovatele, aby mohli znovu použít stejný prostředek sledování SAP a přidruženou spravovanou skupinu. Zjistěte více o spravované skupině prostředků. Ve verzi Public Preview jsou podporovány následující typy zprostředkovatelů:   
- SAP HANA
- Cluster s vysokou dostupností
- Microsoft SQL Server

![Azure Monitor pro poskytovatele řešení SAP](./media/azure-monitor-sap/azure-monitor-providers.png)

Zákazníkům se doporučuje nakonfigurovat alespoň jednoho poskytovatele z dostupných typů poskytovatele v době nasazení prostředku sledování SAP. Když zákazníci nakonfigurují poskytovatele, zahájí shromažďování dat z odpovídající komponenty, pro kterou je poskytovatel nakonfigurovaný.   

Pokud zákazníci nekonfigurují žádné zprostředkovatele v době nasazení prostředku monitorování SAP, i když se prostředek monitorování SAP úspěšně nasadí, nebudou shromažďována žádná data telemetrie. Zákazníci mají možnost Přidat poskytovatele po nasazení prostřednictvím prostředku sledování SAP v rámci Azure Portal. Zákazníci mohou kdykoli přidat nebo odstranit poskytovatele z prostředku monitorování SAP.

> [!Tip]
> Pokud chcete, aby Microsoft implementoval konkrétního poskytovatele, zachovejte prosím na konci tohoto dokumentu zpětnou vazbu prostřednictvím odkazu nebo si vyžádejte tým svého účtu.  

## <a name="provider-type-sap-hana"></a>Typ poskytovatele SAP HANA

Zákazníci mohou nakonfigurovat jednoho nebo více poskytovatelů typu zprostředkovatele *SAP HANA* pro povolení shromažďování dat z databáze SAP HANA. Poskytovatel SAP HANA se připojuje k databázi SAP HANA přes port SQL, získává data telemetrie z databáze a přenáší je do pracovního prostoru Log Analytics v rámci předplatného zákazníka. Poskytovatel SAP HANA shromažďuje data každé 1 minuty z databáze SAP HANA.  

Ve verzi Public Preview můžou zákazníci zobrazit následující data s poskytovatelem SAP HANA: základní využití infrastruktury, SAP HANA stav hostitele, SAP HANA replikace systému a SAP HANA data telemetrie zálohování. Chcete-li nakonfigurovat poskytovatele SAP HANA, je třeba zadat IP adresu hostitele, číslo portu HANA SQL a uživatelské jméno a heslo SYSTEMDB. Zákazníkům se doporučuje nakonfigurovat SAP HANA poskytovatele na SYSTEMDB, ale další poskytovatele můžou být nakonfigurovaní proti jiným databázovým tenantům.

![Azure Monitor pro poskytovatele řešení SAP – SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Typ poskytovatele – cluster s vysokou dostupností
Zákazníci můžou nakonfigurovat jednoho nebo více poskytovatelů typu clusteru s *vysokou dostupností* , aby se povolilo shromažďování dat z clusteru Pacemaker v rámci technologie SAP na šířku. Zprostředkovatel clusteru s vysokou dostupností se připojuje k Pacemaker, pomocí [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) koncového bodu, získává data telemetrie z databáze a přenáší je do pracovního prostoru Log Analytics v rámci předplatného zákazníka. Poskytovatel clusteru s vysokou dostupností shromažďuje data každých 60 sekund od Pacemaker.  

Ve verzi Public Preview můžou zákazníci zobrazit následující data s poskytovatelem clusteru s vysokou dostupností:   
 - Stav clusteru vyjádřený jako souhrn stavů uzlů a prostředků 
 - [účastníky](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor pro poskytovatele řešení SAP – cluster s vysokou dostupností](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Ke konfiguraci poskytovatele clusteru s vysokou dostupností jsou zapojené dva primární kroky:

1. Nainstalujte [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) do *každého* uzlu v clusteru Pacemaker.

   Pro instalaci ha_cluster_exporter máte dvě možnosti:
   
   - Pomocí skriptů Azure Automation nasaďte cluster s vysokou dostupností. Skripty se nainstalují [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) na každý uzel clusteru.  
   - Proveďte [ruční instalaci](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build). 

2. Nakonfigurujte poskytovatele clusteru s vysokou dostupností pro *každý* uzel v rámci clusteru Pacemaker.

   Pokud chcete nakonfigurovat poskytovatele clusteru s vysokou dostupností, vyžadují se tyto informace:
   
   - **Název:** Název tohoto poskytovatele. Měl by být jedinečný pro tento Azure Monitor instance řešení SAP.
   - **Koncový bod Prometheus** Obvykle http \: // \<servername or ip address\> : 9664/metriky.
   - **Identifikátor SID**. Pro systémy SAP použijte protokol SAP SID. Pro jiné systémy (například clustery NFS) použijte pro cluster název se třemi znaky. Identifikátor SID musí být odlišný od jiných monitorovaných clusterů.   
   - **Název clusteru** Název clusteru, který se používá při vytváření clusteru. Název clusteru najdete ve vlastnosti clusteru `cluster-name` .
   - **Název hostitele**. Název hostitele Linux virtuálního počítače.  

## <a name="provider-type-microsoft-sql-server"></a>Typ poskytovatele Microsoft SQL Server

Zákazníci mohou nakonfigurovat jednoho nebo více poskytovatelů typu zprostředkovatele *Microsoft SQL Server* Povolit shromažďování dat z [SQL Server na virtuálních počítačích](https://azure.microsoft.com/services/virtual-machines/sql-server/). Poskytovatel SQL Server se připojuje k Microsoft SQL Server přes port SQL, získává data telemetrie z databáze a přenáší je do pracovního prostoru Log Analytics v rámci předplatného zákazníka. SQL Server musí být nakonfigurovaná pro ověřování SQL a přihlášení SQL Server, aby se databáze SAP DB jako výchozí databáze pro poskytovatele vytvořila. Poskytovatel SQL Server shromažďuje data mezi každých 60 sekund až do každé hodiny z SQL serveru.  

Ve verzi Public Preview můžou zákazníci zobrazit následující data s poskytovatelem SQL Server: základní využití infrastruktury, hlavní příkazy SQL, nejvyšší největší tabulka, problémy zaznamenané v protokolech chyb SQL Server, blokující procesy a další.  

Chcete-li nakonfigurovat poskytovatele Microsoft SQL Server, je nutné zadat ID systému SAP, IP adresu hostitele, SQL Server číslo portu a také SQL Server přihlašovací jméno a heslo.

![Azure Monitor pro poskytovatele řešení SAP – SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>Další kroky

- Vytvořte první Azure Monitor pro prostředek řešení SAP.
- Máte dotazy týkající se Azure Monitor pro řešení SAP? Podívejte se na část [Nejčastější dotazy](./azure-monitor-faq.md) .
