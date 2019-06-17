---
title: Vývoj a nasazení WebJobs pomocí sady Visual Studio – Azure
description: Zjistěte, jak vyvinout a nasadit Azure WebJobs na Azure App Service pomocí sady Visual Studio.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 02/18/2019
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 9f4d3ff6fa02369c0e4a01949cc686b842a63a12
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808464"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Vývoj a nasazení WebJobs pomocí sady Visual Studio – služby Azure App Service

Tento článek vysvětluje, jak pomocí sady Visual Studio nasadit projekt konzolové aplikace do webové aplikace v [služby App Service](overview.md) jako [Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226). Informace o tom, jak nasadit WebJobs pomocí [webu Azure portal](https://portal.azure.com), naleznete v tématu [úlohy spusťte na pozadí pomocí WebJobs](webjobs-create.md).

Více WebJobs můžete publikovat jednu webovou aplikaci. Ujistěte se, že každá webová úloha ve webové aplikaci má jedinečný název.

Verze 3.x [Azure WebJobs SDK](webjobs-sdk-how-to.md) umožňuje vývoj webových úloh, které ke spuštění aplikace .NET Core nebo .NET Framework aplikacích a zároveň je verze 2.x podporuje pouze rozhraní .NET Framework. Způsob, jak nasadit projekt WebJobs se liší pro projekty .NET Core oproti .NET Framework z nich.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs jako aplikace konzoly .NET Core

Při použití verze 3.x webjobs, můžete vytvářet a publikovat WebJobs jako aplikace konzoly .NET Core. Podrobné pokyny týkající se vytvoření a publikování aplikace konzoly .NET Core do Azure jako WebJob, naleznete v tématu [Začínáme se sadou Azure WebJobs SDK pro zpracování na pozadí založený na událostech](webjobs-sdk-get-started.md).

> [!NOTE]
> Webové úlohy .NET core, nedá se propojit s webovými projekty. Pokud potřebujete nasadit své webové úlohy s webovou aplikací, měli byste [vytvoření webové úlohy jako konzolovou aplikaci .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Nasazení do Azure App Service

Publikování webové úlohy .NET Core do služby App Service ze sady Visual Studio používá stejné nástroje jako publikování aplikace ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Typy webová úloha.

Ve výchozím nastavení publikovaná ve webové úloze od .NET Core projekt konzoly spustí pouze v případě, že se aktivuje nebo na vyžádání. Můžete také aktualizovat projekt tak, aby [spouštět podle plánu](#scheduled-execution) běžet pořád.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Plánované spuštění

Když publikujete konzolovou aplikaci .NET Core do Azure, novou *settings.job* přidá soubor do projektu. Tento soubor slouží k nastavení plánu spuštění pro webové úlohy. Další informace najdete v tématu [plánování aktivované webové úlohy](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Nepřetržité provádění

Chcete-li změnit WebJob nepřetržitě, když je zapnuté Always On v Azure můžete použít Visual Studio.

1. Pokud jste tak již neučinili, [publikovat projekt do Azure](#deploy-to-azure-app-service).

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na požadovaný projekt a vyberte **Publikovat**.

1. V **publikovat** kartě **nastavení**. 

1. V **nastavení profilu** dialogovém okně zvolte **průběžné** pro **typ webové úlohy**a zvolte **Uložit**.

    ![Dialogové okno Nastavení publikování pro webové úlohy](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Vyberte **publikovat** znovu publikovat webovou úlohu s aktualizovaným nastavením.

## <a name="webjobs-as-net-framework-console-apps"></a>WebJobs jako aplikace konzoly rozhraní .NET Framework  

Když Visual Studio nasadí projekt webové úlohy .NET Framework konzoly aplikace s povolenými, provede dvě úlohy:

* Zkopíruje soubory modulu runtime do příslušné složky ve službě web app (*App_Data/úlohy/průběžné* pro průběžné WebJobs a *App_Data/úlohy/triggered* pro WebJobs plánované nebo na vyžádání).
* Nastaví [Azure Scheduleru](https://docs.microsoft.com/azure/scheduler/) úlohy pro webových úloh, které jsou naplánovány ke spuštění v určitou dobu. (To není nutné pro průběžné WebJobs).

Projekt povoleno WebJobs má přidat do něj následující položky:

* [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) balíček NuGet.
* A [webové úlohy publikovat settings.json](#publishsettings) soubor, který obsahuje nastavení nasazení a Plánovač. 

![Diagram znázorňující, co je přidán do konzolové aplikace má povolit nasazení jako webové úlohy](./media/webjobs-dotnet-deploy-vs/convert.png)

Můžete přidat tyto položky do existujícího projektu konzolové aplikace nebo použití šablony k vytvoření nového projektu aplikace s povolenými webové úlohy konzoly. 

Můžete nasadit projekt jako webová úloha samostatně, nebo odkaz do webového projektu tak, aby automaticky nasadí pokaždé, když se nasazení webového projektu. Odkaz projekty, Visual Studio obsahuje název projektu povolené WebJobs v [webjobs list.json](#webjobslist) souboru webového projektu.

![Diagram znázorňující projektu úlohy WebJob propojení do webového projektu](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Požadavky

Pokud používáte Visual Studio 2015, nainstalujte [sady Azure SDK pro .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Pokud používáte Visual Studio 2019, nainstalujte [funkcí vývoj pro Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a id="convert"></a> Povolit nasazení WebJobs pro existující projekt konzolové aplikace

Máte dvě možnosti:

* [Povolení automatického nasazení s webovým projektem](#convertlink).

  Existující projekt konzolové aplikace nakonfigurujte tak, aby se automaticky nasadí jako webová úloha při nasazování webového projektu. Tuto možnost použijte, pokud chcete spustit své webové úlohy ve stejné webové aplikaci, ve kterém budete spouštět související webové aplikace.

* [Povolit nasazení bez webový projekt](#convertnolink).

  Nakonfigurujte existující projekt konzolové aplikace nasadit jako Webjobu samostatně, žádné odkazy na webový projekt. Tuto možnost použijte, pokud chcete spustit ve webové úloze ve webové aplikaci samostatně, se žádné webové aplikace spuštěné ve webové aplikaci. Můžete k tomu, aby bylo možné škálovat webovou úlohu prostředky bez ohledu na jejich prostředkům webové aplikace.

#### <a id="convertlink"></a> Povolit automatické nasazení WebJobs pomocí webového projektu

1. Klikněte pravým tlačítkem na webový projekt v **Průzkumníka řešení**a potom klikněte na tlačítko **přidat** > **existujícího projektu jako Azure WebJob**.
   
    ![Stávající projekt jako webová úloha Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    [Přidat webovou úlohu Azure](#configure) zobrazí se dialogové okno.
2. V **název projektu** rozevíracího seznamu vyberte projekt konzolové aplikace Pokud chcete přidat jako webová úloha.
   
    ![Výběrem projektu v dialogovém okně Přidat webovou úlohu Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Dokončení [přidat webovou úlohu Azure](#configure) dialogového okna a pak klikněte na tlačítko **OK**. 

#### <a id="convertnolink"></a> Povolit nasazení WebJobs bez webového projektu
1. Klikněte pravým tlačítkem na projekt konzolové aplikace v **Průzkumníka řešení**a potom klikněte na tlačítko **publikovat jako Azure WebJob...** . 
   
    ![Publikovat jako Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    [Přidat webovou úlohu Azure](#configure) dialogového okna se projekt vybraný v **název projektu** pole.
2. Dokončení [přidat webovou úlohu Azure](#configure) dialogové okno a potom klikněte na **OK**.
   
   **Publikování webu** průvodce se zobrazí.  Pokud nechcete okamžitě publikovat, zavřete průvodce. Nastavení, které jste zadali se uloží pro, pokud chcete [nasazení projektu](#deploy).

### <a id="create"></a>Vytvoření nového projektu s povoleným WebJobs
Chcete-li vytvořit nový projekt povoleno WebJobs, použijte šablonu projektu konzolové aplikace a povolit nasazení webových úloh, jak je vysvětleno v [předchozí části](#convert). Jako alternativu můžete použít šablonu nový projekt webové úlohy:

* [Nový projekt šablonu WebJobs můžete použít pro nezávislé webová úloha.](#createnolink)
  
    Vytvoření projektu a nakonfigurujte ho nasadit samostatně jako webová úloha se žádný odkaz do webového projektu. Tuto možnost použijte, pokud chcete spustit ve webové úloze ve webové aplikaci samostatně, se žádné webové aplikace spuštěné ve webové aplikaci. Můžete k tomu, aby bylo možné škálovat webovou úlohu prostředky bez ohledu na jejich prostředkům webové aplikace.
* [Použití šablony nový projekt webové úlohy WebJob propojené do webového projektu](#createlink)
  
    Vytvoření projektu, který je nakonfigurován automaticky jako webová úloha nasazení při nasazení webového projektu ve stejném řešení. Tuto možnost použijte, pokud chcete spustit své webové úlohy ve stejné webové aplikaci, ve kterém budete spouštět související webové aplikace.

> [!NOTE]
> Nový projekt šablony WebJobs automaticky nainstaluje balíčky NuGet a obsahuje kód v *Program.cs* pro [sada WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Pokud nechcete, aby k použití sady WebJobs SDK, odeberte nebo změňte `host.RunAndBlock` výroky *Program.cs*.
> 
> 

#### <a id="createnolink"></a> Nový projekt šablonu WebJobs můžete použít pro nezávislé webová úloha.
1. Klikněte na tlačítko **souboru** > **nový projekt**a pak v **nový projekt** dialogovém okně **cloudu**  >   **Webová úloha Azure (.NET Framework)** .
   
    ![Dialogové okno Nový projekt zobrazuje šablony webové úlohy](./media/webjobs-dotnet-deploy-vs/np.png)
2. Postupujte podle pokynů na výše uvedené [aplikaci konzoly projektu nezávislé projektu WebJobs](#convertnolink).

#### <a id="createlink"></a> Použití šablony nový projekt webové úlohy WebJob propojené do webového projektu
1. Klikněte pravým tlačítkem na webový projekt v **Průzkumníka řešení**a potom klikněte na tlačítko **přidat** > **nový projekt webové úlohy Azure**.
   
    ![Položka nabídky Nový projekt webové úlohy Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    [Přidat webovou úlohu Azure](#configure) zobrazí se dialogové okno.
2. Dokončení [přidat webovou úlohu Azure](#configure) dialogové okno a potom klikněte na **OK**.

### <a id="configure"></a>Dialogové okno Přidat úlohu WebJob Azure
**Přidat Azure WebJob** dialogové okno umožňuje zadat název úlohy WebJob a běhu režim pro webové úlohy. 

![Přidat dialog Azure WebJob](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Pole v tomto dialogovém okně odpovídají polím na **přidat WebJob** dialogového okna na webu Azure Portal. Další informace najdete v tématu [úlohy spusťte na pozadí pomocí WebJobs](webjobs-create.md).

> [!NOTE]
> * Informace o nasazení příkazového řádku najdete v tématu [povolení příkazového řádku nebo nepřetržité doručování Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Pokud nasadíte ve webové úloze a rozhodněte, kterou chcete změnit typ webové úlohy a znovu nasaďte, budete muset odstranit *webjobs publikovat settings.json* souboru. Díky tomu budou sady Visual Studio zobrazit možnosti publikování znovu, abyste mohli změnit typ webové úlohy.
> * Pokud nasadíte ve webové úloze a později změníte režimu spuštění z průběžné na nesouvislé nebo naopak, Visual Studio při opětovném nasazování vytvoří nové webové úlohy v Azure. Pokud změníte další plánování nastavení, ale ponechte režim spuštění stejné nebo přepínání mezi Scheduled a na vyžádání, Visual Studio aktualizuje existující úlohy spíše než vytvořte novou.
> 
> 

### <a id="publishsettings"></a>webjob-publish-settings.json
Při konfiguraci konzolovou aplikaci WebJobs nasazení Visual Studio nainstaluje [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet balíčku a plánování informací v úložišti *webové úlohy publikovat settings.json*  soubor v projektu *vlastnosti* složky projektu WebJobs. Tady je příklad tohoto souboru:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Tento soubor můžete upravit přímo a Visual Studio nabízí IntelliSense. Schéma souboru je uložen na [ https://schemastore.org ](https://schemastore.org/schemas/json/webjob-publish-settings.json) a lze je zobrazit.  

### <a id="webjobslist"></a>webjobs-list.json
Když propojíte svůj projekt povoleno WebJobs do webového projektu, Visual Studio ukládá název projektu WebJobs v *webjobs list.json* soubor v projektu webové *vlastnosti* složky. Seznam může obsahovat více projektů webových úloh, jak je znázorněno v následujícím příkladu:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Tento soubor můžete upravit přímo a Visual Studio nabízí IntelliSense. Schéma souboru je uložen na [ https://schemastore.org ](https://schemastore.org/schemas/json/webjobs-list.json) a lze je zobrazit.

### <a id="deploy"></a>Nasazení WebJobs projektu
WebJobs projekt, který jste propojili do webového projektu automaticky nasadí s webového projektu. Informace o nasazení webového projektu naleznete v tématu **provede postupy** > **nasadit aplikaci** v levém navigačním panelu.

Nasazení WebJobs projektu, kliknete pravým tlačítkem na projekt v **Průzkumníka řešení** a klikněte na tlačítko **publikovat jako Azure WebJob...** . 

![Publikovat jako Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)

Pro nezávislé úlohy WebJob, stejné **publikování webu** průvodce, který se používá pro webové projekty, ale s menším počtem nastavením možné změnit.

## <a name="scheduling-a-triggered-webjob"></a>Plánování aktivované webové úlohy

Pomocí WebJobs *settings.job* soubor k určení, kdy se webová úloha spustí. Tento soubor slouží k nastavení plánu spuštění pro webové úlohy. Následující příklad spouští každou hodinu od 9: 00 do 17: 00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Tento soubor musí být umístěn v kořenové složce WebJobs, podél straně své webové úlohy skriptu `wwwroot\app_data\jobs\triggered\{job name}` nebo `wwwroot\app_data\jobs\continuous\{job name}`. Když nasadíte webovou úlohu ze sady Visual Studio, označit vaše `settings.job` vlastnosti jako souboru **kopírovat, pokud je novější**. 

Pokud jste [vytvoření webové úlohy z portálu Azure portal](webjobs-create.md), je vytvořen soubor settings.job.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Výrazů CRON

WebJobs používá stejné výrazů CRON pro plánování jako trigger časovače ve službě Azure Functions. Další informace o podpoře CRON, najdete v článku [časovače triggeru článku](../azure-functions/functions-bindings-timer.md#cron-expressions).

### <a name="settingjob-reference"></a>odkaz na Setting.job

Služba WebJobs podporuje následující nastavení:

| **Nastavení** | **Typ**  | **Popis** |
| ----------- | --------- | --------------- |
| `is_in_place` | Vše | Umožňuje tato úloha spustila na místě, aniž se nejprve zkopírována do dočasné složky. Další informace najdete v tématu [WebJobs pracovní adresář](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Průběžný | Webové úlohy spusťte jenom na jednu instanci horizontální navýšení kapacity. Další informace najdete v tématu [nastavit nepřetržité úlohy jako singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Aktivuje | Webová úloha spustí podle plánu na základě CRON. Další informace najdete v tématu [časovače triggeru článku](../azure-functions/functions-bindings-timer.md#cron-expressions). |
| `stopping_wait_time`| Vše | Umožňuje řídit chování při vypnutí. Další informace najdete v tématu [řádné vypnutí](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o sadě WebJobs SDK](webjobs-sdk-how-to.md)
