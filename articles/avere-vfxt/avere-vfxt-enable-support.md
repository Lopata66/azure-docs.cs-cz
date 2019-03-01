---
title: Povolení podpory pro Avere vFXT – Azure
description: Jak povolit podporu ukládání z Avere vFXT pro Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: fe096b2e2a75cc89e3ce5ef905d8e4c347cc153a
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992460"
---
# <a name="enable-support-uploads"></a>Povolení nahrávání podpory

Data podpory o clusteru můžete nahrát automaticky vFXT Avere pro Azure. Tyto nahrávání nechat pracovníci podpory poskytují nejlepší možné péče.

## <a name="steps-to-enable-uploads"></a>Postup povolení nahrávání

Následujícím postupem z ovládacího panelu Avere k aktivaci podpory. (Čtení [přístup ke clusteru vFXT](avere-vfxt-cluster-gui.md) se naučíte, chcete-li otevřít ovládací Panel Avere.)

1. Přejděte **nastavení** kartě v horní části.
1. Klikněte na tlačítko **podporu** odkaz na levé straně a přijměte zásady ochrany osobních údajů.

   ![Snímek obrazovky zobrazující Avere ovládací panely a automaticky otevírané okno s tlačítkem pro potvrzení přijímat zásady ochrany osobních údajů](media/avere-vfxt-privacy-policy.png)

1. Klikněte na trojúhelník nalevo od **informace o zákaznících** rozbalte v části.
1. Klikněte na tlačítko **Revalidate odesílat informace** tlačítko.
1. Nastavení podpory názvu clusteru v **jedinečný název clusteru** – Ujistěte se, že ho jednoznačně identifikuje clusteru pracovníci podpory.
1. Zaškrtněte políčka pro **statistiky monitorování**, **obecné informace o nahrání**, a **havárií nahrát informace**.
1. Klikněte na **Submit** (Odeslat).

   ![Snímek obrazovky, který obsahuje oddíl informací o dokončené Zákaznická podpora nastavení stránky](media/avere-vfxt-support-info.png)

1. Klikněte na trojúhelník nalevo od **zabezpečení proaktivní podpory (SPS)** rozbalte v části.
1. Zaškrtněte políčko u **odkaz povolit aktualizace Service PACKU**.
1. Klikněte na **Submit** (Odeslat).

   ![Snímek obrazovky obsahující dokončené zabezpečení proaktivní podporují oddíl na stránce nastavení podpory](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Další postup

Pokud je potřeba přidat místní nebo existující systém úložiště do clusteru v cloudu, postupujte podle pokynů v [konfigurace úložiště](avere-vfxt-add-storage.md). 

Pokud jste připravení začít s připojení klientů ke clusteru, přečtěte si [připojení clusteru vFXT Avere](avere-vfxt-mount-clients.md).