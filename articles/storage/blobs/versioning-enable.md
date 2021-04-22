---
title: Blobversies inschakelen en beheren
titleSuffix: Azure Storage
description: Informatie over het inschakelen van blobversies in de Azure Portal of met behulp van een Azure Resource Manager sjabloon.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: f6a1d315342ea98ccaf1382630eccca876ada3f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870189"
---
# <a name="enable-and-manage-blob-versioning"></a>Blobversies inschakelen en beheren

U kunt blobopslagversies inschakelen om automatisch eerdere versies van een blob te onderhouden wanneer deze wordt gewijzigd of verwijderd. Wanneer blobversies zijn ingeschakeld, kunt u een eerdere versie van een blob herstellen om uw gegevens te herstellen als deze per ongeluk worden gewijzigd of verwijderd.

In dit artikel wordt beschreven hoe u blobversies voor het opslagaccount in- of uit kunt schakelen met behulp van de Azure Portal of een Azure Resource Manager sjabloon. Zie Blobversies voor meer informatie over [blobversies.](versioning-overview.md)

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Blobversiebeheer inschakelen

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Blobversies inschakelen voor een opslagaccount in de Azure Portal:

1. Navigeer naar uw opslagaccount in de portal.
1. Kies **Blob service** de optie **Gegevensbeveiliging.**
1. Selecteer ingeschakeld **in de** sectie **Versien.**

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Schermopname die laat zien hoe u blobversies kunt inschakelen in Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u blobversiebeheer wilt inschakelen voor een opslagaccount met PowerShell, installeert u eerst moduleversie 2.3.0 of hoger van [Az.Storage.](https://www.powershellgallery.com/packages/Az.Storage) Roep vervolgens de [opdracht Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) aan om versie-uitvoering in teschakelen, zoals wordt weergegeven in het volgende voorbeeld. Vergeet niet om de waarden tussen vierkante haken te vervangen door uw eigen waarden:

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

Als u blobversies wilt inschakelen voor een opslagaccount met Azure CLI, installeert u eerst Azure CLI versie 2.2.0 of hoger. Roep vervolgens de [opdracht az storage account blob-service-properties update aan](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) om versie-uitvoering in te stellen, zoals wordt weergegeven in het volgende voorbeeld. Vergeet niet om de waarden tussen vierkante haken te vervangen door uw eigen waarden:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Sjabloon](#tab/template)

Als u blobversies wilt inschakelen met een sjabloon, maakt u een sjabloon met de eigenschap **IsVersioningEnabled** op **true**. In de volgende stappen wordt beschreven hoe u een sjabloon maakt in de Azure Portal.

1. Kies in Azure Portal de **optie Een resource maken.**
1. In **Marketplace doorzoeken** typt u **sjabloonimplementatie** en drukt u op **ENTER.**
1. Kies **Sjabloonimlementatie**, kies **Maken** en kies vervolgens Uw eigen sjabloon bouwen in **de editor**.
1. Plak in de sjablooneditor de volgende JSON. Vervang de tijdelijke plaatsaanduiding `<accountName>` door de naam van uw opslagaccount.
1. Sla de sjabloon op.
1. Geef de resourcegroep van het account op en kies vervolgens **de** knop Kopen om de sjabloon te implementeren en blobversiebeheer in teschakelen.

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

Zie Resources implementeren met Azure Portal voor meer informatie over het implementeren van [resources](../../azure-resource-manager/templates/deploy-portal.md)met Azure Portal.

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Een blob wijzigen om een nieuwe versie te activeren

In het volgende codevoorbeeld ziet u hoe u het maken van een nieuwe versie activeert met de Azure Storage-clientbibliotheek voor .NET, versie [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) of hoger. Voordat u dit voorbeeld gaat uitvoeren, moet u ervoor zorgen dat u versieversies hebt ingeschakeld voor uw opslagaccount.

In het voorbeeld wordt een blok-blob gemaakt en worden vervolgens de metagegevens van de blob bijgewerkt. Het bijwerken van de metagegevens van de blob activeert het maken van een nieuwe versie. In het voorbeeld worden de eerste versie en de huidige versie opgehaald en wordt weergegeven dat alleen de huidige versie de metagegevens bevat.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>Blobversies in een lijst zetten

Als u blobversies of momentopnamen wilt vermelden met de .NET v12-clientbibliotheek, geeft u de parameter [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) op met het **veld** Versie.

In het volgende codevoorbeeld ziet u hoe u de blobversie vermeldt met de Azure Storage-clientbibliotheek voor .NET, versie [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) of hoger. Voordat u dit voorbeeld gaat uitvoeren, moet u ervoor zorgen dat u versieversies hebt ingeschakeld voor uw opslagaccount.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Volgende stappen

- [Blobversies](versioning-overview.md)
- [Voorlopig verwijderen voor Azure Storage-blobs](./soft-delete-blob-overview.md)