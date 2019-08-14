---
title: Instalace balíčku obsahu Azure AD Power BI | Microsoft Docs
description: Zjistěte, jak nainstalovat balíček obsahu Azure AD Power BI
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f088c8b49fa6485a21b630738149078b7ff45b7d
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988105"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>Rychlý start: Nainstalovat balíček obsahu Power BI Azure Active Directory

|  |
|--|
|V současné době balíček obsahu Azure AD Power BI používá rozhraní Azure AD Graph API k načtení dat z vašeho tenanta Azure AD. V důsledku toho můžete zaznamenat určitý nesoulad mezi daty, která jsou k dispozici v balíčku obsahu, a daty načtenými pomocí [rozhraní Microsoft Graph API pro generování sestav](concept-reporting-api.md). |
|  |

Power BI balíček obsahu pro Azure Active Directory (Azure AD) vám dává možnost vizualizovat data sestav z vašeho prostředí. Můžete stáhnout předem sestavený balíček obsahu a použít ho k vykazování všech aktivit v rámci vašeho adresáře s použitím bohatého prostředí vizualizací, které nabízí služba Power BI. Můžete si vytvořit vlastní řídicí panel a snadno ho sdílet s kýmkoli ve vaší organizaci. 

V tomto rychlém startu se dozvíte, jak tento balíček obsahu nainstalovat.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

* Účet Power BI. Jde o stejný účet, jako je váš účet O365 nebo Azure AD. 
* ID vašeho tenanta Azure AD. Toto je **ID adresáře** vašeho adresáře, které najdete na [stránce vlastností](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) na webu Azure Portal.
* Licence Azure AD Premium (P1/P2). Pokud chcete upgradovat edici Azure Active Directory, přečtěte si téma [Začínáme se Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) .

## <a name="install-azure-ad-power-bi-content-pack"></a>Instalace balíčku obsahu Power BI služby Azure AD 

1. Přihlaste se do služby [Power BI](https://app.powerbi.com/groups/me/getdata/services) pomocí svého účtu Power BI. Jde o stejný účet, jako je váš účet O365 nebo Azure AD.

2. Vyhledejte **Azure Active Directory Activity Logs** na stránce **Aplikace** a vyberte **Získat**. 

   ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/quickstart-install-power-bi-content-pack/getitnow.png) 
    
3. V automaticky otevřeném okně zadejte ID tenanta služby Azure AD, jako počet dnů pro dotaz zadejte **7** a pak vyberte **Další**.
    
   ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/quickstart-install-power-bi-content-pack/connect.png) 

4. Jakmile bude řídicí panel protokolů aktivity Azure Active Directory vytvořen, vyberte ho.

   ![Balíček obsahu Azure Active Directory Power BI Content Pack](./media/quickstart-install-power-bi-content-pack/dashboard.png) 
    
## <a name="next-steps"></a>Další kroky

* [Použití balíčků obsahu Power BI](howto-power-bi-content-pack.md).
* [Řešení potíží s chybami balíčku obsahu](troubleshoot-content-pack.md).
