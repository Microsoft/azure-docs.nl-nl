---
title: Video en audio coderen met Media Services
description: In dit artikel wordt uitgelegd hoe u video en audio met Azure Media Services versleutelt.
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
ms.custom: seodec18
ms.openlocfilehash: 225f1d311739bdafbe39971a2b4ac74917e770e9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279490"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Video en audio coderen met Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

De term encoding in Media Services is van toepassing op het proces van het converteren van bestanden met digitale video en/of audio van de ene standaard indeling naar een andere, met het doel van (a) om de grootte van de bestanden te reduceren en/of (b) die een indeling produceert die compatibel is met een groot aantal apparaten en apps. Dit proces wordt ook wel video compressie of transcode ring genoemd. Zie [gegevens compressie](https://en.wikipedia.org/wiki/Data_compression) en [Wat is code ring en trans codering?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) voor verdere bespreking van de concepten.

Video's worden meestal geleverd aan apparaten en apps door [progressief te downloaden](https://en.wikipedia.org/wiki/Progressive_download) of door [Adaptive bitrate streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming).

> [!IMPORTANT]
> Media Services worden geen geannuleerde of fout taken gefactureerd. Een taak die bijvoorbeeld een voortgang van 50% heeft bereikt en wordt geannuleerd, wordt niet in rekening gebracht tegen 50% van de taak minuten. Er worden alleen kosten in rekening gebracht voor voltooide taken.

* Voor het leveren van een progressieve down load kunt u Azure Media Services gebruiken om een digitaal media bestand (mezzanine) te converteren naar een [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) -bestand, dat Video's bevat die zijn gecodeerd met de [H. 264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) -codec en de audio die is gecodeerd met de [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) -codec. Dit MP4-bestand wordt naar een asset in uw opslag account geschreven. U kunt de Azure Storage Api's of Sdk's (bijvoorbeeld [Storage rest API](../../storage/common/storage-rest-api-auth.md) of [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) gebruiken om het bestand rechtstreeks te downloaden. Als u het uitvoer activum hebt gemaakt met een specifieke container naam in Storage, gebruikt u die locatie. Als dat niet het geval is, kunt u Media Services gebruiken om [de url's van de Asset-container weer te geven](/rest/api/media/assets/listcontainersas). 
* Om inhoud voor te bereiden voor levering door Adaptive Bitrate Streaming, moet het mezzanine-bestand worden gecodeerd op meerdere bitsnelheden (hoog naar laag). Om te zorgen voor een correcte overgang van kwaliteit, wordt de resolutie van de video verlaagd naarmate de bitsnelheid wordt verlaagd. Dit resulteert in een zogenaamde coderings ladder, een tabel met resoluties en bitrates (zie een [automatisch gegenereerde, Adaptive bitrate ladder](encode-autogen-bitrate-ladder.md)). U kunt Media Services gebruiken om uw mezzanine-bestanden op meerdere bitsnelheden te coderen. In dat geval krijgt u een set van MP4-bestanden en bijbehorende streaming-configuratie bestanden die worden geschreven naar een asset in uw opslag account. U kunt vervolgens de [dynamische verpakkings](encode-dynamic-packaging-concept.md) functie in Media Services gebruiken om de video via streaming protocollen zoals [MPEG-Dash](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) en [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)te leveren. Hiervoor moet u een streaming- [Locator](stream-streaming-locators-concept.md) maken en streaming-url's bouwen die overeenkomen met de ondersteunde protocollen. deze kunnen vervolgens worden door gegeven aan apparaten/apps op basis van hun mogelijkheden.

In het volgende diagram ziet u de werk stroom voor code ring op aanvraag met dynamische verpakking.

![Werk stroom voor code ring op aanvraag met dynamische verpakking](./media/encode-dynamic-packaging-concept/media-services-dynamic-packaging.svg)

In dit onderwerp vindt u richt lijnen voor het coderen van inhoud met Media Services v3.

## <a name="transforms-and-jobs"></a>Transformaties en taken

Als u wilt coderen met Media Services v3, moet u een [trans formatie](/rest/api/media/transforms) en een [taak](/rest/api/media/jobs)maken. De trans formatie definieert een recept voor uw coderings instellingen en-uitvoer. de taak is een exemplaar van het recept. Zie [Transformaties en taken](transform-jobs-concept.md) voor meer informatie.

Wanneer u code ring met Media Services, gebruikt u voor instellingen om de encoder te vertellen hoe de invoer media bestanden moeten worden verwerkt. In Media Services V3 gebruikt u de standaard encoder om uw bestanden te coderen. U kunt bijvoorbeeld de video resolutie en/of het aantal audio kanalen opgeven dat u in de gecodeerde inhoud wilt opnemen.

U kunt snel aan de slag met een van de aanbevolen standaard instellingen op basis van best practices in de branche of u kunt kiezen om een aangepaste voor instelling te bouwen om uw specifieke scenario-of apparaat vereisten te bereiken. Zie [coderen met een aangepaste trans formatie](encode-custom-presets-how-to.md)voor meer informatie.

Als u begint met een code ring met de standaard Encoder voor het maken van een MP4-bestand (en), wordt er vanaf januari 2019 een nieuw. mpi-bestand gegenereerd en toegevoegd aan de uitvoer Asset. Dit MPI-bestand is bedoeld om de prestaties te verbeteren voor [dynamische pakket](encode-dynamic-packaging-concept.md) -en streaming-scenario's.

> [!NOTE]
> U mag het MPI-bestand niet wijzigen of verwijderen of afhankelijk van de aanwezigheid (of niet) van een dergelijk bestand afnemen in uw service.

### <a name="creating-job-input-from-an-https-url"></a>Taak invoer maken op basis van een HTTPS-URL

Wanneer u taken voor het verwerken van uw Video's verzendt, moet u Media Services waar u de invoer video kunt vinden. Een van de opties is om een HTTPS-URL op te geven als een taak invoer. Media Services V3 biedt momenteel geen ondersteuning voor gesegmenteerde overdrachts codering via HTTPS-Url's.

#### <a name="examples"></a>Voorbeelden

* [Coderen van een HTTPS-URL met .NET](stream-files-dotnet-quickstart.md)
* [Coderen van een HTTPS-URL met REST](stream-files-tutorial-with-rest.md)
* [Coderen van een HTTPS-URL met CLI](stream-files-cli-quickstart.md)
* [Coderen van een HTTPS-URL met Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Taak invoer maken op basis van een lokaal bestand

De invoer video kan worden opgeslagen als een media service-Asset. in dat geval kunt u een invoer element maken op basis van een bestand (lokaal opgeslagen of in Azure Blob-opslag).

#### <a name="examples"></a>Voorbeelden

[Een lokaal bestand coderen met ingebouwde voor instellingen](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Taak invoer met subknipsel maken

Bij het coderen van een video kunt u opgeven dat u het bron bestand ook wilt knippen of knippen en een uitvoer wilt genereren die alleen een gedeelte van de invoer video bevat. Deze functionaliteit werkt met elke [trans formatie](/rest/api/media/transforms) die is gebouwd met behulp van de [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) -voor instellingen of de [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) -voor waarden.

U kunt opgeven om een [taak](/rest/api/media/jobs/create) te maken met één clip op aanvraag of Live Archive (een vastgelegde gebeurtenis). De taak invoer kan een activa-of een HTTPS-URL zijn.

> [!TIP]
> Als u een sublip van uw video wilt streamen zonder de video opnieuw te coderen, kunt u overwegen om voor het gebruiken van [voor het filteren van manifesten met dynamische pakket](filters-dynamic-manifest-concept.md)functie.

#### <a name="examples"></a>Voorbeelden

Zie voor beelden:

* [Een video met .NET knippen](transform-subclip-video-dotnet-how-to.md)
* [Een video met REST samenknippen](transform-subclip-video-rest-how-to.md)

## <a name="built-in-presets"></a>Ingebouwde voor instellingen

Media Services ondersteunt de volgende ingebouwde coderings definities:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) wordt gebruikt voor het instellen van een ingebouwde voor instelling voor het coderen van de invoer video met de standaard encoder.

De volgende ingebouwde voor instellingen worden momenteel ondersteund:

- **EncoderNamedPreset. AACGoodQualityAudio**: produceert één MP4-bestand met alleen stereo audio dat is gecodeerd met 192 kbps.
- **EncoderNamedPreset. AdaptiveStreaming** (aanbevolen): dit ondersteunt de code ring van de Adaptive bitrate van H. 264. Zie [automatisch genereren van een bitsnelheid](encode-autogen-bitrate-ladder.md)voor meer informatie.
- **EncoderNamerPreset. H265AdaptiveStreaming** : vergelijkbaar met de AdaptiveStreaming-voor instelling, maar maakt gebruik van de HEVC-codec (H. 265). Produceert een set GOP terug-afgevulde MP4-bestanden met H. 265 video en stereo AAC-audio. Genereert automatisch een bitrate voor een bitsnelheid op basis van de invoer resolutie, de bitsnelheid en de frame frequentie. De automatisch gegenereerde voor instelling overschrijdt nooit de invoer resolutie. Als de invoer bijvoorbeeld 720p is, blijft de uitvoer in de beste 720p.
- **EncoderNamedPreset. ContentAwareEncoding**: beschrijft een vooraf ingestelde optie voor H. 264-inhoud bewuste code ring. Op basis van de invoer inhoud probeert de service automatisch het optimale aantal lagen en de juiste bitrate-en resolutie-instellingen te bepalen voor levering door adaptieve streaming. De onderliggende algoritmen blijven in de loop van de tijd worden uitgevoerd. De uitvoer bevat MP4-bestanden met Interleaved video-en audio-indeling. Zie voor meer informatie [code ring met inhoud](encode-content-aware-concept.md).
- **EncoderNamedPreset. H265ContentAwareEncoding**: beschrijft een vooraf ingestelde code ring voor HEVC (H. 265). Produceert een reeks GOP terug-uitgelijnde Mp4's met behulp van inhoud-bewuste code ring. Op basis van de invoer inhoud voert de service een initiële licht gewicht analyse uit van de invoer inhoud en gebruikt de resultaten om het optimale aantal lagen, de juiste bitrate en resolutie-instellingen voor levering door adaptieve streaming te bepalen. Deze vooraf ingestelde versie is bijzonder effectief voor Video's met een lage of middel grote complexiteit, waarbij de uitvoer bestanden lagere bitrates hebben, maar een kwaliteit heeft die nog steeds een goede ervaring voor kijkers biedt. De uitvoer bevat MP4-bestanden met Interleaved video-en audio-indeling.
  > [!NOTE]
  > Zorg ervoor dat u **ContentAwareEncoding** gebruikt die niet ContentAwareEncodingExperimental is en dat nu is afgeschaft

- **EncoderNamedPreset. H264MultipleBitrate1080p**: produceert een set van acht GOP terug-afgevulde MP4-bestanden, variërend van 6000 kbps tot 400 kbps en stereo AAC-audio. De oplossing begint om 1080p en gaat omlaag naar 360p.
- **EncoderNamedPreset. H264MultipleBitrate720p**: produceert een set van zes GOP terug-afgevulde MP4-bestanden, variërend van 3400 kbps tot 400 kbps en stereo AAC-audio. De oplossing begint op 720p en gaat omlaag naar 360p.
- **EncoderNamedPreset. H264MultipleBitrateSD**: produceert een set van vijf GOP terug-afgevulde MP4-bestanden, variërend van 1600 kbps tot 400 kbps en stereo AAC-audio. De oplossing begint bij 480p en gaat omlaag naar 360p.
- **EncoderNamedPreset. H264SingleBitrate1080p**: produceert een MP4-bestand waarin de video is gecodeerd met de H. 264-codec van 6750 kbps en een afbeeldings hoogte van 1080 pixels en de stereo-audio is gecodeerd met AAC-LC-codec op 64 kbps.
- **EncoderNamedPreset. H264SingleBitrate720p**: produceert een MP4-bestand waarin de video is gecodeerd met de H. 264-codec van 4500 kbps en een afbeeldings hoogte van 720 pixels en de stereo-audio is gecodeerd met AAC-LC-codec op 64 kbps.
- **EncoderNamedPreset. H264SingleBitrateSD**: produceert een MP4-bestand waarin de video is gecodeerd met de H. 264-codec van 2200 kbps en een afbeeldings hoogte van 480 pixels en de stereo-audio is gecodeerd met AAC-LC-codec op 64 kbps.
- **EncoderNamedPreset. H265SingleBitrate720P**: produceert een MP4-bestand waarin de video is gecodeerd met de HEVC (H. 265) codec van 1800 kbps en een afbeeldings hoogte van 720 pixels en de stereo-audio is gecodeerd met AAC-LC-codec op 128 kbps.
- **EncoderNamedPreset. H265SingleBitrate1080p**: produceert een MP4-bestand waarin de video is gecodeerd met de HEVC (H. 265) codec van 3500 kbps en een afbeeldings hoogte van 1080 pixels en de stereo-audio is gecodeerd met AAC-LC-codec op 128 kbps.
- **EncoderNamedPreset. H265SingleBitrate4K**: produceert een MP4-bestand waarin de video is gecodeerd met de HEVC (H. 265) codec van 9500 kbps en een afbeeldings hoogte van 2160 pixels en de stereo-audio is gecodeerd met AAC-LC-codec op 128 kbps.

Zie [ingebouwde voor instellingen voor het coderen van Video's](/rest/api/media/transforms/createorupdate#encodernamedpreset)voor een overzicht van de meest recente lijst met voor waarden.

Zie [bestanden uploaden, coderen en streamen](stream-files-tutorial-with-api.md)om te zien hoe de voor instellingen worden gebruikt.

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) beschrijft de instellingen die moeten worden gebruikt bij het coderen van de invoer video met de standaard encoder. Gebruik deze standaard instelling bij het aanpassen van de transformatie definities.

#### <a name="considerations"></a>Overwegingen

Bij het maken van aangepaste voor instellingen gelden de volgende overwegingen:

- Alle waarden voor de hoogte en breedte van AVC-inhoud moeten een meervoud van vier zijn.
- In Azure Media Services v3 bevinden alle coderings bitsnelheden zich in bits per seconde. Dit wijkt af van de voor instellingen met onze v2 Api's, die kilobits/seconde als eenheid hebben gebruikt. Als de bitrate in v2 bijvoorbeeld is opgegeven als 128 (kilobits/seconde), wordt deze in v3 ingesteld op 128000 (bits/seconde).

### <a name="customizing-presets"></a>Voor instellingen aanpassen

Media Services volledig ondersteunt het aanpassen van alle waarden in voor instellingen om te voldoen aan uw specifieke behoeften en vereisten voor de code ring. Voor voor beelden die laten zien hoe u de voor instellingen voor code ring kunt aanpassen, zie de onderstaande lijst:

#### <a name="examples"></a>Voorbeelden

- [Voor instellingen aanpassen met .NET](encode-custom-presets-how-to.md)
- [Voor instellingen aanpassen met CLI](encode-custom-preset-cli-how-to.md)
- [Voor instellingen aanpassen met REST](encode-custom-preset-rest-how-to.md)


## <a name="preset-schema"></a>Vooraf ingesteld schema

In Media Services v3 zijn de voor instellingen sterk getypeerde entiteiten in de API zelf. U kunt de definitie ' schema ' voor deze objecten vinden in de [open API-specificatie (of Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). U kunt de vooraf ingestelde definities (zoals **StandardEncoderPreset**) ook weer geven in de [rest API](/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset) (of een andere Media Services v3 SDK-referentie documentatie).

## <a name="scaling-encoding-in-v3"></a>Versleuteling schalen in v3

Zie [schalen met CLI](media-reserved-units-cli-how-to.md)voor informatie over het schalen van media verwerking.
Voor accounts die zijn gemaakt met met de **2020-05-01** -versie van de API of via de Azure Portal, zijn schalen en gereserveerde media-eenheden niet meer vereist. Schalen wordt automatisch uitgevoerd en wordt door de service intern afgehandeld.

## <a name="billing"></a>Billing

Media Services worden geen geannuleerde of fout taken gefactureerd. Een taak die bijvoorbeeld een voortgang van 50% heeft bereikt en wordt geannuleerd, wordt niet in rekening gebracht tegen 50% van de taak minuten. Er worden alleen kosten in rekening gebracht voor voltooide taken.

Ga voor meer informatie naar het overzicht van [prijzen](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="next-steps"></a>Volgende stappen

* [Uploaden, coderen en streamen met behulp van Media Services](stream-files-tutorial-with-api.md).
* [Code ring van een HTTPS-URL met behulp van ingebouwde voor instellingen](job-input-from-http-how-to.md).
* [Codeer een lokaal bestand met ingebouwde voor instellingen](job-input-from-local-file-how-to.md).
* [Een aangepaste voorinstelling bouwen voor de vereisten van uw specifieke scenario of apparaat](encode-custom-presets-how-to.md).
