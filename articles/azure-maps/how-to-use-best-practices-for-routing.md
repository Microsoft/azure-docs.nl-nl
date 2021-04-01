---
title: Aanbevolen procedures voor het Azure Maps van Route Service in Microsoft Azure Maps
description: Meer informatie over het routeren van Voer tuigen met behulp van Route Service van Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8174529def5e3924086e49f36c225f07a4da2648
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99051648"
---
# <a name="best-practices-for-azure-maps-route-service"></a>Aanbevolen procedures voor Azure Maps route service

De route richtingen en route matrix-Api's in Azure Maps [route service](/rest/api/maps/route) kunnen worden gebruikt om de geschatte aankomst tijden (ETAs) voor elke aangevraagde route te berekenen. Router-Api's beschouwen factoren als realtime verkeers gegevens en historische verkeers gegevens, zoals de standaard snelheden op de gewenste dag van de week en het tijdstip van de dag. De API's retourneren de kortste of snelste routes die beschikbaar zijn voor meerdere bestemmingen tegelijk in een reeks of in een geoptimaliseerde volgorde, op basis van tijd of afstand. Gebruikers kunnen ook specifieke routes en Details aanvragen voor walkie, fietsers en commerciële Voer tuigen, zoals vracht wagens. In dit artikel delen we de aanbevolen procedures voor het aanroepen van Azure Maps [route service](/rest/api/maps/route)en leert u het volgende:

 * Kiezen tussen de route richtingen Api's en de API voor de matrix routering
 * Historische en voorspelde reistijden aanvragen, op basis van real-time en historische verkeersgegevens
 * Routerings gegevens, zoals tijd en afstand, voor de hele route en elk poot van de route aanvragen
 * Vraag route voor een bedrijfs voertuig, zoals een truck
 * Informatie over verkeer aanvragen via een route, zoals storingen en informatie over de telefoon
 * Een route aanvragen die bestaat uit een of meer stop (waypoints)
 * Optimaliseer een route van een of meer stops om de beste volg orde te verkrijgen om elke Stop (waypoint) te bezoeken.
 * Optimaliseer alternatieve routes met ondersteunende punten. U kunt bijvoorbeeld alternatieve routes aanbieden die een elektriciteits station voor het laden van het Voer tuig door geven.
 * De [route service](/rest/api/maps/route) gebruiken met de Azure Maps Web-SDK

## <a name="prerequisites"></a>Vereisten

1. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel.

Zie de [routerings dekking](routing-coverage.md)voor meer informatie over de dekking van de route service.

In dit artikel wordt gebruikgemaakt van de [postman-app](https://www.postman.com/downloads/) om rest-aanroepen te bouwen, maar u kunt elke API-ontwikkel omgeving kiezen.

## <a name="choose-between-route-directions-and-matrix-routing"></a>Kiezen tussen route richtingen en matrix routering

De route richtingen Api's retour neren instructies, inclusief de reis tijd en de coördinaten voor een traject. Met de route matrix-API kunt u de reis tijd en de afstanden berekenen voor een set routes die zijn gedefinieerd op basis van de bron-en doel locatie. Voor elke bepaalde oorsprong berekent de matrix-API de kosten (reis tijd en afstand) van route ring van die oorsprong naar elke opgegeven bestemming. Met al deze Api's kunt u para meters opgeven, zoals de gewenste vertrek tijd, aankomst tijden en het voertuig type, zoals auto of truck. Ze gebruiken allemaal realtime of voorspellende verkeers gegevens om de meest optimale routes te retour neren.

Overweeg het aanroepen van route richtingen Api's als uw scenario:

* Vraag de kortste of snelste route ring tussen twee of meer bekende locaties aan om nauw keurige aankomst tijden voor uw leverings voertuigen te krijgen.
* Vraag gedetailleerde route richtlijnen aan, met inbegrip van route geometrie, voor het visualiseren van routes op de kaart
* Op basis van een lijst met klant locaties, berekent u de kortst mogelijke route om de locatie van de klant te bezoeken en terug te keren naar de oorsprong. Dit scenario staat bekend als het Traveling Salesman-probleem. U kunt Maxi maal 150 waypoints (gestopt) in één aanvraag door geven.
* Verzend batches van query's naar de batch-API voor route richtingen met slechts één API-aanroep.

Overweeg de API voor de route ring van de matrix aan te roepen als uw scenario:

* Bereken de reis tijd of de afstand tussen een reeks oorsprongen en bestemmingen. U hebt bijvoorbeeld 12 Stuur Programma's en u moet het dichtstbijzijnde beschik bare stuur programma vinden om de voedings middelen van het restaurant op te halen.
* Mogelijke routes sorteren op basis van de werkelijke reis afstand of-tijd. De matrix-API retourneert alleen reis tijden en afstanden voor elke oorsprong en doel combinatie.
* Cluster gegevens op basis van reis tijd of afstanden. Uw bedrijf heeft bijvoorbeeld 50 werk nemers, en vind alle werk nemers die binnen een periode van 20 minuten van uw kantoor wonen.

Hier volgt een vergelijking om enkele mogelijkheden van de route richtingen en matrix-Api's weer te geven:

| Azure Maps-API | Maximum aantal query's in de aanvraag | Gebieden voor komen | Route ring van vracht wagen en elektrisch Voer tuig | Waypoints en Traveling Salesman Optimization | Ondersteunende punten |
| :--------------: |  :--------------: |  :--------------: | :--------------: | :--------------: | :--------------: |
| Routebeschrijving ophalen | 1 | | ✔ | ✔ | |
| Route beschrijving plaatsen | 1 | ✔ | ✔ | ✔ | ✔ |
| Post route beschrijving batch | 700 | | ✔ | ✔ | |
| Post-routematrix | 700 | | ✔ | | |

Voor meer informatie over routerings mogelijkheden voor elektrische auto's raadpleegt u onze zelf studie over het [routeren van elektrische Voer tuigen met behulp van Azure notebooks met python](tutorial-ev-routing.md).

## <a name="request-historic-and-real-time-data"></a>Historische en real-time gegevens aanvragen

De route service gaat standaard ervan uit dat de Traveling modus een auto is en de vertrek tijd nu. Het retourneert route op basis van real-time verkeers omstandigheden, tenzij een aanvraag voor route berekening anders aangeeft. Vaste, afhankelijk van de verkeers beperkingen, zoals links, zijn niet toegestaan tussen 4:00 en 6:00 uur, en worden beschouwd door de routerings engine. Voor wegsluitingen, zoals roadworks, wordt rekening gehouden tenzij u specifiek een route aanvraagt die het huidige live verkeer negeert. Als u het huidige verkeer wilt negeren, stelt `traffic` u `false` in op uw API-aanvraag.

De **travelTimeInSeconds** -waarde van de route berekening bevat de vertraging als gevolg van het verkeer. De gegevens worden gegenereerd door gebruik te maken van de huidige en historische reis tijd, wanneer de vertrek tijd is ingesteld op nu. Als uw vertrek tijd in de toekomst is ingesteld, retour neren de Api's voorspelde reis tijden op basis van historische gegevens.

Als u de para meter **computeTravelTimeFor = all** opneemt in uw aanvraag, hebben het samen vattings element in het antwoord de volgende extra velden, inclusief historische verkeers omstandigheden:

| Element | Beschrijving|
| :--- | :--- |
| noTrafficTravelTimeInSeconds | De geschatte reis tijd die wordt berekend alsof er geen vertragingen zijn op de route wegens verkeers omstandigheden, bijvoorbeeld vanwege congestie |
| historicTrafficTravelTimeInSeconds | Geschatte reis tijd berekend met behulp van tijd afhankelijke historische verkeers gegevens |
| liveTrafficIncidentsTravelTimeInSeconds | Geschatte reis tijd berekend met behulp van real-time snelheids gegevens |

In de volgende secties wordt uitgelegd hoe u met behulp van de Gedemonstreerde para meters aanroepen naar de route-Api's.

### <a name="sample-query"></a>Voorbeeldquery

In het eerste voor beeld hieronder wordt de vertrek tijd ingesteld op de toekomst, op het moment van schrijven.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=51.368752,-0.118332:51.385426,-0.128929&travelMode=car&traffic=true&departAt=2025-03-29T08:00:20&computeTravelTimeFor=all
```

Het antwoord bevat een samenvattings element, zoals hieronder wordt weer gegeven. Omdat de vertrek tijd is ingesteld op de toekomst, is de waarde van **trafficDelayInSeconds** nul. De **travelTimeInSeconds** -waarde wordt berekend met behulp van tijd afhankelijke historische verkeers gegevens. In dit geval is de waarde van **travelTimeInSeconds** dus gelijk aan de **historicTrafficTravelTimeInSeconds** -waarde.

```json
"summary": {
    "lengthInMeters": 2131,
    "travelTimeInSeconds": 248,
    "trafficDelayInSeconds": 0,
    "departureTime": "2025-03-29T08:00:20Z",
    "arrivalTime": "2025-03-29T08:04:28Z",
    "noTrafficTravelTimeInSeconds": 225,
    "historicTrafficTravelTimeInSeconds": 248,
    "liveTrafficIncidentsTravelTimeInSeconds": 248
},
```

### <a name="sample-query"></a>Voorbeeldquery

In het tweede voor beeld hieronder hebben we een realtime-routerings aanvraag, waarbij vertrek tijd nu is. Het is niet expliciet opgegeven in de URL omdat dit de standaard waarde is.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&query=47.6422356,-122.1389797:47.6641142,-122.3011268&travelMode=car&traffic=true&computeTravelTimeFor=all
```

Het antwoord bevat een samen vatting zoals hieronder wordt weer gegeven. Vanwege congestie is de waarde voor **trafficDelaysInSeconds** groter dan nul. Het is ook groter dan **historicTrafficTravelTimeInSeconds**.

```json
"summary": {
    "lengthInMeters": 16637, 
    "travelTimeInSeconds": 2905, 
    "trafficDelayInSeconds": 1604, 
    "departureTime": "2020-02-28T01:00:20+00:00",
    "arrivalTime": "2020-02-28T01:48:45+00:00", 
    "noTrafficTravelTimeInSeconds": 872, 
    "historicTrafficTravelTimeInSeconds": 1976, 
    "liveTrafficIncidentsTravelTimeInSeconds": 2905 
},
```

## <a name="request-route-and-leg-details"></a>Route-en poot Details aanvragen

De route service retourneert standaard een matrix met coördinaten. Het antwoord bevat de coördinaten waaruit het pad bestaat in een lijst met de naam `points` . Route antwoord omvat ook de afstand van het begin van de route en de geschatte verstreken tijd. Deze waarden kunnen worden gebruikt om de gemiddelde snelheid voor de volledige route te berekenen.

In de volgende afbeelding wordt het element weer gegeven `points` .

![Element Points](media/how-to-use-best-practices-for-routing/points-list-is-hidden-img.png)

Vouw het `point` element uit om de lijst met coördinaten voor het pad weer te geven:

![Element uitgebreide punten](media/how-to-use-best-practices-for-routing/points-list-img.png)

De route beschrijvingen Api's ondersteunen verschillende indelingen van instructies die kunnen worden gebruikt door de para meter **instructionsType** op te geven. Gebruik **instructionsType = code ring** om instructies te Format teren voor een eenvoudige verwerking van de computer. Gebruik **instructionsType = label** om instructies weer te geven als tekst voor de gebruiker. Instructies kunnen ook worden opgemaakt als tekst waarbij sommige elementen van de instructies zijn gemarkeerd en de instructie wordt weer gegeven met speciale opmaak. Zie de [lijst met ondersteunde instructie typen](/rest/api/maps/route/postroutedirections#routeinstructionstype)voor meer informatie.

Als er instructies worden aangevraagd, retourneert het antwoord een nieuw element met de naam `guidance` . Het `guidance` element bevat twee soorten informatie: instructies en overzicht van de instructie.

![Type instructies](media/how-to-use-best-practices-for-routing/instructions-type-img.png)

Het `instructions` -element bevat instructies voor het omzetten van de reis en de `instructionGroups` bevat een overzicht. Elk overzicht van de instructie omvat een segment van de reis dat meerdere wegen kan omvatten. De Api's kunnen Details voor secties van een route retour neren. zoals, het coördinaten bereik van een storing of de huidige snelheid van het verkeer.

![Schakel instructies in](media/how-to-use-best-practices-for-routing/instructions-turn-by-turn-img.png)

![Overzichts instructies](media/how-to-use-best-practices-for-routing/instructions-summary-img.png)

## <a name="request-a-route-for-a-commercial-vehicle"></a>Een route aanvragen voor een bedrijfs voertuig

Azure Maps routerings-Api's bieden ondersteuning voor route ring van commercieel Voer tuigen, met behulp van de route ring De Api's beschouwen de opgegeven limieten. Zoals, de hoogte en het gewicht van het Voer tuig, en als het Voer tuig een gevaarlijke lading vormt. Als een voer tuig bijvoorbeeld brandbaar is, wordt door de routerings engine voor komen dat bepaalde tunnels bijna op het gebied van de residential omgeving zijn.

### <a name="sample-query"></a>Voorbeeldquery

Met de voorbeeld aanvraag hieronder wordt een route voor een commerciële truck opgevraagd. De vracht wagen heeft klasse 1 gevaarlijk afval materiaal.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass1&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

De route-API retourneert Route beschrijvingen die geschikt zijn voor de afmetingen van de vracht wagen en gevaarlijke afval stoffen. U kunt de route-instructies lezen door het element uit te vouwen `guidance` .

![Truck met klasse 1 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste-img.png)

### <a name="sample-query"></a>Voorbeeldquery

Als u de Amerikaanse Hazmat-klasse van de bovenstaande query wijzigt, resulteert dit in een andere route om deze wijziging aan te passen.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&vehicleWidth=2&vehicleHeight=2&vehicleCommercial=true&vehicleLoadType=USHazmatClass9&travelMode=truck&instructionsType=text&query=51.368752,-0.118332:41.385426,-0.128929
```

De onderstaande reactie geldt voor een truck die een gevaarlijk materiaal van klasse 9 vervoert. Dit is minder gevaarlijk dan een gevaarlijk materiaal van klasse 1. Wanneer u het element uitvouwt `guidance` om de richtingen te lezen, zult u merken dat de richtingen niet hetzelfde zijn. Er zijn meer route-instructies voor de vracht wagen met klasse 1 gevaarlijk materiaal.



![Truck met klasse 9 hazwaste](media/how-to-use-best-practices-for-routing/truck-with-hazwaste9-img.png)



## <a name="request-traffic-information-along-a-route"></a>Verkeers gegevens via een route aanvragen

Met de Azure Maps route Direction-Api's kunnen ontwikkel aars Details aanvragen voor elk sectie type door de `sectionType` para meter in de aanvraag op te nemen. U kunt bijvoorbeeld de snelheids informatie voor elk binnenkomend netwerk segment opvragen. Raadpleeg de [lijst met waarden voor de sleutel sectionType](/rest/api/maps/route/getroutedirections#sectiontype) voor meer informatie over de verschillende details die u kunt aanvragen.

### <a name="sample-query"></a>Voorbeeldquery

Met de volgende query wordt de `sectionType` to `traffic` . Het vraagt de secties die verkeers gegevens van Seattle naar San Diego bevatten.

```http
https://atlas.microsoft.com/route/directions/json?subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&api-version=1.0&sectionType=traffic&query=47.6062,-122.3321:32.7157,-117.1611
```

Het antwoord bevat de secties die geschikt zijn voor verkeer langs de gegeven coördinaten.

![Verkeers secties](media/how-to-use-best-practices-for-routing/traffic-section-type-img.png)

Deze optie kan worden gebruikt om de secties te kleuren wanneer de kaart wordt weer gegeven, zoals in de onderstaande afbeelding: 

![Gekleurde secties worden weer gegeven op de kaart](media/how-to-use-best-practices-for-routing/show-traffic-sections-img.png)

## <a name="calculate-and-optimize-a-multi-stop-route"></a>Een multi-stop-route berekenen en optimaliseren

Azure Maps biedt momenteel twee soorten route optimalisaties:

* Optimalisaties op basis van het gevraagde route type, zonder de volg orde van waypoints te wijzigen. U kunt de [ondersteunde route typen hier](/rest/api/maps/route/postroutedirections#routetype) vinden

* Traveling Salesman Optimization, waarmee de volg orde van de waypoints wordt gewijzigd om de beste volg orde te verkrijgen om elke stop te bezoeken

Voor multi-stop routering kan Maxi maal 150 waypoints worden opgegeven in één route aanvraag. De begin-en eind coördinaten van de locatie kunnen hetzelfde zijn, net als bij een retour. Maar u moet ten minste één extra waypoint opgeven om de route berekening te kunnen uitvoeren. Waypoints kan worden toegevoegd aan de query tussen de oorsprong en de doel coördinaten.

Als u de beste volg orde wilt optimaliseren om de opgegeven waypoints te bezoeken, moet u **computeBestOrder = True** opgeven. Dit scenario staat ook bekend als het Traveling Salesman-optimalisatie probleem.

### <a name="sample-query"></a>Voorbeeldquery

De volgende query vraagt het pad voor zes waypoints, waarbij de `computeBestOrder` para meter is ingesteld op `false` . Het is ook de standaard waarde voor de `computeBestOrder` para meter.

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=false&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

In het antwoord wordt de lengte van het pad voor 140.851 meters beschreven. dit duurt 9.991 seconden om dat pad te transporteren.

![Niet-geoptimaliseerde reactie](media/how-to-use-best-practices-for-routing/non-optimized-response-img.png)

In de onderstaande afbeelding ziet u het pad dat voortkomt uit deze query. Dit pad is een mogelijke route. Het is niet het optimale pad op basis van tijd of afstand.

![Niet-geoptimaliseerde installatie kopie](media/how-to-use-best-practices-for-routing/non-optimized-image-img.png)



Deze route waypoint order is: 0, 1, 2, 3, 4, 5 en 6.

### <a name="sample-query"></a>Voorbeeldquery

De volgende query vraagt het pad voor dezelfde zes waypoints, zoals in het bovenstaande voor beeld. Deze keer wordt de `computeBestOrder` para meter ingesteld op `true` (de Traveling Salesman Optimization).

```http
https://atlas.microsoft.com/route/directions/json?api-version=1.0&subscription-key=<Your-Azure-Maps-Primary-Subscription-Key>&computeBestOrder=true&query=47.606544,-122.336502:47.759892,-122.204821:47.670682,-122.120415:47.480133,-122.213369:47.615556,-122.193689:47.676508,-122.206054:47.495472,-122.360861
```

In het antwoord wordt de lengte van het pad voor 91.814 meters beschreven. dit duurt 7.797 seconden om dat pad te transporteren. De reis afstand en de reis tijd zijn beide lager, omdat de API de geoptimaliseerde route heeft geretourneerd.

![Geoptimaliseerd antwoord](media/how-to-use-best-practices-for-routing/optimized-response-img.png)

In de onderstaande afbeelding ziet u het pad dat voortkomt uit deze query.

![Geoptimaliseerde installatie kopie](media/how-to-use-best-practices-for-routing/optimized-image-img.png)

De optimale route heeft de volgende waypoint-volg orde: 0, 5, 1, 2, 4, 3 en 6.

>[!TIP]
> De informatie over de geoptimaliseerde waypoint-volg orde van de Routing-service biedt een aantal indexen. Hiermee worden de oorsprong en de doel-indexen uitgesloten. U moet deze waarden verhogen met 1 voor het account voor de oorsprong. Voeg vervolgens uw bestemming toe aan het einde om de volledige geordende waypoint-lijst te krijgen.

## <a name="calculate-and-bias-alternative-routes-using-supporting-points"></a>Alternatieve routes berekenen en bias met ondersteunende punten

Er kunnen zich situaties voordoen waarin u een route opnieuw wilt samen stellen om nul of meer alternatieve routes voor een referentie route te berekenen. U kunt bijvoorbeeld alternatieve routes voor klanten weer geven die uw winkel door geven. In dit geval moet u een locatie bias met ondersteunende punten. Hier volgen de stappen voor het afnemen van een locatie:

1. Een route bevinden als is en het pad ophalen uit het antwoord van de route
2. Gebruik het traject om de gewenste locaties op of in de buurt van het traject te vinden. U kunt bijvoorbeeld Azure Maps [Point of interest-API](/rest/api/maps/search/getsearchpoi) gebruiken of uw eigen gegevens in uw data base opvragen.  
3. Volg de locaties op basis van de afstand vanaf het begin van de route
4. Voeg deze locaties toe als ondersteunende punten in een nieuwe route aanvraag naar de [post route richtingen-API](/rest/api/maps/route/postroutedirections). Voor meer informatie over de ondersteunings punten raadpleegt u de documentatie over het [publiceren van route richtingen](/rest/api/maps/route/postroutedirections#supportingpoints). 

Wanneer u de [post route richtingen-API](/rest/api/maps/route/postroutedirections)aanroept, kunt u de minimale afwijkings tijd of de beperkingen voor de afstand instellen, samen met de ondersteunende punten. Gebruik deze para meters als u alternatieve routes wilt bieden, maar u ook de reis tijd wilt beperken. Wanneer deze beperkingen worden gebruikt, volgen de alternatieve routes de referentie route van het oorsprongs punt voor de opgegeven tijd of afstand. Met andere woorden, de andere routes wijken af van de referentie route volgens de opgegeven beperkingen.

De onderstaande afbeelding is een voor beeld van het weer geven van alternatieve routes met de opgegeven afwijkings limieten voor de tijd en de afstand.

![Alternatieve routes](media/how-to-use-best-practices-for-routing/alternative-routes-img.png)

## <a name="use-the-routing-service-in-a-web-app"></a>De Routing-service gebruiken in een web-app

De Azure Maps Web-SDK biedt een [service module](/javascript/api/azure-maps-rest/). Deze module is een helper-bibliotheek waarmee u gemakkelijk de Azure Maps REST-Api's in web-of Node.js toepassingen kunt gebruiken, met Java script of type script. De service module kan worden gebruikt om de geretourneerde routes op de kaart weer te geven. De module bepaalt automatisch welke API moet worden gebruikt met GET-en POST-aanvragen.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie naar:

> [!div class="nextstepaction"]
> [Azure Maps Route-service](/rest/api/maps/route)

> [!div class="nextstepaction"]
> [De service module gebruiken](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Route op de kaart weer geven](./map-route.md)

> [!div class="nextstepaction"]
> [NPM-pakket Azure Maps](https://www.npmjs.com/package/azure-maps-rest  )
