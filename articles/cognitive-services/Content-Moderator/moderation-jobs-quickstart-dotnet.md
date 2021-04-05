---
title: Beheer taken gebruiken met behulp van .NET-Content Moderator
titleSuffix: Azure Cognitive Services
description: Gebruik de Content Moderator .NET SDK om end-to-end taken voor het beheer van inhoud te initiëren voor afbeeldings-of tekst inhoud in azure Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: ec101786f33aa6f2525d685993d6b6c891ab2e9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "88936313"
---
# <a name="define-and-use-moderation-jobs-net"></a>Toezicht taken definiëren en gebruiken (.NET)

Een toezicht taak fungeert als een soort wrapper voor de functionaliteit van toezicht op inhoud, werk stromen en Beoordelingen. Deze hand leiding bevat informatie en code voorbeelden waarmee u aan de slag kunt met de [Content moderator SDK voor .net voor](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) het volgende:

- Een beoordelingstaak starten voor het scannen en maken van beoordelingen voor menselijke beoordelaars
- De status van een openstaande beoordeling opvragen
- De definitieve status van de beoordeling volgen en opvragen
- De beoordelings resultaten verzenden naar de call back-URL

## <a name="prerequisites"></a>Vereisten

- Meld u aan of maak een account op de site van het Content Moderator [controle programma](https://contentmoderator.cognitive.microsoft.com/) .

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Instellen dat uw API-sleutel de beoordelings-API kan aanroepen voor het maken van de beoordeling

Nadat u de vorige stappen hebt uitgevoerd, zou u twee Content Moderator-sleutels kunnen hebben als u vanuit de Azure-portal bent gestart.

Als u van plan bent de door Azure verstrekte API-sleutel in uw SDK-voorbeeld te gebruiken, volgt u de stappen in de sectie [De Azure-sleutel met de beoordelings-API gebruiken](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) zodat uw app de beoordelings-API kan aanroepen en beoordelingen kan maken.

Als u de gratis proefversie van de sleutel gebruikt die wordt gegenereerd door het beoordelingsprogramma, is uw account van het beoordelingsprogramma al op de hoogte van de sleutel, zodat er geen extra stappen zijn vereist.

## <a name="define-a-custom-moderation-workflow"></a>Een aangepaste werkstroom voor beoordeling definiëren

Een beoordelingstaak scant uw inhoud met behulp van de API's en maakt gebruik van een **werkstroom** om te bepalen of er al dan niet beoordelingen moeten worden gemaakt.
Hoewel het beoordelingsprogramma een standaardwerkstroom bevat, gaan we voor deze snelstartgids [een aangepaste werkstroom definiëren](Review-Tool-User-Guide/Workflows.md).

U gebruikt de naam van de werkstroom in uw code waarmee de beoordelingstaak wordt gestart.

## <a name="create-your-visual-studio-project"></a>Het Visual Studio-project maken

1. Voeg een nieuw project van het type **Console-app (.NET Framework)** toe aan uw oplossing.

   Geef het project de naam **CreateReviews** in de voorbeeldcode.

1. Selecteer dit project als het enige opstartproject voor de oplossing.

### <a name="install-required-packages"></a>De vereiste pakketten installeren

Installeer de volgende NuGet-pakketten:

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>De using-instructies van het programma bijwerken

Pas de using-instructies van het programma aan.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>De Content Moderator-client maken

Voeg de volgende code toe om een Content Moderator-client voor uw abonnement te maken.

> [!IMPORTANT]
> Werk de velden **AzureEndpoint** en **CMSubscriptionKey** bij met de waarden van uw eind punt-URL en abonnements sleutel.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

### <a name="initialize-application-specific-settings"></a>Toepassingsspecifieke instellingen initialiseren

Voeg de volgende constanten en statische velden toe aan de klasse **Program** in Program.cs.

> [!NOTE]
> U stelt de constante TeamName in op de naam die u hebt gebruikt tijdens het maken van uw abonnement voor Content Moderator. U kunt de TeamName vinden op de website van Content Moderator.
> Als u bent aangemeld, selecteert u **Credentials** in het menu **Settings** (tandwielpictogram).
>
> De naam van uw team is de waarde van het veld **Id** veld in de sectie **API**.

```csharp
/// <summary>
/// The moderation job will use this workflow that you defined earlier.
/// See the quickstart article to learn how to setup custom workflows.
/// </summary>
private const string WorkflowName = "OCR";

/// <summary>
/// The name of the team to assign the job to.
/// </summary>
/// <remarks>This must be the team name you used to create your
/// Content Moderator account. You can retrieve your team name from
/// the Content Moderator web site. Your team name is the Id associated
/// with your subscription.</remarks>
private const string TeamName = "***";

/// <summary>
/// The URL of the image to create a review job for.
/// </summary>
private const string ImageUrl =
    "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

/// <summary>
/// The name of the log file to create.
/// </summary>
/// <remarks>Relative paths are relative to the execution directory.</remarks>
private const string OutputFile = "OutputLog.txt";

/// <summary>
/// The number of seconds to delay after a review has finished before
/// getting the review results from the server.
/// </summary>
private const int latencyDelay = 45;

/// <summary>
/// The callback endpoint for completed reviews.
/// </summary>
/// <remarks>Reviews show up for reviewers on your team.
/// As reviewers complete reviews, results are sent to the
/// callback endpoint using an HTTP POST request.</remarks>
private const string CallbackEndpoint = "";
```

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Code toevoegen voor automatische beoordeling, het maken van een beoordeling en het ophalen van taakgegevens

> [!Note]
> In de praktijk stelt u de callback-URL **CallbackEndpoint** in op de URL die de resultaten ontvangt van de handmatige beoordeling (via een HTTP POST-aanvraag).

Begin door de volgende code toe te voegen aan de **Main**-methode.

```csharp
using (TextWriter writer = new StreamWriter(OutputFile, false))
{
    using (var client = Clients.NewClient())
    {
        writer.WriteLine("Create review job for an image.");
        var content = new Content(ImageUrl);

        // The WorkflowName contains the name of the workflow defined in the online review tool.
        // See the quickstart article to learn more.
        var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

        // Record the job ID.
        var jobId = jobResult.Result.Body.JobIdProperty;

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
            jobResult.Result.Body, Formatting.Indented));

        Thread.Sleep(2000);
        writer.WriteLine();

        writer.WriteLine("Get review job status.");
        var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
                jobDetails.Result.Body, Formatting.Indented));

        Console.WriteLine();
        Console.WriteLine("Perform manual reviews on the Content Moderator site.");
        Console.WriteLine("Then, press any key to continue.");
        Console.ReadKey();

        Console.WriteLine();
        Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
        Thread.Sleep(latencyDelay * 1000);

        writer.WriteLine("Get review details.");
        jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
        TeamName, jobId);

        // Log just the response body from the returned task.
        writer.WriteLine(JsonConvert.SerializeObject(
        jobDetails.Result.Body, Formatting.Indented));
    }
    writer.Flush();
    writer.Close();
}
```

> [!NOTE]
> Er geldt een limiet voor het aantal aanvragen per seconde (RPS) voor de sleutel van de Content Moderator-service. Als u de limiet overschrijdt, veroorzaakt dit een uitzondering met foutcode 429 in de SDK.
>
> Een sleutel voor de gratis laag heeft een limiet van één RPS.

## <a name="run-the-program-and-review-the-output"></a>Het programma uitvoeren en de uitvoer controleren

U ziet de volgende voorbeelduitvoer in de console:

```console
Perform manual reviews on the Content Moderator site.
Then, press any key to continue.
```

Meld u aan bij het Content Moderator-beoordelingsprogramma om de openstaande beoordeling van de afbeelding te zien.

Gebruik de knop **Volgende** om de code te verzenden.

![Beoordeling van afbeelding voor menselijke beoordelaars](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>De voorbeelduitvoer in het logboekbestand bekijken

> [!NOTE]
> In het uitvoerbestand vertegenwoordigen de tekenreeksen **Teamname**, **ContentId**, **CallBackEndpoint** en **WorkflowId** de waarden die u eerder hebt gebruikt.

```json
Create moderation job for an image.
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
}

Get review details.
{
    "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
    "TeamName": "some team name",
    "Status": "InProgress",
    "WorkflowId": "OCR",
    "Type": "Image",
    "CallBackEndpoint": "",
    "ReviewId": "",
    "ResultMetaData": [],
    "JobExecutionReport": [
    {
        "Ts": "2018-01-07T00:38:26.7714671",
        "Msg": "Successfully got hasText response from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:26.4181346",
        "Msg": "Getting hasText from Moderator"
    },
    {
        "Ts": "2018-01-07T00:38:25.5122828",
        "Msg": "Starting Execution - Try 1"
    }
    ]
}
```

## <a name="your-callback-url-if-provided-receives-this-response"></a>Indien opgegeven ontvangt de URL voor terugbellen deze reactie

U ziet een reactie zoals in het volgende voorbeeld:

> [!NOTE]
> In de callback-reactie vertegenwoordigen de tekenreeksen **ContentId** en **WorkflowId** de waarden die u eerder hebt gebruikt.

```json
{
    "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
    "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
    "WorkFlowId": "OCR",
    "Status": "Complete",
    "ContentType": "Image",
    "CallBackType": "Job",
    "ContentId": "contentID",
    "Metadata": {
        "hastext": "True",
        "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
        "imagename": "contentID"
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Download de [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) en de [Visual Studio-oplossing](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) voor deze en andere snelstarts over Content Moderator voor .NET en begin met de integratie.
