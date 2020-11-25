---
title: Een flyout toevoegen aan de gebruikers interface van de oplossing voor externe bewaking-Azure | Microsoft Docs
description: Dit artikel laat u zien hoe u een nieuw vervolg-flyout kunt toevoegen op een pagina in de Web-UI van de oplossing voor externe bewaking.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: afd8c5b01b9f36606bf6bd43fbed747ca83ba320
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006112"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Een aangepaste flyout toevoegen aan de Web-UI van de externe bewakings oplossing

Dit artikel laat u zien hoe u een nieuwe flyout kunt toevoegen aan een pagina in de Web-UI van de oplossing voor externe bewaking. In het artikel worden de volgende informatie beschreven:

- Het voorbereiden van een lokale ontwikkel omgeving.
- Een nieuwe flyout toevoegen aan een pagina in de gebruikers interface van het web.

In het volgende voor beeld in dit artikel wordt op de pagina weer gegeven met het raster dat u [een aangepast raster aan het hulp programma voor WEBui voor externe controle oplossing toevoegen kunt](iot-accelerators-remote-monitoring-customize-grid.md) zien hoe u dit kunt toevoegen.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt uitvoeren, moet u de volgende software op uw lokale ontwikkel computer installeren:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Voordat u begint

U moet de stappen in de volgende artikelen volt ooien voordat u doorgaat:

- [Een aangepaste pagina toevoegen aan de WEBgebruikersinterface van de oplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-page.md).
- [Een aangepaste service toevoegen aan de webgebruikersinterface voor externe bewaking oplossings versneller](iot-accelerators-remote-monitoring-customize-service.md)
- [Een aangepast raster toevoegen aan de webgebruikersinterface van de oplossings versneller voor externe controle](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Een flyout toevoegen

Als u een flyout wilt toevoegen aan de webgebruikersinterface, moet u de bron bestanden toevoegen die de flyout definiëren en enkele bestaande bestanden wijzigen om de webinterface van het nieuwe onderdeel op de hoogte te stellen.

### <a name="add-the-new-files-that-define-the-flyout"></a>De nieuwe bestanden toevoegen die de flyout definiëren

Om aan de slag te gaan, bevat de map **src/walkthrough/onderdelen/pages/pageWithFlyout/vervolg menu's/exampleFlyout** de bestanden die een flyout definiëren:

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Kopieer de map **src/walkthrough/onderdelen/pages/pageWithFlyout/flyouts** naar de map **src/Components/pages/example** .

### <a name="add-the-flyout-to-the-page"></a>De flyout toevoegen aan de pagina

Wijzig de **elementen src/Components, pages/basicPage.js** om de flyout toe te voegen.

Voeg **btn** toe aan de import bewerkingen uit **componenten/gedeeld** en voeg import bewerkingen toe voor **svgs** en **ExampleFlyoutContainer**:

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

Voeg een **Const** -definitie voor **closedFlyoutState** toe en voeg deze toe aan de status in de constructor:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Voeg de volgende functies toe aan de klasse **BasicPage** :

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Voeg de volgende **Const** -definities toe aan de functie **render** :

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Voeg een knop toe om de vervolg keuzelijst in het context menu te openen:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Voeg tekst en de vervolgmenuanker toe aan de pagina-inhoud:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>De flyout testen

Als de Web-UI niet al lokaal wordt uitgevoerd, voert u de volgende opdracht uit in de hoofdmap van uw lokale kopie van de opslag plaats:

```cmd/sh
npm start
```

Met de vorige opdracht wordt de gebruikers interface lokaal uitgevoerd op `http://localhost:3000/dashboard` . Ga naar de **voorbeeld** pagina en klik op **flyout openen**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de resources die beschikbaar zijn om u te helpen bij het toevoegen of aanpassen van pagina's in de webgebruikersinterface in de oplossings versneller voor externe controle.

Nu u een flyout hebt gedefinieerd op een pagina, is de volgende stap het [toevoegen van een deel venster aan het dash board in de webgebruikersinterface van de oplossing voor externe bewaking](iot-accelerators-remote-monitoring-customize-panel.md).

Zie [architectuur voor externe bewaking](iot-accelerators-remote-monitoring-sample-walkthrough.md)voor meer conceptuele informatie over de oplossings versneller voor externe controle.
