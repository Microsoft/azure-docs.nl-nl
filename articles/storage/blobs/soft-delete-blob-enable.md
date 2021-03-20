---
title: Zacht verwijderen voor blobs inschakelen en beheren
titleSuffix: Azure Storage
description: Schakel zacht verwijderen voor blobs in om uw gegevens eenvoudiger te herstellen wanneer deze foutief worden gewijzigd of verwijderd.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/15/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: a74860b7adf4dade5aedc71a4960595cbe55eaf0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995298"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>Zacht verwijderen voor blobs inschakelen en beheren

Met de methode voor het zacht verwijderen van blobs worden uw gegevens per ongeluk of onbedoeld gewijzigd of verwijderd. Wanneer het dynamisch verwijderen van blobs is ingeschakeld voor een opslag account, kunnen blobs, Blob-versies en moment opnamen in dat opslag account worden hersteld nadat ze zijn verwijderd, binnen een Bewaar periode die u opgeeft.

Als er een kans is dat uw gegevens per ongeluk worden gewijzigd of verwijderd door een toepassing of een ander opslag account, raadt micro soft aan om de optie voor het voorlopig verwijderen van blobs in te scha kelen. In dit artikel wordt beschreven hoe u de optie voor het voorlopig verwijderen van blobs inschakelt. Zie [voorlopig verwijderen voor blobs](soft-delete-blob-overview.md)voor meer informatie over het voorlopig verwijderen van blobs.

Zie voor meer informatie over het inschakelen van voorlopig verwijderen voor containers, voor het inschakelen [en beheren van voorlopig verwijderen voor containers](soft-delete-container-enable.md).

## <a name="enable-blob-soft-delete"></a>Voorlopig verwijderen van BLOB inschakelen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Schakel de optie voor het voorlopig verwijderen van blobs in uw opslag account in met behulp van Azure Portal:

1. Ga in [Azure Portal](https://portal.azure.com/) naar uw opslagaccount.
1. Zoek de optie **gegevens bescherming** onder **BLOB service**.
1. Stel de eigenschap **zacht verwijderen voor BLOB** in op *ingeschakeld*.
1. Geef onder **Bewaar beleid** op hoe lang voorlopig verwijderde blobs worden bewaard door Azure Storage.
1. Sla uw wijzigingen op.

![Scherm opname van Azure Portal met de gegevens bescherming BLOB-service gekozen.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-configuration.png)

Als u de voorlopig verwijderde blobs wilt weer geven, schakelt u het selectie vakje **Verwijderde blobs weer geven** in.

![Scherm afbeelding van de pagina gegevens bescherming BLOB-service met de optie verwijderde blobs weer geven gemarkeerd.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

Als u tijdelijke verwijderde moment opnamen voor een bepaalde BLOB wilt weer geven, selecteert u de BLOB en klikt u vervolgens op **moment opnamen weer geven**.

![Scherm afbeelding van de pagina gegevens bescherming BLOB-service met de optie moment opnamen weer geven gemarkeerd.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Zorg ervoor dat het selectie vakje **Verwijderde moment opnamen weer geven** is geselecteerd.

![Scherm afbeelding van de pagina moment opnamen weer geven met de optie verwijderde blobs weer geven gemarkeerd.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Wanneer u op een zachte verwijderde BLOB of moment opname klikt, ziet u de nieuwe BLOB-eigenschappen. Ze geven aan wanneer het object is verwijderd en hoeveel dagen resteren totdat de moment opname van de BLOB of BLOB permanent is verlopen. Als het voorlopig verwijderde object geen moment opname is, hebt u ook de mogelijkheid om de verwijdering ervan ongedaan te maken.

![Scherm afbeelding van de details van een voorlopig verwijderd object.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-properties.png)

Houd er rekening mee dat het verwijderen van een BLOB ook de verwijdering van alle gekoppelde moment opnamen ongedaan maakt. Als u het verwijderen van tijdelijke verwijderde moment opnamen voor een actieve BLOB ongedaan wilt maken, klikt u op de BLOB en selecteert u **verwijderen van alle moment opnamen ongedaan** maken.

![Scherm afbeelding van de details van een voorlopig verwijderde blob.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Wanneer u de moment opnamen van een BLOB hebt verwijderd, kunt u op **niveau verhogen** klikken om een moment opname te kopiëren over de hoofd-blob, waardoor de BLOB wordt teruggezet naar de moment opname.

![Scherm afbeelding van de pagina moment opnamen weer geven met de optie niveau verhogen gemarkeerd.](media/soft-delete-blob-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u zacht verwijderen wilt inschakelen, werkt u de service-eigenschappen van een BLOB-client bij. In het volgende voor beeld wordt zacht verwijderen ingeschakeld voor een subset van accounts in een abonnement:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```

U kunt controleren of de functie voor voorlopig verwijderen is ingeschakeld met behulp van de volgende opdracht:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Als u blobs wilt herstellen die per ongeluk zijn verwijderd, roept u **dedelete BLOB** aan op deze blobs. Houd er rekening mee dat bij het aanroepen van **undelete BLOB**, zowel op actieve als Soft verwijderde blobs, alle gekoppelde tijdelijke verwijderde moment opnamen worden hersteld als actief. In het volgende voor beeld wordt de **verwijdering van BLOB** op alle voorlopig verwijderde en actieve blobs in een container aangeroepen:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Gebruik de volgende opdracht om het huidige Bewaar beleid voor voorlopig verwijderen te vinden:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Als u zacht verwijderen wilt inschakelen, werkt u de service-eigenschappen van een BLOB-client bij:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Als u wilt controleren of zacht verwijderen is ingeschakeld, gebruikt u de volgende opdracht: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Als u zacht verwijderen wilt inschakelen, werkt u de service-eigenschappen van een BLOB-client bij:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Als u zacht verwijderen wilt inschakelen, werkt u de service-eigenschappen van een BLOB-client bij:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

Als u blobs wilt herstellen die per ongeluk zijn verwijderd, roept u verwijderen ongedaan maken op deze blobs. Houd er rekening mee dat bij het aanroepen van **verwijderen ongedaan** maken, zowel op actieve als Soft verwijderde blobs, alle gekoppelde tijdelijke verwijderde moment opnamen worden hersteld als actief. In het volgende voor beeld wordt de verwijdering van alle voorlopig verwijderde en actieve blobs in een container ongedaan gemaakt:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

Als u wilt herstellen naar een specifieke BLOB-versie, roept u eerst undelete aan voor een BLOB en kopieert u vervolgens de gewenste moment opname over de blob. In het volgende voor beeld wordt een blok-BLOB hersteld naar de meest recent gegenereerde moment opname:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Als u zacht verwijderen wilt inschakelen, werkt u de service-eigenschappen van een BLOB-client bij:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Als u blobs wilt herstellen die per ongeluk zijn verwijderd, roept u **dedelete BLOB** aan op deze blobs. Houd er rekening mee dat bij het aanroepen van **undelete BLOB**, zowel op actieve als Soft verwijderde blobs, alle gekoppelde tijdelijke verwijderde moment opnamen worden hersteld als actief. In het volgende voor beeld wordt de **verwijdering van BLOB** op alle voorlopig verwijderde en actieve blobs in een container aangeroepen:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Als u wilt herstellen naar een specifieke BLOB-versie, roept u eerst de bewerking voor het verwijderen van de **BLOB** aan en kopieert u de gewenste moment opname over de blob. In het volgende voor beeld wordt een blok-BLOB hersteld naar de meest recent gegenereerde moment opname:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

## <a name="next-steps"></a>Volgende stappen

- [Voorlopig verwijderen voor Blob Storage](./soft-delete-blob-overview.md)
- [BLOB-versie beheer](versioning-overview.md)