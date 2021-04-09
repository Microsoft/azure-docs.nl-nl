---
title: Replicatie tussen beheerde instanties configureren
titleSuffix: Azure SQL Managed Instance
description: In deze zelfstudie leert u hoe transactionele replicatie configureert tussen een uitgever/distributor van Azure SQL Managed Instance en een abonnee op SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 47ea5ea73c581313f90791ca6d7892ebad3f666b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690682"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>Zelfstudie: Replicatie tussen twee beheerde instanties configureren

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Met transactionele replicatie kunt u gegevens van de ene database repliceren naar een andere database die op ofwel SQL Server of op een [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) wordt gehost. Een SQL Managed Instance kan een uitgever, distributeur of abonnee zijn in de replicatietopologie. Zie [configuraties voor transactionele replicatie](replication-transactional-overview.md#common-configurations) voor de beschikbare configuraties. 

Transactionele replicatie is momenteel beschikbaar als openbare preview-versie voor SQL Managed Instance. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Configureer een beheerd exemplaar als een uitgever en distributor van replicaties.
> - Een beheerd exemplaar configureren als een abonnee van replicaties.

![Repliceren tussen twee beheerde exemplaren](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

Deze zelfstudie is bedoeld voor een ervaren doelgroep en er wordt verondersteld dat de gebruiker bekend is met het implementeren van en het verbinding maken met zowel beheerde exemplaren als SQL Server-VM's in Azure. 


> [!NOTE]
> - In dit artikel wordt het gebruik van [transactionele replicatie](/sql/relational-databases/replication/transactional/transactional-replication) in Azure SQL Managed Instance beschreven. Dit is niet verwant aan [failovergroepen](../database/auto-failover-group-overview.md), een functie van Azure SQL Managed Instance waarmee u volledige, leesbare replica's van afzonderlijke instanties kunt maken. Er zijn zaken waarmee u extra rekening moet houden wanneer u [transactionele replicatie met failovergroepen](replication-transactional-overview.md#with-failover-groups) configureert.



## <a name="requirements"></a>Vereisten

Voor het configureren van een Azure SQL Managed Instance die als een uitgever en/of een distributor moet fungeren, is het volgende vereist:

- De uitgever van het beheerde exemplaar moet zich in hetzelfde virtuele netwerk als de distributor en abonnee bevinden, of er zijn [VPN-gateways](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) tot stand gebracht tussen de virtuele netwerken van alle drie de entiteiten. 
- Connectiviteit maakt gebruik van SQL-verificatie tussen replicatiedeelnemers.
- Een Azure Storage-accountshare voor de replicatiewerkmap.
- Poort 445 (TCP uitgaand) is geopend in de regels van de netwerkbeveiligingsgroep voor de beheerde exemplaren voor toegang tot de Azure-bestandsshare.  Als u fout `failed to connect to azure storage <storage account name> with os error 53` ontvangt, moet u een uitgaande regel toevoegen aan de NSG van het betreffende SQL Managed Instance-subnet.

## <a name="1---create-a-resource-group"></a>1: een resourcegroep maken

Gebruik [Azure Portal](https://portal.azure.com) om een resourcegroep met de naam `SQLMI-Repl` te maken.  

## <a name="2---create-managed-instances"></a>2: beheerde exemplaren maken

Gebruik [Azure Portal](https://portal.azure.com) om twee [SQL Managed Instances te maken](instance-create-quickstart.md) op hetzelfde virtuele netwerk en subnet. Geef de twee beheerde exemplaren de namen:

- `sql-mi-pub` (samen met enkele tekens voor willekeurigheid)
- `sql-mi-sub` (samen met enkele tekens voor willekeurigheid)

U moet ook [een Azure-VM configureren om verbinding te maken](connect-vm-instance-configure.md) met uw beheerde exemplaren. 

## <a name="3---create-an-azure-storage-account"></a>3: een Azure-opslagaccount maken

[Maak een Azure-opslagaccount](../../storage/common/storage-account-create.md#create-a-storage-account) voor de werkmap en maak vervolgens een [bestandsshare](../../storage/files/storage-how-to-create-file-share.md) in het opslagaccount. 

Kopieer het pad naar de bestandsshare in de volgende notatie: `\\storage-account-name.file.core.windows.net\file-share-name`

Voorbeeld: `\\replstorage.file.core.windows.net\replshare`

Kopieer de toegangssleutels voor de opslag in de volgende notatie: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Voorbeeld: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Zie [Toegangssleutels voor een opslagaccount beheren](../../storage/common/storage-account-keys-manage.md) voor meer informatie. 

## <a name="4---create-a-publisher-database"></a>4: een publicatiedatabase maken

Maak verbinding met uw beheerde `sql-mi-pub`-exemplaar met behulp van SQL Server Management Studio en voer de volgende Transact-SQL-code (T-SQL) uit om uw publicatiedatabase te maken:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
  ID INT NOT NULL PRIMARY KEY,
  c1 VARCHAR(100) NOT NULL,
  dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5: een abonneedatabase maken

Maak verbinding met uw beheerde `sql-mi-sub`-exemplaar met behulp van SQL Server Management Studio en voer de volgende T-SQL-code uit om uw lege abonneedatabase te maken:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
  ID INT NOT NULL PRIMARY KEY,
  c1 VARCHAR(100) NOT NULL,
  dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6: distributie configureren

Maak verbinding met uw beheerde `sql-mi-pub`-exemplaar met behulp van SQL Server Management Studio en voer de volgende T-SQL-code uit om uw distributiedatabase te configureren:

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7: uitgever configureren voor om een distributor te gebruiken

Wijzig in de `sql-mi-pub` van SQL Managed Instance de uitvoering van de query in de [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)-modus en voer de volgende code uit om de nieuwe distributor bij uw uitgever te registreren.

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Gebruik alleen backslashes (`\`) gebruikt voor de parameter file_storage. Het gebruik van een slash (`/`) kan een fout veroorzaken wanneer er verbinding wordt gemaakt met de bestandsshare.

Met dit script wordt een lokale uitgever op het beheerde exemplaar geconfigureerd, wordt een gekoppelde server toegevoegd en wordt een reeks taken voor de SQL Server-agent gemaakt.

## <a name="8---create-publication-and-subscriber"></a>8: publicatie en abonnee maken

Voer in de [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor)-modus het volgende T-SQL-script uit om replicatie voor uw database in te schakelen en configureer replicatie tussen uw uitgever, distributor en abonnee.

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reader agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9: agentparameters wijzigen

Azure SQL Managed Instance heeft op dit moment back-endproblemen met de connectiviteit met de replicatieagents. Hoewel dit probleem wordt aangepakt, kan het probleem worden omzeild door de time-outwaarde voor de aanmelding voor de replicatieagents te verhogen.

Voer de volgende T-SQL-opdracht op de uitgever uit om de time-out voor de aanmelding te verhogen:

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Voer de volgende T-SQL-opdracht opnieuw uit om de time-out voor de aanmelding weer in te stellen op de standaardwaarde. Dit doet u als volgt:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Start alle drie de agents opnieuw om deze wijzigingen toe te passen.

## <a name="10---test-replication"></a>10: replicatie testen

Wanneer de replicatie eenmaal is geconfigureerd, kunt u deze testen door nieuwe items in te voegen in de instantie voor de uitgever en te bekijken hoe de wijzigingen worden doorgegeven aan de abonnee.

Voer het volgende T-SQL-codefragment uit om de rijen in de instantie voor de abonnee weer te geven:

```sql
select * from dbo.ReplTest
```

Voer het volgende T-SQL-codefragment uit om extra rijen in te voegen in de instantie voor de uitgever en bekijk vervolgens de rijen weer in de instantie voor de abonnee.

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de publicatie wilt verwijderen, voert u de volgende T-SQL-opdracht uit:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Als u de replicatieoptie uit de database wilt verwijderen, voert u de volgende T-SQL-opdracht uit:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Als u het publiceren en distribueren wilt uitschakelen, voert u de volgende T-SQL-opdracht uit:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

U kunt uw Azure-resources opschonen door [de SQL Managed Instance-resources uit de resourcegroep te verwijderen](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) en vervolgens resourcegroep `SQLMI-Repl` te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie vinden over [transactionele replicatie met Azure SQL Managed Instance](replication-transactional-overview.md) of leren hoe u replicatie tussen een [uitgever/distributor van SQL Managed Instance/Distributor en een SQL op een Azure VM-abonnee](replication-two-instances-and-sql-server-configure-tutorial.md) kunt configureren.