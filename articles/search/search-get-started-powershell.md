---
title: "Quickstart: Een zoekindex maken in PowerShell met behulp van REST API's"
titleSuffix: Azure Cognitive Search
description: In deze REST API-quickstart leert u hoe u een index maakt, gegevens laadt en query's uitvoert met behulp van Invoke-RestMethod van PowerShell en van de REST API's van Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9dd9de9dcb01e9be200e07e5925d8b856432b620
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94742375"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-powershell-using-rest-apis"></a>Quickstart: Een Azure Cognitive Search-index maken in PowerShell met behulp van REST API's
> [!div class="op_single_selector"]
> * [PowerShell (REST)]()
> * [C#](./search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Python](search-get-started-python.md)
> * [Portal](search-get-started-portal.md)
> 

In dit artikel laten we u zien hoe u een Azure Cognitive Search-index kunt maken, laden en er query's op kunt uitvoeren met PowerShell en de [REST API's van Azure Cognitive Search](/rest/api/searchservice/). In dit artikel wordt uitgelegd hoe u PowerShell-opdrachten interactief uitvoert. U kunt ook een [PowerShell-script downloaden en uitvoeren](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) waarmee dezelfde bewerkingen worden uitgevoerd.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende services en hulpprogramma's zijn vereist voor deze quickstart. 

+ [PowerShell 5.1 of hoger](https://github.com/PowerShell/PowerShell), met [Invoke-RestMethod](/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) voor sequentiële en interactieve stappen.

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt een gratis service voor deze quickstart gebruiken. 

## <a name="copy-a-key-and-url"></a>Een sleutel en URL kopiëren

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

2. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eindpunt en toegangssleutel ophalen](media/search-get-started-rest/get-url-key.png "Een HTTP-eindpunt en toegangssleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist op elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="connect-to-azure-cognitive-search"></a>Verbinding maken met Azure Cognitive Search

1. Maak in PowerShell een **$headers**-object om het inhoudstype en de API-sleutel op te slaan. Vervang de API-sleutel van de beheerder (YOUR-ADMIN-API-KEY) door een sleutel die geldig is voor uw zoekservice. U moet deze header slechts eenmaal in te stellen voor de sessie, maar voegt hem toe aan elk verzoek. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Maak een **$url**-object dat de indexverzameling van de service opgeeft. Vervang de naam van de service (YOUR-SEARCH-SERVICE-NAME) door een geldige zoekservice.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2020-06-30&$select=name"
    ```

3. Voer **Invoke-RestMethod** ouit m een GET-verzoek naar de service te verzenden en de verbinding te controleren. Voeg **ConvertTo-Json** toe zodat u de antwoorden kunt weer geven die zijn teruggestuurd vanuit de service.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Als de service leeg is en geen indexen bevat, dan lijken de resultaten op het volgende voorbeeld. Anders ziet u een JSON-weergave van indexdefinities.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 - Een index maken

Er moet een index in de service aanwezig zijn voordat u gegevens laadt, tenzij u de portal gebruikt. In deze stap wordt de index gedefinieerd en gepusht naar de service. De API [REST API index maken](/rest/api/searchservice/create-index) wordt gebruikt voor deze stap.

De vereiste elementen van een index zijn een naam en een verzameling velden. Met de verzameling velden wordt de structuur van een *document* gedefinieerd. Elk veld is voorzien van een naam, type en kenmerken die bepalen hoe het wordt gebruikt (bijvoorbeeld of er op volledige tekst kan worden gezocht, of er filters kunnen worden toegepast en of het veld kan worden opgehaald in de zoekresultaten). In een index moet een van de velden van het type `Edm.String` worden ingesteld als de *sleutel* voor de documentidentiteit.

Deze index heeft de naam 'hotels-quickstart' en beschikt over de onderstaande velddefinities. Dit is een subset van de grotere [Hotels-index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) die in andere kennisartikelen wordt gebruikt. De velddefinities zijn in deze Quick Start afgekapt om het beknopt te houden.

1. Plak dit voorbeeld in PowerShell om een **$body**-object te maken dat het indexschema bevat.

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. Stel de URI in op de indexverzameling voor uw service en de index *hotels-quickstart*.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2020-06-30"
    ```

3. Voer de opdracht uit met **$url**, **$headers** en **$body** om de index te maken voor de service. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    De resultaten zouden er as volgt moeten uitzien (ingekort tot de eerste twee velden om het beknopt te houden):

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> U kunt ook de lijst met indexen controleren in het portaal.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documenten laden

Voor het pushen van documenten gebruikt u een HTTP POST-aanvraag in het URL-eindpunt van uw index. De REST API voor deze taak is [Documenten toevoegen, bijwerken of verwijderen](/rest/api/searchservice/addupdate-or-delete-documents).

1. Plak dit voorbeeld in PowerShell om een **$body**-object te maken dat de documenten bevat die u wilt uploaden. 

    Dit verzoek bevat twee volledige en één gedeeltelijke record. De gedeeltelijke record laat zien dat u onvolledige documenten kunt uploaden. De parameter `@search.action` geeft aan hoe indexering verloopt. Geldige waarden zijn upload, merge, mergeOrUpload en delete. MergeOrUpload maakt een nieuw document voor hotelId = 3 of werkt de inhoud van het bestaand document bij.

    ```powershell
    $body = @"
    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. Stel het eindpunt in op de documentverzameling *hotels-quickstart* en voeg de indexbewerking (indices/hotels-quickstart/docs/index) toe.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2020-06-30"
    ```

1. Voer de opdracht met **$url**, **$headers** en **$Body** uit om documenten in de index hotels-quickstart te laden.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    De resultaten moeten er ongeveer uitzien als in het volgende voorbeeld. Normaal gezien ziet u een [statuscode 201](/rest/api/searchservice/HTTP-status-codes).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

Bij deze stap ziet u hoe u een query op een index uitvoert met behulp van de [API Documenten zoeken](/rest/api/searchservice/search-documents).

Zorg ervoor dat u enkele aanhalingstekens gebruikt bij het zoeken naar $urls. Queryreeksen bevatten **$** -tekens. Als de volledige reeks tussen enkele aanhalingstekens staat, hoeft u geen escape-teken te gebruiken.

1. Stel het eindpunt in op de documentverzameling *hotels-quickstart* en voeg een parameter **zoeken** toe om in een querytekenreeks door te geven. 
  
   Deze tekenreeks voert een lege zoekopdracht (search=*) uit, die een niet-geclassificeerde lijst (zoekscore  = 1,0) met willekeurige documenten retourneert. Standaard worden in Azure Cognitive Search 50 resultaten per keer geretourneerd. Volgens de structuur worden met deze query een gehele documentstructuur en waarden geretourneerd. Voeg **$count=true** toe om een telling van alle documenten in de resultaten te krijgen.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$count=true'
    ```

1. Voer de opdracht uit om de **$url** naar de service te sturen.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    De resultaten moeten er ongeveer uitzien als in de volgende uitvoer.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Probeer een paar andere queryvoorbeelden uit om een idee te krijgen van de syntaxis. U kunt een tekenreekszoekopdracht uitvoeren, letterlijke $filter-query's uitvoeren, de ingestelde resultaten beperken, het bereik van de zoekopdracht aanpassen voor specifieke velden, en meer.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or higher
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u PowerShell gebruikt om de basiswerkstroom om inhoud te maken en te gebruiken in Azure Cognitive Search te doorlopen. Nu u vertrouwd bent met de concepten, raden we u aan om verder te gaan met meer geavanceerde scenario's, zoals indexeren vanaf Azure-gegevensbronnen;

> [!div class="nextstepaction"]
> [Zelfstudie over REST: Semi-gestructureerde gegevens (JSON-blobs) indexeren en doorzoeken in Azure Cognitive Search](search-semi-structured-data.md)
