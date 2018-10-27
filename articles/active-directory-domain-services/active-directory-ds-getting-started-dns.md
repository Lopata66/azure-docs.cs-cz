---
title: 'Azure Active Directory Domain Services: Aktualizace nastavení DNS pro virtuální síť Azure | Dokumentace Microsoftu'
description: Začínáme se službou Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: d4f3e82c-6807-4690-b298-4eabad2b7927
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: 7d2902c997259fc115a1f204f123983038821887
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157339"
---
# <a name="enable-azure-active-directory-domain-services"></a>Povolení služby Azure Active Directory Domain Services

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Úloha 4: Aktualizace nastavení DNS pro virtuální síť Azure
V předchozích úlohách konfigurace jste úspěšně povolili službu Azure Active Directory Domain Services pro svůj adresář. Dále umožníte počítačům v této virtuální síti, aby se připojily a využívaly tyto služby. V tomto článku provedete aktualizaci nastavení serveru DNS svojí virtuální sítě tak, aby odkazoval na dvě IP adresy, kde je ve virtuální síti dostupná služba Azure Active Directory Domain Services.

Následujícím postupem aktualizujte nastavení serveru DNS virtuální sítě, ve které jste povolili službu Azure Active Directory Domain Services:


1. Na kartě **Přehled** je uveden seznam **požadovaných kroků konfigurace**, které je potřeba provést po úplném zřízení spravované domény. Prvním konfiguračním krokem je **aktualizace nastavení serveru DNS pro virtuální síť**.

    ![Doménové služby – karta Přehled](./media/getting-started/domain-services-provisioned-overview.png)

    > [!TIP]
    > Nevidíte tento krok konfigurace? Pokud jsou nastavení serveru DNS virtuální sítě aktuální, neuvidíte na kartě Přehled dlaždici Aktualizovat nastavení serveru DNS pro virtuální síť.
    >
    >

2. Kliknutím na tlačítko **Konfigurovat** můžete nastavení serveru DNS virtuální sítě aktualizovat.

> [!NOTE]
> Virtuální počítače v síti získají nové nastavení DNS až po restartování. Pokud chcete, aby aktualizované nastavení DNS získaly hned, aktivujte restartování z portálu, pomocí PowerShellu nebo pomocí rozhraní příkazového řádku.
>
>

## <a name="next-step"></a>Další krok
[Úloha 5: Povolení synchronizace hodnot hash hesel do služby Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)
