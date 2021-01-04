---
title: Gegevens synchroniseren vanuit Azure SQL Edge met behulp van Azure Data Factory
description: Lees hier alles over het synchroniseren van gegevens tussen Azure SQL Edge en Azure Blob-opslag
keywords: SQL Edge, gegevens synchroniseren vanuit SQL Edge, SQL Edge-data factory
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: b83201ae864d1f1eb9124af5268360bb1748f6c8
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507605"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Zelfstudie: Gegevens synchroniseren van SQL Edge naar Azure Blob-opslag met behulp van Azure Data Factory

In deze zelfstudie gebruikt u Azure Data Factory om gegevens incrementeel te synchroniseren met Azure Blob-opslag vanuit een tabel in een instantie van Azure SQL Edge.

## <a name="before-you-begin"></a>Voordat u begint

Als u nog geen database of tabel hebt gemaakt in uw implementatie van Azure SQL Edge, gebruikt u een van de volgende methoden om er een te maken:

* Gebruik [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) of [Azure Data Studio](/sql/azure-data-studio/download/) om verbinding te maken met SQL Edge. Voer een SQL-script uit om de database en tabel te maken.
* Maak een database en -tabel met behulp van [SQLCMD](/sql/tools/sqlcmd-utility/) door rechtstreeks verbinding te maken met de SQL Edge-module. Zie [sqlcmd - Connect to the Database Engine](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/) (sqlcmd: verbinding maken met de database-engine) voor meer informatie.
* Gebruik SQLPackage.exe om een DAC-pakketbestand te implementeren in de SQL Edge-container. U kunt dit proces automatiseren door de URI van het SqlPackage-bestand op te geven als onderdeel van de configuratie met de gewenste eigenschappen van de module. U kunt ook rechtstreeks het clienthulpprogramma SqlPackage.exe gebruiken om een DAC-pakket te implementeren in SQL Edge.

    Zie [Download and install sqlpackage](/sql/tools/sqlpackage-download/) (SqlPackage downloaden en installeren) voor meer informatie over het downloaden van SqlPackage.exe. Hier volgen enkele voorbeelden van opdrachten voor SqlPackage.exe. Raadpleeg de documentatie van SqlPackage.exe voor meer informatie.

    **Een DAC-pakket maken**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Een DAC-pakket toepassen**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Een SQL-tabel en -procedure maken om de watermerkniveaus op te slaan en bij te werken

Een watermerktabel wordt gebruikt om een stempel op te slaan met de tijd waarop gegevens voor het laatst zijn gesynchroniseerd met Azure Storage. De watermerktabel wordt na elke synchronisatie bijgewerkt via een opgeslagen Transact-SQL-procedure (T-SQL).

Voer deze opdrachten uit in de instantie van SQL Edge:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp
    WHERE [TableName] = @TableName

    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>Een Data Factory-pijplijn maken

In deze sectie maakt u een Azure Data Factory-pijplijn om gegevens vanuit een tabel in Azure SQL Edge te synchroniseren met Azure Blob-opslag.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Een data factory maken via de gebruikersinterface van Data Factory

Maak een data factory door de instructies in [deze zelfstudie](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) te volgen.

### <a name="create-a-data-factory-pipeline"></a>Een Data Factory-pijplijn maken

1. Selecteer **Pijplijn maken** op de pagina **Aan de slag** in de gebruikersinterface van Data Factory.

    ![Een Data Factory-pijplijn maken](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Voer op de pagina **Algemeen** van het venster **Eigenschappen** voor de pijplijn de naam **PeriodicSync** in.

3. Voeg een opzoekactiviteit toe om de oude watermerkwaarde op te halen. Vouw in het deelvenster **Activiteiten** de optie **Algemeen** uit en sleep de activiteit **Opzoeken** naar het gebied voor het ontwerpen van pijplijnen. Wijzig de naam van de activiteit in **OldWatermark**.

    ![De opzoekactie OldWatermark toevoegen](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Ga naar het tabblad **Instellingen** en selecteer **Nieuw** voor **Brongegevensset**. U gaat nu een gegevensset maken die de gegevens in de watermerktabel vertegenwoordigt. Deze tabel bevat de oude grenswaarde die is gebruikt in de vorige kopieerbewerking.

5. Selecteer in het venster **Nieuwe gegevensset** de optie **Azure SQL Server** en selecteer vervolgens **Doorgaan**.  

6. Ga in het venster **Eigenschappen instellen** voor de gegevensset naar **Naam** en voer **WatermarkDataset** in.

7. Selecteer **Nieuw** voor **Gekoppelde service** en voer deze stappen uit:

    1. Voer **SQLDBEdgeLinkedService** in bij **Naam**.

    2. Voer bij **Servernaam** de gegevens van de SQL Edge-server in.

    3. Selecteer de naam van uw database in de lijst bij **Databasenaam**.

    4. Geef waarden op voor **Gebruikersnaam** en **Wachtwoord**.

    5. Als u de verbinding met de instantie van SQL Edge wilt testen, selecteert u **Verbinding testen**.

    6. Selecteer **Maken**.

    ![Een gekoppelde service maken](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Selecteer **OK**.

8. Selecteer **Bewerken** op het tabblad **Instellingen**.

9. Selecteer op het tabblad **Verbinding** **[dbo].[watermarktable]** voor **Tabel**. Als u eerst een voorbeeld van de gegevens in de tabel wilt bekijken, klikt u op **Gegevens vooraf bekijken**.

10. Ga naar de pijplijneditor door bovenaan op het tabblad Pijplijn te klikken of door de naam van de pijplijn te selecteren in de structuurweergave aan de linkerkant. Controleer of in het eigenschappenvenster voor de opzoekactiviteit **WatermarkDataset** is geselecteerd in de lijst **Brongegevensset**.

11. Vouw in het deelvenster **Activiteiten** de optie **Algemeen** uit en sleep een andere activiteit **Opzoeken** naar het gebied voor het ontwerpen van pijplijnen. Geef de activiteit de naam **NewWatermark** op het tabblad **Algemeen** van het eigenschappenvenster. Met deze opzoekactiviteit wordt de nieuwe watermerkwaarde opgehaald uit de tabel met de brongegevens, zodat deze kan worden gekopieerd naar de bestemming.

12. Ga in het eigenschappenvenster voor de tweede opzoekactiviteit naar het tabblad **Instellingen** en selecteer **Nieuw** om een gegevensset te maken die verwijst naar de brontabel die de nieuwe watermerkwaarde bevat.

13. Selecteer in het venster **Nieuwe gegevensset** de optie **SQL Edge-instantie** en selecteer vervolgens **Doorgaan**.

    1. Voer in het venster **Eigenschappen instellen** onder **Naam** **SourceDataset** in. Selecteer **SQLDBEdgeLinkedService** bij **Gekoppelde service**.

    2. Selecteer bij **Tabel** de tabel die u wilt synchroniseren. U kunt ook een query opgeven voor deze gegevensset, zoals verderop in deze zelfstudie wordt beschreven. De query heeft voorrang op de tabel die u in deze stap opgeeft.

    3. Selecteer **OK**.

14. Ga naar de pijplijneditor door bovenaan op het tabblad Pijplijn te klikken of door de naam van de pijplijn te selecteren in de structuurweergave aan de linkerkant. Controleer of in het eigenschappenvenster voor de opzoekactiviteit **SourceDataset** is geselecteerd in de lijst **Brongegevensset**.

15. Selecteer **Query** bij **Query gebruiken**. Werk de tabelnaam in de volgende query bij en voer vervolgens de query uit. U selecteert alleen de maximumwaarde van `timestamp` in de tabel. Zorg ervoor dat u **Alleen eerste rij** selecteert.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![select-query](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. Vouw in het deelvenster **Activiteiten** de optie **Verplaatsen en transformeren** uit en sleep de activiteit **Kopiëren** van het deelvenster **Activiteiten** naar het ontwerpgebied. Stel de naam van de activiteit in op **IncrementalCopy**.

17. Verbind beide opzoekactiviteiten met de activiteit Kopiëren door de groene knop die is vastgemaakt aan de opzoekactiviteiten naar de activiteit Kopiëren te slepen. Laat de muisknop los als de rand van de activiteit Kopiëren blauw wordt.

18. Selecteer de activiteit Kopiëren en controleer of de eigenschappen voor de activiteit worden weergegeven in het venster **Eigenschappen**.

19. Ga naar het tabblad **Bron** in het venster **Eigenschappen** en voer deze stappen uit:

    1. Selecteer **SourceDataset** in het vak **Brongegevensset**.

    2. Selecteer **Query** bij **Query gebruiken**.

    3. Typ de SQL-query in het vak **Query**. Hier ziet u een voorbeeld:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Selecteer op het tabblad **Sink** de optie **Nieuw** onder **Sink-gegevensset**.

21. In deze zelfstudie is het sink-gegevensarchief een gegevensarchief van Azure Blob Storage. Selecteer **Azure Blob-opslag** en selecteer vervolgens **Doorgaan** in het venster **Nieuwe gegevensset**.

22. Selecteer in het venster **Indeling selecteren** de indeling van uw gegevens en selecteer vervolgens **Doorgaan**.

23. Geef in het venster **Eigenschappen instellen** bij **Naam** de naam **SinkDataset** op. Selecteer **Nieuw** bij **Gekoppelde service**. U gaat nu een verbinding (gekoppelde service) maken met de Azure Blob-opslag.

24. Voer deze stappen uit in het venster **Nieuwe gekoppelde service (Azure Blob-opslag)** :

    1. Geef in het vak **Naam** de naam **AzureStorageLinkedService** op.

    2. Selecteer bij **Naam van opslagaccount** het Azure-opslagaccount voor uw Azure-abonnement.

    3. Test de verbinding en selecteer vervolgens **Voltooien**.

25. Controleer of in het venster **Eigenschappen instellen** **AzureStorageLinkedService** is geselecteerd bij **Gekoppelde service**. Selecteer **Maken** en **OK**.

26. Selecteer **Bewerken** op het tabblad **Sink**.

27. Ga naar het tabblad **Verbinding** van SinkDataset en voer deze stappen uit:

    1. Voer bij **Bestandspad** *asdedatasync/incrementalcopy* in, waarbij *asdedatasync* de naam van de blobcontainer is en *incrementalcopy* de naam van de map. Maak de container als deze niet bestaat of gebruik de naam van een bestaande container. Als de uitvoermap *incrementalcopy* niet bestaat, wordt deze automatisch gemaakt door Azure Data Factory. U kunt ook de knop **Bladeren** voor het **bestandspad** gebruiken om naar een map in een blob-container te navigeren.

    2. Voor het gedeelte **Bestand** van **Bestandspad**, selecteert u **Dynamische inhoud toevoegen [Alt+P]** en typt u vervolgens **@CONCAT('Incremental-', pipeline().RunId, '.txt')** in het venster dat wordt geopend. Selecteer **Finish**. De bestandsnaam wordt dynamisch gegenereerd met behulp van de expressie. Elke pijplijnuitvoering heeft een unieke id. De kopieeractiviteit gebruikt de run-id om de bestandsnaam te genereren.

28. Ga naar de pijplijneditor door bovenaan op het tabblad Pijplijn te klikken of door de naam van de pijplijn te selecteren in de structuurweergave aan de linkerkant.

29. Vouw in het deelvenster **Activiteiten** de optie **Algemeen** uit en sleep de activiteit **Opgeslagen procedure** van het deelvenster **Activiteiten** naar het ontwerpgebied voor pijplijnen. Verbind de groene uitvoer (geslaagd) van de activiteit Kopiëren met de activiteit Opgeslagen procedure.

30. Selecteer **Opgeslagen procedureactiviteit** in de pijplijnontwerper en verander de naam in **SPtoUpdateWatermarkActivity**.

31. Ga naar het tabblad **SQL-account** en selecteer **_QLDBEdgeLinkedService_* bij **Gekoppelde service**.

32. Ga naar het tabblad **Opgeslagen procedure** en voer deze stappen uit:

    1. Selecteer **[dbo].[usp_write_watermark]** onder **Naam opgeslagen procedure**.

    2. Als u waarden wilt opgeven voor de parameters van de opgeslagen procedure, selecteert u **Importparameter** en voert u deze waarden in voor de parameters:

    |Naam|Type|Waarde|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|Tekenreeks|@{activity('OldWaterMark').output.firstRow.TableName}|

33. Klik in de werkbalk op **Valideren** om de instellingen voor de pijplijn te valideren. Controleer of er geen validatiefouten zijn. Selecteer **>>** om het venster **Pijplijnvalidatierapport** te sluiten.

34. Publiceer de entiteiten (gekoppelde services, gegevenssets en pijplijnen) naar de Azure Data Factory-service door de knop **Alles publiceren** te selecteren. Wacht totdat er een bericht verschijnt dat de publicatie is voltooid.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Een pijplijn activeren op basis van een planning

1. Selecteer op de werkbalk van de pijplijn **Trigger toevoegen**, selecteer **Nieuw/bewerken** en selecteer ten slotte **Nieuw**.

2. Geef de trigger de naam **HourlySync**. Selecteer onder **Type** de optie **Planning**. Stel de optie **Herhaling** in op 1 uur.

3. Selecteer **OK**.

4. Selecteer **Alles publiceren**.

5. Selecteer **Nu activeren**.

6. Ga naar het tabblad **Controleren** aan de linkerkant. U kunt de status van de pijplijnuitvoering zien die is geactiveerd met de handmatige trigger. Selecteer **Vernieuwen** om de lijst te vernieuwen.

## <a name="next-steps"></a>Volgende stappen

Met de Azure Data Factory-pijplijn uit deze zelfstudie worden gegevens in een tabel in een SQL Edge-instantie ieder uur gekopieerd naar een locatie in Azure Blob-opslag. Doorloop deze [zelfstudies](../data-factory/tutorial-copy-data-portal.md) voor meer informatie over het gebruiken van Data Factory in andere scenario's.
