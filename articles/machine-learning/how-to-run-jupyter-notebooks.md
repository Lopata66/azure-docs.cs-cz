---
title: Spuštění poznámkových bloků Jupyter ve vašem pracovním prostoru
titleSuffix: Azure Machine Learning
description: Přečtěte si, jak spustit Poznámkový blok Jupyter, aniž byste opustili pracovní prostor v Azure Machine Learning Studiu.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 46e0687056d697afc2d4355bdf900af138273eaf
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99061830"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Spouštění poznámkových bloků Jupyter ve vlastním pracovním prostoru

Přečtěte si, jak spustit poznámkové bloky Jupyter přímo ve vašem pracovním prostoru v Azure Machine Learning Studiu. I když můžete spustit [Jupyter](https://jupyter.org/) nebo [JupyterLab](https://jupyterlab.readthedocs.io), můžete také upravit a spustit poznámkové bloky, aniž byste museli opustit pracovní prostor.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://aka.ms/AMLFree).
* Machine Learning pracovní prostor. Další informace najdete v tématu [Vytvoření pracovního prostoru Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a> Vytvoření poznámkových bloků

V pracovním prostoru Azure Machine Learning vytvořte nový Poznámkový blok Jupyter a začněte pracovat. Nově vytvořený Poznámkový blok je uložený ve výchozím úložišti pracovního prostoru. Tento Poznámkový blok můžete sdílet s kýmkoli, kdo má přístup k pracovnímu prostoru. 

Vytvoření nového poznámkového bloku: 

1. Otevřete pracovní prostor v [Azure Machine Learning Studiu](https://ml.azure.com).
1. Na levé straně vyberte **poznámkové bloky**. 
1. Vyberte ikonu  **vytvořit nový soubor** nad seznamem **uživatelských souborů** v části **Moje soubory** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Create new file":::

1. Pojmenujte soubor. 
1. Pro soubory poznámkového bloku Jupyter jako typ souboru vyberte **Poznámkový blok** .
1. Vyberte adresář souborů.
1. Vyberte **Vytvořit**.

Můžete také vytvořit textové soubory.  Jako typ souboru vyberte **text** a rozšíření přidejte k názvu (například myfile.py nebo myfile.txt).  

Pomocí nástrojů v horní části stránky poznámkových bloků můžete také nahrávat složky a soubory, včetně poznámkových bloků.  Poznámkové bloky a většina typů textových souborů se zobrazí v sekci Preview.  Pro většinu ostatních typů souborů není dostupná žádná verze Preview.

> [!IMPORTANT]
> Obsah v poznámkových blocích a skriptech může potenciálně číst data z vašich relací a přistupovat k datům bez vaší organizace v Azure.  Načte jenom soubory z důvěryhodných zdrojů. Další informace najdete v tématu [bezpečné](concept-secure-code-best-practice.md#azure-ml-studio-notebooks)osvědčené postupy pro kód.

### <a name="clone-samples"></a>Klonovat ukázky

Váš pracovní prostor obsahuje složku **ukázek** s poznámkami, které jsou navržené tak, aby vám pomohly prozkoumat sadu SDK a sloužit jako příklady pro vlastní projekty machine learningu.  Tyto poznámkové bloky můžete klonovat do vlastní složky v kontejneru úložiště pracovního prostoru.  

Příklad najdete v tématu [kurz: vytvoření prvního experimentu ml](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Použít soubory z Gitu a verze moje soubory

Ke všem operacím Git můžete přistupovat pomocí okna terminálu. Všechny soubory a složky Git budou uložené v systému souborů pracovního prostoru.

> [!NOTE]
> Přidejte své soubory a složky kamkoli do složky **~/CloudFiles/Code/Users** , aby se zobrazily ve všech prostředích Jupyter.

Přístup k terminálu:

1. Otevřete pracovní prostor v [Azure Machine Learning Studiu](https://ml.azure.com).
1. Na levé straně vyberte **poznámkové bloky**.
1. Vyberte libovolný Poznámkový blok umístěný v části **soubory uživatelů** na levé straně.  Pokud tam nemáte žádné poznámkové bloky, [vytvořte nejdřív Poznámkový blok](#create) .
1. Vyberte **výpočetní** cíl nebo vytvořte nový a počkejte, než se spustí.
1. Vyberte ikonu **otevřeného terminálu** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Otevření terminálu":::

1. Pokud ikonu nevidíte, vyberte **...** napravo od cíle výpočtů a pak vyberte **otevřít terminál**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Otevřít terminál z...":::


Přečtěte si další informace o [klonování úložišť Git do systému souborů pracovního prostoru](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

### <a name="copy-and-paste-in-terminal"></a>Kopírování a vkládání v terminálu

> * Windows: `Ctrl-Insert` kopírování a používání `Ctrl-Shift-v` nebo `Shift-Insert` vložení.
> * Mac OS: `Cmd-c` pro kopírování a `Cmd-v` vložení.
> * Aplikace FireFox/IE nemusí správně podporovat oprávnění schránky.

### <a name="share-notebooks-and-other-files"></a>Sdílení poznámkových bloků a dalších souborů

Zkopírujte a vložte adresu URL pro sdílení poznámkového bloku nebo souboru.  K této adrese URL mají přístup pouze jiní uživatelé pracovního prostoru.  Přečtěte si další informace o tom, jak [udělit přístup k pracovnímu prostoru](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Úprava poznámkového bloku

Pokud chcete upravit Poznámkový blok, otevřete libovolný Poznámkový blok umístěný v části **uživatelské soubory** v pracovním prostoru. Klikněte na buňku, kterou chcete upravit. 

Poznámkový blok můžete upravovat bez připojení k výpočetní instanci.  Pokud chcete v poznámkovém bloku spustit buňky, vyberte nebo vytvořte výpočetní instanci.  Pokud vyberete zastavenou výpočetní instanci, automaticky se spustí při spuštění první buňky.

Když je spuštěná výpočetní instance, můžete také použít dokončování kódu využívající [technologii IntelliSense](https://code.visualstudio.com/docs/editor/intellisense)v jakémkoli poznámkovém bloku Pythonu.

Na panelu nástrojů poznámkového bloku můžete také spustit Jupyter nebo JupyterLab.  Azure Machine Learning neposkytuje aktualizace a opravují chyby z Jupyter nebo JupyterLab, protože se jedná o open source produkty mimo hranici podpora Microsoftu.

### <a name="focus-mode"></a>Detailní režim

Pomocí detailního režimu můžete rozšířit aktuální zobrazení, abyste se mohli soustředit na aktivní karty. Detailní režim skryje Průzkumníka souborů poznámkových bloků.

1. Na panelu nástrojů okna terminálu vyberte **detailní režim** pro zapnutí detailního režimu. V závislosti na šířce okna se to může nacházet pod položkou nabídky **...** na panelu nástrojů.
1. V detailním režimu se po výběru **standardního zobrazení** vraťte do standardního zobrazení.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Přepnout detailní režim nebo standardní zobrazení":::


### <a name="use-intellisense"></a>Používání technologie IntelliSense

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) je podpora dokončování kódu, která zahrnuje několik funkcí: seznam členů, informace o parametrech, rychlé informace a dokončování slov. Tyto funkce vám pomůžou získat další informace o kódu, který používáte, sledovat parametry, které píšete, a přidávat volání vlastností a metod s pouze několika klávesami.  

Při psaní kódu použijte kombinaci kláves CTRL + mezerník pro aktivaci technologie IntelliSense.

### <a name="clean-your-notebook-preview"></a>Vyčištění poznámkového bloku (Preview)

> [!IMPORTANT]
> Funkce Shromážděte je aktuálně ve verzi Public Preview.
> Verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V průběhu vytváření poznámkového bloku obvykle skončíte s buňkami, které jste použili pro zkoumání nebo ladění dat. Funkce *shromáždit* vám pomůže vytvořit čistý Poznámkový blok bez těchto cizích buněk.

1. Spusťte všechny buňky v poznámkovém bloku.
1. Vyberte buňku obsahující kód, který chcete spustit pro nový Poznámkový blok. Například kód, který odesílá experiment, nebo možná kód, který registruje model.
1. Vyberte ikonu **shromáždit** , která se zobrazí na panelu nástrojů buňky.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Snímek obrazovky: výběr ikony shromáždění":::
1. Zadejte název nového poznámkového bloku "shromážděného".  

Nový Poznámkový blok obsahuje jenom buňky kódu se všemi buňkami, které jsou potřebné k vytvoření stejných výsledků jako buňky, kterou jste vybrali pro shromažďování.

### <a name="save-and-checkpoint-a-notebook"></a>Uložení a vytvoření kontrolního bodu poznámkového bloku

Azure Machine Learning vytvoří soubor kontrolního bodu při vytváření souboru *ipynb* .

Na panelu nástrojů poznámkového bloku vyberte nabídku a pak **soubor &gt; uložte a kontrolní bod** , abyste ručně uložili Poznámkový blok a přidali do něj soubor kontrolního bodu, který je přidružený k poznámkovému bloku.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Snímek obrazovky s nástrojem pro uložení v panelu nástrojů Poznámkový blok":::

Každý Poznámkový blok se automaticky uloží každých 30 sekund. Automatické ukládání aktualizuje pouze počáteční soubor *ipynb* , nikoli soubor kontrolního bodu.
 
Výběrem **kontrolního bodu** v nabídce Poznámkový blok vytvořte pojmenovaný kontrolní bod a obnovte Poznámkový blok na uložený kontrolní bod.

## <a name="delete-a-notebook"></a>Odstranění poznámkového bloku

*Nemůžete* odstranit poznámkové bloky **ukázek** .  Tyto poznámkové bloky jsou součástí studia a aktualizují se při každém publikování nové sady SDK.  

Poznámkové bloky **uživatelských souborů** *můžete* odstranit některým z těchto způsobů:

* V nástroji Studio vyberte **...** na konci složky nebo souboru.  Ujistěte se, že používáte podporovaný prohlížeč (Microsoft Edge, Chrome nebo Firefox).
* Z libovolného panelu nástrojů poznámkového bloku vyberte [**otevřít terminál**](#terminal)  pro přístup k oknu terminálu pro instanci Compute.
* V Jupyter nebo JupyterLab s jejich nástroji.

## <a name="run-a-notebook-or-python-script"></a>Spuštění poznámkového bloku nebo skriptu Pythonu

Pokud chcete spustit Poznámkový blok nebo skript Pythonu, musíte se nejdřív připojit ke spuštěné [výpočetní instanci](concept-compute-instance.md). Pokud nemáte výpočetní instanci, vytvořte ji pomocí těchto kroků: 

1. Vyberte na **+** panelu nástrojů poznámkového bloku nebo skriptu. 
2. Pojmenujte výpočetní prostředky a vyberte **Velikost virtuálního počítače**. 
3. Vyberte **Vytvořit**.
4. Instance COMPUTE je automaticky připojena k souboru.  Nyní můžete spouštět buňky poznámkového bloku nebo skriptu Pythonu pomocí nástroje nalevo od instance Compute.

Pouze můžete zobrazit a použít výpočetní instance, které vytvoříte.  Vaše **uživatelské soubory** jsou uložené odděleně od virtuálního počítače a sdílí se mezi všemi výpočetními instancemi v pracovním prostoru.

### <a name="view-logs-and-output"></a>Zobrazit protokoly a výstup

Pomocí [widgetů poznámkového bloku](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) můžete zobrazit průběh běhu a protokolů. Pomůcka je asynchronní a poskytuje aktualizace až do dokončení školení. Pomůcky Azure Machine Learning jsou také podporovány v Jupyter a JupterLab.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="Snímek obrazovky: widget poznámkového bloku Jupyter ":::

## <a name="explore-variables-in-the-notebook"></a>Prozkoumat proměnné v poznámkovém bloku

Na panelu nástrojů Poznámkový blok použijte nástroj **Průzkumník proměnných** k zobrazení názvu, typu, délky a vzorových hodnot pro všechny proměnné, které byly vytvořeny v poznámkovém bloku.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="Snímek obrazovky: Nástroj Průzkumník proměnných":::

Vyberte nástroj pro zobrazení okna Průzkumníka proměnných.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="Snímek obrazovky: okno Průzkumníka proměnných":::

## <a name="navigate-with-a-toc"></a>Navigace pomocí obsahu

Na panelu nástrojů pro Poznámkový blok použijte nástroj  **obsah** k zobrazení nebo skrytí obsahu.  Spustí Markdownu buňku s nadpisem, který se má přidat do obsahu. Kliknutím na položku v tabulce se posuňte k této buňce v poznámkovém bloku.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="Snímek obrazovky: obsah v poznámkovém bloku":::

## <a name="change-the-notebook-environment"></a>Změna prostředí poznámkového bloku

Panel nástrojů Poznámkový blok umožňuje změnit prostředí, na kterém Poznámkový blok běží.  

Tyto akce nezmění stav poznámkového bloku ani hodnoty jakýchkoli proměnných v poznámkovém bloku:

|Akce  |Výsledek  |
|---------|---------| --------|
|Zastavit jádro     |  Zastaví všechny spuštěné buňky. Při spuštění buňky se jádro automaticky restartuje. |
|Přejít do jiného oddílu pracovního prostoru     |     Běžící buňky jsou zastaveny. |

Tyto akce resetují stav poznámkového bloku a obnoví všechny proměnné v poznámkovém bloku.

|Akce  |Výsledek  |
|---------|---------| --------|
| Změna jádra | Poznámkový blok používá nové jádro |
| Přepnout výpočetní prostředky    |     Poznámkový blok automaticky používá nové výpočetní prostředky. |
| Resetovat výpočetní výkon | Spustí se znovu při pokusu o spuštění buňky. |
| Zastavit výpočetní výkon     |    Nespustí se žádné buňky.  |
| Otevření poznámkového bloku v Jupyter nebo JupyterLab     |    Poznámkový blok se otevřel na nové kartě.  |

### <a name="add-new-kernels"></a>Přidat nové jádra

Poznámkový blok automaticky vyhledá všechny jádra Jupyter nainstalované na připojené výpočetní instanci.  Přidání jádra do výpočetní instance:

1. Na panelu nástrojů poznámkového bloku vyberte [**otevřít terminál**](#terminal) .
1. Pomocí okna terminálu vytvořte nové prostředí.  Například kód uvedený níže vytvoří `newenv` :
    ```shell
    conda create -y --name newenv
    ```
1. Aktivujte prostředí.  Například po vytvoření `newenv` :

    ```shell
    conda activate newenv
    ```
1. Instalace balíčku PIP a ipykernel do nového prostředí a vytvoření jádra pro tento conda ENV

    ```shell
    conda install -y pip
    conda install -y ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```
1. Po instalaci jádra Aktualizujte prosím stránku a otevřete Poznámkový blok. V seznamu jader se teď zobrazí nové jádro.

> [!NOTE]
> Pro správu balíčků v rámci poznámkového bloku použijte funkce **% PIP** nebo **% conda** Magic k automatické instalaci balíčků do **aktuálně běžícího jádra**, ne z **! PIP** nebo **! conda** , která odkazuje na všechny balíčky (včetně balíčků mimo aktuálně běžící jádro).

Můžete nainstalovat kterýkoli z [dostupných jader Jupyter](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) .

### <a name="status-indicators"></a>Indikátory stavu

Indikátor vedle rozevíracího seznamu **COMPUTE** zobrazuje jeho stav.  Stav je zobrazen také v rozevíracím seznamu.  

|Barva |Stav výpočtů |
|---------|---------| 
| Green | Spuštěná výpočetní prostředí |
| Red |Výpočet se nezdařil | 
| Black | Služba COMPUTE byla zastavena |
|  Světle modrá |Výpočetní prostředí pro vytváření, spouštění, restartování a nastavování |
|  Les |Výpočetní odstranění, zastavení |

Indikátor vedle rozevíracího seznamu **jádro** zobrazuje jeho stav.

|Barva |Stav jádra |
|---------|---------|
|  Green |Připojení jádra, nečinné, zaneprázdněné|
|  Les |Jádro není připojeno. |

## <a name="find-compute-details"></a>Najít podrobnosti o COMPUTE

Podrobnosti o vašich výpočetních instancích najdete na stránce **COMPUTE** v [studiu](https://ml.azure.com).

## <a name="troubleshooting"></a>Řešení potíží

* Pokud se nemůžete připojit k poznámkovému bloku, ujistěte se, že komunikace webového soketu **není zakázaná** . Aby funkce COMPUTE instance Jupyter fungovala, musí být povolená komunikace webového soketu. Ujistěte se prosím, že vaše síť povoluje připojení pomocí protokolu WebSocket k *. instances.azureml.net a *. instances.azureml.ms. 

* Když je instance služby COMPUTE nasazená v pracovním prostoru privátního propojení, dá se k ní dostat jenom z virtuální sítě. Pokud používáte vlastní soubor DNS nebo hostitele, přidejte položku pro <název instance>. <region> . instances.azureml.ms s privátní IP adresou privátního koncového bodu v pracovním prostoru. Další informace najdete v článku o [vlastním serveru DNS](https://docs.microsoft.com/azure/machine-learning/how-to-custom-dns?tabs=azure-cli) .
    
## <a name="next-steps"></a>Další kroky

* [Spuštění prvního experimentu](tutorial-1st-experiment-sdk-train.md)
* [Zálohování úložiště souborů pomocí snímků](../storage/files/storage-snapshots-files.md)
* [Práce v zabezpečených prostředích](https://docs.microsoft.com/azure/machine-learning/how-to-secure-training-vnet#compute-instance)
