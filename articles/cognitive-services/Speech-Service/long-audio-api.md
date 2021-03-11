---
title: Lange audio-API-spraak service
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe de lange audio-API is ontworpen voor asynchrone synthese van lange-vorm tekst naar spraak.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: 65c0d80394317c2b2bfbf621d3cc2ad0c2e3448a
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618403"
---
# <a name="long-audio-api"></a>Lange audio-API

De API voor lange audio is ontworpen voor asynchrone synthese van tekst in lange vorm naar spraak (bijvoorbeeld: audio boeken, nieuws artikelen en documenten). Deze API retourneert geen getakte audio in realtime. in plaats daarvan is het raadzaam om de reactie (s) te controleren en de uitvoer (en) te gebruiken zodra deze vanuit de service beschikbaar worden gesteld. In tegens telling tot de tekst-naar-spraak-API die wordt gebruikt door de spraak-SDK, kan de lange audio-API meer dan tien minuten gesynthesizerde audio maken, waardoor het ideaal is voor uitgevers en audio-inhouds platforms om lange audio-inhoud zoals audio boeken in een batch te maken.

Aanvullende voor delen van de API voor lange audio:

* De gesynthesizerde spraak die door de service wordt geretourneerd, maakt gebruik van de beste Neural stemmen.
* Het is niet nodig om een spraak eindpunt te implementeren omdat er stemmen zijn die geen real-time batch-modus hebben.

> [!NOTE]
> De lange audio-API ondersteunt nu zowel [open bare Neural stemmen](./language-support.md#neural-voices) als [aangepaste Neural stemmen](./how-to-custom-voice.md#custom-neural-voices).

## <a name="workflow"></a>Werkstroom

Wanneer u de Long audio-API gebruikt, moet u doorgaans een tekst bestand of bestanden verzenden die moeten worden gesynthesizerd, de status controleren en vervolgens de status geslaagd hebben, kunt u de audio-uitvoer downloaden.

Dit diagram bevat een overzicht op hoog niveau van de werk stroom.

![Lang audio API-werk stroom diagram](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Inhoud voorbereiden voor synthese

Wanneer u het tekst bestand voorbereidt, moet u het volgende controleren:

* Is tekst zonder opmaak (. txt) of SSML tekst (. txt)
* Is gecodeerd als [UTF-8 met een byte order Mark (bom)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Is één bestand, geen zip
* Bevat meer dan 400 tekens voor onbewerkte tekst of 400 [factureer bare tekens](./text-to-speech.md#pricing-note) voor SSML tekst en minder dan 10.000 alinea's
  * Voor tekst zonder opmaak wordt elke alinea gescheiden door op **Enter/Return** - [invoer voor beeld tekst zonder opmaak](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) weer te geven
  * Voor SSML tekst wordt elk SSML-stuk beschouwd als een alinea. SSML stuks moeten worden gescheiden door verschillende alinea's- [voor beeld van SSML-tekst invoer](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) weer geven

## <a name="sample-code"></a>Voorbeeldcode
De rest van deze pagina is gericht op python, maar voorbeeld code voor de API voor lange audio is beschikbaar op GitHub voor de volgende programmeer talen:

* [Voorbeeld code: python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Voorbeeld code: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Voorbeeld code: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)

## <a name="python-example"></a>Python-voor beeld

Deze sectie bevat python-voor beelden die het basis gebruik van de Long audio-API weer geven. Maak een nieuw Python-project met uw favoriete IDE of editor. Kopieer vervolgens dit code fragment naar een bestand met de naam `long_audio_synthesis_client.py` .

```python
import json
import ntpath
import requests
```

Deze bibliotheken worden gebruikt voor het maken van de HTTP-aanvraag en het aanroepen van de lange audio synthese van tekst naar spraak REST API.

### <a name="get-a-list-of-supported-voices"></a>Een lijst met ondersteunde stemmen ophalen

Als u een lijst met ondersteunde stemmen wilt ophalen, verzendt u een GET-aanvraag naar `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices` .


Met deze code kunt u een volledige lijst met stemmen voor een specifieke regio/eind punt die u kunt gebruiken ophalen.
```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

Vervang de volgende waarden:

* Vervang door `<your_key>` uw abonnements sleutel voor uw spraak service. Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang door `<region>` de regio waarin uw spraak bron is gemaakt (bijvoorbeeld: `eastus` of `westus` ). Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).

U ziet een uitvoer die er als volgt uitziet:

```console
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

Als **Properties. publicAvailable** is ingesteld op **True**, is de stem een open bare Neural-stem. Anders is het een aangepaste Neural-stem.

### <a name="convert-text-to-speech"></a>Tekst naar spraak converteren

Bereid een invoer tekst bestand voor in tekst zonder opmaak of SSML tekst en voeg de volgende code toe aan `long_audio_synthesis_client.py` :

> [!NOTE]
> `concatenateResult` is een optionele para meter. Als deze para meter niet is ingesteld, worden de audio-uitvoer per alinea gegenereerd. U kunt de audio waarden ook samen voegen in 1 uitvoer door de para meter in te stellen. 
> `outputFormat` is ook optioneel. De audio-uitvoer wordt standaard ingesteld op RIFF-16khz-16-mono-PCM. Zie [audio-uitvoer indelingen](#audio-output-formats)voor meer informatie over ondersteunde indelingen voor audio-uitvoer.

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

Vervang de volgende waarden:

* Vervang door `<your_key>` uw abonnements sleutel voor uw spraak service. Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang door `<region>` de regio waarin uw spraak bron is gemaakt (bijvoorbeeld: `eastus` of `westus` ). Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang door `<input_file_path>` het pad naar het tekst bestand dat u voor tekst naar spraak hebt voor bereid.
* Vervang door `<locale>` de gewenste land instelling voor uitvoer. Zie [taal ondersteuning](language-support.md#neural-voices)voor meer informatie.

Gebruik een van de stemmen die zijn geretourneerd door de vorige aanroep van het `/voices` eind punt.

* Als u een open bare Neural-stem gebruikt, vervangt u door `<voice_name>` de gewenste uitvoer Voice.
* Als u een aangepaste Neural-stem wilt gebruiken, vervangt u `voice_identities` de variabele door de volgende en vervangt u door `<voice_id>` de `id` aangepaste Neural-stem.
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

U ziet een uitvoer die er als volgt uitziet:

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> Als u meer dan 1 invoer bestanden hebt, moet u meerdere aanvragen indienen. Er zijn enkele beperkingen die u moet kennen.
> * De client mag Maxi maal **5** aanvragen voor de server per seconde indienen voor elk account van een Azure-abonnement. Als deze de beperking overschrijdt, krijgt de client een fout code van 429 (te veel aanvragen). Verminder de aanvraag hoeveelheid per seconde.
> * De server mag Maxi maal **120** aanvragen voor elk account van een Azure-abonnement uitvoeren en in de wachtrij plaatsen. Als de limiet wordt overschreden, retourneert de server een fout code van 429 (te veel aanvragen). Een ogen blik geduld en het verzenden van een nieuwe aanvraag voor komen totdat sommige aanvragen zijn voltooid.

De URL in de uitvoer kan worden gebruikt om de aanvraag status op te halen.

### <a name="get-information-of-a-submitted-request"></a>Informatie over een ingediende aanvraag ophalen

Als u de status van een ingediende synthese aanvraag wilt ophalen, verzendt u een GET-aanvraag naar de URL die wordt geretourneerd door de vorige stap.
```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```
De uitvoer ziet er als volgt uit:
```console
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

Vanuit `status` eigenschap kunt u de status van deze aanvraag lezen. De aanvraag wordt gestart vanaf de `NotStarted` status en vervolgens gewijzigd in `Running` , en uiteindelijk `Succeeded` of `Failed` . U kunt een lus gebruiken om deze API te pollen totdat de status wordt gewijzigd `Succeeded` .

### <a name="download-audio-result"></a>Geluids resultaat downloaden

Zodra een synthese aanvraag is geslaagd, kunt u het audio resultaat downloaden door GET API aan te roepen `/files` .

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```
Vervang door `<request_id>` de id van de aanvraag die u wilt downloaden van het resultaat. U vindt deze in het antwoord van de vorige stap.

De uitvoer ziet er als volgt uit:
```console
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
De uitvoer bevat informatie over 2 bestanden. Het eerste met `"kind": "LongAudioSynthesisScript"` is het invoer script dat is verzonden. De andere met `"kind": "LongAudioSynthesisResult"` is het resultaat van deze aanvraag.
Het resultaat is een zip-bestand met de gegenereerde audio-uitvoer bestanden, samen met een kopie van de invoer tekst.

Beide bestanden kunnen worden gedownload van de URL in hun `links.contentUrl` eigenschap.

### <a name="get-all-synthesis-requests"></a>Alle synthese aanvragen ophalen

U kunt een lijst met alle ingediende aanvragen ophalen met de volgende code:

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

Uitvoer is als volgt:
```console
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

`values` de eigenschap bevat een lijst met synthese aanvragen. De lijst is gepagineerd, met een maximale pagina grootte van 100. Als er meer dan 100 aanvragen zijn, wordt er een `"@nextLink"` eigenschap beschikbaar om de volgende pagina van de gepagineerde lijst op te halen.

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

U kunt ook de pagina grootte aanpassen en het aantal overs Laan door de `skip` `top` para meter en in URL op te geven.

### <a name="remove-previous-requests"></a>Eerdere aanvragen verwijderen

De service bewaart Maxi maal **20.000** aanvragen voor elk account van een Azure-abonnement. Als uw aanvraag bedrag deze beperking overschrijdt, moet u de vorige aanvragen verwijderen voordat u er nieuwe maakt. Als u geen bestaande aanvragen verwijdert, ontvangt u een fout melding.

De volgende code laat zien hoe u een specifieke synthese aanvraag verwijdert.
```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

Als de aanvraag is verwijderd, is de antwoord status code HTTP 204 (geen inhoud).

```console
response.status_code: 204
```

> [!NOTE]
> Aanvragen met de status `NotStarted` of `Running` kunnen niet worden verwijderd of verwijderd.

De voltooide `long_audio_synthesis_client.py` is beschikbaar op [github](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="http-status-codes"></a>HTTP-statuscode

De volgende tabel bevat een overzicht van de HTTP-antwoord codes en berichten van de REST API.

| API | HTTP-statuscode | Beschrijving | Oplossing |
|-----|------------------|-------------|----------|
| Maken | 400 | De stem synthese is niet ingeschakeld in deze regio. | Wijzig de sleutel van het spraak abonnement met een ondersteunde regio. |
|        | 400 | Alleen het **standaard** spraak abonnement voor deze regio is geldig. | Wijzig de code van het spraak abonnement in de prijs categorie Standard. |
|        | 400 | Overschrijdt de limiet van 20.000 aanvragen voor het Azure-account. Verwijder enkele aanvragen voordat u nieuwe verzendt. | De server bewaart Maxi maal 20.000 aanvragen voor elk Azure-account. Enkele aanvragen verwijderen voordat nieuwe worden verzonden. |
|        | 400 | Dit model kan niet worden gebruikt in de spraak-synthese: {modelID}. | Controleer of de status van de {modelID} juist is. |
|        | 400 | De regio voor de aanvraag komt niet overeen met de regio voor het model: {modelID}. | Controleer of de regio van de {modelID} overeenkomt met de regio van de aanvraag. |
|        | 400 | De spraak-synthese ondersteunt alleen het tekst bestand in UTF-8-code ring met de byte volgorde markering. | Zorg ervoor dat de invoer bestanden zich in UTF-8-code ring bevinden met de markering byte volgorde. |
|        | 400 | Alleen geldige SSML-invoer waarden zijn toegestaan in de Voice Synthesis-aanvraag. | Controleer of de ingevoerde SSML-expressies juist zijn. |
|        | 400 | De spraak naam {Voice name} is niet gevonden in het invoer bestand. | De stem naam van de invoer SSML is niet uitgelijnd met de model-ID. |
|        | 400 | Het aantal alinea's in het invoer bestand moet kleiner zijn dan 10.000. | Zorg ervoor dat het aantal alinea's in het bestand kleiner is dan 10.000. |
|        | 400 | Het invoer bestand moet langer zijn dan 400 tekens. | Zorg ervoor dat uw invoer bestand langer is dan 400 tekens. |
|        | 404 | Het model dat is gedeclareerd in de definitie van de audio-synthese, is niet gevonden: {modelID}. | Controleer of {modelID} juist is. |
|        | 429 | De limiet voor actieve audio-synthese overschrijdt. Wacht tot sommige aanvragen zijn voltooid. | De server mag Maxi maal 120 aanvragen voor elk Azure-account uitvoeren en in de wachtrij plaatsen. Wacht tot er nieuwe aanvragen zijn verzonden totdat sommige aanvragen zijn voltooid. |
| Alles       | 429 | Er zijn te veel aanvragen. | De client mag Maxi maal 5 aanvragen voor de server per seconde indienen voor elk Azure-account. Verminder de aanvraag hoeveelheid per seconde. |
| Verwijderen    | 400 | De stem synthese taak is nog in gebruik. | U kunt alleen **voltooide** of **mislukte** aanvragen verwijderen. |
| GetByID   | 404 | De opgegeven entiteit is niet gevonden. | Controleer of de synthese-ID juist is. |

## <a name="regions-and-endpoints"></a>Regio's en eind punten

De lange audio-API is beschikbaar in meerdere regio's met unieke eind punten.

| Region | Eindpunt |
|--------|----------|
| VS - oost | `https://eastus.customvoice.api.speech.microsoft.com` |
| India - centraal | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Azië - zuidoost | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Verenigd Koninkrijk Zuid | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa -west | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Indelingen audio-uitvoer

Flexibele indelingen voor audio-uitvoer worden ondersteund. U kunt audio-uitvoer per alinea genereren of de audio-uitvoer in één uitvoer samen voegen door de para meter ' concatenateResult ' in te stellen. De volgende indelingen voor audio-uitvoer worden ondersteund door de lange audio-API:

> [!NOTE]
> De standaard audio-indeling is RIFF-16khz-16-mono-PCM.

* riff-8khz-16-mono-PCM
* riff-16khz-16-mono-PCM
* riff-24khz-16-mono-PCM
* riff-48khz-16-mono-PCM
* Audio-16khz-32kbitrate-mono-mp3
* Audio-16khz-64kbitrate-mono-mp3
* Audio-16khz-128kbitrate-mono-mp3
* Audio-24khz-48kbitrate-mono-mp3
* Audio-24khz-96kbitrate-mono-mp3
* Audio-24khz-160kbitrate-mono-mp3
