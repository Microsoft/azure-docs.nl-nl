---
title: Een voor instelling voor code ring met Content-Aware
description: In dit artikel wordt beschreven hoe u met inhoud compatibele code ring in Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ce18e71ced320c408933caeb39b469d5885bd6ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095945"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>De vooraf ingestelde coderings voorinstelling gebruiken om de optimale bitrate waarde voor een bepaalde oplossing te vinden

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Video moet worden gecodeerd met meerdere bitsnelheden (hoog naar laag) om inhoud voor te bereiden voor levering door [Adaptive bitrate streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming). Dit zorgt voor een goede vermindering van de kwaliteit, omdat de bitsnelheid lager is. Dit is de resolutie van de video. Een dergelijke multiple-bit-factor encoding maakt gebruik van een zogenaamde ' coderings ladder ', een tabel met resoluties en bitrates, de Media Services [ingebouwde coderings definities](/rest/api/media/transforms/createorupdate#encodernamedpreset).

U moet rekening houden met de inhoud die u verwerkt en de coderings ladder aanpassen of afstemmen op de complexiteit van de afzonderlijke video. Bij elke oplossing is er sprake van een bitsnelheid waarboven een toename van de kwaliteit niet perceptive is. het coderings programma werkt op deze optimale bitrate waarde. Het volgende optimalisatie niveau is het selecteren van de oplossingen op basis van de inhoud. een video van een Power Point-presentatie biedt bijvoorbeeld geen voor deel uit van de volgende 720p. Het coderings programma kan ook worden getaakd om de instellingen voor elke foto in de video te optimaliseren. 

De voor instelling van de [adaptieve streaming](autogen-bitrate-ladder.md) van micro soft is deels gericht op het probleem van de variabiliteit in de kwaliteit en resolutie van de bron Video's. Onze klanten hebben een verschillende combi natie van inhoud, een aantal voor 1080p, anderen op 720p en enkele van de SD en lagere resoluties. Bovendien is niet alle bron inhoud kwalitatief hoogwaardige mezzanine van film of TV Studios. Met de vooraf ingestelde adaptieve streaming worden deze problemen opgelost door ervoor te zorgen dat de videoladder nooit de resolutie of de gemiddelde bitsnelheid van de invoer-mezzanine overschrijdt. Deze standaard instelling onderzoekt echter geen andere bron eigenschappen dan omzetting en bitrate.

## <a name="the-content-aware-encoding"></a>De coderings functionaliteit voor inhoud

De vooraf ingestelde coderings instellingen voor versleuteling breiden het Adaptive Bitrate Streaming-mechanisme uit door aangepaste logica te integreren waarmee de encoder de optimale bitrate waarde voor een bepaalde oplossing kan zoeken, maar zonder dat er uitgebreide reken kundige analyse nodig is. Deze vooraf ingestelde produceert een reeks GOP terug-afgevulde Mp4's. Op basis van de invoer inhoud voert de service een initiële licht gewicht analyse uit van de invoer inhoud en gebruikt de resultaten om het optimale aantal lagen, de juiste bitrate en resolutie-instellingen voor levering door adaptieve streaming te bepalen. Deze standaard instelling is met name van toepassing op Video's met een laag en gemiddeld complexiteit, waarbij de uitvoer bestanden lagere bitsnelheden zijn dan de vooraf ingestelde voor instelling van adaptief streamen, maar met een kwaliteit die nog steeds een goede ervaring voor kijkers biedt. De uitvoer bevat MP4-bestanden met Interleaved video en audio

In de volgende voorbeeld grafieken ziet u de vergelijking met de metrische gegevens van de kwaliteit, zoals [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) en [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). De bron is gemaakt door het samen voegen van korte clips met zeer complexe opnamen van films en TV-Program ma's, die zijn bedoeld om het coderings programma te stressren. Deze standaard instelling produceert per definitie resultaten die variëren van inhoud tot inhoud. het betekent ook dat er voor sommige inhoud geen aanzienlijke vermindering van de bitrate of verbetering van de bitsnelheid kan optreden.

![Distorsie (RD)-curve met behulp van PSNR](media/content-aware-encoding/msrv1.png)

**Afbeelding 1: een bocht-curve (distorsie) met behulp van PSNR-metrische gegevens voor een hoge complexiteits bron**

![Distorsie (RD)-curve met behulp van VMAF](media/content-aware-encoding/msrv2.png)

**Afbeelding 2: een bocht-curve (distorsie) met behulp van VMAF-metrische gegevens voor een hoge complexiteits bron**

Hieronder vindt u de resultaten voor een andere categorie bron inhoud, waarbij het coderings programma kan bepalen dat de invoer van slechte kwaliteit was (veel compressie artefacten vanwege de lage bitrate). Houd er rekening mee dat met de vooraf ingestelde voor instelling voor het coderings programma slechts één uitvoer laag moet worden gemaakt, met een lage bitrate, zodat de meeste clients de stroom kunnen afspelen zonder dat ze worden gestopt.

![RD-curve met PSNR](media/content-aware-encoding/msrv3.png)

**Afbeelding 3: RD-curve met PSNR voor invoer van lage kwaliteit (op 1080p)**

![RD-curve met VMAF](media/content-aware-encoding/msrv4.png)

**Afbeelding 4: RD-curve met VMAF voor invoer van lage kwaliteit (op 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>De vooraf ingestelde coderings instelling gebruiken 

U kunt als volgt trans formaties maken die gebruikmaken van deze voor instelling. 

Zie de sectie [volgende stappen](#next-steps) voor zelf studies die gebruikmaken van trans formatie-uitvoer. Het uitvoer activum kan worden geleverd vanaf Media Services streaming-eind punten in protocollen zoals MPEG-DASH en HLS (zoals weer gegeven in de zelf studies).

> [!NOTE]
> Zorg ervoor dat u de **ContentAwareEncoding** -voor instelling niet ContentAwareEncodingExperimental gebruikt.

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Voor het coderen van taken met behulp van de `ContentAwareEncoding` voor instelling wordt gefactureerd op basis van de uitvoer minuten. 
  
## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Video's uploaden, coderen en streamen met Media Services v3](stream-files-tutorial-with-api.md)
* [Zelfstudie: Extern bestand coderen op basis van URL en video streamen - REST](stream-files-tutorial-with-rest.md)
* [Zelf studie: een extern bestand coderen op basis van URL en de video-CLI streamen](stream-files-cli-quickstart.md)
* [Zelfstudie: Extern bestand coderen op basis van URL en video streamen - .NET](stream-files-dotnet-quickstart.md)
* [Zelfstudie: Extern bestand coderen op basis van URL en video streamen - Node.js](stream-files-nodejs-quickstart.md)
