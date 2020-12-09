---
title: Wat is SQL Data Sync voor Azure?
description: In dit overzicht worden SQL Data Sync voor Azure geïntroduceerd, waarmee u gegevens kunt synchroniseren tussen meerdere Cloud-en on-premises data bases.
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
ms.openlocfilehash: 55fa106f0515405dcad969f05d28e0bc7b975b40
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922273"
---
# <a name="what-is-sql-data-sync-for-azure"></a>Wat is SQL Data Sync voor Azure?

SQL Data Sync is een service die is gebouwd op Azure SQL Database waarmee u de gegevens die u in twee richtingen selecteert, kunt synchroniseren in meerdere data bases, zowel on-premises als in de Cloud. 

> [!IMPORTANT]
> Azure SQL Data Sync biedt op dit moment geen ondersteuning voor het beheerde exemplaar van Azure SQL.


## <a name="overview"></a>Overzicht 

Gegevens synchronisatie is gebaseerd op het concept van een synchronisatie groep. Een synchronisatie groep is een groep data bases die u wilt synchroniseren.

Bij de gegevens synchronisatie wordt gebruikgemaakt van een hub-en spoke-topologie voor het synchroniseren van gegevens. U definieert een van de data bases in de synchronisatie groep als de hub-data base. De rest van de data bases zijn leden databases. Synchronisatie vindt alleen plaats tussen de hub en de afzonderlijke leden.

- De **hub-data base** moet een Azure SQL database zijn.
- De **leden databases** kunnen bestaan uit data bases in Azure SQL database of in exemplaren van SQL Server.
- De meta **gegevens database** bevat de meta gegevens en het logboek voor de gegevens synchronisatie. De meta gegevens database moet een Azure SQL Database zijn die zich in dezelfde regio bevindt als de hub-data base. De meta gegevens database voor synchronisatie is gemaakt door de klant en het eigendom van de klant. U kunt slechts één meta gegevens database synchroniseren per regio en abonnement. De meta gegevens database voor synchronisatie kan niet worden verwijderd of de naam ervan kan niet worden gewijzigd terwijl er synchronisatie groepen of synchronisatie agenten bestaan. U wordt aangeraden een nieuwe, lege database te maken die als Database met metagegevens van synchronisatie wordt gebruikt. Met Data Sync worden tabellen in deze database gemaakt en een regelmatige workload uitgevoerd.

> [!NOTE]
> Als u een on-premises Data Base als een lid-data base gebruikt, moet u [een lokale synchronisatie agent installeren en configureren](sql-data-sync-sql-server-configure.md#add-on-prem).

![Gegevens tussen databases synchroniseren](./media/sql-data-sync-data-sql-server-sql-database/sync-data-overview.png)

Een synchronisatie groep heeft de volgende eigenschappen:

- In het **synchronisatie schema** wordt beschreven welke gegevens worden gesynchroniseerd.
- De **synchronisatie richting** kan bidirectionele zijn of kan in slechts één richting stromen. Dat wil zeggen dat de synchronisatie richting van de *hub naar het lid*, of van het lid is van een *hub* of van beide kan zijn.
- Het **synchronisatie-interval** beschrijft hoe vaak synchronisatie plaatsvindt.
- Het **beleid** voor het oplossen van conflicten is een beleid op groeps niveau, dat *hub wint* of lid van een *WINS-server* kan zijn.

## <a name="when-to-use"></a>Wanneer gebruikt u dit?

Gegevens synchronisatie is handig in gevallen waarin gegevens moeten worden bijgewerkt in meerdere data bases in Azure SQL Database of SQL Server. Hier volgen de belangrijkste use cases voor gegevens synchronisatie:

- **Gegevens synchronisatie hybride:** Met gegevens synchronisatie kunt u gegevens gesynchroniseerd blijven tussen uw data bases in SQL Server en Azure SQL Database om hybride toepassingen in te scha kelen. Deze mogelijkheid kan worden bezwaarder voor klanten die overwegen over te stappen naar de Cloud en graag een deel van hun toepassing in azure willen plaatsen.
- **Gedistribueerde toepassingen:** In veel gevallen is het nuttig om verschillende werk belastingen te scheiden in verschillende data bases. Als u bijvoorbeeld een grote productie database hebt, maar u ook een rapportage-of analyse werk belasting moet uitvoeren op deze gegevens, is het handig om een tweede Data Base voor deze extra workload te hebben. Deze aanpak minimaliseert de invloed op de prestaties van uw productiewerk belasting. U kunt gegevens synchronisatie gebruiken om deze twee data bases te synchroniseren.
- **Wereld wijd gedistribueerde toepassingen:** Veel bedrijven omvatten verschillende regio's en zelfs verschillende landen/regio's. Om de netwerk latentie te minimaliseren, is het het beste om uw gegevens in een regio dicht bij u af te sluiten. Met gegevens synchronisatie kunt u eenvoudig data bases in regio's over de hele wereld gesynchroniseerd blijven.

Gegevens synchronisatie is niet de aanbevolen oplossing voor de volgende scenario's:

| Scenario | Enkele aanbevolen oplossingen |
|----------|----------------------------|
| Herstel na noodgevallen | [Azure geo-redundante back-ups](automated-backups-overview.md) |
| Schaal lezen | [Alleen-lezen replica's gebruiken om taken te verdelen over alleen-lezen query's (preview-versie)](read-scale-out.md) |
| ETL (OLTP to OLAP) | [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) of [SQL Server Integration Services](/sql/integration-services/sql-server-integration-services) |
| Migratie van SQL Server naar Azure SQL Database. SQL Data Sync kunnen echter worden gebruikt nadat de migratie is voltooid, om ervoor te zorgen dat de bron en het doel synchroon blijven.  | [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) |
|||

## <a name="how-it-works"></a>Uitleg

- **Wijzigingen in de gegevens bijhouden:** Gegevens synchronisatie houdt wijzigingen bij met behulp van INSERT-, update-en delete-triggers. De wijzigingen worden vastgelegd in een tabel aan de kant van de gebruikers database. Houd er rekening mee dat BULK INSERT triggers niet standaard wordt geactiveerd. Als FIRE_TRIGGERS niet is opgegeven, worden er geen invoeg triggers uitgevoerd. Voeg de FIRE_TRIGGERS optie toe, zodat gegevens synchronisatie deze toevoegingen kan bijhouden. 
- **Gegevens synchroniseren:** Gegevens synchronisatie is ontworpen in een hub-en spoke-model. De hub wordt met elk lid afzonderlijk gesynchroniseerd. Wijzigingen van de hub worden gedownload naar het lid en vervolgens worden wijzigingen van het lid geüpload naar de hub.
- **Conflicten oplossen:** Gegevens synchronisatie biedt twee opties voor het oplossen van conflicten, *hub WINS* of *lid van WINS*.
  - Als u *hub WINS* selecteert, worden wijzigingen in het lid altijd overschreven door de wijzigingen in de hub.
  - Als u *lid bent van WINS*, worden wijzigingen in het lid overschreven in de hub. Als er meer dan één lid is, is de uiteindelijke waarde afhankelijk van welk lid het eerst synchroniseert.

## <a name="compare-with-transactional-replication"></a>Vergelijken met transactionele replicatie

| | Gegevens synchroniseren | Transactionele replicatie |
|---|---|---|
| **Voordelen** | -Actief-actief ondersteuning<br/>-Bi-richting tussen on-premises en Azure SQL Database | -Laagste latentie<br/>-Transactionele consistentie<br/>-Bestaande topologie na migratie opnieuw gebruiken <br/>-Ondersteuning voor Azure SQL Managed instance |
| **Nadelen** | -Geen transactionele consistentie<br/>-Hogere gevolgen voor de prestaties | -Kan niet publiceren vanaf Azure SQL Database <br/>-Hoge onderhouds kosten |

## <a name="private-link-for-data-sync-preview"></a>Persoonlijke koppeling voor gegevens synchronisatie (preview-versie)
Met de nieuwe functie voor persoonlijke koppelingen (preview) kunt u een privé-Service beheer-eind punt kiezen voor het maken van een beveiligde verbinding tussen de synchronisatie service en uw leden-of hub-data base tijdens het gegevens synchronisatie proces. Een privé-eind punt dat door een service wordt beheerd, is een privé-IP-adres binnen een specifiek virtueel netwerk en subnet. Binnen de gegevens synchronisatie wordt het door de service beheerde privé-eind punt gemaakt door micro soft en wordt het exclusief door de gegevens synchronisatie service gebruikt voor een bepaalde synchronisatie bewerking. Lees de [algemene vereisten](sql-data-sync-data-sql-server-sql-database.md#general-requirements) voor de functie voordat u de persoonlijke koppeling instelt. 

![Persoonlijke koppeling voor gegevens synchronisatie](./media/sql-data-sync-data-sql-server-sql-database/sync-private-link-overview.png)

> [!NOTE]
> U moet het door de service beheerde privé-eind punt hand matig goed keuren op de pagina **verbindingen met privé-eind punten** van de Azure Portal tijdens de implementatie van de synchronisatie groep of door Power shell te gebruiken.

## <a name="get-started"></a>Aan de slag 

### <a name="set-up-data-sync-in-the-azure-portal"></a>Gegevens synchronisatie instellen in de Azure Portal

- [Azure SQL Data Sync instellen](sql-data-sync-sql-server-configure.md)
- Data Sync-agent: [Data Sync-agent voor Azure SQL Data Sync](sql-data-sync-agent-overview.md)

### <a name="set-up-data-sync-with-powershell"></a>Gegevens synchronisatie met Power shell instellen

- [Power shell gebruiken om te synchroniseren tussen meerdere data bases in Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
- [Power shell gebruiken om te synchroniseren tussen een data base in Azure SQL Database en een data base in een SQL Server-exemplaar](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

### <a name="review-the-best-practices-for-data-sync"></a>Bekijk de aanbevolen procedures voor gegevens synchronisatie

- [Aanbevolen procedures voor Azure SQL Data Sync](sql-data-sync-best-practices.md)

### <a name="did-something-go-wrong"></a>Er is iets fout gegaan

- [Problemen oplossen met Azure SQL Data Sync](./sql-data-sync-troubleshoot.md)

## <a name="consistency-and-performance"></a>Consistentie en prestaties

### <a name="eventual-consistency"></a>Consistentie Uiteindelijk

Omdat de gegevens synchronisatie op basis van een trigger is gebaseerd, is de transactionele consistentie niet gegarandeerd. Micro soft garandeert dat alle wijzigingen uiteindelijk worden doorgevoerd en dat gegevens synchronisatie geen gegevens verlies veroorzaakt.

### <a name="performance-impact"></a>Invloed op de prestaties

Bij de gegevens synchronisatie worden de triggers invoegen, bijwerken en verwijderen gebruikt om wijzigingen bij te houden. Hiermee maakt u tabellen in de gebruikers database voor het bijhouden van wijzigingen. Deze activiteiten voor het bijhouden van wijzigingen hebben gevolgen voor de werk belasting van uw data base. Evalueer uw servicelaag en voer zo nodig een upgrade uit.

Het inrichten en verwijderen van de inrichting tijdens het maken van een synchronisatie groep, update en verwijdering kan ook van invloed zijn op de prestaties van de data base.

## <a name="requirements-and-limitations"></a><a name="sync-req-lim"></a> Vereisten en beperkingen

### <a name="general-requirements"></a>Algemene vereisten

- Elke tabel moet een primaire sleutel hebben. Wijzig de waarde van de primaire sleutel in geen enkele rij. Als u een waarde voor de primaire sleutel moet wijzigen, verwijdert u de rij en maakt u deze opnieuw met de nieuwe waarde voor de primaire sleutel.

> [!IMPORTANT]
> Als u de waarde van een bestaande primaire sleutel wijzigt, resulteert dit in het volgende probleem:
> - De gegevens tussen de hub en het lid kunnen verloren gaan, zelfs al wordt er door synchronisatie geen problemen gerapporteerd.
> - De synchronisatie kan mislukken omdat de tracerings tabel een niet-bestaande rij van de bron bevat, omdat de primaire sleutel is gewijzigd.

- Snap shot-isolatie moet zijn ingeschakeld voor synchronisatie leden en-hub. Voor meer informatie zie [Snapshot-isolatie in SQL Server](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server).

- Als u een persoonlijke koppeling wilt gebruiken met de gegevens synchronisatie, moeten zowel de leden-als de hub-data bases worden gehost in azure (dezelfde of verschillende regio's), in hetzelfde Cloud type (bijvoorbeeld zowel in de open bare Cloud als in beide in de overheids Cloud). Daarnaast moeten micro soft. Network resource providers worden geregistreerd voor de abonnementen die fungeren als host voor de hub en lidservers om een persoonlijke koppeling te gebruiken. Ten slotte moet u de persoonlijke koppeling voor gegevens synchronisatie hand matig goed keuren tijdens de synchronisatie configuratie, in de sectie ' persoonlijke eindpunt verbindingen ' in de Azure Portal of via Power shell. Zie [SQL Data Sync instellen](./sql-data-sync-sql-server-configure.md)voor meer informatie over het goed keuren van de persoonlijke koppeling. Zodra u het door de service beheerde privé-eind punt hebt goedgekeurd, wordt alle communicatie tussen de synchronisatie service en de member/hub-data bases uitgevoerd via de privé koppeling. Bestaande synchronisatie groepen kunnen worden bijgewerkt om deze functie in te scha kelen.

### <a name="general-limitations"></a>Algemene beperkingen

- Een tabel kan geen identiteits kolom hebben die niet de primaire sleutel is.
- Een tabel moet een geclusterde index hebben om gegevens synchronisatie te kunnen gebruiken.
- Een primaire sleutel kan niet de volgende gegevens typen bevatten: sql_variant, binary, varbinary, image en XML.
- Wees voorzichtig wanneer u de volgende gegevens typen als primaire sleutel gebruikt, omdat de ondersteunde precisie alleen geldt voor de tweede: time, datetime, DATETIME2, date time offset.
- De namen van objecten (data bases, tabellen en kolommen) mogen niet de periode van het afdruk bare teken (.), het linker vier Kante haakje ([) of de rechter rechte haak (]) bevatten.
- De naam van een tabel mag geen afdruk bare tekens bevatten:! "# $% () * +-spatie
- Azure Active Directory-verificatie wordt niet ondersteund.
- Als er tabellen met dezelfde naam maar een ander schema zijn (bijvoorbeeld dbo. klanten en Sales. klanten), kan slechts één van de tabellen worden toegevoegd aan de synchronisatie.
- Kolommen met User-Defined gegevens typen worden niet ondersteund
- Het verplaatsen van servers tussen verschillende abonnementen wordt niet ondersteund. 

#### <a name="unsupported-data-types"></a>Niet-ondersteunde gegevens typen

- -
- SQL/CLR UDT
- XMLSchemaCollection (XML ondersteund)
- Cursor, RowVersion, Time Stamp, Hierarchyid

#### <a name="unsupported-column-types"></a>Niet-ondersteunde kolom typen

Met gegevens synchronisatie kunnen alleen-lezen of door het systeem gegenereerde kolommen niet worden gesynchroniseerd. Bijvoorbeeld:

- Berekende kolommen.
- Door het systeem gegenereerde kolommen voor tijdelijke tabellen.

#### <a name="limitations-on-service-and-database-dimensions"></a>Beperkingen voor service-en database dimensies

| **Afmetingen**                                                  | **Limiet**              | **Tijdelijke oplossing**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| Het maximum aantal synchronisatie groepen waarvan elke Data Base deel kan uitmaken.       | 5                      |                             |
| Maximum aantal eind punten in één synchronisatie groep              | 30                     |                             |
| Het maximum aantal on-premises eind punten in één synchronisatie groep. | 5                      | Meerdere synchronisatie groepen maken |
| Data Base-, tabel-, schema-en kolom namen                       | 50 tekens per naam |                             |
| Tabellen in een synchronisatie groep                                          | 500                    | Meerdere synchronisatie groepen maken |
| Kolommen in een tabel in een synchronisatie groep                              | 1000                   |                             |
| Grootte van gegevensrij in een tabel                                        | 24 MB                  |                             |

> [!NOTE]
> Er kunnen Maxi maal 30 eind punten in één synchronisatie groep bestaan als er slechts één synchronisatie groep is. Als er meer dan één synchronisatie groep is, kan het totale aantal eind punten voor alle synchronisatie groepen niet meer dan 30 zijn. Als een Data Base deel uitmaakt van meerdere synchronisatie groepen, wordt deze als meerdere eind punten geteld, niet een.

### <a name="network-requirements"></a>Netwerkvereisten

> [!NOTE]
> Als u een persoonlijke koppeling gebruikt, zijn deze netwerk vereisten niet van toepassing. 

Wanneer de synchronisatie groep tot stand is gebracht, moet de Data Sync-service verbinding maken met de hub-data base. Op het moment dat u de synchronisatie groep instelt, moet de Azure SQL-Server de volgende configuratie hebben in de `Firewalls and virtual networks` instellingen:

 * *Toegang tot open bare netwerk weigeren* moet worden ingesteld op *uit*.
 * *Toestaan dat Azure-Services en-resources toegang hebben tot deze server* moet zijn ingesteld op *Ja* of moet u IP-regels maken voor de [IP-adressen die worden gebruikt door de Data Sync-Service](network-access-controls-overview.md#data-sync).

Zodra de synchronisatie groep is gemaakt en ingericht, kunt u deze instellingen uitschakelen. De synchronisatie agent maakt rechtstreeks verbinding met de hub-data base en u kunt de [Firewall-IP-regels](firewall-configure.md) of [particuliere eind punten](private-endpoint-overview.md) van de server gebruiken om de agent toegang te geven tot de hub-server.

> [!NOTE]
> Als u de schema-instellingen van de synchronisatie groep wijzigt, moet u ervoor zorgen dat de Data Sync-service weer toegang heeft tot de server, zodat de hub-data base opnieuw kan worden ingericht.

## <a name="faq-about-sql-data-sync"></a>Veelgestelde vragen over SQL Data Sync

### <a name="how-much-does-the-sql-data-sync-service-cost"></a>Hoeveel kost het SQL Data Sync service kosten

Er worden geen kosten in rekening gebracht voor de SQL Data Sync-service zelf. U kunt echter nog steeds kosten voor gegevens overdracht verzamelen voor het verplaatsen van gegevens in en uit uw SQL Database-exemplaar. Zie [SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database/)voor meer informatie.

### <a name="what-regions-support-data-sync"></a>Welke regio's ondersteunen gegevens synchronisatie

SQL Data Sync is in alle regio's beschikbaar.

### <a name="is-a-sql-database-account-required"></a>Is een SQL Database account vereist

Ja. U moet een SQL Database-account hebben om de hub-data base te hosten.

### <a name="can-i-use-data-sync-to-sync-between-sql-server-databases-only"></a>Kan ik Data Sync gebruiken om alleen te synchroniseren tussen SQL Server data bases

Niet rechtstreeks. U kunt echter indirect synchroniseren tussen SQL Server data bases, door een hub-data base te maken in Azure en vervolgens de on-premises data bases toe te voegen aan de synchronisatie groep.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-subscriptions"></a>Kan ik Data Sync gebruiken om te synchroniseren tussen data bases in SQL Database die tot verschillende abonnementen behoren

Ja. U kunt synchroniseren tussen data bases die deel uitmaken van resource groepen die eigendom zijn van verschillende abonnementen.

- Als de abonnementen deel uitmaken van dezelfde Tenant en u gemachtigd bent voor alle abonnementen, kunt u de synchronisatie groep configureren in de Azure Portal.
- Anders moet u Power shell gebruiken om de synchronisatie leden toe te voegen die deel uitmaken van verschillende abonnementen.

### <a name="can-i-use-data-sync-to-sync-between-databases-in-sql-database-that-belong-to-different-clouds-like-azure-public-cloud-and-azure-china-21vianet"></a>Kan ik Data Sync gebruiken om te synchroniseren tussen data bases in SQL Database die deel uitmaken van verschillende Clouds (zoals Azure Public Cloud en Azure China 21Vianet)

Ja. U kunt synchroniseren tussen data bases die deel uitmaken van verschillende Clouds. U moet Power shell gebruiken om de synchronisatie leden toe te voegen die deel uitmaken van de verschillende abonnementen.

### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-sync-them"></a>Kan ik gegevens synchronisatie gebruiken voor het seeden van gegevens van mijn productie database naar een lege data base en deze vervolgens synchroniseren

Ja. Maak het schema hand matig in de nieuwe Data Base door het uit te schrijven op basis van het oorspronkelijke script. Nadat u het schema hebt gemaakt, voegt u de tabellen toe aan een synchronisatie groep om de gegevens te kopiëren en te synchroniseren.

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>Moet ik SQL Data Sync gebruiken om een back-up te maken van mijn data bases en deze te herstellen

Het is niet raadzaam om SQL Data Sync te gebruiken om een back-up van uw gegevens te maken. U kunt geen back-up-en herstel bewerking naar een bepaald punt in de tijd, omdat SQL Data Sync-synchronisaties geen versie hebben. Daarnaast wordt SQL Data Sync geen back-up gemaakt van andere SQL-objecten, zoals opgeslagen procedures, en wordt het equivalent van een herstel bewerking niet snel uitgevoerd.

Zie [een Data Base kopiëren in Azure SQL database](database-copy.md)voor een aanbevolen back-uptechniek.

### <a name="can-data-sync-sync-encrypted-tables-and-columns"></a>Kan gegevens synchronisatie versleutelde tabellen en kolommen synchroniseren

- Als een Data Base gebruikmaakt van Always Encrypted, kunt u alleen de tabellen en kolommen synchroniseren die *niet* zijn versleuteld. U kunt de versleutelde kolommen niet synchroniseren omdat gegevens synchronisatie de gegevens niet kan ontsleutelen.
- Als een kolom gebruikmaakt van Column-Level Encryption (CLE), kunt u de kolom synchroniseren, mits de Rijgrootte kleiner is dan de maximale grootte van 24 MB. Gegevens synchronisatie behandelt de kolom die is versleuteld met de sleutel (CLE) als normale binaire gegevens. Als u de gegevens op andere synchronisatie leden wilt ontsleutelen, moet u hetzelfde certificaat hebben.

### <a name="is-collation-supported-in-sql-data-sync"></a>Wordt gesorteerd ondersteund in SQL Data Sync

Ja. SQL Data Sync sortering ondersteunt in de volgende scenario's:

- Als de geselecteerde synchronisatie schema tabellen zich nog niet in uw hub of lid databases bevinden, worden de bijbehorende tabellen en kolommen automatisch door de service gemaakt met de sorterings instellingen die zijn geselecteerd in de lege doel databases, wanneer u de synchronisatie groep implementeert.
- Als de tabellen die u wilt synchroniseren al bestaan in zowel uw hub-als lid-data bases, SQL Data Sync vereist dat de primaire-sleutel kolommen dezelfde sortering hebben tussen de data bases van de hub en het lid om de synchronisatie groep goed te implementeren. Er zijn geen sorterings beperkingen voor kolommen die geen primaire-sleutel kolommen zijn.

### <a name="is-federation-supported-in-sql-data-sync"></a>Wordt Federatie ondersteund in SQL Data Sync

De Federatie hoofd database kan worden gebruikt in de SQL Data Sync-Service zonder enige beperking. U kunt het federatieve data base-eind punt niet toevoegen aan de huidige versie van SQL Data Sync.

### <a name="can-i-use-data-sync-to-sync-data-exported-from-dynamics-365-using-bring-your-own-database-byod-feature"></a>Kan ik gegevens synchronisatie gebruiken om gegevens te synchroniseren die zijn geëxporteerd uit Dynamics 365 met de functie uw eigen data base (BYOD) gebruiken?

Met de Dynamics 365-functie van uw eigen data base kunnen beheerders gegevens entiteiten van de toepassing exporteren naar hun eigen Microsoft Azure SQL database. Gegevens synchronisatie kan worden gebruikt om deze gegevens te synchroniseren met andere data bases als gegevens worden geëxporteerd met een **incrementele push** (volledige push wordt niet ondersteund) en **Triggers inschakelen in doel database** is ingesteld op **Ja**.

## <a name="next-steps"></a>Volgende stappen

### <a name="update-the-schema-of-a-synced-database"></a>Het schema van een gesynchroniseerde data base bijwerken

Moet u het schema van een data base in een synchronisatie groep bijwerken? Wijzigingen in het schema worden niet automatisch gerepliceerd. Voor sommige oplossingen raadpleegt u de volgende artikelen:

- [De replicatie van schema wijzigingen automatiseren met SQL Data Sync in azure](./sql-data-sync-update-sync-schema.md)
- [PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep](scripts/update-sync-schema-in-sync-group.md)

### <a name="monitor-and-troubleshoot"></a>Bewaken en problemen oplossen

Wordt SQL Data Sync als verwachting uitgevoerd? Raadpleeg de volgende artikelen voor informatie over het bewaken van activiteiten en het oplossen van problemen:

- [SQL Data Sync bewaken met Azure Monitor-logboeken](./monitor-tune-overview.md)
- [Problemen oplossen met Azure SQL Data Sync](./sql-data-sync-troubleshoot.md)

### <a name="learn-more-about-azure-sql-database"></a>Meer informatie over Azure SQL Database

Raadpleeg de volgende artikelen voor meer informatie over Azure SQL Database:

- [Wat is de Azure SQL Database-service?](sql-database-paas-overview.md)
- [Database Lifecycle Management (DLM)](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
