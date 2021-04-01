---
title: Inleiding van Azure Web Application Firewall
description: Dit artikel geeft een overzicht van Web Application Firewall (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "79475784"
---
# <a name="what-is-azure-web-application-firewall"></a>Wat is Azure Web Application Firewall?

WAF (Web Application Firewall) biedt gecentraliseerde bescherming van uw webtoepassingen, van veelvoorkomende aanvallen tot beveiligingsproblemen. Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van algemeen bekende beveiligingsproblemen. SQL-injectie en cross-site scripting zijn twee van de meest voorkomende aanvallen.

![Overzicht van WAF](media/overview/wafoverview.png)

Het kan een hele uitdaging zijn om dergelijke aanvallen in toepassingscode te voorkomen. Dit kan veel onderhoud, patching en controle vereisen op meerdere lagen van de toepassingstopologie. Een gecentraliseerde WAF helpt het beveiligingsbeheer sterk te vereenvoudigen. Een WAF biedt toepassingsbeheerders ook betere bescherming tegen bedreigingen en indringers.

Een WAF-oplossing kan ook sneller reageren op een beveiligingsrisico door centraal een patch voor een bekend beveiligingsprobleem toe te passen, in plaats van elke afzonderlijke webtoepassing te beveiligen.

## <a name="supported-service"></a>Ondersteunde service

WAF kan worden geïmplementeerd met Azure Application Gateway, Azure Front Door en de Azure CDN-service (Content Delivery Network) van Microsoft. WAF op Azure CDN is momenteel beschikbaar als openbare preview.  WAF heeft functies die zijn aangepast voor elke specifieke service. Zie het overzicht voor elke service voor meer informatie over WAF-functies voor elke service.

## <a name="next-steps"></a>Volgende stappen

- Zie [Web Application Firewall in Azure Application Gateway](./ag/ag-overview.md) voor meer informatie over Web Application Firewall in Application Gateway.
- Zie [Web Application Firewall in Azure Front Door Service](./afds/afds-overview.md) voor meer informatie over Web Application Firewall in Azure Front Door Service.
- Zie [Web Application Firewall in Azure CDN Service](./cdn/cdn-overview.md) voor meer informatie over Web Application Firewall in Azure CDN Service
