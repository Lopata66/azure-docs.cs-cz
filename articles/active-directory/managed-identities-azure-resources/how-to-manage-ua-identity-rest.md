---
title: Správa spravovaných identit přiřazených uživatelem pomocí REST – Azure AD
description: Podrobné pokyny, jak vytvořit, vypsat a odstranit spravovanou identitu přiřazenou uživatelem pro REST API volání.
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
ms.date: 06/26/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39e108451e4c19e77e01b5bcc5d8dd21e86ad73a
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "74547414"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Vytvoření, vypsání nebo odstranění spravované identity přiřazené uživatelem pomocí REST API volání

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Spravované identity pro prostředky Azure poskytují službám Azure možnost ověřování pro služby, které podporují ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

V tomto článku se dozvíte, jak vytvořit, vypsat a odstranit spravovanou identitu přiřazenou uživatelem pomocí OBLÉ k provedení REST API volání.

## <a name="prerequisites"></a>Požadavky

- Pokud neznáte spravované identity prostředků Azure, přečtěte si [část přehled](overview.md). **Nezapomeňte si projít [rozdíl mezi spravovanou identitou přiřazenou systémem a uživatelem](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- Pokud používáte systém Windows, nainstalujte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo použijte [Azure Cloud Shell](../../cloud-shell/overview.md) v Azure Portal.
- Pokud používáte [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about) nebo [distribuci systému Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [nainstalujte místní konzolu Azure CLI](/cli/azure/install-azure-cli).
- Pokud používáte místní konzolu Azure CLI, přihlaste se k Azure `az login` pomocí účtu, který je přidružený k předplatnému Azure. chcete nasadit nebo načíst informace o spravované identitě přiřazené uživatelem.
- Načte přístupový token nosiče pomocí `az account get-access-token` k provedení následujících operací spravované identity přiřazené uživatelem.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem 

K vytvoření spravované identity přiřazené uživatelem potřebuje váš účet přiřazení role [Přispěvatel spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Hlavičky požadavku**

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Udělován*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        |

**Text požadavku**

|Název  |Popis  |
|---------|---------|
|location     | Povinná hodnota. Umístění prostředku.        |

## <a name="list-user-assigned-managed-identities"></a>Výpis spravovaných identit přiřazených uživatelem

K vypsání nebo načtení spravované identity přiřazené uživatelem vyžaduje váš účet [spravovaný operátor identity](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nebo přiřazení role [Přispěvatel spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Hlavička požadavku  |Popis  |
|---------|---------|
|*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Udělován*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Odstranění spravované identity přiřazené uživatelem

Pokud chcete odstranit spravovanou identitu přiřazenou uživatelem, váš účet potřebuje přiřazení role [Přispěvatel spravované identity](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

> [!NOTE]
> Odstraněním spravované identity přiřazené uživatelem se neodebere odkaz z žádného prostředku, ke kterému byl přiřazen. Pokud chcete odebrat uživatelem přiřazenou identitu z virtuálního počítače pomocí technologie KUDRLINKOU, podívejte se na téma [Odebrání uživatelsky přiřazené identity z virtuálního počítače Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Hlavička požadavku  |Popis  |
|---------|---------|
|*Typ obsahu*     | Povinná hodnota. Nastavte na `application/json`.        |
|*Udělován*     | Povinná hodnota. Nastavte na platný `Bearer` přístupový token.        |

## <a name="next-steps"></a>Další kroky

Informace o tom, jak přiřadit uživatelsky spravovanou identitu k VIRTUÁLNÍmu počítači Azure/VMSS pomocí sady prostředků, najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí volání REST API](qs-configure-rest-vm.md#user-assigned-managed-identity) a [Konfigurace spravovaných identit pro prostředky Azure ve službě škálování virtuálního počítače pomocí REST API volání](qs-configure-rest-vmss.md#user-assigned-managed-identity).
