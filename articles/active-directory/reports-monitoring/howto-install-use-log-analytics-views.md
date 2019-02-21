---
title: Jak nainstalovat a používat zobrazení log analytics pro Azure Active Directory (preview) | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat a používat zobrazení log analytics pro Azure Active Directory (preview)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7220fc6b239d20e9f1fc81e7d18b4d7ca6f0ded5
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453626"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Nainstalovat a používat zobrazení log analytics pro Azure Active Directory

Azure Active Directory log analytics zobrazení je využíván k analýze a hledání, které protokoly aktivit Azure AD ve vašem tenantovi Azure AD. Aktivit služby Azure AD protokolů zahrnují:

* Protokoly auditu: [Sestava aktivit protokolů auditu](concept-audit-logs.md) dává vám přístup k historii každé úlohy, které se provádí ve vašem tenantovi.
* Protokoly přihlášení: S [sestavy aktivit přihlašování](concept-sign-ins.md), můžete určit, kdo provedl úlohy, které jsou hlášeny v protokolech auditování.

## <a name="prerequisites"></a>Požadavky

Použití zobrazení log analytics, budete potřebovat:

* Pracovní prostor Log Analytics ve vašem předplatném Azure. Zjistěte, jak [vytvořit pracovní prostor Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Nejprve dokončete postup [trasy Azure AD protokolů aktivit do pracovního prostoru Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
* Stáhněte si zobrazení z [úložiště GitHub](https://aka.ms/AADLogAnalyticsviews) do místního počítače.

## <a name="install-the-log-analytics-views"></a>Instalaci zobrazení log analytics

1. Přejděte do pracovního prostoru Log Analytics. Chcete-li to provést, nejprve přejděte do [webu Azure portal](https://portal.azure.com) a vyberte **všechny služby**. Typ **Log Analytics** textového pole a vyberte **pracovních prostorů Log Analytics**. Vyberte pracovní prostor, můžete směrovat protokoly aktivit, jako součást požadavků.
2. Vyberte **Návrhář zobrazení**vyberte **importovat** a pak vyberte **zvolit soubor** pro import zobrazení ze svého místního počítače.
3. Vyberte zobrazení, které jste si stáhli z požadavků a vyberte **Uložit** uložit importu. Provést tuto akci **události zřizování účtu služby Azure AD** zobrazení a **události přihlášení** zobrazení.

## <a name="use-the-views"></a>Použijte zobrazení

1. Přejděte do pracovního prostoru Log Analytics. Chcete-li to provést, nejprve přejděte do [webu Azure portal](https://portal.azure.com) a vyberte **všechny služby**. Typ **Log Analytics** textového pole a vyberte **pracovních prostorů Log Analytics**. Vyberte pracovní prostor, můžete směrovat protokoly aktivit, jako součást požadavků.

2. Jakmile budete v pracovním prostoru, vyberte **shrnutí pracovního prostoru**. Měli byste vidět následující tři zobrazení:

    * **Účet Azure AD zřizování události**: Toto zobrazení uvádí sestavy k auditování aktivity zajišťování na aktivitu, jako je počet nových uživatelů, které jsou zřízené a selhání zřizování, počet uživatelů, aktualizovat a aktualizovat chyby a počet uživatelů, zrušení zřízení a odpovídající selhání.    
    * **Události přihlášení**: Toto zobrazení uvádí relevantní zprávy související s monitorování aktivit přihlašování, jako je například přihlášení aplikace, uživatelů, zařízení, stejně jako přehled sledování počet přihlášení v čase.

3. Vyberte některou z těchto zobrazení přejít v jednotlivých sestavách. Můžete také nastavit výstrahy na některý z parametrů sestavy. Například můžeme nastavit výstrahu týkající se pokaždé, když dochází k chybě přihlášení. Chcete-li to provést, vyberte nejdřív **události přihlášení** zobrazit, vyberte možnost **chyby přihlášení v průběhu času** sestavy a pak vyberte **Analytics** otevřete stránku podrobností s samotný dotaz dané sestavy. 

    ![Podrobnosti](./media/howto-install-use-log-analytics-views/details.png)


4. Vyberte **nastavit upozornění**a pak vyberte **je prohledávání protokolů pokaždé, když se vlastní &lt;logika se nedefinovala&gt;**  pod **kritéria upozornění** oddílu. Protože chceme upozornění vždy, když dochází k chybě přihlášení, nastavte **prahová hodnota** z výchozí logika upozornění na **1** a pak vyberte **provádí**. 

    ![Konfigurace logiky signálů](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Zadejte název a popis výstrahy a nastavit závažnost **upozornění**.

    ![Vytvořit pravidlo](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Vyberte skupinu akcí s výstrahou. Obecně platí může to být buď tým, které chcete zasílat oznámení prostřednictvím e-mailu nebo textovou zprávu nebo může být automatizaci úloh pomocí webhooků, runbooků, functions, logic apps nebo externí řešení ITSM. Zjistěte, jak [vytváření a Správa skupin akcí na webu Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Vyberte **vytvořit pravidlo upozornění** k vytvoření upozornění. Teď budete upozorněni pokaždé, když dochází k chybě přihlášení.

## <a name="next-steps"></a>Další postup

* [Jak analyzovat protokoly aktivit s protokoly Azure monitoru](howto-analyze-activity-logs-log-analytics.md)
* [Začínáme s Azure Monitor protokolů na webu Azure Portal](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)
