---
title: Prestatie bewaking van Azure Service Fabric
description: Meer informatie over prestatie meter items voor bewaking en diagnose van Azure Service Fabric-clusters.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75464697"
---
# <a name="performance-metrics"></a>Metrische gegevens voor prestaties

Metrische gegevens moeten worden verzameld om inzicht te krijgen in de prestaties van uw cluster en de toepassingen die erop worden uitgevoerd. Voor Service Fabric clusters kunt u het beste de volgende prestatie meter items verzamelen.

## <a name="nodes"></a>Knooppunten

Voor de computers in uw cluster kunt u de volgende prestatie meter items verzamelen om beter inzicht te krijgen in de belasting van elke machine en de juiste beslissingen voor cluster schaling te maken.

| Categorie teller | Tellernaam |
| --- | --- |
| Logische schijf | Beschik bare ruimte op logische schijf |
| Fysieke schijf (per schijf) | Gemiddelde wachtrij lengte voor lezen van schijf |
| Fysieke schijf (per schijf) | Gem. wachtrij lengte schrijf bewerkingen schijf |
| Fysieke schijf (per schijf) | Gemiddelde Lees tijd schijf |
| Fysieke schijf (per schijf) | Gemiddelde schrijf tijd schijf |
| Fysieke schijf (per schijf) | Lees bewerkingen per seconde |
| Fysieke schijf (per schijf) | Gelezen bytes per seconde |
| Fysieke schijf (per schijf) | Schrijf bewerkingen per seconde |
| Fysieke schijf (per schijf) | Geschreven bytes per seconde |
| Geheugen | Beschikbare megabytes |
| PagingFile | Percentage gebruik |
| Processor (totaal) | Percentage processortijd |
| Proces (per service) | Percentage processortijd |
| Proces (per service) | Proces-ID |
| Proces (per service) | Privé-bytes |
| Proces (per service) | Aantal threads |
| Proces (per service) | Virtuele bytes |
| Proces (per service) | Werkset |
| Proces (per service) | Werkset-privé |
| Netwerk interface (alle exemplaren) | Recd bytes |
| Netwerk interface (alle exemplaren) | Verzonden bytes |
| Netwerk interface (alle exemplaren) | Totaal aantal bytes |
| Netwerk interface (alle exemplaren) | Lengte van uitvoer wachtrij |
| Netwerk interface (alle exemplaren) | Genegeerde uitgaande pakketten |
| Netwerk interface (alle exemplaren) | Genegeerde ontvangen pakketten |
| Netwerk interface (alle exemplaren) | Uitgaande pakketten met fouten |
| Netwerk interface (alle exemplaren) | Ontvangen pakketten met fouten |

## <a name="net-applications-and-services"></a>.NET-toepassingen en-services

Verzamel de volgende prestatie meter items als u .NET-Services implementeert in uw cluster. 

| Categorie teller | Tellernaam |
| --- | --- |
| .NET CLR-geheugen (per service) | Proces-id |
| .NET CLR-geheugen (per service) | Aantal in totaal toegewezen bytes |
| .NET CLR-geheugen (per service) | Totaal aantal gereserveerde bytes |
| .NET CLR-geheugen (per service) | Aantal bytes in alle heaps |
| .NET CLR-geheugen (per service) | Grootte van Large Object heap |
| .NET CLR-geheugen (per service) | Aantal GC-ingangen |
| .NET CLR-geheugen (per service) | Aantal verzamelingen van generatie 0 |
| .NET CLR-geheugen (per service) | Aantal verzamelingen van generatie 1 |
| .NET CLR-geheugen (per service) | Aantal verzamelingen van generatie 2 |
| .NET CLR-geheugen (per service) | % Tijd in GC |

### <a name="service-fabrics-custom-performance-counters"></a>Aangepaste prestatie meter items van Service Fabric

Service Fabric genereert een aanzienlijke hoeveelheid aangepaste prestatie meter items. Als u de SDK hebt geïnstalleerd, kunt u de uitgebreide lijst op uw Windows-computer in uw toepassing prestatie meter weer geven (start > prestatie meter). 

In de toepassingen die u in uw cluster implementeert, kunt u, als u Reliable Actors gebruikt, items toevoegen uit `Service Fabric Actor` en `Service Fabric Actor Method` Categorieën (zie [service Fabric reliable actors Diagnostics](service-fabric-reliable-actors-diagnostics.md)).

Als u gebruikmaakt van Reliable Services of service voor externe communicatie, hebben we ook `Service Fabric Service` en `Service Fabric Service Method` tellers categorieën waaruit u items moet verzamelen, Zie [bewaking met Service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md) en [betrouw bare Services prestatie meter items](service-fabric-reliable-services-diagnostics.md#performance-counters). 

Als u gebruikmaakt van betrouw bare verzamelingen, raden we `Avg. Transaction ms/Commit` u aan de van de `Service Fabric Transactional Replicator` toe te voegen om de gemiddelde doorvoer latentie per trans actie te verzamelen.


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het genereren van gebeurtenissen op platform niveau](service-fabric-diagnostics-event-generation-infra.md) in service Fabric
* Metrische gegevens over prestaties verzamelen via [log Analytics-agent](service-fabric-diagnostics-oms-agent.md)
