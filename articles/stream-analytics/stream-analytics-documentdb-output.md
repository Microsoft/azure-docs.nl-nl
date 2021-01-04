---
title: Azure Stream Analytics uitvoer naar Azure Cosmos DB
description: In dit artikel wordt beschreven hoe u Azure Stream Analytics kunt gebruiken om de uitvoer op te slaan in Azure Cosmos DB voor JSON-uitvoer, voor gegevens archivering en query's met lage latentie voor ongestructureerde JSON-gegevens.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: 35231eda43e766b5febd8ba90c4d92a44537e0ef
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97703752"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics uitvoer naar Azure Cosmos DB  
Azure Stream Analytics kan gericht zijn op [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) voor json-uitvoer, waardoor gegevens worden gearchiveerd en query's met lage latentie worden uitgevoerd op ONgestructureerde JSON-gegevens. Dit document bevat enkele aanbevolen procedures voor het implementeren van deze configuratie. Het is raadzaam om uw taak in te stellen op compatibiliteits niveau 1,2 wanneer u Azure Cosmos DB als uitvoer gebruikt.

Als u niet bekend bent met Azure Cosmos DB, raadpleegt u de [Azure Cosmos DB documentatie](../cosmos-db/index.yml) om aan de slag te gaan. 

> [!Note]
> Op dit moment ondersteunt Stream Analytics alleen verbinding met Azure Cosmos DB via de *SQL-API*.
> Andere Azure Cosmos DB Api's worden nog niet ondersteund. Als u Stream Analytics naar Azure Cosmos DB accounts die zijn gemaakt met andere Api's, zijn de gegevens mogelijk niet op de juiste manier opgeslagen. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Basis beginselen van Azure Cosmos DB als uitvoer doel
Met de Azure Cosmos DB uitvoer in Stream Analytics kunt u de resultaten van de stroom verwerking in uw Azure Cosmos DB containers schrijven als JSON-uitvoer. 

Stream Analytics maakt geen containers in uw data base. In plaats daarvan moet u ze vooraan maken. U kunt vervolgens de facturerings kosten van Azure Cosmos DB containers beheren. U kunt ook de prestaties, consistentie en capaciteit van uw containers rechtstreeks afstemmen met behulp van de [Azure Cosmos DB-api's](/rest/api/cosmos-db/).

> [!Note]
> U moet 0.0.0.0 toevoegen aan de lijst met toegestane IP-adressen van uw Azure Cosmos DB firewall.

In de volgende secties worden enkele van de container opties voor Azure Cosmos DB besproken.

## <a name="tuning-consistency-availability-and-latency"></a>Consistentie, Beschik baarheid en latentie afstemmen
Om te voldoen aan de vereisten van uw toepassing, kunt u met Azure Cosmos DB de data base en containers nauw keuriger afstemmen en de beschik baarheid van consistentie, bewerkings periode, latentie en door Voer afzetten. 

Afhankelijk van de niveaus van de Lees consistentie die uw scenario nodig heeft voor lees-en schrijf latentie, kunt u een consistentie niveau voor uw database account kiezen. U kunt de door Voer verbeteren door het schalen van de aanvraag eenheden (RUs) op de container. 

Standaard maakt Azure Cosmos DB synchroon indexeren op elke ruwe bewerking naar uw container. Dit is een andere handige optie voor het beheren van de prestaties van schrijven/lezen in Azure Cosmos DB. 

Raadpleeg het artikel [uw data base en query consistentie niveaus wijzigen](../cosmos-db/consistency-levels.md) voor meer informatie.

## <a name="upserts-from-stream-analytics"></a>Upsert van Stream Analytics
Stream Analytics integratie met Azure Cosmos DB kunt u records in uw container invoegen of bijwerken op basis van een bepaalde **document-ID-** kolom. Dit wordt ook wel een *upsert* genoemd.

Stream Analytics maakt gebruik van een optimistische upsert-benadering. Updates worden alleen uitgevoerd wanneer een invoegen mislukt met een document-ID-conflict. 

Met compatibiliteits niveau 1,0 wordt deze update door Stream Analytics uitgevoerd als een PATCH bewerking, zodat er gedeeltelijke updates voor het document mogelijk zijn. Stream Analytics voegt nieuwe eigenschappen toe of wordt stapsgewijs een bestaande eigenschap vervangen. Wijzigingen in de waarden van matrix eigenschappen in het JSON-document resulteren echter in het overschrijven van de volledige matrix. Dat wil zeggen dat de matrix niet is samengevoegd. 

Met 1,2 wordt het gedrag upsert gewijzigd om het document in te voegen of te vervangen. In de volgende sectie over compatibiliteits niveau 1,2 wordt dit gedrag verder beschreven.

Als het inkomende JSON-document een bestaand ID-veld heeft, wordt dat veld automatisch gebruikt als de kolom **document-id** in azure Cosmos db. Eventuele daaropvolgende schrijf bewerkingen worden zo behandeld, waardoor er een van deze situaties is:

- Unieke Id's kunnen worden ingevoegd.
- Dubbele Id's en **document-id** ingesteld op **id** leiden naar upsert.
- Dubbele Id's en **document-id** zijn niet ingesteld op fout, na het eerste document.

Als u *alle* documenten wilt opslaan, inclusief de bestanden met een dubbele id, wijzigt u de naam van het veld id in uw query (met behulp van het sleutel woord **as** ). Laat Azure Cosmos DB het veld ID maken of de ID vervangen door de waarde van een andere kolom (met behulp van het sleutel woord **as** of met de instelling voor de **document-id** ).

## <a name="data-partitioning-in-azure-cosmos-db"></a>Gegevens partitioneren in Azure Cosmos DB
Met Azure Cosmos DB worden partities automatisch geschaald op basis van uw werk belasting. We raden u daarom aan [onbeperkte](../cosmos-db/partitioning-overview.md) containers te plaatsen als benadering voor het partitioneren van uw gegevens. Als Stream Analytics naar een onbeperkt aantal containers schrijft, worden er zoveel parallelle schrijvers als de vorige query stap of het schema voor het partitioneren van gegevens gebruikt.

> [!NOTE]
> Azure Stream Analytics ondersteunt alleen onbeperkte containers met partitie sleutels op het hoogste niveau. `/region`Wordt bijvoorbeeld ondersteund. Geneste partitie sleutels (bijvoorbeeld `/region/name` ) worden niet ondersteund. 

Afhankelijk van de partitie sleutel die u kiest, kan deze _waarschuwing_ worden weer gegeven:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Het is belang rijk om een partitie sleutel eigenschap te kiezen die een aantal afzonderlijke waarden heeft en waarmee u de werk belasting gelijkmatig over deze waarden kunt verdelen. Als een natuurlijk artefact van partitioneren, worden aanvragen die betrekking hebben op dezelfde partitie sleutel beperkt door de maximale door Voer van één partitie. 

De opslag grootte voor documenten die deel uitmaken van dezelfde partitie sleutel waarde is beperkt tot 20 GB (de limiet voor de [fysieke partitie grootte](../cosmos-db/partitioning-overview.md) is 50 GB). Een [ideale partitie sleutel](../cosmos-db/partitioning-overview.md#choose-partitionkey) is een code die regel matig wordt weer gegeven als filter in uw query's en voldoende kardinaliteit heeft om ervoor te zorgen dat uw oplossing schaalbaar is.

Partitie sleutels die worden gebruikt voor Stream Analytics query's en Cosmos DB hoeven niet identiek te zijn. Volledig parallelle topologieën raden u aan om een *invoer partitie sleutel* te gebruiken, `PartitionId` , als de stream Analytics de partitie sleutel van de query, maar dit is mogelijk niet de aanbevolen keuze voor de partitie sleutel van een Cosmos DB container.

Een partitie sleutel is ook de grens voor trans acties in opgeslagen procedures en triggers voor Azure Cosmos DB. U moet de partitie sleutel kiezen, zodat documenten die in trans acties samen komen, dezelfde partitie sleutel waarde hebben. Het artikel [partitioneren in azure Cosmos DB](../cosmos-db/partitioning-overview.md) biedt meer informatie over het kiezen van een partitie sleutel.

Voor vaste Azure Cosmos DB-containers kunt Stream Analytics op geen enkele manier omhoog of omlaag schalen nadat ze vol zijn. Ze hebben een maximum van 10 GB en 10.000 RU/s van door voer. Als u de gegevens van een vaste container wilt migreren naar een onbeperkte container (bijvoorbeeld een met ten minste 1.000 RU/s en een partitie sleutel), gebruikt u het [hulp programma voor gegevens migratie](../cosmos-db/import-data.md) of de [bibliotheek voor wijzigings invoer](../cosmos-db/change-feed.md).

De mogelijkheid om te schrijven naar meerdere vaste containers wordt afgeschaft. Het is niet raadzaam om uw Stream Analytics-taak uit te schalen.

## <a name="improved-throughput-with-compatibility-level-12"></a>Verbeterde door Voer met compatibiliteits niveau 1,2
Met compatibiliteits niveau 1,2 ondersteunt Stream Analytics systeem eigen integratie om bulksgewijs te schrijven naar Azure Cosmos DB. Met deze integratie kunnen Azure Cosmos DB worden geschreven tijdens het maximaliseren van de door Voer en het efficiënt verwerken van bandbreedte aanvragen. 

Het verbeterde schrijf mechanisme is beschikbaar onder een nieuw compatibiliteits niveau vanwege een verschil in het upsert-gedrag. Met een niveau vóór 1,2, het upsert-gedrag is het invoegen of samen voegen van het document. Met 1,2 wordt het gedrag upsert gewijzigd om het document in te voegen of te vervangen.

Stream Analytics maakt gebruik 1,2 van een aangepaste opgeslagen procedure om documenten per partitie sleutel te upsert in Azure Cosmos DB. Er wordt een batch geschreven als een trans actie. Zelfs wanneer één record een tijdelijke fout (beperking) oplegt, moet de hele batch opnieuw worden geprobeerd. Dit maakt scenario's met zelfs redelijk trage vertraging.

In het volgende voor beeld ziet u twee identieke Stream Analytics taken die van dezelfde Azure Event Hubs-invoer worden gelezen. Beide Stream Analytics taken zijn [volledig gepartitioneerd](./stream-analytics-parallelization.md#embarrassingly-parallel-jobs) met een Passthrough-query en kunnen naar identieke Azure Cosmos DB containers worden geschreven. De metrische gegevens aan de linkerkant zijn afkomstig uit de taak die is geconfigureerd met compatibiliteits niveau 1,0. De metrische gegevens aan de rechter kant worden geconfigureerd met 1,2. De partitie sleutel van een Azure Cosmos DB container is een unieke GUID die afkomstig is van de invoer gebeurtenis.

![Vergelijking van Stream Analytics metrieken](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

De binnenkomende gebeurtenis snelheid in Event Hubs is twee keer hoger dan Azure Cosmos DB containers (20.000 RUs) worden geconfigureerd om in te nemen, waardoor de beperking in Azure Cosmos DB wordt verwacht. De taak met 1,2 wordt echter constant geschreven met een hogere door Voer (uitvoer gebeurtenissen per minuut) en met een lager gemiddeld percentage van SU%. In uw omgeving is dit verschil afhankelijk van meer factoren. Deze factoren omvatten de keuze van de gebeurtenis indeling, invoer gebeurtenis/bericht grootte, partitie sleutels en query.

![Vergelijking van metrische gegevens Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Met 1,2 is Stream Analytics intelligenter in het gebruik van 100 procent van de beschik bare door Voer in Azure Cosmos DB met weinig herverzendingen tegen beperking of frequentie beperking. Dit biedt een betere ervaring voor andere werk belastingen, zoals query's die op de container worden uitgevoerd. Als u wilt zien hoe Stream Analytics met Azure Cosmos DB wordt geschaald als een Sink voor 1.000 tot 10.000 berichten per seconde, probeert u  [Dit Azure-voorbeeld project](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-cosmosdb).

De door Voer van Azure Cosmos DB uitvoer is identiek aan 1,0 en 1,1. We *raden u ten zeerste* aan dat u het compatibiliteits niveau 1,2 in stream Analytics gebruikt met Azure Cosmos db.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Azure Cosmos DB instellingen voor JSON-uitvoer

Met Azure Cosmos DB als uitvoer in Stream Analytics wordt de volgende prompt voor informatie gegenereerd.

![Informatie velden voor een Azure Cosmos DB uitvoer stroom](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Veld           | Beschrijving|
|-------------   | -------------|
|Uitvoeralias    | Een alias om naar deze uitvoer in uw Stream Analytics-query te verwijzen.|
|Abonnement    | Het Azure-abonnement.|
|Account-id      | De naam of eind punt-URI van het Azure Cosmos DB-account.|
|Accountsleutel     | De gedeelde toegangs sleutel voor het Azure Cosmos DB-account.|
|Database        | De naam van de Azure Cosmos DB-Data Base.|
|Containernaam | De naam van de container, zoals `MyContainer` . Er is een container met de naam `MyContainer` moet bestaan.  |
|Document-ID     | Optioneel. De kolom naam in uitvoer gebeurtenissen die wordt gebruikt als de unieke sleutel waarop INSERT-of update-bewerkingen moeten worden gebaseerd. Als u dit leeg laat, worden alle gebeurtenissen ingevoegd, zonder update optie.|

Nadat u de Azure Cosmos DB uitvoer hebt geconfigureerd, kunt u deze in de query gebruiken als het doel van een [into-instructie](/stream-analytics-query/into-azure-stream-analytics). Wanneer u een Azure Cosmos DB uitvoer op die manier gebruikt, [moet een partitie sleutel expliciet worden ingesteld](./stream-analytics-parallelization.md#partitions-in-inputs-and-outputs). 

De uitvoer record moet een hoofdletter gevoelige kolom bevatten met de naam na de partitie sleutel in Azure Cosmos DB. Om meer parallel Lise ring te verkrijgen, kan de instructie een [partitie by-component](./stream-analytics-parallelization.md#embarrassingly-parallel-jobs) vereisen die gebruikmaakt van dezelfde kolom.

Hier ziet u een voorbeeld:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Foutafhandeling en nieuwe pogingen

Als er een tijdelijke fout is opgetreden, wordt de service niet beschik baarheid of beperkt wanneer Stream Analytics gebeurtenissen verzendt naar Azure Cosmos DB, Stream Analytics de bewerking voor onbepaalde tijd wordt uitgevoerd. Er worden echter geen nieuwe pogingen gedaan voor de volgende fouten:

- Niet toegestaan (HTTP-fout code 401)
- NotFound (HTTP-fout code 404)
- Verboden (HTTP-fout code 403)
- Onjuiste aanvraag (HTTP-fout code 400)

## <a name="common-issues"></a>Algemene problemen

1. Een unieke index beperking wordt toegevoegd aan de verzameling en de uitvoer gegevens van Stream Analytics schenden deze beperking. Zorg ervoor dat de uitvoer gegevens van Stream Analytics niet overtreden op unieke beperkingen of verwijder de beperkingen. Zie [beperkingen voor unieke sleutels in azure Cosmos DB](../cosmos-db/unique-keys.md)voor meer informatie.

2. De `PartitionKey` kolom bestaat niet.

3. De `Id` kolom bestaat niet.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de uitvoer van Azure Stream Analytics](stream-analytics-define-outputs.md) 
* [Azure Stream Analytics uitvoer naar Azure SQL Database](stream-analytics-sql-output-perf.md)
* [Aangepaste BLOB-uitvoer partitioneren Azure Stream Analytics](stream-analytics-custom-path-patterns-blob-storage-output.md)