---
title: Aktivovat události v pracovních postupech ML
titleSuffix: Azure Machine Learning
description: Naučte se spouštět aplikace řízené událostmi, procesy nebo postupy CI/CD na základě Azure Machine Learningch událostí a zjednodušit tak životní cyklus ML.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: 236cc46bb6f9e5ed95e4a49068ac41ae77a736f5
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982866"
---
# <a name="trigger-event-driven-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Aktivovat aplikace řízené událostmi, procesy nebo postupy CI/CD na základě Azure Machine Learningch událostí (Preview)

V tomto článku se dozvíte, jak nastavit aplikace řízené událostmi, procesy nebo postupy CI/CD na základě Azure Machine Learningch událostí, jako jsou například e-maily s oznámením o selhání nebo spuštění kanálu ML, pokud jsou v [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/)zjištěny určité podmínky. 

Azure Machine Learning spravuje celý životní cyklus procesu strojového učení, včetně školení modelů, nasazení modelů a monitorování. Event Grid můžete použít k reakci na Azure Machine Learning události, jako je dokončení školicích běhů, registrace a nasazení modelů a detekce posunu dat pomocí moderních architektur bez serveru. Pak se můžete přihlásit k odběru a spotřebovávat události, jako je například změna stavu spuštění, dokončení běhu, registrace modelu, nasazení modelu a detekce posunování dat v pracovním prostoru.

Kdy použít Event Grid pro akce řízené událostmi:
* Odeslat e-maily při spuštění a dokončení spuštění
* Použití funkce Azure Function po registraci modelu
* Streamování událostí z Azure Machine Learning do různých koncových bodů
* Aktivace kanálu ML při zjištění posunu

> [!NOTE] 
> V současné době se události runStatusChanged aktivují jenom v případě, že se stav spuštění **nezdařil** .

## <a name="prerequisites"></a>Požadavky
Chcete-li použít Event Grid, potřebujete přístup přispěvatele nebo vlastníka k pracovnímu prostoru Azure Machine Learning, ve kterém vytvoříte události.

## <a name="the-event-model--types"></a>Typy & modelu událostí

Azure Event Grid čte události ze zdrojů, jako jsou Azure Machine Learning a další služby Azure. Tyto události se pak odesílají do obslužných rutin událostí, jako je například Azure Event Hubs, Azure Functions, Logic Apps a další. Následující diagram ukazuje, jak Event Grid propojuje zdroje a obslužné rutiny, ale není vyčerpávajícím seznamem podporovaných integrací.

![Azure Event Grid funkční model](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Další informace o zdrojích událostí a obslužných rutinách událostí najdete v tématu [co je Event Grid?](/azure/event-grid/overview).

### <a name="event-types-for-azure-machine-learning"></a>Typy událostí pro Azure Machine Learning

Azure Machine Learning poskytuje události v různých místech životního cyklu machine learningu: 

| Typ události | Popis |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Vyvoláno, když je dokončeno spuštění experimentu machine learningu. |
| `Microsoft.MachineLearningServices.ModelRegistered` | Vyvolá se v případě, že je model strojového učení zaregistrován v pracovním prostoru. |
| `Microsoft.MachineLearningServices.ModelDeployed` | Vyvolá se, když se dokončí nasazení odvozené služby s jedním nebo více modely. |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Vyvolá se, když se dokončí úloha detekce posunu dat pro dvě datové sady. |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Vyvolá se, když se změní stav spuštění. aktuálně se aktivuje jenom v případě, že se stav spuštění nezdařil. |

### <a name="filter--subscribe-to-events"></a>Filtrovat & přihlášení k odběru událostí

Tyto události jsou publikovány prostřednictvím Azure Event Grid. Pomocí Azure Portal, PowerShellu nebo Azure CLI můžou zákazníci snadno se přihlásit k odběru událostí [zadáním jednoho nebo více typů událostí a podmínek filtrování](/azure/event-grid/event-filtering). 

Při nastavování událostí můžete použít filtry, aby se aktivovaly jenom pro konkrétní data událostí. V následujícím příkladu můžete pro události změny stavu spuštění filtrovat podle typů spuštění. Událost se aktivuje pouze v případě, že jsou splněna kritéria. Další informace o datech událostí, která můžete filtrovat podle, najdete v tématu [schéma Event gridu Azure Machine Learning](/azure/event-grid/event-schema-machine-learning) . 

Odběry pro události Azure Machine Learning jsou chráněny pomocí řízení přístupu na základě role (RBAC). Jenom [Přispěvatel nebo vlastník](how-to-assign-roles.md#default-roles) pracovního prostoru můžou vytvářet, aktualizovat a odstraňovat odběry událostí.  Filtry lze použít na odběry událostí při [vytváření](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) odběru události nebo později. 


1. Přejít na Azure Portal, vyberte nové předplatné nebo existující. 

1. Vyberte kartu filtry a přejděte dolů k části Rozšířené filtry. Pro **klíč** a **hodnotu**zadejte typy vlastností, podle kterých chcete filtrovat. Tady vidíte, že událost se aktivuje jenom v případě, že typ spuštění je spuštění kanálu nebo krok kanálu.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="Filtrovat události":::


+ **Filtrovat podle typu události:** Odběr události může určit jeden nebo více Azure Machine Learningch typů událostí.

+ **Filtrovat podle předmětu události:** Azure Event Grid podporuje filtry pro závislosti na __začátku__ a __konci__ shody, takže se do odběratele doručí události s odpovídajícím subjektem. Různé události strojového učení mají jiný formát předmětu.

  | Typ události | Formát předmětu | Ukázkový předmět |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Rozšířené filtrování**: Azure Event Grid podporuje také rozšířené filtrování založené na publikovaných schématech událostí. Podrobnosti o schématu události Azure Machine Learning najdete v [Azure Event Grid schématu událostí pro Azure Machine Learning](../event-grid/event-schema-machine-learning.md).  Mezi příklady rozšířených filtrů, které můžete provádět, patří:

  Pro `Microsoft.MachineLearningServices.ModelRegistered` událost, pokud chcete filtrovat hodnotu značky modelu:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Další informace o tom, jak používat filtry, najdete v tématu [filtrování událostí pro Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="consume-machine-learning-events"></a>Využívání Machine Learningch událostí

Aplikace, které zpracovávají Machine Learning události, by měly dodržovat několik doporučených postupů:

> [!div class="checklist"]
> * Vzhledem k tomu, že je možné nakonfigurovat více předplatných pro směrování událostí ke stejné obslužné rutině události, je důležité, aby se události nepředpokládaly z konkrétního zdroje, ale pokud chcete zkontrolovat téma zprávy, abyste měli jistotu, že pochází z pracovního prostoru Machine Learning, který očekáváte.
> * Podobně ověřte, zda je typ eventType, který je připraven ke zpracování, a nepředpokládá se, že všechny události, které obdržíte, budou takové typy, které očekáváte.
> * Vzhledem k tomu, že zprávy mohou docházet mimo pořadí a po nějaké prodlevě, použijte pole ETag k pochopení, zda jsou informace o objektech stále aktuální.  Pomocí polí Sequencer můžete také pochopit pořadí událostí u libovolného konkrétního objektu.
> * Ignorujte pole, která nerozumíte. Tento postup vám pomůže zajistit odolný přístup k novým funkcím, které se v budoucnu můžou přidat.
> * Neúspěšné nebo zrušené operace Azure Machine Learning neaktivují událost. Například pokud nasazení modelu neproběhne úspěšně, Microsoft. MachineLearningServices. ModelDeployed se neaktivuje. Při návrhu aplikací Vezměte v úvahu takový režim selhání. Rozhraní Azure Machine Learning SDK, CLI nebo portál můžete vždy použít ke kontrole stavu operace a pochopení podrobných důvodů selhání.

Azure Event Grid umožňuje zákazníkům vytvářet nespojené obslužné rutiny zpráv, které mohou být aktivovány Azure Machine Learningmi událostmi. Mezi významné příklady obslužných rutin zpráv patří:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Kanál Azure Data Factory
* Obecné Webhooky, které můžou být hostované na platformě Azure nebo jinde

## <a name="set-up-in-azure-portal"></a>Nastavení v Azure Portal

1. Otevřete [Azure Portal](https://portal.azure.com) a přejít do pracovního prostoru Azure Machine Learning.

1. Na levém panelu vyberte možnost __události__ a potom vyberte možnost **odběry událostí**. 

    ![SELECT-events-in-Workspace. png](./media/how-to-use-event-grid/select-event.png)

1. Vyberte typ události, který se má spotřebovat. Například následující snímek obrazovky má __zaregistrovaný model__, byl __nasazen model__, __dokončen běh__a __byl zjištěn posun datových sad__:

    ![Přidat typ události](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Vyberte koncový bod, do kterého se má událost publikovat. Na následujícím snímku obrazovky je __centrum událostí__ vybraným koncovým bodem:

    ![rutina Select-Event-](./media/how-to-use-event-grid/select-event-handler.png)

Po potvrzení výběru klikněte na __vytvořit__. Po dokončení konfigurace budou tyto události vloženy do koncového bodu.


### <a name="set-up-with-the-cli"></a>Nastavení pomocí rozhraní příkazového řádku

Můžete buď nainstalovat nejnovější rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), nebo použít Azure Cloud Shell poskytované jako součást předplatného Azure.

Pokud chcete nainstalovat rozšíření Event Grid, použijte z rozhraní příkazového řádku následující příkaz:

```azurecli-interactive
az add extension --name eventgrid
```

Následující příklad ukazuje, jak vybrat předplatné Azure a vytvořit nový odběr události pro Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>Příklady

### <a name="example-send-email-alerts"></a>Příklad: odesílání e-mailových upozornění

Pomocí [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) můžete nakonfigurovat e-maily pro všechny vaše události. Přizpůsobte si podmínky a určete příjemce, aby bylo možné spolupracovat a povědomí mezi týmy spolupracují.

1. V Azure Portal přejdete do pracovního prostoru Azure Machine Learning a na levém panelu vyberete kartu události. Tady vyberte __Logic Apps__. 

    ![SELECT-Logic-AP](./media/how-to-use-event-grid/select-logic-ap.png)

1. Přihlaste se do uživatelského rozhraní aplikace logiky a jako typ tématu vyberte Machine Learning Service. 

    ![vybrat-téma-typ](./media/how-to-use-event-grid/select-topic-type.png)

1. Vyberte události, které mají být oznamovány. Například následující snímek obrazovky __RunCompleted__.

    ![SELECT-Event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Můžete použít metodu filtrování v části výše nebo přidat filtry, aby se aktivovala pouze aplikace logiky pro podmnožinu typů událostí. Na následujícím snímku obrazovky je použit __Filtr předpony__ __/datadriftID/runs/__ .

    ![filtr – události](./media/how-to-use-event-grid/filtering-events.png)

1. Dále přidejte krok pro použití této události a hledání e-mailu. K příjmu událostí můžete použít několik různých poštovních účtů. Můžete taky nakonfigurovat podmínky, kdy se má poslat e-mailové upozornění.

    ![vybrat e-mail – akce](./media/how-to-use-event-grid/select-email-action.png)

1. Vyberte __Odeslat e-mail__ a vyplňte parametry. V předmětu můžete zahrnout __Typ události__ a __téma__ , které vám pomůžou události filtrovat. Do textu zprávy můžete také přidat odkaz na stránku pracovního prostoru pro spuštění. 

    ![Konfigurace – e-mail – tělo](./media/how-to-use-event-grid/configure-email-body.png)

1. Pokud chcete tuto akci Uložit, vyberte **Uložit jako** v levém horním rohu stránky. Z pravého panelu, který se zobrazí, potvrďte vytvoření této akce.

    ![potvrdit-Logic-App-Create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Příklad: přeškolení triggerů dat

Modely se v průběhu času přejdou zastaralě a nezůstávají užitečné v kontextu, ve kterém běží. Jedním ze způsobů, jak zjistit, jestli je čas na reučení modelu, je detekce posunu dat. 

Tento příklad ukazuje, jak pomocí služby Event Grid s aplikací logiky Azure aktivovat rekurzi. V příkladu se aktivuje kanál Azure Data Factory, pokud dojde k posunu dat mezi školením modelu a obsluhou datových sad.

Než začnete, proveďte následující akce:

* Nastavení monitoru datové sady pro [detekci posunu dat]( https://aka.ms/datadrift) v pracovním prostoru
* Vytvoří publikovaný [kanál Azure Data Factory](https://docs.microsoft.com/azure/data-factory/).

V tomto příkladu se k kopírování souborů do úložiště objektů BLOB a spuštění publikovaného kanálu Machine Learning používá jednoduchý kanál Data Factory. Další informace o tomto scénáři najdete v tématu Postup nastavení [Machine Learning kroku v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)

![ADF – mlpipeline – fáze](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Začněte vytvářením aplikace logiky. Otevřete [Azure Portal](https://portal.azure.com), vyhledejte Logic Apps a vyberte vytvořit.

    ![hledání-logika – aplikace](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Vyplňte požadované informace. Pro zjednodušení prostředí použijte stejné předplatné a skupinu prostředků jako Azure Data Factory kanál a Azure Machine Learning pracovní prostor.

    ![nastavení-Logic-App-for-ADF](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. Po vytvoření aplikace logiky vyberte, __kdy dojde k události Event Grid prostředku__. 

    ![Výběr-Event-Grid-Trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Přihlaste se a vyplňte podrobnosti události. Nastavte __název prostředku__ na název pracovního prostoru. Nastavte __Typ události__ na __DatasetDriftDetected__.

    ![přihlášení a přidání – událost](./media/how-to-use-event-grid/login-and-add-event.png)

1. Přidejte nový krok a vyhledejte __Azure Data Factory__. Vyberte __vytvořit spuštění kanálu__. 

    ![Vytvoření-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Přihlaste se a zadejte publikovaný Azure Data Factory kanál, který se má spustit.

    ![určení – ADF – kanál](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Uložte a vytvořte aplikaci logiky pomocí tlačítka **Uložit** v levém horním rohu stránky. Pokud chcete zobrazit svou aplikaci, přejděte do pracovního prostoru v [Azure Portal](https://portal.azure.com) a klikněte na **události**.

    ![show-Logic-App – Webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Kanál Data Factory se teď aktivuje, když dojde k posunu. Zobrazte si podrobnosti o běhu posunu dat a kanálu Machine Learning na [novém portálu pracovního prostoru](https://ml.azure.com). 

![zobrazení v pracovním prostoru](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Příklad: nasazení modelu založeného na značkách

Objekt Azure Machine Learning modelu obsahuje parametry, na jejichž základě můžete nasazovat nasazení, jako je název modelu, verze, značka a vlastnost. Událost registrace modelu může aktivovat koncový bod a pomocí funkce Azure můžete nasadit model založený na hodnotě těchto parametrů.

Podívejte se například na [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) úložiště a postupujte podle kroků v souboru **Readme** .

## <a name="next-steps"></a>Další kroky

Další informace o Event Grid a poskytnutí Azure Machine Learningch událostí a try:

- [Informace o službě Event Grid](../event-grid/overview.md)

- [Schéma události pro Azure Machine Learning](../event-grid/event-schema-machine-learning.md)

