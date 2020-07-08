---
title: zahrnout soubor
titleSuffix: Azure
description: zahrnout soubor
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 000971878e24c46892aaef1fa0c65237a4219883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "81678531"
---
Po úspěšném nasazení **partnerského vztahu** se můžete pomocí následujících kroků podívat na tento prostředek.

1. V části **skupiny prostředků**vyberte skupinu prostředků, kterou jste vybrali při vytváření prostředku **partnerského vztahu** . Pokud máte příliš mnoho skupin prostředků, použijte pole **filtru** .

    > [!div class="mx-imgBorder"]
    > ![Skupiny prostředků](../media/setup-direct-get-resourcegroup.png)

1. Vyberte prostředek **partnerského vztahu** , který jste vytvořili.

    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředků partnerského vztahu](../media/setup-direct-get-open.png)

1. Stránka **Přehled** zobrazuje informace vysoké úrovně, jak je znázorněno zde.

    > [!div class="mx-imgBorder"]
    > ![Podokno s přehledem partnerského vztahu prostředků](../media/setup-exchange-get-overview.png)

1. Na levé straně vyberte **informace o ASN** pro zobrazení informací, které jste odeslali při vytváření PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![Informace o partnerském vztahu prostředků ASN](../media/setup-direct-get-asninfo.png)

1. Na levé straně vyberte **připojení**. V horní části obrazovky se zobrazí souhrn partnerských připojení mezi číslem ASN a společností Microsoft v různých zařízeních ve službě Metro. K souhrnu připojení můžete také získat přístup ze stránky **Přehled** výběrem možnosti **připojení** v prostředním podokně, jak je znázorněno na obrázku.

    > [!div class="mx-imgBorder"]
    > ![Připojení prostředků partnerského vztahu](../media/setup-exchange-get-connectionssummary.png)

    * **Stav připojení** odpovídá stavu nastavení připojení partnerských vztahů. Stavy zobrazené v tomto poli následují po diagramu stavu zobrazeném v [Průvodci vytvořením partnerského vztahu Exchange](../walkthrough-exchange-all.md).
    * Stav **relace protokolu IPv4** a **stav relace protokolu IPv6** odpovídají stavům relace protokolu BGP IPv4 a IPv6 (v uvedeném pořadí).  
    * Když vyberete řádek v horní části obrazovky, v části **připojení** v dolní části se zobrazí podrobnosti o každém připojení. Vyberte šipky a rozbalte položku **Konfigurace**, **adresa IPv4**a **adresa IPv6**.

    > [!div class="mx-imgBorder"]
    > ![Zobrazení prostředků partnerského vztahu](../media/setup-exchange-get-connectionsipv4.png)
