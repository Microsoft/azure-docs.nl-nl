---
title: Problemen met de capaciteits pool oplossen voor Azure NetApp Files | Microsoft Docs
description: Hierin worden mogelijke problemen beschreven die zich kunnen voordoen bij het beheren van capaciteits groepen en oplossingen voor de problemen.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 759759b67582b241d0bab1e043dd15e54a804faf
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251536"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Problemen met capaciteitspools oplossen

In dit artikel worden oplossingen beschreven voor problemen die u mogelijk hebt bij het beheren van capaciteits groepen, inclusief de bewerking voor het wijzigen van de groep. 

## <a name="issues-managing-a-capacity-pool"></a>Problemen met het beheren van een capaciteits pool 

|     Foutvoorwaarde    |     Oplossing    |
|-|-|
| Problemen met het maken van een capaciteits pool |  Zorg ervoor dat het aantal capaciteits groepen de limiet niet overschrijdt. Zie [resource limieten voor Azure NetApp files](azure-netapp-files-resource-limits.md).  Als de telling kleiner is dan de limiet en u nog steeds problemen ondervindt, kunt u een ondersteunings ticket indienen en de naam van de capaciteits groep opgeven. |
| Problemen met het verwijderen van een capaciteits pool  |  Zorg ervoor dat u alle Azure NetApp Files volumes en moment opnamen verwijdert in het abonnement waarin u de capaciteits groep probeert te verwijderen. <br> Als u alle volumes en moment opnamen al hebt verwijderd en u de capaciteits groep nog steeds niet kunt verwijderen, kunnen er mogelijk nog verwijzingen naar resources bestaan zonder dat ze in de portal worden weer gegeven. In dit geval moet u een ondersteunings ticket indienen en opgeven dat u de hierboven aanbevolen stappen hebt uitgevoerd. |
| Het maken of wijzigen van een volume mislukt met `Requested throughput not available` fout | Beschik bare door Voer voor een volume wordt bepaald door de grootte van de capaciteits groep en het service niveau. Als u onvoldoende door Voer hebt, moet u de pool grootte verg Roten of de bestaande volume doorvoer aanpassen. | 

## <a name="issues-when-changing-the-capacity-pool-of-a-volume"></a>Problemen bij het wijzigen van de capaciteits pool van een volume 

|     Foutvoorwaarde    |     Oplossing    |
|-|-|
| Het is niet toegestaan de capaciteits groep voor een volume te wijzigen. | U bent mogelijk nog niet gemachtigd om deze functie te gebruiken. <br> De functie voor het verplaatsen van een volume naar een andere capaciteits groep is momenteel beschikbaar als preview-versie. Als u deze functie voor de eerste keer gebruikt, moet u eerst de functie registreren en instellen `-FeatureName ANFTierChange` . Zie de registratie stappen in [het service niveau van een volume dynamisch wijzigen](dynamic-change-volume-service-level.md). |
| De grootte van de capaciteits groep is te klein voor de totale volume grootte. |  De fout is het gevolg van de doel capaciteits groep die niet de beschik bare capaciteit heeft voor het volume dat wordt verplaatst.  <br> Verg root de grootte van de doel groep of kies een andere groep die groter is.  Zie [het formaat van een capaciteits groep of een volume wijzigen](azure-netapp-files-resize-capacity-pools-or-volumes.md).   |
|  Het wijzigen van de groep kan niet worden voltooid omdat er al een volume `'{source pool name}'` met de naam in de doel groep bestaat `'{target pool name}'` | Deze fout treedt op omdat het volume met dezelfde naam al bestaat in de doel capaciteits groep.  Selecteer een andere capaciteits groep zonder een volume met dezelfde naam.   | 

## <a name="next-steps"></a>Volgende stappen  

* [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
* [Een handmatige QoS-capaciteitspool maken](manage-manual-qos-capacity-pool.md)
* [Het serviceniveau van een volume dynamisch wijzigen](dynamic-change-volume-service-level.md)
* [Het formaat van een capaciteitspool of volume wijzigen](azure-netapp-files-resize-capacity-pools-or-volumes.md)
