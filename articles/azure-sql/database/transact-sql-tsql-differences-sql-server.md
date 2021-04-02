---
title: T-SQL-verschillen oplossen-migratie
description: Transact-SQL-instructies die kleiner zijn dan volledig ondersteund in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: 1e286b2329cb98d580bbf64071ff8767db304a00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461885"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Verschillen in Transact-SQL oplossen tijdens de migratie naar SQL Database

Wanneer u [uw data base migreert](migrate-to-database-from-sql-server.md) van SQL Server naar Azure SQL database, detecteert u mogelijk dat uw SQL Server-Data Base enige herengineering vereist voordat deze kan worden gemigreerd. Dit artikel bevat richt lijnen om u te helpen bij het uitvoeren van deze rebouwer en inzicht te krijgen in de onderliggende redenen waarom de herengineering nood zakelijk is. Gebruik de [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)om incompatibiliteiten te detecteren.

## <a name="overview"></a>Overzicht

De meeste Transact-SQL-functies die toepassingen gebruiken, worden volledig ondersteund in zowel Microsoft SQL Server als Azure SQL Database. De belangrijkste SQL-onderdelen, zoals gegevens typen, Opera Tors, teken reeks-, reken kundige, logische en cursor functies werken bijvoorbeeld hetzelfde in SQL Server en SQL Database. Er zijn echter enkele T-SQL-verschillen in DDL-elementen (Data Definition Language) en DML (Data Manipulation Language) die resulteren in T-SQL-instructies en query's die slechts gedeeltelijk worden ondersteund (wat verderop in dit artikel wordt besproken).

Daarnaast zijn er een aantal functies en syntaxis die helemaal niet worden ondersteund omdat Azure SQL Database is ontworpen om functies te isoleren op basis van afhankelijkheden van de hoofd database en het besturings systeem. Daarom zijn de meeste activiteiten op server niveau niet geschikt voor SQL Database. T-SQL-instructies en-opties zijn niet beschikbaar als ze opties op server niveau, onderdelen van het besturings systeem of de configuratie van het bestands systeem opgeven. Wanneer dergelijke mogelijkheden vereist zijn, is een geschikt alternatief op een andere manier vaak beschikbaar vanaf SQL Database of vanuit een andere Azure-functie of-service.

Zo is hoge Beschik baarheid ingebouwd in Azure SQL Database met behulp van technologie die vergelijkbaar is met AlwaysOn- [beschikbaarheids groepen](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). T-SQL-instructies die zijn gerelateerd aan beschikbaarheids groepen worden niet ondersteund door SQL Database en de dynamische beheer weergaven die betrekking hebben op AlwaysOn-beschikbaarheids groepen, worden ook niet ondersteund.

Zie [Azure SQL database functie vergelijking](features-comparison.md)voor een lijst met de functies die worden ondersteund en niet worden ondersteund door SQL database. De lijst op deze pagina vormt een aanvulling op het artikel richt lijnen en functies en richt zich op Transact-SQL-instructies.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Transact-SQL-syntaxis instructies met gedeeltelijke verschillen

De belangrijkste DDL-instructies (Data Definition Language) zijn beschikbaar, maar sommige DDL-instructies hebben uitbrei dingen met betrekking tot de plaatsing van schijven en niet-ondersteunde functies.

- CREATE and ALTER data base-instructies hebben meer dan drie dozijn opties. De instructies omvatten opties voor plaatsing, FILESTREAM en Service Broker die alleen van toepassing zijn op SQL Server. Dit is mogelijk niet van belang als u data bases maakt voordat u migreert, maar als u T-SQL-code migreert die data bases maakt, moet u [Create Data Base (Azure SQL database)](/sql/t-sql/statements/create-database-transact-sql) vergelijken met de SQL Server-syntaxis bij [Create data base (SQL Server Transact-SQL)](/sql/t-sql/statements/create-database-transact-sql) om ervoor te zorgen dat alle opties die u gebruikt, worden ondersteund. CREATE data base for Azure SQL Database heeft ook service doelstelling-en elastische schaal opties die alleen van toepassing zijn op SQL Database.
- De instructie CREATE en ALTER TABLE heeft bestands tabel opties die niet kunnen worden gebruikt op SQL Database omdat FILESTREAM niet wordt ondersteund.
- Aanmeldings instructies CREATE en ALTER worden ondersteund, maar SQL Database biedt niet alle opties. Om uw data base draagbaarer te maken, wordt SQL Database aanbevolen data base-gebruikers in plaats van aanmeldingen te gebruiken wanneer dat mogelijk is. Zie voor meer informatie [login maken/wijzigen](/sql/t-sql/statements/alter-login-transact-sql) en [aanmeldingen en gebruikers beheren](logins-create-manage.md).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Transact-SQL-syntaxis wordt niet ondersteund in Azure SQL Database

Naast Transact-SQL-instructies die betrekking hebben op de niet-ondersteunde functies die worden beschreven in [Azure SQL database functie vergelijking](features-comparison.md), worden de volgende instructies en groepen instructies niet ondersteund. Als dat het geval is, moet u, als uw data base wordt gemigreerd, gebruikmaken van een van de volgende functies, uw T-SQL opnieuw bezorgen om deze T-SQL-functies en-instructies te elimineren.

- Systeemobjecten sorteren
- Gerelateerde verbinding: endpoint-instructies. SQL Database biedt geen ondersteuning voor Windows-verificatie, maar biedt wel ondersteuning voor vergelijk bare Azure Active Directory-verificatie. Voor sommige verificatietypen is de nieuwste versie van SSMS vereist. Zie [verbinding maken met SQL database of Azure Azure Synapse Analytics met behulp van Azure Active Directory-verificatie](authentication-aad-overview.md)voor meer informatie.
- Databaseoverschrijdende query’s met drie of vier onderdeelnamen. (Alleen-lezen query’s die databaseoverschrijdend zijn worden ondersteund dankzij [elastische databasequery’s](elastic-query-overview.md).)
- Databaseoverschrijdend het eigendom koppelen, instelling `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Gebruik in plaats daarvan 'EXECUTE AS USER'.
- Versleuteling wordt ondersteund, behalve voor Extensible Key Management
- Gebeurtenis: gebeurtenissen, gebeurtenis meldingen, query meldingen
- File Placement: syntaxis met betrekking tot de plaatsing, grootte en database bestanden van het database bestand die automatisch door Microsoft Azure worden beheerd.
- Hoge Beschik baarheid: syntaxis met betrekking tot hoge Beschik baarheid, die wordt beheerd via uw Microsoft Azure-account. Dit omvat syntaxis voor back-ups, herstellen, Always On, databasespiegeling, de back-upfunctie voor logboekbestanden en herstelmodi.
- Log Reader: syntaxis die afhankelijk is van de logboek lezer, die niet beschikbaar is op SQL Database: push-replicatie, Change Data Capture. SQL Database kan abonnee zijn op een artikel over push-replicatie.
- Functies: `fn_get_sql`, `fn_virtualfilestats`, `fn_virtualservernodes`
- Hardware: syntaxis met betrekking tot hardware-gerelateerde server instellingen: zoals geheugen, worker-threads, CPU-affiniteit, tracerings vlaggen. Gebruik in plaats daarvan service lagen en reken grootten.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET` , en `OPENDATASOURCE` vier delen van namen
- .NET Framework: CLR-integratie met SQL Server
- Semantische zoekopdrachten
- Server referenties: gebruik in plaats daarvan [Data Base-bereik referenties](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) .
- Items op server niveau: Server functies, `sys.login_token` . `GRANT`, `REVOKE` en `DENY` van machtigingen op server niveau zijn niet beschikbaar, maar sommige worden vervangen door machtigingen op database niveau. Sommige handige DMV’s op serverniveau hebben vergelijkbare DMV’s op databaseniveau.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure`-opties en `RECONFIGURE`. Sommige opties zijn beschikbaar met [ALTER DATABASE SCOPED CONFIGURATION](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent: syntaxis die afhankelijk is van de SQL Server Agent of de MSDB-Data Base: waarschuwingen, Opera Tors, centrale beheerser vers. Gebruik in plaats daarvan opties voor scripts, zoals Azure PowerShell.
- SQL Server controle: gebruik in plaats daarvan SQL Database controle.
- SQL Server-tracering
- Tracerings vlaggen: sommige items van de tracerings vlag zijn verplaatst naar de compatibiliteits modi.
- Transact-SQL-foutopsporing
- Triggers: triggers binnen het serverbereik of aanmeldingstriggers
- `USE`-instructie: als u de databasecontext wilt wijzigen naar een andere database, moet u een nieuwe verbinding maken met de nieuwe database.

## <a name="full-transact-sql-reference"></a>Volledige naslaginformatie voor Transact-SQL

Zie [Naslaginformatie voor Transact-SQL (database-engine)](/sql/t-sql/language-reference) in SQL Server Books Online voor meer informatie over Transact-SQL-grammatica, -gebruik en -voorbeelden.

### <a name="about-the-applies-to-tags"></a>Over het label 'Van toepassing op'

De Transact-SQL-Naslag informatie bevat artikelen die betrekking hebben op SQL Server versies 2008 van de huidige versie. Onder de titel van het artikel ziet u een pictogram balk met de vier SQL Server-platformen en de betreffende toepas baarheid. Beschikbaarheidsgroepen zijn bijvoorbeeld geïntroduceerd in SQL Server 2012. In het artikel [beschikbaarheids groep maken](/sql/t-sql/statements/create-availability-group-transact-sql) wordt aangegeven dat de instructie van toepassing is op **SQL Server (te beginnen met 2012)**. De instructie is niet van toepassing op SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure Azure Synapse Analytics of parallel data warehouse.

In sommige gevallen kan het algemene onderwerp van een artikel in een product worden gebruikt, maar er zijn kleine verschillen tussen producten. De verschillen worden als toepasselijk aangegeven op middel punt in het artikel. In sommige gevallen kan het algemene onderwerp van een artikel in een product worden gebruikt, maar er zijn kleine verschillen tussen producten. De verschillen worden als toepasselijk aangegeven op middel punt in het artikel. Het artikel TRIGGER maken is bijvoorbeeld beschikbaar in SQL Database. Maar de optie **alle server** voor triggers op server niveau geeft aan dat triggers op server niveau niet kunnen worden gebruikt in SQL database. Gebruik in plaats daarvan triggers op database niveau.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure SQL database functie vergelijking](features-comparison.md)voor een lijst met de functies die worden ondersteund en niet worden ondersteund door SQL database. De lijst op deze pagina vormt een aanvulling op het artikel richt lijnen en functies en richt zich op Transact-SQL-instructies.