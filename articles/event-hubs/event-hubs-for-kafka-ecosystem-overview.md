---
title: Event Hub van Apache Kafka app gebruiken-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over Apache Kafka ondersteuning door Azure Event Hubs.
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: b0f0da76bba68f8a66695700d530e871cbd35e3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97861331"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Azure Event Hubs van Apache Kafka toepassingen gebruiken
Event Hubs biedt een eind punt dat compatibel is met de Apache Kafka® producer-en Consumer-Api's die kunnen worden gebruikt door de meeste bestaande Apache Kafka-client toepassingen als alternatief voor het uitvoeren van uw eigen Apache Kafka-cluster. Event Hubs ondersteunt de clients van de producent-en consumenten-Api's van Apache Kafka op versie 1,0 en hoger.

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Wat biedt Event Hubs voor Kafka?

De functie Event Hubs voor Apache Kafka voorziet in een Protocolsubstatus boven op Azure Event Hubs die compatibel is met het protocol voor Apache Kafka clients die zijn gebouwd voor Apache Kafka Server versie 1,0 en hoger en die ondersteuning biedt voor zowel lezen van als schrijven naar Event Hubs, die gelijkwaardig zijn aan Apache Kafka onderwerpen. 

U kunt vaak Event Hubs het Kafka-eind punt van uw toepassingen gebruiken zonder dat de code wordt gewijzigd in vergelijking met de bestaande Kafka-instellingen en de configuratie alleen wijzigen: werk de connection string bij in configuraties zodat deze verwijzen naar het Kafka-eind punt dat door uw Event Hub wordt weer gegeven in plaats van naar uw Kafka-cluster te verwijzen. Vervolgens kunt u gebeurtenissen vanuit uw toepassingen die gebruikmaken van het Kafka-protocol in Event Hubs starten. 

Kafka en Event Hubs zijn zeer vergelijkbaar: ze zijn beide gepartitioneerde logboeken die zijn gemaakt voor streaminggegevens, waarbij de client bepaalt welk deel van het Inge sloten logboek dat hij wil lezen. In de volgende tabel worden concepten tussen Kafka en Event Hubs toegewezen.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Conceptuele toewijzing van Kafka en Event hub

| Kafka-concept | Event Hubs concept|
| --- | --- |
| Cluster | Naamruimte |
| Onderwerp | Event Hub |
| Partitie | Partitie|
| Consumenten groep | Consumenten groep |
| Offset | Offset|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>De belangrijkste verschillen tussen Apache Kafka en Event Hubs

Hoewel [Apache Kafka](https://kafka.apache.org/) software is die u doorgaans moet installeren en uitvoeren, is Event hubs een volledig beheerde Cloud service. Er zijn geen servers, schijven of netwerken om te beheren en te controleren en er kunnen geen Brokers worden geconfigureerd of niet meer. U maakt een naam ruimte, een eind punt met een Fully Qualified Domain Name, en vervolgens maakt u Event Hubs (onderwerpen) in die naam ruimte. 

Zie [Event hubs-functies](event-hubs-features.md#namespace)voor meer informatie over Event hubs en naam ruimten. Als Cloud service gebruikt Event Hubs één stabiel virtueel IP-adres als het eind punt, zodat clients niet hoeven te weten over de Brokers of computers binnen een cluster. Hoewel Event Hubs hetzelfde protocol implementeert, betekent dit verschil dat alle Kafka-verkeer voor alle partities wordt zoals verwacht doorgestuurd via dit ene eind punt in plaats van dat er firewall toegang vereist is voor alle Brokers van een cluster.   

De schaal van Event Hubs wordt bepaald door het aantal doorvoer eenheden dat u aanschaft, waarbij elke doorvoer eenheid u recht geeft op 1 MB per seconde, of 1000 gebeurtenissen per seconde van binnenkomend en twee keer dat volume in afwachting is. Event Hubs kunt doorvoer eenheden automatisch schalen wanneer u de doorvoer limiet bereikt door de functie voor [automatisch verg Roten](event-hubs-auto-inflate.md) te gebruiken. deze functie werkt ook met de ondersteuning van het Apache Kafka-protocol.  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Is Apache Kafka de juiste oplossing voor uw werk belasting?

Van het bouwen van toepassingen die gebruikmaken van Apache Kafka, is het ook handig om te begrijpen dat Azure Event Hubs deel uitmaakt van een dienst vloot die ook [Azure service bus](../service-bus-messaging/service-bus-messaging-overview.md)bevat en [Azure Event grid](../event-grid/overview.md). 

Hoewel sommige providers van commerciële distributies van Apache Kafka kunnen Voorst Ellen dat Apache Kafka een eenmalige winkel is voor al uw behoeften van het berichten platform, is de realiteit dat Apache Kafka niet implementeert, bijvoorbeeld het wachtrij patroon voor [concurrerende gebruikers](/azure/architecture/patterns/competing-consumers) , biedt geen ondersteuning voor  [publiceren-abonneren](/azure/architecture/patterns/publisher-subscriber) op een niveau waarmee abonnees toegang hebben tot de inkomende berichten op basis van door de server geëvalueerde regels, met uitzonde ring van gewone offsets. het heeft geen faciliteiten om de levens cyclus van een taak die wordt gestart door een bericht of sidelining fout berichten te traceren in een wachtrij met onbestelbare meldingen.

Als u wilt weten wat de verschillen zijn tussen patronen en welk patroon het meest geschikt is voor welke service, raadpleegt u de [Opties voor asynchrone berichten in azure](/azure/architecture/guide/technology-choices/messaging) -richt lijnen. Als Apache Kafka gebruiker is het mogelijk dat de communicatie paden die u tot nu toe hebt gerealiseerd met Kafka, kunnen worden gerealiseerd met veel minder eenvoudige complexiteit en nog krachtige mogelijkheden met behulp van Event Grid of Service Bus. 

Als u specifieke functies van Apache Kafka nodig hebt die niet beschikbaar zijn via de Event Hubs voor Apache Kafka-interface of als uw implementatie patroon de [Event hubs quota's](event-hubs-quotas.md)overschrijdt, kunt u ook een [systeem eigen Apache Kafka-cluster uitvoeren in azure HDInsight](../hdinsight/kafka/apache-kafka-introduction.md).  

## <a name="security-and-authentication"></a>Verificatie en beveiliging
Elke keer dat u gebeurtenissen publiceert of gebruikt vanuit een Event Hubs voor Kafka, probeert de client toegang te krijgen tot de Event Hubs resources. U wilt er zeker van zijn dat de resources worden geopend met behulp van een geautoriseerde entiteit. Wanneer u Apache Kafka-protocol met uw clients gebruikt, kunt u uw configuratie voor verificatie en versleuteling instellen met behulp van de SASL-mechanismen. Bij het gebruik van Event Hubs voor Kafka is TLS-code ring vereist (omdat alle gegevens in de overdracht met Event Hubs TLS-versleuteld zijn). U kunt dit doen door de SASL_SSL optie in uw configuratie bestand op te geven. 

Azure Event Hubs biedt meerdere opties voor het machtigen van toegang tot uw beveiligde bronnen. 

- OAuth 2.0
- Shared Access Signature (SAS)

#### <a name="oauth-20"></a>OAuth 2.0
Event Hubs integreert met Azure Active Directory (Azure AD), dat een door **OAuth 2,0** compatibele gecentraliseerde autorisatie server biedt. Met Azure AD kunt u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om aan uw client identiteiten verfijnde machtigingen te verlenen. U kunt deze functie met uw Kafka-clients gebruiken door **SASL_SSL** op te geven voor het protocol en  **OAUTHBEARER** voor het mechanisme. Zie [toegang verlenen met Azure AD](authorize-access-azure-active-directory.md)voor meer informatie over Azure-rollen en-niveaus voor het bereik van de toegang.

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Shared Access Signature (SAS)
Event Hubs biedt ook de **Shared Access signatures (SAS)** voor gedelegeerde toegang tot Event hubs voor Kafka-resources. Het machtigen van toegang met behulp van een op tokens gebaseerd OAuth 2,0-mechanisme biedt een superieure beveiliging en gebruiks vriendelijk gebruik van SAS. De ingebouwde rollen kunnen ook de nood zaak voor autorisatie op basis van een toegangs beheer lijst elimineren, die door de gebruiker moet worden onderhouden en beheerd. U kunt deze functie met uw Kafka-clients gebruiken door **SASL_SSL** op te geven voor het **protocol en voor** het mechanisme. 

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Vervang `{YOUR.EVENTHUBS.CONNECTION.STRING}` door de verbindingsreeks voor uw Event Hubs-naamruimte. Zie [Een verbindingsreeks voor Event Hubs ophalen](event-hubs-get-connection-string.md) voor instructies voor het ophalen van de verbindingsreeks. Hier volgt een voorbeeldconfiguratie: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

> [!NOTE]
> Bij het gebruik van SAS-verificatie met Kafka-clients worden de verbinding met de ingestelde verbindingen niet verbroken wanneer de SAS-sleutel opnieuw wordt gegenereerd. 


#### <a name="samples"></a>Voorbeelden 
Voor een **zelf studie** met stapsgewijze instructies voor het maken van een event hub en het openen met behulp van SAS of OAuth, raadpleegt u [Quick Start: streamen met Event hubs met behulp van het Kafka-protocol](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Voor meer voor **beelden** die laten zien hoe u OAuth gebruikt met Event hubs voor Kafka, raadpleegt u voor [beelden op github](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features"></a>Andere Event Hubs functies 

De functie Event Hubs voor Apache Kafka is een van de drie protocollen die gelijktijdig beschikbaar zijn op Azure Event Hubs, waarbij HTTP en AMQP worden aangevuld. U kunt schrijven met een van deze protocollen en met elkaar lezen, zodat uw huidige Apache Kafka producenten kunnen blijven publiceren via Apache Kafka, maar uw lezer kan profiteren van de systeem eigen integratie met Event Hubs ' AMQP-interface, zoals Azure Stream Analytics of Azure Functions. U kunt Azure-Event Hubs in een hand omkeer integreren in AMQP-routerings netwerken als eind punt en de gegevens worden gelezen via Apache Kafka integraties.  

Daarnaast zijn er Event Hubs functies zoals [vastleggen](event-hubs-capture-overview.md), waardoor een uiterst efficiënte, langdurige archivering via Azure Blob Storage en Azure data Lake Storage mogelijk is en [geo-herstel na nood gevallen](event-hubs-geo-dr.md) ook werkt met de Event hubs voor de functie Kafka.

## <a name="apache-kafka-feature-differences"></a>Verschillen in Apache Kafka onderdelen 

Het doel van Event Hubs voor Apache Kafka is om toegang te bieden tot de mogelijkheden van de Azure Event hub voor toepassingen die zijn vergrendeld in de API Apache Kafka en anders moeten worden ondersteund door een Apache Kafka cluster. 

Zoals [hierboven](#is-apache-kafka-the-right-solution-for-your-workload)is uitgelegd, biedt de Azure Messa ging-vloot een rijke en robuuste dekking voor een groot aantal berichten scenario's. Hoewel de volgende functies momenteel niet worden ondersteund via event hubs-ondersteuning voor de Apache Kafka-API, wordt uitgelegd waar en hoe de gewenste mogelijkheid beschikbaar is.

### <a name="transactions"></a>Transacties

[Azure service bus](../service-bus-messaging/service-bus-transactions.md) heeft robuuste transactie ondersteuning waarmee berichten en sessies kunnen worden ontvangen en afgehandeld tijdens het verzenden van uitgaande berichten die voortkomen uit bericht verwerking naar meerdere doel entiteiten onder de consistentie beveiliging van een trans actie. De functieset biedt niet alleen een eenmalige verwerking van elk bericht in een reeks, maar vermijdt het risico dat een andere consument per ongeluk dezelfde berichten verwerkt als het geval is bij Apache Kafka. Service Bus is de aanbevolen Service voor werk belastingen voor transactionele berichten.

### <a name="compression"></a>Compressie

De [compressie](https://cwiki.apache.org/confluence/display/KAFKA/Compression) functie aan client zijde van Apache Kafka comprimeert een batch van meerdere berichten in één bericht aan de producent en decomprimeert de batch aan de gebruiker. De Apache Kafka Broker behandelt de batch als een speciaal bericht.

Deze functie is fundamenteel op conflicteert met Azure Event Hubs ' multi-protocol model, waarmee berichten, zelfs die in batches worden verzonden, afzonderlijk kunnen worden opgehaald uit de Broker en via een protocol. 

De payload van een event hub-gebeurtenis is een byte stroom en de inhoud kan worden gecomprimeerd met een algoritme van uw keuze. De Apache Avro encoding-indeling ondersteunt compressie systeem eigen.

### <a name="log-compaction"></a>Logboek compressie

Apache Kafka logboek compressie is een functie waarmee alle sleutels behalve de laatste record van elke sleutel uit een partitie kunnen worden verwijderd, waardoor een Apache Kafka onderwerp effectief wordt omgezet in een sleutel waarde-archief waarbij de laatste toegevoegde waarde het vorige overschrijft. Deze functie is momenteel niet geïmplementeerd door Azure Event Hubs. Het sleutel-value Store-patroon, zelfs met frequente updates, is veel beter ondersteund door database services zoals [Azure Cosmos DB](../cosmos-db/introduction.md). Raadpleeg het onderwerp [logboek projectie](event-hubs-federation-overview.md#log-projections) in de Event hubs Federation-richt lijnen voor meer informatie. 

### <a name="kafka-streams"></a>Kafka stromen

Kafka streams is een client bibliotheek voor stream Analytics die deel uitmaakt van het Apache Kafka open source-project, maar is gescheiden van de Apache Kafka event stream Broker. 

De meest voorkomende reden waarom Azure Event Hubs klanten vragen om ondersteuning van Kafka-streams is omdat ze geïnteresseerd zijn in het ksqlDB-product. ' ksqlDB ' is een specifiek gedeeld bron project dat een [licentie](https://github.com/confluentinc/ksql/blob/master/LICENSE) heeft, zoals een leverancier ' die software-as-a-service, platform-as-a-service, Infrastructure-as-a-service of andere vergelijk bare onlineservices die concurreren met confluente producten of services, mag gebruiken of bieden ' ksqlDB-ondersteuning. Nagenoeg, als u ksqlDB gebruikt, moet u Kafka zelf gebruiken of moet u de Cloud aanbiedingen van Confluent gebruiken. De licentie voorwaarden kunnen ook van invloed zijn op Azure-klanten die services aanbieden voor een doel einde dat door de licentie wordt uitgesloten.

Zelfstandig en zonder ksqlDB hebben Kafka-streams minder mogelijkheden dan veel alternatieve frameworks en services, waarvan de meeste een ingebouwde streaming SQL-interface hebben, en allemaal zijn geïntegreerd met Azure Event Hubs:

- [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)
- [Azure Synapse Analytics (via Event Hubs Capture)](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache flink](event-hubs-kafka-flink-tutorial.md)
- [Akka stromen](event-hubs-kafka-akka-streams-tutorial.md)

De vermelde services en frameworks kunnen doorgaans gebeurtenis stromen verkrijgen en rechtstreeks vanuit een verscheidenheid aan bronnen verwijzen naar gegevens via adapters. Kafka-streams kunnen alleen gegevens ophalen uit Apache Kafka en uw analyse projecten worden daarom in Apache Kafka vergrendeld. Als u gegevens uit andere bronnen wilt gebruiken, moet u eerst gegevens in Apache Kafka importeren met het Kafka Connect Framework.

Als u het Kafka streams-Framework moet gebruiken in azure, biedt [Apache Kafka op HDInsight](../hdinsight/kafka/apache-kafka-introduction.md) u deze optie. Apache Kafka op HDInsight biedt volledige controle over alle configuratie aspecten van Apache Kafka en is volledig geïntegreerd met verschillende aspecten van het Azure-platform, van het probleem/de plaatsing van het domein bij netwerk isolatie om de integratie te controleren. 

## <a name="next-steps"></a>Volgende stappen
In dit artikel is een inleiding tot Event Hubs voor Kafka beschreven. Zie [Ontwikkelaarshandleiding van Apache Kafka voor Azure Event Hubs](apache-kafka-developer-guide.md) voor meer informatie.