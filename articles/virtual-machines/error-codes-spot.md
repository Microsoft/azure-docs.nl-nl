---
title: Fout codes voor instanties van Azure Spot Virtual Machines en schaal sets
description: Meer informatie over de fout codes die mogelijk worden weer geven wanneer u Azure Spot Virtual Machines en instanties voor schaal sets gebruikt.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 9bea9978f1755e5a40b5fb3ff967eb7f32384d19
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557741"
---
# <a name="error-messages-for-azure-spot-virtual-machines-and-scale-sets"></a>Fout berichten voor Azure Spot Virtual Machines en schaal sets

Hier volgen enkele mogelijke fout codes die u kunt ontvangen wanneer u Azure Spot Virtual Machines en schaal sets gebruikt.


| Sleutel | Bericht | Beschrijving |
|-----|---------|-------------|
| SkuNotAvailable | De aangevraagde laag voor de resource \<resource\> is momenteel niet beschikbaar op de locatie \<location\> voor het abonnement \<subscriptionID\> . Probeer een andere laag of implementeer deze op een andere locatie. | Er is niet voldoende Azure spot-capaciteit voor virtuele machines op deze locatie om uw VM-of schaalset-exemplaar te maken. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  Verwijderings beleid kan alleen worden ingesteld op Azure Spot Virtual Machines. | Deze VM is geen virtuele machine met Azure-steun, dus u kunt het verwijderings beleid niet instellen. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  De virtuele machine van Azure spot wordt niet ondersteund in de Beschikbaarheidsset. | U moet een virtuele machine van Azure spot gebruiken of een VM in een beschikbaarheidsset gebruiken, maar u kunt niet beide kiezen. |
| AzureSpotFeatureNotEnabledForSubscription  |  Het abonnement is niet ingeschakeld met de Azure spot-functie van de virtuele machine. | Gebruik een abonnement dat ondersteuning biedt voor Azure Spot Virtual Machines. |
| VMPriorityCannotBeApplied  |  De opgegeven prioriteits waarde {0} kan niet worden toegepast op de virtuele machine {1} omdat er geen prioriteit is opgegeven tijdens het maken van de virtuele machine. | Geef de prioriteit op wanneer de virtuele machine wordt gemaakt. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Kan de bewerking niet uitvoeren {0} omdat de ingevoerde maximum prijs ' {1} USD ' lager is dan de huidige steun prijs ' {2} USD ' voor de grootte van de virtuele machine van Azure spot ' {3} '. | Selecteer een hoger maximum prijs. Zie de prijs informatie voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) of [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie.|
| MaxPriceValueInvalid  |  Ongeldige waarde voor de maximum prijs. De enige ondersteunde waarden voor de maximum prijs zijn-1 of een decimale waarde groter dan nul. De waarde voor de maximale prijs van-1 geeft aan dat de virtuele machine van Azure Spot niet tegen prijs redenen wordt verwijderd. | Voer een geldige maximum prijs in. Zie prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) of [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. |
| MaxPriceChangeNotAllowedForAllocatedVMs | De maximale prijs wijziging is niet toegestaan wanneer de virtuele machine {0} momenteel is toegewezen. Maak de toewijzing ongedaan en probeer het opnieuw. | Stop\Deallocate de virtuele machine, zodat u de maximum prijs kunt wijzigen. |
| MaxPriceChangeNotAllowed | De maximale prijs wijziging is niet toegestaan. | U kunt de maximale prijs voor deze virtuele machine niet wijzigen. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  De Azure spot-virtuele machine wordt niet ondersteund voor deze API-versie. | De API-versie moet 2019-03-01 zijn. |
| AzureSpotIsNotSupportedForThisVMSize  |  Azure Spot Virtual machine wordt niet ondersteund voor deze VM-grootte {0} . | Selecteer een andere VM-grootte. Zie [Azure Spot Virtual Machines](./spot-vms.md)voor meer informatie. |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  De maximale prijs wordt alleen ondersteund voor Azure Spot Virtual Machines. | Zie [Azure Spot Virtual Machines](./spot-vms.md)voor meer informatie. |
| MoveResourcesWithAzureSpotVMNotSupported  |  De aanvraag voor het verplaatsen van resources bevat een Azure spot-virtuele machine. Wordt niet ondersteund. Raadpleeg de fout Details voor de Id's van de virtuele machine. | U kunt Azure Spot Virtual Machines niet verplaatsen. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  De aanvraag resources verplaatsen bevat een Azure spot-schaalset voor virtuele machines. Wordt niet ondersteund. Raadpleeg de fout Details voor de virtuele-machine schaal sets-Id's. | U kunt geen Azure spot-instanties voor virtuele-machine schaal sets verplaatsen. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | De virtuele machine van Azure spot wordt niet ondersteund in de schaalset voor virtuele machines met de modus VM-indeling. | Stel de Orchestration-modus in op virtuele-machine schaal sets om Azure spot-exemplaren van virtuele machines te gebruiken. |


**Volgende stappen** Zie [spot virtual machines](./spot-vms.md)voor meer informatie.