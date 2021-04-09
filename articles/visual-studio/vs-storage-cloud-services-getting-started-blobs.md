---
title: Aan de slag met Blob Storage met behulp van Visual Studio (Cloud Services)
description: Aan de slag met Azure Blob Storage in een Cloud service project in Visual Studio nadat u verbinding hebt gemaakt met een opslag account met behulp van Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: 1144a958-f75a-4466-bb21-320b7ae8f304
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 89e0d6873ebfd8f8396c36185730c57a66af0dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96007024"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Aan de slag met aan Azure Blob Storage en Visual Studio verbonden services (cloudserviceprojecten)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Overzicht
In dit artikel wordt beschreven hoe u aan de slag gaat met Azure Blob Storage nadat u een Azure Storage-account hebt gemaakt of ernaar hebt verwezen met behulp van het dialoog venster **verbonden services toevoegen** in een Visual Studio-Cloud Services-project. We laten u zien hoe u toegang krijgt tot BLOB-containers en hoe u veelvoorkomende taken kunt uitvoeren, zoals het uploaden, vermelden en downloaden van blobs. De voor beelden zijn geschreven in C \# en gebruiken de [Microsoft Azure Storage-client bibliotheek voor .net](/previous-versions/azure/dn261237(v=azure.100)).

Azure Blob Storage is een service voor het opslaan van grote hoeveel heden ongestructureerde gegevens die overal ter wereld toegankelijk zijn via HTTP of HTTPS. Eén Blob kan elke grootte hebben. Blobs kunnen dingen zijn zoals afbeeldingen, audio-en video bestanden, onbewerkte gegevens en document bestanden.

Net zoals bestanden Live in mappen, opslag-blobs in containers. Nadat u een opslag hebt gemaakt, maakt u een of meer containers in de opslag ruimte. In een opslag met de naam ' Plakboek ' kunt u bijvoorbeeld containers maken in de opslag met de naam "installatie kopieën" om afbeeldingen op te slaan en een andere met de naam "audio" om audio bestanden op te slaan. Nadat u de containers hebt gemaakt, kunt u er afzonderlijke BLOB-bestanden naar uploaden.

* Zie [aan de slag met Azure Blob Storage met .net](../storage/blobs/storage-quickstart-blobs-dotnet.md)voor meer informatie over het programmatisch manipuleren van blobs.
* Zie [opslag documentatie](https://azure.microsoft.com/documentation/services/storage/)voor algemene informatie over Azure Storage.
* Zie [Cloud Services-documentatie](https://azure.microsoft.com/documentation/services/cloud-services/)voor algemene informatie over Azure Cloud Services.
* Zie [ASP.net](https://www.asp.net)voor meer informatie over het Program meren van ASP.NET-toepassingen.

## <a name="access-blob-containers-in-code"></a>Toegang krijgen tot BLOB-containers in code
Als u toegang wilt krijgen tot blobs in Cloud service projecten, moet u de volgende items toevoegen, als deze nog niet aanwezig zijn.

1. Voeg de volgende code naam ruimte declaraties toe aan de bovenkant van een C#-bestand waarin u programmatisch toegang wilt krijgen tot Azure Storage.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Een **Cloud Storage account** -object ophalen dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van uw opslag connection string en het opslag account op te halen uit de Azure-service configuratie.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));
    ```
3. Een **CloudBlobClient** -object ophalen om te verwijzen naar een bestaande container in uw opslag account.
   
    ```csharp
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```
4. Een **CloudBlobContainer** -object ophalen om te verwijzen naar een specifieke BLOB-container.
   
    ```csharp
    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

> [!NOTE]
> Gebruik alle code in de vorige procedure vóór de code die in de volgende secties wordt weer gegeven.
> 
> 

## <a name="create-a-container-in-code"></a>Een container maken in code
> [!NOTE]
> Sommige Api's die aanroepen naar Azure Storage in ASP.NET, zijn asynchroon. Zie [asynchrone programmering met async en wacht](/previous-versions/hh191443(v=vs.140)) op voor meer informatie. In de code in het volgende voor beeld wordt ervan uitgegaan dat u asynchrone programmeer methoden gebruikt.
> 
> 

Als u een container in uw opslag account wilt maken, hoeft u alleen maar een aanroep naar **CreateIfNotExistsAsync** toe te voegen, zoals in de volgende code:

```csharp
// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```


Als u de bestanden in de container beschikbaar wilt maken voor iedereen, kunt u de container instellen op openbaar door de volgende code te gebruiken.

```csharp
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```


Iedereen op internet kan blobs in een open bare container zien, maar u kunt ze alleen wijzigen of verwijderen als u de juiste toegangs sleutel hebt.

## <a name="upload-a-blob-into-a-container"></a>Een blob uploaden naar een container
Azure Storage ondersteunt blok-blobs en pagina-blobs. In de meeste gevallen is een blok-blob het aangewezen type om te gebruiken.

Om een bestand naar een blok-blob te uploaden, haalt u een containerverwijzing op en gebruikt u deze om een blok-blobverwijzing op te halen. Zodra u een blobverwijzing hebt, kunt u er elke gewenste gegevensstroom naar uploaden door de methode **UploadFromStream** aan te roepen. Met deze bewerking wordt de blob gemaakt als deze nog niet bestaat, of overschreven als deze wel al bestaat. Het volgende voorbeeld laat zien hoe u een blob uploadt naar een container. Hierbij wordt ervan uitgegaan dat de container al is gemaakt.

```csharp
// Retrieve a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Blobs in een container vermelden
Als u een lijst van de blobs in een container wilt weergeven, moet u eerst een containerverwijzing ophalen. Vervolgens kunt u de methode **ListBlobs** van de container gebruiken voor het ophalen van de blobs en/of de mappen hierin. Om toegang te krijgen tot de uitgebreide set eigenschappen en methoden voor een geretourneerde **IListBlobItem**, moet u deze converteren naar een **CloudBlockBlob**-, **CloudPageBlob**-of **CloudBlobDirectory** -object. Als het type onbekend is, kunt u typecontrole gebruiken om te bepalen waarnaar het moet worden gecast. De volgende code toont hoe de URI van elk item in de **photos**-container wordt opgehaald en uitgevoerd:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, false))
{
    if (item.GetType() == typeof(CloudBlockBlob))
    {
        CloudBlockBlob blob = (CloudBlockBlob)item;

        Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

    }
    else if (item.GetType() == typeof(CloudPageBlob))
    {
        CloudPageBlob pageBlob = (CloudPageBlob)item;

        Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

    }
    else if (item.GetType() == typeof(CloudBlobDirectory))
    {
        CloudBlobDirectory directory = (CloudBlobDirectory)item;

        Console.WriteLine("Directory: {0}", directory.Uri);
    }
}
```

Zoals u in het vorige code voorbeeld ziet, heeft de BLOB-service ook het concept van mappen in containers. Dit betekent dat u uw blobs in een meer mappen structuur kunt indelen. Bekijk bijvoorbeeld de volgende set blok-blobs in een container met de naam **photos**:

```output
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

Wanneer u **ListBlobs** aanroept in de container (zoals in het voor gaande voor beeld), bevat de geretourneerde verzameling **CloudBlobDirectory** -en **CloudBlockBlob** -objecten die de mappen en blobs vertegenwoordigen die zich op het hoogste niveau bevinden. Dit is de resulterende uitvoer:

```output
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```


U kunt desgewenst de parameter **UseFlatBlobListing** van de methode **ListBlobs** instellen op **true**. Dit resulteert in elke blob die wordt geretourneerd als een **CloudBlockBlob**, ongeacht de Directory. Dit is de aanroep van **ListBlobs**:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

en dit zijn de resultaten:

```output
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

Zie [CloudBlobContainer. ListBlobs](/rest/api/storageservices/List-Blobs)voor meer informatie.

## <a name="download-blobs"></a>Blobs downloaden
Om blobs te downloaden, moet u eerst een blobverwijzing ophalen en vervolgens de methode **DownloadToStream** aanroepen. In het volgende voorbeeld wordt de methode **DownloadToStream** gebruikt om de blobinhoud over te dragen naar een stroomobject, dat u vervolgens persistent kunt maken in een lokaal bestand.

```csharp
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

U kunt ook de methode **DownloadToStream** gebruiken om de inhoud van een blob te downloaden als een tekenreeks.

```csharp
// Get a reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>Blobs verwijderen
Als u een BLOB wilt verwijderen, moet u eerst een BLOB-verwijzing ophalen en vervolgens de **Delete** -methode aanroepen.

```csharp
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```


## <a name="list-blobs-in-pages-asynchronously"></a>Blobs asynchroon op pagina's weergeven
Als u een groot aantal blobs in een lijst weergeeft of als u het aantal resultaten dat per lijst wordt geretourneerd, wilt bepalen, kunt u blobs weergeven op pagina's met resultaten. Dit voorbeeld laat zien hoe resultaten op pagina's asynchroon worden geretourneerd, zodat de uitvoering niet wordt geblokkeerd tijdens het wachten op het retourneren van een groot aantal resultaten.

In dit voorbeeld gebruiken we een platte bloblijst, maar u kunt ook een hiërarchische lijst uitvoeren door de parameter **useFlatBlobListing** van de methode **ListBlobsSegmentedAsync** in te stellen op **false**.

Omdat de voorbeeldmethode een asynchrone bewerking aanroept, moet deze worden voorafgegaan door het sleutelwoord **async** en een **Task**-object retourneren. Het sleutelwoord 'await' dat is opgegeven voor de methode **ListBlobsSegmentedAsync**, onderbreekt de uitvoering van de voorbeeldmethode totdat de taak in de lijst is voltooid.

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    // List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    // Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    // When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        // This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        // or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]