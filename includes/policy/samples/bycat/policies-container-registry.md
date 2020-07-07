---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/23/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 8cf48a6d56af3b33188f177f78539cba1940b281
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85313767"
---
|Name<br /><sub>(Azure Portal)</sub> |Popis |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Registry kontejneru by měly být zašifrované pomocí klíče spravovaného zákazníkem (CMK)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Auditujte Registry kontejnerů, u kterých není šifrování povolené pomocí klíčů spravovaných zákazníkem (CMK). Další informace o šifrování CMK najdete na adrese: [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) . |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Registry kontejneru by neměly umožňovat neomezený přístup k síti.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Auditujte Registry kontejnerů, které nemají nakonfigurovaná pravidla sítě (IP nebo VNET), a ve výchozím nastavení povolí veškerý přístup k síti. Registry kontejnerů s aspoň jedním pravidlem IP/firewallu nebo nakonfigurovanou virtuální sítí se považují za vyhovující. Další informace o Container Registry síťových pravidel najdete na adrese: [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) . |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Registry kontejneru by měly používat privátní odkazy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Auditujte Registry kontejnerů, které nemají alespoň jedno schválené připojení privátního koncového bodu. Klienti ve virtuální síti můžou bezpečně přistupovat k prostředkům, které mají připojení privátního koncového bodu prostřednictvím privátních odkazů. Další informace najdete na adrese: [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) . |Audit, zakázáno |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
