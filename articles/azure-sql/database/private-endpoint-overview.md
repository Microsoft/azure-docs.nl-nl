---
title: Azure Private Link
description: Overzicht van de functie Privé-eindpunt.
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.topic: overview
ms.custom: sqldbrb=1, fasttrack-edit
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 0ce8e45f301ac0fd84f7d5d27d81b63103768f89
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567709"
---
# <a name="azure-private-link-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure Private Link voor Azure SQL Database en Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Met een Private Link kunt u via een **privé-eindpunt** verbinding maken met verschillende PaaS-services in Azure. Raadpleeg de pagina [Documentatie over Private Link](../../private-link/index.yml) voor een lijst van PaaS-services die de Private Link-functionaliteit ondersteunen. Een privé-eindpunt is een privé-IP-adres binnen een specifiek [VNet](../../virtual-network/virtual-networks-overview.md) en subnet.

> [!IMPORTANT]
> Dit artikel is van toepassing op zowel Azure SQL Database als Azure Synapse Analytics. Ter vereenvoudiging verwijst de term “database” naar beide databases in Azure SQL Database en Azure Synapse Analytics. Alle verwijzingen naar “server” verwijzen ook naar de [logische SQL Server](logical-servers.md) die als host fungeert voor Azure SQL Database en Azure Synapse Analytics. Dit artikel is *niet* van toepassing op **Azure SQL Managed Instance**.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Private Link instellen voor Azure SQL Database 

### <a name="creation-process"></a>Proces maken
Privé-eindpunten kunnen worden gemaakt met behulp van de Azure-portal, PowerShell of de Azure CLI:
- [De portal](../../private-link/create-private-endpoint-portal.md)
- [PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [CLI](../../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>Goedkeuringsproces
Zodra de netwerkbeheerder het privé-eindpunt (PE) heeft gemaakt, kan de SQL-beheerder de verbinding met het privé-eindpunt (PEC) met SQL Database beheren.

1. Ga naar de serverresource in de Azure-portal volgens de stappen in de onderstaande schermopname

    - (1) Selecteer de verbindingen met het privé-eindpunt in het linker deelvenster
    - (2) Toont een lijst met alle privé-eindpunt verbindingen (PEC's)
    - (3) Bijbehorende privé-eindpunt (PE) dat is gemaakt ![Schermopname van alle PEC's][3]

1. Selecteer een individuele PEC uit de lijst door deze te kiezen.
![Schermopname van geselecteerde PEC][6]

1. De SQL-beheerder kan kiezen voor het goedkeuren of afwijzen van een PEC en optioneel een korte tekst als antwoord toevoegen.
![Schermopname van PEC-goedkeuring][4]

1. Na goedkeuring of afwijzing wordt in de lijst de juiste staat en de antwoordtekst weergegeven.
![Schermopname van alle PEC's na goedkeuring][5]

## <a name="on-premises-connectivity-over-private-peering"></a>On-premises connectiviteit via privé-peering

Wanneer klanten verbinding maken met het openbare eindpunt vanaf on-premises computers, moet hun IP-adres worden toegevoegd aan de op IP-gebaseerde firewall met behulp van een [firewallregel op serverniveau](firewall-create-server-level-portal-quickstart.md). Hoewel dit model goed werkt voor het toestaan van toegang tot afzonderlijke computers voor ontwikkel- of testwerkbelastingen, is het moeilijk te beheren in een productieomgeving.

Met Private Link kunnen klanten cross-premises toegang tot het privé-eindpunt inschakelen met behulp van [ExpressRoute](../../expressroute/expressroute-introduction.md), privé-peering of VPN-tunneling. Klanten kunnen vervolgens alle toegang uitschakelen via het openbare eindpunt en de op IP-gebaseerde firewall niet gebruiken om IP-adressen toe te staan.

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Cases van Private Link gebruiken voor Azure SQL Database 

Clients kunnen verbinding maken met het privé-eindpunt van hetzelfde virtuele netwerk, een peered virtueel netwerk in dezelfde regio, of via een verbinding van virtueel netwerk tot virtueel netwerk tussen regio's. Daarnaast kunnen clients via on-premises verbinding maken met behulp van ExpressRoute, privé peering of VPN-tunneling. Hieronder ziet u een vereenvoudigd diagram waarin de algemene gebruikscases worden weergegeven.

 ![Schema van connectiviteitsopties][1]

Bovendien kunnen services die niet rechtstreeks in het virtuele netwerk worden uitgevoerd, maar wel met deze zijn geïntegreerd (bijvoorbeeld App Service web-apps of-functies), ook een persoonlijke verbinding met de data base bezorgen. Zie voor meer informatie over deze specifieke use-case de [Web-app met persoonlijke connectiviteit met Azure SQL database](/azure/architecture/example-scenario/private-web-app/private-web-app) architectuur scenario.

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network"></a>De connectiviteit met de SQL Database testen vanaf een Azure-VM in hetzelfde virtuele netwerk

Voor dit scenario wordt ervan uitgegaan dat u een Azure Virtuele machine (VM) hebt gemaakt met Windows Server 2016. 

1. [Start een Extern bureaublad-sessie (RDP) en maak verbinding met de virtuele machine](../../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. U kunt vervolgens een aantal basis connectiviteitscontroles uitvoeren om ervoor te zorgen dat de VM verbinding maakt met de SQL Database via het privé-eindpunt met behulp van de volgende hulpprogramma's:
    1. Telnet
    1. Psping
    1. Nmap
    1. SQL Server Management Studio (SSMS)

### <a name="check-connectivity-using-telnet"></a>Connectiviteit controleren met Telnet

[Telnet-client](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29) is een Windows-functie die kan worden gebruikt om de connectiviteit te testen. Afhankelijk van de versie van het Windows-besturingssysteem moet u deze functie mogelijk expliciet inschakelen. 

Open een opdrachtpromptvenster nadat u Telnet hebt geïnstalleerd. Voer de Telnet-opdracht uit en geef het IP-adres en het privé-eindpunt van de database in SQL Database op.

```
>telnet 10.1.1.5 1433
```

Wanneer Telnet verbinding maakt, wordt in het opdrachtvenster een leeg scherm weer gegeven, zoals in de onderstaande afbeelding:

 ![Diagram van Telnet][2]

### <a name="check-connectivity-using-psping"></a>Connectiviteit controleren met Psping

[Psping](/sysinternals/downloads/psping) kan als volgt worden gebruikt om te controleren of de verbinding met het privé-eindpunt (PEC) luistert naar verbindingen op poort 1433.

Voer psping als volgt uit door de FQDN voor logische SQL-Server en poort 1433 op te geven:

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

De uitvoer laat zien dat Psping het privé-IP-adres dat is gekoppeld aan de PEC kan pingen.

### <a name="check-connectivity-using-nmap"></a>Connectiviteit controleren met Nmap

Nmap (Network Mapper) is een gratis en open source-hulpprogramma dat wordt gebruikt voor netwerkdetectie en beveiligingscontrole. Ga voor meer informatie en de download koppeling naar https://nmap.org. U kunt dit hulpprogramma gebruiken om ervoor te zorgen dat het privé-eindpunt luistert naar verbindingen op poort 1433.

Voer Nmap als volgt uit door het adresbereik op te geven van het subnet dat als host fungeert voor het privé-eindpunt.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

In het resultaat ziet u dat één IP-adres actief is. dat overeenkomt met het IP-adres voor het privé-eindpunt.

### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>Controleer de connectiviteit met behulp van SQL Server Management Studio (SSMS)
> [!NOTE]
> Gebruik de **FQDN (Fully Qualified Domain Name)** van de server in verbindingstekenreeksen voor uw clients (`<server>.database.windows.net`). Aanmeldingspogingen rechtstreeks naar het IP-adres of met behulp van de FQDN van de privékoppeling (`<server>.privatelink.database.windows.net`) zullen falen. Dit gedrag is inherent aan het ontwerp, omdat het privé-eindpunt verkeer naar de SQL-gateway in de regio routeert en de juiste FQDN moet worden opgegeven om aanmeldingen te laten slagen.

Volg de onderstaande stappen voor gebruik van [SSMS om verbinding te maken met de SQL Database](connect-query-ssms.md). Nadat u verbinding hebt gemaakt met de SQL Database met behulp van SSMS, controleert u of u verbinding maakt vanaf het privé-IP-adres van de Azure VM door de volgende query uit te voeren:

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="data-exfiltration-prevention"></a>Preventie van gegevensexfiltratie

Gegevensexfiltratie in Azure SQL Database is wanneer een geautoriseerde gebruiker, zoals een databasebeheerder, gegevens uit het ene systeem kan extraheren en naar een andere locatie of systeem buiten de organisatie kan verplaatsen. De gebruiker verplaatst bijvoorbeeld de gegevens naar een opslagaccount dat eigendom is van een derde partij.

Overweeg een scenario met een gebruiker die SQL Server Management Studio (SSMS) uitvoert binnen een virtuele machine in Azure die verbinding maakt met een database in SQL Database. Deze SQL Database bevindt zich in het datacentrum US - west. In het onderstaande voorbeeld ziet u hoe u de toegang tot de openbare eindpunten op SQL Database met behulp van besturingselementen voor netwerktoegang kunt beperken.

1. Schakel alle Azure-serviceverkeer uit naar SQL Database via het openbare eindpunt door Azure-services toestaan **UIT** te schakelen. Zorg ervoor dat er geen IP-adressen zijn toegestaan in de firewallregels op server- en databaseniveau. Zie [Azure SQL Database en Azure Synapse Analytics-netwerk toegangsbeheer](network-access-controls-overview.md)voor meer informatie.
1. Alleen verkeer naar de database in SQL Database toestaan met behulp van het privé-IP-adres van de VM. Zie de artikelen over [Service-eindpunt](vnet-service-endpoint-rule-overview.md) en [firewallregels voor virtuele netwerken](firewall-configure.md)voor meer informatie.
1. Op de Azure VM beperkt u het bereik van de uitgaande verbinding met behulp van [Netwerkbeveiligingsgroepen (NSG's)](../../virtual-network/manage-network-security-group.md) en servicetags als volgt
    - Geef een NSG-regel op om verkeer voor Service Tag = SQL.WestUs toe te staan – alleen verbinding toestaan met SQL Database in US – West
    - Geef een NSG-regel (met een **hogere prioriteit**) op om verkeer te weigeren voor Service Tag = SQL – verbindingen met SQL Database in alle regio's weigeren

Aan het einde van deze installatie kan de Azure-VM alleen verbinding maken met een database in SQL Database in de regio US - west. De connectiviteit is echter niet beperkt tot één database in SQL Database. De VM kan nog steeds verbinding maken met elke database in de regio US - west, met inbegrip van de databases die geen onderdeel zijn van het abonnement. Hoewel we het bereik van de gegevensexfiltratie in het bovenstaande scenario naar een bepaalde regio hebben gereduceerd, hebben we het niet geheel verwijderd.

Met Private Link kunnen klanten nu netwerk toegangsbeheer instellen, zoals NSG's om de toegang tot het privé-eindpunt te beperken. Afzonderlijke Azure PaaS-resources worden vervolgens toegewezen aan specifieke privé-eindpunten. Een kwaadwillende Insider heeft alleen toegang tot de toegewezen PaaS-resource (bijvoorbeeld een database in SQL Database) en geen andere resource. 

## <a name="limitations"></a>Beperkingen 
Verbindingen met een privé-eindpunt ondersteunen uitsluitend **Proxy** als het [verbindingsbeleid](connectivity-architecture.md#connection-policy)


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network"></a>Verbinding maken vanaf een Azure-VM in een Peered Virtual Network 

Configureer [peering van virtuele netwerken](../../virtual-network/tutorial-connect-virtual-networks-powershell.md) om verbinding te maken met de SQL Database vanuit een Azure-VM in een peered virtueel netwerk.

## <a name="connecting-from-an-azure-vm-in-virtual-network-to-virtual-network-environment"></a>Verbinding maken van een Azure-VM in een virtueel netwerk naar een virtuele netwerkomgeving

Configureer [een VPN-gatewayverbinding van virtueel netwerk naar virtueel netwerk](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) om connectiviteit te maken met een database in SQL Database vanaf een Azure VM in een andere regio of een ander abonnement.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Verbinding maken vanuit een on-premises omgeving via VPN

Als u verbinding wilt maken vanuit een on-premises omgeving met de database in SQL Database, kiest en implementeert u een van de volgende opties:
- [Punt-naar-site-verbinding](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [Site-naar-site-VPN-verbinding](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute-circuit](../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-azure-synapse-analytics-to-azure-storage-using-polybase-and-the-copy-statement"></a>Verbinding maken vanaf Azure Synapse Analytics met Azure Storage met behulp van PolyBase en de COPY-instructie

PolyBase en de COPY-instructie worden vaak gebruikt voor het laden van gegevens in Azure Synapse Analytics vanaf Azure Storage-accounts. Als het Azure Storage-account waarvan u gegevens wilt laden, alleen toegang heeft tot een set virtueel netwerk-subnetten via Privé-eindpunten, Service-eindpunten of op IP-gebaseerde firewalls, wordt de connectiviteit van PolyBase en de COPY-instructie naar het account verbroken. Voor het inschakelen van zowel import- als exportscenario's met Azure Synapse Analytics waarmee verbinding wordt gemaakt met Azure Storage dat is beveiligd met een virtueel netwerk, volgt u de stappen die [hier](vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage) worden weergegeven. 

## <a name="next-steps"></a>Volgende stappen

- Zie [Uw database beveiligen](security-overview.md) voor een overzicht van Azure SQL Database security
- Zie [Azure SQL Database Architecture](connectivity-architecture.md) voor een overzicht van Azure SQL Database connectiviteit
- Mogelijk bent u ook geïnteresseerd in de [Web-app met een particuliere verbinding met het Azure SQL database](/azure/architecture/example-scenario/private-web-app/private-web-app) Architecture-scenario, waarmee een webtoepassing buiten het virtuele netwerk wordt verbonden met het persoonlijke eind punt van een Data Base.

<!--Image references-->
[1]: media/quickstart-create-single-database/pe-connect-overview.png
[2]: media/quickstart-create-single-database/telnet-result.png
[3]: media/quickstart-create-single-database/pec-list-before.png
[4]: media/quickstart-create-single-database/pec-approve.png
[5]: media/quickstart-create-single-database/pec-list-after.png
[6]: media/quickstart-create-single-database/pec-select.png