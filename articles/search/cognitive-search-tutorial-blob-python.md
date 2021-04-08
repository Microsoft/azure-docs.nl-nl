---
title: 'Zelfstudie: Python en AI over Azure-blobs'
titleSuffix: Azure Cognitive Search
description: Bekijk een voorbeeld van tekstextractie en natuurlijke taalverwerking over inhoud in blob-opslag met behulp van een Jupyter Python-notebook en de Azure Cognitive Search REST-API's.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-python
ms.openlocfilehash: ea1cc022cbea7dbf3d1fa12cb83cfe3084b28560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788078"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Zelfstudie: Python en AI gebruiken voor het genereren van doorzoekbare inhoud van Azure-blobs

Als u ongestructureerde tekst of afbeeldingen in Azure Blob-opslag hebt, kunt u met een [AI-verrijkingspijplijn](cognitive-search-concept-intro.md) informatie extraheren en nieuwe inhoud maken die nuttig is voor zoekopdrachten in volledige tekst of kennisanalyse. Hoewel een pijplijn installatiekopieën kan verwerken, wordt in deze Python-zelfstudie aandacht besteed aan tekst, het toepassen van taaldetectie en de verwerking van natuurlijke taal om nieuwe velden te maken die u kunt gebruiken in query's, facetten en filters.

Deze zelfstudie maakt gebruik van Python en de [REST-API's zoeken](/rest/api/searchservice/) om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Begin met hele documenten (ongestructureerde tekst) zoals PDF, HTML, DOCX en PPTX in Azure Blob-opslag.
> * Definieer een pijplijn die tekst extraheert, taal detecteert, entiteiten herkent en sleutelzinnen detecteert.
> * Definieer een index voor het opslaan van de uitvoer (onbewerkte inhoud, plus pijplijn gegenereerde naam/waardeparen).
> * Voer de pijplijn uit om transformaties en analyses te starten en om de index te maken en te laden.
> * Bekijk de resultaten met zoeken in volledige tekst en een uitgebreide query-syntaxis.

Als u geen abonnement op Azure hebt, opent u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3.7](https://www.anaconda.com/distribution/#download-section)
+ [Maak](search-create-service-portal.md) of [vind een bestaande zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> U kunt de gratis service voor deze zelfstudie gebruiken. Een gratis zoekservice beperkt u tot drie indexen, drie indexeerfuncties en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u aan de slag gaat, zorg ervoor dat uw service voldoende ruimte heeft voor de nieuwe resources.

## <a name="download-files"></a>Bestanden downloaden

1. Open deze [OneDrive-map](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) en klik in de linkerbovenhoek op **Downloaden** om de bestanden naar uw computer te kopiëren. 

1. Klik met de rechtermuisknop op het zip-bestand en selecteer **Alles extraheren**. Er zijn 14 bestanden van verschillende typen. U gebruikt 7 voor deze oefening.

## <a name="1---create-services"></a>1- Services maken

In deze zelfstudie maakt gebruik van Azure Cognitive Search voor het indexeren en uitvoeren van query's, Cognitive Services op de back-end voor AI-verrijking en Azure Blob-opslag om de gegevens op te geven. Deze zelfstudie bevindt zich onder de gratis toewijzing van 20 transacties per indexeerfunctie per dag op Cognitive Services, dus de enige services die u moet maken, zijn zoeken en opslag.

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

1. Klik op **En container** om een container te maken en geef deze de naam *cog-search-demo*.

1. Selecteer *cog-search-demo* en klik vervolgens op **Uploaden** om de map te openen waar u de downloadbestanden hebt opgeslagen. Selecteer alle niet-afbeeldingsbestanden. U moet 7 bestanden hebben. Klik op **OK** om deze te uploaden.

   :::image type="content" source="media/cognitive-search-tutorial-blob/sample-files.png" alt-text="Voorbeeldbestanden uploaden" border="false":::

1. Voordat u Azure Storage verlaat, moet u een verbindingsreeks ophalen zodat u een verbinding in Azure Cognitive Search kunt formuleren. 

   1. Ga terug naar de pagina Overzicht van uw opslagaccount (we hebben *blobstragewestus* als voorbeeld gebruikt). 
   
   1. Selecteer in het navigatiedeelvenster links de optie **Toegangstoetsen** en kopieer een van de verbindingsreeksen. 

   De verbindingsreeks is een URL die er ongeveer als volgt uitziet:

      ```http
      DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Sla de verbindingsreeks op in Kladblok. U hebt deze later nodig bij het instellen van de gegevensbronverbinding.

### <a name="cognitive-services"></a>Cognitive Services

AI-verrijking wordt ondersteund door Cognitive Services, waaronder Text Analytics en Computer Vision voor de verwerking van natuurlijke taal en afbeeldingen. Als u een echt prototype of project wilt voltooien, moet u op dit punt Cognitive Services (in dezelfde regio als Azure Cognitive Search) inrichten, zodat u het kunt koppelen aan indexbewerkingen.

Aangezien deze zelfstudie slechts zeven transacties gebruikt, kunt u resource-inrichting overslaan, omdat Azure Cognitive Search verbinding kan maken met Cognitive Services voor 20 gratis transacties per uitvoering van de indexeerfunctie. De gratis toewijzing is voldoende. Voor grotere projecten plant u het inrichten van Cognitive Services op de S0-laag voor betalen per gebruik. Zie voor meer informatie [Cognitive Services koppelen](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Het derde onderdeel is Azure Cognitive Search, dat u kunt [maken in de portal](search-create-service-portal.md). U kunt de gratis laag gebruiken om dit voorbeeld te voltooien. 

Net als bij Azure Blob-opslag duurt het even om de toegangssleutel te verzamelen. Daarnaast moet u, wanneer u begint met het structureren van aanvragen, het eindpunt en de beheer-API-sleutel opgeven die worden gebruikt om elke aanvraag te verifiëren.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een beheer-API-sleutel en URL voor Azure Cognitive Search ophalen

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/) en haal op de pagina **Overzicht** de naam van uw zoekservice op. U kunt uw servicenaam bevestigen door de URL van het eindpunt te controleren. Als uw eindpunt-URL `https://mydemo.search.windows.net` is, wordt uw service naam `mydemo`.

2. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal ook de querysleutel op. Het is een aanbevolen procedure voor het uitgeven van queryaanvragen met alleen-lezen-toegang.

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="De naam van de service en de querysleutels voor beheer ophalen" border="false":::

Voor alle aanvragen is een API-sleutel vereist in de header die naar uw service wordt verzonden. Me een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---start-a-notebook"></a>2 - Een notebook starten

Maak het notebook met behulp van de volgende instructies of download een volledig notebook van [Azure-Search-python-samples opslag plaats](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment).

Gebruik Anaconda Navigator om Jupyter Notebook te starten en een nieuw Python 3-notebook te maken.

In uw notebook voert u dit script uit om de bibliotheken te laden die worden gebruikt om met JSON te werken en HTTP-aanvragen te formuleren.

```python
import json
import requests
from pprint import pprint
```

Definieer in hetzelfde notebook de namen voor de gegevensbron, index, indexeerfunctie en vaardighedenset. Voer dit script uit om de namen in te stellen voor deze zelfstudie.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

Vervang in het volgende script de tijdelijke aanduidingen voor uw zoekservice (UW-ZOEKSERVICE_NAAM) en de beheer-API-sleutel (UW-BEHEER-API-SLEUTEL) en voer deze uit om het eindpunt van de zoekservice in te stellen.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2020-06-30'
}
```

## <a name="3---create-the-pipeline"></a>3: Maak de pijplijn

In Azure Cognitive Search treedt AI-verwerking op tijdens het indexeren (of gegevensopname). In dit deel van het scenario worden vier objecten gemaakt: gegevensbron, indexdefinitie, vaardighedenset, indexeerfunctie. 

### <a name="step-1-create-a-data-source"></a>Stap 1: Een gegevensbron maken

Een [gegevensbron-object](/rest/api/searchservice/create-data-source) biedt de BLOB-container de verbindingsreeks met de bestanden.

Vervang in het volgende script de tijdelijke aanduiding UW-BLOB-RESOURCE-VERBINDINGSREEKS door de verbindingsreeks voor de blob die u in de vorige stap hebt gemaakt. Vervang de tekst van de tijdelijke aanduiding voor de container. Voer vervolgens het script uit om een gegevensbron te maken met de naam `cogsrch-py-datasource`.

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

De aanvraag zou een 201-statuscode moeten retourneren om de succesvolle uitvoering te bevestigen.

Controleer in de Azure-portal op de pagina dashboard van zoekservice of de cogsrch-py-datasource wordt weergegeven in de lijst **Gegevensbronnen**. Klik op **Vernieuwen** om de pagina bij te werken.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-data-source-tile.png" alt-text="De tegel gegevensbronnen in de portal" border="false":::

### <a name="step-2-create-a-skillset"></a>Stap 2: Een set vaardigheden maken

In deze stap definieert u een reeks verrijkingsstappen om uw gegevens toe te passen. We noemen elke verrijkingsstap een *vaardigheid* en de reeks verrijkingsstappen een *set vaardigheden*. In deze zelfstudie wordt gebruikgemaakt van [ingebouwde cognitieve tekstvaardigheden](cognitive-search-predefined-skills.md) voor de set vaardigheden:

+ [Herkenning van entiteiten](cognitive-search-skill-entity-recognition.md) voor het extraheren van de namen van organisaties uit inhoud van de blobcontainer.

+ [Taaldetectie](cognitive-search-skill-language-detection.md) om de taal van de inhoud vast te stellen.

+ [Tekst splitsen](cognitive-search-skill-textsplit.md) om grote inhoud in kleinere stukken op te delen voordat u de vaardigheid voor sleuteltermextractie aanroept. Sleuteltermextractie accepteert invoeren van 50.000 tekens of minder. Enkele voorbeeldbestanden moeten worden opgesplitst om aan deze limiet te voldoen.

+ [Sleuteltermextractie](cognitive-search-skill-keyphrases.md) voor het ophalen van de belangrijkste sleuteltermen. 

Voer het volgende script uit om een vaardighedenset met de naam `cogsrch-py-skillset` te maken.

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", 
                    "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", 
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

De aanvraag zou een 201-statuscode moeten retourneren om de succesvolle uitvoering te bevestigen.

De vaardigheid voor sleuteltermextractie op elke pagina wordt toegepast. Door de context op `"document/pages/*"` in te stellen, voert u deze verrijker uit voor elk onderdeel van de matrix document/pagina's (voor elke pagina in het document).

Elke vaardigheid wordt uitgevoerd voor de inhoud van het document. Tijdens het verwerken opent Azure Cognitive Search elk document om inhoud van verschillende bestandsindelingen te lezen. Tekst die in het bronbestand is gevonden wordt in een `content`-veld geplaatst, één voor elk document. Stel daarom de invoer in als `"/document/content"`.

Een grafische weergave van de set vaardigheden wordt hieronder weergegeven.

:::image type="content" source="media/cognitive-search-tutorial-blob/skillset.png" alt-text="Een vaardighedenset begrijpen" border="false":::

Uitvoeren kunnen aan een index worden toegewezen, als invoer worden gebruikt voor een downstream-vaardigheid, of beide zoals bij taalcode. Een taalcode is in de index handig om te filteren. Taalcode wordt als invoer door vaardigheden voor tekstanalyse gebruikt om de taalregels voor woordafbreking in te stellen.

Zie [How to define a skillset](cognitive-search-defining-skillset.md) (Een set vaardigheden definiëren) voor meer informatie over de grondbeginselen van vaardigheden.

### <a name="step-3-create-an-index"></a>Stap 3: Een index maken

In dit gedeelte kunt u het indexschema definiëren door de velden op te geven die in de doorzoekbare index moeten worden opgenomen en de zoekkenmerken voor elk veld in te stellen. Velden hebben een type en kunnen kenmerken opnemen die bepalen hoe het veld wordt gebruikt (doorzoekbaar, sorteerbaar enzovoort). Veldnamen in een index hoeven niet identiek te zijn aan de veldnamen in de bron. In een latere stap voegt u veldverwijzingen in een indexeerfunctie toe om bron-doelvelden te verbinden. Definieer voor deze stap de index met behulp van veldnaamconventies die relevant zijn voor uw zoektoepassing.

In deze oefening worden de volgende velden en veldtypen gebruikt:

| field-names: | Id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Voer dit script uit om de index met de naam `cogsrch-py-index`te maken.

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

De aanvraag zou een 201-statuscode moeten retourneren om de succesvolle uitvoering te bevestigen.

Zie [Create Index (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index) (Index maken (Azure Cognitive Search REST API)) voor meer informatie over het definiëren van een index.

### <a name="step-4-create-and-run-an-indexer"></a>Stap 4: Indexeerfunctie maken en uitvoeren

Een [Indexeerfunctie](/rest/api/searchservice/create-indexer) voert de pijplijn aan. De drie onderdelen die u tot nu toe hebt gemaakt (gegevensbron, vaardighedenset, index) zijn invoer naar een indexeerfunctie. Het maken van de indexeerfunctie op Azure Cognitive Search is de gebeurtenis die de volledige pijplijn in beweging zet. 

Als u deze objecten in een indexeerfunctie wilt combineren, moet u veldtoewijzingen definiëren.

+ De `"fieldMappings"` worden vóór de vaardighedenset verwerkt, waarbij bronvelden van de gegevensbron worden toegewezen aan de doelvelden in een index. Als veldnamen en -typen gelijk zijn aan beide uiteinden, is toewijzing niet vereist.

+ De `"outputFieldMappings"` worden verwerkt na de vaardighedenset en verwijzen naar `"sourceFieldNames"` die niet bestaan totdat het document wordt gekraakt of verrijkt. De `"targetFieldName"` is een veld in een index.

Naast het koppelen van invoer naar uitvoer, kunt u ook veldtoewijzingen gebruiken om gegevensstructuren samen te voegen. Zie [Verrijkte velden toewijzen aan een doorzoekbare index](cognitive-search-output-field-mapping.md) voor meer informatie.

Voer dit script uit om een indexeerfunctie met de naam `cogsrch-py-indexer` te maken.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

De aanvraag moet snel een statuscode van 201 retourneren, maar het kan enkele minuten duren voordat de verwerking is voltooid. Hoewel de gegevensset klein is, zijn analytische vaardigheden, zoals het analyseren van afbeeldingen, intensief qua rekenkracht en kosten tijd.

U kunt [de status van de indexeerfunctie bewaken](#check-indexer-status) om te bepalen wanneer de indexeerfunctie wordt uitgevoerd of voltooid.

> [!TIP]
> Het maken van een indexeerfunctie roept de pijplijn aan. Als er problemen zijn met toegang tot de gegevens, het toewijzen van invoeren en uitvoeren of met de volgorde van bewerkingen, worden ze in deze fase weergegeven. Mogelijk moet u eerst objecten verwijderen om de pijplijn opnieuw uit te voeren met code- of scriptwijzigingen. Zie [Reset and re-run](#reset) (Opnieuw instellen en uitvoeren) voor meer informatie.

#### <a name="about-the-request-body"></a>Over de aanvraagbody

Het script stelt `"maxFailedItems"` op -1 in, wat de indexeerengine de opdracht geeft om fouten tijdens het importeren van gegevens te negeren. Dit is nuttig omdat de demo-gegevensbron maar een paar documenten bevat. Voor een grotere gegevensbron zou u de waarde op groter dan 0 instellen.

Let ook op de instructie `"dataToExtract":"contentAndMetadata"` in de configuratieparameters. Deze instructie geeft de indexeerfunctie de opdracht om de inhoud van verschillende bestandsindelingen te extraheren en de metagegevens voor elk bestand.

Wanneer inhoud wordt uitgepakt, kunt u instellen dat `imageAction` tekst ophaalt uit afbeeldingen die in de gegevensbron worden gevonden. De configuratie van `"imageAction":"generateNormalizedImages"`, OCR Skill en Text Merge Skill geeft de indexeerfunctie de opdracht om tekst uit de afbeeldingen te extraheren (bijvoorbeeld het woord 'stop' van een Stop-verkeersbord) en deze in te sluiten als onderdeel van het inhoudsveld. Dit gedrag is van toepassing op zowel de afbeeldingen die zijn ingesloten in de documenten (zoals een afbeelding in een pdf-bestand) en afbeeldingen die zijn gevonden in de gegevensbron, zoals een JPG-bestand.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4: Het indexeren bewaken

Nadat de indexeerfunctie is gedefinieerd, wordt deze automatisch uitgevoerd wanneer u de aanvraag verzendt. Afhankelijk van welke cognitieve vaardigheden u hebt gedefinieerd, kan het indexeren langer duren dan verwacht. Als u wilt weten of de verwerking van de indexeerfunctie is voltooid, voert u het volgende script uit.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Controleer in het antwoord de `"lastResult"` op de waarden `"status"` en `"endTime"`. Voer periodiek het script uit om de status te controleren. Als de indexeerfunctie is voltooid, wordt de status ingesteld op "geslaagd", wordt "endTime" opgegeven en bevat het antwoord eventuele fouten en waarschuwingen die zijn opgetreden tijdens het verrijken.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png" alt-text="Indexeerfunctie is gemaakt" border="false":::

Waarschuwingen komen veel voor bij sommige combinaties van bronbestand en vaardigheid en wijzen niet altijd op een probleem. Veel waarschuwingen zijn onschadelijk. Als u bijvoorbeeld een JPEG-bestand indexeert dat geen tekst bevat, ziet u de waarschuwing in deze schermopname.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png" alt-text="Voorbeeld van indexeerfunctiewaarschuwing" border="false":::

## <a name="5---search"></a>5 - Zoeken

Voer nadat het indexeren is voltooid query's uit waarmee de inhoud van afzonderlijke velden wordt geretourneerd. Standaard retourneert Azure Cognitive Search de 50 hoogste resultaten. De voorbeeldgegevens zijn beperkt, dus de standaardinstelling werkt prima. Als u echter werkt met grotere gegevenssets, moet u mogelijk parameters opnemen in de queryreeks om meer resultaten te retourneren. Zie [How to page results in Azure Cogntive Search](search-pagination-page-layout.md) (Resultaten genereren in Azure Cognitive Search) voor instructies.

Als verificatiestap haalt u de indexdefinitie op waarin alle velden worden weergegeven.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

De resultaten moeten er ongeveer uitzien als in het volgende voorbeeld. In de schermopname wordt alleen een deel van het antwoord weergegeven.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png" alt-text="Query-index voor alle velden" border="false":::

De uitvoer is het schema van de index met de naam, het type en de kenmerken van elk veld.

Verzend een tweede query voor `"*"` om alle inhoud te retourneren van één veld, zoals `organizations`.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

De resultaten moeten er ongeveer uitzien als in het volgende voorbeeld. In de schermopname wordt alleen een deel van het antwoord weergegeven.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png" alt-text="Query-index voor de inhoud van organisaties" border="false":::

Herhaal dit voor aanvullende velden: `content`, `languageCode`, `keyPhrases` en `organizations` in deze oefening. U kunt meerdere velden retourneren via `$select` met behulp van een door komma's gescheiden lijst.

U kunt GET of POST gebruiken, afhankelijk van de complexiteit en lengte van de queryreeks. Zie [Query using the REST API](/rest/api/searchservice/search-documents) (Query's uitvoeren met de REST API) voor meer informatie.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de eerste experimentele fasen van ontwikkeling, is de meest praktische aanpak voor ontwerpiteraties om de objecten uit Azure Cognitive Search te verwijderen en uw code ze te laten herbouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

U kunt de portal gebruiken om indexen, indexeerfuncties, gegevensbronnen en vaardighedensets te verwijderen. Wanneer u de indexeerfunctie verwijdert, kunt u eventueel de index, de vaardighedenset en de gegevensbron selectief en tegelijkertijd verwijderen.

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png" alt-text="Zoekobjecten verwijderen in de portal" border="false":::

U kunt ze ook verwijderen met een script. Het volgende script toont hoe u een vaardighedenset verwijdert. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

De statuscode 204 wordt na verwijdering geretourneerd.

## <a name="takeaways"></a>Opgedane kennis

In deze zelfstudie zijn de basisstappen voor het bouwen van een pijplijn voor verrijkte indexering gedemonstreerd via het maken van onderdelen: een gegevensbron, een set vaardigheden, een index en een indexeerfunctie.

[Ingebouwde vaardigheden](cognitive-search-predefined-skills.md) zijn geïntroduceerd, samen met de definities van de set vaardigheden en een manier voor het koppelen van vaardigheden via in- en uitvoeren. U hebt ook geleerd dat `outputFieldMappings` vereist is in de definitie van de indexeerfunctie voor de routering van verrijkte waarden uit de pijplijn naar een doorzoekbare index op een Azure Cognitive Search-service.

Tot slot hebt u geleerd hoe u de resultaten kunt testen en het systeem opnieuw kunt instellen voor verdere iteraties. U hebt geleerd dat het uitvoeren van query's op de index de uitvoer retourneert die is gemaakt door de pijplijn voor verrijkte indexering. Deze release bevat een mechanisme voor het weergeven van interne constructies (verrijkte documenten die zijn gemaakt door het systeem). U hebt ook geleerd hoe u de status van de indexeerfunctie kunt controleren en welke objecten verwijdert moeten worden voordat u een pijplijn opnieuw uitvoert.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt en of u deze moet verwijderen. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling Alle resources of Resourcegroepen in het navigatiedeelvenster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met alle objecten in een AI-verrijkingspijplijn, gaan we kijken naar de definities van vaardighedensets en individuele vaardigheden.

> [!div class="nextstepaction"]
> [Een vaardighedenset maken](cognitive-search-defining-skillset.md)