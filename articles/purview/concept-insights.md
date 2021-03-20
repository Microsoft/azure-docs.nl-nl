---
title: Inzicht in inzichten rapporten in azure controle sfeer liggen
description: In dit artikel wordt uitgelegd wat inzichten bevinden in azure controle sfeer liggen.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: d841fa336b2702a02f3215f97a6403217986d7e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553056"
---
# <a name="understand-insights-in-azure-purview"></a>Inzichten in Azure Purview begrijpen

Dit artikel bevat een overzicht van de functie inzichten in azure controle sfeer liggen.

Inzichten zijn een van de belangrijkste pijlers van controle sfeer liggen. De functie biedt klanten, een enkel glas venster in hun catalogus en is gericht op het bieden van specifieke inzichten aan de gegevens bron beheerders, zakelijke gebruikers, data-auditeur, data Officer en beveiligings beheerders. Op dit moment heeft controle sfeer liggen de volgende inzichten rapporten die voor klanten beschikbaar zijn op een open bare preview.

## <a name="asset-insights"></a>Asset Insights

Dit rapport geeft de weer gave van uw gegevens in vogel vlucht en de distributie per bron type, op classificatie en op bestands grootte als enkele dimensies. Dit rapport is van invloed op verschillende soorten gebruikers die het controle sfeer liggen-account kunnen beheren en waarop scans of zakelijke gebruikers kunnen worden uitgevoerd, die mogelijk willen weten hoeveel activa er zijn met een bepaalde classificatie binnen de gegevens van hun organisatie. 

Het rapport bevat een breed inzicht in grafieken en Kpi's en maakt later kennis met specifieke afwijkingen zoals verkeerd geplaatste bestanden. Het rapport biedt ook ondersteuning voor een end-to-end klant ervaring, waarbij de klant het aantal assets kan weer geven met een specifieke classificatie, de informatie kan opsplitsen op bron typen en de bovenste mappen, en de lijst met assets voor verder onderzoek kan weer geven.

## <a name="scan-insights"></a>Inzichten scannen

Met het rapport kunnen beheerders inzicht krijgen in de algehele status van de scans: het aantal geslaagde, het aantal mislukte, het aantal geannuleerde. Dit rapport geeft een status update voor scans die in het controle sfeer liggen-account zijn uitgevoerd binnen een periode van de laatste zeven dagen of de afgelopen 30 dagen.

Met het rapport kunnen beheerders ook dieper kijken en zien welke scans zijn mislukt en op welke specifieke bron typen. Om gebruikers verder te kunnen onderzoeken, kunnen ze in het rapport zoeken naar de pagina met de geschiedenis van de bron.

## <a name="glossary-insights"></a>Inzichten in woorden lijst

Dit rapport geeft de zakelijke gebruikers en gegevens een status rapport over de verklarende woorden lijst. Gebruikers kunnen dit rapport bekijken om inzicht te krijgen in de distributie van termen in de woorden lijst op basis van de status, meer informatie over hoeveel woorden lijst termen zijn gekoppeld aan assets en hoeveel er nog niet aan een activum zijn gekoppeld. Zakelijke gebruikers kunnen ook meer te weten komen over de volledige term van hun woorden lijst. 

In dit rapport vindt u een overzicht van de belangrijkste items waaraan een zakelijke gebruiker of data steward moet zich concentreren, om een volledige en bruikbare woorden lijst te maken voor zijn/haar organisatie. Gebruikers kunnen ook naar de "woorden lijst"-ervaring van "woorden lijst Insights" navigeren om wijzigingen aan te brengen op een specifieke woordenlijst term.

## <a name="classification-insights"></a>Classificatie inzichten

Dit rapport bevat details over waar geclassificeerde gegevens zich bevinden, de classificaties die tijdens een scan worden gevonden en een drilldownbewerking naar de geclassificeerde bestanden zelf. Het stelt beveiligings beheerders in staat om inzicht te krijgen in de typen gegevens die worden gevonden in de gegevens van de organisatie. 

In azure controle sfeer liggen zijn classificaties vergelijkbaar met de label Tags en worden ze gebruikt om inhoud van een specifiek type te markeren en te identificeren in uw data-erfgoed.

Gebruik het rapport classificatie Insights om inhoud te identificeren met specifieke classificaties en inzicht te krijgen in de vereiste acties, zoals het toevoegen van extra beveiliging aan de opslag plaatsen of het verplaatsen van inhoud naar een veiligere locatie.

Zie [classificatie inzichten over uw gegevens van Azure controle sfeer liggen](classification-insights.md)voor meer informatie.

## <a name="sensitivity-labeling-insights"></a>Gevoeligheid voor het labelen van inzichten

Dit rapport bevat details over de gevoeligheids labels die tijdens een scan zijn gevonden, evenals een uitzoomen op de gelabelde bestanden zelf. Hiermee kunnen beveiligings beheerders zorgen voor de beveiliging van informatie die is gevonden in de gegevens van de organisatie. 

In azure controle sfeer liggen worden gevoeligheids labels gebruikt om classificatie type categorieën te identificeren, evenals het groeps beveiligings beleid dat u op elke categorie wilt Toep assen.

Gebruik het rapport labeling Insights om de gevoeligheids labels te identificeren die in uw inhoud zijn gevonden en inzicht te krijgen in de vereiste acties, zoals het beheren van toegang tot specifieke opslag plaatsen of bestanden.

Zie voor meer informatie [Sensitivity label Insights over uw gegevens in azure controle sfeer liggen](sensitivity-insights.md).

## <a name="file-extension-insights"></a>Inzichten voor bestands extensies

Dit rapport bevat details over de bestands extensies of bestands typen die tijdens een scan worden gevonden, evenals een uitzoomen op de bestanden zelf. 

Gebruik het rapport bestands extensie Insights om inzicht te krijgen in het aantal bestanden van elke keer dat u hebt, waar deze bestanden zich bevinden en of ze scannable zijn voor gevoelige gegevens.

Zie [inzichten over uw gegevens uit Azure controle sfeer liggen](file-extension-insights.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Inzichten in woorden lijst](glossary-insights.md)
* [Inzichten scannen](scan-insights.md)
* [Classificatie inzichten](./classification-insights.md)