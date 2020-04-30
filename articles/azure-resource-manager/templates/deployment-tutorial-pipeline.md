---
title: Průběžná integrace s využitím Azure Pipelines
description: Naučte se průběžně sestavovat, testovat a nasazovat šablony Azure Resource Manager.
ms.date: 04/22/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d1c56ce913a1b63bab90f5dd5aaada382abbf493
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82084324"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Kurz: průběžná integrace šablon Azure Resource Manager s Azure Pipelines

V [předchozím kurzu](./deployment-tutorial-linked-template.md)nasadíte propojenou šablonu.  V tomto kurzu se naučíte, jak používat Azure Pipelines k průběžnému sestavování a nasazování projektů šablon Azure Resource Manager.

Azure DevOps poskytuje Vývojářské služby pro podporu týmů pro plánování práce, spolupráci na vývoji kódu a sestavování a nasazování aplikací. Vývojáři můžou pracovat v cloudu pomocí Azure DevOps Services. Azure DevOps poskytuje integrovanou sadu funkcí, ke kterým můžete přistupovat prostřednictvím webového prohlížeče nebo klienta IDE. Kanál Azure je jednou z těchto funkcí. Azure Pipelines je plně funkční služba pro průběžnou integraci (CI) a průběžné doručování (CD). Funguje s vaším preferovaným poskytovatelem Git a může se nasadit do většiny hlavních cloudových služeb. Potom můžete automatizovat sestavení, testování a nasazení kódu pro Microsoft Azure, Google Cloud Platform nebo Amazon Web Services.

> [!NOTE]
> Vyberte název projektu. Když projdete kurz, nahraďte **AzureRmPipeline** názvem vašeho projektu.
> Tento název projektu se používá ke generování názvů prostředků.  Jedním z prostředků je účet úložiště. Názvy účtů úložiště musí mít délku 3 až 24 znaků a musí obsahovat jenom číslice a malá písmena. Název musí být jedinečný. V šabloně je název účtu úložiště název projektu s připojeným úložištěm a název projektu musí být dlouhý 3 až 11 znaků. Proto název projektu musí splňovat požadavky na název účtu úložiště a má méně než 11 znaků.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava úložiště GitHub
> * Vytvoření projektu Azure DevOps
> * Vytvoření kanálu Azure
> * Ověření nasazení kanálu
> * Aktualizujte šablonu a znovu nasaďte
> * Vyčištění prostředků

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* **Účet GitHubu**, kde ho použijete k vytvoření úložiště pro šablony. Pokud ho ještě nemáte, můžete si [ho vytvořit zdarma](https://github.com). Další informace o použití úložišť GitHub najdete v tématu [sestavení úložišť GitHubu](/azure/devops/pipelines/repos/github).
* **Nainstalujte Git**. Tato instrukce kurzu používá *Git bash* nebo *git shell*. Pokyny najdete v tématu [instalace Gitu]( https://www.atlassian.com/git/tutorials/install-git).
* **Organizace Azure DevOps**. Pokud ho ještě nemáte, můžete si ho vytvořit zdarma. Viz [vytvoření organizace nebo kolekce projektů]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* volitelné **Visual Studio Code s rozšířením nástrojů Správce prostředků Tools**. Pokud [chcete vytvořit Azure Resource Manager šablony](use-vs-code-to-create-template.md), přečtěte si téma použití Visual Studio Code.

## <a name="prepare-a-github-repository"></a>Příprava úložiště GitHub

GitHub slouží k uložení zdrojového kódu projektu, včetně šablon Správce prostředků. Další podporovaná úložiště najdete v tématu [úložiště podporovaná službou Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops).

### <a name="create-a-github-repository"></a>Vytvoření úložiště GitHub

Pokud nemáte účet GitHubu, přečtěte si téma [požadavky](#prerequisites).

1. Přihlaste se k [GitHubu](https://github.com).
1. V pravém horním rohu vyberte svůj obrázek účtu a pak vyberte **svoje úložiště**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines vytvoření úložiště GitHubu](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Vyberte **nové**a zelené tlačítko.
1. Do **název úložiště**zadejte název úložiště.  Například **AzureRmPipeline-úložiště**. Nezapomeňte nahradit libovolný z **AzureRmPipeline** názvem vašeho projektu. V rámci tohoto kurzu můžete vybrat buď **veřejné** , nebo **soukromé** . A pak vyberte **vytvořit úložiště**.
1. Zapište adresu URL. Adresa URL úložiště má následující formát: ** https://github.com/[YourAccountName]/[YourRepositoryName]**.

Toto úložiště se označuje jako *vzdálené úložiště*. Každý z vývojářů stejného projektu může klonovat své vlastní *místní úložiště*a sloučit změny do vzdáleného úložiště.

### <a name="clone-the-remote-repository"></a>Klonování vzdáleného úložiště

1. Otevřete prostředí Git nebo Git bash.  Viz [Požadavky](#prerequisites).
1. Ověřte, jestli je aktuální složka **GitHub**.
1. Spusťte následující příkaz:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Nahraďte **[YourAccountName]** názvem vašeho účtu GitHub a nahraďte **[YourGitHubRepositoryName]** názvem vašeho úložiště, který jste vytvořili v předchozím postupu.

Složka **CreateWebApp** je složka, ve které je šablona uložena. Příkaz **PWD** zobrazuje cestu ke složce. Cesta je místo, kam šablonu uložíte, do následujícího postupu.

### <a name="download-a-quickstart-template"></a>Stažení šablony pro rychlé zprovoznění

Místo vytváření šablon můžete stáhnout šablony a uložit je do složky **CreateWebApp** .

* Hlavní Šablona:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* Odkazovaná Šablona:https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Název složky i názvy souborů se používají tak, jak jsou v kanálu.  Pokud tyto názvy změníte, je nutné aktualizovat názvy používané v kanálu.

### <a name="push-the-template-to-the-remote-repository"></a>Vložení šablony do vzdáleného úložiště

Azuredeploy. JSON se přidal do místního úložiště. V dalším kroku nahrajete šablonu do vzdáleného úložiště.

1. Otevřete *prostředí Git* nebo *Git bash*, pokud není otevřené.
1. Změňte adresář na složku CreateWebApp v místním úložišti.
1. Ověřte, zda je soubor **azuredeploy. JSON** ve složce.
1. Spusťte následující příkaz:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin master
    ```

    Může se zobrazit upozornění na LF. Upozornění můžete ignorovat. **Hlavní** větev je hlavní.  Pro každou aktualizaci obvykle vytvoříte větev. Pro zjednodušení tohoto kurzu budete používat hlavní větev přímo.
1. Přejděte do úložiště GitHub z prohlížeče.  Adresa URL je ** https://github.com/[YourAccountName]/[YourGitHubRepository]**. V rámci složky se zobrazí složka **CreateWebApp** a tři soubory.
1. Vyberte **linkedStorageAccount. JSON** a otevřete šablonu.
1. Vyberte tlačítko **nezpracované** . Adresa URL začíná na **raw.githubusercontent.com**.
1. Zkopírujte adresu URL.  Tuto hodnotu musíte zadat při konfiguraci kanálu později v tomto kurzu.

Zatím jste vytvořili úložiště GitHub a nahráli šablony do úložiště.

## <a name="create-a-devops-project"></a>Vytvoření projektu DevOps

Aby bylo možné přejít k dalšímu postupu, je nutné, aby byla DevOps organizace.  Pokud ho nemáte, přečtěte si téma [požadavky](#prerequisites).

1. Přihlaste se k [Azure DevOps](https://dev.azure.com).
1. Vyberte DevOps organizaci vlevo.

    ![Azure Resource Manager Azure DevOps Azure Pipelines vytvoření projektu Azure DevOps](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Vyberte **Nový projekt**. Pokud nemáte žádné projekty, otevře se stránka vytvořit projekt automaticky.
1. Zadejte následující hodnoty:

    * **Název projektu**: zadejte název projektu. Můžete použít název projektu, který jste vybrali na začátku kurzu.
    * **Správa verzí**: vyberte **Git**. Pro zobrazení **správy verzí**možná budete muset rozbalit možnost **Upřesnit** .

    Pro ostatní vlastnosti použijte výchozí hodnotu.
1. Vyberte **Vytvořit**.

Vytvořte připojení služby, které se používá k nasazení projektů do Azure.

1. V dolní části nabídky vlevo vyberte **nastavení projektu** .
1. V části **kanály**vyberte **připojení služby** .
1. Vyberte možnost **nové připojení služby**, vyberte možnost **Azure Resource Manager**a potom vyberte možnost **Další**.
1. Vyberte **instanční objekt**a pak vyberte **Další**.
1. Zadejte následující hodnoty:

    * **Úroveň oboru**: vyberte **předplatné**.
    * **Předplatné**: vyberte své předplatné.
    * **Skupina prostředků**: ponechte pole prázdné.
    * **Název připojení**: zadejte název připojení. Například **AzureRmPipeline-připojeno**. Poznamenejte si tento název. při vytváření kanálu budete potřebovat název.
    * **Udělte přístupová oprávnění všem kanálům**. Vyberte
1. Vyberte **Uložit**.

## <a name="create-a-pipeline"></a>Vytvoření kanálu

Až do této chvíle jste dokončili následující úlohy.  Pokud přeskočíte předchozí oddíly, protože jste obeznámeni s GitHubem a DevOps, musíte před pokračováním dokončit úkoly.

* Vytvořte úložiště GitHub a uložte šablony do složky **CreateWebApp** v úložišti.
* Vytvořte projekt DevOps a vytvořte připojení služby Azure Resource Manager.

Vytvoření kanálu s krokem pro nasazení šablony:

1. V nabídce vlevo vyberte **kanály** .
1. Vyberte **Nový kanál**.
1. Na kartě **Connect** (Připojit) vyberte **GitHub**. Pokud se zobrazí výzva, zadejte svoje přihlašovací údaje k GitHubu a pak postupujte podle pokynů. Pokud se zobrazí následující obrazovka, vyberte **jenom možnost vybrat úložiště**a ověřte, jestli je vaše úložiště v seznamu, než vyberete **schválit & nainstalovat**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines vybrat jenom úložiště](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Na kartě **Vybrat** vyberte své úložiště.  Výchozí název je **[YourAccountName]/[YourGitHubRepositoryName]**.
1. Na kartě **Konfigurace** vyberte **Počáteční kanál**. Zobrazuje soubor kanálu **Azure-Pipelines. yml** se dvěma kroky skriptu.
1. Odstraňte dva kroky skriptu ze souboru YML.
1. Přesuňte kurzor na řádek za **kroků:**.
1. Vyberte **Zobrazit pomocníka** na pravé straně obrazovky a otevřete podokno **úlohy** .
1. Vyberte **nasazení šablony ARM**.
1. Zadejte následující hodnoty:

    * **deploymentScope**: vyberte **skupinu prostředků**... Další informace o oborech najdete v tématu [obory nasazení](deploy-rest.md#deployment-scope).
    * **Azure Resource Manager připojení**: vyberte název připojení služby, který jste vytvořili dříve.
    * **Předplatné**: Zadejte ID cílového předplatného.
    * **Akce**: vyberte akci **vytvořit nebo aktualizovat skupinu prostředků** 2 akce-1. Pokud je zadaný název nové skupiny prostředků, vytvořte skupinu prostředků. odst. Nasaďte zadanou šablonu.
    * **Skupina prostředků**: zadejte nový název skupiny prostředků. Například **AzureRmPipeline-RG**.
    * **Umístění**: vyberte umístění pro skupinu prostředků, například **střed USA**.
    * **Umístění šablony**: vyberte **propojený artefakt**, což znamená, že úloha hledá soubor šablony přímo z připojeného úložiště.
    * **Šablona**: zadejte **CreateWebApp/azuredeploy. JSON**. Pokud jste změnili název složky a název souboru, musíte tuto hodnotu změnit.
    * **Parametry šablony**: nechte toto pole prázdné. Hodnoty parametrů určíte v parametrech * * přepsat šablony.
    * **overrideParameters**: zadejte **-ProjectName [EnterAProjectName]-linkedTemplateUri [EnterTheLinkedTemplateURL]**. Nahraďte název projektu a adresu URL propojené šablony. Adresa URL propojené šablony je to, co jste napsali na konci [vytváření úložiště GitHubu](#create-a-github-repository).
    * **Režim nasazení**: vyberte **přírůstkové**.
    * **Název nasazení**: zadejte **DeployPipelineTemplate**. Než uvidíte **název nasazení**, vyberte **Upřesnit** .

    ![Krok Azure Resource Manager Azure DevOps Azure Pipelines](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Vyberte **Přidat**.

    Další informace o úloze najdete v tématu [Úloha nasazení skupiny prostředků Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)a [úloha nasazení šablony Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) .

    Soubor YML by měl vypadat nějak takto:

    ![Azure Resource Manager Azure DevOps Azure Pipelines YAML](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Vyberte **Uložit a spustit**.
1. V podokně **Uložit a spustit** vyberte **Uložit a spustit** znovu. Kopie souboru YAML se uloží do připojeného úložiště. Soubor YAML můžete zobrazit tak, že přejdete do úložiště.
1. Ověřte, že se kanál úspěšně spustil.

    ![Azure Resource Manager Azure DevOps Azure Pipelines YAML](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Ověření nasazení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Otevřete skupinu prostředků. Název je to, co jste zadali v souboru YAML kanálu.  Uvidíte, že se vytvořil jeden účet úložiště.  Název účtu úložiště začíná na **Storu**.
1. Vyberte název účtu úložiště, který chcete otevřít.
1. Vyberte **Vlastnosti**. Všimněte si, že **replikace** je **místně redundantní úložiště (LRS)**.

## <a name="update-and-redeploy"></a>Aktualizace a opětovné nasazení

Když aktualizujete šablonu a nahrajete změny do vzdáleného úložiště, kanál v tomto případě automaticky aktualizuje prostředky a účet úložiště.

1. Otevřete **linkedStorageAccount. JSON** z místního úložiště v Visual Studio Code nebo v libovolném textovém editoru.
1. Aktualizujte **defaultValue** hodnotu DefaultValue **storageAccountType** na **Standard_GRS**. Viz následující snímek obrazovky:

    ![Azure Resource Manager Azure DevOps Azure Pipelines Update YAML](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Uložte změny.
1. Dodejte změny do vzdáleného úložiště spuštěním následujících příkazů z Gitu bash/shell.

    ```bash
    git pull origin master
    git add .
    git commit -m "Update the storage account type."
    git push origin master
    ```

    První příkaz (pull) synchronizuje místní úložiště se vzdáleným úložištěm. Soubor YAML kanálu se přidal jenom do vzdáleného úložiště. Spuštění příkazu PULL stáhne kopii souboru YAML do místní větve.

    Čtvrtý příkaz (push) nahraje revidovaný soubor linkedStorageAccount. JSON do vzdáleného úložiště. Když se hlavní větev vzdáleného úložiště aktualizuje, kanál se znovu aktivuje.

Chcete-li ověřit změny, můžete zkontrolovat vlastnost replikace účtu úložiště.  Viz [Ověření nasazení](#verify-the-deployment).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

Možná budete chtít odstranit i úložiště GitHub a projekt Azure DevOps.

## <a name="next-steps"></a>Další kroky

Gratulujeme, dokončili jste tento Správce prostředků kurz nasazení šablony. Dejte nám vědět, pokud máte komentáře a návrhy v části věnované zpětné vazbě. Děkujeme!
Jste připraveni přejít k pokročilejším koncepcím o šablonách. V dalším kurzu se dozvíte víc o používání Referenční dokumentace k šablonám, které vám pomůžou definovat prostředky k nasazení.

> [!div class="nextstepaction"]
> [Využití referenčních informací k šablonám](./template-tutorial-use-template-reference.md)
