---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 543098ecd8936c94f489498820a6c59e4c9e3f40
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82966061"
---
|Name |Popis |Vliv (s) |Verze |GitHubu |
|---|---|---|---|---|
|[Auditovat využití vlastních pravidel RBAC](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Místo vlastních rolí RBAC, které jsou náchylné k chybám, byste měli auditovat předdefinované role, jako je vlastník, přispěvatel, čtenář. Použití vlastních rolí se považuje za výjimku a vyžaduje přísné kontroly a modelování hrozeb. |Audit, zakázáno |1.0.0 |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Vlastní role vlastníka předplatného by neměly existovat.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Tato zásada zajišťuje, že žádné vlastní role vlastníka předplatného neexistují. |Audit, zakázáno |1.0.0 |[Odkaz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |
