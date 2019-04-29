---
title: Správa základních zásad testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak nastavit některé základní zásady (nastavení) pro testovací prostředí v DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 8cc529fbf9b24335be1bec07f81c732ced7a2b72
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773838"
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Správa základních zásad testovacího prostředí ve službě Azure DevTest Labs

Azure DevTest Labs umožňuje řízení nákladů a minimalizace plýtvání v vaše testovací prostředí tím, že spravuje zásady (nastavení) pro každý testovací prostředí. V tomto článku jste Začínáme se zásadami učit, jak nastavit dva nejdůležitější zásad – omezením počtu virtuálních počítačů (VM), které můžou vytvořit nebo nárokován jenom jednoho konkrétního uživatele a konfiguraci automatického vypínání. Chcete-li zobrazit nastavení všech zásad testovacího prostředí, najdete v článku [definice zásad testovacího prostředí ve službě Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Přístup k zásad testovacího prostředí ve službě Azure DevTest Labs
Následující kroky vás provedou nastavením zásad pro testovací prostředí v Azure DevTest Labs:

K zobrazení (a změna) zásad pro testovací prostředí, postupujte podle těchto kroků:

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Vyberte **všechny služby**a pak vyberte **DevTest Labs** ze seznamu.

1. V seznamu testovacích prostředí vyberte požadované prostředí.   

1. Vyberte **konfigurace a zásad**.

    ![Podokno nastavení zásad](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. **Konfigurace a zásad** podokno obsahuje nabídku nastavení, které určíte. Tento článek se týká jenom nastavení pro **virtuálních počítačů na uživatele**, **automatického vypínání**, a **automatického spuštění**. Informace o zbývajících nastaveních najdete v tématu [Správa všech zásad pro testovací prostředí ve službě Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Sada virtuálních počítačů na uživatele
Zásady pro **virtuálních počítačů na uživatele** vám umožní určit maximální počet virtuálních počítačů, které lze vytvořit podle jednotlivých uživatelů. Pokud uživatel se pokusí vytvořit nebo deklarací identity virtuálního počítače, pokud byly splněny limit počtu uživatelů, chybová zpráva označuje, že virtuální počítač nemůže být vytvořen nárokován. 

1. Cvičení **konfigurace a zásad** nabídce vyberte možnost **virtuálních počítačů na uživatele**.
   
    ![Virtuálních počítačů na uživatele](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Vyberte **Ano** omezit počet virtuálních počítačů na uživatele. Pokud nechcete omezit počet virtuálních počítačů na uživatele, vyberte **ne**. Pokud vyberete **Ano**, zadejte číselnou hodnotu určující maximální počet virtuálních počítačů, které můžou vytvořit nebo jeden uživatel. 

1. Vyberte **Ano** omezit počet virtuálních počítačů, které můžete použít SSD (SSD disk). Pokud nechcete omezit počet virtuálních počítačů, které můžete používat SSD, vyberte **ne**. Pokud vyberete **Ano**, zadejte hodnotu, která udává maximální počet virtuálních počítačů, které lze vytvořit pomocí SSD. 

1. Vyberte **Uložit**.

## <a name="set-auto-shutdown"></a>Nastavení automatického vypínání
Zásady automatického vypínání pomáhá minimalizovat plýtvání testovacího prostředí, neboť umožňuje určit čas, který se vypnout virtuální počítače v tomto testovacím prostředí.

1. Cvičení **konfigurace a zásad** vyberte **automatického vypínání**.
   
    ![Automatické vypínání](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Vyberte **na** pro tuto zásadu povolit a **vypnout** ho zakážete.

1. Pokud tuto zásadu povolit, zadejte čas (a časové pásmo), vypnutí všech virtuálních počítačích v aktuálním testovacím prostředí.

1. Zadejte **Ano** nebo **ne** pro možnost poslat oznámení 15 minut před časem zadané automatického vypínání. Pokud se rozhodnete **Ano**, zadejte koncový bod adresy URL webhooku nebo e-mailovou adresu zadáte, ve kterém chcete oznámení k odeslání nebo odeslání. Uživatel obdrží oznámení a je zadána možnost zpoždění vypnutí počítače.

   Další informace o webhooků najdete v tématu [vytvoření webhooku nebo funkce rozhraní API Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Vyberte **Uložit**.

Ve výchozím nastavení, jakmile bude povoleno, tyto zásady platí pro všechny virtuální počítače v aktuálním testovacím prostředí. Tato nastavení odebrat z konkrétní virtuální počítač, otevřete panel pro správu Virtuálního počítače a změňte jeho **automatického vypínání** nastavení.

## <a name="set-auto-start"></a>Nastavení automatické spuštění
Zásady automatického spuštění můžete zadat, pokud by měl být spuštěn virtuální počítače v aktuálním testovacím prostředí.  

1. Cvičení **konfigurace a zásad** vyberte **automatického spuštění**.
   
    ![Automatické spuštění](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Vyberte **na** pro tuto zásadu povolit a **vypnout** ho zakážete.

3. Pokud tuto zásadu povolit, zadejte naplánovaný čas zahájení, časové pásmo a dny v týdnu, pro kterou platí čas. 

4. Vyberte **Uložit**.

Jakmile bude povoleno, není tato zásada použitá automaticky pro všechny virtuální počítače v aktuálním testovacím prostředí. Chcete-li použít tato nastavení pro existující virtuální počítač, otevřete panel pro správu Virtuálního počítače a změňte jeho **automatického spuštění** nastavení.

## <a name="next-steps"></a>Další postup

- [Definice zásad testovacího prostředí ve službě Azure DevTest Labs](devtest-lab-set-lab-policy.md) – zjistěte, jak upravit další zásady testovacího prostředí.
