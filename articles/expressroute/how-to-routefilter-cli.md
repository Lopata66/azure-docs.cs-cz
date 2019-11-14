---
title: 'ExpressRoute: filtry tras – partnerské vztahy Microsoftu: Azure CLI'
description: Tento článek popisuje postup konfigurace filtrů směrování pro Peering Microsoft pomocí Azure CLI
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: anzaman
ms.openlocfilehash: c3c50a005e119890fb17fcf7b3114a747bbe34bf
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033419"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-cli"></a>Konfigurace filtrů směrování pro partnerský vztah Microsoftu: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Filtry tras představují způsob, jak spotřebovat dílčí sadu podporovaných služeb přes partnerský vztah Microsoftu. Kroky v tomto článku vám pomůžou nakonfigurovat a spravovat filtrů směrování pro okruhy ExpressRoute.

Služby Office 365, jako je Exchange Online, SharePoint Online a Skype pro firmy, jsou přístupné prostřednictvím partnerského vztahu Microsoftu. Po vytvoření partnerského vztahu Microsoftu je nakonfigurované v okruhu ExpressRoute, jsou související s těmito službami všechny předpony inzerované prostřednictvím relace protokolu BGP, které jsou vytvořeny. Ke každé předponě je připojená hodnota komunity protokolu BGP, která identifikuje službu nabízenou prostřednictvím dané předpony. Tyto hodnoty komunity protokolu BGP a služeb, které jsou mapovány seznam najdete v tématu [komunit protokolu BGP](expressroute-routing.md#bgp).

Pokud budete vyžadovat připojení ke všem službám, jsou velké množství předpony inzerované prostřednictvím protokolu BGP. To výrazně zvyšuje velikost tabulky směrování udržována směrovače v rámci vaší sítě. Pokud budete chtít využívat pouze podmnožinu službám nabízeným prostřednictvím partnerského vztahu Microsoftu, můžete snížit velikost tabulek směrování dvěma způsoby. Můžete:

* Filtrování nežádoucí předpony použitím filtry tras na komunit protokolu BGP. Je standardní postup sítě a se běžně používá v rámci více sítí.

* Definujte filtry tras a použít je pro váš okruh ExpressRoute. Filtr tras se nový prostředek, který vám umožní vybrat seznam služeb, které budete chtít využívat prostřednictvím partnerského vztahu Microsoftu. ExpressRoute směrovače pouze odeslat seznam předpon, které patří do služby určené ve filtru tras.

### <a name="about"></a>O filtry tras

Po vytvoření partnerského vztahu Microsoftu je nakonfigurovaná na váš okruh ExpressRoute, hraničním směrovačům Microsoft vytvořit dvojici relací protokolu BGP s hraniční směrovače (té vaší nebo váš poskytovatel připojení). Do vaší sítě se žádné trasy neinzerují. Pokud chcete povolit inzerování tras do vaší sítě, je potřeba k ní přidružit filtr tras.

Filtr tras umožňuje identifikovat služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu s vaším okruhem ExpressRoute. V podstatě se jedná o seznam všech povolených hodnot komunity protokolu BGP. Po definování prostředku filtru tras a jeho připojení k okruhu ExpressRoute se do vaší sítě budou inzerovat všechny předpony, které se mapují na hodnoty komunity protokolu BGP.

Aby bylo možné připojit filtry tras se službami Office 365 s nimi, musíte mít oprávnění k využívání služeb Office 365 přes ExpressRoute. Pokud nemáte oprávnění k využívání služeb Office 365 přes ExpressRoute, operace připojit filtry tras se nezdaří. Další informace o procesu ověřování najdete v tématu [Azure ExpressRoute pro Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Partnerský vztah Microsoftu okruhů ExpressRoute, které byly nakonfigurovány před 1. srpna 2017 budou mít všechny služby předpony inzerované prostřednictvím Microsoft partnerský vztah, i když nejsou definovány filtry tras. Partnerský vztah Microsoftu okruhů ExpressRoute, které jsou nakonfigurované 1. srpna 2017 nebo později nebude mít všechny předpony inzerované, dokud se filtr tras je připojen k okruhu.
> 
> 

### <a name="workflow"></a>Pracovní postup

Abyste mohli úspěšně připojit ke službám prostřednictvím partnerského vztahu Microsoftu, třeba provést následující kroky konfigurace:

* Musíte mít aktivní okruh ExpressRoute, který má Microsoft partnerský vztah zřízené. Postupujte podle následujících pokynů můžete použít k provedení následujících úkolů:
  * [Vytvoření okruhu ExpressRoute](howto-circuit-cli.md) a mějte ho povolený podle svého poskytovatele připojení, než budete pokračovat. Okruh ExpressRoute musí být ve stavu zřízený a povolený.
  * [Vytvoření partnerského vztahu Microsoftu](howto-routing-cli.md) při správě přímo relace protokolu BGP. Nebo váš poskytovatel připojení zřízení partnerského vztahu Microsoftu pro váš okruh.

* Musíte vytvořit a konfigurovat filtr tras.
  * Určete služby vám využívat prostřednictvím partnerského vztahu Microsoftu
  * Určení seznamu přidruženého ke službám hodnotami komunity protokolu BGP
  * Vytvořit pravidlo povolující seznam předpon odpovídající tyto hodnoty komunity protokolu BGP

* Je nutné připojit filtr tras k okruhu ExpressRoute.

## <a name="before-you-begin"></a>Než začnete

Než začnete, nainstalujte si nejnovější verzi příkazů rozhraní příkazového řádku (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématech [Instalace Azure CLI](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).

* Zkontrolujte [požadavky](expressroute-prerequisites.md) a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.

* Musí mít aktivní okruh ExpressRoute. Než budete pokračovat, podle pokynů [vytvořte okruh ExpressRoute](howto-circuit-cli.md) a mějte ho povolený vaším poskytovatelem připojení. Okruh ExpressRoute musí být ve stavu zřízený a povolený.

* Musíte mít active partnerský vztah Microsoftu. Postupujte podle pokynů na adrese [vytvoření a změny konfigurace partnerského vztahu](howto-routing-cli.md)

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Přihlaste se ke svému účtu Azure a vyberte své předplatné

Můžete začít s vaší konfigurací, přihlaste se ke svému účtu Azure. Pokud používáte "vyzkoušet", automaticky přihlášeni a můžete přeskočit krok přihlášení. Připojení vám usnadní použijte následující příklady:

```azurecli
az login
```

Zkontrolujte předplatná pro příslušný účet.

```azurecli-interactive
az account list
```

Vyberte předplatné, pro kterou chcete vytvořit okruh ExpressRoute.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="prefixes"></a>Krok 1: Získání seznamu předpon a hodnotami komunity protokolu BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. získání seznamu hodnot komunity protokolu BGP

Chcete-li získat seznam hodnot komunity protokolu BGP přidružené služby přístupné prostřednictvím partnerského vztahu Microsoftu a seznam předpon, které jsou k nim má přiřazené použijte následující rutinu:

```azurecli-interactive
az network route-filter rule list-service-communities
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Vytvořte seznam hodnot, které chcete použít.

Zkontrolujte seznam hodnotami komunity protokolu BGP, které chcete použít ve filtru tras.

## <a name="filter"></a>Krok 2: Vytvoření filtru tras a pravidlo filtru

Filtr tras může mít jenom jedno pravidlo a pravidlo musí být typu "Povolit". Toto pravidlo může mít seznam hodnot komunity protokolu BGP s ním spojená.

### <a name="1-create-a-route-filter"></a>1. Vytvoření filtru tras

Nejprve vytvořte filtr tras. Příkaz `az network route-filter create` pouze vytvořit prostředek filtru tras. Po vytvoření prostředku, musí pak vytvořte pravidlo a připojení k objektu filtru trasy. Spuštěním následujícího příkazu vytvořte prostředek filtr trasy:

```azurecli-interactive
az network route-filter create -n MyRouteFilter -g MyResourceGroup
```

### <a name="2-create-a-filter-rule"></a>2. vytvoření pravidla filtru

Spuštěním následujícího příkazu vytvořte nové pravidlo:
 
```azurecli-interactive
az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
```

## <a name="attach"></a>Krok 3: Připojení filtr tras k okruhu ExpressRoute

Spusťte následující příkaz připojit filtr tras k okruhu ExpressRoute:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="tasks"></a>Běžné úlohy

### <a name="getproperties"></a>Chcete-li získat vlastnosti filtr tras

Pokud chcete získat vlastnosti filtru tras, použijte následující příkaz:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="updateproperties"></a>Aktualizovat vlastnosti filtr tras

Pokud se filtr tras je již připojen k okruhu, aktualizace seznamu komunity protokolu BGP automaticky šířící změny oznámení o inzerovaném programu odpovídající předpona prostřednictvím zavedených relací protokolu BGP. Můžete aktualizovat seznam komunity protokolu BGP vašeho filtru tras pomocí následujícího příkazu:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="detach"></a>Chcete-li odpojit filtr tras z okruhu ExpressRoute

Jakmile se filtr tras se odpojit od okruhu ExpressRoute, jsou bez předpony inzerované prostřednictvím relace protokolu BGP. Filtr tras z okruhu ExpressRoute pomocí následujícího příkazu můžete odpojit:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

### <a name="delete"></a>Odstranit filtr tras

Pokud není připojen k žádné okruhu můžete ho jenom odstranit filtr tras. Ujistěte se, že filtr tras není připojen k žádné okruh před pokusem o jeho odstranění. Můžete odstranit filtr tras pomocí následujícího příkazu:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
