---
title: Aangepaste web API-vaardigheid in vaardig heden
titleSuffix: Azure Cognitive Search
description: Breid mogelijkheden van Azure Cognitive Search vaardig heden uit door aan te roepen naar web-Api's. Gebruik de aangepaste web API-vaardigheid om uw aangepaste code te integreren.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: cb5ee7d3549e433fb184b8c55c28b9a28ed89272
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011928"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Aangepaste web API-vaardigheid in een Azure Cognitive Search-verrijkings pijplijn

Met de **aangepaste web API** -vaardigheid kunt u AI-verrijking uitbreiden door een web-API-eind punt aan te roepen waarmee aangepaste bewerkingen worden uitgevoerd. Net als bij ingebouwde vaardig heden bevat een **aangepaste web-API** -vaardigheid invoer en uitvoer. Afhankelijk van de invoer, ontvangt uw web-API een JSON-nettolading wanneer de Indexeer functie wordt uitgevoerd en voert deze een JSON-nettolading uit als antwoord, samen met de status code geslaagd. Er wordt verwacht dat het antwoord de uitvoer bevat die is opgegeven door uw aangepaste vaardigheid. Elk ander antwoord wordt als een fout beschouwd en er worden geen verrijkingen uitgevoerd.

De structuur van de JSON-nettoladingen wordt verder beschreven in dit document.

> [!NOTE]
> De Indexeer functie voert twee keer opnieuw uit voor bepaalde standaard HTTP-status codes die worden geretourneerd door de Web-API. Deze HTTP-status codes zijn: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Micro soft. skills. custom. WebApiSkill

## <a name="skill-parameters"></a>Vaardigheids parameters

Parameters zijn hoofdlettergevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| `uri` | De URI van de Web-API waarnaar de _JSON_ -nettolading wordt verzonden. Alleen **https** URI-schema is toegestaan |
| `httpMethod` | De methode die moet worden gebruikt bij het verzenden van de payload. Toegestane methoden zijn `PUT` of `POST` |
| `httpHeaders` | Een verzameling sleutel-waardeparen waarbij de sleutels koptekst namen en-waarden vertegenwoordigen header waarden die worden verzonden naar uw web-API en de payload. De volgende headers mogen niet voor komen in deze verzameling:  `Accept` , `Accept-Charset` , `Accept-Encoding` ,,, `Content-Length` `Content-Type` `Cookie` , `Host` , `TE` , `Upgrade` , `Via` |
| `timeout` | Beschrijving Hiermee geeft u de time-out op voor de HTTP-client die de API-aanroep maakt. Deze moet worden ingedeeld als een XSD ' dayTimeDuration-waarde (een beperkte subset van een [ISO 8601 duration](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) -waarde). Bijvoorbeeld `PT60S` gedurende 60 seconden. Als deze niet is ingesteld, wordt de standaard waarde van 30 seconden gekozen. De time-out kan worden ingesteld op een maximum van 230 seconden en een minimum van 1 seconde. |
| `batchSize` | Beschrijving Hiermee wordt aangegeven hoeveel ' gegevens records ' (Zie de structuur van de _JSON_ -nettolading hieronder) per API-aanroep worden verzonden. Als deze niet is ingesteld, wordt de standaard waarde 1000 gekozen. We raden u aan gebruik te maken van deze para meter om een geschikte verhouding te krijgen tussen het door voeren van de indexering en de belasting van uw API |
| `degreeOfParallelism` | Beschrijving Indien opgegeven, wordt hiermee het aantal aanroepen aangegeven dat de Indexeer functie parallel moet maken aan het eind punt dat u hebt opgegeven. U kunt deze waarde verlagen als uw eind punt met een te hoge belasting van de aanvraag is mislukt of als u het wilt verhogen als uw eind punt meer aanvragen kan accepteren en u de prestaties van de Indexeer functie wilt verhogen.  Als deze niet is ingesteld, wordt de standaard waarde 5 gebruikt. De `degreeOfParallelism` kan worden ingesteld op een maximum van 10 en een minimum van 1. |

## <a name="skill-inputs"></a>Vaardigheids invoer

Er zijn geen "vooraf gedefinieerde" invoer voor deze vaardigheid. U kunt een of meer velden kiezen die al beschikbaar zijn op het moment dat de uitvoering van deze vaardigheid als invoer wordt uitgevoerd en de _JSON_ -nettolading die naar de Web-API wordt verzonden, verschillende velden heeft.

## <a name="skill-outputs"></a>Vaardigheids uitvoer

Er zijn geen vooraf gedefinieerde uitvoer voor deze vaardigheid. Afhankelijk van de reactie die uw web-API retourneert, voegt u uitvoer velden toe zodat deze kunnen worden opgehaald uit het _JSON_ -antwoord.


## <a name="sample-definition"></a>Voorbeeld definitie

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Voorbeeld structuur voor de invoer van JSON

Deze _JSON_ -structuur vertegenwoordigt de nettolading die wordt verzonden naar uw web-API.
Deze beperkingen worden altijd gevolgd:

* De entiteit op het hoogste niveau wordt aangeroepen `values` en is een matrix met objecten. Het aantal objecten is de meeste `batchSize`
* Elk object in de `values` matrix heeft
    * Een `recordId` eigenschap die een **unieke** teken reeks is, die wordt gebruikt om die record te identificeren.
    * Een `data` eigenschap die een _JSON_ -object is. De velden van de `data` eigenschap komen overeen met de namen die zijn opgegeven in de `inputs` sectie van de vaardigheids definitie. De waarde van deze velden gaat uit `source` van deze velden (die mogelijk afkomstig zijn uit een veld in het document of mogelijk van een andere vaardigheid)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Voor beeld van JSON-structuur van uitvoer

De "uitvoer" komt overeen met de reactie die wordt geretourneerd door de Web-API. De Web-API mag alleen een _JSON_ -nettolading retour neren (gecontroleerd door te kijken naar de `Content-Type` reactie header) en moet voldoen aan de volgende beperkingen:

* Er moet een entiteit op het hoogste niveau worden aangeroepen `values` die een matrix met objecten moet zijn.
* Het aantal objecten in de matrix moet hetzelfde zijn als het aantal objecten dat naar de Web-API wordt verzonden.
* Elk object moet het volgende hebben:
   * Een `recordId` eigenschap
   * Een `data` eigenschap, die een object is waarbij de velden verrijkingen zijn die overeenkomen met de ' namen ' in de `output` en waarvan de waarde wordt beschouwd als de verrijking.
   * Een `errors` eigenschap, een matrix met eventuele fouten die worden toegevoegd aan de uitvoerings geschiedenis van de Indexeer functie. Deze eigenschap is vereist, maar kan wel een `null` waarde hebben.
   * Een `warnings` eigenschap, een matrix met waarschuwingen die worden toegevoegd aan de uitvoerings geschiedenis van de Indexeer functie. Deze eigenschap is vereist, maar kan wel een `null` waarde hebben.
* De objecten in de `values` matrix moeten zich niet in dezelfde volg orde besturen als de objecten in de `values` matrix die als een aanvraag naar de Web-API worden verzonden. De wordt echter `recordId` gebruikt voor correlatie, zodat alle records in het antwoord met een `recordId` die geen deel uitmaakt van de oorspronkelijke aanvraag voor de Web-API, worden genegeerd.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Fout cases
Naast de Web-API die niet beschikbaar is, of het verzenden van niet-geslaagde status codes, worden de volgende als foutieve cases beschouwd:

* Als de Web-API een status code voor geslaagde pogingen retourneert, maar het antwoord geeft aan dat het niet `application/json` het geval is, wordt het antwoord beschouwd als ongeldig en worden er geen verrijkingen uitgevoerd.
* Als er een **Ongeldige** ( `recordId` in de oorspronkelijke aanvraag of met dubbele waarden) records in de antwoord matrix aanwezig zijn `values` , wordt er geen verrijking uitgevoerd voor **deze** records.

Als de Web-API niet beschikbaar is of een HTTP-fout retourneert, wordt een beschrijvende fout met alle beschik bare Details over de HTTP-fout toegevoegd aan de indexerings geschiedenis.

## <a name="see-also"></a>Zie ook

+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Aangepaste vaardigheid toevoegen aan een AI-verrijkings pijplijn](cognitive-search-custom-skill-interface.md)
+ [Voor beeld: een aangepaste vaardigheid maken voor AI-verrijking](cognitive-search-create-custom-skill-example.md)
