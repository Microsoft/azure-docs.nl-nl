---
title: Databases migreren naar SQL Managed Instance met behulp van de Log Replay-service
description: Meer informatie over het migreren van databases van SQL Server naar SQL Managed Instance met behulp van Log Replay Service
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurecli
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/31/2021
ms.openlocfilehash: 522f4ec2f28f9d8975a8a7a7b10e435f602af855
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484027"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Databases migreren van SQL Server naar SQL Managed Instance met behulp van Log Replay Service (preview)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel wordt uitgelegd hoe u handmatig databasemigratie configureert van SQL Server 2008-2019 naar Azure SQL Managed Instance met behulp van Log Replay Service (LRS), momenteel in openbare preview. LRS is een cloudservice die is ingeschakeld voor SQL Managed Instance en is gebaseerd SQL Server technologie voor logboekverzending. 

[Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) LRS gebruiken dezelfde onderliggende migratietechnologie en dezelfde API's. Door LRS uit te brengen, maken we complexere aangepaste migraties en hybride architectuur mogelijk tussen on-premises SQL Server en SQL Managed Instance.

## <a name="when-to-use-log-replay-service"></a>Wanneer u de Log Replay-service gebruikt

Wanneer u Azure Database Migration Service niet kunt gebruiken voor migratie, kunt u LRS rechtstreeks gebruiken met PowerShell, Azure CLI-cmdlets of API's om databasemigraties handmatig te bouwen en te SQL Managed Instance. 

In de volgende gevallen kunt u LRS gebruiken:
- U hebt meer controle nodig voor uw databasemigratieproject.
- Er is weinig tolerantie voor downtime bij de migratie-cutover.
- Het Database Migration Service uitvoerbare bestand kan niet worden geïnstalleerd in uw omgeving.
- Het Database Migration Service het uitvoerbare bestand heeft geen bestandstoegang tot databaseback-ups.
- Er is geen toegang tot het host-besturingssysteem beschikbaar of er zijn geen beheerdersbevoegdheden.
- U kunt geen netwerkpoorten openen vanuit uw omgeving naar Azure.
- Netwerkbeperking of proxyblokkeringsproblemen in uw omgeving.
- Back-ups worden rechtstreeks opgeslagen Azure Blob Storage via de `TO URL` optie .
- U moet differentiële back-ups gebruiken.

> [!NOTE]
> We raden u aan om de migratie van databases van SQL Server naar SQL Managed Instance te automatiseren met behulp van Database Migration Service. Deze service maakt gebruik van dezelfde LRS-cloudservice aan de back-end, met logboekverzending in de `NORECOVERY` modus. Overweeg handmatig LRS te gebruiken om migraties te Database Migration Service uw scenario's niet volledig ondersteunt.

## <a name="how-it-works"></a>Uitleg

Voor het bouwen van een aangepaste oplossing met behulp van LRS voor het migreren van databases naar de cloud zijn verschillende orchestration-stappen vereist, zoals wordt weergegeven in het diagram en de tabel later in deze sectie.

De migratie bestaat uit het maken van volledige databaseback-ups op SQL Server ingeschakeld en het kopiëren van back-upbestanden `CHECKSUM` naar Azure Blob Storage. LRS wordt gebruikt om back-upbestanden te herstellen van Blob Storage naar SQL Managed Instance. Blob Storage is een intermediaire opslag tussen SQL Server en SQL Managed Instance.

LRS controleert Blob Storage op nieuwe differentiële back-ups of logboekback-ups die worden toegevoegd nadat de volledige back-up is hersteld. LRS herstelt deze nieuwe bestanden vervolgens automatisch. U kunt de service gebruiken om de voortgang te bewaken van back-upbestanden die worden hersteld op SQL Managed Instance en u kunt het proces zo nodig stoppen.

LRS vereist geen specifieke naamconventie voor back-upbestanden. Alle bestanden die op de back-Blob Storage en de back-upketen wordt samengesteld om alleen de bestandskopteksten te lezen. Databases hebben de status Herstellen tijdens het migratieproces. Databases worden hersteld in [de NORECOVERY-modus,](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) zodat ze pas kunnen worden gebruikt voor lezen of schrijven als het migratieproces is voltooid. 

Als u verschillende databases migreert, moet u het volgende doen:
 
- Plaats back-ups voor elke database in een afzonderlijke map op Blob Storage.
- Start LRS afzonderlijk voor elke database.
- Geef verschillende paden op om de Blob Storage te scheiden. 

U kunt LRS starten in de *modus automatisch aanvullen* of *doorlopende* modus. Wanneer u de migratie start in de modus voor automatisch aanvullen, wordt de migratie automatisch voltooien wanneer de laatste van de opgegeven back-upbestanden is hersteld. Wanneer u LRS in de continue modus start, herstelt de service voortdurend nieuwe back-upbestanden die zijn toegevoegd en wordt de migratie alleen voor de handmatige cutover voltooien. 

U wordt aangeraden handmatig over te loggen nadat de laatste back-up van het logboek is gemaakt en wordt weergegeven als hersteld op SQL Managed Instance. De laatste cutover-stap zorgt ervoor dat de database online komt en beschikbaar is voor lees- en schrijfgebruik op SQL Managed Instance.

Nadat LRS is gestopt, automatisch of handmatig via cutover, kunt u het herstelproces niet hervatten voor een database die online is gebracht op SQL Managed Instance. Als u aanvullende back-upbestanden wilt herstellen nadat de migratie is voltooien via automatisch aanvullen of cutover, moet u de database verwijderen. U moet ook de volledige back-upketen opnieuw herstellen door LRS opnieuw op te starten.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Diagram waarin de orchestration-stappen voor log Replay Service voor SQL Managed Instance." border="false":::
    
| Bewerking | Details |
| :----------------------------- | :------------------------- |
| **1. Databaseback-ups kopiëren van SQL Server naar Blob Storage**. | Kopieer volledige, differentiële en logboekback-ups van SQL Server naar Blob Storage container met behulp van [Azcopy](../../storage/common/storage-use-azcopy-v10.md) [of Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br /><br />Gebruik bestandsnamen. LRS vereist geen specifieke bestandsnaamconventie.<br /><br />Bij het migreren van verschillende databases hebt u een afzonderlijke map voor elke database nodig. |
| **2. Start LRS in de cloud**. | U kunt de service opnieuw starten met een keuze uit cmdlets: PowerShell ([start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) of Azure CLI[(az_sql_midb_log_replay_start cmdlets).](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start) <br /><br /> Start LRS afzonderlijk voor elke database die naar een back-upmap op de Blob Storage. <br /><br /> Nadat u de service hebt starten, worden er back-ups gemaakt van de Blob Storage container en worden deze hersteld op SQL Managed Instance.<br /><br /> Als u LRS in de continue modus hebt gestart en alle aanvankelijk geüploade back-ups zijn hersteld, kijkt de service naar nieuwe bestanden die naar de map zijn geüpload. De service zal continu logboeken toepassen op basis van de LSN-keten (Log Sequence Number) totdat deze is gestopt. |
| **2.1. Controleer de voortgang van de bewerking.** | U kunt de voortgang van de herstelbewerking bewaken met een keuze uit cmdlets: PowerShell ([get-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)of Azure CLI[(az_sql_midb_log_replay_show cmdlets).](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show) |
| **2.2. Stop de bewerking indien nodig.** | Als u het migratieproces wilt stoppen, kunt u kiezen uit cmdlets: PowerShell[(stop-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)of Azure CLI[(az_sql_midb_log_replay_stop).](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) <br /><br /> Als u de bewerking stopt, wordt de database verwijderd die u wilt herstellen op SQL Managed Instance. Nadat u een bewerking hebt gestopt, kunt u LRS niet meer hervatten voor een database. U moet het migratieproces opnieuw starten. |
| **3. Ga naar de cloud wanneer u klaar bent.** | Stop de toepassing en de workload. Neem de laatste back-up van het logboek en upload deze naar Azure Blob Storage.<br /><br /> Voltooi de cutover door een LRS-bewerking te starten met een keuze uit `complete` cmdlets: PowerShell ([complete-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)of Azure CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Deze bewerking stopt LRS en zorgt ervoor dat de database online komt voor lees- en schrijfgebruik op SQL Managed Instance.<br /><br /> Wijs de toepassings connection string van SQL Server naar SQL Managed Instance. U moet deze stap zelf ins orchestraeren, hetzij via een handmatige connection string-wijziging in uw toepassing, hetzij automatisch (bijvoorbeeld als uw toepassing bijvoorbeeld de connection string kan lezen vanuit een eigenschap of een database). |

## <a name="requirements-for-getting-started"></a>Vereisten om aan de slag te gaan

### <a name="sql-server-side"></a>SQL Server-zijde
- SQL Server 2008-2019
- Volledige back-up van databases (een of meer bestanden)
- Differentiële back-up (een of meer bestanden)
- Logboekback-up (niet gesplitst voor een transactielogboekbestand)
- `CHECKSUM` ingeschakeld voor back-ups (verplicht)

### <a name="azure-side"></a>Azure-zijde
- PowerShell Az.SQL module versie 2.16.0 of hoger[(](https://www.powershellgallery.com/packages/Az.Sql/) geïnstalleerd of toegankelijk via [Azure Cloud Shell](/azure/cloud-shell/))
- Azure CLI versie 2.19.0 of hoger ([geïnstalleerd](/cli/azure/install-azure-cli))
- Azure Blob Storage container ingericht
- Sas-beveiliging token (Shared Access Signature) met lees- en lijstmachtigingen die zijn gegenereerd voor Blob Storage container

### <a name="migration-of-multiple-databases"></a>Migratie van meerdere databases
U moet back-upbestanden voor verschillende databases in afzonderlijke mappen op Blob Storage.

Start LRS afzonderlijk voor elke database door te wijzen naar een geschikte map op Blob Storage. LRS kan maximaal 100 gelijktijdige herstelprocessen per beheerd exemplaar ondersteunen.

### <a name="azure-rbac-permissions"></a>Azure RBAC-machtigingen
LRS uitvoeren via de opgegeven clients vereist een van de volgende Azure-rollen:
- Rol van abonnementseigenaar
- [Rol Inzender voor beheerd](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) exemplaar
- Aangepaste rol met de volgende machtiging: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Aanbevolen procedures

We raden de volgende best practices aan:
- Voer [Data Migration Assistant](/sql/dma/dma-overview) uit om te controleren of uw databases gereed zijn om te worden gemigreerd naar SQL Managed Instance. 
- Splits volledige en differentiële back-ups in meerdere bestanden, in plaats van één bestand te gebruiken.
- Schakel back-upcompressie in.
- Gebruik Cloud Shell om scripts uit te voeren, omdat deze altijd worden bijgewerkt naar de meest recente cmdlets die zijn uitgebracht.
- Plan de migratie binnen 47 uur na het starten van LRS. Dit is een respijtperiode die de installatie van door het systeem beheerde softwarepatches voorkomt.

> [!IMPORTANT]
> - U kunt de database die wordt hersteld via LRS niet gebruiken totdat het migratieproces is voltooien. 
> - LRS biedt geen ondersteuning voor alleen-lezentoegang tot databases tijdens de migratie.
> - Nadat de migratie is afgerond, wordt het migratieproces afgerond omdat LRS geen ondersteuning biedt voor het hervatting van het herstelproces.

## <a name="steps-to-execute"></a>Uit te voeren stappen

### <a name="make-backups-of-sql-server"></a>Back-ups maken van SQL Server

U kunt back-ups van SQL Server met behulp van een van de volgende opties:

- Back-up naar lokale schijfopslag en upload vervolgens bestanden naar Azure Blob Storage, als uw omgeving directe back-ups beperkt tot Blob Storage.
- Back-up rechtstreeks naar Blob Storage met de optie `TO URL` in T-SQL, als uw omgeving en beveiligingsprocedures dit toestaan. 

Stel databases in die u wilt migreren naar de modus volledig herstel om logboekback-ups toe te staan.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Als u handmatig volledige, differentiële en logboekback-ups van uw database in lokale opslag wilt maken, gebruikt u de volgende T-SQL-voorbeeldscripts. Zorg ervoor `CHECKSUM` dat de optie is ingeschakeld, omdat deze verplicht is voor LRS.

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>Een opslagaccount maken

Azure Blob Storage wordt gebruikt als intermediaire opslag voor back-upbestanden tussen SQL Server en SQL Managed Instance. Volg deze stappen om een nieuw opslagaccount en een blobcontainer in het opslagaccount te maken:

1. [Een opslagaccount maken](../../storage/common/storage-account-create.md?tabs=azure-portal).
2. [Een blobcontainer maken in](../../storage/blobs/storage-quickstart-blobs-portal.md) het opslagaccount.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Back-ups kopiëren van SQL Server naar Blob Storage

Bij het migreren van databases naar een beheerd exemplaar met behulp van LRS kunt u de volgende methoden gebruiken om back-ups te uploaden naar Blob Storage:
- Systeemeigen SQL Server [VAN BACK-UP NAAR URL gebruiken](/sql/relational-databases/backup-restore/sql-server-backup-to-url)
- [Azcopy](../../storage/common/storage-use-azcopy-v10.md) of [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer) back-ups uploaden naar een blobcontainer
- Gebruik Storage Explorer in de Azure Portal

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Back-ups van SQL Server rechtstreeks naar Blob Storage
Als uw bedrijfsbeleid en netwerkbeleid dit toestaan, kunt u ook rechtstreeks back-ups maken van SQL Server naar Blob Storage met behulp van de SQL Server systeemeigen back-up naar [URL.](/sql/relational-databases/backup-restore/sql-server-backup-to-url) Als u deze optie kunt gebruiken, hoeft u geen back-ups te maken op de lokale opslag en deze te uploaden naar Blob Storage.

Als eerste stap moet u voor deze bewerking een SAS-verificatie token genereren voor Blob Storage en het token vervolgens importeren in SQL Server. De tweede stap is het maken van back-ups met `TO URL` de optie in T-SQL. Zorg ervoor dat alle back-ups worden gemaakt met `CHEKSUM` de optie ingeschakeld.

Ter referentie maakt de volgende voorbeeldcode back-ups naar Blob Storage. Dit voorbeeld bevat geen instructies voor het importeren van het SAS-token. Gedetailleerde instructies, waaronder het genereren en importeren van het SAS-token in SQL Server, vindt u in de zelfstudie Use [Azure Blob Storage with SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Een sas Blob Storage-verificatie-token genereren voor LRS

Azure Blob Storage wordt gebruikt als intermediaire opslag voor back-upbestanden tussen SQL Server en SQL Managed Instance. U moet voor LRS een SAS-verificatie token genereren, met alleen de machtigingen list en read. Met het token kan LRS toegang krijgen tot Blob Storage en de back-upbestanden gebruiken om ze te herstellen op SQL Managed Instance. 

Volg deze stappen om het token te genereren:

1. Open Storage Explorer vanuit de Azure Portal.
2. Vouw **BlobContainers uit.**
3. Klik met de rechtermuisknop op de blobcontainer en selecteer **Get Shared Access Signature**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Schermopname met selecties voor het genereren van een S A S-verificatie token.":::

4. Selecteer het tijdsbestek voor het verlopen van het token. Zorg ervoor dat het token geldig is voor de duur van de migratie.
5. Selecteer de tijdzone voor het token: UTC of uw lokale tijd.
    
   > [!IMPORTANT]
   > De tijdzone van het token en uw beheerde exemplaar komen mogelijk niet overeen. Zorg ervoor dat het SAS-token de juiste geldigheidsduur heeft, waarbij u rekening houdt met tijdzones. Stel indien mogelijk de tijdzone in op een eerder en later tijdstip van het geplande migratievenster.
6. Selecteer **alleen** de **machtigingen** Lezen en Lijst.

   > [!IMPORTANT]
   > Selecteer geen andere machtigingen. Als u dit doet, wordt LRS niet start. Deze beveiligingsvereiste is ontworpen.
7. Selecteer **Maken**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Schermopname met selecties voor S A S-tokenverlooptijd, tijdzone en machtigingen, samen met de knop Maken.":::

De SAS-verificatie wordt gegenereerd met de geldigheidsduur die u hebt opgegeven. U hebt de URI-versie van het token nodig, zoals wordt weergegeven in de volgende schermopname.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Schermopname van een voorbeeld van de U R I-versie van een S A S-token.":::

### <a name="copy-parameters-from-the-sas-token"></a>Parameters kopiëren vanuit het SAS-token

Voordat u het SAS-token gebruikt om LRS te starten, moet u de structuur ervan begrijpen. De URI van het gegenereerde SAS-token bestaat uit twee delen gescheiden door een vraagteken ( `?` ), zoals wordt weergegeven in dit voorbeeld:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Voorbeeld-U R I voor een gegenereerd S A S-token voor Log Replay Service." border="false":::

Het eerste deel, beginnend met tot het vraagteken ( ), wordt gebruikt voor de parameter die als `https://` `?` invoer voor `StorageContainerURI` LRS wordt ingevoerd. Het geeft LRS informatie over de map waarin back-upbestanden van de database worden opgeslagen.

Het tweede deel, beginnend na het vraagteken ( ) en helemaal tot het einde van de tekenreeks `?` gaat, is de `StorageContainerSasToken` parameter . Dit is het daadwerkelijk ondertekende verificatie-token, dat geldig is voor de duur van de opgegeven tijd. Dit onderdeel hoeft niet per se te beginnen met `sp=` , zoals wordt weergegeven in het voorbeeld. Uw case kan verschillen.

Kopieer de parameters als volgt:

1. Kopieer het eerste deel van het token, vanaf helemaal tot `https://` aan het vraagteken ( `?` ). Gebruik deze als `StorageContainerUri` parameter in PowerShell of de Azure CLI voor het starten van LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Schermopname van het kopiëren van het eerste deel van het token.":::

2. Kopieer het tweede deel van het token, beginnend vanaf het vraagteken ( ) tot aan `?` het einde van de tekenreeks. Gebruik deze als `StorageContainerSasToken` parameter in PowerShell of de Azure CLI voor het starten van LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Schermopname van het kopiëren van het tweede deel van het token.":::
   
> [!NOTE]
> Neem het vraagteken niet op wanneer u een van beide gedeelten van het token kopieert.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Meld u aan bij Azure en selecteer een abonnement

Gebruik de volgende PowerShell-cmdlet om u aan te melden bij Azure:

```powershell
Login-AzAccount
```

Selecteer het juiste abonnement waarin uw beheerde exemplaar zich bevindt met behulp van de volgende PowerShell-cmdlet:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>De migratie starten

U start de migratie door LRS te starten. U kunt de service starten in de modus automatisch aanvullen of doorlopende modus. 

Wanneer u de modus voor automatisch aanvullen gebruikt, wordt de migratie automatisch voltooien wanneer de laatste van de opgegeven back-upbestanden is hersteld. Voor deze optie is de startopdracht vereist om de bestandsnaam van het laatste back-upbestand op te geven. 

Wanneer u de continue modus gebruikt, herstelt de service voortdurend nieuwe back-upbestanden die zijn toegevoegd. De migratie wordt alleen voor de handmatige cutover voltooien. 

### <a name="start-lrs-in-autocomplete-mode"></a>LRS starten in de modus voor automatisch aanvullen

Als u LRS in de modus voor automatisch aanvullen wilt starten, gebruikt u de volgende PowerShell- of Azure CLI-opdrachten. Geef de naam van het laatste back-upbestand op met behulp van de `-LastBackupName` parameter . Bij het herstellen van de laatste van de opgegeven back-upbestanden, start de service automatisch een cutover.

Hier is een voorbeeld van het starten van LRS in de modus voor automatisch aanvullen met behulp van PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Hier is een voorbeeld van het starten van LRS in de modus voor automatisch aanvullen met behulp van de Azure CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>LRS starten in continue modus

Hier is een voorbeeld van het starten van LRS in de continue modus met behulp van PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Hier is een voorbeeld van het starten van LRS in de continue modus met behulp van de Azure CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

PowerShell- en CLI-clients om LRS in de continue modus te starten, zijn synchroon. Dit betekent dat clients wachten tot de API-reactie rapporteert dat de taak is geslaagd of mislukt. 

Tijdens deze wachttijd wordt het besturingselement niet door de opdrachtprompt terug gegeven. Als u een script maakt voor de migratie-ervaring en u de LRS-startopdracht nodig hebt om het beheer direct terug te geven om door te gaan met de rest van het script, kunt u PowerShell uitvoeren als achtergrondopdracht met de `-AsJob` switch. Bijvoorbeeld:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Wanneer u een achtergrond job start, retourneert een taakobject onmiddellijk, zelfs als het langer duurt om de taak te voltooien. U kunt zonder onderbreking in de sessie blijven werken terwijl de taak wordt uitgevoerd. Zie de PowerShell [Start-Job-documentatie](/powershell/module/microsoft.powershell.core/start-job#description) voor meer informatie over het uitvoeren van PowerShell als achtergrond taak.

Als u een Azure CLI-opdracht in Linux wilt starten als achtergrondproces, gebruikt u ook het en-en-achteren () aan het einde van de `&` LRS-startopdracht:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Nadat u LRS hebt start, worden alle door het systeem beheerde softwarepatches 47 uur gestopt. Na dit venster stopt de volgende geautomatiseerde softwarepatch LRS automatisch. Als dat gebeurt, kunt u de migratie niet hervatten en moet u de migratie opnieuw starten. 

## <a name="monitor-the-migration-progress"></a>De voortgang van de migratie bewaken

Gebruik de volgende opdracht om de voortgang van de migratie te controleren via PowerShell:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Gebruik de volgende opdracht om de voortgang van de migratie via de Azure CLI te bewaken:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>De migratie stoppen

Als u de migratie wilt stoppen, gebruikt u de volgende cmdlets. Als u de migratie stopt, wordt de herstellende database op SQL Managed Instance verwijderd, dus het is niet mogelijk om de migratie te hervat.

Gebruik de volgende opdracht om het migratieproces via PowerShell te stoppen:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Gebruik de volgende opdracht om het migratieproces via de Azure CLI te stoppen:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>De migratie voltooien (continue modus)

Als u LRS in de continue modus hebt gestart en u ervoor hebt gezorgd dat alle back-ups zijn hersteld, wordt de migratie voltooid door de cutover te initiëren. Na de cutover wordt de database gemigreerd en gereed voor lees- en schrijftoegang.

Gebruik de volgende opdracht om het migratieproces in de continue modus van LRS via PowerShell te voltooien:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Gebruik de volgende opdracht om het migratieproces in de continue modus van LRS via de Azure CLI te voltooien:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Functionele beperkingen

Functionele beperkingen van LRS zijn:
- De database die u herstelt, kan niet worden gebruikt voor alleen-lezentoegang tijdens het migratieproces.
- Door het systeem beheerde softwarepatches worden 47 uur na het starten van LRS geblokkeerd. Nadat dit tijdvenster is verlopen, stopt de volgende software-update LRS. Vervolgens moet u LRS opnieuw opstarten.
- LRS vereist dat er back SQL Server databases worden opgeslagen met `CHECKSUM` de optie ingeschakeld.
- Het SAS-token dat door LRS wordt gebruikt, moet worden gegenereerd voor de Azure Blob Storage container en moet alleen lees- en lijstmachtigingen hebben.
- Back-upbestanden voor verschillende databases moeten worden geplaatst in afzonderlijke mappen op Blob Storage.
- LRS moet afzonderlijk worden gestart voor elke database die naar afzonderlijke mappen met back-upbestanden op Blob Storage.
- LRS kan maximaal 100 gelijktijdige herstelprocessen per beheerd exemplaar ondersteunen.

## <a name="troubleshooting"></a>Problemen oplossen

Nadat u LRS hebt start, gebruikt u de bewakings-cmdlet ( `get-azsqlinstancedatabaselogreplay` of ) om de status van de bewerking te `az_sql_midb_log_replay_show` bekijken. Als LRS na enige tijd niet kan worden starten en er een foutmelding wordt weergegeven, controleert u op de meest voorkomende problemen:

- Heeft een bestaande database SQL Managed Instance dezelfde naam als de database die u wilt migreren vanuit SQL Server? Los dit conflict op door de naam van een van de databases te wijzigen.
- Is de back-up van de database SQL Server gemaakt via de `CHECKSUM` optie ?
- Worden de machtigingen voor het SAS-token alleen gelezen en vermeld voor LRS?
- Hebt u het SAS-token voor LRS na het vraagteken ( ) kopiëren, met `?` inhoud die als het volgende begint: `sv=2020-02-10...` ? Â 
- Is de geldigheidsduur van het SAS-token van toepassing op het tijdvenster van het starten en voltooien van de migratie? Er kunnen verschillen zijn vanwege de verschillende tijdzones die worden gebruikt voor SQL Managed Instance en het SAS-token. Probeer het SAS-token opnieuw te maken en de geldigheid van het token voor en na de huidige datum uit te breiden.
- Zijn de databasenaam, de naam van de resourcegroep en de naam van het beheerde exemplaar correct gespeld?
- Als u LRS in de modus voor automatisch aanvullen hebt gestart, is er dan een geldige bestandsnaam opgegeven voor het laatste back-upbestand?

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [het migreren van SQL Server naar SQL Managed Instance](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Meer informatie over [de verschillen SQL Server en SQL Managed Instance](transact-sql-tsql-differences-sql-server.md).
- Meer informatie over [best practices voor kosten en grootte van workloads die naar Azure zijn gemigreerd.](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
