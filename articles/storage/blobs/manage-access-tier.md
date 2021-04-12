---
title: De toegangs laag van een BLOB in een Azure Storage account beheren
description: Meer informatie over het wijzigen van de laag van een BLOB in een GPv2-of Blob Storage-account
author: twooley
ms.author: twooley
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: cd46f5b5a8847150bd56c1daeaac470f9afd2d19
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278538"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>De toegangs laag van een BLOB in een Azure Storage account beheren

Elke BLOB heeft een standaardlaag voor toegang, hetzij hot, cool of Archive. Een BLOB neemt de standaardtoegangs tier van het Azure Storage-account op waar deze is gemaakt. Blob Storage-en GPv2-accounts bieden het kenmerk **toegangs** niveau op account niveau. Met dit kenmerk wordt de standaardaccess-laag opgegeven voor een blob die niet expliciet is ingesteld op object niveau. Voor objecten met de laag die op object niveau is ingesteld, is de laag van de account niet van toepassing. De archief laag kan alleen op object niveau worden toegepast. U kunt op elk gewenst moment scha kelen tussen toegangs lagen door de volgende stappen uit te voeren.

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>De laag van een BLOB in een GPv2-of Blob Storage-account wijzigen

De volgende scenario's gebruiken de Azure Portal of Power shell:

# <a name="portal"></a>[Portal](#tab/portal)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek in het Azure Portal **alle resources** en selecteer deze.

1. Selecteer uw opslagaccount.

1. Selecteer uw container en selecteer vervolgens uw blob.

1. Selecteer in de **BLOB**-eigenschappen **laag wijzigen**.

1. Selecteer de Access-laag **Hot**, **cool** of **Archive** . Als uw BLOB zich momenteel in het archief bevindt en u wilt opnieuw worden gehydrateerd naar een online-laag, kunt u ook een onhydrate prioriteit van **Standard** of **High** selecteren.

1. Selecteer onder **Opslaan** onder.

![BLOB-laag wijzigen in Azure Portal](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Het volgende Power shell-script kan worden gebruikt om de BLOB-laag te wijzigen. De `$rgName` variabele moet worden geïnitialiseerd met de naam van de resource groep. De `$accountName` variabele moet worden geïnitialiseerd met de naam van uw opslag account. De `$containerName` variabele moet worden geïnitialiseerd met de container naam. De `$blobName` variabele moet worden geïnitialiseerd met de naam van de blob.

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>Volgende stappen

- [De toegangs laag van het standaard account van een Azure Storage account beheren](../common/manage-account-default-access-tier.md)
- [Meer informatie over reactiveren BLOB-gegevens uit de laag archief](storage-blob-rehydration.md)
- [Prijzen voor de dynamische, statische en archieflaag controleren in Blob Storage- en GPv2-accounts per regio](https://azure.microsoft.com/pricing/details/storage/)
