---
title: De client bibliotheek voor Elastic Data Base gebruiken met dapper
description: De client bibliotheek voor Elastic Data Base gebruiken met dapper.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: d660e62ea293bd3cc377b95612cfaf41a9f1cd6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793361"
---
# <a name="using-the-elastic-database-client-library-with-dapper"></a>De client bibliotheek voor Elastic Data Base gebruiken met dapper
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dit document is bedoeld voor ontwikkel aars die gebruikmaken van dapper om toepassingen te bouwen, maar u wilt ook [Elastic data base-hulp middelen gebruiken](elastic-scale-introduction.md) om toepassingen te maken die sharding implementeren om hun gegevenslaag te schalen.  Dit document illustreert de wijzigingen in dapper toepassingen die nodig zijn voor de integratie met Elastic data base-hulpprogram ma's. Onze nadruk ligt op het samen stellen van het Elastic data base Shard management en gegevens afhankelijke route ring met dapper. 

**Voorbeeld code**: [hulpprogram ma's voor Elastic data base voor Azure SQL database-dapper-integratie](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).

Het integreren van **dapper** en **DapperExtensions** met de Elastic data base-client bibliotheek voor Azure SQL database is eenvoudig. Uw toepassingen kunnen gegevens afhankelijke route ring gebruiken door het maken en openen van nieuwe [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) -objecten te wijzigen om de [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) -aanroep van de [client bibliotheek](/previous-versions/azure/dn765902(v=azure.100))te gebruiken. Hierdoor worden wijzigingen in uw toepassing beperkt tot alleen nieuwe verbindingen gemaakt en geopend. 

## <a name="dapper-overview"></a>Overzicht van dapper
**Dapper** is een object relationele Mapper. .NET-objecten van uw toepassing worden toegewezen aan een relationele data base (en omgekeerd). In het eerste deel van de voorbeeld code ziet u hoe u de client bibliotheek voor Elastic Data Base kunt integreren met dapper-toepassingen. Het tweede deel van de voorbeeld code illustreert hoe u kunt integreren wanneer zowel dapper als DapperExtensions worden gebruikt.  

De functionaliteit van de Mapper in dapper biedt uitbreidings methoden voor database verbindingen waarmee T-SQL-instructies worden verzonden voor uitvoering of het uitvoeren van query's in de data base. Dapper maakt het eenvoudig om te koppelen tussen uw .NET-objecten en de para meters van SQL-instructies voor het **uitvoeren** van aanroepen, of om de resultaten van uw SQL-query's te gebruiken in .net-objecten met behulp van **query** aanroepen vanuit dapper. 

Wanneer u DapperExtensions gebruikt, hoeft u de SQL-instructies niet meer op te geven. Met uitbreidings methoden zoals **GetList** of **Insert** over de database verbinding maakt u de SQL-instructies achter de schermen.

Een ander voor deel van dapper en ook DapperExtensions is dat de toepassing het maken van de database verbinding beheert. Dit helpt bij het communiceren met de client bibliotheek voor Elastic data bases die is gekoppeld aan data base-verbindingen op basis van de toewijzing van shardlets aan data bases.

Zie [dapper dot net](https://www.nuget.org/packages/Dapper/)om de dapper-assembly's op te halen. Zie [DapperExtensions](https://www.nuget.org/packages/DapperExtensions)voor de dapper-extensies.

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Een beknopt overzicht van de client bibliotheek voor Elastic data base
Met de client bibliotheek voor Elastic data base definieert u partities van uw toepassings gegevens met de naam *shardlets*, wijst u deze toe aan data bases en identificeert u deze met *sharding-sleutels*. U kunt zoveel data bases als u nodig hebt en uw shardlets distribueren over deze data bases. De toewijzing van sharding-sleutel waarden aan de data bases wordt opgeslagen door een Shard-kaart die wordt weer gegeven door de Api's van de bibliotheek. Deze mogelijkheid heet **Shard-toewijzings beheer**. De shard-toewijzing dient ook als broker voor databaseverbindingen voor aanvragen die een sharding-sleutel bevatten. Deze mogelijkheid wordt aangeduid als **gegevens afhankelijke route ring**.

![Shard Maps en gegevens afhankelijke route ring][1]

Shard-toewijzings beheer beveiligt gebruikers tegen inconsistente weer gaven in shardlet-gegevens die kunnen optreden wanneer er gelijktijdige shardlet-beheer bewerkingen op de data bases plaatsvinden. Hiertoe wijst de Shard Maps Broker de database verbindingen toe voor een toepassing die is gebouwd met de bibliotheek. Wanneer Shard-beheer bewerkingen van invloed kunnen zijn op de shardlet, kan de Shard-toewijzings functionaliteit automatisch een database verbinding afbreken. 

In plaats van de traditionele manier om verbindingen voor dapper te maken, moet u de [methode OpenConnectionForKey](/previous-versions/azure/dn824099(v=azure.100))gebruiken. Dit zorgt ervoor dat alle validaties worden uitgevoerd en de verbindingen correct worden beheerd wanneer er gegevens tussen Shards worden verplaatst.

### <a name="requirements-for-dapper-integration"></a>Vereisten voor dapper-integratie
Wanneer u werkt met de client bibliotheek voor Elastic data base en de dapper-Api's, wilt u de volgende eigenschappen behouden:

* **Uitschalen**: we willen data bases toevoegen aan of verwijderen uit de gegevenslaag van de Shard-toepassing, indien nodig voor de capaciteits vereisten van de toepassing. 
* **Consistentie**: omdat de toepassing is uitgeschaald met sharding, moet u gegevens afhankelijke route ring uitvoeren. We willen hiervoor de gegevensafhankelijke routerings mogelijkheden van de bibliotheek gebruiken. In het bijzonder wilt u de verificatie-en consistentie garanties behouden die worden geboden door verbindingen die zijn gebrokerd via de Shard-toewijzings beheer om beschadiging of onjuiste query resultaten te voor komen. Dit zorgt ervoor dat verbindingen met een gegeven shardlet worden geweigerd of worden gestopt als (bijvoorbeeld) de shardlet momenteel is verplaatst naar een andere Shard met behulp van Split/Merge-Api's.
* **Object toewijzing**: we willen het gemak behouden van de toewijzingen die door dapper worden verschaft om te vertalen tussen klassen in de toepassing en de onderliggende database structuren. 

De volgende sectie bevat richt lijnen voor deze vereisten voor toepassingen die zijn gebaseerd op **dapper** en **DapperExtensions**.

## <a name="technical-guidance"></a>Technische richt lijnen
### <a name="data-dependent-routing-with-dapper"></a>Gegevens afhankelijke route ring met dapper
Met dapper is de toepassing meestal verantwoordelijk voor het maken en openen van de verbindingen met de onderliggende data base. Op basis van een type T door de toepassing retourneert dapper query resultaten als .NET-verzamelingen van het type T. dapper voert de toewijzing uit van de T-SQL-resultaat rijen aan de objecten van het type T. Dapper wijst .NET-objecten ook toe aan SQL-waarden of para meters voor de DML-instructies (Data Manipulation Language). Dapper biedt deze functionaliteit via uitbreidings methoden in het gewone [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) -object van de ADO .NET SQL-Client bibliotheken. De SQL-verbinding die wordt geretourneerd door de Elastic Scale-Api's voor DDR, zijn ook normale [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) -objecten. Hierdoor kunnen we dapper-extensies rechtstreeks gebruiken voor het type dat wordt geretourneerd door de DDR-API van de client bibliotheek, omdat het ook een eenvoudige SQL-client verbinding is.

Deze opmerkingen maken het eenvoudig om verbindingen te gebruiken die worden brokert door de client bibliotheek voor Elastic Data Base voor dapper.

Dit code voorbeeld (uit het begeleide voor beeld) illustreert de aanpak waarbij de sharding-sleutel door de toepassing wordt geleverd aan de bibliotheek om de verbinding met de juiste Shard te Broker.   

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1,
                     connectionString: connStrBldr.ConnectionString,
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }
```

De aanroep van de [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) -API vervangt het standaard maken en openen van een SQL-client verbinding. De [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) -aanroep heeft de argumenten die vereist zijn voor gegevens afhankelijke route ring: 

* De Shard-kaart voor toegang tot de gegevens afhankelijke routerings interfaces
* De sharding-sleutel voor het identificeren van de shardlet
* De referenties (gebruikers naam en wacht woord) om verbinding te maken met de Shard

Het Shard map-object maakt een verbinding met de Shard die de shardlet voor de opgegeven sharding sleutel bevat. De client-Api's voor Elastic data base coderen ook de verbinding voor het implementeren van de consistentie garanties. Omdat het aanroepen van [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) een regulier SQL Client-verbindings object retourneert, wordt de volgende aanroep van de methode **Execute** extension van dapper gevolgd door de standaard dapper Practice.

Query's werken op dezelfde manier als u de verbinding voor het eerst opent met [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) van de client-API. Vervolgens gebruikt u de reguliere dapper-extensie methoden om de resultaten van uw SQL-query toe te wijzen aan .NET-objecten:

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate ))
    {
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT *
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }
```

Houd er rekening mee dat met **behulp** van blok keren met het DDR-verbindings bereik alle database bewerkingen binnen het blok worden uitgevoerd op het ene Shard waar tenantId1 wordt bewaard. De query retourneert alleen blogs die zijn opgeslagen op de huidige Shard, maar niet voor de query's die zijn opgeslagen op een andere Shards. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Gegevens afhankelijke route ring met dapper en DapperExtensions
Dapper wordt geleverd met een ecosysteem van extra uitbrei dingen, waardoor de data base meer gemak en abstractie kan bieden bij het ontwikkelen van database toepassingen. DapperExtensions is een voor beeld. 

Het gebruik van DapperExtensions in uw toepassing heeft geen invloed op hoe database verbindingen worden gemaakt en beheerd. Het is nog steeds de verantwoordelijkheid van de toepassing om verbindingen te openen, en regel matige SQL-Client verbindings objecten worden verwacht door de extensie methoden. We kunnen afhankelijk zijn van de [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) zoals hierboven wordt beschreven. Zoals in de volgende voorbeeld code wordt weer gegeven, is de enige wijziging die u niet meer hoeft te schrijven voor de T-SQL-instructies:

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }
```

En dit is het code voorbeeld voor de query: 

```csharp
    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2,
                    connectionString: connStrBldr.ConnectionString,
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }
```

### <a name="handling-transient-faults"></a>Tijdelijke fouten verwerken
Het micro soft-programma voor patronen & practices heeft het [toepassings blok voor de tijdelijke fout afhandeling](/previous-versions/msp-n-p/hh680934(v=pandp.50)) gepubliceerd om toepassings ontwikkelaars te helpen bij het afhandelen van veelvoorkomende problemen met de tijdelijke situatie tijdens het uitvoeren in de Cloud. Zie [Perseverance, geheim van alle Triumphs: het toepassings blok voor de tijdelijke fout afhandeling gebruiken](/previous-versions/msp-n-p/dn440719(v=pandp.60))voor meer informatie.

Het code voorbeeld is afhankelijk van de tijdelijke fout bibliotheek om te beveiligen tegen tijdelijke fouten. 

```csharp
    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn =
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });
```

**SqlDatabaseUtils.SqlRetryPolicy** in de bovenstaande code is gedefinieerd als een **SqlDatabaseTransientErrorDetectionStrategy** met maximaal 10 pogingen en een wachttijd van 5 seconden tussen de pogingen. Als u trans acties gebruikt, moet u ervoor zorgen dat uw bereik voor opnieuw proberen teruggaat naar het begin van de trans actie in het geval van een tijdelijke fout.

## <a name="limitations"></a>Beperkingen
De in dit document beschreven methoden brengen een aantal beperkingen met zich mee:

* De voorbeeld code voor dit document laat zien hoe u schema beheert over Shards.
* Op basis van een aanvraag, wordt ervan uitgegaan dat alle database verwerking is opgenomen in één Shard die wordt geïdentificeerd door de sharding-sleutel die door de aanvraag wordt verstrekt. Deze veronderstelling houdt echter niet altijd vast, bijvoorbeeld wanneer het niet mogelijk is om een sharding-sleutel beschikbaar te maken. Om dit op te lossen, bevat de client bibliotheek voor Elastic data base de [MultiShardQuery-klasse](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardexception). De klasse implementeert een verbindings abstractie voor het uitvoeren van query's op verschillende Shards. Het gebruik van MultiShardQuery in combi natie met dapper valt buiten het bereik van dit document.

## <a name="conclusion"></a>Conclusie
Toepassingen die gebruikmaken van dapper en DapperExtensions kunnen eenvoudig profiteren van de hulpprogram ma's voor elastische data bases voor Azure SQL Database. Via de stappen die in dit document worden beschreven, kunnen deze toepassingen de mogelijkheid van het hulp programma voor gegevens afhankelijke route ring gebruiken door het maken en openen van nieuwe [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) -objecten te wijzigen om de [OpenConnectionForKey](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) -aanroep van de client bibliotheek voor Elastic data base te gebruiken. Hierdoor worden de toepassings wijzigingen beperkt die nodig zijn voor de locaties waar nieuwe verbindingen worden gemaakt en geopend. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-working-with-dapper/dapperimage1.png