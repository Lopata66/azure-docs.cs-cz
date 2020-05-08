---
title: Správa učebných cvičení v Azure Lab Services | Microsoft Docs
description: Naučte se vytvářet a konfigurovat testovací prostředí pro učebnu, Zobrazit všechny vývojové laboratoře, sdílet odkaz na registraci s uživatelem testovacího prostředí nebo odstranit testovací prostředí.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: ed1c2f14ca5a791743a95cc50e5af0c29c74fefe
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983308"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Správa učebných cvičení v Azure Lab Services 
Tento článek popisuje, jak vytvořit a odstranit testovací prostředí pro učebnu. Také se dozvíte, jak zobrazit všechny vývojové laboratoře v účtu testovacího prostředí. 

## <a name="prerequisites"></a>Požadavky
Pokud chcete nastavit testovací prostředí v učebně v účtu testovacího prostředí, musíte v účtu testovacího prostředí být členem role **Autor testovacího prostředí**. Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Vlastník testovacího prostředí může přidat další uživatele do role Autor testovacího prostředí podle postupu v následujícím článku: [Přidání uživatele do role Autor testovacího prostředí](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

1. Přejděte na [web Azure Lab Services](https://labs.azure.com). Aplikace Internet Explorer 11 není zatím podporována. 
2. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje. Vyberte nebo zadejte **ID uživatele** , který je členem role **testovacího prostředí** v účtu testovacího prostředí, a zadejte heslo. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. Vyberte **nové testovací prostředí**. 
    
    ![Vytvoření testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí. 
    2. Vyberte **velikost virtuálních počítačů** , které pro třídu potřebujete. Seznam dostupných velikostí najdete v části [velikosti virtuálních počítačů](#vm-sizes) . 
    3. Vyberte **bitovou kopii virtuálního počítače** , kterou chcete použít pro prostředí učebny. Pokud vyberete image pro Linux, zobrazí se vám možnost Povolit pro ni připojení ke vzdálené ploše. Podrobnosti najdete v tématu [Povolení připojení ke vzdálené ploše pro Linux](how-to-enable-remote-desktop-linux.md).

        Pokud jste se přihlásili pomocí přihlašovacích údajů vlastníka účtu testovacího prostředí, zobrazí se vám možnost Povolit pro testovací prostředí více imagí. Další informace najdete v tématu [Povolení imagí v době vytváření testovacího prostředí](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation).
    4. Zkontrolujte **celkovou cenu za hodinu** zobrazenou na stránce. 
    6. Vyberte **Uložit**.

        ![Nové okno testovacího prostředí](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Pokud byl účet testovacího prostředí nastavený tak, aby [umožňoval výběr umístění testovacího](allow-lab-creator-pick-lab-location.md) prostředí, zobrazí se vám možnost vybrat umístění pro testovací prostředí. 
4. Na stránce **přihlašovací údaje virtuálního počítače** zadejte výchozí přihlašovací údaje pro všechny virtuální počítače v testovacím prostředí.
    1. Zadejte **jméno uživatele** pro všechny virtuální počítače v testovacím prostředí.
    2. Zadejte **heslo** tohoto uživatele. 

        > [!IMPORTANT]
        > Uživatelské jméno a heslo si poznamenejte. Znovu se už nezobrazí.
    3. Pokud chcete, aby studenti nastavili vlastní hesla, zakažte možnost **použít stejné heslo pro všechny virtuální počítače** . Tento krok je **nepovinný**. 

        Učitel se může rozhodnout použít stejné heslo pro všechny virtuální počítače v testovacím prostředí, nebo umožní studentům nastavit hesla pro svoje virtuální počítače. Ve výchozím nastavení je toto nastavení povolené pro všechny image Windows a Linux s výjimkou Ubuntu. Když vyberete virtuální počítač **Ubuntu** , toto nastavení se zakáže, takže studenti budou vyzváni, aby při prvním přihlášení nastavili heslo.  

        ![Nové okno testovacího prostředí](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Pak na stránce **pověření k virtuálnímu počítači** vyberte **Další** . 
5. Na stránce **zásady testovacího prostředí** proveďte následující kroky:
    1. Zadejte počet hodin přidělený každému uživateli (**kvóta pro každého uživatele**) mimo plánovaný čas testovacího prostředí. 
    2. U možnosti **Automatické vypnutí virtuálních počítačů** určete, jestli se má virtuální počítač automaticky vypnout, když se uživatel odpojí. Můžete také určit, jak dlouho by měl virtuální počítač čekat, než se uživatel znovu připojí, než se automaticky vypíná... Další informace najdete v tématu [Povolení automatického vypnutí virtuálních počítačů při odpojení](how-to-enable-shutdown-disconnect.md).
    3. Pak vyberte **Dokončit**. 

        ![Kvóta pro každého uživatele](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Měla by se zobrazit následující obrazovka, která zobrazuje stav vytvoření virtuálního počítače šablony. Vytvoření šablony v testovacím prostředí může trvat až 20 minut. 

    ![Stav vytvoření virtuálního počítače šablony](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na stránce **Šablona** proveďte následující kroky: tyto kroky jsou pro kurz **volitelné** .

    2. Vyberte **Connect** (Připojit) a připojte se k virtuálnímu počítači šablony. Pokud se jedná o virtuální počítač šablony pro Linux, můžete zvolit, jestli se chcete připojit pomocí protokolu SSH nebo RDP (Pokud je povolený protokol RDP).
    1. Pokud chcete resetovat heslo pro tento virtuální počítač, vyberte **resetovat heslo** . 
    1. Nainstalujte a nakonfigurujte na virtuálním počítači šablony požadovaný software. 
    1. **Zastavte** virtuální počítač.  
    1. Zadejte **popis** šablony.
10. Na stránce **Šablona** vyberte **publikovat** na panelu nástrojů. 

    ![Tlačítko publikovat šablonu](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Publikování nejde vrátit zpět. 
8. Na stránce **publikovat šablonu** zadejte počet virtuálních počítačů, které chcete v testovacím prostředí vytvořit, a pak vyberte **publikovat**. 

    ![Šablona publikování – počet virtuálních počítačů](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Na stránce se zobrazí **stav publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Přepněte na stránku **fondu virtuálních počítačů** výběrem možnosti virtuální počítače v nabídce vlevo nebo výběrem dlaždice virtuální počítače. Ověřte, že se zobrazují virtuální počítače, které jsou v **nepřiřazeném** stavu. Tyto virtuální počítače ještě nejsou přiřazené ke studentům. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Virtuální počítače můžete spustit na této stránce nebo jejich spuštění můžete nechat na studentech. 

    ![Virtuální počítače v zastaveném stavu](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Na této stránce provedete následující úlohy (u tohoto kurzu tento postup neprovádějte). Tyto kroky jsou pouze pro vaše informace.): 
    
    1. Pokud chcete změnit kapacitu testovacího prostředí (počet virtuálních počítačů v testovacím prostředí), vyberte na panelu nástrojů možnost **kapacita testovacího prostředí** .
    2. Chcete-li spustit všechny virtuální počítače najednou, vyberte možnost **Spustit vše** na panelu nástrojů. 
    3. Pokud chcete spustit konkrétní virtuální počítač, vyberte ve **stavu**šipku dolů a pak vyberte **Spustit**. Virtuální počítač můžete spustit také tak, že vyberete virtuální počítač v prvním sloupci a pak výběrem možnosti **Spustit** na panelu nástrojů.                

### <a name="vm-sizes"></a>Velikost virtuálních počítačů  

| Velikost | Cores | Paměť RAM | Popis | 
| ---- | ----- | --- | ----------- | 
| Krátkodobé používání | 2 | 3,5 GB | Tato velikost je nejvhodnější pro příkazový řádek, otevírá webový prohlížeč, webové servery s nízkým provozem, malé až střední databáze. |
| Střednědobé používání | 4 | 7 GB | Tato velikost se nejlépe hodí pro relační databáze, ukládání do mezipaměti v paměti a analýzy. | 
| Střední (vnořená virtualizace) | 4 | 16 GB | Tato velikost se nejlépe hodí pro relační databáze, ukládání do mezipaměti v paměti a analýzy. Tato velikost také podporuje vnořenou virtualizaci. <p>Tato velikost se dá použít ve scénářích, kdy každý student potřebuje víc virtuálních počítačů. Učitelé můžou pomocí vnořené virtualizace nastavit v rámci virtuálního počítače několik vnořených virtuálních počítačů s malou velikostí. </p> |
| Malý grafický procesor (COMPUTE) | 6 | 56 GB | Tato velikost se nejlépe hodí pro aplikace náročné na výpočetní výkon a sítě, jako jsou umělá studia a aplikace s hloubkovým učením. | 
| Malý grafický procesor (vizualizace) | 6 | 56 GB | Tato velikost se nejlépe hodí pro vzdálenou vizualizaci, streamování, hraní her a kódování pomocí platforem, jako je OpenGL a DirectX. | 
| Dlouhodobé používání | 8 | 16 GB | Tato velikost je nejvhodnější pro aplikace, které vyžadují rychlejší procesory, lepší výkon místních disků, velké databáze a velké mezipaměti paměti. |
| Velký (vnořená virtualizace) | 8 | 32 GB | Tato velikost je nejvhodnější pro aplikace, které vyžadují rychlejší procesory, lepší výkon místních disků, velké databáze a velké mezipaměti paměti. Tato velikost také podporuje vnořenou virtualizaci. |  
| Střední GPU (vizualizace) | 12 | 112 GB | Tato velikost se nejlépe hodí pro vzdálenou vizualizaci, streamování, hraní her a kódování pomocí platforem, jako je OpenGL a DirectX. | 

> [!NOTE]
> Při vytváření testovacího prostředí s imagemi GPU Azure Lab Services automaticky nainstaluje a nakonfiguruje potřebné ovladače GPU.  

## <a name="view-all-classroom-labs"></a>Zobrazit všechna cvičení v učebně
1. Přejděte na [portál Azure Lab Services](https://labs.azure.com).
2. Vyberte **Sign in** (Přihlásit se). Vyberte nebo zadejte **ID uživatele** , který je členem role **testovacího prostředí** v účtu testovacího prostředí, a zadejte heslo. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. Potvrďte, že ve vybraném účtu testovacího prostředí vidíte všechny laboratoře. Na dlaždici testovacího prostředí uvidíte počet virtuálních počítačů v testovacím prostředí a kvótu pro každého uživatele (mimo naplánovaný čas).

    ![Všechny laboratoře](../media/how-to-manage-classroom-labs/all-labs.png)
3. Pomocí rozevíracího seznamu v horní části vyberte jiný účet testovacího prostředí. V rámci vybraného účtu testovacího prostředí uvidíte Labs. 

## <a name="delete-a-classroom-lab"></a>Odstranění testovacího prostředí učebny
1. Na dlaždici pro testovací prostředí vyberte v rohu tři tečky (...) a pak vyberte **Odstranit**. 

    ![Tlačítko Odstranit](../media/how-to-manage-classroom-labs/delete-button.png)
3. V dialogovém okně **Odstranit testovací prostředí** vyberte **Odstranit** , aby bylo možné pokračovat v odstraňování. 

## <a name="switch-to-another-classroom-lab"></a>Přepnout na další prostředí učebny
Pokud chcete z aktuální nabídky přejít na jiné prostředí učebny, vyberte rozevírací seznam cvičení v účtu testovacího prostředí v horní části.

![V horní části vyberte testovací prostředí z rozevíracího seznamu.](../media/how-to-manage-classroom-labs/switch-lab.png)

Nové testovací prostředí můžete vytvořit také pomocí **nového testovacího prostředí** v tomto rozevíracím seznamu. 

> [!NOTE]
> Ke správě testovacích prostředí můžete použít také modul PowerShellu AZ. LabServices (Preview). Další informace najdete na [domovské stránce AZ. LabServices na GitHubu](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Pokud chcete přepnout na jiný účet testovacího prostředí, vyberte rozevírací nabídku vedle účtu testovacího prostředí a vyberte jiný účet testovacího prostředí. 

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Jako vlastník testovacího prostředí, nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel testovacího prostředí, Access učeben Labs](how-to-use-classroom-lab.md)

