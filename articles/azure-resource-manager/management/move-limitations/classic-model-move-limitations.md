---
title: Klassieke Azure-implementatie resources verplaatsen
description: Gebruik Azure Resource Manager om klassieke implementatie resources te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75485284"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Richt lijnen voor klassieke implementatie model resources verplaatsen

De stappen voor het verplaatsen van resources die via het klassieke model zijn geïmplementeerd, zijn afhankelijk van het feit of u de resources in een abonnement verplaatst of naar een nieuw abonnement.

## <a name="move-in-the-same-subscription"></a>In hetzelfde abonnement verplaatsen

Bij het verplaatsen van resources van een resource groep naar een andere resource groep binnen hetzelfde abonnement gelden de volgende beperkingen:

* Virtuele netwerken (klassiek) kunnen niet worden verplaatst.
* Virtuele machines (klassiek) moeten worden verplaatst met de Cloud service.
* Cloud service kan alleen worden verplaatst wanneer de verplaatsing alle virtuele machines bevat.
* Er kan slechts één Cloud service tegelijk worden verplaatst.
* Er kan slechts één opslag account (klassiek) tegelijk worden verplaatst.
* Het opslag account (klassiek) kan niet in dezelfde bewerking met een virtuele machine of een Cloud service worden verplaatst.

Als u klassieke resources wilt verplaatsen naar een nieuwe resource groep binnen hetzelfde abonnement, gebruikt u de [standaard verplaatsings bewerkingen](../move-resource-group-and-subscription.md) via de portal, Azure PowerShell, Azure CLI of rest API. U gebruikt dezelfde bewerkingen als voor het verplaatsen van Resource Manager-resources.

## <a name="move-across-subscriptions"></a>Scha kelen tussen abonnementen

Bij het verplaatsen van resources naar een nieuw abonnement gelden de volgende beperkingen:

* Alle klassieke resources in het abonnement moeten in dezelfde bewerking worden verplaatst.
* Het doel abonnement mag geen andere klassieke resources hebben.
* De verplaatsing kan alleen worden aangevraagd via een afzonderlijke REST API voor klassieke verplaatsingen. De standaard opdrachten van Resource Manager verplaatsen werken niet wanneer u klassieke resources naar een nieuw abonnement verplaatst.

Als u klassieke resources wilt verplaatsen naar een nieuw abonnement, gebruikt u de REST-bewerkingen die specifiek zijn voor klassieke resources. Voer de volgende stappen uit om REST te gebruiken:

1. Controleer of het bron abonnement kan deel nemen aan een cross-abonnement verplaatsen. Gebruik de volgende bewerking:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     Neem in de hoofd tekst van de aanvraag het volgende op:

   ```json
   {
    "role": "source"
   }
   ```

     Het antwoord op de validatie bewerking heeft de volgende indeling:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Controleer of het doel abonnement kan deel nemen aan een cross-abonnement verplaatsen. Gebruik de volgende bewerking:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     Neem in de hoofd tekst van de aanvraag het volgende op:

   ```json
   {
    "role": "target"
   }
   ```

     Het antwoord heeft dezelfde indeling als de validatie van het bron abonnement.
1. Als beide abonnementen de validatie door geven, verplaatst u alle klassieke resources van het ene abonnement naar een ander abonnement met de volgende bewerking:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    Neem in de hoofd tekst van de aanvraag het volgende op:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

De bewerking kan enkele minuten worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Neem contact op met de [ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)als u problemen ondervindt bij het verplaatsen van klassieke resources.

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../move-resource-group-and-subscription.md)voor opdrachten voor het verplaatsen van resources.
