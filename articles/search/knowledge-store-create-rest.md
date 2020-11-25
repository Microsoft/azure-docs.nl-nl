---
title: Een kennisarchief maken met behulp van REST
titleSuffix: Azure Cognitive Search
description: Gebruik de REST API en Postman om een Azure Cognitive Search-kennisarchief te maken voor persistente verrijkingen van een AI-verrijkingspijplijn.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/18/2020
ms.openlocfilehash: 6af9b8f97d622ae10cfdbcaa8ca50abb42ec7332
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94889049"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Een kennisarchief maken met behulp van REST en Postman

Een kennisarchief bevat uitvoer van een Azure Cognitive Search-verrijkingspijplijn voor latere analyses of andere verwerking in een later stadium. Een met AI verrijkte pijplijn accepteert afbeeldingsbestanden of ongestructureerde tekstbestanden, indexeert deze met behulp van Azure Cognitive Search, past AI-verrijkingen van Cognitive Services (zoals afbeeldingsanalyse en natuurlijke taalverwerking) toe en slaat de resultaten vervolgens op in een kennisarchief in Azure Storage. U kunt hulpprogramma's zoals Power BI of Storage Explorer in Azure Portal gebruiken om het kennisarchief te verkennen.

In dit artikel gebruikt u de REST API-interface om AI-verrijkingen op te nemen, te indexeren en toe te passen op een aantal hotelbeoordelingen. De hotelbeoordelingen worden geïmporteerd in Azure Blob-opslag. De resultaten worden opgeslagen als een kennisarchief in Azure-tabelopslag.

Nadat u het kennisarchief hebt gemaakt, kunt u leren hoe u toegang tot het kennisarchief krijgt met behulp van [Storage Explorer](knowledge-store-view-storage-explorer.md) of [Power BI](knowledge-store-connect-power-bi.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!TIP]
> U kunt het beste de [Postman-bureaublad-app](https://www.getpostman.com/) gebruiken voor dit artikel. De [broncode](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) voor dit artikel bevat een Postman-verzameling met alle aanvragen. 

## <a name="create-services-and-load-data"></a>Services maken en gegevens laden

Voor deze quickstart wordt gebruik gemaakt van Azure Cognitive Search, Azure Blob Storage en [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) voor de AI. 

Omdat de workload zo klein is, wordt achter de schermen gebruikgemaakt van Cognitive Services voor gratis verwerking van maximaal 20 transacties per dag. Omdat de gegevensset zo klein is, kunt u het maken of koppelen van een Cognitive Services-resource overslaan.

1. [Download HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Dit zijn gegevens van hotelbeoordelingen die zijn opgeslagen in een CSV-bestand (oorsprong Kaggle.com). Ze bevatten 19 items met klantenfeedback over een hotel. 

1. [Maak een Azure Storage-account](../storage/common/storage-account-create.md?tabs=azure-portal) of [zoek een bestaand account](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) onder uw huidige abonnement. U gebruikt Azure Storage zowel voor de onbewerkte inhoud die u wilt importeren als het kennisarchief dat het eindresultaat vormt.

   Kies het accounttype **StorageV2 (algemeen gebruik V2)** .

1. Open de Blob-servicepagina's en maak een container met de naam *hotel-beoordelingen*.

1. Klik op **Uploaden**.

    ![De gegevens uploaden](media/knowledge-store-create-portal/upload-command-bar.png "De hotelrecensies uploaden")

1. Selecteer het bestand **HotelReviews-Free.csv** dat u in de eerste stap hebt gedownload.

    ![De Azure-blobcontainer maken](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "De Azure-blobcontainer aanmaken")

1. U bent bijna klaar met deze resource, maar voordat u deze pagina's verlaat, moet u de **Toegangssleutels** nog openen via de koppelingen in het navigatiedeelvenster aan de linkerkant. Gebruik een verbindingsreeks om gegevens op te halen uit de Blob-opslag. Een verbindingsreeks ziet er ongeveer als volgt uit: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Schakel over naar Azure Cognitive Search terwijl u nog in de portal bent. [Maak een nieuwe service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). U kunt een gratis service gebruiken voor deze oefening.

## <a name="configure-postman"></a>Postman configureren

Postman installeren en instellen.

### <a name="download-and-install-postman"></a>Postman downloaden en installeren

1. Download de [broncode van de Postman-verzameling](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Selecteer **Bestand** > **Importeren** om de broncode te importeren in Postman.
1. Selecteer het tabblad **Verzamelingen** en selecteer vervolgens de knop met het beletselteken **...** .
1. Selecteer **Bewerken**. 
   
   ![Postman-app met navigatie](media/knowledge-store-create-rest/postman-edit-menu.png "Ga naar het menu Bewerken in Postman")
1. Selecteer in het dialoogvenster **Bewerken** het tabblad **Variabelen**. 

U kunt op het tabblad **Variabelen** waarden toevoegen die Postman steeds verwisselt wanneer een specifieke variabele wordt aangetroffen binnen dubbele accolades. Postman vervangt bijvoorbeeld het symbool `{{admin-key}}` door de huidige waarde die u hebt ingesteld voor `admin-key`. Postman vervangt de URL's, kopteksten, de hoofdtekst van de aanvraag, enzovoort. 

Ga naar de Azure Cognitive Search-service en selecteer het tabblad **Sleutels** om de waarde voor `admin-key` op te halen. Wijzig `search-service-name` en `storage-account-name` door de waarden die u hebt gekozen in [Services maken](#create-services-and-load-data). Stel `storage-connection-string` in met behulp van de waarde op het tabblad **Toegangssleutels** van het opslagaccount. U kunt voor de overige waarden de standaardinstellingen laten staan.

![Het tabblad met app-variabelen in Postman](media/knowledge-store-create-rest/postman-variables-window.png "Het venster met variabelen in Postman")


| Variabele    | Waar te verkrijgen? |
|-------------|-----------------|
| `admin-key` | Op de pagina **Sleutels** van de Azure Cognitive Search-service.  |
| `api-version` | Laat deze staan op **2020-06-30**. |
| `datasource-name` | Laat deze staan op **hotel-reviews-ds**. | 
| `indexer-name` | Laat deze staan op **hotel-reviews-ixr**. | 
| `index-name` | Laat deze staan op **hotel-reviews-ix**. | 
| `search-service-name` | De naam van de Azure Cognitive Search-service. De URL is `https://{{search-service-name}}.search.windows.net`. | 
| `skillset-name` | Laat deze staan op **hotel-reviews-ss**. | 
| `storage-account-name` | Naam van het Azure-opslagaccount. | 
| `storage-connection-string` | Selecteer in het opslagaccount op het tabblad **Toegangssleutels** de **key1** >  **-verbindingstekenreeks**. | 
| `storage-container-name` | Laat deze staan op **hotel-reviews**. | 

### <a name="review-the-request-collection-in-postman"></a>De verzameling aanvragen weergeven in Postman

Als u een kennisarchief maakt, moet u vier HTTP-aanvragen indienen: 

- **PUT-aanvraag om de index te maken**: Deze index bevat de gegevens die door Azure Cognitive Search worden gebruikt en geretourneerd.
- **Een POST-aanvraag voor het maken van de gegevensbron**: Deze gegevensbron verbindt uw Azure Cognitive Search-gedrag met het opslagaccount van de gegevens en het kennisarchief. 
- **PUT-aanvraag om de vaardighedenset te maken**: De vaardighedenset bevat de verrijkingen die worden toegepast op uw gegevens en de structuur van het kennisarchief.
- **PUT-aanvraag om de indexeerfunctie te maken**: Als de indexeerfunctie wordt uitgevoerd, worden de gegevens gelezen, wordt de vaardighedenset toegepast en worden de resultaten opgeslagen. U moet deze aanvraag als laatste uitvoeren.

De [broncode](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) bevat een Postman-verzameling die de vier aanvragen bevat. Selecteer in Postman het tabblad voor de aanvraag om de aanvragen uit te voeren. Voeg vervolgens `api-key`- en `Content-Type`-aanvraagheaders toe. Stel de waarde van `api-key` in op `{{admin-key}}`. Stel de waarde `Content-type` in op `application/json`. 

![Schermopname van de interface van Postman voor headers](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> U moet in al uw aanvragen `api-key`- en `Content-type`-headers instellen. Als Postman een variabele herkent, wordt de variabele in oranje tekst weergegeven, net als bij `{{admin-key}}` in de vorige schermopname. Als de variabele verkeerd is gespeld, wordt deze rood weergegeven.
>

## <a name="create-an-azure-cognitive-search-index"></a>Een Azure Cognitive Search-index maken

Maak een Azure Cognitive Search-index voor de gegevens die u wilt doorzoeken, filteren en verbeteren. Maak de index door een PUT-aanvraag uit te geven aan `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Postman vervangt symbolen die tussen dubbele accolades staan (zoals `{{search-service-name}}`, `{{index-name}}`en `{{api-version}}`) door de waarden die u hebt ingesteld in [Postman configureren](#configure-postman). Als u een ander hulpprogramma gebruikt om uw REST-opdrachten uit te geven, moet u deze variabelen zelf vervangen.

Stel de structuur van uw Azure Cognitive Search-index in de hoofdtekst van de aanvraag in. Ga in Postman naar het deelvenster **Hoofdtekst** van de aanvraag nadat u de headers `api-key` en `Content-type` hebt ingesteld. Als het goed is, ziet u de volgende JSON. Selecteer **Onbewerkt** > **JSON (application/json)** en plak de volgende code als hoofdtekst als dat niet het geval is:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Deze indexdefinitie is een combinatie van gegevens die u aan de gebruiker wilt presenteren (de naam van het hotel, de inhoud van de beoordeling, de datum), metagegevens van zoekopdrachten en gegevens van de AI-verbetering (sentiment, trefwoordzinnen en taal).

Selecteer **Verzenden** om de PUT-aanvraag te verzenden. Als het goed is, ziet u nu de status `201 - Created`. Zoek in het deelvenster **Hoofdtekst** naar een JSON-antwoord met een foutbericht als u een andere status ziet. 

## <a name="create-the-datasource"></a>De gegevensbron maken

Verbind vervolgens Azure Cognitive Search met de hotelgegevens die u hebt opgeslagen in Blob Storage. Verzend een POST-aanvraag naar `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` om de gegevensbron te maken. U moet de `api-key`- en `Content-Type`-headers instellen zoals eerder is beschreven. 

Ga in Postman naar de aanvraag **Gegevensbron maken** en ga vervolgens naar het deelvenster **Hoofdtekst**. Als het goed is, ziet u de volgende code:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Selecteer **Verzenden** om de POST-aanvraag te verzenden. 

## <a name="create-the-skillset"></a>De vaardighedenset maken 

De volgende stap is het opgeven van de vaardighedenset. Hierin specificeert u zowel de verbeteringen die moeten worden toegepast als het kennisarchief waarin de resultaten moeten worden opgeslagen. Selecteer in Postman het tabblad **Vaardighedenset maken**. Deze aanvraag verzendt een PUT-aanvraag naar `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`. Stel de `api-key`- en `Content-type`-headers in zoals u eerder hebt gedaan. 

Er zijn twee grote objecten op het hoogste niveau: `skills` en `knowledgeStore`. Elk object binnen het `skills`-object is een verrijkingsservice. Elke verrijkingsservice heeft `inputs` en `outputs`. Het `LanguageDetectionSkill` heeft een uitvoer `targetName` van `Language`. De waarde van dit knooppunt wordt door de meeste andere vaardigheden gebruikt als invoer. De bron is `document/Language`. De mogelijkheid om de uitvoer van het ene knooppunt als de invoer voor een andere te gebruiken, is nog duidelijker in `ShaperSkill`, waarin wordt aangegeven hoe de gegevens in de tabellen van het kennisarchief worden opgeslagen.

Het `knowledge_store`-object maakt verbinding met het opslagaccount via de Postman-variabele `{{storage-connection-string}}`. `knowledge_store` bevat een set met toewijzingen tussen het uitgebreide document en de tabellen en kolommen in het kennisarchief. 

Selecteer de knop **Verzenden** in Postman om de aanvraag op te halen en de vaardighedenset te genereren:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>De indexeerfunctie maken

De laatste stap bestaat uit het maken van de indexeerfunctie. De indexeerfunctie leest de gegevens en activeert de vaardighedenset. Selecteer in Postman de aanvraag **Indexeerfunctie maken** en controleer vervolgens de hoofdtekst. De definitie van de indexeerfunctie verwijst naar verschillende andere resources die u al hebt gemaakt: de gegevensbron, de index en de vaardighedenset. 

Het `parameters/configuration`-object bepaalt hoe de indexeerfunctie de gegevens opneemt. In dit geval bevinden de invoergegevens zich in één document met een koptekstregel en door komma's gescheiden waarden. De documentsleutel is een unieke id voor het document. Voordat de codering plaatsvindt, is de URL van het brondocument de documentsleutel. Tenslotte worden de uitvoerwaarden van de vaardighedenset, zoals de taalcode, het sentiment en sleutelzinnen, toegewezen aan hun locaties in het document. Hoewel er sprake is van een enkele waarde voor `Language`, wordt `Sentiment` op elk element in de matrix van `pages` toegepast. `Keyphrases` is een matrix die ook wordt toegepast op elk element in de `pages`-matrix.

Selecteer **Verzenden** in Postman nadat u de `api-key`- en `Content-type`-kopteksten hebt ingesteld en hebt bevestigd dat de hoofdtekst van de aanvraag vergelijkbaar is met de volgende broncode. Postman verzendt een PUT-aanvraag naar `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. Azure Cognitive Search maakt de indexeerfunctie en voert deze uit. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>De indexeerfunctie uitvoeren 

Ga in Azure Portal naar de pagina **Overzicht** van de Azure Cognitive Search-service. Selecteer het tabblad **Indexeerfuncties** en selecteer vervolgens **hotels-reviews-ixr**. Selecteer **Uitvoeren** als de indexeerfunctie nog niet is uitgevoerd. De indexeringstaak kan enkele waarschuwingen met betrekking tot taalherkenning veroorzaken. De gegevens bevatten enkele beoordelingen die zijn geschreven in talen die nog niet door de cognitieve vaardigheden worden ondersteund. 

## <a name="next-steps"></a>Volgende stappen

Nu u uw gegevens hebt verrijkt met Cognitive Services en de resultaten naar een kennisarchief hebt geprojecteerd, kunt u Storage Explorer of Power BI gebruiken om uw verrijkte gegevensset te verkennen.

Zie dit overzicht voor meer informatie over het verkennen van dit kennisarchief met behulp van Storage Explorer:

> [!div class="nextstepaction"]
> [Weergeven met Storage Explorer](knowledge-store-view-storage-explorer.md)

Zie dit overzicht voor meer informatie over het verbinden van dit kennisarchief met Power BI:

> [!div class="nextstepaction"]
> [Verbinden met Power BI](knowledge-store-connect-power-bi.md)

Als u deze oefening wilt herhalen of een ander AI-verrijkingsscenario wilt uitproberen, verwijder dan de indexeerfunctie **hotel-reviews-idxr**. Door de indexeerfunctie te verwijderen, wordt de teller van het aantal gratis dagelijkse transacties weer opnieuw ingesteld.