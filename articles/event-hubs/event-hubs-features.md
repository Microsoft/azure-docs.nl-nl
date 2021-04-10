---
title: Overzicht van functies-Azure Event Hubs | Microsoft Docs
description: In dit artikel vindt u informatie over de functies en terminologie van Azure Event Hubs.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: da59d62cb7060389ea94b3af5e6f66a4b6347d7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602618"
---
# <a name="features-and-terminology-in-azure-event-hubs"></a>Functies en terminologie in azure Event Hubs

Azure Event Hubs is een schaal bare service voor gebeurtenis verwerking waarmee grote hoeveel heden gebeurtenissen en gegevens worden opgenomen en verwerkt, met lage latentie en hoge betrouw baarheid. Zie [Wat is Event hubs?](./event-hubs-about.md) voor een overzicht op hoog niveau.

Dit artikel is gebaseerd op de informatie in het [overzichts artikel](./event-hubs-about.md)en biedt technische en implementatie details over Event hubs onderdelen en functies.

> [!TIP]
> [De protocol ondersteuning voor **Apache Kafka** -clients](event-hubs-for-kafka-ecosystem-overview.md)  (versies >= 1,0) biedt netwerk eindpunten waarmee toepassingen die gebruikmaken van Apache Kafka met een wille keurige client Event hubs kunnen gebruiken. De meeste bestaande Kafka-toepassingen kunnen eenvoudigweg opnieuw worden geconfigureerd zodat ze verwijzen naar een event hub-naam ruimte in plaats van een Boots trap server van Kafka-cluster. 
>
>Vanuit het perspectief van de kosten, operationele inspanning en betrouw baarheid is Azure Event Hubs een geweldig alternatief voor het implementeren en gebruiken van uw eigen Kafka-en Zookeeper-clusters en op Kafka-as-a-service aanbiedingen die geen eigendom zijn van Azure. 
>
> Naast het verkrijgen van dezelfde kern functionaliteit als van de Apache Kafka Broker, hebt u ook toegang tot Azure Event hub-functies zoals automatische batch verwerking en archivering via [Event hubs vastleggen](event-hubs-capture-overview.md), automatisch schalen en balanceren, herstel na nood gevallen, kosten neutrale beschikbaarheids zone ondersteuning, flexibele en veilige netwerk integratie en ondersteuning voor meerdere protocollen, inclusief het protocol firewall vriendelijk AMQP-over-websockets.


## <a name="namespace"></a>Naamruimte
Een Event Hubs naam ruimte biedt DNS Integrated Network-eind punten en een reeks toegangs beheer-en netwerk functies, zoals [IP-filtering](event-hubs-ip-filtering.md), het [eind punt van een virtuele netwerk service](event-hubs-service-endpoints.md)en een [persoonlijke koppeling](private-link-service.md) , en is de beheer container voor een van meerdere Event hub-instanties (of onderwerpen in Kafka jargon).

## <a name="event-publishers"></a>Gebeurtenisuitgevers

Elke entiteit die gegevens naar een event hub verzendt, is een *gebeurtenis Uitgever* (als synoniem gebruikt gebruikt bij *gebeurtenis producer*). Gebeurtenis uitgevers kunnen gebeurtenissen publiceren met HTTPS of AMQP 1,0 of het Kafka-protocol. Gebeurtenis uitgevers gebruiken Azure Active Directory op basis van autorisatie met door OAuth2 uitgegeven JWT-tokens of een SAS-token (Event hub-specific Shared Access Signature) om toegang te krijgen tot de publicatie.

### <a name="publishing-an-event"></a>Een gebeurtenis publiceren

U kunt een gebeurtenis publiceren via AMQP 1,0, het Kafka-protocol of HTTPS. De Event Hubs-service biedt [rest API](/rest/api/eventhub/) -en [.net](event-hubs-dotnet-standard-getstarted-send.md)-, [Java](event-hubs-java-get-started-send.md)-, [python](event-hubs-python-get-started-send.md)-, [Java script](event-hubs-node-get-started-send.md)-en [Go](event-hubs-go-get-started-send.md) -client bibliotheken voor het publiceren van gebeurtenissen naar een event hub. Voor andere runtimes en platforms kunt u een AMQP 1.0-client gebruiken, zoals [Apache Qpid](https://qpid.apache.org/). 

De keuze om AMQP of HTTPS te gebruiken, geldt specifiek voor het gebruiksscenario. AMQP vereist de inrichting van een permanente bidirectionele socket naast Transport Layer Security (TLS) of SSL/TLS. AMQP heeft hogere netwerk kosten bij het initialiseren van de sessie, maar HTTPS vereist extra TLS-overhead voor elke aanvraag. AMQP heeft aanzienlijk hogere prestaties voor frequente uitgevers en kan veel vertragings tijden in beslag nemen bij het gebruik van asynchrone publicatie code.

U kunt gebeurtenissen afzonderlijk of in batch publiceren. Eén publicatie heeft een limiet van 1 MB, ongeacht of het om één gebeurtenis of een batch gaat. Het publiceren van gebeurtenissen die groter zijn dan deze drempel waarde, wordt geweigerd. 

Event Hubs door Voer wordt geschaald met behulp van partities en doorvoer eenheid toewijzingen (zie hieronder). Het is een best practice dat uitgevers niet op de hoogte blijven van het specifieke partitie model dat voor een event hub is gekozen en om alleen een *partitie sleutel* op te geven die wordt gebruikt voor het consistent toewijzen van gerelateerde gebeurtenissen aan dezelfde partitie.

![Partitie sleutels](./media/event-hubs-features/partition_keys.png)

Event Hubs zorgt ervoor dat alle gebeurtenissen die een partitie sleutel waarde delen samen worden opgeslagen en worden geleverd in volg orde van aankomst. De identiteit van de uitgever en de waarde van de partitiesleutel moeten overeenkomen als er partitiesleutels met uitgeversbeleid worden gebruikt. Anders treedt er een fout op.

### <a name="event-retention"></a>Bewaren van gebeurtenissen

Gepubliceerde gebeurtenissen worden verwijderd uit een event hub op basis van een configureerbaar Bewaar beleid op basis van tijd. Hier volgen enkele belang rijke punten:

- De **standaard** waarde en de **kortst** mogelijke Bewaar periode is **1 dag (24 uur)**.
- Voor Event Hubs **Standard** geldt een maximale Bewaar periode van **7 dagen**. 
- Voor Event Hubs **toegewezen**, is de maximale bewaar periode **90 dagen**.
- Als u de Bewaar periode wijzigt, is dit van toepassing op alle berichten, met inbegrip van berichten die al in de Event Hub staan. 

Event Hubs bewaart gebeurtenissen voor een geconfigureerde bewaartijd die wordt toegepast op het niveau van alle partities. Gebeurtenissen worden automatisch verwijderd wanneer de retentieperiode is bereikt. Als u een retentieperiode van één dag opgeeft, wordt de gebeurtenis precies 24 uur nadat deze is geaccepteerd onbeschikbaar. U kunt gebeurtenissen niet expliciet verwijderen. 

Als u gebeurtenissen wilt archiveren na de toegestane retentieperiode, kunt u deze [automatisch laten opslaan in Azure Storage of Azure Data Lake door de functie Event Hubs Capture in te schakelen](event-hubs-capture-overview.md). Als u dergelijke diepe archieven wilt doorzoeken of analyseren, kunt u ze [gemakkelijk importeren in Azure Synapse](store-captured-data-data-warehouse.md) of andere vergelijkbare opslagplaatsen en analyseplatforms. 

De limiet op de retentieperiode voor Event Hubs is om te voorkomen dat grote hoeveelheden historische klantgegevens worden vastgelegd in een archief dat alleen wordt geïndexeerd per timestamp en alleen sequentiële toegang toestaat. De architectuurfilosofie is dat voor historische gegevens uitgebreidere indexering en directere toegang nodig is dan de real-time interface voor gebeurtenissen die Event Hubs of Kafka bieden. Engines voor gebeurtenissenstromen zijn niet geschikt om te fungeren als data lakes of als langetermijnarchieven voor gebeurtenisbronnen. 
 

> [!NOTE]
> Event Hubs is een real-time engine voor gebeurtenis stroom en is niet ontworpen om te worden gebruikt in plaats van een Data Base en/of als een permanente Store voor oneindige gebeurtenis stromen. 
> 
> Hoe dieper de geschiedenis van een gebeurtenis stroom wordt opgehaald, des te meer zult u hulp indexen nodig hebben om een bepaald historisch segment van een bepaalde stroom te vinden. Inspectie van nettoladingen en indexering van gebeurtenissen bevindt zich niet binnen het functie bereik van Event Hubs (of Apache Kafka). Data bases en gespecialiseerde analyse archieven en-engines, zoals [Azure data Lake Store](../data-lake-store/data-lake-store-overview.md), [Azure data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) en [Azure Synapse](../synapse-analytics/overview-what-is.md) , zijn daarom uiterst beter geschikt voor het opslaan van historische gebeurtenissen.
>
> [Event hubs Capture](event-hubs-capture-overview.md) kan rechtstreeks worden geïntegreerd met Azure Blob Storage en Azure data Lake Storage en met deze integratie kunnen ook [stromen rechtstreeks in azure Synapse worden geplaatst](store-captured-data-data-warehouse.md).
>
> Als u het patroon [gebeurtenis bronnen](/azure/architecture/patterns/event-sourcing) wilt gebruiken voor uw toepassing, moet u uw strategie voor moment opnamen uitlijnen met de Bewaar limieten van Event hubs. Zorg dat u geen gerealiseerde weer gaven van onbewerkte gebeurtenissen opnieuw bouwt, te beginnen bij het begin van de tijd. Als uw toepassing al enige tijd en goed wordt gebruikt, zou u een surely van een dergelijke strategie kunnen maken, en uw projectie opbouwt het verloop tot en met jaren wijzigings gebeurtenissen tijdens het opsporen van de nieuwste en doorlopende wijzigingen. 


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

![Event Hubs architectuur](./media/event-hubs-about/event_hubs_architecture.svg)

### <a name="stream-offsets"></a>Stroom-offsets

Een *Offset* is de positie van een gebeurtenis binnen een partitie. U kunt een offset beschouwen als een clientcursor. De offset is een bytenummering van de gebeurtenis. Met deze offset kan een gebeurtenisconsumer (lezer) een punt in de gebeurtenisstroom opgeven vanwaaruit begonnen moet worden met het lezen van gebeurtenissen. U kunt de offset opgeven als een tijdstempel of als een offsetwaarde. Consumers zijn zelf verantwoordelijk voor het opslaan van hun eigen offsetwaarden buiten de Event Hubs-service. Binnen een partitie bevat elke gebeurtenis een offset.

![Partitie-offset](./media/event-hubs-features/partition_offset.png)

### <a name="checkpointing"></a>Controlepunten maken

*Het plaatsen van controlepunten* is een proces waarbij lezers hun positie binnen de gebeurtenisvolgorde van een partitie markeren of vastleggen. Het plaatsen van controlepunten is de verantwoordelijkheid van de consumer en vindt plaats per partitie binnen een consumergroep. Deze verantwoordelijkheid houdt in dat elke partitielezer voor elke consumergroep de huidige positie in de gebeurtenisstroom moet bijhouden en de service kan informeren wanneer de gegevensstroom is voltooid.

Als een lezer van een partitie is losgekoppeld en er vervolgens weer verbinding wordt gemaakt, begint het lezen bij het controlepunt dat eerder is verzonden door de laatste lezer van de betreffende partitie in de consumergroep. Wanneer de lezer verbinding maakt, wordt de offset aan de Event Hub door gegeven om de locatie op te geven waar u wilt beginnen met lezen. Op deze manier kunt u het plaatsen van controlepunten gebruiken om gebeurtenissen te markeren als 'voltooid' door downstream-toepassingen. Bovendien beschikt u met controlepunten over tolerantie bij een failover tussen lezers die op verschillende apparaten worden uitgevoerd. Het is mogelijk om terug te keren naar de oudere gegevens door een lagere offset van dit controlepuntproces op te geven. Via dit mechanisme zorgt het plaatsen van controlepunten voor failover-tolerantie en voor herhaling van gebeurtenisstromen.

> [!IMPORTANT]
> Offsets worden verzorgd door de Event Hubs-service. Het is de verantwoordelijkheid van de gebruiker om een controle punt te maken wanneer gebeurtenissen worden verwerkt.

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
    - [JavaScript](event-hubs-node-get-started-send.md)
* [Programmeer handleiding voor Event Hubs](event-hubs-programming-guide.md)
* [Beschikbaarheid en consistentie in Event Hubs](event-hubs-availability-and-consistency.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
* [Event Hubs-voor beelden](event-hubs-samples.md)