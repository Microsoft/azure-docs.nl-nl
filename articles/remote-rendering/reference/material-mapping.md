---
title: Materiaaltoewijzing voor modelindelingen
description: Beschrijft de standaard conversie van model bron indelingen naar PBR-materiaal
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024041"
---
# <a name="material-mapping-for-model-formats"></a>Materiaaltoewijzing voor modelindelingen

Wanneer een bron activum [als een model wordt geconverteerd](../how-tos/conversion/model-conversion.md), maakt het conversie programma voor elk [net](../concepts/meshes.md) [materialen](../concepts/materials.md) . De manier waarop materialen worden gemaakt, kan worden [overschreven](../how-tos/conversion/override-materials.md). Standaard wordt in de conversie echter [PBR-materialen](../overview/features/pbr-materials.md)gemaakt. Omdat elke bron bestands indeling, zoals FBX, eigen conventies gebruikt voor het definiëren van materialen, moeten deze conventies worden toegewezen aan de PBR-material-para meters van Azure remote rendering. 

In dit artikel vindt u de exacte toewijzingen die worden gebruikt voor het converteren van materialen van bron assets naar runtime-materialen.

## <a name="gltf"></a>glTF

Bijna alles van de glTF 2,0 spec wordt ondersteund in azure remote rendering, met uitzonde ring van *EmissiveFactor* en *EmissiveTexture*.

De volgende tabel toont de toewijzing:

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   metaal                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   ruw                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   bedekking                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphaMode. ondoorzichtig  |   alphaClipEnabled = False, isTransparent = False |
|   alphaMode. MASK    |   alphaClipEnabled = True, isTransparent = False  |
|   alphaMode. BLEND   |   isTransparent = True     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

Elk bitmappatroon in glTF kan een `texCoord` waarde hebben, die ook wordt ondersteund in de Azure remote rendering-materialen.

### <a name="embedded-textures"></a>Inge sloten structuren

Structuren die zijn Inge sloten in *\* bin* -of *\* GLB* -bestanden worden ondersteund.

### <a name="supported-gltf-extension"></a>Ondersteunde glTF-extensie

Naast de basis functieset, ondersteunt Azure remote rendering de volgende glTF-extensies:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): komt overeen met [kleur materialen](../overview/features/color-materials.md). Voor *emissive* -materialen is het raadzaam om deze uitbrei ding te gebruiken.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): in plaats van metallic textures kunt u glossiness-structuren (diffuus en onscherp) bieden. De implementatie van Azure remote rendering volgt direct de conversie formules van de uitbrei ding.

## <a name="fbx"></a>FBX

De FBX-indeling is gesloten-bron-en FBX-materialen zijn niet compatibel met PBR-materialen in het algemeen. FBX maakt gebruik van een complexe beschrijving van Opper vlakken met veel unieke para meters en eigenschappen, en **niet allemaal worden gebruikt door de Azure remote rendering-pijp lijn**.

> [!IMPORTANT]
> De pijp lijn conversie van de Azure remote rendering model ondersteunt alleen **FBX 2011 en hoger**.

De FBX-indeling definieert een conservatieve benadering voor materialen, maar er zijn slechts twee typen in de officiële FBX-specificatie:

* *Lambert* : wordt niet vaak al enige tijd gebruikt, maar wordt wel ondersteund door tijdens de conversie naar Phong te converteren.
* *Phong* -bijna alle materialen en de meeste hulpprogram ma's voor inhoud gebruiken dit type.

Het Phong-model is nauw keuriger en wordt gebruikt als het *enige* model voor FBX-materialen. Hieronder wordt het *FBX-materiaal* genoemd.

> Maya maakt gebruik van twee aangepaste extensies voor FBX door aangepaste eigenschappen te definiëren voor PBR-en Stingray-typen van een materiaal. Deze gegevens zijn niet opgenomen in de FBX-specificatie, waardoor deze niet wordt ondersteund door Azure remote rendering op dit moment.

FBX-materialen gebruiken het ' diffuus en onscherpste SpecularLevel-concept, dus als u een diffuse structuur wilt omzetten in een albedo-kaart, moeten we de andere para meters berekenen om ze van diffuus af te trekken.

> Alle kleuren en bitmappatronen in FBX zijn in de sRGB-ruimte (ook wel gamma-ruimte genoemd), maar externe rendering van Azure werkt met lineaire ruimte tijdens de visualisatie en aan het einde van het frame worden alle gegevens naar sRGB-ruimte geconverteerd. Met de pijp lijn voor het inventariseren van Azure remote rendering worden alles geconverteerd naar lineaire ruimte om het te verzenden als voor bereide gegevens naar de renderer.

In deze tabel ziet u hoe structuren worden toegewezen uit FBX-materialen aan Azure remote rendering-materialen. Sommige daarvan worden niet rechtstreeks gebruikt, maar in combi natie met andere bitmappatronen die deel nemen aan de formules (bijvoorbeeld het diffuse bitmappatroon):

| FBX | Azure Remote Rendering |
|:-----|:----|
| AmbientColor | Bedekking-kaart   |
| DiffuseColor | *gebruikt voor albedo, Metaaling* |
| TransparentColor | *gebruikt voor alfa kanaal van albedo* |
| TransparencyFactor | *gebruikt voor alfa kanaal van albedo* |
| Dekking | *gebruikt voor alfa kanaal van albedo* |
| SpecularColor | *gebruikt voor albedo, metaal, grofheid* |
| SpecularFactor| *gebruikt voor albedo, metaal, grofheid* |
| ShininessExponent | *gebruikt voor albedo, metaal, grofheid* |
| NormalMap | NormalMap |
| Grijs | *geconverteerd naar NormalMap* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

De bovenstaande toewijzing is het meest complexe deel van de materiaal conversie, omdat er veel veronderstellingen zijn die moeten worden gemaakt. Deze hypo Thesen worden hieronder beschreven.

Hieronder worden enkele definities gebruikt:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Red ∗ 0,2125 +  `Specular` . Groen ∗ 0,7154 + `Specular` . Blue ∗ 0,0721
* `DiffuseBrightness` = 0,299 * `Diffuse` . Red<sup>2</sup> + 0,587 * `Diffuse` . Groen<sup>2</sup> + 0,114 * `Diffuse` . Blauw<sup>2</sup>
* `SpecularBrightness` = 0,299 * `Specular` . Red<sup>2</sup> + 0,587 * `Specular` . Groen<sup>2</sup> + 0,114 * `Specular` . Blauw<sup>2</sup>
* `SpecularStrength` = Max ( `Specular` . Rood, `Specular` . Groen, `Specular` . Meng

De formule SpecularIntensity wordt [hier](https://en.wikipedia.org/wiki/Luma_(video))opgehaald.
De formule voor de helderheid wordt in deze [specificatie](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)beschreven.

### <a name="roughness"></a>Ruw

`Roughness` wordt berekend op basis van `Specular` en `ShininessExponent` [deze formule](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf)wordt gebruikt. De formule is een benadering van de grove-exponent van de Phong:

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Metaal

`Metalness` wordt berekend op basis van `Diffuse` en `Specular` met behulp [van deze formule uit de glTF-specificatie](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js).

Het idee hier is dat we de vergelijking oplossen: AX<sup>2</sup> + BX + C = 0.
In principe worden dielectric-Opper vlakken weer gegeven rond 4% licht op een reflecterend manier en is de rest diffuus. Metalen Opper vlakken geven geen licht op een diffuse manier, maar allemaal op een manier.
Deze formule bevat enkele nadelen omdat er geen manier is om onderscheid te maken tussen glanzende plastic en glanzende metalen Opper vlakken. We gaan ervan uit dat het Opper vlak een metallische eigenschappen heeft en dat de glanzende plastic/rubber-Opper vlakken er mogelijk niet op de verwachte manier uitzien.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedo

`Albedo` wordt berekend op basis van `Diffuse` , `Specular` en `Metalness` .

Zoals beschreven in de sectie van de Metaaling, weer spie gelen dielectric Opper vlakken rond 4% licht.  
Het idee hier is om lineair interpoleren tussen `Dielectric` en `Metal` kleuren met `Metalness` waarde als factor. Als de metaaling is `0.0` , is het afhankelijk van het moment dat deze een donkere kleur heeft (als reflecteel hoog is) of diffuus niet verandert (als er geen reflecterend is). Als de metaaling een grote waarde is, verdwijnt de diffuse kleur ten opzichte van de spiegel kleur.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` is berekend met de bovenstaande formule, maar het Alfa kanaal vereist extra berekeningen. De FBX-indeling is vague over transparantie en heeft verschillende manieren om deze te definiëren. Verschillende hulp middelen voor inhoud gebruiken verschillende methoden. Het is hier een idee om ze in één formule samen te voegen. Sommige assets worden onjuist weer gegeven als transparant, maar als ze niet op een gemeen schappelijke manier worden gemaakt.

Dit wordt berekend op basis van `TransparentColor` , `TransparencyFactor` , `Opacity` :

Als `Opacity` is gedefinieerd, gebruikt u het vervolgens rechtstreeks: `AlbedoAlpha`  =  `Opacity` else  
Als `TransparencyColor` is gedefinieerd, dan `AlbedoAlpha` = 1,0-(() `TransparentColor` . Rood + `TransparentColor` . Groen + `TransparentColor` . Blauw)/3,0) anders  
If `TransparencyFactor` , then `AlbedoAlpha` = 1,0- `TransparencyFactor`

De uiteindelijke `Albedo` kleur heeft vier kanalen, waarbij de wordt gecombineerd `AlbedoRGB` met `AlbedoAlpha` .

### <a name="summary"></a>Samenvatting

Als u hier wilt samenvatten, `Albedo` wordt de oorspronkelijke dichtheid in de buurt van het origineel weer gegeven `Diffuse` `Specular` . Anders wordt het Opper vlak weer gegeven als een metallisch Opper vlak en verliest de diffuse kleur. Het Opper vlak ziet er mooi en reflectie uit als het `ShininessExponent` groot genoeg is en `Specular` helder is. Als dat niet het geval is, ziet u dat het Opper vlak er onveranderd en nauwelijks in de omgeving

### <a name="known-issues"></a>Bekende problemen

* De huidige formule werkt niet goed voor een eenvoudige gekleurde geometrie. Als dat `Specular` helder genoeg is, worden alle geometrieën reflecterende metalen Opper vlakken zonder kleur. De tijdelijke oplossing is hier om `Specular` te verlagen tot 30% van de oorspronkelijke of om de conversie-instelling [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)te gebruiken.
* PBR-materialen zijn onlangs toegevoegd aan `Maya` en `3DS Max` hulpprogram ma's voor het maken van inhoud. Ze gebruiken aangepaste, door de gebruiker gedefinieerde zwarte box-eigenschappen om deze door te geven aan FBX. Deze aanvullende eigenschappen worden niet door Azure rendering gelezen omdat deze niet zijn gedocumenteerd en de indeling is gesloten.

## <a name="next-steps"></a>Volgende stappen

* [Modelconversie](../how-tos/conversion/model-conversion.md)
* [Materialen overschrijven tijdens model conversie](../how-tos/conversion/override-materials.md)
