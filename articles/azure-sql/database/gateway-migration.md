---
title: Kennisgeving over migratie van gatewayverkeer
description: Artikel bevat kennisgeving aan gebruikers over de migratie van Azure SQL Database gateway-IP-adressen
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 07611a3620a2fd8efe0da075b03b55a5be3a5be9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505374"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database migratie van verkeer naar nieuwere gateways
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Naarmate de Azure-infrastructuur wordt verbeterd, vernieuwt Microsoft regelmatig hardware om ervoor te zorgen dat we de best mogelijke klantervaring bieden. In de komende maanden zijn we van plan gateways toe te voegen die zijn gebouwd op nieuwere hardware-generaties, verkeer naar deze gateways te migreren en uiteindelijk gateways uit bedrijf te nemen die zijn gebouwd op oudere hardware in sommige regio's.  

Klanten worden op de hoogte gesteld via service health-meldingen ruim voor elke wijziging in gateways die beschikbaar zijn in elke regio. Klanten kunnen [de Azure Portal om waarschuwingen voor activiteitenlogboek in te stellen.](../../service-health/alerts-activity-log-service-notifications-portal.md)

De meest recente informatie wordt bijgehouden in de tabel Azure SQL Database [IP-adressen van de](connectivity-architecture.md#gateway-ip-addresses) gateway.

## <a name="status-updates"></a>Statusupdates

# <a name="in-progress"></a>[Actief](#tab/in-progress-ip)
## <a name="may-2021"></a>Mei 2021
Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's:
- VK - zuid: 51.140.144.36, 51.105.72.32  
- VS - west-centraal: 13.71.193.32, 13.71.193.33 

Deze SQL-gateway begint op 17 mei 2021 met het accepteren van klantverkeer.

## <a name="april-2021"></a>April 2021
Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's:
- VS - oost 2: 40.70.144.193

Deze SQL-gateway begint op 30 april 2021 met het accepteren van klantverkeer.

Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's:
- Noorwegen - oost: 51.120.96.33
- Zuid Azië - oost: 13.67.16.193
- Zuid-Afrika - noord: 102.133.152.32
- Korea - zuid: 52.231.151.96
- Noord-Centraal: VS 52.162.105.9
- Australië - zuidoost: 13.77.49.32 

Deze SQL-gateways accepteren vanaf 5 april 2021 klantverkeer.

## <a name="march-2021"></a>Maart 2021
De volgende SQL-gateways in meerdere regio's worden momenteel gedeactiveerd:
- Brazilië - zuid: 104.41.11.5
- Azië - oost: 191.234.2.139
- VS - oost: 191.238.6.43
- Japan - oost: 191.237.240.43
- Japan - west: 191.238.68.11
- Europa - noord: 191.235.193.75
- VS - zuid-centraal: 23.98.162.75
- Azië - zuidoost: 23.100.117.95
- Europa - west: 191.237.232.75
- VS - west: 23.99.34.75

Er wordt geen impact van de klant verwacht omdat deze gateways (die worden uitgevoerd op oudere hardware) geen klantverkeer routeren. De IP-adressen voor deze gateways worden gedeactiveerd op 15 maart 2021.

# <a name="completed"></a>[Voltooid](#tab/completed-ip)
De volgende gatewaymigraties zijn voltooid: 

## <a name="february-2021"></a>Februari 2021
Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's:

- VS - centraal: 13.89.169.20

Deze SQL-gateways beginnen op 28 februari 2021 met het accepteren van klantverkeer.

## <a name="january-2021"></a>Januari 2021
Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's:

- Australië - centraal: 20.36.104.6, 20.36.104.7 
- Australië - centraal 2: 20.36.112.6 
- Brazilië - zuid: 191.234.144.16 ,191.234.152.3 
- Canada - oost: 40.69.105.9 ,40.69.105.10
- India - centraal: 104.211.86.30 , 104.211.86.31 
- Azië - oost: 13.75.32.14 
- Frankrijk - centraal: 40.79.137.8, 40.79.145.12 
- Frankrijk - zuid: 40.79.177.10 ,40.79.177.12
- Korea - centraal: 52.231.17.22 ,52.231.17.23
- India - west: 104.211.144.4

Deze SQL-gateways beginnen op 31 januari 2021 met het accepteren van klantverkeer.



### <a name="october-2020"></a>Oktober 2020

Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's:

- Duitsland - west-centraal: 51.116.240.0, 51.116.248.0

Deze SQL-gateways beginnen op 12 oktober 2020 met het accepteren van klantverkeer. 

### <a name="september-2020"></a>September 2020
Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's. Deze SQL-gateways beginnen op **15 september 2020** met het accepteren van klantverkeer:

- Australië - zuidoost: 13.77.48.10
- Canada - oost: 40.86.226.166, 52.242.30.154
- VK - zuid: 51.140.184.11, 51.105.64.0

Bestaande SQL-gateways accepteren verkeer in de volgende regio's. Deze SQL-gateways accepteren vanaf **15 september 2020 klantverkeer:**

- Australië - zuidoost: 191.239.192.109 en 13.73.109.251
- VS - centraal: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 en 104.208.21.1
- Azië - oost: 191.234.2.139, 52.175.33.150 en 13.75.32.4
- VS - oost: 40.121.158.30, 40.79.153.12, 191.238.6.43 en 40.78.225.32
- VS - oost 2: 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 en 104.208.150.3
- Frankrijk - centraal: 40.79.137.0 en 40.79.129.1
- Japan - west: 104.214.148.156, 40.74.100.192, 191.238.68.11 en 40.74.97.10
- VS - noord-centraal: 23.96.178.199, 23.98.55.75 en 52.162.104.33
- Azië - zuidoost: 104.43.15.0, 23.100.117.95 en 40.78.232.3
- VS - west: 104.42.238.205, 23.99.34.75 en 13.86.216.196

Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's. Deze SQL-gateways accepteren vanaf **10 september 2020 klantverkeer:**

- VS - west-centraal: 13.78.248.43 
- Zuid-Afrika - noord: 102.133.120.2  

Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's. Deze SQL-gateways beginnen op **1 september 2020** met het accepteren van klantverkeer:

- Europa - noord: 13.74.104.113 
- US - west 2: 40.78.248.10 
- Europa - west: 52.236.184.163 
- VS - zuid-centraal: 20.45.121.1, 20.49.88.1 

Bestaande SQL-gateways accepteren verkeer in de volgende regio's. Deze SQL-gateways beginnen op **1 september 2020** met het accepteren van klantverkeer:
- Japan - oost: 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>Augustus 2020

Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's:

- Australië - oost: 13.70.112.9
- Canada - centraal: 52.246.152.0, 20.38.144.1 
- VS - west 2: 40.78.240.8

Deze SQL-gateways beginnen op 10 augustus 2020 met het accepteren van klantverkeer. 

### <a name="october-2019"></a>Oktober 2019
- Brazilië - zuid
- VS - west
- Europa -west
- VS - oost
- VS - centraal
- Azië - zuidoost
- VS - zuid-centraal
- Europa - noord
- VS - noord-centraal
- Japan - west
- Japan East
- VS - oost 2
- Azië - oost

---

## <a name="impact-of-this-change"></a>Gevolgen van deze wijziging

Verkeermigratie kan het openbare IP-adres wijzigen dat dns voor uw database in Azure SQL Database.
Dit kan gevolgen hebben als u:

- Het IP-adres voor een bepaalde gateway in uw on-premises firewall is in code gecodeerd
- Hebt u subnetten die Microsoft.SQL als een service-eindpunt gebruiken, maar niet kunnen communiceren met de IP-adressen van de gateway
- De [zone-redundante configuratie gebruiken voor de laag Algemeen gebruik](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- De [zone-redundante configuratie gebruiken voor premium & bedrijfskritieke lagen](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)

U wordt niet beïnvloed als u het volgende hebt:
 
- Omleiding als verbindingsbeleid
- Verbindingen met SQL Database vanuit Azure en met behulp van servicetags
- Verbindingen die zijn gemaakt met behulp van ondersteunde versies van het JDBC-stuurprogramma voor SQL Server hebben geen invloed. Zie Microsoft JDBC-stuurprogramma downloaden voor meer informatie over ondersteunde [JDBC-SQL Server.](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)

## <a name="what-to-do-you-do-if-youre-affected"></a>Wat moet u doen als dit gevolgen heeft?

U wordt aangeraden uitgaand verkeer naar IP-adressen toe te staan voor alle [ip-adressen](connectivity-architecture.md#gateway-ip-addresses) van de gateway in de regio op TCP-poort 1433 en poortbereik 11000-11999. Deze aanbeveling is van toepassing op clients die verbinding maken vanaf on-premises en ook clients die verbinding maken via service-eindpunten. Zie Verbindingsbeleid voor meer informatie over [poortbereiken.](connectivity-architecture.md#connection-policy)

Verbindingen die zijn gemaakt vanuit toepassingen die gebruikmaken van het Microsoft JDBC-stuurprogramma onder versie 4.0, mislukken mogelijk de certificaatvalidatie. Lagere versies van Microsoft JDBC zijn afhankelijk van Algemene naam (CN) in het veld Onderwerp van het certificaat. De oplossing is om ervoor te zorgen dat de eigenschap hostNameInCertificate is ingesteld op *.database.windows.net. Zie Verbinding maken met versleuteling voor meer informatie [](/sql/connect/jdbc/connecting-with-ssl-encryption)over het instellen van de eigenschap hostNameInCertificate.

Als de bovenstaande oplossing niet werkt, kunt u een ondersteuningsaanvraag indienen voor SQL Database of SQL Managed Instance de volgende URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over Azure SQL [connectiviteitsarchitectuur](connectivity-architecture.md)