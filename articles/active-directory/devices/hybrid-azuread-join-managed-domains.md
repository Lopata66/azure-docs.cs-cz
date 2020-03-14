---
title: Konfigurace hybridního Azure Active Directory připojení ke spravovaným doménám | Microsoft Docs
description: Naučte se konfigurovat hybridní Azure Active Directory připojení ke spravovaným doménám.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd00972c2da0d3d5dafe76a8619e0f0ccaedc19
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239109"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Kurz: Konfigurace hybridního připojení k Azure Active Directory pro spravované domény

V tomto kurzu se naučíte konfigurovat službu Hybrid Azure Active Directory (Azure AD) JOIN pro zařízení připojená k doméně služby Active Directory. Tato metoda podporuje spravované prostředí, které zahrnuje místní službu Active Directory a Azure AD.

Podobně jako uživatel ve vaší organizaci je zařízení základní identitou, kterou chcete chránit. Identitu zařízení můžete použít k ochraně svých prostředků kdykoli a z libovolného místa. Tento cíl můžete dosáhnout správou identit zařízení ve službě Azure AD. Použijte jednu z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Tento článek se zaměřuje na hybridní připojení ke službě Azure AD.

Uvedení zařízení do Azure AD maximalizuje produktivitu uživatelů prostřednictvím jednotného přihlašování (SSO) napříč vaším cloudem a místními prostředky. Přístup k vašim cloudovým a místním prostředkům můžete zabezpečit současně pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md) .

Spravované prostředí můžete nasadit pomocí [synchronizace hodnot hash hesel (kosmetice)](../hybrid/whatis-phs.md) nebo [předávacího ověřování (PTA)](../hybrid/how-to-connect-pta.md) pomocí [bezproblémového jednotného přihlašování](../hybrid/how-to-connect-sso.md). Tyto scénáře nevyžadují konfiguraci federačního serveru pro ověřování.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace hybridního připojení k Azure AD
> * Povolení zařízení s Windows nižší úrovně
> * Ověření připojených zařízení
> * Řešení problémů

## <a name="prerequisites"></a>Požadavky

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 nebo novější)
- Přihlašovací údaje globálního správce pro vašeho tenanta Azure AD
- Přihlašovací údaje podnikového správce pro jednotlivé doménové struktury

Seznamte se s těmito články:

- [Co je identita zařízení?](overview.md)
- [Postupy: plánování implementace služby Hybrid Azure Active Directory JOIN](hybrid-azuread-join-plan.md)
- [Řízené ověřování pro připojení k hybridní službě Azure AD](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD nepodporuje čipové karty ani certifikáty ve spravovaných doménách.

Ověřte, že Azure AD Connect synchronizoval objekty počítačů zařízení, která chcete mít k Azure AD připojená k hybridní službě Azure AD. Pokud objekty počítače patří konkrétním organizačním jednotkám (OU), nakonfigurujte organizační jednotky pro synchronizaci v Azure AD Connect. Další informace o tom, jak synchronizovat objekty počítačů pomocí Azure AD Connect, najdete v tématu [filtrování na základě organizační jednotky](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Počínaje verzí 1.1.819.0 Azure AD Connect obsahuje průvodce pro konfiguraci hybridního připojení k Azure AD. Průvodce významně zjednodušuje proces konfigurace. Průvodce nakonfiguruje spojovací body služby (SCP) pro registraci zařízení.

Kroky konfigurace v tomto článku jsou založené na používání Průvodce v Azure AD Connect.

Služba připojení k hybridní službě Azure AD vyžaduje, aby zařízení měla přístup k následujícím prostředkům Microsoftu z vaší organizace v síti:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Pokud používáte nebo plánujete používat bezproblémové přihlašování SSO)

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím odchozího proxy serveru, doporučujeme [implementovat automatické zjišťování webových proxy serverů (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) a povolit tak počítačům s Windows 10 registraci zařízení ve službě Azure AD. Problémy s konfigurací a správou protokolu WPAD najdete v tématu [řešení potíží při automatickém zjišťování](/previous-versions/tn-archive/cc302643(v=technet.10)).

Pokud protokol WPAD nepoužíváte, můžete na svém počítači nakonfigurovat nastavení proxy serveru počínaje systémem Windows 10 1709. Další informace najdete v tématu [nastavení proxy WinHTTP nasazená objektem zásad skupiny](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Pokud nakonfigurujete nastavení proxy serveru na svém počítači pomocí nastavení WinHTTP, nepůjde se připojit k Internetu bez jakýchkoli počítačů, které se nemůžou připojit k nakonfigurovanému proxy serveru.

Pokud vaše organizace vyžaduje přístup k Internetu prostřednictvím ověřeného odchozího proxy serveru, ujistěte se, že počítače s Windows 10 se můžou úspěšně ověřit u odchozího proxy serveru. Vzhledem k tomu, že počítače s Windows 10 spouští registraci zařízení pomocí kontextu počítače, nakonfigurujte ověřování odchozího proxy pomocí kontextu počítače. Požadavky na konfiguraci vám sdělí váš poskytovatel odchozího proxy serveru.

Ověřte, že zařízení má přístup k výše uvedeným prostředkům společnosti Microsoft pod účtem System pomocí skriptu pro [připojení k registraci zařízení](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) .

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurace hybridního připojení k Azure AD

Konfigurace hybridního připojení ke službě Azure AD pomocí Azure AD Connect:

1. Spusťte Azure AD Connect a pak vyberte **Konfigurovat**.

   ![Vítejte](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. V **další úlohy**vyberte **Konfigurovat možnosti zařízení**a pak vyberte **Další**.

   ![Další úlohy](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. V **přehledu**vyberte **Další**.

   ![Přehled](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. V části **připojit k Azure AD**zadejte přihlašovací údaje globálního správce pro vašeho TENANTA Azure AD.  

   ![Připojení k Azure AD](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. V **Možnosti zařízení**vyberte **Konfigurovat hybridní připojení k Azure AD**a pak vyberte **Další**.

   ![Možnosti zařízení](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. V části **Konfigurace spojovacího bodu**služby pro každou doménovou strukturu, ve které chcete Azure AD Connect nakonfigurovat SCP, proveďte následující kroky a pak vyberte **Další**.

   1. Vyberte **doménovou strukturu**.
   1. Vyberte **ověřovací službu**.
   1. Vyberte **Přidat** a zadejte přihlašovací údaje podnikového správce.

   ![Spojovací bod služby](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. V části **operační systémy zařízení**vyberte operační systémy, které zařízení v prostředí služby Active Directory používají, a pak vyberte **Další**.

   ![Operační systém zařízení](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. V **Nastavení připraveno ke konfiguraci**vyberte **Konfigurovat**.

   ![Připraveno ke konfiguraci](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. V **nastavení dokončeno**vyberte možnost **ukončit**.

   ![Dokončení konfigurace](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>Povolení zařízení s Windows nižší úrovně

Pokud jsou některá zařízení připojená k doméně zařízení se systémem Windows nižší úrovně, musíte:

- Konfigurace nastavení místního intranetu pro registraci zařízení
- Konfigurace bezproblémového jednotného přihlašování
- Nainstalovat Microsoft Workplace Join pro počítače nižší úrovně Windows

> [!NOTE]
> Podpora Windows 7 skončila 14. ledna 2020. Další informace najdete v tématu [Podpora Windows 7 skončila](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurace nastavení místního intranetu pro registraci zařízení

Pokud chcete dokončit připojení k hybridní službě Azure AD ze zařízení se systémem Windows nižší úrovně a vyhnout se jim výzvy při ověřování zařízení ve službě Azure AD, můžete do zařízení připojených k doméně přidat tyto adresy URL do zóny Místní intranet v Internet Exploreru:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Musíte taky povolit možnost **Povolit aktualizace stavového řádku prostřednictvím skriptu** v zóně místního intranetu uživatele.

### <a name="configure-seamless-sso"></a>Konfigurace bezproblémového jednotného přihlašování

Pokud chcete dokončit hybridní připojení k Azure AD ze zařízení se systémem Windows nižší úrovně ve spravované doméně, která jako metodu ověřování Azure AD používá [synchronizaci hodnot hash hesel](../hybrid/whatis-phs.md) nebo [předávací ověřování](../hybrid/how-to-connect-pta.md) , musíte taky [nakonfigurovat bezproblémové přihlašování](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Nainstalovat Microsoft Workplace Join pro počítače nižší úrovně Windows

Aby bylo možné zaregistrovat zařízení Windows nižší úrovně, musí organizace nainstalovat [Microsoft Workplace JOIN pro počítače s jiným systémem než Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join pro počítače s jiným systémem než Windows 10 je k dispozici na webu Microsoft Download Center.

Balíček můžete nasadit pomocí systému distribuce softwaru, jako je [Microsoft Endpoint Configuration Manager](/configmgr/). Balíček podporuje standardní možnosti bezobslužné instalace s parametrem `quiet`. Aktuální verze Configuration Manager nabízí výhody oproti starším verzím, jako je schopnost sledovat dokončené registrace.

Instalační program vytvoří v systému naplánovanou úlohu, která běží v uživatelském kontextu. Úkol se aktivuje, když se uživatel přihlásí k Windows. Úloha se tiše připojí k zařízení pomocí Azure AD s použitím přihlašovacích údajů uživatele po ověření pomocí Azure AD.

## <a name="verify-the-registration"></a>Ověření registrace

Ověřte stav registrace zařízení v tenantovi Azure pomocí **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** . Tato rutina je v [modulu Azure Active Directory PowerShellu](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Při kontrole podrobností služby použijte rutinu **Get-MSolDevice** :

- Musí existovat objekt s **ID zařízení** , které odpovídá ID na klientském počítači se systémem Windows.
- Hodnota pro **DeviceTrustType** je **připojená k doméně**. Toto nastavení se rovná stavu **připojenému k hybridní službě Azure AD** na stránce **zařízení** na portálu Azure AD.
- U zařízení, která se používají v podmíněném přístupu, je **povolená** hodnota **true** a **DeviceTrustLevel** je **spravovaná**.

Postup kontroly podrobností služby:

1. Otevřete Windows PowerShell jako správce.
1. Zadejte `Connect-MsolService` pro připojení k vašemu tenantovi Azure.  
1. Zadejte `get-msoldevice -deviceId <deviceId>`.
1. Ověřte, že je hodnota **Enabled** (Povoleno) nastavená na **True** (Pravda).

## <a name="troubleshoot-your-implementation"></a>Řešení potíží s implementací

Pokud dochází k problémům s dokončováním hybridního připojení služby Azure AD pro zařízení s Windows připojená k doméně, přečtěte si téma:

- [Řešení potíží s hybridními Azure Active Directory připojenými zařízeními](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s modulem hybridní Azure Active Directory připojená zařízení nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a Naučte se spravovat identity zařízení pomocí Azure Portal.
> [!div class="nextstepaction"]
> [Správa identit zařízení](device-management-azure-portal.md)
