---
title: Azure-SSIS Integration Runtime plannen
description: In dit artikel wordt beschreven hoe u het starten en stoppen van Azure-SSIS Integration Runtime kunt plannen met behulp van Azure Data Factory.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 40c3b8ab228c93fd4c681281d89d16f88ddf30f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384364"
---
# <a name="how-to-start-and-stop-azure-ssis-integration-runtime-on-a-schedule"></a>Azure-SSIS Integration Runtime gepland starten en stoppen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u het starten en stoppen van Azure-SSIS Integration Runtime (IR) kunt plannen met behulp van Azure Data Factory (ADF). Azure-SSIS IR is een ADF-reken resource die is toegewezen voor het uitvoeren van SQL Server Integration Services (SSIS)-pakketten. Er zijn kosten verbonden aan het uitvoeren van Azure-SSIS IR. Daarom wilt u uw IR doorgaans alleen uitvoeren als u SSIS-pakketten moet uitvoeren in Azure en de IR wilt stoppen wanneer u deze niet meer nodig hebt. U kunt de ADF-gebruikers interface (UI)/app of Azure PowerShell gebruiken om [uw IR hand matig te starten of te stoppen](manage-azure-ssis-integration-runtime.md).

U kunt ook webactiviteiten maken in ADF-pijp lijnen om uw IR op schema te starten/stoppen, bijvoorbeeld om de tijd in de ochtend te starten voordat u uw dagelijkse ETL-workloads uitvoert en deze op de middag stopt nadat deze zijn voltooid.  U kunt ook een activiteit voor het uitvoeren van SSIS-pakketten koppelen tussen twee webactiviteiten die uw IR starten en stoppen, zodat uw IR op aanvraag kan worden gestart/gestopt, net zo lang vóór/na de uitvoering van het pakket. Zie voor meer informatie over het uitvoeren van SSIS-pakket activiteit [een SSIS-pakket uitvoeren met behulp van SSIS-pakket activiteit uitvoeren in het ADF-pijplijn](how-to-invoke-ssis-package-ssis-activity.md) artikel.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten
Als u uw Azure-SSIS IR al hebt ingericht, richt u het in door de instructies in de [zelf studie](./tutorial-deploy-ssis-packages-azure.md)te volgen. 

## <a name="create-and-schedule-adf-pipelines-that-start-and-or-stop-azure-ssis-ir"></a>ADF-pijp lijnen maken en plannen die Azure-SSIS IR starten en of stoppen
In deze sectie wordt uitgelegd hoe u webactiviteiten in ADF-pijp lijnen kunt gebruiken om uw Azure-SSIS IR op schema te starten of te stoppen, of om het & op aanvraag te stoppen. U wordt begeleid bij het maken van drie pijp lijnen: 

1. De eerste pijp lijn bevat een webactiviteit waarmee uw Azure-SSIS IR wordt gestart. 
2. De tweede pijp lijn bevat een webactiviteit die uw Azure-SSIS IR stopt.
3. De derde pijp lijn bevat een uitvoering van SSIS-pakket activiteiten die zijn gekoppeld aan twee webactiviteiten die uw Azure-SSIS IR starten/stoppen. 

Nadat u deze pijp lijnen hebt gemaakt en getest, kunt u een plannings trigger maken en deze aan een pijp lijn koppelen. De schema trigger definieert een schema voor het uitvoeren van de gekoppelde pijp lijn. 

U kunt bijvoorbeeld twee triggers maken, de eerste is gepland om dagelijks om 6 uur te worden uitgevoerd en aan de eerste pijp lijn te zijn gekoppeld, terwijl de tweede is gepland om dagelijks om 6 uur te worden uitgevoerd en aan de tweede pijp lijn te zijn gekoppeld.  Op deze manier hebt u een periode tussen 6 en 6 uur elke dag als uw IR actief is, zodat u uw dagelijkse ETL-workloads kunt uitvoeren.  

Als u een derde trigger maakt die gepland is om dagelijks om middernacht te worden uitgevoerd en aan de derde pijp lijn is gekoppeld, wordt die pijp lijn om middernacht elke dag uitgevoerd, wordt de IR gestart voordat de uitvoering van het pakket plaatsvindt, waarna het pakket wordt uitgevoerd en de IR onmiddellijk wordt gestopt nadat het pakket is uitgevoerd.

### <a name="create-your-adf"></a>Uw ADF maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).    
2. Klik op **Nieuw** in het linkermenu en klik vervolgens op **Gegevens en analyses** en **Data Factory**. 
   
   ![Nieuw -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
   
3. Voer op de pagina **nieuw Data Factory** **Gegevensfactory myazuressisdatafactory** in als **naam**. 
      
   ![De pagina Nieuwe data factory](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   De naam van uw ADF moet globaal uniek zijn. Als het volgende fout bericht wordt weer gegeven, wijzigt u de naam van de ADF (bijvoorbeeld Uwnaammyazuressisdatafactory) en probeert u deze opnieuw te maken. Zie [het artikel Data Factory naamgevings regels](naming-rules.md) voor meer informatie over naamgevings regels voor ADF-artefacten.
  
   `Data factory name MyAzureSsisDataFactory is not available`
      
4. Selecteer uw Azure- **abonnement** waaronder u uw ADF wilt maken. 
5. Voer een van de volgende stappen uit voor de **resource groep**:
     
   - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst. 
   - Selecteer **nieuwe maken** en voer de naam van de nieuwe resource groep in.   
         
   Zie [resource groepen gebruiken om uw Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie over resource groepen.
   
6. Selecteer voor **versie** **v2** .
7. Selecteer bij **locatie** een van de locaties die worden ondersteund voor het maken van ADF vanuit de vervolg keuzelijst.
8. Selecteer **Vastmaken aan dashboard**.     
9. Klik op **Create**.
10. In het Azure-dash board ziet u de volgende tegel met de status: **Data Factory implementeren**. 

    ![tegel met de status 'gegevensfactory implementeren'](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
   
11. Nadat het maken is voltooid, ziet u de pagina ADF zoals hieronder wordt weer gegeven.
   
    ![Startpagina van de gegevensfactory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
   
12. Klik op **auteur & monitor** om de ADF-gebruikers interface/-app op een afzonderlijk tabblad te starten.

### <a name="create-your-pipelines"></a>Uw pijp lijnen maken

1. Selecteer op **de pagina aan de slag de** optie **pijp lijn maken**. 

   ![Pagina Aan de slag](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
   
2. Vouw in de werkset **activiteiten** het menu **Algemeen** uit en sleep **& een** webactiviteit naar het ontwerp oppervlak voor pijp lijnen neer te zetten. Wijzig op het tabblad **Algemeen** van het venster Eigenschappen van activiteit de naam van de activiteit in **startMyIR**. Schakel over naar het tabblad **instellingen** en voer de volgende acties uit.

    1. Voor **URL** voert u de volgende URL in voor rest API die Azure-SSIS IR, vervangt `{subscriptionId}` , `{resourceGroupName}` , `{factoryName}` en `{integrationRuntimeName}` met de werkelijke waarden voor uw IR: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/start?api-version=2018-06-01` u kunt ook & plakken van de resource-id van uw IR vanaf de controle pagina van de ADF-UI/app om het volgende deel van de bovenstaande URL te vervangen: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}`
    
       ![ADF SSIS IR-Resource-ID](./media/how-to-schedule-azure-ssis-integration-runtime/adf-ssis-ir-resource-id.png)
  
    2. Selecteer voor **methode** **post**. 
    3. Voer in bij **hoofd tekst** `{"message":"Start my IR"}` . 
    4. Voor **verificatie** selecteert u **MSI** om de beheerde identiteit voor uw ADF te gebruiken. zie [beheerde identiteit voor Data Factory](./data-factory-service-identity.md) artikel voor meer informatie.
    5. Voer in voor resource `https://management.azure.com/` .
    
       ![ADF-webactiviteits schema SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-schedule-ssis-ir.png)
  
3. Kloon de eerste pijp lijn om een tweede te maken, waarbij u de naam van de activiteit wijzigt in **stopMyIR** en de volgende eigenschappen vervangt.

    1. Voor **URL** voert u de volgende URL in voor rest API die het Azure-SSIS IR, vervangen `{subscriptionId}` ,, `{resourceGroupName}` `{factoryName}` en `{integrationRuntimeName}` met de werkelijke waarden voor uw IR stopt: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}/integrationRuntimes/{integrationRuntimeName}/stop?api-version=2018-06-01`
    
    2. Voer in bij **hoofd tekst** `{"message":"Stop my IR"}` . 

4. Maak een derde pijp lijn, Sleep & Haal een activiteit uit het **uitvoeren van SSIS-pakketten** uit de werkset **activiteiten** op het ontwerp oppervlak voor pijp lijnen en configureer deze volgens de instructies in [een SSIS-pakket aanroepen met behulp van SSIS-pakket activiteit uitvoeren in het ADF](how-to-invoke-ssis-package-ssis-activity.md) -artikel.  U kunt ook in plaats daarvan een **opgeslagen procedure** -activiteit gebruiken en deze configureren volgens de instructies in [een SSIS-pakket aanroepen met behulp van opgeslagen procedure activiteit in het ADF](how-to-invoke-ssis-package-stored-procedure-activity.md) -artikel.  Vervolgens koppelt u de activiteit Execute SSIS package/opgeslagen procedure tussen twee webactiviteiten die uw IR starten/stoppen, vergelijkbaar met die webactiviteiten in de eerste/tweede pijp lijnen.

   ![ADF-webactiviteit op aanvraag SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/adf-web-activity-on-demand-ssis-ir.png)

5. Wijs de beheerde identiteit voor uw ADF toe aan de rol **Inzender** , zodat webactiviteiten in de pijp lijnen rest API kunnen aanroepen om Azure-SSIS IRs in IT te starten of te stoppen.  Op de pagina ADF in Azure Portal klikt u op **toegangs beheer (IAM)**, klikt u op **+ roltoewijzing toevoegen** en voert u vervolgens de volgende acties uit op de Blade **roltoewijzing toevoegen** .

    1. Selecteer voor **rol** **Inzender**. 
    2. **Als u toegang wilt toewijzen**, selecteert u **Azure AD-gebruiker,-groep of Service-Principal**. 
    3. Zoek bij **selecteren** naar de ADF-naam en selecteer deze. 
    4. Klik op **Opslaan**.
    
   ![Roltoewijzing beheerde identiteits toewijzing van ADF](./media/how-to-schedule-azure-ssis-integration-runtime/adf-managed-identity-role-assignment.png)

6. Valideer uw ADF en alle pijplijn instellingen door te klikken op **Validate all/validate** op de werk balk Factory/pipeline. Sluit de **validatie-uitvoer van de fabriek/pijp lijn** door te klikken op de **>>** knop.  

   ![Pijplijn valideren](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-your-pipelines"></a>Test uw pijp lijnen uitvoeren

1. Selecteer **test uitvoering** op de werk balk voor elke pijp lijn en Zie **uitvoer** venster in het onderste deel venster. 

   ![Test uitvoering](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
    
2. Als u de derde pijp lijn wilt testen, start u SQL Server Management Studio (SSMS). Voer de volgende acties uit in het venster **verbinding maken met server** . 

    1. Voer bij **Server naam** **&lt; de naam van uw server in &gt; . database.Windows.net**.
    2. Selecteer **opties >>**.
    3. Selecteer **SSISDB** om **verbinding te maken met de data base**.
    4. Selecteer **Verbinding maken**. 
    5. Vouw **Integration Services Catalogs**  ->  **SSISDB** uit > uw map-> **projecten** : > uw SSIS-project->- **pakketten**. 
    6. Klik met de rechter muisknop op het opgegeven SSIS-pakket om uit te voeren en selecteer **rapporten**  ->  **standaard rapporten**  ->  **alle uitvoeringen**. 
    7. Controleer of de toepassing is uitgevoerd. 

   ![SSIS-pakket uitvoering verifiëren](./media/how-to-schedule-azure-ssis-integration-runtime/verify-ssis-package-run.png)

### <a name="schedule-your-pipelines"></a>Uw pijp lijnen plannen

Nu uw pijp lijnen werken zoals verwacht, kunt u triggers maken om ze uit te voeren op opgegeven cadences. Zie [de pijp lijn activeren op basis van een](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) artikel voor meer informatie over het koppelen van triggers aan pijp lijnen.

1. Selecteer op de werk balk pijp lijn de optie **trigger** en selecteer **Nieuw/bewerken**. 

   ![Scherm afbeelding van de optie trigger-> nieuw/bewerken.](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)

2. Selecteer **+ Nieuw** in het deel venster **triggers toevoegen** .

   ![Triggers toevoegen-nieuw](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)

3. Voer in het deel venster **nieuwe trigger** de volgende acties uit: 

    1. Voer bij **naam** een naam in voor de trigger. In het volgende voor beeld is **dagelijks uitvoeren** de naam van de trigger. 
    2. Selecteer bij **type** de optie **planning**. 
    3. Voer voor **begin datum (UTC)** een begin datum en-tijd in UTC in. 
    4. Voer voor **terugkeer patroon** een uitgebracht in voor de trigger. In het volgende voor beeld is het **dagelijks** één keer. 
    5. Selecteer bij **einde** **geen einde** of voer een eind datum en-tijd in na selectie **op datum**. 
    6. Selecteer **geactiveerd** om de trigger onmiddellijk te activeren nadat u de volledige ADF-instellingen hebt gepubliceerd. 
    7. Selecteer **Next**.

   ![Trigger-> nieuw/bewerken](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
    
4. Controleer op de pagina **para meters voor uitvoeren van trigger** een waarschuwing en selecteer **volt ooien**. 
5. Publiceer de volledige ADF-instellingen door **Alles publiceren** te selecteren in de werk balk van de fabriek. 

   ![Alles publiceren](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)

### <a name="monitor-your-pipelines-and-triggers-in-azure-portal"></a>Bewaak uw pijp lijnen en triggers in Azure Portal

1. Als u de uitvoering van triggers en pijplijn uitvoeringen wilt bewaken, gebruikt u het tabblad **controleren** links van de gebruikers interface/app van ADF. Zie [het artikel pijp lijn controleren](quickstart-create-data-factory-portal.md#monitor-the-pipeline) voor gedetailleerde stappen.

   ![Pijplijnuitvoeringen](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)

2. Als u de uitvoeringen van de activiteit wilt weer geven die zijn gekoppeld aan een pijplijn uitvoering, selecteert u de eerste koppeling (uitvoering van de **activiteit weer geven**) in de kolom **acties** . Voor de derde pijp lijn ziet u drie uitvoeringen van de activiteit, één voor elke gekoppelde activiteit in de pijp lijn (webactiviteit voor het starten van uw IR, opgeslagen procedure activiteit voor het uitvoeren van uw pakket en webactiviteit om uw IR te stoppen). Als u de pijp lijn opnieuw wilt weer geven, selecteert u de koppeling **pijp lijnen** bovenaan.

   ![Uitvoering van activiteiten](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)

3. Als u de trigger uitvoeringen wilt weer geven, selecteert u **trigger uitvoeringen** in de vervolg keuzelijst onder **pijplijn uitvoeringen** bovenaan. 

   ![Triggeruitvoeringen](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

### <a name="monitor-your-pipelines-and-triggers-with-powershell"></a>Uw pijp lijnen en triggers bewaken met Power shell

Gebruik scripts zoals de volgende voor beelden om uw pijp lijnen en triggers te bewaken.

1. De status van een pijplijn uitvoering ophalen.

   ```powershell
   Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $myPipelineRun
   ```

2. Informatie over een trigger ophalen.

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name  "myTrigger"
   ```

3. De status ophalen van de uitvoering van een trigger.

   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "myTrigger" -TriggerRunStartedAfter "2018-07-15" -TriggerRunStartedBefore "2018-07-16"
   ```

## <a name="create-and-schedule-azure-automation-runbook-that-startsstops-azure-ssis-ir"></a>Azure Automation-runbook maken en plannen waarmee Azure-SSIS IR wordt gestart/gestopt

In deze sectie leert u hoe u Azure Automation runbook maakt dat Power shell-script uitvoert, uw Azure-SSIS IR start/stopt volgens een planning.  Dit is handig wanneer u extra scripts wilt uitvoeren vóór/na het starten/stoppen van uw IR voor vóór/na de verwerking.

### <a name="create-your-azure-automation-account"></a>Uw Azure Automation-account maken

Als u nog geen Azure Automation-account hebt, maakt u er een door de instructies in deze stap te volgen. Zie [een Azure Automation-account maken](../automation/automation-quickstart-create-account.md) voor gedetailleerde stappen. Als onderdeel van deze stap maakt u een **uitvoeren als** -account voor Azure (een Service-Principal in uw Azure Active Directory) en wijst u dit toe aan een **Inzender** rol in uw Azure-abonnement. Zorg ervoor dat het hetzelfde abonnement is dat de ADF bevat met Azure SSIS IR. Azure Automation gebruikt dit account voor de verificatie van Azure Resource Manager en het uitvoeren van uw resources. 

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. ADF-gebruikers interface/-app wordt momenteel alleen ondersteund in micro soft Edge en Google Chrome-webbrowsers.
2. Meld u aan bij de [Azure-portal](https://portal.azure.com/).    
3. Selecteer **Nieuw** in het menu links, selecteer **controle en beheer** en selecteer **Automation**. 

   ![Scherm afbeelding die de Controle en beheer > Automation-optie markeert.](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
    
2. Voer de volgende acties uit in het deel venster **Automation-account toevoegen** .

    1. Voer bij **naam** een naam in voor uw Azure Automation-account. 
    2. Selecteer bij **abonnement** het abonnement met uw ADF met Azure-SSIS IR. 
    3. Voor **resource groep** selecteert u **Nieuw maken** om een nieuwe resource groep te maken of **bestaande gebruiken** om een bestaande te selecteren. 
    4. Selecteer voor **locatie** een locatie voor uw Azure Automation-account. 
    5. Bevestig een **uitvoeren als-account voor Azure maken** als **Ja**. Een service-principal wordt in uw Azure Active Directory gemaakt en een rol **Inzender** toegewezen in uw Azure-abonnement.
    6. Selecteer **vastmaken aan dash board** om het permanent weer te geven in het Azure-dash board. 
    7. Selecteer **Maken**. 

   ![New-> Controle en beheer-> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
   
3. U ziet de implementatie status van uw Azure Automation-account in azure dash board en meldingen. 
    
   ![Automation implementeren](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
    
4. U ziet de start pagina van uw Azure Automation-account nadat het is gemaakt. 

   ![Start pagina Automation](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-adf-modules"></a>ADF-modules importeren

1. Selecteer in het menu links de optie **modules** in **gedeelde bronnen** en controleer of u **AZ. DataFactory**  +  **AZ. profile** hebt in de lijst met modules.

   ![De vereiste modules controleren](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  Als **AZ. DataFactory** niet is geïnstalleerd, gaat u naar de PowerShell Gallery voor de [module AZ. DataFactory](https://www.powershellgallery.com/packages/Az.DataFactory/), selecteert **u implementeren naar Azure Automation**, selecteert u uw Azure Automation account en selecteert u **OK**. Ga terug naar **modules** weer geven in **gedeelde bronnen** in het menu links en wacht totdat u de **status** van **AZ. DataFactory** -module is gewijzigd in **beschikbaar**.

    ![De Data Factory-module verifiëren](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  Als u geen **AZ. profile** hebt, gaat u naar de PowerShell Gallery voor de [module AZ. profile](https://www.powershellgallery.com/packages/Az.profile/), selecteert **u implementeren naar Azure Automation**, selecteert u uw Azure Automation account en selecteert u **OK**. Ga terug naar **modules** weer geven in **gedeelde bronnen** in het linkermenu en wacht totdat de **status** van de **AZ. profile** -module is gewijzigd in **beschikbaar**.

    ![De profiel module verifiëren](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-your-powershell-runbook"></a>Uw Power shell-runbook maken

De volgende sectie bevat stappen voor het maken van een Power shell-runbook. Het script dat is gekoppeld aan uw runbook start/stopt Azure-SSIS IR op basis van de opdracht die u voor de **bewerkings** parameter opgeeft. In deze sectie vindt u niet de volledige Details voor het maken van een runbook. Zie [een runbook](../automation/automation-quickstart-create-runbook.md) -artikel maken voor meer informatie.

1. Ga naar het tabblad **Runbooks** en selecteer **+ een runbook toevoegen** op de werk balk. 

   ![Scherm afbeelding met de knop + een runbook toevoegen.](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
   
2. Selecteer **een nieuw Runbook maken** en voer de volgende acties uit: 

    1. Voer bij **naam** **StartStopAzureSsisRuntime** in.
    2. Voor het **type Runbook** selecteert u **Power shell**.
    3. Selecteer **Maken**.
    
   ![Een runbook-knop toevoegen](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
   
3. Kopieer & plak het volgende Power shell-script in het venster van het runbook-script. Sla het runbook op en publiceer het met behulp van de knoppen **Opslaan** en **publiceren** op de werk balk. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

   ![Power shell-runbook bewerken](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
    
4. Test uw runbook door te klikken op de knop **Start** op de werk balk. 

   ![Knop runbook starten](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
    
5. Voer in het deel venster **Runbook starten** de volgende acties uit: 

    1. Voer bij naam van de **resource groep** de naam in van de resource groep met de ADF Azure-SSIS IR. 
    2. Voer bij **naam van Data Factory** de naam van de ADF in met Azure-SSIS IR. 
    3. Voer voor **AZURESSISNAME** de naam van Azure-SSIS IR in. 
    4. Voer **begin** in bij **bewerking**. 
    5. Selecteer **OK**.  

   ![Runbook-venster starten](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
   
6. Selecteer in het venster taak de optie **uitvoer** tegel. Wacht in het uitvoer venster op het bericht **# # # # # voltooide # #** # # # nadat het # # # # # wordt weer gegeven als # **#**# # #. Het starten van Azure-SSIS IR duurt ongeveer 20 minuten. **Taak** venster sluiten en teruggaan naar het **Runbook** -venster.

   ![Scherm afbeelding waarmee de uitvoer tegel wordt gemarkeerd.](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
    
7. Herhaal de vorige twee stappen met **stoppen** als waarde voor de **bewerking**. Start uw runbook opnieuw door te klikken op de knop **Start** op de werk balk. Voer de naam van uw resource groep, ADF en Azure-SSIS IR in. Voer bij **bewerking** **Stop** in. Wacht in het uitvoer venster op het bericht **# # # # # voltooide # #** # # # nadat je # # # # # **Stop # #**# # # hebt gestopt. Het stoppen van Azure-SSIS IR duurt niet zo lang als het wordt gestart. **Taak** venster sluiten en teruggaan naar het **Runbook** -venster.

8. U kunt uw runbook ook activeren via een webhook die kan worden gemaakt door het menu-item **webhooks** te selecteren of op basis van een schema dat kan worden gemaakt door het menu-item **planningen** te selecteren zoals hieronder wordt aangegeven.  

## <a name="create-schedules-for-your-runbook-to-startstop-azure-ssis-ir"></a>Schema's voor uw runbook maken om Azure-SSIS IR te starten/stoppen

In de vorige sectie hebt u uw Azure Automation runbook gemaakt waarmee u Azure-SSIS IR kunt starten of stoppen. In deze sectie maakt u twee planningen voor uw runbook. Bij het configureren van de eerste planning geeft u **Start** voor **bewerking** op. Op dezelfde manier kunt u bij het configureren van de tweede de **bewerking** **stoppen** opgeven. Zie [een plannings artikel maken](../automation/shared-resources/schedules.md#create-a-schedule) voor gedetailleerde stappen voor het maken van planningen.

1. In het venster **Runbook** selecteert u **planningen** en selecteert u **+ een schema toevoegen** op de werk balk. 

   ![Azure SSIS-IR-gestart](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
   
2. Voer de volgende acties uit in het deel venster **Runbook plannen** : 

    1. Selecteer **een planning koppelen aan uw runbook**. 
    2. Selecteer **een nieuw schema maken**.
    3. Voer in **Nieuw** deel venster **Start IR dagelijks** in als **naam**. 
    4. Voer bij **starten** een tijd in van een paar minuten na de huidige tijd. 
    5. Selecteer **terugkerend** voor **terugkeer patroon**. 
    6. Voer **1** in voor **herhalen** en selecteer **dag**. 
    7. Selecteer **Maken**. 

   ![Begin van planning voor Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
    
3. Schakel over naar **para meters en voer het tabblad instellingen uit** . Geef de naam van uw resource groep, ADF en Azure-SSIS IR op. Voer voor **bewerking** **Start** in en selecteer **OK**. Selecteer **OK** om de pagina planning op **schema** van uw runbook weer te geven. 

   ![Scherm afbeelding die het bewerkings veld markeert.](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
    
4. Herhaal de vorige twee stappen om een planning te maken met de naam **Stop IR Daily**. Voer een tijd in die ten minste 30 minuten na de tijd die u hebt opgegeven voor het **dagelijkse begin** van de IR. Voer bij **bewerking** **Stop** in en selecteer **OK**. Selecteer **OK** om de pagina planning op **schema** van uw runbook weer te geven. 

5. Selecteer in het venster **Runbook** de optie **taken** in het menu links. U ziet de taken die door uw planningen zijn gemaakt op de opgegeven tijden en hun status. U kunt de taak details zien, zoals de uitvoer, vergelijkbaar met wat u hebt gezien nadat u uw runbook hebt getest. 

   ![Plannen voor het staren van de Azure SSIS IR](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
    
6. Wanneer u klaar bent met testen, kunt u uw planningen uitschakelen door ze te bewerken. Selecteer **schema's** in het linkermenu en selecteer dagelijks **IR dagelijks starten/IR stoppen** en selecteer **Nee** voor **ingeschakeld**. 

## <a name="next-steps"></a>Volgende stappen
Zie het volgende blog bericht:
-   [Uw ETL/ELT-werk stromen moderniseren en uitbreiden met SSIS-activiteiten in ADF-pijp lijnen](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)

Raadpleeg de volgende artikelen uit de SSIS-documentatie: 

- [Deploy, run, and monitor an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) (SSIS-pakket implementeren, uitvoeren en bewaken in Azure)   
- [Connect to SSIS catalog on Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) (Verbinding maken met een SSIS-catalogus in Azure)
- [Schedule package execution on Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) (Pakketuitvoering plannen in Azure)
- [Connect to on-premises data sources with Windows authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Verbinding maken met on-premises gegevensbronnen met Windows-verificatie)