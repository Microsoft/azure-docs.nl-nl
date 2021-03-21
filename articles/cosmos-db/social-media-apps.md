---
title: 'Azure Cosmos DB ontwerp patroon: sociale media-apps'
description: Meer informatie over een ontwerp patroon voor sociale netwerken door gebruik te maken van de opslag flexibiliteit van Azure Cosmos DB en andere Azure-Services.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 329c4b40f11b36de80581d4a1396813bc8de5c73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010313"
---
# <a name="going-social-with-azure-cosmos-db"></a>Sociaal met Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

De levens duur van een zeer onderling verbonden samenleving betekent dat u op een bepaald moment deel uitmaakt van een **sociaal netwerk**. U gebruikt sociale netwerken om in contact te blijven met vrienden, collega's, familie of soms om uw passie te delen met mensen met een gemeen schappelijke interesses.

Als engineers of ontwikkel aars hebt u mogelijk gevraagd hoe deze netwerken uw gegevens opslaan en verbinden. Het is ook mogelijk dat u hebt gewerkt om een nieuw sociaal netwerk te maken of te ontwikkelen voor een specifieke niche-markt. Wanneer de belang rijke vraag zich voordoet: hoe worden al deze gegevens opgeslagen?

Stel dat u een nieuw en glanzend sociaal netwerk wilt maken waar uw gebruikers artikelen kunnen posten met gerelateerde media, zoals afbeeldingen, Video's of zelfs muziek. Gebruikers kunnen commentaar geven op berichten en punten opgeven voor classificaties. Er is een feed van berichten die gebruikers zien en gebruiken op de landings pagina van de hoofd website. Deze methode klinkt in eerste instantie niet complex, maar u kunt het echter het beste stoppen. (U kunt andere feeds van gebruikers die worden beïnvloed door relaties, dieper in het buiten het doel van dit artikel.)

Hoe slaat u deze gegevens op en waar?

Mogelijk hebt u ervaring met SQL-data bases of hebt u een begrip van [relationele gegevens modellering](https://en.wikipedia.org/wiki/Relational_model). U kunt op de volgende manier beginnen met het tekenen van iets:

:::image type="content" source="./media/social-media-apps/social-media-apps-sql.png" alt-text="Diagram dat een relatief relationeel model illustreert" border="false":::

Een perfecte genormaliseerde en mooie gegevens structuur... dat wordt niet geschaald.

Ik vind het niet mis, ik heb al mijn leven met SQL-data bases gewerkt. Ze zijn geweldig, maar net als elk patroon, oefen en software platform is het niet perfect voor elk scenario.

Waarom is SQL de beste keuze in dit scenario? Laten we eens kijken naar de structuur van één bericht. Als ik het bericht in een website of toepassing wil weer geven, moet ik dan een query uitvoeren met... door acht tabellen (!) samen te voegen om één post weer te geven. U kunt nu een stroom maken van berichten die dynamisch worden geladen en op het scherm worden weer gegeven, en u ziet misschien wat er gebeurt.

U kunt een enorm SQL-exemplaar met voldoende kracht gebruiken voor het oplossen van duizenden query's met veel samen voegingen om uw inhoud te leveren. Maar waarom zou u, wanneer er een eenvoudigere oplossing bestaat?

## <a name="the-nosql-road"></a>De NoSQL-weg

In dit artikel wordt u begeleid bij het model leren van de gegevens van uw sociale platform met de NoSQL-data base van Azure [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) kosten effectief. Ook wordt uitgelegd hoe u andere Azure Cosmos DB-functies gebruikt, zoals de [Gremlin-API](../cosmos-db/graph-introduction.md). Met behulp van een [NoSQL](https://en.wikipedia.org/wiki/NoSQL) -benadering, het opslaan van gegevens, in JSON-indeling en het Toep assen van [denormalisatie](https://en.wikipedia.org/wiki/Denormalization), kan de voorheen gecompliceerde post worden omgezet in één [document](https://en.wikipedia.org/wiki/Document-oriented_database):

```json
{
    "id":"ew12-res2-234e-544f",
    "title":"post title",
    "date":"2016-01-01",
    "body":"this is an awesome post stored on NoSQL",
    "createdBy":User,
    "images":["https://myfirstimage.png","https://mysecondimage.png"],
    "videos":[
        {"url":"https://myfirstvideo.mp4", "title":"The first video"},
        {"url":"https://mysecondvideo.mp4", "title":"The second video"}
    ],
    "audios":[
        {"url":"https://myfirstaudio.mp3", "title":"The first audio"},
        {"url":"https://mysecondaudio.mp3", "title":"The second audio"}
    ]
}
```

En kan worden bevraagd met één query en zonder samen voegingen. Deze query is veel eenvoudig en eenvoudig, en voor het budget is er minder bronnen nodig om een beter resultaat te krijgen.

Azure Cosmos DB zorgt ervoor dat alle eigenschappen worden geïndexeerd met de automatische indexering. De automatische indexering kan zelfs worden [aangepast](index-policy.md). Met de aanpak met schema vrije kunt u documenten opslaan met verschillende en dynamische structuren. Misschien wilt u dat er in de toekomst een lijst met categorieën of Hashtags hoort die aan hen zijn gekoppeld? Cosmos DB worden de nieuwe documenten verwerkt met de toegevoegde kenmerken zonder extra werk dat door ons wordt vereist.

Opmerkingen bij een bericht kunnen worden behandeld als andere berichten met een bovenliggende eigenschap. (Deze procedure vereenvoudigt de object toewijzing.)

```json
{
    "id":"1234-asd3-54ts-199a",
    "title":"Awesome post!",
    "date":"2016-01-02",
    "createdBy":User2,
    "parent":"ew12-res2-234e-544f"
}

{
    "id":"asd2-fee4-23gc-jh67",
    "title":"Ditto!",
    "date":"2016-01-03",
    "createdBy":User3,
    "parent":"ew12-res2-234e-544f"
}
```

En alle sociale interacties kunnen worden opgeslagen op een afzonderlijk object als tellers:

```json
{
    "id":"dfe3-thf5-232s-dse4",
    "post":"ew12-res2-234e-544f",
    "comments":2,
    "likes":10,
    "points":200
}
```

Het maken van feeds is slechts een kwestie van het maken van documenten die een lijst met post-Id's met een opgegeven relevantie volgorde kunnen bevatten:

```json
[
    {"relevance":9, "post":"ew12-res2-234e-544f"},
    {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
    {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
]
```

U kunt een ' laatste ' stream hebben met berichten die zijn besteld op aanmaak datum. Of u kunt een ' beste ' stroom hebben met deze berichten die in de afgelopen 24 uur meer leuk zijn. U kunt zelfs een aangepaste stroom implementeren voor elke gebruiker op basis van logica als volgers en interesses. Dit is nog steeds een lijst met berichten. Het is een kwestie van het bouwen van deze lijsten, maar de Lees prestaties blijven onbelemmerd. Zodra u een van deze lijsten hebt aangeschaft, geeft u één query op om Cosmos DB met behulp van het [sleutel woord in](sql-query-keywords.md#in) om pagina's van berichten tegelijk op te halen.

De feed-streams kunnen worden gemaakt met behulp van de achtergrond processen van [Azure-app Services](https://azure.microsoft.com/services/app-service/) : [webjobs](../app-service/webjobs-create.md). Zodra een bericht is gemaakt, kan de achtergrond verwerking worden geactiveerd met behulp van [Azure Storage](https://azure.microsoft.com/services/storage/) - [wacht rijen](../storage/queues/storage-dotnet-how-to-use-queues.md) en webjobs die worden geactiveerd met de [Azure webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki), waarbij de bericht doorgifte binnen streams op basis van uw eigen aangepaste logica wordt geïmplementeerd.

Punten en leuk vindt plaats op een bericht dat op dezelfde manier kan worden verwerkt met behulp van dezelfde techniek om een uiteindelijk consistente omgeving te maken.

Volgers zijn trickier. Cosmos DB heeft een maximale document grootte en het lezen/schrijven van grote documenten kan invloed hebben op de schaal baarheid van uw toepassing. U kunt bijvoorbeeld nadenken over het opslaan van volgers als een document met deze structuur:

```json
{
    "id":"234d-sd23-rrf2-552d",
    "followersOf": "dse4-qwe2-ert4-aad2",
    "followers":[
        "ewr5-232d-tyrg-iuo2",
        "qejh-2345-sdf1-ytg5",
        //...
        "uie0-4tyg-3456-rwjh"
    ]
}
```

Deze structuur kan worden gebruikt voor een gebruiker met een paar duizenden volgers. Als sommige beroemdheden worden toegevoegd aan de rang schikkingen, leidt deze methode echter tot een grote document grootte en kan het uiteindelijk de grootte van het document aanpassen.

U kunt dit probleem oplossen door een gemengde benadering te gebruiken. Als onderdeel van het document met de gebruikers statistieken kunt u het aantal volgers opslaan:

```json
{
    "id":"234d-sd23-rrf2-552d",
    "user": "dse4-qwe2-ert4-aad2",
    "followers":55230,
    "totalPosts":452,
    "totalPoints":11342
}
```

U kunt de werkelijke grafiek van volgers opslaan met behulp van Azure Cosmos DB [GREMLIN API](../cosmos-db/graph-introduction.md) om [hoek punten](http://mathworld.wolfram.com/GraphVertex.html) te maken voor elke gebruiker en [randen](http://mathworld.wolfram.com/GraphEdge.html) die de relaties ' A-follow-B ' behouden. Met de Gremlin-API kunt u de volgers van een bepaalde gebruiker ophalen en complexere query's maken om gebruikers gemeen schappelijk te Voorst Ellen. Als u toevoegt aan de grafiek de inhouds categorieën die mensen leuk of geniet, kunt u een patroon ervaring met detectie van slimme inhoud maken, inhoud Voorst Ellen die de personen zijn die u ondervindt, of mensen zoeken die mogelijk veel worden gebruikt met.

Het document met gebruikers statistieken kan nog steeds worden gebruikt om kaarten in de voor beelden van de gebruikers interface of het snelle profiel te maken.

## <a name="the-ladder-pattern-and-data-duplication"></a>Het patroon van de ladder en gegevens duplicatie

Zoals u mogelijk in het JSON-document hebt gezien dat verwijst naar een bericht, zijn er veel exemplaren van een gebruiker. En u hebt het recht geraden, maar deze dubbele items betekenen dat de informatie die een gebruiker beschrijft, op basis van deze denormalisatie, op meer dan één plek aanwezig kan zijn.

Om sneller query's mogelijk te maken, maakt u gegevens dupliceren. Het probleem met dit neven effect is dat als bij bepaalde actie, de gegevens van een gebruiker worden gewijzigd, u alle activiteiten moet vinden die de gebruiker ooit heeft gedaan en deze allemaal bij te werken. Klinkt het niet praktisch, direct?

U gaat dit oplossen door de sleutel kenmerken te identificeren van een gebruiker die u voor elke activiteit in uw toepassing hebt weer gegeven. Als u een bericht in uw toepassing visueel weergeeft en alleen de naam en afbeelding van de maker weergeeft, waarom slaat u alle gegevens van de gebruiker op in het kenmerk "createdBy"? Als voor elke opmerking alleen de afbeelding van de gebruiker wordt weer gegeven, hebt u niet de rest van de gebruikers gegevens nodig. Dit is de plek waar ik het ' ladder-patroon ' aanroept.

Laten we de gebruikers gegevens als voor beeld aannemen:

```json
{
    "id":"dse4-qwe2-ert4-aad2",
    "name":"John",
    "surname":"Doe",
    "address":"742 Evergreen Terrace",
    "birthday":"1983-05-07",
    "email":"john@doe.com",
    "twitterHandle":"\@john",
    "username":"johndoe",
    "password":"some_encrypted_phrase",
    "totalPoints":100,
    "totalPosts":24
}
```

Door deze informatie te bekijken, kunt u snel detecteren wat essentiële informatie is en wat niet het geval is, dus het maken van een ' ladder ':

:::image type="content" source="./media/social-media-apps/social-media-apps-ladder.png" alt-text="Diagram van een ladder patroon" border="false":::

De kleinste stap wordt een UserChunk genoemd, de minimale hoeveelheid informatie die een gebruiker identificeert en wordt gebruikt voor het dupliceren van gegevens. Door de gedupliceerde gegevens grootte alleen te beperken tot de gegevens die u wilt weer geven, vermindert u de mogelijkheid van massale updates.

De tweede stap wordt de gebruiker genoemd. Dit zijn de volledige gegevens die worden gebruikt voor de meeste prestatie afhankelijke query's op Cosmos DB, het meest toegankelijke en kritieke. Het bevat de informatie die wordt vertegenwoordigd door een UserChunk.

De grootste is de uitgebreide gebruiker. Het bevat de essentiële gebruikers gegevens en andere gegevens die niet snel hoeven te worden gelezen of die een uiteindelijk gebruik heeft, zoals het aanmeldings proces. Deze gegevens kunnen worden opgeslagen buiten Cosmos DB, in Azure SQL Database of Azure Storage tabellen.

Waarom zou u de gebruiker splitsen en deze informatie zelfs opslaan op verschillende plaatsen? Vanwege het prestatie punt van de weer gave zijn de costlier de query's groter. Bewaar documenten dun, met de juiste informatie om al uw prestatie afhankelijke query's voor uw sociale netwerk uit te voeren. Sla de overige aanvullende informatie op voor scenario's die het volledige profiel beslaan, aanmeldingen en gegevens analyse voor gebruiks analyses en Big Data-initiatieven. Het is niet belang rijk dat het verzamelen van gegevens voor gegevens analyse langzamer is, omdat deze wordt uitgevoerd op Azure SQL Database. U hebt al het belang om uw gebruikers een snelle en compacte ervaring te bieden. Een gebruiker die is opgeslagen op Cosmos DB ziet er als volgt uit:

```json
{
    "id":"dse4-qwe2-ert4-aad2",
    "name":"John",
    "surname":"Doe",
    "username":"johndoe"
    "email":"john@doe.com",
    "twitterHandle":"\@john"
}
```

En een bericht zou er als volgt uitzien:

```json
{
    "id":"1234-asd3-54ts-199a",
    "title":"Awesome post!",
    "date":"2016-01-02",
    "createdBy":{
        "id":"dse4-qwe2-ert4-aad2",
        "username":"johndoe"
    }
}
```

Wanneer een bewerking zich voordoet wanneer een segment kenmerk wordt beïnvloed, kunt u de betrokken documenten eenvoudig vinden. Gebruik alleen query's die verwijzen naar de geïndexeerde kenmerken, zoals `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"` en werk vervolgens de segmenten bij.

## <a name="the-search-box"></a>Het zoekvak

Gebruikers genereren, gelukkig, veel inhoud. En u moet de mogelijkheid bieden om inhoud te zoeken en te zoeken die mogelijk niet direct in de inhouds stromen staat, misschien omdat u de makers niet volgt of als u gewoon de oude post probeert te vinden die u zes maanden geleden hebt gedaan.

Omdat u Azure Cosmos DB gebruikt, kunt u binnen enkele minuten eenvoudig een zoek machine implementeren met behulp van [Azure Cognitive Search](https://azure.microsoft.com/services/search/) zonder dat u code hoeft te typen, behalve het zoek proces en de gebruikers interface.

Waarom is dit proces zo gemakkelijk?

Azure Cognitive Search implementeert wat ze [Indexeer](/rest/api/searchservice/Indexer-operations)functies aanroepen, achtergrond processen die aansluiten bij uw gegevensopslag plaatsen en automagiceel toevoegen, bijwerken of verwijderen van objecten in de indexen. Ze ondersteunen een [Azure SQL database Indexeer](/archive/blogs/kaevans/indexing-azure-sql-database-with-azure-search)functies, [indexers van Azure blobs](../search/search-howto-indexing-azure-blob-storage.md) en gelukkig, [Azure Cosmos DB Indexeer functies](../search/search-howto-index-cosmosdb.md). De overgang van informatie van Cosmos DB naar Azure Cognitive Search is eenvoudig. Beide technologieën slaan gegevens in JSON-indeling, dus u hoeft alleen maar de [index te maken](../search/search-what-is-an-index.md) en de kenmerken te koppelen uit uw documenten die u wilt indexeren. Dat is alles. Afhankelijk van de grootte van uw gegevens, is al uw inhoud beschikbaar om binnen enkele minuten te worden doorzocht door de beste Search-as-a-service-oplossing in de Cloud infrastructuur.

Ga naar de [Hitchhiker-gids](/archive/blogs/mvpawardprogram/a-hitchhikers-guide-to-search)voor meer informatie over Azure Cognitive Search.

## <a name="the-underlying-knowledge"></a>De onderliggende kennis

Nadat u al deze inhoud hebt opgeslagen die elke dag groeit en groeit, kunt u denken: wat kan ik doen met al deze gegevens stroom van mijn gebruikers?

Het antwoord is eenvoudig: plaats het om te werken en ontdek het.

Maar wat kunt u zien? Enkele eenvoudige voor beelden zijn [sentiment analyse](https://en.wikipedia.org/wiki/Sentiment_analysis), aanbevelingen voor inhoud op basis van de voor keuren van een gebruiker of zelfs een geautomatiseerde content moderator die ervoor zorgt dat de inhoud die wordt gepubliceerd door uw sociale netwerk, veilig is voor de familie.

Nu u bent gehookd, denkt u waarschijnlijk dat u een aantal PhD in math Science nodig hebt om deze patronen en informatie uit eenvoudige data bases en bestanden uit te pakken, maar dat is niet juist.

[Azure machine learning](https://azure.microsoft.com/services/machine-learning/), een deel van de [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), is een volledig beheerde Cloud service waarmee u werk stromen kunt maken met behulp van algoritmen in een eenvoudige interface voor slepen en neerzetten, uw eigen algoritmen codeert in [R](https://en.wikipedia.org/wiki/R_\(programming_language\))of een aantal van de al ontwikkelde en kant-en-klare api's gebruikt, zoals: [Text Analytics](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), content moderator of [aanbevelingen](https://gallery.azure.ai/Solution/Recommendations-Solution).

Als u een van deze Machine Learning scenario's wilt behaalt, kunt u [Azure data Lake](https://azure.microsoft.com/services/data-lake-store/) gebruiken om de gegevens uit verschillende bronnen op te nemen. U kunt ook [u-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) gebruiken om de informatie te verwerken en een uitvoer te genereren die kan worden verwerkt door Azure machine learning.

Een andere beschik bare optie is het gebruik van [Azure Cognitive Services](https://www.microsoft.com/cognitive-services) om de inhoud van uw gebruikers te analyseren. het is niet alleen mogelijk om ze beter te begrijpen (door te analyseren wat ze schrijven met [Text Analytics-API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), maar u kunt ook ongewenste of rijpe inhoud detecteren en dienovereenkomstig op [Computer Vision-API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api)handelen. Cognitive Services bevat veel out-of-the-box-oplossingen waarvoor geen enkele soort Machine Learning kennis nodig is.

## <a name="a-planet-scale-social-experience"></a>Een planeet met sociale ervaring

Er is een laatste, maar niet minst belang rijk artikel dat moet worden geadresseerd: **schaal baarheid**. Wanneer u een architectuur ontwerpt, moet elk onderdeel zelf worden geschaald. U zult uiteindelijk meer gegevens moeten verwerken of u wilt een grotere geografische dekking hebben. Gelukkig is het uitvoeren van beide taken een kant-en- **klare ervaring** met Cosmos db.

Cosmos DB biedt ondersteuning voor dynamische partitionering met een out-of-the-box. Er worden automatisch partities gemaakt op basis van een bepaalde **partitie sleutel**, die wordt gedefinieerd als een kenmerk in uw documenten. Het definiëren van de juiste partitie sleutel moet tijdens de ontwerp fase worden uitgevoerd. Zie [partitioneren in azure Cosmos DB](partitioning-overview.md)voor meer informatie.

Voor een sociale ervaring moet u de strategie voor partitioneren uitlijnen met de manier waarop u een query uitvoert en schrijft. (Lees bewerkingen binnen dezelfde partitie zijn bijvoorbeeld wenselijk en Vermijd ' HOTS Pots ' door schrijf bewerkingen op meerdere partities te spreiden.) Enkele opties zijn: partities op basis van een tijdelijke sleutel (dag/maand/week), per inhouds categorie, per geografische regio of per gebruiker. Het is allemaal echt afhankelijk van hoe u de gegevens opvraagt en hoe de gegevens in uw sociale ervaring worden weer gegeven.

Cosmos DB worden uw query's (inclusief [aggregaties](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) op transparante wijze uitgevoerd voor al uw partities. u hoeft dus geen logica toe te voegen wanneer uw gegevens groeien.

Met tijd groeit u uiteindelijk in verkeer en neemt het Resource verbruik (gemeten in [RUs](request-units.md)of aanvraag eenheden) toe. U leest en schrijft vaker als uw gebruikers database groeit. De gebruikers database begint met het maken en lezen van meer inhoud. De mogelijkheid om **uw door voer te schalen** is daarom essentieel. Het is eenvoudig om uw RUs te verg Roten. U kunt dit doen met een paar klikken op de Azure Portal of door [opdrachten uit te geven via de API](/rest/api/cosmos-db/replace-an-offer).

:::image type="content" source="./media/social-media-apps/social-media-apps-scaling.png" alt-text="Omhoog schalen en definiëren van een partitie sleutel":::

Wat gebeurt er als de dingen beter blijven? Stel dat gebruikers uit een andere regio, land of continenten uw platform zien en gaan gebruiken. Wat een geweldig!

Maar wacht eens even! U hebt binnenkort de ervaring met uw platform niet optimaal. Ze zijn zo ver weg in uw operationele regio dat de latentie verschrikkelijke is. U wilt ze natuurlijk niet afsluiten. Als er alleen een eenvoudige manier is om **uw wereld wijde bereik uit te breiden**? Er is nog!

Met Cosmos DB kunt u [uw gegevens wereld wijd](../cosmos-db/tutorial-global-distribution-sql-api.md) en transparant repliceren met een paar muis klikken en automatisch een selectie maken tussen de beschik bare regio's van uw [client code](../cosmos-db/tutorial-global-distribution-sql-api.md). Dit proces houdt ook in dat u [meerdere failover-regio's](high-availability.md)kunt hebben.

Wanneer u uw gegevens wereld wijd repliceert, moet u ervoor zorgen dat uw clients hiervan gebruik kunnen maken. Als u een web-frontend gebruikt of Api's van mobiele clients opent, kunt u [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) implementeren en uw Azure app service op alle gewenste regio's klonen met behulp van een prestatie configuratie ter ondersteuning van uw uitgebreide wereld wijde dekking. Wanneer uw clients toegang hebben tot uw frontend of Api's, worden ze doorgestuurd naar de dichtstbijzijnde App Service, die op zijn beurt verbinding maakt met de lokale Cosmos DB replica.

:::image type="content" source="./media/social-media-apps/social-media-apps-global-replicate.png" alt-text="Wereld wijde dekking toevoegen aan uw sociale platform" border="false":::

## <a name="conclusion"></a>Conclusie

In dit artikel wordt een licht niveau in de alternatieven voor het maken van sociale netwerken volledig op Azure uitgeschreven met goedkope Services. het levert resultaten op door het gebruik van een opslag oplossing met meerdere lagen en gegevens distributie genaamd "ladder" te stimuleren.

:::image type="content" source="./media/social-media-apps/social-media-apps-azure-solution.png" alt-text="Diagram van interactie tussen Azure-Services voor sociale netwerken" border="false":::

De waarheid is dat er geen zilver opsommings teken is voor dit soort scenario's. Het is de synergie die is gemaakt door de combi natie van fantastische services waarmee we fantastische ervaringen kunnen bouwen: de snelheid en vrijheid van Azure Cosmos DB om een goede sociale toepassing te bieden, de intelligentie achter een zoek oplossing voor de eerste klasse zoals Azure Cognitive Search, de flexibiliteit van Azure-app Services om niet-neutraal toepassingen te hosten, maar krachtige achtergrond processen en de uitbreid bare Azure Storage en Azure SQL Database voor het opslaan van enorme hoeveel heden gegevens en de analyse kracht van Azure machine Leer hoe u kennis en intelligentie maakt waarmee u feedback kunt geven over uw processen en ons de mogelijkheid biedt de juiste inhoud aan de juiste gebruikers te leveren.

## <a name="next-steps"></a>Volgende stappen

Zie [algemene Cosmos DB use cases](use-cases.md)(Engelstalig) voor meer informatie over gebruiks voorbeelden voor Cosmos db.