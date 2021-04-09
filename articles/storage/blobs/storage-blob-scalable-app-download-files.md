---
title: Grote hoeveelheden willekeurige gegevens downloaden uit Azure Storage | Microsoft Docs
description: Informatie over het gebruik van de Azure SDK om grote hoeveelheden willekeurige gegevens uit een Azure Storage-account te downloaden
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c029abd87e1b819cc4d96e906be8824c019f433
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584971"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Grote hoeveelheden willekeurige gegevens downloaden uit Azure Storage

Deze zelfstudie is deel drie van een serie. Deze zelfstudie laat zien hoe u grote hoeveelheden gegevens uit Azure Storage kunt downloaden.

In deel drie van de serie leert u het volgende:

> [!div class="checklist"]
> * De toepassing bijwerken
> * De toepassing uitvoeren
> * Het aantal verbindingen valideren

## <a name="prerequisites"></a>Vereisten

Voor het volgen van deze zelfstudie moet u de vorige zelfstudie over opslag: [Upload large amounts of random data in parallel to Azure storage][previous-tutorial] (Grote hoeveelheden willekeurige gegevens parallel uploaden naar Azure Storage).

## <a name="remote-into-your-virtual-machine"></a>Extern verbinding maken met uw virtuele machine

 Gebruik de volgende opdracht op uw lokale machine om een sessie met een extern bureaublad te starten voor de virtuele machine. Vervang het IP-adres door het publicIPAddress van de virtuele machine. Wanneer u hierom wordt gevraagd, typt u de referenties die zijn gebruikt bij het maken van de virtuele machine.

```console
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>De toepassing bijwerken

In de vorige zelfstudie hebt u alleen bestanden naar het opslagaccount geüpload. Open `D:\git\storage-dotnet-perf-scale-app\Program.cs` in een teksteditor. Vervang de `Main`-methode door het volgende voorbeeld. Hierdoor wordt de uploadtaak als opmerking gemarkeerd en wordt na voltooiing de downloadtaak en de taak waarmee de inhoud wordt verwijderd uit het opslagaccount, als opmerking verwijderd.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to 
    // ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage 
    // client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        // await UploadFilesAsync();

        // Uncomment the following line to enable downloading of files from the storage account.
        // This is commented out initially to support the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        await DownloadFilesAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.
        // This is commented out initially as the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        // has you upload only for one tutorial and download for the other.
        if (!exception)
        {
            // await DeleteExistingContainersAsync();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

Nadat de toepassing is bijgewerkt, moet u de toepassing opnieuw bouwen. Open een `Command Prompt` en navigeer naar `D:\git\storage-dotnet-perf-scale-app`. Voer de toepassing opnieuw uit door `dotnet build` uit te voeren, zoals het volgende voorbeeld laat zien:

```console
dotnet build
```

## <a name="run-the-application"></a>De toepassing uitvoeren

Nadat de toepassing opnieuw is gebouwd is het tijd om de toepassing met de bijgewerkte code uit te voeren. Als deze niet al is geopend, opent u een `Command Prompt` en navigeert u naar `D:\git\storage-dotnet-perf-scale-app`.

Typ `dotnet run` om de toepassing uit te voeren.

```console
dotnet run
```

De taak `DownloadFilesAsync` wordt in het volgende voorbeeld weergegeven:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

De toepassing leest de containers die zich in het opslagaccount bevinden en die zijn opgegeven in de **storageconnectionstring**. Het doorloopt de blobs met behulp van de methode [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) en downloadt deze naar de lokale computer met behulp van de methode [DownloadToAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_DownloadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

De toepassing leest de containers die zich in het opslagaccount bevinden en die zijn opgegeven in de **storageconnectionstring**. Het doorloopt de blobs 10 tegelijk door de methode [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync) in de containers te gebruiken en downloadt deze naar de lokale computer met behulp van de [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) -methode.

De volgende tabel bevat de [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) die voor elke blob is gedefinieerd, wanneer deze wordt gedownload.

|Eigenschap|Waarde|Beschrijving|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Met deze eigenschap wordt de controle uitgeschakeld van de MD5-hash van de inhoud die wordt geüpload. MD5-validatie zorgt voor een snellere overdracht. Maar hiermee wordt de geldigheid of de integriteit van de bestanden die worden overgebracht, niet bevestigd. |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| Deze eigenschap bepaalt of een MD5-hash wordt berekend en opgeslagen.   |

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 
    // hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.
    // Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container, 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

---

### <a name="validate-the-connections"></a>De verbindingen valideren

Terwijl de bestanden worden gedownload, kunt u controleren hoeveel gelijktijdige verbindingen naar uw opslagaccount er zijn. Open een console venster en typ `netstat -a | find /c "blob:https"` . Met deze opdracht wordt het aantal verbindingen weer gegeven dat momenteel is geopend. Zoals u in het volgende voor beeld kunt zien, waren er meer dan 280 verbindingen geopend bij het downloaden van bestanden van het opslag account.

```console
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Volgende stappen

In deel drie van de serie hebt u geleerd over het downloaden van grote hoeveel heden gegevens uit een opslag account, met inbegrip van het volgende:

> [!div class="checklist"]
> * De toepassing uitvoeren
> * Het aantal verbindingen valideren

Ga naar deel vier van de serie om de metrische gegevens over door Voer en latentie te controleren in de portal.

> [!div class="nextstepaction"]
> [Metrische gegevens over doorvoer en latentie controleren in de portal](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
