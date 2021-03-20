---
title: API Analytics gebruiken in azure API Management | Microsoft Docs
description: Gebruik Analytics in azure API Management om het gebruik van uw Api's en API-prestaties te begrijpen en te categoriseren.
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: ca7bd70bbf99a6d0079717a7a02328b11528d2e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96841438"
---
# <a name="get-api-analytics-in-azure-api-management"></a>API Analytics in azure API Management ophalen

Azure API Management biedt ingebouwde analyses voor uw Api's. Analyseer het gebruik en de prestaties van de Api's in uw API Management-exemplaar over verschillende dimensies, waaronder:

* Tijd
* Geografie
* API's
* API-bewerkingen
* Producten
* Abonnementen
* Gebruikers
* Aanvragen

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="Tijdlijn analyse in de portal":::

Gebruik analyses voor bewaking op hoog niveau en het oplossen van problemen met uw Api's. Zie [zelf studie: gepubliceerde api's bewaken](api-management-howto-use-azure-monitor.md)voor aanvullende controle functies, waaronder bijna realtime metrische gegevens en bron logboeken voor diagnostische gegevens en controle.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>Analytics-Portal

Gebruik de Azure Portal om Analytics-gegevens in één oogopslag te bekijken voor uw API Management-exemplaar.

1. Blader in [Azure Portal](https://portal.azure.com) naar uw API Management-exemplaar. 
1. Selecteer in het menu links onder **controle** de optie **analyse**.

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="Selecteer Analytics for API Management-instantie in de portal":::  
1. Selecteer een tijds bereik voor gegevens of voer een aangepast tijds bereik in.
1. Selecteer een rapport categorie voor analyse gegevens, zoals **tijd lijn**, **geografie**, enzovoort.
1. U kunt het rapport eventueel filteren op een of meer extra categorieën.

## <a name="analytics---rest-api"></a>Analytics-REST API

Gebruik [rapporten](/rest/api/apimanagement/2019-12-01/reports) bewerkingen in de API Management rest API om Analytics-gegevens voor uw API Management-exemplaar op te halen en te filteren.

Beschik bare bewerkingen retour neren rapport records per API, geografie, API-bewerkingen, product, aanvraag, abonnement, tijd of gebruiker.

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: gepubliceerde Api's controleren](api-management-howto-use-azure-monitor.md) voor een inleiding tot Azure monitor functies in API management.
* Zie [uw Api's controleren met Azure API Management, Event hubs en Moesif](api-management-log-to-eventhub-sample.md)voor meer informatie over http-logboek registratie en-bewaking.
* Meer informatie over het integreren van [Azure API Management met Azure-toepassing Insights](api-management-howto-app-insights.md).