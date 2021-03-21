---
title: Geavanceerde code ring uitvoeren door MES-voor instellingen aan te passen | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u geavanceerde code ring kunt uitvoeren door Media Encoder Standard voor instellingen voor de taak aan te passen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 345fd56e5ff069ea222661a4820e75d96a9e2395
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014499"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Geavanceerde code ring uitvoeren door MES-voor instellingen aan te passen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Overzicht

In dit onderwerp wordt beschreven hoe u Media Encoder Standard voor instellingen kunt aanpassen. De [code ring met Media Encoder Standard met aangepaste voor instellingen](media-services-custom-mes-presets-with-dotnet.md) laat zien hoe u .net gebruikt voor het maken van een coderings taak en een taak waarmee deze taak wordt uitgevoerd. Zodra u een voor instelling hebt aangepast, geeft u de aangepaste voor instellingen op voor de coderings taak. 

Als u een XML-voor instelling gebruikt, moet u ervoor zorgen dat de volg orde van de elementen wordt behouden, zoals wordt weer gegeven in de onderstaande XML-voor beelden (KeyFrameInterval moet voorafgaan aan SceneChangeDetection).

> [!NOTE] 
> Veel van de geavanceerde Media Services v2-functies van de Media Encoder Standard zijn momenteel niet beschikbaar in v3. Zie [de migratie handleiding](../latest/migrate-v-2-v-3-migration-introduction.md)voor meer informatie.

## <a name="support-for-relative-sizes"></a>Ondersteuning voor relatieve grootten

Bij het genereren van miniaturen hoeft u niet altijd de uitvoer breedte en-hoogte in pixels op te geven. U kunt deze opgeven in percentages, in het bereik [1%,..., 100%].

### <a name="json-preset"></a>JSON-voor instelling

```json
"Width": "100%",
"Height": "100%"
```

### <a name="xml-preset"></a>XML-voor instelling

```xml
<Width>100%</Width>
<Height>100%</Height>
```

## <a name="generate-thumbnails"></a>Miniaturen genereren

In deze sectie ziet u hoe u een voor instelling kunt aanpassen waarmee miniaturen worden gegenereerd. De hieronder gedefinieerde definitie bevat informatie over hoe u uw bestand wilt coderen en de informatie die nodig is om miniaturen te genereren. U kunt een van de MES-voor instellingen volgen die in [deze](media-services-mes-presets-overview.md) sectie worden beschreven en code toevoegen waarmee miniaturen worden gegenereerd.  

> [!NOTE]
> De instelling **SceneChangeDetection** in de volgende vooraf ingestelde voor instelling kan alleen worden ingesteld op True als u codeert naar een video met één bitsnelheid. Als u codeert naar een multi-bitrate video en **SceneChangeDetection** instelt op True, retourneert het coderings programma een fout.  
>
>

Zie dit onderwerp voor meer informatie over [het](media-services-mes-schema.md) schema.

Zorg ervoor dat u de sectie [overwegingen](#considerations) bekijkt.

### <a name="json-preset"></a>JSON-voor instelling

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
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "PngLayers": [
        {
          "Type": "PngLayer",
          "Width": 640,
          "Height": 360,
        }
      ],
      "Start": "00:00:01",
      "Step": "00:00:10",
      "Range": "00:00:58",
      "Type": "PngImage"
    },
    {
      "BmpLayers": [
        {
          "Type": "BmpLayer",
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "10%",
      "Step": "10%",
      "Range": "90%",
      "Type": "BmpImage"
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
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "PngFormat"
      }
    },
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "BmpFormat"
      }
    },
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
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
          <Width>640</Width>
          <Height>360</Height>
          <Quality>90</Quality>
        </JpgLayer>
      </JpgLayers>
    </JpgImage>
    <BmpImage Start="10%" Step="10%" Range="90%">
      <BmpLayers>
        <BmpLayer>
          <Width>640</Width>
          <Height>360</Height>
        </BmpLayer>
      </BmpLayers>
    </BmpImage>
    <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
      <PngLayers>
        <PngLayer>
          <Width>640</Width>
          <Height>360</Height>
        </PngLayer>
      </PngLayers>
    </PngImage>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <JpgFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <BmpFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <PngFormat />
    </Output>
  </Outputs>
</Preset>
```

### <a name="considerations"></a>Overwegingen

De volgende overwegingen zijn van toepassing:

* Het gebruik van expliciete tijds tempels voor begin/stap/bereik veronderstelt dat de invoer bron ten minste 1 minuut lang is.
* Jpg/png/BmpImage-elementen hebben een teken reeks kenmerken van het soort begin, stap en bereik, die kunnen worden geïnterpreteerd als:

  * Frame nummer als het een niet-negatief geheel getal is, bijvoorbeeld "Start": "120",
  * Ten opzichte van bron duur als%-achtervoegsel, bijvoorbeeld "Start": "15%" of
  * Tijds tempel indien uitgedrukt als uu: MM: SS... Format, bijvoorbeeld "Start": "00:01:00"

    U kunt de notaties combi neren en vergelijken.

    Daarnaast ondersteunt start ook een speciale macro: {Best}, waarmee wordt geprobeerd het eerste interessante frame van de inhouds notitie te bepalen: (stap en bereik worden genegeerd wanneer start is ingesteld op {best})
  * Standaard waarden: starten: {Best}
* De uitvoer indeling moet expliciet worden gegeven voor elke indeling van de afbeelding: jpg/png/BmpFormat. Indien aanwezig, komt MES overeen met JpgVideo in JpgFormat, enzovoort. Output Format introduceert een nieuwe afbeelding-codec specifieke macro: {index}, die aanwezig moet zijn (slechts één keer en slechts één keer) voor uitvoer indelingen voor installatie kopieën.

## <a name="trim-a-video-clipping"></a><a id="trim_video"></a>Een video knippen (knippen)
In deze sectie vindt u informatie over het wijzigen van de coderings instellingen om de invoer video te knippen of bij te snijden, waarbij de invoer een zogenaamde mezzanine-bestand of een bestand op aanvraag is. Het coderings programma kan ook worden gebruikt voor het knippen of bijsnijden van een activum, dat wordt vastgelegd of gearchiveerd vanuit een live stream. de Details voor dit zijn beschikbaar in [deze blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Als u uw Video's wilt bijsnijden, kunt u een van de MES-voor instellingen volgen die in [deze](media-services-mes-presets-overview.md) sectie worden beschreven en het **bron** element wijzigen (zoals hieronder wordt weer gegeven). De waarde van StartTime moet overeenkomen met de absolute tijds tempels van de invoer video. Als bijvoorbeeld het eerste frame van de invoer video een tijds tempel van 12:00:10.000 heeft, moet StartTime ten minste 12:00:10.000 en groter zijn. In het onderstaande voor beeld gaan we ervan uit dat de invoer video een begin-time stamp van nul heeft. **Bronnen** moeten aan het begin van de voor instelling worden geplaatst.

### <a name="json-preset"></a><a id="json"></a>JSON-voor instelling

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "StartTime": "00:00:04",
      "Duration": "00:00:16"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "StretchMode": "AutoSize",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1500,
          "MaxBitrate": 1500,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
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

### <a name="xml-preset"></a>XML-voor instelling
Als u uw Video's wilt bijsnijden, kunt u de MES-voor instellingen die [hier](media-services-mes-presets-overview.md) worden beschreven, uitvoeren en het element **bronnen** wijzigen (zoals hieronder wordt weer gegeven).

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source StartTime="PT4S" Duration="PT14S"/>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>3400</Bitrate>
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
          <MaxBitrate>3400</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>2250</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>2250</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1500</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1500</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1000</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1000</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>650</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>650</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>400</Bitrate>
          <Width>320</Width>
          <Height>180</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>400</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>128</Bitrate>
    </AACAudio>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="create-an-overlay"></a><a id="overlay"></a>Een overlay maken

Met de Media Encoder Standard kunt u een installatie kopie op een bestaande video bedekken. Momenteel worden de volgende indelingen ondersteund: PNG, JPG, GIF en BMP. De definitie die hieronder is gedefinieerd, is een eenvoudig voor beeld van een video-overlay.

Naast het definiëren van een vooraf ingesteld bestand, moet u ook Media Services weten welk bestand in het activum de bedekkings afbeelding is en welk bestand de bron video is waarop u de installatie kopie wilt bedekken. Het video bestand moet het **primaire** bestand zijn.

Als u .NET gebruikt, voegt u de volgende twee functies toe aan het .NET-voor beeld dat in [Dit](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) onderwerp is gedefinieerd. Met de functie **UploadMediaFilesFromFolder** uploadt u bestanden uit een map (bijvoorbeeld BigBuckBunny.mp4 en Image001.png) en stelt u het MP4-bestand in als primair bestand in het-element. De functie **EncodeWithOverlay** maakt gebruik van het aangepaste vooraf ingestelde bestand dat aan het is door gegeven (bijvoorbeeld de vooraf ingestelde voor instelling) om de coderings taak te maken.

```csharp
static public IAsset UploadMediaFilesFromFolder(string folderPath)
{
    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);

    foreach (var af in asset.AssetFiles)
    {
        // The following code assumes 
        // you have an input folder with one MP4 and one overlay image file.
        if (af.Name.Contains(".mp4"))
            af.IsPrimary = true;
        else
            af.IsPrimary = false;

        af.Update();
    }

    return asset;
}

static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
{
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(customPresetFileName);

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input assets to be encoded.
    // This asset contains a source file and an overlay file.
    task.InputAssets.Add(assetSource);

    // Add an output asset to contain the results of the job. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

    return job.OutputMediaAssets[0];
}
```

> [!NOTE]
> Huidige beperkingen:
>
> De instelling dekking dekken wordt niet ondersteund.
>
> Het bron video bestand en het overlay-afbeeldings bestand moeten zich in hetzelfde activum bestemmen en het video bestand moet worden ingesteld als het primaire bestand in deze asset.
>
>

### <a name="json-preset"></a>JSON-voor instelling

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "VideoOverlay": {
          "Position": {
            "X": 100,
            "Y": 100,
            "Width": 100,
            "Height": 50
          },
          "AudioGainLevel": 0.0,
          "MediaParams": [
            {
              "OverlayLoopCount": 1
            },
            {
              "IsOverlay": true,
              "OverlayLoopCount": 1
            }
          ],
          "Source": "Image001.png",
          "Clip": {
            "Duration": "00:00:05"
          },
          "FadeInDuration": {
            "Duration": "00:00:01"
          },
          "FadeOutDuration": {
            "StartTime": "00:00:03",
            "Duration": "00:00:04"
          }
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1045,
          "MaxBitrate": 1045,
          "BufferWindow": "00:00:05",
          "ReferenceFrames": 3,
          "EntropyMode": "Cavlc",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Type": "CopyAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}{Extension}",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="xml-preset"></a>XML-voor instelling

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source>
      <Streams />
      <Filters>
        <VideoOverlay>
          <Source>Image001.png</Source>
          <Clip Duration="PT5S" />
          <FadeInDuration Duration="PT1S" />
          <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
          <Position X="100" Y="100" Width="100" Height="50" />
          <Opacity>0</Opacity>
          <AudioGainLevel>0</AudioGainLevel>
          <MediaParams>
            <MediaParam>
              <IsOverlay>false</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
            <MediaParam>
              <IsOverlay>true</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
          </MediaParams>
        </VideoOverlay>
      </Filters>
      <Pad>true</Pad>
    </Source>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>1045</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>0</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cavlc</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1045</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <CopyAudio />
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}{Extension}">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>Een Silent audio-track invoegen wanneer de invoer geen audio heeft
Standaard, als u een invoer verzendt naar het coderings programma dat alleen video bevat en geen audio, bevat het uitvoer element alleen bestanden die alleen video gegevens bevatten. Sommige spelers kunnen dergelijke uitvoer stromen mogelijk niet verwerken. U kunt deze instelling gebruiken om ervoor te zorgen dat het coderings programma een Silent audio-track toevoegt aan de uitvoer in dat scenario.

Geef de ' InsertSilenceIfNoAudio-waarde op als u wilt afdwingen dat het coderings programma een Asset produceert die een Silent audio-track bevat wanneer de invoer geen audio heeft.

U kunt de in [deze](media-services-mes-presets-overview.md) sectie beschreven mes-voor instellingen uitvoeren en de volgende wijziging aanbrengen:

### <a name="json-preset"></a>JSON-voor instelling

```json
{
  "Channels": 2,
  "SamplingRate": 44100,
  "Bitrate": 96,
  "Type": "AACAudio",
  "Condition": "InsertSilenceIfNoAudio"
}
```

### <a name="xml-preset"></a>XML-voor instelling

```xml
<AACAudio Condition="InsertSilenceIfNoAudio">
  <Channels>2</Channels>
  <SamplingRate>44100</SamplingRate>
  <Bitrate>96</Bitrate>
</AACAudio>
```

## <a name="disable-auto-de-interlacing"></a><a id="deinterlacing"></a>Automatische uninterliniëring uitschakelen
Klanten hoeven niets te doen als de inhoud van de interliniëring automatisch wordt geïnterlinieerd. Wanneer de automatische-interliniëring is ingeschakeld (standaard), voert de MES de automatische detectie van geïnterlinieerde frames en alleen de interlaced frames uit die zijn gemarkeerd als geïnterlinieerd.

U kunt de automatische deinterliniëring uitschakelen. Deze optie wordt niet aanbevolen.

### <a name="json-preset"></a>JSON-voor instelling

```json
"Sources": [
  {
    "Filters": {
      "Deinterlace": {
        "Mode": "Off"
      }
    },
  }
]
```

### <a name="xml-preset"></a>XML-voor instelling

```xml
<Sources>
  <Source>
    <Filters>
      <Deinterlace>
        <Mode>Off</Mode>
      </Deinterlace>
    </Filters>
  </Source>
</Sources>
```

## <a name="audio-only-presets"></a><a id="audio_only"></a>Voor instellingen voor alleen audio
In deze sectie ziet u twee voor beelden van alleen audio-inkomend niveau: AAC-audio en de audio van de juiste kwaliteit van AAC.

### <a name="aac-audio"></a>AAC-audio

```json
{
  "Version": 1.0,
  "Codecs": [
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
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="aac-good-quality-audio"></a>Audio van goede kwaliteit van AAC

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 192,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="concatenate-two-or-more-video-files"></a><a id="concatenate"></a>Twee of meer video bestanden samen voegen

In het volgende voor beeld ziet u hoe u een voor instelling kunt genereren om twee of meer video bestanden samen te voegen. Het meest voorkomende scenario is wanneer u een kop of een trailer wilt toevoegen aan de hoofd video. Het beoogde gebruik is het moment waarop de video bestanden worden bewerkt: share-eigenschappen (video resolutie, frame frequentie, audio track Count, enzovoort). Houd er rekening mee dat u geen Video's van verschillende frame snelheden kunt combi neren of met een ander aantal audio sporen.

>[!NOTE]
>Het huidige ontwerp van de functie voor samen voeging verwacht dat de video clips voor invoer consistent zijn met de voor waarden van de oplossing, de frame frequentie, enzovoort. 

### <a name="requirements-and-considerations"></a>Vereisten en overwegingen

* Invoer Video's mogen slechts één audio track hebben.
* Invoer Video's moeten allemaal dezelfde frame frequentie hebben.
* U moet uw Video's in afzonderlijke assets uploaden en de Video's als primair bestand in elk activum instellen.
* U moet weten wat de duur van uw Video's is.
* In de onderstaande voor beelden wordt ervan uitgegaan dat alle invoer Video's beginnen met een tijds tempel van nul. U moet de waarden voor StartTime wijzigen als de Video's een andere start tijds tempel hebben, zoals het geval is bij Live-archieven.
* De JSON-voor instelling maakt expliciete verwijzingen naar de AssetID-waarden van de invoer assets.
* In de voorbeeld code wordt ervan uitgegaan dat de JSON-voor instelling is opgeslagen in een lokaal bestand, zoals "C:\supportFiles\preset.jsop". Ook wordt ervan uitgegaan dat er twee assets zijn gemaakt door twee video bestanden te uploaden en dat u de resulterende AssetID-waarden kent.
* In het code fragment en de JSON-voor instelling ziet u een voor beeld van het samen voegen van twee video bestanden. U kunt dit uitbreiden naar meer dan twee Video's door:

  1. Taak aanroepen. InputAssets. Add () herhaaldelijk om meer Video's toe te voegen, in de juiste volg orde.
  2. Het maken van corresponderende wijzigingen in het element sources in de JSON door meer vermeldingen toe te voegen in dezelfde volg orde.

### <a name="net-code"></a>.NET-code

```csharp
IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

// Declare a new job.
IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
// Get a media processor reference, and pass to it the name of the
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

// Load the XML (or JSON) from the local file.
string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

// Create a task
ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
    processor,
    configuration,
    TaskOptions.None);

// Specify the input videos to be concatenated (in order).
task.InputAssets.Add(asset1);
task.InputAssets.Add(asset2);
// Add an output asset to contain the results of the job.
// This output is specified as AssetCreationOptions.None, which
// means the output asset is not encrypted.
task.OutputAssets.AddNew("Output asset",
    AssetCreationOptions.None);

job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
job.Submit();
job.GetExecutionProgressTask(CancellationToken.None).Wait();
```

### <a name="json-preset"></a>JSON-voor instelling

Werk uw aangepaste voor instelling bij met id's van de activa die u wilt samen voegen en met het juiste tijd segment voor elke video.

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
      "StartTime": "00:00:01",
      "Duration": "00:00:15"
    },
    {
      "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
      "StartTime": "00:00:02",
      "Duration": "00:00:05"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": true,
      "H264Layers": [
        {
          "Level": "auto",
          "Bitrate": 1800,
          "MaxBitrate": 1800,
          "BufferWindow": "00:00:05",
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
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
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="crop-videos-with-media-encoder-standard"></a><a id="crop"></a>Video’s bijsnijden met Media Encoder Standard
Zie het onderwerp [Video's bijsnijden met Media Encoder Standard](media-services-crop-video.md) .

## <a name="insert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>Een video track invoegen wanneer de invoer geen video bevat

Standaard, als u een invoer verzendt naar het coderings programma dat alleen audio en geen video bevat, bevat het uitvoer element alleen bestanden die alleen audio gegevens bevatten. Sommige spelers, met inbegrip van Azure Media Player (Zie [Dit](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)), kunnen dergelijke stromen mogelijk niet verwerken. U kunt deze instelling gebruiken om ervoor te zorgen dat het coderings programma een monochroom video spoor toevoegt aan de uitvoer in dat scenario.

> [!NOTE]
> Het coderen van het coderings programma voor het invoegen van een uitvoer video spoor neemt de grootte van het uitvoer activum toe en de kosten die zijn gemaakt voor de coderings taak. Voer tests uit om te controleren of deze resulterende toename alleen een bescheiden effect heeft op uw maandelijkse kosten.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Video invoegen met alleen de laagste bitrate

Stel dat u een voor beeld van een meerdere bitsnelheid gebruikt, zoals [' H264 meerdere bitrate 720p '](media-services-mes-preset-h264-multiple-bitrate-720p.md) , waarmee u uw volledige invoer catalogus kunt coderen voor streamen, die een combi natie van video bestanden en bestanden met alleen audio gegevens bevat. In dit scenario kunt u, wanneer de invoer geen video bevat, ervoor zorgen dat het coderings programma een monochroom video track met alleen de laagste bitsnelheid kan invoegen, in tegens telling tot het invoegen van video op elke bitsnelheid van uitvoer. Hiervoor moet u de vlag **InsertBlackIfNoVideoBottomLayerOnly** gebruiken.

U kunt de in [deze](media-services-mes-presets-overview.md) sectie beschreven mes-voor instellingen uitvoeren en de volgende wijziging aanbrengen:

#### <a name="json-preset"></a>JSON-voor instelling

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>XML-voor instelling

Als XML wordt gebruikt, gebruikt u voor waarde = "InsertBlackIfNoVideoBottomLayerOnly" als een kenmerk voor het **H264Video** -element en de voor waarde = "InsertSilenceIfNoAudio" als een kenmerk voor **AACAudio**.

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>Video invoegen bij alle uitvoer bitrates
Stel dat u een multi bitsnelheid-voor instelling voor code ring gebruikt, zoals [' H264 meerdere bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) , om uw volledige invoer catalogus te coderen voor streaming, die een combi natie van video bestanden en bestanden met alleen audio gegevens bevat. In dit scenario is het mogelijk dat als de invoer geen video bevat, u wilt afdwingen dat het coderings programma een monochroom video spoor invoegt bij alle uitvoer bitrates. Dit zorgt ervoor dat uw uitvoer activa homo geen zijn ten opzichte van het aantal video sporen en audio sporen. Hiervoor moet u de vlag "InsertBlackIfNoVideo" opgeven.

U kunt de in [deze](media-services-mes-presets-overview.md) sectie beschreven mes-voor instellingen uitvoeren en de volgende wijziging aanbrengen:

#### <a name="json-preset"></a>JSON-voor instelling

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideo",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>XML-voor instelling

Als XML wordt gebruikt, gebruikt u voor waarde = "InsertBlackIfNoVideo" als een kenmerk voor het **H264Video** -element en de voor waarde = "InsertSilenceIfNoAudio" als een kenmerk voor **AACAudio**.

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a name="rotate-a-video"></a><a id="rotate_video"></a>Een video draaien
De [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) biedt ondersteuning voor rotaties op basis van de hoek van 0/90/180/270. Het standaard gedrag is ' auto ', waarbij wordt geprobeerd om de meta gegevens van de draai bewerking in het inkomende video bestand te detecteren en te compenseren. Neem het volgende **bron** element op in een van de voor instellingen die in [deze](media-services-mes-presets-overview.md) sectie zijn gedefinieerd:

### <a name="json-preset"></a>JSON-voor instelling

```json
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
  ],
  "Codecs": [

    ...
```

### <a name="xml-preset"></a>XML-voor instelling

```xml
<Sources>
  <Source>
    <Streams />
    <Filters>
      <Rotation>90</Rotation>
    </Filters>
  </Source>
</Sources>
```

Zie ook [Dit](media-services-mes-schema.md#PreserveResolutionAfterRotation) onderwerp voor meer informatie over de manier waarop de encoder de instellingen voor breedte en hoogte interpreteert in de voor instelling, wanneer rotatie compensatie wordt geactiveerd.

U kunt de waarde "0" gebruiken om aan te geven dat de encoder de meta gegevens van de draaiing negeren, indien aanwezig, in de video invoer.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Overzicht van Media Services encoding](media-services-encode-asset.md)
