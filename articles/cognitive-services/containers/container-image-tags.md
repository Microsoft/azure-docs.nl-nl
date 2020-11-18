---
title: Tags voor Cognitive Services-containerinstallatiekopie
titleSuffix: Azure Cognitive Services
description: Een uitgebreide lijst met alle afbeeldings Tags voor de cognitieve service container.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/17/2020
ms.author: aahi
ms.openlocfilehash: ba51776942ad28fc8d4b0db7dd2d0e162e5322b5
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94743334"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Azure Cognitive Services container-afbeeldings Tags en opmerkingen bij de release

Azure Cognitive Services biedt veel container installatie kopieën. De container registers en de bijbehorende opslag plaatsen variëren tussen container installatie kopieën. De naam van elke container installatie kopie biedt meerdere labels. Een container afbeeldings code is een mechanisme voor het versie beheer van de container installatie kopie. Dit artikel is bedoeld om te worden gebruikt als een uitgebreide verwijzing voor het weer geven van alle Cognitive Services container installatie kopieën en de beschik bare Tags.

> [!TIP]
> Wanneer u gebruikt [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , moet u zorgvuldig letten op de behuizing van het container register, de opslag plaats, de naam van de container installatie kopie en de bijbehorende tag. Dit zijn **hoofdletter gevoelig**.

## <a name="anomaly-detector"></a>Anomaly Detector

De [afwijkende detector][ad-containers] container installatie kopie vindt u in het `mcr.microsoft.com` container register Syndicate. Deze bevindt zich in de `azure-cognitive-services/decision` opslag plaats en heet `anomaly-detector` . De volledig gekwalificeerde naam van de container installatie kopie is, `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` .

Voor deze container installatie kopie zijn de volgende tags beschikbaar. U kunt ook een volledige lijst met [Tags vinden op het MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list).

# <a name="latest-version"></a>[Nieuwste versie](#tab/current)

| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[Vorige versies](#tab/previous)

| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>OCR (optische teken herkenning) lezen

De [Computer Vision][cv-containers] OCR-container installatie kopie lezen kan worden gevonden in het `mcr.microsoft.com` container register Syndicate. Deze bevindt zich in de `azure-cognitive-services` opslag plaats en heet `read` . De volledig gekwalificeerde naam van de container installatie kopie is, `mcr.microsoft.com/azure-cognitive-services/vision/read` .

Voor deze container installatie kopie zijn de volgende tags beschikbaar. U kunt ook een volledige lijst met [Tags vinden op het MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list).

# <a name="latest-version"></a>[Nieuwste versie](#tab/current)

Release opmerkingen voor `v2.0.013250001-amd64-preview` :

* Verminder het geheugen gebruik voor de container.
* Externe cache is vereist voor het instellen van meerdere peulen. Stel bijvoorbeeld redis in voor caching.
* Vast ontbrekend resultaat wanneer de redis-cache is ingesteld en `ResultExpirationPeriod` is ingesteld op 0.
* De limiet voor de grootte van de aanvraag hoofdtekst van 26MB verwijderen. De container kan nu >26MB-bestanden accepteren.
* Tijds tempel en build-versie toevoegen aan console logboek registratie.

| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[Vorige versies](#tab/previous)

Release opmerkingen voor `1.1.013050001-amd64-preview`

* `ReadEngineConfig:ResultExpirationPeriod`De configuratie van de container initialisatie is toegevoegd om op te geven wanneer het systeem de herkennings resultaten moet opschonen. 
    * De instelling is in uren en de standaard waarde is 48 uur.
    * De instelling kan het geheugen gebruik verminderen voor het opslaan van de resultaten, met name als de opslag ruimte in het geheugen wordt gebruikt.
    * Voorbeeld 1. ReadEngineConfig: ResultExpirationPeriod = 1, het systeem verwijdert het herkennings resultaat 1hr na het proces.
    * Als deze configuratie is ingesteld op 0, wordt het herkennings resultaat door het systeem gewist nadat het resultaat is opgehaald.

* Er is een interne server fout 500 opgelost wanneer een ongeldige afbeeldings indeling wordt door gegeven aan het systeem. Er wordt nu een 400-fout geretourneerd:

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Form Recognizer

De container installatie kopie voor de [formulier herkenning][fr-containers] vindt u in het `mcr.microsoft.com` container register Syndicate. Deze bevindt zich in de `azure-cognitive-services/custom-form` opslag plaats en heet `labeltool` . De volledig gekwalificeerde naam van de container installatie kopie is, `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` .

Voor deze container installatie kopie zijn de volgende tags beschikbaar. U kunt ook een volledige lijst met [Tags vinden op het MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list).

# <a name="latest-version"></a>[Nieuwste versie](#tab/current)

| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[Vorige versies](#tab/previous)

| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

De [Luis][lu-containers] -container installatie kopie vindt u in het `mcr.microsoft.com` container register Syndicate. Deze bevindt zich in de `azure-cognitive-services/language` opslag plaats en heet `luis` . De volledig gekwalificeerde naam van de container installatie kopie is, `mcr.microsoft.com/azure-cognitive-services/language/luis` .

Voor deze container installatie kopie zijn de volgende tags beschikbaar. U kunt ook een volledige lijst met [Tags vinden op het MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list).

# <a name="latest-version"></a>[Nieuwste versie](#tab/current)

| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[Vorige versie](#tab/previous)

| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Custom Speech-naar-tekst

De [Custom speech-naar-tekst][sp-cstt] container installatie kopie vindt u in het `mcr.microsoft.com` container register Syndicate. Deze bevindt zich in de `azure-cognitive-services/speechservices/` opslag plaats en heet `custom-speech-to-text` . De volledig gekwalificeerde naam van de container installatie kopie is, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` . U kunt ook een volledige lijst met [Tags vinden op het MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list).


# <a name="latest-version"></a>[Nieuwste versie](#tab/current)

Release Opmerking voor `2.6.0-amd64` :

**Functies**
* Ondersteuning voor phraselist v2 
* Woordgroepen lijsten worden ondersteund in de volgende land instellingen:
    * en-au
    * en-ca
    * en-GB
    * en-in
    * en-NZ
    * nl-nl
    * zh-cn
* Ondersteuning voor het downloaden van een aangepast basis model. 
    * Gebruiken `BaseModelLocale=<locale>` met de `docker run` opdracht
* Volledig gemigreerd naar .NET 3,1

**Dit**
* Er is een probleem opgelost waarbij de betrouwbaarheids Score altijd 1 is in de Diarization-modus
* Gemigreerd naar de TextAnalytics 3,0-API

Houd er rekening mee dat de grootte van deze container installatie kopie groter is geworden vanwege de opgenomen woordgroepen lijsten.

| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `2.6.0-amd64`                 |       |


# <a name="previous-version"></a>[Vorige versie](#tab/previous)

Release Opmerking voor `2.5.0-amd64` :

**Functies**
* Aangepaste uitspraak op aangepaste modellen ondersteunen
* Azure en Azure Amerikaanse overheids Cloud ondersteunen

**Dit**
* Het uitvoeren als niet-hoofd gebruikers probleem in de Diarization-modus oplossen

| Afbeeldings Tags                    | Opmerkingen               |
|-------------------------------|:--------------------|
| `2.5.0-amd64`                 |   1e GA-versie    |

---

## <a name="custom-text-to-speech"></a>Aangepaste tekst-naar-spraak

De [aangepaste tekst-naar-spraak-][sp-ctts] container installatie kopie vindt u in het `mcr.microsoft.com` container register Syndicate. Deze bevindt zich in de `azure-cognitive-services/speechservices/` opslag plaats en heet `custom-text-to-speech` . De volledig gekwalificeerde naam van de container installatie kopie is, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . U kunt ook een volledige lijst met [Tags vinden op het MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list).


# <a name="latest-version"></a>[Nieuwste versie](#tab/current)

Release Opmerking voor `1.8.0-amd64` :

**Functies**
* Volledig gemigreerd naar .NET 3,1

| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.8.0-amd64`                 |       |


# <a name="previous-version"></a>[Vorige versie](#tab/previous)

Release Opmerking voor `1.7.0-amd64` :

**Functie**
* Gedeeltelijk gemigreerd naar .NET 3,1

| Afbeeldings Tags                    | Opmerkingen               |
|-------------------------------|:--------------------|
| `1.7.0-amd64`                 |   1e GA-versie    |

---

## <a name="speech-to-text"></a>Spraak naar tekst

De afbeelding voor de [spraak-naar-tekst][sp-stt] container kan worden gevonden in het `mcr.microsoft.com` container register Syndicate. Deze bevindt zich in de `azure-cognitive-services/speechservices/` opslag plaats en heet `speech-to-text` . De volledig gekwalificeerde naam van de container installatie kopie is, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . U kunt een volledige lijst met [Tags vinden op het MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list).

Sinds spraak-naar-tekst v-2.5.0 worden afbeeldingen ondersteund in de regio van de *Amerikaanse overheids-Virginia* . Gebruik het facturerings eindpunt en de API-sleutels van de *Amerikaanse overheid* wanneer u deze regio gebruikt.

# <a name="latest-version"></a>[Nieuwste versie](#tab/current)

Release Opmerking voor `2.6.0-amd64-<locale>` :

**Functies**
* Bijgewerkt naar de nieuwste modellen en volledig gemigreerd naar .NET 3,1
* Ondersteuning voor phraselist v2
* Woordgroepen lijsten worden ondersteund in de volgende land instellingen:
    * en-au
    * en-ca
    * en-GB
    * en-in
    * en-NZ
    * nl-nl
    * zh-cn
* Ondersteuning voor nieuwe land instellingen `cs-CZ` 
    * Hoofdletter gebruik en interpunctie worden momenteel niet ondersteund.

**Dit**
* Hiermee wordt een probleem opgelost waarbij betrouwbaarheids scores altijd 1 zijn in de Diarization-modus
* Gemigreerd gebruik de TextAnalytics 3,0-API

Houd er rekening mee dat de grootte van deze container installatie kopie groter is geworden vanwege de opgenomen woordgroepen lijsten. 

| Afbeeldings Tags                    | Opmerkingen                                                                                                | 
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Container installatie kopie met de `en-US` land instelling.                                                             |
| `2.6.0-amd64-<locale>`        | Vervang door `<locale>` een van de beschik bare land instellingen, zoals hieronder wordt weer gegeven. Bijvoorbeeld `2.6.0-amd64-en-us`. |

Voor deze container zijn de volgende land instellingen beschikbaar.

| Land instelling voor v 2.6.0           | Opmerkingen                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Container installatie kopie met de `ar-AE` land instelling. |
| `ar-eg`                     | Container installatie kopie met de `ar-EG` land instelling. |
| `ar-kw`                     | Container installatie kopie met de `ar-KW` land instelling. |
| `ar-qa`                     | Container installatie kopie met de `ar-QA` land instelling. |
| `ar-sa`                     | Container installatie kopie met de `ar-SA` land instelling. |
| `ca-es`                     | Container installatie kopie met de `ca-ES` land instelling. |
| `cs-cz`                     | Container installatie kopie met de `cs-CZ` land instelling. |
| `da-dk`                     | Container installatie kopie met de `da-DK` land instelling. |
| `de-de`                     | Container installatie kopie met de `de-DE` land instelling. |
| `en-au`                     | Container installatie kopie met de `en-AU` land instelling. |
| `en-ca`                     | Container installatie kopie met de `en-CA` land instelling. |
| `en-gb`                     | Container installatie kopie met de `en-GB` land instelling. |
| `en-in`                     | Container installatie kopie met de `en-IN` land instelling. |
| `en-nz`                     | Container installatie kopie met de `en-NZ` land instelling. |
| `en-us`                     | Container installatie kopie met de `en-US` land instelling. |
| `es-es`                     | Container installatie kopie met de `es-ES` land instelling. |
| `es-mx`                     | Container installatie kopie met de `es-MX` land instelling. |
| `fi-fi`                     | Container installatie kopie met de `fi-FI` land instelling. |
| `fr-ca`                     | Container installatie kopie met de `fr-CA` land instelling. |
| `fr-fr`                     | Container installatie kopie met de `fr-FR` land instelling. |
| `gu-in`                     | Container installatie kopie met de `gu-IN` land instelling. |
| `hi-in`                     | Container installatie kopie met de `hi-IN` land instelling. |
| `it-it`                     | Container installatie kopie met de `it-IT` land instelling. |
| `ja-jp`                     | Container installatie kopie met de `ja-JP` land instelling. |
| `ko-kr`                     | Container installatie kopie met de `ko-KR` land instelling. |
| `mr-in`                     | Container installatie kopie met de `mr-IN` land instelling. |
| `nb-no`                     | Container installatie kopie met de `nb-NO` land instelling. |
| `nl-nl`                     | Container installatie kopie met de `nl-NL` land instelling. |
| `pl-pl`                     | Container installatie kopie met de `pl-PL` land instelling. |
| `pt-br`                     | Container installatie kopie met de `pt-BR` land instelling. |
| `pt-pt`                     | Container installatie kopie met de `pt-PT` land instelling. |
| `ru-ru`                     | Container installatie kopie met de `ru-RU` land instelling. |
| `sv-se`                     | Container installatie kopie met de `sv-SE` land instelling. |
| `ta-in`                     | Container installatie kopie met de `ta-IN` land instelling. |
| `te-in`                     | Container installatie kopie met de `te-IN` land instelling. |
| `th-th`                     | Container installatie kopie met de `th-TH` land instelling. |
| `tr-tr`                     | Container installatie kopie met de `tr-TR` land instelling. |
| `zh-cn`                     | Container installatie kopie met de `zh-CN` land instelling. |
| `zh-hk`                     | Container installatie kopie met de `zh-HK` land instelling. |
| `zh-tw`                     | Container installatie kopie met de `zh-TW` land instelling. |


# <a name="previous-version"></a>[Vorige versie](#tab/previous)

Release Opmerking voor `2.5.0-amd64-<locale>` :

**Functies**
* Ondersteuning voor Azure-Cloud voor de Amerikaanse overheid

**Dit**
* Hiermee wordt een probleem opgelost waarbij een niet-hoofd gebruiker wordt uitgevoerd in de Diarization-modus

| Afbeeldings Tags                  | Opmerkingen                                    |
|-----------------------------|:-----------------------------------------|
| `2.5.0-amd64-<locale>`      | Vervang door `<locale>` een van de beschik bare land instellingen, zoals hieronder wordt weer gegeven. Bijvoorbeeld `2.5.0-amd64-en-us`.  |

Voor deze container zijn de volgende land instellingen beschikbaar.

| Land instelling voor v 2.5.0           | Opmerkingen                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Container installatie kopie met de `ar-AE` land instelling. |
| `ar-eg`                     | Container installatie kopie met de `ar-EG` land instelling. |
| `ar-kw`                     | Container installatie kopie met de `ar-KW` land instelling. |
| `ar-qa`                     | Container installatie kopie met de `ar-QA` land instelling. |
| `ar-sa`                     | Container installatie kopie met de `ar-SA` land instelling. |
| `ca-es`                     | Container installatie kopie met de `ca-ES` land instelling. |
| `da-dk`                     | Container installatie kopie met de `da-DK` land instelling. |
| `de-de`                     | Container installatie kopie met de `de-DE` land instelling. |
| `en-au`                     | Container installatie kopie met de `en-AU` land instelling. |
| `en-ca`                     | Container installatie kopie met de `en-CA` land instelling. |
| `en-gb`                     | Container installatie kopie met de `en-GB` land instelling. |
| `en-in`                     | Container installatie kopie met de `en-IN` land instelling. |
| `en-nz`                     | Container installatie kopie met de `en-NZ` land instelling. |
| `en-us`                     | Container installatie kopie met de `en-US` land instelling. |
| `es-es`                     | Container installatie kopie met de `es-ES` land instelling. |
| `es-mx`                     | Container installatie kopie met de `es-MX` land instelling. |
| `fi-fi`                     | Container installatie kopie met de `fi-FI` land instelling. |
| `fr-ca`                     | Container installatie kopie met de `fr-CA` land instelling. |
| `fr-fr`                     | Container installatie kopie met de `fr-FR` land instelling. |
| `gu-in`                     | Container installatie kopie met de `gu-IN` land instelling. |
| `hi-in`                     | Container installatie kopie met de `hi-IN` land instelling. |
| `it-it`                     | Container installatie kopie met de `it-IT` land instelling. |
| `ja-jp`                     | Container installatie kopie met de `ja-JP` land instelling. |
| `ko-kr`                     | Container installatie kopie met de `ko-KR` land instelling. |
| `mr-in`                     | Container installatie kopie met de `mr-IN` land instelling. |
| `nb-no`                     | Container installatie kopie met de `nb-NO` land instelling. |
| `nl-nl`                     | Container installatie kopie met de `nl-NL` land instelling. |
| `pl-pl`                     | Container installatie kopie met de `pl-PL` land instelling. |
| `pt-br`                     | Container installatie kopie met de `pt-BR` land instelling. |
| `pt-pt`                     | Container installatie kopie met de `pt-PT` land instelling. |
| `ru-ru`                     | Container installatie kopie met de `ru-RU` land instelling. |
| `sv-se`                     | Container installatie kopie met de `sv-SE` land instelling. |
| `ta-in`                     | Container installatie kopie met de `ta-IN` land instelling. |
| `te-in`                     | Container installatie kopie met de `te-IN` land instelling. |
| `th-th`                     | Container installatie kopie met de `th-TH` land instelling. |
| `tr-tr`                     | Container installatie kopie met de `tr-TR` land instelling. |
| `zh-cn`                     | Container installatie kopie met de `zh-CN` land instelling. |
| `zh-hk`                     | Container installatie kopie met de `zh-HK` land instelling. |
| `zh-tw`                     | Container installatie kopie met de `zh-TW` land instelling. |

---

## <a name="text-to-speech"></a>Tekst naar spraak

De [tekst-naar-spraak][sp-tts] container installatie kopie vindt u in het `mcr.microsoft.com` container register Syndicate. Deze bevindt zich in de `azure-cognitive-services/speechservices/` opslag plaats en heet `text-to-speech` . De volledig gekwalificeerde naam van de container installatie kopie is, `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` .

Voor deze container installatie kopie zijn de volgende tags beschikbaar. U kunt ook een volledige lijst met [Tags vinden op het MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list).


# <a name="latest-version"></a>[Nieuwste versie](#tab/current)

Release Opmerking voor `1.8.0-amd64-<locale-and-voice>` :

**Functie**
* Volledig gemigreerd naar .NET 3,1

| Afbeeldings Tags                                  | Opmerkingen                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | Container installatie kopie met de `en-US` land instellingen en de `en-US-AriaRUS` stem.                                            | 
| `1.8.0-amd64-<locale-and-voice>`            | Vervang door `<locale>` een van de beschik bare land instellingen, zoals hieronder wordt weer gegeven. Bijvoorbeeld `1.8.0-amd64-en-us-ariarus`.  |


| Land instellingen voor v 1.8.0                          | Opmerkingen                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Container installatie kopie met de `ar-EG` land instellingen en de `ar-EG-Hoda` stem.            |
| `ar-sa-naayf`                               | Container installatie kopie met de `ar-SA` land instellingen en de `ar-SA-Naayf` stem.           |
| `bg-bg-ivan`                                | Container installatie kopie met de `bg-BG` land instellingen en de `bg-BG-Ivan` stem.            |
| `ca-es-herenarus`                           | Container installatie kopie met de `ca-ES` land instellingen en de `ca-ES-HerenaRUS` stem.       |
| `cs-cz-jakub`                               | Container installatie kopie met de `cs-CZ` land instellingen en de `cs-CZ-Jakub` stem.           |
| `da-dk-hellerus`                            | Container installatie kopie met de `da-DK` land instellingen en de `da-DK-HelleRUS` stem.        |
| `de-at-michael`                             | Container installatie kopie met de `de-AT` land instellingen en de `de-AT-Michael` stem.         |
| `de-ch-karsten`                             | Container installatie kopie met de `de-CH` land instellingen en de `de-CH-Karsten` stem.         |
| `de-de-hedda`                               | Container installatie kopie met de `de-DE` land instellingen en de `de-DE-Hedda` stem.           |
| `de-de-heddarus`                            | Container installatie kopie met de `de-DE` land instellingen en de `de-DE-Hedda` stem.           |
| `de-de-stefan-apollo`                       | Container installatie kopie met de `de-DE` land instellingen en de `de-DE-Stefan-Apollo` stem.   |
| `el-gr-stefanos`                            | Container installatie kopie met de `el-GR` land instellingen en de `el-GR-Stefanos` stem.        |
| `en-au-catherine`                           | Container installatie kopie met de `en-AU` land instellingen en de `en-AU-Catherine` stem.       |
| `en-au-hayleyrus`                           | Container installatie kopie met de `en-AU` land instellingen en de `en-AU-HayleyRUS` stem.       |
| `en-ca-heatherrus`                          | Container installatie kopie met de `en-CA` land instellingen en de `en-CA-HeatherRUS` stem.      |
| `en-ca-linda`                               | Container installatie kopie met de `en-CA` land instellingen en de `en-CA-Linda` stem.           |
| `en-gb-george-apollo`                       | Container installatie kopie met de `en-GB` land instellingen en de `en-GB-George-Apollo` stem.   |
| `en-gb-hazelrus`                            | Container installatie kopie met de `en-GB` land instellingen en de `en-GB-HazelRUS` stem.        |
| `en-gb-susan-apollo`                        | Container installatie kopie met de `en-GB` land instellingen en de `en-GB-Susan-Apollo` stem.    |
| `en-ie-sean`                                | Container installatie kopie met de `en-IE` land instellingen en de `en-IE-Sean` stem.            |
| `en-in-heera-apollo`                        | Container installatie kopie met de `en-IN` land instellingen en de `en-IN-Heera-Apollo` stem.    |
| `en-in-priyarus`                            | Container installatie kopie met de `en-IN` land instellingen en de `en-IN-PriyaRUS` stem.        |
| `en-in-ravi-apollo`                         | Container installatie kopie met de `en-IN` land instellingen en de `en-IN-Ravi-Apollo` stem.     |
| `en-us-benjaminrus`                         | Container installatie kopie met de `en-US` land instellingen en de `en-US-BenjaminRUS` stem.     |
| `en-us-guy24krus`                           | Container installatie kopie met de `en-US` land instellingen en de `en-US-Guy24kRUS` stem.       |
| `en-us-aria24krus`                          | Container installatie kopie met de `en-US` land instellingen en de `en-US-Aria24kRUS` stem.      |
| `en-us-ariarus`                             | Container installatie kopie met de `en-US` land instellingen en de `en-US-AriaRUS` stem.         |
| `en-us-zirarus`                             | Container installatie kopie met de `en-US` land instellingen en de `en-US-ZiraRUS` stem.         |
| `es-es-helenarus`                           | Container installatie kopie met de `es-ES` land instellingen en de `es-ES-HelenaRUS` stem.       |
| `es-es-laura-apollo`                        | Container installatie kopie met de `es-ES` land instellingen en de `es-ES-Laura-Apollo` stem.    |
| `es-es-pablo-apollo`                        | Container installatie kopie met de `es-ES` land instellingen en de `es-ES-Pablo-Apollo` stem.    |
| `es-mx-hildarus`                            | Container installatie kopie met de `es-MX` land instellingen en de `es-MX-HildaRUS` stem.        |
| `es-mx-raul-apollo`                         | Container installatie kopie met de `es-MX` land instellingen en de `es-MX-Raul-Apollo` stem.     |
| `fi-fi-heidirus`                            | Container installatie kopie met de `fi-FI` land instellingen en de `fi-FI-HeidiRUS` stem.        |
| `fr-ca-caroline`                            | Container installatie kopie met de `fr-CA` land instellingen en de `fr-CA-Caroline` stem.        |
| `fr-ca-harmonierus`                         | Container installatie kopie met de `fr-CA` land instellingen en de `fr-CA-HarmonieRUS` stem.     |
| `fr-ch-guillaume`                           | Container installatie kopie met de `fr-CH` land instellingen en de `fr-CH-Guillaume` stem.       |
| `fr-fr-hortenserus`                         | Container installatie kopie met de `fr-FR` land instellingen en de `fr-FR-HortenseRUS` stem.     |
| `fr-fr-julie-apollo`                        | Container installatie kopie met de `fr-FR` land instellingen en de `fr-FR-Julie-Apollo` stem.    |
| `fr-fr-paul-apollo`                         | Container installatie kopie met de `fr-FR` land instellingen en de `fr-FR-Paul-Apollo` stem.     |
| `he-il-asaf`                                | Container installatie kopie met de `he-IL` land instellingen en de `he-IL-Asaf` stem.            |
| `hi-in-hemant`                              | Container installatie kopie met de `hi-IN` land instellingen en de `hi-IN-Hemant` stem.          |
| `hi-in-kalpana-apollo`                      | Container installatie kopie met de `hi-IN` land instellingen en de `hi-IN-Kalpana-Apollo` stem.  |
| `hi-in-kalpana`                             | Container installatie kopie met de `hi-IN` land instellingen en de `hi-IN-Kalpana` stem.         |
| `hr-hr-matej`                               | Container installatie kopie met de `hr-HR` land instellingen en de `hr-HR-Matej` stem.           |
| `hu-hu-szabolcs`                            | Container installatie kopie met de `hu-HU` land instellingen en de `hu-HU-Szabolcs` stem.        |
| `id-id-andika`                              | Container installatie kopie met de `id-ID` land instellingen en de `id-ID-Andika` stem.          |
| `it-it-cosimo-apollo`                       | Container installatie kopie met de `it-IT` land instellingen en de `it-IT-Cosimo-Apollo` stem.   |
| `it-it-luciarus`                            | Container installatie kopie met de `it-IT` land instellingen en de `it-IT-LuciaRUS` stem.        |
| `ja-jp-ayumi-apollo`                        | Container installatie kopie met de `ja-JP` land instellingen en de `ja-JP-Ayumi-Apollo` stem.    |
| `ja-jp-harukarus`                           | Container installatie kopie met de `ja-JP` land instellingen en de `ja-JP-HarukaRUS` stem.       |
| `ja-jp-ichiro-apollo`                       | Container installatie kopie met de `ja-JP` land instellingen en de `ja-JP-Ichiro-Apollo` stem.   |
| `ko-kr-heamirus`                            | Container installatie kopie met de `ko-KR` land instellingen en de `ko-KR-HeamiRUS` stem.        |
| `ms-my-rizwan`                              | Container installatie kopie met de `ms-MY` land instellingen en de `ms-MY-Rizwan` stem.          |
| `nb-no-huldarus`                            | Container installatie kopie met de `nb-NO` land instellingen en de `nb-NO-HuldaRUS` stem.        |
| `nl-nl-hannarus`                            | Container installatie kopie met de `nl-NL` land instellingen en de `nl-NL-HannaRUS` stem.        |
| `pl-pl-paulinarus`                          | Container installatie kopie met de `pl-PL` land instellingen en de `pl-PL-PaulinaRUS` stem.      |
| `pt-br-daniel-apollo`                       | Container installatie kopie met de `pt-BR` land instellingen en de `pt-BR-Daniel-Apollo` stem.   |
| `pt-br-heloisarus`                          | Container installatie kopie met de `pt-BR` land instellingen en de `pt-BR-HeloisaRUS` stem.      |
| `pt-pt-heliarus`                            | Container installatie kopie met de `pt-PT` land instellingen en de `pt-PT-HeliaRUS` stem.        |
| `ro-ro-andrei`                              | Container installatie kopie met de `ro-RO` land instellingen en de `ro-RO-Andrei` stem.          |
| `ru-ru-ekaterinarus`                        | Container installatie kopie met de `ru-RU` land instellingen en de `ru-RU-EkaterinaRUS` stem.    |
| `ru-ru-irina-apollo`                        | Container installatie kopie met de `ru-RU` land instellingen en de `ru-RU-Irina-Apollo` stem.    |
| `ru-ru-pavel-apollo`                        | Container installatie kopie met de `ru-RU` land instellingen en de `ru-RU-Pavel-Apollo` stem.    |
| `sk-sk-filip`                               | Container installatie kopie met de `sk-SK` land instellingen en de `sk-SK-Filip` stem.           |
| `sl-si-lado`                                | Container installatie kopie met de `sl-SI` land instellingen en de `sl-SI-Lado` stem.            |
| `sv-se-hedvigrus`                           | Container installatie kopie met de `sv-SE` land instellingen en de `sv-SE-HedvigRUS` stem.       |
| `ta-in-valluvar`                            | Container installatie kopie met de `ta-IN` land instellingen en de `ta-IN-Valluvar` stem.        |
| `te-in-chitra`                              | Container installatie kopie met de `te-IN` land instellingen en de `te-IN-Chitra` stem.          |
| `th-th-pattara`                             | Container installatie kopie met de `th-TH` land instellingen en de `th-TH-Pattara` stem.         |
| `tr-tr-sedarus`                             | Container installatie kopie met de `tr-TR` land instellingen en de `tr-TR-SedaRUS` stem.         |
| `vi-vn-an`                                  | Container installatie kopie met de `vi-VN` land instellingen en de `vi-VN-An` stem.              |
| `zh-cn-huihuirus`                           | Container installatie kopie met de `zh-CN` land instellingen en de `zh-CN-HuihuiRUS` stem.       |
| `zh-cn-kangkang-apollo`                     | Container installatie kopie met de `zh-CN` land instellingen en de `zh-CN-Kangkang-Apollo` stem. |
| `zh-cn-yaoyao-apollo`                       | Container installatie kopie met de `zh-CN` land instellingen en de `zh-CN-Yaoyao-Apollo` stem.   |
| `zh-hk-danny-apollo`                        | Container installatie kopie met de `zh-HK` land instellingen en de `zh-HK-Danny-Apollo` stem.    |
| `zh-hk-tracy-apollo`                        | Container installatie kopie met de `zh-HK` land instellingen en de `zh-HK-Tracy-Apollo` stem.    |
| `zh-hk-tracyrus`                            | Container installatie kopie met de `zh-HK` land instellingen en de `zh-HK-TracyRUS` stem.        |
| `zh-tw-hanhanrus`                           | Container installatie kopie met de `zh-TW` land instellingen en de `zh-TW-HanHanRUS` stem.       |
| `zh-tw-yating-apollo`                       | Container installatie kopie met de `zh-TW` land instellingen en de `zh-TW-Yating-Apollo` stem.   |
| `zh-tw-zhiwei-apollo`                       | Container installatie kopie met de `zh-TW` land instellingen en de `zh-TW-Zhiwei-Apollo` stem.   |


# <a name="previous-version"></a>[Vorige versie](#tab/previous)

Release Opmerking voor `1.7.0-amd64-<locale-and-voice>` :

**Functie**
* Bijgewerkte onderdelen naar .NET 3,1

| Afbeeldings Tags                                  | Opmerkingen                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.7.0-amd64-<locale-and-voice>`            | 1e GA-versie. Vervang door `<locale>` een van de beschik bare land instellingen, zoals hieronder wordt weer gegeven. Bijvoorbeeld `1.7.0-amd64-en-us-ariarus`.  |


| Land instellingen voor v 1.7.0                          | Opmerkingen                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Container installatie kopie met de `ar-EG` land instellingen en de `ar-EG-Hoda` stem.            |
| `ar-sa-naayf`                               | Container installatie kopie met de `ar-SA` land instellingen en de `ar-SA-Naayf` stem.           |
| `bg-bg-ivan`                                | Container installatie kopie met de `bg-BG` land instellingen en de `bg-BG-Ivan` stem.            |
| `ca-es-herenarus`                           | Container installatie kopie met de `ca-ES` land instellingen en de `ca-ES-HerenaRUS` stem.       |
| `cs-cz-jakub`                               | Container installatie kopie met de `cs-CZ` land instellingen en de `cs-CZ-Jakub` stem.           |
| `da-dk-hellerus`                            | Container installatie kopie met de `da-DK` land instellingen en de `da-DK-HelleRUS` stem.        |
| `de-at-michael`                             | Container installatie kopie met de `de-AT` land instellingen en de `de-AT-Michael` stem.         |
| `de-ch-karsten`                             | Container installatie kopie met de `de-CH` land instellingen en de `de-CH-Karsten` stem.         |
| `de-de-hedda`                               | Container installatie kopie met de `de-DE` land instellingen en de `de-DE-Hedda` stem.           |
| `de-de-heddarus`                            | Container installatie kopie met de `de-DE` land instellingen en de `de-DE-Hedda` stem.           |
| `de-de-stefan-apollo`                       | Container installatie kopie met de `de-DE` land instellingen en de `de-DE-Stefan-Apollo` stem.   |
| `el-gr-stefanos`                            | Container installatie kopie met de `el-GR` land instellingen en de `el-GR-Stefanos` stem.        |
| `en-au-catherine`                           | Container installatie kopie met de `en-AU` land instellingen en de `en-AU-Catherine` stem.       |
| `en-au-hayleyrus`                           | Container installatie kopie met de `en-AU` land instellingen en de `en-AU-HayleyRUS` stem.       |
| `en-ca-heatherrus`                          | Container installatie kopie met de `en-CA` land instellingen en de `en-CA-HeatherRUS` stem.      |
| `en-ca-linda`                               | Container installatie kopie met de `en-CA` land instellingen en de `en-CA-Linda` stem.           |
| `en-gb-george-apollo`                       | Container installatie kopie met de `en-GB` land instellingen en de `en-GB-George-Apollo` stem.   |
| `en-gb-hazelrus`                            | Container installatie kopie met de `en-GB` land instellingen en de `en-GB-HazelRUS` stem.        |
| `en-gb-susan-apollo`                        | Container installatie kopie met de `en-GB` land instellingen en de `en-GB-Susan-Apollo` stem.    |
| `en-ie-sean`                                | Container installatie kopie met de `en-IE` land instellingen en de `en-IE-Sean` stem.            |
| `en-in-heera-apollo`                        | Container installatie kopie met de `en-IN` land instellingen en de `en-IN-Heera-Apollo` stem.    |
| `en-in-priyarus`                            | Container installatie kopie met de `en-IN` land instellingen en de `en-IN-PriyaRUS` stem.        |
| `en-in-ravi-apollo`                         | Container installatie kopie met de `en-IN` land instellingen en de `en-IN-Ravi-Apollo` stem.     |
| `en-us-benjaminrus`                         | Container installatie kopie met de `en-US` land instellingen en de `en-US-BenjaminRUS` stem.     |
| `en-us-guy24krus`                           | Container installatie kopie met de `en-US` land instellingen en de `en-US-Guy24kRUS` stem.       |
| `en-us-aria24krus`                          | Container installatie kopie met de `en-US` land instellingen en de `en-US-Aria24kRUS` stem.      |
| `en-us-ariarus`                             | Container installatie kopie met de `en-US` land instellingen en de `en-US-AriaRUS` stem.         |
| `en-us-zirarus`                             | Container installatie kopie met de `en-US` land instellingen en de `en-US-ZiraRUS` stem.         |
| `es-es-helenarus`                           | Container installatie kopie met de `es-ES` land instellingen en de `es-ES-HelenaRUS` stem.       |
| `es-es-laura-apollo`                        | Container installatie kopie met de `es-ES` land instellingen en de `es-ES-Laura-Apollo` stem.    |
| `es-es-pablo-apollo`                        | Container installatie kopie met de `es-ES` land instellingen en de `es-ES-Pablo-Apollo` stem.    |
| `es-mx-hildarus`                            | Container installatie kopie met de `es-MX` land instellingen en de `es-MX-HildaRUS` stem.        |
| `es-mx-raul-apollo`                         | Container installatie kopie met de `es-MX` land instellingen en de `es-MX-Raul-Apollo` stem.     |
| `fi-fi-heidirus`                            | Container installatie kopie met de `fi-FI` land instellingen en de `fi-FI-HeidiRUS` stem.        |
| `fr-ca-caroline`                            | Container installatie kopie met de `fr-CA` land instellingen en de `fr-CA-Caroline` stem.        |
| `fr-ca-harmonierus`                         | Container installatie kopie met de `fr-CA` land instellingen en de `fr-CA-HarmonieRUS` stem.     |
| `fr-ch-guillaume`                           | Container installatie kopie met de `fr-CH` land instellingen en de `fr-CH-Guillaume` stem.       |
| `fr-fr-hortenserus`                         | Container installatie kopie met de `fr-FR` land instellingen en de `fr-FR-HortenseRUS` stem.     |
| `fr-fr-julie-apollo`                        | Container installatie kopie met de `fr-FR` land instellingen en de `fr-FR-Julie-Apollo` stem.    |
| `fr-fr-paul-apollo`                         | Container installatie kopie met de `fr-FR` land instellingen en de `fr-FR-Paul-Apollo` stem.     |
| `he-il-asaf`                                | Container installatie kopie met de `he-IL` land instellingen en de `he-IL-Asaf` stem.            |
| `hi-in-hemant`                              | Container installatie kopie met de `hi-IN` land instellingen en de `hi-IN-Hemant` stem.          |
| `hi-in-kalpana-apollo`                      | Container installatie kopie met de `hi-IN` land instellingen en de `hi-IN-Kalpana-Apollo` stem.  |
| `hi-in-kalpana`                             | Container installatie kopie met de `hi-IN` land instellingen en de `hi-IN-Kalpana` stem.         |
| `hr-hr-matej`                               | Container installatie kopie met de `hr-HR` land instellingen en de `hr-HR-Matej` stem.           |
| `hu-hu-szabolcs`                            | Container installatie kopie met de `hu-HU` land instellingen en de `hu-HU-Szabolcs` stem.        |
| `id-id-andika`                              | Container installatie kopie met de `id-ID` land instellingen en de `id-ID-Andika` stem.          |
| `it-it-cosimo-apollo`                       | Container installatie kopie met de `it-IT` land instellingen en de `it-IT-Cosimo-Apollo` stem.   |
| `it-it-luciarus`                            | Container installatie kopie met de `it-IT` land instellingen en de `it-IT-LuciaRUS` stem.        |
| `ja-jp-ayumi-apollo`                        | Container installatie kopie met de `ja-JP` land instellingen en de `ja-JP-Ayumi-Apollo` stem.    |
| `ja-jp-harukarus`                           | Container installatie kopie met de `ja-JP` land instellingen en de `ja-JP-HarukaRUS` stem.       |
| `ja-jp-ichiro-apollo`                       | Container installatie kopie met de `ja-JP` land instellingen en de `ja-JP-Ichiro-Apollo` stem.   |
| `ko-kr-heamirus`                            | Container installatie kopie met de `ko-KR` land instellingen en de `ko-KR-HeamiRUS` stem.        |
| `ms-my-rizwan`                              | Container installatie kopie met de `ms-MY` land instellingen en de `ms-MY-Rizwan` stem.          |
| `nb-no-huldarus`                            | Container installatie kopie met de `nb-NO` land instellingen en de `nb-NO-HuldaRUS` stem.        |
| `nl-nl-hannarus`                            | Container installatie kopie met de `nl-NL` land instellingen en de `nl-NL-HannaRUS` stem.        |
| `pl-pl-paulinarus`                          | Container installatie kopie met de `pl-PL` land instellingen en de `pl-PL-PaulinaRUS` stem.      |
| `pt-br-daniel-apollo`                       | Container installatie kopie met de `pt-BR` land instellingen en de `pt-BR-Daniel-Apollo` stem.   |
| `pt-br-heloisarus`                          | Container installatie kopie met de `pt-BR` land instellingen en de `pt-BR-HeloisaRUS` stem.      |
| `pt-pt-heliarus`                            | Container installatie kopie met de `pt-PT` land instellingen en de `pt-PT-HeliaRUS` stem.        |
| `ro-ro-andrei`                              | Container installatie kopie met de `ro-RO` land instellingen en de `ro-RO-Andrei` stem.          |
| `ru-ru-ekaterinarus`                        | Container installatie kopie met de `ru-RU` land instellingen en de `ru-RU-EkaterinaRUS` stem.    |
| `ru-ru-irina-apollo`                        | Container installatie kopie met de `ru-RU` land instellingen en de `ru-RU-Irina-Apollo` stem.    |
| `ru-ru-pavel-apollo`                        | Container installatie kopie met de `ru-RU` land instellingen en de `ru-RU-Pavel-Apollo` stem.    |
| `sk-sk-filip`                               | Container installatie kopie met de `sk-SK` land instellingen en de `sk-SK-Filip` stem.           |
| `sl-si-lado`                                | Container installatie kopie met de `sl-SI` land instellingen en de `sl-SI-Lado` stem.            |
| `sv-se-hedvigrus`                           | Container installatie kopie met de `sv-SE` land instellingen en de `sv-SE-HedvigRUS` stem.       |
| `ta-in-valluvar`                            | Container installatie kopie met de `ta-IN` land instellingen en de `ta-IN-Valluvar` stem.        |
| `te-in-chitra`                              | Container installatie kopie met de `te-IN` land instellingen en de `te-IN-Chitra` stem.          |
| `th-th-pattara`                             | Container installatie kopie met de `th-TH` land instellingen en de `th-TH-Pattara` stem.         |
| `tr-tr-sedarus`                             | Container installatie kopie met de `tr-TR` land instellingen en de `tr-TR-SedaRUS` stem.         |
| `vi-vn-an`                                  | Container installatie kopie met de `vi-VN` land instellingen en de `vi-VN-An` stem.              |
| `zh-cn-huihuirus`                           | Container installatie kopie met de `zh-CN` land instellingen en de `zh-CN-HuihuiRUS` stem.       |
| `zh-cn-kangkang-apollo`                     | Container installatie kopie met de `zh-CN` land instellingen en de `zh-CN-Kangkang-Apollo` stem. |
| `zh-cn-yaoyao-apollo`                       | Container installatie kopie met de `zh-CN` land instellingen en de `zh-CN-Yaoyao-Apollo` stem.   |
| `zh-hk-danny-apollo`                        | Container installatie kopie met de `zh-HK` land instellingen en de `zh-HK-Danny-Apollo` stem.    |
| `zh-hk-tracy-apollo`                        | Container installatie kopie met de `zh-HK` land instellingen en de `zh-HK-Tracy-Apollo` stem.    |
| `zh-hk-tracyrus`                            | Container installatie kopie met de `zh-HK` land instellingen en de `zh-HK-TracyRUS` stem.        |
| `zh-tw-hanhanrus`                           | Container installatie kopie met de `zh-TW` land instellingen en de `zh-TW-HanHanRUS` stem.       |
| `zh-tw-yating-apollo`                       | Container installatie kopie met de `zh-TW` land instellingen en de `zh-TW-Yating-Apollo` stem.   |
| `zh-tw-zhiwei-apollo`                       | Container installatie kopie met de `zh-TW` land instellingen en de `zh-TW-Zhiwei-Apollo` stem.   |

---

## <a name="neural-text-to-speech"></a>Tekst-naar-spraak Neural

De [Neural tekst naar spraak][sp-ntts] container installatie kopie vindt u in het `mcr.microsoft.com` container register Syndicate. Deze bevindt zich in de `azure-cognitive-services/speechservices/` opslag plaats en heet `neural-text-to-speech` . De volledig gekwalificeerde naam van de container installatie kopie is, `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` .

Voor deze container installatie kopie zijn de volgende tags beschikbaar. U kunt ook een volledige lijst met [Tags vinden op het MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list).


# <a name="latest-version"></a>[Nieuwste versie](#tab/current)

Release opmerkingen voor `v1.3.0` :
* De Neural tekst-naar-spraak-container is nu algemeen beschikbaar. 

| Afbeeldings Tags                                  | Opmerkingen                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Container installatie kopie met de `en-US` land instellingen en de `en-US-AriaNeural` stem.      |
| `1.3.0-amd64-<locale-and-voice>`    | Vervang door `<locale>` een van de beschik bare land instellingen, zoals hieronder wordt weer gegeven. Bijvoorbeeld `1.3.0-amd64-en-us-arianeural`. |


| v 1.3.0 land instellingen en stemmen           | Opmerkingen                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | Container installatie kopie met de `de-DE` land instellingen en de `de-DE-KatjaNeural` stem.     |
| `en-au-natashaneural`               | Container installatie kopie met de `en-AU` land instellingen en de `en-AU-NatashaNeural` stem.   |
| `en-ca-claraneural`                 | Container installatie kopie met de `en-CA` land instellingen en de `en-CA-ClaraNeural` stem.     |
| `en-gb-libbyneural`                 | Container installatie kopie met de `en-GB` land instellingen en de `en-GB-LibbyNeural` stem.     |
| `en-gb-mianeural`                   | Container installatie kopie met de `en-GB` land instellingen en de `en-GB-MiaNeural` stem.       |
| `en-us-arianeural`                  | Container installatie kopie met de `en-US` land instellingen en de `en-US-AriaNeural` stem.      |
| `en-us-guyneural`                   | Container installatie kopie met de `en-US` land instellingen en de `en-US-GuyNeural` stem.       |
| `es-es-elviraneural`                | Container installatie kopie met de `es-ES` land instellingen en de `es-ES-ElviraNeural` stem.    |
| `es-mx-dalianeural`                 | Container installatie kopie met de `es-MX` land instellingen en de `es-MX-DaliaNeural` stem.     |
| `fr-ca-sylvieneural`                | Container installatie kopie met de `fr-CA` land instellingen en de `fr-CA-SylvieNeural` stem.    |
| `fr-fr-deniseneural`                | Container installatie kopie met de `fr-FR` land instellingen en de `fr-FR-DeniseNeural` stem.    |
| `it-it-elsaneural`                  | Container installatie kopie met de `it-IT` land instellingen en de `it-IT-ElsaNeural` stem.      |
| `ja-jp-nanamineural`                | Container installatie kopie met de `ja-JP` land instellingen en de `ja-JP-NanamiNeural` stem.    |
| `ko-kr-sunhineural`                 | Container installatie kopie met de `ko-KR` land instellingen en de `ko-KR-SunHiNeural` stem.     |
| `pt-br-franciscaneural`             | Container installatie kopie met de `pt-BR` land instellingen en de `pt-BR-FranciscaNeural` stem. |
| `zh-cn-xiaoxiaoneural`              | Container installatie kopie met de `zh-CN` land instellingen en de `zh-CN-XiaoxiaoNeural` stem.  |

# <a name="previous-version"></a>[Vorige versie](#tab/previous)

| Afbeeldings Tags                                  | Opmerkingen                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Container installatie kopie met de `en-US` land instellingen en de `en-US-AriaNeural` stem.      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | Vervang door `<locale>` een van de beschik bare land instellingen, zoals hieronder wordt weer gegeven. Bijvoorbeeld `1.2.0-amd64-en-us-arianeural-preview`. |


| v 1.2.0-voor beeld-land instellingen en stemmen           | Opmerkingen                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Container installatie kopie met de `en-US` land instellingen en de `en-US-AriaNeural` stem.      |
| `de-de-katjaneural-preview`                 | Container installatie kopie met de `de-DE` land instellingen en de `de-DE-KatjaNeural` stem.     |
| `en-au-natashaneural-preview`               | Container installatie kopie met de `en-AU` land instellingen en de `en-AU-NatashaNeural` stem.   |
| `en-ca-claraneural-preview`                 | Container installatie kopie met de `en-CA` land instellingen en de `en-CA-ClaraNeural` stem.     |
| `en-gb-libbyneural-preview`                 | Container installatie kopie met de `en-GB` land instellingen en de `en-GB-LibbyNeural` stem.     |
| `en-gb-mianeural-preview`                   | Container installatie kopie met de `en-GB` land instellingen en de `en-GB-MiaNeural` stem.       |
| `en-us-arianeural-preview`                  | Container installatie kopie met de `en-US` land instellingen en de `en-US-AriaNeural` stem.      |
| `en-us-guyneural-preview`                   | Container installatie kopie met de `en-US` land instellingen en de `en-US-GuyNeural` stem.       |
| `es-es-elviraneural-preview`                | Container installatie kopie met de `es-ES` land instellingen en de `es-ES-ElviraNeural` stem.    |
| `es-mx-dalianeural-preview`                 | Container installatie kopie met de `es-MX` land instellingen en de `es-MX-DaliaNeural` stem.     |
| `fr-ca-sylvieneural-preview`                | Container installatie kopie met de `fr-CA` land instellingen en de `fr-CA-SylvieNeural` stem.    |
| `fr-fr-deniseneural-preview`                | Container installatie kopie met de `fr-FR` land instellingen en de `fr-FR-DeniseNeural` stem.    |
| `it-it-elsaneural-preview`                  | Container installatie kopie met de `it-IT` land instellingen en de `it-IT-ElsaNeural` stem.      |
| `ja-jp-nanamineural-preview`                | Container installatie kopie met de `ja-JP` land instellingen en de `ja-JP-NanamiNeural` stem.    |
| `ko-kr-sunhineural-preview`                 | Container installatie kopie met de `ko-KR` land instellingen en de `ko-KR-SunHiNeural` stem.     |
| `pt-br-franciscaneural-preview`             | Container installatie kopie met de `pt-BR` land instellingen en de `pt-BR-FranciscaNeural` stem. |
| `zh-cn-xiaoxiaoneural-preview`              | Container installatie kopie met de `zh-CN` land instellingen en de `zh-CN-XiaoxiaoNeural` stem.  |

---

## <a name="speech-language-detection"></a>Taaldetectie van spraak

De container installatie kopie voor de [spraak taal detectie][sp-lid] kan worden gevonden in het `mcr.microsoft.com` container register Syndicate. Deze bevindt zich in de `azure-cognitive-services/speechservices/` opslag plaats en heet `language-detection` . De volledig gekwalificeerde naam van de container installatie kopie is, `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` .

Voor deze container installatie kopie zijn de volgende tags beschikbaar. U kunt ook een volledige lijst met [Tags vinden op het MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list).

| Afbeeldings Tags                                  | Opmerkingen                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>Sleuteltermextractie

container installatie kopie vindt u in het `mcr.microsoft.com` container register Syndicate. Deze bevindt zich in de `azure-cognitive-services/textanalytics/` opslag plaats en heet `keyphrase` . De volledig gekwalificeerde naam van de container installatie kopie is, `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` .

Voor deze container installatie kopie zijn de volgende tags beschikbaar. U kunt ook een volledige lijst met [Tags vinden op het MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list).

# <a name="latest-version"></a>[Nieuwste versie](#tab/current)


| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[Vorige versies](#tab/previous)

| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>Taaldetectie van tekst

De [taaldetectie][ta-la] container installatie kopie vindt u in het `mcr.microsoft.com` container register Syndicate. Deze bevindt zich in de `azure-cognitive-services/textanalytics/` opslag plaats en heet `language` . De volledig gekwalificeerde naam van de container installatie kopie is, `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


Voor deze container installatie kopie zijn de volgende tags beschikbaar. U kunt ook een volledige lijst met [Tags vinden op het MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list).

# <a name="latest-versions"></a>[Nieuwste versies](#tab/current)

| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[Vorige versies](#tab/previous)


| Afbeeldings Tags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>Sentimentanalyse

De [sentimentanalyse][ta-se] container installatie kopie vindt u in het `mcr.microsoft.com` container register Syndicate. Deze bevindt zich in de `azure-cognitive-services/textanalytics/` opslag plaats en heet `sentiment` . De volledig gekwalificeerde naam van de container installatie kopie is, `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

Voor deze container installatie kopie zijn de volgende tags beschikbaar. U kunt ook een volledige lijst met [Tags vinden op het MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list).

| Afbeeldings Tags | Opmerkingen                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Sentimentanalyse v3 (Engels)               |
| `3.0-es`   | Sentimentanalyse v3 (Spaans)               |
| `3.0-fr`   | Sentimentanalyse v3 (Frans)                |
| `3.0-it`   | Sentimentanalyse v3 (Italiaans)               |
| `3.0-de`   | Sentimentanalyse v3 (Duits)                |
| `3.0-zh`   | Sentimentanalyse v3 (Chinees-vereenvoudigd)  |
| `3.0-zht`  | Sentimentanalyse v3 (Chinees-traditioneel) |
| `3.0-ja`   | Sentimentanalyse v3 (Japans)              |
| `3.0-pt`   | Sentimentanalyse v3 (Portugees)            |
| `3.0-nl`   | Sentimentanalyse v3 (Nederlands)                 |
| `2.1`    | Sentimentanalyse v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
