---
title: 'Quickstart: Azure Blob-opslagbibliotheek V12 - JavaScript'
description: In deze quickstart leert u hoe u de Azure Blob Storage-clientbibliotheek versie 12 voor JavaScript gebruikt om een container en een blob in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: twooley
ms.author: twooley
ms.date: 09/17/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 6e26a375ebdc97026de09511b0bc0f11fe073e41
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280119"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Quickstart: Blobs beheren met JavaScript v12 SDK in Node.js

In deze quickstart leert u hoe u blobs beheert met behulp van Node.js. Blobs zijn objecten die grote hoeveelheden tekst of binaire gegevens kunnen bevatten, zoals afbeeldingen, documenten, streaming media en archiefgegevens. U kunt blob, downloaden uploaden en weergeven en u kunt containers maken en verwijderen.

Aanvullende bronnen:

* [API-referentiedocumentatie](/javascript/api/@azure/storage-blob)
* [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Pakket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-blob)
* [Voorbeelden](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Instellen

In dit gedeelte wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Blob Storage-clientbibliotheek v12 voor JavaScript.

### <a name="create-the-project"></a>Het project maken

Maak een JavaScript-toepassing met de naam *blob-quickstart-v12*.

1. Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor het project.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Schakel over naar de zojuist gemaakte map *blob-quickstart-v12*.

    ```console
    cd blob-quickstart-v12
    ```

1. Maak een nieuw tekstbestand met de naam *package.json*. Dit bestand definieert het Node.js-project. Sla dit bestand op in de map *blob-quickstart-v12*. Hier is de inhoud van het bestand:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    U kunt desgewenst uw eigen naam invoeren in het veld `author`.

### <a name="install-the-package"></a>Het pakket installeren

Blijf in de map *blob-quickstart-v12* en installeer de Azure Blob Storage-clientbibliotheek voor het JavaScript-pakket met behulp van de opdracht `npm install`. Met deze opdracht wordt het bestand *package.json* gelezen en het pakket Azure Blob Storage-clientbibliotheek v12 voor JavaScript geïnstalleerd, evenals alle bibliotheken waarvan het afhankelijk is.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Open nog een nieuw tekstbestand in uw code-editor
1. Voeg `require`-aanroepen toe om Azure- en Node.js-modules te laden
1. Maak de structuur voor het programma, waaronder eenvoudige afhandeling van uitzonderingen

    Hier volgt de code:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const { v1: uuidv1} = require('uuid');

    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }

    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Sla het nieuwe bestand op als *blob-quickstart-v12.js* in de map *blob-quickstart-v12*.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslagaccount
* Een container in het opslagaccount
* Een blob in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blobs-introduction/blob1.png)

Gebruik de volgende JavaScript-klassen om te communiceren met deze resources:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): Met de klasse `BlobServiceClient` kunt u Azure Storage-resources en blob-containers bewerken.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): Met de klasse `ContainerClient` kunt u Azure Storage-containers en de bijbehorende blobs bewerken.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): Met de klasse `BlobClient` kunt u Azure Storage-blobs bewerken.

## <a name="code-examples"></a>Codevoorbeelden

Deze voorbeeldcodefragmenten laten zien hoe u de volgende acties kunt uitvoeren met de Azure Blob Storage-clientbibliotheek voor JavaScript:

* [De verbindingsreeks ophalen](#get-the-connection-string)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container weergeven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Container verwijderen](#delete-a-container)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de verbindingstekenreeks voor het opslagaccount op van de omgevingsvariabele die is gemaakt in de sectie [De opslagverbindingsreeks configureren](#configure-your-storage-connection-string).

Voeg deze code toe in de functie `main`:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>Een container maken

Verzin een naam voor de nieuwe container. Met de onderstaande code wordt een UUID-waarde aan de containernaam toegevoegd om te verzekeren dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak een exemplaar van de klasse [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) door de methode [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) aan te roepen. Roep vervolgens de methode [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) aan om een verwijzing naar een container te krijgen. Roep tot slot [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) aan om de container in uw opslagaccount te maken.

Voeg deze code toe aan het einde van de functie `main`:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende codefragment:

1. Hiermee wordt een tekenreeks gemaakt om naar een blob te uploaden.
1. Hiermee wordt een verwijzing naar een [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient)-object opgehaald door de methode [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) aan te roepen voor de [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) vanuit de sectie [Een container maken](#create-a-container).
1. Hiermee worden de tekenreeksgegevens geüpload naar de blob door de methode [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) aan te roepen.

Voeg deze code toe aan het einde van de functie `main`:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Geef de blobs in de container weer door de methode [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) aan te roepen. In dit geval is slechts één blob aan de container toegevoegd, zodat met de weergavebewerking alleen die ene blob wordt geretourneerd.

Voeg deze code toe aan het einde van de functie `main`:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Blobs downloaden

Download de eerder gemaakte blob door de methode [download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) aan te roepen. De voorbeeldcode bevat een Help-functie genaamd `streamToString`, die wordt gebruikt om een leesbare Node.js-stream te lezen naar een tekenreeks.

Voeg deze code toe aan het einde van de functie `main`:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Voeg deze Help-functie toe *na* de functie `main`:

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Een container verwijderen

De volgende code ruimt de resources die door de app zijn gemaakt op door de hele container te verwijderen met behulp van de methode [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-). U kunt ook de lokale bestanden verwijderen als u dat wilt.

Voeg deze code toe aan het einde van de functie `main`:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app wordt een tekenreeks gemaakt en naar Blob Storage geüpload. Vervolgens wordt een lijst gemaakt van de blob(s) in de container, wordt de blob gedownload en worden de gedownloade gegevens weergegeven.

Navigeer vanuit een console prompt naar de map met het *blob-quickstart-v12.js* bestand en voer de volgende opdracht uit `node` om de app uit te voeren.

```console
node blob-quickstart-v12.js
```

De uitvoer van de app lijkt op die in het volgende voorbeeld:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Neem de code in uw foutopsporingsprogramma door en controleer uw [Azure-portal](https://portal.azure.com) gedurende de hele procedure. Kijk of de container wordt gemaakt. U kunt de blob in de container openen en de inhoud bekijken.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u blobs kunt uploaden, kunt downloaden en er een lijst van kunt maken met behulp van JavaScript.

Voor zelfstudies, voorbeelden, quickstarts en andere documentatie gaat u naar:

> [!div class="nextstepaction"]
> [Azure voor Java script-ontwikkelaarscentrum](/azure/developer/javascript/)

* Voor meer informatie over het implementeren van een webapp die Azure Blob-opslag gebruikt, zie [Zelfstudie: Afbeeldingsgegevens uploaden in de cloud met Azure Storage](./storage-upload-process-images.md?preserve-view=true&tabs=javascript)
* Als u voorbeeld-apps voor blob-opslag wilt zien, gaat u verder met [Voorbeelden van Azure Blob-opslag-clientbibliotheek v12 JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
* Zie de [Azure Blob-opslag-clientbibliotheek voor JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob)voor meer informatie.
