---
title: Initiële grootte van de Server groep-grootschalige (Citus)-Azure Database for PostgreSQL
description: Kies de juiste begin grootte voor uw use-case
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026400"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Selecteer de begin grootte voor de Server groep grootschalige (Citus)

De grootte van een server groep, zowel het aantal knoop punten als hun hardware-capaciteit, kan [eenvoudig worden gewijzigd](howto-hyperscale-scale-grow.md). U moet echter wel een begin grootte kiezen voor een nieuwe server groep. Hier volgen enkele tips voor een redelijke keuze.

## <a name="multi-tenant-saas-use-case"></a>Multi tenant SaaS-gebruik-Case

Wanneer u migreert naar grootschalige (Citus) vanuit een bestaand PostgreSQL data base-exemplaar met één knoop punt, kiest u een cluster waar het aantal werk vCores en het RAM-geheugen in totaal gelijk is aan dat van het oorspronkelijke exemplaar. In dergelijke scenario's hebben we twee tot drie verbeteringen in prestaties gezien, omdat sharding het gebruik van bronnen verbetert, waardoor kleinere indexen, enzovoort.

Het aantal vCore is in feite de enige beslissing. RAM-toewijzing wordt momenteel bepaald op basis van het aantal vCore, zoals beschreven op de pagina [configuratie opties voor grootschalige (Citus)](concepts-hyperscale-configuration-options.md) .
Het coördinator knooppunt vereist niet zoveel RAM-geheugen als werk nemers, maar er is geen manier om RAM en vCores onafhankelijk te kiezen.

## <a name="real-time-analytics-use-case"></a>Real-time analyse-gebruik-Case

Totaal aantal vCores: wanneer werk gegevens in het RAM-geheugen passen, kunt u een lineaire prestatie verbetering verwachten op grootschalige (Citus) in verhouding tot het aantal worker-kernen. Als u het juiste aantal vCores voor uw behoeften wilt bepalen, moet u rekening houden met de huidige latentie voor query's in uw data base met één knoop punt en de vereiste latentie in grootschalige (Citus). Deel de huidige latentie door de gewenste latentie en rond het resultaat af.

RAM voor werkrollen: de beste manier is om voldoende geheugen beschikbaar te stellen zodat het merendeel van de werksets in het geheugen past. Het type query's dat voor uw toepassing wordt gebruikt, heeft invloed op geheugenvereisten. U kunt uitleg analyseren uitvoeren op een query om te bepalen hoeveel geheugen nodig is. Houd er rekening mee dat vCores en RAM samen worden geschaald, zoals beschreven in het artikel [grootschalige (Citus) configuratie opties](concepts-hyperscale-configuration-options.md) .

## <a name="next-steps"></a>Volgende stappen

- [Servergroep schalen](howto-hyperscale-scale-grow.md)
- Meer informatie over de opties voor de [prestaties](concepts-hyperscale-configuration-options.md)van de Server groep.
