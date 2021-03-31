---
title: Overzicht van bedrijfs continuïteit-Azure Database for MySQL flexibele server
description: Meer informatie over de concepten van bedrijfs continuïteit met Azure Database for MySQL flexibele server
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 4f9cc8321d5d1d19dbcb8294ad6205b01337ee72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101715051"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---flexible-server-preview"></a>Overzicht van bedrijfs continuïteit met Azure Database for MySQL-flexibele server (preview-versie)

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

Azure Database for MySQL flexibele server biedt mogelijkheden voor bedrijfs continuïteit die uw data bases beschermen in het geval van een geplande en ongeplande onderbreking. Functies, zoals geautomatiseerde back-ups en hoge Beschik baarheid, hebben verschillende niveaus van fout beveiliging met verschillende herstel tijd-en gegevens verlies Risico's. Als u uw toepassing wilt beveiligen tegen fouten, moet u rekening houden met de beoogde herstel tijd (RTO) en Recovery Point Objective (RPO) voor elke toepassing. RTO is de uitval tijd tolerantie en de RPO is de tolerantie voor gegevens verlies na een onderbreking van de database service.

In de volgende tabel ziet u de functies van flexibele server aanbiedingen.

| **Functie** | **Beschrijving** | **Beperkingen** |
| ---------- | ----------- | ------------ |
| **Back-up & herstellen** | Flexibele server voert automatisch dagelijkse back-ups van uw database bestanden uit en maakt voortdurend back-ups van transactie Logboeken. Back-ups kunnen worden bewaard gedurende elke periode tussen 1 en 35 dagen. U kunt uw database server herstellen naar elk gewenst moment binnen de retentie periode van de back-up. De herstel tijd is afhankelijk van de grootte van de gegevens die moeten worden hersteld en het tijdstip waarop de logboek herstel bewerking moet worden uitgevoerd. Raadpleeg [concepten: back-up maken en herstellen](./concepts-backup-restore.md) voor meer informatie. |Back-upgegevens blijven binnen de regio |
| **Lokale redundante back-up** | Flexibele server back-ups worden automatisch en veilig opgeslagen in een lokale redundante opslag binnen een regio en in dezelfde beschikbaarheids zone. De lokaal redundante back-ups repliceren de gegevens bestanden van de server back-up drie keer binnen één fysieke locatie in de primaire regio. Lokaal redundante back-upopslag biedt ten minste 99,999999999% (11 Nines) duurzaamheid van objecten in een bepaald jaar. Raadpleeg [concepten: back-up maken en herstellen](./concepts-backup-restore.md) voor meer informatie.| Van toepassing in alle regio's |
| **Zone-redundante hoge beschikbaarheid** | Flexibele servers kunnen worden geïmplementeerd in de modus voor hoge Beschik baarheid, waarmee primaire en stand-by servers in twee verschillende beschikbaarheids zones in een regio worden geïmplementeerd. Dit beschermt tegen storingen op zone niveau en helpt ook bij het verminderen van de uitval tijd van toepassingen tijdens geplande en ongeplande downtime-gebeurtenissen. Gegevens van de primaire server worden synchroon gerepliceerd naar de stand-by replica. Tijdens een downtime-gebeurtenis wordt automatisch een failover naar de stand-by replica uitgevoerd voor de database server. Raadpleeg [concepten-hoge Beschik baarheid](./concepts-high-availability.md) voor meer informatie. | Ondersteund in reken lagen voor algemeen gebruik en geoptimaliseerd voor geheugen. Alleen beschikbaar in regio's waar meerdere zones beschikbaar zijn.|
| **Premiumbestandsshares** | Database bestanden worden opgeslagen in een zeer duurzame en betrouw bare Azure Premium-bestands shares die gegevens redundantie bieden met drie kopieën van replica's die zijn opgeslagen in een beschikbaarheids zone met automatische gegevens herstel mogelijkheden. Raadpleeg [Premium-bestands shares](../../storage/files/storage-how-to-create-file-share.md) voor meer informatie. | Gegevens die zijn opgeslagen in een beschikbaarheids zone |

> [!IMPORTANT]
> Er worden geen uptime-, RTO-en RPO-SLA tijdens de preview-periode geboden. Details die op deze pagina worden verstrekt, alleen voor uw gegevens en plannings doeleinden.

## <a name="planned-downtime-mitigation"></a>Geplande downtime van uitval tijd

Hier volgen enkele geplande onderhouds scenario's voor uitval tijd:

| **Scenario** | **Proces**|
| :------------ | :----------- |
| **Compute scaling (gebruiker)**| Wanneer u de berekening van de reken schaal uitvoert, wordt een nieuwe flexibele server ingericht met behulp van de geschaalde Compute-configuratie. In de bestaande database server kunnen actieve controle punten worden voltooid, worden de client verbindingen geleegd, worden niet-doorgevoerde trans acties geannuleerd en vervolgens afgesloten. De opslag wordt vervolgens gekoppeld aan de nieuwe server en de data base wordt gestart, waardoor zo nodig herstel wordt uitgevoerd voordat client verbindingen worden geaccepteerd. |
| **Nieuwe software-implementatie (Azure)** | Nieuwe functies implementatie-of fout oplossingen worden automatisch uitgevoerd als onderdeel van het geplande onderhoud van de service, en u kunt plannen wanneer deze activiteiten plaatsvinden. Raadpleeg de [documentatie](https://aka.ms/servicehealthpm)voor meer informatie en Controleer ook uw [Portal](https://aka.ms/servicehealthpm) |
| **Secundaire versie-upgrades (Azure)** | Azure Database for MySQL worden database servers automatisch aan de secundaire versie door Azure door berekend. Deze treedt op als onderdeel van het geplande onderhoud van de service. Dit leidt tot een korte downtime in seconden en de database server wordt automatisch opnieuw opgestart met de nieuwe secundaire versie. Raadpleeg de [documentatie](../concepts-monitoring.md#planned-maintenance-notification)voor meer informatie en Controleer ook uw [Portal](https://aka.ms/servicehealthpm).|

Wanneer de flexibele server is geconfigureerd met een **zone redundant hoge Beschik baarheid**, voert de flexibele server eerst bewerkingen uit op de stand-by-server en vervolgens op de primaire server zonder failover. Raadpleeg [concepten-hoge Beschik baarheid](./concepts-high-availability.md) voor meer informatie.

## <a name="unplanned-downtime-mitigation"></a>Ongeplande downtime-beperking

Ongeplande uitval tijd kan optreden als gevolg van onvoorziene fouten, waaronder onderliggende hardwarestoringen, netwerk problemen en software fouten. Als de database server onverwacht uitvalt, als deze is geconfigureerd met hoge Beschik baarheid [HA], wordt de stand-by replica geactiveerd. Als dat niet het geval is, wordt er automatisch een nieuwe database server ingericht. Hoewel een ongeplande downtime niet kan worden vermeden, vermindert de flexibele server de uitval tijd door automatisch herstel bewerkingen uit te voeren op database server-en opslag lagen zonder menselijke tussen komst.

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Ongeplande downtime: fout scenario's en service herstel

Hier volgen enkele ongeplande fout scenario's en het herstel proces:

| **Scenario** | **Herstel proces [niet-HA]** | **Herstel proces [HA]** |
| :---------- | ---------- | ------- |
| **Fout in database server** | Als de database server niet beschikbaar is vanwege een bepaalde onderliggende hardwarefout, worden actieve verbindingen verwijderd en worden eventuele trans acties van de vlucht afgebroken. Er wordt geprobeerd om de database server opnieuw te starten met Azure. Als dat lukt, wordt het herstel van de data base uitgevoerd. Als het opnieuw opstarten mislukt, wordt geprobeerd de database server opnieuw op te starten op een ander fysiek knoop punt.  <br /> <br /> De herstel tijd (RTO) is afhankelijk van verschillende factoren, waaronder de activiteit op het moment van de fout, zoals grote trans acties en de hoeveelheid herstel die moet worden uitgevoerd tijdens het opstart proces van de database server. <br /> <br /> Toepassingen die gebruikmaken van de MySQL-data bases, moeten worden gebouwd op een manier die ze detecteren en verbroken verbindingen en mislukte trans acties opnieuw proberen.  Wanneer de toepassing opnieuw probeert, worden de verbindingen omgeleid naar de zojuist gemaakte database server. | Als er een fout is opgetreden in de database server, wordt de stand-by-database server geactiveerd, waardoor de downtime wordt verminderd. Raadpleeg de [pagina ha-concepten](concepts-high-availability.md) voor meer informatie. RTO wordt verwacht 60-120 s, met RPO = 0 |
| **Opslag fout** | Toepassingen zien geen gevolgen voor eventuele problemen met betrekking tot de opslag, zoals een schijf storing of een fysieke blok beschadiging. Wanneer de gegevens worden opgeslagen in 3 kopieën, wordt de kopie van de gegevens geleverd door de overgebleven opslag. Blok beschadigingen worden automatisch gecorrigeerd. Als er een kopie van gegevens verloren is gegaan, wordt er automatisch een nieuwe kopie van de gegevens gemaakt. | Voor niet-herstel bare fouten wordt een failover van de flexibele server naar de stand-by replica uitgevoerd om de downtime te verminderen. Raadpleeg de [pagina ha-concepten](./concepts-high-availability.md) voor meer informatie. |
| **Logische/gebruikers fouten** | Herstel van gebruikers fouten, zoals het per ongeluk verwijderen van tabellen of onjuiste bijgewerkte gegevens, omvat het uitvoeren van een herstel naar een bepaald [tijdstip](concepts-backup-restore.md) (PITR), door de gegevens te herstellen en te herstellen tot het moment dat de fout zich voordeed.<br> <br>  Als u alleen een subset van data bases of specifieke tabellen wilt herstellen in plaats van alle data bases in de database server, kunt u de database server herstellen in een nieuw exemplaar, de tabel (len) exporteren via [pg_dump](https://www.postgresql.org/docs/current/app-pgdump.html)en vervolgens [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html) gebruiken om die tabellen te herstellen in uw data base. | Deze gebruikers fouten worden niet beveiligd met hoge Beschik baarheid omdat alle gebruikers bewerkingen ook naar de stand-bymodus worden gerepliceerd. |
| **Fout in beschikbaarheids zone** | Hoewel het een zeldzame gebeurtenis is, kunt u, als u wilt herstellen van een storing op zone niveau, herstel naar een bepaald tijdstip uitvoeren met behulp van de back-up en een aangepast herstel punt kiezen om de meest recente gegevens op te halen. Er wordt een nieuwe flexibele server geïmplementeerd in een andere zone. De tijd die nodig is om te herstellen, is afhankelijk van de vorige back-up en het aantal transactie logboeken dat moet worden hersteld. | Flexibele server voert automatische failover uit naar de stand-by-site. Raadpleeg de [pagina ha-concepten](./concepts-high-availability.md) voor meer informatie. |
| **Regio fout** | De functies voor replicatie tussen regio's en geo-Restore worden nog niet ondersteund in de preview-versie. | |

> [!IMPORTANT]
> Verwijderde servers **kunnen niet** worden hersteld. Als u de server verwijdert, worden ook alle data bases die deel uitmaken van de server, verwijderd en kunnen deze niet worden hersteld. Gebruik [Azure resource Lock](../../azure-resource-manager/management/lock-resources.md) om onbedoelde verwijdering van uw server te voor komen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [zone redundante hoge Beschik baarheid](./concepts-high-availability.md)
- Meer informatie over [back-up en herstel](./concepts-backup-restore.md)