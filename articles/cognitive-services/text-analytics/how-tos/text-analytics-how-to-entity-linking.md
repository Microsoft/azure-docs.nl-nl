---
title: Entiteits herkenning gebruiken met de Text Analytics-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het identificeren en dubbel zinnigheid van de identiteit van een entiteit gevonden in tekst met de Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 5b064365a6f0bd8a544f57d67cd6e4beb98bb404
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505236"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Benoemde entiteits herkenning gebruiken in Text Analytics

Met de Text Analytics-API kunt u ongestructureerde tekst afgeven en een lijst met disambiguated-entiteiten retour neren met koppelingen naar meer informatie op het web. De API ondersteunt zowel NER (named entity Recognition) voor meerdere entiteits categorieën als entiteits koppelingen.

## <a name="entity-linking"></a>Entiteiten koppelen

Entiteit koppelen is de mogelijkheid om de identiteit van een entiteit te identificeren en dubbel zinnigheid die in tekst is gevonden (bijvoorbeeld door te bepalen of een exemplaar van het woord ' Mars ' verwijst naar de planeet of naar het Romeinse niet van War). Voor dit proces moet de aanwezigheid van een Knowledge Base in de juiste taal worden gekoppeld om herkende entiteiten in de tekst te koppelen. De koppeling van de entiteit maakt gebruik van [Wikipedia](https://www.wikipedia.org/) als deze Knowledge Base.

## <a name="named-entity-recognition-ner"></a>NER (Herkenning van benoemde entiteiten)

Herkenning van benoemde entiteiten (NER) is de mogelijkheid om verschillende entiteiten in tekst te identificeren en ze te categoriseren in vooraf gedefinieerde klassen of typen zoals: persoon, locatie, gebeurtenis, product en organisatie.  

## <a name="personally-identifiable-information-pii"></a>Persoonlijk identificeer bare informatie (PII)

De PII-functie maakt deel uit van NER en kan gevoelige entiteiten identificeren en redigeren in tekst die is gekoppeld aan een individuele persoon, zoals: telefoon nummer, e-mail adres, e-mail adres, paspoort nummer.

## <a name="named-entity-recognition-features-and-versions"></a>Functies en versies van de benoemde entiteit herkenning

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| Functie                                                         | NER v 3.0 | NER v 3.1-Preview. 3 |
|-----------------------------------------------------------------|--------|----------|
| Methoden voor afzonderlijke aanvragen en batchaanvragen                          | X      | X        |
| Uitbrei ding van entiteits herkenning over verschillende categorieën           | X      | X        |
| Afzonderlijke eind punten voor het verzenden van entiteits koppelings-en NER-aanvragen. | X      | X        |
| Erkenning van persoonlijke ( `PII` ) en status ( `PHI` )-informatie entiteiten        |        | X        |
| Redactie van `PII`        |        | X        |

Zie [taal ondersteuning](../language-support.md) voor meer informatie.

Named entity Recognition V3 biedt uitgebreide detectie over meerdere typen. Op dit moment kan NER v 3.0 entiteiten herkennen in de [categorie algemene entiteit](../named-entity-types.md).

Named entity Recognition v 3.1-Preview. 3 bevat de detectie mogelijkheden van v 3.0 en: 
* De mogelijkheid om persoonlijke gegevens () te detecteren `PII` met behulp van het `v3.1-preview.3/entities/recognition/pii` eind punt. 
* Een optionele `domain=phi` para meter voor het detecteren van vertrouwelijke status informatie ( `PHI` ).
* [Asynchrone bewerking](text-analytics-how-to-call-api.md) met behulp van het `/analyze` eind punt.

Zie voor meer informatie het artikel [entiteits categorieën](../named-entity-types.md) en de onderstaande sectie [aanvragen voor eind punten](#request-endpoints) . 

## <a name="sending-a-rest-api-request"></a>Een REST API-aanvraag verzenden

### <a name="preparation"></a>Voorbereiding

U moet JSON-documenten hebben met de volgende indeling: ID, tekst, taal.

Elk document moet 5.120 tekens lang zijn en u kunt Maxi maal 1.000 items (Id's) per verzameling hebben. De verzameling is in de hoofdtekst van de aanvraag ingediend.

### <a name="structure-the-request"></a>Structureer de aanvraag

Maak een POST-aanvraag. U kunt [postman](text-analytics-how-to-call-api.md) of de **API-test console** in de volgende koppelingen gebruiken om een snelle structuur en verzen ding te sturen. 

> [!NOTE]
> U vindt de sleutel en het eindpunt voor uw Text Analytics-resource in Azure Portal. U vindt deze op de **Quickstart**-pagina van de resource, onder **Resourcebeheer**. 


### <a name="request-endpoints"></a>Eindpunten voor aanvragen

#### <a name="version-31-preview3"></a>[Versie 3,1-Preview. 3](#tab/version-3-preview)

Herkenning van benoemde entiteiten `v3.1-preview.3` maakt gebruik van afzonderlijke eind punten voor ner, PII en aanvragen voor het koppelen van entiteiten. Gebruik een URL-indeling hieronder op basis van uw aanvraag.

**Entiteiten koppelen**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/linking`

[Named entity Recognition versie 3,1-Preview-verwijzing voor `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesLinking)

**Herkenning van benoemde entiteiten**
* Algemene entiteiten- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/general`

[Named entity Recognition versie 3,1-Preview-verwijzing voor `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionGeneral)

**Persoonlijk identificeer bare informatie (PII)**
* Persoonlijke `PII` gegevens ()- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii`

U kunt ook de optionele `domain=phi` para meter gebruiken om informatie over de status ( `PHI` ) in de tekst te detecteren. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi`

Vanaf `v3.1-preview.3` is het JSON-antwoord een `redactedText` eigenschap die de gewijzigde invoer tekst bevat waarin de GEDETECTEERDe PII-entiteiten worden vervangen door een `*` voor elk teken in de entiteiten.

[Named entity Recognition versie 3,1-Preview-verwijzing voor `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionPii)

#### <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Met named entity Recognition v3 worden afzonderlijke eind punten gebruikt voor NER en aanvragen voor entiteits koppelingen. Gebruik een URL-indeling hieronder op basis van uw aanvraag:

**Entiteiten koppelen**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[Verwijzing naar benoemde entiteit Recognition versie 3,0 voor `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

**Herkenning van benoemde entiteiten**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[Verwijzing naar benoemde entiteit Recognition versie 3,0 voor `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

Stel een hoofdtekst in om uw Text Analytics-API-sleutel in de aanvraag op te nemen. Geef in de hoofd tekst van de aanvraag de JSON-documenten op die u hebt voor bereid.

### <a name="example-ner-request"></a>Voor beeld van NER-aanvraag 

De volgende JSON is een voor beeld van inhoud die u naar de API zou kunnen verzenden. De aanvraag indeling is hetzelfde voor beide versies van de API.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}

```

## <a name="post-the-request"></a>Plaats de aanvraag

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie de sectie [gegevens limieten](../overview.md#data-limits) in het overzicht voor informatie over de grootte en het aantal aanvragen dat u per minuut en seconde kunt verzenden.

De Text Analytics-API is stateless (staatloos). Er worden geen gegevens in uw account opgeslagen en de resultaten worden onmiddellijk in het antwoord geretourneerd.

## <a name="view-results"></a>Resultaten weergeven

Alle POST-aanvragen retour neren een JSON-indelings antwoord met de eigenschappen id en gedetecteerde entiteit.

Uitvoer wordt onmiddellijk geretourneerd. U kunt de resultaten streamen naar een toepassing die JSON accepteert of u kunt de uitvoer opslaan als lokaal bestand en vervolgens importeren in een toepassing waarmee u kunt sorteren, zoeken en de gegevens kunt manipuleren. Vanwege meertalige en Emoji-ondersteuning kan het antwoord tekstverschuivingen bevatten. Zie [tekst verschuivingen verwerken](../concepts/text-offsets.md)voor meer informatie.

### <a name="example-responses"></a>Voorbeeld reacties

Versie 3 biedt afzonderlijke eind punten voor algemene NER, PII en het koppelen van entiteiten. De antwoorden voor beide bewerkingen vindt u hieronder. 

#### <a name="version-31-preview"></a>[Versie 3.1-preview](#tab/version-3-preview)

Voor beeld van een PII-antwoord:
```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

Voor beeld van een entiteit koppelings reactie:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```


#### <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Voor beeld van een algemeen NER-antwoord:
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

---


## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd voor het koppelen van entiteiten met behulp van Text Analytics in Cognitive Services. Samenvatting:

* JSON-documenten in de aanvraagbody omvatten een id, tekst en taalcode.
* POST-aanvragen worden verzonden naar een of meer eind punten met behulp van een aangepaste [toegangs sleutel en een eind punt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dat geldig is voor uw abonnement.
* De antwoord uitvoer, die bestaat uit gekoppelde entiteiten (inclusief betrouwbaarheids scores, verschuivingen en webkoppelingen, voor elke document-ID), kan worden gebruikt in elke toepassing

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Text Analytics](../overview.md)
* [De Text Analytics-clientbibliotheek gebruiken](../quickstarts/client-libraries-rest-api.md)
* [Nieuwe functies](../whats-new.md)
