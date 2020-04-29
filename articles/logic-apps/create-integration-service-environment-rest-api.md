---
title: Vytváření prostředí ISEs (Integration Service Environment) pomocí Logic Apps REST API
description: Vytvořte prostředí ISE (Integration Service Environment) pomocí REST API Logic Apps, abyste mohli získat přístup k Azure Virtual Networks (virtuální sítě) z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 0670331d2338b4b6419ffbff1452b5fbac91029f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478828"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Vytvoření prostředí ISE (Integration Service Environment) pomocí Logic Apps REST API

Tento článek ukazuje, jak vytvořit [ *prostředí ISE (Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) prostřednictvím REST API Logic Apps ve scénářích, ve kterých aplikace logiky a účty pro integraci potřebují přístup k [virtuální síti Azure](../virtual-network/virtual-networks-overview.md). ISE je izolované prostředí, které využívá vyhrazené úložiště a další prostředky, které jsou udržovány odděleně od "globálního" víceklientské Logic Apps služby. Toto oddělení také snižuje vliv na výkon, který můžou mít jiní klienti Azure na výkon vašich aplikací. ISE také poskytuje vlastní statické IP adresy. Tyto IP adresy jsou oddělené od statických IP adres, které jsou sdílené pomocí Logic Apps ve veřejné víceklientské službě.

Můžete také vytvořit ISE pomocí [ukázkové Azure Resource Manager šablony pro rychlé](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) zprovoznění nebo pomocí [Azure Portal](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Logic Apps, integrované triggery, integrované akce a konektory spouštěné ve vašem ISE používají Cenový tarif, který se liší od cenového plánu založeného na spotřebě. Informace o cenách a fakturační práci pro ISEs najdete v článku o [cenovém modelu Logic Apps](../logic-apps/logic-apps-pricing.md#fixed-pricing). Cenové sazby najdete v tématu [Logic Apps ceny](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Požadavky

* Stejné [požadavky](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) a [požadavky pro povolení přístupu pro ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) jako při vytváření ISE v Azure Portal

* Nástroj, který můžete použít k vytvoření ISE, voláním Logic Apps REST API s požadavkem PUT protokolu HTTPS. Můžete například použít [post](https://www.getpostman.com/downloads/)nebo můžete vytvořit aplikaci logiky, která provede tuto úlohu.

## <a name="send-the-request"></a>Odeslat žádost

Pokud chcete vytvořit ISE voláním REST API Logic Apps, udělejte tuto žádost o vložení HTTPS:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Verze Logic Apps REST API 2019-05-01 vyžaduje, abyste si pro konektory ISE napravili vlastní požadavek HTTP PUT.

Nasazení obvykle trvá do dvou hodin, než se dokončí. V některých případech může nasazení trvat až čtyři hodiny. Pokud chcete zjistit stav nasazení, v [Azure Portal](https://portal.azure.com)na panelu nástrojů Azure vyberte ikonu oznámení, která otevře podokno oznámení.

> [!NOTE]
> Pokud se nasazení nepovede nebo odstraníte ISE, může Azure trvat až hodinu, než se vaše podsítě uvolní. Tato prodleva znamená, že možná budete muset počkat, než tyto podsítě znovu použijete v jiné ISE.
>
> Pokud virtuální síť odstraníte, Azure obvykle trvá až dvě hodiny, než se uvolní vaše podsítě, ale tato operace může trvat delší dobu. 
> Při odstraňování virtuálních sítí se ujistěte, že nejsou připojené žádné prostředky. 
> Viz [odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Hlavička požadavku

V hlavičce požadavku zahrňte tyto vlastnosti:

* `Content-type`: Nastavte tuto vlastnost na `application/json`hodnotu.

* `Authorization`: Nastavte tuto hodnotu vlastnosti na nosný token pro zákazníka, který má přístup k předplatnému Azure nebo skupině prostředků, kterou chcete použít.

### <a name="request-body-syntax"></a>Syntaxe textu žádosti

Tady je syntaxe textu žádosti, která popisuje vlastnosti, které se mají použít při vytváření ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>Příklad textu žádosti

Tento ukázkový text požadavku zobrazuje ukázkové hodnoty:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>Další kroky

* [Přidání prostředků do prostředí integrační služby](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

