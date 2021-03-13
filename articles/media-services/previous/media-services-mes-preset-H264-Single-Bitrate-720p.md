---
title: H264 single bitrate 720p Media Encoder Standard vooraf ingesteld-Azure | Microsoft Docs
description: Dit artikel geeft een overzicht van de Media Encoder Standard H264 single bitrate.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: f66da66c-9f21-441d-8038-51e3094e9307
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 2f772d8d881f64e91c4cb047b7587bd1f83b9b23
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011745"
---
# <a name="h264-single-bitrate-720p"></a>H264 Single Bitrate 720p

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` Hiermee definieert u een set coderings definities die u kunt gebruiken bij het maken van coderings taken. U kunt een gebruiken om aan te `preset name` geven welke indeling uw media bestand moet coderen. U kunt ook uw eigen voor keuren voor JSON of XML maken (met UTF-8-of UTF-16-code ring. Vervolgens geeft u de aangepaste voor instelling door aan het coderings programma. Zie voor de lijst met alle vooraf gedefinieerde namen die worden ondersteund door dit `Media Encoder Standard` coderings programma, voor [instellingen voor taken voor Media Encoder Standard](media-services-mes-presets-overview.md).  
  
 In dit onderwerp worden de `H264 Single Bitrate 720p` voor instellingen in de XML-en JSON-indeling weer gegeven.  
  
 Deze vooraf ingestelde produceert een enkel MP4-bestand met een bitrate van 4500 kbps en stereo AAC-audio. Voor gedetailleerde informatie over profiel, bitrate, sampling frequentie, enzovoort, bekijkt u de hieronder gedefinieerde XML of JSON. Zie het onderwerp [Media Encoder Standard schema](media-services-mes-schema.md) voor uitleg over wat elk-element in deze voor instellingen betekent en de geldige waarden voor elk element.  
  
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
          "Bitrate": 4500,  
          "MaxBitrate": 4500,  
          "BufferWindow": "00:00:05",  
          "Width": 1280,  
          "Height": 720,  
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
