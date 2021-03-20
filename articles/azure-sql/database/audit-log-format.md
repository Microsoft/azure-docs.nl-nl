---
title: Indeling van SQL Database audit logboek
description: Begrijpen hoe Azure SQL Database controle logboeken worden gestructureerd.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: reference
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 06/03/2020
ms.openlocfilehash: f5c176db4f679c79bb42c6ceb46b3588e9440874
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572215"
---
# <a name="sql-database-audit-log-format"></a>Indeling van SQL Database audit logboek

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Met [Azure SQL database controle](auditing-overview.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in uw Azure Storage-account geschreven, of verzonden naar Event Hub of log Analytics voor downstream-verwerking en-analyse.

## <a name="naming-conventions"></a>Naamconventies

### <a name="blob-audit"></a>BLOB-controle

Audit logboeken die zijn opgeslagen in Azure Blob-opslag worden opgeslagen in een container `sqldbauditlogs` met de naam in het Azure-opslag account. De Directory-hiërarchie in de container is van het formulier `<ServerName>/<DatabaseName>/<AuditName>/<Date>/` . De indeling van de bestands naam van de blob is `<CreationTime>_<FileNumberInSession>.xel` , in de vorm `CreationTime` van `hh_mm_ss_ms` een UTC-indeling, en `FileNumberInSession` is een lopende index in het geval van sessie logboeken over meerdere BLOB-bestanden.

Voor de Data Base `Database1` op `Server1` het volgende is bijvoorbeeld een mogelijk geldig pad:

`Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel`

[Alleen-lezen replica's](read-scale-out.md) audit logboeken worden opgeslagen in dezelfde container. De Directory-hiërarchie in de container is van het formulier `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/` . De naam van het BLOB-bestand deelt dezelfde indeling. De audit logboeken van alleen-lezen Replica's worden in dezelfde container opgeslagen.


### <a name="event-hub"></a>Event Hub

Controlegebeurtenissen worden geschreven naar de naamruimte en Event Hub die tijdens de controleconfiguratie zijn gedefinieerd. Ze worden vastgelegd in de hoofdtekst van [Apache Avro](https://avro.apache.org/)-gebeurtenissen en opgeslagen met behulp van een JSON-indeling met UTF-8-codering. Als u de auditlogboeken wilt lezen, kunt u [Avro-hulpprogramma's](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) of gelijksoortige hulpmiddelen gebruiken waarmee deze indeling wordt verwerkt.

### <a name="log-analytics"></a>Log Analytics

Controlegebeurtenissen worden geschreven naar de `AzureDiagnostics` tabel met de categorie `SQLSecurityAuditEvents` naar de Log Analytics-werkruimte die tijdens de controleconfiguratie is gedefinieerd. Zie [Naslag voor Log Analytics](../../azure-monitor/logs/log-query-overview.md) voor meer nuttige informatie over de zoektaal en -opdrachten in Log Analytics.

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Controle logboek velden

| Naam (BLOB) | Naam (Event Hubs/Log Analytics) | Beschrijving | Blob-type | Type Event Hubs/Log Analytics |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID van de actie | varchar (4) | tekenreeks |
| action_name | action_name_s | De naam van de actie | N.v.t. | tekenreeks |
| additional_information | additional_information_s | Eventuele aanvullende informatie over de gebeurtenis, opgeslagen als XML | nvarchar (4000) | tekenreeks |
| affected_rows | affected_rows_d | Het aantal rijen dat wordt beïnvloed door de query | bigint | int |
| application_name | application_name_s| Naam van client toepassing | nvarchar (128) | tekenreeks |
| audit_schema_version | audit_schema_version_d | Altijd 1 | int | int |
| class_type | class_type_s | Type controle bare entiteit waarop de audit plaatsvindt | varchar (2) | tekenreeks |
| class_type_desc | class_type_description_s | Beschrijving van de controle bare entiteit waarvoor de audit plaatsvindt | N.v.t. | tekenreeks |
| client_ip | client_ip_s | Bron-IP van de client toepassing | nvarchar (128) | tekenreeks |
| connection_id | N.v.t. | ID van de verbinding in de server | GUID | N.v.t. |
| data_sensitivity_information | data_sensitivity_information_s | Gegevens typen en gevoeligheids labels die worden geretourneerd door de gecontroleerde query, op basis van de geclassificeerde kolommen in de data base. Meer informatie over het [detecteren en classificeren van Azure SQL database gegevens](data-discovery-and-classification-overview.md) | nvarchar (4000) | tekenreeks |
| database_name | database_name_s | De database context waarin de actie is uitgevoerd | sysname | tekenreeks |
| database_principal_id | database_principal_id_d | ID van de database gebruikers context waarin de actie wordt uitgevoerd | int | int |
| database_principal_name | database_principal_name_s | De naam van de database gebruikers context waarin de actie wordt uitgevoerd | sysname | tekenreeks |
| duration_milliseconds | duration_milliseconds_d | Uitvoerings duur van de query in milliseconden | bigint | int |
| event_time | event_time_t | De datum en tijd waarop de Controleer bare actie wordt geactiveerd | datetime2 | datum/tijd |
| host_name | N.v.t. | Hostnaam van client | tekenreeks | N.v.t. |
| is_column_permission | is_column_permission_s | Vlag waarmee wordt aangegeven of dit een machtiging op kolom niveau is. 1 = waar, 0 = ONWAAR | bit | tekenreeks |
| N.v.t. | is_server_level_audit_s | Vlag waarmee wordt aangegeven of deze controle zich op server niveau bevindt | N.v.t. | tekenreeks |
| object_-id | object_id_d | De ID van de entiteit waarop de audit heeft plaatsgevonden. Dit omvat de volgende elementen: Server objecten, data bases, database objecten en schema-objecten. 0 als de entiteit de server zelf is of als de audit niet wordt uitgevoerd op het niveau van een object | int | int |
| object_name | object_name_s | De naam van de entiteit waarop de audit heeft plaatsgevonden. Dit omvat de volgende elementen: Server objecten, data bases, database objecten en schema-objecten. 0 als de entiteit de server zelf is of als de audit niet wordt uitgevoerd op het niveau van een object | sysname | tekenreeks |
| permission_bitmask | permission_bitmask_s | In voorkomend geval worden de machtigingen weer gegeven die zijn verleend, geweigerd of ingetrokken | varbinary (16) | tekenreeks |
| response_rows | response_rows_d | Aantal rijen dat wordt geretourneerd in de resultatenset | bigint | int |
| schema_name | schema_name_s | De schema context waarin de actie is uitgevoerd. NULL voor controles buiten een schema | sysname | tekenreeks |
| N.v.t. | securable_class_type_s | Beveilig bare object dat is gekoppeld aan de class_type wordt gecontroleerd | N.v.t. | tekenreeks |
| sequence_group_id | sequence_group_id_g | Unieke id | varbinary | GUID |
| sequence_number | sequence_number_d | Houdt de volg orde bij van records binnen één controle record die te groot is om te passen in de schrijf buffer voor audits | int | int |
| server_instance_name | server_instance_name_s | Naam van het Server exemplaar waarop de audit heeft plaatsgevonden | sysname | tekenreeks |
| server_principal_id | server_principal_id_d | ID van de aanmeldings context waarin de actie wordt uitgevoerd | int | int |
| server_principal_name | server_principal_name_s | Huidige aanmelding | sysname | tekenreeks |
| server_principal_sid | server_principal_sid_s | Huidige aanmeldings-SID | varbinary | tekenreeks |
| session_id | session_id_d | De ID van de sessie waarop de gebeurtenis heeft plaatsgevonden | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Server-Principal voor sessie | sysname | tekenreeks |
| rekeningen | statement_s | T-SQL-instructie die is uitgevoerd (indien van toepassing) | nvarchar (4000) | tekenreeks |
| geslaagd | succeeded_s | Hiermee wordt aangegeven of de actie die de gebeurtenis heeft geactiveerd is geslaagd. Voor andere gebeurtenissen dan aanmelden en batch wordt hiermee alleen gerapporteerd of de machtiging is geslaagd of mislukt, niet voor de bewerking. 1 = geslaagd, 0 = mislukt | bit | tekenreeks |
| target_database_principal_id | target_database_principal_id_d | De databaseprincipal waarmee de bewerking GRANT/DENY/REVOKE wordt uitgevoerd. 0 indien niet van toepassing | int | int |
| target_database_principal_name | target_database_principal_name_s | Doel gebruiker van actie. NULL indien niet van toepassing | tekenreeks | tekenreeks |
| target_server_principal_id | target_server_principal_id_d | Server-Principal waarop de bewerking GRANT/DENY/REVOKE wordt uitgevoerd. Retourneert 0 indien niet van toepassing | int | int |
| target_server_principal_name | target_server_principal_name_s | Aanmeldingen bij doel van actie. NULL indien niet van toepassing | sysname | tekenreeks |
| target_server_principal_sid | target_server_principal_sid_s | SID van de doel aanmelding. NULL indien niet van toepassing | varbinary | tekenreeks |
| transaction_id | transaction_id_d | Alleen SQL Server (vanaf 2016)-0 voor Azure SQL Database | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Door de gebruiker gedefinieerde gebeurtenis-ID door gegeven als argument voor sp_audit_write. NULL voor systeem gebeurtenissen (standaard) en niet-nul voor door de gebruiker gedefinieerde gebeurtenis. Zie [sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) voor meer informatie. | smallint | int |
| user_defined_information | user_defined_information_s | Door de gebruiker gedefinieerde gegevens die als argument aan sp_audit_write worden door gegeven. NULL voor systeem gebeurtenissen (standaard) en niet-nul voor door de gebruiker gedefinieerde gebeurtenis. Zie [sp_audit_write (Transact-SQL)](/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) voor meer informatie. | nvarchar (4000) | tekenreeks |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [controleren van Azure SQL database](auditing-overview.md).