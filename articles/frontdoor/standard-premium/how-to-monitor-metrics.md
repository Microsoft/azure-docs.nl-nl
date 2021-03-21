---
title: Metrische gegevens bewaken voor de standaard/Premium van Azure front deur
description: In dit artikel worden de metrische gegevens voor de Azure front-deur standaard/Premium-bewaking beschreven.
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: bb10fb337972db2696960b530f2d7538bd36a2fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101098847"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Real-time bewaking in azure front deur Standard/Premium

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

Azure front deur Standard/Premium is geïntegreerd met Azure Monitor en heeft 11 metrische gegevens om de voor deur standaard/Premium van Azure in realtime te bewaken om problemen op te sporen, op te lossen en te voor komen.  

Voor waarden voor de standaard/Premium van de Azure-deur en worden de metrische gegevens binnen 60 seconden verzonden. De metrische gegevens kunnen Maxi maal drie minuten in de portal worden weer gegeven. Metrische gegevens kunnen worden weer gegeven in diagrammen of raster van uw keuze en zijn toegankelijk via Portal, Power shell, CLI en API. Zie [Azure monitor metrische](../../azure-monitor/platform/data-platform-metrics.md)gegevens voor meer informatie.  

De standaard waarden zijn gratis. U kunt extra metrische gegevens inschakelen voor extra kosten. 

U kunt waarschuwingen voor elke metriek configureren, zoals een drempel waarde voor 4XXErrorRate of 5XXErrorRate. Wanneer de fout frequentie de drempel waarde overschrijdt, wordt er een waarschuwing geactiveerd zoals geconfigureerd. Zie [metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../../azure-monitor/platform/alerts-metric.md)voor meer informatie. 

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Metrische gegevens die worden ondersteund in de voor deur standaard/Premium van Azure

| Metrische gegevens  | Beschrijving | Dimensies |
| ------------- | ------------- | ------------- |
| Percentage treffers in cache | Het percentage van de uitvoer van de AFD-cache, berekend op basis van het totale aantal uitgangen. </br> **Verhouding van byte-treffers** = (uitgang van de rand van de oorsprong)/egress vanaf de rand. </br> **Scenario's die zijn uitgesloten voor de berekening van de verhouding treffers in bytes**:</br> 1. u kunt geen cache expliciet configureren door middel van regel Engine of cache gedrag van query reeks. </br> 2. u kunt de instructie cache-Control expliciet configureren met No-Store of private cache. </br>3. de verhouding van de byte treffer kan laag zijn als het meeste verkeer wordt doorgestuurd naar de oorsprong in plaats van dat de gegevens in de cache worden verwerkt op basis van uw configuraties of scenario's. | Eindpunt |
| RequestCount | Het aantal client aanvragen dat door CDN wordt geleverd. | Eind punt, client land, client regio, HTTP-status, HTTP-status groep |
| ResponseSize | Het aantal client aanvragen dat door AFD wordt geleverd. |Eind punt, client land, client regio, HTTP-status, HTTP-status groep |
| TotalLatency | De totale tijd van de client aanvraag die door CDN is ontvangen **totdat de laatste reactie byte van CDN naar de client is verzonden**. |Eind punt, client land, client regio, HTTP-status, HTTP-status groep |
| RequestSize | Het aantal bytes dat is verzonden als aanvragen van clients naar AFD. | Eind punt, client land, client regio, HTTP-status, HTTP-status groep |
| 4XX% ErrorRate | Het percentage van alle client aanvragen waarvoor de antwoord status code is 4XX. | Eind punt, client land, client regio |
| 5XX% ErrorRate | Het percentage van alle client aanvragen waarvoor de antwoord status code is 5XX. | Eind punt, client land, client regio |
| OriginRequestCount  | Het aantal aanvragen dat van AFD naar oorsprong is verzonden | Eind punt, oorsprong, HTTP-status, HTTP-status groep |
| OriginLatency | De tijd die wordt berekend vanaf het moment waarop de aanvraag is verzonden door AFD Edge naar de back-end totdat AFD de laatste reactie-byte van de back-end heeft ontvangen. | Eind punt, oorsprong |
| OriginHealth% | Het percentage geslaagde status tests van AFD tot oorsprong.| Oorsprong, oorspronkelijke groep |
| Aantal WAF-aanvragen | Overeenkomende WAF-aanvraag. | Actie, naam van regel, beleids naam |

## <a name="access-metrics-in-azure-portal"></a>Toegangs gegevens in Azure Portal

1. Selecteer **alle resources** in het menu Azure Portal  >>  **\<your-AFD Standard/Premium (Preview) -profile>** .

2. Onder **bewaking** selecteert u **metrische gegevens**:

3. Selecteer bij **metrische gegevens** de toe te voegen metriek:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="Scherm opname van de pagina metrische gegevens." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. Selecteer **filter toevoegen** om een filter toe te voegen:

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="Scherm afbeelding van het toevoegen van filters aan metrische gegevens." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. Selecteer **splitsing Toep assen** om gegevens te splitsen op basis van verschillende dimensies:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="Scherm afbeelding van het toevoegen van dimensies aan metrische gegevens." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. Selecteer **nieuwe** grafiek om een nieuwe grafiek toe te voegen:

## <a name="configure-alerts-in-azure-portal"></a>Waarschuwingen configureren in Azure Portal

1. Stel waarschuwingen in voor Azure front-deur standaard/Premium (preview) door **bewakings**  >>  **waarschuwingen** te selecteren.

1. Selecteer **nieuwe waarschuwings regel** voor metrische gegevens die worden weer gegeven in de sectie metrische gegevens.

Er wordt een waarschuwing berekend op basis van Azure Monitor. Zie [Azure monitor-waarschuwingen](../../azure-monitor/platform/alerts-overview.md)voor meer informatie over waarschuwingen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure front-deur Standard/Premium-rapporten](how-to-reports.md).
- Meer informatie over [Azure front deur Standard/Premium-logboeken](how-to-logs.md).
