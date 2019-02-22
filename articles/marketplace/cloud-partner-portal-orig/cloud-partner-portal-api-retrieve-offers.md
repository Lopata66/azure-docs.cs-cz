---
title: Načtení nabízí rozhraní API | Dokumentace Microsoftu
description: Rozhraní API načte souhrnný seznam nabídky v rámci oboru názvů vydavatele.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: de9261548ec79e206b0db87caabc1fa4c9ad6771
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56591546"
---
<a name="retrieve-offers"></a>Nabízí načtení
===============

Načte souhrnný seznam nabídky v rámci oboru názvů vydavatele.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>Parametry identifikátoru URI
--------------

| **Název**         |  **Popis**                         |  **Datový typ** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identifikátor vydavatel, například `contoso` |   String    |
|  verze API-version     | Nejnovější verzi rozhraní API                    |    Datum        |
|  |  |


<a name="header"></a>Hlavička
------

|  **Název**        |         **Hodnota**       |
|  --------------- |       ----------------  |
|  Typ obsahu    | `application/json`      |
|  Autorizace   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Příklad těla
------------

### <a name="response"></a>Odpověď

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Vlastnosti textu odpovědi

|  **Název**       |       **Popis**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Určuje typ nabídky                                                                                           |
|  publisherId    | Identifikátor, který jednoznačně identifikuje vydavatele                                                                      |
|  status         | Stav nabídky. Seznam možných hodnot najdete v tématu [stav nabídky](#offer-status) níže.                         |
|  id             | Identifikátor GUID, který jednoznačně identifikuje nabídky v oboru názvů vydavatele.                                                    |
|  version        | Aktuální verze nabídky. Klient nemůže upravit vlastnost version. Se zvýší po jednotlivých publikování. |
|  Definice     | Obsahuje souhrnné zobrazení skutečné definice úlohy. Chcete-li získat podrobné definici, použijte [načtení konkrétní nabídky](./cloud-partner-portal-api-retrieve-specific-offer.md) rozhraní API. |
|  changedTime    | Čas UTC poslední úpravy nabídky                                                                              |
|  |  |


### <a name="response-status-codes"></a>Stavové kódy odezvy

| **Kód**  |  **Popis**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -Žádost byla úspěšně zpracována a všechny nabídky v části vydavatele vrácených do klienta.  |
|  400      | `Bad/Malformed request` Text – Chyba odpovědi může obsahovat další informace.                                    |
|  403      | `Forbidden` -Klienta nemá přístup k určený obor názvů.                                          |
|  404      | `Not found` -Zadaná entita neexistuje.                                                                 |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Název**                    | **Popis**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Nabídka se nikdy nepublikoval.                  |
|  NotStarted                  | Nabídka je nový, ale není spuštěna.                 |
|  WaitingForPublisherReview   | Nabídka je čekání na schválení vydavatele.         |
|  Spuštěno                     | Nabídka odeslání se zpracovává.             |
|  Úspěch                   | Nabídka odeslání dokončil zpracování.       |
|  Zrušeno                    | Odeslání nabídka byla zrušena.                   |
|  Selhalo                      | Nabídka odeslání se nezdařilo.                         |
|  |  |
