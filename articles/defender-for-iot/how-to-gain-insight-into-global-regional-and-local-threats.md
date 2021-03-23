---
title: Inzicht krijgen in globale, regionale en lokale bedreigingen
description: Krijg inzicht in wereld wijde, regionale en lokale bedreigingen met behulp van de site kaart in de on-premises beheer console.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: db3b9fbca9acd6c4ce1cfe137a4024f66d8a6292
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784115"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>Inzicht krijgen in globale, regionale en lokale bedreigingen

De site kaart in de on-premises beheer console helpt u bij het behalen van de volledige beveiligings dekking door uw netwerk te delen in geografische en logische segmenten die overeenkomen met uw bedrijfs topologie:

- **Niveau van de geografische faciliteit**: een site weerspiegelt een aantal apparaten gegroepeerd op basis van een geografische locatie die op de kaart wordt weer gegeven. Standaard biedt Azure Defender voor IoT u een wereld kaart. U werkt de kaart bij om uw organisatie-of bedrijfs structuur weer te geven. Gebruik bijvoorbeeld een kaart die de sites in een bepaald land, dezelfde stad of industrieel universiteits weerspiegelt. Wanneer de site kleur op de kaart wordt gewijzigd, biedt het SOC team een indicatie van de kritieke systeem status in de faciliteit.

  De kaart is interactief en maakt het mogelijk om elke site te openen en in te zoomen op de gegevens van deze site.

- **Global Logical Layer**: een bedrijfs eenheid is een manier om uw onderneming te verdelen in logische segmenten op basis van specifieke branches. Wanneer u dit doet, wordt de topologie van uw bedrijf op de kaart weer gegeven.

  Zo kan een wereld wijd bedrijf dat glazen fabrieken, plastic fabrieken en auto Mobile-fabrieken bevat, worden beheerd als drie verschillende bedrijfs eenheden. Een fysieke site in Amsterdam bevat drie verschillende glas productie lijnen, een plastic productie lijn en een productie lijn voor een truck-engine. Deze site heeft dus vertegenwoordigers van de drie business units.

- **Niveau van geografische regio**: Maak regio's om een wereld wijde onderneming te verdelen in geografische regio's. Het bedrijf dat we hebben beschreven, kan bijvoorbeeld de regio's Noord-Amerika, West-Europa en Oost-Europa gebruiken. Noord-Amerika heeft fabrieken van alle drie de business units. West-Europa heeft auto Mobile-fabrieken en glazen fabrieken en Oost-Europa heeft alleen plastic fabrieken.

- **Lokaal logisch segment niveau**: een zone is een logisch segment binnen een site die bijvoorbeeld een functie gebied of productie regel definieert. Als u werkt met zones, kunnen beveiligings beleidsregels worden afgedwongen die relevant zijn voor de zone definitie. Een site met vijf productie regels kan bijvoorbeeld worden gesegmenteerd in vijf zones.

- **Lokaal weergave niveau**: een lokale weer gave van een installatie met één sensor biedt inzicht in de operationele en beveiligings status van verbonden apparaten.

## <a name="work-with-site-map-views"></a>Werken met site kaart weergaven

De on-premises beheer console biedt een algemeen overzicht van uw industriële netwerk in een context afhankelijke kaart. In de weer gave algemeen ziet u de algemene kaart van uw organisatie met de geografische locatie van elke site.

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="Scherm opname van de weer gave van de bedrijfs kaart.":::

### <a name="color-coded-map-views"></a>Kaart weergaven met kleur code

**Groen**: het aantal beveiligings gebeurtenissen is lager dan de drempel waarde die Defender voor IOT voor uw systeem heeft gedefinieerd. Er is geen actie nodig.

**Geel**: het aantal beveiligings gebeurtenissen is gelijk aan de drempel die voor het systeem is gedefinieerd voor Defender voor IOT. Overweeg de gebeurtenissen te onderzoeken.  

**Rood**: het aantal beveiligings gebeurtenissen ligt buiten de drempel die Defender voor IOT voor uw systeem heeft gedefinieerd. Direct actie ondernemen.

### <a name="risk-level-map-views"></a>Kaart weergaven op risico niveau

**Risico analyse**: in de weer gave risico analyse wordt informatie over site Risico's weer gegeven. Met risico gegevens kunt u de prioriteit van de oplossing bepalen en een wegkaart bouwen om beveiligings verbeteringen te plannen.

**Reactie op incidenten**: een gecentraliseerde weer gave van alle niet-bevestigde waarschuwingen op elke site in de hele onderneming ophalen. U kunt inzoomen en waarschuwingen beheren die zijn gedetecteerd op een specifieke site.

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="Scherm opname van de weer gave voor bedrijfs kaarten met reactie op incidenten.":::

**Schadelijke activiteit**: als er malware is gedetecteerd, wordt de site rood weer gegeven. Dit geeft aan dat u onmiddellijk actie moet ondernemen.

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="Scherm opname van de weer gave bedrijfs kaart met schadelijke activiteiten.":::

**Operationele waarschuwingen**: in deze kaart weergave voor de systemen van het systeem wordt een beter inzicht geboden in de manier waarop het niet mogelijk is om operationele incidenten te ondervinden, zoals PLC gestopt, het uploaden van de firmware en het uploaden van Program ma's.

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="Scherm opname van de weer gave ondernemings kaart met operationele waarschuwingen.":::

Een kaart weergave kiezen:

1. Selecteer de **standaard weergave** van de kaart.
2. Selecteer een weergave.

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="Scherm afbeelding van de standaard weergave van het site overzicht.":::

## <a name="update-the-site-map-image"></a>De afbeelding van het site overzicht bijwerken

Defender voor IoT biedt een standaard kaart voor wereld wijde kaarten. U kunt dit wijzigen in uw organisatie: een land kaart of een stads kaart, bijvoorbeeld. 

De kaart vervangen:

1. Selecteer **systeem instellingen** in het linkerdeel venster.

2. Selecteer de **site kaart wijzigen** en upload het afbeeldings bestand om de standaard kaart te vervangen.

## <a name="next-step"></a>Volgende stap

[Waarschuwingen weergeven](how-to-view-alerts.md)
