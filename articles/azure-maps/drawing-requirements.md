---
title: Pakket vereisten in Microsoft Azure Maps Creator maken (preview)
description: Meer informatie over de vereisten voor het teken pakket voor het converteren van uw ontwerp bestanden voor de locatie om gegevens toe te wijzen
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/08/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: bed5373cbb9967bd1d86bb80bb3a449430c3b6ae
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044778"
---
# <a name="drawing-package-requirements"></a>Vereisten voor tekenpakketten


> [!IMPORTANT]
> Azure Maps Creator-services zijn momenteel beschikbaar als openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

U kunt geüploade teken pakketten converteren naar kaart gegevens met behulp van de [Azure Maps conversie service](/rest/api/maps/conversion). In dit artikel worden de teken pakket vereisten voor de conversie-API beschreven. Als u een voorbeeld pakket wilt weer geven, kunt u het voorbeeld [teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples)downloaden.

## <a name="prerequisites"></a>Vereisten

Het teken pakket bevat tekeningen die zijn opgeslagen in de DWG-indeling. Dit is de systeem eigen bestands indeling voor de AutoCAD®-software van auto Desk.

U kunt kiezen welke CAD-software de tekeningen in het teken pakket moet produceren.  

De [Azure Maps conversie service](/rest/api/maps/conversion) converteert het tekening pakket naar kaart gegevens. De conversie service werkt met de AutoCAD DWG-bestands indeling. `AC1032` is de interne indelings versie voor de DWG-bestanden, en het is een goed idee om te selecteren `AC1032` voor de interne versie van de DWG-bestands indeling.  

## <a name="glossary-of-terms"></a>Verklarende woordenlijst

Hier volgen enkele termen en definities die belang rijk zijn wanneer u dit artikel leest.

| Termijn  | Definitie |
|:-------|:------------|
| Laag | Een AutoCAD DWG-laag.|
| Niveau | Een gebied van een gebouw bij een ingestelde uitbrei ding. Bijvoorbeeld de basis van een gebouw. |
| XREF  |Een bestand in AutoCAD DWG-bestands indeling (. DWG), gekoppeld aan de primaire tekening als externe verwijzing.  |
| Functie | Een object dat een geometrie combineert met meer meta gegevens. |
| Functie klassen | Een veelvoorkomende blauw druk voor functies. Een *eenheid* is bijvoorbeeld een functie klasse en een *kantoor* is een functie. |

## <a name="drawing-package-structure"></a>Structuur van tekening pakket

Een teken pakket is een zip-archief dat de volgende bestanden bevat:

* DWG-bestanden in AutoCAD DWG-bestands indeling.
* Een _manifest.jsin_ het bestand waarin de DWG-bestanden in het teken pakket worden beschreven.

Het teken pakket moet worden ingepakt in één archief bestand met de extensie. zip. De DWG-bestanden kunnen op een wille keurige manier binnen het pakket worden georganiseerd, maar het manifest bestand moet zich in de hoofdmap van het gezipte pakket bevinden. De volgende secties beschrijven de vereisten voor de DWG-bestanden, het manifest bestand en de inhoud van deze bestanden.

## <a name="dwg-files-requirements"></a>Vereisten voor DWG-bestanden

Er is één DWG-bestand vereist voor elk niveau van de faciliteit. De gegevens van het niveau moeten zich in één DWG-bestand bevinden. Alle externe verwijzingen (_xrefs_) moeten aan de bovenliggende tekening worden gebonden. Daarnaast elk DWG-bestand:

* U moet de _buiten_ -en _eenheids_ lagen definiëren. U kunt eventueel de volgende optionele lagen definiëren: _wand_, _deur_, _UnitLabel_, _zone_ en _ZoneLabel_.
* Mag geen functies van meerdere niveaus bevatten.
* Mag geen functies van meerdere faciliteiten bevatten.
* Moet verwijzen naar hetzelfde meet systeem en dezelfde maat eenheid als andere DWG-bestanden in het teken pakket.

De [Azure Maps-conversie service](/rest/api/maps/conversion) kan de volgende functie klassen uit een DWG-bestand extra heren:

* Niveaus
* Eenheden
* Zones
* Openingen
* Lijnen
* Verticale binnendringingen

Alle conversie taken resulteren in een minimale set standaard categorieën: room, structuur. Wall, openen. deur, zone en faciliteit. Aanvullende categorieën zijn voor elke categorie naam waarnaar wordt verwezen door objecten.  

Een DWG-laag moet functies van één klasse bevatten. Klassen mogen geen laag delen. Eenheden en wanden kunnen bijvoorbeeld geen laag delen.

DWG-lagen moeten ook voldoen aan de volgende criteria:

* De oorsprong van tekeningen voor alle DWG-bestanden moeten worden uitgelijnd op dezelfde breedte graad en lengte graad.
* Elk niveau moet dezelfde richting hebben als de andere niveaus.
* Zelf-intersecte veelhoeken worden automatisch gerepareerd en de [Azure Maps conversie service](/rest/api/maps/conversion) genereert een waarschuwing. Het is raadzaam om de gerepareerde resultaten hand matig te controleren, omdat deze mogelijk niet overeenkomen met de verwachte resultaten.

Alle laag entiteiten moeten een van de volgende typen zijn: lijn, poly lijn, veelhoek, cirkel vormige boog, cirkel, ellips (gesloten) of tekst (enkele regel). Andere entiteits typen worden genegeerd.

In de onderstaande tabel ziet u een overzicht van de ondersteunde entiteits typen en de functies van geconverteerde kaarten voor elke laag. Als een laag entiteits typen bevat die niet worden ondersteund, worden deze entiteiten genegeerd door de [Azure Maps conversie service](/rest/api/maps/conversion) .  

| Laag | Entiteitstypen | Geconverteerde functies |
| :----- | :-------------------| :-------
| [Postzegel](#exterior-layer) | Veelhoek, poly lijn (gesloten), cirkel, ellips (gesloten) | Niveaus
| [Eenheid](#unit-layer) |  Veelhoek, poly lijn (gesloten), cirkel, ellips (gesloten) | Verticale indringings, eenheid
| [Muren](#wall-layer)  | Veelhoek, poly lijn (gesloten), cirkel, ellips (gesloten) | Niet van toepassing. Zie de laag met de [wand](#wall-layer)voor meer informatie.
| [Door](#door-layer) | Veelhoek, poly lijn, lijn, CircularArc, cirkel | Openingen
| [Zone](#zone-layer) | Veelhoek, poly lijn (gesloten), cirkel, ellips (gesloten) | Zone
| [UnitLabel](#unitlabel-layer) | Tekst (één regel) | Niet van toepassing. Deze laag kan alleen eigenschappen toevoegen aan de eenheids functies van de laag eenheden. Zie de [UnitLabel-laag](#unitlabel-layer)voor meer informatie.
| [ZoneLabel](#zonelabel-layer) | Tekst (één regel) | Niet van toepassing. Deze laag kan alleen eigenschappen toevoegen aan zone functies van de ZonesLayer. Zie de [ZoneLabel-laag](#zonelabel-layer)voor meer informatie.

In de volgende secties worden de vereisten voor elke laag gedetailleerd beschreven.

### <a name="exterior-layer"></a>Buiten-laag

Het DWG-bestand voor elk niveau moet een laag bevatten om de omtrek van dat niveau te definiëren. Deze laag wordt aangeduid als de *buitenste* laag. Als een faciliteit bijvoorbeeld twee niveaus bevat, moet deze twee DWG-bestanden hebben, met een buitenste laag voor elk bestand.

Ongeacht het aantal entiteits tekeningen dat zich in de buiten-laag bevindt, bevat de [resulterende faciliteit gegevensset](tutorial-creator-indoor-maps.md#create-a-feature-stateset) slechts één niveau functie voor elk DWG-bestand. Aanvullend:

* Buitens moeten worden getekend als veelhoek, poly lijn (gesloten), cirkel of ellips (gesloten).
* Buiten kant kan overlappen, maar worden in één geometrie opgelost.
* De resulterende niveau functie moet ten minste 4 vier Kante meters zijn.
* De resulterende niveau functie mag niet groter zijn dan 400 vier Kante meters.

Als de laag meerdere overlappende polylijnen bevat, worden de polylinen opgelost in een functie met één niveau. Als de laag echter meerdere niet-overlappende polylijnen bevat, heeft de resulterende niveau functie een multi-veelhoekige weer gave.

U ziet een voor beeld van de buitenste laag als de overzichtskop in het [voorbeeld teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unit-layer"></a>Eenheids laag

Het DWG-bestand voor elk niveau definieert een laag met eenheden. Eenheden zijn navigeerbaar ruimten in het gebouw, zoals kant oren, hal, traps en liften. Als de `VerticalPenetrationCategory` eigenschap is gedefinieerd, worden navigeerbaar-eenheden die meerdere niveaus omvatten, zoals liften en trappen, geconverteerd naar verticale Indringings functies. Verticale indringings functies die elkaar overlappen, worden toegewezen aan één `setid` .

De laag eenheden moet voldoen aan de volgende vereisten:

* Eenheden moeten worden getekend als veelhoek, poly lijn (gesloten), cirkel of ellips (gesloten).
* Eenheden moeten binnen de grenzen van de buitenste omtrek van de faciliteit vallen.
* Eenheden mogen niet gedeeltelijk elkaar overlappen.
* Eenheden mogen geen zelf-overlappende geometrie bevatten.

Noem een eenheid door een tekst object in de laag UnitLabel te maken en plaats het object binnen de grenzen van de eenheid. Zie de [UnitLabel-laag](#unitlabel-layer)voor meer informatie.

U ziet een voor beeld van de laag eenheden in het [voorbeeld teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Laag van wand

Het DWG-bestand voor elk niveau kan een laag bevatten waarin de fysieke gebieden van wanden, kolommen en andere bouw structuren worden gedefinieerd.

* Wanden moeten worden getekend als veelhoek, poly lijn (gesloten), cirkel of ellips (gesloten).
* De laag of lagen van de wand mogen alleen geometrie bevatten die wordt geïnterpreteerd als bouw structuur.

U ziet een voor beeld van de laag wanden in het [voorbeeld teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="door-layer"></a>Laag van deur

U kunt een DWG-laag met deuren toevoegen. Elke deur moet de rand van een eenheid van de laag van de eenheid overlappen.

Openingen in een Azure Maps-gegevensset worden weer gegeven als een segment met één lijn dat de grenzen van meerdere eenheden overlapt. In de volgende afbeeldingen ziet u hoe geometrie in de laag van de deur kan worden geconverteerd om functies in een gegevensset te openen.

![Vier grafische afbeeldingen waarin de stappen voor het genereren van openingen worden weer gegeven](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Zone laag

Het DWG-bestand voor elk niveau kan een zone-laag bevatten die de fysieke gebieden van zones definieert. Een zone is een niet-navigeerbaar ruimte die kan worden benoemd en gerenderd. Zones kunnen meerdere niveaus omvatten en samen worden gegroepeerd met de eigenschap zoneSetId.

* Zones moeten worden getekend als veelhoek, poly lijn (gesloten) of ellips (gesloten).
* Zones kunnen elkaar overlappen.
* Zones kunnen binnen of buiten de buitenste omtrek van de faciliteit vallen.

Noem een zone door een tekst object te maken in de ZoneLabel-laag en het tekst object binnen de grenzen van de zone te plaatsen. Zie [ZoneLabel Layer](#zonelabel-layer)voor meer informatie.

U kunt een voor beeld van de zone laag bekijken in het [voorbeeld teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unitlabel-layer"></a>UnitLabel-laag

Het DWG-bestand voor elk niveau kan een UnitLabel-laag bevatten. De UnitLabel-laag voegt een naam eigenschap toe aan eenheden die zijn geëxtraheerd uit de laag van de eenheid. Voor eenheden met een naam eigenschap kunnen meer details worden opgegeven in het manifest bestand.

* Eenheidslabels moeten tekst entiteiten van één regel zijn.
* Eenheidslabels moeten binnen de grenzen van hun eenheid vallen.
* Eenheden mogen geen meerdere tekst entiteiten bevatten in de UnitLabel-laag.

U ziet een voor beeld van de laag UnitLabel in het [voorbeeld teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="zonelabel-layer"></a>ZoneLabel-laag

Het DWG-bestand voor elk niveau kan een ZoneLabel-laag bevatten. Deze laag voegt een naam eigenschap toe aan zones die worden geëxtraheerd uit de laag van de zone. In zones met een naam eigenschap kunnen meer details worden opgegeven in het manifest bestand.

* Labels voor zones moeten tekst entiteiten van één regel zijn.
* Labels voor zones moeten binnen de grenzen van hun zone vallen.
* Zones mogen geen meerdere tekst entiteiten bevatten in de ZoneLabel-laag.

U ziet een voor beeld van de laag ZoneLabel in het [voorbeeld teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Vereisten voor manifest bestand

De map zip moet een manifest bestand op het hoofd niveau van de map bevatten en het bestand moet de naam **manifest.jsop** hebben. Hierin worden de DWG-bestanden beschreven zodat de [Azure Maps conversie service](/rest/api/maps/conversion) de inhoud kan parseren. Alleen de bestanden die door het manifest worden geïdentificeerd, worden opgenomen. Bestanden die zich in de map zip bevinden, maar niet op de juiste manier worden vermeld in het manifest, worden genegeerd.

De bestands paden in het `buildingLevels` object van het manifest bestand moeten relatief zijn ten opzichte van de hoofdmap van de map zip. De naam van het DWG-bestand moet exact overeenkomen met de naam van het niveau van de faciliteit. Een DWG-bestand voor het niveau ' Basement ' is bijvoorbeeld ' basement. DWG '. Een DWG-bestand voor niveau 2 heeft de naam ' level_2. DWG '. Gebruik een onderstrepings teken als de naam van uw niveau een spatie heeft.

Hoewel er vereisten gelden wanneer u de manifest-objecten gebruikt, zijn niet alle objecten vereist. De volgende tabel bevat de vereiste en optionele objecten voor versie 1,1 van de [Azure Maps conversie service](/rest/api/maps/conversion).

| Object | Vereist | Beschrijving |
| :----- | :------- | :------- |
| `version` | true |Schema versie van manifest. Op dit moment wordt alleen versie 1,1 ondersteund.|
| `directoryInfo` | true | Geeft een overzicht van de geografische locatie-en contact gegevens. Het kan ook worden gebruikt voor een overzicht van de geografische en contact gegevens van een inzittende. |
| `buildingLevels` | true | Hiermee geeft u de niveaus van de gebouwen en de bestanden die het ontwerp van de niveaus bevatten. |
| `georeference` | true | Bevat numerieke geografische gegevens voor de faciliteit tekening. |
| `dwgLayers` | true | Een lijst met de namen van de lagen en elke laag bevat de namen van de eigen functies. |
| `unitProperties` | onjuist | Kan worden gebruikt om meer meta gegevens voor de onderdelen van de eenheid in te voegen. |
| `zoneProperties` | onjuist | Kan worden gebruikt voor het invoegen van meer meta gegevens voor de zone-functies. |

In de volgende secties worden de vereisten voor elk object gedetailleerd beschreven.

### `directoryInfo`

| Eigenschap  | Type | Vereist | Beschrijving |
|-----------|------|----------|-------------|
| `name`      | tekenreeks | true   |  De naam van het gebouw. |
| `streetAddress`|    tekenreeks |    onjuist    | Het adres van het gebouw. |
|`unit`     | tekenreeks    |  onjuist    |  Eenheid in gebouw. |
| `locality` |    tekenreeks |    onjuist |    De naam van een gebied, groep of regio. Bijvoorbeeld "overlake" of "Central District". De lokale locatie maakt geen deel uit van het post adres. |
| `adminDivisions` |    JSON-matrix met teken reeksen |    onjuist     | Een matrix met adres ontwerpen (land, staat, plaats) of (land, prefectuur, stad, stad). Gebruik ISO 3166-land codes en ISO 3166-2 staat/regio codes. |
| `postalCode` |    tekenreeks    | onjuist    | De e-mail BIC-code. |
| `hoursOfOperation` |    tekenreeks |     onjuist | Voldoet aan de [OSM Openings uren](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) -indeling. |
| `phone`    | tekenreeks |    onjuist |    Het telefoon nummer dat aan het gebouw is gekoppeld. De land code moet worden meegenomen. |
| `website`    | tekenreeks |    onjuist    | De website die aan het gebouw is gekoppeld. Moet beginnen met http of https. |
| `nonPublic` |    booleaans    | onjuist | Vlag waarmee wordt aangegeven of het gebouw open is. |
| `anchorLatitude` | numeriek |    onjuist | Breedte graad van een faciliteit anker (punaise). |
| `anchorLongitude` | numeriek |    onjuist | Lengte graad van een faciliteit anker (punaise). |
| `anchorHeightAboveSeaLevel`  | numeriek | onjuist | Hoogte van de grond vloer van de faciliteit boven Sea-niveau, in meters. |
| `defaultLevelVerticalExtent` | numeriek | onjuist | De standaard hoogte (breedte) van een niveau van deze faciliteit dat moet worden gebruikt wanneer een niveau `verticalExtent` niet is gedefinieerd. |

### `buildingLevels`

Het `buildingLevels` object bevat een JSON-matrix met de niveaus van gebouwen.

| Eigenschap  | Type | Vereist | Beschrijving |
|-----------|------|----------|-------------|
|`levelName`    |tekenreeks    |true |    Beschrijvende niveau naam. Bijvoorbeeld: Floor 1, lobby, Blue parkeren of Basement.|
|`ordinal` | geheel getal |    true | Bepaalt de verticale volg orde van niveaus. Elke faciliteit moet een niveau hebben met een rang telwoord van 0. |
|`heightAboveFacilityAnchor` | numeriek | onjuist |    Niveau hoogte boven het anker in meters. |
| `verticalExtent` | numeriek | onjuist | De hoogte (breedte) van het niveau in meters. |
|`filename` |    tekenreeks |    true |    Bestandssysteempad naar het bestands systeem van de CAD-tekening voor een gebouw niveau. Deze moet relatief zijn ten opzichte van de hoofdmap van het zip-bestand van het gebouw. |

### `georeference`

| Eigenschap  | Type | Vereist | Beschrijving |
|-----------|------|----------|-------------|
|`lat`    | numeriek |    true |    Decimale weer gave van graden met een breedte graad op de oorspronkelijke positie van de tekening. De oorsprongs coördinaten moeten zich in WGS84 Web Mercator () bekomen `EPSG:3857` .|
|`lon`    |numeriek|    true|    Decimale weer gave van graden met de lengte van de faciliteit tekening. De oorsprongs coördinaten moeten zich in WGS84 Web Mercator () bekomen `EPSG:3857` . |
|`angle`|    numeriek|    true|   De hoek rechtsom, in graden, tussen True en de verticale as (Y) van de tekening.   |

### `dwgLayers`

| Eigenschap  | Type | Vereist | Beschrijving |
|-----------|------|----------|-------------|
|`exterior`    |tekenreeksmatrix|    true|    Namen van lagen die het buitenste bouw profiel definiëren.|
|`unit`|    tekenreeksmatrix|    true|    Namen van lagen waarmee eenheden worden gedefinieerd.|
|`wall`|    tekenreeksmatrix    |onjuist|    Namen van lagen waarmee wanden worden gedefinieerd.|
|`door`    |tekenreeksmatrix|    onjuist   | Namen van lagen waarmee deuren worden gedefinieerd.|
|`unitLabel`    |tekenreeksmatrix|    onjuist    |Namen van lagen waarmee de namen van eenheden worden gedefinieerd.|
|`zone` | tekenreeksmatrix    | onjuist    | Namen van lagen waarmee zones worden gedefinieerd.|
|`zoneLabel` | tekenreeksmatrix |     onjuist |    Namen van lagen waarmee de namen van zones worden gedefinieerd.|

### `unitProperties`

Het `unitProperties` object bevat een JSON-matrix met de eigenschappen van de eenheid.

| Eigenschap  | Type | Vereist | Beschrijving |
|-----------|------|----------|-------------|
|`unitName`    |tekenreeks    |true    |De naam van de eenheid die aan deze record moet worden gekoppeld `unitProperty` . Deze record is alleen geldig wanneer er een label overeenkomst `unitName` in de lagen is gevonden `unitLabel` . |
|`categoryName`|    tekenreeks|    onjuist    |Categorie naam. Raadpleeg de [categorie](https://aka.ms/pa-indoor-spacecategories)voor een volledige lijst met categorieën. |
|`navigableBy`| tekenreeksmatrix |    onjuist    |Hiermee worden de typen navigatie agenten aangegeven die de eenheid kunnen passeren. Deze eigenschap informeert de wayfinding-mogelijkheden. De toegestane waarden zijn: `pedestrian` , `wheelchair` , `machine` , `bicycle` , `automobile` , `hiredAuto` , `bus` , `railcar` , `emergency` , `ferry` , `boat` en `disallowed` .|
|`routeThroughBehavior`|    tekenreeks|    onjuist    |Het gedrag van de route voor de eenheid. De toegestane waarden zijn `disallowed` , `allowed` en `preferred` . De standaardwaarde is `allowed`.|
|`occupants`    |matrix van directoryInfo-objecten |onjuist    |De lijst met inzittenden voor de eenheid. |
|`nameAlt`|    tekenreeks|    onjuist|    De alternatieve naam van de eenheid. |
|`nameSubtitle`|    tekenreeks    |onjuist|    Subtitel van de eenheid. |
|`addressRoomNumber`|    tekenreeks|    onjuist|    Kamer, eenheid, appartement of suite nummer van de eenheid.|
|`verticalPenetrationCategory`|    tekenreeks|    onjuist| Als deze eigenschap is gedefinieerd, is de resulterende functie een verticale indringing (VRT) in plaats van een eenheid. U kunt VRTs gebruiken om naar andere VRT-functies te gaan in de bovenstaande niveaus. Verticale indringing is een [categorie](https://aka.ms/pa-indoor-spacecategories) naam. Als deze eigenschap is gedefinieerd, `categoryName` wordt de eigenschap overschreven door `verticalPenetrationCategory` . |
|`verticalPenetrationDirection`|    tekenreeks|    onjuist    |Als `verticalPenetrationCategory` is gedefinieerd, definieert u eventueel de geldige reis richting. De toegestane waarden zijn: `lowToHigh` , `highToLow` , en `both` `closed` . De standaardwaarde is `both`.|
| `nonPublic` | booleaans | onjuist | Hiermee wordt aangegeven of de eenheid open is voor het publiek. |
| `isRoutable` | booleaans | onjuist | Als deze eigenschap is ingesteld op `false` , kunt u niet naar of door de eenheid gaan. De standaardwaarde is `true`. |
| `isOpenArea` | booleaans | onjuist | Hiermee kan de navigatie agent de eenheid invoeren zonder dat er een openings koppeling met de eenheid nodig is. Deze waarde is standaard ingesteld op `true` voor eenheden zonder openingen en `false` voor eenheden met openingen. Hand matig instellen op `isOpenArea` `false` een eenheid zonder openingen resulteert in een waarschuwing, omdat de resulterende eenheid niet bereikbaar is voor een andere agent.|

### `zoneProperties`

Het `zoneProperties` object bevat een JSON-matrix met zone-eigenschappen.

| Eigenschap  | Type | Vereist | Beschrijving |
|-----------|------|----------|-------------|
|zone naam        |tekenreeks    |true    |De naam van de zone die aan de record moet worden gekoppeld `zoneProperty` . Deze record is alleen geldig wanneer een label overeenkomst `zoneName` wordt gevonden in de `zoneLabel` laag van de zone.  |
|categoryName|    tekenreeks|    onjuist    |Categorie naam. Raadpleeg de [categorie](https://aka.ms/pa-indoor-spacecategories)voor een volledige lijst met categorieën. |
|zoneNameAlt|    tekenreeks|    onjuist    |Alternatieve naam van de zone.  |
|zoneNameSubtitle|    tekenreeks |    onjuist    |Ondertitel van de zone. |
|zoneSetId|    tekenreeks |    onjuist    | Stel ID in om een relatie tussen meerdere zones tot stand te brengen, zodat deze kunnen worden opgevraagd of als groep kan worden geselecteerd. Bijvoorbeeld zones die meerdere niveaus beslaan. |

### <a name="sample-drawing-package-manifest"></a>Voor beeld van teken pakket manifest

Hieronder ziet u het manifest bestand voor het voorbeeld teken pakket. Zie voor [beeld tekening pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples)om het hele pakket te downloaden.

#### <a name="manifest-file"></a>Manifest bestand

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

Als uw teken pakket aan de vereisten voldoet, kunt u de [Azure Maps conversie service](/rest/api/maps/conversion) gebruiken om het pakket te converteren naar een kaart gegevensset. Vervolgens kunt u de gegevensset gebruiken om een binnenste kaart te genereren met behulp van de module kaarten.

> [!div class="nextstepaction"]
>[Creator (preview) voor kaarten in de binnenste](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Zelf studie: een koppeling maken (preview) in het binnenste](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamische stijlen voor kaarten in de binnenste](indoor-map-dynamic-styling.md)