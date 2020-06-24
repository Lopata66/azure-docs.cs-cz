---
title: Přizpůsobení vlastností protokolu RDP pomocí prostředí PowerShell – Azure
description: Postup přizpůsobení vlastností protokolu RDP pro virtuální počítače s Windows pomocí rutin prostředí PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 760436376f4b97cff8a5da6d74cbcbb092378e40
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85206067"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) pro fond hostitelů

>[!IMPORTANT]
>Tento obsah se vztahuje na verzi 2019, která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objekty virtuálních klientů Windows, které byly zavedeny v aktualizaci jarní 2020, přečtěte si [Tento článek](../customize-rdp-properties.md).

Přizpůsobení vlastností protokol RDP (Remote Desktop Protocol) (RDP) fondu hostitelů, jako je například prostředí pro více monitorů a přesměrování zvuku, umožňuje poskytovat optimální prostředí pro uživatele podle svých potřeb. Vlastnosti protokolu RDP můžete přizpůsobit ve virtuální ploše Windows pomocí parametru **-CustomRdpProperty** v rutině **set-RdsHostPool** .

Úplný seznam podporovaných vlastností a jejich výchozích hodnot najdete v tématu [podporované nastavení souboru RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .

Nejdřív [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](/powershell/windows-virtual-desktop/overview/) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali. Potom spuštěním následující rutiny se přihlaste ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Výchozí vlastnosti protokolu RDP

Publikované soubory RDP ve výchozím nastavení obsahují následující vlastnosti:

|Vlastnosti protokolu RDP | Desktops | Vzdálené aplikace RemoteApp |
|---|---| --- |
| Režim více monitorů | Povoleno | – |
| Přesměrování jednotky povolena | Jednotky, schránka, tiskárny, porty COM, zařízení USB a čipové karty| Jednotky, schránka a tiskárny |
| Režim vzdáleného zvuku | Přehrát místně | Přehrát místně |

Tato výchozí nastavení se přepíšou všemi vlastními vlastnostmi, které definujete pro fond hostitelů.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Přidat nebo upravit jednu vlastní vlastnost RDP

Pokud chcete přidat nebo upravit jednu vlastní vlastnost RDP, spusťte následující rutinu PowerShellu:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![Snímek obrazovky rutiny PowerShellu Get-RDSRemoteApp se zvýrazněným názvem a FriendlyName.](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Přidat nebo upravit více vlastních vlastností protokolu RDP

Chcete-li přidat nebo upravit více vlastních vlastností protokolu RDP, spusťte následující rutiny prostředí PowerShell zadáním vlastních vlastností protokolu RDP jako řetězce odděleného středníkem:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![Snímek obrazovky rutiny PowerShellu Get-RDSRemoteApp se zvýrazněným názvem a FriendlyName.](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Resetovat všechny vlastní vlastnosti protokolu RDP

Jednotlivé vlastní vlastnosti protokolu RDP můžete obnovit na výchozí hodnoty podle pokynů v tématu [Přidání nebo úprava jedné vlastní vlastnosti protokolu RDP](#add-or-edit-a-single-custom-rdp-property), nebo můžete obnovit všechny vlastní vlastnosti protokolu RDP pro fond hostitelů spuštěním následující rutiny prostředí PowerShell:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![Snímek obrazovky rutiny PowerShellu Get-RDSRemoteApp se zvýrazněným názvem a FriendlyName.](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste přizpůsobili vlastnosti protokolu RDP pro daný fond hostitelů, se můžete přihlásit k klientovi virtuální plochy Windows a otestovat je jako součást uživatelské relace. Tyto další dva postupy se dozvíte, jak se připojit k relaci pomocí klienta podle vašeho výběru:

- [Připojení s desktopovým klientem Windows](../connect-windows-7-and-10.md)
- [Připojení k webovému klientovi](connect-web-2019.md)
