---
title: Tijdelijke verwijderde blobs beheren en herstellen
titleSuffix: Azure Storage
description: Met voorlopig verwijderde blobs en moment opnamen beheren en herstellen met de Azure Portal of met de Azure Storage-client bibliotheken.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d5ef85d947ae999fd94ba5a6e9cdb00baec9786
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556103"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>Tijdelijke verwijderde blobs beheren en herstellen

Met de methode voor het tijdelijk verwijderen van blobs worden een afzonderlijke Blob en de bijbehorende versies, moment opnamen en meta gegevens beschermd tegen onbedoeld verwijderen of worden overschreven door de verwijderde gegevens in het systeem gedurende een bepaalde periode te bewaren. Tijdens de retentie periode kunt u de BLOB tijdens het verwijderen herstellen naar de status. Nadat de Bewaar periode is verlopen, wordt de BLOB permanent verwijderd. Zie [voorlopig verwijderen voor blobs](soft-delete-blob-overview.md)voor meer informatie over het voorlopig verwijderen van blobs.

Het dynamisch verwijderen van blobs maakt deel uit van een uitgebreide strategie voor gegevens beveiliging voor BLOB-gegevens. Zie [overzicht van gegevens beveiliging](data-protection-overview.md)voor meer informatie over de aanbevelingen van micro soft voor gegevens bescherming.

## <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Met de Azure Portal tijdelijke verwijderde blobs beheren

U kunt de Azure Portal gebruiken om voorlopig verwijderde blobs en moment opnamen weer te geven en te herstellen.

### <a name="view-deleted-blobs"></a>Verwijderde blobs weer geven

Wanneer blobs zacht worden verwijderd, zijn ze standaard onzichtbaar in de Azure Portal. Als u verwijderde blobs wilt weer geven, gaat u naar de **overzichts** pagina voor de container en schakelt u de instelling **Verwijderde blobs weer geven** in. Voorlopig verwijderde blobs worden weer gegeven met de status **verwijderd**.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Scherm afbeelding die laat zien hoe u met zachte verwijderde blobs in Azure Portal kunt weer geven":::

Selecteer vervolgens de verwijderde Blob in de lijst met blobs om de eigenschappen ervan weer te geven. Op het tabblad **overzicht** ziet u dat de status van de blob is ingesteld op **verwijderd**. In de portal wordt ook het aantal dagen weer gegeven totdat de BLOB permanent wordt verwijderd.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Scherm opname van eigenschappen van met Soft verwijderde Blob in Azure Portal":::

### <a name="view-deleted-snapshots"></a>Verwijderde moment opnamen weer geven

Als u een BLOB verwijdert, worden ook alle moment opnamen verwijderd die aan de BLOB zijn gekoppeld. Als een zachte verwijderde BLOB moment opnamen bevat, kunnen de verwijderde moment opnamen ook worden weer gegeven in de portal. Geef de eigenschappen van de Soft verwijderde BLOB weer, navigeer naar het tabblad **moment opnamen** en Schakel **Verwijderde moment opnamen weer geven** uit.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="Scherm opname die wordt weer gegeven ":::

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Voorlopig verwijderde objecten herstellen als versie beheer is uitgeschakeld

Als u een voorlopig verwijderde Blob in de Azure Portal wilt herstellen wanneer BLOB-versie beheer niet is ingeschakeld, geeft u eerst de eigenschappen van de BLOB weer en selecteert u vervolgens de knop **verwijderen ongedaan** maken op het tabblad **overzicht** . Als u een BLOB herstelt, worden ook alle moment opnamen hersteld die zijn verwijderd tijdens de tijdelijke Bewaar periode voor het verwijderen.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="Scherm afbeelding die laat zien hoe u een voorlopig verwijderde BLOB kunt herstellen in Azure Portal":::

Als u een voorlopig verwijderde moment opname wilt promo veren naar de basis-blob, moet u eerst controleren of de voorlopig verwijderde moment opnamen van de BLOB zijn hersteld. Selecteer de knop **verwijderen ongedaan** maken om de voorlopig verwijderde moment opnamen van de BLOB te herstellen, zelfs als de basis-blob zelf niet zacht is verwijderd. Selecteer vervolgens de moment opname die u wilt promo veren en gebruik de knop **moment opname verhogen** om de basis-BLOB te overschrijven met de inhoud van de moment opname.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="Scherm afbeelding die laat zien hoe u een moment opname kunt promo veren naar de basis-BLOB":::

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Tijdelijke verwijderde blobs herstellen als versie beheer is ingeschakeld

Als u een voorlopig verwijderde Blob in de Azure Portal wilt herstellen wanneer versie beheer is ingeschakeld, selecteert u de zachte verwijderde blob om de eigenschappen ervan weer te geven en selecteert u vervolgens het tabblad **versies** . Selecteer de versie die u wilt promo veren als de huidige versie en selecteer vervolgens **huidige versie maken**.  

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Scherm afbeelding die laat zien hoe u een versie kunt promo veren om een BLOB te herstellen in Azure Portal":::

Als u verwijderde versies of moment opnamen wilt herstellen wanneer versie beheer is ingeschakeld, geeft u de eigenschappen van de BLOB weer en selecteert u vervolgens de knop **verwijderen ongedaan** maken op het tabblad **overzicht** .

> [!NOTE]
> Wanneer versie beheer is ingeschakeld, wordt door het selecteren van de knop **verwijderen** in een verwijderde BLOB alle door soft verwijderde versies of moment opnamen hersteld, maar wordt de basis-BLOB niet hersteld. Als u de basis-BLOB wilt herstellen, moet u een eerdere versie promoten.

## <a name="manage-soft-deleted-blobs-with-code"></a>Door soft verwijderde blobs beheren met code

U kunt de Azure Storage-client bibliotheken gebruiken om een voorlopig verwijderde BLOB of moment opname te herstellen. In de volgende voor beelden ziet u hoe u de .NET-client bibliotheek gebruikt.

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>Voorlopig verwijderde objecten herstellen als versie beheer is uitgeschakeld

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Als u verwijderde blobs wilt herstellen wanneer versie beheer niet is ingeschakeld, roept u de bewerking voor het verwijderen van de [BLOB](/rest/api/storageservices/undelete-blob) aan op deze blobs. Met de bewerking voor het verwijderen van een **BLOB** worden zachte verwijderde blobs en verwijderde moment opnamen die zijn gekoppeld aan deze blobs, hersteld.

Het aanroepen van een **Verwijder BLOB** voor een blob die niet is verwijderd, heeft geen effect. In het volgende voor beeld wordt het **verwijderen van BLOB** op alle blobs in een container aangeroepen en worden de voorlopig verwijderde blobs en hun moment opnamen hersteld:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Als u een specifieke versie wilt herstellen, moet u eerst de bewerking voor het **verwijderen van blobs** op de basis-BLOB of-versie aanroepen en vervolgens de gewenste versie kopiëren via de basis-blob. In het volgende voor beeld wordt een blok-BLOB teruggezet naar de laatst opgeslagen versie:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Als u verwijderde blobs wilt herstellen wanneer versie beheer niet is ingeschakeld, roept u de bewerking voor het verwijderen van de [BLOB](/rest/api/storageservices/undelete-blob) aan op deze blobs. Met de bewerking voor het verwijderen van een **BLOB** worden zachte verwijderde blobs en verwijderde moment opnamen die zijn gekoppeld aan deze blobs, hersteld.

Het aanroepen van een **Verwijder BLOB** voor een blob die niet is verwijderd, heeft geen effect. In het volgende voor beeld wordt het **verwijderen van BLOB** op alle blobs in een container aangeroepen en worden de voorlopig verwijderde blobs en hun moment opnamen hersteld:

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Als u een specifieke moment opname wilt herstellen, moet u eerst de bewerking voor het verwijderen van een **BLOB** op de basis-BLOB aanroepen en vervolgens de gewenste moment opname via de basis-BLOB kopiëren. In het volgende voor beeld wordt een blok-BLOB teruggezet naar de meest recent gegenereerde moment opname:

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>Tijdelijke verwijderde blobs herstellen als versie beheer is ingeschakeld

Als u een voorlopig verwijderde BLOB wilt herstellen wanneer versie beheer is ingeschakeld, kopieert u een eerdere versie via de basis-blob met een [Kopieer-BLOB](/rest/api/storageservices/copy-blob) of kopieert u de [URL van](/rest/api/storageservices/copy-blob-from-url) de bewerking.  

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Niet van toepassing. BLOB-versie beheer wordt alleen ondersteund in de Azure Storage-client bibliotheken versie 12. x en hoger.

---

## <a name="next-steps"></a>Volgende stappen

- [Voorlopig verwijderen voor Blob Storage](./soft-delete-blob-overview.md)
- [Voorlopig verwijderen inschakelen voor blobs](soft-delete-blob-enable.md)
- [BLOB-versie beheer](versioning-overview.md)
