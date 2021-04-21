---
title: Bepalen welk versleutelingssleutelmodel wordt gebruikt voor het opslagaccount
titleSuffix: Azure Storage
description: Gebruik Azure Portal, PowerShell of Azure CLI om te controleren hoe versleutelingssleutels worden beheerd voor het opslagaccount. Sleutels kunnen worden beheerd door Microsoft (de standaardinstelling) of door de klant. Door de klant beheerde sleutels moeten worden opgeslagen in Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 08bc36500bbd95633d1cb1d02bf10a7397401aa4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780115"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Bepalen welk Azure Storage versleutelingssleutelmodel wordt gebruikt voor het opslagaccount

Gegevens in uw opslagaccount worden automatisch versleuteld door Azure Storage. Azure Storage-versleuteling biedt twee opties voor het beheren van versleutelingssleutels op het niveau van het opslagaccount:

- **Door Microsoft beheerde sleutels.** Microsoft beheert standaard de sleutels die worden gebruikt om uw opslagaccount te versleutelen.
- **Door de klant beheerde sleutels.** U kunt er eventueel voor kiezen om versleutelingssleutels voor uw opslagaccount te beheren. Door de klant beheerde sleutels moeten worden opgeslagen in Azure Key Vault.

Daarnaast kunt u een versleutelingssleutel bieden op het niveau van een afzonderlijke aanvraag voor sommige Blob Storage-bewerkingen. Wanneer een versleutelingssleutel wordt opgegeven in de aanvraag, overschreven deze sleutel de versleutelingssleutel die actief is in het opslagaccount. Zie Specify [a customer-provided key on a request to Blob storage (Een door](../blobs/storage-blob-customer-provided-key.md)de klant verstrekte sleutel opgeven bij een aanvraag voor Blob Storage) voor meer informatie.

Zie Voor meer informatie over versleutelingssleutels [Azure Storage versleuteling voor data-at-rest.](storage-service-encryption.md)

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Controleer het versleutelingssleutelmodel voor het opslagaccount

Gebruik een van de volgende methoden om te bepalen of een opslagaccount door Microsoft beheerde sleutels of door de klant beheerde sleutels gebruikt voor versleuteling.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Als u het versleutelingsmodel voor het opslagaccount wilt controleren met behulp van Azure Portal, volgt u deze stappen:

1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer de **instelling Versleuteling** en noteer de instelling.

In de volgende afbeelding ziet u een opslagaccount dat is versleuteld met door Microsoft beheerde sleutels:

![Account weergeven dat is versleuteld met door Microsoft beheerde sleutels](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

En in de volgende afbeelding ziet u een opslagaccount dat is versleuteld met door de klant beheerde sleutels:

![Schermopname van de instelling van de versleutelingssleutel in Azure Portal](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u het versleutelingsmodel voor het opslagaccount wilt controleren met behulp van PowerShell, roept u de opdracht [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) aan en controleert u vervolgens de **eigenschap KeySource** voor het account.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Als de waarde van de **eigenschap KeySource** `Microsoft.Storage` is, wordt het account versleuteld met door Microsoft beheerde sleutels. Als de waarde van de **eigenschap KeySource** `Microsoft.Keyvault` is, wordt het account versleuteld met door de klant beheerde sleutels.

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u het versleutelingsmodel voor het opslagaccount wilt controleren met behulp van Azure CLI, roept u de opdracht [az storage account show](/cli/azure/storage/account#az_storage_account_show) aan en controleert u vervolgens de eigenschap **keySource** voor het account.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Als de waarde van de **eigenschap keySource** `Microsoft.Storage` is, wordt het account versleuteld met door Microsoft beheerde sleutels. Als de waarde van de **eigenschap keySource** is, wordt het account versleuteld `Microsoft.Keyvault` met door de klant beheerde sleutels.

---

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor inactieve gegevens](storage-service-encryption.md)
- [Door de klant beheerde sleutels voor Azure Storage versleuteling](customer-managed-keys-overview.md)
