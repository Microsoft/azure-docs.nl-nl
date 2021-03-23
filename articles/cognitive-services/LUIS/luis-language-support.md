---
title: Taal ondersteuning-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS heeft diverse functies binnen de service. Niet alle functies bevinden zich in dezelfde taalpariteit. Zorg ervoor dat de functies die u interesseren, worden ondersteund in de taalcultuur die u als doel heeft. Een LUIS-app is specifiek voor een cultuur en kan niet meer worden gewijzigd nadat deze is ingesteld.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 724b94a44ae289063bbb1c6ebf53b38c0a617b88
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799324"
---
# <a name="language-and-region-support-for-luis"></a>Taal-en regio ondersteuning voor LUIS

LUIS heeft diverse functies binnen de service. Niet alle functies bevinden zich in dezelfde taalpariteit. Zorg ervoor dat de functies die u interesseren, worden ondersteund in de taalcultuur die u als doel heeft. Een LUIS-app is specifiek voor een cultuur en kan niet meer worden gewijzigd nadat deze is ingesteld.

## <a name="multi-language-luis-apps"></a>LUIS-apps met meerdere talen

Als u een LUIS-client toepassing met meerdere talen nodig hebt, zoals een chatbot, hebt u een aantal opties. Als LUIS alle talen ondersteunt, ontwikkelt u een LUIS-app voor elke taal. Elke LUIS-app heeft een unieke App-ID en een eindpunt logboek. Als u taal inzicht moet bieden voor een taal LUIS ondersteunt, kunt u de [Translator-service](../Translator/translator-info-overview.md) gebruiken om de utterance te vertalen in een ondersteunde taal, de utterance naar het Luis-eind punt te verzenden en de resulterende scores te ontvangen.

## <a name="languages-supported"></a>Ondersteunde talen

LUIS begrijpt uitingen in de volgende talen:

| Taal |Landinstelling  |  Vooraf gebouwd domein | Vooraf gebouwde entiteit | Aanbevelingen voor woordgroepen lijst | **[Tekst analyse](../text-analytics/language-support.md)<br>(Sentiment en<br>Woord|
|--|--|:--:|:--:|:--:|:--:|
| Arabisch (preview-modern Standard-Arabisch) |`ar-AR`|-|-|-|-|
| *[Chinees](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Nederlands |`nl-NL` |✔|-|-|✔|
| Engels (Verenigde Staten) |`en-US` | ✔ | ✔  |✔|✔|
| Frans (Canada) |`fr-CA` |-|-|-|✔|
| Frans (Frankrijk) |`fr-FR` |✔| ✔ |✔ |✔|
| Duits |`de-DE` |✔| ✔ |✔ |✔|
| Gujarati | `gu-IN`|-|-|-|-|
| Hindi | `hi-IN`|-|✔|-|-|
| Italiaans |`it-IT` |✔| ✔ |✔|✔|
| *[Japans](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Alleen sleutel woorden|
| Koreaans |`ko-KR` |✔|-|-|Alleen sleutel woorden|
| Mahrati | `mr-IN`|-|-|-|-|
| Portugees (Brazilië) |`pt-BR` |✔| ✔ |✔ |niet alle subcultuuren|
| Spaans (Mexico)|`es-MX` |-|✔|✔|✔|
| Spaans (Spanje) |`es-ES` |✔| ✔ |✔|✔|
| Tamil | `ta-IN`|-|-|-|-|
| Telugu | `te-IN`|-|-|-|-|
| Turks | `tr-TR` |✔|✔|-|Alleen sentiment|




Taal ondersteuning is afhankelijk van [vooraf gemaakte entiteiten](luis-reference-prebuilt-entities.md) en [vooraf gemaakte domeinen](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>* Japanse ondersteunings opmerkingen

 - Omdat LUIS geen syntaxis analyse biedt en geen inzicht heeft in het verschil tussen Keigo en informele Japans, moet u de verschillende formaliteiten op het niveau van de voor beelden voor uw toepassingen opnemen.
     - でございます is niet hetzelfde als です.
     - です is niet hetzelfde als だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Ondersteunde talen voor spraak-API
Zie spraak [ondersteunde talen](../speech-service/speech-to-text.md) voor talen voor spraak herkenning.

### <a name="bing-spell-check-supported-languages"></a>Bing Spellingcontrole ondersteunde talen
Zie Bing Spellingcontrole [ondersteunde talen](../bing-spell-check/language-support.md) voor een lijst met ondersteunde talen en status.

## <a name="rare-or-foreign-words-in-an-application"></a>Zeldzame of afwijkende woorden in een toepassing
In de `en-us` cultuur leert Luis om de meeste Engelse woorden, waaronder slang, te onderscheiden. In de `zh-cn` cultuur leert Luis om de meeste Chinese tekens te onderscheiden. Als u een zeldzaam woord in `en-us` of teken in gebruikt `zh-cn` , en u ziet dat Luis niet kan worden onderscheiden van dat woord of teken, kunt u dat woord of teken toevoegen aan een [lijst functie van woord groepen](luis-how-to-add-features.md). Bijvoorbeeld: woorden buiten de cultuur van de toepassing, dat wil zeggen afwijkende woorden--moeten worden toegevoegd aan een woordgroepen lijst functie.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Hybride talen
Hybride talen combi neren woorden uit twee cult uren, zoals Engels en Chinees. Deze talen worden niet ondersteund in LUIS omdat een app is gebaseerd op één cultuur.

## <a name="tokenization"></a>Token
Om machine learning uit te voeren, breekt LUIS een utterance in [tokens](luis-glossary.md#token) op basis van de cultuur.

|Taal|  elke spatie of speciaal teken | teken niveau|samengestelde woorden
|--|:--:|:--:|:--:|
|Arabisch|✔|||
|Chinees||✔||
|Nederlands|✔||✔|
|Engels (en-us)|✔ |||
|Frans (fr-FR)|✔|||
|Frans (FR-CA)|✔|||
|Duits|✔||✔|
|Gujarati|✔|||
|Hindi|✔|||
|Italiaans|✔|||
|Japans|||✔
|Koreaans||✔||
|Mahrati|✔|||
|Portugees (Brazilië)|✔|||
|Spaans (es-ES)|✔|||
|Spaans (es-MX)|✔|||
|Tamil|✔|||
|Telugu|✔|||
|Turks|✔|||


### <a name="custom-tokenizer-versions"></a>Aangepaste tokenizer-versies

De volgende cult uren hebben aangepaste tokenizer-versies:

|Cultuur|Versie|Doel|
|--|--|--|
|Duits<br>`de-de`|1.0.0|Tokenizes woorden door ze te splitsen met behulp van een tokenizer op basis van machine learning dat probeert samengestelde woorden op te splitsen in hun afzonderlijke onderdelen.<br>Als een gebruiker `Ich fahre einen krankenwagen` als een utterance wordt ingevoerd, wordt deze ingeschakeld `Ich fahre einen kranken wagen` . Het markeren van `kranken` en `wagen` onafhankelijk van verschillende entiteiten toestaan.|
|Duits<br>`de-de`|1.0.2|Tokenizes woorden door ze te splitsen op spaties.<br> Als een gebruiker `Ich fahre einen krankenwagen` als een utterance wordt ingevoerd, blijft het één token. `krankenwagen`Is daarom gemarkeerd als één entiteit. |
|Nederlands<br>`nl-nl`|1.0.0|Tokenizes woorden door ze te splitsen met behulp van een tokenizer op basis van machine learning dat probeert samengestelde woorden op te splitsen in hun afzonderlijke onderdelen.<br>Als een gebruiker `Ik ga naar de kleuterschool` als een utterance wordt ingevoerd, wordt deze ingeschakeld `Ik ga naar de kleuter school` . Het markeren van `kleuter` en `school` onafhankelijk van verschillende entiteiten toestaan.|
|Nederlands<br>`nl-nl`|1.0.1|Tokenizes woorden door ze te splitsen op spaties.<br> Als een gebruiker `Ik ga naar de kleuterschool` als een utterance wordt ingevoerd, blijft het één token. `kleuterschool`Is daarom gemarkeerd als één entiteit. |


### <a name="migrating-between-tokenizer-versions"></a>Migreren tussen tokenizer-versies
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID.

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`.

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

Tokeniseren gebeurt op het niveau van de app. Er wordt geen ondersteuning geboden voor tokenisatie op versie niveau.

[Importeer het bestand als een nieuwe app](luis-how-to-start-new-app.md), in plaats van een versie. Deze actie betekent dat de nieuwe app een andere app-ID heeft, maar gebruikmaakt van de Tokenizer-versie die is opgegeven in het bestand.
