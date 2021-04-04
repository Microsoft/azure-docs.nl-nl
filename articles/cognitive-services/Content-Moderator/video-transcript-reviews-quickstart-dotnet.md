---
title: Afschriften over video transcripten maken met behulp van .NET-Content Moderator
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van overzichten voor het afschriften van Video's met de Azure Cognitive Services Content Moderator SDK voor .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 326fc2cc162a2ab54b40888250fbeef55ad8800a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96853455"
---
# <a name="create-video-transcript-reviews-using-net"></a>Transcriptie van video maken met behulp van .NET

In dit artikel vindt u informatie en code voorbeelden waarmee u snel aan de slag kunt met de [Content moderator SDK met C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) voor het volgende:

- Een video-beoordeling maken voor menselijke moderators
- Een getemperd transcript toevoegen aan de beoordeling
- De beoordeling publiceren

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account op de site Content Moderator [controle programma](https://contentmoderator.cognitive.microsoft.com/) als u dit nog niet hebt gedaan.
- In dit artikel wordt ervan uitgegaan dat u [de video hebt gematigd](video-moderation-api.md) en [de video beoordeling hebt gemaakt](video-reviews-quickstart-dotnet.md) in het beoordelings programma voor het maken van een menselijk besluit. U wilt nu gelaagde video transcripten toevoegen in het beoordelings programma.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Zorg ervoor dat uw API-sleutel de beoordelings-API kan aanroepen (taak maken)

Nadat u de vorige stappen hebt uitgevoerd, zou u twee Content Moderator-sleutels kunnen hebben als u vanuit de Azure-portal bent gestart.

Als u van plan bent de door Azure verstrekte API-sleutel in uw SDK-voorbeeld te gebruiken, volgt u de stappen in de sectie [De Azure-sleutel met de beoordelings-API gebruiken](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) zodat uw app de beoordelings-API kan aanroepen en beoordelingen kan maken.

Als u de gratis proefversie van de sleutel gebruikt die wordt gegenereerd door het beoordelingsprogramma, is uw account van het beoordelingsprogramma al op de hoogte van de sleutel, zodat er geen extra stappen zijn vereist.

## <a name="prepare-your-video-for-review"></a>Uw video voorbereiden voor beoordeling

Voeg de transcriptie toe aan een video beoordeling. De video moet online worden gepubliceerd. U hebt het streaming-eind punt nodig. Met het streaming-eind punt kunt u de video afspelen met de video speler voor het beoordelings programma.

![Miniatuur van video demo](images/ams-video-demo-view.PNG)

- Kopieer de **URL** op deze [Azure Media Services demo](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) pagina voor de manifest-URL.

## <a name="create-your-visual-studio-project"></a>Het Visual Studio-project maken

1. Voeg een nieuw project van het type **Console-app (.NET Framework)** toe aan uw oplossing.

1. Geef het project de naam **VideoTranscriptReviews**.

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

Voeg de volgende persoonlijke eigenschappen toe aan naam ruimte **VideoTranscriptReviews**, Class **Program**. Werk de `AzureEndpoint` `CMSubscriptionKey` velden en bij met de waarden van uw eind punt-URL en abonnements sleutel. U vindt deze in het Azure Portal op het tabblad **Quick Start** van uw resource.

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

Voeg de volgende methode definitie toe aan de naam ruimte VideoTranscriptReviews, het programma class.

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
> Er geldt een limiet voor het aantal aanvragen per seconde (RPS) voor de sleutel van de Content Moderator-service. Als u de limiet overschrijdt, veroorzaakt dit een uitzondering met foutcode 429 in de SDK.
>
> Een sleutel voor de gratis laag heeft een limiet van één RPS.

## <a name="add-transcript-to-video-review"></a>Transcript toevoegen aan video beoordeling

U voegt een transcript toe aan een video beoordeling met **ContentModeratorClient. revisies. AddVideoTranscript**. **AddVideoTranscript** heeft de volgende vereiste para meters:
1. Uw Content Moderator team-ID.
1. De ID van de video controle die door **CreateVideoReviews** wordt geretourneerd.
1. Een **Stream** -object dat de transcripten bevat.

De transcript moet de WebVTT-indeling hebben. Zie voor meer informatie [WebVTT: de indeling van de web video-tekst](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Het programma gebruikt een voor beeld van een transcript in de VTT-indeling. In een echte oplossing gebruikt u de Azure Media Indexer-service om [een transcriptie te genereren](../../media-services/previous/media-services-index-content.md) op basis van een video.

Voeg de volgende methode definitie toe aan de naam ruimte VideotranscriptReviews, het programma class.

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Een afschrift beheer resultaat toevoegen aan de video beoordeling

Naast het toevoegen van een transcript aan een video beoordeling, voegt u ook het resultaat van het beheer van de transcriptie toe. U doet dit met **ContentModeratorClient. revisies. AddVideoTranscriptModerationResult**. Zie voor meer informatie de [API-naslaghandleiding](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** heeft de volgende vereiste para meters:
1. Een teken reeks die een MIME-type bevat, dat ' application/json ' moet zijn. 
1. De naam van uw Content Moderator team.
1. De ID van de video controle die door **CreateVideoReviews** wordt geretourneerd.
1. Een IList \<TranscriptModerationBodyItem> . Een **TranscriptModerationBodyItem** heeft de volgende eigenschappen:
1. **Voor waarden**. Een IList \<TranscriptModerationBodyItemTermsItem> . Een **TranscriptModerationBodyItemTermsItem** heeft de volgende eigenschappen:
1. **Index**. De op nul gebaseerde index van de term.
1. **Term**. Een teken reeks die de term bevat.
1. **Tijds tempel**. Een teken reeks die, in seconden, de tijd in de transcripten bevat waarop de termen worden gevonden.

De transcript moet de WebVTT-indeling hebben. Zie voor meer informatie [WebVTT: de indeling van de web video-tekst](https://www.w3.org/TR/webvtt1/).

Voeg de volgende methode definitie toe aan de naam ruimte VideoTranscriptReviews, het programma class. Met deze methode wordt een transcript verzonden naar de methode **ContentModeratorClient. TextModeration. ScreenText** . Ook wordt het resultaat omgezet in een IList \<TranscriptModerationBodyItem> en verzonden naar **AddVideoTranscriptModerationResult**.

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

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

Voeg de definitie van de **hoofd** methode toe aan naam ruimte VideoTranscriptReviews, class-programma. Sluit ten slotte de programma klasse en de VideoTranscriptReviews-naam ruimte.

> [!NOTE]
> Het programma gebruikt een voor beeld van een transcript in de VTT-indeling. In een echte oplossing gebruikt u de Azure Media Indexer-service om [een transcriptie te genereren](../../media-services/previous/media-services-index-content.md) op basis van een video.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

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

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Navigeer naar uw video-transcript beoordeling

Ga naar de video-transcriptcontrole in het hulp programma content moderator beoordeling op het >  > **transcript** scherm van de video bekijken.

U ziet de volgende functies:
- De twee regels transcripten die u hebt toegevoegd
- De term voor scheld woorden die door de service voor tekst toezicht is gevonden en gemarkeerd
- Als u een transcriptie-tekst selecteert, wordt de video vanuit die tijds tempel gestart

![Video-Transcript beoordeling voor menselijke moderators](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Volgende stappen

Down load de [Content moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) en de [Visual Studio-oplossing](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) voor deze en andere content moderator Quick starts voor .net.

Meer informatie over het genereren van [video beoordelingen](video-reviews-quickstart-dotnet.md) in het beoordelings programma.
