---
title: Gezichten met Azure Media Analytics redigeren | Microsoft Docs
description: Azure Media Redactor is een Azure Media Analytics media processor die schaal bare gezichts redactie in de Cloud biedt. In dit artikel wordt beschreven hoe u met Azure Media Analytics gezichten kunt redigeren.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7d416810f6a39fb36bfa3c5225301fe87fdd128c
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013462"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Gezichten met Azure Media Analytics redigeren

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Overzicht

**Azure media Redactor** is een [Azure Media Analytics](./legacy-components.md) media processor (MP) dat schaal bare gezichts redactie in de Cloud biedt. Met gezichts redactie kunt u uw video aanpassen zodat u de gezichten van geselecteerde personen kunt vervagen. U kunt de gezichts redactie service gebruiken in de scenario's voor open bare veiligheid en nieuws media. Een paar minuten van beeld materiaal dat meerdere gezichten bevat, kan uren duren om hand matig te worden geredigeerd, maar bij deze service zijn slechts enkele eenvoudige stappen vereist voor het redactie proces van het gezicht.

Dit artikel bevat informatie over **Azure media Redactor** en laat zien hoe u deze kunt gebruiken met Media Services SDK voor .net.

## <a name="face-redaction-modes"></a>Modus voor redactie van gezicht

Gezichts redactie werkt door het detecteren van gezichten in elk frame van de video en bij het volgen van het gezichts object in de loop van de tijd, zodat dezelfde persoon van andere hoeken ook kan worden vervaagd. Het geautomatiseerde redactie proces is complex en produceert niet altijd 100% van de gewenste uitvoer. Daarom biedt Media Analytics u een aantal manieren om de uiteindelijke uitvoer te wijzigen.

Naast een volledig automatische modus, is er een twee richtings werk stroom, waarmee de selectie/de selectie van gevonden gezichten via een lijst met Id's mogelijk is. Als u een wille keurig aantal aanpassingen per kader wilt maken, gebruikt het MP een meta gegevensbestand in JSON-indeling. Deze werk stroom is opgesplitst in de modi **analyseren** en **redactie** . U kunt de twee modi combi neren in één fase waarmee beide taken in één taak worden uitgevoerd. deze modus heet **gecombineerd**.

   > [!NOTE]
   > De software met een gezichts detectie medium is vanaf juni 2020 verouderd, [Azure Media Services verouderde onderdelen](./legacy-components.md). U kunt overwegen Azure Media Services v3 API te gebruiken.

### <a name="combined-mode"></a>Gecombineerde modus

Dit produceert automatisch een geredigeerde MP4 zonder hand matige invoer.

| Fase | Bestandsnaam | Notities |
| --- | --- | --- |
| Invoer Asset |Foo. Bar |Video in WMV-, MOV-of MP4-indeling |
| Invoer configuratie |Vooraf ingestelde taak configuratie |{' version ': ' 1.0 ', ' opties ': {' mode ': ' gecombineerd '}} |
| Uitvoer activum |foo_redacted.mp4 |Video met vervaging toegepast |

### <a name="analyze-mode"></a>Analyse modus

De **analyse** fase van de werk stroom met twee slagen neemt een video-invoer en produceert een JSON-bestand met gezichts locaties en JPG-afbeeldingen van elk gedetecteerd gezicht.

| Fase | Bestandsnaam | Notities |
| --- | --- | --- |
| Invoer Asset |Foo. Bar |Video in WMV-, MPV-of MP4-indeling |
| Invoer configuratie |Vooraf ingestelde taak configuratie |{' version ': ' 1.0 ', ' opties ': {' mode ': ' analyse '}} |
| Uitvoer activum |foo_annotations.jsop |Aantekening gegevens van gezichts locaties in JSON-indeling. Dit kan worden bewerkt door de gebruiker om de vervagings kaders te wijzigen. Zie het voor beeld hieronder. |
| Uitvoer activum |foo_thumb% 06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Een bijgesneden jpg van elk gedetecteerd gezicht, waarbij het nummer de labelId van het gezicht aangeeft |

#### <a name="output-example"></a>Uitvoer voorbeeld

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-mode"></a>Modus redactie

De tweede fase van de werk stroom vergt een groter aantal invoer waarden die in één activum moeten worden gecombineerd.

Dit omvat een lijst met Id's voor vervagen, de oorspronkelijke video en de JSON van de aantekeningen. In deze modus wordt de annotaties gebruikt om vervaging toe te passen op de invoer video.

De uitvoer van de analyse fase bevat niet de oorspronkelijke video. De video moet worden geüpload naar de invoer Asset voor de redactie modus taak en als primair bestand zijn geselecteerd.

| Fase | Bestandsnaam | Notities |
| --- | --- | --- |
| Invoer Asset |Foo. Bar |Video in WMV-, MPV-of MP4-indeling. Dezelfde video als in stap 1. |
| Invoer Asset |foo_annotations.jsop |Meta gegevensbestand van de fase één, met optionele wijzigingen. |
| Invoer Asset |foo_IDList.txt (optioneel) |Optionele, door de nieuwe gescheiden lijst met gezichts-Id's voor redactie. Als dit veld leeg blijft, worden alle gezichten vervaagd. |
| Invoer configuratie |Vooraf ingestelde taak configuratie |{' version ': ' 1.0 ', ' opties ': {' mode ': ' redactie '}} |
| Uitvoer activum |foo_redacted.mp4 |Video met vervaging toegepast op basis van aantekeningen |

#### <a name="example-output"></a>Voorbeelduitvoer

Dit is de uitvoer van een IDList waarvoor een ID is geselecteerd.

Voor beeld foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Vervagings typen

In de **gecombineerde** of **redactie** modus zijn er vijf verschillende vervagings modi waaruit u kunt kiezen via de JSON-invoer configuratie: **laag**, **med**, **hoog**, **vak** en **zwart**. Standaard wordt **med** gebruikt.

Hieronder vindt u voor beelden van de vervagings typen die hieronder worden beschreven.

### <a name="example-json"></a>Voor beeld JSON

```json
{
    'version':'1.0',
    'options': {
        'Mode': 'Combined',
        'BlurType': 'High'
    }
}
```

#### <a name="low"></a>Beperkt

![Beperkt](./media/media-services-face-redaction/blur1.png)

#### <a name="med"></a>STB

![STB](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Hoog

![Hoog](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Zwart

![Zwart](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Elementen van het JSON-uitvoer bestand

De redactie-MP biedt een hoge precisie voor detectie en tracering van locaties, waarmee u in een video frame Maxi maal 64 menselijke gezichten kunt detecteren. Front-gezichten bieden de beste resultaten, terwijl de zijde gezichten en kleine gezichten (kleiner dan of gelijk aan 24x24 pixels) lastig zijn.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET-voorbeeld code

Het volgende programma laat zien hoe u:

1. Maak een Asset en upload een media bestand naar de Asset.
2. Maak een taak met een gezichts redactie taak op basis van een configuratie bestand dat de volgende JSON-voor instelling bevat: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
                }
            }
    ```

3. Down load de JSON-uitvoer bestanden.

### <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkel omgeving in en vul het app.config bestand in met verbindings informatie, zoals beschreven in [Media Services ontwikkeling met .net](media-services-dotnet-how-to-use.md).

#### <a name="example"></a>Voorbeeld

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace FaceRedaction
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

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                error.Code,
                                error.Message));
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                       mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
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
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwante koppelingen

[Overzicht van Azure Media Services Analytics](./legacy-components.md)

[Demo's Azure Media Analytics](http://amslabs.azurewebsites.net/demos/Analytics.html)
