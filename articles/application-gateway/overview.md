---
title: Wat is Azure Application Gateway?
description: Ontdek hoe u een Azure-toepassingsgateway kunt gebruiken voor het beheren van webverkeer naar uw toepassing.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 4344cd38d9a58eec27c6202e81b8ef678a510681
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176005"
---
# <a name="what-is-azure-application-gateway"></a>Wat is Azure Application Gateway?

Azure Application Gateway is een load balancer voor webverkeer waarmee u het verkeer naar uw webapps kunt beheren. Traditionele load balancers werken op de transportlaag (OSI-laag 4 - TCP en UDP) en routeren verkeer op basis van IP-bronadres en een bronpoort naar een IP-doeladres en doelpoort.

Application Gateway kan routeringsbeslissingen nemen op basis van extra kenmerken van een HTTP-aanvraag, bijvoorbeeld URI-pad of hostheaders. U kunt bijvoorbeeld verkeer op basis van de binnenkomende URL routeren. Dus als `/images` de binnenkomende URL is, kunt u verkeer routeren naar een specifieke set servers (ook wel een pool genoemd) die is geconfigureerd voor installatiekopieën. Als `/video` zich in de URL bevindt, wordt dat verkeer gerouteerd naar een andere pool die voor video's is geoptimaliseerd.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Dit type routering staat bekend al taakverdeling op de toepassingslaag (OSI-laag 7). Azure Application Gateway kan URL-gebaseerde routering en meer uitvoeren.

>[!NOTE]
> Azure biedt een pakket volledig beheerde oplossingen voor taakverdeling voor uw scenario's. 
> * Als u op zoek bent naar een internationale routering op basis van DNS en u voldoet **niet** aan de vereisten voor beëindiging van het TLS-protocol (Transport Layer Security), ('SSL-offload') of aanvragen per HTTP/HTTPS-aanvraag, verwerking via de toepassingslaag, raadpleegt u [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Als u de wereld wijde route ring van uw webverkeer wilt optimaliseren en de prestaties en betrouw baarheid van de eind gebruikers van de bovenste laag wilt optimaliseren via snelle globale failover, raadpleegt u de [voor deur](../frontdoor/front-door-overview.md).
> * Raadpleeg [Load Balancer](../load-balancer/load-balancer-overview.md) voor taakverdeling in de netwerklaag. 
> 
> Uw end-to-end scenario 's kunnen eventueel profiteren van een combinatie van deze oplossingen.
> Zie [Opties voor taakverdeling in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview) voor een vergelijking van de opties voor taakverdeling van Azure.


## <a name="features"></a>Functies

Zie [Functies van Azure Application Gateway](features.md) voor meer informatie over Application Gateway-functies.

## <a name="pricing-and-sla"></a>Prijzen en SLA

Zie [Prijzen van Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) voor meer informatie over Application Gateway-prijzen.

Zie [SLA van Application Gateway](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/) voor meer informatie over de Application Gateway-SLA.

## <a name="whats-new"></a>Nieuwe functies

Zie [Azure-updates](https://azure.microsoft.com/updates/?category=networking&query=Application%20Gateway) om te ontdekken wat er nieuw is in Azure Application Gateway.

## <a name="next-steps"></a>Volgende stappen

Afhankelijk van uw vereisten en omgeving kunt u een Application Gateway voor testdoeleinden maken met behulp van de Azure-portal, Azure PowerShell of Azure CLI.

- [Quickstart: Webverkeer omleiden met Azure Application Gateway - Azure Portal](quick-create-portal.md)
- [Snelstart: webverkeer omleiden met Azure Application Gateway - Azure PowerShell](quick-create-powershell.md)
- [Quickstart: Webverkeer omleiden met Azure Application Gateway - Azure CLI](quick-create-cli.md)