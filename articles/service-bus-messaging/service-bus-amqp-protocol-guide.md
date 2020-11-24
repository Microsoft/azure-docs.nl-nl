---
title: AMQP 1,0 in Azure Service Bus en Event Hubs protocol handleiding | Microsoft Docs
description: Protocol gids voor expressies en beschrijving van AMQP 1,0 in Azure Service Bus en Event Hubs
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e001327c2c7da08cb9a3552f97fc9a7d8b7921a2
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95736711"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1,0 in Azure Service Bus en Event Hubs protocol gids

Het Advanced Message queueing Protocol 1,0 is een gestandaardiseerd protocol voor framing en overdracht voor asynchrone, veilige en betrouw bare overdracht van berichten tussen twee partijen. Het is het primaire protocol van Azure Service Bus Messa ging en Azure Event Hubs.  

AMQP 1,0 is het resultaat van een brede samen werking bij het samen stellen van middleware-leveranciers, zoals micro soft en Red Hat, met veel gebruikers voor het door geven van berichten, zoals JP Morgan, die de financiële dienst verleners vertegenwoordigen. Het technisch normalisatie forum voor het AMQP-protocol en de extensie specificaties is OASIS en heeft een formele goed keuring behaald als een internationale norm als ISO/IEC 19494:2014. 

## <a name="goals"></a>Doelstellingen

In dit artikel vindt u een overzicht van de basis concepten van de AMQP 1,0-bericht specificatie over de extensie specificaties die zijn ontwikkeld door het [technisch Oasis AMQP Technical Committee](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) en wordt uitgelegd hoe Azure service bus implementeert en bouwt op basis van deze specificaties.

Het doel is voor alle ontwikkel aars die een bestaande AMQP 1,0-client stack gebruiken op elk platform om te kunnen communiceren met Azure Service Bus via AMQP 1,0.

Algemene AMQP 1,0-stacks van algemeen gebruik, zoals [Apache Qpid Proton](https://qpid.apache.org/proton/index.html) of [AMQP.net Lite](https://github.com/Azure/amqpnetlite), implementeren alle kern AMQP 1,0-protocol elementen zoals sessies of koppelingen. Deze basis elementen worden soms verpakt met een API van een hoger niveau. Apache Proton biedt zelfs twee, de verplichte Mess enger-API en de reactieve reactor-API.

In de volgende bespreking gaan we ervan uit dat het beheer van AMQP-verbindingen,-sessies en-koppelingen en de verwerking van frame overdrachten en datatransport besturing worden verwerkt door de respectieve stack (zoals Apache Proton-C) en niet veel als enige specifieke aandacht van toepassings ontwikkelaars. We gaan abstracten uitgaan van een paar API-primitieven, zoals de mogelijkheid om verbinding te maken, en om een vorm van de abstractie objecten van de *afzender* en de *ontvanger* te maken, die respectievelijk een of meer vorm `send()` en `receive()` bewerkingen hebben.

Bij het bespreken van geavanceerde mogelijkheden van Azure Service Bus, zoals het bladeren door berichten of het beheer van sessies, worden deze functies uitgelegd in AMQP-voor waarden, maar ook als een gelaagde pseudo-implementatie boven op de API-abstractie.

## <a name="what-is-amqp"></a>Wat is AMQP?

AMQP is een framing-en overdrachts protocol. Framing houdt in dat het een structuur biedt voor binaire gegevens stromen die in een van beide richtingen van een netwerk verbinding stromen. De structuur biedt een overzicht van de afzonderlijke gegevens blokken ( *frames* genoemd) die moeten worden uitgewisseld tussen de verbonden partijen. De overdrachts mogelijkheden zorgen ervoor dat beide communicerende partijen een gemeen schappelijk inzicht kunnen krijgen over wanneer frames moeten worden overgedragen, en wanneer overdrachten worden beschouwd als voltooid.

In tegens telling tot eerdere verlopen concept versies die zijn geproduceerd door de AMQP-werk groep en die nog steeds worden gebruikt door een paar bericht Brokers, schrijft de laatste en gestandaardiseerde AMQP 1,0-protocol niet de aanwezigheid van een Message Broker of een bepaalde topologie voor entiteiten binnen een Message Broker.

Het protocol kan worden gebruikt voor symmetrische peer-to-peer-communicatie, voor interactie met bericht Brokers die ondersteuning bieden voor wacht rijen en het publiceren/abonneren van entiteiten, zoals Azure Service Bus. Het kan ook worden gebruikt voor interactie met de infra structuur voor berichten waarbij de interactie patronen verschillen van de normale wacht rijen, zoals het geval is bij Azure Event Hubs. Een event hub fungeert als een wachtrij wanneer er gebeurtenissen naar worden verzonden, maar fungeert meer als een seriële opslag service wanneer er gebeurtenissen van worden gelezen. het lijkt iets op een tape station. De client kiest een offset in de beschik bare gegevens stroom en vervolgens worden alle gebeurtenissen van die offset naar de meest recente Beschik baarheid verzonden.

Het AMQP 1,0-protocol is uitbreidbaar, waardoor er meer specificaties kunnen worden ontwikkeld om de mogelijkheden te verbeteren. De drie extensie specificaties die in dit document worden beschreven, illustreren dit. Voor communicatie over een bestaande HTTPS/websockets-infra structuur is het configureren van de systeem eigen AMQP TCP-poorten mogelijk lastig. Een bindings specificatie definieert hoe AMQP worden gelaagd via websockets. Voor interactie met de Messa ging-infra structuur in een aanvraag/antwoord mode voor beheer doeleinden of om geavanceerde functionaliteit te bieden, definieert de AMQP Management-specificatie de vereiste basis interactie primitieven. Voor de integratie van federatieve verificatie modellen definieert de AMQP op claims gebaseerde beveiliging het koppelen en vernieuwen van autorisatie tokens die zijn gekoppeld aan koppelingen.

## <a name="basic-amqp-scenarios"></a>Eenvoudige AMQP-scenario's

In deze sectie wordt het basis gebruik van AMQP 1,0 met Azure Service Bus uitgelegd. Dit omvat het maken van verbindingen, sessies en koppelingen, en het overdragen van berichten naar en van Service Bus entiteiten zoals wacht rijen, onderwerpen en abonnementen.

De meest gezaghebbende bron om meer te weten te komen over de manier waarop AMQP werkt, is de [AMQP 1,0-specificatie](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html), maar de specificatie werd geschreven om de implementatie nauw keurig te begeleiden en niet om het protocol te leren kennen. Deze sectie richt zich op het introduceren van zoveel terminologie als nodig is voor het beschrijven van de manier waarop Service Bus AMQP 1,0 gebruikt. Voor een uitgebreidere inleiding tot AMQP, evenals een bredere bespreking van AMQP 1,0, kunt u [deze video cursus][this video course]bekijken.

### <a name="connections-and-sessions"></a>Verbindingen en sessies

AMQP roept *de Program ma's* voor communicatie Programma's aan; Deze bevatten *knoop punten*, die de communicerende entiteiten binnen deze containers zijn. Een wachtrij kan een dergelijk knoop punt zijn. AMQP maakt multiplexing mogelijk, waardoor een enkele verbinding kan worden gebruikt voor veel communicatie paden tussen knoop punten. een toepassings-client kan bijvoorbeeld gelijktijdig van de ene wachtrij ontvangen en via dezelfde netwerk verbinding naar een andere wachtrij verzenden.

![Diagram waarin sessies en verbindingen tussen containers worden weer gegeven.][1]

De netwerk verbinding is daarom verankerd op de container. Het wordt gestart door de container in de client functie en maakt een uitgaande TCP-socket verbinding met een container in de ontvanger receive die binnenkomende TCP-verbindingen luistert en accepteert. De verbindings-Handshake omvat het onderhandelen over de Protocol versie, het declareren of onderhandelen van het gebruik van beveiliging op transport niveau (TLS/SSL) en een verificatie/autorisatie-Handshake bij het verbindings bereik dat is gebaseerd op SASL.

Voor Azure Service Bus is het gebruik van TLS te allen tijde vereist. Het ondersteunt verbindingen via TCP-poort 5671, waarbij de TCP-verbinding voor het eerst wordt overlapt met TLS voordat de AMQP-protocol-handshake wordt ingevoerd en ook verbindingen via TCP-poort 5672 worden ondersteund, waarbij de server onmiddellijk een verplichte upgrade van de verbinding met TLS biedt met behulp van het door AMQP voorgeschreven model. Met de AMQP-websockets binding wordt een tunnel via TCP-poort 443 gemaakt die vervolgens overeenkomt met AMQP 5671-verbindingen.

Nadat u de verbinding en TLS hebt ingesteld, biedt Service Bus twee opties voor SASL-mechanisme:

* SASL PLAIN wordt doorgaans gebruikt om gebruikers naam-en wachtwoord referenties door te geven aan een server. Service Bus heeft geen accounts, maar met de naam [Shared Access Security Rules](service-bus-sas.md)die rechten verlenen en aan een sleutel zijn gekoppeld. De naam van een regel wordt gebruikt als de gebruikers naam en de sleutel (als base64-gecodeerde tekst) wordt gebruikt als het wacht woord. De rechten die zijn gekoppeld aan de gekozen regel bepalen welke bewerkingen zijn toegestaan voor de verbinding.
* SASL ANONYMOUS wordt gebruikt voor het overs laan van SASL-autorisatie wanneer de client het CBS-model (Claims-based-Security) wil gebruiken dat later wordt beschreven. Met deze optie kan een client verbinding anoniem worden ingesteld voor een korte periode waarin de client alleen kan communiceren met het CBS-eind punt en de CBS-Handshake moet worden voltooid.

Nadat de transport verbinding tot stand is gebracht, declareren de containers elk de maximum grootte van het frame die ze kunnen verwerken en na een time-out voor inactiviteit, wordt de verbinding verbroken als er geen activiteit is voor het verbinden.

Ze declareren ook hoeveel gelijktijdige kanalen worden ondersteund. Een kanaal is een virtueel overdrachts traject van één locatie, bovenop de verbinding. Een sessie haalt een kanaal van elk van de onderling verbonden containers op om een bidirectionele communicatie traject te maken.

Sessies hebben een flow control model op basis van een venster; Wanneer een sessie wordt gemaakt, declareert elke partij hoeveel frames het kan accepteren in het venster ontvangen. Wanneer de partijen Exchange-frames gebruiken, vullen de overgebrachte frames het venster en de overdracht stoppen wanneer het venster vol is en totdat het venster opnieuw wordt ingesteld of uitgevouwen met behulp van de *flow Performative* (*PERFORMATIVE* is de AMQP-term voor op protocol niveau geruilde bewegingen tussen de twee partijen).

Dit model op basis van een venster is ongeveer hetzelfde als het TCP-concept van een datatransport besturing op basis van een venster, maar op het sessie niveau binnen de socket. Het concept van het protocol dat meerdere gelijktijdige sessies mogelijk maakt, bestaat, zodat het verkeer met hoge prioriteit een vertraging heeft onderlopend normaal verkeer, zoals een snelle Lane in de weg.

Azure Service Bus gebruikt momenteel slechts één sessie voor elke verbinding. De Service Bus maximale frame grootte is 262.144 bytes (256-K bytes) voor Service Bus standaard. Het is 1.048.576 (1 MB) voor Service Bus Premium en Event Hubs. Service Bus geen specifieke beperkings Vensters op sessie niveau, maar het venster wordt regel matig opnieuw ingesteld als onderdeel van datatransport besturing op koppelings niveau (Zie [de volgende sectie](#links)).

Verbindingen, kanalen en sessies zijn kortstondig. Als de onderliggende verbinding is samengevouwen, worden verbindingen, TLS-tunnel, SASL-autorisatie context en sessies opnieuw tot stand gebracht.

### <a name="amqp-outbound-port-requirements"></a>AMQP voor de uitgaande poort

Clients die gebruikmaken van AMQP-verbindingen via TCP, moeten poorten 5671 en 5672 in de lokale firewall moeten worden geopend. Naast deze poorten is het mogelijk nodig extra poorten te openen als de [EnableLinkRedirect](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect?view=azure-dotnet) -functie is ingeschakeld. `EnableLinkRedirect` is een nieuwe berichten functie waarmee u een hop kunt overs Laan tijdens het ontvangen van berichten, waardoor de door Voer wordt verbeterd. De client begint direct met de back-end-service te communiceren via het poort bereik 104XX, zoals wordt weer gegeven in de volgende afbeelding. 

![Lijst met doel poorten][4]

Een .NET-client zou mislukken met een SocketException (' er is een poging gedaan om toegang te krijgen tot een socket op een manier die niet wordt toegestaan door de toegangs machtigingen ') als deze poorten zijn geblokkeerd door de firewall. De functie kan worden uitgeschakeld door `EnableAmqpLinkRedirect=false` in de Connection String in te stellen, waardoor de clients met de externe service via poort 5671 kunnen communiceren.


### <a name="links"></a>Koppelingen

AMQP brengt berichten over koppelingen over. Een koppeling is een communicatie traject dat is gemaakt over een sessie die het overdragen van berichten in één richting mogelijk maakt. de onderhandeling over de overdrachts status bevindt zich boven de koppeling en bidirectionele tussen de verbonden partijen.

![Scherm opname van een sessie met een koppeling tussen twee containers.][2]

Koppelingen kunnen worden gemaakt door elke container op elk gewenst moment en via een bestaande sessie, waardoor AMQP afwijkt van een groot aantal andere protocollen, waaronder HTTP en MQTT, waarbij het initiëren van transfers en transfer Path een exclusieve bevoegdheid is van de partij die de socket verbinding maakt.

De koppeling voor het initiëren van de container vraagt de tegenovergestelde container om een koppeling te accepteren en kiest een rol van de afzender of ontvanger. Daarom kan een container met behulp van het maken van een eenrichtings-of bidirectionele communicatie paden worden gestart, met het laatste model als paar koppelingen.

Koppelingen hebben de naam en zijn gekoppeld aan knoop punten. Zoals vermeld in het begin, zijn knoop punten de communicerende entiteiten binnen een container.

In Service Bus is een knoop punt rechtstreeks gelijk aan een wachtrij, een onderwerp, een abonnement of een subwachtrij deadletter van een wachtrij of abonnement. De naam van het knoop punt dat in AMQP wordt gebruikt, is daarom de relatieve naam van de entiteit binnen de naam ruimte Service Bus. Als de naam van een wachtrij is `myqueue` , is dat ook de naam van het AMQP-knoop punt. Een onderwerp-abonnement volgt de HTTP API-Conventie door te sorteren in een ' Abonnementen ' resource verzameling. Daarom heeft een abonnement **Sub** in een onderwerp **mytopic** de AMQP-knooppunt naam **mytopic/abonnementen/sub**.

De client waarmee verbinding moet worden gemaakt, is ook vereist voor het gebruik van een lokale knooppunt naam voor het maken van koppelingen. Service Bus is geen voor schriften voor de namen van deze knoop punten en interpreteert deze niet. AMQP 1,0-client stacks gebruiken meestal een schema om ervoor te zorgen dat deze namen van tijdelijke knoop punten uniek zijn binnen het bereik van de client.

### <a name="transfers"></a>Making

Zodra een koppeling tot stand is gebracht, kunnen berichten via die koppeling worden verzonden. In AMQP wordt een overdracht uitgevoerd met een expliciete protocol beweging (de *overdrachts* -Performative) die een bericht verplaatst van afzender naar ontvanger via een koppeling. Een overdracht is voltooid wanneer het ' verrekend ' is, wat inhoudt dat beide partijen een gedeeld memorandum hebben vastgesteld van het resultaat van die overdracht.

![Een diagram van de overdracht van een bericht tussen de afzender en de ontvanger en de bestemming hiervan.][3]

In het meest eenvoudige geval kan de afzender kiezen voor het verzenden van berichten ' vooraf vereffend ', wat betekent dat de client niet geïnteresseerd is in het resultaat en dat de ontvanger geen feedback geeft over het resultaat van de bewerking. Deze modus wordt ondersteund door Service Bus op het niveau van het AMQP-protocol, maar wordt niet weer gegeven in een van de client-Api's.

Het gewone geval is dat berichten niet-vereffend worden verzonden en dat de ontvanger vervolgens aanvaar ding of weigering aanduidt met behulp van de *dispositie* Performative. Weigering treedt op wanneer de ontvanger het bericht om de een of andere reden niet kan accepteren en het afwijzings bericht bevat informatie over de reden. Dit is een fout structuur die is gedefinieerd door AMQP. Als berichten worden afgewezen als gevolg van interne fouten in de Service Bus, retourneert de service extra informatie in die structuur die kan worden gebruikt om diagnostische hints te bieden om personeel te ondersteunen als u ondersteunings aanvragen indient. Later vindt u meer informatie over fouten.

Een speciale vorm van afwijzing is de *uitgebrachte* status, waarmee wordt aangegeven dat de ontvanger geen technische objecten heeft voor de overdracht, maar dat er geen belang is voor het vereffenen van de overdracht. Dit is bijvoorbeeld het geval wanneer een bericht wordt bezorgd bij een Service Bus-client en de client het bericht ' abandon ' kiest omdat het werk niet kan worden uitgevoerd als gevolg van het verwerken van het bericht. de bericht bezorging zelf is niet fout. Een variant van deze status is de *gewijzigde* status, waardoor het bericht kan worden gewijzigd wanneer het wordt losgelaten. Deze status wordt op dit moment niet door Service Bus gebruikt.

De AMQP 1,0-specificatie definieert een verdere status met de naam *Received*, die specifiek helpt om het herstel van koppelingen te verwerken. Met koppelings herstel kunt u de status van een koppeling en eventuele bezorgings aanvragen boven op een nieuwe verbinding en sessie, wanneer de eerdere verbinding en sessie verloren zijn gegaan.

Service Bus biedt geen ondersteuning voor koppelings herstel. Als de client de verbinding met Service Bus met een niet-vereffende bericht overdracht verliezen, gaat de bericht overdracht verloren en moet de client opnieuw verbinding maken, de koppeling herstellen en de overdracht opnieuw proberen.

Zo kunnen Service Bus en Event Hubs ten minste één keer worden ondersteund, waarbij de afzender kan worden gegarandeerd dat het bericht is opgeslagen en geaccepteerd, maar ' slechts eenmaal ' overdrachten op het AMQP niveau, waarbij het systeem zou proberen de koppeling te herstellen en te blijven onderhandelen over de leverings status om te voor komen dat de bericht overdracht wordt verdubbeld.

Om mogelijke dubbele verzen dingen te compenseren, ondersteunt Service Bus duplicaten detectie als een optionele functie in wacht rijen en onderwerpen. Met duplicaten detectie worden de bericht-Id's van alle inkomende berichten geregistreerd tijdens een door de gebruiker gedefinieerd tijd venster en worden alle berichten die met dezelfde bericht-Id's worden verzonden, in dat zelfde venster weer gegeven.

### <a name="flow-control"></a>Stoombeheer

Naast het stroom beheer model op sessie niveau dat eerder is besproken, heeft elke koppeling een eigen stroom beheer model. Met Datatransport besturing op sessie niveau kan de container te allen tijde niet te veel frames tegelijk verwerken, de Datatransport besturing op koppelings niveau zorgt ervoor dat de toepassing wordt belast met het aantal berichten dat via een koppeling moet worden verwerkt en wanneer.

![Scherm opname van een logboek met bron, doel, bron poort, doel poort en protocol naam. In de eerste rij wordt de doel poort 10401 (0x28 A 1) in zwart beschreven.][4]

Bij een koppeling kunnen de overdrachten alleen plaatsvinden wanneer de afzender voldoende *link-tegoed* heeft. Link tegoed is een teller die door de ontvanger wordt ingesteld met behulp van de *flow* Performative, die is gericht op een koppeling. Wanneer de afzender een koppelings tegoed heeft gekregen, wordt geprobeerd dat tegoed te gebruiken door berichten te leveren. Elke bericht bezorging verlaagt het resterende tegoed van de link met 1. Wanneer het tegoed voor de koppeling wordt gebruikt, worden de leveringen gestopt.

Als Service Bus zich in de ontvanger van de receiver bevindt, wordt de afzender direct een tegoed van meer dan links verstrekt, zodat berichten direct kunnen worden verzonden. Als tegoed voor de koppeling wordt gebruikt, stuurt Service Bus af en toe een *stroom* Performative naar de afzender om het tegoed van de link bij te werken.

In de rol afzender verzendt Service Bus berichten om een openstaand link-tegoed te gebruiken.

Een ' Receive '-aanroep op het API-niveau wordt omgezet in een *stroom* Performative die wordt verzonden naar service bus door de client. service bus verbruikt dat tegoed door het eerste beschik bare, niet-vergrendelde bericht uit de wachtrij te vergren delen en te verplaatsen. Als er geen bericht beschikbaar is om te worden geleverd, blijft het openstaande tegoed door een koppeling die is gemaakt met die entiteit, opgeslagen in de volg orde van aankomst, en worden berichten vergrendeld en overgedragen zodra deze beschikbaar komen, om een openstaand tegoed te gebruiken.

De vergren deling van een bericht wordt vrijgegeven wanneer de overdracht wordt vereffend met een van de *geaccepteerde*, *Geweigerde* of *vrijgegeven* statussen van de Terminal. Het bericht wordt van Service Bus verwijderd wanneer de status van de terminal wordt *geaccepteerd*. Het blijft in Service Bus en wordt bij de volgende ontvanger bezorgd wanneer de overdracht een van de andere statussen bereikt. Service Bus verplaatst het bericht automatisch naar de deadletter-wachtrij van de entiteit wanneer het maximum aantal bezorgingen dat is toegestaan voor de entiteit bereikt als gevolg van herhaalde weigeringen of releases.

Hoewel de Service Bus-Api's een dergelijke optie vandaag niet direct beschikbaar maken, kan een AMQP-client met een lager niveau het link-credit model gebruiken om de interactie ' pull-Style ' in te scha kelen voor elke ontvangst aanvraag in een ' push-stijl ' model door een groot aantal koppelings kredieten te verlenen en vervolgens berichten te ontvangen wanneer deze beschikbaar zijn zonder verdere interactie. Push wordt ondersteund via de instellingen van de eigenschap [MessagingFactory. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) of [MessageReceiver. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) . Als ze niet gelijk zijn aan nul, gebruikt de AMQP-client deze als het tegoed van de koppeling.

In deze context is het belang rijk om te begrijpen dat de klok voor het verlopen van de vergren deling van het bericht in de entiteit wordt gestart wanneer het bericht wordt opgehaald van de entiteit, niet wanneer het bericht wordt geplaatst op de kabel. Wanneer de client gereedheid aangeeft om berichten te ontvangen door het verzenden van een koppelings tegoed, wordt verwacht dat er actief berichten over het netwerk worden opgehaald en gereed zijn om ze te verwerken. Anders is het mogelijk dat de bericht vergrendeling is verlopen voordat het bericht zelfs wordt bezorgd. Het gebruik van de Datatransport besturing van de link-tegoed moet rechtstreeks overeenkomen met de onmiddellijke gereedheid om te omgaan met beschik bare berichten die naar de ontvanger worden verzonden.

In samen vatting bieden de volgende secties een schematisch overzicht van de Performative-stroom tijdens verschillende API-interacties. In elke sectie wordt een andere logische bewerking beschreven. Sommige van deze interacties kunnen ' luie ' zijn, wat betekent dat ze alleen kunnen worden uitgevoerd wanneer dat nodig is. Het maken van een bericht afzender kan geen netwerk interactie veroorzaken totdat het eerste bericht wordt verzonden of aangevraagd.

De pijlen in de volgende tabel geven de stroom richting van Performative weer.

#### <a name="create-message-receiver"></a>Bericht ontvanger maken

| Client | Service Bus |
| --- | --- |
| --> koppelen (<br/>name = {naam koppeling},<br/>ingang = {numerieke ingang},<br/>Role =**ontvanger**,<br/>bron = {naam entiteit},<br/>doel = {client koppelings-ID}<br/>) |Client gekoppeld aan entiteit als ontvanger |
| Service Bus antwoorden die het einde van de koppeling koppelen |<--koppelen (<br/>name = {naam koppeling},<br/>ingang = {numerieke ingang},<br/>Role =**Sender**,<br/>bron = {naam entiteit},<br/>doel = {client koppelings-ID}<br/>) |

#### <a name="create-message-sender"></a>Bericht afzender maken

| Client | Service Bus |
| --- | --- |
| --> koppelen (<br/>name = {naam koppeling},<br/>ingang = {numerieke ingang},<br/>Role =**Sender**,<br/>Bron = {client koppelings-ID},<br/>doel = {naam entiteit}<br/>) |Geen actie |
| Geen actie |<--koppelen (<br/>name = {naam koppeling},<br/>ingang = {numerieke ingang},<br/>Role =**ontvanger**,<br/>Bron = {client koppelings-ID},<br/>doel = {naam entiteit}<br/>) |

#### <a name="create-message-sender-error"></a>Afzender van het bericht maken (fout)

| Client | Service Bus |
| --- | --- |
| --> koppelen (<br/>name = {naam koppeling},<br/>ingang = {numerieke ingang},<br/>Role =**Sender**,<br/>Bron = {client koppelings-ID},<br/>doel = {naam entiteit}<br/>) |Geen actie |
| Geen actie |<--koppelen (<br/>name = {naam koppeling},<br/>ingang = {numerieke ingang},<br/>Role =**ontvanger**,<br/>Bron = null,<br/>doel = Null<br/>)<br/><br/><--ontkoppelen (<br/>ingang = {numerieke ingang},<br/>gesloten =**True**,<br/>fout = {fout info}<br/>) |

#### <a name="close-message-receiversender"></a>Bericht ontvanger/afzender sluiten

| Client | Service Bus |
| --- | --- |
| --> loskoppelen (<br/>ingang = {numerieke ingang},<br/>gesloten =**True**<br/>) |Geen actie |
| Geen actie |<--ontkoppelen (<br/>ingang = {numerieke ingang},<br/>gesloten =**True**<br/>) |

#### <a name="send-success"></a>Verzenden (geslaagd)

| Client | Service Bus |
| --- | --- |
| --> overdracht (<br/>delivery-id = {numerieke ingang},<br/>Delivery-tag = {binaire ingang},<br/>afgewikkeld =**False**,, meer =**False**,<br/>status =**Null**,<br/>Resume =**False**<br/>) |Geen actie |
| Geen actie |<--toestand (<br/>Role = ontvanger,<br/>First = {Delivery-ID},<br/>Last = {Delivery-ID},<br/>vereffend =**waar**,<br/>status =**geaccepteerd**<br/>) |

#### <a name="send-error"></a>Verzenden (fout)

| Client | Service Bus |
| --- | --- |
| --> overdracht (<br/>delivery-id = {numerieke ingang},<br/>Delivery-tag = {binaire ingang},<br/>afgewikkeld =**False**,, meer =**False**,<br/>status =**Null**,<br/>Resume =**False**<br/>) |Geen actie |
| Geen actie |<--toestand (<br/>Role = ontvanger,<br/>First = {Delivery-ID},<br/>Last = {Delivery-ID},<br/>vereffend =**waar**,<br/>status =**afgewezen**(<br/>fout = {fout info}<br/>)<br/>) |

#### <a name="receive"></a>Ontvangen

| Client | Service Bus |
| --- | --- |
| --> flow (<br/>link-Credit = 1<br/>) |Geen actie |
| Geen actie |< overdracht (<br/>delivery-id = {numerieke ingang},<br/>Delivery-tag = {binaire ingang},<br/>vereffend =**Onwaar**,<br/>meer =**Onwaar**,<br/>status =**Null**,<br/>Resume =**False**<br/>) |
| --> toestand (<br/>Role =**ontvanger**,<br/>First = {Delivery-ID},<br/>Last = {Delivery-ID},<br/>vereffend =**waar**,<br/>status =**geaccepteerd**<br/>) |Geen actie |

#### <a name="multi-message-receive"></a>Ontvangen van meerdere berichten

| Client | Service Bus |
| --- | --- |
| --> flow (<br/>link-credit = 3<br/>) |Geen actie |
| Geen actie |< overdracht (<br/>delivery-id = {numerieke ingang},<br/>Delivery-tag = {binaire ingang},<br/>vereffend =**Onwaar**,<br/>meer =**Onwaar**,<br/>status =**Null**,<br/>Resume =**False**<br/>) |
| Geen actie |< overdracht (<br/>delivery-id = {numerieke ingang + 1},<br/>Delivery-tag = {binaire ingang},<br/>vereffend =**Onwaar**,<br/>meer =**Onwaar**,<br/>status =**Null**,<br/>Resume =**False**<br/>) |
| Geen actie |< overdracht (<br/>delivery-id = {numerieke ingang + 2},<br/>Delivery-tag = {binaire ingang},<br/>vereffend =**Onwaar**,<br/>meer =**Onwaar**,<br/>status =**Null**,<br/>Resume =**False**<br/>) |
| --> toestand (<br/>Role = ontvanger,<br/>First = {Delivery-ID},<br/>Last = {Delivery-ID + 2},<br/>vereffend =**waar**,<br/>status =**geaccepteerd**<br/>) |Geen actie |

### <a name="messages"></a>Berichten

In de volgende secties wordt uitgelegd welke eigenschappen van de standaard AMQP-bericht secties worden gebruikt door Service Bus en hoe deze worden toegewezen aan de API-set van Service Bus.

Alle eigenschappen die door de toepassing moeten worden gedefinieerd, moeten worden toegewezen aan de toewijzing van AMQP `application-properties` .

#### <a name="header"></a>koptekst

| Veldnaam | Gebruik | API-naam |
| --- | --- | --- |
| duurzame |- |- |
| priority |- |- |
| ttl |Time to Live voor dit bericht |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| eerste acquirer |- |- |
| aantal leveringen |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Veldnaam | Gebruik | API-naam |
| --- | --- | --- |
| bericht-id |Een door de toepassing gedefinieerde, vrije-vorm-id voor dit bericht. Wordt gebruikt voor duplicaten detectie. |[MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Door de toepassing gedefinieerde gebruikers-id die niet wordt geïnterpreteerd door Service Bus. |Niet toegankelijk via de Service Bus-API. |
| tot |De door de toepassing gedefinieerde doel-id, die niet wordt geïnterpreteerd door Service Bus. |[Aan](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Onderwerp |Door de toepassing gedefinieerde doel-id voor bericht, niet geïnterpreteerd door Service Bus. |[Adres](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| beantwoorden |Een door de toepassing gedefinieerde antwoord-Path-Indicator die niet wordt geïnterpreteerd door Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Door de toepassing gedefinieerde correlatie-id, die niet wordt geïnterpreteerd door Service Bus. |[Correlatie](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| inhouds type |Door de toepassing gedefinieerde inhouds type-indicator voor de hoofd tekst, niet geïnterpreteerd door Service Bus. |[Invoer](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-encoding |De door de toepassing gedefinieerde coderings indicator voor de hoofd tekst, die niet wordt geïnterpreteerd door Service Bus. |Niet toegankelijk via de Service Bus-API. |
| absoluut-verloop tijd |Declareert op welke absolute direct het bericht verloopt. Genegeerd bij invoer (TTL van header wordt waargenomen), gezaghebbend op uitvoer. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| aanmaak tijd |Declareert op welk tijdstip het bericht is gemaakt. Niet gebruikt door Service Bus |Niet toegankelijk via de Service Bus-API. |
| groep-ID |Een door de toepassing gedefinieerde id voor een gerelateerde set berichten. Wordt gebruikt voor Service Bus sessies. |[SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| groep-reeks |Teller waarmee het relatieve Volg nummer van het bericht binnen een sessie wordt geïdentificeerd. Genegeerd door Service Bus. |Niet toegankelijk via de Service Bus-API. |
| antwoord-naar-groep-ID |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Bericht aantekeningen

Er zijn enkele andere eigenschappen van Service Bus-berichten, die geen deel uitmaken van AMQP-bericht eigenschappen, en die worden door gegeven als `MessageAnnotations` op het bericht.

| Toewijzings sleutel van annotatie | Gebruik | API-naam |
| --- | --- | --- |
| x-opt-scheduled-time | Declareert op welk tijdstip het bericht moet worden weer gegeven op de entiteit |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x-opt-Partition-sleutel | Een door de toepassing gedefinieerde sleutel die bepaalt in welke partitie het bericht moet worden gegrond. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-via-Partition-sleutel | Een door de toepassing gedefinieerde partitie sleutel waarde wanneer een trans actie moet worden gebruikt voor het verzenden van berichten via een overdrachts wachtrij. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x-opt-in-time | De door de service gedefinieerde UTC-tijd voor de werkelijke tijd van het enqueuing van het bericht. Genegeerd bij invoer. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x-opt-Sequence-Number | Een door de service gedefinieerd uniek nummer dat is toegewezen aan een bericht. | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x-opt-offset | Service-gedefinieerd Volg nummer van in wachtrij geplaatste bericht. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt-Lock-until | Service gedefinieerd. De datum en tijd tot wanneer het bericht in de wachtrij/het abonnement wordt vergrendeld. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x-opt-deadletter-source | Service gedefinieerd. Als het bericht wordt ontvangen van de wachtrij voor onbestelbare berichten, de bron van het oorspronkelijke bericht. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>Transactie mogelijkheid

Een transactie groepeert twee of meer bewerkingen in een uitvoeringsbereik. Een dergelijke trans actie moet er dus voor zorgen dat alle bewerkingen die tot een bepaalde groep bewerkingen behoren, slagen of mislukken.
De bewerkingen worden gegroepeerd op een id `txn-id` .

Voor transactionele interactie fungeert de client als een `transaction controller` , waarmee de bewerkingen worden beheerd die samen moeten worden gegroepeerd. Service Bus-service fungeert als een `transactional resource` en voert het werk uit zoals aangevraagd door de `transaction controller` .

De client en service communiceren via een `control link` , die door de client is vastgesteld. De `declare` `discharge` -en-berichten worden door de controller via de beheer koppeling verzonden om respectievelijk trans acties toe te wijzen en te volt ooien (ze vertegenwoordigen niet de afbakening van transactioneel werk). De daad werkelijke verzen ding/ontvangst wordt niet uitgevoerd op deze koppeling. Elke aangevraagde transactionele bewerking wordt expliciet geïdentificeerd met de gewenste `txn-id` en kan daarom worden uitgevoerd op een koppeling op de verbinding. Als de koppeling van het besturings element wordt gesloten terwijl er nog niet-gefactureerde trans acties bestaan, worden alle dergelijke trans acties onmiddellijk teruggedraaid en wordt geprobeerd om verdere transactionele werkzaamheden uit te voeren. Berichten op de besturings koppeling mogen niet vooraf worden afgerekend.

Elke verbinding moet een eigen beheer koppeling initiëren om trans acties te kunnen starten en beëindigen. De service definieert een speciaal doel dat fungeert als een `coordinator` . De client/controller brengt een beheer koppeling naar dit doel tot stand. De beheer koppeling valt buiten de grens van een entiteit, dat wil zeggen, dezelfde beheer koppeling kan worden gebruikt om trans acties voor meerdere entiteiten te initiëren en te corrigeren.

#### <a name="starting-a-transaction"></a>Een trans actie starten

Om te beginnen met transactioneel werk. de controller moet een `txn-id` van de coördinator verkrijgen. Dit doet u door een `declare` type bericht te verzenden. Als de declaratie is geslaagd, reageert de coördinator met een positie resultaat, waarmee de toewijzing wordt uitgevoerd `txn-id` .

| Client (controller) | Richting | Service Bus (coördinator) |
| :--- | :---: | :--- |
| Koppel<br/>name = {naam koppeling},<br/>... ,<br/>Role =**Sender**,<br/>target =**coördinator**<br/>) | ------> |  |
|  | <------ | Koppel<br/>name = {naam koppeling},<br/>... ,<br/>doel = coördinator ()<br/>) |
| overbrengen<br/>delivery-id = 0,...)<br/>{AmqpValue (**Declare ()**)}| ------> |  |
|  | <------ | toestand <br/> First = 0, last = 0, <br/>status =**gedeclareerd**(<br/>**trans actie-id**= {trans actie-id}<br/>))|

#### <a name="discharging-a-transaction"></a>Een trans actie wordt geloosd

De controller sluit de transactionele werkzaamheden door een `discharge` bericht naar de coördinator te verzenden. De controller geeft aan dat de transactionele werkzaamheden moeten worden doorgevoerd of teruggedraaid door de `fail` vlag in te stellen op de kwijtings instantie. Als de coördinator de kwijting niet kan volt ooien, wordt het bericht met dit resultaat afgewezen `transaction-error` .

> Opmerking: Fail = True verwijst naar terugdraaien van een trans actie, en fout = False verwijst naar door voeren.

| Client (controller) | Richting | Service Bus (coördinator) |
| :--- | :---: | :--- |
| overbrengen<br/>delivery-id = 0,...)<br/>{AmqpValue (declare ())}| ------> |  |
|  | <------ | toestand <br/> First = 0, last = 0, <br/>status = gedeclareerd (<br/>trans actie-id = {trans actie-ID}<br/>))|
| | . . . <br/>Transactioneel werk<br/>op andere koppelingen<br/> . . . |
| overbrengen<br/>levering-id = 57,...)<br/>{ AmqpValue (<br/>**Kwijting (trans actie-id = 0, <br/> Fail = false)**)}| ------> |  |
| | <------ | toestand <br/> First = 57, last = 57, <br/>status =**geaccepteerd ()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Een bericht in een trans actie verzenden

Alle transactionele werkzaamheden worden uitgevoerd met de transactionele leverings status `transactional-state` die de trans actie-id uitvoert. In het geval van het verzenden van berichten wordt de transactionele status uitgevoerd door het overdrachts frame van het bericht. 

| Client (controller) | Richting | Service Bus (coördinator) |
| :--- | :---: | :--- |
| overbrengen<br/>delivery-id = 0,...)<br/>{AmqpValue (declare ())}| ------> |  |
|  | <------ | toestand <br/> First = 0, last = 0, <br/>status = gedeclareerd (<br/>trans actie-id = {trans actie-ID}<br/>))|
| overbrengen<br/>ingang = 1,<br/>delivery-id = 1, <br/>**status = <br/> TransactionalState ( <br/> trans actie-id = 0)**)<br/>nettolading| ------> |  |
| | <------ | toestand <br/> First = 1, last = 1, <br/>status =**TransactionalState ( <br/> trans actie-id = 0, <br/> resultaat = geaccepteerd ()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Een bericht in een trans actie afstoten

Bericht toestand bevat bewerkingen zoals `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer` . Als u deze bewerkingen binnen een trans actie wilt uitvoeren, geeft u het door `transactional-state` met de toestand.

| Client (controller) | Richting | Service Bus (coördinator) |
| :--- | :---: | :--- |
| overbrengen<br/>delivery-id = 0,...)<br/>{AmqpValue (declare ())}| ------> |  |
|  | <------ | toestand <br/> First = 0, last = 0, <br/>status = gedeclareerd (<br/>trans actie-id = {trans actie-ID}<br/>))|
| | <------ |overbrengen<br/>ingang = 2,<br/>delivery-id = 11, <br/>status = Null)<br/>nettolading|  
| toestand <br/> First = 11, last = 11, <br/>status =**TransactionalState ( <br/> trans actie-id = 0, <br/> resultaat = geaccepteerd ()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Geavanceerde Service Bus mogelijkheden

In deze sectie vindt u geavanceerde mogelijkheden van Azure Service Bus die zijn gebaseerd op concept uitbreidingen op AMQP, die momenteel worden ontwikkeld in het OASIS Technical Committee for AMQP. Service Bus implementeert de nieuwste versies van deze concepten en neemt wijzigingen op die zijn geïntroduceerd in de standaard status.

> [!NOTE]
> Geavanceerde bewerkingen voor berichten van Service Bus worden ondersteund via een aanvraag/antwoord patroon. De details van deze bewerkingen worden beschreven in het artikel [AMQP 1,0 in service bus: bewerkingen op basis van aanvragen](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>AMQP-beheer

De AMQP-beheer specificatie is de eerste van de concept uitbreidingen die in dit artikel worden besproken. Deze specificatie definieert een set protocollen die is gelaagd boven op het AMQP-protocol waarmee beheer interacties met de infra structuur voor berichten via AMQP worden toegestaan. De specificatie definieert algemene bewerkingen, zoals *maken*, *lezen*, *bijwerken* en *verwijderen* voor het beheren van entiteiten in een infra structuur voor berichten en een set query bewerkingen.

Voor al deze gebaren is een aanvraag/antwoord interactie tussen de client en de infra structuur voor berichten vereist. de specificatie definieert daarom hoe u het interactie patroon op AMQP kunt model leren: de client maakt verbinding met de infra structuur voor berichten, initieert een sessie en maakt vervolgens een paar koppelingen. Op één koppeling fungeert de-client als afzender en op de andere, fungeert deze als ontvanger, waardoor er een paar koppelingen worden gemaakt die kunnen fungeren als een bi-directioneel kanaal.

| Logische bewerking | Client | Service Bus |
| --- | --- | --- |
| Reactie pad voor aanvraag maken |--> koppelen (<br/>name = {*naam koppeling*},<br/>ingang = {*numerieke ingang*},<br/>Role =**Sender**,<br/>bron =**Null**,<br/>target = "myentity/$management"<br/>) |Geen actie |
| Reactie pad voor aanvraag maken |Geen actie |\<-- attach(<br/>name = {*naam koppeling*},<br/>ingang = {*numerieke ingang*},<br/>Role =**ontvanger**,<br/>Bron = null,<br/>target = "myentity"<br/>) |
| Reactie pad voor aanvraag maken |--> koppelen (<br/>name = {*naam koppeling*},<br/>ingang = {*numerieke ingang*},<br/>Role =**ontvanger**,<br/>source = "myentity/$management",<br/>target = "myclient $ id"<br/>) | |
| Reactie pad voor aanvraag maken |Geen actie |\<-- attach(<br/>name = {*naam koppeling*},<br/>ingang = {*numerieke ingang*},<br/>Role =**Sender**,<br/>Bron = "myentity",<br/>target = "myclient $ id"<br/>) |

Met dat paar koppelingen op locatie is de aanvraag/antwoord-implementatie eenvoudig: een aanvraag is een bericht dat wordt verzonden naar een entiteit in de infra structuur voor berichten die dit patroon begrijpt. In dat verzoek-bericht wordt het veld *beantwoorden* in de sectie *Eigenschappen* ingesteld op de *doel* -id voor de koppeling waarop het antwoord moet worden afgeleverd. De entiteit handling verwerkt de aanvraag en stuurt vervolgens het antwoord over de koppeling waarvan de *doel* -id overeenkomt met de opgegeven *antwoord-* id.

Het patroon vereist uiteraard dat de client container en de door de client gegenereerde id voor de antwoord bestemming uniek zijn op alle clients en om veiligheids redenen moeilijk te voors pellen.

De bericht uitwisselingen die worden gebruikt voor het beheer protocol en voor alle andere protocollen die gebruikmaken van hetzelfde patroon, gebeuren op toepassings niveau. Er worden geen nieuwe AMQP-bewegingen voor het protocol niveau gedefinieerd. Dat is opzettelijk, zodat toepassingen onmiddellijk kunnen profiteren van deze uitbrei dingen met compatibele AMQP 1,0-stacks.

Service Bus implementeert momenteel geen van de kern functies van de beheer specificatie, maar het aanvraag/antwoord-patroon dat door de beheer specificatie is gedefinieerd, is voor de functie op claims gebaseerde beveiliging en voor vrijwel alle geavanceerde mogelijkheden die in de volgende secties worden besproken:

### <a name="claims-based-authorization"></a>Autorisatie op basis van claims

De specificatie van het AMQP Claims based Authorization (CBS) is gebaseerd op het patroon van de beheer specificatie aanvraag/reactie en beschrijft een gegeneraliseerde model voor het gebruik van federatieve beveiligings tokens met AMQP.

Het standaard beveiligings model van AMQP dat in de inleiding wordt besproken, is gebaseerd op SASL en kan worden geïntegreerd met de AMQP-Handshake van de verbinding. Het gebruik van SASL heeft het voor deel dat het een uitbreidbaar model biedt waarvoor een set mechanismen is gedefinieerd, op basis waarvan elk protocol dat op SASL formeel kan profiteren. Een van deze mechanismen is "PLAIN" voor de overdracht van gebruikers namen en wacht woorden "EXTERNAL" om te verbinden met beveiliging op TLS-niveau, "anoniem" om het ontbreken van expliciete verificatie/autorisatie, en een groot aantal extra mechanismen waarmee verificatie en/of autorisatie referenties of-tokens kunnen worden door gegeven.

De SASL-integratie van AMQP heeft twee nadelen:

* Alle referenties en tokens zijn binnen het bereik van de verbinding. Een infra structuur voor berichten kan een gedifferentieerd toegangs beheer op basis van afzonderlijke eenheden bieden. bijvoorbeeld, zodat de drager van een token kan verzenden naar de wachtrij A, maar niet naar de wachtrij B. Met de autorisatie context verankerd op de verbinding, is het niet mogelijk om één verbinding te gebruiken en nog verschillende toegangs tokens voor wachtrij A en wachtrij B te gebruiken.
* Toegangs tokens zijn doorgaans alleen geldig voor een beperkte periode. Deze geldigheid vereist dat de gebruiker periodiek tokens opnieuw ophaalt en de uitgever van het token de mogelijkheid biedt om een nieuw token te weigeren als de toegangs machtigingen van de gebruiker zijn gewijzigd. AMQP-verbindingen kunnen lange tijd duren. Het SASL-model biedt alleen de mogelijkheid om een token in te stellen op verbindings tijd, wat betekent dat de infra structuur van berichten de client moet loskoppelen wanneer het token verloopt of dat het risico moet worden geaccepteerd dat permanente communicatie met een client die toegangs rechten toestaat, mogelijk is ingetrokken in de tijdelijke oplossing.

De AMQP CBS-specificatie, geïmplementeerd door Service Bus, maakt een elegante tijdelijke oplossing voor beide problemen: Hiermee kan een client toegangs tokens aan elk knoop punt koppelen en deze tokens bijwerken voordat ze verlopen, zonder de berichten stroom te onderbreken.

CBS definieert een virtueel beheer knooppunt, met de naam *$CBS*, dat wordt opgegeven door de infra structuur voor berichten. Het beheer knooppunt accepteert tokens namens andere knoop punten in de Messa ging-infra structuur.

De protocol beweging is een aanvraag/antwoord uitwisseling zoals gedefinieerd door de beheer specificatie. Dit betekent dat de client een paar koppelingen tot stand brengt met het *$CBS* knoop punt en vervolgens een aanvraag doorgeeft op de uitgaande koppeling en vervolgens wacht op de reactie op de inkomende koppeling.

Het aanvraag bericht heeft de volgende toepassings eigenschappen:

| Sleutel | Optioneel | Waardetype | Inhoud van waarde |
| --- | --- | --- | --- |
| bewerking |No |tekenreeks |**put-token** |
| type |No |tekenreeks |Het type van het token dat wordt geplaatst. |
| naam |No |tekenreeks |De "doel groep" waarop het token van toepassing is. |
| verval |Ja |tijdstempel |De verloop tijd van het token. |

De eigenschap *name* identificeert de entiteit waaraan het token moet worden gekoppeld. In Service Bus is het het pad naar de wachtrij, of onderwerp/abonnement. De eigenschap *type* geeft het token type aan:

| Token type | Beschrijving van het token | Type hoofd tekst | Notities |
| --- | --- | --- | --- |
| AMQP: JWT |JSON Web Token (JWT) |AMQP-waarde (teken reeks) |Nog niet beschikbaar. |
| AMQP: swt |Eenvoudig webtoken (SWT) |AMQP-waarde (teken reeks) |Alleen ondersteund voor SWT-tokens die zijn uitgegeven door AAD/ACS |
| servicebus. Windows. net: sastoken |Service Bus SAS-token |AMQP-waarde (teken reeks) |- |

Tokens verlenen rechten. Service Bus weet ongeveer drie fundamentele rechten: verzenden ondersteunt het verzenden, Luis teren van ontvangen en beheren van entiteiten. SWT-tokens die zijn uitgegeven door AAD/ACS, bevatten expliciet die rechten als claims. Service Bus SAS-tokens verwijzen naar regels die zijn geconfigureerd op de naam ruimte of entiteit, en deze regels worden geconfigureerd met rechten. Als u het token ondertekent met de sleutel die aan deze regel is gekoppeld, maakt het token de respectieve rechten. Met het token dat is gekoppeld aan een entiteit die gebruikmaakt van een *put-token,* kan de verbonden client communiceren met de entiteit per de token rechten. Voor een koppeling waarbij de client de rol van *afzender* gebruikt, is het recht ' verzenden ' vereist. voor het nemen van de rol *ontvanger* is het recht ' Luis teren ' vereist.

Het antwoord bericht heeft de volgende *eigenschaps* waarden van de toepassing:

| Sleutel | Optioneel | Waardetype | Inhoud van waarde |
| --- | --- | --- | --- |
| status-code |No |int |HTTP-antwoord code **[RFC2616]**. |
| status-beschrijving |Yes |tekenreeks |De beschrijving van de status. |

De client kan *put-token* herhaaldelijk aanroepen en voor elke entiteit in de infra structuur voor berichten. De tokens zijn toegewezen aan de huidige client en verankerd op de huidige verbinding, wat betekent dat de server behouden tokens verwijdert wanneer de verbinding wordt verbroken.

Met de huidige Service Bus-implementatie is CBS alleen toegestaan in combi natie met de SASL-methode ' ANONYMOUS '. Er moet altijd een SSL/TLS-verbinding bestaan vóór de SASL-handshake.

Het anonieme mechanisme moet daarom worden ondersteund door de gekozen AMQP 1,0-client. Anonieme toegang betekent dat de eerste verbindings-Handshake, inclusief het maken van de eerste sessie, wordt uitgevoerd zonder Service Bus weten wie de verbinding maakt.

Zodra de verbinding en sessie tot stand zijn gebracht, koppelt u de koppelingen aan het knoop punt *$CBS* en verzendt u de aanvraag van de *put-token* als enige toegestane bewerkingen. Een geldig token moet worden ingesteld met behulp van een *put-token* aanvraag voor een bepaald entity-knoop punt binnen 20 seconden nadat de verbinding tot stand is gebracht, anders wordt de verbinding met Service Bus verwijderd.

De client wordt vervolgens verantwoordelijk voor het bijhouden van de verval datum van tokens. Wanneer een token verloopt, Service Bus u alle koppelingen op de verbinding met de betreffende entiteit onmiddellijk weglaten. Om het probleem te voor komen, kan de client het token voor het knoop punt op elk gewenst moment vervangen door het knoop punt virtuele *$CBS* -beheer met dezelfde *put-token-* beweging, en zonder de manier te ontvangen van het payload-verkeer dat op verschillende koppelingen stromen.

### <a name="send-via-functionality"></a>Functionaliteit voor verzenden via

Send [-by/overdracht Sender](service-bus-transactions.md#transfers-and-send-via) is een functie waarmee service bus een bepaald bericht via een andere entiteit kan door sturen naar een doel entiteit. Deze functie wordt gebruikt voor het uitvoeren van bewerkingen tussen entiteiten in één trans actie.

Met deze functionaliteit maakt u een afzender en brengt u de koppeling tot stand `via-entity` . Tijdens het maken van de koppeling wordt aanvullende informatie door gegeven om de werkelijke bestemming van de berichten/overdrachten op deze koppeling tot stand te brengen. Zodra het koppelen is gelukt, worden alle berichten die via deze koppeling worden verzonden, automatisch doorgestuurd naar de *doel entiteit* via *via-entity*. 

> Opmerking: verificatie moet worden uitgevoerd voor zowel *via-entity* als *doel-entiteit* voordat deze koppeling tot stand wordt gebracht.

| Client | Richting | Service Bus |
| :--- | :---: | :--- |
| Koppel<br/>name = {naam koppeling},<br/>Role = Sender,<br/>Bron = {client koppelings-ID},<br/>doel =**{via-entity}**,<br/>**Eigenschappen = map [( <br/> com. micro soft: overdracht-doel-adres = <br/> {doel-entiteit})]** ) | ------> | |
| | <------ | Koppel<br/>name = {naam koppeling},<br/>Role = ontvanger,<br/>Bron = {client koppelings-ID},<br/>doel = {via-entity},<br/>eigenschappen = map [(<br/>com. micro soft: overdracht-bestemming-adres =<br/>{Destination-entity})] ) |

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over AMQP naar de volgende koppelingen:

* [Overzicht van Service Bus AMQP]
* [AMQP 1,0-ondersteuning voor Service Bus gepartitioneerde wacht rijen en onderwerpen]
* [AMQP in Service Bus voor Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Overzicht van Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1,0-ondersteuning voor Service Bus gepartitioneerde wacht rijen en onderwerpen]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)
