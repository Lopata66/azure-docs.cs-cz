---
title: Vyžádání bezpečného přenosu ve službě Azure Storage | Dokumentace Microsoftu
description: Další informace o funkci "Vyžadovat zabezpečený přenos" za Azure Storage a jak se dá povolit.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 06/20/2017
ms.author: fryu
ms.component: common
ms.openlocfilehash: 72e0937e91a7287d240bbdb25996865f934d432d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876498"
---
# <a name="require-secure-transfer-in-azure-storage"></a>Vyžádání bezpečného přenosu ve službě Azure Storage

Možnost "Vyžadovat zabezpečený přenos" zvyšuje zabezpečení vašeho účtu úložiště tím, že pouze požadavky na účet ze zabezpečeného připojení. Například při volání rozhraní REST API pro přístup k účtu úložiště, musíte připojení pomocí protokolu HTTPS. "Vyžadovat zabezpečený přenos" odmítne požadavky, které používají protokol HTTP.

Při použití služby soubory Azure jakékoli připojení, bez šifrování selže, pokud je povolena možnost "Vyžadovat zabezpečený přenos". To zahrnuje scénáře, které používají protokol SMB 2.1, bez šifrování protokolu SMB 3.0 a některé verze klient Linux SMB. 

Ve výchozím nastavení je zakázaná možnost "Vyžadovat zabezpečený přenos" při vytváření účtu úložiště pomocí sady SDK. A je povolená ve výchozím nastavení při vytváření účtu úložiště na webu Azure Portal.

> [!NOTE]
> Vzhledem k tomu, že úložiště Azure nepodporuje adresy typu HTTPS pro vlastní názvy domén, není tato možnost použita, když používáte vlastní název domény. A klasických účtů úložiště se nepodporují.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Povolit "Vyžadovat zabezpečený přenos" na webu Azure Portal

Můžete zapnout "zabezpečený přenos vyžaduje" nastavení při vytváření účtu úložiště [webu Azure portal](https://portal.azure.com). Můžete také povolit ho pro existující účty úložiště.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Vyžádání bezpečného přenosu pro nový účet úložiště

1. Otevřít **vytvořit účet úložiště** podokně webu Azure Portal.
1. V části **vyžadovat zabezpečený přenos**vyberte **povoleno**.

  ![Vytvořit okno účet úložiště](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Vyžádání bezpečného přenosu pro existující účet úložiště

1. Vyberte existující účet úložiště na webu Azure Portal.
1. Ve službě storage account nabídky podokně v části **nastavení**vyberte **konfigurace**.
1. V části **vyžadovat zabezpečený přenos**vyberte **povoleno**.

  ![Podokno nabídce účtu úložiště](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Povolit "Vyžadovat zabezpečený přenos" prostřednictvím kódu programu

Pokud chcete vyžadovat zabezpečený přenos prostřednictvím kódu programu, použijte nastavení _supportsHttpsTrafficOnly_ ve vlastnosti účtu úložiště pomocí rozhraní REST API, nástrojů nebo knihovny:

* [Rozhraní REST API](https://docs.microsoft.com/rest/api/storagerp/storageaccounts) (verze: 2016-12-01)
* [Prostředí PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0) (verze: 4.1.0)
* [Rozhraní příkazového řádku](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (verze: 2.0.11)
* [Prostředí NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (verze: 1.1.0)
* [Sady .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (verze: 6.3.0)
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (verze: 1.1.0)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) (verze: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>Povolit "Zabezpečený přenos vyžaduje" nastavení pomocí Powershellu

Tato ukázka vyžaduje modul Azure Powershellu verze 4.1 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Spuštěním příkazu `Connect-AzureRmAccount` vytvořte připojení k Azure.

 Pokud chcete zkontrolovat nastavení, použijte následující příkazový řádek:

```powershell
> Get-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Povolit nastavení použijte následující příkazový řádek:

```powershell
> Set-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>Povolit "Zabezpečený přenos vyžaduje" nastavení pomocí rozhraní příkazového řádku

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Pokud chcete zkontrolovat nastavení, použijte následující příkazový řádek:

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Povolit nastavení použijte následující příkazový řádek:

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Další postup
Azure Storage nabízí komplexní sadu funkcí zabezpečení, které společně umožňují vývojářům vytvářet zabezpečené aplikace. Další podrobnosti najdete v části [Příručka zabezpečení úložiště](storage-security-guide.md).
