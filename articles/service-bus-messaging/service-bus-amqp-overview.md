---
title: Overzicht van AMQP 1,0 in Azure Service Bus
description: Lees hoe Azure Service Bus Advanced Message Queueing Protocol (AMQP) ondersteunt, een open standaard protocol.
ms.topic: article
ms.date: 02/17/2021
ms.openlocfilehash: b2ca126312f5fc3da2a7ff6e20a9ade252f489f1
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653880"
---
# <a name="amqp-10-support-in-service-bus"></a>Ondersteuning voor AMQP 1,0 in Service Bus
De Azure Service Bus-Cloud service maakt gebruik van het [Advanced Message queueing Protocol (AMQP) 1,0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html) als het belangrijkste communicatie middel. Micro soft heeft partners in de branche, zowel klanten als leveranciers van concurrerende Messa ging-makelaars, ontwikkeld om AMQP in het afgelopen decennium te ontwikkelen en ontwikkelen, met nieuwe uitbrei dingen die worden ontwikkeld in het [Oasis AMQP Technical Committee](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp). AMQP 1,0 is een ISO-en IEC-norm ([iso 19464:20149](https://www.iso.org/standard/64955.html)). 

AMQP stelt u in staat om platformoverschrijdende, hybride toepassingen te bouwen met behulp van een leverancier-neutraal en implementatie-neutraal open standaard protocol. U kunt toepassingen bouwen met behulp van onderdelen die zijn gebouwd met behulp van verschillende talen en frameworks, en die worden uitgevoerd op verschillende besturings systemen. Al deze onderdelen kunnen verbinding maken met Service Bus en gestructureerde bedrijfs berichten efficiënt en met volledige betrouw baarheid uitwisselen.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Inleiding: wat is AMQP 1,0 en waarom is het belang rijk?
Normaal gesp roken hebben op berichten georiënteerde middlewareproducten een eigen protocol gebruikt voor communicatie tussen client toepassingen en makelaars. Dit betekent dat als u de Messa ging-Broker van een bepaalde leverancier hebt geselecteerd, u de bibliotheken van die leverancier moet gebruiken om uw client toepassingen te verbinden met die Broker. Dit leidt tot een zekere mate van afhankelijkheid van die leverancier, omdat het voor de poort van een toepassing naar een ander product nood zakelijk is dat code wijzigingen in alle verbonden toepassingen worden aangebracht. In de Java-Community zijn taalspecifieke API-standaarden zoals Java Message Service (JMS) en de abstractie van het lente-Framework minder goed, maar hebben ze een zeer smal functie bereik en kunnen ontwikkel aars met andere talen worden uitgesloten.

Bovendien is het maken van de communicatie van Messa ging-Brokers van verschillende leveranciers lastig. Dit vereist meestal bridging op toepassings niveau om berichten van het ene naar het andere systeem te verplaatsen en te vertalen tussen hun eigen bericht indelingen. Dit is een algemene vereiste. bijvoorbeeld wanneer u een nieuwe, geïntegreerde interface moet bieden voor oudere systemen of als u de IT-systemen wilt integreren na een fusie. Met AMQP kunt u rechtstreeks verbinding maken met Brokers, bijvoorbeeld met behulp van routers zoals [Apache Qpid Dispatch router](https://qpid.apache.org/components/dispatch-router/index.html) of Broker-native "shovels", zoals het [RabbitMQ](service-bus-integrate-with-rabbitmq.md).

De software-industrie is een bedrijf dat snel kan worden verplaatst. nieuwe programmeer talen en toepassings raamwerken worden in een soms bewildering tempo geïntroduceerd. Op dezelfde manier kunnen de vereisten van IT-systemen in de loop van tijd en ontwikkel aars profiteren van de nieuwste platform functies. Soms wordt deze platformen echter niet ondersteund door de leverancier van de geselecteerde berichten. Als de berichten protocollen eigendom zijn, is het niet mogelijk dat anderen bibliotheken voor deze nieuwe platforms bieden. Daarom moet u benaderingen gebruiken zoals het maken van gateways of bruggen waarmee u het bericht product kunt blijven gebruiken.

De ontwikkeling van de Advanced Message Queueing Protocol (AMQP) 1,0 is gemotiveerd door deze problemen. Het is afkomstig van de Morgan-oplossing van JP, wie, zoals de meeste ondernemingen van financiële dienst verlening, zware gebruikers van de bericht georiënteerde middleware. Het doel is eenvoudig: om een open-standaard berichten protocol te maken dat het mogelijk maakt om op berichten gebaseerde toepassingen te bouwen met behulp van onderdelen die zijn gemaakt met verschillende talen, frameworks en besturings systemen, waarbij alles wordt gebruikt voor het gebruik van de beste onderdelen van een assortiment leveranciers.

## <a name="amqp-10-technical-features"></a>Technische functies van AMQP 1,0
AMQP 1,0 is een efficiënt, betrouwbaar, Wire-level berichten protocol dat u kunt gebruiken voor het bouwen van robuuste, platform onafhankelijke toepassingen voor bericht verwerking. Het protocol heeft een eenvoudig doel: voor het definiëren van de mechanismen van de veilige, betrouw bare en efficiënte overdracht van berichten tussen twee partijen. De berichten zelf worden gecodeerd met behulp van een draag bare gegevens weergave waarmee heterogene afzenders en ontvangers gestructureerde zakelijke berichten kunnen uitwisselen met volledige betrouw baarheid. Hier volgt een overzicht van de belangrijkste functies:

* **Efficiënt**: AMQP 1,0 is een verbindingsgeoriënteerd protocol dat gebruikmaakt van een binaire code ring voor de instructies van het protocol en de bedrijfs berichten die worden overgedragen. Het bevat geavanceerde schema's voor stroom beheer om het gebruik van het netwerk en de verbonden onderdelen te maximaliseren. Het protocol is zo ontworpen dat het een evenwicht heeft tussen efficiëntie, flexibiliteit en interoperabiliteit.
* **Betrouw bare**: met het AMQP 1,0-protocol kunnen berichten worden uitgewisseld met een bereik van betrouwbaarheids garanties, van brand en voor verg eten tot betrouw bare, precies eenmaal bevestigde levering.
* **Flexibel**: AMQP 1,0 is een flexibel protocol dat kan worden gebruikt om verschillende topologieën te ondersteunen. Hetzelfde protocol kan worden gebruikt voor communicatie van client naar client, client naar Broker en Broker-naar-Broker.
* **Broker-model onafhankelijk**: de AMQP 1,0-specificatie maakt geen vereisten voor het berichten model dat wordt gebruikt door een Broker. Dit betekent dat het mogelijk is om eenvoudig AMQP 1,0-ondersteuning toe te voegen aan bestaande Messa ging-brokers.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1,0 is een standaard (met een kapitaal)
AMQP 1,0 is een internationale standaard, goedgekeurd door ISO en IEC als ISO/IEC 19464:2014.

AMQP 1,0 is in ontwikkeling sinds 2008 door een kern groep van meer dan 20 bedrijven, zowel technologie leveranciers als eind gebruikers. Gedurende die tijd hebben gebruikers bedrijven hun eigen bedrijfs vereisten bijgedragen en hebben de technologie leveranciers het protocol ontwikkeld om aan deze vereisten te voldoen. Tijdens het hele proces hebben leveranciers gedeeld in workshops waarin ze zijn gewerkt om de interoperabiliteit tussen hun implementaties te valideren.

In oktober 2011 werd het ontwikkelings werk overgegaan naar een technisch comité binnen de organisatie voor de vervroeging van Structured Information Standards (OASIS) en de OASIS AMQP 1,0 Standard is uitgebracht in oktober 2012. De volgende ondernemingen hebben in het technisch comité gedeeld tijdens de ontwikkeling van de standaard:

* **Technologie leveranciers**: Axway software, Huawei Technologies, IIT software, INETCO Systems, Kaazing, micro soft, Mitre Corporation, Primeton Technologies, progressief software, Red Hat, Sita, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Gebruikers bedrijven**: Bank of America, Credit Suisse, Deutsche boerse, Goldman Sachs, JPMorgan-nadoende.

De huidige stoelen van het [Technical Committee Oasis AMQP](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) zijn Red Hat en micro soft.

Enkele van de meest voorkomende voor delen van open standaarden zijn:

* Minder kans op het vergren delen van een leverancier
* Interoperabiliteit
* Brede Beschik baarheid van bibliotheken en hulpprogram ma's
* Bescherming tegen economische veroudering
* Beschik baarheid van deskundig personeel
* Lager en beheersbaar risico

## <a name="amqp-10-and-service-bus"></a>AMQP 1,0 en Service Bus
AMQP 1,0-ondersteuning in Azure Service Bus houdt in dat u gebruik kunt maken van de Service Bus Queuing en de functies voor het publiceren en abonneren van Brokered Messaging vanuit diverse platformen met behulp van een efficiënt binair protocol. Daarnaast kunt u toepassingen bouwen die bestaan uit onderdelen die zijn gebouwd met behulp van een combi natie van talen, frameworks en besturings systemen.

In de volgende afbeelding ziet u een voorbeeld implementatie waarbij Java-clients die worden uitgevoerd op Linux, worden geschreven met behulp van de standaard JMS-API (Java Message Service) en .NET-clients die worden uitgevoerd op Windows, Exchange-berichten via Service Bus met AMQP 1,0.

![Diagram met een Service Bus het uitwisselen van berichten met twee Linux-omgevingen en twee Windows-omgevingen.][0]

**Afbeelding 1: voor beeld van implementatie scenario met verschillende platform berichten met behulp van Service Bus en AMQP 1,0**

Alle ondersteunde Service Bus-client bibliotheken die beschikbaar zijn via de Azure SDK gebruiken AMQP 1,0.

- [Azure Service Bus voor .NET](/dotnet/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus-bibliotheken voor Java](/java/api/overview/azure/servicebus?preserve-view=true)
- [Azure Service Bus-provider voor Java JMS 2.0](how-to-use-java-message-service-20.md)
- [Azure Service Bus-modules voor JavaScript en TypeScript](/javascript/api/overview/azure/service-bus?preserve-view=true)
- [Azure Service Bus-bibliotheken voor Python](/python/api/overview/azure/servicebus?preserve-view=true)

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

Daarnaast kunt u Service Bus gebruiken vanuit elke AMQP 1,0 compatibele protocol stack:

[!INCLUDE [messaging-oss-amqp-stacks.md](../../includes/messaging-oss-amqp-stacks.md)]

## <a name="summary"></a>Samenvatting
* AMQP 1,0 is een open, betrouw bare berichten protocol dat u kunt gebruiken voor het bouwen van platformoverschrijdende, hybride toepassingen. AMQP 1,0 is een OASIS-standaard.

## <a name="next-steps"></a>Volgende stappen
Wilt u meer weten? Ga naar de volgende koppelingen:

* [Service Bus van .NET gebruiken met AMQP]
* [Service Bus van Java gebruiken met AMQP]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Service Bus van .NET gebruiken met AMQP]: service-bus-amqp-dotnet.md
[Service Bus van Java gebruiken met AMQP]: ./service-bus-java-how-to-use-jms-api-amqp.md

