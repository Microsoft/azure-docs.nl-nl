---
title: Configuratie van Azure-toepassing gateway-listener
description: In dit artikel wordt beschreven hoe u Azure-toepassing gateway-listeners configureert.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 15f68e8cbca65e7b970944f7ca5ef1952140cc6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397635"
---
# <a name="application-gateway-listener-configuration"></a>Configuratie van Application Gateway listener

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Een listener is een logische entiteit die controleert op binnenkomende verbindings aanvragen met behulp van de poort, het Protocol, de host en het IP-adres. Wanneer u de listener configureert, moet u waarden opgeven voor deze die overeenkomen met de overeenkomende waarden in de inkomende aanvraag op de gateway.

Wanneer u een toepassings gateway maakt met behulp van de Azure Portal, maakt u ook een standaard-listener door het protocol en de poort voor de listener te kiezen. U kunt kiezen of u ondersteuning voor HTTP2 wilt inschakelen voor de listener. Nadat u de toepassings gateway hebt gemaakt, kunt u de instellingen van de standaard-listener (*appGatewayHttpListener*) bewerken of nieuwe listeners maken.

## <a name="listener-type"></a>Type listener

Wanneer u een nieuwe listener maakt, kiest u tussen een [ *basis* en een *multi-site*](./application-gateway-components.md#types-of-listeners).

- Als u wilt dat al uw aanvragen (voor elk domein) worden geaccepteerd en doorgestuurd naar back-endservers, kiest u basis. Meer informatie [over het maken van een toepassings gateway met een Basic-listener](./quick-create-portal.md).

- Als u aanvragen wilt door sturen naar verschillende back-Pools op basis van de *host* -header of hostnamen, kiest u multi-site listener, waarbij u ook een hostnaam moet opgeven die overeenkomt met de inkomende aanvraag. Dit komt omdat Application Gateway afhankelijk zijn van HTTP 1,1-hostheaders om meer dan één website op hetzelfde open bare IP-adres en dezelfde poort te hosten. Zie [meerdere sites hosten met behulp van Application Gateway](multiple-site-overview.md)voor meer informatie.

### <a name="order-of-processing-listeners"></a>Volg orde van de verwerkings listeners

Voor de V1-SKU worden aanvragen vergeleken volgens de volg orde van de regels en het type listener. Als een regel met Basic listener het eerst in de bestelling komt, wordt deze eerst verwerkt en wordt elke aanvraag voor die poort en IP-combi natie geaccepteerd. Om dit te voor komen, configureert u de regels met multi-site listeners eerst en pusht u de regel met de Basic-listener naar de laatste in de lijst.

Voor de v2-SKU worden multi-site-listeners verwerkt vóór basis-listeners.

## <a name="front-end-ip-address"></a>Front-end-IP-adres

Kies het front-end-IP-adres dat u wilt koppelen aan deze listener. De listener luistert naar binnenkomende aanvragen op dit IP-adres.

## <a name="front-end-port"></a>Front-end-poort

Kies de front-end poort. Selecteer een bestaande poort of maak een nieuwe. Kies een wille keurige waarde in het [toegestane bereik van poorten](./application-gateway-components.md#ports). U kunt niet alleen bekende poorten gebruiken, zoals 80 en 443, maar elke toegestane aangepaste poort die geschikt is. Een poort kan worden gebruikt voor open bare listeners of op privé gerichte listeners.

## <a name="protocol"></a>Protocol

Kies HTTP of HTTPS:

- Als u HTTP kiest, wordt het verkeer tussen de client en de toepassings gateway niet versleuteld.

- Kies HTTPS als u [TLS-beëindiging](features.md#secure-sockets-layer-ssltls-termination) of [end-to-end TLS-versleuteling](./ssl-overview.md)wilt. Het verkeer tussen de client en de toepassings gateway is versleuteld. En de TLS-verbinding wordt beëindigd bij de toepassings gateway. Als u end-to-end TLS-code ring wilt, moet u HTTPS kiezen en de **http-instelling back-end** configureren. Dit zorgt ervoor dat verkeer wordt opnieuw versleuteld wanneer het van de toepassings gateway naar de back-end wordt verplaatst.


Als u TLS-beëindiging en end-to-end TLS-code ring wilt configureren, moet u een certificaat aan de listener toevoegen om ervoor te zorgen dat de toepassings gateway een symmetrische sleutel kan afleiden. Dit wordt bepaald door de TLS-protocol specificatie. De symmetrische sleutel wordt gebruikt voor het versleutelen en ontsleutelen van het verkeer dat naar de gateway wordt verzonden. Het gateway certificaat moet de PFX-indeling (Personal Information Exchange) hebben. Met deze indeling kunt u de persoonlijke sleutel exporteren die door de gateway wordt gebruikt voor het versleutelen en ontsleutelen van verkeer.

## <a name="supported-certificates"></a>Ondersteunde certificaten

Zie [overzicht van TLS-beëindiging en end-to-end TLS met Application Gateway](ssl-overview.md#certificates-supported-for-tls-termination)

## <a name="additional-protocol-support"></a>Aanvullende protocol ondersteuning

### <a name="http2-support"></a>HTTP2-ondersteuning

Ondersteuning voor HTTP/2-protocollen is beschikbaar voor clients die alleen verbinding maken met de Application Gateway-listeners. De communicatie met back-end-server groepen is via HTTP/1.1. HTTP/2-ondersteuning is standaard uitgeschakeld. In het volgende code fragment van Azure PowerShell ziet u hoe u dit kunt inschakelen:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>Ondersteuning voor WebSocket

Ondersteuning voor websockets is standaard ingeschakeld. Er is geen door de gebruiker Configureer bare instelling om deze in of uit te scha kelen. U kunt websockets gebruiken met HTTP-en HTTPS-listeners.

## <a name="custom-error-pages"></a>Aangepaste foutpagina's

U kunt een aangepaste fout op globaal niveau of op het niveau van de listener definiëren. Het maken van aangepaste fout pagina's op globaal niveau vanuit het Azure Portal wordt momenteel niet ondersteund. U kunt een aangepaste fout pagina configureren voor een 403-Web Application Firewall fout of een 502-onderhouds pagina op het niveau van de listener. U moet ook een openbaar toegankelijke BLOB-URL opgeven voor de gegeven fout status code. Zie voor meer informatie [Aangepaste foutpagina's maken voor Application Gateway](./custom-error.md).

![Application Gateway fout codes](/azure/application-gateway/media/custom-error/ag-error-codes.png)

Als u een algemene aangepaste fout pagina wilt configureren, raadpleegt u [Azure PowerShell-configuratie](./custom-error.md#azure-powershell-configuration).

## <a name="tls-policy"></a>TLS-beleid

U kunt TLS/SSL-certificaat beheer centraliseren en de overhead voor het ontsleutelen van versleuteling voor een back-endserver verlagen. Met gecentraliseerde TLS-verwerking kunt u ook een centraal TLS-beleid opgeven dat geschikt is voor uw beveiligings vereisten. U kunt *standaard*, *vooraf gedefinieerd* of *aangepast* TLS-beleid kiezen.

U configureert TLS-beleid voor het beheren van TLS-protocol versies. U kunt een toepassings gateway configureren voor het gebruik van een minimale Protocol versie voor TLS-Handshakes van TLS 1.0, TLS 1.1 en TLS 1.2. SSL 2,0 en 3,0 zijn standaard uitgeschakeld en kunnen niet worden geconfigureerd. Zie [Application Gateway TLS-beleids overzicht](./application-gateway-ssl-policy-overview.md)voor meer informatie.

Nadat u een listener hebt gemaakt, koppelt u deze aan een regel voor het door sturen van aanvragen. Deze regel bepaalt hoe aanvragen die worden ontvangen op de listener worden doorgestuurd naar de back-end.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over routerings regels voor aanvragen](configuration-request-routing-rules.md).