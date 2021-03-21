---
title: Event Hubs uitvoer van Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u gegevens kunt uitvoeren van Azure Stream Analytics naar Azure Event Hubs.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 02abdd752528ce28642b6228648062ed961d5ae3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452387"
---
# <a name="event-hubs-output-from-azure-stream-analytics"></a>Event Hubs uitvoer van Azure Stream Analytics

De [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/) -service is een zeer schaal bare Event ingestor voor publiceren en abonneren. Het kan miljoenen gebeurtenissen per seconde verzamelen. Een Event Hub als uitvoer wordt gebruikt wanneer de uitvoer van een Stream Analytics taak de invoer van een andere streaming-taak wordt. Zie de sectie [uitvoer Batch grootte](#output-batch-size) voor informatie over de maximale grootte van berichten en het optimaliseren van de Batch grootte.

## <a name="output-configuration"></a>Uitvoer configuratie

De volgende tabel bevat de para meters die nodig zijn voor het configureren van gegevens stromen van Event hubs als uitvoer.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias | Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze Event Hub te sturen. |
| Event hub-naamruimte | Een container voor een set met bericht entiteiten. Wanneer u een nieuwe Event Hub hebt gemaakt, hebt u ook een Event Hub naam ruimte gemaakt. |
| Naam van Event Hub | De naam van de Event Hub uitvoer. |
| Naam van de Event hub-beleid | Het beleid voor gedeelde toegang, dat u kunt maken op het tabblad **configureren** van de Event hub. Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. |
| Event hub-beleids sleutel | De gedeelde toegangs sleutel die wordt gebruikt voor het verifiëren van toegang tot de naam ruimte Event Hub. |
| Partitie sleutel kolom | Optioneel. Een kolom die de partitie sleutel voor Event Hub uitvoer bevat. |
| Serialisatie-indeling voor gebeurtenissen | De serialisatie-indeling voor uitvoer gegevens. JSON, CSV en Avro worden ondersteund. |
| Encoding | Voor CSV en JSON is UTF-8 de enige coderings indeling die op dit moment wordt ondersteund. |
| Scheidingsteken | Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal gemeen schappelijke scheidings tekens voor het serialiseren van gegevens in CSV-indeling. Ondersteunde waarden zijn komma, punt komma, spatie, tab en verticale streep. |
| Indeling | Alleen van toepassing op JSON-serialisatie. Met **regel scheiding** wordt opgegeven dat de uitvoer wordt opgemaakt door elk JSON-object gescheiden door een nieuwe regel. Als u **regel gescheiden** selecteert, wordt de JSON één object per keer gelezen. De gehele inhoud zelf zou geen geldige JSON kunnen zijn. **Matrix** geeft aan dat de uitvoer is ingedeeld als een matrix van JSON-objecten.  |
| Eigenschappen kolommen | Optioneel. Door komma's gescheiden kolommen die moeten worden toegevoegd als gebruikers eigenschappen van het uitgaande bericht in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [aangepaste meta gegevens eigenschappen voor uitvoer](#custom-metadata-properties-for-output). |

## <a name="partitioning"></a>Partitionering

Partitioneren varieert, afhankelijk van de uitlijning van de partitie. Wanneer de partitie sleutel voor Event Hub uitvoer gelijk wordt uitgelijnd met de stap upstream (vorige), is het aantal schrijvers hetzelfde als het aantal partities in Event Hub uitvoer. Elke schrijver maakt gebruik van de [EventHubSender-klasse](/dotnet/api/microsoft.servicebus.messaging.eventhubsender) voor het verzenden van gebeurtenissen naar de specifieke partitie. Wanneer de partitie sleutel voor Event Hub uitvoer niet is afgestemd op de stap van de upstream (vorige), is het aantal schrijvers hetzelfde als het aantal partities in die vorige stap. Elke schrijver maakt gebruik van de [SendBatchAsync-klasse](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync) in **EventHubClient** om gebeurtenissen te verzenden naar alle uitvoer partities. 

## <a name="output-batch-size"></a>Grootte van uitvoer batch

De maximale bericht grootte is 256 KB of 1 MB per bericht. Zie [Event hubs limieten](../event-hubs/event-hubs-quotas.md)voor meer informatie. Wanneer invoer/uitvoer-partitionering niet is uitgelijnd, wordt elke gebeurtenis afzonderlijk verpakt in `EventData` en verzonden in een batch van Maxi maal de maximale bericht grootte. Dit gebeurt ook als [Eigenschappen van aangepaste meta gegevens](#custom-metadata-properties-for-output) worden gebruikt. Wanneer de invoer/uitvoer-partitionering is uitgelijnd, worden meerdere gebeurtenissen in één `EventData` exemplaar verpakt, tot de maximale bericht grootte en verzonden.

## <a name="custom-metadata-properties-for-output"></a>Aangepaste meta gegevens eigenschappen voor uitvoer

U kunt query kolommen als gebruikers eigenschappen aan uw uitgaande berichten toevoegen. Deze kolommen gaan niet naar de payload. De eigenschappen zijn aanwezig in de vorm van een woorden lijst in het uitvoer bericht. *Sleutel* is de kolom naam en- *waarde* is de kolom waarde in de woorden lijst eigenschappen. Alle Stream Analytics gegevens typen worden ondersteund, behalve record en matrix.

In het volgende voor beeld worden de velden `DeviceId` en `DeviceStatus` worden toegevoegd aan de meta gegevens.

1. Gebruik de volgende query:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Configureren `DeviceId,DeviceStatus` als eigenschaps kolommen in de uitvoer.

   :::image type="content" source="media/event-hubs-output/property-columns.png" alt-text="Eigenschappen kolommen":::

De volgende afbeelding heeft de verwachte uitvoer bericht eigenschappen die in EventHub zijn geïnspecteerd met behulp van [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/event-hubs-output/custom-properties.png" alt-text="Aangepaste eigenschappen van gebeurtenis":::

## <a name="next-steps"></a>Volgende stappen

* [Beheerde identiteiten gebruiken voor toegang tot Event hub vanuit een Azure Stream Analytics-taak (preview-versie)](event-hubs-managed-identity.md)
* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
