---
title: Osobní údaje v Azure Resource Manageru | Dokumentace Microsoftu
description: Další informace o správě osobních údajů operace Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: tomfitz
ms.openlocfilehash: db4e1b8705b879fd5716763869462bafdf1f905c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66128457"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Správa osobních údajů pomocí Azure Resource Manageru

Aby se zabránilo úniku citlivých informací, odstraňte všechny osobní údaje, které jste poskytli v nasazení, skupiny prostředků nebo značky. Azure Resource Manager poskytuje operace, které vám umožní spravovat osobní údaje, které jste poskytli v nasazení, skupiny prostředků nebo značky.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Odstranění osobních údajů v historii nasazení

V případě nasazení Resource Manageru uchovává hodnoty parametrů a stavové zprávy v historii nasazení. Tyto hodnoty budou zachovány, dokud je odstranit z historie nasazení. Pokud chcete zjistit, jestli jste zadali osobních údajů v těchto hodnot, uveďte nasazení. Pokud je vyhledání osobních údajů, odstraňte z historie nasazení.

Do seznamu **nasazení** v historii, použijte:

* [Seznam podle skupin prostředků](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [seznam az skupiny nasazení](/cli/azure/group/deployment#az-group-deployment-list)

Chcete-li odstranit **nasazení** z historie, použijte:

* [Odstranění](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [Odstranit az skupiny nasazení](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Odstranění osobních údajů v názvy skupin prostředků

Název skupiny prostředků opakuje, dokud je neodstraníte skupinu prostředků. Pokud budete mít k dispozici osobních údajů v názvech najdete seznam skupin prostředků. Pokud je vyhledání osobních údajů, [přesunout prostředky](resource-group-move-resources.md) do nové skupiny prostředků a odstranit skupinu prostředků s osobními údaji v názvu.

Do seznamu **skupiny prostředků**, použijte:

* [Seznam](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [seznam skupin az](/cli/azure/group#az-group-list)

Chcete-li odstranit **skupiny prostředků**, použijte:

* [Odstranění](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Odstranění osobních údajů v značky

Názvy značek a hodnoty budou zachovány, dokud odstranit nebo upravit značky. Pokud jste zadali osobních údajů ve značkách najdete seznam značek. Pokud je vyhledání osobních údajů, odstraňte značky.

Do seznamu **značky**, použijte:

* [Seznam](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [seznam značek az](/cli/azure/tag#az-tag-list)

Chcete-li odstranit **značky**, použijte:

* [Odstranění](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [AZ tag delete](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Další postup
* Přehled Azure Resource Manageru, najdete v článku [co je Resource Manager?](resource-group-overview.md)