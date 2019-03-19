---
title: Stream protokolů služby Azure Active Directory SumoLogic používat Azure Monitor (preview) | Dokumentace Microsoftu
description: Zjistěte, jak integrace protokolů Azure Active Directory s SumoLogic používat Azure Monitor (preview)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
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
ms.openlocfilehash: 98134a8fd7609a6f8857d633d2c45251e4c9197b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58081572"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor-preview"></a>Integrace protokolů Azure Active Directory s SumoLogic používat Azure Monitor (preview)

V tomto článku se dozvíte, jak integrovat protokoly služby Azure Active Directory (Azure AD) SumoLogic používat Azure Monitor. První směrování protokolů do služby Azure event hub a pak integruje Centrum událostí s SumoLogic.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:
* Protokoly centra událostí Azure, který obsahuje aktivitu Azure AD. Zjistěte, jak [streamování protokolů aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* SumoLogic jednotného přihlašování povolená předplatného.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Postup integrace protokolů Azure AD s SumoLogic 

1. Nejprve je potřeba [streamování protokolů služby Azure AD do služby Azure event hub](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Umožňuje nakonfigurovat instanci SumoLogic k [shromažďování protokolů pro Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Nainstalujte aplikaci Azure AD SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) použití předem nakonfigurované řídicích panelů, které poskytují analýzy v reálném čase vašeho prostředí.

   ![Řídicí panel](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Další postup

* [Interpretace schématu protokolů auditu ve službě Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretace schématu protokolů přihlášení ve službě Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Nejčastější dotazy a známé problémy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
