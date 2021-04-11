---
title: Initiële grootte van de Server groep-grootschalige (Citus)-Azure Database for PostgreSQL
description: Kies de juiste begin grootte voor uw use-case
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 8b87cfc8276d13ccc7e12a4901489ea0b1e770a5
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012503"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Selecteer de begin grootte voor de Server groep grootschalige (Citus)

De grootte van een server groep, zowel het aantal knoop punten als de hardware-capaciteit is [eenvoudig te wijzigen](howto-hyperscale-scale-grow.md)). U moet echter wel een begin grootte kiezen voor een nieuwe server groep. Hier volgen enkele tips voor een redelijke keuze.

## <a name="use-cases"></a>Use-cases

Grootschalige (Citus) wordt vaak op de volgende manieren gebruikt.

### <a name="multi-tenant-saas"></a>SaaS met meerdere tenants

Wanneer u migreert naar grootschalige (Citus) vanuit een bestaand PostgreSQL data base-exemplaar met één knoop punt, kiest u een cluster waar het aantal werk vCores en het RAM-geheugen in totaal gelijk is aan dat van het oorspronkelijke exemplaar. In dergelijke scenario's hebben we twee tot drie verbeteringen in prestaties gezien, omdat sharding het gebruik van bronnen verbetert, waardoor kleinere indexen, enzovoort.

Het aantal vCore is in feite de enige beslissing. RAM-toewijzing wordt momenteel bepaald op basis van het aantal vCore, zoals beschreven op de pagina [configuratie opties voor grootschalige (Citus)](concepts-hyperscale-configuration-options.md) .
Het coördinator knooppunt vereist niet zoveel RAM-geheugen als werk nemers, maar er is geen manier om RAM en vCores onafhankelijk te kiezen.

### <a name="real-time-analytics"></a>Realtime analyses

Totaal aantal vCores: wanneer werk gegevens in het RAM-geheugen passen, kunt u een lineaire prestatie verbetering verwachten op grootschalige (Citus) in verhouding tot het aantal worker-kernen. Als u het juiste aantal vCores voor uw behoeften wilt bepalen, moet u rekening houden met de huidige latentie voor query's in uw data base met één knoop punt en de vereiste latentie in grootschalige (Citus). Deel de huidige latentie door de gewenste latentie en rond het resultaat af.

RAM van werk nemers: de beste manier is dat er voldoende geheugen beschikbaar is dat de meeste werk sets in het geheugen passen. Het type query's dat voor uw toepassing wordt gebruikt, heeft invloed op geheugenvereisten. U kunt uitleg analyseren uitvoeren op een query om te bepalen hoeveel geheugen nodig is. Houd er rekening mee dat vCores en RAM samen worden geschaald, zoals beschreven in het artikel [grootschalige (Citus) configuratie opties](concepts-hyperscale-configuration-options.md) .

## <a name="choosing-a-hyperscale-citus-tier"></a>Een grootschalige-laag (Citus) kiezen

> [!IMPORTANT]
> De grootschalige (Citus) Basic-laag is momenteel beschikbaar als preview-versie.  Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
>
> U kunt een volledige lijst met andere nieuwe functies bekijken in [Preview-functies voor grootschalige (Citus)](hyperscale-preview-features.md).

In de bovenstaande secties ziet u hoe veel vCores en hoeveel RAM-geheugen nodig zijn voor elke use-case. U kunt aan deze vereisten voldoen via een keuze tussen twee grootschalige-lagen (Citus): de laag basis en de laag standaard.

De laag basis maakt gebruik van één database knooppunt voor het uitvoeren van verwerking, terwijl de Standard-laag meer knoop punten toestaat. De lagen zijn anders identiek, waarbij dezelfde functies worden aangeboden. In sommige gevallen kunnen de vCores van een knoop punt en schijf ruimte naar voldoende worden geschaald, en in andere gevallen is de samen werking tussen meerdere knoop punten vereist.

Voor een vergelijking van de lagen, zie de pagina [basis concepten laag](concepts-hyperscale-tiers.md) .

## <a name="next-steps"></a>Volgende stappen

- [Servergroep schalen](howto-hyperscale-scale-grow.md)
- Meer informatie over de opties voor de [prestaties](concepts-hyperscale-configuration-options.md)van de Server groep.
