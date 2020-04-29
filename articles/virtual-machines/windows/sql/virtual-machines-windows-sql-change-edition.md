---
title: Místní změna edice SQL Server
description: Přečtěte si, jak změnit edici virtuálního počítače s SQL Server v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77605455"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Místní změna edice SQL Server na virtuálním počítači Azure

Tento článek popisuje, jak změnit edici SQL Server na virtuálním počítači s Windows v Azure. 

Edice SQL Server je určena klíčovým produktem a je určena během procesu instalace pomocí instalačního média. Edice určuje, jaké [funkce](/sql/sql-server/editions-and-components-of-sql-server-2017) jsou k dispozici v SQL Server produktu. Můžete změnit edici SQL Server pomocí instalačního média a snížit náklady nebo upgradovat, aby se povolilo více funkcí.

Po interní změně edice SQL Server k virtuálnímu počítači SQL Server musíte v Azure Portal pro účely fakturace aktualizovat vlastnost edice SQL Server. 

## <a name="prerequisites"></a>Požadavky

Chcete-li provést místní změnu edice SQL Server, budete potřebovat následující: 

- [Předplatné Azure](https://azure.microsoft.com/free/).
- [SQL Server virtuální počítač ve Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) zaregistrovaný u [poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- Nastavte médium s **požadovanou verzí** SQL Server. Zákazníci, kteří mají [program Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) , mohou získat instalační média z [centra](https://www.microsoft.com/Licensing/servicecenter/default.aspx)multilicenčního programu. Zákazníci, kteří nemají Software Assurance, mohou použít instalační médium z Azure Marketplace SQL Server image virtuálního počítače, která má požadovanou edici (obvykle se `C:\SQLServerFull`nachází v části). 


## <a name="upgrade-an-edition"></a>Upgrade edice

> [!WARNING]
> Upgrade edice SQL Server restartuje službu pro SQL Server společně s případnými přidruženými službami, jako jsou služby Analysis Services a R. 

Chcete-li upgradovat edici SQL Server, Získejte instalační médium SQL Server pro požadovanou edici SQL Server a proveďte následující kroky:

1. Otevřete soubor Setup. exe z instalačního média SQL Server. 
1. Pokračujte na **Údržba** a vyberte možnost **upgradu edice** . 

   ![Výběr pro upgrade edice SQL Server](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. Vyberte **Další** , dokud se nedostanete na stránku **připraveno k upgradu** , a pak vyberte **upgradovat**. Když se změna projeví, může okno instalace přestat reagovat během několika minut. Po **dokončení** této stránky se ověří, že je upgrade edice dokončený. 

Po upgradu edice SQL Server upravte vlastnost edice SQL Server virtuálního počítače v Azure Portal. Tím se aktualizují metadata a fakturace přidružená k tomuto virtuálnímu počítači.

## <a name="downgrade-an-edition"></a>Downgrade edice

Chcete-li downgradovat edici SQL Server, je nutné zcela odinstalovat SQL Server a znovu ji znovu nainstalovat pomocí instalačního média požadovaného vydání. 

> [!WARNING]
> Odinstalace SQL Server může způsobit další výpadky. 

Verzi SQL Server můžete downgradovat pomocí následujících kroků:

1. Zálohujte všechny databáze, včetně systémových databází. 
1. Přesuňte systémové databáze (hlavní, model a msdb) do nového umístění. 
1. Kompletně odinstalujte SQL Server a všechny přidružené služby. 
1. Restartujte virtuální počítač. 
1. Nainstalujte SQL Server pomocí média s požadovanou verzí SQL Server.
1. Nainstalujte nejnovější aktualizace Service Pack a kumulativní aktualizace.  
1. Nahraďte nové systémové databáze, které byly vytvořeny během instalace, se systémovými databázemi, které jste dříve přesunuli do jiného umístění. 

Po downgradu edice SQL Server upravte vlastnost edice SQL Server virtuálního počítače v Azure Portal. Tím se aktualizují metadata a fakturace přidružená k tomuto virtuálnímu počítači.

## <a name="change-edition-in-portal"></a>Změna edice na portálu 

Pokud jste změnili edici SQL Server pomocí instalačního média a zaregistrovali jste SQL Server virtuální počítač s [poskytovatelem prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md), můžete pomocí Azure Portal změnit vlastnost edice SQL Server virtuálního počítače pro účely fakturace. Postup je následující: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
1. Přejít na prostředek SQL Server prostředku virtuálního počítače. 
1. V části **Nastavení**vyberte **Konfigurovat**. Pak v rozevíracím seznamu v části **edice**vyberte požadovanou edici SQL Server. 

   ![Změnit metadata edice](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. Přečtěte si upozornění, které uvádí, že je nutné nejprve změnit edici SQL Server a zda vlastnost edice musí odpovídat edici SQL Server. 
1. Pokud chcete použít změny metadat edice, vyberte **použít** . 


## <a name="remarks"></a>Poznámky

- Vlastnost edice pro virtuální počítač s SQL Server musí odpovídat edici SQL Server nainstalované pro všechny SQL Server virtuální počítače, včetně průběžných plateb a typů licencí.
- Pokud přetáhnete prostředek virtuálního počítače SQL Server, vrátíte se do nastavení pevně zakódované verze image.
- Možnost změnit edici je funkcí poskytovatele prostředků virtuálního počítače SQL. Nasazení bitové kopie Azure Marketplace pomocí Azure Portal automaticky registruje SQL Server virtuální počítač s poskytovatelem prostředků. Zákazníci, kteří si SQL Server vlastní instalaci, ale budou muset ručně [zaregistrovat svůj SQL Server virtuální počítač](virtual-machines-windows-sql-register-with-resource-provider.md).
- Přidání virtuálního počítače SQL Server do skupiny dostupnosti vyžaduje opětovné vytvoření virtuálního počítače. Všechny virtuální počítače přidané do skupiny dostupnosti se vrátí k výchozí edici a Tato edice bude muset být upravena znovu.

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích: 

* [Přehled SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy k SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Doprovodné materiály k cenách pro SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Poznámky k verzi pro SQL Server na virtuálním počítači s Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


