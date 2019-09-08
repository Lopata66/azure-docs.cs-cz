---
title: Řešení Azure VMware podle CloudSimple – přidělení veřejných IP adres
description: Popisuje, jak přidělit veřejné IP adresy pro virtuální počítače v prostředí privátního cloudu.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773799"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Přidělit veřejné IP adresy pro prostředí privátního cloudu

Otevřete kartu veřejné IP adresy na stránce síť a přidělte veřejné IP adresy pro virtuální počítače ve vašem prostředí privátního cloudu.

1. [Přejděte na portál CloudSimple](access-cloudsimple-portal.md) a v postranní nabídce vyberte **síť** .
2. Vyberte **veřejné IP adresy**.
3. Klikněte na **nová veřejná IP adresa**.

    ![Stránka veřejné IP adresy](media/public-ips-page.png)

4. Zadejte název pro identifikaci položky IP adresy.
5. Ponechte výchozí umístění.
6. V případě potřeby změňte časový limit nečinnosti pomocí posuvníku.
7. Zadejte místní IP adresu, pro kterou chcete přiřadit veřejnou IP adresu.
8. Zadejte přidružený název DNS.
9. Klikněte na **Submit** (Odeslat).

![Přidělit veřejné IP adresy](media/network-public-ip-allocate.png)

Spustí se úkol přidělení veřejné IP adresy. Stav úlohy můžete zjistit na stránce **aktivity > úlohy** . Po dokončení přidělení se nová položka zobrazí na stránce veřejné IP adresy.
