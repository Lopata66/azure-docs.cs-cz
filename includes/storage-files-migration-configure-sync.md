---
title: Konfigurace Azure File Sync
description: Nakonfigurujte Azure File Sync. Společný textový blok sdílený v rámci migračních dokumentů.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 64b99976a306c3c8423f5115c95a15158a3ddb51
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043177"
---
Tento krok spojuje všechny prostředky a složky, které jste v instanci Windows serveru nastavili během předchozích kroků.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyhledejte prostředek služby synchronizace úložiště.
1. Vytvořte novou *skupinu synchronizace* v rámci prostředku služby synchronizace úložiště pro každou sdílenou složku Azure. V Azure File Sync terminologii se sdílená složka Azure stane *koncovým bodem cloudu* v topologii synchronizace, který budete popisovat pomocí vytvoření skupiny synchronizace. Když vytváříte skupinu synchronizace, poskytněte jí známý název, který vám umožní rozpoznat, kterou sadu souborů se synchronizuje. Ujistěte se, že se na sdílenou složku Azure odkazuje pomocí odpovídajícího názvu.
1. Po vytvoření skupiny synchronizace se v seznamu skupin synchronizace zobrazí řádek. Vyberte název (odkaz), chcete-li zobrazit obsah skupiny synchronizace. Sdílená složka Azure se zobrazí v části **koncové body cloudu** .
1. Vyhledejte tlačítko **+ Přidat server koncový bod** . Složka na místním serveru, kterou jste zřídili, se stane cestou k tomuto *koncovému bodu serveru* .
