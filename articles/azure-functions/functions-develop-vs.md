---
title: Vývoj pomocí sady Visual Studio Azure Functions | Dokumentace Microsoftu
description: Zjistěte, jak vyvíjet a testovat funkce Azure s využitím Azure Functions Tools pro Visual Studio 2019.
services: functions
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: glenga
ms.openlocfilehash: 8ed3b42c61456f110925e34473dbb326dafc1b80
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447717"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Vývoj Azure Functions pomocí sady Visual Studio  

Nástroje Azure Functions je rozšířením pro Visual Studio, která umožňuje vývoj, testování a nasazení C# funkce do Azure. Pokud toto prostředí je první s využitím Azure Functions, další informace najdete v [Úvod do služby Azure Functions](functions-overview.md).

Nástroje Azure Functions nabízí následující výhody: 

* Upravit, vytvářet a spouštět službu functions na místním počítači pro vývoj. 
* Projekt Azure Functions publikujte přímo do Azure. 
* Chcete-li deklarovat funkci vazby přímo v kódu jazyka C# namísto zachování samostatné function.json pro vazby definice použijte atributy WebJobs.
* Vývoj a nasazení předem kompilovaných funkcí jazyka C#. Předem splněny functions poskytuje lepší studený start výkonu než založených na skriptech funkcí jazyka C#. 
* Kód vaší funkce v jazyce C# přitom má všechny výhody vývoj sady Visual Studio. 

Tento článek obsahuje podrobnosti o tom, jak používat Azure Functions Tools for Visual Studio 2019 k vývoji C# funkce a publikovat je do Azure. Předtím, než se pustíte do čtení tohoto článku, měli byste pokračovat [funkce Rychlý start pro Visual Studio](functions-create-your-first-function-visual-studio.md). 

> [!IMPORTANT]
> Nekombinujte místní vývoj pomocí portálu ve stejné aplikaci function app. Při publikování z místní projekt aplikace function app, procesu nasazení přepíše všechny funkce, které jste vytvořili na portálu.

## <a name="prerequisites"></a>Požadavky

Nástroje Azure Functions je součástí sady funkcí vývoj pro Azure [Visual Studio 2017](https://www.visualstudio.com/vs/), nebo novější. Ujistěte se, že zahrnete **vývoj pro Azure** úlohy v instalaci sady Visual Studio 2019:

![Nainstalovat Visual Studio 2019 s úlohou vývoj pro Azure](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Ujistěte se, že Visual Studio je aktuální a že používáte [nejnovější verzi](#check-your-tools-version) nástrojů Azure Functions.

### <a name="azure-resources"></a>Prostředky Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Další materiály, které potřebujete, jako je například účet služby Azure Storage jsou vytvořeny v rámci vašeho předplatného během procesu publikování.

### <a name="check-your-tools-version"></a>Zkontrolujte verzi nástroje

1. Z **nástroje** nabídce zvolte **rozšíření a aktualizace**. Rozbalte **nainstalováno** > **nástroje** a zvolte **Azure Functions and Web Jobs Tools**.

    ![Ověření verze nástrojů funkce](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

2. Mějte na paměti nainstalované **verze**. Můžete porovnat tato verze na nejnovější verzi, uvedené [v poznámkách k verzi](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

3. Pokud je vaše verze starší, aktualizujte své nástroje v sadě Visual Studio, jak je znázorněno v následující části.

### <a name="update-your-tools"></a>Aktualizovat vaše nástroje

1. V **rozšíření a aktualizace** dialogového okna, rozbalte **aktualizace** > **Visual Studio Marketplace**, zvolte **Azure Functions and Web Jobs Tools**  a vyberte **aktualizace**.

    ![Aktualizovat verzi Functions nástroje](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

2. Po stažení aktualizace nástroje zavřete sadu Visual Studio na trigger nástroje aktualizace pomocí instalátor VSIX.

3. V instalačním programu, zvolte **OK** spuštění a pak **změnit** aktualizace nástrojů. 

4. Po dokončení aktualizace, zvolte **Zavřít** a restartujte aplikaci Visual Studio.

## <a name="create-an-azure-functions-project"></a>Vytvoření projektu Azure Functions

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Šablona projektu vytvoří projektu v jazyce C#, nainstaluje `Microsoft.NET.Sdk.Functions` balíčku NuGet a nastaví cílovou architekturu. Funkce 1.x zaměřené na rozhraní .NET Framework a funkce 2.x cíle .NET Standard. Nový projekt má následující soubory:

* **host.json**: Umožňuje konfigurovat funkce hostitele. Tato nastavení platí i při spuštění místně i v Azure. Další informace najdete v tématu [referenční materiály k host.json](functions-host-json.md).

* **local.settings.json**: Udržuje nastavení používaná při místním spuštění funkce. Tato nastavení nejsou použity při spuštění v Azure. Další informace najdete v tématu [souboru místní nastavení](#local-settings-file).

    >[!IMPORTANT]
    >Protože souboru local.settings.json může obsahovat tajné kódy, musíte ho vyloučit ze správy zdrojových kódů pro váš projekt. **Kopírovat do výstupního adresáře** nastavení pro tento soubor by měl vždy být **kopírovat, pokud je novější**. 

Další informace najdete v tématu [projekt knihovny tříd funkce](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Nastavení v local.settings.json nejsou automaticky nahraje při publikování tohoto projektu. Pokud chcete mít jistotu, že tato nastavení také existují ve vaší aplikaci function app v Azure, musíte nahrát je po publikování projektu. Další informace najdete v tématu [fungovat nastavení aplikace](#function-app-settings).

Hodnoty v **ConnectionStrings** se nikdy publikováno.

Hodnoty nastavení aplikace funkcí můžete číst také ve vašem kódu jako proměnné prostředí. Další informace najdete v tématu [proměnné prostředí](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-the-project-for-local-development"></a>Konfigurace projektu pro místní vývoj

Modul runtime služby Functions interně používá účet Azure Storage. Pro aktivaci všech typů jiných než HTTP a webhooky, musíte nastavit **Values.AzureWebJobsStorage** klíč platný připojovací řetězec účtu úložiště Azure. Můžete také použít aplikaci function app [emulátoru úložiště Azure](../storage/common/storage-use-emulator.md) pro **AzureWebJobsStorage** nastavení připojení, který je vyžadováno v projektu. Pokud chcete použít emulátor, nastavte hodnotu **AzureWebJobsStorage** k `UseDevelopmentStorage=true`. Toto nastavení změňte na připojení k skutečného úložiště před nasazením.

Chcete-li nastavit připojovací řetězec účtu úložiště:

1. V sadě Visual Studio, otevřete **Průzkumníka cloudu**, rozbalte **účtu úložiště** > **svůj účet úložiště**a pak vyberte **vlastnosti**a zkopírujte **primární připojovací řetězec** hodnotu.

2. Ve vašem projektu otevřete soubor local.settings.json a nastavte hodnotu **AzureWebJobsStorage** zkopírujete připojovací řetězec klíče.

3. Opakujte předchozí krok a přidejte jedinečné klíče **hodnoty** pole pro všechna připojení, které vyžadují vaše funkce.

## <a name="add-a-function-to-your-project"></a>Přidání funkce do vašeho projektu

V předem zkompilované funkce jsou definovány vazby používá funkci použití atributů v kódu. Při použití nástrojů Azure Functions k vytvoření funkce z dodané šablony tyto atributy jsou použity pro vás. 

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na uzel projektu a vyberte **Přidat** > **Nová položka**. Vyberte **funkce Azure Functions**, zadejte **název** pro třídu a klikněte na tlačítko **přidat**.

2. Zvolte aktivační událost, nastavte vlastnosti vazby a klikněte na tlačítko **vytvořit**. Následující příklad ukazuje nastavení při vytváření fronty úložiště aktivaci funkce. 

    ![Vytvoření funkce aktivované frontou](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    V tomto příkladu aktivační události používá připojovací řetězec s klíčem s názvem **QueueStorage**. Tato nastavení připojovacího řetězce musí být definován v [souboru local.settings.json](functions-run-local.md#local-settings-file).

3. Prozkoumejte nově přidané třídy. Zobrazí se statickou **spustit** metody s atributem **FunctionName** atribut. Tento atribut označuje, že metoda je vstupním bodem pro funkci.

    Například následující třídy C# představuje základní funkce úložiště aktivované frontou:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

    Atribut specifické pro vazbu se použije pro každý parametr vazby zadaný pro metodu vstupního bodu. Atribut přijímá informace o vazbě jako parametry. V předchozím příkladu má první parametr **QueueTrigger** atribut, která funkce aktivované frontou. Název fronty a nastavení název připojovacího řetězce jsou předány jako parametry **QueueTrigger** atribut. Další informace najdete v tématu [vazby úložiště front Azure pro službu Azure Functions](functions-bindings-storage-queue.md#trigger---c-example).

Výše uvedený postup slouží k přidání dalších funkcí pro váš projekt aplikace funkcí. Každá funkce v projektu může mít jinou aktivační událost, ale funkce musí mít přesně jeden trigger. Další informace najdete v tématu [aktivace Azure Functions a vazby koncepty](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Přidat vazby

Stejně jako u triggerů, vstupní a výstupní vazby jsou přidány do funkce jako atributů vazby. Přidejte vazby na funkce takto:

1. Ujistěte se, že máte [nakonfigurovali projekt pro místní vývoj](#configure-the-project-for-local-development).

2. Přidejte si odpovídající balíček NuGet rozšíření pro konkrétní vazbu. Další informace najdete v tématu [místní C# vývoj pomocí sady Visual Studio](./functions-bindings-register.md#local-csharp) v článku triggerů a vazeb. Požadavky balíčku NuGet specifické pro vazbu se nacházejí v článku odkaz pro vazbu. Například vyhledejte balíček požadavky pro trigger služby Event Hubs v [článku vazby služby Event Hubs](functions-bindings-event-hubs.md).

3. Pokud jsou nastavení aplikace, které potřebuje vazby, přidejte je do **hodnoty** kolekce [místní nastavení souboru](functions-run-local.md#local-settings-file). Tyto hodnoty se používají, když je funkce spuštěná místně. Když je funkce spuštěná v aplikaci function app v Azure, [fungovat nastavení aplikace](#function-app-settings) se používají.

4. Přidejte atribut příslušnou datovou vazbu do podpisu metody. V následujícím příkladu zpráva fronty aktivuje funkci a výstupní vazby vytvoří nové zprávy fronty se stejný text do jiné fronty.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   Připojení k frontě úložiště se získává z `AzureWebJobsStorage` nastavení. Další informace najdete v článku odkaz pro konkrétní vazbu. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Testování funkcí

Nástroje Azure Functions Core umožňují spouštět projekt Azure Functions na místním počítači pro vývoj. K instalaci těchto nástrojů budete vyzváni při prvním spuštění funkce ze sady Visual Studio.

Pokud chcete funkci otestovat, stiskněte F5. Po výzvě přijměte požadavek ze sady Visual Studio na stažení a instalaci nástrojů Azure Functions Core (CLI). Může být také potřeba povolit výjimku brány firewall, aby nástroje mohly zpracovávat požadavky HTTP.

Spuštění k projektu můžete otestovat kód, jako byste testovali nasazenou funkci. Další informace najdete v tématu [strategie pro testování kódu ve službě Azure Functions](functions-test-a-function.md). Při spuštění v režimu ladění, zarážky v sadě Visual Studio podle očekávání. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Další informace o používání nástrojů Azure Functions Core najdete v tématu [kódu a testování Azure functions místně](functions-run-local.md).

## <a name="publish-to-azure"></a>Publikování do Azure

Při publikování ze sady Visual Studio, se používají jedním ze dvou způsobů nasazení:

* [Webu nasadit](functions-deployment-technologies.md#web-deploy-msdeploy): balíčky a nasadí aplikace Windows k jakémukoli serveru služby IIS.
* [Zazipovat Deploy with Run-z-Package povolené](functions-deployment-technologies.md#zip-deploy): doporučují pro nasazení Azure Functions.

Použijte následující postup k publikování projektu do aplikace function app v Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Nastavení aplikace funkcí

Všechna nastavení, které jste přidali v kroku local.settings.json musí být rovněž přidán do aplikace function app v Azure. Tato nastavení nejsou automaticky nahraje při publikování tohoto projektu.

Nejjednodušší způsob, jak nahrát požadovaná nastavení do vaší aplikace funkcí v Azure je použít **spravovat nastavení aplikace...**  odkaz, který se zobrazí po úspěšném publikování projektu.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Zobrazí se **nastavení aplikace** dialogové okno pro aplikaci function app, ve kterém můžete přidat nové nastavení aplikace nebo upravte stávající.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Místní** představuje hodnotu nastavení v souboru local.settings.json a **vzdálené** je aktuální nastavení aplikace function App v Azure.  Zvolte **přidat nastavení** vytvořit nové nastavení aplikace. Použití **vložit hodnotu z místního** odkaz zkopírovat hodnotu nastavení **vzdálené** pole. Čekající změny jsou zapsány do souboru místní nastavení a aplikace function app při výběru **OK**.

Můžete také spravovat nastavení aplikace v jednom z těchto způsobů:

* [Pomocí webu Azure portal](functions-how-to-use-azure-function-app-settings.md#settings).
* [Použití `--publish-local-settings` možnost publikování v Azure Functions Core Tools](functions-run-local.md#publish).
* [Použití Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Funkce monitorování

Díky integraci vaší aplikace function app pomocí Azure Application Insights je doporučeným způsobem, jak provádění funkcí monitorování. Když vytvoříte aplikaci function app na webu Azure Portal, je tato integrační ve výchozím nastavení provede za vás. Když vytvoříte aplikaci function app během publikování sady Visual Studio, není dokončení integrace ve vaší aplikaci function app v Azure.

Povolení Application Insights pro aplikaci funkcí:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

## <a name="next-steps"></a>Další postup

Další informace o Azure Functions Core Tools, naleznete v tématu [kódu a testování Azure functions místně](functions-run-local.md).

Další informace o vývoji funkcí jako knihoven tříd .NET najdete v tématu [referenční informace pro vývojáře Azure Functions C#](functions-dotnet-class-library.md). Tento článek taky obsahuje odkazy na příklady toho, jak použít atributy k deklarování různé typy vazeb Azure Functions podporuje.    
