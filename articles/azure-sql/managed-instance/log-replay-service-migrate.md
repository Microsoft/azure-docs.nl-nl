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
ms.date: 02/17/2021
ms.openlocfilehash: 7892b1fe0fcad77d1fde8b44f4a8745b5c7dd334
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654187"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>Data bases migreren van SQL Server naar een door SQL beheerd exemplaar met de replay-service voor logboek registratie
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel wordt uitgelegd hoe u database migratie hand matig kunt configureren van SQL Server 2008-2019 naar een door SQL beheerd exemplaar met behulp van de replay-service voor logboeken (LRS). Dit is een Cloud service die is ingeschakeld voor een beheerd exemplaar op basis van de SQL Server de back-uptechnologie voor logboeken in geen herstel modus. LRS moet worden gebruikt in gevallen waarbij DMS (Data Migration service) niet kan worden gebruikt, wanneer er meer controle nodig is of wanneer er weinig tolerantie voor uitval tijd is.

## <a name="when-to-use-log-replay-service"></a>Wanneer moet u de replay-service voor logboek registratie gebruiken?

Als [Azure DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) niet kan worden gebruikt voor migratie, kan de LRS-Cloud service rechtstreeks worden gebruikt met Power shell, CLI-cmdlets of API, om database migraties hand matig te bouwen en te organiseren in een door SQL beheerd exemplaar. 

U kunt overwegen om LRS Cloud service te gebruiken in een van de volgende situaties:
- Er is meer controle nodig voor uw database migratie project
- Er bestaat een beetje tolerantie voor de uitval tijd voor de migratie cutover
- Een DMS-uitvoerbaar bestand kan niet worden geïnstalleerd in uw omgeving
- DMS-uitvoerbaar bestand heeft geen bestands toegang tot back-ups van data bases
- Er is geen toegang tot het hostbesturingssysteem beschikbaar of er zijn geen beheerders bevoegdheden

> [!NOTE]
> De aanbevolen methode voor het migreren van data bases van SQL Server naar een SQL Managed instance maakt gebruik van Azure DMS. Deze service maakt gebruik van dezelfde LRS-Cloud service aan de back-end met logboek verzending in de modus voor niet-herstel. U kunt het beste hand matig gebruikmaken van LRS voor het indelen van migraties in gevallen waarin Azure DMS uw scenario's niet volledig ondersteunt.

## <a name="how-does-it-work"></a>Zo werkt het

Voor het bouwen van een aangepaste oplossing met behulp van LRS om een Data Base naar de cloud te migreren, moeten verschillende Orchestration-stappen worden weer gegeven in het diagram en worden beschreven in de onderstaande tabel.

Bij de migratie wordt geprofiteerd van het maken van volledige database back-ups op SQL Server en het kopiëren van back-upbestanden naar Azure Blob Storage. LRS wordt gebruikt om back-upbestanden van Azure Blob-opslag naar een door SQL beheerd exemplaar te herstellen. Azure Blob-opslag wordt gebruikt als een intermediaire opslag tussen SQL Server en een door SQL beheerd exemplaar.

LRS bewaakt Azure Blob-opslag voor een nieuw differentieel of logboek back-ups die zijn toegevoegd nadat de volledige back-up is hersteld en herstelt automatisch nieuwe bestanden die worden toegevoegd. De voortgang van back-upbestanden die worden hersteld op een SQL Managed instance kan worden bewaakt met de service en het proces kan ook worden afgebroken als dat nodig is. Data bases die tijdens het migratie proces worden hersteld, bevinden zich in een herstel modus en kunnen niet worden gebruikt om te lezen of te schrijven totdat het proces is voltooid.

LRS kan worden gestart in de modus automatisch aanvullen of doorlopend. Wanneer de modus AutoAanvullen wordt gestart, wordt de migratie automatisch voltooid wanneer het laatst opgegeven back-upbestand is hersteld. Wanneer de service wordt gestart in de modus continu, worden nieuwe back-upbestanden die worden toegevoegd, voortdurend teruggezet en wordt de migratie alleen op de hand matige cutover voltooid. Met de laatste cutover-stap worden data bases beschikbaar gesteld voor lezen en schrijven voor gebruik door SQL beheerd exemplaar. 

  ![Orchestrator-service voor het opnieuw afspelen van logboeken die wordt uitgelegd voor een SQL-beheerd exemplaar](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Bewerking | Details |
| :----------------------------- | :------------------------- |
| **1. Kopieer back-ups van de data base van SQL Server naar Azure Blob-opslag**. | -Kopieer volledige, differentiële en logboek back-ups van SQL Server naar Azure Blob-opslag met behulp van [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) of [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br />-Bij het migreren van verschillende data bases is een afzonderlijke map vereist voor elke Data Base. |
| **2. Start de LRS-service in de Cloud**. | -Service kan worden gestart met een keuze uit de volgende cmdlets: <br /> Power shell [Start-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_start-cmdlets](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start). <br /><br />Eenmaal gestart, maakt de service back-ups van de Azure Blob-opslag en wordt het herstellen ervan gestart op een SQL-beheerd exemplaar. <br /> -Wanneer alle voor het eerst geüploade back-ups zijn hersteld, wordt de service gecontroleerd op nieuwe bestanden die naar de map worden geüpload en worden er voortdurend logboeken op basis van de LSN-keten toegepast totdat de service wordt gestopt. |
| **2,1. Controleer de voortgang van de bewerking**. | -De voortgang van de herstel bewerking kan worden bewaakt met een keuze of cmdlets: <br /> Power shell [Get-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_show-cmdlets](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show). |
| **2,2. Stop\abort de bewerking indien nodig**. | -Als het migratie proces moet worden afgebroken, kan de bewerking worden gestopt met een keuze uit de volgende cmdlets: <br /> Power shell [-Stop-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) -cmdlets. <br /><br />-Dit leidt ertoe dat de data base die wordt hersteld op een SQL-beheerd exemplaar, wordt verwijderd. <br />-Na het stoppen kan LRS niet worden voortgezet voor een Data Base. Migratie proces moet volledig opnieuw worden gestart. |
| **3. Cutover naar de Cloud wanneer u klaar bent**. | -Zodra alle back-ups zijn hersteld naar een SQL Managed instance, voltooit u de cutover door de bewerking LRS volt ooien te starten met een keuze aan API-aanroepen of cmdlets: <br />Power shell [voltooid-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) -cmdlets. <br /><br />-Dit zorgt ervoor dat de LRS-service wordt gestopt en de Data Base op het beheerde exemplaar wordt hersteld. <br />-Sluit de toepassing connection string van SQL Server naar een door SQL beheerd exemplaar. <br />-De Data Base voor het volt ooien van de bewerking is beschikbaar voor R/W-bewerkingen in de Cloud. |

## <a name="requirements-for-getting-started"></a>Vereisten voor aan de slag

### <a name="sql-server-side"></a>SQL Server zijde
- SQL Server 2008-2019
- Volledige back-up van data bases (een of meer bestanden)
- Differentiële back-up (een of meer bestanden)
- Logboek back-up (niet gesplitst voor transactie logboek bestand)
- **Controlesom moet zijn ingeschakeld** als verplicht

### <a name="azure-side"></a>Azure-zijde
-   Power shell AZ. SQL module versie 2.16.0 of hoger ([Installeer](https://www.powershellgallery.com/packages/Az.Sql/), of gebruik Azure [Cloud shell](https://docs.microsoft.com/azure/cloud-shell/))
-   CLI-versie 2.19.0 of hoger ([installeren](https://docs.microsoft.com/cli/azure/install-azure-cli))
-   Azure Blob Storage ingericht
-   SAS-beveiligings token met alleen- **lezen** en **lijst** met machtigingen die zijn gegenereerd voor de Blob-opslag

## <a name="best-practices"></a>Aanbevolen procedures

Het volgende wordt aanbevolen als aanbevolen procedures:
- Als u [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) uitvoert om uw data bases te valideren, worden er geen problemen gemigreerd naar een SQL-beheerd exemplaar. 
- U kunt volledige en differentiële back-ups in meerdere bestanden splitsen, in plaats van één bestand.
- Back-upcompressie inschakelen.
- Gebruik Cloud Shell om scripts uit te voeren, omdat deze altijd worden bijgewerkt naar de nieuwste cmdlets die zijn uitgebracht.
- Plan de migratie binnen 47 uur te volt ooien, omdat de LRS-service is gestart.

> [!IMPORTANT]
> - De data base die wordt hersteld met LRS kan pas worden gebruikt als het migratie proces is voltooid. De reden hiervoor is dat de onderliggende technologie logboek verzending in geen herstel modus is.
> - De standby-modus voor logboek verzending wordt niet ondersteund door LRS vanwege de versie verschillen tussen het beheerde exemplaar van SQL en de nieuwste in-Market SQL Server versie.

## <a name="steps-to-execute"></a>Stappen om uit te voeren

## <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Back-ups kopiëren van SQL Server naar Azure Blob-opslag

De volgende twee benaderingen kunnen worden gebruikt voor het kopiëren van back-ups naar de Blob-opslag in data bases migreren naar een beheerd exemplaar met behulp van LRS:
- Gebruik SQL Server systeem eigen [back-up naar URL-](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) functionaliteit.
- Het kopiëren van back-ups naar een BLOB-container met [Azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)of [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer). 

## <a name="create-azure-blob-and-sas-authentication-token"></a>Azure-Blob en SAS-verificatie token maken

Azure Blob-opslag wordt gebruikt als een tussenliggende opslag voor back-upbestanden tussen SQL Server en SQL Managed instance. Volg deze stappen om een Azure Blob Storage-container te maken:

1. [Een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Een BLOB-container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) in het opslag account Crete

Als een BLOB-container is gemaakt, genereert u een SAS-verificatie token met de machtigingen lezen en lijst. u kunt de volgende stappen uitvoeren:

1. Toegang tot opslag account met Azure Portal
2. Ga naar Storage Explorer
3. BLOB-containers uitvouwen
4. Klik met de rechter muisknop op de BLOB-container
5. Shared Access Signature ophalen selecteren
6. Selecteer de periode voor het verloop van het token. Zorg ervoor dat het token geldig is voor de duur van de migratie.
7. Zorg ervoor dat de machtigingen lezen en alleen lijst zijn geselecteerd
8. Klik op maken
9. Het token te beginnen met "SV =" in de URI kopiëren voor gebruik in uw code

> [!IMPORTANT]
> Machtigingen voor het SAS-token voor Azure Blob-opslag moeten alleen worden gelezen en alleen worden weer geven. In het geval van andere machtigingen die voor het SAS-verificatie token worden verleend, mislukt het starten van de LRS-service. Deze beveiligings vereisten zijn per ontwerp.

## <a name="log-in-to-azure-and-select-subscription"></a>Meld u aan bij Azure en selecteer abonnement

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

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoComplete -LastBackupName "last_backup.bak"
```

LRS starten in de modus voor automatisch aanvullen-CLI-voor beeld:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>LRS starten in doorlopende modus

LRS starten in doorlopende modus-Power shell-voor beeld:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Start LRS in de continue modus-CLI-voor beeld:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> Zodra LRS is gestart, worden alle door het systeem beheerde software patches voor de volgende 47 uur gestopt. Wanneer dit venster wordt door gegeven, stopt de volgende geautomatiseerde software patch automatisch de doorlopende LRS. In dat geval kan de migratie niet worden hervat en moet deze helemaal opnieuw worden opgestart. 

## <a name="monitor-the-migration-progress"></a>De voortgang van de migratie bewaken

Als u de voortgang van de migratie bewerking wilt controleren, gebruikt u de volgende Power shell-opdracht:

```powershell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Gebruik de volgende CLI-opdracht om de voortgang van de migratie bewerking te controleren:

```cli
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>De migratie stoppen

Gebruik de volgende cmdlets voor het geval u de migratie wilt stoppen. Als u de migratie stopt, wordt de data base die wordt teruggezet op een SQL Managed instance verwijderd, omdat het niet mogelijk is om de migratie te hervatten.

Gebruik de volgende Power shell-opdracht om het migratie proces te stop\abort:

```powershell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Gebruik de volgende CLI-opdracht om het migratie proces te stop\abort:

```cli
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>De migratie volt ooien (doorlopende modus)

Als LRS in de modus continu wordt gestart, wordt de migratie voltooid wanneer u hebt gezorgd dat alle back-ups zijn hersteld. Wanneer de cutover is voltooid, wordt de data base gemigreerd en gereed voor lees-en schrijf toegang.

Gebruik de volgende Power shell-opdracht om het migratie proces in de doorlopende modus LRS te volt ooien:

```powershell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" -LastBackupName "last_backup.bak"
```

Gebruik de volgende CLI-opdracht om het migratie proces in de doorlopende modus LRS te volt ooien:

```cli
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [het migreren van SQL Server naar SQL Managed instance](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Meer informatie over [verschillen tussen SQL Server en Azure SQL Managed instance](transact-sql-tsql-differences-sql-server.md).
- Meer informatie over [Best practices voor werk belastingen die zijn gemigreerd naar Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
