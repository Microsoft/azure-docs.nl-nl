---
title: Phoenix Query Server REST-SDK-Azure HDInsight
description: Installeer en gebruik de REST-SDK voor de Phoenix Query Server in azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/01/2020
ms.openlocfilehash: 5ce459e2820cfd2ea5cacb036f5d6885c9c5afa0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933371"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>REST SDK van Apache Phoenix query server

[Apache Phoenix](https://phoenix.apache.org/) is een open-source, enorm parallelle relationele Gegevenstoegangslaag op [Apache HBase](apache-hbase-overview.md). Met Phoenix kunt u SQL-achtige query's gebruiken met HBase via SSH-hulpprogram ma's zoals [sqlline gebruiken](apache-hbase-query-with-phoenix.md). Phoenix biedt ook een HTTP-server met de naam Phoenix Query Server (PQS), een thin client die twee transport mechanismen voor client communicatie ondersteunt: JSON-en protocol buffers. Protocol buffers is het standaard mechanisme en biedt een efficiëntere communicatie dan JSON.

In dit artikel wordt beschreven hoe u de PQS REST-SDK gebruikt om tabellen, afzonderlijke rijen en in bulk te maken en gegevens te selecteren met behulp van SQL-instructies. In de voor beelden wordt het [Microsoft .net stuur programma voor Apache Phoenix query server](https://www.nuget.org/packages/Microsoft.Phoenix.Client)gebruikt. Deze SDK is gebaseerd op [de Avatica-api's van Apache Calcite](https://calcite.apache.org/avatica/) , die exclusief protocol buffers gebruiken voor de serialisatie-indeling.

Zie voor meer informatie [referentie voor Apache Calcite Avatica-protocol buffers](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>De SDK installeren

Microsoft .NET stuur programma voor Apache Phoenix query server is beschikbaar als een NuGet-pakket, dat kan worden geïnstalleerd via de Visual Studio **NuGet Package Manager-console** met de volgende opdracht:

```console
Install-Package Microsoft.Phoenix.Client
```

## <a name="instantiate-new-phoenixclient-object"></a>Nieuw PhoenixClient-object instantiëren

Als u de bibliotheek wilt gebruiken, moet u een nieuw object instantiëren `PhoenixClient` `ClusterCredentials` met daarin het `Uri` cluster en de Apache Hadoop gebruikers naam en het wacht woord van het cluster.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Vervang CLUSTERNAME door de naam van uw HDInsight HBase-cluster en de gebruikers naam en het wacht woord met de Hadoop-referenties die zijn opgegeven bij het maken van het cluster. De standaard Hadoop-gebruikers naam is **beheerder**.

## <a name="generate-unique-connection-identifier"></a>Unieke verbindings-id genereren

Als u een of meer aanvragen wilt verzenden naar PQS, moet u een unieke verbindings-id toevoegen om de aanvragen te koppelen aan de verbinding.

```csharp
string connId = Guid.NewGuid().ToString();
```

Elk voor beeld maakt eerst een aanroep `OpenConnectionRequestAsync` van de-methode, waarbij de unieke verbindings-id wordt door gegeven. Definieer vervolgens `ConnectionProperties` `RequestOptions` deze objecten en geef deze door en de gegenereerde verbindings-id met de `ConnectionSyncRequestAsync` methode. Het object van PQS zorgt `ConnectionSyncRequest` ervoor dat zowel de client als de server een consistente weer gave van de data base-eigenschappen hebben.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest en de bijbehorende ConnectionProperties

`ConnectionSyncRequestAsync`Geef een object door om aan te roepen `ConnectionProperties` .

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

Hier volgen enkele interessante eigenschappen:

| Eigenschap | Beschrijving |
| -- | -- |
| Door voeren | Een Booleaanse waarde die aangeeft of de `autoCommit` Phoenix-trans acties zijn ingeschakeld. |
| ReadOnly | Een Booleaanse waarde die aangeeft of de verbinding alleen-lezen is. |
| TransactionIsolation | Een geheel getal dat het niveau van de transactie isolatie volgens de JDBC-specificatie aangeeft: Zie de volgende tabel.|
| Catalogus | De naam van de catalogus die moet worden gebruikt bij het ophalen van verbindings eigenschappen. |
| Schema | De naam van het schema dat moet worden gebruikt bij het ophalen van verbindings eigenschappen. |
| IsDirty | Een Booleaanse waarde die aangeeft of de eigenschappen zijn gewijzigd. |

Dit zijn de `TransactionIsolation` waarden:

| Isolatie waarde | Beschrijving |
| -- | -- |
| 0 | Trans acties worden niet ondersteund. |
| 1 | Vuil Lees bewerkingen, niet-Herhaal bare Lees bewerkingen en fantoom Lees bewerkingen kunnen zich voordoen. |
| 2 | Vuil Lees bewerkingen worden verhinderd, maar niet-Herhaal bare Lees bewerkingen en fantoom Lees bewerkingen kunnen zich voordoen. |
| 4 | Vuil Lees bewerkingen en niet-Herhaal bare Lees bewerkingen worden voor komen, maar er kunnen spook Lees bewerkingen optreden. |
| 8 | Vuil Lees bewerkingen, niet-Herhaal bare Lees bewerkingen en fantoom Lees bewerkingen worden niet voor komen. |

## <a name="create-a-new-table"></a>Een nieuwe tabel maken

HBase, zoals andere RDBMS, slaat gegevens op in tabellen. Phoenix gebruikt standaard SQL-query's voor het maken van nieuwe tabellen, terwijl de primaire sleutel en kolom typen worden gedefinieerd.

In dit voor beeld en alle latere voor beelden gebruikt u het geïnstantieerd `PhoenixClient` object zoals gedefinieerd in [instantiëren van een nieuw PhoenixClient-object](#instantiate-new-phoenixclient-object).

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

In het voor gaande voor beeld wordt een nieuwe tabel gemaakt `Customers` met de naam met behulp van de `IF NOT EXISTS` optie. De `CreateStatementRequestAsync` aanroep maakt een nieuwe-instructie in de Avitica-server (PQS). Het `finally` blok sluit het geretourneerde `CreateStatementResponse` object en de `OpenConnectionResponse` objecten.

## <a name="insert-data-individually"></a>Gegevens afzonderlijk invoegen

In dit voor beeld ziet u een afzonderlijke gegevens invoer, met een verwijzing naar een `List<string>` verzameling van de afkortingen van de Amerikaanse staat en het gebied:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

De kolom waarde van de tabel `StateProvince` wordt gebruikt in een latere Selecteer bewerking.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

De structuur voor het uitvoeren van een instructie INSERT is vergelijkbaar met het maken van een nieuwe tabel. Aan het einde van het `try` blok wordt de trans actie expliciet doorgevoerd. In dit voor beeld wordt een insert-trans actie van 300 keer herhaald. In het volgende voor beeld ziet u een efficiënt batch-invoeg proces.

## <a name="batch-insert-data"></a>Batch gegevens invoegen

De volgende code is bijna identiek aan de code voor het afzonderlijk invoegen van gegevens. In dit voor beeld wordt het `UpdateBatch` object in een aanroep van gebruikt in `ExecuteBatchRequestAsync` plaats van herhaaldelijk te worden aangeroepen `ExecuteRequestAsync` met een voor bereide instructie.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

In een test omgeving is het niet meer nodig om 300 nieuwe records in te voegen bijna twee minuten. Als u daarentegen 300 records invoegt als een batch, is er slechts zes seconden een vereiste.

## <a name="select-data"></a>Gegevens selecteren

In dit voor beeld ziet u hoe u één verbinding opnieuw kunt gebruiken om meerdere query's uit te voeren:

1. Selecteer alle records en haal vervolgens de resterende records op nadat de standaard waarde van 100 is geretourneerd.
2. Gebruik een instructie voor het totale aantal rijen om het enkele scalaire resultaat op te halen.
3. Voer een SELECT-instructie uit waarmee het totaal aantal klanten per staat of gebied wordt geretourneerd.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

De uitvoer van de `select` instructies moet het volgende resultaat zijn:

```output
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>Volgende stappen

* [Apache Phoenix in HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [De Apache HBase REST SDK gebruiken](apache-hbase-rest-sdk.md)
