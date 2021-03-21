---
title: Gebruik Azure Queue Storage om Media Services taak meldingen te bewaken met .NET | Microsoft Docs
description: Meer informatie over het gebruik van Azure Queue-opslag om Media Services taak meldingen te bewaken. Het code voorbeeld is geschreven in C# en maakt gebruik van de Media Services SDK voor .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: f535d0b5-f86c-465f-81c6-177f4f490987
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: dc6ab94b3685e55125032b0afa52076eae72ba5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016216"
---
# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Gebruik Azure Queue Storage om Media Services taak meldingen te bewaken met .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Wanneer u coderings taken uitvoert, hebt u vaak een manier nodig om de voortgang van de taak bij te houden. U kunt Media Services configureren voor het leveren van meldingen aan [Azure Queue-opslag](../../storage/queues/storage-dotnet-how-to-use-queues.md). U kunt de voortgang van de taak controleren door meldingen van de wachtrij opslag te ontvangen. 

Berichten die aan de wachtrij opslag worden geleverd, kunnen overal ter wereld worden geopend. De Messa ging-architectuur voor wachtrij opslag is betrouwbaar en zeer schaalbaar. Het navragen van de wachtrij opslag voor berichten wordt aanbevolen om andere methoden te gebruiken.

Een veelvoorkomend scenario voor het Luis teren naar Media Services meldingen is als u een inhouds beheersysteem ontwikkelt dat een extra taak moet uitvoeren nadat een coderings taak is voltooid (bijvoorbeeld om de volgende stap in een werk stroom te activeren of om inhoud te publiceren).

In dit artikel wordt beschreven hoe u meldings berichten kunt ophalen uit de wachtrij opslag.  

## <a name="considerations"></a>Overwegingen
Houd rekening met het volgende bij het ontwikkelen van Media Services toepassingen die gebruikmaken van Queue Storage:

* Queue Storage biedt geen garantie voor de bestelde levering van First-in-first-out (FIFO). Zie [Azure queues and Azure service bus queues vergeleken en daarentegen](/previous-versions/azure/hh767287(v=azure.100))voor meer informatie.
* Queue Storage is geen push service. U moet de wachtrij navragen.
* U kunt een onbeperkt aantal wacht rijen hebben. Zie [Queue Service rest API](/rest/api/storageservices/queue-service-rest-api)voor meer informatie.
* Voor de wachtrij opslag gelden enkele beperkingen en specifieke informatie. Deze worden beschreven in [Azure queues en Azure service bus-wacht rijen vergeleken en daarentegen](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

## <a name="net-code-example"></a>Voor beeld van .NET-code

Het code voorbeeld in deze sectie doet het volgende:

1. Hiermee wordt de klasse **EncodingJobMessage** gedefinieerd die wordt toegewezen aan de indeling van het meldings bericht. De code deserialeert berichten die zijn ontvangen van de wachtrij in objecten van het type **EncodingJobMessage** .
2. Laadt de Media Services-en gegevens van het opslag account uit het app.config bestand. In het code voorbeeld wordt deze informatie gebruikt voor het maken van de **cloudmediacontext maakt** -en **CloudQueue** -objecten.
3. Hiermee maakt u de wachtrij die meldings berichten over de coderings taak ontvangt.
4. Hiermee maakt u het meldings eindpunt dat is toegewezen aan de wachtrij.
5. Het eind punt van de melding wordt gekoppeld aan de taak en de coderings taak wordt verzonden. Er kunnen meerdere eind punten aan een melding aan een taak zijn gekoppeld.
6. Hiermee wordt **NotificationJobState. FinalStatesOnly** door gegeven aan de methode **AddNew** . (In dit voor beeld zijn we alleen geïnteresseerd in de definitieve statussen van de taak verwerking.)

    ```csharp
    job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
    ```

7. Als u **NotificationJobState. alle** hebt, worden alle volgende status wijzigings meldingen weer gegeven: in de wachtrij geplaatst, gepland, verwerkt en voltooid. Zoals eerder vermeld, garandeert de wachtrij opslag echter geen bestelde levering. Voor het best Ellen van berichten gebruikt u de eigenschap **Time Stamp** (gedefinieerd in het type **EncodingJobMessage** in het onderstaande voor beeld). Dubbele berichten zijn mogelijk. Als u wilt controleren op dubbele waarden, gebruikt u de **ETAG-eigenschap** (gedefinieerd in het type **EncodingJobMessage** ). Het is ook mogelijk dat sommige meldingen over status wijziging worden overgeslagen.
8. Wacht tot de taak is voltooid, door de wachtrij elke 10 seconden te controleren. Verwijdert berichten nadat ze zijn verwerkt.
9. De wachtrij en het eind punt van de melding worden verwijderd.

> [!NOTE]
> De aanbevolen manier om de status van een taak te controleren, is door te Luis teren naar meldings berichten, zoals wordt weer gegeven in het volgende voor beeld:
>
> U kunt ook de status van een taak controleren met behulp van de eigenschap **IJob. State** .  Een meldings bericht over de voltooiing van een taak kan worden ontvangen voordat de status op **IJob** is ingesteld op **voltooid**. De eigenschap **IJob. State**  weerspiegelt de nauw keurige status met een lichte vertraging.
>
>

### <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

1. Stel uw ontwikkel omgeving in en vul het app.config bestand in met verbindings informatie, zoals beschreven in [Media Services ontwikkeling met .net](media-services-dotnet-how-to-use.md). 
2. Maak een nieuwe map (de map kan zich overal op uw lokale station bevinden) en kopieer een MP4-bestand dat u wilt coderen en streamen of progressief wilt downloaden. In dit voor beeld wordt het pad ' C:\Media ' gebruikt.
3. Voeg een verwijzing naar de bibliotheek **System. runtime. serialization** toe.

### <a name="code"></a>Code

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Collections.Generic;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Queue;
using System.Runtime.Serialization.Json;

namespace JobNotification
{
    public class EncodingJobMessage
    {
        // MessageVersion is used for version control.
        public String MessageVersion { get; set; }

        // Type of the event. Valid values are
        // JobStateChange and NotificationEndpointRegistration.
        public String EventType { get; set; }

        // ETag is used to help the customer detect if
        // the message is a duplicate of another message previously sent.
        public String ETag { get; set; }

        // Time of occurrence of the event.
        public String TimeStamp { get; set; }

        // Collection of values specific to the event.

        // For the JobStateChange event the values are:
        //     JobId - Id of the Job that triggered the notification.
        //     NewState- The new state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
        //     OldState- The old state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished

        // For the NotificationEndpointRegistration event the values are:
        //     NotificationEndpointId- Id of the NotificationEndpoint
        //          that triggered the notification.
        //     State- The state of the Endpoint.
        //          Valid values are: Registered and Unregistered.

        public IDictionary<string, object> Properties { get; set; }
    }

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

        private static readonly string _StorageConnectionString = 
            ConfigurationManager.AppSettings["StorageConnectionString"];

        private static CloudMediaContext _context = null;
        private static CloudQueue _queue = null;
        private static INotificationEndPoint _notificationEndPoint = null;

        private static readonly string _singleInputMp4Path =
            Path.GetFullPath(@"C:\Media\BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            string endPointAddress = Guid.NewGuid().ToString();

            // Create the context.
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
            
            // Create the queue that will be receiving the notification messages.
            _queue = CreateQueue(_StorageConnectionString, endPointAddress);

            // Create the notification point that is mapped to the queue.
            _notificationEndPoint =
                    _context.NotificationEndPoints.Create(
                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);


            if (_notificationEndPoint != null)
            {
                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                WaitForJobToReachedFinishedState(job.Id);
            }

            // Clean up.
            _queue.Delete();
            _notificationEndPoint.Delete();
        }


        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);

            // Create the queue client
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

            // Retrieve a reference to a queue
            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);

            // Create the queue if it doesn't already exist
            queue.CreateIfNotExists();

            return queue;
        }


        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");

            //Create an encrypted asset and upload the mp4.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted,
                inputMediaFilePath);

            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the conversion details, using a configuration file.
            ITask task = job.Tasks.AddNew("My encoding Task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Add a notification point to the job. You can add multiple notification points.  
            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly,
                _notificationEndPoint);

            job.Submit();

            return job;
        }

        public static void WaitForJobToReachedFinishedState(string jobId)
        {
            int expectedState = (int)JobState.Finished;
            int timeOutInSeconds = 600;

            bool jobReachedExpectedState = false;
            DateTime startTime = DateTime.Now;
            int jobState = -1;

            while (!jobReachedExpectedState)
            {
                // Specify how often you want to get messages from the queue.
                Thread.Sleep(TimeSpan.FromSeconds(10));

                foreach (var message in _queue.GetMessages(10))
                {
                    using (Stream stream = new MemoryStream(message.AsBytes))
                    {
                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                        settings.UseSimpleDictionaryFormat = true;
                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);

                        Console.WriteLine();

                        // Display the message information.
                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                        foreach (var property in encodingJobMsg.Properties)
                        {
                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                        }

                        // We are only interested in messages
                        // where EventType is "JobStateChange".
                        if (encodingJobMsg.EventType == "JobStateChange")
                        {
                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                            if (JobId == jobId)
                            {
                                string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                string newJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;

                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);

                                if (newJobState == (JobState)expectedState)
                                {
                                    Console.WriteLine("job with Id: {0} reached expected state: {1}",
                                        jobId, newJobState);
                                    jobReachedExpectedState = true;
                                    break;
                                }
                            }
                        }
                    }
                    // Delete the message after we've read it.
                    _queue.DeleteMessage(message);
                }

                // Wait until timeout
                TimeSpan timeDiff = DateTime.Now - startTime;
                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                if (timedOut)
                {
                    Console.WriteLine(@"Timeout for checking job notification messages,
                                        latest found state ='{0}', wait time = {1} secs",
                        jobState,
                        timeDiff.TotalSeconds);

                    throw new TimeoutException();
                }
            }
        }

        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(),
                assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);
            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading of {0}", assetFile.Name);

            return asset;
        }

        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
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

In het voor gaande voor beeld is de volgende uitvoer gegenereerd: de waarden variëren.

```output
Created assetFile BigBuckBunny.mp4
Upload BigBuckBunny.mp4
Done uploading of BigBuckBunny.mp4

EventType: NotificationEndPointRegistration
MessageVersion: 1.0
ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
TimeStamp: 2013-05-14T20:22:37
    NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
    State: Registered
    Name: dde957b2-006e-41f2-9869-a978870ac620
    Created: 2013-05-14T20:22:35

EventType: JobStateChange
MessageVersion: 1.0
ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
TimeStamp: 2013-05-14T20:24:40
    JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
    JobName: My MP4 to Smooth Streaming encoding job
    NewState: Finished
    OldState: Processing
    AccountName: westeuropewamsaccount
job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected
State: Finished
```

## <a name="next-step"></a>Volgende stap
Media Services-leertrajecten bekijken.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
