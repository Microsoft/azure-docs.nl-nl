---
title: De HBase .NET SDK-Azure HDInsight gebruiken
description: Gebruik de HBase .NET SDK om tabellen te maken en te verwijderen en om gegevens te lezen en te schrijven.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/02/2019
ms.openlocfilehash: 70c1dcc133f01cf72392f444192d977440c55cf4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866621"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>De .NET-SDK gebruiken voor Apache HBase

[Apache HBase](apache-hbase-overview.md) biedt twee primaire opties voor het werken met uw gegevens: [Apache Hive query's en aanroepen naar de rest API van HBase](apache-hbase-tutorial-get-started-linux.md). U kunt rechtstreeks met de REST API werken met behulp van de `curl` opdracht of een vergelijkbaar hulp programma.

Voor C#-en .NET-toepassingen biedt de [micro soft HBASE rest-client bibliotheek voor .net](https://www.nuget.org/packages/Microsoft.HBase.Client/) een client bibliotheek boven op de HBase-rest API.

## <a name="install-the-sdk"></a>De SDK installeren

De HBase .NET SDK wordt weer gegeven als een NuGet-pakket, dat via de Visual Studio **NuGet Package Manager-console** kan worden geïnstalleerd met de volgende opdracht:

```console
Install-Package Microsoft.HBase.Client
```

## <a name="instantiate-a-new-hbaseclient-object"></a>Een nieuw HBaseClient-object instantiëren

Als u de SDK wilt gebruiken, moet u een nieuw object instantiëren `HBaseClient` , `ClusterCredentials` bestaande uit het `Uri` cluster en de Hadoop-gebruikers naam en-wacht woord.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Vervang CLUSTERNAME door de naam van uw HDInsight HBase-cluster en de gebruikers naam en het wacht woord met de Apache Hadoop referenties die zijn opgegeven bij het maken van het cluster. De standaard Hadoop-gebruikers naam is **beheerder**.

## <a name="create-a-new-table"></a>Een nieuwe tabel maken

HBase slaat gegevens op in tabellen. Een tabel bestaat uit een *Rowkey*, de primaire sleutel en een of meer groepen kolommen met de naam *kolom families*. De gegevens in elke tabel worden horizon taal gedistribueerd met een Rowkey bereik in *regio's*. Elke regio heeft een begin-en een end-sleutel. Een tabel kan een of meer regio's bevatten. Wanneer de gegevens in de tabel groeit, splitst HBase grote regio's op in kleinere regio's. Regio's worden opgeslagen in *regio servers*, waarbij één regio server meerdere regio's kan opslaan.

De gegevens worden fysiek opgeslagen in *HFiles*. Eén HFile bevat gegevens voor één tabel, één regio en één kolom familie. Rijen in HFile worden opgeslagen in Rowkey. Elke HFile heeft een *B +-structuur* index voor het snel ophalen van de rijen.

Als u een nieuwe tabel wilt maken, geeft u een `TableSchema` en kolommen op. Met de volgende code wordt gecontroleerd of de tabel RestSDKTable al bestaat. als dat niet het geval is, wordt de tabel gemaakt.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Deze nieuwe tabel bestaat uit twee kolom families, T1 en T2. Aangezien kolom families afzonderlijk in verschillende HFiles worden opgeslagen, is het zinvol om een afzonderlijke kolom familie te hebben voor vaak opgevraagde gegevens. In het volgende voor beeld van [Insert data](#insert-data) worden kolommen toegevoegd aan de T1-kolom familie.

## <a name="delete-a-table"></a>Een tabel verwijderen

Een tabel verwijderen:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Gegevens invoegen

Als u gegevens wilt invoegen, geeft u een unieke rijdefinitie op als de rij-id. Alle gegevens worden opgeslagen in een `byte[]` matrix. De volgende code definieert en voegt de `title` `director` kolommen,, en `release_date` toe aan de T1-kolom familie, omdat deze kolommen het meest worden gebruikt. De `description` `tagline` kolommen en worden toegevoegd aan de kolom familie T2. U kunt uw gegevens naar behoefte in kolom families partitioneren.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

HBase implementeert [Cloud Bigtable](https://cloud.google.com/bigtable/), dus de gegevens indeling ziet eruit als in de volgende afbeelding:

:::image type="content" source="./media/apache-hbase-rest-sdk/hdinsight-table-roles.png" alt-text="Voorbeeld gegevens uitvoer Apache HBase" border="true":::

## <a name="select-data"></a>Gegevens selecteren

Als u gegevens uit een HBase-tabel wilt lezen, geeft u de tabel naam en de rij-sleutel door aan de `GetCellsAsync` methode om de te retour neren `CellSet` .

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

In dit geval retourneert de code alleen de eerste overeenkomende rij, omdat er slechts één rij voor een unieke sleutel moet zijn. De geretourneerde waarde wordt gewijzigd in `string` de indeling van de `byte[]` matrix. U kunt de waarde ook converteren naar andere typen, zoals een geheel getal voor de release datum van de film:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Scannen over rijen

HBase gebruikt `scan` om een of meer rijen op te halen. In dit voor beeld wordt een aanvraag voor meerdere rijen in batches van 10 en worden gegevens opgehaald waarvan de sleutel waarden tussen 25 en 35 liggen. Nadat u alle rijen hebt opgehaald, verwijdert u de scanner om de resources op te schonen.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met een voorbeeld van Apache HBase in HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Een end-to-end-toepassing bouwen met het [analyseren van real-time Twitter-sentiment met Apache HBase](./apache-hbase-tutorial-get-started-linux.md)