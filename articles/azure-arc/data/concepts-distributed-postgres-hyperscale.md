---
title: Concepten voor het distribueren van gegevens en het uitschalen met Arc ingeschakelde PostgreSQL grootschalige-Server groep
titleSuffix: Azure Arc enabled data services
description: Concepten voor het distribueren van gegevens met PostgreSQL grootschalige-Server groep met Arc-functionaliteit
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c01da4aed9e27296ea7b570420bb190b16749848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90936380"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>Concepten voor het distribueren van gegevens met PostgreSQL grootschalige-Server groep met Arc-functionaliteit

In dit artikel worden de belangrijkste concepten beschreven die belang rijk zijn voor het grootste deel van Azure Arc enabled PostgreSQL grootschalige.
De onderstaande artikelen verwijzen naar de concepten die worden beschreven voor Azure Database for PostgreSQL grootschalige (Citus). Het is dezelfde technologie als Azure Arc enabled PostgreSQL grootschalige, zodat dezelfde concepten en perspectieven van toepassing zijn.

**Wat is het verschil tussen deze?**
- _Azure Database for PostgreSQL Hyperscale (Citus)_

Dit is de grootschalige vorm factor van de post gres-data base-engine die beschikbaar is als Database as a Service in azure (PaaS). Het wordt aangestuurd door de Citus-extensie waarmee de grootschalige-ervaring wordt ingeschakeld. In deze vorm factor wordt de service uitgevoerd in de micro soft-data centers en wordt beheerd door micro soft.

- _Azure Arc enabled PostgreSQL grootschalige_

Dit is de grootschalige vorm factor van de post gres-data base-engine die beschikbaar is voor de gegevens service Azure Arc enabled. In deze vorm factor bieden onze klanten de infra structuur die als host moet fungeren voor de systemen en deze kan worden gebruikt.

Hieronder vindt u een overzicht van de belangrijkste concepten over Azure Arc enabled PostgreSQL grootschalige:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>Knooppunten en tabellen
Het is belang rijk dat u op de hoogte bent van een van de volgende concepten om te profiteren van de post gres-grootschalige van Azure Arc enabled:
- Gespecialiseerde post gres-knoop punten in azure Arc enabled PostgreSQL grootschalige: coördinator en werk nemers
- Typen tabellen: gedistribueerde tabellen, referentie tabellen en lokale tabellen
- Shards

Meer informatie vindt u op [knoop punten en tabellen in azure database for PostgreSQL – grootschalige (Citus)](../../postgresql/concepts-hyperscale-nodes.md). 

## <a name="determine-the-application-type"></a>Het toepassings type bepalen
Het is belang rijk om het type toepassing dat u bouwt duidelijk te identificeren. Hoe komt dat? Omdat het uitvoeren van efficiënte query's voor een Azure-PostgreSQL grootschalige-Server groep vereist dat tabellen goed worden gedistribueerd over servers. De aanbevolen distributie is afhankelijk van het type toepassing en de bijbehorende query patronen. Er zijn in ruime mate twee soorten toepassingen die goed werken op Azure Arc enabled post gres grootschalige:
- Multi tenant-toepassingen
- Realtime toepassingen

De eerste stap bij het model leren van gegevens is om te identificeren welke van de toepassingen er beter uitziet dan uw toepassing.

Bekijk de details bij het bepalen van het [toepassings type](../../postgresql/concepts-hyperscale-app-type.md).


## <a name="choose-a-distribution-column"></a>Een distributiekolom kiezen
Waarom een gedistribueerde kolom kiezen?

Dit is een van de belangrijkste beslissingen voor het maken van modellen. Azure Arc enabled PostgreSQL grootschalige slaat rijen op in Shards op basis van de waarde van de kolom distributie kolommen. De juiste keuze groepen hebben gerelateerde gegevens samen op dezelfde fysieke knoop punten, waardoor query's snel worden gemaakt en ondersteuning wordt toegevoegd voor alle SQL-functies. Een onjuiste keuze maakt het systeem langzaam en wordt niet alle SQL-functies op verschillende knoop punten ondersteund. In dit artikel worden de twee meest voorkomende grootschalige-scenario's voor de verdeling van kolom tips geboden.

Bekijk de details op [distributie kolommen kiezen](../../postgresql/concepts-hyperscale-choose-distribution-column.md).


## <a name="table-colocation"></a>Tabelcolocatie

De co-locatie bevat het opslaan van gerelateerde informatie samen op dezelfde knoop punten. Query's kunnen snel worden uitgevoerd wanneer alle benodigde gegevens beschikbaar zijn zonder netwerk verkeer. Als u gerelateerde gegevens op verschillende knoop punten wilt verplaatsen, kunt u query's efficiënt uitvoeren op elk knoop punt.

Meer informatie vindt u op tabel co- [locatie](../../postgresql/concepts-hyperscale-colocation.md).


## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over het maken van Azure Arc enabled PostgreSQL grootschalige](create-postgresql-hyperscale-server-group.md)
- [Meer informatie over het uitschalen van Azure Arc ingeschakelde PostgreSQL grootschalige-Server groepen die zijn gemaakt in uw Arc-gegevens controller](scale-out-postgresql-hyperscale-server-group.md)
- [Meer informatie over Azure Arc enabled Data Services](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Meer informatie over Azure Arc](https://aka.ms/azurearc)

