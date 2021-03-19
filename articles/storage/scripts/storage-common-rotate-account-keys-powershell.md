---
title: Toegangssleutels voor opslagaccounts roteren met PowerShell
titleSuffix: Azure Storage
description: Maak een Azure Storage-account en haal een van de toegangssleutels van het account op en draai deze.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7d8585b5d05012ab2aff2580d41fecf6423b509c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89070425"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>Toegangssleutels voor opslagaccounts roteren met PowerShell

Met dit script wordt een Azure Storage-account gemaakt, worden de primaire toegangssleutels van het nieuwe opslagaccount weergegeven en wordt de sleutel vernieuwd (gedraaid).

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep, het opslagaccount en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om het opslagaccount te maken en een van de toegangssleutels op te halen en te draaien. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Hiermee haalt u alle locaties en de ondersteunde resourceproviders voor elke locatie op. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een Azure-resourcegroep. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Hiermee maakt u een opslagaccount. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Hiermee worden de toegangssleutels voor Azure opslagaccounts weergegeven. |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Hiermee genereert u een toegangssleutel voor een Azure Storage-account. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

Meer PowerShell-voorbeeldscripts voor Storage vindt u in de [PowerShell-voorbeelden voor Azure Blob Storage](../blobs/storage-samples-blobs-powershell.md).
