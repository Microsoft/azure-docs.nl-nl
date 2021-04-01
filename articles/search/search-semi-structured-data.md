---
title: 'Zelfstudie: Semi-gestructureerde gegevens in JSON-blobs indexeren'
titleSuffix: Azure Cognitive Search
description: Meer informatie over het indexeren en doorzoeken van semi-gestructureerde Azure JSON-blobs met behulp van Azure Cognitive Search REST API's en Postman.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: a7a010e3c60d6b96947597878fcd870e9845b2b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98746134"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Zelfstudie: JSON-blobs vanuit Azure Storage indexeren met behulp van REST

Met Azure Cognitive Search kunnen JSON-documenten en matrices in Azure Blob Storage worden geïndexeerd met behulp van een [indexeerfunctie](search-indexer-overview.md) die weet hoe semi-gestructureerde gegevens moeten worden gelezen. Semi-gestructureerde gegevens bevatten labels of markeringen die inhoud in de gegevens scheiden. Hierin ligt ook het verschil tussen niet-gestructureerde gegevens en formeel gestructureerde gegevens. Niet-gestructureerde gegevens moeten als geheel worden geïndexeerd, gestructureerde gegevens zijn in overeenstemming met een gegevensmodel, zoals een relationele-databaseschema, en kunnen op veldbasis worden geïndexeerd.

Deze zelfstudie maakt gebruik van Postman en de [REST API's zoeken](/rest/api/searchservice/) om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een Azure Cognitive Search-gegevensbron configureren voor een Azure-blobcontainer
> * Een Azure Cognitive Search-index maken met doorzoekbare inhoud
> * Een indexeerfunctie configureren en uitvoeren voor het lezen van de container en het extraheren van doorzoekbare inhoud uit Azure Blob Storage
> * De index doorzoeken die u zojuist hebt gemaakt

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Postman bureaublad-app](https://www.getpostman.com/)
+ [Maak](search-create-service-portal.md) of [vind een bestaande zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> U kunt de gratis service voor deze zelfstudie gebruiken. Een gratis zoekservice beperkt u tot drie indexen, drie indexeerfuncties en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u aan de slag gaat, zorg ervoor dat uw service voldoende ruimte heeft voor de nieuwe resources.

## <a name="download-files"></a>Bestanden downloaden

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) bevat de gegevens die in deze zelfstudie worden gebruikt. Download dit bestand en pak het uit in een eigen map. De gegevens zijn afkomstig van [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results), die voor deze zelfstudie zijn omgezet naar JSON.

## <a name="1---create-services"></a>1- Services maken

In deze zelfstudie wordt Azure Cognitive Search gebruikt voor het indexeren en het uitvoeren van query's en Azure Blob Storage om de gegevens op te geven. 

Maak, indien mogelijk, beide in dezelfde regio en resourcegroep voor nabijheid en beheerbaarheid. In de praktijk kan uw Azure Storage-account zich in een willekeurige regio bevinden.

### <a name="start-with-azure-storage"></a>Aan de slag met Azure Storage

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/) en klik op **En resourcegroepen maken**.

1. Zoek naar *Opslagaccount* en selecteer de aanbieding van het Microsoft-opslagaccount.

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="Een opslagaccount maken" border="false":::

1. Op het tabblad Basisinstellingen zijn de volgende items vereist. Accepteer de standaardwaarden voor alle andere.

   + **Resourcegroep**. Selecteer een bestaande naam of maak een nieuwe, maar gebruik dezelfde groep voor alle services, zodat u ze gezamenlijk kunt beheren.

   + **Naam van opslagaccount**. Als u denkt dat u mogelijk meerdere resources van hetzelfde type hebt, gebruikt u de naam om op type en regio te onderscheiden, bijvoorbeeld *blobstoragewestus*. 

   + **Locatie**. Kies indien mogelijk dezelfde locatie die wordt gebruikt voor Azure Cognitive Search en Cognitive Services. Een enkele locatie vermindert bandbreedtekosten aanzienlijk.

   + **Soort account**. Kies de standaardinstelling *StorageV2 (algemeen gebruik v2)* .

1. Klik vervolgens op **Beoordelen en maken** om de service te maken.

1. Zodra de app is gemaakt, klikt u op **Ga naar de resource** om de pagina Overzicht te openen.

1. Klik op **Blobs**-service.

1. [Maak een blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md) waarin u voorbeeldgegevens gaat toevoegen. U kunt het niveau voor openbare toegang instellen op een van de geldige waarden.

1. Nadat de container is gemaakt, opent u deze en selecteert u **Uploaden** op de opdrachtbalk.

   :::image type="content" source="media/search-semi-structured-data/upload-command-bar.png" alt-text="Uploaden op de opdrachtbalk" border="false":::

1. Navigeer naar de map met de voorbeeldbestanden. Selecteer deze allemaal en klik vervolgens op **Uploaden**.

   :::image type="content" source="media/search-semi-structured-data/clinicalupload.png" alt-text="Bestanden uploaden" border="false":::

Nadat de upload is voltooid, worden de bestanden weergegeven in hun eigen submap in de gegevenscontainer.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

De volgende resource is Azure Cognitive Search, die u kunt [maken in de portal](search-create-service-portal.md). U kunt de gratis laag gebruiken om dit voorbeeld te voltooien. 

Net als bij Azure Blob-opslag duurt het even om de toegangssleutel te verzamelen. Daarnaast moet u, wanneer u begint met het structureren van aanvragen, het eindpunt en de beheer-API-sleutel opgeven die worden gebruikt om elke aanvraag te verifiëren.

### <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Een HTTP-eindpunt en toegangssleutel ophalen" border="false":::

Voor alle aanvragen is een API-sleutel vereist op elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---set-up-postman"></a>2 - Postman instellen

Start Postman en stel een HTTP-aanvraag in. Als u niet bekend bent met dit hulp programma, raadpleegt u [een zoek index maken met behulp van rest-api's](search-get-started-rest.md).

De aanvraagmethoden voor elke aanroep in deze zelfstudie zijn **POST** en **GET**. U maakt drie API-aanroepen naar uw zoekservice om een gegevensbron, een index en een indexeerfunctie te maken. De gegevensbron bevat een verwijzing naar uw opslagaccount en uw JSON-gegevens. Uw zoekservice maakt de verbinding tijdens het laden van de gegevens.

Stel bij Headers de optie Inhoudstype in op `application/json` en stel `api-key` in op de API-sleutel voor beheerders van uw Azure Cognitive Search-service. Zodra u de headers hebt ingesteld, kunt u ze gebruiken voor elke aanvraag in deze oefening.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="URL en header voor Postman-aanvraag" border="false":::

Met behulp van URI's moet een API-versie worden opgegeven en elke aanroep moet **201 - Gemaakt** retourneren. De algemeen beschikbare API-versie voor het gebruik van JSON-matrices is `2020-06-30`.

## <a name="3---create-a-data-source"></a>3 - Een gegevensbron maken

Met de [Create Data Source-API](/rest/api/searchservice/create-data-source) wordt een Azure Cognitive Search-object gemaakt waarmee wordt aangegeven welke gegevens moeten worden geïndexeerd.

1. Stel het eindpunt van deze aanroep in op `https://[service name].search.windows.net/datasources?api-version=2020-06-30`. Vervang `[service name]` door de naam van uw zoekservice. 

1. Kopieer de volgende JSON naar de aanvraagbody.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Vervang de verbindingsreeks door een geldige tekenreeks voor uw account.

1. Vervang [blob container name] door de container die u hebt gemaakt voor de voorbeeldgegevens. 

1. Verzend de aanvraag. Het antwoord moet er als volgt uitzien:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
        },
        "container": {
            "name": "[mycontainernamehere]",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="4---create-an-index"></a>4 - Een index maken

De tweede aanroep is [Create Index-API](/rest/api/searchservice/create-index), waarmee een Azure Cognitive Search-index wordt gemaakt waarin alle doorzoekbare gegevens worden opgeslagen. Een index geeft alle parameters en hun kenmerken op.

1. Stel het eindpunt van deze aanroep in op `https://[service name].search.windows.net/indexes?api-version=2020-06-30`. Vervang `[service name]` door de naam van uw zoekservice.

1. Kopieer de volgende JSON naar de aanvraagbody.

    ```json
    {
      "name": "clinical-trials-json-index",  
      "fields": [
      {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
      {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
      {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
      ]
    }
   ```

1. Verzend de aanvraag. Het antwoord moet er als volgt uitzien:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag": "\"0x8D505FC00EDD5FA\"",
        "name": "clinical-trials-json-index",
        "fields": [
            {
                "name": "FileName",
                "type": "Edm.String",
                "searchable": false,
                "filterable": false,
                "retrievable": true,
                "sortable": true,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "Description",
                "type": "Edm.String",
                "searchable": true,
                "filterable": false,
                "retrievable": false,
                "sortable": false,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            ...
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 - Een indexeerfunctie maken en uitvoeren

Een indexeerfunctie maakt verbinding met de gegevensbron, importeert gegevens in de doelzoekindex en biedt optioneel een schema om het vernieuwen van de gegevens te automatiseren. De REST API is [Create Indexer](/rest/api/searchservice/create-indexer).

1. Stel de URI voor deze aanroep in op `https://[service name].search.windows.net/indexers?api-version=2020-06-30`. Vervang `[service name]` door de naam van uw zoekservice.

1. Kopieer de volgende JSON naar de aanvraagbody.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Verzend de aanvraag. De aanvraag wordt onmiddellijk verwerkt. Wanneer het antwoord terugkomt, beschikt u over een index waarin op volledige tekst kan worden gezocht. Het antwoord moet er als volgt uitzien:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
        "targetIndexName": "clinical-trials-json-index",
        "schedule": null,
        "parameters": {
            "batchSize": null,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonArray"
            }
        },
        "fieldMappings": [],
        "enrichers": [],
        "disabled": null
    }
    ```

## <a name="6---search-your-json-files"></a>6 - Uw JSON-bestanden doorzoeken

Zodra het eerste document is geladen, kunt u meteen beginnen met zoeken.

1. Wijzig de bewerking in **GET**.

1. Stel de URI voor deze aanroep in op `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2020-06-30&$count=true`. Vervang `[service name]` door de naam van uw zoekservice.

1. Verzend de aanvraag. Dit is een niet-opgegeven query voor zoeken op volledige tekst waarmee alle velden worden opgehaald die als Kan worden opgehaald zijn gemarkeerd in de index, samen met het aantal documenten. Het antwoord moet er als volgt uitzien:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. Voeg de queryparameter `$select` toe om de resultaten te beperken tot een kleiner aantal velden: `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2020-06-30&$count=true`.  Voor deze query komen 100 documenten overeen, maar standaard worden met Azure Cognitive Search niet meer dan 50 documenten in de resultaten geretourneerd.

   :::image type="content" source="media/search-semi-structured-data/lastquery.png" alt-text="Geparameteriseerde query" border="false":::

1. Een voorbeeld van een complexere query omvat `$filter=MinimumAge ge 30 and MaximumAge lt 75`. Deze retourneert alleen resultaten als de parameter MinimumAge groter is dan of gelijk is aan 30 en als MaximumAge kleiner is dan 75. Vervang de `$select`-expressie door de `$filter`-expressie.

   :::image type="content" source="media/search-semi-structured-data/metadatashort.png" alt-text="Semi-gestructureerde zoekopdracht" border="false":::

U kunt logische operators (AND, OR, NOT) en vergelijkingsoperators (eq, ne, gt, lt, ge en le) gebruiken. Tekenreeksvergelijkingen zijn hoofdlettergevoelig. Zie [Een eenvoudige query maken](search-query-simple-examples.md) voor meer informatie en voorbeelden.

> [!NOTE]
> De parameter `$filter` werkt alleen met metagegevens die bij het maken van de index zijn gemarkeerd als filterbaar.

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de eerste experimentele fasen van ontwikkeling, is de meest praktische aanpak voor ontwerpiteraties om de objecten uit Azure Cognitive Search te verwijderen en uw code ze te laten herbouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

U kunt de portal gebruiken om indexen, indexeerfuncties en gegevensbronnen te verwijderen. Of gebruik **DELETE** en geef voor elk object een URL op. Met de volgende opdracht wordt een indexeerfunctie verwijderd.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2020-06-30
```

De statuscode 204 wordt na verwijdering geretourneerd.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt en of u deze moet verwijderen. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling Alle resources of Resourcegroepen in het navigatiedeelvenster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met de basisprincipes van het indexeren van Azure-blobs, gaan we de configuratie van de indexeerfunctie voor JSON-blobs in Azure Storage nader bekijken.

> [!div class="nextstepaction"]
> [Het indexeren van JSON-blobs configureren](search-howto-index-json-blobs.md)