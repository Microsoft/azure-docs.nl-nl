---
title: Problemen met verbindingen oplossen-Azure Database for PostgreSQL-één server
description: Meer informatie over het oplossen van verbindings problemen met Azure Database for PostgreSQL-één server.
keywords: postgresql-verbinding, connection string, connectiviteits problemen, tijdelijke fout, verbindings fout
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: bff930153dc8941fbfe561edf963d5b1c1e7811f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96014615"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>Verbindings problemen met Azure Database for PostgreSQL-één server oplossen

Verbindings problemen kunnen worden veroorzaakt door diverse dingen, waaronder:

* Firewallinstellingen
* Time-out van verbinding
* Onjuiste aanmeldings gegevens
* De maximum limiet is bereikt op enkele Azure Database for PostgreSQL bronnen
* Problemen met de infra structuur van de service
* Onderhoud wordt uitgevoerd in de service
* De reken toewijzing van de server wordt gewijzigd door het aantal vCores te schalen of naar een andere servicelaag te verplaatsen

Over het algemeen kunnen verbindings problemen met Azure Database for PostgreSQL als volgt worden geclassificeerd:

* Tijdelijke fouten (kort geduurd of tijdelijk)
* Permanente of niet-tijdelijke fouten (fouten die regel matig worden herhaald)

## <a name="troubleshoot-transient-errors"></a>Tijdelijke fouten oplossen

Er treden tijdelijke fouten op wanneer onderhoud wordt uitgevoerd. het systeem detecteert een fout met de hardware of software of u wijzigt de vCores of de servicelaag van de server. De Azure Database for PostgreSQL-service heeft ingebouwde hoge Beschik baarheid en is ontworpen om deze typen problemen automatisch te verhelpen. Uw toepassing verliest echter de verbinding met de server voor een korte periode van meestal minder dan 60 seconden. Sommige gebeurtenissen nemen af en toe meer tijd in beslag om te beperken, bijvoorbeeld wanneer een grote trans actie een langlopend herstel veroorzaakt.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Stappen voor het oplossen van problemen met de tijdelijke verbinding

1. Controleer het [Microsoft Azure-service dashboard](https://azure.microsoft.com/status) voor eventuele bekende storingen die zijn opgetreden tijdens de periode waarin de fouten zijn gerapporteerd door de toepassing.
2. Toepassingen die verbinding maken met een Cloud service, zoals Azure Database for PostgreSQL, moeten tijdelijke fouten verwachten en proberen logica te implementeren voor het afhandelen van deze fouten in plaats van deze te halen als toepassings fouten aan gebruikers. Bekijk de [verwerking van tijdelijke connectiviteits fouten voor Azure database for PostgreSQL](concepts-connectivity.md) voor aanbevolen procedures en ontwerp richtlijnen voor het afhandelen van tijdelijke fouten.
3. Wanneer een server de resource limieten nadert, kunnen fouten een probleem met de tijdelijke verbinding zijn. Zie de [beperkingen in azure database for PostgreSQL](concepts-limits.md).
4. Als er verbindings problemen blijven bestaan of als de duur waarvoor uw toepassing de fout ondervindt, groter is dan 60 seconden of als er meerdere exemplaren van de fout in een bepaalde dag worden weer gegeven, kunt u een Azure-ondersteunings aanvraag indienen door **ondersteuning** te selecteren op de [ondersteunings](https://azure.microsoft.com/support/options) site van Azure.

## <a name="troubleshoot-persistent-errors"></a>Problemen met permanente fouten oplossen

Als de toepassing permanent geen verbinding kan maken met Azure Database for PostgreSQL, duidt dit meestal op een probleem met een van de volgende:

* Server firewall configuratie: Zorg ervoor dat de firewall van de Azure Database for PostgreSQL-server zo is geconfigureerd dat verbindingen van uw client worden toegestaan, inclusief proxy servers en gateways.
* Firewall configuratie van de client: de firewall op uw client moet verbindingen met uw database server toestaan. IP-adressen en poorten van de server die u niet wilt toestaan en toepassings namen als PostgreSQL in sommige firewalls.
* Gebruikers fout: mogelijk hebt u een type verbindings parameter, zoals de server naam in de connection string of een ontbrekend *\@ Server* achtervoegsel in de gebruikers naam.
* Als de fout _server niet is geconfigureerd voor het toestaan van IPv6-verbindingen_, ziet u dat de laag basis geen ondersteuning biedt voor VNet-service-eind punten. U moet het micro soft. SQL-eind punt verwijderen uit het subnet dat probeert verbinding te maken met de basis server.
* Als de verbindings fout _sslmode waarde "* * *" ongeldig is wanneer SSL-ondersteuning niet is gecompileerd_ , betekent dit dat uw postgresql-client geen ondersteuning biedt voor SSL. Waarschijnlijk is de libpq aan de client zijde niet gecompileerd met de vlag---with-openssl. Probeer verbinding te maken met een PostgreSQL-client met SSL-ondersteuning. 

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Stappen voor het oplossen van problemen met de permanente verbinding

1. Stel [firewall regels](howto-manage-firewall-using-portal.md) in om het client-IP-adres toe te staan. Stel voor tijdelijke test doeleinden alleen een firewall regel in met behulp van 0.0.0.0 als het begin-IP-adres en gebruik 255.255.255.255 als het laatste IP-adres. Hiermee wordt de server op alle IP-adressen geopend. Als uw connectiviteits probleem hiermee wordt opgelost, verwijdert u deze regel en maakt u een firewall regel voor een adequaat beperkt IP-adres of adres bereik.
2. Controleer op alle firewalls tussen de client en Internet of poort 5432 is geopend voor uitgaande verbindingen.
3. Controleer uw connection string en andere Verbindings instellingen.
4. Controleer de service status in het dash board. Als u denkt dat er een regionale storing is, raadpleegt u [overzicht van bedrijfs continuïteit met Azure database for PostgreSQL](concepts-business-continuity.md) voor stappen om te herstellen naar een nieuwe regio.

## <a name="next-steps"></a>Volgende stappen

* [Verwerking van tijdelijke verbindings fouten voor Azure Database for PostgreSQL](concepts-connectivity.md)
