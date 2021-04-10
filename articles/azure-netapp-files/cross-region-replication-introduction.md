---
title: Replicatie tussen regio's van Azure NetApp Files volumes | Microsoft Docs
description: Beschrijft wat Azure NetApp Files replicatie tussen regio's ondersteunt, ondersteunde regio paren, serviceniveau doelstellingen, gegevens duurzaamheid en kosten model.
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
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: b-juche
ms.custom: references_regions
ms.openlocfilehash: ac0f9e6e5e1a1988386cc85c2d7576719acbd6e6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104590956"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Replicatie tussen regio's van Azure NetApp Files volumes

De functionaliteit van Azure NetApp Files replicatie biedt gegevens beveiliging via volume replicatie in meerdere regio's. U kunt asynchroon gegevens repliceren vanaf een Azure NetApp Files volume (bron) in een regio naar een andere Azure NetApp Files volume (bestemming) in een andere regio.  Met deze mogelijkheid kunt u een failover van uw kritieke toepassing in het geval van een regionale storing of ramp.

> [!IMPORTANT]
> De functie voor replicatie van meerdere regio's is momenteel beschikbaar als open bare preview. U moet een Waitlist-aanvraag indienen om toegang te krijgen tot de functie via de [Waitlist-verzend pagina van Azure NetApp files cross-Region replicatie](https://aka.ms/anfcrrpreviewsignup). Wacht op een officiële bevestigings-e-mail van het Azure NetApp Files team voordat u de functie voor replicatie tussen regio's gebruikt.

## <a name="supported-cross-region-replication-pairs"></a><a name="supported-region-pairs"></a>Ondersteunde replicatie paren tussen regio's

Azure NetApp Files volume replicatie wordt ondersteund tussen verschillende [Azure-regionale paren](../best-practices-availability-paired-regions.md#azure-regional-pairs) en niet-paren. Azure NetApp Files volume replicatie momenteel beschikbaar is tussen de volgende regio's:  

### <a name="azure-regional-pairs"></a>Azure-regionale paren

* VS-Oost, VS-West
* VS-Oost 2 en VS-Centraal
* Australië-oost en Australië-zuidoost
* Canada-centraal en Canada-oost
* India-zuid en Centraal-India 
* Duitsland-west-centraal en Duitsland-noord
* Japan-Oost en Japan-West
* Europa-noord en Europa-west
* UK-zuid en UK-west

### <a name="azure-regional-non-pairs"></a>Azure regionale niet-paren

*   VS-West 2 en VS-Oost
*   Zuid-Centraal VS en VS-Centraal
*   Zuid-Centraal VS en VS-Oost
*   VS Zuid-Centraal en VS-Oost 2
*   VS-Oost en VS-Oost 2
*   VS-Oost 2 en VS-West 2
*   Australië-oost en Zuidoost-Azië 
*   Duitsland-west-centraal en UK-zuid

## <a name="service-level-objectives"></a>Serviceniveau doelstellingen

Herstel punt doelstellingen (RPO) of het Maxi maal toegestane aantal gegevens verlies is gedefinieerd als twee maal het replicatie schema.  De werkelijke vrijgegeven RPO kunnen variëren afhankelijk van factoren zoals de totale grootte van de gegevensset, samen met het wijzigings percentage, het percentage gegevens dat wordt overschreven en de replicatie bandbreedte die beschikbaar is voor overdracht.   

* Voor het replicatie schema van 10 minuten is de maximale RPO 20 minuten.  
* Voor het replicatie schema per uur is de maximale RPO twee uur.  
* Voor het dagelijkse replicatie schema is de maximale RPO twee dagen.  

De beoogde herstel tijd (RTO) of de Maxi maal toegestane downtime van bedrijfs toepassingen wordt bepaald door factoren in het opstellen van de toepassing en het verlenen van toegang tot de gegevens op de tweede site. Het opslag gedeelte van de RTO voor het opsplitsen van de peering-relatie voor het activeren van het doel volume en het leveren van lees-en schrijf toegang op de tweede site wordt naar verwachting binnen een minuut voltooid.

## <a name="cost-model-for-cross-region-replication"></a>Kosten model voor replicatie tussen regio's  

Met Azure NetApp Files replicatie tussen regio's betaalt u alleen voor de hoeveelheid gegevens die u repliceert. Er zijn geen instellings kosten of minimale gebruiks kosten. De replicatie prijs is gebaseerd op de replicatie frequentie en de regio van het *doel* volume dat u tijdens de configuratie van de eerste replicatie kiest. Zie de pagina met [prijzen voor Azure NetApp files](https://azure.microsoft.com/pricing/details/netapp/) voor meer informatie.  

Normale Azure NetApp Files opslag capaciteit geldt voor het replicatie doel volume (ook wel het *gegevens beveiligings* volume genoemd). 

### <a name="pricing-examples"></a>Prijsvoorbeelden

De replicatie hoeveelheid tussen regio's die in een maand wordt gefactureerd, is gebaseerd op de hoeveelheid gegevens die wordt gerepliceerd via de functie voor replicatie van meerdere regio's tijdens die maand. De hoeveelheid gegevens die wordt gerepliceerd, wordt gemeten in GiB. Het vertegenwoordigt de som van de gegevens die worden gerepliceerd tussen twee regio's tijdens alle reguliere replicaties van de bron volumes naar de doel volumes en tijdens alle replicatie van de doel volumes naar de bron volumes.

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>Voor beeld 1: maand 1 basis lijn replicatie en incrementele replicaties

Stel dat de volgende situaties zich voordoen:

* Het *bron* volume is van het Azure NetApp files *Premium* -service niveau. Het heeft een volume quotum grootte van 1000 GiB en een volume grootte van 500 GiB aan het begin van de eerste dag van een maand. Het volume bevindt zich in de regio *VS Zuid-Centraal* .
* Het *doel* volume is van het Azure NetApp files *Standard* -service niveau. Het bevindt zich in de regio *VS Oost 2* .
* U hebt een op *elk uur* gebaseerde replicatie tussen verschillende regio's geconfigureerd tussen de bovenstaande volumes. Daarom is de prijs van replicatie $0,12 per GiB.
* Voor het gemak wordt ervan uitgegaan dat uw bron volume elk uur een constante 0,5-GiB gegevens wijziging heeft, maar de totale grootte van het volume dat is verbruikt, niet groter is (blijft 500 GiB). 

Na de initiële installatie vindt de basislijn replicatie onmiddellijk plaats.  

* Gegevens aantal gerepliceerd tijdens basislijn replicatie: `500 GiB`
* Kosten basis lijn replicatie: `500 GiB * $0.12 = $60`

Na de basislijn replicatie worden alleen gewijzigde blokken gerepliceerd. Daarom worden slechts 0,5 GiB van gegevens elke uur gerepliceerd in de volgende incrementele replicaties.

* De som van de gegevens die zijn gerepliceerd over incrementele replicaties voor een maand van 30 dagen: `0.5 GiB * 24 hours * 30 days = 360 GiB`
* Incrementele replicatie kosten: `360 GiB * $0.12 = $43.2`

Aan het einde van maand 1 zijn de totale kosten voor replicatie voor meerdere regio's als volgt:  

*  Totale kosten voor replicatie van meerdere regio's van maand 1: `$60 + $43.2 = $103.2`

De kosten voor normale Azure NetApp Files opslag capaciteit zijn van toepassing op het doel volume. Het doel volume kan echter een opslaglaag gebruiken die afwijkt van (en goed koper is dan) de laag van het bron volume.

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>Voor beeld 2: maanden 2 incrementele replicaties en Resync-replicaties  

Stel dat u een bron volume, een doel volume en een replicatie relatie hebt tussen de twee instellingen zoals beschreven in voor beeld 1. Gedurende 29 dagen van de tweede maand (een maand van 30 dagen) zijn de replicaties van elk uur op de verwachte wijze uitgevoerd.

* De som van de gegevens hoeveelheid die wordt gerepliceerd over incrementele replicaties gedurende 29 dagen: `0.5 GiB * 24 hours * 29 days = 348 GiB`

Stel dat op de laatste dag van de maand een ongeplande onderbreking is opgetreden in de bron regio en dat u een failover hebt uitgevoerd naar het doel volume. Na 2 uur is de bron regio hersteld en hebt u een hersynchronisaties replicatie uitgevoerd vanaf het doel volume naar het bron volume. Gedurende de 2 uur zijn er 0,8 gegevens gewijzigd op het doel volume en moeten ze opnieuw worden gesynchroniseerd met de bron.

* De som van de gegevens hoeveelheid die gedurende 22 uur op de laatste dag wordt gerepliceerd tussen reguliere replicaties: `0.5 GiB * 22 hours = 11 GiB`
* Gegevens hoeveelheid gerepliceerd tijdens één synchronisatie replicatie: `0.8 GiB`

Daarom is aan het einde van maand 2 de totale kosten voor replicatie voor meerdere regio's als volgt:  

* Totale kosten voor replicatie van meerdere regio's van maand 2: `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

De kosten voor de opslag capaciteit van de vaste Azure NetApp Files voor maand 2 zijn van toepassing op het doel volume.

## <a name="next-steps"></a>Volgende stappen
* [Vereisten en overwegingen voor het gebruik van replicatie tussen regio's](cross-region-replication-requirements-considerations.md)
* [Volumereplicatie maken](cross-region-replication-create-peering.md)
* [Status van replicatierelatie weergeven](cross-region-replication-display-health-status.md)
* [Herstel na noodgevallen beheren](cross-region-replication-manage-disaster-recovery.md)
* [Het formaat van een replicatie doel volume voor meerdere regio's wijzigen](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-cross-region-replication-destination-volume)
* [Metrische gegevens van de volume replicatie](azure-netapp-files-metrics.md#replication)
* [Volumereplicaties of volumes verwijderen](cross-region-replication-delete.md)
* [Problemen met replicatie tussen regio's oplossen](troubleshoot-cross-region-replication.md)