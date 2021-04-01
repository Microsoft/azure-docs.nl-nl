---
title: Gegevens stromen in Azure SQL Edge
description: Meer informatie over het streamen van gegevens in Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f63ab040e750c0c642c9656a5482529b926e9295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392109"
---
# <a name="data-streaming-in-azure-sql-edge"></a>Gegevens stromen in Azure SQL Edge

Azure SQL Edge biedt een systeem eigen implementatie van mogelijkheden voor het streamen van gegevens met de naam T-SQL-streaming. Het biedt realtime gegevens stromen, analyses en gebeurtenis verwerking voor het analyseren en verwerken van hoge volumes van gegevens snel streamen uit meerdere bronnen tegelijk. T-SQL-streaming is gebouwd met behulp van dezelfde Streaming-Engine met hoge prestaties die [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) in Microsoft Azure voorziet. De functie ondersteunt een vergelijk bare reeks mogelijkheden die wordt geboden door Azure Stream Analytics die aan de rand worden uitgevoerd.

Net als bij Stream Analytics herkent T-SQL streaming patronen en relaties in gegevens die worden geëxtraheerd uit een aantal IoT-invoer bronnen, waaronder apparaten, Sens oren en toepassingen. U kunt deze patronen gebruiken om acties te activeren en werk stromen te initiëren. U kunt bijvoorbeeld waarschuwingen, feedgegevens maken voor een rapportage-of visualisatie-oplossing, of de gegevens opslaan voor later gebruik. 

Met T-SQL streaming kunt u:

* Analyseer realtime telemetriegegevens van IoT-apparaten.
* Gebruik real-time analyse van gegevens die zijn gegenereerd op basis van autonome en stuur Programma's.
* Gebruik externe controle en voor speld onderhoud van hoogwaardige industriële of productie-assets.
* Gebruik anomalie detectie en patroon herkenning van IoT-sensor-aflezingen in een land bouwer of een energie Farm.

## <a name="how-does-t-sql-streaming-work"></a>Hoe werkt T-SQL-streaming?

T-SQL-streaming werkt op exact dezelfde manier als [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md#how-does-stream-analytics-work). Het concept van streaming- *taken* wordt bijvoorbeeld gebruikt voor het verwerken van realtime gegevens stromen. 

Een stream Analytics-taak bestaat uit:

- **Stroom invoer**: Hiermee worden de verbindingen met een gegevens bron gedefinieerd waaruit de gegevens stroom moet worden gelezen. Azure SQL Edge ondersteunt momenteel de volgende invoer typen voor streams:
    - Edge hub
    - Kafka (ondersteuning voor Kafka-invoer is momenteel alleen beschikbaar voor Intel/AMD64-versies van Azure SQL Edge.)

- **Uitvoer** van de stream: Hiermee worden de verbindingen met een gegevens bron gedefinieerd om de gegevens stroom naar te schrijven. Azure SQL Edge ondersteunt momenteel de volgende uitvoer typen voor streams
    - Edge hub
    - SQL (de SQL-uitvoer kan een lokale Data Base zijn in het exemplaar van Azure SQL Edge of een externe SQL Server of Azure SQL Database.) 

- **Stroom query**: Hiermee definieert u de trans formatie, aggregaties, filters, sorteren en samen voegingen die moeten worden toegepast op de invoer stroom voordat deze wordt geschreven naar de uitvoer van de stroom. De stroom query is gebaseerd op de query taal die wordt gebruikt door Stream Analytics. Zie [Stream Analytics query language (Engelstalig](/stream-analytics-query/stream-analytics-query-language-reference)) voor meer informatie.

> [!IMPORTANT]
> T-SQL-streaming, in tegens telling tot Stream Analytics, biedt momenteel geen ondersteuning [voor het gebruik van referentie gegevens voor Zoek opdrachten](../stream-analytics/stream-analytics-use-reference-data.md) of het [gebruik van UDF en UDA in een stroom taak](../stream-analytics/streaming-technologies.md#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> T-SQL streaming ondersteunt alleen een subset van de taal surface area die door Stream Analytics wordt ondersteund. Zie [Stream Analytics query language (Engelstalig](/stream-analytics-query/stream-analytics-query-language-reference)) voor meer informatie.

## <a name="limitations-and-restrictions"></a>Beperkingen en beperkingen

De volgende beperkingen en beperkingen zijn van toepassing op T-SQL-streaming. 

- Er kan slechts één streaming-taak op een specifiek tijdstip actief zijn. Taken die al worden uitgevoerd, moeten worden gestopt voordat een andere taak kan worden gestart.
- De uitvoering van elke streaming-taak wordt uitgevoerd met één thread. Als de streaming-taak meerdere query's bevat, wordt elke query geëvalueerd in een seriële volg orde.
- Wanneer u een streaming-taak in Azure SQL Edge hebt gestopt, kan er enige vertraging optreden voordat de volgende streaming-taak kan worden gestart. Deze vertraging wordt geïntroduceerd omdat het onderliggende streaming-proces moet worden gestopt als reactie op de aanvraag voor het stoppen van de taak en vervolgens opnieuw wordt gestart als reactie op de aanvraag voor het starten van een taak. 
- T-SQL-streaming tot 32 partities voor een Kafka-stroom. Pogingen om een hoger aantal partities te configureren, resulteren in een fout. 

## <a name="next-steps"></a>Volgende stappen

- [Een Stream Analytics-taak maken in Azure SQL Edge ](create-stream-analytics-job.md)
- [Meta gegevens weer geven die zijn gekoppeld aan stroom taken in Azure SQL Edge ](streaming-catalog-views.md)
- [Externe stroom maken](create-external-stream-transact-sql.md)