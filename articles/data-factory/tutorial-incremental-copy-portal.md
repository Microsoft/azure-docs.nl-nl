---
title: Een tabel incrementeel kopiëren met behulp van de Azure-portal
description: In deze zelfstudie maakt u een Azure-gegevensfactory met een pijplijn waarmee deltagegevens uit een tabel in een Azure SQL Database worden geladen naar Azure Blob Storage.
services: data-factory
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 11/09/2020
ms.openlocfilehash: d1a7f47b1cdccb02952bd7d9d333855f5eec27d5
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508523"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-the-azure-portal"></a>Incrementeel gegevens uit een Azure SQL-database laden in Azure Blob Storage met de Azure-portal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie maakt u een Azure-gegevensfactory met een pijplijn waarmee deltagegevens uit een tabel in een Azure SQL Database worden geladen naar Azure Blob Storage.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Bereid de gegevensopslag voor om de grenswaarde in op te slaan.
> * Een data factory maken.
> * Maak gekoppelde services.
> * Bron-, sink- en grenswaardegegevenssets maken.
> * Maak een pijplijn.
> * Voer de pijplijn uit.
> * De pijplijnuitvoering controleert.
> * Resultaat controleren
> * Voeg meer gegevens toe aan de bron.
> * Voer de pijplijn opnieuw uit.
> * Controleer de tweede pijplijnuitvoering.
> * Bekijk de resultaten van de tweede uitvoering.


## <a name="overview"></a>Overzicht
Hier volgt de diagramoplossing op hoog niveau:

![Stapsgewijs gegevens laden](media/tutorial-Incremental-copy-portal/incrementally-load.png)

Dit zijn de belangrijke stappen voor het maken van deze oplossing:

1. **Selecteer de grenswaardekolom**.
    Selecteer één kolom in de brongegevensopslag die kan worden gebruikt om de nieuwe of bijgewerkte records voor elke uitvoering te segmenteren. Normaal gesproken nemen de gegevens in deze geselecteerde kolom (bijvoorbeeld, last_modify_time of id) toe wanneer de rijen worden gemaakt of bijgewerkt. De maximale waarde in deze kolom wordt gebruikt als grenswaarde.

2. **Bereid een gegevensopslag voor om de grenswaarde in op te slaan**. In deze zelfstudie slaat u de grenswaarde op in een SQL-database.

3. **Maak een pijplijn met de volgende werkstroom**:

    De pijplijn in deze oplossing heeft de volgende activiteiten:

    * Maak twee opzoekactiviteiten. Gebruik de eerste opzoekactiviteit om de laatste grenswaarde op te halen. Gebruik de tweede opzoekactiviteit om de nieuwe grenswaarde op te halen. Deze grenswaarden worden doorgegeven aan de kopieeractiviteit.
    * Maak een kopieeractiviteit waarmee rijen uit de brongegevensopslag worden gekopieerd met een waarde uit de grenswaardekolom die groter is dan de oude grenswaarde en kleiner dan de nieuwe grenswaarde. Vervolgens worden de deltagegevens uit de brongegevensopslag als een nieuw bestand gekopieerd naar een Blob-opslag.
    * Maak een opgeslagen-procedureactiviteit waarmee de grenswaarde wordt bijgewerkt voor de pijplijn die de volgende keer wordt uitgevoerd.


Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten
* **Azure SQL-database**. U gebruikt de database als de brongegevensopslag. Als u geen database in Azure SQL Database hebt, raadpleegt u het artikel [Een database in Azure SQL Database maken](../azure-sql/database/single-database-create-quickstart.md) om er een te maken.
* **Azure Storage**. U gebruikt de Blob-opslag als de sinkgegevensopslag. Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken. Maak een container met de naam adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Een gegevensbrontabel maken in uw SQL-database
1. Open SQL Server Management Studio. Klik in **Server Explorer** met de rechtermuisknop op de database en kies **Nieuwe query**.

2. Voer de volgende SQL-opdracht uit voor de SQL-database om een tabel met de naam `data_source_table` te maken als de gegevensbronopslag:

    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    In deze zelfstudie gebruikt u LastModifytime als de grenswaardekolom. De gegevens in de brongegevensopslag worden weergegeven in de volgende tabel:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Nog een tabel in uw SQL-database maken om de bovengrenswaarde op te slaan

1. Voer de volgende SQL-opdracht uit op de SQL-database om een tabel met de naam `watermarktable` te maken om de grenswaarde op te slaan:  

    ```sql
    create table watermarktable
    (

    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Stel de standaardwaarde van de bovengrens in met de tabelnaam van de brongegevensopslag. In deze zelfstudie is de tabelnaam data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Controleer de gegevens in de tabel `watermarktable`.

    ```sql
    Select * from watermarktable
    ```
    Uitvoer:

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Een opgeslagen procedure maken in uw SQL-database

Voer de volgende opdracht uit om een opgeslagen procedure te maken in uw SQL-database:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime
WHERE [TableName] = @TableName

END
```

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
2. Selecteer in het linkermenu **Een resource maken** > **Integratie** > **Data Factory**:

   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Voer op de pagina **Nieuwe gegevensfactory** **ADFTutorialBulkCopyDF** in als de **naam**.

   De naam van de Azure-gegevensfactory moet **wereldwijd uniek** zijn. Als u een rood uitroepteken ziet met het volgende foutbericht, wijzigt u de naam van de gegevensfactory (bijvoorbeeld uwnaamADFIncCopyTutorialDF) en probeert u het opnieuw. Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

    *Data factory-naam 'ADFIncCopyTutorialDF' is niet beschikbaar*
4. Selecteer het Azure-**abonnement** waarin u de gegevensfactory wilt maken.
5. Voer een van de volgende stappen uit voor de **Resourcegroep**:

      - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.
      - Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.   
         
        Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
6. Selecteer **V2** als de **versie**.
7. Selecteer de **locatie** voor de gegevensfactory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (Azure Storage, Azure SQL Database, Azure SQL Managed Instance enzovoort) en rekenprocessen (HDInsight enzovoort) die worden gebruikt in data factory, kunnen zich in andere regio's bevinden.
8. Klik op **Create**.      
9. Na het aanmaken ziet u de pagina **Data Factory** zoals weergegeven in de afbeelding.

   ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)
10. Klik op de tegel **Author & Monitor** om de gebruikersinterface (UI) van Azure Data Factory te openen in een afzonderlijk tabblad.

## <a name="create-a-pipeline"></a>Een pijplijn maken
In deze zelfstudie maakt u een pijplijn met twee opzoekactiviteiten, één kopieeractiviteit en één opgeslagen procedureactiviteit als keten in één pijplijn.

1. Klik op de pagina **Aan de slag** van de Data Factory-UI op de tegel **Pijplijn maken**.

   ![Pagina Aan de slag van de Data Factory-UI](./media/doc-common-process/get-started-page.png)    
3. Geef op het tabblad Algemeen bij **Eigenschappen** **IncrementalCopyPipeline** op als **Naam**. Vouw het paneel vervolgens samen door in de rechterbovenhoek op het pictogram Eigenschappen te klikken.

4. We gaan de eerste opzoekactiviteit gebruiken om de oudste grenswaarde op te halen. Vouw in de **Activiteiten**-werkset de optie **Algemeen** uit. Gebruik vervolgens slepen-en-neerzetten om de **opzoekactiviteit** te verplaatsen naar het ontwerpoppervlak voor pijplijnen. Wijzig de naam van de activiteit in **LookupOldWaterMarkActivity**.

   ![Eerst opzoekactiviteit - naam](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Ga naar het tabblad **Instellingen** en klik op **+ Nieuw** voor **Brongegevensset**. In deze stap maakt u een gegevensset die de gegevens in de **grenswaardetabel** vertegenwoordigt. Deze tabel bevat de oude grenswaarde die is gebruikt in de vorige kopieerbewerking.

6. Selecteer in het venster **Nieuwe gegevensset** de optie **Azure SQL Database** en klik op **Doorgaan**. Er wordt nu een nieuw venster geopend voor de gegevensset.

7. Voer in het venster **Eigenschappen instellen** voor de gegevensset **WatermarkDataset** in als **Naam**.

8. Selecteer **Nieuw** voor **Gekoppelde service** en voer de volgende stappen uit:

    1. Voer **AzureSqlDatabaseLinkedService** in als **Naam**.
    2. Selecteer uw server als **Servernaam**.
    3. Selecteer de naam van uw database in de vervolgkeuzelijst bij **Databasenaam**.
    4. Voer uw **Gebruikersnaam** & **Wachtwoord** in.
    5. Als u de verbinding met uw SQL-database wilt testen, klikt u op **Verbinding testen**.
    6. Klik op **Voltooien**.
    7. Controleer of **AzureSqlDatabaseLinkedService** is geselecteerd als **Gekoppelde service**.

        ![Venster Nieuwe gekoppelde service](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
    8. Selecteer **Finish**.
9. Selecteer op het tabblad **Verbinding** **[dbo].[watermarktable]** voor **Tabel**. Klik op **Gegevens vooraf bekijken** om een voorbeeld van de gegevens in de tabel te bekijken.

    ![Grenswaardegegevensset - verbindingsinstellingen](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
10. Ga naar de pijplijneditor door op het pijplijntabblad bovenaan te klikken of door in de structuurweergave aan de linkerkant op de naam van de pijplijn te klikken. Bevestig in het venster Eigenschappen voor de **opzoekactiviteit** dat **WatermarkDataset** is geselecteerd in het veld **Brongegevensset**.

11. Vouw in de **Activiteiten**-werkset de optie **Algemeen** uit. Gebruik vervolgens slepen-en-neerzetten om nog een **opzoekactiviteit** te verplaatsen naar het ontwerpoppervlak voor pijplijnen. Stel de naam op het tabblad **Algemeen** in het venster Eigenschappen in op **LookupNewWaterMarkActivity**. Met deze opzoekactiviteit wordt de nieuwe grenswaarde opgehaald uit de tabel met de brongegevens die moeten worden gekopieerd naar de bestemming.

12. Ga in het venster Eigenschappen voor de tweede **opzoekactiviteit** naar het tabblad **Instellingen** en klik op **Nieuw**. U maakt een gegevensset om te verwijzen naar de brontabel met de nieuwe grenswaarde (maximumwaarde van LastModifyTime).

13. Selecteer in het venster **Nieuwe gegevensset** de optie **Azure SQL Database** en klik op **Doorgaan**.
14. Voer in het venster **Eigenschappen instellen** **SourceDataset** in als **Naam**. Selecteer **AzureSqlDatabaseLinkedService** als **Gekoppelde service**.
15. Selecteer **[dbo].[data_source_table]** als Tabel. Verderop in de zelfstudie geeft u een query op voor deze gegevensset. De query heeft voorrang op de tabel die u in deze stap opgeeft.
16. Selecteer **Finish**.
17. Ga naar de pijplijneditor door op het pijplijntabblad bovenaan te klikken of door in de structuurweergave aan de linkerkant op de naam van de pijplijn te klikken. Bevestig in het venster Eigenschappen voor de **opzoekactiviteit** dat **SourceDataset** is geselecteerd in het veld **Brongegevensset**.
18. Selecteer **Query** in het veld **Query gebruiken** en voer de volgende query in: u selecteert alleen de maximumwaarde van **LastModifytime** uit **data_source_table**. Zorg ervoor dat u ook **Alleen eerste rij** hebt geselecteerd.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Tweede opzoekactiviteit - query](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. Vouw in de **Activiteiten**-werkset de optie **Verplaatsen en transformeren** uit. Gebruik vervolgens slepen-en-neerzetten om de **kopieeractiviteit** uit de Activiteiten-werkset te verplaatsen, en stel de naam in op **IncrementalCopyActivity**.

20. **Verbind beide opzoekactiviteiten met de kopieeractiviteit** door de **groene knop** die is gekoppeld aan de opzoekactiviteiten, naar de kopieeractiviteit te slepen. Laat de muisknop los als u ziet dat de randkleur van de kopieeractiviteit is gewijzigd in blauw.

    ![Opzoekactiviteiten verbinden met kopieeractiviteit](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Selecteer de **kopieeractiviteit** en controleer of de eigenschappen voor de activiteit worden weergegeven in het venster **Eigenschappen**.

22. Ga naar het tabblad **Bron** in het venster **Eigenschappen** en voer de volgende stappen uit:

    1. Selecteer **SourceDataset** in het veld **Brongegevensset**.
    2. Selecteer **Query** in het veld **Query gebruiken**.
    3. Voer de volgende SQL-query in het veld **Query** in.

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```

        ![Kopieeractiviteit - bron](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Ga naar het tabblad **Sink** en klik op **+ Nieuw** voor het veld **Sink-gegevensset**.

24. In deze zelfstudie is het sink-gegevensexemplaar van het type Azure Blob-opslag. Selecteer daarom **Azure Blob-opslag** en klik in het venster **Nieuwe gegevensset** op **Doorgaan**.
25. Selecteer in het venster **Indeling selecteren** de indeling van uw gegevens en klik op **Doorgaan**.
25. Voer in het venster **Eigenschappen instellen** **SinkDataset** in als **Naam**. Selecteer **+ Nieuw** bij **Gekoppelde service**. In deze stap maakt u een verbinding (gekoppelde service) voor uw **Azure Blob-opslag**.
26. Voer de volgende stappen uit in het venster **Nieuwe gekoppelde service (Azure Blob Storage)** :

    1. Voer **AzureStorageLinkedService** in als **Naam**.
    2. Selecteer uw Azure Storage-account bij **Storage account name**.
    3. Test de verbinding en klik vervolgens op **Voltooien**.

27. Controleer of in het venster **Eigenschappen instellen** **AzureStorageLinkedService** is geselecteerd bij **Gekoppelde service**. Selecteer vervolgens **Voltooien**.
28. Ga naar het tabblad **Verbinding** van SinkDataset en voer de volgende stappen uit:
    1. Voer **adftutorial/incrementalcopy** in in het veld **Bestandspad**. **adftutorial** is de naam van de blob-container en **incrementalcopy** is de naam van de map. In dit fragment wordt ervan uitgegaan dat u een blobcontainer hebt met de naam adftutorial in uw Blob-opslag. Maak de container als deze bestaat niet of stel deze in op de naam van een bestaande container. Als de uitvoermap **incrementalcopy** niet bestaat, wordt deze automatisch gemaakt in Azure Data Factory. U kunt ook de knop **Bladeren** voor het **bestandspad** gebruiken om naar een map in een blob-container te navigeren.
    2. Voor het gedeelte **Bestand** van het veld **Bestandspad** selecteert u **Dynamische inhoud toevoegen [Alt+P]** en typt u vervolgens `@CONCAT('Incremental-', pipeline().RunId, '.txt')` in het venster dat wordt geopend. Selecteer vervolgens **Voltooien**. De bestandsnaam wordt dynamisch gegenereerd met behulp van de expressie. Elke pijplijnuitvoering heeft een unieke id. De kopieeractiviteit gebruikt de run-id om de bestandsnaam te genereren.

28. Ga naar de **pijplijneditor** door op het pijplijntabblad bovenaan te klikken of door in de structuurweergave aan de linkerkant op de naam van de pijplijn te klikken.
29. Vouw in de **Activiteiten**-werkset de optie **Algemeen** uit. Gebruik vervolgens slepen-en-neerzetten om de **opgeslagen-procedureactiviteit** uit de **Activiteiten**-werkset te verplaatsen naar het ontwerpoppervlak voor pijplijnen. **Verbind** de groene uitvoer (geslaagd) van de **kopieeractiviteit** met de **opgeslagen-procedureactiviteit**.

24. Selecteer **Opgeslagen procedureactiviteit** in de pijplijnontwerper en verander de naam ervan in **StoredProceduretoWriteWatermarkActivity**.

25. Ga naar het tabblad **SQL-account** en selecteer **AzureSqlDatabaseLinkedService** als **Gekoppelde service**.

26. Ga naar het tabblad **Opgeslagen procedure** en voer de volgende stappen uit:

    1. Selecteer **usp_write_watermark** als naam van de **opgeslagen procedure**.
    2. Als u waarden wilt opgeven voor de opgeslagen-procedureparameters, klikt u op **Importparameter** en voert u de volgende waarden voor de parameters in:

        | Naam | Type | Waarde |
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | Tekenreeks | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

        ![Opgeslagen-procedureactiviteit - instellingen voor de opgeslagen procedure](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. Klik in de werkbalk op **Valideren** om de instellingen voor de pijplijn te valideren. Controleer of er geen validatiefouten zijn. Klik om >> om het venster **Pijplijnvalidatierapport** te sluiten.   

28. Publiceer entiteiten (gekoppelde services, gegevenssets en pijplijnen) naar de Azure Data Factory-service door op de knop **Alles publiceren** te klikken. Wacht tot u een bericht ziet waarin staat dat het publiceren is voltooid.


## <a name="trigger-a-pipeline-run"></a>Een pijplijnuitvoering activeren
1. Klik in de werkbalk op **Trigger toevoegen** en klik op **Nu activeren**.

2. Selecteer in het venster **Pijplijnuitvoering** de optie **Voltooien**.

## <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet de status van de pijplijnuitvoering die is geactiveerd met een handmatige trigger. U kunt via koppelingen in de kolom **NAAM PIJPLIJN** uitvoeringsdetails bekijken en de pijplijn opnieuw uitvoeren.

2. Selecteer de koppeling in de kolom **NAAM PIJPLIJN** om de uitvoering van activiteiten te zien die zijn gekoppeld aan de pijplijnuitvoering. Selecteer de koppeling **Details** (pictogram van een bril) in de kolom **NAAM ACTIVITEIT** om details van de uitvoeringen van een activiteit te zien. Selecteer **Alle pijplijnuitvoeringen** bovenaan om terug te gaan naar de weergave Pijplijnuitvoeringen. Selecteer **Vernieuwen** om de weergave te vernieuwen.


## <a name="review-the-results"></a>De resultaten bekijken
1. Maak verbinding met uw Azure Storage-account met behulp van hulpprogramma's zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Controleer of er een uitvoerbestand is gemaakt in de map **incrementalcopy** in de container **adftutorial**.

    ![Eerste uitvoerbestand](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Als u het uitvoerbestand opent, ziet u dat alle gegevens uit **data_source_table** zijn gekopieerd naar het blob-bestand.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Controleer de laatste waarde van `watermarktable`. U ziet dat de grenswaarde is bijgewerkt.

    ```sql
    Select * from watermarktable
    ```

    Dit is de uitvoer:

    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 |

## <a name="add-more-data-to-source"></a>Meer gegevens toevoegen aan de bron

Voeg nieuwe gegevens in uw database (brongegevensopslag) in.

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
```

De bijgewerkte gegevens in uw database zijn:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```

## <a name="trigger-another-pipeline-run"></a>Een andere pijplijnuitvoering activeren

1. Ga naar het tabblad **Bewerken**. Klik op de pijplijn in de structuurweergave als deze niet is geopend in de ontwerper.

2. Klik in de werkbalk op **Trigger toevoegen** en klik op **Nu activeren**.


## <a name="monitor-the-second-pipeline-run"></a>Controleer de tweede pijplijnuitvoering.

1. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet de status van de pijplijnuitvoering die is geactiveerd met een handmatige trigger. U kunt via koppelingen in de kolom **NAAM PIJPLIJN** details van activiteiten bekijken en de pijplijn opnieuw uitvoeren.

2. Selecteer de koppeling in de kolom **NAAM PIJPLIJN** om de uitvoering van activiteiten te zien die zijn gekoppeld aan de pijplijnuitvoering. Selecteer de koppeling **Details** (pictogram van een bril) in de kolom **NAAM ACTIVITEIT** om details van de uitvoeringen van een activiteit te zien. Selecteer **Alle pijplijnuitvoeringen** bovenaan om terug te gaan naar de weergave Pijplijnuitvoeringen. Selecteer **Vernieuwen** om de weergave te vernieuwen.


## <a name="verify-the-second-output"></a>De tweede uitvoer controleren

1. In de Blob-opslag ziet u dat een ander bestand is gemaakt. In deze zelfstudie is de nieuwe bestandsnaam `Incremental-<GUID>.txt`. Als u dit bestand opent, ziet u twee rijen met records.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Controleer de laatste waarde van `watermarktable`. U ziet dat de grenswaarde opnieuw is bijgewerkt.

    ```sql
    Select * from watermarktable
    ```
    voorbeelduitvoer:

    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |



## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u de volgende stappen uitgevoerd:

> [!div class="checklist"]
> * Bereid de gegevensopslag voor om de grenswaarde in op te slaan.
> * Een data factory maken.
> * Maak gekoppelde services.
> * Bron-, sink- en grenswaardegegevenssets maken.
> * Maak een pijplijn.
> * Voer de pijplijn uit.
> * De pijplijnuitvoering controleert.
> * Resultaat controleren
> * Voeg meer gegevens toe aan de bron.
> * Voer de pijplijn opnieuw uit.
> * Controleer de tweede pijplijnuitvoering.
> * Bekijk de resultaten van de tweede uitvoering.

In deze zelfstudie heeft de pijplijn gegevens uit één tabel in een SQL-database naar een Blob-opslag gekopieerd. Ga door naar de volgende zelfstudie voor meer informatie over het kopiëren van gegevens uit meerdere tabellen in een SQL Server-database naar SQL Database.

> [!div class="nextstepaction"]
>[Incrementeel gegevens uit meerdere tabellen in SQL Server naar een Azure SQL Database kopiëren](tutorial-incremental-copy-multiple-tables-portal.md)
