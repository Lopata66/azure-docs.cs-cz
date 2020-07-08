---
title: Základní aktualizace obrázků – úkoly
description: Přečtěte si o základních imagí pro Image kontejnerů aplikací a o tom, jak může základní aktualizace image aktivovat úlohu Azure Container Registry.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 35933c4cdbbf2762f7a54bd945f8a8ffa55b9f21
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85918500"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Základní aktualizace obrázků pro úlohy ACR

Tento článek poskytuje základní informace o aktualizacích základní image aplikace a o tom, jak můžou tyto aktualizace aktivovat úlohu Azure Container Registry.

## <a name="what-are-base-images"></a>Jaké jsou základní image?

Fázemi definující většinu imagí kontejnerů určuje nadřazený obrázek, ze kterého je obrázek založen, často se označuje jako *základní obrázek*. Základní image obvykle obsahují operační systém, například [Alpine Linux][base-alpine] nebo [Windows Nano Server][base-windows], na kterém jsou použity zbývající vrstvy kontejneru. Můžou také obsahovat architektury aplikace, jako je třeba [Node.js][base-node] nebo [.NET Core][base-dotnet]. Tyto základní image jsou obvykle založené na veřejných nadřazených obrázcích. Několik imagí vaší aplikace může sdílet společný základní obrázek.

Základní image často aktualizuje maintainer image, aby zahrnovala nové funkce nebo vylepšení operačního systému nebo architektury v imagi. Dalším běžným důvodem pro aktualizaci základní image jsou opravy zabezpečení. Pokud dojde k těmto nadřazeným aktualizacím, je nutné také aktualizovat základní image, aby zahrnovaly kritickou opravu. Každý obrázek aplikace musí být pak znovu sestaven, aby zahrnoval tyto opravy pro odesílání dat, které jsou nyní součástí základní image.

V některých případech, jako je například soukromý vývojový tým, může základní obrázek určovat více než operační systém nebo architekturu. Základní bitová kopie může být například bitová kopie součásti sdílené služby, kterou je třeba sledovat. Členové týmu mohou potřebovat sledovat tuto základní image pro testování nebo musí při vývoji imagí aplikace pravidelně aktualizovat image.

## <a name="track-base-image-updates"></a>Sledovat základní aktualizace obrázků

ACR Tasks zahrnuje možnost, která za vás při aktualizaci základní image kontejnerů automaticky tyto image sestaví.

ACR úlohy dynamicky zjišťují základní závislosti obrázků při sestavení image kontejneru. V důsledku toho může rozpoznat, kdy se aktualizuje základní obrázek image aplikace. S jedním předkonfigurovaným úkolem sestavení můžou úlohy ACR automaticky znovu sestavit všechny image aplikace, které odkazují na základní image. Díky této automatické detekci a novému sestavování vám ACR úlohy šetří čas a úsilí obvykle potřebné k ručnímu sledování a aktualizaci jednotlivých imagí a všech imagí aplikace, které odkazují na aktualizovanou základní image.

## <a name="base-image-locations"></a>Základní umístění imagí

Pro sestavení imagí z souboru Dockerfile úloha ACR detekuje závislosti základních imagí v následujících umístěních:

* Stejný registr kontejneru Azure, ve kterém se úloha spouští
* Jiný privátní registr kontejneru Azure ve stejné nebo jiné oblasti 
* Veřejné úložiště v Docker Hub 
* Veřejné úložiště v Microsoft Container Registry

Pokud se základní bitová kopie zadaná v `FROM` příkazu nachází v jednom z těchto umístění, úloha ACR přidá vidlici, aby se zajistilo, že se image znovu sestaví i v případě, že se aktualizuje její základ.

## <a name="base-image-notifications"></a>Oznámení základní image

Čas mezi aktualizací základní image a aktivací závislého úkolu závisí na umístění základní Image:

* **Základní image z veřejného úložiště v Docker Hub nebo MCR** – pro základní image ve veřejných úložištích je úloha ACR vyhledává aktualizace imagí v náhodném intervalu od 10 do 60 minut. Závislé úlohy se spouštějí odpovídajícím způsobem.
* **Základní image z Azure Container Registry** – pro základní image ve službě Azure Container Registry okamžitě aktivuje úloha ACR spuštění, když se aktualizuje základní image. Základní obrázek může být ve stejném ACR, kde je úloha spuštěná, nebo v jiné ACR v jakékoli oblasti.

## <a name="additional-considerations"></a>Další aspekty

* **Základní image pro Image aplikací** – v současné době úloha ACR sleduje jenom základní aktualizace obrázků pro aplikace (*běhové*image). Nesleduje aktualizace základních imagí pro mezilehlé (*BuildTime*) image používané ve více fázích fázemi.  

* **Povoleno ve výchozím nastavení** – když vytvoříte úlohu ACR pomocí příkazu [AZ ACR Task Create][az-acr-task-create] , ve výchozím nastavení je úloha *povolená* pro aktivaci základní aktualizací image. To znamená, že `base-image-trigger-enabled` vlastnost je nastavena na hodnotu true. Pokud chcete toto chování v úloze zakázat, aktualizujte vlastnost na false. Například spusťte následující příkaz [AZ ACR Task Update][az-acr-task-update] :

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Aktivační událost ke sledování závislostí** – Pokud chcete, aby úloha ACR mohla určit a sledovat závislosti image kontejneru – což zahrnuje základní image, musíte nejdřív aktivovat úlohu, aby se image sestavila **aspoň jednou**. Úlohu můžete například aktivovat ručně pomocí příkazu [AZ ACR Task Run][az-acr-task-run] .

* **Stabilní značka pro základní image** – Chcete-li aktivovat úlohu na základě aktualizace základního obrázku, musí mít základní image *stabilní* značku, například `node:9-alpine` . Toto označení je typické pro základní bitovou kopii, která je aktualizována pomocí operačních systémů a oprav rozhraní .NET na nejnovější stabilní verzi. Pokud se základní image aktualizuje pomocí nové značky verze, neaktivuje úlohu. Další informace o označování obrázků naleznete v [doprovodnéch materiálech k osvědčeným postupům](container-registry-image-tag-version.md). 

* **Další triggery úloh** – v úloze aktivované aktualizacemi základních imagí můžete také povolit triggery na základě [potvrzení zdrojového kódu](container-registry-tutorial-build-task.md) nebo [plánu](container-registry-tasks-scheduled.md). Základní aktualizace image může také aktivovat úlohu s [více kroky](container-registry-tasks-multi-step.md).

## <a name="next-steps"></a>Další kroky

V následujících kurzech najdete scénáře pro automatizaci sestavení imagí aplikace po aktualizaci základní Image:

* [Automatizace sestavení imagí kontejneru při aktualizaci základní image ve stejném registru](container-registry-tutorial-base-image-update.md)

* [Automatizace sestavení imagí kontejneru při aktualizaci základní image v jiném registru](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
