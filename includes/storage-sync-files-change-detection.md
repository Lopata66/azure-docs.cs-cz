---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: beb08c29587e4ce522131142fd61925b5af45fa9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114577"
---
Změny provedené na sdílenou složku Azure pomocí webu Azure portal nebo podepisování SMB jsou bezprostředně zjištěna a replikovat jako změny koncový bod serveru. Služba soubory Azure ještě nemá oznámení o změnách nebo záznamu do deníku, takže neexistuje žádný způsob, jak automaticky zahájí relace synchronizace, pokud se změnily soubory. Ve Windows serveru, využívá Azure File Sync [záznamu do deníku Windows USN](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) automaticky zahájení relace synchronizace, když se změní soubory.<br /><br /> Ke zjištění změn sdílenou složku Azure, Azure File Sync má plánovanou úlohu nazvanou *změnit úlohy zjišťování*. Úloha zjišťování změn zobrazí každý soubor ve sdílené složce a porovná ho s verzí synchronizace pro tento soubor. Když úloha zjišťování změn zjistí, že soubory se změnily, Azure File Sync spustí relaci synchronizace. Každých 24 hodin se spustí úloha zjišťování změn. Protože úloha zjišťování změn funguje tak, že výčet každý soubor ve sdílené složce Azure file, detekce změn trvá déle, v větší oborech názvů než v menších obory názvů. Pro velké obory názvů může trvat déle než jednou za 24 hodin k určení, které soubory se změnily.<br /><br />
Poznámka: provedené změny sdílenými složkami Azure pomocí rozhraní REST nemá nikoli aktualizaci, čas poslední změny SMB a se projeví jako změnu synchronizace. <br /><br />
Jsme se s touto přidávání detekce změn pro sdílené složky Azure podobný USN pro svazky ve Windows serveru. Nám pomohou určit prioritu tuto funkci pro budoucí vývoj hlasováním ji do [UserVoice soubory Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
