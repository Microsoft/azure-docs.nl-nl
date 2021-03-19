---
title: Interface definitie voor aangepaste vaardig heden
titleSuffix: Azure Cognitive Search
description: Aangepaste interface voor het uitpakken van gegevens voor aangepaste web-API-vaardig heden in een AI-verrijkings pijplijn in azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: e78f0d1e8d6d637dfebe1ff475ab8416ba49a263
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935412"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Een aangepaste vaardigheid toevoegen aan een Azure Cognitive Search-verrijkings pijplijn

> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=172&end=221]

Een [verrijkings pijplijn](cognitive-search-concept-intro.md) in azure Cognitive Search kan worden samengesteld op basis van [ingebouwde cognitieve vaardig heden](cognitive-search-predefined-skills.md) en [aangepaste vaardig heden](cognitive-search-custom-skill-web-api.md) die u persoonlijk maakt en toevoegt aan de pijp lijn. In dit artikel leert u hoe u een aangepaste vaardigheid maakt waarmee een interface wordt weer gegeven, zodat deze kan worden opgenomen in een AI-verrijkings pijplijn. 

Het bouwen van een aangepaste vaardigheid biedt een manier om trans formaties die uniek zijn voor uw inhoud, in te voegen. Een aangepaste vaardigheid wordt onafhankelijk uitgevoerd, waarbij de gewenste verrijkings stap wordt toegepast. U kunt bijvoorbeeld veld-specifieke aangepaste entiteiten definiëren en aangepaste classificatie modellen bouwen om onderscheid te maken tussen zakelijke en financiële contracten en documenten, of u kunt een vaardigheid voor spraak herkenning toevoegen om diep gaande geluids bestanden voor relevante inhoud te bereiken. Voor een stapsgewijs voor beeld raadpleegt [u voor beeld: een aangepaste vaardigheid maken voor AI-verrijking](cognitive-search-create-custom-skill-example.md).

 Welke aangepaste capaciteit u nodig hebt, er is een eenvoudige en heldere interface voor het koppelen van een aangepaste vaardigheid aan de rest van de verrijkings pijplijn. De enige vereiste voor insluiting in een [vaardig heden](cognitive-search-defining-skillset.md) is de mogelijkheid om invoer te accepteren en uitvoer te verzenden op manieren die kunnen worden gebruikt binnen de vaardig heden als geheel. De focus van dit artikel bevindt zich in de invoer-en uitvoer indelingen die de verrijkings pijplijn nodig heeft.

## <a name="web-api-custom-skill-interface"></a>Aangepaste vaardigheden interface voor web-API

Aangepaste vaardigheids eindpunten voor WebAPI op standaard time-out als ze geen antwoord retour neren binnen een venster van 30 seconden. De indexerings pijplijn is synchroon en het indexeren produceert een time-outfout als er in dat venster geen antwoord wordt ontvangen.  U kunt de time-out Maxi maal 230 seconden configureren door de para meter time-out in te stellen:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Zorg ervoor dat de URI veilig is (HTTPS).

Momenteel is het enige mechanisme voor interactie met een aangepaste vaardigheid via een web API-interface. De Web-API moet voldoen aan de vereisten die in deze sectie worden beschreven.

### <a name="1--web-api-input-format"></a>1. Web-API-invoer indeling


> [!VIDEO https://www.youtube.com/embed/fHLCE-NZeb4?version=3&start=294&end=340]


De Web-API moet een matrix accepteren met records die moeten worden verwerkt. Elke record moet een ' eigenschappen verzameling ' bevatten die de invoer is van uw web-API. 

Stel dat u een eenvoudige verrijker wilt maken waarin de eerste datum wordt aangegeven die in de tekst van een contract wordt vermeld. In dit voor beeld accepteert de vaardigheid één invoer *contractText* als de contract tekst. De vaardigheid heeft ook één uitvoer, wat de datum van het contract is. Als u de verrijker interessanter wilt maken, retourneert u deze *contractDate* in de vorm van een complex type met meerdere delen.

De Web-API moet gereed zijn voor het ontvangen van een batch met invoer records. Elk lid van de matrix *waarden* vertegenwoordigt de invoer voor een bepaalde record. Elke record moet de volgende elementen bevatten:

+ Een *RecordID* -lid dat de unieke id voor een bepaalde record is. Wanneer de resultaten door de verrijker worden geretourneerd, moet *deze opnieuw* worden opgegeven om ervoor te zorgen dat de beller de record resultaten kan aanpassen aan de invoer.

+ Een *Gegevenslid* dat in feite een zak van invoer velden voor elke record is.

Als u het voor beeld meer wilt doen, moet uw web-API aanvragen verwachten die er als volgt uitzien:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
In werkelijkheid kan uw service worden aangeroepen met honderden of duizenden records in plaats van alleen de drie die hier worden weer gegeven.

### <a name="2-web-api-output-format"></a>2. Web API-uitvoer indeling

De indeling van de uitvoer is een set records met een *RecordID* en een eigenschappen verzameling 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

Dit voor beeld heeft slechts één uitvoer, maar u kunt meer dan één eigenschap uitvoeren. 

### <a name="errors-and-warning"></a>Fouten en waarschuwingen

Zoals weer gegeven in het vorige voor beeld, kunt u fout-en waarschuwings berichten voor elke record retour neren.

## <a name="consuming-custom-skills-from-skillset"></a>Aangepaste vaardig heden uit vaardig heden gebruiken

Wanneer u een web API-verrijker maakt, kunt u HTTP-headers en-para meters beschrijven als onderdeel van de aanvraag. In het volgende fragment ziet u hoe aanvraag parameters en *optionele* http-headers kunnen worden beschreven als onderdeel van de definitie van de vaardigheidset. HTTP-headers zijn geen vereiste, maar u kunt hiermee aanvullende configuratie mogelijkheden aan uw vaardig heden toevoegen en deze instellen vanuit de definitie van de vaardig heden.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel worden de interface vereisten behandeld die nodig zijn voor het integreren van een aangepaste vaardigheid in een vaardig heden. Klik op de volgende koppelingen voor meer informatie over aangepaste vaardig heden en vaardigheidset-samen stelling.

+ [Bekijk onze video over aangepaste vaardig heden](https://youtu.be/fHLCE-NZeb4)
+ [Power vaardig heden: een opslag plaats met aangepaste vaardig heden](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Voor beeld: een aangepaste vaardigheid maken voor AI-verrijking](cognitive-search-create-custom-skill-example.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Vaardig heden maken (REST)](/rest/api/searchservice/create-skillset)
+ [Verrijkte velden toewijzen](cognitive-search-output-field-mapping.md)