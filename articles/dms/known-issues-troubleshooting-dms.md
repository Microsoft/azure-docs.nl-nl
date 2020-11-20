---
title: Veelvoorkomende problemen-Azure Database Migration Service
description: Meer informatie over het oplossen van veelvoorkomende bekende problemen/fouten die zijn gekoppeld aan het gebruik van Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: f4baca7f261aa7544b54992a5e1ddf620794774f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962277"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Veelvoorkomende problemen met Azure Database Migration Service oplossen

In dit artikel worden enkele veelvoorkomende problemen en fouten beschreven die Azure Database Migration Service gebruikers kunnen aankomen. Het artikel bevat ook informatie over het oplossen van deze problemen en fouten.

> [!NOTE]
> Oordeelloze communicatie
>
> Microsoft biedt ondersteuning voor een gevarieerde en insluitende omgeving. Dit artikel bevat verwijzingen naar het woord _slaaf_. In de [stijlgids voor oordeelloze communicatie](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) wordt dit woord herkend als uitsluitend. Het woord wordt in dit artikel gebruikt voor consistentie, omdat het momenteel het woord is dat wordt weergegeven in de software. Wanneer de software is bijgewerkt om het woord te verwijderen, wordt dit artikel ook bijgewerkt zodat het is afgestemd.
>

## <a name="migration-activity-in-queued-state"></a>Migratie activiteit in de wachtrij status

Wanneer u nieuwe activiteiten in een Azure Database Migration Service project maakt, blijven de activiteiten in de wachtrij staat.

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| Dit probleem treedt op wanneer de Azure Database Migration Service-instantie de maximum capaciteit heeft bereikt voor lopende taken die gelijktijdig worden uitgevoerd. Er wordt een nieuwe activiteit in de wachtrij geplaatst totdat de capaciteit weer beschikbaar wordt. | Controleer of het Data Migration service-exemplaar activiteiten uitvoert in projecten. U kunt door gaan met het maken van nieuwe activiteiten die automatisch worden toegevoegd aan de wachtrij voor uitvoering. Zodra een van de bestaande uitgevoerde activiteiten is voltooid, wordt de volgende activiteit in de wachtrij gestart en wordt de status automatisch gewijzigd in het uitvoeren van de status. U hoeft geen verdere actie te ondernemen om de migratie van activiteiten in de wachtrij te starten.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Maximum aantal data bases dat is geselecteerd voor migratie

De volgende fout treedt op bij het maken van een activiteit voor een database migratie project voor het verplaatsen naar Azure SQL Database of een door Azure SQL beheerd exemplaar:

* **Fout**: validatie fout van de migratie-instellingen "," errorDetail ":" er is meer dan het maximum aantal ' 4 ' objecten van data bases geselecteerd voor migratie. "

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| Deze fout wordt weer gegeven wanneer u meer dan vier data bases hebt geselecteerd voor één migratie activiteit. Op dit moment is elke migratie activiteit beperkt tot vier data bases. | Selecteer vier of minder data bases per migratie activiteit. Als u meer dan vier data bases parallel wilt migreren, moet u een andere instantie van Azure Database Migration Service inrichten. Op dit moment ondersteunt elk abonnement Maxi maal twee Azure Database Migration Service exemplaren.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Fouten voor MySQL-migratie naar Azure MySQL met herstel fouten

Wanneer u migreert van MySQL naar Azure Database for MySQL met behulp van Azure Database Migration Service, mislukt de migratie activiteit met de volgende fout:

* **Fout**: database migratie fout-taak ' TaskID ' is onderbroken vanwege het mislukken van een opeenvolgende herstel bewerking door [n].

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| Deze fout kan optreden als de gebruiker die de migratie uitvoert, ReplicationAdmin Role en/of privileges van de replicatie-CLIENT, replicatie REPLICA en SUPER (eerdere versies dan MySQL 5.6.6) ontbreekt.<br><br><br><br><br><br><br><br><br><br><br><br><br> | Zorg ervoor dat de [vereiste bevoegdheden](./tutorial-mysql-azure-mysql-online.md#prerequisites) voor het gebruikers account nauw keurig zijn geconfigureerd op het Azure database for MySQL-exemplaar. U kunt bijvoorbeeld de volgende stappen volgen om een gebruiker met de naam ' migrateuser ' te maken met de vereiste bevoegdheden:<br>1. Maak de gebruikers migrateuser@ '% ', geïdentificeerd door ' geheim '; <br>2. ken alle bevoegdheden voor db_name. * toe aan ' migrateuser ' @ '% ', geïdentificeerd door ' geheim '; Herhaal deze stap om toegang te verlenen aan meer data bases <br>3. Grant replicatie slave op *.* op ' migrateuser ' @ '% ' geïdentificeerd door ' geheim ';<br>4. Grant Replication-client op *.* op ' migrateuser ' @ '% ' geïdentificeerd door ' geheim ';<br>5. machtigingen voor leegmaken; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Fout bij het stoppen van de Azure Database Migration Service

U ontvangt de volgende fout bij het stoppen van het Azure Database Migration Service-exemplaar:

* **Fout**: de service is niet gestopt. Fout: {'error':{'code':'InvalidRequest','message':'Een of meer activiteiten worden momenteel uitgevoerd. Als u de service wilt stoppen, wacht u totdat de activiteiten zijn voltooid of stopt u deze activiteiten hand matig en probeert u het opnieuw.}}

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| Deze fout wordt weer gegeven wanneer het service-exemplaar dat u probeert te stoppen, activiteiten bevat die nog in de migratie projecten worden uitgevoerd of aanwezig zijn. <br><br><br><br><br><br> | Zorg ervoor dat er geen activiteiten worden uitgevoerd in het exemplaar van Azure Database Migration Service dat u probeert te stoppen. U kunt ook de activiteiten of projecten verwijderen voordat u probeert de service te stoppen. De volgende stappen laten zien hoe u projecten kunt verwijderen om het migratie service-exemplaar op te schonen door alle actieve taken te verwijderen:<br>1. Install-Module naam AzureRM. DataMigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription-Subscriptionname " \<subName> " <br> 4. Remove-AzureRmDataMigrationProject-name \<projectName> -ResourceGroupName \<rgName> -ServiceName \<serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Fout bij het starten van Azure Database Migration Service

U ontvangt de volgende fout bij het starten van het Azure Database Migration Service-exemplaar:

* **Fout**: de service kan niet worden gestart. Fout: {' errorDetail ': ' de service kan niet worden gestart. Neem contact op met micro soft ondersteuning '}

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| Deze fout wordt weer gegeven wanneer het vorige exemplaar niet intern is mislukt. Deze fout treedt zelden op en het technische team is hiervan op de hoogte. <br> | Verwijder het exemplaar van de service dat u niet kunt starten en richt het nieuwe in om het te vervangen. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Fout bij het herstellen van de data base tijdens het migreren van SQL naar Azure SQL data base Managed instance

Wanneer u een online migratie uitvoert van SQL Server naar een beheerd exemplaar van Azure SQL, mislukt de cutover met de volgende fout:

* **Fout**: de herstel bewerking is mislukt voor de bewerkings-id ' operationId '. Code ' AuthorizationFailed ', Message ' de client ' clientId ' met object-id ' objectId ' heeft geen machtiging om de actie ' micro soft. SQL/locations/managedDatabaseRestoreAzureAsyncOperation/read ' over scope '/subscriptions/subscriptionId ' uit te voeren.

| Oorzaak         | Oplossing    |
| ------------- | ------------- |
| Deze fout geeft aan dat de toepassings-principal die wordt gebruikt voor online migratie van SQL Server naar SQL Managed instance geen Contribute-machtiging heeft voor het abonnement. Voor bepaalde API-aanroepen met het beheerde exemplaar is deze machtiging vereist voor de herstel bewerking. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Gebruik de `Get-AzureADServicePrincipal` Power shell-cmdlet die `-ObjectId` beschikbaar is in het fout bericht om een lijst weer te geven met de weergave naam van de toepassings-id die wordt gebruikt.<br><br> Valideer de machtigingen voor deze toepassing en controleer of deze de [rol Inzender](../role-based-access-control/built-in-roles.md#contributor) heeft op het abonnements niveau. <br><br> Het Azure Database Migration Service engineering team is bezig om de vereiste toegang te beperken voor de huidige Contribute-rol bij het abonnement. Als u een zakelijke vereiste hebt die het gebruik van een Contribute-functie niet toestaat, neemt u contact op met de ondersteuning van Azure voor meer informatie. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Fout bij het verwijderen van de NIC die is gekoppeld aan Azure Database Migration Service

Wanneer u probeert een netwerk interface kaart te verwijderen die is gekoppeld aan Azure Database Migration Service, mislukt de verwijderings poging met deze fout:

* **Fout**: kan de NIC die is gekoppeld aan Azure database Migration service niet verwijderen vanwege het gebruik van de NIC-service

| Oorzaak         | Oplossing    |
| ------------- | ------------- |
| Dit probleem treedt op wanneer het Azure Database Migration Service-exemplaar mogelijk nog steeds aanwezig is en gebruikmaakt van de NIC. <br><br><br><br><br><br><br><br> | Als u deze NIC wilt verwijderen, verwijdert u het DMS-service-exemplaar dat automatisch de NIC verwijdert die door de service wordt gebruikt.<br><br> **Belang rijk**: Zorg ervoor dat het Azure database Migration service exemplaar dat wordt verwijderd, geen actieve activiteiten heeft.<br><br> Nadat alle projecten en activiteiten die aan het Azure Database Migration Service exemplaar zijn gekoppeld, zijn verwijderd, kunt u het service-exemplaar verwijderen. De NIC die wordt gebruikt door het service-exemplaar wordt automatisch gereinigd als onderdeel van de service verwijdering. |

## <a name="connection-error-when-using-expressroute"></a>Verbindingsfout bij het gebruik van ExpressRoute

Als u verbinding wilt maken met de bron in de wizard Azure Database Migration-serviceproject, mislukt de verbinding na een langere time-out, als de bron gebruikmaakt van ExpressRoute voor connectiviteit.

| Oorzaak         | Oplossing    |
| ------------- | ------------- |
| Wanneer u [ExpressRoute](https://azure.microsoft.com/services/expressroute/)gebruikt, [moet](./tutorial-sql-server-azure-sql-online.md) Azure database Migration service drie service-eind punten inrichten op het Virtual Network subnet dat is gekoppeld aan de service:<br> --Service Bus-eind punt<br> --Eind punt van opslag<br> --Doel database-eind punt (bijvoorbeeld SQL-eind punt, Cosmos DB-eind punt)<br><br><br><br><br> | [Schakel](./tutorial-sql-server-azure-sql-online.md) de vereiste service-eind punten in voor de ExpressRoute-connectiviteit tussen de bron en de Azure database Migration service. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Fout tijdens vergren deling wacht tijd bij het migreren van een MySQL-data base naar Azure DB voor MySQL

Wanneer u via Azure Database Migration Service een MySQL-data base migreert naar een Azure Database for MySQL-exemplaar, mislukt de migratie met de volgende fout in de vergrendelings wachttijd:

* **Fout**: kan het bestand niet laden vanwege een database migratie fout-kan het laden van het bestand ' n ' RetCode niet starten: SQL_ERROR SQLSTATE: HY000 NativeError: 1205 bericht: [mysql] [ODBC-stuur programma] [mysqld] vergrendelings wachttijd voor vergren deling overschreden; trans actie opnieuw starten

| Oorzaak         | Oplossing    |
| ------------- | ------------- |
| Deze fout treedt op wanneer de migratie is mislukt vanwege een time-out tijdens de migratie. | Overweeg de waarde van de server parameter **innodb_lock_wait_timeout** te verhogen. De hoogst toegestane waarde is 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Fout bij het verbinden met de bron SQL Server bij het gebruik van een dynamische poort of een benoemd exemplaar

Wanneer u probeert om Azure Database Migration Service verbinding te maken met SQL Server bron die wordt uitgevoerd op een benoemd exemplaar of een dynamische poort, mislukt de verbinding met deze fout:

* **Fout**:-1-SQL-verbinding is mislukt. Een netwerkgerelateerde of exemplaarspecifieke fout is opgetreden bij het maken van een verbinding met SQL Server. De server wordt niet gevonden of toegang tot de server is niet mogelijk. Controleer of de naam van het exemplaar juist is en of SQL Server is geconfigureerd om externe verbindingen toe te staan. (provider: SQL-netwerk interfaces, fout: 26-fout bij zoeken naar opgegeven server/exemplaar)

| Oorzaak         | Oplossing    |
| ------------- | ------------- |
| Dit probleem treedt op wanneer het bron SQL Server exemplaar waarmee Azure Database Migration Service verbinding probeert te maken, een dynamische poort heeft of een benoemd exemplaar gebruikt. De SQL Server Browser-service luistert naar UDP-poort 1434 voor binnenkomende verbindingen met een benoemd exemplaar of bij het gebruik van een dynamische poort. De dynamische poort kan elke keer worden gewijzigd SQL Server service opnieuw wordt gestart. U kunt de dynamische poort controleren die is toegewezen aan een exemplaar via netwerk configuratie in SQL Server Configuration Manager.<br><br><br> |Controleer of Azure Database Migration Service verbinding kan maken met de bron SQL Server Browser service op UDP-poort 1434 en het SQL Server exemplaar via de dynamisch toegewezen TCP-poort die van toepassing is. |

## <a name="additional-known-issues"></a>Meer bekende problemen

* [Bekende problemen/migratie beperkingen met online migraties naar Azure SQL Database](./known-issues-azure-sql-online.md)
* [Bekende problemen/migratie beperkingen met online migraties naar Azure Database for MySQL](./known-issues-azure-mysql-online.md)
* [Bekende problemen/migratie beperkingen met online migraties naar Azure Database for PostgreSQL](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>Volgende stappen

* Bekijk het artikel [Azure database Migration Service Power shell](/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Bekijk het artikel [How to Configure Server para meters in azure database for MySQL met behulp van de Azure Portal](../mysql/howto-server-parameters.md).
* Bekijk het artikel [overzicht van de vereisten voor het gebruik van Azure database Migration service](./pre-reqs.md).
* Raadpleeg de [Veelgestelde vragen over het gebruik van Azure database Migration service](./faq.md).