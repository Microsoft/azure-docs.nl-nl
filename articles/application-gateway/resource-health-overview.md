---
title: Overzicht van Resource Health voor Azure Application Gateway
description: Dit artikel bevat een overzicht van de resource Health-functie voor Azure-toepassing gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: 7e30a93f8270cfaf8910130cc1e2633bb80c2b8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397169"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Overzicht van Resource Health voor Azure Application Gateway

[Azure Resource Health](../service-health/resource-health-overview.md) helpt u bij het stellen van diagnoses en bij het verkrijgen van ondersteuning wanneer een Azure-serviceprobleem gevolgen heeft voor uw resources. Er wordt u geïnformeerd over de huidige en eerdere status van uw resources. En biedt technische ondersteuning om u te helpen bij het oplossen van problemen.

Voor Application Gateway is Resource Health afhankelijk van de door de gateway verzonden signalen om te beoordelen of het in orde is of niet. Als de gateway niet in orde is, Resource Health analyseert u aanvullende informatie om de oorzaak van het probleem vast te stellen. Het identificeert ook acties die micro soft uitvoert of wat u kunt doen om het probleem op te lossen.

Bekijk de volledige lijst met resource typen en status controles in [Azure resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)voor meer informatie over de beoordeling van de status.


De status voor Application Gateway wordt weer gegeven als een van de volgende statussen:

## <a name="available"></a>Beschikbaar

Een **beschik bare** status betekent dat de service geen gebeurtenissen heeft gedetecteerd die van invloed zijn op de status van de resource. In de afgelopen 24 uur wordt de melding **recent opgelost** weer geven wanneer de gateway is hersteld van ongeplande downtime.

![Beschik bare status](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Niet beschikbaar

Een niet- **beschik bare** status betekent dat de service een voortdurende platform of niet-platform gebeurtenis heeft gedetecteerd die van invloed is op de status van de gateway.

### <a name="platform-events"></a>Platformgebeurtenissen

Platform gebeurtenissen worden geactiveerd door meerdere onderdelen van de Azure-infra structuur. Ze omvatten zowel geplande acties (bijvoorbeeld gepland onderhoud) als onverwachte incidenten (bijvoorbeeld een niet-geplande host opnieuw opstarten).

Resource Health biedt aanvullende informatie over de gebeurtenis en het herstel proces. Ook kunt u contact opnemen met de ondersteuning, zelfs als u geen actieve micro soft-ondersteunings overeenkomst hebt.

![Status niet beschikbaar](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Onbekend

De **onbekende** status geeft aan resource Health geen meer dan tien minuten informatie over de gateway heeft ontvangen. Deze status is geen definitieve indicatie van de status van de gateway. Maar het is een belang rijk gegevens punt in het probleemoplossings proces.

Als de gateway op de verwachte manier wordt uitgevoerd, wordt de status na een paar minuten gewijzigd in **beschikbaar** .

Als u problemen ondervindt, kan de **onbekende** status Voorst Ellen dat een gebeurtenis in het platform van invloed is op de gateway.

![Onbekende status](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Verminderd beschikbaar

De **gedegradeerde** status geeft aan dat uw gateway een verlies van prestaties heeft gedetecteerd, maar nog steeds beschikbaar is voor gebruik.

![Status Gefeliciteerd](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Volgende stappen

Zie [problemen met Web Application firewall (WAF) voor Azure-toepassing gateway oplossen](../web-application-firewall/ag/web-application-firewall-troubleshoot.md)voor meer informatie over het oplossen van problemen Application Gateway Web Application firewall (WAF).