---
title: Media Encoder Standard schema | Microsoft Docs
description: In dit artikel worden enkele elementen en typen van het XML-schema beschreven waarop Media Encoder Standard voor instellingen zijn gebaseerd.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: f82e0c3f76dba05c3404b11e07c7130119ce0b9d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015655"
---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard-schema

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

In dit artikel worden enkele elementen en typen van het XML-schema beschreven waarop [Media Encoder Standard voor instellingen](media-services-mes-presets-overview.md) zijn gebaseerd. Het artikel geeft uitleg over elementen en hun geldige waarden.  

## <a name="preset-root-element"></a><a name="Preset"></a> Vooraf ingesteld (hoofd element)
Hiermee wordt een vooraf ingestelde code ring gedefinieerd.  

### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Codering** |[Codering](media-services-mes-schema.md#Encoding) |Hoofd element, geeft aan dat de invoer bronnen moeten worden gecodeerd. |
| **Uitvoerwaarden** |[Uitvoerwaarden](media-services-mes-schema.md#Output) |Verzameling gewenste uitvoer bestanden. |
| **StretchMode**<br/>minOccurs = "0"<br/>standaard instelling = "AutoSize|XS: teken reeks|De kader grootte, opvulling, pixel of weergave hoogte-breedte verhouding van het video besturings element beheren. **StretchMode** kan een van de volgende waarden hebben: **geen**, **AutoSize** (standaard) of **AutoAanpassen**.<br/><br/>**Geen**: de uitvoer resolutie (bijvoorbeeld de **breedte** en de **hoogte** in de voor instelling) volgen zonder rekening te houden met de pixel verhouding of de hoogte-breedte verhouding van de invoer video. Aanbevolen in scenario's zoals [bijsnijden](media-services-crop-video.md), waarbij de video met uitvoer een andere hoogte-breedte verhouding heeft dan de invoer. <br/><br/>**AutoSize**: de uitvoer resolutie past in het venster (breedte * hoogte) dat is opgegeven met de voor instelling. Het coderings programma produceert echter een uitvoer video met een vier kante pixel verhouding (1:1). Daarom kan de uitvoer breedte of uitvoer hoogte worden overschreven zodat deze overeenkomt met de weergave hoogte-breedte verhouding van de invoer, zonder opvulling. Als de invoer bijvoorbeeld 1920 is en de voor instelling voor code ring vraagt om 1280x1280, wordt de waarde van Height in de voor instelling overschreven en wordt de uitvoer op 1280x720, die de invoer hoogte-breedte verhouding van 16:9 houdt. <br/><br/>**AutoAanpassen**: als dat nodig is, kunt u de uitvoer video (met ofwel letterbox of pillarbox) aanvullen om te voldoen aan de gewenste uitvoer resolutie, terwijl u ervoor zorgt dat de actieve video regio in de uitvoer dezelfde hoogte-breedte verhouding heeft als de invoer. Stel bijvoorbeeld dat de invoer 1920 is en dat de voor instelling voor code ring vraagt om 1280x1280. De uitvoer video gaat dan op 1280x1280, maar bevat een binnenste 1280x720-rechthoek van ' actieve video ' met een hoogte-breedte verhouding van 16:9 en letterbox regio's 280 pixels hoog aan de bovenkant en de onderkant. Een ander voor beeld: als de invoer is 1440x1080 en de voor instelling voor code ring vraagt om 1280x720, is de uitvoer op 1280x720, die een binnenste rechthoek bevat van 960x720 met een hoogte-breedte verhouding van 4:3 en de regio's in de pijler Box 160 pixels breed aan de linkerkant en de rechter kant. 

### <a name="attributes"></a>Kenmerken

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Versie**<br/><br/> Vereist |**XS: decimaal** |De vooraf ingestelde versie. De volgende beperkingen zijn van toepassing: XS: fractionDigits-waarde = "1" en XS: minInclusive-waarde = "1" bijvoorbeeld **Version = "1.0"**. |

## <a name="encoding"></a><a name="Encoding"></a> Gecodeerd
Bevat een reeks van de volgende elementen:  

### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Instellingen voor de H. 264-code ring van de video. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Instellingen voor AAC-code ring van audio. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Instellingen voor BMP-afbeelding. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Instellingen voor PNG-afbeelding. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Instellingen voor JPG-afbeelding. |

## <a name="h264video"></a><a name="H264Video"></a> H264Video
### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs = "0" |**XS: Boolean** |Op dit moment wordt slechts één pass-code ring ondersteund. |
| **KeyFrameInterval**<br/><br/> minOccurs = "0"<br/><br/> **standaard instelling = "00:00:02"** |**XS: tijd** |Bepaalt de vaste afstand tussen IDR frames in eenheden van seconden. Wordt ook wel de GOP terug-duur genoemd. Zie **SceneChangeDetection** om te bepalen of het coderings programma kan afwijken van deze waarde. |
| **SceneChangeDetection**<br/><br/> minOccurs = "0"<br/><br/> standaard = "false" |**XS: Boolean** |Als deze eigenschap is ingesteld op True, probeert encoder de scène wijziging in de video te detecteren en wordt een IDR-frame ingevoegd. |
| **Complexe**<br/><br/> minOccurs = "0"<br/><br/> standaard instelling = "evenwichtig" |**XS: teken reeks** |Hiermee bepaalt u de afweging tussen de snelheid en video kwaliteit van de code ring. Dit kan een van de volgende waarden zijn: **snelheid**, **gebalanceerd** of **kwaliteit**<br/><br/> Standaard: **evenwichtig** |
| **SyncMode**<br/><br/> minOccurs = "0" | |De functie wordt weer gegeven in een toekomstige versie. |
| **H264Layers**<br/><br/> minOccurs = "0" |[H264Layers](media-services-mes-schema.md#H264Layers) |Verzameling video lagen voor uitvoer. |

### <a name="attributes"></a>Kenmerken

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Condition** |**XS: teken reeks** | Wanneer de invoer geen video bevat, wilt u mogelijk het coderings programma dwingen om een monochroom video spoor in te voegen. Gebruik hiervoor voor waarde = "InsertBlackIfNoVideoBottomLayerOnly" (om een video te plaatsen bij alleen de laagste bitrate) of voor waarde = "InsertBlackIfNoVideo" (om een video in te voegen bij alle uitvoer bitrates). Zie [Dit](media-services-advanced-encoding-with-mes.md#no_video) artikel voor meer informatie.|

## <a name="h264layers"></a><a name="H264Layers"></a> H264Layers

Standaard, als u een invoer verzendt naar het coderings programma dat alleen audio bevat en geen video, bevat de uitvoer Asset alleen bestanden met audio gegevens. Sommige spelers kunnen dergelijke uitvoer stromen mogelijk niet verwerken. U kunt de H264Video's **InsertBlackIfNoVideo** -kenmerk instelling gebruiken om ervoor te zorgen dat het coderings programma een video spoor toevoegt aan de uitvoer in dat scenario. Zie [Dit](media-services-advanced-encoding-with-mes.md#no_video) artikel voor meer informatie.
              
### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs = "0" maxOccurs = "onbegrensd" |[H264Layer](media-services-mes-schema.md#H264Layer) |Een verzameling H264-lagen. |

## <a name="h264layer"></a><a name="H264Layer"></a> H264Layer
> [!NOTE]
> Video limieten zijn gebaseerd op de waarden die worden beschreven in de tabel [H264 niveaus](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) .  
> 
> 

### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Profiel**<br/><br/> minOccurs = "0"<br/><br/> standaard = ' auto ' |**XS: teken reeks** |Dit kan een van de volgende **XS: teken reeks** waarden zijn **: auto**, **baseline**, **Main**, **High**. |
| **Afvlakking**<br/><br/> minOccurs = "0"<br/><br/> standaard = ' auto ' |**XS: teken reeks** | |
| **Bitsnelheid**<br/><br/> minOccurs = "0" |**XS: int** |De bitrate die wordt gebruikt voor deze videolaag, opgegeven in kbps. |
| **MaxBitrate**<br/><br/> minOccurs = "0" |**XS: int** |De maximale bitsnelheid die wordt gebruikt voor deze videolaag, opgegeven in kbps. |
| **BufferWindow**<br/><br/> minOccurs = "0"<br/><br/> standaard instelling = "00:00:05" |**XS: tijd** |Lengte van de video buffer. |
| **Breedte**<br/><br/> minOccurs = "0" |**XS: int** |De breedte van het video frame van de uitvoer, in pixels.<br/><br/> Op dit moment moet u zowel width als Height opgeven. De breedte en hoogte moeten even cijfers zijn. |
| **Hoogte**<br/><br/> minOccurs = "0" |**XS: int** |De hoogte van het video frame van de uitvoer, in pixels.<br/><br/> Op dit moment moet u zowel width als Height opgeven. De breedte en hoogte moeten even cijfers zijn.|
| **BFrames**<br/><br/> minOccurs = "0" |**XS: int** |Aantal B-frames tussen referentie frames. |
| **ReferenceFrames**<br/><br/> minOccurs = "0"<br/><br/> standaard instelling = "3" |**XS: int** |Het aantal referentie frames in een GOP terug. |
| **EntropyMode**<br/><br/> minOccurs = "0"<br/><br/> standaard instelling = "CABAC" |**XS: teken reeks** |Dit kan een van de volgende waarden zijn: **CABAC** en **Cavlc**. |
| **Snelheid**<br/><br/> minOccurs = "0" |rationeel nummer |Bepaalt de frame frequentie van de uitvoer video. Gebruik de standaard waarde van "0/1" om ervoor te zorgen dat het coderings programma dezelfde frame frequentie gebruikt als de invoer video. Toegestane waarden worden naar verwachting gang bare video frame frequenties. Een geldige rationele ordening is echter toegestaan. Bijvoorbeeld: 1/1 zou 1 fps zijn en geldig zijn.<br/><br/> -12/1 (12 fps)<br/><br/> -15/1 (15 fps)<br/><br/> -24/1 (24 fps)<br/><br/> -24000/1001 (23,976 fps)<br/><br/> -25/1 (25 fps)<br/><br/>  -30/1 (30 fps)<br/><br/> -30000/1001 (29,97 fps) <br/> <br/>**Opmerking** Als u een aangepaste voor instelling voor code ring met meerdere bitsnelheden maakt, **moeten** voor alle lagen van de vooraf ingestelde de waarde voor de frame snelheid worden gebruikt.|
| **AdaptiveBFrame**<br/><br/> minOccurs = "0" |**XS: Boolean** |Kopiëren uit Azure Media Encoder |
| **Segmenten**<br/><br/> minOccurs = "0"<br/><br/> standaard instelling = "0" |**XS: int** |Hiermee wordt bepaald op hoeveel segmenten een frame is verdeeld. U wordt aangeraden standaard te gebruiken. |

## <a name="aacaudio"></a><a name="AACAudio"></a> AACAudio
 Bevat een reeks van de volgende elementen en groepen.  

 Zie [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)voor meer informatie over AAC.  

### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Profiel**<br/><br/> minOccurs = "0"<br/><br/> standaard instelling = "AACLC" |**XS: teken reeks** |Dit kan een van de volgende waarden zijn: **AACLC**, **HEAACV1** of **HEAACV2**. |

### <a name="attributes"></a>Kenmerken

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Condition** |**XS: teken reeks** |Geef de ' InsertSilenceIfNoAudio-waarde op als u wilt afdwingen dat het coderings programma een Asset produceert die een Silent audio-track bevat wanneer de invoer geen audio heeft.<br/><br/> Standaard, als u een invoer verzendt naar het coderings programma dat alleen video bevat en geen audio, bevat het uitvoer element alleen bestanden die alleen video gegevens bevatten. Sommige spelers kunnen dergelijke uitvoer stromen mogelijk niet verwerken. U kunt deze instelling gebruiken om ervoor te zorgen dat het coderings programma een Silent audio-track toevoegt aan de uitvoer in dat scenario. |

### <a name="groups"></a>Groepen

| Naslaginformatie | Beschrijving |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs = "0" |Zie beschrijving van [AudioGroup](media-services-mes-schema.md#AudioGroup) om het juiste aantal kanalen, de sampling frequentie en de bitsnelheid te weten die voor elk profiel kunnen worden ingesteld. |

## <a name="audiogroup"></a><a name="AudioGroup"></a> AudioGroup
Zie de tabel ' Details van audiocodec ' verderop voor meer informatie over welke waarden voor elk profiel geldig zijn.  

### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Kanalen**<br/><br/> minOccurs = "0" |**XS: int** |Het aantal gecodeerde audio kanalen. Hier volgen geldige opties: 1, 2, 5, 6, 8.<br/><br/> Standaard waarde: 2. |
| **SamplingRate**<br/><br/> minOccurs = "0" |**XS: int** |De steekproef frequentie van de audio, opgegeven in Hz. |
| **Bitsnelheid**<br/><br/> minOccurs = "0" |**XS: int** |De bitrate die wordt gebruikt bij het coderen van de audio, opgegeven in kbps. |

### <a name="audio-codec-details"></a>Details van audiocodec

Audiocodec|Details  
-----------------|---  
**AACLC** |1:<br/><br/> -11025:8 &lt; = bitrate &lt; 16<br/><br/> -12000:8 &lt; = bitrate &lt; 16<br/><br/> -16000:8 &lt; = bitrate &lt; 32<br/><br/>-22050:24 &lt; = bitrate &lt; 32<br/><br/> -24000:24 &lt; = bitrate &lt; 32<br/><br/> -32000:32 &lt; = bitrate &lt; = 192<br/><br/> -44100:56 &lt; = bitrate &lt; = 288<br/><br/> -48000:56 &lt; = bitrate &lt; = 288<br/><br/> -88200:128 &lt; = bitrate &lt; = 288<br/><br/> -96000:128 &lt; = bitrate &lt; = 288<br/><br/> 2:<br/><br/> -11025:16 &lt; = bitrate &lt; 24<br/><br/> -12000:16 &lt; = bitrate &lt; 24<br/><br/> -16000:16 &lt; = bitrate &lt; 40<br/><br/> -22050:32 &lt; = bitrate &lt; 40<br/><br/> -24000:32 &lt; = bitrate &lt; 40<br/><br/> -32000:40 &lt; = bitrate &lt; = 384<br/><br/> -44100:96 &lt; = bitrate &lt; = 576<br/><br/> -48000:96 &lt; = bitrate &lt; = 576<br/><br/> -88200:256 &lt; = bitrate &lt; = 576<br/><br/> -96000:256 &lt; = bitrate &lt; = 576<br/><br/> 5/6:<br/><br/> -32000:160 &lt; = bitrate &lt; = 896<br/><br/> -44100:240 &lt; = bitrate &lt; = 1024<br/><br/> -48000:240 &lt; = bitrate &lt; = 1024<br/><br/> -88200:640 &lt; = bitrate &lt; = 1024<br/><br/> -96000:640 &lt; = bitrate &lt; = 1024<br/><br/> 8:<br/><br/> -32000:224 &lt; = bitrate &lt; = 1024<br/><br/> -44100:384 &lt; = bitrate &lt; = 1024<br/><br/> -48000:384 &lt; = bitrate &lt; = 1024<br/><br/> -88200:896 &lt; = bitrate &lt; = 1024<br/><br/> -96000:896 &lt; = bitrate &lt; = 1024  
**HEAACV1** |1:<br/><br/> -22050: bitrate = 8<br/><br/> -24000:8 &lt; = bitrate &lt; = 10<br/><br/> -32000:12 &lt; = bitrate &lt; = 64<br/><br/> -44100:20 &lt; = bitrate &lt; = 64<br/><br/> -48000:20 &lt; = bitrate &lt; = 64<br/><br/> -88200: bitrate = 64<br/><br/> 2:<br/><br/> -32000:16 &lt; = bitrate &lt; = 128<br/><br/> -44100:16 &lt; = bitrate &lt; = 128<br/><br/> -48000:16 &lt; = bitrate &lt; = 128<br/><br/> -88200:96 &lt; = bitrate &lt; = 128<br/><br/> -96000:96 &lt; = bitrate &lt; = 128<br/><br/> 5/6:<br/><br/> -32000:64 &lt; = bitrate &lt; = 320<br/><br/> -44100:64 &lt; = bitrate &lt; = 320<br/><br/> -48000:64 &lt; = bitrate &lt; = 320<br/><br/> -88200:256 &lt; = bitrate &lt; = 320<br/><br/> -96000:256 &lt; = bitrate &lt; = 320<br/><br/> 8:<br/><br/> -32000:96 &lt; = bitrate &lt; = 448<br/><br/> -44100:96 &lt; = bitrate &lt; = 448<br/><br/> -48000:96 &lt; = bitrate &lt; = 448<br/><br/> -88200:384 &lt; = bitrate &lt; = 448<br/><br/> -96000:384 &lt; = bitrate &lt; = 448  
**HEAACV2** |2:<br/><br/> -22050:8 &lt; = bitrate &lt; = 10<br/><br/> -24000:8 &lt; = bitrate &lt; = 10<br/><br/> -32000:12 &lt; = bitrate &lt; = 64<br/><br/> -44100:20 &lt; = bitrate &lt; = 64<br/><br/> -48000:20 &lt; = bitrate &lt; = 64<br/><br/> -88200:64 &lt; = bitrate &lt; = 64  
  
## <a name="clip"></a><a name="Clip"></a> Clip
### <a name="attributes"></a>Kenmerken

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **StartTime** |**XS: duration** |Hiermee geeft u de begin tijd van een presentatie op. De waarde van StartTime moet overeenkomen met de absolute tijds tempels van de invoer video. Als bijvoorbeeld het eerste frame van de invoer video een tijds tempel van 12:00:10.000 heeft, moet StartTime ten minste 12:00:10.000 of hoger zijn. |
| **Duur** |**XS: duration** |Hiermee geeft u de duur van een presentatie op (bijvoorbeeld de weer gave van een overlay in de video). |

## <a name="output"></a><a name="Output"></a> Uitvoer
### <a name="attributes"></a>Kenmerken

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Bestands** |**XS: teken reeks** |De naam van het uitvoer bestand.<br/><br/> U kunt de macro's die in de volgende tabel worden beschreven, gebruiken om de namen van de uitvoer bestanden te maken. Bijvoorbeeld:<br/><br/> **"Uitvoer": [{"FileName": "{Basenaam}*{Resolution}*{bitrate}. mp4", "Format": {"type": "MP4Format"}}]** |

### <a name="macros"></a>Schrijft

| Macro | Beschrijving |
| --- | --- |
| **{Basispad}** |Als u VoD-code ring uitvoert, is {basispad} de eerste 32 tekens van de eigenschap AssetFile.Name van het primaire bestand in de invoer Asset.<br/><br/> Als het invoer element een live archief is, wordt {basispad} afgeleid van de kenmerken voor naam bijhouden in het server manifest. Als u een subclip verzendt met behulp van de TopBitrate, zoals in: "<VideoStream \> TopBitrate</VideoStream \> " en het uitvoer bestand bevat video, is {basispad} de eerste 32 tekens van de naam van de laag met de hoogste bitsnelheid.<br/><br/> Als u in plaats daarvan een subfragment verzendt met behulp van alle invoer bitrates, zoals "<VideoStream \> * </VideoStream \> " en het uitvoer bestand bevat een video, is {basispad} de eerste 32 tekens van de naam van de tracering van de bijbehorende videolaag. |
| **Videocodec** |Wordt toegewezen aan ' H264 ' voor video en ' AAC ' voor audio. |
| **Bitsnelheid** |De doel video-bitsnelheid als het uitvoer bestand video en audio bevat of als het uitvoer bestand alleen audio bevat. De gebruikte waarde is de bitsnelheid in kbps. |
| **Kanalen** |Aantal audio kanalen als het bestand audio bevat. |
| **Breder** |De breedte van de video, in pixels, in het uitvoer bestand, als het bestand video bevat. |
| **Hoogte** |De hoogte van de video, in pixels, in het uitvoer bestand, als het bestand video bevat. |
| **Switch** |Neemt over van de eigenschap ' type ' voor het uitvoer bestand. De naam van het uitvoer bestand heeft een extensie die een van de volgende is: "MP4", "TS", "jpg", "PNG" of "BMP". |
| **TabIndex** |Verplicht voor miniatuur. Mag slechts één keer worden weer gegeven. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a> Video (complex type neemt van codec over)
### <a name="attributes"></a>Kenmerken

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Begin** |**XS: teken reeks** | |
| **Stap** |**XS: teken reeks** | |
| **Bereik** |**XS: teken reeks** | |
| **PreserveResolutionAfterRotation** |**XS: Boolean** |Zie de volgende sectie voor gedetailleerde uitleg: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Het is raadzaam om de vlag **PreserveResolutionAfterRotation** in combi natie met oplossings waarden te gebruiken, uitgedrukt in percentages (breedte = "100%", hoogte = "100%").  

Standaard zijn de instellingen voor het omzetten van de omzetting (breedte, hoogte) in de voor instellingen van de Media Encoder Standard (MES) gericht op Video's met een rotatie van 0 graden. Als uw invoer video bijvoorbeeld 1280x720 is met een draai hoek van nul graden, moeten de standaard instellingen ervoor zorgen dat de uitvoer dezelfde resolutie heeft.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Als de invoer video is vastgelegd met een draaiing die niet gelijk is aan nul (bijvoorbeeld een smartphone of tablet dat verticaal wordt gehouden), past MES standaard de instellingen voor het coderen van de omzetting (breedte, hoogte) toe aan de invoer video en compenseert u vervolgens de draaiing. Zie bijvoorbeeld de volgende afbeelding. De vooraf ingestelde breedte = "100%", height = "100%", die door de MES wordt geïnterpreteerd als vereist dat de uitvoer 1280 pixels breed is en 720 pixels hoog. Na het draaien van de video, wordt de afbeelding verkleind zodat deze in het venster past, waardoor de vakken van de pijler aan de linkerkant en rechts worden weer gegeven.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

U kunt ook het gebruik van de vlag **PreserveResolutionAfterRotation** maken en instellen op ' True ' (standaard is ' false '). Als uw vooraf ingestelde breedte = "100%", height = "100%" en PreserveResolutionAfterRotation is ingesteld op "True", wordt een invoer video, die 1280 pixels breed is en 720 pixels hoog met de lengte van het 90-graden, een uitvoer met een rotatie van nul graden, maar 720 pixels breed en 1280 pixels hoog. Zie de volgende afbeelding:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a> FormatGroup (groep)
### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Element

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Breedte**<br/><br/> minOccurs = "0" |**XS: int** | |
| **Hoogte**<br/><br/> minOccurs = "0" |**XS: int** | |

### <a name="attributes"></a>Kenmerken

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Condition** |**XS: teken reeks** | |

## <a name="pnglayer"></a><a name="PngLayer"></a> PngLayer
### <a name="element"></a>Element

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Breedte**<br/><br/> minOccurs = "0" |**XS: int** | |
| **Hoogte**<br/><br/> minOccurs = "0" |**XS: int** | |

### <a name="attributes"></a>Kenmerken

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Condition** |**XS: teken reeks** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Element

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Breedte**<br/><br/> minOccurs = "0" |**XS: int** | |
| **Hoogte**<br/><br/> minOccurs = "0" |**XS: int** | |
| **Kwaliteit**<br/><br/> minOccurs = "0" |**XS: int** |Geldige waarden: 1 (slechtste)-100 (beste) |

### <a name="attributes"></a>Kenmerken

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **Condition** |**XS: teken reeks** | |

## <a name="pnglayers"></a><a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs = "0" maxOccurs = "onbegrensd" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs = "0" maxOccurs = "onbegrensd" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs = "0" maxOccurs = "onbegrensd" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a> BmpImage (complex type neemt uit video over)
### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG-lagen |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a> JpgImage (complex type neemt uit video over)
### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG-lagen |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a> PngImage (complex type neemt uit video over)
### <a name="elements"></a>Elementen

| Naam | Type | Beschrijving |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG-lagen |

## <a name="examples"></a>Voorbeelden
Zie voor beelden van XML-voor instellingen die zijn gebouwd op basis van dit schema. Zie [taak voorinstellingen voor mes (Media Encoder Standard)](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

