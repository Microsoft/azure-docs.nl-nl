---
title: Bericht replicatie en Federatie-Azure Service Bus tussen regio's | Microsoft Docs
description: In dit artikel vindt u een overzicht van gebeurtenis replicatie en Federatie met meerdere regio's met Azure Service Bus.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: 32d8c9112eeb2f71e7f2c8dcd6f8f73da2dc1ca9
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657414"
---
# <a name="message-replication-and-cross-region-federation"></a>Bericht replicatie en Federatie van meerdere regio's

In naam ruimten biedt Azure Service Bus ondersteuning voor het [maken van topologieën van gekoppelde wacht rijen en onderwerp-abonnementen met behulp van autoforwarding](service-bus-auto-forwarding.md) , zodat de implementatie van verschillende routerings patronen mogelijk wordt. U kunt bijvoorbeeld partners voorzien van specifieke wacht rijen waarnaar ze machtigingen voor verzenden of ontvangen hebben en die tijdelijk kunnen worden opgeschort, en ze flexibel verbinden met andere entiteiten die privé zijn voor de toepassing. U kunt ook complexe topologieën voor multi-stage routering maken, of u kunt wacht rijen in een postvak stijl maken, waarmee u de wachtrij-achtige abonnementen van onderwerpen verwerkt en meer opslag capaciteit per abonnee toestaat. 

Voor veel geavanceerde oplossingen moeten ook berichten worden gerepliceerd tussen naam ruimte grenzen om deze en andere patronen te kunnen implementeren. Berichten moeten mogelijk stromen tussen naam ruimten die zijn gekoppeld aan meerdere, verschillende tenants van toepassingen of meerdere verschillende Azure-regio's. 

Uw oplossing houdt meerdere Service Bus naam ruimten in verschillende regio's bij en repliceert berichten tussen wacht rijen en onderwerpen en/of u gaat berichten uitwisselen met bronnen en doelen zoals [azure Event hubs](../event-hubs/event-hubs-about.md), [azure IOT hub](../iot-fundamentals/iot-introduction.md)of [Apache Kafka](https://kafka.apache.org). 

Deze scenario's zijn de focus van dit artikel. 

## <a name="federation-patterns"></a>Federatie patronen

Er zijn talrijke mogelijke motivaties voor waarom u berichten wilt verplaatsen tussen Service Bus entiteiten zoals wacht rijen of onderwerpen, of tussen Service Bus en andere bronnen en doelen. 

Vergeleken met de vergelijk bare set patronen voor [Event hubs](../service-bus-messaging/service-bus-federation-overview.md), is Federatie voor wachtrij-achtige entiteiten complexer, omdat berichten wachtrijen hun eigen eigendom van een wille keurig bericht kunnen hand haven, naar verwachting worden bewaard en voor de Broker voor het coördineren van de billijke distributie van berichten tussen [concurrerende consumenten](/azure/architecture/patterns/competing-consumers). 

Er zijn praktische belemmeringen, met inbegrip van de beperkingen van de [Cap-theorema](https://en.wikipedia.org/wiki/CAP_theorem), waardoor het moeilijk is om een uniforme weer gave van een wachtrij te bieden die gelijktijdig beschikbaar is in meerdere regio's en die regionaal kan worden gedistribueerd, waardoor [concurrerende gebruikers](/azure/architecture/patterns/competing-consumers) zich kunnen aanmelden met exclusieve eigendom van berichten. Een dergelijke geografische gedistribueerde wachtrij zou volledig consistente replicatie van niet alleen berichten vereisen, maar ook de bezorgings status van elk bericht voordat berichten beschikbaar kunnen worden gesteld aan gebruikers. Een doel van een volledige consistentie voor een hypothetische, regionale gedistribueerde wachtrij is direct conflicteren met het belangrijkste doel dat bijna alle Azure Service Bus klanten hebben bij het overwegen van Federation-scenario's: maximale Beschik baarheid en betrouw baarheid voor hun oplossingen. 

De patronen die hier worden gepresenteerd, zijn dus gericht op Beschik baarheid en betrouw baarheid, terwijl het voor het beste vermijden van gegevens verlies en dubbele verwerking van berichten. 

### <a name="resiliency-against-regional-availability-events"></a>Tolerantie voor regionale beschikbaarheids gebeurtenissen 

Hoewel de maximale Beschik baarheid en betrouw baarheid de beste operationele prioriteiten voor Service Bus zijn, zijn er echter veel manieren waarop een producent of consument kan worden voor komen dat er wordt gereageerd op de toegewezen "primaire" Service Bus als gevolg van problemen met netwerken of naam omzetting of wanneer Service Bus entiteit mogelijk tijdelijk niet reageert of fouten retourneert. De aangewezen berichten processor is mogelijk ook niet meer beschikbaar.

Dergelijke voor waarden zijn niet "disastrous", zodat u de regionale implementatie helemaal in een nood herstel situatie kunt afbreken, maar het bedrijfs scenario van sommige toepassingen wordt mogelijk al beïnvloed door beschikbaarheids gebeurtenissen die niet meer dan een paar minuten of zelfs seconden duren. Azure Service Bus wordt vaak gebruikt in hybride Cloud omgevingen en met clients die zich aan de rand van het netwerk bevinden, bijvoorbeeld in winkels, Restaurant, bank filialen, productie sites, logistiek faciliteiten en lucht havens. Het is mogelijk dat een netwerk routering of congestie probleem invloed heeft op de mogelijkheid van een site om het toegewezen Service Bus eind punt te bereiken terwijl een secundair eind punt in een andere regio mogelijk bereikbaar is. Op hetzelfde moment kunnen systemen voor het verwerken van berichten die vanaf deze sites arriveren nog steeds toegang hebben tot de primaire en secundaire Service Bus-eind punten. 

Er zijn veel praktische voor beelden van dergelijke hybride Cloud-en Edge-toepassingen met een lage bedrijfs tolerantie voor gevolgen van problemen met de netwerk routering of van tijdelijke beschikbaarheids problemen van een Service Bus entiteit. Dit zijn onder andere verwerking van betalingen op Retail sites, het afhandelen van lucht havens en mobiele telefoon orders van restaurants, die allemaal aan een onmiddellijke en aan de slag gaan.

In deze categorie bespreken we drie verschillende gedistribueerde patronen: "alles-actief" replicatie, "actief-passief" replicatie en "spillover"-replicatie. 

#### <a name="all-active-replication"></a>Replicatie All-Active

Met het replicatie patroon ' alles-actief ' kan een actieve replica van hetzelfde logische onderwerp (of een wachtrij) beschikbaar zijn in meerdere naam ruimten (en regio's), en voor alle berichten die beschikbaar moeten zijn in alle replica's, ongeacht waar ze in de wachtrij staan. Het patroon behoudt de volg orde van berichten ten opzichte van elke uitgever. 

![Alle actieve patroon](media/service-bus-federation-overview/mirrored-topics.jpg)

Zoals in de afbeelding wordt weer gegeven, wordt het patroon in het algemeen in Service Bus-onderwerpen gelean. Eén onderwerp voor elke naam ruimte die deel uitmaakt van het replicatie schema. Elk van deze onderwerpen heeft één ' replicatie abonnement ' voor een van de andere onderwerpen waarnaar berichten moeten worden gerepliceerd. In de bovenstaande afbeelding hebben we slechts een paar onderwerpen en daarom een replicatie abonnement voor het desbetreffende andere onderwerp. In een scenario met drie naam ruimten *{N1, N2, N3}* heeft een onderwerp in de naam ruimte *N1* twee replicatie abonnementen: één voor het bijbehorende onderwerp in *N2* en één voor het bijbehorende onderwerp in *N3*. 

Elk replicatie abonnement heeft een regel die een SQL-filter expressie ( `replicated <> 1` ) en een SQL-actie ( `set replicated = 1` ) combineert. Het filter van de regel zorgt ervoor dat alleen berichten waarbij de aangepaste eigenschap `replication` niet is ingesteld of omdat de waarde niet `1` in aanmerking komt voor dit abonnement. met de actie wordt de exacte eigenschap ingesteld op de waarde voor `1` elk geselecteerd bericht. Het effect is dat wanneer het bericht wordt gekopieerd naar het bijbehorende onderwerp, het niet langer in aanmerking komt voor replicatie in tegenovergestelde richting en daarom wordt voor komen dat berichten die tussen replica's worden stuiteren.

Een abonnement met een regel kan eenvoudig worden toegevoegd aan elk onderwerp met behulp van de Azure CLI zoals deze.

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

Voor het model leren van een wachtrij is elk onderwerp beperkt tot slechts één gewoon abonnement (met uitzonde ring van de replicatie abonnementen) die alle consumenten delen.

> Het replicatie model all-Active heeft een kopie van elk bericht dat in een van de onderwerpen wordt verzonden naar elk van de onderwerpen. Dit betekent dat de code van uw toepassing in elke regio alle berichten kan zien en verwerken.
> Dit patroon is geschikt voor scenario's waarbij gegevens worden gedeeld in meerdere regio's of als redundante verwerking over het algemeen gewenst is. Als u elk bericht slechts één keer wilt verwerken, zoals bij een normale wachtrij, moet u een van de volgende twee patronen overwegen.  

#### <a name="active-passive-replication"></a>Replicatie Active-Passive

Het replicatie patroon ' actief/passief ' is variatie van het vorige patroon waarbij slechts één van de onderwerpen (de primaire) actief wordt gebruikt door de toepassing voor het verzenden en ontvangen van berichten en berichten worden gerepliceerd naar een secundair onderwerp voor het geval wanneer het primaire onderwerp mogelijk niet meer beschikbaar of onbereikbaar is. 

![Actief passieve patroon](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

Het belangrijkste verschil tussen dit patroon en het vorige patroon is dat replicatie uit het primaire onderwerp in het secundaire onderwerp komt. Het secundaire onderwerp wordt nooit de primaire, maar is een back-upoptie voor wanneer het primaire onderwerp tijdelijk onbruikbaar wordt. 

De kant van het gebruik van dit patroon is dat er wordt geprobeerd dubbele verwerking te minimaliseren. Tijdens de replicatie wordt de `TimeToLive` bericht eigenschap ingesteld op een duur van de gerepliceerde berichten die de verwachte tijd weergeeft waarin een storing van de primaire naar een failover leidt. Als uw gebruiks scenario bijvoorbeeld een overschakeling van de consument naar het secundaire proces vereist binnen Maxi maal 1 minuut bij het ophalen van berichten van de primaire start over problemen, moet de secundaire in het ideale geval alle berichten bevatten die u niet meer kunt openen in de primaire, maar een minimum aantal berichten dat u al eerder hebt verwerkt voordat de problemen werden weer gegeven. Als we de `TimeToLive` twee keer die periode, 2 minuten, tijdens de replicatie ( `set sys.TimeToLive = '0:2:0'` in de regel actie) hebben ingesteld, blijft de secundaire berichten gedurende 2 minuten behouden en worden oudere versies verwijderd. Dit betekent dat wanneer de ontvanger overschakelt naar de secundaire, berichten kan lezen en verwijderen die ouder zijn dan de laatste verwerkte IT en vervolgens verwerken van het eerste bericht dat deze nog niet is weer gegeven. De werkelijke Bewaar duur is afhankelijk van de specifieke gebruiks aanvraag en snel en op basis waarvan u wilt overstappen naar de secundaire in uw toepassing. De `TimeToLive` instelling wordt binnen een paar seconden tot dagen in het bereik gerespecteerd. 

Terwijl de toepassing de secundaire gebruikt, kan deze ook rechtstreeks worden gepubliceerd naar het secundaire onderwerp, dat vervolgens fungeert als een regel matig onderwerp. Na de overgang naar de secundaire, ziet de consument daarom een combi natie van gerepliceerde berichten en berichten die rechtstreeks op de secundaire worden gepubliceerd. De toepassing moet daarom eerst worden overgeschakeld naar de primaire en nog steeds het verwerken van de lokaal gepubliceerde berichten toestaan voordat de Consumer weer naar het secundaire bericht wordt overgeschakeld. Omdat de replicatie automatisch wordt hervat zodra de primaire versie weer beschikbaar is, zal de consumer ook nieuwe berichten ontvangen die tijdens die tijd naar de primaire versie worden gepubliceerd, met een iets hogere latentie. 

> Dit patroon is geschikt voor scenario's waarin berichten slechts eenmaal moeten worden verwerkt. De toepassing moet samen werken voor het bijhouden van de berichten die door de primaire server zijn verwerkt, omdat er dubbele waarden worden gevonden voor de duur van het failover-venster in de secundaire, waarna er dubbele waarden worden gevonden wanneer u terugkeert.
> Het criterium voor de ontdubbeling moet het beste een door de toepassing geleverde methode zijn `MessageId` . De `EnqueuedTimeUtc` waarde is ook geschikt als een watermerk indicator, maar de toepassing moet een zekere mate van klok snelheid (enkele seconden) tussen de primaire en secundaire tijd toestaan, net als bij een gedistribueerd systeem.


#### <a name="spillover-replication"></a>Spillover-replicatie

Met het replicatie patroon ' spillover ' kunnen meerdere Service Bus entiteiten in meerdere regio's worden gebruikt voor het scenario waarin Service Bus in orde is, maar de *consument* wordt overbelast met het aantal in behandeling zijnde berichten of is niet beschikbaar. Een reden hiervoor kan zijn dat een Data Base die een back-up maakt van het proces van de consument traag of niet beschikbaar is. Dit patroon werkt met onbewerkte wacht rijen en met onderwerp-abonnementen.  

![Spillover-replicatie](media/service-bus-federation-overview/spillover.jpg)  

Zoals in de afbeelding wordt weer gegeven, repliceert het spillover-replicatie patroon berichten van de [wachtrij](service-bus-dead-letter-queues.md) van een wachtrij of het abonnement aan een gekoppelde wachtrij of een onderwerp in een andere naam ruimte. 

Zonder dat er sprake is van een fout, worden de twee naam ruimten parallel gebruikt, waarbij elke subset van het totale bericht verkeer wordt ontvangen en de bijbehorende consumers die subset verwerken. Zodra een van de consumenten een hoge fout frequentie krijgt of niet meer goed stopt, worden de betreffende berichten uiteindelijk in de wachtrij voor onbestelbare meldingen geplaatst door het aantal leveringen te overschrijden of verlopen. De replicatie taken worden vervolgens verzameld en vervolgens opnieuw in de gekoppelde wachtrij geplaatst, waar ze vervolgens worden gepresenteerd aan de vermoedelijke, gezonde consument. 

Als de verwerking binnen een bepaalde deadline moet plaatsvinden, `TimeToLive` moeten de voor de wachtrij en/of berichten zodanig worden ingesteld dat de verwerking nog steeds op tijd kan worden uitgevoerd door de spillover secundair, bijvoorbeeld `TimeToLive` kan worden ingesteld op de helft van de toegestane tijd.

Net als bij het [patroon alles-actief](#all-active-replication)kan de toepassing een indicator toevoegen aan het bericht of het bericht al een keer is gerepliceerd, maar dat ze niet stuiteren tussen het paar wacht rijen, maar in plaats daarvan worden geplaatst in een hulp wachtrij die fungeert als wachtrij met onbestelbare berichten voor het samengestelde patroon.

> Dit patroon is geschikt voor scenario's waarbij het belangrijkste probleem zich voordoet bij het beschermen van de beschik baarheid van gebruikers of bronnen die door de consumenten worden vertrouwt, en voor het opnieuw distribueren van verkeers pieken in een van de gekoppelde wacht rijen. Het biedt ook beveiliging tegen een van de naam ruimten die niet meer beschikbaar zijn als gebruikers van beide wacht rijen worden gelezen, maar de replicatie vertraging die door de `TimeToLive` verval datum wordt opgelegd, kan ertoe leiden dat berichten binnen dat tijd venster deel uitmaakt van de niet-beschik bare naam ruimte. 

### <a name="latency-optimization"></a>Latentie optimalisatie 

Onderwerpen worden gebruikt om informatie te distribueren naar meerdere gebruikers. In sommige gevallen, met name de consumenten met een brede geografische distributie, kan het nuttig zijn om berichten van een onderwerp te repliceren naar een onderwerp in een secundaire naam ruimte dichter bij consumenten.

![Latentie optimalisatie](media/service-bus-federation-overview/latency-optimization.jpg)  

Bij het delen van gegevens tussen regionale, continentale hubs is het echter efficiënter om informatie slechts één keer tussen hubs over te dragen en hebben ze hun kopie van de gegevens van deze hubs ontvangen. 

Replicatie overdrachten kunnen worden uitgevoerd in batches, welke consumenten vaak berichten één voor één verkrijgen en vereffenen. Met een basis netwerk latentie van 100 MS tussen, zegt Noord-Amerika en Europa, neemt elk bericht 200 MS langer op voor het verwerken van de twee Retouren naar een externe entiteit voor het verkrijgen en afwijzen van de berichten, vergeleken met een entiteit in dezelfde regio. 

### <a name="validation-reduction-and-enrichment"></a>Validatie, reductie en verrijking

Berichten kunnen worden verzonden naar een Service Bus wachtrij of onderwerp door clients die extern zijn voor uw eigen oplossing.

![Validatie, reductie, verrijking](media/service-bus-federation-overview/validation.jpg)  

Dergelijke berichten kunnen controleren op naleving van een bepaald schema en voor niet-compatibele berichten die worden verwijderd of onbesteld. Het kan zijn dat sommige berichten in complexiteit moeten worden verkleind door gegevens te weglaten en andere moeten worden verrijkt door gegevens toe te voegen op basis van zoek opdrachten voor referentie gegevens. De bewerkingen kunnen worden uitgevoerd met aangepaste functionaliteit in de replicatie taak. 

### <a name="stream-to-queue-replication"></a>Stroom naar wachtrij replicatie

Azure Event Hubs is een ideale oplossing voor het verwerken van extreme volumes van binnenkomende gebeurtenissen. Maar niet Event Hubs en soort gelijke engines, zoals Apache Kafka bieden een [concurrerend gebruikers](/azure/architecture/patterns/competing-consumers) model dat door een service wordt beheerd, waarbij meerdere consumenten berichten gelijktijdig kunnen afhandelen vanuit dezelfde bron zonder risico op dubbele verwerking, en deze berichten tot slot afrekenen zodra ze zijn verwerkt. 

![Integratie](media/service-bus-federation-overview/hub-to-queue.jpg)

Met een stroom naar wachtrij replicatie wordt de inhoud van een single event hub-partitie of de inhoud van een volledige Event hub overgebracht naar een Service Bus wachtrij, van waaruit de berichten veilig, transactioneel en met concurrerende consumenten kunnen worden verwerkt. Met deze replicatie kunnen ook alle andere Service Bus mogelijkheden voor deze berichten worden gebruikt, met inbegrip van route ring met onderwerpen en demultiplexing op basis van een sessie. 

### <a name="consolidation-and-normalization"></a>Consolidatie en normalisatie 

Wereld wijde oplossingen bestaan vaak uit regionale footprint die grotendeels onafhankelijk zijn, inclusief hun eigen verwerkings mogelijkheden, maar voor supra-regionale en wereld wijde perspectieven is gegevens integratie vereist en dus een centrale samen voeging van dezelfde bericht gegevens die in de respectieve regionale Footprints voor het lokale perspectief worden geëvalueerd. 

![Gekopieerd](media/service-bus-federation-overview/merge.jpg)

Normalisatie is een basis van het consolidatie scenario, waarbij twee of meer inkomende reeksen berichten dezelfde soort informatie hebben, maar met verschillende structuren of verschillende code ringen, en de berichten moeten worden gedecodeerd of getransformeerd voordat ze kunnen worden gebruikt. 

Normalisatie kan ook cryptografische werkzaamheden omvatten, zoals het ontsleutelen van end-to-end versleutelde nettoladingen en het opnieuw versleutelen van deze met verschillende sleutels en algoritmen voor de stroomafwaartse consument. 

### <a name="splitting-and-routing"></a>Splitsen en omleiden

Service Bus-onderwerpen en hun abonnements regels worden vaak gebruikt om een stroom berichten te filteren voor een bepaalde doel groep en die doel groep heeft vervolgens de gefilterde set opgehaald uit een abonnement. 

![Splitsen](media/service-bus-federation-overview/split.jpg)

In een globaal systeem waarbij de doel groep voor deze berichten wereld wijd wordt gedistribueerd of deel uitmaakt van verschillende toepassingen, kan replicatie worden gebruikt om berichten van een dergelijk abonnement over te brengen naar een wachtrij of onderwerp in een andere naam ruimte, waar ze worden verbruikt.

### <a name="replication-applications-in-azure-functions"></a>Replicatie toepassingen in Azure Functions

Voor de implementatie van de bovenstaande patronen is een schaal bare en betrouw bare uitvoerings omgeving vereist voor de replicatie taken die u wilt configureren en uitvoeren. In Azure is de runtime-omgeving die het meest geschikt is voor stateless taken [Azure functions](../azure-functions/functions-overview.md). 

Azure Functions kan worden uitgevoerd onder een door [Azure beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) , zodat de replicatie taken kunnen worden geïntegreerd met de op rollen gebaseerde regels voor toegangs beheer van de bron-en doel Services zonder dat u geheimen hoeft te beheren via het replicatie traject. Voor replicatie bronnen en doelen die expliciete referenties vereisen, kan Azure Functions de configuratie waarden voor die referenties in nauw keurige, door Access beheerde opslag binnen [Azure Key Vault](../key-vault/general/overview.md)bewaren.

Azure Functions kunnen de replicatie taken bovendien rechtstreeks integreren met Azure Virtual Networks en [service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) voor alle Azure Messa ging-Services, en het is gemakkelijk geïntegreerd met [Azure monitor](../azure-monitor/overview.md).

Het belangrijkste is dat Azure Functions vooraf ontwikkelde, schaal bare triggers en uitvoer bindingen voor [azure Event hubs](../azure-functions/functions-bindings-service-bus.md), [azure IOT hub](../azure-functions/functions-bindings-event-iot.md), [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event grid](../azure-functions/functions-bindings-event-grid.md)en [Azure Queue Storage](/azure-functions/functions-bindings-storage-queue.md), aangepaste uitbrei dingen voor [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)en [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)heeft. De meeste triggers worden dynamisch aan de doorvoer behoeften aangepast door het aantal gelijktijdig uitgevoerde instanties te schalen op basis van gedocumenteerde metrische gegevens. 

Met het Azure Functions-verbruiks abonnement kan de vooraf gemaakte triggers zelfs op nul worden geschaald, terwijl er geen berichten beschikbaar zijn voor replicatie. Dit betekent dat u geen kosten hebt om de configuratie gereed te maken voor het schalen van back-ups. De sleutel die het verbruiks plan gebruikt, is dat de latentie voor replicatie taken van deze status aanzienlijk hoger is dan met de hosting plannen waar de infra structuur actief is.  

In tegens telling tot al deze, de meest voorkomende replicatie-engines voor berichten en gebeurtenissen, zoals de [MirrorMaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) van Apache Kafka, moet u een hostomgeving opgeven en zelf de replicatie-engine schalen. Dit omvat het configureren en integreren van de beveiligings-en netwerk functies en het vergemakkelijken van de stroom van bewakings gegevens, en u hebt dan nog steeds geen mogelijkheid om aangepaste replicatie taken in de stroom in te voeren. 

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een reeks Federatie patronen verkend en de rol van Azure Functions uitgelegd als runtime voor gebeurtenis-en Messa ging-replicatie in Azure. 

Vervolgens wilt u weten hoe u een Replicator-toepassing instelt met Azure Functions en hoe u gebeurtenis stromen kunt repliceren tussen Event Hubs en verschillende andere gebeurtenissen voor gebeurtenis-en bericht systemen:

- [Replicatie toepassingen in Azure Functions](service-bus-federation-replicator-functions.md)
- [Gebeurtenissen tussen Service Bus entiteiten repliceren](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [Routerings gebeurtenissen naar Azure Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [Gebeurtenissen ophalen van Azure Event Hubs](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubsCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif 
