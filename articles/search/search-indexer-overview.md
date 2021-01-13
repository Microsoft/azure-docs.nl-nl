---
title: Indexeer functies voor het verkennen van gegevens tijdens het importeren
titleSuffix: Azure Cognitive Search
description: Crawl Azure SQL Database, SQL Managed instance, Azure Cosmos DB of Azure Storage om Doorzoek bare gegevens op te halen en een Azure Cognitive Search-index te vullen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 0405db2b68abefbfdc424def9e35e363e45043cd
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180129"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexeerfuncties in Azure Cognitive Search

Een *Indexeer functie* in azure Cognitive Search is een crawler waarmee Doorzoek bare gegevens en meta gegevens worden geëxtraheerd uit een externe Azure-gegevens bron en waarmee een zoek index wordt gevuld met veld-naar-veld Toewijzingen tussen bron gegevens en uw index. Deze methode wordt ook wel ' pull model ' genoemd, omdat de service gegevens ophaalt zonder dat u code hoeft te schrijven waarmee gegevens worden toegevoegd aan een index.

Indexeer functies zijn alleen Azure, met afzonderlijke Indexeer functies voor Azure SQL, Azure Cosmos DB, Azure Table Storage en Blob Storage. Wanneer u een Indexeer functie configureert, geeft u een gegevens bron op (oorsprong), evenals een index (doel). Verschillende gegevens bronnen, zoals Blob Storage-Indexeer functies, hebben extra eigenschappen die specifiek zijn voor dat inhouds type.

U kunt op aanvraag Indexeer functies of een periodiek schema voor gegevens vernieuwing uitvoeren dat wordt uitgevoerd op elke vijf minuten. Voor frequentere updates is een push model vereist waarmee gelijktijdig gegevens worden bijgewerkt in zowel Azure Cognitive Search als uw externe gegevens bron.

## <a name="usage-scenarios"></a>Gebruiksscenario's

U kunt een Indexeer functie gebruiken als enige manier voor het opnemen van gegevens, of een combi natie van technieken gebruiken die het laden van een deel van de velden in uw index omvatten, waarbij u eventueel inhoud kunt transformeren of verrijken. De volgende tabel bevat een overzicht van de belangrijkste scenario's.

| Scenario |Strategie |
|----------|---------|
| Eén bron | Dit patroon is de eenvoudigste: één gegevens bron is de enige inhouds provider voor een zoek index. Vanuit de bron identificeert u een veld dat unieke waarden bevat om als document sleutel in de zoek index te dienen. De unieke waarde wordt gebruikt als een id. Alle andere bron velden worden impliciet of expliciet toegewezen aan overeenkomende velden in een index. </br></br>Een belang rijke maakt is dat de waarde van een document sleutel afkomstig is uit bron gegevens. Met een zoek service worden geen sleutel waarden gegenereerd. Bij volgende uitvoeringen worden inkomende documenten met nieuwe sleutels toegevoegd, terwijl inkomende documenten met bestaande sleutels worden samengevoegd of overschreven, afhankelijk van het feit of de index velden null of leeg zijn. |
| Meerdere bronnen| Een index kan inhoud van meerdere bronnen accepteren, waarbij elke uitvoering nieuwe inhoud van een andere bron brengt. </br></br>Een resultaat kan een index zijn die documenten verkrijgt nadat elke Indexeer functie is uitgevoerd, waarbij volledige documenten volledig van elke bron worden gemaakt. De uitdaging voor dit scenario is het ontwerpen van een index schema dat geschikt is voor alle inkomende gegevens en een document sleutel die uniform in de zoek index staat. Als de waarden die een document uniek identificeren bijvoorbeeld zijn metadata_storage_path in een BLOB-container en een primaire sleutel in een SQL-tabel, kunt u er Voorst Ellen dat een of beide bronnen moeten worden gewijzigd om sleutel waarden in een gemeen schappelijke indeling te bieden, ongeacht de oorsprong van de inhoud. Voor dit scenario moet u een bepaald niveau van vooraf-verwerking uitvoeren om de gegevens te vermengen zodat deze kunnen worden opgenomen in één index.</br></br>Een alternatieve uitkomst kan zoeken in documenten die gedeeltelijk zijn ingevuld bij de eerste uitvoering en vervolgens verder worden ingevuld door volgende uitvoeringen om waarden uit andere bronnen te halen. De uitdaging van dit patroon is ervoor te zorgen dat de uitvoering van elke index hetzelfde document heeft. Voor het samen voegen van velden in een bestaand document is een overeenkomst vereist voor de document sleutel. Zie [zelf studie: index uit meerdere gegevens bronnen](tutorial-multiple-data-sources.md)voor een demonstratie van dit scenario. |
| Inhouds transformatie | Cognitive Search ondersteunt optionele [AI-verrijkings](cognitive-search-concept-intro.md) gedragingen waarmee afbeeldings analyse en natuurlijke taal verwerking worden toegevoegd om nieuwe Doorzoek bare inhoud en structuur te maken. AI-verrijking wordt gedefinieerd door een [vaardig heden](cognitive-search-working-with-skillsets.md)die aan een Indexeer functie is gekoppeld. Voor het uitvoeren van AI-verrijking heeft de Indexeer functie nog steeds een index en gegevens bron nodig, maar in dit scenario voegt de vaardigheidset-verwerking toe aan de uitvoering van de Indexeer functie. |

## <a name="approaches-for-creating-and-managing-indexers"></a>Strategieën voor het maken en beheren van indexeerfuncties

U kunt op de volgende manieren indexeerfuncties maken en beheren:

+ [Wizard Portal-> gegevens importeren](search-import-data-portal.md)
+ [Service REST API](/rest/api/searchservice/Indexer-operations)
+ [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

Als u een SDK gebruikt, maakt u een [SearchIndexerClient](/dotnet/api/azure.search.documents.indexes.searchindexerclient) om te werken met Indexeer functies, gegevens bronnen en vaardig heden. De bovenstaande koppeling is voor de .NET SDK, maar alle Sdk's bieden een SearchIndexerClient en vergelijk bare Api's.

In eerste instantie worden nieuwe gegevens bronnen aangekondigd als preview-functies en alleen-REST. Na het afronden van de algemene Beschik baarheid is volledige ondersteuning ingebouwd in de portal en in de verschillende Sdk's, die allemaal op hun eigen release planningen zijn opgenomen.

## <a name="permissions"></a>Machtigingen

Voor alle bewerkingen met betrekking tot Indexeer functies, waaronder GET-aanvragen voor status of definities, is een [admin API-sleutel](search-security-api-keys.md)vereist.

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen

Indexeer functies verkennen gegevens archieven in Azure.

+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (in preview-versie)
+ [Azure-tabelopslag](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server op virtuele machines in Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

## <a name="stages-of-indexing"></a>Stadia van indexeren

Bij een eerste uitvoering, wanneer de index leeg is, leest een Indexeer functie alle gegevens die zijn opgegeven in de tabel of container. Bij volgende uitvoeringen kan de Indexeer functie normaal gesp roken alleen de gewijzigde gegevens detecteren en ophalen. Voor BLOB-gegevens wordt de detectie van wijzigingen automatisch. Voor andere gegevens bronnen, zoals Azure SQL of Cosmos DB, moet u de functie voor het detecteren van wijzigingen inschakelen.

Voor elk van de documenten die worden opgenomen in, implementeert een Indexeer functie meerdere stappen, van het ophalen van documenten naar een laatste zoek engine "besteld" voor het indexeren. Een Indexeer functie is optioneel ook een instrumentatie bij het aansturen van vaardig heden en uitvoer, ervan uitgaande dat er een kwalificatieset is gedefinieerd.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Indexerings fasen" border="false":::

### <a name="stage-1-document-cracking"></a>Fase 1: document kraken

Het kraken van documenten is het proces van het openen van bestanden en het uitpakken van inhoud. Afhankelijk van het type gegevens bron probeert de Indexeer functie verschillende bewerkingen uit te voeren om mogelijk Indexeer bare inhoud op te halen.  

Voorbeelden:  

+ Wanneer het document een record in een [Azure SQL-gegevens bron](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)is, haalt de Indexeer functie elk van de velden voor de record op.
+ Wanneer het document een PDF-bestand is in een [Azure Blob Storage-gegevens bron](search-howto-indexing-azure-blob-storage.md), worden de tekst, afbeeldingen en meta gegevens door de Indexeer functie opgehaald.
+ Wanneer het document een record in een [Cosmos DB gegevens bron](search-howto-index-cosmosdb.md)is, haalt de Indexeer functie de velden en subvelden uit het Cosmos DB document.

### <a name="stage-2-field-mappings"></a>Fase 2: veld toewijzingen 

Met een Indexeer functie wordt tekst uit een bron veld geëxtraheerd en naar een doel veld in een index of kennis archief verzonden. Als veld namen en typen samen vallen, wordt het pad gewist. Het is echter mogelijk dat u verschillende namen of typen in de uitvoer wilt hebben. in dat geval moet u de Indexeer functie vertellen hoe u het veld wilt toewijzen. Deze stap treedt op na het kraken van documenten, maar vóór trans formaties, wanneer de Indexeer functie van de bron documenten wordt gelezen. Wanneer u een [veld toewijzing](search-indexer-field-mappings.md)definieert, wordt de waarde van het Bron veld verzonden als-is naar het doel veld zonder wijzigingen. Veld Toewijzingen zijn optioneel.

### <a name="stage-3-skillset-execution"></a>Fase 3: uitvoering van vaardig heden

Het uitvoeren van vaardig heden is een optionele stap voor het aanroepen van ingebouwde of aangepaste AI-verwerking. U hebt deze mogelijk nodig voor optische teken herkenning (OCR) in de vorm van afbeeldings analyse, of u hebt mogelijk een vertaling van de taal nodig. Welke trans formatie, vaardig heden-uitvoering wordt uitgevoerd, waar verrijkingen plaatsvinden. Als een Indexeer functie een pijp lijn is, kunt u een [vaardig heden](cognitive-search-defining-skillset.md) beschouwen als een ' pijp lijn in de pijp lijn '. Een vaardig heden heeft zijn eigen volg orde van de stappen die vaardig heden worden genoemd.

### <a name="stage-4-output-field-mappings"></a>Fase 4: uitvoer veld toewijzingen

De uitvoer van een vaardig heden is in feite een structuur van gegevens die het verrijkte document worden genoemd. Met de toewijzingen van het uitvoer veld kunt u selecteren welke delen van deze boom structuur in de velden in uw index moeten worden toegewezen. Meer informatie over het [definiëren van uitvoer veld Toewijzingen](cognitive-search-output-field-mapping.md).

Net als veld toewijzingen waarmee Verbatim waarden van bron-naar doel velden worden gekoppeld, geven uitvoer veld Toewijzingen de Indexeer functie aan hoe de getransformeerde waarden in het verrijkte document moeten worden gekoppeld aan doel velden in de index. In tegens telling tot veld toewijzingen, die als optioneel worden beschouwd, moet u altijd een toewijzing van een uitvoer veld definiëren voor getransformeerde inhoud die in een index moet worden opgeslagen.

De volgende afbeelding toont een voor beeld van een debug-sessie voor het [opsporen van fouten](cognitive-search-debug-session.md) in de Indexeer fase: document kraken, veld toewijzingen, vaardigheidset-uitvoering en uitvoer veld toewijzingen.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="voor beeld van foutopsporingssessie" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-configuration-steps"></a>Basisconfiguratiestappen

Indexeerfuncties kunnen functies bieden die uniek voor de gegevensbron zijn. In dit opzicht variëren bepaalde aspecten van de configuratie van de indexeerfunctie of de gegevensbron al naar gelang het type indexeerfunctie. Alle indexeerfuncties hebben echter dezelfde basissamenstelling en voor alle indexeerfuncties gelden dezelfde vereisten. Hieronder vindt u de stappen die voor alle indexeerfuncties gemeenschappelijk zijn.

### <a name="step-1-create-a-data-source"></a>Stap 1: Een gegevensbron maken

Met een Indexeer functie wordt een gegevens bron verbinding opgehaald van een *gegevens bron* object. De definitie van de gegevens bron biedt een connection string en mogelijk referenties. Roep de-klasse [Create data source](/rest/api/searchservice/create-data-source) rest API of [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) aan om de resource te maken.

Gegevensbronnen worden geconfigureerd en onafhankelijk van de indexeerfuncties beheerd die gebruikmaken van de gegevensbronnen. Dit betekent dat een gegevensbron door meerdere indexeerfuncties kan worden gebruikt om tegelijkertijd meer dan één index te laden.

### <a name="step-2-create-an-index"></a>Stap 2: een index maken

Een indexeerfunctie automatiseert bepaalde taken met betrekking tot de opname van gegevens, maar het maken van een index behoort hier niet toe. Een vereiste is dat u een vooraf gedefinieerde index moet hebben met velden die overeenkomen met de velden in uw externe gegevensbron. Velden moeten overeenkomen met de naam en het gegevens type. Zie [Create a index (Azure Cognitive Search rest API)](/rest/api/searchservice/Create-Index) of [SearchIndex-klasse](/dotnet/api/azure.search.documents.indexes.models.searchindex)voor meer informatie over het structureren van een index. Zie [veld toewijzingen in Azure Cognitive Search-Indexeer functies](search-indexer-field-mappings.md)voor hulp bij veld koppelingen.

> [!Tip]
> Hoewel indexeerfuncties een index niet voor u kunnen genereren, kan de wizard **Gegevens importeren** in de portal helpen. In de meeste gevallen kan de wizard een indexschema afleiden uit bestaande metagegevens in de bron, met een voorlopig indexschema dat u kunt bewerken terwijl de wizard actief is. Als de index eenmaal is aangemaakt op de service, blijven verdere bewerkingen in de portal meestal beperkt tot het toevoegen van nieuwe velden. Overweeg de wizard voor het maken, maar niet voor het herzien van een index. Doorloop het [Portaloverzicht](search-get-started-portal.md) om aan de slag te gaan.

### <a name="step-3-create-and-schedule-the-indexer"></a>Stap 3: de indexeerfunctie maken en plannen

De definitie van de Indexeer functie is een constructie waarmee alle elementen worden gecombineerd die zijn gerelateerd aan gegevens opname. De vereiste elementen bevatten een gegevens bron en index. Optionele elementen bevatten een schema-en veld toewijzingen. Veld Toewijzingen zijn alleen optioneel als bron velden en index velden duidelijk overeenkomen. Zie [Indexeer functie maken (Azure Cognitive Search rest API)](/rest/api/searchservice/Create-Indexer)voor meer informatie over het structureren van een Indexeer functie.

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Indexeer functies op aanvraag uitvoeren

Hoewel het gebruikelijk is om het indexeren te plannen, kan een Indexeer functie ook op aanvraag worden aangeroepen met behulp van de [opdracht uitvoeren](/rest/api/searchservice/run-indexer):

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2020-06-30
api-key: [Search service admin key]
```

> [!NOTE]
> Wanneer run API een succes code retourneert, is de aanroep van de Indexeer functie gepland, maar wordt de daad werkelijke verwerking asynchroon uitgevoerd. 

U kunt de status van de Indexeer functie bewaken in de portal of via de API van de [Indexeer functie ophalen](/rest/api/searchservice/get-indexer-status). 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>De Indexeer functie-status ophalen

U kunt de status en de uitvoerings geschiedenis van een Indexeer functie ophalen met behulp van de [opdracht Get Indexing-status](/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

Het antwoord bevat de algemene status van de Indexeer functie, de laatste (of in uitvoering zijnde) indexerings aanroep en de geschiedenis van recente Indexeer functie-aanroepen.

```output
{
    "status":"running",
    "lastResult": {
        "status":"success",
        "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
     },
    "executionHistory":[ {
        "status":"success",
         "errorMessage":null,
        "startTime":"2018-11-26T03:37:18.853Z",
        "endTime":"2018-11-26T03:37:19.012Z",
        "errors":[],
        "itemsProcessed":11,
        "itemsFailed":0,
        "initialTrackingState":null,
        "finalTrackingState":null
    }]
}
```

De uitvoerings geschiedenis bevat tot de 50 meest recente voltooide uitvoeringen, die in omgekeerde chronologische volg orde worden gesorteerd (zodat de meest recente uitvoering eerst in het antwoord komt).

## <a name="next-steps"></a>Volgende stappen

Nu u het uitgangspunt hebt begrepen, is de volgende stap de vereisten en taken te bekijken die specifiek zijn voor elk gegevensbrontype.

+ [Azure SQL Database, SQL Managed instance of SQL Server op een virtuele machine van Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure-tabelopslag](search-howto-indexing-azure-tables.md)
+ [CSV-blobs indexeren met de Azure Cognitive Search BLOB-Indexer](search-howto-index-csv-blobs.md)
+ [JSON-blobs indexeren met Azure Cognitive Search BLOB-Indexer](search-howto-index-json-blobs.md)