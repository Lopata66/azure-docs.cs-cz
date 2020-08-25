---
title: Azure Key Vault přesunu trezoru do jiného předplatného | Microsoft Docs
description: Pokyny k přesunutí trezoru klíčů do jiného předplatného.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: b37b327a535b716bbce845cd5883e58ec5379c48
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782715"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Přesunutí Azure Key Vault do jiného předplatného

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled

> [!IMPORTANT]
> **Přesunutí trezoru klíčů do jiného předplatného způsobí zásadní změnu prostředí.**
> Ujistěte se, že rozumíte dopadu této změny, a před tím, než se rozhodnete přesunout Trezor klíčů k novému předplatnému, pečlivě postupujte podle pokynů v tomto článku.
> Pokud používáte identity spravované služby (MSI), přečtěte si pokyny po přesunutí na konci dokumentu. 

Když vytvoříte Trezor klíčů, je automaticky svázán s výchozím ID klienta Azure Active Directory pro předplatné, ve kterém je vytvořený. Zároveň jsou k tomuto ID tenanta vázány i všechny položky zásad přístupu. Pokud přesunete předplatné Azure z tenanta A na tenanta B, stávající trezory klíčů budou pro objekty služby (uživatelé a aplikace) v tenantovi B nepřístupné. Chcete-li tento problém vyřešit, je třeba provést následující kroky:

* Změňte ID tenanta přidružené ke všem stávajícím trezorům klíčů v předplatném na tenanta B.
* Odeberte všechny stávající položky zásad přístupu.
* Přidejte nové položky zásad přístupu přidružené k tenantovi B.

## <a name="limitations"></a>Omezení

Některé objekty služby (uživatelé a aplikace) jsou vázány na konkrétního tenanta. Pokud přesunete Trezor klíčů do předplatného jiného tenanta, může se stát, že nebudete moct obnovit přístup k určitému instančnímu objektu. Ujistěte se, že v tenantovi existují všechny základní instanční objekty, u kterých přesouváte Trezor klíčů.

## <a name="design-considerations"></a>Co vzít v úvahu při návrhu

Vaše organizace mohla implementovat Azure Policy s vynucením nebo vyloučením na úrovni předplatného. V předplatném může být jiná sada přiřazení zásad, kde váš Trezor klíčů momentálně existuje, a předplatné, na které přesouváte Trezor klíčů. Konflikt v požadavcích zásad má potenciál pro přerušení vašich aplikací.

### <a name="example"></a>Příklad

Máte aplikaci připojenou k trezoru klíčů, která vytváří certifikáty platné po dobu dvou let. Předplatné, ve kterém se pokoušíte přesunout Trezor klíčů, má přiřazení zásady, které blokuje vytváření certifikátů, které jsou platné po dobu delší než jeden rok. Po přesunutí trezoru klíčů na nové předplatné bude operace vytvoření certifikátu, který je platný po dobu dvou let, blokována přiřazením zásad Azure.

### <a name="solution"></a>Řešení

Ujistěte se, že přejdete na stránku Azure Policy na Azure Portal a Prohlédněte si přiřazení zásad pro vaše aktuální předplatné a předplatné, na které se přesouváte, a zajistěte, aby nedocházelo k žádnému neshodě.

## <a name="prerequisites"></a>Požadavky

* Přístup na úrovni přispěvatele nebo vyšší pro aktuální předplatné, ve kterém existuje Trezor klíčů.
* Přístup na úrovni přispěvatele nebo vyšší pro předplatné, ve kterém chcete Trezor klíčů přesunout.
* Skupina prostředků v novém předplatném.

## <a name="procedure"></a>Postup

Pokud jste 

### <a name="moving-key-vault-to-a-new-subscription-within-the-same-tenant"></a>Přesun Key Vault k novému předplatnému v rámci stejného tenanta

1. Přihlášení k webu Azure Portal
2. Přejděte do trezoru klíčů.
3. Klikněte na kartu Přehled.
4. Vyberte tlačítko přesunout.
5. Z možností rozevíracího seznamu vyberte přesunout do jiného předplatného.
6. Vyberte skupinu prostředků, do které chcete přesunout Trezor klíčů.
7. Potvrzení upozornění týkající se přesunu prostředků
8. Vyberte OK.

### <a name="additional-steps-if-you-moved-key-vault-to-a-subscription-in-a-new-tenant"></a>Další kroky, pokud jste přesunuli Trezor klíčů do předplatného v novém tenantovi

Pokud jste Trezor klíčů přesunuli do předplatného v novém tenantovi, budete muset ručně aktualizovat ID tenanta a odebrat staré zásady přístupu. Tady jsou kurzy pro tyto kroky v PowerShellu a Azure CLI. Pokud používáte PowerShell, možná budete muset spustit příkaz Clear-AzContext popsaný níže, který vám umožní zobrazit prostředky mimo aktuálně vybraný obor. 

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Teď, když je váš trezor přidružený ke správnému ID tenanta a staré položky zásad přístupu se odeberou, nastavte nové položky zásad přístupu pomocí rutiny Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) nebo pomocí příkazu Azure CLI [AZ klíčů trezor set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

Pokud používáte spravovanou identitu pro prostředky Azure, budete ji muset aktualizovat taky na nového tenanta Azure AD. Další informace o spravovaných identitách najdete v tématu [zajištění Key Vaultho ověřování pomocí spravované identity](managed-identity.md).

Pokud používáte MSI, budete muset aktualizovat taky identitu MSI, protože stará identita už nebude ve správném tenantovi AAD. Tento problém můžete vyřešit v následujících dokumentech. 

* [Aktualizace MSI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)
* [Přenést předplatné do nového adresáře](https://docs.microsoft.com/azure/role-based-access-control/transfer-subscription)


