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
ms.date: 03/11/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 80a943d235783852f57832363b5af8048f010575
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599426"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Procedure: Text Analytics gebruiken voor de status (preview)

> [!IMPORTANT] 
> Text Analytics status is een preview-functie die IS ingesteld op ' AS IS ' en ' WITH ALL FAULTs '. Daarom **moet Text Analytics voor status (preview) niet worden geïmplementeerd of geïmplementeerd in productie gebruik.** Text Analytics de status niet is bedoeld of beschikbaar gesteld voor gebruik als medisch apparaat, klinisch ondersteunings programma of andere technologie, bedoeld om te worden gebruikt in de diagnose, het verkrijgen, beperken, behandelen of voor komen van ziekten of andere voor waarden en er wordt geen licentie of recht verleend door micro soft om deze mogelijkheid voor dergelijke doel einden te gebruiken. Deze mogelijkheid is niet ontworpen of bedoeld om te worden geïmplementeerd of gedistribueerd als een plaatsvervanger voor professioneel medisch advies of advies, diagnose, behandeling of het klinisch arrest van een ziekte medewerker, en mag niet als zodanig worden gebruikt. De klant is alleen verantwoordelijk voor het gebruik van Text Analytics voor de status. Micro soft garandeert niet dat Text Analytics voor de gezondheid of materialen die in verband met de mogelijkheid worden geleverd, voldoende zijn voor medische doel einden of dat anderszins voldoen aan de gezondheids-en medische vereisten van een persoon. 


Text Analytics status is een functie van de Text Analytics-API-service die relevante medische gegevens ophaalt en uitpakt vanuit ongestructureerde teksten, zoals dokters notities, samen vattingen, klinische documenten en elektronische status records.  Er zijn twee manieren om deze service te gebruiken: 

* [De API (asynchroon) van het web](#structure-the-api-request-for-the-hosted-asynchronous-web-api)
* [Een docker-container (synchroon)](#hosted-asynchronous-web-api-response)   

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Functies

Text Analytics voor de status name entity Recognition (NER) wordt uitgevoerd, wordt het ophalen van relaties, het afleiden van entiteits koppelingen en entiteits koppeling in de Engelse taal van tekst om inzichten in ongestructureerde klinisch en biomedische tekst te ontdekken.

### <a name="named-entity-recognition"></a>[Herkenning van benoemde entiteiten](#tab/ner)

Herkenning met de naam entiteit detecteert woorden en zinsdelen die in ongestructureerde tekst worden genoemd en die kunnen worden gekoppeld aan een of meer semantische typen, zoals diagnose, medicijnen naam, symptoom/ondertekening of leeftijd.

> [!div class="mx-imgBorder"]
> ![Status NER](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Relatie-extractie](#tab/relation-extraction)

Met relatie-extractie worden betekenis volle verbindingen van concepten aangegeven die worden vermeld in de tekst. Bijvoorbeeld, een relatie ' tijd van voor waarde ' wordt gevonden door een naam van een voor waarde te koppelen aan een tijd of tussen een afkorting en een volledige beschrijving.  

> [!div class="mx-imgBorder"]
> ![Status RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Entiteiten koppelen](#tab/entity-linking)

Entiteit koppelt disambiguates afzonderlijke entiteiten door benoemde entiteiten die worden vermeld in tekst te koppelen aan concepten die zijn gevonden in een vooraf gedefinieerde data base met concepten, waaronder het Unified medisch language-systeem (UMLS). Medische concepten worden ook als voorkeurs benaming toegewezen, als een extra vorm van normalisatie.

> [!div class="mx-imgBorder"]
> ![Health EL](../media/ta-for-health/health-entity-linking.png)

Text Analytics status ondersteunt de koppeling naar de Health-en biomedische woorden lijst in de[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)-kennis bron (Unified medisch language System).

### <a name="assertion-detection"></a>[Bevestigings detectie](#tab/assertion-detection) 

De betekenis van medische inhoud wordt sterk beïnvloed door aanpassings functies, zoals negatieve of voorwaardelijke beweringen die kritieke implicaties kunnen hebben als dat niet het geval is. Text Analytics voor de status ondersteunt drie categorieën bevestigings detectie voor entiteiten in de tekst: 

* zekerheid
* waarden
* verband

> [!div class="mx-imgBorder"]
> ![Status NEG](../media/ta-for-health/assertions.png)

---

Bekijk de [entiteits categorieën](../named-entity-types.md?tabs=health) die door Text Analytics worden geretourneerd voor een volledige lijst met ondersteunde entiteiten. Zie de [Opmerking over Text Analytics transparantie](/legal/cognitive-services/text-analytics/transparency-note#general-guidelines-to-understand-and-improve-performance?context=/azure/cognitive-services/text-analytics/context/context)voor meer informatie over betrouwbaarheids scores. 

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

De documenten mogen niet meer dan 5.120 tekens per document bevatten. Zie het artikel [gegevenslimieten](../concepts/data-limits.md?tabs=version-3) onder Concepten voor het maximum aantal documenten dat is toegestaan in een verzameling. De verzameling is in de hoofdtekst van de aanvraag ingediend.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>De API-aanvraag voor de gehoste asynchrone Web-API structureren

Voor zowel de container als de gehoste Web-API moet u een POST-aanvraag maken. U kunt [met behulp van Postman](text-analytics-how-to-call-api.md), een krul opdracht of de **API-test console** in de [Text Analytics voor status gehoste API-referentie](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) snel een post-aanvraag samen stellen en verzenden naar de gehoste Web-API in uw gewenste regio. 

> [!NOTE]
> Zowel de asynchrone `/analyze` als de `/health` eind punten zijn alleen beschikbaar in de volgende REGIO'S: VS-West 2, VS-Oost 2, VS-centraal, Europa-noord en Europa-West.  Als u geslaagde aanvragen voor deze eind punten wilt maken, controleert u of uw resource is gemaakt in een van deze regio's.

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

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/entities/health/jobs/<jobID>`

Als u de taak status wilt controleren, maakt u een GET-aanvraag naar de URL in de waarde van de sleutel header van de bewerkings locatie van het POST-antwoord.  De volgende statussen worden gebruikt om de status van een taak weer te geven: `NotStarted` , `running` , `succeeded` ,,, en `failed` `rejected` `cancelling` `cancelled` .  

U kunt een taak met een `NotStarted` of `running` -status annuleren met een delete http-aanroep naar dezelfde URL als de GET-aanvraag.  Meer informatie over het verwijderen van de aanroep vindt u in de [Text Analytics voor status gehoste API-referentie](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Hier volgt een voor beeld van het antwoord van een GET-aanvraag.  De uitvoer is beschikbaar voor ophalen totdat de `expirationDateTime` (24 uur vanaf het tijdstip waarop de taak is gemaakt) is verstreken waarna de uitvoer wordt verwijderd.

```json
{
    "jobId": "be437134-a76b-4e45-829e-9b37dcd209bf",
    "lastUpdateDateTime": "2021-03-11T05:43:37Z",
    "createdDateTime": "2021-03-11T05:42:32Z",
    "expirationDateTime": "2021-03-12T05:42:32Z",
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
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "name": "remdesivir",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "DRUGBANK",
                                "id": "DB14761"
                            },
                            {
                                "dataSource": "GS",
                                "id": "6192"
                            },
                            {
                                "dataSource": "MEDCIN",
                                "id": "398132"
                            },
                            {
                                "dataSource": "MMSL",
                                "id": "d09540"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "MTHSPL",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            },
                            {
                                "dataSource": "NDDF",
                                "id": "018308"
                            },
                            {
                                "dataSource": "RXNORM",
                                "id": "2284718"
                            },
                            {
                                "dataSource": "SNOMEDCT_US",
                                "id": "870592005"
                            },
                            {
                                "dataSource": "VANDF",
                                "id": "4039395"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.94
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/3",
                                "role": "Time"
                            }
                        ]
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2021-03-01"
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
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

### <a name="assertion-output"></a>Bevestigings uitvoer

Text Analytics voor de status retour neren bevestigings parameters, die informatieve kenmerken zijn die zijn toegewezen aan medische concepten die dieper inzicht hebben in de context van de concepten in de tekst. Deze wijzigings functies zijn onderverdeeld in drie categorieën, die allemaal gericht zijn op een ander aspect en die een set van elkaar uitsluitende waarden bevatten. Er wordt slechts één waarde per categorie toegewezen aan elke entiteit. De meest voorkomende waarde voor elke categorie is de standaard waarde. Het uitvoer antwoord van de service bevat alleen bevestigings parameters die afwijken van de standaard waarde.

**Zekerheid**  : geeft informatie over de aanwezigheid (aanwezig versus afwezig) van het concept en hoe duidelijk de tekst betrekking heeft op de aanwezigheid (afdoende versus mogelijk).
*   **Positief** [standaard]: het concept bestaat of is gebeurd.
* **Negatief**: het concept bestaat nu niet of is nog nooit gebeurd.
* **Positive_Possible**: mogelijk bestaat het concept, maar er is enige onzekerheid.
* **Negative_Possible**: het bestaan van het concept is onwaarschijnlijk, maar er is enige onzekerheid.
* **Neutral_Possible**: het concept kan al dan niet bestaan zonder dat er aan beide zijden een tendens is.

**Voorwaardelijkheid** : geeft informatie over of het bestaan van een concept afhankelijk is van bepaalde voor waarden. 
*   **Geen** [standaard]: het concept is een feit dat geen hypothetische en niet afhankelijk is van bepaalde voor waarden.
*   **Hypothetisch**: het concept kan in de toekomst worden ontwikkeld of uitgevoerd.
*   **Voorwaardelijk**: het concept bestaat of treedt alleen op onder bepaalde voor waarden.

**Koppeling** : beschrijft of het concept is gekoppeld aan het onderwerp van de tekst of iemand anders.
*   **Onderwerp** [standaard]: het concept is gekoppeld aan het onderwerp van de tekst, meestal de patiënt.
*   **Someone_Else**: het concept is gekoppeld aan iemand die niet het onderwerp van de tekst is.


In de bevestigings detectie worden ontkennende entiteiten aangeduid als een negatieve waarde voor de categorie zekerheid, bijvoorbeeld:

```json
{
                        "offset": 381,
                        "length": 3,
                        "text": "SOB",
                        "category": "SymptomOrSign",
                        "confidenceScore": 0.98,
                        "assertion": {
                            "certainty": "negative"
                        },
                        "name": "Dyspnea",
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C0013404"
                            },
                            {
                                "dataSource": "AOD",
                                "id": "0000005442"
                            },
    ...
```

### <a name="relation-extraction-output"></a>Uitvoer van relatie-extractie

Text Analytics voor de status herkent relaties tussen verschillende concepten, inclusief relaties tussen het kenmerk en de entiteit (bijvoorbeeld de richting van de hoofd structuur, de dosering van de medicijnen) en tussen entiteiten (bijvoorbeeld afkortings detectie).

**VEELGEBRUIKT**

**DIRECTION_OF_BODY_STRUCTURE**

**DIRECTION_OF_CONDITION**

**DIRECTION_OF_EXAMINATION**

**DIRECTION_OF_TREATMENT**

**DOSAGE_OF_MEDICATION**

**FORM_OF_MEDICATION**

**FREQUENCY_OF_MEDICATION**

**FREQUENCY_OF_TREATMENT**

**QUALIFIER_OF_CONDITION**

**RELATION_OF_EXAMINATION**

**ROUTE_OF_MEDICATION** 

**TIME_OF_CONDITION**

**TIME_OF_EVENT**

**TIME_OF_EXAMINATION**

**TIME_OF_MEDICATION**

**TIME_OF_TREATMENT**

**UNIT_OF_CONDITION**

**UNIT_OF_EXAMINATION**

**VALUE_OF_CONDITION**  

**VALUE_OF_EXAMINATION**

> [!NOTE]
> * Relaties die naar voor waarde verwijzen, kunnen verwijzen naar het type van de controle-entiteit of het entiteits type SYMPTOM_OR_SIGN.
> * Relaties die betrekking hebben op medicijnen, kunnen verwijzen naar het type MEDICATION_NAME entiteit of het entiteits type MEDICATION_CLASS.
> * Relaties die naar tijd verwijzen, kunnen verwijzen naar het type tijd entiteit of het type datum entiteit.

Uitvoer van relatie-extractie bevat URI-verwijzingen en toegewezen rollen van de entiteiten van het relatie type. Bijvoorbeeld:

```json
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/0",
                                "role": "Dosage"
                            },
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            }
                        ]
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "entities": [
                            {
                                "ref": "#/results/documents/0/entities/1",
                                "role": "Medication"
                            },
                            {
                                "ref": "#/results/documents/0/entities/2",
                                "role": "Route"
                            }
                        ]
...
]
```

## <a name="see-also"></a>Zie ook

* [Overzicht van Text Analytics](../overview.md)
* [Benoemde entiteits Categorieën](../named-entity-types.md)
* [Nieuwe functies](../whats-new.md)
