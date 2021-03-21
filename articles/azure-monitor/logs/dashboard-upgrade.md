---
title: Uw Log Analytics dashboard visualisaties bijwerken
description: Meer informatie over het bijwerken van uw Log Analytics dashboard visualisaties met query's waarmee u krachtige inzichten kunt krijgen.
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: 276ea1e2f083da7ab4a6ab44e60bc7e0832e8651
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030933"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>Uw Log Analytics dashboard visualisaties bijwerken

In februari 2020 hebben we een verbeterde visualisatie technologie geïntroduceerd. Verbetering en verbetering van de mogelijkheden voor het visualiseren van query resultaten en het bereiken van krachtige inzichten, snel. 

Meer informatie over deze upgrade vindt u in deze [Azure update](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/). 

Deze nieuwe visualisatie technologie is een goede manier om een nieuwe en verbeterde ervaring te bieden rond uw query resultaten. 

## <a name="dashboards-in-azure"></a>Dash boards in azure

Azure-Dash boards zijn een manier om de status van uw volledige Azure-surface area te visualiseren. Ze zijn ontworpen om een enkel glas venster te bieden aan de status van Azure erfgoed en kunnen diverse snelkoppelingen naar algemene acties toestaan. 

Zie [Azure-Dash boards](../../azure-portal/azure-portal-dashboards.md) voor meer informatie.


## <a name="upgrading-log-analytics-dashboard-parts"></a>Log Analytics dashboard onderdelen bijwerken

De nieuwe visualisatie technologie behandelt enkele veelvoorkomende problemen met de oude implementatie en introduceert een aantal nieuwe mogelijkheden voor vastgemaakte Log Analytics onderdelen: 

- **Dezelfde beschik bare typen** : alle visualisatie typen die beschikbaar zijn in log Analytics zijn beschikbaar als vastgemaakte onderdelen op Dash boards.

- **Consistent uiterlijk** : het uiterlijk van de visualisatie voor vastgemaakte onderdelen is nu bijna identiek aan die in log Analytics. De verschillen worden veroorzaakt door optimalisaties waarvoor subtiele verschillen zijn vereist in de gegevens inhoud van de Visual. Zie het gedeelte overwegingen van dit document voor meer inzicht in deze verschillen.

- **Knop Info en labels** : nieuwe vastgemaakte log Analytics visualisaties ondersteunen knop info. Cirkel-en ring diagrammen ondersteunen nu labels.

- **Interactieve legenda's** : als u op de visualisatie legenda klikt, kunt u dimensies toevoegen aan of verwijderen uit de vastgemaakte Visual als in log Analytics.

## <a name="stage-1---opt-in-upgrade-message"></a>Fase 1-upgrade bericht voor opt-in

Wanneer een Log Analytics vastgemaakte deel kan worden bijgewerkt, wordt er een nieuwe *opt-in-* melding weer gegeven op Log Analytics vastgemaakte onderdelen in dash boards waarmee gebruikers hun visualisatie kunnen bijwerken. Als u de nieuwe visualisaties wilt ervaren voor het bijwerken van geselecteerde visualisaties in hun dash board.

 
![Zijbalk](media/dashboard-upgrade/update-message-1.png)
 
![Scherm afbeelding die laat zien hoe u de tegel visualisatie bijwerkt.](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> Wanneer het dash board is gepubliceerd, kan de upgrade onomkeerbaar zijn. Wijzigingen worden echter genegeerd als u het dash board verlaat zonder opnieuw te publiceren.  

Zodra u hebt geklikt, wordt de visualisatie bijgewerkt naar de nieuwe technologie. Subtiele verschillen in de visualisatie kunnen zich voordoen in Log Analytics.

Nadat de visualisaties zijn bijgewerkt, moet u het dash board opnieuw publiceren om de wijziging door te voeren.

![Scherm opname van de bijgewerkte visualisaties.](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>Fase 2: migratie van alle Dash boards

Nadat een eerste aanmeldings periode is afgelopen, zal het Log Analytics team alle Dash boards in het systeem bijwerken. Met het uitlijnen van alle Azure-Dash boards kan het team meer visualisaties maken en verbeteringen op het bord ervaren.

## <a name="considerations"></a>Overwegingen

Log Analytics visualisaties die zijn vastgemaakt aan een dash board, hebben een specifiek gedrag dat is ontworpen voor een optimale ervaring. Bekijk de volgende aandachtspunten bij het vastmaken van een visualisatie aan een dash board.

### <a name="query-time-scope---30-day-limit"></a>Query-tijd bereik-Maxi maal 30 dagen

Omdat dashboards meerdere visualisaties van meerdere query's kunnen bevatten, is het tijdsbereik voor één vastgemaakte query beperkt tot 30 dagen. Eén query kan alleen worden uitgevoerd op een tijds panne die kleiner is dan of gelijk is aan 30 dagen. Deze beperking is het garanderen van een redelijke laad tijd van een dash board.

### <a name="query-data-values---25-values-and-other-grouping"></a>Query's uitvoeren op gegevens waarden-25 waarden en andere groeperingen

Dash boards kunnen visueel en complex zijn. Als u de cognitieve belasting wilt beperken tijdens het weer geven van een dash board, optimaliseert u de visualisaties door de weer gave te beperken tot 25 verschillende gegevens typen. Wanneer er meer dan 25 Log Analytics, worden de gegevens geoptimaliseerd. De 25 typen worden afzonderlijk weer gegeven met de meeste gegevens als afzonderlijke en vervolgens de resterende waarden worden gegroepeerd in een andere waarde. In het volgende diagram ziet u een voor beeld van een dergelijk geval.  

![Scherm opname van een dash board met 25 verschillende gegevens typen.](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>Dash board vernieuwen bij laden

Dash boards worden vernieuwd bij het laden. Alle query's met betrekking tot door het dash board vastgemaakte Log Analytics visualisaties worden uitgevoerd en het dash board wordt vernieuwd zodra het wordt geladen. Als de dashboard pagina geopend blijft, worden de gegevens in het dash board elke 60 minuten vernieuwd.

## <a name="next-steps"></a>Volgende stappen

[Dash boards maken en delen in Log Analytics](../visualize/tutorial-logs-dashboards.md)