---
title: Media Services-assets beheren voor meerdere opslag accounts | Microsoft Docs
description: In deze artikelen worden richt lijnen gegeven voor het beheren van Media Services-assets over meerdere opslag accounts.
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
ms.openlocfilehash: b3b2cb4328a68c7718101c5acd362258706cb6d8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013241"
---
# <a name="managing-media-services-assets-across-multiple-storage-accounts"></a>Media Services-assets beheren in meerdere opslag accounts  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

U kunt meerdere opslag accounts koppelen aan één Media Services-account. De mogelijkheid om meerdere opslag accounts aan een Media Services-account toe te voegen biedt de volgende voor delen:

* Taak verdeling van uw assets over meerdere opslag accounts.
* Het schalen van Media Services voor grote hoeveel heden inhoud verwerkingen (zoals momenteel één opslag account heeft een maximum limiet van 500 TB). 

In dit artikel wordt beschreven hoe u meerdere opslag accounts koppelt aan een Media Services-account met behulp van [Azure Resource Manager-api's](/rest/api/media/operations/azure-media-services-rest-api-reference) en [Power shell](/powershell/module/az.media). Ook wordt uitgelegd hoe u verschillende opslag accounts kunt opgeven wanneer u assets maakt met behulp van de Media Services SDK. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="considerations"></a>Overwegingen

Wanneer u meerdere opslag accounts aan uw Media Services-account koppelt, zijn de volgende overwegingen van toepassing:

* Het Media Services-account en alle gekoppelde opslagaccounts moeten zich in hetzelfde Azure-abonnement bevinden. Het is raadzaam om opslagaccounts te gebruiken die zich op dezelfde locatie bevinden als het Media Services-account.
* Wanneer een opslag account is gekoppeld aan het opgegeven Media Services-account, kan het niet worden losgekoppeld.
* Primair opslag account is de naam die wordt aangegeven tijdens het maken van de Media Services account. Op dit moment kunt u het standaard opslag account niet wijzigen. 
* Als u een koud opslag account wilt toevoegen aan het AMS-account, moet het opslag account een BLOB-type zijn en worden ingesteld op niet-Primary.

Andere overwegingen:

Media Services gebruikt de waarde van de eigenschap **IAssetFile.name** bij het bouwen van url's voor de streaming-inhoud (bijvoorbeeld http://{WAMSAccount}. Origin. Media Services. Windows. net/{GUID}/{IAssetFile. name}/streamingParameters.) Daarom is het percentage code ring niet toegestaan. De waarde van de eigenschap name mag niet de volgende tekens voor [percentage versleuteling](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)bevatten:! * ' ();: @ &= + $,/?% # [] '. Er kan ook één '. ' zijn. voor de bestandsnaam extensie.

## <a name="to-attach-storage-accounts"></a>Opslag accounts koppelen  

Als u opslag accounts aan uw AMS-account wilt koppelen, gebruikt u [Azure Resource Manager-api's](/rest/api/media/operations/azure-media-services-rest-api-reference) en [Power shell](/powershell/module/az.media), zoals wordt weer gegeven in het volgende voor beeld:

```azurepowershell
$regionName = "West US"
$subscriptionId = " xxxxxxxx-xxxx-xxxx-xxxx- xxxxxxxxxxxx "
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccount1Name = "skystorage1"
$storageAccount2Name = "skystorage2"
$storageAccount1Id = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccount1Name"
$storageAccount2Id = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccount2Name"
$storageAccount1 = New-AzMediaServiceStorageConfig -StorageAccountId $storageAccount1Id -IsPrimary
$storageAccount2 = New-AzMediaServiceStorageConfig -StorageAccountId $storageAccount2Id
$storageAccounts = @($storageAccount1, $storageAccount2)
    
Set-AzMediaService -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccounts $storageAccounts
```

### <a name="support-for-cool-storage"></a>Ondersteuning voor coole opslag

Als u momenteel een koud opslag account wilt toevoegen aan het AMS-account, moet het opslag account een BLOB-type zijn en worden ingesteld op niet-Primary.

## <a name="to-manage-media-services-assets-across-multiple-storage-accounts"></a>Media Services-assets beheren in meerdere opslag accounts
De volgende code maakt gebruik van de nieuwste Media Services SDK om de volgende taken uit te voeren:

1. Alle opslag accounts weer geven die zijn gekoppeld aan het opgegeven Media Services-account.
2. Haal de naam op van het standaard opslag account.
3. Maak een nieuwe asset in het standaard opslag account.
4. Maak een uitvoer activum van de coderings taak in het opgegeven opslag account.
   
```cs
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace MultipleStorageAccounts
{
    class Program
    {
        // Location of the media file that you want to encode. 
        private static readonly string _singleInputFilePath =
            Path.GetFullPath(@"../..\supportFiles\multifile\interview2.wmv");

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Display the storage accounts associated with 
            // the specified Media Services account:
            foreach (var sa in _context.StorageAccounts)
                Console.WriteLine(sa.Name);

            // Retrieve the name of the default storage account.
            var defaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == true).FirstOrDefault();
            Console.WriteLine("Name: {0}", defaultStorageName.Name);
            Console.WriteLine("IsDefault: {0}", defaultStorageName.IsDefault);

            // Retrieve the name of a storage account that is not the default one.
            var notDefaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == false).FirstOrDefault();
            Console.WriteLine("Name: {0}", notDefaultStorageName.Name);
            Console.WriteLine("IsDefault: {0}", notDefaultStorageName.IsDefault);

            // Create the original asset in the default storage account.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None,
                defaultStorageName.Name, _singleInputFilePath);
            Console.WriteLine("Created the asset in the {0} storage account", asset.StorageAccountName);

            // Create an output asset of the encoding job in the other storage account.
            IAsset outputAsset = CreateEncodingJob(asset, notDefaultStorageName.Name, _singleInputFilePath);
            if (outputAsset != null)
                Console.WriteLine("Created the output asset in the {0} storage account", outputAsset.StorageAccountName);

        }

        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string storageName, string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();

            // If you are creating an asset in the default storage account, you can omit the StorageName parameter.
            var asset = _context.Assets.Create(assetName, storageName, assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);

            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static IAsset CreateEncodingJob(IAsset asset, string storageName, string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My encoding job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset", storageName,
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish. 
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // Get an updated job reference.
            job = GetJob(job.Id);

            // If job state is Error the event handling 
            // method for job progress should log errors.  Here we check 
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                Console.WriteLine("\nExiting method due to job error.");
                return null;
            }

            // Get a reference to the output asset from the job.
            IAsset outputAsset = job.OutputMediaAssets[0];

            return outputAsset;
        }

        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }

        private static void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("********************");
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine("Please wait while local tasks or downloads complete...");
                    Console.WriteLine("********************");
                    Console.WriteLine();
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
                    Console.WriteLine("An error occurred in {0}", job.Id);
                    break;
                default:
                    break;
            }
        }

        static IJob GetJob(string jobId)
        {
            // Use a Linq select query to get an updated 
            // reference by Id. 
            var jobInstance =
                from j in _context.Jobs
                where j.Id == jobId
                select j;
            // Return the job reference as an Ijob. 
            IJob job = jobInstance.FirstOrDefault();

            return job;
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

