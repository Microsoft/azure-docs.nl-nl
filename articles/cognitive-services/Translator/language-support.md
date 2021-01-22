---
title: Taal ondersteuning-Translator
titleSuffix: Azure Cognitive Services
description: Cognitive Services Translator ondersteunt de volgende talen voor tekst omzetting met Neural machine translation (NMT).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/10/2020
ms.author: swmachan
ms.openlocfilehash: 60da61d094316b29c8fbc5454472bb898d693937
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681580"
---
# <a name="language-and-region-support-for-text-and-speech-translation"></a>Ondersteuning van talen en regio's voor tekst-en spraak omzetting

Gebruik Translator voor het vertalen van en naar een van de talen voor 70 en tekst vertalingen. Neural machine translation (NMT) is de nieuwe standaard voor automatische vertalingen van een hoge kwaliteit en is beschikbaar als de standaard waarde met behulp van v3 van Translator wanneer er een Neural-systeem beschikbaar is.

U kunt Translator ook gebruiken in combi natie met aangepaste vertaler voor het bouwen van Neural-Vertaal systemen die inzicht hebben in de terminologie die wordt gebruikt in uw eigen bedrijf en branche, en met micro soft Speech Service om spraak omzetting toe te voegen aan uw app.

[Meer informatie over hoe automatische vertaling werkt](https://www.microsoft.com/translator/mt.aspx)

## <a name="text-translation"></a>Tekstomzetting
Tekst omzetting is beschikbaar via de Vertaal bewerking naar of vanuit een van de beschik bare talen in Translator. De API biedt ook taal detectie met behulp van de detectie bewerking, vele met behulp van de trans actie, en tweetalige woorden lijsten met behulp van de voor beelden van dictionary lookup en Dictionary. De beschik bare talen voor elk van deze bewerkingen worden hieronder weer gegeven. 

### <a name="translate"></a>Translator

Translator ondersteunt de volgende talen voor tekst vertaling. 

[Naslag documentatie voor de Vertaal bewerking weer geven](reference/v3-0-translate.md)

| Taal | Taalcode |
|:-|:-:|
| Afrikaans | `af` |
| Arabisch | `ar` |
| Assamees | `as` |
| Bengaals | `bn` |
| Bosnisch (Latijns) | `bs` |
| Bulgaars | `bg` |
| Kantonees (traditioneel) | `yue` |
| Catalaans | `ca` |
| Chinees (vereenvoudigd) | `zh-Hans` |
| Chinees (traditioneel) | `zh-Hant` |
| Kroatisch | `hr` |
| Tsjechisch | `cs` |
| Dari | `prs` |
| Deens | `da` |
| Nederlands | `nl` |
| Engels | `en` |
| Ests | `et` |
| Fijisch | `fj` |
| Filipino | `fil` |
| Fins | `fi` |
| Frans | `fr` |
| Frans (Canada) | `fr-ca` |
| Duits | `de` |
| Grieks | `el` |
| Gujarati | `gu` |
| Haïtiaans | `ht` |
| Hebreeuws | `he` |
| Hindi | `hi` |
| Hmong Daw | `mww` |
| Hongaars | `hu` |
| IJslands | `is` |
| Indonesisch | `id` |
| Iers | `ga` |
| Italiaans | `it` |
| Japans | `ja` |
| Kannada | `kn` |
| Kazachs | `kk` |
| Klingon | `tlh-Latn` |
| Klingon (plqaD) | `tlh-Piqd` |
| Koreaans | `ko` |
| Koerdisch (centraal) | `ku` |
| Koerdisch (noordelijk) | `kmr` |
| Lets | `lv` |
| Litouws | `lt` |
| Malagassisch | `mg` |
| Maleisisch | `ms` |
| Malayalam | `ml` |
| Maltees | `mt` |
| Maori | `mi` |
| Mahrati | `mr` |
| Noors | `nb` |
| Odia | `or` |
| Pashto | `ps` |
| Perzisch | `fa` |
| Pools | `pl` |
| Portugees (Brazilië) | `pt-br` |
| Portugees (Portugal) | `pt-pt` |
| Punjabi | `pa` |
| Queretaro Otomi | `otq` |
| Roemeens | `ro` |
| Russisch | `ru` |
| Samoaans | `sm` |
| Servisch (Cyrillisch) | `sr-Cyrl` |
| Servisch (Latijns) | `sr-Latn` |
| Slowaaks | `sk` |
| Sloveens | `sl` |
| Spaans | `es` |
| Swahili | `sw` |
| Zweeds | `sv` |
| Tahitiaans | `ty` |
| Tamil | `ta` |
| Telugu | `te` |
| Thai | `th` |
| Tongaans | `to` |
| Turks | `tr` |
| Oekraïens | `uk` |
| Urdu | `ur` |
| Vietnamees | `vi` |
| Welsh | `cy` |
| Yucateeks Maya | `yua` |

> [!NOTE]
> Taal code `pt` wordt standaard ingesteld op `pt-br` Portugees (Brazilië).

### <a name="detect"></a>Detecteren

Translator detecteert de volgende talen voor vertaal-en vele.

[Referentie documentatie voor detectie bewerkingen weer geven](reference/v3-0-detect.md)

| Taal | Taalcode |
|:-|:-:|
| Afrikaans | `af` |
| Arabisch | `ar` |
| Bulgaars | `bg` |
| Catalaans | `ca` |
| Chinees (vereenvoudigd) | `zh-Hans` |
| Chinees (traditioneel) | `zh-Hant` |
| Kroatisch | `hr` |
| Tsjechisch | `cs` |
| Deens | `da` |
| Nederlands | `nl` |
| Engels | `en` |
| Ests | `et` |
| Fins | `fi` |
| Frans | `fr` |
| Duits | `de` |
| Grieks | `el` |
| Gujarati | `gu` |
| Haïtiaans | `ht` |
| Hebreeuws | `he` |
| Hindi | `hi` |
| Hongaars | `hu` |
| IJslands | `is` |
| Indonesisch | `id` |
| Iers | `ga` |
| Italiaans | `it` |
| Japans | `ja` |
| Klingon | `tlh-Latn` |
| Koreaans | `ko` |
| Koerdisch (centraal) | `ku-Arab` |
| Lets | `lv` |
| Litouws | `lt` |
| Maleisisch | `ms` |
| Maltees | `mt` |
| Noors | `nb` |
| Pashto | `ps` |
| Perzisch | `fa` |
| Pools | `pl` |
| Portugees | `pt` |
| Roemeens | `ro` |
| Russisch | `ru` |
| Servisch (Cyrillisch) | `sr-Cyrl` |
| Servisch (Latijns) | `sr-Latn` |
| Slowaaks | `sk` |
| Sloveens | `sl` |
| Spaans | `es` |
| Swahili | `sw` |
| Zweeds | `sv` |
| Tahitiaans | `ty` |
| Thai | `th` |
| Turks | `tr` |
| Oekraïens | `uk` |
| Urdu | `ur` |
| Vietnamees | `vi` |
| Welsh | `cy` |
| Yucateeks Maya | `yua` |

### <a name="transliterate"></a>Transcriberen

De methode transtranscrib ondersteunt de volgende talen. In de ' aan/van ', ' <-> ' geeft aan dat de taal kan worden getranscribeerd van of naar een van de vermelde scripts. De '--> ' geeft aan dat de taal alleen kan worden getranscribeerd van het ene script naar het andere.

[Referentie documentatie voor trans-isatie bewerking weer geven](reference/v3-0-translate.md)


| Taal    | Taalcode | Script | Naar/van | Script|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| Arabisch | `ar` | Arabisch `Arab` | <--> | Latijnse `Latn` |
| Bengaals  | `bn` | Bengaals `Beng` | <--> | Latijnse `Latn` |
|Wit-Russisch| `be` | Cyrillisch `Cyrl`  | <--> | Latijnse `Latn` |
|Bulgaars| `bg` | Cyrillisch `Cyrl`  | <--> | Latijnse `Latn` |
| Chinees (Vereenvoudigd) | `zh-Hans` | Vereenvoudigd Chinees `Hans`| <--> | Latijnse `Latn` |
| Chinees (Vereenvoudigd) | `zh-Hans` | Vereenvoudigd Chinees `Hans`| <--> | Traditioneel Chinees `Hant`|
| Chinees (Traditioneel) | `zh-Hant` | Traditioneel Chinees `Hant`| <--> | Latijnse `Latn` |
| Chinees (Traditioneel) | `zh-Hant` | Traditioneel Chinees `Hant`| <--> | Vereenvoudigd Chinees `Hans` |
|Grieks| `el` | Grieks `Grek`  | <--> | Latijnse `Latn` |
| Gujarati | `gu`  | Gujarati `Gujr` | <--> | Latijnse `Latn` |
| Hebreeuws | `he` | Hebreeuws `Hebr` | <--> | Latijnse `Latn` |
| Hindi | `hi` | Devanagari `Deva` | <--> | Latijnse `Latn` |
| Japans | `ja` | Japans `Jpan` | <--> | Latijnse `Latn` |
| Kannada | `kn` | Kannada `Knda` | <--> | Latijnse `Latn` |
|Kazachs| `kk` | Cyrillisch `Cyrl`  | <--> | Latijnse `Latn` |
|Kirgizisch| `ky` | Cyrillisch `Cyrl`  | <--> | Latijnse `Latn` |
|Macedonisch| `mk` | Cyrillisch `Cyrl`  | <--> | Latijnse `Latn` |
| Malayalam | `ml` | Malajalam `Mlym` | <--> | Latijnse `Latn` |
| Mahrati | `mr` | Devanagari `Deva` | <--> | Latijnse `Latn` |
|Mongools| `mn` | Cyrillisch `Cyrl`  | <--> | Latijnse `Latn` |
| Odia | `or` | Odia `Orya` | <--> | Latijnse `Latn` |
|Perzisch| `fa` | Arabisch `Arab`  | <--> | Latijnse `Latn` |
| Punjabi | `pa` | Gurmukhi `Guru`  | <--> | Latijnse `Latn`  |
|Russisch| `ru` | Cyrillisch `Cyrl`  | <--> | Latijnse `Latn` |
| Servisch (Cyrillisch) | `sr-Cyrl` | Cyrillisch `Cyrl`  | --> | Latijnse `Latn` |
| Servisch (Latijns) | `sr-Latn` | Latijnse `Latn` | --> | Cyrillisch `Cyrl`|
|Sindhi| `sd` | Arabisch `Arab`  | <--> | Latijnse `Latn` |
|Tadzjieks| `tg` | Cyrillisch `Cyrl`  | <--> | Latijnse `Latn` |
| Tamil | `ta` | Tamil `Taml` | <--> | Latijnse `Latn` |
|Tataars| `tt` | Cyrillisch `Cyrl`  | <--> | Latijnse `Latn` |
| Telugu | `te` | Telugu `Telu` | <--> | Latijnse `Latn` |
| Thai | `th` | Thais `Thai` | --> | Latijnse `Latn` |
|Oekraïens| `uk` | Cyrillisch `Cyrl`  | <--> | Latijnse `Latn` |
|Urdu| `ur` | Arabisch `Arab`  | <--> | Latijnse `Latn` |

### <a name="dictionary"></a>Woordenlijst

De woorden lijst ondersteunt de volgende talen in of vanuit het Engels met behulp van de methoden voor zoeken en voor beelden.

Referentie documentatie weer geven voor de bewerkingen voor het [opzoeken van woorden lijsten](reference/v3-0-dictionary-lookup.md) en voor [beelden van woorden lijsten](reference/v3-0-dictionary-examples.md) .

| Taal    | Taalcode |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabisch       | `ar`          |
| Bengaals      | `bn`          |
| Bosnisch (Latijns)      | `bs`          |
| Bulgaars      | `bg`          |
| Catalaans      | `ca`          |
| Chinees (vereenvoudigd)      | `zh-Hans`          |
| Kroatisch      | `hr`          |
| Tsjechisch      | `cs`          |
| Deens      | `da`          |
| Nederlands      | `nl`          |
| Ests      | `et`          |
| Fins      | `fi`          |
| Frans      | `fr`          |
| Duits      | `de`          |
| Grieks      | `el`          |
| Haïtiaans      | `ht`          |
| Hebreeuws      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Hongaars      | `hu`          |
| IJslands    | `is`  |
| Indonesisch      | `id`          |
| Italiaans      | `it`          |
| Japans      | `ja`          |
| Klingon      | `tlh`          |
| Koreaans      | `ko`          |
| Lets      | `lv`          |
| Litouws      | `lt`          |
| Maleisisch      | `ms`          |
| Maltees      | `mt`          |
| Noors      | `nb`          |
| Perzisch      | `fa`          |
| Pools      | `pl`          |
| Portugees (Brazilië)     | `pt`          |
| Roemeens      | `ro`          |
| Russisch      | `ru`          |
| Servisch (Latijns)      | `sr-Latn`          |
| Slowaaks     | `sk`          |
| Sloveens      | `sl`          |
| Spaans      | `es`          |
| Swahili      | `sw`          |
| Zweeds      | `sv`          |
| Tamil      | `ta`          |
| Thai      | `th`          |
| Turks      | `tr`          |
| Oekraïens      | `uk`          |
| Urdu      | `ur`          |
| Vietnamees      | `vi`          |
| Welsh      | `cy`          |

### <a name="access-the-translator-language-list-programmatically"></a>Programmatisch toegang tot de lijst met Vertaal talen

U kunt een lijst met ondersteunde talen voor Translator ophalen met behulp van de talen methode. U kunt de lijst weer geven op functie, taal code en de taal naam in het Engels of een andere ondersteunde taal. Deze lijst wordt automatisch bijgewerkt door de micro soft Translator-service wanneer er nieuwe talen beschikbaar worden gesteld.

[Documentatie voor het bewerkings overzicht van talen weer geven](reference/v3-0-languages.md)

## <a name="customization"></a>Aanpassing

De volgende talen zijn beschikbaar voor aanpassing in of vanuit het Engels met [aangepaste vertaler](https://aka.ms/CustomTranslator).

| Taal    | Taalcode |
|:----------- |:-------------:|
|Afrikaans| `af`|
| Arabisch       | `ar`          |
| Bengaals      | `bn`          |
| Bosnisch (Latijns)      | `bs`          |
| Bulgaars      | `bg`          |
|Catalaans|   `ca`    |
| Chinees (vereenvoudigd)      | `zh-Hans`          |
|Chinees (traditioneel)|   `zh-Hant`   |
| Kroatisch      | `hr`          |
| Tsjechisch      | `cs`          |
| Deens      | `da`          |
| Nederlands      | `nl`          |
| Engels    | `en`     |
| Ests      | `et`          |
|Fijisch|    `fj`    |
|Filipino|  `fil`   |
| Fins      | `fi`          |
| Frans      | `fr`          |
| Duits      | `de`          |
| Grieks      | `el`          |
| Gujarati| `gu`    |
| Hebreeuws      | `he`          |
| Hindi      | `hi`          |
| Hongaars      | `hu`          |
| IJslands | `is` |
| Indonesisch|   `id`    |
| Iers | `ga`  |
| Italiaans      | `it`          |
| Japans      | `ja`          |
|Kannada|`kn`|
| Koreaans      | `ko`          |
| Lets      | `lv`          |
| Litouws      | `lt`          |
| Malagassisch| `mg`    |
| Maleisisch|    `ms` |
|Maltees|   `mt`    |
| Maori| `mi`  |
| Mahrati| `mr`  |
| Noors      | `nb`          |
| Perzisch      | `fa`          |
| Pools      | `pl`          |
| Portugees (Brazilië) | `pt-br` |
| Punjabi|`pa`|
| Roemeens      | `ro`          |
| Russisch      | `ru`          |
| Samoaans|   `sm`    |
| Servisch (Latijns)      | `sr-Latn`          |
| Slowaaks     | `sk`          |
| Sloveens      | `sl`          |
| Spaans      | `es`          |
| Swahili|  `sw`    |
| Zweeds      | `sv`          |
|Tahitiaans|  `ty`    |
| Thai      | `th`          |
|Tongaans|    `to`    |
| Turks      | `tr`          |
| Oekraïens      | `uk`          |
| Urdu| `ur`    |
| Vietnamees      | `vi`          |
| Welsh | `cy` |

## <a name="speech-translation"></a>Speech Translation
Spraak omzetting is beschikbaar via Translator met Cognitive Services Speech Service. Raadpleeg de documentatie van de [Speech-Service](../speech-service/index.yml) voor meer informatie over het gebruik van spraak omzetting en voor het weer geven van alle [beschik bare taal opties](../speech-service/language-support.md).

### <a name="speech-to-text"></a>Spraak naar tekst
Converteer spraak naar tekst om om te zetten naar de tekst taal van uw keuze. Spraak-naar-tekst wordt gebruikt voor spraak naar tekst omzetting, of voor conversie van spraak naar spraak wanneer deze wordt gebruikt in combi natie met spraak synthese.

| Taal    |
|:----------- |
|Arabisch|
|Kantonees (traditioneel)|
|Catalaans|
|Chinees (vereenvoudigd)|
|Chinees (traditioneel)|
|Deens|
|Nederlands|
|Engels|
|Fins|
|Frans|
|Frans (Canada)|
|Duits|
|Gujarati|
|Hindi|
|Italiaans|
|Japans|
|Koreaans|
|Mahrati|
|Noors|
|Pools|
|Portugees (Brazilië)|
|Portugees (Portugal)|
|Russisch|
|Spaans|
|Zweeds|
|Tamil|
|Telugu|
|Thai|
|Turks|

### <a name="text-to-speech"></a>Tekst naar spraak
Zet tekst om in spraak. Tekst-naar-spraak wordt gebruikt om hoorbare uitvoer van Vertaal resultaten toe te voegen, of voor conversie van spraak naar spraak bij gebruik met spraak-naar-tekst. 

| Taal |
|:-|
| Arabisch |
| Bulgaars |
| Kantonees (traditioneel) |
| Catalaans |
| Chinees (vereenvoudigd) |
| Chinees (traditioneel) |
| Kroatisch |
| Tsjechisch |
| Deens |
| Nederlands |
| Engels |
| Fins |
| Frans |
| Frans (Canada) |
| Duits |
| Grieks |
| Hebreeuws |
| Hindi |
| Hongaars |
| Indonesisch |
| Italiaans |
| Japans |
| Koreaans |
| Maleisisch |
| Noors |
| Pools |
| Portugees (Brazilië) |
| Portugees (Portugal) |
| Roemeens |
| Russisch |
| Slowaaks |
| Sloveens |
| Spaans |
| Zweeds |
| Tamil |
| Telugu |
| Thai |
| Turks |
| Vietnamees |

## <a name="view-the-language-list-on-the-microsoft-translator-website"></a>De taal lijst op de micro soft Translator-website weer geven

Voor een beknopt overzicht van de talen bevat de micro soft Translator-website alle talen die door Translator worden ondersteund voor tekst vertalingen en spraak service voor spraak omzetting. Deze lijst bevat geen informatie die specifiek is voor de ontwikkelaar, zoals taal codes.

[Bekijk de lijst met talen](https://www.microsoft.com/translator/languages.aspx)
