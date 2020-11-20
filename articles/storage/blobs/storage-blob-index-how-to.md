---
title: BLOB-index Tags gebruiken om gegevens te beheren en te zoeken op Azure Blob Storage
description: Zie voor beelden van het gebruik van BLOB-index Tags voor het categoriseren, beheren en opvragen van blob-objecten.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/19/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e3e16b71d52edd9ab4eaf55651567b95e334b84
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961784"
---
# <a name="use-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Gebruik BLOB index Tags (preview) om gegevens te beheren en te zoeken op Azure Blob Storage

Met Blob-index Tags worden gegevens in uw opslag account gecategoriseerd met behulp van code kenmerken van de sleutel waarde. Deze tags worden automatisch geïndexeerd en weer gegeven als een Doorzoek bare multi-dimensionale index om eenvoudig gegevens te vinden. Dit artikel laat u zien hoe u gegevens kunt instellen, ophalen en zoeken met behulp van BLOB-index Tags.

> [!NOTE]
> BLOB-index bevindt zich in de open bare preview en is beschikbaar in de regio's **Canada-centraal**, **Canada-Oost**, **Frankrijk-centraal** en **Frankrijk-Zuid** . Zie [Azure Blob-gegevens beheren en zoeken met Blob-index Tags (preview)](storage-manage-find-blobs.md)voor meer informatie over deze functie, samen met bekende problemen en beperkingen.

## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/azure-portal)

- Een Azure-abonnement dat is geregistreerd en goedgekeurd voor toegang tot de BLOB-index preview
- Toegang tot de [Azure Portal](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)

Als blob-index is beschikbaar in preview, wordt het .NET-opslag pakket vrijgegeven in de preview-NuGet-feed. Deze bibliotheek is onderhevig aan wijzigingen tijdens de preview-periode.

1. Stel uw Visual Studio-project in om aan de slag te gaan met de Azure Blob Storage-client bibliotheek V12 voor .NET. Zie [.net Quick](storage-quickstart-blobs-dotnet.md) start (Engelstalig) voor meer informatie.

2. Zoek in de NuGet package manager het pakket **Azure. storage. blobs** en Installeer versie **12.7.0-Preview. 1** of hoger voor uw project. U kunt ook de Power shell-opdracht uitvoeren: `Install-Package Azure.Storage.Blobs -Version 12.7.0-preview.1`

   Zie [een pakket zoeken en installeren](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)voor meer informatie.

3. Voeg de volgende using-instructies toe aan de bovenkant van het code bestand.

    ```csharp
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    using Azure.Storage.Blobs.Specialized;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

---

## <a name="upload-a-new-blob-with-index-tags"></a>Een nieuwe BLOB uploaden met index Tags

Deze taak kan worden uitgevoerd door een [gegevens eigenaar](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) van de opslag-BLOB of een beveiligingsprincipal die toestemming heeft gekregen voor de bewerking van de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure-resource provider](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) via een aangepaste Azure-rol.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer uw opslag account in de [Azure Portal](https://portal.azure.com/) 

2. Navigeer naar de optie **containers** onder **BLOB service** en selecteer uw container

3. Selecteer de knop **uploaden** en blader door het lokale bestands systeem om een bestand te vinden dat u wilt uploaden als een blok-blob.

4. Vouw de vervolg keuzelijst **Geavanceerd** uit en ga naar de sectie **index Tags voor BLOB**

5. Voer de tags voor de BLOB-index van de sleutel/waarde in die u wilt Toep assen op uw gegevens

6. Selecteer de knop **uploaden** om de BLOB te uploaden

:::image type="content" source="media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png" alt-text="Scherm afbeelding van de Azure Portal waarin wordt weer gegeven hoe u een blob met index Tags uploadt.":::

# <a name="net"></a>[.NET](#tab/net)

In het volgende voor beeld ziet u hoe u een toevoeg-BLOB maakt met tags die zijn ingesteld tijdens het maken.

```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");

          // Blob index tags to upload
          AppendBlobCreateOptions appendOptions = new AppendBlobCreateOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Labels voor BLOB-indexen ophalen, instellen en bijwerken

Het ophalen van BLOB-index Tags kan worden uitgevoerd door een [gegevens eigenaar](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) van de opslag-BLOB of een beveiligingsprincipal die toestemming heeft gekregen voor de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` [Azure resource provider-bewerking](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) via een aangepaste Azure-rol.

Het instellen en bijwerken van BLOB-index Tags kan worden uitgevoerd door een [gegevens eigenaar](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) van de opslag-BLOB of een beveiligingsprincipal die machtigingen heeft gekregen voor de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [Azure resource provider-bewerking](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) via een aangepaste Azure-rol.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer uw opslag account in de [Azure Portal](https://portal.azure.com/) 

2. Navigeer naar de optie **containers** onder **BLOB-service**, selecteer uw container

3. Selecteer uw Blob in de lijst met blobs in de geselecteerde container

4. Op het tabblad Overzicht van BLOB worden de eigenschappen van uw BLOB weer gegeven, inclusief alle **BLOB-index Tags**

5. U kunt de index Tags voor sleutel/waarde voor uw BLOB ophalen, instellen, wijzigen of verwijderen

6. Selecteer de knop **Opslaan** om de updates voor uw BLOB te bevestigen

:::image type="content" source="media/storage-blob-index-concepts/blob-index-get-set-tags.png" alt-text="Scherm afbeelding van de Azure Portal waarin wordt weer gegeven hoe index Tags op blobs worden opgehaald, ingesteld, bijgewerkt en verwijderd.":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Gegevens filteren en vinden met Blob-index Tags

Deze taak kan worden uitgevoerd door een [gegevens eigenaar](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) van de opslag-BLOB of een beveiligingsprincipal die toestemming heeft gekregen voor de bewerking van de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` [Azure-resource provider](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) via een aangepaste Azure-rol.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Binnen het Azure Portal past het filter index Tags van blob automatisch de `@container` para meter toe om de geselecteerde container te bereiken. Als u gelabelde gegevens in uw hele opslag account wilt filteren en zoeken, gebruikt u onze REST API, Sdk's of hulpprogram ma's.

1. Selecteer uw opslag account in de [Azure Portal](https://portal.azure.com/). 

2. Navigeer naar de optie **containers** onder **BLOB service** en selecteer uw container

3. Selecteer de knop voor het **filteren van BLOB-index Tags** om binnen de geselecteerde container te filteren

4. Voer een BLOB-index code sleutel en label waarde in

5. Selecteer de knop voor het **filteren van BLOB-index Tags** om extra label filters toe te voegen (Maxi maal 10)

:::image type="content" source="media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png" alt-text="Scherm afbeelding van de Azure Portal waarin wordt weer gegeven hoe gecodeerde blobs worden gefilterd en gevonden met index Tags":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<TaggedBlobItem> blobs = new List<TaggedBlobItem>();
          await foreach (TaggedBlobItem taggedBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(taggedBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Levenscyclus beheer met filters voor BLOB-index codes

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer uw opslag account in de [Azure Portal](https://portal.azure.com/). 

2. Ga naar de optie **levenscyclus beheer** onder **BLOB-service**

3. Selecteer *regel toevoegen* en vul vervolgens de velden voor de actie sets in

4. **Filterset** selecteren om een optioneel filter toe te voegen voor voorvoegsel overeenkomst en overeenkomende BLOB-index

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png" alt-text="Scherm afbeelding van de Azure Portal waarin wordt weer gegeven hoe index Tags voor levenscyclus beheer moeten worden toegevoegd.":::

5. Selecteer **controleren + toevoegen** om de regel instellingen te controleren

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png" alt-text="Scherm afbeelding van de Azure Portal een levenscyclus beheer regel met een filter voor de BLOB index Tags weer geven":::

6. Selecteer **toevoegen** om de nieuwe regel toe te passen op het levenscyclus beheer beleid

# <a name="net"></a>[.NET](#tab/net)

Het [levenscyclus beheer](storage-lifecycle-management-concepts.md) beleid wordt voor elk opslag account toegepast op het niveau van het controle vlak. Voor .NET installeert u de [Microsoft Azure Management Storage-bibliotheek](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) versie 16.0.0 of hoger.

---

## <a name="next-steps"></a>Volgende stappen

 - Zie [Azure Blob-gegevens beheren en zoeken met Blob-index Tags (preview)](storage-manage-find-blobs.md ) voor meer informatie over blob-index Tags.
 - Zie [de Azure Blob Storage levenscyclus beheren](storage-lifecycle-management-concepts.md) voor meer informatie over levenscyclus beheer.
