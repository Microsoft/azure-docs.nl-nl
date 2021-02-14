---
title: 'Zelfstudie: webservices migreren vanuit Google Maps | Microsoft Azure Maps'
description: Zelfstudie over het migreren van webservices vanuit Google Maps naar Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 04bbb0465f26e82d247c53e7393c4476b9d36fde
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386846"
---
# <a name="tutorial-migrate-web-service-from-google-maps"></a>Zelfstudie: Webservice migreren vanuit Google Maps

Zowel Azure Maps als Google Maps biedt toegang tot ruimtelijke API's via REST-webservices. Met de API-interfaces van deze platforms worden vergelijkbare functies uitgevoerd. Er worden echter verschillende naamconventies en antwoordobjecten voor gebruikt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Voorwaartse en achterwaartse geocodering
> * Zoeken naar nuttige plaatsen
> * Routes en routebeschrijvingen berekenen
> * Een kaartafbeelding ophalen
> * Een afstandsmatrix berekenen
> * Tijdzone-informatie ophalen

U leert ook het volgende:

> [!div class="checklist"]
> * Welk Azure Maps REST-service wanneer u van een Google Maps-webservice migreert
> * Tips om het meeste uit Azure Maps-services te halen
> * Inzicht in andere gerelateerde Azure Maps-services

De tabel bevat de service-API's van Azure Maps, die een vergelijkbare functionaliteit hebben als de vermelde service-API's van Google Maps.

| Service-API van Google Maps | Service-API van Azure Maps                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Aanwijzingen              | [Route](/rest/api/maps/route)                                     |                         
| Afstandsmatrix         | [Routematrix](/rest/api/maps/route/postroutematrixpreview)       |                         
| Geocodering               | [Zoeken](/rest/api/maps/search)                                   |                         
| Locaties zoeken           | [Zoeken](/rest/api/maps/search)                                   |                         
| Locatie automatisch aanvullen      | [Zoeken](/rest/api/maps/search)                                   |                         
| Uitlijnen op weg            | Zie de sectie [Routes en routebeschrijvingen berekenen](#calculate-routes-and-directions).            
| Snelheidslimieten            | Zie de sectie [Reverse geocodering van een coördinaat](#reverse-geocode-a-coordinate).                  
| Statische kaart              | [Weergeven](/rest/api/maps/render/getmapimage)                       |                         
| Tijdzone               | [Tijdzone](/rest/api/maps/timezone)                              |                         
| Terrein               | [Terrein (preview)](/rest/api/maps/elevation)                   |                         |

De volgende service-API's zijn momenteel niet beschikbaar in Azure Maps:

- Geolocatie-Azure Maps heeft een service met de naam geolocatie, maar biedt ook een IP-adres voor locatie-informatie, maar ondersteunt momenteel geen ondersteuning voor de triangulatie van een celrand of WiFi.
- Details en foto's van plaatsen: telefoonnummers en website-URL zijn beschikbaar in de zoek-API van Azure Maps.
- Kaart-URL's
- Dichtstbijzijnde wegen - dit is te realiseren via de web-SDK, zoals [hier](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic) te zien is, maar dit is momenteel niet beschikbaar als een service.
- Statische straatweergave

Azure Maps heeft verschillende aanvullende REST-webservices die interessant kunnen zijn:

- [Ruimtelijke bewerkingen](/rest/api/maps/spatial): Complexe ruimtelijke berekeningen en bewerkingen, zoals geofencing, offloaden naar een service.
- [Verkeer](/rest/api/maps/traffic): Toegang tot realtime verkeersstromen en incidentgegevens.

## <a name="prerequisites"></a>Vereisten

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
2. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel. Zie [Verificatie beheren in Azure Maps](how-to-manage-authentication.md) voor meer informatie over verificatie in Azure Maps.

## <a name="geocoding-addresses"></a>Geocodering van adressen

Geocodering is het proces van het omzetten van een adres in een coördinaat. 1 Microsoft way, Redmond, WA wordt bijvoorbeeld omgezet in lengtegraad -122.1298 en breedtegraad 47.64005. Coördinaten kunnen vervolgens worden gebruikt voor verschillende soorten doelen, zoals het plaatsen van een markering op een kaart.

Azure Maps biedt verschillende methoden voor geocodering van adressen:

- [**Vrije adresgeocodering**](/rest/api/maps/search/getsearchaddress): Een enkele adrestekenreeks opgeven en de aanvraag direct verwerken. 1 Microsoft way, Redmond, WA is een voorbeeld van een enkele adrestekenreeks. Deze API wordt aanbevolen voor snelle geocodering van afzonderlijke adressen.
- [**Gestructureerde adresgeocodering**](/rest/api/maps/search/getsearchaddressstructured): De onderdelen van een enkel adres opgeven, zoals straatnaam, plaats, land/regio en postcode en de aanvraag direct verwerken. Deze API wordt aanbevolen voor snelle geocodering van afzonderlijke adressen die al zijn geparseerd in afzonderlijke adresonderdelen.
- [**Batchmatige adresgeocodering**](/rest/api/maps/search/postsearchaddressbatchpreview): Een aanvraag met maximaal 10.000 adressen maken en deze in de loop van de tijd laten verwerken. Geocodering van alle adressen vindt parallel plaats op de server en als dit proces is voltooid, kan de volledige resultatenset worden gedownload. Dit wordt aanbevolen voor geocodering van grote gegevenssets.
- [**Fuzzy zoekopdrachten**](/rest/api/maps/search/getsearchfuzzy): Met deze API wordt adresgeocodering gecombineerd met het zoeken naar nuttige plaatsen. Deze API maakt gebruik van een vrije tekenreeks. Deze tekenreeks kan een adres, plaats, oriëntatiepunt, nuttige plaats of nuttigeplaatscategorie zijn. Met deze API wordt de aanvraag in vrijwel realtime verwerkt. Deze API wordt aanbevolen wanneer gebruikers in hetzelfde tekstvak naar adressen of nuttige plaatsen zoeken.
- [**Batchmatige fuzzy zoekopdrachten**](/rest/api/maps/search/postsearchfuzzybatchpreview): Een aanvraag maken met maximaal 10.000 adressen, plaatsen, oriëntatiepunten of nuttige plaatsen en deze in de loop van de tijd laten verwerken. Alle gegevens worden parallel op de server verwerkt en als dit proces is voltooid, kan de volledige resultatenset worden gedownload.

De volgende tabel bevat de API-parameters in Google Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Google Maps | Vergelijkbare API-parameter in Azure Maps  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` en `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` -plaats/stad<br/>`municipalitySubdivision` - omgeving, sub-/superplaats<br/>`countrySubdivision` -staat of provincie<br/>`countrySecondarySubdivision` - gemeente<br/>`countryTertiarySubdivision` - district<br/>`countryCode` - land-/regiocode van twee letters |
| `key`                       | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](azure-maps-authentication.md). |
| `language`                  | `language` - zie de documentatie [Ondersteunde talen](supported-languages.md).  |
| `region`                    | `countrySet`                       |

Een voorbeeld van het gebruik van de zoekservice is [hier](how-to-search-for-address.md) gedocumenteerd. Lees de [Aanbevolen procedures voor zoeken](how-to-use-best-practices-for-search.md).

> [!TIP]
> De API's voor vrije adresgeocodering en fuzzy zoekopdrachten kunnen worden gebruikt in de modus Automatisch aanvullen door `&typeahead=true` toe te voegen aan de aanvraag-URL. Hiermee wordt de server geïnformeerd dat de invoertekst waarschijnlijk gedeeltelijk is en wordt de zoekopdracht in de voorspellende modus uitgevoerd.

## <a name="reverse-geocode-a-coordinate"></a>Reverse geocodering van een coördinaat

Reverse geocodering is het proces van het converteren van geografische coördinaten naar een nabijgelegen adres. Coördinaten met lengtegraad -122,1298 en breedtegraad 47.64005 worden geconverteerd naar 1 Microsoft way Redmond, WA.

Azure Maps biedt verschillende methoden voor reverse geocodering:

- [**Reverse geocodering van adressen**](/rest/api/maps/search/getsearchaddressreverse): Een enkele geografische coördinaat opgeven om het nabijgelegen adres te verkrijgen dat overeenkomt met deze coördinaat. De aanvraag wordt vrijwel realtime verwerkt.
- [**Reverse geocodering van zijstraten**](/rest/api/maps/search/getsearchaddressreversecrossstreet): Een enkele geografische coördinaat opgeven om informatie over nabijgelegen zijstraten op te halen en de aanvraag direct verwerken. U kunt bijvoorbeeld de zijstraten 1st Ave en Main St. verkrijgen.
- [**Batchmatige reverse geocoding van adressen**](/rest/api/maps/search/postsearchaddressreversebatchpreview): Een aanvraag met maximaal 10.000 coördinaten maken en deze in de loop van de tijd laten verwerken. Alle gegevens worden parallel verwerkt op de server. Als de aanvraag is voltooid, kunt u de volledige set resultaten downloaden.

Deze tabel bevat de API-parameters in Google Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Google Maps   | Vergelijkbare API-parameter in Azure Maps   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](azure-maps-authentication.md). |
| `language`                  | `language` - zie de documentatie [Ondersteunde talen](supported-languages.md).  |
| `latlng`                    | `query`  |
| `location_type`             | *N.v.t.*     |
| `result_type`               | `entityType`    |

Lees de [Aanbevolen procedures voor zoeken](how-to-use-best-practices-for-search.md).

De API voor reverse geocodering van Azure Maps biedt enkele aanvullende functies, die niet beschikbaar zijn in Google Maps. Deze functies kunnen nuttig zijn om in uw toepassing te integreren als u uw app migreert:

* Snelheidslimietgegevens ophalen
* Informatie over het weggebruik ophalen: lokale weg, hoofdweg, beperkte toegang, helling enzovoort
* De kant van de straat ophalen waar zich een coördinaat bevindt

## <a name="search-for-points-of-interest"></a>Zoeken naar nuttige plaatsen

In Google Maps kan naar nuttige plaatsen worden gezocht met behulp van de API Locaties zoeken. Deze API biedt drie verschillende manieren om te zoeken naar nuttige plaatsen:

* **Locatie zoeken op basis van tekst:** Hiermee wordt gezocht naar een nuttige plaats op basis van de naam, het adres of het telefoonnummer ervan.
* **In de buurt zoeken**: Hiermee wordt naar nuttige plaatsen gezocht die zich binnen een bepaalde afstand van een locatie bevinden.
* **Zoeken op basis van tekst:** Hiermee wordt naar plaatsen gezocht met behulp van een vrije tekst, die een nuttige plaats en een locatie bevat. Bijvoorbeeld pizza in New York of restaurants in de buurt van Main St.

Azure Maps biedt verschillende zoek-API's voor nuttige plaatsen:

- [**Nuttige plaatsen zoeken**](/rest/api/maps/search/getsearchpoi): Nuttige plaatsen zoeken op naam. Voorbeeld: Starbucks.
- [**Nuttigeplaatscategorieën zoeken**](/rest/api/maps/search/getsearchpoicategory): Nuttige plaatsen zoeken op categorie. Bijvoorbeeld: restaurant.
- [**In de buurt zoeken**](/rest/api/maps/search/getsearchnearby): Hiermee wordt naar nuttige plaatsen gezocht die zich binnen een bepaalde afstand van een locatie bevinden.
- [**Fuzzy zoekopdrachten**](/rest/api/maps/search/getsearchfuzzy): Met deze API wordt adresgeocodering gecombineerd met het zoeken naar nuttige plaatsen. Deze API maakt gebruik van een vrije tekenreeks die een adres, plaats, oriëntatiepunt, nuttige plaats of nuttigeplaatscategorie kan zijn. De aanvraag wordt in vrijwel realtime verwerkt. Deze API wordt aanbevolen wanneer gebruikers in hetzelfde tekstvak naar adressen of nuttige plaatsen zoeken.
- [**Zoeken binnen geometrie**](/rest/api/maps/search/postsearchinsidegeometry): Zoeken naar nuttige plaatsen die zich binnen een opgegeven geometrie bevinden. Voorbeeld: zoeken naar een nuttige plaats binnen een veelhoek.
- [**Zoeken langs route**](/rest/api/maps/search/postsearchalongroute): Zoeken naar nuttige plaatsen die zich aan een opgegeven routepad bevinden.
- [**Batchmatige fuzzy zoekopdrachten**](/rest/api/maps/search/postsearchfuzzybatchpreview): Een aanvraag maken met maximaal 10.000 adressen, plaatsen, oriëntatiepunten of nuttige plaatsen. De aanvraag wordt in de loop van de tijd verwerkt. Alle gegevens worden parallel verwerkt op de server. Als de aanvraag is verwerkt, kunt u de volledige set resultaten downloaden.

Momenteel heeft Azure Maps geen API die is te vergelijken met de API Zoeken op basis van tekst in Google Maps.

> [!TIP]
> De API's voor nuttige plaatsen zoeken, nuttigeplaatscategorieën zoeken en fuzzy zoekopdrachten kunnen worden gebruikt in de modus Automatisch aanvullen door `&typeahead=true` toe te voegen aan de aanvraag-URL. Hiermee wordt de server geïnformeerd dat de invoertekst waarschijnlijk gedeeltelijk is. De zoekopdracht wordt in de voorspellende modus uitgevoerd door de API.

Raadpleeg de documentatie [Aanbevolen procedures voor zoeken](how-to-use-best-practices-for-search.md).

### <a name="find-place-from-text"></a>Locatie zoeken op basis van tekst

Gebruik [Nuttige plaatsen zoeken](/rest/api/maps/search/getsearchpoi) en [Fuzzy zoekopdracht](/rest/api/maps/search/getsearchfuzzy) van Azure Maps om nuttige plaatsen te zoeken op naam of adres.

De tabel bevat de API-parameters in Google Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Google Maps | Vergelijkbare API-parameter in Azure Maps |
|---------------------------|-------------------------------------|
| `fields`                  | *N.v.t.*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N.v.t.*                               |
| `key`                     | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](azure-maps-authentication.md). |
| `language`                | `language` - zie de documentatie [Ondersteunde talen](supported-languages.md).  |
| `locationbias`            | `lat`, `lon` en `radius`<br/>`topLeft` en `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>In de buurt zoeken

Gebruik de API voor [in de buurt zoeken](/rest/api/maps/search/getsearchnearby) om nuttige plaatsen op te halen in Azure Maps.

De tabel bevat de API-parameters in Google Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Google Maps | Vergelijkbare API-parameter in Azure Maps  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](azure-maps-authentication.md). |
| `keyword`                   | `categorySet` en `brandSet`        |
| `language`                  | `language` - zie de documentatie [Ondersteunde talen](supported-languages.md).  |
| `location`                  | `lat` en `lon`                     |
| `maxprice`                  | *N.v.t.*                               |
| `minprice`                  | *N.v.t.*                               |
| `name`                      | `categorySet` en `brandSet`        |
| `opennow`                   | *N.v.t.*                               |
| `pagetoken`                 | `ofs` en `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N.v.t.*                               |
| `type`                      | `categorySet –` - zie de documentatie [Ondersteunde zoekcategorieën](supported-search-categories.md).   |

## <a name="calculate-routes-and-directions"></a>Routes en routebeschrijvingen berekenen

Bereken routes en routebeschrijvingen met behulp van Azure Maps. Azure Maps biedt veel dezelfde functies als de routeringsservice van Google Maps, zoals:

* Aankomst- en vertrektijden.
* Realtime verkeersroutes en verkeersroutes op basis van voorspellingen.
* Verschillende transportmodi, zoals met de auto, lopend of met de fiets.

> [!NOTE]
> Voor Azure Maps moeten alle routepunten coördinaten zijn. Adressen moeten eerst worden gegeocodeerd.

De routeringsservice van Azure Maps biedt de volgende API's voor het berekenen van routes:

- [**Route berekenen**](/rest/api/maps/route/getroutedirections): Een route berekenen en de aanvraag direct laten verwerken. Deze API biedt ondersteuning voor zowel GET- als POST-aanvragen. POST-aanvragen worden aanbevolen als er een groot aantal routepunten wordt opgegeven of als er veel routeopties worden gebruikt, om te voorkomen dat de URL-aanvraag te lang wordt en problemen veroorzaakt. De POST-routebeschrijving in Azure Maps bevat een optie waarmee duizenden [ondersteuningspunten](/rest/api/maps/route/postroutedirections#supportingpoints) kunnen worden verwerkt en waartussen een logisch routepad wordt gemaakt (uitlijnen op weg). 
- [**Batchroute**](/rest/api/maps/route/postroutedirectionsbatchpreview): Een aanvraag met maximaal 1.000 routeaanvragen maken en deze in de loop van de tijd laten verwerken. Alle gegevens worden parallel op de server verwerkt en als dit proces is voltooid, kan de volledige resultatenset worden gedownload.
- [**Mobility-services (preview) **](/rest/api/maps/mobility): Routes en routebeschrijvingen berekenen op basis van het openbaar vervoer.

De tabel bevat de API-parameters in Google Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Google Maps    | Vergelijkbare API-parameter in Azure Maps  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` - coördinaten in de indeling `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](azure-maps-authentication.md). |
| `language`                     | `language` - zie de documentatie [Ondersteunde talen](supported-languages.md).   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N.v.t.* - deze functie is gerelateerd aan geocodering. Gebruik de parameter *countrySet* wanneer u de geocoderings-API van Azure Maps gebruikt.  |
| `traffic_model`               | *N.v.t.* - er kan alleen worden opgegeven of er verkeersgegevens moeten worden gebruikt met de parameter *traffic*. |
| `transit_mode`                | Raadpleeg de [documentatie voor Mobility-services (preview)](/rest/api/maps/mobility) |
| `transit_routing_preference` | Raadpleeg de [documentatie voor Mobility-services (preview)](/rest/api/maps/mobility) |
| `units`                        | *N.v.t.* - in Azure Maps wordt alleen het metrische systeem gebruikt.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Met de route-API van Azure Maps wordt standaard alleen een samenvatting geretourneerd. De afstand, de tijden en de coördinaten voor het routepad worden geretourneerd. Gebruik de parameter `instructionsType` om instructies per afslag op te halen. En gebruik de parameter `routeRepresentation` om de samenvatting en het routepad te filteren.

De routerings-API van Azure Maps biedt aanvullende functies die niet beschikbaar zijn in Google Maps. Overweeg het gebruik van deze functies als u uw app migreert. Deze kunnen nuttig voor u zijn.

* Ondersteuning voor de routetypen: kortste, snelste, trilling en zuinigst.
* Ondersteuning voor aanvullende reismodi: bus, motor, taxi, vrachtwagen en busje.
* Ondersteuning voor 150 routepunten.
* Meerdere reistijden berekenen in één aanvraag; historisch verkeer, liveverkeer, geen verkeer.
* Aanvullende wegtypen vermijden: carpoolwegen, onverharde wegen, reeds gebruikte wegen.
* Te vermijden aangepaste gebieden opgeven.
* Stijgingen in de route beperken.
* Route op basis van motorspecificaties. Routes berekenen voor voertuigen met verbrandingsmotor of elektrische voertuigen op basis van motorspecificaties en de resterende brandstof of acculading.
* Ondersteuning voor routeparameters van commerciële voertuigen, zoals de afmetingen, het gewicht, het aantal assen en het ladingtype van voertuigen.
* De maximale voertuigsnelheid opgeven.

Daarnaast ondersteunt de Route-service in Azure Maps [het berekenen van routeerbare bereiken](/rest/api/maps/route/getrouterange). Berekeningen van routeerbare bereiken worden ook wel isochronen genoemd. Dit omvat het genereren van een veelhoek boven een gebied dat in elke richting vanuit een startpunt kan worden bereisd. En dat binnen een opgegeven tijdsduur of een bepaalde hoeveelheid brandstof of acculading.

Raadpleeg de documentatie [Best practices voor routering](how-to-use-best-practices-for-routing.md).

## <a name="retrieve-a-map-image"></a>Een kaartafbeelding ophalen

Azure Maps biedt een API voor het weergeven van de statische kaartafbeeldingen waarbij gegevens boven elkaar worden weergegeven. De API [Kaartafbeelding weergeven](/rest/api/maps/render/getmapimagerytile) in Azure Maps is vergelijkbaar met de API Statische kaart in Google Maps.

> [!NOTE]
> Voor Azure Maps moeten het midden, alle markeringen en de padlocaties coördinaten in de indeling lengtegraad,breedtegraad zijn. In Google Maps wordt daarentegen de indeling breedtegraad,lengtegraad gebruikt. Adressen moeten eerst een geocodering krijgen.

De tabel bevat de API-parameters in Google Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Google Maps | Vergelijkbare API-parameter in Azure Maps  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` - opgegeven als onderdeel van het URL-pad. Op dit moment wordt alleen PNG ondersteund. |
| `key`                       | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](azure-maps-authentication.md). |
| `language`                  | `language` - zie de documentatie [Ondersteunde talen](supported-languages.md).  |
| `maptype`                   | `layer` en `style` - zie de documentatie [Ondersteunde kaartstijlen](supported-map-styles.md). |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N.v.t.* - dit is een functie die is gerelateerd aan geocodering. Gebruik de parameter `countrySet` wanneer u de geocoderings-API van Azure Maps gebruikt.  |
| `scale`                     | *N.v.t.*                              |
| `size`                      | `width` en `height` - mag maximaal 8192 x 8192 groot zijn. |
| `style`                     | *N.v.t.*                              |
| `visible`                   | *N.v.t.*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> In het tegelsysteem van Azure Maps zijn de tegels tweemaal zo groot als de kaarttegels die in Google Maps worden gebruikt. Als zodanig wordt de zoomniveauwaarde in Azure Maps één niveau dichterbij weergegeven dan in Google Maps. U kunt dit verschil compenseren door het zoomniveau in de te migreren aanvragen te verlagen.

Zie voor meer informatie de [instructiegids over de API Kaartafbeelding weergeven](how-to-render-custom-data.md).

Naast de mogelijkheid om een statische kaartafbeelding te genereren biedt de weergaveservice van Azure Maps de mogelijkheid om rechtstreeks toegang te krijgen tot kaarttegels in raster- (PNG) en vectorindeling:

- [**Kaarttegel**](/rest/api/maps/render/getmaptile): Raster- (PNG) en vectortegels ophalen voor de basiskaarten (wegen, grenzen, achtergrond).
- [**Kaartfototegel**](/rest/api/maps/render/getmapimagerytile): Luchtfoto- en satellietbeeldtegels ophalen.

> [!TIP]
> Veel Google Maps-toepassingen zijn enkele jaren geleden gewijzigd van ervaringen met interactieve kaarten in statische kaartafbeeldingen. Dit is gedaan als kostenbesparingsmethode. In Azure Maps is het doorgaans kosteneffectiever om het interactieve kaartbesturingselement in de Web SDK te gebruiken. Het interactieve kaartbesturingselement wordt in rekening gebracht op basis van het aantal geladen tegels. Kaarttegels in Azure Maps zijn groot. Vaak zijn er slechts enkele tegels nodig om dezelfde kaartweergave opnieuw te maken als een statische kaart. Kaarttegels worden automatisch in de cache opgeslagen door de browser. Als zodanig genereert het interactieve kaartbesturingselement vaak een fractie van een transactie bij het reproduceren van een statische kaartweergave. Bij het pannen en zoomen worden meer tegels geladen. Er zijn echter opties in het kaartbesturingselement om dit gedrag uit te schakelen. Het interactieve kaartbesturingselement biedt ook veel meer visualisatieopties dan de statischekaartservices.

### <a name="marker-url-parameter-format-comparison"></a>Vergelijking van de indeling van de markerings-URL-parameter

**Voor: Google Maps**

Voeg markeringen toe met behulp van de parameter `markers` in de URL. De parameter `markers` maakt gebruik van een stijl en een lijst met locaties die in die stijl op de kaart moeten worden weergegeven, zoals hieronder wordt weergegeven:

```text
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Als u extra stijlen wilt toevoegen, gebruikt u de `markers`-parameters voor de URL met een andere stijl en set locaties.

Geef de markeringslocaties op in de indeling breedtegraad,lengtegraad.

Voeg markeringsstijlen toe in de indeling `optionName:value`, waarbij meerdere stijlen worden gescheiden door een verticale streep (\|), zoals optieNaam1:waarde1\|optieNaam2:waarde2. Zoals u ziet worden de optienamen en waarden gescheiden door een dubbele punt (:). Gebruik de volgende stijloptienamen voor stijlmarkeringen in Google Maps:

* `color` - de kleur van het standaardmarkeringspictogram. Dit kan een 24-bits hexadecimale kleur (`0xrrggbb`) of een van de volgende waarden zijn. `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
* `label` - een enkele alfanumerieke hoofdletter die boven het pictogram wordt weergegeven.
* `size` - de grootte van de markering. De waarde kan `tiny`, `mid`of `small` zijn.

Gebruik de volgende stijloptienamen voor aangepaste pictogrammen in Google Maps:

* `anchor` - geeft aan hoe de pictogramafbeelding moet worden uitgelijnd met de coördinaat. Dit kan een pixelwaarde (x, y) of een van de volgende waarden zijn: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft` of `bottomright`.
* `icon` - een URL die wijst naar de pictogramafbeelding.

Laten we bijvoorbeeld een rode, middelgrote markering toevoegen aan de kaart op lengtegraad -110 en breedtegraad 45:

```text
&markers=color:red|size:mid|45,-110
```

![Markering in Google Maps](media/migrate-google-maps-web-services/google-maps-marker.png)

**Na: Azure Maps**

Voeg markeringen toe aan een statische kaartafbeelding door de parameter `pins` op te geven in de URL. Net als bij Google Maps geeft u een stijl en een lijst met locaties op in de parameter. De parameter `pins` kan meerdere keren worden opgegeven om markeringen met verschillende stijlen te ondersteunen.

```text
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Als u aanvullende stijlen wilt gebruiken, voegt u aanvullende `pins`-parameters met een andere stijl en set locaties toe aan de URL.

In Azure Maps moet de speldlocatie de indeling lengtegraad breedtegraad hebben. In Google Maps wordt de indeling breedtegraad,lengtegraad gebruikt. De lengtegraad en de breedtegraad worden in de Azure Maps-indeling gescheiden door een spatie, niet door een komma.

Met `iconType` geeft u het type van de te maken speld op. Het kan de volgende waarden hebben:

* `default` - het standaardspeldpictogram.
* `none` -er wordt geen pictogram weergegeven, er worden alleen labels weergegeven.
* `custom` - geeft aan dat er een aangepast pictogram moet worden gebruikt. Een URL die wijst naar de pictogramafbeelding kan worden toegevoegd aan het einde van de parameter `pins`, achter de locatiegegevens van de speld.
* `{udid}` - een unieke gegevens-id (UDID) voor een pictogram dat is opgeslagen in het Azure Maps Data Storage-platform.

Voeg speldstijlen toe met de indeling `optionNameValue`. Scheid meerdere stijlen door een verticale streep (\|). Bijvoorbeeld: `iconType|optionName1Value1|optionName2Value2`. De optienamen en waarden worden niet gescheiden. Gebruik de volgende stijloptienamen voor stijlmarkeringen:

* `al` - geeft de matheid (alfa) van de markering aan. Kies een getal tussen 0 en 1.
* `an` - geeft het speldanker aan. Geef x- en y-pixelwaarden op in de indeling 'x y'.
* `co` - de kleur van de speld. Geef een 24-bits hexadecimale kleur op: `000000` tot `FFFFFF`.
* `la` - geeft de ankerkleur aan. Geef x- en y-pixelwaarden op in de indeling 'x y'.
* `lc` - de kleur van het label. Geef een 24-bits hexadecimale kleur op: `000000` tot `FFFFFF`.
* `ls` - de grootte van het label in pixels. Kies een getal dat groter is dan 0.
* `ro` - het aantal graden dat het pictogram moet worden gedraaid. Kies een getal tussen -360 en 360.
* `sc` - een schaalwaarde voor het speldpictogram. Kies een getal dat groter is dan 0.

Geef labelwaarden op voor elke speldlocatie. Deze aanpak is efficiënter dan het toepassen van één labelwaarde op alle markeringen in de lijst met locaties. De labelwaarde kan een tekenreeks met meerdere tekens zijn. Verpak de tekenreeks in enkele aanhalingstekens om ervoor te zorgen dat deze niet wordt opgevat als een stijl- of locatiewaarde.

Laten we een rood (`FF0000`) standaardpictogram toevoegen met daaronder (15 50) de naam 'Space Needle'. Het pictogram bevindt zich op lengtegraad -122,349300 en breedtegraad 47.620180:

```text
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

![Markering in Azure Maps](media/migrate-google-maps-web-services/azure-maps-marker.png)

Voeg drie spelden toe met de labelwaarden 1, 2 en 3:

```text
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

![Meerdere markeringen in Azure Maps](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)

### <a name="path-url-parameter-format-comparison"></a>Vergelijking van de indeling van de pad-URL-parameter

**Voor: Google Maps**

Voeg lijnen en een veelhoek toe aan een statische kaartafbeelding met behulp van de parameter `path` in de URL. De parameter `path` maakt gebruik van een stijl en een lijst met locaties die op de kaart moeten worden weergegeven, zoals hieronder wordt weergegeven:

```text
&path=pathStyles|pathLocation1|pathLocation2|...
```

U kunt aanvullende stijlen gebruiken door aanvullende `path`-parameters met een andere stijl en set locaties toe te voegen aan de URL.

Padlocaties worden opgegeven met de indeling `latitude1,longitude1|latitude2,longitude2|…`. Paden kunnen worden gecodeerd of adressen voor punten bevatten.

Voeg padstijlen toe met de indeling \| en scheid meerdere stijlen door een verticale streep (`optionName:value`). Scheid de optienamen en waarden door een dubbele punt (:). Voorbeeld: `optionName1:value1|optionName2:value2`. De volgende stijloptienamen kunnen worden gebruikt voor stijlpaden in Google Maps:

* `color` - de kleur van het pad of de contour van de veelhoek. Dit kan een 24-bits hexadecimale kleur (`0xrrggbb`), een 32-bits hexadecimale kleur (`0xrrggbbbaa`) of een van de volgende waarden zijn: zwart, bruin, groen, paars, geel, blauw, grijs, oranje, rood, wit.
* `fillColor` - de kleur om het padgebied mee op te vullen (veelhoek). Dit kan een 24-bits hexadecimale kleur (`0xrrggbb`), een 32-bits hexadecimale kleur (`0xrrggbbbaa`) of een van de volgende waarden zijn: zwart, bruin, groen, paars, geel, blauw, grijs, oranje, rood, wit.
* `geodesic` – geeft aan of het pad een lijn moet zijn die de kromming van de aarde volgt.
* `weight` - de dikte van de padlijn in pixels.

Voeg tussen de coördinaten een rode lijnmatheid en pixeldikte toe aan de kaart, in de URL-parameter. In het onderstaande voorbeeld heeft de lijn een matheid van 50 procent en een dikte van vier pixels. De coördinaten zijn lengtegraad -110, breedtegraad 45 en lengtegraad -100, breedtegraad 50.

```text
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

![Polylijn in Google Maps](media/migrate-google-maps-web-services/google-maps-polyline.png)

**Na: Azure Maps**

Voeg lijnen en veelhoeken toe aan een statische kaartafbeelding door de parameter `path` op te geven in de URL. Net als bij Google Maps geeft u een stijl en een lijst met locaties op in deze parameter. Geef de parameter `path` meerdere keren op om meerdere cirkels, lijnen en veelhoeken met verschillende stijlen weer te geven.

```text
&path=pathStyles||pathLocation1|pathLocation2|...
```

Voor padlocaties moeten de coördinaten in Azure Maps de indeling lengtegraad breedtegraad hebben. In Google Maps wordt de indeling breedtegraad,lengtegraad gebruikt. De lengtegraad en de breedtegraad worden in de Azure Maps-indeling gescheiden door een spatie, niet door een komma. Azure Maps ondersteunt geen gecodeerde paden of adressen voor punten. Upload grotere gegevenssets als een GeoJSON-bestand naar de Azure Maps Data Storage-API, zoals [hier](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage) wordt beschreven.

Voeg padstijlen toe met de indeling `optionNameValue`. Scheid meerdere stijlen door een verticale streep (\|), bijvoorbeeld `optionName1Value1|optionName2Value2`. De optienamen en waarden worden niet gescheiden. Gebruik de volgende stijloptienamen voor stijlpaden in Azure Maps:

* `fa` - de matheid (alfa) van de opvulkleur die wordt gebruikt bij het weergeven van veelhoeken. Kies een getal tussen 0 en 1.
* `fc` - de opvulkleur die wordt gebruikt om het gebied van een veelhoek weer te geven.
* `la` - de matheid (alfa) van de lijnkleur die wordt gebruikt bij het weergeven van lijnen en de contour van veelhoeken. Kies een getal tussen 0 en 1.
* `lc` - de lijnkleur die wordt gebruikt bij het weergeven van lijnen en de contour van veelhoeken.
* `lw` - de breedte van de lijn in pixels.
* `ra` - geeft de cirkelradius in meters aan.

Voeg tussen de coördinaten een rode lijnmatheid en pixeldikte toe, in de URL-parameter. In het onderstaande voorbeeld heeft de lijn een matheid van 50 procent en een dikte van vier pixels. De coördinaten hebben de volgende waarden: lengtegraad: -110, breedtegraad 45 en lengtegraad -100, breedtegraad 50.

```text
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

![Polylijn in Azure Maps](media/migrate-google-maps-web-services/azure-maps-polyline.png)

## <a name="calculate-a-distance-matrix"></a>Een afstandsmatrix berekenen

Azure Maps biedt de afstandmatrix-API. Gebruik deze API om de reistijden en afstanden tussen een set locaties te berekenen met een afstandsmatrix. Deze is vergelijkbaar met de afstandsmatrix-API in Google Maps.

- [**Routematrix**](/rest/api/maps/route/postroutematrixpreview): Hiermee worden asynchroon reistijden en afstanden voor een reeks startpunten en bestemmingen berekend. Ondersteunt maximaal 700 cellen per aanvraag. Dat is het aantal startpunten vermenigvuldigd met het aantal bestemmingen. Met die beperking in gedachte zijn dit voorbeelden van mogelijke matrixdimensies: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Een aanvraag voor de afstandsmatrix-API kan alleen worden gedaan met behulp van een POST-aanvraag waarin de startpunt- en bestemmingsinformatie is opgenomen in de hoofdtekst. Daarnaast moeten in Azure Maps alle startpunten en bestemmingen coördinaten zijn. Adressen moeten eerst een geocodering krijgen.

Deze tabel bevat de API-parameters in Google Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Google Maps      | Vergelijkbare API-parameter in Azure Maps  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` - in de hoofdtekst van de aanvraag opgeven als GeoJSON. |
| `key`                          | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](azure-maps-authentication.md). |
| `language`                     | `language` - zie de documentatie [Ondersteunde talen](supported-languages.md).  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` - in de hoofdtekst van de aanvraag opgeven als GeoJSON.  |
| `region`                       | *N.v.t.* - deze functie is gerelateerd aan geocodering. Gebruik de parameter `countrySet` wanneer u de geocoderings-API van Azure Maps gebruikt. |
| `traffic_model`                | *N.v.t.* - er kan alleen worden opgegeven of er verkeersgegevens moeten worden gebruikt met de parameter `traffic`. |
| `transit_mode`                 | *N.v.t.* - afstandsmatrices op basis van het openbaar vervoer worden op dit moment niet ondersteund.  |
| `transit_routing_preference`   | *N.v.t.* - afstandsmatrices op basis van het openbaar vervoer worden op dit moment niet ondersteund.  |
| `units`                        | *N.v.t.* - in Azure Maps wordt alleen het metrische systeem gebruikt. |

> [!TIP]
> Alle geavanceerde routeringsopties die beschikbaar zijn in de routerings-API van Azure Maps worden ondersteund in de afstandsmatrix-API van Azure Maps. Geavanceerde routeringsopties zijn: vrachtwagenroutering, motorspecificaties enzovoort.

Raadpleeg de documentatie [Best practices voor routering](how-to-use-best-practices-for-routing.md).

## <a name="get-a-time-zone"></a>Een tijdzone ophalen

Azure Maps biedt een API voor het ophalen van de tijdzone van een coördinaat. De tijdzone-API in Azure Maps is vergelijkbaar met de tijdzone-API in Google Maps:

- [**Tijdzone per coördinaat**](/rest/api/maps/timezone/gettimezonebycoordinates): Een coördinaat opgeven en de tijdzonegegevens van de coördinaat ontvangen.

Deze tabel bevat de API-parameters in Google Maps en de vergelijkbare API-parameters in Azure Maps.

| API-parameter in Google Maps | Vergelijkbare API-parameter in Azure Maps   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` - zie ook de documentatie [Verificatie met Azure Maps](azure-maps-authentication.md).       |
| `language`                  | `language` - zie de documentatie [Ondersteunde talen](supported-languages.md).    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Naast deze API biedt Azure Maps een aantal andere tijdzone-API's. Met deze API's wordt de tijd geconverteerd op basis van de namen of de id's van de tijdzone:

- [**Tijdzone op id**](/rest/api/maps/timezone/gettimezonebyid): Hiermee worden de huidige, historische en toekomstige tijdzonegegevens voor de opgegeven IANA-tijdzone-id geretourneerd.
- [**Opsomming IANA-tijdzones**](/rest/api/maps/timezone/gettimezoneenumiana): Retourneert een volledige lijst met IANA-tijdzone-id's. Updates in de IANA-service worden binnen één dag in het systeem doorgevoerd.
- [**Opsomming Windows-tijdzones**](/rest/api/maps/timezone/gettimezoneenumwindows): Retourneert een volledige lijst met Windows-tijdzone-id's.
- [**IANA-tijdzoneversie**](/rest/api/maps/timezone/gettimezoneianaversion): Retourneert het huidige IANA-versienummer dat wordt gebruikt door Azure Maps.
- [**Windows-tijdzone naar IANA**](/rest/api/maps/timezone/gettimezonewindowstoiana): Retourneert de overeenkomstige IANA-id van een geldige Windows-tijdzone-id. Er kunnen meerdere IANA-id's voor één Windows-id worden geretourneerd.

## <a name="client-libraries"></a>Clientbibliotheken

Azure Maps biedt clientbibliotheken voor de volgende computertalen:

* JavaScript, TypeScript, Node.js – [documentatie](how-to-use-services-module.md) \| [NPM-pakket](https://www.npmjs.com/package/azure-maps-rest)

Deze bibliotheken voor opensource-clients zijn voor andere computertalen:

* .NET Standard 2.0 – [GitHub-project](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet-pakket](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="clean-up-resources"></a>Resources opschonen

Er zijn geen resources om op te schonen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Maps REST-services:

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor zoeken](how-to-use-best-practices-for-search.md)
