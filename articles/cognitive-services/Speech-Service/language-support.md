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
ms.date: 01/07/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 4e626cb5cac29a0e5133eb77cbaff3f4131b8456
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722344"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Taal-en spraak ondersteuning voor de spraak service

Taal ondersteuning is afhankelijk van de functionaliteit van de spraak service. In de volgende tabellen vindt u een overzicht van taal ondersteuning voor service aanbiedingen voor [spraak naar tekst](#speech-to-text), [tekst naar spraak](#text-to-speech)en [spraak omzetting](#speech-translation) .

## <a name="speech-to-text"></a>Spraak naar tekst

Zowel de micro soft Speech SDK als de REST API ondersteunen de volgende talen (land instellingen). 

Om de nauw keurigheid te verbeteren, wordt aanpassing aangeboden voor een subset van de talen via het uploaden van **Audio en Transcripten met menselijke labels** of **gerelateerde tekst: zinnen**. Ondersteuning voor aanpassing van het akoestische model met **Audio en Transcripten met menselijke labels** is beperkt tot de hieronder vermelde basis modellen. Andere basis modellen en talen gebruiken alleen de tekst van de transcripten voor het trainen van aangepaste modellen, net als bij **Verwante tekst: zinnen**. Zie aan de [slag met Custom speech](./custom-speech-overview.md)voor meer informatie over aanpassingen.

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Taal                 | Land instelling (BCP-47) | Aanpassingen  | [Taaldetectie](how-to-automatic-language-detection.md) |
|------------------------------------|--------|---------------------------------------------------|-------------------------------|
| Arabisch (Bahrein), modern, standaard  | `ar-BH` | Tekst                                   | Ja                           | 
| Arabisch (Egypte)                     | `ar-EG` | Tekst                                   | Ja                          |
| Arabisch (Irak)                      | `ar-IQ` | Tekst                                   |                           |
| Arabisch (Israël)                    | `ar-IL` | Tekst                                   |                           |
| Arabisch (Jordanië)                    | `ar-JO` | Tekst                                   |                           |
| Arabisch (Koeweit)                    | `ar-KW` | Tekst                                   |                           |
| Arabisch (Libanon)                   | `ar-LB` | Tekst                                   |                           |
| Arabisch (Oman)                      | `ar-OM` | Tekst                                   |                           |
| Arabisch (Qatar)                     | `ar-QA` | Tekst                                   |                           |
| Arabisch (Saoedi-Arabië)              | `ar-SA` | Tekst                                   | Ja                          |
| Arabisch (status van Palestijnse)        | `ar-PS` | Tekst                                   |                           |
| Arabisch (Syrië)                     | `ar-SY` | Tekst                                   | Ja                          |
| Arabisch (Verenigde Arabische Emiraten)      | `ar-AE` | Tekst                                   |                           |
| Bulgaars (Bulgarije)               | `bg-BG` | Tekst                                   |                           |
| Catalaans (Spanje)                    | `ca-ES` | Tekst                                   | Ja                          |
| Chinees (Kantonees, traditioneel)   | `zh-HK` | Audio (20201015)<br>Tekst                 |        Ja                   |
| Chinees (Mandarijn, vereenvoudigd)     | `zh-CN` | Audio (20200910)<br>Tekst                 |     Ja                      |
| Chinees (Taiwan Mandarijn)       | `zh-TW` | Audio (20190701, 20201015)<br>Tekst                 |           Ja                |
| Kroatisch (Kroatië)                 | `hr-HR` | Tekst                                   |                           |
| Tsjechisch (Tsjechische Republiek)             | `cs-CZ` | Tekst                                   |                           |
| Deens (Denemarken)                   | `da-DK` | Tekst                                   | Ja                          |
| Nederlands (Nederland)                | `nl-NL` | Audio (20201015)<br>Tekst                                   |    Ja                       |
| Engels (Australië)                | `en-AU` | Audio (20201019)<br>Tekst                 | Ja                          |
| Engels (Canada)                   | `en-CA` | Audio (20201019)<br>Tekst                 | Ja                          |
| Engels (Ghana)                    | `en-GH` | Tekst                                   |                           |
| Engels (Hongkong)                | `en-HK` | Tekst                                   |                           |
| Engels (India)                    | `en-IN` | Audio (20200923)<br>Tekst                 | Ja                          |
| Engels (Ierland)                  | `en-IE` | Tekst                                   |                           |
| Engels (Kenia)                    | `en-KE` | Tekst                                   |                           |
| Engels (Nieuw-Zeeland)              | `en-NZ` | Audio (20201019)<br>Tekst                 |  Ja                         |
| Engels (Nigeria)                  | `en-NG` | Tekst                                   |                           |
| Engels (Filipijnen)              | `en-PH` | Tekst                                   |                           |
| Engels (Singapore)                | `en-SG` | Tekst                                   |                           |
| Engels (Zuid-Afrika)             | `en-ZA` | Tekst                                   |                           |
| Engels (Tanzania)                 | `en-TZ` | Tekst                                   |                           |
| Engels (Verenigd Koninkrijk)           | `en-GB` | Audio (20201019)<br>Tekst<br>Uitspraak van| Ja                          |
| Engels (Verenigde Staten)            | `en-US` | Audio (20201019)<br>Tekst<br>Uitspraak van| Ja                          |
| Estisch (Estland)                  | `et-EE` | Tekst                                   |                           |
| Filipijns (Filipijnen)             | `fil-PH`| Tekst                                   |                           |
| Fins (Finland)                  | `fi-FI` | Tekst                                   |     Ja                      |
| Frans (Canada)                    | `fr-CA` | Audio (20201015)<br>Tekst                 |     Ja                      |
| Frans (Frankrijk)                    | `fr-FR` | Audio (20201015)<br>Tekst<br>Uitspraak van|      Ja                     |
| Frans (Zwitserland)               | `fr-CH` | Tekst                                   |                           |
| Duits (Oostenrijk)                   | `de-AT` | Tekst                                   |                           |
| Duits (Duitsland)                   | `de-DE` | Audio (20190701, 20200619, 20201127)<br>Tekst<br>Uitspraak van|  Ja                         |
| Grieks (Griekenland)                     | `el-GR` | Tekst                                   |                           |
| Gujarati (Indiase)                  | `gu-IN` | Tekst                                   |                           |
| Hindi (India)                      | `hi-IN` | Audio (20200701)<br>Tekst                 |     Ja                      |
| Hongaars (Hongarije)                | `hu-HU` | Tekst                                   |                           |
| Indonesisch (Indonesië)             | `id-ID` | Tekst                                   |                           |
| Ierse (Ierland)                     | `ga-IE` | Tekst                                   |                           |
| Italiaans (Italië)                    | `it-IT` | Audio (20201016)<br>Tekst<br>Uitspraak van|      Ja                     |
| Japans (Japan)                   | `ja-JP` | Tekst                                   |      Ja                     |
| Koreaans (Korea)                     | `ko-KR` | Audio (20201015)<br>Tekst                 |      Ja                     |
| Lets (Letland)                   | `lv-LV` | Tekst                                   |                           |
| Litouws (Litouwen)             | `lt-LT` | Tekst                                   |                           |
| Maltees (Malta)                     | `mt-MT` | Tekst                                   |                           |
| Marathi (India)                    | `mr-IN` | Tekst                                   |                           |
| Noors (Bokmål, Noorwegen)         | `nb-NO` | Tekst                                   |     Ja                      |
| Pools (Polen)                    | `pl-PL` | Tekst                                   |       Ja                    |
| Portugees (Brazilië)                | `pt-BR` | Audio (20190620, 20201015)<br>Tekst<br>Uitspraak van|          Ja                 |
| Portugees (Portugal)              | `pt-PT` | Tekst                                   |             Ja              |
| Roemeens (Roemenië)                 | `ro-RO` | Tekst                                   |                           |
| Russisch (Rusland)                   | `ru-RU` | Audio (20200907)<br>Tekst                 |                Ja           |
| Slowaaks (Slowakije)                  | `sk-SK` | Tekst                                   |                           |
| Sloveens (Slovenië)               | `sl-SI` | Tekst                                   |                           |
| Spaans (Argentinië)                | `es-AR` | Tekst                                   |                           |
| Spaans (Bolivia)                  | `es-BO` | Tekst                                   |                           |
| Spaans (Chili)                    | `es-CL` | Tekst                                   |                           |
| Spaans (Colombia)                 | `es-CO` | Tekst                                   |                           |
| Spaans (Costa Rica)               | `es-CR` | Tekst                                   |                           |
| Spaans (Cuba)                     | `es-CU` | Tekst                                   |                           |
| Spaans (Dominicaanse Republiek)       | `es-DO` | Tekst                                   |                           |
| Spaans (Ecuador)                  | `es-EC` | Tekst                                   |                           |
| Spaans (El Salvador)              | `es-SV` | Tekst                                   |                           |
| Spaans (Equatoriaal-Guinea)        | `es-GQ` | Tekst                                   |                           |
| Spaans (Guatemala)                | `es-GT` | Tekst                                   |                           |
| Spaans (Honduras)                 | `es-HN` | Tekst                                   |                           |
| Spaans (Mexico)                   | `es-MX` | Audio (20200907)<br>Tekst                 |    Ja                       |
| Spaans (Nicaragua)                | `es-NI` | Tekst                                   |                           |
| Spaans (Panama)                   | `es-PA` | Tekst                                   |                           |
| Spaans (Paraguay)                 | `es-PY` | Tekst                                   |                           |
| Spaans (Peru)                     | `es-PE` | Tekst                                   |                           |
| Spaans (Puerto Rico)              | `es-PR` | Tekst                                   |                           |
| Spaans (Spanje)                    | `es-ES` | Audio (20201015)<br>Tekst                 |  Ja                         |
| Spaans (Uruguay)                  | `es-UY` | Tekst                                   |                           |
| Spaans (Verenigde Staten)                      | `es-US` | Tekst                                   |                           |
| Spaans (Venezuela)                | `es-VE` | Tekst                                   |                           |
| Zweeds (Zweden)                   | `sv-SE` | Tekst                                   |   Ja                        |
| Tamil (India)                      | `ta-IN` | Tekst                                   |                           |
| Telugu (India)                     | `te-IN` | Tekst                                   |                           |
| Thai (Thailand)                    | `th-TH` | Tekst                                   |      Ja                     |
| Turks (Turkije)                   | `tr-TR` | Tekst                                   |                           |
| Vietnamees (Vietnam)               | `vi-VN` | Tekst                                   |                           |

## <a name="text-to-speech"></a>Tekst naar spraak

Zowel de micro soft Speech SDK als REST Api's ondersteunen deze stemmen, die elk een specifieke taal en dialect ondersteunt, geïdentificeerd door land instellingen. U kunt ook een volledige lijst met talen en stemmen verkrijgen die worden ondersteund voor elke specifieke regio/elk eind punt via de [stemmen/List-API](rest-text-to-speech.md#get-a-list-of-voices). 

> [!IMPORTANT]
> De prijzen zijn afhankelijk van de standaard, aangepaste en Neural stemmen. Ga naar de pagina met [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) voor meer informatie.

### <a name="neural-voices"></a>Neurale stemmen

Neural text-to-speech is een nieuw type spraak synthese dat wordt aangedreven door diepe Neural-netwerken. Als u een neurale stem gebruikt, is de gesynthetiseerde spraak bijna niet van de menselijke opnamen te onderscheiden.

Neural stemmen kunnen worden gebruikt om interacties te maken met chat bots uitbreiden en spraak assistenten die natuurlijk en aantrekkelijker zijn, en om digitale teksten, zoals e-books, te converteren naar Audiobooks en in-car navigatie systemen te verbeteren. Dankzij de menselijke, natuurlijke prosodie en duidelijke articulatie verminderen neurale stemmen de bij het luisteren optredende vermoeidheid wanneer gebruikers met AI-systemen communiceren.

> [!NOTE]
> Neural stemmen worden gemaakt op basis van voor beelden die een sample frequentie van 24 kHz gebruiken.
> Alle stemmen kunnen worden gesampled of gedownsamplen naar andere sampling frequenties bij het nakunsten.


| Taal | Landinstelling | Geslacht | Spraak naam | Stijl ondersteuning |
|---|---|---|---|---|
| Arabisch (Egypte) | `ar-EG` | Vrouw | `ar-EG-SalmaNeural` | Algemeen |
| Arabisch (Egypte) | `ar-EG` | Man | `ar-EG-ShakirNeural` <sup>Nieuw</sup> | Algemeen |
| Arabisch (Saoedi-Arabië) | `ar-SA` | Vrouw | `ar-SA-ZariyahNeural` | Algemeen |
| Arabisch (Saoedi-Arabië) | `ar-SA` | Man | `ar-SA-HamedNeural` <sup>Nieuw</sup> | Algemeen |
| Bulgaars (Bulgarije) | `bg-BG` | Vrouw | `bg-BG-KalinaNeural` | Algemeen |
| Bulgaars (Bulgarije) | `bg-BG` | Man | `bg-BG-BorislavNeural` <sup>Nieuw</sup> | Algemeen |
| Catalaans (Spanje) | `ca-ES` | Vrouw | `ca-ES-AlbaNeural` | Algemeen |
| Catalaans (Spanje) | `ca-ES` | Vrouw | `ca-ES-JoanaNeural` <sup>Nieuw</sup> | Algemeen |
| Catalaans (Spanje) | `ca-ES` | Man | `ca-ES-EnricNeural` <sup>Nieuw</sup> | Algemeen |
| Chinees (Kantonees, traditioneel) | `zh-HK` | Vrouw | `zh-HK-HiuGaaiNeural` | Algemeen |
| Chinees (Kantonees, traditioneel) | `zh-HK` | Vrouw | `zh-HK-HiuMaanNeural` <sup>Nieuw</sup> | Algemeen |
| Chinees (Kantonees, traditioneel) | `zh-HK` | Man | `zh-HK-WanLungNeural` <sup>Nieuw</sup> | Algemeen |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-XiaoxiaoNeural` | Algemeen, meerdere spraak stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-XiaoyouNeural` | Kid Voice, geoptimaliseerd voor het opnemen van tekst in een verhaal |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Man | `zh-CN-YunyangNeural` | Geoptimaliseerd voor lezen van nieuws,<br /> meerdere spraak stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Man | `zh-CN-YunyeNeural` | Geoptimaliseerd voor het opnemen van tekst in een verhaal  |
| Chinees (Taiwan Mandarijn) | `zh-TW` | Vrouw | `zh-TW-HsiaoChenNeural` <sup>Nieuw</sup> | Algemeen |
| Chinees (Taiwan Mandarijn) | `zh-TW` | Vrouw | `zh-TW-HsiaoYuNeural` | Algemeen |
| Chinees (Taiwan Mandarijn) | `zh-TW` | Man | `zh-TW-YunJheNeural` <sup>Nieuw</sup> | Algemeen |
| Kroatisch (Kroatië) | `hr-HR` | Vrouw | `hr-HR-GabrijelaNeural` | Algemeen |
| Kroatisch (Kroatië) | `hr-HR` | Man | `hr-HR-SreckoNeural` <sup>Nieuw</sup> | Algemeen |
| Tsjechisch (Tsjechisch) | `cs-CZ` | Vrouw | `cs-CZ-VlastaNeural` | Algemeen |
| Tsjechisch (Tsjechisch) | `cs-CZ` | Man | `cs-CZ-AntoninNeural` <sup>Nieuw</sup> | Algemeen |
| Deens (Denemarken) | `da-DK` | Vrouw | `da-DK-ChristelNeural` | Algemeen |
| Deens (Denemarken) | `da-DK` | Man | `da-DK-JeppeNeural` <sup>Nieuw</sup> | Algemeen |
| Nederlands (Nederland) | `nl-NL` | Vrouw | `nl-NL-ColetteNeural` | Algemeen |
| Nederlands (Nederland) | `nl-NL` | Vrouw | `nl-NL-FennaNeural` <sup>Nieuw</sup> | Algemeen |
| Nederlands (Nederland) | `nl-NL` | Man | `nl-NL-MaartenNeural` <sup>Nieuw</sup> | Algemeen |
| Engels (Australië) | `en-AU` | Vrouw | `en-AU-NatashaNeural` | Algemeen |
| Engels (Australië) | `en-AU` | Man | `en-AU-WilliamNeural` | Algemeen |
| Engels (Canada) | `en-CA` | Vrouw | `en-CA-ClaraNeural` | Algemeen |
| Engels (Canada) | `en-CA` | Man | `en-CA-LiamNeural` <sup>Nieuw</sup> | Algemeen |
| Engels (India) | `en-IN` | Vrouw | `en-IN-NeerjaNeural` | Algemeen |
| Engels (India) | `en-IN` | Man | `en-IN-PrabhatNeural` <sup>Nieuw</sup> | Algemeen |
| Engels (Ierland) | `en-IE` | Vrouw | `en-IE-EmilyNeural` | Algemeen |
| Engels (Ierland) | `en-IE` | Man | `en-IE-ConnorNeural` <sup>Nieuw</sup> | Algemeen |
| Engels (Verenigd Koninkrijk) | `en-GB` | Vrouw | `en-GB-LibbyNeural` | Algemeen |
| Engels (Verenigd Koninkrijk) | `en-GB` | Vrouw | `en-GB-MiaNeural` | Algemeen |
| Engels (Verenigd Koninkrijk) | `en-GB` | Man | `en-GB-RyanNeural` | Algemeen |
| Engels (Verenigde Staten) | `en-US` | Vrouw | `en-US-AriaNeural` | Algemeen, meerdere spraak stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Engels (Verenigde Staten) | `en-US` | Vrouw | `en-US-JennyNeural` | Algemeen |
| Engels (Verenigde Staten) | `en-US` | Man | `en-US-GuyNeural` | Algemeen |
| Fins (Finland) | `fi-FI` | Vrouw | `fi-FI-NooraNeural` | Algemeen |
| Fins (Finland) | `fi-FI` | Vrouw | `fi-FI-SelmaNeural` <sup>Nieuw</sup> | Algemeen |
| Fins (Finland) | `fi-FI` | Man | `fi-FI-HarriNeural` <sup>Nieuw</sup> | Algemeen |
| Frans (Canada) | `fr-CA` | Vrouw | `fr-CA-SylvieNeural` | Algemeen |
| Frans (Canada) | `fr-CA` | Man | `fr-CA-AntoineNeural` <sup>Nieuw</sup> | Algemeen |
| Frans (Canada) | `fr-CA` | Man | `fr-CA-JeanNeural` | Algemeen |
| Frans (Frankrijk) | `fr-FR` | Vrouw | `fr-FR-DeniseNeural` | Algemeen |
| Frans (Frankrijk) | `fr-FR` | Man | `fr-FR-HenriNeural` | Algemeen |
| Frans (Zwitserland) | `fr-CH` | Vrouw | `fr-CH-ArianeNeural` | Algemeen |
| Frans (Zwitserland) | `fr-CH` | Man | `fr-CH-FabriceNeural` <sup>Nieuw</sup> | Algemeen |
| Duits (Oostenrijk) | `de-AT` | Vrouw | `de-AT-IngridNeural` | Algemeen |
| Duits (Oostenrijk) | `de-AT` | Man | `de-AT-JonasNeural` <sup>Nieuw</sup> | Algemeen |
| Duits (Duitsland) | `de-DE` | Vrouw | `de-DE-KatjaNeural` | Algemeen |
| Duits (Duitsland) | `de-DE` | Man | `de-DE-ConradNeural` | Algemeen |
| Duits (Zwitserland) | `de-CH` | Vrouw | `de-CH-LeniNeural` | Algemeen |
| Duits (Zwitserland) | `de-CH` | Man | `de-CH-JanNeural` <sup>Nieuw</sup> | Algemeen |
| Grieks (Griekenland) | `el-GR` | Vrouw | `el-GR-AthinaNeural` | Algemeen |
| Grieks (Griekenland) | `el-GR` | Man | `el-GR-NestorasNeural` <sup>Nieuw</sup> | Algemeen |
| Hebreeuws (Israël) | `he-IL` | Vrouw | `he-IL-HilaNeural` | Algemeen |
| Hebreeuws (Israël) | `he-IL` | Man | `he-IL-AvriNeural` <sup>Nieuw</sup> | Algemeen |
| Hindi (India) | `hi-IN` | Vrouw | `hi-IN-SwaraNeural` | Algemeen |
| Hindi (India) | `hi-IN` | Man | `hi-IN-MadhurNeural` <sup>Nieuw</sup> | Algemeen |
| Hongaars (Hongarije) | `hu-HU` | Vrouw | `hu-HU-NoemiNeural` | Algemeen |
| Hongaars (Hongarije) | `hu-HU` | Man | `hu-HU-TamasNeural` <sup>Nieuw</sup> | Algemeen |
| Indonesisch (Indonesië) | `id-ID` | Vrouw | `id-ID-GadisNeural` <sup>Nieuw</sup> | Algemeen |
| Indonesisch (Indonesië) | `id-ID` | Man | `id-ID-ArdiNeural` | Algemeen |
| Italiaans (Italië) | `it-IT` | Vrouw | `it-IT-ElsaNeural` | Algemeen |
| Italiaans (Italië) | `it-IT` | Vrouw | `it-IT-IsabellaNeural` | Algemeen |
| Italiaans (Italië) | `it-IT` | Man | `it-IT-DiegoNeural` | Algemeen |
| Japans (Japan) | `ja-JP` | Vrouw | `ja-JP-NanamiNeural` | Algemeen |
| Japans (Japan) | `ja-JP` | Man | `ja-JP-KeitaNeural` | Algemeen |
| Koreaans (Korea) | `ko-KR` | Vrouw | `ko-KR-SunHiNeural` | Algemeen |
| Koreaans (Korea) | `ko-KR` | Man | `ko-KR-InJoonNeural` | Algemeen |
| Maleis (Maleisië) | `ms-MY` | Vrouw | `ms-MY-YasminNeural` | Algemeen |
| Maleis (Maleisië) | `ms-MY` | Man | `ms-MY-OsmanNeural` <sup>Nieuw</sup> | Algemeen |
| Noors (Bokmål, Noorwegen) | `nb-NO` | Vrouw | `nb-NO-IselinNeural` | Algemeen |
| Noors (Bokmål, Noorwegen) | `nb-NO` | Vrouw | `nb-NO-PernilleNeural` <sup>Nieuw</sup> | Algemeen |
| Noors (Bokmål, Noorwegen) | `nb-NO` | Man | `nb-NO-FinnNeural` <sup>Nieuw</sup> | Algemeen |
| Pools (Polen) | `pl-PL` | Vrouw | `pl-PL-AgnieszkaNeural` <sup>Nieuw</sup> | Algemeen |
| Pools (Polen) | `pl-PL` | Vrouw | `pl-PL-ZofiaNeural` | Algemeen |
| Pools (Polen) | `pl-PL` | Man | `pl-PL-MarekNeural` <sup>Nieuw</sup> | Algemeen |
| Portugees (Brazilië) | `pt-BR` | Vrouw | `pt-BR-FranciscaNeural` | Algemeen, meerdere spraak stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles)  |
| Portugees (Brazilië) | `pt-BR` | Man | `pt-BR-AntonioNeural` | Algemeen |
| Portugees (Portugal) | `pt-PT` | Vrouw | `pt-PT-FernandaNeural` | Algemeen |
| Portugees (Portugal) | `pt-PT` | Vrouw | `pt-PT-RaquelNeural` <sup>Nieuw</sup> | Algemeen |
| Portugees (Portugal) | `pt-PT` | Man | `pt-PT-DuarteNeural` <sup>Nieuw</sup> | Algemeen |
| Roemeens (Roemenië) | `ro-RO` | Vrouw | `ro-RO-AlinaNeural` | Algemeen |
| Roemeens (Roemenië) | `ro-RO` | Man | `ro-RO-EmilNeural` <sup>Nieuw</sup> | Algemeen |
| Russisch (Rusland) | `ru-RU` | Vrouw | `ru-RU-DariyaNeural` | Algemeen |
| Russisch (Rusland) | `ru-RU` | Vrouw | `ru-RU-SvetlanaNeural` <sup>Nieuw</sup> | Algemeen |
| Russisch (Rusland) | `ru-RU` | Man | `ru-RU-DmitryNeural` <sup>Nieuw</sup> | Algemeen |
| Slowaaks (Slowakije) | `sk-SK` | Vrouw | `sk-SK-ViktoriaNeural` | Algemeen |
| Slowaaks (Slowakije) | `sk-SK` | Man | `sk-SK-LukasNeural` <sup>Nieuw</sup> | Algemeen |
| Sloveens (Slovenië) | `sl-SI` | Vrouw | `sl-SI-PetraNeural` | Algemeen |
| Sloveens (Slovenië) | `sl-SI` | Man | `sl-SI-RokNeural` <sup>Nieuw</sup> | Algemeen |
| Spaans (Mexico) | `es-MX` | Vrouw | `es-MX-DaliaNeural` | Algemeen |
| Spaans (Mexico) | `es-MX` | Man | `es-MX-JorgeNeural` | Algemeen |
| Spaans (Spanje) | `es-ES` | Vrouw | `es-ES-ElviraNeural` | Algemeen |
| Spaans (Spanje) | `es-ES` | Man | `es-ES-AlvaroNeural` | Algemeen |
| Zweeds (Zweden) | `sv-SE` | Vrouw | `sv-SE-HilleviNeural` | Algemeen |
| Zweeds (Zweden) | `sv-SE` | Vrouw | `sv-SE-SofieNeural` <sup>Nieuw</sup> | Algemeen |
| Zweeds (Zweden) | `sv-SE` | Man | `sv-SE-MattiasNeural` <sup>Nieuw</sup> | Algemeen |
| Tamil (India) | `ta-IN` | Vrouw | `ta-IN-PallaviNeural` | Algemeen |
| Tamil (India) | `ta-IN` | Man | `ta-IN-ValluvarNeural` <sup>Nieuw</sup> | Algemeen |
| Telugu (India) | `te-IN` | Vrouw | `te-IN-ShrutiNeural` | Algemeen |
| Telugu (India) | `te-IN` | Man | `te-IN-MohanNeural` <sup>Nieuw</sup> | Algemeen |
| Thai (Thailand) | `th-TH` | Vrouw | `th-TH-AcharaNeural` | Algemeen |
| Thai (Thailand) | `th-TH` | Vrouw | `th-TH-PremwadeeNeural` | Algemeen |
| Thai (Thailand) | `th-TH` | Man | `th-TH-NiwatNeural` <sup>Nieuw</sup> | Algemeen |
| Turks (Turkije) | `tr-TR` | Vrouw | `tr-TR-EmelNeural` | Algemeen |
| Turks (Turkije) | `tr-TR` | Man | `tr-TR-AhmetNeural` <sup>Nieuw</sup> | Algemeen |
| Vietnamees (Vietnam) | `vi-VN` | Vrouw | `vi-VN-HoaiMyNeural` | Algemeen |
| Vietnamees (Vietnam) | `vi-VN` | Man | `vi-VN-NamMinhNeural` <sup>Nieuw</sup> | Algemeen |

#### <a name="neural-voices-in-preview"></a>Neural stemmen in Preview

Hieronder vindt u een open bare preview van Neural stemmen. 

| Taal                         | Landinstelling  | Geslacht | Spraak naam                             | Stijl ondersteuning |
|----------------------------------|---------|--------|----------------------------------------|---------------|
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-XiaohanNeural` | Algemeen, meerdere stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-XiaomoNeural` | Algemeen, meerdere rollen-afspelen en stijlen die beschikbaar zijn [met SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-XiaoruiNeural` | Senior Voice, meerdere stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Vrouw | `zh-CN-XiaoxuanNeural` | Algemeen, meerdere rollen-afspelen en stijlen die beschikbaar zijn [met SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Man   | `zh-CN-YunxiNeural` | Algemeen, meerdere stijlen beschikbaar [via SSML](speech-synthesis-markup.md#adjust-speaking-styles) |
| Estisch (Estland) | `et-EE` | Vrouw | `et-EE-AnuNeural` | Algemeen |
| Estisch (Estland) | `et-EE` | Man | `et-EE-KertNeural` <sup>Nieuw</sup> | Algemeen |
| Ierse (Ierland) | `ga-IE` | Vrouw | `ga-IE-OrlaNeural` | Algemeen |
| Ierse (Ierland) | `ga-IE` | Man | `ga-IE-ColmNeural` <sup>Nieuw</sup> | Algemeen |
| Lets (Letland) | `lv-LV` | Vrouw | `lv-LV-EveritaNeural` | Algemeen |
| Lets (Letland) | `lv-LV` | Man | `lv-LV-NilsNeural` <sup>Nieuw</sup> | Algemeen |
| Litouws (Litouwen) | `lt-LT` | Vrouw | `lt-LT-OnaNeural` | Algemeen |
| Litouws (Litouwen) | `lt-LT` | Man | `lt-LT-LeonasNeural` <sup>Nieuw</sup> | Algemeen |
| Maltees (Malta) | `mt-MT` | Vrouw | `mt-MT-GraceNeural` | Algemeen |
| Maltees (Malta) | `mt-MT` | Man | `mt-MT-JosephNeural` <sup>Nieuw</sup> | Algemeen |

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

> [!NOTE]
> Met twee uitzonde ringen worden standaard stemmen gemaakt op basis van steek proeven die een sample frequentie van 16 kHz gebruiken.
> **De AriaRUS** en en **-US-GuyRUS** stemmen worden ook gemaakt op basis van steek proeven die gebruikmaken van een sample frequentie van 24 kHz.
> Alle stemmen kunnen worden gesampled of gedownsamplen naar andere sampling frequenties bij het nakunsten.

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
| Engels (Verenigde Staten) | `en-US` | Vrouw | `en-US-AriaRUS`|
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

Aangepaste spraak is beschikbaar in de Standard-en Neural-laag. De ondersteunde talen verschillen voor deze twee lagen. 

| Taal | Landinstelling | Standard | Neural |
|--|--|--|--|
| Chinees (Mandarijn, vereenvoudigd) | `zh-CN` | Ja | Ja |
| Chinees (Mandarijn, vereenvoudigd), Engels, tweetalig | `zh-CN` meertalige | Ja | Ja |
| Engels (Australië) | `en-AU` | Nee | Ja |
| Engels (India) | `en-IN` | Ja | Ja |
| Engels (Verenigd Koninkrijk) | `en-GB` | Ja | Ja |
| Engels (Verenigde Staten) | `en-US` | Ja | Ja |
| Frans (Canada) | `fr-CA` | Nee | Ja |
| Frans (Frankrijk) | `fr-FR` | Ja | Ja |
| Duits (Duitsland) | `de-DE` | Ja | Ja |
| Italiaans (Italië) | `it-IT` | Ja | Ja |
| Japans (Japan) | `ja-JP` | Nee | Ja |
| Koreaans (Korea) | `ko-KR` | Nee | Ja |
| Portugees (Brazilië) | `pt-BR` | Ja | Ja |
| Spaans (Mexico) | `es-MX` | Ja | Ja |
| Spaans (Spanje) | `es-ES` | Nee | Ja |

Selecteer de juiste land instelling die overeenkomt met de trainings gegevens die u nodig hebt om een aangepast spraak model te trainen. Als de opname gegevens die u hebt gesp roken in het Engels met een Britse accent, selecteert u bijvoorbeeld `en-GB` .

> [!NOTE]
> We bieden geen ondersteuning voor bidirectionele model trainingen in aangepaste spraak, met uitzonde ring van de Chinese-English bi. Selecteer ' Chinees-Engels-tweetalig ' als u een Chinese stem wilt trainen die ook Engels kan spreken. Chinese-English tweetalige model training met de standaard methode is alleen beschikbaar in Europa-noord en Noord-Centraal vs. Aangepaste Neural-stem training is beschikbaar in UK-zuid en VS-Oost.

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
|Engels (VS)  |  en-US  |  ja  |  ja  |  ja |
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
