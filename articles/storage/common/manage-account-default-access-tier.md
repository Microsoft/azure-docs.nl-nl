---
title: De standaardlaag voor toegang tot een Azure Storage account beheren
description: Meer informatie over het wijzigen van de standaardlaag voor toegang tot een GPv2-of Blob Storage-account
author: twooley
ms.author: twooley
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 026ab6be1fd4ef79f818f796c4725f6613a9bc6d
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277382"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>De standaardlaag voor toegang tot een Azure Storage account beheren

Elk Azure Storage account heeft een standaardaccess-laag, hetzij Hot of cool. U wijst de Access-laag toe wanneer u een opslag account maakt. De standaardlaag voor toegang is hot.

U kunt de standaardlaag voor het account wijzigen door het kenmerk **toegangs niveau** in te stellen voor het opslag account. Het wijzigen van de servicelaag is van toepassing op alle objecten die zijn opgeslagen in het account waarvoor geen expliciete laag is ingesteld. Het in-of uitschakelen van de laag van dynamisch naar koeling-schrijf bewerkingen (per 10.000) voor alle blobs zonder een set-laag in GPv2-accounts en het omschakelen van koud naar warm keer de kosten voor zowel lees bewerkingen (per 10.000) als voor het ophalen van gegevens (per GB) voor alle blobs in Blob Storage-en GPv2-accounts.

Voor blobs met de laag die op object niveau is ingesteld, is de laag van de account niet van toepassing. De Archive-laag kan alleen worden toegepast op objectniveau. U kunt op elk gewenst moment scha kelen tussen toegangs lagen.

U kunt de standaardlaag voor toegang wijzigen nadat een opslag account is gemaakt door de volgende stappen uit te voeren.

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>De toegangs laag van het standaard account van een GPv2-of Blob Storage-account wijzigen

De volgende scenario's gebruiken de Azure Portal of Power shell:

# <a name="portal"></a>[Portal](#tab/portal)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek in het Azure Portal **alle resources** en selecteer deze.

1. Selecteer uw opslagaccount.

1. Selecteer in **instellingen** de optie **configuratie** om de account configuratie te bekijken en te wijzigen.

1. Selecteer de juiste toegangs laag voor uw behoeften: Stel de **toegangs laag** in op **koud** of **dynamisch**.

1. Klik bovenaan op **Opslaan** .

![De standaardlaag van de account wijzigen in Azure Portal](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Het volgende Power shell-script kan worden gebruikt om de account tier te wijzigen. De `$rgName` variabele moet worden geïnitialiseerd met de naam van de resource groep. De `$accountName` variabele moet worden geïnitialiseerd met de naam van uw opslag account.

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>Volgende stappen

- [De laag van een BLOB in een Azure Storage account beheren](../blobs/manage-access-tier.md)
- [Bepalen of Premium-prestaties uw app zouden voor u hebben](../blobs/storage-blob-performance-tiers.md)
- [Gebruik van de huidige opslagaccounts evalueren door metrische gegevens voor Azure Storage in te schakelen](../blobs/monitor-blob-storage.md)
