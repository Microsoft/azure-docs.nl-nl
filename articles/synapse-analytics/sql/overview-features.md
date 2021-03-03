---
title: Verschillen in T-SQL-functies in Synapse SQL
description: Lijst met Transact-SQL-functies die beschikbaar zijn in Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 769149d49d4d233c5c202f570ceb871365728c59
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671230"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Transact-SQL-functies die worden ondersteund in Azure Synapse SQL

Azure Synapse SQL is een analyseservice voor big data waarmee u uw gegevens kunt opvragen en analyseren met behulp van de taal T-SQL. U kunt voor gegevensanalyse een standaard ANSI-compatibel dialect gebruiken van een SQL-taal die op SQL Server en Azure SQL Database wordt gebruikt. 

De Transact-SQL-taal die in serverloze SQL-pools en toegewezen modellen wordt gebruikt, kan verwijzen naar verschillende objecten; de set ondersteunde functies kan onderling enigszins verschillen. Op deze pagina vindt u een overzicht van de belangrijkste verschillen in de Transact-SQL-taal tussen de verbruiksmodellen van Synapse SQL.

## <a name="database-objects"></a>Databaseobjecten

Met verbruiksmodellen in Synapse SQL kunt u verschillende databaseobjecten gebruiken. De vergelijking van ondersteunde objecttypen wordt weergegeven in de volgende tabel:

|   | Toegewezen | Serverloos |
| --- | --- | --- |
| **Tabellen** | [Ja](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Nee, een serverloos model kan alleen query's uitvoeren op externe gegevens in [Azure Storage](#storage-options) |
| **Weergaven** | [Ja](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). Weergaven kunnen [querytaalelementen](#query-language) gebruiken die beschikbaar zijn in het toegewezen model. | [Ja](/sql/t-sql/statements/create-view-transact-sql?view=azure-sqldw-latest&preserve-view=true). Weergaven kunnen [querytaalelementen](#query-language) gebruiken die beschikbaar zijn in een serverloos model. |
| **Schema's** | [Ja](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Ja](/sql/t-sql/statements/create-schema-transact-sql?view=azure-sqldw-latest&preserve-view=true) |
| **Tijdelijke tabellen** | [Ja](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nee |
| **Procedures** | [Ja](/sql/t-sql/statements/create-procedure-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja |
| **Functies** | [Ja](/sql/t-sql/statements/create-function-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) | Ja, alleen inline tabelfuncties. |
| **Triggers** | Nee | Nee |
| **Externe tabellen** | [Ja](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Zie ondersteunde [gegevensindelingen](#data-formats). | [Ja](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true). Zie ondersteunde [gegevensindelingen](#data-formats). |
| **Query's opslaan in de cache** | Ja, meerdere formulieren (op SSD gebaseerde caching, in-memory, caching van resultatensets). Daarnaast wordt Gerealiseerde weergave ondersteund | Nee |
| **Tabelvariabelen** | [Nee](/sql/t-sql/data-types/table-transact-sql?view=azure-sqldw-latest&preserve-view=true), gebruik tijdelijke tabellen | Nee |
| **[Tabeldistributie](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Ja | Nee |
| **[Tabelindexen](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Ja | Nee |
| **[Tabelpartities](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Ja | Nee |
| **[Statistieken](develop-tables-statistics.md)**            | Ja | Ja |
| **[Werkbelastingbeheer, resourceklassen en gelijktijdigheidsbeheer](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Ja    | Nee |
| **Kostenbeheer** | Ja, met behulp van omhoog en omlaag schalen. | Ja, met behulp van [Azure Portal of T-SQL-procedure](./data-processed.md#cost-control). |

## <a name="query-language"></a>Querytaal

Querytalen die in Synapse SQL worden gebruikt, kunnen afhankelijk van het verbruiksmodel verschillende ondersteunde functies hebben. De volgende tabel toont een overzicht van de belangrijkste verschillen in de querytaal in Transact-SQL-dialecten:

|   | Toegewezen | Serverloos |
| --- | --- | --- |
| **SELECT-instructie** | Ja. De Transact-SQL-querycomponenten [FOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true) en [MATCH](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true) worden niet ondersteund. | Ja. Transact-SQL-querycomponenten [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?view=azure-sqldw-latest&preserve-view=true), [MATCH](/sql/t-sql/queries/match-sql-graph?view=azure-sqldw-latest&preserve-view=true), [PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true) en queryhints worden niet ondersteund. |
| **INSERT-instructie** | Ja | Nee |
| **UPDATE-instructie** | Ja | Nee |
| **DELETE-instructie** | Ja | Nee |
| **MERGE-instructie** | Ja ([Preview-versie](/sql/t-sql/statements/merge-transact-sql?view=azure-sqldw-latest&preserve-view=true)) | Nee |
| **[Transacties](develop-transactions.md)** | Ja | Ja, van toepassing op metagegevensobjecten. |
| **[Labels](develop-label.md)** | Ja | Nee |
| **Gegevens laden** | Ja. Het hulpprogramma dat de voorkeur heeft, is de [COPY](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)-instructie, maar het systeem ondersteunt zowel BULKsgewijs laden (BCP) als [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) voor het laden van gegevens. | Nee |
| **Gegevensexport** | Ja. Gebruik van [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true). | Ja. Gebruik van [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true). |
| **Typen** | Ja, alle Transact-SQL-typen behalve [cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), [ntext, text en image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), [ruimtelijke typen](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true) en [xml](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja, alle Transact-SQL-typen behalve [cursor](/sql/t-sql/data-types/cursor-transact-sql?view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?view=azure-sqldw-latest&preserve-view=true), [ntext, text en image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?view=azure-sqldw-latest&preserve-view=true), [ruimtelijke typen](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?view=azure-sqldw-latest&preserve-view=true), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?view=azure-sqldw-latest&preserve-view=true), [xml](/sql/t-sql/xml/xml-transact-sql?view=azure-sqldw-latest&preserve-view=true) en Table-type |
| **Query's tussen databases** | Nee | Ja, met inbegrip van de [USE](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true)-instructie. |
| **Ingebouwde functies (analyse)** | Ja, alle [analyse-](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), conversie-, [datum- en tijd-](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), logische en [wiskundige](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) functies van Transact-SQL-functies, behalve [CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?view=azure-sqldw-latest&preserve-view=true), [IIF](/sql/t-sql/functions/logical-functions-iif-transact-sql?view=azure-sqldw-latest&preserve-view=true) en [PARSE](/sql/t-sql/functions/parse-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja, alle [analyse-](/sql/t-sql/functions/analytic-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), conversie-, [datum- en tijd-](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), logische en [wiskundige](/sql/t-sql/functions/mathematical-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) functies van Transact-SQL. |
| **Ingebouwde functies (tekst)** | Ja. Alle [tekenreeks-](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), [JSON-](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) en sorteringsfuncties van Transact-SQL, behalve [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?view=azure-sqldw-latest&preserve-view=true) en [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja. Alle [tekenreeks-](/sql/t-sql/functions/string-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true), [JSON-](/sql/t-sql/functions/json-functions-transact-sql?view=azure-sqldw-latest&preserve-view=true) en sorteringsfuncties van Transact-SQL. |
| **Ingebouwde functies voor tabelwaarden** | Ja, [Transact-SQL-rijensetfuncties](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions), behalve [OPENXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true) en [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja, [Transact-SQL-rijensetfuncties](/sql/t-sql/functions/functions?view=azure-sqldw-latest&preserve-view=true#rowset-functions), behalve [OPENXML](/sql/t-sql/functions/openxml-transact-sql?view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?view=azure-sqldw-latest&preserve-view=true) en [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?view=azure-sqldw-latest&preserve-view=true)  |
| **Aggregaties** |  Ingebouwde aggregaties van Transact-SQL, behalve [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?view=azure-sqldw-latest&preserve-view=true) en [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ingebouwde aggregaties van Transact-SQL. |
| **Operators** | Ja, alle [Transact-SQL-operators](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true) behalve [! >](/sql/t-sql/language-elements/not-greater-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) en [! <](/sql/t-sql/language-elements/not-less-than-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja, alle [Transact-SQL-operators](/sql/t-sql/language-elements/operators-transact-sql?view=azure-sqldw-latest&preserve-view=true)  |
| **Stroombesturing** | Ja. Alle [stroombesturingsinstructies van Transact-SQL](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) behalve [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?view=azure-sqldw-latest&preserve-view=true), [GOTO](/sql/t-sql/language-elements/goto-transact-sql?view=azure-sqldw-latest&preserve-view=true), [RETURN](/sql/t-sql/language-elements/return-transact-sql?view=azure-sqldw-latest&preserve-view=true), [USE](/sql/t-sql/language-elements/use-transact-sql?view=azure-sqldw-latest&preserve-view=true) en [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Ja. Alle [stroombesturingsinstructies van Transact-SQL](/sql/t-sql/language-elements/control-of-flow?view=azure-sqldw-latest&preserve-view=true) SELECT-query in `WHILE (...)`-voorwaarde |
| **DDL-instructies (CREATE, ALTER, DROP)** | Ja. Alle DDL-instructies van Transact-SQL die van toepassing zijn op de ondersteunde objecttypen | Ja. Alle DDL-instructies van Transact-SQL die van toepassing zijn op de ondersteunde objecttypen |

## <a name="security"></a>Beveiliging

Met Synapse SQL kunt u ingebouwde beveiligingsfuncties gebruiken om uw gegevens te beveiligen en de toegang te beheren. In de volgende tabel worden de belangrijkste verschillen vergeleken tussen de Synapse SQL-verbruiksmodellen.

|   | Toegewezen | Serverloos |
| --- | --- | --- |
| **Aanmeldingen** | N.v.t. (alleen opgenomen gebruikers worden ondersteund in databases) | Ja |
| **Gebruikers** |  N.v.t. (alleen opgenomen gebruikers worden ondersteund in databases) | Ja |
| **[Ingesloten gebruikers](/sql/relational-databases/security/contained-database-users-making-your-database-portable?view=azure-sqldw-latest&preserve-view=true)** | Ja. **Opmerking:** slechts één Azure Active Directory-gebruiker kan een onbeperkte beheerder zijn | Nee |
| **Verificatie van SQL-gebruikersnaam/-wachtwoord**| Ja | Ja |
| **Azure Active Directory-verificatie (Azure AD)**| Ja, Azure AD-gebruikers | Ja, Azure AD-aanmeldingen en -gebruikers |
| **Passthrough-verificatie voor opslag voor Azure AD (Azure Active Directory)** | Ja | Ja |
| **SAS-tokenverificatie voor opslag** | Nee | Ja, met behulp van [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) in [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) of [CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) op exemplaarniveau. |
| **Verificatie met toegangssleutel voor opslag** | Ja, met behulp van [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true) in [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Nee |
| **Verificatie van [beheerde identiteit](../security/synapse-workspace-managed-identity.md) voor opslag** | Ja, met behulp van [Managed Service Identity-referenties](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&preserve-view=true&toc=%2fazure%2fsynapse-analytics%2ftoc.json&view=azure-sqldw-latest&preserve-view=true) | Ja, met `Managed Identity`-referenties. |
| **Verificatie van toepassingsidentiteit voor opslag** | [Ja](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Nee |
| **Machtigingen - objectniveau** | Ja, inclusief de mogelijkheid om machtigingen voor gebruikers toe te kennen, te weigeren en in te trekken | Ja, inclusief de mogelijkheid om machtigingen toe te kennen, te weigeren en in te trekken voor gebruikers/aanmeldingen op de ondersteunde systeemobjecten |
| **Machtigingen - schemaniveau** | Ja, inclusief de mogelijkheid om machtigingen voor gebruikers/aanmeldingen in het schema toe te kennen, te weigeren en in te trekken | Ja, inclusief de mogelijkheid om machtigingen voor gebruikers/aanmeldingen in het schema toe te kennen, te weigeren en in te trekken |
| **Machtigingen - [databaseniveau](/sql/relational-databases/security/authentication-access/database-level-roles?view=azure-sqldw-latest&preserve-view=true)** | Ja | Ja |
| **Machtigingen - [serverniveau](/sql/relational-databases/security/authentication-access/server-level-roles)** | Nee | Ja, sysadmin en andere serverrollen worden ondersteund |
| **Machtigingen - [Beveiliging op kolomniveau](../sql-data-warehouse/column-level-security.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)** | Ja | Ja |
| **Rollen/groepen** | Ja (databasebereik) | Ja (zowel server- als databasebereik) |
| **Beveiligings- &amp; identiteitsfuncties** | Enkele Transact-SQL-beveiligingsfuncties en -operators: `CURRENT_USER`, `HAS_DBACCESS`, `IS_MEMBER`, `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS`, `OPEN/CLOSE MASTER KEY` | Enkele Transact-SQL-beveiligingsfuncties en -operators: `CURRENT_USER`, `HAS_DBACCESS`, `HAS_PERMS_BY_NAME`, `IS_MEMBER', 'IS_ROLEMEMBER`, `IS_SRVROLEMEMBER`, `SESSION_USER`, `SESSION_CONTEXT`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS` en `REVERT`. Beveiligingsfuncties kunnen niet worden gebruikt om externe gegevens op te vragen (sla het resultaat op in een variabele die in de query kan worden gebruikt).  |
| **REFERENTIES MET DATABASEBEREIK** | Ja | Ja |
| **REFERENTIES MET SERVERBEREIK** | Nee | Ja |
| **Beveiliging op rijniveau** | [Ja](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nee |
| **TDE (Transparent Data Encryption)** | [Ja](../../azure-sql/database/transparent-data-encryption-tde-overview.md) | Nee | 
| **Gegevensdetectie en -classificatie** | [Ja](../../azure-sql/database/data-discovery-and-classification-overview.md) | Nee |
| **Beoordeling van beveiligingsproblemen** | [Ja](../../azure-sql/database/sql-vulnerability-assessment.md) | Nee |
| **Advanced Threat Protection** | [Ja](../../azure-sql/database/threat-detection-overview.md)
| **Controle** | [Ja](../../azure-sql/database/auditing-overview.md) | Nee |
| **[Firewallregels](../security/synapse-workspace-ip-firewall.md)**| Ja | Ja |
| **[Privé-eindpunt](../security/synapse-workspace-managed-private-endpoints.md)**| Ja | Ja |

Toegewezen SQL-pools en serverloze SQL-pools gebruiken de Transact-SQL-standaardtaal om gegevens op te vragen. Raadpleeg de [Transact-SQL-taalverwijzing](/sql/t-sql/language-reference) voor gedetailleerde verschillen.

## <a name="tools"></a>Hulpprogramma's

U kunt verschillende hulpprogramma's gebruiken om verbinding te maken met Synapse SQL om gegevens op te vragen.

|   | Toegewezen | Serverloos |
| --- | --- | --- |
| **Synapse Studio** | Ja, SQL-scripts | Ja, SQL-scripts |
| **Power BI** | Ja | [Ja](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Service** | Ja | Ja |
| **Azure Data Studio** | Ja | Ja, versie 1.18.0 of hoger. SQL-scripts en SQL-notebooks worden ondersteund. |
| **SQL Server Management Studio** | Ja | Ja, versie 18.5 of hoger |

> [!NOTE]
> U kunt SSMS gebruiken om verbinding te maken met een serverloze SQL-pool en query's uit te voeren. Het wordt gedeeltelijk ondersteund vanaf versie 18.5, maar u kunt de tool gebruiken om alleen verbinding te maken en query's uit te voeren.

Voor de meeste toepassingen wordt de standaard Transact-SQL-taal gebruikt om gegevens op te vragen bij zowel toegewezen als serverloze verbruiksmodellen van Synapse SQL.

## <a name="storage-options"></a>Opslagopties

Geanalyseerde gegevens kunnen op verschillende opslagtypen worden opgeslagen. De volgende tabel bevat een lijst van alle beschikbare opslagopties:

|   | Toegewezen | Serverloos |
| --- | --- | --- |
| **Interne opslag** | Ja | Nee |
| **Azure Data Lake v2** | Ja | Ja |
| **Azure Blob Storage** | Ja | Ja |
| **Azure SQL (extern)** | Nee | Nee |
| **Transactionele opslag in Azure CosmosDB** | Nee | Nee |
| **Azure CosmosDB analytical storage** | Nee | Ja, met behulp van [Synapse Link (preview)](../../cosmos-db/synapse-link.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) ([openbare preview](../../cosmos-db/synapse-link.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json#limitations)) |
| **Apache Spark-tabellen (in werkruimte)** | Nee | Alleen PARQUET-tabellen met behulp van [synchronisatie van metagegevens](develop-storage-files-spark-tables.md) |
| **Apache Spark-tabellen (extern)** | Nee | Nee |
| **Databricks-tabellen (extern)** | Nee | Nee |

## <a name="data-formats"></a>Bestandsindelingen

Geanalyseerde gegevens kunnen in verschillende bestandsindelingen worden opgeslagen. De volgende tabel bevat een lijst van alle beschikbare gegevensindelingen die kunnen worden geanalyseerd:

|   | Toegewezen | Serverloos |
| --- | --- | --- |
| **Met scheidingstekens** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Ja](query-single-csv-file.md) |
| **CSV** | Ja (het gebruik van meerdere scheidingstekens wordt niet ondersteund) | [Ja](query-single-csv-file.md) |
| **Parquet** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | [Ja](query-parquet-files.md), inclusief bestanden met [geneste typen](query-parquet-nested-types.md) |
| **Hive ORC** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Nee |
| **Hive RC** | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Nee |
| **JSON** | Ja | [Ja](query-json-files.md) |
| **Avro** | Nee | Nee |
| **[Delta-lake](https://delta.io/)** | Nee | Nee |
| **[CDM](/common-data-model/)** | Nee | Nee |

## <a name="next-steps"></a>Volgende stappen
Aanvullende informatie over best practices voor toegewezen SQL-pools en serverloze SQL-pools vindt u in de volgende artikelen:

- [Best practices voor toegewezen SQL-pools](best-practices-sql-pool.md)
- [Best practices voor serverloze SQL-pools](best-practices-sql-on-demand.md)