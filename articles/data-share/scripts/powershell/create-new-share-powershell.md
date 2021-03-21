---
title: 'Power shell-script: een nieuwe Azure-gegevens share maken'
description: Met dit Power shell-script maakt u een nieuwe gegevens share binnen een bestaand gegevens share-account.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 36c1bdfef2afe4c34796a804784317a5e7fe12ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221346"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Power shell gebruiken voor het maken van een gegevens share in azure

Met dit Power shell-script maakt u een nieuwe gegevens share binnen een bestaand gegevens share-account.

## <a name="sample-script"></a>Voorbeeldscript

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [New-AzDataShare](/powershell/module/az.datashare/new-azdatashare) | Hiermee maakt u een gegevens share. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende voor beelden van Power shell-scripts voor Azure data share vindt u in de [Azure data share Power shell](../../samples-powershell.md)-voor beelden.
