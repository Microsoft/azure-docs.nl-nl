---
title: Invoer toewijzen aan uitvoer velden
titleSuffix: Azure Cognitive Search
description: Haal en verrijkende brongegevens velden op en wijs deze toe aan uitvoer velden in een Azure Cognitive Search-index.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 58bb87d5af785d3cffd96f3bd02477f97ed967a9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001300"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>AI-verrijkte velden toewijzen aan een Doorzoek bare index

![Indexerings fasen](./media/cognitive-search-output-field-mapping/indexer-stages-output-field-mapping.png "indexerings fasen")

In dit artikel leert u hoe u verrijkte invoer velden kunt toewijzen aan uitvoer velden in een Doorzoek bare index. Zodra u [een vaardig heden hebt gedefinieerd](cognitive-search-defining-skillset.md), moet u de uitvoer velden toewijzen van alle vaardig heden waarmee waarden rechtstreeks worden bijgedragen aan een bepaald veld in uw zoek index.

Uitvoer veld Toewijzingen zijn vereist voor het verplaatsen van inhoud van verrijkte documenten naar de index.  Het verrijkte document is in feite een structuur van informatie en hoewel er wel sprake is van ondersteuning voor complexe typen in de index, wilt u mogelijk de gegevens van de uitgebreide boom structuur transformeren naar een eenvoudigere type (bijvoorbeeld een matrix met teken reeksen). Met de toewijzingen van het uitvoer veld kunt u gegevens vorm transformaties uitvoeren door gegevens af te vlakken. Uitvoer veld toewijzingen worden altijd uitgevoerd na de uitvoering van de vaardigy, hoewel het mogelijk is dat deze fase wordt uitgevoerd zelfs als er geen vakkennisset is gedefinieerd.

Voor beelden van uitvoer veld toewijzingen:

* Als onderdeel van uw vaardig heden extraheert u de namen van de organisaties die worden vermeld op de pagina's van uw document. Nu wilt u elk van deze organisatie namen toewijzen aan een veld in uw index van het type EDM. Collection (EDM. String).

* Als onderdeel van uw vaardig heden hebt u een nieuw knoop punt met de naam ' document/translated_text ' gemaakt. U wilt de informatie op dit knoop punt toewijzen aan een specifiek veld in uw index.

* U hebt geen vaardig heden, maar u kunt wel een complex type indexeren vanuit een Cosmos DB-Data Base. U wilt een knoop punt op dat complexe type verkrijgen en toewijzen aan een veld in uw index.

> [!NOTE]
> We hebben onlangs de functionaliteit van toewijzings functies ingeschakeld voor uitvoer veld toewijzingen. Zie [veld toewijzings functies](./search-indexer-field-mappings.md#field-mapping-functions) voor meer informatie over het toewijzen van functies

## <a name="use-outputfieldmappings"></a>OutputFieldMappings gebruiken

Als u velden wilt toewijzen, voegt `outputFieldMappings` u deze toe aan de definitie van de Indexeer functie, zoals hieronder wordt weer gegeven:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
```

De hoofd tekst van de aanvraag is als volgt gestructureerd:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions",
            "mappingFunction": {
                "name": "base64Decode"
            }
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```

Stel voor elke toewijzing van het uitvoer veld de locatie van de gegevens in de verrijkte document structuur (sourceFieldName) en de naam van het veld in waarnaar wordt verwezen in de index (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Informatie afvlakken van complexe typen 

Het pad in een sourceFieldName kan bestaan uit één element of meerdere elementen. In het bovenstaande voor beeld ```/document/content/sentiment``` vertegenwoordigt een enkele numerieke waarde, terwijl ```/document/content/organizations/*/description``` verschillende beschrijvingen van de organisatie worden aangeduid. 

Als er meerdere elementen zijn, worden deze ' afgevlakt ' in een matrix die elk van de elementen bevat. 

```/document/content/organizations/*/description```In het voor beeld worden de gegevens in het veld *beschrijvingen* als een vlakke matrix met beschrijvingen weer gegeven voordat de indexering wordt geïndexeerd:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Dit is een belang rijk beginsel, dus we bieden een ander voor beeld. Stel dat u een matrix van complexe typen hebt als onderdeel van de verrijkings structuur. Stel dat er een lid met de naam customEntities is dat een matrix met complexe typen bevat zoals hieronder wordt beschreven.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

We gaan ervan uit dat uw index een veld bevat met de naam ' ziekten ' van het type verzameling (EDM. String), waar u elk van de namen van de entiteiten wilt opslaan. 

Dit kan eenvoudig worden gedaan met behulp van het \* symbool ' ', als volgt:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Met deze bewerking worden de namen van de customEntities-elementen in één matrix met teken reeksen als volgt afgevlakt:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Volgende stappen
Zodra u uw verrijkte velden aan Doorzoek bare velden hebt toegewezen, kunt u de veld kenmerken voor elk van de Doorzoek bare velden instellen [als onderdeel van de definitie van de index](search-what-is-an-index.md).

Zie [veld toewijzingen in Azure Cognitive Search-Indexeer functies](search-indexer-field-mappings.md)voor meer informatie over het toewijzen van velden.