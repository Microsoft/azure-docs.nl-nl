---
title: Uw toepassing verbinden met een SQL-beheerd exemplaar
titleSuffix: Azure SQL Managed Instance
description: In dit artikel wordt beschreven hoe u uw toepassing verbindt met Azure SQL Managed instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 3106ec1a45b307207624e5cd077a222acccc83d1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692824"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>Uw toepassing verbinden met het beheerde Azure SQL-exemplaar
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

U hebt nu meerdere keuzes wanneer u bepaalt hoe en waar u uw toepassing host.

U kunt ervoor kiezen om de toepassing in de cloud te hosten met behulp van Azure App Service of een aantal geïntegreerde virtuele netwerk opties van Azure, zoals Azure App Service Environment, Azure Virtual Machines en schaal sets voor virtuele machines. U kunt ook een hybride Cloud aanpak maken en uw toepassingen on-premises houden.

Welke keuze u ook maakt, u kunt verbinding maken met Azure SQL Managed instance. 

![Hoge beschikbaarheid](./media/connect-application-instance/application-deployment-topologies.png)

In dit artikel wordt beschreven hoe u een toepassing verbindt met Azure SQL Managed Instance in een aantal verschillende toepassingsscenario's. 

## <a name="connect-inside-the-same-vnet"></a>Verbinding maken binnen hetzelfde VNet

Het maken van een toepassing binnen hetzelfde virtuele netwerk als een SQL Managed instance is het eenvoudigste scenario. Virtuele machines in het virtuele netwerk kunnen rechtstreeks verbinding maken met elkaar, zelfs als ze zich in verschillende subnetten bevinden. Dit betekent dat u alleen verbinding moet maken tussen een toepassing in App Service Environment of een virtuele machine om de connection string op de juiste wijze in te stellen.  

## <a name="connect-inside-a-different-vnet"></a>Verbinding maken binnen een ander VNet

Het verbinden van een toepassing wanneer deze zich in een ander virtueel netwerk bevindt vanuit een SQL Managed instance is een beetje complexer omdat het SQL Managed instance privé-IP-adressen heeft in het eigen virtuele netwerk. Als u verbinding wilt maken, moet een toepassing toegang hebben tot het virtuele netwerk waarin SQL Managed instance wordt geïmplementeerd. Daarom moet u een verbinding maken tussen de toepassing en het virtuele netwerk van het SQL-beheerde exemplaar. Dit scenario werkt alleen als de virtuele netwerken zich in hetzelfde abonnement behoeven.

Er zijn twee opties voor het verbinden van virtuele netwerken:

- [Azure VNet-peering](../../virtual-network/virtual-network-peering-overview.md)
- VNet-naar-VNet VPN-gateway ([Azure Portal](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [Power shell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure cli](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Peering is de voor keur omdat deze gebruikmaakt van het micro soft backbone-netwerk, dus in het oogpunt van connectiviteit is er geen merkbaar verschil in latentie tussen virtuele machines in een gekoppeld virtueel netwerk en in hetzelfde virtuele netwerk. De peering van het virtuele netwerk moet worden ondersteund tussen de netwerken in dezelfde regio. Globale Virtual Network-peering wordt ook ondersteund met de beperking die wordt beschreven in de onderstaande opmerking.  

> [!IMPORTANT]
> [Op 9/22/2020 zijn wereld wijde virtuele netwerk peering aangekondigd voor nieuwe virtuele clusters](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Dit betekent dat de peering van globale virtuele netwerken wordt ondersteund voor SQL-beheerde instanties die zijn gemaakt in lege subnetten na de aankondigings datum, en ook voor alle daaropvolgende beheerde exemplaren die in deze subnetten zijn gemaakt. Voor alle andere SQL Managed instances-ondersteuning voor peering is beperkt tot de netwerken in dezelfde regio vanwege de [beperkingen van globale virtuele netwerk peering](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Zie ook de relevante sectie van het artikel Veelgestelde [vragen over virtuele netwerken van Azure](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. 

## <a name="connect-from-on-premises"></a>Verbinding maken vanaf on-premises 

U kunt uw on-premises toepassing ook verbinden met een SQL-beheerd exemplaar. Een door SQL beheerd exemplaar kan alleen worden gebruikt via een privé-IP-adres. U moet een site-naar-site-verbinding maken tussen de toepassing en het virtuele netwerk van het SQL Managed instance om deze van on-premises te kunnen openen.

Er zijn twee opties voor het verbinden van on-premises met een virtueel Azure-netwerk:

- Site-naar-site-VPN-verbinding ([Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md), [Power shell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure cli](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) -verbinding  

Als u een on-premises Azure-verbinding tot stand hebt gebracht en u geen verbinding kunt maken met een SQL-beheerd exemplaar, controleert u of uw firewall een open uitgaande verbinding heeft op SQL-poort 1433 en het bereik van poorten voor omleiding van 11000-11999.

## <a name="connect-the-developer-box"></a>Verbinding maken tussen het vak voor ontwikkel aars

Het is ook mogelijk om verbinding te maken met uw ontwikkelaars venster met een SQL-beheerd exemplaar. SQL Managed instance is alleen toegankelijk via een privé-IP-adres. om het te openen vanuit het vak voor ontwikkel aars, moet u eerst een verbinding maken tussen het vak voor ontwikkel aars en het virtuele netwerk van het SQL Managed instance. Hiervoor configureert u een punt-naar-site-verbinding met een virtueel netwerk met behulp van systeem eigen Azure-certificaat verificatie. Zie  [een punt-naar-site-verbinding configureren om verbinding te maken met Azure SQL Managed instance van een on-premises computer](point-to-site-p2s-configure.md)voor meer informatie.

## <a name="connect-with-vnet-peering"></a>Verbinding maken met VNet-peering

Een ander scenario dat door klanten wordt geïmplementeerd, is de plek waar een VPN-gateway wordt geïnstalleerd in een afzonderlijk virtueel netwerk en een abonnement op een host die wordt gehost door SQL Managed instance. De twee virtuele netwerken worden vervolgens gelijkwaardig. In het volgende voor beeld van een architectuur diagram ziet u hoe dit kan worden geïmplementeerd.

![Peering op virtueel netwerk](./media/connect-application-instance/vnet-peering.png)

Zodra u de basis infrastructuur hebt ingesteld, moet u enkele instellingen wijzigen zodat de VPN-gateway de IP-adressen in het virtuele netwerk kan zien die als host fungeert voor SQL Managed instance. Hiervoor moet u de volgende zeer specifieke wijzigingen aanbrengen in de **instellingen voor peering**.

1. Ga in het virtuele netwerk dat als host fungeert voor de VPN-gateway naar **peerings**, ga naar de gekoppelde virtuele netwerk verbinding voor SQL Managed instance en klik vervolgens op **Gateway doorvoer toestaan**.
2. Ga in het virtuele netwerk dat als host fungeert voor SQL Managed instance naar **peerings**, ga naar de gekoppelde virtuele netwerk verbinding voor de VPN-gateway en klik vervolgens op **externe gateways gebruiken**.

## <a name="connect-azure-app-service"></a>Azure App Service verbinden 

U kunt ook verbinding maken met een toepassing die wordt gehost door Azure App Service. SQL Managed instance is alleen toegankelijk via een privé-IP-adres, dus als u toegang wilt krijgen tot Azure App Service, moet u eerst een verbinding maken tussen de toepassing en het virtuele SQL Managed instance-netwerk. Zie [uw app integreren met een virtueel Azure-netwerk](../../app-service/web-sites-integrate-with-vnet.md).  

Zie [problemen met virtuele netwerken en toepassingen oplossen](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting)voor probleem oplossing. Als er geen verbinding kan worden gemaakt, probeert u [de netwerk configuratie te synchroniseren](azure-app-sync-network-configuration.md).

Een speciaal geval van het verbinden van Azure App Service met een SQL Managed instance is wanneer u Azure App Service integreert in een netwerk dat is gekoppeld aan een virtueel netwerk van een SQL Managed instance. Voor dat geval moet de volgende configuratie worden ingesteld:

- Virtueel netwerk van SQL Managed instance mag geen gateway hebben  
- Voor het virtuele netwerk van SQL Managed instance moet de `Use remote gateways` optie set zijn ingesteld
- Voor het gekoppelde virtuele netwerk moet de `Allow gateway transit` optie set zijn ingesteld

Dit scenario wordt geïllustreerd in het volgende diagram:

![geïntegreerde app-peering](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>De functie voor integratie van virtuele netwerken integreert geen app met een virtueel netwerk met een ExpressRoute-gateway. Zelfs als de ExpressRoute-gateway is geconfigureerd in de modus voor samen werking, werkt de integratie van het virtuele netwerk niet. Als u toegang nodig hebt tot resources via een ExpressRoute-verbinding, kunt u App Service Environment gebruiken, die wordt uitgevoerd in uw virtuele netwerk.

## <a name="troubleshooting-connectivity-issues"></a>Connectiviteitsproblemen oplossen

Raadpleeg het volgende voor meer informatie over het oplossen van verbindings problemen:

- Als u geen verbinding kunt maken met een SQL Managed instance vanaf een virtuele Azure-machine binnen hetzelfde virtuele netwerk, maar met een ander subnet, controleert u of er een netwerk beveiligings groep is ingesteld op het VM-subnet dat de toegang blokkeert. Daarnaast kunt u de uitgaande verbinding openen voor SQL-poort 1433 en poorten in het bereik 11000-11999, omdat deze nodig zijn om verbinding te maken via omleiding binnen de Azure-grens.
- Zorg ervoor dat BGP-doorgifte is ingesteld op **ingeschakeld** voor de route tabel die is gekoppeld aan het virtuele netwerk.
- Als u P2S VPN gebruikt, controleert u de configuratie in de Azure Portal om te zien of er **ingangs-en uitgangs** nummers worden weer geven. Niet-nul getallen geven aan dat Azure verkeer routeert naar/van on-premises.

   ![ingangs-en uitgangs nummers](./media/connect-application-instance/ingress-egress-numbers.png)

- Controleer of de client computer (waarop de VPN-client wordt uitgevoerd) route vermeldingen heeft voor alle virtuele netwerken waartoe u toegang wilt. De routes worden opgeslagen in `%AppData%\Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` .

   ![route.txt](./media/connect-application-instance/route-txt.png)

   Zoals in deze afbeelding wordt weer gegeven, zijn er twee vermeldingen voor elk betrokken virtuele netwerk en een derde vermelding voor het VPN-eind punt dat is geconfigureerd in de portal.

   Een andere manier om de routes te controleren, is via de volgende opdracht. De uitvoer toont de routes naar de verschillende subnetten:

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- Als u gebruikmaakt van peering op virtueel netwerk, moet u ervoor zorgen dat u de instructies voor het instellen van [Gateway doorvoer toestaan en externe gateways gebruiken](#connect-from-on-premises)hebt gevolgd.

- Als u gebruikmaakt van peering van virtuele netwerken om verbinding te maken met een Azure App Service gehoste toepassing, en het virtuele SQL Managed instance-netwerk een openbaar IP-adres bereik heeft, moet u ervoor zorgen dat uw gehoste toepassings instellingen uw uitgaand verkeer naar open bare IP-netwerken kunnen routeren. Volg de instructies in [regionale virtuele netwerk integratie](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration).

## <a name="required-versions-of-drivers-and-tools"></a>Vereiste versies van Stuur Programma's en hulpprogram ma's

De volgende minimale versies van de hulpprogram ma's en stuur Programma's worden aanbevolen als u verbinding wilt maken met een SQL Managed instance:

| Stuur programma/hulp programma | Versie |
| --- | --- |
|.NET Framework | 4.6.1 (of .NET core) |
|ODBC-stuurprogramma| v17 |
|PHP-stuurprogramma| 5.2.0 |
|JDBC-stuurprogramma| 6.4.0 |
|Node.js-stuurprogramma| 2.1.1 |
|OLEDB-stuurprogramma| 18.0.2.0 |
|SSMS| 18,0 of [hoger](/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) of hoger |

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is SQL Managed instance?](sql-managed-instance-paas-overview.md)voor meer informatie over SQL Managed instance.
- Zie [een beheerd exemplaar maken](instance-create-quickstart.md)voor een zelf studie waarin wordt getoond hoe u een nieuw beheerd exemplaar maakt.