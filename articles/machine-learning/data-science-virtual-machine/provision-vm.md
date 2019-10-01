---
title: 'Rychlý Start: vytvoření DSVM pro Windows'
description: Nakonfigurujte a vytvořte Data Science Virtual Machine v Azure pro analýzy a strojové učení.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: fcd115b672e4e2677cb7ad48fc94905747d66781
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675115"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Rychlý Start: nastavení Data Science Virtual Machine pro Windows

Začněte pracovat s Windows Data Science Virtual Machine.

## <a name="prerequisite"></a>Předpoklad

Pokud chcete vytvořit Data Science Virtual Machine Windows, musíte mít předplatné Azure. [Vyzkoušejte si Azure zdarma](https://azure.com/free).
Upozorňujeme prosím, že bezplatné účty Azure nepodporují SKU virtuálních počítačů s povoleným GPU.

## <a name="create-your-dsvm"></a>Vytvoření DSVM

Vytvoření instance DSVM:

1. Pokud ještě nejste přihlášení, můžete přejít na [Azure Portal](https://portal.azure.com) zobrazí se výzva k přihlášení k účtu Azure.
1. Vyhledejte výpis virtuálního počítače zadáním příkazu "virtuální počítač pro datové vědy" a výběrem Data Science Virtual Machine-Windows 2016.

    ![Seznam virtuálních počítačů s Windows](./media/provision-vm/search-windows.png)

1. V dolní části vyberte tlačítko **vytvořit** .

    [![](media/provision-vm/create-windows-expanded.png#lightbox)(media/provision-vm/create-windows.png "Tlačítko pro vytvoření počítače s Windows")](media/provision-vm/create-windows-expanded.png#lightbox)

1. Měli byste se přesměrovat na okno vytvořit virtuální počítač.
   karta ![Basics odpovídající virtuálnímu počítači s Windows @ no__t-1

1. Vyplňte kartu **základy** :
      * **Předplatné**: Pokud máte více než jedno předplatné, vyberte ten, na kterém se bude počítač vytvářet a účtují. Pro toto předplatné musíte mít oprávnění pro vytváření prostředků.
      * **Skupina prostředků**: Vytvořte novou skupinu nebo použijte existující.
      * **Název virtuálního počítače**: zadejte název virtuálního počítače. Tímto způsobem se zobrazí ve vašem Azure Portal.
      * **Umístění**: vyberte příslušné datové centrum. Pro nejrychlejší přístup k síti je to datové centrum, které má většinu vašich dat nebo je nejblíže vašemu fyzickému umístění. Přečtěte si další informace o [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Obrázek**: ponechte výchozí hodnotu.
      * **Velikost**: Tato hodnota by měla automaticky naplnit velikost, která je vhodná pro obecné úlohy. Přečtěte si další informace o [velikostech virtuálních počítačů s Windows v Azure](../../virtual-machines/windows/sizes.md).
      * **Uživatelské jméno**: zadejte uživatelské jméno správce. Toto je uživatelské jméno, které použijete k přihlášení k virtuálnímu počítači, a nemusí být stejné jako uživatelské jméno Azure.
      * **Heslo**: zadejte heslo, které budete používat pro přihlášení k virtuálnímu počítači.    
1. Vyberte **zkontrolovat + vytvořit**.
1. **Zkontrolovat a vytvořit**
   * Ověřte, zda jsou všechny informace, které jste zadali, správné. 
   * Vyberte **vytvořit**.


> [!NOTE]
> * Na software, který je připravený předem načtený na virtuálním počítači, neplatíte poplatky za licence. Za velikost serveru, kterou jste zvolili v kroku **Velikost** , platíte náklady na výpočetní výkon.
> * Zřizování trvá 10 až 20 minut. Stav virtuálního počítače můžete zobrazit na Azure Portal.

## <a name="access-the-dsvm"></a>Přístup k DSVM

Až se virtuální počítač vytvoří a zřídí, postupujte podle kroků uvedených v části [připojení k virtuálnímu počítači založenému na Azure](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md). Použijte přihlašovací údaje účtu správce, které jste nakonfigurovali v kroku **základní informace** o vytvoření virtuálního počítače. 

Jste připraveni začít používat nástroje, které jsou nainstalované a nakonfigurované na virtuálním počítači. Mnohé z těchto nástrojů jsou dostupné prostřednictvím dlaždic v nabídce **Start** a ikon na ploše.

K Azure Notebooks také můžete připojit DSVM a spustit poznámkové bloky Jupyter na virtuálním počítači a obejít omezení bezplatné úrovně služby. Další informace najdete v tématu [Správa a konfigurace projektů poznámkových bloků](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects).

<a name="tools"></a>


## <a name="next-steps"></a>Další kroky

* Prozkoumejte nástroje na DSVM otevřením nabídky **Start** .
* Přečtěte si o službě Azure Machine Learning, kterou si přečtete, [co je Azure Machine Learning služba?](../service/overview-what-is-azure-ml.md) a vyzkoušet si [kurzy](../index.yml).
* V Průzkumníku souborů přejděte do složky C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts, kde najdete ukázky, které používají knihovnu RevoScaleR v jazyce R, která podporuje analýzu dat v podnikovém měřítku. 
* Přečtěte si článek [deset věcí, které můžete provádět na data Science Virtual Machine](https://aka.ms/dsvmtenthings).
* Naučte se systematicky sestavovat komplexní Analytická řešení pomocí [procesu vědeckého zpracování týmových dat](../team-data-science-process/index.yml).
* Podívejte se na [Azure AI Gallery](https://gallery.cortanaintelligence.com) pro ukázkové strojové učení a analýzu dat, které používají Azure Machine Learning a související datové služby v Azure. K dispozici jsme také ikonu pro tuto galerii v nabídce **Start** a na ploše virtuálního počítače.
* Projděte si příslušnou [referenční dokumentaci](./reference-windows-vm.md) pro tento virtuální počítač.

