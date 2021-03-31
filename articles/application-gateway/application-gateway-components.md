---
title: Onderdelen van Application Gateway
description: Dit artikel bevat informatie over de verschillende onderdelen in een toepassings gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: surmb
ms.openlocfilehash: ebd06b0b78ee511dce535ff4220df03087fb6906
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88723313"
---
# <a name="application-gateway-components"></a>Onderdelen van Application Gateway

 Een toepassings gateway fungeert als één aanspreek punt voor clients. Het distribueert het binnenkomende toepassings verkeer over meerdere back-end-Pools, waaronder Azure-Vm's, schaal sets voor virtuele machines, Azure App Service en on-premises/externe servers. Voor het distribueren van verkeer gebruikt een toepassings gateway verschillende onderdelen die in dit artikel worden beschreven.

![De onderdelen die worden gebruikt in een toepassings gateway](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Frontend-IP-adressen

Een frontend-IP-adres is het IP-adres dat is gekoppeld aan een toepassings gateway. U kunt een toepassings gateway configureren voor een openbaar IP-adres, een privé IP-adres of beide. Een toepassings gateway ondersteunt één openbaar of een privé-IP-adres. Het virtuele netwerk en het open bare IP-adres moeten zich op dezelfde locatie bevindt als uw toepassings gateway. Nadat de app is gemaakt, wordt een frontend-IP-adres gekoppeld aan een listener.

### <a name="static-versus-dynamic-public-ip-address"></a>Statisch versus dynamisch openbaar IP-adres

De SKU v2 van de Azure-toepassing gateway kan worden geconfigureerd voor het ondersteunen van zowel een statisch intern IP-adres als een statisch openbaar IP-adres, of alleen een statisch openbaar IP-adres. Het kan niet worden geconfigureerd voor ondersteuning van alleen statisch intern IP-adres.

De V1-SKU kan worden geconfigureerd voor het ondersteunen van statisch of dynamisch intern IP-adres en dynamisch openbaar IP-adres. Het dynamische IP-adres van Application Gateway wordt niet gewijzigd op een actieve gateway. Het kan alleen worden gewijzigd wanneer u de gateway stopt of start. Deze wijziging is niet van invloed op systeem fouten, updates, updates van Azure-hosts enz. 

De DNS-naam die is gekoppeld aan een toepassings gateway wordt niet gewijzigd gedurende de levens cyclus van de gateway. Als gevolg hiervan moet u een CNAME-alias gebruiken en deze verwijzen naar het DNS-adres van de toepassings gateway.

## <a name="listeners"></a>Listeners

Een listener is een logische entiteit die controleert op binnenkomende verbindings aanvragen. Een listener accepteert een aanvraag als het Protocol, de poort, de hostnaam en het IP-adres dat is gekoppeld aan de aanvraag, overeenkomen met de elementen die zijn gekoppeld aan de configuratie van de listener.

Voordat u een toepassings gateway gebruikt, moet u ten minste één listener toevoegen. Er kunnen meerdere listeners zijn gekoppeld aan een toepassings gateway en ze kunnen worden gebruikt voor hetzelfde protocol.

Nadat een listener binnenkomende aanvragen van clients heeft gedetecteerd, stuurt de Application Gateway deze aanvragen door naar leden in de back-endadresgroep die in de regel is geconfigureerd.

Listeners ondersteunen de volgende poorten en protocollen.

### <a name="ports"></a>Poorten

Een poort is de locatie waar een listener luistert naar de client aanvraag. U kunt poorten configureren van 1 tot 65502 voor de V1-SKU en 1 tot 65199 voor de v2-SKU.

### <a name="protocols"></a>Protocollen

Application Gateway ondersteunt vier protocollen: HTTP, HTTPS, HTTP/2 en WebSocket:
>[!NOTE]
>Ondersteuning voor HTTP/2-protocollen is alleen beschikbaar voor clients die verbinding maken met de gateway-listeners van de toepassing. De communicatie met back-endserver-Server groepen is altijd over HTTP/1.1. HTTP/2-ondersteuning is standaard uitgeschakeld. U kunt ervoor kiezen om deze functie in te scha kelen.

- Geef de HTTP-en HTTPS-protocollen op in de configuratie van de listener.
- Ondersteuning voor [Websockets en http/2-protocollen](features.md#websocket-and-http2-traffic) wordt systeem eigen en [WebSocket-ondersteuning](application-gateway-websocket.md) is standaard ingeschakeld. Er is geen door de gebruiker configureerbare instelling om selectief WebSocket-ondersteuning in of uit te schakelen. Gebruik websockets met HTTP-en HTTPS-listeners.

Gebruik een HTTPS-listener voor TLS-beëindiging. Een HTTPS-listener versleutelt de versleuteling en ontsleuteling van uw toepassings gateway, zodat uw webservers niet worden belast met de overhead.

### <a name="custom-error-pages"></a>Aangepaste foutpagina's

Met Application Gateway kunt u aangepaste fout pagina's maken in plaats van de standaardfout pagina's weer te geven. U kunt uw eigen huisstijl en lay-out hanteren door een aangepaste foutpagina te gebruiken. Application Gateway wordt een aangepaste fout pagina weer gegeven wanneer een aanvraag de back-end niet kan bereiken.

Zie [aangepaste fout pagina's voor uw toepassings gateway](custom-error.md)voor meer informatie.

### <a name="types-of-listeners"></a>Typen listeners

Er zijn twee soorten listeners:

- **Basic**. Dit type listener luistert naar één domein site, waarbij deze één DNS-toewijzing heeft voor het IP-adres van de toepassings gateway. Deze listener-configuratie is vereist wanneer u één site host achter een toepassings gateway.

- **Meerdere locaties**. Deze listener-configuratie is vereist wanneer u route ring wilt configureren op basis van de hostnaam of domein naam voor meer dan één webtoepassing op dezelfde toepassings gateway. Dit stelt u in staat om een efficiëntere topologie voor uw implementaties te configureren door maximaal 100 websites toe te voegen aan één toepassingsgateway. Elke website kan worden omgeleid naar een eigen back-endpool. Drie domeinen - contoso.com, fabrikam.com en adatum.com - wijzen bijvoorbeeld naar het IP-adres van de toepassingsgateway. U maakt drie [multi-site listeners](multiple-site-overview.md) en configureert elke listener voor de respectieve poort-en protocol instelling. 

    U kunt ook hostnamen met jokertekens definiëren in een listener voor meerdere sites (maximaal vijf hostnamen per listener). Zie [namen van hostnamen in listener (preview)](multiple-site-overview.md#wildcard-host-names-in-listener-preview)voor meer informatie.

    Zie voor meer informatie over het configureren van een multi-site [-listener meerdere sites hosten in Application Gateway met behulp van Azure Portal](create-multiple-sites-portal.md).

Nadat u een listener hebt gemaakt, koppelt u deze aan een routerings regel voor aanvragen. Deze regel bepaalt hoe de ontvangen aanvraag op de listener moet worden doorgestuurd naar de back-end. De regel voor aanvraag routering bevat ook de back-end-groep waarnaar moet worden doorgestuurd en de HTTP-instelling waarin de back-end-poort, het protocol enzovoort worden genoemd.

## <a name="request-routing-rules"></a>Routeringsregels aanvragen

Een regel voor het routeren van aanvragen is een belang rijk onderdeel van een toepassings gateway, omdat deze bepaalt hoe verkeer moet worden gerouteerd op de listener. De regel verbindt de listener, de back-end-server groep en de back-end-HTTP-instellingen.

Wanneer een listener een aanvraag accepteert, stuurt de regel voor aanvraag routering de aanvraag door naar de back-end of wordt deze elders omgeleid. Als de aanvraag wordt doorgestuurd naar de back-end, definieert de routerings regel voor aanvragen welke back-endserver moet worden doorgestuurd naar. De regel voor aanvraag routering bepaalt ook of de kopteksten in de aanvraag opnieuw moeten worden geschreven. Eén listener kan aan één regel worden gekoppeld.

Er zijn twee typen routerings regels voor aanvragen:

- **Basic**. Alle aanvragen op de gekoppelde listener (bijvoorbeeld blog.contoso.com/*) worden doorgestuurd naar de bijbehorende back-end-groep met behulp van de bijbehorende HTTP-instelling.

- **Op basis van een pad**. Met deze routerings regel kunt u de aanvragen van de gekoppelde listener door sturen naar een specifieke back-end-groep, op basis van de URL in de aanvraag. Als het pad van de URL in een aanvraag overeenkomt met het pad patroon in een regel op basis van een pad, stuurt de regel deze aanvraag. Hiermee wordt het pad patroon alleen toegepast op het URL-pad, niet naar de query parameters. Als het URL-pad op een listener-aanvraag niet overeenkomt met een van de op pad gebaseerde regels, stuurt het de aanvraag door naar de standaard back-end-en HTTP-instellingen.

Zie [URL-based route ring](url-route-overview.md)voor meer informatie.

### <a name="redirection-support"></a>Ondersteuning voor omleiding

Met de regel voor aanvraag routering kunt u het verkeer omleiden naar de Application Gateway. Dit is een algemeen mechanisme voor omleiding, zodat u kunt omleiden naar en van elke poort die u definieert met behulp van regels.

U kunt kiezen dat het doel van de omleiding een andere listener is (waarmee automatische HTTP-naar-HTTPS-omleiding kan worden ingeschakeld) of een externe site. U kunt er ook voor kiezen om de omleiding tijdelijk of permanent te laten, of om het URI-pad en de query reeks toe te voegen aan de omgeleide URL.

Zie [verkeer omleiden naar uw toepassings gateway](redirect-overview.md)voor meer informatie.

### <a name="rewrite-http-headers-and-url"></a>HTTP-headers en URL opnieuw schrijven

Met behulp van regels voor herschrijven kunt u HTTP (S)-aanvragen en-antwoord headers toevoegen, verwijderen of bijwerken, evenals het URL-pad en de query reeks parameters, omdat de aanvraag-en antwoord pakketten worden verplaatst tussen de client-en back-end-groepen via de toepassings gateway.

De para meters headers en URL kunnen worden ingesteld op statische waarden of op andere headers en Server variabelen. Dit helpt bij belang rijke gebruiks gevallen, zoals het extra heren van client-IP-adressen, het verwijderen van gevoelige informatie over de back-end, het toevoegen van meer beveiliging, enzovoort.

Zie [HTTP-headers en-url's herschrijven in uw toepassings gateway](rewrite-http-headers-url.md)voor meer informatie.

## <a name="http-settings"></a>HTTP-instellingen

Een toepassings gateway routeert verkeer naar de back-endservers (opgegeven in de routerings regel voor aanvragen die HTTP-instellingen bevatten) met behulp van het poort nummer, het protocol en andere instellingen die in dit onderdeel worden beschreven.

De poort en het protocol dat wordt gebruikt in de HTTP-instellingen bepalen of het verkeer tussen de toepassings gateway en de back-endservers is versleuteld (End-to-end TLS) of niet-versleuteld.

Dit onderdeel wordt ook gebruikt voor het volgende:

- Bepaal of een gebruikers sessie op dezelfde server moet worden bewaard met de [sessie affiniteit op basis van cookies](features.md#session-affinity).

- Back-end-groeps leden zonder problemen verwijderen met behulp van het verwerkings [Stop](features.md#connection-draining).

- Koppel een aangepaste test om de status van de back-end te controleren, de time-outinterval van de aanvraag in te stellen, de hostnaam en het pad in de aanvraag te overschrijven en met één klik te bieden voor het opgeven van instellingen voor de App Service back-end.

## <a name="backend-pools"></a>Back-end-Pools

Een back-end-groep routeert aanvragen naar back-endservers, die de aanvraag verwerken. Back-end-Pools kunnen het volgende bevatten:

- NIC’s
- Virtuele-machineschaalsets
- Openbare IP-adressen
- Interne IP-adressen
- FQDN
- Multi tenant-back-ends (zoals App Service)

Application Gateway back-end-groeps leden zijn niet gekoppeld aan een beschikbaarheidsset. Een toepassings gateway kan communiceren met exemplaren buiten het virtuele netwerk waarin deze zich bevindt. Als gevolg hiervan kunnen de leden van de back-endservers zich in verschillende clusters bevinden, in data centers of buiten Azure, zolang er een IP-verbinding is.

Als u interne Ip's als back-endadresgroep wilt gebruiken, moet u [virtuele netwerk peering](../virtual-network/virtual-network-peering-overview.md) of een [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)gebruiken. Peering van virtuele netwerken wordt ondersteund en is handig voor taak verdeling van verkeer in andere virtuele netwerken.

Een toepassings gateway kan ook communiceren met naar on-premises servers wanneer ze zijn verbonden met Azure ExpressRoute of VPN-tunnels als verkeer is toegestaan.

U kunt verschillende back-endservers maken voor verschillende typen aanvragen. Maak bijvoorbeeld één back-end-groep voor algemene aanvragen en vervolgens een andere back-end-groep voor aanvragen van de micro Services voor uw toepassing.

## <a name="health-probes"></a>Statuscontroles

Standaard controleert een toepassings gateway de status van alle resources in de back-end-pool en worden de beschadigde items automatisch verwijderd. Vervolgens worden de beschadigde instanties bewaakt en worden ze weer toegevoegd aan de juiste back-end-groep wanneer deze beschikbaar komen en reageren op status controles.

Naast het gebruik van de standaard controle van de status test, kunt u ook de status test aanpassen aan de vereisten van uw toepassing. Met aangepaste tests kunt u de controle over de status controle nauw keuriger maken. Wanneer u aangepaste tests gebruikt, kunt u een aangepaste hostnaam, een URL-pad, een test interval, en het aantal mislukte antwoorden dat moet worden geaccepteerd voordat u het exemplaar van de back-end-groep markeert als beschadigd, aangepaste status codes en overeenkomen met de antwoord tekst, enzovoort configureren. U wordt aangeraden aangepaste tests te configureren om de status van elke back-end-groep te controleren.

Zie [de status van uw toepassings gateway bewaken](../application-gateway/application-gateway-probe-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Een toepassings gateway maken:

* [In de Azure-Portal](quick-create-portal.md)
* [Met behulp van Azure PowerShell](quick-create-powershell.md)
* [Via de Azure CLI](quick-create-cli.md)
