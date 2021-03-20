---
title: 'Power shell-script: een BLOB-gegevensset toevoegen aan een Azure-gegevens share'
description: Met dit Power shell-script wordt een BLOB-gegevensset toegevoegd aan een bestaande share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 7f2a2dded0b9c817e0700f4f93782d3b5c11ac3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221380"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Power shell gebruiken voor het maken van een gegevens share in azure

Met dit Power shell-script wordt een BLOB-gegevensset toegevoegd aan een bestaande gegevens share.

## <a name="sample-script"></a>Voorbeeldscript

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$blobDatasetName = "<Dataset name>"
$blobContainer = "<Blob container name>"
$blobFilePath = "<Blob file path>"
$storageAccountResourceId = "<Storage account resource id>"

#Add a blob dataset to the datashare
New-AzDataShareDataSet -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $blobDataSetName -StorageAccountResourceId $storageAccountResourceId -FilePath $blobFilePath

```


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [New-AzDataShareDataSet](/powershell/module/az.datashare/new-azdatasharedataset) | Voegt een gegevensset toe aan een gegevens share. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende voor beelden van Power shell-scripts voor Azure data share vindt u in de [Azure data share Power shell](../../samples-powershell.md)-voor beelden.
