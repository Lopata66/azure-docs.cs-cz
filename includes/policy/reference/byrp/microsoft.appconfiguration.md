---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: bd4990fe25c60f09ecc44a12e8ca000d4facb03e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555452"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |Vliv (s) |Verze<br /><sub>GitHubu</sub> |
|---|---|---|---|
|[Konfigurace aplikace by měla používat klíč spravovaný zákazníkem](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |Klíče spravované zákazníkem poskytují rozšířenou ochranu dat tím, že vám umožní spravovat šifrovací klíče. To se často vyžaduje pro splnění požadavků na dodržování předpisů. |Audit, zamítnutí, zakázáno |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json) |
|[Konfigurace aplikace by měla používat privátní odkaz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Privátní odkaz Azure umožňuje připojit virtuální síť ke službám Azure bez veřejné IP adresy ve zdroji nebo cíli. Platforma privátních odkazů zpracovává připojení mezi klientem a službami přes páteřní síť Azure. Namapováním privátních koncových bodů na instance konfigurace aplikace namísto celé služby budete chráněni před riziky úniku dat. Další informace najdete tady: [https://aka.ms/appconfig/private-endpoint](https://aka.ms/appconfig/private-endpoint) . |AuditIfNotExists, zakázáno |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
