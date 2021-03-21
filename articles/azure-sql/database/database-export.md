---
title: Een Azure SQL Database exporteren naar een BACPAC-bestand (de Azure Portal)
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Een Data Base exporteren naar een BACPAC-bestand met behulp van de Azure Portal.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
author: stevestein
ms.custom: sqldbrb=2
ms.author: sstein
ms.reviewer: ''
ms.date: 01/11/2021
ms.topic: how-to
ms.openlocfilehash: 866500e9cd9e3fe6aac6a5bfded0dbb21ab137fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614238"
---
# <a name="export-to-a-bacpac-file---azure-sql-database-and-azure-sql-managed-instance"></a>Exporteren naar een BACPAC-bestand-Azure SQL Database en Azure SQL Managed instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Wanneer u een Data Base voor archivering wilt exporteren of als u wilt overstappen op een ander platform, kunt u het database schema en de gegevens exporteren naar een [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#Anchor_4) -bestand. Een BACPAC-bestand is een ZIP-bestand met een uitbrei ding van BACPAC die de meta gegevens en gegevens uit de Data Base bevat. Een BACPAC-bestand kan worden opgeslagen in Azure Blob-opslag of in lokale opslag op een on-premises locatie en later opnieuw worden geïmporteerd in Azure SQL Database, Azure SQL Managed instance of een SQL Server-exemplaar.

## <a name="considerations"></a>Overwegingen

- Als u een transactioneel exemplaar wilt exporteren, moet u ervoor zorgen dat er geen schrijf activiteit plaatsvindt tijdens het exporteren of dat u exporteert vanuit een [Transactioneel consistente kopie](database-copy.md) van uw data base.
- Als u exporteert naar Blob Storage, is de maximale grootte van een BACPAC-bestand 200 GB. Als u een groter BACPAC-bestand wilt archiveren, exporteert u het naar lokale opslag.
- Het exporteren van een BACPAC-bestand naar Azure Premium Storage met behulp van de methoden die in dit artikel worden beschreven, wordt niet ondersteund.
- Opslag achter een firewall wordt momenteel niet ondersteund.
- De naam van het opslag bestand of de invoer waarde voor StorageURI moet kleiner zijn dan 128 tekens en mag niet eindigen op '. ' en mag geen speciale tekens bevatten zoals een spatie teken of ' <, >, *,%, &,:, \, /,? '. 
- Als de export bewerking 20 uur overschrijdt, kan deze worden geannuleerd. Als u de prestaties tijdens het exporteren wilt verbeteren, kunt u het volgende doen:

  - Uw reken grootte tijdelijk verhogen.
  - Stopt alle Lees-en schrijf activiteiten tijdens het exporteren.
  - Gebruik een [geclusterde index](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described) met niet-Null-waarden voor alle grote tabellen. Zonder geclusterde indexen kan een export mislukken als deze langer duurt dan 6-12 uur. Dit komt doordat de export service een tabel scan moet volt ooien om een volledige tabel te kunnen exporteren. Een goede manier om te bepalen of uw tabellen zijn geoptimaliseerd voor exporteren, is door **DBCC SHOW_STATISTICS** uit te voeren en ervoor te zorgen dat de *RANGE_HI_KEY* niet null is en dat de waarde van de tabel goed is gedistribueerd. Zie [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql)voor meer informatie.

> [!NOTE]
> BACPACs zijn niet bedoeld om te worden gebruikt voor back-up-en herstel bewerkingen. Azure maakt automatisch back-ups voor elke gebruikers database. Zie [overzicht van bedrijfs continuïteit](business-continuity-high-availability-disaster-recover-hadr-overview.md) en [SQL database back-ups](automated-backups-overview.md)voor meer informatie.

## <a name="the-azure-portal"></a>Azure Portal

Het exporteren van een BACPAC van een Data Base vanuit een [Azure SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md) met behulp van de Azure portal wordt momenteel niet ondersteund. Gebruik in plaats daarvan SQL Server Management Studio of SQLPackage.

> [!NOTE]
> Op machines waarmee import-/exportaanvragen worden verwerkt die zijn verzonden via Azure Portal of PowerShell, moet het BACPAC-bestand worden opgeslagen, evenals tijdelijke bestanden die worden gegenereerd door Data-Tier Application Framework (DacFX). De benodigde schijfruimte varieert aanzienlijk tussen databases met dezelfde grootte. Er kan tot drie keer zoveel schijfruimte nodig zijn als de grootte van de database. Machines met het verzoek om te importeren/exporteren hebben 450GB lokale schijf ruimte. Als gevolg hiervan kunnen sommige aanvragen mislukken met de fout `There is not enough space on the disk`. Dit kan worden opgelost door sqlpackage.exe uit te voeren op een machine met voldoende lokale schijfruimte. We raden u aan [SqlPackage](#sqlpackage-utility) -data bases die groter zijn dan 150 GB te importeren/exporteren om dit probleem te voor komen.

1. Als u een Data Base wilt exporteren met behulp van de [Azure Portal](https://portal.azure.com), opent u de pagina voor uw data base en klikt u op **exporteren** op de werk balk.

   ![Scherm afbeelding die de knop exporteren markeert.](./media/database-export/database-export1.png)

2. Geef de BACPAC-bestands naam op, selecteer een bestaand Azure Storage-account en een container voor de export en geef vervolgens de juiste referenties op voor toegang tot de bron database. Een SQL **Server-beheerders aanmelding** is hier ook nodig, zelfs als u de Azure-beheerder bent, omdat een Azure-beheerder niet gelijk is aan de beheerders machtigingen in Azure SQL database of Azure SQL Managed instance.

    ![Data base exporteren](./media/database-export/database-export2.png)

3. Klik op **OK**.

4. Als u de voortgang van de export bewerking wilt controleren, opent u de pagina voor de server die de Data Base bevat die wordt geëxporteerd. Onder **instellingen** en klikt u op **geschiedenis van importeren/exporteren**.

   ![Geschiedenis exporteren](./media/database-export/export-history.png)

## <a name="sqlpackage-utility"></a>Hulp programma SQLPackage

Zie [para meters en eigenschappen exporteren](/sql/tools/sqlpackage#export-parameters-and-properties)als u een data base in SQL database wilt exporteren met behulp van het opdracht regel programma [SqlPackage](/sql/tools/sqlpackage) . Het hulp programma SQLPackage wordt geleverd met de nieuwste versies van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) en [SQL Server Data tools voor Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt), of u kunt de nieuwste versie van [SQLPackage](https://www.microsoft.com/download/details.aspx?id=53876) rechtstreeks downloaden vanuit het micro soft Download centrum.

We raden u aan het SQLPackage-hulp programma te gebruiken voor schaal en prestaties in de meeste productie omgevingen. Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).

In dit voor beeld ziet u hoe u een Data Base exporteert met SqlPackage.exe met Active Directory universele verificatie:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

De nieuwste versies van SQL Server Management Studio bieden een wizard voor het exporteren van een data base in Azure SQL Database of een SQL Managed instance-data base naar een BACPAC-bestand. Zie de [toepassing een gegevenslaag exporteren](/sql/relational-databases/data-tier-applications/export-a-data-tier-application)voor meer informatie.

## <a name="powershell"></a>PowerShell

> [!NOTE]
> [Azure SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md) biedt momenteel geen ondersteuning voor het exporteren van een Data Base naar een BACPAC-bestand met behulp van Azure PowerShell. Als u een beheerd exemplaar wilt exporteren naar een BACPAC-bestand, gebruikt u SQL Server Management Studio of SQLPackage.

Gebruik de cmdlet [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) om een aanvraag voor het exporteren van een Data Base naar de Azure SQL database-service te verzenden. Afhankelijk van de grootte van uw data base kan het enige tijd duren voordat de export bewerking is voltooid.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Als u de status van de export aanvraag wilt controleren, gebruikt u de cmdlet [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) . Als deze onmiddellijk wordt uitgevoerd, wordt de status van de aanvraag meestal geretourneerd **: InProgress**. Wanneer u **status ziet: geslaagd** dat de export is voltooid.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```
## <a name="cancel-the-export-request"></a>De export aanvraag annuleren

Gebruik de [API data base-annuleren](https://docs.microsoft.com/rest/api/sql/databaseoperations/cancel) of de Power shell [-opdracht stop-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Stop-AzSqlDatabaseActivity). Hier volgt een voor beeld van een Power shell-opdracht.

```cmd
Stop-AzSqlDatabaseActivity -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -OperationId $Operation.OperationId
```

## <a name="next-steps"></a>Volgende stappen

- Zie [lange termijn retentie van back-ups](long-term-retention-overview.md)voor meer informatie over lange termijn retentie van een Data Base en gepoolde data bases als alternatief voor het exporteren van een Data Base voor archief doeleinden. U kunt SQL Agent-taken gebruiken voor het plannen van [back-ups met alleen-kopiëren](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) als alternatief voor lange termijn retentie van back-ups.
- Raadpleeg dit blogartikel van het SQL Server-klantadviesteam over migratie met behulp van BACPAC-bestanden: [Migrating from SQL Server to Azure SQL Database using BACPAC Files](/archive/blogs/sqlcat/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files) (Migreren van SQL Server naar Azure SQL Database met BACPAC-bestanden).
- Zie [een BACPAC naar een SQL Server-Data Base importeren](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database)voor meer informatie over het importeren van een BACPAC naar een SQL Server-Data Base.
- Zie [een gegevenslaag toepassing exporteren](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) voor meer informatie over het exporteren van een BACPAC vanuit een SQL Server-Data Base.
- Zie voor meer informatie over het gebruik van de Data Migration-service voor het migreren van een Data Base [migreren van SQL Server naar Azure SQL database offline met behulp van DMS](../../dms/tutorial-sql-server-to-azure-sql.md).
- Als u exporteert van SQL Server als een prelude naar Azure SQL Database, raadpleegt u [een SQL Server-Data Base migreren naar Azure SQL database](migrate-to-database-from-sql-server.md).
- Zie de [Azure Storage-beveiligingshandleiding](../../storage/blobs/security-recommendations.md) voor meer informatie over het veilig beheren en delen van opslagsleutels en handtekeningen voor gedeelde toegang.
