---
title: Een Data Base migreren van SQL Server naar Azure Arc enabled SQL Managed instance
description: Data base migreren van SQL Server naar Azure Arc enabled SQL Managed instance
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 86563b0a44bade2cedaf76af3c247821756111fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90936329"
---
# <a name="migrate-sql-server-to-azure-arc-enabled-sql-managed-instance"></a>Migreren: SQL Server naar Azure Arc enabled SQL Managed instance

Dit scenario begeleidt u stapsgewijs door de stappen voor het migreren van een Data Base van een SQL Server-exemplaar naar Azure SQL Managed instance in azure Arc via twee verschillende methoden voor back-up en herstel.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="use-azure-blob-storage"></a>Azure Blob-opslag gebruiken 

Gebruik Azure Blob Storage voor de migratie naar Azure Arc enabled SQL Managed instance.

Deze methode maakt gebruik van Azure Blob Storage als tijdelijke opslag locatie waarop u een back-up kunt maken en waarvan u de herstel bewerking wilt herstellen.

### <a name="prerequisites"></a>Vereisten

- [Azure Data Studio installeren](install-client-tools.md)
- [Azure Storage Explorer installeren](https://azure.microsoft.com/features/storage-explorer/)
- Azure-abonnement

### <a name="step-1-provision-azure-blob-storage"></a>Stap 1: Azure Blob-opslag inrichten

1. Volg de stappen die worden beschreven in [een Azure Blob Storage-account maken](../../storage/blobs/storage-blob-create-account-block-blob.md?tabs=azure-portal)
1. Azure Storage Explorer starten
1. [Meld u aan bij Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) om toegang te krijgen tot de Blob-opslag die in de vorige stap is gemaakt
1. Klik met de rechter muisknop op het Blob Storage-account en selecteer **BLOB-container maken** om een nieuwe container te maken waarin het back-upbestand wordt opgeslagen

### <a name="step-2-get-storage-blob-credentials"></a>Stap 2: de referenties van de opslag-BLOB ophalen

1. Klik in Azure Storage Explorer met de rechter muisknop op de zojuist gemaakte BLOB-container en selecteer **ophalen Shared Access Signature**

1. Selecteer de **Lees**-, **Schrijf** -en **lijst**

1. Selecteer **Maken**

   Noteer de URI en de query reeks in dit scherm. Deze zijn nodig in latere stappen. Klik op de knop **kopiëren** om deze op te slaan in Klad blok/OneNote, enzovoort.

1. Sluit het **Shared Access Signature** venster.

### <a name="step-3-backup-database-file-to-azure-blob-storage"></a>Stap 3: back-up maken van database bestand naar Azure Blob Storage

In deze stap maakt u verbinding met de bron SQL Server en maakt u het back-upbestand van de data base die u wilt migreren naar SQL Managed instance-Azure Arc.

1. Azure Data Studio starten
1. Verbinding maken met het SQL Server-exemplaar met de data base die u wilt migreren naar een SQL-beheerd exemplaar-Azure-boog
1. Klik met de rechter muisknop op de data base en selecteer **nieuwe query** .
1. Bereid uw query voor in de volgende indeling Vervang de tijdelijke aanduidingen die worden aangegeven door de `<...>` informatie uit de gedeelde toegangs handtekening in eerdere stappen.  Nadat u de waarden hebt vervangen, voert u de query uit.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
      SECRET = '<SAS_TOKEN>';  
   ```

1. U kunt ook de opdracht **Backup Data Base** als volgt voorbereiden om een back-upbestand te maken in de BLOB-container.  Nadat u de waarden hebt vervangen, voert u de query uit.

   ```sql
   BACKUP DATABASE <database name> TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>'
   ```

1. Open Azure Storage Explorer en controleer of het back-upbestand dat u in de vorige stap hebt gemaakt, zichtbaar is in de BLOB-container

### <a name="step-4-restore-the-database-from-azure-blob-storage-to-sql-managed-instance---azure-arc"></a>Stap 4: de data base terugzetten van Azure Blob-opslag naar een SQL-beheerd exemplaar-Azure-boog

1. Meld u aan bij Azure Data Studio en maak verbinding met het SQL Managed instance-Azure Arc.
1. Vouw de **systeem databases** uit, klik met de rechter muisknop op de **hoofd** database en selecteer **nieuwe query**.
1. Bereid en voer in het venster query-editor dezelfde query uit de vorige stap uit om de referenties te maken.

   ```sql
   IF NOT EXISTS  
   (SELECT * FROM sys.credentials
   WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
   CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
     WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
     SECRET = '<SAS_TOKEN>';  
   ```

1. Bereid en voer de onderstaande opdracht uit om te controleren of het back-upbestand leesbaar is en intact is.

   ```console
   RESTORE FILELISTONLY FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>.bak'
   ```

1. Bereid en voer de opdracht **Data Base terugzetten** als volgt uit om het back-upbestand terug te zetten naar een Data Base op een SQL Managed instance-Azure Arc

   ```sql
   RESTORE DATABASE <database name> FROM URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>/<file name>'
   WITH MOVE 'Test' to '/var/opt/mssql/data/<file name>.mdf'
   ,MOVE 'Test_log' to '/var/opt/mssql/data/<file name>.ldf'
   ,RECOVERY  
   ,REPLACE  
   ,STATS = 5;  
   GO
   ```

Meer informatie over back-up naar URL:

[Back-up naar URL-documenten](/sql/relational-databases/backup-restore/sql-server-backup-to-url)

[Back-up naar URL met behulp van SQL Server Management Studio (SSMS)](/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service)

-------

## <a name="method-2-copy-the-backup-file-into-an-azure-sql-managed-instance---azure-arc-pod-using-kubectl"></a>Methode 2: Kopieer het back-upbestand naar een Azure SQL Managed instance-Azure Arc Pod met kubectl

Deze methode laat zien hoe u een back-upbestand maakt dat u via een wille keurige methode hebt gemaakt en dit vervolgens kopieert naar een lokale opslag in de Azure SQL Managed instance Pod, zodat u op een typisch bestands systeem op Windows of Linux kunt herstellen. In dit scenario gebruikt u de opdracht `kubectl cp` om het bestand te kopiëren van de ene locatie naar het bestands systeem van de pod.

### <a name="prerequisites"></a>Vereisten

- Installeer en configureer kubectl om te verwijzen naar uw Kubernetes-cluster waarop Azure Arc Data Services is geïmplementeerd
- Laat een hulp programma zoals Azure Data Studio of SQL Server-beheer server geïnstalleerd en verbonden zijn met de SQL Server waar u het back-upbestand wilt maken of dat er al een bestaand. bak-bestand op uw lokale bestands systeem is gemaakt.

### <a name="step-1-backup-the-database-if-you-havent-already"></a>Stap 1: back-up maken van de Data Base als u deze nog niet hebt

Back-up maken van de SQL Server-Data Base naar het lokale bestandspad zoals gebruikelijk SQL Server back-up naar schijf:

 ```sql
BACKUP DATABASE Test
TO DISK = 'c:\tmp\test.bak'
WITH FORMAT, MEDIANAME = 'Test’ ;
GO
```

### <a name="step-2-copy-the-backup-file-into-the-pods-file-system"></a>Stap 2: Kopieer het back-upbestand naar het bestands systeem van de pod

Zoek de naam van de pod waarin het SQL-exemplaar wordt geïmplementeerd. Normaal gesp roken moet het er als volgt uitzien `pod/<sqlinstancename>-0`

Haal de lijst met alle peulen door uit:

 ```console
kubectl get pods -n <namespace of data controller>
```

Voorbeeld:

Kopieer het back-upbestand van de lokale opslag naar het SQL-pod in het cluster.

 ```console
kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>

#Example:
kubectl cp C:\Backupfiles\test.bak sqlinstance1-0:var/opt/mssql/data/test.bak -n arc
```

### <a name="step-3-restore-the-database"></a>Stap 3: de data base herstellen

Bereid en voer de herstel opdracht uit om het back-upbestand te herstellen naar het beheerde Azure SQL-exemplaar-Azure-boog

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```

Voorbeeld:

```sql
RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/test.bak'
WITH MOVE 'test' to '/var/opt/mssql/data/test.mdf'  
,MOVE 'test' to '/var/opt/mssql/data/test_log.ldf'  
,RECOVERY  
,REPLACE  
,STATS = 5;  
GO
```


## <a name="next-steps"></a>Volgende stappen

[Meer informatie over functies en mogelijkheden van Azure Arc enabled SQL Managed instance](managed-instance-features.md)

[Begin door een gegevenscontroller aan te maken](create-data-controller.md)

[Hebt u al een gegevens controller gemaakt? Een door Azure Arc ingeschakeld SQL Managed instance maken](create-sql-managed-instance.md)