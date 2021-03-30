---
title: 'Power shell-script: lijst met uitnodigingen voor Azure-gegevens delen die worden verzonden naar een consument'
description: Meer informatie over hoe dit Power shell-script uitnodigingen voor een consument ontvangt en een voor beeld van het script dat u kunt gebruiken.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 5f75894094fa2a15dbc9e2809ed6c3631df96c3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221210"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Power shell gebruiken voor het ophalen van een uitnodiging voor een gegevens share

Met dit Power shell-script worden uitnodigingen voor een consument verzonden.

## <a name="sample-script"></a>Voorbeeldscript
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | Verzonden uitnodigingen voor gegevens delen ophalen en weer geven. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende voor beelden van Power shell-scripts voor Azure data share vindt u in de [Azure data share Power shell](../../samples-powershell.md)-voor beelden.
