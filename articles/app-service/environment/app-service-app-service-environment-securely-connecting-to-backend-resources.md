---
title: Verbinding maken met back-end v1
description: Meer informatie over hoe u veilig verbinding maakt met de back-end-bronnen via een App Service Environment. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 9f8e288f771b9d584a0fd3430115f5fa60f68e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961802"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Veilig verbinding maken met back-end-bronnen vanuit een App Service omgeving
Omdat een App Service Environment altijd wordt **gemaakt in een** Azure Resource Manager virtueel netwerk **of** een klassiek implementatie model [virtueel netwerk][virtualnetwork], kunnen uitgaande verbindingen van een app service Environment naar andere back-upbronnen alleen stromen via het virtuele netwerk. Vanaf 2016 juni kan as ook worden geïmplementeerd in virtuele netwerken die gebruikmaken van open bare adresbereiken of RFC1918-adres ruimten (privé adressen).  

Er kan bijvoorbeeld een SQL Server worden uitgevoerd op een cluster met virtuele machines met poort 1433 vergrendeld.  Het eind punt kan worden ACLd om alleen toegang toe te staan vanuit andere bronnen in hetzelfde virtuele netwerk.  

Een ander voor beeld: gevoelige eind punten kunnen on-premises worden uitgevoerd en met Azure worden verbonden via [site-naar-site-][SiteToSite] of [Azure ExpressRoute][ExpressRoute] -verbindingen.  Als gevolg hiervan kunnen alleen bronnen in virtuele netwerken die zijn verbonden met de site-naar-site-of ExpressRoute-tunnels toegang krijgen tot on-premises eind punten.

Voor al deze scenario's kunnen apps die op een App Service Environment worden uitgevoerd, veilig verbinding maken met de verschillende servers en bronnen. Als uitgaand verkeer van apps wordt uitgevoerd in een App Service Environment naar privé-eind punten in hetzelfde virtuele netwerk of is verbonden met hetzelfde virtuele netwerk, loopt het alleen over het virtuele netwerk.  Uitgaand verkeer naar privé-eind punten loopt niet over op het open bare Internet.

Een probleem geldt voor uitgaand verkeer van een App Service Environment naar eind punten in een virtueel netwerk. App Service omgevingen kunnen geen eind punten bereiken van virtuele machines die zich in **hetzelfde** subnet bevinden als de app service environment. Deze beperking is doorgaans niet van toepassing als App Service omgevingen worden geïmplementeerd in een subnet dat uitsluitend door de App Service Environment is gereserveerd.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Vereisten voor uitgaande verbindingen en DNS
Voor een goede werking van App Service Environment is uitgaande toegang tot verschillende eind punten vereist. Een volledige lijst van de externe eind punten die worden gebruikt door een ASE vindt u in de sectie ' vereiste netwerk verbinding ' van het artikel [netwerk configuratie voor ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

App Service omgevingen vereisen een geldige DNS-infra structuur die is geconfigureerd voor het virtuele netwerk.  Als de DNS-configuratie wordt gewijzigd na het maken van een App Service Environment, kunnen ontwikkel aars een App Service Environment dwingen om de nieuwe DNS-configuratie op te halen. Selecteer boven aan de Blade App Service Environment-beheer in de portal het pictogram **opnieuw opstarten** om het opnieuw opstarten van de Rolling omgeving te activeren. Dit zorgt ervoor dat de omgeving de nieuwe DNS-configuratie kan ophalen.

Het is ook raadzaam dat alle aangepaste DNS-servers op het vnet vooraf worden ingesteld voordat een App Service Environment wordt gemaakt.  Als de DNS-configuratie van een virtueel netwerk wordt gewijzigd tijdens het maken van een App Service Environment, wordt het proces voor het maken van de App Service Environment mislukt. Als er aan het andere uiteinde van een VPN-gateway een aangepaste DNS-server is die onbereikbaar is of niet beschikbaar is, mislukt het proces voor het maken van App Service Environment ook.

## <a name="connecting-to-a-sql-server"></a>Verbinding maken met een SQL Server
Een algemene SQL Server configuratie heeft een eind punt dat luistert op poort 1433:

![SQL Server-eind punt][SqlServerEndpoint]

Er zijn twee benaderingen voor het beperken van verkeer naar dit eind punt:

* [Lijst met netwerk Access Control][NetworkAccessControlLists] (netwerk-acl's)
* [Netwerkbeveiligingsgroepen][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Toegang beperken met een netwerk toegangs beheer lijst
Poort 1433 kan worden beveiligd met behulp van een netwerk toegangs beheer lijst.  In het onderstaande voor beeld worden de toewijzings machtigingen voor de client adressen van binnen een virtueel netwerk toegevoegd en wordt de toegang tot alle andere clients geblokkeerd.

![Voor beeld van lijst met netwerk Access Control][NetworkAccessControlListExample]

Toepassingen die worden uitgevoerd in App Service Environment, in hetzelfde virtuele netwerk als de SQL Server, kunnen verbinding maken met het SQL Server exemplaar. Gebruik het **interne VNet** -IP-adres voor de virtuele machine van SQL Server.  

In het onderstaande voor beeld connection string wordt verwezen naar de SQL Server met behulp van het bijbehorende privé-IP-adres.

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

Hoewel de virtuele machine ook een openbaar eind punt heeft, worden Verbindings pogingen om het open bare IP-adres te gebruiken geweigerd vanwege de netwerk toegangs beheer lijst. 

## <a name="restricting-access-with-a-network-security-group"></a>Toegang beperken met een netwerk beveiligings groep
Een alternatieve methode voor het beveiligen van de toegang is met een netwerk beveiligings groep.  Netwerk beveiligings groepen kunnen worden toegepast op afzonderlijke virtuele machines of op een subnet met virtuele machines.

Eerst moet u een netwerk beveiligings groep maken:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Het beperken van toegang tot het interne VNet-verkeer is eenvoudig met een netwerk beveiligings groep.  De standaard regels in een netwerk beveiligings groep staan alleen toegang toe vanaf andere netwerkclients in hetzelfde virtuele netwerk.

Als gevolg hiervan is het vergren delen van toegang tot SQL Server eenvoudig. Pas een netwerk beveiligings groep met de standaard regels toe op de virtuele machines met SQL Server, of het subnet met de virtuele machines.

In het onderstaande voor beeld wordt een netwerk beveiligings groep toegepast op het subnet dat het bevat:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

Het uiteindelijke resultaat is een set beveiligings regels waarmee externe toegang wordt geblokkeerd, terwijl VNet interne toegang is toegestaan:

![Standaard regels voor netwerk beveiliging][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Aan de slag
Zie [Inleiding tot app service Environment][IntroToAppServiceEnvironment] om aan de slag te gaan met app service omgevingen

Zie voor meer informatie over het beheren van inkomend verkeer naar uw App Service Environment het [beheren van inkomend verkeer naar een app service Environment][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png