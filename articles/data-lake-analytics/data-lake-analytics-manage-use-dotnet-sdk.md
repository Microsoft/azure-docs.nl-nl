---
title: Azure Data Lake Analytics beheren met Azure .NET SDK
description: In dit artikel wordt beschreven hoe u de Azure .NET SDK gebruikt om apps te schrijven die Data Lake Analytics taken, gegevens bronnen & gebruikers beheren.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/18/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: a9ece3fe54ca8596f610439f8a0dd228ae1ef907
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220904"
---
# <a name="manage-azure-data-lake-analytics-a-net-app"></a>Azure Data Lake Analytics beheren met behulp van een .NET-app

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

In dit artikel wordt beschreven hoe u Azure Data Lake Analytics accounts, gegevens bronnen, gebruikers en taken beheert met behulp van een app die is geschreven met behulp van de Azure .NET SDK. 

## <a name="prerequisites"></a>Vereisten

* **Visual Studio 2015, Visual Studio 2013 update 4 of Visual Studio 2012 met Visual C++ geïnstalleerd**.
* **Microsoft Azure SDK voor .NET versie 2.5 of hoger**.  U kunt dit installeren met het [Webplatforminstallatieprogramma](https://www.microsoft.com/web/downloads/platform.aspx).
* **Vereiste NuGet-pakketten**

### <a name="install-nuget-packages"></a>NuGet-pakketten installeren

|Pakket|Versie|
|-------|-------|
|[Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)| 2.3.1|
|[Microsoft.Azure.Management.DataLake.Analytics](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics)|3.0.0|
|[Microsoft.Azure.Management.DataLake.Store](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store)|2.2.0|
|[Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|1.6.0-Preview|
|[Microsoft.Azure.Graph.RBAC](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|3.4.0-Preview|

U kunt deze pakketten installeren via de NuGet-opdracht regel met de volgende opdrachten:

```powershell
Install-Package -Id Microsoft.Rest.ClientRuntime.Azure.Authentication  -Version 2.3.1
Install-Package -Id Microsoft.Azure.Management.DataLake.Analytics  -Version 3.0.0
Install-Package -Id Microsoft.Azure.Management.DataLake.Store  -Version 2.2.0
Install-Package -Id Microsoft.Azure.Management.ResourceManager  -Version 1.6.0-preview
Install-Package -Id Microsoft.Azure.Graph.RBAC -Version 3.4.0-preview
```

## <a name="common-variables"></a>Algemene variabelen

```csharp
string subid = "<Subscription ID>"; // Subscription ID (a GUID)
string tenantid = "<Tenant ID>"; // AAD tenant ID or domain. For example, "contoso.onmicrosoft.com"
string rg == "<value>"; // Resource  group name
string clientid = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID (this will work, but you should pick your own)
```

## <a name="authentication"></a>Verificatie

U hebt meerdere opties om u aan te melden bij Azure Data Lake Analytics. Het volgende code fragment toont een voor beeld van verificatie met interactieve gebruikers verificatie met een pop-up.

``` csharp
using System;
using System.IO;
using System.Threading;
using System.Security.Cryptography.X509Certificates;

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Analytics;
using Microsoft.Azure.Management.DataLake.Analytics.Models;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Graph.RBAC;

public static Program
{
   public static string TENANT = "microsoft.onmicrosoft.com";
   public static string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
   public static System.Uri ARM_TOKEN_AUDIENCE = new System.Uri( @"https://management.core.windows.net/");
   public static System.Uri ADL_TOKEN_AUDIENCE = new System.Uri( @"https://datalake.azure.net/" );
   public static System.Uri GRAPH_TOKEN_AUDIENCE = new System.Uri( @"https://graph.windows.net/" );

   static void Main(string[] args)
   {
      string MY_DOCUMENTS= System.Environment.GetFolderPath( System.Environment.SpecialFolder.MyDocuments);
      string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");

      var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
      var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var graphCreds = GetCreds_User_Popup(TENANT, GRAPH_TOKEN_AUDIENCE, CLIENTID, tokenCache);
   }
}
```

De bron code voor **GetCreds_User_Popup** en de code voor andere verificatie opties worden in [Data Lake Analytics .NET-verificatie opties](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options) besproken.


## <a name="create-the-client-management-objects"></a>De client beheer objecten maken

``` csharp
var resourceManagementClient = new ResourceManagementClient(armCreds) { SubscriptionId = subid };

var adlaAccountClient = new DataLakeAnalyticsAccountManagementClient(armCreds);
adlaAccountClient.SubscriptionId = subid;

var adlsAccountClient = new DataLakeStoreAccountManagementClient(armCreds);
adlsAccountClient.SubscriptionId = subid;

var adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(adlCreds);
var adlaJobClient = new DataLakeAnalyticsJobManagementClient(adlCreds);

var adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

var  graphClient = new GraphRbacManagementClient(graphCreds);
graphClient.TenantID = domain;

```

## <a name="manage-accounts"></a>Accounts beheren

### <a name="create-an-azure-resource-group"></a>Een Azure-resourcegroep maken

Als u er nog geen hebt gemaakt, moet u een Azure-resource groep hebben om uw Data Lake Analytics-onderdelen te maken. U hebt uw verificatie referenties, abonnements-ID en locatie nodig. De volgende code laat zien hoe u een resource groep maakt:

``` csharp
var resourceGroup = new ResourceGroup { Location = location };
resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rg);
```

Zie Azure-resource groepen en-Data Lake Analytics voor meer informatie.

### <a name="create-a-data-lake-store-account"></a>Een Data Lake Store-account maken

Voor het ADLA-account is een ADLS-account vereist. Als u er nog geen moet gebruiken, kunt u er een maken met de volgende code:

``` csharp
var new_adls_params = new DataLakeStoreAccount(location: _location);
adlsAccountClient.Account.Create(rg, adls, new_adls_params);
```

### <a name="create-a-data-lake-analytics-account"></a>Een Data Lake Analytics-account maken

Met de volgende code wordt een ADLS-account gemaakt

``` csharp
var new_adla_params = new DataLakeAnalyticsAccount()
{
   DefaultDataLakeStoreAccount = adls,
   Location = location
};

adlaClient.Account.Create(rg, adla, new_adla_params);
```

### <a name="list-data-lake-store-accounts"></a>Data Lake Store accounts weer geven

``` csharp
var adlsAccounts = adlsAccountClient.Account.List().ToList();
foreach (var adls in adlsAccounts)
{
   Console.WriteLine($"ADLS: {0}", adls.Name);
}
```

### <a name="list-data-lake-analytics-accounts"></a>Data Lake Analytics accounts weer geven

``` csharp
var adlaAccounts = adlaClient.Account.List().ToList();

for (var adla in AdlaAccounts)
{
   Console.WriteLine($"ADLA: {0}, adla.Name");
}
```

### <a name="checking-if-an-account-exists"></a>Controleren of er een account bestaat

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
```

### <a name="get-information-about-an-account"></a>Informatie over een account ophalen

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
if (exists)
{
   var adla_accnt = adlaClient.Account.Get(rg, adla);
}
```

### <a name="delete-an-account"></a>Een account verwijderen

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
   adlaClient.Account.Delete(rg, adla);
}
```

### <a name="get-the-default-data-lake-store-account"></a>Het standaard Data Lake Store-account ophalen

Voor elk Data Lake Analytics account is een standaard Data Lake Store-account vereist. Gebruik deze code om het standaard opslag account voor een Analytics-account te bepalen.

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
  var adla_accnt = adlaClient.Account.Get(rg, adla);
  string def_adls_account = adla_accnt.DefaultDataLakeStoreAccount;
}
```

## <a name="manage-data-sources"></a>Gegevensbronnen beheren

Data Lake Analytics ondersteunt momenteel de volgende gegevens bronnen:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage-account](../storage/common/storage-introduction.md)

### <a name="link-to-an-azure-storage-account"></a>Koppeling naar een Azure Storage-account

U kunt koppelingen maken naar Azure Storage-accounts.

``` csharp
string storage_key = "xxxxxxxxxxxxxxxxxxxx";
string storage_account = "mystorageaccount";
var addParams = new AddStorageAccountParameters(storage_key);            
adlaClient.StorageAccounts.Add(rg, adla, storage_account, addParams);
```

### <a name="list-azure-storage-data-sources"></a>Azure Storage gegevens bronnen weer geven

``` csharp
var stg_accounts = adlaAccountClient.StorageAccounts.ListByAccount(rg, adla);

if (stg_accounts != null)
{
  foreach (var stg_account in stg_accounts)
  {
      Console.WriteLine($"Storage account: {0}", stg_account.Name);
  }
}
```

### <a name="list-data-lake-store-data-sources"></a>Data Lake Store gegevens bronnen weer geven

``` csharp
var adls_accounts = adlsClient.Account.List();

if (adls_accounts != null)
{
  foreach (var adls_accnt in adls_accounts)
  {
      Console.WriteLine($"ADLS account: {0}", adls_accnt.Name);
  }
}
```

### <a name="upload-and-download-folders-and-files"></a>Mappen en bestanden uploaden en downloaden

U kunt het Data Lake Store bestandssysteem client beheer object gebruiken voor het uploaden en downloaden van afzonderlijke bestanden of mappen van Azure naar uw lokale computer, met behulp van de volgende methoden:

- UploadFolder
- Upload File
- DownloadFolder
- DownloadFile

De eerste para meter voor deze methoden is de naam van het Data Lake Store account, gevolgd door para meters voor het bronpad en het doelpad.

In het volgende voor beeld ziet u hoe u een map in de Data Lake Store kunt downloaden.

``` csharp
adlsFileSystemClient.FileSystem.DownloadFolder(adls, sourcePath, destinationPath);
```

### <a name="create-a-file-in-a-data-lake-store-account"></a>Een bestand maken in een Data Lake Store-account

``` csharp
using (var memstream = new MemoryStream())
{
   using (var sw = new StreamWriter(memstream, UTF8Encoding.UTF8))
   {
      sw.WriteLine("Hello World");
      sw.Flush();
      
      memstream.Position = 0;

      adlsFileSystemClient.FileSystem.Create(adls, "/Samples/Output/randombytes.csv", memstream);
   }
}
```

### <a name="verify-azure-storage-account-paths"></a>Paden van Azure Storage-account controleren

Met de volgende code wordt gecontroleerd of een Azure Storage-account (storageAccntName) bestaat in een Data Lake Analytics-account (analyticsAccountName) en of een container (containerName) bestaat in het Azure Storage-account.

``` csharp
string storage_account = "mystorageaccount";
string storage_container = "mycontainer";
bool accountExists = adlaClient.Account.StorageAccountExists(rg, adla, storage_account));
bool containerExists = adlaClient.Account.StorageContainerExists(rg, adla, storage_account, storage_container));
```

## <a name="manage-catalog-and-jobs"></a>Catalogus en taken beheren

Het DataLakeAnalyticsCatalogManagementClient-object biedt methoden voor het beheren van de SQL database voor elk Azure Data Lake Analytics-account. De DataLakeAnalyticsJobManagementClient biedt methoden om taken te verzenden en te beheren die worden uitgevoerd op de data base met U-SQL-scripts.

### <a name="list-databases-and-schemas"></a>Data bases en schema's weer geven

In de verschillende dingen die u kunt vermelden, zijn de meest voorkomende data bases en hun schema. Met de volgende code wordt een verzameling data bases opgehaald en wordt vervolgens het schema voor elke Data Base opgesomd.

``` csharp
var databases = adlaCatalogClient.Catalog.ListDatabases(adla);
foreach (var db in databases)
{
  Console.WriteLine($"Database: {db.Name}");
  Console.WriteLine(" - Schemas:");
  var schemas = adlaCatalogClient.Catalog.ListSchemas(adla, db.Name);
  foreach (var schm in schemas)
  {
      Console.WriteLine($"\t{schm.Name}");
  }
}
```

### <a name="list-table-columns"></a>Tabel kolommen weer geven

De volgende code laat zien hoe u toegang krijgt tot de data base met een Data Lake Analytics Catalog Management-client om de kolommen in een opgegeven tabel weer te geven.

```csharp
var tbl = adlaCatalogClient.Catalog.GetTable(adla, "master", "dbo", "MyTableName");
IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

foreach (USqlTableColumn utc in columns)
{
  Console.WriteLine($"\t{utc.Name}");
}
```

### <a name="submit-a-u-sql-job"></a>Een U-SQL-taak verzenden

De volgende code laat zien hoe u een Data Lake Analytics Job Management-client kunt gebruiken om een taak te verzenden.

``` csharp
string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
string scriptTxt = string.Empty;
using (StreamReader sr = new StreamReader(scriptStrm))
{
    scriptTxt = sr.ReadToEnd();
}

var jobName = "SR_Wikipedia";
var jobId = Guid.NewGuid();
var properties = new USqlJobProperties(scriptTxt);
var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
var jobInfo = adlaJobClient.Job.Create(adla, jobId, parameters);
Console.WriteLine($"Job {jobName} submitted.");
```

### <a name="list-failed-jobs"></a>Mislukte taken weer geven

De volgende code bevat informatie over taken die zijn mislukt.

```csharp
var odq = new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" };
var jobs = adlaJobClient.Job.List(adla, odq);
foreach (var j in jobs)
{
   Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
}
```

### <a name="list-pipelines"></a>Pijp lijnen weer geven

De volgende code bevat informatie over elke pijp lijn van taken die naar het account worden verzonden.

``` csharp
var pipelines = adlaJobClient.Pipeline.List(adla);
foreach (var p in pipelines)
{
   Console.WriteLine($"Pipeline: {p.Name}\t{p.PipelineId}\t{p.LastSubmitTime}");
}
```

### <a name="list-recurrences"></a>Terugkeer patronen weer geven

De volgende code bevat informatie over elk herhaling van taken die naar het account worden verzonden.

``` csharp
var recurrences = adlaJobClient.Recurrence.List(adla);
foreach (var r in recurrences)
{
   Console.WriteLine($"Recurrence: {r.Name}\t{r.RecurrenceId}\t{r.LastSubmitTime}");
}
```

## <a name="common-graph-scenarios"></a>Algemene grafiek scenario's

### <a name="look-up-user-in-the-aad-directory"></a>Gebruiker opzoeken in de AAD-Directory

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
```

### <a name="get-the-objectid-of-a-user-in-the-aad-directory"></a>De ObjectId ophalen van een gebruiker in de AAD-Directory

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
Console.WriteLine( userinfo.ObjectId )
```

## <a name="manage-compute-policies"></a>Reken beleid beheren

Het DataLakeAnalyticsAccountManagementClient-object biedt methoden voor het beheren van het reken beleid voor een Data Lake Analytics-account.

### <a name="list-compute-policies"></a>Reken beleid weer geven

Met de volgende code wordt een lijst van reken beleid voor een Data Lake Analytics-account opgehaald.

``` csharp
var policies = adlaAccountClient.ComputePolicies.ListByAccount(rg, adla);
foreach (var p in policies)
{
   Console.WriteLine($"Name: {p.Name}\tType: {p.ObjectType}\tMax AUs / job: {p.MaxDegreeOfParallelismPerJob}\tMin priority / job: {p.MinPriorityPerJob}");
}
```

### <a name="create-a-new-compute-policy"></a>Een nieuw reken beleid maken

Met de volgende code wordt een nieuw reken beleid gemaakt voor een Data Lake Analytics-account, waarbij het maximum aantal van AUs wordt ingesteld op de opgegeven gebruiker tot 50 en de minimale taak prioriteit op 250.

``` csharp
var userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde";
var newPolicyParams = new ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250);
adlaAccountClient.ComputePolicies.CreateOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams);
```

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Azure Data Lake Analytics beheren met Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Azure Data Lake Analytics-taken bewaken en problemen oplossen met Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)