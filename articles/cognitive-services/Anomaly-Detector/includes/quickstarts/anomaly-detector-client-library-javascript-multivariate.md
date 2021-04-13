---
title: Quick start voor anomalie detectie multidimensionale java script-client bibliotheek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 4e0f2d1bae07f0814b4f096d8be315bd92cd42fe
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316026"
---
Ga aan de slag met de multidimensionale-client bibliotheek voor anomalie detectie voor Java script. Volg deze stappen om het pakket te installeren en te beginnen met het gebruik van de algoritmen die door de service worden gebruikt. Met de nieuwe multidimensionale anomalie detectie-Api's kunnen ontwikkel aars eenvoudig geavanceerde AI integreren voor het detecteren van afwijkingen van groepen met metrische gegevens, zonder dat er machine learning kennis of labels zijn vereist. Afhankelijkheden en tussen correlaties tussen verschillende signalen worden automatisch geteld als sleutel factoren. Zo kunt u uw complexe systemen proactief beveiligen tegen storingen.

Gebruik de multidimensionale-client bibliotheek voor anomalie detectie voor Java script voor het volgende:

* Afwijkingen op systeem niveau van een groep tijd reeksen detecteren.
* Wanneer een wille keurige tijd reeks niet meer vertelt en u alle signalen moet bekijken om een probleem te detecteren.
* Het predicaat onderhoud van dure fysieke activa met tien tot honderden verschillende typen Sens oren die verschillende aspecten van de systeem status meten.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De huidige versie van [Node.js](https://nodejs.org/)
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="maakt u een Anomaly Detector-resource "  target="_blank">Een Anomaly Detector-resource maken</a> in de Azure-portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt om de toepassing te verbinden met de Anomaly Detector-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp && cd myapp
```

Voer de opdracht `npm init` uit om een knooppunttoepassing te maken met een `package.json`-bestand. 

```console
npm init
```

Maak een bestand `index.js` met de naam en importeer de volgende bibliotheken: '
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource. Maak een andere variabele voor het voorbeeld gegevensbestand.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Voor het gebruik van de anomalie detectie multidimensionale-Api's moeten we ons eigen model trainen voordat ze detectie kunnen gebruiken. Gegevens die worden gebruikt voor de training, zijn een batch van een tijd reeks, elke time series moet in CSV-indeling zijn met twee kolommen, tijds tempel en waarde. Alle tijd reeksen moeten worden ingepakt in één ZIP-bestand en naar [Azure Blob-opslag](../../../../storage/blobs/storage-blobs-introduction.md)worden geüpload. Standaard wordt de bestands naam gebruikt om de variabele voor de tijd reeks weer te geven. U kunt ook een extra meta.jsvoor het bestand opnemen in het zip-bestand als u wilt dat de naam van de variabele afwijkt van de naam van het zip-bestand. Zodra de URL voor de [BLOB SAS (Shared Access signatures)](../../../../storage/common/storage-sas-overview.md)is gegenereerd, kunnen we de URL voor de training gebruiken in het zip-bestand.

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer de NPM-pakketten `ms-rest-azure` en `azure-ai-anomalydetector`. De CSV-parseerbibliotheek wordt ook gebruikt in deze snelstart:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Anomaly Detector-clientbibliotheek voor Node.js:

* [De client verifiëren](#authenticate-the-client)
* [Een model trainen](#train-a-model)
* [Afwijkingen detecteren](#detect-anomalies)
* [Model exporteren](#export-model)
* [Model verwijderen](#delete-model)

## <a name="authenticate-the-client"></a>De client verifiëren

Een object instantiëren `AnomalyDetectorClient` met uw eind punt en referenties.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey)).client;
```

## <a name="train-a-model"></a>Een model trainen

### <a name="construct-a-model-result"></a>Een model resultaat maken

Eerst moeten we een model aanvraag bouwen. Zorg ervoor dat de begin-en eind tijd worden uitgelijnd met uw gegevens bron.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Een nieuw model trainen

U moet uw model aanvraag door geven aan de methode van de afwijkings detector-client `trainMultivariateModel` .

```javascript
console.log("Training a new model...")
var train_response = await client.trainMultivariateModel(Modelrequest)
var model_id = train_response.location.split("/").pop()
console.log("New model ID: " + model_id)
```

Als u wilt controleren of de training van uw model is voltooid, kunt u de status van het model volgen:

```javascript
var model_response = await client.getMultivariateModel(model_id)
var model_status = model_response.modelInfo.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    var model_response = await client.getMultivariateModel(model_id)
    var model_status = model_response.modelInfo.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>Afwijkingen detecteren

Gebruik de `detectAnomaly` `getDectectionResult` functies en om te bepalen of er afwijkingen in uw gegevens bron zijn.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location.split("/").pop()
var result = await client.getDetectionResult(result_id)
var result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    var result = await client.getDetectionResult(result_id)
    var result_status = result.summary.status
}
```

## <a name="export-model"></a>Model exporteren

Gebruik de functie om uw getrainde model te exporteren `exportModel` .

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Model verwijderen

Gebruik de functie om een bestaand model dat beschikbaar is voor de huidige resource, te verwijderen `deleteMultivariateModel` .

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node` in uw quickstart-bestand.

```console
node index.js
```

## <a name="next-steps"></a>Volgende stappen

* [Best practices voor anomalie detectie multidimensionale](../../concepts/best-practices-multivariate.md)
