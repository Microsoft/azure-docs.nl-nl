---
title: Zoeken naar een locatie met behulp van Azure Maps-Zoek Services
description: Meer informatie over de Azure Maps Search-service. Zie hoe u deze set Api's gebruikt voor geocodering, geocodering terugdraaien, fuzzy Zoek opdrachten en omgekeerde Cross-Street Zoek opdrachten.
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/19/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: dddf56edf2037d87a28589a59834db32f8d04a4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98598363"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Zoeken naar een locatie met behulp van Azure Maps-Zoek Services

De [Azure Maps Search service](/rest/api/maps/search) is een reeks rest api's die is ontworpen om ontwikkel aars te helpen bij het zoeken naar adressen, plaatsen en zakelijke vermeldingen op naam, categorie en andere geografische informatie. Naast het ondersteunen van traditionele geocodering kunnen services ook Geocode-adressen en Cross-Streets omkeren op basis van de breedte graad en lengte graad. De breedte-en lengte waarden die door de zoek opdracht worden geretourneerd, kunnen als para meters in andere Azure Maps services worden gebruikt, zoals [route](/rest/api/maps/route) en [weer](/rest/api/maps/weather) Services.


In dit artikel leert u het volgende:

* Vraag de breedte-en lengte coördinaten voor een adres (locatie van een geocode) aan met behulp van de [API voor Zoek adressen]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).
* Zoek naar een adres of belang stelling (POI) met behulp van de [fuzzy Search-API](/rest/api/maps/search/getsearchfuzzy).
* Maak een [achterwaartse adres zoekactie](/rest/api/maps/search/getsearchaddressreverse) om de coördinaat locatie te vertalen naar het adres.
* Vertaal de locatie van de coördinaat in een door een mens begrijpelijke cross-straat met behulp van [Search Address reverse Cross Street-API](/rest/api/maps/search/getsearchaddressreversecrossstreet).  Dit is meestal nodig bij het volgen van toepassingen die een GPS-feed ontvangen van een apparaat of Asset en waarvan u wilt weten waar de coördinaat zich bevindt.

## <a name="prerequisites"></a>Vereisten

1. [Een Azure Maps-account maken](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Een primaire sleutel voor een abonnement verkrijgen](quick-demo-map-app.md#get-the-primary-key-for-your-account), ook wel bekend als de primaire sleutel of de abonnementssleutel.

In deze zelfstudie wordt gebruikgemaakt van de [Postman](https://www.postman.com/)-toepassing, maar u kunt ook een andere API-ontwikkelomgeving kiezen.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>De breedte graad en lengte graad voor een adres aanvragen (geocodering)

In dit voor beeld gebruiken we de Azure Maps [Zoek adres-API ophalen](/rest/api/maps/search/getsearchaddress) om een adres te converteren naar de coördinaten van de breedte graad en lengte graad. Dit proces wordt ook wel *geocodering* genoemd. Naast het retour neren van de coördinaten retourneert het antwoord ook gedetailleerde adres eigenschappen, zoals straat, post code, gemeente en land/regio gegevens.

>[!TIP]
>Als u een reeks adressen naar Geocode hebt, kunt u de batch- [API post Search-adres](/rest/api/maps/search/postsearchaddressbatch) gebruiken om een batch met query's te verzenden in één API-aanroep.

1. Open de Postman-app. Selecteer **New** (Nieuw) bovenaan de Postman-app. Selecteer **Collection** (Verzameling) in het venster **Create New** (Nieuwe maken).  Geef de verzameling een naam en selecteer de knop **Create** (Maken). U gebruikt deze verzameling voor de rest van de voor beelden in dit document.

2. Selecteer nogmaals **New** (Nieuw) om de aanvraag te maken. Selecteer **Request** (Aanvraag) in het venster **Create New** (Nieuwe maken). Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u in de vorige stap hebt gemaakt en selecteer **Save** (Opslaan).

3. Selecteer de methode http **ophalen** op het tabblad Builder en voer de volgende URL in. In deze aanvraag zoeken we naar een specifiek adres: `400 Braod St, Seattle, WA 98109` . Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

4. Klik op de knop voor blauwe **verzen ding** . De antwoord tekst bevat gegevens voor één locatie.

5. Nu gaan we een adres doorzoeken dat meer dan één mogelijke locatie heeft. In de sectie **params** wijzigt u de `query` sleutel in `400 Broad, Seattle` . Klik op de knop voor blauwe **verzen ding** .

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="Adres zoeken":::

6. Probeer vervolgens de `query` sleutel in te stellen `400 Broa` op.

7. Klik op de knop **Verzenden**. U kunt nu zien dat het antwoord reacties van meerdere landen bevat. Als u de resultaten voor uw gebruikers wilt geobias, voegt u altijd zoveel locatie gegevens toe aan de aanvraag.

## <a name="using-fuzzy-search-api"></a>Fuzzy Search-API gebruiken

De Azure Maps [fuzzy Search-API](/rest/api/maps/search/getsearchfuzzy) ondersteunt standaard enkelvoudige en vrije-vorm zoekopdrachten. U wordt aangeraden de Azure Maps zoek actie voor fuzzy te gebruiken wanneer u het invoer type van uw gebruiker voor een zoek aanvraag niet kent.  De query-invoer kan een volledig of gedeeltelijk adres zijn. Het kan ook een POI-token (Point of interest) zijn, zoals een naam van POI, een POI categorie of een merk naam. Daarnaast kunnen de query resultaten worden beperkt door een coördinaat locatie en RADIUS, of door een selectie vakje te definiëren om de relevantie van de zoek resultaten te verbeteren.

>[!TIP]
>De meeste zoek query's zijn standaard maxFuzzyLevel = 1 voor het verkrijgen van prestaties en het verminderen van ongebruikelijke resultaten. U kunt het overeenkomst niveau aanpassen met behulp van de `maxFuzzyLevel` `minFuzzyLevel` para meters of. Zie voor meer informatie over `maxFuzzyLevel` en een volledige lijst met alle optionele para meters de [para meters voor fuzzy Search-uri's](/rest/api/maps/search/getsearchfuzzy#uri-parameters)

### <a name="search-for-an-address-using-fuzzy-search"></a>Zoeken naar een adres met behulp van fuzzy Search

In dit voor beeld gebruiken we fuzzy Search om te zoeken naar de hele wereld voor `pizza` .  Vervolgens laten we u zien hoe u kunt zoeken in het bereik van een bepaald land. Ten slotte laten we u zien hoe u een coördinaat locatie en RADIUS kunt gebruiken om een zoek opdracht in een bepaald gebied te bereiken en het aantal geretourneerde resultaten te beperken.

>[!IMPORTANT]
>Als u de resultaten voor uw gebruikers wilt geobias, voegt u altijd zoveel mogelijk locatie gegevens toe. Zie [Aanbevolen procedures voor zoeken voor](how-to-use-best-practices-for-search.md#geobiased-search-results)meer informatie.

1. Open de Postman-app, klik op **Nieuw** en selecteer **aanvraag**. Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u hebt gemaakt in de vorige sectie of maak een nieuwe, en selecteer vervolgens **Opslaan**.

2. Selecteer de methode http **ophalen** op het tabblad Builder en voer de volgende URL in. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel.

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >Het _JSON_ -kenmerk in het URL-pad bepaalt de antwoord indeling. In dit artikel wordt JSON gebruikt voor gebruiks gemak en lees baarheid. Zie de `format` parameter definitie in de [naslag documentatie over URI-para meters](/rest/api/maps/search/getsearchfuzzy#uri-parameters)voor meer informatie over andere ondersteunde antwoord indelingen.

3. Klik op **verzenden** en controleer de tekst van het antwoord.

    De dubbel zinnige query reeks voor ' pizza ' heeft 10 [resultaat van de rente](/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) in de categorieën ' pizza ' en ' restaurant ' geretourneerd. Elk resultaat bevat details zoals het adres van de straat, de breedte graad en de lengte graad, de weergave poort en de toegangs punten voor de locatie. De resultaten zijn nu variërend voor deze query en zijn niet gekoppeld aan een referentie locatie.
  
    In de volgende stap gebruiken we de `countrySet` para meter om alleen de landen/regio's op te geven waarvoor uw toepassing behoefte heeft. Zie [Zoek dekking](./geocoding-coverage.md)voor een volledige lijst met ondersteunde landen/regio's.

4. Het standaard gedrag is om de hele wereld te doorzoeken, waardoor mogelijk onnodige resultaten worden geretourneerd. We zoeken nu alleen naar Pizza van de Verenigde Staten. Voeg de `countrySet` sleutel toe aan de sectie **params** en stel de waarde in op `US` . Als de `countrySet` sleutel wordt ingesteld op `US` , worden de resultaten gebonden aan de Verenigde Staten.

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="Zoeken naar pizza in de Verenigde Staten":::

    De resultaten worden nu begrensd door de land code en de query retourneert pizza restaurants in de Verenigde Staten.

5. Als u een nog effectiever zoek actie wilt uitvoeren, kunt u zoeken naar het bereik van een lat./Lon. coördinaten paar. In dit voor beeld gebruiken we lat./Lon. van de naald Space van Seattle. Omdat we alleen resultaten binnen een RADIUS van 400 meter willen retour neren, voegen we de `radius` para meter toe. Daarnaast voegen we de `limit` para meter toe om de resultaten te beperken tot de vijf dichtstbijzijnde pizza-locaties.

    Voeg in de sectie **params** de volgende sleutel/waarde-paren toe:

     | Sleutel | Waarde |
    |-----|------------|
    | lat | 47,620525 |
    | Lon | -122,349274 |
    | RADIUS | 400 |
    | limiet | 5|

6. Klik op **Verzenden**. Het antwoord bevat resultaten voor pizza restaurants in de buurt van de naald ruimte van Seattle.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Zoeken naar een adres met behulp van Reverse Address Search

Met de Azure Maps [Zoek adres terugdraaiende API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) worden de coördinaten omgezet in de door menselijke Lees bare straat adressen. Deze API wordt vaak gebruikt voor toepassingen die gebruikmaken van GPS-feeds en adressen op specifieke coördinaten punten moeten detecteren.

>[!IMPORTANT]
>Als u de resultaten voor uw gebruikers wilt geobias, voegt u altijd zoveel mogelijk locatie gegevens toe. Zie [Aanbevolen procedures voor zoeken voor](how-to-use-best-practices-for-search.md#geobiased-search-results)meer informatie.

>[!TIP]
>Als u een set coördinaten locaties hebt voor reverse Geocode, kunt u [post Search Address reverse batch-API](/rest/api/maps/search/postsearchaddressreversebatch) gebruiken om een batch met query's in één API-aanroep te verzenden.

In dit voor beeld maken we omgekeerde Zoek opdrachten met enkele van de optionele para meters die beschikbaar zijn. Zie [Reverse Search-para meters](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)voor een volledige lijst met optionele para meters.

1. Klik in de app postman op **Nieuw** en selecteer **aanvraag**. Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u in de eerste sectie hebt gemaakt of maak een nieuwe, en selecteer vervolgens **Opslaan**.

2. Selecteer de methode http **ophalen** op het tabblad Builder en voer de volgende URL in. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel. De aanvraag moet lijken op de volgende URL:

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. Klik op **verzenden** en controleer de tekst van het antwoord. Er wordt een resultaat van de query weer geven. Het antwoord bevat sleutel adres informatie over het veld Safeco.
  
4. Nu voegen we de volgende sleutel/waarde-paren toe aan de sectie **params** :

    | Sleutel | Waarde | Retouren
    |-----|------------|------|
    | getal | 1 |Het antwoord kan de zijde van de straat (links/rechts) bevatten en ook een verschuivings positie voor het getal.|
    | returnSpeedLimit | true | Retourneert de snelheids limiet op het adres.|
    | returnRoadUse | true | Hiermee wordt het gebruik van typen weg op het adres geretourneerd. Voor alle mogelijke typen weg gebruik, Zie [typen voor gebruik](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)van de weg.|
    | returnMatchType | true| Hiermee wordt het type overeenkomst geretourneerd. Zie [omgekeerde adres Zoek resultaten](/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult) voor alle mogelijke waarden

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="Zoeken in omgekeerde volg orde.":::

5. Klik op **verzenden** en controleer de tekst van het antwoord.

6. Vervolgens voegt u de sleutel toe `entityType` en stelt u de waarde in op `Municipality` . De `entityType` sleutel wordt vervangen door de `returnMatchType` sleutel in de vorige stap. We moeten ook proberen om `returnSpeedLimit` `returnRoadUse` informatie over de gemeente te verwijderen.  Zie [entiteits typen](/rest/api/maps/search/getsearchaddressreverse#entitytype)voor alle mogelijke entiteits typen.

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="Achterwaartse-entityType zoeken.":::

7. Klik op **Verzenden**. Vergelijk de resultaten met de resultaten die u in stap 5 hebt geretourneerd.  Omdat het aangevraagde entiteit type nu is `municipality` , bevat het antwoord geen adres informatie. De geretourneerde `geometryId` kan ook worden gebruikt om grens veelhoek aan te vragen via Azure Maps [Zoek VEELHOEK-API](/rest/api/maps/search/getsearchpolygon)ophalen.

>[!TIP]
>Zie de [sectie terugkerend zoeken](/rest/api/maps/search/getsearchaddressreverse#uri-parameters)voor meer informatie over deze para meters en informatie over anderen.

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Zoeken naar meerdere Streets met omgekeerde adres zoeken in meerdere straat

In dit voor beeld zoeken we naar een cross-straat op basis van de coördinaten van een adres.

1. Klik in de app postman op **Nieuw** en selecteer **aanvraag**. Voer een **Request name** (Aanvraagnaam) in voor de aanvraag. Selecteer de verzameling die u in de eerste sectie hebt gemaakt of maak een nieuwe, en selecteer vervolgens **Opslaan**.

2. Selecteer de methode http **ophalen** op het tabblad Builder en voer de volgende URL in. Voor deze aanvraag en andere aanvragen die in dit artikel worden vermeld, vervangt u `{Azure-Maps-Primary-Subscription-key}` door uw primaire abonnementssleutel. De aanvraag moet lijken op de volgende URL:
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="Zoeken in Kruis adres.":::
  
3. Klik op **verzenden** en controleer de tekst van het antwoord. U ziet dat het antwoord een waarde bevat `crossStreet` van `South Atlantic Street` .

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Maps Search Service REST API](/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Aanbevolen procedures voor Azure Maps Search Service](how-to-use-best-practices-for-search.md)