---
title: Bestaande data bases migreren om uit te schalen
description: Shard-data bases converteren om Elastic Database-hulpprogram ma's te gebruiken door een Shard-toewijzings beheer te maken
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: c6ad8b4c80f4b9c2fdb3c1a14209dcf0febc89e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92787139"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Bestaande data bases migreren om uit te schalen
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Beheer eenvoudig uw bestaande uitgeschaalde Shard-data bases met behulp van hulpprogram ma's (zoals de [Elastic database-client bibliotheek](elastic-database-client-library.md)). Converteer eerst een bestaande set data bases om de [Shard-kaart Manager](elastic-scale-shard-map-management.md)te gebruiken.

## <a name="overview"></a>Overzicht

Een bestaande Shard-data base migreren:

1. Bereid de [Shard map manager-data base](elastic-scale-shard-map-management.md)voor.
2. Maak de Shard-kaart.
3. Bereid de afzonderlijke Shards voor.  
4. Voeg toewijzingen toe aan de Shard-kaart.

Deze technieken kunnen worden geïmplementeerd met behulp van de [.NET Framework-client bibliotheek](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)of de Power shell-scripts die zijn gevonden op [Azure SQL database Elastic database scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). In de voor beelden worden de Power shell-scripts gebruikt.

Zie [Shard-toewijzings beheer](elastic-scale-shard-map-management.md)voor meer informatie over de ShardMapManager. Zie [Elastic database-functies Overview](elastic-scale-introduction.md)(Engelstalig) voor een overzicht van de Elastic database-hulpprogram ma's.

## <a name="prepare-the-shard-map-manager-database"></a>De Shard map manager-data base voorbereiden

Het Shard-toewijzings beheer is een speciale data base die de gegevens bevat voor het beheren van uitgeschaalde data bases. U kunt een bestaande Data Base gebruiken of een nieuwe data base maken. Een Data Base die fungeert als Shard-toewijzings beheer, mag niet dezelfde data base zijn als een Shard. Met het Power shell-script wordt de data base niet voor u gemaakt.

## <a name="step-1-create-a-shard-map-manager"></a>Stap 1: een Shard-toewijzings beheer maken

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>Het Shard-toewijzings beheer ophalen

Nadat u hebt gemaakt, kunt u het Shard-toewijzings beheer ophalen met deze cmdlet. Deze stap is nodig elke keer dat u het ShardMapManager-object moet gebruiken.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>Stap 2: de Shard-kaart maken

Selecteer het type Shard-toewijzing dat u wilt maken. De keuze is afhankelijk van de database architectuur:

1. Eén Tenant per data base (Zie de [verklarende woorden lijst](elastic-scale-glossary.md)) voor meer informatie.
2. Meerdere tenants per data base (twee typen):
   1. Lijst toewijzing
   2. Toewijzing van bereik

Maak voor een model met één Tenant een **lijst** Shard toewijzing. Het model met één Tenant wijst één data base per Tenant toe. Dit is een effectief model voor SaaS-ontwikkel aars waarmee het beheer wordt vereenvoudigd.

![Lijst toewijzing][1]

Het model met meerdere tenants wijst meerdere tenants toe aan een afzonderlijke data base (en u kunt groepen tenants distribueren over meerdere data bases). Gebruik dit model wanneer u verwacht dat elke Tenant kleine gegevens nodig heeft. Wijs in dit model een aantal tenants toe aan een Data Base met behulp van **bereik toewijzing**.

![Toewijzing van bereik][2]

U kunt ook een multi tenant-database model implementeren met een *lijst toewijzing* om meerdere tenants toe te wijzen aan een afzonderlijke data base. DB1 wordt bijvoorbeeld gebruikt voor het opslaan van informatie over Tenant-ID 1 en 5, en de DB2-gegevens worden opgeslagen voor Tenant 7 en Tenant 10.

![Meerdere tenants op één data base][3]

**Kies een van de volgende opties op basis van uw keuze:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Optie 1: een Shard-toewijzing maken voor een lijst toewijzing

Maak een Shard-kaart met het ShardMapManager-object.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Optie 2: een Shard-toewijzing maken voor een toewijzing van een bereik

Als u dit toewijzings patroon wilt gebruiken, moeten de Tenant-ID-waarden doorlopende bereiken zijn. het is acceptabel om tussen ruimte in de bereiken te hebben door het bereik bij het maken van de data bases over te slaan.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>Optie 3: toewijzingen weer geven voor een afzonderlijke data base

Voor het instellen van dit patroon moet u ook een lijst overzicht maken, zoals wordt weer gegeven in stap 2, optie 1.

## <a name="step-3-prepare-individual-shards"></a>Stap 3: een afzonderlijke Shards voorbereiden

Voeg elke Shard (data base) toe aan het Shard-toewijzings beheer. Hierdoor worden de afzonderlijke data bases voor het opslaan van toewijzings gegevens voor bereid. Voer deze methode uit op elke Shard.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>Stap 4: toewijzingen toevoegen

Het toevoegen van toewijzingen is afhankelijk van het type Shard-toewijzing dat u hebt gemaakt. Als u een lijst toewijzing hebt gemaakt, voegt u lijst toewijzingen toe. Als u een bereik toewijzing hebt gemaakt, voegt u bereik toewijzingen toe.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Optie 1: de gegevens voor een lijst toewijzing toewijzen

De gegevens toewijzen door een lijst toewijzing voor elke Tenant toe te voegen.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Optie 2: de gegevens voor een toewijzing van een bereik toewijzen

Voeg de bereik toewijzingen toe voor alle Tenant-ID-bereik-database koppelingen:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Stap 4, optie 3: de gegevens voor meerdere tenants in een afzonderlijke data base toewijzen

Voer voor elke Tenant de Add-ListMapping uit (optie 1).

## <a name="checking-the-mappings"></a>De toewijzingen controleren

Informatie over de bestaande Shards en de bijbehorende toewijzingen kunnen worden opgevraagd met behulp van de volgende opdrachten:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Samenvatting

Nadat u de installatie hebt voltooid, kunt u beginnen met het gebruik van de Elastic Database-client bibliotheek. U kunt ook [gegevens afhankelijke route ring](elastic-scale-data-dependent-routing.md) en [query's met meerdere Shard](elastic-scale-multishard-querying.md)gebruiken.

## <a name="next-steps"></a>Volgende stappen

Down load de Power shell-scripts van [Azure SQL Database-Elastic data base tools-scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

De hulpprogram ma's bevinden zich ook in GitHub: [Azure/Elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Gebruik het hulp programma voor splitsen en samen voegen om gegevens van of naar een model met meerdere tenants te verplaatsen naar één Tenant model. Zie het [hulp programma splitsing samen voegen](elastic-scale-get-started.md).

## <a name="additional-resources"></a>Aanvullende bronnen

Zie voor informatie over algemene gegevensarchitectuurpatronen van multitenant software as a service (SaaS)-databasetoepassingen, [Ontwerppatronen voor multitenant SaaS-toepassingen met Azure SQL Database](saas-tenancy-app-design-patterns.md).

## <a name="questions-and-feature-requests"></a>Vragen en functie aanvragen

Gebruik voor vragen de [pagina micro soft Q&een vraag voor SQL database](/answers/topics/azure-sql-database.html) en voeg deze voor functie aanvragen toe aan het [forum SQL database feedback](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/elastic-convert-to-use-elastic-tools/multipleonsingledb.png