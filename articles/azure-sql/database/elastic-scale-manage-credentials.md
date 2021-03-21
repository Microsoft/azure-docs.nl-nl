---
title: Referenties beheren in de client bibliotheek voor Elastic data base
description: Het juiste niveau van Referenties instellen, beheerder op alleen-lezen, voor Elastic data base-apps
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 5b91986d4f94861b87e413c9ff781107c3ed04a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92786595"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Referenties die worden gebruikt voor toegang tot de Elastic Database-client bibliotheek
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In de [Elastic database-client bibliotheek](elastic-database-client-library.md) worden drie verschillende soorten referenties gebruikt om toegang te krijgen tot de [Shard-kaart Manager](elastic-scale-shard-map-management.md). Gebruik, afhankelijk van de behoefte, de referentie met het laagste toegangs niveau dat mogelijk is.

* **Beheer referenties**: voor het maken of bewerken van een Shard-toewijzings beheer. (Zie de [woorden lijst](elastic-scale-glossary.md).)
* **Toegangs referenties**: voor toegang tot een bestaand Shard-toewijzings beheer voor het verkrijgen van informatie over Shards.
* **Verbindings referenties**: om verbinding te maken met Shards.

Zie ook [data bases en aanmeldingen beheren in Azure SQL database](logins-create-manage.md).

## <a name="about-management-credentials"></a>Over beheer referenties

Beheer referenties worden gebruikt voor het maken van een **ShardMapManager** -object ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) voor toepassingen die Shard-kaarten bewerken. (Zie bijvoorbeeld [een Shard toevoegen met Elastic database-hulpprogram ma's](elastic-scale-add-a-shard.md) en [gegevens afhankelijke route ring](elastic-scale-data-dependent-routing.md)). De gebruiker van de elastische Scale-client bibliotheek maakt de SQL-gebruikers en SQL-aanmeldingen en zorgt ervoor dat alle machtigingen voor lezen/schrijven worden verleend aan de globale Shard-toewijzings database en alle Shard-data bases. Deze referenties worden gebruikt om de globale Shard-toewijzing bij te houden en de lokale Shard wijst toe wanneer er wijzigingen in de Shard-toewijzing worden uitgevoerd. Gebruik bijvoorbeeld de beheer referenties om het Shard map Manager-object te maken (met behulp van **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

De variabele **smmAdminConnectionString** is een Connection String die de beheer referenties bevat. De gebruikers-ID en het wacht woord bieden lees-/schrijftoegang tot zowel de Shard-kaart database als afzonderlijke Shards. De beheer connection string bevat ook de server naam en database naam om de data base van de globale Shard-toewijzing te identificeren. Hier volgt een typische connection string voor dit doel:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Gebruik in plaats daarvan geen waarden in de vorm ' username@server ', maar gebruik hiervoor alleen de waarde ' username '.  Dit komt doordat referenties moeten samen werken aan zowel de Shard-map Manager-Data Base als de individuele Shards, die zich op verschillende servers kan bevinden.

## <a name="access-credentials"></a>Toegangs referenties

Bij het maken van een Shard-toewijzings beheer in een toepassing die geen Shard-kaarten beheert, gebruikt u referenties met alleen-lezen machtigingen voor de globale Shard-toewijzing. De gegevens die zijn opgehaald uit de globale Shard-toewijzing onder deze referenties, worden gebruikt voor [gegevens afhankelijke route ring](elastic-scale-data-dependent-routing.md) en om de Shard-toewijzings cache op de client in te vullen. De referenties worden verzorgd via hetzelfde aanroep patroon voor **GetSqlShardMapManager**:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Let op het gebruik van de **smmReadOnlyConnectionString** voor het gebruik van verschillende referenties voor deze toegang voor gebruikers die geen **beheerder zijn** : deze referenties mogen geen schrijf machtigingen bieden voor de globale Shard-toewijzing.

## <a name="connection-credentials"></a>Verbindingsreferenties

Er zijn aanvullende referenties nodig bij het gebruik van de methode **OpenConnectionForKey**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) om toegang te krijgen tot een Shard die is gekoppeld aan een sharding-sleutel. Deze referenties moeten machtigingen geven voor alleen-lezen toegang tot de lokale Shard-toewijzings tabellen die zich op de Shard bevinden. Dit is nodig om verbindings validatie uit te voeren voor gegevens afhankelijke route ring op het Shard. Met dit code fragment kunt u gegevens toegang krijgen in de context van gegevens afhankelijke route ring:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

In dit voor beeld bevat **smmUserConnectionString** de Connection String voor de gebruikers referenties. Voor Azure SQL Database is hier een typisch connection string voor gebruikers referenties:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Net als bij de beheerders referenties gebruikt u geen waarden in de vorm van username@server . Gebruik in plaats daarvan "username".  Houd er ook rekening mee dat de connection string geen server naam en database naam bevat. Dat komt doordat de **OpenConnectionForKey** -aanroep automatisch de verbinding naar de juiste Shard doorstuurt op basis van de sleutel. Daarom zijn de naam van de data base en de server naam niet gegeven.

## <a name="see-also"></a>Zie ook

[Databases en aanmeldingen beheren in Azure SQL Database](logins-create-manage.md)

[Uw SQL Database beveiligen](security-overview.md)

[taak voor Elastic Database](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]