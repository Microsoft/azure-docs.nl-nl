---
title: Informatie ophalen over omzettingen
description: Informatie ophalen over omzettingen
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 89ec0ad40822785457e988cf9e0f9bd6d00ed81f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576622"
---
# <a name="get-information-about-conversions"></a>Informatie ophalen over omzettingen

## <a name="information-about-a-conversion-the-result-file"></a>Informatie over een conversie: het resultaat bestand

Wanneer de conversie service een Asset converteert, wordt een samen vatting van eventuele problemen naar een ' resultaat bestand ' geschreven. Als een bestand bijvoorbeeld `buggy.gltf` wordt geconverteerd, bevat de uitvoer container een bestand met de naam `buggy.result.json` .

In het resultaat bestand worden eventuele fouten en waarschuwingen weer gegeven die zijn opgetreden tijdens de conversie en wordt een resultaat samengevat, een van `succeeded` `failed` of `succeeded with warnings` .
Het resultaat bestand is gestructureerd als een JSON-matrix met objecten, die allemaal een teken reeks eigenschap hebben die is een van,,, `warning` `error` `internal warning` `internal error` en `result` . Er is Maxi maal één fout ( `error` of `internal error` ) en er is er altijd een `result` .

## <a name="example-result-file"></a>Voor beeld van *resultaat* bestand

In het volgende voor beeld wordt een conversie beschreven waarbij een arrAsset is gegenereerd. Omdat er echter een structuur ontbreekt, is de resulterende arrAsset mogelijk niet zo bedoeld.

```JSON
[
  {"warning":"4004","title":"Missing texture","details":{"texture":"buggy_baseColor.png","material":"buggy_col"}},
  {"result":"succeeded with warnings"}
]
```

## <a name="information-about-a-converted-model-the-info-file"></a>Informatie over een geconverteerd model: het info bestand

Het arrAsset-bestand dat door de conversie service wordt geproduceerd, is uitsluitend bedoeld voor gebruik door de rendering-service. Het kan echter voor komen dat u toegang wilt krijgen tot informatie over een model zonder een rendering-sessie te starten. Ter ondersteuning van deze werk stroom plaatst de conversie service een JSON-bestand naast het arrAsset-bestand in de uitvoer container. Als een bestand bijvoorbeeld `buggy.gltf` wordt geconverteerd, bevat de uitvoer container een bestand dat `buggy.info.json` naast het geconverteerde activum wordt genoemd `buggy.arrAsset` . Het bevat informatie over het bron model, het geconverteerde model en de conversie zelf.

## <a name="example-info-file"></a>Voor beeld van *info* bestand

Hier volgt een voor *beeld van een bestand dat* wordt gegenereerd door het converteren van een bestand met de naam `buggy.gltf` :

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "materialOverrides": {
        "numOverrides": 4,
        "numOverriddenMaterials": 4
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Informatie in het info bestand

### <a name="the-files-section"></a>De sectie *files*

Deze sectie bevat de opgegeven bestands namen.

* `input`: De naam van het bron bestand.
* `output`: De naam van het uitvoer bestand, wanneer de gebruiker een niet-standaard naam heeft opgegeven.

### <a name="the-conversionsettings-section"></a>De sectie *conversionSettings*

Deze sectie bevat een kopie van de [ConversionSettings](configure-model-conversion.md#settings-file) die is opgegeven bij het converteren van het model.

### <a name="the-inputinfo-section"></a>De sectie *inputInfo*

In deze sectie vindt u informatie over de indeling van het bron bestand.

* `sourceAssetExtension`: De bestands extensie van het bron bestand.
* `sourceAssetFormat`: Een beschrijving van de bron bestands indeling.
* `sourceAssetFormatVersion`: De versie van de bron bestands indeling.
* `sourceAssetGenerator`: De naam van het hulp programma dat het bron bestand heeft gegenereerd, indien beschikbaar.

### <a name="the-materialoverrides-section"></a>De sectie *materialOverrides*

In deze sectie vindt u informatie over het overschrijven van [materialen](override-materials.md) wanneer een materiaal overschrijving aan de conversie service werd door gegeven.
De sectie bevat de volgende informatie:
* `numOverrides`: Het aantal onderdrukkings vermeldingen dat is gelezen uit het bestand material override.
* `numOverriddenMaterials`: Het aantal materialen dat is overschreven.

### <a name="the-inputstatistics-section"></a>De sectie *inputStatistics*

Deze sectie bevat informatie over de bron scène. Er zijn vaak verschillen tussen de waarden in deze sectie en de equivalente waarden in het hulp programma waarmee het bron model is gemaakt. Dergelijke verschillen worden verwacht, omdat het model tijdens de export-en conversie stappen wordt gewijzigd.

* `numMeshes`: Het aantal netonderdelen, waarbij elk deel naar één enkel materiaal kan verwijzen.
* `numFaces`: Het totale aantal _drie hoeken_ in het hele model. Houd er rekening mee dat het net wordt getriangulatie tijdens de conversie. Dit aantal draagt bij aan de grens van de polygoon in de [standaard weergave server grootte](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons).
* `numVertices`: Het totale aantal hoek punten in het hele model.
* `numMaterial`: Het totale aantal materialen in het hele model.
* `numFacesSmallestMesh`: Het aantal drie hoeken in het kleinste Maas van het model.
* `numFacesBiggestMesh`: Het aantal drie hoeken in de grootste mesh van het model.
* `numNodes`: Het aantal knoop punten in de scène grafiek van het model.
* `numMeshUsagesInScene`: Het aantal malen dat knoop punten verwijzen naar een Maas wijdte. Meer dan één knoop punt kan verwijzen naar hetzelfde net.
* `maxNodeDepth`: De maximale diepte van de knoop punten in het scène diagram.

### <a name="the-outputinfo-section"></a>De sectie *outputInfo*

In deze sectie vindt u algemene informatie over de gegenereerde uitvoer.

* `conversionToolVersion`: Versie van het model conversieprogramma.
* `conversionHash`: Een hash van de gegevens binnen de arrAsset die kan bijdragen aan rendering. Kan worden gebruikt om te begrijpen of de conversie service een ander resultaat heeft geproduceerd bij het opnieuw uitvoeren van hetzelfde bestand.

### <a name="the-outputstatistics-section"></a>De sectie *outputStatistics*

In deze sectie worden gegevens vastgelegd die zijn berekend op basis van de geconverteerde activa.

* `numMeshPartsCreated`: Het aantal mazen in de arrAsset. Dit kan verschillen van `numMeshes` in de `inputStatistics` sectie, omdat instancing wordt beïnvloed door het conversie proces.
* `numMeshPartsInstanced`: Het aantal mazen dat opnieuw wordt gebruikt in de arrAsset.
* `recenteringOffset`: Als de `recenterToOrigin` optie in de [ConversionSettings](configure-model-conversion.md) is ingeschakeld, is deze waarde de vertaling waarmee het geconverteerde model weer naar de oorspronkelijke positie wordt verplaatst.
* `boundingBox`: De grenzen van het model.

## <a name="deprecated-features"></a>Verouderde functies:

De conversie service schrijft de bestanden `stdout.txt` en `stderr.txt` naar de uitvoer container en deze zijn de enige bron van waarschuwingen en fouten.
Deze bestanden zijn nu afgeschaft. Gebruik in plaats daarvan [resultaat bestanden](#information-about-a-conversion-the-result-file) voor dit doel einde.

## <a name="next-steps"></a>Volgende stappen

* [Modelconversie](model-conversion.md)
* [De modelconversie configureren](configure-model-conversion.md)
