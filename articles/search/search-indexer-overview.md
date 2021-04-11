---
title: Overzicht van de indexeerfunctie
titleSuffix: Azure Cognitive Search
description: Crawl Azure SQL Database, SQL Managed instance, Azure Cosmos DB of Azure Storage om Doorzoek bare gegevens op te halen en een Azure Cognitive Search-index te vullen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 6cce37a7c719c6a0c183e166fa28967ea926a221
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581642"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexeerfuncties in Azure Cognitive Search

Een *Indexeer functie* in azure Cognitive Search is een crawler waarmee Doorzoek bare tekst en meta gegevens worden geëxtraheerd uit een externe Azure-gegevens bron en waarmee een zoek index wordt gevuld met veld-naar-veld Toewijzingen tussen bron gegevens en uw index. Deze methode wordt ook wel ' pull model ' genoemd, omdat de service gegevens ophaalt zonder dat u code hoeft te schrijven waarmee gegevens worden toegevoegd aan een index. Indexeer functies bieden ook de [AI-verrijkings](cognitive-search-concept-intro.md) mogelijkheden van Cognitive Search, waarbij externe verwerking van inhoud en route naar een index wordt geïntegreerd.

Indexeer functies zijn alleen Azure, met afzonderlijke Indexeer functies voor [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md), [Azure Table Storage](search-howto-indexing-azure-tables.md) en [Blob Storage](search-howto-indexing-azure-blob-storage.md). Wanneer u een Indexeer functie configureert, geeft u een gegevens bron op (oorsprong), evenals een index (doel). Verschillende bronnen, zoals Blob Storage, hebben aanvullende configuratie-eigenschappen die specifiek zijn voor dat inhouds type.

U kunt op aanvraag Indexeer functies of een periodiek schema voor gegevens vernieuwing uitvoeren dat wordt uitgevoerd op elke vijf minuten. Voor frequentere updates is een [push model](search-what-is-data-import.md) vereist waarmee gelijktijdig gegevens worden bijgewerkt in zowel Azure Cognitive Search als uw externe gegevens bron.

## <a name="usage-scenarios"></a>Gebruiksscenario's

U kunt een Indexeer functie gebruiken als enige manier voor het opnemen van gegevens, of als onderdeel van een combi natie van technieken die het laden en eventueel verrijken van inhoud op de weg worden getransformeerd. De volgende tabel bevat een overzicht van de belangrijkste scenario's.

| Scenario |Strategie |
|----------|---------|
| Enkele gegevens bron | Dit patroon is de eenvoudigste: één gegevens bron is de enige inhouds provider voor een zoek index. Vanuit de bron identificeert u een veld dat unieke waarden bevat om als document sleutel in de zoek index te dienen. De unieke waarde wordt gebruikt als een id. Alle andere bron velden worden impliciet of expliciet toegewezen aan overeenkomende velden in een index. </br></br>Een belang rijke maakt is dat de waarde van een document sleutel afkomstig is uit bron gegevens. Met een zoek service worden geen sleutel waarden gegenereerd. Bij volgende uitvoeringen worden inkomende documenten met nieuwe sleutels toegevoegd, terwijl inkomende documenten met bestaande sleutels worden samengevoegd of overschreven, afhankelijk van het feit of de index velden null of leeg zijn. |
| Meerdere gegevens bronnen | Een index kan inhoud van meerdere bronnen accepteren, waarbij elke uitvoering nieuwe inhoud van een andere bron brengt. </br></br>Een resultaat kan een index zijn die documenten verkrijgt nadat elke Indexeer functie is uitgevoerd, waarbij volledige documenten volledig van elke bron worden gemaakt. Documenten 1-100 zijn bijvoorbeeld afkomstig uit Blob Storage, documenten 101-200 van Azure SQL, enzovoort. De uitdaging voor dit scenario is het ontwerpen van een index schema dat geschikt is voor alle inkomende gegevens en een document sleutel structuur die uniform is in de zoek index. Systeem eigen, de waarden die een document uniek identificeren, worden metadata_storage_path in een BLOB-container en een primaire sleutel in een SQL-tabel. U kunt zich Voorst Ellen dat een of beide bronnen moeten worden gewijzigd om sleutel waarden in een gemeen schappelijke indeling te bieden, ongeacht de oorsprong van de inhoud. Voor dit scenario moet u een bepaald niveau van vooraf-verwerking uitvoeren om de gegevens te vermengen zodat deze kunnen worden opgenomen in één index. </br></br>Een alternatieve uitkomst kan zoeken in documenten die gedeeltelijk zijn ingevuld bij de eerste uitvoering en vervolgens verder worden ingevuld door volgende uitvoeringen om waarden uit andere bronnen te halen. Bijvoorbeeld: Fields 1-10 zijn van Blob Storage, 11-20 van Azure SQL, enzovoort. De uitdaging van dit patroon is ervoor te zorgen dat de uitvoering van elke index hetzelfde document heeft. Voor het samen voegen van velden in een bestaand document is een overeenkomst vereist voor de document sleutel. Zie [zelf studie: index uit meerdere gegevens bronnen](tutorial-multiple-data-sources.md)voor een demonstratie van dit scenario. |
| Meerdere Indexeer functies | Als u meerdere gegevens bronnen gebruikt, hebt u mogelijk ook meerdere Indexeer functies nodig als u de uitvoerings tijd parameters, het schema of de veld toewijzingen wilt variëren. Hoewel meerdere Indexeer functie-gegevens bron sets dezelfde index kunnen bereiken, moet u voorzichtig zijn met Indexeer functies waarmee bestaande waarden in de index kunnen worden overschreven. Als een tweede Indexeer functie-gegevens bron dezelfde documenten en velden als doel heeft, worden alle waarden van de eerste uitvoering overschreven. Veld waarden worden volledig vervangen. een Indexeer functie kan geen waarden van meerdere uitvoeringen samen voegen in hetzelfde veld.</br></br>Een andere use-case voor meerdere indexen is [het verschil tussen regio's Cognitive Search](search-performance-optimization.md#data-sync). Mogelijk hebt u kopieën van dezelfde zoek index in verschillende regio's. Als u de inhoud van de zoek index wilt synchroniseren, kunt u meerdere Indexeer functies uit dezelfde gegevens bron halen, waarbij elke Indexeer functie een andere zoek index als doel heeft.</br></br>Voor het [parallel indexeren](search-howto-large-index.md#parallel-indexing) van zeer grote gegevens sets is ook een strategie met meerdere indexen vereist. Elke Indexeer functie is gericht op een subset van de gegevens. |
| Inhouds transformatie | Cognitive Search ondersteunt optionele [AI-verrijkings](cognitive-search-concept-intro.md) gedragingen waarmee afbeeldings analyse en natuurlijke taal verwerking worden toegevoegd om nieuwe Doorzoek bare inhoud en structuur te maken. AI-verrijking is gebaseerd op Indexeer functie via een gekoppelde [vaardig heden](cognitive-search-working-with-skillsets.md). Voor het uitvoeren van AI-verrijking heeft de Indexeer functie nog steeds een index en een Azure-gegevens bron nodig, maar in dit scenario voegt de vaardigheidset-verwerking toe aan de uitvoering van de Indexeer functie. |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen

Indexeer functies verkennen gegevens archieven in Azure.

+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (in preview-versie)
+ [Azure Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server op virtuele machines in Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

Indexeer verbindingen met externe gegevens bronnen kunnen worden gemaakt met behulp van standaard Internet verbindingen (openbaar) of versleutelde particuliere verbindingen wanneer u Azure Virtual Networks gebruikt voor client-apps. U kunt ook verbindingen instellen om te verifiëren met behulp van een vertrouwde service-identiteit. Zie voor meer informatie over beveiligde verbindingen [toegang verlenen via persoonlijke eind punten](search-indexer-securing-resources.md#granting-access-via-private-endpoints) en [verbinding maken met een gegevens bron met behulp van een beheerde identiteit](search-howto-managed-identities-data-sources.md).

## <a name="stages-of-indexing"></a>Stadia van indexeren

Bij een eerste uitvoering, wanneer de index leeg is, leest een Indexeer functie alle gegevens die zijn opgegeven in de tabel of container. Bij volgende uitvoeringen kan de Indexeer functie normaal gesp roken alleen de gewijzigde gegevens detecteren en ophalen. Voor BLOB-gegevens wordt de detectie van wijzigingen automatisch. Voor andere gegevens bronnen, zoals Azure SQL of Cosmos DB, moet u de functie voor het detecteren van wijzigingen inschakelen.

Voor elk document dat wordt ontvangen, implementeert een Indexeer functie meerdere stappen, van het ophalen van documenten naar een laatste zoek engine "besteld" voor het indexeren. Een Indexeer functie is optioneel ook een instrumentatie bij het aansturen van vaardig heden en uitvoer, ervan uitgaande dat er een kwalificatieset is gedefinieerd.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Indexerings fasen" border="false":::

### <a name="stage-1-document-cracking"></a>Fase 1: document kraken

Het kraken van documenten is het proces van het openen van bestanden en het uitpakken van inhoud. Afhankelijk van het type gegevens bron probeert de Indexeer functie verschillende bewerkingen uit te voeren om mogelijk Indexeer bare inhoud op te halen.  

Voorbeelden:  

+ Wanneer het document een record in een [Azure SQL-gegevens bron](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)is, haalt de Indexeer functie elk van de velden voor de record op.
+ Wanneer het document een PDF-bestand is in een [Azure Blob Storage-gegevens bron](search-howto-indexing-azure-blob-storage.md), worden de tekst, afbeeldingen en meta gegevens door de Indexeer functie opgehaald.
+ Wanneer het document een record in een [Cosmos DB gegevens bron](search-howto-index-cosmosdb.md)is, haalt de Indexeer functie de velden en subvelden uit het Cosmos DB document.

### <a name="stage-2-field-mappings"></a>Fase 2: veld toewijzingen 

Met een Indexeer functie wordt tekst uit een bron veld geëxtraheerd en naar een doel veld in een index of kennis archief verzonden. Als veld namen en typen samen vallen, wordt het pad gewist. Het is echter mogelijk dat u verschillende namen of typen in de uitvoer wilt hebben. in dat geval moet u de Indexeer functie vertellen hoe u het veld wilt toewijzen. 

Deze stap treedt op na het kraken van documenten, maar vóór trans formaties, wanneer de Indexeer functie van de bron documenten wordt gelezen. Wanneer u een [veld toewijzing](search-indexer-field-mappings.md)definieert, wordt de waarde van het Bron veld verzonden als-is naar het doel veld zonder wijzigingen. 

### <a name="stage-3-skillset-execution"></a>Fase 3: uitvoering van vaardig heden

Het uitvoeren van vaardig heden is een optionele stap voor het aanroepen van ingebouwde of aangepaste AI-verwerking. U hebt deze mogelijk nodig voor optische teken herkenning (OCR) in de vorm van afbeeldings analyse als de bron gegevens een binaire afbeelding zijn of als de inhoud in verschillende talen kan worden vertaald. 

Welke trans formatie, vaardig heden-uitvoering wordt uitgevoerd, waar verrijkingen plaatsvinden. Als een Indexeer functie een pijp lijn is, kunt u een [vaardig heden](cognitive-search-defining-skillset.md) beschouwen als een ' pijp lijn in de pijp lijn '.

### <a name="stage-4-output-field-mappings"></a>Fase 4: uitvoer veld toewijzingen

Als u een vaardig heden opneemt, moet u waarschijnlijk uitvoer veld Toewijzingen bevatten. De uitvoer van een vaardig heden is in feite een structuur van gegevens die het *verrijkte document* worden genoemd. Met de toewijzingen van het uitvoer veld kunt u selecteren welke delen van deze boom structuur in de velden in uw index moeten worden toegewezen. Meer informatie over het [definiëren van uitvoer veld Toewijzingen](cognitive-search-output-field-mapping.md).

Terwijl veld Toewijzingen Verbatim waarden van de gegevens bron koppelen aan doel velden, geven uitvoer veld Toewijzingen de Indexeer functie aan hoe de getransformeerde waarden in het verrijkte document moeten worden gekoppeld aan doel velden in de index. In tegens telling tot veld toewijzingen, die als optioneel worden beschouwd, moet u altijd een toewijzing van een uitvoer veld definiëren voor getransformeerde inhoud die in een index moet worden opgeslagen.

De volgende afbeelding toont een voor beeld van een debug-sessie voor het [opsporen van fouten](cognitive-search-debug-session.md) in de Indexeer fase: document kraken, veld toewijzingen, vaardigheidset-uitvoering en uitvoer veld toewijzingen.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="voor beeld van foutopsporingssessie" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>Basiswerkstroom

Indexeerfuncties kunnen functies bieden die uniek voor de gegevensbron zijn. In dit opzicht variëren bepaalde aspecten van de configuratie van de indexeerfunctie of de gegevensbron al naar gelang het type indexeerfunctie. Alle indexeerfuncties hebben echter dezelfde basissamenstelling en voor alle indexeerfuncties gelden dezelfde vereisten. Hieronder vindt u de stappen die voor alle indexeerfuncties gemeenschappelijk zijn.

### <a name="step-1-create-a-data-source"></a>Stap 1: Een gegevensbron maken

Voor Indexeer functies is een *gegevens bron* object vereist dat een Connection String en mogelijk referenties bevat. Roep de-klasse [Create Data Source (rest)](/rest/api/searchservice/create-data-source) of [SearchIndexerDataSourceConnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) aan om de resource te maken.

Gegevensbronnen worden geconfigureerd en onafhankelijk van de indexeerfuncties beheerd die gebruikmaken van de gegevensbronnen. Dit betekent dat een gegevensbron door meerdere indexeerfuncties kan worden gebruikt om tegelijkertijd meer dan één index te laden.

### <a name="step-2-create-an-index"></a>Stap 2: een index maken

Een indexeerfunctie automatiseert bepaalde taken met betrekking tot de opname van gegevens, maar het maken van een index behoort hier niet toe. Een vereiste is dat u een vooraf gedefinieerde index moet hebben met velden die overeenkomen met de velden in uw externe gegevensbron. Velden moeten overeenkomen met de naam en het gegevens type. Als dat niet het geval is, kunt u [veld toewijzingen definiëren](search-indexer-field-mappings.md) om de koppeling tot stand te brengen. Zie [een index maken (rest)](/rest/api/searchservice/Create-Index) of [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex)voor meer informatie over het structureren van een index.

> [!Tip]
> Hoewel indexeerfuncties een index niet voor u kunnen genereren, kan de wizard **Gegevens importeren** in de portal helpen. In de meeste gevallen kan de wizard een indexschema afleiden uit bestaande metagegevens in de bron, met een voorlopig indexschema dat u kunt bewerken terwijl de wizard actief is. Als de index eenmaal is aangemaakt op de service, blijven verdere bewerkingen in de portal meestal beperkt tot het toevoegen van nieuwe velden. Overweeg de wizard voor het maken, maar niet voor het herzien van een index. Doorloop het [Portaloverzicht](search-get-started-portal.md) om aan de slag te gaan.

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>Stap 3: de Indexeer functie maken en uitvoeren (of plannen)

Een Indexeer functie wordt uitgevoerd wanneer u voor het eerst [een Indexeer functie maakt](/rest/api/searchservice/Create-Indexer) in de zoek service. Het wordt alleen weer gegeven wanneer u de Indexeer functie maakt of uitvoert die u zult ontdekken als de gegevens bron toegankelijk is of de vaardig heden geldig zijn. Nadat de eerste keer is uitgevoerd, kunt u deze op aanvraag opnieuw uitvoeren met de [Indexeer functie uitvoeren](/rest/api/searchservice/run-indexer)of kunt u [een terugkerend schema definiëren](search-howto-schedule-indexers.md). 

U kunt de status van de Indexeer functie bewaken in de portal of via de API van de [Indexeer functie ophalen](/rest/api/searchservice/get-indexer-status). U moet ook [query's uitvoeren op de index](search-query-create.md) om te controleren of het resultaat is wat u verwacht.

## <a name="next-steps"></a>Volgende stappen

Nu u een volgende stap hebt geïntroduceerd, is het mogelijk om de Indexeer functie-eigenschappen en-para meters, planning en indexerings bewaking te controleren. U kunt ook teruggaan naar de lijst met [ondersteunde gegevens bronnen](#supported-data-sources) voor meer informatie over een specifieke bron.

+ [Indexeer functies maken](search-howto-create-indexers.md)
+ [Indexeer functies opnieuw instellen en uitvoeren](search-howto-run-reset-indexers.md)
+ [Indexeerfuncties inplannen](search-howto-schedule-indexers.md)
+ [Veld toewijzingen definiëren](search-indexer-field-mappings.md)
+ [Indexeer functie status bewaken](search-howto-monitor-indexers.md)