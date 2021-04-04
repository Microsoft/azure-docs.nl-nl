---
title: Verbeter de doorvoer prestaties naar Azure SQL Database van Azure Stream Analytics
description: Meer informatie over het uitvoeren van gegevens naar SQL Azure van Azure Stream Analytics en een hogere doorvoer snelheid voor schrijf bewerkingen.
author: chetanmsft
ms.author: chetang
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 8baa33c8d9622ff76db04345f5c6c465f026e261
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020227"
---
# <a name="increase-throughput-performance-to-azure-sql-database-from-azure-stream-analytics"></a>Verbeter de doorvoer prestaties naar Azure SQL Database van Azure Stream Analytics

In dit artikel worden tips beschreven voor betere schrijf doorvoer prestaties wanneer u gegevens laadt in Azure SQL Database met behulp van Azure Stream Analytics.

SQL-uitvoer in Azure Stream Analytics ondersteunt schrijven parallel als een optie. Met deze optie kunt u [volledig parallelle](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) taak topologieën maken waarbij meerdere uitvoer partities parallel worden geschreven naar de doel tabel. Het inschakelen van deze optie in Azure Stream Analytics is echter mogelijk niet voldoende voor hogere door Voer, omdat deze aanzienlijk afhankelijk is van uw database configuratie en tabel schema. De keuze van indexen, cluster sleutel, index opvul factor en compressie hebben invloed op de tijd voor het laden van tabellen. Zie [SQL database performance guidance](../azure-sql/database/performance-guidance.md)(Engelstalig) voor meer informatie over hoe u uw data base optimaliseert om de prestaties van query's en taken te verbeteren op basis van interne benchmarks. De volg orde van schrijf bewerkingen is niet gegarandeerd bij het parallel schrijven naar SQL Database.

Hier vindt u enkele configuraties binnen elke service die de algehele door Voer van uw oplossing kunnen helpen verbeteren.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Partities overnemen** : met deze configuratie optie voor SQL-uitvoer kunt u het partitie schema van uw vorige query stap of-invoer overnemen. Als u deze functie inschakelt en schrijft naar een tabel op basis van een schijf en een [volledig parallelle](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologie voor uw taak hebt, wordt verwacht dat u betere door voeren ziet. Deze partitionering vindt al veel andere [uitvoer](stream-analytics-parallelization.md#partitions-in-inputs-and-outputs)plaats. Tabel vergrendeling (TABLOCK hebt) is ook uitgeschakeld voor bulk toevoegingen die zijn gemaakt met deze optie.

> [!NOTE] 
> Wanneer er meer dan 8 invoer partities zijn, is het overnemen van het schema voor de invoer partitie mogelijk niet de juiste keuze. Deze bovengrens is waargenomen in een tabel met één identiteits kolom en een geclusterde index. In dit geval kunt u overwegen om het aantal uitvoer schrijvers expliciet op te geven [in 8 in](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) uw query. Uw opmerkingen zijn afhankelijk van het schema en de keuze van de indexen.

- **Batch grootte** -SQL-uitvoer configuratie Hiermee kunt u de maximale Batch grootte in een Azure stream Analytics SQL-uitvoer opgeven op basis van de aard van de doel tabel of-workload. Batch grootte is het maximum aantal records dat wordt verzonden met elke bulksgewijze insert-trans actie. In geclusterde column Store-indexen kunnen batch grootten rond [100.000](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) meer parallel Lise ring, minimale logboek registratie en vergrendelings optimalisaties bieden. In op schijven gebaseerde tabellen kan 10K (standaard) of lager optimaal zijn voor uw oplossing, omdat een hogere Batch grootte de vergrendelings escalatie kan activeren tijdens bulksgewijs invoegen.

- **Afstemming van invoer berichten** : als u hebt geoptimaliseerd voor het overnemen van partitionering en Batch grootte, kunt u het aantal invoer gebeurtenissen per bericht per partitie verhogen om uw schrijf doorvoer verder te verfijnen. Met de functie voor het afstemmen van invoer berichten kunnen batch grootten binnen Azure Stream Analytics worden ingesteld op de opgegeven Batch grootte, waardoor de door Voer wordt verbeterd. Dit kan worden bereikt door [compressie](stream-analytics-define-inputs.md) te gebruiken of de grootte van het invoer bericht te verhogen in EventHub of BLOB.

## <a name="sql-azure"></a>SQL Azure

- **Gepartitioneerde tabel en indexen** : door gebruik te maken van een [gepartitioneerde](/sql/relational-databases/partitions/partitioned-tables-and-indexes) SQL-tabel en gepartitioneerde indexen in de tabel met dezelfde kolom als uw partitie sleutel (bijvoorbeeld PartitionId) kunnen de conflicten tussen partities tijdens het schrijven aanzienlijk verminderen. Voor een gepartitioneerde tabel moet u een [partitie functie](/sql/t-sql/statements/create-partition-function-transact-sql) en een [partitie schema](/sql/t-sql/statements/create-partition-scheme-transact-sql) maken voor de primaire bestands groep. Hierdoor wordt ook de beschik baarheid van bestaande gegevens verhoogd terwijl er nieuwe gegevens worden geladen. De i/o-limiet voor logboeken kan worden bereikt op basis van het aantal partities dat kan worden verhoogd door de SKU bij te werken.

- **Vermijd unieke sleutel schendingen** : als u [waarschuwingen met meerdere sleutel overtredingen](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) ontvangt in het activiteiten logboek van Azure stream Analytics, moet u ervoor zorgen dat uw taak niet wordt beïnvloed door de schendingen van unieke beperkingen die waarschijnlijk optreden tijdens herstel cases. Dit kan worden vermeden door [de \_ \_ sleutel optie dubbele negeren](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) in te stellen op uw indexen.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure Data Factory-en In-Memory tabellen

- **In-Memory tabel als tijdelijke tabel** - [in-Memory tabellen](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) kunnen zeer snelle gegevens worden geladen, maar gegevens moeten in het geheugen passen. Benchmarks tonen het bulksgewijs laden van een in-Memory tabel naar een tabel op basis van een schijf is ongeveer tien keer sneller dan het rechtstreeks invoegen van een enkele schrijver in de op schijven gebaseerde tabel met een identiteits kolom en een geclusterde index. Als u gebruik wilt maken van deze bulksgewijs ingevoegde prestaties, stelt u een [Kopieer taak in met Azure Data Factory](../data-factory/connector-azure-sql-database.md) waarmee gegevens uit de tabel in het geheugen worden gekopieerd naar de tabel op basis van de schijf.

## <a name="avoiding-performance-pitfalls"></a>Prestatie problemen voor komen
Bulksgewijs invoegen van gegevens is veel sneller dan het laden van gegevens met één plaats, omdat de Herhaal bare overhead voor het overdragen van de gegevens, het parseren van de instructie INSERT, het uitvoeren van de-instructie en het uitgeven van een transactie record wordt voor komen. In plaats daarvan wordt er een efficiënt pad naar de opslag engine gebruikt om de gegevens te streamen. De instellings kosten van dit pad zijn echter veel hoger dan een instructie INSERT in een tabel op basis van een schijf. Het eindeloze punt is doorgaans ongeveer 100 rijen, waardoor bulksgewijs laden bijna altijd efficiënt is. 

Als de frequentie van binnenkomende gebeurtenissen laag is, kan het eenvoudig batch formaten maken die kleiner zijn dan 100 rijen, waardoor bulksgewijs inefficiënt inactief kan worden ingevoegd en te veel schijf ruimte gebruikt. Als u deze beperking wilt omzeilen, kunt u een van de volgende acties uitvoeren:
* Maak een in plaats van een [trigger](/sql/t-sql/statements/create-trigger-transact-sql) om eenvoudig invoegen te gebruiken voor elke rij.
* Gebruik een In-Memory tijdelijke tabel zoals beschreven in de vorige sectie.

Een ander scenario treedt op tijdens het schrijven naar een niet-geclusterde column store-index (NCCI), waarbij kleinere bulk toevoegingen te veel segmenten kunnen maken, waardoor de index kan vastlopen. In dit geval is de aanbeveling een geclusterde column store-index te gebruiken.

## <a name="summary"></a>Samenvatting

In samen vatting, met de gepartitioneerde uitvoer functie in Azure Stream Analytics voor SQL-uitvoer, wordt uitgelijnde parallel Lise ring van uw taak met een gepartitioneerde tabel in SQL Azure u aanzienlijke verbeteringen in de door voer geven. Het gebruik van Azure Data Factory voor het indelen van gegevens verplaatsing vanuit een In-Memory tabel in op schijven gebaseerde tabellen kan een volg orde van omvang van de doorvoer snelheid bieden. Als dat mogelijk is, kan het verbeteren van de bericht dichtheid ook een belang rijke factor zijn bij het verbeteren van de algehele door voer.
