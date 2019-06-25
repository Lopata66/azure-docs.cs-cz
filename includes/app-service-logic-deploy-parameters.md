---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: f188f2c7bea511f1109d37ef49563e0f745a770e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175426"
---
S Azure Resource Manageru, můžete definovat parametry pro hodnoty pro použití při nasazování šablony. Šablona obsahuje `parameters` oddíl, který obsahuje všechny hodnoty parametrů. Každá hodnota parametru používá k definování prostředků, které chcete nasadit šablonu.

> [!NOTE]
> Nedefinujte parametry pro hodnoty, které jsou vždy stejné. Definujte parametry jenom pro hodnoty, které se liší podle projektu, který nasazujete nebo podle prostředí, do kterého nasazujete.

Při definování parametrů:

* Pokud chcete zadat povolené hodnoty, které můžete během nasazení zadat uživatele, použijte **allowedValues** pole.

* Pokud chcete přiřadit výchozí hodnoty parametru, když se nezadají žádné hodnoty během nasazení, použijte **defaultValue** pole. 
