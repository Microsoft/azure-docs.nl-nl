---
title: Load Balancer TCP Reset en time-out voor inactiviteit in azure
titleSuffix: Azure Load Balancer
description: In dit artikel vindt u informatie over Azure Load Balancer met bidirectionele TCP eerste pakketten bij time-out voor inactiviteit.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: allensu
ms.openlocfilehash: 0d02b46345af13770f77a7dac452127a665e01fd
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696741"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>Load Balancer TCP Reset en time-out voor inactiviteit

U kunt [Standard Load Balancer](./load-balancer-overview.md) gebruiken om een meer voorspel bare toepassings gedrag te maken voor uw SCENARIO'S door TCP reset in te scha kelen bij niet-actief voor een bepaalde regel. Het standaard gedrag van Load Balancer is het op de achtergrond neerzetten van stromen wanneer de time-out van een stroom niet actief is.  Als u deze functie inschakelt, worden Load Balancer bidirectionele TCP resets (TCP eerste pakket) verzonden naar time-out voor inactiviteit.  Hiermee wordt de eind punten van uw toepassing geïnformeerd dat er een time-out voor de verbinding is opgetreden. deze kan niet meer worden gebruikt.  Eind punten kunnen onmiddellijk een nieuwe verbinding tot stand brengen, indien nodig.

![Load Balancer TCP opnieuw instellen](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>Opnieuw instellen van TCP

U wijzigt dit standaard gedrag en schakelt het verzenden van TCP-opnieuw instellen in op inactieve time-outwaarde voor binnenkomende NAT-regels, taakverdelings regels en [Uitgaande regels](./load-balancer-outbound-connections.md#outboundrules).  Wanneer dit is ingeschakeld per regel, verzendt Load Balancer bidirectionele TCP-opnieuw instellen (eerste TCP-pakketten) naar zowel client-als server eindpunten op het moment van time-out voor inactiviteit voor alle overeenkomende stromen.

Eind punten die TCP eerste pakketten ontvangen, sluiten de overeenkomstige socket direct. Dit biedt een onmiddellijke melding aan de eind punten die de release van de verbinding heeft en toekomstige communicatie op dezelfde TCP-verbinding zal mislukken.  Toepassingen kunnen verbindingen opschonen wanneer de socket wordt gesloten en de verbindingen zo nodig opnieuw tot stand worden gebracht, zonder te wachten tot de TCP-verbinding uiteindelijk een time-out heeft.

Voor veel scenario's kan dit ertoe leiden dat de TCP-(of Application Layer) keepalives niet hoeven te worden verzonden om de time-out voor inactiviteit van een stroom te vernieuwen. 

Als uw niet-actieve duur groter is dan de limieten die zijn toegestaan door de configuratie of uw toepassing een ongewenste gedrag laat zien waarbij TCP opnieuw instellen is ingeschakeld, moet u mogelijk nog steeds TCP-keepalives gebruiken (of keepalives van de toepassingslaag) om de Live van de TCP-verbindingen te bewaken.  Verder kan keepalives ook handig blijven als de verbinding op een andere locatie in het pad wordt verzonden, met name voor keepalives van de toepassingslaag.  

Bekijk zorgvuldig het volledige end-to-end-scenario om te bepalen of u voor delen hebt voor het inschakelen van TCP-opnieuw instellen, het aanpassen van de time-out voor inactiviteit, en als er extra stappen nodig zijn om ervoor te zorgen dat het gewenste toepassings gedrag

## <a name="configurable-tcp-idle-timeout"></a>Configureerbare time-out voor TCP-inactiviteit

Azure Load Balancer heeft het volgende time-outbereik voor inactiviteit:
-  4 minuten tot 100 minuten voor uitgaande regels
-  4 minuten tot 30 minuten voor Load Balancer regels en binnenkomende NAT-regels

Standaard is deze ingesteld op 4 minuten. Als een periode van inactiviteit langer is dan de time-outwaarde, is er geen garantie dat de TCP-of HTTP-sessie tussen de client en de Cloud service wordt bewaard.

Wanneer de verbinding wordt gesloten, kan uw client toepassing het volgende fout bericht ontvangen: ' de onderliggende verbinding is gesloten: er is een verbinding die naar verwachting wordt gehouden door de server verbroken. '

Een veelvoorkomende procedure is het gebruik van een TCP-Keep-Alive. Deze procedure houdt de verbinding gedurende een langere periode actief. Zie voor meer informatie deze [.net-voor beelden](/dotnet/api/system.net.servicepoint.settcpkeepalive). Als Keep-Alive is ingeschakeld, worden pakketten verzonden tijdens peri Oden van inactiviteit op de verbinding. Keep-Alive-pakketten zorgen ervoor dat de time-outwaarde niet wordt bereikt en de verbinding gedurende een lange periode wordt bewaard.

De instelling werkt alleen voor binnenkomende verbindingen. Om te voor komen dat de verbinding wordt verbroken, configureert u de TCP Keep-Alive met een interval van minder dan de instelling voor niet-actieve time-out of verhoogt u de time-outwaarde. Ter ondersteuning van deze scenario's is ondersteuning voor een Configureer bare time-out voor inactiviteit toegevoegd.

TCP Keep-Alive werkt voor scenario's waarbij de levens duur van de accu geen beperking is. Het wordt niet aanbevolen voor mobiele toepassingen. Het gebruik van een TCP-Keep-Alive in een mobiele toepassing kan de accu van het apparaat sneller leeg laten.


## <a name="limitations"></a>Beperkingen

- TCP Reset wordt alleen verzonden tijdens de TCP-verbinding in de INGESTELDe status.
- TCP Reset wordt niet verzonden voor interne load balancers waarvoor HA-poorten zijn geconfigureerd.
- De time-out voor inactiviteit van TCP heeft geen invloed op de taakverdelings regels op het UDP-protocol.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Standard Load Balancer](./load-balancer-overview.md).
- Meer informatie over [Uitgaande regels](./load-balancer-outbound-connections.md#outboundrules).
- [EERSTE TCP-time-out voor inactiviteit configureren](load-balancer-tcp-idle-timeout.md)