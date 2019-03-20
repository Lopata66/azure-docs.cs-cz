---
title: Skladové položky registru kontejnerů Azure
description: Porovnání různých úrovních služby dostupné ve službě Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2018
ms.author: danlep
ms.openlocfilehash: 8fc364eac619c2f23ad2db1051a9b3ffa1bd38d6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58138078"
---
# <a name="azure-container-registry-skus"></a>Skladové položky registru kontejnerů Azure

Azure Container Registry (ACR) je k dispozici v několika úrovních služeb, označované jako skladové položky. Tyto skladové položky poskytují předvídatelné ceny a několik možností, zarovnání struktury kapacitu a využití privátního registru Dockeru v Azure.

| Skladová jednotka (SKU) | Spravované | Popis |
| --- | :-------: | ----------- |
| **Basic** | Ano | Vstupní bod optimalizovaný z hlediska nákladů pro vývojáře, kteří se seznamují se službou Azure Container Registry. Základní registry mají stejné programové funkce jako Standard a Premium (Integrace ověřování Azure Active Directory, odstranění image a webhooky). Zahrnuté úložiště a propustnost bitové kopie jsou však nejvhodnější pro nižší scénáře použití. |
| **Standard** | Ano | Standardními registry nabízí stejné funkce jako základní s zahrnuty vyšší propustnost úložiště a image. Registry úrovně Standard by měly vyhovovat požadavkům většiny produkčních scénářů. |
| **Premium** | Ano | Registry úrovně Premium poskytují nejvyšší velikost zahrnutého úložiště a souběžné operace povolení scénářů s vysokými objemy. Kromě vyšší propustnost image Premium přidává funkce včetně [geografickou replikaci] [ container-registry-geo-replication] pro správu jednoho registru napříč několika oblastmi a [obsahu důvěryhodnosti(veverzipreview)](container-registry-content-trust.md) k podepisování značka obrázku, a [brány firewall a virtuální sítě (preview)](container-registry-vnet.md) k omezení přístupu k registru. |
| Classic<sup>1</sup> | Ne | Tato skladová položka povolena počáteční verzi služby Azure Container Registry v Azure. Registry Classic se zálohují na účet úložiště Azure se vytvoří v rámci vašeho předplatného, což omezí možnosti pro službu ACR a poskytují vyšší úrovně možnosti, jako je například vyšší propustnost a geografická replikace. |

<sup>1</sup> Classic SKU bude **zastaralé** v **. března 2019**. Použití úrovně Basic, Standard nebo Premium pro všechny nové registry kontejnerů.

Když zvolíte že vyšší skladovou Položku poskytuje větší výkon a škálování, ale všechny spravované skladové položky nabízejí stejné programové funkce. S více úrovní služeb můžete začít s Basic pak převod na Standard a Premium jako zvýšení využití vašeho registru.

## <a name="managed-vs-unmanaged"></a>Spravované nebo nespravované

Basic, Standard a SKU úrovně Premium se souhrnně nazývají *spravované* registry a registry Classic jako *nespravované*. Hlavní rozdíl mezi nimi je způsob uložení imagí kontejnerů.

### <a name="managed-basic-standard-premium"></a>Managed (Basic, Standard, Premium)

Spravované registry výhody z úložiště image kompletně spravované službou Azure. To znamená, že účet úložiště, která ukládá váš obrázky se nezobrazí v rámci vašeho předplatného Azure. Existuje více výhod získaných pomocí jedné z spravovaný registr SKU, popsané hlouběji v [úložiště image kontejneru ve službě Azure Container Registry][container-registry-storage]. Tento článek se zaměřuje na spravovaný registr skladové položky a jejich funkce.

### <a name="unmanaged-classic"></a>Nespravované (Classic)

> [!IMPORTANT]
> Klasické SKU je zastaralé a budou k dispozici od března 2019. Použijte pro všechny nové registry úrovně Basic, Standard nebo Premium.

Registry Classic jsou "nespravovaného" v tom smyslu, že účet úložiště, která zálohuje registru Classic se nachází v rámci *vaše* předplatného Azure. V důsledku toho můžete zodpovídají za správu účtu úložiště, ve kterém jsou uložené vaše Image kontejneru. Pomocí nespravovaných registrů, nemůžete přepínat mezi skladové položky podle vašich aktuálních potřeb (jiné než [upgrade] [ container-registry-upgrade] na spravovaný registr), a (například nejsou k dispozici několik funkcí spravované registry odstranění image kontejneru, [geografickou replikaci][container-registry-geo-replication], a [webhooky][container-registry-webhook]).

Další informace o upgrade registru Classic k jednomu z spravovanými skladovými položkami, naleznete v tématu [Upgrade registru Classic][container-registry-upgrade].

## <a name="sku-feature-matrix"></a>Přehled funkcí SKU

Následující tabulka obsahuje podrobnosti o funkcích a omezení úrovní služeb Basic, Standard a Premium.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>Změna skladové položky

Můžete změnit SKU registru pomocí Azure CLI nebo na webu Azure Portal. Vám můžou volně přesouvat mezi spravovanými skladovými položkami za předpokladu, SKU přecházíte k má požadovaná maximální úložnou kapacitu. Pokud přejdete z modelu Classic na jednu z spravovanými skladovými položkami, nelze přesunout zpět do klasické – je jednosměrná převod.

### <a name="azure-cli"></a>Azure CLI

Chcete-li přechodu mezi skladovými položkami v Azure CLI, použijte [az acr update] [ az-acr-update] příkazu. Chcete-li například přepnout na Premium:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>portál Azure

V registru kontejneru **přehled** na webu Azure Portal, vyberte **aktualizace**, vyberte novou **SKU** z rozevírací nabídky SKU.

![Aktualizace skladové položky registru kontejneru na webu Azure portal][update-registry-sku]

Pokud máte registru Classic, nelze vybrat spravované skladové položky na webu Azure portal. Místo toho je nutné nejprve [upgradovat] [ container-registry-upgrade] na spravovaný registr (naleznete v tématu [změna z modelu nasazení Classic](#changing-from-classic)).

## <a name="changing-from-classic"></a>Změna z modelu nasazení Classic

Některé další aspekty vzít v úvahu při migraci do spravované Basic, Standard nebo SKU úrovně Premium nespravované registru Classic. Pokud váš registr Classic obsahuje velký počet imagí a je mnoho gigabajtů velikost, proces migrace může nějakou dobu trvat. Kromě toho `docker push` operace jsou zakázané, dokud se nedokončí migrace.

Podrobnosti o upgrade registru Classic k jednomu z spravovanými skladovými položkami, naleznete v tématu [Upgrade registru Classic kontejneru][container-registry-upgrade].

## <a name="pricing"></a>Ceny

Informace o cenách v každém skladové jednotky Azure Container Registry, najdete v článku [Container Registry ceny][container-registry-pricing].

Podrobnosti o cenách pro přenosy dat najdete v tématu [podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Další postup

**Azure Container Registry Roadmap**

Přejděte [plán služby ACR] [ acr-roadmap] na Githubu najdete informace o chystaných funkcí ve službě.

**Azure Container Registry UserVoice**

Odeslat a hlasovat o doporučeních nové funkce v [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-webhook]: container-registry-webhook.md
