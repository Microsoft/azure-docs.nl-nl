---
title: AMQP 1.0 in Azure Service Bus and Event Hubs protocol guide | Microsoft Docs
description: Protocolhandleiding voor expressies en beschrijving van AMQP 1.0 in Azure Service Bus en Event Hubs
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 8d346aeef74e1f67d3d525c061d40314ee5342aa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531006"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 in Azure Service Bus en Event Hubs protocolhandleiding

Advanced Message Queueing Protocol 1.0 is een gestandaardiseerd frame- en overdrachtsprotocol voor het asynchroon, veilig en betrouwbaar overdragen van berichten tussen twee partijen. Het is het primaire protocol van Azure Service Bus Messaging en Azure Event Hubs.  

AMQP 1.0 is het resultaat van een brede samenwerking in de branche die middlewareleveranciers, zoals Microsoft en Red Hat, heeft samengebracht, met veel berichten-middlewaregebruikers, zoals JP Jp Messaging, die de financiële dienstverlening vertegenwoordigen. Het forum voor technische standaardisatie voor het AMQP-protocol en de extensiespecificaties is OASIS. Het heeft formele goedkeuring als internationale standaard als ISO/IEC 19494:2014 bereikt. 

## <a name="goals"></a>Doelstellingen

In dit artikel worden de belangrijkste concepten van de AMQP 1.0-berichtspecificatie samen met de extensiespecificaties beschreven die zijn ontwikkeld door de [OASIS AMQP Technical College](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) en wordt uitgelegd hoe Azure Service Bus deze specificaties implementeert en bouwt op deze specificaties.

Het doel is dat elke ontwikkelaar die een bestaande AMQP 1.0-clientstack op elk platform gebruikt, kan communiceren met Azure Service Bus via AMQP 1.0.

Algemene AMQP 1.0-stacks voor algemeen gebruik, zoals [Apache Qpid Ether](https://qpid.apache.org/proton/index.html) [of AMQP.NET Lite,](https://github.com/Azure/amqpnetlite)implementeren alle kernelementen van het AMQP 1.0-protocol, zoals sessies of koppelingen. Deze basiselementen worden soms verpakt met een API op een hoger niveau; Apache Apache Apache biedt zelfs twee, de imperatieve Messenger-API en de reactieve Reactor-API.

In de volgende discussie gaan we ervan uit dat het beheer van AMQP-verbindingen, -sessies en -koppelingen en de verwerking van frameoverdrachten en stroombeheer wordt verwerkt door de respectieve stack (zoals Apache Apache Apache-C) en dat er niet veel of geen specifieke aandacht van toepassingsontwikkelaars nodig is. We gaan abstract uit van het bestaan van een paar API-primitieven, zoals de mogelijkheid om verbinding te maken, en om een vorm van afzender- en ontvangerabstracties te maken, die vervolgens respectievelijk een vorm van - en -bewerkingen   `send()` `receive()` hebben.

Bij het bespreken van geavanceerde mogelijkheden van Azure Service Bus, zoals het bladeren door berichten of het beheren van sessies, worden deze functies uitgelegd in AMQP-termen, maar ook als een gelaagde pseudo-implementatie boven op deze veronderstelde API-abstractie.

## <a name="what-is-amqp"></a>Wat is AMQP?

AMQP is een frame- en overdrachtsprotocol. Frame maakt het mogelijk om binaire gegevensstromen te structureren die in beide richtingen van een netwerkverbinding stromen. De structuur biedt aflijning voor afzonderlijke gegevensblokken, ook wel *frames* genoemd, die moeten worden uitgewisseld tussen de verbonden partijen. De overdrachtsmogelijkheden zorgen ervoor dat beide communicerende partijen een gedeeld begrip kunnen krijgen over wanneer frames moeten worden overgedragen en wanneer overdrachten als voltooid worden beschouwd.

In tegenstelling tot eerdere verlopen conceptversies die worden geproduceerd door de AMQP-werkgroep die nog steeds worden gebruikt door een paar berichtbrokers, wordt de aanwezigheid van een berichtenbroker of een bepaalde topologie voor entiteiten in een berichtenbroker niet door het definitieve en gestandaardiseerde AMQP 1.0-protocol geactheseerd.

Het protocol kan worden gebruikt voor symmetrische peer-to-peer-communicatie, voor interactie met berichtenbrokers die ondersteuning bieden voor wachtrijen en publiceren/abonneren van entiteiten, zoals Azure Service Bus doen. Het kan ook worden gebruikt voor interactie met de berichteninfrastructuur waarbij de interactiepatronen verschillen van gewone wachtrijen, zoals het geval is bij Azure Event Hubs. Een Event Hub fungeert als een wachtrij wanneer er gebeurtenissen naar worden verzonden, maar fungeert meer als een seriële opslagservice wanneer gebeurtenissen ervan worden gelezen; het lijkt enigszins op een tapestation. De client kiest een offset in de beschikbare gegevensstroom en krijgt vervolgens alle gebeurtenissen van die offset tot de meest recente beschikbare.

Het AMQP 1.0-protocol is ontworpen om te worden uitgebreid, waardoor verdere specificaties mogelijk zijn om de mogelijkheden ervan te verbeteren. De drie extensiespecificaties die in dit document worden besproken, illustreren dit. Voor communicatie via bestaande HTTPS/WebSockets-infrastructuur kan het lastig zijn om de systeemeigen AMQP TCP-poorten te configureren. Een bindingsspecificatie definieert hoe AMQP een laag moet worden boven WebSockets. Voor interactie met de berichteninfrastructuur op aanvraag-/antwoord-wijze voor beheerdoeleinden of om geavanceerde functionaliteit te bieden, definieert de AMQP-beheerspecificatie de vereiste basisinteractieprim primitieven. Voor integratie van federatief autorisatiemodel definieert de op CLAIMS gebaseerde beveiligingsspecificatie van AMQP hoe autorisatietokens die aan koppelingen zijn gekoppeld, moeten worden gekoppeld en vernieuwd.

## <a name="basic-amqp-scenarios"></a>AmQP-basisscenario's

In deze sectie wordt het basisgebruik van AMQP 1.0 met Azure Service Bus uitgelegd. Dit omvat het maken van verbindingen, sessies en koppelingen en het overdragen van berichten van en naar Service Bus-entiteiten, zoals wachtrijen, onderwerpen en abonnementen.

De meest gezaghebbende bron voor meer informatie over de werking van AMQP is de [AMQP 1.0-specificatie,](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html)maar de specificatie is geschreven om de implementatie nauwkeurig te begeleiden en niet om het protocol te leren. Deze sectie is gericht op het introduceren van zoveel terminologie als nodig is om te beschrijven hoe Service Bus AMQP 1.0 gebruikt. Voor een uitgebreidere inleiding tot AMQP, evenals een uitgebreidere bespreking van AMQP 1.0, kunt u deze [videocursus bekijken.][this video course]

### <a name="connections-and-sessions"></a>Verbindingen en sessies

AMQP roept de communicatieprogrammacontainers *aan;* deze bevatten *knooppunten,* die de communicerende entiteiten in deze containers zijn. Een wachtrij kan een dergelijk knooppunt zijn. AMQP maakt multiplexing mogelijk, zodat één verbinding kan worden gebruikt voor veel communicatiepaden tussen knooppunten; Een toepassingsclient kan bijvoorbeeld gelijktijdig van de ene wachtrij ontvangen en naar een andere wachtrij verzenden via dezelfde netwerkverbinding.

![Diagram met sessies en verbindingen tussen containers.][1]

De netwerkverbinding is dus verankerd in de container. Het wordt geïnitieerd door de container in de clientrol en maakt een uitgaande TCP-socketverbinding met een container in de ontvangerrol, die naar binnenkomende TCP-verbindingen luistert en accepteert. De verbindingshandhake omvat het onderhandelen over de protocolversie, het declareren of onderhandelen over het gebruik van Transport Level Security (TLS/SSL) en een verificatie-/autorisatiehandhake voor het verbindingsbereik dat is gebaseerd op SASL.

Azure Service Bus vereist het gebruik van TLS te allen tijde. Het biedt ondersteuning voor verbindingen via TCP-poort 5671, waarbij de TCP-verbinding eerst wordt overschreven met TLS voordat de handshake van het AMQP-protocol wordt gebruikt, en tevens verbindingen via TCP-poort 5672, waarbij de server onmiddellijk een verplichte upgrade van de verbinding met TLS biedt met behulp van het AMQP-voorgeschreven model. De AMQP WebSockets-binding maakt een tunnel via TCP-poort 443 die vervolgens gelijk is aan AMQP 5671-verbindingen.

Na het instellen van de verbinding en TLS biedt Service Bus twee opties voor het SASL-mechanisme:

* SASL PLAIN wordt vaak gebruikt voor het doorgeven van gebruikersnaam- en wachtwoordreferenties aan een server. Service Bus geen accounts, maar heeft de naam [Beveiligingsregels](service-bus-sas.md)voor gedeelde toegang, die rechten verlenen en zijn gekoppeld aan een sleutel. De naam van een regel wordt gebruikt als gebruikersnaam en de sleutel (als base64-gecodeerde tekst) wordt gebruikt als wachtwoord. De rechten die zijn gekoppeld aan de gekozen regel bepalen de bewerkingen die zijn toegestaan voor de verbinding.
* SASL ANONYMOUS wordt gebruikt voor het omzeilen van SASL-autorisatie wanneer de client gebruik wil maken van het op claims gebaseerde beveiligingsmodel dat later wordt beschreven. Met deze optie kan een clientverbinding gedurende korte tijd anoniem tot stand worden gebracht, waarbij de client alleen kan communiceren met het ZIJ-eindpunt en de HANDSHAKE moet worden voltooid.

Nadat de transportverbinding tot stand is gebracht, declareerden de containers elk de maximale framegrootte die ze bereid zijn te verwerken. Na een time-out voor inactiviteit wordt de verbinding verbroken als er geen activiteit op de verbinding is.

Ze declareer ook hoeveel gelijktijdige kanalen worden ondersteund. Een kanaal is een unidirectioneel, uitgaand, virtueel overdrachtspad boven op de verbinding. Een sessie gebruikt een kanaal van elk van de onderling verbonden containers om een bi-directioneel communicatiepad te vormen.

Sessies hebben een stroombeheermodel op basis van vensters; wanneer een sessie wordt gemaakt, geeft elke partij aan hoeveel frames het wil accepteren in het ontvangstvenster. Wanneer de partijen frames uitwisselen, vullen overgedragen frames dat venster en worden de overdrachten gestopt wanneer het venster vol is en totdat het venster opnieuw wordt ingesteld of uitgebreid met behulp van de *stroom-performatieve (performatief* is de AMQP-term voor gebaren op protocolniveau die tussen de twee partijen worden uitgewisseld).

Dit model op basis van vensters is grofweg vergelijkbaar met het TCP-concept van stroombeheer op basis van vensters, maar op sessieniveau binnen de socket. Het protocol heeft het concept van het toestaan van meerdere gelijktijdige sessies, zodat verkeer met hoge prioriteit kan worden omsleerd tot voorbij beperkt normaal verkeer, zoals op een express-snelweg.

Azure Service Bus gebruikt momenteel precies één sessie voor elke verbinding. De Service Bus maximale framegrootte is 262.144 bytes (256.000 bytes) voor Service Bus Standard. Het is 1.048.576 (1 MB) voor Service Bus Premium en Event Hubs. Service Bus legt geen specifieke beperkingsvensters op sessieniveau op, maar stelt het venster regelmatig opnieuw in als onderdeel van het stroombeheer op koppelingsniveau (zie [de volgende sectie).](#links)

Verbindingen, kanalen en sessies zijn kortstondig. Als de onderliggende verbinding samengevouwen wordt, moeten verbindingen, TLS-tunnel, SASL-autorisatiecontext en sessies opnieuw worden gemaakt.

### <a name="amqp-outbound-port-requirements"></a>Vereisten voor uitgaande AMQP-poort

Voor clients die AMQP-verbindingen via TCP gebruiken, moeten de poorten 5671 en 5672 worden geopend in de lokale firewall. Naast deze poorten kan het nodig zijn om extra poorten te openen als de [functie EnableLinkRedirect](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect) is ingeschakeld. `EnableLinkRedirect` is een nieuwe berichtenfunctie waarmee u één hop kunt overslaan tijdens het ontvangen van berichten, waardoor de doorvoer wordt versterkt. De client communiceert dan rechtstreeks met de back-endservice via poortbereik 104XX, zoals wordt weergegeven in de volgende afbeelding. 

![Lijst met doelpoorten][4]

Een .NET-client zou mislukken met een SocketException ('er is geprobeerd toegang te krijgen tot een socket op een manier die niet is toegestaan door de toegangsrechten') als deze poorten worden geblokkeerd door de firewall. De functie kan worden uitgeschakeld door in de connection string in te stellen, waardoor de clients moeten communiceren met de externe `EnableAmqpLinkRedirect=false` service via poort 5671.


### <a name="links"></a>Koppelingen

AMQP brengt berichten over via koppelingen. Een koppeling is een communicatiepad dat is gemaakt via een sessie waarmee berichten in één richting kunnen worden verzonden; de overdrachtsstatusonderhandeling gaat over de koppeling en bi-directionele tussen de verbonden partijen.

![Schermopname van een sessie met een koppelingsverbinding tussen twee containers.][2]

Koppelingen kunnen op elk moment en via een bestaande sessie door beide containers worden gemaakt, waardoor AMQP verschilt van veel andere protocollen, waaronder HTTP en MQTT, waarbij het initiëren van overdrachten en het overdrachtspad een exclusieve bevoegdheid is van de partij die de socketverbinding maakt.

De container die de koppeling initieert, vraagt de tegenovergestelde container om een koppeling te accepteren en kiest een rol van afzender of ontvanger. Daarom kan een van beide containers het maken van unidirectionele of bidirectionele communicatiepaden initiëren, met de laatste gemodelleerd als koppelingen.

Koppelingen worden benoemd en gekoppeld aan knooppunten. Zoals vermeld in het begin, zijn knooppunten de communicerende entiteiten binnen een container.

In Service Bus is een knooppunt direct gelijk aan een wachtrij, een onderwerp, een abonnement of een subqueue van een wachtrij of abonnement. De knooppuntnaam die in AMQP wordt gebruikt, is daarom de relatieve naam van de entiteit in de Service Bus naamruimte. Als een wachtrij de naam heeft, is dat ook de naam van het `myqueue` AMQP-knooppunt. Een onderwerpabonnement volgt de HTTP API-conventie door te worden gesorteerd in een  resourceverzameling 'abonnementen', zodat een abonnementssub van een onderwerp **mytopic** de naam van het AMQP-knooppunt **mytopic/subscriptions/sub** heeft.

De verbindende client is ook vereist voor het gebruik van een lokale knooppuntnaam voor het maken van koppelingen; Service Bus is niet prescriptief over deze knooppuntnamen en interpreteert ze niet. AMQP 1.0-clientstacks gebruiken doorgaans een schema om ervoor te zorgen dat deze kortstondige knooppuntnamen uniek zijn binnen het bereik van de client.

### <a name="transfers"></a>Transfers

Zodra een koppeling tot stand is gebracht, kunnen berichten via die koppeling worden overgedragen. In AMQP wordt een overdracht uitgevoerd met  een expliciete protocolbewegingen (de overdrachtsuitvoer) die een bericht van afzender naar ontvanger verplaatst via een koppeling. Een overdracht is voltooid wanneer deze is 'vereffend', wat betekent dat beide partijen een gedeeld begrip hebben gekregen van het resultaat van die overdracht.

![Een diagram met de overdracht van een bericht tussen de afzender en ontvanger en de positie die het resultaat is van het bericht.][3]

In het eenvoudigste geval kan de afzender ervoor kiezen om berichten vooraf te verzenden, wat betekent dat de client niet geïnteresseerd is in het resultaat en de ontvanger geen feedback geeft over het resultaat van de bewerking. Deze modus wordt ondersteund door Service Bus op het niveau van het AMQP-protocol, maar niet zichtbaar in een van de client-API's.

Het normale geval is dat berichten niet worden verzonden en de ontvanger vervolgens acceptatie of afwijzing aanwijst met behulp van *de disposition-performatieve.* Afwijzing treedt op wanneer de ontvanger het bericht om een bepaalde reden niet kan accepteren en het afwijzingsbericht informatie bevat over de reden, een foutstructuur die is gedefinieerd door AMQP. Als berichten worden geweigerd vanwege interne fouten in Service Bus, retourneert de service extra informatie binnen die structuur die kan worden gebruikt voor het verstrekken van diagnostische hints aan ondersteuningsmedewerkers als u ondersteuningsaanvragen indient. Later vindt u meer informatie over fouten.

Een speciale vorm van  afwijzing is de status Vrijgegeven, die aangeeft dat de ontvanger geen technisch probleem heeft met de overdracht, maar ook geen interesse heeft om de overdracht te vereffenen. Dat geval bestaat bijvoorbeeld wanneer een bericht wordt bezorgd bij een Service Bus-client en de client ervoor kiest om het bericht te 'verlaten' omdat het niet het werk kan uitvoeren dat het resultaat is van het verwerken van het bericht; de levering van berichten zelf is niet de fout. Een variant van die status is *de gewijzigde* status, waarmee wijzigingen in het bericht kunnen worden aangebracht wanneer het wordt vrijgegeven. Deze status wordt momenteel niet door Service Bus gebruikt.

De AMQP 1.0-specificatie definieert een verdere status met de naam *ontvangen,* die specifiek helpt bij het verwerken van koppelingsherstel. Met koppelingsherstel kunt u de status van een koppeling en eventuele in behandeling zijnde leveringen opnieuw afstemmen op een nieuwe verbinding en sessie, wanneer de vorige verbinding en sessie verloren zijn gegaan.

Service Bus biedt geen ondersteuning voor koppelingsherstel; Als de client de verbinding met Service Bus verliest als er een niet-geseede berichtoverdracht in behandeling is, gaat die berichtoverdracht verloren en moet de client opnieuw verbinding maken, de koppeling opnieuw publiceren en de overdracht opnieuw proberen.

Als zodanig ondersteunen Service Bus en Event Hubs 'ten minste één keer' overdracht waarbij de afzender kan worden gegarandeerd dat het bericht is opgeslagen en geaccepteerd, maar geen ondersteuning biedt voor 'exact één keer' overdrachten op AMQP-niveau, waarbij het systeem probeert de koppeling te herstellen en door te gaan met onderhandelen over de leveringstoestand om duplicatie van de berichtoverdracht te voorkomen.

Ter compensatie van mogelijke dubbele verzendt, ondersteunt Service Bus detectie van duplicaten als een optionele functie voor wachtrijen en onderwerpen. Bij duplicaatdetectie worden de bericht-ID's van alle binnenkomende berichten vastgelegd tijdens een door de gebruiker gedefinieerd tijdvenster. Vervolgens worden alle berichten die tijdens datzelfde venster met dezelfde bericht-ID's zijn verzonden, op de stille plaats weergegeven.

### <a name="flow-control"></a>Stoombeheer

Naast het stroombeheermodel op sessieniveau dat eerder is besproken, heeft elke koppeling een eigen stroombeheermodel. Met stroombeheer op sessieniveau wordt de container beschermd tegen het verwerken van te veel frames tegelijk. Met stroombeheer op koppelingsniveau wordt de toepassing verantwoordelijk voor het aantal berichten dat de container wil verwerken vanaf een koppeling en wanneer dat gebeurt.

![Schermopname van een logboek met Bron, Doel, Bronpoort, Doelpoort en Protocolnaam. In de eerste rij wordt doelpoort 10401 (0x28 A 1) zwart aangegeven.][4]

Op een koppeling kunnen overdrachten alleen plaatsvinden wanneer de afzender voldoende *koppelingstegoed heeft.* Koppelingstegoed is een teller  die door de ontvanger is ingesteld met behulp van de stroomuitvoer, die is beperkt tot een koppeling. Wanneer aan de afzender een koppelingstegoed is toegewezen, wordt geprobeerd dat tegoed te gebruiken door berichten te leveren. Bij elke berichtbezorging wordt het resterende koppelingstegoed met 1 afgeschreven. Wanneer het koppelingstegoed is op, worden de leveringen gestopt.

Wanneer Service Bus de ontvangerrol heeft, krijgt de afzender onmiddellijk een uitgebreid koppelingstegoed, zodat berichten onmiddellijk kunnen worden verzonden. Als koppelingstegoed wordt gebruikt, Service Bus  af en toe een stroom naar de afzender om het saldo van het koppelingstegoed bij te werken.

In de rol afzender verzendt Service Bus berichten om openstaande koppelingstegoeden te gebruiken.

Een aanroep 'ontvangen' op API-niveau wordt omgezet in een stroom die wordt verzonden naar Service Bus door de client. Service Bus verbruikt dat tegoed door het eerste beschikbare, ontgrendelde bericht uit de wachtrij te halen, te vergrendelen en over te dragen.  Als er geen bericht beschikbaar is voor levering, blijft het openstaande tegoed via een koppeling die met die specifieke entiteit tot stand is gebracht, geregistreerd op volgorde van aankomst en worden berichten vergrendeld en overgedragen zodra ze beschikbaar komen, zodat eventuele openstaande tegoeden kunnen worden gebruikt.

De vergrendeling van een bericht wordt vrijgegeven wanneer de overdracht wordt vereffend in een van de terminale staten *geaccepteerd,* *afgewezen* of *vrijgegeven.* Het bericht wordt verwijderd uit Service Bus wanneer de terminaltoestand wordt *geaccepteerd.* Deze blijft in Service Bus en wordt aan de volgende ontvanger geleverd wanneer de overdracht een van de andere staten bereikt. Service Bus verplaatst het bericht automatisch naar de wachtrij voor in de wachtrij van de entiteit wanneer het maximum aantal toegestane leveringen voor de entiteit wordt bereikt vanwege herhaalde afwijzingen of releases.

Hoewel de Service Bus-API's een dergelijke optie momenteel niet rechtstreeks beschikbaar maken, kan een client met een LAGER AMQP-protocol het koppelingstegoedmodel gebruiken om de interactie 'pull-stijl' van het uitgeven van één kredieteenheid voor elke ontvangstaanvraag om te zetten in een 'push-stijl'-model door een groot aantal koppelingstegoeden uit te geven en vervolgens berichten te ontvangen zodra deze beschikbaar zijn zonder verdere interactie. Push wordt ondersteund via de [eigenschapsinstellingen MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) of [MessageReceiver.PrefetchCount.](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) Wanneer ze niet nul zijn, gebruikt de AMQP-client dit als het koppelingstegoed.

In deze context is het belangrijk om te begrijpen dat de klok voor de vervaldatum van de vergrendeling van het bericht in de entiteit begint wanneer het bericht uit de entiteit wordt genomen, niet wanneer het bericht op de kabel wordt geplaatst. Wanneer de client aangeeft gereed te zijn om berichten te ontvangen door een koppelingstegoed uit te geven, wordt daarom verwacht dat deze actief berichten binnen het netwerk binnenhalen en gereed zijn om ze te verwerken. Anders is de berichtvergrendeling mogelijk verlopen voordat het bericht zelfs wordt bezorgd. Het gebruik van het besturingselement voor de stroom van koppelingstegoeden moet direct de onmiddellijke gereedheid weerspiegelen voor het omgaan met beschikbare berichten die naar de ontvanger zijn verzonden.

Samengevat bieden de volgende secties een schematisch overzicht van de performatieve stroom tijdens verschillende API-interacties. In elke sectie wordt een andere logische bewerking beschreven. Sommige van deze interacties kunnen 'lui' zijn, wat betekent dat ze alleen kunnen worden uitgevoerd wanneer dat nodig is. Het maken van een afzender van een bericht kan pas een netwerkinteractie veroorzaken als het eerste bericht wordt verzonden of aangevraagd.

De pijlen in de volgende tabel tonen de richting van de performatieve stroom.

#### <a name="create-message-receiver"></a>Ontvanger van bericht maken

| Client | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numerieke handle},<br/>role =**ontvanger**,<br/>source={entity name},<br/>target={client link ID}<br/>) |Client wordt als ontvanger aan de entiteit gekoppeld |
| Service Bus antwoorden die het einde van de koppeling koppelen |<-- attach(<br/>name={link name},<br/>handle={numerieke handle},<br/>rol =**afzender**,<br/>source={entity name},<br/>target={client link ID}<br/>) |

#### <a name="create-message-sender"></a>Afzender van bericht maken

| Client | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numerieke handle},<br/>rol =**afzender**,<br/>source={client link ID},<br/>target={entity name}<br/>) |Geen actie |
| Geen actie |<-- attach(<br/>name={link name},<br/>handle={numerieke handle},<br/>rol =**ontvanger**,<br/>source={client link ID},<br/>target={entity name}<br/>) |

#### <a name="create-message-sender-error"></a>Afzender van bericht maken (fout)

| Client | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numerieke handle},<br/>rol =**afzender**,<br/>source={client link ID},<br/>target={entity name}<br/>) |Geen actie |
| Geen actie |<-- attach(<br/>name={link name},<br/>handle={numerieke handle},<br/>rol =**ontvanger**,<br/>source=null,<br/>target=null<br/>)<br/><br/><-- loskoppelen(<br/>handle={numerieke handle},<br/>closed =**true**,<br/>error={error info}<br/>) |

#### <a name="close-message-receiversender"></a>Ontvanger/afzender van bericht sluiten

| Client | Service Bus |
| --- | --- |
| --> loskoppelen(<br/>handle={numerieke handle},<br/>gesloten =**true**<br/>) |Geen actie |
| Geen actie |<-- loskoppelen(<br/>handle={numerieke handle},<br/>gesloten =**true**<br/>) |

#### <a name="send-success"></a>Verzenden (geslaagd)

| Client | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numerieke handle},<br/>delivery-tag={binary handle},<br/>settled =**false**,,more =**false**,<br/>state =**null**,<br/>resume =**false**<br/>) |Geen actie |
| Geen actie |<-- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled =**true**,<br/>state =**geaccepteerd**<br/>) |

#### <a name="send-error"></a>Verzenden (fout)

| Client | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numerieke handle},<br/>delivery-tag={binary handle},<br/>settled =**false**,,more =**false**,<br/>state =**null**,<br/>resume =**false**<br/>) |Geen actie |
| Geen actie |<-- disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled =**true**,<br/>state =**rejected**(<br/>error={error info}<br/>)<br/>) |

#### <a name="receive"></a>Ontvangen

| Client | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=1<br/>) |Geen actie |
| Geen actie |< transfer(<br/>delivery-id={numerieke handle},<br/>delivery-tag={binary handle},<br/>settled =**false**,<br/>more =**false**,<br/>state =**null**,<br/>resume =**false**<br/>) |
| --> disposition(<br/>role =**ontvanger**,<br/>first={delivery ID},<br/>last={delivery ID},<br/>settled =**true**,<br/>state =**geaccepteerd**<br/>) |Geen actie |

#### <a name="multi-message-receive"></a>Meerdere berichten ontvangen

| Client | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=3<br/>) |Geen actie |
| Geen actie |< transfer(<br/>delivery-id={numerieke handle},<br/>delivery-tag={binary handle},<br/>settled =**false**,<br/>more =**false**,<br/>state =**null**,<br/>resume =**false**<br/>) |
| Geen actie |< transfer(<br/>delivery-id={numerieke handle+1},<br/>delivery-tag={binary handle},<br/>settled =**false**,<br/>more =**false**,<br/>state =**null**,<br/>resume =**false**<br/>) |
| Geen actie |< transfer(<br/>delivery-id={numerieke handle+2},<br/>delivery-tag={binary handle},<br/>settled =**false**,<br/>more =**false**,<br/>state =**null**,<br/>resume =**false**<br/>) |
| --> disposition(<br/>role=receiver,<br/>first={delivery ID},<br/>last={delivery ID+2},<br/>settled =**true**,<br/>state =**geaccepteerd**<br/>) |Geen actie |

### <a name="messages"></a>Berichten

In de volgende secties wordt uitgelegd welke eigenschappen van de standaard AMQP-berichtsecties worden gebruikt door Service Bus en hoe ze worden Service Bus API-set.

Elke eigenschap die door de toepassing moet worden bepaald, moet worden toe te wijs aan de `application-properties` amqp-kaart.

#### <a name="header"></a>koptekst

| Veldnaam | Gebruik | API-naam |
| --- | --- | --- |
| Duurzaam |- |- |
| priority |- |- |
| ttl |Time to Live voor dit bericht |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| first-acquirer |- |- |
| aantal leveringen |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Veldnaam | Gebruik | API-naam |
| --- | --- | --- |
| message-id |Door de toepassing gedefinieerde id in vrije vorm voor dit bericht. Wordt gebruikt voor duplicaatdetectie. |[Messageid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Door de toepassing gedefinieerde gebruikers-id, niet geïnterpreteerd door Service Bus. |Niet toegankelijk via de Service Bus API. |
| tot |Door de toepassing gedefinieerde doel-id, niet geïnterpreteerd door Service Bus. |[Aan](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Onderwerp |Door de toepassing gedefinieerde berichtdoel-id, niet geïnterpreteerd door Service Bus. |[Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| antwoord op |Door de toepassing gedefinieerde indicator voor het antwoordpad, niet geïnterpreteerd door Service Bus. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Door de toepassing gedefinieerde correlatie-id, niet geïnterpreteerd door Service Bus. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-type |Door de toepassing gedefinieerde indicator voor inhoudstype voor de body, niet geïnterpreteerd door Service Bus. |[Contenttype](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-encoding |Door de toepassing gedefinieerde indicator voor inhoudcoderen voor de body, niet geïnterpreteerd door Service Bus. |Niet toegankelijk via de Service Bus API. |
| absolute verlooptijd |Declareer op welk absolute moment het bericht verloopt. Genegeerd bij invoer (header-TTL wordt waargenomen), gezaghebbend voor uitvoer. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| aanmaaktijd |Declareer op welk moment het bericht is gemaakt. Niet gebruikt door Service Bus |Niet toegankelijk via de Service Bus API. |
| group-id |Door de toepassing gedefinieerde id voor een gerelateerde set berichten. Wordt gebruikt voor Service Bus sessies. |[Sessionid](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| groepsvolgorde |Teller die het relatieve volgnummer van het bericht binnen een sessie identificeert. Genegeerd door Service Bus. |Niet toegankelijk via de Service Bus API. |
| reply-to-group-id |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Berichtaantekeningen

Er zijn nog enkele andere Service Bus-berichteigenschappen, die geen deel uitmaken van de EIGENSCHAPPEN van AMQP-berichten, en die worden doorgegeven zoals `MessageAnnotations` in het bericht.

| Kaartsleutel voor aantekeningen | Gebruik | API-naam |
| --- | --- | --- |
| x-opt-scheduled-enqueue-time | Declareer op welk moment het bericht op de entiteit moet worden weergegeven |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc) |
| x-opt-partition-key | Door de toepassing gedefinieerde sleutel die bepaalt in welke partitie het bericht moet worden geplaatst. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey) |
| x-opt-via-partition-key | Door de toepassing gedefinieerde partitiesleutelwaarde wanneer een transactie moet worden gebruikt voor het verzenden van berichten via een overdrachtswachtrij. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey) |
| x-opt-enqueued-time | Door de service gedefinieerde UTC-tijd die de werkelijke tijd van het enqueuingbericht vertegenwoordigt. Genegeerd bij invoer. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) |
| x-opt-sequence-number | Door de service gedefinieerd uniek nummer dat is toegewezen aan een bericht. | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) |
| x-opt-offset | Service-defined enqueued sequence number of the message. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber) |
| x-opt-locked-until | Door de service gedefinieerd. De datum en tijd totdat het bericht wordt vergrendeld in de wachtrij/het abonnement. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc) |
| x-opt-deadletter-source | Door de service gedefinieerd. Als het bericht wordt ontvangen van een wachtrij voor in een wachtrij met in wachtrij geplaatste berichten, is dit de bron van het oorspronkelijke bericht. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource) |

### <a name="transaction-capability"></a>Transactiemogelijkheid

Een transactie groepeert twee of meer bewerkingen in een uitvoeringsbereik. Een dergelijke transactie moet er van nature voor zorgen dat alle bewerkingen die behoren tot een bepaalde groep bewerkingen gezamenlijk slagen of mislukken.
De bewerkingen worden gegroepeerd op een id `txn-id` .

Voor transactionele interactie fungeert de client als een , waarmee de bewerkingen worden gecontroleerd `transaction controller` die moeten worden gegroepeerd. Service Bus-service fungeert als een `transactional resource` en voert werk uit zoals aangevraagd door de `transaction controller` .

De client en service communiceren via een `control link` , die wordt ingesteld door de client. De berichten en worden door de controller verzonden via de besturingskoppeling om respectievelijk transacties toe te wijzen en te voltooien (ze vertegenwoordigen niet de afbakening `declare` `discharge` van transactionele werkzaamheden). Het daadwerkelijke verzenden/ontvangen wordt niet uitgevoerd op deze koppeling. Elke aangevraagde transactionele bewerking wordt expliciet geïdentificeerd met de gewenste bewerking en kan daarom `txn-id` plaatsvinden op elke koppeling in de verbinding. Als de besturingskoppeling wordt gesloten terwijl er niet-geseedeerde transacties zijn gemaakt, worden al deze transacties onmiddellijk teruggedraaid en zullen pogingen om verdere transactionele werkzaamheden op deze transacties uit te voeren leiden tot fouten. Berichten op besturingskoppelingen mogen niet vooraf worden vereffend.

Elke verbinding moet een eigen besturingskoppeling initiëren om transacties te kunnen starten en beëindigen. De service definieert een speciaal doel dat fungeert als een `coordinator` . De client/controller brengt een besturingskoppeling naar dit doel tot leven. Besturingskoppeling ligt buiten de grens van een entiteit, dat wil zeggen dat dezelfde besturingskoppeling kan worden gebruikt om transacties voor meerdere entiteiten te initiëren en te verwerken.

#### <a name="starting-a-transaction"></a>Een transactie starten

Om transactioneel werk te starten. de controller moet een verkrijgen `txn-id` van de coördinator. Dit gebeurt door een `declare` typebericht te verzenden. Als de declaratie is geslaagd, reageert de coördinator met een disposition outcome, die de toegewezen `txn-id` bevat.

| Client (controller) | Richting | Service Bus (coördinator) |
| :--- | :---: | :--- |
| attach(<br/>name={link name},<br/>... ,<br/>rol =**afzender**,<br/>target =**Coördinator**<br/>) | ------> |  |
|  | <------ | attach(<br/>name={link name},<br/>... ,<br/>target=Coordinator()<br/>) |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state =**Declared**(<br/>**txn-id**={transaction ID}<br/>))|

#### <a name="discharging-a-transaction"></a>Een transactie in rekening brengen

De controller sluit het transactionele werk af door een bericht naar de `discharge` coördinator te verzenden. De controller geeft aan dat de controller het transactionele werk wil commiten of terugdraaien door de vlag `fail` in te stellen op de body van de autor. Als de coördinator de zelfruit niet kan voltooien, wordt het bericht geweigerd met dit resultaat met de `transaction-error` .

> Opmerking: fail=true verwijst naar Terugdraaien van een transactie en fail=false verwijst naar Commit.

| Client (controller) | Richting | Service Bus (coördinator) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transactie-id}<br/>))|
| | . . . <br/>Transactioneel werk<br/>op andere koppelingen<br/> . . . |
| transfer(<br/>delivery-id=57, ...)<br/>{ AmqpValue (<br/>**2016(txn-id=0, <br/> fail=false)**)}| ------> |  |
| | <------ | disposition( <br/> first=57, last=57, <br/>state =**Accepted()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Een bericht verzenden in een transactie

Alle transactionele werkzaamheden worden uitgevoerd met de transactionele leveringstoestand `transactional-state` die de txn-id bevat. In het geval van het verzenden van berichten wordt de transactionele status overgedragen door het overdrachtsframe van het bericht. 

| Client (controller) | Richting | Service Bus (coördinator) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transactie-id}<br/>))|
| transfer(<br/>handle=1,<br/>delivery-id=1, <br/>**state = <br/> TransactionalState( <br/> txn-id=0)**)<br/>{ nettolading }| ------> |  |
| | <------ | disposition( <br/> first=1, last=1, <br/>state=**TransactionalState( <br/> txn-id=0, <br/> outcome=Accepted()**))|

#### <a name="disposing-a-message-in-a-transaction"></a>Een bericht in een transactie weer geven

Bericht disposition bevat bewerkingen zoals `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer` . Als u deze bewerkingen binnen een transactie wilt uitvoeren, voert u `transactional-state` de door met de -dispositie.

| Client (controller) | Richting | Service Bus (coördinator) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={transactie-id}<br/>))|
| | <------ |transfer(<br/>handle =2,<br/>delivery-id=11, <br/>state=null)<br/>{ nettolading }|  
| disposition( <br/> first=11, last=11, <br/>state=**TransactionalState( <br/> txn-id=0, <br/> outcome=Accepted()**))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Geavanceerde Service Bus

In deze sectie worden de geavanceerde mogelijkheden van Azure Service Bus die zijn gebaseerd op conceptextensies voor AMQP, die momenteel worden ontwikkeld in de TECHNISCHE oasis voor AMQP. Service Bus implementeert de nieuwste versies van deze conceptversies en neemt wijzigingen aan die zijn geïntroduceerd wanneer deze concepten de standaardstatus bereiken.

> [!NOTE]
> Service Bus Geavanceerde berichtenbewerkingen worden ondersteund via een aanvraag-/antwoordpatroon. De details van deze bewerkingen worden beschreven in het artikel [AMQP 1.0 in Service Bus: op](service-bus-amqp-request-response.md)aanvraag gebaseerde bewerkingen.
> 
> 

### <a name="amqp-management"></a>AMQP-beheer

De AMQP-beheerspecificatie is de eerste van de conceptextensies die in dit artikel worden besproken. Deze specificatie definieert een reeks protocollen die boven op het AMQP-protocol zijn gelaagd waarmee beheerinteracties met de berichteninfrastructuur via AMQP mogelijk zijn. De specificatie definieert algemene bewerkingen zoals *maken,* *lezen,* *bijwerken* en *verwijderen* voor het beheren van entiteiten in een berichteninfrastructuur en een set querybewerkingen.

Al deze gebaren vereisen een interactie tussen aanvraag en antwoord tussen de client en de berichteninfrastructuur. Daarom definieert de specificatie hoe dat interactiepatroon boven op AMQP moet worden gemodelleerd: de client maakt verbinding met de berichteninfrastructuur, initieert een sessie en maakt vervolgens een paar koppelingen. Op de ene koppeling fungeert de client als afzender en op de andere fungeert deze als ontvanger, waardoor een paar koppelingen worden aanmaken die als een bi-directioneel kanaal kunnen fungeren.

| Logische bewerking | Client | Service Bus |
| --- | --- | --- |
| Aanvraagreactiepad maken |--> attach(<br/>name={*link name*},<br/>handle={*numerieke greep*},<br/>rol =**afzender**,<br/>source =**null**,<br/>target="myentity/$management"<br/>) |Geen actie |
| Aanvraagreactiepad maken |Geen actie |\<-- attach(<br/>name={*link name*},<br/>handle={*numerieke greep*},<br/>rol =**ontvanger**,<br/>source=null,<br/>target="myentity"<br/>) |
| Aanvraagreactiepad maken |--> attach(<br/>name={*link name*},<br/>handle={*numerieke greep*},<br/>role =**ontvanger**,<br/>source="myentity/$management",<br/>target="myclient$id"<br/>) | |
| Aanvraagreactiepad maken |Geen actie |\<-- attach(<br/>name={*link name*},<br/>handle={*numerieke greep*},<br/>rol =**afzender**,<br/>source="myentity",<br/>target="myclient$id"<br/>) |

Als dat paar koppelingen is gekoppeld, is de implementatie van de aanvraag/het antwoord eenvoudig: een aanvraag is een bericht dat wordt verzonden naar een entiteit in de berichteninfrastructuur die dit patroon begrijpt. In dat aanvraagbericht wordt het *antwoordveld* in de sectie  Eigenschappen ingesteld op de doel-id voor de koppeling waarop het antwoord moet worden gegeven.  De afhandelingsentiteit verwerkt de aanvraag en  levert vervolgens het antwoord via de koppeling waarvan de doel-id overeenkomt met de aangegeven *antwoord-op-id.*

Het patroon vereist natuurlijk dat de clientcontainer en de door de client gegenereerde id voor de antwoordbestemming uniek zijn voor alle clients en om veiligheidsredenen ook moeilijk te voorspellen zijn.

De berichtuitwisselingen die worden gebruikt voor het beheerprotocol en voor alle andere protocollen die hetzelfde patroon gebruiken, vinden op toepassingsniveau plaatsvinden; Ze definiëren geen nieuwe amqp-protocollen. Dat is opzettelijk, zodat toepassingen direct kunnen profiteren van deze extensies met compatibele AMQP 1.0-stacks.

Service Bus implementeert momenteel geen van de kernfuncties van de beheerspecificatie, maar het aanvraag-/antwoordpatroon dat is gedefinieerd door de beheerspecificatie is een basis voor de op claims gebaseerde beveiligingsfunctie en voor vrijwel alle geavanceerde mogelijkheden die in de volgende secties worden besproken:

### <a name="claims-based-authorization"></a>Autorisatie op basis van claims

Het concept van de SPECIFICATIE op basis van AMQP-claims bouwt voort op het aanvraag-/antwoordpatroon van de beheerspecificatie en beschrijft een ge generaliseerd model voor het gebruik van federatief beveiligingstokens met AMQP.

Het standaardbeveiligingsmodel van AMQP dat in de inleiding wordt besproken, is gebaseerd op SASL en kan worden geïntegreerd met de AMQP-verbindingshandhake. Het gebruik van SASL heeft als voordeel dat het een extensible model biedt waarvoor een set mechanismen is gedefinieerd waarvan elk protocol dat formeel gebruik maakt van SASL, kan profiteren. Een van deze mechanismen is 'PLAIN' voor de overdracht van gebruikersnamen en wachtwoorden, 'EXTERNAL' om te binden aan beveiliging op TLS-niveau, 'ANONYMOUS' om aan te geven dat er geen expliciete verificatie/autorisatie is en een groot aantal aanvullende mechanismen waarmee verificatie- en/of autorisatiereferenties of tokens kunnen worden doorgeven.

De SASL-integratie van AMQP heeft twee nadelen:

* Alle referenties en tokens zijn verbonden met de verbinding. Een berichteninfrastructuur kan gedifferentieerd toegangsbeheer per entiteit bieden; bijvoorbeeld door de bearer van een token toe te staan naar wachtrij A te verzenden, maar niet naar wachtrij B. Nu de autorisatiecontext is verankerd in de verbinding, is het niet mogelijk om één verbinding te gebruiken en toch verschillende toegangstokens te gebruiken voor wachtrij A en wachtrij B.
* Toegangstokens zijn doorgaans slechts tijdelijk geldig. Deze geldigheid vereist dat de gebruiker periodiek tokens opnieuw opvraagt en biedt de gebruiker de mogelijkheid om de uitgifte van een nieuw token te weigeren als de toegangsrechten van de gebruiker zijn gewijzigd. AMQP-verbindingen kunnen lange tijd duren. Het SASL-model biedt alleen de mogelijkheid om een token in te stellen tijdens de verbinding, wat betekent dat de berichteninfrastructuur de client moet loskoppelen wanneer het token is verlopen of dat het het risico moet accepteren dat verdere communicatie met een client met toegangsrechten voorlopig is ingetrokken.

De AMQP-SPECIFICATIE VAN DEP, geïmplementeerd door Service Bus, biedt een elegante tijdelijke oplossing voor beide problemen: Hiermee kan een client toegangstokens aan elk knooppunt koppelen en deze tokens bijwerken voordat ze verlopen, zonder de berichtstroom te onderbreken.

MET WORDT EEN VIRTUEEL beheer-knooppunt met de *naam $cbs* definieert dat moet worden geleverd door de berichteninfrastructuur. Het beheerknooppunt accepteert tokens namens andere knooppunten in de berichteninfrastructuur.

De protocolbewegingen zijn een uitwisseling van aanvragen/antwoorden zoals gedefinieerd in de beheerspecificatie. Dit betekent dat de client een  paar koppelingen met het $cbs-knooppunt maakt en vervolgens een aanvraag door geeft op de uitgaande koppeling en vervolgens wacht op het antwoord op de inkomende koppeling.

Het aanvraagbericht heeft de volgende toepassingseigenschappen:

| Sleutel | Optioneel | Waardetype | Waarde-inhoud |
| --- | --- | --- | --- |
| bewerking |Nee |tekenreeks |**put-token** |
| type |Nee |tekenreeks |Het type token dat wordt gebruikt. |
| naam |Nee |tekenreeks |De doelgroep waarop het token van toepassing is. |
| Verloop |Ja |tijdstempel |De verlooptijd van het token. |

De *eigenschap name* identificeert de entiteit waaraan het token moet worden gekoppeld. In Service Bus is dit het pad naar de wachtrij of het onderwerp/abonnement. De *eigenschap type* identificeert het tokentype:

| Tokentype | Beschrijving van token | Body Type | Notities |
| --- | --- | --- | --- |
| jwt |JSON Web Token (JWT) |AMQP-waarde (tekenreeks) | |
| servicebus.windows.net:sastoken |Service Bus SAS-token |AMQP-waarde (tekenreeks) |- |

Tokens geven rechten. Service Bus over drie fundamentele rechten: 'Verzenden' maakt verzenden mogelijk, 'Luisteren' maakt ontvangen mogelijk en 'Beheren' maakt het bewerken van entiteiten mogelijk. Service Bus SAS-tokens verwijzen naar regels die zijn geconfigureerd voor de naamruimte of entiteit. Deze regels worden geconfigureerd met rechten. Als u het token ondertekent met de sleutel die aan die regel is gekoppeld, geeft het token dus de respectieve rechten weer. Met het token dat is gekoppeld aan een entiteit met behulp van *een put-token* kan de verbonden client communiceren met de entiteit volgens de tokenrechten. Voor een koppeling waarbij de client de rol van *afzender krijgt,* is het recht 'Verzenden' vereist; Voor het nemen van *de ontvangerrol* is het recht 'Luisteren' vereist.

Het antwoordbericht heeft de volgende *waarden voor toepassingseigenschappen*

| Sleutel | Optioneel | Waardetype | Inhoud van waarde |
| --- | --- | --- | --- |
| statuscode |Nee |int |HTTP-antwoordcode **[RFC2616]**. |
| status-description |Ja |tekenreeks |Beschrijving van de status. |

De client kan het *put-token herhaaldelijk aanroepen* en voor elke entiteit in de berichteninfrastructuur. De tokens zijn ingesteld op de huidige client en zijn verankerd in de huidige verbinding, wat betekent dat de server alle bewaard tokens uitvalt wanneer de verbinding wordt verwijderd.

De huidige Service Bus-implementatie staat ALLEEN SAS toe in combinatie met de SASL-methode 'ANONYMOUS'. Er moet altijd een SSL/TLS-verbinding bestaan vóór de SASL-handshake.

Het ANONIEME mechanisme moet daarom worden ondersteund door de gekozen AMQP 1.0-client. Anonieme toegang betekent dat de eerste verbindingshandhake, inclusief het maken van de eerste sessie, wordt gemaakt zonder Service Bus weten wie de verbinding maakt.

Zodra de verbinding en sessie tot stand  zijn gebracht, zijn het koppelen van de koppelingen aan het $cbs-knooppunt en het verzenden van de *put-tokenaanvraag* de enige toegestane bewerkingen. Een geldig token moet worden ingesteld met behulp van een *put-token-aanvraag* voor een entiteits-knooppunt binnen 20 seconden nadat de verbinding tot stand is gebracht, anders wordt de verbinding in één keer Service Bus.

De client is vervolgens verantwoordelijk voor het bijhouden van de verloopdatum van het token. Wanneer een token verloopt, Service Bus alle koppelingen op de verbinding met de respectieve entiteit direct laten vallen. Om te voorkomen dat er een probleem optreedt, kan de client het  token voor het knooppunt op elk moment vervangen door een nieuw token via het virtuele $cbs-beheerpunt met dezelfde beweging van het *put-token* en zonder dat het nettoladingverkeer dat via verschillende koppelingen stroomt, in de weg zit.

### <a name="send-via-functionality"></a>Send-via-functionaliteit

[Send-via/Transfer sender](service-bus-transactions.md#transfers-and-send-via) is een functionaliteit waarmee Service Bus een bepaald bericht via een andere entiteit kan doorsturen naar een doelentiteit. Deze functie wordt gebruikt om bewerkingen uit te voeren tussen entiteiten in één transactie.

Met deze functionaliteit maakt u een afzender en maakt u de koppeling naar de `via-entity` . Tijdens het tot stand brengen van de koppeling wordt aanvullende informatie doorgegeven om de werkelijke bestemming van de berichten/overdrachten op deze koppeling vast te stellen. Zodra de koppeling is geslaagd, worden alle berichten die via  deze koppeling worden verzonden, automatisch doorgestuurd naar de doelentiteit *via -entity*. 

> Opmerking: Verificatie moet worden uitgevoerd voor zowel *via-entity* als *destination-entity* voordat u deze koppeling tot stand kunt brengen.

| Client | Richting | Service Bus |
| :--- | :---: | :--- |
| attach(<br/>name={link name},<br/>role=sender,<br/>source={client link ID},<br/>target =**{via-entity}**,<br/>**properties=map [( <br/> com.microsoft:transfer-destination-address= <br/> {destination-entity} )]** ) | ------> | |
| | <------ | attach(<br/>name={link name},<br/>role=receiver,<br/>source={client link ID},<br/>target={via-entity},<br/>properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )] ) |

## <a name="next-steps"></a>Volgende stappen

Ga naar de volgende koppelingen voor meer informatie over AMQP:

* [Service Bus AMQP-overzicht]
* [AMQP 1.0-ondersteuning voor Service Bus gepartities en onderwerpen]
* [AMQP in Service Bus voor Windows Server]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Service Bus AMQP-overzicht]: service-bus-amqp-overview.md
[AMQP 1.0-ondersteuning voor Service Bus gepartities en onderwerpen]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)