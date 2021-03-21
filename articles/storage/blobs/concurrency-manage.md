---
title: Gelijktijdigheid beheren in Blob Storage
titleSuffix: Azure Storage
description: Meer informatie over het beheren van meerdere schrijvers naar een BLOB door een optimistische of pessimistische gelijktijdigheid in uw toepassing te implementeren. Optimistische gelijktijdigheid controleert de ETag-waarde voor een BLOB en vergelijkt deze met de vrijgestelde ETag. Pessimistische gelijktijdigheid maakt gebruik van een exclusieve lease om de BLOB te vergren delen met andere schrijvers.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ea0bed0884a3a03e2cd15b274b2afb0f054b0cbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96523442"
---
# <a name="managing-concurrency-in-blob-storage"></a>Gelijktijdigheid beheren in Blob Storage

Moderne toepassingen hebben vaak meerdere gebruikers tegelijk de gegevens weer geven en bijwerken. Toepassings ontwikkelaars moeten zorgvuldig nadenken over hoe u een voorspel bare ervaring kunt bieden aan hun eind gebruikers, met name voor scenario's waarin meerdere gebruikers dezelfde gegevens kunnen bijwerken. Er zijn drie hoofd strategieën voor het gelijktijdigheid van gegevens die ontwikkel aars doorgaans overwegen:  

- **Optimistische gelijktijdigheid**: een toepassing die een update uitvoert, wordt als onderdeel van de update bepaald of de gegevens zijn gewijzigd sinds de laatste keer dat de toepassing de gegevens heeft gelezen. Als bijvoorbeeld twee gebruikers die een wikipagina bekijken, een update naar die pagina maken, moet het wiki-platform ervoor zorgen dat de tweede update de eerste update niet overschrijft. Het moet er ook voor zorgen dat beide gebruikers begrijpen of hun update is geslaagd. Deze strategie wordt meestal gebruikt in webtoepassingen.

- **Pessimistische gelijktijdigheid**: een toepassing die een update wilt uitvoeren, kan een object vergren delen voor komen dat andere gebruikers de gegevens kunnen bijwerken tot de vergren deling is opgeheven. In een scenario met een primaire/secundaire gegevens replicatie waarbij alleen de primaire updates worden uitgevoerd, bevat de primaire server meestal een exclusieve vergren deling voor de gegevens gedurende lange tijd om ervoor te zorgen dat niemand anders deze kan bijwerken.

- **Laatste schrijver WINS**: een benadering waarmee update bewerkingen kunnen door gaan zonder eerst te bepalen of een andere toepassing de gegevens heeft bijgewerkt sinds deze is gelezen. Deze methode wordt doorgaans gebruikt wanneer gegevens worden gepartitioneerd, waardoor meerdere gebruikers op hetzelfde moment geen toegang hebben tot dezelfde gegevens. Het kan ook nuttig zijn wanneer er verwerkte gegevens stromen worden verwerkt.

Azure Storage ondersteunt alle drie de strategieën, hoewel het een onderscheidende mogelijkheid is om volledige ondersteuning te bieden voor optimistische en pessimistische gelijktijdigheid. Azure Storage is ontworpen om een krachtig consistentie model te bieden dat garandeert dat nadat de service een invoeg-of update bewerking heeft uitgevoerd, de meest recente update door volgende Lees bewerkingen wordt geretourneerd.

Ontwikkel aars moeten niet alleen een geschikte gelijktijdigheids strategie selecteren, maar ook op de hoogte zijn van de manier waarop een opslag platform wijzigingen aanbrengt, met name voor het wijzigen van hetzelfde object in trans acties. Azure Storage maakt gebruik van snap shot-isolatie om Lees bewerkingen gelijktijdig met schrijf bewerkingen in één partitie toe te staan. Snap shot-isolatie garandeert dat alle Lees bewerkingen een consistente moment opname van de gegevens retour neren, zelfs wanneer er updates optreden.

U kunt ervoor kiezen om optimistische of pessimistische gelijktijdigheids modellen te gebruiken voor het beheren van de toegang tot blobs en containers. Als u geen expliciete strategie opgeeft, wordt standaard de laatste schrijver WINS.  

## <a name="optimistic-concurrency"></a>Optimistische gelijktijdigheid

Azure Storage wijst een id toe aan elk opgeslagen object. Deze id wordt bijgewerkt wanneer een schrijf bewerking wordt uitgevoerd op een object. De id wordt geretourneerd naar de client als onderdeel van een HTTP GET-antwoord in de ETag-header die is gedefinieerd door het HTTP-protocol.

Een client die een update uitvoert, kan de oorspronkelijke ETag samen met een voorwaardelijke koptekst verzenden om ervoor te zorgen dat een update alleen wordt uitgevoerd als aan een bepaalde voor waarde is voldaan. Als bijvoorbeeld de **if-match-** header is opgegeven, wordt door Azure Storage gecontroleerd of de waarde van de ETAG die in de update aanvraag is opgegeven, overeenkomt met de ETAG voor het object dat wordt bijgewerkt. Zie [voorwaardelijke kopteksten voor BLOB service bewerkingen opgeven](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)voor meer informatie over voorwaardelijke kopteksten.

Het overzicht van dit proces is als volgt:  

1. Haal een BLOB op uit Azure Storage. Het antwoord bevat een HTTP ETag-header waarde waarmee de huidige versie van het object wordt geïdentificeerd.
1. Wanneer u de BLOB bijwerkt, neemt u de ETag-waarde op die u in stap 1 hebt ontvangen in de voorwaardelijke koptekst **if-match** van de schrijf aanvraag. Azure Storage vergelijkt de ETag-waarde in de aanvraag met de huidige ETag-waarde van de blob.
1. Als de huidige ETag-waarde van de BLOB verschilt van het aantal dat is opgegeven in de voorwaardelijke koptekst van het **if-match** -bericht dat op de aanvraag is gegeven, retourneert Azure Storage HTTP-status code 412 (de voor waarde is mislukt). Deze fout geeft aan dat de client een andere proces heeft heeft de BLOB heeft bijgewerkt sinds de client het eerst heeft opgehaald.
1. Als de huidige ETag-waarde van de BLOB dezelfde versie is als de ETag in de voorwaardelijke koptekst in de **if-match-** header in de aanvraag, wordt Azure Storage de aangevraagde bewerking uitgevoerd en wordt de huidige ETAG-waarde van de BLOB bijgewerkt.  

De volgende code voorbeelden laten zien hoe u een **if-match-** voor waarde opstelt voor de schrijf aanvraag waarmee de ETAG-waarde voor een BLOB wordt gecontroleerd. Azure Storage evalueert of de huidige ETag van de BLOB hetzelfde is als de ETag die is opgegeven voor de aanvraag en voert de schrijf bewerking alleen uit als de twee ETag-waarden overeenkomen. Als een ander proces de BLOB in de tussenwaarde heeft bijgewerkt, retourneert Azure Storage een status bericht HTTP 412 (voorzover mislukt).  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstrateOptimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstrateOptimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate optimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    // Create test blob. The default strategy is last writer wins, so
    // write operation will overwrite existing blob if present.
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");

    // Retrieve the ETag from the newly created blob.
    string originalETag = blockBlob.Properties.ETag;
    Console.WriteLine("Blob added. Original ETag = {0}", originalETag);

    /// This code simulates an update by another client.
    string helloText = "Blob updated by another client.";
    // No ETag was provided, so original blob is overwritten and ETag updated.
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}", blockBlob.Properties.ETag);

    // Now try to update the blob using the original ETag value.
    try
    {
        Console.WriteLine(@"Attempt to update blob using original ETag
                            to generate if-match access condition");
        blockBlob.UploadText(helloText, accessCondition: AccessCondition.GenerateIfMatchCondition(originalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure as expected.
                                Blob's ETag does not match.");
        }
        else
        {
            throw;
        }
    }
    Console.WriteLine();
}
```

---

Azure Storage biedt ook ondersteuning voor andere voorwaardelijke kopteksten, waaronder **If-Modified-sinds**, **if-unmodified-sinds** en **If-None-Match**. Zie [voorwaardelijke headers opgeven voor BLOB-service bewerkingen](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)voor meer informatie.  

## <a name="pessimistic-concurrency-for-blobs"></a>Pessimistische gelijktijdigheid voor blobs

Als u een BLOB wilt vergren delen voor exclusief gebruik, kunt u er een lease op verkrijgen. Wanneer u de lease aanschaft, geeft u de duur van de lease op. Een beperkte lease kan tussen 15 en 60 seconden geldig zijn. Een lease kan ook oneindig zijn, wat een exclusieve vergren deling inhoudt. U kunt een beperkte lease vernieuwen om deze uit te breiden en u kunt de lease vrijgeven wanneer u er klaar mee bent. Azure Storage beperkte leases worden automatisch vrijgegeven wanneer deze verlopen.  

Met leases kunnen verschillende synchronisatie strategieën worden ondersteund, waaronder exclusieve schrijf-en gedeelde Lees bewerkingen, exclusieve write/exclusieve Lees bewerkingen en gedeelde write/exclusieve Lees bewerkingen. Wanneer er een lease bestaat, dwingt Azure Storage exclusieve toegang af voor schrijf bewerkingen voor de lease houder. Voor de exclusiviteit voor lees bewerkingen moet de ontwikkelaar echter controleren of alle client toepassingen een lease-ID gebruiken en dat slechts één client tegelijk een geldige lease-ID heeft. Lees bewerkingen die geen lease-ID bevatten, resulteren in gedeelde Lees bewerkingen.  

De volgende code voorbeelden laten zien hoe u een exclusieve lease kunt verkrijgen voor een blob, hoe u de inhoud van de BLOB bijwerkt door de lease-ID op te geven en de lease vervolgens vrijgeven. Als de lease actief is en de lease-ID niet is opgegeven voor een schrijf aanvraag, mislukt de schrijf bewerking met de fout code 412 (de voor waarde is mislukt).  

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Concurrency.cs" id="Snippet_DemonstratePessimisticConcurrencyBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
public void DemonstratePessimisticConcurrencyBlob(string containerName, string blobName)
{
    Console.WriteLine("Demonstrate pessimistic concurrency");

    // Parse connection string and create container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExists();

    CloudBlockBlob blockBlob = container.GetBlockBlobReference(blobName);
    blockBlob.UploadText("Hello World!");
    Console.WriteLine("Blob added.");

    // Acquire lease for 15 seconds.
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation should succeed.
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    // Simulate another client attempting to update to blob without providing lease.
    try
    {
        // Operation will fail as no valid lease was provided.
        Console.WriteLine("Now try to update blob without valid lease.");
        blockBlob.UploadText("Update operation will fail without lease.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine(@"Precondition failure error as expected.
                                Blob lease not provided.");
        }
        else
        {
            throw;
        }
    }

    // Release lease proactively.
    blockBlob.ReleaseLease(accessCondition);
    Console.WriteLine();
}
```

---

## <a name="pessimistic-concurrency-for-containers"></a>Pessimistische gelijktijdigheid voor containers

Leases in containers bieden dezelfde synchronisatie strategieën die worden ondersteund voor blobs, waaronder exclusieve schrijf-en gedeelde Lees-, exclusieve write/exclusieve Lees-en gedeelde write/exclusieve Lees bewerking. Voor containers wordt de exclusieve vergren deling echter alleen afgedwongen voor Delete-bewerkingen. Als u een container met een actieve lease wilt verwijderen, moet een client de actieve lease-ID met de aanvraag verwijderen bevatten. Alle andere container bewerkingen worden uitgevoerd op een geleasde container zonder de lease-ID.  

## <a name="next-steps"></a>Volgende stappen

* [Voorwaardelijke kopteksten voor Blob service bewerkingen opgeven](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)
* [Lease-container](/rest/api/storageservices/lease-container)
* [Lease-blob](/rest/api/storageservices/lease-blob)
