---
title: Het formaat van de capaciteits groep of een volume voor Azure NetApp Files wijzigen | Microsoft Docs
description: Meer informatie over het wijzigen van de grootte van een capaciteits groep of een volume. Het wijzigen van de grootte van de capaciteits groep wijzigt de gekochte Azure NetApp Files capaciteit.
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
ms.topic: how-to
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 869f46207b940521ee0b66b5afa9c6e2718ab04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594475"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Het formaat van een capaciteitspool of volume wijzigen
U kunt de grootte van een capaciteits groep of een volume zo nodig wijzigen. 

## <a name="resize-the-capacity-pool"></a>Grootte van de capaciteits groep wijzigen 

U kunt de grootte van de capaciteits groep wijzigen in stappen van 1-TiB of verlaagt. De grootte van de capaciteits groep mag echter niet kleiner zijn dan 4 TiB. Het wijzigen van de grootte van de capaciteits groep wijzigt de gekochte Azure NetApp Files capaciteit.

1. Klik op de Blade NetApp-account beheren op de capaciteits groep waarvan u de grootte wilt wijzigen. 
2. Klik met de rechter muisknop op de naam van de capaciteits groep of klik op de... pictogram aan het einde van de rij van de capaciteits groep om het context menu weer te geven. 
3. Gebruik de opties in het context menu om het formaat van de capaciteits groep te wijzigen of te verwijderen.

## <a name="resize-a-volume"></a>Het formaat van een volume wijzigen

U kunt de grootte van een volume indien nodig wijzigen. Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool.

1. Klik op de Blade NetApp-account beheren op **volumes**. 
2. Klik met de rechter muisknop op de naam van het volume waarvan u het formaat wilt wijzigen of klik op de... pictogram aan het einde van de rij van het volume om het context menu weer te geven.
3. Gebruik de opties in het context menu om het volume te verg Roten of verkleinen of verwijderen.

## <a name="resize-a-cross-region-replication-destination-volume"></a>Het formaat van een replicatie doel volume voor meerdere regio's wijzigen 

In een [replicatie relatie tussen regio's](cross-region-replication-introduction.md) wordt een doel volume automatisch aangepast op basis van de grootte van het bron volume. U hoeft dus niet afzonderlijk het formaat van het doel volume te wijzigen. Dit gedrag bij het automatisch aanpassen van het formaat is van toepassing wanneer de volumes zich in een actieve replicatie relatie bevinden of wanneer replicatie peering is verbroken met de [hersynchronisatie-bewerking](cross-region-replication-manage-disaster-recovery.md#resync-replication). 

In de volgende tabel wordt het gedrag voor het wijzigen van het doel volume beschreven op basis van de status van de [Mirror](cross-region-replication-display-health-status.md):

|  Spiegel status  | Grootte van het gedrag voor het wijzigen van het doel volume |
|-|-|
| *Gespiegeld* | Wanneer het doel volume is geïnitialiseerd en gereed is voor het ontvangen van mirroring updates, wijzigt het formaat van het bron volume automatisch de grootte van de doel volumes. |
| *Gemaakte* | Wanneer u de grootte van het bron volume wijzigt en de status van de mirror is *verbroken*, wordt de grootte van het doel volume automatisch aangepast met de [bewerking opnieuw synchroniseren](cross-region-replication-manage-disaster-recovery.md#resync-replication).  |
| *Niet geïnitialiseerd* | Wanneer u de grootte van het bron volume wijzigt en de status van de mirror nog steeds niet- *geïnitialiseerd* is, moet u de grootte van het doel volume hand matig wijzigen. Daarom is het raadzaam te wachten tot de initialisatie is voltooid (dat wil zeggen, wanneer de status van de mirror *gespiegeld* wordt) om het formaat van het bron volume te wijzigen. | 

> [!IMPORTANT]
> Zorg ervoor dat u voldoende ruimte hebt in de capaciteits groepen voor zowel de bron-als de doel volumes van replicatie tussen regio's. Wanneer u de grootte van het bron volume wijzigt, wordt het doel volume automatisch gewijzigd. Maar als de capaciteits pool die het doel volume host niet voldoende ruimte heeft, mislukt het wijzigen van de grootte van zowel de bron-als de doel volumes.

## <a name="next-steps"></a>Volgende stappen

- [Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)
- [Een handmatige QoS-capaciteitspool maken](manage-manual-qos-capacity-pool.md)
- [Het serviceniveau van een volume dynamisch wijzigen](dynamic-change-volume-service-level.md) 