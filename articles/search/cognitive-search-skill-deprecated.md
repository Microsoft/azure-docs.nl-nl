---
title: Afgeschafte cognitieve vaardig heden
titleSuffix: Azure Cognitive Search
description: Deze pagina bevat een lijst met cognitieve vaardig heden die als verouderd worden beschouwd en wordt niet meer ondersteund in de nabije toekomst in azure Cognitive Search vaardig heden.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 85f3b9862bd8155c1a4b11860dc82d92a2f9e810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936092"
---
# <a name="deprecated-cognitive-skills-in-azure-cognitive-search"></a>Afgeschafte cognitieve vaardig heden in azure Cognitive Search

In dit document worden cognitieve vaardig heden beschreven die als afgeschaft worden beschouwd. Gebruik de volgende hand leiding voor de inhoud:

* Vaardigheids naam: de naam van de vaardigheid die wordt afgeschaft, die wordt toegewezen aan het @odata.type kenmerk.
* Laatst beschik bare API-versie: de laatste versie van de Azure Cognitive Search open bare API waarmee vaardig heden met de bijbehorende afgeschafte vaardig heden kunnen worden gemaakt/bijgewerkt.
* Einde van de ondersteuning: de laatste dag waarna de bijbehorende vaardigheid als niet-ondersteund wordt beschouwd. Eerder gemaakte vaardig heden moet nog steeds blijven functioneren, maar gebruikers worden aanbevolen een gedeprecieerde vaardigheid te migreren.
* Aanbevelingen: migratie traject vooruit om een ondersteunde vaardigheid te gebruiken. Gebruikers wordt aangeraden de aanbevelingen te volgen om ondersteuning te blijven ontvangen.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Micro soft. skills. Text. NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Laatst beschik bare API-versie

11-11-2017-preview

### <a name="end-of-support"></a>Einde van ondersteuning

15 februari 2019

### <a name="recommendations"></a>Aanbevelingen 

Gebruik in plaats daarvan [micro soft. skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) . Het biedt de meeste functionaliteit van de NamedEntityRecognitionSkill met een hogere kwaliteit. Het bevat ook uitgebreide informatie over de complexe uitvoer velden.

Als u wilt migreren naar de [kwalificatie voor entiteits herkenning](cognitive-search-skill-entity-recognition.md), moet u een of meer van de volgende wijzigingen door voeren in uw vaardigheids definitie. U kunt de vaardigheids definitie bijwerken met behulp van de [Update-vaardigheidset-API](/rest/api/searchservice/update-skillset).

> [!NOTE]
> Momenteel wordt de betrouwbaarheids score als concept niet ondersteund. De `minimumPrecision` para meter bevindt zich op de `EntityRecognitionSkill` voor toekomstig gebruik en voor achterwaartse compatibiliteit.

1. *(Vereist)* Wijzig de `@odata.type` van `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` in `"#Microsoft.Skills.Text.EntityRecognitionSkill"` .

2. *(Optioneel)* Als u de `entities` uitvoer maakt, gebruikt u de `namedEntities` complexe verzamelings uitvoer van de `EntityRecognitionSkill` in plaats daarvan. U kunt de `targetName` in de vaardigheids definitie gebruiken om deze toe te wijzen aan een aantekening met de naam `entities` .

3. *(Optioneel)* Als u de niet expliciet opgeeft `categories` , kan de `EntityRecognitionSkill` retour waarde van een ander type categorieën worden geretourneerd, behalve die van de `NamedEntityRecognitionSkill` . Als dit gedrag niet gewenst is, moet u de `categories` para meter expliciet instellen op `["Person", "Location", "Organization"]` .

    _Voor beelden van migratie definities_

    * Eenvoudige migratie

        _Bij NamedEntityRecognition-vaardigheids definitie_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _After EntityRecognition-vaardigheids definitie_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Enigszins gecompliceerde migratie

        _Bij NamedEntityRecognition-vaardigheids definitie_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _After EntityRecognition-vaardigheids definitie_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Vaardigheid van entiteits herkenning](cognitive-search-skill-entity-recognition.md)