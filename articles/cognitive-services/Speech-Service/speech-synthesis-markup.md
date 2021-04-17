---
title: Speech Synthesis Markup Language (SSML) - Speech-service
titleSuffix: Azure Cognitive Services
description: De Speech Synthesis Markup Language gebruiken om uitspraak en prosody in tekst-naar-spraak te bepalen.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1d21691af4d52892f507695a56331816b14bf517
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588374"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Synthese verbeteren met Speech Synthesis Markup Language (SSML)

Speech Synthesis Markup Language (SSML) is een op XML gebaseerde markup-taal waarmee ontwikkelaars kunnen opgeven hoe invoertekst wordt geconverteerd naar gesynthetiseerde spraak met behulp van de text-to-speech-service. Vergeleken met tekst zonder tekst kunnen ontwikkelaars met SSML de toonhoogte, uitspraak, spreeksnelheid, volume en meer van de tekst-naar-spraak-uitvoer afstemmen. Normale leestekens, zoals onderbreken na een punt of het gebruik van de juiste intonatie wanneer een zin eindigt met een vraagteken, worden automatisch afgehandeld.

De speech-service-implementatie van SSML is gebaseerd op World Wide Web Consortium [Speech Synthesis Markup Language versie 1.0 van de World Wide Web Consortium.](https://www.w3.org/TR/speech-synthesis)

> [!IMPORTANT]
> Chinese, Japanse en Koreaans tekens tellen als twee tekens voor facturering. Zie Prijzen voor [meer informatie.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

## <a name="neural-and-custom-voices"></a>Neurale en aangepaste stemmen

Gebruik een menselijke neurale stem of maak uw eigen aangepaste stem die uniek is voor uw product of merk. Zie Taalondersteuning voor een volledige lijst met ondersteunde talen, talen en [stemmen.](language-support.md) Zie Overzicht van tekst-naar-spraak voor meer informatie over neurale en [aangepaste stemmen.](text-to-speech.md)


> [!NOTE]
> U kunt stemmen in verschillende stijlen en toonhoogten horen en voorbeeldtekst lezen met behulp [Text to Speech pagina](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features).


## <a name="special-characters"></a>Speciale tekens

Houd er bij het gebruik van SSML rekening mee dat speciale tekens, zoals aanhalingstekens, apostroofs en haakjes, van een escape-teken moeten worden uitgesloten. Zie voor meer informatie [Extensible Markup Language (XML) 1.0: Bijlage D.](https://www.w3.org/TR/xml/#sec-entexpand)

## <a name="supported-ssml-elements"></a>Ondersteunde SSML-elementen

Elk SSML-document wordt gemaakt met SSML-elementen (of tags). Deze elementen worden gebruikt om toonhoogte, prosody, volume en meer aan te passen. In de volgende secties wordt beschreven hoe elk element wordt gebruikt en wanneer een element vereist of optioneel is.

> [!IMPORTANT]
> Vergeet niet om dubbele aanhalingstekens te gebruiken rond kenmerkwaarden. Voor standaarden voor goed gevormde, geldige XML moeten kenmerkwaarden tussen dubbele aanhalingstekens worden ingesloten. Is bijvoorbeeld `<prosody volume="90">` een goed gevormd, geldig element, maar `<prosody volume=90>` niet. SSML herkent mogelijk geen kenmerkwaarden die niet tussen aanhalingstekens staan.

## <a name="create-an-ssml-document"></a>Een SSML-document maken

`speak` is het hoofdelement en is **vereist voor** alle SSML-documenten. Het element bevat belangrijke informatie, zoals versie, taal en de definitie van de `speak` woordenlijst voor markeringen.

**Syntaxis**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist /optioneel |
|-----------|-------------|---------------------|
| `version` | Geeft de versie aan van de SSML-specificatie die wordt gebruikt voor het interpreteren van de document-markeringen. De huidige versie is 1.0. | Vereist |
| `xml:lang` | Hiermee geeft u de taal van het hoofddocument. De waarde kan een kleine letter, tweeletterig taalcode (bijvoorbeeld ) of de taalcode en `en` hoofdletters land/regio (bijvoorbeeld ) `en-US` bevatten. | Vereist |
| `xmlns` | Hiermee geeft u de URI op voor het document dat de markup-woordenlijst (de elementtypen en kenmerknamen) van het SSML-document definieert. De huidige URI is http://www.w3.org/2001/10/synthesis . | Vereist |

## <a name="choose-a-voice-for-text-to-speech"></a>Een stem kiezen voor tekst-naar-spraak

Het `voice` element is vereist. Deze wordt gebruikt om de stem op te geven die wordt gebruikt voor tekst-naar-spraak.

**Syntaxis**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist /optioneel |
|-----------|-------------|---------------------|
| `name` | Identificeert de stem die wordt gebruikt voor tekst-naar-spraak-uitvoer. Zie Taalondersteuning voor een volledige lijst met [ondersteunde stemmen.](language-support.md#text-to-speech) | Vereist |

**Voorbeeld**

> [!NOTE]
> In dit voorbeeld wordt de stem `en-US-JennyNeural` gebruikt. Zie Taalondersteuning voor een volledige lijst met [ondersteunde stemmen.](language-support.md#text-to-speech)

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Meerdere stemmen gebruiken

In het `speak` -element kunt u meerdere stemmen opgeven voor tekst-naar-spraak-uitvoer. Deze stemmen kunnen in verschillende talen zijn. Voor elke stem moet de tekst in een element zijn `voice` verpakt.

**Kenmerken**

| Kenmerk | Beschrijving | Vereist /optioneel |
|-----------|-------------|---------------------|
| `name` | Identificeert de stem die wordt gebruikt voor tekst-naar-spraak-uitvoer. Zie Taalondersteuning voor een volledige lijst met [ondersteunde stemmen.](language-support.md#text-to-speech) | Vereist |

> [!IMPORTANT]
> Meerdere stemmen zijn niet compatibel met het woord grensfunctie. De functie voor woordgrens moet worden uitgeschakeld om meerdere stemmen te kunnen gebruiken.

### <a name="disable-word-boundary"></a>Woordgrens uitschakelen

Afhankelijk van de speech-SDK-taal stelt u de eigenschap in `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` op op een exemplaar van het `false` `SpeechConfig` object.

# <a name="c"></a>[C#](#tab/csharp)

Zie voor meer <a href="/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty" target="_blank"> `SetProperty` </a>informatie.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Zie voor meer <a href="/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` </a>informatie.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Zie voor meer <a href="/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` </a>informatie.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Zie voor meer <a href="/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` </a>informatie.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Zie voor meer <a href="/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#setproperty-string--string-" target="_blank"> `setProperty` </a>informatie.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Zie voor meer <a href="/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>informatie.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Zie voor meer <a href="/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>informatie.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        Good morning!
    </voice>
    <voice name="en-US-GuyNeural">
        Good morning to you too Jenny!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Spreekstijlen aanpassen

De tekst-naar-spraak-service synthetiseert standaard tekst met een neutrale spreekstijl voor neurale stemmen. U kunt de spreekstijl aanpassen om verschillende emoties uit te drukken, zoals gevoel, empathie en rustig, of de stem optimaliseren voor verschillende scenario's, zoals klantenservice, newscasting en spraakassistent, met behulp van het `mstts:express-as` -element. Dit is een optioneel element dat uniek is voor de Speech-service.

Op dit moment worden aanpassingen in de spreekstijl ondersteund voor de volgende neurale stemmen:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural` (Preview)
* `zh-CN-XiaohanNeural` (Preview)
* `zh-CN-XiaomoNeural` (Preview)
* `zh-CN-XiaoxuanNeural` (Preview)
* `zh-CN-XiaoruiNeural` (Preview)

De intensiteit van de spreekstijl kan verder worden gewijzigd om beter bij uw gebruikscase te passen. U kunt een sterkere of zachte stijl opgeven met om de spraak expressiever of `styledegree` gedreigder te maken. Op dit moment worden aanpassingen in spreekstijl ondersteund voor chinese neurale stemmen (Mandarijn, Vereenvoudigd).

Naast het aanpassen van de spreekstijlen en de stijlgraad kunt u ook de parameter aanpassen, zodat de stem een andere leeftijd en een ander `role` geslacht aan het werk gaat. Een mannenstem kan bijvoorbeeld de toonhoogte verhogen en de intonatie wijzigen om een vrouwenstem te laten afbootsen, maar de naam van de stem wordt niet gewijzigd. Op dit moment worden rolaanpassingen ondersteund voor deze Chinese neurale stemmen (Mandarijn, Vereenvoudigd) :
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

Bovenstaande wijzigingen worden toegepast op zinsniveau en stijlen en rollen spelen per stem af. Als een stijl of rollenspel niet wordt ondersteund, retournt de service spraak op de standaardneutrale manier. U kunt zien welke stijlen en rollen worden ondersteund voor elke stem via de [spraaklijst-API](rest-text-to-speech.md#get-a-list-of-voices) of via het codevrije [audio-inhoud maken](https://aka.ms/audiocontentcreation) platform.

**Syntaxis**

```xml
<mstts:express-as style="string"></mstts:express-as>
```
```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
```xml
<mstts:express-as role="string" style="string"></mstts:express-as>
```
> [!NOTE]
> Op dit moment ondersteunt `styledegree` alleen Chinese neurale stemmen (Mandarijn, Vereenvoudigd). `role` biedt alleen ondersteuning voor zh-CN-VoormoNeural en zh-CN-ZalxxiaNeural.

**Kenmerken**

| Kenmerk | Beschrijving | Vereist /optioneel |
|-----------|-------------|---------------------|
| `style` | Hiermee geeft u de spreekstijl. Op dit moment zijn spreekstijlen spraaksyte specifiek. | Vereist bij het aanpassen van de spreekstijl voor een neurale stem. Als u `mstts:express-as` gebruikt, moet de stijl worden opgegeven. Als er een ongeldige waarde is opgegeven, wordt dit element genegeerd. |
| `styledegree` | Hiermee geeft u de intensiteit van de spreekstijl. **Geaccepteerde waarden:** 0,01 tot en met 2. De standaardwaarde is 1. Dit betekent de vooraf gedefinieerde stijl intensiteit. De minimale eenheid is 0,01, wat resulteert in een iets grotere trend voor de doelstijl. Een waarde van 2 resulteert in een verdubbelde van de standaardstijl intensiteit.  | Optioneel (op dit moment ondersteunt `styledegree` alleen Chinese neurale stemmen (Mandarijn, Vereenvoudigd))|
| `role` | Hiermee geeft u het spreekrolspel op. De stem zal fungeren als een andere leeftijd en een ander geslacht, maar de naam van de stem wordt niet gewijzigd.  | Optioneel (op dit moment `role` biedt alleen ondersteuning voor zh-CN-HiermoNeural en zh-CN-HebtxzhNeural.)|

Gebruik deze tabel om te bepalen welke spreekstijlen worden ondersteund voor elke neurale stem.

| Spraak                   | Stijl                     | Description                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Geeft een formele, vertrouwens- en gezaghebbende toon weer voor nieuwslevering |
|                         | `style="newscast-casual"` | Geeft een veelzijdige en informele toon weer voor algemene nieuwslevering        |
|                         | `style="narration-professional"` | Een professionele, objectieve toon voor het lezen van inhoud uitdrukken        |
|                         | `style="customerservice"` | Geeft een vriendelijke en nuttige toon aan voor klantondersteuning  |
|                         | `style="chat"`            | Geeft een informele en ongedwongen toon weer                         |
|                         | `style="cheerful"`        | Geeft een positieve en blije toon aan                         |
|                         | `style="empathetic"`      | Geeft een gevoel van aandacht en begrip               |
| `en-US-JennyNeural`     | `style="customerservice"` | Geeft een vriendelijke en nuttige toon aan voor klantondersteuning  |
|                         | `style="chat"`            | Geeft een informele en ongedwongen toon weer                         |
|                         | `style="assistant"`       | Geeft een warme en ongedwongen toon weer voor digitale assistenten    |
|                         | `style="newscast"`        | Geeft een veelzijdige en informele toon weer voor algemene nieuwslevering   |
| `en-US-GuyNeural`       | `style="newscast"`        | Geeft een formele en professionele toon weer voor het vertellen van nieuws |
| `pt-BR-FranciscaNeural` | `style="calm"`            | Geeft een cool, verzameld en samengestelde spreektijd aan. Toon, toon en prosody zijn veel uniformer in vergelijking met andere typen spraak.                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Geeft een formele en professionele toon weer voor het vertellen van nieuws |
|                         | `style="customerservice"` | Geeft een vriendelijke en nuttige toon weer voor klantondersteuning  |
|                         | `style="assistant"`       | Geeft een warme en ongedwongen toon weer voor digitale assistenten    |
|                         | `style="chat"`            | Geeft een informele en ongedwongen toon weer voor chit-chat           |
|                         | `style="calm"`            | Geeft een cool, verzameld en samengestelde spreektijd weer. Toon, toon en prosody zijn veel uniformer in vergelijking met andere typen spraak.                                |
|                         | `style="cheerful"`        | Geeft een opgeslagen en intensige toon weer, met een hogere toon en meer energie                         |
|                         | `style="sad"`             | Geeft een weersmatige toon, met een hogere toon, minder intensiteit en lagere energie voor de energie van de muziek. Veelvoorkomende indicatoren van deze emotie zijn whimpers of janken tijdens spraak.            |
|                         | `style="angry"`           | Geeft een moeto en een onderbuiktoon uit, met een lagere toonhoogte, een hogere intensiteit en een hogere energie. De spreker is in een toestand van irate, displeased en disleded.       |
|                         | `style="fearful"`         | Geeft een afdaal ensnelheidsintensaal weer, met een hogere toonhoogte, een hogere energie van de stemmen en een hogere snelheid. De spreker is in een toestand van spanning en onbespresheid.                          |
|                         | `style="disgruntled"`     | Geeft een onainlijke en klachtende toon weer. De spraak van deze emotie geeft ontstemdheid en afkeuring weer.              |
|                         | `style="serious"`         | Geeft een strikte en opdrachtintenserende toon weer. Spreker klinkt vaak korter en veel minder relaxed met vaste snelheid.          |
|                         | `style="affectionate"`    | Geeft een warme en aanstekelijke toon weer, met een hogere toonhoogte en meer energie. De spreker trekt de aandacht van de listener. De 'persoonlijkheid' van de spreker is vaak van aard.          |
|                         | `style="gentle"`          | Geeft een vriendelijke, vriendelijke en vriendelijke toon weer, met lagere toonhoogte en energie         |
|                         | `style="lyrical"`         | Emoties op een melodische en sentimentele manier uitdrukken         |
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Geeft een vriendelijke en nuttige toon weer voor klantondersteuning  |
| `zh-CN-YunyeNeural`     | `style="calm"`            | Geeft een cool, verzameld en samengestelde spreektijd weer. Toon, toonhoogte, prosody is veel uniformer in vergelijking met andere typen spraak.    |
|                         | `style="cheerful"`        | Geeft een opgeslagen en enthousiast toon weer, met een hogere toonhoogte en meer energie                         |
|                         | `style="sad"`             | Geeft een weersmatige toon weer, met een hogere toon, minder intensiteit en lagere energie. Veelvoorkomende indicatoren van deze emotie zijn janken of janken tijdens spraak.            |
|                         | `style="angry"`           | Geeft een af en toesingsgeluid weer, met een lagere toonhoogte, een hogere intensiteit en een hogere energie van de stemmen. De spreker is in staat om te worden irate, displeased en displeed.       |
|                         | `style="fearful"`         | Geeft een onder andere een nerfse toon aan, met een hogere toonhoogte, een hogere energie en een hogere snelheid. De spreker is in een toestand van spanning en onbespretbaarheid.                          |
|                         | `style="disgruntled"`     | Geeft een onainlijke en klachtende toon weer. De spraak van deze emotie geeft niet-blijdheid en afkeuring weer.              |
|                         | `style="serious"`         | Geeft een strikte en opdrachtintenserende toon aan. Spreker klinkt vaak korter en veel minder relaxed met vaste snelheid.          |
| `zh-CN-YunxiNeural`     | `style="cheerful"`        | Geeft een opgeslagen en intensige toon weer, met een hogere toon en meer energie                         |
|                         | `style="sad"`             | Geeft een weersmatige toon, met een hogere toon, minder intensiteit en lagere energie voor de energie van de muziek. Veelvoorkomende indicatoren van deze emotie zijn whimpers of janken tijdens spraak.            |
|                         | `style="angry"`           | Geeft een moetf en een matige toon aan, met een lagere toon, een hogere intensiteit en een hogere energie voor de energie van de stem. De spreker is in staat om te worden irate, displeased en displeed.       |
|                         | `style="fearful"`         | Geeft een onder andere een nerfse toon aan, met een hogere toonhoogte, een hogere energie en een hogere snelheid. De spreker is in een toestand van spanning en onbespretbaarheid.                          |
|                         | `style="disgruntled"`     | Geeft een onainlijke en klachtende toon weer. De spraak van deze emotie geeft niet-blijdheid en afkeuring weer.              |
|                         | `style="serious"`         | Geeft een strikte en opdrachtintenserende toon aan. Spreker klinkt vaak korter en veel minder relaxed met vaste snelheid.    |
|                         | `style="depressed"`       | Geeft een gedesponde toon weer met een lagere toon en energie    |
|                         | `style="embarrassed"`     | Geeft een onzekere en hesitante toon weer wanneer de spreker zich onprettig voelen   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | Geeft een opgeslagen en enthousiast toon weer, met een hogere toonhoogte en meer energie                         |
|                         | `style="sad"`             | Geeft een weersmatige toon weer, met een hogere toon, minder intensiteit en lagere energie. Veelvoorkomende indicatoren van deze emotie zijn janken of janken tijdens spraak.            |
|                         | `style="angry"`           | Geeft een moeto en een onderbuiktoon uit, met een lagere toonhoogte, een hogere intensiteit en een hogere energie. De spreker is in een toestand van irate, displeased en aanstootgevend.       |
|                         | `style="fearful"`         | Geeft een afdaal ensnelheidsintensaal weer, met een hogere toonhoogte, een hoger energiepercentage en een hogere snelheid. De spreker is in een toestand van spanning en onbespresheid.                          |
|                         | `style="disgruntled"`     | Geeft een onainlijke en klachtende toon weer. De spraak van deze emotie geeft ontstemdheid en afkeuring weer.              |
|                         | `style="serious"`         | Geeft een strikte en opdrachtintenserende toon weer. Spreker klinkt vaak korter en veel minder relaxed met vaste snelheid.    |
|                         | `style="embarrassed"`     | Geeft een onzekere en hesitante toon weer wanneer de spreker zich onprettig voelen   |
|                         | `style="affectionate"`    | Geeft een warme en aanstekelijke toon weer, met een hogere toonhoogte en meer energie. De spreker trekt de aandacht van de listener. De 'persoonlijkheid' van de spreker is vaak van aard.          |
|                         | `style="gentle"`          | Geeft een vriendelijke, vriendelijke en vriendelijke toon weer, met lagere toonhoogte en energie         |
| `zh-CN-XiaomoNeural`    | `style="cheerful"`        | Geeft een opgeslagen en intensige toon weer, met een hogere toonhoogte en meer energie                         |
|                         | `style="angry"`           | Geeft een moeto en een onderbuiktoon uit, met een lagere toonhoogte, een hogere intensiteit en een hogere energie. De spreker is in een toestand van irate, displeased en disleded.       |
|                         | `style="fearful"`         | Geeft een afdaal ensnelheidsintensaal weer, met een hogere toonhoogte, een hoger energiepercentage en een hogere snelheid. De spreker is in een toestand van spanning en onbespretbaarheid.                          |
|                         | `style="disgruntled"`     | Geeft een onainlijke en klachtende toon weer. De spraak van deze emotie geeft ons afkeurend en afkeurend weer.              |
|                         | `style="serious"`         | Geeft een strikte en opdrachtintenserende toon aan. De spreker klinkt vaak korter en veel minder relaxed met vaste snelheid.    |
|                         | `style="depressed"`       | Geeft een gedesponde toon weer met een lagere toon en energie    |
|                         | `style="gentle"`          | Geeft een mooie, vriendelijke en matige toon weer, met een lagere toon en een lagere energie         |
| `zh-CN-XiaoxuanNeural`  | `style="cheerful"`        | Geeft een opgeslagen en intensige toon weer, met een hogere toon en meer energie                         |
|                         | `style="angry"`           | Geeft een moetf en een matige toon aan, met een lagere toon, een hogere intensiteit en een hogere energie voor de energie van de stem. De spreker is in staat om te worden irate, displeased en displeed.       |
|                         | `style="fearful"`         | Geeft een onder andere een nerfse toon aan, met een hogere toonhoogte, een hogere energie en een hogere snelheid. De spreker is in een toestand van spanning en onbespretbaarheid.                          |
|                         | `style="disgruntled"`     | Geeft een onainlijke en klachtende toon weer. De spraak van deze emotie geeft niet-blijdheid en afkeuring weer.              |
|                         | `style="serious"`         | Geeft een strikte en opdrachtintenserende toon aan. Spreker klinkt vaak korter en veel minder relaxed met vaste snelheid.    |
|                         | `style="depressed"`       | Geeft een gedesponde toon weer met een lagere toon en energie    |
|                         | `style="gentle"`          | Geeft een mooie, vriendelijke en matige toon weer, met een lagere toon en een lagere energie         |
| `zh-CN-XiaoruiNeural`    | `style="sad"`             | Geeft een weersmatige toon, met een hogere toon, minder intensiteit en lagere energie voor de energie van de muziek. Veelvoorkomende indicatoren van deze emotie zijn whimpers of janken tijdens spraak.            |
|                         | `style="angry"`           | Geeft een af en toesingsgeluid weer, met een lagere toonhoogte, een hogere intensiteit en een hogere energie van de stemmen. De spreker is in een toestand van irate, displeased en disleded.       |
|                         | `style="fearful"`         | Geeft een afdaal ensnelheidsintensaal weer, met een hogere toonhoogte, een hogere energie van de stemmen en een hogere snelheid. De spreker is in een toestand van spanning en onbespresheid.                          |

Gebruik deze tabel om de ondersteunde rollen en hun definities te controleren.

|Rol                     | Beschrijving                |
|-------------------------|----------------------------|
|`role="Girl"`            | De stem wordt geïmiteerd door een vrouw. |
|`role="Boy"`             | De stem wordt geïmiteerd door een zoon. |
|`role="YoungAdultFemale"`| De stem wordt geïmiteerd door een jonge volwassen vrouw.|
|`role="YoungAdultMale"`  | De stem wordt geïmiteerd door een jonge volwassen man.|
|`role="OlderAdultFemale"`| De stem wordt geïmiteerd door een oudere volwassen vrouw.|
|`role="OlderAdultMale"`  | De stem wordt geïmiteerd door een oudere volwassen man.|
|`role="SeniorFemale"`    | De stem wordt geïmiteerd door een senior vrouw.|
|`role="SeniorMale"`      | De stem wordt geïmiteerd aan een senior man.|


**Voorbeeld**

Dit SSML-fragment laat zien hoe het element wordt gebruikt `<mstts:express-as>` om de spreekstijl te wijzigen in `cheerful` .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

Dit SSML-fragment laat zien hoe het kenmerk wordt gebruikt om de intensiteit van de spreekstijl voor `styledegree` ToxiaoNeural te wijzigen.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

In dit SSML-fragment ziet u hoe het kenmerk wordt gebruikt om het rollenspel `role` voorPetmoNeural te wijzigen.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaomoNeural">
        女儿看见父亲走了进来，问道：
        <mstts:express-as role="YoungAdultFemale" style="calm">
            “您来的挺快的，怎么过来的？”
        </mstts:express-as>
        父亲放下手提包，说：
        <mstts:express-as role="OlderAdultMale" style="calm">
            “刚打车过来的，路上还挺顺畅。”
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Een onderbreking/pauze toevoegen of verwijderen

Gebruik het -element om pauzes (of onderbrekingen) tussen woorden in te voegen of te voorkomen dat pauzes automatisch worden toegevoegd door de `break` service text-to-speech.

> [!NOTE]
> Gebruik dit element om het standaardgedrag van tekst-naar-spraak (TTS) voor een woord of woordgroep te overschrijven als de gesynthetiseerde spraak voor dat woord of de woordgroep onnadig klinkt. Stel `strength` in op om een `none` prosodische onderbreking te voorkomen, die automatisch wordt ingevoegd door de text-to-speech-service.

**Syntaxis**

```xml
<break strength="string" />
<break time="string" />
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist /optioneel |
|-----------|-------------|---------------------|
| `strength` | Hiermee geeft u de relatieve duur van een pauze met behulp van een van de volgende waarden:<ul><li>geen</li><li>x-weak</li><li>Zwakke</li><li>medium (standaard)</li><li>Sterke</li><li>x-strong</li></ul> | Optioneel |
| `time` | Hiermee geeft u de absolute duur van een pauze in seconden of milliseconden. Deze waarde moet worden ingesteld op minder dan 5000 ms. Voorbeelden van geldige waarden zijn `2s` en `500ms` | Optioneel |

| Kracht                      | Description |
|-------------------------------|-------------|
| Geen, of als er geen waarde is opgegeven | 0 ms        |
| x-weak                        | 250 ms      |
| Zwakke                          | 500 ms      |
| gemiddeld                        | 750 ms      |
| Sterke                        | 1000 ms     |
| x-strong                      | 1250 ms     |

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>Stilte toevoegen

Gebruik het `mstts:silence` -element om pauzes voor of na tekst in te voegen, of tussen de twee aangrenzende zinnen.

> [!NOTE]
>Het verschil tussen en is dat kan worden toegevoegd aan elke plaats in de tekst, maar stilte werkt alleen aan het begin of einde van invoertekst of aan de grens van twee aangrenzende `mstts:silence` `break` `break` zinnen.


**Syntaxis**

```xml
<mstts:silence  type="string"  value="string"/>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist /optioneel |
|-----------|-------------|---------------------|
| `type` | Hiermee geeft u de locatie van stilte moet worden toegevoegd: <ul><li>`Leading` – aan het begin van de tekst </li><li>`Tailing` – aan het einde van de tekst </li><li>`Sentenceboundary` – tussen aangrenzende zinnen </li></ul> | Vereist |
| `Value` | Hiermee geeft u de absolute duur van een pauze in seconden of milliseconden, moet deze waarde worden ingesteld op minder dan 5000 ms. Voorbeelden van geldige waarden zijn `2s` en `500ms` | Vereist |

**Voorbeeld** In dit voorbeeld wordt gebruikt om 200 ms stilte toe te voegen `mtts:silence` tussen twee zinnen.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="en-US-AriaNeural">
<mstts:silence  type="Sentenceboundary" value="200ms"/>
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way.
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time.
</voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Alinea's en zinnen opgeven

`p` - `s` en -elementen worden gebruikt om respectievelijk alinea's en zinnen aan te duiden. Als deze elementen ontbreken, bepaalt de text-to-speech-service automatisch de structuur van het SSML-document.

Het `p` element kan tekst en de volgende elementen bevatten: , , `audio` , , , , en `break` `phoneme` `prosody` `say-as` `sub` `mstts:express-as` `s` .

Het `s` element kan tekst en de volgende elementen bevatten: , `audio` , , , , en `break` `phoneme` `prosody` `say-as` `mstts:express-as` `sub` .

**Syntaxis**

```XML
<p></p>
<s></s>
```

**Voorbeeld**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Gebruik phonemes om uitspraak te verbeteren

Het `ph` -element wordt gebruikt voor een phonetische uitspraak in SSML-documenten. Het `ph` element kan alleen tekst bevatten, geen andere elementen. Geef altijd door mensen leesbare spraak op als terugval.

Telefoon alfabetische alfabeten bestaan uit telefoons, die bestaan uit letters, cijfers of tekens, soms in combinatie. Elke telefoon beschrijft een uniek spraakgeluid. Dit is in tegenstelling tot het Latijnse alfabet, waar elke letter meerdere gesproken geluiden kan vertegenwoordigen. Houd rekening met de verschillende uitspraak van de letter 'c' in de woorden 'candy' en 'niet meer', of de verschillende uitspraak van de lettercombinatie 'th' in de woorden 'ding' en 'die'.

> [!NOTE]
> De tag Phonemes wordt momenteel niet ondersteund voor deze vijf stemmen (et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnaNeural, lv-LV-EveritaNeural en mt-MT-GarceNeural).

**Syntaxis**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist /optioneel |
|-----------|-------------|---------------------|
| `alphabet` | Hiermee geeft u het letter alfabet moet worden gebruikt bij het synthetiseren van de uitspraak van de tekenreeks in het `ph` kenmerk. De tekenreeks die het alfabet opgeeft, moet in kleine letters worden opgegeven. Hier volgen de mogelijke alfabeten die u kunt opgeven.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">International Phonetic Alphabet</a></li><li>`sapi`&ndash; [Telefoon alfabetische spraakservice](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">Universele telefoonset</a></li></ul><br>Het alfabet is alleen van toepassing op `phoneme` de in het element .. | Optioneel |
| `ph` | Een tekenreeks met telefoons die de uitspraak van het woord in het `phoneme` element specificeren. Als de opgegeven tekenreeks niet-herkende telefoons bevat, weigert de TTS-service (text-to-speech) het hele SSML-document en produceert geen van de spraakuitvoer die is opgegeven in het document. | Vereist als u phonemes gebruikt. |

**Voorbeelden**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Aangepast woordencon gebruiken om uitspraak te verbeteren

Soms kan de text-to-speech-service een woord niet nauwkeurig uitveren. Bijvoorbeeld de naam van een bedrijf of een medische term. Ontwikkelaars kunnen definiëren hoe afzonderlijke entiteiten worden gelezen in SSML met behulp van de `phoneme` `sub` tags en . Als u echter wilt definiëren hoe meerdere entiteiten worden gelezen, kunt u een aangepast woordencon maken met behulp van de `lexicon` tag .

> [!NOTE]
> Aangepast woordencon ondersteunt momenteel UTF-8-codering.

> [!NOTE]
> Aangepaste woordencon wordt momenteel niet ondersteund voor deze vijf stemmen (et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnaNeural, lv-LV-EveritaNeural en mt-MT-GarceNeural).


**Syntaxis**

```XML
<lexicon uri="string"/>
```

**Kenmerken**

| Kenmerk | Beschrijving                               | Vereist /optioneel |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Het adres van het externe PLS-document. | Vereist.           |

**Gebruik**

Als u wilt definiëren hoe meerdere entiteiten worden gelezen, kunt u een aangepast woordenreeksbestand maken dat wordt opgeslagen als een XML- of PLS-bestand. Hier volgt een voorbeeld van een XML-bestand.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias>By the way</alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

Het `lexicon` element bevat ten minste één `lexeme` element. Elk `lexeme` element bevat ten minste één element en een of meer elementen , en `grapheme` `grapheme` `alias` `phoneme` . Het `grapheme` element bevat tekst die de <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">orthografie beschrijft. </a> De elementen worden gebruikt om de uitspraak van `alias` een acroniem of een verkorte term aan te geven. Het `phoneme` element biedt tekst waarin wordt beschreven hoe de wordt `lexeme` uitgesproken.

Het is belangrijk te weten dat u de uitspraak van een woordgroep niet rechtstreeks kunt instellen met behulp van het aangepaste woordencon. Als u de uitspraak voor een acroniem of een verkorte term moet instellen, geeft u eerst een op en koppelt u de `alias` `phoneme` aan die `alias` . Bijvoorbeeld:

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>ScotlandMV</alias>
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme>
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

U kunt ook rechtstreeks uw verwachte `alias` voor het acroniem of de verkorte term verstrekken. Bijvoorbeeld:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>Scotland Media Wave</alias>
  </lexeme>
```

> [!IMPORTANT]
> Het `phoneme` element mag geen spaties bevatten bij het gebruik van IPA.

Zie [PlS-versie 1.0 (Pronunciation Lexicon Specification) voor](https://www.w3.org/TR/pronunciation-lexicon/)meer informatie over aangepast woordenlijstbestand.

Publiceer vervolgens uw aangepaste woordenconbestand. Hoewel we geen beperkingen hebben voor de plaats waar dit bestand kan worden opgeslagen, raden we u aan om de [Azure Blob Storage.](../../storage/blobs/storage-quickstart-blobs-portal.md)

Nadat u het aangepaste woordencon hebt gepubliceerd, kunt u hier vanuit uw SSML naar verwijzen.

> [!NOTE]
> Het `lexicon` element moet zich in het element `voice` .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
          xmlns:mstts="http://www.w3.org/2001/mstts"
          xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

Wanneer u dit aangepaste woordencon gebruikt, wordt 'BTW' gelezen als 'By the way'. Benigni wordt gelezen met de opgegeven IPA -bɛˈniːnji.

**Beperkingen**
- Bestandsgrootte: de maximale grootte van het aangepaste woordenconbestand is 100 KB. Als deze grootte is bereikt, mislukt de syntheseaanvraag.
- Cachevernieuwing voor woordencons: het aangepaste woordencon wordt in de cache opgeslagen met URI als sleutel in de TTS-service wanneer deze voor het eerst wordt geladen. Woordencon met dezelfde URI wordt niet binnen 15 minuten opnieuw geladen, dus aangepaste woordenconwijziging moet tot wel 15 minuten wachten om van kracht te worden.

**Telefoonsets voor Spraakservice**

In het bovenstaande voorbeeld gebruiken we het International Phonetic Alphabet, ook wel bekend als de IPA-telefoonset. We raden ontwikkelaars aan de IPA te gebruiken, omdat dit de internationale standaard is. Voor sommige IPA-tekens hebben ze de vooraf gecomprimeerde en ontlede versie wanneer ze worden weergegeven met Unicode. Aangepast woordencon biedt alleen ondersteuning voor de gedecomprimeerde unicodes.

Gezien het feit dat de IPA niet gemakkelijk te onthouden is, definieert de Speech-service een f phonetische set voor zeven talen ( `en-US` , , , , , en `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` `zh-TW` ).

U kunt de gebruiken `sapi` als de waarde voor het kenmerk met aangepaste `alphabet` woordencons zoals hieronder wordt gedemonstreerd:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Zie de telefoonsets van de Speech-service voor meer informatie over het gedetailleerde telefoon alfabet [van de Speech-service.](speech-ssml-phonetic-sets.md)

## <a name="adjust-prosody"></a>Prosody aanpassen

Het element wordt gebruikt om wijzigingen in de toonhoogte, omtrek, bereik, snelheid, duur en volume voor de `prosody` tekst-naar-spraak-uitvoer op te geven. Het `prosody` element kan tekst en de volgende elementen bevatten: , , `audio` , , , , en `break` `p` `phoneme` `prosody` `say-as` `sub` `s` .

Omdat de prosodische kenmerkwaarden in een breed bereik kunnen variëren, interpreteert de spraakherkenning de toegewezen waarden als een suggestie van wat de werkelijke prosodische waarden van de geselecteerde stem moeten zijn. De tekst-naar-spraak-service beperkt of vervangt waarden die niet worden ondersteund. Voorbeelden van niet-ondersteunde waarden zijn een pitch van 1 MHz of een volume van 120.

**Syntaxis**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist /optioneel |
|-----------|-------------|---------------------|
| `pitch` | Geeft de basislijnpraat voor de tekst aan. U kunt de pitch uitdrukken als:<ul><li>Een absolute waarde, uitgedrukt als een getal gevolgd door 'Hz' (Hertz). Bijvoorbeeld `<prosody pitch="600Hz">some text</prosody>`.</li><li>Een relatieve waarde, uitgedrukt als een getal voorafgegaan door '+' of '-' en gevolgd door 'Hz' of 'st', dat een hoeveelheid aangeeft om de pitch te wijzigen. Bijvoorbeeld: `<prosody pitch="+80Hz">some text</prosody>` of `<prosody pitch="-2st">some text</prosody>` . De 'st' geeft aan dat de wijzigingseenheid semitone is, wat de helft is van een toon (een halve stap) op de standaarddiatone schaal.</li><li>Een constante waarde:<ul><li>x-low</li><li>Lage</li><li>gemiddeld</li><li>hoog</li><li>x-high</li><li>standaardinstelling</li></ul></li></ul> | Optioneel |
| `contour` |Contour ondersteunt nu zowel neurale als standaardstemmen. Contour vertegenwoordigt wijzigingen in de pitch. Deze wijzigingen worden weergegeven als een matrix met doelen op opgegeven tijdposities in de spraakuitvoer. Elk doel wordt gedefinieerd door sets parameterparen. Bijvoorbeeld: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>De eerste waarde in elke set parameters geeft de locatie van de pitchwijziging op als een percentage van de duur van de tekst. Met de tweede waarde geeft u de hoeveelheid op die de pitch moet verhogen of verlagen, met behulp van een relatieve waarde of een enumeratiewaarde voor de pitch (zie `pitch` ). | Optioneel |
| `range` | Een waarde die het bereik van de pitch voor de tekst vertegenwoordigt. U kunt `range` uitdrukken met behulp van dezelfde absolute waarden, relatieve waarden of enumeratiewaarden die worden gebruikt om te `pitch` beschrijven. | Optioneel |
| `rate` | Geeft de spreeksnelheid van de tekst aan. U kunt het volgende `rate` uitdrukken:<ul><li>Een relatieve waarde, uitgedrukt als een getal dat fungeert als vermenigvuldiger van de standaardwaarde. Een waarde van *1* resulteert bijvoorbeeld in geen wijziging in de snelheid. Een waarde *van 0,5* resulteert in een afdringing van de snelheid. Een waarde *van 3* resulteert in een tripling van de snelheid.</li><li>Een constante waarde:<ul><li>x-traag</li><li>Langzaam</li><li>gemiddeld</li><li>snel</li><li>x-fast</li><li>standaardinstelling</li></ul></li></ul> | Optioneel |
| `duration` | De periode die verstreken moet zijn terwijl de spraaksyntheseservice de tekst leest, in seconden of milliseconden. Bijvoorbeeld *2-en* of *1800 ms.* Duration ondersteunt alleen standaardstemmen.| Optioneel |
| `volume` | Geeft het volumeniveau van de spreekstem aan. U kunt het volume als het volgende uitdrukken:<ul><li>Een absolute waarde, uitgedrukt als een getal in het bereik van 0,0 tot 100,0, van *stilste* naar *hardste*. Bijvoorbeeld 75. De standaardwaarde is 100.0.</li><li>Een relatieve waarde, uitgedrukt als een getal voorafgegaan door '+' of '-', dat een hoeveelheid specificeert om het volume te wijzigen. Bijvoorbeeld + 10 of -5,5.</li><li>Een constante waarde:<ul><li>Stille</li><li>x-soft</li><li>Zachte</li><li>gemiddeld</li><li>Luid</li><li>x-hard</li><li>standaardinstelling</li></ul></li></ul> | Optioneel |

### <a name="change-speaking-rate"></a>Spreeksnelheid wijzigen

De spreeksnelheid kan worden toegepast op neurale stemmen en standaardstemmen op woord- of zinsniveau.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-GuyNeural">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Volume wijzigen

Volumewijzigingen kunnen worden toegepast op standaardstemmen op woord- of zinsniveau. Terwijl volumewijzigingen alleen kunnen worden toegepast op neurale stemmen op zinsniveau.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Pitch wijzigen

Pitchwijzigingen kunnen worden toegepast op standaardstemmen op woord- of zinsniveau. Terwijl pitchwijzigingen alleen kunnen worden toegepast op neurale stemmen op zinsniveau.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>De contour van de pitch wijzigen

> [!IMPORTANT]
> Wijzigingen in de pitchcontour worden nu ondersteund met neurale stemmen.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room?
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>say-as-element

`say-as` is een optioneel element dat het inhoudstype (zoals getal of datum) van de tekst van het element aangeeft. Dit biedt richtlijnen voor de engine voor spraaksynthese over hoe de tekst moet worden geseed.

**Syntaxis**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist /optioneel |
|-----------|-------------|---------------------|
| `interpret-as` | Hiermee wordt het inhoudstype van de tekst van het element aangegeven. Zie de onderstaande tabel voor een lijst met typen. | Vereist |
| `format` | Biedt aanvullende informatie over de precieze opmaak van de tekst van het element voor inhoudstypen die dubbelzinnige indelingen kunnen hebben. SSML definieert indelingen voor inhoudstypen die deze gebruiken (zie de onderstaande tabel). | Optioneel |
| `detail` | Geeft het detailniveau aan dat moet worden uitgesproken. Met dit kenmerk kan bijvoorbeeld worden gevraagd of de spraaksynthese-engine leestekens moet gebruiken. Er zijn geen standaardwaarden gedefinieerd voor `detail` . | Optioneel |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Hier volgen de ondersteunde inhoudstypen voor de `interpret-as` kenmerken `format` en . Neem het `format` kenmerk alleen op als is ingesteld op datum en `interpret-as` tijd.

| interpret-as | indeling | Interpretatie |
|--------------|--------|----------------|
| `address` | | De tekst wordt gesproken als een adres. De spraaksynthese-engine heeft het volgende:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Als 'Ik ben bij de 150e rechter noord-oost redmond washington'. |
| `cardinal`, `number` | | De tekst wordt gesproken als een kardinaliteitsnummer. De spraaksynthese-engine heeft het volgende:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Er zijn drie alternatieven. |
| `characters`, `spell-out` | | De tekst wordt gesproken als afzonderlijke letters (gespeld). De spraaksynthese-engine doet het volgende:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Als 'T E S T'. |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | De tekst wordt gesproken als een datum. Het `format` kenmerk geeft de notatie van de datum aan (*d=day, m=month en y=year*). De spraaksynthese-engine eert:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Zoals 'Vandaag is oktober 2019 2000.' |
| `digits`, `number_digit` | | De tekst wordt gesproken als een reeks afzonderlijke cijfers. De spraaksynthese-engine eert:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Als '1 2 3 4 5 6 7 8 9'. |
| `fraction` | | De tekst wordt gesproken als een fractioneel getal. De engine voor spraaksynthese houdt het volgende in:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Als 'drie achten van een inch'. |
| `ordinal` | | De tekst wordt gesproken als een rangnummer. De engine voor spraaksynthese houdt het volgende in:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Selecteer de derde optie. |
| `telephone` | | De tekst wordt gesproken als een telefoonnummer. Het `format` kenmerk kan cijfers bevatten die een landcode vertegenwoordigen. Bijvoorbeeld '1' voor de Verenigde Staten of '39' voor Italië. De spraaksynthese-engine kan deze informatie gebruiken om de uitspraak van een telefoonnummer te begeleiden. Het telefoonnummer kan ook de landcode bevatten, en als dat het het beste is, heeft dit voorrang op de landcode in de `format` . De spraaksynthese-engine eert:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />'Mijn nummer is netnummer acht acht acht vijf vijf een twee twee'. |
| `time` | hms12, hms24 | De tekst wordt als een tijd gesproken. Het kenmerk geeft aan of de tijd wordt opgegeven met behulp van een `format` 12-uurs klok (hms12) of een 24-uurs klok (hms24). Gebruik een dubbele punt om getallen te scheiden die uren, minuten en seconden vertegenwoordigen. Hier volgen geldige tijdvoorbeelden: 12:35, 1:14:32, 08:15 en 02:50:45. De spraaksynthese-engine heeft het volgende:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />'De training is om vier uur' |

**Gebruik**

Het `say-as` element mag alleen tekst bevatten.

**Voorbeeld**

De spraaksynthese-engine spreekt het volgende voorbeeld uit als 'Uw eerste aanvraag was voor één kamer op oktober en twintig met een vroege aankomst om 123.00 uur'.

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Opgenomen audio toevoegen

`audio` is een optioneel element waarmee u MP3-audio in een SSML-document kunt invoegen. De body van het audio-element kan tekst zonder tekst of SSML-markup bevatten die wordt uitgesproken als het audiobestand niet beschikbaar of niet kan worden afspelen. Daarnaast kan het element tekst en de volgende `audio` elementen bevatten: `audio` , , , , , , , en `break` `p` `s` `phoneme` `prosody` `say-as` `sub` .

Audio die in het SSML-document is opgenomen, moet aan de volgende vereisten voldoen:

* De MP3 moet worden gehost op een HTTPS-eindpunt dat toegankelijk is via internet. HTTPS is vereist en het domein dat als host voor het MP3-bestand wordt gebruikt, moet een geldig, vertrouwd TLS/SSL-certificaat presenteren.
* De MP3 moet een geldig MP3-bestand (MPEG v2) zijn.
* De bitsnelheid moet 48 kbps zijn.
* De samplefrequentie moet 16.000 Hz zijn.
* De gecombineerde totale tijd voor alle tekst- en audiobestanden in één antwoord mag niet langer zijn dan negentig (90) seconden.
* De MP3 mag geen klantspecifieke of andere gevoelige informatie bevatten.

**Syntaxis**

```xml
<audio src="string"/></audio>
```

**Kenmerken**

| Kenmerk | Beschrijving                                   | Vereist /optioneel                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Hiermee geeft u de locatie/URL van het audiobestand op. | Vereist als u het audio-element in uw SSML-document gebruikt. |

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Achtergrondgeluid toevoegen

Met `mstts:backgroundaudio` het -element kunt u achtergrondgeluid toevoegen aan uw SSML-documenten (of een audiobestand combineren met tekst-naar-spraak). Met kunt u een audiobestand op de achtergrond herhalen, vervagen aan het begin van tekst-naar-spraak en vervaagd aan het einde van `mstts:backgroundaudio` tekst-naar-spraak.

Als de opgegeven achtergrondgeluid korter is dan de tekst-naar-spraak of vervaagt, wordt deze herhaald. Als de tekst langer is dan de tekst-naar-spraak, stopt deze wanneer het vervaagt is voltooid.

Er is slechts één audiobestand op de achtergrond toegestaan per SSML-document. U kunt echter tags tussen het element invoegen om extra audio toe te `audio` `voice` voegen aan uw SSML-document.

**Syntaxis**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist /optioneel |
|-----------|-------------|---------------------|
| `src` | Hiermee geeft u de locatie/URL van het audiobestand op de achtergrond op. | Vereist als u achtergrondgeluid in uw SSML-document gebruikt. |
| `volume` | Hiermee geeft u het volume van het audiobestand op de achtergrond. **Geaccepteerde waarden:** `0` `100` inclusief. De standaardwaarde is `1`. | Optioneel |
| `fadein` | Hiermee geeft u de duur van de achtergrondgeluid 'vervaagt' in milliseconden. De standaardwaarde is `0` . Dit is het equivalent van geen vervaaging. **Geaccepteerde waarden:** `0` `10000` inclusief.  | Optioneel |
| `fadeout` | Hiermee geeft u de duur van de audio op de achtergrond vervaagt in milliseconden. De standaardwaarde is `0` . Dit is het equivalent van 'fade out'. **Geaccepteerde waarden:** `0` `10000` inclusief.  | Optioneel |

**Voorbeeld**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JennyNeural)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="bookmark-element"></a>Het element Bladwijzer

Met het element Bladwijzer kunt u aangepaste markeringen invoegen in SSML om de offset van elke markering in de audiostroom op te halen.
We lezen de bladwijzerelementen niet uit.
Het bladwijzerelement kan worden gebruikt om te verwijzen naar een specifieke locatie in de tekst- of tagreeks.

> [!NOTE]
> `bookmark` element werkt voor `en-US-AriaNeural` nu alleen voor spraak.

**Syntaxis**

```xml
<bookmark mark="string"/>
```

**Kenmerken**

| Kenmerk | Beschrijving                                   | Vereist /optioneel                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
|  `mark`   | Hiermee geeft u de referentietekst van het `bookmark` element op. | Vereist. |

**Voorbeeld**

Als voorbeeld wilt u mogelijk de tijdsver offset van elk bloemwoord als volgt weten

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        We are selling <bookmark mark='flower_1'/>roses and <bookmark mark='flower_2'/>daisies.
    </voice>
</speak>
```

### <a name="get-bookmark-using-speech-sdk"></a>Bladwijzers op halen met behulp van speech-SDK

U kunt zich abonneren op de `BookmarkReached` gebeurtenis in speech-SDK om de bladwijzer-offsets op te halen.

> [!NOTE]
> `BookmarkReached` gebeurtenis is alleen beschikbaar sinds Speech SDK versie 1.16.0.

`BookmarkReached` gebeurtenissen worden verhoogd wanneer de audiogegevens van de uitvoer beschikbaar komen, wat sneller is dan het afspelen naar een uitvoerapparaat.

* `AudioOffset` rapporteert de verstreken tijd van de uitvoeraudio tussen het begin van de synthese en het bladwijzerelement. Dit wordt gemeten in eenheden van honderd nanoseconden (HNS) met 10.000 HNS gelijk aan 1 milliseconde.
* `Text` is de referentietekst van het bladwijzerelement. Dit is de tekenreeks die u in het kenmerk `mark` in stelt.

# <a name="c"></a>[C#](#tab/csharp)

Zie voor meer <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkreached" target="_blank"> `BookmarkReached` </a>informatie.

```csharp
synthesizer.BookmarkReached += (s, e) =>
{
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    Console.WriteLine($"Bookmark reached. Audio offset: " +
        $"{e.AudioOffset / 10000}ms, bookmark text: {e.Text}.");
};
```

In het bovenstaande voorbeeld van SSML wordt de gebeurtenis twee keer geactiveerd `BookmarkReached` en wordt de console-uitvoer
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="c"></a>[C++](#tab/cpp)

Zie voor meer <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#bookmarkreached" target="_blank"> `BookmarkReached` </a>informatie.

```cpp
synthesizer->BookmarkReached += [](const SpeechSynthesisBookmarkEventArgs& e)
{
    cout << "Bookmark reached. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "bookmark text: " << e.Text << "." << endl;
};
```

In het bovenstaande voorbeeld van SSML wordt de gebeurtenis twee keer `BookmarkReached` geactiveerd en wordt de console-uitvoer
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="java"></a>[Java](#tab/java)

Zie voor meer <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkReached#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_BookmarkReached" target="_blank"> `BookmarkReached` </a>informatie.

```java
synthesizer.BookmarkReached.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Bookmark reached. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("bookmark text: " + e.getText() + ".");
});
```

In het bovenstaande voorbeeld van SSML wordt de gebeurtenis twee keer geactiveerd `BookmarkReached` en wordt de console-uitvoer
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="python"></a>[Python](#tab/python)

Zie voor meer <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#bookmark-reached" target="_blank"> `bookmark_reached` </a>informatie.

```python
# The unit of evt.audio_offset is tick (1 tick = 100 nanoseconds), divide it by 10,000 to convert to milliseconds.
speech_synthesizer.bookmark_reached.connect(lambda evt: print(
    "Bookmark reached: {}, audio offset: {}ms, bookmark text: {}.".format(evt, evt.audio_offset / 10000, evt.text)))
```

In het bovenstaande voorbeeld van SSML wordt de gebeurtenis twee keer geactiveerd `bookmark_reached` en wordt de console-uitvoer
```text
Bookmark reached, audio offset: 825ms, bookmark text: flower_1.
Bookmark reached, audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Zie voor meer <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer#bookmarkReached" target="_blank"> `bookmarkReached` </a>informatie.

```javascript
synthesizer.bookmarkReached = function (s, e) {
    window.console.log("(Bookmark reached), Audio offset: " + e.audioOffset / 10000 + "ms, bookmark text: " + e.text);
}
```

In het bovenstaande voorbeeld van SSML wordt de gebeurtenis twee keer geactiveerd `bookmarkReached` en wordt de console-uitvoer
```text
(Bookmark reached), Audio offset: 825ms, bookmark text: flower_1.
(Bookmark reached), Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Zie voor meer <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#addbookmarkreachedeventhandler" target="_blank"> `addBookmarkReachedEventHandler` </a>informatie.

```objectivec
[synthesizer addBookmarkReachedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisBookmarkEventArgs *eventArgs) {
    // The unit of AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to converted to milliseconds.
    NSLog(@"Bookmark reached. Audio offset: %fms, bookmark text: %@.", eventArgs.audioOffset/10000., eventArgs.text);
}];
```

In het bovenstaande voorbeeld van SSML wordt de gebeurtenis twee keer geactiveerd `BookmarkReached` en wordt de console-uitvoer
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="swift"></a>[Swift](#tab/swift)

Zie voor meer <a href="/objectivec/cognitive-services/speech/spxspeechsynthesizer" target="_blank"> `addBookmarkReachedEventHandler` </a>informatie.

---

## <a name="next-steps"></a>Volgende stappen

* [Taalondersteuning: stemmen, talen, talen](language-support.md)
