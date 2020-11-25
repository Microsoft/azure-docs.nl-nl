---
title: Een paneel toevoegen aan de gebruikers interface van de oplossing voor externe bewaking-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een nieuw deel venster aan het dash board toevoegt in de webgebruikersinterface van de oplossing voor externe bewaking.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 1dcca8409022ba4cf1f988b7c777e3a1fa511060
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006061"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Een aangepast paneel toevoegen aan het dash board in de Web-UI van de oplossing voor externe bewaking

In dit artikel wordt beschreven hoe u een nieuw deel venster kunt toevoegen aan een dashboard pagina in de webgebruikersinterface van de oplossing voor externe bewaking. In het artikel worden de volgende informatie beschreven:

- Het voorbereiden van een lokale ontwikkel omgeving.
- Een nieuw deel venster toevoegen aan een dashboard pagina in de gebruikers interface van het web.

Het deel venster voor beeld in dit artikel wordt weer gegeven op de bestaande dash board-pagina.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt uitvoeren, moet u de volgende software op uw lokale ontwikkel computer installeren:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Voordat u begint

U moet de stappen in het artikel [een aangepaste pagina toevoegen aan de Web-UI van de oplossing voor externe controle](iot-accelerators-remote-monitoring-customize-page.md) uitvoeren voordat u doorgaat.

## <a name="add-a-panel"></a>Een deelvenster toevoegen

Als u een paneel wilt toevoegen aan de gebruikers interface, moet u de bron bestanden toevoegen die het paneel definiëren en vervolgens het dash board wijzigen om het deel venster weer te geven.

### <a name="add-the-new-files-that-define-the-panel"></a>De nieuwe bestanden toevoegen die het paneel definiëren

Om aan de slag te gaan, bevat de map **src/walkthrough/onderdelen/pages/dash board/panels/examplePanel** de bestanden waarmee een paneel wordt gedefinieerd, waaronder:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Kopieer de map **src/walkthrough/onderdelen/pages/dash board/panels/examplePanel** naar de map **src/Components/pages/dash board/panels** .

Voeg de volgende export toe aan de **bron/walkthrough/onderdelen/pagina's/dash board/deel Vensters/index.js** bestand:

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Het deel venster toevoegen aan het dash board

Wijzig het paneel **src/Components/pages/dash board/dashboard.js** om het deel venster toe te voegen.

Voeg het deel venster voor beeld toe aan de lijst met invoer uit de deel Vensters:

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

Voeg de volgende definitie van de cel toe aan het raster in de pagina-inhoud:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>De flyout testen

Als de Web-UI niet al lokaal wordt uitgevoerd, voert u de volgende opdracht uit in de hoofdmap van uw lokale kopie van de opslag plaats:

```cmd/sh
npm start
```

Met de vorige opdracht wordt de gebruikers interface lokaal uitgevoerd op `http://localhost:3000/dashboard` . Navigeer naar de pagina **dash board** om het nieuwe deel venster weer te geven.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de resources die beschikbaar zijn om u te helpen bij het toevoegen of aanpassen van Dash boards in de Web-UI in de oplossings versneller voor externe controle.

Zie [architectuur voor externe bewaking](iot-accelerators-remote-monitoring-sample-walkthrough.md)voor meer conceptuele informatie over de oplossings versneller voor externe controle.
