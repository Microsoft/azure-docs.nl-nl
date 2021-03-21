---
title: Peering-problemen van virtueel netwerk oplossen
description: Stappen om de meeste problemen met peering van het virtuele netwerk op te lossen.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 608f3d72aecb0c6d53325c0d082b1779822e9c80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200529"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Peering-problemen van virtueel netwerk oplossen

Deze hand leiding bevat stappen die u helpen bij het oplossen van de meeste problemen met [peering in het virtuele netwerk](virtual-network-peering-overview.md) .

![Diagram van peering van virtuele netwerken](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Virtuele netwerk peering tussen twee virtuele netwerken configureren

Bevinden de virtuele netwerken zich in hetzelfde abonnement of in verschillende abonnementen?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>De virtuele netwerken bevinden zich in hetzelfde abonnement

Gebruik de methoden in de volgende artikelen om de peering van het virtuele netwerk te configureren voor de virtuele netwerken die zich in hetzelfde abonnement bevinden:

* Zie [een peering maken](./virtual-network-manage-peering.md#create-a-peering)als de virtuele netwerken zich in *dezelfde regio* bevinden.
* Zie [virtuele netwerk peering](./virtual-network-peering-overview.md)als de virtuele netwerken zich in de *verschillende regio's* bevinden. 

> [!Note]
> De connectiviteit werkt niet via globale peering van het virtuele netwerk voor de volgende resources: 
>
> * Virtuele machines (Vm's) achter de ILB-SKU (Basic Internal load balancer)
> * Redis-cache (maakt gebruik van Basic ILB SKU)
> * Application Gateway v1 (maakt gebruik van Basic ILB SKU)
> * Schaal sets voor virtuele machines (maakt gebruik van Basic ILB SKU)
> * Azure Service Fabric-clusters (maakt gebruik van Basic ILB SKU)
> * SQL Server always on (maakt gebruik van Basic ILB SKU)
> * Azure App Service Environment voor PowerApps (maakt gebruik van Basic ILB SKU)
> * Azure API Management (maakt gebruik van Basic ILB SKU)
> * Azure Active Directory Domain Services (Azure AD DS) (maakt gebruik van Basic ILB SKU)

Zie de [vereisten en beperkingen](./virtual-network-peering-overview.md#requirements-and-constraints) van wereld wijde peering voor meer informatie.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>De virtuele netwerken bevinden zich in verschillende abonnementen of Active Directory tenants

Zie [peering maken in verschillende abonnementen voor Azure cli](./create-peering-different-subscriptions.md#cli)voor het configureren van peering voor virtuele netwerken in verschillende abonnementen of Active Directory tenants.

> [!Note]
> Als u Network-peering wilt configureren, moet u in beide abonnementen over de machtiging **netwerk Inzender** beschikken. Zie [machtigingen voor peering](virtual-network-manage-peering.md#permissions)voor meer informatie.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Configureer peering voor het virtuele netwerk met hub-spoke-topologie die gebruikmaakt van on-premises resources

![Diagram van peering van virtuele netwerken met on-premises Spaak](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Voor een site-naar-site-verbinding of een ExpressRoute-verbinding

Volg de stappen in: [Configure VPN gateway Transit voor peering van virtuele netwerken](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="for-point-to-site-connections"></a>Voor punt-naar-site-verbindingen

1. Volg de stappen in: [Configure VPN gateway Transit voor peering van virtuele netwerken](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Nadat de peering van het virtuele netwerk is vastgesteld of gewijzigd, downloadt en installeert u het punt-naar-site-pakket zodat de punt-naar-site-clients de bijgewerkte routes naar het spoke-virtuele netwerk ophalen.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Peering voor het virtuele netwerk configureren met het virtuele netwerk hub-spoke-topologie

![Diagram van peering van het virtuele netwerk met een virtueel netwerk Spaak](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>De virtuele netwerken bevinden zich in dezelfde regio


1. Configureer een virtueel netwerk apparaat (NVA) in het virtuele netwerk van de hub.
1. In de spoke-virtuele netwerken hebben door de gebruiker gedefinieerde routes met het volgende type hop ' netwerk virtueel apparaat ' toegepast.

Zie [service Chaining](./virtual-network-peering-overview.md#service-chaining)voor meer informatie.

> [!Note]
> Als u hulp nodig hebt bij het instellen van een NVA, [neemt u contact op met de leverancier van NVA](https://mskb.pkisolutions.com/kb/2984655).

Zie [problemen met virtuele netwerk apparaten in azure](./virtual-network-troubleshoot-nva.md)voor hulp bij het oplossen van problemen met de installatie en route ring van het NVA-apparaat.

### <a name="the-virtual-networks-are-in-different-regions"></a>De virtuele netwerken bevinden zich in verschillende regio's

Transit via globale Virtual Network-peering wordt nu ondersteund. De connectiviteit werkt niet via globale peering van het virtuele netwerk voor de volgende resources:

* VM's achter Basic ILB SKU
* Redis-cache (maakt gebruik van Basic ILB SKU)
* Application Gateway (maakt gebruik van Basic ILB SKU)
* Schaal sets (maakt gebruik van Basic ILB SKU)
* Service Fabric-clusters (maakt gebruik van Basic ILB SKU)
* SQL Server always on (maakt gebruik van Basic ILB SKU)
* App Service Environment (maakt gebruik van Basic ILB SKU)
* API Management (maakt gebruik van Basic ILB SKU)
* Azure AD DS (maakt gebruik van Basic ILB SKU)

Zie [Virtual Network-peering](./virtual-network-peering-overview.md#requirements-and-constraints)voor meer informatie over algemene vereisten en beperkingen voor peering.

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Problemen met een connectiviteits probleem tussen twee gekoppelde virtuele netwerken oplossen

Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een account met de benodigde [rollen en machtigingen](virtual-network-manage-peering.md#permissions). Selecteer het virtuele netwerk, selecteer **peering** en controleer vervolgens het veld **status** . Wat is de status?

### <a name="the-peering-status-is-connected"></a>De peering-status is verbonden

U kunt dit probleem als volgt oplossen:

1. Controleer het netwerk verkeer:

   Gebruik [verbindings problemen oplossen](../network-watcher/network-watcher-connectivity-overview.md) en [IP-stroom controleren](../network-watcher/network-watcher-ip-flow-verify-overview.md) van de bron-VM naar de doel-VM om te bepalen of er sprake is van een NSG of UDR dat storingen veroorzaakt in verkeers stromen.

   Als u een firewall of NVA gebruikt: 
   1. Documenteer de UDR-para meters, zodat u ze kunt herstellen nadat deze stap is voltooid.
   2. Verwijder de UDR uit het subnet van de bron-VM of de NIC die verwijst naar de NVA als de volgende hop. Controleer de connectiviteit van de bron-VM rechtstreeks naar de bestemming die de NVA omzeilt. Als deze stap niet werkt, raadpleegt u de [probleem oplosser voor NVA](./virtual-network-troubleshoot-nva.md).

2. Een netwerk tracering uitvoeren: 
   1. Start een netwerk tracering op de doel-VM. Voor Windows kunt u **netsh** gebruiken. Gebruik **TCPDump** voor Linux.
   2. Voer **TcpPing** of **PsPing** uit van de bron naar het doel-IP.

      Dit is een voor beeld van een **TcpPing** -opdracht: `tcping64.exe -t <destination VM address> 3389`

   3. Stop de netwerk tracering op de bestemming nadat de **TcpPing** is voltooid.
   4. Als pakketten van de bron arriveren, is er geen netwerk probleem. Controleer zowel de VM-firewall als de toepassing die op die poort luistert om het configuratie probleem te vinden.

   > [!Note]
   > U kunt geen verbinding maken met de volgende bron typen via globale virtuele netwerk peering (virtuele netwerken in verschillende regio's):
   >
   > * VM's achter Basic ILB SKU
   > * Redis-cache (maakt gebruik van Basic ILB SKU)
   > * Application Gateway (maakt gebruik van Basic ILB SKU)
   > * Schaal sets (maakt gebruik van Basic ILB SKU)
   > * Service Fabric-clusters (maakt gebruik van Basic ILB SKU)
   > * SQL Server always on (maakt gebruik van Basic ILB SKU)
   > * App Service Environment (maakt gebruik van Basic ILB SKU)
   > * API Management (maakt gebruik van Basic ILB SKU)
   > * Azure AD DS (maakt gebruik van Basic ILB SKU)

Zie de [vereisten en beperkingen](./virtual-network-peering-overview.md#requirements-and-constraints) van wereld wijde peering voor meer informatie.

### <a name="the-peering-status-is-disconnected"></a>De peering-status is niet verbonden

U kunt dit probleem oplossen door de peering uit beide virtuele netwerken te verwijderen en ze vervolgens opnieuw te maken.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Problemen met de connectiviteit tussen een virtueel netwerk met hub-spoke en een on-premises resource oplossen

Maakt uw netwerk gebruik van een NVA of VPN-gateway van derden?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Mijn netwerk maakt gebruik van een NVA-of VPN-gateway van derden

Raadpleeg de volgende artikelen om verbindings problemen op te lossen die van invloed zijn op een NVA of VPN-gateway van derden:

* [NVA-probleem Oplosser](./virtual-network-troubleshoot-nva.md)
* [Servicechaining](./virtual-network-peering-overview.md#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Mijn netwerk maakt geen gebruik van een NVA-of VPN-gateway van derden

Hebben het virtuele hub-netwerk en het virtuele spoke-netwerk een VPN-gateway?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Zowel het virtuele hub-netwerk als het virtuele spoke-netwerk hebben een VPN-gateway

Het gebruik van een externe gateway wordt niet ondersteund.

Als het virtuele spoke-netwerk al een VPN-gateway heeft, wordt de optie **externe gateway gebruiken** niet ondersteund op het virtuele spoke-netwerk. Dit komt door een beperking van de peering van het virtuele netwerk.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Zowel het virtuele hub-netwerk als het virtuele spoke-netwerk hebben geen VPN-gateway

Voor site-naar-site-of Azure ExpressRoute-verbindingen raadpleegt u de volgende primaire oorzaken van connectiviteits problemen met het externe virtuele netwerk van on-premises:

* Controleer op het virtuele netwerk met een gateway of het selectie vakje **doorgestuurd verkeer toestaan** is ingeschakeld.
* Controleer op het virtuele netwerk dat geen gateway heeft, of het selectie vakje **externe gateway gebruiken** is ingeschakeld.
* Zorg ervoor dat uw netwerk beheerder uw on-premises apparaten controleert om te controleren of de externe adres ruimte van het virtuele netwerk is toegevoegd.

Voor punt-naar-site-verbindingen:

* Controleer op het virtuele netwerk met een gateway of het selectie vakje **doorgestuurd verkeer toestaan** is ingeschakeld.
* Controleer op het virtuele netwerk dat geen gateway heeft, of het selectie vakje **externe gateway gebruiken** is ingeschakeld.
* Down load en installeer het punt-naar-site-client pakket opnieuw. Routes van virtuele netwerken die nieuw zijn, worden niet automatisch toegevoegd aan punt-naar-site-clients.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Problemen met een hub-spoke-netwerk verbinding tussen spoke-virtuele netwerken in dezelfde regio oplossen

Een hub-netwerk moet een NVA bevatten. Configureer Udr's in spokes die een NVA hebben ingesteld als de volgende hop en Schakel **doorgestuurd verkeer toestaan** in het virtuele hub-netwerk in.

Zie [service Chaining](./virtual-network-peering-overview.md#service-chaining)voor meer informatie en bespreek deze vereisten met de [NVA leverancier](https://mskb.pkisolutions.com/kb/2984655) van uw keuze.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Problemen met een hub-spoke-netwerk verbinding tussen spoke-virtuele netwerken in verschillende regio's oplossen

Transit via globale Virtual Network-peering wordt nu ondersteund. De connectiviteit werkt niet via globale peering van het virtuele netwerk voor de volgende resources:

* VM's achter Basic ILB SKU
* Redis-cache (maakt gebruik van Basic ILB SKU)
* Application Gateway (maakt gebruik van Basic ILB SKU)
* Schaal sets (maakt gebruik van Basic ILB SKU)
* Service Fabric-clusters (maakt gebruik van Basic ILB SKU)
* SQL Server always on (maakt gebruik van Basic ILB SKU)
* App Service Environment (maakt gebruik van Basic ILB SKU)
* API Management (maakt gebruik van Basic ILB SKU)
* Azure AD DS (maakt gebruik van Basic ILB SKU)

Zie voor meer informatie de [vereisten en beperkingen](./virtual-network-peering-overview.md#requirements-and-constraints) van globale peering en [verschillende VPN-topologieën](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Problemen met een hub-spoke-netwerk verbinding tussen een web-app en het virtuele spoke-netwerk oplossen

U kunt dit probleem als volgt oplossen:

1. Meld u aan bij Azure Portal. 
1. Selecteer in de web-app **netwerken** en selecteer vervolgens **VNet-integratie**.
1. Controleer of u het externe virtuele netwerk kunt zien. Voer hand matig de adres ruimte van het externe virtuele netwerk in (**synchronisatie netwerk** en **Voeg routes toe**).

Raadpleeg voor meer informatie de volgende artikelen:

* [Een app integreren met een virtueel Azure-netwerk](../app-service/web-sites-integrate-with-vnet.md)
* [Over point-to-site-VPN-routering](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Problemen met een configuratie fout bericht voor peering in virtuele netwerken oplossen 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>De huidige Tenant `<TENANT ID>` is niet gemachtigd om toegang te krijgen tot het gekoppelde abonnement

Zie [peering maken-Azure cli](./create-peering-different-subscriptions.md#cli)om dit probleem op te lossen.

### <a name="not-connected"></a>Niet verbonden

U kunt dit probleem oplossen door de peering uit beide virtuele netwerken te verwijderen en vervolgens opnieuw te maken.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Kan geen peer van een virtueel Databricks-netwerk uitvoeren

U kunt dit probleem oplossen door de peering van het virtuele netwerk te configureren onder **Azure Databricks** en vervolgens het virtuele doel netwerk op te geven met behulp van de **resource-id**. Zie [een virtueel Databricks-netwerk koppelen aan een extern virtueel netwerk](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-peering#id2)voor meer informatie.

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>Het externe virtuele netwerk heeft geen gateway

Dit probleem doet zich voor wanneer u virtuele netwerken peert van verschillende tenants en deze later wilt configureren `Use Remote Gateways` . Een beperking van de Azure Portal is dat de aanwezigheid van een virtuele netwerk gateway in een virtueel netwerk van een andere Tenant niet kan worden gevalideerd.

Er zijn twee manieren om het probleem op te lossen:

 * Verwijder de Peerings en activeer de `Use Remote Gateways` optie wanneer u een nieuwe peering maakt.
 * Gebruik Power shell of CLI in plaats van de Azure Portal om in te scha kelen `Use Remote Gateways` .

## <a name="next-steps"></a>Volgende stappen

* [Problemen met de connectiviteit tussen virtuele Azure-machines oplossen](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
