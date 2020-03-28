---
title: 'Kurz: Starší verze aplikace s kontejnerem (náhled)'
description: Zjistěte, jak migrovat vlastní kontejner Windows do služby Azure App Service a nasadit vlastní software v kontejneru.
ms.topic: tutorial
ms.date: 10/22/2019
ms.custom: mvc, seodec18
ms.openlocfilehash: 74cb88bc1ace87155a35163ca8f9d3d6c4242ae0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80046620"
---
# <a name="migrate-an-aspnet-app-to-azure-app-service-using-a-windows-container-preview"></a>Migrace aplikace ASP.NET do služby Azure App Service pomocí kontejneru Windows (verze Preview)

[Azure App Service](overview.md) poskytuje předdefinované zásobníky aplikací ve Windows, jako je ASP.NET nebo Node.js, které běží ve službě IIS. Předkonfigurované prostředí Windows uzamkne přístup k operačnímu systému pro správu a zamezí instalaci softwaru, změnám globální mezipaměti sestavení (GAC) atd. (viz [Funkce operačního systému ve službě Azure App Service](operating-system-functionality.md)). Použití vlastního kontejneru Windows ve službě App Service vám ale umožňuje udělat změny operačního systému, které vaše aplikace potřebuje, takže migrace místní aplikace, která vyžaduje vlastní konfiguraci operačního systému a softwaru, je snadná. Tento kurz ukazuje, jak do služby App Service migrovat aplikaci ASP.NET, která využívá vlastní písma nainstalovaná v knihovně písem Windows. Do služby [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nasadíte vlastní nakonfigurovanou image Windows ze sady Visual Studio a pak ji spustíte ve službě App Service.

![](media/app-service-web-tutorial-windows-containers-custom-fonts/app-running.png)

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- <a href="https://hub.docker.com/" target="_blank">Zaregistrovat si účet Centra Dockeru</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Nainstalujte Docker for Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Přepnout Docker na spouštění kontejnerů Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Nainstalujte SiVisual Studio 2019</a> s **ASP.NET a vývoj webových aplikací** a **úlohy vývoje Azure.** Pokud už jste Visual Studio 2019 nainstalovali:
    - Nainstalujte nejnovější aktualizace v sadě Visual Studio klepnutím na tlačítko**Vyhledat aktualizace** **v nápovědě** > .
    - Přidejte úlohy v sadě Visual Studio kliknutím na **nástroje** > **získat nástroje a funkce**.

## <a name="set-up-the-app-locally"></a>Místní nastavení aplikace

### <a name="download-the-sample"></a>Stažení ukázky

V tomto kroku nastavíte místní projekt .NET.

- [Stáhněte si ukázkový projekt](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- Rozbalte (rozzipujte) soubor *custom-font-win-container.zip*.

Tento ukázkový projekt obsahuje jednoduchou aplikaci ASP.NET, která využívá vlastní písmo nainstalované do knihovny písem Windows. Písma není potřeba instalovat, jde ale o příklad aplikace, která je integrovaná s podkladovým operačním systémem. Pokud chcete takovou aplikaci migrovat do služby App Service, buď úpravou kódu odeberete tuto integraci, nebo ji migrujete tak jak je ve vlastním kontejneru Windows.

### <a name="install-the-font"></a>Instalace písma

V Průzkumníku Windows přejděte na _custom-font-win-container-master/CustomFontSample_, klikněte pravým tlačítkem na _FrederickatheGreat-Regular.ttf_ a vyberte **Nainstalovat**.

Toto písmo je veřejně dostupné na webu [Google Fonts](https://fonts.google.com/specimen/Fredericka+the+Great).

### <a name="run-the-app"></a>Spuštění aplikace

Otevřete soubor *custom-font-win-container/CustomFontSample.sln* v sadě Visual Studio. 

Zadáním `Ctrl+F5` spusťte aplikaci bez zapnutého ladění. Aplikace se zobrazí ve vašem výchozím prohlížeči. 

![Dialogové okno Nový projekt ASP.NET](media/app-service-web-tutorial-windows-containers-custom-fonts/local-app-in-browser.png)

Protože tato aplikace používá nainstalované písmo, nemůže běžet v sandboxu služby App Service. Můžete ji ale místo toho nasadit pomocí kontejneru Windows, protože písmo můžete nainstalovat do tohoto kontejneru Windows.

### <a name="configure-windows-container"></a>Konfigurace kontejneru Windows

V Průzkumníku řešení klikněte pravým tlačítkem na projekt **CustomFontSample** a vyberte **Přidat** > **Podpora orchestrace kontejnerů**.

![Dialogové okno Nový projekt ASP.NET](media/app-service-web-tutorial-windows-containers-custom-fonts/enable-container-orchestration.png)

Vyberte **dockeru compose** > **OK**.

Projekt je teď nastavený tak, aby běžel v kontejneru Windows. Do projektu **CustomFontSample** se přidá soubor _Dockerfile_ a projekt **docker-compose** se přidá do řešení. 

V Průzkumníku řešení otevřete soubor **Dockerfile**.

Musíte použít [podporovanou nadřazenou image](app-service-web-get-started-windows-container.md#use-a-different-parent-image). Nadřazenou image změníte tak, že řádek `FROM` nahradíte následujícím kódem:

```Dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

Na konec souboru přidejte následující řádek a pak tento soubor uložte:

```Dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

_InstallFont.ps1_ najdete v projektu **CustomFontSample**. Jde o jednoduchý skript, který nainstaluje toto písmo. Složitější verzi tohoto skriptu najdete v [centru skriptů](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133).

> [!NOTE]
> Chcete-li otestovat kontejner windows místně, ujistěte se, že Docker je spuštěn na místním počítači.
>

## <a name="publish-to-azure-container-registry"></a>Publikování do služby Azure Container Registry

Služba [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) uchovává vaše image pro nasazení kontejnerů. Službu App Service můžete nakonfigurovat tak, aby používala image hostované ve službě Azure Container Registry.

### <a name="open-publish-wizard"></a>Otevření průvodce publikováním

V Průzkumníku řešení klikněte pravým tlačítkem na projekt **CustomFontSample** a vyberte **Publikovat**.

![Dialogové okno Nový projekt ASP.NET](media/app-service-web-tutorial-windows-containers-custom-fonts/open-publish-wizard.png)

### <a name="create-registry-and-publish"></a>Vytvoření registru a publikování

V průvodci publikováním vyberte **registr kontejnerů** > **vytvořit nový registr** > kontejnerů Azure**Publish**.

![Dialogové okno Nový projekt ASP.NET](media/app-service-web-tutorial-windows-containers-custom-fonts/create-registry.png)

### <a name="sign-in-with-azure-account"></a>Přihlášení pomocí účtu Azure

V dialogu **Vytvořit nový Azure Container Registry** vyberte **Přidat účet** a přihlaste se ke svému předplatnému Azure. Pokud už jste přihlášeni, vyberte z rozevíracího seznamu účet, který obsahuje požadované předplatné.

![Přihlášení k Azure](./media/app-service-web-tutorial-windows-containers-custom-fonts/add-an-account.png)

### <a name="configure-the-registry"></a>Konfigurace registru

Nakonfigurujte nový registr kontejneru podle navržených hodnot v následující tabulce. Jakmile budete hotovi, klikněte na **Vytvořit**.

| Nastavení  | Navrhovaná hodnota | Další informace |
| ----------------- | ------------ | ----|
|**Předpona DNS**| Ponechejte vygenerovaný název registru nebo ho změňte na jiný jedinečný název. |  |
|**Skupina prostředků**| Klikněte na **Nový**, zadejte **myResourceGroup** a klikněte na **OK**. |  |
|**Sku**| Basic | [Cenové úrovně](https://azure.microsoft.com/pricing/details/container-registry/)|
|**Umístění registru**| Západní Evropa | |

![Konfigurace Azure Container Registry](./media/app-service-web-tutorial-windows-containers-custom-fonts/configure-registry.png)

Otevře se okno terminálu s průběhem nasazování image. Počkejte, než se nasazení dokončí.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-web-app"></a>Vytvoření webové aplikace

V levé nabídce vyberte Vytvořit**webovou** >  **webovou** > **aplikaci pro kontejnery**.

### <a name="configure-app-basics"></a>Konfigurace základů aplikací

Na kartě **Základy** nakonfigurujte nastavení podle následující tabulky a klikněte na **Další: Docker**.

| Nastavení  | Navrhovaná hodnota | Další informace |
| ----------------- | ------------ | ----|
|**Předplatné**| Zkontrolujte, zda je vybráno správné předplatné. |  |
|**Skupina prostředků**| Vyberte **Vytvořit nový**, zadejte **myResourceGroup**a klepněte na tlačítko **OK**. |  |
|**Název**| Zadejte jedinečný název. | Adresa URL webové aplikace je `http://<app-name>.azurewebsites.net`, kde `<app-name>` je název vaší aplikace. |
|**Publikovat**| Kontejner Dockeru | |
|**Operační systém**| Windows | |
|**Oblasti**| Západní Evropa | |
|**Plán Windows**| Vyberte **Vytvořit nový**, zadejte **myAppServicePlan**a klepněte na **tlačítko OK**. | |

Karta **Základy** by měla vypadat takto:

![](media/app-service-web-tutorial-windows-containers-custom-fonts/configure-app-basics.png)

### <a name="configure-windows-container"></a>Konfigurace kontejneru Windows

Na kartě **Docker** nakonfigurujte vlastní kontejner Windows, jak je znázorněno v následující tabulce, a vyberte **Zkontrolovat + vytvořit**.

| Nastavení  | Navrhovaná hodnota |
| ----------------- | ------------ |
|**Zdroj image**| Registr kontejnerů Azure |
|**Registru**| Vyberte [registr, který jste vytvořili dříve](#publish-to-azure-container-registry). |
|**Obrázek**| vlastní ukázka písma |
|**Značku**| nejnovější |

### <a name="complete-app-creation"></a>Dokončení vytvoření aplikace

Klikněte na **Vytvořit** a počkejte, až Azure vytvoří požadované prostředky.

## <a name="browse-to-the-web-app"></a>Přechod na webovou aplikaci

Po dokončení operace Azure se zobrazí okno s oznámením.

![](media/app-service-web-tutorial-windows-containers-custom-fonts/portal-create-finished.png)

1. Klikněte na **Přejít k prostředku**.

2. Na stránce aplikace klikněte na odkaz v části **Adresa URL**.

Na nové stránce prohlížeče se otevře následující stránka:

![](media/app-service-web-tutorial-windows-containers-custom-fonts/app-starting.png)

Počkejte pár minut a zkuste to znovu, dokud se nedostanete na domovskou stránku s písmem, které očekáváte:

![](media/app-service-web-tutorial-windows-containers-custom-fonts/app-running.png)

**Blahopřejeme!** Migrovali jste aplikaci ASP.NET do služby Azure App Service v kontejneru Windows.

## <a name="see-container-start-up-logs"></a>Zobrazení protokolů spuštění kontejneru

Načtení kontejneru s Windows může nějakou dobu trvat. Chcete-li zobrazit průběh, přejděte na * \<* následující adresu URL nahrazením>názvu aplikace názvem aplikace.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

Streamované protokoly vypadají přibližně takto:

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

