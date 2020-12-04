---
title: 'Zelfstudie: checklist voor netwerkplanning'
description: Lees hier alles over de netwerkvereisten en informatie over netwerkconnectiviteit en netwerkpoorten voor Azure VMware Solution.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 52f973ada23576fd6d542c40c9a9e63e6f270df3
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575156"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Checklist voor netwerkplanning voor Azure VMware Solution 

De Azure VMware-oplossing biedt een VMware-privécloud, die toegankelijk is voor gebruikers en toepassingen van on-premises en Azure-omgevingen of -resources. De connectiviteit wordt geleverd via netwerkservices zoals Azure ExpressRoute en VPN-verbindingen, en vereist een aantal specifieke netwerkadresbereiken en firewallpoorten voor het inschakelen van de services. In dit artikel vindt u de informatie die u nodig hebt om uw netwerk correct te configureren voor gebruik met Azure VMware Solution.

In deze zelfstudie wordt aandacht besteed aan:

> [!div class="checklist"]
> * Overwegingen voor virtuele netwerken en ExpressRoute-circuits
> * Vereisten voor routering en subnet
> * Vereiste netwerkpoorten voor communicatie met de services
> * DHCP- en DNS-overwegingen in Azure VMware Solution

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>Overwegingen voor virtuele netwerken en ExpressRoute-circuits
Wanneer u een verbinding voor een virtueel netwerk maakt in uw abonnement, wordt het ExpressRoute-circuit tot stand gebracht via peering, een autorisatiesleutel en een peering-ID die u in de Azure-portal kunt aanvragen. De peering is een particuliere, een-op-een-verbinding tussen uw privécloud en het virtuele netwerk.

> [!NOTE] 
> Het ExpressRoute-circuit maakt geen deel uit van een implementatie van een privécloud. Het on-premises ExpressRoute-circuit valt buiten het bereik van dit document. Als u een on-premises verbinding met uw privécloud nodig hebt, kunt u een van uw bestaande ExpressRoute-circuits gebruiken of deze in de Azure-portal aanschaffen.

Wanneer u een privécloud implementeert, ontvangt u IP-adressen voor vCenter en NSX-T-beheer. Voor toegang tot deze beheerinterfaces moet u extra resources maken in het virtuele netwerk van uw abonnement. U vindt de procedures voor het maken van deze resources en het tot stand brengen van [persoonlijke peering met ExpressRoute](tutorial-expressroute-global-reach-private-cloud.md) in de zelfstudies.

Het logisch netwerk van de privécloud wordt geleverd met vooraf ingerichte NSX-T. Een Tier-0-gateway en een Tier-1-gateway is vooraf ingericht voor u. U kunt een segment maken en dit koppelen aan de bestaande gateway van de Tier-1 of koppelen aan een nieuwe door u opgegeven tier-1-gateway. NSX-T-onderdelen voor logische netwerken bieden oost-west-connectiviteit tussen workloads en bieden een noord-zuid-verbinding met het internet en Azure-services.

## <a name="routing-and-subnet-considerations"></a>Overwegingen voor routering en subnetten
De AVS-privécloud is verbonden met uw virtuele Azure-netwerk met behulp van een Azure ExpressRoute-verbinding. Via deze verbinding met hoge bandbreedte en lage latentie kunt u toegang krijgen tot services die worden uitgevoerd in uw Azure-abonnement vanuit uw privécloud. De routering is op basis van Border Gateway Protocol (BGP) en automatisch ingericht en ingeschakeld voor elke implementatie van een privécloud. 

Voor AVS-privéclouds is minimaal een `/22` CIDR-netwerkadresblok vereist voor subnetten, zoals hieronder wordt weergegeven. Dit netwerk vormt een aanvulling op uw on-premises netwerken. Het adresblok mag geen overlap vertonen met de in andere virtuele netwerken gebruikte adresblokken die zich in uw abonnement en on-premises netwerken bevinden. Beheer, inrichting en vMotion-netwerken worden automatisch ingericht in dit adresblok.

>[!NOTE]
>Toegestane bereiken voor uw adresblok zijn de RFC 1918-privéadresruimten (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), met uitzondering van 172.17.0.0/16.

Voorbeeld `/22` CIDR-netwerkadresblok:  `10.10.0.0/22`

De subnetten:

| Netwerkgebruik             | Subnet | Voorbeeld          |
| ------------------------- | ------ | ---------------- |
| Privécloudbeheer  | `/26`  | `10.10.0.0/26`   |
| Migraties van HCX Mgmt       | `/26`  | `10.10.0.64/26`  |
| Global Reach Reserved     | `/26`  | `10.10.0.128/26` |
| ExpressRoute Reserved     | `/27`  | `10.10.0.192/27` |
| ExpressRoute-peering      | `/27`  | `10.10.0.224/27` |
| ESXi-beheer           | `/25`  | `10.10.1.0/25`   |
| vMotion-network           | `/25`  | `10.10.1.128/25` |
| Replicatienetwerk       | `/25`  | `10.10.2.0/25`   |
| vSAN                      | `/25`  | `10.10.2.128/25` |
| HCX Uplink                | `/26`  | `10.10.3.0/26`   |
| Gereserveerd                  | `/26`  | `10.10.3.64/26`  |
| Gereserveerd                  | `/26`  | `10.10.3.128/26` |
| Gereserveerd                  | `/26`  | `10.10.3.192/26` |



## <a name="required-network-ports"></a>Vereiste netwerkpoorten

| Bron | Doel | Protocol | Poort | Beschrijving  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| DNS-servers voor privécloud | On-premises DNS-servers | UDP | 53 | DNS-client: aanvragen doorsturen van PC vCenter voor alle on-premises DNS-query's (zie de sectie DNS hieronder) |  
| On-premises DNS-servers   | DNS-servers voor privécloud | UDP | 53 | DNS-client: aanvragen doorsturen van on-premises services naar DNS-servers privéclouds (zie de sectie DNS hieronder) |  
| On-premises netwerk  | VCenter-server voor privécloud  | TCP (HTTP)  | 80 | Voor vCenter Server is poort 80 vereist voor directe HTTP-verbindingen. Poort 80 stuurt aanvragen naar HTTPS-poort 443. Deze omleiding helpt u bij het gebruik van `http://server` in plaats van `https://server`.  <br><br>WS-Management (hiervoor moet poort 443 zijn geopend) <br><br>Als u een aangepaste Microsoft-SQL database gebruikt en niet de gebundelde SQL Server 2008-database op de vCenter Server, wordt poort 80 door de SQL Reporting Services gebruikt. Wanneer u vCenter Server installeert, wordt u door het installatieprogramma gevraagd de HTTP-poort voor vCenter Server te wijzigen. Wijzig de vCenter Server HTTP-poort in een aangepaste waarde voor een succesvolle installatie. Microsoft IIS (Internet Information Services) gebruikt ook poort 80. Zie Conflict tussen vCenter Server en IIS voor poort 80. |  
| Netwerk voor privécloudbeheer | On-premises Active Directory  | TCP  | 389 | Deze poort moet geopend zijn op de lokale en externe exemplaren van vCenter Server. Deze poort is het LDAP-poortnummer voor de Directory Services voor de vCenter Server-groep. Het vCenter Server-systeem moet verbinding maken met poort 389, zelfs als u dit vCenter Server-exemplaar niet koppelt aan een Linked Mode-groep. Als er een andere service wordt uitgevoerd op deze poort, kan het raadzaam zijn om deze te verwijderen of om de poort te wijzigen in een andere poort. U kunt de LDAP-service uitvoeren op elke poort van 1025 tot en met 65535.  Als dit exemplaar als Microsoft Windows Active Directory wordt gebruikt, wijzigt u het poortnummer van 389 naar een beschikbare poort van 1025 tot en met 65535. Deze poort is optioneel voor het configureren van on-premises AD als een identiteitsbron op de vCenter van de privécloud. |  
| On-premises netwerk  | VCenter-server voor privécloud  | TCP (HTTPS)  | 443 | Met deze poort krijgt u toegang tot vCenter vanuit een on-premises netwerk. De standaard poort die het vCenter Server-systeem gebruikt om naar verbindingen van de vSphere-client te luisteren. Open poort 443 in de firewall om het vCenter Server-systeem in staat te stellen gegevens van de vSphere-client te ontvangen. Het vCenter Server-systeem gebruikt poort 443 ook voor het bewaken van gegevensoverdracht van SDK-clients. Deze poort wordt ook gebruikt voor de volgende services: WS-Management (hiervoor moet poort 80 geopend zijn). vSphere-clienttoegang tot vSphere-updatebeheer. Clientverbindingen voor netwerkbeheer van derden met vCenter Server. Clienttoegang tot hosts voor netwerkbeheer van derden. |  
| Webbrowser  | Hybrid Cloud Manager  | TCP (HTTPS) | 9443 | De beheerinterface voor virtuele apparaten van Hybrid Cloud Manager voor de Hybrid Cloud Manager-systeemconfiguratie. |
| Beheerdersnetwerk  | Hybrid Cloud Manager | SSH | 22 | SSH-beheerderstoegang tot Hybrid Cloud Manager. |
| HCM | Cloudgateway | TCP (HTTPS) | 8123 | Verzend instructies voor de replicatieservice op basis van de host naar de Hybrid Cloud-gateway. |
| HCM | Cloudgateway | HTTP TCP (HTTPS) | 9443 | Verzend beheerinstructies naar de lokale Hybrid Cloud-gateway met behulp van de REST API. |
| Cloudgateway | L2C | TCP (HTTPS) | 443 | Verzend beheerinstructies van de cloudgateway naar L2C wanneer L2C hetzelfde pad gebruikt als de Hybrid Cloud-gateway. |
| Cloudgateway | ESXi-hosts | TCP | 80.902 | Beheer en OVF-implementatie. |
| Cloudgateway (lokaal)| Cloudgateway (extern) | UDP | 4500 | Vereist voor IPSEC<br>   IKEv2 (Internet Key Exchange) voor het inkapselen van workloads voor de bidirectionele tunnel. Network Address Translation-Traversal (NAT-T) wordt ook ondersteund. |
| Cloudgateway (lokaal) | Cloudgateway (extern)  | UDP | 500 | Vereist voor IPSEC<br> ISAKMP (Internet Key Exchange) voor de bidirectionele tunnel. |
| On-premises vCenter-netwerk | Netwerk voor privécloudbeheer | TCP | 8000 |  vMotion of VMs van on-premises vCenter naar privécloud-vCenter   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>Overwegingen voor DHCP- en DNS-omzetting
Toepassingen en workloads die worden uitgevoerd in een privécloud-omgeving, moeten naamomzetting en DHCP-services hebben voor het opzoeken en toewijzen van IP-adressen. U hebt de juiste DHCP- en DNS-infrastructuur nodig om deze services te kunnen leveren. U kunt een virtuele machine configureren om deze services in uw privécloud te leveren.  

Gebruik de DHCP-service die is ingebouwd in NSX of een lokale DHCP-server in de privécloud in plaats van broadcast-DHCP-verkeer via het WAN terug naar on-premises te leiden.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u gelezen over de overwegingen en vereisten voor het implementeren van een privécloud van Azure VMware Solution. 


Zodra u de juiste netwerken hebt ingesteld, gaat u verder met de volgende zelfstudie om uw Azure VMware Solution-privécloud te maken.

> [!div class="nextstepaction"]
> [Een Azure VMware Solution-privécloud maken](tutorial-create-private-cloud.md)
