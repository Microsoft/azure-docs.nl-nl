---
title: Metrische gegevens en waarschuwingen in azure Traffic Manager
description: In dit artikel vindt u informatie over de metrische gegevens en waarschuwingen die beschikbaar zijn voor Traffic Manager in Azure.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: duau
ms.openlocfilehash: b18e0329aeb4e95e021c3326b6b428c10edc0c6e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586415"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Metrische gegevens en waarschuwingen Traffic Manager

Traffic Manager biedt u de taak verdeling op basis van DNS met meerdere routerings methoden en opties voor eindpunt bewaking. In dit artikel worden de metrische gegevens en bijbehorende waarschuwingen beschreven die voor klanten beschikbaar zijn. 

## <a name="metrics-available-in-traffic-manager"></a>Metrische gegevens die beschikbaar zijn in Traffic Manager 

Traffic Manager biedt de volgende metrische gegevens per profiel dat klanten kunnen gebruiken om het gebruik van Traffic Manager en de status van hun eind punten onder dat profiel te begrijpen.  

### <a name="queries-by-endpoint-returned"></a>Query's op eind punt geretourneerd
Gebruik [Deze metrische gegevens](../azure-monitor/essentials/metrics-supported.md) om het aantal query's weer te geven dat gedurende een bepaalde periode door een Traffic Manager profiel wordt verwerkt. U kunt ook dezelfde informatie weer geven op het niveau van de granulatie op het eind punt, zodat u begrijpt hoe vaak een eind punt is geretourneerd in de antwoorden op query's van Traffic Manager.

In het volgende voor beeld toont afbeelding 1 alle antwoorden op query's die het Traffic Manager-profiel retourneert. 

  
![Samengevoegde weer gave van alle query's](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Afbeelding 1: geaggregeerde weer gave met alle query's*
  
In afbeelding 2 wordt dezelfde informatie weer gegeven, maar deze wordt gesplitst op eind punten. Als gevolg hiervan kunt u het volume van de query antwoorden zien waarin een bepaald eind punt is geretourneerd.

![Traffic Manager metrische gegevens: gesplitste weer gave van query volume per eind punt](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Afbeelding 2: de weer gave splitsen met het query volume dat wordt geretourneerd per eind punt*

## <a name="endpoint-status-by-endpoint"></a>Eindpunt status op eind punt
Gebruik [Deze metrische gegevens](../azure-monitor/essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) om inzicht te krijgen in de status van de eind punten in het profiel. Er zijn twee waarden nodig:
 - Gebruik **1** als het eind punt actief is.
 - Gebruik **0** als het eind punt niet beschikbaar is.

Deze metriek kan worden weer gegeven als een statistische waarde die de status van alle metrische gegevens (afbeelding 3) vertegenwoordigt, of kan worden gesplitst (zie afbeelding 4) om de status van specifieke eind punten weer te geven. Als het voormalige, als het aggregatie niveau is geselecteerd als **Gem**, is de waarde van deze metriek het reken kundige gemiddelde van de status van alle eind punten. Als een profiel bijvoorbeeld twee eind punten heeft en er slechts één is in orde, dan heeft deze metriek de waarde **0,50** , zoals wordt weer gegeven in afbeelding 3. 


![Traffic Manager metrische gegevens: samengestelde weer gave van de eindpunt status](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Afbeelding 3: samengestelde weer gave van metrische gegevens van eindpunt status – "Gem" aggregatie geselecteerd*


![Traffic Manager metrische gegevens-gesplitste weer gave van de eindpunt status](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Afbeelding 4: de gesplitste weer gave van metrische gegevens van de eindpunt status*

U kunt deze metrische gegevens gebruiken via de portal van [Azure monitor Services](../azure-monitor/essentials/metrics-supported.md), [rest API](/rest/api/monitor/), [Azure cli](/cli/azure/monitor)en [Azure PowerShell](/powershell/module/az.applicationinsights), of via de sectie metrische gegevens van de portal-ervaring van Traffic Manager.

## <a name="alerts-on-traffic-manager-metrics"></a>Waarschuwingen over Traffic Manager metrische gegevens
Naast het verwerken en weer geven van metrische gegevens van Traffic Manager, kunt u met Azure Monitor klanten waarschuwingen configureren en ontvangen die zijn gekoppeld aan deze metrische gegevens. U kunt kiezen aan welke voor waarden in deze metrische gegevens moet worden voldaan om een waarschuwing te vinden, hoe vaak deze voor waarden moeten worden bewaakt en hoe de waarschuwingen naar u moeten worden gezonden. Zie [Azure monitor Alerts documentation](../azure-monitor/alerts/alerts-metric.md)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Monitor-service](../azure-monitor/essentials/metrics-supported.md)
- Meer informatie over het [maken van een grafiek met Azure monitor](../azure-monitor/essentials/metrics-getting-started.md#create-your-first-metric-chart)