---
title: Přidání mřížky do uživatelského uživatelského režimu řešení vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak přidat nový gid na stránce ve webovém uživatelském uživatelském počítači akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.openlocfilehash: a24cb7f39ccb8ea07d4dde2869dc7c924b91983a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447093"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Přidání vlastní mřížky do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování

Tento článek ukazuje, jak přidat novou mřížku na stránku ve webovém uživatelském uživatelském počítači akcelerátoru řešení vzdáleného monitorování. Článek popisuje:

- Jak připravit prostředí místního rozvoje.
- Jak přidat novou mřížku na stránku ve webovém uživatelském uživatelském tlačítko.

Ukázková mřížka v tomto článku zobrazuje data ze služby, kterou doplněk Přidat vlastní službu do článku s postupem webového uživatelského panelu webového uživatelského [panelu akcelerátoru řešení vzdáleného monitorování,](iot-accelerators-remote-monitoring-customize-service.md) ukazuje, jak přidat.

## <a name="prerequisites"></a>Požadavky

Chcete-li provést kroky v tomto návodu, potřebujete v místním vývojovém počítači nainstalovaný následující software:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Než začnete

Než budete pokračovat, měli byste provést kroky v následujících článcích:

- [Přidejte vlastní stránku do webového uživatelského uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-page.md).
- [Přidání vlastní služby do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Přidat mřížku

Chcete-li přidat mřížku do webového uživatelského uživatelského nastavení, je třeba přidat zdrojové soubory, které definují mřížku, a upravit některé existující soubory tak, aby webové uživatelské uživatelské uživatelské nastavení bylo informováno o nové součásti.

### <a name="add-the-new-files-that-define-the-grid"></a>Přidání nových souborů, které definují mřížku

Chcete-li začít, složka **src/walkthrough/components/pages/pageWithGrid/exampleGrid** obsahuje soubory, které definují mřížku:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Zkopírujte složku **src/walkthrough/components/pages/pageWithGrid/exampleGrid** do složky **src/components/pages/example.**

### <a name="add-the-grid-to-the-page"></a>Přidání mřížky na stránku

Chcete-li importovat definice služby, upravte soubor **src/components/pages/example/basicPage.container.js** následujícím způsobem:

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

Chcete-li přidat mřížku, upravte **soubor src/components/pages/example/basicPage.js** následujícím způsobem:

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

Chcete-li aktualizovat testy, upravte **soubor src/components/pages/example/basicPage.test.js** následujícím způsobem:

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>Testování mřížky

Pokud webové uživatelské uživatelské prostředí ještě není spuštěno místně, spusťte v kořenovém adresáři místní kopie úložiště následující příkaz:

```cmd/sh
npm start
```

Předchozí příkaz spustí ui místně [http://localhost:3000/dashboard](http://localhost:3000/dashboard)na . Přejděte na stránku **Příklad** a zobrazte data mřížky ze služby.

## <a name="select-rows"></a>Vybrat řádky

Existují dvě možnosti, jak uživateli umožnit výběr řádků v mřížce:

### <a name="hard-select-rows"></a>Řádky s tvrdým výběrem

Pokud uživatel potřebuje jednat na více řádků najednou, použijte zaškrtávací políčka na řádcích:

1. Povolte pevný výběr řádků přidáním **zaškrtávacího políčkaSloupec** do sloupceDefs dosítěné do **mřížky.** **zaškrtávací políčkoSloupec** je definováno v **souboru /src/components/shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Pro přístup k vybraným položkám získáte odkaz na interní mřížkové rozhraní API:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Poskytněte stránce tlačítka kontextu, když je pevně vybraný řádek v mřížce:

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. Po klepnutí na tlačítko kontextu získáte pevně vybrané položky, na kterých můžete pracovat:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Řádky s měkkým výběrem

Pokud uživatel potřebuje pouze na jeden řádek, nakonfigurujte odkaz pro měkký výběr pro jeden nebo více sloupců ve **sloupciDefs**.

1. V **souboru exampleGridConfig.js**přidejte **softselectlinkrenderer** jako **cellRendererFramework** pro **columnDef**.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. Po klepnutí na odkaz pro měkký výběr spustí událost **onSoftSelectChange.** Proveďte jakoukoli akci, která je pro tento řádek žádoucí, například otevření informačního rámečku podrobností. Tento příklad jednoduše zapíše do konzoly:

    ```js
    onSoftSelectChange = (rowId, rowData) => {
      //Note: only the Id is reliable, rowData may be out of date
      const { onSoftSelectChange } = this.props;
      if (rowId) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', rowId);
        this.setState({ softSelectedId: rowId });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(rowId, rowData);
      }
    }
    ```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o dostupných prostředcích, které vám pomohou přidat nebo přizpůsobit stránky ve webovém uživatelském rozhraní v akcelerátoru řešení vzdáleného monitorování.

Nyní jste definovali mřížku, dalším krokem je [přidání vlastního informačního rámečku do webového uživatelského uživatelského uživatelského nastavení akcelerátoru řešení vzdáleného monitorování,](iot-accelerators-remote-monitoring-customize-flyout.md) které se zobrazí na ukázkové stránce.

Další rámcové informace o akcelerátoru řešení vzdáleného monitorování naleznete v [tématu Architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md).
