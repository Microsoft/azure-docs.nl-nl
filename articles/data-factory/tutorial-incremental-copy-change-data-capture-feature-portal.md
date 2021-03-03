---
title: Incrementeel gegevens kopiëren met change data capture
description: In deze zelfstudie maakt u een Azure Data Factory-pijplijn waarmee wijzigingsgegevens incrementeel uit een tabel in een Azure SQL Managed Instance-database naar Azure Storage worden gekopieerd.
ms.author: nihurt
author: hurtn
ms.service: data-factory
ms.topic: tutorial
ms.date: 02/18/2021
ms.openlocfilehash: a00ec8698b188b8fa87935e498e8cfab3aeab5aa
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724979"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>Incrementeel laden van gegevens vanuit Azure SQL Managed Instance naar Azure Storage met behulp van change data capture (CDC)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In deze zelfstudie maakt u een Azure data factory met een pijplijn die gewijzigde gegevens laadt op basis van informatie over **change data capture (CDC)** in de Azure SQL Managed Instance-brondatabase naar Azure Blob Storage.  

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Voorbereiden van de bron-gegevensopslag
> * Een data factory maken.
> * Maak gekoppelde services.
> * Maak bron- en sinkgegevenssets.
> * De pijplijn maken, fouten erin opsporen en uitvoeren om te controleren op gewijzigde gegevens
> * Gegevens in de brontabel wijzigen
> * De volledige pijplijn voor incrementeel kopiëren voltooien, uitvoeren en bewaken

## <a name="overview"></a>Overzicht
De change data capture-technologie die wordt ondersteund door gegevensarchieven zoals Azure SQL Managed Instance en SQL Server kunnen worden gebruikt voor het identificeren van gewijzigde gegevens.  In deze zelfstudie wordt beschreven hoe u met Azure Data Factory kunt gebruikmaken van SQL change data capture-technologie voor het incrementeel laden van wijzigingsgegevens uit Azure SQL Managed Instance naar Azure Blob Storage.  Zie [Change Data Capture in SQL Server](/sql/relational-databases/track-changes/about-change-data-capture-sql-server) voor meer concrete informatie over SQL change data capture-technologie.

## <a name="end-to-end-workflow"></a>End-to-end werkstroom
Hier zijn de gangbare end-to-end werkstroomstappen voor het incrementeel laden van gegevens met behulp van change data capture-technologie.

> [!NOTE]
> Zowel Azure SQL MI als SQL Server ondersteunen de change data capture-technologie. In deze zelfstudie wordt Azure SQL Managed Instance gebruikt als de gegevensopslagbron. U kunt ook een lokale SQL Server gebruiken.

## <a name="high-level-solution"></a>Oplossingen op hoog niveau
In deze zelfstudie maakt u een pijplijn waarmee de volgende bewerkingen worden uitgevoerd:  

   1. Maak een **opzoekactiviteit** om het aantal gewijzigde records in de SQL Database CDC-tabel te tellen en door te geven aan een IF Condition-activiteit.
   2. Maak een **If Condition** om te controleren of er gewijzigde records zijn. Als dat zo is, roept u de kopieeractiviteit op.
   3. Maak een **Kopieeractiviteit** om de ingevoegde/bijgewerkte/verwijderde gegevens te kopiëren van de CDC-tabel naar Azure Blob Storage.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten
* **Azure SQL Managed Instance-database**. U gebruikt de database als de **brongegevensopslag**. Als u geen Azure SQL Managed Instance-database hebt, raadpleegt u het artikel [Create an Azure SQL Database Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md) (Een Azure SQL Managed Instance-database maken) om een database te maken.
* **Een Azure Storage-account**. U gebruikt de Blob-opslag als de **sinkgegevensopslag**. Als u geen Azure-opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) om een account te maken. Maak een container met de naam **raw**. 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Een gegevensbrontabel maken in Azure SQL Database

1. Start **SQL Server Management Studio** en maak verbinding met uw Azure SQL Managed Instance-server.
2. Klik in **Server Explorer** met de rechtermuisknop op de **database** en kies de **Nieuwe query**.
3. Voer de volgende SQL-opdracht uit voor de Azure SQL Managed Instance-database om een tabel met de naam `customers` te maken als gegevensbronopslag.  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. Schakel **Change data capture** in uw database en de brontabel (customers) in door de volgende SQL-query uit te voeren:

    > [!NOTE]
    > - Vervang &lt;de naam van het bronschema&gt; door het schema van Azure SQL MI met de tabel 'customers'.
    > - Change data capture doet niets als onderdeel van de transacties die de getraceerde tabel wijzigen. In plaats daarvan worden de invoeg-, update- en verwijderbewerkingen naar het transactielogboek geschreven. Gegevens die in wijzigingstabellen worden neergezet, worden onbeheerbaar als u de gegevens niet regelmatig verwijdert. Zie voor meer informatie [Enable Change Data Capture for a database](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server#enable-change-data-capture-for-a-database) (Change data capture voor een database inschakelen)

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. Voeg gegevens in de tabel 'customers' in door de volgende opdracht uit te voeren:

    ```sql
     insert into customers 
         (customer_id, first_name, last_name, email, city) 
     values 
         (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
         (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > Er worden geen historische wijzigingen van de tabel vastgelegd voordat change data capture wordt ingeschakeld.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Selecteer in het linkermenu **Een resource maken** > **Gegevens en analyses** > **Data factory**:

   ![Selectie van Data Factory in het deelvenster Nieuw](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png)

2. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in als **naam**.

     ![De pagina Nieuwe data factory](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png)

   De naam van de Azure-gegevensfactory moet **wereldwijd uniek** zijn. Als u het volgende foutbericht krijgt, wijzigt u de naam van de gegevensfactory (bijvoorbeeld uwnaamADFTutorialDataFactory) en probeert u het opnieuw. Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

    *De naam van de gegevensfactory "ADFTutorialDataFactory" is niet beschikbaar.*
3. Selecteer **V2** als de **versie**.
4. Selecteer het Azure-**abonnement** waarin u de gegevensfactory wilt maken.
5. Voer een van de volgende stappen uit voor de **Resourcegroep**:

   1. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.
   2. Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.   
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
5. Selecteer de **locatie** voor de gegevensfactory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.
6. Maak de selectie van **GIT inschakelen** ongedaan.     
7. Klik op **Create**.
8. Zodra de implementatie is voltooid, klikt u op **Ga naar resource**

   ![Schermopname toont een bericht dat uw implementatie is voltooid en een optie om naar de resource te gaan.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png)
9. Na het aanmaken ziet u de pagina **Data Factory** zoals weergegeven in de afbeelding.

   ![Schermopname toont de data factory die u hebt geïmplementeerd.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-home-page.png)
10. Klik op de tegel **Author & Monitor** om de gebruikersinterface (UI) van Azure Data Factory te openen in een afzonderlijk tabblad.
11. Ga op de pagina **get started** naar het tabblad **Edit** in het linkervenster, zoals wordt weergegeven in de volgende afbeelding:

    ![Knop Pijplijn maken](./media/tutorial-incremental-copy-change-data-capture-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Gekoppelde services maken
U maakt gekoppelde services in een gegevensfactory om uw gegevensarchieven en compute-services aan de gegevensfactory te koppelen. In deze sectie maakt u gekoppelde services in het Azure Storage-account en de Azure SQL MI.

### <a name="create-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maakt.
Tijdens deze stap koppelt u uw Azure Storage-account aan de data factory.

1. Klik op **Connections** en klik op **+ New**.

   ![Knop Nieuwe verbinding](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png)
2. In het venster **New Linked Service** selecteert u **Azure Blob Storage** en klikt u op **Continue**.

   ![Azure Blob Storage selecteren](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png)
3. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit:

   1. Voer **AzureStorageLinkedService** in als **Naam**.
   2. Selecteer uw Azure Storage-account bij **Storage account name**.
   3. Klik op **Opslaan**.

   ![Instellingen Azure Storage-account](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-mi-database-linked-service"></a>Maak een gekoppelde Azure SQL MI-databaseservice.
In deze stap koppelt u uw Azure SQL MI-database aan de data factory.

> [!NOTE]
> [Hier](./connector-azure-sql-managed-instance.md#prerequisites) vindt u informatie over toegang via openbare en privé-eindpunten voor SQL MI-gebruikers. Als u een privé-eindpunt gebruikt, moet u deze pijplijn uitvoeren met een zelf-hostende Information Runtime. Hetzelfde geldt voor degenen die SQL Server on-premises gebruiken, in een VM- of VNet-scenario.

1. Klik op **Connections** en klik op **+ New**.
2. In het venster **Nieuwe gekoppelde service** selecteert u **Azure SQL Database Managed Instance** en klikt u op **Doorgaan**.
3. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit:

   1. Voer **AzureSqlMI1** in het veld **Naam** in.
   2. Selecteer uw SQL-server in het veld **Servernaam**.
   4. Selecteer uw SQL-database in het veld **Databasenaam**.
   5. Voer de gebruikersnaam in bij **Gebruikersnaam**.
   6. Voer het wachtwoord voor de gebruiker in bij **Wachtwoord**.
   7. Als u de verbinding wilt testen, klikt u op **Verbinding testen**.
   8. Klik op **Opslaan** om de gekoppelde service op te slaan.

   ![Instellingen voor gekoppelde Azure SQL MI-databaseservice](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png)

## <a name="create-datasets"></a>Gegevenssets maken
In deze stap maakt u gegevenssets die voor de gegevensbron en -bestemming staan.

### <a name="create-a-dataset-to-represent-source-data"></a>Een gegevensset maken om brongegevens weer te geven
In deze stap maakt u een gegevensset die de brongegevens vertegenwoordigt.

1. Klik in de structuurweergave op **+ (plus)** en klik op **Dataset**.

   ![Menu Nieuwe gegevensset](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Selecteer **Azure SQL Database Managed Instance** en klik op **Doorgaan**.

   ![Type brongegevensset - Azure SQL Database](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png)
   
3. Stel op het tabblad **Eigenschappen instellen** de naam van de gegevensset en de verbindingsgegevens in:
 
   1. Selecteer **AzureSqlMI1** bij **Gekoppelde service**.
   2. Selecteer **[dbo].[dbo_customers_CT]** bij **Tabelnaam**.  Opmerking: deze tabel is automatisch gemaakt toen CDC werd ingeschakeld voor de tabel 'customers'. Gewijzigde gegevens worden nooit rechtstreeks vanuit deze tabel opgevraagd, maar worden in plaats daarvan geëxtraheerd via de [CDC-functies](/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql).

   ![Bronverbinding](./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Maak een gegevensset om gegevens weer te geven die zijn gekopieerd naar het sink-gegevensarchief.
In deze stap maakt u een gegevensset die de gegevens voorstelt die worden gekopieerd uit de gegevensopslag van de bron. U hebt de data lake-container gemaakt in uw Azure-blobopslag als onderdeel van de vereisten. Maak de container als deze bestaat niet (of) stel deze in op de naam van een bestaande container. In deze zelfstudie wordt de naam van het uitvoerbestand dynamisch gegenereerd met behulp van de triggertijd, die later wordt geconfigureerd.

1. Klik in de structuurweergave op **+ (plus)** en klik op **Dataset**.

   ![Menu Nieuwe gegevensset](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Selecteer **Azure Blob Storage** en klik op **Doorgaan**.

   ![Gegevenssettype sink - Azure Blob Storage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png)
3. Selecteer **DelimitedText** en klik op **Doorgaan**.

   ![Indeling sinkgegevensset - DelimitedText](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png)
4. Stel op het tabblad **Eigenschappen instellen** de naam van de gegevensset en de verbindingsgegevens in:

   1. Selecteer **AzureStorageLinkedService** bij **Linked service**.
   2. Voer **raw** in voor de tegel **container** van het **filePath**.
   3. Schakel **Eerste rij als koptekst** in
   4. Klik op **OK**.

   ![Sink-gegevensset - verbinding](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png)

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>Een pijplijn maken om de gewijzigde gegevens te kopiëren
In deze stap maakt u een pijplijn, waarmee eerst het aantal gewijzigde records in de wijzigingstabel wordt gecontroleerd met behulp van een **opzoekactiviteit**. Een If Condition-activiteit controleert of het aantal gewijzigde records groter is dan nul en voert een **kopieeractiviteit** uit om de ingevoegde/bijgewerkte/verwijderde gegevens te kopiëren van Azure SQL Database naar Azure Blob Storage. Ten slotte wordt er een tumblingvenstertrigger geconfigureerd en worden de begin- en eindtijden doorgegeven aan de activiteiten als de begin- en eindparameters. 

1. Ga in de Data Factory-gebruikersinterface naar het tabblad **Edit**. Klik op **+ (plus)** in het linkervenster en klik op **Pipeline**.

    ![Menu Nieuwe pijplijn](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png)
2. Er wordt een nieuw tabblad weergegeven voor het configureren van de pijplijn. U ziet ook de pijplijn in de structuurweergave. In het venster **Properties** wijzigt u de naam van de pijplijn in **IncrementalCopyPipeline**.

    ![Naam pijplijn](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png)
3. Vouw in de **Activiteiten**-werkset de optie **Algemeen** uit. Gebruik vervolgens slepen-en-neerzetten om de **opzoekactiviteit** te verplaatsen naar het ontwerpoppervlak voor pijplijnen. Stel de naam van de activiteit in op **GetChangeCount**. Met deze activiteit wordt het aantal records in de wijzigingstabel voor een bepaald tijdvenster opgehaald.

    ![Activiteit Lookup - naam](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png)
4. Ga naar het tabblad **Instellingen** in het venster **Eigenschappen**:
   1. Geef de naam op van de SQL MI-gegevensset voor het veld **Brongegevensset**.
   2. Selecteer de optie Query en voer het volgende in het queryvak in:
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```
   3. Schakel **Alleen eerste rij** in

    ![Activiteit Lookup - instellingen](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png)
5. Klik op de knop **Preview-gegevens** om te controleren of er een geldige uitvoer wordt verkregen door de opzoekactiviteit

    ![Opzoekactiviteit - preview](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png)
6. Vouw in de werkset **Activiteiten** de optie **Iteratie en voorwaarden** uit en sleep de **If Condition**-activiteit naar het ontwerpoppervlak voor pijplijnen. Stel de naam van de activiteit in op **HasChangedRows**. 

    ![If Condition-activiteit - naam](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png)
7. Ga in het venster **Eigenschappen** naar **Activiteiten**:

   1. Voer de volgende **Expressie** in
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. Klik op het potloodpictogram om de True-voorwaarde te bewerken.

   ![If Condition-activiteit - instellingen](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png)

   3. Vouw **Algemeen** uit in de werkset **Activiteiten**. Sleep vervolgens een activiteit **Wachten** naar het ontwerpoppervlak voor pijplijnen. Dit is een tijdelijke activiteit voor het opsporen van fouten in de If Condition en wordt later in de zelfstudie gewijzigd. 

   ![If Condition True - wachten](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png)

   4. Klik op de breadcrumb IncrementalCopyPipeline om terug te keren naar de hoofdpijplijn.

8. Voer de pijplijn uit in de modus **Foutopsporing** om te controleren of de pijplijn wordt uitgevoerd. 

   ![Pijplijn - fouten opsporen](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png)
9. Ga vervolgens terug naar de stap met de True-voorwaarde en verwijder de activiteit **Wachten**. Vouw in de werkset **Activiteiten** de optie **Verplaatsen en transformeren** uit. Sleep vervolgens de **Kopieeractiviteit** naar het ontwerpoppervlak voor pijplijnen. Stel de naam van de activiteit in op **IncrementalCopyActivity**. 

   ![Kopieeractiviteit - naam](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png)
10. Ga naar het tabblad **Bron** in het venster **Eigenschappen** en voer de volgende stappen uit:

   1. Geef de naam op van de SQL MI-gegevensset voor het veld **Brongegevensset**. 
   2. Selecteer **Query** bij **Use Query**.
   3. Voer het volgende in voor **Query**.

      ```sql
      DECLARE @from_lsn binary(10), @to_lsn binary(10); 
      SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
      SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
      SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
      ```

   ![Kopieeractiviteit - broninstellingen](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png)

11. Klik op preview om te controleren of de query de gewijzigde rijen correct retourneert.

    ![Schermopname toont een preview om de query te controleren.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png)
12. Ga naar het tabblad **Sink** en geef de Azure Storage-gegevensset op voor het veld **Sinkgegevensset**.

    ![Schermopname toont het tabblad Sink.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png)
13. Klik om terug te gaan naar het canvas van de hoofdpijplijn en verbind één voor één de activiteit **Opzoeken** met de activiteit **If Condition**. Sleep de **groene** knop die is gekoppeld aan de activiteit **Opzoeken** naar de activiteit **If Condition**.

    ![Lookup- en Copy-activiteiten verbinden](./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png)
14. Klik op **Validate** op de werkbalk. Controleer of er geen validatiefouten zijn. Sluit het venster **Pipeline Validation Report** door op **>>** te klikken.

    ![Knop Valideren](./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png)
15. Klik op Foutopsporing om de pijplijn te testen en te controleren of er een bestand is gegenereerd op de opslaglocatie.

    ![Incrementeel fouten opsporen in pijplijn-2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png)
16. Publiceer entiteiten (gekoppelde services, gegevenssets en pijplijnen) naar de Data Factory-service door te klikken op de knop **Alles publiceren**. Wacht tot u het bericht **Publishing succeeded** ziet.

    ![De knop Publiceren](./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png)    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>De tumblingvenstertrigger en de parameters van het CDC-venster configureren 
In deze stap maakt u een tumblingvenstertrigger om de taak volgens een frequent schema uit te voeren. U gebruikt de systeemvariabelen WindowStart en WindowEnd van de tumblingvenstertrigger en geeft ze door als parameters aan de pijplijn die u wilt gebruiken in de CDC-query.

1. Ga naar het tabblad **Parameters** van de pijplijn **IncrementalCopyPipeline** en gebruik de knop **+ Nieuw** om twee parameters (**triggerStartTime** en **triggerEndTime**) toe te voegen aan de pijplijn. Dit zijn de begin- en eindtijd van het tumblingvenster. Voor foutopsporing voegt u standaardwaarden toe in de indeling **JJJJ-MM-DD HH24: MI: SS. FFF**. Zorg ervoor dat de triggerStartTime niet eerder is dan wanneer CDC op de tabel is ingeschakeld, anders treedt er een fout op.

    ![Menu Trigger Now](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png)
2. Klik op het tabblad Instellingen van de activiteit **Opzoeken** en configureer de query om de begin- en eindparameters te gebruiken. Kopieer het volgende naar de query:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. Ga naar de activiteit **Kopiëren** in het geval dat de activiteit **If Condition** True is en klik op het tabblad **Bron**. Kopieer het volgende naar de query:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. Klik op het tabblad **Sink** van de activiteit **Kopiëren** en klik op **Openen** om de eigenschappen van de gegevensset te bewerken. Klik op het tabblad **Parameters** en voeg een nieuwe parameter toe met de naam **triggerStart**    

    ![Schermopname toont het toevoegen van een nieuwe parameter aan het tabblad Parameters.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png)
5. Configureer vervolgens de eigenschappen van de gegevensset om de gegevens op te slaan in een submap **customers/incremental** met op datums gebaseerde partities.
   1. Klik op het tabblad **Verbinding** van de eigenschappen van de gegevensset en voeg dynamische inhoud toe voor de secties **Map** en **Bestand**. 
   2. Voer de volgende expressie in de sectie **Map** in door te klikken op de link voor dynamische inhoud onder het tekstvak:
    
    ```sql
    @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
    ```
   3. Voer de volgende expressie in het gedeelte **Bestand** in. Hiermee worden bestandsnamen gemaakt op basis van de begindatum en -tijd van de trigger, en krijgen ze een achtervoegsel met de cvs-extensie:
    
    ```sql
    @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
    ```
    ![Sinkgegevensset configureren-3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png)

   4. Ga terug naar de instellingen voor **Sink** in de activiteit **Kopiëren** door te klikken op het tabblad **IncrementalCopyPipeline**. 
   5. Vouw de eigenschappen van de gegevensset uit en voer dynamische inhoud in de parameterwaarde triggerStart in met de volgende expressie:
     ```sql
     @pipeline().parameters.triggerStartTime
     ```
    ![Sinkgegevensset configureren-4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png)

6. Klik op Foutopsporing om de pijplijn te testen en ervoor te zorgen dat de mapstructuur en het uitvoerbestand naar verwachting worden gegenereerd. Download en open het bestand om de inhoud te controleren. 

    ![Fouten opsporen incrementeel kopiëren-3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png)
7. Controleer of de parameters in de query worden geïnjecteerd door de invoerparameters van de pijplijnuitvoering te bekijken.

    ![Fouten opsporen incrementeel kopiëren-4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png)
8. Publiceer entiteiten (gekoppelde services, gegevenssets en pijplijnen) naar de Data Factory-service door te klikken op de knop **Alles publiceren**. Wacht tot u het bericht **Publishing succeeded** ziet.
9. Configureer tot slot een tumblingvenstertrigger om de pijplijn met een regelmatig interval uit te voeren en de begin- en eindparameters in te stellen. 
   1. Klik op de knop **Trigger toevoegen** en selecteer **Nieuw/bewerken**

   ![Nieuwe trigger toevoegen](./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png)

   2. Voer een naam voor de trigger in en geef een begintijd op, die gelijk is aan de eindtijd van het bovenstaande venster Foutopsporing.

   ![Tumblingvenstertrigger](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png)

   3. Geef in het volgende scherm respectievelijk de volgende waarden voor de begin- en eindparameters op.
    ```sql
    @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
    @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
    ```

   ![Tumblingvenstertrigger-2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png)

> [!NOTE]
> Let op: de trigger wordt pas uitgevoerd nadat deze is gepubliceerd. Daarnaast is het verwachte gedrag van het tumblingvenster het uitvoeren van alle historische intervallen vanaf de begindatum tot nu. Meer informatie over de tumblingvenstertriggers vindt u [hier](./how-to-create-tumbling-window-trigger.md). 
  
10. U kunt met behulp van **SQL Server Management Studio** aanvullende wijzigingen aanbrengen in de klantentabel door de volgende SQL-versie uit te voeren:
    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. Klik op de knop **Alles publiceren**. Wacht tot u het bericht **Publishing succeeded** ziet.  
12. Na een paar minuten wordt de pijplijn geactiveerd en wordt er een nieuw bestand geladen in Azure Storage


### <a name="monitor-the-incremental-copy-pipeline"></a>De pijplijn incrementele kopie bewaken
1. Klik op het tabblad **Monitor** aan de linkerkant. U ziet de pijplijnuitvoering in de lijst en de status ervan. Als u de lijst wilt vernieuwen, klikt u op **Refresh**. Beweeg de muisaanwijzer over de naam van de pijplijn om het actie- en verbruiksrapport opnieuw uit te voeren.

    ![Pijplijnuitvoeringen](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png)
2. Als u de uitvoeringen van activiteiten die zijn gekoppeld aan de pijplijnuitvoering wilt weergeven, klikt u op de naam van de pijplijn. Als er gewijzigde gegevens zijn gedetecteerd, worden er drie activiteiten, waaronder de activiteit Kopiëren, weergegeven, anders worden er maar twee weergegeven. Als u wilt terugkeren naar de weergave met pijplijnuitvoeringen, klikt u op de koppeling **Alle pijplijnen** bovenaan.

    ![Uitvoering van activiteiten](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png)


### <a name="review-the-results"></a>De resultaten bekijken
U ziet u het tweede bestand in de `customers/incremental/YYYY/MM/DD` map van de `raw` container.

![Bestand voor uitvoer van een incrementele kopie](media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png)
 

## <a name="next-steps"></a>Volgende stappen
Ga naar de volgende zelfstudie voor meer informatie over het kopiëren van nieuwe en gewijzigde bestanden alleen op basis van hun LastModifiedDate:

> [!div class="nextstepaction"]
>[Nieuwe bestanden kopiëren op basis van lastmodifieddate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
