---
title: Předání přihlašovacích údajů do Azure pomocí konfigurace požadovaného stavu
description: Přečtěte si, jak bezpečně předat přihlašovací údaje virtuálním počítačům Azure pomocí konfigurace požadovaného stavu PowerShellu (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: DSC
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: f191ab5819cd69ce90699db60be1665ef77e10a9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955884"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Předání přihlašovacích údajů obslužné rutině Azure DSCExtension

Tento článek se zabývá rozšířením konfigurace požadovaného stavu (DSC) pro Azure. Přehled obslužné rutiny rozšíření DSC najdete v tématu [Úvod do obslužné rutiny rozšíření konfigurace požadovaného stavu Azure](dsc-overview.md).

 

## <a name="pass-in-credentials"></a>Přihlašovací údaje pro předání

V rámci procesu konfigurace možná budete muset nastavit uživatelské účty, služby přístupu nebo nainstalovat program do uživatelského kontextu. K provedení těchto akcí je potřeba zadat přihlašovací údaje.

K nastavení parametrizovaných konfigurací můžete použít DSC. V parametrizované konfiguraci se přihlašovací údaje předávají do konfigurace a bezpečně ukládají do souborů. mof. Obslužná rutina rozšíření Azure zjednodušuje správu přihlašovacích údajů tím, že poskytuje automatickou správu certifikátů.

Následující konfigurační skript DSC vytvoří místní uživatelský účet se zadaným heslem:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Je důležité zahrnout jako součást konfigurace **uzel localhost** . Obslužná rutina rozšíření konkrétně hledá příkaz **Node localhost** . Pokud tento příkaz chybí, následující kroky nefungují. Je také důležité zahrnout přetypovat **[PsCredential]**. Tento konkrétní typ aktivuje rozšíření pro šifrování přihlašovacích údajů.

Publikování tohoto skriptu do úložiště objektů BLOB v Azure:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Pokud chcete nastavit rozšíření Azure DSC a zadat přihlašovací údaje:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Způsob zabezpečení přihlašovacích údajů

Po spuštění tohoto kódu se zobrazí výzva k zadání přihlašovacích údajů. Po zadání přihlašovacích údajů se krátce uloží do paměti. Při publikování přihlašovacích údajů pomocí rutiny **set-AzVMDscExtension** se přihlašovací údaje přenáší přes HTTPS do virtuálního počítače. V tomto virtuálním počítači Azure ukládá přihlašovací údaje šifrované na disk pomocí certifikátu místního virtuálního počítače. Přihlašovací údaje se krátce dešifrují v paměti a pak se znovu zašifrují, aby je bylo možné předat DSC.

Tento proces se liší od [použití zabezpečených konfigurací bez obslužné rutiny rozšíření](/powershell/scripting/dsc/pull-server/securemof). Prostředí Azure nabízí způsob, jak bezpečně přenášet konfigurační data prostřednictvím certifikátů. Když použijete obslužnou rutinu rozšíření DSC, nemusíte zadávat **$CertificatePath** ani **$CertificateID** /  **$Thumbprint** záznam v **ConfigurationData**.

## <a name="next-steps"></a>Další kroky

- Získejte [Úvod k obslužné rutině rozšíření Azure DSC](dsc-overview.md).
- Projděte si [šablonu Azure Resource Manager pro rozšíření DSC](dsc-template.md).
- Další informace o prostředí PowerShell DSC najdete v [centru dokumentace PowerShellu](/powershell/scripting/dsc/overview/overview).
- Další funkce, které můžete spravovat pomocí prostředí PowerShell DSC a další prostředky DSC, najdete v [galerii prostředí PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).