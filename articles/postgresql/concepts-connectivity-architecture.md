---
title: Connectiviteits architectuur-Azure Database for PostgreSQL-één server
description: Beschrijft de connectiviteits architectuur van uw Azure Database for PostgreSQL-één server.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: conceptual
ms.date: 2/11/2021
ms.openlocfilehash: 97778951777677ce02adec59d906c1246494457a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369404"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Connectiviteits architectuur in Azure Database for PostgreSQL
In dit artikel wordt uitgelegd wat de Azure Database for PostgreSQL connectiviteits architectuur is en hoe het verkeer wordt omgeleid naar uw Azure Database for PostgreSQL data base-exemplaar van clients, zowel binnen als buiten Azure.

## <a name="connectivity-architecture"></a>Connectiviteitsarchitectuur
De verbinding met uw Azure Database for PostgreSQL wordt tot stand gebracht via een gateway die verantwoordelijk is voor de route ring van binnenkomende verbindingen naar de fysieke locatie van uw server in onze clusters. In het volgende diagram ziet u de verkeers stroom.

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="Overzicht van de connectiviteits architectuur":::


Wanneer de client verbinding maakt met de data base, wordt het connection string naar de server omgezet in het IP-adres van de gateway. De gateway luistert naar het IP-adres op poort 5432. Binnen het database cluster wordt verkeer doorgestuurd naar de juiste Azure Database for PostgreSQL. Daarom is het nodig om verbinding te maken met uw server, bijvoorbeeld van bedrijfs netwerken, om de firewall aan de **client zijde te openen, zodat uitgaand verkeer de gateways kan bereiken**. Hieronder vindt u een volledige lijst met de IP-adressen die worden gebruikt door onze gateways per regio.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>IP-adressen van Azure Database for PostgreSQL gateway

De Gateway Service wordt gehost op de groep stateless Compute-knoop punten die achter een IP-adres staan, wat uw client voor het eerst zou bereiken wanneer er verbinding wordt gemaakt met een Azure Database for PostgreSQL-server. 

Als onderdeel van het onderhoud van de service, zullen we de compute-hardware die als host fungeert voor de gateways periodiek vernieuwen om ervoor te zorgen dat we de veiligste en beste connectiviteits ervaring bieden. Wanneer de gateway-hardware wordt vernieuwd, wordt eerst een nieuwe ring van de reken knooppunten gebouwd. Deze nieuwe ring verzendt het verkeer voor alle nieuw gemaakte Azure Database for PostgreSQL servers en er is een ander IP-adres van oudere Gateway ringen in dezelfde regio om het verkeer te onderscheiden. De oudere Gateway-hardware blijft bestaande servers behouden, maar is gepland voor het buiten gebruik stellen in de toekomst. Voordat u een gateway-hardware buiten gebruik stelt, worden klanten die hun servers uitvoeren en verbinding maken met oudere Gateway ringen via e-mail en in de Azure Portal, drie maanden vooraf gewaarschuwd voordat ze buiten gebruik worden gesteld. Het buiten gebruik stellen van gateways kan invloed hebben op de connectiviteit met uw servers als 

* U kunt de IP-adressen van de gateway in de connection string van uw toepassing. Het wordt **niet aanbevolen**. Gebruik Fully Qualified Domain Name (FQDN) van uw server in de indeling <servername> . postgres.database.Azure.com, in de Connection String voor uw toepassing. 
* U werkt de nieuwere gateway-IP-adressen in de client-side firewall niet bij zodat uitgaand verkeer de nieuwe gateway ringen kan bereiken.

De volgende tabel geeft een lijst van de IP-adressen van de gateway van de Azure Database for PostgreSQL gateway voor alle gegevens regio's. De meest recente informatie van de gateway-IP-adressen voor elke regio wordt in de onderstaande tabel bewaard. In de onderstaande tabel vertegenwoordigen de kolommen de volgende:

* **IP-adressen van Gateway:** Deze kolom bevat de huidige IP-adressen van de gateways die worden gehost op de nieuwste generatie hardware. Als u een nieuwe server inricht, raden we u aan de firewall aan de client zijde te openen om uitgaand verkeer toe te staan voor de IP-adressen die in deze kolom worden weer gegeven.
* **IP-adressen van Gateway (buiten gebruik stellen):** Deze kolom bevat de IP-adressen van de gateways die worden gehost op een oudere generatie hardware die nu wordt buiten gebruik gesteld. Als u een nieuwe server inricht, kunt u deze IP-adressen negeren. Als u een bestaande server hebt, blijft de uitgaande regel voor de firewall voor deze IP-adressen behouden, omdat deze nog niet uit bedrijf is genomen. Als u de firewall regels voor deze IP-adressen weghaalt, kunnen er connectiviteits fouten optreden. In plaats daarvan moet u de nieuwe IP-adressen die worden vermeld in de kolom IP-adressen van Gateway, proactief toevoegen aan de regel voor uitgaande firewall zodra u de melding voor het buiten gebruik stellen ontvangt. Dit zorgt ervoor dat uw server wordt gemigreerd naar de meest recente gateway-hardware, maar er zijn geen onderbrekingen in de connectiviteit met uw server.
* **IP-adressen van Gateway (buiten gebruik gesteld):** In deze kolommen worden de IP-adressen van de gateway ringen weer gegeven, die buiten gebruik worden gesteld en niet meer in werking zijn. U kunt deze IP-adressen veilig verwijderen uit uw uitgaande firewall regel. 


| **Regio naam** | **IP-adressen van Gateway** |**IP-adressen van Gateway (buiten gebruik stellen)** | **IP-adressen van Gateway (buiten gebruik gesteld)** |
|:----------------|:-------------------------|:-------------------------------------------|:------------------------------------------|
| Australië - centraal| 20.36.105.0  | | |
| Australië-Central2     | 20.36.113.0  | | |
| Australië - oost | 13.75.149.87, 40.79.161.1     |  | |
| Australië - zuidoost |191.239.192.109, 13.73.109.251   |  | |
| Brazilië - zuid |191.233.201.8, 191.233.200.16    |  | 104.41.11.5|
| Canada - midden |40.85.224.249  | | |
| Canada - oost | 40.86.226.166    | | |
| VS - centraal | 23.99.160.139, 52.182.136.37, 52.182.136.38 | 13.67.215.62 | |
| China East | 139.219.130.35    | | |
| China - oost 2 | 40.73.82.1  | | |
| China - noord | 139.219.15.17    | | |
| China - noord 2 | 40.73.50.0     | | |
| Azië - oost | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     | | |
| VS - oost |40.71.8.203, 40.71.83.113 |40.121.158.30|191.238.6.43 |
| VS - oost 2 | 40.70.144.38, 52.167.105.38  | 52.177.185.181 | |
| Frankrijk - centraal | 40.79.137.0, 40.79.129.1  | | |
| Frankrijk - zuid | 40.79.177.0     | | |
| Duitsland - centraal | 51.4.144.100     | | |
| Duitsland-noord Oost | 51.5.144.179  | | |
| India - centraal | 104.211.96.159     | | |
| India - zuid | 104.211.224.146  | | |
| India - west | 104.211.160.80    | | |
| Japan - oost | 40.79.192.23 | 13.78.61.196 | |
| Japan - west | 191.238.68.11, 40.74.96.6, 40.74.96.7     | 104.214.148.156 | |
| Korea - centraal | 52.231.17.13   | 52.231.32.42 | |
| Korea - zuid | 52.231.145.3     | 52.231.200.86 | |
| VS - noord-centraal | 52.162.104.35, 52.162.104.36    | 23.96.178.199 | |
| Europa - noord | 52.138.224.6, 52.138.224.7  | 40.113.93.91 |191.235.193.75 |
| Zuid-Afrika - noord  | 102.133.152.0    | | |
| Zuid-Afrika - west | 102.133.24.0   | | |
| VS - zuid-centraal |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Azië - zuidoost | 40.78.233.2, 23.98.80.12     | 104.43.15.0 | |
| UAE - centraal | 20.37.72.64  | | |
| VAE - noord | 65.52.248.0    | | |
| Verenigd Koninkrijk Zuid | 51.140.184.11   | | |
| Verenigd Koninkrijk West | 51.141.8.11  | | |
| VS - west-centraal | 13.78.145.25     | | |
| Europa -west |13.69.105.208, 104.40.169.187 | 40.68.37.158 | 191.237.232.75 |
| VS - west |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| VS - west 2 | 13.66.226.202  | | |
||||

## <a name="next-steps"></a>Volgende stappen

* [Azure Database for PostgreSQL firewall regels maken en beheren met behulp van de Azure Portal](./howto-manage-firewall-using-portal.md)
* [Azure Database for PostgreSQL firewall regels maken en beheren met Azure CLI](./howto-manage-firewall-using-cli.md)