---
title: Azure-Notification Hubs registraties in bulk exporteren en importeren | Microsoft Docs
description: Meer informatie over het gebruik van Notification Hubs bulksgewijze ondersteuning voor het uitvoeren van een groot aantal bewerkingen op een notification hub of voor het exporteren van alle registraties.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 73c19b72c75d9638213f3c813a708a272cbe2a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102452336"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Azure Notification Hubs-registraties bulksgewijs exporteren en importeren

Er zijn scenario's waarin het nodig is om grote aantallen registraties te maken of te wijzigen in een notification hub. Enkele van deze scenario's zijn label updates na batch-berekeningen of migreren een bestaande push-implementatie om Azure Notification Hubs te gebruiken.

In dit artikel wordt uitgelegd hoe u een groot aantal bewerkingen op een notification hub uitvoert of alle registraties in bulk exporteert.

## <a name="high-level-flow"></a>Stroom op hoog niveau

Batch ondersteuning is ontworpen ter ondersteuning van langlopende taken waarbij miljoenen registraties worden gebruikt. Om deze schaal te verzorgen, gebruikt batch ondersteuning Azure Storage om taak gegevens en uitvoer op te slaan. De gebruiker is verplicht om een bestand te maken in een BLOB-container, waarvan de inhoud de lijst met registratie-update bewerkingen is. Bij het starten van de taak levert de gebruiker een URL naar de invoer-blob, samen met een URL naar een uitvoermap (ook in een BLOB-container). Nadat de taak is gestart, kan de gebruiker de status controleren door een query uit te zoeken naar een URL-locatie die aan het begin van de taak is gegeven. Een specifieke taak kan alleen bewerkingen uitvoeren van een specifieke soort (maken, bijwerken of verwijderen). Export bewerkingen worden analoog uitgevoerd.

## <a name="import"></a>Importeren

### <a name="set-up"></a>Instellen

In deze sectie wordt ervan uitgegaan dat u de volgende entiteiten hebt:

- Een ingerichte notification hub.
- Een Azure Storage BLOB-container.
- Verwijst naar het [Azure Storage NuGet-pakket](https://www.nuget.org/packages/windowsazure.storage/) en het [Notification hubs NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Invoer bestand maken en opslaan in een BLOB

Een invoer bestand bevat een lijst met registraties die zijn geserialiseerd in XML, één per rij. In het volgende code voorbeeld van de Azure SDK ziet u hoe u de registraties kunt serialiseren en uploaden naar de BLOB-container:

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(registrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> Met de voor gaande code worden de registraties in het geheugen gedecodeerd en wordt vervolgens de volledige stream geüpload naar een blob. Als u een bestand van meer dan slechts een paar mega bytes hebt geüpload, raadpleegt u de Azure Blob Guidance voor informatie over het uitvoeren van deze stappen. bijvoorbeeld blok- [blobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>URL-tokens maken

Zodra uw invoer bestand is geüpload, genereert u de Url's om uw notification hub te voorzien van het invoer bestand en de uitvoermap. U kunt twee verschillende BLOB-containers gebruiken voor invoer en uitvoer.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>De taak verzenden

Met de twee invoer-en uitvoer-Url's kunt u de batch-taak nu starten.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

Naast de invoer-en uitvoer-Url's maakt dit voor beeld een- `NotificationHubJob` object dat een object bevat. dit `JobType` kan een van de volgende typen zijn:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Zodra de aanroep is voltooid, wordt de taak voortgezet door de notification hub en kunt u de status controleren met de aanroep van [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync).

Wanneer de taak is voltooid, kunt u de resultaten controleren door de volgende bestanden in de map uitvoermap te bekijken:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Deze bestanden bevatten de lijst met geslaagde en mislukte bewerkingen van de batch. De bestands indeling is `.cvs` , waarin elke rij het regel nummer van het oorspronkelijke invoer bestand bevat en de uitvoer van de bewerking (doorgaans de gemaakte of bijgewerkte registratie beschrijving).

### <a name="full-sample-code"></a>Volledige voorbeeld code

Met de volgende voorbeeld code worden registraties in een notification hub geïmporteerd.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            // Write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            // Import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(registrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Exporteren

Het exporteren van de registratie is vergelijkbaar met de import, met de volgende verschillen:

- U hebt alleen de uitvoer-URL nodig.
- U maakt een NotificationHubJob van het type ExportRegistrations.

### <a name="sample-code-snippet"></a>Voorbeeld code fragment

Hier volgt een voor beeld van een code fragment voor het exporteren van registraties in Java:

```java
// Submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// Wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over registraties:

- [Registratiebeheer](notification-hubs-push-notification-registration-management.md)
- [Labels voor registraties](notification-hubs-tags-segment-push-message.md)
- [Sjabloonregistraties](notification-hubs-templates-cross-platform-push-messages.md)
