---
title: H264 single bitrate 4.000 Media Encoder Standard vooraf ingesteld-Azure | Microsoft Docs
description: In dit artikel wordt een overzicht gegeven van de voor instelling Media Encoder Standard H264 single bitrate voor 4.000 taken.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 8e437aea-8193-49a0-9ff2-4fd391c80972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 62984c938a3db67550b36c731e7a5a27de5df7ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012867"
---
# <a name="h264-single-bitrate-4k"></a>H264 Single Bitrate 4K

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` Hiermee definieert u een set coderings definities die u kunt gebruiken bij het maken van coderings taken. U kunt een gebruiken om aan te `preset name` geven welke indeling uw media bestand moet coderen. U kunt ook uw eigen voor keuren voor JSON of XML maken (met UTF-8-of UTF-16-code ring. Vervolgens geeft u de aangepaste voor instelling door aan het coderings programma. Zie voor de lijst met alle vooraf gedefinieerde namen die worden ondersteund door dit `Media Encoder Standard` coderings programma, voor [instellingen voor taken voor Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 In dit onderwerp worden de `H264 Single Bitrate 4K` voor instellingen in de XML-en JSON-indeling weer gegeven.  
  
 Deze vooraf ingestelde produceert een enkel MP4-bestand met een bitrate van 18000 kbps en stereo AAC-audio. Voor gedetailleerde informatie over profiel, bitrate, sampling frequentie, enzovoort, bekijkt u de hieronder gedefinieerde XML of JSON. Zie het onderwerp [Media Encoder Standard schema](media-services-mes-schema.md) voor uitleg over wat elk-element in deze voor instellingen betekent en de geldige waarden voor elk element.  
  
> [!NOTE]
>  U moet het type Premium gereserveerde eenheid ophalen met de code van 4.000 kB. Zie [How to scale encoding](./media-services-scale-media-processing-overview.md)(Engelstalig) voor meer informatie.  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>18000</Bitrate>  
          <Width>3840</Width>  
          <Height>2160</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>18000</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
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
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 18000,  
          "MaxBitrate": 18000,  
          "BufferWindow": "00:00:05",  
          "Width": 3840,  
          "Height": 2160,  
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
