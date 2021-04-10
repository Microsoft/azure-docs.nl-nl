---
title: Video beoordelingen maken met behulp van .NET-Content Moderator
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u informatie en code voorbeelden waarmee u snel aan de slag kunt gaan met behulp van de Content Moderator SDK met C# om video beoordelingen te maken.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 55802b04e10f06c7e362033f97d24c4e20c12c62
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566486"
---
# <a name="create-video-reviews-using-net"></a>Video beoordelingen maken met .NET

In dit artikel vindt u informatie en code voorbeelden waarmee u snel aan de slag kunt met de [Content moderator SDK met C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) voor het volgende:

- Een video-beoordeling maken voor menselijke moderators
- Frames toevoegen aan een beoordeling
- De frames voor de beoordeling ophalen
- De status en Details van de beoordeling ophalen
- De beoordeling publiceren

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account op de site van het Content Moderator [controle programma](https://contentmoderator.cognitive.microsoft.com/) .
- In dit artikel wordt ervan uitgegaan dat u [de video hebt gematigd (zie Quick Start)](video-moderation-api.md) en de antwoord gegevens bevat. U hebt deze nodig voor het maken van op frames gebaseerde Beoordelingen voor menselijke moderators.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Instellen dat uw API-sleutel de beoordelings-API kan aanroepen voor het maken van de beoordeling

Nadat u de vorige stappen hebt uitgevoerd, zou u twee Content Moderator-sleutels kunnen hebben als u vanuit de Azure-portal bent gestart.

Als u van plan bent de door Azure verstrekte API-sleutel in uw SDK-voorbeeld te gebruiken, volgt u de stappen in de sectie [De Azure-sleutel met de beoordelings-API gebruiken](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) zodat uw app de beoordelings-API kan aanroepen en beoordelingen kan maken.

Als u de gratis proefversie van de sleutel gebruikt die wordt gegenereerd door het beoordelingsprogramma, is uw account van het beoordelingsprogramma al op de hoogte van de sleutel, zodat er geen extra stappen zijn vereist.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Uw video en video frames voorbereiden voor beoordeling

De video-en voorbeeld video frames die moeten worden beoordeeld, moeten online worden gepubliceerd omdat u hun Url's nodig hebt.

> [!NOTE]
> Het programma gebruikt hand matig opgeslagen scherm opnamen van de video met wille keurige volgroeide/ongepaste-scores om het gebruik van de beoordelings-API te illustreren. In een praktijk situatie gebruikt u de uitvoer van de [video-moderator](video-moderation-api.md#run-the-program-and-review-the-output) om afbeeldingen te maken en scores toe te wijzen. 

Voor de video hebt u een streaming-eind punt nodig zodat het hulp programma voor het controleren de video in de weer gave speler speelt.

![Miniatuur van video demo](images/ams-video-demo-view.PNG)

- Kopieer de **URL** op deze [Azure Media Services demo](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) pagina voor de manifest-URL.

Gebruik de volgende installatie kopieën voor de video frames (afbeeldingen):

![Video frame miniatuur 1](images/ams-video-frame-thumbnails-1.PNG) | ![Video frame miniatuur 2](images/ams-video-frame-thumbnails-2.PNG) | ![Video frame miniatuur 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
Frame 1 | Kader 2 | Kader 3 |

## <a name="create-your-visual-studio-project"></a>Het Visual Studio-project maken

1. Voeg een nieuw project van het type **Console-app (.NET Framework)** toe aan uw oplossing.

1. Geef het project de naam **VideoReviews**.

1. Selecteer dit project als het enige opstartproject voor de oplossing.

### <a name="install-required-packages"></a>De vereiste pakketten installeren

Installeer de volgende NuGet-pakketten voor het TermLists-project.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>De using-instructies van het programma bijwerken

Wijzig de using-instructies van het programma als volgt.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Private-eigenschappen toevoegen

Voeg de volgende persoonlijke eigenschappen toe aan naam ruimte **VideoReviews**, Class **Program**. Werk de `AzureEndpoint` `CMSubscriptionKey` velden en bij met de waarden van uw eind punt-URL en abonnements sleutel. U vindt deze in het Azure Portal op het tabblad **Quick Start** van uw resource.


```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Content Moderator-client object maken

Voeg de volgende methode definitie toe aan de naam ruimte **VideoReviews**, het **programma** class.

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Een video beoordeling maken

Maak een video beoordeling met **ContentModeratorClient. revisies. CreateVideoReviews**. Zie voor meer informatie de [API-naslaghandleiding](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** heeft de volgende vereiste para meters:
1. Een teken reeks die een MIME-type bevat, dat ' application/json ' moet zijn. 
1. De naam van uw Content Moderator team.
1. Een **IList \<CreateVideoReviewsBodyItem>** -object. Elk **CreateVideoReviewsBodyItem** -object vertegenwoordigt een video-beoordeling. Deze Quick Start maakt één beoordeling per keer.

**CreateVideoReviewsBodyItem** heeft verschillende eigenschappen. U kunt ten minste de volgende eigenschappen instellen:
- **Inhoud**. De URL van de video die moet worden gecontroleerd.
- **ContentId**. Een ID die aan de video beoordeling moet worden toegewezen.
- **Status**. Stel de waarde in op ' unpublished '. Als u deze niet instelt, wordt de standaard waarde ' in behandeling ' gebruikt, wat betekent dat de video beoordeling wordt gepubliceerd en de beoordeling van de mens in behandeling is. Zodra een video controle is gepubliceerd, kunt u geen video frames, transcripten of afschrift resultaten meer toevoegen.

> [!NOTE]
> **CreateVideoReviews** retourneert een IList \<string> . Elk van deze teken reeksen bevat een ID voor een video beoordeling. Deze Id's zijn GUID'S en zijn niet hetzelfde als de waarde van de eigenschap **ContentId** . 

Voeg de volgende methode definitie toe aan de naam ruimte VideoReviews, het programma class.

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> De sleutel van uw Content Moderator-service heeft een limiet voor het aantal aanvragen per seconde (RPS) en als u die limiet overschrijdt, genereert de SDK een uitzondering met foutcode 429.
>
> Een sleutel voor de gratis laag heeft een limiet van één RPS.

## <a name="add-video-frames-to-the-video-review"></a>Video frames toevoegen aan de video beoordeling

U voegt video frames toe aan een video beoordeling met **ContentModeratorClient. revisies. AddVideoFrameUrl** (als uw video frames online worden gehost) of **ContentModeratorClient. revisies. AddVideoFrameStream** (als uw video frames lokaal worden gehost). In deze Quick Start wordt ervan uitgegaan dat uw video frames online worden gehost, waardoor **AddVideoFrameUrl** wordt gebruikt. Zie voor meer informatie de [API-naslaghandleiding](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** heeft de volgende vereiste para meters:
1. Een teken reeks die een MIME-type bevat, dat ' application/json ' moet zijn.
1. De naam van uw Content Moderator team.
1. De ID van de video controle die door **CreateVideoReviews** wordt geretourneerd.
1. Een **IList \<VideoFrameBodyItem>** -object. Elk **VideoFrameBodyItem** -object vertegenwoordigt een video frame.

**VideoFrameBodyItem** heeft de volgende eigenschappen:
- **Tijds tempel**. Een teken reeks die, in seconden, de tijd in de video van waaruit het video frame is gemaakt.
- **FrameImage**. De URL van het video frame.
- **Meta gegevens**. Een IList \<VideoFrameBodyItemMetadataItem> . **VideoFrameBodyItemMetadataItem** is gewoon een sleutel/waarde-paar. Geldige sleutels zijn onder andere:
- **reviewRecommended**. Waar als een menselijke beoordeling van het video frame wordt aanbevolen.
- **adultScore**. Een waarde tussen 0 en 1 waarmee de ernst van inhoud voor volwassenen in het video frame wordt geclassificeerd.
- **a**. Waar als de video inhoud voor volwassenen bevat.
- **racyScore**. Een waarde tussen 0 en 1 waarmee de ernst van de ongepaste-inhoud in het video frame wordt geclassificeerd.
- **r**. Waar als het video frame ongepaste inhoud bevat.
- **ReviewerResultTags**. Een IList \<VideoFrameBodyItemReviewerResultTagsItem> . **VideoFrameBodyItemReviewerResultTagsItem** is gewoon een sleutel/waarde-paar. Een toepassing kan deze tags gebruiken om video frames te organiseren.

> [!NOTE]
> Deze Quick Start genereert wille keurige waarden voor de eigenschappen **adultScore** en **racyScore** . In een productie toepassing zou u deze waarden verkrijgen van de [video-moderator service](video-moderation-api.md), geïmplementeerd als een Azure media-service.

Voeg de volgende methode definities toe aan naam ruimte VideoReviews, class Program.

```csharp
<summary>
/// Create a video frame to add to a video review after the video review is created.
/// </summary>
/// <param name="url">The URL of the video frame image.</param>
/// <returns>The video frame.</returns>
private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
{
    // We generate random "adult" and "racy" scores for the video frame.
    Random rand = new Random();

    var frame = new VideoFrameBodyItem
    {
        // The timestamp is measured in milliseconds. Convert from seconds.
        Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
        FrameImage = url,

        Metadata = new List<VideoFrameBodyItemMetadataItem>
        {
            new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
            new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("a", "false"),
            new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("r", "false")
        },

        ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
        {
            new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
        }
    };

    return frame;
}
```

```csharp
/// <summary>
/// Add a video frame to the indicated video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="url">The URL of the video frame image.</param>
static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
{
    Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

    var frames = new List<VideoFrameBodyItem>()
    {
        CreateFrameToAddToReview(url, timestamp_seconds)
    };
        
    client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

    Thread.Sleep(throttleRate);
```

## <a name="get-video-frames-for-video-review"></a>Video frames voor video beoordeling ophalen

U kunt de video frames voor een video beoordeling ontvangen met **ContentModeratorClient. revisies. GetVideoFrames**. **GetVideoFrames** heeft de volgende vereiste para meters:
1. De naam van uw Content Moderator team.
1. De ID van de video controle die door **CreateVideoReviews** wordt geretourneerd.
1. De op nul gebaseerde index van het eerste video frame dat moet worden opgehaald.
1. Het aantal video frames dat moet worden opgehaald.

Voeg de volgende methode definitie toe aan de naam ruimte VideoReviews, het programma class.

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>Informatie over video controle ophalen

U krijgt informatie over een video beoordeling met **ContentModeratorClient. recensies. GetReview**. **GetReview** heeft de volgende vereiste para meters:
1. De naam van uw Content Moderator team.
1. De ID van de video controle die door **CreateVideoReviews** wordt geretourneerd.

Voeg de volgende methode definitie toe aan de naam ruimte VideoReviews, het programma class.

```csharp
/// <summary>
/// Get the information for the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void GetReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

    var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>Video beoordeling publiceren

U publiceert een video beoordeling met **ContentModeratorClient. revisies. PublishVideoReview**. **PublishVideoReview** heeft de volgende vereiste para meters:
1. De naam van uw Content Moderator team.
1. De ID van de video controle die door **CreateVideoReviews** wordt geretourneerd.

Voeg de volgende methode definitie toe aan de naam ruimte VideoReviews, het programma class.

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>Alles samenvoegen

Voeg de definitie van de **hoofd** methode toe aan naam ruimte VideoReviews, class-programma. Sluit ten slotte de programma klasse en de VideoReviews-naam ruimte.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
        var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
        var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

        // Add the frames from 17, 64, and 144 seconds.
        AddFrame(client, review_id, frame1_url, "17");
        AddFrame(client, review_id, frame2_url, "64");
        AddFrame(client, review_id, frame3_url, "144");

        // Get frames information and show
        GetFrames(client, review_id);
        GetReview(client, review_id);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Het programma uitvoeren en de uitvoer controleren
Wanneer u de toepassing uitvoert, ziet u een uitvoer op de volgende regels:

```json
Creating a video review.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "VideoFrames": [
    {
        "Timestamp": "17000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.808312381528463"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.846378884206702"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "64000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.576078300166912"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.244768953064815"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "144000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.664480847150311"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.933817870418456"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
        ]
    }
    ]
}

Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "SubTeam": "public",
    "Status": "UnPublished",
    "ReviewerResultTags": [],
    "CreatedBy": "testreview6",
    "Metadata": [
    {
        "Key": "FrameCount",
        "Value": "3"
    }
    ],
    "Type": "Video",
    "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    "ContentId": "review1",
    "CallbackEndpoint": null
}

Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="check-out-your-video-review"></a>Bekijk uw video-beoordeling

Ten slotte ziet u de video-beoordeling in uw content moderator controle programma-account op het > scherm **video** bekijken.

![Video beoordeling voor menselijke moderators](images/ams-video-review.PNG)

## <a name="next-steps"></a>Volgende stappen

Down load de [Content moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) en de [Visual Studio-oplossing](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) voor deze en andere content moderator Quick starts voor .net.
