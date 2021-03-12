---
title: Een Data Base uitschalen
description: De ShardMapManager-client bibliotheek voor Elastic Data Base gebruiken
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 03bf92a2d77fb262ed6506bf18c0d27006e435a7
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201195"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Data bases uitschalen met Shard-toewijzings beheer
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Als u data bases eenvoudig wilt uitschalen op Azure SQL Database, gebruikt u een Shard-toewijzings beheer. Het Shard-toewijzings beheer is een speciale data base met algemene toewijzings informatie over alle Shards (data bases) in een Shard-set. Met de meta gegevens kan een toepassing verbinding maken met de juiste data base op basis van de waarde van de **sharding-sleutel**. Daarnaast bevat elke Shard in de set Maps voor het bijhouden van de lokale Shard-gegevens (ook wel **shardlets** genoemd).

![Shard-toewijzingsbeheer](./media/elastic-scale-shard-map-management/glossary.png)

Meer informatie over hoe deze kaarten worden samengesteld, is essentieel voor Shard-toewijzings beheer. Dit wordt gedaan met behulp van de ShardMapManager-klasse ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) die is gevonden in de [Elastic database-client bibliotheek](elastic-database-client-library.md) voor het beheren van Shard-kaarten.  

## <a name="shard-maps-and-shard-mappings"></a>Shard Maps-en Shard-toewijzingen

Voor elke Shard moet u het type Shard-toewijzing selecteren dat u wilt maken. De keuze is afhankelijk van de database architectuur:

1. Eén Tenant per data base  
2. Meerdere tenants per data base (twee typen):
   1. Lijst toewijzing
   2. Toewijzing van bereik

Maak voor een model met één Tenant een Shard toewijzing **met lijst toewijzingen** . Het model met één Tenant wijst één data base per Tenant toe. Dit is een effectief model voor SaaS-ontwikkel aars, omdat het Shard-toewijzings beheer vereenvoudigt.

![Lijst toewijzing][1]

Het model met meerdere tenants wijst meerdere tenants toe aan een afzonderlijke data base (en u kunt groepen tenants distribueren over meerdere data bases). Gebruik dit model wanneer u verwacht dat elke Tenant kleine gegevens nodig heeft. Wijs in dit model een aantal tenants toe aan een Data Base met behulp van **bereik toewijzing**.

![Toewijzing van bereik][2]

U kunt ook een multi tenant-database model implementeren met een *lijst toewijzing* om meerdere tenants toe te wijzen aan een afzonderlijke data base. DB1 wordt bijvoorbeeld gebruikt voor het opslaan van informatie over Tenant-ID 1 en 5, en de DB2-gegevens worden opgeslagen voor Tenant 7 en Tenant 10.

![Meerdere tenants op één data base][3]

### <a name="supported-types-for-sharding-keys"></a>Ondersteunde typen voor sharding-sleutels

Elastisch schalen ondersteunt de volgende typen als sharding-sleutels:

| .NET | Java |
| --- | --- |
| geheel getal |geheel getal |
| long |long |
| guid |uuid |
| byte []  |byte [] |
| datum/tijd | tijdstempel |
| tijdsbestek | duur|
| datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Lijst-en bereik Shard Maps

Shard Maps kunnen worden samengesteld met behulp **van lijsten met afzonderlijke sharding-sleutel waarden**, of ze kunnen worden samengesteld met behulp **van bereiken van sharding-sleutel waarden**.

### <a name="list-shard-maps"></a>Lijst met Shard-kaarten

**Shards** bevatten **shardlets** en de toewijzing van shardlets aan Shards wordt onderhouden door een Shard-kaart. Een **lijst Shard toewijzing** is een koppeling tussen de afzonderlijke sleutel waarden die de shardlets en de data bases identificeren die fungeren als Shards.  **Lijst toewijzingen** zijn expliciete en andere sleutel waarden kunnen worden toegewezen aan dezelfde data base. Bijvoorbeeld: sleutel waarde 1 wordt toegewezen aan data base A en de sleutel waarden 3 en 6 zijn beide toegewezen aan data base B.

| Sleutel | Locatie van Shard |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Bereik Shard Maps

In een **Shard-toewijzing** wordt het sleutel bereik beschreven met een paar **[lage waarde, hoge waarde)** , waarbij de *lage waarde* de minimum sleutel in het bereik is en de *hoogste waarde* de eerste waarde hoger is dan het bereik.

Bijvoorbeeld: **[0, 100)** omvat alle gehele getallen die groter dan of gelijk zijn aan 0 en kleiner zijn dan 100. Houd er rekening mee dat meerdere bereiken naar dezelfde data base kunnen verwijzen en dat gescheiden bereiken worden ondersteund (bijvoorbeeld [100.200) en [400.600) naar Data Base C in het volgende voor beeld.)

| Sleutel | Locatie van Shard |
| --- | --- |
| [1, 50) |Database_A |
| [50.100) |Database_B |
| [100.200) |Database_C |
| [400.600) |Database_C |
| ... |... |

Elk van de hierboven weer gegeven tabellen is een conceptueel voor beeld van een **ShardMap** -object. Elke rij is een vereenvoudigd voor beeld van een afzonderlijke **PointMapping** (voor de lijst Shard kaart) of **RangeMapping** (voor het bereik Shard kaart object).

## <a name="shard-map-manager"></a>Shard-toewijzingsbeheer

In de client bibliotheek is het Shard-toewijzings beheer een verzameling van Shard-kaarten. De gegevens die worden beheerd door een **ShardMapManager** -exemplaar, worden op drie locaties bewaard:

1. **Global Sharde map (GSM)**: u geeft een Data Base op die als opslag plaats fungeert voor alle bijbehorende Shard-kaarten en-toewijzingen. Speciale tabellen en opgeslagen procedures worden automatisch gemaakt voor het beheren van de informatie. Dit is normaal gesp roken een kleine data base en licht toegankelijk en mag niet worden gebruikt voor andere behoeften van de toepassing. De tabellen bevinden zich in een speciaal schema met de naam **__ShardManagement**.
2. **Lokale Shard-toewijzing (LSM)**: elke Data Base die u opgeeft als Shard, is gewijzigd in een aantal kleine tabellen en speciale opgeslagen procedures die Shard kaart informatie bevatten en beheren die specifiek is voor die Shard. Deze informatie is redundant met de informatie in de GSM en maakt het mogelijk dat de toepassing de Shard-kaart gegevens in de cache valideert zonder dat ze een belasting op de GSM hoeven te plaatsen; de toepassing maakt gebruik van de LSM om te bepalen of een toewijzing in de cache nog geldig is. De tabellen die overeenkomen met de LSM op elke Shard bevinden zich ook in het schema **__ShardManagement**.
3. **Toepassings cache**: elke toepassings instantie die toegang heeft tot een **ShardMapManager** -object, houdt een lokale cache in het geheugen bij van de bijbehorende toewijzingen. De routerings gegevens die recent zijn opgehaald, worden opgeslagen.

## <a name="constructing-a-shardmapmanager"></a>Een ShardMapManager maken

Een **ShardMapManager** -object is gemaakt met behulp van een fabrieks patroon ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)). De methode **ShardMapManagerFactory. GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) neemt referenties (inclusief de server naam en database naam die de GSM bevat) op in de vorm van een **Connections Tring** en retourneert een exemplaar van een **ShardMapManager**.  

**Let op:** De **ShardMapManager** moet slechts eenmaal per app-domein worden geïnstantieerd binnen de initialisatie code voor een toepassing. Het maken van extra instanties van ShardMapManager in hetzelfde app-domein resulteert in meer geheugen en CPU-gebruik van de toepassing. Een **ShardMapManager** kan een wille keurig aantal Shard-kaarten bevatten. Hoewel één Shard-kaart mogelijk voldoende is voor veel toepassingen, zijn er situaties waarin verschillende sets data bases worden gebruikt voor een ander schema of voor unieke doel einden. in deze gevallen is het mogelijk dat meerdere Shard-kaarten de voor keur hebben.

In deze code probeert een toepassing een bestaande **ShardMapManager** te openen met de methode TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) . Als objecten die een Global **ShardMapManager** (GSM) vertegenwoordigen, nog niet in de data base bestaan, worden deze door de client bibliotheek gemaakt met de methode CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)).

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager;
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString,
                                        ShardMapManagerLoadPolicy.Lazy,
                                        out shardMapManager);

if (shardMapManagerExists)
{
    Console.WriteLine("Shard Map Manager already exists");
}
else
{
    // Create the Shard Map Manager.
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager");

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString,
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
}
```

Voor de .NET-versie kunt u Power shell gebruiken om een nieuw Shard-toewijzings beheer te maken. [Hier](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)vindt u een voor beeld.

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Een RangeShardMap of ListShardMap ophalen

Nadat u een Shard-toewijzings beheer hebt gemaakt, kunt u de RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) of ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.net) ophalen](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)met behulp van de TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), de TryGetListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) of de GetShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap))-methode.

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
}
```

### <a name="shard-map-administration-credentials"></a>Shard-toewijzings beheer referenties

Toepassingen die Shard-kaarten beheren en manipuleren, verschillen van degene die gebruikmaken van de Shard-kaarten om verbindingen te routeren.

Voor het beheren van Shard Maps (toevoegen of wijzigen van Shards, Shard Maps, Shard toewijzingen, enzovoort) moet u de **ShardMapManager** instantiëren met behulp **van referenties met lees-/schrijfmachtigingen voor zowel de GSM-Data Base als voor elke Data Base die fungeert als een Shard**. De referenties moeten schrijf bewerkingen toestaan voor de tabellen in zowel de GSM-als de LSM als Shard-toewijzings gegevens worden ingevoerd of gewijzigd, en voor het maken van LSM-tabellen op nieuwe Shards.  

Zie [de referenties die worden gebruikt voor toegang tot de Elastic database-client bibliotheek](elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Alleen beïnvloede meta gegevens

De methoden die worden gebruikt voor het vullen of wijzigen van de **ShardMapManager** -gegevens, wijzigen niet de gebruikers gegevens die zijn opgeslagen in de Shards zelf. Methoden zoals **CreateShard**, **DeleteShard**, **UpdateMapping**, etc. beïnvloeden bijvoorbeeld alleen de meta gegevens van Shard-kaarten. Gebruikers gegevens in de Shards worden niet verwijderd, toegevoegd of gewijzigd. In plaats daarvan zijn deze methoden ontworpen om te worden gebruikt in combi natie met afzonderlijke bewerkingen die u uitvoert voor het maken of verwijderen van werkelijke data bases of het verplaatsen van rijen van de ene Shard naar een andere om een Shard-omgeving te herverdelen.  (Het hulp programma voor **splitsen en samen voegen** dat is opgenomen in hulpprogram ma's voor Elastic data bases maakt gebruik van deze api's samen met het organiseren van werkelijke gegevens verplaatsing tussen Shards.) Zie [schalen met behulp van het Elastic database hulp programma voor splitsen en samen voegen](elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Gegevensafhankelijke routering

Het Shard-toewijzings beheer wordt gebruikt in toepassingen waarvoor database verbindingen zijn vereist om de app-specifieke gegevens bewerkingen uit te voeren. Deze verbindingen moeten worden gekoppeld aan de juiste data base. Dit wordt ook wel **gegevens afhankelijke route ring** genoemd. Voor deze toepassingen maakt u een instantie van een Shard-toewijzings beheer object vanuit de fabriek met referenties die alleen-lezen toegang hebben voor de GSM-data base. Afzonderlijke aanvragen voor latere verbindingen geven referenties op die nodig zijn om verbinding te maken met de juiste Shard-data base.

Houd er rekening mee dat deze toepassingen (met **ShardMapManager** geopend met alleen-lezen referenties) geen wijzigingen kunnen aanbrengen in de toewijzingen of toewijzingen. Voor die behoeften kunt u administratieve specifieke toepassingen of Power shell-scripts maken die referenties met meer privileges bieden zoals eerder is besproken. Zie [de referenties die worden gebruikt voor toegang tot de Elastic database-client bibliotheek](elastic-scale-manage-credentials.md).

Zie [gegevens afhankelijke route ring](elastic-scale-data-dependent-routing.md)voor meer informatie.

## <a name="modifying-a-shard-map"></a>Een Shard-kaart wijzigen

Een Shard-kaart kan op verschillende manieren worden gewijzigd. Met de volgende methoden worden de meta gegevens van de Shards en hun toewijzingen gewijzigd, maar de gegevens worden niet fysiek gewijzigd in de Shards, noch worden de daad werkelijke data bases gemaakt of verwijderd.  Sommige van de bewerkingen op de Shard-toewijzing die hieronder worden beschreven, moeten mogelijk worden gecoördineerd met beheer acties waarmee gegevens fysiek worden verplaatst of waarmee data bases worden toegevoegd en verwijderd die als Shards fungeren.

Deze methoden werken samen als de bouw stenen die beschikbaar zijn voor het wijzigen van de algemene distributie van gegevens in uw Shard-database omgeving.  

* Shards toevoegen of verwijderen: gebruik **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) en **DeleteShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), .net) van de shardmap-klasse [](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap)(Java [,](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap) [.net).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)
  
    De server en de data base die het doel-Shard vertegenwoordigen, moeten al bestaan voor het uitvoeren van deze bewerkingen. Deze methoden hebben geen invloed op de data bases zelf, alleen op meta gegevens in de Shard-kaart.
* Punten of bereiken maken of verwijderen die zijn toegewezen aan de Shards: gebruik **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.net](/previous-versions/azure/dn841993(v=azure.100))), **DeleteMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) van de RangeShardMapping-klasse ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) en **CreatePointMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) van de ListShardMap-klasse ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)).
  
    Veel verschillende punten of bereiken kunnen worden toegewezen aan dezelfde Shard. Deze methoden zijn alleen van invloed op meta gegevens: ze hebben geen invloed op gegevens die mogelijk al aanwezig zijn in Shards. Als er gegevens moeten worden verwijderd uit de data base zodat deze consistent zijn met **DeleteMapping** -bewerkingen, voert u deze bewerkingen afzonderlijk uit, maar in combi natie met deze methoden.  
* Om bestaande bereiken te splitsen in twee of aangrenzende bereiken samen voegen tot één: gebruik **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) en **MergeMappings** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Houd er rekening mee dat splitsen en samen voegen **de Shard waaraan sleutel waarden worden toegewezen, niet wijzigen**. Een split breekt een bestaand bereik op in twee delen, maar blijft hetzelfde als toegewezen aan dezelfde Shard. Een samen voeging werkt op twee aangrenzende bereiken die al aan dezelfde Shard zijn toegewezen, en voegt deze toe aan één bereik.  De verplaatsing van punten of bereiken tussen Shards moet worden gecoördineerd met behulp van **UpdateMapping** in combi natie met de daad werkelijke gegevens verplaatsing.  U kunt de service voor **splitsen en samen voegen** gebruiken die deel uitmaakt van de hulpprogram ma's voor elastische data bases om Shard toe te voegen aan gegevens verplaatsing, wanneer de verplaatsing nodig is.
* Afzonderlijke punten of bereiken opnieuw toewijzen (of verplaatsen) naar verschillende Shards: gebruik **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Omdat gegevens mogelijk moeten worden verplaatst van de ene Shard naar een andere zodat deze consistent zijn met **UpdateMapping** -bewerkingen, moet u die verplaatsing afzonderlijk uitvoeren, maar in combi natie met deze methoden.

* Toewijzingen online en offline halen: gebruik **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) en **MarkMappingOnline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) om de online status van een toewijzing te bepalen.
  
    Bepaalde bewerkingen op Shard-toewijzingen zijn alleen toegestaan wanneer een toewijzing de status offline heeft, inclusief **UpdateMapping** en **DeleteMapping**. Wanneer een toewijzing offline is, retourneert een gegevens afhankelijke aanvraag op basis van een sleutel die is opgenomen in die toewijzing een fout. Wanneer een bereik voor het eerst offline wordt genomen, worden alle verbindingen met de betrokken Shard automatisch afgebroken om inconsistente of onvolledige resultaten te voor komen voor query's die zijn gericht op bereiken die worden gewijzigd.

Toewijzingen zijn onveranderbare objecten in .NET.  Met alle methoden hierboven die wijzigings toewijzingen worden de verwijzingen in uw code ook ongeldig gemaakt. Om het gemakkelijker te maken om reeksen bewerkingen uit te voeren die de status van een toewijzing wijzigen, retour neren alle methoden die een toewijzing wijzigen een nieuwe toewijzings verwijzing, waardoor bewerkingen kunnen worden gekoppeld. Als u bijvoorbeeld een bestaande toewijzing in shardmap SM wilt verwijderen die de sleutel 25 bevat, kunt u het volgende uitvoeren:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Een Shard toevoegen

Toepassingen moeten vaak nieuwe Shards toevoegen voor het afhandelen van gegevens die worden verwacht van nieuwe sleutels of belang rijke bereiken, voor een Shard-kaart die al bestaat. Het is bijvoorbeeld mogelijk dat een toepassings Shard op basis van de Tenant-ID een nieuwe Shard moet inrichten voor een nieuwe Tenant, of dat de gegevens Shard maandelijks een nieuwe Shard moeten inrichten voor het begin van elke nieuwe maand.

Als het nieuwe bereik met sleutel waarden niet al deel uitmaakt van een bestaande toewijzing en er geen gegevens verplaatsing nodig is, is het eenvoudig om het nieuwe Shard toe te voegen en de nieuwe sleutel of het bereik aan die Shard te koppelen. Zie [een nieuwe Shard toevoegen](elastic-scale-add-a-shard.md)voor meer informatie over het toevoegen van nieuwe Shards.

Voor scenario's waarvoor gegevens verplaatsing vereist is, is het hulp programma voor splitsen en samen voegen echter nodig om de gegevens verplaatsing tussen Shards in combi natie met de benodigde Shard-toewijzings updates te organiseren. Zie [overzicht van splitsen en samen](elastic-scale-overview-split-and-merge.md) voegen voor meer informatie over het gebruik van het hulp programma Split-Merge

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-shard-map-management/listmapping.png
[2]: ./media/elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/elastic-scale-shard-map-management/multipleonsingledb.png
