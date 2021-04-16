---
title: Gegevens filteren met behulp van Azure Data Lake Storage queryversnelling | Microsoft Docs
description: Gebruik queryversnelling om een subset met gegevens op te halen uit uw opslagaccount.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp
ms.openlocfilehash: 58b8cdef604861342a6489ef4e57ff1d057cd3f4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377731"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Gegevens filteren met behulp van Azure Data Lake Storage queryversnelling

In dit artikel wordt beschreven hoe u queryversnelling gebruikt om een subset van gegevens op te halen uit uw opslagaccount. 

Met queryversnelling kunnen toepassingen en analyse-frameworks de gegevensverwerking aanzienlijk optimaliseren door alleen de gegevens op te halen die ze nodig hebben om een bepaalde bewerking uit te voeren. Zie Queryversnelling voor [Azure Data Lake Storage meer informatie.](data-lake-storage-query-acceleration.md)

## <a name="prerequisites"></a>Vereisten

- U hebt een Azure-abonnement nodig voor toegang tot Azure Storage. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- Een **v2-opslagaccount voor** algemeen gebruik. Zie [Een opslagaccount maken.](../common/storage-account-create.md)

- Kies een tabblad om alle SDK-specifieke vereisten weer te zien.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  Niet van toepassing

  ### <a name="net"></a>[.NET](#tab/dotnet)

  De [.NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java Development Kit (JDK)](/java/azure/jdk/)-versie 8 of hoger

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > In dit artikel wordt ervan uitgenomen dat u een Java-project hebt gemaakt met behulp van Apache Maven. Zie Instellen voor een voorbeeld van het maken [](storage-quickstart-blobs-java.md#setting-up)van een project met behulp van Apache Maven.
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3.8 of hoger.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Er zijn geen aanvullende vereisten vereist voor het gebruik van de Node.js SDK.

---

## <a name="enable-query-acceleration"></a>Queryversnelling inschakelen

Als u queryversnelling wilt gebruiken, moet u de functie queryversnelling registreren bij uw abonnement. Nadat u hebt gecontroleerd of de functie is geregistreerd, moet u de resourceprovider Azure Storage registreren. 

### <a name="step-1-register-the-query-acceleration-feature"></a>Stap 1: de functie queryversnelling registreren

Als u queryversnelling wilt gebruiken, moet u eerst de functie queryversnelling registreren bij uw abonnement. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Open een Windows PowerShell opdrachtvenster.

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

3. Registreer de functie queryversnelling met behulp [van de opdracht Register-AzProviderFeature.](/powershell/module/az.resources/register-azproviderfeature)

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Open de [Azure Cloud Shell](../../cloud-shell/overview.md)of als u [](/cli/azure/install-azure-cli) de Azure CLI lokaal hebt geïnstalleerd, opent u een opdrachtconsoletoepassing zoals Windows PowerShell.

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op abonnement van het opslagaccount.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

3. Registreer de functie queryversnelling met behulp van [de opdracht az feature register.](/cli/azure/feature#az-feature-register)

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

Gebruik de opdracht az feature om te controleren of de [registratie is](/cli/azure/feature#az-feature-show) voltooid.

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>Stap 3: de resourceprovider Azure Storage registreren

Nadat uw registratie is goedgekeurd, moet u de resourceprovider opnieuw Azure Storage registreren. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Gebruik de opdracht [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) om de resourceprovider te registreren.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de opdracht az [provider register](/cli/azure/provider#az-provider-register) om de resourceprovider te registreren.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>Uw omgeving instellen

### <a name="step-1-install-packages"></a>Stap 1: pakketten installeren 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Installeer de Az-module versie 4.6.0 of hoger.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Voer de volgende opdracht uit om een oudere versie van Az bij te werken:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Open een opdrachtprompt en wijzig map ( `cd` ) in uw projectmap Bijvoorbeeld:

   ```console
   cd myProject
   ```

2. Installeer de `12.5.0-preview.6` versie of hoger van de Azure Blob Storage-clientbibliotheek voor het .NET-pakket met behulp van de opdracht `dotnet add package` . 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.8.0
   ```

3. In de voorbeelden die in dit artikel worden weergegeven, wordt een CSV-bestand geparseerd met behulp van de [CsvHelper-bibliotheek.](https://www.nuget.org/packages/CsvHelper/) Gebruik de volgende opdracht om deze bibliotheek te gebruiken.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Open het *pom.xml* bestand van uw project in een teksteditor. Voeg de volgende afhankelijkheidselementen toe aan de groep met afhankelijkheden. 

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

Installeer de Azure Data Lake Storage-clientbibliotheek voor Python met behulp van [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Installeer de Data Lake-clientbibliotheek voor JavaScript door een terminalvenster te openen en de volgende opdracht te typen.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>Stap 2: instructies toevoegen

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Niet van toepassing

#### <a name="net"></a>[.NET](#tab/dotnet)

Voeg deze `using` instructies toe boven aan het codebestand.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

Queryversnelling haalt gegevens in CSV- en JSON-indeling op. Zorg er daarom voor dat u using-instructies toevoegt voor alle CSV- of Json-parseringsbibliotheken die u wilt gebruiken. In de voorbeelden die in dit artikel worden weergegeven, wordt een CSV-bestand geparseerd met behulp van de [CsvHelper-bibliotheek](https://www.nuget.org/packages/CsvHelper/) die beschikbaar is op NuGet. Daarom voegen we deze instructies toe `using` aan de bovenkant van het codebestand.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Als u voorbeelden wilt compileren die in dit artikel worden gepresenteerd, moet u deze instructies `using` ook toevoegen.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Voeg deze `import` instructies toe boven aan het codebestand.

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

Voeg deze importin instructies toe boven aan uw codebestand.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Neem de `storage-blob` module op door deze instructie boven aan uw codebestand te plaatsen. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

Queryversnelling haalt gegevens in CSV- en JSON-indeling op. Zorg er daarom voor dat u instructies toevoegt voor csv- of Json-parseringsmodules die u wilt gebruiken. In de voorbeelden die in dit artikel worden weergegeven, wordt een CSV-bestand geparseerd met behulp van de [fast-csv-module.](https://www.npmjs.com/package/fast-csv) Daarom voegen we deze instructie toe aan de bovenkant van het codebestand.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Gegevens ophalen met behulp van een filter

U kunt SQL gebruiken om de rijfilterpredicaten en kolomprojecties op te geven in een queryversnellingsaanvraag. Met de volgende code wordt een query op een CSV-bestand in opslag opgevraagd en worden alle rijen met gegevens retourneert, waarbij de derde kolom overeenkomt met de waarde `Hemingway, Ernest` . 

- In de SQL-query wordt het `BlobStorage` trefwoord gebruikt om het bestand aan te geven dat wordt opgevraagd.

- Kolomverwijzingen worden opgegeven als `_N` de plaats waar de eerste kolom `_1` is. Als het bronbestand een headerrij bevat, kunt u verwijzen naar kolommen met de naam die is opgegeven in de headerrij. 

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

De async-methode verzendt de query naar de `BlobQuickQueryClient.QueryAsync` queryversnellings-API en streamt de resultaten vervolgens als een [Stream-object](/dotnet/api/system.io.stream) naar de toepassing.

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
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture, hasHeaderRecord: true) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Parser.Record));
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

De methode verzendt de query naar de API voor queryversnelling en streamt de resultaten vervolgens terug naar de toepassing als een object dat kan worden gelezen zoals elk ander `BlobQuickQueryClient.openInputStream()` `InputStream` InputStream-object.

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

In dit voorbeeld wordt de query naar de API voor queryversnelling verzendt en worden de resultaten vervolgens terug gestreamd. Het `blob` object dat wordt doorgegeven aan de `queryHemingway` helperfunctie is van het type [BlockBlobClient.](/javascript/api/@azure/storage-blob/blockblobclient) Zie Quickstart: Blobs beheren met [JavaScript v12 SDK in ](storage-quickstart-blobs-nodejs.md)Node.jsvoor meer informatie over het krijgen van een [BlockBlobClient-object.](/javascript/api/@azure/storage-blob/blockblobclient)

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

U kunt het bereik van uw resultaten wijzigen in een subset kolommen. Op die manier haalt u alleen de kolommen op die nodig zijn om een bepaalde berekening uit te voeren. Dit verbetert de prestaties van toepassingen en vermindert de kosten omdat er minder gegevens via het netwerk worden overgedragen. 

Met deze code wordt alleen de `BibNum` kolom opgehaald voor alle boeken in de gegevensset. Er wordt ook gebruikgemaakt van de informatie uit de headerrij in het bronbestand om te verwijzen naar kolommen in de query.

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

In de volgende code worden rijfilters en kolomprojecties gecombineerd tot dezelfde query. 

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

- [Azure Data Lake Storage queryversnelling](data-lake-storage-query-acceleration.md)
- [Naslag voor SQL-taal voor queryversnelling](query-acceleration-sql-reference.md)
