---
title: Export Jupyter Notebook projektu z Azure Notebooks Preview
description: Rychle exportujte Jupyter Notebook projekt.
ms.topic: quickstart
ms.date: 06/29/2020
ms.openlocfilehash: a2e76f2c1956df4d0de59cfb429dc1c9dce85a62
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892095"
---
# <a name="quickstart-export-a-jupyter-notebook-project-in-azure-notebooks-preview"></a>Rychlý Start: Export Jupyter Notebook projektu v Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

V tomto rychlém startu stáhnete Azure Notebooks projekt pro použití v jiných Jupyter Notebook řešení. 

## <a name="prerequisites"></a>Předpoklady

Existující projekt Azure Notebooks.

## <a name="export-an-azure-notebooks-project"></a>Exportovat Azure Notebooks projekt

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Vyberte projekt.
1. Kliknutím na tlačítko Stáhnout Stáhnout spusťte stahování souboru zip, který obsahuje všechny soubory projektu.
1. Případně můžete z konkrétní stránky projektu stáhnout všechny soubory daného projektu kliknutím na tlačítko Stáhnout projekt.

Po stažení souborů projektu je můžete použít s jinými řešeními Jupyter Notebook. Některé možnosti jsou popsané v následujících částech: 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [Visual Studio Codespaces](#use-notebooks-in-visual-studio-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>Vytvoření prostředí pro poznámkové bloky

Pokud chcete vytvořit prostředí, které odpovídá Azure Notebooks ve verzi Preview, můžete použít soubor skriptu, který je k dispozici na GitHubu.

1. Přejděte do [úložiště GitHub](https://github.com/microsoft/AzureNotebooks) Azure Notebooks nebo [Získejte přímý přístup ke složce prostředí](https://aka.ms/aznbrequirementstxt).
1. Z příkazového řádku přejděte do adresáře, který chcete použít pro vaše projekty.
1. Stáhněte si obsah složky prostředí a podle pokynů v souboru READme nainstalujte Azure Notebooks závislostí balíčku.


## <a name="use-notebooks-in-visual-studio-code"></a>Použití poznámkových bloků v Visual Studio Code

[Vs Code](https://code.visualstudio.com/) je bezplatný Editor kódu, který můžete použít místně nebo připojit ke vzdálenému výpočetnímu prostředí. V kombinaci s rozšířením Python nabízí celé prostředí pro vývoj v jazyce Python, včetně bohatých nativních prostředí pro práci s Jupyter poznámkovým blokem. 

![Podpora VS Code Jupyter Notebook](media/vs-code-jupyter-notebook.png)

Po [stažení](#export-an-azure-notebooks-project) souborů projektu je můžete použít s vs Code. Pokyny, jak VS Code s poznámkovým blokům Jupyter, najdete v tématu [práce s poznámkovými bloky Jupyter v Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support) a [v oblasti datové vědy v tématu Visual Studio Code](https://code.visualstudio.com/docs/python/data-science-tutorial) kurzy.

Pomocí [skriptu Azure Notebooks prostředí](#create-an-environment-for-notebooks) s Visual Studio Code můžete také vytvořit prostředí, které odpovídá Azure Notebooks Preview.

## <a name="use-notebooks-in-visual-studio-codespaces"></a>Použití poznámkových bloků v aplikaci Visual Studio Codespaces

Visual Studio Codespaces poskytuje prostředí hostovaná v cloudu, kde můžete upravovat svoje poznámkové bloky pomocí Visual Studio Code nebo webového prohlížeče. Nabízí stejné skvělé prostředí Jupyter jako VS Code, ale bez nutnosti nainstalovat na zařízení cokoli. Pokud nechcete nastavit místní prostředí a preferovat řešení, které je v cloudu, pak je vytvoření codespace Skvělé. Jak začít:

1. [Stáhněte](#export-an-azure-notebooks-project) si soubory projektu.
1. [Vytvořte úložiště GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) pro ukládání vašich poznámkových bloků. 
1. [Přidejte své soubory](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) do úložiště.
1. Instalační program sady Visual Studio Codespaces v [prohlížeči](https://docs.microsoft.com/visualstudio/online/how-to/browser), v [aplikaci visual Studio](https://docs.microsoft.com/visualstudio/online/how-to/vside)nebo [Visual Studio Code](https://docs.microsoft.com/visualstudio/online/how-to/vscode).

## <a name="use-notebooks-with-azure-machine-learning"></a>Použití poznámkových bloků s Azure Machine Learning

Azure Machine Learning poskytuje ucelenou platformu pro strojové učení, která uživatelům umožňuje rychlejší sestavování a nasazování modelů v Azure. Azure ML umožňuje spouštět poznámkové bloky Jupyter na virtuálním počítači nebo ve sdíleném výpočetním prostředí clusteru. Pokud potřebujete cloudové řešení pro úlohy ML s využitím sledování experimentů, správy datových sad a dalších, doporučujeme Azure Machine Learning. Začínáme s Azure ML:

1. [Stáhněte](#export-an-azure-notebooks-project) si soubory projektu.
1. [Vytvořte pracovní prostor](../machine-learning/how-to-manage-workspace.md) v Azure Portal.

   ![Vytvořit pracovní prostor](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Otevřete [Azure Studio (Preview)](https://ml.azure.com/).
1. Pomocí panelu navigace na levé straně vyberte **poznámkové bloky**.
1. Klikněte na tlačítko **nahrát soubory** a nahrajte soubory projektu, které jste stáhli z Azure Notebooks.

Pokud chcete získat další informace o Azure ML a spuštění poznámkových bloků Jupyter, můžete si projít [dokumentaci](../machine-learning/how-to-run-jupyter-notebooks.md) nebo si v Microsoft Learn vyzkoušet [úvodní a Machine Learning](https://docs.microsoft.com/learn/modules/intro-to-azure-machine-learning-service/) modul.


## <a name="use-azure-lab-services"></a>Použít Azure Lab Services

[Azure Lab Services](https://azure.microsoft.com/services/lab-services/) umožnit pedagogům snadné nastavení a poskytování přístupu na vyžádání k předkonfigurovaným virtuálním počítačům pro celou učebnu. Pokud hledáte způsob, jak pracovat s poznámkovým blokům Jupyter a Cloud COMPUTE v prostředí s přizpůsobenou učebnou, je testovací služba skvělou možností.

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

 Po [stažení](#export-an-azure-notebooks-project) souborů projektu je můžete použít s Azure Lab Services. Pokyny k nastavení testovacího prostředí najdete v tématu [Nastavení testovacího prostředí pro učení datových věd pomocí poznámkových bloků Python a Jupyter](../lab-services/class-type-jupyter-notebook.md) .

## <a name="use-github"></a>Použití GitHubu

GitHub poskytuje bezplatný a zálohovaný způsob založený na zdrojovém řízení pro ukládání poznámkových bloků (a dalších souborů), sdílení vašich poznámkových bloků s ostatními a spolupráci. Pokud hledáte způsob, jak sdílet projekty a spolupracovat s ostatními, je GitHub skvělým parametrem a můžete ho kombinovat se sadou [Visual Studio Codespaces](#use-notebooks-in-visual-studio-codespaces) , abyste mohli Skvělé vývojové prostředí. Začínáme s GitHubem

1. [Stáhněte](#export-an-azure-notebooks-project) si soubory projektu.
1. [Vytvořte úložiště GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) pro ukládání vašich poznámkových bloků. 
1. [Přidejte své soubory](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) do úložiště.

## <a name="next-steps"></a>Další kroky

- [Informace o Pythonu v Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial)
- [Další informace o Azure Machine Learning a poznámkových blocích Jupyter](../machine-learning/how-to-run-jupyter-notebooks.md)
- [Další informace o Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/)
- [Informace o Azure Lab Services](https://azure.microsoft.com/services/lab-services/)
- [Další informace o GitHubu](https://help.github.com/github/getting-started-with-github/)
