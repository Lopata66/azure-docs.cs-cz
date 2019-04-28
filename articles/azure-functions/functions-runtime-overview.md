---
title: Přehled modulu Runtime Azure Functions | Dokumentace Microsoftu
description: Přehled služby ve verzi Preview modulu Runtime Azure Functions
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: 2af9575c50ee522d6330ddf46c75b666132b7a84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61021183"
---
# <a name="azure-functions-runtime-overview-preview"></a>Přehled modulu Runtime Azure Functions (preview)

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Modul Runtime služby Azure Functions (preview) poskytuje nový způsob, jakým lze využívat jednoduchost a pružnosti Azure Functions programování místním modelu. Vytvořená na základě stejné opensourcové kořeny jako funkce Azure Functions, modul Runtime služby Azure Functions je nasazená místně na poskytuje téměř stejné vývojové prostředí jako cloudová služba.

![Portál Preview modulu Runtime Azure Functions][1]

Modul Runtime služby Azure Functions poskytuje způsob, jak je chcete vyzkoušet Azure Functions před potvrzením do cloudu. Tímto způsobem prvky kódu, které vytváříte můžete pak se zobrazí stránka s vámi cloudu při migraci.  Modul runtime také otevírá nové možnosti, jako je třeba použití náhradní výpočetní výkon místních počítačů pro spuštění dávkové procesy přes noc. Můžete také použít zařízení ve vaší organizaci podmíněně odesílat data do jiných systémů, v místním prostředí i v cloudu.

Modul Runtime služby Azure Functions se skládá ze dvou částí:

* Role správy modulu Runtime Azure Functions
* Role pracovního procesu modulu Runtime Azure Functions

## <a name="azure-functions-management-role"></a>Role pro správu Azure Functions

Role pro správu Azure Functions poskytuje pro řízení funkcí na místní hostitele. Tato role provádí následující úlohy:

* Hostování funkcí portál pro správu Azure, který je stejný jako ten se zobrazí v [webu Azure portal](https://portal.azure.com). Portál poskytuje konzistentní prostředí, které vám umožní vyvíjet funkce stejně, jako byste na webu Azure Portal.
* Funkce distribuce napříč více funkcí pracovních procesů.
* Poskytuje koncový bod publikování tak, aby vaše funkce přímo ze sady Microsoft Visual Studio můžete publikovat tak, že stahování a import profilu publikování.

## <a name="azure-functions-worker-role"></a>Role pracovního procesu Azure Functions

Role pracovního procesu Azure Functions jsou nasazené v kontejnerech Windows a jsou, kde se spouští kód vaší funkce.  Můžete nasadit několik rolí pracovního procesu v rámci vaší organizace a tato možnost je klíče způsobem, ve kterém se zákazníci mohli použít náhradní výpočetní výkon.  Jedna je například tam, kde existuje náhradní výpočty v mnoha organizacích počítače neustále zapnutá, ale nepoužívá velké časová období.

## <a name="minimum-requirements"></a>Minimální požadavky

Abyste mohli začít s modul Runtime služby Azure Functions, musíte mít počítač s Windows serverem 2016 nebo Windows 10 Creators Update s přístupem k instanci systému SQL Server.

## <a name="next-steps"></a>Další kroky

Nainstalujte [modul Runtime služby Azure Functions ve verzi preview](https://aka.ms/azafrdoc)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png
