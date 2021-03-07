---
title: Kennisgeving van migratie van Gateway verkeer
description: Artikel geeft gebruikers informatie over de migratie van IP-adressen van Azure SQL Database gateways
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 588c6548afb07fb8ee3de5152c240ddd9ea2293b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430187"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database verkeer migratie naar nieuwere gateways
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Naarmate de Azure-infra structuur wordt verbeterd, zal micro soft hardware periodiek vernieuwen om ervoor te zorgen dat we de best mogelijke klant ervaring bieden. In de komende maanden kunnen we gateways toevoegen die zijn gebouwd op nieuwere hardware-generaties, verkeer naar hen migreren en uiteindelijk buiten gebruik stellen van gateways die zijn gebouwd op oudere hardware in sommige regio's.  

Klanten ontvangen een melding via service Health Notifications voordat er wijzigingen zijn in de gateways die beschikbaar zijn in elke regio. Klanten kunnen [de Azure Portal gebruiken om waarschuwingen in het activiteiten logboek in te stellen](../../service-health/alerts-activity-log-service-notifications-portal.md).

De meest recente informatie wordt bewaard in de tabel met [IP-adressen van de Azure SQL database gateway](connectivity-architecture.md#gateway-ip-addresses) .

## <a name="status-updates"></a>Status updates

# <a name="in-progress"></a>[Actief](#tab/in-progress-ip)

## <a name="april-2021"></a>April 2021
Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's:
- Noor wegen-Oost: 51.120.96.33
- Zuid-Azië-oost: 13.67.16.193
- Zuid-Afrika-noord: 102.133.152.32
- Korea-zuid: 52.231.151.96
- Noord-Centraal: US 52.162.105.9
- Australië-Zuid-Oost: 13.77.49.32 

Deze SQL-gateways beginnen met het accepteren van klant verkeer op 5 april 2021.

## <a name="march-2021"></a>2021 maart
De volgende SQL-gateways in meerdere regio's worden gedeactiveerd:

- Brazilië-zuid: 104.41.11.5
- Azië-oost: 191.234.2.139
- VS-Oost: 191.238.6.43
- Japan-Oost: 191.237.240.43
- Japan-West: 191.238.68.11
- Europa-noord: 191.235.193.75
- VS Zuid-Centraal: 23.98.162.75
- Zuidoost-Azië: 23.100.117.95
- Europa-west: 191.237.232.75
- VS-West: 23.99.34.75

Er wordt geen invloed op de klant verwacht omdat deze gateways (die worden uitgevoerd op oudere hardware) geen verkeer van de klant door sturen. De IP-adressen voor deze gateways moeten worden gedeactiveerd op 15 maart 2021.

## <a name="february-2021"></a>Februari 2021
Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's:

- VS-centraal: 13.89.169.20

Deze SQL-gateways beginnen met het accepteren van klant verkeer op 28 februari 2021.

## <a name="january-2021"></a>Januari 2021
Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's:

- Australië-centraal: 20.36.104.6, 20.36.104.7 
- Australië-centraal 2:20.36.112.6 
- Brazilië-zuid: 191.234.144.16, 191.234.152.3 
- Canada-oost: 40.69.105.9, 40.69.105.10
- India centraal: 104.211.86.30, 104.211.86.31 
- Azië-oost: 13.75.32.14 
- Frankrijk-centraal: 40.79.137.8, 40.79.145.12 
- Frankrijk-zuid: 40.79.177.10, 40.79.177.12
- Korea-centraal: 52.231.17.22, 52.231.17.23
- India-West: 104.211.144.4

Deze SQL-gateways beginnen met het accepteren van klant verkeer op 31 januari 2021.

# <a name="completed"></a>[Voltooid](#tab/completed-ip)
De volgende gateway migraties zijn voltooid: 

### <a name="october-2020"></a>Oktober 2020

Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's:

- Duitsland-west-centraal: 51.116.240.0, 51.116.248.0

Deze SQL-gateways beginnen het accepteren van klant verkeer op 12 oktober 2020. 

### <a name="september-2020"></a>September 2020
Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's. Deze SQL-gateways beginnen het accepteren van klant verkeer op **15 September 2020**:

- Australië-zuidoost: 13.77.48.10
- Canada-oost: 40.86.226.166, 52.242.30.154
- UK-zuid: 51.140.184.11, 51.105.64.0

Bestaande SQL-gateways gaan het verkeer accepteren in de volgende regio's. Deze SQL-gateways beginnen het accepteren van klant verkeer op **15 September 2020** :

- Australië-zuidoost: 191.239.192.109 en 13.73.109.251
- VS-Midden: 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96 en 104.208.21.1
- Azië-oost: 191.234.2.139, 52.175.33.150 en 13.75.32.4
- VS-Oost: 40.121.158.30, 40.79.153.12, 191.238.6.43 en 40.78.225.32
- VS-Oost 2:40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107 en 104.208.150.3
- Frankrijk-centraal: 40.79.137.0 en 40.79.129.1
- Japan-West: 104.214.148.156, 40.74.100.192, 191.238.68.11 en 40.74.97.10
- Noord-Centraal VS: 23.96.178.199, 23.98.55.75 en 52.162.104.33
- Zuidoost-Azië: 104.43.15.0, 23.100.117.95 en 40.78.232.3
- VS-West: 104.42.238.205, 23.99.34.75 en 13.86.216.196

Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's. Deze SQL-gateways beginnen het accepteren van klant verkeer op **10 September 2020**:

- West-Centraal VS: 13.78.248.43 
- Zuid-Afrika-noord: 102.133.120.2  

Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's. Deze SQL-gateways beginnen het accepteren van klant verkeer op **1 September 2020**:

- Europa-noord: 13.74.104.113 
- West-VS2:40.78.248.10 
- Europa-west: 52.236.184.163 
- Zuid-Centraal VS: 20.45.121.1, 20.49.88.1 

Bestaande SQL-gateways gaan het verkeer accepteren in de volgende regio's. Deze SQL-gateways beginnen het accepteren van klant verkeer op **1 September 2020**:
- Japan-Oost: 40.79.184.8, 40.79.192.5


### <a name="august-2020"></a>Augustus 2020

Nieuwe SQL-gateways worden toegevoegd aan de volgende regio's:

- Australië-oost: 13.70.112.9
- Canada-centraal: 52.246.152.0, 20.38.144.1 
- VS-West 2:40.78.240.8

Deze SQL-gateways beginnen het accepteren van klant verkeer op 10 augustus 2020. 

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
- Japan - oost
- VS - oost 2
- Azië - oost

---

## <a name="impact-of-this-change"></a>Impact van deze wijziging

Verkeer migratie kan het open bare IP-adres wijzigen dat DNS voor uw data base in Azure SQL Database verhelpt.
U hebt mogelijk gevolgen voor het volgende:

- Het IP-adres voor een bepaalde gateway in uw on-premises firewall is vastgelegd.
- Subnetten die gebruikmaken van micro soft. SQL als een service-eind punt, maar niet kunnen communiceren met de IP-adressen van de gateway
- De [zone-redundante configuratie gebruiken voor de laag voor algemeen](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) gebruik
- Gebruik de [zone redundante configuratie voor Premium-& bedrijfs kritieke lagen](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability)

U hebt de volgende gevolgen:
 
- Omleiding als verbindings beleid
- Verbindingen met SQL Database van binnen Azure en met behulp van service Tags
- Verbindingen die zijn gemaakt met ondersteunde versies van het JDBC-stuur programma voor SQL Server, zien geen invloed. Zie [micro soft JDBC-stuur programma voor SQL Server downloaden](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)voor een ondersteunde versie van JDBC.

## <a name="what-to-do-you-do-if-youre-affected"></a>Wat u moet doen als dit van invloed is

We raden u aan om uitgaand verkeer naar IP-adressen toe te staan voor alle [gateway-IP-adressen](connectivity-architecture.md#gateway-ip-addresses) in de regio op TCP-poort 1433 en poort bereik 11000-11999. Deze aanbeveling is van toepassing op clients die vanaf on-premises verbinding maken en die verbinding maken via service-eind punten. Zie [verbindings beleid](connectivity-architecture.md#connection-policy)voor meer informatie over poortbereiken.

Voor verbindingen die zijn gemaakt via toepassingen met het micro soft JDBC-stuur programma onder versie 4,0, kan het certificaat niet worden gevalideerd. Lagere versies van micro soft JDBC zijn afhankelijk van de algemene naam (CN) in het veld onderwerp van het certificaat. De beperking is om ervoor te zorgen dat de eigenschap hostNameInCertificate is ingesteld op *. database.windows.net. Zie [verbinding maken met versleuteling](/sql/connect/jdbc/connecting-with-ssl-encryption)voor meer informatie over het instellen van de eigenschap hostNameInCertificate.

Als de bovenstaande oplossing niet werkt, kunt u een ondersteunings aanvraag indienen voor SQL Database of een door SQL beheerd exemplaar met behulp van de volgende URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure SQL-connectiviteits architectuur](connectivity-architecture.md)