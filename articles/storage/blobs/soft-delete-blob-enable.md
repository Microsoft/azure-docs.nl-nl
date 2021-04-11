---
title: Voorlopig verwijderen inschakelen voor blobs
titleSuffix: Azure Storage
description: Schakel zacht verwijderen voor blobs in om blobgegevens te beschermen tegen onbedoeld verwijderen of overschrijvingen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 11323f2aec05935b9dc45187ed54597e61af924d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554111"
---
# <a name="enable-soft-delete-for-blobs"></a>Voorlopig verwijderen inschakelen voor blobs

Met de methode voor het tijdelijk verwijderen van blobs worden een afzonderlijke Blob en de bijbehorende versies, moment opnamen en meta gegevens beschermd tegen onbedoeld verwijderen of worden overschreven door de verwijderde gegevens in het systeem gedurende een bepaalde periode te bewaren. Tijdens de retentie periode kunt u de BLOB tijdens het verwijderen herstellen naar de status. Nadat de Bewaar periode is verlopen, wordt de BLOB permanent verwijderd. Zie [voorlopig verwijderen voor blobs](soft-delete-blob-overview.md)voor meer informatie over het voorlopig verwijderen van blobs.

Het dynamisch verwijderen van blobs maakt deel uit van een uitgebreide strategie voor gegevens beveiliging voor BLOB-gegevens. Zie [overzicht van gegevens beveiliging](data-protection-overview.md)voor meer informatie over de aanbevelingen van micro soft voor gegevens bescherming.

## <a name="enable-blob-soft-delete"></a>Voorlopig verwijderen van BLOB inschakelen

Het dynamisch verwijderen van een blob is standaard uitgeschakeld voor een nieuw opslag account. U kunt voorlopig verwijderen voor een opslag account op elk gewenst moment in-of uitschakelen met behulp van de Azure Portal, Power shell of Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om het uitvoeren van een BLOB zacht verwijderen voor uw opslag account in te scha kelen met behulp van de Azure Portal:

1. Ga in [Azure Portal](https://portal.azure.com/) naar uw opslagaccount.
1. Zoek de optie **gegevens bescherming** onder **BLOB service**.
1. Selecteer in de sectie **herstel** de optie **voorlopig verwijderen inschakelen voor blobs**.
1. Geef een Bewaar periode op tussen 1 en 365 dagen. Micro soft raadt een minimale Bewaar periode van zeven dagen aan.
1. Sla uw wijzigingen op.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Scherm afbeelding die laat zien hoe u zacht verwijderen in de Azure Portal inschakelt":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de optie voor het voorlopig verwijderen van BLOB wilt inschakelen met Power shell, roept u de opdracht [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) aan, waarbij u de Bewaar periode in dagen opgeeft.

In het volgende voor beeld wordt de optie voor het voorlopig verwijderen van BLOB ingeschakeld en wordt de Bewaar periode ingesteld op zeven dagen. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Als u de huidige instellingen voor het voorlopig verwijderen van blobs wilt controleren, roept u de opdracht [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) :

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Als u de optie voor het voorlopig verwijderen van blobs wilt inschakelen met Azure CLI, roept u de opdracht [AZ Storage account BLOB-Service-Properties update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) aan, waarbij u de Bewaar periode in dagen opgeeft.

In het volgende voor beeld wordt de optie voor het voorlopig verwijderen van BLOB ingeschakeld en wordt de Bewaar periode ingesteld op zeven dagen. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Als u de huidige instellingen voor het voorlopig verwijderen van blobs wilt controleren, roept u de opdracht [AZ Storage account BLOB-Service-Properties show](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_show) aan:

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Volgende stappen

- [Blobs voorlopig verwijderen](soft-delete-blob-overview.md)
- [Tijdelijke verwijderde blobs beheren en herstellen](soft-delete-blob-manage.md)
