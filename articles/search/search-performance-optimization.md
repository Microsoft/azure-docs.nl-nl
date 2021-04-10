---
title: Schalen voor prestaties
titleSuffix: Azure Cognitive Search
description: Leer technieken en aanbevolen procedures voor het afstemmen van de prestaties van Azure Cognitive Search en het configureren van de optimale schaal.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.custom: references_regions
ms.openlocfilehash: 60371888dbc4f0cbc33f1ad1b2a685dbb071c01a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670710"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Schaal voor prestaties op Azure Cognitive Search

In dit artikel worden aanbevolen procedures beschreven voor geavanceerde scenario's met geavanceerde vereisten voor schaal baarheid en beschik baarheid.

## <a name="start-with-baseline-numbers"></a>Beginnen met basislijn nummers

Voordat u een grotere implementatie inspanning inneemt, moet u weten hoe een typische query belasting eruitziet. De volgende richt lijnen kunnen u helpen bij het aankomen van basislijn query nummers.

1. Kies een doel latentie (of maximale tijds duur) waarmee een typische zoek opdracht moet worden voltooid.

1. Maak en test een echte werk belasting op uw zoek service met een realistische gegevensset om deze latentie tarieven te meten.

1. Begin met een laag aantal query's per seconde (QPS) en verhoog vervolgens geleidelijk het aantal dat in de test wordt uitgevoerd totdat de query latentie onder het vooraf gedefinieerde doel daalt. Dit is een belang rijk referentie punt om u te helpen bij het plannen van de schaal als uw toepassing groeit in gebruik.

1. Gebruik waar mogelijk HTTP-verbindingen. Als u de Azure Cognitive Search .NET SDK gebruikt, betekent dit dat u een instantie of [SearchClient](/dotnet/api/azure.search.documents.searchclient) -exemplaar opnieuw moet gebruiken. Als u de rest API gebruikt, moet u één httpclient maakt opnieuw gebruiken.

1. U kan de query op aanvragen van query's variëren zodat de zoek actie plaatsvindt in verschillende onderdelen van de index. Variatie is belang rijk omdat als u dezelfde Zoek opdrachten doorlopend uitvoert, het opslaan van gegevens in de cache wordt gestart, waardoor de prestaties kunnen worden verbeterd.

1. U kunt de structuur van query-aanvragen variëren zodat u verschillende soorten query's krijgt. Niet alle zoek query's moeten op hetzelfde niveau worden uitgevoerd. Bijvoorbeeld: een zoek-of zoek voorstel voor documenten is doorgaans sneller dan een query met een groot aantal facetten en filters. De test samenstelling moet verschillende query's bevatten, in ongeveer dezelfde verhouding als bij de productie.  

Tijdens het maken van deze test werkbelastingen zijn er enkele kenmerken van Azure Cognitive Search die u moet onthouden:

+ Het is mogelijk om uw service te overbelasten door te veel zoek query's tegelijk te pushen. Als dit gebeurt, worden HTTP 503-antwoord codes weer geven. Als u een 503 tijdens het testen wilt voor komen, kunt u beginnen met verschillende bereiken van zoek opdrachten om de verschillen in latentie te zien wanneer u meer zoek aanvragen toevoegt.

+ Met Azure Cognitive Search worden index taken niet op de achtergrond uitgevoerd. Als uw service tegelijkertijd query's uitvoert en werk belastingen gelijktijdig indexeert, moet u rekening houden met het introduceren van index taken in uw query tests of door opties te verkennen voor het uitvoeren van index taken tijdens de piek uren.

> [!Tip]
> U kunt een realistische query belasting simuleren met behulp van hulpprogram ma's voor het testen van de belasting. Probeer [tests met Azure DevOps te laden](/azure/devops/test/load-test/get-started-simple-cloud-load-test) of gebruik een van deze [alternatieven](/azure/devops/test/load-test/overview#alternatives).

## <a name="scale-for-high-query-volume"></a>Schaal voor hoog query volume

Een service wordt overbelast wanneer query's te lang duren of wanneer de service aanvragen verwijdert. Als dit het geval is, kunt u het probleem op een van de volgende twee manieren aanpakken:

+ **Replica's toevoegen**  

  Elke replica is een kopie van uw gegevens, zodat de service aanvragen voor meerdere exemplaren kan verdelen.  Alle taak verdeling en replicatie van gegevens worden beheerd door Azure Cognitive Search en u kunt op elk gewenst moment het aantal replica's aanpassen dat voor uw service wordt toegewezen. U kunt Maxi maal 12 replica's toewijzen in een Standard-zoek service en 3 replica's in een Basic Search-service. Replica's kunnen worden aangepast op basis van de [Azure Portal](search-create-service-portal.md) of [Power shell](search-manage-powershell.md).

+ **Een nieuwe service in een hogere laag maken**  

  Azure Cognitive Search maakt deel uit [van een aantal lagen](https://azure.microsoft.com/pricing/details/search/) en elk abonnement biedt verschillende prestatie niveaus. In sommige gevallen hebt u mogelijk zoveel query's die de laag die u aanmeldt, niet voldoende opleverbaar bieden, zelfs wanneer er replica's worden benut. In dit geval kunt u overwegen om over te stappen op een hoger niveau, zoals de standaard S3-laag, ontworpen voor scenario's met een groot aantal documenten en extreem hoge query werkbelastingen.

## <a name="scale-for-slow-individual-queries"></a>Schaal voor langzame afzonderlijke query's

Een andere reden voor hoge latentie is dat een enkele query te lang duurt om te worden voltooid. In dit geval is het toevoegen van replica's geen uitkomst. Twee mogelijke opties die kunnen helpen bij het volgende:

+ **Partities verhogen**

  Een partitie splitst gegevens over extra computer bronnen. Met twee partities worden gegevens in de helft gesplitst, een derde partitie gesplitst in derde, enzovoort. Eén positieve neven effect is dat tragere query's soms sneller worden uitgevoerd als gevolg van parallelle computing. We hebben op parallel Lise ring genoteerd dat er weinig selectiviteit-query's zijn, zoals query's die overeenkomen met veel documenten, of facetten die aantallen bieden over een groot aantal documenten. Aangezien er een belang rijke berekening is vereist om de relevancy van de documenten te beoordelen, of om het aantal documenten te tellen, kunnen er met extra partities sneller query's worden uitgevoerd.  
   
  Er kunnen Maxi maal 12 partities in de Standard Search-service en 1 partitie in de Basic Search-service zijn. Partities kunnen worden aangepast op basis van de [Azure Portal](search-create-service-portal.md) of [Power shell](search-manage-powershell.md).

+ **Velden met hoge kardinaliteit beperken**

  Een hoog veld voor kardinaliteit bestaat uit een facetbaar of filterbaar veld met een groot aantal unieke waarden. als gevolg hiervan worden aanzienlijke bronnen verbruikt bij het berekenen van resultaten. Zo kunt u bijvoorbeeld een product-ID of beschrijvings veld instellen als facetable/filterbaar als hoge kardinaliteit, omdat de meeste waarden van document naar document uniek zijn. Beperk waar mogelijk het aantal velden met hoge kardinaliteit.

+ **Zoek niveau verhogen**  

  Het verplaatsen naar een hogere Azure Cognitive Search-laag kan een andere manier zijn om de prestaties van trage query's te verbeteren. Elke hogere laag biedt snellere Cpu's en meer geheugen, beide met een positieve invloed op de prestaties van query's.

## <a name="scale-for-availability"></a>Schaal voor Beschik baarheid

Replica's helpen niet alleen de latentie van query's te verminderen, maar kunnen ook hoge Beschik baarheid toestaan. Met één replica moet u een periodieke downtime verwachten omdat de server opnieuw wordt opgestart na software-updates of voor andere onderhouds gebeurtenissen die optreden. Daarom is het belang rijk om te overwegen of uw toepassing hoge Beschik baarheid van zoek opdrachten (query's) vereist en schrijf bewerkingen (indexerings gebeurtenissen). Azure Cognitive Search biedt SLA-opties voor alle betaalde zoek aanbiedingen met de volgende kenmerken:

+ Twee replica's voor hoge Beschik baarheid van alleen-lezen workloads (query's)

+ Drie of meer replica's voor hoge Beschik baarheid van werk belastingen voor lezen/schrijven (query's en indexering)

Ga naar de [Azure Cognitive Search Service Level Agreement](https://azure.microsoft.com/support/legal/sla/search/v1_0/)voor meer informatie hierover.

Aangezien replica's kopieën zijn van uw gegevens, kunnen met meerdere replica's Azure Cognitive Search de computer opnieuw opstarten en onderhoud uitvoeren voor één replica, terwijl de uitvoering van de query wordt voortgezet op andere replica's. Als u replica's hebt verwijderd, kunt u de prestaties van de query afnemen, ervan uitgaande dat deze replica's een minder gebruikte bron zijn.

<a name="availability-zones"></a>

### <a name="availability-zones"></a>Beschikbaarheidszones

[Beschikbaarheidszones](../availability-zones/az-overview.md) de data centers van een regio in verschillende fysieke locatie groepen te delen om hoge Beschik baarheid te bieden binnen dezelfde regio. Voor Cognitive Search zijn afzonderlijke replica's de eenheden voor zone toewijzing. Een zoek service wordt binnen één regio uitgevoerd; de replica's worden uitgevoerd in verschillende zones.

U kunt Beschikbaarheidszones met Azure Cognitive Search gebruiken door twee of meer replica's aan uw zoek service toe te voegen. Elke replica wordt in een andere beschikbaarheids zone in de regio geplaatst. Als u meer replica's hebt dan Beschikbaarheidszones, worden de replica's zo gelijkmatig mogelijk verdeeld over Beschikbaarheidszones.

Azure Cognitive Search ondersteunt momenteel Beschikbaarheidszones voor de Standard-laag of hogere zoek services die zijn gemaakt in een van de volgende regio's:

+ Australië-oost (gemaakt op 30 januari 2021 of hoger)
+ Canada-centraal (gemaakt op 30 januari 2021 of hoger)
+ VS-centraal (gemaakt op 4 december 2020 of hoger)
+ VS-Oost (gemaakt op 27 januari 2021 of hoger)
+ VS-Oost 2 (gemaakt op 30 januari 2021 of hoger)
+ Frankrijk-centraal (gemaakt op 23 oktober 2020 of hoger)
+ Japan-Oost (gemaakt op 30 januari 2021 of hoger)
+ Europa-noord (gemaakt op 28 januari 2021 of hoger)
+ Zuid-Azië-oost (gemaakt op 31 januari 2021 of hoger)
+ UK-zuid (gemaakt op 30 januari 2021 of hoger)
+ Europa-west (gemaakt op 29 januari 2021 of hoger)
+ VS-West 2 (gemaakt op 30 januari 2021 of hoger)

Beschikbaarheidszones hebben geen invloed op de [Service Level Agreement van Azure Cognitive Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/). U hebt nog drie of meer replica's nodig voor een hoge Beschik baarheid van query's.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Schaal voor geografisch gedistribueerde workloads en geo-redundantie

Voor geografisch gedistribueerde werk belastingen hebben gebruikers die zich ver van het host-data centrum bevinden een hogere latentie snelheid. Een beperking is het inrichten van meerdere zoek services in regio's met dichter nabijheid voor deze gebruikers.

Azure Cognitive Search biedt momenteel geen automatische methode voor het geo-repliceren van Azure Cognitive Search-indexen in regio's, maar er zijn enkele technieken die kunnen worden gebruikt om dit proces eenvoudig te implementeren en te beheren. Deze worden in de volgende paar secties beschreven.

Het doel van een geografisch gedistribueerde set Zoek Services bestaat uit twee of meer indexen die beschikbaar zijn in twee of meer regio's, waarbij een gebruiker wordt doorgestuurd naar de Azure Cognitive Search-service die de laagste latentie levert zoals in dit voor beeld wordt weer gegeven:

   ![Meerdere tabbladen van services per regio][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Gegevens gesynchroniseerd blijven tussen meerdere services

Er zijn twee opties om uw gedistribueerde zoek services synchroon te houden, die bestaan uit het gebruik van de [azure Cognitive Search indexer](search-indexer-overview.md) of de Push-API (ook wel de [Azure Cognitive Search rest API](/rest/api/searchservice/)genoemd).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Indexeer functies gebruiken voor het bijwerken van inhoud op meerdere services

Als u Indexeer functie al gebruikt voor één service, kunt u een tweede Indexeer functie configureren voor een tweede service om hetzelfde gegevens bron object te gebruiken en gegevens uit dezelfde locatie op te halen. Elke service in elke regio heeft een eigen Indexeer functie en een doel index (uw zoek index wordt niet gedeeld, wat betekent dat de gegevens worden gedupliceerd), maar elke Indexeer functie verwijst naar dezelfde gegevens bron.

Hier volgt een globaal visueel element van wat die architectuur eruit zou zien.

   ![Eén gegevens bron met combi Naties van gedistribueerde Indexeer functies en services][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>REST-Api's gebruiken voor het pushen van inhouds updates op meerdere services

Als u de Azure Cognitive Search-REST API gebruikt om [inhoud in uw Azure Cognitive search-index te pushen](/rest/api/searchservice/update-index), kunt u uw verschillende Zoek Services synchroon laten door wijzigingen in alle zoek services te pushen wanneer een update is vereist. Zorg ervoor dat u in uw code cases afhandelt waarbij een update naar één zoek service mislukt, maar slaagt voor andere zoek services.

## <a name="leverage-azure-traffic-manager"></a>Gebruik Azure Traffic Manager

Met [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) kunt u aanvragen naar meerdere geo-locaties routeren die vervolgens door meerdere zoek services worden ondersteund. Een voor deel van het Traffic Manager is dat het Azure Cognitive Search kan testen om er zeker van te zijn dat het beschikbaar is en gebruikers kan omleiden naar alternatieve Zoek Services in het geval van downtime. Daarnaast kunt u, als u zoek aanvragen begeleidt via Azure-websites, in azure Traffic Manager taken verdelen waarbij de website wel of niet Azure Cognitive Search is. Hier volgt een voor beeld van de architectuur die gebruikmaakt van Traffic Manager.

   ![Meerdere tabbladen van services per regio, met centrale Traffic Manager][3]

## <a name="next-steps"></a>Volgende stappen

Zie [service limieten](search-limits-quotas-capacity.md)voor meer informatie over de prijs categorieën en de limieten voor services voor elk van deze. Zie [capaciteit plannen](search-capacity-planning.md) voor meer informatie over combi Naties van partities en replica's.

Bekijk de volgende video voor een bespreking van de prestaties en demonstraties van de technieken die in dit artikel worden besproken:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png