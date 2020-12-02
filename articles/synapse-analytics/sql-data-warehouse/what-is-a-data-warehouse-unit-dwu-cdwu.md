---
title: Data warehouse units (Dwu's) voor toegewezen SQL-groep (voorheen SQL DW)
description: Aanbevelingen voor het kiezen van het ideale aantal Data Warehouse Units (DWU's) om de prijs en prestaties te optimaliseren en hoe u het aantal units kunt wijzigen.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3d9d5d4009ad40eecee26271b726c6a3e9aeb8b6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459195"
---
# <a name="data-warehouse-units-dwus-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Data warehouse units (Dwu's) voor toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics

Aanbevelingen voor het kiezen van het ideale aantal Data Warehouse Units (DWU's) om de prijs en prestaties te optimaliseren en hoe u het aantal units kunt wijzigen.

## <a name="what-are-data-warehouse-units"></a>Wat zijn Data Warehouse Units?

Een [toegewezen SQL-groep (voorheen SQL DW)](sql-data-warehouse-overview-what-is.md) vertegenwoordigt een verzameling analytische resources die worden ingericht. Analytische resources worden gedefinieerd als een combinatie van CPU, geheugen en IO.

Deze drie resources worden gebundeld in rekenschaaleenheden, ook wel Data Warehouse Units (DWU's) genoemd. Een DWU vertegenwoordigt een abstracte, genormaliseerde meting van rekenresources en prestaties.

Door een wijziging in uw serviceniveau wordt het aantal beschikbare DWU's voor het systeem gewijzigd, waardoor de prestaties en de kosten van uw systeem worden aangepast.

Voor betere prestaties kunt u het aantal DWU's verhogen. Voor mindere prestaties vermindert u het aantal DWU's. Opslag-en rekenkosten worden afzonderlijk gefactureerd, zodat het wijzigen van DWU's geen invloed heeft op de opslagkosten.

De prestaties van DWU's zijn gebaseerd op de metrische gegevens van de workload van deze datawarehouses:

- Hoe snel een standaard-query van een exclusieve SQL-groep (voorheen SQL DW) een groot aantal rijen kan scannen en vervolgens een complexe aggregatie kan uitvoeren. Dit is een I/O-bewerking waarbij de CPU intensief wordt belast.
- Hoe snel de toegewezen SQL-groep (voorheen SQL DW) gegevens uit Azure Storage blobs of Azure Data Lake kan opnemen. Dit is een netwerkbewerking waarbij de CPU intensief wordt belast.
- Hoe snel de [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL-opdracht een tabel kan kopiëren. Deze bewerking omvat het lezen van gegevens uit de opslag, het verdelen van de gegevens over de knooppunten van het systeem en het weer terugschrijven van de gegevens naar de opslag. Dit is een bewerking waarbij de CPU, de IO en het netwerk intensief worden belast.

DWU's verhogen:

- Lineaire wijzigingen in de prestaties van het systeem voor scans, aggregaties en CTAS-instructies
- Verhoogt het aantal lezers en schrijvers voor PolyBase-laadbewerkingen
- Hiermee wordt het maximale aantal gelijktijdige query's en gelijktijdigheidssleuven verhoogd.

## <a name="service-level-objective"></a>Service Level Objective

De Service Level Objective (SLO) is de schaalbaarheidsinstelling waarmee de kosten en het prestatieniveau van uw datawarehouse worden bepaald. De serviceniveaus voor Gen2 worden gemeten in compute Data Warehouse Units (cDWU), bijvoorbeeld DW2000c. Gen1-serviceniveaus worden gemeten in DWU's, bijvoorbeeld DW2000.

De serviceniveau doelstelling (SLO) is de schaal baarheid-instelling waarmee de kosten en het prestatie niveau van uw toegewezen SQL-groep (voorheen SQL DW) worden bepaald. De service niveaus voor Gen2 toegewezen SQL-groep (voorheen SQL DW) worden gemeten in data warehouse units (DWU), bijvoorbeeld DW2000c.

> [!NOTE]
> Een toegewezen SQL-groep (voorheen SQL DW) Gen2 heeft onlangs extra schaal mogelijkheden toegevoegd ter ondersteuning van reken lagen van 100 cDWU. Bestaande datawarehouses met Gen1 waarvoor de lagere rekenlagen zijn vereist, kunnen nu kosteloos worden bijgewerkt naar Gen2 in de regio's die momenteel beschikbaar zijn.  Als uw regio nog niet wordt ondersteund, kunt u nog steeds een upgrade uitvoeren naar een ondersteunde regio. Zie [Upgrade naar Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie.

In T-SQL bepaalt de SERVICE_OBJECTIVE instelling het service niveau en de prestatie-laag voor uw toegewezen SQL-groep (voorheen SQL DW).

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Prestatielagen en Data Warehouse Units

In elke prestatielaag wordt niet precies dezelfde maateenheid voor de Data Warehouse Units gebruikt. Dit verschil wordt op de factuur weergegeven wanneer de schaaleenheid rechtstreeks wordt omgezet in de facturering.

- Gen1-datawarehouses worden gemeten in Data Warehouse Units (DWU's).
- Gen2-datawarehouses worden gemeten in compute Data Warehouse Units (cDWU's).

Zowel DWU's als cDWU's ondersteunen het schalen van rekenkracht naar boven of beneden en het pauzeren van rekenkracht wanneer u het datawarehouse niet hoeft te gebruiken. Deze bewerkingen zijn allemaal on-demand. Gen2 maakt gebruik van een lokale schijfcache op de rekenknooppunten om de prestaties te verbeteren. Wanneer u het systeem schaalt of pauzeert, is de cache ongeldig en moet de cache gedurende een bepaalde periode worden opgewarmd voordat optimale prestaties worden bereikt.  

Elke SQL-server (bijvoorbeeld myserver.database.windows.net) heeft een [DTU-quotum (Database Transaction Unit)](../../azure-sql/database/service-tiers-dtu.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) dat een specifiek aantal DWU's toestaat. Zie de [Capaciteitslimieten voor workloadbeheer](sql-data-warehouse-service-capacity-limits.md#workload-management) voor meer informatie.

## <a name="capacity-limits"></a>Capaciteitslimieten

Elke SQL-server (bijvoorbeeld myserver.database.windows.net) heeft een [DTU-quotum (Database Transaction Unit)](../../sql-database/sql-database-what-is-a-dtu.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) dat een specifiek aantal DWU's toestaat. Zie de [Capaciteitslimieten voor workloadbeheer](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management) voor meer informatie.

## <a name="how-many-data-warehouse-units-do-i-need"></a>Hoeveel DWU's heb ik nodig?

Het ideale aantal DWU's hangt zeer veel af van uw workload en de hoeveelheid gegevens die u in het systeem hebt geladen.

Stappen voor het vinden van de beste DWU voor uw workload:

1. Begin door een kleinere DWU te selecteren.
2. Bewaak de prestaties van uw toepassing tijdens het testen van gegevens die in het systeem worden geladen, waarbij u het aantal geselecteerde DWU's vergelijkt met de prestaties die u ziet.
3. Identificeer eventuele aanvullende vereisten voor periodieke perioden met piekactiviteit. Workloads die aanzienlijke pieken en dalen in activiteiten tonen, moeten mogelijk regelmatig worden geschaald.

Een toegewezen SQL-groep (voorheen SQL DW) is een scale-out systeem waarmee u enorme hoeveel heden gegevens kunt inrichten en opvragen.

Als u de echte mogelijkheden voor schalen wilt zien, met name bij grotere DWU's, kunt u het beste de gegevensset schalen terwijl u schaalt om ervoor te zorgen dat u voldoende invoer hebt voor de CPU's. Voor schaaltests kunt u het beste minimaal 1 TB gebruiken.

> [!NOTE]
>
> Queryprestaties worden alleen verhoogd met meer parallellisatie als het werk kan worden gesplitst tussen rekenknooppunten. Als u merkt dat de prestaties niet veranderen door het schalen, moet u mogelijk uw tabelontwerp en/of uw query's afstemmen. Zie [Gebruikersquery's beheren](cheat-sheet.md) voor meer informatie over het afstemmen van query's.

## <a name="permissions"></a>Machtigingen

Voor het wijzigen van de DWU's zijn de machtigingen vereist die worden beschreven in [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Ingebouwde Azure-rollen, zoals Inzender voor SQL-databases en Inzender voor SQL Server, kunnen DWU-instellingen wijzigen.

## <a name="view-current-dwu-settings"></a>Huidige DWU-instellingen weergeven

De huidige DWU-instellingen weergeven:

1. Open SQL Server-objectverkenner in Visual Studio.
2. Maak verbinding met de hoofddatabase die aan de logische SQL-Server is gekoppeld.
3. Maak een selectie in de dynamische beheerweergave sys.database_service_objectives. Hier volgt een voorbeeld:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>DWU's wijzigen

### <a name="azure-portal"></a>Azure Portal

DWU's wijzigen:

1. Open de [Azure-portal](https://portal.azure.com), open uw database en klik op **Schalen**.

2. Verplaats onder **Schalen** de schuifregelaar naar links of rechts om de DWU-instelling te wijzigen.

3. Klik op **Opslaan**. Er verschijnt een bevestigingsbericht. Klik op **Ja** om te bevestigen of **Nee** om te annuleren.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u de DWU's wilt wijzigen, gebruikt u de PowerShell-cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase). In het volgende voorbeeld wordt de serviceniveaudoelstelling ingesteld op DW1000 voor de database MijnSQLDW die wordt gehost op server MijnServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Zie [Power shell-cmdlets voor exclusieve SQL-groep (voorheen SQL DW)](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie.

### <a name="t-sql"></a>T-SQL

Met T-SQL kunt u de huidige DWU-instellingen weergeven, de instellingen wijzigen en de voortgang controleren.

De DWU's wijzigen:

1. Maak verbinding met de hoofddatabase die aan uw server is gekoppeld.
2. Gebruik de TSQL-instructie [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). In het volgende voorbeeld wordt de serviceniveaudoelstelling ingesteld op DW1000c voor de database MijnSQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>REST-API’s

Als u de DWU's wilt wijzigen, gebruikt u de REST API [Database maken of bijwerken](/rest/api/sql/databases/createorupdate). In het volgende voorbeeld wordt de serviceniveaudoelstelling ingesteld op DW1000c voor de database MijnSQLDW die wordt gehost op server MijnServer. De server bevindt zich in een Azure-resourcegroep met de naam ResourceGroep1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Zie [rest api's voor exclusieve SQL-groep (voorheen SQL DW)](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)voor meer rest API voor beelden.

## <a name="check-status-of-dwu-changes"></a>Status van DWU-wijzigingen controleren

Het kan enkele minuten duren voordat DWU-wijzigingen zijn doorgevoerd. Als u automatisch schaalt, kunt u overwegen logica te implementeren om ervoor te zorgen dat bepaalde bewerkingen zijn voltooid voordat u doorgaat met een andere actie.

Door de databasestatus via verschillende eindpunten te controleren, kunt u automatiseringen juist implementeren. De portal geeft een melding nadat een bewerking is voltooid en de huidige status van de databases, maar staat geen programmatische controle van de status toe.

U kunt de status van de database niet controleren op scale-outbewerkingen met de Azure-portal.

De status van DWU-wijzigingen controleren:

1. Maak verbinding met de hoofddatabase die aan uw server is gekoppeld.
2. Verzend de volgende query om de status van de database te controleren.

```sql
SELECT    *
FROM      sys.databases
;
```

1. Verzend de volgende query om de status van de bewerking te controleren

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```

Deze DMV retourneert informatie over verschillende beheer bewerkingen op uw toegewezen SQL-groep (voorheen SQL DW), zoals de bewerking en de status van de bewerking, die IN_PROGRESS of voltooid is.

## <a name="the-scaling-workflow"></a>De werkstroom voor schalen

Wanneer u een schaalbewerking start, beëindigt het systeem eerst alle geopende sessies. Alle geopende transacties worden teruggedraaid om een consistente status te garanderen. Schaalbewerkingen worden alleen uitgevoerd nadat de transactionele terugdraaibewerking is voltooid.  

- Bij omhoog schalen worden alle rekenknooppunten losgekoppeld, worden er extra rekenknooppunten ingericht die vervolgens opnieuw worden gekoppeld aan de opslaglaag.
- Bij omlaag schalen worden alle rekenknooppunten losgekoppeld en worden vervolgens alleen de benodigde knooppunten opnieuw gekoppeld aan de opslaglaag.

## <a name="next-steps"></a>Volgende stappen

Zie [Resourceklassen voor workloadbeheer](resource-classes-for-workload-management.md) en [Geheugen en gelijktijdigheidslimieten](memory-concurrency-limits.md) voor meer informatie over het beheren van prestaties.
