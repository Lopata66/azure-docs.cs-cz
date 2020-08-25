---
title: 'Kurz: Vytvoření segmentu sítě NSX-T v řešení Azure VMware'
description: V tomto kurzu jste vytvořili segmenty sítě NSX-T, které se používají pro virtuální počítače v vCenter.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: cee65211cbef25ec029c68888bc8e6059f7c7896
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750461"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>Kurz: Vytvoření segmentu sítě NSX-T v řešení Azure VMware

Segmenty sítě vytvořené v NSX-T Manageru se používají jako sítě pro virtuální počítače v vCenter. Virtuální počítače vytvořené v vCenter se umístí do segmentů sítě vytvořených v NSX-T a zobrazují se v vCenter.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Pokud chcete přidat segmenty sítě, přejděte do správce NSX-T.
> * Přidat nový segment sítě
> * Sledovat nový segment sítě v vCenter

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu se vyžaduje privátní cloud řešení Azure VMware s přístupem k rozhraním pro správu vCenter a NSX-T. Podívejte se na [kurz: konfigurace sítě pro privátní cloud VMware v Azure](tutorial-configure-networking.md).

## <a name="provision-a-network-segment-in-nsx-t"></a>Zřízení segmentu sítě v NSX-T

1. V vCenter pro váš privátní cloud vyberte **SDDC-Datacenter > sítě** a Všimněte si, že ještě neexistují žádné sítě.

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="V vCenter pro váš privátní cloud vyberte SDDC-Datacenter > sítě a Všimněte si, že ještě neexistují žádné sítě.":::

1. V nástroji NSX-T Manager pro váš privátní cloud vyberte **sítě**.

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="V nástroji NSX-T Manager pro váš privátní cloud vyberte sítě.":::

1. Vyberte **segmenty**.

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="Na stránce Přehled sítě vyberte segmenty.":::

1. Na stránce Přehled segmentů NSX-T vyberte **Přidat segment**. Tři segmenty se vytvářejí v rámci zřizování privátního cloudu a nejde je použít pro virtuální počítače.  Pro tento účel budete muset přidat nový segment sítě.

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="Na stránce Přehled segmentů NSX-T vyberte Přidat SEGMENT.":::

1. Pojmenujte segment, zvolte předem nakonfigurovanou bránu Tier1 (TNTxx-T1) jako **připojenou bránu**, ponechte **typ** jako flexibilní, zvolte předem nakonfigurovanou **zónu** překrývající přenos (TNTxx – překryvný – TZ) a pak vyberte nastavit podsítě. Všechna ostatní nastavení v této části a profily **portů** a **segmentů** můžou zůstat ve výchozím nastavení, jako je konfigurace.

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="Nastavte název segmentu, připojenou bránu a typ a přenosovou zónu a pak vyberte nastavit podsíť.":::

1. Nastavte IP adresu brány pro nový segment a pak vyberte **Přidat**. IP adresa, kterou použijete, musí být na překrývající se RFC1918 adres, což zajistí, že se můžete připojit k virtuálním počítačům v novém segmentu.

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="Nastavte IP adresu brány pro nový segment a pak vyberte Přidat.":::

1. Použijte nový segment sítě tak, že vyberete **použít** a pak konfiguraci uložíte pomocí **Uložit**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="Použijte nový segment sítě na konfiguraci NSX-T s použitím použít.":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="Uložte nový segment sítě do konfigurace NSX-T pomocí SAVE.":::

1. Nyní byl vytvořen nový segment sítě a odmítnete možnost pokračovat v konfiguraci segmentu výběrem možnosti **ne**.

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="Kliknutím na ne můžete odmítnout další konfiguraci nově vytvořeného segmentu sítě.":::

1. Potvrďte, že se nový segment sítě nachází v NSX-T, a to tak, že vyberete **sítě > segmenty** a uvidíte nový segment v seznamu (v tomto případě "LS01").

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="Ověřte, že se nový segment sítě nachází v NSX-T.":::

1. Potvrďte, že se nový segment sítě nachází v vCenter, a to tak, že vyberete **sítě > SDDC-datacentra** a v seznamu budete pozorovat nový segment (v tomto případě "LS01").

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="Potvrďte, že se nový segment sítě nachází v vCenter.":::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili segmenty sítě NSX-T, které se používají pro virtuální počítače v vCenter. Teď můžete použít [kurz: Vytvoření knihovny obsahu pro nasazení virtuálních počítačů v řešení Azure VMware](tutorial-deploy-vm-content-library.md) pro vytvoření knihovny obsahu v vCenter a zřízení virtuálního počítače v síti, kterou jste vytvořili v tomto kurzu.

<!-- LINKS - external-->

<!-- LINKS - internal -->
