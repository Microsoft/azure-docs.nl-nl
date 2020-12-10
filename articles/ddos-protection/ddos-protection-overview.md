---
title: Azure DDoS Protection standaard overzicht
description: Meer informatie over hoe de Azure DDoS Protection Standard in combinatie met best practices in toepassingsontwerp verdediging biedt tegen DDoS-aanvallen.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: c3931cafb6c755c6119bf8deb4ecc6b58ea7bc51
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938440"
---
# <a name="azure-ddos-protection-standard-overview"></a>Overzicht van Azure DDoS Protection Standard

DDoS-aanvallen (Distributed Denial of Service-aanvallen) vormen een van de grootste beschikbaarheids- en beveiligingsproblemen voor klanten die hun toepassingen verplaatsen naar de cloud. Met een DDoS-aanval wordt geprobeerd de resources van een toepassing uit te putten, waardoor de toepassing niet meer beschikbaar is voor legitieme gebruikers. DDoS-aanvallen kunnen worden gericht op elk eindpunt dat openbaar bereikbaar is via internet.

Elke eigenschap in azure wordt zonder extra kosten beschermd door de Azure Infrastructure DDoS (Basic)-beveiliging. De schaal en de capaciteit van het wereld wijd geïmplementeerde Azure-netwerk bieden bescherming tegen veelvoorkomende aanvallen via netwerk lagen via de controle van het verkeer en de real-time-oplossing. Voor DDoS Protection Basic zijn geen gebruikers configuratie of toepassings wijzigingen vereist. DDoS Protection Basic helpt bij het beveiligen van alle Azure-Services, waaronder PaaS-Services, zoals Azure DNS.

Azure DDoS Protection Standard, gecombineerd met de aanbevolen procedures voor het ontwerpen van toepassingen, biedt verbeterde DDoS-beperkings functies om te beschermen tegen DDoS-aanvallen. Het wordt automatisch afgestemd om uw specifieke Azure-resources in een virtueel netwerk te beveiligen. Beveiliging is eenvoudig in te scha kelen op een nieuw of bestaand virtueel netwerk en er zijn geen wijzigingen in de toepassing of resource. Het heeft verschillende voor delen ten opzichte van de Basic-service, inclusief logboek registratie, waarschuwingen en telemetrie. 

![Vergelijking van Azure DDoS Protection-Service](./media/ddos-protection-overview/ddos-comparison.png)

Azure DDoS Protection slaat geen klant gegevens op.

## <a name="features"></a>Functies

- **Systeem eigen platform integratie:** Systeem eigen geïntegreerd in Azure. Bevat configuratie via de Azure Portal. DDoS Protection standaard begrijpt uw resources en resource configuratie.
- Kant-en- **klare beveiliging:** Vereenvoudigde configuratie beveiligt onmiddellijk alle resources in een virtueel netwerk zodra DDoS Protection standaard is ingeschakeld. Er is geen interventie-of gebruikers definitie vereist. DDoS Protection Standard onmiddellijk en vermindert de aanval automatisch, zodra deze is gedetecteerd.
- **Bewaking over altijd verkeer:** Uw toepassings verkeers patronen worden 24 uur per dag, 7 dagen per week gecontroleerd en er wordt gezocht naar indica toren van DDoS-aanvallen. De beperking wordt toegepast wanneer het beveiligings beleid wordt overschreden.
- **Adaptieve afstemming:** Intelligent verkeer profile ring leert het verkeer van uw toepassing gedurende een bepaalde periode en selecteert en werkt het profiel dat het meest geschikt is voor uw service. Het profiel wordt aangepast naarmate het verkeer na verloop van tijd verandert.
- **Beveiliging met meerdere lagen:** Biedt volledige stack DDoS-beveiliging, wanneer deze met een Web Application Firewall wordt gebruikt om de beveiliging op te halen op de netwerklaag (laag 3 en 4, aangeboden door Azure DDoS Protection Standard) en op de toepassingslaag (laag 7, aangeboden door een WAF). WAF-aanbiedingen zijn onder andere Azure [Application Gateway WAF-SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en Web Application firewall aanbiedingen van derden die beschikbaar zijn op de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).
- **Uitgebreide beperkings schaal:** Meer dan 60 verschillende typen aanvallen kunnen worden gereduceerd, met globale capaciteit om te beschermen tegen de grootste bekende DDoS-aanvallen.
- **Aanvals analyse:** Ontvang gedetailleerde rapporten in stappen van vijf minuten tijdens een aanval en een volledig overzicht nadat de aanval is beëindigd. Stroom logboeken voor risico beperking naar [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) of offline Security Information en Event Management (Siem) System voor bijna realtime-bewaking tijdens een aanval.
- **Maat staven voor aanvallen:** Een overzicht van de metrische gegevens van elke aanval is toegankelijk via Azure Monitor.
- **Waarschuwing voor aanvallen:** Waarschuwingen kunnen worden geconfigureerd bij het starten en stoppen van een aanval en over de duur van de aanval, met behulp van ingebouwde aanvals waarden. Waarschuwingen worden geïntegreerd in uw operationele software, zoals Microsoft Azure controle logboeken, Splunk, Azure Storage, E-mail en de Azure Portal.
- **DDoS snelle reactie**: deel het DDoS Protection DRR-team (Rapid Response) voor hulp bij het onderzoeken en analyseren van aanvallen. Zie [DDoS Rapid Response](ddos-rapid-response.md)(Engelstalig) voor meer informatie.
- **Kosten garantie:** Service-tegoeden voor gegevens overdracht en toepassingen voor gedocumenteerde DDoS-aanvallen.

## <a name="pricing"></a>Prijzen

Zie [Azure DDoS Protection Standard-prijzen](https://azure.microsoft.com/pricing/details/ddos-protection/)voor meer informatie over Azure DDoS Protection standaard prijzen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een DDoS Protection Plan maken](manage-ddos-protection.md)