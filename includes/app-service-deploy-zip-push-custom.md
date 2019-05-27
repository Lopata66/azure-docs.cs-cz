---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132555"
---
## <a name="deployment-customization"></a>Vlastní nastavení nasazení

Proces nasazení předpokládá, že soubor .zip, který nahrajete obsahuje připravené ke spuštění aplikace. Ve výchozím nastavení se spustí bez přizpůsobení. Pokud chcete povolit stejné procesy sestavení, které jste získali díky průběžné integraci, přidejte následující nastavení aplikace:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

Při použití nabízené nasazení ZIP, toto nastavení je **false** ve výchozím nastavení. Výchozí hodnota je **true** pro průběžnou integraci nasazení. Pokud je nastavena na **true**, nastavení týkající se nasazení se používají během nasazení. Tato nastavení můžete nakonfigurovat buď jako nastavení aplikace, nebo v konfiguračním souboru .deployment, který se nachází v kořenovém adresáři souboru ZIP. Další informace najdete v tématu [úložiště a nastavení souvisejících s nasazením](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) v odkazu nasazení.