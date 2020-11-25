---
title: 'Quickstart: Azure Blob Storage-clientbibliotheek voor .NET'
description: In deze quickstart leert u hoe u de Azure Blob Storage-clientbibliotheek voor .NET kunt gebruiken om een container te maken en een blob-in-blob-opslag (object). Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: effb193e54be7331c9dc2874a4a34ef55442ed30
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96021738"
---
# <a name="quickstart-azure-blob-storage-client-library-v11-for-net"></a>Quickstart: Azure Blob Storage-clientbibliotheek v11 voor .NET

Ga aan de slag met de Azure Blob Storage-clientbibliotheek v11 voor .NET. Azure Blob Storage is de oplossing voor opslag van objecten in de cloud van Microsoft. Volg de stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens.

> [!NOTE]
> In deze snelstart wordt gebruikgemaakt van een oudere versie van de Azure Blob Storage-clientbibliotheek. Als u aan de slag wilt gaan met de vorige versie, raadpleegt u [Quickstart: Azure Blob Storage-clientbibliotheek v12 voor .NET](storage-quickstart-blobs-dotnet.md).

Gebruik de Azure Blob Storage-clientbibliotheek voor .NET voor het volgende:

* Een container maken
* Machtigingen instellen voor een container
* Een blob maken in Azure Storage
* De blob downloaden op uw lokale computer
* Alle blobs in een container weergeven
* Een container verwijderen

Aanvullende bronnen:

* [API-referentiedocumentatie](/dotnet/api/overview/azure/storage)
* [Broncode van bibliotheek](https://github.com/Azure/azure-storage-net/tree/master/Blob)
* [Pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)
* [Voorbeelden](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
* Azure Storage-account: [maak een opslagaccount](../common/storage-account-create.md)
* De huidige versie van de [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturingssysteem. Zorg ervoor dat u de SDK en niet de runtime ophaalt.

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Blob Storage-clientbibliotheek voor .NET.

### <a name="create-the-project"></a>Het project maken

Maak eerst een .NET Core-toepassing met de naam *blob-quickstart*.

1. Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam *blob-quickstart*. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: *Program.cs*.

   ```console
   dotnet new console -n blob-quickstart
   ```

2. Ga naar de zojuist gemaakte map *blob-quickstart* en bouw de app om te controleren of alles klopt.

   ```console
   cd blob-quickstart
   ```

   ```console
   dotnet build
   ```

De verwachte uitvoer van de build moet er ongeveer als volgt uitzien:

```output
C:\QuickStarts\blob-quickstart> dotnet build
Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 44.31 ms for C:\QuickStarts\blob-quickstart\blob-quickstart.csproj.
  blob-quickstart -> C:\QuickStarts\blob-quickstart\bin\Debug\netcoreapp2.1\blob-quickstart.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:03.08
```

### <a name="install-the-package"></a>Het pakket installeren

Blijf in de toepassingsmap en installeer de Azure Blob Storage-clientbibliotheek voor het .NET-pakket met behulp van de opdracht `dotnet add package`.

```console
dotnet add package Microsoft.Azure.Storage.Blob
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Open het bestand *Program.cs* in uw editor
2. Verwijder de instructie `Console.WriteLine`
3. Voeg `using`-instructies toe
4. Maak een `ProcessAsync`-methode waar de hoofdcode van het voorbeeld wordt opgeslagen
5. Roep de methode `ProcessAsync` asynchroon aan vanuit `Main`

Hier volgt de code:

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

namespace blob_quickstart
{
    class Program
    {
        public static async Task Main()
        {
            Console.WriteLine("Azure Blob Storage - .NET quickstart sample\n");

            await ProcessAsync();

            Console.WriteLine("Press any key to exit the sample application.");
            Console.ReadLine();
        }

        private static async Task ProcessAsync()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopieer uw referenties van de Azure Portal

Wanneer met de voorbeeldtoepassing een aanvraag wordt ingediend bij Azure Storage, moet deze aanvraag worden geautoriseerd. Om een aanvraag te autoriseren voegt u de referenties van uw opslagaccount toe als een verbindingsreeks. U kunt de referenties van het opslagaccount weergeven door de volgende stappen te volgen:

1. Navigeer naar [Azure Portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. In de sectie **Instellingen** van het overzicht met opslagaccounts selecteert u **Toegangssleutels**. Hier worden de toegangssleutels van uw account weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Verbindingsreeks** onder **key1** en selecteer de knop **Kopiëren** om de verbindingsreeks te kopiëren. U gaat in de volgende stap de waarde voor de verbinding toevoegen aan een omgevingsvariabele.

    ![Schermopname waarin een verbindingsreeks vanuit de Azure-portal wordt gekopieerd](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

Nadat u de verbindingsreeks hebt gekopieerd, schrijft u deze naar een nieuwe omgevingsvariabele op de lokale computer waarop de toepassing wordt uitgevoerd. Als u de omgevingsvariabele wilt instellen, opent u een consolevenster en volgt u de aanwijzingen voor uw besturingssysteem. Vervang `<yourconnectionstring>` door de feitelijke verbindingsreeks.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

Nadat u de omgevingsvariabele in Windows hebt toegevoegd, moet u een nieuw exemplaar van het opdrachtvenster starten.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>MacOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

Nadat u de omgevingsvariabele hebt toegevoegd, start u actieve programma's die de omgevingsvariabele moeten lezen, opnieuw. Start bijvoorbeeld uw ontwikkelomgeving of editor opnieuw voordat u doorgaat.

## <a name="object-model"></a>Objectmodel

Azure Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslagaccount.
* Een container in het opslagaccount
* Een blob in een container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-quickstart-blobs-dotnet/blob1.png)

Gebruik de volgende .NET-klassen om te communiceren met deze resources:

* [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount): De klasse `CloudStorageAccount` vertegenwoordigt uw Azure Storage-account. Gebruik deze klasse om toegang tot Blob Storage te autoriseren met behulp van de toegangssleutels van uw account.
* [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient): De klasse `CloudBlobClient` biedt een toegangspunt naar de Blob service in uw code.
* [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer): De klasse `CloudBlobContainer` vertegenwoordigt een blobcontainer in uw code.
* [CloudBlockBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob): Het object `CloudBlockBlob` vertegenwoordigt een blok-blob in uw code. Blok-blobs bestaan uit blokken met gegevens die afzonderlijk kunnen worden beheerd.

## <a name="code-examples"></a>Codevoorbeelden

Deze voorbeeldcodefragmenten laten zien hoe u de volgende acties kunt uitvoeren met de Azure Blob Storage-clientbibliotheek voor .NET:

   * [De client verifiëren](#authenticate-the-client)
   * [Een container maken](#create-a-container)
   * [Machtigingen instellen voor een container](#set-permissions-on-a-container)
   * [Blobs uploaden naar een container](#upload-blobs-to-a-container)
   * [De blobs in een container weergeven](#list-the-blobs-in-a-container)
   * [Blobs downloaden](#download-blobs)
   * [Container verwijderen](#delete-a-container)

### <a name="authenticate-the-client"></a>De client verifiëren

Met de onderstaande code wordt gecontroleerd of de omgevingsvariabele een verbindingsreeks bevat die kan worden geparseerd om een [CloudStorageAccount](/dotnet/api/microsoft.azure.storage.cloudstorageaccount)-object te maken dat naar het opslagaccount wijst. Gebruik de [TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse)-methode om te controleren of de verbindingsreeks geldig is. Als `TryParse` is geslaagd, wordt de variabele `storageAccount` geïnitialiseerd en wordt `true` geretourneerd.

Voeg deze code toe in de methode `ProcessAsync`:

```csharp
// Retrieve the connection string for use with the application. The storage 
// connection string is stored in an environment variable on the machine 
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the 
// environment variable is created after the application is launched in a 
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");

// Check whether the connection string can be parsed.
CloudStorageAccount storageAccount;
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob
    // storage here.
    // ADD OTHER OPERATIONS HERE
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'AZURE_STORAGE_CONNECTION_STRING' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the application.");
    Console.ReadLine();
}
```

> [!NOTE]
> Als u de rest van de bewerkingen in dit artikel wilt uitvoeren, vervangt u `// ADD OTHER OPERATIONS HERE` in de bovenstaande code door de codefragmenten in de volgende secties.

### <a name="create-a-container"></a>Een container maken

Maak om de container te maken eerst een instantie van het object [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient), dat naar de Blob-opslag in uw opslagaccount wijst. Maak vervolgens een instantie van het object [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer) en maak de container.

In dit geval wordt met de code de methode [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync) aangeroepen om de container te maken. Er wordt een GUID-waarde toegevoegd aan de containernaam om ervoor te zorgen dat deze uniek is. In een productieomgeving is het vaak beter om de methode [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync) te gebruiken om alleen een container te maken als deze nog niet bestaat.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

```csharp
// Create the CloudBlobClient that represents the 
// Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and 
// append a GUID value to it to make the name unique.
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs" + 
        Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();
```

### <a name="set-permissions-on-a-container"></a>Machtigingen instellen voor een container

Stel machtigingen in voor de container zodat alle blobs in de container openbaar zijn. Als een blob openbaar is, is deze anoniem toegankelijk voor elke client.

```csharp
// Set the permissions so the blobs are public.
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Met het volgende codefragment wordt een verwijzing naar een `CloudBlockBlob`-object opgehaald door de methode [GetBlockBlobReference](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getblockblobreference) aan te roepen in de container die in de vorige sectie is gemaakt. Vervolgens wordt het geselecteerde lokale bestand naar de blob geüpload door de methode [UploadFromFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromfileasync) aan te roepen. Met deze methode wordt de blob gemaakt als deze nog niet bestaat, of overschreven als dat wel het geval is.

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Geef de blobs in de container weer met behulp van de methode [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync). In het geval is slechts één blob aan de container toegevoegd, zodat met de weergavebewerking alleen die ene blob wordt geretourneerd.

Als er te veel blobs zijn om met één aanroep te retourneren (standaard meer dan 5.000), retourneert de methode `ListBlobsSegmentedAsync` een segment van de totale resultatenset en een vervolgtoken. Om het volgende segment met blobs op te halen, geeft u het vervolgtoken op dat door de vorige aanroep is geretourneerd, enzovoort, totdat het vervolgtoken null is. Een null-vervolgtoken geeft aan dat alle blobs zijn opgehaald. De code laat best practices zien voor het gebruik van het vervolgtoken.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null); // Loop while the continuation token is not null.

```

### <a name="download-blobs"></a>Blobs downloaden

Download de eerder gemaakte blob in uw lokale bestandssysteem met behulp van de methode [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync). De voorbeeldcode voegt het achtervoegsel _DOWNLOADED toe aan de naam van de blob, zodat u beide bestanden in het lokale bestandssysteem kunt zien.

```csharp
// Download the blob to a local file, using the reference created earlier.
// Append the string "_DOWNLOADED" before the .txt extension so that you 
// can see both files in MyDocuments.
string destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);
```

### <a name="delete-a-container"></a>Een container verwijderen

De volgende code ruimt de resources die door de app zijn gemaakt op door de hele container te verwijderen met behulp van [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). U kunt ook de lokale bestanden verwijderen als u dat wilt.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the example files, " +
    "example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app wordt een testbestand gemaakt in uw lokale map *Mijn documenten* en geüpload naar de Blob-opslag. Vervolgens wordt een lijst gemaakt van de blobs in de container en wordt het bestand gedownload met een nieuwe naam, zodat u het oude en nieuwe bestand kunt vergelijken.

Navigeer naar de toepassingsmap, bouw de toepassing en voer deze uit.

```console
dotnet build
```

```console
dotnet run
```

De uitvoer van de app lijkt op die in het volgende voorbeeld:

```output
Azure Blob storage - .NET Quickstart example

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-96
97c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f
4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-
958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926
-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the example files and example container.
```

Wanneer u op **Enter** drukt, worden de opslagcontainer en de bestanden verwijderd. Controleer voordat u doorgaat of de map *Mijn documenten* de twee bestanden bevat. Als u ze opent, ziet u dat ze identiek zijn. Kopieer de URL van de blob in het consolevenster en plak deze in een browser om de inhoud van de blob weer te geven.

Nadat u de bestanden hebt gecontroleerd, drukt u op een willekeurige toets om de demo te voltooien en de testbestanden te verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u blobs kunt uploaden, downloaden en er een lijst van maken met behulp van .NET.

Als u wilt leren hoe u een web-app maakt om een afbeelding te uploaden naar Blob-opslag, gaat u door naar:

> [!div class="nextstepaction"]
> [Een afbeelding uploaden en verwerken](storage-upload-process-images.md)

* Zie voor meer informatie over .NET Core [Aan de slag met .NET in 10 minuten](https://www.microsoft.com/net/learn/get-started/).
* Zie [.NET Photo Gallery Web Application Sample with Azure Blob Storage](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/) voor meer informatie over een voorbeeld van een toepassing die u kunt implementeren vanuit Visual Studio voor Windows.