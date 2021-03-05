---
title: Data bases migreren naar een door SQL beheerd exemplaar met de replay-service voor logboek registratie
description: Meer informatie over het migreren van data bases van SQL Server naar SQL Managed instance met behulp van de replay-service voor logboek registratie
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 0bc00aea67fa2f71599ee62e657e1ca1b0627681
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199846"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Data bases migreren van SQL Server naar een door SQL beheerd exemplaar met behulp van de replay-service voor logboeken (preview)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel wordt uitgelegd hoe u de database migratie van SQL Server 2008-2019 naar Azure SQL Managed instance hand matig kunt configureren met behulp van LRS (log replay service), die momenteel beschikbaar is als open bare preview. LRS is een Cloud service die is ingeschakeld voor SQL Managed instance en is gebaseerd op SQL Server technologie voor het verzenden van Logboeken. 

[Azure database Migration service](/azure/dms/tutorial-sql-server-to-managed-instance) en LRS gebruiken dezelfde onderliggende migratie technologie en dezelfde api's. Door LRS vrij te maken, kunnen we complexe aangepaste migraties en hybride architectuur maken tussen on-premises SQL Server en SQL Managed instance.

## <a name="when-to-use-log-replay-service"></a>Wanneer moet u de replay-service voor logboek registratie gebruiken?

Wanneer u Azure Database Migration Service niet kunt gebruiken voor migratie, kunt u LRS rechtstreeks gebruiken met Power shell, Azure CLI-cmdlets of Api's om database migraties hand matig te bouwen en te organiseren in een door SQL beheerd exemplaar. 

U kunt overwegen om LRS in de volgende gevallen te gebruiken:
- U hebt meer controle over uw database migratie project nodig.
- Er is weinig tolerantie voor de uitval tijd voor de migratie cutover.
- Het uitvoer bare bestand van Database Migration Service kan niet worden geïnstalleerd in uw omgeving.
- Het uitvoer bare bestand van Database Migration Service heeft geen bestands toegang tot back-ups van data bases.
- Er is geen toegang tot het hostbesturingssysteem beschikbaar of er zijn geen beheerders bevoegdheden.
- U kunt geen netwerk poorten openen vanuit uw omgeving naar Azure.
- Back-ups worden direct opgeslagen in Azure Blob Storage via de `TO URL` optie.
- U moet differentiële back-ups gebruiken.

> [!NOTE]
> Het is raadzaam om de migratie van data bases van SQL Server naar SQL Managed instance te automatiseren met behulp van Database Migration Service. Deze service maakt gebruik van dezelfde LRS-Cloud service aan de back-end, met logboek verzending in de `NORECOVERY` modus. Overweeg hand matig met LRS om migraties te organiseren wanneer Database Migration Service uw scenario's niet volledig ondersteunt.

## <a name="how-it-works"></a>Uitleg

Het bouwen van een aangepaste oplossing met behulp van LRS voor het migreren van data bases naar de Cloud vereist verschillende indelings stappen, zoals wordt weer gegeven in het diagram en de tabel verderop in deze sectie.

De migratie bestaat uit het maken van volledige database back-ups op SQL Server met `CHECKSUM` ingeschakelde en het kopiëren van back-upbestanden naar Azure Blob Storage. LRS wordt gebruikt om back-upbestanden van Blob Storage naar een door SQL beheerd exemplaar te herstellen. Blob Storage is de tussenliggende opslag tussen SQL Server en SQL Managed instance.

LRS bewaakt Blob Storage voor nieuwe differentiële of logboek back-ups die zijn toegevoegd nadat de volledige back-up is hersteld. LRS herstelt deze nieuwe bestanden vervolgens automatisch. U kunt de service gebruiken om de voortgang te bewaken van back-upbestanden die worden hersteld op een SQL Managed instance, en u kunt het proces zo nodig stoppen.

LRS vereist geen specifieke naamgevings Conventie voor back-upbestanden. Hiermee worden alle bestanden gescand die op Blob Storage worden geplaatst en wordt de back-upketen alleen de bestands koppen gelezen. Data bases bevinden zich in de status ' herstellen ' tijdens het migratie proces. Data bases worden hersteld in de modus [norecovery](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) , zodat ze niet kunnen worden gebruikt voor lezen of schrijven totdat het migratie proces is voltooid. 

Als u verschillende data bases migreert, moet u het volgende doen:
 
- Plaats back-ups voor elke data base in een afzonderlijke map op Blob Storage.
- LRS voor elke Data Base afzonderlijk starten.
- Geef verschillende paden op om Blob Storage mappen te scheiden. 

U kunt LRS starten in de modus *automatisch aanvullen* of *doorlopend* . Wanneer u het in de modus automatisch aanvullen start, wordt de migratie automatisch voltooid wanneer de laatste van de opgegeven back-upbestanden zijn hersteld. Wanneer u LRS in de continue modus start, worden nieuwe back-upbestanden die worden toegevoegd, voortdurend teruggezet en wordt de migratie alleen op de hand matige cutover voltooid. 

We raden u aan hand matig over te nemen nadat de laatste back-up op basis van het logboek is gemaakt en wordt weer gegeven als hersteld op een SQL-beheerd exemplaar. Met de laatste cutover stap wordt de data base online gezet en beschikbaar voor het gebruik van lees-en schrijf bewerkingen op een SQL-beheerd exemplaar.

Nadat LRS is gestopt, kunt u automatisch aanvullen of hand matig volt ooien via cutover, het herstel proces niet hervatten voor een Data Base die online is gebracht op een SQL Managed instance. Als u aanvullende back-upbestanden wilt herstellen nadat de migratie is voltooid via automatisch aanvullen of cutover, moet u de data base verwijderen. U moet ook de volledige back-upketen herstellen door LRS opnieuw te starten.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Diagram met uitleg over de Orchestrator-service voor het opnieuw afspelen van Logboeken voor SQL Managed instance." border="false":::
    
| Bewerking | Details |
| :----------------------------- | :------------------------- |
| **1. Kopieer back-ups van de data base van SQL Server naar Blob Storage**. | Kopieer volledige, differentiële en logboek back-ups van SQL Server naar een Blob Storage-container met behulp van [Azcopy](/azure/storage/common/storage-use-azcopy-v10) of [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br /><br />Gebruik bestands namen. LRS vereist geen specifieke Conventie voor bestands namen.<br /><br />Bij het migreren van verschillende data bases hebt u een afzonderlijke map voor elke data base nodig. |
| **2. Start LRS in de Cloud**. | U kunt de service opnieuw starten met een keuze uit de volgende cmdlets: Power shell ([Start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) of Azure CLI ([az_sql_midb_log_replay_start-cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)). <br /><br /> Start LRS afzonderlijk voor elke Data Base die naar een map met back-ups op Blob Storage wijst. <br /><br /> Nadat u de service hebt gestart, worden er back-ups gemaakt van de Blob Storage-container en worden ze teruggezet op een SQL-beheerd exemplaar.<br /><br /> Als u de LRS in de continue modus hebt gestart, worden alle nieuwe bestanden die naar de map worden geüpload, door de service gecontroleerd. De service past voortdurend Logboeken toe op basis van de LSN-keten (log Sequence Number) totdat deze wordt gestopt. |
| **2,1. Controleer de voortgang van de bewerking**. | U kunt de voortgang van de herstel bewerking bewaken met een keuze uit cmdlets: Power shell ([Get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) of Azure CLI ([az_sql_midb_log_replay_show-cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2,2. Stop de bewerking indien nodig**. | Als u het migratie proces wilt stoppen, kunt u kiezen uit de volgende cmdlets: Power shell ([Stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) of Azure CLI ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> Als u de bewerking stopt, wordt de data base die u herstelt, verwijderd uit het SQL-beheerde exemplaar. Nadat u een bewerking hebt gestopt, kunt u LRS voor een Data Base niet meer hervatten. U moet het migratie proces helemaal opnieuw starten. |
| **3. Als u klaar bent, gaat u naar de Cloud**. | Stop de toepassing en de werk belasting. Haal de laatste back-up voor het einde van het logboek op en upload deze naar Azure Blob Storage.<br /><br /> Voltooi de cutover door een LRS-bewerking te initiëren `complete` met een keuze uit cmdlets: Power shell ([complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) of Azure cli [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Met deze bewerking wordt LRS gestopt en wordt de data base online gezet voor lees-en schrijf bewerkingen op een SQL-beheerd exemplaar.<br /><br /> De toepassing connection string opnieuw laten verwijzen van SQL Server naar een door SQL beheerd exemplaar. |

## <a name="requirements-for-getting-started"></a>Vereisten voor aan de slag

### <a name="sql-server-side"></a>SQL Server zijde
- SQL Server 2008-2019
- Volledige back-up van data bases (een of meer bestanden)
- Differentiële back-up (een of meer bestanden)
- Logboek back-up (niet gesplitst voor een transactie logboek bestand)
- `CHECKSUM` ingeschakeld voor back-ups (verplicht)

### <a name="azure-side"></a>Azure-zijde
- Power shell AZ. SQL module versie 2.16.0 of hoger ([geïnstalleerd](https://www.powershellgallery.com/packages/Az.Sql/) of geopend via [Azure Cloud shell](/azure/cloud-shell/))
- Azure CLI-versie 2.19.0 of hoger ([geïnstalleerd](/cli/azure/install-azure-cli))
- Ingerichte Azure Blob Storage-container
- Beveiligings token voor Shared Access Signature (SAS) met lees-en lijst machtigingen die zijn gegenereerd voor de Blob Storage container

### <a name="migration-of-multiple-databases"></a>Migratie van meerdere data bases
U moet back-upbestanden voor verschillende data bases in afzonderlijke mappen op Blob Storage plaatsen.

Start LRS afzonderlijk voor elke Data Base door naar een geschikte map op Blob Storage te wijzen. LRS kan Maxi maal 100 gelijktijdige herstel processen per één beheerd exemplaar ondersteunen.

### <a name="azure-rbac-permissions"></a>Azure RBAC-machtigingen
Voor het uitvoeren van LRS via de gegeven clients is een van de volgende Azure-rollen vereist:
- Rol abonnements eigenaar
- Rol [Inzender beheerde instantie](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)
- Aangepaste rol met de volgende machtiging: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Aanbevolen procedures

We raden u aan de volgende aanbevolen procedures uit te voeren:
- Voer [Data Migration Assistant](/sql/dma/dma-overview) uit om te controleren of uw data bases klaar zijn om te worden gemigreerd naar een SQL-beheerd exemplaar. 
- U kunt volledige en differentiële back-ups in meerdere bestanden splitsen, in plaats van één bestand te gebruiken.
- Back-upcompressie inschakelen.
- Gebruik Cloud Shell om scripts uit te voeren, omdat deze altijd worden bijgewerkt naar de nieuwste cmdlets die zijn uitgebracht.
- Plan de migratie binnen 47 uur uit te voeren nadat u LRS hebt gestart. Dit is een respijt periode die voor komt dat door de systeem beheerde software patches worden geïnstalleerd.

> [!IMPORTANT]
> - U kunt de data base die wordt hersteld via LRS pas gebruiken als het migratie proces is voltooid. 
> - LRS biedt geen ondersteuning voor alleen-lezen toegang tot data bases tijdens de migratie.
> - Nadat de migratie is voltooid, wordt het migratie proces voltooid, omdat LRS het herstel proces niet kan hervatten.

## <a name="steps-to-execute"></a>Stappen om uit te voeren

### <a name="make-backups-of-sql-server"></a>Back-ups maken van SQL Server

U kunt back-ups maken van SQL Server met behulp van een van de volgende opties:

- Maak een back-up naar de lokale schijf opslag en upload bestanden naar Azure Blob Storage als uw omgeving directe back-ups naar Blob Storage beperkt.
- Maak rechtstreeks een back-up van Blob Storage met de `TO URL` optie in T-SQL, als uw omgeving en beveiligings procedures dit toestaan. 

Stel de data bases in die u wilt migreren naar de volledige herstel modus om logboek back-ups toe te staan.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Als u hand matig volledige, differentiële en logboek back-ups van uw Data Base op lokale opslag wilt maken, gebruikt u de volgende voor beelden van T-SQL-scripts. Zorg ervoor dat de `CHECKSUM` optie is ingeschakeld, omdat deze verplicht is voor LRS.

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

Azure Blob Storage wordt gebruikt als de tussenliggende opslag voor back-upbestanden tussen SQL Server en SQL Managed instance. Voer de volgende stappen uit om een nieuw opslag account en een BLOB-container in het opslag account te maken:

1. [Een opslagaccount maken](../../storage/common/storage-account-create.md?tabs=azure-portal).
2. [Crete een BLOB-container](../../storage/blobs/storage-quickstart-blobs-portal.md) in het opslag account.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Back-ups kopiëren van SQL Server naar Blob Storage

Bij het migreren van data bases naar een beheerd exemplaar met behulp van LRS, kunt u de volgende benaderingen gebruiken om back-ups te uploaden naar Blob Storage:
- De functionaliteit van SQL Server native [back-up naar URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) gebruiken
- Back-ups naar een BLOB-container uploaden met behulp van [Azcopy](/azure/storage/common/storage-use-azcopy-v10) of [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer)
- Storage Explorer gebruiken in de Azure Portal

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Back-ups rechtstreeks van SQL Server naar Blob Storage maken
Als uw bedrijfs-en netwerk beleid dit toestaat, is het een alternatief om back-ups rechtstreeks van SQL Server naar Blob Storage te maken met behulp van de optie SQL Server systeem eigen [back-up naar URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . Als u deze optie kunt uitvoeren, hoeft u geen back-ups te maken op de lokale opslag en deze te uploaden naar Blob Storage.

Als eerste stap moet u voor deze bewerking een SAS-verificatie token genereren voor Blob Storage en vervolgens het token importeren in SQL Server. De tweede stap is het maken van back-ups met de `TO URL` optie in T-SQL. Zorg ervoor dat alle back-ups zijn gemaakt met de `CHEKSUM` optie ingeschakeld.

Ter referentie: de volgende voorbeeld code maakt back-ups Blob Storage. Dit voor beeld bevat geen instructies voor het importeren van het SAS-token. U vindt gedetailleerde instructies, zoals hoe u het SAS-token kunt genereren en importeren in SQL Server, in de zelf studie [Azure Blob Storage gebruiken met SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

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

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Een Blob Storage SAS-verificatie token genereren voor LRS

Azure Blob Storage wordt gebruikt als de tussenliggende opslag voor back-upbestanden tussen SQL Server en SQL Managed instance. U moet een SAS-verificatie token genereren, met alleen lijst-en lees machtigingen voor LRS. Met het token kan LRS toegang krijgen tot Blob Storage en de back-upbestanden gebruiken om ze te herstellen op een SQL-beheerd exemplaar. 

Volg deze stappen om het token te genereren:

1. Open Storage Explorer vanuit de Azure Portal.
2. Vouw **BLOB-containers** uit.
3. Klik met de rechter muisknop op de BLOB-container en selecteer **ophalen Shared Access Signature**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Scherm opname van selecties voor het genereren van een s-verificatie token van S.":::

4. Selecteer het tijds bestek voor de verval datum van het token. Zorg ervoor dat het token geldig is voor de duur van de migratie.
5. Selecteer de tijd zone voor het token: UTC of uw lokale tijd.
    
   > [!IMPORTANT]
   > De tijd zone van het token en uw beheerde exemplaar komen mogelijk niet overeen. Zorg ervoor dat de SAS-token de juiste geldigheids duur heeft, waarbij tijd zones in overweging worden genomen. Stel, indien mogelijk, de tijd zone in op een eerdere en latere tijd van uw geplande migratie venster.
6. Selecteer alleen **Lees** -en **lijst** machtigingen.

   > [!IMPORTANT]
   > Selecteer geen andere machtigingen. Als u dit wel doet, wordt LRS niet gestart. Deze beveiligings vereiste is standaard.
7. Selecteer **Maken**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Scherm afbeelding met selecties voor S A S-token verloop, een tijd zone en machtigingen, samen met de knop maken.":::

De SAS-verificatie wordt gegenereerd met de geldigheid van de tijd die u hebt opgegeven. U hebt de URI-versie van het token nodig, zoals wordt weer gegeven in de volgende scherm afbeelding.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Scherm afbeelding met een voor beeld van de U R I-versie van een s-token van S.":::

### <a name="copy-parameters-from-the-sas-token"></a>Para meters kopiëren uit het SAS-token

Voordat u het SAS-token gebruikt om LRS te starten, moet u de structuur ervan begrijpen. De URI van de gegenereerde SAS-token bestaat uit twee delen, gescheiden door een vraag teken ( `?` ), zoals wordt weer gegeven in dit voor beeld:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Voor beeld U R I voor een S-token gegenereerd S voor de replay-service voor logboek registratie." border="false":::

Het eerste deel, beginnend met `https://` totdat het vraag teken ( `?` ) wordt gebruikt voor de `StorageContainerURI` para meter die wordt ingevoerd als in de invoer voor LRS. Het biedt LRS informatie over de map waarin de database back-upbestanden worden opgeslagen.

Het tweede deel, vanaf het vraag teken ( `?` ) en de andere manier tot het einde van de teken reeks, is de `StorageContainerSasToken` para meter. Dit is het werkelijke ondertekende verificatie token, dat geldig is voor de duur van de opgegeven tijd. Dit onderdeel hoeft niet noodzakelijkerwijs te beginnen `sp=` , zoals wordt weer gegeven in het voor beeld. Uw aanvraag kan anders zijn.

Kopieer de para meters als volgt:

1. Kopieer het eerste deel van het token, beginnend bij de `https://` weg tot het vraag teken ( `?` ). Gebruik deze als de `StorageContainerUri` para meter in Power shell of de Azure CLI voor het starten van LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Scherm opname van het kopiëren van het eerste deel van het token.":::

2. Kopieer het tweede gedeelte van het token, beginnend bij het vraag teken ( `?` ), helemaal tot het einde van de teken reeks. Gebruik deze als de `StorageContainerSasToken` para meter in Power shell of de Azure CLI voor het starten van LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Scherm opname van het kopiëren van het tweede gedeelte van het token.":::
   
> [!NOTE]
> Neem het vraag teken niet op wanneer u een deel van het token kopieert.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Meld u aan bij Azure en selecteer een abonnement

Gebruik de volgende Power shell-cmdlet om u aan te melden bij Azure:

```powershell
Login-AzAccount
```

Selecteer het juiste abonnement waarin uw beheerde exemplaar zich bevindt met behulp van de volgende Power shell-cmdlet:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>De migratie starten

U start de migratie door LRS te starten. U kunt de service starten in de modus automatisch aanvullen of doorlopend. 

Wanneer u de modus AutoAanvullen gebruikt, wordt de migratie automatisch voltooid wanneer de laatste van de opgegeven back-upbestanden zijn hersteld. Deze optie vereist dat de start opdracht de bestands naam van het laatste back-upbestand opgeeft. 

Wanneer u de doorlopende modus gebruikt, herstelt de service voortdurend nieuwe back-upbestanden die zijn toegevoegd. De migratie wordt alleen op de hand matige cutover voltooid. 

### <a name="start-lrs-in-autocomplete-mode"></a>LRS starten in de modus automatisch aanvullen

Als u LRS in de modus automatisch aanvullen wilt starten, gebruikt u de volgende Power shell-of Azure CLI-opdrachten. Geef de naam van het laatste back-upbestand op met behulp van de `-LastBackupName` para meter. Wanneer de laatste van de opgegeven back-upbestanden worden teruggezet, initieert de service automatisch een cutover.

Hier volgt een voor beeld van het starten van LRS in de modus automatisch volt ooien met behulp van Power shell:

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

Hier volgt een voor beeld van het starten van LRS in de modus automatisch volt ooien met behulp van de Azure CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>LRS starten in doorlopende modus

Hier volgt een voor beeld van het starten van LRS in doorlopende modus met behulp van Power shell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Hier volgt een voor beeld van het starten van LRS in de continue modus met behulp van de Azure CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Power shell-en CLI-clients om LRS te starten in de continue modus zijn synchroon. Dit betekent dat clients wachten tot de API-reactie rapporteert over geslaagde of mislukte pogingen om de taak te starten. 

Tijdens deze wacht tijd wordt de opdracht niet teruggestuurd naar de opdracht prompt. Als u de migratie-ervaring bijwerkt en u de LRS-start opdracht nodig hebt om het besturings element direct te laten door gaan met de rest van het script, kunt u Power shell uitvoeren als een achtergrond taak met de `-AsJob` Switch. Bijvoorbeeld:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

Wanneer u een achtergrond taak start, retourneert een taak object onmiddellijk, zelfs als de taak een lange tijd kost om te volt ooien. U kunt zonder onderbreking in de sessie blijven werken terwijl de taak wordt uitgevoerd. Zie de documentatie voor [Power shell-start taken](/powershell/module/microsoft.powershell.core/start-job#description) voor meer informatie over het uitvoeren van Power shell als een achtergrond taak.

Als u een Azure CLI-opdracht op Linux als achtergrond proces wilt starten, gebruikt u het en `&` -teken () aan het einde van de LRS-start opdracht:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> Nadat u LRS hebt gestart, worden alle door het systeem beheerde software patches gedurende 47 uur gestopt. Na dit venster wordt de volgende geautomatiseerde software patch automatisch gestopt LRS. Als dat gebeurt, kunt u de migratie niet hervatten en moet u deze helemaal opnieuw opstarten. 

## <a name="monitor-the-migration-progress"></a>De voortgang van de migratie bewaken

Als u de voortgang van de migratie via Power shell wilt controleren, gebruikt u de volgende opdracht:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Als u de voortgang van de migratie via de Azure CLI wilt bewaken, gebruikt u de volgende opdracht:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>De migratie stoppen

Als u de migratie wilt stoppen, gebruikt u de volgende cmdlets. Als de migratie wordt gestopt, wordt de data base die wordt teruggezet op een SQL-beheerd exemplaar verwijderd, waardoor het hervatten van de migratie niet mogelijk is.

Als u wilt stoppen met het migratie proces via Power shell, gebruikt u de volgende opdracht:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Als u het migratie proces wilt stoppen via de Azure CLI, gebruikt u de volgende opdracht:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>De migratie volt ooien (doorlopende modus)

Als u de LRS in de continue modus hebt gestart, kunt u, nadat u hebt gecontroleerd of alle back-ups zijn hersteld, de migratie volt ooien door de cutover te initiëren. Na de cutover wordt de data base gemigreerd en gereed voor lees-en schrijf toegang.

Gebruik de volgende opdracht om het migratie proces in de doorlopende modus LRS via Power shell te volt ooien:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Gebruik de volgende opdracht om het migratie proces in de doorlopende modus LRS te volt ooien via de Azure CLI:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Functionele beperkingen

De functionele beperkingen van LRS zijn:
- De data base die u wilt herstellen, kan niet worden gebruikt voor alleen-lezen toegang tijdens het migratie proces.
- Door het systeem beheerde software patches worden gedurende 47 uur geblokkeerd nadat LRS is gestart. Nadat dit tijd venster is verlopen, stopt de volgende software-update LRS. U moet LRS vervolgens helemaal opnieuw opstarten.
- LRS vereist dat er een back-up van de data bases op SQL Server worden gemaakt met de `CHECKSUM` optie ingeschakeld.
- Het SAS-token dat LRS gebruikt, moet worden gegenereerd voor de hele Azure Blob Storage-container en mag alleen lees-en lijst machtigingen hebben.
- Back-upbestanden voor verschillende data bases moeten in afzonderlijke mappen op Blob Storage worden geplaatst.
- LRS moet afzonderlijk worden gestart voor elke Data Base die verwijst naar mappen met back-upbestanden op Blob Storage.
- LRS kan Maxi maal 100 gelijktijdige herstel processen per één beheerd exemplaar ondersteunen.

## <a name="troubleshooting"></a>Problemen oplossen

Nadat u LRS hebt gestart, gebruikt u de bewakings-cmdlet ( `get-azsqlinstancedatabaselogreplay` of `az_sql_midb_log_replay_show` ) om de status van de bewerking weer te geven. Als LRS na enige tijd niet kan worden gestart en u een fout melding krijgt, controleert u de meest voorkomende problemen:

- Heeft een bestaande data base in een SQL Managed instance dezelfde naam als het exemplaar dat u probeert te migreren van SQL Server? Los dit conflict op door de naam van een van de data bases te wijzigen.
- Is de data base-back-up op SQL Server gemaakt via de `CHECKSUM` optie?
- Zijn de machtigingen voor de SAS-token alleen lezen en worden ze weer geven voor LRS?
- Hebt u het SAS-token voor LRS gekopieerd na het vraag teken ( `?` ), waarbij inhoud begint als volgt: `sv=2020-02-10...` ? 
- Is de geldigheids duur van het SAS-token van toepassing op het tijd venster voor het starten en volt ooien van de migratie? Er zijn mogelijk niet-overeenkomende problemen als gevolg van de verschillende tijd zones die worden gebruikt voor SQL Managed instance en het SAS-token. Probeer het SAS-token opnieuw te genereren en de geldigheid van het token uit te breiden van het tijd venster vóór en na de huidige datum.
- Zijn de database naam, de naam van de resource groep en de naam van de beheerde instantie correct gespeld?
- Als u LRS in de modus AutoAanvullen hebt gestart, was een geldige bestands naam voor het laatst opgegeven back-upbestand?

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [het migreren van SQL Server naar SQL Managed instance](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Meer informatie over [verschillen tussen SQL Server en SQL Managed instance](transact-sql-tsql-differences-sql-server.md).
- Meer informatie over [Best practices voor werk belastingen die zijn gemigreerd naar Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
