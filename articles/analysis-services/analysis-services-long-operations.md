---
title: Aanbevolen procedures voor langlopende bewerkingen in Azure Analysis Services | Microsoft Docs
description: In dit artikel worden aanbevolen procedures beschreven voor langlopende bewerkingen.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: owend
ms.openlocfilehash: 4e069effae0cb7f834b2c3dac696d05304d841a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92014831"
---
# <a name="best-practices-for-long-running-operations"></a>Best practices voor langlopende bewerkingen

In Azure Analysis Services vertegenwoordigt een *knoop punt* een virtuele host-machine waarop een server bron wordt uitgevoerd. Sommige bewerkingen, zoals langlopende query's, vernieuwings bewerkingen en de synchronisatie van de query scale-out kunnen mislukken als een server bron naar een ander knoop punt wordt verplaatst. Veelvoorkomende fout berichten in dit scenario zijn:

- ' Er is een fout opgetreden tijdens het zoeken naar een langdurige XMLA-aanvraag. De aanvraag is mogelijk onderbroken door de service-upgrade of het opnieuw opstarten van de server.
- De taak met de ID <guid> voor het model <database> is geannuleerd vanwege een service fout (inactiviteit) met het bericht de vernieuwings aanvraag annuleren omdat deze is vastgelopen zonder dat er updates zijn. Dit is een intern service probleem. Verzend de taak opnieuw of voer een ticket in om hulp te krijgen als dit probleem zich blijft voordoen. "

Er zijn verschillende redenen waarom langlopende bewerkingen kunnen worden onderbroken. Bijvoorbeeld updates in azure, zoals: 
- Patches voor besturings systeem 
- Beveiligingsupdates
- Azure Analysis Services service-updates
- Service Fabric updates. Service Fabric is een platform onderdeel dat wordt gebruikt door een aantal micro soft-Cloud Services, waaronder Azure Analysis Services.

Naast de updates die in de service optreden, is er sprake van een natuurlijke verplaatsing van services op verschillende knoop punten vanwege de taak verdeling. Knooppunt bewegingen vormen een verwacht onderdeel van een Cloud service. Azure Analysis Services probeert de effecten van knooppunt bewegingen te minimaliseren, maar het is niet mogelijk om ze volledig te elimineren. 

Naast knooppunt bewegingen zijn er ook andere fouten die kunnen optreden. Bijvoorbeeld, een database systeem van de gegevens bron is mogelijk offline of de verbinding met het netwerk is verbroken. Als tijdens het vernieuwen een partitie 10.000.000 rijen bevat en er een fout optreedt in de 9 millionth-rij, is er geen manier om vernieuwen op het moment van de fout opnieuw te starten. De service moet opnieuw worden gestart vanaf het begin. 

## <a name="refresh-rest-api"></a>REST API vernieuwen

Service onderbrekingen kunnen lastig zijn voor langlopende bewerkingen zoals het vernieuwen van gegevens. Azure Analysis Services bevat een REST API om te helpen bij het verminderen van negatieve gevolgen van onderbrekingen van de service. Zie [asynchroon vernieuwen met de rest API](analysis-services-async-refresh.md)voor meer informatie.
 
Naast de REST API zijn er andere benaderingen die u kunt gebruiken om potentiële problemen tijdens langdurige vernieuwings bewerkingen te minimaliseren. Het belangrijkste doel is om te voor komen dat u de vernieuwings bewerking vanaf het begin opnieuw moet starten en in plaats daarvan vernieuwt in kleinere batches die in fasen kunnen worden vastgelegd. 
 
De REST API maakt een dergelijke herstart mogelijk, maar biedt geen volledige flexibiliteit voor het maken en verwijderen van partities. Als een scenario complexe gegevens beheer bewerkingen vereist, moet uw oplossing in de logica een vorm van batch verwerking bevatten. Door bijvoorbeeld trans acties te gebruiken voor het verwerken van gegevens in meerdere, kan bij afzonderlijke batches een fout niet opnieuw worden gestart vanaf het begin, maar in plaats daarvan vanuit een tussenliggend controle punt. 
 
## <a name="scale-out-query-replicas"></a>Scale-out query replica's

Client toepassings query's kunnen nog steeds inconsistente of tussenliggende resultaten retour neren als batches worden verwerkt, ongeacht of REST of aangepaste logica wordt gebruikt. Als er tijdens de verwerking consistente gegevens worden geretourneerd door client toepassings query's, en model gegevens zich in een tussenliggende status bevindt, kunt u [uitschalen](analysis-services-scale-out.md) gebruiken met alleen-lezen query replica's.

Met alleen-lezen query replica's, terwijl vernieuwingen worden uitgevoerd in batches, kunnen gebruikers van client toepassingen de oude moment opname van gegevens op de alleen-lezen replica's blijven doorzoeken. Zodra het vernieuwen is voltooid, kan een synch bewerking worden uitgevoerd om de alleen-lezen replica's up-to-date te brengen.


## <a name="next-steps"></a>Volgende stappen

[Asynchroon vernieuwen met de REST API](analysis-services-async-refresh.md)  
[Uitschalen van Azure Analysis Services](analysis-services-scale-out.md)  
[Hoge beschikbaarheid van Analysis Services](analysis-services-bcdr.md)  
[Richt lijnen voor opnieuw proberen voor Azure-Services](/azure/architecture/best-practices/retry-service-specific)