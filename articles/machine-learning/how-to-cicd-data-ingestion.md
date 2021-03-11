---
title: DevOps voor een gegevensopnamepijplijn
titleSuffix: Azure Machine Learning
description: Meer informatie over het Toep assen van DevOps-procedures voor het maken van een pijp lijn voor gegevens opname om gegevens voor te bereiden met Azure Data Factory en Azure Databricks.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: e8a8b952d917db3a7eefd2e0371d41287c5be944
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612470"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps voor een gegevensopnamepijplijn

In de meeste gevallen is een oplossing voor gegevens opname een samen stelling van scripts, aanroepen van de service en een pijp lijn die alle activiteiten organiseert. In dit artikel leert u hoe u DevOps-procedures toepast op de ontwikkelings levenscyclus van een gemeen schappelijke gegevens opname pijplijn die gegevens voorbereidt voor machine learning model training. De pijp lijn is gebouwd met behulp van de volgende Azure-Services:

* __Azure Data Factory__: de onbewerkte gegevens worden gelezen en gegevens worden voor bereid.
* __Azure Databricks__: voert een python-notebook uit waarmee de gegevens worden getransformeerd.
* __Azure pipelines__: automatiseert een doorlopend integratie-en ontwikkelings proces.

## <a name="data-ingestion-pipeline-workflow"></a>Pijplijn werk stroom voor gegevens opname

De pijp lijn voor gegevens opname implementeert de volgende werk stroom:

1. Onbewerkte gegevens worden in een Azure Data Factory-pijp lijn (ADF) gelezen.
1. De ADF-pijp lijn stuurt de gegevens naar een Azure Databricks-cluster, waarmee een python-notebook wordt uitgevoerd om de gegevens te transformeren.
1. De gegevens worden opgeslagen in een BLOB-container, waar deze door Azure Machine Learning kan worden gebruikt om een model te trainen.

![pijplijn werk stroom voor gegevens opname](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

## <a name="continuous-integration-and-delivery-overview"></a>Overzicht van doorlopende integratie en levering

Net als bij veel software oplossingen is er een team (bijvoorbeeld gegevens technici) aan het werk. Ze werken samen en delen dezelfde Azure-resources, zoals Azure Data Factory, Azure Databricks en Azure Storage accounts. Het verzamelen van deze resources is een ontwikkel omgeving. De gegevens technici dragen bij aan dezelfde bron code basis.

Een continue integratie-en leverings systeem automatiseert het proces van het bouwen, testen en leveren van de oplossing. Het proces doorlopend integratie (CI) voert de volgende taken uit:

* De code wordt geassembleerd
* Hiermee wordt gecontroleerd of de code wordt getest
* Voert eenheids tests uit
* Produceert artefacten zoals geteste code en Azure Resource Manager sjablonen

Het proces doorlopende levering (CD) implementeert de artefacten op de downstream-omgevingen.

![cicd-diagram voor gegevens opname](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

In dit artikel wordt beschreven hoe u de CI-en CD-processen kunt automatiseren met [Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Broncode beheer

Broncode beheer is nodig om wijzigingen bij te houden en samen werking tussen team leden in te scha kelen.
De code wordt bijvoorbeeld opgeslagen in een Azure DevOps-, GitHub-of GitLab-opslag plaats. De werk stroom voor samen werking is gebaseerd op een vertakkings model. Bijvoorbeeld [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html).

### <a name="python-notebook-source-code"></a>Bron code van python-notebook

De gegevens technici werken met de bron code van python-notebook lokaal in een IDE (bijvoorbeeld [Visual Studio code](https://code.visualstudio.com)) of rechtstreeks in de Databricks-werk ruimte. Zodra de code wijzigingen zijn voltooid, worden ze samengevoegd met de opslag plaats na een vertakkings beleid.

> [!TIP] 
> We raden u aan de code op te slaan in `.py` bestanden in plaats van in `.ipynb` Jupyter notebook indeling. Het verbetert de Lees baarheid van de code en maakt automatische controle van de code kwaliteit mogelijk in het CI-proces.

### <a name="azure-data-factory-source-code"></a>Azure Data Factory bron code

De bron code van Azure Data Factory pijp lijnen is een verzameling JSON-bestanden die door een Azure Data Factory-werk ruimte worden gegenereerd. Normaal gesp roken werken de gegevens technici met een visuele ontwerper in de werk ruimte Azure Data Factory, in plaats van de bron code bestanden rechtstreeks. 

Zie [Author with Azure opslag plaatsen Git Integration](../data-factory/source-control.md#author-with-azure-repos-git-integration)voor informatie over het configureren van de werk ruimte voor het gebruik van een broncode beheer bibliotheek.   

## <a name="continuous-integration-ci"></a>Continue integratie (CI)

Het ultieme doel van het proces voor continue integratie is het verzamelen van het gezamenlijke team werk van de bron code en het voorbereiden van de implementatie op de downstream-omgevingen. Net als bij broncode beheer is dit proces verschillend voor de python-notebooks en Azure Data Factory pijp lijnen. 

### <a name="python-notebook-ci"></a>Python-notebook-CI

Het CI-proces voor de python-notebooks haalt de code op uit de vertakking voor samen werking (bijvoorbeeld ***Master** _ of _ *_ontwikkeling_* *) en voert de volgende activiteiten uit:
* Code linting
* Het testen van modules
* De code opslaan als een artefact

In het volgende code fragment ziet u de implementatie van deze stappen in een Azure DevOps ***yaml*** -pijp lijn:

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks
```

De pijp lijn gebruikt [flake8](https://pypi.org/project/flake8/) om de python-code te gebruiken. De eenheids tests die in de bron code zijn gedefinieerd, worden uitgevoerd en de resultaten van de linting en test worden gepubliceerd, zodat ze beschikbaar zijn in het uitvoerings scherm van Azure pipeline:

![testen van de pluis eenheid](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Als de samenvoegings-en eenheids test is geslaagd, kopieert de pijp lijn de bron code naar de artefact opslagplaats die moet worden gebruikt door de volgende implementaties tappen.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

CI-proces voor een Azure Data Factory pijp lijn is een knel punt voor een gegevens opname pijplijn. Er is geen continue integratie. Een implementeerbaar artefact voor Azure Data Factory is een verzameling Azure Resource Manager sjablonen. De enige manier om deze sjablonen te maken, is door op de knop ***publiceren*** te klikken in de werk ruimte Azure Data Factory.

1. De gegevens technici voegen de bron code van hun functie vertakkingen samen in de vertakking voor samen werking, bijvoorbeeld ***Master** _ of _ *_ontwikkelen_* *. 
1. Iemand met de verleende machtigingen klikt op de knop ***publiceren*** om Azure Resource Manager sjablonen te genereren van de bron code in de vertakking voor samen werking. 
1. De werk ruimte valideert de pijp lijnen (deze zijn van toepassing op het maken van linten en het testen van eenheden), genereert Azure Resource Manager sjablonen (denk eraan dat deze zijn gemaakt) en slaat de gegenereerde sjablonen op in een technische vertakking ***adf_publish*** in dezelfde code opslagplaats (denk eraan als publicatie artefacten). Deze vertakking wordt automatisch gemaakt door de Azure Data Factory-werk ruimte. 

Zie voor meer informatie over dit proces [doorlopende integratie en levering in azure Data Factory](../data-factory/continuous-integration-deployment.md).

Het is belang rijk om ervoor te zorgen dat de gegenereerde Azure Resource Manager sjablonen omgeving neutraal zijn. Dit betekent dat alle waarden die kunnen verschillen tussen omgevingen, parametrized zijn. Azure Data Factory is slim genoeg om het meren deel van dergelijke waarden als para meters weer te geven. Zo worden in de volgende sjabloon de verbindings eigenschappen voor een Azure Machine Learning werk ruimte als para meters weer gegeven:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

Het is echter mogelijk dat u uw aangepaste eigenschappen wilt weer geven die niet standaard worden verwerkt door de Azure Data Factory-werk ruimte. In het scenario van dit artikel roept een Azure Data Factory pijp lijn een python-notebook aan om de gegevens te verwerken. Het notitie blok accepteert een para meter met de naam van een invoer gegevensbestand.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Deze naam wijkt af van de ***dev** _-, _*_QA_*_-, _*_bedoeld_*_-en _*_productie_*_ omgevingen. In een complexe pijp lijn met meerdere activiteiten kunnen er verschillende aangepaste eigenschappen zijn. Het is raadzaam om al deze waarden op één plek te verzamelen en ze te definiëren als pijp lijn _ *_variabelen_* *:

![In de scherm afbeelding wordt een notitie blok met de naam PrepareData en M L-pijp lijn uitvoeren met de naam M L uitvoeren pijp lijn weer gegeven bovenaan met het tabblad variabelen dat hieronder is geselecteerd, met de optie om nieuwe variabelen toe te voegen.](media/how-to-cicd-data-ingestion/adf-variables.png)

De pijplijn activiteiten kunnen verwijzen naar de pijplijn variabelen en ze in feite gebruiken:

![Scherm afbeelding toont een notitie blok met de naam PrepareData en M L-pijp lijn uitvoeren met de naam M L Voer pijp lijn uit, bovenaan met het tabblad instellingen hieronder geselecteerd.](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

In de Azure Data Factory-werk ruimte worden pijplijn variabelen ***niet*** standaard weer gegeven als Azure Resource Manager sjablonen-para meters. In de werk ruimte wordt gebruikgemaakt van de [standaard sjabloon voor parameterisering](../data-factory/continuous-integration-deployment.md#default-parameterization-template) die bepaalt welke pijplijn eigenschappen moeten worden weer gegeven als Azure Resource Manager sjabloon parameters. Als u pijplijn variabelen wilt toevoegen aan de lijst, werkt u de `"Microsoft.DataFactory/factories/pipelines"` sectie van de [standaard sjabloon parameterisering](../data-factory/continuous-integration-deployment.md#default-parameterization-template) bij met het volgende code fragment en plaatst u het JSON-bestand in de hoofdmap van de bronmap:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Als u dit doet, wordt de Azure Data Factory-werk ruimte de variabelen aan de lijst met para meters toegevoegd wanneer u op de knop ***publiceren*** klikt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

De waarden in het JSON-bestand zijn standaard waarden die zijn geconfigureerd in de pijplijn definitie. Ze worden naar verwachting overschreven door de doel omgevings waarden wanneer de Azure Resource Manager sjabloon wordt geïmplementeerd.

## <a name="continuous-delivery-cd"></a>Continue levering (CD)

Het doorlopende leverings proces neemt de artefacten en implementeert deze in de eerste doel omgeving. Er wordt gecontroleerd of de oplossing werkt door tests uit te voeren. Als dat lukt, gaat het verder met de volgende omgeving. 

De CD Azure-pijp lijn bestaat uit meerdere fasen die de omgevingen vertegenwoordigen. Elke fase bevat [implementaties](/azure/devops/pipelines/process/deployment-jobs) en [taken](/azure/devops/pipelines/process/phases?tabs=yaml) die de volgende stappen uitvoeren:

* Een python-notebook implementeren op Azure Databricks werk ruimte
* Een Azure Data Factory-pijp lijn implementeren 
* De pijplijn uitvoeren
* Het resultaat van de gegevens opname controleren

De pijplijn fasen kunnen worden geconfigureerd met [goed keuringen](/azure/devops/pipelines/process/approvals?tabs=check-pass) en [poorten](/azure/devops/pipelines/release/approvals/gates) die extra controle bieden over hoe het implementatie proces zich ontwikkelt door de keten van omgevingen.

### <a name="deploy-a-python-notebook"></a>Een python-notebook implementeren

Het volgende code fragment definieert een Azure-pijplijn [implementatie](/azure/devops/pipelines/process/deployment-jobs) waarmee een python-notebook wordt gekopieerd naar een Databricks-cluster:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

De artefacten die door de CI worden geproduceerd, worden automatisch naar de implementatie agent gekopieerd en zijn beschikbaar in de `$(Pipeline.Workspace)` map. In dit geval verwijst de implementatie taak naar het `di-notebooks` artefact met de python-notebook. Deze [implementatie](/azure/devops/pipelines/process/deployment-jobs) maakt gebruik van de [Databricks Azure DevOps-extensie](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) om de notitieblok bestanden te kopiëren naar de Databricks-werk ruimte.

Het `Deploy_to_QA` stadium bevat een verwijzing naar de `devops-ds-qa-vg` variabelen groep die is gedefinieerd in het Azure DevOps-project. De stappen in deze fase verwijzen naar de variabelen van deze variabelen groep (bijvoorbeeld `$(DATABRICKS_URL)` en `$(DATABRICKS_TOKEN)` ). Het idee is dat de volgende fase (bijvoorbeeld `Deploy_to_UAT` ) werkt met dezelfde namen van variabelen die zijn gedefinieerd in de eigen variabelen groep bedoeld.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Een Azure Data Factory-pijp lijn implementeren

Een implementeerbaar artefact voor Azure Data Factory is een Azure Resource Manager sjabloon. Het wordt geïmplementeerd met de implementatie taak van de ***Azure-resource groep*** , zoals wordt gedemonstreerd in het volgende code fragment:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
De waarde van de para meter data filename is afkomstig van de variabele die is `$(DATA_FILE_NAME)` gedefinieerd in een QA-fase variabelen groep. Op dezelfde manier kunnen alle para meters die zijn gedefinieerd in ***ARMTemplateForFactory.jsop*** worden overschreven. Als dat niet het geval is, worden de standaard waarden gebruikt.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>De pijp lijn uitvoeren en het resultaat van de gegevens opname controleren

De volgende stap is om ervoor te zorgen dat de geïmplementeerde oplossing werkt. De volgende taak definitie voert een Azure Data Factory pijp lijn met een [Power shell-script](https://github.com/microsoft/DataOps/tree/master/adf/utils) uit en voert een python-notebook uit op een Azure Databricks cluster. Het notitie blok controleert of de gegevens correct zijn opgenomen en valideert het resultaat gegevensbestand met de `$(bin_FILE_NAME)` naam.

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

De laatste taak in de taak controleert het resultaat van de uitvoering van de notitie blok. Als er een fout wordt geretourneerd, wordt de status van de pijplijn uitvoering ingesteld op mislukt.

## <a name="putting-pieces-together"></a>Delen samen brengen

De volledige CI/CD Azure-pijp lijn bestaat uit de volgende fasen:
* CI
* Implementeren naar QA
    * Implementeren naar Databricks + implementeren naar ADF
    * Integratie test

Het bevat een aantal ***Implementeer** _-fasen die gelijk zijn aan het aantal doel omgevingen dat u hebt. Elke _ *_Deploy_**-fase bevat twee [implementaties](/azure/devops/pipelines/process/deployment-jobs) die parallel worden uitgevoerd en een [taak](/azure/devops/pipelines/process/phases?tabs=yaml) die wordt uitgevoerd na implementaties om de oplossing in de omgeving te testen.

Een voorbeeld implementatie van de pijp lijn wordt geassembleerd in het volgende ***yaml*** -fragment:

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Volgende stappen

* [Broncodebeheer in Azure Data Factory](../data-factory/source-control.md)
* [Continue integratie en levering in Azure Data Factory](../data-factory/continuous-integration-deployment.md)
* [DevOps voor Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)