---
title: SSIS-pakket uitvoeren met activiteit opgeslagen procedure-Azure
description: In dit artikel wordt beschreven hoe u een SQL Server Integration Services (SSIS)-pakket uitvoert in een Azure Data Factory-pijp lijn met behulp van de opgeslagen procedure activiteit.
author: swinarko
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6260606fe56d4dfc6bac93e04e726b5fd3298777
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100391504"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Een SSIS-pakket uitvoeren met de activiteit Opgeslagen procedure in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u een SSIS-pakket uitvoert in een Azure Data Factory pijp lijn met behulp van een opgeslagen procedure activiteit. 

## <a name="prerequisites"></a>Vereisten

### <a name="azure-sql-database"></a>Azure SQL Database 
In de stapsgewijze instructies in dit artikel wordt gebruikgemaakt van Azure SQL Database voor het hosten van de SSIS-catalogus. U kunt ook Azure SQL Managed instance gebruiken.

## <a name="create-an-azure-ssis-integration-runtime"></a>Een Azure SSIS Integration Runtime maken
Een Azure SSIS Integration runtime maken als u er nog geen hebt door de stapsgewijze instructie in de [zelf studie te volgen: SSIS-pakketten implementeren](./tutorial-deploy-ssis-packages-azure.md).

## <a name="data-factory-ui-azure-portal"></a>Data Factory gebruikers interface (Azure Portal)
In deze sectie gebruikt u Data Factory gebruikers interface om een Data Factory pijp lijn te maken met een opgeslagen procedure activiteit die een SSIS-pakket aanroept.

### <a name="create-a-data-factory"></a>Een gegevensfactory maken
De eerste stap is het maken van een data factory met behulp van de Azure Portal. 

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
2. Navigeer naar [Azure Portal](https://portal.azure.com). 
3. Klik op **Nieuw** in het linkermenu en klik vervolgens op **Gegevens en analyses** en **Data Factory**. 
   
   ![Nieuw -> DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in als **naam**. 
      
     ![De pagina Nieuwe data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   De naam van de Azure-gegevensfactory moet **wereldwijd uniek** zijn. Als het volgende foutbericht wordt weergegeven voor het naamveld, wijzigt u de naam van de data factory (bijvoorbeeld uwnaamADFTutorialDataFactory). Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
  
     ![Naam niet beschikbaar - fout](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Selecteer het Azure-**abonnement** waarin u de gegevensfactory wilt maken. 
4. Voer een van de volgende stappen uit voor de **Resourcegroep**:
     
   - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst. 
   - Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.   
         
     Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
4. Selecteer **V2** als de **versie**.
5. Selecteer de **locatie** voor de gegevensfactory. In de vervolgkeuzelijst ziet u alleen locaties die worden ondersteund in Data Factory. De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich op andere locaties bevinden.
6. Selecteer **Vastmaken aan dashboard**.     
7. Klik op **Create**.
8. Op het dashboard ziet u de volgende tegel met de status: **Gegevensfactory implementeren**. 

     ![tegel met de status 'gegevensfactory implementeren'](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Na het aanmaken ziet u de pagina **Data Factory** zoals weergegeven in de afbeelding.
   
     ![Startpagina van de gegevensfactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Klik op de tegel **Maken en controleren** om de gebruikersinterface (UI) van Azure Data Factory te openen op een afzonderlijk tabblad. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Een pijp lijn maken met een opgeslagen procedure activiteit
In deze stap gebruikt u de Data Factory-gebruikers interface om een pijp lijn te maken. U voegt een opgeslagen procedure activiteit toe aan de pijp lijn en configureert deze voor het uitvoeren van het SSIS-pakket door gebruik te maken van de sp_executesql opgeslagen procedure. 

1. Klik op de pagina aan de slag op **pijp lijn maken**: 

    ![Pagina Aan de slag](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Vouw in de werkset **activiteiten** het item **Algemeen** uit en sleep de activiteit **opgeslagen procedure** naar het ontwerp oppervlak voor pijp lijnen. 

    ![Opgeslagen procedure-activiteit slepen en neerzetten](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. Ga in het venster Eigenschappen voor de activiteit opgeslagen procedure naar het tabblad **SQL-account** en klik op **+ Nieuw**. U maakt een verbinding met de data base in Azure SQL Database die als host fungeert voor de SSIS-catalogus (SSIDB-data base). 
   
    ![Knop Nieuwe gekoppelde service](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit: 

    1. Selecteer **Azure SQL database** voor **type**.
    2. Selecteer de **standaard** Azure Integration runtime om verbinding te maken met de Azure SQL database die als host fungeert voor de- `SSISDB` Data Base.
    3. Selecteer de Azure SQL Database die als host fungeert voor de SSISDB-Data Base voor het veld **Server naam** .
    4. Selecteer **SSISDB** voor de naam van de **Data Base**.
    5. Voer bij **gebruikers naam** de naam in van de gebruiker die toegang heeft tot de data base.
    6. Voer bij **wacht woord** het wacht woord van de gebruiker in. 
    7. Test de verbinding met de data base door te klikken op de knop **verbinding testen** .
    8. Sla de gekoppelde service op door te klikken op de knop **Opslaan** . 

        ![Scherm opname van het proces voor het toevoegen van een nieuwe gekoppelde service.](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. Ga in het venster Eigenschappen naar het tabblad **opgeslagen procedure** van het tabblad **SQL-account** en voer de volgende stappen uit: 

    1. Selecteer **Bewerken**. 
    2. Voer in het veld **opgeslagen procedure naam** in `sp_executesql` . 
    3. Klik op **+ Nieuw** in het gedeelte **para meters van de opgeslagen procedure** . 
    4. Voer **stmt** in bij **naam** van de para meter. 
    5. Voer een **teken reeks** in bij het **type** van de para meter. 
    6. Voer de volgende SQL-query in voor de **waarde** van de para meter:

        Geef in de SQL-query de juiste waarden op voor de para meters **folder_name**, **project_name** en **package_name** . 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Een gekoppelde Azure SQL Database-service](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Klik op **valideren** op de werk balk om de configuratie van de pijp lijn te valideren. Sluit het venster **Pipeline Validation Report** door op **>>** te klikken.

    ![Pijplijn valideren](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Publiceer de pijp lijn naar Data Factory door te klikken op **de knop Alles publiceren** . 

    ![Publiceren](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>De pijp lijn uitvoeren en controleren
In deze sectie gaat u een pijplijn uitvoering activeren en vervolgens controleren. 

1. Als u een pijplijn uitvoering wilt activeren, klikt u op **trigger** op de werk balk en klikt u op **nu activeren**. 

    ![Nu activeren](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. Selecteer in het venster **Pijplijnuitvoering** de optie **Voltooien**. 
3. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet de pijplijn uitvoering en de status ervan samen met andere informatie (zoals run-start tijd). Als u de lijst wilt vernieuwen, klikt u op **Vernieuwen**.

    ![Pijplijnuitvoeringen](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Klik op de koppeling **Uitvoeringen van activiteiten weergeven** in de kolom **Acties**. U ziet dat er slechts één activiteit wordt uitgevoerd, omdat de pijp lijn maar één activiteit heeft (opgeslagen-procedure activiteit).

    ![Uitvoering van activiteiten](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. U kunt de volgende **query** uitvoeren op de SSISDB-data base in SQL database om te controleren of het pakket is uitgevoerd. 

    ```sql
    select * from catalog.executions
    ```

    ![Pakket uitvoeringen controleren](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> U kunt ook een geplande trigger voor uw pijp lijn maken zodat de pijp lijn op een schema wordt uitgevoerd (elk uur, dagelijks, enzovoort). Zie [een Data Factory-Data Factory gebruikers interface maken](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)voor een voor beeld.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze sectie gebruikt u Azure PowerShell om een Data Factory-pijp lijn te maken met een opgeslagen procedure activiteit die een SSIS-pakket aanroept. 

Installeer de nieuwste Azure PowerShell-modules met de instructies in [Azure PowerShell installeren en configureren](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Een gegevensfactory maken
U kunt dezelfde data factory gebruiken die de Azure-SSIS IR heeft of een afzonderlijke data factory maken. De volgende procedure bevat stappen voor het maken van een data factory. In deze data factory maakt u een pijp lijn met een opgeslagen procedure activiteit. Met de activiteit opgeslagen procedure wordt een opgeslagen procedure uitgevoerd in de SSISDB-data base om uw SSIS-pakket uit te voeren. 

1. Definieer een variabele voor de naam van de resourcegroep die u later gaat gebruiken in PowerShell-opdrachten. Kopieer de tekst van de volgende opdracht naar PowerShell, geef tussen dubbele aanhalingstekens een naam op voor de [Azure-resourcegroep](../azure-resource-manager/management/overview.md) en voer de opdracht uit. Bijvoorbeeld: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Als de resourcegroep al bestaat, wilt u waarschijnlijk niet dat deze wordt overschreven. Wijs een andere waarde toe aan de `$ResourceGroupName`-variabele en voer de opdracht opnieuw uit.
2. Voer de volgende opdracht uit om de resourcegroep te maken: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Als de resourcegroep al bestaat, wilt u waarschijnlijk niet dat deze wordt overschreven. Wijs een andere waarde toe aan de `$ResourceGroupName`-variabele en voer de opdracht opnieuw uit. 
3. Definieer een variabele voor de naam van de data factory. 

    > [!IMPORTANT]
    >  Werk de naam van de data factory zodanig bij dat deze uniek is. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Voer de volgende cmdlet **Set-AzDataFactoryV2** uit om de data factory te maken met de eigenschappen Locatie en ResourceGroupName van de variabele $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Houd rekening met de volgende punten:

* De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als de volgende fout zich voordoet, wijzigt u de naam en probeert u het opnieuw.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rollen **Inzender** of **Eigenaar**, of moet dit een **beheerder** van het Azure-abonnement zijn.
* Voor een lijst met Azure-regio's waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio's waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensopslagexemplaren (Azure Storage, Azure SQL Database, enzovoort) en berekeningen (HDInsight, enzovoort) die worden gebruikt in Data Factory, kunnen zich in andere regio's bevinden.

### <a name="create-an-azure-sql-database-linked-service"></a>Een gekoppelde Azure SQL Database-service maken
Maak een gekoppelde service om uw data base die als host fungeert voor de SSIS-catalogus, te koppelen aan uw data factory. Data Factory maakt gebruik van informatie in deze gekoppelde service om verbinding te maken met SSISDB-data base en voert een opgeslagen procedure uit om een SSIS-pakket uit te voeren. 

1. Maak een JSON-bestand met de naam **AzureSqlDatabaseLinkedService.js** in de map **C:\ADF\RunSSISPackage** met de volgende inhoud: 

    > [!IMPORTANT]
    > Vervang &lt; servername &gt; , &lt; username &gt; en &lt; Password door de &gt; waarden van uw Azure SQL database voordat u het bestand opslaat.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. Ga in **Azure PowerShell** naar de map **C:\ADF\RunSSISPackage** .

3. Voer de cmdlet **Set-AzDataFactoryV2LinkedService** uit om de gekoppelde service te maken: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Een pijp lijn maken met een opgeslagen procedure activiteit 
In deze stap maakt u een pijp lijn met een opgeslagen procedure activiteit. Met de activiteit wordt de sp_executesql opgeslagen procedure aangeroepen om uw SSIS-pakket uit te voeren. 

1. Maak een JSON-bestand met de naam **RunSSISPackagePipeline.js** in de map **C:\ADF\RunSSISPackage** met de volgende inhoud:

    > [!IMPORTANT]
    > Vervang &lt; de mapnaam &gt; , &lt; de project naam &gt; , &lt; de pakket naam &gt; met de namen van de map, het project en het pakket in de SSIS-catalogus voordat u het bestand opslaat. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. Als u de pijp lijn wilt maken: **RunSSISPackagePipeline**, voert u de cmdlet **set-AzDataFactoryV2Pipeline** uit.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Hier volgt een voorbeeld van uitvoer:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Een pijplijnuitvoering maken
Gebruik de cmdlet **invoke-AzDataFactoryV2Pipeline** om de pijp lijn uit te voeren. De cmdlet retourneert de id voor de pijplijnuitvoering voor toekomstige controle.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

Voer het volgende PowerShell-script uit om continu de status van de pijplijnuitvoering te controleren totdat het kopiëren van de gegevens is voltooid. Kopieer/plak het volgende script in het PowerShell-venster en druk op ENTER. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>Een trigger maken
In de vorige stap hebt u de pijp lijn op aanvraag geactiveerd. U kunt ook een schema trigger maken om de pijp lijn op een schema uit te voeren (elk uur, dagelijks, enzovoort).

1. Maak een JSON-bestand met de naam **MyTrigger.js** in de map **C:\ADF\RunSSISPackage** met de volgende inhoud: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. Ga in **Azure PowerShell** naar de map **C:\ADF\RunSSISPackage** .
3. Voer de cmdlet **set-AzDataFactoryV2Trigger** uit om de trigger te maken. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. De trigger heeft standaard de status gestopt. Start de trigger door de cmdlet **Start-AzDataFactoryV2Trigger** uit te voeren. 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Controleer of de trigger is gestart door de cmdlet **Get-AzDataFactoryV2Trigger** uit te voeren. 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Voer de volgende opdracht na het volgende uur uit. Als de huidige tijd bijvoorbeeld 3:25 uur UTC is, voert u de opdracht uit op 4 PM UTC. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    U kunt de volgende query uitvoeren op de SSISDB-data base in SQL Database om te controleren of het pakket is uitgevoerd. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Volgende stappen
U kunt de pijp lijn ook bewaken met behulp van de Azure Portal. Zie [de pijp lijn controleren](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)voor stapsgewijze instructies.