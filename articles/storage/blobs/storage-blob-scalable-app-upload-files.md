---
title: Grote hoeveelheden willekeurige gegevens gelijktijdig uploaden naar Azure Storage
description: Informatie over het gebruik van de Azure Storage-clientbibliotheek om grote hoeveelheden willekeurige gegevens gelijktijdig naar een Azure Storage-account te uploaden
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: ed7020a58f3f15403108934bcc3fab644bd1b627
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584462"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Grote hoeveelheden willekeurige gegevens gelijktijdig uploaden naar Azure Storage

Deze zelfstudie is deel twee van een serie. Deze zelfstudie laat zien hoe u een toepassing kunt implementeren waarmee grote hoeveelheden willekeurige gegevens naar een Azure Storage-account kunnen worden geüpload.

In deel twee van de serie leert u het volgende:

> [!div class="checklist"]
> * De verbindingsreeks configureren
> * De toepassing bouwen
> * De toepassing uitvoeren
> * Het aantal verbindingen valideren

Microsoft Azure Blob Storage biedt een schaal bare service voor het opslaan van uw gegevens. Om er zeker van te kunnen zijn of uw toepassing optimaal presteert, is het raadzaam te weten hoe blob-opslag werkt. Het is belangrijk dat u kennis hebt van de limieten van Azure-blobs. Als u meer wilt weten over deze limieten, gaat u naar: [Schaalbaarheids- en prestatiedoelen voor blob-opslag](../blobs/scalability-targets.md).

[Naamgevingsregels voor partities](../blobs/storage-performance-checklist.md#partitioning) kan ook een belangrijke factor zijn bij het ontwerpen van een High Performance-toepassing met behulp van blobs. Voor blokgrootten groter dan of gelijk aan vier MiB, worden [blok-blobs met hoge doorvoer](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) gebruikt, en zijn de naamgevingsregels voor partities niet van invloed op de prestaties. Voor blokgrootten kleiner dan vier MiB, maakt Azure-opslag voor schalen en taakverdeling gebruik van een op bereiken gebaseerd partitieschema. Deze configuratie betekent dat bestanden met vergelijkbare naamconventies of voorvoegsels naar dezelfde partitie gaan. Deze logica bevat de naam van de container waar de bestanden naar worden geüpload. In deze zelfstudie gebruikt u de bestanden die GUID's voor namen en willekeurig gegenereerde inhoud bevatten. Deze worden vervolgens naar vijf verschillende containers met willekeurige namen geüpload.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie, moet u de vorige zelfstudie over opslag hebben voltooid: [Create a virtual machine and storage account for a scalable application][previous-tutorial] (Een virtuele machine en opslagaccount voor een schaalbare toepassing maken).

## <a name="remote-into-your-virtual-machine"></a>Extern verbinding maken met uw virtuele machine

Gebruik de volgende opdracht op uw lokale machine om een sessie met een extern bureaublad te starten voor de virtuele machine. Vervang het IP-adres door het publicIPAddress van de virtuele machine. Wanneer u hierom wordt gevraagd, typt u de referenties die u hebt gebruikt bij het maken van de virtuele machine.

```console
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>De verbindingsreeks configureren

Ga in Azure Portal naar uw opslagaccount. Selecteer bij **Instellingen** in uw opslagaccount de optie **Toegangssleutels**. Kopieer de **verbindingsreeks** uit de primaire of secundaire sleutel. Meld u aan bij de virtuele machine die u tijdens de vorige zelfstudie hebt gemaakt. Open een **Opdrachtprompt** als beheerder en voer de opdracht `setx` uit met de `/m`-switch. Met deze opdracht wordt een omgevingsvariabele van een machine-instelling opgeslagen. De omgevingsvariabele wordt pas beschikbaar wanneer u de **Opdrachtprompt** opnieuw laadt. Vervang **\<storageConnectionString\>** in het volgende voorbeeld:

```console
setx storageconnectionstring "<storageConnectionString>" /m
```

Wanneer u klaar bent, opent u een andere **Opdrachtprompt**, navigeert u naar `D:\git\storage-dotnet-perf-scale-app` en typt u `dotnet build` om de toepassing te bouwen.

## <a name="run-the-application"></a>De toepassing uitvoeren

Navigeer naar `D:\git\storage-dotnet-perf-scale-app`.

Typ `dotnet run` om de toepassing uit te voeren. De eerste keer dat u `dotnet` uitvoert, wordt uw lokale pakketcache gevuld, waardoor de snelheid voor het terugzetten van back-ups verbetert en offlinetoegang mogelijk is. De uitvoering van deze opdracht duurt maximaal een minuut en is eenmalig.

```console
dotnet run
```

De toepassing maakt vijf containers met willekeurige namen en begint met het uploaden van de bestanden in de staging-map naar het opslagaccount.

De `UploadFilesAsync` methode wordt weer gegeven in het volgende voor beeld:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_UploadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Het minimum en maximum aantal threads worden ingesteld op 100 om ervoor te zorgen dat een groot aantal gelijktijdige verbindingen is toegestaan.

```csharp
private static async Task UploadFilesAsync()
{
    // Create five randomly named containers to store the uploaded files.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();

    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // Path to the directory to upload
    string uploadPath = currentdir + "\\upload";

    // Start a timer to measure how long it takes to upload all the files.
    Stopwatch time = Stopwatch.StartNew();

    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);

        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections
        // are retried with a back off policy. As multiple large files are being uploaded using
        // large block sizes, this can cause an issue if an exponential retry policy is not defined.
        // Additionally, parallel operations are enabled with a thread count of 8.
        // This should be a multiple of the number of processor cores in the machine.
        // Lastly, MD5 hash validation is disabled for this example, improving the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };

        // Create a new instance of the SemaphoreSlim class to 
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set the block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;

            await sem.WaitAsync();

            // Create a task for each file to upload. The tasks are
            // added to a collection and all run asynchronously.
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));

            count++;
        }

        // Run all the tasks asynchronously.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```
Naast het instellen van de limietinstellingen voor threads en verbindingen zijn de [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) voor de methode [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) geconfigureerd om gebruik te maken van parallelle uitvoering en om validatie van de MD5-hash uit te schakelen. De bestanden worden geüpload in blokken van 100 MB. Deze configuratie biedt betere prestaties maar kan duur zijn als er een slecht presterend netwerk wordt gebruikt, omdat, wanneer er zich een storing voordoet, het gehele blok van 100 MB opnieuw wordt geüpload.

|Eigenschap|Waarde|Beschrijving|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| Met deze instelling wordt de blob in blokken opgesplitst bij het uploaden. Voor de beste prestaties moet deze waarde acht keer het aantal kerngeheugens zijn. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Met deze eigenschap wordt de controle uitgeschakeld van de MD5-hash van de inhoud die wordt geüpload. MD5-validatie zorgt voor een snellere overdracht. Maar hiermee wordt de geldigheid of de integriteit van de bestanden die worden overgebracht, niet bevestigd.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| Deze eigenschap bepaalt of een MD5-hash wordt berekend en samen met het bestand opgeslagen.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| Twee seconden uitstel bij maximaal tien nieuwe pogingen |Hiermee bepaalt u het beleid voor het opnieuw proberen van aanvragen. Bij verbindingsfouten wordt opnieuw geprobeerd. In dit voorbeeld is een [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry)-beleid geconfigureerd met een uitstel van twee seconden en een maximumaantal nieuwe pogingen van 10. Deze instelling is belangrijk als de toepassing de schaalbaarheidsdoelen voor blob-opslag bijna heeft bereikt. Zie [Schaalbaarheids- en prestatiedoelen voor blob-opslag](../blobs/scalability-targets.md) voor meer informatie.  |

---

Het volgende voorbeeld bevat de afgekapte uitvoer van een toepassing die op een Windows-systeem wordt uitgevoerd.

```console
Created container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Created container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Created container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Created container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Created container 92480da9-f695-4a42-abe8-fb35e71eb887
Iterating in directory: C:\git\myapp\upload
Found 5 file(s)
Uploading 1d596d16-f6de-4c4c-8058-50ebd8141e4d.pdf to container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Uploading 242ff392-78be-41fb-b9d4-aee8152a6279.pdf to container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Uploading 38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.pdf to container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Uploading 45930d63-b0d0-425f-a766-cda27ff00d32.pdf to container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Uploading 5129b385-5781-43be-8bac-e2fbb7d2bd82.pdf to container 92480da9-f695-4a42-abe8-fb35e71eb887
Uploaded 5 files in 16.9552163 seconds
```

### <a name="validate-the-connections"></a>De verbindingen valideren

Terwijl de bestanden worden geüpload, kunt u controleren hoeveel gelijktijdige verbindingen naar uw opslagaccount er zijn. Open een console venster en typ `netstat -a | find /c "blob:https"` . Met deze opdracht wordt het aantal verbindingen weer gegeven dat momenteel is geopend. Zoals u in het volgende voor beeld kunt zien, zijn er 800-verbindingen geopend bij het uploaden van de wille keurige bestanden naar het opslag account. Deze waarde wijzigt tijdens het uploaden. Door blokken in brokken gelijktijdig te uploaden, wordt de hoeveelheid tijd die nodig is om inhoud over te dragen, aanzienlijk lager.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Volgende stappen

In deel twee uit de serie bent u meer te weten gekomen over het gelijktijdig uploaden van grote hoeveelheden willekeurige gegevens naar een opslagaccount, om het volgende te kunnen doen:

> [!div class="checklist"]
> * De verbindingsreeks configureren
> * De toepassing bouwen
> * De toepassing uitvoeren
> * Het aantal verbindingen valideren

Ga verder met deel drie van de serie als u grote hoeveelheden gegevens uit een opslagaccount wilt downloaden.

> [!div class="nextstepaction"]
> [Grote hoeveelheden willekeurige gegevens downloaden uit Azure Storage](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
