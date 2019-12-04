---
title: Rozšířená ochrana před internetovými útoky – Azure Portal-Azure Database for MySQL
description: Přečtěte si, jak nakonfigurovat rozšířenou ochranu před internetovými útoky pro detekci neobvykléch databázových aktivit, které indikují potenciální ohrožení zabezpečení databáze.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 11ac05c710f7daf709ffbc29aebff706aea704d5
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765042"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Advanced Threat Protection pro Azure Database for MySQL

Služba Advanced Threat Protection pro Azure Database for MySQL detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

Rozšířená ochrana před internetovými útoky je součástí rozšířené nabídky zabezpečení dat, což je jednotný balíček pro pokročilé funkce zabezpečení. Rozšířená ochrana před internetovými útoky je dostupná a spravovaná prostřednictvím [Azure Portal](https://portal.azure.com) a je aktuálně ve verzi Preview.

> [!NOTE]
> Funkce rozšířené ochrany před internetovými útoky **není dostupná v** těchto oblastech cloudu Azure a v svrchovaném cloudu: US Gov – Texas, US Gov – Arizona, US gov – Iowa, US, gov) – virginia, US DoD – východ, US DoD – střed, Německo Central, Německo – sever, Čína – východ, Čína – východ 2. Pokud chcete získat obecnou dostupnost produktu, navštivte prosím [produkty dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/) .
>

> [!NOTE]
> Tato funkce je k dispozici ve všech oblastech Azure, kde Azure Database for MySQL nasazeny pro Pro obecné účely a paměťově optimalizované servery.

## <a name="set-up-threat-detection"></a>Nastavení detekce hrozeb
1. [https://portal.azure.com](https://portal.azure.com)spusťte Azure Portal.
2. Přejděte na stránku konfigurace serveru Azure Database for MySQL, který chcete chránit. V nastavení zabezpečení vyberte **Rozšířená ochrana před internetovými útoky (Preview)** .
3. Na stránce konfigurace **rozšířené ochrany před internetovými útoky (Preview)** :

   - Povolit rozšířenou ochranu před internetovými útoky na serveru.
   - V části **Upřesnit nastavení ochrany před internetovými útoky**zadejte do textového pole **Odeslat výstrahy do** seznam e-mailů, které budou dostávat výstrahy zabezpečení při detekci neobvykléch databázových aktivit.
  
   ![Nastavení detekce hrozeb](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Prozkoumejte aktivity databáze neobvyklé

Po detekci neobvykléch databázových aktivit obdržíte e-mailové oznámení. E-mail obsahuje informace o podezřelé události zabezpečení, včetně povahy aktivit neobvyklé, názvu databáze, názvu serveru, názvu aplikace a času události. Kromě toho e-mail poskytuje informace o možných příčinách a doporučených akcích k prošetření a zmírnění potenciální hrozby pro databázi.
 
1. Kliknutím na odkaz **Zobrazit nedávné výstrahy** v e-mailu spustíte Azure Portal a zobrazí se stránka Azure Security Center výstrahy, která poskytuje přehled aktivních hrozeb zjištěných v databázi SQL.
    
    ![Sestava aktivity neobvyklé](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Zobrazit aktivní hrozby:

    ![Aktivní hrozby](./media/howto-database-threat-protection-portal/active-threats.png)

2. Kliknutím na konkrétní výstrahu získáte další podrobnosti a akce pro šetření této hrozby a opravaí budoucích hrozeb.
    
    ![Konkrétní výstraha](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Prozkoumat výstrahy detekce hrozeb

SQL Database detekce hrozeb integruje své výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/). Živá dlaždice detekce hrozeb SQL sleduje stav aktivních hrozeb na stránkách databáze a ATP SQL v Azure Portal.

Kliknutím na **výstrahu detekce hrozeb** spustíte stránku Azure Security Center výstrahy a získáte přehled o aktivních hrozbách SQL zjištěných v databázi.

   ![Výstraha detekce hrozeb](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Další informace o cenách najdete na stránce s [cenami Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/) .  
