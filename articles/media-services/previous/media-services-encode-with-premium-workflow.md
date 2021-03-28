---
title: Geavanceerde code ring met Media Encoder Premium Workflow | Microsoft Docs
description: Meer informatie over het coderen met Media Encoder Premium Workflow. Code voorbeelden zijn geschreven in C# en gebruiken de Media Services SDK voor .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 8d39d54498872201afe2f705da1fc5654d8befb2
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644995"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Geavanceerd coderen met Media Encoder Premium Workflow

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Encoder Premium Workflow media processor die in dit artikel wordt besproken, is niet beschikbaar in China.
>
>

## <a name="overview"></a>Overzicht
Microsoft Azure Media Services introduceert de **Media Encoder Premium workflow** media processor. Deze processor biedt geavanceerde coderings mogelijkheden voor uw Premium on-demand werk stromen.

De volgende onderwerpen bevatten informatie over de **Media Encoder Premium workflow**:

* [Indelingen die worden ondersteund door de Media Encoder Premium workflow](./media-services-encode-asset.md) : hierin worden de bestands indelingen en codecs besproken die door **Media Encoder Premium workflow** worden ondersteund.
* [Overzicht en vergelijking van media encoders van Azure op aanvraag](media-services-encode-asset.md) vergelijkt de coderings mogelijkheden van **Media Encoder Premium workflow** en **Media Encoder Standard**.

In dit artikel wordt beschreven hoe u kunt coderen met **Media Encoder Premium workflow** met behulp van .net.

Voor het coderen van taken voor de **Media Encoder Premium workflow** is een afzonderlijk configuratie bestand vereist. dit wordt een werk stroom bestand genoemd. Deze bestanden hebben de extensie. werk stroom en worden gemaakt met het hulp programma [Workflow Designer](media-services-workflow-designer.md) .

U kunt [hier](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/media-services/previous/media-services-encode-with-premium-workflow.md
)ook de standaard werk stroom bestanden ophalen. De map bevat ook de beschrijving van deze bestanden.

De werk stroom bestanden moeten worden geüpload naar uw Media Services-account als een Asset en deze asset moet worden door gegeven aan de coderings taak.

## <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkel omgeving in en vul het app.config bestand in met verbindings informatie, zoals beschreven in [Media Services ontwikkeling met .net](media-services-dotnet-how-to-use.md). 

## <a name="encoding-example"></a>Voor beeld van code ring

In het volgende voor beeld ziet u hoe u kunt coderen met **Media Encoder Premium workflow**.

De volgende stappen worden uitgevoerd:

1. Maak een Asset en upload een werk stroom bestand.
2. Maak een Asset en upload een bron media bestand.
3. Haal de media processor ' Media Encoder Premium Workflow ' op.
4. Maak een taak en een taak.

    In de meeste gevallen is de configuratie teken reeks voor de taak leeg (zoals in het volgende voor beeld). Er zijn een aantal geavanceerde scenario's (waarvoor u runtime-eigenschappen dynamisch moet instellen) in dat geval kunt u een XML-teken reeks voor de coderings taak opgeven. Voor beelden van dergelijke scenario's zijn: het maken van een overlay, parallelle of sequentiële media steek-en Subtitling.
5. Voeg twee invoer assets toe aan de taak.

   1. 1e: de werk stroom Asset.
   2. 2e: de video-Asset.

      >[!NOTE]
      >Het werk stroom activum moet worden toegevoegd aan de taak vóór de media-asset.
      De configuratie teken reeks voor deze taak moet leeg zijn.
   
6. De coderings taak verzenden.

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderPremiumWorkflowSample
{
    class Program
    {
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _workflowFilePath =
            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

        private static readonly string _singleMP4InputFilePath =
            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

        static void Main(string[] args)
        {

            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

        }

        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                processor,
                "",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(workflow);
            task.InputAssets.Add(video); // we add one asset
                                         // Add an output asset to contain the results of the job.
                                         // This output is specified as AssetCreationOptions.None, which
                                         // means the output asset is not encrypted.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                throw new Exception("\nExiting method due to job error.");
            }

            return job.OutputMediaAssets[0];
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

## <a name="need-help"></a>Hulp nodig?

U kunt een ondersteunings ticket openen door te navigeren naar de [nieuwe ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
