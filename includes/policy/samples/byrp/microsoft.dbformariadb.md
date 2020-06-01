---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 83b8e9d2bd65d04a5260495c5add3cb98288955c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235487"
---
|Název |Popis |Vliv (s) |Verze |GitHubu |
|---|---|---|---|---|
|[Pro Azure Database for MariaDB by měla být povolená geograficky redundantní záloha.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Tato zásada Audituje jakékoli Azure Database for MariaDB s geograficky redundantní zálohou, která není povolená. |Audit, zakázáno |1.0.0 |[Propojit](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[Server MariaDB by měl používat koncový bod služby virtuální sítě.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Tato zásada Audituje MariaDB servery, které nejsou nakonfigurované na používání koncového bodu služby virtuální sítě. Další podrobnosti najdete na adrese [https://aka.ms/mariadbvirtualnetwork](https://aka.ms/mariadbvirtualnetwork) . |AuditIfNotExists, zakázáno |1.0.1 |[Propojit](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Privátní koncový bod by měl být povolený pro MariaDB servery.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Tato zásada Audituje MariaDB servery, které nejsou nakonfigurované na používání privátního koncového bodu. Další podrobnosti najdete na adrese [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink) . |AuditIfNotExists, zakázáno |1.0.1 |[Propojit](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[Přístup k veřejné síti by měl být pro MariaDB servery zakázaný.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Tato zásada Audituje MariaDB servery ve vašem prostředí s povoleným přístupem k veřejné síti. Další podrobnosti najdete na adrese [https://go.microsoft.com/fwlink/?linkid=2119542](https://go.microsoft.com/fwlink/?linkid=2119542) . |Audit, zakázáno |1.0.0 |[Propojit](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
