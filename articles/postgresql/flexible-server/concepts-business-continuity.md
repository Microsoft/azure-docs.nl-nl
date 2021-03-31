---
title: Overzicht van bedrijfs continuïteit met Azure Database for PostgreSQL-flexibele server
description: Meer informatie over de concepten van bedrijfs continuïteit met Azure Database for PostgreSQL-flexibele server
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 341d3c6f0a2d84c2b7b725959db9f7e5dd80d3ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92542961"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---flexible-server"></a>Overzicht van bedrijfs continuïteit met Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

**Bedrijfs continuïteit** in azure database for PostgreSQL-flexibele server verwijst naar de mechanismen, beleids regels en procedures die uw bedrijf in staat stellen te blijven werken in het geval van storingen, met name op de computer infrastructuur. In de meeste gevallen zal een flexibele server de verstorende gebeurtenissen uitvoeren die zich in de cloud omgeving kunnen voordoen, zodat uw toepassingen en bedrijfs processen actief blijven. Er zijn echter enkele gebeurtenissen die niet automatisch kunnen worden verwerkt, zoals:

- Gebruiker verwijdert per ongeluk een rij in een tabel of werkt deze bij.
- Aarding zorgt voor een stroom storing en tijdelijke uitschakeling van een Data Center of een beschikbaarheids zone.
- Database patches vereist voor het oplossen van een fout-of beveiligings probleem.

Flexibele server biedt functies waarmee gegevens worden beveiligd en de downtime voor uw essentiële data bases wordt gereduceerd in het geval van geplande en niet-geplande downtime-gebeurtenissen. Flexibele servers zijn gebouwd op basis van de Azure-infra structuur die al een robuuste tolerantie en beschik baarheid biedt, maar heeft een functionaliteit voor bedrijfs continuïteit die extra fout beveiliging biedt, vereisten voor de herstel tijd van adressen en de bloot stelling van gegevens verlies verminderen. Bij het ontwerpen van uw toepassingen, moet u rekening houden met de uitval tijd tolerantie: de RTO (Recovery Time objectief) en de bloot stelling van gegevens verlies. Dit is de Recovery Point Objective (RPO). Uw bedrijfs kritieke data base vereist bijvoorbeeld veel strengere uptime-vereisten ten opzichte van een test database.  

> [!IMPORTANT]
> De uptime van% service level agreement (SLA) wordt niet aangeboden tijdens de preview-versie. 

In de volgende tabel ziet u de functies van flexibele server aanbiedingen.


| **Functie** | **Beschrijving** | **Overwegingen** |
| ---------- | ----------- | ------------ |
| **Automatische back-ups** | Flexibele server voert automatisch dagelijkse back-ups van uw database bestanden uit en maakt voortdurend back-ups van transactie Logboeken. Back-ups kunnen Maxi maal 7 dagen tot 35 dagen worden bewaard. U kunt uw database server herstellen naar elk gewenst moment binnen de retentie periode van de back-up. RTO is afhankelijk van de grootte van de gegevens die moeten worden hersteld en het tijdstip waarop de logboek herstel bewerking moet worden uitgevoerd. Het kan een paar minuten tot 12 uur duren. Zie [concepten: back-up maken en herstellen](./concepts-backup-restore.md)voor meer informatie. |Back-upgegevens blijven binnen de regio. |
| **Zone-redundante hoge beschikbaarheid** | Flexibele servers kunnen worden geïmplementeerd met een zone redundante High Availability (HA)-configuratie waarbij primaire en stand-by-servers in twee verschillende beschikbaarheids zones binnen een regio worden geïmplementeerd. Deze HA-configuratie beveiligt uw data bases tegen storingen op zone niveau en helpt tijdens geplande en niet-geplande uitval tijd de uitval tijd van toepassingen te verminderen. Gegevens van de primaire server worden gerepliceerd naar de stand-by replica in synchrone modus. In het geval van een onderbreking van de primaire server wordt automatisch een failover naar de stand-by replica uitgevoerd voor de server. RTO in de meeste gevallen wordt verwacht in 60s-120s. RPO wordt verwacht nul te zijn (geen gegevens verlies). Zie [concepten-hoge Beschik baarheid](./concepts-high-availability.md)voor meer informatie. | Ondersteund in reken lagen voor algemeen gebruik en geoptimaliseerd voor geheugen. Alleen beschikbaar in regio's waar meerdere zones beschikbaar zijn. |
| **Premium-beheerde schijven** | Database bestanden worden opgeslagen in een zeer duurzame en betrouw bare, Premium beheerde opslag. Dit biedt gegevens redundantie met drie kopieën van de replica die zijn opgeslagen in een beschikbaarheids zone met automatische gegevens herstel mogelijkheden. Zie [Managed disks documentation](../../virtual-machines/managed-disks-overview.md)(Engelstalig) voor meer informatie. | Gegevens die zijn opgeslagen in een beschikbaarheids zone. |
| **Zone redundante back-up** | Flexibele server back-ups worden automatisch en veilig opgeslagen in een zone redundante opslag binnen een regio. Tijdens een storing op zone niveau waar uw server is ingericht, en als uw server niet is geconfigureerd met zone redundantie, kunt u uw data base nog steeds herstellen met behulp van het meest recente herstel punt in een andere zone. Zie [concepten: back-up maken en herstellen](./concepts-backup-restore.md)voor meer informatie.| Alleen van toepassing in regio's waar meerdere zones beschikbaar zijn.|


## <a name="planned-downtime-events"></a>Geplande downtime-gebeurtenissen
Hieronder vindt u enkele geplande onderhouds scenario's. Deze gebeurtenissen hebben doorgaans een paar minuten uitval tijd en zonder gegevens verlies.

| **Scenario** | **Proces**|
| ------------------- | ----------- | 
| <b>Compute scaling (door de gebruiker gestart)| Tijdens het schalen van de berekening kunnen actieve controle punten worden voltooid, worden de client verbindingen leeg gemaakt, worden eventuele niet-doorgevoerde trans acties geannuleerd, wordt de opslag losgekoppeld en vervolgens afgesloten. Een nieuwe flexibele server met dezelfde naam voor de database server wordt ingericht met de schaal bare Compute-configuratie. De opslag wordt vervolgens gekoppeld aan de nieuwe server en de data base wordt gestart, waardoor zo nodig herstel wordt uitgevoerd voordat client verbindingen worden geaccepteerd. |
| <b>Opslag ruimte omhoog schalen (door de gebruiker gestart) | Wanneer een bewerking voor het omhoog schalen van een opslag wordt gestart, kunnen actieve controle punten worden voltooid, worden de client verbindingen geleegd, worden eventuele niet-doorgevoerde trans acties geannuleerd en vervolgens afgesloten. De opslag wordt geschaald naar de gewenste grootte en vervolgens gekoppeld aan de nieuwe server. Er wordt zo nodig een herstel bewerking uitgevoerd voordat client verbindingen worden geaccepteerd. Houd er rekening mee dat het omlaag schalen van de opslag grootte niet wordt ondersteund. |
| <b>Nieuwe software-implementatie (door Azure gestart) | Nieuwe functies implementatie-of fout oplossingen worden automatisch uitgevoerd als onderdeel van het geplande onderhoud van de service, en u kunt plannen wanneer deze activiteiten plaatsvinden. Controleer uw [Portal](https://aka.ms/servicehealthpm)voor meer informatie. | 
| <b>Secundaire versie-upgrades (door Azure gestart) | Azure Database for PostgreSQL worden database servers automatisch aan de secundaire versie door Azure door berekend. Deze treedt op als onderdeel van het geplande onderhoud van de service. De database server wordt automatisch opnieuw opgestart met de nieuwe secundaire versie. Zie de [documentatie](../concepts-monitoring.md#planned-maintenance-notification)voor meer informatie. U kunt ook uw [Portal](https://aka.ms/servicehealthpm)controleren.| 

 Wanneer de flexibele server is geconfigureerd met een **zone redundant hoge Beschik baarheid**, voert de flexibele server eerst de schaal en de onderhouds bewerkingen op de stand-by-server uit. Zie [concepten-hoge Beschik baarheid](./concepts-high-availability.md)voor meer informatie.

##  <a name="unplanned-downtime-mitigation"></a>Ongeplande downtime-beperking

Ongeplande uitval tijd kan optreden als gevolg van onvoorziene onderbrekingen, zoals onderliggende hardwarestoringen, netwerk problemen en software fouten. Als de database server die is geconfigureerd met hoge Beschik baarheid onverwacht uitvalt, wordt de stand-by replica geactiveerd en kunnen de clients hun bewerkingen hervatten. Als het niet is geconfigureerd met hoge Beschik baarheid (HA) en de poging tot opnieuw opstarten mislukt, wordt er automatisch een nieuwe database server ingericht. Hoewel een ongeplande uitval tijd niet kan worden vermeden, kan de flexibele server de uitval tijd beperken door automatisch herstel bewerkingen uit te voeren zonder menselijke tussen komst. 
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Ongeplande downtime: fout scenario's en service herstel
Hieronder vindt u een aantal ongeplande fout scenario's en het herstel proces. 

| **Scenario** | **Herstel proces [niet-HA]** | **Herstel proces [HA]** |
| ---------- | ---------- | ------- |
| <B>Fout in database server | Als de database server niet beschikbaar is, zal Azure proberen de database server opnieuw te starten. Als dat niet lukt, wordt de database server opnieuw opgestart op een ander fysiek knoop punt.  <br /> <br /> De herstel tijd (RTO) is afhankelijk van verschillende factoren, waaronder de activiteit op het moment van de fout, zoals grote trans acties en het herstel volume dat moet worden uitgevoerd tijdens het opstart proces van de database server. <br /> <br /> Toepassingen die gebruikmaken van de PostgreSQL-data bases, moeten worden gebouwd op een manier die ze detecteert en de verwijderde verbindingen en mislukte trans acties opnieuw proberen. | Als er een fout is opgetreden in de database server, wordt een failover naar de stand-by-server uitgevoerd, waardoor de uitval tijd wordt verminderd. Zie de [pagina ha-concepten](./concepts-high-availability.md)voor meer informatie. RTO wordt verwacht 60 120s, met geen gegevens verlies. |
| <B>Opslag fout | Toepassingen zien geen gevolgen voor eventuele problemen met betrekking tot de opslag, zoals een schijf storing of een fysieke blok beschadiging. Wanneer de gegevens worden opgeslagen in drie kopieën, wordt de kopie van de gegevens geleverd door de overgebleven opslag. Het beschadigde gegevens blok wordt automatisch hersteld en er wordt automatisch een nieuwe kopie van de gegevens gemaakt. | Voor zeldzame en niet-herstel bare fouten, zoals de volledige opslag is niet toegankelijk, de flexibele server failover naar de stand-by replica om de uitval tijd te verminderen. Zie de [pagina ha-concepten](./concepts-high-availability.md)voor meer informatie. |
| <b> Logische/gebruikers fouten | Als u wilt herstellen van gebruikers fouten, zoals het per ongeluk verwijderen van tabellen of gegevens die niet juist zijn bijgewerkt, moet u een PITR (herstel naar een bepaald [tijdstip](../concepts-backup.md) ) uitvoeren. Tijdens het uitvoeren van de herstel bewerking geeft u het aangepaste herstel punt op. Dit is de tijd die het beste ligt voordat de fout optrad.<br> <br>  Als u alleen een subset van data bases of specifieke tabellen wilt herstellen in plaats van alle data bases in de database server, kunt u de database server herstellen in een nieuw exemplaar, de tabel (len) exporteren via [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html)en vervolgens [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) gebruiken om die tabellen te herstellen in uw data base. | Deze gebruikers fouten worden niet beveiligd met hoge Beschik baarheid, omdat alle wijzigingen synchroon worden gerepliceerd naar de stand-bymodus van de replica. U moet herstel naar een bepaald tijdstip uitvoeren om dergelijke fouten te herstellen. |
| <b> Fout in beschikbaarheids zone | Als u een fout op zone niveau wilt herstellen, kunt u herstel naar een bepaald tijdstip uitvoeren met behulp van de back-up en een aangepast herstel punt kiezen met de laatste tijd om de meest recente gegevens te herstellen. Er wordt een nieuwe flexibele server geïmplementeerd in een andere zone die niet van invloed is. De tijd die nodig is om te herstellen, is afhankelijk van de vorige back-up en het volume van de transactie logboeken die moeten worden hersteld. | Voor een flexibele server wordt automatisch een failover uitgevoerd naar de stand-by-server binnen 60 120s met geen gegevens verlies. Zie de [pagina ha-concepten](./concepts-high-availability.md)voor meer informatie. | 
| <b> Regio fout | Kruis regio lezen replica en geo-herstel van back-upfuncties worden nog niet ondersteund in de preview-versie. | |


> [!IMPORTANT]
> Verwijderde servers **kunnen niet** worden hersteld. Als u de server verwijdert, worden ook alle data bases die deel uitmaken van de server, verwijderd en kunnen deze niet worden hersteld. Gebruik [Azure resource Lock](../../azure-resource-manager/management/lock-resources.md) om onbedoelde verwijdering van uw server te voor komen.


## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over [zone redundante hoge Beschik baarheid](./concepts-high-availability.md)
-   Meer informatie over [back-up en herstel](./concepts-backup-restore.md)