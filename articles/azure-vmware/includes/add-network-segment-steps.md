---
title: Přidat segment sítě NSX-T
description: Postup přidání síťového segmentu NSX-T pro řešení Azure VMware.
ms.topic: include
ms.date: 11/09/2020
ms.openlocfilehash: 5b97f0b280fa12eff39c9601bb73e439dba8e9fd
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335043"
---
<!-- Used in manage-dhcp.md and tutorial-nsx-t-network-segment.md -->

1. Ve Správci NSX-T vyberte **Networking**  >  **segmenty** sítě a pak vyberte **Přidat segment**. 

   :::image type="content" source="../media/nsxt/nsxt-segments-overview.png" alt-text="Snímek obrazovky ukazující, jak přidat nový segment":::

1. Vyberte **Přidat segment** a zadejte název segmentu.

1. Vyberte bránu Tier1 (TNTxx-T1) jako **připojenou bránu** a ponechte **typ** jako flexibilní.

1. Vyberte předem nakonfigurovanou zónu překrývající **přenos** (TNTXX-PŘEKRYVNÉ – TZ) a pak vyberte **nastavit podsítě**. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-specs.png" alt-text="Nastavte název segmentu, připojenou bránu a typ a přenosovou zónu a pak vyberte nastavit podsíť.":::

1. Zadejte IP adresu brány a pak vyberte **Přidat**. 

   >[!IMPORTANT]
   >IP adresa musí být na překrývající se RFC1918 adres, která zajišťuje připojení k virtuálním počítačům v novém segmentu.

   :::image type="content" source="../media/nsxt/nsxt-create-segment-gateway.png" alt-text="Nastavte IP adresu brány pro nový segment a pak vyberte Přidat.":::

1. Vyberte **použít** a pak **Uložit**.

1. Vyberte **ne** , pokud chcete, aby možnost pokračovala v konfiguraci segmentu. 

   :::image type="content" source="../media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Kliknutím na ne můžete odmítnout další konfiguraci nově vytvořeného segmentu sítě.":::

1. Potvrďte přítomnost nového segmentu sítě. V tomto příkladu je **LS01** nový segment sítě.

   1. Ve Správci NSX-T vyberte **Networking**  >  **segmenty** sítě. 

      :::image type="content" source="../media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Ověřte, že se nový segment sítě nachází v NSX-T.":::

   1. V vCenter vyberte **síť > SDDC-Datacenter**.

      :::image type="content" source="../media/nsxt/vcenter-with-ls01-2.png" alt-text="Potvrďte, že se nový segment sítě nachází v vCenter.":::