---
title: Een indexeerfunctie maken
titleSuffix: Azure Cognitive Search
description: Stel eigenschappen in een Indexeer functie in om de oorsprong en bestemming van de gegevens te bepalen. U kunt para meters instellen om runtime-gedrag te wijzigen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: 5fc47599d09e5be60311dbda15868d87de4d91d2
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509381"
---
# <a name="creating-indexers-in-azure-cognitive-search"></a>Indexeer functies maken in azure Cognitive Search

Een Indexeer functie biedt een geautomatiseerde werk stroom voor het overbrengen van documenten en inhoud vanuit een externe gegevens bron naar een zoek index in uw zoek service. Zoals oorspronkelijk is ontworpen, worden tekst en meta gegevens uit een Azure-gegevens bron geëxtraheerd, worden documenten geserialiseerd in JSON en worden de resulterende documenten door gegeven aan een zoek machine voor het indexeren. Dit is sindsdien uitgebreid ter ondersteuning van [AI-verrijking](cognitive-search-concept-intro.md) voor grondige verwerking van inhoud. 

Door Indexeer functies te gebruiken, wordt het aantal en de complexiteit van de code die u moet schrijven aanzienlijk verminderd. Dit artikel richt zich op de mechanismen voor het maken van een Indexeer functie als voor bereiding voor geavanceerdere werkzaamheden met [vaardig heden](cognitive-search-working-with-skillsets.md)en-functies.

## <a name="whats-an-indexer-definition"></a>Wat is een indexerings definitie?

Indexeer functies worden gebruikt voor op tekst gebaseerde indexering die alfanumerieke inhoud ophaalt van bron velden in index velden, of op AI gebaseerde verwerking die niet-gedifferentieerde tekst analyseert voor de structuur, of afbeeldingen analyseert voor tekst en informatie, ook de inhoud toevoegen aan een index. De volgende index definities zijn gebruikelijk van wat u voor beide scenario's zou kunnen maken.

### <a name="indexers-for-text-content"></a>Indexeer functies voor tekst inhoud

Het oorspronkelijke doel van een Indexeer functie was het vereenvoudigen van het complexe proces van het laden van een index door een mechanisme te bieden voor het maken van verbinding met en het lezen van tekst en numerieke inhoud van velden in een gegevens bron, het serialiseren van die inhoud als JSON-documenten en het afleveren van deze documenten aan de zoek machine voor het indexeren. Dit is nog steeds een primair gebruiks voorbeeld en voor deze bewerking moet u een Indexeer functie maken met de eigenschappen die in het volgende voor beeld zijn gedefinieerd.

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String indicated which existing data source to use,
  "targetIndexName": (required) String,
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [ optional unless there are field discrepancies that need resolution]
}
```

De **`name`** **`dataSourceName`** Eigenschappen, en **`targetIndexName`**  zijn vereist, afhankelijk van hoe u de Indexeer functie maakt, moeten zowel de gegevens bron als de index al bestaan in de service voordat u de Indexeer functie kunt uitvoeren. 

De **`parameters`** eigenschap wijzigt het uitvoerings tijd gedrag, zoals het aantal fouten dat moet worden geaccepteerd voordat de volledige taak wordt uitgevoerd. Met para meters kunt u ook bron-specifieke gedragingen opgeven. Als de bron Blob Storage is, kunt u bijvoorbeeld een para meter instellen die filtert op bestands extensies: `"parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }` .

De **`field mappings`** eigenschap wordt gebruikt voor het expliciet toewijzen van bron-naar-doel velden als deze velden een andere naam of een ander type hebben. Andere eigenschappen (niet weer gegeven), worden gebruikt om [een schema](search-howto-schedule-indexers.md)op te geven, de Indexeer functie in een uitgeschakelde status te maken of een [versleutelings sleutel](search-security-manage-encryption-keys.md) op te geven voor een bijkomende versleuteling van gegevens in rust.

### <a name="indexers-for-ai-indexing"></a>Indexeer functies voor AI-indexering

Indexeer functies zijn het mechanisme waarmee uitgaande aanvragen door een zoek service worden verwerkt, indexeringen zijn uitgebreid om AI-verrijkingen te ondersteunen, infra structuur en objecten toe te voegen voor het implementeren van deze use-case.

Alle bovenstaande eigenschappen en para meters zijn van toepassing op Indexeer functies die AI-verrijking uitvoeren. De volgende eigenschappen zijn specifiek voor AI-verrijking: **`skillSets`** , **`outputFieldMappings`** , **`cache`** (alleen voor beeld en alleen rest). 

```json
{
  "name": (required) String that uniquely identifies the indexer,
  "dataSourceName": (required) String, name of an existing data source,
  "targetIndexName": (required) String, name of an existing index,
  "skillsetName" : (required for AI enrichment) String, name of an existing skillset,
  "cache":  {
    "storageConnectionString" : (required for caching AI enriched content) Connection string to a blob container,
    "enableReprocessing": true
    },
  "parameters": {
    "batchSize": null,
    "maxFailedItems": null,
    "maxFailedItemsPerBatch": null
  },
  "fieldMappings": [],
  "outputFieldMappings" : (required for AI enrichment) { ... },
}
```

AI-verrijking valt buiten het bereik van dit artikel. Voor meer informatie kunt u beginnen met de volgende artikelen: [AI-verrijking](cognitive-search-concept-intro.md), [vaardig heden in azure Cognitive Search](cognitive-search-working-with-skillsets.md)en [vaardig heden maken (rest)](/rest/api/searchservice/create-skillset).

## <a name="choose-an-indexer-client-and-create-the-indexer"></a>Een Indexeer functie-client kiezen en de Indexeer functie maken

Wanneer u klaar bent om een Indexeer functie te maken op een externe zoek service, hebt u een zoek-client nodig in de vorm van een hulp programma, zoals Azure Portal of Postman, of code die een Indexeer functie-client instantiet. We raden u aan de Azure Portal-of REST-Api's voor vroegtijdige ontwikkeling en test-of-concept tests uit te voeren.

### <a name="permissions"></a>Machtigingen

Alle bewerkingen met betrekking tot Indexeer functies, waaronder GET-aanvragen voor status of definities, vereisen een [admin API-sleutel](search-security-api-keys.md) voor de aanvraag.

### <a name="limits"></a>Limieten

Alle [service lagen beperken](search-limits-quotas-capacity.md#indexer-limits) het aantal objecten dat u kunt maken. Als u op de laag gratis experimenteert, kunt u slechts drie objecten van elk type hebben en 2 minuten van index verwerking (exclusief vaardigheidset-verwerking).

### <a name="use-azure-portal-to-create-an-indexer"></a>Azure Portal gebruiken om een Indexeer functie te maken

De portal biedt twee opties voor het maken van een Indexeer functie: [**wizard gegevens importeren**](search-import-data-portal.md) en **nieuwe Indexeer functie** waarmee velden worden opgegeven voor het opgeven van een definitie van een Indexeer functie. De wizard is uniek in het maken van alle vereiste elementen. Voor andere benaderingen moet u een gegevens bron en index vooraf hebben gedefinieerd.

De volgende scherm afbeelding laat zien waar u deze functies in de portal kunt vinden. 

  :::image type="content" source="media/search-howto-create-indexers/portal-indexer-client.png" alt-text="indexeerfunctie voor hotels" border="true":::

### <a name="use-a-rest-client"></a>Een REST-client gebruiken

Postman en Visual Studio code (met een extensie voor Azure Cognitive Search) kunnen worden gebruikt als een indexer-client. Met beide hulp middelen kunt u verbinding maken met uw zoek service en aanvragen voor [Indexeer functie (rest)](/rest/api/searchservice/create-indexer) verzenden. Er zijn talloze zelf studies en voor beelden waarin REST-clients voor het maken van objecten worden gedemonstreerd. 

Begin met een van deze artikelen voor meer informatie over elke client:

+ [Een zoek index maken met behulp van REST en postman](search-get-started-rest.md)
+ [Aan de slag met Visual Studio Code en Azure Cognitive Search](search-get-started-vs-code.md)

Raadpleeg de [operationser-bewerkingen (rest)](/rest/api/searchservice/Indexer-operations) voor hulp bij het formuleren van indexerings aanvragen.

### <a name="use-an-sdk"></a>Een SDK gebruiken

Voor Cognitive Search implementeren de Azure-Sdk's algemeen beschik bare functies. Zo kunt u een van de Sdk's gebruiken om objecten die aan de Indexeer functie zijn gerelateerd te maken. Allemaal bieden een **SearchIndexerClient** met methoden voor het maken van Indexeer functies en gerelateerde objecten, waaronder vaardig heden.

| Azure SDK | Client | Voorbeelden |
|-----------|--------|----------|
| .NET | [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) | [DotNetHowToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) |
| Java | [SearchIndexerClient](/java/api/com.azure.search.documents.indexes.searchindexerclient) | [CreateIndexerExample. java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) |
| Javascript | [SearchIndexerClient](/javascript/api/@azure/search-documents/searchindexerclient) | [Indexeerfuncties](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |
| Python | [SearchIndexerClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexerclient) | [sample_indexers_operations. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) |

## <a name="run-the-indexer"></a>De indexeerfunctie uitvoeren

Een Indexeer functie wordt automatisch uitgevoerd wanneer u de Indexeer functie voor de service maakt. Dit is het moment waarop u zich kunt voordoen als er verbindings fouten met de gegevens bron, problemen met de veld toewijzing of vaardigheids worden gevonden. 

Er zijn verschillende manieren waarop u een Indexeer functie kunt uitvoeren:

+ Verzend een HTTP-aanvraag voor [Create Indexeer functie](/rest/api/searchservice/create-indexer) of [Update Indexeer functie](/rest/api/searchservice/update-indexer) om de definitie toe te voegen of te wijzigen en voer de Indexeer functie uit.

+ Een HTTP-aanvraag voor [Run Indexeer functie](/rest/api/searchservice/run-indexer) verzenden om een Indexeer functie zonder wijzigingen in de definitie uit te voeren.

+ Voer een programma uit dat SearchIndexerClient-methoden aanroept voor Create, update of run.

> [!NOTE]
> Als u wilt voor komen dat een Indexeer functie onmiddellijk wordt uitgevoerd bij het maken, neemt u **`disabled=true`** in de definitie van de Indexeer functie op.

U kunt de Indexeer functie ook [op een schema](search-howto-schedule-indexers.md) plaatsen om de verwerking met regel matige tussen pozen aan te roepen. 

Geplande verwerking komt doorgaans overeen met een behoefte aan incrementele indexering van gewijzigde inhoud. Wijzigingen detectie logica is een functie die is ingebouwd in bron platforms. Wijzigingen in een BLOB-container worden automatisch gedetecteerd door de Indexeer functie. Raadpleeg de documentatie van de Indexeer functie voor specifieke gegevens bronnen voor hulp bij het gebruik van wijzigingen detectie in andere gegevens bronnen:

+ [Azure SQL database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) (Azure SQL-database)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="know-your-data"></a>Uw gegevens weten

Indexeer functies verwachten een set rijen in een tabel, waarbij elke rij een volledig of gedeeltelijk Zoek document in de index wordt. Vaak is er sprake van een een-op-een-correspondentie tussen een rij en het resulterende Zoek document, waarbij alle velden in de rij elk document volledig invullen. Maar u kunt Indexeer functies gebruiken om slechts een deel van een document te genereren, bijvoorbeeld als u meerdere Indexeer functies of benaderingen gebruikt voor het samen stellen van de index. 

Als u relationele gegevens wilt samen voegen in een rij-set, moet u een SQL-weer gave maken of een query bouwen waarmee bovenliggende en onderliggende records in dezelfde rij worden geretourneerd. De voor beeld-gegevensset van ingebouwde hotels is bijvoorbeeld een SQL database met 50 records (één voor elk hotel), gekoppeld aan room-records in een gerelateerde tabel. De query die de collectieve gegevens samenvoegt in een rij-set, sluit alle room-informatie in JSON-documenten in elk Hotel record in. De Inge sloten room-informatie is een gegenereerd door een query die gebruikmaakt van de component **for JSON auto** . Meer informatie over deze techniek vindt u in [een query definiëren die een Inge sloten JSON retourneert](index-sql-relational-data.md#define-a-query-that-returns-embedded-json). Dit is slechts één voor beeld. u kunt andere benaderingen vinden waarmee hetzelfde effect wordt bereikt.

Naast het samen voegen van gegevens is het belang rijk dat u alleen Doorzoek bare gegevens ophaalt. Doorzoek bare gegevens zijn alfanumeriek. Cognitive Search kunt niet in een wille keurige indeling zoeken naar binaire gegevens, maar kan ook tekst beschrijvingen van afbeeldings bestanden ophalen en afleiden (Zie [AI-verrijking](cognitive-search-concept-intro.md)) om Doorzoek bare inhoud te maken. Op dezelfde manier kan grote tekst met behulp van AI-verrijking worden geanalyseerd door middel van natuurlijke taal modellen om structuur of relevante informatie te vinden en nieuwe inhoud te genereren die u aan een zoek document kunt toevoegen.

Op voor waarde dat Indexeer functies geen gegevens problemen oplossen, is het mogelijk dat er andere vormen van het opruimen of manipuleren van gegevens nodig zijn. Raadpleeg voor meer informatie de product documentatie van uw [Azure data base-product](/azure/?product=databases).

## <a name="know-your-index"></a>Ken uw index

Stel in dat Indexeer functies de zoek documenten niet door geven aan de zoek machine voor het indexeren. Net zoals Indexeer functies eigenschappen hebben die het uitvoerings gedrag bepalen, bevat een index schema eigenschappen die van invloed zijn op hoe teken reeksen worden geïndexeerd (alleen teken reeksen worden geanalyseerd en getokend). Afhankelijk van analyse toewijzingen kunnen geïndexeerde teken reeksen afwijken van wat u hebt door gegeven in. U kunt de effecten van analyse functies evalueren met behulp van [tekst analyseren (rest)](/rest/api/searchservice/test-analyzer). Zie voor meer informatie over analyse functies [analyseren voor tekst verwerking](search-analyzers.md).

Met een Indexeer functie worden alleen veld namen en typen gecontroleerd op basis van de manier waarop Indexeer functies met een index werken. Er is geen validerings stap die ervoor zorgt dat inkomende inhoud juist is voor het bijbehorende zoek veld in de index. Als verificatie stap kunt u query's uitvoeren op de gevulde index waarmee hele documenten of geselecteerde velden worden geretourneerd. Zie [een eenvoudige query maken](search-query-create.md)voor meer informatie over het uitvoeren van query's op de inhoud van een index.

## <a name="next-steps"></a>Volgende stappen

+ [Indexeerfuncties inplannen](search-howto-schedule-indexers.md)
+ [Veld toewijzingen definiëren](search-indexer-field-mappings.md)
+ [Indexeer functie status bewaken](search-howto-monitor-indexers.md)
+ [Verbinding maken met behulp van beheerde identiteiten](search-howto-managed-identities-data-sources.md)