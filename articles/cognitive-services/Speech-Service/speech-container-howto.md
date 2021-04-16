---
title: Docker-containers voor de Speech Service-API's installeren en uitvoeren
titleSuffix: Azure Cognitive Services
description: Gebruik de Docker-containers voor de Speech-service om spraakherkenning, transcriptie, generatie en meer on-premises uit te voeren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
ms.custom: cog-serv-seo-aug-2020
keywords: on-premises, Docker, container
ms.openlocfilehash: cb99dc3c5e16ee117df46d7fda0caab9c57f0853
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388088"
---
# <a name="install-and-run-docker-containers-for-the-speech-service-apis"></a>Docker-containers voor de Speech Service-API's installeren en uitvoeren 

Met containers kunt u enkele van de Speech Service-API's uitvoeren in uw eigen omgeving. Containers zijn ideaal voor specifieke vereisten voor beveiliging en gegevensbeheer. In dit artikel leert u hoe u een spraakcontainer downloadt, installeert en uitvoert.

Met spraakcontainers kunnen klanten een spraaktoepassingsarchitectuur maken die is geoptimaliseerd voor zowel robuuste cloudmogelijkheden als edge-locaties. Er zijn verschillende containers beschikbaar die dezelfde prijzen [gebruiken](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) als azure Speech Services in de cloud.


> [!IMPORTANT]
> De volgende spraakcontainers zijn nu algemeen beschikbaar:
> * Standaard spraak-naar-tekst
> * Aangepaste spraak-naar-tekst
> * Standaard tekst-naar-spraak
> * Neurale tekst-naar-spraak
>
> De volgende spraakcontainers zijn in gated preview.
> * Aangepaste tekst naar spraak
> * Spraak Taaldetectie 
>
> Als u de spraakcontainers wilt gebruiken, moet u een onlineaanvraag indienen en deze laten goedkeuren. Zie de **sectie Goedkeuring aanvragen voor het uitvoeren van** de container hieronder voor meer informatie.

| Container | Functies | Laatste |
|--|--|--|
| Spraak naar tekst | Analyseert sentiment en transcribert continue realtime spraak- of batch audio-opnamen met tussenliggende resultaten.  | 2.11.0 |
| Aangepaste spraak-naar-tekst | Met behulp van een aangepast model uit de [Custom Speech-portal](https://speech.microsoft.com/customspeech)transcribert continue realtime spraak- of batchaudio-opnamen naar tekst met tussenliggende resultaten. | 2.11.0 |
| Tekst naar spraak | Converteert tekst naar natuurlijk klinkende spraak met tekst zonder tekst of Speech Synthesis Markup Language (SSML). | 1.13.0 |
| Aangepaste tekst naar spraak | Met een aangepast model uit [de Custom Voice-portal](https://aka.ms/custom-voice-portal)converteert u tekst naar natuurlijk klinkende spraak met invoer zonder tekst of Speech Synthesis Markup Language (SSML). | 1.13.0 |
| Spraak Taaldetectie | Detecteer de taal die wordt gesproken in audiobestanden. | 1.0 |
| Neurale tekst-naar-spraak | Converteert tekst naar natuurlijk klinkende spraak met behulp van deep neurale netwerktechnologie, waardoor meer natuurlijke gesynthetiseerde spraak mogelijk is. | 1.5.0 |

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

De volgende vereisten voordat u Speech-containers gebruikt:

| Vereist | Doel |
|--|--|
| Docker-engine | De Docker-engine moet zijn geïnstalleerd op een [hostcomputer.](#the-host-computer) Docker biedt pakketten waarmee de Docker-omgeving op [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms) kan worden geconfigureerd. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.<br><br> Docker moet worden geconfigureerd zodat de containers verbinding kunnen maken met en factureringsgegevens naar Azure kunnen verzenden. <br><br> **In Windows** moet Docker ook worden geconfigureerd om Linux-containers te ondersteunen.<br><br> |
| Bekendheid met Docker | U moet basiskennis hebben van Docker-concepten, zoals registers, opslagplaatsen, containers en container-afbeeldingen, evenals kennis van `docker` basisopdrachten. |
| Spraakresource | Als u deze containers wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure _Speech-resource_ om de bijbehorende API-sleutel en eindpunt-URI op te halen. Beide waarden zijn beschikbaar op de Azure Portal van **de** pagina Spraakoverzicht en Sleutels van de Azure Portal. Ze zijn beide vereist om de container te starten.<br><br>**{API_KEY}**: Een van de twee beschikbare resourcesleutels op de **pagina** Sleutels<br><br>**{ENDPOINT_URI}**: Het eindpunt zoals opgegeven op de **pagina** Overzicht |

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Ondersteuning voor Geavanceerde vectorextensie

De **host** is de computer met de Docker-container. De host *moet AVX2* [(Advanced Vector Extensions)](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) ondersteunen. U kunt controleren op AVX2-ondersteuning op Linux-hosts met de volgende opdracht:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> De hostcomputer is *vereist voor* de ondersteuning van AVX2. De container *werkt niet* correct zonder AVX2-ondersteuning.

### <a name="container-requirements-and-recommendations"></a>Vereisten en aanbevelingen voor containers

In de volgende tabel wordt de minimale en aanbevolen toewijzing van resources voor elke Speech-container beschreven.

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Spraak naar tekst | 2 kernen, 2 GB geheugen | 4 kernen, 4 GB geheugen |
| Aangepaste spraak-naar-tekst | 2 kernen, 2 GB geheugen | 4 kernen, 4 GB geheugen |
| Tekst naar spraak | 1 kern, 2 GB geheugen | 2 kernen, 3 GB geheugen |
| Aangepaste tekst-naar-spraak | 1 kern, 2 GB geheugen | 2 kernen, 3 GB geheugen |
| Spraak Taaldetectie | 1 kern, 1 GB geheugen | 1 kern, 1 GB geheugen |
| Neurale tekst-naar-spraak | 6 kernen, 12 GB geheugen | 8 kernen, 16 GB geheugen |

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.

De kern en het geheugen komen overeen `--cpus` met de instellingen en , die worden gebruikt als onderdeel van de opdracht `--memory` `docker run` .

> [!NOTE]
> Het minimum en de aanbevolen limieten zijn gebaseerd op Docker-limieten, *niet op* de resources van de hostmachine. In containers voor spraak-naar-tekst worden bijvoorbeeld geheugengedeelten van  een groot taalmodel in kaart gebracht. Het wordt aanbevolen dat het hele bestand in het geheugen past, wat nog eens 4-6 GB is. Bovendien kan de eerste uitvoering van een van beide containers langer duren, omdat modellen in het geheugen worden gepaginad.

## <a name="request-approval-to-the-run-the-container"></a>Goedkeuring aanvragen voor het uitvoeren van de container

Vul het aanvraagformulier in [en verzend het om](https://aka.ms/csgate) toegang tot de container aan te vragen. 

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]


## <a name="get-the-container-image-with-docker-pull"></a>De containerafbeelding op te halen met `docker pull`

Container-afbeeldingen voor Spraak zijn beschikbaar in de volgende Container Registry.

# <a name="speech-to-text"></a>[Spraak-naar-tekst](#tab/stt)

| Container | Opslagplaats |
|-----------|------------|
| Spraak naar tekst | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Aangepaste spraak-naar-tekst](#tab/cstt)

| Container | Opslagplaats |
|-----------|------------|
| Aangepaste spraak-naar-tekst | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Text-to-speech](#tab/tts)

| Container | Opslagplaats |
|-----------|------------|
| Tekst naar spraak | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest` |

# <a name="neural-text-to-speech"></a>[Neurale tekst-naar-spraak](#tab/ntts)

| Container | Opslagplaats |
|-----------|------------|
| Neurale tekst-naar-spraak | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Aangepaste tekst naar spraak](#tab/ctts)

| Container | Opslagplaats |
|-----------|------------|
| Aangepaste tekst naar spraak | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest` |

# <a name="speech-language-detection"></a>[Spraak Taaldetectie](#tab/lid)

> [!TIP]
> Voor de nuttigste resultaten raden we u aan de container Spraaktaaldetectie te gebruiken met de containers Spraak-naar-tekst of Aangepaste spraak-naar-tekst. 

| Container | Opslagplaats |
|-----------|------------|
| Spraak Taaldetectie | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker pull voor de Spraak-containers

# <a name="speech-to-text"></a>[Spraak-naar-tekst](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker pull voor de spraak-naar-tekst-container

Gebruik de [opdracht docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een containerafbeelding te downloaden uit Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest
```

> [!IMPORTANT]
> De `latest` tag haalt de `en-US` lokeert op. Zie [Speech-to-text locales (Taal-naar-tekst-taal) voor meer informatie.](#speech-to-text-locales)

#### <a name="speech-to-text-locales"></a>Taal-naar-tekst-taal

Alle tags, met uitzondering `latest` van hebben de volgende indeling en zijn casegevoelig:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

De volgende tag is een voorbeeld van de indeling:

```
2.6.0-amd64-en-us
```

Zie Afbeeldingstags van **spraak-naar-tekst** voor alle ondersteunde localen van de container voor [spraak-naar-tekst.](../containers/container-image-tags.md#speech-to-text)

# <a name="custom-speech-to-text"></a>[Aangepaste spraak-naar-tekst](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker pull voor de custom speech-to-text-container

Gebruik de [opdracht docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een containerafbeelding te downloaden uit het Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text:latest
```

> [!NOTE]
> De `locale` en voor aangepaste `voice` Spraak-containers worden bepaald door het aangepaste model dat door de container is opgenomen.

# <a name="text-to-speech"></a>[Text-to-speech](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker pull voor de text-to-speech-container

Gebruik de [opdracht docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een containerafbeelding te downloaden uit het Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech:latest
```

> [!IMPORTANT]
> De `latest` tag haalt de `en-US` locale en voice `ariarus` op. Zie [Text-to-speech locales (Tekst-naar-spraak-taal) voor meer informatie.](#text-to-speech-locales)

#### <a name="text-to-speech-locales"></a>Tekst-naar-spraak-taal

Alle tags, met uitzondering `latest` van , hebben de volgende indeling en zijn case-gevoelig:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

De volgende tag is een voorbeeld van de indeling:

```
1.8.0-amd64-en-us-ariarus
```

Zie Tekst-naar-spraak-afbeeldingstags voor alle ondersteunde taal en bijbehorende stemmen van de **tekst-naar-spraak-container.** [](../containers/container-image-tags.md#text-to-speech)

> [!IMPORTANT]
> Bij het maken van een *Text-to-Speech* HTTP POST is voor het [SSML-bericht (Speech Synthesis Markup Language)](speech-synthesis-markup.md) een element met `voice` een kenmerk `name` vereist. De waarde is de bijbehorende container-locale en voice, ook wel bekend als de ['korte naam'.](language-support.md#standard-voices) De tag heeft `latest` bijvoorbeeld de spraaknaam `en-US-AriaRUS` .

# <a name="neural-text-to-speech"></a>[Neurale tekst-naar-spraak](#tab/ntts)

#### <a name="docker-pull-for-the-neural-text-to-speech-container"></a>Docker-pull voor de neurale tekst-naar-spraak-container

Gebruik de [opdracht docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een containerafbeelding te downloaden uit het Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech:latest
```

> [!IMPORTANT]
> De `latest` tag haalt de `en-US` locale en voice `arianeural` op. Zie Neurale [tekst-naar-spraak-locales voor meer informatie.](#neural-text-to-speech-locales)

#### <a name="neural-text-to-speech-locales"></a>Neurale tekst-naar-spraak-localen

Alle tags, met uitzondering `latest` van hebben de volgende indeling en zijn casegevoelig:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>
```

De volgende tag is een voorbeeld van de indeling:

```
1.3.0-amd64-en-us-arianeural
```

Zie Tags voor neurale tekst-naar-spraak-afbeeldingen voor alle ondersteunde localen en bijbehorende stemmen van de **neurale tekst-naar-spraak-container.** [](../containers/container-image-tags.md#neural-text-to-speech)

> [!IMPORTANT]
> Bij het maken van een *Neural Text-to-Speech* HTTP POST is voor het [SSML-bericht (Speech Synthesis Markup Language)](speech-synthesis-markup.md) een element met `voice` een kenmerk `name` vereist. De waarde is de bijbehorende container-locale en voice, ook wel bekend als de ['korte naam'.](language-support.md#neural-voices) De tag zou `latest` bijvoorbeeld de spraaknaam `en-US-AriaNeural` hebben.

# <a name="custom-text-to-speech"></a>[Aangepaste tekst naar spraak](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker pull voor de aangepaste tekst-naar-spraak-container

Gebruik de [opdracht docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een containerafbeelding te downloaden uit Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech:latest
```

> [!NOTE]
> De `locale` en voor aangepaste `voice` Spraak-containers worden bepaald door het aangepaste model dat door de container is opgenomen.

# <a name="speech-language-detection"></a>[Spraak Taaldetectie](#tab/lid)

#### <a name="docker-pull-for-the-speech-language-detection-container"></a>Docker pull voor de Speech Taaldetectie-container

Gebruik de [opdracht docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om een containerafbeelding te downloaden uit Microsoft Container Registry.

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection:latest
```

***

## <a name="how-to-use-the-container"></a>De container gebruiken

Zodra de container op de [hostcomputer staat, gebruikt](#the-host-computer)u het volgende proces om met de container te werken.

1. [Voer de container uit](#run-the-container-with-docker-run)met de vereiste factureringsinstellingen. Meer [voorbeelden](speech-container-configuration.md#example-docker-run-commands) van de `docker run` opdracht zijn beschikbaar.
1. [Een query uitvoeren op het voorspellings-eindpunt van de container.](#query-the-containers-prediction-endpoint)

## <a name="run-the-container-with-docker-run"></a>De container uitvoeren met `docker run`

Gebruik de [opdracht docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg Het [verzamelen van vereiste parameters](#gathering-required-parameters) voor meer informatie over het op halen van de waarden en `{Endpoint_URI}` `{API_Key}` . Er [zijn](speech-container-configuration.md#example-docker-run-commands) ook aanvullende `docker run` voorbeelden van de opdracht beschikbaar.

# <a name="speech-to-text"></a>[Spraak-naar-tekst](#tab/stt)

Voer de volgende opdracht *uit om de standaardcontainer spraak-naar-tekst* uit te `docker run` voeren.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert een *spraak-naar-tekst-container* uit vanuit de containerafbeelding.
* Wijst 4 CPU-kernen en 4 gigabyte (GB) geheugen toe.
* Maakt TCP-poort 5000 beschikbaar en wijst een pseudo-TTY toe voor de container.
* De container wordt automatisch verwijderd nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.

> [!NOTE]
> Containers ondersteunen gecomprimeerde audio-invoer voor speech-SDK met GStreamer.
> Als u GStreamer in een container wilt installeren, volgt u de Linux-instructies voor GStreamer in [Codec gecomprimeerde audio-invoer gebruiken met de Speech SDK.](how-to-use-codec-compressed-audio-input-streams.md)

#### <a name="diarization-on-the-speech-to-text-output"></a>Diarisatie van de spraak-naar-tekst-uitvoer
Diarisatie is standaard ingeschakeld. als u diarisatie in uw reactie wilt krijgen, gebruikt u `diarize_speech_config.set_service_property` .

1. Stel de indeling van de woordgroepenuitvoer in op `Detailed` .
2. Stel de diarisatiemodus in. De ondersteunde modi zijn `Identity` en `Anonymous` .
```python
diarize_speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Format',
    value='Detailed',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)

diarize_speech_config.set_service_property(
    name='speechcontext-phraseDetection.speakerDiarization.mode',
    value='Identity',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```
> [!NOTE]
> De modus Identiteit retourneert `"SpeakerId": "Customer"` of `"SpeakerId": "Agent"` .
> De modus Anoniem `"SpeakerId": "Speaker 1"` retourneert of `"SpeakerId": "Speaker 2"`


#### <a name="analyze-sentiment-on-the-speech-to-text-output"></a>Sentiment analyseren op de uitvoer van spraak-naar-tekst 
Vanaf v2.6.0 van de spraak-naar-tekst-container moet u het TextAnalytics 3.0 API-eindpunt gebruiken in plaats van het preview-eindpunt. Bijvoorbeeld
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0/sentiment`
* `https://localhost:5000/text/analytics/v3.0/sentiment`

> [!NOTE]
> De Text Analytics `v3.0` API is niet achterwaarts compatibel met Text Analytics `v3.0-preview.1` . Gebruik voor de meest recente ondersteuning voor sentimentfunctie `v2.6.0` de containerafbeelding van spraak-naar-tekst en Text Analytics `v3.0` .

Vanaf v2.2.0 van de spraak-naar-tekst-container kunt u de [API voor sentimentanalyse v3](../text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md) aanroepen in de uitvoer. Als u sentimentanalyse wilt aanroepen, hebt u een Text Analytics API-resource-eindpunt nodig. Bijvoorbeeld: 
* `https://westus2.api.cognitive.microsoft.com/text/analytics/v3.0-preview.1/sentiment`
* `https://localhost:5000/text/analytics/v3.0-preview.1/sentiment`

Als u een Text Analytics-eindpunt in de cloud gebruikt, hebt u een sleutel nodig. Als u een lokaal Text Analytics, hoeft u dit mogelijk niet op te geven.

De sleutel en het eindpunt worden doorgegeven aan de Speech-container als argumenten, zoals in het volgende voorbeeld.

```bash
docker run -it --rm -p 5000:5000 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
CloudAI:SentimentAnalysisSettings:TextAnalyticsHost={TEXT_ANALYTICS_HOST} \
CloudAI:SentimentAnalysisSettings:SentimentAnalysisApiKey={SENTIMENT_APIKEY}
```

Met deze opdracht gebeurt het volgende:

* Voert dezelfde stappen uit als de bovenstaande opdracht.
* Slaat een Text Analytics API-eindpunt en -sleutel op voor het verzenden van aanvragen voor sentimentanalyse. 

#### <a name="phraselist-v2-on-the-speech-to-text-output"></a>Phraselist v2 in de spraak-naar-tekst-uitvoer 

Vanaf v2.6.0 van de spraak-naar-tekst-container kunt u de uitvoer krijgen met uw eigen zinnen: de hele zin of zinnen in het midden. Bijvoorbeeld de *lange man* in de volgende zin:

* "Dit is een zin **die de lange man** is, dit is een andere zin."

Als u een frasenlijst wilt configureren, moet u uw eigen zinnen toevoegen wanneer u de aanroep maakt. Bijvoorbeeld:

```python
    phrase="the tall man"
    recognizer = speechsdk.SpeechRecognizer(
        speech_config=dict_speech_config,
        audio_config=audio_config)
    phrase_list_grammer = speechsdk.PhraseListGrammar.from_recognizer(recognizer)
    phrase_list_grammer.addPhrase(phrase)
    
    dict_speech_config.set_service_property(
        name='setflight',
        value='xonlineinterp',
        channel=speechsdk.ServicePropertyChannel.UriQueryParameter
    )
```

Als u meerdere zinnen wilt toevoegen, roept u elke woordgroep aan om deze toe te `.addPhrase()` voegen aan de woordgroepenlijst. 


# <a name="custom-speech-to-text"></a>[Aangepaste spraak-naar-tekst](#tab/cstt)

De *custom speech-to-text-container* is afhankelijk van een aangepast spraakmodel. Het aangepaste model moet zijn getraind met [behulp](how-to-custom-speech-train-model.md) van de [aangepaste spraakportal](https://speech.microsoft.com/customspeech).

De aangepaste **spraakmodel-id** is vereist om de container uit te voeren. U vindt deze op de **pagina Training** van de aangepaste spraakportal. Ga in de aangepaste spraakportal naar de **pagina Training** en selecteer het model.
<br>

![Aangepaste pagina voor spraaktraining](media/custom-speech/custom-speech-model-training.png)

Verkrijg de **model-id** die moet worden gebruikt als het argument voor de `ModelId` parameter van de `docker run` opdracht.
<br>

![Details van aangepast spraakmodel](media/custom-speech/custom-speech-model-details.png)

De volgende tabel vertegenwoordigt de verschillende `docker run` parameters en de bijbehorende beschrijvingen:

| Parameter | Beschrijving |
|---------|---------|
| `{VOLUME_MOUNT}` | De [volume-mount van de hostcomputer,](https://docs.docker.com/storage/volumes/)die docker gebruikt om het aangepaste model te blijven gebruiken. Bijvoorbeeld *C:\CustomSpeech* waar het *C-station* zich op de hostmachine bevindt. |
| `{MODEL_ID}` | De Custom Speech **Model ID** van de **pagina Training** van de portal voor aangepaste spraak. |
| `{ENDPOINT_URI}` | Het eindpunt is vereist voor het meten en factureren. Zie Het verzamelen van vereiste [parameters voor meer informatie.](#gathering-required-parameters) |
| `{API_KEY}` | De API-sleutel is vereist. Zie Het verzamelen van vereiste [parameters voor meer informatie.](#gathering-required-parameters) |

Voer de volgende opdracht uit om de aangepaste *spraak-naar-tekst-container* uit te `docker run` voeren:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert een *aangepaste spraak-naar-tekst-container* uit vanuit de containerafbeelding.
* Wijst 4 CPU-kernen en 4 gigabyte (GB) geheugen toe.
* Laadt *het aangepaste spraak-naar-tekst-model* vanaf de volume-invoer mount, bijvoorbeeld *C:\CustomSpeech.*
* Maakt TCP-poort 5000 beschikbaar en wijst een pseudo-TTY toe voor de container.
* Downloadt het model op basis `ModelId` van de (indien niet gevonden op de volume-mount).
* Als het aangepaste model eerder is gedownload, wordt `ModelId` de genegeerd.
* De container wordt automatisch verwijderd nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.


#### <a name="base-model-download-on-the-custom-speech-to-text-container"></a>Basismodel downloaden op de aangepaste container voor spraak-naar-tekst  
Vanaf v2.6.0 van de container custom-speech-to-text kunt u de beschikbare basismodelgegevens verkrijgen met behulp van de optie `BaseModelLocale=<locale>` . Met deze optie krijgt u een lijst met beschikbare basismodellen op basis van die lokatie onder uw factureringsrekening. Bijvoorbeeld:

```bash
docker run --rm -it \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
BaseModelLocale={LOCALE} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert een *aangepaste spraak-naar-tekst-container* uit vanuit de containerafbeelding.
* Controleer en retourneren de beschikbare basismodellen van de doel-locale.

De uitvoer geeft u een lijst met basismodellen met de informatie-id, de model-id en de datum/tijd van het maken. U kunt de model-id gebruiken om het liever specifieke basismodel te downloaden en te gebruiken. Bijvoorbeeld:
```
Checking available base model for en-us
2020/10/30 21:54:20 [Info] Searching available base models for en-us
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T08:23:42Z, Id: a3d8aab9-6f36-44cd-9904-b37389ce2bfa
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2016-11-04T12:01:02Z, Id: cc7826ac-5355-471d-9bc6-a54673d06e45
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2017-08-17T12:00:00Z, Id: a1f8db59-40ff-4f0e-b011-37629c3a1a53
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-04-16T11:55:00Z, Id: c7a69da3-27de-4a4b-ab75-b6716f6321e5
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-09-21T15:18:43Z, Id: da494a53-0dad-4158-b15f-8f9daca7a412
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-10-19T11:28:54Z, Id: 84ec130b-d047-44bf-a46d-58c1ac292ca7
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T07:59:09Z, Id: ee5c100f-152f-4ae5-9e9d-014af3c01c56
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2018-11-26T09:21:55Z, Id: d04959a6-71da-4913-9997-836793e3c115
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-01-11T10:04:19Z, Id: 488e5f23-8bc5-46f8-9ad8-ea9a49a8efda
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-02-18T14:37:57Z, Id: 0207b3e6-92a8-4363-8c0e-361114cdd719
2020/10/30 21:54:21 [Info] [Base model] Locale: en-us, CreatedDate: 2019-03-03T17:34:10Z, Id: 198d9b79-2950-4609-b6ec-f52254074a05
2020/10/30 21:54:21 [Fatal] Please run this tool again and assign --modelId '<one above base model id>'. If no model id listed above, it means currently there is no available base model for en-us
```

#### <a name="custom-pronunciation-on-the-custom-speech-to-text-container"></a>Aangepaste uitspraak voor de aangepaste container voor spraak-naar-tekst 
Vanaf v2.5.0 van de container custom-speech-to-text kunt u een aangepast uitspraakresultaat krijgen in de uitvoer. U hoeft alleen maar uw eigen aangepaste uitspraakregels in te stellen in uw aangepaste model en het model te laten toevoegen aan de container custom-speech-to-text.


# <a name="text-to-speech"></a>[Text-to-speech](#tab/tts)

Voer de volgende opdracht *uit om de standaardcontainer Tekst-naar-spraak* uit te `docker run` voeren.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert een *standaardtekst-naar-spraak-container* uit vanuit de containerafbeelding.
* Wijst 1 CPU-kern en 2 gigabyte (GB) geheugen toe.
* Maakt TCP-poort 5000 beschikbaar en wijst een pseudo-TTY toe voor de container.
* De container wordt automatisch verwijderd nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.

# <a name="neural-text-to-speech"></a>[Neurale tekst-naar-spraak](#tab/ntts)

Voer de volgende opdracht uit om de neurale *tekst-naar-spraak-container* uit te `docker run` voeren.

```bash
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert een *neurale tekst-naar-spraak-container* uit vanuit de containerafbeelding.
* Wijst 6 CPU-kernen en 12 GIGABYTE (GB) geheugen toe.
* Maakt TCP-poort 5000 beschikbaar en wijst een pseudo-TTY toe voor de container.
* De container wordt automatisch verwijderd nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.

# <a name="custom-text-to-speech"></a>[Aangepaste tekst naar spraak](#tab/ctts)

De *container Aangepaste tekst-naar-spraak* is afhankelijk van een aangepast spraakmodel. Het aangepaste model moet zijn getraind [met behulp](how-to-custom-voice-create-voice.md) van de [Custom Voice-portal.](https://aka.ms/custom-voice-portal) De **model-id voor aangepaste** spraak is vereist om de container uit te voeren. U vindt deze op de **pagina Training** van de custom voice-portal. Navigeer in de portal voor aangepaste spraak naar de **pagina Training** en selecteer het model.
<br>

![Trainingspagina voor aangepaste spraak](media/custom-voice/custom-voice-model-training.png)

Verkrijg de **model-id die** moet worden gebruikt als het argument voor `ModelId` de parameter van de opdracht docker run.
<br>

![Details van aangepast spraakmodel](media/custom-voice/custom-voice-model-details.png)

De volgende tabel vertegenwoordigt de verschillende `docker run` parameters en de bijbehorende beschrijvingen:

| Parameter | Beschrijving |
|---------|---------|
| `{VOLUME_MOUNT}` | De volume mount [van de hostcomputer,](https://docs.docker.com/storage/volumes/)die docker gebruikt om het aangepaste model persistent te maken. Bijvoorbeeld *C:\CustomSpeech* waar het *C-station* zich op de hostmachine bevindt. |
| `{MODEL_ID}` | De Custom Speech **Model-id** van de **pagina Training** van de custom voice-portal. |
| `{ENDPOINT_URI}` | Het eindpunt is vereist voor het meten en factureren. Zie vereiste parameters verzamelen [voor meer informatie.](#gathering-required-parameters) |
| `{API_KEY}` | De API-sleutel is vereist. Zie Vereiste parameters verzamelen [voor meer informatie.](#gathering-required-parameters) |

Voer de volgende opdracht *uit om de aangepaste tekst-naar-spraak-container* uit te `docker run` voeren:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert een *aangepaste tekst-naar-spraak-container* uit vanuit de containerafbeelding.
* Wijst 1 CPU-kern en 2 gigabyte (GB) geheugen toe.
* Laadt *het aangepaste tekst-naar-spraak-model* vanaf de volume-invoer mount, bijvoorbeeld *C:\CustomVoice.*
* Maakt TCP-poort 5000 beschikbaar en wijst een pseudo-TTY toe voor de container.
* Downloadt het model op basis `ModelId` van de (indien niet gevonden op de volume-mount).
* Als het aangepaste model eerder is gedownload, wordt `ModelId` de genegeerd.
* De container wordt automatisch verwijderd nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.

# <a name="speech-language-detection"></a>[Spraak Taaldetectie](#tab/lid)

Als u de *Speech Taaldetectie* container wilt uitvoeren, voert u de volgende opdracht `docker run` uit.

```bash
docker run --rm -it -p 5003:5003 --memory 1g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende: 

* Voert een container voor spraaktaaldetectie uit vanuit de containerafbeelding. Er worden momenteel geen kosten in rekening gebracht voor het uitvoeren van deze afbeelding.
* Wijst 1 CPU-kernen en 1 gigabyte (GB) geheugen toe.
* Maakt TCP-poort 5003 beschikbaar en wijst een pseudo-TTY toe voor de container.
* De container wordt automatisch verwijderd nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.

Als u alleen speech-Taaldetectie verstuurt, moet u de waarde van de Speech-client `phraseDetection` instellen op `None` .  

```python
speech_config.set_service_property(
      name='speechcontext-phraseDetection.Mode',
      value='None',
      channel=speechsdk.ServicePropertyChannel.UriQueryParameter
   )
```

Als u deze container wilt uitvoeren met de container spraak-naar-tekst, kunt u deze [Docker-afbeelding gebruiken.](https://hub.docker.com/r/antsu/on-prem-client) Nadat beide containers zijn gestart, gebruikt u deze Docker Run-opdracht om uit te `speech-to-text-with-languagedetection-client` voeren.

```Docker
docker run --rm -v ${HOME}:/root -ti antsu/on-prem-client:latest ./speech-to-text-with-languagedetection-client ./audio/LanguageDetection_en-us.wav --host localhost --lport 5003 --sport 5000
```

> [!NOTE]
> Het verhogen van het aantal gelijktijdige aanroepen kan van invloed zijn op de betrouwbaarheid en latentie. Voor taaldetectie raden we maximaal 4 gelijktijdige aanroepen aan met 1 CPU en 1 GB geheugen. Voor hosts met 2 CPU's en 2 GB geheugen raden we maximaal 6 gelijktijdige aanroepen aan.

***

> [!IMPORTANT]
> De opties , en moeten worden opgegeven om de container uit te voeren. Anders `Eula` wordt de container niet `Billing` `ApiKey` start.  Zie Facturering voor meer [informatie.](#billing)

## <a name="query-the-containers-prediction-endpoint"></a>Een query uitvoeren op het voorspellingseindpunt van de container

> [!NOTE]
> Gebruik een uniek poortnummer als u meerdere containers gebruikt.

| Containers | SDK-host-URL | Protocol |
|--|--|--|
| Standaard spraak-naar-tekst en aangepaste spraak-naar-tekst | `ws://localhost:5000` | WS |
| Tekst-naar-spraak (inclusief Standard, Aangepast en Neuraal), spraaktaaldetectie | `http://localhost:5000` | HTTP |

Zie containerbeveiliging voor meer informatie over het gebruik van WSS- en [HTTPS-protocollen.](../cognitive-services-container-support.md#azure-cognitive-services-container-security)

### <a name="speech-to-text-standard-and-custom"></a>Spraak-naar-tekst (Standard en Custom)

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

#### <a name="analyze-sentiment"></a>Stemming analyseren

Als u uw api Text Analytics referenties hebt opgegeven voor de [container](#analyze-sentiment-on-the-speech-to-text-output), kunt u de Speech SDK gebruiken om spraakherkenningsaanvragen te verzenden met sentimentanalyse. U kunt de API-antwoorden configureren om een eenvoudige *of* gedetailleerde *indeling te* gebruiken.
> [!NOTE]
> v1.13 van de Speech Service Python SDK heeft een geïdentificeerd probleem met sentimentanalyse. Gebruik v1.12.x of eerder als u sentimentanalyse gebruikt in de Speech Service Python SDK.

# <a name="simple-format"></a>[Eenvoudige indeling](#tab/simple-format)

Als u de Speech-client wilt configureren voor het gebruik van een eenvoudige indeling, voegt `"Sentiment"` u toe als een waarde voor `Simple.Extensions` . Als u een specifieke modelversie Text Analytics, vervangt u `'latest'` in de `speechcontext-phraseDetection.sentimentAnalysis.modelversion` eigenschapsconfiguratie.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Simple.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Simple.Extensions` retournt het gevoelsresultaat in de hoofdlaag van het antwoord.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6098574b79434bd4849fee7e0a50f22e",
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

# <a name="detailed-format"></a>[Gedetailleerde indeling](#tab/detailed-format)

Als u de Speech-client wilt configureren voor het gebruik van een gedetailleerde indeling, voegt u toe als `"Sentiment"` een waarde voor , of `Detailed.Extensions` `Detailed.Options` beide. Als u een specifieke Text Analytics modelversie wilt kiezen, vervangt u `'latest'` in de `speechcontext-phraseDetection.sentimentAnalysis.modelversion` eigenschapsconfiguratie.

```python
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Options',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-PhraseOutput.Detailed.Extensions',
    value='["Sentiment"]',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentAnalysis.modelversion',
    value='latest',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

`Detailed.Extensions` biedt gevoelsresultaat in de hoofdlaag van het antwoord. `Detailed.Options` geeft het resultaat weer in `NBest` de laag van het antwoord. Ze kunnen afzonderlijk of samen worden gebruikt.

```json
{
   "DisplayText":"What's the weather like?",
   "Duration":13000000,
   "Id":"6a2aac009b9743d8a47794f3e81f7963",
   "NBest":[
      {
         "Confidence":0.973695,
         "Display":"What's the weather like?",
         "ITN":"what's the weather like",
         "Lexical":"what's the weather like",
         "MaskedITN":"What's the weather like",
         "Sentiment":{
            "Negative":0.03,
            "Neutral":0.79,
            "Positive":0.18
         }
      },
      {
         "Confidence":0.9164971,
         "Display":"What is the weather like?",
         "ITN":"what is the weather like",
         "Lexical":"what is the weather like",
         "MaskedITN":"What is the weather like",
         "Sentiment":{
            "Negative":0.02,
            "Neutral":0.88,
            "Positive":0.1
         }
      }
   ],
   "Offset":4700000,
   "RecognitionStatus":"Success",
   "Sentiment":{
      "Negative":0.03,
      "Neutral":0.79,
      "Positive":0.18
   }
}
```

---

Als u sentimentanalyse volledig wilt uitschakelen, voegt u een `false` waarde toe aan `sentimentanalysis.enabled` .

```python
speech_config.set_service_property(
    name='speechcontext-phraseDetection.sentimentanalysis.enabled',
    value='false',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

### <a name="text-to-speech-standard-neural-and-custom"></a>Tekst-naar-spraak (Standaard, Neuraal en Aangepast)

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Meerdere containers op dezelfde host uitvoeren

Als u van plan bent om meerdere containers met open zichtbare poorten uit te voeren, moet u elke container met een andere open poort uitvoeren. Voer bijvoorbeeld de eerste container uit op poort 5000 en de tweede container op poort 5001.

U kunt deze container en een andere Azure Cognitive Services container samen op de HOST laten uitvoeren. U kunt ook meerdere containers van hetzelfde Cognitive Services container uitvoeren.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Bij het starten of uitvoeren van de container kunnen er problemen zijn. Gebruik een [uitvoer-mount en](speech-container-configuration.md#mount-settings) schakel logboekregistratie in. Hierdoor kan de container logboekbestanden genereren die handig zijn bij het oplossen van problemen.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De Spraak-containers verzenden factureringsgegevens naar Azure met behulp van een *Spraak-resource* in uw Azure-account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie Containers configureren voor meer informatie [over deze opties.](speech-container-configuration.md)

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werkstromen geleerd voor het downloaden, installeren en uitvoeren van Speech-containers. Samenvatting:

* Speech biedt vier Linux-containers voor Docker, die verschillende mogelijkheden omvatten:
  * *Spraak-naar-tekst*
  * *Aangepaste spraak-naar-tekst*
  * *Text-to-speech*
  * *Aangepaste tekst naar spraak*
  * *Neurale tekst-naar-spraak*
  * *Spraak Taaldetectie*
* Container-afbeeldingen worden gedownload uit het containerregister in Azure.
* Container-afbeeldingen worden uitgevoerd in Docker.
* Of u nu de REST API (alleen tekst-naar-spraak) of de SDK (Spraak-naar-tekst of Tekst-naar-spraak) gebruikt, u geeft de host-URI van de container op. 
* U moet factureringsgegevens verstrekken bij het instantiëren van een container.

> [!IMPORTANT]
>  Cognitive Services containers zijn niet gelicentieerd om te worden uitgevoerd zonder dat ze zijn verbonden met Azure voor het meten. Klanten moeten de containers in staat stellen om te allen tijde factureringsgegevens te communiceren met de meetservice. Cognitive Services containers verzenden geen klantgegevens (bijvoorbeeld de afbeelding of tekst die wordt geanalyseerd) naar Microsoft.

## <a name="next-steps"></a>Volgende stappen

* Bekijk [Containers configureren](speech-container-configuration.md) voor configuratie-instellingen
* Meer informatie over het [gebruik van Speech Service-containers met Kubernetes en Helm](speech-container-howto-on-premises.md)
* Meer containers [Cognitive Services gebruiken](../cognitive-services-container-support.md)
