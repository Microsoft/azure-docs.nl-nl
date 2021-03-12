---
title: Migreren van Windows Azure Media Encoder naar Media Encoder Standard | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u migreert van Windows Azure Media Encoder naar de Media Encoder Standard media processor.
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
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 0a80d25145162c81ef999f8af1015cd590d5a090
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011116"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Migreren van Windows Azure Media Encoder naar Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

In dit artikel worden de stappen beschreven voor het migreren van de verouderde Windows Azure Media Encoder (WAME)-media processor (die buiten gebruik wordt gesteld) naar de Media Encoder Standard-media processor. Voor de pensioen datums raadpleegt u dit onderwerp over [oudere onderdelen](legacy-components.md) .

Bij het coderen van bestanden met WAME gebruikt klanten doorgaans een vooraf ingestelde teken reeks, zoals `H264 Adaptive Bitrate MP4 Set 1080p` . Als u wilt migreren, moet uw code worden bijgewerkt om gebruik te kunnen maken van de **Media Encoder Standard** media processor in plaats van WAME, en een van de equivalente [voor instellingen](media-services-mes-presets-overview.md) van het systeem, zoals `H264 Multiple Bitrate 1080p` . 

## <a name="migrating-to-media-encoder-standard"></a>Migreren naar Media Encoder Standard

Hier volgt een voor beeld van een C#-code die gebruikmaakt van het verouderde onderdeel. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Dit is de bijgewerkte versie die gebruikmaakt van Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Geavanceerde scenario's 

Als u uw eigen coderings voorinstelling voor WAME hebt gemaakt met behulp van het bijbehorende schema, is er een [gelijkwaardig schema voor Media Encoder Standard](media-services-mes-schema.md).

## <a name="known-differences"></a>Bekende verschillen 

Media Encoder Standard is robuuster, betrouwbaarder, heeft betere prestaties en produceert een betere kwaliteit van de uitvoer dan het oudere WAME-coderings programma. Bovendien: 

* Media Encoder Standard produceert uitvoer bestanden met een andere naamgevings Conventie dan WAME.
* Media Encoder Standard produceert artefacten, zoals bestanden met de meta gegevens van het [invoer bestand](media-services-input-metadata-schema.md) en de [meta gegevens van het uitvoer bestand](media-services-output-metadata-schema.md).
* Zoals is beschreven op de [pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/#encoding) (met name in het gedeelte Veelgestelde vragen), wordt u gefactureerd op basis van de duur van de bestanden die worden geproduceerd als uitvoer wanneer u Video's codeert met behulp van Media Encoder Standard. Met WAME wordt u gefactureerd op basis van de grootte van de invoer video bestanden en video bestand (en).

## <a name="need-help"></a>Hulp nodig?

U kunt een ondersteunings ticket openen door te navigeren naar de [nieuwe ondersteunings aanvraag](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Volgende stappen

* [Verouderde onderdelen](legacy-components.md)
* [Pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/#encoding)
