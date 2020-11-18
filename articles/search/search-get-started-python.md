---
title: "Quickstart: Een zoekindex maken in Python met behulp van REST API's"
titleSuffix: Azure Cognitive Search
description: Hierin wordt uitgelegd hoe u een index maakt, gegevens laadt en query's uitvoert met behulp van Python, Jupyter Notebooks en de REST API van Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 08/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: dca53dc27eacc5c7e04bbf6cb5df82a8e8da0dfc
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694548"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>Quickstart: Een Azure Cognitive Search-index maken in Python met behulp van Jupyter-notebooks

> [!div class="op_single_selector"]
> * [Python (REST)](search-get-started-python.md)
> * [PowerShell (REST)](./search-get-started-powershell.md)
> * [C#](./search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Portal](search-get-started-portal.md)
> 

Bouw een Jupyter-notebook waarmee een Azure Cognitive Search-index wordt gemaakt en geladen en waarmee query's kunnen worden uitgevoerd met behulp van Python en de [REST API's van Azure Cognitive Search](/rest/api/searchservice/). In dit artikel wordt stap voor stap uitgelegd hoe u een notebook maakt. Een andere optie is om [een voltooide Jupyter Python-notebook te downloaden en uit te voeren](https://github.com/Azure-Samples/azure-search-python-samples).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende services en hulpprogramma's zijn vereist voor deze quickstart. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), biedt Python 3.x en Jupyter Notebooks.

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt voor deze quickstart de gratis laag gebruiken. 

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

![Een HTTP-eindpunt en toegangssleutel ophalen](media/search-get-started-rest/get-url-key.png "Een HTTP-eindpunt en toegangssleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist op elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="connect-to-azure-cognitive-search"></a>Verbinding maken met Azure Cognitive Search

In deze taak start u een Jupyter-notebook en controleert u of u verbinding kunt maken met Azure Cognitive Search. Hiervoor gaat u een lijst met indices uit uw service aanvragen. In Windows met Anaconda3 kunt u Anaconda Navigator gebruiken om een notebook te starten.

1. Maak een nieuwe Python3-notebook.

1. In de eerste cel laadt u de bibliotheken die worden gebruiken om met JSON te werken en HTTP-aanvragen te formuleren.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. In de tweede cel voert u de aanvraagelementen in die bij elke aanvraag constanten zullen zijn. Vervang de naam van de zoekservice (YOUR-SEARCH-SERVICE-NAME) en de beheer-API-sleutel (YOUR-ADMIN-API-KEY) door geldige waarden. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2020-06-30'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   Als ConnectionError `"Failed to establish a new connection"` wordt weergegeven, controleert u of de api-key een primaire of secundaire beheersleutel is en of alle voorloop- en volgtekens (`?` en `/`) op de goede plaats staan.

1. Formuleer de aanvraag in de derde cel. Deze GET-aanvraag is gericht op de verzameling indices van uw zoekservice; hiermee selecteert u de naameigenschap van bestaande indices.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. Voer elke stap uit. Als er indices aanwezig zijn, bevat het resultaat een lijst met indexnamen. In de onderstaande schermopname is de service al voorzien van de indices azureblob en realestate-us-sample.

   ![Python-script in Jupyter-notebook met HTTP-aanvragen voor Azure Cognitive Search](media/search-get-started-python/connect-azure-search.png "Python-script in Jupyter-notebook met HTTP-aanvragen voor Azure Cognitive Search")

   Daarentegen wordt door een lege indexverzameling de volgende reactie geretourneerd: `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - Een index maken

Er moet een index in de service aanwezig zijn voordat u gegevens laadt, tenzij u de portal gebruikt. Bij deze stap gebruiken we de [REST API Index maken](/rest/api/searchservice/create-index) om een indexschema naar de service te pushen.

De vereiste elementen van een index zijn een naam, een verzameling velden en een sleutel. Met de verzameling velden wordt de structuur van een *document* gedefinieerd. Elk veld is voorzien van een naam, type en kenmerken die bepalen hoe het veld wordt gebruikt (bijvoorbeeld of er op volledige tekst kan worden gezocht, of er filters kunnen worden toegepast en of het veld kan worden opgehaald in de zoekresultaten). In een index moet een van de velden van het type `Edm.String` worden ingesteld als de *sleutel* voor de documentidentiteit.

Deze index heeft de naam 'hotels-quickstart' en beschikt over de onderstaande velddefinities. Dit is een subset van de grotere [Hotels-index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) die in andere scenario's wordt gebruikt. We hebben de index voor deze quickstart ingekort.

1. In de volgende cel plakt u het volgende voorbeeld in een cel om het schema op te geven. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. Formuleer de aanvraag in een andere cel. Deze POST-aanvraag is gericht op de verzameling indices van uw zoekservice en hiermee wordt een index gemaakt op basis van het indexschema dat u in de vorige cel hebt opgegeven.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. Voer elke stap uit.

   Het antwoord bevat de JSON-weergave van het schema. In de volgende schermopname ziet u een klein deel van het antwoord.

    ![Aanvraag om een index te maken](media/search-get-started-python/create-index.png "Aanvraag om een index te maken")

> [!Tip]
> U kunt ook controleren of de index wordt gemaakt door de lijst met indices in de portal te bekijken.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documenten laden

Voor het pushen van documenten gebruikt u een HTTP POST-aanvraag in het URL-eindpunt van uw index. De REST API is [Documenten toevoegen, bijwerken of verwijderen](/rest/api/searchservice/addupdate-or-delete-documents). Documenten zijn afkomstig van [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) op GitHub.

1. In een nieuwe cel geeft u vier documenten op die het indexschema bevestigen. Geef voor elk document een uploadactie op.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. Formuleer de aanvraag in een andere cel. Deze POST-aanvraag is gericht op de verzameling documenten van de index hotels-quickstart en pusht de documenten die in de vorige stap zijn opgegeven.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. Voer elke stap uit om de documenten naar een index in uw zoekservice te pushen. De resultaten moeten er ongeveer uitzien als in het volgende voorbeeld. 

    ![Documenten naar een index verzenden](media/search-get-started-python/load-index.png "Documenten naar een index verzenden")

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

Bij deze stap ziet u hoe u een query op een index uitvoert met behulp van de [REST API Documenten zoeken](/rest/api/searchservice/search-documents).

1. Geef in een cel een query-expressie op waarmee een lege zoekopdracht (search=*) wordt uitgevoerd en waardoor een niet-geclassificeerde lijst (zoekscore  = 1,0) met willekeurige documenten wordt geretourneerd. Standaard worden in Azure Cognitive Search 50 resultaten per keer geretourneerd. Volgens de structuur worden met deze query een gehele documentstructuur en waarden geretourneerd. Voeg $count=true toe om een telling van alle documenten in de resultaten te krijgen.

   ```python
   searchstring = '&search=*&$count=true'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. Geef in een nieuwe cel het volgende voorbeeld op om te zoeken op de termen 'hotels' en 'wifi'. Voeg $select toe om op te geven welke velden moeten worden meegenomen in de zoekresultaten.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)   
   ```

   De resultaten moeten er ongeveer uitzien als in de volgende uitvoer. 

    ![Een index doorzoeken](media/search-get-started-python/search-index.png "Een index doorzoeken")

1. Pas vervolgens een $filter-expressie toe waarmee alleen die hotels worden geselecteerd die een hogere waardering hebben dan 4. 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)     
   ```

1. Standaard worden met de zoekengine de 50 hoogste documenten geretourneerd, maar u kunt top en skip gebruiken om paginering toe te voegen en te kiezen hoeveel documenten elk resultaat bevat. Met deze query worden twee documenten in elke resultatenset geretourneerd.

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. In dit laatste voorbeeld gebruiken we $orderby om resultaten op stad te sorteren. Dit voorbeeld bevat velden uit de adresverzameling.

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

## <a name="clean-up"></a>Opruimen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Om het iets eenvoudiger te maken, wordt in deze quickstart een beknopte versie van de Hotels-index gebruikt. U kunt de volledige versie maken om meer interessante query's te testen. U kunt de volledige versie en alle 50 documenten ophalen door de wizard **Gegevens importeren** uit te voeren en *hotels-sample* te selecteren bij de meegeleverde voorbeeldgegevensbronnen.

> [!div class="nextstepaction"]
> [Snelstart: Een index maken in Azure Portal](search-get-started-portal.md)