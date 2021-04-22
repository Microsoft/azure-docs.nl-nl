---
title: Voorlopig verwijderen inschakelen voor blobs
titleSuffix: Azure Storage
description: Schakel het gebruik van de functie voor het verwijderen van blobs in om blobgegevens te beschermen tegen onbedoeld verwijderen of overschrijven.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4a8d1f872ca042429276b8f0e1112bc5837d8e38
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869271"
---
# <a name="enable-soft-delete-for-blobs"></a>Voorlopig verwijderen inschakelen voor blobs

Met de optie Voor het verwijderen van blobs worden een afzonderlijke blob en de versies, momentopnamen en metagegevens beschermd tegen onbedoeld verwijderen of overschrijven door de verwijderde gegevens in het systeem gedurende een opgegeven periode te onderhouden. Tijdens de retentieperiode kunt u de blob bij verwijdering herstellen naar de status. Nadat de retentieperiode is verlopen, wordt de blob permanent verwijderd. Zie Soft delete for blobs (Soft delete voor [blobs) voor meer informatie over het verwijderen van blobs.](soft-delete-blob-overview.md)

Blob soft delete maakt deel uit van een uitgebreide strategie voor gegevensbeveiliging voor blobgegevens. Zie Overzicht van gegevensbescherming voor meer informatie over de aanbevelingen van Microsoft [voor gegevensbeveiliging.](data-protection-overview.md)

## <a name="enable-blob-soft-delete"></a>Blob soft delete inschakelen

Blob soft delete is standaard uitgeschakeld voor een nieuw opslagaccount. U kunt de functie voor het verwijderen van gegevens op elk moment voor een opslagaccount in- of uitschakelen met behulp van Azure Portal, PowerShell of Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Volg deze stappen om de functie voor het verwijderen van blobs voor uw opslagaccount in Azure Portal te stellen:

1. Ga in [Azure Portal](https://portal.azure.com/) naar uw opslagaccount.
1. Zoek de **optie Gegevensbeveiliging** onder **Blob service**.
1. Selecteer in **de sectie** Herstel de optie Soft **Delete in- of in-/uit- voor blobs.**
1. Geef een bewaarperiode op tussen 1 en 365 dagen. Microsoft raadt een minimale bewaarperiode van zeven dagen aan.
1. Sla uw wijzigingen op.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Schermopname die laat zien hoe u het verwijderen van de functie voor Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u het verwijderen van blobs met PowerShell wilt inschakelen, roept u de opdracht [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) aan en geeft u de bewaarperiode in dagen op.

In het volgende voorbeeld wordt het verwijderen van blobs voor een zachte blob mogelijk en wordt de retentieperiode op zeven dagen. Vergeet niet om de tijdelijke aanduidingen tussen haakjes te vervangen door uw eigen waarden:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Als u de huidige instellingen voor het verwijderen van blobs wilt controleren, roept u de [opdracht Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) aan:

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Als u blob soft delete wilt inschakelen met Azure CLI, roept u de opdracht [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) aan, waarin u de bewaarperiode in dagen opgeeft.

In het volgende voorbeeld wordt het verwijderen van blobs voor een zachte blob mogelijk en wordt de retentieperiode op zeven dagen in stellen. Vergeet niet om de tijdelijke aanduidingswaarden tussen haakjes te vervangen door uw eigen waarden:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Als u de huidige instellingen voor blob soft delete wilt controleren, roept u [de opdracht az storage account blob-service-properties show](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_show) aan:

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Volgende stappen

- [Blobs voorlopig verwijderen](soft-delete-blob-overview.md)
- [Zacht verwijderde blobs beheren en herstellen](soft-delete-blob-manage.md)
