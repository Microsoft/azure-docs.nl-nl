---
title: Ondersteuning voor websockets in Azure-toepassing gateway
description: Application Gateway biedt systeemeigen ondersteuning voor WebSocket in gateways van alle grootten. Er zijn geen door de gebruiker geconfigureerde instellingen.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: 59ca7708b24d2e75381290b80adeb671e2b49822
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91362692"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Overzicht van ondersteuning voor websockets in Application Gateway

Application Gateway biedt systeemeigen ondersteuning voor WebSocket in gateways van alle grootten. Er is geen door de gebruiker configureerbare instelling om selectief WebSocket-ondersteuning in of uit te schakelen. 

Met het WebSocket-protocol, dat is gestandaardiseerd in [RFC6455](https://tools.ietf.org/html/rfc6455) , is een volledige duplex communicatie tussen een server en een client mogelijk via een LANGLOPENDe TCP-verbinding. Deze functie biedt een meer interactieve communicatie tussen de webserver en de client. Dit kan bidirectionele zijn zonder dat hiervoor polling nodig is in op HTTP gebaseerde implementaties. WebSocket heeft weinig overhead, in tegens telling tot HTTP en kan dezelfde TCP-verbinding opnieuw gebruiken voor meerdere aanvragen en antwoorden, wat resulteert in een efficiënter gebruik van resources. WebSocket-protocollen zijn ontworpen om te werken met de traditionele HTTP-poorten 80 en 443.

U kunt een standaard HTTP-listener op poort 80 of 443 blijven gebruiken voor het ontvangen van WebSocket-verkeer. WebSocket-verkeer wordt vervolgens omgeleid naar de back-endserver voor websockets met behulp van de juiste back-end-groep zoals opgegeven in de regels voor de toepassings gateway. De back-end-server moet reageren op de test van de toepassings gateway, die wordt beschreven in de sectie Overzicht van de [status test](application-gateway-probe-overview.md) . Status controles van Application Gateway zijn alleen HTTP/HTTPS. Elke back-end-server moet reageren op HTTP-tests voor Application Gateway om WebSocket-verkeer naar de server te routeren.

Het wordt gebruikt in apps die profiteren van snelle, realtime communicatie, zoals chat-, dash board-en game-apps.

## <a name="how-does-websocket-work"></a>Hoe werkt WebSocket?

Als u een WebSocket-verbinding tot stand wilt brengen, wordt er een specifieke op HTTP gebaseerde Handshake uitgewisseld tussen de client en de server. Als dit lukt, wordt het Protocol van de toepassings laag "geüpgraded" van HTTP naar websockets met behulp van de eerder ingestelde TCP-verbinding. Als dit gebeurt, wordt HTTP volledig uit de afbeelding weer gegeven. gegevens kunnen worden verzonden of ontvangen via het WebSocket-protocol door beide eind punten, totdat de WebSocket-verbinding is gesloten. 

![Diagram vergelijkt een client interactie met een webserver en verbindt twee keer om twee antwoorden te krijgen, met een WebSocket-interactie, waarbij een client eenmaal verbinding maakt met een server om meerdere antwoorden te krijgen.](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>Configuratie-element Listener

Een bestaande HTTP-listener kan worden gebruikt ter ondersteuning van WebSocket-verkeer. Hier volgt een fragment van een httpListeners-element van een voorbeeld sjabloon bestand. U hebt HTTP-en HTTPS-listeners nodig om WebSocket en Secure WebSocket-verkeer te ondersteunen. Op dezelfde manier kunt u de portal of Azure PowerShell gebruiken om een toepassings gateway te maken met listeners op poort 80/443 voor het ondersteunen van WebSocket-verkeer.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>Configuratie van BackendAddressPool, BackendHttpSetting en routerings regel

Een BackendAddressPool wordt gebruikt voor het definiëren van een back-end-groep met servers die geschikt zijn voor websockets. De backendHttpSetting is gedefinieerd met een back-end-poort 80 en 443. De time-outwaarde voor de aanvraag in HTTP-instellingen is ook van toepassing op de WebSocket-sessie. Er is geen wijziging vereist in de routerings regel, die wordt gebruikt om de juiste listener te koppelen aan de bijbehorende back-end-adres groep. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>Back-end voor WebSocket

Uw back-end moet een HTTP/HTTPS-webserver zijn die wordt uitgevoerd op de geconfigureerde poort (meestal 80/443) om WebSocket te kunnen gebruiken. Deze vereiste is dat het WebSocket-protocol de eerste Handshake vereist om HTTP te zijn met een upgrade naar het WebSocket-protocol als een header-veld. Hier volgt een voor beeld van een header:

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

Een andere reden hiervoor is dat de back-end-status test van Application Gateway alleen HTTP-en HTTPS-protocollen ondersteunt. Als de back-endserver niet reageert op HTTP-of HTTPS-tests, wordt de server uit de back-end-pool gehaald.

## <a name="next-steps"></a>Volgende stappen

Nadat u hebt leren over WebSocket-ondersteuning, gaat u naar [een toepassings gateway maken](quick-create-powershell.md) om aan de slag te gaan met een webtoepassing met WebSocket.