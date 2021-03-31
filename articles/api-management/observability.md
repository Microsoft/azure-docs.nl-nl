---
title: Beachtbaarheid in azure API Management | Microsoft Docs
description: Overzicht van alle opties voor de instellingen voor naleving in azure API Management.
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: apimpm
ms.openlocfilehash: 1ebebed465952bbb5d3e8f82ae1c7776c441c6b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87096738"
---
# <a name="observability-in-azure-api-management"></a>Waarneem bare API Management van Azure

De beschik baarheid is de mogelijkheid om de interne status van een systeem te begrijpen van de gegevens die worden geproduceerd en de mogelijkheid om die gegevens te verkennen om vragen te beantwoorden over wat er is gebeurd en waarom. 

Met Azure API Management kunnen organisaties het beheer van alle Api's centraliseren. Omdat deze fungeert als één punt van binnenkomst van het API-verkeer, is het een ideale plek om de Api's te observeren. 

## <a name="observability-tools"></a>Hulpprogram Ma's voor waarneembaar

De volgende tabel bevat een overzicht van alle hulpprogram ma's die door API Management worden ondersteund om Api's te observeren. Dit is handig voor een of meer scenario's:

| Hulpprogramma        | Handig voor    | Gegevens vertraging | Bewaartermijn | Steekproeven | Gegevens soort | Ingeschakeld|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[API-controle](api-management-howto-api-inspector.md)** | Testen en foutopsporing | Meteen | Laatste 100 traceringen | Ingeschakeld per aanvraag | Traceringen aanvragen | Altijd
| **Ingebouwde analyse** | Rapportage en bewaking | Minuten | Dood | 100% | Rapporten en Logboeken | Altijd |
| **[Metrische gegevens van Azure Monitor](api-management-howto-use-azure-monitor.md)** | Rapportage en bewaking | Minuten | 93 dagen (upgrade om uit te breiden) | 100% | Metrische gegevens | Altijd |
| **[Azure Monitor-logboeken](api-management-howto-use-azure-monitor.md)** | Rapportage, bewaking en fout opsporing | Minuten | 31 dagen/5 GB (upgrade naar uitbrei ding) | 100% (aanpasbaar) | Logboeken | Optioneel |
| **[Inzichten Azure-toepassing](api-management-howto-app-insights.md)** | Rapportage, bewaking en fout opsporing | Seconden | 90 dagen/5 GB (upgrade om uit te breiden) | Aangepast | Logboeken, metrische gegevens | Optioneel |
| **[Logboek registratie via Azure Event hub](api-management-howto-log-event-hubs.md)** | Aangepaste scenario's | Seconden | Door gebruiker beheerd | Aangepast | Aangepast | Optioneel |

### <a name="self-hosted-gateway"></a>Zelf-hostende gateway

Alle hierboven genoemde hulpprogram ma's worden ondersteund door de beheerde gateway in de Cloud. De [zelf-hostende gateway](self-hosted-gateway-overview.md) verzendt momenteel geen Diagnostische logboeken naar Azure monitor. Het is echter mogelijk om lokaal logboeken te configureren en persistent te maken, waarbij de zelf-hostende gateway wordt geïmplementeerd. Zie [metrische gegevens over de Cloud en logboeken configureren voor zelf-hostende gateway](how-to-configure-cloud-metrics-logs.md) en [lokale metrische gegevens en logboeken configureren voor zelf-hostende gateways](how-to-configure-local-metrics-logs.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Volg de zelf studies voor meer informatie over API Management](import-and-publish.md)
