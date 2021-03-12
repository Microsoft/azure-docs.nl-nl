---
title: 'Quickstart: Een zoekindex maken in Python'
titleSuffix: Azure Cognitive Search
description: Meer informatie over het maken van een zoek index, het laden van gegevens en het uitvoeren van query's met behulp van python, Jupyter Notebook en de Azure.Documents. Zoek naar de client bibliotheek voor python.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/12/2021
ms.custom: devx-track-python
ms.openlocfilehash: 8b9c4792fa6dbdc70f657ce3c5f1757473a22fda
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225214"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebook"></a>Snelstartgids: een Azure Cognitive Search-index in python maken met behulp van Jupyter Notebook

> [!div class="op_single_selector"]
> * [Python](search-get-started-python.md)
> * [PowerShell (REST)](search-get-started-powershell.md)
> * [C#](search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Portal](search-get-started-portal.md)
>

Bouw een notitie blok dat een Azure Cognitive Search-index maakt, laadt en opvraagt met behulp van python en de [bibliotheek Azure-Search-Documents](/python/api/overview/azure/search-documents-readme) in de Azure SDK voor python. In dit artikel wordt stap voor stap uitgelegd hoe u een notebook maakt. Een andere optie is om [een voltooide Jupyter Python-notebook te downloaden en uit te voeren](https://github.com/Azure-Samples/azure-search-python-samples).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende services en hulpprogramma's zijn vereist voor deze quickstart.

* [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section), biedt Python 3.x en Jupyter Notebook.

* [pakket azure-search-documents](https://pypi.org/project/azure-search-documents/)

* [Een zoek service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt voor deze quickstart de gratis laag gebruiken. 

## <a name="copy-a-key-and-url"></a>Een sleutel en URL kopiëren

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   ![Een HTTP-eindpunt en toegangssleutel ophalen](media/search-get-started-rest/get-url-key.png "Een HTTP-eindpunt en toegangssleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist op elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="connect-to-azure-cognitive-search"></a>Verbinding maken met Azure Cognitive Search

Start Jupyter Notebook in deze taak en controleer of u verbinding kunt maken met Azure Cognitive Search. Hiervoor gaat u een lijst met indices uit uw service aanvragen. In Windows met Anaconda3 kunt u Anaconda Navigator gebruiken om een notebook te starten.

1. Maak een nieuwe Python3-notebook.

1. In de eerste cel laadt u de bibliotheken van de Azure SDK voor Python, met inbegrip van [azure-search-documents](/python/api/azure-search-documents).

   ```python
    !pip install azure-search-documents --pre
    !pip show azure-search-documents
    
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.search.documents.indexes import SearchIndexClient 
    from azure.search.documents import SearchClient
    from azure.search.documents.indexes.models import (
        ComplexField,
        CorsOptions,
        SearchIndex,
        ScoringProfile,
        SearchFieldDataType,
        SimpleField,
        SearchableField
    )
   ```

1. In de tweede cel voert u de aanvraagelementen in die bij elke aanvraag constanten zullen zijn. Geef de naam van de zoekservice, de admin-API-sleutel en de query-API-sleutel op die u in een vorige stap hebt gekopieerd. In deze cel worden ook de clients ingesteld die u wilt gebruiken voor specifieke bewerkingen: [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) voor het maken van een index en [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) voor het uitvoeren van query's op een index.

   ```python
    service_name = "YOUR-SEARCH-SERIVCE-NAME"
    admin_key = "YOUR-SEARCH-SERVICE-ADMIN-API-KEY"
    
    index_name = "hotels-quickstart"
    
    # Create an SDK client
    endpoint = "https://{}.search.windows.net/".format(service_name)
    admin_client = SearchIndexClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
    
    search_client = SearchClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
   ```

1. In de derde cel voert u een delete_index-bewerking uit om eventuele bestaande *hotels-quickstart*-indexen uit uw service te verwijderen. Door de index te verwijderen, kunt u een andere *hotels quickstart*-index maken met dezelfde naam.

   ```python
    try:
        result = admin_client.delete_index(index_name)
        print ('Index', index_name, 'Deleted')
    except Exception as ex:
        print (ex)
   ```

1. Voer elke stap uit.

## <a name="1---create-an-index"></a>1 - Een index maken

De vereiste elementen van een index zijn een naam, een verzameling velden en een sleutel. De verzameling velden definieert de structuur van een logisch *zoekdocument*, dat zowel kan worden gebruikt voor het laden van gegevens als voor het retourneren van resultaten. 

Elk veld is voorzien van een naam, type en kenmerken die bepalen hoe het veld wordt gebruikt (bijvoorbeeld of er op volledige tekst kan worden gezocht, of er filters kunnen worden toegepast en of het veld kan worden opgehaald in de zoekresultaten). In een index moet een van de velden van het type `Edm.String` worden ingesteld als de *sleutel* voor de documentidentiteit.

Deze index heeft de naam 'hotels-quickstart' en beschikt over de onderstaande velddefinities. Dit is een subset van de grotere [Hotels-index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) die in andere scenario's wordt gebruikt. We hebben de index voor deze quickstart ingekort.

1. In de volgende cel plakt u het volgende voorbeeld in een cel om het schema op te geven.

    ```python
    # Specify the index schema
    name = index_name
    fields = [
            SimpleField(name="HotelId", type=SearchFieldDataType.String, key=True),
            SearchableField(name="HotelName", type=SearchFieldDataType.String, sortable=True),
            SearchableField(name="Description", type=SearchFieldDataType.String, analyzer_name="en.lucene"),
            SearchableField(name="Description_fr", type=SearchFieldDataType.String, analyzer_name="fr.lucene"),
            SearchableField(name="Category", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
        
            SearchableField(name="Tags", collection=True, type=SearchFieldDataType.String, facetable=True, filterable=True),
    
            SimpleField(name="ParkingIncluded", type=SearchFieldDataType.Boolean, facetable=True, filterable=True, sortable=True),
            SimpleField(name="LastRenovationDate", type=SearchFieldDataType.DateTimeOffset, facetable=True, filterable=True, sortable=True),
            SimpleField(name="Rating", type=SearchFieldDataType.Double, facetable=True, filterable=True, sortable=True),
    
            ComplexField(name="Address", fields=[
                SearchableField(name="StreetAddress", type=SearchFieldDataType.String),
                SearchableField(name="City", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="StateProvince", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="PostalCode", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="Country", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
            ])
        ]
    cors_options = CorsOptions(allowed_origins=["*"], max_age_in_seconds=60)
    scoring_profiles = []
    suggester = [{'name': 'sg', 'source_fields': ['Tags', 'Address/City', 'Address/Country']}]
    ```

1. Formuleer de aanvraag in een andere cel. Deze create_index-aanvraag is gericht op de verzameling indices van uw zoekservice en hiermee wordt een [SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) (zoekindex) gemaakt op basis van het indexschema dat u in de vorige cel hebt opgegeven.

    ```python
    index = SearchIndex(
        name=name,
        fields=fields,
        scoring_profiles=scoring_profiles,
        suggesters = suggester,
        cors_options=cors_options)
    
    try:
        result = admin_client.create_index(index)
        print ('Index', result.name, 'created')
    except Exception as ex:
        print (ex)
    ```

1. Voer elke stap uit.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Documenten laden

Voor het laden van documenten maakt u een verzameling documenten met behulp van een [indexactie](/python/api/azure-search-documents/azure.search.documents.models.indexaction) voor het bewerkingstype (uploaden, samenvoegen en uploaden, enzovoort). Documenten zijn afkomstig van [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) op GitHub.

1. In een nieuwe cel geeft u vier documenten op die het indexschema bevestigen. Geef voor elk document een uploadactie op.

    ```python
    documents = [
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
    ```  

1. Formuleer de aanvraag in een andere cel. Deze upload_documents-aanvraag is gericht op de verzameling documenten van de index hotels-quickstart en pusht de documenten die in de vorige stap zijn opgegeven naar de Cognitive Search-index.

    ```python
    try:
        result = search_client.upload_documents(documents=documents)
        print("Upload of new document succeeded: {}".format(result[0].succeeded))
    except Exception as ex:
        print (ex.message)
    ```

1. Voer elke stap uit om de documenten naar een index in uw zoekservice te pushen.

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

In deze stap ziet u hoe u een query uitvoert op een index met behulp van de **Zoek** methode van de [klasse Search. client](/python/api/azure-search-documents/azure.search.documents.searchclient).

1. In de volgende stap wordt een lege zoek opdracht uitgevoerd ( `search=*` ), waarbij een niet-gerangte lijst (zoek score = 1,0) met wille keurige documenten wordt geretourneerd. Omdat er geen criteria zijn, worden alle documenten opgenomen in de resultaten. Met deze query worden slechts twee velden in elk document afgedrukt. Er wordt ook `include_total_count=True` toegevoegd om een telling van alle documenten (4) in de resultaten te krijgen.

    ```python
    results =  search_client.search(search_text="*", include_total_count=True)
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. Met de volgende query worden volledige termen toegevoegd aan de zoekexpressie ("wifi"). Met deze query wordt opgegeven dat de resultaten alleen die velden bevatten in de `select`-instructie. Het beperken van de velden die worden geretourneerd, minimaliseert de hoeveelheid gegevens die via de kabel wordt verzonden en vermindert de zoeklatentie.

    ```python
    results =  search_client.search(search_text="wifi", include_total_count=True, select='HotelId,HotelName,Tags')
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}: {}".format(result["HotelId"], result["HotelName"], result["Tags"]))
    ```

1. Pas vervolgens een filterexpressie toe waarmee alleen die hotels worden geselecteerd die een hogere waardering hebben dan 4, gesorteerd in aflopende volgorde.

    ```python
    results =  search_client.search(search_text="hotels", select='HotelId,HotelName,Rating', filter='Rating gt 4', order_by='Rating desc')
    
    for result in results:
        print("{}: {} - {} rating".format(result["HotelId"], result["HotelName"], result["Rating"]))
    ```

1. Voeg `search_fields` toe aan een bereikquery die overeenkomt met één veld.

    ```python
    results =  search_client.search(search_text="sublime", search_fields='HotelName', select='HotelId,HotelName')
    
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. Facetten zijn labels die kunnen worden gebruikt voor het samenstellen van een facetnavigatiestructuur. Met deze query worden facetten en tellingen per categorie geretourneerd.

    ```python
    results =  search_client.search(search_text="*", facets=["Category"])
    
    facets = results.get_facets()
    
    for facet in facets["Category"]:
        print("    {}".format(facet))
    ```

1. Zoek in dit voorbeeld een specifiek document op basis van de bijbehorende sleutel. Normaal gesproken wilt u een document retourneren wanneer een gebruiker op een document klikt in een zoekresultaat.

    ```python
    result = search_client.get_document(key="3")
    
    print("Details for hotel '3' are:")
    print("Name: {}".format(result["HotelName"]))
    print("Rating: {}".format(result["Rating"]))
    print("Category: {}".format(result["Category"]))
    ```

1. In dit voorbeeld gebruiken we de functie automatisch aanvullen. Deze wordt meestal gebruikt in een zoekvak om datgene wat de gebruiker in het zoekvak typt, automatisch aan te vullen met mogelijke overeenkomsten.

   Bij het maken van de index is er ook een suggestieprogramma met de naam 'sg' gemaakt als onderdeel van de aanvraag. Een suggestieprogrammadefinitie geeft aan welke velden kunnen worden gebruikt om mogelijke overeenkomsten te vinden voor aanvragen. In dit voorbeeld zijn dat de velden Tags, Adres/plaats en Adres/land. Als u automatisch aanvullen wilt simuleren, geeft u de letters 'sa' als een gedeeltelijke tekenreeks door. Met de methode voor automatisch aanvullen van [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) worden mogelijke overeenkomende termen geretourneerd.

    ```python
    search_suggestion = 'sa'
    results = search_client.autocomplete(search_text=search_suggestion, suggester_name="sg", mode='twoTerms')
    
    print("Autocomplete for:", search_suggestion)
    for result in results:
        print (result['text'])
    ```

## <a name="clean-up"></a>Opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Om het iets eenvoudiger te maken, wordt in deze quickstart een beknopte versie van de Hotels-index gebruikt. U kunt de volledige versie maken om meer interessante query's te testen. U kunt de volledige versie en alle 50 documenten ophalen door de wizard **Gegevens importeren** uit te voeren en *hotels-sample* te selecteren bij de meegeleverde voorbeeldgegevensbronnen.

> [!div class="nextstepaction"]
> [Snelstart: Een index maken in Azure Portal](search-get-started-portal.md)