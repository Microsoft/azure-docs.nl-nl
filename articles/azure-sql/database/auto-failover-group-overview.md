---
title: Groep voor automatische failover
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Met groepen voor automatische failover kunt u replicatie en automatische/gecoördineerde failover van een groep data bases op een server of alle data bases in een beheerd exemplaar beheren.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 11/16/2020
ms.openlocfilehash: e91a3cc0a96add1f53d220e04fb98d63cc7c33f4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841083"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-failover-of-multiple-databases"></a>Gebruik groepen voor automatische failover om transparante en gecoördineerde failover van meerdere data bases mogelijk te maken
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Met de functie groepen voor automatische failover kunt u de replicatie en failover van een groep data bases op een server of voor alle data bases in een beheerd exemplaar naar een andere regio beheren. Het is een declaratieve abstractie boven op de bestaande [actieve geo-replicatie](active-geo-replication-overview.md) functie, ontworpen om de implementatie en het beheer van geo-gerepliceerde data bases op schaal te vereenvoudigen. U kunt failover hand matig initiëren of u kunt deze overdragen aan de Azure-service op basis van een door de gebruiker gedefinieerd beleid. Met de laatste optie kunt u automatisch meerdere gerelateerde data bases in een secundaire regio herstellen na een onherstelbare fout of een andere niet-geplande gebeurtenis die het volledige of gedeeltelijke verlies van de beschik baarheid van de SQL Database of SQL Managed instance in de primaire regio tot gevolg heeft. Een failover-groep kan een of meer data bases bevatten, die meestal worden gebruikt door dezelfde toepassing. Daarnaast kunt u de Lees bare secundaire data bases gebruiken om werk belastingen met alleen-lezen query's te offloaden. Omdat voor groepen voor automatische failover meerdere data bases zijn vereist, moeten deze data bases worden geconfigureerd op de primaire server. Automatische failover-groepen ondersteunen replicatie van alle data bases in de groep naar slechts één secundaire server of exemplaar in een andere regio.

> [!NOTE]
> Als u meerdere Azure SQL Database secundaire zones in dezelfde of verschillende regio's wilt, gebruikt u [actieve geo-replicatie](active-geo-replication-overview.md).

Als u gebruikmaakt van groepen voor automatische failover met automatische failoverbeleid, wordt een storing die van invloed is op een of meer van de data bases in de groep, in automatische failover veroorzaakt. Dit zijn doorgaans incidenten die niet kunnen worden verholpen door de ingebouwde automatische maximale Beschik baarheid. De voor beelden van failover-triggers zijn een incident dat wordt veroorzaakt door een SQL Database Tenant-ring of besturings element dat niet beschikbaar is vanwege een geheugenlek van een OS-kernel op verschillende reken knooppunten, of een incident dat door een of meer tenants wordt veroorzaakt omdat er een onjuiste netwerk kabel is opgetreden tijdens het opontmantelen van de routine.  Zie [SQL database hoge Beschik baarheid](high-availability-sla.md)voor meer informatie.

Daarnaast bieden automatische-failover-groepen alleen-lezen-en alleen-lezen listener-eind punten die ongewijzigd blijven tijdens failovers. Ongeacht of u hand matige of automatische failover hebt geactiveerd, schakelt failover alle secundaire data bases in de groep over naar primair. Nadat de data base-failover is voltooid, wordt de DNS-record automatisch bijgewerkt om de eind punten om te leiden naar de nieuwe regio. Zie [overzicht van bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md)voor de specifieke RPO-en RTO-gegevens.

Wanneer u groepen voor automatische failover gebruikt met automatische failoverbeleid, wordt elke storing die invloed heeft op data bases op een server of een beheerd exemplaar, automatisch failover. U kunt de groep voor automatische failover beheren met:

- [Azure Portal](geo-distributed-application-configure-tutorial.md)
- [Azure CLI: failover-groep](scripts/add-database-to-failover-group-cli.md)
- [Power shell: failover-groep](scripts/add-database-to-failover-group-powershell.md)
- [Rest API: failovergroep](/rest/api/sql/failovergroups).

Zorg er na een failover voor dat de verificatie vereisten voor uw data base en server, of dat het exemplaar is geconfigureerd op de nieuwe primaire. Zie [SQL database Security na nood herstel](active-geo-replication-security-configure.md)voor meer informatie.

Voor een echte bedrijfs continuïteit is het toevoegen van database redundantie tussen data centers slechts een deel van de oplossing. Het herstellen van een toepassing (Service) End-to-end na een onherstelbare fout vereist het herstellen van alle onderdelen die de service en eventuele afhankelijke services vormen. Voor beelden van deze onderdelen zijn de client software (bijvoorbeeld een browser met een aangepaste Java script), web-front-ends, opslag en DNS. Het is van essentieel belang dat alle onderdelen zich in dezelfde storingen bevinden en beschikbaar komen binnen de beoogde herstel tijd (RTO) van uw toepassing. Daarom moet u alle afhankelijke services identificeren en inzicht krijgen in de garanties en mogelijkheden die ze bieden. Vervolgens moet u de juiste stappen nemen om ervoor te zorgen dat uw service functioneert tijdens de failover van de services waarvan deze afhankelijk is. Zie [cloud oplossingen ontwerpen voor herstel na nood gevallen met actieve geo-replicatie](designing-cloud-solutions-for-disaster-recovery.md)voor meer informatie over het ontwerpen van oplossingen voor herstel na nood gevallen.

## <a name="terminology-and-capabilities"></a>Terminologie en mogelijkheden

- **Failovergroep (mist)**

  Een failovergroep is een benoemde groep data bases die wordt beheerd door één server of binnen een beheerd exemplaar en waarvoor een failover kan worden uitgevoerd als een eenheid naar een andere regio voor het geval alle of sommige primaire data bases niet beschikbaar zijn vanwege een storing in de primaire regio. Wanneer deze is gemaakt voor een SQL-beheerd exemplaar, bevat een failovergroep alle gebruikers databases in het exemplaar en kan er slechts één failovergroep worden geconfigureerd voor een exemplaar.
  
  > [!IMPORTANT]
  > De naam van de failovergroep moet globaal uniek zijn binnen het `.database.windows.net` domein.

- **Servers**

     Met servers kunnen sommige of alle gebruikers databases op een server in een failovergroep worden geplaatst. Daarnaast ondersteunt een server meerdere failover-groepen op één server.

- **Primair**

  De server of het beheerde exemplaar dat als host fungeert voor de primaire data bases in de failovergroep.

- **Secundair**

  De server of het beheerde exemplaar dat als host fungeert voor de secundaire data bases in de failovergroep. De secundaire kan zich niet in dezelfde regio bevinden als de primaire.

- **Afzonderlijke data bases aan een failovergroep toevoegen**

  U kunt verschillende afzonderlijke data bases op dezelfde server in dezelfde failover-groep plaatsen. Als u één data base aan de failovergroep toevoegt, wordt er automatisch een secundaire data base gemaakt met dezelfde editie en reken grootte op de secundaire server.  U hebt de server opgegeven toen de failovergroep werd gemaakt. Als u een Data Base toevoegt die al een secundaire Data Base op de secundaire server heeft, wordt die geo-replicatie koppeling overgenomen door de groep. Wanneer u een Data Base toevoegt die al een secundaire Data Base bevat op een server die geen deel uitmaakt van de failovergroep, wordt er een nieuw secundair gemaakt op de secundaire server.

  > [!IMPORTANT]
  > Zorg ervoor dat de secundaire server geen data base met dezelfde naam heeft als deze een bestaande secundaire data base is. In failover-groepen voor SQL Managed instance worden alle gebruikers databases gerepliceerd. U kunt geen subset van gebruikers databases kiezen voor replicatie in de failovergroep.

- **Data bases in elastische pool toevoegen aan failovergroep**

  U kunt alle of meerdere data bases binnen een elastische pool in dezelfde failovergroep plaatsen. Als de primaire data base zich in een elastische pool bevindt, wordt het secundaire apparaat automatisch in de elastische pool gemaakt met dezelfde naam (secundaire groep). U moet ervoor zorgen dat de secundaire server een elastische pool met dezelfde exacte naam en voldoende vrije capaciteit bevat voor het hosten van de secundaire data bases die worden gemaakt door de failovergroep. Als u een data base in de groep toevoegt die al een secundaire data base in de secundaire groep heeft, wordt die geo-replicatie koppeling overgenomen door de groep. Wanneer u een Data Base toevoegt die al een secundaire Data Base bevat op een server die geen deel uitmaakt van de failovergroep, wordt er een nieuwe secundaire in de secundaire groep gemaakt.
  
- **Eerste seeding**

  Bij het toevoegen van data bases, elastische Pools of beheerde exemplaren aan een failovergroep, is er een initiële seeding-fase voordat de gegevens replicatie wordt gestart. De eerste seeding-fase is de langste en duurste bewerking. Zodra de initiële seeding is voltooid, worden de gegevens gesynchroniseerd, waarna alleen de volgende gegevens wijzigingen worden gerepliceerd. De tijd die nodig is om het oorspronkelijke Seed te volt ooien, is afhankelijk van de grootte van uw gegevens, het aantal gerepliceerde data bases en de snelheid van de koppeling tussen de entiteiten in de failovergroep. Onder normale omstandigheden geldt een mogelijke seeding-snelheid van Maxi maal 500 GB per uur voor SQL Database en tot 360 GB per uur voor een SQL-beheerd exemplaar. Seeding wordt uitgevoerd voor alle data bases parallel.

  Voor SQL Managed Instance kunt u de snelheid van de snelle route koppeling tussen de twee instanties bepalen bij het schatten van de tijd van de eerste seeding-fase. Als de snelheid van de koppeling tussen de twee instanties langzamer is dan nodig is, wordt de tijd tot Seed waarschijnlijk vooral beïnvloed. U kunt de vermelde seeding-snelheid, het aantal data bases, de totale grootte van de gegevens en de koppelings snelheid gebruiken om te schatten hoe lang de eerste seeding duurt voordat de replicatie van de gegevens wordt gestart. Voor een Data Base van één 100 GB zou de eerste seed-fase ongeveer 1,2 uur duren als de koppeling in staat is om 84 GB per uur te pushen, en als er geen andere data bases worden seeding. Als de koppeling 10 GB per uur kan overdragen, neemt de seeding van een Data Base van 100 GB ongeveer 10 uur in beslag. Als er meerdere data bases zijn om te repliceren, wordt seeding parallel uitgevoerd en, in combi natie met een langzame verbindings snelheid, kan de initiële seeding-fase aanzienlijk langer duren, met name als de parallelle seeding van gegevens uit alle data bases de beschik bare koppelings bandbreedte overschrijdt. Als de netwerk bandbreedte tussen twee instanties beperkt is en u meerdere beheerde exemplaren aan een failovergroep toevoegt, kunt u overwegen meerdere beheerde instanties opeenvolgend toe te voegen aan de failovergroep, één voor één. Gezien de gateway-SKU van een juiste grootte tussen de twee beheerde instanties, en als de band breedte van het bedrijfs netwerk dit toestaat, is het mogelijk dat er snelheden van Maxi maal 360 GB per uur kunnen worden gerealiseerd.  

- **DNS-zone**

  Een unieke ID die automatisch wordt gegenereerd wanneer er een nieuw exemplaar van SQL Managed wordt gemaakt. Een SAN-certificaat (multi-Domain) voor dit exemplaar is ingericht voor het verifiëren van de client verbindingen met een wille keurig exemplaar in dezelfde DNS-zone. De twee beheerde exemplaren in dezelfde failovergroep moeten de DNS-zone delen.
  
  > [!NOTE]
  > Een DNS-zone-ID is niet vereist voor failover-groepen die zijn gemaakt voor SQL Database.

- **Listener voor lezen/schrijven van failover-groep**

  Een DNS CNAME-record die verwijst naar de URL van de huidige primaire. Het wordt automatisch gemaakt wanneer de failovergroep wordt gemaakt en de werk belasting lezen-schrijven kan transparant opnieuw verbinding maken met de primaire data base wanneer de primaire wijzigingen na een failover worden uitgevoerd. Wanneer de failovergroep is gemaakt op een server, wordt de DNS CNAME-record voor de URL van de listener gevormd als `<fog-name>.database.windows.net` . Wanneer de failovergroep is gemaakt op een SQL-beheerd exemplaar, wordt de DNS CNAME-record voor de URL van de listener gevormd als `<fog-name>.<zone_id>.database.windows.net` .

- **Listener voor alleen-lezen van failover-groep**

  Een DNS CNAME-record die verwijst naar de alleen-lezen listener die verwijst naar de URL van de secundaire. Het wordt automatisch gemaakt wanneer de failovergroep wordt gemaakt en de alleen-lezen SQL-workload kan transparant worden verbonden met de secundaire met behulp van de opgegeven taakverdelings regels. Wanneer de failovergroep is gemaakt op een server, wordt de DNS CNAME-record voor de URL van de listener gevormd als `<fog-name>.secondary.database.windows.net` . Wanneer de failovergroep is gemaakt op een SQL-beheerd exemplaar, wordt de DNS CNAME-record voor de URL van de listener gevormd als `<fog-name>.secondary.<zone_id>.database.windows.net` .

- **Beleid voor automatische failover**

  Een failover-groep is standaard geconfigureerd met een automatisch failoverbeleid. De failover van Azure wordt geactiveerd nadat de fout is gedetecteerd en de respijt periode is verlopen. Het systeem moet controleren of de onderbreking niet kan worden verholpen door de ingebouwde [infra structuur met hoge Beschik baarheid](high-availability-sla.md) vanwege de schaal van de impact. Als u de werk stroom van de failover vanuit de toepassing of hand matig wilt beheren, kunt u automatische failover uitschakelen.
  
  > [!NOTE]
  > Omdat verificatie van de omvang van de storing en hoe snel deze kan worden verholpen, acties van het operationele team worden uitgevoerd, kan de respijt periode niet meer dan één uur worden ingesteld. Deze beperking is van toepassing op alle data bases in de failovergroep, ongeacht hun gegevens synchronisatie status.

- **Alleen-lezen failoverbeleid**

  De failover van de alleen-lezen listener is standaard uitgeschakeld. Het zorgt ervoor dat de prestaties van de primaire server niet worden beïnvloed wanneer de secundaire offline is. Het betekent echter ook dat de alleen-lezen sessies geen verbinding kunnen maken tot het secundaire bestand is hersteld. Als u uitval tijd niet kunt gebruiken voor de alleen-lezen sessies en de primaire alleen-lezen-en lezen-schrijven-verkeer met de potentiële prestaties van de primaire prestatie vermindering wilt, kunt u failover voor de alleen-lezen listener inschakelen door de eigenschap te configureren `AllowReadOnlyFailoverToPrimary` . In dat geval wordt het alleen-lezen verkeer automatisch omgeleid naar de primaire als de secundaire niet beschikbaar is.

  > [!NOTE]
  > De `AllowReadOnlyFailoverToPrimary` eigenschap is alleen van invloed als automatische failoverbeleid is ingeschakeld en er een automatische failover is geactiveerd door Azure. Als de eigenschap is ingesteld op True, is de nieuwe primaire waarde alleen-lezen-schrijven en alleen-lezen-sessies.

- **Geplande failover**

   De geplande failover voert een volledige synchronisatie uit tussen de primaire en secundaire data bases vóór de secundaire switches naar de primaire rol. Dit garandeert geen gegevens verlies. Geplande failover wordt gebruikt in de volgende scenario's:

  - Herstel na nood gevallen (DR) in productie uitvoeren wanneer het gegevens verlies niet acceptabel is
  - De data bases naar een andere regio verplaatsen
  - De data bases retour neren naar de primaire regio nadat de storing is verholpen (failback).

- **Niet-geplande failover**

   Bij een niet-geplande of geforceerde failover wordt de secundaire functie direct overgeschakeld naar de primaire rol zonder synchronisatie met de primaire. Met deze bewerking wordt gegevens verlies veroorzaakt. Niet-geplande failover wordt gebruikt als herstel methode tijdens storingen wanneer de primaire niet toegankelijk is. Wanneer de oorspronkelijke primaire primair weer online is, wordt automatisch opnieuw verbinding gemaakt zonder synchronisatie en wordt een nieuwe secundaire.

- **Hand matige failover**

  U kunt failover op elk gewenst moment hand matig initiëren, ongeacht de automatische failover-configuratie. Als er geen automatische failoverbeleid is geconfigureerd, moet er hand matige failover worden uitgevoerd om data bases in de failovergroep naar de secundaire te herstellen. U kunt geforceerde of een gebruiks vriendelijke failover initiëren (met volledige gegevens synchronisatie). Deze laatste kan worden gebruikt om de primaire naar de secundaire regio te verplaatsen. Wanneer de failover is voltooid, worden de DNS-records automatisch bijgewerkt om ervoor te zorgen dat er verbinding wordt gemaakt met de nieuwe primaire.

- **Respijt periode met gegevens verlies**

  Omdat de primaire en secundaire data bases worden gesynchroniseerd met asynchrone replicatie, kan de failover leiden tot gegevens verlies. U kunt het beleid voor automatische failover aanpassen zodat de tolerantie van uw toepassing wordt aangepast aan gegevens verlies. Door te configureren `GracePeriodWithDataLossHours` kunt u bepalen hoe lang het systeem wacht voordat de failover wordt gestart die gegevens verlies waarschijnlijk zal veroorzaken.

- **Meerdere failover-groepen**

  U kunt meerdere failover-groepen voor hetzelfde paar servers configureren om het bereik van failovers te beheren. Elke groep failover onafhankelijk. Als uw multi tenant-toepassing gebruikmaakt van elastische Pools, kunt u deze mogelijkheid gebruiken om de primaire en secundaire data bases in elke groep te combi neren. Op deze manier kunt u de impact van een storing beperken tot slechts de helft van de tenants.

  > [!NOTE]
  > SQL Managed instance biedt geen ondersteuning voor meerdere failover-groepen.
  
## <a name="permissions"></a>Machtigingen

Machtigingen voor een failovergroep worden beheerd via [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md). De rol [SQL Server Inzender](../../role-based-access-control/built-in-roles.md#sql-server-contributor) heeft alle benodigde machtigingen voor het beheren van failover-groepen.

### <a name="create-failover-group"></a>Failovergroep maken

Als u een failovergroep wilt maken, moet u RBAC schrijf toegang hebben tot de primaire en secundaire servers en naar alle data bases in de failovergroep. Voor een SQL Managed instance hebt u RBAC-schrijf toegang nodig tot zowel het primaire als het secundaire exemplaar van SQL Managed, maar de machtigingen voor afzonderlijke data bases zijn niet relevant, omdat afzonderlijke SQL Managed instance-data bases niet kunnen worden toegevoegd aan of verwijderd uit een failovergroep.

### <a name="update-a-failover-group"></a>Een failovergroep bijwerken

Als u een failovergroep wilt bijwerken, moet u RBAC schrijf toegang hebben tot de failovergroep en alle data bases op de huidige primaire server of een beheerd exemplaar.  

### <a name="fail-over-a-failover-group"></a>Een failover-groep uitvoeren

Als u een failover wilt uitvoeren, moet u RBAC-schrijf toegang hebben tot de failovergroep op de nieuwe primaire server of het beheerde exemplaar.

## <a name="best-practices-for-sql-database"></a>Aanbevolen procedures voor SQL Database

De groep voor automatische failover moet worden geconfigureerd op de primaire server en wordt verbonden met de secundaire server in een andere Azure-regio. De groepen kunnen alle of sommige data bases op deze servers bevatten. Het volgende diagram illustreert een typische configuratie van een geo-redundante Cloud toepassing met behulp van meerdere data bases en een groep voor automatische failover.

![Diagram toont een typische configuratie van een geo-redundante Cloud toepassing met behulp van meerdere data bases en een groep voor automatische failover.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Zie [SQL database toevoegen aan een failovergroep](failover-group-add-single-database-tutorial.md) voor een gedetailleerde zelf studie met stapsgewijze instructies om een data base in SQL database aan een failovergroep toe te voegen.

Houd bij het ontwerpen van een service met bedrijfs continuïteit de volgende algemene richt lijnen:

### <a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a>Een of meer failover-groepen gebruiken om failover van meerdere data bases te beheren

Er kunnen een of meer failover-groepen worden gemaakt tussen twee servers in verschillende regio's (primaire en secundaire servers). Elke groep kan een of meer data bases bevatten die als een eenheid worden hersteld in het geval dat alle of enkele primaire data bases niet beschikbaar zijn vanwege een storing in de primaire regio. De groep failover maakt geo-secundaire data base met dezelfde service doelstelling als de primaire. Als u een bestaande geo-replicatie relatie aan de failovergroep toevoegt, zorg er dan voor dat de geo-Secondary is geconfigureerd met dezelfde servicelaag en reken grootte als de primaire.
  
> [!IMPORTANT]
> Het maken van failover-groepen tussen twee servers in verschillende abonnementen wordt momenteel niet ondersteund voor Azure SQL Database. Als u de primaire of secundaire server naar een ander abonnement verplaatst nadat de failovergroep is gemaakt, kan dit leiden tot fouten in de failover-aanvragen en andere bewerkingen.

### <a name="using-read-write-listener-for-oltp-workload"></a>Lezen-schrijven-listener gebruiken voor OLTP-workload

Bij het uitvoeren van OLTP-bewerkingen gebruikt u `<fog-name>.database.windows.net` als de server-URL en worden de verbindingen automatisch naar de primaire verbinding geleid. Deze URL wordt niet gewijzigd na de failover. Opmerking voor de failover moet de DNS-record worden bijgewerkt, zodat de client verbindingen alleen worden omgeleid naar de nieuwe primaire server nadat de DNS-cache van de client is vernieuwd.

### <a name="using-read-only-listener-for-read-only-workload"></a>Alleen-lezen-listener gebruiken voor alleen-lezen werk belasting

Als u een logisch geïsoleerde alleen-lezen werk belasting hebt die tolerant is voor bepaalde verouderde gegevens, kunt u de secundaire data base in de toepassing gebruiken. Gebruik voor alleen-lezen-sessies `<fog-name>.secondary.database.windows.net` als de server-URL en de verbinding wordt automatisch naar de secundaire omgeleid. U wordt ook aangeraden om in connection string Lees intentie op te geven met behulp van `ApplicationIntent=ReadOnly` .

### <a name="preparing-for-performance-degradation"></a>Prestatie vermindering voorbereiden

Een typische Azure-toepassing maakt gebruik van meerdere Azure-Services en bestaat uit meerdere onderdelen. De automatische failover van de failovergroep wordt geactiveerd op basis van de status van de Azure SQL-onderdelen. Andere Azure-Services in de primaire regio worden mogelijk niet beïnvloed door de storing en hun onderdelen zijn mogelijk nog steeds beschikbaar in die regio. Zodra de primaire data bases overschakelen naar de DR-regio, kan de latentie tussen de afhankelijke onderdelen toenemen. Om te voor komen dat de gevolgen van een hogere latentie van de prestaties van de toepassing worden beïnvloed, moet u ervoor zorgen dat alle onderdelen van de toepassing in de DR-regio worden redundantie en deze [richt lijnen voor netwerk beveiliging](#failover-groups-and-network-security)volgen.

### <a name="preparing-for-data-loss"></a>Gegevens verlies voorbereiden

Als er een storing wordt gedetecteerd, wacht Azure voor de periode die u hebt opgegeven door `GracePeriodWithDataLossHours` . De standaard waarde is 1 uur. Als u geen gegevens verlies kunt veroorloven, moet u ervoor zorgen dat `GracePeriodWithDataLossHours` het een voldoende groot getal is, bijvoorbeeld 24 uur. Gebruik hand matige failover van de groep om een failback uit te geven van de secundaire naar de primaire.

> [!IMPORTANT]
> Elastische Pools met 800 of minder Dtu's en meer dan 250-data bases die gebruikmaken van geo-replicatie, kunnen problemen ondervinden, inclusief langere, geplande failovers en verminderde prestaties.  Deze problemen treden waarschijnlijk vaker op voor het schrijven van intensieve workloads, wanneer geo-replicatie-eind punten op grote schaal worden gescheiden door geografie, of wanneer meerdere secundaire eind punten worden gebruikt voor elke Data Base.  Symptomen van deze problemen worden aangegeven wanneer de geo-replicatie vertraging na verloop van tijd toeneemt.  Deze vertraging kan worden bewaakt met behulp van [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).  Als deze problemen optreden, zijn de mogelijke oplossingen het verhogen van het aantal groeps Dtu's of het verminderen van het aantal geo-gerepliceerde data bases in dezelfde groep.

### <a name="changing-secondary-region-of-the-failover-group"></a>Secundaire regio van de failovergroep wijzigen

Ter illustratie van de wijzigings volgorde gaan we ervan uit dat Server A de primaire server is, Server B de bestaande secundaire server is en server C de nieuwe tweede in de derde regio is.  Voer de volgende stappen uit om de overgang te maken:

1. Maak aanvullende secundaire zones van elke Data Base op server A naar server C met behulp van [actieve geo-replicatie](active-geo-replication-overview.md). Elke Data Base op server A heeft twee secundaire zones: één op server B en één op server C. Hiermee wordt gegarandeerd dat de primaire data bases beveiligd blijven tijdens de overgang.
2. Verwijder de failovergroep. De aanmeldingen worden op dit moment niet uitgevoerd. Dit komt doordat de SQL-aliassen voor de listeners van de failovergroep zijn verwijderd en de gateway de naam van de failover-groep niet herkent.
3. Maak de failovergroep opnieuw met dezelfde naam tussen de servers A en C. Op dit moment worden de aanmeldingen niet meer uitgevoerd.
4. Voeg alle primaire data bases op server A toe aan de nieuwe failovergroep.
5. Haal server B weg. Alle data bases op B worden automatisch verwijderd.

### <a name="changing-primary-region-of-the-failover-group"></a>Primaire regio van de failovergroep wijzigen

Ter illustratie van de wijzigings volgorde gaan we ervan uit dat Server A de primaire server is, Server B de bestaande secundaire server is en server C de nieuwe primair is in de derde regio.  Voer de volgende stappen uit om de overgang te maken:

1. Voer een geplande failover uit om de primaire server over te scha kelen naar B. server A wordt de nieuwe secundaire server. De failover kan leiden tot enkele minuten uitval tijd. De werkelijke tijd is afhankelijk van de grootte van de failovergroep.
2. Maak aanvullende secundaire zones van elke Data Base op server B naar server C met behulp van [actieve geo-replicatie](active-geo-replication-overview.md). Elke Data Base op server B heeft twee secundaire gegevens, één op server A en één op server C. Hiermee wordt gegarandeerd dat de primaire data bases beveiligd blijven tijdens de overgang.
3. Verwijder de failovergroep. De aanmeldingen worden op dit moment niet uitgevoerd. Dit komt doordat de SQL-aliassen voor de listeners van de failovergroep zijn verwijderd en de gateway de naam van de failover-groep niet herkent.
4. Maak de failovergroep opnieuw met dezelfde naam tussen de servers B en C. Op dit moment worden de aanmeldingen niet meer uitgevoerd.
5. Voeg alle primaire data bases op B toe aan de nieuwe failovergroep.
6. Voer een geplande failover van de failovergroep uit om tussen B en C te scha kelen. Nu wordt Server C de primaire en B-de secundaire. Alle secundaire data bases op server A worden automatisch gekoppeld aan de Primaries op C. Net als bij stap 1 kan de failover tot enkele minuten uitval tijd tot gevolg hebben.
7. Verwijder de server A. Alle data bases op een worden automatisch verwijderd.

> [!IMPORTANT]
> Wanneer de failovergroep wordt verwijderd, worden ook de DNS-records voor de listener-eind punten verwijderd. Op dat moment is er sprake van een niet-nul-kans van iemand anders die een failovergroep of server alias met dezelfde naam maakt, waardoor u deze niet opnieuw kunt gebruiken. Gebruik geen algemene namen voor failover-groepen om het risico tot een minimum te beperken.

## <a name="best-practices-for-sql-managed-instance"></a>Aanbevolen procedures voor SQL Managed instance

De groep voor automatische failover moet worden geconfigureerd op het primaire exemplaar en wordt verbonden met het secundaire exemplaar in een andere Azure-regio.  Alle databases in het exemplaar worden gerepliceerd naar het secundaire exemplaar.

Het volgende diagram illustreert een typische configuratie van een geo-redundante Cloud toepassing met behulp van beheerde exemplaren en de groep voor automatische failover.

![diagram voor automatische failover](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> Zie [Managed instance toevoegen aan een failovergroep](../managed-instance/failover-group-add-instance-tutorial.md) voor een gedetailleerde stapsgewijze zelf studie voor het toevoegen van een failovergroep door een door SQL beheerd exemplaar toe te voegen.

Als uw toepassing gebruikmaakt van SQL Managed instance als de gegevenslaag, volgt u deze algemene richt lijnen bij het ontwerpen voor bedrijfs continuïteit:

### <a name="creating-the-secondary-instance"></a>Het secundaire exemplaar maken

Om ervoor te zorgen dat de verbinding met het primaire SQL-beheerde exemplaar na een failover niet wordt onderbroken, moeten de primaire en secundaire exemplaren zich in dezelfde DNS-zone bevindt. Hiermee wordt gegarandeerd dat hetzelfde multi-Domain (SAN)-certificaat kan worden gebruikt voor het verifiëren van de client verbindingen met een van de twee exemplaren in de failovergroep. Wanneer uw toepassing gereed is voor productie-implementatie, maakt u een secundair SQL-beheerd exemplaar in een andere regio en zorgt u ervoor dat de DNS-zone wordt gedeeld met het primaire SQL-beheerde exemplaar. U kunt dit doen door de optionele para meter op te geven `DNS Zone Partner` met behulp van de Azure Portal, Power shell of de rest API.

> [!IMPORTANT]
> Het eerste beheerde exemplaar dat in het subnet wordt gemaakt, bepaalt de DNS-zone voor alle volgende instanties in hetzelfde subnet. Dit betekent dat twee exemplaren van hetzelfde subnet geen deel kunnen uitmaken van verschillende DNS-zones.

Zie [een secundaire beheerde instantie maken](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance)voor meer informatie over het maken van het secundaire SQL-beheerde exemplaar in dezelfde DNS-zone als het primaire exemplaar.

### <a name="using-geo-paired-regions"></a>Geografisch gekoppelde regio's gebruiken

Implementeer beide beheerde exemplaren in [gekoppelde regio's](../../best-practices-availability-paired-regions.md), om prestatieredenen. Beheerde exemplaren die zich in geografisch gekoppelde regio's bevinden, presteren veel beter dan die in niet-gekoppelde regio's. 

### <a name="enabling-replication-traffic-between-two-instances"></a>Replicatie verkeer tussen twee instanties inschakelen

Omdat elk exemplaar is geïsoleerd in een eigen VNet, moet twee richtings verkeer tussen deze VNets worden toegestaan. Zie [Azure VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a>Een failovergroep maken tussen beheerde exemplaren in verschillende abonnementen

U kunt een failovergroep tussen SQL Managed instances maken in twee verschillende abonnementen, zolang abonnementen zijn gekoppeld aan dezelfde [Azure Active Directory Tenant](../../active-directory/fundamentals/active-directory-whatis.md#terminology). Wanneer u Power shell API gebruikt, kunt u dit doen door de `PartnerSubscriptionId` para meter op te geven voor het secundaire SQL-beheerde exemplaar. Wanneer u REST API gebruikt, kan elke exemplaar-ID die is opgenomen in de `properties.managedInstancePairs` para meter een eigen subscriptionID hebben.
  
> [!IMPORTANT]
> Azure Portal biedt geen ondersteuning voor het maken van failover-groepen voor verschillende abonnementen. Voor de bestaande failover-groepen voor verschillende abonnementen en/of resource groepen kan failover ook niet hand matig worden gestart via de portal van het primaire SQL-beheerde exemplaar. Start deze vanuit het geo-secundaire exemplaar.

### <a name="managing-failover-to-secondary-instance"></a>Failover naar een secundair exemplaar beheren

De failovergroep beheert de failover van alle databases in de SQL Managed Instance. Wanneer een groep wordt gemaakt, wordt elke database in het exemplaar automatisch geo-gerepliceerd naar de secundaire SQL Managed Instance. U kunt geen failover-groepen gebruiken om een gedeeltelijke failover van een subset van de databases te initiëren.

> [!IMPORTANT]
> Als een Data Base wordt verwijderd uit het primaire exemplaar van SQL Managed, wordt deze ook automatisch neergezet op het geo-secundair SQL-beheerde exemplaar.

### <a name="using-read-write-listener-for-oltp-workload"></a>Lezen-schrijven-listener gebruiken voor OLTP-workload

Bij het uitvoeren van OLTP-bewerkingen gebruikt u `<fog-name>.zone_id.database.windows.net` als de server-URL en worden de verbindingen automatisch naar de primaire verbinding geleid. Deze URL wordt niet gewijzigd na de failover. De failover omvat het bijwerken van de DNS-record, zodat de client verbindingen alleen worden omgeleid naar de nieuwe primaire server nadat de DNS-cache van de client is vernieuwd. Omdat het secundaire exemplaar de DNS-zone met de primaire share deelt, kan de client toepassing opnieuw verbinding maken met het certificaat met behulp van dezelfde SAN-certificaten.

### <a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a>Een alleen-lezen listener gebruiken om verbinding te maken met het secundaire exemplaar

Als u een logisch geïsoleerde alleen-lezen werk belasting hebt die tolerant is voor bepaalde verouderde gegevens, kunt u de secundaire data base in de toepassing gebruiken. Als u rechtstreeks verbinding wilt maken met het geo-gerepliceerde secundair, gebruikt u `<fog-name>.secondary.<zone_id>.database.windows.net` als de server-URL en wordt de verbinding direct tot stand gebracht met het geo-gerepliceerde secundaire.

> [!NOTE]
> In de service lagen Premium, Bedrijfskritiek en grootschalige ondersteunt SQL Database het gebruik van alleen- [lezen replica's](read-scale-out.md) voor het uitvoeren van alleen-lezen query's met behulp van de capaciteit van een of meer alleen-lezen replica's, met behulp van de `ApplicationIntent=ReadOnly` para meter in de Connection String. Wanneer u een secundaire geo-replicatie hebt geconfigureerd, kunt u deze mogelijkheid gebruiken om verbinding te maken met een alleen-lezen-replica op de primaire locatie of op de geo-gerepliceerde locatie.
>
> - Gebruik en om verbinding te maken met een alleen-lezen replica op de primaire locatie `ApplicationIntent=ReadOnly` `<fog-name>.<zone_id>.database.windows.net` .
> - Gebruik en om verbinding te maken met een alleen-lezen replica op de secundaire locatie `ApplicationIntent=ReadOnly` `<fog-name>.secondary.<zone_id>.database.windows.net` .

### <a name="preparing-for-performance-degradation"></a>Prestatie vermindering voorbereiden

Een typische Azure-toepassing maakt gebruik van meerdere Azure-Services en bestaat uit meerdere onderdelen. De automatische failover van de failovergroep wordt geactiveerd op basis van de status van de Azure SQL-onderdelen. Andere Azure-Services in de primaire regio worden mogelijk niet beïnvloed door de storing en hun onderdelen zijn mogelijk nog steeds beschikbaar in die regio. Zodra de primaire data bases overschakelen naar de secundaire regio, kan de latentie tussen de afhankelijke onderdelen toenemen. Om de impact van hogere latentie van de prestaties van de toepassing te vermijden, moet u de redundantie van alle onderdelen van de toepassing in de secundaire regio en failover van toepassings onderdelen uitvoeren met de data base. Op de configuratie tijd volgt u de [richt lijnen voor netwerk beveiliging](#failover-groups-and-network-security) om ervoor te zorgen dat er verbinding wordt met de data base in de secundaire regio.

### <a name="preparing-for-data-loss"></a>Gegevens verlies voorbereiden

Als er een storing wordt gedetecteerd, wordt een failover voor lezen/schrijven geactiveerd als er geen gegevens verloren zijn, tot het beste van onze kennis. Anders wordt de failover uitgesteld voor de periode die u opgeeft met `GracePeriodWithDataLossHours` . Als u hebt opgegeven `GracePeriodWithDataLossHours` , moet u worden voor bereid voor gegevens verlies. Over het algemeen is het voor de beschik baarheid van Azure van belang. Als u geen gegevens verlies kunt veroorloven, moet u ervoor zorgen dat toegevoegd wordt ingesteld op een voldoende groot getal, zoals 24 uur, of om automatische failover uit te scha kelen.

De DNS-update van de listener voor lezen-schrijven gebeurt onmiddellijk nadat de failover is gestart. Deze bewerking resulteert niet in gegevens verlies. Het proces van het wisselen van database rollen kan echter tot vijf minuten duren onder normale omstandigheden. Totdat de data bases in het nieuwe primaire exemplaar zijn voltooid, hebben ze nog steeds het kenmerk alleen-lezen. Als een failover wordt gestart met behulp van Power shell, is de bewerking om de rol van primaire replica te wijzigen synchroon. Als het wordt gestart met behulp van de Azure Portal, wordt in de gebruikers interface de voltooiings status weer geven. Als deze is gestart met behulp van de REST API, gebruikt u het polling mechanisme van de standaard Azure Resource Manager om te controleren of het is voltooid.

> [!IMPORTANT]
> Gebruik hand matige failover van groep om Primaries terug te zetten naar de oorspronkelijke locatie. Wanneer de storing die de failover veroorzaakte, wordt verholpen, kunt u de primaire data bases naar de oorspronkelijke locatie verplaatsen. Als u dit wilt doen, moet u de hand matige failover van de groep initiëren.
  
### <a name="changing-secondary-region-of-the-failover-group"></a>Secundaire regio van de failovergroep wijzigen

We gaan ervan uit dat exemplaar A het primaire exemplaar is, instantie B is de bestaande secundaire instantie en exemplaar C is de nieuwe secundaire instantie in de derde regio.  Voer de volgende stappen uit om de overgang te maken:

1. Maak instantie C met dezelfde grootte als en in dezelfde DNS-zone.
2. Verwijder de failovergroep tussen de exemplaren A en B. De aanmeldingen worden op dit moment niet uitgevoerd omdat de SQL-aliassen voor de listeners van de failovergroep zijn verwijderd en de gateway de naam van de failovergroep niet herkent. De secundaire data bases worden losgekoppeld van de Primaries en worden gelezen-write-data bases.
3. Maak een failovergroep met dezelfde naam tussen exemplaar A en C. Volg de instructies in de [failovergroep met de zelf studie voor SQL Managed instance](../managed-instance/failover-group-add-instance-tutorial.md). Dit is een omvang van de gegevens bewerking en wordt voltooid wanneer alle data bases van instantie A worden geseed en gesynchroniseerd.
4. Verwijder instance B als dit niet nodig is om onnodige kosten te voor komen.

> [!NOTE]
> Na stap 2 en totdat stap 3 is voltooid, blijven de data bases in instantie A onbeschermd tegen een onherstelbare fout van instantie A.

### <a name="changing-primary-region-of-the-failover-group"></a>Primaire regio van de failovergroep wijzigen

We gaan ervan uit dat exemplaar A het primaire exemplaar is, instantie B is de bestaande secundaire instantie en exemplaar C is de nieuwe primaire instantie in de derde regio.  Voer de volgende stappen uit om de overgang te maken:

1. Maak instantie C met dezelfde grootte als B en in dezelfde DNS-zone.
2. Maak verbinding met exemplaar B en hand matige failover om de primaire instantie over te scha kelen naar B. instantie A wordt automatisch het nieuwe secundaire exemplaar.
3. Verwijder de failovergroep tussen de exemplaren A en B. De aanmeldingen worden op dit moment niet uitgevoerd omdat de SQL-aliassen voor de listeners van de failovergroep zijn verwijderd en de gateway de naam van de failovergroep niet herkent. De secundaire data bases worden losgekoppeld van de Primaries en worden gelezen-write-data bases.
4. Maak een failovergroep met dezelfde naam tussen exemplaar A en C. Volg de instructies in de [failovergroep with Managed instance zelf studie](../managed-instance/failover-group-add-instance-tutorial.md). Dit is een omvang van de gegevens bewerking en wordt voltooid wanneer alle data bases van instantie A worden geseed en gesynchroniseerd.
5. Verwijder exemplaar A als dit niet nodig is om onnodige kosten te voor komen.

> [!CAUTION]
> Na stap 3 en totdat stap 4 is voltooid, blijven de data bases in instantie A onbeschermd tegen een onherstelbare fout van instantie A.

> [!IMPORTANT]
> Wanneer de failovergroep wordt verwijderd, worden ook de DNS-records voor de listener-eind punten verwijderd. Op dat moment is er sprake van een niet-nul-kans van iemand anders die een failovergroep of server alias met dezelfde naam maakt, waardoor u deze niet opnieuw kunt gebruiken. Gebruik geen algemene namen voor failover-groepen om het risico tot een minimum te beperken.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>Scenario's die afhankelijk zijn van objecten in de systeem databases inschakelen
Systeem databases worden niet gerepliceerd naar het secundaire exemplaar in een failovergroep. Voor het inschakelen van scenario's die afhankelijk zijn van objecten uit de systeem databases, op het secundaire exemplaar, moet u ervoor zorgen dat u dezelfde objecten op de secundaire maakt. Als u bijvoorbeeld van plan bent om dezelfde aanmeldingen te gebruiken op de secundaire instantie, moet u deze maken met dezelfde SID. 
```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 


## <a name="failover-groups-and-network-security"></a>Failover-groepen en netwerk beveiliging

Voor sommige toepassingen moeten de beveiligings regels vereisen dat het netwerk toegang tot de gegevenslaag wordt beperkt tot een specifiek onderdeel of andere onderdelen, zoals een VM, webservice, enzovoort. Deze vereiste vormt enkele uitdagingen voor het ontwerpen van bedrijfs continuïteit en het gebruik van de failover-groepen. Houd rekening met de volgende opties bij het implementeren van dergelijke beperkte toegang.

### <a name="using-failover-groups-and-virtual-network-rules"></a>Failover-groepen en regels voor virtuele netwerken gebruiken

Als u [Virtual Network Service-eind punten en-regels](vnet-service-endpoint-rule-overview.md) gebruikt om de toegang tot uw data base in SQL database of SQL Managed instance te beperken, moet u er rekening mee houden dat elk service-eind punt van de virtuele netwerk alleen van toepassing is op één Azure-regio. Met het eind punt kunnen andere regio's geen communicatie van het subnet accepteren. Daarom kunnen alleen de client toepassingen die in dezelfde regio worden geïmplementeerd, verbinding maken met de primaire data base. Omdat de failover resulteert in de SQL Database-client sessies worden omgeleid naar een server in een andere (secundaire) regio, mislukken deze sessies als ze afkomstig zijn van een client buiten die regio. Het automatische failoverbeleid kan daarom niet worden ingeschakeld als de deelnemende servers of instanties zijn opgenomen in de Virtual Network-regels. Voer de volgende stappen uit om hand matige failover te ondersteunen:

1. Richt de redundante kopieën in van de front-end onderdelen van uw toepassing (webservice, virtuele machines, enzovoort) in de secundaire regio
2. De regels voor het [virtuele netwerk](vnet-service-endpoint-rule-overview.md) afzonderlijk configureren voor de primaire en secundaire server
3. De [front-end-failover inschakelen met behulp van de Traffic Manager-configuratie](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)
4. Start de hand matige failover wanneer de onderbreking wordt gedetecteerd. Deze optie is geoptimaliseerd voor de toepassingen die een consistente latentie vereisen tussen de front-end en de gegevenslaag en ondersteuning bieden voor herstel wanneer een front-end, een gegevenslaag of beide worden beïnvloed door de storing.

> [!NOTE]
> Als u de **alleen-lezen listener** gebruikt om taken te verdelen over een alleen-lezen werk belasting, moet u ervoor zorgen dat deze werk belasting wordt uitgevoerd in een virtuele machine of in een andere resource in de secundaire regio zodat deze verbinding kan maken met de secundaire data base.

### <a name="use-failover-groups-and-firewall-rules"></a>Failover-groepen en firewall regels gebruiken

Als failover is vereist voor uw bedrijfs continuïteits plan met behulp van groepen met automatische failover, kunt u de toegang tot uw data base in SQL Database beperken met behulp van de traditionele firewall regels. Voer de volgende stappen uit om automatische failover te ondersteunen:

1. [Een openbaar IP-adres maken](../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Maak een open bare Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) en wijs hieraan het open bare IP-adres toe.
3. [Een virtueel netwerk en de virtuele machines](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) voor uw front-end-onderdelen maken
4. [Netwerk beveiligings groep maken](../../virtual-network/network-security-groups-overview.md) en binnenkomende verbindingen configureren.
5. Zorg ervoor dat de uitgaande verbindingen zijn geopend om Azure SQL Database met behulp van de SQL- [service-tag](../../virtual-network/network-security-groups-overview.md#service-tags).
6. Maak een [SQL database firewall regel](firewall-configure.md) om binnenkomend verkeer toe te staan van het open bare IP-adres dat u in stap 1 hebt gemaakt.

Zie [uitgaande verbindingen van load balancers](../../load-balancer/load-balancer-outbound-connections.md)voor meer informatie over het configureren van uitgaande toegang en welk IP-adres moet worden gebruikt in de firewall regels.

De bovenstaande configuratie zorgt ervoor dat de automatische failover geen verbindingen van de front-end-onderdelen blokkeert en er wordt van uitgegaan dat de toepassing de langere latentie tussen de front-end en de gegevenslaag kan verdragen.

> [!IMPORTANT]
> Om de bedrijfs continuïteit voor regionale uitval te garanderen, moet u ervoor zorgen dat u geografische redundantie voor zowel de front-end-onderdelen als de data bases hebt.

## <a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a>Geo-replicatie tussen beheerde exemplaren en hun VNets inschakelen

Wanneer u een failovergroep tussen de primaire en secundaire SQL Managed instances in twee verschillende regio's instelt, wordt elk exemplaar geïsoleerd met behulp van een onafhankelijk virtueel netwerk. Als u replicatie verkeer tussen deze VNets wilt toestaan, moet u ervoor zorgen dat aan deze vereisten wordt voldaan:

- De twee exemplaren van SQL Managed instance moeten zich in verschillende Azure-regio's bevindt.
- De twee exemplaren van SQL Managed instance moeten dezelfde servicelaag hebben en dezelfde opslag grootte hebben.
- Uw secundaire exemplaar van een door SQL beheerd exemplaar moet leeg zijn (geen gebruikers databases).
- De virtuele netwerken die worden gebruikt door de instanties van SQL Managed instance moeten worden verbonden via een [VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) of [Express route](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md). Wanneer twee virtuele netwerken verbinding maken via een on-premises netwerk, zorg er dan voor dat er geen firewallregel is die poorten 5022 en 11000-11999 blokkeert. Wereld wijde VNet-peering wordt ondersteund door de beperking die wordt beschreven in de onderstaande opmerking.

   > [!IMPORTANT]
   > [Op 9/22/2020 zijn wereld wijde virtuele netwerk peering aangekondigd voor nieuwe virtuele clusters](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Dit betekent dat de peering van globale virtuele netwerken wordt ondersteund voor SQL-beheerde instanties die zijn gemaakt in lege subnetten na de aankondigings datum, en ook voor alle daaropvolgende beheerde exemplaren die in deze subnetten zijn gemaakt. Voor alle andere SQL Managed instances-ondersteuning voor peering is beperkt tot de netwerken in dezelfde regio vanwege de [beperkingen van globale virtuele netwerk peering](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Zie ook de relevante sectie van het artikel Veelgestelde [vragen over virtuele netwerken van Azure](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. 

- De twee SQL Managed instance VNets kunnen geen overlappende IP-adressen hebben.
- U moet uw netwerkbeveiligingsgroepen (NSG) zodanig instellen dat poort 5022 en het bereik 11000–12000 zijn geopend voor inkomende en uitgaande verbindingen van het subnet van het andere beheerde exemplaar. Dit is om replicatieverkeer tussen de exemplaren mogelijk te maken.

   > [!IMPORTANT]
   > Onjuist geconfigureerde NSG-beveiligings regels leiden tot vastgelopen database Kopieer bewerkingen.

- Het secundaire SQL Managed instance is geconfigureerd met de juiste DNS-zone-ID. DNS-zone is een eigenschap van een door SQL beheerd exemplaar en een onderliggend virtueel cluster, en de bijbehorende ID is opgenomen in het adres van de hostnaam. De zone-ID wordt gegenereerd als een wille keurige teken reeks wanneer het eerste door SQL beheerde exemplaar wordt gemaakt in elk VNet en dezelfde ID wordt toegewezen aan alle andere exemplaren in hetzelfde subnet. Zodra het is toegewezen, kan de DNS-zone niet worden gewijzigd. SQL-beheerde instanties die zijn opgenomen in dezelfde failovergroep, moeten de DNS-zone delen. Dit doet u door de zone-ID van het primaire exemplaar door te geven als waarde voor de para meter DnsZonePartner bij het maken van het secundaire exemplaar.

   > [!NOTE]
   > Zie [een SQL-beheerd exemplaar toevoegen aan een failovergroep](../managed-instance/failover-group-add-instance-tutorial.md)voor een gedetailleerde zelf studie over het configureren van failover-groepen met SQL Managed instance.

## <a name="upgrading-or-downgrading-a-primary-database"></a>Een primaire data base bijwerken of downgrade uitvoeren

U kunt een upgrade of downgrade van een primaire Data Base naar een andere reken grootte (binnen dezelfde servicelaag, niet tussen Algemeen en Bedrijfskritiek) zonder de verbinding van secundaire data bases te verbreken. Wanneer u een upgrade uitvoert, raden we u aan om eerst alle secundaire data bases bij te werken en vervolgens een upgrade uit te voeren van de primaire. Wanneer u een downgrade uitvoert, maakt u de volg orde ongedaan: downgradet u eerst de primaire data base. Bij het upgraden of downgraden van de database naar een andere servicelaag wordt deze aanbeveling afgedwongen.

Deze volgorde wordt aangeraden om te voorkomen dat het secundaire exemplaar bij een lagere SKU overbelast raakt en opnieuw moet worden geseed tijdens een upgrade- of downgradeproces. U kunt dit probleem ook vermijden door het primaire exemplaar alleen-lezen te maken. Dit gaat ten koste van alle lezen/schrijven-werkbelastingen die zijn gekoppeld aan het primaire exemplaar.

> [!NOTE]
> Als u een secundaire Data Base hebt gemaakt als onderdeel van de configuratie van de failovergroep, wordt het niet aanbevolen de secundaire data base te downgradeen. Zo zorgt u ervoor dat uw gegevenslaag voldoende capaciteit heeft om uw normale werk belasting te verwerken nadat de failover is geactiveerd.

## <a name="preventing-the-loss-of-critical-data"></a>Het verlies van kritieke gegevens voor komen

Vanwege de hoge latentie van Wide Area Networks, gebruikt doorlopende kopieën een mechanisme voor asynchrone replicatie. Asynchrone replicatie maakt enkele gegevens verlies onvermijdbaar als er een fout optreedt. Voor sommige toepassingen is het echter mogelijk dat er geen gegevens verloren gaan. Een ontwikkelaar van een toepassing kan deze essentiële updates beveiligen door de [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) -systeem procedure onmiddellijk aan te roepen nadat de trans actie is doorgevoerd. Aanroepen `sp_wait_for_database_copy_sync` blokkeert de aanroepende thread totdat de laatste vastgelegde trans actie is verzonden naar de secundaire data base. Er wordt echter niet gewacht tot de verzonden trans acties moeten worden herhaald en op de secundaire moeten worden doorgevoerd. `sp_wait_for_database_copy_sync` ligt binnen het bereik van een specifieke koppeling voor doorlopende kopieën. Elke gebruiker met de verbindings rechten voor de primaire data base kan deze procedure aanroepen.

> [!NOTE]
> `sp_wait_for_database_copy_sync` voor komt gegevens verlies na een failover, maar biedt geen volledige synchronisatie voor lees toegang. De vertraging die wordt veroorzaakt door een `sp_wait_for_database_copy_sync` procedure aanroep kan aanzienlijk zijn en is afhankelijk van de grootte van het transactie logboek op het moment van de aanroep.

## <a name="failover-groups-and-point-in-time-restore"></a>Failover-groepen en herstel naar een bepaald tijdstip

Zie Point-in-time [herstel (PITR)](recovery-using-backups.md#point-in-time-restore)voor informatie over het gebruik van herstel naar een bepaald tijdstip met failover-groepen.

## <a name="limitations-of-failover-groups"></a>Beperkingen van failover-groepen

Houd rekening met de volgende beperkingen:

- Failover-groepen kunnen niet worden gemaakt tussen twee servers of exemplaren in dezelfde Azure-regio's.
- De naam van failover-groepen kan niet worden gewijzigd. U moet de groep verwijderen en opnieuw maken met een andere naam.
- De naam van een Data Base wordt niet ondersteund voor exemplaren in een failovergroep. U moet een failovergroep tijdelijk verwijderen om de naam van een Data Base te kunnen wijzigen.
- Systeem databases worden niet gerepliceerd naar het secundaire exemplaar in een failovergroep. Daarom zijn scenario's die afhankelijk zijn van objecten van de systeem databases niet mogelijk op het secundaire exemplaar, tenzij de objecten hand matig op de secundaire instantie worden gemaakt.

## <a name="programmatically-managing-failover-groups"></a>Programmatisch beheer van failover-groepen

Zoals eerder besproken, kunnen automatische failover-groepen en actieve geo-replicatie ook programmatisch worden beheerd met behulp van Azure PowerShell en de REST API. De volgende tabellen bevatten een beschrijving van de beschik bare opdrachten. Actieve geo-replicatie bevat een set Azure Resource Manager Api's voor beheer, met inbegrip van de [Azure SQL database-rest API](/rest/api/sql/) en [Azure PowerShell-cmdlets](/powershell/azure/). Deze Api's vereisen het gebruik van resource groepen en bieden beveiliging op basis van rollen (RBAC). Zie voor meer informatie over het implementeren van toegangs rollen [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="manage-sql-database-failover"></a>SQL Database failover beheren

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Beschrijving |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Met deze opdracht maakt u een failovergroep en registreert u deze op de primaire en secundaire servers|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Hiermee wordt een failovergroep van de server verwijderd |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hiermee wordt de configuratie van een failovergroep opgehaald |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Hiermee wijzigt u de configuratie van een failovergroep |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Hiermee wordt een failover van een failovergroep naar de secundaire server geactiveerd |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Voegt een of meer data bases toe aan een failovergroep|

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

| Opdracht | Beschrijving |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Met deze opdracht maakt u een failovergroep en registreert u deze op de primaire en secundaire servers|
| [AZ SQL failover-Group Delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Hiermee wordt een failovergroep van de server verwijderd |
| [AZ SQL failover-Group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Hiermee wordt een configuratie van een failovergroep opgehaald |
| [AZ SQL failover-Group Update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Hiermee wordt de configuratie van een failovergroep gewijzigd en/of worden een of meer data bases toegevoegd aan een failovergroep|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Hiermee wordt een failover van een failovergroep naar de secundaire server geactiveerd |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | Beschrijving |
| --- | --- |
| [Failovergroep maken of bijwerken](/rest/api/sql/failovergroups/createorupdate) | Hiermee wordt een failovergroep gemaakt of bijgewerkt |
| [Failovergroep verwijderen](/rest/api/sql/failovergroups/delete) | Hiermee wordt een failovergroep van de server verwijderd |
| [Failover (gepland)](/rest/api/sql/failovergroups/failover) | De failover van de huidige primaire server naar de secundaire server met volledige gegevens synchronisatie wordt geactiveerd.|
| [Forceren van gegevens verlies door failover toestaan](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Hiermee wordt een failover van de huidige primaire server naar de secundaire server geactiveerd zonder dat er gegevens worden gesynchroniseerd. Deze bewerking kan leiden tot verlies van gegevens. |
| [Failovergroep ophalen](/rest/api/sql/failovergroups/get) | Hiermee wordt de configuratie van een failover-groep opgehaald. |
| [Failover-groepen weer geven per server](/rest/api/sql/failovergroups/listbyserver) | Geeft een lijst van de failover-groepen op een server. |
| [Failovergroep bijwerken](/rest/api/sql/failovergroups/update) | Hiermee wordt de configuratie van een failovergroep bijgewerkt. |

---

### <a name="manage-sql-managed-instance-failover"></a>Failover van SQL Managed instance beheren


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | Beschrijving |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Met deze opdracht maakt u een failovergroep en registreert u deze op zowel primaire als secundaire exemplaren|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Hiermee wijzigt u de configuratie van een failovergroep|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Hiermee wordt de configuratie van een failovergroep opgehaald|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Hiermee wordt een failover van een failovergroep naar het secundaire exemplaar geactiveerd|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Hiermee verwijdert u een failovergroep|


# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

| Opdracht | Beschrijving |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az-sql-failover-group-create) |Met deze opdracht maakt u een failovergroep en registreert u deze op de primaire en secundaire servers|
| [AZ SQL failover-Group Delete](/cli/azure/sql/failover-group#az-sql-failover-group-delete) | Hiermee wordt een failovergroep van de server verwijderd |
| [AZ SQL failover-Group show](/cli/azure/sql/failover-group#az-sql-failover-group-show) | Hiermee wordt een configuratie van een failovergroep opgehaald |
| [AZ SQL failover-Group Update](/cli/azure/sql/failover-group#az-sql-failover-group-update) |Hiermee wordt de configuratie van een failovergroep gewijzigd en/of worden een of meer data bases toegevoegd aan een failovergroep|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az-sql-failover-group-set-primary) | Hiermee wordt een failover van een failovergroep naar de secundaire server geactiveerd |

# <a name="rest-api"></a>[Rest API](#tab/rest-api)

| API | Beschrijving |
| --- | --- |
| [Failovergroep maken of bijwerken](/rest/api/sql/instancefailovergroups/createorupdate) | Hiermee wordt de configuratie van een failovergroep gemaakt of bijgewerkt |
| [Failovergroep verwijderen](/rest/api/sql/instancefailovergroups/delete) | Hiermee verwijdert u een failover-groep uit het exemplaar |
| [Failover (gepland)](/rest/api/sql/instancefailovergroups/failover) | Hiermee wordt een failover van het huidige primaire exemplaar naar dit exemplaar geactiveerd met volledige gegevens synchronisatie. |
| [Forceren van gegevens verlies door failover toestaan](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Hiermee wordt een failover van het huidige primaire exemplaar naar het secundaire exemplaar geactiveerd zonder dat de gegevens worden gesynchroniseerd. Deze bewerking kan leiden tot verlies van gegevens. |
| [Failovergroep ophalen](/rest/api/sql/instancefailovergroups/get) | Hiermee wordt de configuratie van een failover-groep opgehaald. |
| [Failover-groepen weer geven-lijst per locatie](/rest/api/sql/instancefailovergroups/listbylocation) | Hiermee worden de failover-groepen op een locatie weer gegeven. |

---

## <a name="next-steps"></a>Volgende stappen

- Zie voor gedetailleerde zelf studies
  - [SQL Database toevoegen aan een failovergroep](failover-group-add-single-database-tutorial.md)
  - [Een elastische pool aan een failovergroep toevoegen](failover-group-add-elastic-pool-tutorial.md)
  - [Een door SQL beheerd exemplaar toevoegen aan een failovergroep](../managed-instance/failover-group-add-instance-tutorial.md)
- Zie voor voorbeeld scripts:
  - [Power shell gebruiken voor het configureren van actieve geo-replicatie voor Azure SQL Database](scripts/setup-geodr-and-failover-database-powershell.md)
  - [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor een pooldatabase in Azure SQL Database](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [Power shell gebruiken om een Azure SQL Database toe te voegen aan een failovergroep](scripts/add-database-to-failover-group-powershell.md)
- Zie [overzicht van bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md) voor een overzicht en scenario's voor bedrijfs continuïteit
- Zie [SQL database automatische back-ups](automated-backups-overview.md)voor meer informatie over Azure SQL database automatische back-ups.
- Zie [een Data Base herstellen vanuit de door de service geïnitieerde back-ups](recovery-using-backups.md)voor meer informatie over het gebruik van automatische back-ups voor herstel.
- Zie [SQL database beveiliging na nood herstel](active-geo-replication-security-configure.md)voor meer informatie over de verificatie vereisten voor een nieuwe primaire server en data base.
