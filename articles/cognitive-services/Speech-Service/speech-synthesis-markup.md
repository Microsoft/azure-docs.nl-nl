---
title: Speech-synthese Markup Language (SSML)-Speech Service
titleSuffix: Azure Cognitive Services
description: De opmaak taal voor spraak synthese gebruiken om uitspraak en prosody in tekst naar spraak te beheren.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f2d3b6b40a7b258da682d36260f6292d10635095
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102498716"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>De synthese verbeteren met Markup Language voor spraak synthese (SSML)

SSML (Speech synthese Markup Language) is een op XML gebaseerde Markup-taal waarmee ontwikkel aars kunnen opgeven hoe invoer tekst wordt omgezet in gesynthesizerde spraak met behulp van de tekst-naar-spraak-service. Vergeleken met onbewerkte tekst kunnen ontwikkel aars met SSML de hoogte, de uitspraak, de spreek snelheid, het volume en meer van de tekst-naar-spraak-uitvoer nauw keuriger instellen. Normale Lees tekens, zoals het onderbreken na een periode, of het gebruik van de juiste intonation wanneer een zin eindigt met een vraag teken, worden automatisch afgehandeld.

De implementatie van de speech-service van SSML is gebaseerd op de [opmaak taal versie 1,0](https://www.w3.org/TR/speech-synthesis)van de spraak-synthese van World Wide Web Consortium.

> [!IMPORTANT]
> Chinese, Japanse en Koreaanse tekens tellen als twee tekens voor facturering. Zie [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)voor meer informatie.

## <a name="standard-neural-and-custom-voices"></a>Standaard, Neural en aangepaste stemmen

Kies uit standaard-en Neural stemmen of maak uw eigen aangepaste spraak die uniek is voor uw product of merk. 75 + standaard stemmen zijn verkrijgbaar in meer dan 45 talen en land instellingen en vijf Neural stemmen zijn beschikbaar in vier talen en land instellingen. Zie [taal ondersteuning](language-support.md)voor een volledige lijst met ondersteunde talen, land instellingen en stemmen (Neural en Standard).

Zie [tekst-naar-spraak-overzicht](text-to-speech.md)voor meer informatie over standaard-, Neural-en aangepaste stemmen.


> [!NOTE]
> U kunt stemmen in verschillende stijlen horen en lees voorbeeld tekst met behulp van [de pagina Text to speech](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features).


## <a name="special-characters"></a>Speciale tekens

Houd er bij het gebruik van SSML rekening mee dat speciale tekens, zoals aanhalings tekens, apostrofs en haakjes, moeten worden voorafgegaan. Zie [Extensible Markup Language (XML) 1,0: bijlage D](https://www.w3.org/TR/xml/#sec-entexpand)voor meer informatie.

## <a name="supported-ssml-elements"></a>Ondersteunde SSML-elementen

Elk SSML-document wordt gemaakt met SSML-elementen (of-Tags). Deze elementen worden gebruikt voor het aanpassen van de pitch, prosody, volume en meer. In de volgende secties wordt uitgelegd hoe elk-element wordt gebruikt en wanneer een element vereist of optioneel is.  

> [!IMPORTANT]
> Vergeet geen dubbele aanhalings tekens rond kenmerk waarden te gebruiken. Standaarden voor juist opgemaakte, geldige XML vereist dat kenmerk waarden tussen dubbele aanhalings tekens worden geplaatst. `<prosody volume="90">`Het is bijvoorbeeld een goed gevormd, geldig element, maar dit is `<prosody volume=90>` niet het geval. SSML kunnen kenmerk waarden die geen aanhalings tekens zijn niet herkennen.

## <a name="create-an-ssml-document"></a>Een SSML-document maken

`speak` is het hoofd element en is **vereist** voor alle SSML-documenten. Het `speak` -element bevat belang rijke informatie, zoals versie, taal en de definitie van de aantekeningen woordenlijst.

**Syntaxis**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist/optioneel |
|-----------|-------------|---------------------|
| `version` | Hiermee wordt de versie van de SSML-specificatie aangegeven die wordt gebruikt om de document markering te interpreteren. De huidige versie is 1,0. | Vereist |
| `xml:lang` | Hiermee geeft u de taal van het hoofd document. De waarde kan een kleine letter, een taal code van twee letters (bijvoorbeeld `en` ) of de taal code en het hoofd land/de regio (bijvoorbeeld) bevatten `en-US` . | Vereist |
| `xmlns` | Hiermee geeft u de URI op voor het document dat de aantekeningen woordenlijst (de element typen en kenmerk namen) van het SSML-document definieert. De huidige URI is http://www.w3.org/2001/10/synthesis . | Vereist |

## <a name="choose-a-voice-for-text-to-speech"></a>Kies een stem voor tekst naar spraak

Het `voice` element is vereist. Dit wordt gebruikt om de stem op te geven die wordt gebruikt voor tekst naar spraak.

**Syntaxis**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist/optioneel |
|-----------|-------------|---------------------|
| `name` | Hiermee wordt de stem geïdentificeerd die wordt gebruikt voor de tekst-naar-spraak-uitvoer. Zie [taal ondersteuning](language-support.md#text-to-speech)voor een volledige lijst met ondersteunde stemmen. | Vereist |

**Voorbeeld**

> [!NOTE]
> In dit voor beeld wordt de `en-US-AriaRUS` stem gebruikt. Zie [taal ondersteuning](language-support.md#text-to-speech)voor een volledige lijst met ondersteunde stemmen.

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Meerdere stemmen gebruiken

Binnen het `speak` -element kunt u meerdere stemmen opgeven voor tekst-naar-spraak-uitvoer. Deze stemmen kunnen zich in verschillende talen bevindt. Voor elke stem moet de tekst worden ingepakt in een- `voice` element. 

**Kenmerken**

| Kenmerk | Beschrijving | Vereist/optioneel |
|-----------|-------------|---------------------|
| `name` | Hiermee wordt de stem geïdentificeerd die wordt gebruikt voor de tekst-naar-spraak-uitvoer. Zie [taal ondersteuning](language-support.md#text-to-speech)voor een volledige lijst met ondersteunde stemmen. | Vereist |

> [!IMPORTANT]
> Meerdere stemmen zijn incompatibel met de functie voor woord grens. De functie woord grens moet worden uitgeschakeld om meerdere stemmen te kunnen gebruiken.

### <a name="disable-word-boundary"></a>Woord grens uitschakelen

Afhankelijk van de taal van de Speech SDK, stelt u de `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` eigenschap in `false` op een exemplaar van het `SpeechConfig` object.

# <a name="c"></a>[C#](#tab/csharp)

Zie <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty" target="_blank"> `SetProperty` </a>voor meer informatie.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Zie <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` </a>voor meer informatie.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Zie <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` </a>voor meer informatie.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Zie <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` </a>voor meer informatie.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Zie <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#setproperty-string--string-" target="_blank"> `setProperty` </a>voor meer informatie.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Zie <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>voor meer informatie.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Zie <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>voor meer informatie.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Spreek stijlen aanpassen

> [!IMPORTANT]
> De aanpassing van gesp roken stijlen werkt alleen met Neural stemmen.

Standaard wordt tekst door de service tekst naar spraak gesynthesizerd met behulp van een neutrale spreek stijl voor de standaard-en Neural stemmen. Met Neural stemmen kunt u de spreek stijl aanpassen aan een snelle andere emoties, zoals cheerfulness, empathie en rustig, of de stem optimaliseren voor verschillende scenario's zoals klanten service, newscasting en Voice Assistant, met behulp van het- `mstts:express-as` element. Dit is een optioneel element dat uniek is voor de spraak service.

Op dit moment worden de volgende Neural stemmen ondersteund:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural` Evaluatie
* `zh-CN-XiaohanNeural` Evaluatie
* `zh-CN-XiaomoNeural` Evaluatie
* `zh-CN-XiaoxuanNeural` Evaluatie
* `zh-CN-XiaoruiNeural` Evaluatie

De intensiteit van de stijl van de spraak kan verder worden gewijzigd om beter te voldoen aan uw use-case. U kunt een sterker of zachtere stijl opgeven `styledegree` om de spraak herkenning duidelijker of gematigd te maken. 

Op dit moment worden de volgende Neural stemmen ondersteund:
* `zh-CN-XiaoxiaoNeural`

Naast het aanpassen van de spraak stijlen en de stijl graad kunt u ook de `role` para meter aanpassen zodat de stem een andere leeftijd en geslacht krijgt. Een mannelijk stem kan bijvoorbeeld de Toon hoogte verhogen en de intonation wijzigen om een vrouwelijke stem te imiteren.

Momenteel worden aanpassingen voor rollen afspelen ondersteund voor deze Neural stemmen:
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

De bovenstaande wijzigingen worden toegepast op het niveau van de zin, en stijlen en rollen worden afgespeeld per stem. Als een stijl of rol-afspelen niet wordt ondersteund, retourneert de service spraak op de standaard neutrale manier. U kunt zien welke stijlen en rollen tijdens het afspelen worden ondersteund voor elke stem via de [Voice List API](rest-text-to-speech.md#get-a-list-of-voices) of via het platform voor het maken van de [audio-inhoud](https://aka.ms/audiocontentcreation) zonder code.

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
> Op het moment `styledegree` ondersteunt alleen zh-cn-XiaoxiaoNeural. `role` ondersteunt alleen zh-CN-XiaomoNeural en zh-CN-XiaoxuanNeural.

**Kenmerken**

| Kenmerk | Beschrijving | Vereist/optioneel |
|-----------|-------------|---------------------|
| `style` | Geeft de spreek stijl aan. Op dit moment zijn gesp roken stijlen specifiek voor spraak. | Vereist bij het aanpassen van de spreek stijl voor een Neural-stem. Als u gebruikt `mstts:express-as` , moet de stijl worden gegeven. Als er een ongeldige waarde wordt gegeven, wordt dit element genegeerd. |
| `styledegree` | Geeft de intensiteit van de stijl van de spraak aan. **Geaccepteerde waarden**: 0,01 tot en met 2. De standaard waarde is 1, wat de vooraf gedefinieerde stijl intensiteit aangeeft. De minimale eenheid is 0,01, wat leidt tot een enigszins tendens van de doel stijl. Een waarde van 2 resulteert in een verdubbeling van de standaard stijl intensiteit.  | Optioneel (op het moment `styledegree` ondersteunt alleen zh-cn-XiaoxiaoNeural.)|
| `role` | Hiermee geeft u de functie voor spreken-afspelen op. De stem zal fungeren als een andere leeftijd en geslacht.  | Optioneel (op het moment `role` ondersteunt alleen zh-cn-XiaomoNeural en zh-cn-XiaoxuanNeural.)|

Gebruik deze tabel om te bepalen welke spraak stijlen worden ondersteund voor elke Neural-stem.

| Spraak                   | Stijl                     | Beschrijving                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Een formele, vertrouwende en bindende Toon voor nieuws levering |
|                         | `style="newscast-casual"` | Een veelzijdige en informe Toon voor algemene nieuws levering        |
|                         | `style="customerservice"` | Een beschrijvende en handige Toon voor klant ondersteuning  |
|                         | `style="chat"`            | Een informe en een ongeforceerde Toon                         |
|                         | `style="cheerful"`        | Een positieve en fijne Toon                         |
|                         | `style="empathetic"`      | Een idee van caring en inzicht               |
| `en-US-JennyNeural`     | `style="customerservice"` | Een beschrijvende en handige Toon voor klant ondersteuning  |
|                         | `style="chat"`            | Een informe en een ongeforceerde Toon                         |
|                         | `style="assistant"`       | Een warme en beperkte Toon voor digitale assistenten    |
|                         | `style="newscast"`        | Een veelzijdige en informe Toon voor algemene nieuws levering   |
| `en-US-GuyNeural`       | `style="newscast"`        | Een formele en professionele Toon voor gesp roken nieuws |
| `pt-BR-FranciscaNeural` | `style="calm"`            | Een fraaie, verzamelde en opgebouwde stand voor het praten. Tint, Pitch, prosody is veel meer uniform vergeleken met andere soorten spraak.                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Een formele en professionele Toon voor gesp roken nieuws |
|                         | `style="customerservice"` | Een beschrijvende en handige Toon voor klant ondersteuning  |
|                         | `style="assistant"`       | Een warme en beperkte Toon voor digitale assistenten    |
|                         | `style="chat"`            | Een ongepaarde Toon voor Chit-Chat           |
|                         | `style="calm"`            | Een fraaie, verzamelde en opgebouwde stand voor het praten. Tint, Pitch, prosody is veel meer uniform vergeleken met andere soorten spraak.                                |
|                         | `style="cheerful"`        | Een opvallende en enthousiaste Toon, met hogere Pitch-en gespreks energie                         |
|                         | `style="sad"`             | Hiermee wordt een Sorrowful-Toon met hogere hoogte, minder intensiteit en lagere vocaal-energie. Algemene indica toren van deze Emotion worden whimpers of huilend tijdens de spraak.            |
|                         | `style="angry"`           | Hiermee wordt een boos en geergerte Toon, met een lagere hoogte, hogere intensiteit en hogere stem-energie. De spreker heeft de status Irate, gereageerd en is niet in orde.       |
|                         | `style="fearful"`         | Een Scared-en zenuw Toon, met een hogere hoogte, hogere vocaal-energie en een snellere snelheid. De spreker heeft de status tenseness en Uneasiness.                          |
|                         | `style="disgruntled"`     | Een disdainful-en-klagende Toon. De spraak van deze Emotion toont het aantal plezier en de bewaarde.              |
|                         | `style="serious"`         | Hiermee wordt een strikte en een inschakeling van een Toon. De spreker klinkt vaak stijfer en veel minder geforceerd met uitgebracht.          |
|                         | `style="affectionate"`    | Een warme en affectionate Toon, met hogere Toon-en gesprek energie. De spreker heeft de status van het aantrekken van de aandacht van de listener. De ' persoonlijkheid ' van de spreker is vaak endearing.          |     
|                         | `style="gentle"`          | Hiermee wordt een milde, versterkte en prettige Toon, met lagere Toon hoogte en vocaal-energie         |   
|                         | `style="lyrical"`         | Drukt op emoties op een melodic-en Sentimental manier         |   
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Een beschrijvende en handige Toon voor klant ondersteuning  | 
| `zh-CN-YunyeNeural`     | `style="calm"`            | Een fraaie, verzamelde en opgebouwde stand voor het praten. Tint, Pitch, prosody is veel meer uniform vergeleken met andere soorten spraak.    | 
|                         | `style="cheerful"`        | Een opvallende en enthousiaste Toon, met hogere Pitch-en gespreks energie                         |
|                         | `style="sad"`             | Hiermee wordt een Sorrowful-Toon met hogere hoogte, minder intensiteit en lagere vocaal-energie. Algemene indica toren van deze Emotion worden whimpers of huilend tijdens de spraak.            |
|                         | `style="angry"`           | Hiermee wordt een boos en geergerte Toon, met een lagere hoogte, hogere intensiteit en hogere stem-energie. De spreker heeft de status Irate, gereageerd en is niet in orde.       |
|                         | `style="fearful"`         | Een Scared-en zenuw Toon, met een hogere hoogte, hogere vocaal-energie en een snellere snelheid. De spreker heeft de status tenseness en Uneasiness.                          |
|                         | `style="disgruntled"`     | Een disdainful-en-klagende Toon. De spraak van deze Emotion toont het aantal plezier en de bewaarde.              |
|                         | `style="serious"`         | Hiermee wordt een strikte en een inschakeling van een Toon. De spreker klinkt vaak stijfer en veel minder geforceerd met uitgebracht.          |
| `zh-CN-YunxiNeural`     | `style="cheerful"`        | Een opvallende en enthousiaste Toon, met hogere Pitch-en gespreks energie                         |
|                         | `style="sad"`             | Hiermee wordt een Sorrowful-Toon met hogere hoogte, minder intensiteit en lagere vocaal-energie. Algemene indica toren van deze Emotion worden whimpers of huilend tijdens de spraak.            |
|                         | `style="angry"`           | Hiermee wordt een boos en geergerte Toon, met een lagere hoogte, hogere intensiteit en hogere stem-energie. De spreker heeft de status Irate, gereageerd en is niet in orde.       |
|                         | `style="fearful"`         | Een Scared-en zenuw Toon, met een hogere hoogte, hogere vocaal-energie en een snellere snelheid. De spreker heeft de status tenseness en Uneasiness.                          |
|                         | `style="disgruntled"`     | Een disdainful-en-klagende Toon. De spraak van deze Emotion toont het aantal plezier en de bewaarde.              |
|                         | `style="serious"`         | Hiermee wordt een strikte en een inschakeling van een Toon. De spreker klinkt vaak stijfer en veel minder geforceerd met uitgebracht.    |
|                         | `style="depressed"`       | Een melancholic-en respondent-Toon met een lagere Toon hoogte en energie    |
|                         | `style="embarrassed"`     | Een onzekere en cloudhoster Toon wanneer de spreker niet vertrouwd is   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | Een opvallende en enthousiaste Toon, met hogere Pitch-en gespreks energie                         |
|                         | `style="sad"`             | Hiermee wordt een Sorrowful-Toon met hogere hoogte, minder intensiteit en lagere vocaal-energie. Algemene indica toren van deze Emotion worden whimpers of huilend tijdens de spraak.            |
|                         | `style="angry"`           | Hiermee wordt een boos en geergerte Toon, met een lagere hoogte, hogere intensiteit en hogere stem-energie. De spreker heeft de status Irate, gereageerd en is niet in orde.       |
|                         | `style="fearful"`         | Een Scared-en zenuw Toon, met een hogere hoogte, hogere vocaal-energie en een snellere snelheid. De spreker heeft de status tenseness en Uneasiness.                          |
|                         | `style="disgruntled"`     | Een disdainful-en-klagende Toon. De spraak van deze Emotion toont het aantal plezier en de bewaarde.              |
|                         | `style="serious"`         | Hiermee wordt een strikte en een inschakeling van een Toon. De spreker klinkt vaak stijfer en veel minder geforceerd met uitgebracht.    |
|                         | `style="embarrassed"`     | Een onzekere en cloudhoster Toon wanneer de spreker niet vertrouwd is   |
|                         | `style="affectionate"`    | Een warme en affectionate Toon, met hogere Toon-en gesprek energie. De spreker heeft de status van het aantrekken van de aandacht van de listener. De ' persoonlijkheid ' van de spreker is vaak endearing.          |     
|                         | `style="gentle"`          | Hiermee wordt een milde, versterkte en prettige Toon, met lagere Toon hoogte en vocaal-energie         |   
| `zh-CN-XiaomoNeural`    | `style="cheerful"`        | Een opvallende en enthousiaste Toon, met hogere Pitch-en gespreks energie                         |
|                         | `style="angry"`           | Hiermee wordt een boos en geergerte Toon, met een lagere hoogte, hogere intensiteit en hogere stem-energie. De spreker heeft de status Irate, gereageerd en is niet in orde.       |
|                         | `style="fearful"`         | Een Scared-en zenuw Toon, met een hogere hoogte, hogere vocaal-energie en een snellere snelheid. De spreker heeft de status tenseness en Uneasiness.                          |
|                         | `style="disgruntled"`     | Een disdainful-en-klagende Toon. De spraak van deze Emotion toont het aantal plezier en de bewaarde.              |
|                         | `style="serious"`         | Hiermee wordt een strikte en een inschakeling van een Toon. De spreker klinkt vaak stijfer en veel minder geforceerd met uitgebracht.    |
|                         | `style="depressed"`       | Een melancholic-en respondent-Toon met een lagere Toon hoogte en energie    |
|                         | `style="gentle"`          | Hiermee wordt een milde, versterkte en prettige Toon, met lagere Toon hoogte en vocaal-energie         |  
| `zh-CN-XiaoxuanNeural`  | `style="cheerful"`        | Een opvallende en enthousiaste Toon, met hogere Pitch-en gespreks energie                         |
|                         | `style="angry"`           | Hiermee wordt een boos en geergerte Toon, met een lagere hoogte, hogere intensiteit en hogere stem-energie. De spreker heeft de status Irate, gereageerd en is niet in orde.       |
|                         | `style="fearful"`         | Een Scared-en zenuw Toon, met een hogere hoogte, hogere vocaal-energie en een snellere snelheid. De spreker heeft de status tenseness en Uneasiness.                          |
|                         | `style="disgruntled"`     | Een disdainful-en-klagende Toon. De spraak van deze Emotion toont het aantal plezier en de bewaarde.              |
|                         | `style="serious"`         | Hiermee wordt een strikte en een inschakeling van een Toon. De spreker klinkt vaak stijfer en veel minder geforceerd met uitgebracht.    |
|                         | `style="depressed"`       | Een melancholic-en respondent-Toon met een lagere Toon hoogte en energie    |
|                         | `style="gentle"`          | Hiermee wordt een milde, versterkte en prettige Toon, met lagere Toon hoogte en vocaal-energie         |   
| `zh-CN-XiaoruiNeural`    | `style="sad"`             | Hiermee wordt een Sorrowful-Toon met hogere hoogte, minder intensiteit en lagere vocaal-energie. Algemene indica toren van deze Emotion worden whimpers of huilend tijdens de spraak.            |
|                         | `style="angry"`           | Hiermee wordt een boos en geergerte Toon, met een lagere hoogte, hogere intensiteit en hogere stem-energie. De spreker heeft de status Irate, gereageerd en is niet in orde.       |
|                         | `style="fearful"`         | Een Scared-en zenuw Toon, met een hogere hoogte, hogere vocaal-energie en een snellere snelheid. De spreker heeft de status tenseness en Uneasiness.                          |

Gebruik deze tabel om te bepalen welke rollen worden ondersteund voor elke Neural-stem.

| Spraak                   | Rol                       | Beschrijving                                                 |
|-------------------------|----------------------------|-------------------------------------------------------------|
| `zh-CN-XiaomoNeural`    | `role="YoungAdultFemale"`  | De stem heeft een jonge volwassene vrouw.                 |
|                         | `role="OlderAdultMale"`    | De stem heeft een ouder volwassen mannelijk.                   |
|                         | `role="Girl"`              | De stem heeft een meisje.                               |
|                         | `role="Boy"`               | De stem die wordt nagebootst naar een jongen.                                |
| `zh-CN-XiaoxuanNeural`  | `role="YoungAdultFemale"`  | De stem heeft een jonge volwassene vrouw.                 |
|                         | `role="OlderAdultFemale"`  | De stem heeft een oudere volwassene vrouw.                 |
|                         | `role="OlderAdultMale"`    | De stem heeft een ouder volwassen mannelijk.                   |

**Voorbeeld**

Dit SSML-fragment laat zien hoe het `<mstts:express-as>` element wordt gebruikt voor het wijzigen van de spreek stijl in `cheerful` .

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

Dit SSML-fragment laat zien hoe het `styledegree` kenmerk wordt gebruikt om de intensiteit van de spreek stijl voor XiaoxiaoNeural te wijzigen.
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

Dit SSML-fragment laat zien hoe het `role` kenmerk wordt gebruikt voor het wijzigen van de rol-Play voor XiaomoNeural.
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

Gebruik het `break` element om pauzes (of onderbrekingen) tussen woorden in te voegen of onderbrekingen die automatisch worden toegevoegd door de tekst-naar-spraak-service te voor komen.

> [!NOTE]
> Gebruik dit element om het standaard gedrag van text-to-speech (TTS) voor een woord of woord groep te overschrijven als de gesynthesizerde spraak voor het woord of de woord groep onnatuurlijk klinkt. Ingesteld `strength` op `none` om te voor komen dat een Prosodic-verbreekt, die automatisch wordt ingevoegd door de service tekst naar spraak.

**Syntaxis**

```xml
<break strength="string" />
<break time="string" />
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist/optioneel |
|-----------|-------------|---------------------|
| `strength` | Hiermee geeft u de relatieve duur van een onderbreking op met een van de volgende waarden:<ul><li>geen</li><li>x-zwak</li><li>Schakel</li><li>gemiddeld (standaard)</li><li>strakk</li><li>x-Strong</li></ul> | Optioneel |
| `time` | Hiermee geeft u de absolute duur van een onderbreking in seconden of milliseconden, deze waarde moet worden ingesteld op minder dan 5000ms. Voor beelden van geldige waarden zijn `2s` en `500ms` | Optioneel |

| Hoger                      | Beschrijving |
|-------------------------------|-------------|
| Geen, of als er geen waarde wordt gegeven | 0 MS        |
| x-zwak                        | 250 MS      |
| Schakel                          | 500 ms      |
| gemiddeld                        | 750 ms      |
| strakk                        | 1000 MS     |
| x-Strong                      | 1250 MS     |

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>Stilte toevoegen

Gebruik het `mstts:silence` element om pauzes in te voegen voor of na tekst of tussen de twee aangrenzende zinnen. 

> [!NOTE]
>Het verschil tussen `mstts:silence` en `break` is dat `break` kan worden toegevoegd aan elke plaats in de tekst, maar stilte werkt alleen aan het begin of einde van invoer tekst, of op de grens van 2 aangrenzende zinnen.  


**Syntaxis**

```xml
<mstts:silence  type="string"  value="string"/>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist/optioneel |
|-----------|-------------|---------------------|
| `type` | Hiermee geeft u de locatie van stilte toevoegen: <ul><li>Regel afstand: aan het begin van de tekst </li><li>Staart: aan het einde van de tekst </li><li>Sentenceboundary: tussen aangrenzende zinnen </li></ul> | Vereist |
| `Value` | Hiermee geeft u de absolute duur van een onderbreking in seconden of milliseconden, deze waarde moet worden ingesteld op minder dan 5000ms. Voor beelden van geldige waarden zijn `2s` en `500ms` | Vereist |

**Voor beeld** In dit voor beeld `mtts:silence` wordt gebruikt om 200 MS-stilte tussen twee zinnen toe te voegen.
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

`p` en `s` elementen worden gebruikt om respectievelijk alinea's en zinnen aan te duiden. Als deze elementen ontbreken, bepaalt de tekst naar spraak-service automatisch de structuur van het SSML-document.

Het `p` element kan tekst en de volgende elementen bevatten: `audio` , `break` , `phoneme` , `prosody` , `say-as` , `sub` , en `mstts:express-as` `s` .

Het `s` element kan tekst en de volgende elementen bevatten: `audio` , `break` , `phoneme` , `prosody` , `say-as` `mstts:express-as` `sub` , en.

**Syntaxis**

```XML
<p></p>
<s></s>
```

**Voorbeeld**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
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

## <a name="use-phonemes-to-improve-pronunciation"></a>Fonemen gebruiken om de uitspraak te verbeteren

Het `ph` element wordt gebruikt voor fonetische uitspraak in SSML-documenten. Het `ph` element kan alleen tekst bevatten, geen andere elementen. Bied altijd lees bare spraak als terugval.

Fonetische alfabetten bestaan uit telefoons, die bestaan uit letters, cijfers of tekens, soms in combi natie. Elke telefoon beschrijft een uniek geluid van spraak. Dit is in tegens telling tot het Latijnse alfabet, waarbij een wille keurige letter meerdere gesp roken geluiden kan vertegenwoordigen. Houd rekening met de verschillende uitspraak van de letter "c" in de woorden "snoep" en "Stop" of de verschillende uitspraak van de letter combinaties "th" in de woorden "ding" en "die".

> [!NOTE]
> Het fonemen-label wordt op het moment niet ondersteund voor deze vijf stemmen (et-EE-AnuNeural, NH-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural en MT-MT-GarceNeural).

**Syntaxis**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist/optioneel |
|-----------|-------------|---------------------|
| `alphabet` | Hiermee geeft u het fonetische alfabet op dat moet worden gebruikt wanneer de uitspraak van de teken reeks in het kenmerk wordt gesynthesizerd `ph` . De teken reeks die het alfabet opgeeft, moet worden opgegeven in kleine letters. Hier volgen de mogelijke alfabetten die u kunt opgeven.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Internationaal Fonetisch alfabet</a></li><li>`sapi`Het &ndash; [fonetische alfabet](speech-ssml-phonetic-sets.md) van de speech-service</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">Universele telefoonset</a></li></ul><br>Het alfabet is alleen van toepassing op de `phoneme` in het element.. | Optioneel |
| `ph` | Een teken reeks met telefoons waarmee de uitspraak van het woord in het element wordt opgegeven `phoneme` . Als de opgegeven teken reeks niet-herkende telefoons bevat, weigert de TTS-Service (tekst naar spraak) het hele SSML-document en wordt er geen van de spraak uitvoer opgegeven in het document. | Vereist als u fonemen gebruikt. |

**Voorbeelden**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Aangepaste Lexicon gebruiken om de uitspraak te verbeteren

Soms kan de tekst naar spraak-service een woord niet nauw keurig uitspreken. Bijvoorbeeld de naam van een bedrijf of een medische term. Ontwikkel aars kunnen bepalen hoe afzonderlijke entiteiten in SSML worden gelezen met behulp van de `phoneme` `sub` Tags en. Als u echter wilt definiëren hoe meerdere entiteiten worden gelezen, kunt u een aangepast Lexicon maken met behulp van de `lexicon` tag.

> [!NOTE]
> Aangepaste Lexicon ondersteunt momenteel UTF-8-code ring. 

> [!NOTE]
> Er wordt op dit moment geen aangepaste Lexicon ondersteund voor deze vijf stemmen (et-EE-AnuNeural, NH-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural en MT-MT-GarceNeural).


**Syntaxis**

```XML
<lexicon uri="string"/>
```

**Kenmerken**

| Kenmerk | Beschrijving                               | Vereist/optioneel |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Het adres van het externe gebruik-document. | Vereist.           |

**Gebruik**

Als u wilt definiëren hoe meerdere entiteiten worden gelezen, kunt u een aangepast Lexicon maken, dat is opgeslagen als een. XML-of. gebruik-bestand. Hier volgt een voor beeld van een XML-bestand.

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

Het `lexicon` element bevat ten minste één `lexeme` element. Elk `lexeme` element bevat ten minste één `grapheme` element en een of meer `grapheme` `alias` elementen, en `phoneme` . Het `grapheme` element bevat tekst met een beschrijving van de <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">orthography </a>. De `alias` elementen worden gebruikt om de uitspraak van een acroniem of een kortere term aan te geven. Het `phoneme` element biedt tekst die beschrijft hoe de `lexeme` wordt uitgesp roken.

Het is belang rijk te weten dat u de uitspraak van een woord groep niet rechtstreeks kunt instellen met behulp van het aangepaste lexicon. Als u de uitspraak voor een acroniem of een afkorting wilt instellen, geeft u eerst een `alias` op en koppelt u deze aan `phoneme` die `alias` . Bijvoorbeeld:

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

U kunt ook uw verwachte `alias` acroniem of afgekort term direct opgeven. Bijvoorbeeld:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme> 
    <alias>Scotland Media Wave</alias> 
  </lexeme>
```

> [!IMPORTANT]
> Het `phoneme` element mag geen spaties bevatten wanneer IPA wordt gebruikt.

Zie [gebruik-versie 1,0 (conuitspraak Lexicon Specification) (Engelstalig)](https://www.w3.org/TR/pronunciation-lexicon/)voor meer informatie over het aangepaste Lexicon bestand.

Publiceer vervolgens uw aangepaste Lexicon bestand. Er zijn geen beperkingen voor het opslaan van dit bestand, maar we raden u aan [Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-portal.md)te gebruiken.

Nadat u uw aangepaste Lexicon hebt gepubliceerd, kunt u ernaar verwijzen vanuit uw SSML.

> [!NOTE]
> Het `lexicon` element moet zich in het-element bevindt `voice` .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

Als u dit aangepaste Lexicon gebruikt, wordt "BTW" als "op de manier" gelezen. ' Benigni ' wordt gelezen met de meegeleverde IPA ' bɛ ˈ ni ː nji '.  

**Beperkingen**
- Bestands grootte: de maximale limiet voor de grootte van het aangepaste Lexicon bestand is 100KB. als dit niet het geval is, mislukt de synthese aanvraag.
- Vernieuwen van Lexicon cache: het aangepaste Lexicon wordt in de cache opgeslagen als Key op de TTS-service wanneer het voor het eerst wordt geladen. Een Lexicon met dezelfde URI kan niet binnen 15 minuten opnieuw worden geladen, dus de aangepaste Lexicon wijziging moet worden gewacht om te worden doorgevoerd.

**Fonetische sets van spraak service**

In het bovenstaande voor beeld gebruiken we het internationale fonetische alfabet, ook wel bekend als de IPA-telefoonset. Ontwikkel aars suggereren dat ze de IPA gebruiken, omdat dit de internationale standaard is. Voor sommige IPA-tekens hebben ze de versie ' premated ' en ' ontgebouwd ' wanneer ze worden weer gegeven met Unicode. Aangepaste Lexicon biedt alleen ondersteuning voor de ontgebouwde Unicode-tekens.

Als u overweegt dat de IPA niet eenvoudig te onthouden is, definieert de speech-service een fonetische set voor zeven talen (,,,,, `en-US` `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` en `zh-TW` ).

U kunt de `sapi` as Vale voor het kenmerk gebruiken `alphabet` met aangepaste lexicons, zoals hieronder wordt geïllustreerd:

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

Zie voor meer informatie over het fonetische alfabet van de gedetailleerde spraak service de [fonetische set van de spraakherkennings service](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Prosody aanpassen

Het `prosody` element wordt gebruikt om wijzigingen op te geven in de hoogte, de contour, het bereik, de frequentie, de duur en het volume voor de tekst-naar-spraak-uitvoer. Het `prosody` element kan tekst en de volgende elementen bevatten: `audio` , `break` , `p` , `phoneme` , `prosody` , `say-as` , en `sub` `s` .

Omdat Prosodic kenmerk waarden kunnen variëren per breed bereik, interpreteert de spraak herkenner de toegewezen waarden als een suggestie van wat de werkelijke Prosodic-waarden van de geselecteerde stem moeten zijn. De service tekst naar spraak beperkt of vervangt waarden die niet worden ondersteund. Voor beelden van niet-ondersteunde waarden zijn een Toon hoogte van 1 MHz of een volume van 120.

**Syntaxis**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist/optioneel |
|-----------|-------------|---------------------|
| `pitch` | Geeft de hoogte van de basis lijn voor de tekst aan. U kunt de Toon hoogte uitdrukken als:<ul><li>Een absolute waarde, uitgedrukt als een getal gevolgd door ' Hz ' (Hertz). Bijvoorbeeld `<prosody pitch="600Hz">some text</prosody>`.</li><li>Een relatieve waarde, uitgedrukt als een getal voorafgegaan door ' + ' of '-', gevolgd door ' Hz ' of ' St ', waarmee een hoeveelheid wordt opgegeven om de Toon hoogte te wijzigen. Bijvoorbeeld: `<prosody pitch="+80Hz">some text</prosody>` of `<prosody pitch="-2st">some text</prosody>` . De ' St ' geeft aan dat de wijzigings eenheid semitone is. Dit is de helft van een Toon (een halve stap) op de standaard diatonic schaal.</li><li>Een constante waarde:<ul><li>x-laag</li><li>gebrek</li><li>gemiddeld</li><li>hoog</li><li>x-hoog</li><li>standaardinstelling</li></ul></li></ul> | Optioneel |
| `contour` |De werklast beschrijving ondersteunt nu zowel Neural als standaard stemmen. Contour vertegenwoordigt wijzigingen in de Toon hoogte. Deze wijzigingen worden weer gegeven als een matrix met doelen op de opgegeven tijd posities in de spraak uitvoer. Elk doel wordt gedefinieerd door sets van parameter paren. Bijvoorbeeld: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>De eerste waarde in elke set para meters geeft de locatie van de pitch wijziging aan als een percentage van de duur van de tekst. Met de tweede waarde geeft u de hoeveelheid op waarmee de Toon hoogte moet worden verhoogd of verlaagd, met behulp van een relatieve waarde of een opsommings waarde voor Pitch (Zie `pitch` ). | Optioneel |
| `range` | Een waarde die het bereik van de hoogte van de tekst aangeeft. U kunt snel `range` gebruikmaken van dezelfde absolute waarden, relatieve waarden of opsommings waarden die worden gebruikt om te beschrijven `pitch` . | Optioneel |
| `rate` | Geeft het spreek tempo van de tekst aan. U kunt uitdrukken `rate` als:<ul><li>Een relatieve waarde, uitgedrukt als een getal dat fungeert als een vermenigvuldiger van de standaard instelling. Een waarde van *1* resulteert bijvoorbeeld in geen wijziging in het aantal. Een waarde van *0,5* resulteert in een helft van het aantal. Een waarde van *3* resulteert in een toename van het aantal.</li><li>Een constante waarde:<ul><li>x-langzaam</li><li>trage</li><li>gemiddeld</li><li>snel</li><li>x-snel</li><li>standaardinstelling</li></ul></li></ul> | Optioneel |
| `duration` | De tijds duur die moet verstrijken terwijl de TTS-Service (spraak-synthese) de tekst leest, in seconden of in milliseconden. Bijvoorbeeld, *2s* of *1800ms*. Duur ondersteunt alleen standaard stemmen.| Optioneel |
| `volume` | Hiermee wordt het volume niveau van de spreek spraak aangegeven. U kunt het volume uitdrukken als:<ul><li>Een absolute waarde, uitgedrukt als een getal in het bereik 0,0 tot en met 100,0, van *zacht* naar *hard*. Bijvoorbeeld 75. De standaard waarde is 100,0.</li><li>Een relatieve waarde, uitgedrukt als een getal voorafgegaan door ' + ' of '-', waarmee een hoeveelheid wordt opgegeven om het volume te wijzigen. Bijvoorbeeld + 10 of-5,5.</li><li>Een constante waarde:<ul><li>achtergrond</li><li>x-zacht</li><li>Proof</li><li>gemiddeld</li><li>hard</li><li>x-hardop</li><li>standaardinstelling</li></ul></li></ul> | Optioneel |

### <a name="change-speaking-rate"></a>Spreek snelheid wijzigen

Het spreek tempo kan worden toegepast op Neural stemmen en standaard stemmen op het niveau van het woord of de zin. 

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

Volume wijzigingen kunnen worden toegepast op standaard stemmen op het niveau van het woord of de zin. Terwijl volume wijzigingen alleen kunnen worden toegepast op Neural stemmen op het niveau van de zin.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Toon hoogte wijzigen

Wijzigingen in de hoogte kunnen worden toegepast op standaard stemmen op het niveau van het woord of de zin. Dat wijzigingen in de hoogte alleen kunnen worden toegepast op Neural stemmen op het niveau van de zin.

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Contour hoogte wijzigen

> [!IMPORTANT]
> De hoogte van de contour wijzigingen wordt nu ondersteund met Neural stemmen.

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
## <a name="say-as-element"></a>het element dict-as

`say-as` is een optioneel element dat het inhouds type (zoals getal of datum) van de tekst van het element aangeeft. Dit biedt richt lijnen voor de engine voor spraak synthese over hoe u de tekst uitspreekt.

**Syntaxis**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist/optioneel |
|-----------|-------------|---------------------|
| `interpret-as` | Hiermee wordt het inhouds type van de tekst van het element aangegeven. Zie de onderstaande tabel voor een lijst met typen. | Vereist |
| `format` | Biedt aanvullende informatie over de exacte opmaak van de tekst van het element voor inhouds typen die mogelijk niet-eenduidige indelingen hebben. SSML definieert indelingen voor inhouds typen die deze gebruiken (Zie de tabel hieronder). | Optioneel |
| `detail` | Hiermee wordt het detail niveau aangegeven dat moet worden gesp roken. Dit kenmerk kan bijvoorbeeld vragen of de spraakherkennings engine Lees tekens uitspreekt. Er zijn geen standaard waarden gedefinieerd voor `detail` . | Optioneel |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Hieronder vindt u de ondersteunde inhouds typen voor de `interpret-as` `format` kenmerken en. Neem het `format` kenmerk alleen op als de waarde `interpret-as` is ingesteld op datum en tijd.

| interpreteren als | indeling | Interpretatie |
|--------------|--------|----------------|
| `address` | | De tekst wordt gesp roken als een adres. De engine voor spraak synthese uitspreekt:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Als "Ik ben bij 150the rechtbank voor het noordoosten van Redmond Washington" |
| `cardinal`, `number` | | De tekst wordt gesp roken als een hoofd getal. De engine voor spraak synthese uitspreekt:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Als "er zijn drie alternatieven." |
| `characters`, `spell-out` | | De tekst wordt gesp roken als afzonderlijke letters (gespeld). De engine voor spraak synthese uitspreekt:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Als T E S T. |
| `date` | DMY, MDJ, YMD, JDM, YM, my, MD, DM, d, m, y | De tekst wordt als een datum gesp roken. Het `format` kenmerk geeft de notatie van de datum aan (*d = dag, m = maand en y = jaar*). De engine voor spraak synthese uitspreekt:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />"Vandaag is oktober nineteenth 2016." |
| `digits`, `number_digit` | | De tekst wordt gesp roken als een reeks afzonderlijke cijfers. De engine voor spraak synthese uitspreekt:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Als ' 1 2 3 4 5 6 7 8 9 '. |
| `fraction` | | De tekst wordt gesp roken als een breuk getal. De engine voor spraak synthese uitspreekt:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Als "drie achtstes van een inch" |
| `ordinal` | | De tekst wordt gesp roken als een rang nummer. De engine voor spraak synthese uitspreekt:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Als ' Selecteer de derde optie '. |
| `telephone` | | De tekst wordt gesp roken als een telefoon nummer. Het `format` kenmerk mag cijfers bevatten die een land code vertegenwoordigen. Bijvoorbeeld ' 1 ' voor de Verenigde Staten of ' 39 ' voor Italië. De engine voor spraak synthese kan deze informatie gebruiken om de uitspraak van een telefoon nummer te hand leiding. Het telefoon nummer kan ook de land code bevatten, en als dat zo is, heeft dat prioriteit boven de land code in de `format` . De engine voor spraak synthese uitspreekt:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Als ' mijn nummer is gebieds code 8 8 8 5 5 5 1 2 1 2 '. |
| `time` | hms12, hms24 | De tekst wordt als een tijd gesp roken. Het `format` kenmerk geeft aan of de tijd wordt opgegeven met een 12-uurs klok (hms12) of een 24-uurs klok (hms24). Gebruik een dubbele punt als scheidings waarde voor uren, minuten en seconden. Hier volgen enkele voor beelden van geldige tijd: 12:35, 1:14:32, 08:15 en 02:50:45. De engine voor spraak synthese uitspreekt:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Als "de trein maakt op vier A M." |

**Gebruik**

Het `say-as` element mag alleen tekst bevatten.

**Voorbeeld**

De engine voor spraak synthese spreekt het volgende voor beeld uit als ' uw eerste aanvraag is voor één kamer op nineteenth oktober 20 10 met vroege aankomst om 12 35 uur. '
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Opgenomen audio toevoegen

`audio` is een optioneel element waarmee u MP3-audio kunt invoegen in een SSML-document. De hoofd tekst van het audio-element kan onbewerkte tekst of SSML markeringen bevatten die worden gesp roken als het audio bestand niet beschikbaar is of niet kan worden afgespeeld. Daarnaast kan het `audio` element tekst en de volgende elementen bevatten: `audio` ,, `break` `p` ,,,, en `s` `phoneme` `prosody` `say-as` `sub` .

Audio die is opgenomen in het SSML-document moet voldoen aan deze vereisten:

* De MP3 moet worden gehost op een HTTPS-eind punt dat toegankelijk is via internet. HTTPS is vereist en het domein dat als host fungeert voor het MP3-bestand moet een geldig, vertrouwd TLS/SSL-certificaat bevatten.
* MP3 moet een geldig MP3-bestand (MPEG v2) zijn.
* De bitsnelheid moet 48 kbps zijn.
* Het sample frequentie moet 16.000 Hz zijn.
* De gecombineerde totale tijd voor alle tekst-en audio bestanden in één antwoord mag niet groter zijn dan 90 (90) seconden.
* De MP3 mag geen klantspecifieke of andere gevoelige informatie bevatten.

**Syntaxis**

```xml
<audio src="string"/></audio>
```

**Kenmerken**

| Kenmerk | Beschrijving                                   | Vereist/optioneel                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Hiermee geeft u de locatie/URL van het audio bestand op. | Vereist als u het audio-element in uw SSML-document gebruikt. |

**Voorbeeld**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
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

## <a name="add-background-audio"></a>Achtergrond geluid toevoegen

Met het `mstts:backgroundaudio` element kunt u achtergrond geluid toevoegen aan uw SSML-documenten (of een audio bestand combi neren met tekst-naar-spraak). Met `mstts:backgroundaudio` kunt u een audio bestand op de achtergrond laten vervagen aan het begin van tekst-naar-spraak en uitfaden aan het einde van tekst-naar-spraak.

Als de achtergrond audio kleiner is dan de tekst-naar-spraak of de uitfaden, wordt er een lus weer gegeven. Als de tekst-naar-spraak langer is, wordt deze gestopt wanneer het uitfadepen is voltooid.

Er is slechts één achtergrond geluids bestand toegestaan per SSML-document. U kunt echter `audio` Tags in het- `voice` element bijvoegen om extra audio aan uw SSML-document toevoegen.

**Syntaxis**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Kenmerken**

| Kenmerk | Beschrijving | Vereist/optioneel |
|-----------|-------------|---------------------|
| `src` | Hiermee geeft u de locatie/URL van het audio bestand op de achtergrond. | Vereist als u achtergrond geluid in uw SSML-document gebruikt. |
| `volume` | Hiermee geeft u het volume van het audio bestand op de achtergrond. **Geaccepteerde waarden**: `0` tot `100` inclusief. De standaardwaarde is `1`. | Optioneel |
| `fadein` | Hiermee geeft u de duur van de achtergrond audio "infaden" als milliseconden. De standaard waarde is `0` , die gelijk is aan niet vervagen in. **Geaccepteerde waarden**: `0` tot `10000` inclusief.  | Optioneel |
| `fadeout` | Hiermee geeft u de duur van de achtergrond audio vervagen in milliseconden. De standaard waarde is `0` , die gelijk is aan geen uitfaden. **Geaccepteerde waarden**: `0` tot `10000` inclusief.  | Optioneel |

**Voorbeeld**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Volgende stappen

* [Taal ondersteuning: stemmen, land instellingen, talen](language-support.md)
