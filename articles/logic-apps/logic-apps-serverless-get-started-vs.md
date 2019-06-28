---
title: Vytvářejte aplikace bez serveru s Azure Logic Apps a Azure Functions v sadě Visual Studio
description: Vytvořit, nasadit a spravovat vaši první aplikaci bez serveru s Azure Logic Apps a Azure Functions v sadě Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: e51a3f9971006a0b50cec1abdc5e955d06c23466
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295853"
---
# <a name="build-your-first-serverless-app-with-azure-logic-apps-and-azure-functions---visual-studio"></a>Vytvořte svoji první aplikaci bez serveru s Azure Logic Apps a Azure Functions – Visual Studio

Můžete rychle vyvíjet a nasazení cloudových aplikací pomocí nástroje bez serverů a možnosti v Azure jako [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [Azure Functions](../azure-functions/functions-overview.md). Tento článek ukazuje, jak začít vytvářet aplikace bez serveru, který používá aplikace logiky, která volá funkci Azure, v sadě Visual Studio. Další informace o řešeních bez serveru v Azure najdete v tématu [Azure bez serveru s využitím Functions a Logic Apps](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Požadavky

K sestavení aplikace bez serveru v sadě Visual Studio, budete potřebovat tyto položky:

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Pokud je ještě nemáte, stáhněte a nainstalujte tyto nástroje:

  * [Edice sady Visual Studio. 2019 2017 nebo 2015 – Community nebo vyšší](https://aka.ms/download-visual-studio). 
  V tomto kurzu se používá sada Visual Studio Community 2017, která je zdarma.

    > [!IMPORTANT]
    > Když instalujete Visual Studio 2019 nebo 2017, ujistěte se, že jste vybrali **vývoj pro Azure** pracovního vytížení.

  * [Microsoft Azure SDK pro .NET (2.9.1 nebo novější)](https://azure.microsoft.com/downloads/). 
  Další informace o sadě [Azure SDK pro .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Azure Logic Apps Tools pro verzi sady Visual Studio, které chcete:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Azure Logic Apps Tools můžete stáhnout a nainstalovat přímo z Visual Studio Marketplace nebo si můžete přečíst, [jak toto rozšíření nainstalovat v rámci sady Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Po dokončení instalace nezapomeňte sadu Visual Studio restartovat.

  * [Nástroje Azure Functions Core](https://www.npmjs.com/package/azure-functions-core-tools) pro místní ladění funkce

* Přístup k webu při používání vloženého Návrháře aplikace logiky

  Návrhář k vytváření prostředků v Azure a ke čtení vlastností a dat z konektorů ve vaší aplikaci logiky vyžaduje připojení k internetu. 
  Pokud například použijete konektor Dynamics CRM Online, návrhář zkontroluje ve vaší instanci CRM dostupné výchozí a vlastní vlastnosti.

## <a name="create-resource-group-project"></a>Vytvoření projektu skupiny prostředků

Pokud chcete začít, vytvořte [projekt skupiny prostředků Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) pro vaši aplikaci bez serveru. V Azure vytvořte prostředky v rámci skupiny prostředků, což je logická kolekce, které používáte pro organizování a správě a nasazování prostředků pro celou aplikaci jako jeden prostředek. Pro aplikace bez serveru v Azure vaše skupina prostředků obsahuje prostředky pro Azure Logic Apps a Azure Functions. Další informace o [skupinách prostředků a prostředcích Azure](../azure-resource-manager/resource-group-overview.md).

1. Spusťte sadu Visual Studio a přihlaste se pomocí svého účtu Azure.

1. V nabídce **Soubor** vyberte **Nový** > **Projekt**.

   ![Vytvoření nového projektu v sadě Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. V části **Nainstalováno**, vyberte **Visual C#** nebo **Visual Basic**. Vyberte **Cloud** > **Skupina prostředků Azure**.

   > [!NOTE]
   > Pokud **cloudu** kategorie nebo **skupiny prostředků Azure** projekt neexistuje, ujistěte se, že jste nainstalovali sadu Azure SDK pro Visual Studio.

   Pokud používáte Visual Studio 2019, postupujte podle těchto kroků:

   1. V **vytvořte nový projekt** vyberte **skupiny prostředků Azure** šablona projektu pro buď Visual C# nebo Visual Basic a zvolte **Další**.

   1. Zadejte název skupiny prostředků Azure, které chcete používat a další informace o projektu. Jakmile budete hotoví, vyberte **Vytvořit**.

1. Dejte projektu název a umístění a klikněte na tlačítko **OK**.

   Visual Studio vás vyzve, abyste ze seznamu šablon vyberte šablonu. 
   Tento příklad používá šablonu Azure pro rychlý start, tak můžete vytvářet aplikace bez serveru, který obsahuje aplikace logiky a volání funkce Azure.

   > [!TIP]
   > Ve scénářích, kde nechcete k nasazení řešení do skupiny prostředků Azure, můžete použít prázdnou **aplikace logiky** šablony, které právě vytvoří prázdný logiku aplikace.

1. Z **zobrazit šablony z tohoto umístění** seznamu vyberte **Quickstart pro Azure (github.com/Azure/azure-quickstart-templates)** .

1. Do vyhledávacího pole zadejte jako filtr "aplikace logiky". Ve výsledcích vyberte tuto šablonu: **101-logic-app-and-function-app**

   ![Vyberte šablonu pro rychlý start Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio vytvoří a otevře řešení pro projekt skupiny prostředků. 
   Šablony Azure pro rychlý start jste vybrali vytvoří šablonu nasazení s názvem `azuredeploy.json` uvnitř projektu skupiny prostředků. Tato šablona nasazení obsahuje definici pro jednoduchou aplikaci logiky, která aktivuje na požadavek HTTP, volá funkci Azure a vrátí výsledek jako odpověď HTTP.

   ![Nové řešení bez serveru](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Vaše řešení v dalším kroku musí nasadit do Azure, aby bylo možné otevřít šablonu nasazení a zkontrolujte prostředky pro vaši aplikaci bez serveru.

## <a name="deploy-your-solution"></a>Nasazení řešení

Před spuštěním aplikace logiky pomocí návrháře aplikace logiky v sadě Visual Studio, musí mít skupinu prostředků Azure, který je již nasazené v Azure. Návrhář poté můžete vytvořit připojení k prostředkům a službám ve vaší aplikaci logiky. Pro tento úkol Nasaďte řešení na webu Azure portal ze sady Visual Studio.

1. V Průzkumníku řešení v místní nabídce projektu prostředků, vyberte **nasadit** > **nový**.

   ![Vytvořte nové nasazení pro skupinu prostředků](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Pokud ještě není vybraná, vyberte předplatné a skupinu prostředků, do které chcete nasadit. Zvolte **nasazení**.

   ![Nastavení nasazení](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Pokud **upravit parametry** pole se zobrazí, zadejte název prostředku pro aplikaci logiky a aplikace Azure function app na nasazení a nastavení následně uložte. Ujistěte se, že používáte globálně jedinečný název pro vaši aplikaci function app.

   ![Zadejte názvy pro vaše aplikace logiky a aplikace function app](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Když Visual Studio spustí nasazení tak, aby vaše zadané skupině prostředků, se zobrazí stav nasazení vašeho řešení v sadě Visual Studio **výstup** okna. 
   Po dokončení nasazení vaše aplikace logiky je za provozu na webu Azure Portal.

## <a name="edit-logic-app-in-visual-studio"></a>Upravit aplikace logiky v sadě Visual Studio

Teď, když vaše řešení je nasazená do vaší skupiny prostředků, otevřete aplikaci logiky pomocí návrháře aplikace logiky tak, že můžete upravit a změnit svou aplikaci logiky.

1. V Průzkumníku řešení od `azuredeploy.json` místní nabídky souboru, vyberte **otevřít pomocí návrháře aplikace logiky**.

   !["Azuredeploy.json" Otevřít v návrháři aplikace logiky](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Pokud tento příkaz nemáte k dispozici v aplikaci Visual Studio 2019, zkontrolujte, že máte nejnovější aktualizace pro sadu Visual Studio.

1. Po **vlastnosti aplikace logiky** se zobrazí okno a pokud ještě není vybraná, v části **předplatné**, vyberte své předplatné Azure. V části **skupiny prostředků**, vyberte skupinu prostředků a umístění, kam jste nasadili řešení a pak zvolte **OK**.

   ![Vlastnosti aplikace logiky](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Po otevře se návrhář aplikace logiky, můžete pokračovat v přidávání kroky nebo změnit pracovní postup a uložte tyto aktualizace.

   ![Aplikace logiky otevřen v návrháři aplikace logiky](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-azure-functions-project"></a>Vytvoření projektu Azure Functions

K vytvoření projektu funkce a funkce s použitím jazyka JavaScript, Python, F#, prostředí PowerShell, Batch nebo prostředí Bash, postupujte podle kroků v článku, [pracovat s Azure Functions Core Tools](../azure-functions/functions-run-local.md). Vyvíjet funkce Azure pomocí jazyka C# ve vašem řešení, můžete použít knihovnu tříd C# pomocí následujících kroků v článku, [publikovat jako aplikaci Function App knihovny tříd .NET](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Nasazení služby functions v sadě Visual Studio

Nasazení šablony nasadí jakékoli Azure functions, které máte ve vašem řešení z úložiště Git, která je zadána proměnné v `azuredeploy.json` souboru. Pokud vytvoříte a vytvoření projektu funkce ve vašem řešení, můžete zkontrolovat tento projekt do správy zdrojového kódu Gitu, například GitHub nebo Azure DevOps a pak aktualizujte `repo` proměnné tak, aby šablona nasadí funkce Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Správa aplikací logiky a zobrazení historie spuštění

Pro logic apps již nasazené v Azure stále můžete upravit, spravovat, zobrazte historii spuštění a zakázat tyto aplikace ze sady Visual Studio.

1. Z **zobrazení** nabídky v sadě Visual Studio, otevřete **Průzkumníka cloudu**.

1. V části **Všechna předplatná**, vyberte předplatné Azure spojené s logic apps, kterou chcete spravovat a zvolte **použít**.

1. V části **Logic Apps**, vyberte svou aplikaci logiky. Tuto aplikaci místní nabídce vyberte **otevřít pomocí editoru aplikace logiky**.

   > [!TIP]
   > Pokud tento příkaz nemáte k dispozici v aplikaci Visual Studio 2019, zkontrolujte, že máte nejnovější aktualizace pro sadu Visual Studio.

Teď si můžete stáhnout aplikaci logiky už byla publikována do projektu skupiny prostředků. Takže i když je možné, že jste začali aplikace logiky na webu Azure Portal, můžete stále importovat a spravovat aplikace v sadě Visual Studio. Další informace najdete v tématu [Správa aplikací logiky pomocí sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Další postup

* [Správa aplikací logiky s využitím sady Visual Studio](manage-logic-apps-with-visual-studio.md)
