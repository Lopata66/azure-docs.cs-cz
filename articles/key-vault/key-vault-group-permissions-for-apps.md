---
title: Udělení oprávnění k velkému počtu aplikací pro přístup k Azure key vaultu - Azure Key Vault | Dokumentace Microsoftu
description: Zjistěte, jak udělit oprávnění k velkému počtu aplikací pro přístup k trezoru klíčů
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 39624595b4dd4e72ab1d34082a6ba44a1b3636d0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721991"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Udělit několik aplikací přístup k trezoru klíčů

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zásada řízení přístupu je možné udělit několik aplikací přístup k trezoru klíčů. Zásady řízení přístupu může podporovat až 1024 aplikace a je konfigurován takto:

1. Vytvoření skupiny zabezpečení služby Azure Active Directory. 
2. Přidejte všechny aplikace přiřazené instanční objekty do skupiny zabezpečení.
3. Udělte přístup skupiny zabezpečení ke službě Key Vault.

## <a name="prerequisites"></a>Požadavky

Tady jsou požadavky:
* [Nainstalujte prostředí Azure PowerShell](/powershell/azure/overview).
* [Instalace modulu Azure Active Directory V2 PowerShell](https://www.powershellgallery.com/packages/AzureAD).
* Oprávnění k vytvoření nebo úpravě skupiny v tenantovi Azure Active Directory. Pokud nemáte příslušná oprávnění, budete muset kontaktovat svého správce Azure Active Directory. Zobrazit [o službě Azure Key Vault klíče, tajné kódy a certifikáty](about-keys-secrets-and-certificates.md) podrobnosti o službě Key Vault přístup k oprávnění zásad.

## <a name="granting-key-vault-access-to-applications"></a>Udělení přístupu službě Key Vault do aplikací

Spusťte následující příkazy v prostředí PowerShell:

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Pokud je potřeba udělit jinou sadu oprávnění pro skupinu aplikací, vytvořte samostatnou skupinu zabezpečení Azure Active Directory pro takové aplikace.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak [zabezpečení trezoru klíčů](key-vault-secure-your-key-vault.md).
