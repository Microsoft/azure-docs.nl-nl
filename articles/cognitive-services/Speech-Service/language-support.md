---
title: Taal ondersteuning-spraak service
titleSuffix: Azure Cognitive Services
description: De spraak service ondersteunt talloze talen voor conversie van spraak naar tekst en tekst naar spraak, samen met spraak omzetting. In dit artikel vindt u een uitgebreide lijst met taal ondersteuning per service functie.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 27cf5fef027b1e4786c0ff77f9f673bafea15f98
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487266"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Taal-en spraak ondersteuning voor de spraak service

Taal ondersteuning is afhankelijk van de functionaliteit van de spraak service. In de volgende tabellen vindt u een overzicht van taal ondersteuning voor service aanbiedingen voor [spraak naar tekst](#speech-to-text), [tekst naar spraak](#text-to-speech)en [spraak omzetting](#speech-translation) .

## <a name="speech-to-text"></a>Spraak naar tekst

Zowel de micro soft Speech SDK als de REST API ondersteunen de volgende talen (land instellingen). 

Om de nauw keurigheid te verbeteren, wordt aanpassing aangeboden voor een subset van de talen via het uploaden van **Audio en Transcripten met menselijke labels** of **gerelateerde tekst: zinnen**. Zie aan de [slag met Custom speech](./custom-speech-overview.md)voor meer informatie over aanpassingen.

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Taal                          | Land instelling (BCP-47) | Aanpassingen                                   |
|-----------------------------------|--------|--------------------------------------------------|
|Arabisch (Bahrein), modern, standaard  |`ar-BH` | Taalmodel                                   |
|Arabisch (Egypte)                     |`ar-EG` | Taalmodel                                   |
|Arabisch (Irak)                      |`ar-IQ` | Taalmodel                                   |
|Arabisch (Jordanië)                    |`ar-JO` | Taalmodel                                   |
|Arabisch (Koeweit)                    |`ar-KW` | Taalmodel                                   |
|Arabisch (Libanon)                   |`ar-LB` | Taalmodel                                   |
|Arabisch (Oman)                      |`ar-OM` | Taalmodel                                   |
|Arabisch (Qatar)                     |`ar-QA` | Taalmodel                                   |
|Arabisch (Saoedi-Arabië)              |`ar-SA` | Taalmodel                                   |
|Arabisch (Syrië)                     |`ar-SY` | Taalmodel                                   |
|Arabisch (Verenigde Arabische Emiraten)      |`ar-AE` | Taalmodel                                   |
|Bulgaars (Bulgarije)               |`bg-BG` | Taalmodel                                   |
|Catalaans (Spanje)                    |`ca-ES` | Taalmodel                                   |
|Chinees (Kantonees, traditioneel)   |`zh-HK` | Taalmodel                                   |
|Chinees (Mandarijn, vereenvoudigd)     |`zh-CN` | Akoestisch model<br>Taalmodel                 |
|Chinees (Taiwan Mandarijn)       |`zh-TW` | Taalmodel                                   |
|Kroatisch (Kroatië)                 |`hr-HR` | Taalmodel                                   |
|Tsjechisch (Tsjechische Republiek)             |`cs-CZ` | Taal model                                   |
|Deens (Denemarken)                   |`da-DK` | Taalmodel                                   |
|Nederlands (Nederland)                |`nl-NL` | Taalmodel                                   |
|Engels (Australië)                |`en-AU` | Akoestisch model<br>Taalmodel                 |
|Engels (Canada)                   |`en-CA` | Akoestisch model<br>Taalmodel                 |
|Engels (Hongkong)                |`en-HK` | Taal model                                   |
|Engels (India)                    |`en-IN` | Akoestisch model<br>Taalmodel                 |
|Engels (Ierland)                  |`en-IE` | Taal model                                   |
|Engels (Nieuw-Zeeland)              |`en-NZ` | Akoestisch model<br>Taalmodel                 |
|Engels (Filipijnen)              |`en-PH` | Taal model                                   |
|Engels (Singapore)                |`en-SG` | Taal model                                   |
|Engels (Zuid-Afrika)             |`en-ZA` | Taal model                                   |
|Engels (Verenigd Koninkrijk)           |`en-GB` | Akoestisch model<br>Taalmodel<br>Uitspraak van|
|Engels (Verenigde Staten)            |`en-US` | Akoestisch model<br>Taalmodel<br>Uitspraak van|
|Estisch (Estland)                  |`et-EE` | Taal model                                   |
|Fins (Finland)                  |`fi-FI` | Taalmodel                                   |
|Frans (Canada)                    |`fr-CA` | Akoestisch model<br>Taalmodel                 |
|Frans (Frankrijk)                    |`fr-FR` | Akoestisch model<br>Taalmodel<br>Uitspraak van|
|Duits (Duitsland)                   |`de-DE` | Akoestisch model<br>Taalmodel<br>Uitspraak van|
|Grieks (Griekenland)                     |`el-GR` | Taalmodel                                   |
|Gujarati (Indiase)                  |`gu-IN` | Taalmodel                                   |
|Hindi (India)                      |`hi-IN` | Akoestisch model<br>Taalmodel                 |
|Hongaars (Hongarije)                |`hu-HU` | Taal model                                   |
|Ierse (Ierland)                     |`ga-IE` | Taalmodel                                   |
|Italiaans (Italië)                    |`it-IT` | Akoestisch model<br>Taalmodel<br>Uitspraak van|
|Japans (Japan)                   |`ja-JP` | Taalmodel                                   |
|Koreaans (Korea)                     |`ko-KR` | Taalmodel                                   |
|Lets (Letland)                   |`lv-LV` | Taalmodel                                   |
|Litouws (Litouwen)             |`lt-LT` | Taalmodel                                   |
|Maltees (Malta)                     |`mt-MT` | Taalmodel                                   |
|Marathi (India)                    |`mr-IN` | Taalmodel                                   |
|Noors (Bokmål, Noorwegen)         |`nb-NO` | Taalmodel                                   |
|Pools (Polen)                    |`pl-PL` | Taalmodel                                   |
|Portugees (Brazilië)                |`pt-BR` | Akoestisch model<br>Taalmodel<br>Uitspraak van|
|Portugees (Portugal)              |`pt-PT` | Taalmodel                                   |
|Roemeens (Roemenië)                 |`ro-RO` | Taalmodel                                   |
|Russisch (Rusland)                   |`ru-RU` | Akoestisch model<br>Taalmodel                 |
|Slowaaks (Slowakije)                  |`sk-SK` | Taalmodel                                   |
|Sloveens (Slovenië)               |`sl-SI` | Taalmodel                                   |
|Spaans (Argentinië)                |`es-AR` | Taal model                                   |
|Spaans (Bolivia)                  |`es-BO` | Taal model                                   |
|Spaans (Chili)                    |`es-CL` | Taal model                                   |
|Spaans (Colombia)                 |`es-CO` | Taal model                                   |
|Spaans (Costa Rica)               |`es-CR` | Taal model                                   |
|Spaans (Cuba)                     |`es-CU` | Taal model                                   |
|Spaans (Dominicaanse Republiek)       |`es-DO` | Taal model                                   |
|Spaans (Ecuador)                  |`es-EC` | Taal model                                   |
|Spaans (El Salvador)              |`es-SV` | Taal model                                   |
|Spaans (Guatemala)                |`es-GT` | Taal model                                   |
|Spaans (Honduras)                 |`es-HN` | Taal model                                   |
|Spaans (Mexico)                   |`es-MX` | Akoestisch model<br>Taalmodel                 |
|Spaans (Nicaragua)                |`es-NI` | Taal model                                   |
|Spaans (Panama)                   |`es-PA` | Taal model                                   |
|Spaans (Paraguay)                 |`es-PY` | Taal model                                   |
|Spaans (Peru)                     |`es-PE` | Taal model                                   |
|Spaans (Puerto Rico)              |`es-PR` | Taal model                                   |
|Spaans (Spanje)                    |`es-ES` | Akoestisch model<br>Taalmodel                 |
|Spaans (Uruguay)                  |`es-UY` | Taal model                                   |
|Spaans (Verenigde Staten)                      |`es-US` | Taal model                                   |
|Spaans (Venezuela)                |`es-VE` | Taal model                                   |
|Zweeds (Zweden)                   |`sv-SE` | Taalmodel                                   |
|Tamil (India)                      |`ta-IN` | Taalmodel                                   |
|Telugu (India)                     |`te-IN` | Taalmodel                                   |
|Thai (Thailand)                    |`th-TH` | Taalmodel                                   |
|Turks (Turkije)                   |`tr-TR` | Taalmodel                                   |

## <a name="text-to-speech"></a>Tekst naar spraak

Zowel de micro soft Speech SDK als REST Api's ondersteunen deze stemmen, die elk een specifieke taal en dialect ondersteunt, geïdentificeerd door land instellingen. U kunt ook een volledige lijst met talen en stemmen verkrijgen die worden ondersteund voor elke specifieke regio/elk eind punt via de [stemmen/List-API](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> De prijzen zijn afhankelijk van de standaard, aangepaste en Neural stemmen. Ga naar de pagina met [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) voor meer informatie.

### <a name="neural-voices"></a>Neurale stemmen

Neural text-to-speech is een nieuw type spraak synthese dat wordt aangedreven door diepe Neural-netwerken. Als u een neurale stem gebruikt, is de gesynthetiseerde spraak bijna niet van de menselijke opnamen te onderscheiden.

Neural stemmen kunnen worden gebruikt om interacties te maken met chat bots uitbreiden en spraak assistenten die natuurlijk en aantrekkelijker zijn, en om digitale teksten, zoals e-books, te converteren naar Audiobooks en in-car navigatie systemen te verbeteren. Dankzij de menselijke, natuurlijke prosodie en duidelijke articulatie verminderen neurale stemmen de bij het luisteren optredende vermoeidheid wanneer gebruikers met AI-systemen communiceren.

| Taal | Landinstelling | Geslacht | Spraak naam | Stijl ondersteuning |
|---|---|---|---|---|
| Arabisch (Egypte) | `ar-EG` | Vrouw | `ar-EG-SalmaNeural` | Algemeen |
| Arabisch (Saoedi-Arabië) | `ar-SA` | Vrouw | `ar-SA-ZariyahNeural` | Algemeen |
| Bulgaars (Bulgary) | `bg-BG` | Vrouw | `bg-BG-KalinaNeural` <sup>Nieuw</sup> | Algemeen |
| Catalaans (Spanje) | `ca-ES` | Vrouw | `ca-ES-AlbaNeural` | Algemeen |
| Chinees (Kantonees, traditioneel) | `zh-HK` | Vrouw | `zh-HK-HiuGaaiNeural` | Algemeen |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-XiaoxiaoNeural` | Algemeen, meerdere spraak stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-XiaoyouNeural` | Kid Voice, geoptimaliseerd voor het opnemen van tekst in een verhaal |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Man   | `zh-CN-YunyangNeural` | Geoptimaliseerd voor lezen van nieuws,<br /> meerdere spraak stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Man   | `zh-CN-YunyeNeural` | Geoptimaliseerd voor het opnemen van tekst in een verhaal |
| Chinees (Taiwan Mandarijn) | `zh-TW` | Vrouw | `zh-TW-HsiaoYuNeural` | Algemeen |
| Kroatisch (Kroatië) | `hr-HR` | Vrouw | `hr-HR-GabrijelaNeural` <sup>Nieuw</sup> | Algemeen |
| Tsjechisch (Tsjechisch) | `cs-CZ` | Vrouw | `cs-CZ-VlastaNeural` <sup>Nieuw</sup>    | Algemeen |
| Deens (Denemarken) | `da-DK` | Vrouw | `da-DK-ChristelNeural` | Algemeen |
| Nederlands (Nederland) | `nl-NL` | Vrouw | `nl-NL-ColetteNeural` | Algemeen |
| Engels (Australië) | `en-AU` | Vrouw | `en-AU-NatashaNeural` | Algemeen |
| Engels (Australië) | `en-AU` | Man   | `en-AU-WilliamNeural` <sup>Nieuw</sup>  | Algemeen |
| Engels (Canada) | `en-CA` | Vrouw | `en-CA-ClaraNeural` | Algemeen |
| Engels (India) | `en-IN` | Vrouw | `en-IN-NeerjaNeural` | Algemeen |
| Engels (Ierland) | `en-IE` | Vrouw | `en-IE-EmilyNeural` <sup>Nieuw</sup> | Algemeen |
| Engels (Verenigd Koninkrijk) | `en-GB` | Vrouw | `en-GB-LibbyNeural` | Algemeen |
| Engels (Verenigd Koninkrijk) | `en-GB` | Vrouw | `en-GB-MiaNeural` | Algemeen |
| Engels (Verenigd Koninkrijk) | `en-GB` | Man | `en-GB-RyanNeural` <sup>Nieuw</sup> | Algemeen |
| Engels (Verenigde Staten) | `en-US` | Vrouw | `en-US-AriaNeural` | Algemeen, meerdere spraak stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Engels (Verenigde Staten) | `en-US` | Man | `en-US-GuyNeural` | Algemeen |
| Engels (Verenigde Staten) | `en-US` | Vrouw | `en-US-JennyNeural` <sup>Nieuw</sup> | Algemeen, meerdere spraak stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Fins (Finland) | `fi-FI` | Vrouw | `fi-FI-NooraNeural` | Algemeen |
| Frans (Canada) | `fr-CA` | Vrouw | `fr-CA-SylvieNeural` | Algemeen |
| Frans (Canada) | `fr-CA` | Man | `fr-CA-JeanNeural` <sup>Nieuw</sup> | Algemeen |
| Frans (Frankrijk) | `fr-FR` | Vrouw | `fr-FR-DeniseNeural` | Algemeen |
| Frans (Frankrijk) | `fr-FR` | Man | `fr-FR-HenriNeural` <sup>Nieuw</sup> | Algemeen |
| Frans (Zwitserland) | `fr-CH` | Vrouw | `fr-CH-ArianeNeural` <sup>Nieuw</sup> | Algemeen |
| Duits (Oostenrijk) | `de-AT` | Vrouw | `de-AT-IngridNeural` <sup>Nieuw</sup> | Algemeen |
| Duits (Duitsland) | `de-DE` | Vrouw | `de-DE-KatjaNeural` | Algemeen |
| Duits (Duitsland) | `de-DE` | Man | `de-DE-ConradNeural` <sup>Nieuw</sup> | Algemeen |
| Duits (Zwitserland) | `de-CH` | Vrouw | `de-CH-LeniNeural` <sup>Nieuw</sup> | Algemeen |
| Grieks (Griekenland) | `el-GR` | Vrouw | `el-GR-AthinaNeural` <sup>Nieuw</sup> | Algemeen |
| Hebreeuws (Israël) | `he-IL` | Vrouw | `he-IL-HilaNeural` <sup>Nieuw</sup> | Algemeen |
| Hindi (India) | `hi-IN` | Vrouw | `hi-IN-SwaraNeural` | Algemeen |
| Hongaars (Hongarije) | `hu-HU` | Vrouw | `hu-HU-NoemiNeural` <sup>Nieuw</sup> | Algemeen |
| Indonesisch (Indonesië) | `id-ID` | Man | `id-ID-ArdiNeural` <sup>Nieuw</sup> | Algemeen |
| Italiaans (Italië) | `it-IT` | Vrouw | `it-IT-ElsaNeural` | Algemeen |
| Italiaans (Italië) | `it-IT` | Vrouw | `it-IT-IsabellaNeural` <sup>Nieuw</sup> | Algemeen |
| Italiaans (Italië) | `it-IT` | Man | `it-IT-DiegoNeural` <sup>Nieuw</sup> | Algemeen |
| Japans (Japan) | `ja-JP` | Vrouw | `ja-JP-NanamiNeural` | Algemeen |
| Japans (Japan) | `ja-JP` | Man | `ja-JP-KeitaNeural` <sup>Nieuw</sup> | Algemeen |
| Koreaans (Korea) | `ko-KR` | Vrouw | `ko-KR-SunHiNeural` | Algemeen |
| Koreaans (Korea) | `ko-KR` | Man | `ko-KR-InJoonNeural` <sup>Nieuw</sup> | Algemeen |
| Maleis (Maleisië) | `ms-MY` | Vrouw | `ms-MY-YasminNeural` <sup>Nieuw</sup> | Algemeen |
| Noors (Bokmål, Noorwegen) | `nb-NO` | Vrouw | `nb-NO-IselinNeural` | Algemeen |
| Pools (Polen) | `pl-PL` | Vrouw | `pl-PL-ZofiaNeural` | Algemeen |
| Portugees (Brazilië) | `pt-BR` | Vrouw | `pt-BR-FranciscaNeural` | Algemeen, meerdere spraak stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Portugees (Brazilië) | `pt-BR` | Man | `pt-BR-AntonioNeural` <sup>Nieuw</sup> | Algemeen |
| Portugees (Portugal) | `pt-PT` | Vrouw | `pt-PT-FernandaNeural` | Algemeen |
| Roemeens (Roemenië) | `ro-RO` | Vrouw | `ro-RO-AlinaNeural` <sup>Nieuw</sup> | Algemeen |
| Russisch (Rusland) | `ru-RU` | Vrouw | `ru-RU-DariyaNeural` | Algemeen |
| Slowaaks (Slowakije) | `sk-SK` | Vrouw | `sk-SK-ViktoriaNeural` <sup>Nieuw</sup> | Algemeen |
| Sloveens (Slovenië) | `sl-SI` | Vrouw | `sl-SI-PetraNeural` <sup>Nieuw</sup> | Algemeen |
| Spaans (Mexico) | `es-MX` | Vrouw | `es-MX-DaliaNeural` | Algemeen |
| Spaans (Mexico) | `es-MX` | Man | `es-MX-JorgeNeural` <sup>Nieuw</sup> | Algemeen |
| Spaans (Spanje) | `es-ES` | Vrouw | `es-ES-ElviraNeural` | Algemeen |
| Spaans (Spanje) | `es-ES` | Man | `es-ES-AlvaroNeural` <sup>Nieuw</sup> | Algemeen |
| Zweeds (Zweden) | `sv-SE` | Vrouw | `sv-SE-HilleviNeural` | Algemeen |
| Tamil (India) | `ta-IN` | Vrouw | `ta-IN-PallaviNeural` <sup>Nieuw</sup> | Algemeen |
| Telugu (India) | `te-IN` | Vrouw | `te-IN-ShrutiNeural` <sup>Nieuw</sup> | Algemeen |
| Thai (Thailand) | `th-TH` | Vrouw | `th-TH-AcharaNeural` | Algemeen |
| Thai (Thailand) | `th-TH` | Vrouw | `th-TH-PremwadeeNeural` <sup>Nieuw</sup> | Algemeen |
| Turks (Turkije) | `tr-TR` | Vrouw | `tr-TR-EmelNeural` | Algemeen |
| Vietnamees (Vietnam) | `vi-VN` <sup>Nieuw</sup> | Vrouw | `vi-VN-HoaiMyNeural` | Algemeen|

#### <a name="neural-voices-in-preview"></a>Neural stemmen in Preview

Hieronder vindt u een open bare preview van Neural stemmen. 

| Taal                         | Landinstelling  | Geslacht | Spraak naam                             | Stijl ondersteuning |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Man   | `zh-CN-YunxiNeural` <sup>Nieuw</sup> | Algemeen, meerdere stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-XiaohanNeural` <sup>Nieuw</sup> | Algemeen, meerdere stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-XiaoxuanNeural` <sup>Nieuw</sup> | Algemeen, meerdere rollen-afspelen en [stijlen die beschikbaar zijn met SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-XiaomoNeural` <sup>Nieuw</sup> | Algemeen, meerdere rollen-afspelen en [stijlen die beschikbaar zijn met SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-XiaoruiNeural` <sup>Nieuw</sup> | Senior Voice, meerdere stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Estisch (Estland) | `et-EE` | Vrouw | `et-EE-AnuNeural` <sup>Nieuw</sup> | Algemeen |
| Gaelisch (Ierland) | `ga-IE` | Vrouw | `ga-IE-OrlaNeural` <sup>Nieuw</sup> | Algemeen |
| Litouws (Litouwen) | `lt-LT` | Vrouw | `lt-LT-OnaNeural` <sup>Nieuw</sup> | Algemeen |
| Lets (Letland) | `lv-LV` | Vrouw | `lv-LV-EveritaNeural` <sup>Nieuw</sup> | Algemeen |
| Maltees (Malta) | `mt-MT` | Vrouw | `mt-MT-GraceNeural` <sup>Nieuw</sup> | Algemeen |

> [!IMPORTANT]
> Stemmen in de open bare preview zijn alleen beschikbaar in drie service regio's: VS-Oost, Europa-west en Zuidoost-Azië.

Zie [regio's](regions.md#standard-and-neural-voices)voor meer informatie over regionale Beschik baarheid.

Zie voor meer informatie over het configureren en aanpassen van Neural stemmen, zoals gesp roken stijlen, de taal voor de [opmaak van spraak synthese](speech-synthesis-markup.md#adjust-speaking-styles).

> [!IMPORTANT]
> De `en-US-JessaNeural` stem is gewijzigd in `en-US-AriaNeural` . Als u ' Jessa ' eerder gebruikt, converteer dan naar ' Aria '.

> [!TIP]
> U kunt de volledige toewijzing van de service naam blijven gebruiken als ' micro soft server Speech Text to Speech Voice (nl-nl, AriaNeural) ' in uw spraakherkennings aanvragen.

### <a name="standard-voices"></a>Standaard stemmen

Meer dan 75 standaard stemmen zijn beschikbaar in meer dan 45 talen en land instellingen, waarmee u tekst kunt converteren naar gesynthesizerde spraak. Zie [regio's](regions.md#standard-and-neural-voices)voor meer informatie over regionale Beschik baarheid.

| Taal | Land instelling (BCP-47) | Geslacht | Spraak naam |
|--|--|--|--|
| Arabisch (Arabisch) | `ar-EG` | Vrouw | `ar-EG-Hoda`|
| Arabisch (Saoedi-Arabië) | `ar-SA` | Man | `ar-SA-Naayf`|
| Bulgaars (Bulgarije) | `bg-BG` | Man | `bg-BG-Ivan`|
| Catalaans (Spanje) | `ca-ES` | Vrouw | `ca-ES-HerenaRUS`|
| Chinees (Kantonees, traditioneel) | `zh-HK` | Man | `zh-HK-Danny`|
| Chinees (Kantonees, traditioneel) | `zh-HK` | Vrouw | `zh-HK-TracyRUS`|
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-HuihuiRUS`|
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Man | `zh-CN-Kangkang`|
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-Yaoyao`|
| Chinees (Taiwan Mandarijn) |  `zh-TW` | Vrouw | `zh-TW-HanHanRUS`|
| Chinees (Taiwan Mandarijn) |  `zh-TW` | Vrouw | `zh-TW-Yating`|
| Chinees (Taiwan Mandarijn) |  `zh-TW` | Man | `zh-TW-Zhiwei`|
| Kroatisch (Kroatië) | `hr-HR` | Man | `hr-HR-Matej`|
| Tsjechisch (Tsjechische Republiek) | `cs-CZ` | Man | `cs-CZ-Jakub`|
| Deens (Denemarken) | `da-DK` | Vrouw | `da-DK-HelleRUS`|
| Nederlands (Nederland) | `nl-NL` | Vrouw | `nl-NL-HannaRUS`|
| Engels (Australië) | `en-AU` | Vrouw | `en-AU-Catherine`|
| Engels (Australië) | `en-AU` | Vrouw | `en-AU-HayleyRUS`|
| Engels (Canada) | `en-CA` | Vrouw | `en-CA-HeatherRUS`|
| Engels (Canada) | `en-CA` | Vrouw | `en-CA-Linda`|
| Engels (India) | `en-IN` | Vrouw | `en-IN-Heera`|
| Engels (India) | `en-IN` | Vrouw | `en-IN-PriyaRUS`|
| Engels (India) | `en-IN` | Man | `en-IN-Ravi`|
| Engels (Ierland) | `en-IE` | Man | `en-IE-Sean`|
| Engels (Verenigd Koninkrijk) | `en-GB` | Man | `en-GB-George`|
| Engels (Verenigd Koninkrijk) | `en-GB` | Vrouw | `en-GB-HazelRUS`|
| Engels (Verenigd Koninkrijk) | `en-GB` | Vrouw | `en-GB-Susan`|
| Engels (Verenigde Staten) | `en-US` | Man | `en-US-BenjaminRUS`|
| Engels (Verenigde Staten) | `en-US` | Man | `en-US-GuyRUS`|
| Engels (Verenigde Staten) | `en-US` | Vrouw | `en-US-JessaRUS`|
| Engels (Verenigde Staten) | `en-US` | Vrouw | `en-US-ZiraRUS`|
| Fins (Finland) | `fi-FI` | Vrouw | `fi-FI-HeidiRUS`|
| Frans (Canada) | `fr-CA` | Vrouw | `fr-CA-Caroline`|
| Frans (Canada) | `fr-CA` | Vrouw | `fr-CA-HarmonieRUS`|
| Frans (Frankrijk) | `fr-FR` | Vrouw | `fr-FR-HortenseRUS`|
| Frans (Frankrijk) | `fr-FR` | Vrouw | `fr-FR-Julie`|
| Frans (Frankrijk) | `fr-FR` | Man | `fr-FR-Paul`|
| Frans (Zwitserland) | `fr-CH` | Man | `fr-CH-Guillaume`|
| Duits (Oostenrijk) | `de-AT` | Man | `de-AT-Michael`|
| Duits (Duitsland) | `de-DE` | Vrouw | `de-DE-HeddaRUS`|
| Duits (Duitsland) | `de-DE` | Man | `de-DE-Stefan`|
| Duits (Zwitserland) | `de-CH` | Man | `de-CH-Karsten`|
| Grieks (Griekenland) | `el-GR` | Man | `el-GR-Stefanos`|
| Hebreeuws (Israël) | `he-IL` | Man | `he-IL-Asaf`|
| Hindi (India) | `hi-IN` | Man | `hi-IN-Hemant`|
| Hindi (India) | `hi-IN` | Vrouw | `hi-IN-Kalpana`|
| Hongaars (Hongarije) | `hu-HU` | Man | `hu-HU-Szabolcs`|
| Indonesisch (Indonesië) | `id-ID` | Man | `id-ID-Andika`|
| Italiaans (Italië) | `it-IT` | Man | `it-IT-Cosimo`|
| Italiaans (Italië) | `it-IT` | Vrouw | `it-IT-LuciaRUS`|
| Japans (Japan) | `ja-JP` | Vrouw | `ja-JP-Ayumi`|
| Japans (Japan) | `ja-JP` | Vrouw | `ja-JP-HarukaRUS`|
| Japans (Japan) | `ja-JP` | Man | `ja-JP-Ichiro`|
| Koreaans (Korea) | `ko-KR` | Vrouw | `ko-KR-HeamiRUS`|
| Maleis (Maleisië) | `ms-MY` | Man | `ms-MY-Rizwan`|
| Noors (Bokmål, Noorwegen) | `nb-NO` | Vrouw | `nb-NO-HuldaRUS`|
| Pools (Polen) | `pl-PL` | Vrouw | `pl-PL-PaulinaRUS`|
| Portugees (Brazilië) | `pt-BR` | Man | `pt-BR-Daniel`|
| Portugees (Brazilië) | `pt-BR` | Vrouw | `pt-BR-HeloisaRUS`|
| Portugees (Portugal) | `pt-PT` | Vrouw | `pt-PT-HeliaRUS`|
| Roemeens (Roemenië) | `ro-RO` | Man | `ro-RO-Andrei`|
| Russisch (Rusland) | `ru-RU` | Vrouw | `ru-RU-EkaterinaRUS`|
| Russisch (Rusland) | `ru-RU` | Vrouw | `ru-RU-Irina`|
| Russisch (Rusland) | `ru-RU` | Man | `ru-RU-Pavel`|
| Slowaaks (Slowakije) | `sk-SK` | Man | `sk-SK-Filip`|
| Sloveens (Slovenië) | `sl-SI` | Man | `sl-SI-Lado`|
| Spaans (Mexico) | `es-MX` | Vrouw | `es-MX-HildaRUS`|
| Spaans (Mexico) | `es-MX` | Man | `es-MX-Raul`|
| Spaans (Spanje) | `es-ES` | Vrouw | `es-ES-HelenaRUS`|
| Spaans (Spanje) | `es-ES` | Vrouw | `es-ES-Laura`|
| Spaans (Spanje) | `es-ES` | Man | `es-ES-Pablo`|
| Zweeds (Zweden) | `sv-SE` | Vrouw | `sv-SE-HedvigRUS`|
| Tamil (India) | `ta-IN` | Man | `ta-IN-Valluvar`|
| Telugu (India) | `te-IN` | Vrouw | `te-IN-Chitra`|
| Thai (Thailand) | `th-TH` | Man | `th-TH-Pattara`|
| Turks (Turkije) | `tr-TR` | Vrouw | `tr-TR-SedaRUS`|
| Vietnamees (Vietnam) | `vi-VN` | Man | `vi-VN-An` |

> [!IMPORTANT]
> De `en-US-Jessa` stem is gewijzigd in `en-US-Aria` . Als u ' Jessa ' eerder gebruikt, converteer dan naar ' Aria '.

> [!TIP]
> U kunt de volledige toewijzing van de service naam blijven gebruiken als ' micro soft server Speech Text to Speech Voice (nl-nl, AriaRUS) ' in uw spraakherkennings aanvragen.

### <a name="customization"></a>Aanpassing

Stem aanpassing is beschikbaar voor,,,,,, `de-DE` `en-GB` , en `en-IN` `en-US` `es-MX` `fr-FR` `it-IT` `pt-BR` `zh-CN` . Selecteer de juiste land instelling die overeenkomt met de trainings gegevens die u nodig hebt om een aangepast spraak model te trainen. Als de opname gegevens die u hebt gesp roken in het Engels met een Britse accent, selecteert u bijvoorbeeld `en-GB` .

> [!NOTE]
> We bieden geen ondersteuning voor bidirectionele model trainingen in aangepaste spraak, met uitzonde ring van de Chinese-English bi. Selecteer ' Chinees-Engels-tweetalig ' als u een Chinese stem wilt trainen die ook Engels kan spreken. Spraak training in alle land instellingen begint met een gegevensset van 2000 + uitingen, met uitzonde ring van de `en-US` en `zh-CN` waar u kunt beginnen met elke grootte van de trainings gegevens.

## <a name="speech-translation"></a>Spraakomzetting

De API voor **spraak omzetting** ondersteunt verschillende talen voor conversie van spraak naar spraak en spraak naar tekst. De bron taal moet altijd afkomstig zijn uit de tabel met spraak-naar-tekst taal. De beschik bare doel talen zijn afhankelijk van het feit of het Vertaal doel spraak of tekst is. U kunt binnenkomende spraak omzetten in meer dan [60 talen](https://www.microsoft.com/translator/business/languages/). Er is een subset van talen beschikbaar voor [spraak synthese](language-support.md#text-languages).

### <a name="text-languages"></a>Tekst talen

| Tekst taal           | Taalcode |
|:------------------------|:-------------:|
| Afrikaans               | `af`          |
| Arabisch                  | `ar`          |
| Bengaals                  | `bn`          |
| Bosnisch (Latijns)         | `bs`          |
| Bulgaars               | `bg`          |
| Kantonees (traditioneel) | `yue`         |
| Catalaans                 | `ca`          |
| Chinees (vereenvoudigd)      | `zh-Hans`     |
| Chinees (traditioneel)     | `zh-Hant`     |
| Kroatisch                | `hr`          |
| Tsjechisch                   | `cs`          |
| Deens                  | `da`          |
| Nederlands                   | `nl`          |
| Engels                 | `en`          |
| Ests                | `et`          |
| Fijisch                  | `fj`          |
| Filipino                | `fil`         |
| Fins                 | `fi`          |
| Frans                  | `fr`          |
| Duits                  | `de`          |
| Grieks                   | `el`          |
| Gujarati                | `gu`          |
| Haïtiaans          | `ht`          |
| Hebreeuws                  | `he`          |
| Hindi                   | `hi`          |
| Hmong Daw               | `mww`         |
| Hongaars               | `hu`          |
| Indonesisch              | `id`          |
| Iers                   | `ga`          |
| Italiaans                 | `it`          |
| Japans                | `ja`          |
| Kannada                 | `kn`          |
| Kiswahili               | `sw`          |
| Klingon                 | `tlh-Latn`    |
| Klingon (plqaD)         | `tlh-Piqd`    |
| Koreaans                  | `ko`          |
| Lets                 | `lv`          |
| Litouws              | `lt`          |
| Malagassisch                | `mg`          |
| Maleisisch                   | `ms`          |
| Malayalam               | `ml`          |
| Maltees                 | `mt`          |
| Maori                   | `mi`          |
| Mahrati                 | `mr`          |
| Noors               | `nb`          |
| Perzisch                 | `fa`          |
| Pools                  | `pl`          |
| Portugees (Brazilië)     | `pt-br`       |
| Portugees (Portugal)   | `pt-pt`       |
| Punjabi                 | `pa`          |
| Queretaro Otomi         | `otq`         |
| Roemeens                | `ro`          |
| Russisch                 | `ru`          |
| Samoaans                  | `sm`          |
| Servisch (Cyrillisch)      | `sr-Cyrl`     |
| Servisch (Latijns)         | `sr-Latn`     |
| Slowaaks                  | `sk`          |
| Sloveens               | `sl`          |
| Spaans                 | `es`          |
| Zweeds                 | `sv`          |
| Tahitiaans                | `ty`          |
| Tamil                   | `ta`          |
| Telugu                  | `te`          |
| Thai                    | `th`          |
| Tongaans                  | `to`          |
| Turks                 | `tr`          |
| Oekraïens               | `uk`          |
| Urdu                    | `ur`          |
| Vietnamees              | `vi`          |
| Welsh                   | `cy`          |
| Yucateeks Maya            | `yua`         |

## <a name="speaker-recognition"></a>Speaker Recognition

Raadpleeg de volgende tabel voor ondersteunde talen voor de verschillende Speaker Recognition-Api's. Zie het [overzicht](speaker-recognition-overview.md) voor meer informatie over speaker Recognition.

| Taal | Land instelling (BCP-47) | Tekstafhankelijke verificatie | Tekstonafhankelijke verificatie | Tekstonafhankelijke identificatie |
|----|----|----|----|----|
|Engels (VS)  |  nl-NL  |  ja  |  ja  |  ja |
|Chinees (Mandarijn, vereenvoudigd) | zh-CN     |     n.v.t. |     ja |     ja|
|Engels (Australië)     | en-AU     | n.v.t.     | ja     | ja|
|Engels (Canada)     | en-CA     | n.v.t. |     ja |     ja|
|Engels (UK)     | en-GB     | n.v.t.     | ja     | ja|
|Frans (Canada)     | fr-CA     | n.v.t.     | ja |     ja|
|Frans (Frankrijk)     | fr-FR     | n.v.t.     | ja     | ja|
|Duits (Duitsland)     | de-DE     | n.v.t.     | ja     | ja|
|Italiaans | it-IT     |     n.v.t.     | ja |     ja|
|Japans     | ja-JP | n.v.t.     | ja     | ja|
|Portugees (Brazilië) | pt-BR |     n.v.t. |     ja |     ja|
|Spaans (Mexico)     | es-MX     | n.v.t. |     ja |     ja|
|Spaans (Spanje)     | es-ES | n.v.t.     | ja |     ja|

## <a name="next-steps"></a>Volgende stappen

* [Een gratis Azure-account maken](https://azure.microsoft.com/free/cognitive-services/)
* [Zie spraak herkennen in C #](./get-started-speech-to-text.md?pivots=programming-language-chsarp)