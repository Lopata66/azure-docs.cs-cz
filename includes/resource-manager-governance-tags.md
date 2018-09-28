---
title: zahrnout soubor
description: zahrnout soubor
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/13/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 2be7e4d2f3697649df669a4f20ba4db62c1fc486
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47401639"
---
Použití značek k prostředkům Azure poskytuje metadata pro jejich logické uspořádání do taxonomie. Každá značka se skládá z názvu a hodnoty pár. Můžete například použít název Prostředí a hodnotu Produkční na všechny prostředky v produkčním prostředí.

Po použití značek můžete načíst všechny prostředky v předplatném s daným názvem a hodnotou značky. Značky umožňují načíst související prostředky z různých skupin prostředků. Tento přístup je užitečný v případě, že potřebujete uspořádat prostředky pro účely fakturace nebo správy.

Taxonomii zvažte metadat samoobslužné označování strategie kromě strategie automatického označení snižují zátěž kladenou na uživatele a zvýšit přesnost.

Na značky se vztahují následující omezení:

* Každý prostředek nebo skupina prostředků může mít maximálně 15 párů název/hodnota značek. Toto omezení se vztahuje pouze na značky použité přímo na prostředek nebo skupinu prostředků. Skupina prostředků může obsahovat řadu prostředků, z nichž každý má 15 párů název/hodnota značek. Pokud k prostředku potřebujete přidružit více než 15 hodnot, použijte jako hodnotu značky řetězec JSON. Řetězec JSON může obsahovat mnoho hodnot, které se použijí pro jeden název značky. Tento článek ukazuje příklad přiřazení řetězce JSON ke značce.
* Název značky je omezen na 512 znaků a hodnota značky je omezena na 256 znaků. Pro účty úložiště je název značky omezen na 128 znaků a hodnota značky je omezena na 256 znaků.
* Virtuální počítače jsou omezené na celkem 2 048 znaků pro všechny názvy a hodnoty značek.
* Prostředky ve skupině prostředků nedědí značky použité na danou skupinu prostředků.
* Značky nelze použít pro klasické prostředky, jako jsou cloudové služby.
* Názvy značek nemůže obsahovat tyto znaky: `<`, `>`, `%`, `&`, `\`, `?`, `/`
