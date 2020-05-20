---
title: Začínáme s rozhraním API pro vytváření sestav Azure AD | Microsoft Docs
description: Jak začít s rozhraním API pro vytváření sestav Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5dbfca54227895de53b984dcf5c7a1f8c5fbf51d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647407"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Začínáme s rozhraním API pro vytváření sestav Azure Active Directory

Azure Active Directory poskytuje celou řadu [sestav](overview-reports.md), které obsahují užitečné informace pro aplikace, jako jsou systémy Siem, audit a Business Intelligence Tools. 

Pomocí rozhraní Microsoft Graph API pro sestavy služby Azure AD můžete získat programový přístup k datům prostřednictvím sady rozhraní API založených na REST. Tato rozhraní API můžete volat z nejrůznějších programovacích jazyků a nástrojů.

Tento článek obsahuje přehled rozhraní API pro vytváření sestav, včetně způsobů přístupu k němu.

Pokud narazíte na problémy, přečtěte si téma [Jak získat podporu pro Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto).

## <a name="prerequisites"></a>Požadavky

Pokud chcete získat přístup k rozhraní API pro vytváření sestav s zásahem uživatele nebo bez něj, musíte:

1. Přiřazení rolí (čtecí modul zabezpečení, správce zabezpečení, globální správce)
2. Registrace aplikace
3. Udělení oprávnění
4. Shromáždit nastavení konfigurace

Podrobné pokyny najdete v tématu [požadavky pro přístup k rozhraní API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>Koncové body rozhraní API 

Koncový bod rozhraní API Microsoft Graph pro protokoly auditu je `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits` a koncovým bodem rozhraní Microsoft Graph API pro přihlášení je `https://graph.microsoft.com/v1.0/auditLogs/signIns` . Další informace najdete v referenční příručce rozhraní API pro [audit](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/directoryaudit) a Reference k [rozhraní API pro přihlašování](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/signIn).

Kromě toho můžete pomocí [rozhraní API detekce rizik ochrany identity](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) získat programový přístup k detekcím zabezpečení pomocí Microsoft Graph. Další informace najdete v tématu [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](../identity-protection/graph-get-started.md). 
  
## <a name="apis-with-microsoft-graph-explorer"></a>Rozhraní API s Microsoft Graph Explorer

K ověření přihlašovacích údajů a auditování dat rozhraní API můžete použít [průzkumníka Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) . Přihlaste se ke svému účtu pomocí obou přihlašovacích tlačítek v uživatelském rozhraní Průzkumníka graphu a nastavte **AuditLog. Read. All** a **Directory. Read. All** oprávnění pro vašeho tenanta, jak je znázorněno na následujícím obrázku.   

![Průzkumník grafů](./media/concept-reporting-api/graph-explorer.png)

![Upravit uživatelské rozhraní oprávnění](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Použití certifikátů pro přístup k rozhraní API pro vytváření sestav Azure AD 

Rozhraní API pro vytváření sestav Azure AD s certifikáty použijte v případě, že plánujete načíst data sestav bez zásahu uživatele.

Podrobné pokyny najdete v tématu [získání dat pomocí rozhraní API pro vytváření sestav Azure AD s certifikáty](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Další kroky

 * [Požadavky pro přístup k rozhraní API pro vytváření sestav](howto-configure-prerequisites-for-reporting-api.md) 
 * [Získání dat pomocí rozhraní API pro generování sestav Azure AD s certifikáty](tutorial-access-api-with-certificates.md)
 * [Řešení chyb v rozhraní API pro vytváření sestav Azure AD](troubleshoot-graph-api.md)


