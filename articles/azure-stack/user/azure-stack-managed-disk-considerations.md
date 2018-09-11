---
title: Rozdíly a aspekty u služby Managed Disks ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o rozdíly a aspekty při práci se službou Managed Disks v Azure stacku.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 26f8880d01da00780317ee2a6f66ee5007576a50
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347660"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Managed Disks zásobníku: Rozdíly a aspekty
Tento článek shrnuje známé rozdíly mezi Azure Stack Managed Disks a Managed Disks pro Azure. Další informace o základní rozdíly mezi Azure Stack a Azure, najdete v článku [klíče aspekty](azure-stack-considerations.md) článku.

Spravované disky zjednodušují správu disků pro virtuální počítače IaaS pomocí správy [účty úložiště](/azure/azure-stack/azure-stack-manage-storage-accounts) přidružené k diskům virtuálních počítačů.
  

## <a name="cheat-sheet-managed-disk-differences"></a>Tahák: spravovaného disku rozdíly

| Funkce | Azure (globální) | Azure Stack |
| --- | --- | --- |
|Šifrování pro neaktivní uložená Data |Šifrování služby Azure Storage (SSE), Azure Disk Encryption (ADE)     |Šifrování AES 128-bit nástroje BitLocker      |
|Image          | Podpora spravovanou vlastní image |Není dosud podporován.|
|Možnosti zálohování |Podpora služby Azure Backup |Není dosud podporován. |
|Možnosti zotavení po havárii |Podpora Azure Site Recovery |Není dosud podporován.|
|Typy disků     |Premium SSD, SSD na úrovni Standard (Preview) a Standard pevný disk |Premium SSD, Standard HDD |
|Disky Premium  |Plně podporované. |Je možné zřídit, ale bez omezení výkonu nebo záruk  |
|Prémiové disky vstupně-výstupních operací  |Závisí na velikosti disku  |2300 IOPs na disk |
|Propustnost disků úrovně Premium |Závisí na velikosti disku |145 MB za sekundu na disk |
|Maximální velikost disku  |4 TB       |1 TB       |
|Výkon disků analýzy |Agregace metrik a metriky pro disk podporované |Není dosud podporován. |
|Migrace      |Poskytuje nástroj pro migraci z existující nespravované správce prostředků virtuálních počítačů Azure bez nutnosti znovu vytvořte virtuální počítač  |Není dosud podporován. |


## <a name="metrics"></a>Metriky
Existují také rozdíly pomocí metrik storage:
- Pomocí služby Azure Stack nerozlišují data transakcí v metrikách storage šířky pásma sítě interní nebo externí.
- Azure Stack data transakcí v metrikách storage neobsahuje virtuální počítač přístup k připojené disky.


## <a name="api-versions"></a>Verze rozhraní API
Služba Azure Managed Disks zásobníku podporuje následující verze rozhraní API:
- 2017-03-30


## <a name="next-steps"></a>Další postup
[Další informace o virtuálních počítačích Azure Stack](azure-stack-compute-overview.md)
