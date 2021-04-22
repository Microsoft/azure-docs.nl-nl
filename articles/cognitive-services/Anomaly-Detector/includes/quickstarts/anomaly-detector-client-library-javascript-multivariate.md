---
title: Anomaly Detector quickstart over meerdere JavaScript-clientbibliotheek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 656270c80e8da0ece83bb04190fa7e5710a0203e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880280"
---
Ga aan de slag met Anomaly Detector multivariate clientbibliotheek voor JavaScript. Volg deze stappen om het pakket te installeren en de algoritmen van de service te gaan gebruiken. Met de nieuwe API's voor anomaliedetectie met meerdere afwijkingen kunnen ontwikkelaars eenvoudig geavanceerde AI integreren voor het detecteren van afwijkingen uit groepen met metrische gegevens, zonder dat machine learning kennis of gelabelde gegevens nodig zijn. Afhankelijkheden en onderlinge correlaties tussen verschillende signalen worden automatisch geteld als belangrijke factoren. Dit helpt u om uw complexe systemen proactief te beschermen tegen storingen.

Gebruik de Anomaly Detector multivariate clientbibliotheek voor JavaScript voor het volgende:

* Anomalieën op systeemniveau detecteren uit een groep tijdreeksen.
* Wanneer een afzonderlijke tijdreeks u niet veel vertelt en u alle signalen moet bekijken om een probleem te detecteren.
* Predicatief onderhoud van dure fysieke activa met tientallen tot honderden verschillende typen sensoren die verschillende aspecten van de systeemtoestand meten.

[Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/anomalydetector/ai-anomaly-detector)  |  [Pakket (npm)](https://www.npmjs.com/package/@azure/ai-anomaly-detector)  |  [Voorbeeldcode](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)

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

Maak een bestand met de `index.js` naam en importeer de volgende bibliotheken: '
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource. Maak nog een variabele voor het voorbeeldgegevensbestand.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Als u de Anomaly Detector api's wilt gebruiken, moeten we ons eigen model trainen voordat u detectie gebruikt. Gegevens die worden gebruikt voor training zijn een batch tijdreeksen. Elke tijdreeks moet een CSV-indeling hebben met twee kolommen, een tijdstempel en een waarde. Alle tijdreeksen moeten worden ingepakt in één ZIP-bestand en worden geüpload naar [Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md) Standaard wordt de bestandsnaam gebruikt om de variabele voor de tijdreeks weer te geven. U kunt ook een extra meta.jsin het zip-bestand als u wilt dat de naam van de variabele verschilt van de naam van het ZIP-bestand. Zodra we de [BLOB SAS-URL (Shared Access Signatures)](../../../../storage/common/storage-sas-overview.md)hebben gegenereerd, kunnen we de URL naar het ZIP-bestand gebruiken voor training.

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer de NPM-pakketten `ms-rest-azure` en `azure-ai-anomalydetector`. De CSV-parseerbibliotheek wordt ook gebruikt in deze snelstart:

```console
npm install @azure/ai-anomaly-detector csv-parse
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

Instantieer een `AnomalyDetectorClient` -object met uw eindpunt en referenties.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey));
```

## <a name="train-a-model"></a>Een model trainen

### <a name="construct-a-model-result"></a>Een modelresultaat maken

Eerst moeten we een modelaanvraag maken. Zorg ervoor dat de begin- en eindtijd zijn afgestemd op uw gegevensbron.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Een nieuw model trainen

U moet uw modelaanvraag doorgeven aan de Anomaly Detector `trainMultivariateModel` clientmethode.

```javascript
console.log("Training a new model...")
const train_response = await client.trainMultivariateModel(Modelrequest)
const model_id = train_response.location?.split("/").pop() ?? ""
console.log("New model ID: " + model_id)
```

Als u wilt controleren of de training van uw model is voltooid, kunt u de status van het model volgen:

```javascript
let model_response = await client.getMultivariateModel(model_id)
let model_status = model_response.modelInfo?.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    model_response = await client.getMultivariateModel(model_id)
    model_status = model_response.modelInfo?.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>Afwijkingen detecteren

Gebruik de `detectAnomaly` functies en om te bepalen of er afwijkingen zijn in uw `getDectectionResult` gegevensbron.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location?.split("/").pop() ?? ""
let result = await client.getDetectionResult(result_id)
let result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    result = await client.getDetectionResult(result_id)
    result_status = result.summary.status
}
```

## <a name="export-model"></a>Model exporteren

Gebruik de functie om uw getrainde model te `exportModel` exporteren.

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody?.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Model verwijderen

Als u een bestaand model wilt verwijderen dat beschikbaar is voor de huidige resource, gebruikt u de `deleteMultivariateModel` functie .

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Voordat u de toepassing gaat uitvoeren, kan het handig zijn om uw code te controleren op de [volledige voorbeeldcode](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/anomalydetector/ai-anomaly-detector/samples/v3/javascript/sample_multivariate_detection.js)

Voer de toepassing uit met de opdracht `node` in uw quickstart-bestand.

```console
node index.js
```

## <a name="next-steps"></a>Volgende stappen

* [Anomaly Detector best practices voor meerdere varianten](../../concepts/best-practices-multivariate.md)
