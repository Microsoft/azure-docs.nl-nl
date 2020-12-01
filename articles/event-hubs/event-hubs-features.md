---
title: Overzicht van functies-Azure Event Hubs | Microsoft Docs
description: In dit artikel vindt u informatie over de functies en terminologie van Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a38cf4ba6a06dc6e977f9ea168fcf67ce83ff5de
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339979"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Functies en terminologie in azure Event Hubs

Azure Event Hubs is een schaal bare service voor gebeurtenis verwerking waarmee grote hoeveel heden gebeurtenissen en gegevens worden opgenomen en verwerkt, met lage latentie en hoge betrouw baarheid. Zie [Wat is Event hubs?](./event-hubs-about.md) voor een overzicht op hoog niveau.

Dit artikel is gebaseerd op de informatie in het [overzichts artikel](./event-hubs-about.md)en biedt technische en implementatie details over Event hubs onderdelen en functies.

## <a name="namespace"></a>Naamruimte
Een Event Hubs naam ruimte biedt een unieke bereik container, waarnaar wordt verwezen door de [Fully Qualified Domain name](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), waarin u een of meer Event hubs of Kafka-onderwerpen maakt. 

## <a name="event-hubs-for-apache-kafka"></a>Event Hubs voor Apache Kafka

[Deze functie](event-hubs-for-kafka-ecosystem-overview.md) biedt een eind punt waarmee klanten kunnen praten met Event hubs met behulp van het Kafka-protocol. Deze integratie biedt klanten een Kafka-eind punt. Zo kunnen klanten hun bestaande Kafka-toepassingen configureren om te praten met Event Hubs, waardoor ze ook hun eigen Kafka-clusters kunnen uitvoeren. Event Hubs voor Apache Kafka ondersteunt Kafka-Protocol 1,0 en hoger. 

Met deze integratie hoeft u geen Kafka-clusters uit te voeren of ze te beheren met Zookeeper. Op deze manier kunt u ook werken met een aantal van de meest veeleisende functies van Event Hubs zoals vastleggen, automatisch verg Roten en geo-nood herstel.

Met deze integratie kunnen toepassingen zoals spiegel aars of Framework, zoals Kafka, verbinding maken met clusters zonder configuratie wijzigingen. 

## <a name="event-publishers"></a>Gebeurtenisuitgevers

Elke entiteit die gegevens naar een Event Hub verzendt, is een gebeurtenis producent of een *Uitgever van gebeurtenissen*. Gebeurtenis uitgevers kunnen gebeurtenissen publiceren met HTTPS of AMQP 1,0 of Kafka 1,0 of hoger. Gebeurtenisuitgevers gebruiken een Shared Access Signature-token (SAS) om zichzelf te identificeren bij een Event Hub en kunnen een unieke identiteit hebben of een algemene SAS-token gebruiken.

### <a name="publishing-an-event"></a>Een gebeurtenis publiceren

U kunt een gebeurtenis publiceren via AMQP 1,0, Kafka 1,0 (en later) of HTTPS. De Event Hubs-service biedt [rest API](/rest/api/eventhub/) -en [.net](event-hubs-dotnet-standard-getstarted-send.md)-, [Java](event-hubs-java-get-started-send.md)-, [python](event-hubs-python-get-started-send.md)-, [Java script](event-hubs-node-get-started-send.md)-en [Go](event-hubs-go-get-started-send.md) -client bibliotheken voor het publiceren van gebeurtenissen naar een event hub. Voor andere runtimes en platforms kunt u een AMQP 1.0-client gebruiken, zoals [Apache Qpid](https://qpid.apache.org/). 

U kunt gebeurtenissen afzonderlijk of batchgewijs publiceren. Eén publicatie (gebeurtenis gegevens exemplaar) heeft een limiet van 1 MB, ongeacht of het om één gebeurtenis of een batch gaat. Het publiceren van gebeurtenissen die groter zijn dan deze drempel resulteert in een fout. Het is een best practice dat uitgevers geen rekening hoeven te houden met partities binnen de Event Hub en om alleen een *partitie sleutel* op te geven (geïntroduceerd in de volgende sectie) of hun identiteit via hun SAS-token.

De keuze om AMQP of HTTPS te gebruiken, geldt specifiek voor het gebruiksscenario. AMQP vereist de inrichting van een permanente bidirectionele socket naast Transport Layer Security (TLS) of SSL/TLS. AMQP heeft hogere netwerk kosten bij het initialiseren van de sessie, maar HTTPS vereist extra TLS-overhead voor elke aanvraag. AMQP biedt betere prestaties voor regelmatige uitgevers.

![Partitie sleutels](./media/event-hubs-features/partition_keys.png)

Event Hubs zorgt ervoor dat alle gebeurtenissen met een partitiesleutelwaarde op volgorde en aan dezelfde partitie worden geleverd. De identiteit van de uitgever en de waarde van de partitiesleutel moeten overeenkomen als er partitiesleutels met uitgeversbeleid worden gebruikt. Anders treedt er een fout op.

### <a name="publisher-policy"></a>Uitgeversbeleid

In Event Hubs kunt u gebeurtenisuitgevers nauwkeurig beheren met behulp van *uitgeversbeleid*. Uitgeversbeleid bestaat uit runtimefuncties die zijn ontworpen om grote aantallen onafhankelijke gebeurtenisuitgevers mogelijk te maken. Als u uitgeversbeleid implementeert, gebruikt elke uitgever zijn eigen unieke id bij het publiceren van gebeurtenissen naar een Event Hub. Hierbij wordt het volgende mechanisme gebruikt:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>
```

Het is niet nodig om van tevoren uitgeversnamen te maken. De namen moeten echter wel overeenkomen met het SAS-token dat wordt gebruikt wanneer een gebeurtenis wordt gepubliceerd. Hiermee wordt voor onafhankelijke uitgeversidentiteiten gezorgd. Als u uitgeversbeleid gebruikt, is de waarde **Partitiesleutel** ingesteld op de naam van de uitgever. Voor een goede werking moeten deze waarden overeenkomen.

## <a name="capture"></a>Vastleggen

Met [Event hubs Capture](event-hubs-capture-overview.md) kunt u automatisch de streaminggegevens in Event hubs vastleggen en opslaan in een Blob Storage-account of een Azure data Lake-service account. U kunt vastleggen inschakelen vanuit het Azure Portal en een minimale grootte en tijd venster opgeven om de vastleg ging uit te voeren. Met Event Hubs Capture geeft u uw eigen Azure Blob Storage-account en-container of een Azure Data Lake service account op. deze wordt gebruikt om de vastgelegde gegevens op te slaan. Vastgelegde gegevens worden geschreven in de Apache Avro-indeling.

## <a name="partitions"></a>Partities
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]


## <a name="sas-tokens"></a>SAS-tokens

Event Hubs maakt gebruik van *hand tekeningen voor gedeelde toegang*, die beschikbaar zijn op het niveau van de naam ruimte en het event hub. Een SAS-token wordt gegenereerd uit een SAS-sleutel en is een SHA-hash of URL. gecodeerd in een specifieke indeling. Event Hubs kan de hash opnieuw genereren met de naam van de sleutel (het beleid) en de token, en op deze manier de afzender verifiëren. Normaal gesp roken worden SAS-tokens voor gebeurtenis uitgevers alleen gemaakt met de machtiging **verzenden** voor een specifieke Event hub. Dit URL-mechanisme met SAS-token vormt de basis voor de uitgeversidentificatie die in het uitgeversbeleid wordt geïntroduceerd. Zie [Shared Access Signature-verificatie met Service Bus](../service-bus-messaging/service-bus-sas.md) voor meer informatie over werken met SAS.

## <a name="event-consumers"></a>Gebeurtenisconsumers

Elke entiteit die gebeurtenis gegevens van een Event Hub leest, is een *event consumer*. Alle Event Hubs-consumers maken verbinding via de AMQP 1.0-sessie, waarin gebeurtenissen worden geleverd zodra deze beschikbaar komen. De client hoeft de beschikbaarheid van de gegevens niet te peilen.

### <a name="consumer-groups"></a>Consumentengroepen

Het mechanisme voor publiceren/abonneren van Event Hubs wordt ingeschakeld via *consumenten groepen*. Een consumergroep is een weergave (status, positie of offset) van een volledige Event Hub. Consumergroepen maken het mogelijk dat meerdere consumerende toepassingen beschikken over een afzonderlijke weergave van de gebeurtenisstroom. De toepassingen kunnen de stroom onafhankelijk, in hun eigen tempo en met hun eigen offsets lezen.

In een architectuur waarin de stroom wordt verwerkt, is elke downstream-toepassing gelijk aan een consumergroep. Als u gebeurtenisgegevens naar de langetermijnopslag wilt schrijven, is de schrijftoepassing die hiervoor wordt gebruikt, een consumergroep. De complexe verwerking van gebeurtenissen kan vervolgens worden uitgevoerd door een andere, afzonderlijke consumergroep. U hebt alleen toegang tot partities via een consumergroep. Een Event Hub bevat altijd een standaardconsumergroep. Voor een Event Hub van het type Standaard kunt u maximaal 20 consumergroepen maken.

Er kunnen Maxi maal vijf gelijktijdige lezers op een partitie per consumenten groep zijn. **het is echter raadzaam dat er slechts één actieve ontvanger is op een partitie per Consumer groep**. Binnen één partitie ontvangt elke lezer alle berichten. Als u meerdere lezers op dezelfde partitie hebt, verwerkt u dubbele berichten. U moet dit in uw code afhandelen. Dit is mogelijk niet gelastig. Het is echter een geldige benadering in sommige scenario's.

Sommige clients die door de Azure-Sdk's worden aangeboden, zijn intelligente consumenten agenten die automatisch de details beheren om ervoor te zorgen dat elke partitie één lezer heeft en dat alle partities voor een Event Hub worden gelezen. Hierdoor kan uw code zich richten op het verwerken van de gebeurtenissen die worden gelezen van de Event Hub zodat er veel details van de partities kunnen worden genegeerd. Zie [verbinding maken met een partitie](#connect-to-a-partition)voor meer informatie.

In de volgende voor beelden ziet u de URI-Conventie voor de Consumer groep:

```http
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>
```

In de volgende afbeelding ziet u de architectuur voor de verwerking van stromen van Event Hubs:

![Event Hubs architectuur](./media/event-hubs-features/event_hubs_architecture.png)

### <a name="stream-offsets"></a>Stroom-offsets

Een *Offset* is de positie van een gebeurtenis binnen een partitie. U kunt een offset beschouwen als een clientcursor. De offset is een bytenummering van de gebeurtenis. Met deze offset kan een gebeurtenisconsumer (lezer) een punt in de gebeurtenisstroom opgeven vanwaaruit begonnen moet worden met het lezen van gebeurtenissen. U kunt de offset opgeven als een tijdstempel of als een offsetwaarde. Consumers zijn zelf verantwoordelijk voor het opslaan van hun eigen offsetwaarden buiten de Event Hubs-service. Binnen een partitie bevat elke gebeurtenis een offset.

![Partitie-offset](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Controlepunten maken

*Het plaatsen van controlepunten* is een proces waarbij lezers hun positie binnen de gebeurtenisvolgorde van een partitie markeren of vastleggen. Het plaatsen van controlepunten is de verantwoordelijkheid van de consumer en vindt plaats per partitie binnen een consumergroep. Deze verantwoordelijkheid houdt in dat elke partitielezer voor elke consumergroep de huidige positie in de gebeurtenisstroom moet bijhouden en de service kan informeren wanneer de gegevensstroom is voltooid.

Als een lezer van een partitie is losgekoppeld en er vervolgens weer verbinding wordt gemaakt, begint het lezen bij het controlepunt dat eerder is verzonden door de laatste lezer van de betreffende partitie in de consumergroep. Wanneer de lezer verbinding maakt, wordt de offset aan de Event Hub door gegeven om de locatie op te geven waar u wilt beginnen met lezen. Op deze manier kunt u het plaatsen van controlepunten gebruiken om gebeurtenissen te markeren als 'voltooid' door downstream-toepassingen. Bovendien beschikt u met controlepunten over tolerantie bij een failover tussen lezers die op verschillende apparaten worden uitgevoerd. Het is mogelijk om terug te keren naar de oudere gegevens door een lagere offset van dit controlepuntproces op te geven. Via dit mechanisme zorgt het plaatsen van controlepunten voor failover-tolerantie en voor herhaling van gebeurtenisstromen.

> [!NOTE]
> Als u Azure Blob Storage gebruikt als controlepunt opslag in een omgeving die ondersteuning biedt voor een andere versie van de Storage BLOB SDK dan die welke meestal beschikbaar zijn op Azure, moet u code gebruiken om de API-versie van de opslag service te wijzigen in de specifieke versie die wordt ondersteund door die omgeving. Als u bijvoorbeeld [Event hubs uitvoert op een Azure stack hub versie 2002](/azure-stack/user/event-hubs-overview), is de hoogste beschik bare versie van de opslag service versie 2017-11-09. In dit geval moet u code gebruiken om de API-versie van de Storage-service te richten op 2017-11-09. Zie voor een voor beeld van het richten op een specifieke opslag-API-versie de volgende voor beelden op GitHub: 
> - [.Net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [Java script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) of  [type script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

### <a name="common-consumer-tasks"></a>Algemene taken voor consumers

Alle Event Hubs consumenten maken verbinding via een AMQP 1,0-sessie, een status bewust bidirectionele communicatie kanaal. Elke partitie heeft een AMQP 1.0-sessie die het mogelijk maakt partitiespecifieke gebeurtenissen te transporteren.

#### <a name="connect-to-a-partition"></a>Verbinding maken met een partitie

Wanneer u verbinding maakt met partities, is het gebruikelijk om een lease mechanisme te gebruiken voor het coördineren van lezers verbindingen met specifieke partities. Op deze manier is het mogelijk dat elke partitie in een Consumer groep slechts één actieve lezer heeft. Controle punten, leasing en beheer van lezers worden vereenvoudigd door gebruik te maken van de clients in de Event Hubs Sdk's, die fungeren als intelligente consumenten agenten. Deze zijn:

- De [EventProcessorClient](/dotnet/api/azure.messaging.eventhubs.eventprocessorclient) voor .net
- De [EventProcessorClient](/java/api/com.azure.messaging.eventhubs.eventprocessorclient) voor Java
- De [EventHubConsumerClient](/python/api/azure-eventhub/azure.eventhub.aio.eventhubconsumerclient) voor python
- De [EventHubConsumerClient](/javascript/api/@azure/event-hubs/eventhubconsumerclient) voor Java script/type script

#### <a name="read-events"></a>Gebeurtenissen lezen

Nadat er voor een specifieke partitie een AMQP 1.0-sessie en -koppeling zijn geopend, worden de gebeurtenissen door de Event Hubs-service aan de AMQP 1.0-client geleverd. Dit leveringsmechanisme maakt hogere doorvoer en lagere latentie mogelijk dan pull-mechanismen zoals HTTP GET. Tijdens het verzenden van gebeurtenissen naar de client wordt elk gebeurtenisgegeven voorzien van belangrijke metagegevens, zoals de offset en het volgnummer. Deze worden gebruikt om het plaatsen van controlepunten in de gebeurtenisvolgorde mogelijk te maken.

Gebeurtenisgegevens:
* Offset
* Volgnummer
* Hoofdtekst
* Gebruikerseigenschappen
* Systeemeigenschappen

U bent verantwoordelijk voor het beheer van de offset.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Event Hubs gaat u naar de volgende koppelingen:

- Aan de slag met Event Hubs
    - [.NET](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Programmeer handleiding voor Event Hubs](event-hubs-programming-guide.md)
* [Beschikbaarheid en consistentie in Event Hubs](event-hubs-availability-and-consistency.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Event Hubs-voor beelden](event-hubs-samples.md)