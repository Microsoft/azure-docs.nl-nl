---
title: 'Snelstartgids: Azure Blob Storage bibliotheek V12-Xamarin'
description: In deze Quick Start leert u hoe u de Azure Blob Storage-client bibliotheek versie 12 kunt gebruiken met Xamarin om een container en een BLOB in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw mobiele apparaat downloadt en hoe u alle blobs in een container kunt weergeven.
author: codemillmatt
ms.author: masoucou
ms.date: 10/09/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: dab938a526aa89f6fe5a014e10869bd8da8b475d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98802352"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>Snelstartgids: Azure Blob Storage-client bibliotheek V12 met Xamarin

Ga aan de slag met de Azure Blob Storage-client bibliotheek V12 met Xamarin. Azure Blob Storage is de oplossing voor opslag van objecten in de cloud van Microsoft. Volg de stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens.

Gebruik de Azure Blob Storage-client bibliotheek V12 met Xamarin om het volgende te doen:

* Een container maken
* Een blob uploaden naar Azure Storage
* Alle blobs in een container weergeven
* De blob downloaden op uw apparaat
* Een container verwijderen

Naslagkoppelingen:

* [API-referentiedocumentatie](/dotnet/api/azure.storage.blobs)
* [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Pakket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Voorbeeld](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
* Azure Storage-account: [maak een opslagaccount](../common/storage-account-create.md)
* Visual Studio waarop [Mobile Development voor .NET-workloads](/xamarin/get-started/installation/?pivots=windows) geïnstalleerd us of [Visual Studio voor Mac](/visualstudio/mac/installation?view=vsmac-2019)

## <a name="setting-up"></a>Instellen
    
In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Blob Storage-client bibliotheek V12 met Xamarin.
    
### <a name="create-the-project"></a>Het project maken

1. Open Visual Studio en maak een Blank Forms App.
1. Noem deze: BlobQuickstartV12

### <a name="install-the-package"></a>Het pakket installeren

1. Klik in het deelvenster Solution Explorer met de rechtermuisknop op uw oplossing en kies **NuGet-pakketten beheren voor oplossing**.
1. Zoek **Azure.Storage.Blobs** en installeer de meest recente stabiele versie in alle projecten in uw oplossing.

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Vanuit de map **BlobQuickstartV12** map:

1. Open het bestand *MainPage.xaml* in uw editor
1. Verwijder alles dat tussen de `<ContentPage></ContentPage>`-elementen staat en vervang dit door het onderstaande:

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslagaccount
* Een container in het opslagaccount
* Een blob in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blobs-introduction/blob1.png)

Gebruik de volgende .NET-klassen om te communiceren met deze resources:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): Met de klasse `BlobServiceClient` kunt u Azure Storage-resources en blob-containers bewerken.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): Met de klasse `BlobContainerClient` kunt u Azure Storage-containers en de bijbehorende blobs bewerken.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): Met de klasse `BlobClient` kunt u Azure Storage-blobs bewerken.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): De klasse `BlobDownloadInfo` vertegenwoordigt de eigenschappen en inhoud die worden geretourneerd door het downloaden van een blob.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeld code fragmenten ziet u hoe u de volgende taken kunt uitvoeren met de Azure Blob Storage-client bibliotheek voor .NET in een Xamarin. Forms-app:

* [Variabelen op klasseniveau maken](#create-class-level-variables)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container weergeven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Container verwijderen](#delete-a-container)

### <a name="create-class-level-variables"></a>Variabelen op klasseniveau maken

De onderstaande code declareert verschillende variabelen op klasseniveau. Ze moeten in de rest van dit voor beeld communiceren met Azure Blob Storage.

Deze komen bij de verbindingsreeks voor het opslagaccount die is ingesteld in de sectie [De opslagverbindingsreeks configureren](#configure-your-storage-connection-string).

Voeg deze code toe als variabelen op klasseniveau in het bestand *MainPage.xaml.cs*:

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>Een container maken

Verzin een naam voor de nieuwe container. Met de onderstaande code wordt een GUID-waarde aan de containernaam toegevoegd om te verzekeren dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Een instantie van de klasse [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) maken. Roep vervolgens de methode [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) aan om de container in uw opslagaccount te maken.

Voeg deze code toe aan het bestand *MainPage.xaml.cs*:

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende codefragment:

1. Maakt een `MemoryStream` met tekst.
1. Uploadt de tekst naar een Blob door de functie [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) van de klasse [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) aan te roepen, en deze zowel in de bestandsnaam als de `MemoryStream` met tekst door te geven. Met deze methode wordt de blob gemaakt als deze nog niet bestaat, of overschreven als dat wel het geval is.

Voeg deze code toe aan het bestand *MainPage.xaml.cs*:

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Hiermee worden de blobs in de container weergegeven door de methode [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) aan te roepen. In dit geval is slechts één blob aan de container toegevoegd, zodat met de weergavebewerking alleen die ene blob wordt geretourneerd.

Voeg deze code toe aan het bestand *MainPage.xaml.cs*:

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>Blobs downloaden

Download de eerder gemaakte blob door de methode [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) aan te roepen. De voorbeeldcode kopieert de `Stream`-weergave van de blob eerst naar een `MemoryStream` en vervolgens naar een `StreamReader` zodat de tekst kan worden weergegeven.

Voeg deze code toe aan het bestand *MainPage.xaml.cs*:

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>Een container verwijderen

Met de volgende code worden de resources opgeruimd die door de app zijn gemaakt door de hele container te verwijderen met behulp van [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync).

De app vraagt eerst om bevestiging voordat de blob en de container worden verwijderd. Dit is een goede gelegenheid om te controleren dat de resources correct zijn gemaakt, voordat ze worden verwijderd.

Voeg deze code toe aan het bestand *MainPage.xaml.cs*:

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>De code uitvoeren

Wanneer de app wordt opgestart, maakt deze eerst de container zoals deze wordt weergegeven. Vervolgens klikt u op de knoppen om de blobs te uploaden, weer te geven en te downloaden en de container te verwijderen.

Druk op F5 om de app uit te voeren in Windows. Druk op Cmd + Enter om de app uit te voeren op Mac.

De app schrijft na elke bewerking naar het scherm. De uitvoer van de app lijkt op die in het onderstaande voorbeeld:

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

Voordat u begint met het opschonen, controleert u of de uitvoer van de inhoud van de blob op het scherm overeenkomt met de geüploade waarde.

Nadat u de waarden hebt gecontroleerd, bevestigt u de vraag om de container te verwijderen en voltooit u de demo.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u blobs kunt uploaden, downloaden en vermelden met behulp van Azure Blob Storage client library V12 met Xamarin.

Als u voorbeeld-apps voor Blob-opslag wilt zien, ga dan naar:

> [!div class="nextstepaction"]
> [Voor beeld van Azure Blob Storage SDK V12 Xamarin](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* Ga naar [Azure voor mobiele ontwikkelaars](/azure/mobile-apps) voor zelfstudies, voorbeelden, quickstarts en andere documentatie.
* Bekijk [Aan de slag met Xamarin](/xamarin/get-started/)voor meer informatie over Xamarin.
