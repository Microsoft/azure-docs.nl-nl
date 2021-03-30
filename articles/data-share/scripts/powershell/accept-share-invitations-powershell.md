---
title: 'Power shell-script: uitnodiging accepteren van een Azure-gegevens share'
description: Dit Power shell-script accepteert uitnodigingen van een bestaande gegevens share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 24091bafac7ad5c558b975d52064f12715b3d622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221397"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Power shell gebruiken voor het accepteren van een uitnodiging voor een gegevens share

Dit Power shell-script accepteert de uitnodigingen die worden verzonden naar een consumer.

## <a name="sample-script"></a>Voorbeeldscript
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | Verzonden uitnodigingen voor gegevens delen ophalen en weer geven. |
| [New-AzDataShareSubscription](/powershell/module/az.datashare/get-azdatasharesubscription) | Een gegevens share-abonnement maken. |
|||

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende voor beelden van Power shell-scripts voor Azure data share vindt u in de [Azure data share Power shell](../../samples-powershell.md)-voor beelden.
