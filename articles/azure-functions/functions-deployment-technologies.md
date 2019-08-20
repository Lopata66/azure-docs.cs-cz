---
title: Technologie nasazení v Azure Functions | Microsoft Docs
description: Seznamte se s různými způsoby, jak můžete nasadit kód pro Azure Functions.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 9f40ec658fc6725f381300d967c9d7cd61c3a218
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624151"
---
# <a name="deployment-technologies-in-azure-functions"></a>Technologie nasazení v Azure Functions

K nasazení kódu Azure Functions projektu do Azure můžete použít několik různých technologií. Tento článek obsahuje vyčerpávající seznam těchto technologií, popisuje, které technologie jsou k dispozici pro které typy funkcí, vysvětluje, co se stane, když použijete jednotlivé metody, a poskytuje doporučení, jak nejlépe využít v různých scénářích. . Různé nástroje, které podporují nasazení na Azure Functions, jsou vyladěny na správnou technologii na základě jejich kontextu. Obecně platí, že nasazení zip je doporučenou technologií nasazení pro Azure Functions.

## <a name="deployment-technology-availability"></a>Dostupnost technologie nasazení

Azure Functions podporuje místní vývoj a hostování pro různé platformy v systémech Windows a Linux. V současné době jsou k dispozici tři plány hostování:

+ [Nároky](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Vyhrazeno (App Service)](functions-scale.md#app-service-plan)

Každý plán má jiné chování. Ne všechny technologie nasazení jsou k dispozici pro každý charakter Azure Functions. Následující graf ukazuje, které technologie nasazení jsou podporovány pro každou kombinaci operačního systému a plánu hostování:

| Technologie nasazení | Spotřeba Windows | Windows Premium (Preview) | Vyhrazená pro Windows  | Spotřeba Linux | Linux Premium (Preview) | Vyhrazený pro Linux |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| Adresa URL externího balíčku<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Nasazení zip |✔|✔|✔|✔|✔|✔|
| Kontejner Dockeru | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| Správy zdrojového kódu |✔|✔|✔| |✔|✔|
| Místní Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Cloudová synchronizace<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Úpravy portálu |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> technologie nasazení, která vyžaduje [synchronizaci ručních triggerů](#trigger-syncing).  
<sup>2</sup> úpravy portálu jsou povolené jenom pro aktivační události http a Timer pro funkce v systému Linux pomocí prémiových a vyhrazených plánů.

## <a name="key-concepts"></a>Klíčové koncepty

Některé klíčové koncepty jsou zásadní pro porozumění způsobu, jakým nasazení fungují v Azure Functions.

### <a name="trigger-syncing"></a>Spustit synchronizaci

Při změně jakékoli aktivační události musí infrastruktura funkcí znát změny. K synchronizaci dochází automaticky pro mnoho technologií nasazení. V některých případech je však nutné triggery ručně synchronizovat. Když nasadíte aktualizace odkazem na adresu URL externího balíčku, místní Git, synchronizace cloudu nebo FTP, musíte triggery ručně synchronizovat. Triggery můžete synchronizovat jedním ze tří způsobů:

* Restartujte aplikaci Function App v Azure Portal
* Odešlete požadavek HTTP POST na `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` použití [hlavního klíče](functions-bindings-http-webhook.md#authorization-keys).
* Odeslat požadavek HTTP POST do `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Zástupné symboly nahraďte ID předplatného, názvem skupiny prostředků a názvem vaší aplikace Function App.

### <a name="remote-build"></a>Vzdálené sestavení

Azure Functions může automaticky provádět buildy na kódu, který obdrží po nasazení zip. Tato sestavení se chovají trochu různě v závislosti na tom, jestli je aplikace spuštěná v systému Windows nebo Linux. Vzdálená sestavení se neprovádí, pokud se aplikace dřív nastavila tak, aby běžela v režimu [balíčku](run-functions-from-deployment-package.md) . 

> [!NOTE]
> Pokud máte problémy se vzdáleným sestavením, může to být způsobeno tím, že aplikace byla vytvořena před zpřístupněním funkce (1. srpna 2019). Zkuste vytvořit novou aplikaci Function App.

#### <a name="remote-build-on-windows"></a>Vzdálené sestavení ve Windows

Všechny aplikace Function App spuštěné v systému Windows mají malou aplikaci pro správu, lokalitu SCM (nebo [Kudu](https://github.com/projectkudu/kudu)). Tato lokalita zpracovává většinu nasazení a logiku sestavení pro Azure Functions.

Při nasazení aplikace do systému Windows se spustí příkazy specifické pro jazyk, například `dotnet restore` (C#) nebo `npm install` (JavaScript).

#### <a name="remote-build-on-linux-preview"></a>Vzdálené sestavení v systému Linux (Preview)

Chcete-li povolit vzdálené sestavení v systému Linux, je nutné nastavit následující [nastavení aplikace](functions-how-to-use-azure-function-app-settings.md#settings):

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Když se aplikace na platformě Linux vytváří vzdáleně, [spouštějí se z balíčku pro nasazení](run-functions-from-deployment-package.md).

> [!NOTE]
> Vzdálené sestavení pro plán vyhrazený pro Linux (App Service) se v tuto chvíli podporuje jenom pro Node. js a Python.

##### <a name="consumption-preview-plan"></a>Plán využití (Preview)

Aplikace Functions pro Linux běžící v plánu spotřeby nemají web SCM/Kudu, který omezuje možnosti nasazení. Aplikace Function App v systému Linux spuštěné v plánu spotřeby ale podporují vzdálená sestavení. Tato vzdálená sestavení používají [Oryx](https://github.com/microsoft/Oryx).

##### <a name="dedicated-and-premium-preview-plans"></a>Plány vyhrazené a Premium (verze Preview)

Aplikace Function App běžící na Linux ve [vyhrazeném plánu (App Service)](functions-scale.md#app-service-plan) a [plánu Premium](functions-scale.md#premium-plan) mají taky omezený web SCM/Kudu, který využívá [Oryx](https://github.com/microsoft/Oryx).

## <a name="deployment-technology-details"></a>Podrobnosti o technologii nasazení

V Azure Functions jsou k dispozici následující metody nasazení.

### <a name="external-package-url"></a>Adresa URL externího balíčku

Adresu URL externího balíčku můžete použít k odkazování na vzdálený soubor balíčku (. zip), který obsahuje vaši aplikaci Function App. Soubor se stáhne ze zadané adresy URL a aplikace se spustí v režimu [spuštění z balíčku](run-functions-from-deployment-package.md) .

>__Jak ji použít:__ Přidejte `WEBSITE_RUN_FROM_PACKAGE` do nastavení aplikace. Hodnota tohoto nastavení by měla být adresa URL (umístění konkrétního souboru balíčku, který chcete spustit). Nastavení můžete přidat buď [na portálu](functions-how-to-use-azure-function-app-settings.md#settings) , nebo [pomocí Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Pokud používáte službu Azure Blob Storage, pomocí privátního kontejneru se [sdíleným přístupovým podpisem (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) udělte funkce přístup k balíčku. Pokaždé, když se aplikace restartuje, načte kopii obsahu. Váš odkaz musí být platný po dobu života aplikace.

>__Kdy ji použít:__ Adresa URL externího balíčku je jediná podporovaná metoda nasazení Azure Functions spuštěná v systému Linux v plánu spotřeby, pokud uživatel konkrétně nechce, aby došlo ke vzdálenému sestavení. Když aktualizujete soubor balíčku, na který odkazuje aplikace Function App, musíte [triggery ručně synchronizovat](#trigger-syncing) a sdělit tak Azure, že se vaše aplikace změnila.

### <a name="zip-deploy"></a>Nasazení zip

Pomocí nástroje zip Deploy nahrajte soubor. zip, který obsahuje vaši aplikaci Function App do Azure. Volitelně můžete nastavit, aby se aplikace spouštěla [z balíčku](run-functions-from-deployment-package.md), nebo určit, že dojde ke [vzdálenému sestavení](#remote-build) .

>__Jak ji použít:__ Nasazení pomocí vašeho oblíbeného klientského nástroje: [Vs Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)nebo [Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Pokud chcete soubor. zip nasadit do aplikace Function App ručně, postupujte podle pokynů v tématu [nasazení ze souboru. zip nebo adresy URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

Chcete-li provést nasazení souboru zip se vzdáleným sestavením, použijte následující příkaz pro [základní nástroje](functions-run-local.md) :

```bash
func azure functionapp publish <app name> --build remote
```

Alternativně můžete VS Code provést vzdálené sestavení při nasazení přidáním příznaku ' ' azureFunctions. scmDoBuildDuringDeployment '. Pokud se chcete dozvědět, jak VS Code přidat příznak, přečtěte si pokyny na [wikiwebu rozšíření Azure Functions](https://github.com/microsoft/vscode-azurefunctions/wiki).

>Když nasadíte pomocí nasazení zip, můžete nastavit, aby se aplikace [spouštěla z balíčku](run-functions-from-deployment-package.md). Pro spuštění z balíčku nastavte `WEBSITE_RUN_FROM_PACKAGE` hodnotu nastavení aplikace na. `1` Doporučujeme nasazení zip. Poskytuje rychlejší načítání pro vaše aplikace a je výchozím nastavením pro VS Code, Visual Studio a Azure CLI. 

>__Kdy ji použít:__ Nasazení zip je doporučená technologie nasazení pro Azure Functions.

### <a name="docker-container"></a>Kontejner Dockeru

Můžete nasadit image kontejneru pro Linux, která obsahuje vaši aplikaci Function App.

>__Jak ji použít:__ Vytvořte aplikaci Functions pro Linux v plánu Premium nebo vyhrazené a určete, ze které image kontejneru se má spustit. Můžete to provést dvěma způsoby:
>
>* Vytvořte aplikaci funkcí pro Linux v plánu Azure App Service Azure Portal. V části **publikovat**vyberte **Image Docker**a pak nakonfigurujte kontejner. Zadejte umístění, kde je bitová kopie hostovaná.
>* Pomocí Azure CLI vytvoříte aplikaci funkcí pro Linux v plánu App Service. Další informace o postupu najdete v tématu [Vytvoření funkce na platformě Linux pomocí vlastní image](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Pokud chcete nasadit do existující aplikace pomocí vlastního kontejneru, v [Azure Functions Core Tools](functions-run-local.md)použijte [`func deploy`](functions-run-local.md#publish) příkaz.

>__Kdy ji použít:__ Možnost kontejneru Docker použijte, když potřebujete větší kontrolu nad prostředím Linux, ve kterém běží aplikace Function App. Tento mechanismus nasazení je k dispozici pouze pro funkce spuštěné v systému Linux.

### <a name="web-deploy-msdeploy"></a>Nasazení webu (MSDeploy)

Nasazení webu balíčky a nasadí aplikace Windows na libovolný server služby IIS, včetně aplikací Function App běžících na Windows v Azure.

>__Jak ji použít:__ Použijte [Visual Studio Tools for Azure Functions](functions-create-your-first-function-visual-studio.md). Zrušte zaškrtnutí políčka **Spustit ze souboru balíčku (doporučeno)** .
>
>Můžete si také stáhnout [nasazení webu 3,6](https://www.iis.net/downloads/microsoft/web-deploy) a zavolat `MSDeploy.exe` přímo.

>__Kdy ji použít:__ Nasazení webu se podporuje a nemá žádné problémy, ale upřednostňovaným mechanismem je [nasazení zip s povoleným spuštěním z balíčku](#zip-deploy). Další informace najdete v příručce pro [vývoj sady Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Správy zdrojového kódu

Použijte správu zdrojového kódu k připojení aplikace Function App k úložišti Git. Aktualizace kódu v tomto úložišti aktivuje nasazení. Další informace najdete na [wikiwebu Kudu](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Jak ji použít:__ Pomocí centra nasazení v oblasti funkce portálu nastavte publikování ze správy zdrojového kódu. Další informace najdete v tématu [průběžné nasazování pro Azure Functions](functions-continuous-deployment.md).

>__Kdy ji použít:__ Používání správy zdrojového kódu je osvědčeným postupem pro týmy, které spolupracují na svých aplikacích Function App. Správa zdrojového kódu je dobrá možnost nasazení, která umožňuje náročnější kanály nasazení.

### <a name="local-git"></a>Místní Git

Místní Git můžete použít k nabízení kódu z místního počítače do Azure Functions pomocí Gitu.

>__Jak ji použít:__ Podle pokynů v tématu [nasazení místního úložiště Git Azure App Service](../app-service/deploy-local-git.md).

>__Kdy ji použít:__ Obecně doporučujeme, abyste používali jinou metodu nasazení. Při publikování z místního Gitu musíte [triggery synchronizovat ručně](#trigger-syncing).

### <a name="cloud-sync"></a>Synchronizace cloudu

Použijte synchronizaci cloudu k synchronizaci obsahu z Dropboxu a OneDrivu po Azure Functions.

>__Jak ji použít:__ Postupujte podle pokynů v části [synchronizace obsahu z cloudové složky](../app-service/deploy-content-sync.md).

>__Kdy ji použít:__ Obecně doporučujeme další metody nasazení. Když publikujete pomocí synchronizace cloudu, musíte [triggery ručně synchronizovat](#trigger-syncing).

### <a name="ftp"></a>FTP

Protokol FTP můžete použít k přímému přenosu souborů do Azure Functions.

>__Jak ji použít:__ Postupujte podle pokynů v části [nasazení obsahu pomocí protokolu FTP/s](../app-service/deploy-ftp.md).

>__Kdy ji použít:__ Obecně doporučujeme další metody nasazení. Při publikování pomocí FTP je nutné [triggery ručně synchronizovat](#trigger-syncing).

### <a name="portal-editing"></a>Úpravy portálu

V editoru založeném na portálu můžete přímo upravovat soubory, které jsou ve vaší aplikaci Function App (v podstatě se nasazují při každém uložení změn).

>__Jak ji použít:__ Aby bylo možné upravit funkce v Azure Portal, je nutné [vytvořit své funkce na portálu](functions-create-first-azure-function.md). Aby bylo možné zachovat jeden zdroj pravdy, může použití jakékoli jiné metody nasazení fungovat jen pro čtení a zabránit pokračování v úpravách portálu. Chcete-li se vrátit do stavu, ve kterém můžete upravovat soubory v Azure Portal, můžete ručně zapnout režim úprav zpátky `Read/Write` a odebrat všechna nastavení aplikace související s nasazením (například `WEBSITE_RUN_FROM_PACKAGE`). 

>__Kdy ji použít:__ Portál je dobrým způsobem, jak začít s Azure Functions. Pro přesnější vývojovou práci doporučujeme použít jeden z následujících nástrojů klienta:
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools (příkazový řádek)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

V následující tabulce jsou uvedeny operační systémy a jazyky, které podporují úpravy portálu:

| | Spotřeba Windows | Windows Premium (Preview) | Vyhrazená pro Windows | Spotřeba Linux | Linux Premium (Preview)| Vyhrazený pro Linux |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|:---------------:|
| C# | | | | | |
| C#Pravidel |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (Preview) | | | | | | |
| PowerShell (Preview) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup>Úpravy portálu jsou povolené jenom pro aktivační události HTTP a Timer pro funkce v systému Linux pomocí prémiových a vyhrazených plánů.

## <a name="deployment-slots"></a>Nasazovací sloty

Když nasadíte aplikaci Function App do Azure, můžete ji nasadit do samostatného slotu pro nasazení místo přímo do produkčního prostředí. Další informace o slotech nasazení najdete v dokumentaci [Azure Functions Deployments](../app-service/deploy-staging-slots.md) – další podrobnosti.

## <a name="next-steps"></a>Další postup

Další informace o nasazení aplikací Function App najdete v těchto článcích: 

+ [Průběžné nasazování se službou Azure Functions](functions-continuous-deployment.md)
+ [Průběžné doručování pomocí Azure DevOps](functions-how-to-azure-devops.md)
+ [Nasazení zip pro Azure Functions](deployment-zip-push.md)
+ [Spuštění Azure Functions ze souboru balíčku](run-functions-from-deployment-package.md)
+ [Automatizace nasazení prostředků pro aplikaci Function App v Azure Functions](functions-infrastructure-as-code.md)
