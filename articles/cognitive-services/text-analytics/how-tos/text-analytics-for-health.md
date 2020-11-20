---
title: Text Analytics voor de status gebruiken
titleSuffix: Azure Cognitive Services
description: Meer informatie over het extra heren en labelen van medische gegevens uit ongestructureerde klinische tekst met Text Analytics voor de status.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: e7f017c1f3dc189af2b0fc053912decca3459478
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952757"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Procedure: Text Analytics gebruiken voor de status (preview)

> [!IMPORTANT] 
> Text Analytics status is een preview-functie die IS ingesteld op ' AS IS ' en ' WITH ALL FAULTs '. Daarom **moet Text Analytics voor status (preview) niet worden geïmplementeerd of geïmplementeerd in productie gebruik.** Text Analytics de status niet is bedoeld of beschikbaar gesteld voor gebruik als medisch apparaat, klinisch ondersteunings programma of andere technologie, bedoeld om te worden gebruikt in de diagnose, het verkrijgen, beperken, behandelen of voor komen van ziekten of andere voor waarden en er wordt geen licentie of recht verleend door micro soft om deze mogelijkheid voor dergelijke doel einden te gebruiken. Deze mogelijkheid is niet ontworpen of bedoeld om te worden geïmplementeerd of gedistribueerd als een plaatsvervanger voor professioneel medisch advies of advies, diagnose, behandeling of het klinisch arrest van een ziekte medewerker, en mag niet als zodanig worden gebruikt. De klant is alleen verantwoordelijk voor het gebruik van Text Analytics voor de status. Micro soft garandeert niet dat Text Analytics voor de gezondheid of materialen die in verband met de mogelijkheid worden geleverd, voldoende zijn voor medische doel einden of dat anderszins voldoen aan de gezondheids-en medische vereisten van een persoon. 


Text Analytics status is een functie van de Text Analytics-API-service die relevante medische gegevens ophaalt en uitpakt vanuit ongestructureerde teksten, zoals dokters notities, samen vattingen, klinische documenten en elektronische status records.  Er zijn twee manieren om deze service te gebruiken: 

* De API (asynchroon) van het web 
* Een docker-container (synchroon)   

## <a name="features"></a>Functies

Text Analytics voor de status name entity Recognition (NER) wordt uitgevoerd, wordt het ophalen van relaties, het afleiden van entiteits koppelingen en entiteits koppeling in de Engelse taal van tekst om inzichten in ongestructureerde klinisch en biomedische tekst te ontdekken.

### <a name="named-entity-recognition"></a>[Herkenning van benoemde entiteiten](#tab/ner)

Herkenning met de naam entiteit detecteert woorden en zinsdelen die in ongestructureerde tekst worden genoemd en die kunnen worden gekoppeld aan een of meer semantische typen, zoals diagnose, medicijnen naam, symptoom/ondertekening of leeftijd.

> [!div class="mx-imgBorder"]
> ![Status NER](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Relatie-extractie](#tab/relation-extraction)

Met relatie-extractie worden betekenis volle verbindingen van concepten aangegeven die worden vermeld in de tekst. Zo wordt een relatie ' tijd van voor waarde ' gevonden door een voorwaarde naam te koppelen aan een tijd. 

> [!div class="mx-imgBorder"]
> ![Status RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Entiteiten koppelen](#tab/entity-linking)

Entiteit koppelt disambiguates afzonderlijke entiteiten door benoemde entiteiten die worden vermeld in tekst te koppelen aan concepten die zijn gevonden in een vooraf gedefinieerde data base met concepten. Bijvoorbeeld het Unified medisch taal systeem (UMLS).

> [!div class="mx-imgBorder"]
> ![Health EL](../media/ta-for-health/health-entity-linking.png)

Text Analytics status ondersteunt de koppeling naar de Health-en biomedische woorden lijst in de[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)-kennis bron (Unified medisch language System).

### <a name="negation-detection"></a>[Detectie van negatie](#tab/negation-detection) 

De betekenis van medische inhoud wordt sterk beïnvloed door para meters zoals negatie, wat kritieke implicatie kan hebben als er een probleem is vastgesteld. Text Analytics voor de status ondersteunt de detectie van negatie voor de verschillende entiteiten die in de tekst worden genoemd. 

> [!div class="mx-imgBorder"]
> ![Status NEG](../media/ta-for-health/health-negation.png)

---

Bekijk de [entiteits categorieën](../named-entity-types.md?tabs=health) die door Text Analytics worden geretourneerd voor een volledige lijst met ondersteunde entiteiten.

### <a name="supported-languages-and-regions"></a>Ondersteunde talen en regio's

Text Analytics alleen voor de status ondersteunt documenten in de Engelse taal. 

De Text Analytics voor de status-gehoste Web-API is momenteel alleen beschikbaar in deze regio's: VS-West 2, VS-Oost 2, centraal VS, Europa-noord en Europa-west.

## <a name="request-access-to-the-public-preview"></a>Toegang aanvragen tot de open bare preview

Vul het [Cognitive Services aanvraag formulier](https://aka.ms/csgate) in om toegang tot de Text Analytics voor de open bare preview van de status aan te vragen. U wordt niet gefactureerd voor Text Analytics voor het status gebruik. 

Het formulier vraagt informatie over u, uw bedrijf en het gebruikers scenario waarvoor u de container gaat gebruiken. Nadat u het formulier hebt verzonden, wordt het door het Azure Cognitive Services-team gecontroleerd en een e-mail bericht met een beslissing verzonden.

> [!IMPORTANT]
> * Op het formulier moet u een e-mail adres gebruiken dat is gekoppeld aan een Azure-abonnements-ID.
> * De Azure-resource die u gebruikt, moet zijn gemaakt met de goedgekeurde Azure-abonnements-ID. 
> * Controleer uw e-mail adres (zowel postvak in-map als ongewenste e-mail) voor updates over de status van uw toepassing van micro soft.

## <a name="using-the-docker-container"></a>De docker-container gebruiken 

Als u de Text Analytics voor de status container in uw eigen omgeving wilt uitvoeren, volgt u deze [instructies om de container te downloaden en te installeren](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare).

## <a name="using-the-client-library"></a>De clientbibliotheek gebruiken

Met de nieuwste Prerelease van de Text Analytics-client bibliotheek kunt u Text Analytics aanroepen voor status met behulp van een client object. Raadpleeg de referentie documentatie en zie de voor beelden op GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>Een REST API-aanvraag verzenden 

### <a name="preparation"></a>Voorbereiding

Text Analytics voor de status resulteert in een hogere kwaliteit wanneer u het kleinere aantal tekst aan de hand geeft. Dit geldt voor een aantal andere Text Analytics functies, zoals het uitpakken van sleutel zinnen, waarmee u beter op grotere blokken tekst kunt. Als u de beste resultaten van deze bewerkingen wilt krijgen, kunt u overwegen de invoer dienovereenkomstig te herstructureren.

U moet JSON-documenten in deze indeling hebben: id, tekst en taal. 

De documenten mogen niet meer dan 5.120 tekens per document bevatten. Zie het artikel over de [gegevens limieten](../concepts/data-limits.md?tabs=version-3) onder concepten voor het maximum aantal documenten dat is toegestaan in een verzameling. De verzameling is in de hoofdtekst van de aanvraag ingediend.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>De API-aanvraag voor de gehoste asynchrone Web-API structureren

Voor zowel de container als de gehoste Web-API moet u een POST-aanvraag maken. U kunt [met behulp van Postman](text-analytics-how-to-call-api.md), een krul opdracht of de **API-test console** in de [Text Analytics voor status gehoste API-referentie](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) snel een post-aanvraag samen stellen en verzenden naar de gehoste Web-API in uw gewenste regio. 

Hieronder ziet u een voor beeld van een JSON-bestand dat is gekoppeld aan de Text Analytics voor de hoofd tekst van de status-API-aanvraag:

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

### <a name="hosted-asynchronous-web-api-response"></a>Asynchroon Web API-antwoord gehost 

Omdat deze POST-aanvraag wordt gebruikt voor het verzenden van een taak voor de asynchrone bewerking, is er geen tekst in het antwoord object.  U hebt echter de waarde van de bewerkings locatie sleutel in de antwoord headers nodig om een GET-aanvraag te maken om de status van de taak en de uitvoer te controleren.  Hieronder ziet u een voor beeld van de waarde van de bewerkings locatie in de antwoord header van de POST-aanvraag:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

Als u de taak status wilt controleren, maakt u een GET-aanvraag naar de URL in de waarde van de sleutel header van de bewerkings locatie van het POST-antwoord.  De volgende statussen worden gebruikt om de status van een taak weer te geven: `NotStarted` , `running` , `succeeded` ,,, en `failed` `rejected` `cancelling` `cancelled` .  

U kunt een taak met een `NotStarted` of `running` -status annuleren met een delete http-aanroep naar dezelfde URL als de GET-aanvraag.  Meer informatie over het verwijderen van de aanroep vindt u in de [Text Analytics voor status gehoste API-referentie](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Hier volgt een voor beeld van het antwoord van een GET-aanvraag.  Houd er rekening mee dat de uitvoer beschikbaar is voor ophalen totdat de `expirationDateTime` (24 uur vanaf het tijdstip waarop de taak is gemaakt) is verstreken waarna de uitvoer wordt verwijderd.

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>De API-aanvraag voor de container structureren

U kunt de volgende opdracht gebruiken om een query in te dienen bij de container die u hebt geïmplementeerd met [behulp van Postman](text-analytics-how-to-call-api.md) of onderstaande krul aanvraag, waarbij u de variabele vervangt door `serverURL` de juiste waarde.  Opmerking de versie van de API in de URL voor de container wijkt af van de gehoste API.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

De volgende JSON is een voor beeld van een JSON-bestand dat is gekoppeld aan de Text Analytics voor de hoofd tekst van de status-API-aanvraag:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Hoofd tekst van container

De volgende JSON is een voor beeld van de Text Analytics voor de status-API-antwoord tekst van de synchrone aanroep in de container:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>Uitvoer van negatie detectie

Wanneer u de detectie van negatie gebruikt, kan één negatie term in sommige gevallen meerdere voor waarden tegelijk aanpakken. De ontkenning van een herkende entiteit wordt weer gegeven in de JSON-uitvoer door de Booleaanse waarde van de `isNegated` vlag, bijvoorbeeld:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Uitvoer van relatie-extractie

Uitvoer van relatie-extractie bevat URI-verwijzingen naar de *bron* van de relatie en het *doel* ervan. Entiteiten met een relatie rol van `ENTITY` worden toegewezen aan het `target` veld. Entiteiten met een relatie rol van `ATTRIBUTE` worden toegewezen aan het `source` veld. Afkortings relaties bevatten bidirectionele `source` en `target` velden en worden `bidirectional` ingesteld op `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Zie ook

* [Overzicht van Text Analytics](../overview.md)
* [Benoemde entiteits Categorieën](../named-entity-types.md)
* [Nieuwe functies](../whats-new.md)
