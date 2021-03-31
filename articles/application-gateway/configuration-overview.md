---
title: Overzicht van Azure-toepassing gateway configuratie
description: In dit artikel wordt beschreven hoe u de onderdelen van Azure-toepassing gateway configureert
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 0012ac99600c77dce5940387e1da54f29c3f6ab7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89652000"
---
# <a name="application-gateway-configuration-overview"></a>Overzicht van Application Gateway configuratie

Azure-toepassing gateway bestaat uit verschillende onderdelen die u op verschillende manieren kunt configureren voor verschillende scenario's. In dit artikel wordt beschreven hoe u elk onderdeel kunt configureren.

![Stroom diagram van Application Gateway onderdelen](./media/configuration-overview/configuration-overview1.png)

Deze afbeelding illustreert een toepassing met drie listeners. De eerste twee zijn multi-site listeners voor `http://acme.com/*` en `http://fabrikam.com/*` . Luister beide op poort 80. De derde is een eenvoudige listener met end-to-end Transport Layer Security (TLS) beëindiging, voorheen bekend als Secure Sockets Layer (SSL).

## <a name="infrastructure"></a>Infrastructuur

De Application Gateway-infra structuur omvat het virtuele netwerk, subnetten, netwerk beveiligings groepen en door de gebruiker gedefinieerde routes.

Zie [Application Gateway Infrastructure Configuration](configuration-infrastructure.md)(Engelstalig) voor meer informatie.



## <a name="front-end-ip-address"></a>Front-end-IP-adres

U kunt de toepassings gateway configureren voor een openbaar IP-adres, een privé IP-adres of beide. Een openbaar IP-adres is vereist wanneer u een back-end host dat clients via internet toegang moeten hebben via een virtueel IP-adres (VIP) op internet.

Zie de configuratie van een [front-end-IP-adres Application Gateway](configuration-front-end-ip.md)voor meer informatie.

## <a name="listeners"></a>Listeners

Een listener is een logische entiteit die controleert op binnenkomende verbindings aanvragen met behulp van de poort, het Protocol, de host en het IP-adres. Wanneer u de listener configureert, moet u waarden opgeven voor deze die overeenkomen met de overeenkomende waarden in de inkomende aanvraag op de gateway.

Zie [Application Gateway listener-configuratie](configuration-listeners.md)voor meer informatie.

## <a name="request-routing-rules"></a>Routeringsregels aanvragen

Wanneer u een toepassings gateway maakt met behulp van de Azure Portal, maakt u een standaard regel (*firewallregel1*). Deze regel koppelt de standaard-listener (*appGatewayHttpListener*) aan de standaard back-end-pool (*appGatewayBackendPool*) en de standaard back-end-http-instellingen (*appGatewayBackendHttpSettings*). Nadat u de gateway hebt gemaakt, kunt u de instellingen van de standaard regel bewerken of nieuwe regels maken.

Zie [Application Gateway routerings regels voor aanvragen](configuration-request-routing-rules.md)voor meer informatie.

## <a name="http-settings"></a>HTTP-instellingen

De Application Gateway routeert het verkeer naar de back-endservers door gebruik te maken van de configuratie die u hier opgeeft. Nadat u een HTTP-instelling hebt gemaakt, moet u deze koppelen aan een of meer aanvraag-routerings regels.

Zie [Application Gateway configuratie van HTTP-instellingen](configuration-http-settings.md)voor meer informatie.

## <a name="back-end-pool"></a>Back-endgroep

U kunt een back-end-pool naar vier typen back-endservers verwijzen: een specifieke virtuele machine, een virtuele-machine schaalset, een IP-adres/FQDN of een app service. 

Nadat u een back-end-pool hebt gemaakt, moet u deze koppelen aan een of meer aanvraag-routerings regels. U moet ook status controles configureren voor elke back-end-pool op uw toepassings gateway. Wanneer aan een regel voorwaarde voor het door sturen van de aanvraag wordt voldaan, stuurt de toepassings gateway het verkeer naar de gezonde servers (zoals bepaald door de status tests) in de bijbehorende back-end-pool.

## <a name="health-probes"></a>Statuscontroles

Een toepassings gateway controleert standaard de status van alle resources in de back-end. We raden u echter ten zeerste aan een aangepaste test te maken voor elke back-end-HTTP-instelling om meer controle te krijgen over de status controle. Zie [aangepaste Health probe-instellingen](application-gateway-probe-overview.md#custom-health-probe-settings)voor meer informatie over het configureren van een aangepaste test.

> [!NOTE]
> Nadat u een aangepaste status test hebt gemaakt, moet u deze koppelen aan een back-end-HTTP-instelling. Een aangepaste test controleert de status van de back-end-pool alleen als de bijbehorende HTTP-instelling expliciet is gekoppeld aan een listener met behulp van een regel.

## <a name="next-steps"></a>Volgende stappen

Nu u weet over Application Gateway-onderdelen, kunt u het volgende doen:

- [Een toepassings gateway maken in de Azure Portal](quick-create-portal.md)
- [Een toepassings gateway maken met behulp van Power shell](quick-create-powershell.md)
- [Een toepassings gateway maken met behulp van de Azure CLI](quick-create-cli.md)
