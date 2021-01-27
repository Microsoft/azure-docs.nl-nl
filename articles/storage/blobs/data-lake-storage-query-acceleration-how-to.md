---
title: Gegevens filteren met behulp van Azure Data Lake Storage-query versnelling | Microsoft Docs
description: Gebruik query versnelling om een subset van gegevens uit uw opslag account op te halen.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 0340ab0a13d7c639d5e5bba49b3cdd445bad5a83
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879878"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Gegevens filteren met behulp van Azure Data Lake Storage-query versnelling

Dit artikel laat u zien hoe u query versnelling kunt gebruiken om een subset van gegevens uit uw opslag account op te halen. 

Met de functie voor het versnellen van query's kunnen toepassingen en analyse raamwerken gegevens verwerking aanzienlijk optimaliseren door alleen de gegevens op te halen die nodig zijn voor het uitvoeren van een bepaalde bewerking. Zie [Azure data Lake Storage-query versnelling](data-lake-storage-query-acceleration.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- U hebt een Azure-abonnement nodig voor toegang tot Azure Storage. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- Een **v2-** opslag account voor algemeen gebruik. Zie [een opslag account maken](../common/storage-account-create.md).

- Kies een tabblad om alle SDK-specifieke vereisten te bekijken.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  Niet van toepassing

  ### <a name="net"></a>[.NET](#tab/dotnet)

  De [.NET-SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)-versie 8 of hoger

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > In dit artikel wordt ervan uitgegaan dat u een Java-project hebt gemaakt met behulp van Apache Maven. Zie [instellen](storage-quickstart-blobs-java.md#setting-up)voor een voor beeld van het maken van een project met Apache Maven.
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3,8 of hoger.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Er zijn geen aanvullende vereisten vereist voor het gebruik van de Node.js SDK.

---

## <a name="enable-query-acceleration"></a>Query versnelling inschakelen

Als u query versnelling wilt gebruiken, moet u de functie voor query versnelling registreren bij uw abonnement. Nadat u hebt gecontroleerd of de functie is geregistreerd, moet u de Azure Storage Resource provider registreren. 

### <a name="step-1-register-the-query-acceleration-feature"></a>Stap 1: de functie voor query versnelling registreren

Als u query versnelling wilt gebruiken, moet u eerst de functie voor query versnelling registreren bij uw abonnement. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Open een Windows Power shell-opdracht venster.

1. Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

   ```powershell
   Connect-AzAccount
   ```

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

3. De functie voor het versnellen van query's registreren met behulp van de opdracht [REGI ster-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Open de [Azure Cloud shell](../../cloud-shell/overview.md)of open een opdracht console toepassing zoals Windows Power shell als u de Azure cli lokaal hebt [geïnstalleerd](/cli/azure/install-azure-cli) .

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

3. De functie voor het versnellen van query's registreren met behulp van de opdracht [AZ feature REGI ster](/cli/azure/feature#az-feature-register) .

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>Stap 2: controleren of de functie is geregistreerd

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Gebruik de opdracht [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) om te controleren of de registratie is voltooid.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u wilt controleren of de registratie is voltooid, gebruikt u de opdracht [AZ feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>Stap 3: de Azure Storage Resource provider registreren

Nadat de registratie is goedgekeurd, moet u de Azure Storage Resource provider opnieuw registreren. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Gebruik de opdracht [REGI ster-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) om de resource provider te registreren.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de resource provider wilt registreren, gebruikt u de opdracht [AZ provider REGI ster](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>Uw omgeving instellen

### <a name="step-1-install-packages"></a>Stap 1: pakketten installeren 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installeer de AZ-module versie 4.6.0 of hoger.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Als u een oudere versie van AZ wilt bijwerken, voert u de volgende opdracht uit:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Open een opdracht prompt en wijzig de map ( `cd` ) in de projectmap, bijvoorbeeld:

   ```console
   cd myProject
   ```

2. Installeer de `12.5.0-preview.6` versie van de Azure Blob Storage-client bibliotheek voor .net-pakket met behulp van de `dotnet add package` opdracht. 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.6.0
   ```

3. De voor beelden die in dit artikel worden weer gegeven, parseren een CSV-bestand met behulp van de [CsvHelper](https://www.nuget.org/packages/CsvHelper/) -bibliotheek. Gebruik de volgende opdracht om die bibliotheek te gebruiken.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Open het *pom.xml* -bestand van uw project in een tekst editor. Voeg de volgende afhankelijkheidselementen toe aan de groep met afhankelijkheden. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

Installeer de Azure Data Lake Storage-client bibliotheek voor python met behulp van [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Installeer Data Lake-client bibliotheek voor Java script door een Terminal venster te openen en de volgende opdracht te typen.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>Stap 2: instructies toevoegen

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Niet van toepassing

#### <a name="net"></a>[.NET](#tab/dotnet)

Voeg deze `using` instructies toe aan de bovenkant van het code bestand.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

Met query versnelling worden gegevens uit CSV-en JSON-indeling opgehaald. Zorg er daarom voor dat u met instructies toevoegt voor de CSV-of JSON-bibliotheken die u wilt gebruiken. De voor beelden die in dit artikel worden weer gegeven, parseren een CSV-bestand met behulp van de [CsvHelper](https://www.nuget.org/packages/CsvHelper/) -bibliotheek die beschikbaar is op NuGet. Daarom voegen we deze `using` instructies toe aan het begin van het code bestand.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Voor het compileren van voor beelden die in dit artikel worden weer gegeven, moet u ook deze `using` instructies toevoegen.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Voeg deze `import` instructies toe aan de bovenkant van het code bestand.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

Voeg deze import instructies toe aan de bovenkant van het code bestand.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Voeg de `storage-blob` module toe door deze instructie boven aan het code bestand te plaatsen. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

Met query versnelling worden gegevens uit CSV-en JSON-indeling opgehaald. Zorg er daarom voor dat u instructies toevoegt voor de CSV-of JSON-modules die u wilt gebruiken. De voor beelden die in dit artikel worden weer gegeven, parseren een CSV-bestand met behulp van de module [snelle CSV](https://www.npmjs.com/package/fast-csv) . Daarom voegen we deze instructie toe aan de bovenkant van het code bestand.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Gegevens ophalen met behulp van een filter

U kunt SQL gebruiken om de rij filter predikaten en kolom projectie op te geven in een aanvraag voor query versnelling. Met de volgende code wordt een query uitgevoerd op een CSV-bestand in de opslag en worden alle rijen met gegevens geretourneerd, waarbij de derde kolom overeenkomt met de waarde `Hemingway, Ernest` . 

- In de SQL-query wordt het sleutel woord `BlobStorage` gebruikt voor het aanduiden van het bestand dat wordt opgevraagd.

- Kolom verwijzingen worden opgegeven `_N` waar de eerste kolom zich bevindt `_1` . Als het bron bestand een veldnamenrij bevat, kunt u naar kolommen verwijzen met de naam die is opgegeven in de rij met koppen. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

De asynchrone methode `BlobQuickQueryClient.QueryAsync` verzendt de query naar de API voor query versnelling, waarna de resultaten worden teruggestuurd naar de toepassing als een [Stream](/dotnet/api/system.io.stream) -object.

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Context.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

De `BlobQuickQueryClient.openInputStream()` -methode verzendt de query naar de API voor query versnelling, waarna de resultaten worden teruggestuurd naar de toepassing als een `InputStream` object dat kan worden gelezen zoals elk ander InputStream-object.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

In dit voor beeld wordt de query verzonden naar de API voor query versnelling, waarna de resultaten weer worden gestreamd. Het `blob` object dat is door gegeven aan de `queryHemingway` Help-functie, is van het type [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient). Zie voor meer informatie over het ophalen van een [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) -object [Quick Start: blobs beheren met Java script v12 SDK in Node.js](storage-quickstart-blobs-nodejs.md).

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>Specifieke kolommen ophalen

U kunt de resultaten in een subset van kolommen bereiken. Op die manier haalt u alleen de kolommen op die nodig zijn om een bepaalde berekening uit te voeren. Dit verbetert de prestaties van de toepassing en vermindert de kosten omdat er minder gegevens via het netwerk worden overgedragen. 

Met deze code wordt alleen de `BibNum` kolom voor alle boeken in de gegevensset opgehaald. De gegevens uit de rij met koppen in het bron bestand worden ook gebruikt om te verwijzen naar kolommen in de query.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

De volgende code combineert rijen en kolom projecties in dezelfde query. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>Volgende stappen

- [Azure Data Lake Storage-query versnelling](data-lake-storage-query-acceleration.md)
- [Naslag informatie over SQL-taal voor query versnelling](query-acceleration-sql-reference.md)