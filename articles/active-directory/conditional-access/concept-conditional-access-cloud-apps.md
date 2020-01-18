---
title: Klientské aplikace v zásadách podmíněného přístupu – Azure Active Directory
description: ''
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb291f33d2b757ca381e1d675ddc386c78a55d52
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170833"
---
# <a name="conditional-access-cloud-apps-and-actions"></a>Podmíněný přístup: cloudové aplikace a akce

Cloudové aplikace nebo akce jsou klíčovou součástí zásad podmíněného přístupu. Zásady podmíněného přístupu umožňují správcům přiřazovat ovládací prvky konkrétním aplikacím nebo akcím. 

- Správci si můžou vybrat ze seznamu aplikací, které obsahují integrované aplikace Microsoftu a všechny [integrované aplikace Azure AD](../manage-apps/what-is-application-management.md) , včetně galerie, mimo galerie a aplikace publikované prostřednictvím [proxy aplikací](../manage-apps/what-is-application-proxy.md).
- Správci se můžou rozhodnout definovat zásady, které nejsou založené na cloudové aplikaci, ale na akci uživatele. Jediná podporovaná akce je registrace informací o zabezpečení (Preview), která umožňuje podmíněný přístup vymáhat ovládací prvky v rámci [kombinovaného prostředí pro registraci informací o zabezpečení](../authentication/howto-registration-mfa-sspr-combined.md).

![Definování zásad podmíněného přístupu a určení cloudových aplikací](./media/concept-conditional-access-cloud-apps/conditional-access-define-policy-specify-cloud-apps.png)

## <a name="microsoft-cloud-applications"></a>Cloudové aplikace Microsoftu

Mnohé z existujících cloudových aplikací Microsoftu jsou uvedené v seznamu aplikací, ze kterých můžete vybírat. 

Správci můžou k těmto cloudovým aplikacím od Microsoftu přiřazovat zásady podmíněného přístupu. Některé aplikace, jako je například sada Office 365 (Preview) a Správa Microsoft Azure, zahrnují několik souvisejících podřízených aplikací nebo služeb. Následující seznam není vyčerpávající a může se změnit.

- [Office 365 (Preview)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database a datový sklad](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Analýzy Microsoft Application Insights
- [Microsoft Azure Information Protection](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Správa Microsoft Azure](#microsoft-azure-management)
- Správa předplatného Microsoft Azure
- Microsoft Cloud App Security
- Portál Microsoft Commerce Tools Access Control Portal
- Služba Microsoft Commerce Tools Authentication Service
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Registrace Microsoft Intune](https://docs.microsoft.com/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Hledání ve službě Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Sway pro Office
- Outlook Groups
- Služba Power BI
- Project Online
- Online Skype pro firmy
- Virtuální privátní síť (VPN)
- Ochrana ATP v programu Windows Defender

### <a name="office-365-preview"></a>Office 365 (Preview)

Office 365 poskytuje cloudové služby pro produktivitu a spolupráci, jako je Exchange, SharePoint a Microsoft Teams. Cloudové služby Office 365 jsou integrovány, aby bylo zajištěno hladké a spolupracující prostředí. Tato integrace může při vytváření zásad způsobit nejasnost, protože některé aplikace, jako je například Microsoft teams, mají závislosti na jiných, jako je SharePoint nebo Exchange.

Aplikace Office 365 (Preview) umožňuje zaměřit se na tyto služby najednou. Místo cílení na jednotlivé cloudové aplikace doporučujeme použít novou aplikaci Office 365 (Preview). Cílení na tuto skupinu aplikací pomáhá zabránit problémům, ke kterým může dojít kvůli nekonzistentním zásadám a závislostem.

Správci se můžou rozhodnout vyloučit konkrétní aplikace ze zásad, pokud si chtějí zahrnout aplikaci Office 365 (Preview) a vyloučit konkrétní aplikace podle jejich výběru v zásadách.

Klíčové aplikace, které jsou zahrnuté v klientské aplikaci Office 365 (Preview):

   - Microsoft Flow
   - Microsoft Forms
   - Portál systém Microsoft Office 365
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Sada Office 365 Search Service
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - OneDrive
   - PowerApps
   - Online Skype pro firmy
   - Sway

### <a name="microsoft-azure-management"></a>Správa Microsoft Azure

Aplikace pro správu Microsoft Azure zahrnuje několik základních služeb. 

   - Portál Azure
   - Poskytovatel Azure Resource Manager
   - Rozhraní API modelu nasazení Classic
   - Azure PowerShell
   - Portál pro správu předplatných sady Visual Studio
   - Azure DevOps
   - Portál Azure Data Factory

> [!NOTE]
> Aplikace Microsoft Azure Management se vztahuje na Azure PowerShell, která volá rozhraní API Azure Resource Manager. Nevztahuje se na Azure AD PowerShell, který volá Microsoft Graph.

## <a name="other-applications"></a>Další aplikace

Kromě aplikací Microsoftu můžou správci přidat do zásad podmíněného přístupu libovolnou aplikaci registrovanou v Azure AD. Tyto aplikace mohou zahrnovat: 

- Aplikace publikované prostřednictvím [Azure proxy aplikací služby AD](../manage-apps/what-is-application-proxy.md)
- [Aplikace přidané z Galerie](../manage-apps/add-application-portal.md)
- [Vlastní aplikace, které nejsou v galerii](../manage-apps/add-non-gallery-app.md)
- [Starší verze aplikací publikované prostřednictvím řadičů pro doručování aplikací a sítí](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>Akce uživatele

Akce uživatele jsou úkoly, které může provést uživatel. Jediná aktuálně podporovaná akce je **Registrace informací o zabezpečení (Preview)** , která umožňuje vyhovět zásadám podmíněného přístupu, když se uživatelům, kteří mají povolený kombinovaný pokus o registraci, pokusí zaregistrovat své bezpečnostní údaje. Další informace najdete v článku [o registraci kombinovaného zabezpečení (Preview)](../authentication/concept-registration-mfa-sspr-combined.md).

## <a name="next-steps"></a>Další kroky

- [Komponenty zásad podmíněného přístupu](concept-conditional-access-policies.md)
- [Závislosti klientských aplikací](service-dependencies.md)
- [Microsoft Intune: vyžadovat MFA pro zápis zařízení](https://docs.microsoft.com/intune/enrollment/multi-factor-authentication)
