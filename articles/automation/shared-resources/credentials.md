---
title: Assety přihlašovacích údajů ve službě Azure Automation
description: Assety přihlašovacích údajů ve službě Azure Automation obsahovat zabezpečovací pověření, která slouží k ověřování k prostředkům přistupuje z runbooku nebo konfigurace DSC. Tento článek popisuje, jak vytvořit assety přihlašovacích údajů a jejich použití v runbooku nebo konfigurace DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 04/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 44bc49d10c492822c1b5d30ad5794ac2522cb918
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478156"
---
# <a name="credential-assets-in-azure-automation"></a>Assety přihlašovacích údajů ve službě Azure Automation

Prostředek přihlašovacích údajů Automation obsahuje objekt, který obsahuje zabezpečovacích přihlašovacích údajů, jako je například uživatelské jméno a heslo. Runbooků a DSC konfigurace může použít rutiny přijmout objekt PSCredential pro ověřování, nebo se může extrahovat uživatelské jméno a heslo objekt PSCredential poskytovat nějaká aplikace nebo služby, které vyžadují ověřování. Vlastnosti přihlašovacích údajů jsou bezpečně uložené ve službě Azure Automation a je přístupná v runbooku nebo konfigurace DSC se [Get-AutomationPSCredential](#activities) aktivity.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Zabezpečené prostředky ve službě Azure Automation zahrnovat přihlašovací údaje, certifikátů, připojení a zašifrované proměnné. Tyto prostředky jsou zašifrované a uložené ve službě Azure Automation jednotlivých účtů automation pomocí jedinečný klíč, který je generován. Tento klíč uložený ve službě Key Vault. Před uložením o zabezpečený prostředek, je klíč načíst ze služby Key Vault a použije k zašifrování assetu.

## <a name="azure-classic-powershell-cmdlets"></a>Rutiny Azure Classic PowerShell

Rutiny v následující tabulce se používají k vytváření a správě prostředků přihlašovacích údajů automation pomocí prostředí Windows PowerShell.  Se dodávají jako součást [modulu Azure PowerShell](/powershell/azure/overview), která je k dispozici pro použití v runbooků služeb automatizace a konfigurace DSC.

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential) |Načte informace o asset přihlašovacích údajů. Přihlašovací údaje samotného dá načíst jenom z **Get-AutomationPSCredential** aktivity. |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Vytvoří nový přihlašovací údaj automatizace. |
| [Remove-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Odebere přihlašovací údaje služby Automation. |
| [Set-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Nastaví vlastnosti pro existující pověření služby Automation. |

## <a name="azurerm-powershell-cmdlets"></a>Rutiny AzureRM Powershellu

Pro AzureRM rutiny v následující tabulce se používají k vytváření a správě prostředků přihlašovacích údajů automation pomocí prostředí Windows PowerShell.  Se dodávají jako součást [modulu Azure RM.Automation](/powershell/azure/overview), která je k dispozici pro použití v runbooků služeb automatizace a konfigurace DSC.

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential) |Načte informace o asset přihlašovacích údajů. To nevrací objekt PSCredential.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential) |Vytvoří nový přihlašovací údaj automatizace. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential) |Odebere přihlašovací údaje služby Automation. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential) |Nastaví vlastnosti pro existující pověření služby Automation. |

## <a name="activities"></a>Činnosti

Aktivity v následující tabulce se používají pro přístup k přihlašovacím údajům v runbooku a konfiguracích DSC.

| Činnosti | Popis |
|:--- |:--- |
| Get-AutomationPSCredential |Získá přihlašovací údaje pro použití v runbooku nebo konfigurace DSC. Vrátí [System.Management.Automation.PSCredential](/dotnet/api/system.management.automation.pscredential) objektu. |

> [!NOTE]
> Měli byste se vyhnout používání proměnných v názvu parametru – Get-AutomationPSCredential, protože to může zkomplikovat zjišťování závislostí mezi runbooky a konfigurace DSC a assety přihlašovacích údajů v době návrhu.

## <a name="python2-functions"></a>Funkce Python2

Funkce v následující tabulce se používá pro přístup k přihlašovacím údajům v sadě runbook Python2.

| Funkce | Popis |
|:---|:---|
| automationassets.get_automation_credential | Načte informace o asset přihlašovacích údajů. |

> [!NOTE]
> Je nutné naimportovat modul "automationassets" v horní části stránky sady Python runbook pro přístup k funkce asset.

## <a name="creating-a-new-credential-asset"></a>Vytvoření nových assetů přihlašovacích údajů

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>K vytvoření nových assetů přihlašovacích údajů pomocí webu Azure portal

1. Ve svém účtu automation vyberte **pověření** pod **sdílené prostředky**.
1. Klikněte na tlačítko **+ přidat přihlašovací údaj**.
1. Vyplňte formulář a klikněte na tlačítko **vytvořit** uložit nový přihlašovací údaj.

> [!NOTE]
> Uživatelské účty, které používají ověřování službou Multi-Factor Authentication nejsou podporovány pro použití ve službě Azure Automation.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>K vytvoření nových assetů přihlašovacích údajů pomocí Windows Powershellu

Následující vzorové příkazy znázorňují postup vytvoření nových přihlašovacích údajů služby automation. Objekt PSCredential je poprvé vytvořena s uživatelské jméno a heslo a pak použít k vytvoření asset přihlašovacích údajů. Alternativně můžete použít **Get-Credential** rutiny výzva k zadání jména a hesla.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Pomocí přihlašovacích údajů prostředí PowerShell

Načíst asset přihlašovacích údajů v runbooku nebo konfigurace DSC se **Get-AutomationPSCredential** aktivity. Tím se vrátí [objekt PSCredential](/dotnet/api/system.management.automation.pscredential) , můžete použít s aktivitu nebo rutinu, která vyžaduje parametr PSCredential. Můžete také načíst vlastnosti objekt přihlašovacích údajů použít jednotlivě. Objekt má vlastnost pro uživatelské jméno a zabezpečené heslo, nebo můžete použít **GetNetworkCredential** metodu pro návrat [NetworkCredential](/dotnet/api/system.net.networkcredential) objekt, který bude poskytovat nezabezpečené verze heslo.

> [!NOTE]
> **Get-AzureRmAutomationCredential** nevrátí **PSCredential** , který lze použít pro ověřování. Obsahuje jenom informace o přihlašovacích údajích. Pokud je třeba použít přihlašovací údaje v sadě runbook je nutné použít **Get-AutomationPSCredential** načíst **PSCredential** objektu.

### <a name="textual-runbook-sample"></a>Ukázka textové sady runbook

Následující vzorové příkazy znázorňují postup použití přihlašovacích údajů prostředí PowerShell v runbooku. V tomto příkladu přihlašovací údaje, které je načten a jeho uživatelské jméno a heslo, které jsou přiřazené do proměnné.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Můžete také použít přihlašovací údaje pro ověření do Azure s [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). Ve většině případů byste měli používat [účet Spustit jako](../manage-runas-account.md) a načíst ji [Get-AutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Ukázkový grafický runbook

Přidáte **Get-AutomationPSCredential** aktivitu grafický runbook tak, že kliknete pravým tlačítkem na přihlašovací údaje, které v podokně knihovna grafický editor a vyberete **přidat na plátno**.

![Přidat přihlašovací údaj na plátno](../media/credentials/credential-add-canvas.png)

Následující obrázek ukazuje příklad použití přihlašovacích údajů v grafický runbook.  V takovém případě se používá k ověřování pro sady runbook a prostředky Azure, jak je popsáno v [ověření Runbooků pomocí uživatelského účtu Azure AD](../automation-create-aduser-account.md).  První aktivita načte přihlašovací údaj, který má přístup k předplatnému Azure.  **Add-AzureAccount** aktivita pak používá tento přihlašovací údaj k ověřování pro všechny aktivity, které následují po.  A [propojení kanálu](../automation-graphical-authoring-intro.md#links-and-workflow) je tady od **Get-AutomationPSCredential** je očekáván jeden objekt.  

![Přidat přihlašovací údaj na plátno](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Pomocí přihlašovacích údajů prostředí PowerShell DSC

Během konfigurace DSC ve službě Azure Automation může odkazovat na používání prostředků přihlašovacích údajů **Get-AutomationPSCredential**, assety přihlašovacích údajů můžete také předat ve prostřednictvím parametrů, pokud je potřeba. Další informace najdete v tématu [kompilace konfigurací v Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Pomocí přihlašovacích údajů v Python2

Následující příklad ukazuje příklad přístup k přihlašovacím údajům runbooky Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Další postup

* Další informace o propojeních ve vytváření grafického obsahu najdete v tématu [odkazy v vytváření grafického obsahu](../automation-graphical-authoring-intro.md#links-and-workflow)
* Různé metody ověřování díky službě Automation najdete v tématu [zabezpečení Azure Automation](../automation-security-overview.md)
* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](../automation-first-runbook-graphical.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](../automation-first-runbook-textual.md).
* Začínáme s runbooky Python2 najdete v článku [Můj první runbook Python2](../automation-first-runbook-textual-python2.md) 
