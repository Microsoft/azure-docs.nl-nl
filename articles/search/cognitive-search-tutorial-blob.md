---
title: 'Zelfstudie: REST en AI over Azure-blobs'
titleSuffix: Azure Cognitive Search
description: Bekijk een voorbeeld van tekstextractie en natuurlijke taalverwerking over inhoud in blob-opslag met behulp van Postman en de REST API's van Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: 21f0d141567f17c470732088c6a93a2ae7ed3c67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94738047"
---
# <a name="tutorial-use-rest-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Zelfstudie: REST en AI gebruiken voor het genereren van doorzoekbare inhoud van Azure-blobs

Als u ongestructureerde tekst of afbeeldingen in Azure Blob-opslag hebt, kunt u met een [AI-verrijkingspijplijn](cognitive-search-concept-intro.md) informatie extraheren en nieuwe inhoud maken van blobs die nuttig zijn voor zoekopdrachten in volledige tekst of kennisanalyse. Hoewel een pijplijn installatiekopieën kan verwerken, wordt in deze REST-zelfstudie aandacht besteed aan tekst, het toepassen van taaldetectie en de verwerking van natuurlijke taal om nieuwe velden te maken die u in query's, facetten en filters kunt gebruiken.

Deze zelfstudie maakt gebruik van Postman en de [REST API's zoeken](/rest/api/searchservice/) om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Services en een Postman-verzameling instellen.
> * Definieer een verrijkingspijplijn die tekst extraheert, taal detecteert, entiteiten herkent en sleutelzinnen detecteert.
> * Maak een index voor het opslaan van de uitvoer (onbewerkte inhoud en pijplijn gegenereerde paren van namen en waarden).
> * Voer de pijplijn uit om transformaties en analyses uit te voeren en om de index te laden.
> * Bekijk de resultaten met zoeken in volledige tekst en een uitgebreide query-syntaxis.

Als u geen abonnement op Azure hebt, opent u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="overview"></a>Overzicht

Deze zelfstudie maakt gebruik van C# en de Azure Cognitive Search REST API's om een gegevensbron, index, indexeerfunctie en vaardighedenset te maken. U begint met hele documenten (ongestructureerde tekst) zoals PDF, HTML, DOCX en PPTX in Azure Blob-opslag. U voert deze vervolgens uit met een vaardigheden om entiteiten, sleutelzinnen en andere tekst uit de inhoudsbestanden te extraheren.

Deze vaardighedenset maakt gebruik van ingebouwde vaardigheden op basis van Cognitive Services-API's. Stappen in de pijplijn omvatten taaldetectie voor tekst, sleutelzinextractie en entiteitsherkenning (organisaties). Nieuwe informatie wordt opgeslagen in nieuwe velden die u kunt gebruiken voor query's, facetten en filters.

## <a name="prerequisites"></a>Vereisten

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Postman bureaublad-app](https://www.getpostman.com/)
+ [Maak](search-create-service-portal.md) of [vind een bestaande zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> U kunt de gratis service voor deze zelfstudie gebruiken. Een gratis zoekservice beperkt u tot drie indexen, drie indexeerfuncties en drie gegevensbronnen. In deze zelfstudie wordt één exemplaar van elk onderdeel gemaakt. Voordat u aan de slag gaat, zorg ervoor dat uw service voldoende ruimte heeft voor de nieuwe resources.

## <a name="download-files"></a>Bestanden downloaden

1. Open deze [OneDrive-map](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) en klik in de linkerbovenhoek op **Downloaden** om de bestanden naar uw computer te kopiëren. 

1. Klik met de rechtermuisknop op het zip-bestand en selecteer **Alles extraheren**. Er zijn 14 bestanden van verschillende typen. U gebruikt 7 voor deze oefening.

U kunt eventueel ook de broncode (een Postman-verzamelingsbestand) voor deze zelfstudie downloaden. U vindt de broncode op [https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial).

## <a name="1---create-services"></a>1- Services maken

In deze zelfstudie maakt gebruik van Azure Cognitive Search voor het indexeren en uitvoeren van query's, Cognitive Services op de back-end voor AI-verrijking en Azure Blob-opslag om de gegevens op te geven. Deze zelfstudie bevindt zich onder de gratis toewijzing van 20 transacties per indexeerfunctie per dag op Cognitive Services, dus de enige services die u moet maken, zijn zoeken en opslag.

Maak, indien mogelijk, beide in dezelfde regio en resourcegroep voor nabijheid en beheerbaarheid. In de praktijk kan uw Azure Storage-account zich in een willekeurige regio bevinden.

### <a name="start-with-azure-storage"></a>Aan de slag met Azure Storage

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/) en klik op **En resourcegroepen maken**.

1. Zoek naar *Opslagaccount* en selecteer de aanbieding van het Microsoft-opslagaccount.

   ![Een opslagaccount maken](media/cognitive-search-tutorial-blob/storage-account.png "Een opslagaccount maken")

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

   ![Voorbeeldbestanden uploaden](media/cognitive-search-tutorial-blob/sample-files.png "Voorbeeldbestanden uploaden")

1. Voordat u Azure Storage verlaat, moet u een verbindingsreeks ophalen zodat u een verbinding in Azure Cognitive Search kunt formuleren. 

   1. Ga terug naar de pagina Overzicht van uw opslagaccount (we hebben *blobstragewestus* als voorbeeld gebruikt). 
   
   1. Selecteer in het navigatiedeelvenster links de optie **Toegangstoetsen** en kopieer een van de verbindingsreeksen. 

   De verbindingsreeks is een URL die er ongeveer als volgt uitziet:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Sla de verbindingsreeks op in Kladblok. U hebt deze later nodig bij het instellen van de gegevensbronverbinding.

### <a name="cognitive-services"></a>Cognitive Services

AI-verrijking wordt ondersteund door Cognitive Services, waaronder Text Analytics en Computer Vision voor de verwerking van natuurlijke taal en afbeeldingen. Als u een echt prototype of project wilt voltooien, moet u op dit punt Cognitive Services (in dezelfde regio als Azure Cognitive Search) inrichten, zodat u het kunt koppelen aan indexbewerkingen.

Voor deze oefening kunt u echter het inrichten van resources overslaan omdat Azure Cognitive Search verbinding kan maken met Cognitive Services achter de schermen en u 20 gratis transacties per uitvoering van indexeerfunctie geeft. Omdat in deze zelfstudie 7 transacties worden gebruikt, is de gratis toewijzing voldoende. Voor grotere projecten plant u het inrichten van Cognitive Services op de S0-laag voor betalen per gebruik. Zie voor meer informatie [Cognitive Services koppelen](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Het derde onderdeel is Azure Cognitive Search, dat u kunt [maken in de portal](search-create-service-portal.md). U kunt de gratis laag gebruiken om dit voorbeeld te voltooien. 

Net als bij Azure Blob-opslag duurt het even om de toegangssleutel te verzamelen. Daarnaast moet u, wanneer u begint met het structureren van aanvragen, het eindpunt en de beheer-API-sleutel opgeven die worden gebruikt om elke aanvraag te verifiëren.

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een beheer-API-sleutel en URL voor Azure Cognitive Search kopiëren

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/) en haal op de pagina **Overzicht** de naam van uw zoekservice op. U kunt uw servicenaam bevestigen door de URL van het eindpunt te controleren. Als uw eindpunt-URL `https://mydemo.search.windows.net` is, wordt uw service naam `mydemo`.

2. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Haal ook de querysleutel op. Het is een aanbevolen procedure voor het uitgeven van queryaanvragen met alleen-lezen-toegang.

   ![De naam van de service en de querysleutels voor beheer ophalen](media/search-get-started-javascript/service-name-and-keys.png)

Voor alle aanvragen is een API-sleutel vereist in de header die naar uw service wordt verzonden. Me een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---set-up-postman"></a>2 - Postman instellen

Start Postman en stel een HTTP-aanvraag in. Als u niet bekend bent met dit hulpprogramma, raadpleegt u [REST API's van Azure Cognitive Search verkennen](search-get-started-rest.md).

De aanvraagmethoden in deze zelfstudie zijn **POST**, **PUT** en **GET**. U gebruikt de methoden om vier API-aanroepen uit te voeren naar uw zoekservice: voor het maken van een gegevensbron, een vaardighedenset, een index en een indexeerfunctie.

Stel bij Headers de optie Inhoudstype in op `application/json` en stel `api-key` in op de API-sleutel voor beheerders van uw Azure Cognitive Search-service. Zodra u de headers hebt ingesteld, kunt u ze gebruiken voor elke aanvraag in deze oefening.

  ![URL en header voor Postman-aanvraag](media/search-get-started-rest/postman-url.png "URL en header voor Postman-aanvraag")

## <a name="3---create-the-pipeline"></a>3: Maak de pijplijn

In Azure Cognitive Search treedt verrijking op tijdens het indexeren (of gegevensopname). In dit deel van het scenario worden vier objecten gemaakt: gegevensbron, indexdefinitie, vaardighedenset, indexeerfunctie. 

### <a name="step-1-create-a-data-source"></a>Stap 1: Een gegevensbron maken

Een [gegevensbron-object](/rest/api/searchservice/create-data-source) biedt de BLOB-container de verbindingsreeks met de bestanden.

1. Gebruik **POST** en de volgende URL, waarbij u YOUR-SERVICE-NAME vervangt door de werkelijke naam van uw service.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2020-06-30
   ```

1. Kopieer in aanvraag **Hoofdtekst** de volgende JSON-definitie en vervang `connectionString` door de feitelijke verbinding van uw opslagaccount. 

   Vergeet niet om ook de containernaam te bewerken. In een eerdere stap is cog-search-demo voorgesteld als containernaam.

    ```json
    {
      "name" : "cog-search-demo-ds",
      "description" : "Demo files to demonstrate cognitive search capabilities.",
      "type" : "azureblob",
      "credentials" :
      { "connectionString" :
        "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-ACCOUNT-KEY>;"
      },
      "container" : { "name" : "<YOUR-BLOB-CONTAINER-NAME>" }
    }
    ```
1. Verzend de aanvraag. U moet statuscode 201 te zien krijgen als bevestiging. 

Als u een 403- of 404-fout krijgt, controleert u de constructie van de aanvraag: `api-version=2020-06-30` moet in het eindpunt staan, `api-key` in de header na `Content-Type`, en de waarde ervan moet geldig voor een zoekservice zijn. U kunt het JSON-document uitvoeren via een online JSON-validatieprogramma om ervoor te zorgen dat de syntaxis juist is. 

### <a name="step-2-create-a-skillset"></a>Stap 2: Een set vaardigheden maken

Een [vaardighedensetobject](/rest/api/searchservice/create-skillset) is een reeks verrijkingsstappen die op uw inhoud wordt toegepast. 

1. Gebruik **PUT** en de volgende URL, waarbij u YOUR-SERVICE-NAME vervangt door de werkelijke naam van uw service.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-sd?api-version=2020-06-30
    ```

1. Kopieer in aanvraag **Hoofdtekst** de volgende JSON-definitie. Deze vaardighedenset bestaat uit de volgende ingebouwde vaardigheden.

   | Vaardigheid                 | Beschrijving    |
   |-----------------------|----------------|
   | [Herkenning van entiteiten](cognitive-search-skill-entity-recognition.md) | Hiermee worden de namen van personen, organisaties en locaties uit inhoud van de blobcontainer geëxtraheerd. |
   | [Taaldetectie](cognitive-search-skill-language-detection.md) | Hiermee wordt de taal van de inhoud gedetecteerd. |
   | [Tekst splitsen](cognitive-search-skill-textsplit.md)  | Hiermee wordt grote inhoud in kleinere stukken opgedeeld voordat de vaardigheid voor sleuteltermextractie wordt aangeroepen. Sleuteltermextractie accepteert invoeren van 50.000 tekens of minder. Enkele voorbeeldbestanden moeten worden opgesplitst om aan deze limiet te voldoen. |
   | [Sleuteltermextractie](cognitive-search-skill-keyphrases.md) | Hiermee worden de belangrijkste sleuteltermen opgehaald. |

   Elke vaardigheid wordt uitgevoerd voor de inhoud van het document. Tijdens het verwerken opent Azure Cognitive Search elk document om inhoud van verschillende bestandsindelingen te lezen. Gevonden tekst uit het bronbestand wordt in een gegenereerd ```content```-veld geplaatst, één voor elk document. Als zodanig wordt de invoer ```"/document/content"```.

   Voor het extraheren van sleuteltermen is de context voor de vaardigheid sleuteltermextractie ```"document/pages/*"``` (voor elke pagina in het document) in plaats van ```"/document/content"```, omdat de vaardigheid Tekst splitsen wordt gebruikt om grotere bestanden in pagina's op te splitsen.

    ```json
    {
      "description": "Extract entities, detect language and extract key-phrases",
      "skills":
      [
        {
          "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
          "categories": [ "Person", "Organization", "Location" ],
          "defaultLanguageCode": "en",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "persons", "targetName": "persons" },
            { "name": "organizations", "targetName": "organizations" },
            { "name": "locations", "targetName": "locations" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "languageCode", "targetName": "languageCode" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
          "textSplitMode" : "pages",
          "maximumPageLength": 4000,
          "inputs": [
            { "name": "text", "source": "/document/content" },
            { "name": "languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "textItems", "targetName": "pages" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
          "context": "/document/pages/*",
          "inputs": [
            { "name": "text", "source": "/document/pages/*" },
            { "name":"languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "keyPhrases", "targetName": "keyPhrases" }
          ]
        }
      ]
    }
    ```
    Een grafische weergave van de set vaardigheden wordt hieronder weergegeven. 

    ![Een vaardighedenset begrijpen](media/cognitive-search-tutorial-blob/skillset.png "Een vaardighedenset begrijpen")

1. Verzend de aanvraag. Postman moet een 201-statuscode retourneren om de uitvoering te bevestigen. 

> [!NOTE]
> Uitvoeren kunnen aan een index worden toegewezen, als invoer worden gebruikt voor een downstream-vaardigheid, of beide zoals bij taalcode. Een taalcode is in de index handig om te filteren. Taalcode wordt als invoer door vaardigheden voor tekstanalyse gebruikt om de taalregels voor woordafbreking in te stellen. Zie [How to define a skillset](cognitive-search-defining-skillset.md) (Een set vaardigheden definiëren) voor meer informatie over de grondbeginselen van vaardigheden.

### <a name="step-3-create-an-index"></a>Stap 3: Een index maken

Een [index](/rest/api/searchservice/create-index) voorziet in het schema dat wordt gebruikt voor het maken van de fysieke expressie van uw inhoud in omgekeerde indexen en andere constructen in Azure Cognitive Search. Het grootste onderdeel van een index is de verzameling velden, waarbij gegevenstype en kenmerken de inhoud en het gedrag in Azure Cognitive Search bepalen.

1. Gebruik **PUT** en de volgende URL, waarbij u YOUR-SERVICE-NAME vervangt door de werkelijke naam van uw service om de index een naam te geven.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2020-06-30
   ```

1. Kopieer in aanvraag **Hoofdtekst** de volgende JSON-definitie. In het veld `content` wordt het document zelf opgeslagen. Aanvullende velden voor `languageCode`, `keyPhrases`en `organizations` vertegenwoordigen nieuwe informatie (velden en waarden) die door de vaardighedenset zijn gemaakt.

    ```json
    {
      "fields": [
        {
          "name": "id",
          "type": "Edm.String",
          "key": true,
          "searchable": true,
          "filterable": false,
          "facetable": false,
          "sortable": true
        },
        {
          "name": "metadata_storage_name",
          "type": "Edm.String",
          "searchable": false,
          "filterable": false,
          "facetable": false,
          "sortable": false
        },
        {
          "name": "content",
          "type": "Edm.String",
          "sortable": false,
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "languageCode",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "keyPhrases",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "persons",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "organizations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "locations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        }
      ]
    }
    ```

1. Verzend de aanvraag. Postman moet een 201-statuscode retourneren om de uitvoering te bevestigen. 

### <a name="step-4-create-and-run-an-indexer"></a>Stap 4: Indexeerfunctie maken en uitvoeren

Een [Indexeerfunctie](/rest/api/searchservice/create-indexer) voert de pijplijn aan. De drie onderdelen die u tot nu toe hebt gemaakt (gegevensbron, vaardighedenset, index) zijn invoer naar een indexeerfunctie. Het maken van de indexeerfunctie op Azure Cognitive Search is de gebeurtenis die de volledige pijplijn in beweging zet. 

1. Gebruik **PUT** en de volgende URL, waarbij u YOUR-SERVICE-NAME vervangt door de werkelijke naam van uw service om de indexeerfunctie een naam te geven.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2020-06-30
   ```

1. Kopieer in aanvraag **Hoofdtekst** de volgende JSON-definitie. Let op de elementen van de veldtoewijzing; deze toewijzingen zijn belangrijk omdat ze de gegevensstroom definiëren. 

   `fieldMappings` worden vóór de vaardighedenset verwerkt, waarbij inhoud van de gegevensbron wordt verzonden naar de doelvelden in een index. U gebruikt veldtoewijzingen voor het verzenden van bestaande, niet-gewijzigde inhoud naar de index. Als veldnamen en -typen gelijk zijn aan beide uiteinden, is toewijzing niet vereist.

   De `outputFieldMappings` zijn voor velden die door vaardighedensets zijn gemaakt en dus worden verwerkt nadat de vaardighedenset is uitgevoerd. De verwijzingen naar `sourceFieldNames` in `outputFieldMappings` bestaan pas wanneer ze door kraken van het document of verrijking zijn gemaakt. `targetFieldName` is een veld in een index dat in het indexschema is gedefinieerd.

    ```json
    {
      "name":"cog-search-demo-idxr",
      "dataSourceName" : "cog-search-demo-ds",
      "targetIndexName" : "cog-search-demo-idx",
      "skillsetName" : "cog-search-demo-ss",
      "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "metadata_storage_name",
          "targetFieldName" : "metadata_storage_name",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "content",
          "targetFieldName" : "content"
        }
      ],
      "outputFieldMappings" :
      [
        {
          "sourceFieldName" : "/document/persons",
          "targetFieldName" : "persons"
        },
        {
          "sourceFieldName" : "/document/organizations",
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/locations",
          "targetFieldName" : "locations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*",
          "targetFieldName" : "keyPhrases"
        },
        {
          "sourceFieldName": "/document/languageCode",
          "targetFieldName": "languageCode"
        }
      ],
      "parameters":
      {
        "maxFailedItems":-1,
        "maxFailedItemsPerBatch":-1,
        "configuration":
        {
          "dataToExtract": "contentAndMetadata",
          "parsingMode": "default",
          "firstLineContainsHeaders": false,
          "delimitedTextDelimiter": ","
        }
      }
    }
    ```

1. Verzend de aanvraag. Postman moet een 201-statuscode retourneren om de verwerking te bevestigen. 

   Deze stap kan enkele minuten in beslag nemen. De gegevensset is klein, maar analytische vaardigheden zijn berekeningsintensief. 

> [!NOTE]
> Het maken van een indexeerfunctie roept de pijplijn aan. Als er problemen zijn met het bereiken van de gegevens, het toewijzen van invoeren en uitvoeren of de volgorde van bewerkingen, worden ze in deze fase weergegeven. Mogelijk moet u eerst objecten verwijderen om de pijplijn opnieuw uit te voeren met code- of scriptwijzigingen. Zie [Reset and re-run](#reset) (Opnieuw instellen en uitvoeren) voor meer informatie.

#### <a name="about-indexer-parameters"></a>Parameters voor indexeerfuncties

Het script stelt ```"maxFailedItems"``` op -1 in, wat de indexeerengine de opdracht geeft om fouten tijdens het importeren van gegevens te negeren. Dit is acceptabel omdat de demo-gegevensbron maar een paar documenten bevat. Voor een grotere gegevensbron zou u de waarde op groter dan 0 instellen.

De ```"dataToExtract":"contentAndMetadata"```-instructie geeft de indexeerfunctie de opdracht om automatisch de inhoud van verschillende bestandsindelingen te extraheren, evenals metagegevens voor elk bestand. 

Wanneer inhoud wordt uitgepakt, kunt u instellen dat ```imageAction``` tekst ophaalt uit afbeeldingen die in de gegevensbron worden gevonden. De configuratie van ```"imageAction":"generateNormalizedImages"```, OCR Skill en Text Merge Skill geeft de indexeerfunctie de opdracht om tekst uit de afbeeldingen te extraheren (bijvoorbeeld het woord 'stop' van een Stop-verkeersbord) en deze in te sluiten als onderdeel van het inhoudsveld. Dit gedrag is van toepassing op zowel de afbeeldingen die zijn ingesloten in de documenten (zoals een afbeelding in een pdf-bestand) als afbeeldingen die zijn gevonden in de gegevensbron, zoals een JPG-bestand.

## <a name="4---monitor-indexing"></a>4: Het indexeren bewaken

Het indexeren en verrijken begint zodra u de aanvraag Indexeerfunctie maken hebt verzonden. Afhankelijk van welke cognitieve vaardigheden u hebt gedefinieerd, kan het indexeren lange tijd duren. Als u wilt weten of de indexeerfunctie nog wordt uitgevoerd, verzendt u de volgende aanvraag om de status van de indexeerfunctie te controleren.

1. Gebruik **GET** en de volgende URL, waarbij u YOUR-SERVICE-NAME vervangt door de werkelijke naam van uw service om de indexeerfunctie een naam te geven.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2020-06-30
   ```

1. Bekijk het antwoord om te zien of de indexeerfunctie wordt uitgevoerd of om fout- en waarschuwingsgegevens te bekijken.  

Als u de gratis laag gebruikt, wordt het volgende bericht verwacht: 'Kan geen inhoud of metagegevens uit het document extraheren. Geëxtraheerde tekst afgekapt tot 32768 tekens'. Dit bericht wordt weergegeven omdat het indexeren van blobs in de gratis laag een [limiet van 32 k heeft voor tekenextractie](search-limits-quotas-capacity.md#indexer-limits). Bij hogere lagen wordt dit bericht niet voor deze gegevensset weergegeven. 

> [!NOTE]
> Waarschuwingen zijn in sommige scenario's gebruikelijk en duiden niet altijd op een probleem. Als een blobcontainer bijvoorbeeld afbeeldingsbestanden bevat en de pijplijn geen afbeeldingen afhandelt, wordt er een waarschuwing weergegeven met de mededeling dat de installatiekopieën niet zijn verwerkt.

## <a name="5---search"></a>5 - Zoeken

Nu u nieuwe velden en gegevens hebt gemaakt, gaan we enkele query's uitvoeren om inzicht te krijgen in de waarde van de cognitieve zoekopdracht, omdat deze is gekoppeld aan een typisch zoekscenario.

We zijn begonnen met blob-inhoud, waarbij het hele document is verpakt in één `content`-veld. U kunt dit veld doorzoeken en overeenkomsten met uw query's vinden.

1. Gebruik **GET** en de volgende URL, waarbij u YOUR-SERVICE-NAME vervangt door de werkelijke naam van uw service, om naar instanties van een term of woordgroep te zoeken. Het `content`-veld en een telling van het aantal overeenkomende documenten worden geretourneerd.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=content&api-version=2020-06-30
   ```
   
   De resultaten van deze query retourneren inhoud van documenten. Dit geeft hetzelfde resultaat als het gebruik van de blob-indexeerfunctie zonder de pijplijn voor cognitief zoeken. Dit veld is doorzoekbaar, maar dit is niet geschikt als u facetten, filters of automatisch aanvullen wilt gebruiken.

   ![Uitvoer van inhoudveld](media/cognitive-search-tutorial-blob/content-output.png "Uitvoer van inhoudveld")
   
1. Voor de tweede query retourneert u enkele van de nieuwe velden die door de pijplijn zijn gemaakt (personen, organisaties, locaties, languageCode). Sleuteltermen worden voor het gemak weggelaten, maar neem ze op als u deze waarden wilt zien.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2020-06-30
   ```
   De velden in de $select-instructie bevatten nieuwe informatie, gemaakt op basis van de mogelijkheden tot verwerking van natuurlijke taal van Cognitive Services. Zoals u kunt verwachten, bevatten de resultaten ruis en zitten er variaties in de documenten, maar in veel gevallen leveren de analytische modellen nauwkeurige resultaten op.

   In de volgende afbeelding ziet u de resultaten voor de open brief van Satya Nadella, waarin ze de functie van CEO bij Microsoft op zich neemt.

   ![Uitvoer van pijplijn](media/cognitive-search-tutorial-blob/pipeline-output.png "Uitvoer van pijplijn")

1. Als u wilt weten hoe u van deze velden kunt profiteren, voegt u een facetparameter toe om een aggregatie van overeenkomende documenten per locatie te retourneren.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2020-06-30
   ``` 

   In dit voorbeeld zijn er twee of drie overeenkomsten voor elke locatie.

   ![Uitvoer van facet](media/cognitive-search-tutorial-blob/facet-output.png "Uitvoer van facet")
   

1. In dit laatste voorbeeld past u een filter toe op de verzameling organisaties, waarbij twee overeenkomsten worden geretourneerd voor filtercriteria op basis van de NASDAQ.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2020-06-30
   ```

Deze query's illustreren een aantal manieren waarop u kunt werken met querysyntaxis en filters voor nieuwe velden die met behulp van cognitief zoeken zijn gemaakt. Zie [Voorbeelden in Documenten zoeken (Azure Cognitive Search REST API)](/rest/api/searchservice/search-documents#bkmk_examples), [Voorbeelden van query's met eenvoudige syntaxis](search-query-simple-examples.md)en [Voorbeelden van volledige Lucene-query's](search-query-lucene-examples.md)voor meer query voorbeelden.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Opnieuw instellen en uitvoeren

In de eerste experimentele fasen van ontwikkeling, is de meest praktische aanpak voor ontwerpiteraties om de objecten uit Azure Cognitive Search te verwijderen en uw code ze te laten herbouwen. Resourcenamen zijn uniek. Na het verwijderen van een object kunt u het opnieuw maken met dezelfde naam.

U kunt de portal gebruiken om indexen, indexeerfuncties, gegevensbronnen en vaardighedensets te verwijderen. Wanneer u de indexeerfunctie verwijdert, kunt u eventueel de index, de vaardighedenset en de gegevensbron selectief en tegelijkertijd verwijderen.

![Zoekobjecten verwijderen](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Zoekobjecten verwijderen in de portal")

Of gebruik **DELETE** en geef voor elk object een URL op. Met de volgende opdracht wordt een indexeerfunctie verwijderd.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr?api-version=2020-06-30
```

De statuscode 204 wordt na verwijdering geretourneerd.

## <a name="takeaways"></a>Opgedane kennis

In deze zelfstudie zijn de basisstappen voor het bouwen van een pijplijn voor verrijkte indexering gedemonstreerd via het maken van onderdelen: een gegevensbron, een set vaardigheden, een index en een indexeerfunctie.

[Ingebouwde vaardigheden](cognitive-search-predefined-skills.md) zijn geïntroduceerd, samen met de definitie van de vaardighedenset en het mechanisme voor het koppelen van vaardigheden via in- en uitvoer. U hebt ook geleerd dat `outputFieldMappings` vereist is in de definitie van de indexeerfunctie voor de routering van verrijkte waarden uit de pijplijn naar een doorzoekbare index op een Azure Cognitive Search-service.

Tot slot hebt u geleerd hoe u resultaten kunt testen en het systeem opnieuw kunt instellen voor verdere iteraties. U hebt geleerd dat het uitvoeren van query's op de index de uitvoer retourneert die is gemaakt door de pijplijn voor verrijkte indexering. 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt en of u deze moet verwijderen. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling Alle resources of Resourcegroepen in het navigatiedeelvenster aan de linkerkant.

## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met alle objecten in een AI-verrijkingspijplijn, gaan we kijken naar de definities van vaardighedensets en individuele vaardigheden.

> [!div class="nextstepaction"]
> [Een vaardighedenset maken](cognitive-search-defining-skillset.md)
