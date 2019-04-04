---
title: Referenční informace nastavení podmíněného přístupu Azure Active Directory | Dokumentace Microsoftu
description: Získejte přehled o podporovaných nastavení v zásadách podmíněného přístupu Azure Active Directory.
services: active-directory.
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/22/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f2334497c6329ee335c529e1490a2f966f51dff
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891714"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Odkaz nastavení podmíněného přístupu Azure Active Directory

Můžete použít [podmíněného přístupu Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) řídit jak oprávněným uživatelům můžete přístup k vašim prostředkům.

Tento článek obsahuje informace o podpoře pro následující možnosti konfigurace zásad podmíněného přístupu:

- Přiřazení aplikací cloudu
- Podmínka platformy zařízení
- Stav klienta aplikace
- Požadavek schválené klientské aplikace

Pokud to není informace, které hledáte, uveďte ji v poznámce na konci tohoto článku.

## <a name="cloud-apps-assignments"></a>Přiřazení aplikací cloudu

Díky zásadám podmíněného přístupu můžete řídit, jak vaši uživatelé přístup k vaší [cloudové aplikace](conditions.md#cloud-apps). Při konfiguraci zásad podmíněného přístupu musíte vybrat aspoň jednu aplikaci v cloudu. 

![Vyberte cloudové aplikace zásady](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Cloudové aplikace Microsoftu

Zásady podmíněného přístupu můžete přiřadit následující cloudové aplikace od Microsoftu:

- Azure Analysis Services
- Azure DevOps
- Azure SQL Database a Data Warehouse – [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Microsoft Application Insights Analytics
- Microsoft Azure Information Protection – [Další informace](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Microsoft Azure Management – [Další informace](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Microsoft Azure RemoteApp
- Správa předplatného Microsoft Azure
- Microsoft Cloud App Security
- Nástroje Microsoft Commerce Portal řízení přístupu
- Služba ověřování nástroje Microsoft Commerce
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- Registrace v Microsoft Intune
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerApps
- Vyhledávání v Bingu
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Project Online
- Online Skype pro firmy
- Virtuální privátní síť (VPN)
- Visual Studio App Center
- Ochrana ATP v programu Windows Defender

### <a name="other-applications"></a>Další aplikace

Kromě cloudové aplikace Microsoftu můžete přiřadit zásadu podmíněného přístupu pro následující typy cloudových aplikací:

- Aplikace Azure AD připojení
- Předem integrované federované aplikace software jako služba (SaaS)
- Aplikace, které používají heslo jednotné přihlašování (SSO)
- Obchodní aplikace
- Aplikace, které používají Azure AD Application Proxy

## <a name="device-platform-condition"></a>Podmínka platformy zařízení

V zásadách podmíněného přístupu můžete nakonfigurovat podmínku platformu zařízení a jejich zapojení zásady tak, aby operační systém na klientovi. Podmíněný přístup Azure AD podporuje tyto platformy zařízení:

- Android
- iOS
- telefon se systémem Windows
- Windows
- macOS

![Tie zásady přístupu na klientský operační systém](./media/technical-reference/41.png)

## <a name="client-apps-condition"></a>Stav klienta aplikace

Ve své zásady podmíněného přístupu můžete nakonfigurovat [klientské aplikace](conditions.md#client-apps) podmínku a jejich zapojení zásady tak, aby klientská aplikace, který inicioval pokus o přístup. Nastavení klienta aplikace podmínku udělit nebo blokovat přístup, když je proveden pokus o přístup z následujících typů klientských aplikací:

- Prohlížeč
- Mobilní aplikace a desktopové aplikace

![Řízení přístupu pro klientské aplikace.](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Podporované prohlížeče

Ve své zásady podmíněného přístupu můžete vybrat **prohlížeče** jako klientskou aplikaci.

![Řízení přístupu pro podporované prohlížeče](./media/technical-reference/05.png)

Toto nastavení funguje u všech prohlížečů. Tím se uspokojí zásady pro zařízení, jako je zařízení kompatibilní s požadavkem, následující operační systémy a prohlížeče jsou však podporovány:

| Operační systém                     | Prohlížeče                                      |
| :--                    | :--                                           |
| Windows 10             | Internet Explorer, Microsoft Edge, Chrome     |
| Windows 8 nebo 8.1        | Internet Explorer, Chrome                     |
| Windows 7              | Internet Explorer, Chrome                     |
| iOS                    | Safari, Intune Managed Browser                |
| Android                | Chrome, Intune Managed Browser                |
| telefon se systémem Windows          | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Internet Explorer, Microsoft Edge             |
| Windows Server 2016    | Chrome                                        |
| Windows Server 2012 R2 | Internet Explorer, Chrome                     |
| Windows Server 2008 R2 | Internet Explorer, Chrome                     |
| macOS                  | Chrome, Safari                                |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Proč vidím certifikát výzvu v prohlížeči

Azure AD ve Windows 7, iOS, Android a macOS Určuje zařízení používají klientský certifikát, který je zřízený, když je zařízení zaregistrované ve službě Azure AD.  Když uživatel poprvé přihlásí pomocí prohlížeče bude uživatel vyzván k výběru certifikátu. Než začnete používat prohlížeč, musí uživatel vybrat tento certifikát.

#### <a name="chrome-support"></a>Podpora Chrome

Chrome podpoře v **Windows 10 Creators Update (verze 1703)** nebo později, nainstalujte [toto rozšíření](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Toto rozšíření automaticky nasazovat do prohlížečů Chrome, vytvořte následující klíč registru:

|    |    |
| --- | --- |
| Cesta | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Název | 1 |
| Type | REG_SZ (String) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx |

Chrome podpoře v **Windows 8.1 a 7**, vytvořte následující klíč registru:

|    |    |
| --- | --- |
| Cesta | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Název | 1 |
| Type | REG_SZ (String) |
| Data | {"vzor": "https://device.login.microsoftonline.comfiltr","": {"VYSTAVITELE": {"CN": "MS-organizace přístup"}}} |

Tyto prohlížeče podporují ověřování zařízení umožňuje identifikovat a ověřovat na zásady zařízení. Kontrola zařízení selže, pokud je spuštěn prohlížeč v privátním režimu.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Podporovaná mobilní aplikace a desktopoví klienti

Ve své zásady podmíněného přístupu můžete vybrat **mobilní aplikace a desktopoví klienti** jako klientskou aplikaci.

![Řízení přístupu pro podporované mobilní aplikace a desktopoví klienti](./media/technical-reference/06.png)

Toto nastavení nemá vliv na pokusy o přístup z následující mobilní aplikace a desktopoví klienti:

| Klientské aplikace | Cílová služba | Platforma |
| --- | --- | --- |
| Vzdálené aplikace Azure | Služba vzdálené aplikace Azure | Windows 10, Windows 8.1, Windows 7, iOS, Android a macOS |
| Aplikace Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS a Android |
| E-mailu nebo kalendáře/lidí aplikace Outlook 2016, Outlook 2013 (s moderním ověřováním)| Office 365 Exchange Online | Windows 10 |
| Zásady MFA a polohy pro aplikace. Zařízení na základě zásad nejsou podporovány.| Všechny služby app service pro Moje aplikace| Android a iOS |
| Microsoft Teams služby – tato volba určuje všechny služby, které podporují Microsoft Teams a všechny jeho klientské aplikace – Windows Desktop, iOS, Android, webové části a webový klient | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android a macOS |
| Synchronizovat klientských aplikací Office 2016, Office 2013 (s moderním ověřováním), OneDrive (viz [poznámky](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Aplikace pro Office 2016, Office univerzální aplikace, Office 2013 (s moderním ověřováním), synchronizačního klienta Onedrivu (naleznete v tématu [poznámky](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), do budoucna plánujeme přidat podpora skupin Office, do budoucna plánujeme přidat podpora aplikací služby SharePoint | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint, OneNote pouze). OneDrive pro firmy podpora do budoucna plánujeme přidat| Office 365 SharePoint Online| macOS|
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Mobilní aplikace Office | Office 365 SharePoint Online | Android, iOS |
| Aplikace Yammer pro Office | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office pro macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (s moderním ověřováním), Skype pro firmy (s moderním ověřováním) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Mobilní aplikace Outlook | Office 365 Exchange Online | Android, iOS |
| Aplikace Power BI | Služba Power BI | Windows 10, Windows 8.1, Windows 7, Android a iOS |
| Skype pro firmy | Office 365 Exchange Online| Android, IOS |
| Visual Studio Team Services aplikace | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS a Android |

## <a name="support-for-legacy-authentication"></a>Podpora pro starší verze ověřování

Výběrem **ostatní klienty**, určíte podmínku, která má vliv na aplikace, které používají základní ověřování pomocí protokolů e-mailu, jako jsou IMAP, MAPI, POP, SMTP a starší aplikace Office, které nepoužívají moderní ověřování.  

![Ostatní klienti](./media/technical-reference/11.png)

Další informace najdete v tématu [klientské aplikace](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Požadavek schválené klientské aplikace

Ve své zásady podmíněného přístupu můžete vyžadovat, že přístup pokusí vybrané cloudové aplikace musí být provedeny z schválenou klientskou aplikací. 

![Řízení přístupu pro schválené klientské aplikace.](./media/technical-reference/21.png)

Toto nastavení platí pro následující klientské aplikace:

- Microsoft Azure Information Protection
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype pro firmy
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer

**Poznámky**

- Schválené klientské aplikace podporují funkce správy mobilních aplikací Intune.
- **Vyžadovat klientem schválenou aplikaci** požadavek:
   - Podporuje jenom pro iOS a Android pro [podmínku platformy zařízení](#device-platform-condition).

## <a name="next-steps"></a>Další postup

- Přehled podmíněného přístupu najdete v tématu [co je podmíněný přístup v Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu ve vašem prostředí, najdete v článku [doporučené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png
