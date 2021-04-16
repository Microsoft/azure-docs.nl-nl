---
title: Azure SQL Database connectiviteitsarchitectuur
description: In dit document wordt uitgelegd Azure SQL Database connectiviteitsarchitectuur voor databaseverbindingen vanuit Azure of van buiten Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 01/25/2021
ms.openlocfilehash: 3442e3003ef8a299beb88cd212602c8713915474
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499951"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Azure SQL Database- en Azure Synapse Analytics-connectiviteitsarchitectuur
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

In dit artikel wordt de architectuur uitgelegd van verschillende onderdelen die netwerkverkeer naar een server in Azure SQL Database of Azure Synapse Analytics. Ook worden verschillende verbindingsbeleidsregels uitgelegd en wordt uitgelegd hoe dit van invloed is op clients die verbinding maken vanuit Azure en clients die verbinding maken van buiten Azure.

> [!IMPORTANT]
> Dit artikel is *niet* van toepassing op **Azure SQL Managed Instance**. Raadpleeg [Connectiviteitsarchitectuur voor een beheerd exemplaar.](../managed-instance/connectivity-architecture-overview.md)

## <a name="connectivity-architecture"></a>Connectiviteitsarchitectuur

Het volgende diagram biedt een overzicht op hoog niveau van de connectiviteitsarchitectuur.

![Diagram met een overzicht op hoog niveau van de connectiviteitsarchitectuur.](./media/connectivity-architecture/connectivity-overview.png)

In de volgende stappen wordt beschreven hoe een verbinding tot stand wordt gebracht om Azure SQL Database:

- Clients maken verbinding met de gateway, die een openbaar IP-adres heeft en luistert op poort 1433.
- Afhankelijk van het effectieve verbindingsbeleid stuurt of stuurt de gateway het verkeer door naar het juiste databasecluster.
- Binnen het databasecluster wordt verkeer doorgestuurd naar de juiste database.

## <a name="connection-policy"></a>Verbindingsbeleid

Servers in SQL Database en Azure Synapse ondersteuning voor de volgende drie opties voor de beleidsinstelling voor verbindingen van de server:

- **Omleiding (aanbevolen):** Clients maken rechtstreeks verbindingen met het knooppunt dat als host voor de database wordt gebruikt, wat leidt tot een verminderde latentie en verbeterde doorvoer. Clients moeten het volgende doen om deze modus te kunnen gebruiken voor verbindingen:
  - Sta uitgaande communicatie van de client toe naar Azure SQL IP-adressen in de regio op poorten in het bereik van 11000 11999. Gebruik de servicetags voor SQL om dit gemakkelijker te beheren.  
  - Sta uitgaande communicatie van de client toe Azure SQL Database ip-adressen van gateways op poort 1433.

- **Proxy:** In deze modus worden alle verbindingen via de Azure SQL Database geproxied, wat leidt tot een hogere latentie en verminderde doorvoer. Voor verbindingen die deze modus gebruiken, moeten clients uitgaande communicatie van de client naar Azure SQL Database gateway-IP-adressen op poort 1433 toestaan.

- **Standaardinstelling:** Dit is het verbindingsbeleid dat van kracht is op alle servers na het maken, tenzij u het verbindingsbeleid expliciet wijzigt in `Proxy` of `Redirect` . Het standaardbeleid is voor alle clientverbindingen die afkomstig zijn van Azure (bijvoorbeeld van een virtuele Azure-machine) en voor alle clientverbindingen die van buiten afkomstig zijn (bijvoorbeeld verbindingen vanaf uw lokale `Redirect` `Proxy` werkstation).

Wij raden het `Redirect` verbindingsbeleid ten zeerste aan via `Proxy` het verbindingsbeleid voor de laagste latentie en de hoogste doorvoer. U moet echter voldoen aan de aanvullende vereisten voor het toestaan van netwerkverkeer, zoals hierboven wordt beschreven. Als de client een virtuele Azure-machine is, kunt u dit doen met behulp van netwerkbeveiligingsgroepen (NSG's) met [servicetags.](../../virtual-network/network-security-groups-overview.md#service-tags) Als de client verbinding maakt vanaf een on-premises werkstation, moet u mogelijk samenwerken met uw netwerkbeheerder om netwerkverkeer via uw bedrijfsfirewall toe te staan.

## <a name="connectivity-from-within-azure"></a>Connectiviteit vanuit Azure

Als u verbinding maakt vanuit Azure, hebben uw verbindingen standaard een `Redirect` verbindingsbeleid van. Een beleid van betekent dat nadat de TCP-sessie tot stand is gebracht voor Azure SQL Database, de clientsessie vervolgens wordt omgeleid naar het juiste databasecluster met een wijziging in het virtuele IP-doel van die van de Azure SQL Database-gateway naar die van het `Redirect` cluster. Daarna worden alle volgende pakketten rechtstreeks naar het cluster gestroomd, en wordt de gateway Azure SQL Database omzeild. Het volgende diagram illustreert deze verkeersstroom.

![overzicht van architectuur](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Connectiviteit van buiten Azure

Als u verbinding maakt van buiten Azure, hebben uw verbindingen standaard een `Proxy` verbindingsbeleid van. Een beleid van betekent dat de TCP-sessie tot stand wordt gebracht via de Azure SQL Database-gateway en dat alle `Proxy` volgende pakketten via de gateway stromen. Het volgende diagram illustreert deze verkeersstroom.

![Diagram dat laat zien hoe de TCP-sessie tot stand wordt gebracht via Azure SQL Database gateway en alle volgende pakketten stromen via de gateway.](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Open ook TCP-poorten 1434 en 14000-14999 om verbinding te maken [met DAC](/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>IP-adressen van gateway

In de onderstaande tabel staan de IP-adressen van gateways per regio. Als u verbinding wilt SQL Database of Azure Synapse, moet u netwerkverkeer van en naar **alle** gateways voor de regio toestaan.

Meer informatie over hoe verkeer moet worden gemigreerd naar nieuwe gateways in specifieke [regio's, staat](gateway-migration.md) in het volgende artikel: Azure SQL Database migratie van verkeer naar nieuwere gateways

| Regionaam          | IP-adressen van gateway |
| --- | --- |
| Australië - centraal    | 20.36.105.0, 20.36.104.6, 20.36.104.7 |
| Australië - centraal 2   | 20.36.113.0, 20.36.112.6 |
| Australië - oost       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Australië - zuidoost | 191.239.192.109, 13.73.109.251, 13.77.48.10, 13.77.49.32 |
| Brazilië - zuid         | 191.233.200.14, 191.234.144.16, 191.234.152.3 |
| Canada - midden       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Canada - oost          | 40.86.226.166, 52.242.30.154, 40.69.105.9 , 40.69.105.10 |
| VS - centraal           | 13.67.215.62, 52.182.137.15, 104.208.16.96, 104.208.21.1, 13.89.169.20 |
| China East           | 139.219.130.35     |
| China - oost 2         | 40.73.82.1         |
| China - noord          | 139.219.15.17      |
| China - noord 2        | 40.73.50.0         |
| Azië - oost            | 52.175.33.150, 13.75.32.4, 13.75.32.14 |
| VS - oost              | 40.121.158.30, 40.79.153.12, 40.78.225.32 |
| VS - oost 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3, 40.70.144.193 |
| Frankrijk - centraal       | 40.79.137.0, 40.79.129.1, 40.79.137.8, 40.79.145.12 |
| Frankrijk - zuid         | 40.79.177.0, 40.79.177.10 ,40.79.177.12 |
| Duitsland - centraal      | 51.4.144.100       |
| Duitsland - noord - oost   | 51.5.144.179       |
| Duitsland - west-centraal | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| India - centraal        | 104.211.96.159, 104.211.86.30 , 104.211.86.31 |
| India - zuid          | 104.211.224.146    |
| India - west           | 104.211.160.80, 104.211.144.4 |
| Japan East           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 40.79.192.5 |
| Japan - west           | 104.214.148.156, 40.74.100.192, 40.74.97.10 |
| Korea - centraal        | 52.231.32.42, 52.231.17.22 ,52.231.17.23 |
| Korea - zuid          | 52.231.200.86, 52.231.151.96 |
| VS - noord-centraal     | 23.96.178.199, 23.98.55.75, 52.162.104.33, 52.162.105.9 |
| Europa - noord         | 40.113.93.91, 52.138.224.1, 13.74.104.113 |
| Noorwegen - oost          | 51.120.96.0, 51.120.96.33 |
| Noorwegen - west          | 51.120.216.0       |
| Zuid-Afrika - noord   | 102.133.152.0, 102.133.120.2, 102.133.152.32 |
| Zuid-Afrika - west    | 102.133.24.0       |
| VS - zuid-centraal     | 13.66.62.124, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Azië - zuidoost      | 104.43.15.0, 40.78.232.3, 13.67.16.193 |
| Zwitserland - noord    | 51.107.56.0, 51.107.57.0 |
| Zwitserland - west     | 51.107.152.0, 51.107.153.0 |
| UAE - centraal          | 20.37.72.64        |
| VAE - noord            | 65.52.248.0        |
| Verenigd Koninkrijk Zuid             | 51.140.184.11, 51.105.64.0, 51.140.144.36, 51.105.72.32 |
| Verenigd Koninkrijk West              | 51.141.8.11        |
| VS - west-centraal      | 13.78.145.25, 13.78.248.43, 13.71.193.32, 13.71.193.33 |
| Europa -west          | 40.68.37.158, 104.40.168.105, 52.236.184.163  |
| VS - west              | 104.42.238.205, 13.86.216.196   |
| VS - west 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
| VS - west 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |


## <a name="next-steps"></a>Volgende stappen

- Zie [conn-policy](/cli/azure/sql/server/conn-policy)Azure SQL Database informatie over het wijzigen van het verbindingsbeleid voor een server.
- Zie Azure SQL Database Poorten boven [1433 voor ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md)voor informatie over het verbindingsgedrag voor clients die ADO.NET 4.5 of hoger gebruiken.
- Zie Application Development Overview (Overzicht van toepassingsontwikkeling) SQL Database overzicht van de ontwikkeling [van algemene toepassingen.](develop-overview.md)
