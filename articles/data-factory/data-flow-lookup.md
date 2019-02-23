---
title: Azure Data Factory mapování transformace vyhledávání toku dat
description: Azure Data Factory mapování transformace vyhledávání toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b156917a9987b023a9bf94e51c0cc14aebb133c7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738381"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory mapování transformace vyhledávání toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Přidat odkaz na data z jiného zdroje do toku dat pomocí vyhledávání. Transformace vyhledávání vyžaduje definovaný zdroj, který odkazuje na referenční tabulku a odpovídá na klíčové pole.

![Transformace vyhledávání](media/data-flow/lookup1.png "vyhledávání")

Vyberte klíče, které chcete porovnává příchozí datový proud pole a pole ze zdroje odkazu na pole. Musíte nejprve vytvoříte nový zdroj na plátně návrhu toku dat použít jako pravá strana pro vyhledávání.

Když shoda nenajde, výsledné řádky a sloupce ze zdrojového odkazu se přidají do vašeho datového toku. Můžete zvolit pole, která zájmu, které chcete zahrnout do na konci toku dat pro jímku.
