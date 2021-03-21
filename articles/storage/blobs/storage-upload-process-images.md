---
title: Afbeeldingsgegevens uploaden in de cloud met Azure Storage | Microsoft Docs
description: U kunt Azure Blob Storage gebruiken met web-apps om app-gegevens op te slaan in een opslagaccount. In deze zelfstudie maakt u een web-app waarin afbeeldingen uit Azure Storage worden opgeslagen en weergegeven.
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b50aadfa16ed95bacb5247187c15489a1b017d39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98676571"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Zelfstudie: Afbeeldingsgegevens uploaden in de cloud met Azure Storage

Deze zelfstudie is deel één van een serie. In deze zelfstudie leert u hoe u een web-app implementeert. De web-app gebruikt Azure Blob Storage-clientbibliotheek om afbeeldingen te uploaden in een opslagaccount. Wanneer u klaar bent, hebt u een web-app die afbeeldingen vanuit Azure-opslag kan opslaan en weergeven.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

![App voor wijzigen van afbeeldingen in .NET](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

![App voor wijzigen van afbeeldingen in JavaScript](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

In deel 1 van de reeks leert u het volgende:

> [!div class="checklist"]

> * Create a storage account
> * Een container maken en machtigingen instellen
> * Een toegangssleutel ophalen
> * Een web-app implementeren in Azure
> * App-instellingen configureren
> * Interactie met de web-app

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien. Maak een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u de CLI lokaal wilt installeren en gebruiken, voert u Azure CLI versie 2.0.4 of hoger uit. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.  

In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt.

```azurecli
az group create --name myResourceGroup --location southeastasia
```

```powershell
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Create a storage account

Met het voorbeeld worden afbeeldingen geüpload naar een blobcontainer in een Azure Storage-account. Een opslagaccount biedt een unieke naamruimte voor het opslaan en openen van uw Azure Storage-gegevensobjecten. Maak een opslagaccount in de resourcegroep die u hebt gemaakt met behulp van de opdracht [az storage account create](/cli/azure/storage/account).

> [!IMPORTANT]
> In deel 2 van de zelfstudie gebruikt u Azure Event Grid met Blob-opslag. Zorg dat u het opslagaccount maakt in een Azure-regio die ondersteuning biedt voor Event Grid. Zie [Azure-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all) voor een lijst met ondersteunde regio's.

Vervang de tijdelijke aanduiding `<blob_storage_account>` in de volgende opdracht door uw eigen, wereldwijd unieke naam voor het Blob-opslagaccount.

```azurecli
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

```powershell
$blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia `
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>Blob-opslagcontainers maken

De app gebruikt twee containers in het Blob Storage-account. Containers zijn vergelijkbaar met mappen. Hier worden blobs opgeslagen. In de container *images* worden afbeeldingen in volledige resolutie opgeslagen. In een later deel van de serie leert u hoe een Azure-functie-app verkleinde afbeeldingsminiaturen naar de container *thumbnails* uploadt.

Haal de opslagaccountsleutel op met behulp van de opdracht [az storage account keys list](/cli/azure/storage/account/keys). Gebruik vervolgens deze sleutel om twee containers te maken met de opdracht [az storage container create](/cli/azure/storage/container).

De openbare toegang tot de container met de *installatiekopieën* is ingesteld op `off`. De openbare toegang tot de container met *miniaturen* is ingesteld op `container`. De instelling `container` van de openbare toegang zorgt ervoor dat gebruikers die de webpagina bezoeken, de miniaturen kunnen bekijken.

```bash
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey --public-access container
```

Noteer de naam en sleutel van het Blob-opslagaccount. De voorbeeld-app maakt gebruik van deze instellingen om verbinding te maken met het opslagaccount om de afbeeldingen te uploaden. 

## <a name="create-an-app-service-plan"></a>Een App Service-plan maken

Een [App Service-plan](../../app-service/overview-hosting-plans.md) geeft de locatie, de grootte en de functies van de webserverfarm aan die als host fungeert voor uw app.

Maak een App Service-plan met de opdracht [az appservice plan create](/cli/azure/appservice/plan).

In het volgende voorbeeld wordt een App Service-plan gemaakt met de naam `myAppServicePlan` en de prijscategorie **Gratis**:

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

```powershell
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Een webtoepassing maken

De web-app biedt een hostingruimte voor de code van de voorbeeld-app. De ruimte wordt geïmplementeerd vanuit de GitHub-voorbeeld opslagplaats. Maak een [web-app](../../app-service/overview.md) in het App Service-plan `myAppServicePlan` met de opdracht [az webapp create](/cli/azure/webapp).  

Vervang `<web_app>` in de volgende opdracht door een unieke naam. Geldige tekens zijn `a-z`, `0-9` en `-`. Als `<web_app>` niet uniek is, krijgt u het volgende foutbericht: *Er bestaat al een website met de naam `<web_app>`.* De standaard-URL van de web-app is `https://<web_app>.azurewebsites.net`.  

```azurecli
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

```powershell
$webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Voorbeeld-app implementeren vanuit de GitHub-opslagplaats

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

App Service ondersteunt diverse manieren om inhoud in een web-app te implementeren. In deze zelfstudie implementeert u de web-app vanaf een [openbare GitHub-voorbeeldopslagplaats](https://github.com/Azure-Samples/storage-blob-upload-from-webapp) (Engelstalig). Configureer GitHub-implementatie naar de webtoepassing met de opdracht [az webapp deployment source config](/cli/azure/webapp/deployment/source).

Het voorbeeldproject bevat een [ASP.NET MVC](https://www.asp.net/mvc)-app. De app accepteert een afbeelding, slaat deze op in een opslagaccount en geeft afbeeldingen weer vanuit een miniaturencontainer. De web-app maakt gebruik van de naamruimten [Azure.Storage](/dotnet/api/azure.storage), [Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs) en [Azure.Storage.Blobs.Models](/dotnet/api/azure.storage.blobs.models) voor interactie met de Azure Storage-service.

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

App Service ondersteunt diverse manieren om inhoud in een web-app te implementeren. In deze zelfstudie implementeert u de web-app vanaf een [openbare GitHub-voorbeeldopslagplaats](https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs) (Engelstalig). Configureer GitHub-implementatie naar de webtoepassing met de opdracht [az webapp deployment source config](/cli/azure/webapp/deployment/source).

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

---

## <a name="configure-web-app-settings"></a>Web-app-instellingen configureren

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

De voorbeeld-web-app gebruikt de [Azure Storage Api's voor .NET](/dotnet/api/overview/azure/storage) om installatiekopieën te uploaden. Referenties van het opslagaccount worden ingesteld in de app-instellingen voor de web-app. Voeg app-instellingen toe aan de geïmplementeerde app met de opdracht [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AzureStorageConfig__AccountName=$blobStorageAccount `
    AzureStorageConfig__ImageContainer=images `
    AzureStorageConfig__ThumbnailContainer=thumbnails `
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

De voorbeeld-web-app gebruikt de [Azure Storage-clientbibliotheek voor JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage) om installatiekopieën te uploaden. Referenties van het opslagaccount worden ingesteld in de app-instellingen voor de web-app. Voeg app-instellingen toe aan de geïmplementeerde app met de opdracht [az webapp config appsettings set](/cli/azure/webapp/config/appsettings).

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Wanneer de web-app is geïmplementeerd en geconfigureerd, kunt u de functionaliteit voor het uploaden van afbeeldingen in de app testen.

## <a name="upload-an-image"></a>Een installatiekopie uploaden

U kunt de web-app testen door naar de URL van de gepubliceerde app te gaan. De standaard-URL van de web-app is `https://<web_app>.azurewebsites.net`.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Selecteer het gebied **Upload photos** als u een bestand wilt opgeven en uploaden, of sleep een bestand naar het gebied. Als de afbeelding is geüpload, verdwijnt deze. De sectie **Gegenereerde miniaturen** blijft leeg totdat we deze verderop in deze zelfstudie testen.

![Foto's uploaden in .NET](media/storage-upload-process-images/figure1.png)

In de voorbeeldcode wordt de taak `UploadFileToStorage` in bestand *Storagehelper.cs* gebruikt om de afbeeldingen door middel van de methode [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) te uploaden naar de container *images* in het opslagaccount. Het volgende codevoorbeeld bevat de taak `UploadFileToStorage`.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName,
                                                    AzureStorageConfig _storageConfig)
{
    // Create a URI to the blob
    Uri blobUri = new Uri("https://" +
                          _storageConfig.AccountName +
                          ".blob.core.windows.net/" +
                          _storageConfig.ImageContainer +
                          "/" + fileName);

    // Create StorageSharedKeyCredentials object by reading
    // the values from the configuration (appsettings.json)
    StorageSharedKeyCredential storageCredentials =
        new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create the blob client.
    BlobClient blobClient = new BlobClient(blobUri, storageCredentials);

    // Upload the file
    await blobClient.UploadAsync(fileStream);

    return await Task.FromResult(true);
}
```

In de vorige taak zijn de volgende klassen en methoden gebruikt:

| Klasse | Methode |
|-------|--------|
| [Uri](/dotnet/api/system.uri) | [URI-constructor](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [StorageSharedKeyCredential(String, String)-constructor](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Selecteer **Bestand kiezen** om een bestand te selecteren en klik u vervolgens op **Installatiekopie uploaden**. De sectie **Gegenereerde miniaturen** blijft leeg totdat we deze verderop in deze zelfstudie testen.

![Foto's uploaden in Node.js](media/storage-upload-process-images/upload-app-nodejs.png)

In de voorbeeldcode is de `post`-route verantwoordelijk voor het uploaden van de afbeelding naar een blobcontainer. De route gebruikt de modules om de upload te verwerken:

- [multer](https://github.com/expressjs/multer) implementeert de uploadstrategie voor de routehandler.
- [into-stream](https://github.com/sindresorhus/into-stream) converteert de buffer in een stream, zoals is vereist door [uploadStream](/javascript/api/%40azure/storage-blob/blockblobclient#uploadstream-readable--number--number--blockblobuploadstreamoptions-).

Wanneer het bestand naar de route wordt verzonden, blijft de inhoud van het bestand in het geheugen staan totdat het bestand is geüpload naar de blobcontainer.

> [!IMPORTANT]
> Het laden van grote bestanden in het geheugen kan een negatief effect hebben op de prestaties van uw web-app. Als u verwacht dat gebruikers grote bestanden posten, kunt u overwegen om bestanden tijdelijk weg te zetten op het bestandssysteem van de webserver en vervolgens uploads naar Blob-opslag te plannen. Zodra de bestanden in Blob-opslag staan, kunt u ze verwijderen van het bestandssysteem van de server.

```javascript
if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}

const {
  BlobServiceClient,
  StorageSharedKeyCredential,
  newPipeline
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const containerName1 = 'thumbnails';
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName2 = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };

const sharedKeyCredential = new StorageSharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = newPipeline(sharedKeyCredential);

const blobServiceClient = new BlobServiceClient(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, '');
  return `${identifier}-${originalName}`;
};

router.get('/', async (req, res, next) => {

  let viewData;

  try {
    const containerClient = blobServiceClient.getContainerClient(containerName1);
    const listBlobsResponse = await containerClient.listBlobFlatSegment();

    for await (const blob of listBlobsResponse.segment.blobItems) {
      console.log(`Blob: ${blob.name}`);
    }

    viewData = {
      title: 'Home',
      viewName: 'index',
      accountName: process.env.AZURE_STORAGE_ACCOUNT_NAME,
      containerName: containerName1
    };

    if (listBlobsResponse.segment.blobItems.length) {
      viewData.thumbnails = listBlobsResponse.segment.blobItems;
    }
  } catch (err) {
    viewData = {
      title: 'Error',
      viewName: 'error',
      message: 'There was an error contacting the blob storage container.',
      error: err
    };
    res.status(500);
  } finally {
    res.render(viewData.viewName, viewData);
  }
});

router.post('/', uploadStrategy, async (req, res) => {
  const blobName = getBlobName(req.file.originalname);
  const stream = getStream(req.file.buffer);
  const containerClient = blobServiceClient.getContainerClient(containerName2);;
  const blockBlobClient = containerClient.getBlockBlobClient(blobName);

  try {
    await blockBlobClient.uploadStream(stream,
      uploadOptions.bufferSize, uploadOptions.maxBuffers,
      { blobHTTPHeaders: { blobContentType: "image/jpeg" } });
    res.render('success', { message: 'File uploaded to Azure Blob storage.' });
  } catch (err) {
    res.render('error', { message: err.message });
  }
});

module.exports = router;
```

---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Controleren of de afbeelding in het opslagaccount wordt weergegeven

Meld u aan bij de [Azure-portal](https://portal.azure.com). In het linkermenu selecteert u **opslagaccounts** en vervolgens de naam van uw opslagaccount. Selecteer **Containers** en vervolgens de container voor **afbeeldingen**.

Controleer of de afbeelding in de container wordt weergegeven.

![Vermelding in Azure-portal van container voor afbeeldingen](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Weergave van miniaturen testen

Als u de weergave van miniaturen wilt testen, uploadt u een afbeelding naar de container **thumbnails** om te controleren of de app de container **thumbnails** kan lezen.

Meld u aan bij de [Azure-portal](https://portal.azure.com). In het linkermenu selecteert u **opslagaccounts** en vervolgens de naam van uw opslagaccount. Selecteer **Containers** en vervolgens de container voor **miniaturen**. Selecteer **Uploaden** om het deelvenster **Blob uploaden** te openen.

Kies een bestand met de bestandenkiezer en selecteer **Uploaden**.

Ga terug naar de app om te controleren of de naar de **thumbnails**-container geüploade afbeelding zichtbaar is.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

![.NET-app voor wijzigen van afbeeldingen, met nieuwe afbeelding weergegeven](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

![Node.js-app voor wijzigen van afbeeldingen, met nieuwe afbeelding weergegeven](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

In deel twee van de serie automatiseert u het maken van miniatuurafbeeldingen zodat u deze afbeelding niet nodig hebt. In de **thumbnails**-container selecteert u de geüploade afbeelding en vervolgens **Verwijderen** om de afbeelding te verwijderen.

U kunt Content Delivery Network (CDN) naar cache-inhoud inschakelen vanuit het Azure-opslagaccount. Zie [Een Azure storage-account integreren met Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In deel één van de serie hebt u geleerd hoe u een web-app configureert om te communiceren met opslag.

Ga verder met deel twee van de serie om te leren hoe u Event Grid gebruikt om een Azure-functie te activeren om het formaat van een afbeelding aan te passen.

> [!div class="nextstepaction"]
> [Event Grid gebruiken om een Azure-functie te activeren bij het aanpassen van geüploade afbeeldingen](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
