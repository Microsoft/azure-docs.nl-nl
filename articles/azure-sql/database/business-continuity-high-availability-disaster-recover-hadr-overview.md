---
title: Bedrijfs continuïteit in de Cloud-database herstel
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Meer informatie over hoe Azure SQL Database en SQL Managed instance ondersteuning bieden voor Cloud-bedrijfs continuïteit en database herstel, en om bedrijfs kritieke Cloud toepassingen te blijven uitvoeren.
keywords: bedrijfscontinuïteit, bedrijfscontinuïteit met de cloud, databasenoodherstel, databaseherstel
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/25/2019
ms.openlocfilehash: 7bd991bd709bb4be69325afe967d7e5600a9e1a4
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222561"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Overzicht van bedrijfscontinuïteit met Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

**Bedrijfs continuïteit** in Azure SQL database en SQL Managed instance verwijst naar de mechanismen, beleids regels en procedures waarmee uw bedrijf kan blijven werken in het geval van storingen, met name op de computer infrastructuur. In de meeste gevallen verwerkt SQL Database en SQL Managed instance de storende gebeurtenissen die zich in de cloud omgeving kunnen voordoen en blijven uw toepassingen en bedrijfs processen actief. Er zijn echter enkele storende gebeurtenissen die niet automatisch kunnen worden verwerkt door SQL Database, zoals:

- Gebruiker heeft per ongeluk een rij in een tabel verwijderd of bijgewerkt.
- Kwaadwillende aanvaller heeft gegevens verwijderd of een Data Base verwijderen.
- Aarding heeft geleid tot een stroom storing en een tijdelijk uitgeschakeld Data Center.

In dit overzicht worden de mogelijkheden beschreven die SQL Database en SQL Managed instance bieden voor bedrijfs continuïteit en herstel na nood gevallen. Meer informatie over opties, aanbevelingen en zelf studies voor het herstellen van storende gebeurtenissen die gegevens verlies kunnen veroorzaken of ervoor zorgen dat uw data base en toepassing niet meer beschikbaar zijn. Meer informatie over wat u moet doen wanneer een gebruiker of toepassings fout van invloed is op de gegevens integriteit, een Azure-regio een storing heeft of als uw toepassing onderhoud vereist.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>SQL Database-functies die u kunt gebruiken voor bedrijfscontinuïteit

Vanuit het perspectief van een Data Base zijn er vier belang rijke scenario's voor onderbrekingen:

- Lokale hardware-of software fouten die van invloed zijn op het database knooppunt, zoals een fout in de schijf schijf.
- Gegevens beschadiging of-verwijdering worden meestal veroorzaakt door een fout in de toepassing of door een humane fout. Dergelijke fouten zijn toepassingsspecifiek en worden normaal gesp roken niet gedetecteerd door de database service.
- Storing in Data Center, mogelijk veroorzaakt door een natuur ramp. Dit scenario vereist een zekere mate van geo-redundantie met de failover van de toepassing naar een ander Data Center.
- Upgrade-of onderhouds fouten, onverwachte problemen die optreden tijdens geplande onderhoud van de infra structuur of upgrades kunnen een snelle terugdraai bewerking naar een eerdere database status vereisen.

SQL Database bevat een [architectuur met hoge Beschik baarheid](high-availability-sla.md)om de lokale hardware-en software fouten te verhelpen, waardoor automatisch herstel van deze fouten wordt gegarandeerd met een sla van maxi maal 99,995% Beschik baarheid.  

Om uw bedrijf te beschermen tegen gegevens verlies, maken SQL Database en SQL Managed instance automatisch wekelijks volledige back-ups van data bases, differentiële database back-ups om de 12 uur en back-ups van transactie Logboeken om de 5-10 minuten. De back-ups worden opgeslagen in RA-GRS-opslag gedurende ten minste 7 dagen voor alle service lagen. Alle service lagen, met uitzonde ring van Basic Support Configureer bare Bewaar periode voor back-ups voor herstel naar een bepaald tijdstip, Maxi maal 35 dagen.

SQL Database en SQL Managed instance bieden ook verschillende functies voor bedrijfs continuïteit die u kunt gebruiken om verschillende niet-geplande scenario's te beperken.

- [Tijdelijke tabellen](../temporal-tables.md) maken het mogelijk rijversies te herstellen naar ieder gewenst tijdstip.
- Met [ingebouwde automatische back-ups](automated-backups-overview.md) en [herstel](recovery-using-backups.md#point-in-time-restore) naar een tijdstip kunt u de volledige data base op een bepaald moment binnen de geconfigureerde Bewaar periode tot 35 dagen herstellen.
- U kunt [een verwijderde data base herstellen](recovery-using-backups.md#deleted-database-restore) naar het punt waarop deze is verwijderd als de **server niet is verwijderd**.
- Met de [lange termijn retentie van back-ups](long-term-retention-overview.md) kunt u de back-ups tot wel tien jaar bewaren. Dit is een beperkte open bare Preview voor SQL Managed instance
- Met [actieve geo-replicatie](active-geo-replication-overview.md) kunt u lees bare replica's maken en hand matig failover naar een replica in het geval van een storing in een Data Center of een toepassings upgrade.
- Met de [groep automatische failover](auto-failover-group-overview.md#terminology-and-capabilities) kan de toepassing automatisch worden hersteld in het geval van een storing in het Data Center.

## <a name="recover-a-database-within-the-same-azure-region"></a>Een Data Base binnen dezelfde Azure-regio herstellen

U kunt automatische database back-ups gebruiken om een Data Base te herstellen naar een bepaald tijdstip in het verleden. Op deze manier kunt u herstellen van gegevens beschadigingen die worden veroorzaakt door menselijke fouten. Met het herstel naar een bepaald tijdstip kunt u een nieuwe data base maken op dezelfde server die de status van de gegevens van vóór de beschadigde gebeurtenis weergeeft. Voor de meeste data bases worden de herstel bewerkingen minder dan 12 uur in beslag genomen. Het kan langer duren om een zeer grote of zeer actieve Data Base te herstellen. Zie [Data Base Recovery Time](recovery-using-backups.md#recovery-time)(Engelstalig) voor meer informatie over herstel tijd.

Als de maximale ondersteunde Bewaar periode voor back-ups voor PITR (Point-in-time Restore) niet voldoende is voor uw toepassing, kunt u deze uitbreiden door een beleid voor lange termijn retentie (LTR) voor de data base (s) te configureren. Zie [lange termijn retentie van back-ups](long-term-retention-overview.md)voor meer informatie.

## <a name="compare-geo-replication-with-failover-groups"></a>Geo-replicatie met failover-groepen vergelijken

Met [groepen voor automatische failover](auto-failover-group-overview.md#terminology-and-capabilities) worden de implementatie en het gebruik van [geo-replicatie](active-geo-replication-overview.md) vereenvoudigd en worden de extra mogelijkheden toegevoegd, zoals beschreven in de volgende tabel:

|                                              | Geo-replicatie | Failovergroepen  |
|:---------------------------------------------| :-------------- | :----------------|
| **Automatische failover**                          |     Nee          |      Ja         |
| **Gelijktijdige failover van meerdere databases**  |     Nee          |      Ja         |
| **Gebruiker moet verbindingsreeks bijwerken na failover**      |     Ja         |      Nee          |
| **Ondersteuning voor SQL Managed Instance**                   |     Nee          |      Ja         |
| **Kan zich in dezelfde regio bevinden als primaire replica**             |     Ja         |      Nee          |
| **Meerdere replica's**                            |     Ja         |      Nee          |
| **Ondersteunt lezen-schalen**                          |     Ja         |      Ja         |


## <a name="recover-a-database-to-the-existing-server"></a>Een Data Base naar de bestaande server herstellen

Hoewel zeldzame gevallen kan een Azure-Data Center een storing hebben. Wanneer er een storing optreedt, veroorzaakt deze een bedrijfsonderbreking die mogelijk slechts een paar minuten maar ook enkele uren kan duren.

- Een optie is om te wachten tot de Data Base weer online is wanneer de storing van het Data Center is overschreden. Dit werkt voor toepassingen waarvoor het niet erg is dat de database offline is. Bijvoorbeeld een ontwikkelingsproject of een gratis proefversie waaraan u niet voortdurend hoeft te werken. Als er een storing in een Data Center optreedt, weet u niet hoe lang de onderbreking kan duren, dus deze optie werkt alleen als u de data base enige tijd niet nodig hebt.
- Een andere mogelijkheid is om een Data Base op een wille keurige server in een Azure-regio te herstellen met behulp van [geo-redundante database back-ups](recovery-using-backups.md#geo-restore) (geo-Restore). Geo-Restore gebruikt een geografisch redundante back-up als bron en kan worden gebruikt om een Data Base te herstellen, zelfs als de data base of het Data Center niet toegankelijk is vanwege een storing.
- Ten slotte kunt u snel de uitval van een storing herstellen als u geo-secundair hebt geconfigureerd met behulp van [actieve geo-replicatie](active-geo-replication-overview.md) of een [groep met automatische failover](auto-failover-group-overview.md) voor uw data base of data bases. Afhankelijk van uw keuze van deze technologieën kunt u hand matig of automatisch een failover gebruiken. Hoewel failover zelf slechts een paar seconden duurt, zal de service ten minste één uur duren om deze te activeren. Dit is nodig om ervoor te zorgen dat de failover gerechtvaardigd is door de schaal van de storing. De failover kan ook leiden tot een klein gegevens verlies als gevolg van de aard van de asynchrone replicatie.

Tijdens het ontwikkelen van uw plan voor bedrijfscontinuïteit moet u weten wat de maximaal acceptabele tijd is voordat de toepassing volledig is hersteld na de verstorende gebeurtenis. De tijd die nodig is om de toepassing volledig te herstellen, wordt de beoogde herstel tijd (RTO) genoemd. U moet ook inzicht krijgen in de maximale periode van recente gegevens updates (tijds interval) die de toepassing kan verliezen bij het herstellen van een niet-geplande verstorende gebeurtenis. Het mogelijke gegevens verlies staat bekend als beoogd herstel punt (RPO).

Verschillende herstel methoden bieden verschillende niveaus van RPO en RTO. U kunt een specifieke herstel methode kiezen of een combi natie van methoden gebruiken om volledige toepassings herstel te krijgen. De volgende tabel vergelijkt RPO en RTO van elke herstel optie. Met groepen voor automatische failover worden de implementatie en het gebruik van geo-replicatie vereenvoudigd en worden de extra mogelijkheden toegevoegd, zoals beschreven in de volgende tabel.

| Herstel methode | RTO | RPO |
| --- | --- | --- |
| Geo-herstel van geo-gerepliceerde back-ups | 12 uur | 1 uur |
| Automatische failover-groepen | 1 uur | 5 s |
| Hand matige data base-failover | 30 s | 5 s |

> [!NOTE]
> *Hand matige data base-failover* verwijst naar een failover van een enkele data base naar het geo-gerepliceerde secundair met de niet- [geplande modus](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities).
Zie de tabel eerder in dit artikel voor meer informatie over de RTO en RPO van de automatische failover.

Gebruik groepen voor automatische failover als uw toepassing aan een van deze criteria voldoet:

- Is bedrijfskritiek.
- Heeft een service level agreement (SLA) die 12 uur of langer uitval tijd niet toestaat.
- Uitval tijd kan leiden tot financiële aansprakelijkheid.
- Heeft een hoge mate van gegevens wijziging en 1 uur aan gegevens verlies is niet acceptabel.
- De extra kosten van actieve geo-replicatie zijn lager dan de mogelijke financiële verplichting en het bijbehorende bedrijfsverlies.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

U kunt ervoor kiezen om een combi natie van database back-ups en actieve geo-replicatie te gebruiken, afhankelijk van de vereisten van uw toepassing. Zie [een toepassing ontwerpen voor nood herstel](designing-cloud-solutions-for-disaster-recovery.md) in de Cloud en [strategieën voor nood herstel voor elastische Pools](disaster-recovery-strategies-for-applications-with-elastic-pool.md)voor een bespreking van ontwerp overwegingen voor zelfstandige data bases en voor elastische Pools met behulp van deze functies voor bedrijfs continuïteit.

In de volgende secties vindt u een overzicht van de stappen die u kunt herstellen met behulp van database back-ups of actieve geo-replicatie. Zie [een Data Base herstellen in SQL database van een storing](disaster-recovery-guidance.md)voor gedetailleerde stappen, zoals plannings vereisten, stappen na herstel en informatie over het simuleren van een storing voor het uitvoeren van een nood herstel analyse.

### <a name="prepare-for-an-outage"></a>Voorbereiden op een storing

Ongeacht de functie voor bedrijfscontinuïteit die u gebruikt, moet u:

- De doel server identificeren en voorbereiden, waaronder IP-firewall regels op server niveau, aanmeldingen en machtigingen op hoofd database niveau.
- Bepalen hoe clients en clienttoepassingen naar de nieuwe server worden omgeleid.
- Andere afhankelijkheden documenteren, zoals controle-instellingen en waarschuwingen.

Als u zich niet op de juiste manier voorbereidt, kunt u uw toepassingen online brengen na een failover of een herstel bewerking van een Data Base meer tijd en waarschijnlijk ook problemen oplossen op het moment van stress. Dit is een ongeldige combi natie.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Een failover uitvoeren naar een secundaire data base met geo-replicatie

Als u actieve groepen voor geo-replicatie of automatische failover gebruikt als herstel mechanisme, kunt u een automatisch failoverbeleid configureren of [hand matig niet-geplande failover](active-geo-replication-configure-portal.md#initiate-a-failover)gebruiken. Zodra de failover is gestart, wordt het secundaire de nieuwe primaire en gereed om nieuwe trans acties te registreren en te reageren op query's-met mini maal gegevens verlies voor de gegevens die nog niet zijn gerepliceerd. Zie [een toepassing ontwerpen voor nood herstel](designing-cloud-solutions-for-disaster-recovery.md)in de Cloud voor meer informatie over het ontwerpen van het failoverproces.

> [!NOTE]
> Wanneer het Data Center weer online komt, wordt de oude Primaries automatisch opnieuw verbonden met de nieuwe primaire en worden secundaire data bases. Als u de primaire keer terug naar de oorspronkelijke regio wilt verplaatsen, kunt u een geplande failover hand matig initiëren (failback).

### <a name="perform-a-geo-restore"></a>Geo-herstel uitvoeren

Als u gebruikmaakt van de automatische back-ups met geografisch redundante opslag (standaard ingeschakeld), kunt u de data base herstellen met behulp van [geo-Restore](disaster-recovery-guidance.md#recover-using-geo-restore). Herstel plaatsvindt doorgaans binnen 12 uur, met gegevens verlies van Maxi maal één uur, afhankelijk van het moment waarop de laatste back-up van het logboek is gemaakt en gerepliceerd. Totdat het herstellen is voltooid, kan de database geen transacties registreren en niet reageren op query's. Opmerking: met geo-Restore wordt de data base alleen teruggezet naar het laatst beschik bare tijdstip.

> [!NOTE]
> Als het Data Center weer online komt voordat u de toepassing overschakelt naar de herstelde data base, kunt u het herstel annuleren.

### <a name="perform-post-failover--recovery-tasks"></a>Taken na failover/hersteltaken uitvoeren

Na herstel via een van beide herstelmechanismen moet u de volgende aanvullende taken uitvoeren voordat uw gebruikers en toepassingen opnieuw actief zijn:

- Clients en client toepassingen omleiden naar de nieuwe server en de data base herstellen.
- Zorg ervoor dat de juiste IP-firewall regels op server niveau zijn ingesteld voor gebruikers om verbinding te maken met [firewalls op database niveau](firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) om de juiste regels in te scha kelen.
- Zorg ervoor dat de juiste aanmeldingen en machtigingen op hoofd database niveau aanwezig zijn (of gebruik [Inge sloten gebruikers](/sql/relational-databases/security/contained-database-users-making-your-database-portable)).
- Configureer de controle, indien van toepassing.
- Waarschuwingen configureren, indien van toepassing.

> [!NOTE]
> Als u een failovergroep gebruikt en verbinding maakt met de data bases met behulp van de Read-Write-listener, wordt de omleiding na een failover automatisch en transparant voor de toepassing uitgevoerd.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Een toepassing upgraden met minimale uitvaltijd

Soms moet een toepassing offline worden gehaald vanwege gepland onderhoud, zoals een toepassings upgrade. Bij het [beheren van toepassings upgrades](manage-application-rolling-upgrade.md) wordt beschreven hoe u actieve geo-replicatie kunt gebruiken om rolling upgrades van uw Cloud toepassing mogelijk te maken om de downtime tijdens upgrades te minimaliseren en een herstelpad op te geven als er iets verkeerd gaat.

## <a name="next-steps"></a>Volgende stappen

Zie [een toepassing ontwerpen voor nood herstel](designing-cloud-solutions-for-disaster-recovery.md) in de Cloud en [strategieën voor nood herstel voor elastische Pools](disaster-recovery-strategies-for-applications-with-elastic-pool.md)voor een bespreking van overwegingen voor het ontwerpen van toepassingen voor afzonderlijke data bases en voor elastische Pools.
