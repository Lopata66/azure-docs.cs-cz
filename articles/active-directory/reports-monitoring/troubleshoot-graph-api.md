---
title: Řešení potíží s chybami v Azure Active Directory API pro vytváření sestav | Dokumentace Microsoftu
description: Poskytuje řešení pro chyby při volání Azure Active Directory Reporting API.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b517204fb650020bdebf8172186f30fff58f722
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438542"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Řešení potíží s chybami v Azure Active Directory API pro vytváření sestav

Tento článek uvádí běžné chybové zprávy, které můžete narazit při přístupu k sestavy aktivit pomocí rozhraní MS Graph API a kroků pro jejich řešení.

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>500 protokolu HTTP se interní chyba serveru při přístupu k Microsoft Graphu V2 koncového bodu

Aktuálně nepodporujeme koncového bodu v2 Microsoft Graphu – Ujistěte se, že přístup k protokolům aktivit pomocí koncového bodu v1 Microsoft Graphu.

### <a name="error-failed-to-get-user-roles-from-ad-graph"></a>Chyba: Nepovedlo se získat uživatelské role z AD Graphu

Může zobrazit tato chybová zpráva při pokusu o přístup k přihlášení pomocí Graph Exploreru. Ujistěte se, že jste přihlášeni ke svému účtu pomocí tlačítek přihlášení v Uživatelském rozhraní Graph Explorer, jak je znázorněno na následujícím obrázku. 

![Graph Exploreru](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-ad-graph"></a>Chyba: Nepovedlo se provést kontrola licence premium z AD Graphu 

Pokud narazíte na tato chybová zpráva při pokusu o přístup k přihlášení pomocí Graph Exploreru zvolte **upravit oprávnění** pod svůj účet na levém navigačním podokně a vyberte **Tasks.ReadWrite** a **Directory.Read.All**. 

![Upravit oprávnění uživatelského rozhraní](./media/troubleshoot-graph-api/modify-permissions.png)


### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>Chyba: Je tenant B2C ani klient nemá licenci premium

Přístup k sestavy přihlášení Azure Active Directory premium 1 (P1) vyžaduje licenci. Pokud se zobrazí tato chybová zpráva při přístupu k přihlášení, ujistěte se, že je váš tenant licenci na licenci Azure AD P1.

### <a name="error-user-is-not-in-the-allowed-roles"></a>Chyba: Uživatel není povolené role 

Pokud při pokusu o přístup k protokolům auditu nebo přihlášení pomocí rozhraní API se zobrazí tato chybová zpráva, ujistěte se, že váš účet je součástí **Čtenář zabezpečení** nebo **čtenáře sestav** role ve službě Azure Active Directory tenanta. 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Chyba: Aplikace AAD oprávnění čtení dat adresáře 

Postupujte podle kroků v [požadavky pro přístup k API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) zajistit, že aplikace běží s správnou sadu oprávnění. 

### <a name="error-application-missing-msgraph-api-read-all-audit-log-data-permission"></a>Chyba: Aplikace oprávnění 'Číst všechna data protokolů auditu' MSGraph rozhraní API

Postupujte podle kroků v [požadavky pro přístup k API pro vytváření sestav Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) zajistit, že aplikace běží s správnou sadu oprávnění. 

## <a name="next-steps"></a>Další kroky

[Použití auditování reference k rozhraní API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)
[použijte odkaz na sestavu rozhraní API aktivit přihlašování](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
