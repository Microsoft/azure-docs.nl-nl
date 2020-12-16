---
title: Functies en mogelijkheden van Azure Arc enabled SQL Managed instance
description: Functies en mogelijkheden van Azure Arc enabled SQL Managed instance
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c42acb69e13cc1eb0fbba3fcafaec1451bc4d77
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589216"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>Functies en mogelijkheden van Azure Arc enabled SQL Managed instance

Azure Arc enabled SQL Managed instance deelt een algemene code basis met de nieuwste stabiele versie van SQL Server. De meeste standaard SQL-taal, query verwerking en database beheer functies zijn identiek. De functies die gemeen schappelijk zijn tussen SQL Server en SQL Database of SQL Managed instance zijn:

- Taal functies: [controle van sleutel woorden](/sql/t-sql/language-elements/control-of-flow), [cursors](/sql/t-sql/language-elements/cursors-transact-sql), [gegevens typen](/sql/t-sql/data-types/data-types-transact-sql), [DML-instructies](/sql/t-sql/queries/queries), [predikaten](/sql/t-sql/queries/predicates), [Volg nummers](/sql/relational-databases/sequence-numbers/sequence-numbers), [opgeslagen procedures](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)en [variabelen](/sql/t-sql/language-elements/variables-transact-sql).
- Database functies: [automatisch afstemmen (afdwingen van plan)](/sql/relational-databases/automatic-tuning/automatic-tuning), [bijhouden van wijzigingen](/sql/relational-databases/track-changes/about-change-tracking-sql-server), [database sortering](/sql/relational-databases/collations/set-or-change-the-database-collation), [Inge sloten data bases](/sql/relational-databases/databases/contained-databases), [opgenomen gebruikers](/sql/relational-databases/security/contained-database-users-making-your-database-portable), [gegevens compressie](/sql/relational-databases/data-compression/data-compression), [database configuratie-instellingen](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), online- [index bewerkingen](/sql/relational-databases/indexes/perform-index-operations-online), [partitionering](/sql/relational-databases/partitions/partitioned-tables-and-indexes)en [tijdelijke tabellen](/sql/relational-databases/tables/temporal-tables) ([Zie aan de slag-hand leiding](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables)).
- Beveiligings functies: [toepassings rollen](/sql/relational-databases/security/authentication-access/application-roles), [dynamische gegevens maskering](/sql/relational-databases/security/dynamic-data-masking) ([aan de slag met SQL database dynamische gegevens maskering met de Azure Portal](../../azure-sql/database/dynamic-data-masking-configure-portal.md)), [beveiliging op rijniveau](/sql/relational-databases/security/row-level-security)
- Mogelijkheden voor meerdere modellen: [grafiek verwerking](/sql/relational-databases/graphs/sql-graph-overview), [JSON-gegevens](/sql/relational-databases/json/json-data-sql-server), [OpenXML](/sql/t-sql/functions/openxml-transact-sql), [ruimtelijke](/sql/relational-databases/spatial/spatial-data-sql-server), [openjson](/sql/t-sql/functions/openjson-transact-sql)en [XML-indexen](/sql/t-sql/statements/create-xml-index-transact-sql).



[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="features-of-azure-arc-enabled-sql-managed-instance"></a>Functies van Azure Arc enabled SQL Managed instance

###  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> Hoge Beschik baarheid RDBMS  
  
|Functie|SQL Managed Instance met Azure Arc|
|-------------|----------------|
|Back-upfunctie voor logboekbestanden|Ja| 
|Back-upcompressie|Ja|
|Database momentopname|Ja|
|Altijd op failover-cluster exemplaar<sup>1</sup>| Niet van toepassing. Vergelijk bare mogelijkheden beschikbaar |
|AlwaysOn-beschikbaarheids groepen<sup>2</sup>|De mogelijkheden van HA worden gepland.|
|Basic-beschikbaarheids groepen <sup>2</sup>|De mogelijkheden van HA worden gepland.|
|Minimale beschikbaarheids groep voor replica doorvoer <sup>2</sup>|De mogelijkheden van HA worden gepland.|
|Clusterloze beschikbaarheidsgroep|Ja|
|Online pagina en bestanden herstellen|Ja|
|Online indexeren|Ja|
|Hervat bare online-index opnieuw opbouwen|Ja|
|Online schema wijziging|Ja|
|Snel herstel|Ja|
|Gespiegelde back-ups|Ja|
|Dynamisch toevoegen van geheugen en CPU|Ja|
|Versleutelde back-up|Ja|
|Hybride back-up naar Azure (back-up naar URL)|Ja|

<sup>1</sup> in het scenario waarin een pod-fout optreedt, wordt een nieuw SQL Managed instance gestart en opnieuw gekoppeld aan het permanente volume met uw gegevens. Meer [informatie over Kubernetes permanente volumes vindt u hier](https://kubernetes.io/docs/concepts/storage/persistent-volumes).

<sup>2</sup> toekomstige releases bieden AG-mogelijkheden 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> Schaal baarheid en prestaties van RDBMS  

| Functie | SQL Managed Instance met Azure Arc |
|--|--|
| Column | Ja |
| Binaire bestanden voor grote objecten in geclusterde column Store-indexen | Ja |
| Online niet-geclusterde column store-index opnieuw samen stellen | Ja |
| In-Memory OLTP | Ja |
| Permanent hoofd geheugen | Ja |
| Partitioneren van tabellen en indexen | Ja |
| Gegevenscompressie | Ja |
| Resource Governor | Ja |
| Gepartitioneerde tabel parallellisme | Ja |
| De toewijzing van geheugen en buffer matrix voor NUMA-bewuste en grote pagina's | Ja |
| IO-resource beheer | Ja |
| Uitgestelde duurzaamheid | Ja |
| Automatisch afstemmen | Ja |
| Adaptieve samen voegingen in batch modus | Ja |
| Feedback over geheugen toekenning in batch modus | Ja |
| Interleaved uitvoering voor tabel gewaardeerde functies met meerdere instructies | Ja |
| Verbeteringen voor bulksgewijs invoegen | Ja |

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> RDBMS beveiliging

| Functie | SQL Managed Instance met Azure Arc |
|--|--|
| Beveiliging op rijniveau | Ja |
| Always Encrypted | Ja |
| Always Encrypted met beveiligde enclaves | Nee |
| Dynamische gegevensmaskering | Ja |
| Basis controle | Ja |
| Nauw keurige controle | Ja |
| Transparante database versleuteling | Ja |
| Door de gebruiker gedefinieerde rollen | Ja |
| Ingesloten databases | Ja |
| Versleuteling voor back-ups | Ja |

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> RDBMS-beheer baarheid  

| Functie | SQL Managed Instance met Azure Arc |
|--|--|
| Exclusieve beheerders verbinding | Ja |
| Ondersteuning voor Power shell-scripts | Ja |
| Ondersteuning voor Operations-tier toepassings onderdeel bewerkingen-uitpakken, implementeren, upgraden, verwijderen | Ja |
| Beleids automatisering (controleren op schema en wijziging) | Ja |
| Prestatie gegevens verzamelaar | Ja |
| Rapporten met standaard prestaties | Ja |
| Plan gidsen plannen en bevriezen plannen voor plan richtlijnen | Ja |
| Directe query van geïndexeerde weer gaven (met de hint voor het uitvouwen) | Ja |
| Onderhoud van automatische geïndexeerde weer gaven | Ja |
| Gedistribueerde gepartitioneerde weer gaven | Ja |
| Parallelle geïndexeerde bewerkingen | Ja |
| Automatisch gebruik van geïndexeerde weer gave door query optimalisatie | Ja |
| Parallelle consistentie controle | Ja |

### <a name="programmability"></a><a name="Programmability"></a> Programmeer baarheid  

| Functie | SQL Managed Instance met Azure Arc |
|--|--|
| JSON | Ja |
| Query Store | Ja |  |
| Tijdelijke | Ja |  |
| Systeem eigen XML-ondersteuning | Ja |  |
| XML-indexering | Ja |  |
| & UPSERT-mogelijkheden samen VOEGen | Ja |  |
| Gegevens typen voor datum en tijd | Ja |  |
| Ondersteuning voor meerdere talen | Ja |  |
| Volledige tekst en semantische zoek opdracht | Nee |
| Specificatie van taal in query | Ja |  |
| Service Broker (berichten) | Ja |  |
| Transact-SQL-eind punten | Ja |  |
| Graph | Ja |  |
| Machine Learning Services | Nee |  |
| PolyBase | Nee |


### <a name="tools"></a>Hulpprogramma's

Azure Arc enabled SQL Managed instance ondersteunt diverse hulp middelen voor gegevens die u kunnen helpen bij het beheren van uw gegevens.

| **Hulpprogramma** | SQL Managed Instance met Azure Arc|
| --- | --- | --- |
| Azure Portal <sup>1</sup> | Nee |
| Azure CLI | Nee |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | Ja |
| Azure PowerShell | Ja |
| [BACPAC-bestand (exporteren)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Ja |
| [BACPAC-bestand (importeren)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Ja |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | Ja |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | Ja |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | Ja |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | Ja |

<sup>1</sup> de Azure portal wordt alleen gebruikt voor het weer geven van door Azure Arc INGESCHAKELDe SQL-instanties in de modus alleen-lezen tijdens de preview-versie.


### <a name="unsupported-features--services"></a><a name="Unsupported"></a> Niet-ondersteunde functies & Services

De volgende functies en services zijn niet beschikbaar voor Azure Arc enabled SQL Managed instance. De ondersteuning van deze functies wordt steeds meer in de loop van de tijd ingeschakeld.

| Gebied | Niet-ondersteunde functie of service |
|-----|-----|
| **Database-engine** | Samenvoeg replicatie |
| &nbsp; | Stretch-data base |
| &nbsp; | Gedistribueerde query met verbindingen van derden |
| &nbsp; | Gekoppelde servers met andere gegevens bronnen dan SQL Server en Azure SQL-producten |
| &nbsp; | Door systeem uitgebreide opgeslagen procedures (XP_CMDSHELL, enzovoort) |
| &nbsp; | Bestands tabel, FILESTREAM |
| &nbsp; | CLR-assembly's met de EXTERNAL_ACCESS of onveilige machtigingenset |
| &nbsp; | Extensie van buffer groep |
| **SQL Server Agent** |  Subsystemen: CmdExec, Power shell, Queue Reader, SSIS, SSAS, SSRS |
| &nbsp; | Waarschuwingen |
| &nbsp; | Beheerde back-up |
| **Hoge beschikbaarheid** | Databasespiegeling  |
| **Beveiliging** | Uitbreidbaar sleutel beheer |
| &nbsp; | AD-verificatie voor gekoppelde servers | 
| &nbsp; | AD-verificatie voor beschikbaarheids groepen (AGs) | 