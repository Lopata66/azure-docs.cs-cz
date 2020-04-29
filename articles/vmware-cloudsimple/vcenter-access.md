---
title: Řešení Azure VMware podle CloudSimple – přístup ke klientovi vSphere
description: Popisuje, jak přistupovat ke vCenter vašeho privátního cloudu.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77022660"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Přístup k portálu pro vCenter privátního cloudu

Svůj privátní cloudový portál vCenter můžete spustit z portálu Azure Portal nebo CloudSimple.  portál vCenter umožňuje správu infrastruktury VMware v privátním cloudu.

## <a name="before-you-begin"></a>Před zahájením

Aby bylo možné získat přístup k portálu vCenter, musí být navázáno síťové připojení a musí být povoleno rozlišení názvů DNS.  Síťové připojení k privátnímu cloudu můžete vytvořit pomocí kterékoli z následujících možností.

* [Připojení z místního prostředí k CloudSimple pomocí ExpressRoute](on-premises-connection.md)
* [Konfigurace připojení VPN k privátnímu cloudu CloudSimple](set-up-vpn.md)

Pokud chcete nastavit překlad názvů DNS vaší komponenty infrastruktury privátního cloudu, přečtěte si téma [Konfigurace DNS pro překlad adres IP pro privátní cloud přístup vCenter z místních pracovních stanic](on-premises-dns-setup.md) .

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Přístup k vCenter z Azure Portal

Z Azure Portal můžete spustit portál vCenter z vašeho privátního cloudu.

1. Vyberte **Všechny služby**.

2. Vyhledejte **služby CloudSimple Services**.

3. Vyberte službu CloudSimple vašeho privátního cloudu, ke kterému se chcete připojit.

4. Na stránce **Přehled** klikněte na **Zobrazit privátní cloudy VMware.**

    ![Přehled služby CloudSimple](media/cloudsimple-service-overview.png)

5. V seznamu privátních cloudů vyberte privátní cloud a klikněte na **Spustit klienta vSphere**.

    ![Spustit klienta vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Přístup k vCenter z portálu CloudSimple

Portál vCenter vašeho privátního cloudu můžete spustit z portálu CloudSimple.

1. Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

2. Z **prostředků** vyberte privátní cloud, ke kterému chcete získat přístup, a klikněte na **Spustit klienta vSphere**.

    ![Spustit klienta vSphere – prostředky](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Portál vCenter můžete také spustit ze souhrnné obrazovky vašeho privátního cloudu.

    ![Spustit klienta vSphere – souhrn](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Další kroky

* [Vytváření a správa sítí VLAN a podsítí pro privátní cloudy](create-vlan-subnet.md)
* [Model oprávnění privátního cloudu CloudSimple VMware vCenter](learn-private-cloud-permissions.md)