---
title: Status in azure Service Fabric Services beheren
description: Meer informatie over de status in azure Service Fabric, waaronder het definiëren en beheren van de service status in Service Fabric Services.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75614567"
---
# <a name="service-state"></a>Servicestatus
**Service status** verwijst naar de in-Memory of op schijf gegevens die een service nodig heeft om te functioneren. Het bevat bijvoorbeeld de variabelen voor gegevens structuren en leden die door de service worden gelezen en geschreven om te werken. Afhankelijk van hoe de service is ontworpen, kan deze ook bestanden of andere bronnen die op schijf zijn opgeslagen, bevatten. De bestanden die door een Data Base zouden worden gebruikt voor het opslaan van gegevens en transactie Logboeken.

Als voorbeeld service kunt u een Calculator beschouwen. Een Basic Calculator-service gebruikt twee getallen en retourneert de som ervan. Het uitvoeren van deze berekening bestaat uit geen lidvariabelen of andere informatie.

Denk nu aan dezelfde reken machine, maar met een extra methode voor het opslaan en retour neren van de laatste som die is berekend. Deze service is nu stateful. Stateful houdt in dat er een status wordt vermeld waarnaar wordt geschreven wanneer er een nieuwe som wordt berekend en gelezen wanneer u vraagt om de laatst berekende som te retour neren.

In azure Service Fabric wordt de eerste service een stateless service genoemd. De tweede service wordt een stateful service genoemd.

## <a name="storing-service-state"></a>Service status opslaan
De status kan extern of gezamenlijk worden gevonden met de code die de status bewerkt. De Externalization van de status wordt doorgaans uitgevoerd met behulp van een externe data base of een ander gegevens archief dat wordt uitgevoerd op verschillende computers via het netwerk of out-of-process op dezelfde computer. In het voor beeld van de reken machine kan het gegevens archief een SQL database of een exemplaar van het Azure-tabel archief zijn. Elke aanvraag voor het berekenen van de som voert een update op deze gegevens uit en vraagt naar de service om de waarde te retour neren in de huidige waarde die wordt opgehaald uit de Store. 

De status kan ook naast elkaar worden gevonden met de code die de status bewerkt. Stateful Services in Service Fabric zijn doorgaans gebouwd met behulp van dit model. Service Fabric biedt de infra structuur om ervoor te zorgen dat deze status Maxi maal beschikbaar, consistent en duurzaam is en dat de services die op deze manier zijn gebouwd, eenvoudig kunnen worden geschaald.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Service Fabric concepten:

* [Beschik baarheid van Service Fabric Services](service-fabric-availability-services.md)
* [Schaal baarheid van Service Fabric Services](service-fabric-concepts-scalability.md)
* [Service Fabric Services partitioneren](service-fabric-concepts-partitioning.md)
* [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)
