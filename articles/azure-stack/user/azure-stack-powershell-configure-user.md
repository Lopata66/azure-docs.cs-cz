---
title: Připojení ke službě Azure Stack pomocí prostředí PowerShell jako uživatel | Dokumentace Microsoftu
description: Kroky pro připojení k instanci služby Azure Stack uživatele.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2018
ms.author: sethm
ms.reviewer: Balsu.G
ms.openlocfilehash: 7710a08fffed304d4d9dd9b036e4c00bcd02a9a9
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52965953"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Připojení ke službě Azure Stack pomocí prostředí PowerShell jako uživatel

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tento článek obsahuje kroky pro připojení k vaší instanci služby Azure Stack. Musíte se připojit ke správě prostředků Azure Stack pomocí Powershellu. Například můžete použít PowerShell k odběru nabídky, vytvářet virtuální počítače a nasazení šablon Azure Resource Manageru. Abyste mohli spustit rutiny prostředí PowerShell.

Získat nastavení:
  - Ujistěte se, že máte požadavky.
  - Spojte se s Azure Active Directory (Azure AD) nebo služby Active Directory Federation Services (AD FS). 
  - Zaregistrujte poskytovatele prostředků.
  - Test připojení.

## <a name="prerequisites"></a>Požadavky

Můžete nakonfigurovat tyto požadavky z [sada](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), nebo z Windows na základě externí klienta máte [připojené prostřednictvím sítě VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Nainstalujte [moduly Azure Powershellu kompatibilní s Azure Stack](azure-stack-powershell-install.md).
* Ve službě [Azure Stack development Kit by měl být blobEndpoint](azure-stack-powershell-download.md) .

Nezapomeňte že nahradit proměnné následujícího skriptu s hodnotami z konfigurace služby Azure Stack:

- **Název tenanta Azure AD**  
  Název tenanta Azure AD slouží ke správě služby Azure Stack, například yourdirectory.onmicrosoft.com.
- **Koncový bod Azure Resource Manageru**  
  Pro Azure Stack development kit, tato hodnota nastavená na https://management.local.azurestack.external. K získání této hodnoty pro integrované systémy Azure Stack, obraťte se na svého poskytovatele služeb.

## <a name="connect-with-azure-ad"></a>Spojte se s Azure AD

  ```PowerShell
  $AADTenantName = "yourdirectory.onmicrosoft.com"
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantId
   ```

## <a name="connect-with-ad-fs"></a>Spojte se s AD FS

  ```PowerShell  
  $ArmEndpoint = "https://management.local.azurestack.external"

  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
  $tenantId = (invoke-restmethod "$($AuthEndpoint)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

  # Sign in to your environment

  $cred = get-credential

  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $tenantId `
     $cred = get-credential
  ```

## <a name="register-resource-providers"></a>Zaregistrovat poskytovatele prostředků

Poskytovatelé prostředků nejsou registrovány automaticky pro nové předplatné uživatele, kteří nemají žádné prostředky nasazené prostřednictvím portálu. Můžete explicitně zaregistrovat poskytovatele prostředků spuštěním následujícího skriptu:

```PowerShell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Otestovat připojení

Když máte všechna nastavení, otestujte připojení pomocí prostředí PowerShell k vytváření prostředků v Azure stacku. Jako test vytvořte skupinu prostředků pro aplikaci a přidejte virtuální počítač. Spuštěním následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další postup

- [Vývoj šablon pro Azure Stack](azure-stack-develop-templates.md)
- [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
- Pokud chcete nastavit prostředí PowerShell pro operátor prostředí cloud, podívejte se na [konfigurace prostředí PowerShell pro operátory Azure stacku](../azure-stack-powershell-configure-admin.md) článku.