---
title: Lange audio-API (preview)-spraak service
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
ms.openlocfilehash: 255cfe11f8601abc89a1d96f702f453c2af1ccbd
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533057"
---
# <a name="long-audio-api-preview"></a>Lange audio-API (preview-versie)

De API voor lange audio is ontworpen voor asynchrone synthese van tekst in lange vorm naar spraak (bijvoorbeeld: audio boeken, nieuws artikelen en documenten). Deze API retourneert geen getakte audio in realtime. in plaats daarvan is het raadzaam om de reactie (s) te controleren en de uitvoer (en) te gebruiken zodra deze vanuit de service beschikbaar worden gesteld. In tegens telling tot de tekst-naar-spraak-API die wordt gebruikt door de spraak-SDK, kan de lange audio-API meer dan tien minuten gesynthesizerde audio maken, waardoor deze ideaal is voor uitgevers en platforms voor audio-inhoud.

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
> [!NOTE]
> Voor Chinees (vasteland), Chinees (Hong Kong SAR), Chinees (Taiwan), Japans en Koreaans wordt één woord als twee tekens beschouwd. 

## <a name="python-example"></a>Python-voor beeld

Deze sectie bevat python-voor beelden die het basis gebruik van de Long audio-API weer geven. Maak een nieuw Python-project met uw favoriete IDE of editor. Kopieer vervolgens dit code fragment naar een bestand met de naam `voice_synthesis_client.py` .

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

Deze bibliotheken worden gebruikt voor het parseren van argumenten, het samen stellen van de HTTP-aanvraag en het aanroepen van de tekst-naar-spraak-audio REST API.

### <a name="get-a-list-of-supported-voices"></a>Een lijst met ondersteunde stemmen ophalen

Met deze code kunt u een volledige lijst met stemmen voor een specifieke regio/eind punt die u kunt gebruiken ophalen. Voeg de code toe aan `voice_synthesis_client.py` :

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

Voer het script uit met behulp van de opdracht `python voice_synthesis_client.py --voices -key <your_key> -region <region>` en vervang de volgende waarden:

* Vervang door `<your_key>` uw abonnements sleutel voor uw spraak service. Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang door `<region>` de regio waarin uw spraak bron is gemaakt (bijvoorbeeld: `eastus` of `westus` ). Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).

U ziet een uitvoer die er als volgt uitziet:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

Als de para meter **PublicVoice** is ingesteld op **True**, is de stem open bare Neural-stem. Als dat niet het geval is, is de aangepaste Neural-stem.

### <a name="convert-text-to-speech"></a>Tekst naar spraak converteren

Bereid een invoer tekst bestand voor in tekst zonder opmaak of SSML tekst en voeg de volgende code toe aan `voice_synthesis_client.py` :

> [!NOTE]
> ' concatenateResult ' is een optionele para meter. Als deze para meter niet is ingesteld, worden de audio-uitvoer per alinea gegenereerd. U kunt de audio waarden ook samen voegen in 1 uitvoer door de para meter in te stellen. De audio-uitvoer wordt standaard ingesteld op RIFF-16khz-16-mono-PCM. Zie [audio-uitvoer indelingen](#audio-output-formats)voor meer informatie over ondersteunde audio-uitvoer.

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

Voer het script uit met behulp van de opdracht `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` en vervang de volgende waarden:

* Vervang door `<your_key>` uw abonnements sleutel voor uw spraak service. Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang door `<region>` de regio waarin uw spraak bron is gemaakt (bijvoorbeeld: `eastus` of `westus` ). Deze informatie is beschikbaar op het tabblad **overzicht** voor uw resource in de [Azure Portal](https://aka.ms/azureportal).
* Vervang door `<input>` het pad naar het tekst bestand dat u voor tekst naar spraak hebt voor bereid.
* Vervang door `<locale>` de gewenste land instelling voor uitvoer. Zie [taal ondersteuning](language-support.md#neural-voices)voor meer informatie.
* Vervang door `<voice_guid>` de gewenste uitvoer Voice. Gebruik een van de stemmen die zijn geretourneerd door de vorige aanroep van het `/voicesynthesis/voices` eind punt.

U ziet een uitvoer die er als volgt uitziet:

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

Het resultaat bevat de invoer tekst en de audio-uitvoer bestanden die door de service worden gegenereerd. U kunt deze bestanden downloaden in een zip-bestand.

> [!NOTE]
> Als u meer dan 1 invoer bestanden hebt, moet u meerdere aanvragen indienen. Er zijn enkele beperkingen die u moet kennen. 
> * De client mag Maxi maal **5** aanvragen voor de server per seconde indienen voor elk account van een Azure-abonnement. Als deze de beperking overschrijdt, krijgt de client een fout code van 429 (te veel aanvragen). Verminder de aanvraag hoeveelheid per seconde
> * De server mag Maxi maal **120** aanvragen voor elk account van een Azure-abonnement uitvoeren en in de wachtrij plaatsen. Als de limiet wordt overschreden, retourneert de server een fout code van 429 (te veel aanvragen). Een ogen blik geduld en het verzenden van een nieuwe aanvraag voor komen totdat sommige aanvragen zijn voltooid

### <a name="remove-previous-requests"></a>Eerdere aanvragen verwijderen

De service bewaart Maxi maal **20.000** aanvragen voor elk account van een Azure-abonnement. Als uw aanvraag bedrag deze beperking overschrijdt, moet u de vorige aanvragen verwijderen voordat u er nieuwe maakt. Als u geen bestaande aanvragen verwijdert, ontvangt u een fout melding.

Voeg de volgende code toe aan `voice_synthesis_client.py`:

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

Voer uit `python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` om een lijst op te halen met de synthese aanvragen die u hebt gedaan. U ziet een uitvoer als volgt:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Als u een aanvraag wilt verwijderen, voert u `python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` uit en vervangt u `<synthesis_id>` deze door een aanvraag-id-waarde die is geretourneerd door de vorige aanvraag.

> [!NOTE]
> Aanvragen met de status Running/Waiting kunnen niet worden verwijderd of verwijderd.

De voltooide `voice_synthesis_client.py` is beschikbaar op [github](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

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

| Regio | Eindpunt |
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

## <a name="sample-code"></a>Voorbeeldcode
Voorbeeld code voor lange audio-API is beschikbaar op GitHub.

* [Voorbeeld code: python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Voorbeeld code: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Voorbeeld code: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)