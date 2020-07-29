---
title: Připojení kompletního řešení
titleSuffix: Azure Digital Twins
description: Kurz pro sestavení uceleného řešení digitálních vláken Azure, které je založené na datech zařízení
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: aae1797f7f1a252a4f094ee9f1b079fb60ba72f3
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131731"
---
# <a name="build-out-an-end-to-end-solution"></a>Sestavení kompletního řešení

K nastavení kompletního řešení založeného na živých datech z vašeho prostředí můžete propojit instanci digitálních vláken Azure s dalšími službami Azure za účelem správy zařízení a dat.

V tomto kurzu budete...
* Nastavení instance digitálních vláken Azure
* Seznamte se s ukázkovým scénářem sestavení a vytvořením instance předem zapsaných komponent.
* Použití aplikace [Azure Functions](../azure-functions/functions-overview.md) ke směrování simulované telemetrie ze [IoT Hub](../iot-hub/about-iot-hub.md) zařízení do digitálních vlastností
* Rozšíří změny přes dodaný **graf**zpracováním digitálních dvojitých oznámení pomocí Azure Functions, koncových bodů a tras.

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Nastavit relaci Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>Začínáme se scénářem vytváření

Vzorový projekt použitý v tomto kurzu představuje reálný **scénář stavby**, který obsahuje podlahovou místnost, místnost a termostat zařízení. Tyto součásti budou digitálně zastoupeny v instanci digitálních vláken Azure, která se pak připojí k [IoT Hub](../iot-hub/about-iot-hub.md), [Event Grid](../event-grid/overview.md)a dvěma [funkcím Azure](../azure-functions/functions-overview.md) , aby se usnadnil pohyb dat.

Níže je diagram znázorňující celý scénář. 

Nejprve vytvoříte instanci digitálních vláken Azure (**oddíl A** v diagramu) a pak nastavíte tok dat telemetrie do digitálních vláken (**šipka B**) a pak nastavíte šíření dat pomocí grafu s dvojitou smyčkou (**šipka C**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Obrázek celého scénáře stavby Znázorňuje tok dat ze zařízení do IoT Hub, prostřednictvím funkce Azure (šipka B) k instanci digitálních vláken Azure (oddíl A), pak prostřednictvím Event Grid na jinou funkci Azure pro zpracování (šipka C).":::

Pokud chcete pracovat v tomto scénáři, budete pracovat s komponentami předem napsané ukázkové aplikace, kterou jste si stáhli dříve.

Tady jsou komponenty implementované ukázkovou aplikací *AdtSampleApp* scénář vytváření:
* Ověřování zařízení 
* Příklady použití [rozhraní .NET (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) (najdete v *CommandLoop.cs*)
* Rozhraní konzoly pro volání rozhraní API digitálních vláken Azure
* *SampleClientApp* – ukázkové řešení digitálních vláken Azure
* *SampleFunctionsApp* – aplikace Azure Functions, která aktualizuje graf digitálních vláken Azure v důsledku telemetrie z IoT Hub a událostí digitálních vláken Azure

Ukázkový projekt obsahuje také interaktivní autorizační komponentu. Pokaždé, když projekt spustíte, otevře se okno prohlížeče s výzvou k přihlášení pomocí účtu Azure.

### <a name="instantiate-the-pre-created-twin-graph"></a>Vytvoření instance předem vytvořeného grafu s dvojitou čárkou

Nejprve použijete řešení *AdtSampleApp* z ukázkového projektu k sestavení části digitálních vláken Azure v rámci kompletního a koncového scénáře (**oddíl A**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="Výňatek z kompletního vývojového scénáře – část s digitálním výsledkem a, instance digitálního vlákna Azure":::

V okně aplikace Visual Studio, kde je otevřen projekt _**AdtE2ESample**_ , spusťte projekt pomocí tohoto tlačítka na panelu nástrojů:

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Tlačítko Start pro Visual Studio (projekt SampleClientApp)":::

Otevře se okno konzoly, provede se ověřování a počká na příkaz. V této konzole spusťte následující příkaz pro vytvoření instance ukázkového řešení digitálních vláken Azure.

> [!IMPORTANT]
> Pokud již máte digitální vazby a vztahy v instanci digitálních vláken Azure, spusťte tento příkaz a nahraďte je dvojitými zprávami a relacemi pro ukázkový scénář.

```cmd/sh
SetupBuildingScenario
```

Výstupem tohoto příkazu je série potvrzujících zpráv, [**protože se vytvářejí**](concepts-twins-graph.md) a připojují ve vaší instanci digitálních vláken Azure: podlahu s názvem *floor1*, místnost s názvem *room21*a snímač teploty s názvem *thermostat67*. Tyto digitální vlákna reprezentují entity, které by existovaly ve skutečném prostředí.

Jsou propojeny prostřednictvím vztahů s následujícím [**dvojitým grafem**](concepts-twins-graph.md). Dvojitý graf představuje prostředí jako celek, včetně toho, jak entity vzájemně spolupracují a vzájemně souvisí.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="Graf, který znázorňuje, že floor1 obsahuje room21 a room21 obsahuje thermostat67" border="false":::

Můžete ověřit, které vlákna byly vytvořeny spuštěním následujícího příkazu, který se dotazuje připojené instance digitálního vlákna Azure pro všechny digitální vlákna, které obsahuje:

```cmd/sh
Query
```

Potom můžete zastavit běh projektu. Nechejte řešení otevřené v aplikaci Visual Studio, i když ho budete dál používat v celém kurzu.

## <a name="set-up-the-sample-function-app"></a>Nastavení ukázkové aplikace Function App

Dalším krokem je nastavení [aplikace Azure Functions](../azure-functions/functions-overview.md) , která se bude používat v celém rámci tohoto kurzu ke zpracování dat. Aplikace Function App, *SampleFunctionsApp*, obsahuje dvě funkce:
* *ProcessHubToDTEvents*: zpracovává příchozí IoT Hub data a podle toho aktualizuje digitální vlákna Azure.
* *ProcessDTRoutedData*: zpracovává data z digitálních vláken a aktualizuje nadřazených vláken v rámci digitálních vláken Azure odpovídajícím způsobem.

V této části publikujete předem napsanou aplikaci funkcí a zajistěte, aby aplikace Function App měla přístup k digitálním událostem Azure pomocí přiřazení Azure Active Directory (Azure AD). Dokončením těchto kroků umožníte zbývající část kurzu používat funkce uvnitř aplikace Function App. 

### <a name="publish-the-app"></a>Publikování aplikace

Zpět v okně aplikace Visual Studio, kde je otevřen projekt _**AdtE2ESample**_ , v podokně *Průzkumník řešení* klikněte pravým tlačítkem myši na soubor projektu _**SampleFunctionsApp**_ a stiskněte **publikovat**.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Visual Studio: publikování projektu":::

Na následující stránce *publikování* ponechte výchozí cílový výběr **Azure** a potom klikněte na tlačítko *Další*. 

V případě konkrétního cíle zvolte **Azure Function App (Windows)** a stiskněte tlačítko *Další*.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Publikování funkce Azure v aplikaci Visual Studio: konkrétní cíl":::

Na stránce *funkce instance* vyberte své předplatné. To by mělo naplnit pole *skupinami prostředků* v rámci vašeho předplatného.

Vyberte skupinu prostředků vaší instance a stiskněte *+ vytvořit novou funkci Azure Functions...*.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Publikování funkce Azure Functions v aplikaci Visual Studio: instance Functions (před aplikací Function App)":::

V *Function App (Windows) – vytvořit nové* okno vyplňte pole následujícím způsobem:
* **Název** je název plánu spotřeby, který Azure použije k hostování vaší Azure Functions aplikace. Tím se také stane název aplikace Function App, která obsahuje vaši skutečnou funkci. Můžete zvolit vlastní jedinečnou hodnotu nebo ponechat výchozí návrh.
* Ujistěte se, že **předplatné** odpovídá předplatnému, které chcete použít. 
* Ujistěte se, že **Skupina prostředků** má skupinu prostředků, kterou chcete použít.
* Ponechat **typ plánu** jako *spotřebu*
* Vyberte **umístění** , které odpovídá umístění vaší skupiny prostředků.
* Vytvořte nový prostředek **Azure Storage** pomocí odkazu *New...* . Nastavte umístění tak, aby odpovídalo vaší skupině prostředků, použijte jiné výchozí hodnoty a stiskněte OK.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Publikování funkce Azure v aplikaci Visual Studio: Function App (Windows) – vytvořit nový":::

Potom vyberte **Vytvořit**.

To by mělo vrátit se zpátky na stránku *instance Functions* , kde je vaše nová aplikace Functions teď viditelná pod vaší skupinou prostředků. *Zakončete*volání.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Publikování funkce Azure Functions v aplikaci Visual Studio: instance Functions (po aplikaci Function App)":::

V podokně *publikovat* , které se otevře zpátky v hlavním okně sady Visual Studio, zaškrtněte políčko všechny informace vypadají správně a vyberte **publikovat**.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Publikování funkce Azure v aplikaci Visual Studio: publikování":::

> [!NOTE]
> Může se zobrazit automaticky otevírané okno: :::image type="content" source="media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="publikovat funkci Azure v aplikaci Visual Studio: přihlašovací údaje pro publikování" border="false":::
> Pokud ano, vyberte **pokus o načtení přihlašovacích údajů z Azure** a **uložte**je.
>
> Pokud se zobrazí upozornění, že *vaše verze modulu runtime Functions neodpovídá verzi běžící v Azure*, postupujte podle pokynů pro upgrade na nejnovější verzi modulu runtime Azure Functions. K tomuto problému může dojít, pokud používáte starší verzi sady Visual Studio, než kterou jste doporučili v části *požadavky* na začátku tohoto kurzu.

### <a name="assign-permissions-to-the-function-app"></a>Přiřazení oprávnění k aplikaci Function App

Chcete-li povolit aplikaci Function App přístup k digitálním událostem Azure, je dalším krokem konfigurace nastavení aplikace, přiřazení aplikace identitě spravované systémem a udělení této oprávnění *vlastníka* identity v instanci digitálních vláken Azure.

V Azure Cloud Shell pomocí následujícího příkazu nastavte nastavení aplikace, které vaše aplikace Function App použije k odkazování na vaši instanci digitálního vlákna.

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-digital-twin-instance-URL>"
```

Pomocí následujícího příkazu vytvořte identitu spravovanou systémem. Poznamenejte si pole *principalId* ve výstupu.

```azurecli-interactive
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Pomocí hodnoty *principalId* v následujícím příkazu přiřaďte identitu aplikace Function App k roli *vlastníka* pro instanci digitálního vlákna Azure:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

Výsledkem tohoto příkazu jsou informace o přiřazení role, kterou jste vytvořili. Aplikace Function App má teď oprávnění pro přístup k instanci digitálních vláken Azure.

## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>Zpracovat simulovanou telemetrii z IoT Hubho zařízení

Graf digitálních vláken Azure by měl být řízený telemetrie z reálných zařízení. 

V tomto kroku připojíte simulované zařízení, které je zaregistrované v [IoT Hub](../iot-hub/about-iot-hub.md) k digitálnímu vlákna, které představuje v Azure Digital autovlákna. Když simulované zařízení vysílá telemetrii, budou data směrována prostřednictvím funkce *ProcessHubToDTEvents* Azure, která v digitálním vlákna spustí odpovídající aktualizaci. Tímto způsobem je digitální neaktuálnost v reálném čase díky datům reálného zařízení. V rámci digitálních vláken Azure se proces nasměrování dat událostí z jednoho místa na jiný nazývá [**Směrování událostí**](concepts-route-events.md).

K tomu dochází v této části koncového scénáře (**šipka B**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="Výňatek z celého scénáře sestavování grafického zvýraznění – šipka B, prvky před digitálním výsledkem Azure: zařízení, IoT Hub a první funkce Azure":::

Tady jsou akce, které dokončíte pro nastavení připojení tohoto zařízení:
1. Vytvoření služby IoT Hub, která bude spravovat simulované zařízení
2. Připojte službu IoT Hub ke správné funkci Azure tím, že nastavíte odběr události.
3. Registrace simulovaného zařízení ve službě IoT Hub
4. Spuštění simulovaného zařízení a generování telemetrie
5. Dotazování na živé výsledky Azure pro zobrazení živých výsledků

### <a name="create-an-iot-hub-instance"></a>Vytvoření instance IoT Hubu

Digitální vlákna Azure je navržená tak, aby fungovala vedle [IoT Hub](../iot-hub/about-iot-hub.md), což je služba Azure pro správu zařízení a jejich dat. V tomto kroku nastavíte centrum IoT, které bude spravovat ukázkové zařízení v tomto kurzu.

V Azure Cloud Shell pomocí tohoto příkazu vytvořte novou IoT Hub:

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

Výstupem tohoto příkazu jsou informace o vytvořeném službě IoT Hub.

Uložte název, který jste přiřadili do služby IoT Hub. Budete jej později potřebovat.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>Připojení centra IoT ke službě Azure Functions

V dalším kroku připojte službu IoT Hub k funkci *ProcessHubToDTEvents* Azure v aplikaci Function App, kterou jste publikovali dříve, aby se data mohla přesměrovat ze zařízení do IoT Hub prostřednictvím funkce, která aktualizuje digitální vlákna Azure.

Pokud to chcete provést, vytvoříte **odběr událostí** v IoT Hub s funkcí Azure jako koncovým bodem. Tato možnost se přihlásí k odběru událostí, která se děje v IoT Hub.

V [Azure Portal](https://portal.azure.com/)přejděte do svého nově vytvořeného centra IoT tak, že na horním panelu vyhledávání vyhledáte jeho název. V nabídce centra vyberte možnost *události* a vyberte *+ odběr události*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Azure Portal: odběr událostí IoT Hub":::

Tím se zobrazí stránka *vytvořit odběr události* .

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Azure Portal: Vytvoření odběru událostí":::

Vyplňte pole následujícím způsobem (ve výchozím nastavení se nezobrazují pole, která jsou vyplněna):
* *Podrobnosti*  >  odběru události **Název**: zadejte název předplatného události.
* *Podrobnosti o tématu*  >  **Název systémového tématu**: zadejte název, který se má použít pro systémové téma. 
* *typy událostí*  >  **Filtrovat na typy událostí**: z možností nabídky vyberte *telemetrie zařízení* .
* Podrobnosti koncového *bodu*  >  **Typ koncového bodu**: z možností nabídky vyberte *Azure Function* .
* Podrobnosti koncového *bodu*  >  **Koncový bod**: stiskněte odkaz *Vybrat koncový bod* . Otevře se okno *Vybrat funkci Azure* : :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Azure Portal odběr událostí: vybrat funkci Azure Functions" border="false":::
    - Vyplňte svoje **předplatné**, **skupinu prostředků**, **aplikaci funkcí** a **funkci** (*ProcessHubToDTEvents*). Některé z těchto možností mohou automaticky být vyplněny po výběru předplatného.
    - **Potvrďte výběr**.

Zpátky na stránce *vytvořit odběr události* klikněte na **vytvořit**.

### <a name="register-the-simulated-device-with-iot-hub"></a>Zaregistrujte simulované zařízení s IoT Hub 

V této části se vytvoří reprezentace zařízení v IoT Hub s ID *thermostat67*. Simulované zařízení se k tomu připojí a to znamená, jak budou události telemetrie ze zařízení přecházet do IoT Hub, kde se přihlašuje předplacená funkce Azure z předchozího kroku, která je připravená na výběr událostí a pokračování v zpracování.

V Azure Cloud Shell vytvořte v IoT Hub zařízení pomocí následujícího příkazu:

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

Výstupem jsou informace o vytvořeném zařízení.

### <a name="configure-and-run-the-simulation"></a>Konfigurace a spuštění simulace

Dále nakonfigurujte simulátor zařízení, aby odesílal data do instance IoT Hub.

Začněte získáním *připojovacího řetězce centra IoT* pomocí tohoto příkazu:

```azurecli
az iot hub show-connection-string -n <your-IoT-hub-name>
```

Pak Získejte *připojovací řetězec zařízení* pomocí tohoto příkazu:

```azurecli
az iot hub device-identity show-connection-string --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

Tyto hodnoty připojíte do kódu simulátoru zařízení v místním projektu a připojíte simulátor do tohoto zařízení IoT Hub a IoT Hub.

V novém okně sady Visual Studio otevřete (ze stažené složky řešení) _simulátor zařízení > **DeviceSimulator. sln**_.

>[!NOTE]
> Nyní byste měli mít dvě okna sady Visual Studio, jednu s _**DeviceSimulator. sln**_ a jednu z předchozích verzí _**AdtE2ESample. sln**_.

V podokně *Průzkumník řešení* v tomto novém okně sady Visual Studio vyberte možnost _DeviceSimulator/**AzureIoTHub.cs** _ a otevřete ji v okně pro úpravy. Změňte následující hodnoty připojovacího řetězce na hodnoty, které jste shromáždili výše:

```csharp
connectionString = <Iot-hub-connection-string>
deviceConnectionString = <device-connection-string>
```

Soubor uložte.

Nyní chcete-li zobrazit výsledky simulace dat, kterou jste nastavili, spusťte projekt **DeviceSimulator** pomocí tohoto tlačítka na panelu nástrojů:

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Tlačítko Start pro Visual Studio (projekt DeviceSimulator)":::

Otevře se okno konzoly, ve kterém se zobrazí simulované zprávy telemetrie o teplotě. Tyto zprávy jsou odesílány do IoT Hub, kde jsou následně vydány a zpracovány funkcí Azure Functions.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Výstup konzoly simulátoru zařízení, který ukazuje, že se posílá telemetrie na teplotu":::

V této konzole nemusíte nic dalšího dělat, ale při provádění dalších kroků ho nechte spuštěný.

### <a name="see-the-results-in-azure-digital-twins"></a>Podívejte se na výsledky v části digitální vlákna Azure

Funkce *ProcessHubToDTEvents* , kterou jste publikovali dříve, naslouchá datům IoT Hub a volá rozhraní API digitálních vláken Azure s cílem aktualizovat vlastnost *teploty* na *thermostat67* .

Pokud se chcete podívat na data z oblasti digitálních vláken Azure, přejděte do okna aplikace Visual Studio, kde je projekt _**AdtE2ESample**_ otevřený a spusťte projekt.

V okně konzoly projektu, které se otevře, spusťte následující příkaz, který získá teploty hlášené pomocí digitálního vlákna *thermostat67*:

```cmd
ObserveProperties thermostat67 Temperature
```

Měli byste vidět živá aktualizované teploty *z instance digitálních vláken Azure* , které jsou protokolovány do konzoly každých 10 sekund.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="Výstup konzoly zobrazující protokol teplotních zpráv z digitálního vlákna thermostat67":::

Jakmile ověříte, že toto ověření funguje úspěšně, můžete zastavit spouštění obou projektů. Nechejte okna sady Visual Studio otevřené, jak je budete dál používat ve zbývající části kurzu.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>Šíření událostí digitálních vláken Azure pomocí grafu

V tomto kurzu jste se seznámili s tím, jak se digitální vlákna Azure dají aktualizovat z externích dat zařízení. V dalším kroku uvidíte, jak se můžou změny jedné digitální funkce šířit prostřednictvím grafu digitálních vláken Azure – jinými slovy, jak aktualizovat vlákna z interních dat služby.

K tomu použijete funkci *ProcessDTRoutedData* Azure k aktualizaci vlákna v *místnosti* , když se aktualizuje připojený *termostat* . K tomu dochází v této části koncového scénáře (**šipka C**):

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="Výňatek z celého scénáře sestavování grafického zvýraznění – šipka C, prvky po digitálních událostech Azure: Event Grid a druhá funkce Azure":::

Tady jsou akce, které dokončíte pro nastavení tohoto toku dat:
1. Vytvoření koncového bodu digitálních vláken Azure, který připojuje instanci k Event Grid
2. Nastavení trasy v rámci digitálních vláken Azure pro posílání dvojitých událostí změny vlastností do koncového bodu
3. Nasaďte aplikaci Azure Functions, která na koncovém bodu naslouchá (prostřednictvím [Event Grid](../event-grid/overview.md)), a odpovídajícím způsobem aktualizuje další vlákna.
4. Spuštění simulovaného zařízení a dotazování digitálních vláken Azure na výsledky zobrazení živých výsledků

### <a name="set-up-endpoint"></a>Nastavení koncového bodu

[Event Grid](../event-grid/overview.md) je služba Azure, která pomáhá směrovat a doručovat události přicházející ze služeb Azure na jiné místo v rámci Azure. Můžete vytvořit [téma Event gridu](../event-grid/concepts.md) , ve kterém se budou shromažďovat určité události ze zdroje, a potom můžou předplatitelé naslouchat na tématu, aby mohli přijímat události při jejich absolvování.

V této části vytvoříte téma Event Grid a potom v rámci digitálních vláken Azure, který odkazuje (odesílá události) do tohoto tématu, vytvoříte koncový bod. 

V Azure Cloud Shell spusťte následující příkaz, který vytvoří téma Event gridu:

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> Pokud chcete vypsat seznam názvů oblastí Azure, které se dají předávat do příkazů v Azure CLI, spusťte tento příkaz:
> ```azurecli
> az account list-locations -o table
> ```

Výstupem tohoto příkazu jsou informace o tématu, které jste vytvořili.

V dalším kroku vytvořte koncový bod digitálních vláken Azure, který odkazuje na téma Event gridu. Použijte následující příkaz, podle potřeby vyplňte pole zástupné symboly:

```azurecli
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

Výstupem tohoto příkazu jsou informace o koncovém bodu, který jste vytvořili.

Spuštěním následujícího příkazu můžete také ověřit, jestli se vytvoření koncového bodu dokončilo, a to tak, že spustíte následující příkaz, který se dotazuje na instanci digitálního vlákna Azure pro tento

```azurecli
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

Vyhledejte `provisioningState` pole ve výstupu a zkontrolujte, zda je hodnota "úspěch".

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="Výsledek dotazu koncového bodu zobrazující koncový bod s provisioningStateem úspěšného":::

Uložte názvy, které jste zadali do svého tématu Event gridu, a koncový bod digitálních vláken Azure. Později je budete používat.

### <a name="set-up-route"></a>Nastavení trasy

Dále vytvořte trasu digitálních vláken Azure, která odesílá události do koncového bodu digitálních vláken Azure, který jste právě vytvořili.

```azurecli
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

Výstupem z tohoto příkazu jsou některé informace o trasách, které jste vytvořili.

#### <a name="connect-the-function-to-event-grid"></a>Připojení funkce k Event Grid

Dále přihlaste *ProcessDTRoutedData* funkci Azure do tématu Event gridu, které jste vytvořili dříve, aby data telemetrie mohla být z *thermostat67* vyplněna prostřednictvím tématu Event gridu do funkce, která se vrátí do digitálních vláken Azure a odpovídajícím způsobem aktualizuje *room21* .

Pokud to chcete provést, vytvoříte **Event Grid předplatné** z tématu Event gridu do *ProcessDTRoutedData* funkce Azure jako koncový bod.

V [Azure Portal](https://portal.azure.com/)přejděte na téma Event gridu tak, že na horním panelu vyhledávání vyhledáte jeho název. Vyberte *+ Odběr události*.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Azure Portal: odběr událostí Event Grid":::

Postup vytvoření odběru událostí je podobný jako při přihlášení k odběru první funkce Azure, která IoT Hub dříve v tomto kurzu. Tentokrát nemusíte zadávat *telemetrii zařízení* jako typ události, která se má naslouchat, a Vy se připojíte k jiné funkci Azure Functions.

Na stránce *vytvořit odběr události* vyplňte pole následujícím způsobem (pole vyplněná ve výchozím nastavení nejsou zmíněná):
* *Podrobnosti*  >  odběru události **Název**: zadejte název předplatného události.
* Podrobnosti koncového *bodu*  >  **Typ koncového bodu**: z možností nabídky vyberte *Azure Function* .
* Podrobnosti koncového *bodu*  >  **Koncový bod**: stiskněte odkaz *Vybrat koncový bod* . Otevře se okno *Vybrat Azure Function* :
    - Vyplňte svoje **předplatné**, **skupinu prostředků**, **aplikaci funkcí** a **funkci** (*ProcessDTRoutedData*). Některé z těchto možností mohou automaticky být vyplněny po výběru předplatného.
    - **Potvrďte výběr**.

Zpátky na stránce *vytvořit odběr události* klikněte na **vytvořit**.

### <a name="run-the-simulation-and-see-the-results"></a>Spustit simulaci a zobrazit výsledky

Nyní můžete spustit simulátor zařízení, aby se aktivoval nový tok událostí, který jste nastavili. V okně aplikace Visual Studio, kde je otevřený projekt _**DeviceSimulator**_ , a spusťte projekt.

Podobně jako v případě, že jste dříve spustili simulátor zařízení, okno konzoly otevře a zobrazí simulované zprávy telemetrie o teplotě. Tyto události procházejí tokem, který jste nastavili dříve, abyste aktualizovali *thermostat67u* , a pak projdete tokem, který jste v poslední době nastavili, aby se shodovala shoda s *room21* .

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="Výstup konzoly simulátoru zařízení, který ukazuje, že se posílá telemetrie na teplotu":::

V této konzole nemusíte nic dalšího dělat, ale při provádění dalších kroků ho nechte spuštěný.

Pokud se chcete podívat na data z oblasti digitálních vláken Azure, přejděte do okna aplikace Visual Studio, kde je projekt _**AdtE2ESample**_ otevřený, a spusťte projekt.

V okně konzoly projektu, které se otevře, spusťte následující příkaz, který získá teploty hlášené digitálním **both** *thermostat67em* a digitálním *room21em*.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

Měli byste vidět živá aktualizované teploty *z instance digitálních vláken Azure* , které jsou protokolovány do konzoly každých 10 sekund. Všimněte si, že teplota pro *room21* se aktualizuje tak, aby odpovídala aktualizacím *thermostat67*.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="Výstup konzoly zobrazující protokol teplotních zpráv, z termostatu a místnosti":::

Jakmile ověříte, že toto ověření funguje úspěšně, můžete zastavit spouštění obou projektů. Okna sady Visual Studio můžete také zavřít, protože kurz je nyní dokončen.

## <a name="review"></a>Opakování

Tady je přehled scénáře, který jste vytvořili v tomto kurzu.

1. Instance digitálních vláken Azure představuje v následujícím diagramu podlahu, místnost a termostat (reprezentované **částí a** v diagramu níže).
2. Telemetrii simulovaného zařízení se odesílá do IoT Hub, kde *ProcessHubToDTEvents* funkce Azure naslouchá událostem telemetrie. Funkce *ProcessHubToDTEvents* Azure používá informace v těchto událostech k nastavení vlastnosti *teploty* v *thermostat67* (**šipka B** v diagramu).
3. Události změny vlastností v digitálních událostech Azure jsou směrované do tématu Event gridu, kde *ProcessDTRoutedData* funkce Azure naslouchá událostem. Funkce *ProcessDTRoutedData* Azure používá informace v těchto událostech k nastavení vlastnosti *teploty* v *room21* (**šipka C** v diagramu).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="Obrázek celého scénáře stavby Znázorňuje tok dat ze zařízení do IoT Hub, prostřednictvím funkce Azure (šipka B) k instanci digitálních vláken Azure (oddíl A), pak prostřednictvím Event Grid na jinou funkci Azure pro zpracování (šipka C).":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky vytvořené v tomto kurzu nepotřebujete, odstraňte je pomocí těchto kroků. 

Pomocí Azure Cloud Shell můžete odstranit všechny prostředky Azure ve skupině prostředků pomocí příkazu [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) . Tím odeberete skupinu prostředků. instance digitálního vlákna Azure; Centrum IoT a registrace zařízení v centru téma Event Grid a související odběry; a aplikace Azure Functions, včetně přidružených prostředků, jako je úložiště.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

V dalším kroku odstraňte registraci aplikace Azure AD, kterou jste vytvořili pro klientskou aplikaci, pomocí tohoto příkazu:

```azurecli
az ad app delete --id <your-application-ID>
```

Nakonec odstraňte ukázkovou složku projektu, kterou jste stáhli z místního počítače.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili kompletní scénář, který zobrazuje digitální vlákna Azure, která se řídí daty živého zařízení.

Pak začněte hledat v dokumentaci konceptu, kde najdete další informace o prvcích, se kterými jste pracovali v tomto kurzu:
* [*Koncepty: vlastní modely*](concepts-models.md)

Případně můžete v tomto kurzu podrobněji podrobnější informace o procesech, a to spuštěním článků s postupy:
* [*Postupy: použití rozhraní příkazového řádku Azure Digital zdvojené*](how-to-use-cli.md)
