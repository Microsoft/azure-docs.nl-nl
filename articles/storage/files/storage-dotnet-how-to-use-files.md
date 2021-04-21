---
title: Ontwikkelen voor Azure Files met .NET | Microsoft Docs
description: Meer informatie over het ontwikkelen van .NET-toepassingen en -services die gebruikmaken Azure Files voor het opslaan van gegevens.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c00f001ae3cba9420a137a42f9f696619584d50
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817373"
---
# <a name="develop-for-azure-files-with-net"></a>Ontwikkelen voor Azure Files met .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Leer de basisbeginselen van het ontwikkelen van .NET-toepassingen die gebruikmaken [Azure Files](storage-files-introduction.md) voor het opslaan van gegevens. In dit artikel wordt beschreven hoe u een eenvoudige consoletoepassing maakt om het volgende te doen met .NET en Azure Files:

- Haal de inhoud van een bestand op.
- Stel de maximale grootte of het quotum voor een bestands share in.
- Maak een Shared Access Signature (SAS) voor een bestand.
- Een bestand kopiëren naar een ander bestand in hetzelfde opslagaccount.
- Een bestand kopiëren naar een blob in hetzelfde opslagaccount.
- Maak een momentopname van een bestands share.
- Een bestand herstellen vanuit een momentopname van een share.
- Gebruik Azure Storage metrics voor probleemoplossing.

Zie Wat is Azure Files? voor meer informatie [over Azure Files?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>De .NET-API's

Azure Files biedt twee veelzijdige methoden voor het maken van clienttoepassingen: Server Message Block (SMB) en REST. Binnen .NET worden deze `System.IO` benaderingen door de `Azure.Storage.Files.Shares` API's en geabstraheerd.

API | Wanneer gebruikt u dit? | Opmerkingen
----|-------------|------
[System.IO](/dotnet/api/system.io) | Uw toepassing: <ul><li>Moet bestanden lezen/schrijven met behulp van SMB</li><li>Wordt uitgevoerd op een apparaat met toegang tot uw Azure Files-account via poort 445</li><li>Hoeft geen beheerinstellingen van de bestandsshare te beheren</li></ul> | Bestands-I/O die met een Azure Files via SMB is geïmplementeerd, is in het algemeen hetzelfde als I/O met een netwerkbestands share of een lokaal opslagapparaat. Zie de zelfstudie Consoletoepassing voor een inleiding tot een [](/dotnet/csharp/tutorials/console-teleprompter) aantal functies in .NET, waaronder bestands-I/O.
[Azure.Storage.Files.Shares](/dotnet/api/azure.storage.files.shares) | Uw toepassing: <ul><li>Kan geen toegang krijgen Azure Files SMB op poort 445 vanwege firewall- of ISP-beperkingen</li><li>Vereist beheerfunctionaliteit, zoals de mogelijkheid tot het instellen van een quotum voor een bestandsshare of het maken van een Shared Access Signature (gedeelde-toegangshandtekening)</li></ul> | In dit artikel wordt beschreven hoe u voor bestands-I/O gebruik kunt maken van REST in plaats van SMB en `Azure.Storage.Files.Shares` het beheer van de bestands share.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>De consoletoepassing maken en de assembly verkrijgen

U kunt de Azure Files-clientbibliotheek gebruiken in elk type .NET-app. Deze apps omvatten Azure-cloud-, web-, desktop- en mobiele apps. In deze handleiding maken we ter vereenvoudiging een consoletoepassing.

Maak in Visual Studio een nieuwe Windows-consoletoepassing. In de volgende stappen ziet u hoe u een consoletoepassing maakt in Visual Studio 2019. De stappen zijn nagenoeg gelijk in andere versies van Visual Studio.

1. Start Visual Studio en selecteer **Een nieuw project maken**.
1. Kies **in Een nieuw project maken** de optie **Console-app (.NET Framework) voor** C# en selecteer vervolgens **Volgende.**
1. Voer **in Uw nieuwe project configureren** een naam in voor de app en selecteer **Maken.**

Voeg alle codevoorbeelden in dit artikel toe aan de `Program` klasse in het bestand *Program.cs.*

## <a name="use-nuget-to-install-the-required-packages"></a>NuGet gebruiken om de vereiste pakketten te installeren

Raadpleeg deze pakketten in uw project:

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

- [Azure-kernbibliotheek voor .NET:](https://www.nuget.org/packages/Azure.Core/)dit pakket is de implementatie van de Azure-clientpijplijn.
- [Azure Storage Blob clientbibliotheek voor .NET:](https://www.nuget.org/packages/Azure.Storage.Blobs/)dit pakket biedt programmatische toegang tot blob-resources in uw opslagaccount.
- [Azure Storage Files-clientbibliotheek voor .NET:](https://www.nuget.org/packages/Azure.Storage.Files.Shares/)dit pakket biedt programmatische toegang tot bestandsbronnen in uw opslagaccount.
- [Systeembibliotheek Configuration Manager .NET:](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/)dit pakket biedt een klasse waarin waarden worden opgeslagen en opgehaald in een configuratiebestand.

U kunt NuGet gebruiken om de pakketten te verkrijgen. Volg deze stappen:

1. Klik **in Solution Explorer** met de rechtermuisknop op uw project en kies **NuGet-pakketten beheren.**
1. In **NuGet Pakketbeheer** selecteert u **Bladeren.** Zoek en kies **vervolgens Azure.Core** en selecteer **installeren.**

   Met deze stap worden het pakket en de afhankelijkheden ervan geïnstalleerd.

1. Zoek en installeer deze pakketten:

   - **Azure.Storage.Blobs**
   - **Azure.Storage.Files.Shares**
   - **System.Configuration.ConfigurationManager**

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

- [Microsoft Azure Storage algemene bibliotheek voor .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)dit pakket biedt programmatische toegang tot algemene resources in uw opslagaccount.
- [Microsoft Azure Storage Blob-bibliotheek voor .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)dit pakket biedt programmatische toegang tot blob-resources in uw opslagaccount.
- [Microsoft Azure Storage-bestandsbibliotheek voor .NET:](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)dit pakket biedt programmatische toegang tot bestandsbronnen in uw opslagaccount.
- [Microsoft Azure Configuration Manager voor .NET:](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)dit pakket biedt een klasse voor het parseren van een connection string in een configuratiebestand, waar uw toepassing ook wordt uitgevoerd.

U kunt NuGet gebruiken om de pakketten te verkrijgen. Volg deze stappen:

1. Klik **in Solution Explorer** met de rechtermuisknop op uw project en kies **NuGet-pakketten beheren.**
1. In **NuGet Pakketbeheer** selecteert u **Bladeren.** Zoek en kies **microsoft.Azure.Storage.Blob** en selecteer **vervolgens Installeren.**

   Met deze stap worden het pakket en de afhankelijkheden ervan geïnstalleerd.
1. Zoek en installeer deze pakketten:

   - **Microsoft.Azure.Storage.Common**
   - **Microsoft.Azure.Storage.File**
   - **Microsoft.Azure.ConfigurationManager**

---

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Sla de referenties van uw opslagaccount op in het App.config bestand

Sla vervolgens uw referenties op in het bestand van *App.config* project. Dubbelklik **Solution Explorer** in het volgende voorbeeld en bewerk het bestand, zodat `App.config` het vergelijkbaar is met het volgende voorbeeld.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

Vervang `myaccount` door de naam van uw opslagaccount en door de sleutel van uw `mykey` opslagaccount.

:::code language="xml" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/app.config" highlight="5,6,7":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Vervang `myaccount` door de naam van uw opslagaccount en door de sleutel van uw `StorageAccountKeyEndingIn==` opslagaccount.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString"
      value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
  </appSettings>
</configuration>
```

---

> [!NOTE]
> De Azurite-opslagemulator biedt momenteel geen ondersteuning voor Azure Files. Uw connection string moet zich richten op een Azure-opslagaccount in de cloud om te kunnen werken met Azure Files.

## <a name="add-using-directives"></a>Using-instructies toevoegen

Open **Solution Explorer** het bestand *Program.cs* en voeg de volgende using-instructies toe aan de bovenkant van het bestand.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UsingStatements":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

---

## <a name="access-the-file-share-programmatically"></a>Via een programma toegang krijgen tot de bestandsshare

Voeg in *het bestand Program.cs* de volgende code toe om programmatisch toegang te krijgen tot de bestands share.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

Met de volgende methode maakt u een bestands share als deze nog niet bestaat. De methode begint met het maken van een [ShareClient-object](/dotnet/api/azure.storage.files.shares.shareclient) op connection string. Het voorbeeld probeert vervolgens een bestand te downloaden dat we eerder hebben gemaakt. Roep deze methode aan vanuit `Main()` .

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShare":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Voeg vervolgens de volgende inhoud toe aan de methode , na de code die hierboven wordt `Main()` weergegeven, om de volgende connection string. Met deze code wordt een verwijzing naar het bestand dat we eerder hebben gemaakt, opgeslagen en wordt de inhoud ervan uitgevoerd.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Voer de consoletoepassing uit om de uitvoer te zien.

---

## <a name="set-the-maximum-size-for-a-file-share"></a>De maximale grootte voor een bestandsshare instellen

Vanaf versie 5.x van de Azure Files-clientbibliotheek kunt u het quotum (maximale grootte) voor een bestands share instellen. U kunt ook controleren hoeveel gegevens er momenteel zijn opgeslagen in de share.

Het instellen van het quotum voor een share beperkt de totale grootte van de bestanden die op de share zijn opgeslagen. Als de totale grootte van bestanden op de share het quotum overschrijdt, kunnen clients de grootte van bestaande bestanden niet verhogen. Clients kunnen ook geen nieuwe bestanden maken, tenzij deze bestanden leeg zijn.

In onderstaand voorbeeld ziet u hoe u het huidige gebruik voor een share controleert en een quotum voor de share instelt.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_SetMaxShareSize":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

---

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Een Shared Access Signature genereren voor een bestand of bestandsshare

Vanaf versie 5.x van de Azure Files-clientbibliotheek kunt u een SHARED Access Signature (SAS) genereren voor een bestands share of voor een afzonderlijk bestand.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

De volgende voorbeeldmethode retourneert een SAS op een bestand in de opgegeven share.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_GetFileSasUri":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

U kunt ook een opgeslagen toegangsbeleid maken op een bestands share om handtekeningen voor gedeelde toegang te beheren. U wordt aangeraden een opgeslagen toegangsbeleid te maken, omdat u hiermee de SAS kunt intrekken als deze wordt aangetast. In het volgende voorbeeld wordt een opgeslagen toegangsbeleid voor een share gemaakt. In het voorbeeld wordt dat beleid gebruikt om de beperkingen op te geven voor een SAS voor een bestand in de share.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

---

Zie How a shared access signature works (Hoe een shared access signature werkt) voor meer informatie over het maken en gebruiken van handtekeningen voor [gedeelde toegang.](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)

## <a name="copy-files"></a>Bestanden kopiëren

Vanaf versie 5.x van de Azure Files-clientbibliotheek kunt u een bestand kopiëren naar een ander bestand, een bestand naar een blob of een blob naar een bestand.

U kunt AzCopy ook gebruiken om het ene bestand naar het andere te kopiëren of om een blob te kopiëren naar een bestand of andersom. Raadpleeg [Aan de slag met AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Als u een blob kopieert naar een bestand of een bestand naar een blob, moet u een Shared Access Signature (SAS) gebruiken om toegang tot het bronobject toe te staan, zelfs als u binnen hetzelfde opslagaccount kopieert.

### <a name="copy-a-file-to-another-file"></a>Een bestand kopiëren naar een ander bestand

In het volgende voorbeeld wordt een bestand gekopieerd naar een ander bestand in dezelfde share. U kunt verificatie [met gedeelde sleutels gebruiken](/rest/api/storageservices/authorize-with-shared-key) om de kopie uit te voeren, omdat met deze bewerking bestanden binnen hetzelfde opslagaccount worden gekopieerd.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFile":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

---

### <a name="copy-a-file-to-a-blob"></a>Een bestand kopiëren naar een blob

In het volgende voorbeeld wordt een bestand gemaakt en vervolgens gekopieerd naar een blob in hetzelfde opslagaccount. In het voorbeeld wordt een SAS voor het bronbestand gemaakt, die tijdens de kopieerbewerking door de service wordt gebruikt om toegang tot het bronbestand toe te staan.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFileToBlob":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

---

Op dezelfde manier kunt u een blob naar een bestand kopiëren. Als het bronobject een blob is, maakt u een SAS om tijdens de kopieerbewerking de toegang tot de blob toe te staan.

## <a name="share-snapshots"></a>Momentopnamen van shares

Vanaf versie 8.5 van de Azure Files clientbibliotheek kunt u een momentopname van een share maken. U kunt ook de lijst met momentopnamen van shares weergeven, door een lijst met momentopnamen van shares bladeren en momentopnamen van shares verwijderen. Zodra momentopnamen van shares zijn gemaakt, zijn ze alleen-lezen.

### <a name="create-share-snapshots"></a>Momentopnamen van shares maken

In het volgende voorbeeld wordt een momentopname van een bestandsshare gemaakt.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShareSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

---

### <a name="list-share-snapshots"></a>Momentopnamen van shares opvragen

In het volgende voorbeeld worden de momentopnamen op een share vermeld.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListShareSnapshots":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

---

### <a name="list-files-and-directories-within-share-snapshots"></a>Bestanden en mappen in momentopnamen van shares in een lijst bekijken

In het volgende voorbeeld worden bestanden en mappen in momentopnamen van shares gebladerd.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListSnapshotContents":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

---

### <a name="restore-file-shares-or-files-from-share-snapshots"></a>Bestands shares of bestanden herstellen vanuit momentopnamen van shares

Door een momentopname van een bestands share te maken, kunt u afzonderlijke bestanden of de volledige bestands share herstellen.

U kunt een bestand vanuit een momentopname van de bestandsshare herstellen door de momentopnamen van shares van een bestandsshare op te vragen. Vervolgens kunt u een bestand ophalen dat bij een bepaalde momentopname van een share hoort. Gebruik deze versie om het bestand rechtstreeks te lezen of te herstellen.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_RestoreFileFromSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

---

### <a name="delete-share-snapshots"></a>Momentopnamen van shares verwijderen

In het volgende voorbeeld wordt een momentopname van een bestandsshare verwijderd.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_DeleteSnapshot":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

---

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Problemen met Azure Files oplossen met behulp van metrische gegevens<a name="troubleshooting-azure-files-using-metrics"></a>

Azure Opslaganalyse ondersteunt metrische gegevens voor Azure Files. Met metrische gegevens kunt u aanvragen volgen en problemen diagnosticeren.

U kunt metrische gegevens voor Azure Files inschakelen vanuit [Azure Portal](https://portal.azure.com). U kunt metrische gegevens ook [](/rest/api/storageservices/set-file-service-properties) programmatisch inschakelen door de bewerking Eigenschappen van bestandsservice instellen aan te roepen met de REST API of een van de analogen ervan in de Azure Files-clientbibliotheek.

In het volgende codevoorbeeld ziet u hoe u de .NET-clientbibliotheek gebruikt om metrische gegevens in te Azure Files.

# <a name="azure-net-sdk-v12"></a>[Azure \. NET SDK v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UseMetrics":::

# <a name="azure-net-sdk-v11"></a>[Azure \. NET SDK v11](#tab/dotnetv11)

Voeg eerst de volgende instructies `using` toe aan het bestand *Program.cs,* samen met de instructies die u hierboven hebt toegevoegd:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Hoewel Azure Blobs, Azure Tables en Azure Queues het gedeelde type in de naamruimte gebruiken, gebruikt Azure Files een eigen type, het type in de `ServiceProperties` `Microsoft.Azure.Storage.Shared.Protocol` `FileServiceProperties` `Microsoft.Azure.Storage.File.Protocol` naamruimte. U moet echter verwijzen naar beide naamruimten uit uw code om de volgende code te compileren.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

---

Als u problemen ondervindt, raadpleegt u Problemen oplossen Azure Files [in Windows.](storage-troubleshoot-windows-file-connection-problems.md)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende resources Azure Files informatie over de gegevens:

### <a name="conceptual-articles-and-videos"></a>Conceptuele artikelen en video's

- [Azure Files: een naadloos SMB-bestandssysteem voor Windows en Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Azure Files gebruiken met Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Hulpprogramma-ondersteuning voor File Storage

- [Aan de slag met AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- [Problemen met Azure Files in Windows oplossen](./storage-troubleshoot-windows-file-connection-problems.md)

### <a name="reference"></a>Referentie

- [Azure Storage-API's voor .NET](/dotnet/api/overview/azure/storage)
- [Bestandsservice REST API](/rest/api/storageservices/File-Service-REST-API)