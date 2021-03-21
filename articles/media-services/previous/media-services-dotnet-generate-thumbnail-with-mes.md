---
title: Miniatuurweergaven genereren met Media Encoder Standard met .NET
description: In dit onderwerp wordt beschreven hoe u .NET kunt gebruiken om een Asset te coderen en tegelijkertijd miniaturen te genereren met behulp van Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 0159a706bd5f5229a12ab52bc92d240f01a2ed11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014040"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Miniatuurweergaven genereren met Media Encoder Standard met .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

U kunt Media Encoder Standard gebruiken om een of meer miniaturen te genereren op basis van uw invoer video in [JPEG](https://en.wikipedia.org/wiki/JPEG)-, [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)-of [BMP](https://en.wikipedia.org/wiki/BMP_file_format) -afbeeldings bestands indelingen. U kunt taken verzenden die alleen installatie kopieën produceren, of u kunt het genereren van miniaturen combi neren met code ring. In dit artikel vindt u enkele voor beelden van XML-en JSON-miniaturen voor dergelijke scenario's. Aan het einde van het artikel ziet u een [voorbeeld code](#code_sample) die laat zien hoe u de Media Services .NET SDK kunt gebruiken om de coderings taak uit te voeren.

Raadpleeg [Media Encoder Standard schema](media-services-mes-schema.md)voor meer informatie over de elementen die worden gebruikt in voorbeeld voorinstellingen.

Zorg ervoor dat u de sectie [overwegingen](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) bekijkt.
    
## <a name="example-of-a-single-png-file-preset"></a>Voor beeld van een standaard instelling voor één PNG-bestand

De volgende JSON en XML-voor instelling kunnen worden gebruikt voor het produceren van één uitvoer PNG-bestand van de eerste paar seconden van de invoer video, waarbij het coderings programma een beste poging doet bij het zoeken naar een interessant frame. Houd er rekening mee dat de afmetingen van de uitvoer afbeelding zijn ingesteld op 100%, wat betekent dat ze overeenkomen met de afmetingen van de invoer video. Houd er ook rekening mee dat de instelling ' notatie ' in ' uitvoer ' vereist is om te voldoen aan het gebruik van ' PngLayers ' in de sectie ' codecs '. 

### <a name="json-preset"></a>JSON-voor instelling

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
```
    
### <a name="xml-preset"></a>XML-voor instelling

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Voor beeld van een standaard instelling voor een ' reeks JPEG-afbeeldingen '

De volgende JSON en XML-voor instelling kunnen worden gebruikt voor het produceren van een set van 10 installatie kopieën met een tijds tempel van 5%, 15%,..., 95% van de invoer tijdlijn, waarbij de grootte van de afbeelding is ingesteld op één kwar taal van de invoer video.

### <a name="json-preset"></a>JSON-voor instelling

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-voor instelling
    
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Voor beeld van een definitie van een installatie kopie met een specifieke tijds tempel

De volgende JSON en XML-voor instelling kunnen worden gebruikt om één JPEG-afbeelding te maken met de 30-seconde van de invoer video. Deze vooraf ingesteld verwacht dat de invoer video meer dan 30 seconden lang is (anders mislukt de taak).

### <a name="json-preset"></a>JSON-voor instelling

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-voor instelling
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Voor beeld van een ' miniaturen met verschillende resoluties '

De volgende vooraf ingestelde voor instelling kan worden gebruikt om miniaturen te genereren met verschillende resoluties in één taak. In het voor beeld, op posities 5%, 15%,..., 95% van de invoer tijdlijn, genereert het coderings programma twee installatie kopieën: één op 100% van de video resolutie voor invoer en de andere op 50%.

Let op het gebruik van de macro {resolution} in de bestands naam; Hiermee wordt aangegeven dat het coderings programma de breedte en hoogte moet gebruiken die u hebt opgegeven in de sectie encoding van de voor instelling tijdens het genereren van de bestands naam van de uitvoer installatie kopieën. Dit helpt u ook bij het eenvoudig onderscheiden van de verschillende installatie kopieën

### <a name="json-preset"></a>JSON-voor instelling

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-voor instelling
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Voor beeld van het genereren van een miniatuur tijdens het coderen

Hoewel al deze voor beelden hebben besproken hoe u een coderings taak kunt verzenden die alleen installatie kopieën produceert, kunt u ook video-en audio codering combi neren met het genereren van miniaturen. Met de volgende vooraf ingestelde JSON en XML wordt **Media Encoder Standard** een miniatuur tijdens het coderen te genereren.

### <a name="json-preset"></a><a id="json"></a>JSON-voor instelling
Zie dit artikel voor meer informatie over [het](./media-services-mes-schema.md) schema.

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a><a id="xml"></a>XML-voor instelling
Zie dit artikel voor meer informatie over [het](./media-services-mes-schema.md) schema.

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   
```

## <a name="encode-video-and-generate-thumbnail-with-net"></a><a id="code_sample"></a>Video coderen en miniaturen genereren met .NET

In het volgende code voorbeeld wordt Media Services .NET SDK gebruikt om de volgende taken uit te voeren:

* Maak een coderings taak.
* Een verwijzing naar de Media Encoder Standard encoder ophalen.
* Laad de vooraf ingestelde [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) of [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) die de voor instelling voor de code ring bevat en de informatie die nodig is om miniaturen te genereren. U kunt deze  [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) of [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) opslaan in een bestand en de volgende code gebruiken om het bestand te laden.

    ```csharp
    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(fileName);  
    ```

* Voeg één coderings taak aan de taak toe. 
* Geef op welke invoer-Asset moet worden gecodeerd.
* Maak een uitvoer activum dat het gecodeerde activum bevat.
* Voeg een gebeurtenis-handler toe om de voortgang van de taak te controleren.
* Verzend de taak.

Zie het artikel [Media Services Development with .net](media-services-dotnet-how-to-use.md) voor instructies over het instellen van uw ontwikkel omgeving.

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace EncodeAndGenerateThumbnails
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

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
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

## <a name="considerations"></a>Overwegingen
De volgende overwegingen zijn van toepassing:

* Het gebruik van expliciete tijds tempels voor begin/stap/bereik veronderstelt dat de invoer bron ten minste 1 minuut lang is.
* Jpg/png/BmpImage-elementen hebben een teken reeks kenmerken van het soort begin, stap en bereik, die kunnen worden geïnterpreteerd als:
  
  * Frame nummer als het een niet-negatief geheel getal is, bijvoorbeeld "Start": "120",
  * Ten opzichte van bron duur als%-achtervoegsel, bijvoorbeeld "Start": "15%" of
  * Tijds tempel indien uitgedrukt als uu: MM: SS... als indeling. Bijvoorbeeld "Start": "00:01:00"
    
    U kunt de notaties combi neren en vergelijken.
    
    Daarnaast ondersteunt start ook een speciale macro: {Best}, waarmee wordt geprobeerd het eerste interessante frame van de inhouds notitie te bepalen: (stap en bereik worden genegeerd wanneer start is ingesteld op {best})
  * Standaard waarden: starten: {Best}
* De uitvoer indeling moet expliciet worden gegeven voor elke indeling van de afbeelding: jpg/png/BmpFormat. Indien aanwezig, komt MES overeen met JpgVideo in JpgFormat, enzovoort. Output Format introduceert een nieuwe afbeelding-codec specifieke macro: {index}, die aanwezig moet zijn (slechts één keer en slechts één keer) voor uitvoer indelingen voor installatie kopieën.

## <a name="next-steps"></a>Volgende stappen

U kunt de voortgang van de [taak](media-services-check-job-progress.md) controleren terwijl de coderings taak in behandeling is.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Overzicht van Media Services encoding](media-services-encode-asset.md)