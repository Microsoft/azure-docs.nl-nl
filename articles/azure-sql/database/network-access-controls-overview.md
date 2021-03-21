---
title: Netwerk toegangs beheer
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Overzicht van het beheren en controleren van de netwerk toegang voor Azure SQL Database en Azure Synapse Analytics.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 90bc57af3aaf0d11cd354bfe7163014f836a72e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460001"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-network-access-controls"></a>Azure SQL Database-en Azure Synapse Analytics-netwerk toegangs beheer

Wanneer u een logische SQL-Server maakt op basis van de [Azure Portal](single-database-create-quickstart.md) voor Azure SQL database en Azure Synapse Analytics, is het resultaat een openbaar eind punt met de notatie *yourservername.database.Windows.net*.

U kunt de volgende besturings elementen voor netwerk toegang gebruiken om toegang tot een Data Base selectief toe te staan via het open bare eind punt:

- Azure-Services toestaan: als deze is ingesteld op aan, kunnen andere resources binnen de grens van Azure, bijvoorbeeld een virtuele machine van Azure, toegang krijgen tot SQL Database
- IP-firewall regels: gebruik deze functie om expliciet verbindingen toe te staan van een specifiek IP-adres, bijvoorbeeld van on-premises machines

U kunt ook persoonlijke toegang tot de data base via [virtuele netwerken](../../virtual-network/virtual-networks-overview.md) toestaan via:

- Firewall regels voor virtuele netwerken: gebruik deze functie om verkeer toe te staan van een specifiek virtueel netwerk binnen de Azure-grens
- Privé-koppeling: gebruik deze functie om een persoonlijk eind punt te maken voor een [logische SQL-Server](logical-servers.md) binnen een specifiek virtueel netwerk

> [!IMPORTANT]
> Dit artikel is *niet* van toepassing op een door **SQL beheerd exemplaar**. Zie [verbinding maken met een Azure SQL Managed instance](../managed-instance/connect-application-instance.md) voor meer informatie over de netwerk configuratie.

Zie de onderstaande video voor een gedetailleerde uitleg van deze toegangs controles en wat ze doen:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Azure-Services toestaan

Bij het maken van een nieuwe logische SQL-Server [vanuit het Azure Portal](single-database-create-quickstart.md), is deze instelling standaard ingesteld op **uit**. Deze instelling wordt weer gegeven wanneer connectiviteit is toegestaan met behulp van een openbaar service-eind punt.

U kunt deze instelling ook wijzigen via het deel venster Firewall nadat de logische SQL-Server als volgt is gemaakt.
  
![Scherm opname van Server Firewall beheren][2]

Als deze functie is **ingesteld op aan, staat** uw server communicatie toe van alle resources binnen de grens van Azure, die al dan niet deel kan uitmaken van uw abonnement.

In veel gevallen is de instelling **on** moeilijker dan de meeste klanten. U kunt deze instelling instellen op **uit** en vervangen door meer beperkende IP-firewall regels of firewall regels voor virtuele netwerken. 

Dit heeft echter gevolgen voor de volgende functies die worden uitgevoerd op virtuele machines in azure die geen deel uitmaken van uw virtuele netwerk en daarom verbinding maken met de data base via een Azure IP-adres:

### <a name="import-export-service"></a>Import export service

Import export service werkt niet wanneer **toegang tot Azure-Services toestaan** is ingesteld op **uit**. U kunt het probleem echter omzeilen [door sqlpackage.exe hand matig uit te voeren vanaf een Azure VM of door de export](./database-import-export-azure-services-off.md) rechtstreeks in uw code uit te voeren met behulp van de DACFX-API.

### <a name="data-sync"></a>Gegevens synchroniseren

Als u de functie gegevens synchronisatie wilt gebruiken om **toegang tot Azure** -Services **in te stellen**, moet u afzonderlijke firewall regel vermeldingen maken [om IP-adressen](firewall-create-server-level-portal-quickstart.md) van de SQL- **service-tag** toe te voegen voor de regio die als host fungeert voor de **hub** -data base.
Voeg deze firewall regels op server niveau toe aan de servers die fungeren als host voor de data bases van zowel **hubs** als **leden** (die zich in verschillende regio's kunnen bevinden)

Gebruik het volgende Power shell-script om IP-adressen te genereren die overeenkomen met de SQL-service-tag voor de regio vs West

```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> Get-AzNetworkServiceTag retourneert het globale bereik voor SQL service-tag ondanks het opgeven van de locatie parameter. Zorg ervoor dat u deze filtert op de regio die de hub-data base host die door de synchronisatie groep wordt gebruikt

Houd er rekening mee dat de uitvoer van het Power shell-script zich in CIDR-notatie (Classloos Inter-Domain Routing) bevindt. Dit moet worden geconverteerd naar een indeling van het begin-en eind-IP-adres met [Get-IPrangeStartEnd.ps1](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) als volgt:

```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26
start        end
-----        ---
52.229.17.64 52.229.17.127
```

U kunt dit extra Power shell-script gebruiken om alle IP-adressen van CIDR te converteren naar de begin-en eind notatie van het IP-adres.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```

U kunt deze nu als afzonderlijke firewall regels toevoegen en vervolgens instellen **dat Azure-Services toegang heeft**  tot de server.

## <a name="ip-firewall-rules"></a>IP-firewallregels

Op IP gebaseerde firewall is een functie van de logische SQL-Server in azure waarmee de toegang tot uw server wordt voor komen, totdat u de [IP-adressen](firewall-create-server-level-portal-quickstart.md) van de client computers expliciet toevoegt.

## <a name="virtual-network-firewall-rules"></a>Firewallregels voor virtueel netwerk

Naast de IP-regels, kunt u met de server firewall regels voor het *virtuele netwerk* definiëren.  
Zie [service-eind punten en-regels voor virtuele netwerken voor Azure SQL database](vnet-service-endpoint-rule-overview.md) of Bekijk deze video:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

### <a name="azure-networking-terminology"></a>Terminologie van Azure Networking

Houd rekening met de volgende Azure-netwerk termen wanneer u de firewall regels voor virtuele netwerken bekijkt

**Virtueel netwerk:** U kunt virtuele netwerken koppelen aan uw Azure-abonnement

**Subnet:** Een virtueel netwerk bevat **subnetten**. Alle Azure virtual machines (Vm's) die u hebt toegewezen aan subnetten. Eén subnet kan meerdere Vm's of andere reken knooppunten bevatten. Reken knooppunten die zich buiten uw virtuele netwerk bevinden, hebben geen toegang tot het virtuele netwerk tenzij u de beveiliging zo configureert dat toegang wordt toegestaan.

**Service-eind punt van virtueel netwerk:** Een [service-eind punt van een virtueel netwerk](../../virtual-network/virtual-network-service-endpoints-overview.md) is een subnet waarvan de eigenschaps waarden een of meer formele namen van Azure-service typen bevatten. In dit artikel zijn we geïnteresseerd in de type naam van **micro soft. SQL**, die verwijst naar de Azure-service met de naam SQL database.

**Regel voor virtueel netwerk:** Een regel voor het virtuele netwerk voor uw server is een subnet dat wordt vermeld in de toegangs beheer lijst (ACL) van uw server. Het subnet moet de naam van het **micro soft. SQL** -type bevatten in de ACL voor uw data base in SQL database. Een regel voor het virtuele netwerk geeft aan dat uw server communicatie accepteert van elk knoop punt dat zich in het subnet bevindt.

## <a name="ip-vs-virtual-network-firewall-rules"></a>Firewall regels voor IP versus virtueel netwerk

Met de Azure SQL Database firewall kunt u IP-adresbereiken opgeven waarvan de communicatie in SQL Database wordt geaccepteerd. Deze aanpak is nauw keurig voor stabiele IP-adressen die zich buiten het particuliere Azure-netwerk bevinden. Virtuele machines (Vm's) in het particuliere Azure-netwerk zijn echter geconfigureerd met *dynamische* IP-adressen. Dynamische IP-adressen kunnen veranderen wanneer de virtuele machine opnieuw wordt opgestart en de op IP gebaseerde firewall regel ongeldig maken. Het is Folly om een dynamisch IP-adres op te geven in een firewall regel, in een productie omgeving.

U kunt deze beperking omzeilen door een *statisch* IP-adres voor uw virtuele machine te verkrijgen. Zie [een virtuele machine maken met een statisch openbaar IP-adres met behulp van de Azure Portal](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md)voor meer informatie. De aanpak van een statisch IP-adres kan echter moeilijk te beheren zijn, en het kost veel tijd om op schaal te worden uitgevoerd.

Regels voor virtuele netwerken zijn eenvoudiger om de toegang tot stand te brengen en te beheren vanaf een specifiek subnet dat uw virtuele machines bevat.

> [!NOTE]
> U kunt nog geen SQL Database in een subnet hebben. Als uw server een knoop punt in een subnet in het virtuele netwerk is, kunnen alle knoop punten in het virtuele netwerk communiceren met uw SQL Database. In dit geval kunnen uw Vm's communiceren met SQL Database zonder dat er regels voor het virtuele netwerk of IP-regels nodig zijn.

## <a name="private-link"></a>Private Link

Met persoonlijke koppeling kunt u verbinding maken met een server via een **persoonlijk eind punt**. Een persoonlijk eind punt is een privé-IP-adres binnen een specifiek [virtueel netwerk](../../virtual-network/virtual-networks-overview.md) en subnet.

## <a name="next-steps"></a>Volgende stappen

- Zie [een Data Base maken in SQL database](single-database-create-quickstart.md)voor een Snelstartgids voor het maken van een IP-firewall regel op server niveau.

- Zie [Virtual Network Service-eind punten en-regels voor Azure SQL database](vnet-service-endpoint-rule-overview.md)voor een Snelstartgids voor het maken van een firewall regel op server niveau.

- Voor hulp bij het maken van verbinding met een data base in SQL Database van open-source-of toepassingen van derden raadpleegt [u Quick Start code samples to SQL database](/previous-versions/azure/ee336282(v=azure.100)).

- Voor informatie over aanvullende poorten die u mogelijk moet openen, raadpleegt u de sectie **SQL database: buiten** bevindend in poorten van meer [dan 1433 voor ADO.NET 4,5 en SQL database](adonet-v12-develop-direct-route-ports.md)

- Zie [Azure SQL-connectiviteits architectuur](connectivity-architecture.md) voor een overzicht van Azure SQL database connectiviteit

- Zie [Uw database beveiligen](security-overview.md) voor een overzicht van Azure SQL Database security

<!--Image references-->
[1]: media/quickstart-create-single-database/new-server2.png
[2]: media/quickstart-create-single-database/manage-server-firewall.png
