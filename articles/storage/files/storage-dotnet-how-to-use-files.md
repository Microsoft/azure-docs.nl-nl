---
title: Ontwikkelen voor Azure Files met .NET | Microsoft Docs
description: Meer informatie over het ontwikkelen van .NET-toepassingen en-services die gebruikmaken van Azure Files om gegevens op te slaan.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: e112060db4a44884d3094a939b03ff106ba72e65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96492196"
---
# <a name="develop-for-azure-files-with-net"></a>Ontwikkelen voor Azure Files met .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Leer de basis beginselen van het ontwikkelen van .NET-toepassingen die gebruikmaken van [Azure files](storage-files-introduction.md) om gegevens op te slaan. In dit artikel wordt beschreven hoe u een eenvoudige console toepassing maakt om het volgende te doen met .NET en Azure Files:

- De inhoud van een bestand ophalen.
- Stel de maximale grootte of het quotum voor een bestands share in.
- Een Shared Access Signature (SAS) voor een bestand maken.
- Een bestand kopiëren naar een ander bestand in hetzelfde opslagaccount.
- Een bestand kopiëren naar een blob in hetzelfde opslagaccount.
- Maak een moment opname van een bestands share.
- Een bestand herstellen vanuit een moment opname van een share.
- Gebruik Azure Storage metrische gegevens voor het oplossen van problemen.

Zie [Wat is Azure files?](storage-files-introduction.md) voor meer informatie over Azure files.

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>De .NET-API's

Azure Files biedt twee veelzijdige methoden voor het maken van clienttoepassingen: Server Message Block (SMB) en REST. Binnen .NET hebben de- `System.IO` en- `Azure.Storage.Files.Shares` api's deze methoden abstract.

API | Wanneer gebruikt u dit? | Opmerkingen
----|-------------|------
[System.IO](/dotnet/api/system.io) | Uw toepassing: <ul><li>Moet bestanden lezen/schrijven met behulp van SMB</li><li>Wordt uitgevoerd op een apparaat met toegang tot uw Azure Files-account via poort 445</li><li>Hoeft geen beheerinstellingen van de bestandsshare te beheren</li></ul> | Bestands-I/O dat is geïmplementeerd met Azure Files over SMB, is over het algemeen hetzelfde als I/O met een netwerk bestands share of een lokaal opslag apparaat. Raadpleeg de zelf studie over de [console toepassing](/dotnet/csharp/tutorials/console-teleprompter) voor een inleiding tot een aantal functies in .net, waaronder bestands-I/O.
[Azure. storage. files. shares](/dotnet/api/azure.storage.files.shares) | Uw toepassing: <ul><li>Geen toegang tot Azure Files met behulp van SMB op poort 445 vanwege firewall-of ISP-beperkingen</li><li>Vereist beheerfunctionaliteit, zoals de mogelijkheid tot het instellen van een quotum voor een bestandsshare of het maken van een Shared Access Signature (gedeelde-toegangshandtekening)</li></ul> | In dit artikel wordt het gebruik beschreven van `Azure.Storage.Files.Shares` voor bestands-I/O met behulp van rest in plaats van SMB en het beheer van de bestands share.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>De consoletoepassing maken en de assembly verkrijgen

U kunt de Azure Files-client bibliotheek in elk type .NET-app gebruiken. Deze apps zijn onder andere Azure Cloud-, Web-, desktop-en Mobile-apps. In deze hand leiding maken we een console toepassing voor eenvoud.

Maak in Visual Studio een nieuwe Windows-consoletoepassing. In de volgende stappen ziet u hoe u een consoletoepassing maakt in Visual Studio 2019. De stappen zijn nagenoeg gelijk in andere versies van Visual Studio.

1. Start Visual Studio en selecteer **Een nieuw project maken**.
1. Kies in **een nieuw project maken de** optie **console-app (.NET Framework)** voor C# en selecteer vervolgens **volgende**.
1. In **uw nieuwe project configureren** voert u een naam in voor de app en selecteert u **maken**.

Voeg alle code voorbeelden in dit artikel toe aan de `Program` klasse in het bestand *Program. cs* .

## <a name="use-nuget-to-install-the-required-packages"></a>NuGet gebruiken om de vereiste pakketten te installeren

Raadpleeg deze pakketten in uw project:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [Azure core library voor .net](https://www.nuget.org/packages/Azure.Core/): dit pakket is de implementatie van de Azure client-pijp lijn.
- [Azure Storage BLOB-client bibliotheek voor .net](https://www.nuget.org/packages/Azure.Storage.Blobs/): dit pakket biedt programmatische toegang tot BLOB-resources in uw opslag account.
- [Azure Storage files-client bibliotheek voor .net](https://www.nuget.org/packages/Azure.Storage.Files.Shares/): dit pakket biedt programmatische toegang tot bestands resources in uw opslag account.
- [Systeem Configuration Manager bibliotheek voor .net](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/): dit pakket biedt een klasse voor het opslaan en ophalen van waarden in een configuratie bestand.

U kunt NuGet gebruiken om de pakketten te verkrijgen. Volg deze stappen:

1. Klik in **Solution Explorer** met de rechter muisknop op het project en kies **NuGet-pakketten beheren**.
1. Selecteer in **NuGet package manager** de optie **Browse**. Zoek en kies **Azure. core**, en selecteer vervolgens **installeren**.

   Met deze stap installeert u het pakket en de bijbehorende afhankelijkheden.

1. Deze pakketten zoeken en installeren:

   - **Azure. storage. blobs**
   - **Azure. storage. files. shares**
   - **System.Configuration.ConfigurationManager**

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Microsoft Azure Storage algemene bibliotheek voor .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): dit pakket biedt programmatische toegang tot algemene resources in uw opslag account.
- [Microsoft Azure Storage BLOB-bibliotheek voor .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/): dit pakket biedt programmatische toegang tot BLOB-resources in uw opslag account.
- [Microsoft Azure Storage bestands bibliotheek voor .net](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/): dit pakket biedt programmatische toegang tot bestands resources in uw opslag account.
- [Microsoft Azure Configuration Manager bibliotheek voor .net](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): dit pakket biedt een klasse voor het parseren van een Connection String in een configuratie bestand, ongeacht waar de toepassing wordt uitgevoerd.

U kunt NuGet gebruiken om de pakketten te verkrijgen. Volg deze stappen:

1. Klik in **Solution Explorer** met de rechter muisknop op het project en kies **NuGet-pakketten beheren**.
1. Selecteer in **NuGet package manager** de optie **Browse**. Zoek en kies **micro soft. Azure. storage. blob** en selecteer vervolgens **installeren**.

   Met deze stap installeert u het pakket en de bijbehorende afhankelijkheden.
1. Deze pakketten zoeken en installeren:

   - **Micro soft. Azure. storage. common**
   - **Micro soft. Azure. storage. File**
   - **Microsoft.Azure.ConfigurationManager**

---

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Sla de referenties van uw opslag account op in het App.config bestand

Sla vervolgens uw referenties op in het *App.config* -bestand van uw project. In **Solution Explorer** dubbelklikt u op `App.config` het bestand en bewerkt u dit zoals in het volgende voor beeld.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Vervang door `myaccount` de naam van uw opslag account en `mykey` met de sleutel van uw opslag account.

:::code language="xml" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/app.config" highlight="5,6,7":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Vervang door `myaccount` de naam van uw opslag account en `StorageAccountKeyEndingIn==` met de sleutel van uw opslag account.

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
> De Azurite-opslag emulator biedt momenteel geen ondersteuning voor Azure Files. Uw connection string moet zijn gericht op een Azure-opslag account in de cloud om te werken met Azure Files.

## <a name="add-using-directives"></a>Using-instructies toevoegen

Open in **Solution Explorer** het bestand *Program. cs* en voeg de volgende instructies toe aan het begin van het bestand.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

---

## <a name="access-the-file-share-programmatically"></a>Via een programma toegang krijgen tot de bestandsshare

Voeg in het bestand *Program. cs* de volgende code toe om via een programma toegang te krijgen tot de bestands share.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Met de volgende methode wordt een bestands share gemaakt als deze nog niet bestaat. De methode begint met het maken van een [ShareClient](/dotnet/api/azure.storage.files.shares.shareclient) -object van een Connection String. Het voor beeld probeert vervolgens een bestand te downloaden dat u eerder hebt gemaakt. Roep deze methode vanuit aan `Main()` .

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShare":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Voeg vervolgens de volgende inhoud toe aan de `Main()` -methode, na de hierboven weer gegeven code, om de Connection String op te halen. Met deze code wordt een verwijzing opgehaald naar het bestand dat we eerder hebben gemaakt en de inhoud ervan wordt uitgevoerd.

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

Vanaf versie 5. x van de Azure Files-client bibliotheek kunt u het quotum (maximum grootte) voor een bestands share instellen. U kunt ook controleren hoeveel gegevens er momenteel zijn opgeslagen in de share.

Als het quotum voor een share wordt ingesteld, wordt de totale grootte beperkt van de bestanden die zijn opgeslagen op de share. Als de totale grootte van bestanden op de share het quotum overschrijdt, kunnen clients de omvang van bestaande bestanden niet verg Roten. Clients kunnen ook geen nieuwe bestanden maken, tenzij deze bestanden leeg zijn.

In onderstaand voorbeeld ziet u hoe u het huidige gebruik voor een share controleert en een quotum voor de share instelt.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_SetMaxShareSize":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

Vanaf versie 5. x van de Azure Files-client bibliotheek kunt u een Shared Access Signature (SAS) voor een bestands share of voor een afzonderlijk bestand genereren.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Met de volgende voorbeeld methode wordt een SAS voor een bestand in de opgegeven share geretourneerd.

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_GetFileSasUri":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

U kunt ook een opgeslagen toegangs beleid maken op een bestands share om hand tekeningen voor gedeelde toegang te beheren. We raden u aan om een opgeslagen toegangs beleid te maken, omdat u hiermee de SAS kunt intrekken als deze wordt aangetast. In het volgende voor beeld wordt een opgeslagen toegangs beleid gemaakt op een share. In het voor beeld wordt dat beleid gebruikt om de beperkingen voor een SAS op een bestand in de share op te geven.

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

Zie [How a Shared Access Signature](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)(Engelstalig) voor meer informatie over het maken en gebruiken van hand tekeningen voor gedeelde toegang.

## <a name="copy-files"></a>Bestanden kopiëren

Vanaf versie 5. x van de Azure Files-client bibliotheek kunt u een bestand kopiëren naar een ander bestand, een bestand naar een BLOB of een BLOB naar een bestand.

U kunt AzCopy ook gebruiken om één bestand naar een andere te kopiëren of een BLOB naar een bestand of een andere manier te kopiëren. Raadpleeg [Aan de slag met AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Als u een blob kopieert naar een bestand of een bestand naar een blob, moet u een Shared Access Signature (SAS) gebruiken om toegang tot het bronobject toe te staan, zelfs als u binnen hetzelfde opslagaccount kopieert.

### <a name="copy-a-file-to-another-file"></a>Een bestand kopiëren naar een ander bestand

In het volgende voorbeeld wordt een bestand gekopieerd naar een ander bestand in dezelfde share. U kunt verificatie met behulp van [gedeelde sleutels](/rest/api/storageservices/authorize-with-shared-key) gebruiken om de kopie uit te voeren, omdat deze bewerking bestanden binnen hetzelfde opslag account kopieert.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFile":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CopyFileToBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

Vanaf versie 8,5 van de Azure Files-client bibliotheek kunt u een moment opname van een share maken. U kunt ook de lijst met momentopnamen van shares weergeven, door een lijst met momentopnamen van shares bladeren en momentopnamen van shares verwijderen. Na het maken zijn moment opnamen delen alleen-lezen.

### <a name="create-share-snapshots"></a>Momentopnamen van shares maken

In het volgende voorbeeld wordt een momentopname van een bestandsshare gemaakt.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_CreateShareSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

---

### <a name="list-share-snapshots"></a>Momentopnamen van shares opvragen

In het volgende voor beeld worden de moment opnamen op een share weer gegeven.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListShareSnapshots":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

---

### <a name="list-files-and-directories-within-share-snapshots"></a>Bestanden en mappen in moment opnamen van shares weer geven

In het volgende voor beeld worden bestanden en mappen in moment opnamen van shares gebladerd.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_ListSnapshotContents":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

---

### <a name="restore-file-shares-or-files-from-share-snapshots"></a>Bestands shares of bestanden terugzetten van moment opnamen van shares

Door een moment opname van een bestands share te maken, kunt u afzonderlijke bestanden of de volledige bestands share herstellen.

U kunt een bestand vanuit een momentopname van de bestandsshare herstellen door de momentopnamen van shares van een bestandsshare op te vragen. Vervolgens kunt u een bestand ophalen dat hoort bij een bepaalde share-moment opname. Deze versie gebruiken om het bestand direct te lezen of te herstellen.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_RestoreFileFromSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_DeleteSnapshot":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

---

## <a name="troubleshoot-azure-files-by-using-metrics"></a>Problemen met Azure Files oplossen met metrische gegevens<a name="troubleshooting-azure-files-using-metrics"></a>

Azure Opslaganalyse ondersteunt metrische gegevens voor Azure Files. Met metrische gegevens kunt u aanvragen volgen en problemen diagnosticeren.

U kunt de metrische gegevens voor Azure Files inschakelen vanuit de [Azure Portal](https://portal.azure.com). U kunt metrische gegevens ook programmatisch in te scha kelen door de bewerking [set file Service Properties](/rest/api/storageservices/set-file-service-properties) aan te roepen met de rest API of een van de analogen in de Azure files-client bibliotheek.

In het volgende code voorbeeld ziet u hoe u de .NET-client bibliotheek gebruikt om metrische gegevens in te scha kelen voor Azure Files.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/files/howto/dotnet/dotnet-v12/FileShare.cs" id="snippet_UseMetrics":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Voeg eerst de volgende- `using` instructies toe aan het bestand *Program. cs* , samen met de regels die u hierboven hebt toegevoegd:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Hoewel Azure-blobs, Azure-tabellen en Azure-wacht rijen gebruikmaken van het gedeelde `ServiceProperties` type in de `Microsoft.Azure.Storage.Shared.Protocol` naam ruimte, Azure files gebruikt een eigen type, het `FileServiceProperties` type in de `Microsoft.Azure.Storage.File.Protocol` naam ruimte. U moet vanuit uw code naar beide naam ruimten verwijzen om de volgende code te kunnen compileren.

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

Als u problemen ondervindt, kunt u verwijzen naar [Azure files problemen oplossen in Windows](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen voor meer informatie over Azure Files:

### <a name="conceptual-articles-and-videos"></a>Conceptuele artikelen en video's

- [Azure Files: een naadloos SMB-bestandssysteem voor Windows en Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
- [Azure Files gebruiken met Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Hulpprogramma-ondersteuning voor File Storage

- [Aan de slag met AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
- [Problemen met Azure Files in Windows oplossen](./storage-troubleshoot-windows-file-connection-problems.md)

### <a name="reference"></a>Naslaginformatie

- [Azure Storage-API's voor .NET](/dotnet/api/overview/azure/storage)
- [Bestandsservice REST API](/rest/api/storageservices/File-Service-REST-API)