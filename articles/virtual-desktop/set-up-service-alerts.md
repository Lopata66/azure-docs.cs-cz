---
title: Nastavení upozornění služby pro virtuální počítače s Windows – Azure
description: Jak nastavit Azure Service Health pro příjem oznámení služby pro virtuální počítač s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2834ba924fa9c29d955c38fbaeb45ab23e5c4e9b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79127712"
---
# <a name="tutorial-set-up-service-alerts"></a>Kurz: nastavení výstrah služby

Pomocí Azure Service Health můžete monitorovat problémy služby a Poradce pro stav pro virtuální počítače s Windows. Azure Service Health vás může informovat s různými typy výstrah (například e-mailem nebo SMS), pomůže vám pochopit dopad problému a průběžně ho aktualizovat, jakmile se problém vyřeší. Azure Service Health vám taky může pomáhat zmírnit výpadky a připravit se na plánovanou údržbu a změny, které by mohly ovlivnit dostupnost vašich prostředků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte a nakonfigurujte výstrahy služby.

Další informace o Azure Service Health najdete v dokumentaci ke [službě Azure Health](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Požadavky

- [Kurz: vytvoření tenanta ve virtuální ploše Windows](tenant-setup-azure-active-directory.md)
- [Kurz: vytvoření instančních objektů a přiřazení rolí pomocí PowerShellu](create-service-principal-role-powershell.md)
- [Kurz: Vytvoření fondu hostitelů pomocí Azure Marketplace](create-host-pools-azure-marketplace.md)

## <a name="create-service-alerts"></a>Vytvoření výstrah služby

V této části se dozvíte, jak nakonfigurovat Azure Service Health a jak nastavit oznámení, ke kterým můžete přistupovat v Azure Portal. Můžete nastavit různé typy výstrah a naplánovat jejich včasné oznámení.

### <a name="recommended-service-alerts"></a>Doporučené výstrahy služby

Doporučujeme vytvořit výstrahy služby pro následující typy událostí stavu:

- **Problém se službou:** Dostávat oznámení o podstatných problémech, které mají vliv na připojení vašich uživatelů ke službě, nebo s možností spravovat tenanta virtuálních klientů ve Windows.
- **Poradenský Poradce pro stav:** Dostanou oznámení, která vyžadují vaši pozornost. Níže jsou uvedeny některé příklady tohoto typu oznámení:
    - Virtual Machines (virtuální počítače) nejsou bezpečně nakonfigurované jako otevřený port 3389.
    - Vyřazení funkčnosti

### <a name="configure-service-alerts"></a>Konfigurace výstrah služby

Konfigurace výstrah služby:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **Service Health.**
3. Pomocí pokynů v tématu [vytvoření výstrah protokolu aktivit v oznámeních služby](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) nastavte výstrahy a oznámení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak nastavit a použít Azure Service Health k monitorování problémů služby a Poradce pro stav pro virtuální počítače s Windows. Další informace o tom, jak se přihlásit k virtuálnímu počítači s Windows, najdete v tématu připojení k virtuálnímu počítači s Windows.

> [!div class="nextstepaction"]
> [Připojení ke klientovi vzdálené plochy v systému Windows 7 a Windows 10](./connect-windows-7-and-10.md)
