---
title: Jak monitorovat Azure Data Share
description: Naučte se monitorovat stav pozvánky, sdílet odběry a historii snímků ve službě Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a3d13919a54e170d410abba5da66172c2029d9b5
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636270"
---
# <a name="monitor-azure-data-share"></a>Monitorování služby Azure Data Share  

Tento článek vysvětluje, jak můžete monitorovat sdílené složky dat pomocí Azure Data Share. Jako poskytovatel dat můžete monitorovat různé aspekty vašich relací sdílení dat. Podrobnosti o tom, jestli vaši příjemci dat přijali vaše pozvání ke sdílení dat, a taky to, jestli si vytvořili předplatné pro sdílení a začali používat vaše data, jsou k dispozici pro monitorování. 

Jako příjemce dat můžete monitorovat snímky, které se aktivovaly v rámci vašeho předplatného Azure. 

## <a name="monitor-invitation-status"></a>Stav pozvánky monitorování

Přejděte na odeslané sdílené složky – > pozvánky, zobrazte stav pozvání ke sdílení dat. 

![Stav pozvánky](./media/invitation-status.png "Stav pozvánky") 

Existují tři stavy, ve kterých může být vaše Pozvánka:

* Čeká na dokončení – příjemce sdílené složky tuto pozvánku ještě nepřijal.
* Přijatý – příjemce sdílení dat přijal pozvánku.
* Odmítnuto – příjemce sdílené složky odmítl pozvání.

> [!IMPORTANT]
> Pokud pozvánku odstraníte poté, co byla přijata, není ekvivalentem odvolat přístup. Pokud chcete zastavit budoucí snímky z kopírování do účtu úložiště dat pro uživatele, musíte odvolat přístup prostřednictvím karty *sdílení předplatných* . 

## <a name="monitor-share-subscriptions"></a>Monitorování předplatných sdílení

Projděte si stav vašich předplatných sdílení tak, že přejdete na odeslané sdílené složky – > sdílet předplatná. Tím získáte podrobnosti o aktivních předplatných vytvořených vašimi příjemci dat po přijetí pozvánky. Budoucí aktualizace svého příjemce dat můžete zastavit výběrem položky sdílet předplatné a výběrem možnosti *odvolat*. 

## <a name="snapshot-history"></a>Historie snímků 

Na kartě **Historie** sdílené složky můžete zobrazit, kdy se data zkopírují z poskytovatele dat do úložiště dat příjemce dat. Můžete monitorovat četnost, dobu trvání a stav každého snímku. 

![Historie snímků](./media/sent-shares.png "Historie snímků") 

Další podrobnosti o každém spuštění snímku můžete zobrazit kliknutím na datum zahájení spuštění. Pak kliknutím na stav pro každou datovou sadu zobrazíte množství přenesených dat, počet kopírovaných souborů nebo záznamů, dobu trvání snímku, počet použitých virtuální jádra a chybové zprávy, pokud existuje. 

Zobrazí se až 30 dní historie snímků. Pokud potřebujete uložit a Zobrazit více než 30 dní pro historii, můžete využít nastavení diagnostiky.

## <a name="diagnostic-setting"></a>Nastavení diagnostiky

Nastavení diagnostiky můžete nakonfigurovat tak, aby ušetřilo data protokolu nebo události. Přejděte na monitorování – nastavení diagnostiky > a vyberte **Přidat nastavení diagnostiky**. Vyberte data protokolu nebo události, které vás zajímají, a kam je chcete uložit nebo odeslat. 

![Historie snímků](./media/diagnostic-settings.png "Nastavení diagnostiky") 

## <a name="next-steps"></a>Další kroky 

Další informace o [terminologii Azure Data Share](terminology.md)