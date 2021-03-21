---
title: 'Power shell-script: synchronisatie-instellingen voor Azure data share instellen en weer geven'
description: Met dit Power shell-script worden synchronisatie-instellingen voor delen ingesteld en opgehaald.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 401754436739e5594c0d7baac6507f9d9984fc31
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221278"
---
# <a name="use-powershell-to-set-azure-data-share-synchronization-settings"></a>Power shell gebruiken om synchronisatie-instellingen voor Azure data share in te stellen

Met dit Power shell-script worden synchronisatie-instellingen voor delen ingesteld en opgehaald.

## <a name="sample-script"></a>Voorbeeldscript


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$settingName = "<Synchronization setting name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new synchronization setting
New-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $settingName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List share synchronization settings
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName 

#Get a specific share synchronization setting
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName -Name $settingName  
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [New-AzDataShareSynchronizationSetting](/powershell/module/az.datashare/new-azdatasharesynchronizationsetting) | Maak een synchronisatie van shares. |
| [Get-AzDataShareSynchronizationSetting](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting) | Hiermee worden de synchronisatie-instellingen van een synchronisatie van shares opgehaald. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende voor beelden van Power shell-scripts voor Azure data share vindt u in de [Azure data share Power shell](../../samples-powershell.md)-voor beelden.
