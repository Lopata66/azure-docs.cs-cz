---
title: Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption
description: Tento článek popisuje kroky pro vytvoření a konfiguraci trezoru klíčů pro použití s Azure Disk Encryption
ms.service: virtual-machines-linux
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ecbc44a3fe376d41b245c88ab11c5937f57155a9
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87372684"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption

Azure Disk Encryption používá Azure Key Vault k řízení a správě šifrovacích klíčů a tajných klíčů disku.  Další informace o trezorech klíčů najdete v tématu [Začínáme s Azure Key Vault](../../key-vault/general/overview.md) a [zabezpečení trezoru klíčů](../../key-vault/general/secure-your-key-vault.md). 

> [!WARNING]
> - Pokud jste předtím používali Azure Disk Encryption se službou Azure AD k šifrování virtuálního počítače, musíte tuto možnost použít k zašifrování virtuálního počítače. Podrobnosti najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-key-vault-aad.md) .

Vytvoření a konfigurace trezoru klíčů pro použití s Azure Disk Encryption zahrnuje tři kroky:

1. V případě potřeby vytvořte skupinu prostředků.
2. Vytváří se Trezor klíčů. 
3. Nastavují se zásady rozšířeného přístupu trezoru klíčů.

Tyto kroky jsou znázorněné v následujících rychlých startech:

- [Vytvoření a šifrování virtuálního počítače s Linuxem s využitím Azure CLI](disk-encryption-cli-quickstart.md)
- [Vytvoření a šifrování virtuálního počítače s Linuxem s využitím Azure PowerShellu](disk-encryption-powershell-quickstart.md)

Můžete také, pokud chcete, vygenerovat nebo importovat klíč šifrování klíče (KEK).

> [!Note]
> Kroky v tomto článku jsou automatizovány ve skriptu rozhraní příkazového [řádku Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started) a v tématu [Azure Disk Encryption předpoklady PowerShellu](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Nainstalovat nástroje a připojit se k Azure

Kroky v tomto článku se dají dokončit pomocí [Azure CLI](/cli/azure/), [Azure PowerShell Az Module](/powershell/azure/)nebo [Azure Portal](https://portal.azure.com). 

I když je portál přístupný prostřednictvím prohlížeče, Azure CLI a Azure PowerShell vyžadují místní instalaci; Podrobnosti najdete v tématu [Azure Disk Encryption pro Linux: Instalace nástrojů](disk-encryption-linux.md#install-tools-and-connect-to-azure) .

### <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

Než začnete používat rozhraní příkazového řádku Azure CLI nebo Azure PowerShell, musíte se nejdřív připojit k předplatnému Azure. Provedete to tak, že [se přihlásíte pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [přihlásíte se pomocí Azure PowerShellu](/powershell/azure/authenticate-azureps?view=azps-2.5.0)nebo po zobrazení výzvy dodáte přihlašovací údaje do Azure Portal.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
 
## <a name="next-steps"></a>Další kroky

- [Skript CLI pro Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started)
- [Skript prostředí PowerShell pro Azure Disk Encryption předpoklady](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Informace o [Azure Disk Encryption scénářích pro virtuální počítače se systémem Linux](disk-encryption-linux.md)
- Postup [řešení potíží s Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Přečtěte si [ukázkové skripty Azure Disk Encryption](disk-encryption-sample-scripts.md)
