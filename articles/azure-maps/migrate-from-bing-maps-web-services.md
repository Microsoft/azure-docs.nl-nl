---
title: 'Zelfstudie: Webservices migreren vanuit Bing Maps | Microsoft Azure Maps'
description: Zelfstudie over het migreren van webservices vanuit Bing Kaarten naar Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: c6e63f67aca279b64829e67e1aa06a69d312fd58
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897021"
---
# <a name="tutorial---migrate-web-service-from-bing-maps"></a>Zelfstudie: webservice migreren vanuit Bing Kaarten

Zowel Azure Maps als Bing Maps biedt toegang tot ruimtelijke API's via REST-webservices. De API-interfaces voor deze platformen voeren vergelijkbare functies uit, maar gebruiken verschillende naamconventies en antwoordobjecten.

De volgende tabel bevat de API’s van de Azure Maps-service die vergelijkbare functionaliteit bieden als de vermelde API's van de Bing Maps-service.

| Service-API van Bing Maps                 | Service-API van Azure Maps      |
|---------------------------------------|-----------------------------|
| Automatische suggestie                           | [Zoeken](/rest/api/maps/search)     |
| Routebeschrijving (inclusief voor vrachtwagens)          | [Routebeschrijving](/rest/api/maps/route/getroutedirections)                          |
| Afstandsmatrix                       | [Routematrix](/rest/api/maps/route/postroutematrixpreview)                          |
| Afbeeldingen - Statische kaart                  | [Weergeven](/rest/api/maps/render/getmapimage)                                   |
| Isochronen                            | [Routebereik](/rest/api/maps/route/getrouterange)                                    |
| Local Insights                        | [Routebereik](/rest/api/maps/search) + [zoeken](/rest/api/maps/route/getrouterange)    |
| Lokale zoekopdracht                          | [Zoeken](/rest/api/maps/search)     |
| Locatieherkenning (nuttige plaatsen)           | [Zoeken](/rest/api/maps/search)     |
| Locaties (forward/reverse geocodering) | [Zoeken](/rest/api/maps/search)                                               |
| Uitlijnen op weg                          | [Routebeschrijvingen plaatsen](/rest/api/maps/route/postroutedirections)                         |
| Services voor ruimtelijke gegevens (SDS)           | [Route](/rest/api/maps/search) + [zoeken](/rest/api/maps/route) + andere Azure-services |
| Tijdzone                             | [Tijdzone](/rest/api/maps/timezone)  |
| Verkeersincidenten                     | [Details van verkeersincidenten](/rest/api/maps/traffic/gettrafficincidentdetail)                     |

De volgende service-API's zijn momenteel niet beschikbaar in Azure Maps:

-   Hoogtetoename - Gepland
-   Geoptimaliseerde reisroutes - Gepland. De route-API van Azure Maps ondersteunt geen optimalisatie voor één voertuig voor verkopers in de buitendienst.
-   Metagegevens van afbeeldingen: voornamelijk gebruikt voor het ophalen van tegel-Url's in Bing Maps. Azure Maps heeft een zelfstandige service voor het rechtstreeks openen van kaarttegels.

Azure Maps heeft verschillende aanvullende REST-webservices die interessant kunnen zijn;

-   [Azure Maps Creator](./creator-indoor-maps.md) – Maak een aangepaste digitale dubbel van gebouwen en spaties.
-   [Ruimtelijke bewerkingen ](/rest/api/maps/spatial) – Besteed complexe ruimtelijke berekeningen en bewerkingen, zoals geofencing, uit aan een service.
-   [Kaarttegels](/rest/api/maps/render/getmaptile) – Krijg toegang tot weg- en afbeeldingstegels vanuit Azure Maps als raster- en vectortegels.
-   [Batchroutering](/rest/api/maps/route/postroutedirectionsbatchpreview) – Hiermee kunnen maximaal 1.000 routeaanvragen in één batch worden gemaakt gedurende een bepaalde periode. Routes worden parallel op de server berekend voor een snellere verwerking.
-   [Verkeers](/rest/api/maps/traffic)doorstroming – Krijg toegang tot realtime gegevens over verkeerstromen op raster- en vectortegels.
-   [Geolocatie-API](/rest/api/maps/geolocation/getiptolocationpreview) – Haal de locatie van een IP-adres op.
-   [Weerdiensten](/rest/api/maps/weather) – Krijg toegang tot realtime weersgegevens en weersvoorspellingen.

Controleer ook de volgende handleidingen voor aanbevolen procedures:

-   [Aanbevolen procedures voor zoeken](./how-to-use-best-practices-for-search.md)
-   [Best practices voor routering](./how-to-use-best-practices-for-routing.md)

## <a name="geocoding-addresses"></a>Geocodering van adressen

Bij geocodering wordt een adres (zoals `"1 Microsoft way, Redmond, WA"`) omgezet naar een coördinaat (zoals lengtegraad: -122,1298, breedtegraad: 47.64005). Coördinaten worden vervolgens vaak gebruikt om een punaise op een kaart te plaatsen of om een kaart te centreren.

Azure Maps biedt verschillende methoden voor geocodering van adressen;

-   [Vrije adresgeocodering](/rest/api/maps/search/getsearchaddress): Een enkele adrestekenreeks (zoals `"1 Microsoft way, Redmond, WA"`) opgeven en de aanvraag direct verwerken. Deze service wordt aanbevolen voor snelle geocodering van afzonderlijke adressen.
-   [Gestructureerde adresgeocodering](/rest/api/maps/search/getsearchaddressstructured): De onderdelen van een enkel adres opgeven, zoals straatnaam, plaats, land en postcode en de aanvraag direct verwerken. Deze service wordt aanbevolen voor snelle geocodering van afzonderlijke adressen die al zijn geparseerd in afzonderlijke adresonderdelen.
-   [Batchmatige adresgeocodering](/rest/api/maps/search/postsearchaddressbatchpreview): Een aanvraag met maximaal 10.000 adressen maken en deze in de loop van de tijd laten verwerken. Geocodering van alle adressen vindt parallel plaats op de server en als dit proces is voltooid, kan de volledige resultatenset worden gedownload. Deze service wordt aanbevolen voor geocodering van grote gegevenssets.
-   [Fuzzy zoekopdrachten](/rest/api/maps/search/getsearchfuzzy): Met deze API wordt adresgeocodering gecombineerd met het zoeken naar nuttige plaatsen. Deze API maakt gebruik van een vrije tekenreeks die een adres, plaats, oriëntatiepunt, nuttige plaats of nuttigeplaatscategorie kan zijn en verwerkt de aanvraag direct. Deze API wordt aanbevolen waarin gebruikers in hetzelfde tekstvak naar adressen of nuttige plaatsen kunnen zoeken.
-   [Batchmatige fuzzy zoekopdrachten](/rest/api/maps/search/postsearchfuzzybatchpreview): Een aanvraag maken met maximaal 10.000 adressen, plaatsen, oriëntatiepunten of nuttige plaatsen en deze in de loop van de tijd laten verwerken. Alle gegevens worden parallel op de server verwerkt en als dit proces is voltooid, kan de volledige resultatenset worden gedownload.

De volgende tabellen bevatten kruisverwijzingen naar de API-parameters van Bing Maps met de vergelijkbare API-parameters in Azure Maps voor gestructureerde en vrije adresgeocodering.

**Locatie op adres (gestructureerd adres)**

| API-parameter in Bing Maps              | Vergelijkbare API-parameter in Azure Maps                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber`, `streetName` of `crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` en `countryCode`                     |
| `locality`                         | `municipality` of `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | N.v.t. – Wordt altijd geretourneerd door Azure Maps, indien beschikbaar.   |
| `include` (`incl`)               | N.v.t. – De ISO2-code van het land wordt altijd geretourneerd door Azure Maps. |
| `key`                              | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)                  | `language` - zie de documentatie [Ondersteunde talen](./supported-languages.md). |
| `userRegion` (`ur`)              | `view` -Zie de documentatie over [ondersteunde weergaven](./supported-languages.md#azure-maps-supported-views). |

Azure Maps ondersteunt ook;

-   `countrySecondarySubdivision` -Regio, districten
-   `countryTertiarySubdivision` -Gebieden met naam; buurten, kantons, gemeenschappen
-   `ofs` -Paginering van de resultaten in combinatie met de `maxResults`-parameter.

**Locatie per query (vrije adrestekenreeks)**

| API-parameter in Bing Maps              | Vergelijkbare API-parameter in Azure Maps      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | N.v.t. – Wordt altijd geretourneerd door Azure Maps, indien beschikbaar.  |
| `include` (`incl`)               | N.v.t. – De ISO2-code van het land wordt altijd geretourneerd door Azure Maps.  |
| `key`                              | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)                  | `language` - zie de documentatie [Ondersteunde talen](./supported-languages.md).  |
| `userRegion` (`ur`)              | `view` – Zie de documentatie over [ondersteunde weergaven](./supported-languages.md#azure-maps-supported-views). |

Azure Maps ondersteunt ook;

-   `typeahead` – Geeft aan of de query wordt geïnterpreteerd als een gedeeltelijke invoer en de zoekopdracht overschakelt naar de voorspellingsmodus (automatische suggestie/automatisch aanvullen).
-   `countrySet` – Een door komma's gescheiden lijst met ISO2-landcodes waartoe de zoekopdracht kan worden beperkt.
-   `lat`/`lon`, `topLeft`/`btmRight`, `radius` – Geef de locatie en het gebied van de gebruiker op om de resultaten lokaal relevant te maken.
-   `ofs` -Paginering van de resultaten in combinatie met de `maxResults`-parameter.

Een voorbeeld van het gebruik van de zoekservice is [hier](./how-to-search-for-address.md) gedocumenteerd. Raadpleeg ook de documentatie [Aanbevolen procedures voor zoeken](./how-to-use-best-practices-for-search.md).

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>Omgekeerde geocodering naar een coördinaat (een locatie zoeken op punt)

Reverse geocodering is het proces van het converteren van geografische coördinaten (zoals lengtegraad: -122.1298, breedtegraad: 47.64005) naar een geschat adres (zoals `"1 Microsoft way, Redmond, WA"`).

Azure Maps biedt verschillende methoden voor reverse geocodering;

-   [Reverse geocodering van adressen](/rest/api/maps/search/getsearchaddressreverse): Geef een enkele geografische coördinaat op om het adres bij benadering op te halen en de aanvraag direct verwerken.
-   [Reverse geocodering van zijstraten](/rest/api/maps/search/getsearchaddressreversecrossstreet): Een enkele geografische coördinaat opgeven om informatie over nabijgelegen zijstraten (bijvoorbeeld de eerste en hoofdstraat) op te halen en de aanvraag direct verwerken.
-   [Batchmatige reverse geocoding van adressen](/rest/api/maps/search/postsearchaddressreversebatchpreview): Een aanvraag met maximaal 10.000 coördinaten maken en deze in de loop van de tijd laten verwerken. Alle gegevens worden parallel op de server verwerkt en als dit proces is voltooid, kan de volledige resultatenset worden gedownload.

De volgende tabel bevat de API-parameters in Bing Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Bing Maps              | Vergelijkbare API-parameter in Azure Maps       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType` – Raadpleeg de vergelijkingstabel voor entiteitstypen hieronder.    |
| `includeNeighborhood` (`inclnb`)     | N.v.t. – Wordt altijd geretourneerd door Azure Maps, indien beschikbaar.         |
| `include` (`incl`)                   | N.v.t. – De ISO2-code van het land wordt altijd geretourneerd door Azure Maps.    |
| `key`                                | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)                      | `language` - zie de documentatie [Ondersteunde talen](./supported-languages.md).   |
| `userRegion` (`ur`)                  | `view` – Zie de documentatie over [ondersteunde weergaven](./supported-languages.md#azure-maps-supported-views). |

Raadpleeg ook de documentatie [Aanbevolen procedures voor zoeken](./how-to-use-best-practices-for-search.md).

De API voor reverse geocoderings van Azure Maps bevat enkele extra functies die niet beschikbaar zijn in Bing Maps en waarvan integratie nuttig kan zijn bij het migreren van uw app:

-   Snelheidslimietgegevens ophalen.
-   Informatie over het weggebruik ophalen: lokale weg, hoofdweg, beperkte toegang, helling enzovoort.
-   De kant van de straat waar het coördinaat zich bevindt.

**Vergelijkingstabel voor entiteitstypen**

De volgende tabel bevat kruisverwijzingen naar de waarden van entiteitstypen in Bing Maps met de equivalente eigenschapsnamen in Azure Maps.

| Entiteitstype in Bing Maps | Vergelijkbaar entiteitstype in Azure Maps               | Description                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *Adres*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | *Buurt*                             |
| `PopulatedPlace`      | `Municipality` of `MunicipalitySubdivision`     | *Stad*, *Plaats of voorstad* of *Grootstedelijk gebied*     |
| `Postcode1`           | `PostalCodeArea`                                | *Postcode* of *Zipcode*                |
| `AdminDivision1`      | `CountrySubdivision`                            | *Staat* of *Provincie*                      |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | *Regio* of *districten*                    |
| `CountryRegion`       | `Country`                                       | *Naam van land*                             |
|                       | `CountryTertiarySubdivision`                    | *Buurten*, *Kantons*, *Gemeenschappen*          |

## <a name="get-location-suggestions-autosuggest"></a>Locatiesuggesties ophalen (automatische suggestie)

Verschillende van de zoek-API’s in Azure Maps bieden ondersteuning voor de voorspellende modus, die kan worden gebruikt voor automatische suggesties. De API voor [fuzzy zoekopdrachten](/rest/api/maps/search/getsearchfuzzy) in Azure Maps lijkt het meest op de API voor automatische suggesties in Bing Maps. De volgende API’s bieden ook ondersteuning voor de voorspellende modus, voeg `&typeahead=true` toe aan de query;

-   [Vrije adresgeocodering](/rest/api/maps/search/getsearchaddress): Een enkele adrestekenreeks (zoals `"1 Microsoft way, Redmond, WA"`) opgeven en de aanvraag direct verwerken. Deze service wordt aanbevolen voor snelle geocodering van afzonderlijke adressen.
-   [Fuzzy zoekopdrachten](/rest/api/maps/search/getsearchfuzzy): Met deze API wordt adresgeocodering gecombineerd met het zoeken naar nuttige plaatsen. Deze API maakt gebruik van een vrije tekenreeks die een adres, plaats, oriëntatiepunt, nuttige plaats of nuttigeplaatscategorie kan zijn en verwerkt de aanvraag direct. Deze API wordt aanbevolen waarin gebruikers in hetzelfde tekstvak naar adressen of nuttige plaatsen kunnen zoeken.
-   [Nuttige plaatsen zoeken](/rest/api/maps/search/getsearchpoi): Nuttige plaatsen zoeken op naam. Bijvoorbeeld `"starbucks"`.
-   [Nuttigeplaatscategorieën zoeken](/rest/api/maps/search/getsearchpoicategory): Nuttige plaatsen zoeken op categorie. Bijvoorbeeld 'restaurant'.

## <a name="calculate-routes-and-directions"></a>Routes en routebeschrijvingen berekenen

Azure Maps kan worden gebruikt om routes en routebeschrijvingen te berekenen. Azure Maps biedt veel dezelfde functies als de routeringsservice van Bing Maps, zoals;

-   aankomst- en vertrektijden
-   realtime verkeersroutes en verkeersroutes op basis van voorspellingen
-   verschillende transportmodi, rijden, wandelen, vrachtwagen
-   optimalisatie van meerdere bestemmingen (verkopers in de buitendienst)

> [!NOTE]
> Voor Azure Maps moeten alle routepunten coördinaten zijn. Adressen moeten eerst een geocodering krijgen.

De routeringsservice van Azure Maps biedt de volgende API's voor het berekenen van routes;

-   [Route berekenen](/rest/api/maps/route/getroutedirections): Een route berekenen en de aanvraag direct laten verwerken. Deze API biedt ondersteuning voor zowel GET- als POST-aanvragen. POST-aanvragen worden aanbevolen als er een groot aantal routepunten wordt opgegeven of als er veel routeopties worden gebruikt, om te voorkomen dat de URL-aanvraag te lang wordt en problemen veroorzaakt.
-   [Batchroute](/rest/api/maps/route/postroutedirectionsbatchpreview): Een aanvraag met maximaal 1.000 routeaanvragen maken en deze in de loop van de tijd laten verwerken. Alle gegevens worden parallel op de server verwerkt en als dit proces is voltooid, kan de volledige resultatenset worden gedownload.
-   [Mobility-services](/rest/api/maps/mobility): Routes en routebeschrijvingen berekenen op basis van het openbaar vervoer.

De volgende tabel bevat de API-parameters in Bing Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Bing Maps                                    | Vergelijkbare API-parameter in Azure Maps               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` of `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | N.v.t.                                               |
| `distanceUnit` (`du`)                                      | N.v.t. - in Azure Maps wordt alleen het metrische systeem gebruikt.     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`, `alternativeType`, `minDeviationDistance` en `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` en `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` of `arriveAt`                          |
| `tolerances` (`tl`)                                        | N.v.t.                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n` (`wp.n`) of `viaWaypoint.n` (`vwp.n`)         | `query` - coördinaten in de indeling `lat0,lon0:lat1,lon1….`   |
| `key`                                                      | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)                                            | `language` - zie de documentatie [Ondersteunde talen](./supported-languages.md). |
| `userRegion` (`ur`)                                        | `view` – Zie de documentatie over [ondersteunde weergaven](./supported-languages.md#azure-maps-supported-views). |

De routerings-API in Azure Maps ondersteunt ook routering van vrachtwagens met dezelfde API. De volgende tabel bevat kruisverwijzingen naar de extra parameters voor vrachtwagenroutes in Bing Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Bing Maps                  | Vergelijkbare API-parameter in Azure Maps        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | N.v.t – Alleen dimensies in meters worden ondersteund. |
| `weightUnit` (`wu`)                      | N.v.t. – Alleen gewichten in kilo’s worden ondersteund. |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **N.v.t.**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **N.v.t.**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **N.v.t.**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **N.v.t.**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **N.v.t.**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> De route-API in Azure Maps retourneert standaard alleen een samenvatting (afstand en tijden) en de coördinaten voor de route. Gebruik de parameter `instructionsType` om instructies per afslag op te halen. De parameter `routeRepresentation` kan worden gebruikt om de samenvatting en het routepad te filteren.

Vergeet niet om ook de documentatie [Best practices voor routering](./how-to-use-best-practices-for-routing.md) door te nemen.

De API voor routering in Azure Maps bevat veel extra functies die niet beschikbaar zijn in Bing Maps en waarvan integratie nuttig kan zijn bij het migreren van uw app:

-   Ondersteuning voor de routetypen: kortste, snelste, trilling en zuinigst.
-   Ondersteuning voor aanvullende reismodi: fiets, bus, motor, taxi, vrachtwagen en busje.
-   Ondersteuning voor 150 routepunten.
-   Meerdere reistijden berekenen in één aanvraag; historisch verkeer, liveverkeer, geen verkeer.
-   Aanvullende wegtypen vermijden: carpoolwegen, onverharde wegen, reeds gebruikte wegen.
-   Route op basis van motorspecificaties. Routes berekenen voor voertuigen met verbrandingsmotor of elektrische voertuigen op basis van hun resterende brandstof/acculading en motorspecificaties.
-   De maximale voertuigsnelheid opgeven.

## <a name="snap-coordinates-to-road"></a>Coördinaten uitlijnen met de weg

Er zijn verschillende manieren waarop u coördinaten kunt uitlijnen met wegen in Azure Maps.

-   Gebruik de API voor routebeschrijvingen om coördinaten uit te lijnen op een logische route over het wegennet.
-   Gebruik de Azure Maps Web SDK om afzonderlijke coördinaten uit te lijnen met de dichtstbijzijnde weg op de vectortegels.
-   Gebruik de Azure Maps-vectortegels om afzonderlijke coördinaten uit te lijnen.

**De API voor routebeschrijvingen gebruiken om coördinaten uit te lijnen**

Azure Maps kan coördinaten uitlijnen aan wegen met behulp van de API voor [routebeschrijvingen](/rest/api/maps/route/postroutedirections). Deze service kan worden gebruikt om een logische route tussen een reeks coördinaten samen te stellen en is vergelijkbaar met de API voor het uitlijnen aan wegen in Bing Maps.

De API voor routebeschrijvingen kan op twee manieren worden gebruikt om coördinaten uit te lijnen met wegen.

-   Als er 150 of minder coördinaten zijn, kunnen deze worden doorgegeven als waypoints aan de API voor het ophalen van routebeschrijvingen. U kunt deze methode gebruiken om twee verschillende soorten uitgelijnde gegevens op te halen. De route-instructies bevatten de afzonderlijke uitgelijnde waypoints, terwijl het traject een geïnterpoleerde reeks coördinaten bevat die het volledige pad tussen de coördinaten vullen.
-   Als er meer dan 150 coördinaten zijn, kan de API voor het plaatsen van routebeschrijvingen worden gebruikt. De start- en eindcoördinaten moeten worden doorgegeven in de queryparameter, maar alle coördinaten kunnen worden doorgegeven aan de parameter `supportingPoints` in de hoofdtekst van de POST-aanvraag en worden opgemaakt als een verzameling GeoJSON-geometriepunten. De enige uitgelijnde gegevens die beschikbaar zijn via deze methode, zijn het traject, dat een geïnterpoleerde reeks coördinaten is waarmee het volledige pad tussen de coördinaten wordt opgevuld. [Hier staat een voorbeeld](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path) van deze methode met behulp van de services-module in de Azure Maps Web-SDK.

De volgende tabel bevat de API-parameters in Bing Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Bing Maps    | Vergelijkbare API-parameter in Azure Maps                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints` – Geef deze punten door in de hoofdtekst van de POST-aanvraag  |
| `interpolate`              | N.v.t.                                                                 |
| `includeSpeedLimit`        | N.v.t.                                                                 |
| `includeTruckSpeedLimit`   | N.v.t.                                                                 |
| `speedUnit`                | N.v.t.                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)            | `language` - zie de documentatie [Ondersteunde talen](./supported-languages.md).   |
| `userRegion` (`ur`)        | `view` – Zie de documentatie over [ondersteunde weergaven](./supported-languages.md#azure-maps-supported-views).   |

De API voor routering in Azure Maps ondersteunt ook parameters voor vrachtwagenroutes binnen dezelfde API om ervoor te zorgen dat logische paden worden berekend. De volgende tabel bevat kruisverwijzingen naar de extra parameters voor vrachtwagenroutes in Bing Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Bing Maps                 | Vergelijkbare API-parameter in Azure Maps        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | N.v.t – Alleen dimensies in meters worden ondersteund. |
| `weightUnit` (`wu`)                     | N.v.t. – Alleen gewichten in kilo’s worden ondersteund. |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **N.v.t.**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **N.v.t.**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **N.v.t.**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **N.v.t.**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **N.v.t.**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

Omdat deze methode gebruikmaakt van de API voor routebeschrijvingen, kan de volledige set opties in die service worden gebruikt voor het aanpassen van de logica die wordt gebruikt om de coördinaat uit te lijnen op wegen. Als u bijvoorbeeld een vertrektijd opgeeft, wordt er rekening gehouden met historische verkeersgegevens.

De API voor routebeschrijvingen in Azure Maps retourneert momenteel geen gegevens over snelheidslimieten, maar deze kunnen wel worden opgehaald met behulp van de API voor reverse geocodering in Azure Maps.

**De Web SDK gebruiken om coördinaten uit te lijnen**

De Azure Maps Web SDK gebruikt vectortegels om de kaarten weer te geven. Deze vectortegels bevatten de onbewerkte geometriegegevens van wegen en kunnen worden gebruikt voor het berekenen van de dichtstbijzijnde weg naar een coördinaat voor eenvoudige uitlijning van afzonderlijke coördinaten. Dit is handig als u wilt dat de coördinaten visueel over wegen worden weergegeven en u de Azure Maps Web SDK al gebruikt voor het visualiseren van de gegevens.

Deze methode lijnt echter alleen uit met de wegsegmenten die zijn geladen in de kaartweergave. Bij uitzoomen op landniveau zijn er mogelijk geen weggegevens zichtbaar, waardoor er geen uitlijning kan plaatsvinden. Op dat zoomniveau kan echter één pixel het gebied van verschillende steden vertegenwoordigen, waardoor uitlijning niet nodig is. Om dit op te lossen, kan de logica voor uitlijnen steeds worden toegepast wanneer de kaart is verplaatst. [Hier staat een codevoorbeeld](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic) waarin dit wordt gedemonstreerd.

**De Azure Maps-vectortegels gebruiken om coördinaten direct uit te lijnen**

De vectortegels uit Azure Maps bevatten de onbewerkte geometriegegevens van wegen en kunnen worden gebruikt om het dichtstbijzijnde punt op een weg te berekenen naar een coördinaat voor eenvoudige uitlijning van afzonderlijke coördinaten. Alle wegsegmenten worden weergegeven in de sectoren op zoomniveau 15, zodat u van daar uw tegels wilt ophalen. U kunt vervolgens de [quadtree tile pyramid-formule](./zoom-levels-and-tile-grid.md) gebruiken om te bepalen of er tegels nodig zijn en om de tegels te converteren naar geometrische elementen. Vandaar kan een bibliotheek voor ruimtelijke wiskunde, zoals [turf js](http://turfjs.org/) of [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite), worden gebruikt voor het berekenen van de dichtstbijzijnde lijnsegmenten.

## <a name="retrieve-a-map-image-static-map"></a>Een kaartafbeelding ophalen (statische kaart)

Azure Maps biedt een API voor het weergeven van de statische kaartafbeeldingen waarbij gegevens boven elkaar worden weergegeven. De API [Kaartafbeelding weergeven](/rest/api/maps/render/getmapimagerytile) is vergelijkbaar met de API voor statische kaarten in Bing Maps.

> [!NOTE]
> Azure Maps vereist dat het midden, alle markeringspunten en paden coördinaten zijn in de `longitude,latitude`-indeling, terwijl Bing Maps de `latitude,longitude`-indeling gebruikt.</p>
<p>Adressen moeten eerst een geocodering krijgen.

De volgende tabel bevat de API-parameters in Bing Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Bing Maps  | Vergelijkbare API-parameter in Azure Maps            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format` - opgegeven als onderdeel van het URL-pad. Op dit moment wordt alleen PNG ondersteund.  |
| `heading`                | N.v.t. – Straatkant wordt niet ondersteund.                |
| `imagerySet`             | `layer` en `style` - zie de documentatie [Ondersteunde kaartstijlen](./supported-map-styles.md).   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | N.v.t.                                            |
| `mapSize` (`ms`)         | `width` en `height` - mag maximaal 8192 x 8192 groot zijn. |
| `declutterPins` (`dcl`)  | N.v.t.                                            |
| `dpi`                    | N.v.t.                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | N.v.t.                                            |
| `pitch`                  | N.v.t. – Straatkant wordt niet ondersteund.                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | N.v.t. – Het midden of het begrenzingsvak moet worden gebruikt.     |
| `highlightEntity` (`he`) | N.v.t.                                            |
| `style`                  | N.v.t.                                            |
| routeparameters         | N.v.t.                                            |
| `key`                    | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)          | `language` - zie de documentatie [Ondersteunde talen](./supported-languages.md).   |
| `userRegion` (`ur`)      | `view` – Zie de documentatie over [ondersteunde weergaven](./supported-languages.md#azure-maps-supported-views). |

> [!NOTE]
> Azure Maps maakt gebruik van een tegelsysteem met tegels die tweemaal zo groot zijn als de kaarttegels die worden gebruikt in Bing Maps. Daarom wordt de waarde van het zoomniveau in Azure Maps één niveau dichterbij weergegeven in Azure Maps dan in Bing Maps. Verlaag het zoomniveau in de aanvragen die u migreert met 1 om dit te compenseren.

Zie de [instructiegids over de API Kaartafbeelding weergeven](./how-to-render-custom-data.md) voor meer informatie.

Naast de mogelijkheid om een statische kaartafbeelding te genereren biedt de weergaveservice van Azure Maps ook de mogelijkheid om rechtstreeks toegang te krijgen tot kaarttegels in raster- (PNG) en vectorindeling;

-   [Kaarttegel](/rest/api/maps/render/getmaptile) – Haal raster- (PNG) en vectortegels op voor de basiskaarten (wegen, grenzen, achtergrond).
-   [Tegel met kaartafbeelding](/rest/api/maps/render/getmapimagerytile) – Haal luchtfoto- en satellietbeeldtegels op.

### <a name="pushpin-url-parameter-format-comparison"></a>Vergelijking van de indeling van de URL-parameter voor de markering

**Vóór: Bing Maps**

In Bing Maps kunnen markeringspunten aan een statische kaartafbeelding worden toegevoegd met behulp van de parameter `pushpin` in de URL. De parameter `pushpin` gebruikt de locatie in de `latitude,longitude`-indeling, een pictogramstijl en een tekstlabel (maximaal drie tekens), zoals hieronder wordt weergegeven:

> `&pushpin=latitude,longitude;iconStyle;label`

Er kunnen extra markeringspunten worden toegevoegd door aanvullende `pushpin`-parameters met een andere reeks waarden toe te voegen aan de URL. De stijlen voor markeringspuntpictogrammen zijn beperkt tot een van de vooraf gedefinieerde stijlen in de API van Bing Maps.

In Bing Maps kan bijvoorbeeld een rood markeringspunt met het label 'AB' worden toegevoegd aan de kaart op coördinaten (lengtegraad:-110, breedtegraad: 45) met de volgende URL-parameter:

> `&pushpin=45,-110;7;AB`

<center>

![Markeringspunt op statische kaart in Bing Maps](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)</center>

**Na: Azure Maps**

In Azure Maps kunnen ook markeringspunten aan een statische kaartafbeelding worden toegevoegd door de parameter `pins` in de URL op te geven. Markeringspunten in Azure Maps worden gedefinieerd door middel van een pictogramstijl en een lijst met locaties die deze pictogramstijl gebruiken. Deze informatie wordt vervolgens doorgegeven naar de parameter `pins` en kan meerdere keren worden opgegeven om markeringen met verschillende stijlen te ondersteunen.

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

Er kunnen aanvullende stijlen worden gebruikt door aanvullende `pins`-parameters met een andere stijl en set locaties toe te voegen aan de URL.

Voor locaties naar markeringspunten moeten de coördinaten in Azure Maps de `longitude latitude`-indeling hebben. Bing Maps gebruikt daarentegen de `latitude,longitude`-indeling. Houd er ook rekening mee dat **er een spatie staat, geen komma** om de lengtegraad en breedtegraad te scheiden in Azure Maps.

De waarde `iconType` geeft het type markeringspunt aan dat moet worden gemaakt en kan de volgende waarden hebben:

-   `default` - het standaardspeldpictogram.
-   `none` -er wordt geen pictogram weergegeven, er worden alleen labels weergegeven.
-   `custom` - geeft aan dat er een aangepast pictogram moet worden gebruikt. Een URL die wijst naar de pictogramafbeelding kan worden toegevoegd aan het einde van de parameter `pins`, achter de locatiegegevens van de speld.
-   `{udid}` – Een unieke gegevens-id (UDID) voor een pictogram dat is opgeslagen in het Azure Maps Data Storage-platform.

Stijlen van markeringspunten in Azure Maps worden toegevoegd met de indeling `optionNameValue`, waarbij meerdere stijlen worden gescheiden door een verticale streep (`|`), zoals dit `iconType|optionName1Value1|optionName2Value2`. Houd er rekening mee dat de optienamen en waarden niet zijn gescheiden. De volgende stijloptienamen kunnen worden gebruikt voor het opmaken van markeringspunten in Azure Maps:

-   `al` - geeft de matheid (alfa) van de markeringspunten aan. Kan een getal tussen 0 en 1 zijn.
-   `an` - geeft het speldanker aan. De waarden voor de x- en y-pixel opgegeven in de notatie `x y`.
-   `co` - de kleur van de speld. Moet een 24-bits hexadecimale kleur zijn: `000000` tot `FFFFFF`.
-   `la` - geeft de ankerkleur aan. De waarden voor de x- en y-pixel opgegeven in de notatie `x y`.
-   `lc` - de kleur van het label. Moet een 24-bits hexadecimale kleur zijn: `000000` tot `FFFFFF`.
-   `ls` - de grootte van het label in pixels. Kan een getal zijn dat groter is dan 0.
-   `ro` - het aantal graden dat het pictogram moet worden gedraaid. Kan een getal tussen -360 en 360 zijn.
-   `sc` - een schaalwaarde voor het speldpictogram. Kan een getal zijn dat groter is dan 0.

Labelwaarden worden opgegeven voor elke locatie van een markeringspunt in plaats van een enkele labelwaarde te hebben die van toepassing is op alle markeringspunten in de lijst met locaties. De labelwaarde kan bestaan uit een tekenreeks van meerdere tekens en moet worden voorzien van enkele aanhalingstekens om ervoor te zorgen dat deze niet wordt verward met een stijl- of locatiewaarde.

U kunt in Azure Maps bijvoorbeeld een rood (`FF0000`) standaardpictogram toevoegen met het label 'Ruimtenaald' onder (15 50) het pictogram op coördinaten (lengtegraad:-122,349300, breedtegraad: 47.620180) met de volgende URL-parameter:

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

<center>

![Markeringspunt op statische kaart in Azure Maps](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)</center>

In het volgende voorbeeld worden drie markeringspunten toegevoegd met de labelwaarden '1', '2' en '3':

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

<center>

![Meerdere markeringspunten op statische kaart in Azure Maps](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)</center>

### <a name="draw-curve-url-parameter-format-comparison"></a>Vergelijking van de notatie van de URL-parameter om krommen te tekenen

**Vóór: Bing Maps**

In Bing Maps kunnen lijnen en veelhoeken aan een statische kaartafbeelding worden toegevoegd met behulp van de parameter `drawCurve` in de URL. De parameter `drawCurve` maakt gebruik van een vormtype, een stijltype en een lijst met locaties die op de kaart moeten worden weergegeven, zoals hieronder wordt weergegeven:

> `&drawCurve=shapeType,styleType,location1,location2...`

Er kunnen aanvullende stijlen worden gebruikt door aanvullende `drawCurve`-parameters met een andere stijl en set locaties toe te voegen aan de URL.

Locaties worden in Bing Maps opgegeven met de indeling `latitude1,longitude1_latitude2,longitude2_…`. Locaties kunnen ook worden gecodeerd.

Vormtypen in Bing Maps zijn onder meer lijnen, veelhoeken, cirkels en krommen. Stijltypen zijn onder andere lijnkleur, lijndikte, contourkleur, opvulkleur, contourdikte en straal van de cirkel.

In Bing Maps kan bijvoorbeeld een blauwe lijn met een dekking van 50% en een dikte van vier pixels aan de kaart worden toegevoegd tussen coördinaten (lengtegraad:-110, breedtegraad: 45 en lengtegraad -100, breedtegraad 50) met de volgende URL-parameter:

`&drawCurve=l,FF000088,4;45,-110_50,-100`

<center>

![Lijn op statische kaart in Bing Maps](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)</center>

**Na: Azure Maps**

In Azure Maps kunnen ook lijnen en veelhoeken aan een statische kaartafbeelding worden toegevoegd door de *path*-parameter in de URL op te geven. Net als Bing Maps kunnen een stijl en een lijst met locaties worden opgegeven in deze parameter en kan de *path*-parameter meerdere keren worden opgegeven om meerdere cirkels, lijnen en veelhoeken met verschillende stijlen weer te geven.

> `&path=pathStyles||pathLocation1|pathLocation2|...`

Voor padlocaties moeten de coördinaten in Azure Maps de `longitude latitude`-indeling hebben. Bing Maps gebruikt daarentegen de `latitude,longitude`-indeling. Houd er ook rekening mee dat **er een spatie staat, geen komma** om de lengtegraad en breedtegraad te scheiden in Azure Maps. Azure Maps ondersteunt momenteel geen gecodeerde paden. Grotere gegevenssets kunnen worden geüpload als een GeoJSON-bestand naar de Azure Maps Data Storage-API, zoals [hier](./how-to-render-custom-data.md#get-data-from-azure-maps-data-storage) wordt beschreven.

Padstijlen in Azure Maps worden toegevoegd met de indeling `optionNameValue`, waarbij meerdere stijlen worden gescheiden door een verticale streep (`|`), zoals dit `optionName1Value1|optionName2Value2`. Houd er rekening mee dat de optienamen en waarden niet zijn gescheiden. De volgende stijloptienamen kunnen worden gebruikt voor stijlpaden in Azure Maps:

-   `fa` - De matheid (alfa) van de opvulkleur die wordt gebruikt bij het weergeven van veelhoeken. Kan een getal tussen 0 en 1 zijn.
-   `fc` - De opvulkleur die wordt gebruikt om het gebied van een veelhoek weer te geven.
-   `la` - de matheid (alfa) van de lijnkleur die wordt gebruikt bij het weergeven van lijnen en de contour van veelhoeken. Kan een getal tussen 0 en 1 zijn.
-   `lc` - de lijnkleur die wordt gebruikt bij het weergeven van lijnen en de contour van veelhoeken.
-   `lw` - de breedte van de lijn in pixels.
-   `ra` - geeft de cirkelradius in meters aan.

In Azure Maps kan bijvoorbeeld een blauwe lijn met een dekking van 50% en een dikte van vier pixels aan de kaart worden toegevoegd tussen coördinaten (lengtegraad:-110, breedtegraad: 45 en lengtegraad -100, breedtegraad 50) met de volgende URL-parameter:

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

<center>

![Lijn op statische kaart in Azure Maps](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)</center>

## <a name="calculate-a-distance-matrix"></a>Een afstandsmatrix berekenen

Azure Maps biedt een API voor het berekenen van de reistijden en de afstand tussen een reeks locaties als een afstandsmatrix. De API voor de afstandsmatrix in Azure Maps is vergelijkbaar met de API voor de afstandsmatrix in Bing Maps;

-   [Routematrix](/rest/api/maps/route/postroutematrixpreview): Hiermee worden asynchroon reistijden en afstanden voor een reeks startpunten en bestemmingen berekend. Er worden maximaal 700 cellen per aanvraag ondersteund (het aantal startpunten vermenigvuldigd met het aantal bestemmingen). Met die beperking in gedachte zijn dit voorbeelden van mogelijke matrixdimensies: `700x1`, `50x10`, `10x10`, `28x25`, `10x70`.

> [!NOTE]
> Een aanvraag voor de afstandsmatrix-API kan alleen worden gedaan met behulp van een POST-aanvraag waarin de startpunt- en bestemmingsinformatie is opgenomen in de hoofdtekst.</p>
<p>Daarnaast moeten in Azure Maps alle startpunten en bestemmingen coördinaten zijn. Adressen moeten eerst een geocodering krijgen.

De volgende tabel bevat de API-parameters in Bing Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Bing Maps | Vergelijkbare API-parameter in Azure Maps                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins` - in de hoofdtekst van de aanvraag opgeven als GeoJSON.    |
| `destinations`          | `destination` - in de hoofdtekst van de aanvraag opgeven als GeoJSON. |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | N.v.t.                                                         |
| `distanceUnit`          | N.v.t. – Alle afstanden in meters.                              |
| `timeUnit`              | N.v.t. – Alle tijden in seconden.                                 |
| `key`                   | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)         | `language` - zie de documentatie [Ondersteunde talen](./supported-languages.md).  |
| `userRegion` (`ur`)     | `view` – Zie de documentatie over [ondersteunde weergaven](./supported-languages.md#azure-maps-supported-views).     |

> [!TIP]
> Alle geavanceerde routeringsopties die beschikbaar zijn in de routerings-API van Azure Maps (vrachtwagenroute, motorspecificaties, vermijden ...) worden ondersteund in de afstandsmatrix-API van Azure Maps.

## <a name="calculate-an-isochrone"></a>Een isochrone berekenen

Azure Maps biedt een API voor het berekenen van een isochrone, een veelhoek die een gebied bedekt dat in een willekeurige richting kan worden verplaatst vanaf een oorsprongspunt binnen een bepaalde tijd of hoeveelheid brandstof/acculading. De API voor routebereik in Azure Maps is vergelijkbaar met de isochrone-API in Bing Maps;

-   [Routebereik](/rest/api/maps/route/getrouterange)**: Bereken een veelhoek die een gebied bedekt dat in een willekeurige richting kan worden verplaatst vanaf een oorsprongspunt binnen een bepaalde tijd of afstand of met een beschikbare hoeveelheid brandstof/acculading.

> [!NOTE]
> Voor Azure Maps moet de queryoorsprong een coördinaat zijn. Adressen moeten eerst een geocodering krijgen.</p>
<p>Bing Maps kan ook isochronen berekenen op basis van tijd of afstand, terwijl Azure Maps isochronen kan berekenen op basis van tijd, afstand of beschikbare brandstof/acculading.

De volgende tabel bevat de API-parameters in Bing Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Bing Maps      | Vergelijkbare API-parameter in Azure Maps            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | N.v.t. – Alle tijden in seconden.                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | N.v.t. – Alle afstanden in meters.                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)              | `language` - zie de documentatie [Ondersteunde talen](./supported-languages.md).  |
| `userRegion` (`ur`)          | `view` – Zie de documentatie over [ondersteunde weergaven](./supported-languages.md#azure-maps-supported-views). |

> [!TIP]
> Alle geavanceerde routeringsopties die beschikbaar zijn in de routerings-API van Azure Maps (vrachtwagenroute, motorspecificaties, vermijden ...) worden ondersteund in de isochrone-API van Azure Maps.

## <a name="search-for-points-of-interest"></a>Zoeken naar nuttige plaatsen

In Bing Maps kan naar nuttige plaatsen worden gezocht met behulp van de volgende API’s:

-   **Lokale zoekopdracht:** Zoekt naar nuttige plaatsen in de buurt (radiale zoekopdracht) op naam of op entiteitstype (categorie). De API’s voor [zoeken naar nuttige plaats](/rest/api/maps/search/getsearchpoi) en [zoeken naar nuttige plaats op categorie](/rest/api/maps/search/getsearchpoicategory) in Azure Maps zijn vergelijkbaar met deze API.
-   **Locatieherkenning**: Hiermee wordt naar nuttige plaatsen gezocht die zich binnen een bepaalde afstand van een locatie bevinden. De API voor [zoeken in de buurt](/rest/api/maps/search/getsearchnearby) in Azure Maps lijkt het meest op deze API.
-   **Lokale inzichten:** Zoekt naar nuttige plaatsen binnen een opgegeven maximale reistijd of afstand ten opzichte van een specifieke coördinaat. Dit wordt uitgevoerd door Azure Maps door eerst een isochrone te berekenen en deze vervolgens door te geven aan de API voor [zoeken binnen geometrie](/rest/api/maps/search/postsearchinsidegeometry).

Azure Maps biedt verschillende zoek-API's voor nuttige plaatsen:

-   [Nuttige plaatsen zoeken](/rest/api/maps/search/getsearchpoi): Nuttige plaatsen zoeken op naam. Bijvoorbeeld `"starbucks"`.
-   [Nuttigeplaatscategorieën zoeken](/rest/api/maps/search/getsearchpoicategory): Nuttige plaatsen zoeken op categorie. Bijvoorbeeld 'restaurant'.
-   [In de buurt zoeken](/rest/api/maps/search/getsearchnearby): Hiermee wordt naar nuttige plaatsen gezocht die zich binnen een bepaalde afstand van een locatie bevinden.
-   [Fuzzy zoekopdrachten](/rest/api/maps/search/getsearchfuzzy): Met deze API wordt adresgeocodering gecombineerd met het zoeken naar nuttige plaatsen. Deze API maakt gebruik van een vrije tekenreeks die een adres, plaats, oriëntatiepunt, nuttige plaats of nuttigeplaatscategorie kan zijn en verwerkt de aanvraag direct. Deze API wordt aanbevolen waarin gebruikers in hetzelfde tekstvak naar adressen of nuttige plaatsen kunnen zoeken.
-   [Zoeken binnen geometrie](/rest/api/maps/search/postsearchinsidegeometry): Zoeken naar nuttige plaatsen die zich binnen een opgegeven geometrie (veelhoek) bevinden.
-   [Zoeken langs route](/rest/api/maps/search/postsearchalongroute): Zoeken naar nuttige plaatsen die zich aan een opgegeven routepad bevinden.
-   [Batchmatige fuzzy zoekopdrachten](/rest/api/maps/search/postsearchfuzzybatchpreview): Een aanvraag maken met maximaal 10.000 adressen, plaatsen, oriëntatiepunten of nuttige plaatsen en deze in de loop van de tijd laten verwerken. Alle gegevens worden parallel op de server verwerkt en als dit proces is voltooid, kan de volledige resultatenset worden gedownload.

Raadpleeg ook de documentatie [Aanbevolen procedures voor zoeken](./how-to-use-best-practices-for-search.md).

## <a name="get-traffic-incidents"></a>Verkeersincidenten ophalen

Azure Maps biedt verschillende API's voor het ophalen van verkeersgegevens. Er zijn twee soorten verkeersgegevens beschikbaar;

-   **Stroomgegevens** – Voorzien in metrische gegevens over de verkeersstroom op delen van wegen. Deze worden vaak gebruikt om een kleurcode aan wegen toe te kennen. Deze gegevens worden om de 2 minuten bijgewerkt.
-   **Incidentgegevens** – Voorzien in gegevens over wegwerkzaamheden, wegsluitingen, ongelukken en andere incidenten die van invloed kunnen zijn op verkeer. Deze gegevens worden om de minuut bijgewerkt.

Bing Maps biedt stroom- en incidentgegevens via de interactieve besturingselementen voor kaarten en stelt incidentgegevens ook beschikbaar als een service.

Verkeersgegevens zijn ook geïntegreerd in de interactieve besturingselementen van Azure Maps. Azure Maps biedt ook de volgende API's voor verkeersdiensten;

-   [Segmenten van verkeersstromen](/rest/api/maps/traffic/gettrafficflowsegment): Bevat informatie over de snelheden en reistijden van het deel van de weg dat het dichtst bij het opgegeven coördinaten ligt.
-   [Tegels van verkeersstromen](/rest/api/maps/traffic/gettrafficflowtile): Biedt raster- en vectortegels met gegevens over de verkeersstromen. Deze kunnen worden gebruikt met de besturingselementen in Azure Maps of in besturingselementen voor kaarten van derden, zoals Leaflet. De vectortegels kunnen ook worden gebruikt voor geavanceerde gegevensanalyse.
-   [Details van verkeersincidenten](/rest/api/maps/traffic/gettrafficincidentdetail): Biedt details over verkeersincidenten binnen een selectiekader, zoomniveau en verkeersmodel.
-   [Tegels voor verkeersincidenten](/rest/api/maps/traffic/gettrafficincidenttile): Biedt raster- en vectortegels met gegevens over verkeersincidenten.
-   [Viewport voor verkeersincidenten](/rest/api/maps/traffic/gettrafficincidentviewport): Haalt de juridische en technische informatie op voor de viewport die in de aanvraag wordt beschreven, zoals de verkeersmodel-ID.

De volgende tabel bevat de parameters voor de API voor verkeer in Bing Maps en de vergelijkbare parameters voor de API voor details van verkeersincidenten in Azure Maps.

| API-parameter in Bing Maps  | Vergelijkbare API-parameter in Azure Maps   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` en `boundingZoom`      |
| `includeLocationCodes`   | N.v.t.                                   |
| `severity` (`s`)         | N.v.t. – Alle gegevens worden geretourneerd               |
| `type` (`t`)             | N.v.t. – Alle gegevens worden geretourneerd               |
| `key`                    | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)          | `language` - zie de documentatie [Ondersteunde talen](./supported-languages.md). |
| `userRegion` (`ur`)      | `view` – Zie de documentatie over [ondersteunde weergaven](./supported-languages.md#azure-maps-supported-views). |

## <a name="get-a-time-zone"></a>Een tijdzone ophalen

Azure Maps biedt een API voor het ophalen van de tijdzone waarin een coördinaat zich bevindt. De tijdzone-API in Azure Maps is vergelijkbaar met de tijdzone-API in Bing Maps;

-   [Tijdzone per coördinaat](/rest/api/maps/timezone/gettimezonebycoordinates): Geef een coördinaat op en haal de details op voor de tijdzone waarin deze valt.

De volgende tabel bevat de API-parameters in Bing Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Bing Maps | Vergelijkbare API-parameter in Azure Maps          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | N.v.t. – Adressen moeten eerst worden gegeocodeerd.      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | N.v.t. – Altijd opgenomen in de reactie door Azure Maps. |
| `key`                   | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](./azure-maps-authentication.md). |
| `culture` (`c`)         | `language` - zie de documentatie [Ondersteunde talen](./supported-languages.md).  |
| `userRegion` (`ur`)     | `view` – Zie de documentatie over [ondersteunde weergaven](./supported-languages.md#azure-maps-supported-views).  |

Daarnaast biedt het Azure Maps-platform ook een aantal extra tijdzone-API's om te helpen bij conversies mettijdzone namen en -id's;

-   [Tijdzone op id](/rest/api/maps/timezone/gettimezonebyid): Hiermee worden de huidige, historische en toekomstige tijdzonegegevens voor de opgegeven IANA-tijdzone-id geretourneerd.
-   [Opsomming IANA-tijdzones](/rest/api/maps/timezone/gettimezoneenumiana): Retourneert een volledige lijst met IANA-tijdzone-id's. Updates in de IANA-service worden binnen één dag in het systeem doorgevoerd. 
-   [Opsomming Windows-tijdzones](/rest/api/maps/timezone/gettimezoneenumwindows): Retourneert een volledige lijst met Windows-tijdzone-id's.
-   [IANA-tijdzoneversie](/rest/api/maps/timezone/gettimezoneianaversion): Retourneert het huidige IANA-versienummer dat wordt gebruikt door Azure Maps. 
-   [Windows-tijdzone naar IANA](/rest/api/maps/timezone/gettimezonewindowstoiana): Retourneert de overeenkomstige IANA-id van een geldige Windows-tijdzone-id. Er kunnen meerdere IANA-id's voor één Windows-id worden geretourneerd.

## <a name="spatial-data-services-sds"></a>Services voor ruimtelijke gegevens (SDS)

De services voor ruimtelijke gegevens in Bing Maps bieden drie belangrijke functies:

-   Batchgewijze geocodering – Verwerk een grote batch met geocodes van adressen in één aanvraag.
-   Gegevens van administratieve grenzen ophalen – Gebruik een coördinaat en haal een snijpuntgrens op voor een opgegeven entiteitstype.
-   Ruimtelijke bedrijfsgegevens hosten en opvragen – Upload een eenvoudige 2D-gegevenstabel en open deze met enkele eenvoudige ruimtelijke query's.

### <a name="batch-geocode-data"></a>Geocodegegevens batchgewijs verwerken

Bij batchgewijze geocodering wordt een groot aantal adressen of plaatsen genomen, die allemaal in één aanvraag aan een service worden doorgegeven, waarna ze in parallel worden gegeocodeerd en de resultaten in één antwoord worden geretourneerd.

Met Bing Maps kunnen maximaal 200.000 adressen worden doorgegeven in één aanvraag voor batchgewijze geocodering. Deze aanvraag wordt in een wachtrij geplaatst en doorgaans verwerkt in een paar minuten tot enkele uren, afhankelijk van de grootte van de gegevensset en de belasting van de service. Elk adres in de aanvraag heeft een transactie gegenereerd.

Azure Maps heeft een service voor batchgewijze geocodering, maar daarmee kunnen maximaal 10.000 adressen worden doorgegeven in één aanvraag. Afhankelijk van de grootte van de gegevensset en de belasting van de service wordt de aanvraag in enkele seconden tot minuten verwerkt. Elk adres in de aanvraag heeft een transactie gegenereerd. De service voor batchgewijze geocodering in Azure Maps is alleen beschikbaar vanaf categorie S1.

Een andere optie voor het geocoderen van een groot aantal adressen met Azure Maps is door parallelle aanvragen te maken voor de standaard zoek-API's. Deze services accepteren slechts één adres per aanvraag, maar kunnen worden gebruikt in combinatie met de S0-categorie, die ook limieten voor vrij gebruik biedt. Met de categorie S0 kunnen vanuit één account maximaal 50 aanvragen per seconde naar het Azure Maps-platform worden verzonden. Tijdens de verwerking moeten de aanvragen dus binnen die limiet vallen. In dat geval is het mogelijk om een uur meer dan 180.000 adressen te verwerken. Categorie S1 heeft geen gedocumenteerde limiet voor het aantal query's per seconde dat met één account kan worden gemaakt, waardoor veel meer gegevens sneller kunnen worden verwerkt. Het gebruik van de service voor batchgewijze geocodering helpt echter de totale hoeveelheid gegevens die wordt overgedragen te verminderen, waardoor het netwerkverkeer drastisch afneemt.

-   [Vrije adresgeocodering](/rest/api/maps/search/getsearchaddress): Een enkele adrestekenreeks (zoals `"1 Microsoft way, Redmond, WA"`) opgeven en de aanvraag direct verwerken. Deze service wordt aanbevolen voor snelle geocodering van afzonderlijke adressen.
-   [Gestructureerde adresgeocodering](/rest/api/maps/search/getsearchaddressstructured): De onderdelen van een enkel adres opgeven, zoals straatnaam, plaats, land en postcode en de aanvraag direct verwerken. Deze service wordt aanbevolen voor snelle geocodering van afzonderlijke adressen die al zijn geparseerd in afzonderlijke adresonderdelen.
-   [Batchmatige adresgeocodering](/rest/api/maps/search/postsearchaddressbatchpreview): Een aanvraag met maximaal 10.000 adressen maken en deze in de loop van de tijd laten verwerken. Geocodering van alle adressen vindt parallel plaats op de server en als dit proces is voltooid, kan de volledige resultatenset worden gedownload. Deze service wordt aanbevolen voor geocodering van grote gegevenssets.
-   [Fuzzy zoekopdrachten](/rest/api/maps/search/getsearchfuzzy): Met deze API wordt adresgeocodering gecombineerd met het zoeken naar nuttige plaatsen. Deze API maakt gebruik van een vrije tekenreeks die een adres, plaats, oriëntatiepunt, nuttige plaats of nuttigeplaatscategorie kan zijn en verwerkt de aanvraag direct. Deze API wordt aanbevolen waarin gebruikers in hetzelfde tekstvak naar adressen of nuttige plaatsen kunnen zoeken.
-   [Batchmatige fuzzy zoekopdrachten](/rest/api/maps/search/postsearchfuzzybatchpreview): Een aanvraag maken met maximaal 10.000 adressen, plaatsen, oriëntatiepunten of nuttige plaatsen en deze in de loop van de tijd laten verwerken. Alle gegevens worden parallel op de server verwerkt en als dit proces is voltooid, kan de volledige resultatenset worden gedownload.

### <a name="get-administrative-boundary-data"></a>Gegevens over administratieve grenzen ophalen

In Bing Maps worden administratieve grenzen voor landen, provincies, regio's, steden en postcodes beschikbaar gesteld via de API voor geografische gegevens. Deze API neemt een coördinaat of een query op voor geocodering. Als een query wordt doorgegeven, wordt deze gegeocodeerd en worden de coördinaten van het eerste resultaat gebruikt. Deze API neemt de coördinaten en haalt de grens op van het opgegeven entiteitstype dat de coördinaat kruist. Houd er rekening mee dat deze API niet noodzakelijkerwijs de grens retourneert voor de query die is doorgegeven. Als een query voor `"Seattle, WA"` wordt doorgegeven, maar de waarde van het entiteitstype op land is ingesteld, wordt de grens voor de Verenigde Staten geretourneerd.

Azure Maps biedt ook toegang tot administratieve grenzen (landen, provincies, regio's, steden en postcodes). Om een grens op te halen, moet u een query uitvoeren voor een van de zoek-API's voor de gewenste grens (bijvoorbeeld `Seattle, WA`). Als het zoekresultaat een bijbehorende grens heeft, wordt er een geometrie-ID in het antwoord gegeven. Vervolgens kan de API voor het zoeken naar veelhoeken worden gebruikt om de exacte grenzen voor een of meer geometrie-id's op te halen. Dit is iets anders dan in Bing Maps, omdat Azure Maps de grens retourneert voor wat er is doorzocht, terwijl Bing Maps een grens retourneert voor een opgegeven entiteitstype bij een opgegeven coördinaat. Daarnaast retourneert Azure Maps de grensgegevens in GeoJSON-indeling.

Samenvattend:

1.  Geef een query door voor de grens die u wilt ontvangen in een van de volgende zoek-API's.
    -   [Vrije adresgeocodering](/rest/api/maps/search/getsearchaddress)
    -   [Gestructureerde adresgeocodering](/rest/api/maps/search/getsearchaddressstructured)
    -   [Batchmatige adresgeocodering](/rest/api/maps/search/postsearchaddressbatchpreview)
    -   [Fuzzy zoekopdracht](/rest/api/maps/search/getsearchfuzzy)
    -   [Batchmatige fuzzy zoekopdrachten](/rest/api/maps/search/postsearchfuzzybatchpreview)
2.  Als de gewenste resultaten een geometrie-id hebben, geeft u deze door aan de [API voor het zoeken naar veelhoeken](/rest/api/maps/search/getsearchpolygon).

### <a name="host-and-query-spatial-business-data"></a>Ruimtelijke bedrijfsgegevens hosten en opvragen

De services voor ruimtelijke gegevens in Bing Maps bieden een eenvoudige opslagoplossing voor ruimtelijke gegevens voor het hosten van zakelijke gegevens en het beschikbaar stellen ervan als ruimtelijke REST-service. Deze service biedt vier hoofdquery's: zoeken op eigenschap, zoeken in de buurt, zoeken binnen begrenzingsvak en zoeken binnen 1 mijl van een route. Veel bedrijven die deze service gebruiken, hebben vaak al hun zakelijke gegevens opgeslagen in een database en een kleine subset ervan naar deze service geüpload om toepassingen, zoals zoekfuncties voor winkels, mogelijk te maken. Omdat verificatie op basis van sleutels basale beveiliging biedt, is het raadzaam deze service alleen te gebruiken met openbare gegevens.

De meeste bedrijfslocatiegegevens beginnen in een database. Daarom is het raadzaam om bestaande Azure-opslagoplossingen, zoals Azure SQL of Azure PostgreSQL (met de PostGIS-invoegtoepassing) te gebruiken. Beide opslagoplossingen ondersteunen ruimtelijke gegevens en bieden een uitgebreide reeks mogelijkheden voor ruimtelijke query's. Zodra uw gegevens zich in een geschikte opslagoplossing bevinden, kunnen ze in uw toepassing worden geïntegreerd door een aangepaste webservice te maken of door een framework zoals ASP.NET of Entity Framework te gebruiken. Met deze aanpak beschikt u over meer query's en veel hogere beveiligingsopties.

Azure Cosmos DB biedt ook een beperkt aantal ruimtelijke mogelijkheden die, afhankelijk van uw scenario, mogelijk voldoende zijn.

Hier volgen enkele nuttige bronnen rond het hosten en opvragen van ruimtelijke gegevens in Azure.

-   [Overzicht van ruimtelijke gegevenstypen in Azure SQL](/sql/relational-databases/spatial/spatial-data-types-overview)
-   [Azure SQL Spatial – Dichtstbijzijnde buren opvragen](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
-   [Overzicht van georuimtelijke mogelijkheden in Azure Cosmos DB](../cosmos-db/sql-query-geospatial-intro.md)

## <a name="client-libraries"></a>Clientbibliotheken

Azure Maps biedt clientbibliotheken voor de volgende computertalen;

-   JavaScript, TypeScript, Node.js – [documentatie](./how-to-use-services-module.md) \| [NPM-pakket](https://www.npmjs.com/package/azure-maps-rest)

Bibliotheken voor opensource-clients voor andere programmeertalen;

-   .NET Standard 2.0 – [GitHub-project](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet-pakket](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de REST-services van Azure Maps.

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor het gebruik van Zoekservice](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Best practices voor het gebruik van de routeringsservice](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [De services-module gebruiken (Web SDK)](how-to-use-best-practices-for-routing.md)

> [!div class="nextstepaction"]
> [Naslagdocumentatie over de REST-service-API van Azure Maps](/rest/api/maps/)

> [!div class="nextstepaction"]
> [Codevoorbeelden](/samples/browse/?products=azure-maps)