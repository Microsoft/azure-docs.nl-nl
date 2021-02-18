---
title: Een taak met meerdere trans formatie-uitvoer maken
description: In dit onderwerp ziet u hoe u een Azure Media Services-taak maakt met meerdere trans formatie-uitvoer.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a40ce1fd7b3d9d946641c91f5876d8c73755b005
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092265"
---
# <a name="create-a-job-with-multiple-transform-outputs"></a>Een taak met meerdere trans formatie-uitvoer maken

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit onderwerp wordt beschreven hoe u een trans formatie maakt met twee trans formatie-uitvoer. De eerste keer dat de invoer wordt gecodeerd voor Adaptive Bitrate Streaming met een ingebouwde [AdaptiveStreaming](encoding-concept.md#builtinstandardencoderpreset) -voor instelling. De tweede roept het audio signaal in de invoer video op dat met de [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md#built-in-presets)moet worden verwerkt. Nadat de trans formatie is gemaakt, kunt u een taak verzenden waarmee uw video op de juiste wijze wordt verwerkt. Omdat in dit voor beeld twee trans formatie-uitvoer worden opgegeven, moeten we twee taak uitvoer opgeven. U kunt ervoor kiezen om beide taak uitvoer naar dezelfde activa te sturen (zoals hieronder wordt weer gegeven), of u kunt de resultaten naar afzonderlijke assets schrijven.
 

> [!TIP]
> Controleer voordat u begint met het ontwikkelen [met behulp van Media Services v3 api's](media-services-apis-overview.md) (bevat informatie over het openen van api's, naam conventies, enzovoort).

## <a name="create-a-transform"></a>Een transformatie maken

De volgende code laat zien hoe u een trans formatie maakt die twee uitvoer produceert.

```csharp
private static async Task<Transform> GetOrCreateTransformAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = await client.Transforms.GetAsync(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // You need to specify what you want it to produce as an output
        TransformOutput[] output = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    // This sample uses the built-in encoding preset for Adaptive Bitrate Streaming.
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            },
            // Create an analyzer preset with video insights.
            new TransformOutput(new AudioAnalyzerPreset("en-US"))
        };

        // Create the Transform with the output defined above
        transform = await client.Transforms.CreateOrUpdateAsync(resourceGroupName, accountName, transformName, output);
    }

    return transform;
}
```

## <a name="submit-a-job"></a>Een taak indienen

Maak een taak met een invoer van een HTTPS-URL en met twee taak uitvoer.

```csharp
private static async Task<Job> SubmitJobAsync(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string transformName)
{
    // Output from the encoding Job must be written to an Asset, so let's create one
    string outputAssetName1 = $"output-" + Guid.NewGuid().ToString("N");
    Asset outputAsset = await client.Assets.CreateOrUpdateAsync(resourceGroup, accountName, outputAssetName1, new Asset());

    // This example shows how to encode from any HTTPs source URL - a new feature of the v3 API.  
    // Change the URL to any accessible HTTPs URL or SAS URL from Azure.
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        // Since we are specifying two Transform Outputs, two Job Outputs are needed.
        // In this example, the first Job Output is for the results from adaptive bitrate encoding,
        // and the second is for the results from audio analysis. In this example, both are written to the
        // same output Asset. Or, you can specify different Assets.
        
        new JobOutputAsset(outputAsset.Name),
        new JobOutputAsset(outputAsset.Name)

    };

    // In this example, we are using a unique job name.
    //
    // If you already have a job with the desired name, use the Jobs.Get method
    // to get the existing job. In Media Services v3, Get methods on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).
    Job job;
    try
    {
        string jobName = $"job-" + Guid.NewGuid().ToString("N");
        job = await client.Jobs.CreateAsync(
                    resourceGroup,
                    accountName,
                    transformName,
                    jobName,
                    new Job
                    {
                        Input = jobInput,
                        Outputs = jobOutputs,
                    });
    }
    catch (Exception exception)
    {
        if (exception.GetBaseException() is ApiErrorException apiException)
        {
            Console.Error.WriteLine(
                $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
        }
        throw exception;
    }

    return job;
}
```
## <a name="job-error-codes"></a>Foutcodes in taak

Zie [Foutcodes](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Volgende stappen

[Azure Media Services v3-voor beelden met behulp van .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/) 
