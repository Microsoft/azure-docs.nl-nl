---
title: 'Quickstart: Een zoekindex maken in JavaScript'
titleSuffix: Azure Cognitive Search
description: In deze JavaScript-quickstart leert u hoe u een index maakt, gegevens laadt en query's uitvoert in Azure Cognitive Search met behulp van JavaScript
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.devlang: javascript
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/02/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7d31201f9d1a4519538aba3ac57ddfd340d936d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509398"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-the-javascript-sdk"></a>Quickstart: Een Azure Cognitive Search-index maken met JavaScript SDK
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [REST](search-get-started-rest.md)


Gebruik de [JavaScript/Typscript SDK voor Azure Cognitive Search](/javascript/api/overview/azure/search-documents-readme) om een Node.js-toepassing te maken in JavaScript waarmee een zoekindex wordt gemaakt, geladen en opgevraagd.

In dit artikel komt erachter hoe u stapsgewijs hoe u de toepassing maakt. U kunt ook [de broncode en gegevens downloaden](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) en de toepassing uitvoeren vanaf de opdrachtregel.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u beschikken over de volgende hulpprogramma's en services:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

+ Een Azure Cognitive Search-service. [Maak een service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). U kunt een gratis service voor deze quickstart gebruiken. 

+ [Node.js](https://nodejs.org) en [NPM](https://www.npmjs.com)

+ [Visual Studio Code](https://code.visualstudio.com) of een andere IDE


## <a name="set-up-your-project"></a>Uw project instellen

Begin met het ophalen van het eindpunt en de sleutel voor uw zoekservice. Maak vervolgens een nieuw project met NPM zoals hieronder beschreven.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Een sleutel en eindpunt kopiëren

Voor aanroepen naar de service zijn voor elke aanvraag een URL-eindpunt en een toegangssleutel vereist. Als eerste stap zoekt u de API-sleutel en de URL die u aan uw project wilt toevoegen. U geeft beide waarden op bij het maken van de client in een latere stap.

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

2. In **Instellingen** > **Sleutels**, kunt u een beheerderssleutel ophalen voor volledige rechten op de service. Deze heeft u nodig voor het maken en verwijderen van objecten. Er zijn twee uitwisselbare primaire en secundaire sleutels. U kunt beide gebruiken.

   ![Een HTTP-eindpunt en toegangssleutel ophalen](media/search-get-started-rest/get-url-key.png "Een HTTP-eindpunt en toegangssleutel ophalen")

Voor alle aanvragen is een API-sleutel vereist op elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

### <a name="create-a-new-npm-project"></a>Maak een nieuw NPM-project

Begin met het openen van VS-code en de [geïntegreerde terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) of een andere terminal, zoals de Node.js-opdrachtprompt.

1. Maak een ontwikkelingsmap en noem deze map `quickstart`:

    ```cmd
    mkdir quickstart
    cd quickstart
    ```

2. Een leeg project initialiseren met NPM door het volgende uit te voeren: 

    ```cmd
    npm init
    ```
     Accepteer de standaardwaarden, met uitzondering van de licentie. Deze moet u instellen op MIT. 

3. Installeer `@azure/search-documents`, de [JavaScript/Typscript SDK voor Azure Cognitive Search](/javascript/api/overview/azure/search-documents-readme).

    ```cmd
    npm install @azure/search-documents
    ```

4. Installeer `dotenv` , die wordt gebruikt om de omgevings variabelen te importeren, zoals de service naam en API-sleutel.
    ```cmd
    npm install dotenv
    ```

5. Bevestig dat u de projecten en de bijbehorende afhankelijkheden hebt geconfigureerd door te controleren of het bestand **package.json** er als de volgende json uitziet:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "@azure/search-documents": "^11.0.3",
        "dotenv": "^8.2.0"
      }
    }
    ```

6. Maak het bestand **.env** om de parameters van de zoekservice op te slaan:

    ```
    SEARCH_API_KEY=<search-admin-key>
    SEARCH_API_ENDPOINT=https://<search-service-name>.search.windows.net
    ```

Vervang de waarde `<search-service-name>` door de naam van uw zoekservice. Vervang `<search-admin-key>` door de sleutelwaarden die u eerder hebt vastgelegd. 

### <a name="create-indexjs-file"></a>Maak het bestand index.js

Vervolgens maken we het bestand **index.js**. Dit is het hoofdbestand dat de code zal hosten.

Importeer boven aan dit bestand de `@azure/search-documents`-bibliotheek:

```javascript
const { SearchIndexClient, SearchClient, AzureKeyCredential, odata } = require("@azure/search-documents");
```

Vervolgens moet het `dotenv`-pakket als volgt worden gelezen in de parameters van het bestand **.env**:

```javascript
// Load the .env file if it exists
require("dotenv").config();

// Getting endpoint and apiKey from .env file
const endpoint = process.env.SEARCH_API_ENDPOINT || "";
const apiKey = process.env.SEARCH_API_KEY || "";
```

Met behulp van onze import-en omgevingsvariabelen kunt u de hoofdfunctie definiëren.

De meeste functionaliteit in de SDK is asynchroon, dus we maken onze hoofdfunctie `async`. We nemen ook een `main().catch()` op onder de hoofdfunctie om eventuele fouten te ondervangen en te registreren:

```javascript
async function main() {
    console.log(`Running Azure Cognitive Search Javascript quickstart...`);
    if (!endpoint || !apiKey) {
        console.log("Make sure to set valid values for endpoint and apiKey with proper authorization.");
        return;
    }

    // remaining quickstart code will go here
}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

Met deze basis kunnen we een index gaan maken.

## <a name="1---create-index"></a>1 - Index maken 

Maak een bestand **hotels_quickstart_index.json**.  Met dit bestand wordt gedefinieerd hoe Azure Cognitive Search werkt met de documenten die u in de volgende stap gaat laden. Elk veld wordt geïdentificeerd door een `name` en beschikt over een opgegeven `type`. Elk veld beschikt ook over een reeks indexkenmerken waarmee wordt opgegeven of Azure Cognitive Search op basis van het veld kan zoeken, filteren en sorteren en facetten kan maken. De meeste velden zijn eenvoudige gegevenstypen, maar een aantal, zoals `AddressType`, is van een complex type waarmee u uitgebreide gegevensstructuren in uw index kunt maken.  U kunt meer lezen over [ondersteunde gegevens typen](/rest/api/searchservice/supported-data-types) en index kenmerken die worden beschreven in [Create Index (rest)](/rest/api/searchservice/create-index). 

Voeg het volgende toe aan **hotels_quickstart_index.json** of [download het bestand](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels_quickstart_index.json). 

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

Als u de indexdefinitie hebt geïmplementeerd, wilt u  **hotels_quickstart_index. json** boven in het bestand **index.js** importeren, zodat de hoofdfunctie de indexdefinitie kan openen.

```javascript
const indexDefinition = require('./hotels_quickstart_index.json');
```

Binnen de hoofdfunctie maken we een `SearchIndexClient`, die wordt gebruikt voor het maken en beheren van indexen voor Azure Cognitive Search. 

```javascript
const indexClient = new SearchIndexClient(endpoint, new AzureKeyCredential(apiKey));
```

De index moet worden verwijderd als deze al bestaat. Dit is een veelvoorkomende procedure voor test- en voorbeeldcode.

Dit doet u door een eenvoudige functie te definiëren die probeert om de index te verwijderen.

```javascript
async function deleteIndexIfExists(indexClient, indexName) {
    try {
        await indexClient.deleteIndex(indexName);
        console.log('Deleting index...');
    } catch {
        console.log('Index does not exist yet.');
    }
}
```

Als u de functie wilt uitvoeren, halen we de indexnaam op uit de indexdefinitie en geven we de `indexName` door met de `indexClient` aan de functie `deleteIndexIfExists()`.

```javascript
const indexName = indexDefinition["name"];

console.log('Checking if index exists...');
await deleteIndexIfExists(indexClient, indexName);
```

Daarna kunt u de index maken met behulp van de methode `createIndex()`.

```javascript
console.log('Creating index...');
let index = await indexClient.createIndex(indexDefinition);

console.log(`Index named ${index.name} has been created.`);
```

### <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

U bent nu klaar om de voorbeeldtoepassing uit te voeren. Gebruik een terminalvenster om de volgende opdrachten uit te voeren:

```cmd
node index.js
```

Als u [de broncode hebt gedownload](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) en de vereiste pakketten nog niet hebt geïnstalleerd, voert u eerst `npm install` uit.

U ziet nu een reeks berichten waarin de acties worden beschreven die door het programma worden uitgevoerd. 

Open het **Overzicht** van uw zoekservice in Azure Portal. Selecteer het tabblad **Indexen**. Er verschijnt informatie die er ongeveer als volgt uitziet:

:::image type="content" source="media/search-get-started-javascript/create-index-no-data.png" alt-text="Schermopname van Azure Portal, overzicht van de zoekservice, het tabblad Indexen" border="false":::

In de volgende stap gaat u gegevens aan de index toevoegen. 

## <a name="2---load-documents"></a>2 - Documenten laden 


In Azure Cognitive Search zijn documenten gegevensstructuren die zowel de invoer van indexeringen als de uitvoer van query's zijn. U kunt dergelijke gegevens naar de index pushen of een [Indexeerfunctie](search-indexer-overview.md) gebruiken. In dat geval kunnen we de documenten via een programma naar de index pushen.

De documentinvoer bestaat mogelijk uit rijen in een database, blobs in Blob Storage of, zoals in dit voorbeeld, JSON-documenten op een schijf. U kunt [hotels.json](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels.json) downloaden of uw eigen **hotels.json**-bestand maken met de volgende inhoud:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}
```

Net als bij de Indexdefinitie moeten we ook `hotels.json` importeren boven aan **index.js** zodat de gegevens kunnen worden geopend in de hoofdfunctie.

```javascript
const hotelData = require('./hotels.json');
```


Als u gegevens in de zoekindex wilt indexeren, moet u nu een `SearchClient` maken. Terwijl de `SearchIndexClient` wordt gebruikt voor het maken en beheren van een index, wordt de `SearchClient` gebruikt voor het uploaden van documenten en het uitvoeren van query's op de index.

Er zijn twee manieren om een `SearchClient` maken. De eerste optie is het maken van een geheel nieuwe `SearchClient`:

```javascript
 const searchClient = new SearchClient(endpoint, indexName, new AzureKeyCredential(apiKey));
```

U kunt ook de methode `getSearchClient()` van de `SearchIndexClient` gebruiken om de `SearchClient` te maken:

```javascript
const searchClient = indexClient.getSearchClient(indexName);
```

Nu de client is gedefinieerd, uploadt u de documenten in de zoekindex. In dit geval gebruiken we de methode `mergeOrUploadDocuments()`, waarmee de documenten worden geüpload of samengevoegd met een bestaand document als er al een document met dezelfde sleutel bestaat.

```javascript
console.log('Uploading documents...');
let indexDocumentsResult = await searchClient.mergeOrUploadDocuments(hotelData['value']);

console.log(`Index operations succeeded: ${JSON.stringify(indexDocumentsResult.results[0].succeeded)}`);
```

Voer het programma opnieuw uit met `node index.js`. U ziet nu een iets andere reeks berichten dan in stap 1. Nu bestaat de index *wel* en u zou een bericht moeten zien over het verwijderen van de index voordat de nieuwe index wordt gemaakt en hierin gegevens worden geplaatst. 

Voordat we de query's in de volgende stap uitvoeren, definieert u een functie om het programma één seconde te laten wachten. Dit is alleen bedoeld voor test- en demonstratiedoeleinden om te controleren of de indexering is voltooid en of de documenten beschikbaar zijn in de index voor onze query's.

```javascript
function sleep(ms) {
    var d = new Date();
    var d2 = null;
    do {
        d2 = new Date();
    } while (d2 - d < ms);
}
```

Als u het programma één seconde wilt laten wachten, roept u de functie `sleep` aan zoals hieronder:

```javascript
sleep(1000);
```

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

Als er een index is gemaakt en documenten zijn geüpload, bent u klaar om query's naar de index te verzenden. In deze sectie sturen we vijf verschillende query's naar de zoekindex om verschillende onderdelen van de beschikbare query-functionaliteit te demonstreren.

De query's worden geschreven in een functie `sendQueries()` die u als volgt aanroept in de hoofdfunctie:

```javascript
await sendQueries(searchClient);
```

Query's worden verzonden met behulp van de methode `search()` van `searchClient`. De eerste parameter is de zoektekst en de tweede parameter zijn alle extra zoekopties.

De eerste query zoekt `*`, wat gelijk is aan ‘zoeken in alle’ en selecteert drie van de velden in de index. Het is een best practice om alleen de velden te `select` die u nodig hebt, omdat het ophalen van overbodige gegevens een latentie kan veroorzaken bij uw query's.

De `searchOptions` voor deze query heeft ook `includeTotalCount` ingesteld op `true`, waardoor het aantal gevonden resultaten wat overeenkomt wordt geretourneerd.

```javascript
async function sendQueries(searchClient) {
    console.log('Query #1 - search everything:');
    let searchOptions = {
        includeTotalCount: true,
        select: ["HotelId", "HotelName", "Rating"]
    };

    let searchResults = await searchClient.search("*", searchOptions);
    for await (const result of searchResults.results) {
        console.log(`${JSON.stringify(result.document)}`);
    }
    console.log(`Result count: ${searchResults.count}`);

    // remaining queries go here
}
```

De resterende query's die hieronder worden beschreven, moeten ook worden toegevoegd aan de functie `sendQueries()`. Ze worden hier gescheiden voor leesbaarheid.

In de volgende query geven we de zoekterm `"wifi"` op en nemen we ook een filter op om alleen resultaten te retourneren waarvan de status gelijk is aan `'FL'`. Resultaten worden ook gerangschikt op de `Rating`van het hotel.

```javascript
console.log('Query #2 - Search with filter, orderBy, and select:');
let state = 'FL';
searchOptions = {
    filter: odata`Address/StateProvince eq ${state}`,
    orderBy: ["Rating desc"],
    select: ["HotelId", "HotelName", "Rating"]
};

searchResults = await searchClient.search("wifi", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

Vervolgens kan de zoekopdracht worden beperkt tot één doorzoekbaar veld met behulp van de parameter `searchFields`. Dit is een uitstekende optie om uw query efficiënter te maken als u weet dat u alleen geïnteresseerd bent in overeenkomende resultaten in bepaalde velden. 

```javascript
console.log('Query #3 - Limit searchFields:');
searchOptions = {
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("sublime cliff", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
console.log();
```

Een andere veelgebruikte optie die in een query moet worden opgenomen, is `facets`. Met facetten kunt u filters maken op uw gebruikersinterface, zodat gebruikers eenvoudig kunnen zien welke waarden ze kunnen filteren.

```javascript
console.log('Query #4 - Use facets:');
searchOptions = {
    facets: ["Category"],
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("*", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

De laatste query maakt gebruik van de methode `getDocument()` van de `searchClient`. Zo kunt u een document efficiënt ophalen met de bijbehorende sleutel. 

```javascript
console.log('Query #5 - Lookup document:');
let documentResult = await searchClient.getDocument(key='3')
console.log(`HotelId: ${documentResult.HotelId}; HotelName: ${documentResult.HotelName}`)
```

### <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Voer het programma uit met `node index.js`. Nu worden de query's, als aanvulling op de vorige stappen, verzonden en worden de resultaten naar de console geschreven.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze JavaScript-quickstart hebt u een reeks taken uitgevoerd om een index te maken, hier documenten in te laden en query's uit te voeren. 

Als u al enige ervaring met Azure Cognitive Search hebt, kunt u dit voorbeeld gebruiken als springplank om een suggestiefunctie (type-ahead of automatisch aangevulde query's), filters en facetnavigatie te proberen. Als u Azure Cognitive Search voor het eerst gebruikt, is het raadzaam andere zelfstudies te bekijken om inzicht te verwerven in wat u zoal kunt maken. Bezoek de [documentatiepagina](https://azure.microsoft.com/documentation/services/search/) voor meer resources. 

> [!div class="nextstepaction"]
> [Een reactief front-end voor Azure-Cognitive Search bouwen](https://github.com/dereklegenzoff/azure-search-react-template)