---
title: Actieve geo-replicatie
description: Gebruik actieve geo-replicatie om Lees bare secundaire data bases te maken van afzonderlijke data bases in Azure SQL Database in dezelfde of verschillende datacenter regio's.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 08/27/2020
ms.openlocfilehash: 3a678f6280b5f2d0fd372e75bfbeb6eb2e9b1577
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634291"
---
# <a name="creating-and-using-active-geo-replication---azure-sql-database"></a>Actieve geo-replicatie-Azure SQL Database maken en gebruiken
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Actieve geo-replicatie is een Azure SQL Database functie waarmee u lees bare secundaire data bases kunt maken van afzonderlijke data bases op een server in hetzelfde of een ander Data Center (regio).

> [!NOTE]
> Actieve geo-replicatie wordt niet ondersteund door Azure SQL Managed instance. Gebruik [groepen voor automatische failover](auto-failover-group-overview.md)voor geografische failover van exemplaren van SQL Managed instance.

> [!NOTE]
> Zie [SQL database migreren met behulp van actieve geo-replicatie](../../germany/germany-migration-databases.md#migrate-sql-database-using-active-geo-replication)om SQL-data bases te migreren vanuit Azure Duitsland met behulp van actieve geo-replicatie.

Actieve geo-replicatie is ontworpen als een oplossing voor bedrijfs continuïteit waarmee de toepassing snel herstel van afzonderlijke data bases kan uitvoeren in het geval van een regionale storing of een grote schaal onderbreking. Als geo-replicatie is ingeschakeld, kan de toepassing failover initiëren naar een secundaire data base in een andere Azure-regio. Maxi maal vier secundaire, worden in dezelfde of verschillende regio's ondersteund, en de secundairen kunnen ook worden gebruikt voor alleen-lezen toegang query's. De failover moet hand matig worden geïnitieerd door de toepassing of de gebruiker. Na een failover heeft de nieuwe primaire een ander eind punt voor de verbinding.

> [!NOTE]
> Actieve geo-replicatie repliceert wijzigingen door het transactie logboek van de streaming-data base. Het is niet gerelateerd aan [transactionele replicatie](/sql/relational-databases/replication/transactional/transactional-replication), waardoor wijzigingen worden GEREPLICEERD door DML-opdrachten (insert, update, Delete) uit te voeren.

Het volgende diagram illustreert een typische configuratie van een geo-redundante Cloud toepassing met behulp van actieve geo-replicatie.

![actieve geo-replicatie](./media/active-geo-replication-overview/geo-replication.png )

> [!IMPORTANT]
> SQL Database biedt ook ondersteuning voor groepen met automatische failover. Zie voor meer informatie gebruik van [groepen voor automatische failover](auto-failover-group-overview.md).

Als uw primaire data base niet kan worden uitgevoerd of alleen offline moet worden gehaald, kunt u een failover initiëren naar een van de secundaire data bases. Wanneer failover wordt geactiveerd voor een van de secundaire data bases, worden alle andere secundairen automatisch gekoppeld aan de nieuwe primaire.

U kunt de replicatie en failover van een afzonderlijke data base of een set met data bases op een server of in een elastische pool beheren door gebruik te maken van actieve geo-replicatie. U kunt dit doen met:

- De [Azure Portal](active-geo-replication-configure-portal.md)
- [Power shell: één data base](scripts/setup-geodr-and-failover-database-powershell.md)
- [Power shell: elastische pool](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL: Eén data base of elastische pool](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: Eén data base](/rest/api/sql/replicationlinks)

Actieve geo-replicatie maakt gebruik van de AlwaysOn- [beschikbaarheids groep](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technologie van de data base-engine om op asynchrone wijze doorgevoerde trans acties op de primaire data base te repliceren naar een secundaire data base met behulp van snap shot-isolatie. Automatische-failover-groepen bieden de groeps semantiek boven op actieve geo-replicatie, maar hetzelfde mechanisme voor asynchrone replicatie wordt gebruikt. Hoewel de secundaire Data Base op een bepaald moment mogelijk iets achter de primaire data base ligt, worden de secundaire gegevens gegarandeerd nooit gedeeltelijk trans acties. Dankzij de redundantie van meerdere regio's kunnen toepassingen snel worden hersteld van een permanent verlies van een volledig Data Center of delen van een Data Center, veroorzaakt door natuur rampen, fatale menselijke fouten of kwaad aardige handelingen. De specifieke RPO-gegevens kunt u vinden in [overzicht van bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md).

> [!NOTE]
> Als er sprake is van een netwerk fout tussen twee regio's, wordt elke 10 seconden opnieuw geprobeerd verbindingen tot stand te brengen.

> [!IMPORTANT]
> Om ervoor te zorgen dat een kritieke wijziging op de primaire Data Base naar een secundair wordt gerepliceerd voordat u een failover doorvoert, kunt u de synchronisatie afdwingen om te zorgen voor de replicatie van belang rijke wijzigingen (bijvoorbeeld wachtwoord updates). Geforceerde synchronisatie is van invloed op de prestaties omdat het de aanroepende thread blokkeert totdat alle doorgevoerde trans acties worden gerepliceerd. Zie [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync)voor meer informatie. Zie [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database)voor het bewaken van de replicatie vertraging tussen de primaire data base en geo-secundair.

In de volgende afbeelding ziet u een voor beeld van actieve geo-replicatie die is geconfigureerd met een primaire waarde in de regio Noord-Centraal VS en secundair in de regio Zuid-Centraal vs.

![Geo-replicatie relatie](./media/active-geo-replication-overview/geo-replication-relationship.png)

Omdat de secundaire data bases leesbaar zijn, kunnen ze worden gebruikt voor het offloaden van alleen-lezen werk belastingen, zoals rapportage taken. Als u actieve geo-replicatie gebruikt, is het mogelijk om de secundaire data base in dezelfde regio te maken met de primaire, maar wordt de tolerantie van de toepassing niet verhoogd naar fatale fouten. Als u gebruikmaakt van groepen voor automatische failover, wordt de secundaire data base altijd in een andere regio gemaakt.

Naast herstel na nood geval kan geo-replicatie worden gebruikt in de volgende scenario's:

- **Database migratie**: u kunt actieve geo-replicatie gebruiken om een Data Base te migreren van de ene server naar een andere online met minimale downtime.
- **Toepassings upgrades**: u kunt een extra secundair als een failback-kopie maken tijdens de upgrade van de toepassing.

Voor een echte bedrijfs continuïteit is het toevoegen van database redundantie tussen data centers slechts een deel van de oplossing. Het herstellen van een toepassing (Service) End-to-end na een onherstelbare fout vereist het herstellen van alle onderdelen die de service en eventuele afhankelijke services vormen. Voor beelden van deze onderdelen zijn de client software (bijvoorbeeld een browser met een aangepaste Java script), web-front-ends, opslag en DNS. Het is van essentieel belang dat alle onderdelen zich in dezelfde storingen bevinden en beschikbaar komen binnen de beoogde herstel tijd (RTO) van uw toepassing. Daarom moet u alle afhankelijke services identificeren en inzicht krijgen in de garanties en mogelijkheden die ze bieden. Vervolgens moet u de juiste stappen nemen om ervoor te zorgen dat uw service functioneert tijdens de failover van de services waarvan deze afhankelijk is. Zie [cloud oplossingen ontwerpen voor herstel na nood gevallen met actieve geo-replicatie](designing-cloud-solutions-for-disaster-recovery.md)voor meer informatie over het ontwerpen van oplossingen voor herstel na nood gevallen.

## <a name="active-geo-replication-terminology-and-capabilities"></a>De terminologie en mogelijkheden van actieve geo-replicatie

- **Automatische asynchrone replicatie**

  U kunt alleen een secundaire data base maken door toe te voegen aan een bestaande data base. De secundaire kan op elke server worden gemaakt. Nadat de secundaire data base is gemaakt, wordt deze ingevuld met de gegevens die zijn gekopieerd uit de primaire data base. Dit proces wordt seeding genoemd. Nadat de secundaire data base is gemaakt en geseedd, worden updates van de primaire data base asynchroon gerepliceerd naar de secundaire data base. Asynchrone replicatie betekent dat trans acties worden doorgevoerd op de primaire Data Base voordat ze worden gerepliceerd naar de secundaire data base.

- **Lees bare secundaire data bases**

  Een toepassing kan toegang krijgen tot een secundaire Data Base voor alleen-lezen bewerkingen met dezelfde of een andere beveiligings-principal die wordt gebruikt voor toegang tot de primaire data base. De secundaire data bases worden in de isolatie modus voor moment opnamen uitgevoerd om ervoor te zorgen dat de replicatie van de updates van de primaire (logboek herhaling) niet wordt vertraagd door query's die worden uitgevoerd op de secundaire.

> [!NOTE]
> De replay van het logboek wordt uitgesteld op de secundaire Data Base als er schema-updates zijn voor de primaire. Hiervoor is een schema vergrendeling vereist voor de secundaire data base.

> [!IMPORTANT]
> U kunt geo-replicatie gebruiken om een secundaire data base te maken in dezelfde regio als die van de primaire. U kunt deze secundaire gebruiken om taken te verdelen over een alleen-lezen werk belasting in dezelfde regio. Een secundaire data base in dezelfde regio biedt echter geen extra fout tolerantie en is daarom geen geschikt failover-doel voor herstel na nood gevallen. Er wordt ook geen isolatie van de beschik bare zone gegarandeerd. Gebruik bedrijfs kritieke of Premium-servicelaag met [zone-redundante configuratie](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) of algemeen [zone met een redundante configuratie](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) van de servicelaag voor de beschik baarheid van de zone.
>

- **Geplande failover**

  Met de geplande failover worden de rollen van de primaire en secundaire data bases overgeschakeld nadat de volledige synchronisatie is voltooid. Het is een online bewerking die geen gegevens verlies tot gevolg heeft. De tijd van de bewerking is afhankelijk van de grootte van het transactie logboek op de primaire waarde die moet worden gesynchroniseerd. Geplande failover is ontworpen voor de volgende scenario's: (a) voor het uitvoeren van DR-oefeningen in productie wanneer het gegevens verlies niet acceptabel is; (b) om de Data Base naar een andere regio te verplaatsen; en (c) om de data base te retour neren naar de primaire regio nadat de storing is verholpen (failback).

- **Niet-geplande failover**

  Bij een niet-geplande of geforceerde failover wordt de secundaire functie direct overgeschakeld naar de primaire rol zonder synchronisatie met de primaire. Trans acties die zijn toegewezen aan de primaire maar niet zijn gerepliceerd naar de secundaire, gaan verloren. Deze bewerking is ontworpen als herstel methode tijdens storingen wanneer de primaire niet toegankelijk is, maar de beschik baarheid van de data base moet snel worden hersteld. Wanneer de oorspronkelijke primaire primair weer online is, wordt de verbinding automatisch opnieuw tot stand gebracht en wordt er een nieuwe secundaire. Alle niet-gesynchroniseerde trans acties vóór de failover blijven behouden in het back-upbestand, maar worden niet gesynchroniseerd met de nieuwe primaire om conflicten te voor komen. Deze trans acties moeten hand matig worden samengevoegd met de meest recente versie van de primaire data base.

- **Meerdere Lees bare secundaire zones**

  Er kunnen Maxi maal vier secundaire data bases worden gemaakt voor elke primaire. Als er slechts één secundaire data base is en deze mislukt, wordt de toepassing blootgesteld aan een hoger risico totdat een nieuwe secundaire data base wordt gemaakt. Als er meerdere secundaire data bases bestaan, blijft de toepassing beveiligd, zelfs als een van de secundaire data bases mislukt. De extra secundairen kunnen ook worden gebruikt om de alleen-lezen werk belastingen uit te breiden

  > [!NOTE]
  > Als u actieve geo-replicatie gebruikt voor het bouwen van een wereld wijd gedistribueerde toepassing en alleen-lezen toegang tot gegevens in meer dan vier regio's nodig hebt, kunt u secundaire van een secundaire maken (een proces dat wordt aangeduid als koppeling). Op deze manier kunt u de schaal van database replicatie bijna onbeperkt aanpassen. Daarnaast vermindert het koppelen van de overhead van replicatie van de primaire data base. De afweging is de verhoogde replicatie vertraging op de Blade van de meeste secundaire data bases.

- **Geo-replicatie van data bases in een elastische pool**

  Elke secundaire data base kan afzonderlijk deel nemen aan een elastische pool of zich helemaal niet in een elastische pool bevindt. De keuze van de groep voor elke secundaire data base is gescheiden en is niet afhankelijk van de configuratie van een andere secundaire data base (primair of secundair). Elke elastische pool bevindt zich in één regio, waardoor meerdere secundaire data bases in dezelfde topologie nooit een elastische pool kunnen delen.

- **Door de gebruiker beheerde failover en failback**

  Een secundaire data base kan op elk gewenst moment door de toepassing of de gebruiker expliciet worden overgeschakeld naar de primaire rol. Tijdens een echte onderbreking moet u de optie ' ongepland ' gebruiken, die direct een secundaire is om de primaire te zijn. Wanneer de mislukte primaire herstel bewerking opnieuw beschikbaar is, markeert het systeem automatisch de herstelde primaire als secundaire en wordt deze up-to-date met de nieuwe primaire. Als gevolg van de asynchrone aard van de replicatie, kan er tijdens ongeplande failovers een kleine hoeveelheid gegevens verloren gaan als er een primaire fout optreedt voordat de meest recente wijzigingen worden gerepliceerd naar de secundaire. Wanneer een primaire met meerdere secundaire zones een failover uitvoeren, worden de replicatie relaties automatisch opnieuw geconfigureerd door het systeem en worden de resterende secundairen gekoppeld aan de zojuist gepromoveerde primaire, zonder dat er tussen komst van de gebruiker is vereist. Nadat de storing die de failover heeft veroorzaakt, is verholpen, kan het wenselijk zijn om de toepassing te retour neren naar de primaire regio. Hiertoe moet de opdracht failover worden aangeroepen met de optie gepland.

## <a name="preparing-secondary-database-for-failover"></a>Secundaire data base voorbereiden voor failover

Zorg ervoor dat de verificatie vereisten voor de secundaire server en de data base correct zijn geconfigureerd om ervoor te zorgen dat uw toepassing direct toegang heeft tot de nieuwe primaire na een failover. Zie [SQL database Security na nood herstel](active-geo-replication-security-configure.md)voor meer informatie. Zorg ervoor dat het Bewaar beleid voor back-ups op de secundaire data base overeenkomt met die van de primaire gegevens bank om de naleving na een failover te garanderen. Deze instellingen maken geen deel uit van de data base en worden niet gerepliceerd. Standaard wordt het secundaire geconfigureerd met een standaard PITR-Bewaar periode van zeven dagen. Zie [SQL database automatische back-ups](automated-backups-overview.md)voor meer informatie.

> [!IMPORTANT]
> Als uw data base lid is van een failovergroep, kunt u de failover niet starten met behulp van de opdracht voor de failover van geo-replicatie. Gebruik de opdracht failover voor de groep. Als u een afzonderlijke Data Base wilt failover, moet u deze eerst verwijderen uit de groep failover. Zie  [failover-groepen](auto-failover-group-overview.md) voor meer informatie.

## <a name="configuring-secondary-database"></a>Secundaire data base configureren

Zowel de primaire als de secundaire data base moeten dezelfde servicelaag hebben. Het wordt ook ten zeerste aanbevolen om de secundaire data base te maken met dezelfde back-upopslag en de berekenings grootte (Dtu's of vCores) als de primaire. Als de primaire Data Base een zware schrijf werk belasting ondervindt, kan het zijn dat een secundaire met een lagere reken capaciteit deze niet kan blijven gebruiken. Dit leidt tot een vertraging opnieuw op de secundaire, en potentiële onbeschikbaarheid van de secundaire. Om deze Risico's te beperken, wordt de limiet voor het transactie logboek van de primaire replicatie zo nodig beperkt.

Een ander gevolg van een niet-sluitende secundaire configuratie is dat na een failover de prestaties van de toepassing kunnen afnemen vanwege onvoldoende reken capaciteit van de nieuwe primaire. In dat geval is het nood zakelijk om de database service doelstelling te schalen naar het benodigde niveau. Dit kan aanzienlijke tijd en reken bronnen duren, en er is een failover voor [hoge Beschik baarheid](high-availability-sla.md) aan het eind van het opruimings proces vereist.

Als u besluit om de secundaire te maken met een lagere reken grootte, biedt de grafiek IO-percentage in Azure Portal een goede manier om de minimale reken grootte van de secundaire te schatten die nodig is om de replicatie te laden. Als uw primaire data base bijvoorbeeld P6 (1000 DTU) is en het schrijf percentage van het logboek 50% is, moet het secundaire bedrijf ten minste P4 (500 DTU) zijn. Als u historische logboek-IO-gegevens wilt ophalen, gebruikt u de weer gave [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) . Als u recente logboek Schrijf gegevens wilt ophalen met een grotere granulariteit die beter aansluit op korte-termijn pieken in de logboek frequentie, gebruikt u [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) weer gave.

De frequentie beperking van transactie logboeken op de primaire waarde als gevolg van een lagere reken grootte op een secundair wordt gerapporteerd met behulp van het HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO wacht type, zichtbaar in de [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) en [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) database weergaven.

Standaard is de redundantie opslag ruimte van de back-up van de secundaire data base hetzelfde als die van de hoofd database. U kunt ervoor kiezen om de secundaire te configureren met een andere opslag redundantie voor back-ups. Back-ups worden altijd uitgevoerd op de primaire data base. Als de secundaire is geconfigureerd met een andere redundantie voor back-upopslag, worden back-ups gefactureerd op basis van de opslag redundantie die is geselecteerd op de nieuwe primaire (vorige secundaire) nadat de failover is gepromoveerd tot de primaire. 

> [!NOTE]
> Het transactie logboek op de primaire waarde kan worden beperkt om redenen die niet te maken hebben met een lagere reken grootte op een secundaire. Dit type beperking kan optreden, zelfs als de secundaire computer dezelfde of een hogere reken grootte heeft dan de primaire. Zie [Trans Action log rate governance](resource-limits-logical-server.md#transaction-log-rate-governance)(Engelstalig) voor meer informatie, waaronder wacht typen voor verschillende soorten logboek frequentie beperking.

> [!NOTE]
> Azure SQL Database Configureer bare back-upopslag redundantie is momenteel beschikbaar in de open bare preview-versie van Brazilië-zuid en is algemeen alleen beschikbaar in de Azure-regio Zuidoost-Azië. Wanneer de bron database is gemaakt met lokaal redundante of zone redundante back-upopslag redundantie, wordt het maken van een secundaire data base in een andere Azure-regio niet ondersteund. 

Zie [Wat zijn SQL database service lagen](purchasing-models.md)voor meer informatie over de SQL database Compute sizes.

## <a name="cross-subscription-geo-replication"></a>Geo-replicatie tussen verschillende abonnementen

Voor het instellen van actieve geo-replicatie tussen twee data bases die deel uitmaken van verschillende abonnementen (of onder dezelfde Tenant of niet), moet u de speciale procedure volgen die in deze sectie wordt beschreven.  De procedure is gebaseerd op SQL-opdrachten en vereist:

- Het maken van een geprivilegieerde aanmelding op beide servers
- Het IP-adres toevoegen aan de acceptatie lijst van de client die de wijziging op beide servers uitvoert (zoals het IP-adres van de host waarop SQL Server Management Studio).

De client die de wijzigingen uitvoert, heeft netwerk toegang nodig tot de primaire server. Hoewel hetzelfde IP-adres van de client moet worden toegevoegd aan de acceptatie lijst op de secundaire server, is de netwerk verbinding met de secundaire server niet strikt vereist.

### <a name="on-the-master-of-the-primary-server"></a>Op de master van de primaire server

1. Voeg het IP-adres toe aan de acceptatie lijst van de client die de wijzigingen uitvoert (Zie [firewall configureren](firewall-configure.md)) voor meer informatie.
1. Maak een aanmelding die specifiek is ingesteld voor het instellen van actieve geo-replicatie (en pas de referenties indien nodig aan):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Maak een bijbehorende gebruiker en wijs deze toe aan de DBManager-rol:

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. Let op de SID van de nieuwe aanmelding met behulp van deze query:

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>Op de bron database op de primaire server

1. Een gebruiker maken voor dezelfde aanmelding:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Voeg de gebruiker toe aan de db_owner rol:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>Op de master van de secundaire server

1. Voeg het IP-adres van de client toe aan de lijst met toegestane clients onder firewall regels voor de secundaire server. Controleer of precies hetzelfde client-IP-adres dat is toegevoegd op de primaire server ook is toegevoegd aan het secundaire. Dit is een vereiste stap die moet worden uitgevoerd voordat u de opdracht ALTER data base secundair toevoegen uitvoert om geo-replicatie te initiëren.

1. Maak dezelfde aanmelding als op de primaire server met hetzelfde wacht woord voor de gebruikers naam en SID:

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Maak een bijbehorende gebruiker en wijs deze toe aan de DBManager-rol:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>Op de master van de primaire server

1. Meld u aan bij de master van de primaire server met de nieuwe aanmelding.
1. Maak een secundaire replica van de bron database op de secundaire server (Wijzig de database naam en servername indien nodig):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

Na de eerste installatie kunnen de gemaakte gebruikers, aanmeldingen en firewall regels worden verwijderd.

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>Referenties en firewall regels synchroon houden

U wordt aangeraden [IP-firewall regels op database niveau](firewall-configure.md) te gebruiken voor geo-gerepliceerde data bases, zodat deze regels kunnen worden gerepliceerd met de-data base om ervoor te zorgen dat alle secundaire data bases dezelfde IP-firewall regels hebben als de primaire. Deze aanpak elimineert dat klanten niet hand matig firewall regels hoeven te configureren en onderhouden op servers die de primaire en secundaire data bases hosten. Op dezelfde manier zorgt u er bij het gebruik van [Inge sloten database gebruikers](logins-create-manage.md) voor gegevens toegang voor dat zowel de primaire als de secundaire data base altijd dezelfde gebruikers referenties hebben tijdens een failover, er geen onderbrekingen zijn veroorzaakt door niet-overeenkomende aanmeldingen en wacht woorden. Met de toevoeging van [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)kunnen klanten gebruikers toegang tot zowel primaire als secundaire data bases beheren en de nood zaak voor het beheer van referenties in data bases verwijderen.

## <a name="upgrading-or-downgrading-primary-database"></a>Primaire data base bijwerken of downgrade uitvoeren

U kunt een upgrade of downgrade van een primaire Data Base naar een andere reken grootte (binnen dezelfde servicelaag, niet tussen Algemeen en Bedrijfskritiek) zonder de verbinding van secundaire data bases te verbreken. Wanneer u een upgrade uitvoert, raden we u aan eerst de secundaire data base bij te werken en vervolgens een upgrade uit te voeren van de primaire. Als u een downgrade wilt uitvoeren, doet u dat in omgekeerde volgorde: downgrade eerst de primaire en vervolgens de secundaire. Bij het upgraden of downgraden van de database naar een andere servicelaag wordt deze aanbeveling afgedwongen.

> [!NOTE]
> Als u een secundaire database hebt gemaakt als onderdeel van de configuratie van de failovergroep, wordt het niet aanbevolen de secundaire database te downgraden. Zo zorgt u ervoor dat uw gegevenslaag voldoende capaciteit heeft om uw normale werk belasting te verwerken nadat de failover is geactiveerd.

> [!IMPORTANT]
> De primaire database in een failovergroep kan niet naar een hogere laag worden geschaald, tenzij eerst de secundaire database naar de hogere laag wordt geschaald. Als u de primaire data base probeert te schalen voordat de secundaire data base is geschaald, wordt mogelijk de volgende fout weer gegeven:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>Het verlies van kritieke gegevens voor komen

Vanwege de hoge latentie van Wide Area Networks, gebruikt doorlopende kopieën een mechanisme voor asynchrone replicatie. Asynchrone replicatie maakt enkele gegevens verlies onvermijdbaar als er een fout optreedt. Voor sommige toepassingen is het echter mogelijk dat er geen gegevens verloren gaan. Een ontwikkelaar van een toepassing kan deze essentiële updates beveiligen door de [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) -systeem procedure onmiddellijk aan te roepen nadat de trans actie is doorgevoerd. Het aanroepen van **sp_wait_for_database_copy_sync** blokkeert de aanroepende thread totdat de laatste vastgelegde trans actie is verzonden naar de secundaire data base. Er wordt echter niet gewacht tot de verzonden trans acties moeten worden herhaald en op de secundaire moeten worden doorgevoerd. **sp_wait_for_database_copy_sync** ligt binnen het bereik van een specifieke koppeling voor doorlopende kopieën. Elke gebruiker met de verbindings rechten voor de primaire data base kan deze procedure aanroepen.

> [!NOTE]
> **sp_wait_for_database_copy_sync** voor komt gegevens verlies na een failover, maar biedt geen volledige synchronisatie voor lees toegang. De vertraging veroorzaakt door een **sp_wait_for_database_copy_sync** procedure aanroep kan aanzienlijk zijn en is afhankelijk van de grootte van het transactie logboek op het moment van de aanroep.

## <a name="monitoring-geo-replication-lag"></a>Vertraging van geo-replicatie bewaken

Als u de vertraging met betrekking tot RPO wilt bewaken, gebruikt u *replication_lag_sec* kolom van [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) op de primaire data base. Er wordt een vertraging in seconden weer gegeven tussen de trans acties die zijn doorgevoerd op de primaire en persistent gemaakt op de secundaire. Bijvoorbeeld Als de waarde van de vertraging 1 seconde is, betekent dit dat als de primaire wordt beïnvloed door een onderbreking op dit moment en failover wordt gestart, 1 seconde van de meest recente overgangen niet wordt opgeslagen.

Als u de vertraging wilt meten met betrekking tot wijzigingen op de primaire data base die zijn toegepast op het secundaire, dat wil zeggen, de *last_commit* tijd op de secundaire data base vergelijken met dezelfde waarde op de primaire data base.

> [!NOTE]
> Soms *replication_lag_sec* op de primaire Data Base een null-waarde, wat betekent dat het primaire op dit moment niet weet hoe ver de secundaire is.   Dit gebeurt meestal nadat het proces opnieuw is gestart en een tijdelijke voor waarde moet zijn. Overweeg om de toepassing te waarschuwen als de *replication_lag_sec* gedurende lange tijd Null retourneert. Dit geeft aan dat de secundaire data base niet kan communiceren met de primaire database vanwege een permanente verbindings fout. Er zijn ook omstandigheden die ertoe kunnen leiden dat het verschil tussen *last_commit* tijd op de secundaire data base groot wordt. Bijvoorbeeld Als een commit-bewerking wordt uitgevoerd op de primaire waarde na een lange periode van geen wijzigingen, springt het verschil naar een grote waarden voordat u snel terugkeert naar 0. Houd er rekening mee dat er een fout optreedt wanneer het verschil tussen deze twee waarden gedurende een lange periode groot blijft.

## <a name="programmatically-managing-active-geo-replication"></a>Programmatisch beheer van actieve geo-replicatie

Zoals eerder besproken, kan actieve geo-replicatie ook programmatisch worden beheerd met behulp van Azure PowerShell en de REST API. De volgende tabellen bevatten een beschrijving van de beschik bare opdrachten. Actieve geo-replicatie bevat een set Azure Resource Manager Api's voor beheer, met inbegrip van de [Azure SQL database-rest API](/rest/api/sql/) en [Azure PowerShell-cmdlets](/powershell/azure/). Deze Api's vereisen het gebruik van resource groepen en bieden ondersteuning voor Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Zie voor meer informatie over het implementeren van toegangs rollen [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: failover van één en gepoolde data bases beheren

> [!IMPORTANT]
> Deze Transact-SQL-opdrachten zijn alleen van toepassing op actieve geo-replicatie en zijn niet van toepassing op failover-groepen. Daarom zijn ze ook niet van toepassing op exemplaren van een SQL-beheerd exemplaar, aangezien ze alleen failover-groepen ondersteunen.

| Opdracht | Beschrijving |
| --- | --- |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Gebruik het argument secundair op SERVER toevoegen om een secundaire Data Base voor een bestaande Data Base te maken en gegevens replicatie te starten |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |FAILOVER of FORCE_FAILOVER_ALLOW_DATA_LOSS gebruiken om naar een secundaire data base te scha kelen die primair moet zijn om FAILOVER te initiëren |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Gebruik secundaire verwijderen op de SERVER om een gegevens replicatie tussen een SQL Database en de opgegeven secundaire data base te beëindigen. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |Retourneert informatie over alle bestaande replicatie koppelingen voor elke Data Base op een server. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Hiermee worden de laatste replicatie tijd, de laatste replicatie vertraging en andere informatie over de replicatie koppeling voor een bepaalde data base opgehaald. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Hier wordt de status weer gegeven voor alle database bewerkingen, inclusief de status van de replicatie koppelingen. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |zorgt ervoor dat de toepassing wacht totdat alle doorgevoerde trans acties worden gerepliceerd en bevestigd door de actieve secundaire data base. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>Power shell: failover van één en gepoolde data bases beheren

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De module PowerShell Azure Resource Manager wordt nog steeds ondersteund in Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

| Cmdlet | Beschrijving |
| --- | --- |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Hiermee haalt u een of meer databases op. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary) |Hiermee maakt u een secundaire database voor een bestaande database en wordt gegevensreplicatie gestart. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary) |Hiermee wordt overgeschakeld op een secundaire database als primaire om de failover te initiëren. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) |Hiermee wordt gegevensreplicatie tussen een SQL Database en de opgegeven secundaire database beëindigd. |
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Hiermee haalt u de geo-replicatiekoppelingen tussen een Azure SQL Database en een resourcegroep of logische SQL Server op. |
|  | |

> [!IMPORTANT]
> Zie voor voorbeeld scripts [een afzonderlijke data base configureren en failover gebruiken met behulp van actieve geo-replicatie](scripts/setup-geodr-and-failover-database-powershell.md) en [een gegroepeerde Data Base configureren en failoveren met behulp van actieve geo-replicatie](scripts/setup-geodr-and-failover-elastic-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: failover van één en gepoolde data bases beheren

| API | Beschrijving |
| --- | --- |
| [Data base maken of bijwerken (createMode = herstellen)](/rest/api/sql/databases/createorupdate) |Hiermee wordt een primaire of secundaire data base gemaakt, bijgewerkt of teruggezet. |
| [Database status van maken of bijwerken ophalen](/rest/api/sql/databases/createorupdate) |Retourneert de status tijdens het maken van een bewerking. |
| [Secundaire Data Base als primair instellen (geplande failover)](/rest/api/sql/replicationlinks/failover) |Hiermee wordt ingesteld welke secundaire data base primair is door een failover uit te geven van de huidige primaire data base. **Deze optie wordt niet ondersteund voor SQL Managed instance.**|
| [Secundaire Data Base als primair instellen (niet-geplande failover)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Hiermee wordt ingesteld welke secundaire data base primair is door een failover uit te geven van de huidige primaire data base. Deze bewerking kan leiden tot verlies van gegevens. **Deze optie wordt niet ondersteund voor SQL Managed instance.**|
| [Replicatie koppeling ophalen](/rest/api/sql/replicationlinks/get) |Hiermee wordt een specifieke replicatie koppeling voor een bepaalde data base in een geo-replicatie relatie opgehaald. Hiermee wordt de informatie opgehaald die zichtbaar is in de catalogus weergave van sys.geo_replication_links. **Deze optie wordt niet ondersteund voor SQL Managed instance.**|
| [Replicatie koppelingen-lijst op Data Base](/rest/api/sql/replicationlinks/listbydatabase) | Hiermee worden alle replicatie koppelingen voor een bepaalde data base in een geo-replicatie relatie opgehaald. Hiermee wordt de informatie opgehaald die zichtbaar is in de catalogus weergave van sys.geo_replication_links. |
| [Replicatie koppeling verwijderen](/rest/api/sql/replicationlinks/delete) | Hiermee verwijdert u een database replicatie koppeling. Kan niet worden uitgevoerd tijdens failover. |
|  | |




## <a name="next-steps"></a>Volgende stappen

- Zie voor voorbeeld scripts:
  - [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor één Azure SQL-database](scripts/setup-geodr-and-failover-database-powershell.md)
  - [PowerShell gebruiken voor het configureren van actieve geo-replicatie voor een Azure SQL-pooldatabase](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- SQL Database biedt ook ondersteuning voor groepen met automatische failover. Zie voor meer informatie gebruik van [groepen voor automatische failover](auto-failover-group-overview.md).
- Zie [overzicht van bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md) voor een overzicht en scenario's voor bedrijfs continuïteit
- Zie [SQL database automatische back-ups](automated-backups-overview.md)voor meer informatie over Azure SQL database automatische back-ups.
- Zie [een Data Base herstellen vanuit de door de service geïnitieerde back-ups](recovery-using-backups.md)voor meer informatie over het gebruik van automatische back-ups voor herstel.
- Zie [SQL database beveiliging na nood herstel](active-geo-replication-security-configure.md)voor meer informatie over de verificatie vereisten voor een nieuwe primaire server en data base.
