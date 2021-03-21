---
title: Veelgestelde vragen over Azure-toepassing gateway
description: Vind antwoorden op veelgestelde vragen over Azure-toepassing gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: 83779dcd319614ae15de6b7e3e4e3abfd9599089
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102619151"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Veelgestelde vragen over Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hier volgen enkele veelgestelde vragen over Azure-toepassing gateway.

## <a name="general"></a>Algemeen

### <a name="what-is-application-gateway"></a>Wat is Application Gateway?

Azure-toepassing gateway biedt een ADC (Application Delivery controller) als een service. Het biedt diverse Layer 7-functies voor taak verdeling voor uw toepassingen. Deze service is Maxi maal beschikbaar en schaalbaar en volledig beheerd door Azure.

### <a name="what-features-does-application-gateway-support"></a>Welke functies ondersteunt Application Gateway?

Application Gateway ondersteunt automatisch schalen, TLS-offloading en end-to-end TLS, een Web Application Firewall (WAF), sessie affiniteit op basis van cookies, op URL-pad gebaseerde route ring, hosting van meerdere sites en andere functies. Zie [Inleiding tot Application Gateway](./overview.md)voor een volledige lijst met ondersteunde functies.

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Hoe verschillen Application Gateway en Azure Load Balancer?

Application Gateway is een laag 7-load balancer, wat betekent dat het alleen werkt met webverkeer (HTTP, HTTPS, WebSocket en HTTP/2). Het ondersteunt mogelijkheden als TLS-beëindiging, sessie affiniteit op basis van cookies en round robin voor verkeer voor taak verdeling. Load Balancer load-balanceert het verkeer op laag 4 (TCP of UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Welke protocollen Application Gateway ondersteunen?

Application Gateway ondersteunt HTTP, HTTPS, HTTP/2 en WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Hoe ondersteunt Application Gateway HTTP/2?

Zie [http/2-ondersteuning](./configuration-listeners.md#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Welke resources worden ondersteund als onderdeel van een back-end-groep?

Bekijk de [ondersteunde back-end-bronnen](./application-gateway-components.md#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>In welke regio's is Application Gateway beschikbaar?

Application Gateway v1 (Standard en WAF) is beschikbaar in alle regio's van wereld wijd Azure. Het is ook beschikbaar in [Azure China 21vianet](https://www.azure.cn/) en [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

Zie voor de beschik baarheid van Application Gateway v2 (Standard_v2 en WAF_v2) [ondersteunde regio's voor Application Gateway v2](./application-gateway-autoscaling-zone-redundant.md#supported-regions)

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Is deze implementatie specifiek voor mijn abonnement of wordt deze gedeeld door klanten?

Application Gateway is een speciale implementatie in uw virtuele netwerk.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Ondersteunt Application Gateway HTTP-naar-HTTPS-omleiding?

Omleiding wordt ondersteund. Zie [Application Gateway omleidings overzicht](./redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>In welke volg orde worden listeners verwerkt?

Bekijk de [volg orde van de verwerking van de listener](./configuration-listeners.md#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Waar vind ik de Application Gateway IP en DNS?

Als u een openbaar IP-adres gebruikt als een eind punt, vindt u de IP-en DNS-informatie over de open bare IP-adres resource. U kunt deze ook vinden in de portal, op de pagina overzicht voor de toepassings gateway. Als u interne IP-adressen gebruikt, vindt u de informatie op de pagina overzicht.

Open voor de v2-SKU de open bare IP-resource en selecteer **configuratie**. Het **DNS-naam label (optioneel)** veld is beschikbaar voor het configureren van de DNS-naam.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Wat zijn de instellingen voor Keep-Alive time-out en TCP-time-out voor inactiviteit?

*Keep-Alive time-out* bepaalt hoe lang de Application Gateway wacht totdat een client een andere HTTP-aanvraag verzendt naar een permanente verbinding voordat deze opnieuw gebruikt of wordt gesloten. *TCP-time-out voor inactiviteit* bepaalt hoe lang een TCP-verbinding open blijft in het geval van geen activiteit. 

De *time-out voor Keep-Alive* in de Application Gateway v1-sku is 120 seconden en in de v2-sku is 75 seconden. De *time-out voor TCP-inactiviteit* is een standaard waarde van 4 minuten voor het frontend-VIP (Virtual IP) van zowel de v1-als v2 SKU van Application Gateway. U kunt de time-outwaarde voor inactiviteit van de TCP-en v2-toepassings gateway configureren voor een periode van 4 minuten tot 30 minuten. Voor zowel v1-als v2-toepassings gateways moet u naar het open bare IP-adres van de Application Gateway navigeren en de time-out voor TCP-inactiviteit wijzigen op de Blade ' configuratie ' van het open bare IP-adres op de portal. U kunt de time-outwaarde voor inactiviteit van het open bare IP-adres instellen via Power shell door de volgende opdrachten uit te voeren: 

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Verandert de IP-of DNS-naam gedurende de levens duur van de toepassings gateway?

In Application Gateway v1 SKU kan het VIP worden gewijzigd als u de toepassings gateway stopt en start. Maar de DNS-naam die aan de toepassings gateway is gekoppeld, wordt niet gewijzigd gedurende de levens duur van de gateway. Omdat de DNS-naam niet wordt gewijzigd, moet u een CNAME-alias gebruiken en deze verwijzen naar het DNS-adres van de toepassings gateway. In Application Gateway v2 SKU kunt u het IP-adres instellen als statisch, zodat de IP-en DNS-naam niet wordt gewijzigd gedurende de levens duur van de toepassings gateway. 

### <a name="does-application-gateway-support-static-ip"></a>Ondersteunt Application Gateway statisch IP-adres?

Ja, de SKU van Application Gateway v2 ondersteunt statische open bare IP-adressen. De V1-SKU ondersteunt statische interne Ip's.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Ondersteunt Application Gateway meerdere open bare IP-adressen op de gateway?

Een toepassings gateway ondersteunt slechts één openbaar IP-adres.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Hoe groot moet ik mijn subnet voor Application Gateway maken?

Zie [Application Gateway de grootte](./configuration-infrastructure.md#size-of-the-subnet)van het subnet.

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Kan ik meer dan één Application Gateway resource op één subnet implementeren?

Ja. Naast meerdere exemplaren van een bepaalde Application Gateway-implementatie, kunt u een andere unieke Application Gateway resource inrichten voor een bestaand subnet dat een andere Application Gateway resource bevat.

Eén subnet biedt geen ondersteuning voor zowel v2-als v1 Application Gateway-Sku's.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Ondersteunt Application Gateway V2 door de gebruiker gedefinieerde routes (UDR)?

Ja, maar alleen specifieke scenario's. Zie [Application Gateway Infrastructure Configuration](configuration-infrastructure.md#supported-user-defined-routes)(Engelstalig) voor meer informatie.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Ondersteunt Application Gateway x-doorgestuurd-voor kopteksten?

Ja. Zie [wijzigingen aan een aanvraag](./how-application-gateway-works.md#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Hoe lang duurt het om een toepassings gateway te implementeren? Werkt mijn toepassings gateway terwijl deze wordt bijgewerkt?

Het kan Maxi maal 20 minuten duren voordat nieuwe SKU-implementaties van Application Gateway v1 beschikbaar zijn. Wijzigingen in de exemplaar grootte of het aantal zijn niet verstoord en de gateway blijft actief gedurende deze periode.

De meeste implementaties die gebruikmaken van de v2-SKU nemen ongeveer 6 minuten in beslag. Het kan echter langer duren, afhankelijk van het type implementatie. Implementaties over meerdere Beschikbaarheidszones met veel instanties kunnen bijvoorbeeld meer dan 6 minuten duren. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Kan ik Exchange Server gebruiken als een back-end met Application Gateway?

Nee. Application Gateway biedt geen ondersteuning voor e-mail protocollen zoals SMTP, IMAP en POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Zijn er richt lijnen beschikbaar om te migreren van de V1-SKU naar de v2-SKU?

Ja. Voor meer informatie raadpleegt u [Azure-toepassing gateway en Web Application firewall van v1 naar v2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Wordt de Application Gateway v1-SKU nog steeds ondersteund?

Ja. De SKU van Application Gateway v1 wordt nog steeds ondersteund. Het wordt echter ten zeerste aanbevolen om over te stappen op v2 om te profiteren van de functie-updates in die SKU. Zie voor meer informatie automatisch [schalen en zone-redundante Application Gateway v2](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>Ondersteunt Application Gateway v2 proxy aanvragen met NTLM-verificatie?

Nee. Application Gateway v2 biedt geen ondersteuning voor proxy aanvragen met NTLM-verificatie.

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Ondersteunt Application Gateway kenmerk SameSite-cookie?
Ja, de [V80-update](https://chromiumdash.appspot.com/schedule) van de [chroom browser](https://www.chromium.org/Home) heeft een mandaat geïntroduceerd op http-cookies zonder SameSite kenmerk dat moet worden behandeld als SameSite = slordig. Dit betekent dat de Application Gateway affiniteits cookie niet wordt verzonden door de browser in een context van een derde partij. 

Ter ondersteuning van dit scenario wordt door Application Gateway een andere cookie met de naam *ApplicationGatewayAffinityCORS* , naast de bestaande *ApplicationGatewayAffinity* cookie injecteerd.  Deze cookies zijn vergelijkbaar, maar aan de *ApplicationGatewayAffinityCORS* -cookie zijn twee meer kenmerken toegevoegd: *SameSite = none; Beveiligd*. Deze kenmerken behouden plak sessies, zelfs voor cross-Origin-aanvragen. Zie de [sectie affiniteit op basis van cookies](configuration-http-settings.md#cookie-based-affinity) voor meer informatie.

## <a name="performance"></a>Prestaties

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Hoe ondersteunt Application Gateway hoge Beschik baarheid en schaal baarheid?

De Application Gateway v1-SKU ondersteunt scenario's met hoge Beschik baarheid wanneer u twee of meer exemplaren hebt geïmplementeerd. Azure distribueert deze instanties over update-en fout domeinen om ervoor te zorgen dat de exemplaren niet allemaal op hetzelfde moment worden uitgevoerd. De V1-SKU ondersteunt schaal baarheid door meerdere exemplaren van dezelfde gateway toe te voegen om de belasting te delen.

De v2-SKU zorgt er automatisch voor dat nieuwe instanties worden verspreid over fout domeinen en update domeinen. Als u zone redundantie kiest, worden de nieuwste instanties ook verspreid over de beschik bare zones om zonegebonden-fout tolerantie aan te bieden.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Hoe kan ik een DR-scenario in data centers behaalt met behulp van Application Gateway?

Gebruik Traffic Manager om verkeer te verdelen over meerdere toepassings gateways in verschillende data centers.

### <a name="does-application-gateway-support-autoscaling"></a>Ondersteunt Application Gateway automatisch schalen?

Ja, de SKU van Application Gateway v2 ondersteunt automatisch schalen. Zie voor meer informatie automatisch [schalen en zone-redundante Application Gateway](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>Leidt hand matig of automatisch omhoog of omlaag schalen tot uitval tijd?

Nee. Instanties worden gedistribueerd over upgrade domeinen en fout domeinen.

### <a name="does-application-gateway-support-connection-draining"></a>Ondersteunt Application Gateway verbindings afvoer?

Ja. U kunt de verbinding verbreken instellen om leden binnen een back-end-groep zonder onderbreking te wijzigen. Zie voor meer informatie de [sectie verbindings afvoer van Application Gateway](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Kan ik de instantie grootte wijzigen van gemiddeld in groot, zonder onderbreking?

Ja.

## <a name="configuration"></a>Configuratie

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Wordt Application Gateway altijd geïmplementeerd in een virtueel netwerk?

Ja. Application Gateway wordt altijd geïmplementeerd in een subnet van een virtueel netwerk. Dit subnet kan alleen toepassings gateways bevatten. Zie [vereisten voor virtuele netwerken en subnetten](./configuration-infrastructure.md#virtual-network-and-dedicated-subnet)voor meer informatie.

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Kan Application Gateway communiceren met exemplaren buiten het virtuele netwerk of buiten het bijbehorende abonnement?

Als u een IP-verbinding hebt, kan Application Gateway communiceren met exemplaren buiten het virtuele netwerk waarin het zich bevindt. Application Gateway kunt ook communiceren met exemplaren buiten het abonnement waarin deze zich bevinden. Als u interne Ip's als back-endadresgroep wilt gebruiken, gebruik dan [virtuele netwerk peering](../virtual-network/virtual-network-peering-overview.md) of [Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Kan ik iets anders in het subnet van de toepassings Gateway implementeren?

Nee. Maar u kunt ook andere toepassings gateways implementeren in het subnet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Worden netwerk beveiligings groepen ondersteund op het subnet van de toepassings gateway?

Zie [netwerk beveiligings groepen in het Application Gateway subnet](./configuration-infrastructure.md#network-security-groups).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Ondersteunt het subnet van de toepassings gateway door de gebruiker gedefinieerde routes?

Zie door [de gebruiker gedefinieerde routes die worden ondersteund in het Application Gateway subnet](./configuration-infrastructure.md#supported-user-defined-routes).

### <a name="are-service-endpoint-policies-supported-in-the-application-gateway-subnet"></a>Worden service-eindpunt beleidsregels ondersteund in het subnet van Application Gateway?

Nee. [Beleids regels voor service-eind punten](../virtual-network/virtual-network-service-endpoint-policies-overview.md) voor opslag accounts worden niet ondersteund in Application Gateway subnet en het configureren van Azure-infrastructuur verkeer wordt geblokkeerd.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Wat zijn de limieten voor Application Gateway? Kan ik deze limieten verhogen?

Zie [Application Gateway limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Kan ik Application Gateway gelijktijdig gebruiken voor zowel extern als intern verkeer?

Ja. Application Gateway ondersteunt één intern IP-adres en één extern IP-adres per toepassings gateway.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Ondersteunt Application Gateway peering voor virtuele netwerken?

Ja. Met peering voor virtuele netwerken kunt u verkeer verdelen in andere virtuele netwerken.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Kan ik praten met on-premises servers wanneer ze zijn verbonden via ExpressRoute of VPN-tunnels?

Ja, zolang verkeer is toegestaan.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Kan één back-end-pool veel toepassingen op verschillende poorten gebruiken?

Micro service-architectuur wordt ondersteund. Als u op verschillende poorten wilt testen, moet u meerdere HTTP-instellingen configureren.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Ondersteunen aangepaste tests joker tekens of regex op antwoord gegevens?

Nee. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Hoe worden routerings regels verwerkt in Application Gateway?

Zie [volg orde van verwerkings regels](./configuration-request-routing-rules.md#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Wat is het veld host voor aangepaste tests?

In het veld host geeft u de naam op voor het verzenden van de test wanneer u meerdere locaties op Application Gateway hebt geconfigureerd. Gebruik anders ' 127.0.0.1 '. Deze waarde wijkt af van de hostnaam van de virtuele machine. De notatie is \<protocol\> :// \<host\> : \<port\> \<path\> .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Kan ik Application Gateway toegang tot slechts enkele bron-IP-adressen toestaan?

Ja. Zie [toegang tot specifieke bron-ip's beperken](./configuration-infrastructure.md#allow-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Kan ik dezelfde poort gebruiken voor zowel open bare als privé gerichte listeners?

Nee.

### <a name="does-application-gateway-support-ipv6"></a>Ondersteunt Application Gateway IPv6?

Application Gateway v2 biedt momenteel geen ondersteuning voor IPv6. Het kan worden gebruikt in een dual stack VNet met alleen IPv4, maar het gateway-subnet moet alleen IPv4 zijn. Application Gateway v1 biedt geen ondersteuning voor dual stack-VNets. 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Application Gateway v2 met alleen het persoonlijke frontend-IP-adres Hoe kan ik gebruiken?

Application Gateway v2 biedt momenteel geen ondersteuning voor de modus Private IP. Het ondersteunt de volgende combi Naties
* Privé-IP en open bare IP
* Alleen openbaar IP

Maar als u Application Gateway v2 wilt gebruiken met alleen particulier IP-adres, kunt u het onderstaande proces volgen:
1. Een Application Gateway maken met zowel het open bare als het persoonlijke frontend-IP-adres
2. Maak geen listeners voor het open bare frontend-IP-adres. Application Gateway luistert niet naar verkeer op het open bare IP-adres als er geen listeners worden gemaakt.
3. Maak en koppel een [netwerk beveiligings groep](../virtual-network/network-security-groups-overview.md) voor het subnet Application Gateway met de volgende configuratie in volg orde van prioriteit:
    
    a. Sta verkeer toe van de bron als **GatewayManager** -service label en-bestemming als **een** doel poort van **65200-65535**. Dit poort bereik is vereist voor de communicatie van Azure-infra structuur. Deze poorten worden beveiligd (vergrendeld) door verificatie via certificaat. Externe entiteiten, met inbegrip van de gebruikers beheerders van de gateway, kunnen geen wijzigingen op deze eind punten initiëren zonder dat de juiste certificaten aanwezig zijn
    
    b. Verkeer toestaan van bron als **AzureLoadBalancer** -service label en bestemming en doel poort 
    
    c. Alle binnenkomend verkeer van de bron als de code van de **Internet** -service en de doel-en doel poort als **elk** te weigeren. Geef deze regel de *minste prioriteit* in de regels voor binnenkomende verbindingen
    
    d. Behoud de standaard regels zoals het toestaan van VirtualNetwork inkomend zodat de toegang op privé-IP-adres niet wordt geblokkeerd
    
    e. De uitgaande Internet verbinding kan niet worden geblokkeerd. Anders worden er problemen met logboek registratie, metrische gegevens, enzovoort.

Voor beeld van NSG-configuratie voor alleen persoonlijke IP-toegang: ![ Application Gateway v2 NSG configuratie alleen voor privé-IP-toegang](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>Configuratie-TLS

### <a name="what-certificates-does-application-gateway-support"></a>Welke certificaten ondersteunt Application Gateway?

Application Gateway ondersteunt zelfondertekende certificaten, certificaten van certificerings instanties, certificaten voor uitgebreide validatie (EV), certificaten voor meerdere domeinen (SAN) en Joker certificaten.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Welke coderings suites ondersteunt Application Gateway?

Application Gateway ondersteunt de volgende coderings suites. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Zie [TLS-beleids versies en coderings suites configureren op Application Gateway](application-gateway-configure-ssl-policy-powershell.md)voor meer informatie over het aanpassen van TLS-opties.

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Ondersteunt Application Gateway het opnieuw versleutelen van verkeer naar de back-end?

Ja. Application Gateway ondersteunt TLS-offload en end-to-end TLS, waarmee het verkeer wordt opnieuw versleuteld naar de back-end.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Kan ik TLS-beleid configureren voor het beheren van TLS-protocol versies?

Ja. U kunt Application Gateway zodanig configureren dat TLS 1.0, TLS 1.1 en TLS 1.2 worden geweigerd. SSL 2,0 en 3,0 zijn standaard al uitgeschakeld en kunnen niet worden geconfigureerd.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Kan ik coderings suites en beleids volgorde configureren?

Ja. In Application Gateway kunt u [coderings suites configureren](application-gateway-ssl-policy-overview.md). Schakel ten minste één van de volgende coderings suites in om een aangepast beleid te definiëren. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway maakt gebruik van SHA256 voor back-end-beheer.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Hoeveel TLS/SSL-certificaten ondersteunt Application Gateway?

Application Gateway ondersteunt Maxi maal 100 TLS/SSL-certificaten.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Hoeveel authenticatie certificaten voor back-end versleuteling Application Gateway ondersteuning?

Application Gateway ondersteunt Maxi maal 100 verificatie certificaten.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Biedt Application Gateway systeem eigen integratie met Azure Key Vault?

Ja, de SKU van Application Gateway v2 ondersteunt Key Vault. Zie [TLS-beëindiging met Key Vault-certificaten](key-vault-certs.md) voor meer informatie.

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>HTTPS-listeners Hoe kan ik configureren voor. com-en .net-sites? 

Voor meerdere op een domein gebaseerde (op een host gebaseerde) route ring kunt u multi site-listeners maken, listeners instellen die HTTPS gebruiken als protocol en de listeners koppelen aan de routerings regels. Zie voor meer informatie [meerdere sites hosten met behulp van Application Gateway](./multiple-site-overview.md).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Kan ik speciale tekens in het wacht woord voor het pfx-bestand gebruiken?

Nee, gebruik alleen alfanumerieke tekens in het wacht woord voor het pfx-bestand.

### <a name="my-ev-certificate-is-issued-by-digicert-and-my-intermediate-certificate-has-been-revoked-how-do-i-renew-my-certificate-on-application-gateway"></a>Mijn EV-certificaat wordt uitgegeven door DigiCert en mijn tussenliggende certificaat is ingetrokken. Hoe kan ik mijn certificaat op Application Gateway vernieuwen?

De browser van de certificerings instantie (CA) meldt recent rapporten die zijn uitgegeven door leveranciers van de certificerings instantie die door onze klanten, micro soft en de grotere technologie worden gebruikt en die niet in overeenstemming zijn met de industrie normen voor openbaar vertrouwde certificerings instanties.De rapporten met betrekking tot de niet-compatibele Ca's vindt u hier:  

* [Bug 1649951](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)
* [Bug 1650910](https://bugzilla.mozilla.org/show_bug.cgi?id=1650910)

Conform de nalevings vereisten van de branche begon de leveranciers van de certificerings instantie niet-compatibele certificerings instanties aan te roepen en certificaten te verlenen die voldoen aan het beleid.Micro soft werkt nauw samen met deze leveranciers om de potentiële impact op Azure-Services te minimaliseren, **maar uw zelf verleende certificaten of certificaten die worden gebruikt in de scenario's ' uw eigen certificaat gebruiken ' (BYOC) zijn nog steeds kwetsbaar voor onverwacht gebruik**.

Als u wilt controleren of de certificaten die door uw toepassing worden gebruikt, zijn ingetrokken, geeft u de [aankondiging van de DigiCert](https://knowledge.digicert.com/alerts/DigiCert-ICA-Replacement) en de [intrekkings tracering](https://misissued.com/#revoked)voor het certificaat op. Als uw certificaten zijn ingetrokken of worden ingetrokken, moet u nieuwe certificaten aanvragen bij de CA-leverancier die in uw toepassingen wordt gebruikt. Als u wilt voor komen dat de beschik baarheid van uw toepassing wordt onderbroken omdat de certificaten onverwacht zijn ingetrokken, of als u een ingetrokken certificaat wilt bijwerken, raadpleegt u de Azure updates post voor herstel koppelingen van verschillende Azure-Services die ondersteuning bieden voor BYOC: https://azure.microsoft.com/updates/certificateauthorityrevocation/

Zie hieronder voor Application Gateway specifieke informatie.

Als u een certificaat gebruikt dat is uitgegeven door een van de ingetrokken ICAs, kan de beschik baarheid van uw toepassing worden onderbroken en afhankelijk van uw toepassing, kunnen er diverse fout berichten worden weer gegeven, inclusief, maar niet beperkt tot: 

1.  Ongeldig certificaat/ingetrokken certificaat
2.  Time-out opgetreden voor verbinding
3.  HTTP 502

U moet de volgende acties uitvoeren om te voor komen dat uw toepassing wordt onderbroken wegens dit probleem, of om een ingetrokken certificerings instantie opnieuw te verlenen: 

1.  Neem contact op met uw certificaat provider voor het opnieuw uitgeven van uw certificaten
2.  Nadat het opnieuw is uitgegeven, werkt u uw certificaten op de Azure-toepassing gateway-WAF bij met de volledige [vertrouwens keten](/windows/win32/seccrypto/certificate-chains) (Leaf, tussenliggend, basis certificaat). Voer de onderstaande stappen uit om de certificaten bij te werken, op basis van waar u uw certificaat gebruikt, hetzij op de listener of de HTTP-instellingen van de Application Gateway, en Raadpleeg de documentatie koppelingen die worden vermeld voor meer informatie.
3.  Werk uw back-end-toepassings servers bij om het opnieuw uitgegeven certificaat te gebruiken. Afhankelijk van de back-endserver die u gebruikt, kunnen de stappen voor het bijwerken van het certificaat verschillen. Raadpleeg de documentatie van uw leverancier.

Het certificaat in de listener bijwerken:

1.  Open uw Application Gateway resource in het [Azure Portal](https://portal.azure.com/)
2.  Open de instellingen van de listener die zijn gekoppeld aan uw certificaat
3.  Klik op het geselecteerde certificaat vernieuwen of bewerken
4.  Upload uw nieuwe PFX-certificaat met het wacht woord en klik op opslaan
5.  Ga naar de website en controleer of de site werkt zoals verwacht. Raadpleeg [hier](./renew-certificates.md)de documentatie voor meer informatie.

Als u naar certificaten van Azure-sleutel kluis in uw Application Gateway-listener verwijst, raden we u aan de volgende stappen voor een snelle wijziging aan te brengen:

1.  Ga in het [Azure Portal](https://portal.azure.com/)naar de instellingen van de Azure-sleutel kluis die is gekoppeld aan de Application Gateway
2.  Het opnieuw verleende certificaat in uw archief toevoegen/importeren. Raadpleeg hier de documentatie voor meer informatie over hoe u [Dit](../key-vault/certificates/quick-create-portal.md) kunt doen.
3.  Zodra het certificaat is geïmporteerd, gaat u naar de instellingen van uw Application Gateway-listener en klikt u onder ' Kies een certificaat van Key Vault ' op de vervolg keuzelijst certificaat en kiest u het onlangs toegevoegde certificaat
4.  Klik op opslaan voor meer informatie over het beëindigen van TLS op Application Gateway met Key Vault certificaten. Raadpleeg [hier](./key-vault-certs.md)de documentatie.


Het certificaat in uw HTTP-instellingen bijwerken:

Als u v1 SKU van de Application Gateway/WAF-service gebruikt, moet u het nieuwe certificaat uploaden als uw back-end-verificatie certificaat.
1.  Open uw Application Gateway resource in het [Azure Portal](https://portal.azure.com/)
2.  De HTTP-instellingen openen die aan uw certificaat zijn gekoppeld
3.  Klik op certificaat toevoegen en upload het opnieuw uitgegeven certificaat en klik op opslaan.
4.  U kunt het oude certificaat later verwijderen door te klikken op de '... ' op de knop opties naast het oude certificaat en selecteer verwijderen en klik op opslaan.
Raadpleeg [de documentatie voor](./end-to-end-ssl-portal.md#add-authenticationtrusted-root-certificates-of-back-end-servers)meer informatie.

Als u de v2-SKU van de Application Gateway/WAF-service gebruikt, hoeft u het nieuwe certificaat niet te uploaden in de HTTP-instellingen sinds v2 SKU gebruikmaakt van ' vertrouwde basis certificaten ' en hoeft u hier geen actie te ondernemen.

## <a name="configuration---ingress-controller-for-aks"></a>Configuratie-ingangs controller voor AKS

### <a name="what-is-an-ingress-controller"></a>Wat is een ingangs controller?

Met Kubernetes kan het maken van `deployment` en `service` de resource een groep van Peul intern in het cluster weer geven. Om dezelfde service extern beschikbaar te maken, wordt een [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) resource gedefinieerd die taak verdeling, TLS-beëindiging en op naam gebaseerde virtuele hosting biedt.
Om aan deze `Ingress` resource te voldoen, moet een ingangs controller worden geluisterd naar wijzigingen in `Ingress` resources en wordt het Load Balancer beleid geconfigureerd.

Met de Application Gateway ingangs controller (AGIC) kan [Azure-toepassing gateway](https://azure.microsoft.com/services/application-gateway/) worden gebruikt als binnenkomend verkeer voor een [Azure Kubernetes-service](https://azure.microsoft.com/services/kubernetes-service/) , ook wel een AKS-cluster genoemd.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Kan één exemplaar van een ingangs controller meerdere toepassings gateways beheren?

Op dit moment kan slechts één exemplaar van de ingangs controller aan een Application Gateway worden gekoppeld.

### <a name="why-is-my-aks-cluster-with-kubenet-not-working-with-agic"></a>Waarom werkt mijn AKS-cluster met kubenet niet met AGIC?

AGIC probeert de resource van de route tabel automatisch te koppelen aan het subnet van de Application Gateway, maar kan niet worden uitgevoerd vanwege een gebrek aan machtigingen van de AGIC. Als de route tabel niet kan worden gekoppeld aan het subnet Application Gateway, wordt er een fout weer gegeven in de AGIC-Logboeken. in dat geval moet u de route tabel die door het AKS-cluster is gemaakt, hand matig koppelen aan het subnet van de Application Gateway. Zie Ondersteunde door de [gebruiker gedefinieerde routes](configuration-infrastructure.md#supported-user-defined-routes)voor meer informatie.

### <a name="can-i-connect-my-aks-cluster-and-application-gateway-in-separate-virtual-networks"></a>Kan ik mijn AKS-cluster en Application Gateway in afzonderlijke virtuele netwerken koppelen? 

Ja, zolang de virtuele netwerken zijn gekoppeld en ze geen overlappende adres ruimten hebben. Als u AKS met kubenet uitvoert, moet u ervoor zorgen dat u de door AKS gegenereerde route tabel koppelt aan het subnet Application Gateway. 

### <a name="what-features-are-not-supported-on-the-agic-add-on"></a>Welke functies worden niet ondersteund voor de invoeg toepassing AGIC? 

Bekijk de verschillen tussen de AGIC die zijn geïmplementeerd via helm en geïmplementeerd als een AKS- [](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) invoeg toepassing.

### <a name="when-should-i-use-the-add-on-versus-the-helm-deployment"></a>Wanneer moet ik de invoeg toepassing gebruiken versus de helm-implementatie? 

Zie de verschillen tussen de AGIC die zijn geïmplementeerd via helm en geïmplementeerd als een AKS-invoeg toepassing, met name de tabellen die worden gebruikt voor het gebruik van AGIC die worden geïmplementeerd via helm, in [plaats van een](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)AKS-invoeg toepassing. Over het algemeen kunt u met helm implementeren van bèta functies en kandidaten voor een officiële versie testen. 

### <a name="can-i-control-which-version-of-agic-will-be-deployed-with-the-add-on"></a>Kan ik bepalen welke versie van AGIC wordt geïmplementeerd met de invoeg toepassing?

Nee, AGIC-invoeg toepassing is een beheerde service, wat betekent dat micro soft de invoeg toepassing automatisch bijwerkt naar de nieuwste stabiele versie. 

## <a name="diagnostics-and-logging"></a>Diagnostische gegevens en logboekregistratie

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Welke typen logboeken bevat Application Gateway?

Application Gateway biedt drie logboeken: 

* **ApplicationGatewayAccessLog**: het toegangs logboek bevat elke aanvraag die is verzonden naar het front Application Gateway-front-end. De gegevens omvatten het IP-adres van de beller, de aangevraagde URL, reactie latentie, retour code en bytes in en uit. Het bevat één record per toepassings gateway.
* **ApplicationGatewayPerformanceLog**: in het prestatie logboek worden prestatie gegevens voor elke toepassings gateway vastgelegd. De informatie omvat de door Voer in bytes, totaal aantal geleverde aanvragen, aantal mislukte aanvragen en een gezonde en slechte back-end van een backend-exemplaar.
* **ApplicationGatewayFirewallLog**: voor toepassings gateways die u CONFIGUREERT met WAF, bevat het logboek van de firewall aanvragen die zijn geregistreerd via de detectie modus of de modus voor preventie.

Alle logboeken worden elke 60 seconden verzameld. Zie voor meer informatie [back-end status, Diagnostische logboeken en metrische gegevens voor Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Hoe kan ik weet of de leden van de back-endadresgroep in orde zijn?

Controleer de status met behulp van de Power shell-cmdlet `Get-AzApplicationGatewayBackendHealth` of de portal. Zie [Application Gateway Diagnostics (diagnostische](application-gateway-diagnostics.md)gegevens) voor meer informatie.

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Wat is het Bewaar beleid voor de diagnostische logboeken?

Diagnostische logboeken stroomt naar het opslag account van de klant. Klanten kunnen het Bewaar beleid instellen op basis van hun voor keur. Diagnostische logboeken kunnen ook worden verzonden naar een Event Hub-of Azure Monitor-Logboeken. Zie [Application Gateway Diagnostics (diagnostische](application-gateway-diagnostics.md)gegevens) voor meer informatie.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Hoe kan ik audit logboeken voor Application Gateway ophalen?

Selecteer in de portal op de menu-Blade van een toepassings gateway het **activiteiten logboek** voor toegang tot het controle logboek. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Kan ik waarschuwingen instellen met Application Gateway?

Ja. In Application Gateway worden waarschuwingen voor metrische gegevens geconfigureerd. Zie [Application Gateway metrische gegevens](./application-gateway-metrics.md) en [waarschuwings meldingen ontvangen](../azure-monitor/alerts/alerts-overview.md)voor meer informatie.

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Hoe kan ik verkeers statistieken voor Application Gateway analyseren?

U kunt toegangs logboeken op verschillende manieren weer geven en analyseren. Gebruik Azure Monitor-logboeken, Excel, Power BI, enzovoort.

U kunt ook een resource manager-sjabloon gebruiken waarmee de populaire [GoAccess](https://goaccess.io/) log analyzer voor Application Gateway Access-Logboeken wordt geïnstalleerd en uitgevoerd. GoAccess biedt waardevolle statistieken voor HTTP-verkeer, zoals unieke bezoekers, aangevraagde bestanden, hosts, besturings systemen, browsers en HTTP-status codes. Voor meer informatie, in GitHub, raadpleegt u het [Leesmij-bestand in de map Resource Manager-sjabloon](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Wat kan ertoe leiden dat de status van de back-end wordt weer gegeven?

Normaal gesp roken ziet u een onbekende status wanneer de toegang tot de back-end wordt geblokkeerd door een netwerk beveiligings groep (NSG), aangepaste DNS of door de gebruiker gedefinieerde route ring (UDR) op het toepassings gateway-subnet. Zie voor meer informatie [status van back-end, diagnostische logboek registratie en metrische gegevens voor Application Gateway](application-gateway-diagnostics.md).

### <a name="are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet"></a>Worden NSG-stroom logboeken ondersteund op Nsg's die zijn gekoppeld aan Application Gateway v2-subnet?

Als gevolg van de beperkingen van het huidige platform, als u een NSG hebt op het Application Gateway v2-subnet (Standard_v2, WAF_v2) en als u NSG-stroom Logboeken hebt ingeschakeld, ziet u niet-deterministisch gedrag. dit scenario wordt momenteel niet ondersteund.

### <a name="where-does-application-gateway-store-customer-data"></a>Waar worden klant gegevens Application Gateway opgeslagen?

Met Application Gateway worden klant gegevens niet verplaatst of opgeslagen uit de regio waarin deze is geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure-toepassing gateway?](overview.md)voor meer informatie over Application Gateway.
