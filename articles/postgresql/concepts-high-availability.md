---
title: Hoge Beschik baarheid-Azure Database for PostgreSQL-één server
description: In dit artikel vindt u informatie over hoge Beschik baarheid in Azure Database for PostgreSQL-één server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/15/2020
ms.openlocfilehash: aa9f38b2cefa60a0c3341c1317cf45fbcb735301
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92485440"
---
# <a name="high-availability-in-azure-database-for-postgresql--single-server"></a>Hoge Beschik baarheid in Azure Database for PostgreSQL-één server
De Azure Database for PostgreSQL-service met één server biedt een gegarandeerd hoog niveau van Beschik baarheid met de SLA (financieel ondersteunde service level agreement) van [99,99%](https://azure.microsoft.com/support/legal/sla/postgresql) uptime. Azure Database for PostgreSQL biedt een hoge Beschik baarheid tijdens geplande gebeurtenissen, zoals de initated van de gebruiker en ook wanneer niet-geplande gebeurtenissen, zoals onderliggende hardware, software of netwerk fouten, optreden. Azure Database for PostgreSQL kan snel van de meeste kritieke omstandigheden worden hersteld, waardoor er bijna geen toepassings tijd meer is bij het gebruik van deze service.

Azure Database for PostgreSQL is geschikt voor het uitvoeren van essentiële data bases die veel tijd in beslag nemen. De service is gebouwd op basis van Azure-architectuur en biedt de mogelijkheid tot hoge Beschik baarheid, redundantie en flexibiliteit om de uitval tijd van de data base van geplande en ongeplande storingen te beperken, zonder dat u extra onderdelen hoeft te configureren. 

## <a name="components-in-azure-database-for-postgresql--single-server"></a>Onderdelen in Azure Database for PostgreSQL-één server

| **Onderdeel** | **Beschrijving**|
| ------------ | ----------- |
| <b>PostgreSQL-database server | Azure Database for PostgreSQL biedt beveiliging, isolatie, bron beveiligingen en de mogelijkheid om snel opnieuw op te starten voor database servers. Deze mogelijkheden vergemakkelijken bewerkingen zoals schalen en database server herstel na een onderbreking in enkele seconden. <br/> Gegevens wijzigingen in de database server worden meestal uitgevoerd in de context van een database transactie. Alle database wijzigingen worden synchroon vastgelegd in de vorm van write-Ahead Logboeken (WAL) op Azure Storage, dat is gekoppeld aan de database server. Tijdens het database [controlepunt](https://www.postgresql.org/docs/11/sql-checkpoint.html) proces worden gegevens pagina's van het database server geheugen ook naar de opslag leeg gemaakt. |
| <b>Externe opslag | Alle PostgreSQL fysieke gegevens bestanden en WAL-bestanden worden opgeslagen op Azure Storage, die is ontworpen voor het opslaan van drie kopieën van gegevens binnen een regio om gegevens redundantie, Beschik baarheid en betrouw baarheid te garanderen. De opslaglaag is ook onafhankelijk van de database server. Dit kan binnen een paar seconden worden losgekoppeld van een mislukte database server en opnieuw worden gekoppeld aan een nieuwe database server. Azure Storage doorlopend monitors voor eventuele opslag fouten. Als een blok beschadiging wordt gedetecteerd, wordt dit automatisch opgelost door een nieuwe opslag kopie te instantiëren. |
| <b>Gateway | De gateway fungeert als een database proxy, stuurt alle client verbindingen naar de database server. |

## <a name="planned-downtime-mitigation"></a>Geplande downtime van uitval tijd
Azure Database for PostgreSQL is ontworpen om hoge Beschik baarheid te bieden tijdens geplande downtime. 

:::image type="content" source="./media/concepts-high-availability/azure-postgresql-elastic-scaling.png" alt-text="weer gave van elastisch schalen in azure PostgreSQL":::

1. Binnen enkele seconden omhoog en omlaag PostgreSQL-database servers schalen
2. Gateway die fungeert als een proxy om client te routeren, maakt verbinding met de juiste database server
3. Het schalen van opslag ruimte kan zonder uitval tijd worden uitgevoerd. Met externe opslag kunt u na de failover snel loskoppelen/opnieuw koppelen.
Hier volgen enkele geplande onderhouds scenario's:

| **Scenario** | **Beschrijving**|
| ------------ | ----------- |
| <b>Berekenings schaal omhoog/omlaag | Wanneer de gebruiker de bewerking omhoog/omlaag Compute Scale uitvoert, wordt een nieuwe database server ingericht met behulp van de geschaalde Compute-configuratie. In de oude database server mogen actieve controle punten worden voltooid, worden de client verbindingen geleegd, worden niet-doorgevoerde trans acties geannuleerd en vervolgens afgesloten. De opslag wordt vervolgens losgekoppeld van de oude database server en gekoppeld aan de nieuwe database server. Wanneer de client toepassing de verbinding probeert te maken of probeert een nieuwe verbinding tot stand te brengen, stuurt de gateway de verbindings aanvraag door naar de nieuwe database server.|
| <b>Opslag ruimte omhoog schalen | Het omhoog schalen van de opslag is een online bewerking en de database server wordt niet onderbroken.|
| <b>Nieuwe software-implementatie (Azure) | Nieuwe functies implementatie of fout oplossingen worden automatisch uitgevoerd als onderdeel van het geplande onderhoud van de service. Raadpleeg de [documentatie](./concepts-monitoring.md#planned-maintenance-notification)voor meer informatie en Controleer ook de [Portal](https://aka.ms/servicehealthpm).|
| <b>Secundaire versie-upgrades | Azure Database for PostgreSQL worden database servers automatisch aan de secundaire versie door Azure door berekend. Deze treedt op als onderdeel van het geplande onderhoud van de service. Dit leidt tot een korte downtime in seconden en de database server wordt automatisch opnieuw opgestart met de nieuwe secundaire versie. Raadpleeg de [documentatie](./concepts-monitoring.md#planned-maintenance-notification)voor meer informatie en Controleer ook de [Portal](https://aka.ms/servicehealthpm).|


##  <a name="unplanned-downtime-mitigation"></a>Ongeplande downtime-beperking

Ongeplande uitval tijd kan optreden als gevolg van onvoorziene storingen, waaronder onderliggende hardwarestoringen, netwerk problemen en software fouten. Als de database server onverwacht uitvalt, wordt er in een paar seconden automatisch een nieuwe database server ingericht. De externe opslag wordt automatisch gekoppeld aan de nieuwe database server. De PostgreSQL-engine voert de herstel bewerking uit met WAL en database bestanden, en opent de database server om clients toe te staan verbinding te maken. Niet-doorgevoerde trans acties gaan verloren en moeten opnieuw worden geprobeerd door de toepassing. Hoewel een ongeplande uitval tijd niet kan worden vermeden, Azure Database for PostgreSQL de uitval tijd verminderen door automatisch herstel bewerkingen uit te voeren op de database server en opslag lagen zonder menselijke tussen komst. 


:::image type="content" source="./media/concepts-high-availability/azure-postgresql-built-in-high-availability.png" alt-text="weer gave van hoge Beschik baarheid in azure PostgreSQL":::

1. Azure PostgreSQL-servers met snelle schaal mogelijkheden.
2. Gateway die fungeert als een proxy voor het routeren van client verbindingen met de juiste database server
3. Azure Storage met drie kopieën voor betrouw baarheid, Beschik baarheid en redundantie.
4. Met externe opslag kunt u ook snel loskoppelen/opnieuw koppelen na de failover van de server.
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Ongeplande downtime: fout scenario's en service herstel
Hier volgen enkele fout scenario's en hoe Azure Database for PostgreSQL automatisch herstelt:

| **Scenario** | **Automatisch herstel** |
| ---------- | ---------- |
| <B>Fout in database server | Als de database server niet beschikbaar is vanwege een bepaalde onderliggende hardwarefout, worden actieve verbindingen verwijderd en worden eventuele trans acties van de vlucht afgebroken. Er wordt automatisch een nieuwe database server geïmplementeerd en de externe gegevens opslag is gekoppeld aan de nieuwe database server. Nadat het herstel van de data base is voltooid, kunnen clients via de gateway verbinding maken met de nieuwe database server. <br /> <br /> De herstel tijd (RTO) is afhankelijk van verschillende factoren, waaronder de activiteit op het moment van de fout, zoals grote trans acties en de hoeveelheid herstel die moet worden uitgevoerd tijdens het opstart proces van de database server. <br /> <br /> Toepassingen die gebruikmaken van de PostgreSQL-data bases, moeten worden gebouwd op een manier die ze detecteert en de verwijderde verbindingen en mislukte trans acties opnieuw proberen.  Wanneer de toepassing opnieuw probeert, wordt de verbinding met de zojuist gemaakte database server op transparante wijze door de gateway omgeleid. |
| <B>Opslag fout | Toepassingen zien geen gevolgen voor eventuele problemen met betrekking tot de opslag, zoals een schijf storing of een fysieke blok beschadiging. Wanneer de gegevens worden opgeslagen in 3 kopieën, wordt de kopie van de gegevens geleverd door de overgebleven opslag. Blok beschadigingen worden automatisch gecorrigeerd. Als er een kopie van gegevens verloren is gegaan, wordt er automatisch een nieuwe kopie van de gegevens gemaakt. |

Hier volgen enkele fout scenario's waarvoor gebruikers actie moet worden hersteld:

| **Scenario** | **Herstel plan** |
| ---------- | ---------- |
| <b> Regio fout | Uitval van een regio is een zeldzame gebeurtenis. Als u echter beveiliging van een regio fout nodig hebt, kunt u een of meer Lees replica's in andere regio's configureren voor herstel na nood gevallen (DR). (Zie [dit artikel](./howto-read-replicas-portal.md) over het maken en beheren van Lees replica's voor meer informatie). In het geval van een storing op regio niveau kunt u de Lees replica die is geconfigureerd voor de andere regio hand matig promo veren tot de productie database server. |
| <b> Logische/gebruikers fouten | Herstel van gebruikers fouten, zoals het per ongeluk verwijderen van tabellen of onjuiste bijgewerkte gegevens, omvat het uitvoeren van een herstel naar een bepaald [tijdstip](./concepts-backup.md) (PITR), door de gegevens te herstellen en te herstellen tot het moment dat de fout zich voordeed.<br> <br>  Als u alleen een subset van data bases of specifieke tabellen wilt herstellen in plaats van alle data bases in de database server, kunt u de database server herstellen in een nieuw exemplaar, de tabel (len) exporteren via [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html)en vervolgens [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) gebruiken om die tabellen te herstellen in uw data base. |



## <a name="summary"></a>Samenvatting

Azure Database for PostgreSQL biedt de mogelijkheid om snel opnieuw op te starten van database servers, redundante opslag en efficiënte route ring vanaf de gateway. Voor aanvullende gegevens beveiliging kunt u back-ups naar geo-replicatie configureren en ook een of meer Lees replica's in andere regio's implementeren. Met inherente mogelijkheden voor hoge Beschik baarheid beschermt Azure Database for PostgreSQL uw data bases tegen de meest voorkomende storingen en biedt deze een toonaangevende, door de financiën ondersteund [99,99% van de sla voor uptime](https://azure.microsoft.com/support/legal/sla/postgresql). Al deze mogelijkheden voor Beschik baarheid en betrouw baarheid zorgen dat Azure het ideale platform is voor het uitvoeren van uw bedrijfs kritieke toepassingen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure-regio's](../availability-zones/az-overview.md)
- Meer informatie over het [verwerken van tijdelijke connectiviteits fouten](concepts-connectivity.md)
- Meer informatie over het [repliceren van uw gegevens met replica's](howto-read-replicas-portal.md)