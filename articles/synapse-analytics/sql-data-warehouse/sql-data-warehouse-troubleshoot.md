---
title: Problemen met toegewezen SQL-pool oplossen (voorheen SQL DW)
description: Problemen met toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: af653585ec1b57b5fd697dc755e495a96e04e677
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565403"
---
# <a name="troubleshooting-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Problemen met toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics

In dit artikel vindt u algemene probleemoplossingsproblemen in toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics.

## <a name="connecting"></a>Verbinding maken

| Probleem                                                        | Oplossing                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Aanmelding mislukt voor gebruiker 'NT AUTHORITY\ANONYMOUS LOGON'. (Microsoft SQL Server fout: 18456) | Deze fout treedt op wanneer een Azure AD-gebruiker verbinding probeert te maken met de hoofddatabase, maar geen gebruiker in de hoofddatabase heeft.  Als u dit probleem wilt oplossen, geeft u de toegewezen SQL-pool (voorheen SQL DW) op die u wilt verbinden op het tijdstip van de verbinding of voegt u de gebruiker toe aan de hoofddatabase.  Zie [het artikel Beveiligingsoverzicht](sql-data-warehouse-overview-manage-security.md) voor meer informatie. |
| De server-principal 'MijnGebruikersnaam' heeft in de huidige beveiligingscontext geen toegang tot de hoofddatabase. Kan de standaarddatabase van de gebruiker niet openen. Aanmelden mislukt. Aanmelden is mislukt voor gebruiker 'MijnGebruikersnaam'. (Microsoft SQL Server, Fout: 916) | Deze fout treedt op wanneer een Azure AD-gebruiker verbinding probeert te maken met de hoofddatabase, maar geen gebruiker in de hoofddatabase heeft.  U kunt dit probleem oplossen door de toegewezen SQL-pool (voorheen SQL DW) op te geven die u wilt verbinden op het tijdstip van de verbinding of door de gebruiker toe te voegen aan de hoofddatabase.  Zie [het artikel Beveiligingsoverzicht](sql-data-warehouse-overview-manage-security.md) voor meer informatie. |
| CTAIP-fout                                                  | Deze fout kan optreden wanneer er een aanmelding is gemaakt op de SQL Database-hoofddatabase, maar niet in de specifieke SQL database.  Als u deze fout tegenkomt, bekijkt u het artikel [Beveiligingsoverzicht.](sql-data-warehouse-overview-manage-security.md)  In dit artikel wordt uitgelegd hoe u een aanmelding en gebruiker maakt in de hoofddatabase en vervolgens hoe u een gebruiker maakt in een SQL database. |
| Geblokkeerd door firewall                                          | Toegewezen SQL-pool (voorheen SQL DW) wordt beveiligd door firewalls om ervoor te zorgen dat alleen bekende IP-adressen toegang hebben tot een database. De firewalls zijn standaard beveiligd, wat betekent dat u het IP-adres of adresbereik expliciet moet inschakelen voordat u verbinding kunt maken.  Volg de stappen in Serverfirewalltoegang voor uw [client-IP](create-data-warehouse-portal.md) configureren in de inrichtingsinstructies om uw firewall te configureren [voor toegang.](create-data-warehouse-portal.md) |
| Kan geen verbinding maken met hulpprogramma of stuurprogramma                           | Toegewezen SQL-pool (voorheen SQL DW) raadt u aan [om SSMS,](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [SSDT](sql-data-warehouse-install-visual-studio.md)voor Visual Studio of [sqlcmd te](sql-data-warehouse-get-started-connect-sqlcmd.md) gebruiken om een query uit te voeren op uw gegevens. Zie de artikelen Stuurprogramma's voor Azure Synapse [](sql-data-warehouse-connection-strings.md) en Verbinding maken met Azure Synapse voor meer informatie over stuurprogramma Azure Synapse en verbinding maken [met](sql-data-warehouse-connect-overview.md) Azure Synapse. |

## <a name="tools"></a>Hulpprogramma's

| Probleem                                                        | Oplossing                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio Azure AD-gebruikers ontbreken in objectverkenner           | Dit is een bekend probleem.  Als tijdelijke oplossing kunt u de gebruikers weergeven in [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Zie [Verificatie voor Azure Synapse](sql-data-warehouse-authentication.md) meer informatie over het gebruik van Azure Active Directory met toegewezen SQL-pool (voorheen SQL DW). |
| Handmatige scripting, het gebruik van de scriptwizard of het maken van verbinding via SSMS is traag, reageert niet of produceert fouten | Zorg ervoor dat gebruikers zijn gemaakt in de hoofddatabase. Zorg er bij scriptopties ook voor dat de engine-editie is ingesteld op 'Microsoft Azure Synapse Analytics Edition' en dat het enginetype 'Microsoft Azure SQL Database'. |
| Genereren van scripts mislukt in SSMS                               | Het genereren van een script voor toegewezen SQL-pool (voorheen SQL DW) mislukt als de optie Script genereren voor afhankelijke objecten is ingesteld op Waar. Als tijdelijke oplossing moeten gebruikers handmatig naar Extra **->-opties ->SQL Server-objectverkenner -> Script** genereren voor afhankelijke opties gaan en instellen op onwaar |

## <a name="data-ingestion-and-preparation"></a>Gegevensopname en -voorbereiding

| Probleem                                                        | Oplossing                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Het exporteren van lege tekenreeksen met behulp van CETAS resulteert in NULL-waarden in Parquet- en ORC-bestanden. Opmerking: als u lege tekenreeksen exporteert uit kolommen met NOT NULL-beperkingen, resulteert CETAS in afgewezen records en kan de export mislukken. | Verwijder lege tekenreeksen of de aanstootgevende kolom in de SELECT-instructie van uw CETAS. |
| Het laden van een waarde buiten het bereik van 0-127 in een tinyint-kolom voor parquet- en ORC-bestandsindeling wordt niet ondersteund. | Geef een groter gegevenstype op voor de doelkolom.           |

## <a name="performance"></a>Prestaties

| Probleem                                                        | Oplossing                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Problemen met queryprestaties oplossen                            | Als u problemen met een bepaalde query probeert op te lossen, begint u met [Leren hoe u uw query's bewaakt.](sql-data-warehouse-manage-monitor.md#monitor-query-execution) |
| Problemen met TempDB-ruimte | [Bemonitor het gebruik van TempDB-ruimte.](sql-data-warehouse-manage-monitor.md#monitor-tempdb)  Veelvoorkomende oorzaken voor het bijna opgebruikte TempDB-ruimte zijn:<br>- Er zijn onvoldoende resources toegewezen aan de query, waardoor gegevens worden geleegd naar TempDB.  Zie [Workloadbeheer](resource-classes-for-workload-management.md) <br>- Statistieken ontbreken of zijn verouderd, wat leidt tot overmatige verplaatsing van gegevens.  Zie [Tabelstatistieken onderhouden voor](sql-data-warehouse-tables-statistics.md) meer informatie over het maken van statistieken<br>- TempDB-ruimte wordt toegewezen per serviceniveau.  [Als u uw toegewezen SQL-pool (voorheen SQL DW)](sql-data-warehouse-manage-compute-overview.md#scaling-compute) schaalt naar een hogere DWU-instelling, wordt meer TempDB-ruimte toegewezen.|
| Slechte queryprestaties en plannen zijn vaak het gevolg van ontbrekende statistieken | De meest voorkomende oorzaak van slechte prestaties is een gebrek aan statistieken in uw tabellen.  Zie [Tabelstatistieken onderhouden voor](sql-data-warehouse-tables-statistics.md) meer informatie over het maken van statistieken en waarom ze essentieel zijn voor uw prestaties. |
| Lage gelijktijdigheid /query's in de wachtrij                             | Inzicht [in Workloadbeheer](resource-classes-for-workload-management.md) is belangrijk om te begrijpen hoe geheugentoewijzing met gelijktijdigheid kan worden ge balanceren. |
| Best practices implementeren                              | De beste plaats om te leren hoe u de queryprestaties kunt verbeteren, is het artikel over toegewezen best practices voor [SQL-pool (voorheen SQL DW).](sql-data-warehouse-best-practices.md) |
| Prestaties verbeteren met schalen                      | Soms kunt u de prestaties verbeteren door gewoon meer rekenkracht toe te voegen aan uw query's door uw toegewezen [SQL-pool (voorheen SQL DW) te schalen.](sql-data-warehouse-manage-compute-overview.md) |
| Slechte queryprestaties als gevolg van slechte indexkwaliteit     | Soms kunnen query's vertragen vanwege slechte [kwaliteit van columnstore-indexen.](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality)  Zie dit artikel voor meer informatie en het herbouwen van [indexen om de segmentkwaliteit te verbeteren.](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality) |

## <a name="system-management"></a>Systeembeheer

| Probleem                                                        | Oplossing                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: kan de bewerking niet uitvoeren omdat de server het toegestane quotum voor de databasetransactieeenheid van 45000 overschrijdt. | Verminder de [DWU van](what-is-a-data-warehouse-unit-dwu-cdwu.md) de database die u wilt maken of [vraag een quotumverhoging aan.](sql-data-warehouse-get-started-create-support-ticket.md) |
| Ruimtegebruik onderzoeken                              | Zie [Tabelgrootten voor](sql-data-warehouse-tables-overview.md#table-size-queries) meer inzicht in het ruimtegebruik van uw systeem. |
| Hulp bij het beheren van tabellen                                    | Zie het [artikel Tabeloverzicht](sql-data-warehouse-tables-overview.md) voor hulp bij het beheren van uw tabellen.  Dit artikel bevat ook koppelingen naar gedetailleerdere onderwerpen zoals Tabelgegevenstypen [,](sql-data-warehouse-tables-data-types.md)Een tabel distribueren, [](sql-data-warehouse-tables-distribute.md) [Een](sql-data-warehouse-tables-index.md)tabel indexeren, [](sql-data-warehouse-tables-partition.md)Een tabel partitioneren, Tabelstatistieken onderhouden en Tijdelijke [tabellen](sql-data-warehouse-tables-temporary.md). [](sql-data-warehouse-tables-statistics.md) |
| TDE-voortgangsbalk (Transparent Data Encryption) wordt niet bijgewerkt in de Azure Portal | U kunt de status van TDE weergeven via [PowerShell.](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) |

## <a name="differences-from-sql-database"></a>Verschillen met SQL Database

| Probleem                                 | Oplossing                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Niet-ondersteunde SQL Database functies     | Zie [Niet-ondersteunde tabelfuncties.](sql-data-warehouse-tables-overview.md#unsupported-table-features) |
| Niet-ondersteunde SQL Database gegevenstypen   | Zie [Niet-ondersteunde gegevenstypen.](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types)        |
| Beperkingen van opgeslagen procedures          | Zie [Beperkingen van opgeslagen procedures voor](sql-data-warehouse-develop-stored-procedures.md#limitations) meer informatie over enkele beperkingen van opgeslagen procedures. |
| UDF's bieden geen ondersteuning voor SELECT-instructies | Dit is een huidige beperking van onze UDF's.  Zie [CREATE FUNCTION voor](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) de syntaxis die we ondersteunen. |
| sp_rename (preview) voor kolommen werkt niet in schema's buiten *dbo* | Dit is een huidige beperking van Synapse [sp_rename (preview) voor kolommen](/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Kolommen in objecten die geen deel uitmaken van *het dbo-schema* kunnen via een CTAS worden gewijzigd in een nieuwe tabel. |

## <a name="next-steps"></a>Volgende stappen

Hier vindt u enkele andere resources die u kunt proberen voor meer hulp bij het vinden van een oplossing voor uw probleem.

* [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Functieverzoeken](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Video's](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Ondersteuningsticket maken](sql-data-warehouse-get-started-create-support-ticket.md)
* [Microsoft Q&A-vragenpagina](/answers/topics/azure-synapse-analytics.html)
* [Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)