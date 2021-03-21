---
title: Overzicht van redundante hoge Beschik baarheid van zone met Azure Database for PostgreSQL-flexibele server (preview-versie)
description: Meer informatie over de concepten van redundante hoge Beschik baarheid in zones met Azure Database for PostgreSQL-flexibele server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: c0d9b6042ae695caa73d926653f237b756bf4971
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94366720"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---flexible-server"></a>Concepten met hoge Beschik baarheid in Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

Azure Database for PostgreSQL-flexibele server biedt een configuratie met hoge Beschik baarheid met automatische failover-functionaliteit met **zone-redundante** Server implementatie. Bij een implementatie in een zone-redundante configuratie wordt door de flexibele server automatisch een stand-byreplica in een andere beschikbaarheidszone ingericht en beheerd. Met PostgreSQL streaming-replicatie worden de gegevens gerepliceerd naar de stand-by replica-server in **synchrone** modus. 

Zone redundante configuratie maakt automatische failover mogelijk met geen gegevens verlies tijdens geplande gebeurtenissen, zoals door de gebruiker geïnitieerde schaal Compute-bewerking, en ook tijdens niet-geplande gebeurtenissen, zoals onderliggende hardware-en software fouten, netwerk fouten en fouten in de beschikbaarheids zone. 

:::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="zone redundant hoge Beschik baarheid"::: 

## <a name="zone-redundant-high-availability-architecture"></a>Architectuur van redundante hoge Beschik baarheid in zone

U kunt de regio en de beschikbaarheidszone kiezen waarin u de primaire databaseserver wilt implementeren. In een andere beschikbaarheidszone wordt een stand-byreplicaserver ingericht met dezelfde configuratie als de primaire server, waaronder de rekenlaag, rekengrootte, opslaggrootte en netwerkconfiguratie. Transactie logboeken worden gerepliceerd in de synchrone modus naar de stand-by replica met behulp van PostgreSQL streaming-replicatie. Automatische back-ups worden periodiek uitgevoerd vanaf de primaire database server, terwijl de transactie logboeken continu worden gearchiveerd naar de back-upopslag van de stand-by replica. 

De status van de configuratie met hoge Beschik baarheid wordt voortdurend bewaakt en gerapporteerd op de portal. De zone redundante hoge Beschik baarheid wordt hieronder weer gegeven:

| **Status** | **Beschrijving** |
| ------- | ------ |
| <b> Initialiseren | In het proces van het maken van een nieuwe stand-by-server |
| <b> Gegevens repliceren | Nadat de stand-by is gemaakt, wordt deze opgeteld bij de primaire. |
| <b> Blijft | De replicatie heeft een stabiele status en is in orde. |
| <b> Failover uitvoeren | De database server is bezig met het uitvoeren van een failover naar de stand-by. |
| <b> Stand-by verwijderen | In het proces voor het verwijderen van de stand-by-server. | 
| <b> Niet ingeschakeld | Zone redundante hoge Beschik baarheid is niet ingeschakeld.  |

## <a name="steady-state-operations"></a>Bewerkingen met stabiele status

PostgreSQL-client toepassingen zijn verbonden met de primaire server met behulp van de naam van de DB-server. Lees bewerkingen van toepassingen worden rechtstreeks vanaf de primaire server bediend, terwijl door voeringen en schrijf bewerkingen alleen worden bevestigd aan de toepassing nadat de gegevens zijn opgeslagen op de primaire server en de stand-by replica. Als gevolg van deze extra round-trip vereiste kunnen toepassingen verhoogde latentie voor schrijf bewerkingen en door voeringen verwachten. U kunt de status van de hoge Beschik baarheid in de portal bewaken.

:::image type="content" source="./media/business-continuity/concepts-high-availability-steady-state.png" alt-text="zone redundante hoge Beschik baarheid-constante status"::: 

1. Clients maken verbinding met de flexibele server en voeren schrijf bewerkingen uit.
2. Wijzigingen worden gerepliceerd naar de stand-by-site.
3. Primaire ontvangst bevestiging.
4. Schrijf bewerkingen/door voeringen worden bevestigd.

## <a name="failover-process---planned-downtimes"></a>Failover-proces-geplande uitval tijd

Geplande downtime-gebeurtenissen zijn onder andere Azure geplande periodieke software-updates en secundaire versie-upgrades. Wanneer deze bewerkingen worden geconfigureerd in hoge Beschik baarheid, worden deze eerst toegepast op de stand-by replica terwijl de toepassingen toegang blijven houden tot de primaire server. Zodra de stand-by replica is bijgewerkt, worden primaire server verbindingen leeg gemaakt en wordt er een failover geactiveerd waarmee de stand-by replica als primair wordt geactiveerd met dezelfde database server naam. Client toepassingen moeten opnieuw verbinding maken met dezelfde database server naam als de nieuwe primaire server en de bewerkingen kunnen hervatten. Er wordt een nieuwe stand-by-server in dezelfde zone gemaakt als de oude primaire. 

Voor andere door de gebruiker gestarte bewerkingen, zoals schalen of schalen, worden de wijzigingen eerst toegepast op de stand-by, gevolgd door de primaire. Op dit moment worden er geen failover van de verbindingen naar de stand-by uitgevoerd en wordt er daarom uitval tijd in rekening gebracht tijdens het uitvoeren van de bewerking op de primaire server.

### <a name="reducing-planned-downtime-with-managed-maintenance-window"></a>Geplande uitval tijd beperken met het onderhouds venster

 U kunt door Azure geïnitieerde onderhouds activiteiten plannen door een periode van 30 minuten te kiezen op een dag van uw voor keur, waarbij de activiteiten op de data bases naar verwachting laag zijn. Onderhouds taken van Azure, zoals het bijwerken van patches of secundaire versies, zouden tijdens dat venster optreden.  Voor flexibele servers die zijn geconfigureerd met maximale Beschik baarheid, worden deze onderhouds activiteiten eerst uitgevoerd op de stand-by replica en vervolgens geactiveerd. Toepassingen maken vervolgens opnieuw verbinding met de nieuwe primaire server en hervatten hun bewerkingen terwijl er een nieuwe stand-by wordt ingericht.

## <a name="failover-process---unplanned-downtimes"></a>Failover-proces-ongeplande downtime

Ongeplande uitval omvat software fouten of infrastructuur onderdeel fouten die de beschik baarheid van de data base beïnvloeden. Als de server niet beschik baarheid wordt gedetecteerd door het bewakings systeem, is de replicatie naar de stand-by replica ernstig en is de stand-by replica geactiveerd voor de primaire database server. Clients kunnen opnieuw verbinding maken met de database server met behulp van dezelfde connection string en hun bewerkingen hervatten. De verwachte failover-tijd duurt 60 120s. Afhankelijk van de activiteit op de primaire database server op het moment van de failover, zoals grote trans acties en herstel tijd, kan de failover echter langer duren.

:::image type="content" source="./media/business-continuity/concepts-high-availability-failover-state.png" alt-text="zone redundante hoge Beschik baarheid-failover"::: 

1. De primaire database server is niet beschikbaar en de clients hebben geen verbinding meer met de data base. 
2. De stand-by-server wordt geactiveerd om de nieuwe primaire server te worden. De client maakt verbinding met de nieuwe primaire server met behulp van dezelfde connection string. Het hebben van de client toepassing in dezelfde zone als de primaire database server vermindert de latentie en verbetert de prestaties.
3. De stand-by-server wordt in dezelfde zone gemaakt als de oude primaire server en de streaming-replicatie wordt gestart. 
4. Zodra de replicatie van de stationaire toestand tot stand is gebracht, worden de door Voer en schrijf bewerkingen van de client bevestigd nadat de gegevens op beide sites zijn bewaard.

## <a name="point-in-time-restore"></a>Terugzetten naar eerder tijdstip 

Flexibele servers die zijn geconfigureerd met maximale Beschik baarheid, repliceren gegevens in realtime naar de stand-by-server om deze up-to-date te houden. Eventuele gebruikers fouten op de primaire server, zoals een onbedoelde verwijdering van een tabel of onjuiste gegevens updates, worden goed gerepliceerd naar de stand-by replica. U kunt dus geen stand-by gebruiken om dergelijke logische fouten te herstellen. Als u dergelijke fouten wilt herstellen, moet u herstel vanuit een tijdstip uitvoeren vanuit een back-up.  Met de flexibele functionaliteit voor het terugzetten van de server kunt u de tijd herstellen voordat de fout optrad. Voor data bases die zijn geconfigureerd met maximale Beschik baarheid, wordt een nieuwe database server teruggezet als een flexibele server met één zone met een door de gebruiker opgegeven naam. Vervolgens kunt u het object van de database server exporteren en importeren naar uw productie database server. En als u uw database server wilt klonen voor test-en ontwikkelings doeleinden of als u voor andere doel einden wilt herstellen, kunt u herstel op tijdstippen uitvoeren.

## <a name="zone-redundant-high-availability---features"></a>Zone redundante hoge Beschik baarheid-functies

-   De stand-by replica wordt in een exacte VM-configuratie geïmplementeerd, ook als de primaire server, waaronder vCores, opslag, netwerk instellingen (VNET, firewall), enzovoort.

-   De mogelijkheid om hoge Beschik baarheid voor een bestaande database server toe te voegen.

-   Mogelijkheid om de stand-by replica te verwijderen door hoge Beschik baarheid uit te scha kelen.

-   De mogelijkheid om uw beschikbaarheids zone te kiezen voor uw primaire database server.

-   De mogelijkheid om zowel primaire als standby-database servers te stoppen, te starten en opnieuw op te starten.

-   Automatische back-ups worden uitgevoerd vanaf de primaire database server en opgeslagen in een zone redundante opslag.

-   Clients maken altijd verbinding met de primaire database server.

-   De mogelijkheid om de server opnieuw op te starten om alle para meters voor de statische server op te halen.
  
-   Periodieke onderhouds activiteiten, zoals upgrades van de secundaire versie, worden eerst op de stand-by gezet en er wordt een failover van de service uitgevoerd om de downtime te verminderen.  

## <a name="zone-redundant-high-availability---limitations"></a>Zone redundante hoge Beschik baarheid-beperkingen

-   Hoge Beschik baarheid wordt niet ondersteund met een burstive Compute-laag.
-   Hoge Beschik baarheid wordt alleen ondersteund in regio's waar meerdere zones beschikbaar zijn.
-   Vanwege synchrone replicatie naar een andere beschikbaarheids zone kunnen toepassingen verhoogde schrijf-en doorvoer latentie ervaren.

-   De stand-by replica kan niet worden gebruikt voor lees query's.

-   Afhankelijk van de werk belasting en activiteit op de primaire server, kan het failoverproces langer dan 120 seconden duren.

-   Wanneer de primaire database server opnieuw wordt opgestart, wordt de stand-by replica ook opnieuw gestart. 

-   Het configureren van aanvullende Lees replica's wordt niet ondersteund.

-   Het configureren van door de klant geïnitieerde beheer taken kan niet worden gepland tijdens het beheerde onderhouds venster.

-   Geplande gebeurtenissen, zoals schaalberekening en schaalopslag, worden eerst uitgevoerd op de stand-byserver en vervolgens op de primaire server. De server heeft geen failover voor deze geplande bewerkingen. 

-  Als logische decodering of logische replicatie is geconfigureerd met een door HA geconfigureerde flexibele server, in het geval van een failover naar de stand-by-server, worden de logische replicatie sleuven niet naar de stand-by-server gekopieerd.  

## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over [bedrijfs continuïteit](./concepts-business-continuity.md)
-   Meer informatie over het [beheren van hoge Beschik baarheid](./how-to-manage-high-availability-portal.md)
-   Meer informatie over [back-up en herstel](./concepts-backup-restore.md)