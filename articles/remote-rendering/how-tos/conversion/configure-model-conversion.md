---
title: De modelconversie configureren
description: Beschrijving van alle para meters voor model conversie
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: 1cb5312e164bac09930497c377f1590b6a77ca05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205316"
---
# <a name="configure-the-model-conversion"></a>De modelconversie configureren

In dit hoofd stuk worden de opties voor de model conversie gedocumenteerd.

## <a name="settings-file"></a>Instellingen bestand

Als een bestand `<modelName>.ConversionSettings.json` met de naam wordt gevonden in de invoer container naast het invoer model `<modelName>.<ext>` , wordt dit gebruikt om aanvullende configuratie voor het model conversie proces te bieden.
Bijvoorbeeld, `box.ConversionSettings.json` wordt gebruikt bij de conversie `box.gltf` .

De inhoud van het bestand moet voldoen aan het volgende JSON-schema:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        },
        "metadataKeys": {
            "type": "array",
            "items": {
                "type": "string"
            }
        }
    },
    "additionalProperties" : false
}
```

Een voorbeeld bestand `box.ConversionSettings.json` kan zijn:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Geometry-para meters

* `scaling` -Met deze para meter wordt een model uniform geschaald. Schalen kan worden gebruikt om een model te verg Roten of verkleinen, bijvoorbeeld om een model op een tabel bovenaan weer te geven.
Schalen is ook belang rijk wanneer een model wordt gedefinieerd in andere eenheden dan meters, omdat de rendering-engine meters verwacht.
Als bijvoorbeeld een model in centimeters is gedefinieerd, moet het model op de juiste grootte worden gerenderd met een schaal van 0,01.
Sommige brongegevens indelingen (bijvoorbeeld. fbx) bieden een hint voor eenheids schaal, in welk geval de conversie het model impliciet schaalt naar meter eenheden. De impliciete schaal aanpassing van de bron indeling wordt boven op de schaal parameter toegepast.
De uiteindelijke schaal factor wordt toegepast op de geometrie hoekpunten en de lokale trans formaties van de knoop punten van de scène grafiek. De schaal van de trans formatie van de basis entiteit blijft ongewijzigd.

* `recenterToOrigin` -Geeft aan dat een model moet worden geconverteerd zodat het selectie vakje wordt gecentreerd bij de oorsprong.
Als een bron model ver van de oorsprong wordt overgeplaatst, kunnen drijvende-komma precisie problemen leiden tot rendering-artefacten.
Het model centreren kan in deze situatie helpen.

* `opaqueMaterialDefaultSidedness` -De rendering-engine veronderstelt dat dekkende materialen dubbelzijdig worden weer gegeven.
Als deze veronderstelling niet van toepassing is op een bepaald model, moet deze para meter worden ingesteld op ' SingleSided '. Zie [ :::no-loc text="single sided"::: rendering](../../overview/features/single-sided-rendering.md)voor meer informatie.

### <a name="material-de-duplication"></a>Materiaal van de duplicatie

* `deduplicateMaterials` -Met deze para meter wordt de automatische verdubbeling van materialen die dezelfde eigenschappen en bitmappatronen delen, in-of uitgeschakeld. De-duplicatie vindt plaats nadat de overschrijvingen van het materiaal zijn verwerkt. Het is standaard ingeschakeld.

* Als zelfs na het weglaten van een model meer dan 65.535 materialen worden gedupliceerd, probeert de service materialen samen te voegen met vergelijk bare eigenschappen. Als laatste redmiddel worden alle materialen die de limiet overschrijden, vervangen door een rood fout materiaal.

![Afbeelding toont twee kubussen van 68.921 gekleurde drie hoeken.](media/mat-dedup.png?raw=true)

Twee kubussen van 68.921 gekleurde drie hoeken. Links: vóór de verdubbeling met 68.921-kleuren materialen. Rechts: na de ontdubbeling met 64.000-kleuren materialen. De limiet is 65.535 materialen. (Zie [limieten](../../reference/limits.md).)

### <a name="color-space-parameters"></a>Kleur ruimte parameters

De rendering-engine verwacht dat kleur waarden in lineaire ruimte worden weer gegeven.
Als een model is gedefinieerd met behulp van gamma ruimte, moeten deze opties worden ingesteld op True.

* `gammaToLinearMaterial` -Materiaal kleuren van gamma-ruimte naar lineaire ruimte converteren
* `gammaToLinearVertex` - :::no-loc text="vertex"::: Kleuren van gamma-ruimte naar lineaire ruimte converteren

> [!NOTE]
> Voor FBX-bestanden worden deze instellingen standaard ingesteld op `true` . Voor alle andere bestands typen is de standaard waarde `false` .

### <a name="scene-parameters"></a>Scène parameters

* `sceneGraphMode` -Definieert hoe de scène grafiek in het bron bestand wordt geconverteerd:
  * `dynamic` (standaard): alle objecten in het bestand worden weer gegeven als [entiteiten](../../concepts/entities.md) in de API en kunnen onafhankelijk worden getransformeerd. De knooppunt hiërarchie tijdens runtime is identiek aan de structuur in het bron bestand.
  * `static`: Alle objecten worden weer gegeven in de API, maar ze kunnen niet onafhankelijk worden getransformeerd.
  * `none`: De scène grafiek is samengevouwen in één object.

Elke modus heeft verschillende runtime-prestaties. In `dynamic` de modus worden de prestatie kosten lineair geschaald met het aantal [entiteiten](../../concepts/entities.md) in de grafiek, zelfs als er geen deel wordt verplaatst. Gebruik `dynamic` de modus alleen wanneer dit nodig is om onderdelen afzonderlijk te verplaatsen, bijvoorbeeld voor de animatie ' explosie weergave '.

In de `static` modus wordt de volledige scène grafiek geëxporteerd, maar onderdelen in deze grafiek hebben een constante trans formatie ten opzichte van het hoofd gedeelte. Het hoofd knooppunt van het object kan echter nog steeds worden verplaatst, gedraaid of geschaald zonder aanzienlijke prestatie kosten. Daarnaast retour neren [ruimtelijke query's](../../overview/features/spatial-queries.md) afzonderlijke onderdelen en kan elk deel worden gewijzigd via [status onderdrukkingen](../../overview/features/override-hierarchical-state.md). In deze modus is de runtime overhead per object verwaarloosbaar. Het is ideaal voor grote scènes waarbij u nog steeds een controle per object nodig hebt, maar geen trans formatie per object wijzigt.

De `none` modus heeft de minste runtime overhead en ook iets betere laad tijden. De inspectie of trans formatie van afzonderlijke objecten is niet mogelijk in deze modus. Use cases zijn bijvoorbeeld Photogrammetry modellen die in de eerste plaats geen zinvolle scène grafiek hebben.

> [!TIP]
> Veel toepassingen laden meerdere modellen. U moet de conversie parameters voor elk model optimaliseren, afhankelijk van hoe het wordt gebruikt. Als u bijvoorbeeld het model van een auto wilt weer geven om de gebruiker uit te voeren en in detail te controleren, moet u deze converteren naar de `dynamic` modus. Als u de auto echter ook in een omgeving voor weer geven wilt plaatsen, kan dat model worden geconverteerd met `sceneGraphMode` ingesteld op `static` of zelfs `none` .

### <a name="physics-parameters"></a>Fysische para meters

* `generateCollisionMesh` -Als u ondersteuning nodig hebt voor [ruimtelijke query's](../../overview/features/spatial-queries.md) in een model, moet deze optie zijn ingeschakeld. In het ergste geval kan het maken van een botsend net de conversie tijd verdubbelen. Modellen met botsingen kunnen langer worden geladen en wanneer u een `dynamic` scène diagram gebruikt, hebben ze ook een hogere runtime-prestatie overhead. Voor algemene optimale prestaties moet u deze optie uitschakelen voor alle modellen waarvoor u geen ruimtelijke query's nodig hebt.

### <a name="unlit-materials"></a>Unlit-materialen

* `unlitMaterials` -Standaard maakt de conversie het voor keur om [PBR-materialen](../../overview/features/pbr-materials.md)te maken. Met deze optie geeft u aan dat het conversie programma alle materialen als [kleur materiaal](../../overview/features/color-materials.md) behandelt. Als u gegevens hebt die al een verlichting bevatten, zoals modellen die zijn gemaakt via Photogrammetry, kunt u met deze optie snel de juiste conversie afdwingen voor alle materialen, zonder dat u [elk materiaal](override-materials.md) afzonderlijk hoeft te overschrijven.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Conversie van oudere FBX-indelingen, met een Phong-materiaal model

* `fbxAssumeMetallic` -Oudere versies van de FBX-indeling definiëren hun materialen met behulp van een Phong-materiaal model. Het conversie proces moet afleiden hoe deze materialen worden toegewezen aan het pbr- [model](../../overview/features/pbr-materials.md)van de renderer. Dit werkt normaal gesp roken goed, maar een dubbel zinnigheid kan zich voordoen wanneer een materiaal geen bitmappatronen, hoge indirecte waarden en een niet-grijze albedo kleur heeft. In dit geval moet de conversie worden gekozen om te kiezen uit de prioriteit van de hoge, ondoorzichtige waarden, waarbij u een zeer reflecterende, metallische stof definieert waarbij de kleur van de albedo wordt opgelost, of de prioriteit van de albedo kleur kan worden bepaald, zoals een glanzend kleurige plastic. Het conversie proces veronderstelt standaard dat zeer onduidelijke waarden een metaal materiaal impliceren in gevallen waarin ambiguïteit van toepassing is. Deze para meter kan worden ingesteld op `false` om naar het tegenovergestelde te scha kelen.

### <a name="coordinate-system-overriding"></a>Het coördinaten systeem overschrijven

* `axis` -Als u de coördinaten systeem-eenheid vectoren wilt overschrijven. Standaard waarden zijn `["+x", "+y", "+z"]` . In theorie heeft de FBX-indeling een kop waarin deze vectoren worden gedefinieerd en de conversie gebruikt die informatie om de scène te transformeren. De glTF-indeling definieert ook een vast coördinaten systeem. In de praktijk hebben sommige assets onjuiste gegevens in hun koptekst of zijn ze opgeslagen met een andere coördinaten systeem Conventie. Met deze optie kunt u het coördinaten systeem overschrijven om te compenseren. Bijvoorbeeld: `"axis" : ["+x", "+z", "-y"]` de Z-as en Y-as worden uitgewisseld en de coördinaten van de y-as worden bijkomen.

### <a name="node-meta-data"></a>Meta gegevens van knoop punt

* `metadataKeys` -Hiermee kunt u sleutels van eigenschappen van knoop punt meta gegevens opgeven die u in het conversie resultaat wilt laten blijven. U kunt exacte sleutels of joker tekens opgeven. Joker tekens hebben de notatie ABC * en komen overeen met een sleutel die begint met ' ABC '. Ondersteunde typen waarden voor meta gegevens zijn `bool` , `int` , en `float` `string` .

    Voor GLTF-bestanden zijn deze gegevens afkomstig uit het [extras-object op knoop punten](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#nodeextras). Voor FBX-bestanden zijn deze gegevens afkomstig uit de `Properties70` gegevens op `Model nodes` . Raadpleeg de documentatie van uw 3D-hulp programma voor meer informatie.

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex"::: Formatteer

Het is mogelijk om de :::no-loc text="vertex"::: indeling van een net aan te passen aan de handels nauwkeurigheid voor het besparen van geheugen. Met een lagere geheugen capaciteit kunt u grotere modellen laden of betere prestaties behaalt. Afhankelijk van uw gegevens kan dit echter een onjuiste indeling hebben die de weergave kwaliteit aanzienlijk kan beïnvloeden.

> [!CAUTION]
> Het wijzigen :::no-loc text="vertex"::: van de indeling zou een laatste redmiddel moeten zijn wanneer modellen niet meer in het geheugen passen, of bij het optimaliseren van de best mogelijke prestaties. Wijzigingen kunnen eenvoudig rendering-artefacten introduceren, zowel duidelijke als subtiele items. Tenzij u weet wat er moet worden gezocht voor, moet u de standaard instelling niet wijzigen.

Deze aanpassingen zijn mogelijk:

* Specifieke gegevens stromen kunnen expliciet worden opgenomen of uitgesloten.
* De nauw keurigheid van gegevens stromen kan worden verkleind om het geheugen gebruik te verminderen.

De volgende `vertex` sectie in het `.json` bestand is optioneel. Voor elk gedeelte dat niet expliciet is opgegeven, wordt de standaard instelling van de conversie service hersteld.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Door een onderdeel af te dwingen naar `NONE` wordt gegarandeerd dat het uitvoer gaas niet de respectieve stroom heeft.

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>Onderdeel indelingen per :::no-loc text="vertex"::: Stream

Deze indelingen zijn toegestaan voor de respectieve onderdelen:

| :::no-loc text="Vertex"::: -onderdeel | Ondersteunde indelingen (vet = standaard) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, geen |
|kleur1| 8_8_8_8_UNSIGNED_NORMALIZED, **geen**|
|opmaakprofiel| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, geen |
|lijn| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, geen |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, geen |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, geen |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, geen |

#### <a name="supported-component-formats"></a>Ondersteunde onderdeel indelingen

De geheugen footprint van de indelingen zijn als volgt:

| Indeling | Beschrijving | Bytes per :::no-loc text="vertex"::: |
|:-------|:------------|:---------------|
|32_32_FLOAT|twee onderdelen met een volledige zwevende komma precisie|8
|16_16_FLOAT|twee onderdelen, half zwevende punt precisie|4
|32_32_32_FLOAT|nauw keurigheid van drie onderdelen met drijvende komma|12
|16_16_16_16_FLOAT|vier-punt precisie voor halve drijvende komma|8
|8_8_8_8_UNSIGNED_NORMALIZED|een byte van vier componenten, genormaliseerd naar `[0; 1]` bereik|4
|8_8_8_8_SIGNED_NORMALIZED|een byte van vier componenten, genormaliseerd naar `[-1; 1]` bereik|4

#### <a name="best-practices-for-component-format-changes"></a>Aanbevolen procedures voor het wijzigen van de indeling van onderdelen

* `position`: Het is niet vaak voldoende nauw keurigheid te verminderen. **16_16_16_16_FLOAT** introduceert duidelijk kwantisatiefouten artefacten, zelfs voor kleine modellen.
* `normal`, `tangent` , `binormal` : Doorgaans worden deze waarden samen gewijzigd. Tenzij er merk bare belichtings artefacten zijn die het resultaat zijn van normale kwantisatiefouten, is er geen reden om de nauw keurigheid te verg Roten. In sommige gevallen kunnen deze onderdelen echter worden ingesteld op **geen**:
  * `normal`, `tangent` en `binormal` zijn alleen nodig wanneer ten minste één materiaal in het model moet worden gebrand. In ARR is dit het geval wanneer een [PBR-materiaal](../../overview/features/pbr-materials.md) op elk gewenst moment op het model wordt gebruikt.
  * `tangent` en `binormal` zijn alleen nodig als een van de Lit-materialen gebruikmaakt van een normaal kaart patroon.
* `texcoord0`, `texcoord1` : Texture-coördinaten kunnen een gereduceerde nauw keurigheid (**16_16_FLOAT**) gebruiken wanneer hun waarden binnen het `[0; 1]` bereik blijven en wanneer de geadresseerde bitmappatronen een maximum grootte hebben van 2048 x 2048 pixels. Als deze limieten worden overschreden, zal de kwaliteit van de toewijzing van het patroon afnemen.

#### <a name="example"></a>Voorbeeld

Stel dat u een Photogrammetry-model hebt, wat de belichtings geïntegreerde in de bitmappatronen heeft. Alles wat nodig is om het model weer te geven, zijn :::no-loc text="vertex"::: Positions en texture-coördinaten.

Het conversie programma moet er standaard van uitgaan dat u op een bepaald moment PBR-materialen wilt gebruiken op een model, zodat er `normal` `tangent` `binormal` gegevens voor u worden gegenereerd. Daarom is het geheugen gebruik per vertex `position` (12 bytes) + `texcoord0` (8 bytes) + `normal` (4 bytes) + `tangent` (4 bytes) + `binormal` (4 bytes) = 32 bytes. Grotere modellen van dit type kunnen eenvoudig een groot aantal modellen hebben, waardoor er :::no-loc text="vertices"::: meerdere GB geheugen kan worden opgedeeld. Dergelijke grote hoeveel heden gegevens zijn van invloed op de prestaties. het kan zelfs voor komen dat er onvoldoende geheugen beschikbaar is.

Als u zeker weet dat u nooit dynamische verlichting nodig hebt voor het model en u zeker weet dat alle Texture-coördinaten binnen het `[0; 1]` bereik vallen, kunt u, `normal` `tangent` , en `binormal` tot en met de `NONE` `texcoord0` halve precisie () instellen, `16_16_FLOAT` wat resulteert in slechts 16 bytes per :::no-loc text="vertex"::: . Als u de netgegevens in tweeën knipt, kunt u grotere modellen laden en mogelijk de prestaties verbeteren.

## <a name="memory-optimizations"></a>Geheugen optimalisaties

Het geheugen gebruik van geladen inhoud kan een knel punt op het rendering-systeem worden. Als de nettolading van het geheugen te groot wordt, kan dit de weergave prestaties in beslag nemen of kan het model niet samen worden geladen. In dit artikel worden enkele belang rijke strategieën besproken om het geheugen gebruik te verminderen.

### <a name="instancing"></a>Instantiëren

Instancing is een concept waarbij netten opnieuw worden gebruikt voor onderdelen met afzonderlijke ruimtelijke trans formaties, in tegens telling tot elk onderdeel dat verwijst naar een eigen unieke geometrie. Instancing heeft aanzienlijke invloed op de geheugen ruimte.
Voor beelden van use cases voor instancing zijn de schroeven in een engine model of stoelen in een architectuur model.

> [!NOTE]
> Instancing kan het geheugen gebruik (en dus ook de laad tijd) aanzienlijk verbeteren, maar de verbeteringen van de prestaties van de rendering zijn echter onbeduidend.

De omrekenings service respecteert instancing als onderdelen in overeenstemming worden aangegeven in het bron bestand. De conversie voert echter geen extra diepe analyse van netgegevens uit om herbruikbare onderdelen te identificeren. Het hulp programma voor het maken van inhoud en de export pijplijn zijn dus de doorslaggevende criteria voor de juiste instancing-installatie.

Een eenvoudige manier om te testen of instancing-informatie tijdens de conversie verloren gaat, is door een overzicht te geven van de [uitvoer statistieken](get-information.md#example-info-file), met name het `numMeshPartsInstanced` lid. Als de waarde voor `numMeshPartsInstanced` groter is dan nul, duidt dit erop dat er netten worden gedeeld door verschillende exemplaren.

#### <a name="example-instancing-setup-in-3ds-max"></a>Voor beeld: instancing instellen in 3ds Max

[Auto Desk 3ds Max](https://www.autodesk.de/products/3ds-max) heeft afzonderlijke methoden **`Copy`** voor het klonen **`Instance`** van objecten en **`Reference`** die zich anders gedragen met betrekking tot instancing in het geëxporteerde `.fbx` bestand.

![Klonen in 3ds Max.](./media/3dsmax-clone-object.png)

* **`Copy`** : In deze modus wordt het net gekloond, dus er wordt geen instancing gebruikt ( `numMeshPartsInstanced` = 0).
* **`Instance`** : De twee objecten delen dezelfde mesh, dus instancing wordt gebruikt ( `numMeshPartsInstanced` = 1).
* **`Reference`** : DISTINCT-wijzigings functies kunnen worden toegepast op de geometrie, zodat de Exporter een conservatieve benadering kiest en geen instancing ( `numMeshPartsInstanced` = 0) gebruikt.


### <a name="depth-based-composition-mode"></a>Op diepte gebaseerde compositie modus

Als het geheugen een probleem is, configureert u de renderer met de [op diepte gebaseerde compositie modus](../../concepts/rendering-modes.md#depthbasedcomposition-mode). In deze modus wordt GPU-Payload gedistribueerd over meerdere Gpu's.

### <a name="decrease-vertex-size"></a>Hoekpunt grootte verlagen

Zoals beschreven in de sectie [Aanbevolen procedures voor het wijzigen van onderdelen opmaak](configure-model-conversion.md#best-practices-for-component-format-changes) , kan het aanpassen van de vertex indeling de geheugen ruimte verminderen. Deze optie moet echter de laatste redmiddel zijn.

### <a name="texture-sizes"></a>Patroon grootten

Afhankelijk van het type scenario, kan de hoeveelheid textuur gegevens zwaarder zijn dan het geheugen dat wordt gebruikt voor netgegevens. Photogrammetry-modellen zijn kandidaten.
De conversie configuratie biedt geen manier om bitmappatronen automatisch te schalen. Als dat nodig is, moet het schalen van het patroon worden uitgevoerd als een vooraf verwerkings stap aan de client zijde. Tijdens de conversie wordt echter een geschikte [patroon compressie-indeling](/windows/win32/direct3d11/texture-block-compression-in-direct3d-11)gekozen:

* `BC1` voor ondoorzichtige kleur structuren
* `BC7` voor Bronk leur patronen met alfa kanaal

Aangezien notatie `BC7` twee maal zo groot is als de geheugen footprint in vergelijking tot `BC1` , is het belang rijk om ervoor te zorgen dat de invoer structuren geen alfa kanaal bevatten.

## <a name="typical-use-cases"></a>Typische gebruiks voorbeelden

Het vinden van goede import instellingen voor een bepaalde use-case kan een tijdrovend proces zijn. Aan de andere kant kunnen de conversie-instellingen een aanzienlijke invloed hebben op de prestaties van de runtime.

Er zijn bepaalde klassen van use cases die in aanmerking komen voor specifieke optimalisaties. Hieronder vindt u enkele voor beelden.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Use-case: architecturale visualisatie/grote overzichten van buiten

* Deze soorten scènes zijn meestal statisch, wat betekent dat ze geen verplaatste onderdelen nodig hebben. De kan daarom `sceneGraphMode` worden ingesteld op `static` of zelfs `none` , waardoor de prestaties van de uitvoering verbeteren. Met `static` modus kan het hoofd knooppunt van de scène nog steeds worden verplaatst, gedraaid en geschaald, bijvoorbeeld om dynamisch te scha kelen tussen 1:1 schaal (voor de eerste persoons weergave) en een tabel weergave.

* Wanneer u onderdelen wilt verplaatsen, betekent dat doorgaans ook dat u ondersteuning nodig hebt voor raycasts of andere [ruimtelijke query's](../../overview/features/spatial-queries.md), zodat u deze onderdelen in de eerste plaats kunt kiezen. Daarentegen, als u niet van plan bent om iets rond te verplaatsen, is de kans groot dat u deze ook niet nodig hebt om deel te nemen aan ruimtelijke query's en daarom de vlag uit te scha kelen `generateCollisionMesh` . Deze switch heeft aanzienlijke invloed op de conversie tijden, laad tijden en runtime per kader update kosten.

* Als de toepassing geen [geknipte abonnementen](../../overview/features/cut-planes.md)gebruikt, `opaqueMaterialDefaultSidedness` moet de vlag worden uitgeschakeld. De prestatie verbetering is doorgaans 20%-30%. Knip abonnementen kunnen nog steeds worden gebruikt, maar er zijn geen back-upobjecten bij het zoeken naar de binnenste delen van objecten, waardoor teller-intuïtief wordt weer gegeven. Zie [ :::no-loc text="single sided"::: rendering](../../overview/features/single-sided-rendering.md)voor meer informatie.

### <a name="use-case-photogrammetry-models"></a>Use-case: Photogrammetry-modellen

Bij het renderen van Photogrammetry-modellen is er doorgaans geen scène grafiek nodig, zodat u de `sceneGraphMode` kunt instellen op `none` . Omdat deze modellen zelden een complexe scène grafiek bevatten om te beginnen met, zou de impact van deze optie echter onbeduidend moeten zijn.

Omdat er al een belichting is geïntegreerde in de bitmappatronen, is er geen dynamische belichting nodig. Daarom:

* Stel de `unlitMaterials` vlag in op `true` om alle materialen om te zetten in Unlit- [kleuren materialen](../../overview/features/color-materials.md).
* Verwijder overbodige gegevens uit de vertex notatie. Zie het bovenstaande [voor beeld](#example) .

### <a name="use-case-visualization-of-compact-machines-etc"></a>Use-case: visualisatie van compacte machines, enzovoort.

In deze gebruiks gevallen hebben de modellen vaak zeer veel details binnen een klein volume. De renderer is sterk geoptimaliseerd om dergelijke gevallen goed te verwerken. De meeste optimalisaties die in de vorige use-case worden genoemd, zijn echter niet van toepassing:

* Afzonderlijke onderdelen moeten selecteerbaar en verplaatsbaar zijn, dus de `sceneGraphMode` moet resteren `dynamic` .
* Ray-casts zijn doorgaans een integraal onderdeel van de toepassing. er moeten dus botsingen worden gegenereerd.
* Knip abonnementen zien er beter uit terwijl de `opaqueMaterialDefaultSidedness` markering is ingeschakeld.

## <a name="deprecated-features"></a>Verouderde functies:

Het opgeven van instellingen met de niet-model-specifieke bestands naam `conversionSettings.json` wordt nog steeds ondersteund, maar is afgeschaft.
Gebruik in plaats daarvan de model-specifieke bestands naam `<modelName>.ConversionSettings.json` .

Het gebruik van een `material-override` instelling voor het identificeren van een bestand voor het [overschrijven van materiaal](override-materials.md) in het bestand met conversie-instellingen wordt nog steeds ondersteund, maar is afgeschaft. Gebruik in plaats daarvan de model-specifieke bestands naam `<modelName>.MaterialOverrides.json` .

## <a name="next-steps"></a>Volgende stappen

* [Modelconversie](model-conversion.md)
* [Kleurmaterialen](../../overview/features/color-materials.md)
* [PBR-materialen](../../overview/features/pbr-materials.md)
* [Materialen overschrijven tijden modelconversie](override-materials.md)