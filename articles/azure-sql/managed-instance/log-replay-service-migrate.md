---
title: Data bases migreren naar een door SQL beheerd exemplaar met de replay-service voor logboek registratie
description: Meer informatie over het migreren van data bases van SQL Server naar een door SQL beheerd exemplaar met de replay-service voor logboek registratie
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 02/23/2021
ms.openlocfilehash: 73963763716d7e18b757b5ade8998f23cc589fdb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661355"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>Data bases migreren van SQL Server naar een door SQL beheerd exemplaar met de replay-service voor logboek registratie
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel wordt uitgelegd hoe u database migratie hand matig kunt configureren van SQL Server 2008-2019 naar een door SQL beheerd exemplaar met behulp van de replay-service voor logboeken (LRS). Dit is een Cloud service die is ingeschakeld voor een beheerd exemplaar op basis van de SQL Server voor de back-uptechnologie van het logboek. LRS moet worden gebruikt in gevallen waarin de Azure Data Migration service (DMS) niet kan worden gebruikt, wanneer er meer controle nodig is, of wanneer er weinig tolerantie voor uitval tijd is.

## <a name="when-to-use-log-replay-service"></a>Wanneer moet u de replay-service voor logboek registratie gebruiken?

Als [Azure DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) niet kan worden gebruikt voor migratie, kan de LRS-Cloud service rechtstreeks worden gebruikt met Power shell, CLI-cmdlets of API, om database migraties hand matig te bouwen en te organiseren in een door SQL beheerd exemplaar. 

U kunt overwegen om LRS Cloud service te gebruiken in een van de volgende situaties:
- Er is meer controle nodig voor uw database migratie project
- Er bestaat een beetje tolerantie voor de uitval tijd voor de migratie cutover
- Een DMS-uitvoerbaar bestand kan niet worden geïnstalleerd in uw omgeving
- DMS-uitvoerbaar bestand heeft geen bestands toegang tot back-ups van data bases
- Er is geen toegang tot het hostbesturingssysteem beschikbaar of er zijn geen beheerders bevoegdheden
- Kan geen netwerk poorten openen vanuit uw omgeving naar Azure

> [!NOTE]
> De aanbevolen methode voor het migreren van data bases van SQL Server naar een SQL Managed instance maakt gebruik van Azure DMS. Deze service maakt gebruik van dezelfde LRS-Cloud service aan de back-end met logboek verzending in de modus voor NORECOVERY. U kunt het beste hand matig gebruikmaken van LRS voor het indelen van migraties in gevallen waarin Azure DMS uw scenario's niet volledig ondersteunt.

## <a name="how-does-it-work"></a>Zo werkt het

Voor het bouwen van een aangepaste oplossing met behulp van LRS om data bases te migreren naar de Cloud, moeten verschillende Orchestration-stappen worden weer gegeven in het diagram en worden beschreven in de onderstaande tabel.

De migratie bestaat uit het maken van volledige database back-ups op SQL Server met ingeschakelde CONTROLESOM en het kopiëren van back-upbestanden naar Azure Blob Storage. LRS wordt gebruikt om back-upbestanden van Azure Blob Storage te herstellen naar een door SQL beheerd exemplaar. Azure Blob Storage wordt gebruikt als een intermediaire opslag tussen SQL Server en SQL Managed instance.

LRS bewaakt Azure Blob Storage voor een nieuw differentieel of logboek back-ups die zijn toegevoegd nadat de volledige back-up is hersteld en herstelt automatisch eventuele nieuwe bestanden die worden toegevoegd. De voortgang van back-upbestanden die worden hersteld op een SQL Managed instance kan worden bewaakt met de service en het proces kan ook worden afgebroken als dat nodig is.

LRS vereist geen specifieke naamgevings Conventie voor back-upbestanden omdat hiermee alle bestanden worden gescand die in Azure worden geplaatst Blob Storage en de back-upketen alleen de bestands koppen lezen. Data bases bevinden zich in de status ' herstellen ' tijdens het migratie proces, omdat ze worden hersteld in de modus [norecovery](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=sql-server-ver15#comparison-of-recovery-and-norecovery) en kunnen niet worden gebruikt voor lezen of schrijven totdat het migratie proces volledig is voltooid. 

Bij het migreren van verschillende data bases moeten back-ups voor elke data base in een afzonderlijke map op Azure Blob Storage worden geplaatst. LRS moet afzonderlijk worden gestart voor elke Data Base en er moeten verschillende paden worden opgegeven voor het scheiden van Azure Blob Storage-mappen. 

LRS kan worden gestart in de modus automatisch aanvullen of doorlopend. Wanneer de modus AutoAanvullen wordt gestart, wordt de migratie automatisch voltooid wanneer de laatst opgegeven back-upbestand is hersteld. Wanneer de service wordt gestart in de modus continu, worden nieuwe back-upbestanden die worden toegevoegd, voortdurend teruggezet en wordt de migratie alleen op de hand matige cutover voltooid. Het is raadzaam om de hand matige cutover alleen uit te voeren nadat de laatste back-up voor het sluiten van het logboek is gemaakt en weer gegeven als hersteld op een SQL Managed instance. Met de laatste cutover stap wordt de data base online gezet en beschikbaar voor het gebruik van lees-en schrijf bewerkingen op een SQL-beheerd exemplaar.

Zodra LRS is gestopt, automatisch door AutoAanvullen of hand matig op cutover, kan het herstel proces niet worden hervat voor een Data Base die online is gebracht op SQL Managed instance. Als u aanvullende back-upbestanden wilt herstellen nadat de migratie is voltooid via automatisch aanvullen, of hand matig op cutover, moet de Data Base worden verwijderd en moet de volledige back-upketen helemaal opnieuw worden hersteld door de LRS opnieuw te starten.

  ![Orchestrator-service voor het opnieuw afspelen van logboeken die wordt uitgelegd voor een SQL-beheerd exemplaar](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Bewerking | Details |
| :----------------------------- | :------------------------- |
| **1. Kopieer back-ups van de data base van SQL Server naar Azure Blob Storage**. | -Kopieer volledige, differentiële en logboek back-ups van SQL Server naar Azure Blob Storage container met behulp van [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)of [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br />-Gebruik bestands namen, aangezien LRS geen specifieke naamgevings Conventie vereist voor het bestand.<br />-Bij het migreren van verschillende data bases is een afzonderlijke map vereist voor elke Data Base. |
| **2. Start de LRS-service in de Cloud**. | -Service kan worden gestart met een keuze uit de volgende cmdlets: <br /> Power shell [Start-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_start-cmdlets](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start). <br /> -Start LRS afzonderlijk voor elke andere data base die verwijst naar een andere back-upmap op Azure Blob Storage. <br />-Zodra de service is gestart, worden er back-ups gemaakt van de Azure Blob Storage-container en beginnen met het herstellen ervan op een SQL-beheerd exemplaar.<br /> -In het geval LRS is gestart in de modus continu, worden alle nieuwe bestanden die zijn geüpload naar de map door de service gecontroleerd en worden er voortdurend logboeken op basis van de LSN-keten toegepast totdat de service wordt gestopt. |
| **2,1. Controleer de voortgang van de bewerking**. | -De voortgang van de herstel bewerking kan worden bewaakt met een keuze of cmdlets: <br /> Power shell [Get-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_show-cmdlets](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show). |
| **2,2. Stop\abort de bewerking indien nodig**. | -Als het migratie proces moet worden afgebroken, kan de bewerking worden gestopt met een keuze uit de volgende cmdlets: <br /> Power shell [-Stop-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) -cmdlets. <br /><br />-Dit resulteert in het verwijderen van de data base die wordt hersteld op een SQL Managed instance. <br />-Eenmaal gestopt, kan LRS voor een Data Base niet worden hervat. Migratie proces moet volledig opnieuw worden gestart. |
| **3. Cutover naar de Cloud wanneer u klaar bent**. | -Zodra alle back-ups zijn hersteld naar een SQL Managed instance, voltooit u de cutover door de bewerking LRS volt ooien te starten met een keuze uit de volgende cmdlets: <br />Power shell [voltooid-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) -cmdlets. <br /><br />-Hierdoor wordt de LRS-service gestopt en wordt de data base online gezet voor lees-en schrijf gebruik op een SQL-beheerd exemplaar.<br /> -Sluit de toepassing connection string van SQL Server naar een door SQL beheerd exemplaar. |

## <a name="requirements-for-getting-started"></a>Vereisten voor aan de slag

### <a name="sql-server-side"></a>SQL Server zijde
- SQL Server 2008-2019
- Volledige back-up van data bases (een of meer bestanden)
- Differentiële back-up (een of meer bestanden)
- Logboek back-up (niet gesplitst voor transactie logboek bestand)
- De **controlesom moet zijn ingeschakeld** voor back-ups (verplicht)

### <a name="azure-side"></a>Azure-zijde
- Power shell AZ. SQL module versie 2.16.0 of hoger ([Installeer](https://www.powershellgallery.com/packages/Az.Sql/), of gebruik Azure [Cloud shell](https://docs.microsoft.com/azure/cloud-shell/))
- CLI-versie 2.19.0 of hoger ([installeren](https://docs.microsoft.com/cli/azure/install-azure-cli))
- Ingerichte Azure Blob Storage-container
- SAS-beveiligings token met alleen- **lezen** en **lijst** met machtigingen die zijn gegenereerd voor de BLOB storage-container

### <a name="migrating-multiple-databases"></a>Meerdere data bases migreren
- Back-upbestanden voor verschillende data bases moeten in afzonderlijke mappen op Azure Blob Storage worden geplaatst.
- LRS moet afzonderlijk worden gestart voor elke Data Base die verwijst naar een geschikte map op Azure Blob Storage.
- LRS kan Maxi maal 100 gelijktijdige herstel processen ondersteunen per single SQL Managed instance.

### <a name="azure-rbac-permissions-required"></a>Vereiste Azure RBAC-machtigingen
Voor het uitvoeren van LRS via de door u aangelegde clients is een van de volgende Azure-rollen vereist:
- De rol van abonnements eigenaar of
- Rol van [beheerde instantie Inzender](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) of
- Aangepaste rol met de volgende machtiging:
  - `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Aanbevolen procedures

Het volgende wordt aanbevolen als aanbevolen procedures:
- Voer [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) uit om te controleren of uw data bases klaar zijn om te worden gemigreerd naar een SQL-beheerd exemplaar. 
- U kunt volledige en differentiële back-ups in meerdere bestanden splitsen, in plaats van één bestand.
- Back-upcompressie inschakelen.
- Gebruik Cloud Shell om scripts uit te voeren, omdat deze altijd worden bijgewerkt naar de nieuwste cmdlets die zijn uitgebracht.
- Plan de migratie binnen 47 uur te volt ooien, omdat de LRS-service is gestart. Dit is een respijt periode voor het voor komen van door het systeem beheerde software patches zodra LRS is gestart.

> [!IMPORTANT]
> - De data base die wordt hersteld met LRS kan pas worden gebruikt als het migratie proces is voltooid. De reden hiervoor is dat de onderliggende technologie herstellen in de modus NORECOVERY.
> - De modus stand-by herstellen waarvoor alleen-lezen toegang tot data bases tijdens de migratie wordt toegestaan, wordt niet ondersteund door LRS vanwege de versie verschillen tussen SQL Managed instance en in-Market SQL servers.
> - Zodra de migratie is voltooid door het automatisch aanvullen of op hand matige cutover, wordt het migratie proces afgerond, omdat LRS geen ondersteuning biedt voor het hervatten van herstel.

## <a name="steps-to-execute"></a>Stappen om uit te voeren

### <a name="make-backups-on-the-sql-server"></a>Back-ups maken op het SQL Server

Back-ups op de SQL Server kunnen worden gemaakt met een van de volgende twee opties:

- U maakt een back-up naar de lokale schijf opslag en uploadt vervolgens bestanden naar Azure Blob Storage, voor het geval uw omgeving beperkend is voor directe back-ups naar Azure Blob Storage.
- Maak rechtstreeks een back-up naar Azure Blob Storage met de optie ' naar URL ' in T-SQL, in het geval uw omgevings-en beveiligings procedures u in staat stellen om dit te doen. 

Stel de data bases in die u wilt migreren naar de modus voor volledig herstel om logboek back-ups toe te staan.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery model.
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Als u hand matig volledige, diff-en logboek back-up van uw Data Base op de lokale opslag wilt maken, gebruikt u de volgende voor beelden van T-SQL-scripts. Zorg ervoor dat de CONTROLESOM optie is ingeschakeld, omdat dit een verplichte vereiste is voor LRS.

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full_14_43.bak',
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff_14_44.bak',
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the log backup
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log_14_45.bak',
WITH CHECKSUM
GO
```

Bestanden waarvan een back-up is gemaakt naar de lokale opslag, moeten worden geüpload naar de Azure-Blob Storage. Als uw bedrijfs beleid dit toestaat, kan een alternatieve manier om back-ups rechtstreeks naar Azure Blob Storage te maken, worden beschreven in de volgende zelf studie: [gebruik Azure Blob Storage-service met SQL Server](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). Als u deze alternatieve methode gebruikt, moet u ervoor zorgen dat alle back-ups zijn gemaakt met de optie CONTROLESOM ingeschakeld.

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Back-ups kopiëren van SQL Server naar Azure Blob Storage

Enkele van de volgende benaderingen kunnen worden gebruikt voor het uploaden van back-ups naar de Blob-opslag in data bases migreren naar een beheerd exemplaar met behulp van LRS:
- Gebruik SQL Server systeem eigen [back-up naar URL-](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) functionaliteit.
- Met [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)of [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer) het uploaden van back-ups naar een BLOB-container.
- Het gebruik van Storage Explorer in Azure Portal.

### <a name="create-azure-blob-and-sas-authentication-token"></a>Azure-Blob en SAS-verificatie token maken

Azure Blob Storage wordt gebruikt als een intermediaire opslag voor back-upbestanden tussen SQL Server en SQL Managed instance. Volg deze stappen om Azure Blob Storage-container te maken:

1. [Een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Een BLOB-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) in het opslag account Crete

Als een BLOB-container is gemaakt, genereert u een SAS-verificatie token met de machtigingen lezen en lijst. u kunt de volgende stappen uitvoeren:

1. Toegang tot opslag account met Azure Portal
2. Ga naar Storage Explorer
3. BLOB-containers uitvouwen
4. Klik met de rechter muisknop op de BLOB-container
5. Shared Access Signature ophalen selecteren
6. Selecteer de periode voor het verloop van het token. Zorg ervoor dat het token geldig is voor de duur van de migratie.
    - Houd er rekening mee dat de tijd zone van het token en uw door SQL beheerde instantie mogelijk niet overeenkomen. Zorg ervoor dat de SAS-token de juiste tijds duur heeft om rekening te houden met tijd zones. Stel, indien mogelijk, de tijd zone in op een eerdere en latere tijd van uw geplande migratie venster.
8. Zorg ervoor dat de machtigingen lezen en alleen lijst zijn geselecteerd
9. Klik op maken
10. Kopieer het token na het vraag teken '? ' en vervolgens naar de andere. Het SAS-token begint doorgaans met "SV = 2020-10" in de URI voor gebruik in uw code.

> [!IMPORTANT]
> - Machtigingen voor het SAS-token voor Azure Blob Storage moeten worden gelezen en alleen worden weer geven. In het geval van andere machtigingen die voor het SAS-verificatie token worden verleend, mislukt het starten van de LRS-service. Deze beveiligings vereisten zijn per ontwerp.
> - Het token moet de juiste geldigheids duur hebben. Zorg ervoor dat tijd zones tussen het token en het beheerde exemplaar in aanmerking worden genomen.
> - Zorg ervoor dat het token wordt gekopieerd vanaf "SV = 2020-10..." tot het einde van de teken reeks.

### <a name="log-in-to-azure-and-select-subscription"></a>Meld u aan bij Azure en selecteer abonnement

Gebruik de volgende Power shell-cmdlet om u aan te melden bij Azure:

```powershell
Login-AzAccount
```

Selecteer het juiste abonnement waar uw SQL Managed instance zich bevindt met behulp van de volgende Power shell-cmdlet:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>De migratie starten

De migratie wordt gestart door de LRS-service te starten. De service kan worden gestart in de modus automatisch aanvullen of doorlopend. Wanneer de modus AutoAanvullen wordt gestart, wordt de migratie automatisch voltooid wanneer het laatst opgegeven back-upbestand is hersteld. Deze optie vereist dat de start opdracht de bestands naam van het laatste back-upbestand opgeeft. Wanneer LRS wordt gestart in de continue modus, worden er door de service voortdurend nieuwe back-upbestanden hersteld en wordt de migratie alleen op de hand matige cutover voltooid. 

### <a name="start-lrs-in-autocomplete-mode"></a>LRS starten in de modus automatisch aanvullen

Als u de LRS-service in de modus automatisch aanvullen wilt starten, gebruikt u de volgende Power shell-of CLI-opdrachten. Geef de naam van het laatste back-upbestand met de para meter-LastBackupName op. Bij het herstellen van de laatste back-up die is opgegeven, initieert de service automatisch een cutover.

LRS starten in de modus AutoAanvullen-Power shell-voor beeld:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

LRS starten in de modus voor automatisch aanvullen-CLI-voor beeld:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>LRS starten in doorlopende modus

LRS starten in doorlopende modus-Power shell-voor beeld:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Start LRS in de continue modus-CLI-voor beeld:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> Zodra LRS is gestart, worden alle door het systeem beheerde software patches voor de volgende 47 uur gestopt. Wanneer dit venster wordt door gegeven, stopt de volgende geautomatiseerde software patch automatisch de doorlopende LRS. In dat geval kan de migratie niet worden hervat en moet deze helemaal opnieuw worden opgestart. 

## <a name="monitor-the-migration-progress"></a>De voortgang van de migratie bewaken

Als u de voortgang van de migratie bewerking wilt controleren, gebruikt u de volgende Power shell-opdracht:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Gebruik de volgende CLI-opdracht om de voortgang van de migratie bewerking te controleren:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>De migratie stoppen

Gebruik de volgende cmdlets voor het geval u de migratie wilt stoppen. Als u de migratie stopt, wordt de data base die wordt teruggezet op een SQL Managed instance verwijderd, omdat het niet mogelijk is om de migratie te hervatten.

Gebruik de volgende Power shell-opdracht om het migratie proces te stop\abort:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Gebruik de volgende CLI-opdracht om het migratie proces te stop\abort:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>De migratie volt ooien (doorlopende modus)

Als LRS in de modus continu wordt gestart, wordt de migratie voltooid wanneer u hebt gezorgd dat alle back-ups zijn hersteld. Wanneer de cutover is voltooid, wordt de data base gemigreerd en gereed voor lees-en schrijf toegang.

Gebruik de volgende Power shell-opdracht om het migratie proces in de doorlopende modus LRS te volt ooien:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Gebruik de volgende CLI-opdracht om het migratie proces in de doorlopende modus LRS te volt ooien:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="troubleshooting"></a>Problemen oplossen

Wanneer u de LRS hebt gestart, gebruikt u de controle-cmdlets (Get-azsqlinstancedatabaselogreplay of az_sql_midb_log_replay_show) om de status van de bewerking te bekijken. Als na enige tijd LRS niet kan worden gestart met een fout, controleert u op een aantal van de meest voorkomende problemen:
- Is de back-up van de Data Base op de SQL Server gemaakt met behulp van de **controlesom** optie?
- Worden de machtigingen voor het SAS-token alleen **gelezen** en **weer geven** voor de LRS-service?
- Was het SAS-token voor LRS gekopieerd vanaf het vraag teken '? ' met inhoud die vergelijkbaar is met ' SV = 2020-02-10... '? 
- Is de **geldigheids** duur van het SAS-token van toepassing op het tijd venster voor het starten en volt ooien van de migratie? Houd er rekening mee dat er geen verschillen kunnen optreden als gevolg van de verschillende **tijd zones** die worden gebruikt voor SQL Managed instance en het SAS-token. Probeer het SAS-token opnieuw te genereren met een uitbrei ding van de geldigheid van het token van het tijd venster vóór en na de huidige datum.
- Zijn de database naam, de naam van de resource groep en de naam van de beheerde instantie correct gespeld?
- Als LRS is gestart in de modus automatisch aanvullen, was een geldige bestands naam voor het laatst opgegeven back-upbestand?

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [het migreren van SQL Server naar SQL Managed instance](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Meer informatie over [verschillen tussen SQL Server en Azure SQL Managed instance](transact-sql-tsql-differences-sql-server.md).
- Meer informatie over [Best practices voor werk belastingen die zijn gemigreerd naar Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
