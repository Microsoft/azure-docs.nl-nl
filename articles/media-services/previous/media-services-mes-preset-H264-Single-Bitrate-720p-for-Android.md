---
title: H264e single bitrate 720p voor Android | Microsoft Docs
description: Het onderwerp bevat een overzicht van de **H264 single bitrate voor Android** -taak voorinstelling.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 4f9569a3-5aca-4fea-8242-024925a8af90
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: a05203925c0e731c1c05e0c97d0f67f997419970
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016437"
---
# <a name="h264-single-bitrate-720p-for-android"></a>H264 Single Bitrate 720p for Android

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

`Media Encoder Standard` Hiermee definieert u een set coderings definities die u kunt gebruiken bij het maken van coderings taken. U kunt een gebruiken om aan te `preset name` geven welke indeling uw media bestand moet coderen. U kunt ook uw eigen voor keuren voor JSON of XML maken (met UTF-8-of UTF-16-code ring. Vervolgens geeft u de aangepaste voor instelling door aan het coderings programma. Zie voor de lijst met alle vooraf gedefinieerde namen die worden ondersteund door dit `Media Encoder Standard` coderings programma, voor [instellingen voor taken voor Media Encoder Standard](media-services-mes-presets-overview.md).  
  
In dit onderwerp worden de `H264 Single Bitrate 720p for Android` voor instellingen in de XML-en JSON-indeling weer gegeven.  
  
Deze vooraf ingestelde produceert een enkel MP4-bestand met een bitrate van 2000 kbps en stereo AAC. Voor gedetailleerde informatie over profiel, bitrate, sampling frequentie, enzovoort, bekijkt u de hieronder gedefinieerde XML of JSON. Zie het onderwerp [Media Encoder Standard schema](media-services-mes-schema.md) voor uitleg over wat elk-element in deze voor instellingen betekent en de geldige waarden voor elk element.  
  
 XML  
  
```
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:05</KeyFrameInterval>
      <SceneChangeDetection>true</SceneChangeDetection>
      <H264Layers>
        <H264Layer>
          <Bitrate>2000</Bitrate>
          <Width>1280</Width>
          <Height>720</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Baseline</Profile>
          <Level>3.1</Level>
          <BFrames>0</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>false</AdaptiveBFrame>
          <EntropyMode>Cavlc</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>2000</MaxBitrate>
        </H264Layer>
      </H264Layers>
      <Chapters />
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>192</Bitrate>
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
      "KeyFrameInterval": "00:00:05",
      "SceneChangeDetection": true,
      "H264Layers": [
        {
          "Profile": "Baseline",
          "Level": "3.1",
          "Bitrate": 2000,
          "MaxBitrate": 2000,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cavlc",
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
      "Bitrate": 192,
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
