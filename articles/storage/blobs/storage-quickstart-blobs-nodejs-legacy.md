---
title: 'Quickstart: Azure Blob Storage-clientbibliotheek v10 voor JavaScript'
description: Containers maken, uploaden en verwijderen in Node.js met behulp van de Azure Storage-clientbibliotheek v10 voor JavaScript
author: twooley
ms.author: twooley
ms.date: 01/19/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: ddcce3e624de7e8bed62783bdc22c5c4211e19e2
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280102"
---
# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-nodejs"></a>Quickstart: Blobs beheren met JavaScript v10 SDK in Node.js

In deze quickstart leert u hoe u blobs beheert met behulp van Node.js. Blobs zijn objecten die grote hoeveelheden tekst of binaire gegevens kunnen bevatten, zoals afbeeldingen, documenten, streaming media en archiefgegevens. U kunt blobs downloaden, uploaden, weergeven en verwijderen en u kunt containers beheren.

> [!NOTE]
> In deze snelstart wordt gebruikgemaakt van een oudere versie van de Azure Blob Storage-clientbibliotheek. Als u aan de slag wilt gaan met de vorige versie, raadpleegt u [Quickstart: Blobs beheren met JavaScript v12 SDK in Node.js](storage-quickstart-blobs-nodejs.md).

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

De [voorbeeldtoepassing](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git) in deze quickstart is een eenvoudige Node.js-consoletoepassing. Kloon om te beginnen de opslagplaats naar uw computer met de volgende opdracht:

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

Wijzig vervolgens de mappen voor de toepassing:

```bash
cd azure-storage-js-v10-quickstart
```

Open nu de map in uw favoriete omgeving voor codebewerking.

## <a name="configure-your-storage-credentials"></a>Uw opslagreferenties configureren

U moet eerst de beveiligingsreferenties voor uw opslagaccount opgeven om de toepassing te kunnen uitvoeren. Het voorbeeld van de opslagplaats bevat een bestand met de naam *.env.example*. U kunt de naam van dit bestand wijzigen door de *.example*-extensie te verwijderen, wat resulteert in een bestand met de naam *.env*. Voeg in het *.env*-bestand uw accountnaam en sleutelwaarden toe bij de sleutels *AZURE_STORAGE_ACCOUNT_NAME* en *AZURE_STORAGE_ACCOUNT_ACCESS_KEY*.

## <a name="install-required-packages"></a>De vereiste pakketten installeren

Voer in de toepassingsmap de opdracht *npm install* uit om de vereiste pakketten voor de toepassing te installeren.

```bash
npm install
```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Nu de afhankelijkheden zijn geïnstalleerd, kunt u het voorbeeld uitvoeren door de volgende opdracht te geven:

```bash
npm start
```

De uitvoer van de app ziet er ongeveer als volgt uit:

```bash
Container "demo" is created
Containers:
 - container-one
 - container-two
 - demo
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Als u voor deze snelstart een nieuw opslagaccount gebruikt, wordt mogelijk alleen de *demo*-container weergegeven onder het label '*Containers*'.

## <a name="understanding-the-code"></a>De code begrijpen

Het voorbeeld begint met het importeren van een aantal klassen en functies van de Azure Blob Storage-naamruimte. Elk van de geïmporteerde items wordt besproken in de context waarin ze worden gebruikt in het voorbeeld.

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

Referenties worden op basis van de juiste context gelezen uit de omgevingsvariabelen.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
```

De module *dotenv* laadt omgevingsvariabelen wanneer de app lokaal wordt uitgevoerd voor foutopsporing. De waarden worden gedefinieerd in een bestand met de naam *.env* en geladen in de huidige uitvoeringscontext. In een productiecontext worden deze waarden geleverd door de serverconfiguratie, daarom wordt deze code alleen uitgevoerd als het script *niet* wordt uitgevoerd in een productieomgeving.

Het volgende blok modules wordt geïmporteerd om communicatie met het bestandssysteem makkelijker te maken.

```javascript
const fs = require('fs');
const path = require('path');
```

Dit is het doel van deze modules: 

- *fs* is de systeemeigen Node.js-module die wordt gebruikt om te werken met het bestandssysteem;

- *path* is vereist om het absolute pad van het bestand te bepalen dat wordt voor het uploaden van een bestand naar blob-opslag.

Waarden van omgevingsvariabelen worden vervolgens gelezen en vastgelegd als constanten.

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```
De volgende set constanten helpt bij het bepalen van de intentie van de bestandsgrootteberekeningen tijdens uploadbewerkingen.

```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```

Aanvragen van de API kunnen zo worden ingesteld dat deze na een bepaalde periode een time-out bereiken. De [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-legacy&preserve-view=true)-klasse is verantwoordelijk voor het beheren van hoe time-outs van aanvragen worden behandeld en de volgende constante wordt gebruikt voor het definiëren van de time-outs die in dit voorbeeld worden gebruikt.

```javascript
const ONE_MINUTE = 60 * 1000;
```

### <a name="calling-code"></a>Code aanroepen

Om de *async/await*-syntaxis van JavaScript te ondersteunen, wordt alle aanroepcode verpakt in een functie met de naam *execute*. Vervolgens wordt *execute* aangeroepen en verwerkt als promise.

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```

Alle onderstaande code wordt uitgevoerd binnen de execute-functie waar de `// commands...`-opmerking is geplaatst.

Eerst worden de relevante variabelen gedeclareerd om namen en voorbeeldinhoud toe te wijzen en om het lokale bestand aan te wijzen voor upload naar de blobopslag.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

Opslagaccountreferenties worden gebruikt om een pijplijn te maken die verantwoordelijk is voor het beheren van hoe aanvragen worden verzonden naar de REST-API. Pijplijnen zijn thread-safe en bevatten logica voor het beleid voor opnieuw proberen, logboekregistratie en deserialisatieregels voor HTTP-antwoorden.

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```

De volgende klassen worden gebruikt in dit codeblok:

- De klasse [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-legacy&preserve-view=true) is verantwoordelijk voor het verpakken van opslagaccountreferenties om deze door te geven aan een aanvraagpijplijn.

- De klasse [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-legacy&preserve-view=true) is verantwoordelijk voor het maken van een nieuwe pijplijn.

- De [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-legacy&preserve-view=true) modelleert een URL die wordt gebruikt in de REST-API. Met instanties van deze klasse kunt u acties uitvoeren zoals het weergeven van een lijst met containers en het leveren van contextinformatie om container-URL's te genereren.

Het exemplaar *ServiceURL* wordt gebruikt met de exemplaren [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-legacy&preserve-view=true) en [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-legacy&preserve-view=true) om containers en blobs in uw opslagaccount te beheren.

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```

De variabelen *containerURL* en *blockBlobURL* worden in het voorbeeld gebruikt om acties uit te voeren in het opslagaccount. 

De container bestaat op dit moment niet in het opslagaccount. Het exemplaar *ContainerURL* vertegenwoordigt een URL waarop u kunt reageren. U kunt met behulp van dit exemplaar de container maken en verwijderen. De locatie van deze container komt overeen met een locatie als deze:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

De *blockBlobURL* wordt gebruikt voor het beheren van afzonderlijke blobs, zodat u blob-inhoud kunt uploaden, downloaden en verwijderen. De hier weergegeven URL lijkt op deze locatie:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```

Net als bij de container, bestaat de blok-blob nog niet. De variabele *blockBlobURL* wordt later gebruikt om de blob te maken door inhoud te uploaden.

### <a name="using-the-aborter-class"></a>De Aborter-klasse gebruiken

Aanvragen van de API kunnen zo worden ingesteld dat deze na een bepaalde periode een time-out bereiken. De *Aborter*-klasse is verantwoordelijk voor het beheren van hoe time-outs van aanvragen worden verwerkt. De volgende code maakt een context waarin een reeks aanvragen 30 minuten krijgen om te worden uitgevoerd.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```

Aborters geven u controle over aanvragen doordat u:

- een hoeveelheid tijd kunt toewijzen voor een batch aanvragen;
- kunt bepalen hoe lang een afzonderlijke aanvraag in de batch er over mag doen om uit te voeren;
- aanvragen kunt annuleren;
- het statische lid *Aborter.none* kunt gebruiken om te voorkomen dat er time-outs voor uw aanvragen plaatsvinden.

### <a name="create-a-container"></a>Een container maken

De methode *create* van *ContainerURL* wordt gebruikt om een container te maken.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```

Omdat de naam van de container wordt gedefinieerd bij het aanroepen van *ContainerURL.fromServiceURL(serviceURL, containerName)* , is het aanroepen van de methode *create* het enige dat nodig is om de container te maken.

### <a name="show-container-names"></a>Containernamen weergeven

Accounts kunnen een groot aantal containers opslaan. De volgende code laat zien hoe u containers op een gesegmenteerde wijze in een lijst kunt weergeven, zodat u een groot aantal containers kunt doorlopen. De functie *showContainerNames* wordt doorgegeven in exemplaren van *ServiceURL* en *Aborter*.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```

De functie *showContainerNames* maakt gebruik van de methode *listContainersSegment* om batches met namen van containers aan te vragen bij het opslagaccount.

```javascript
async function showContainerNames(aborter, serviceURL) {
    let marker = undefined;

    do {
        const listContainersResponse = await serviceURL.listContainersSegment(aborter, marker);
        marker = listContainersResponse.nextMarker;
        for(let container of listContainersResponse.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```

Wanneer het antwoord wordt geretourneerd, wordt *containerItems* herhaald om de naam naar de console te sturen. 

### <a name="upload-text"></a>Tekst uploaden

Als u tekst wilt uploaden naar de blob, gebruikt u de methode *upload*.

```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```

Hier worden de tekst en de lengte doorgegeven aan de methode.

### <a name="upload-a-local-file"></a>Een lokaal bestand uploaden

Als een lokaal bestand wilt uploaden naar de container, hebt u de URL van een container en het pad naar het bestand nodig.

```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```

De functie *uploadLocalFile* roept de functie *uploadFileToBlockBlob* aan, die het bestandspad en een bestemmingsexemplaar van de blok-blob als argumenten gebruikt.

```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```

### <a name="upload-a-stream"></a>Een stream uploaden

Het uploaden van streams wordt ook ondersteund. In dit voorbeeld wordt een lokaal bestand geopend als een stream die doorgegeven wordt aan de uploadmethode.

```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```

De functie *uploadStream* roept *uploadStreamToBlockBlob* aan om de stream te uploaden naar de opslagcontainer.

```javascript
async function uploadStream(aborter, containerURL, filePath) {
    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```

Tijdens het uploaden wijst *uploadStreamToBlockBlob* buffers toe om gegevens uit de stream in cache op te slaan, mocht een nieuwe poging nodig zijn. De waarde *maxBuffers* bepaalt het maximaal te gebruiken aantal buffers, waarbij elke buffer een afzonderlijke uploadaanvraag maakt. In het ideale geval zorgt het gebruik van meer buffers voor hogere snelheden, maar leidt het wel tot hoger geheugengebruik. De uploadsnelheid bereikt een maximum wanneer het aantal buffers hoog genoeg om het knelpunt van de client te verplaatsen naar het netwerk of de schijf.

### <a name="show-blob-names"></a>Blob-namen weergeven

Net zoals accounts veel containers kunnen bevatten, kan elke container een grote hoeveelheid blobs bevatten. Toegang tot elke blob in een container is mogelijk via een exemplaar van de klasse *ContainerURL*.

```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```

De functie *showBlobNames* roept *listBlobFlatSegment* aan om batches blobs uit de container op te vragen.

```javascript
async function showBlobNames(aborter, containerURL) {
    let marker = undefined;

    do {
        const listBlobsResponse = await containerURL.listBlobFlatSegment(Aborter.none, marker);
        marker = listBlobsResponse.nextMarker;
        for (const blob of listBlobsResponse.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```

### <a name="download-a-blob"></a>Een blob downloaden

Wanneer een blob is gemaakt, kunt u de inhoud downloaden met behulp van de methode *download*.

```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = await streamToString(downloadResponse.readableStreamBody);
console.log(`Downloaded blob content: "${downloadedContent}"`);
```

Het antwoord wordt geretourneerd als een stream. In dit voorbeeld wordt de stroom geconverteerd naar een tekenreeks met behulp van de volgende *streamToString*-helperfunctie.

```javascript
// A helper method used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", data => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
}
```

### <a name="delete-a-blob"></a>Een blob verwijderen

De methode *delete* van een *BlockBlobURL*-exemplaar verwijdert een blob uit de container.

```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```

### <a name="delete-a-container"></a>Een container verwijderen

De methode *delete* van een *ContainerURL*-exemplaar verwijdert een container uit het opslagaccount.

```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Resources opschonen

Alle gegevens die zijn geschreven naar het opslagaccount worden automatisch verwijderd aan het einde van de voorbeeldcode. 

## <a name="next-steps"></a>Volgende stappen

In deze snelstart leert u hoe u met behulp van Node.js blobs en containers kunt beheren in Azure Blob Storage. Voor meer informatie over het werken met deze SDK, gaat u naar de GitHub-opslagplaats.

> [!div class="nextstepaction"]
> [Opslagplaats voor Azure Storage SDK v10 voor JavaScript](https://github.com/Azure/azure-storage-js)
> [Azure Storage JavaScript API Reference](/javascript/api/overview/azure/storage-overview)
