---
title: Connectiviteits architectuur-Azure Database for MySQL
description: Beschrijft de connectiviteits architectuur voor uw Azure Database for MySQL-server.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: b23ff9762ef519d66ed55f75f27dcb80e2d18039
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007242"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Connectiviteits architectuur in Azure Database for MySQL
In dit artikel wordt uitgelegd wat de Azure Database for MySQL connectiviteits architectuur is en hoe het verkeer wordt omgeleid naar uw Azure Database for MySQL exemplaar van clients, zowel binnen als buiten Azure.

## <a name="connectivity-architecture"></a>Connectiviteitsarchitectuur
De verbinding met uw Azure Database for MySQL wordt tot stand gebracht via een gateway die verantwoordelijk is voor de route ring van binnenkomende verbindingen naar de fysieke locatie van uw server in onze clusters. In het volgende diagram ziet u de verkeers stroom.

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="Overzicht van de connectiviteits architectuur":::

Wanneer een client verbinding maakt met de data base, krijgen ze een connection string die verbinding maakt met de gateway. Deze gateway heeft een openbaar IP-adres dat luistert naar poort 3306. Binnen het database cluster wordt verkeer doorgestuurd naar de juiste Azure Database for MySQL. Daarom is het nodig om verbinding te maken met uw server, zoals van bedrijfs netwerken, het is nood zakelijk om de firewall aan de client zijde te openen zodat uitgaand verkeer de gateways kan bereiken. Hieronder vindt u een volledige lijst met de IP-adressen die worden gebruikt door onze gateways per regio.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>IP-adressen van Azure Database for MySQL gateway
De volgende tabel geeft een lijst van de primaire en secundaire IP-adressen van de Azure Database for MySQL gateway voor alle gegevens regio's. Het primaire IP-adres is het huidige IP-adres van de gateway en het tweede IP-adres is een failover-IP-adres in geval van een storing van de primaire. Zoals gezegd, moeten klanten uitgaande berichten toestaan voor de IP-adressen. Het tweede IP-adres luistert op geen enkele services totdat het wordt geactiveerd door Azure Database for MySQL om verbindingen te accepteren.

| **Naam regio** | **IP-adressen van Gateway** |
|:----------------|:-------------|
| Australië - centraal| 20.36.105.0     |
| Australië-Central2     | 20.36.113.0   |
| Australië - oost | 13.75.149.87, 40.79.161.1     |
| Australië - zuidoost |191.239.192.109, 13.73.109.251   |
| Brazil South | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| Canada - midden |40.85.224.249  |
| Canada - oost | 40.86.226.166    |
| Central US | 23.99.160.139, 13.67.215.62, 52.182.136.37, 52.182.136.38     |
| China East | 139.219.130.35    |
| China - oost 2 | 40.73.82.1  |
| China - noord | 139.219.15.17    |
| China - noord 2 | 40.73.50.0     |
| Azië - oost | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| VS - oost | 40.121.158.30, 191.238.6.43, 40.71.8.203, 40.71.83.113 |
| VS - oost 2 |40.79.84.180, 191.239.224.107, 52.177.185.181, 40.70.144.38, 52.167.105.38  |
| Frankrijk - centraal | 40.79.137.0, 40.79.129.1  |
| Frankrijk - zuid | 40.79.177.0     |
| Duitsland - centraal | 51.4.144.100     |
| Duitsland-noord Oost | 51.5.144.179  |
| India - centraal | 104.211.96.159     |
| India - zuid | 104.211.224.146  |
| India - west | 104.211.160.80    |
| Japan East | 13.78.61.196, 191.237.240.43, 40.79.192.23 |
| Japan - west | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    |
| Korea - centraal | 52.231.32.42   |
| Korea - zuid | 52.231.200.86    |
| VS - noord-centraal | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| Europa - noord | 40.113.93.91, 191.235.193.75, 52.138.224.6, 52.138.224.7    |
| Zuid-Afrika - noord  | 102.133.152.0    |
| Zuid-Afrika - west | 102.133.24.0   |
| South Central US |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0   |
| Azië - zuidoost | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| UAE - centraal | 20.37.72.64  |
| VAE - noord | 65.52.248.0    |
| Verenigd Koninkrijk Zuid | 51.140.184.11   |
| Verenigd Koninkrijk West | 51.141.8.11  |
| VS - west-centraal | 13.78.145.25     |
| Europa -west | 40.68.37.158, 191.237.232.75, 13.69.105.208 ,104.40.169.187 |
| VS - west | 104.42.238.205, 23.99.34.75 ,13.86.216.212, 13.86.217.212 |
| West US 2 | 13.66.226.202  |
||||

## <a name="connection-redirection"></a>Verbindings omleiding

Azure Database for MySQL ondersteunt een extra verbindings beleid, **omleiding**, waarmee de netwerk latentie tussen client toepassingen en MySQL-servers kan worden gereduceerd. Als deze functie is ingesteld, wordt na de eerste TCP-sessie naar de Azure Database for MySQL-server het back-end-adres van het knoop punt dat als host fungeert voor de MySQL-server naar de client geretourneerd. Daarna stroomt alle volgende pakketten rechtstreeks naar de server, zodat de gateway wordt omzeild. Als pakketten rechtstreeks naar de server stromen, hebben latentie en door Voer betere prestaties.

Deze functie wordt ondersteund in Azure Database for MySQL-servers met Engine versies 5,6, 5,7 en 8,0.

Ondersteuning voor omleiding is beschikbaar in de PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -uitbrei ding, ontwikkeld door micro soft en is beschikbaar op [PECL](https://pecl.php.net/package/mysqlnd_azure). Zie het artikel [omleiding configureren](./howto-redirection.md) voor meer informatie over het gebruik van omleiding in uw toepassingen.

> [!IMPORTANT]
> Ondersteuning voor omleiding in de PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -uitbrei ding is momenteel als preview-versie beschikbaar.

## <a name="next-steps"></a>Volgende stappen

* [Azure Database for MySQL firewall regels maken en beheren met behulp van de Azure Portal](./howto-manage-firewall-using-portal.md)
* [Azure Database for MySQL firewall regels maken en beheren met Azure CLI](./howto-manage-firewall-using-cli.md)
* [Omleiding configureren met Azure Database for MySQL](./howto-redirection.md)
