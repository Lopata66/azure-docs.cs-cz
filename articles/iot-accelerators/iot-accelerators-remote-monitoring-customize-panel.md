---
title: Přidání panelu do uživatelského rozhraní řešení vzdáleného monitorování – Azure | Microsoft Docs
description: V tomto článku se dozvíte, jak přidat nový panel do řídicího panelu ve webovém uživatelském rozhraní akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-javascript
ms.openlocfilehash: 207e5b8d9735004b6dc1a9662a3f66fbed8c2307
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422687"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidání vlastního panelu na řídicí panel ve webovém uživatelském rozhraní akcelerátoru řešení vzdáleného monitorování

V tomto článku se dozvíte, jak přidat nový panel na stránku řídicího panelu ve webovém uživatelském rozhraní akcelerátoru řešení vzdáleného monitorování. Článek popisuje:

- Jak připravit místní vývojové prostředí.
- Postup přidání nového panelu na stránku řídicího panelu ve webovém uživatelském rozhraní.

Vzorový panel v tomto článku se zobrazí na existující stránce řídicího panelu.

## <a name="prerequisites"></a>Předpoklady

K dokončení kroků v tomto průvodci, potřebujete na svém místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Než začnete

Než budete pokračovat, měli byste provést kroky uvedené v článku [Přidání vlastní stránky do webového uživatelského rozhraní akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-page.md) .

## <a name="add-a-panel"></a>Přidat panel

Chcete-li přidat panel do webového uživatelského rozhraní, je nutné přidat zdrojové soubory, které definují panel, a poté upravit řídicí panel tak, aby panel zobrazoval.

### <a name="add-the-new-files-that-define-the-panel"></a>Přidat nové soubory, které definují panel

Chcete-li začít, obsahuje soubor **Src/názor/komponenty, stránky/Řídicí panel/panely/examplePanel** soubory, které definují panel, včetně:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Zkopírujte **zdroj/návod/komponenty/stránky/Řídicí panel/panely/panely/složka examplePanel** do složky **Src/Components/Pages/paneles/panely** .

Přidejte následující export do souboru **Src/názorného/Components/Pages/Dashboard** /paneles/index.js:

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Přidat panel na řídicí panel

Upravte **Src/Components/Pages/Dashboard/dashboard.js** a přidejte panel.

Přidejte panel příkladu do seznamu importů z panelů:

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Přidejte následující definici buňky do mřížky v obsahu stránky:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Test informačního rámečku

Pokud webové uživatelské rozhraní ještě neběží, spusťte následující příkaz v kořenovém adresáři místní kopie úložiště:

```cmd/sh
npm start
```

Předchozí příkaz spustí uživatelské rozhraní lokálně na `http://localhost:3000/dashboard` . Přejděte na stránku **řídicí panel** , abyste zobrazili nový panel.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o dostupných zdrojích, které vám pomůžou s přidáním nebo přizpůsobením řídicích panelů ve webovém uživatelském rozhraní v akcelerátoru řešení vzdáleného monitorování.

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).
