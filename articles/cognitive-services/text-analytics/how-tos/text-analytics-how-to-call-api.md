---
title: De Tekstanalyse-API aanroepen
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de Azure Cognitive Services Text Analytics REST API en Postman kunt aanroepen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 5790c7c62b9d97df9683773170301b6e09a47667
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728478"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>De Text Analytics REST API

In dit artikel gebruiken we de Text Analytics REST API [en Postman om](https://www.postman.com/downloads/) de belangrijkste concepten te demonstreren. De API biedt verschillende synchrone en asynchrone eindpunten voor het gebruik van de functies van de service. 

## <a name="create-a-text-analytics-resource"></a>Een Text Analytics-resource maken

> [!NOTE]
> * U hebt een Text Analytics resource met een Prijscategorie Standard [(S)](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) nodig als u de `/analyze` eindpunten of wilt `/health` gebruiken. Het `/analyze` eindpunt is opgenomen in uw [prijscategorie](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

Voordat u de Text Analytics API gebruikt, moet u een Azure-resource maken met een sleutel en eindpunt voor uw toepassingen. 

1.  Ga eerst naar de [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) en maak een nieuwe Text Analytics resource als u er nog geen hebt. Kies een [prijscategorie](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

2.  Selecteer de regio die u wilt gebruiken voor uw eindpunt.  Houd er rekening mee dat de eindpunten en alleen beschikbaar zijn in de volgende regio's: VS - west 2, VS - oost 2, VS - `/analyze` `/health` centraal, Europa - noord en Europa - west.

3.  Maak de Text Analytics resource en ga naar de blade Sleutels en eindpunt links op de pagina. Kopieer de sleutel die later moet worden gebruikt wanneer u de API's aanroept. U voegt deze later toe als een waarde voor de `Ocp-Apim-Subscription-Key` header.

4. Ga als volgende te werk om het aantal tekstrecords te controleren dat is verzonden met behulp van Text Analytics resource:

    1. Navigeer naar Text Analytics resource in de Azure Portal. 
    2. Klik **op Metrische** gegevens, onder **Bewaking** in het linkernavigatiemenu. 
    3. Selecteer *Verwerkte tekstrecords* in de vervolgkeuzevak voor **Metrische gegevens.**
    
Een tekstrecord is 1000 tekens.

## <a name="change-your-pricing-tier"></a>Uw prijscategorie wijzigen 

Als u een bestaande Text Analytics resource hebt die gebruikmaakt van de prijscategorie S0 tot en met S4, moet u deze bijwerken om de prijscategorie Standard (S) [te gebruiken.](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) De prijscategorie S0 tot en met S4 wordt ingetrokken. De prijzen van uw resource bijwerken:

1. Navigeer naar Text Analytics resource in [de Azure Portal](https://portal.azure.com/).
2. Selecteer **Prijscategorie** in het linkernavigatiemenu. Deze staat onder **RESOURCE MANAGEMENT.** 
3. Kies de prijscategorie Standard (S). Klik vervolgens op **Selecteren**.

U kunt ook een nieuwe Text Analytics maken met de prijscategorie Standard (S) en uw toepassingen migreren om de referenties voor de nieuwe resource te gebruiken. 

## <a name="using-the-api-synchronously"></a>De API synchroon gebruiken

U kunt een Text Analytics aanroepen (voor scenario's met lage latentie). U moet elke API (functie) afzonderlijk aanroepen wanneer u synchrone API gebruikt. Als u meerdere functies wilt aanroepen, raadpleegt u de onderstaande sectie over het asynchroon Text Analytics aanroepen. 

## <a name="using-the-api-asynchronously"></a>De API asynchroon gebruiken

Vanaf v3.1-preview.3 biedt de Text Analytics-API twee asynchrone eindpunten: 

* Met het eindpunt voor Text Analytics kunt u dezelfde set tekstdocumenten analyseren met meerdere `/analyze` tekstanalysefuncties in één API-aanroep. Voorheen moest u voor elke bewerking afzonderlijke API-aanroepen maken om meerdere functies te gebruiken. Overweeg deze mogelijkheid wanneer u grote sets documenten met meer dan één functie Text Analytics analyseren.

* Het eindpunt voor Text Analytics voor gezondheid, waarmee relevante medische gegevens uit klinische documenten kunnen worden `/health` geëxtraheert en gelabeld.  

Houd er rekening mee dat de eindpunten /analyze en /health alleen beschikbaar zijn in de volgende regio's: VS - west 2, VS - oost 2, VS - centraal, Europa - noord en Europa - west.

Zie de onderstaande tabel om te zien welke functies asynchroon kunnen worden gebruikt. Houd er rekening mee dat er slechts enkele functies kunnen worden aangeroepen vanaf `/analyze` het eindpunt. 

| Functie | Synchroon | Asynchroon |
|--|--|--|
| Taaldetectie | ✔ |  |
| Sentimentanalyse | ✔ |  |
| Meninganalyse | ✔ |  |
| Sleuteltermextractie | ✔ | ✔* |
| Herkenning van benoemde entiteiten (inclusief PII en PHI) | ✔ | ✔* |
| Entiteiten koppelen | ✔ | ✔* |
| Text Analytics voor status (container) | ✔ |  |
| Text Analytics for health (API) |  | ✔  |

`*` - Asynchroon aangeroepen via `/analyze` het eindpunt.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

<a name="json-schema"></a>

## <a name="api-request-formats"></a>INDELINGen van API-aanvragen

U kunt zowel synchrone als asynchrone aanroepen verzenden naar de Text Analytics API.

#### <a name="synchronous"></a>[Synchroon](#tab/synchronous)

### <a name="synchronous-requests"></a>Synchrone aanvragen

De indeling voor API-aanvragen is hetzelfde voor alle synchrone bewerkingen. Documenten worden in een JSON-object verzonden als onbewerkte ongestructureerde tekst. XML wordt niet ondersteund. Het JSON-schema bestaat uit de hieronder beschreven elementen.

| Element | Geldige waarden | Vereist? | Gebruik |
|---------|--------------|-----------|-------|
|`id` |Het gegevenstype is een tekenreeks, maar in de praktijk zijn document-ID's meestal gehele getallen. | Vereist | Het systeem maakt gebruik van de door u verstrekt ID's om de uitvoer te structureren. Taalcodes, sleuteltermen en gevoelsscores worden gegenereerd voor elke id in de aanvraag.|
|`text` | Ongestructureerde onbewerkte tekst, maximaal 5120 tekens. | Vereist | Voor taaldetectie kan tekst in elke taal worden uitgedrukt. Voor sentimentanalyse, sleuteltermextractie en entiteitsidentificatie moet de tekst zich in een [ondersteunde taal hebben.](../language-support.md) |
|`language` | ISO [639-1-code](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) van 2 tekens voor een [ondersteunde taal](../language-support.md) | Varieert | Vereist voor sentimentanalyse, sleuteltermextractie en entiteitskoppeling; optioneel voor taaldetectie. Er is geen fout als u deze uitsluit, maar de analyse wordt zonder deze fout veroorzaakt. De taalcode moet overeenkomen met de die `text` u hebt verstrekt. |

Hier volgt een voorbeeld van een API-aanvraag voor de synchrone Text Analytics eindpunten. 

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

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>Asynchrone aanvragen naar `/analyze` het eindpunt

> [!NOTE]
> Met de nieuwste voorlopige versie van de Text Analytics-clientbibliotheek kunt u Asynchrone analysebewerkingen aanroepen met behulp van een clientobject. Voorbeelden vindt u op GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

Met `/analyze` het eindpunt kunt u kiezen welke ondersteunde Text Analytics u wilt gebruiken in één API-aanroep. Dit eindpunt ondersteunt momenteel:

* Sleuteltermextractie 
* Herkenning van benoemde entiteiten (inclusief PII en PHI)
* Entiteiten koppelen

| Element | Geldige waarden | Vereist? | Gebruik |
|---------|--------------|-----------|-------|
|`displayName` | Tekenreeks | Optioneel | Wordt gebruikt als de weergavenaam voor de unieke id voor de taak.|
|`analysisInput` | Bevat het `documents` onderstaande veld | Vereist | Bevat de informatie voor de documenten die u wilt verzenden. |
|`documents` | Bevat de `id` velden `text` en hieronder | Vereist | Bevat informatie voor elk document dat wordt verzonden en de onbewerkte tekst van het document. |
|`id` | Tekenreeks | Vereist | De door u op te geven ID's worden gebruikt om de uitvoer te structureren. |
|`text` | Ongestructureerde onbewerkte tekst, maximaal 125.000 tekens. | Vereist | Moet in de Engelse taal zijn. Dit is de enige taal die momenteel wordt ondersteund. |
|`tasks` | Bevat de volgende Text Analytics functies: `entityRecognitionTasks` `entityLinkingTasks` , of `keyPhraseExtractionTasks` `entityRecognitionPiiTasks` . | Vereist | Een of meer van de Text Analytics die u wilt gebruiken. Houd er rekening `entityRecognitionPiiTasks` mee dat een `domain` optionele parameter is die kan worden ingesteld op `pii` of en de voor de detectie van geselecteerde `phi` `pii-categories` entiteitstypen. Als de parameter niet is gespecificeerd, wordt `domain` het systeem standaard ingesteld op `pii` . |
|`parameters` | Bevat de `model-version` velden `stringIndexType` en hieronder | Vereist | Dit veld is opgenomen in de bovenstaande functietaken die u kiest. Ze bevatten informatie over de modelversie die u wilt gebruiken en het indextype. |
|`model-version` | Tekenreeks | Vereist | Geef op welke versie van het model wordt aangeroepen die u wilt gebruiken.  |
|`stringIndexType` | Tekenreeks | Vereist | Geef de tekstdecoder op die overeenkomt met uw programmeeromgeving.  Ondersteunde typen zijn `textElement_v8` (standaard), `unicodeCodePoint` , `utf16CodeUnit` . Raadpleeg het [artikel Tekst offsets](../concepts/text-offsets.md#offsets-in-api-version-31-preview) voor meer informatie.  |
|`domain` | Tekenreeks | Optioneel | Is alleen van toepassing als parameter voor `entityRecognitionPiiTasks` de taak en kan worden ingesteld op of `pii` `phi` . De standaardwaarde is `pii` indien niet gespecificeerd.  |

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
        "entityLinkingTasks": [
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
                "model-version": "latest",
                "stringIndexType": "TextElements_v8",
                "domain": "phi",
                "pii-categories":"default"
            }
        }]
    }
}

```

### <a name="asynchronous-requests-to-the-health-endpoint"></a>Asynchrone aanvragen naar `/health` het eindpunt

De indeling voor API-aanvragen voor de Text Analytics gehoste API voor status is hetzelfde als voor de container. Documenten worden in een JSON-object verzonden als onbewerkte ongestructureerde tekst. XML wordt niet ondersteund. Het JSON-schema bestaat uit de hieronder beschreven elementen.  Vul het formulier in en verzend het [Cognitive Services om](https://aka.ms/csgate) toegang aan te vragen tot de Text Analytics voor de openbare preview-versie van de status. Er worden geen kosten in rekening Text Analytics voor statusgebruik. 

| Element | Geldige waarden | Vereist? | Gebruik |
|---------|--------------|-----------|-------|
|`id` |Het gegevenstype is een tekenreeks, maar in de praktijk zijn document-ID's meestal gehele getallen. | Vereist | Het systeem maakt gebruik van de door u verstrekt ID's om de uitvoer te structureren. |
|`text` | Ongestructureerde onbewerkte tekst, maximaal 5120 tekens. | Vereist | Houd er rekening mee dat momenteel alleen Engelse tekst wordt ondersteund. |
|`language` | [ISO 639-1-code](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) van 2 tekens voor een [ondersteunde taal](../language-support.md) | Vereist | Alleen `en` wordt momenteel ondersteund. |

Hier volgt een voorbeeld van een API-aanvraag voor de Text Analytics status-eindpunten. 

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
> Zie het [artikel Gegevens en snelheidslimieten](../concepts/data-limits.md) voor informatie over de snelheids- en groottelimieten voor het verzenden van gegevens naar Text Analytics API.


## <a name="set-up-a-request"></a>Een aanvraag instellen 

Voeg in Postman (of een ander web-API-testprogramma) het eindpunt toe voor de functie die u wilt gebruiken. Gebruik de onderstaande tabel om de juiste eindpuntindeling te vinden en vervang `<your-text-analytics-resource>` door uw resource-eindpunt. Bijvoorbeeld:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Synchroon](#tab/synchronous)

### <a name="endpoints-for-sending-synchronous-requests"></a>Eindpunten voor het verzenden van synchrone aanvragen

| Functie | Soort aanvraag | Resource-eindpunten |
|--|--|--|
| Taaldetectie | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Sentimentanalyse | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Meninganalyse | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Sleuteltermextractie | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Herkenning van benoemde entiteiten - algemeen | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Herkenning van benoemde entiteiten - PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Herkenning van benoemde entiteiten - PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="asynchronous"></a>[Asynchroon](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>Eindpunten voor het verzenden van asynchrone aanvragen naar `/analyze` het eindpunt

| Functie | Soort aanvraag | Resource-eindpunten |
|--|--|--|
| Analyse-taak verzenden | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze` |
| Analysestatus en -resultaten krijgen | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>Eindpunten voor het verzenden van asynchrone aanvragen naar `/health` het eindpunt

| Functie | Soort aanvraag | Resource-eindpunten |
|--|--|--|
| Een Text Analytics verzenden voor de status van de taak  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs` |
| Taakstatus en -resultaten op halen | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |
| Taak annuleren | DELETE | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |

--- 

Nadat u uw eindpunt hebt, in Postman (of een ander web-API-testprogramma):

1. Kies het aanvraagtype voor de functie die u wilt gebruiken.
2. Plak het eindpunt van de juiste bewerking uit de bovenstaande tabel.
3. Stel de drie aanvraagheaders in:

   + `Ocp-Apim-Subscription-Key`: uw toegangssleutel, verkregen van Azure Portal
   + `Content-Type`: application/json
   + `Accept`: application/json

    Als u Postman gebruikt, moet uw aanvraag er ongeveer uitzien als in de volgende schermopname, uitgaande van `/keyPhrases` een eindpunt.
    
    ![Schermopname van aanvraag met eindpunt en headers](../media/postman-request-keyphrase-1.png)
    
4. **Onbewerkt** kiezen voor de indeling van de **body**
    
    ![Schermopname van aanvraag met instellingen voor de body](../media/postman-request-body-raw.png)

5. Plak sommige JSON-documenten in een geldige indeling. Gebruik de voorbeelden in de **sectie API-aanvraagindeling** hierboven en zie de onderstaande onderwerpen voor meer informatie en voorbeelden:

      + [Taaldetectie](text-analytics-how-to-language-detection.md)
      + [Sleuteltermextractie](text-analytics-how-to-keyword-extraction.md)
      + [Sentimentanalyse](text-analytics-how-to-sentiment-analysis.md)
      + [Herkenning van entiteiten](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>De aanvraag verzenden

Verzend de API-aanvraag. Als u de aanroep naar een synchroon eindpunt hebt gedaan, wordt het antwoord onmiddellijk weergegeven als één JSON-document, met een item voor elke document-id die in de aanvraag is opgegeven.

Als u de aanroep naar de asynchrone of eindpunten hebt gedaan, controleert u of u een `/analyze` `/health` 202-responscode hebt ontvangen. U moet het antwoord krijgen om de resultaten weer te geven:

1. Zoek in het API-antwoord de in de header, waarmee de taak wordt geïdentificeerd `Operation-Location` die u naar de API hebt verzonden. 
2. Maak een GET-aanvraag voor het eindpunt dat u hebt gebruikt. Raadpleeg de [bovenstaande tabel voor](#set-up-a-request) de eindpuntindeling en raadpleeg de [API-referentiedocumentatie.](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus) Bijvoorbeeld:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>`

3. Voeg de `Operation-Location` toe aan de aanvraag.

4. Het antwoord is één JSON-document, met een item voor elke document-id die in de aanvraag is opgegeven.

Houd er rekening mee dat voor zowel asynchrone bewerkingen als bewerkingen de resultaten van de GET-aanvraag in stap 2 hierboven 24 uur beschikbaar zijn vanaf het moment dat de taak `/analyze` `/health` is gemaakt.  Deze tijd wordt aangegeven door de `expirationDateTime` waarde in het GET-antwoord.  Na deze periode worden de resultaten verwijderd en kunnen ze niet meer worden opgehaald.    

## <a name="example-api-responses"></a>Voorbeeld van API-antwoorden
 
# <a name="synchronous"></a>[Synchroon](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>Voorbeeldreacties voor synchrone bewerking

De synchrone eindpuntreacties variëren afhankelijk van het eindpunt dat u gebruikt. Zie de volgende artikelen voor voorbeeldreacties.

+ [Taaldetectie](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Sleuteltermextractie](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Sentimentanalyse](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Herkenning van entiteiten](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[Asynchroon](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>Voorbeeldreacties voor asynchrone bewerkingen

Als dit lukt, retourneert de GET-aanvraag naar het eindpunt een `/analyze` -object met de toegewezen taken. Bijvoorbeeld `keyPhraseExtractionTasks`. Deze taken bevatten het antwoordobject van de juiste Text Analytics functie. Zie de volgende artikelen voor meer informatie.

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
