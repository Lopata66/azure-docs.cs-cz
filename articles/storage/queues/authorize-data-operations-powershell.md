---
title: Spuštění příkazů PowerShellu s přihlašovacími údaji Azure AD pro přístup k datům ve frontě
titleSuffix: Azure Storage
description: PowerShell podporuje přihlášení pomocí přihlašovacích údajů Azure AD ke spouštění příkazů v datech Queue Storage Azure. K dispozici je přístupový token pro relaci a slouží k autorizaci operací volání. Oprávnění závisí na roli Azure přiřazené k objektu zabezpečení Azure AD.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 09/14/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: bf2696d329f852741c42219219600dc773090623
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590711"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-queue-data"></a>Spuštění příkazů PowerShellu s přihlašovacími údaji Azure AD pro přístup k datům ve frontě

Azure Storage poskytuje rozšíření pro PowerShell, která umožňují přihlášení a spouštění příkazů skriptování s přihlašovacími údaji Azure Active Directory (Azure AD). Když se přihlásíte k PowerShellu pomocí přihlašovacích údajů Azure AD, vrátí se přístupový token OAuth 2,0. Tento token automaticky používá PowerShell k autorizaci následných operací s daty proti Queue Storage. U podporovaných operací už nemusíte předávat klíč účtu nebo token SAS pomocí příkazu.

Pomocí řízení přístupu na základě role Azure (Azure RBAC) můžete přiřadit data do fronty k objektu zabezpečení služby Azure AD. Další informace o rolích Azure v Azure Storage najdete v tématu [Správa přístupových práv k datům Azure Storage pomocí Azure RBAC](../common/storage-auth-aad-rbac-portal.md).

## <a name="supported-operations"></a>Podporované operace

Rozšíření Azure Storage jsou podporovaná pro operace s daty ve frontě. Operace, které můžete volat, závisí na oprávněních udělených objektu zabezpečení služby Azure AD, se kterým se přihlašujete k PowerShellu. Oprávnění k frontám jsou přiřazena prostřednictvím Azure RBAC. Pokud jste například přiřadili roli **čtečka dat fronty** , můžete spustit skriptovací příkazy, které čtou data z fronty. Pokud jste přiřadili roli **Přispěvatel dat fronty** , můžete spustit skriptovací příkazy, které čtou, zapisují nebo odstraňují frontu nebo data, která obsahují.

Podrobnosti o oprávněních potřebných pro jednotlivé operace Azure Storage ve frontě najdete v tématu [volání operací úložiště s tokeny OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Volání příkazů PowerShellu pomocí přihlašovacích údajů Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete použít Azure PowerShell k přihlášení a spuštění dalších operací s Azure Storage pomocí přihlašovacích údajů Azure AD, vytvořte kontext úložiště pro odkazování na účet úložiště a zahrňte `-UseConnectedAccount` parametr.

Následující příklad ukazuje, jak vytvořit frontu v novém účtu úložiště z Azure PowerShell pomocí vašich přihlašovacích údajů Azure AD. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

1. Přihlaste se ke svému účtu Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) :

    ```powershell
    Connect-AzAccount
    ```

    Další informace o přihlášení k Azure pomocí PowerShellu najdete v tématu věnovaném [přihlášení pomocí Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Vytvořte skupinu prostředků Azure voláním [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Vytvořte účet úložiště voláním [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Získejte kontext účtu úložiště, který určuje nový účet úložiště voláním [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Když pracujete na účtu úložiště, můžete místo opakovaného předání přihlašovacích údajů odkazovat na kontext. Zahrňte `-UseConnectedAccount` parametr, který bude volat všechny následné datové operace pomocí přihlašovacích údajů Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Než vytvoříte frontu, přiřaďte roli [Přispěvatel dat fronty úložiště](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) sami sobě. I když jste vlastníkem účtu, potřebujete explicitní oprávnění k provádění operací s daty v účtu úložiště. Další informace o přiřazování rolí Azure najdete v tématu [použití Azure Portal k přiřazení role Azure pro přístup k datům BLOB a front](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Rozšiřování přiřazení rolí Azure může trvat několik minut.

1. Vytvořte frontu voláním [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue). Vzhledem k tomu, že toto volání používá kontext vytvořený v předchozích krocích, vytvoří se fronta pomocí vašich přihlašovacích údajů Azure AD.

    ```powershell
    $queueName = "sample-queue"
    New-AzStorageQueue -Name $queueName -Context $ctx
    ```

## <a name="next-steps"></a>Další kroky

- [Přiřazení role Azure pro přístup k datům BLOB a Queue pomocí PowerShellu](../common/storage-auth-aad-rbac-powershell.md)
- [Autorizace přístupu k datům BLOB a Queue pomocí spravovaných identit pro prostředky Azure](../common/storage-auth-aad-msi.md)
