---
title: Azure App Service jako zdroj Event Grid
description: Tento článek popisuje, jak použít Azure App Service jako zdroj události Event Grid. Poskytuje schéma a odkazy na články týkající se kurzu a postupů.
services: event-grid
author: jasonfreeberg
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: jafreebe
ms.openlocfilehash: 0a24e8ba84739dbc1b5de5e0546a8fe0d2e826f1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83650704"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Azure App Service jako zdroj Event Grid

Tento článek poskytuje vlastnosti a schéma pro události Azure App Service. Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md). Nabízí také seznam rychlých startů a kurzů pro použití Azure App Service jako zdroje událostí.

## <a name="event-grid-event-schema"></a>Schéma událostí služby Event Grid

### <a name="available-event-types"></a>Dostupné typy událostí

Azure App Service emituje následující typy událostí.

|    Event Type                                             |    Description                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft. Web/weby. BackupOperationStarted             |    Aktivované při zahájení zálohování                             |
|    Microsoft. Web/weby. BackupOperationCompleted           |    Aktivované po dokončení zálohování                           |
|    Microsoft. Web/weby. BackupOperationFailed              |    Aktivované při selhání zálohování                              |
|    Microsoft. Web/weby. RestoreOperationStarted            |    Aktivuje se, když se spustí obnovení ze zálohy.        |
|    Microsoft. Web/weby. RestoreOperationCompleted          |    Aktivuje se, když se dokončí obnovení ze zálohy.      |
|    Microsoft. Web/weby. RestoreOperationFailed             |    Aktivuje se, když se nepovedlo obnovení ze zálohy.         |
|    Microsoft. Web/weby. SlotSwapStarted                    |    Aktivuje se při spuštění prohození slotu.                          |
|    Microsoft. Web/weby. SlotSwapCompleted                  |    Aktivuje se, když se dokončí prohození slotu.                      |
|    Microsoft. Web/weby. SlotSwapFailed                     |    Aktivuje se, když se nezdařil swap slot.                           |
|    Microsoft. Web/weby. SlotSwapWithPreviewStarted         |    Aktivuje se, když se spustí swap slot s náhledem.           |
|    Microsoft. Web/weby. SlotSwapWithPreviewCancelled       |    Aktivuje se, když se zruší výměna slotu s náhledem.    |
|    Microsoft. Web/weby. AppUpdated. restartuje se.               |    Aktivuje se při restartování lokality.                      |
|    Microsoft. Web/weby. AppUpdated. Stopped                 |    Aktivuje se, když se web zastavil.                          |
|    Microsoft. Web/weby. AppUpdated.ChangedAppSettings      |    Aktivuje se, když se změní nastavení aplikace webu.             |
|    Microsoft. Web/serverových farem. AppServicePlanUpdated        |    Aktivuje se při aktualizaci plánu App Service.                 |

### <a name="the-contents-of-an-event-response"></a>Obsah odpovědi na událost

Když se aktivuje událost, Služba Event Grid odešle data o této události do předplatného koncového bodu.
V této části najdete příklad toho, jak by tato data vypadala jako u každé události. Každá událost má následující data nejvyšší úrovně:

|     Vlastnost          |     Typ     |     Description                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    téma              |    řetězec    |    Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid.                                      |
|    závislosti            |    řetězec    |    Cesta definovaná vydavatelem k předmětu události                                                                                              |
|    Typ          |    řetězec    |    Jeden z registrovaných typů událostí pro tento zdroj události.                                                                                  |
|    eventTime          |    řetězec    |    Čas, kdy se událost generuje na základě času UTC poskytovatele.                                                                         |
|    id                 |    řetězec    |    Jedinečný identifikátor události                                                                                                            |
|    data               |    odkazy objektů    |    Data události služby Blob Storage.                                                                                                                    |
|    dataVersion        |    řetězec    |    Verze schématu datového objektu. Verzi schématu definuje vydavatel.                                                          |
|    metadataVersion    |    řetězec    |    Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid.    |

#### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.BackupOperationStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data: {
        "appEventTypeDetail": { "action": "Started" },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    }
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

Datový objekt obsahuje následující vlastnosti:

|    Vlastnost                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    odkazy objektů    |    Podrobnosti o akci u aplikace                                                                                       |
|    action                  |    řetězec    |    Typ akce operace                                                                                   |
|    name                    |    řetězec    |    název webu, který měl tuto událost                                                                          |
|    ID žádosti klienta         |    řetězec    |    ID žádosti klienta vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost         |
|    correlationRequestId    |    řetězec    |    ID žádosti o korelaci vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost    |
|    Identifikátor               |    řetězec    |    ID žádosti vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost                |
|    adresa                 |    řetězec    |    Adresa URL požadavku HTTP této operace                                                                                |
|    operace                    |    řetězec    |    Příkaz HTTP této operace                                                                                       |

#### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.RestoreOperationStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: { 
            action: "Started" 
        },
        siteName: "<site-name>",
        clientRequestId: "None",
        correlationRequestId: "None",
        requestId: "292f499d-04ee-4066-994d-c2df57b99198",
        address: "None",
        verb: "POST"
    }
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Datový objekt obsahuje následující vlastnosti:

|    Vlastnost                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    odkazy objektů    |    Podrobnosti o akci u aplikace                                                                                       |
|    action                  |    řetězec    |    Typ akce operace                                                                                   |
|    name                    |    řetězec    |    název webu, který měl tuto událost                                                                          |
|    ID žádosti klienta         |    řetězec    |    ID žádosti klienta vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost         |
|    correlationRequestId    |    řetězec    |    ID žádosti o korelaci vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost    |
|    Identifikátor               |    řetězec    |    ID žádosti vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost                |
|    adresa                 |    řetězec    |    Adresa URL požadavku HTTP této operace                                                                                |
|    operace                    |    řetězec    |    Příkaz HTTP této operace                                                                                       |

#### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Datový objekt obsahuje následující vlastnosti:

|    Vlastnost                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    odkazy objektů    |    Podrobnosti o akci u aplikace                                                                                       |
|    action                  |    řetězec    |    Typ akce operace                                                                                   |
|    name                    |    řetězec    |    název webu, který měl tuto událost                                                                          |
|    ID žádosti klienta         |    řetězec    |    ID žádosti klienta vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost         |
|    correlationRequestId    |    řetězec    |    ID žádosti o korelaci vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost    |
|    Identifikátor               |    řetězec    |    ID žádosti vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost                |
|    adresa                 |    řetězec    |    Adresa URL požadavku HTTP této operace                                                                                |
|    operace                    |    řetězec    |    Příkaz HTTP této operace                                                                                       |
|    sourceSlot              |    řetězec    |    Zdrojový slot prohození                                                                                       |

#### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapWithPreviewStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Datový objekt obsahuje následující vlastnosti:

|    Vlastnost                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    odkazy objektů    |    Podrobnosti o akci u aplikace                                                                                       |
|    action                  |    řetězec    |    Typ akce operace                                                                                   |
|    name                    |    řetězec    |    název webu, který měl tuto událost                                                                          |
|    ID žádosti klienta         |    řetězec    |    ID žádosti klienta vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost         |
|    correlationRequestId    |    řetězec    |    ID žádosti o korelaci vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost    |
|    Identifikátor               |    řetězec    |    ID žádosti vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost                |
|    adresa                 |    řetězec    |    Adresa URL požadavku HTTP této operace                                                                                |
|    operace                    |    řetězec    |    Příkaz HTTP této operace                                                                                       |

#### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated. restarted, AppUpdated. Stopped, AppUpdated. ChangedAppSettings

```js
{
    id: 'b74ea56b-2a3f-4de5-a5d7-38e60c81cf23',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.AppUpdated',
    eventTime: '2020-01-28T18:22:30.2760952Z',
    data: {
        appEventTypeDetail: {
            action: 'Stopped'
        },
        siteName: '<site-name>',
        clientRequestId: '64a5e0aa-7cee-4ff1-9093-b9197b820014',
        correlationRequestId: '25bb36a5-8f6c-4f04-b615-e9a0ee045756',
        requestId: 'f2e8eb3f-b190-42de-b99e-6acefe587374',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop',
        verb: 'POST'
    },
    topic: '/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Datový objekt má následující vlastnosti:

|    Vlastnost                |    Typ      |    Description                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    odkazy objektů    |    Podrobnosti o akci u aplikace                                                                                       |
|    action                  |    řetězec    |    Typ akce operace                                                                                   |
|    name                    |    řetězec    |    název webu, který měl tuto událost                                                                          |
|    ID žádosti klienta         |    řetězec    |    ID žádosti klienta vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost         |
|    correlationRequestId    |    řetězec    |    ID žádosti o korelaci vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost    |
|    Identifikátor               |    řetězec    |    ID žádosti vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost                |
|    adresa                 |    řetězec    |    Adresa URL požadavku HTTP této operace                                                                                |
|    operace                    |    řetězec    |    Příkaz HTTP této operace                                                                                       |

#### <a name="serverfarmsappserviceplanupdated"></a>Serverových farem. AppServicePlanUpdated

```js
{
   id: "56501672-9150-40e1-893a-18420c7fdbf7",
   subject: "/Microsoft.Web/serverfarms/<plan-name>",
   eventType: "Microsoft.Web.AppServicePlanUpdated",
   eventTime: "2020-01-28T18:22:23.5516004Z",
   data: {
        serverFarmEventTypeDetail: {
            stampKind: "Public",
            action: "Updated",
            status: "Started"
        },
        serverFarmId: "0",
        sku: {
            name: "P1v2",
            tier: "PremiumV2",
            size: "P1v2",
            family: "Pv2",
            capacity: 1
        },
        clientRequestId: "8f880321-a991-45c7-b743-6ff63fe4c004",
        correlationRequestId: "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        requestId: "b973a8e6-6949-4783-b44c-ac778be831bb",
        address: "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        verb: "PUT"
   },
   topic: "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
   dataVersion: "1",
   metaDataVersion: "1"
}
```

Datový objekt má následující vlastnosti:

|    Vlastnost                         |    Typ      |    Description                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appServicePlanEventTypeDetail    |    odkazy objektů    |    Podrobnosti o akci v plánu služby App Service                                                                          |
|    stampKind                        |    řetězec    |    Druh prostředí, ve kterém je plán služby App Service                                                                     |
|    action                           |    řetězec    |    Typ akce v plánu služby App Service                                                                            |
|    status                           |    řetězec    |    Stav operace v plánu služby App Service                                                                   |
|    skladové                              |    odkazy objektů    |    SKU plánu služby App Service                                                                                       |
|    name                             |    řetězec    |    název plánu služby App Service                                                                                      |
|    Úroveň                             |    řetězec    |    úroveň plánu služby App Service                                                                                      |
|    Velikost                             |    řetězec    |    velikost plánu služby App Service                                                                                      |
|    Rodina                           |    řetězec    |    řada plánu služby App Service                                                                                        |
|    Kapacita                         |    řetězec    |    kapacita plánu služby App Service                                                                                      |
|    action                           |    řetězec    |    Typ akce operace                                                                                   |
|    name                             |    řetězec    |    název webu, který měl tuto událost                                                                          |
|    ID žádosti klienta                  |    řetězec    |    ID žádosti klienta vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost         |
|    correlationRequestId             |    řetězec    |    ID žádosti o korelaci vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost    |
|    Identifikátor                        |    řetězec    |    ID žádosti vygenerované službou App Service pro operaci rozhraní API lokality, která aktivovala tuto událost                |
|    adresa                          |    řetězec    |    Adresa URL požadavku HTTP této operace                                                                                |
|    operace                             |    řetězec    |    Příkaz HTTP této operace                                                                                       |
