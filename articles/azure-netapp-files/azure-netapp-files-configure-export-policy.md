---
title: Konfigurace zásad export pro svazek systému souborů NFS pomocí souborů NetApp Azure | Dokumentace Microsoftu
description: Popisuje postup konfigurace export zásad pro řízení přístupu na použití služby soubory Azure NetApp svazek systému souborů NFS
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: b-juche
ms.openlocfilehash: 8cda5921a1aec86d28beabbd9cea5b07a203a0e8
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401486"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurace zásad export pro svazek systému souborů NFS

Můžete volitelně nakonfigurovat zásady exportu pro řízení přístupu ke svazku Azure NetApp Files. Export zásad je podpora pouze pro svazky systému souborů NFS. 

## <a name="steps"></a>Kroky 

1.  Klikněte na okno **Vytvořit zásady exportu** z okna Správa svazku. 

2.  Zadejte do následujících polí informace pro vytvoření pravidla zásad exportu:   
    *  **Index**   
        Určete číslo indexu pro pravidlo.  
        Zásady exportu můžou mít maximálně pět pravidel. Pravidla se vyhodnocují podle jejich pořadí v seznamu čísel indexu. Pravidla s nižším číslem indexu se vyhodnocují jako první. Například pravidlo s číslem indexu 1 je vyhodnoceno před pravidlem s číslem indexu 2. 

    * **Povolení klienti**   
        Zadejte hodnotu v jednom z následujících formátů:  
        * IPv4 adresa, například `10.1.12.24` 
        * IPv4 adresa s maskou podsítě, vyjádřená jako počet bitů, například `10.1.12.10/4`

    * **Přístup**  
        Vyberte jeden z následujících typů přístupu:  
        * Zakázaný přístup 
        * Čtení a zápis
        * Jen pro čtení

    * **Protokoly**   
        Zadejte protokol, který se použije pro zásadu exportu.   
        Export zásad souborů NetApp Azure v současné době podporuje pouze NFSv3.

    ![Zásada exportu](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Další postup 
* [Správa svazků](azure-netapp-files-manage-volumes.md)
* [Připojování nebo odpojování svazku pro virtuální počítače](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Správa snímků](azure-netapp-files-manage-snapshots.md)
