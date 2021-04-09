---
title: Overzicht van Azure Service Bus-berichtenservice | Microsoft Docs
description: Dit artikel bevat een overzicht op hoog niveau van Azure Service Bus, een volledig beheerde berichtenbroker die binnen ondernemingen kan worden geïntegreerd.
ms.topic: overview
ms.date: 02/16/2021
ms.openlocfilehash: 897729b9748d69ad3c6de507e800dbb3a1a3619c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570476"
---
# <a name="what-is-azure-service-bus"></a>Wat is Azure Service Bus?
Microsoft Azure Service Bus is een volledig beheerde zakelijke berichtenbroker met berichtwachtrijen en onderwerpen over publiceren-abonneren. Service Bus wordt gebruikt om toepassingen en services van elkaar los te koppelen, waardoor de volgende voordelen worden geboden:

- Taakverdeling tussen concurrerende werkrollen
- Veilig routeren en overdragen van gegevens en controle over de grenzen van services en toepassingen heen
- Transactionele werkzaamheden coördineren waarvoor een hoge mate van betrouwbaarheid vereist is 

## <a name="overview"></a>Overzicht
Gegevens worden uitgewisseld tussen verschillende toepassingen en services met behulp van *berichten*. Een bericht is een container die is voorzien van metagegevens en gegevens bevat. De gegevens kunnen elk soort informatie zijn, inclusief gestructureerde gegevens die zijn gecodeerd in algemene indelingen, zoals de volgende: JSON, XML, Apache Avro, tekst zonder opmaak.

Enkele algemene berichtenscenario's:

* *Berichtenuitwisseling*. Draag bedrijfsgegevens over, zoals verkoop- of inkooporders, dagboeken of voorraadverplaatsingen.
* *Toepassingen loskoppelen*. Verbeter de betrouwbaarheid en schaalbaarheid van toepassingen en services. Producent en consument hoeven niet tegelijkertijd online of onmiddellijk beschikbaar te zijn. De [belasting wordt verdeeld](/azure/architecture/patterns/queue-based-load-leveling), zodat verkeerspieken een service niet overbelasten. 
* *Taakverdeling*. Meerdere [concurrerende consumenten](/azure/architecture/patterns/competing-consumers) kunnen tegelijk lezen uit een wachtrij, waarbij elk veilig exclusief eigendom van specifieke berichten verkrijgt. 
* *Onderwerpen en abonnementen*. Schakel 1:*n*-relaties tussen [uitgevers en abonnees](/azure/architecture/patterns/publisher-subscriber) in, zodat abonnees bepaalde berichten uit een stroom met gepubliceerde berichten kunnen selecteren.
* *Transacties*. Hiermee kunt u verschillende bewerkingen uitvoeren, allemaal binnen het bereik van een atomische transactie. De volgende bewerkingen kunnen bijvoorbeeld worden uitgevoerd binnen het bereik van een transactie.  

    1. Een bericht uit één wachtrij ophalen.
    2. Resultaten van verwerking naar een of meer verschillende wachtrijen verzenden.
    3. Het invoerbericht vanuit de oorspronkelijke wachtrij verplaatsen. 
    
    Alleen bij geslaagde resultaten zijn deze zichtbaar voor downstreamconsumenten, inclusief de geslaagde afwikkeling van invoerberichten, waardoor er slechts één verwerking nodig is. Dit transactiemodel is een robuuste basis voor het patroon van [compenserende transacties](/azure/architecture/patterns/compensating-transaction) in de grotere oplossingscontext. 
* *Berichtsessies*. Implementeer een grootschalige coördinatie van werkstromen en gemultiplexte overdrachten waarvoor strikte berichtordening of berichtuitstel vereist is.

Als u bekend bent met andere berichtenbrokers zoals Apache ActiveMQ, zijn Service Bus-concepten vergelijkbaar met wat u al kent. Omdat Service Bus een PaaS-product (Platform-as-a-Service) is, is een belangrijk verschil dat u zich geen zorgen hoeft te maken over de volgende acties. Azure voert deze taken voor u uit. 

- Logboeken plaatsen en schijfruimte beheren
- Back-ups afhandelen
- Patches toepassen op de besturingssystemen of de producten
- Hardwarestoringen afhandelen 
- Failover naar een reservecomputer uitvoeren

## <a name="compliance-with-standards-and-protocols"></a>Naleving van standaarden en protocollen

Het primaire wire-protocol voor Service Bus is [Advanced Messaging Queueing Protocol (AMQP) 1.0](service-bus-amqp-overview.md), een open ISO/IEC-standaard. Hiermee kunnen klanten toepassingen schrijven die werken met Service Bus en on-premises brokers, zoals ActiveMQ of RabbitMQ. De [AMQP-protocolhandleiding](service-bus-amqp-protocol-guide.md) bevat gedetailleerde informatie voor het geval u een dergelijke abstractie wilt maken.

[Service Bus Premium](service-bus-premium-messaging.md) is volledig compatibel met de API van Java/Jakarta EE [Java Message Service (JMS) 2.0](how-to-use-java-message-service-20.md). En Service Bus Standard biedt ondersteuning voor de JMS 1.1-subset die op wachtrijen gericht is. JMS is een algemene abstractie voor berichtenbrokers en kan worden geïntegreerd met veel toepassingen en frameworks, waaronder het populaire Spring-framework. Als u van een andere broker wilt overschakelen naar Azure Service Bus, hoeft u alleen de topologie van wachtrijen en onderwerpen opnieuw te maken en de afhankelijkheden en configuratie van de clientprovider te wijzigen. Zie de [ActiveMQ-migratiehandleiding](migrate-jms-activemq-to-servicebus.md) voor een voorbeeld.

## <a name="concepts-and-terminology"></a>Concepten en terminologie 
In deze sectie worden de concepten en terminologie van Service Bus besproken.

### <a name="namespaces"></a>Naamruimten
Een naamruimte is een container voor alle berichtenonderdelen. Er kunnen zich meerdere wachtrijen en onderwerpen in één naamruimte bevinden, en naamruimten fungeren vaak als toepassingscontainers. 

Een naamruimte kan worden vergeleken met een "server" in de terminologie van andere brokers, maar de concepten zijn niet volledig gelijkwaardig. Een Service Bus-naamruimte is uw eigen capaciteitssegment van een groot cluster dat uit tientallen allemaal actieve virtuele machines bestaat. Deze kan eventueel drie [Azure-beschikbaarheidszones](../availability-zones/az-overview.md) omvatten. Daarom krijgt u alle voordelen van beschikbaarheid en robuustheid van het uitvoeren van de berichtenbroker op een enorme schaal. En u hoeft zich geen zorgen te maken over de onderliggende complexiteiten. Service Bus is "serverloze" berichtafhandeling.

### <a name="queues"></a>Wachtrijen
Berichten worden verzonden naar en ontvangen van *wachtrijen*. Wachtrijen slaan berichten op totdat de ontvangende toepassing ze kan ontvangen en verwerken.

![Wachtrij](./media/service-bus-messaging-overview/about-service-bus-queue.png)

Berichten in wachtrijen worden bij ontvangst geordend en voorzien van een tijdstempel. Als het bericht eenmaal door de broker is geaccepteerd, wordt het altijd opgeslagen in drievoudig-redundante opslag, verspreid over beschikbaarheidszones als de naamruimtezone is ingeschakeld. Service Bus houdt berichten nooit in het geheugen of vluchtige opslag nadat deze aan de client als geaccepteerd zijn gerapporteerd.

Berichten worden bezorgd in *pull*-modus, waarbij berichten alleen op aanvraag worden geleverd. In tegenstelling tot het busy-pollingmodel van sommige andere cloudwachtrijen, kan de pull-bewerking lang duren en pas worden voltooid als er een bericht beschikbaar is. 

### <a name="topics"></a>Onderwerpen

U kunt ook *onderwerpen* gebruiken voor het verzenden en ontvangen van berichten. Daar waar een wachtrij vaak wordt gebruikt voor punt-naar-punt communicatie, zijn onderwerpen handig in scenario's met publiceren/abonneren.

![Onderwerp](./media/service-bus-messaging-overview/about-service-bus-topic.png)

Onderwerpen kunnen meerdere, onafhankelijke abonnementen hebben, die aan het onderwerp worden gekoppeld en anders exact als wachtrijen van de ontvangerzijde werken. Een abonnee van een onderwerp kan een kopie ontvangen van elk bericht dat naar dat onderwerp wordt verzonden. Abonnementen worden entiteiten genoemd. Abonnementen duren standaard lang, maar kunnen worden geconfigureerd om te verlopen en vervolgens automatisch te worden verwijderd. Met behulp van de JMS-API biedt Service Bus Premium u ook de mogelijkheid om kortlopende abonnementen te maken die bestaan voor de duur van de verbinding.

U kunt regels definiëren voor een abonnement. Een abonnementsregel bevat een *filter* om een voorwaarde te definiëren voor het bericht dat moet worden gekopieerd naar het abonnement en een optionele *actie* die metagegevens van berichten kan wijzigen. Zie [Onderwerpfilters en acties](topic-filters.md) voor meer informatie. Deze functie is handig in de volgende scenario's:

- U wilt niet dat een abonnement alle berichten ontvangt die naar een onderwerp worden gestuurd.
- U wilt berichten met extra metagegevens markeren wanneer deze via een abonnement worden doorgegeven.

## <a name="advanced-features"></a>Geavanceerde functies

Service Bus beschikt over geavanceerde functies waarmee u complexere problemen met berichtenuitwisseling kunt oplossen. In de volgende secties worden verschillende van deze functies beschreven.

### <a name="message-sessions"></a>Berichtsessies

Als u er zeker van wilt zijn dat berichten in Service Bus op basis van FIFO (first in, first out) worden verwerkt, moet u sessies gebruiken. Berichtsessies maken exclusieve, geordende verwerking van niet-gekoppelde reeksen gerelateerde berichten mogelijk. Voor het afhandelen van sessies in grootschalige systemen met hoge beschikbaarheid biedt de sessiefunctie ook de mogelijkheid om de sessiestatus op te slaan, zodat sessies veilig tussen handlers kunnen worden verplaatst. Zie [Message sessions: first in, first out (FIFO)](message-sessions.md) (Berichtensessies: first in, first out (FIFO)) voor meer informatie.

### <a name="autoforwarding"></a>Automatisch doorsturen

De functie voor automatisch doorsturen koppelt een wachtrij of abonnement aan een andere wachtrij of een ander onderwerp binnen dezelfde naamruimte. Wanneer u deze functie gebruikt, verplaatst Service Bus berichten van een wachtrij of abonnement automatisch naar een doelwachtrij of -onderwerp. Alle dergelijke verplaatsingen worden transactioneel uitgevoerd. Zie [Chaining Service Bus entities with autoforwarding](service-bus-auto-forwarding.md) (Service Bus-entiteiten koppelen met automatisch doorsturen) voor meer informatie.

### <a name="dead-letter-queue"></a>Wachtrij voor onbestelbare berichten

Aan alle Service Bus-wachtrijen en -onderwerpabonnementen is een wachtrij met onbestelbare berichten (DLQ) gekoppeld. Een DLQ bevat berichten die voldoen aan de volgende criteria: 

- Ze kunnen niet bij elke ontvanger worden afgeleverd.
- Er is een time-out opgetreden.
- Ze worden expliciet door de ontvangende toepassing ter zijde geplaatst. 

Berichten in de wachtrij met onbestelbare meldingen worden vermeld met de reden waarom ze daar zijn geplaatst. De wachtrij voor onbestelbare berichten heeft een speciaal eindpunt, maar is verder zoals een gewone wachtrij. Een toepassing of een hulpprogramma kan door een DLQ bladeren of een bericht eruit verwijderen. U kunt een wachtrij met onbestelbare berichten ook automatisch doorsturen. Zie [Overview of Service Bus dead-letter queues](service-bus-dead-letter-queues.md) (Overzicht van Service Bus-wachtrijen voor onbestelbare berichten) voor meer informatie.

### <a name="scheduled-delivery"></a>Geplande bezorging

U kunt berichten verzenden naar een wachtrij of onderwerp voor vertraagde verwerking, waarbij een tijd wordt ingesteld wanneer het bericht beschikbaar wordt voor gebruik. Geplande berichten kunnen ook worden geannuleerd. Zie [Geplande berichten](message-sequencing.md#scheduled-messages) voor meer informatie.

### <a name="message-deferral"></a>Berichten uitstellen

Een wachtrij- of abonnementsclient kan het ophalen van een ontvangen bericht tot een later tijdstip uitstellen. Het bericht is mogelijk buiten een verwachte volgorde geplaatst en de client wil wachten totdat er een ander bericht wordt ontvangen. Uitgestelde berichten blijven in de wachtrij of het abonnement en moeten expliciet opnieuw worden geactiveerd met behulp van het door de service toegewezen volgnummer. Zie [Berichten uitstellen](message-deferral.md) voor meer informatie.

### <a name="batching"></a>Batchverwerking

Met batchverwerking aan clientzijde kan een wachtrij of een onderwerpclient een set berichten verzamelen en deze gezamenlijk overdragen. Vaak wordt dit gedaan om bandbreedte te besparen of de doorvoer te vergroten. Zie [Client-side batching](service-bus-performance-improvements.md#client-side-batching) (Batchverwerking aan de clientzijde) voor meer informatie.

### <a name="transactions"></a>Transacties

Een transactie groepeert twee of meer bewerkingen in een *uitvoeringsbereik*. Service Bus biedt u de mogelijkheid bewerkingen te groeperen voor meerdere berichtentiteiten binnen het bereik van één transactie. Een berichtentiteit kan een wachtrij, onderwerp of abonnement zijn. Zie [Overview of Service Bus transaction processing](service-bus-transactions.md) (Overzicht van transactieverwerking in Service Bus) voor meer informatie.

### <a name="autodelete-on-idle"></a>Automatisch verwijderen bij inactiviteit
Automatisch verwijderen bij inactiviteit houdt in dat u een interval voor inactiviteit kunt opgeven waarna de wachtrij of het onderwerpabonnement automatisch wordt verwijderd. De minimale duur is vijf minuten. 

### <a name="duplicate-detection"></a>Detectie van duplicaten
Met de functie voor detectie van duplicaten kan de afzender hetzelfde bericht opnieuw verzenden en moet de broker een mogelijk duplicaat verwijderen. Zie [Detectie van duplicaten](duplicate-detection.md) voor meer informatie.

### <a name="geo-disaster-recovery"></a>Geo-noodherstel

Wanneer er sprake is van uitval in een Azure-regio, zorgt de functie voor geo-herstel na een noodgeval ervoor dat de gegevensverwerking kan plaatsvinden in een andere regio of in een ander datacentrum. De functie houdt een structurele mirror bij van een naamruimte die beschikbaar is in de secundaire regio en staat toe dat de naamruimte-id naar de secundaire naamruimte overschakelt. Berichten die al zijn geplaatst, blijven in de voormalige primaire naamruimte voor herstel als de beschikbaarheid is hersteld. Zie [Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md) (Geo-herstel na noodgeval in Azure Service Bus) voor meer informatie.

### <a name="security"></a>Beveiliging

Service Bus biedt standaard ondersteuning voor [AMQP 1.0](service-bus-amqp-overview.md) en [HTTP- of REST](/rest/api/servicebus/)-protocollen en hun respectieve beveiligingsfaciliteiten, met inbegrip van beveiliging op transportniveau (Transport Level Security, TLS). Clients kunnen worden gemachtigd voor toegang met behulp van [Shared Access Signature](service-bus-sas.md)- of [Azure Active Directory](service-bus-authentication-and-authorization.md)-beveiliging op basis van rollen. 

Service Bus biedt [beveiligingsfuncties](network-security.md) zoals IP-firewall en integratie met virtuele netwerken voor beveiliging tegen ongewenst verkeer. 

## <a name="client-libraries"></a>Clientbibliotheken

Volledig ondersteunde Service Bus-clientbibliotheken zijn beschikbaar via de Azure SDK.

- [Azure Service Bus voor .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus-bibliotheken voor Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Azure Service Bus-provider voor Java JMS 2.0](how-to-use-java-message-service-20.md)
- [Azure Service Bus-modules voor JavaScript en TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus-bibliotheken voor Python](/python/api/overview/azure/servicebus?preserve-view=true)

[Het primaire protocol van Azure Service Bus is AMQP 1.0](service-bus-amqp-overview.md) en kan worden gebruikt vanuit elke client die compatibel is met het AMQP 1.0-protocol. Verschillende open source AMQP-clients bevatten voorbeelden die expliciet interoperabiliteit met Service Bus illustreren. Raadpleeg de [Handleiding bij het AMQP 1.0-protocol](service-bus-amqp-protocol-guide.md) voor meer informatie over het rechtstreeks gebruiken van Service Bus-functies met AMQP 1.0-clients.

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="integration"></a>Integratie

Service Bus is volledig geïntegreerd met veel Microsoft- en Azure-services, bijvoorbeeld:

* [Event Grid](service-bus-to-event-grid-integration-example.md)
* [Logic Apps](../connectors/connectors-create-api-servicebus.md)
* [Azure Functions](../azure-functions/functions-bindings-service-bus.md)
* [Power Platform](../connectors/connectors-create-api-servicebus.md)
* [Dynamics 365](/dynamics365/fin-ops-core/dev-itpro/business-events/how-to/how-to-servicebus)
* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen om aan de slag te gaan met de Service Bus-berichtenservice:

* Zie [Vergelijking van services](../event-grid/compare-messaging-services.md?toc=%2fazure%2fservice-bus-messaging%2ftoc.json&bc=%2fazure%2fservice-bus-messaging%2fbreadcrumb%2ftoc.json) als u Azure-berichtenservices wilt vergelijken.
* Probeer de quickstarts voor [.NET](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md) of [JMS](service-bus-java-how-to-use-jms-api-amqp.md).
* Zie [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases) voor het beheren van Service Bus-resources.
* Zie [Prijzen van Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) voor meer informatie over de Standard- en Premium-categorieën en de bijbehorende tarieven.
* Zie [Premium Messaging](https://techcommunity.microsoft.com/t5/Service-Bus-blog/Premium-Messaging-How-fast-is-it/ba-p/370722) voor meer informatie over de prestaties en latentie voor de Premium-categorie.