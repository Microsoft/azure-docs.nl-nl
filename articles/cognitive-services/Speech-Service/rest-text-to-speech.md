---
title: Naslaginformatie over de Text-to-Speech-API (REST) - Speech-service
titleSuffix: Azure Cognitive Services
description: Informatie over het gebruik van de tekst-naar-spraak-REST API. In dit artikel vindt u informatie over autorisatieopties, queryopties, het structureren van een aanvraag en het ontvangen van een antwoord.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.author: trbye
ms.custom: references_regions
ms.openlocfilehash: 0065b6f4a7039e2883bca6acd5cf659be7b71069
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717671"
---
# <a name="text-to-speech-rest-api"></a>REST API voor tekst-naar-spraak

Met de Speech-service kunt u tekst [](#get-a-list-of-voices) converteren naar [gesynthetiseerde](#convert-text-to-speech) spraak en een lijst met ondersteunde stemmen voor een regio op halen met behulp van een set REST API's. Elk beschikbaar eindpunt is gekoppeld aan een regio. Er is een abonnementssleutel vereist voor het eindpunt/de regio die u wilt gebruiken.

De tekst-naar-spraak-REST API ondersteuning voor neurale en standaardtekst-naar-spraak-stemmen, die elk een specifieke taal en dialect ondersteunen, geïdentificeerd op basis van landtalen.

* Zie taalondersteuning voor een volledige lijst [met stemmen.](language-support.md#text-to-speech)
* Zie regio's voor meer informatie over regionale [beschikbaarheid.](regions.md#text-to-speech)

> [!IMPORTANT]
> De kosten variëren voor standaard-, aangepaste en neurale stemmen. Zie Prijzen voor meer [informatie.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

Voordat u deze API gebruikt, moet u het volgende begrijpen:

* Voor de tekst-naar-spraak-REST API een autorisatieheader vereist. Dit betekent dat u een tokenuitwisseling moet voltooien om toegang te krijgen tot de service. Zie [Verificatie](#authentication) voor meer informatie.

> [!TIP]
> Zie [dit artikel voor](sovereign-clouds.md) Azure Government- en Azure China-eindpunten.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Een lijst met stemmen op halen

Met `voices/list` het eindpunt kunt u een volledige lijst met stemmen voor een specifieke regio/eindpunt krijgen.

### <a name="regions-and-endpoints"></a>Regio's en eindpunten

| Region | Eindpunt |
|--------|----------|
| Australië - oost | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Brazilië - zuid | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Canada - midden | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Central US | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Azië - oost | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - oost | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - oost 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Frankrijk - centraal | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| India - centraal | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Japan - oost | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Korea - centraal | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - noord-centraal | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa - noord | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Zuid-Afrika - noord | `https://southafricanorth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - zuid-centraal | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Azië - zuidoost | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Verenigd Koninkrijk Zuid | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - west-centraal | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| Europa -west | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - west | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| VS - west 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

> [!TIP]
> [Stemmen in de preview-versie](language-support.md#neural-voices-in-preview) zijn alleen beschikbaar in deze drie regio's: VS - oost, Europa - west Azië - zuidoost.

### <a name="request-headers"></a>Aanvraagheaders

Deze tabel bevat de vereiste en optionele headers voor tekst-naar-spraak-aanvragen.

| Header | Description | Vereist /optioneel |
|--------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | De abonnementssleutel voor de Speech-service. | Deze header of `Authorization` is vereist. |
| `Authorization` | Een autorisatie-token voorafgegaan door het woord `Bearer` . Zie [Verificatie](#authentication) voor meer informatie. | Deze header of `Ocp-Apim-Subscription-Key` is vereist. |



### <a name="request-body"></a>Aanvraagbody

Er is geen body vereist voor `GET` aanvragen naar dit eindpunt.

### <a name="sample-request"></a>Voorbeeldaanvraag

Voor deze aanvraag is alleen een autorisatieheader vereist.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
```

### <a name="sample-response"></a>Voorbeeldantwoord

Dit antwoord is afgekapt om de structuur van een antwoord te illustreren.

> [!NOTE]
> Spraakbeschikbaarheid varieert per regio/eindpunt.

```json
[

    {
    "Name": "Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)",
    "DisplayName": "Aria",
    "LocalName": "Aria",
    "ShortName": "en-US-AriaNeural",
    "Gender": "Female",
    "Locale": "en-US",
    "StyleList": [
      "chat",
      "customerservice",
      "newscast-casual",
      "newscast-formal",
      "cheerful",
      "empathetic"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "GA"
  },

  ...

     {
    "Name": "Microsoft Server Speech Text to Speech Voice (ga-IE, OrlaNeural)",
    "DisplayName": "Orla",
    "LocalName": "Orla",
    "ShortName": "ga-IE-OrlaNeural",
    "Gender": "Female",
    "Locale": "ga-IE",
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

  ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (zh-CN, YunxiNeural)",
    "DisplayName": "Yunxi",
    "LocalName": "云希",
    "ShortName": "zh-CN-YunxiNeural",
    "Gender": "Male",
    "Locale": "zh-CN",
    "StyleList": [
      "Calm",
      "Fearful",
      "Cheerful",
      "Disgruntled",
      "Serious",
      "Angry",
      "Sad",
      "Depressed",
      "Embarrassed"
    ],
    "SampleRateHertz": "24000",
    "VoiceType": "Neural",
    "Status": "Preview"
  },

    ...

   {
    "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
    "DisplayName": "Hoda",
    "LocalName": "هدى",
    "ShortName": "ar-EG-Hoda",
    "Gender": "Female",
    "Locale": "ar-EG",
    "SampleRateHertz": "16000",
    "VoiceType": "Standard",
    "Status": "GA"
  },

...

]
```

### <a name="http-status-codes"></a>HTTP-statuscode

De HTTP-statuscode voor elk antwoord geeft aan dat de actie is geslaagd of dat er veelvoorkomende fouten zijn.

| HTTP-statuscode | Beschrijving | Mogelijke reden |
|------------------|-------------|-----------------|
| 200 | OK | De aanvraag is geslaagd. |
| 400 | Onjuiste aanvraag | Een vereiste parameter ontbreekt, is leeg of null. Of de waarde die wordt doorgegeven aan een vereiste of optionele parameter is ongeldig. Een veelvoorkomende probleem is een header die te lang is. |
| 401 | Niet geautoriseerd | De aanvraag is niet geautoriseerd. Controleer of uw abonnementssleutel of token geldig en in de juiste regio is. |
| 429 | Te veel aanvragen | U hebt het quotum of het aantal toegestane aanvragen voor uw abonnement overschreden. |
| 502 | Slechte gateway    | Netwerk- of serverprobleem. Kan ook duiden op ongeldige headers. |


## <a name="convert-text-to-speech"></a>Tekst naar spraak converteren

Met het eindpunt kunt u tekst naar spraak converteren met `v1` behulp van Speech Synthesis [Markup Language (SSML).](speech-synthesis-markup.md)

### <a name="regions-and-endpoints"></a>Regio's en eindpunten

Deze regio's worden ondersteund voor tekst-naar-spraak met behulp van REST API. Zorg ervoor dat u het eindpunt selecteert dat overeenkomt met uw abonnementsregio.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Aanvraagheaders

Deze tabel bevat de vereiste en optionele headers voor tekst-naar-spraak-aanvragen.

| Header | Description | Vereist /optioneel |
|--------|-------------|---------------------|
| `Authorization` | Een autorisatie-token voorafgegaan door het woord `Bearer` . Zie [Verificatie](#authentication) voor meer informatie. | Vereist |
| `Content-Type` | Hiermee geeft u het inhoudstype voor de opgegeven tekst. Geaccepteerde waarde: `application/ssml+xml` . | Vereist |
| `X-Microsoft-OutputFormat` | Hiermee geeft u de audio-uitvoerindeling. Zie audio-uitvoer voor een volledige lijst met [geaccepteerde waarden.](#audio-outputs) | Vereist |
| `User-Agent` | De toepassingsnaam. De opgegeven waarde moet minder dan 255 tekens lang zijn. | Vereist |

### <a name="audio-outputs"></a>Audio-uitvoer

Dit is een lijst met ondersteunde audio-indelingen die in elke aanvraag als header worden `X-Microsoft-OutputFormat` verzonden. Elk bevat een bitrate en coderingstype. De Speech-service ondersteunt audio-uitvoer van 24 kHz, 16 kHz en 8 kHz.

```output
raw-16khz-16bit-mono-pcm            riff-16khz-16bit-mono-pcm
raw-24khz-16bit-mono-pcm            riff-24khz-16bit-mono-pcm
raw-48khz-16bit-mono-pcm            riff-48khz-16bit-mono-pcm
raw-8khz-8bit-mono-mulaw            riff-8khz-8bit-mono-mulaw
raw-8khz-8bit-mono-alaw             riff-8khz-8bit-mono-alaw
audio-16khz-32kbitrate-mono-mp3     audio-16khz-64kbitrate-mono-mp3
audio-16khz-128kbitrate-mono-mp3    audio-24khz-48kbitrate-mono-mp3
audio-24khz-96kbitrate-mono-mp3     audio-24khz-160kbitrate-mono-mp3
audio-48khz-96kbitrate-mono-mp3     audio-48khz-192kbitrate-mono-mp3
raw-16khz-16bit-mono-truesilk       raw-24khz-16bit-mono-truesilk
webm-16khz-16bit-mono-opus          webm-24khz-16bit-mono-opus
ogg-16khz-16bit-mono-opus           ogg-24khz-16bit-mono-opus
ogg-48khz-16bit-mono-opus
```

> [!NOTE]
> Als de geselecteerde spraak- en uitvoerindeling verschillende bitsnelheden hebben, wordt de audio indien nodig opnieuw gesampeld.
> ogg-24 khz-16bits-mono-opus kan worden gedecodeerd met [opus codec](https://opus-codec.org/downloads/)

### <a name="request-body"></a>Aanvraagbody

De body van elke `POST` aanvraag wordt verzonden als [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). Met SSML kunt u de stem en taal kiezen van de gesynthetiseerde spraak die wordt geretourneerd door de text-to-speech-service. Zie Taalondersteuning voor een volledige lijst met [ondersteunde stemmen.](language-support.md#text-to-speech)

> [!NOTE]
> Als u een aangepaste stem gebruikt, kan de body van een aanvraag worden verzonden als tekst zonder tekst (ASCII of UTF-8).

### <a name="sample-request"></a>Voorbeeldaanvraag

Deze HTTP-aanvraag maakt gebruik van SSML om de stem en taal op te geven. Als de lengte van de body lang is en de resulterende audio langer is dan 10 minuten, wordt deze afgekapt tot 10 minuten. Met andere woorden, de audiolengte mag niet langer zijn dan 10 minuten.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-AriaNeural'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-status-codes"></a>HTTP-statuscode

De HTTP-statuscode voor elk antwoord geeft aan dat het is gelukt of dat er veelvoorkomende fouten zijn.

| HTTP-statuscode | Beschrijving | Mogelijke reden |
|------------------|-------------|-----------------|
| 200 | OK | De aanvraag is geslaagd; de antwoord-body is een audiobestand. |
| 400 | Onjuiste aanvraag | Een vereiste parameter ontbreekt, is leeg of null. Of de waarde die wordt doorgegeven aan een vereiste of optionele parameter is ongeldig. Een veelvoorkomende probleem is een header die te lang is. |
| 401 | Niet geautoriseerd | De aanvraag is niet geautoriseerd. Controleer of uw abonnementssleutel of token geldig en in de juiste regio is. |
| 415 | Niet-ondersteund mediatype | Het is mogelijk dat het verkeerde `Content-Type` is opgegeven. `Content-Type` moet worden ingesteld op `application/ssml+xml` . |
| 429 | Te veel aanvragen | U hebt het quotum of het aantal toegestane aanvragen voor uw abonnement overschreden. |
| 502 | Slechte gateway    | Netwerk- of serverprobleem. Kan ook duiden op ongeldige headers. |

Als de HTTP-status `200 OK` is, bevat de body van het antwoord een audiobestand in de aangevraagde indeling. Dit bestand kan worden afgespeeld wanneer het wordt overgedragen, opgeslagen in een buffer of wordt opgeslagen in een bestand.

## <a name="next-steps"></a>Volgende stappen

- [Een gratis Azure-account maken](https://azure.microsoft.com/free/cognitive-services/)
- [Asynchrone synthese voor audio in lange vorm](./long-audio-api.md)
- [Aan de slag met Custom Voice](how-to-custom-voice.md)
