---
title: Conversie fouten en waarschuwingen voor Azure Maps tekening
description: Meer informatie over de conversie fouten en waarschuwingen die u kunt door lopen tijdens het gebruik van de Azure Maps conversie service. Lees de aanbevelingen voor het oplossen van de fouten en de waarschuwingen, met enkele voor beelden.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: cecc19f0984ce1801d50e5cbda73e98a01e2825b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906213"
---
# <a name="drawing-conversion-errors-and-warnings"></a>Fouten en waarschuwingen over tekenconversies

Met de [Azure Maps conversie service](/rest/api/maps/conversion) kunt u geüploade teken pakketten naar kaart gegevens converteren. Teken pakketten moeten voldoen aan de vereisten voor het [teken pakket](drawing-requirements.md). Als er niet aan een of meer vereisten wordt voldaan, worden er fouten of waarschuwingen door de conversie service geretourneerd. In dit artikel vindt u een overzicht van de conversie fout-en waarschuwings codes, met aanbevelingen voor het oplossen ervan. Het bevat ook enkele voor beelden van tekeningen die ertoe kunnen leiden dat de conversie service deze codes retourneert.

De conversie service wordt uitgevoerd als er conversie waarschuwingen zijn. Het is echter raadzaam om alle waarschuwingen te controleren en op te lossen. Er wordt een waarschuwing gegeven dat een deel van de conversie wordt genegeerd of automatisch is opgelost. Als u de waarschuwingen niet oplost, kan dit leiden tot fouten in de laatste processen.

## <a name="general-warnings"></a>Algemene waarschuwingen

### <a name="geometrywarning"></a>**geometryWarning**

#### <a name="description-for-geometrywarning"></a>*Beschrijving voor geometryWarning*

Een **geometryWarning** treedt op wanneer de tekening een ongeldige entiteit bevat. Een ongeldige entiteit is een entiteit die niet voldoet aan de geometrische beperkingen. Voor beelden van een ongeldige entiteit zijn een self-doorsnede veelhoek of een niet-gesloten poly lijn in een laag die alleen gesloten geometrie ondersteunt.

De conversie service kan geen toewijzings functie maken van een ongeldige entiteit en wordt in plaats daarvan genegeerd.

#### <a name="examples-for-geometrywarning"></a>*Voor beelden voor geometryWarning*

* In de twee onderstaande afbeeldingen ziet u voor beelden van zelf-snij veelhoeken.

     ![Voor beeld van een niet-overlappende veelhoek, voor beeld 1.](./media/drawing-conversion-error-codes/geometry-warning-1.png)

     ![Voor beeld van een zelf-overlappende veelhoek, bijvoorbeeld twee.](./media/drawing-conversion-error-codes/geometry-warning-2.png)

* Hieronder ziet u een afbeelding waarin een niet-gesloten poly lijn wordt weer gegeven. Stel dat de laag alleen gesloten geometrie ondersteunt.

    ![Voor beeld van een niet-gesloten poly lijn](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-geometrywarning"></a>*GeometryWarning oplossen*

Inspecteer de **geometryWarning** voor elke entiteit om te controleren of deze geometrische beperkingen volgt.

### <a name="unexpectedgeometryinlayer"></a>**unexpectedGeometryInLayer**

#### <a name="description-for-unexpectedgeometryinlayer"></a>*Beschrijving voor unexpectedGeometryInLayer*

Er treedt een **unexpectedGeometryInLayer** -waarschuwing op wanneer de tekening geometrie bevat die niet compatibel is met het verwachte type geometrie voor een bepaalde laag. Wanneer de conversie service een **unexpectedGeometryInLayer** -waarschuwing retourneert, wordt die geometrie genegeerd.

#### <a name="example-for-unexpectedgeometryinlayer"></a>*Voor beeld voor unexpectedGeometryInLayer*

In de onderstaande afbeelding ziet u een niet-gesloten poly lijn. Stel dat de laag alleen gesloten geometrie ondersteunt.

![Voor beeld van een niet-gesloten poly lijn](./media/drawing-conversion-error-codes/geometry-warning-3.png)

#### <a name="how-to-fix-unexpectedgeometryinlayer"></a>*UnexpectedGeometryInLayer oplossen*

Inspecteer elke **unexpectedGeometryInLayer** -waarschuwing en verplaats de niet-compatibele geometrie naar een compatibele laag. Als deze niet compatibel is met een van de andere lagen, moet dit worden verwijderd.

### <a name="unsupportedfeaturerepresentation"></a>**unsupportedFeatureRepresentation**

#### <a name="description-for-unsupportedfeaturerepresentation"></a>*Beschrijving voor unsupportedFeatureRepresentation*

De waarschuwing **unsupportedFeatureRepresentation** treedt op wanneer de tekening een niet-ondersteund entiteits type bevat.

#### <a name="example-for-unsupportedfeaturerepresentation"></a>*Voor beeld voor unsupportedFeatureRepresentation*

In de onderstaande afbeelding ziet u een niet-ondersteund entiteits type als een object met meerdere regels tekst op een label laag.
  
![Voor beeld van een object met meerdere regels tekst in de label laag](./media/drawing-conversion-error-codes/multi-line.png)

#### <a name="how-to-fix-unsupportedfeaturerepresentation"></a>*UnsupportedFeatureRepresentation oplossen*

Zorg ervoor dat uw DWG-bestanden alleen de ondersteunde entiteits typen bevatten. De ondersteunde typen worden vermeld [in de sectie vereisten voor tekening bestanden in het artikel vereisten voor het teken pakket](drawing-requirements.md#drawing-package-requirements).

### <a name="automaticrepairperformed"></a>**automaticRepairPerformed**

#### <a name="description-for-automaticrepairperformed"></a>*Beschrijving voor automaticRepairPerformed*

De waarschuwing **automaticRepairPerformed** treedt op wanneer de conversie service automatisch de ongeldige geometrie herstelt.

#### <a name="examples-for-automaticrepairperformed"></a>*Voor beelden voor automaticRepairPerformed*

* In de volgende afbeelding ziet u hoe de conversie service een zelf-overlappende veelhoek heeft gerepareerd in een geldige geometrie.

    ![Voor beeld van een gerepareerde veelhoek die is gesnijdd](./media/drawing-conversion-error-codes/automatic-repair-1.png)

* In de onderstaande afbeelding ziet u hoe de conversie service het eerste en laatste hoek punt van een niet-gesloten poly lijn heeft uitgelijnd om een gesloten poly lijn te maken, waarbij het eerste en laatste hoek punt kleiner zijn dan 1 mm van elkaar.  

    ![Voor beeld van een magnetische poly lijn](./media/drawing-conversion-error-codes/automatic-repair-2.png)

* In de onderstaande afbeelding ziet u hoe, in een laag die alleen gesloten Polylines ondersteunt, de conversie service meerdere niet-gesloten polylineen heeft gerepareerd. Om te voor komen dat de niet-gesloten poly lijnen worden verwijderd, wordt de service gecombineerd in één gesloten polylijn.

    ![Voor beeld van niet-gesloten poly lijnen gecombineerd tot één gesloten veelhoek](./media/drawing-conversion-error-codes/automatic-repair-3.png)

#### <a name="how-to-fix-automaticrepairperformed"></a>*AutomaticRepairPerformed oplossen*

Voer de volgende acties uit om een **automaticRepairPerformed** -waarschuwing te herstellen:

1. Inspecteer de geometrie van elke waarschuwing en de specifieke waarschuwings tekst.
2. Bepaal of het geautomatiseerde herstel proces juist is.
3. Als het herstel juist is, gaat u verder. Als dat niet het geval is, gaat u naar het ontwerp bestand en lost u de waarschuwing hand matig op.

>[!TIP]
>Als u een waarschuwing in de toekomst wilt onderdrukken, brengt u wijzigingen aan in de oorspronkelijke tekening zodat de oorspronkelijke tekening overeenkomt met de gerepareerde tekening.

## <a name="manifest-warnings"></a>Manifest waarschuwingen

### <a name="redundantattribution"></a>**redundantAttribution**

#### <a name="description-for-redundantattribution"></a>*Beschrijving voor redundantAttribution*

De waarschuwing **redundantAttribution** treedt op wanneer het manifest redundante of conflicterende object eigenschappen bevat.

#### <a name="examples-for-redundantattribution"></a>*Voor beelden voor redundantAttribution*

* In het onderstaande JSON-fragment bevinden zich twee of meer `unitProperties` objecten met dezelfde `name` .

    ```json
    "unitProperties": [
        {
            "unitName": "L1-100",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        },
        {
            "unitName": "L1-101",
            "categoryName": "room.office"
        }
    ]
    ```

* In het onderstaande JSON-fragment hebben twee of meer `zoneProperties` objecten dezelfde `name` .

    ```json
     "zoneProperties": [
        {
            "zoneName": "Assembly Area 1",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        },
        {
            "zoneName": "Assembly Area 2",
            "categoryName": "zone.assembly"
        }
    ```

#### <a name="how-to-fix-redundantattribution"></a>*RedundantAttribution oplossen*

Verwijder redundante of conflicterende object eigenschappen om een **redundantAttribution* waarschuwing te herstellen.

### <a name="manifestwarning"></a>**manifestWarning**

#### <a name="description-for-manifestwarning"></a>*Beschrijving voor manifestWarning*

Een **manifestWarning** treedt op wanneer het manifest unitProperties-of zoneProperties-objecten bevat die niet worden gebruikt tijdens de conversie.

#### <a name="examples-for-manifestwarning"></a>*Voor beelden voor manifestWarning*

* Het manifest bevat een `unitProperties` object met een `unitName` dat geen overeenkomend label in een `unitLabel` laag heeft.

* Het manifest bevat een `zoneProperties` object met een `zoneName` dat geen overeenkomend label in een `zoneLabel` laag heeft.

#### <a name="how-to-fix-manifestwarning"></a>*ManifestWarning oplossen*

Als u een **manifestWarning** wilt herstellen, verwijdert u het ongebruikte `unitProperties` of `zoneProperties` object uit het manifest of voegt u een eenheid/zone label aan de tekening toe, zodat het object Properties wordt gebruikt tijdens de conversie.

## <a name="wall-warnings"></a>Wand waarschuwingen

### <a name="walloutsidelevel"></a>**wallOutsideLevel**

#### <a name="description-for-walloutsidelevel"></a>*Beschrijving voor wallOutsideLevel*

De waarschuwing **wallOutsideLevel** treedt op wanneer de tekening een geometrie van een wand bevat buiten de grenzen van een niveau-overzicht.

#### <a name="example-for-walloutsidelevel"></a>*Voor beeld voor wallOutsideLevel*

* In de onderstaande afbeelding ziet u een binnenkant van de wand, in het rood, buiten de grens van het gele niveau.

    ![Voor beeld van een interieur wand buiten de grens van het niveau](./media/drawing-conversion-error-codes/wall-outside-level.png)

* In de volgende afbeelding ziet u een buiten wand, in rood, buiten de grens van het gele niveau.

    ![Voor beeld van buiten wand buiten de grens van het niveau](./media/drawing-conversion-error-codes/wall-outside-level-exterior.png)

#### <a name="how-to-fix-walloutsidelevel"></a>*WallOutsideLevel oplossen*

Als u een **wallOutsideLevel** -waarschuwing wilt herstellen, vouwt u de niveau geometrie uit zodat alle wanden worden toegevoegd. U kunt ook de grenzen van de wand aanpassen binnen de grens van het niveau.

## <a name="unit-warnings"></a>Eenheids waarschuwingen

### <a name="unitoutsidelevel"></a>**unitOutsideLevel**

#### <a name="description-for-unitoutsidelevel"></a>*Beschrijving voor unitOutsideLevel*

Er treedt een **unitOutsideLevel** -waarschuwing op wanneer de tekening een eenheids geometrie bevat buiten de grenzen van het niveau overzicht.

#### <a name="example-for-unitoutsidelevel"></a>*Voor beeld voor unitOutsideLevel*

 In de volgende afbeelding, de eenheids geometrie, in rood, overschrijdt de grenzen van de grens van het gele niveau.

 ![Voor beeld van een eenheid die de grens van het niveau overschrijdt](./media/drawing-conversion-error-codes/unit-outside-level.png)

#### <a name="how-to-fix-unitoutsidelevel"></a>*UnitOutsideLevel oplossen*

Als u een **unitOutsideLevel** -waarschuwing wilt herstellen, vouwt u de niveau grens uit om alle eenheden op te geven. Of wijzig de eenheids geometrie zodat deze binnen de grens van het niveau past.

### <a name="partiallyoverlappingunit"></a>**partiallyOverlappingUnit**

#### <a name="description-for-partiallyoverlappingunit"></a>*Beschrijving voor partiallyOverlappingUnit*

Er treedt een **partiallyOverlappingUnit** -waarschuwing op wanneer de tekening een eenheids geometrie bevat die gedeeltelijk overlapt met een andere geometrie van een eenheid. De conversie service verwijdert overlappende eenheden.

#### <a name="example-scenarios-partiallyoverlappingunit"></a>*Voorbeeld scenario's partiallyOverlappingUnit*

In de volgende afbeelding is de overlappende eenheid rood gemarkeerd. `UNIT110` en `HALLWAY` worden genegeerd.

![Voor beeld van overlappende eenheden](./media/drawing-conversion-error-codes/partially-overlapping-unit.png)

#### <a name="how-to-fix-partiallyoverlappingunit"></a>*PartiallyOverlappingUnit oplossen*

Als u een **partiallyOverlappingUnit** -waarschuwing wilt herstellen, moet u elke gedeeltelijk overlappende eenheid opnieuw tekenen zodat deze geen andere eenheden overlapt.

## <a name="door-warnings"></a>Waarschuwingen voor deuren

### <a name="dooroutsidelevel"></a>**doorOutsideLevel**

#### <a name="description-for-dooroutsidelevel"></a>*Beschrijving voor doorOutsideLevel*

Er treedt een **doorOutsideLevel** -waarschuwing op wanneer de tekening een deur geometrie bevat buiten de grenzen van de geometrie van het niveau.

#### <a name="example-for-dooroutsidelevel"></a>*Voor beeld voor doorOutsideLevel*

In de volgende afbeelding is de geometrie van de deur, gemarkeerd in rood, de grens van het gele niveau overlapt.

![Voor beeld van een deur die overlapt met een niveau grens](./media/drawing-conversion-error-codes/door-outside-level.png)

#### <a name="how-to-fix-dooroutsidelevel"></a>*DoorOutsideLevel oplossen*

Als u een **doorOutsideLevel** -waarschuwing wilt herstellen, moet u de geometrie van de deur opnieuw tekenen zodat deze zich binnen de grenzen van het niveau bevindt.

## <a name="zone-warnings"></a>Zone waarschuwingen

### <a name="zonewarning"></a>**zoneWarning**

#### <a name="description-for-zonewarning"></a>*Beschrijving voor zoneWarning*

De **zoneWarning** treedt op wanneer een zone geen label bevat. De conversie service verwijdert een zone die geen label is. l

#### <a name="example-for-zonewarning"></a>*Voor beeld voor zoneWarning*

In de volgende afbeelding ziet u een zone die geen label bevat.

![Voor beeld van een zone bevat geen label](./media/drawing-conversion-error-codes/zone-warning.png)

#### <a name="how-to-fix-zonewarning"></a>*ZoneWarning oplossen*

Als u een **zoneWarning** wilt herstellen, controleert u of elke zone één label heeft.

## <a name="label-warnings"></a>Label waarschuwingen

### <a name="labelwarning"></a>*labelWarning*

#### <a name="description-for-labelwarning"></a>*Beschrijving voor labelWarning*

De **labelWarning** treedt op wanneer de tekening een functie met ambigue of een niet-eenduidige labels bevat.

Een **labelWarning** wordt veroorzaakt door een of meer van de volgende redenen:

* Een eenheids label bevindt zich niet in eenheden.
* Een zone label bevindt zich niet in zones.
* Een zone label bevindt zich in twee of meer zones.

#### <a name="example-for-labelwarning"></a>*Voor beeld voor labelWarning*

In de volgende afbeelding ziet u een label dat binnen twee zones wordt weer gegeven.

![Voor beeld van een label in twee zones ](./media/drawing-conversion-error-codes/label-warning.png)

#### <a name="how-to-fix-labelwarning"></a>*LabelWarning oplossen*

Als u een **labelWarning** wilt herstellen, moet u het volgende doen:

* Alle labels voor eenheden bevinden zich binnen eenheden.
* Alle zone-labels bevinden zich in zones.
* Alle zone-labels bevinden zich in slechts één zone.

## <a name="drawing-package-errors"></a>Fouten in het tekening pakket

### <a name="invalidarchiveformat"></a>**invalidArchiveFormat**

#### <a name="description-for-invalidarchiveformat"></a>*Beschrijving voor invalidArchiveFormat*

Er treedt een **invalidArchiveFormat** -fout op wanneer het teken pakket een ongeldige Archief indeling heeft, zoals gzip of 7-zip. Alleen de indeling ZIP-archief wordt ondersteund.

Er wordt ook een **invalidArchiveFormat** -fout gegenereerd als het zip-archief leeg is.

#### <a name="how-to-fix-invalidarchiveformat"></a>*InvalidArchiveFormat oplossen*

Als u een **invalidArchiveFormat** -fout wilt oplossen, controleert u het volgende:

* De naam van het archief bestand eindigt op _. zip_.
* Uw ZIP-archief bevat gegevens.
* U kunt uw ZIP-archief openen.

### <a name="invaliduserdata"></a>**invalidUserData**

#### <a name="description-for-invaliduserdata"></a>*Beschrijving voor invalidUserData*

Er treedt een **invalidUserData** -fout op wanneer de conversie service een gebruikers gegevens object uit de opslag niet kan lezen.

#### <a name="example-scenario-for-invaliduserdata"></a>*Voorbeeld scenario voor invalidUserData*

U hebt geprobeerd een teken pakket met een onjuiste `udid` para meter te uploaden.

#### <a name="how-to-fix-invaliduserdata"></a>*InvalidUserData oplossen*

Als u een **invalidUserData** -fout wilt oplossen, controleert u het volgende:

* U hebt een juiste versie `udid` voor het geüploade pakket ingevoerd.
* Azure Maps Creator (preview) is ingeschakeld voor het Azure Maps account dat u hebt gebruikt voor het uploaden van het teken pakket.
* De API-aanvraag voor de conversie service bevat de abonnements sleutel voor het Azure Maps account dat u hebt gebruikt voor het uploaden van het teken pakket.

### <a name="dwgerror"></a>**dwgError**

#### <a name="description-for-dwgerror"></a>*Beschrijving voor dwgError*

Een **dwgError** wanneer het teken pakket een probleem bevat met een of meer DWG-bestanden in het GEÜPLOADe zip-archief.

De **dwgError** treedt op wanneer het teken pakket een DWG-bestand bevat dat niet kan worden geopend omdat het ongeldig of beschadigd is.

* Een DWG-bestand is geen geldige tekening voor het maken van AutoCAD-bestands indelingen.
* Een DWG-bestand is beschadigd.
* Een DWG-bestand wordt weer gegeven in de _manifest.jsop_ bestand, maar ontbreekt in het zip-archief.

#### <a name="how-to-fix-dwgerror"></a>*DwgError oplossen*

Als u een **dwgError** wilt herstellen, controleert u uw _manifest.jsop_ het volgende bestand:

* Alle DWG-bestanden in uw ZIP-archief zijn geldige tekeningen van AutoCAD DWG-indeling. open er een in AutoCAD. Alle ongeldige tekeningen verwijderen of herstellen.
* De lijst met DWG-bestanden in de _manifest.jsop_  komt overeen met de DWG-bestanden in het zip-archief.

## <a name="manifest-errors"></a>Manifest fouten

### <a name="invalidjsonformat"></a>**invalidJsonFormat**

#### <a name="description-for-invalidjsonformat"></a>Beschrijving voor invalidJsonFormat

Er treedt een **invalidJsonFormat** -fout op wanneer de _manifest.jsin_ het bestand niet kan worden gelezen.

Het _manifest.json_file kan niet worden gelezen vanwege JSON-indeling of syntaxis fouten. Voor meer informatie over de JSON-indeling en syntaxis raadpleegt u [de indeling van de JavaScript object Notation (JSON)-gegevens uitwisseling](https://tools.ietf.org/html/rfc7159)

#### <a name="how-to-fix-invalidjsonformat"></a>*InvalidJsonFormat oplossen*

Als u een **invalidJsonFormat** -fout wilt oplossen, gebruikt u een JSON-lint om eventuele JSON-fouten te detecteren en op te lossen.

### <a name="missingrequiredfield"></a>**missingRequiredField**

#### <a name="description-for-missingrequiredfield"></a>*Beschrijving voor missingRequiredField*

Er treedt een **missingRequiredField** -fout op wanneer de vereiste gegevens _voor demanifest.jsin_ het bestand ontbreken.

#### <a name="how-to-fix-missingrequiredfield"></a>*MissingRequiredField oplossen*

Als u een **missingRequiredField** -fout wilt oplossen, controleert u of het manifest alle vereiste eigenschappen bevat. Zie de [sectie manifest in de vereisten voor het teken pakket](drawing-requirements.md#manifest-file-requirements) voor een volledige lijst met vereiste manifest objecten  

### <a name="missingmanifest"></a>**missingManifest**

#### <a name="description-for-missingmanifest"></a>*Beschrijving voor missingManifest*

De **missingManifest** -fout treedt op wanneer de _manifest.jsin_ het bestand ontbreekt in het zip-archief.

De **missingManifest** -fout wordt veroorzaakt door een of meer van de volgende redenen:

* De _manifest.jsin_ het bestand is verkeerd gespeld.
* De _manifest.js_ ontbreekt.
* De _manifest.jsop_ is niet in de hoofdmap van het zip-archief.

#### <a name="how-to-fix-missingmanifest"></a>*MissingManifest oplossen*

Als u een **missingManifest** -fout wilt oplossen, controleert u of het archief een bestand bevat met de naam _manifest.jsop_ op het hoofd niveau van het zip-archief.

### <a name="conflict"></a>**conflict**

#### <a name="description-for-conflict"></a>*Beschrijving voor conflict*

De **conflict** fout treedt op wanneer de _manifest.jsin_ het bestand conflicterende gegevens bevat.

#### <a name="example-scenario-for-conflict"></a>*Voorbeeld scenario voor conflict*

De conversie service retourneert een **conflict** fout wanneer er meer dan één niveau is gedefinieerd met het rang telwoord van hetzelfde niveau. In het volgende JSON-fragment worden twee niveaus weer gegeven die met hetzelfde rang telwoord zijn gedefinieerd.

```JSON
"buildingLevels":
{
    "levels": [
        {
            "levelName": "Ground",
            "ordinal": 0,
            "filename": "./Level_0.dwg"
        },
        {
            "levelName": "Parking",
            "ordinal": 0,
            "filename": "./Level_P.dwg"
        }
    ]
}
```

#### <a name="how-to-fix-conflict"></a>*Conflict oplossen*

Als u een **conflict** fout wilt oplossen, controleert u uw _manifest.jsop_ en verwijdert u eventuele conflicterende gegevens.

### <a name="invalidgeoreference"></a>**invalidGeoreference**

#### <a name="description-for-invalidgeoreference"></a>*Beschrijving voor invalidGeoreference*

De **invalidGeoreference** -fout treedt op wanneer een _manifest.jsin_ het bestand een ongeldige geoverwijzing bevat.

De **invalidGeoreference** -fout wordt veroorzaakt door een of meer van de volgende redenen:

* De gebruiker verwijst naar een geografische of geografische lengte waarde die buiten het bereik valt.
* De gebruiker verwijst naar een rotatie waarde die buiten het bereik valt.

#### <a name="example-scenario-for-invalidgeoreference"></a>*Voorbeeld scenario voor invalidGeoreference*

In het onderstaande JSON-fragment ligt de breedte graad boven de bovengrens.

```json
"georeference"
{
    "lat": 88.0,
    "lon": -122.132600,
    "angle": 0
},
```

#### <a name="how-to-fix-invalidgeoreference"></a>*InvalidGeoreference oplossen*

Als u een **invalidGeoreference** -fout wilt oplossen, controleert u of de waarden in de geovalues binnen het bereik liggen.

>[!IMPORTANT]
>In geojson is de rang orde van de coördinaten in lengte graad en breedte graad. Als u niet de juiste volg orde gebruikt, mag u per ongeluk een waarde voor breedte graad of lengte graad opgeven die buiten het bereik valt.

## <a name="wall-errors"></a>Wand fouten

### <a name="wallerror"></a>**wallError**

#### <a name="description-for-wallerror"></a>*Beschrijving voor wallError*

De **wallError** treedt op wanneer de tekening een fout bevat tijdens het maken van een Wall-functie.

#### <a name="example-scenario-for-wallerror"></a>*Voorbeeld scenario voor wallError*

In de volgende afbeelding ziet u een muur-functie die geen eenheden overlapt.

![Voor beeld van een muur-functie die geen eenheden overlapt](./media/drawing-conversion-error-codes/wall-error.png)

#### <a name="how-to-fix-wallerror"></a>*WallError oplossen*

Als u een **wallError** -fout wilt herstellen, moet u de wand opnieuw tekenen zodat deze ten minste één eenheid overlapt. Of maak een nieuwe eenheid die de wand overlapt.

## <a name="vertical-penetration-errors"></a>Verticale indringings fouten

### <a name="verticalpenetrationerror"></a>**verticalPenetrationError**

#### <a name="description-for-verticalpenetrationerror"></a>*Beschrijving voor verticalPenetrationError*

De **verticalPenetrationError** treedt op wanneer de tekening een onduidelijke verticale indringings functie bevat.

De **verticalPenetrationError** wordt veroorzaakt door een of meer van de volgende redenen:

* De tekening bevat een verticaal indringings gebied zonder overlappende verticale indringings gebieden op een of meer niveaus.
* Het teken pakket bevat een niveau met twee of meer verticale indringings functies waarmee één verticale indringings functie wordt overlapt op een ander niveau direct boven of onder het.

#### <a name="example-scenario-for-verticalpenetrationerror"></a>*Voorbeeld scenario voor verticalPenetrationError*

In de onderstaande afbeelding ziet u een verticaal indringings gebied zonder overlappende verticale indringings gebieden op bovenliggende niveaus.

![Voor beeld van een verticale indringing 1](./media/drawing-conversion-error-codes/vrt-2.png)

De volgende afbeelding toont een verticaal indringings gebied dat meer dan één verticaal indringings gebied op een aangrenzend niveau overlapt.

![Voor beeld van een verticale indringing 2](./media/drawing-conversion-error-codes/vrt-1.png)

#### <a name="how-to-fix-verticalpenetrationerror"></a>VerticalPenetrationError oplossen

Als u een **verticalPenetrationError** -fout wilt oplossen, leest u meer over het gebruik van een verticale indringings functie in het artikel vereisten voor het [teken pakket](drawing-requirements.md) .

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Maps tekening fout visualer gebruiken](drawing-error-visualizer.md)

> [!div class="nextstepaction"]
> [Maker (preview) voor een binnenste toewijzing](creator-indoor-maps.md)