---
title: 'Azure-snelstart: een blob maken in objectopslag met Go | Microsoft Docs'
description: In deze snelstart maakt u een opslagaccount en een container in object(blob)-opslag. Vervolgens gebruikt u de opslagclientbibliotheek voor Go om een blob in Azure Storage te uploaden, een blob te downloaden en de blobs in een container te vermelden.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: bf0e868e9ee746da1dfe1b03403d21f7edb3bd5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544646"
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>Quickstart: blobs downloaden, uploaden en vermelden met behulp van Go

In deze quickstart leert u hoe u de programmeertaal Go kunt gebruiken om blok-bobs te uploaden, te downloaden en weer te geven in een container in Azure Blob-opslag. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Zorg dat u de volgende aanvullende vereisten hebt geïnstalleerd:
 
* [Go 1.8 of hoger](https://golang.org/dl/)
* [Azure Storage Blob-SDK voor Go](https://github.com/azure/azure-storage-blob-go/) met behulp van de volgende opdracht:

    ```
    go get -u github.com/Azure/azure-storage-blob-go/azblob
    ``` 

    > [!NOTE]
    > Zorg ervoor dat u `Azure` in de URL met hoofdletters schrijft om importproblemen met betrekking tot hoofdletters/kleine letters te voorkomen bij het werken met SDK. Schrijf `Azure` in de importinstructies ook met hoofdletters.
    
## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden
De [voorbeeldtoepassing](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git) die in deze quickstart wordt gebruikt, is een Go-basistoepassing.  

Gebruik [git](https://git-scm.com/) om een kopie van de toepassing naar uw ontwikkelomgeving te downloaden. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

Met deze opdracht wordt de opslagplaats naar uw lokale git-map gekloond. Om het Go-voorbeeld voor Blob-opslag te openen, zoekt u het bestand storage-quickstart.go op.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren
Deze oplossing vereist dat uw opslagaccountnaam en -sleutel veilig zijn opgeslagen in omgevingsvariabelen die lokaal op de computer staan waarop u het voorbeeld uitvoert. Volg afhankelijk van uw besturingssysteem een van de onderstaande voorbeelden voor het maken van de omgevingsvariabelen.

# <a name="linux"></a>[Linux](#tab/linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windows"></a>[Windows](#tab/windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren
Met dit voorbeeld wordt een testbestand gemaakt in de huidige map, het testbestand geüpload naar Blob-opslag, worden de blobs in the container vermeld en wordt het bestand met een nieuwe naam gedownload naar een buffer. 

Geef de volgende opdracht om het voorbeeld uit te voeren: 

```go run storage-quickstart.go```

De volgende uitvoer is een voorbeeld van de uitvoer die wordt geretourneerd wanneer de toepassing wordt uitgevoerd:
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
Wanneer u op een willekeurige toets drukt om door te gaan, worden de opslagcontainer en de bestanden door het voorbeeldprogramma verwijderd. 

> [!TIP]
> U kunt ook een hulpprogramma zoals [Azure Storage Explorer](https://storageexplorer.com) gebruiken om de bestanden in de Blob-opslag te bekijken. Azure Storage Explorer is een gratis hulpprogramma voor meerdere platforms waarmee u toegang hebt tot de gegevens van uw opslagaccount. 
>

## <a name="understand-the-sample-code"></a>De voorbeeldcode begrijpen

We gaan nu de voorbeeldcode bespreken, zodat u begrijpt hoe deze werkt.

### <a name="create-containerurl-and-bloburl-objects"></a>ContainerURL- en BlobURL-objecten maken
Als eerste moeten verwijzingen worden gemaakt naar de ContainerURL- en BlobURL-objecten die worden gebruikt voor het verkrijgen van toegang tot de Blob-opslag en voor het beheren ervan. Deze objecten bieden API's op laag niveau zoals Create, Upload en Download om REST API's uit te geven.

* Gebruik de struct [**SharedKeyCredential**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#SharedKeyCredential) voor het opslaan van uw referenties. 

* Maak een [**pijplijn**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#NewPipeline) met behulp van de referenties en opties. De pijplijn bevat items zoals het beleid voor nieuwe pogingen, logboekregistratie en deserialisatie van payloads voor HTTP-antwoorden.  

* Maak een nieuw [**ContainerURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#ContainerURL)- en een nieuw [**BlobURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#BlobURL)-object om bewerkingen op de container (Create) en blobs (Upload en Download) uit te voeren.


Zodra u de ContainerURL hebt, kunt u het **BlobURL**-object maken dat verwijst naar een blob en bewerkingen uitvoeren zoals uploaden, downloaden en kopiëren.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Naming and referencing containers, blobs, and metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Containers, blobs en metagegevens een naam geven en hiernaar verwijzen) voor meer informatie over namen voor blobs en containers.

In deze sectie maakt u een nieuwe container. De container heeft de naam **quickstartblobs-[random string]**. 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential, err := azblob.NewSharedKeyCredential(accountName, accountKey)
if err != nil {
    log.Fatal("Invalid credentials with error: " + err.Error())
}
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err = containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>Blobs uploaden naar de container

Blob-opslag ondersteunt blok-blobs, toevoeg-blobs en pagina-blobs. Omdat blok-blobs het meest worden gebruikt, worden ze in deze Quick Start gebruikt.  

Als u een bestand wilt uploaden naar een blob, opent u het bestand met **os. Open**. U kunt het bestand vervolgens naar het opgegeven pad uploaden met een van de REST API's: Upload (PutBlob), StageBlock/CommitBlockList (PutBlock/PutBlockList). 

De SDK biedt [API's op hoog niveau](https://github.com/Azure/azure-storage-blob-go/blob/master/azblob/highlevel.go) die zijn gebaseerd op de REST-API's op laag niveau. De functie ***UploadFileToBlockBlob*** maakt bijvoorbeeld gebruik van StageBlock-bewerkingen (PutBlock) om gelijktijdig een bestand in segmenten te uploaden om de doorvoer te optimaliseren. Als het bestand minder dan 256 MB is, wordt in plaats daarvan Upload (PutBlob) gebruikt om de overdracht in één transactie te voltooien.

In het volgende voorbeeld wordt het bestand geüpload naar de container met de naam **quickstartblobs-[randomstring]**.

```go
// Create a file to test the upload and download.
fmt.Printf("Creating a dummy file to test the upload and download\n")
data := []byte("hello world this is a blob\n")
fileName := randomString()
err = ioutil.WriteFile(fileName, data, 0700)
handleErrors(err)

// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level Upload (PutBlob) API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that Upload can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/rest/api/storageservices/put-blob
// To upload more than 256MB, use StageBlock (PutBlock) and CommitBlockList (PutBlockList) functions. 
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.Upload(ctx, file, azblob.BlobHTTPHeaders{ContentType: "text/plain"}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls StageBlock/CommitBlockList for files larger 256 MBs, and calls Upload for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Haal een lijst met bestanden in de container op met behulp van de methode **ListBlobs** in een **ContainerURL**. ListBlobs retourneert een enkel segment met blobs (maximaal 5000) vanaf de opgegeven **markering**. Gebruik een lege markering om de opsomming vanaf het begin starten. Blob-namen worden in lexografische volgorde geretourneerd. Nadat u een segment hebt opgehaald, verwerkt u het en roept u vervolgens ListBlobs opnieuw aan waarmee de eerder geretourneerde markering wordt doorgegeven.  

```go
// List the container that we have created above
fmt.Println("Listing the blobs in the container:")
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobsFlatSegment(ctx, marker, azblob.ListBlobsSegmentOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Segment.BlobItems {
        fmt.Print(" Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>De blob downloaden

Download blobs met de **Download**-functie op laag niveau in een BlobURL. Dit resulteert in een **DownloadResponse**-struct. Voer de functie **Body** uit in de struct om een **RetryReader**-stroom op te halen voor het lezen van gegevens. Als een verbinding mislukt tijdens het lezen, worden aanvullende aanvragen gedaan om opnieuw een verbinding tot stand te brengen en door te gaan met lezen. Wanneer een RetryReaderOption met MaxRetryRequests die is ingesteld op 0 (standaard) wordt opgegeven, wordt de oorspronkelijke hoofdtekst van de reactie geretourneerd en worden geen nieuwe pogingen uitgevoerd. U kunt ook de API's op hoog niveau **DownloadBlobToBuffer** of **DownloadBlobToFile** gebruiken om uw code te vereenvoudigen.

Met de volgende code wordt de blob gedownload met behulp van de functie **Download**. De inhoud van de blob wordt naar een buffer geschreven en weergegeven in de console.

```go
// Here's how to download the blob
downloadResponse, err := blobURL.Download(ctx, 0, azblob.CountToEnd, azblob.BlobAccessConditions{}, false)

// NOTE: automatically retries are performed if the connection fails
bodyStream := downloadResponse.Body(azblob.RetryReaderOptions{MaxRetryRequests: 20})

// read the body into a buffer
downloadedData := bytes.Buffer{}
_, err = downloadedData.ReadFrom(bodyStream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>Resources opschonen
Als u de blobs die in deze quickstart zijn geüpload niet langer nodig hebt, kunt u de volledige container verwijderen met behulp van de methode **Delete**. 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="resources-for-developing-go-applications-with-blobs"></a>Resources voor het ontwikkelen van Go-toepassingen met blobs

Zie de volgende aanvullende bronnen voor Go-ontwikkeling met Blob-opslag:

- Bekijk en installeer de [broncode voor de Go-clientbibliotheek](https://github.com/Azure/azure-storage-blob-go) voor Azure Storage op GitHub.
- Verken [Blob-opslagvoorbeelden](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#pkg-examples) die zijn geschreven met de Go-clientbibliotheek.

## <a name="next-steps"></a>Volgende stappen
 
In deze quickstart hebt u geleerd hoe u bestanden overbrengt tussen een lokale schijf en Azure Blob-opslag met behulp van Go. Raadpleeg de [broncode](https://github.com/Azure/azure-storage-blob-go/) en [API-verwijzing](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob) voor meer informatie over de SDK van Azure Storage Blob.