---
title: Multi-site en Federatie-Azure-Event Hubs met meerdere regio's | Microsoft Docs
description: In dit artikel vindt u een overzicht van de Federatie met meerdere sites en meerdere regio's met Azure Event Hubs.
ms.topic: article
ms.date: 12/12/2020
ms.author: spelluru
ms.openlocfilehash: 12ef895c8b16fe18ed02ebf01d17624ac71c2f3e
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861461"
---
# <a name="multi-site-and-multi-region-federation"></a>Federatie in meerdere sites en regio's

Voor veel geavanceerde oplossingen moeten dezelfde gebeurtenis stromen beschikbaar worden gemaakt voor gebruik op meerdere locaties, of moeten gebeurtenis stromen op meerdere locaties worden verzameld en vervolgens worden geconsolideerd in een specifieke locatie voor verbruik. Er is ook vaak de nood zaak om gebeurtenis stromen te verrijken of te verminderen, of om te zorgen voor de conversies van gebeurtenis indelingen, ook binnen één regio en oplossing.

Dit betekent dat uw oplossing meerdere Event Hubs houdt, vaak in verschillende regio's en Event Hubs naam ruimten, en vervolgens gebeurtenissen ertussen repliceert. U kunt ook gebeurtenissen uitwisselen met bronnen en doelen als [Azure service bus](../service-bus-messaging/service-bus-messaging-overview.md), [Azure IOT hub](../iot-fundamentals/iot-introduction.md)of [Apache Kafka](https://kafka.apache.org). 

Door meerdere actieve Event Hubs in verschillende regio's te onderhouden, kunnen clients ook kiezen en hiertussen scha kelen als de inhoud wordt samengevoegd, waardoor het algehele systeem flexibeler is tegen regionale beschikbaarheids problemen.

In het hoofd stuk ' Federatie ' worden Federatie patronen uitgelegd en wordt beschreven hoe u deze patronen kunt realiseren met behulp van de serverloze [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) of de [Azure functions][1] runtimes, met de mogelijkheid om uw eigen trans formatie of verrijkings code recht te hebben in het pad naar de gebeurtenis stroom. 

## <a name="federation-patterns"></a>Federatie patronen

Er zijn veel potentiële motivaties voor waarom u gebeurtenissen wilt verplaatsen tussen verschillende Event Hubs of andere bronnen en doelen, en we hebben de belangrijkste patronen in deze sectie opgesomd en ook een koppeling naar meer gedetailleerde richt lijnen voor het respectieve patroon. 

- [Tolerantie voor regionale beschikbaarheids gebeurtenissen](#resiliency-against-regional-availability-events)
- [Latentie optimalisatie](#latency-optimization)
- [Validatie, reductie en verrijking](#validation-reduction-and-enrichment)
- [Integratie met Analytics Services](#integration-with-analytics-services)
- [Consolidatie en normalisatie van gebeurtenis stromen](#consolidation-and-normalization-of-event-streams)
- [Gebeurtenis stromen splitsen en routeren](#splitting-and-routing-of-event-streams)
- [Logboek-projecties](#log-projections)
  
### <a name="resiliency-against-regional-availability-events"></a>Tolerantie voor regionale beschikbaarheids gebeurtenissen 

![Regionale beschikbaarheid](media/event-hubs-federation-overview/regional-availability.jpg)

Hoewel maximale Beschik baarheid en betrouw baarheid de belangrijkste operationele prioriteiten voor Event Hubs zijn, zijn er echter veel manieren waarop een producent of consument kan worden voor komen dat er wordt gereageerd op de toegewezen ' primaire ' event hub vanwege netwerk-of naam omzettings problemen of wanneer een event hub tijdelijk niet kan reageren of fouten retourneert. 

Dergelijke voor waarden zijn niet "disastrous", zodat u de regionale implementatie volledig kunt afbreken, omdat u zich mogelijk in een [nood herstel](event-hubs-geo-dr.md) situatie voordoet, maar het bedrijfs scenario van sommige toepassingen wordt mogelijk al beïnvloed door beschikbaarheids gebeurtenissen die niet meer dan een paar minuten of zelfs seconden duren. 

Er zijn twee Foundation-patronen om dergelijke scenario's te verhelpen:

- Het [replicatie][4] patroon staat op het punt om de inhoud van een primaire Event hub te repliceren naar een secundaire Event hub, waarbij de primaire Event hub doorgaans door de toepassing wordt gebruikt voor het produceren en gebruiken van gebeurtenissen en de secundaire fungeert als een terugval optie als de primaire Event hub niet beschikbaar is. Omdat de replicatie op een andere manier van de primaire naar de secundaire replica is, zorgt een overschakeling van zowel de producent als de consument van een niet-beschik bare primaire naar de secundaire, ervoor dat de oude primair geen nieuwe gebeurtenissen meer ontvangt en dat deze daarom niet langer actueel is.
  Zuivere replicatie is daarom alleen geschikt voor failover-scenario's met één richting. Zodra de failover is uitgevoerd, wordt de oude primaire primair afgebroken en moet er een nieuwe secundaire Event hub in een andere doel regio worden gemaakt.
- Het [samenvoegings][5] patroon breidt het replicatie patroon uit door een continue samen voeging van de inhoud van twee of meer Event hubs uit te voeren. Elke gebeurtenis die oorspronkelijk is geproduceerd in een van de Event Hubs in het schema, wordt gerepliceerd naar de andere Event Hubs. Wanneer gebeurtenissen worden gerepliceerd, worden ze door het replicatie proces van het replicatie doel genegeerd. De resultaten van het gebruik van het samenvoeg patroon zijn twee of meer Event Hubs die dezelfde set gebeurtenissen op een uiteindelijk consistente manier bevatten. 
  
In beide gevallen is de inhoud van de Event Hubs niet identiek. Gebeurtenissen van één producent en gegroepeerd op dezelfde partitie sleutel worden weer gegeven in dezelfde relatieve volg orde als oorspronkelijk is verzonden, maar de absolute volg orde van de gebeurtenissen kan verschillen. Dit geldt met name voor scenario's waarbij het aantal partities van de bron-en doel Event Hubs verschillen, wat wenselijk is voor diverse uitgebreide patronen die hier worden beschreven. Een [Splitser of router](#splitting-and-routing-of-event-streams) kan een segment van een veel grotere Event hub verkrijgen met honderden partities en trechter in een kleinere Event hub met slechts een aantal partities, wat geschikt is voor het afhandelen van de subset met beperkte verwerkings resources. Daarentegen kunnen gegevens van verschillende kleinere Event Hubs worden afgesplitst [in een enkele](#consolidation-and-normalization-of-event-streams) , grotere Event hub met meer partities om te voldoen aan de geconsolideerde door Voer en de verwerkings behoeften.

Het criterium voor het bijeenhouden van gebeurtenissen is de partitie sleutel en niet de oorspronkelijke partitie-ID. Meer overwegingen met betrekking tot de relatieve volg orde en het uitvoeren van een failover van een event hub naar de volgende, zonder dat hiervoor hetzelfde bereik van stroom offsets nodig is, wordt beschreven in beschrijving van het [replicatie][4] patroon.


Indicatie 
- [Replicatie patroon][4]
- [Patroon samen voegen][5]

### <a name="latency-optimization"></a>Latentie optimalisatie 

![Latentie optimalisatie](media/event-hubs-federation-overview/latency-optimization.jpg)  

Gebeurtenis stromen worden eenmaal per producent geschreven, maar kunnen een wille keurig aantal keren door gebeurtenis gebruikers worden gelezen. Voor scenario's waarin een gebeurtenis stroom in een regio wordt gedeeld door meerdere gebruikers en die herhaaldelijk moet worden gebruikt tijdens analyse verwerking die zich in een andere regio bevindt, of in het geval van aanvragen die gelijktijdig tijd resteert zijn, kan het nuttig zijn om een kopie van de gebeurtenis stroom bij de analyse processor te plaatsen om de retour latentie te verminderen. 

Goede voor beelden voor wanneer replicatie moet worden voor rang boven het gebruik van gebeurtenissen die op afstand worden gebruikt in verschillende regio's, waarbij de regio's zeer ver uit elkaar liggen, bijvoorbeeld Europa en Australië bijna Antipodes zijn, kunnen geografische en netwerk latenties voor een wille keurige afronding heel gemakkelijk 250 MS overschrijden.
U kunt de snelheid van het licht niet versnellen, maar het aantal afrondingen met een hoge latentie kan worden beperkt om te communiceren met gegevens.

Indicatie 
- [Replicatie patroon][4]

### <a name="validation-reduction-and-enrichment"></a>Validatie, reductie en verrijking

![Validatie, reductie, verrijking](media/event-hubs-federation-overview/validation-enrichment.jpg)  

Gebeurtenis stromen kunnen door clients extern naar uw eigen oplossing worden verzonden naar een event hub. Dergelijke gebeurtenis stromen kunnen vereisen dat extern ingediende gebeurtenissen worden gecontroleerd op naleving van een bepaald schema, en voor niet-conforme gebeurtenissen die moeten worden verwijderd. 

In scenario's waarin clients extreem weinig band breedte hebben, omdat dit het geval is in veel ' Internet of Things scenario's met een band breedte met een capaciteit, of waarbij gebeurtenissen oorspronkelijk via niet-IP-netwerken met beperkte pakket groottes worden verzonden, moeten de gebeurtenissen mogelijk worden verrijkt met referentie gegevens om verdere context te kunnen toevoegen door downstream-gebeurtenis processors.

In andere gevallen, met name wanneer stromen worden geconsolideerd, moeten de gebeurtenis gegevens mogelijk worden verminderd met de complexiteit of de grootte van de Sheer door een deel van de details te weglaten.

Elk van deze bewerkingen kan optreden als onderdeel van replicatie-, consolidatie-of samenvoeg stromen. 

Indicatie 
- [Patroon van editor][6]

### <a name="integration-with-analytics-services"></a>Integratie met Analytics Services

![Integratie met Analytics Services](media/event-hubs-federation-overview/integration.jpg)

Een aantal Cloud-eigen analyse services van Azure, zoals Azure Stream Analytics of Azure Synapse, werken het beste met gestreamde of vooraf gebatcheerde gegevens uit Azure Event Hubs en Azure Event Hubs biedt ook integratie met verschillende open source-analyse pakketten zoals Apache Samza, Apache flink, Apache Spark en Apache Storm. 

Als uw oplossing voornamelijk gebruikmaakt van Service Bus of Event Grid, kunt u deze gebeurtenissen eenvoudig toegankelijk maken voor dergelijke analyse systemen en ook voor archivering met behulp van Event Hubs Capture als u ze in een event hub aftrechtert. Event Grid kunt dit doen met de integratie van de [Event hub](../event-grid/handler-event-hubs.md), voor service bus u de [Service Bus replicatie richtlijnen](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)volgt.

Azure Stream Analytics kan [rechtstreeks worden geïntegreerd met Event hubs](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs).

Indicatie 
- [Replicatie patroon][4]

### <a name="consolidation-and-normalization-of-event-streams"></a>Consolidatie en normalisatie van gebeurtenis stromen

![Consolidatie en normalisatie van gebeurtenis stromen](media/event-hubs-federation-overview/consolidation.jpg)

Wereld wijde oplossingen bestaan vaak uit regionale footprint die grotendeels onafhankelijk zijn, met inbegrip van hun eigen analyse mogelijkheden, maar voor supra-regionale en wereld wijde analyse perspectieven is een geïntegreerd perspectief vereist. Daarom is er een centrale consolidatie van dezelfde gebeurtenis stromen die in de respectieve regionale Footprints voor het lokale perspectief worden geëvalueerd. 

Normalisatie is een basis van het consolidatie scenario, waarbij twee of meer binnenkomende gebeurtenis stromen hetzelfde soort gebeurtenissen hebben, maar met verschillende structuren of verschillende code ringen, en de gebeurtenissen die het meest worden gedecodeerd of getransformeerd voordat ze kunnen worden gebruikt. 

Normalisatie kan ook cryptografische werkzaamheden omvatten, zoals het ontsleutelen van end-to-end versleutelde nettoladingen en het opnieuw versleutelen van deze met verschillende sleutels en algoritmen voor de stroomafwaartse consument. 

Indicatie 
- [Patroon samen voegen][5]
- [Patroon van editor][6]

### <a name="splitting-and-routing-of-event-streams"></a>Gebeurtenis stromen splitsen en routeren

![Gebeurtenis stromen splitsen en routeren](media/event-hubs-federation-overview/splitting.jpg)

Azure Event Hubs wordt af en toe gebruikt in scenario's met ' publiceren en abonneren ' waarbij een binnenkomende torrent van opgenomen gebeurtenissen de capaciteit van Azure Service Bus of Azure Event Grid overschrijdt, waarvan beide een systeem eigen functie hebben voor het filteren van publicaties en distributie, en die de voor keur hebben voor dit patroon. 

Hoewel het mogelijk is dat de mogelijkheid om de gewenste gebeurtenissen te kiezen door een ' Abonneer-Subscriber '-functie wordt ingeschakeld, heeft het splitsings patroon tot gevolg dat de producent gebeurtenissen met een vooraf bepaald distributie model en de aangewezen consumenten ophaalt. Als de Event hub het algehele verkeer buffert, kan de inhoud van een bepaalde partitie, die een fractie van het oorspronkelijke doorvoer volume vertegenwoordigt, vervolgens worden gerepliceerd naar een wachtrij voor betrouw bare, transactioneel, concurrerend verbruik van de klant.

Veel scenario's waarbij Event Hubs hoofd zakelijk wordt gebruikt voor het verplaatsen van gebeurtenissen binnen een toepassing binnen een regio, zijn gevallen waarin het selecteren van gebeurtenissen, misschien alleen vanaf één partitie, ook elders beschikbaar moet worden gesteld. Dit scenario is vergelijkbaar met het scenario voor het splitsen, maar kan ook een schaal bare router gebruiken die van toepassing is op alle berichten die in een event hub en Cherry worden opgenomen. er worden slechts enkele gepickt voor route ring en kunnen routerings doelen onderscheiden per gebeurtenis meta gegevens of inhoud. 

Indicatie
- [Routerings patroon][7]

### <a name="log-projections"></a>Logboek-projecties 

![Projectie van logboek](media/event-hubs-federation-overview/log-projection.jpg)

In sommige scenario's wilt u toegang hebben tot de meest recente waarde die voor een substream van een gebeurtenis is verzonden en die meestal wordt onderscheiden door de partitie sleutel. In Apache Kafka wordt dit vaak bereikt door ' logboek compressie ' in te scha kelen in een onderwerp, waarmee alle, behalve de laatste gebeurtenis met een unieke sleutel wordt verwijderd. De methode voor het comprimeren van een logboek heeft drie verschillende nadelen: 

- Voor de compressie is een permanente reorganisatie van het logboek vereist. Dit is een overmatig dure bewerking voor een Broker die is geoptimaliseerd voor alleen-lezen workloads. 
- De compressie is destructief en het is niet toegestaan een gecomprimeerd en niet-gecomprimeerd perspectief van dezelfde stroom te maken.
- Een gecomprimeerde stroom heeft nog steeds een sequentieel toegangs model, wat inhoudt dat de gewenste waarde in het logboek moet worden gelezen in het ergste geval, wat doorgaans leidt tot optimalisaties die het exacte patroon implementeren dat hier wordt weer gegeven: projecteer de logboek inhoud in een Data Base of cache. 

Uiteindelijk is een gecomprimeerd logboek een sleutel-waarde-archief en als zodanig, is het de slechts mogelijke implementatie optie voor een dergelijke opslag. Het is veel efficiënter voor zoek acties en voor query's voor het maken en gebruiken van een permanente projectie van het logboek op een juiste sleutel-waarde Store of een andere data base. 

Omdat gebeurtenissen onveranderbaar zijn en de volg orde altijd in een logboek wordt bewaard, is een projectie van een logboek in een sleutel-waarde-archief altijd hetzelfde als het bereik van gebeurtenissen, wat inhoudt dat een projectie die u bijwerkt altijd een gezaghebbende weer gave biedt en er nooit een goede reden is om deze opnieuw op te bouwen bij de inhoud van het logboek. 

Indicatie
- [Projectie van logboek][8]

## <a name="replication-application-technologies"></a>Technologieën voor replicatie toepassingen

Voor de implementatie van de bovenstaande patronen is een schaal bare en betrouw bare uitvoerings omgeving vereist voor de replicatie taken die u wilt configureren en uitvoeren. In azure zijn de runtime omgevingen die het meest geschikt zijn voor dergelijke taken stateless taken [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) voor stateful stroom replicatie taken en [Azure functions](../azure-functions/functions-overview.md) voor stateless replicatie taken.

### <a name="stateful-replication-applications-in-azure-stream-analytics"></a>Stateful replicatie toepassingen in Azure Stream Analytics

Voor stateful-replicatie toepassingen die relaties tussen gebeurtenissen moeten overwegen, samengestelde gebeurtenissen, verrijkende gebeurtenissen maken of gebeurtenissen verminderen, gegevens aggregaties maken en nettoladingen van gebeurtenissen transformeren, [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) de beste implementatie optie is.

In Azure Stream Analytics maakt u [taken](../stream-analytics/stream-analytics-quick-create-portal.md) voor de integratie van [invoer](../stream-analytics/stream-analytics-add-inputs.md) en [uitvoer](../stream-analytics/stream-analytics-define-outputs.md) en integreert u de gegevens van de invoer via [query's](/stream-analytics-query/stream-analytics-query-language-reference) waarmee een resultaat wordt verkregen dat vervolgens beschikbaar wordt gesteld aan de uitvoer.

Query's zijn gebaseerd op de [SQL-query taal](/stream-analytics-query/stream-analytics-query-language-reference) en kunnen worden gebruikt om streaminggegevens in een bepaalde periode eenvoudig te filteren, te sorteren, te combi neren en samen te voegen. U kunt deze SQL-taal ook uitbreiden met [Java script](../stream-analytics/stream-analytics-javascript-user-defined-functions.md) en door de [gebruiker gedefinieerde functies (udf's) van C#](../stream-analytics/stream-analytics-edge-csharp-udf-methods.md). U kunt eenvoudig de opties voor gebeurtenisvolgorde en de duur van tijdvensters aanpassen bij het uitvoeren van combinatiebewerkingen, met behulp van eenvoudige taalconstructs en/of configuraties.

Elke taak heeft een of meer uitvoerwaarden voor de getransformeerde gegevens, en u kunt bepalen wat er gebeurt als reactie op de informatie die u hebt geanalyseerd. U kunt bijvoorbeeld:

- Stuur gegevens naar services zoals Azure Functions, Service Bus Topics of Queues om communicatie of downstream aangepaste werkstromen te activeren.
- Stuur gegevens naar een Power BI-dashboard voor realtime dashboards.
- Sla gegevens op in andere Azure Storage-services (bijvoorbeeld Azure Data Lake, Azure Synapse Analytics, enzovoort) om batch analyses uit te voeren of machine learning modellen te trainen op basis van zeer grote, geïndexeerde groepen met historische gegevens.
- Store-projecties (ook wel ' gerealiseerde weer gaven ' genoemd) in data bases ([SQL database](../stream-analytics/sql-database-output.md), [Cosmos DB](../stream-analytics/azure-cosmos-db-output.md) ).

### <a name="stateless-replication-applications-in-azure-functions"></a>Stateless replicatie toepassingen in Azure Functions

Voor stateless replicatie taken waarbij u gebeurtenissen wilt door sturen zonder rekening te houden met de payloads of deze afzonderlijk te verwerken zonder dat u de relaties van gebeurtenissen (behalve hun relatieve volg orde) moet gebruiken, kunt u Azure Functions, dat enorm flexibel is.

Azure Functions heeft vooraf ontwikkelde, schaal bare triggers en uitvoer bindingen voor [azure Event hubs](../azure-functions/functions-bindings-event-hubs.md), [azure IOT hub](../azure-functions/functions-bindings-event-iot.md), [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event grid](../azure-functions/functions-bindings-event-grid.md)en [Azure Queue Storage](../azure-functions/functions-bindings-storage-queue.md), evenals aangepaste uitbrei dingen voor [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)en [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension). De meeste triggers worden dynamisch aan de doorvoer behoeften aangepast door het aantal gelijktijdig uitgevoerde instanties te schalen op basis van gedocumenteerde metrische gegevens. 

Azure Functions ondersteunt uitvoer bindingen voor [Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md) en [Azure Table Storage](../azure-functions/functions-bindings-storage-table-output.md)voor het opbouwen van logboek projecties.

Azure Functions kan worden uitgevoerd onder een door [Azure beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) en met die kan de configuratie waarden voor referenties worden opgeslagen in nauw keurige, door Access beheerde opslag binnen [Azure Key Vault](../key-vault/general/overview.md).

Azure Functions zorgt er ook voor dat de replicatie taken rechtstreeks kunnen worden geïntegreerd met Azure Virtual Networks en [service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) voor alle Azure Messa ging-Services, en het is gemakkelijk geïntegreerd met [Azure monitor](../azure-monitor/overview.md).

Met het Azure Functions-verbruiks abonnement kan de vooraf gemaakte triggers zelfs op nul worden geschaald, terwijl er geen berichten beschikbaar zijn voor replicatie. Dit betekent dat u geen kosten hebt om de configuratie gereed te maken voor het schalen van back-ups. de sleutel die het verbruiks plan gebruikt, is dat de latentie voor replicatie taken van deze status aanzienlijk hoger is dan met de hosting plannen waar de infra structuur actief is.  

In tegens telling tot al deze, de meest voorkomende replicatie-engines voor berichten en gebeurtenissen, zoals de [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) van Apache Kafka, moet u een hostomgeving opgeven en zelf de replicatie-engine schalen. Dit omvat het configureren en integreren van de beveiligings-en netwerk functies en het vergemakkelijken van de stroom van bewakings gegevens, en u hebt dan nog steeds geen mogelijkheid om aangepaste replicatie taken in de stroom in te voeren. 

### <a name="choosing-between-azure-functions-and-azure-stream-analytics"></a>Kiezen tussen Azure Functions en Azure Stream Analytics

Azure Stream Analytics (ASA) is de beste optie wanneer u de payload van uw gebeurtenissen wilt verwerken tijdens het repliceren. Met ASA kunnen gebeurtenissen één voor één worden gekopieerd of worden aggregaties gemaakt waarmee de gegevens van gebeurtenis stromen worden versmald voordat deze worden doorgestuurd. Het kan eenvoudig worden doorgevoerd in een [aanvulling op de referentie gegevens](../stream-analytics/stream-analytics-use-reference-data.md) in Azure Blob Storage of Azure SQL database zonder dat dergelijke gegevens in een stroom hoeven te worden geïmporteerd.

Met ASA kunt u eenvoudig permanente, gerealiseerde weer gaven van stromen maken in Hyper-Scale-data bases. Het is een uiterst superieure benadering van het clunky-model van Apache Kafka en de vluchtige tabel projectie van Kafka-streams. 

ASA kan snel gebeurtenissen verwerken met nettoladingen die zijn gecodeerd in de [CSV-, JSON-en Apache Avro-indelingen](../stream-analytics/stream-analytics-parsing-json.md) en u kunt [aangepaste deserializers](../stream-analytics/custom-deserializer.md) voor elke andere indeling.

Voor alle replicatie taken waarbij u gebeurtenis stromen wilt kopiëren "as-is" en zonder de payloads te raken, of als u een router moet implementeren, cryptografische werkzaamheden moeten worden uitgevoerd, de code ring van nettoladingen wilt wijzigen of als u de inhoud van de gegevens stroom anders volledig wilt beheren, Azure Functions de beste optie is.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een reeks Federatie patronen verkend en de rol van Azure Functions uitgelegd als runtime voor gebeurtenis-en Messa ging-replicatie in Azure.

Vervolgens kunt u lezen hoe u een Replicator-toepassing instelt met Azure Stream Analytics of Azure Functions en vervolgens gebeurtenis stromen tussen Event Hubs en verschillende andere gebeurtenissen voor gebeurtenis en berichten repliceert:

- [Gebeurtenisreplicatietaakpatronen][10]
- [Gegevens verwerken met Azure Stream Analytics][9]
- [Gebeurtenis-Replicator toepassingen in Azure Functions][1]
- [Gebeurtenissen tussen Event Hubs repliceren][2]
- [Gebeurtenissen repliceren naar Azure Service Bus][3]
- [Apache Kafka MirrorMaker gebruiken met Event Hubs][11] 

[1]: event-hubs-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus
[4]: event-hubs-federation-patterns.md#replication
[5]: event-hubs-federation-patterns.md#merge
[6]: event-hubs-federation-patterns.md#editor
[7]: event-hubs-federation-patterns.md#routing
[8]: event-hubs-federation-patterns.md#log-projection
[9]: process-data-azure-stream-analytics.md
[10]: event-hubs-federation-patterns.md#replication
[11]: event-hubs-kafka-mirror-maker-tutorial.md