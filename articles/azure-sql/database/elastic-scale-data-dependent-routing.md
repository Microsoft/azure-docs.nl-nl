---
title: Gegevensafhankelijke routering
description: De ShardMapManager-klasse gebruiken in .NET-Apps voor gegevens afhankelijke route ring, een functie van Shard-data bases in Azure SQL Database
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
ms.openlocfilehash: 60e8b4b21a9e62279cd0eccfabbbed680183e2a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92787020"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-an-appropriate-database"></a>Gegevens afhankelijke route ring gebruiken om een query naar een geschikte data base te routeren
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

**Gegevens afhankelijke route ring** is de mogelijkheid om de gegevens in een query te gebruiken om de aanvraag naar een geschikte data base te routeren. Gegevens afhankelijke route ring is een fundamenteel patroon bij het werken met Shard-data bases. De aanvraag context kan ook worden gebruikt voor het routeren van de aanvraag, met name als de sharding-sleutel geen deel uitmaakt van de query. Elke specifieke query of trans actie in een toepassing die gebruikmaakt van gegevens afhankelijke route ring is beperkt tot het openen van één data base per aanvraag. Voor de Azure SQL Database elastische hulpprogram ma's wordt deze route ring uitgevoerd met de klasse **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)).

De toepassing hoeft geen verschillende verbindings reeksen of DB-locaties te volgen die zijn gekoppeld aan verschillende gegevens segmenten in de Shard omgeving. In plaats daarvan opent [Shard-toewijzings beheer](elastic-scale-shard-map-management.md) verbindingen met de juiste data bases wanneer dit nodig is, op basis van de gegevens in de Shard-toewijzing en de waarde van de sharding-sleutel die het doel is van de aanvraag van de toepassing. De sleutel is doorgaans de *Customer_ID*, *tenant_id*, *date_key* of een andere specifieke id die een fundamentele para meter van de database aanvraag is.

Zie [SQL Server uitschalen met Data-Dependent route ring](/previous-versions/sql/sql-server-2005/administrator/cc966448(v=technet.10))voor meer informatie.

## <a name="download-the-client-library"></a>De client bibliotheek downloaden

Downloaden:

* De Java-versie van de-bibliotheek, Zie [maven Central repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools)(Engelstalig).
* Zie [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)voor de .net-versie van de bibliotheek.

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Een ShardMapManager gebruiken in een gegevens afhankelijke routerings toepassing

Toepassingen moeten de **ShardMapManager** tijdens de initialisatie instantiëren met behulp van de Factory Call **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). In dit voor beeld worden zowel een **ShardMapManager** als een specifieke **ShardMap** die het bevat, geïnitialiseerd. In dit voor beeld worden de methoden GetSqlShardMapManager en GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.net](/previous-versions/azure/dn824173(v=azure.100))) weer gegeven.

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>De laagste bevoegdheids referenties gebruiken voor het ophalen van de Shard-toewijzing

Als een toepassing de Shard-kaart zelf niet bewerkt, moeten de referenties die in de fabrieks methode worden gebruikt, alleen-lezen-machtigingen hebben voor de data base van de **globale Shard-toewijzing** . Deze referenties verschillen meestal van referenties die worden gebruikt voor het openen van verbindingen met het Shard-toewijzings beheer. Zie ook [de referenties die worden gebruikt voor toegang tot de Elastic database-client bibliotheek](elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>De methode OpenConnectionForKey aanroepen

De **methode ShardMap. OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) retourneert een verbinding die gereed is voor het uitgeven van opdrachten aan de juiste data base op basis van de waarde van de **sleutel** parameter. Shard-gegevens worden in de cache opgeslagen door de **ShardMapManager**, dus voor deze aanvragen is het niet mogelijk om een database zoekactie te maken op basis van de **globale Shard-toewijzings** database.

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* De **sleutel** parameter wordt gebruikt als opzoek sleutel in de Shard-toewijzing om de juiste Data Base voor de aanvraag te bepalen.
* De **Connections Tring** wordt gebruikt om alleen de gebruikers referenties voor de gewenste verbinding door te geven. Er is geen database naam of server naam in deze *Connections Tring* opgenomen, aangezien de methode de data base en de server bepaalt met behulp van de **ShardMap**.
* De **connectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) moet worden ingesteld op **connectionOptions. validate** als een omgeving waar Shard Maps kan veranderen en rijen kunnen worden verplaatst naar andere data bases als gevolg van een split-of samenvoeg bewerking. Deze validatie omvat een korte query voor de lokale Shard-toewijzing in de doel database (niet op de globale Shard-toewijzing) voordat de verbinding aan de toepassing wordt geleverd.

Als de validatie van de lokale Shard-toewijzing mislukt (hetgeen aangeeft dat de cache onjuist is), stuurt de Shard-toewijzings beheer de globale Shard-toewijzing om de nieuwe juiste waarde voor de zoek actie te verkrijgen, de cache bij te werken en de juiste database verbinding te verkrijgen en te retour neren.

Gebruik **ConnectionOptions. none** alleen wanneer er wijzigingen in de Shard-toewijzing worden verwacht terwijl een toepassing online is. In dat geval kunnen de in de cache opgeslagen waarden worden verondersteld dat ze altijd juist zijn en dat de extra round-trip validatie oproep naar de doel database veilig kan worden overgeslagen. Die het database verkeer reduceert. De **connectionOptions** kan ook worden ingesteld via een waarde in een configuratie bestand om aan te geven of er gedurende een bepaalde periode sharding-wijzigingen worden verwacht of niet.  

In dit voor beeld wordt de waarde van een integer-sleutel **CustomerID** gebruikt met behulp van een **ShardMap** -object met de naam **customerShardMap**.  

```Java
int customerId = 12345;
int productId = 4321;
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

// Connect to the shard for that customer ID. No need to call a SqlConnection
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
{
    // Execute a simple command.
    SqlCommand cmd = conn.CreateCommand();
    cmd.CommandText = @"UPDATE Sales.Customer
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID";

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
    cmd.ExecuteNonQuery();
}  
```

De methode **OpenConnectionForKey** retourneert een nieuwe verbinding die al is geopend met de juiste data base. Verbindingen die op deze manier worden gebruikt, blijven optimaal profiteren van groepsgewijze verbindingen.

De **methode OpenConnectionForKeyAsync** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) is ook beschikbaar als uw toepassing gebruik maakt van asynchrone programmering.

## <a name="integrating-with-transient-fault-handling"></a>Integreren met tijdelijke fout afhandeling

Een best practice in het ontwikkelen van toepassingen voor gegevens toegang in de Cloud is om ervoor te zorgen dat tijdelijke fouten worden onderschept door de app, en dat de bewerkingen meerdere keren opnieuw worden geprobeerd voordat een fout optreedt. Tijdelijke fout afhandeling voor Cloud toepassingen wordt beschreven op tijdelijke fout afhandeling ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.net](/previous-versions/msp-n-p/dn440719(v=pandp.60))).

Tijdelijke fout afhandeling kan natuurlijk worden gecombineerd met het Data-Dependent-routerings patroon. De belangrijkste vereiste is om de volledige aanvraag voor gegevens toegang opnieuw uit te voeren, met inbegrip van het blok **gebruiken** dat de gegevens afhankelijke routerings verbinding heeft verkregen. Het vorige voor beeld kan als volgt worden herschreven.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Voor beeld: gegevens afhankelijke route ring met tijdelijke fout afhandeling

```Java
int customerId = 12345;
int productId = 4321;
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

Configuration.SqlRetryPolicy.ExecuteAction(() -> {

    // Connect to the shard for a customer ID.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
    {
        // Execute a simple command
        SqlCommand cmd = conn.CreateCommand();

        cmd.CommandText = @"UPDATE Sales.Customer
                            SET PersonID = @newPersonID
                            WHERE CustomerID = @customerID";

        cmd.Parameters.AddWithValue("@customerID", customerId);
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
        cmd.ExecuteNonQuery();

        Console.WriteLine("Update completed");
    }
});
```

Pakketten die nodig zijn voor het implementeren van tijdelijke fout afhandeling, worden automatisch gedownload wanneer u de voorbeeld toepassing voor Elastic data base bouwt.

## <a name="transactional-consistency"></a>Transactionele consistentie

Transactionele eigenschappen worden gegarandeerd voor alle bewerkingen lokaal naar een Shard. Trans acties die worden verzonden via gegevens afhankelijke route ring, worden bijvoorbeeld uitgevoerd binnen het bereik van de doel-Shard voor de verbinding. Op dit moment zijn er geen mogelijkheden voor het inschrijven van meerdere verbindingen in een trans actie. Daarom zijn er geen transactionele garanties voor bewerkingen die worden uitgevoerd op Shards.

## <a name="next-steps"></a>Volgende stappen

Als u een Shard wilt ontkoppelen of een Shard opnieuw wilt koppelen, raadpleegt u [de toewijzingen oplossen-klasse gebruiken om Shard-kaart problemen op te lossen](elastic-database-recovery-manager.md).

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]