---
title: Een raster toevoegen aan de gebruikers interface van de oplossing voor externe bewaking-Azure | Microsoft Docs
description: In dit artikel leest u hoe u een nieuwe GID kunt toevoegen op een pagina in de webgebruikersinterface van de oplossing voor externe bewaking.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 7fc878e0c9e099b201264c1c3981c603668214d3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017760"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Een aangepast raster toevoegen aan de webgebruikersinterface van de oplossings versneller voor externe controle

In dit artikel wordt beschreven hoe u een nieuw raster kunt toevoegen aan een pagina in de webgebruikersinterface van de oplossing voor externe bewaking. In het artikel worden de volgende informatie beschreven:

- Het voorbereiden van een lokale ontwikkel omgeving.
- Een nieuw raster toevoegen aan een pagina in de gebruikers interface van het web.

In het voorbeeld raster in dit artikel worden de gegevens van de service weer gegeven die door de [een aangepaste service worden toegevoegd aan het web-UI](iot-accelerators-remote-monitoring-customize-service.md) -artikel van de oplossing voor externe controle

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt uitvoeren, moet u de volgende software op uw lokale ontwikkel computer installeren:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Voordat u begint

U moet de stappen in de volgende artikelen volt ooien voordat u doorgaat:

- [Een aangepaste pagina toevoegen aan de WEBgebruikersinterface van de oplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-page.md).
- [Een aangepaste service toevoegen aan de webgebruikersinterface voor externe bewaking oplossings versneller](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Een raster toevoegen

Als u een raster wilt toevoegen aan de webgebruikersinterface, moet u de bron bestanden toevoegen die het raster definiëren en enkele bestaande bestanden wijzigen om de webinterface van het nieuwe onderdeel op de hoogte te stellen.

### <a name="add-the-new-files-that-define-the-grid"></a>De nieuwe bestanden toevoegen waarmee het raster wordt gedefinieerd

Om aan de slag te gaan, de map **src/walkthrough/onderdelen/pages/pageWithGrid/exampleGrid** bevat de bestanden die een raster definiëren:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Kopieer de map **src/walkthrough/onderdelen/pages/pageWithGrid/exampleGrid** naar de map **src/Components/pages/example** .

### <a name="add-the-grid-to-the-page"></a>Het raster toevoegen aan de pagina

Wijzig de **bron/onderdelen/pagina's/voor beeld/basicPage.container.js** als volgt om de service definities te importeren:

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

Wijzig de **bron/onderdelen/pagina's/voor beeld/basicPage.js** als volgt om het raster toe te voegen:

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

Wijzig **basicPage.test.js** de volgende stappen om de tests bij te werken als volgt:

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

## <a name="test-the-grid"></a>Het raster testen

Als de Web-UI niet al lokaal wordt uitgevoerd, voert u de volgende opdracht uit in de hoofdmap van uw lokale kopie van de opslag plaats:

```cmd/sh
npm start
```

Met de vorige opdracht wordt de gebruikers interface lokaal uitgevoerd op `http://localhost:3000/dashboard` . Ga naar de **voorbeeld** pagina om het raster weer te geven gegevens van de service.

## <a name="select-rows"></a>Rijen selecteren

Er zijn twee opties voor het inschakelen van een gebruiker om rijen te selecteren in het raster:

### <a name="hard-select-rows"></a>Rijen hard selecteren

Als een gebruiker op hetzelfde moment op meerdere rijen moet handelen, gebruikt u selectie vakjes in rijen:

1. Schakel harde selecties van rijen in door een **checkboxColumn** toe te voegen aan de **columnDefs** die aan het raster worden gegeven. **checkboxColumn** is gedefinieerd in **/src/Components/Shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Voor toegang tot de geselecteerde items krijgt u een verwijzing naar de interne Grid-API:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Geef context knoppen op de pagina op wanneer een rij in het raster hard is geselecteerd:

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

1. Als er op een context knop wordt geklikt, kunt u de geselecteerde items ophalen om uw werk uit te voeren:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Voorlopig: rijen selecteren

Als de gebruiker alleen op één rij moet reageren, configureert u een tijdelijke koppeling voor een of meer kolommen in de **columnDefs**.

1. Voeg **SoftSelectLinkRenderer** in **exampleGridConfig.js** toe als de **cellRendererFramework** voor een **columnDef**.

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

1. Wanneer u op een zachte koppeling klikt, wordt de gebeurtenis **onSoftSelectChange** geactiveerd. Voer een wille keurige actie uit voor die rij, zoals het openen van een info-flyout. Dit voor beeld schrijft simpelweg naar de-console:

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

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de resources die beschikbaar zijn om u te helpen bij het toevoegen of aanpassen van pagina's in de webgebruikersinterface in de oplossings versneller voor externe controle.

U hebt nu een raster gedefinieerd. de volgende stap is het [toevoegen van een aangepaste flyout aan de gebruikers interface van de externe bewaking oplossings versneller](iot-accelerators-remote-monitoring-customize-flyout.md) die wordt weer gegeven op de voorbeeld pagina.

Zie [architectuur voor externe bewaking](iot-accelerators-remote-monitoring-sample-walkthrough.md)voor meer conceptuele informatie over de oplossings versneller voor externe controle.
