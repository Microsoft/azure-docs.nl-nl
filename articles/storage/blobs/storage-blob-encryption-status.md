---
title: De versleutelings status van een BLOB controleren-Azure Storage
description: Meer informatie over het gebruik van Azure Portal, Power shell of Azure CLI om te controleren of een bepaalde blob is versleuteld. Als een BLOB niet is versleuteld, leert u hoe u AzCopy kunt gebruiken om versleuteling af te dwingen door de BLOB te downloaden en opnieuw te uploaden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: ee54357250e3f31ef9db633d933d897fff362f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878558"
---
# <a name="check-the-encryption-status-of-a-blob"></a>De versleutelings status van een BLOB controleren

Elke blok-blob, een toevoeg-BLOB of een pagina-blob die na 20 oktober 2017 is geschreven naar Azure Storage, is versleuteld met Azure Storage versleuteling. De blobs die vóór deze datum zijn gemaakt, worden versleuteld met een achtergrond proces.

In dit artikel wordt beschreven hoe u kunt bepalen of een bepaalde blob is versleuteld.

## <a name="check-a-blobs-encryption-status"></a>De versleutelings status van een BLOB controleren

Gebruik de Azure Portal, Power shell of Azure CLI om te bepalen of een BLOB zonder code is versleuteld.

### <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om te controleren of een blob is versleuteld met behulp van de Azure Portal:

1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer **containers** om naar een lijst met containers in het account te navigeren.
1. Zoek de BLOB en geef het tabblad **overzicht** weer.
1. De **versleutelde** eigenschap van de server weer geven. Indien **waar**, zoals wordt weer gegeven in de volgende afbeelding, wordt de BLOB versleuteld. U ziet dat de eigenschappen van de BLOB ook de datum en tijd bevatten waarop de blob is gemaakt.

    ![Scherm afbeelding die laat zien hoe de versleutelde eigenschap van de server wordt gecontroleerd in Azure Portal](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u Power shell wilt gebruiken om te controleren of een blob is versleuteld, controleert u de eigenschap **IsServerEncrypted** van de blob. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Controleer de waarde van de eigenschap **gemaakt** om te bepalen wanneer de blob is gemaakt:

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Als u Azure CLI wilt gebruiken om te controleren of een blob is versleuteld, controleert u de eigenschap **IsServerEncrypted** van de blob. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Controleer de waarde van de eigenschap **gemaakt** om te bepalen wanneer de blob is gemaakt.

---

## <a name="force-encryption-of-a-blob"></a>Versleuteling van een BLOB afdwingen

Als een blob die is gemaakt vóór 20 oktober 2017 nog niet is versleuteld door het achtergrond proces, kunt u afdwingen dat versleuteling onmiddellijk plaatsvindt door de BLOB te downloaden en opnieuw te uploaden. Een eenvoudige manier om dit te doen is met AzCopy.

Als u een BLOB wilt downloaden naar uw lokale bestands systeem met AzCopy, gebruikt u de volgende syntaxis:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Als u de BLOB opnieuw wilt uploaden naar Azure Storage met AzCopy, gebruikt u de volgende syntaxis:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Zie voor meer informatie over het gebruik van AzCopy voor het kopiëren van BLOB-gegevens [gegevens overdragen met AzCopy en Blob Storage](../common/storage-use-azcopy-v10.md#transfer-data).

## <a name="next-steps"></a>Volgende stappen

[Azure Storage-versleuteling voor inactieve gegevens](../common/storage-service-encryption.md)
