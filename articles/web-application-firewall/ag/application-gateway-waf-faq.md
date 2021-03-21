---
title: Azure Web Application firewall op Application Gateway-Veelgestelde vragen
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Web Application firewall op Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 890688dba70a7fa654e97652b3e474b919f9a077
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585380"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Veelgestelde vragen over de firewall van Azure Web Application op Application Gateway

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Web Application firewall (WAF) op Application Gateway-functies en-functionaliteit. 

## <a name="what-is-azure-waf"></a>Wat is Azure WAF?

Azure WAF is een Web Application Firewall waarmee u uw webtoepassingen kunt beschermen tegen veelvoorkomende bedreigingen, zoals SQL-injectie, cross-site scripting en andere webexploits. U kunt een WAF-beleid definiëren dat bestaat uit een combi natie van aangepaste en beheerde regels voor het beheren van de toegang tot uw webtoepassingen.

Een Azure WAF-beleid kan worden toegepast op webtoepassingen die worden gehost op Application Gateway of Azure front deuren.

## <a name="what-features-does-the-waf-sku-support"></a>Welke functies worden ondersteund door de WAF-SKU?

De WAF-SKU ondersteunt alle beschik bare functies in de standaard-SKU.

## <a name="how-do-i-monitor-waf"></a>WAF Hoe kan ik controleren?

Bewaak WAF via diagnostische logboek registratie. Zie [Diagnostische logboek registratie en metrische gegevens voor Application Gateway](../../application-gateway/application-gateway-diagnostics.md)voor meer informatie.

## <a name="does-detection-mode-block-traffic"></a>Blokkeert de detectie modus verkeer?

Nee. In de detectie modus wordt alleen verkeer geregistreerd dat een WAF-regel activeert.

## <a name="can-i-customize-waf-rules"></a>Kan ik WAF-regels aanpassen?

Ja. Zie [WAF-regel groepen en-regels aanpassen](application-gateway-customize-waf-rules-portal.md)voor meer informatie.

## <a name="what-rules-are-currently-available-for-waf"></a>Welke regels zijn momenteel beschikbaar voor WAF?

WAF biedt momenteel ondersteuning voor CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3,0](application-gateway-crs-rulegroups-rules.md#owasp30)en [3,1](application-gateway-crs-rulegroups-rules.md#owasp31). Deze regels bieden basis beveiliging tegen de meeste van de Top-10 beveiligings problemen die OWASP (Web Application Security project) identificeren: 

* Beveiliging tegen SQL-injecties
* Beveiliging voor cross-site scripting
* Bescherming tegen veelvoorkomende webaanvalen, zoals het injecteren van opdrachten, HTTP-aanvragen smuggling, het splitsen van HTTP-antwoorden en insluiting van externe bestanden
* Beveiliging tegen schendingen van het HTTP-protocol
* Beveiliging tegen afwijkingen van het HTTP-protocol, zoals een gebruikersagent voor de host en Accept-headers die ontbreken
* Beveiliging tegen bots, crawlers en scanners
* Detectie van veelvoorkomende onjuiste configuraties van toepassingen (dat wil zeggen Apache, IIS, enzovoort)

Zie [OWASP Top-10-beveiligings problemen](https://owasp.org/www-project-top-ten/)voor meer informatie.

## <a name="what-content-types-does-waf-support"></a>Welke inhouds typen biedt ondersteuning voor WAF?

Application Gateway WAF ondersteunen de volgende inhouds typen voor beheerde regels:

* application/json
* toepassing/XML
* Application/x-www-form-urlencoded
* meerdelige/formulier-data

En voor aangepaste regels:

* Application/x-www-form-urlencoded
* meerdelige/formulier-data

## <a name="does-waf-support-ddos-protection"></a>Biedt WAF ondersteuning voor DDoS-beveiliging?

Ja. U kunt DDoS-beveiliging inschakelen voor het virtuele netwerk waarin de toepassings gateway is geïmplementeerd. Met deze instelling zorgt u ervoor dat de Azure DDoS Protection-Service ook de virtuele IP-adressen van de toepassings gateway (VIP) beveiligt.

### <a name="does-waf-store-customer-data"></a>Slaat WAF klant gegevens op?

Nee, WAF slaat geen klant gegevens op.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [firewall van Azure Web Application](../overview.md).
- Meer informatie over de [voor deur van Azure](../../frontdoor/front-door-overview.md).
