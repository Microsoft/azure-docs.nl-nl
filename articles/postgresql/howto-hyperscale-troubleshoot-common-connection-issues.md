---
title: Problemen met verbindingen oplossen-grootschalige (Citus)-Azure Database for PostgreSQL
description: Meer informatie over het oplossen van verbindings problemen met Azure Database for PostgreSQL-grootschalige (Citus)
keywords: postgresql-verbinding, connection string, connectiviteits problemen, tijdelijke fout, verbindings fout
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/8/2019
ms.openlocfilehash: e1c6825820ae943d10157279dfe93922a7521b75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91295614"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Verbindings problemen met Azure Database for PostgreSQL-grootschalige (Citus) oplossen

Verbindings problemen kunnen verschillende oorzaken hebben, zoals:

* Firewallinstellingen
* Time-out van verbinding
* Onjuiste aanmeldings gegevens
* Verbindings limiet bereikt voor Server groep
* Problemen met de infra structuur van de service
* Onderhoud van de service
* Het coördinator knooppunt voert een failover uit naar nieuwe hardware

Over het algemeen kunnen verbindings problemen met grootschalige (Citus) als volgt worden geclassificeerd:

* Tijdelijke fouten (kort geduurd of tijdelijk)
* Permanente of niet-tijdelijke fouten (fouten die regel matig worden herhaald)

## <a name="troubleshoot-transient-errors"></a>Tijdelijke fouten oplossen

Er zijn een aantal redenen om tijdelijke fouten te voor komen. Het meest voorkomende systeem onderhoud, fout met hardware of software en vCore upgrades van het coördinator knooppunt.

Het inschakelen van grootschalige-knoop punten (Citus) met hoge Beschik baarheid kan deze typen problemen automatisch oplossen. Uw toepassing moet echter nog steeds worden voor bereid om de verbinding kort te verliezen. Ook kunnen andere gebeurtenissen langer duren om te beperken, bijvoorbeeld wanneer een grote trans actie een langlopend herstel veroorzaakt.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Stappen voor het oplossen van problemen met de tijdelijke verbinding

1. Controleer het [Microsoft Azure-service dashboard](https://azure.microsoft.com/status) voor eventuele bekende storingen die zijn opgetreden tijdens het moment waarop de toepassing fouten rapporteert.
2. Toepassingen die verbinding maken met een Cloud service zoals grootschalige (Citus), moeten tijdelijke fouten verwachten en op de juiste wijze reageren. Zo moeten toepassingen proberen logica te implementeren om deze fouten af te handelen in plaats van ze te halen als toepassings fouten aan gebruikers.
3. Omdat de Server groep de resource limieten nadert, kunnen fouten lijken op problemen met de tijdelijke verbinding. Het verg Roten van het RAM-geheugen of het toevoegen van werk knooppunten en herverdelings gegevens kan helpen.
4. Als verbindings problemen blijven bestaan of langer dan 60 seconden of vaker dan één keer per dag optreden, moet u een Azure-ondersteunings aanvraag indienen door **ondersteuning** te selecteren op de [ondersteunings](https://azure.microsoft.com/support/options) site van Azure.

## <a name="troubleshoot-persistent-errors"></a>Problemen met permanente fouten oplossen

Als de toepassing permanent geen verbinding kan maken met grootschalige (Citus), zijn de meest voorkomende oorzaken voor een onjuiste firewall configuratie of een gebruikers fout.

* Firewall configuratie van coördinator knooppunt: Controleer of de grootschalige-Server firewall (Citus) is geconfigureerd voor het toestaan van verbindingen van uw client, inclusief proxy servers en gateways.
* Firewall configuratie van de client: de firewall op uw client moet verbindingen met uw database server toestaan. Sommige firewalls vereisen niet alleen toepassing op naam, maar kunnen de IP-adressen en poorten van de server toestaan.
* Gebruikers fout: dubbel Controleer de connection string. Mogelijk hebt u niet-getypte para meters zoals de server naam. U kunt verbindings reeksen vinden voor verschillende taal frameworks en psql in de Azure Portal. Ga naar de pagina **verbindings reeksen** in de Server groep grootschalige (Citus). Houd er ook wel voor dat grootschalige-clusters (Citus) slechts één data base hebben en dat de vooraf gedefinieerde naam **Citus** is.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Stappen voor het oplossen van problemen met de permanente verbinding

1. Stel [firewall regels](howto-hyperscale-manage-firewall-using-portal.md) in om het client-IP-adres toe te staan. Stel voor tijdelijke test doeleinden alleen een firewall regel in met behulp van 0.0.0.0 als het begin-IP-adres en gebruik 255.255.255.255 als het laatste IP-adres. Met deze regel wordt de server naar alle IP-adressen geopend. Als de regel uw connectiviteits probleem oplost, verwijdert u deze en maakt u een firewall regel voor een adequaat beperkt IP-adres of adres bereik.
2. Controleer op alle firewalls tussen de client en Internet of poort 5432 is geopend voor uitgaande verbindingen.
3. Controleer uw connection string en andere Verbindings instellingen.
4. Controleer de service status in het dash board.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de concepten van [firewall regels in azure database for PostgreSQL-grootschalige (Citus)](concepts-hyperscale-firewall-rules.md)
* Zie [firewall regels beheren voor Azure database for PostgreSQL-grootschalige (Citus)](howto-hyperscale-manage-firewall-using-portal.md)
