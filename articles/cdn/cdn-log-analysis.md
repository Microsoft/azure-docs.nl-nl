---
title: Gebruikspatronen van Azure CDN analyseren
description: In dit artikel worden de verschillende typen analyse rapporten beschreven die beschikbaar zijn voor Azure CDN producten.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2020
ms.author: allensu
ms.openlocfilehash: 2c593cda7761ce1defa6bdb31e0dbb528b9e5eca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96483985"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Gebruikspatronen van Azure CDN analyseren

Nadat u CDN hebt ingeschakeld voor uw toepassing, kunt u het CDN-gebruik controleren, de status van uw levering controleren en mogelijke problemen oplossen. Azure CDN biedt deze mogelijkheden op de volgende manieren: 

## <a name="raw-logs-for-azure-cdn-from-microsoft"></a>Onbewerkte logboeken voor Azure CDN van micro soft
Met een standaard Profiel van micro soft kunt u onbewerkte logboeken inschakelen en logboeken selecteren voor het volgende:

* Azure Storage
* Event Hubs
* Azure Log Analytics

Met Azure Log Analytics kunt u metrische gegevens over bewaking bekijken en waarschuwingen instellen. 

Zie [Azure CDN onbewerkte HTTP-logboeken](monitoring-and-access-log.md)voor meer informatie.


## <a name="core-analytics-via-azure-diagnostic-logs"></a>Kern analyse via Diagnostische logboeken van Azure

Kern analyse is beschikbaar voor CDN-eind punten voor alle prijs categorieën. Met Azure Diagnostics-logboeken kan kern analyse worden geëxporteerd naar Azure Storage, Event hubs of Azure Monitor Logboeken. Azure Monitor-logboeken bieden een oplossing met grafieken die door de gebruiker kunnen worden geconfigureerd en aangepast. Zie [Azure Diagnostic logs](cdn-azure-diagnostic-logs.md)(Engelstalig) voor meer informatie over Azure Diagnostische logboeken.

## <a name="verizon-core-reports"></a>Verizon-kern rapporten

**Azure CDN Standard van Verizon** -of **Azure CDN Premium van Verizon** -profielen bieden kern rapporten. U kunt kern rapporten weer geven in de aanvullende portal van Verizon. Verizon-kern rapporten zijn toegankelijk via de optie **beheren** van de Azure Portal en bieden verschillende soorten grafieken en weer gaven. Zie voor meer informatie [kern rapporten van Verizon](cdn-analyze-usage-patterns.md).

## <a name="verizon-custom-reports"></a>Aangepaste Verizon-rapporten

**Azure CDN Standard van Verizon** -of **Azure CDN Premium van Verizon** -profielen bieden aangepaste rapporten. U kunt aangepaste rapporten weer geven in de aanvullende portal van Verizon. Aangepaste Verizon-rapporten zijn toegankelijk via de optie **beheren** van de Azure Portal. 

In de aangepaste rapporten wordt het aantal treffers of gegevens overgezet voor elke Edge CNAME weer gegeven. Gegevens worden gegroepeerd op HTTP-respons code of cache status over de periode. Zie [aangepaste rapporten van Verizon](cdn-verizon-custom-reports.md)voor meer informatie.

## <a name="azure-cdn-premium-from-verizon-reports"></a>Azure CDN Premium van Verizon-rapporten

Met **Azure CDN Premium van Verizon** kunt u ook toegang krijgen tot de volgende rapporten:
   * [Geavanceerde HTTP-rapporten](cdn-advanced-http-reports.md)
   * [Realtime statistieken](cdn-real-time-stats.md)
   * [Prestaties van Azure CDN Edge-knoop punt](cdn-edge-performance.md)

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd over de verschillende opties voor analyse rapporten voor Azure CDN.

Zie voor meer informatie over Azure CDN en de andere Azure-Services die in dit artikel worden genoemd:

* [Wat is Azure CDN?](cdn-overview.md)
* [Onbewerkte HTTP-logboeken Azure CDN](monitoring-and-access-log.md)
