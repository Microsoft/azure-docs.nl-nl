---
title: Materialen overschrijven tijden modelconversie
description: Geeft uitleg over de manier waarop de werk stroom op de conversie tijd wordt overschreven
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 11bd79a1bc88d2605a20744f5a6b6536d754c100
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576639"
---
# <a name="override-materials-during-model-conversion"></a>Materialen overschrijven tijden modelconversie

De materiaal instellingen in het bron model worden gebruikt voor het definiëren van de [PBR-materialen](../../overview/features/pbr-materials.md) die worden gebruikt door de renderer.
Soms geeft de [standaard conversie](../../reference/material-mapping.md) niet de gewenste resultaten en u moet wijzigingen aanbrengen.
Wanneer een model wordt geconverteerd voor gebruik in azure-rendering op afstand, kunt u een bestand voor het overschrijven van materialen opgeven om te bepalen hoe de conversie van materialen per materiaal wordt uitgevoerd.
Als een bestand met de naam `<modelName>.MaterialOverrides.json` wordt gevonden in de invoer container naast het invoer model `<modelName>.<ext>` , wordt het gebruikt als het overschrijvings bestand van het materiaal.

## <a name="the-override-file-used-during-conversion"></a>Het overschrijvings bestand dat wordt gebruikt tijdens de conversie

Stel dat een box-model een enkel materiaal heeft met de naam standaard.
Daarnaast moeten we de kleur van de albedo aanpassen voor gebruik in ARR.
In dit geval kan een `box.MaterialOverrides.json` bestand als volgt worden gemaakt:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

Het `box.MaterialOverrides.json` bestand wordt geplaatst in de invoer container naast `box.fbx` , waarmee wordt aangegeven dat de conversie service de nieuwe instellingen toepast.

### <a name="color-materials"></a>Kleurmaterialen

Het [kleuren materiaal](../../overview/features/color-materials.md) model beschrijft een voortdurend gearceerd Opper vlak dat onafhankelijk is van de belichting.
Kleur materialen zijn handig voor activa die door Photogrammetry-algoritmen worden gemaakt, bijvoorbeeld.
In materiaal onderdrukking bestanden kan een materiaal worden gedeclareerd als een kleur materiaal door in te stellen `unlit` op `true` .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Specifieke patroon kaarten negeren

Soms wilt u het conversie proces specifieke structuur toewijzingen negeren. Dit kan het geval zijn wanneer het model is gegenereerd door een hulp programma waarmee speciale kaarten worden gegenereerd die niet goed zijn begrepen door de renderer. Bijvoorbeeld een ' OpacityMap ' die wordt gebruikt voor het definiëren van iets anders dan dekking, of een model waarin ' NormalMap ' is opgeslagen als ' BumpMap '. (In het laatste geval kunt u "NormalMap" negeren. Dit zorgt ervoor dat het conversie programma "BumpMap" als "NormalMap" gebruikt.)

Het principe is eenvoudig. Voeg een eigenschap toe met de naam `ignoreTextureMaps` en voeg een structuur toewijzing toe die u wilt negeren:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Zie het onderstaande JSON-schema voor een volledige lijst met patroon toewijzingen die u kunt negeren.

### <a name="applying-the-same-overrides-to-multiple-materials"></a>Dezelfde onderdrukkingen Toep assen op meerdere materialen

Standaard wordt een vermelding in het bestand materiaal onderdrukkingen toegepast wanneer de naam overeenkomt met de naam van het materiaal exact.
Omdat het vaak gebruikelijk is dat dezelfde onderdrukking op meerdere materialen van toepassing is, kunt u eventueel een reguliere expressie opgeven als de naam van de vermelding.
Het veld `nameMatching` heeft een standaard waarde `exact` , maar kan worden ingesteld op `regex` om aan te duiden dat de vermelding moet worden toegepast op elk overeenkomend materiaal.
De syntaxis die wordt gebruikt, is hetzelfde als die voor Java script. In het volgende voor beeld ziet u een onderdrukking die van toepassing is op materialen met namen als "Material2", "Material01" en "Material999".

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

Er is Maxi maal één vermelding in een materiaal overschrijvings bestand van toepassing op één enkel materiaal.
Als er een exacte overeenkomst is (bijvoorbeeld `nameMatching` afwezig of gelijk aan `exact` ) voor de naam van het materiaal, wordt deze vermelding gekozen.
Anders wordt de eerste regex-vermelding in het bestand dat overeenkomt met de naam van het materiaal gekozen.

### <a name="getting-information-about-which-entries-applied"></a>Informatie opvragen over welke vermeldingen zijn toegepast

Het [Info bestand](get-information.md#information-about-a-converted-model-the-info-file) dat naar de uitvoer container wordt geschreven, bevat informatie over het aantal overschrijvingen dat wordt gegeven en het aantal materialen dat is overschreven.

## <a name="json-schema"></a>JSON-schema

Hier wordt het volledige JSON-schema voor materiaal bestanden gegeven. Met uitzonde ring van `unlit` en `ignoreTextureMaps` , zijn de beschik bare eigenschappen een subset van de eigenschappen die worden beschreven in de secties op het [kleuren](../../overview/features/color-materials.md) -en [PBR-materiaal](../../overview/features/pbr-materials.md) model.

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Kleurmaterialen](../../overview/features/color-materials.md)
* [PBR-materialen](../../overview/features/pbr-materials.md)
