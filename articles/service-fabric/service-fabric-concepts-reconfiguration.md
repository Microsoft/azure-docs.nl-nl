---
title: Opnieuw configureren in azure Service Fabric
description: Meer informatie over configuraties voor stateful service replica's en het proces van herconfiguratie Service Fabric gebruikt om consistentie en beschik baarheid te behouden tijdens de wijziging.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: bd46a7776495624affef77a44fcf68334750ba17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75609992"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Opnieuw configureren in azure Service Fabric
Een *configuratie* wordt gedefinieerd als de replica's en hun rollen voor een partitie van een stateful service.

Een nieuwe *configuratie* is het proces van het verplaatsen van een configuratie naar een andere configuratie. Er wordt een wijziging aangebracht in de replicaset voor een partitie van een stateful service. De oude configuratie wordt de *vorige configuratie (PC)* genoemd en de nieuwe configuratie wordt de *huidige configuratie (CC)* genoemd. Het herconfiguratie-protocol in azure Service Fabric behoudt de consistentie en houdt de beschik baarheid bij van alle wijzigingen in de replicaset.

Failover Manager initieert herconfiguraties in reactie op verschillende gebeurtenissen in het systeem. Als de primaire fout bijvoorbeeld mislukt, wordt een herconfiguratie geïnitieerd om een actief secundair te promo veren naar een primaire. Een ander voor beeld is in reactie op toepassings upgrades wanneer dit nodig kan zijn om de primaire naar een ander knoop punt te verplaatsen om het knoop punt bij te werken.

## <a name="reconfiguration-types"></a>Typen opnieuw configureren
Herconfiguraties kunnen worden onderverdeeld in twee typen:

- Herconfiguraties waarvan de primaire wordt gewijzigd:
    - **Failover**: failovers zijn herconfiguraties als reactie op het mislukken van een actieve primaire.
    - **SwapPrimary**: swaps zijn herconfiguraties waarbij service Fabric een actieve primaire van het ene naar het andere knoop punt moet verplaatsen, meestal als reactie op taak verdeling of een upgrade.

- Herconfiguraties waarbij de primaire niet wordt gewijzigd.

## <a name="reconfiguration-phases"></a>Fasen opnieuw configureren
Een herconfiguratie verloopt in verschillende fasen:

- **Phase0**: deze fase treedt op in swap-primaire herconfiguratie, waarbij de huidige primaire status wordt overgezet naar de nieuwe primaire en overgangen naar Active secondary.

- **Phase1**: deze fase treedt op wanneer de primaire configuratie wordt gewijzigd. Tijdens deze fase geeft Service Fabric de juiste primaire replica op onder de huidige replica's. Deze fase is niet nodig tijdens swap-primaire herconfiguraties omdat de nieuwe primaire keer al is gekozen. 

- **Phase2**: tijdens deze fase zorgt service Fabric ervoor dat alle gegevens beschikbaar zijn in een meerderheid van de replica's van de huidige configuratie.

Er zijn verschillende andere fasen die alleen voor intern gebruik zijn.

## <a name="stuck-reconfigurations"></a>Vastgelopen herconfiguraties
Het opnieuw configureren van de configuratie kan om verschillende redenen *vastzitten* . Enkele veelvoorkomende redenen zijn onder andere:

- **Down-replica's**: voor sommige herconfiguratie fasen is een meerderheid van de replica's in de configuratie vereist.
- **Netwerk-of communicatie problemen**: opnieuw configureren vereist een netwerk verbinding tussen verschillende knoop punten.
- **API-fouten**: het protocol voor opnieuw configureren vereist dat service-implementaties bepaalde api's volt ooien. Als u het annulerings token in een betrouw bare service bijvoorbeeld niet verhelpt, worden SwapPrimary opnieuw geconfigureerd om vastgelopen te krijgen.

Gebruik status rapporten van systeem onderdelen, zoals System.FM, System. RA en System. RAP, om te achterhalen waar een herconfiguratie is vastgelopen. Op de [pagina systeem status rapport](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) worden deze status rapporten beschreven.

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over Service Fabric concepten:

- [Levenscyclus van Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
- [Systeem status rapporten](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Replica's en exemplaren](service-fabric-concepts-replica-lifecycle.md)
