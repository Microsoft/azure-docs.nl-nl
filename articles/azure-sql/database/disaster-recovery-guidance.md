---
title: Herstel na noodgeval
description: Meer informatie over het herstellen van een Data Base uit een regionale Data Center-storing of-storing met de Azure SQL Database actieve geo-replicatie en mogelijkheden voor geoherstel.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 06/21/2019
ms.openlocfilehash: 11c83a6ec364865eb3478112c9f33add22a5c09d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643258"
---
# <a name="restore-your-azure-sql-database-or-failover-to-a-secondary"></a>Uw Azure SQL Database of failover naar een secundaire herstellen
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database biedt de volgende mogelijkheden voor het herstellen van een storing:

- [Actieve geo-replicatie](active-geo-replication-overview.md)
- [Automatische failover-groepen](auto-failover-group-overview.md)
- [Geo-herstel](recovery-using-backups.md#point-in-time-restore)
- [Zone-redundante data bases](high-availability-sla.md)

Zie [bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md)voor meer informatie over scenario's voor bedrijfs continuïteit en de functies die deze scenario's ondersteunen.

> [!NOTE]
> Als u gebruikmaakt van zone-redundante Premium of Bedrijfskritiek data bases of Pools, wordt het herstel proces geautomatiseerd en is de rest van dit materiaal niet van toepassing.
>
> Zowel de primaire als de secundaire data base moeten dezelfde servicelaag hebben. Het wordt ook ten zeerste aanbevolen om de secundaire data base te maken met dezelfde reken grootte (Dtu's of vCores) als de primaire. Zie [upgrade of downgrade als primaire data base](active-geo-replication-overview.md#upgrading-or-downgrading-primary-database)voor meer informatie.
>
> Gebruik een of meer failover-groepen om failover van meerdere data bases te beheren.
> Als u een bestaande geo-replicatie relatie aan de failovergroep toevoegt, zorg er dan voor dat de geo-Secondary is geconfigureerd met dezelfde servicelaag en reken grootte als de primaire. Zie [automatische failover-groepen gebruiken om een transparante en gecoördineerde failover van meerdere data bases mogelijk te maken](auto-failover-group-overview.md)voor meer informatie.

## <a name="prepare-for-the-event-of-an-outage"></a>Voorbereiden op het geval van een storing

Voor een geslaagde herstel bewerking voor een ander gegevens gebied met behulp van failover-groepen of geo-redundante back-ups, moet u een server in een andere Data Center-storing voorbereiden om de nieuwe primaire server te worden, en moet er ook een goed gedefinieerde stappen worden beschreven en getest om te zorgen voor een soepel herstel. Deze voorbereidings stappen zijn onder andere:

- Identificeer de server in een andere regio om de nieuwe primaire server te worden. Voor geo-Restore is dit doorgaans een server in de [gekoppelde regio](../../best-practices-availability-paired-regions.md) voor de regio waarin uw data base zich bevindt. Dit elimineert de kosten voor extra verkeer tijdens de geo-herstel bewerkingen.
- Identificeer en optioneel de IP-firewall regels op server niveau die nodig zijn voor gebruikers om toegang te krijgen tot de nieuwe primaire data base.
- Bepaal hoe u gebruikers omleidt naar de nieuwe primaire server, zoals door verbindings reeksen te wijzigen of door DNS-vermeldingen te wijzigen.
- Identificeer en optioneel, de aanmeldingen die aanwezig moeten zijn in de hoofd database op de nieuwe primaire server, en zorg ervoor dat deze aanmeldingen de juiste machtigingen hebben in de hoofd database, indien van toepassing. Zie [SQL database Security na nood herstel](active-geo-replication-security-configure.md) voor meer informatie.
- Waarschuwings regels identificeren die moeten worden bijgewerkt om ze toe te wijzen aan de nieuwe primaire data base.
- De controle configuratie op de huidige primaire data base documenteren
- Voer een [nood herstel analyse](disaster-recovery-drills.md)uit. Als u een storing wilt simuleren voor geo-herstel, kunt u de bron database verwijderen of de naam ervan wijzigen, zodat de verbindings fout van de toepassing wordt veroorzaakt. Als u een storing wilt simuleren met behulp van failover-groepen, kunt u de webtoepassing of virtuele machine die is verbonden met de data base uitschakelen of de data base failoveren om verbindings fouten van toepassingen te veroorzaken.

## <a name="when-to-initiate-recovery"></a>Wanneer moet u het herstel starten?

De herstel bewerking is van invloed op de toepassing. Hiervoor moet de SQL-connection string of-omleiding worden gewijzigd met DNS. Dit kan leiden tot permanent gegevens verlies. Daarom moet het worden gedaan alleen wanneer de storing langer duurt dan de beoogde herstel tijd van de toepassing. Wanneer de toepassing wordt geïmplementeerd voor productie, moet u regel matig de status van de toepassing controleren en de volgende gegevens punten gebruiken om te bevestigen dat het herstel is gerechtvaardigd:

1. Permanente verbindings fout van de toepassings tier naar de data base.
2. De Azure Portal toont een waarschuwing over een incident in de regio met een grote impact.

> [!NOTE]
> Als u failover-groepen gebruikt en automatische failover hebt gekozen, wordt het herstel proces geautomatiseerd en transparant voor de toepassing.

Afhankelijk van de toepassings tolerantie tot uitval tijd en mogelijke bedrijfs aansprakelijkheid kunt u de volgende herstel opties overwegen.

Gebruik de [database herstel bare data base](/previous-versions/azure/reference/dn800985(v=azure.100)) (*LastAvailableBackupDate*) ophalen om het meest recente, door de geo gerepliceerde terugzet punt op te halen.

## <a name="wait-for-service-recovery"></a>Wachten op service herstel

De Azure-teams werken om de beschik baarheid van de service zo snel mogelijk te herstellen, maar afhankelijk van de hoofd oorzaak kan het uren of dagen duren.  Als uw toepassing aanzienlijke downtime kan verdragen, kunt u gewoon wachten tot het herstel is voltooid. In dit geval is er geen actie voor uw onderdeel vereist. U kunt de huidige status van de service zien op het [Azure service Health-dash board](https://azure.microsoft.com/status/). Na het herstel van de regio wordt de beschik baarheid van uw toepassing hersteld.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Een failover uitvoeren naar een secundaire server met geo-replicatie in de failovergroep

Als de uitval tijd van uw toepassing kan leiden tot bedrijfs aansprakelijkheid, moet u failover-groepen gebruiken. Zo kan de toepassing de beschik baarheid in een andere regio snel herstellen in het geval van een storing. Zie [een geografisch gedistribueerde data base implementeren](geo-distributed-application-configure-tutorial.md)voor een zelf studie.

Als u de beschik baarheid van de data base (s) wilt herstellen, moet u de failover naar de secundaire server initiëren met een van de ondersteunde methoden.

Gebruik een van de volgende hand leidingen voor een failover naar een secundaire data base met geo-replicatie:

- [Een failover uitvoeren naar een secundaire server met geo-replicatie met behulp van de Azure Portal](active-geo-replication-configure-portal.md)
- [Failover naar de secundaire server met behulp van Power shell](scripts/setup-geodr-and-failover-database-powershell.md)
- [Failover naar een secundaire server met behulp van Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Herstellen met behulp van geo-Restore

Als de downtime van uw toepassing niet leidt tot bedrijfs aansprakelijkheid, kunt u [geo-herstel](recovery-using-backups.md) gebruiken als methode om uw toepassings database (s) te herstellen. Er wordt een kopie van de data base gemaakt op basis van de meest recente geo-redundante back-up.

## <a name="configure-your-database-after-recovery"></a>Uw data base configureren na herstel

Als u geo-herstel gebruikt om een storing te herstellen, moet u ervoor zorgen dat de verbinding met de nieuwe data bases juist is geconfigureerd zodat de normale toepassings functie kan worden hervat. Dit is een controle lijst met taken voor het voorbereiden van uw herstelde database productie.

### <a name="update-connection-strings"></a>Verbindings reeksen bijwerken

Omdat de herstelde data base zich op een andere server bevindt, moet u de connection string van de toepassing bijwerken zodat deze naar die server verwijst.

Zie voor meer informatie over het wijzigen van verbindings reeksen de juiste ontwikkelings taal voor uw [verbindings bibliotheek](connect-query-content-reference-guide.md#libraries).

### <a name="configure-firewall-rules"></a>Firewallregels configureren

U moet ervoor zorgen dat de firewall regels die zijn geconfigureerd op de server en op de data base overeenkomen met die zijn geconfigureerd op de primaire server en de primaire data base. Zie [How to: Configure Firewall Settings (Azure SQL database) (Engelstalig)](firewall-configure.md)voor meer informatie.

### <a name="configure-logins-and-database-users"></a>Aanmeldingen en database gebruikers configureren

U moet ervoor zorgen dat alle aanmeldingen die door uw toepassing worden gebruikt, bestaan op de server die als host fungeert voor de herstelde data base. Zie [beveiligings configuratie voor geo-replicatie](active-geo-replication-security-configure.md)voor meer informatie.

> [!NOTE]
> U moet de firewall regels en-aanmeldingen (en de machtigingen) van uw server configureren en testen tijdens een nood herstel analyse. Deze objecten op server niveau en hun configuratie zijn mogelijk niet beschikbaar tijdens de onderbreking.

### <a name="setup-telemetry-alerts"></a>Telemetrie-waarschuwingen instellen

U moet ervoor zorgen dat de instellingen van de bestaande waarschuwings regel worden bijgewerkt om toe te wijzen aan de herstelde data base en de andere server.

Zie [waarschuwings meldingen ontvangen](../../azure-monitor/alerts/alerts-overview.md) en [service Health bijhouden](../../service-health/service-notifications.md)voor meer informatie over database waarschuwings regels.

### <a name="enable-auditing"></a>Controle inschakelen

Als controle is vereist voor toegang tot uw data base, moet u controle inschakelen na het herstel van de data base. Zie [Data Base auditing](../../azure-sql/database/auditing-overview.md)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL database automatische back-ups](automated-backups-overview.md) voor meer informatie over Azure SQL database automatische back-ups
- Zie [continuïteits scenario's](business-continuity-high-availability-disaster-recover-hadr-overview.md) voor meer informatie over ontwerp-en herstel scenario's voor bedrijfs continuïteit
- Zie [een Data Base herstellen vanuit de door de service geïnitieerde back-ups](recovery-using-backups.md) voor meer informatie over het gebruik van automatische back-ups voor herstel