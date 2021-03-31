---
title: Query's voor replica's lezen
description: Azure SQL biedt de mogelijkheid om de capaciteit van alleen-lezen replica's te gebruiken voor lees werkbelastingen, die read scale-out wordt genoemd.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 01/20/2021
ms.openlocfilehash: 5f9e7e1c96db2b60e41fe0ded69ea562cf8fcea6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663982"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>Alleen-lezen replica's gebruiken om werk belastingen met alleen-lezen query's te offloaden
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Als onderdeel van een [architectuur met hoge Beschik baarheid](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)wordt elke afzonderlijke Data Base, elastische pool-data base en een beheerd exemplaar in de Premium-en bedrijfskritiek-servicelaag automatisch ingericht met een primaire replica met het kenmerk alleen-lezen en enkele secundaire alleen-lezen replica's. De secundaire replica's worden ingericht met dezelfde reken grootte als de primaire replica. Met de functie voor het *lezen van scale-out* kunt u alleen-lezen workloads offloaden met behulp van de reken capaciteit van een van de alleen-lezen replica's, in plaats van deze op te starten in de replica lezen-schrijven. Op deze manier kunnen bepaalde alleen-lezen workloads worden geïsoleerd van de werk belastingen voor lezen en schrijven. Dit heeft geen invloed op de prestaties. De functie is bedoeld voor de toepassingen die logisch gescheiden alleen-lezen werk belastingen bevatten, zoals analyses. In de service lagen Premium en Bedrijfskritiek kunnen toepassingen prestatie voordelen krijgen met behulp van deze extra capaciteit zonder extra kosten.

De functie *scale-out lezen* is ook beschikbaar in de grootschalige, wanneer ten minste één secundaire replica is gemaakt. Meerdere secundaire replica's kunnen worden gebruikt voor werk belastingen met alleen-lezen taak verdeling waarvoor meer resources nodig zijn dan beschikbaar zijn op één secundaire replica.

De architectuur met hoge Beschik baarheid van de service lagen Basic, Standard en Algemeen bevat geen replica's. De functie *scale-out lezen* is niet beschikbaar in deze service lagen.

In het volgende diagram ziet u de functie.

![Alleen-lezen replica's](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

De functie voor het *lezen van scale-out* is standaard ingeschakeld voor nieuwe Premium-, bedrijfskritiek-en grootschalige-data bases. Voor grootschalige wordt standaard één secundaire replica gemaakt voor nieuwe data bases. 

> [!NOTE]
> Uitschalen voor lezen is altijd ingeschakeld in de service tier Bedrijfskritiek van het beheerde exemplaar.

Als uw SQL connection string is geconfigureerd met `ApplicationIntent=ReadOnly` , wordt de toepassing omgeleid naar een alleen-lezen replica van die data base of een beheerd exemplaar. Zie voor meer informatie over het gebruik van de `ApplicationIntent` eigenschap [toepassings intentie opgeven](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Als u er zeker van wilt zijn dat de toepassing verbinding maakt met de primaire replica, ongeacht de `ApplicationIntent` instelling in de SQL-Connection String, moet u het lezen van uitschalen expliciet uitschakelen bij het maken van de data base of bij het wijzigen van de configuratie. Als u bijvoorbeeld een upgrade uitvoert van uw data base van Standard of Algemeen laag naar Premium, Bedrijfskritiek of grootschalige en wilt ervoor zorgen dat al uw verbindingen naar de primaire replica gaan, schakelt u lezen uitschalen uit. Zie voor meer informatie over het uitschakelen van de functie [uitschalen voor lezen in-en uitschakelen](#enable-and-disable-read-scale-out).

> [!NOTE]
> De functies query Store en SQL Profiler worden niet ondersteund voor alleen-lezen replica's. 

## <a name="data-consistency"></a>Gegevensconsistentie

Een van de voor delen van replica's is dat de replica's altijd in de transactionele consistente status zijn, maar op verschillende tijdstippen kan er sprake zijn van een kleine latentie tussen verschillende replica's. Uitschaal baarheid lezen ondersteunt consistentie op sessie niveau. Als de alleen-lezen sessie opnieuw verbinding maakt nadat een verbindings fout is veroorzaakt doordat de replica niet beschikbaar is, kan deze worden omgeleid naar een replica die niet 100% up-to-date is met de replica met het kenmerk lezen-schrijven. Als een toepassing gegevens schrijft met behulp van een sessie voor lezen-schrijven en de gegevens onmiddellijk leest met behulp van een sessie voor alleen-lezen, is het evenzo mogelijk dat de meest recente updates niet direct zichtbaar zijn op de replica. De latentie wordt veroorzaakt door een redo-bewerking voor het transactielogboek die asynchroon wordt uitgevoerd.

> [!NOTE]
> Replicatie latenties in de regio zijn laag en deze situatie is zeldzaam. Zie [controle en probleem oplossing alleen-lezen replica](#monitoring-and-troubleshooting-read-only-replicas)om replicatie latentie te controleren.

## <a name="connect-to-a-read-only-replica"></a>Verbinding maken met een alleen-lezen replica

Wanneer u uitschalen voor een Data Base inschakelt, `ApplicationIntent` bepaalt de optie in de Connection String van de client of de verbinding wordt doorgestuurd naar de schrijf replica of naar een alleen-lezen replica. Met name als de `ApplicationIntent` waarde is `ReadWrite` (de standaard waarde), wordt de verbinding omgeleid naar de replica lezen-schrijven. Dit is identiek aan het gedrag wanneer `ApplicationIntent` niet is opgenomen in de Connection String. Als de `ApplicationIntent` waarde is `ReadOnly` , wordt de verbinding doorgestuurd naar een alleen-lezen replica.

De volgende connection string de client bijvoorbeeld verbindt met een alleen-lezen replica (waarbij de items in de punt haken worden vervangen door de juiste waarden voor uw omgeving en de punt haken worden neergezet):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Een van de volgende verbindings reeksen verbindt de client met een replica met het kenmerk lezen/schrijven (waarbij de items in de punt haken worden vervangen door de juiste waarden voor uw omgeving en de punt haken worden neergezet):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Controleren of een verbinding een alleen-lezen replica is

U kunt controleren of u verbonden bent met een alleen-lezen replica door de volgende query uit te voeren in de context van uw data base. Er wordt READ_ONLY geretourneerd wanneer u bent verbonden met een alleen-lezen replica.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> In Premium-en Bedrijfskritiek-service lagen is slechts één van de alleen-lezen replica's toegankelijk op een bepaald moment. Grootschalige ondersteunt meerdere alleen-lezen replica's.

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>Bewaken en problemen met alleen-lezen replica's oplossen

Wanneer er verbinding wordt gemaakt met een alleen-lezen replica, zien dynamische beheer weergaven (Dmv's) de status van de replica en kunnen er query's worden uitgevoerd voor bewakings-en probleemoplossings doeleinden. De data base-engine biedt meerdere weer gaven om een groot aantal bewakings gegevens beschikbaar te maken. 

Veelgebruikte weer gaven zijn:

| Name | Doel |
|:---|:---|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Voorziet in de metrische gegevens van het resource gebruik voor het afgelopen uur, inclusief CPU, data IO en logboek schrijf gebruik in verhouding tot service doelstelling limieten.|
|[sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| Biedt geaggregeerde wacht statistieken voor het exemplaar van de data base-engine. |
|[sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| Biedt status-en synchronisatie statistieken voor de replica. De grootte van de wachtrij opnieuw uitvoeren en het aantal opnieuw uitvoeren fungeert als indicator van de gegevens latentie op de alleen-lezen replica. |
|[sys.dm_os_performance_counters](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| Biedt prestatie meter items voor de data base-engine.|
|[sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| Biedt uitvoerings statistieken per query, zoals het aantal uitvoeringen, de gebruikte CPU-tijd, enzovoort.|
|[sys.dm_exec_query_plan ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| Biedt query abonnementen in de cache. |
|[sys.dm_exec_sql_text ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| Geeft query tekst voor een query plan in de cache.|
|[sys.dm_exec_query_profiles](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Biedt real-time query voortgang terwijl query's worden uitgevoerd.|
|[sys.dm_exec_query_plan_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Voorziet in het laatste bekende werkelijke uitvoerings plan, inclusief runtime statistieken voor een query.|
|[sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| Biedt gegevens over de opslag-IOPS, door Voer en latentie voor alle database bestanden. |

> [!NOTE]
> De `sys.resource_stats` en `sys.elastic_pool_resource_stats` dmv's in de logische hoofd database retour neren gegevens over het resource gebruik van de primaire replica.

### <a name="monitoring-read-only-replicas-with-extended-events"></a>Alleen-lezen replica's met uitgebreide gebeurtenissen bewaken

Een Extended Event-sessie kan niet worden gemaakt wanneer deze is verbonden met een alleen-lezen replica. In Azure SQL Database worden echter de definities van [uitgebreide gebeurtenis](xevent-db-diff-from-svr.md) sessies met database bereik die op de primaire replica zijn gemaakt en gewijzigd, gerepliceerd naar alleen-lezen replica's, met inbegrip van geo-replica's, en gebeurtenissen vastleggen op alleen-lezen replica's.

Een Extended Event-sessie op een alleen-lezen replica die is gebaseerd op een sessie definitie van de primaire replica kan worden gestart en onafhankelijk van de primaire replica worden gestopt. Wanneer een Extended Event-sessie wordt verwijderd voor de primaire replica, wordt deze ook verwijderd voor alle alleen-lezen replica's.

### <a name="transaction-isolation-level-on-read-only-replicas"></a>Transactie isolatie niveau voor alleen-lezen replica's

Query's die worden uitgevoerd op alleen-lezen replica's, worden altijd toegewezen aan het isolatie niveau van [snap shot](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server) -trans acties. Snap shot-isolatie maakt gebruik van rij-versie beheer om te voor komen dat de blok keringen van lezers worden geblokkeerd.

In zeldzame gevallen kan het gebeuren dat als een snap shot-isolatie transactie object meta gegevens opent die in een andere gelijktijdige trans actie zijn gewijzigd, fout [3961](/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error)wordt weer gegeven, de snap shot-isolatie transactie is mislukt in Data Base %1! omdat het object dat door de instructie wordt gebruikt, is gewijzigd door een DDL-instructie in een andere gelijktijdige trans actie sinds het begin van deze trans actie. Dit is niet toegestaan omdat versiebeheer niet is ingeschakeld voor de metagegevens. Een gelijktijdige update van meta gegevens kan leiden tot inconsistenties bij het combi natie met snap shot-isolatie.

### <a name="long-running-queries-on-read-only-replicas"></a>Langlopende query's voor alleen-lezen replica's

Query's die worden uitgevoerd op alleen-lezen replica's, moeten toegang hebben tot meta gegevens voor de objecten waarnaar wordt verwezen in de query (tabellen, indexen, statistieken, enzovoort). In zeldzame gevallen, als een meta gegevens object op de primaire replica is gewijzigd terwijl een query een vergren deling op hetzelfde object op de alleen-lezen replica bevat, kan de query het proces dat wijzigingen van de primaire replica op de alleen-lezen replica toepast, [blok keren](/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK) . Als een dergelijke query al lange tijd zou worden uitgevoerd, zou de alleen-lezen replica aanzienlijk niet synchroon zijn met de primaire replica.

Als een langlopende query op een alleen-lezen replica dit soort blok kering veroorzaakt, wordt deze automatisch beëindigd. De sessie ontvangt fout 1219, ' uw sessie is verbroken vanwege een DDL-bewerking met hoge prioriteit ', of fout 3947 ' de trans actie is afgebroken omdat de secundaire Compute niet opnieuw kan worden uitgevoerd. Voer de trans actie opnieuw uit.

> [!NOTE]
> Als fout 3961, 1219 of 3947 wordt weer gegeven bij het uitvoeren van query's op een alleen-lezen replica, voert u de query opnieuw uit.

> [!TIP]
> In Premium-en Bedrijfskritiek-service lagen, wanneer verbinding wordt gemaakt met een alleen-lezen replica, `redo_queue_size` `redo_rate` kunnen de kolommen en in de [sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) dmv worden gebruikt voor het bewaken van het gegevens synchronisatie proces, met als indica toren van gegevens latentie op de alleen-lezen replica.
> 

## <a name="enable-and-disable-read-scale-out"></a>Uitschalen voor lezen in-en uitschakelen

Uitschalen voor lezen is standaard ingeschakeld voor Premium-, Bedrijfskritiek-en grootschalige-service lagen. Uitschalen van Lees bewerkingen kan niet worden ingeschakeld in service lagen Basic, Standard of Algemeen. Uitschalen voor lezen wordt automatisch uitgeschakeld voor grootschalige-data bases die zijn geconfigureerd met nul replica's.

Met de volgende methoden kunt u het lezen van uitschalen uitschakelen voor afzonderlijke data bases en elastische pool databases in de service lagen Premium of Bedrijfskritiek.

> [!NOTE]
> Voor afzonderlijke data bases en elastische pool databases is het mogelijk om Lees scale-out uit te scha kelen voor achterwaartse compatibiliteit. Uitschalen voor lezen kan niet worden uitgeschakeld voor Bedrijfskritiek beheerde exemplaren.

### <a name="azure-portal"></a>Azure Portal

U kunt de instelling Lees scale-out beheren op de Blade Data Base **configureren** .

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. De Azure Resource Manager-module blijft oplossingen voor problemen ontvangen tot ten minste december 2020.  De argumenten voor de opdrachten in de module AZ en in de Azure Resource Manager-modules zijn in wezen identiek. Zie [Inleiding tot de nieuwe Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az)voor meer informatie over de compatibiliteit.

Voor het beheren van Read scale-out in Azure PowerShell is de Azure PowerShell release van december 2016 of hoger vereist. Zie [Azure PowerShell](/powershell/azure/install-az-ps)voor de nieuwste Power shell-versie.

U kunt het lezen van uitschalen in Azure PowerShell uitschakelen of opnieuw inschakelen door de cmdlet [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) aan te roepen en de gewenste waarde ( `Enabled` of `Disabled` ) voor de para meter door te geven `-ReadScale` .

Als u uitschalen wilt uitschakelen voor een bestaande data base (door de items in de punt haken te vervangen door de juiste waarden voor uw omgeving en de punt haken neer te zetten):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

U kunt uitschalen uitschakelen voor een nieuwe data base (door de items in de punt haken te vervangen door de juiste waarden voor uw omgeving en de punt haken neer te zetten):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Als u het lezen van uitschalen voor een bestaande data base opnieuw wilt inschakelen (Vervang de items in de punt haken door de juiste waarden voor uw omgeving en zet u de punt haken neer):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST-API

Als u een Data Base met lees uitschalen wilt maken of de instelling voor een bestaande Data Base wilt wijzigen, gebruikt u de volgende methode waarbij de `readScale` eigenschap is ingesteld op `Enabled` of `Disabled` , zoals in de volgende voorbeeld aanvraag.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Zie [data bases-maken of bijwerken](/rest/api/sql/databases/createorupdate)voor meer informatie.

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>De `tempdb` Data Base op een alleen-lezen replica gebruiken

De `tempdb` Data Base op de primaire replica wordt niet gerepliceerd naar de alleen-lezen replica's. Elke replica heeft een eigen `tempdb` Data Base die wordt gemaakt wanneer de replica wordt gemaakt. Dit zorgt ervoor dat `tempdb` kan worden bijgewerkt en kan worden gewijzigd tijdens het uitvoeren van de query. Als uw alleen-lezen werk belasting afhankelijk is van het gebruik van `tempdb` objecten, moet u deze objecten maken als onderdeel van uw query script.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Lezen van scale-out gebruiken met geo-gerepliceerde data bases

Geo-gerepliceerde secundaire data bases hebben dezelfde architectuur voor hoge Beschik baarheid als de primaire data bases. Als u verbinding maakt met de secundaire data base met geo-replicatie met lees scale-out ingeschakeld, worden uw sessies met `ApplicationIntent=ReadOnly` naar een van de Maxi maal beschik bare replica's gerouteerd op dezelfde manier als de gegevens die worden gerouteerd op de primaire beschrijvende data base. De sessies zonder `ApplicationIntent=ReadOnly` worden doorgestuurd naar de primaire replica van het secundaire geo-gerepliceerde secundair, wat ook alleen-lezen is. 

Op deze manier biedt het maken van een geo-replica twee meer alleen-lezen replica's voor een primaire Data Base voor lezen/schrijven, voor een totaal van drie alleen-lezen replica's. Elke extra geo-replica biedt een andere set met alleen-lezen replica's. Geo-replica's kunnen worden gemaakt in elke Azure-regio, inclusief de regio van de primaire data base.

> [!NOTE]
> Er is geen automatische Round-Robin of andere route ring met gelijke taak verdeling tussen de replica's van een secundaire data base met geo-replicatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [grootschalige service tier](service-tier-hyperscale.md)(Engelstalig) voor meer informatie over SQL database grootschalige-aanbieding.