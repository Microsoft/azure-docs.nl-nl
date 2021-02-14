---
title: BLOB-versie beheer inschakelen en beheren
titleSuffix: Azure Storage
description: Meer informatie over het inschakelen van BLOB-versie beheer in de Azure Portal of met behulp van een Azure Resource Manager sjabloon.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 5b6bd16eacf4b1bbb7b93f5500813e7fa9dc7eef
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095835"
---
# <a name="enable-and-manage-blob-versioning"></a>BLOB-versie beheer inschakelen en beheren

U kunt versie beheer van Blob Storage inschakelen om automatisch eerdere versies van een BLOB te behouden wanneer deze wordt gewijzigd of verwijderd. Wanneer BLOB-versie beheer is ingeschakeld, kunt u een eerdere versie van een BLOB herstellen om uw gegevens te herstellen als deze ten onrechte zijn gewijzigd of verwijderd.

In dit artikel wordt beschreven hoe u BLOB-versie beheer in-of uitschakelt voor het opslag account met behulp van de Azure Portal of een Azure Resource Manager sjabloon. Zie [BLOB-versie beheer](versioning-overview.md)voor meer informatie over blob-versie beheer.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Blobversiebeheer inschakelen

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u BLOB-versie beheer wilt inschakelen voor een opslag account in de Azure Portal:

1. Navigeer naar uw opslag account in de portal.
1. Kies onder **BLOB service** de optie **gegevens beveiliging**.
1. Selecteer **ingeschakeld** in de sectie **versie beheer** .

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Scherm afbeelding die laat zien hoe u BLOB-versie beheer inschakelt in Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u BLOB-versie beheer wilt inschakelen voor een opslag account met Power shell, installeert u eerst de [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) -module versie 2.3.0 of hoger. Vervolgens roept u de opdracht [Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) aan om versie beheer in te scha kelen, zoals wordt weer gegeven in het volgende voor beeld. Vergeet niet om de waarden tussen punt haken te vervangen door uw eigen waarden:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -IsVersioningEnabled $true
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u BLOB-versie beheer wilt inschakelen voor een opslag account met Azure CLI, installeert u eerst de Azure CLI-versie 2.2.0 of hoger. Vervolgens roept u de opdracht [AZ Storage account BLOB-Service-Properties update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) aan om versie beheer in te scha kelen, zoals wordt weer gegeven in het volgende voor beeld. Vergeet niet om de waarden tussen punt haken te vervangen door uw eigen waarden:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Sjabloon](#tab/template)

Als u BLOB-versie beheer met een sjabloon wilt inschakelen, maakt u een sjabloon met de eigenschap **IsVersioningEnabled** in **waar**. In de volgende stappen wordt beschreven hoe u een sjabloon maakt in de Azure Portal.

1. Kies in het Azure Portal **een resource maken**.
1. Typ in **de Marketplace zoeken de** **sjabloon implementatie** en druk vervolgens op **Enter**.
1. Kies **Sjabloonimlementatie**, kies **maken** en kies vervolgens **uw eigen sjabloon bouwen in de editor**.
1. Plak in de sjabloon editor de volgende JSON. Vervang de tijdelijke plaatsaanduiding `<accountName>` door de naam van uw opslagaccount.
1. Sla de sjabloon op.
1. Geef de resource groep van het account op en kies vervolgens de knop **aanschaffen** om de sjabloon te implementeren en BLOB-versie beheer in te scha kelen.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Zie [resources implementeren met Azure Portal](../../azure-resource-manager/templates/deploy-portal.md)voor meer informatie over het implementeren van resources met sjablonen in de Azure Portal.

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Een BLOB wijzigen om een nieuwe versie te activeren

In het volgende code voorbeeld ziet u hoe u het maken van een nieuwe versie kunt activeren met de Azure Storage-client bibliotheek voor .NET, versie [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) of hoger. Voordat u dit voor beeld uitvoert, moet u ervoor zorgen dat versie beheer voor uw opslag account is ingeschakeld.

In het voor beeld wordt een blok-BLOB gemaakt en vervolgens worden de meta gegevens van de BLOB bijgewerkt. Bij het bijwerken van de meta gegevens van de BLOB wordt het maken van een nieuwe versie geactiveerd. In het voor beeld worden de initiële versie en de huidige versie opgehaald en wordt aangegeven dat alleen de huidige versie de meta gegevens bevat.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>BLOB-versies weer geven

Als u BLOB-versies of moment opnamen wilt weer geven met de .NET V12-client bibliotheek, geeft u de para meter [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) op met het veld **versie** .

In het volgende code voorbeeld ziet u hoe de versie van de blobs wordt weer gegeven met de Azure Storage-client bibliotheek voor .NET, versie [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) of hoger. Voordat u dit voor beeld uitvoert, moet u ervoor zorgen dat versie beheer voor uw opslag account is ingeschakeld.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Volgende stappen

- [BLOB-versie beheer](versioning-overview.md)
- [Voorlopig verwijderen voor Azure Storage-blobs](./soft-delete-blob-overview.md)