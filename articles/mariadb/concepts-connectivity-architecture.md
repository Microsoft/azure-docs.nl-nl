---
title: Connectiviteits architectuur-Azure Database for MariaDB
description: Beschrijft de connectiviteits architectuur voor uw Azure Database for MariaDB-server.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/11/2021
ms.openlocfilehash: 50aaae9e71ac9de366ee4db1981e633491094946
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199960"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Connectiviteits architectuur in Azure Database for MariaDB
In dit artikel wordt uitgelegd wat de Azure Database for MariaDB connectiviteits architectuur is en hoe het verkeer wordt omgeleid naar uw Azure Database for MariaDB exemplaar van clients, zowel binnen als buiten Azure.

## <a name="connectivity-architecture"></a>Connectiviteitsarchitectuur

De verbinding met uw Azure Database for MariaDB wordt tot stand gebracht via een gateway die verantwoordelijk is voor de route ring van binnenkomende verbindingen naar de fysieke locatie van uw server in onze clusters. In het volgende diagram ziet u de verkeers stroom.

![Overzicht van de connectiviteits architectuur](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)


Wanneer de client verbinding maakt met de data base, wordt het connection string naar de server omgezet in het IP-adres van de gateway. De gateway luistert naar het IP-adres op poort 3306. Binnen het database cluster wordt verkeer doorgestuurd naar de juiste Azure Database for MariaDB. Daarom is het nodig om verbinding te maken met uw server, bijvoorbeeld van bedrijfs netwerken, om de firewall aan de **client zijde te openen, zodat uitgaand verkeer de gateways kan bereiken**. Hieronder vindt u een volledige lijst met de IP-adressen die worden gebruikt door onze gateways per regio.

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>IP-adressen van Azure Database for MariaDB gateway

De Gateway Service wordt gehost op de groep stateless Compute-knoop punten die achter een IP-adres staan, wat uw client voor het eerst zou bereiken wanneer er verbinding wordt gemaakt met een Azure Database for MariaDB-server. 

Als onderdeel van het onderhoud van de onderhouds beurt zullen wij Compute-hardware die als host fungeert voor de gateways periodiek vernieuwen om ervoor te zorgen dat we de veiligste en beste ervaring bieden. Wanneer de gateway-hardware wordt vernieuwd, wordt eerst een nieuwe ring van de reken knooppunten gebouwd. Deze nieuwe ring verzendt het verkeer voor alle nieuw gemaakte Azure Database for MariaDB servers en er is een ander IP-adres van oudere Gateway ringen in dezelfde regio om het verkeer te onderscheiden. Zodra de nieuwe ring volledig functioneel is, zijn de oudere Gateway hardware die bestaande servers levert, gepland voor het buiten gebruik stellen. Voordat u een gateway-hardware buiten gebruik stelt, worden klanten die hun servers uitvoeren en verbinding maken met oudere Gateway ringen via e-mail en in de Azure Portal, drie maanden vooraf gewaarschuwd voordat ze buiten gebruik worden gesteld. Het buiten gebruik stellen van gateways kan invloed hebben op de connectiviteit met uw servers als 

* U kunt de IP-adressen van de gateway in de connection string van uw toepassing. Het wordt **niet aanbevolen**. Gebruik Fully Qualified Domain Name (FQDN) van uw server in de indeling <servername> . mariadb.database.Azure.com, in de Connection String voor uw toepassing. 
* U werkt de nieuwere gateway-IP-adressen in de client-side firewall niet bij zodat uitgaand verkeer de nieuwe gateway ringen kan bereiken.

De volgende tabel geeft een lijst van de IP-adressen van de gateway van de Azure Database for MariaDB gateway voor alle gegevens regio's. De meest recente informatie van de gateway-IP-adressen voor elke regio wordt in de onderstaande tabel bewaard. In de onderstaande tabel vertegenwoordigen de kolommen de volgende:

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
| Duitsland - noord | 51.116.56.0 | |
| Duitsland-noord Oost | 51.5.144.179  | | |
| Duitsland - west-centraal | 51.116.152.0 | |
| India - centraal | 104.211.96.159     | | |
| India - zuid | 104.211.224.146  | | |
| India - west | 104.211.160.80    | | |
| Japan - oost | 40.79.192.23, 40.79.184.8 | 13.78.61.196 | |
| Japan - west | 191.238.68.11, 40.74.96.6, 40.74.96.7     | 104.214.148.156 | |
| Korea - centraal | 52.231.17.13   | 52.231.32.42 | |
| Korea - zuid | 52.231.145.3     | 52.231.200.86 | |
| VS - noord-centraal | 52.162.104.35, 52.162.104.36    | 23.96.178.199 | |
| Europa - noord | 52.138.224.6, 52.138.224.7  | 40.113.93.91 |191.235.193.75 |
| Zuid-Afrika - noord  | 102.133.152.0    | | |
| Zuid-Afrika - west | 102.133.24.0   | | |
| VS - zuid-centraal |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Azië - zuidoost | 40.78.233.2, 23.98.80.12     | 104.43.15.0 | |
| Zwitserland - noord | 51.107.56.0 ||
| Zwitserland - west | 51.107.152.0| ||
| UAE - centraal | 20.37.72.64  | | |
| VAE - noord | 65.52.248.0    | | |
| Verenigd Koninkrijk Zuid | 51.140.184.11   | | |
| Verenigd Koninkrijk West | 51.141.8.11  | | |
| VS - west-centraal | 13.78.145.25     | | |
| Europa -west |13.69.105.208, 104.40.169.187 | 40.68.37.158 | 191.237.232.75 |
| VS - west |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| VS - west 2 | 13.66.226.202  | | |
||||

## <a name="connection-redirection"></a>Verbindings omleiding

Azure Database for MariaDB ondersteunt een extra verbindings beleid, **omleiding**, waarmee de netwerk latentie tussen client toepassingen en MariaDB-servers kan worden verminderd. Als deze functie is ingesteld, wordt na de eerste TCP-sessie naar de Azure Database for MariaDB-server het back-end-adres van het knoop punt dat als host fungeert voor de MariaDB-server naar de client geretourneerd. Daarna stroomt alle volgende pakketten rechtstreeks naar de server, zodat de gateway wordt omzeild. Als pakketten rechtstreeks naar de server stromen, hebben latentie en door Voer betere prestaties.

Deze functie wordt ondersteund in Azure Database for MariaDB-servers met Engine versie 10,2 en 10,3.

Ondersteuning voor omleiding is beschikbaar in de PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -uitbrei ding, ontwikkeld door micro soft en is beschikbaar op [PECL](https://pecl.php.net/package/mysqlnd_azure). Zie het artikel [omleiding configureren](./howto-redirection.md) voor meer informatie over het gebruik van omleiding in uw toepassingen.

> [!IMPORTANT]
> Ondersteuning voor omleiding in de PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -uitbrei ding is momenteel als preview-versie beschikbaar.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="what-you-need-to-know-about-this-planned-maintenance"></a>Wat u moet weten over dit geplande onderhoud?
Dit is alleen een DNS-wijziging die transparant maakt voor clients. Terwijl het IP-adres voor FQDN wordt gewijzigd op de DNS-server, wordt de lokale DNS-cache vernieuwd binnen vijf minuten en wordt deze automatisch uitgevoerd door de besturings systemen. Nadat de lokale DNS-vernieuwing is uitgevoerd, worden alle nieuwe verbindingen verbinding gemaakt met het nieuwe IP-adres. alle bestaande verbindingen blijven verbonden met het oude IP-adres zonder onderbreking totdat de oude IP-adressen volledig buiten gebruik zijn gesteld. Het oude IP-adres neemt ongeveer drie tot vier weken in beslag voordat u het buiten gebruik stelt. Daarom mag het geen effect hebben op de client toepassingen.

### <a name="what-are-we-decommissioning"></a>Wat is het buiten gebruik stellen?
Alleen gateway knooppunten worden buiten gebruik gesteld. Wanneer gebruikers verbinding maken met hun servers, is de eerste stop van de verbinding naar het gateway knooppunt voordat de verbinding wordt doorgestuurd naar de server. We ontmantelen oude gateway ringen (niet tenants die de server uitvoert) Raadpleeg de [connectiviteits architectuur](#connectivity-architecture) voor meer informatie.

### <a name="how-can-you-validate-if-your-connections-are-going-to-old-gateway-nodes-or-new-gateway-nodes"></a>Hoe kunt u controleren of uw verbindingen naar oude gateway knooppunten of nieuwe gateway knooppunten gaan?
Ping de FQDN van de server, bijvoorbeeld  ``ping xxx.mariadb.database.azure.com`` . Als het geretourneerde IP-adres een van de IP-adressen is die in het bovenstaande document worden weer gegeven met de naam van de gateway (uit het buiten gebruik stellen), betekent dit dat uw verbinding via de oude gateway gaat. Daarentegen, als het geretourneerde IP-adres een van de IP-adressen is die worden weer gegeven onder gateway-ip's, betekent dit dat uw verbinding via de nieuwe gateway gaat.

U kunt ook testen door [PSPing](https://docs.microsoft.com/sysinternals/downloads/psping) of TCPPing van de database server van uw client toepassing met poort 3306 en ervoor te zorgen dat het GERETOURNEERDe IP-adres niet de IP-adressen uit de buiten gebruik stelt.

### <a name="how-do-i-know-when-the-maintenance-is-over-and-will-i-get-another-notification-when-old-ip-addresses-are-decommissioned"></a>Hoe kan ik weet wanneer het onderhoud wordt uitgevoerd en krijgt hij een andere melding wanneer oude IP-adressen buiten gebruik worden gesteld?
U ontvangt een e-mail bericht waarin u wordt gewaarschuwd wanneer het onderhouds werk wordt gestart. Het onderhoud kan tot één maand duren, afhankelijk van het aantal servers dat in al regio's moet worden gemigreerd. Bereid uw client voor om verbinding te maken met de database server met behulp van de FQDN of gebruik het nieuwe IP-adres uit de bovenstaande tabel. 

### <a name="what-do-i-do-if-my-client-applications-are-still-connecting-to-old-gateway-server-"></a>Wat moet ik doen als mijn client toepassingen nog steeds verbinding maken met de oude Gateway server?
Dit geeft aan dat uw toepassingen verbinding maken met de server met een statisch IP-adres in plaats van een FQDN-naam. Controleer de instellingen voor verbindings reeksen en groepsgewijze verbindingen, de instelling AKS of zelfs in de bron code.

### <a name="is-there-any-impact-for-my-application-connections"></a>Zijn er gevolgen voor de verbindingen van mijn toepassing?
Dit onderhoud is alleen een DNS-wijziging, waardoor het transparant is voor de client. Zodra de DNS-cache is vernieuwd in de client (automatisch uitgevoerd door het besturings systeem), wordt alle nieuwe verbinding gemaakt met het nieuwe IP-adres en blijft de bestaande verbinding goed werken totdat het oude IP-adres volledig uit bedrijf is genomen, meestal een aantal weken later. En de logica voor opnieuw proberen is niet vereist voor deze aanvraag, maar het is wel goed om te zien of de toepassing een nieuwe pogings logica heeft geconfigureerd. Gebruik FQDN om verbinding te maken met de database server of schakel de nieuwe gateway-IP-adressen in het connection string van uw toepassing in.
Deze onderhouds bewerking verwijdert de bestaande verbindingen niet. Hierdoor worden alleen de nieuwe verbindings aanvragen naar de nieuwe gateway ring gestuurd.

### <a name="can-i-request-for-a-specific-time-window-for-the-maintenance"></a>Kan ik een specifiek tijd venster voor het onderhoud aanvragen? 
Omdat de migratie transparant moet zijn en geen invloed heeft op de connectiviteit van de klant, verwachten we dat er geen problemen zijn voor de meeste gebruikers. Controleer uw toepassing proactief en zorg ervoor dat u FQDN gebruikt om verbinding te maken met de database server of schakel de nieuwe gateway-IP-adressen in uw toepassing in connection string.

### <a name="i-am-using-private-link-will-my-connections-get-affected"></a>Ik gebruik een persoonlijke koppeling, mijn verbindingen worden beïnvloed?
Nee, dit is een gateway-hardware buiten gebruik stellen en heeft geen relatie met persoonlijke koppelingen of privé-IP-adressen, maar heeft alleen invloed op open bare IP-adressen die worden vermeld onder de uit bedrijf nemende IP-adressen.


## <a name="next-steps"></a>Volgende stappen

* [Azure Database for MariaDB firewall regels maken en beheren met behulp van de Azure Portal](./howto-manage-firewall-portal.md)
* [Azure Database for MariaDB firewall regels maken en beheren met Azure CLI](./howto-manage-firewall-cli.md)
