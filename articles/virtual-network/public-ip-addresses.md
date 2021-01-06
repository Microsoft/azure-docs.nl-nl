---
title: Openbare IP-adressen in Azure
titlesuffix: Azure Virtual Network
description: Meer informatie over openbare IP-adressen in Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 1e46cf78c76e873bcb78af4942f42a5c4be45391
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955583"
---
# <a name="public-ip-addresses"></a>Openbare IP-adressen

Door openbare IP-adressen te gebruiken, zijn internetbronnen in staat inkomende communicatie voor Azure-resources te verwerken. Met open bare IP-adressen kunnen Azure-resources communiceren met internet en open bare Azure-Services. Het adres is toegewezen aan de resource totdat het door u is toegewezen. Een resource zonder een openbaar IP-adres dat is toegewezen, kan uitgaand communiceren. Azure wijst dynamisch een beschikbaar IP-adres toe dat niet is toegewezen aan de resource. Zie [Uitleg over uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over uitgaande verbindingen in Azure.

In Azure Resource Manager is een [openbaar IP](virtual-network-public-ip-address.md)-adres een resource die zijn eigen eigenschappen heeft. Enkele van de resources waarmee u een open bare IP-adres resource kunt koppelen:

* Netwerkinterfaces van virtuele machines
* Internetgerichte load balancers
* VPN-gateways
* Toepassingsgateways
* Azure Firewall

## <a name="ip-address-version"></a>IP-adresversie

Openbare IP-adressen worden gemaakt met een IPv4- of IPv6-adres. 

## <a name="sku"></a>SKU

Raadpleeg voor meer informatie over de SKU-upgrade de [open bare IP-upgrade](../virtual-network/virtual-network-public-ip-address-upgrade.md).

Openbare IP-adressen worden gemaakt met een van de volgende SKU's:

>[!IMPORTANT]
> Er zijn overeenkomende Sku's vereist voor load balancer en open bare IP-resources. Het is niet mogelijk om een combinatie van resources uit de Basic-SKU en Standard-SKU te gebruiken. Het is evenmin mogelijk om zelfstandige virtuele machines, virtuele machines in een resource van een beschikbaarheidsset of resources uit schaalset met virtuele machines op beide SKU's tegelijk in te stellen.  In nieuwe ontwerpen is het raadzaam om resources uit de Standard-SKU te gebruiken.  Raadpleeg [Overzicht van load balancer uit Standard-SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie.

### <a name="standard"></a>Standard

Standaard-SKU open bare IP-adressen:

- Gebruiken altijd de statische toewijzingsmethode.
- Hebben een aanpasbare time-out voor inactiviteit van de stroom met inkomende gegevens van 4-30 minuten (de standaardwaarde is vier minuten), en een vaste time-out voor inactiviteit van de stroom met uitgaande gegevens van vier minuten.
- Standaard beveiligd en gesloten voor binnenkomend verkeer. Lijst met binnenkomend verkeer met een [netwerk beveiligings groep](security-overview.md#network-security-groups)toestaan.
- Toegewezen aan netwerk interfaces, standaard open bare load balancers of toepassings gateways. Zie voor meer informatie over standaard load balancer [Azure Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Kan zone-redundante (op te maken van alle drie zones) of zonegebonden (kan worden gemaakt zonegebonden en worden gegarandeerd in een specifieke beschikbaarheids zone). Zie [Overzicht van beschikbaarheidszones in Azure](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en [Standard-load balancer en beschikbaarheidszones](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over beschikbaarheidszones. **Redundante Ip's voor zones kunnen alleen worden gemaakt in [regio's waar drie beschikbaarheids zones](https://docs.microsoft.com/azure/availability-zones/az-region) Live zijn.** IP-adressen die zijn gemaakt voordat zones Live zijn, zijn niet-redundante zones.
- Kan worden gebruikt als anycast frontend-Ip's voor [meerdere regio's load balancers](https://docs.microsoft.com/azure/load-balancer/cross-region-overview) (Preview-functionaliteit).
 
> [!NOTE]
> Inkomende communicatie met een resource uit de Standard-SKU mislukt totdat u een [netwerkbeveiligingsgroep](security-overview.md#network-security-groups) maakt en koppelt en het gewenste binnenkomende verkeer expliciet toestaat.

> [!NOTE]
> Alleen open bare IP-adressen met de basis-SKU zijn beschikbaar wanneer IMDS wordt gebruikt voor het gebruiken van [meta gegevens service](../virtual-machines/windows/instance-metadata-service.md). De standaard-SKU wordt niet ondersteund.

### <a name="basic"></a>Basic

Alle openbare IP-adressen die zijn gemaakt vóór de introductie van SKU's zijn openbare IP-adressen van de basis-SKU. 

Geef bij de introductie van Sku's op welke SKU u het open bare IP-adres wilt maken. 

Basis-SKU-adressen:

- Worden toegewezen met de statische of dynamische toewijzingsmethode.
- Hebben een aanpasbare time-out voor inactiviteit van de stroom met inkomende gegevens van 4-30 minuten (de standaardwaarde is vier minuten), en een vaste time-out voor inactiviteit van de stroom met uitgaande gegevens van vier minuten.
- Zijn standaard geopend.  Netwerkbeveiligingsgroepen worden aanbevolen, maar zijn optioneel voor het beperken van binnenkomend of uitgaand verkeer.
- Toegewezen aan een Azure-resource waaraan een openbaar IP-adres kan worden toegewezen, zoals:
    * Netwerkinterfaces
    * VPN-gateways
    * Toepassingsgateways
    * Open bare load balancers
- Geen ondersteuning voor beschikbaarheids zone-scenario's. Gebruik de standaard SKU open bare IP voor de beschikbaarheids zone. Zie [Overzicht van beschikbaarheidszones in Azure](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en [Standard-load balancer en beschikbaarheidszones](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over beschikbaarheidszones.

## <a name="allocation-method"></a>Toewijzingsmethode

Basis-en standaard open bare Ip's ondersteunen **statische** toewijzing.  Er wordt een IP-adres toegewezen aan de resource op het moment dat deze wordt gemaakt. Het IP-adres wordt vrijgegeven wanneer de resource wordt verwijderd.

Basis-SKU open bare IP-adressen ondersteunen een **dynamische** toewijzing. Dynamische is de standaard toewijzings methode. Het IP-adres **wordt niet** aan de resource toegewezen op het moment dat u dynamisch selecteert.

Het IP-adres wordt toegewezen wanneer u de open bare IP-adressen koppelt aan een:

* Virtuele machine 
* De eerste virtuele machine is gekoppeld aan de back-end-groep van een load balancer.

Het IP-adres wordt weer vrijgegeven wanneer u de resource stopt (of verwijdert).  

Een open bare IP-resource wordt bijvoorbeeld vrijgegeven uit een resource met de naam **resource a**. **Resource A** ontvangt een ander IP-adres bij het opstarten als de open bare IP-resource opnieuw wordt toegewezen.

Het IP-adres wordt vrijgegeven wanneer de toewijzings methode wordt gewijzigd van **statisch** naar **dynamisch**. Om ervoor te zorgen dat het IP-adres voor de gekoppelde resource hetzelfde blijft, stelt u de toewijzings methode expliciet in op **statisch**. Een statisch IP-adres wordt onmiddellijk toegewezen.

> [!NOTE]
> Ook als u de toewijzingsmethode instelt op **statisch**, kunt u het IP-adres dat aan de openbare IP-adresresource wordt toegewezen, echter niet zelf opgeven. Azure wijst het IP-adres toe vanuit een pool van beschikbare IP-adressen op de Azure-locatie waarin de resource is gemaakt.
>

Statische openbare IP-adressen worden vaak gebruikt in de volgende scenario's:

* U moet firewallregels bijwerken om te communiceren met uw Azure-resources.
* U gebruikt een DNS-naamomzetting waarbij een wijziging in het IP-adres het bijwerken van A-records vereist.
* Uw Azure-resources communiceren met andere apps of services die een op IP-adressen gebaseerd beveiligingsmodel gebruiken.
* U gebruikt TLS/SSL-certificaten die zijn gekoppeld aan een IP-adres.

> [!NOTE]
> In Azure worden openbare IP-adressen toegewezen uit een bereik dat uniek is voor elke regio in elke Azure-cloud. U kunt de lijst met bereiken (voorvoegsels) downloaden voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).
>

## <a name="dns-hostname-resolution"></a>DNS-hostnaamomzetting

Selecteer de optie om een DNS-domein naam label op te geven voor een open bare IP-resource. 

Met deze selectie wordt een toewijzing gemaakt voor **domeinnaam label**. **Location**. cloudapp.Azure.com naar het open bare IP-adres in de door Azure beheerde DNS. 

Bijvoorbeeld het maken van een openbaar IP-adres met:

* **Contoso** als een **domeinnaam label**
* **VS-West** Azure- **locatie**

De Fully Qualified Domain Name (FQDN) **contoso.westus.cloudapp.Azure.com** wordt omgezet in het open bare IP-adres van de resource.

> [!IMPORTANT]
> Elk domeinnaamlabel dat wordt gemaakt, moet uniek zijn binnen de Azure-locatie.  
>

## <a name="dns-recommendations"></a>Aanbevelingen voor DNS

Als er een regio verplaatsing nodig is, kunt u de FQDN-namen van uw open bare IP-adres niet migreren. Gebruik de FQDN voor het maken van een aangepaste CNAME-record die verwijst naar het open bare IP-adres. 

Als een verplaatsing naar een ander openbaar IP-adres vereist is, werkt u de CNAME-record bij in plaats van de FQDN-gegevens bij te werken.

U kunt [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) of een externe DNS-provider voor uw DNS-record gebruiken. 

## <a name="virtual-machines"></a>Virtuele machines

U kunt een openbaar IP-adres koppelen aan een virtuele [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- of [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-machine door het toe te wijzen aan de **netwerkinterface**. 

Kies **dynamisch** of **statisch** voor het open bare IP-adres. Meer informatie over [IP-adressen toewijzen aan netwerkinterfaces](virtual-network-network-interface-addresses.md).

## <a name="internet-facing-load-balancers"></a>Internetgerichte load balancers

U kunt een openbaar IP-adres van beide [sku's](#sku) koppelen met een [Azure Load Balancer](../load-balancer/load-balancer-overview.md)door het toe te wijzen aan de Load Balancer front- **End** -configuratie. Het open bare IP fungeert als een IP-adres met taak verdeling. 

U kunt een dynamisch of statisch openbaar IP-adres toewijzen aan de front-end van een load balancer. U kunt meerdere open bare IP-adressen toewijzen aan een load balancer front-end. Deze configuratie maakt [multi-VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -scenario's mogelijk, zoals een omgeving met meerdere tenants met op TLS gebaseerde websites. 

Zie [Standaard-SKU's van Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over SKU's van Azure Load Balancer.

## <a name="vpn-gateways"></a>VPN-gateways

[Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) verbindt een virtueel Azure-netwerk met:

* Virtuele netwerken van Azure.
* On-premises netwerk (en). 

Er wordt een openbaar IP-adres toegewezen aan de VPN Gateway om communicatie met het externe netwerk mogelijk te maken. U kunt alleen een *dynamisch* openbaar IP-adres uit de Basic-SKU toewijzen aan een VPN-gateway.

## <a name="application-gateways"></a>Toepassingsgateways

U kunt een openbaar IP-adres koppelen aan een Azure-[toepassingsgateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) door het toe te wijzen aan de **front-end**-configuratie van de gateway. 

* Wijs een **dynamisch** , algemeen openbaar IP-adres toe aan een Application Gateway v1-front-end-configuratie. 
* Wijs een **statisch** standaard-SKU-adres toe aan een v2-front-end-configuratie.

## <a name="azure-firewall"></a>Azure Firewall

Met [Azure firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) kunt u toepassingen en beleids regels voor netwerk verbindingen maken, afdwingen en registreren in abonnementen en virtuele netwerken.

U kunt alleen **statische** standaard open bare IP-adressen koppelen aan een firewall. Hierdoor kunnen externe firewalls het verkeer identificeren dat afkomstig is van het virtuele netwerk. 


## <a name="at-a-glance"></a>In een oogopslag

In de volgende tabel ziet u de eigenschap waarmee een openbaar IP-adres kan worden gekoppeld aan een resource op het hoogste niveau en de mogelijke toewijzings methoden.

| Resource op het hoogste niveau | IP-adreskoppeling | Dynamisch | Statisch |
| --- | --- | --- | --- |
| Virtuele machine |Netwerkinterface |Ja |Ja |
| Internetgerichte load balancer |Front-end-configuratie |Ja |Ja |
| VPN-gateway |Gateway-IP-configuratie |Ja |Nee |
| Toepassingsgateway |Front-end-configuratie |Ja (alleen V1) |Ja (alleen V2) |
| Azure Firewall | Front-end-configuratie | Nee | Ja|

## <a name="limits"></a>Limieten

De limieten voor IP-adres Sering worden weer gegeven in de volledige set [limieten voor netwerken](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) in Azure. 

De limieten gelden per regio en per abonnement. [Neem contact](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) op met ondersteuning om de standaard limieten te verhogen tot de maximum limieten op basis van uw bedrijfs behoeften.

## <a name="pricing"></a>Prijzen

Openbare IP-adressen kunnen een kostprijs hebben. Voor meer informatie over prijzen voor IP-adressen in Azure raadpleegt u de pagina [Prijzen van IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [privé-IP-adressen in azure](private-ip-addresses.md)
* [Een virtuele machine met een statisch openbaar IP-adres implementeren via Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)

