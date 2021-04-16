---
title: Wat is SQL Data Sync voor Azure?
description: Dit overzicht introduceert SQL Data Sync voor Azure, waarmee u gegevens kunt synchroniseren tussen meerdere cloud- en on-premises databases.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync, sqldbrb=1, fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/20/2019
ms.openlocfilehash: 695409740348e78ae51b263b44d9ed1cbadc1054
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531928"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Wat is SQL Data Sync voor Azure?

SQL Data Sync is een service die is gebouwd op Azure SQL Database waarmee u de gegevens die u selecteert in twee richtingen kunt synchroniseren in meerdere databases, zowel on-premises als in de cloud. 

> [!IMPORTANT]
> Azure SQL Data Sync biedt momenteel geen Azure SQL Managed Instance ondersteuning.


## <a name="overview"></a>Overzicht 

Data Sync is gebaseerd op het concept van een synchronisatiegroep. Een synchronisatiegroep is een groep databases die u wilt synchroniseren.

Data Sync gebruikt een hub en spoke-topologie om gegevens te synchroniseren. U definieert een van de databases in de synchronisatiegroep als de hubdatabase. De rest van de databases zijn liddatabases. Synchronisatie vindt alleen plaats tussen de hub en afzonderlijke leden.

- De **hubdatabase** moet een Azure SQL Database.
- De **liddatabases** kunnen databases zijn in Azure SQL Database of in exemplaren van SQL Server.
- De **metagegevensdatabase voor** synchronisatie bevat de metagegevens en het logboek voor Data Sync. De metagegevensdatabase voor synchronisatie moet een Azure SQL Database zich in dezelfde regio bevinden als de hubdatabase. De database met metagegevens van synchronisatie is gemaakt door de klant en eigendom van de klant. U kunt slechts één metagegevensdatabase voor synchronisatie per regio en abonnement hebben. Metagegevensdatabase van synchronisatie kan niet worden verwijderd of hernoemd terwijl er synchronisatiegroepen of synchronisatieagents bestaan. U wordt aangeraden een nieuwe, lege database te maken die als Database met metagegevens van synchronisatie wordt gebruikt. Met Data Sync worden tabellen in deze database gemaakt en een regelmatige workload uitgevoerd.

> [!NOTE]
> Als u een on-premises database als liddatabase gebruikt, moet u een lokale [synchronisatieagent installeren en configureren.](sql-data-sync-sql-server-configure.md#add-on-prem)

![Gegevens tussen databases synchroniseren](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

Een synchronisatiegroep heeft de volgende eigenschappen:

- In **het synchronisatieschema** wordt beschreven welke gegevens worden gesynchroniseerd.
- De **synchronisatierichting** kan in twee richtingen zijn of kan in slechts één richting stromen. Dat wil zeggen dat de synchronisatierichting *Hub naar Lid of* Lid naar *Hub* of beide kan zijn.
- Het **synchronisatie-interval** beschrijft hoe vaak synchronisatie plaatsvindt.
- Het **conflictoplossingsbeleid** is een beleid op groepsniveau. Dit kan *Hub wins of* Member *wins zijn.*

## <a name="when-to-use"></a>Wanneer gebruikt u dit?

Data Sync is handig in gevallen waarin gegevens moeten worden bijgewerkt in verschillende databases in Azure SQL Database of SQL Server. Dit zijn de belangrijkste gebruiksgevallen voor Data Sync:

- **Hybride gegevenssynchronisatie:** Met Data Sync kunt u gegevens gesynchroniseerd houden tussen uw databases in SQL Server en Azure SQL Database hybride toepassingen inschakelen. Deze mogelijkheid kan een beroep doen op klanten die overwegen om over te gaan naar de cloud en een deel van hun toepassing in Azure willen zetten.
- **Gedistribueerde toepassingen:** In veel gevallen is het handig om verschillende workloads over verschillende databases te verdelen. Als u bijvoorbeeld een grote productiedatabase hebt, maar u ook een workload voor rapportage of analyse moet uitvoeren op deze gegevens, is het handig om een tweede database te hebben voor deze extra workload. Deze aanpak minimaliseert de invloed van de prestaties op uw productieworkload. U kunt de Data Sync deze twee databases gesynchroniseerd te houden.
- **Wereldwijd gedistribueerde toepassingen:** Veel bedrijven hebben verschillende regio's en zelfs verschillende landen/regio's. Als u de netwerklatentie wilt minimaliseren, kunt u uw gegevens het beste in een regio bij u in de buurt hebben. Met Data Sync kunt u databases eenvoudig gesynchroniseerd houden in regio's over de hele wereld.

Data Sync is niet de voorkeursoplossing voor de volgende scenario's:

| Scenario | Enkele aanbevolen oplossingen |
|----------|----------------------------|
| Herstel na noodgevallen | [Geografisch redundante back-ups van Azure](automated-backups-overview.md) |
| Leesschaal | [Alleen-lezen replica's gebruiken om alleen-lezen queryworkloads te verdelen (preview)](read-scale-out.md) |
| ETL (OLTP naar OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) of [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) |
| Migratie van SQL Server naar Azure SQL Database. De SQL Data Sync kunnen echter worden gebruikt nadat de migratie is voltooid, om ervoor te zorgen dat de bron en het doel synchroon worden gehouden.  | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-it-works"></a>Uitleg

- **Wijzigingen in gegevens bijhouden:** Data Sync houdt wijzigingen bij met behulp van triggers voor invoegen, bijwerken en verwijderen. De wijzigingen worden vastgelegd in een zijtabel in de gebruikersdatabase. Houd er BULK INSERT dat er standaard geen triggers worden geactiveerd. Als FIRE_TRIGGERS niet is opgegeven, worden er geen invoegtriggers uitgevoerd. Voeg de FIRE_TRIGGERS toe zodat Data Sync invoegingen kunnen volgen. 
- **Gegevens synchroniseren:** Data Sync is ontworpen in een hub en spoke-model. De hub wordt afzonderlijk gesynchroniseerd met elk lid. Wijzigingen van de hub worden gedownload naar het lid en vervolgens worden wijzigingen van het lid geüpload naar de hub.
- **Conflicten oplossen:** Data Sync biedt twee opties voor conflictoplossing: *Hub wins* of *Member wins.*
  - Als u *Hub wins selecteert,* overschrijven de wijzigingen in de hub altijd wijzigingen in het lid.
  - Als u Lid *selecteert, wint*, worden de wijzigingen in het lid overschreven in de hub. Als er meer dan één lid is, is de uiteindelijke waarde afhankelijk van welk lid het eerst wordt gesynchroniseerd.

## <a name="compare-with-transactional-replication"></a>Vergelijken met transactionele replicatie

| | Gegevens synchroniseren | Transactionele replicatie |
|---|---|---|
| **Voordelen** | - Actief/actief-ondersteuning<br/>- Bi-directioneel tussen on-premises en Azure SQL Database | - Lagere latentie<br/>- Transactionele consistentie<br/>- Bestaande topologie opnieuw gebruiken na migratie <br/>-Azure SQL Managed Instance-ondersteuning |
| **Nadelen** | - Geen transactionele consistentie<br/>- Hogere invloed op prestaties | - Kan niet publiceren vanuit Azure SQL Database <br/>- Hoge onderhoudskosten |

## <a name="private-link-for-data-sync-preview"></a>Privékoppeling voor Data Sync (preview)
Met de nieuwe private link-functie (preview) kunt u een door de service beheerd privé-eindpunt kiezen om een beveiligde verbinding tot stand te brengen tussen de synchronisatieservice en uw lid-/hubdatabases tijdens het gegevenssynchronisatieproces. Een door een service beheerd privé-eindpunt is een privé-IP-adres binnen een specifiek virtueel netwerk en subnet. Binnen Data Sync wordt het door de service beheerde privé-eindpunt gemaakt door Microsoft en wordt het uitsluitend gebruikt door de Data Sync-service voor een bepaalde synchronisatiebewerking. Lees de algemene vereisten voor de functie voordat u [de](sql-data-sync-data-sql-server-sql-database.md#general-requirements) privékoppeling instelt. 

![Privékoppeling voor Data Sync](./media/sql-data-sync-data-sql-server-sql-database/sync-private-link-overview.png)

> [!NOTE]
> U moet het door de service beheerde privé-eindpunt handmatig goedkeuren op de pagina Privé-eindpuntverbindingen van de Azure Portal tijdens de implementatie van de synchronisatiegroep of met behulp van PowerShell. 

## <a name="get-started"></a>Aan de slag 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Een Data Sync instellen in de Azure Portal

- [Azure SQL Data Sync instellen](sql-data-sync-sql-server-configure.md)
- Data Sync-agent: [Data Sync-agent voor Azure SQL Data Sync](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>Een Data Sync met PowerShell

- [PowerShell gebruiken om te synchroniseren tussen meerdere databases in Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [PowerShell gebruiken om te synchroniseren tussen een database in Azure SQL Database en een database in een SQL Server-exemplaar](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="set-up-data-sync-with-rest-api"></a>Een Data Sync met REST API
- [Gebruik REST API om te synchroniseren tussen meerdere databases in Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases-rest-api.md)

### <a name="review-the-best-practices-for-data-sync"></a>Bekijk de best practices voor Data Sync

- [Aanbevolen procedures voor Azure SQL Data Sync](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Is er iets misgegaan?

- [Problemen oplossen met Azure SQL Data Sync](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Consistentie en prestaties

### <a name="eventual-consistency"></a>Consistentie Uiteindelijk

Aangezien Data Sync op triggers is gebaseerd, wordt transactionele consistentie niet gegarandeerd. Microsoft garandeert dat alle wijzigingen uiteindelijk worden aangebracht en dat Data Sync gegevensverlies niet veroorzaakt.

### <a name="performance-impact"></a>Invloed op de prestaties

Data Sync maakt gebruik van triggers voor invoegen, bijwerken en verwijderen om wijzigingen bij te houden. Er worden zijtabellen gemaakt in de gebruikersdatabase voor het bijhouden van veranderingen. Deze activiteiten voor het bijhouden van veranderingen hebben invloed op de workload van uw database. Evalueer uw servicelaag en upgrade indien nodig.

Het inrichten en de inrichting van de inrichting tijdens het maken, bijwerken en verwijderen van een synchronisatiegroep kan ook van invloed zijn op de prestaties van de database.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Vereisten en beperkingen

### <a name="general-requirements"></a>Algemene vereisten

- Elke tabel moet een primaire sleutel hebben. Wijzig de waarde van de primaire sleutel in geen enkele rij. Als u een primaire sleutelwaarde moet wijzigen, verwijdert u de rij en maakt u deze opnieuw met de nieuwe primaire sleutelwaarde.

> [!IMPORTANT]
> Het wijzigen van de waarde van een bestaande primaire sleutel leidt tot het volgende foutgedrag:
> - Gegevens tussen hub en lid kunnen verloren gaan, zelfs als synchronisatie geen probleem rapporteert.
> - Synchronisatie kan mislukken omdat de traceringstabel een niet-bestaande rij van de bron heeft vanwege de primaire sleutelwijziging.

- Isolatie van momentopnamen moet zijn ingeschakeld voor synchronisatieleden en hubs. Voor meer informatie zie [Snapshot-isolatie in SQL Server](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

- Als u private link wilt gebruiken met Data Sync, moeten zowel de liddatabase als de hubdatabase worden gehost in Azure (dezelfde of verschillende regio's), in hetzelfde cloudtype (bijvoorbeeld in de openbare cloud of in de cloud van de overheid). Als u private link wilt gebruiken, moeten Microsoft.Network-resourceproviders bovendien Geregistreerd zijn voor de abonnementen die als host dienen voor de hub- en lidservers. Ten laatste moet u de privékoppeling voor Data Sync handmatig goedkeuren tijdens de synchronisatieconfiguratie, in de sectie Privé-eindpuntverbindingen in de Azure Portal of via PowerShell. Zie De privékoppeling instellen voor meer informatie over het goedkeuren [van de SQL Data Sync.](./sql-data-sync-sql-server-configure.md) Nadat u het door de service beheerde privé-eindpunt hebt goedgekeurd, vindt alle communicatie tussen de synchronisatieservice en de lid-/hubdatabases plaats via de private link. Bestaande synchronisatiegroepen kunnen worden bijgewerkt zodat deze functie is ingeschakeld.

### <a name="general-limitations"></a>Algemene beperkingen

- Een tabel kan geen identiteitskolom hebben die niet de primaire sleutel is.
- Een tabel moet een geclusterde index hebben om gegevenssynchronisatie te kunnen gebruiken.
- Een primaire sleutel kan niet de volgende gegevenstypen hebben: sql_variant, binair, varbinary, afbeelding, xml.
- Wees voorzichtig wanneer u de volgende gegevenstypen als primaire sleutel gebruikt, omdat de ondersteunde precisie slechts tot de tweede is: tijd, datum/tijd, datum/tijd2, datetimeoffset.
- De namen van objecten (databases, tabellen en kolommen) mogen niet de afdrukbare tekens bevatten, punt (.), vierkant haakje links ([) of vierkant haakje rechts (]).
- Een tabelnaam mag geen afdrukbare tekens bevatten: ! " # $ % ' ( ) * + - spatie
- Azure Active Directory verificatie wordt niet ondersteund.
- Als er tabellen zijn met dezelfde naam maar een ander schema (bijvoorbeeld dbo.customers en sales.customers), kan slechts één van de tabellen worden toegevoegd aan de synchronisatie.
- Kolommen met User-Defined gegevenstypen worden niet ondersteund
- Het verplaatsen van servers tussen verschillende abonnementen wordt niet ondersteund. 
- Als twee primaire sleutels alleen verschillend zijn in het geval (bijvoorbeeld Foo en foo), biedt Data Sync geen ondersteuning voor dit scenario.

#### <a name="unsupported-data-types"></a>Niet-ondersteunde gegevenstypen

- Filestream
- SQL/CLR UDT
- XMLSchemaCollection (XML ondersteund)
- Cursor, RowVersion, Timestamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Niet-ondersteunde kolomtypen

Data Sync kunnen geen alleen-lezen kolommen of door het systeem gegenereerde kolommen synchroniseren. Bijvoorbeeld:

- Berekende kolommen.
- Door het systeem gegenereerde kolommen voor tijdelijke tabellen.

#### <a name="limitations-on-service-and-database-dimensions"></a>Beperkingen voor service- en databasedimenssie

| **Dimensies**                                                  | **Limiet**              | **Tijdelijke oplossing**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Maximum aantal synchronisatiegroepen waar een database deel van kan uitmaken.       | 5                      |                             |
| Maximum aantal eindpunten in één synchronisatiegroep              | 30                     |                             |
| Maximum aantal on-premises eindpunten in één synchronisatiegroep. | 5                      | Meerdere synchronisatiegroepen maken |
| Database-, tabel-, schema- en kolomnamen                       | 50 tekens per naam |                             |
| Tabellen in een synchronisatiegroep                                          | 500                    | Meerdere synchronisatiegroepen maken |
| Kolommen in een tabel in een synchronisatiegroep                              | 1000                   |                             |
| Gegevensrijgrootte in een tabel                                        | 24 MB                  |                             |

> [!NOTE]
> Er kunnen maximaal 30 eindpunten in één synchronisatiegroep zijn als er slechts één synchronisatiegroep is. Als er meer dan één synchronisatiegroep is, mag het totale aantal eindpunten voor alle synchronisatiegroepen niet groter zijn dan 30. Als een database tot meerdere synchronisatiegroepen behoort, wordt deze geteld als meerdere eindpunten, niet één.

### <a name="network-requirements"></a>Netwerkvereisten

> [!NOTE]
> Als u private link gebruikt, zijn deze netwerkvereisten niet van toepassing. 

Wanneer de synchronisatiegroep tot stand is gebracht, moet Data Sync-service verbinding maken met de hubdatabase. Op het moment dat u de synchronisatiegroep tot stand Azure SQL, moet de Azure SQL de volgende configuratie in de instellingen `Firewalls and virtual networks` hebben:

 * *Openbare netwerktoegang weigeren* moet worden ingesteld op *Uit.*
 * *Toestaan dat Azure-services en -resources* toegang hebben tot deze server, moet zijn ingesteld op *Ja,* of u moet IP-regels maken voor de IP-adressen die worden gebruikt [door Data Sync service](network-access-controls-overview.md#data-sync).

Zodra de synchronisatiegroep is gemaakt en ingericht, kunt u deze instellingen uitschakelen. De synchronisatieagent maakt rechtstreeks verbinding met de hubdatabase en u [](private-endpoint-overview.md) kunt de [IP-regels](firewall-configure.md) of privé-eindpunten van de firewall van de server gebruiken om de agent toegang te geven tot de hubserver.

> [!NOTE]
> Als u de schema-instellingen van de synchronisatiegroep wijzigt, moet u de Data Sync-service opnieuw toegang geven tot de server, zodat de hubdatabase opnieuw kan worden ingericht.

## <a name="faq-about-sql-data-sync"></a>Veelgestelde vragen over SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Hoeveel kost de SQL Data Sync service?

Er worden geen kosten in rekening SQL Data Sync service zelf. U verzamelt echter nog steeds kosten voor gegevensoverdracht voor het verplaatsen van en naar uw SQL Database exemplaar. Zie prijzen voor SQL Database [meer informatie.](https://azure.microsoft.com/pricing/details/sql-database/)

### <a name="what-regions-support-data-sync"></a>Welke regio's ondersteunen Data Sync

SQL Data Sync is beschikbaar in alle regio's.

### <a name="is-a-sql-database-account-required"></a>Is een SQL Database account vereist

Ja. U moet een SQL Database hebben om de hubdatabase te hosten.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Kan ik de Data Sync gebruiken om alleen te synchroniseren SQL Server databases

Niet rechtstreeks. U kunt echter SQL Server databases indirect synchroniseren door een Hub-database in Azure te maken en vervolgens de on-premises databases toe te voegen aan de synchronisatiegroep.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>Kan ik een Data Sync om databases in een SQL Database die tot verschillende abonnementen behoren, te synchroniseren

Ja. U kunt synchroniseren tussen databases die deel uitmaken van resourcegroepen die eigendom zijn van verschillende abonnementen.

- Als de abonnementen tot dezelfde tenant behoren en u machtigingen hebt voor alle abonnementen, kunt u de synchronisatiegroep configureren in de Azure Portal.
- Anders moet u PowerShell gebruiken om de synchronisatieleden toe te voegen die tot verschillende abonnementen behoren.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Kan ik deze Data Sync om te synchroniseren tussen databases in SQL Database die deel uitmaken van verschillende clouds (zoals openbare Azure-cloud en Azure China 21Vianet)

Ja. U kunt synchroniseren tussen databases die deel uitmaken van verschillende clouds. U moet PowerShell gebruiken om de synchronisatieleden toe te voegen die deel uitmaken van de verschillende abonnementen.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Kan ik een Data Sync om gegevens uit mijn productiedatabase te seeden naar een lege database en deze vervolgens te synchroniseren

Ja. Maak het schema handmatig in de nieuwe database door een script uit te voeren op de oorspronkelijke database. Nadat u het schema hebt gemaakt, voegt u de tabellen toe aan een synchronisatiegroep om de gegevens te kopiëren en gesynchroniseerd te houden.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Moet ik een SQL Data Sync back-up maken van mijn databases en deze herstellen?

Het wordt afgeraden om een back-up van SQL Data Sync gegevens te maken. U kunt geen back-up maken en herstellen naar een bepaald tijdstip, omdat er geen versies SQL Data Sync synchronisaties worden gemaakt. Bovendien maakt SQL Data Sync geen back-up van andere SQL-objecten, zoals opgeslagen procedures, en wordt het equivalent van een herstelbewerking niet snel uitgevoerd.

Zie Een database kopiëren in Azure SQL Database voor een [aanbevolen back-uptechniek.](database-copy.md)

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Kan Data Sync versleutelde tabellen en kolommen synchroniseren

- Als een database gebruikmaakt Always Encrypted, kunt u alleen de tabellen en kolommen synchroniseren die *niet zijn* versleuteld. U kunt de versleutelde kolommen niet synchroniseren, omdat Data Sync de gegevens niet kunnen ontsleutelen.
- Als een kolom gebruikmaakt Column-Level Encryption (CLE), kunt u de kolom synchroniseren, zolang de rijgrootte kleiner is dan de maximale grootte van 24 MB. Data Sync behandelt de kolom die is versleuteld met sleutel (CLE) als normale binaire gegevens. Als u de gegevens op andere synchronisatieleden wilt ontsleutelen, moet u hetzelfde certificaat hebben.

### <a name="is-collation-supported-in-sql-data-sync"></a>Wordt collatie ondersteund in SQL Data Sync

Ja. SQL Data Sync ondersteunt collatie in de volgende scenario's:

- Als de geselecteerde synchronisatieschematabellen zich nog niet in uw hub- of liddatabases, maakt de service automatisch de bijbehorende tabellen en kolommen met de collatie-instellingen die zijn geselecteerd in de lege doeldatabases wanneer u de synchronisatiegroep implementeert.
- Als de tabellen die moeten worden gesynchroniseerd al bestaan in uw hub- en liddatabases, moet SQL Data Sync dat de primaire sleutelkolommen dezelfde collatie tussen hub- en liddatabases hebben om de synchronisatiegroep te kunnen implementeren. Er zijn geen seratiebeperkingen voor kolommen, met andere dan de kolommen met de primaire sleutel.

### <a name="is-federation-supported-in-sql-data-sync"></a>Wordt federatie ondersteund in SQL Data Sync

Federatiedatabase kan zonder enige beperking worden gebruikt in SQL Data Sync-service. U kunt het federatief database-eindpunt niet toevoegen aan de huidige versie van SQL Data Sync.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>Kan ik Data Sync gegevens synchroniseren die zijn geëxporteerd uit Dynamics 365 met behulp van de BYOD-functie (Bring Your Own Database) ?

Met de Dynamics 365 Bring Your Own Database-functie kunnen beheerders gegevensentiteiten uit de toepassing exporteren naar hun eigen Microsoft Azure SQL database. Data Sync kunnen worden gebruikt om deze gegevens te synchroniseren met andere databases als gegevens worden geëxporteerd met **incrementele push** (volledige push wordt niet ondersteund) en **triggers inschakelen in** de doeldatabase is ingesteld op **Ja.**

## <a name="next-steps"></a>Volgende stappen

### <a name="update-the-schema-of-a-synced-database"></a>Het schema van een gesynchroniseerde database bijwerken

Moet u het schema van een database in een synchronisatiegroep bijwerken? Schemawijzigingen worden niet automatisch gerepliceerd. Zie de volgende artikelen voor een aantal oplossingen:

- [De replicatie van schemawijzigingen automatiseren met SQL Data Sync in Azure](./sql-data-sync-update-sync-schema.md)
- [PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Controleren en problemen oplossen

Doet SQL Data Sync zoals verwacht? Zie de volgende artikelen voor informatie over het bewaken van activiteiten en het oplossen van problemen:

- [SQL Data Sync bewaken met Azure Monitor-logboeken](./monitor-tune-overview.md)
- [Problemen oplossen met Azure SQL Data Sync](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>Meer informatie over Azure SQL Database

Zie de volgende artikelen Azure SQL Database meer informatie over de gegevens:

- [Wat is de Azure SQL Database-service?](sql-database-paas-overview.md)
- [Database Lifecycle Management (DLM)](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
