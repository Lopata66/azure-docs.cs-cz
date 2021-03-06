---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2689e81b089147dfc913fb119e0a499d60574b60
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050203"
---
V tomto kroku otestujete naslouchací proces skupiny dostupnosti pomocí klientské aplikace, která běží ve stejné síti.

Připojení klienta má následující požadavky:

* Klientská připojení k naslouchacímu procesu musí pocházet z počítačů, které se nacházejí v jiné cloudové službě, než je ta, která je hostitelem replik dostupnosti Always On.
* Pokud jsou repliky Always On v různých podsítích, klienti musí v připojovacím řetězci zadat *MultiSubnetFailover = true* . Tato podmínka vede k pokusům o paralelní připojení k replikám v různých podsítích. Tento scénář zahrnuje nasazení skupiny dostupnosti AlwaysOn pro různé oblasti.

Jedním z příkladů je připojit se k naslouchacímu procesu z jednoho z virtuálních počítačů ve stejné virtuální síti Azure (ale ne jednoho, který hostuje repliku). Snadný způsob, jak tento test provést, je zkusit připojit SQL Server Management Studio k naslouchacího procesu skupiny dostupnosti. Další jednoduchou metodou je spustit [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx), a to následujícím způsobem:

```console
sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15
```

> [!NOTE]
> Pokud je hodnota EndpointPort *1433*, není nutné ji zadat v volání. Předchozí volání také předpokládá, že je klientský počítač připojen ke stejné doméně a že volajícímu bylo uděleno oprávnění k databázi pomocí ověřování systému Windows.
> 
> 

Při testování naslouchacího procesu nezapomeňte převzít skupinu dostupnosti, abyste se ujistili, že se klienti mohou připojovat k naslouchacímu procesu v rámci převzetí služeb při selhání.

