---
title: Ruční zastavení nebo spuštění skupiny kontejnerů
description: Přečtěte si, jak ručně zastavit nebo spustit skupinu kontejnerů v Azure Container Instances.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533425"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Ruční zastavení nebo spuštění kontejnerů v Azure Container Instances

Nastavení [zásad restartování](container-instances-restart-policy.md) skupiny kontejnerů určuje, jak se ve výchozím nastavení spouštějí a zastavují instance kontejnerů. Výchozí nastavení můžete přepsat ručním zastavením nebo spuštěním skupiny kontejnerů.

## <a name="stop"></a>Zastavit

Ručně zastavte spuštěnou skupinu kontejnerů – například pomocí příkazu [AZ Container stop][az-container-stop] nebo Azure Portal. U některých úloh kontejnerů můžete chtít zastavit dlouhodobou spuštěnou skupinu kontejnerů po definovaném období, aby se ušetřily náklady. 

*Když skupina kontejnerů vstoupí do stavu Zastaveno, ukončí a recykluje všechny kontejnery ve skupině. Nechová stav kontejneru.*

Po recyklaci kontejnerů jsou [prostředky](container-instances-container-groups.md#resource-allocation) uvolněny a pro skupinu kontejnerů se zastaví.

Akce zastavit nemá žádný vliv, pokud se skupina kontejnerů už ukončila (je v úspěšném nebo neúspěšném stavu). Například skupina kontejnerů s dokončenými úlohami kontejneru spustit po, která se úspěšně ukončila v úspěšném stavu. Pokusy o zastavení skupiny v tomto stavu nemění stav. 

## <a name="start"></a>Start

Když se skupina kontejnerů zastaví – buď z důvodu, že se kontejnery ukončily, nebo jste skupinu ručně zastavili – můžete spustit kontejnery. K ručnímu spuštění kontejnerů ve skupině použijte například příkaz [AZ Container Start][az-container-start] nebo Azure Portal. Pokud se aktualizuje image kontejneru pro libovolný kontejner, načte se nový obrázek. 

Spuštění skupiny kontejnerů zahájí nové nasazení se stejnou konfigurací kontejneru. Tato akce vám pomůže rychle znovu použít známou konfiguraci skupiny kontejnerů, která funguje podle očekávání. Nemusíte vytvářet novou skupinu kontejnerů ke spuštění stejné úlohy.

Tato akce spustí všechny kontejnery ve skupině kontejnerů. Ve skupině nemůžete spustit konkrétní kontejner.

Po ručním spuštění nebo restartování skupiny kontejnerů se skupina kontejnerů spustí podle nakonfigurovaných zásad restartování.
  
## <a name="restart"></a>Restartování

Skupinu kontejnerů můžete restartovat, pokud je spuštěná – například pomocí příkazu [AZ Container restart][az-container-restart] . Tato akce restartuje všechny kontejnery ve skupině kontejnerů. Pokud se aktualizuje image kontejneru pro libovolný kontejner, načte se nový obrázek. 

Restartování skupiny kontejnerů je užitečné, pokud chcete řešit potíže s nasazením. Pokud třeba dočasné omezení prostředků zabrání v úspěšném spuštění vašich kontejnerů, může problém vyřešit restartováním skupiny.

Tato akce restartuje všechny kontejnery ve skupině kontejnerů. V této skupině nemůžete restartovat konkrétní kontejner.

Po ručním restartování skupiny kontejnerů se skupina kontejnerů spustí podle nakonfigurovaných zásad restartování.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [nastavení zásad restartování](container-instances-restart-policy.md) v Azure Container Instances.

Kromě ručního zastavení a spuštění skupiny kontejnerů s existující konfigurací můžete [aktualizovat nastavení](container-instances-update.md) spuštěné skupiny kontejnerů.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
