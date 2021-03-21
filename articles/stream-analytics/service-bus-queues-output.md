---
title: Service Bus uitvoer wachtrijen van Azure Stream Analytics
description: In dit artikel worden Service Bus wacht rijen als uitvoer voor Azure Stream Analytics beschreven.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: ad5bb90a0df68cc6c1c230c234c1f695312da3d8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102451776"
---
# <a name="service-bus-queues-output-from-azure-stream-analytics"></a>Service Bus uitvoer wachtrijen van Azure Stream Analytics

[Service Bus wachtrijen](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) bieden een FIFO-bericht levering aan een of meer concurrerende consumenten. Normaal gesp roken worden berichten ontvangen en verwerkt door de ontvangers in de tijdelijke volg orde waarin ze aan de wachtrij zijn toegevoegd. Elk bericht wordt door slechts één bericht verbruiker ontvangen en verwerkt.

In [compatibiliteits niveau 1,2](stream-analytics-compatibility-level.md)maakt Azure stream Analytics gebruik van het [AMQP-berichten Protocol (Advanced Message queueing Protocol)](../service-bus-messaging/service-bus-amqp-overview.md) om te schrijven naar service bus-wacht rijen en-onderwerpen. Met AMQP kunt u platform onafhankelijke, hybride toepassingen bouwen met behulp van een open standaard protocol.

## <a name="output-configuration"></a>Uitvoer configuratie

De volgende tabel bevat de namen van de eigenschappen en de bijbehorende beschrijvingen voor het maken van een wachtrij-uitvoer.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias |Een beschrijvende naam die wordt gebruikt in query's om de uitvoer van de query naar deze Service Bus wachtrij te sturen. |
| Service Bus naam ruimte |Een container voor een set met bericht entiteiten. |
| Wachtrijnaam |De naam van de Service Bus wachtrij. |
| Naam van wachtrij beleid |Wanneer u een wachtrij maakt, kunt u ook een beleid voor gedeelde toegang maken op het tabblad **configureren** van de wachtrij. Elk gedeeld toegangs beleid heeft een naam, machtigingen die u instelt en toegangs sleutels. |
| Wachtrij beleids sleutel |De gedeelde toegangs sleutel die wordt gebruikt voor het verifiëren van toegang tot de naam ruimte Service Bus. |
| Serialisatie-indeling voor gebeurtenissen |De serialisatie-indeling voor uitvoer gegevens. JSON, CSV en Avro worden ondersteund. |
| Encoding |Voor CSV en JSON is UTF-8 de enige coderings indeling die op dit moment wordt ondersteund. |
| Scheidingsteken |Alleen van toepassing op CSV-serialisatie. Stream Analytics ondersteunt een aantal gemeen schappelijke scheidings tekens voor het serialiseren van gegevens in CSV-indeling. Ondersteunde waarden zijn komma, punt komma, spatie, tab en verticale streep. |
| Indeling |Alleen van toepassing op JSON-type. Met **regel scheiding** wordt opgegeven dat de uitvoer wordt opgemaakt door elk JSON-object gescheiden door een nieuwe regel. Als u **regel gescheiden** selecteert, wordt de JSON één object per keer gelezen. De gehele inhoud zelf zou geen geldige JSON kunnen zijn. **Matrix** geeft aan dat de uitvoer is ingedeeld als een matrix van JSON-objecten. |
| Eigenschappen kolommen | Optioneel. Door komma's gescheiden kolommen die moeten worden toegevoegd als gebruikers eigenschappen van het uitgaande bericht in plaats van de payload. Meer informatie over deze functie vindt u in de sectie [aangepaste meta gegevens eigenschappen voor uitvoer](#custom-metadata-properties-for-output). |
| Kolom systeem eigenschappen | Optioneel. Sleutel waarde-paren van systeem eigenschappen en bijbehorende kolom namen die aan het uitgaande bericht moeten worden toegevoegd in plaats van de payload.  |

Het aantal partities is [gebaseerd op de service bus SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitie sleutel is een unieke gehele waarde voor elke partitie.

## <a name="partitioning"></a>Partitionering

Partitioneren wordt automatisch gekozen. Het aantal partities is gebaseerd op de [Service Bus SKU en grootte](../service-bus-messaging/service-bus-partitioning.md). De partitie sleutel is een unieke gehele waarde voor elke partitie. Het aantal schrijvers van de uitvoer is hetzelfde als het aantal partities in de uitvoer wachtrij.

## <a name="output-batch-size"></a>Grootte van uitvoer batch

De maximale bericht grootte is 256 KB per bericht voor de Standard-laag en 1 MB voor de Premium-laag. Zie [Service Bus limieten](../service-bus-messaging/service-bus-quotas.md)voor meer informatie. Als u wilt optimaliseren, gebruikt u één gebeurtenis per bericht.

## <a name="custom-metadata-properties-for-output"></a>Aangepaste meta gegevens eigenschappen voor uitvoer

U kunt query kolommen als gebruikers eigenschappen aan uw uitgaande berichten toevoegen. Deze kolommen gaan niet naar de payload. De eigenschappen zijn aanwezig in de vorm van een woorden lijst in het uitvoer bericht. *Sleutel* is de kolom naam en- *waarde* is de kolom waarde in de woorden lijst eigenschappen. Alle Stream Analytics gegevens typen worden ondersteund, behalve record en matrix.

In het volgende voor beeld worden de velden `DeviceId` en `DeviceStatus` worden toegevoegd aan de meta gegevens.

1. Gebruik de volgende query:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Configureren `DeviceId,DeviceStatus` als eigenschaps kolommen in de uitvoer.

   :::image type="content" source="media/service-bus-queues-output/property-columns.png" alt-text="Eigenschappen kolommen":::

De volgende afbeelding heeft de verwachte uitvoer bericht eigenschappen die in EventHub zijn geïnspecteerd met behulp van [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/service-bus-queues-output/custom-properties.png" alt-text="Aangepaste eigenschappen van gebeurtenis":::

## <a name="system-properties"></a>Systeemeigenschappen

U kunt query kolommen als [systeem eigenschappen](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#properties) koppelen aan uw uitgaande service bus-wachtrij of onderwerp-berichten.

Deze kolommen worden niet in de payload gezet, maar de bijbehorende [systeem eigenschap](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#properties) BrokeredMessage wordt gevuld met de query kolom waarden.
Deze systeem eigenschappen worden ondersteund `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc` .

Teken reeks waarden van deze kolommen worden geparseerd als overeenkomend type eigenschaps waarde van het systeem en eventuele fouten bij het parseren worden behandeld als gegevens fouten.
Dit veld wordt als een JSON-object indeling gegeven. Meer informatie over deze indeling is als volgt:

* Omgeven door accolades {} .
* Geschreven in sleutel/waarde-paren.
* Sleutels en waarden moeten teken reeksen zijn.
* Sleutel is de naam en waarde van de systeem eigenschap is de kolom naam van de query.
* Sleutels en waarden worden gescheiden door een dubbele punt.
* Elke sleutel/waarde-paar wordt gescheiden door een komma.

Hier ziet u hoe u deze eigenschap gebruikt –

* Ophalen `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Kolom systeem eigenschappen: `{ "MessageId": "column1", "PartitionKey": "column2"}`

Hiermee stelt u de `MessageId` Service Bus-wachtrij berichten in met `column1` de waarden en PartitionKey wordt ingesteld met `column2` de waarden.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
* [Quickstart: een Azure Stream Analytics-taak maken via de Azure CLI](quick-create-azure-cli.md)
* [Quickstart: Een Azure Stream Analytics-taak maken via een ARM-sjabloon](quick-create-azure-resource-manager.md)
* [Quickstart: Een Stream Analytics-taak maken met behulp van Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Een Azure Stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)
* [Snelstartgids: een Azure Stream Analytics-taak maken in Visual Studio code](quick-create-visual-studio-code.md)