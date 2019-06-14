---
title: Správa uživatelsky přiřazené spravovanou identitu pomocí Azure CLI
description: Podrobné pokyny o tom, jak vytvořit, vypsat a odstranit uživatel přiřazenou spravované identit pomocí Azure CLI.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28520b3ba5d4e62fd4e1c9b78e68cc7dc2b48c61
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60290991"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Vytvoření seznamu nebo odstranit uživatelem přidělenou spravovanou identitu pomocí Azure CLI

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytuje služby Azure s využitím spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření služby, které podporují ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

V tomto článku se dozvíte, jak vytvářet, vypsat a odstranit uživatelsky přiřazené spravovanou identitu pomocí Azure CLI.

## <a name="prerequisites"></a>Požadavky

- Pokud nejste obeznámeni s spravovaných identit pro prostředky Azure, podívejte se [oddílu přehled](overview.md). **Nezapomeňte si přečíst [rozdíl mezi systém přiřadil a uživatelsky přiřazené identity spravované](overview.md#how-does-it-work)** .
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Spuštění ukázkové skripty rozhraní příkazového řádku, máte tři možnosti:
    - Použití [Azure Cloud Shell](../../cloud-shell/overview.md) z portálu Azure portal (viz další část).
    - Použijte vložené Azure Cloud Shell pomocí "Vyzkoušet" tlačítka, nachází v pravém horním rohu každý blok kódu.
    - [Nainstalujte nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 nebo novější) Pokud byste radši chtěli použít místní konzoly příkazového řádku. Přihlaste se k Azure s využitím `az login`, pomocí účtu, který je přidružený k předplatnému Azure, pod kterým chcete nasadit, uživatel přiřazenou se identita spravované.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem 

Pro vytvoření uživatelsky přiřazené identity spravované, musí váš účet [Přispěvatel spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) přiřazení role.

Použití [vytvoření az identity](/cli/azure/identity#az-identity-create) příkaz pro vytvoření uživatel přiřazenou se identita spravované. `-g` Parametr určuje skupinu prostředků, kde k vytvoření uživatelsky přiřazené spravovanou identitu a `-n` parametr určuje její název. Nahradit `<RESOURCE GROUP>` a `<USER ASSIGNED IDENTITY NAME>` parametr hodnoty vlastními hodnotami:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Seznam uživatelsky přiřazené spravované identity

Do seznamu/čtení uživatelsky přiřazené spravovanou identitu, musí váš účet [operátor spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nebo [Přispěvatel spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) přiřazení role.

Seznam uživatelsky přiřazené identity spravované, použijte [seznam identit az](/cli/azure/identity#az-identity-list) příkazu. Nahraďte `<RESOURCE GROUP>` s vlastní hodnotou:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
V odpovědi json uživatelsky přiřazené identity spravované mají `"Microsoft.ManagedIdentity/userAssignedIdentities"` hodnotu pro klíč, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživateli

Pokud chcete odstranit spravovanou identitu uživatele přiřazeny, musí váš účet [Přispěvatel spravovaných identit](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) přiřazení role.

Chcete-li odstranit spravovanou identitu uživatele přiřazeny, použijte [az identity odstranit](/cli/azure/identity#az-identity-delete) příkazu.  Parametr - n Určuje její název a -g parametr určuje skupinu prostředků, kde byl vytvořen uživatelem přidělenou spravovaná identita. Nahradit `<USER ASSIGNED IDENTITY NAME>` a `<RESOURCE GROUP>` parametry hodnoty vlastními hodnotami:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Odstraňuje se uživatel přiřazenou spravovanou identitu nedojde k odebrání odkazu, ze všech prostředků, který byl přiřazen. Odeberte prosím z virtuálního počítače/škálovací sady pomocí `az vm/vmss identity remove` příkaz

## <a name="next-steps"></a>Další postup

Úplný seznam příkazů rozhraní příkazového řádku Azure identity najdete v tématu [az identity](/cli/azure/identity).

Informace o tom, jak přiřadit uživateli přiřazena spravované identity pro virtuální počítač Azure najdete [konfigurace spravovaných identit pro prostředky Azure na Virtuálním počítači Azure pomocí Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
