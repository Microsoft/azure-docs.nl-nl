---
title: De Tekstanalyse-API aanroepen
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de Azure Cognitive Services Text Analytics REST API en postman kunt aanroepen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: bf53ce5ed3f9505572538533263f0d17c5dcbf45
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562562"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>De Text Analytics aanroepen REST API

In dit artikel gebruiken we de Text Analytics REST API en [postman](https://www.postman.com/downloads/) om de belangrijkste concepten te demonstreren. De API biedt verschillende synchrone en asynchrone eind punten voor het gebruik van de functies van de service. 

## <a name="create-a-text-analytics-resource"></a>Een Text Analytics-resource maken

> [!NOTE]
> * Als u de eind punten wilt gebruiken, hebt u een Text Analytics resource met een [prijs categorie](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) Standard (S) nodig `/analyze` `/health` . Het `/analyze` eind punt is opgenomen in uw [prijs categorie](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

Voordat u de Text Analytics-API gebruikt, moet u een Azure-resource maken met een sleutel en een eind punt voor uw toepassingen. 

1.  Ga eerst naar de [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) en maak een nieuwe Text Analytics resource als u er nog geen hebt. Kies een [prijs categorie](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

2.  Selecteer de regio die u voor het eind punt wilt gebruiken.  Houd er rekening mee dat de `/analyze` `/health` eind punten alleen beschikbaar zijn in de volgende REGIO'S: VS-West 2, VS-Oost 2, VS-midden, Europa-noord en Europa-West.

3.  Maak de Text Analytics resource en ga naar de Blade sleutels en eind punt aan de linkerkant van de pagina. Kopieer de sleutel die u later wilt gebruiken wanneer u de Api's aanroept. U voegt dit later toe als waarde voor de `Ocp-Apim-Subscription-Key` koptekst.

## <a name="using-the-api-synchronously"></a>De API synchroon gebruiken

U kunt Text Analytics synchroon aanroepen (voor scenario's met lage latentie). U moet elke API (functie) afzonderlijk aanroepen wanneer u synchrone API gebruikt. Als u meerdere functies moet aanroepen, raadpleegt u de sectie over het asynchroon aanroepen van Text Analytics. 

## <a name="using-the-api-asynchronously"></a>De API asynchroon gebruiken

Vanaf v 3.1-Preview. 3 biedt de Text Analytics-API twee asynchrone eind punten: 

* `/analyze`Met het eind punt voor Text Analytics kunt u dezelfde set tekst documenten met meerdere tekst analyse functies in één API-aanroep analyseren. Voor het gebruik van meerdere functies moet u echter voor elke bewerking afzonderlijke API-aanroepen maken. Houd rekening met deze mogelijkheid wanneer u grote sets documenten wilt analyseren met meer dan één Text Analytics-functie.

* Het `/health` eind punt voor Text Analytics status, waarmee relevante medische gegevens uit klinische documenten kunnen worden opgehaald en gelabeld.  

Zie de onderstaande tabel om te zien welke functies asynchroon kunnen worden gebruikt. Houd er rekening mee dat er slechts enkele functies kunnen worden aangeroepen vanuit het `/analyze` eind punt. 

| Functie | Synchroon | Asynchroon |
|--|--|--|
| Taaldetectie | ✔ |  |
| Sentimentanalyse | ✔ |  |
| Meninganalyse | ✔ |  |
| Sleuteltermextractie | ✔ | ✔* |
| Herkenning van benoemde entiteiten (inclusief PII en PHI) | ✔ | ✔* |
| Text Analytics voor status (container) | ✔ |  |
| Text Analytics voor status (API) |  | ✔  |

`*` -Wordt asynchroon aangeroepen door het `/analyze` eind punt.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]


<a name="json-schema"></a>

## <a name="api-request-formats"></a>Indelingen van API-aanvragen

U kunt zowel synchrone als asynchrone aanroepen naar de Text Analytics-API verzenden.

#### <a name="synchronous"></a>[Synchroon](#tab/synchronous)

### <a name="synchronous-requests"></a>Synchrone aanvragen

De indeling voor API-aanvragen is hetzelfde voor alle synchrone bewerkingen. Documenten worden in een JSON-object ingediend als onbewerkte ongestructureerde tekst. XML wordt niet ondersteund. Het JSON-schema bestaat uit de elementen die hieronder worden beschreven.

| Element | Geldige waarden | Vereist? | Gebruik |
|---------|--------------|-----------|-------|
|`id` |Het gegevens type is teken reeks, maar in oefen document-Id's zijn meestal gehele getallen. | Vereist | Het systeem gebruikt de Id's die u opgeeft om de uitvoer te structureren. Taal codes, sleutel zinnen en sentiment-scores worden gegenereerd voor elke ID in de aanvraag.|
|`text` | Ongestructureerde onbewerkte tekst, Maxi maal 5.120 tekens. | Vereist | Voor taal detectie kan tekst in elke taal worden weer gegeven. Voor sentiment analyse, extractie van sleutel zinnen en Entiteits-ID moet de tekst in een [ondersteunde taal](../language-support.md)worden gesteld. |
|`language` | [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) -code van 2 tekens voor een [ondersteunde taal](../language-support.md) | Varieert | Vereist voor sentiment analyse, extractie van sleutel zinnen en entiteits koppeling; optioneel voor taal detectie. Er is geen fout als u deze uitsluit, maar de analyse verzwakt. De taal code moet overeenkomen met de `text` die u opgeeft. |

Hier volgt een voor beeld van een API-aanvraag voor de synchrone Text Analytics-eind punten. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="asynchronous"></a>[Asynchroon](#tab/asynchronous)

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>Asynchrone aanvragen voor het `/analyze` eind punt

> [!NOTE]
> Met de nieuwste Prerelease van de Text Analytics-client bibliotheek kunt u asynchrone analyse bewerkingen aanroepen met behulp van een client object. U kunt voor beelden vinden op GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

Met het `/analyze` eind punt kunt u kiezen welke van de ondersteunde Text Analytics functies die u in één API-aanroep wilt gebruiken. Dit eind punt ondersteunt momenteel:

* sleuteltermextractie 
* Herkenning van benoemde entiteiten (inclusief PII en PHI)

| Element | Geldige waarden | Vereist? | Gebruik |
|---------|--------------|-----------|-------|
|`displayName` | Tekenreeks | Optioneel | Wordt gebruikt als de weergave naam voor de unieke id voor de taak.|
|`analysisInput` | Bevat onderstaand `documents` veld | Vereist | Bevat de informatie voor de documenten die u wilt verzenden. |
|`documents` | Bevat de `id` `text` velden en onder | Vereist | Bevat informatie voor elk document dat wordt verzonden en de onbewerkte tekst van het document. |
|`id` | Tekenreeks | Vereist | De Id's die u opgeeft, worden gebruikt voor het structureren van de uitvoer. |
|`text` | Ongestructureerde onbewerkte tekst, Maxi maal 125.000 tekens. | Vereist | Moet in de Engelse taal worden gesteld. Dit is de enige taal die momenteel wordt ondersteund. |
|`tasks` | Bevat de volgende Text Analytics functies: `entityRecognitionTasks` , `keyPhraseExtractionTasks` of `entityRecognitionPiiTasks` . | Vereist | Een of meer van de Text Analytics functies die u wilt gebruiken. Houd er rekening mee dat `entityRecognitionPiiTasks` een optionele `domain` para meter heeft die kan worden ingesteld op `pii` of `phi` . Als u geen waarde opgeeft, wordt het systeem standaard ingesteld op `pii` . |
|`parameters` | Bevat de `model-version` `stringIndexType` velden en onder | Vereist | Dit veld is opgenomen in de bovenstaande functie taken die u kiest. Ze bevatten informatie over de model versie die u wilt gebruiken en het index type. |
|`model-version` | Tekenreeks | Vereist | Geef op welke versie van het model moet worden aangeroepen dat u wilt gebruiken.  |
|`stringIndexType` | Tekenreeks | Vereist | Geef de tekst decoder op die overeenkomt met uw programmeer omgeving.  Typen die worden ondersteund zijn `textElement_v8` (standaard), `unicodeCodePoint` , `utf16CodeUnit` . Raadpleeg het [artikel over verschuivingen van tekst](../concepts/text-offsets.md#offsets-in-api-version-31-preview) voor meer informatie.  |
|`domain` | Tekenreeks | Optioneel | Is alleen van toepassing als een para meter voor de `entityRecognitionPiiTasks` taak en kan worden ingesteld op `pii` of `phi` . De standaard instelling is `pii` als niet opgegeven.  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

### <a name="asynchronous-requests-to-the-health-endpoint"></a>Asynchrone aanvragen voor het `/health` eind punt

De indeling voor API-aanvragen voor de Text Analytics voor de status-gehoste API is hetzelfde als voor de bijbehorende container. Documenten worden in een JSON-object ingediend als onbewerkte ongestructureerde tekst. XML wordt niet ondersteund. Het JSON-schema bestaat uit de elementen die hieronder worden beschreven.  Vul het [Cognitive Services aanvraag formulier](https://aka.ms/csgate) in om toegang tot de Text Analytics voor de open bare preview van de status aan te vragen. U wordt niet gefactureerd voor Text Analytics voor het status gebruik. 

| Element | Geldige waarden | Vereist? | Gebruik |
|---------|--------------|-----------|-------|
|`id` |Het gegevens type is teken reeks, maar in oefen document-Id's zijn meestal gehele getallen. | Vereist | Het systeem gebruikt de Id's die u opgeeft om de uitvoer te structureren. |
|`text` | Ongestructureerde onbewerkte tekst, Maxi maal 5.120 tekens. | Vereist | Houd er rekening mee dat er op dit moment alleen Engelse tekst wordt ondersteund. |
|`language` | [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) -code van 2 tekens voor een [ondersteunde taal](../language-support.md) | Vereist | `en`Wordt momenteel alleen ondersteund. |

Hier volgt een voor beeld van een API-aanvraag voor de Text Analytics voor status eindpunten. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Zie het artikel [gegevens-en frequentie limieten](../concepts/data-limits.md) voor informatie over de tarieven en grootte limieten voor het verzenden van gegevens naar de Text Analytics-API.


## <a name="set-up-a-request"></a>Een aanvraag instellen 

Voeg in Postman (of een ander web API-test hulpprogramma) het eind punt toe voor de functie die u wilt gebruiken. Gebruik de onderstaande tabel om de juiste eindpunt indeling te vinden en vervang door `<your-text-analytics-resource>` het resource-eind punt. Bijvoorbeeld:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Synchroon](#tab/synchronous)

### <a name="endpoints-for-sending-synchronous-requests"></a>Eind punten voor het verzenden van synchrone aanvragen

| Functie | Soort aanvraag | Resource-eind punten |
|--|--|--|
| Taaldetectie | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Sentimentanalyse | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Meninganalyse | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Sleuteltermextractie | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Herkenning van benoemde entiteiten-algemeen | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Herkenning van benoemde entiteiten-PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Herkenning van benoemde entiteiten-PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="asynchronous"></a>[Asynchroon](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>Eind punten voor het verzenden van asynchrone aanvragen naar het `/analyze` eind punt

| Functie | Soort aanvraag | Resource-eind punten |
|--|--|--|
| Analyse taak verzenden | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| Status en resultaten van de analyse ophalen | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>Eind punten voor het verzenden van asynchrone aanvragen naar het `/health` eind punt

| Functie | Soort aanvraag | Resource-eind punten |
|--|--|--|
| Text Analytics verzenden voor status taak  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| Taak status en-resultaten ophalen | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| Taak annuleren | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

Nadat u uw eind punt hebt, in Postman (of een ander web-API-test programma):

1. Kies het aanvraag type voor de functie die u wilt gebruiken.
2. Plak het eind punt van de juiste bewerking die u wilt uit de bovenstaande tabel.
3. Stel de drie aanvraag headers in:

   + `Ocp-Apim-Subscription-Key`: uw toegangs sleutel, verkregen van Azure Portal
   + `Content-Type`: toepassing/JSON
   + `Accept`: toepassing/JSON

    Als u postman gebruikt, moet uw aanvraag er ongeveer uitzien als de volgende scherm afbeelding, uitgaande van een `/keyPhrases` eind punt.
    
    ![Scherm opname van aanvraag met eind punt en kopteksten](../media/postman-request-keyphrase-1.png)
    
4. Kies **RAW** voor de indeling van de **hoofd tekst**
    
    ![Scherm opname van aanvraag met hoofdtekst instellingen](../media/postman-request-body-raw.png)

5. Plak een aantal JSON-documenten in een geldige indeling. Gebruik de voor beelden in de sectie **indeling van API-aanvraag** hierboven en zie de onderstaande onderwerpen voor meer informatie en voor beelden:

      + [Taaldetectie](text-analytics-how-to-language-detection.md)
      + [Sleuteltermextractie](text-analytics-how-to-keyword-extraction.md)
      + [Sentimentanalyse](text-analytics-how-to-sentiment-analysis.md)
      + [Herkenning van entiteiten](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>De aanvraag verzenden

Verzend de API-aanvraag. Als u de aanroep naar een synchroon eind punt hebt gemaakt, wordt het antwoord onmiddellijk weer gegeven als één JSON-document met een item voor elke document-ID die in de aanvraag is opgegeven.

Als u de asynchrone `/analyze` of eind punten hebt aangeroepen `/health` , controleert u of u een 202-respons code hebt ontvangen. u moet het antwoord ophalen om de resultaten weer te geven:

1. Zoek in de API-reactie de `Operation-Location` uit de header, waarmee de taak wordt geïdentificeerd die u naar de API hebt verzonden. 
2. Maak een GET-aanvraag voor het eind punt dat u hebt gebruikt. Raadpleeg de [bovenstaande tabel](#set-up-a-request) voor de indeling van het eind punt en lees de [API-referentie documentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Bijvoorbeeld:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. Voeg het `Operation-Location` toe aan de aanvraag.

4. Het antwoord is één JSON-document met een item voor elke document-ID die in de aanvraag is gegeven.

Voor zowel asynchroon `/analyze` als `/health` bewerkingen zijn de resultaten van de GET-aanvraag in stap 2 hierboven 24 uur beschikbaar vanaf het moment waarop de taak is gemaakt.  Deze tijd wordt aangegeven door de `expirationDateTime` waarde in het antwoord ophalen.  Na deze periode worden de resultaten opgeschoond en zijn deze niet meer beschikbaar voor ophalen.    

## <a name="example-api-responses"></a>Voor beeld van API-antwoorden
 
# <a name="synchronous"></a>[Synchroon](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>Voorbeeld reacties voor synchrone bewerking

De synchrone eindpunt reacties variëren, afhankelijk van het eind punt dat u gebruikt. Raadpleeg de volgende artikelen voor voorbeeld reacties.

+ [Taaldetectie](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Sleuteltermextractie](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Sentimentanalyse](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Herkenning van entiteiten](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[Asynchroon](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>Voorbeeld reacties voor asynchrone bewerkingen

Als dit lukt, wordt de GET-aanvraag voor het `/analyze` eind punt een object geretourneerd dat de toegewezen taken bevat. Bijvoorbeeld `keyPhraseExtractionTasks`. Deze taken bevatten het antwoord object van de juiste Text Analytics-functie. Raadpleeg de volgende artikelen voor meer informatie.

+ [Sleuteltermextractie](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Herkenning van entiteiten](text-analytics-how-to-entity-linking.md#view-results)
+ [Text Analytics voor status](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>Zie ook

* [Overzicht van Text Analytics](../overview.md)
* [Veelgestelde vragen](../text-analytics-resource-faq.md)</br>
* [Text Analytics-productpagina](//go.microsoft.com/fwlink/?LinkID=759712)
* [De Text Analytics-clientbibliotheek gebruiken](../quickstarts/client-libraries-rest-api.md)
* [Nieuwe functies](../whats-new.md)
