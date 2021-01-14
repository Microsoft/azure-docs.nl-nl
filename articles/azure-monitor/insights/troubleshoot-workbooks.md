---
title: Problemen met Azure Monitor op basis van een werkmap oplossen
description: Biedt richt lijnen voor probleem oplossing voor Azure Monitor inzichten op basis van een werkmap voor services zoals Azure Key Vault, Azure CosmosDB, Azure Storage en Azure cache voor redis.
services: azure-monitor
ms.author: lagayhar
author: lgayhardt
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 3a2765e9dcf3b0e7bf28191e6ecbb069f26c42f5
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208519"
---
# <a name="troubleshooting-workbook-based-insights"></a>Problemen met op werk blad gebaseerde inzichten oplossen

Dit artikel helpt u bij het diagnosticeren en oplossen van problemen met enkele veelvoorkomende problemen die kunnen optreden bij het gebruik van Azure Monitor gebaseerd op werk op basis van een werkmap.


## <a name="why-can-i-only-see-200-resources"></a>Waarom kan ik alleen 200 resources zien

Voor het aantal geselecteerde resources geldt een limiet van 200, ongeacht het aantal abonnementen dat is geselecteerd.

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Wat gebeurt er wanneer ik op een onlangs vastgemaakte tegel in het dash board Klik

* Als u op een wille keurige plek op de tegel klikt, gaat u naar het tabblad waar de tegel is vastgemaakt. Als u bijvoorbeeld een grafiek in het tabblad Overzicht vastmaakt wanneer u op die tegel in het dash board klikt, wordt deze weer gegeven in de standaard weergave, maar als u een grafiek van uw eigen opgeslagen kopie vastmaakt, wordt de weer gave van uw opgeslagen kopie geopend.
* Met het filter pictogram in de linkerbovenhoek van de titel opent u het tabblad ' tegel instellingen configureren '.
* Met het pictogram met de ovaal in de rechter bovenhoek krijgt u de opties voor het aanpassen van de titel gegevens, aanpassen, vernieuwen en verwijderen uit het dash board.

## <a name="what-happens-when-i-save-a-workbook"></a>Wat gebeurt er wanneer ik een werkmap opsla

* Wanneer u een werkmap opslaat, kunt u een nieuwe kopie maken van de werkmap met uw wijzigingen en de titel wijzigen. Als u opslaat, wordt de werkmap niet overschreven, de huidige werkmap is altijd de standaard weergave.
* Een **niet-opgeslagen** werkmap is alleen de standaard weergave.

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Waarom zie ik niet al mijn abonnementen in de portal

In de portal worden alleen gegevens weer gegeven voor geselecteerde abonnementen op het starten van de portal. Als u wilt wijzigen welke abonnementen zijn geselecteerd, gaat u naar rechtsboven en klikt u op het notitie blok met een filter pictogram. Met deze optie wordt het tabblad **map en abonnementen** weer gegeven.

![Map en abonnement](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>Wat is een tijds bereik?

In het tijds bereik worden gegevens uit een bepaald tijds bestek weer gegeven. Als het tijds bereik bijvoorbeeld 24 uur is, worden de gegevens van de afgelopen 24 uur weer gegeven.

## <a name="what-is-time-granularity-time-grain"></a>Wat is een tijd granulatie (tijd korrel)

Tijd granulatie is het tijds verschil tussen twee gegevens punten. Als de tijd korrel bijvoorbeeld is ingesteld op 1 seconde, worden metrische gegevens elke seconde verzameld.

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Wat is de tijd granulatie zodra een deel van de werkmappen is vastgemaakt aan een dash board

De standaardtijd granulatie is ingesteld op automatisch. deze kan momenteel niet worden gewijzigd.

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Hoe kan ik het tijds bereik van de werkmap stap in mijn dash board wijzigen

Standaard is het tijds bereik voor time span uren op de dashboard tegel ingesteld op 24 uur, om deze klik op het weglatings teken in de rechter bovenhoek te wijzigen. Selecteer **tegel gegevens aanpassen**, schakel het selectie vakje de tijd instellingen van het dash board negeren op het niveau van de titel in en kies vervolgens een time span in het vervolg keuzemenu.  

![Selecteer de weglatings tekens in de rechter hoek van de tegel en kies deze gegevens aanpassen](./media/storage-insights-overview/fqa-data-settings.png)

![Selecteer in de tegel instellingen configureren de vervolg keuzelijst time span om het interval/tijds bereik te wijzigen](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Hoe kan ik Wijzig de titel van de werkmap of de stap van een werkmap die ik heb vastgemaakt aan een dash board

De titel van de werkmap of werkmap stap die is vastgemaakt aan een dash board, behoudt dezelfde naam als in de werkmap. Als u de titel wilt wijzigen, moet u uw eigen kopie van de werkmap opslaan. Vervolgens kunt u de werkmap een naam geeft voordat u op Opslaan klikt.

![Selecteer boven opslaan om een kopie van de werkmap op te slaan en de naam ervan te wijzigen](./media/storage-insights-overview/fqa-change-workbook-name.png)

Als u de naam van een stap in uw opgeslagen werkmap wilt wijzigen, selecteert u bewerken onder de stap en selecteert u vervolgens het vistuig onder instellingen.

![Selecteer Bewerken onder aan de stap van een werkmap om de instellingen ](./media/storage-insights-overview/fqa-edit.png)
 ![ in instellingen te openen Selecteer onder aan het vistuig de optie aan de onderkant, zodat de stap naam kan worden gewijzigd](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de scenario's voor werkmappen is ontworpen voor ondersteuning, het ontwerpen van nieuwe en het aanpassen van bestaande rapporten, en meer door [interactieve rapporten maken met Azure monitor werkmappen](../platform/workbooks-overview.md)te controleren.
