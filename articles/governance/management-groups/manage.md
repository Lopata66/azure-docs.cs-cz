---
title: Jak změnit, odstranit nebo spravovat skupiny pro správu – zásady správného řízení Azure
description: Naučte se zobrazovat, udržovat, aktualizovat a odstraňovat hierarchii skupin pro správu.
author: rthorn17
ms.service: governance
ms.date: 05/22/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 28bfabd5c28fe833050551e7be690c0ea3d1e6a0
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338939"
---
# <a name="manage-your-resources-with-management-groups"></a>Správa prostředků pomocí skupin pro správu

Pokud má vaše organizace mnoho předplatných, možná budete potřebovat způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů pro tato předplatná. Skupiny pro správu Azure představují úroveň rozsahu nad předplatnými. Předplatná uspořádáte do kontejnerů označovaných jako skupiny pro správu a na tyto skupiny pro správu použijete své zásady správného řízení. Všechna předplatná v rámci skupiny pro správu automaticky dědí podmínky, které se na příslušnou skupinu pro správu vztahují.

Skupiny pro správu poskytují správu na podnikové úrovni ve velkém měřítku bez ohledu na to, jaké typy předplatného případně máte.  Další informace o skupinách pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>Změna názvu skupiny pro správu

Název skupiny pro správu můžete změnit pomocí portálu, PowerShellu nebo rozhraní příkazového řádku Azure CLI.

### <a name="change-the-name-in-the-portal"></a>Změna názvu na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny** > **skupiny pro správu**služeb.

1. Vyberte skupinu pro správu, kterou chcete přejmenovat.

1. Vyberte **Podrobnosti**.

1. V horní části stránky vyberte možnost **Přejmenovat skupinu** .

   ![Možnost Přejmenovat skupinu na stránce skupiny pro správu](./media/detail_action_small.png)

1. Po otevření nabídky zadejte nový název, který chcete zobrazit.

   ![Přejmenovat skupinu pro správu přejmenováním podokna skupiny](./media/rename_context.png)

1. Vyberte **Uložit**.

### <a name="change-the-name-in-powershell"></a>Změna názvu v PowerShellu

K aktualizaci zobrazovaného názvu použijte **Update-AzManagementGroup**. Pokud například chcete změnit zobrazované jméno skupin pro správu z "contoso IT" na "skupina contoso", spustíte následující příkaz:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Změna názvu v Azure CLI

V případě Azure CLI použijte příkaz Update.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Odstranění skupiny pro správu

Chcete-li odstranit skupinu pro správu, musí být splněny následující požadavky:

1. Ve skupině pro správu nejsou žádné podřízené skupiny pro správu ani předplatná.

   - Pokud chcete přesunout předplatné ze skupiny pro správu, přečtěte si téma [Přesun předplatného do jiné skupiny pro správu](#move-subscriptions-in-the-hierarchy).

   - Chcete-li přesunout skupinu pro správu do jiné skupiny pro správu, přečtěte si téma [přesunutí skupin pro správu v hierarchii](#move-management-groups-in-the-hierarchy).

1. Máte oprávnění k zápisu do skupiny pro správu (vlastník, přispěvatel nebo přispěvatel skupiny pro správu). Chcete-li zjistit, jaká oprávnění máte, vyberte skupinu pro správu a pak vyberte **IAM**. Další informace o rolích RBAC najdete v tématu [Správa přístupu a oprávnění pomocí RBAC](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Odstranit na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny** > **skupiny pro správu**služeb.

1. Vyberte skupinu pro správu, kterou chcete odstranit.

1. Vyberte **Podrobnosti**.

1. Vybrat **Odstranit**

    > [!TIP]
    > Pokud je ikona zakázaná, můžete ukazatel myši na ikonu zobrazit důvod.

   ![Možnost odstranění skupiny](./media/delete.png)

1. Otevře se okno s potvrzením, že chcete odstranit skupinu pro správu.

   ![Okno pro potvrzení odstranění skupiny](./media/delete_confirm.png)

1. Vyberte **Ano**.

### <a name="delete-in-powershell"></a>Odstranit v PowerShellu

Pomocí příkazu **Remove-AzManagementGroup** v prostředí PowerShell odstraňte skupiny pro správu.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Odstranění v Azure CLI

Pomocí Azure CLI, použijte příkaz AZ Account Management-Group DELETE.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Zobrazit skupiny pro správu

Můžete zobrazit jakoukoli skupinu pro správu, na které máte přímo nebo zděděnou roli RBAC.  

### <a name="view-in-the-portal"></a>Zobrazit na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny** > **skupiny pro správu**služeb.

1. Načte se stránka hierarchie skupiny pro správu. Na této stránce můžete prozkoumat všechny skupiny pro správu a odběry, ke kterým máte přístup. Výběr názvu skupiny vám převezme úroveň v hierarchii. Navigace funguje stejně jako Průzkumník souborů.

1. Chcete-li zobrazit podrobnosti o skupině pro správu, vyberte odkaz **(podrobnosti)** vedle názvu skupiny pro správu. Pokud tento odkaz není k dispozici, nemáte oprávnění k zobrazení této skupiny pro správu.

   ![Hlavní](./media/main.png)

### <a name="view-in-powershell"></a>Zobrazit v PowerShellu

K načtení všech skupin použijte příkaz Get-AzManagementGroup.  Úplný seznam skupin pro správu získat příkazy prostředí PowerShell najdete v tématu [AZ.](/powershell/module/az.resources/Get-AzManagementGroup) Resources – moduly.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Pro informace o jedné skupině pro správu použijte parametr-název_skupiny.

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Pokud chcete vrátit konkrétní skupinu pro správu a všechny úrovně hierarchie pod ní, použijte parametry **-expand** a **-** rekurze.  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Zobrazit v Azure CLI

K načtení všech skupin slouží příkaz list.  

```azurecli-interactive
az account management-group list
```

Informace o jedné skupině pro správu získáte pomocí příkazu show.

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Pokud chcete vrátit konkrétní skupinu pro správu a všechny úrovně hierarchie pod ní, použijte parametry **-expand** a **-** rekurze.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Přesunout odběry v hierarchii

Jedním z důvodů, proč vytvořit skupinu pro správu, je seskupit odběry dohromady. Podřízené položky jiné skupiny pro správu lze vytvořit pouze pro skupiny pro správu a odběry. Předplatné, které se přesune do skupiny pro správu, zdědí všechny uživatelské přístupy a zásady z nadřazené skupiny pro správu.

Chcete-li přesunout předplatné, musí být splněna všechna následující oprávnění RBAC:

- Role Owner (Vlastník) v podřízeném předplatném.
- Role Owner (Vlastník), přispěvatel nebo přispěvatel skupiny pro správu v cílové nadřazené skupině pro správu.
- Role vlastník, přispěvatel nebo přispěvatel skupiny pro správu v existující nadřazené skupině pro správu.

Pokud je cílová nebo existující nadřazená skupina pro správu kořenovou skupinou pro správu, požadavky na oprávnění se nepoužijí. Vzhledem k tomu, že kořenová skupina pro správu je výchozím cílovým bodem pro všechny nové skupiny pro správu a odběry, nepotřebujete pro ni oprávnění k přesunutí položky.

Pokud je role vlastníka v předplatném zděděná z aktuální skupiny pro správu, cíle přesunutí jsou omezené. Předplatné můžete přesunout jenom do jiné skupiny pro správu, kde máte roli vlastníka. Nemůžete ho přesunout do skupiny pro správu, kde jste přispěvatel, protože byste ztratili vlastnictví tohoto předplatného. Pokud jste přiřazeni přímo k roli vlastníka pro předplatné (nedědí se ze skupiny pro správu), můžete ho přesunout do jakékoli skupiny pro správu, kde jste přispěvatelem.

Chcete-li zjistit, jaká oprávnění máte v Azure Portal, vyberte skupinu pro správu a pak vyberte **IAM**. Další informace o rolích RBAC najdete v tématu [Správa přístupu a oprávnění pomocí RBAC](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Přesunout předplatná na portálu

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Přidání existujícího předplatného do skupiny pro správu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny** > **skupiny pro správu**služeb.

1. Vyberte skupinu pro správu, které plánujete mít jako nadřazenou.

1. V horní části stránky vyberte **Přidat předplatné**.

1. Vyberte v seznamu předplatné se správným ID.

   ![Dostupná předplatná, která se mají přidat do skupiny pro správu](./media/add_context_sub.png)

1. Vyberte Save (Uložit).

#### <a name="remove-a-subscription-from-a-management-group"></a>Odebrání předplatného ze skupiny pro správu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny** > **skupiny pro správu**služeb.

1. Vyberte skupinu pro správu, kterou plánujete, která je aktuální nadřazenou položkou.  

1. Vyberte tři tečky na konci řádku pro odběr v seznamu, který chcete přesunout.

   ![Možnost přesunutí ve skupině pro správu](./media/move_small.png)

1. Vyberte **přesunout**.

1. V nabídce, která se otevře, vyberte **nadřazenou skupinu pro správu**.

   ![Přesune podokno pro změnu nadřazené skupiny.](./media/move_small_context.png)

1. Vyberte **Uložit**.

### <a name="move-subscriptions-in-powershell"></a>Přesunout předplatná v PowerShellu

Pokud chcete přesunout předplatné v PowerShellu, použijte příkaz New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Chcete-li odebrat propojení mezi a odběrem a skupinou pro správu, použijte příkaz Remove-AzManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Přesun předplatných v Azure CLI

Chcete-li přesunout odběr v rozhraní příkazového řádku, použijte příkaz Add.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Chcete-li odebrat odběr ze skupiny pro správu, použijte příkaz Odebrat odběr.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Přesunutí skupin pro správu v hierarchii  

Když přesunete nadřazenou skupinu pro správu, přesune se k ní hierarchie v této skupině. Přístup, který potřebujete k přesunutí skupin pro správu, najdete v tématu [přístup ke skupině pro správu](overview.md#management-group-access).

### <a name="move-management-groups-in-the-portal"></a>Přesunutí skupin pro správu na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny** > **skupiny pro správu**služeb.

1. Vyberte skupinu pro správu, které plánujete mít jako nadřazenou.

1. V horní části stránky vyberte **Přidat skupinu pro správu**.

1. V nabídce, která se otevře, vyberte, jestli chcete novou nebo použít existující skupinu pro správu.

   - Když vyberete nový, vytvoří se nová skupina pro správu.
   - Když vyberete existující, zobrazí se rozevírací seznam všech skupin pro správu, které můžete přesunout do této skupiny pro správu.  

   ![Přesunout skupinu pro správu do nové nebo existující skupiny](./media/add_context_MG.png)

1. Vyberte **Uložit**.

### <a name="move-management-groups-in-powershell"></a>Přesunutí skupin pro správu v PowerShellu

Pomocí příkazu Update-AzManagementGroup v prostředí PowerShell přesuňte skupinu pro správu pod jinou skupinu.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Přesunutí skupin pro správu v Azure CLI

Pomocí příkazu Update přesuňte skupinu pro správu pomocí Azure CLI.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Audit skupin pro správu s využitím protokolů aktivit

Skupiny pro správu se podporují v rámci [protokolu aktivit Azure](../../azure-monitor/platform/activity-logs-overview.md). Můžete se dotázat na všechny události, ke kterým dochází ve skupině pro správu ve stejném centrálním umístění jako ostatní prostředky Azure.  Pro konkrétní skupinu pro správu si můžete si zobrazit všechny změny přiřazení zásad nebo přiřazení rolí.

![Protokoly aktivit se skupinami pro správu](media/al-mg.png)

Pokud se chcete na skupiny pro správu dotazovat mimo Azure Portal, cílový obor pro skupiny pro správu vypadá takto: **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** .

## <a name="referencing-management-groups-from-other-resource-providers"></a>Odkazování na skupiny pro správu od jiných poskytovatelů prostředků

Při odkazování na skupiny pro správu z jiných akcí poskytovatele prostředků použijte jako obor následující cestu. Tato cesta se používá při použití prostředí PowerShell, rozhraní příkazového řádku Azure a rozhraní REST API.  

>"/providers/Microsoft.Management/managementGroups/{yourMgID}"

Příkladem použití této cesty je při přiřazování přiřazení nové role ke skupině pro správu v prostředí PowerShell.

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Stejná cesta oboru se používá při načítání definice zásady ve skupině pro správu.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Další kroky

Další informace o řešeních pro správu najdete v následujících tématech:

- [Vytváření skupin pro správu pro organizaci prostředků Azure](create.md)
- [Jak měnit, odstraňovat nebo spravovat skupiny pro správu](manage.md)
- [Kontrola skupin pro správu v modulu Prostředky Azure PowerShellu](/powershell/module/az.resources#resources)
- [Kontrola skupin pro správu v rozhraní REST API](/rest/api/resources/managementgroups)
- [Kontrola skupin pro správu v Azure CLI](/cli/azure/account/management-group)