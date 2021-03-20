---
title: Beschik baarheid van Service Fabric Services
description: Hierin wordt een beschrijving van de fout detectie, failover en herstel van een service in een Azure Service Fabric-toepassing beschreven.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018899"
---
# <a name="availability-of-service-fabric-services"></a>Beschik baarheid van Service Fabric Services
Dit artikel geeft een overzicht van de manier waarop Azure Service Fabric de beschik baarheid van een service beheert.

## <a name="availability-of-service-fabric-stateless-services"></a>Beschik baarheid van Service Fabric stateless Services
Service Fabric Services kunnen stateful of stateless zijn. Een stateless service is een toepassings service zonder [lokale status](service-fabric-concepts-state.md) die Maxi maal beschikbaar of betrouwbaar moet zijn.

Als u een stateless service wilt maken, moet u een opgeven `InstanceCount` . Met het aantal instanties wordt het aantal exemplaren gedefinieerd van de toepassings logica van de stateless service die in het cluster moet worden uitgevoerd. Het verhogen van het aantal exemplaren is de aanbevolen manier om een stateless service te schalen.

Wanneer een exemplaar van een stateless benoemde service mislukt, wordt er een nieuw exemplaar gemaakt op een in aanmerking komend knoop punt in het cluster. Een stateless service-exemplaar kan bijvoorbeeld mislukken op Knooppunt1 en opnieuw worden gemaakt op Knooppunt5.

## <a name="availability-of-service-fabric-stateful-services"></a>Beschik baarheid van Service Fabric stateful Services
Aan een stateful service is een status gekoppeld. In Service Fabric wordt een stateful service als een reeks replica's gemodelleerd. Elke replica is een actief exemplaar van de code van de service. De replica heeft ook een kopie van de status voor die service. Lees-en schrijf bewerkingen worden uitgevoerd op één replica met de naam *Primary*. Wijzigingen in de status van schrijf bewerkingen worden *gerepliceerd* naar de andere replica's in de replicaset, *actieve secundaire* zones genoemd en toegepast. 

Er kan slechts één primaire replica zijn, maar er kunnen meerdere actieve secundaire replica's zijn. Het aantal actieve secundaire replica's kan worden geconfigureerd en een hoger aantal replica's kan een groter aantal gelijktijdige software-en hardwarestoringen verdragen.

Als de primaire replica uitvalt, Service Fabric maakt een van de actieve secundaire replica's de nieuwe primaire replica. Deze actieve secundaire replica heeft al de bijgewerkte versie van de status, via *replicatie*, en kan verdere Lees-en schrijf bewerkingen blijven verwerken. Dit proces wordt *herconfiguratie* genoemd en wordt verder beschreven in het artikel [herconfiguratie](service-fabric-concepts-reconfiguration.md) .

Het concept van een replica is een primair of actief secundair, wordt de *replica-rol* genoemd. Deze replica's worden verder beschreven in het artikel [replica's en exemplaren](service-fabric-concepts-replica-lifecycle.md) . 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Service Fabric concepten:

- [Service Fabric Services schalen](service-fabric-concepts-scalability.md)
- [Service Fabric Services partitioneren](service-fabric-concepts-partitioning.md)
- [Status definiëren en beheren](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)

