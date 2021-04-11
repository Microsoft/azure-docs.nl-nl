---
title: Media Encoder Standard voor instellingen aanpassen | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u geavanceerde code ring kunt uitvoeren door Media Encoder Standard voor instellingen voor de taak aan te passen. In het onderwerp wordt beschreven hoe u Media Services .NET SDK gebruikt om een coderings taak en-taak te maken. Ook wordt uitgelegd hoe u aangepaste voor instellingen kunt opgeven voor de coderings taak.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 758a2fc607217f38306f8d50273c580daf523982
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492691"
---
# <a name="customizing-media-encoder-standard-presets"></a>Media Encoder Standard voor instellingen aanpassen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

## <a name="overview"></a>Overzicht

In dit artikel wordt beschreven hoe u met een aangepaste voor instelling geavanceerde code ring met Media Encoder Standard (MES) uitvoert. In het artikel wordt .NET gebruikt voor het maken van een coderings taak en een taak waarmee deze taak wordt uitgevoerd.  

In dit artikel wordt beschreven hoe u een vooraf ingestelde procedure kunt aanpassen door de [H264 multiple bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) -voor instelling te nemen en het aantal lagen te verminderen. Het artikel [customizing Media Encoder Standard presets](media-services-advanced-encoding-with-mes.md) bevat aangepaste voor instellingen die kunnen worden gebruikt voor het uitvoeren van geavanceerde coderings taken.

> [!NOTE]
> De aangepaste voor instellingen die in dit artikel worden beschreven, kunnen niet worden gebruikt in [Media Services v3](../latest/index.yml) -trans formaties of de CLI-opdrachten. Zie de [richt lijnen voor migratie van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md) voor meer informatie.

## <a name="customizing-a-mes-preset"></a><a id="customizing_presets"></a> Een MES-voor instelling aanpassen

### <a name="original-preset"></a>Oorspronkelijke voor instelling

Sla de JSON op die is gedefinieerd in het artikel [H264 meerdere bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) in een bestand met de extensie. json. Bijvoorbeeld **CustomPreset_JSON.jsop**.

### <a name="customized-preset"></a>Aangepaste voor instelling

Open de **CustomPreset_JSON.jsin** het bestand en Verwijder eerst drie lagen uit **H264Layers** zodat uw bestand er als volgt uitziet.

```json 
  {  
    "Version": 1.0,  
    "Codecs": [  
      {  
        "KeyFrameInterval": "00:00:02",  
        "H264Layers": [  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 1000,  
            "MaxBitrate": 1000,  
            "BufferWindow": "00:00:05",  
            "Width": 640,  
            "Height": 360,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          },  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 650,  
            "MaxBitrate": 650,  
            "BufferWindow": "00:00:05",  
            "Width": 640,  
            "Height": 360,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          },  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 400,  
            "MaxBitrate": 400,  
            "BufferWindow": "00:00:05",  
            "Width": 320,  
            "Height": 180,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          }  
        ],  
        "Type": "H264Video"  
      },  
      {  
        "Profile": "AACLC",  
        "Channels": 2,  
        "SamplingRate": 48000,  
        "Bitrate": 128,  
        "Type": "AACAudio"  
      }  
    ],  
    "Outputs": [  
      {  
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
        "Format": {  
          "Type": "MP4Format"  
        }  
      }  
    ]  
  }  
```

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Code ring met Media Services .NET SDK

In het volgende code voorbeeld wordt Media Services .NET SDK gebruikt om de volgende taken uit te voeren:

- Maak een coderings taak.
- Een verwijzing naar de Media Encoder Standard encoder ophalen.
- Laad de aangepaste JSON-voor instelling die u in de vorige sectie hebt gemaakt. 

    ```csharp
    // Load the JSON from the local file.
    string configuration = File.ReadAllText(fileName);  
    ```

- Een coderings taak toevoegen aan de taak. 
- Geef op welke invoer-Asset moet worden gecodeerd.
- Maak een uitvoer activum dat het gecodeerde activum bevat.
- Voeg een gebeurtenis-handler toe om de voortgang van de taak te controleren.
- Verzend de taak.
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkel omgeving in en vul het app.config bestand in met verbindings informatie, zoals beschreven in [Media Services ontwikkeling met .net](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Voorbeeld   

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace CustomizeMESPresests
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using custom presets.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("CustomPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }

    }
}
```

## <a name="see-also"></a>Zie ook

- [Coderen met een aangepaste trans formatie met behulp van CLI](../latest/transform-custom-preset-cli-how-to.md)
- [Coderen met Media Services v3](../latest/encode-concept.md)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
