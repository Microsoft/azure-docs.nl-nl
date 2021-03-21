---
title: Een Asset coderen met Media Encoder Standard met behulp van .NET | Microsoft Docs
description: In dit artikel wordt beschreven hoe u .NET kunt gebruiken om een Asset te coderen met behulp van Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: anilmur
ms.custom: devx-track-csharp
ms.openlocfilehash: b94ec0b50182381d2a4e1bca09f4c8bc482d13ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014091"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Een Asset coderen met Media Encoder Standard met behulp van .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Coderings taken zijn een van de meest voorkomende verwerkings bewerkingen in Media Services. U maakt coderings taken voor het converteren van media bestanden van de ene code ring naar een andere. Wanneer u codeert, kunt u de ingebouwde Media Encoder van Media Services gebruiken. U kunt ook een coderings programma van een Media Services partner gebruiken. coderings Programma's van derden zijn beschikbaar via de Azure Marketplace. 

In dit artikel wordt beschreven hoe u .NET kunt gebruiken om uw assets te coderen met Media Encoder Standard (MES). Media Encoder Standard is geconfigureerd met een van de voor instellingen van coderings Programma's die [hier](./media-services-mes-presets-overview.md)worden beschreven.

Het is raadzaam om uw bron bestanden altijd te coderen in een adaptieve bitsnelheid MP4 en de set vervolgens te converteren naar de gewenste indeling met behulp van de [dynamische verpakking](media-services-dynamic-packaging-overview.md). 

Als uw uitvoer activum is versleuteld, moet u het beleid voor de levering van assets configureren. Zie voor meer informatie beleid voor het [leveren van assets configureren](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES produceert een uitvoer bestand met een naam die de eerste 32 tekens van de naam van het invoer bestand bevat. De naam is gebaseerd op wat is opgegeven in het vooraf ingestelde bestand. Bijvoorbeeld "bestands naam": "{basispad} _ {index} {extension}". {Basispad} wordt vervangen door de eerste 32 tekens van de naam van het invoer bestand.
> 
> 

### <a name="mes-formats"></a>MES-indelingen
[Indelingen en codecs](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>MES-standaardinstellingen
Media Encoder Standard is geconfigureerd met een van de voor instellingen van coderings Programma's die [hier](./media-services-mes-presets-overview.md)worden beschreven.

### <a name="input-and-output-metadata"></a>Invoer-en uitvoer meta gegevens
Wanneer u een invoer activum (of activa) codeert met behulp van MES, ontvangt u een uitvoer activum wanneer de versleutelings taak is voltooid. Het uitvoer element bevat video, audio, miniaturen, manifest enz. op basis van de voor instelling voor de code ring die u gebruikt.

Het uitvoer activum bevat ook een bestand met meta gegevens over de invoer Asset. De naam van het XML-bestand met meta gegevens heeft de volgende indeling: <asset_id # C1_metadata.xml (bijvoorbeeld 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), waarbij <asset_id> de AssetId-waarde van de invoer-Asset is. Het schema van deze XML voor invoer van meta gegevens wordt [hier](media-services-input-metadata-schema.md)beschreven.

Het uitvoer activum bevat ook een bestand met meta gegevens over het uitvoer element. De naam van het XML-bestand met meta gegevens heeft de volgende indeling: <source_file_name # C1_manifest.xml (bijvoorbeeld BigBuckBunny_manifest.xml). Het schema van deze XML voor uitvoer meta gegevens wordt [hier](media-services-output-metadata-schema.md)beschreven.

Als u een van de twee meta gegevensbestanden wilt controleren, kunt u een SAS-Locator maken en het bestand downloaden naar uw lokale computer. Hier vindt u een voor beeld van hoe u een SAS-Locator maakt en een bestand downloadt met behulp van de Media Services .NET SDK-extensies.

## <a name="download-sample"></a>Voorbeeld downloaden
U kunt een voor beeld ophalen en uitvoeren dat laat zien hoe u deze codeert met [mes.](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/)

## <a name="net-sample-code"></a>.NET-voorbeeld code

In het volgende code voorbeeld wordt Media Services .NET SDK gebruikt om de volgende taken uit te voeren:

* Maak een coderings taak.
* Een verwijzing naar de Media Encoder Standard encoder ophalen.
* Geef op of u de voor instelling [adaptief streamen](media-services-autogen-bitrate-ladder-with-mes.md) wilt gebruiken. 
* Voeg één coderings taak aan de taak toe. 
* Geef op welke invoer-Asset moet worden gecodeerd.
* Maak een uitvoer activum dat het gecodeerde activum bevat.
* Voeg een gebeurtenis-handler toe om de voortgang van de taak te controleren.
* Verzend de taak.

#### <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

Stel uw ontwikkel omgeving in en vul het app.config bestand in met verbindings informatie, zoals beschreven in [Media Services ontwikkeling met .net](media-services-dotnet-how-to-use.md). 

#### <a name="example"></a>Voorbeeld 

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderStandardSample
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

            // Encode and generate the output using the "Adaptive Streaming" preset.
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

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "Adaptive Streaming",
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

## <a name="advanced-encoding-features-to-explore"></a>Geavanceerde coderings functies voor verkennen
* [Miniaturen genereren](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Miniaturen genereren tijdens het coderen](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Video's bijsnijden tijdens het coderen](media-services-crop-video.md)
* [Coderings definities aanpassen](media-services-custom-mes-presets-with-dotnet.md)
* [Een video met een afbeelding bedekkend of in een water merk](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
[Miniatuur genereren met behulp van Media Encoder Standard met .net](media-services-dotnet-generate-thumbnail-with-mes.md) 
 [Overzicht van Media Services encoding](media-services-encode-asset.md)