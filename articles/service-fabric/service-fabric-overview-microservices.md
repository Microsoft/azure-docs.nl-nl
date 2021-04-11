---
title: Inleiding tot microservices in Azure
description: Een overzicht van het bouwen van Cloud toepassingen met een micro Services-benadering is belang rijk voor het ontwikkelen van moderne toepassingen en hoe Azure Service Fabric een platform biedt om dit te bereiken.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 46d4be7321057bd7a5ee19e0aca136c8c60d43bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044427"
---
# <a name="why-use-a-microservices-approach-to-building-applications"></a>Waarom een micro Services-benadering gebruiken voor het ontwikkelen van toepassingen

Voor software ontwikkelaars is het samen stellen van een toepassing in onderdeel onderdelen niets nieuw. Normaal gesp roken wordt een gelaagde benadering gebruikt, met een back-end-opslag, bedrijfs logica op middelste laag en een front-end gebruikers interface (UI). Wat in de afgelopen *jaren is gewijzigd* , is dat ontwikkel aars gedistribueerde toepassingen bouwen voor de Cloud.

Hier volgen enkele veranderende bedrijfs behoeften:

* Een service die op schaal wordt gebouwd en bediend om klanten in nieuwe geografische regio's te bereiken.
* Een snellere levering van functies en mogelijkheden om op een flexibele manier op de vraag van klanten te reageren.
* Verbeterd resource gebruik om de kosten te verlagen.

Deze zakelijke behoeften zijn van invloed op de *manier waarop* we toepassingen bouwen.

Zie micro [Services: een Application revolutie die wordt ondersteund door de Cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)voor meer informatie over de Azure-benadering van micro Services.

## <a name="monolithic-vs-microservices-design-approach"></a>Monolithische versus ontwerp benadering van micro Services

Toepassingen ontwikkelen in de loop van de tijd. Succes volle toepassingen ontwikkelen door nuttig te zijn voor mensen. Niet-geslaagde toepassingen worden niet verder ontwikkeld en zijn uiteindelijk afgeschaft. Dit is de vraag: hoeveel weet u vandaag nog over uw vereisten en wat ze in de toekomst zullen blijven? Stel bijvoorbeeld dat u een rapportage toepassing bouwt voor een afdeling in uw bedrijf. U zeker weet dat de toepassing alleen van toepassing is binnen het bereik van uw bedrijf en dat de rapporten niet lang worden bewaard. Uw aanpak wijkt af van het maken van een service die video-inhoud levert aan tien tallen miljoenen klanten.

Soms is het verkrijgen van iets uit de deur als een test van het concept de aandrijf factor. U weet dat de toepassing later opnieuw kan worden ontworpen. Er is een beetje meer punten in over-engineering iets dat nooit wordt gebruikt. Aan de andere kant, wanneer bedrijven voor de Cloud bouwen, is de verwachte groei en het gebruik. Groei en schaal zijn onvoorspelbaar. We willen snel prototypen bouwen en weten dat we een pad hebben dat toekomstige succes kan afhandelen. Dit is de Lean Startup-benadering: bouwen, meten, leren en herhalen.

Tijdens de client-en server vormgeving zullen we zich richten op het bouwen van gelaagde toepassingen door gebruik te maken van specifieke technologieën in elke laag. De term *monolithische* toepassing is opgetreden om deze benaderingen te beschrijven. De interfaces bevinden zich tussen de lagen en een nauw keurig gekoppeld ontwerp tussen onderdelen van elke laag wordt gebruikt. Ontwikkel aars die zijn ontworpen en gefactord klassen die in bibliotheken zijn gecompileerd en samen met een paar uitvoer bare bestanden en Dll's zijn gekoppeld.

Er zijn voor delen voor een monolithische-ontwerp benadering. Monolithische-toepassingen zijn vaak eenvoudiger te ontwerpen en aanroepen tussen onderdelen zijn sneller omdat deze aanroepen vaak via de communicatie tussen interprocess (IPC) zijn. Daarnaast test iedereen één product, dat een efficiëntere manier is om human resources te gebruiken. Het nadeel is dat er sprake is van een nauwe koppeling tussen gelaagde lagen en u geen afzonderlijke onderdelen kunt schalen. Als u oplossingen of upgrades wilt uitvoeren, moet u wachten tot anderen hun tests hebben voltooid. Het is moeilijker om flexibel te zijn.

Micro Services is gericht op deze nauw keurigheid en is beter in overeenstemming met de voor gaande zakelijke vereisten. Maar ze hebben ook beide voor delen en passiva. De voor delen van micro Services zijn dat elk van beide doorgaans eenvoudiger bedrijfs functionaliteit inkapselt, die u kunt uitbreiden of in, testen, implementeren en beheren. Een belang rijk voor deel van een micro Services-benadering is dat teams meer worden aangedreven door bedrijfs scenario's dan met technologie. Kleinere teams ontwikkelen een micro service op basis van een klant scenario en gebruiken alle technologieën die ze willen gebruiken.

Met andere woorden, de organisatie hoeft geen technische voor het onderhoud van micro service-toepassingen te standaardiseren. Individuele teams die eigen services kunnen doen, zijn afhankelijk van de team expertise of wat het meest geschikt is om het probleem op te lossen. In de praktijk verdient een aantal aanbevolen technologieën, zoals een bepaald NoSQL-archief of Web Application Framework, de voor keur.

Het nadeel van micro Services is dat u meer afzonderlijke entiteiten moet beheren en met complexere implementaties en versie beheer kunt werken. Netwerk verkeer tussen de micro Services neemt toe, evenals de bijbehorende netwerk latentie. Veel intensieve, granulaire Services kunnen leiden tot een prestatie Nightmare. Zonder hulp middelen die u helpen bij het weer geven van deze afhankelijkheden, is het moeilijk om het hele systeem te zien.

Standaarden zorgen ervoor dat de micro Services aanpakt door op te geven hoe alleen de dingen die u nodig hebt van een service te communiceren en te verdragen, in plaats van in stijve contracten. Het is belang rijk dat u deze contracten vooraf in het ontwerp definieert omdat de services onafhankelijk van elkaar worden bijgewerkt. Een andere beschrijving die is gemunt voor het ontwerpen van een micro Services-benadering is ' verfijnde ' service-georiënteerde architectuur (SOA) '.

***De methode voor het ontwerpen van het micro Services-ontwerp is in het eenvoudigst gebaseerd op een losgekoppelde Federatie van services, met onafhankelijke wijzigingen aan elke en overeengekomen standaarden voor communicatie.***

Naarmate er meer Cloud toepassingen worden geproduceerd, hebben mensen ontdekt dat deze ontleding van de algehele toepassing in onafhankelijke, op scenario's gerichte Services, een betere lange termijn benadering is.

## <a name="comparison-between-application-development-approaches"></a>Vergelijking tussen methoden voor het ontwikkelen van toepassingen

![Ontwikkeling van platform toepassingen Service Fabric][Image1]

1) Een monolithische-toepassing bevat domein-specifieke functionaliteit en is doorgaans onderverdeeld in functionele lagen zoals web, Business en data.

2) U kunt een monolithische-toepassing schalen door deze te klonen op meerdere servers/virtuele machines/containers.

3) Een micro service-toepassing scheidt functionaliteit in afzonderlijke, kleinere Services.

4) De micro Services-benadering wordt uitgeschaald door elke service afzonderlijk te implementeren, waarbij instanties van deze services worden gemaakt op servers/virtuele machines/containers.

Ontwerpen met een micro Services-benadering is niet geschikt voor alle projecten, maar wordt nauw keuriger uitgelijnd met de bedrijfs doelstellingen die eerder zijn beschreven. Beginnen met een monolithische-benadering kan zinvol zijn als u weet dat u de mogelijkheid hebt om de code later te bewerken in een micro Services-ontwerp. U begint meestal met een monolithische-toepassing en verbreekt deze langzaam in fasen, beginnend met de functionele gebieden die meer schaalbaar of flexibel moeten zijn.

Wanneer u gebruikmaakt van een micro Services-benadering, stelt u uw toepassing van veel kleine Services samen. Deze services worden uitgevoerd in containers die worden geïmplementeerd op een cluster van machines. Kleinere teams ontwikkelen een service die gericht is op een scenario en onafhankelijk test, versie, implementatie en schaal van elke service, zodat de gehele toepassing kan worden ontwikkeld.

## <a name="what-is-a-microservice"></a>Wat is een microservice?

Er zijn verschillende definities van micro Services. Maar de meeste van deze kenmerken van micro Services zijn algemeen geaccepteerd:

* Een klant-of bedrijfs scenario inkapselen. Welk probleem verhelpt u?
* Ontwikkeld door een klein technisch team.
* Geschreven in elke programmeer taal, met behulp van een Framework.
* Bestaan uit code en optioneel, waarbij beide afzonderlijk zijn versie, geïmplementeerd en geschaald.
* Communiceer met andere micro Services via goed gedefinieerde interfaces en protocollen.
* Hebben unieke namen (Url's) die worden gebruikt om de locatie op te lossen.
* Blijf consistent en beschikbaar in de aanwezigheid van fouten.

U kunt als volgt een som berekenen:

***Micro service-toepassingen bestaan uit kleine, onafhankelijke versie gerichte en schaal bare Services op basis van klanten die met elkaar communiceren via standaard protocollen met goed gedefinieerde interfaces.***

### <a name="written-in-any-programming-language-using-any-framework"></a>Geschreven in elke programmeer taal, met behulp van een Framework

Als ontwikkel aars willen we gratis een taal of Framework kiezen, afhankelijk van onze vaardig heden en de behoeften van de service die we maken. Voor sommige services kunt u de prestatie voordelen van C++ boven iets anders opwaarderen. Voor anderen is het eenvoudiger om beheerde ontwikkeling te ontwikkelen die u van C# of Java krijgt. In sommige gevallen moet u mogelijk een specifieke partner bibliotheek, een technologie voor gegevens opslag of een methode gebruiken voor het weer geven van de service aan clients.

Nadat u een technologie hebt gekozen, moet u rekening houden met het beheer van de operationele of de levens cyclus en het schalen van de service.

### <a name="allows-code-and-state-to-be-independently-versioned-deployed-and-scaled"></a>Code en status kunnen onafhankelijk van versie worden uitgevoerd, worden geïmplementeerd en geschaald

Hoe u ook uw micro Services, de code en optioneel de status schrijft, moeten onafhankelijk worden geïmplementeerd, geüpgraded en geschaald. Dit probleem is moeilijk op te lossen, omdat het beschikbaar is voor uw keuze van technologieën. Voor schalen moet u weten hoe u de code partitioneert (of Shard) en dat de status lastig is. Wanneer de code en status gebruikmaken van verschillende technologieën, die tegenwoordig gebruikelijk zijn, moeten de implementatie scripts voor uw micro service beide kunnen worden geschaald. Deze schei ding gaat ook over flexibiliteit en flexibiliteit, zodat u een upgrade kunt uitvoeren van een aantal micro Services zonder dat u deze tegelijk hoeft bij te werken.

We gaan terug naar onze vergelijking van de monolithische-en micro Services-benaderingen. Dit diagram toont de verschillen in de methoden voor het opslaan van de status:

#### <a name="state-storage-for-the-two-approaches"></a>Status opslag voor de twee benaderingen

![Status opslag van Service Fabric platform][Image2]

***De monolithische-benadering, aan de linkerkant, heeft één data base en lagen van specifieke technologieën.***

***De micro Services-aanpak aan de rechter kant heeft een grafiek van onderling verbonden micro services waarbij de status doorgaans van toepassing is op de micro service en verschillende technologieën worden gebruikt.***

In een monolithische-benadering gebruikt de toepassing meestal een enkele data base. Het voor deel van het gebruik van één data base is dat deze zich op één locatie bevindt. Dit maakt het eenvoudig om te implementeren. Elk onderdeel kan één tabel hebben om de status op te slaan. Teams moeten de status strikt onderscheiden, wat een uitdaging is. Het onvermijdelijk is dat iemand een kolom aan een bestaande klanten tabel toevoegt, een koppeling tussen tabellen doet en afhankelijkheden maakt op de opslaglaag. Als dit gebeurt, kunt u de afzonderlijke onderdelen niet schalen.

In de micro Services-benadering beheert en opslaat elke service zijn eigen status. Elke service is verantwoordelijk voor het schalen van zowel code als status, zodat deze voldoet aan de vereisten van de service. Een nadeel is dat wanneer u weer gaven of query's moet maken van de gegevens van uw toepassing, u een query moet uitvoeren in meerdere status archieven. Dit probleem wordt doorgaans opgelost door een afzonderlijke micro service die een weer gave bouwt over een verzameling micro Services. Als u meerdere niet-geplande query's moet uitvoeren op de gegevens, kunt u overwegen om de gegevens van elke micro service naar een Data Warehouse-service te schrijven voor offline analyse.

Micro Services hebben een versie nummer. Het is mogelijk dat verschillende versies van een micro service naast elkaar worden uitgevoerd. Een nieuwere versie van een micro service kan mislukken tijdens een upgrade en moet worden teruggedraaid naar een eerdere versie. Versie beheer is ook handig voor het testen van A/B, waarbij verschillende gebruikers verschillende versies van de service ervaren. Het is bijvoorbeeld gebruikelijk om een micro service voor een specifieke set klanten bij te werken om nieuwe functies te testen voordat u deze uitgebreid uitbreidt.

### <a name="interacts-with-other-microservices-over-well-defined-interfaces-and-protocols"></a>Communiceert met andere micro Services via goed gedefinieerde interfaces en protocollen

In de afgelopen tien jaar is uitgebreide informatie gepubliceerd met een beschrijving van de communicatie patronen in service georiënteerde architecturen. Over het algemeen gebruikt service communicatie een REST-benadering met HTTP-en TCP-protocollen en XML of JSON als de serialisatie-indeling. In het perspectief van de interface is het een manier om een webontwerp te maken. Maar het is niet meer mogelijk om binaire protocollen of uw eigen gegevens indelingen te gebruiken. Houd er rekening mee dat mensen over een lastigere tijd gebruik kunnen maken met uw micro Services als deze protocollen en indelingen niet open beschikbaar zijn.

### <a name="has-a-unique-name-url-used-to-resolve-its-location"></a>Heeft een unieke naam (URL) die wordt gebruikt om de locatie op te lossen

Uw micro service moet adresseerbaar zijn waar deze wordt uitgevoerd. Als u op de hoogte bent van computers en er een bepaalde micro service wordt uitgevoerd, kunnen de dingen snel worden beschadigd.

Op dezelfde manier als een bepaalde URL door DNS naar een bepaalde computer wordt omgezet, moet uw micro service een unieke naam hebben zodat de huidige locatie kan worden gedetecteerd. Micro Services heeft adresseer bare namen nodig die onafhankelijk zijn van de infra structuur waarop ze worden uitgevoerd. Dit betekent dat er een interactie is tussen hoe uw service wordt geïmplementeerd en hoe deze wordt gedetecteerd, omdat er een service register moet zijn. Wanneer een machine uitvalt, moet de register service aangeven waar de service naartoe is verplaatst.

### <a name="remains-consistent-and-available-in-the-presence-of-failures"></a>Blijft consistent en beschikbaar in de aanwezigheid van fouten

Het afhandelen van onverwachte fouten is een van de moeilijk te verhelpen problemen, met name in een gedistribueerd systeem. Veel van de code die we als ontwikkel aars schrijven, is voor het verwerken van uitzonde ringen. Tijdens het testen best Eden we ook de meeste tijd aan het verwerken van uitzonde ringen. Het proces is meer betrokken dan het schrijven van code voor het afhandelen van fouten. Wat gebeurt er wanneer de computer waarop de micro service wordt uitgevoerd, mislukt? U moet de fout ontdekken. Dit is een hard probleem. Maar u moet ook uw micro service opnieuw starten.

Voor Beschik baarheid moet een micro service tot fouten leiden en op een andere computer opnieuw worden opgestart. Naast deze tolerantie vereisten is het niet nodig om gegevens te verliezen en moeten de gegevens consistent blijven.

Tolerantie is moeilijk te realiseren wanneer er fouten optreden tijdens een toepassings upgrade. De micro service, die werkt met het implementatie systeem, hoeft niet te worden hersteld. De IT-afdeling moet bepalen of deze kan blijven door gaan naar de nieuwere versie of terugdraait naar een eerdere versie om een consistente status te behouden. U moet rekening houden met een aantal vragen, zoals of er voldoende computers beschikbaar zijn om door te gaan en om eerdere versies van de micro service te herstellen. Als u deze beslissingen wilt nemen, hebt u de micro service nodig om status informatie te verzenden.

### <a name="reports-health-and-diagnostics"></a>Rapporteert de status en diagnostische gegevens

Het lijkt misschien duidelijk en het is vaak te zien, maar een micro service moet zijn status en diagnostische gegevens rapporteren. Anders hebt u weinig inzicht in de status van een bewerkings perspectief. Het correleren van diagnostische gebeurtenissen in een reeks onafhankelijke services, en het afstemmen van de computer klok scheefheden maken van de gebeurtenis volgorde, is lastig. Op dezelfde manier als u communiceert met een micro service via overeengekomen protocollen en gegevens indelingen, moet u de manier van vastleggen van de status-en diagnostische gebeurtenissen voor het uitvoeren van query's in een gebeurtenis archief in een logboek voor het zoeken en weer geven. Met een micro Services-benadering moeten verschillende teams akkoord gaan met één indeling voor logboek registratie. Er moet een consistente aanpak zijn voor het weer geven van diagnostische gebeurtenissen in de toepassing als geheel.

De status wijkt af van de diagnostische gegevens. De status is van toepassing op de huidige status van de micro service om passende maat regelen te nemen. Een goed voor beeld is het werken met upgrade-en implementatie mechanismen voor het onderhouden van Beschik baarheid. Hoewel een service momenteel niet in orde is vanwege een proces storing of het opnieuw opstarten van de computer, kan de service nog steeds operationeel zijn. Het laatste wat u nodig hebt, is om de situatie te verergeren door een upgrade te starten. De beste aanpak is om eerst te onderzoeken of tijd te geven voor de micro service te herstellen. Met status gebeurtenissen van een micro service kunnen wij weloverwogen beslissingen nemen en, in feite, hulp bieden bij het maken van Self-Retoucheer Services.

## <a name="guidance-for-designing-microservices-on-azure"></a>Richt lijnen voor het ontwerpen van micro Services op Azure

Ga naar het Azure Architecture Center voor meer informatie over het [ontwerpen en bouwen van micro Services in azure](/azure/architecture/microservices/).

## <a name="service-fabric-as-a-microservices-platform"></a>Service Fabric als een micro services-platform

Azure Service Fabric opgetreden toen micro soft overstapt van het leveren van producten in de periode, meestal monolithische, om services te leveren. De ervaring van het bouwen en gebruiken van grote Services, zoals Azure SQL Database en Azure Cosmos DB, Shaped Service Fabric. Het platform is na verloop van tijd ontwikkeld naarmate er meer services zijn aangenomen. Service Fabric moest niet alleen worden uitgevoerd in azure, maar ook in zelfstandige implementaties van Windows Server.

***Het doel van Service Fabric is het oplossen van de vaste problemen bij het bouwen en uitvoeren van een service en het efficiënt gebruiken van infrastructuur resources, zodat teams bedrijfs problemen kunnen oplossen met behulp van een micro Services-benadering.***

In deze korte video worden Service Fabric en microservices geïntroduceerd:
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

Service Fabric helpt u bij het bouwen van toepassingen die gebruikmaken van een micro Services-benadering door het volgende te bieden:

* Een platform dat systeem services biedt voor het implementeren, upgraden, detecteren en opnieuw starten van mislukte services, het detecteren van services, het beheren van de status en de controle status.
* De mogelijkheid om toepassingen te implementeren die worden uitgevoerd in containers of als processen. Service Fabric is een container en proces-Orchestrator.
* Productief programmeer-Api's om u te helpen bij het bouwen van toepassingen als micro Services: [ASP.net core, reliable actors en reliable Services](service-fabric-choose-framework.md). U kunt bijvoorbeeld status-en diagnostische gegevens ophalen, maar u kunt ook profiteren van de ingebouwde hoge Beschik baarheid.

***Service Fabric is neutraal over hoe u uw service bouwt en u kunt elke technologie gebruiken. Dit biedt echter ingebouwde programmeer-Api's waarmee u eenvoudig micro Services kunt bouwen.***

### <a name="migrating-existing-applications-to-service-fabric"></a>Bestaande toepassingen migreren naar Service Fabric

Met Service Fabric kunt u bestaande code hergebruiken en moderniseren met nieuwe micro Services. Er zijn vijf fasen voor de modernisering van toepassingen, en u kunt elke fase starten en stoppen. De fasen zijn:

1) Begin met een traditionele monolithische-toepassing.  
2) Migrat. Gebruik containers of uitvoer bare gast bestanden om bestaande code in Service Fabric te hosten.  
3) Moderniseren. Voeg nieuwe micro Services naast bestaande container code toe.  
4) Innoveren. Splits de toepassing monolithische in micro Services op basis van behoefte.  
5) Transformeer toepassingen in micro Services. Transformeer bestaande monolithische-toepassingen of bouw nieuwe Ontwikkel-toepassingen.

![Migratie naar micro Services][Image3]

Denk eraan dat u *in elk van deze fasen kunt starten en stoppen*. U hoeft niet naar de volgende fase te gaan. 

Laten we eens kijken naar voor beelden voor elk van deze fasen.

**Migreren**  
Om twee redenen migreren veel bedrijven bestaande monolithische-toepassingen in containers:

* Kosten reductie, hetzij als gevolg van consolidatie en verwijdering van bestaande hardware of vanwege het uitvoeren van toepassingen met een hogere dichtheid.
* Een consistent implementatie contract tussen ontwikkeling en bewerkingen.

Kosten reducties zijn eenvoudig. Bij micro soft worden veel bestaande toepassingen gecontainerd, waardoor miljoenen dollars worden bespaard. Een consistente implementatie is moeilijker te evalueren, maar is even belang rijk. Dit betekent dat ontwikkel aars de technologieën kunnen kiezen die hieraan voldoen, maar de bewerkingen accepteren slechts één methode voor het implementeren en beheren van de toepassingen. Het vermindert van de werking van de complexiteit van de ondersteuning van verschillende technologieën zonder dat ontwikkel aars alleen bepaalde onderdelen hoeven te kiezen. In wezen wordt elke toepassing in een container geïmplementeerd in zelfstandige implementatie-installatie kopieën.

Veel organisaties stoppen hier. Ze hebben al de voor delen van containers en Service Fabric biedt de volledige beheer ervaring, inclusief implementatie, upgrades, versie beheer, terugdraai acties en status controle.

**Moderniseren**  
Modernisering is het toevoegen van nieuwe services naast bestaande container code. Als u nieuwe code wilt schrijven, kunt u het beste kleine stappen uitvoeren in het micro Services-pad. Dit kan betekenen dat er een nieuw REST API-eind punt of nieuwe bedrijfs logica wordt toegevoegd. Op deze manier start u het proces van het bouwen van nieuwe micro Services en het ontwikkelen en implementeren van deze procedures.

**Innoveren**  
Een micro Services-benadering is geschikt voor veranderende bedrijfs behoeften. In deze fase moet u beslissen of u de monolithische-toepassing wilt splitsen in Services of innoveren. Een klassiek voor beeld is dit wanneer een Data Base die u als werk stroom wachtrij gebruikt, een verwerkings knelpunt wordt. Naarmate het aantal werk stroom aanvragen toeneemt, moet het werk worden gedistribueerd voor schalen. Neem dat bepaald deel van de toepassing die niet wordt geschaald, of dat deze vaker moet worden bijgewerkt, en splits deze als een micro service en innovatie.

**Toepassingen transformeren in micro Services**  
In deze fase is uw toepassing volledig samengesteld uit (of onderverdeeld in) micro Services. Om dit punt te bereiken, hebt u de micro Services-reis gemaakt. U kunt hier beginnen, maar dit doen zonder een micro services-platform om u te helpen een belang rijke investering te vereisen.

### <a name="are-microservices-right-for-my-application"></a>Zijn micro Services geschikt voor mijn toepassing?

Wel. Bij micro soft, naarmate er meer teams zijn begonnen met het bouwen van de cloud om zakelijke redenen, hebben veel van hen de voor delen van het gebruik van een micro service-achtige benadering gerealiseerd. Bing heeft bijvoorbeeld micro Services voor jaren gebruikt. Voor andere teams was de micro Services-benadering nieuw. Teams hebben geconstateerd dat er sprake is van harde problemen om te voor komen dat ze worden opgelost buiten hun kern gebied van sterkte. Daarom Service Fabric de tractie als technologie voor het bouwen van services.

De doel stelling van Service Fabric is het verminderen van de complexiteit van het bouwen van micro service-toepassingen, zodat u niet zo veel kost bare reontwerps hoeft te door lopen. Begin klein, schaal indien nodig, veraf van services, Voeg nieuwe toe en ontwikkeling met klant gebruik. We weten ook dat er nog vele andere problemen moeten worden opgelost om micro Services voor de meeste ontwikkel aars meer te kunnen benutten. Containers en het actor-programmeer model zijn voor beelden van kleine stappen in die richting. Het is belang rijk dat er meer innovaties worden gebruikt om een micro Services-benadering eenvoudiger te maken.

## <a name="next-steps"></a>Volgende stappen

* [Micro Services: een toepassings revolutie die wordt aangedreven door de Cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)
* [Azure Architecture Center: Micro Services op Azure bouwen](/azure/architecture/microservices/)
* [Aanbevolen procedures voor Azure Service Fabric-toepassingen en clusters](./service-fabric-best-practices-security.md)
* [Overzicht van Service Fabric terminologie](service-fabric-technical-overview.md)

[Image1]: media/service-fabric-overview-microservices/monolithic-vs-micro.png
[Image2]: media/service-fabric-overview-microservices/statemonolithic-vs-micro.png
[Image3]: media/service-fabric-overview-microservices/microservices-migration.png