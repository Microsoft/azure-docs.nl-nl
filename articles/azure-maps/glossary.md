---
title: Woorden lijst Azure Maps | Microsoft Docs
description: Een verklarende woorden lijst met veelgebruikte termen die zijn gekoppeld aan Azure Maps, Location-Based Services en GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 05071be4fc1705787a649d331613e8d9399f983f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92897174"
---
# <a name="glossary"></a>Woordenlijst

In de volgende lijst worden veelvoorkomende woorden beschreven die worden gebruikt met de Azure Maps Services.

## <a name="a"></a>A

<a name="address-validation"></a>**Adres validatie**: het proces voor het controleren van het bestaan van een adres.

<a name="advanced-routing"></a>**Geavanceerde route ring**: een verzameling services die voorbereidings bewerkingen uitvoeren met behulp van verkeers routering gegevens; zoals, het berekenen van bereik bare bereiken (van isochronen), de afstands matrices en batch route aanvragen.

<a name="aerial-imagery"></a>**Foto afbeelding**: Zie [satelliet afbeelding](#satellite-imagery). 

<a name="along-a-route-search"></a>**Langs een route zoeken**: een ruimtelijke query die zoekt naar gegevens binnen een opgegeven afronding tijd of afstand van een routenet pad.

<a name="altitude"></a>**Hoogte**: de hoogte of verticale verhoging van een punt boven een referentie-Opper vlak. De hoogte metingen zijn gebaseerd op een gegeven referentie datum, zoals gemiddeld Sea-niveau. Zie ook uitbrei ding van bevoegdheden.

<a name="ambiguous"></a>**Ambigu**: een status van onzekerheid in de gegevens classificatie die bestaat wanneer een object twee of meer waarden voor een bepaald kenmerk kan toewijzen. Bij geocodering "CA" worden bijvoorbeeld twee dubbel zinnige resultaten geretourneerd: "Canada" en "Californië". "CA" is een land/regio en een status code, respectievelijk "Canada" en "Californië". 

<a name="annotation"></a>**Aantekening**: tekst of afbeeldingen die worden weer gegeven op de kaart om informatie aan de gebruiker toe te voegen. Aantekening kan een specifieke kaart entiteit identificeren of beschrijven, algemene informatie geven over een gebied op de kaart of informatie over de kaart zelf opgeven.

<a name="antimeridian"></a>**Antimeridian**: ook bekend als de 180 <sup>th</sup> meridiaan. Dit is het punt waar-180 graden en 180 graden van de lengte graad overeenkomen. Dit is het tegenovergestelde van de Prime meridiaan op de hele wereld.

<a name="application-programming-interface-api"></a>**API (Application Programming Interface)**: een specificatie waarmee ontwikkel aars toepassingen kunnen maken.

<a name="api-key"></a>**API-sleutel**: Zie [verificatie van gedeelde sleutel](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Interesse gebied (AOI)**: de mate waarin een focus gebied wordt gedefinieerd voor een kaart of een database productie.

<a name="asset-tracking"></a>**Tracering van activa**: het proces van het bijhouden van de locatie van een activum, zoals een persoon, een voer tuig of een ander object.

<a name="asynchronous-request"></a>**Asynchrone aanvraag**: een HTTP-aanvraag waarmee een verbinding wordt geopend en een aanvraag wordt gemaakt naar de server die een id voor de asynchrone aanvraag retourneert, waarna de verbinding wordt gesloten. De server blijft de aanvraag verwerken en de gebruiker kan de status controleren met behulp van de id. Wanneer de verwerking van de aanvraag is voltooid, kan de gebruiker de reactie downloaden. Dit type aanvraag wordt gebruikt voor langlopende processen.

<a name="autocomplete"></a>**Automatisch aanvullen**: een functie in een toepassing die de rest van een woord voor spelt dat een gebruiker typt. 

<a name="autosuggest"></a>**Automatische suggestie**: een functie in een toepassing die logische mogelijkheden voorspelt voor wat de gebruiker typt.

<a name="azure-location-based-services-lbs"></a>**Azure Location Based Services (lbs)**: de oude naam van Azure Maps bij de preview-versie.

<a name="azure-active-directory"></a>**Azure Active Directory (Azure AD)**: Azure AD is de cloud-gebaseerde service voor identiteits-en toegangs beheer van micro soft. Azure Maps Azure AD-integratie is momenteel beschikbaar als preview-versie voor alle Azure Maps-Api's. Azure AD biedt ondersteuning voor op rollen gebaseerd toegangs beheer (Azure RBAC) voor Azure om de toegang tot Azure Maps resources te verfijnen. Zie [Azure Maps en Azure AD](azure-maps-authentication.md) en [verificatie beheren in azure Maps](how-to-manage-authentication.md)voor meer informatie over Azure Maps Azure AD-integratie.

<a name="azure-maps-key"></a>**Azure Maps sleutel**: Zie [verificatie van gedeelde sleutel](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a>**Basis kaart**: het gedeelte van een kaart toepassing dat achtergrond referentie-informatie, zoals wegen, bezienswaardigheden en politieke grenzen, weergeeft.

<a name="batch-request"></a>**Batch-aanvraag**: het proces waarbij meerdere aanvragen worden gecombineerd tot één aanvraag.

<a name="bearing"></a>**Voorzien** van: de horizontale richting van een punt ten opzichte van een ander punt. Dit wordt uitgedrukt als een hoek ten opzichte van Noord, van 0 tot 360 graden in de richting van de klok. 

<a name="boundary"></a>**Grens**: een lijn of veelhoek die de aangrenzende politieke entiteiten scheidt, zoals landen/regio's, districten en eigenschappen. Een grens is een lijn die al dan niet mag worden gevolgd door fysieke functies, zoals rivieren, bergen of wanden.

<a name="bounds"></a>**Grenzen**: Zie [selectie kader](#bounding-box).

<a name="bounding-box"></a>**Begrenzingsvak**: een set coördinaten die wordt gebruikt om een rechthoekig gebied op de kaart weer te geven. 

## <a name="c"></a>C

<a name="cadastre"></a>**Cadastre**: een record met geregistreerde land en eigenschappen. Zie ook [pakket](#parcel).

<a name="camera"></a>**Camera**: in de context van een interactief kaart besturings element, definieert een camera het veld kaarten van weer gave. De View Port van de camera wordt bepaald op basis van de volgende kaart parameters: Center, zoom niveau, Toon hoogte. 

<a name="centroid"></a>**Massa middelpunt**: het geometrische midden van een functie. De massa middelpunt van een lijn zou het middel punt zijn terwijl de massa middelpunt van een veelhoek het midden van het gebied zou zijn.

<a name="choropleth-map"></a>**Choropleth kaart**: een thematische kaart waarin gebieden worden gearceerd in verhouding tot een meting van een statistische variabele. Deze statistische variabele wordt weer gegeven op de kaart. U kunt bijvoorbeeld de grens van elke Amerikaanse staat op basis van de relatieve populatie naar alle andere statussen kleuren.

<a name="concave-hull"></a>**Holle romp**: een vorm die een mogelijke holle geometrie vertegenwoordigt die alle vormen in de opgegeven gegevensset omsluit. De gegenereerde vorm is vergelijkbaar met het inpakken van de gegevens met een plastic terugloop en deze vervolgens te verhitten, waardoor grote hoeveel heden van punten naar grot in andere gegevens punten worden verkleind.

<a name="consumption-model"></a>**Verbruiks model**: informatie die de snelheid definieert waarmee een voer tuig brand stof of elektriciteit verbruikt. Zie ook de [documentatie over het verbruiks model](consumption-model.md).

<a name="control"></a>**Besturings element**: een zelfstandig of herbruikbaar onderdeel dat bestaat uit een Graphical User Interface dat een set gedrag definieert voor de interface. Bijvoorbeeld: een kaart besturings element is doorgaans het gedeelte van de gebruikers interface dat een interactieve kaart laadt.

<a name="convex-hull"></a>**Convexe romp**: een convexe romp is een vorm die de minimale bol-geometrie vertegenwoordigt die alle vormen in de opgegeven gegevensset omsluit. De gegenereerde vorm is vergelijkbaar met het inpakken van een elastische band rond de gegevensset.

<a name="coordinate"></a>**Coördinaat**: bestaat uit de lengte-en breedte waarden die worden gebruikt om een locatie op een kaart weer te geven.

<a name="coordinate-system"></a>**Coördinaten systeem**: een referentie raamwerk dat wordt gebruikt voor het definiëren van de posities van punten in ruimte in twee of drie dimensies.

<a name="country-code"></a>**Land nummer**: een unieke id voor een land/regio op basis van de ISO-standaard. ISO2 is een code van twee tekens voor een land/regio (bijvoorbeeld Verenigde Staten), die ISO3 staat voor een code van drie tekens (bijvoorbeeld USA).

<a name="country-subdivision"></a>**Lands streek**: een deelvak van het hoogste niveau van een land/regio, algemeen bekend als een staat of provincie.

<a name="country-secondary-subdivision"></a>**Land secundaire deelvak**: een indeling op het tweede niveau van een land/regio, algemeen bekend als een regio.

<a name="country-tertiary-subdivision"></a>**Lands tertiaire onderverdeling**: een indeling van een derde niveau van een land/regio, meestal een benoemd gebied, zoals een naar boven.

<a name="cross-street"></a>**Kruis straat**: een punt waar twee of meer straten INTERSECT.

<a name="cylindrical-projection"></a>**Cilindrische projectie**: een projectie waarmee punten van een spheroid of bol worden getransformeerd naar een Tangent of een secans cilinder. De cilinder wordt vervolgens van boven naar beneden gesegmenteerd en afgevlakt in een vlak.

## <a name="d"></a>D

<a name="datum"></a>**Datum**: de referentie specificaties van een meet systeem, een systeem van coördinaten posities op een Opper vlak (een horizontale datum) of de hoogte boven of onder een Opper vlak (een verticale datum).

<a name="dbf-file"></a>**DBF-bestand**: een bestands indeling voor de data base die wordt gebruikt in combi natie met ESRI (SHP).

<a name="degree-minutes-seconds-dms"></a>**Graden minuten seconden (DMS)**: de maat eenheid voor het beschrijven van breedte graad en lengte graad. Een diploma is 1/360 de<sup>th</sup> van een cirkel. Een diploma is verder onderverdeeld in 60 minuten en een minuut wordt in 60 seconden verdeeld.

<a name="delaunay-triangulation"></a>**Delaunay triangulatie**: een techniek voor het maken van een net van aaneengesloten, niet-overlappende drie hoeken van een gegevensset van punten. De circumscribing cirkel van elke drie hoek bevat geen punten uit de gegevensset in de binnenkant.

<a name="demographics"></a>**Demografische** gegevens: de statistische kenmerken (zoals leeftijd, geboorte rente en inkomsten) van een menselijke populatie.

<a name="destination"></a>**Doel**: een eind punt of locatie waar iemand op reis is.

<a name="digital-elevation-model-dem"></a>**Digital Elevation Model (DEM)**: een gegevensset van verhogings waarden die betrekking hebben op een Opper vlak, met behulp van een gemeen schappelijke datum vastgelegd in een gebied met regel matige tussen pozen. DEMs worden meestal gebruikt om terrein verlichting te vertegenwoordigen.

<a name="dijkstra's-algorithm"></a>**Algoritme van Dijkstra**: een algoritme waarmee de verbinding van een netwerk wordt onderzocht om het kortste pad tussen twee punten te vinden.

<a name="distance-matrix"></a>**Afstands matrix**: een matrix die reis tijd-en afstands gegevens tussen een reeks oorsprongen en bestemmingen bevat. 

## <a name="e"></a>E

<a name="elevation"></a>**Uitbrei ding**: de verticale afstand van een punt of een object boven of onder een referentie-Opper vlak of datum. Over het algemeen is het referentie oppervlak het niveau Sea. Uitbrei ding van bevoegdheden heeft doorgaans betrekking op de verticale hoogte van het land.

<a name="envelope"></a>**Envelop**: Zie [selectie kader](#bounding-box).

<a name="extended-postal-code"></a>**Uitgebreide post code**: een post code die mogelijk aanvullende informatie bevat. In Nederland hebben Post codes bijvoorbeeld vijf cijfers. Maar een uitgebreide post code, ook wel zip + 4 genoemd, bevat vier extra cijfers. Deze extra cijfers worden gebruikt voor het identificeren van een geografisch segment binnen het leverings gebied van vijf cijfers, zoals een stads blok, een groep appartementen of een postbus van het bedrijf. Het geografische segment helpt u bij het sorteren en leveren van efficiënte e-mail berichten.

<a name="extent"></a>**Gebied**: Zie [selectie kader](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Federatieve verificatie**: een verificatie methode waarmee één aanmeldings-of authenticatie mechanisme kan worden gebruikt voor meerdere web-en mobiele apps. 

<a name="feature"></a>**Functie**: een object dat een geometrie combineert met aanvullende meta gegevens. 

<a name="feature-collection"></a>**Functie verzameling**: een verzameling functie objecten.

<a name="find-along-route"></a>Volgende **route zoeken**: een ruimtelijke query die zoekt naar gegevens binnen een opgegeven afronding tijd of afstand van een routenet pad.

<a name="find-nearby"></a>**Nabijgelegen zoeken**: een ruimtelijke query die een vaste lineaire afstand (als de hanen, vliegt) doorzoekt vanaf een punt.

<a name="fleet-management"></a>**Vloot beheer**: het beheer van commerciële Voer tuigen, zoals auto's, vracht wagens, schepen en plannen. Vloot beheer kan bestaan uit een reeks functies, zoals het financieren van het Voer tuig, onderhoud, telematica (tracering en diagnose), alsook stuur programma, snelheid, brand stof en gezondheids-en veiligheids beheer. Vloot beheer is een proces dat wordt gebruikt door bedrijven die afhankelijk zijn van het Trans Port in hun bedrijf. De bedrijven willen de Risico's tot een minimum beperken en hun totale kosten voor het Trans Port en personeel verminderen, terwijl zij voldoen aan de overheids wetgeving.

<a name="free-flow-speed"></a>**Vrije stroom snelheid**: de vrije stroom snelheid die wordt verwacht onder ideale omstandigheden. Doorgaans de snelheids limiet.

<a name="free-form-address"></a>**Vrije-vorm adres**: een volledig adres dat wordt weer gegeven als één regel tekst.

<a name="fuzzy-search"></a>**Zoek actie op fuzzy**: een zoek opdracht die in een vrije teken reeks wordt gebruikt voor tekst die een adres of interesse kan zijn. 

## <a name="g"></a>G

<a name="geocode"></a>**Geocode**: een adres of locatie die is geconverteerd naar een coördinaat die kan worden gebruikt om die locatie weer te geven op een kaart. 

<a name="geocoding"></a>**Geocodering**: ook wel ' geocodering ' genoemd, is het proces waarbij de locatie gegevens worden geconverteerd naar coördinaten. 

<a name="geodesic-path"></a>**Geodesic pad**: het kortste pad tussen twee punten op een gekromd Opper vlak. Wanneer dit pad wordt weer gegeven op Azure Maps, wordt dit weer gegeven als een gekromde lijn als gevolg van de Mercator-projectie.

<a name="geofence"></a>**Geofence**: een gedefinieerde geografische regio die kan worden gebruikt voor het activeren van gebeurtenissen wanneer een apparaat de regio binnenkomt of bestaat.

<a name="geojson"></a>**Geojson**: is een gemeen schappelijke JSON-bestands indeling die wordt gebruikt voor het opslaan van geografische vector gegevens zoals punten, lijnen en veelhoeken. **Opmerking**: in azure Maps wordt een uitgebreide versie van geojson gebruikt, zoals [hier wordt beschreven](extend-geojson.md).

<a name="geometry"></a>**Geometrie**: vertegenwoordigt een ruimtelijke object, zoals een punt, lijn of veelhoek.

<a name="geometrycollection"></a>**GeometryCollection**: een verzameling Geometry-objecten.

<a name="geopol"></a>**GeoPol**: verwijst naar geopolitieke gevoelige gegevens, zoals betwiste randen en namen van locaties.

<a name="georeference"></a>**Georeferentie**: het proces van het afstemmen van geografische gegevens of installatie kopieën naar een bekend coördinaten systeem. Dit proces kan bestaan uit het verplaatsen, draaien, schalen of scheef trekken van de gegevens.

<a name="georss"></a>**GeoRSS**: een XML-extensie voor het toevoegen van ruimtelijke gegevens aan RSS-feeds.

<a name="gis"></a>**GIS**: een acroniem voor "geografisch informatie systeem". Een algemene term die wordt gebruikt om de toewijzings branche te beschrijven.

<a name="gml"></a>**GML**: ook wel bekend als de taal voor geografische markeringen. Een XML-bestands extensie voor het opslaan van ruimtelijke gegevens.

<a name="gps"></a>**GPS**: ook wel bekend als Global Positioning System, is een systeem van satellieten dat wordt gebruikt om de positie van apparaten op de aarde te bepalen. De banen satellieten verzenden signalen waarmee een GPS-ontvanger overal op de aarde een eigen locatie kan berekenen via trilateration.

<a name="gpx"></a>**GPX**: ook bekend als GPS Exchange-indeling, is een XML-bestands indeling die meestal wordt gemaakt op basis van GPS-apparaten.  

<a name="great-circle-distance"></a>**Uitstekende cirkel afstand**: de kortste afstand tussen twee punten op het Opper vlak van een bol.

<a name="greenwich-mean-time-gmt"></a>**Greenwich Mean Time (GMT)**: de tijd op de Prime meridiaan, die wordt uitgevoerd via de Royal-post in GMT, England.

<a name="guid"></a>**GUID**: een Globally Unique Identifier. Een teken reeks die wordt gebruikt om een unieke identificatie van een interface, klasse, type bibliotheek, onderdeel categorie of record op te geven.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Haversine formule**: een algemene vergelijking die wordt gebruikt voor het berekenen van de afstand tussen twee punten op een bol.

<a name="hd-maps"></a>**HD-kaarten**: ook wel bekend als high-definition Maps, bestaat uit hoogwaardige wegnetwerk informatie, zoals Lane-markeringen, ondertekening en richtingaanwijzers die vereist zijn voor autonoom rijden.

<a name="heading"></a>**Kop**: de richting waarin wordt verwezen naar of gericht is. Zie ook [.](#heading)

<a name="heatmap"></a>**Heatmap**: een gegevens visualisatie waarin een reeks kleuren de dichtheid van punten in een bepaald gebied vertegenwoordigen. Zie ook thematische kaart.

<a name="hybrid-imagery"></a>**Hybride installatie kopie**: satelliet-of lucht afbeelding met weg gegevens en labels boven op de afbeelding.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: een acroniem voor de Internet Assigned Numbers Authority. Een non-profit groep die de toewijzing van globale IP-adressen overziet.

<a name="isochrone"></a>**Isochrone**: een isochrone definieert het gebied waarin iemand binnen een bepaalde tijd kan reizen voor een transport modus in elke richting van een bepaalde locatie. Zie ook [bereikbaar bereik](#reachable-range).

<a name="isodistance"></a>**Isodistance**: op basis van een locatie definieert een isochrone het gebied waarin iemand binnen een bepaalde afstand voor een transport methode in een wille keurige richting kan reizen. Zie ook [bereikbaar bereik](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: ook wel bekend als de taal voor het opmaken van een vector, is een algemene XML-bestands indeling voor het opslaan van geografische gegevens zoals punten, lijnen en veelhoeken. 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**: MultiSpectral, door NASA ontwikkelde satellieten die installatie kopieën van gronden verzamelen. Deze installatie kopie wordt gebruikt in veel branches, zoals land bouw, bosbouw en Cartography.

<a name="latitude"></a>**Latitude**: de hoek afstand gemeten in graden van de gelijkheid in een Noord-of Zuid-richting.

<a name="level-of-detail"></a>**Detail niveau**: Zie zoom niveau.

<a name="lidar"></a>**LIDAR**: een acroniem voor lichte detectie en bereik. Een techniek voor externe detectie die gebruikmaakt van lasers om afstanden te meten naar reflecterende Opper vlakken.

<a name="linear-interpolation"></a>**Lineaire interpolatie**: de schatting van een onbekende waarde met behulp van de lineaire afstand tussen bekende waarden.

<a name="linestring"></a>**Lines Tring**: een geometrie die wordt gebruikt om een lijn aan te duiden. Ook wel een poly lijn genoemd. 

<a name="localization"></a>**Lokalisatie**: ondersteuning voor verschillende talen en cult uren.

<a name="logistics"></a>**Logistiek**: het proces van het verplaatsen van personen, voer tuigen, leveringen of activa op een gecoördineerde manier.

<a name="longitude"></a>**Lengte graad**: de afstand van de hoek gemeten in graden van de Prime meridiaan in een Oost-of-West-richting.

## <a name="m"></a>M

<a name="map-tile"></a>**Kaart tegel**: een rechthoekige afbeelding die een partitie van een kaart doek vertegenwoordigt. Zie de [documentatie voor zoom niveaus en tegel raster](zoom-levels-and-tile-grid.md)voor meer informatie.

<a name="marker"></a>**Markering**: ook wel een pincode of Punaise genoemd, is een pictogram dat een punt locatie op een kaart vertegenwoordigt.

<a name="mercator-projection"></a>**Mercator-projectie**: een cilindrische kaart projectie die de standaard kaart projectie voor zeemijl is geworden vanwege de mogelijkheid om regels van constante cursus, ook wel rhumb-lijnen, te vertegenwoordigen als rechte segmenten waarmee de hoeken met de meridianen worden bewaard. Alle vlakke kaart projecties vervormen de vormen of grootten van de kaart in vergelijking met de werkelijke indeling van het Opper vlak van de aarde. De Mercator-projectie exaggerates gebieden die ver van de vergelijkend zijn, zodat kleinere gebieden groter worden weer gegeven op de kaart wanneer u de palen benadert. 

<a name="multilinestring"></a>**Multi line string**: een geometrie die een verzameling Lines Tring-objecten vertegenwoordigt. 

<a name="multipoint"></a>**Multi point**: een geometrie die een verzameling punt objecten vertegenwoordigt.

<a name="multipolygon"></a>**Multiveelhoek**: een geometrie die een verzameling veelhoek objecten vertegenwoordigt. Als u bijvoorbeeld de grens van Hawaï wilt weer geven, wordt elk eiland beschreven met een veelhoek. De grens van Hawaï zou dus een multiveelhoek zijn.

<a name="municipality"></a>**Gemeente**: een stad of stad. 

<a name="municipality-subdivision"></a>**Gemeentelijke deelvak**: een deelvak van een gemeente, zoals een groep of een naam van een lokaal gebied, zoals ' centrum '.

## <a name="n"></a>N

<a name="navigation-bar"></a>**Navigatie balk**: de set besturings elementen in een kaart die wordt gebruikt voor het aanpassen van het zoom niveau, de hoogte, de draaiing en de omschakeling van de laag van de basis kaart.

<a name="nearby-search"></a>**Zoek naar** in de buurt: een ruimtelijke query die een vaste lineaire afstand (als de hanen, vliegt) vanaf een punt doorzoekt.

<a name="neutral-ground-truth"></a>**Onpartijdige waarheid**: een kaart die labels weergeeft in de officiële taal van de regio die het vertegenwoordigt en in lokale scripts, indien beschikbaar.

## <a name="o"></a>O

<a name="origin"></a>**Oorsprong**: een begin punt of locatie waar een gebruiker zich bevindt.

## <a name="p"></a>P

<a name="panning"></a>**Pannen**: het proces van het verplaatsen van de kaart in een wille keurige richting en het behoud van een constant zoom niveau.

<a name="parcel"></a>**Perceel**: een waarnemings punt of grens van een eigenschap.

<a name="pitch"></a>**Pitch**: de hoeveelheid kanteling van de kaart ten opzichte van de verticale plaats op de kaart.

<a name="point"></a>**Punt**: een geometrie die een enkele positie op de kaart vertegenwoordigt. 

<a name="points-of-interest-poi"></a>**Points of Interest (POI)**: een bedrijf, oriëntatie punt of gemeen schappelijke plaats van belang.

<a name="polygon"></a>**Veelhoek**: een effen geometrie die een gebied op een kaart vertegenwoordigt. 

<a name="polyline"></a>**Poly lijn**: een geometrie die wordt gebruikt om een lijn aan te duiden. Ook wel bekend als een Lines Tring. 

<a name="position"></a>**Positie**: de lengte graad, breedte graad en hoogte (x, y, z-coördinaten) van een punt.

<a name="post-code"></a>**Post code**: Zie [Post code](#postal-code).

<a name="postal-code"></a>**Post code**: een reeks letters of cijfers, of beide, in een specifieke indeling. De post code wordt gebruikt door de post dienst van een land/regio om geografische gebieden in zones te verdelen, zodat de levering van e-mail wordt vereenvoudigd.

<a name="primary-key"></a>**Primaire sleutel**: de eerste van twee abonnements sleutels die zijn verschaft voor Azure Maps gedeelde sleutel verificatie. Zie [verificatie van de gedeelde sleutel](#shared-key-authentication).

<a name="prime-meridian"></a>**Prime meridiaan**: een lijn met een lengte graad die een lengte van 0 graden aangeeft. Over het algemeen zijn de lengte waarden kleiner wanneer ze in een Westerly-richting tot 180 graden worden gereisd en verg Roten wanneer ze in een Paas richting naar-180-graden reizen. 

<a name="prj"></a>**PRJ**: een tekst bestand dat vaak vergezeld gaat van een vorm bestand-bestand dat informatie bevat over het geprojecteerde coördinaten systeem waarin de gegevensset zich bevindt.

<a name="projection"></a>**Projectie**: een geprojecteerd coördinaten systeem op basis van een kaart projectie, zoals transversale Mercator, Albers equally Area en Robinson. Dit biedt de mogelijkheid om kaarten van het bolvormige vlak van de aarde te projecteren op een tweedimensionale Cartesisch coördinaten vlak. Geprojecteerde coördinaten systemen worden soms een kaart projectie genoemd.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**: een base-4-adres index voor een tegel binnen een quadtree-systeem. Zie [zoom niveaus en tegel grid](zoom-levels-and-tile-grid.md) -documentatie voor meer informatie.

<a name="quadtree"></a>**Quadtree**: een gegevens structuur waarin elk knoop punt precies vier onderliggende items heeft. Het tegel systeem dat in Azure Maps wordt gebruikt, maakt gebruik van een quadtree-structuur, zoals een gebruiker op één niveau zoomt, waarbij elke kaart tegel in vier subtegels wordt onderverdeeld.  Zie [zoom niveaus en tegel grid](zoom-levels-and-tile-grid.md) -documentatie voor meer informatie.

<a name="queries-per-second-qps"></a>**Query's per seconde (qps)**: het aantal query's of aanvragen dat binnen één seconde kan worden gemaakt aan een service of platform. 

## <a name="r"></a>R

<a name="radial-search"></a>**Radiaal zoekactie**: een ruimtelijke query die een vaste lineaire afstand (als de hanen en andere) doorzoekt vanaf een punt. 

<a name="raster-data"></a>**Raster gegevens**: een matrix met cellen (of pixels) die zijn ingedeeld in rijen en kolommen (of een raster) waarbij elke cel een waarde bevat, zoals de Tempe ratuur. Rasters bevatten digitale lucht Foto's, afbeelding van satellieten, digitale afbeeldingen en gescande kaarten.

<a name="raster-layer"></a>**Raster laag**: een tegel laag die uit raster afbeeldingen bestaat.

<a name="reachable-range"></a>Bereikbaar **bereik**: een bereikbaar bereik definieert het gebied waarin iemand binnen een opgegeven tijd of afstand kan reizen, voor een transport wijze, in elke richting van een locatie. Zie ook [isochrone](#isochrone) en [Isodistance](#isodistance).

<a name="remote-sensing"></a>**Externe detectie**: het proces van het verzamelen en interpreteren van sensor gegevens van een afstand.

<a name="rest-service"></a>**Rest-service**: de acroniem rest staat voor een representatieve status overdracht. Een REST-service is een URL-gebaseerde webservice die gebruikmaakt van de eenvoudige webtechnologie om te communiceren, de meest voorkomende methoden om HTTP GET-en POST-aanvragen te verzenden. Deze typen services worden vaak veel sneller en kleiner dan traditionele op SOAP gebaseerde services.

<a name="reverse-geocode"></a>**Omgekeerde Geocode**: het proces van het nemen van een coördinaat en het bepalen van het adres dat op een kaart staat.

<a name="reproject"></a>Opnieuw **projecteren**: Zie [trans formatie](#transformation).

<a name="rest-service"></a>**Rest-service**: een acroniem voor representatieve status overdracht. Een architectuur voor het uitwisselen van gegevens tussen peers in een gecentraliseerde, gedistribueerde omgeving. REST Hiermee kunnen Program ma's op verschillende computers onafhankelijk van een besturings systeem of platform communiceren. Een service kan een Hypertext Transfer Protocol (HTTP)-aanvraag verzenden naar een URL (Uniform Resource Locator) en gegevens terughalen.

<a name="route"></a>**Route**: een pad tussen twee of meer locaties, dat ook aanvullende informatie kan bevatten, zoals instructies voor waypoints over de route.

<a name="requests-per-second-rps"></a>**Aanvragen per seconde (RPS)**: Zie [query's per seconde (qps)](#queries-per-second-qps). 

<a name="rss"></a>**RSS**: acroniem voor een echt eenvoudige syndicatie, een RDF-site samen vatting (Resource Description Framework) of een uitgebreid site overzicht, afhankelijk van de bron. Een eenvoudige, gestructureerde XML-indeling voor het delen van inhoud tussen verschillende websites. RSS-documenten bevatten meta gegevenselementen zoals auteur, datum, titel, korte beschrijving en een hypertekst koppeling. Deze informatie helpt een gebruiker (of een RSS-uitgever service) om te bepalen welke materialen verder moeten worden onderzocht.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Satelliet installatie kopie**: afbeelding die is vastgelegd door plannen en satellieten die naar beneden wijzen.

<a name="secondary-key"></a>**Secundaire sleutel**: de tweede van twee abonnements sleutels die zijn verschaft voor Azure Maps gedeelde sleutel verificatie. Zie [verificatie van de gedeelde sleutel](#shared-key-authentication).

<a name="shapefile-shp"></a>**Vorm bestand (SHP)**: ook wel ESRI vorm bestand genoemd, is een indeling voor vector gegevens opslag voor het opslaan van de locatie, vorm en kenmerken van geografische functies. Een vorm bestand wordt opgeslagen in een set gerelateerde bestanden.

<a name="shared-key-authentication"></a>**Gedeelde sleutel verificatie**: gedeelde sleutel verificatie is afhankelijk van het door geven van Azure Maps accounts gegenereerde sleutels met elke aanvraag naar Azure Maps. Deze sleutels worden vaak abonnements sleutels genoemd. Het wordt aanbevolen dat sleutels regel matig opnieuw worden gegenereerd voor beveiliging. Er worden twee sleutels gegeven, zodat u verbindingen met één sleutel kunt onderhouden tijdens het opnieuw genereren van de andere. Als u de sleutels opnieuw genereert, dient u elke toepassing bij te werken die toegang heeft tot dit account om de nieuwe sleutels te kunnen gebruiken. Zie [Azure Maps en Azure AD](azure-maps-authentication.md) en [verificatie beheren in azure Maps](how-to-manage-authentication.md)voor meer informatie over Azure Maps-verificatie.

<a name="software-development-kit-sdk"></a>**Software Development Kit (SDK)**: een verzameling documentatie, voorbeeld code en voor beeld-apps om een ontwikkelaar te helpen bij het maken van apps met behulp van een API.

<a name="spherical-mercator-projection"></a>**Bolvormige Mercator-projectie**: Zie [Web Mercator](#web-mercator). 

<a name="spatial-query"></a>**Ruimtelijke query**: een aanvraag voor een service die een ruimtelijke bewerking uitvoert. Zoals een radiaal zoekactie of via een zoek opdracht in de route.

<a name="spatial-reference"></a>**Ruimtelijke verwijzing**: een lokaal, regionaal of globaal systeem op basis van coördinaten dat wordt gebruikt om geografische entiteiten nauw keurig te vinden. Hiermee wordt het coördinaten systeem gedefinieerd dat wordt gebruikt om kaart coördinaten te koppelen aan locaties in de praktijk. Ruimtelijke verwijzingen zorgen ervoor dat ruimtelijke gegevens uit verschillende lagen of bronnen kunnen worden geïntegreerd voor nauw keurige weer gave of analyse. Azure Maps maakt gebruik van het referentie systeem [EPSG: 3857](https://epsg.io/3857) coördinaten en WGS 84 voor het invoeren van geometrie gegevens.

<a name="sql-spatial"></a>**Ruimtelijke** bewaarde SQL: verwijst naar de ruimtelijke functionaliteit die is ingebouwd in SQL Azure en SQL Server 2008 en hoger. Deze ruimtelijke functionaliteit is ook beschikbaar als een .NET-bibliotheek die onafhankelijk van SQL Server kan worden gebruikt. Zie de [documentatie over ruimtelijke gegevens (SQL Server)](/sql/relational-databases/spatial/spatial-data-sql-server) voor meer informatie.

<a name="subscription-key"></a>**Abonnements sleutel**: Zie [gedeelde sleutel verificatie](#shared-key-authentication).

<a name="synchronous-request"></a>**Synchrone aanvraag**: een HTTP-aanvraag opent een verbinding en wacht op een reactie. Browsers beperken het aantal gelijktijdige HTTP-aanvragen dat vanaf een pagina kan worden gemaakt. Als er tegelijkertijd meerdere langdurige synchrone aanvragen worden gedaan, kan deze limiet worden bereikt. Aanvragen worden uitgesteld totdat een van de andere aanvragen is voltooid.

## <a name="t"></a>T

<a name="telematics"></a>**Telematica**: het verzenden, ontvangen en opslaan van informatie via telecommunicatie apparaten in combi natie met invloed op externe objecten. 

<a name="temporal-data"></a>**Tijdelijke gegevens**: gegevens die specifiek verwijzen naar tijden of datums. Tijdelijke gegevens kunnen verwijzen naar discrete gebeurtenissen, zoals bliksem aantekeningen; objecten verplaatsen, zoals treinen; of herhaalde waarnemingen, zoals aantallen van Traffic Sens oren.

<a name="terrain"></a>**Terrein**: een gebied van land dat een bepaald kenmerk heeft, zoals zand of berg terrein.

<a name="thematic-maps"></a>**Thematische kaarten**: een thematische kaart is een eenvoudige kaart die is gemaakt op basis van een thema over een geografisch gebied. Een veelvoorkomend scenario voor dit type kaart is het kleuren van de administratieve regio's, zoals landen/regio's, op basis van een aantal gegevens waarden.

<a name="tile-layer"></a>**Laag van Tegel**: een laag die wordt weer gegeven door kaart tegels (rechthoekige secties) samen te stellen in een doorlopende laag. De tegels zijn raster afbeeldings tegels of vector tegels. Raster tegel lagen worden doorgaans vooraf weer gegeven en worden als installatie kopieën op een server opgeslagen. Raster tegel lagen kunnen gebruikmaken van een grote opslag ruimte. Vector tegel lagen worden bijna in realtime weer gegeven binnen de client toepassing. De opslag vereisten aan de server zijde zijn dus kleiner voor vector tegel lagen.

<a name="time-zone"></a>**Tijd zone**: een gebied van de wereld met een uniforme standaard tijd voor juridische, commerciële en sociale doel einden. Tijd zones volgen doorgaans de grenzen van landen/regio's en hun onderverdelingen.

<a name="transaction"></a>**Trans actie**: Azure Maps gebruikt een transactionele licentie model waarbij;

- Er wordt één trans actie gemaakt voor elke 15 kaart of de gevraagde verkeers tegels.
- Er wordt één trans actie gemaakt voor elke API-aanroep naar een van de services in Azure Maps. Zoeken en route ring zijn voor beelden van Azure Maps service.

<a name="transformation"></a>**Trans formatie**: het proces van het converteren van gegevens tussen verschillende geografische coördinaten systemen. U kunt bijvoorbeeld een aantal gegevens hebben die zijn vastgelegd in het Verenigd Konink rijk en op basis van het OSGB 1936 geografische coördinaten systeem. Azure Maps maakt gebruik van de [EPSG: 3857](https://epsg.io/3857) -coördinaten referentie systeem variant van WGS84. Om de gegevens op de juiste manier weer te geven, moet de coördinaten van het ene naar het andere systeem worden getransformeerd.

<a name="traveling-salesmen-problem-tsp"></a>**Traveling salesmen-probleem (TSP)**: een Hamiltonian-circuit probleem waarbij een verkoper de meest efficiënte manier moet vinden om een reeks stops te bezoeken en vervolgens terug te keren naar de start locatie.  

<a name="trilateration"></a>**Trilateration**: het proces van het bepalen van de positie van een punt op het Opper vlak van de aarde, met betrekking tot twee andere punten, door de afstand tussen de drie punten te meten.

<a name="turn-by-turn-navigation"></a>**Scha kelen tussen navigatie**: een toepassing die route-instructies biedt voor elke stap van een route wanneer de gebruikers de volgende bewegings nadert.

## <a name="v"></a>V

<a name="vector-data"></a>**Vector gegevens**: gegevens op basis van een coördinaat die als punten, lijnen of veelhoeken worden weer gegeven.

<a name="vector-tile"></a>**Vector tegel**: een open gegevens specificatie voor het opslaan van georuimtelijke vector gegevens met hetzelfde tegel systeem als het kaart besturings element. Zie ook [laag voor tegels](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**Route ring van het Voer tuig (VRP)**: een klasse van problemen, waarbij een reeks bestelde routes voor een vloot van Voer tuigen wordt berekend, waarbij rekening wordt gehouden met de beperkingen. Deze beperkingen kunnen betrekking hebben op de bezorgings tijd Windows, meerdere route capaciteiten en beperkingen voor de reis duur.

<a name="voronoi-diagram"></a>**Voronoi diagram**: een schijf ruimte in gebieden, of cellen, die een reeks geometrische objecten, doorgaans punt functies, omring. Deze cellen, of veelhoeken, moeten voldoen aan de criteria voor Delaunay-drie hoeken. Alle locaties binnen een gebied bevinden zich dichter bij het object dat wordt omgeven door een ander object in de set. Voronoi-diagrammen worden vaak gebruikt om gebieden van invloed op geografische functies af te bakenen. 

## <a name="w"></a>W

<a name="waypoint"></a>**Waypoint**: een waypoint is een opgegeven geografische locatie die wordt gedefinieerd door lengte graad en breedte graad die wordt gebruikt voor navigatie doeleinden. Wordt vaak gebruikt om een punt weer te geven waarin iemand naar een route navigeert.

<a name="waypoint-optimization"></a>**Waypoint optimalisatie**: het proces waarbij een set van waypoints wordt gewijzigd om de reis tijd of de afstand die nodig is voor het door geven van alle opgegeven waypoints, te minimaliseren. Afhankelijk van de complexiteit van de optimalisatie wordt deze optimalisatie vaak aangeduid als het [Traveling salesmen-probleem](#traveling-salesmen-problem-tsp) of het [routerings probleem van het Voer tuig](#vehicle-routing-problem-vrp).

<a name="web-map-service-wms"></a>**Web map service (WMS)**: WMS is een open geografische Consortium (OGC) die op afbeeldingen gebaseerde kaart Services definieert. WMS-services bieden installatie kopieën voor specifieke gebieden binnen een kaart op aanvraag. Afbeeldingen bevatten vooraf gerenderde symbology en kunnen worden weer gegeven in een van de verschillende benoemde stijlen, indien gedefinieerd door de service.

<a name="web-mercator"></a>**Web-Mercator**: ook wel bolvormig Mercator-projectie genoemd. Het is een lichte variant van de Mercator-projectie, een die hoofd zakelijk wordt gebruikt in webtoewijzings Programma's. Het maakt gebruik van dezelfde formules als de standaard Mercator-projectie die wordt gebruikt voor kleinschalige kaarten. De Mercator maakt echter gebruik van de bolvormige formules op alle schalen, maar grootschalige Mercator-kaarten gebruiken normaal gesp roken de Ellipsoidal vorm van de projectie. Het verschil is onmerkbaar op de wereld wijde schaal, maar het zorgt ervoor dat kaarten van lokale gebieden iets afwijkt van echte Ellipsoidal Mercator Maps, op dezelfde schaal.

<a name="wgs84"></a>**WGS84**: een set constanten die wordt gebruikt om ruimtelijke coördinaten te koppelen aan locaties op het Opper vlak van de kaart. De datum WGS84 is de standaard die wordt gebruikt door de meeste online toewijzings providers en GPS-apparaten. Azure Maps maakt gebruik van de [EPSG: 3857](https://epsg.io/3857) -coördinaten referentie systeem variant van WGS84.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Z-coördinaat**: Zie [hoogte](#altitude). 

<a name="zip-code"></a>**Post code**: Zie [Post code](#postal-code).

<a name="Zoom level"></a>**Zoom niveau**: Hiermee geeft u het detail niveau en het gedeelte van de kaart weer gegeven. Wanneer u helemaal ingezoomd op het niveau van 0, is de volledige wereld kaart vaak zichtbaar. Maar in de kaart worden de beperkte gegevens weer gegeven, zoals namen van land/regio, randen en Oceaan. Wanneer u inzoomt op niveau 17, wordt in de kaart een gebied van een aantal plaatsings blokken weer gegeven met gedetailleerde informatie over de weg. In Azure Maps is het hoogste zoom niveau 22. Zie de documentatie voor [zoom niveaus en tegel raster](zoom-levels-and-tile-grid.md) voor meer informatie.