---
title: CI/CD met Azure-pijp lijnen en-sjablonen
description: Hierin wordt beschreven hoe u doorlopende integratie in azure-pijp lijnen configureert met behulp van Azure Resource Manager sjablonen. U ziet hoe u een Power shell-script gebruikt of bestanden kopieert naar een faserings locatie en vanaf daar implementeert.
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 4a2f1f15de0abd802f3dce138b2cea33e52e3dfc
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561939"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Resource Manager-sjablonen integreren met Azure-pijplijnen

U kunt Azure Resource Manager sjablonen (ARM-sjablonen) integreren met Azure-pijp lijnen voor continue integratie en continue implementatie (CI/CD). In dit artikel leert u twee geavanceerdere manieren om sjablonen te implementeren met Azure-pijp lijnen.

## <a name="select-your-option"></a>Selecteer uw optie

Voordat u verder gaat met dit artikel, kunt u de verschillende opties voor het implementeren van een ARM-sjabloon vanuit een pijp lijn overwegen.

* **Implementatie taak voor arm-sjabloon gebruiken**. Deze optie is de eenvoudigste optie. Deze aanpak werkt wanneer u een sjabloon rechtstreeks vanuit een opslag plaats wilt implementeren. Deze optie is niet in dit artikel opgenomen, maar wordt in plaats daarvan behandeld in de zelf studie [continue integratie van arm-sjablonen met Azure-pijp lijnen](deployment-tutorial-pipeline.md). U ziet hoe u de [implementatie taak voor arm-sjablonen](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) kunt gebruiken om een sjabloon te implementeren vanuit uw github opslag plaats.

* **Voeg een taak toe die een Azure PowerShell script uitvoert**. Deze optie biedt het voor deel dat u binnen de levens cyclus van de ontwikkeling consistentie krijgt, omdat u hetzelfde script kunt gebruiken dat u hebt gebruikt bij het uitvoeren van lokale tests. Uw script implementeert de sjabloon, maar kan ook andere bewerkingen uitvoeren, zoals het ophalen van waarden die worden gebruikt als para meters. Deze optie wordt weer gegeven in dit artikel. Zie [Azure PowerShell taak](#azure-powershell-task).

   Visual Studio biedt het [Azure-resource groeps project](create-visual-studio-deployment-project.md) dat een Power shell-script bevat. Het script faseert artefacten van uw project naar een opslag account dat door Resource Manager kan worden geopend. Artefacten zijn items in uw project, zoals gekoppelde sjablonen, scripts en binaire bestanden voor toepassingen. Als u het script van het project wilt blijven gebruiken, gebruikt u de Power shell-script taak die in dit artikel wordt weer gegeven.

* **Voeg taken toe om taken te kopiëren en te implementeren**. Deze optie biedt een handig alternatief voor het project script. U configureert twee taken in de pijp lijn. Met een taak worden de artefacten op een toegankelijke locatie faseert. De andere taak implementeert de sjabloon vanaf die locatie. Deze optie wordt weer gegeven in dit artikel. Zie [taken kopiëren en implementeren](#copy-and-deploy-tasks).

## <a name="prepare-your-project"></a>Uw project voorbereiden

In dit artikel wordt ervan uitgegaan dat uw ARM-sjabloon en Azure DevOps-organisatie klaar zijn voor het maken van de pijp lijn. De volgende stappen laten zien hoe u ervoor kunt zorgen dat u klaar bent:

* U hebt een Azure DevOps-organisatie. Als u dit niet hebt, [kunt u er gratis een maken](/azure/devops/pipelines/get-started/pipelines-sign-up). Als uw team al een Azure DevOps-organisatie heeft, zorg er dan voor dat u een beheerder bent van het Azure DevOps-project dat u wilt gebruiken.

* U hebt een [service verbinding](/azure/devops/pipelines/library/connect-to-azure) met uw Azure-abonnement geconfigureerd. De taken in de pijp lijn worden uitgevoerd onder de identiteit van de Service-Principal. Zie [een DevOps-project maken](deployment-tutorial-pipeline.md#create-a-devops-project)voor de stappen voor het maken van de verbinding.

* U hebt een [arm-sjabloon](quickstart-create-templates-use-visual-studio-code.md) waarmee de infra structuur voor uw project wordt gedefinieerd.

## <a name="create-pipeline"></a>Pijplijn maken

1. Als u nog geen pijp lijn hebt toegevoegd, moet u een nieuwe pijp lijn maken. Selecteer in uw Azure DevOps-organisatie **pijp lijnen** en **nieuwe pijp lijn**.

   ![Nieuwe pijp lijn toevoegen](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Geef op waar de code wordt opgeslagen. In de volgende afbeelding ziet u hoe u **Azure opslag plaatsen Git** selecteert.

   ![Code bron selecteren](./media/add-template-to-azure-pipelines/select-source.png)

1. Selecteer de opslag plaats met de code voor het project uit de bron.

   ![Opslag plaats selecteren](./media/add-template-to-azure-pipelines/select-repo.png)

1. Selecteer het type pijp lijn dat u wilt maken. U kunt een **starter pijp lijn** selecteren.

   ![Pijp lijn selecteren](./media/add-template-to-azure-pipelines/select-pipeline.png)

U kunt een Azure PowerShell-taak of het kopieer bestand toevoegen en taken implementeren.

## <a name="azure-powershell-task"></a>Azure PowerShell-taak

In deze sectie wordt uitgelegd hoe u doorlopende implementatie kunt configureren met één taak die het Power shell-script in uw project uitvoert. Zie [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) of [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1)als u een Power shell-script nodig hebt dat een sjabloon implementeert.

Met het volgende YAML-bestand wordt een [Azure PowerShell taak](/azure/devops/pipelines/tasks/deploy/azure-powershell)gemaakt:

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-AzTemplate.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

Wanneer u de taak instelt op `AzurePowerShell@5` , gebruikt de pijp lijn de [module AZ](/powershell/azure/new-azureps-module-az). Als u de AzureRM-module in uw script gebruikt, stelt u de taak in op `AzurePowerShell@3` .

```yaml
steps:
- task: AzurePowerShell@3
```

`azureSubscription`Geef voor de naam op van de service verbinding die u hebt gemaakt.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

`scriptPath`Geef voor het relatieve pad van het pijplijn bestand naar het script op. U kunt het pad bekijken in uw opslag plaats.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

`ScriptArguments`Geef in alle para meters op die nodig zijn voor het script. In het volgende voor beeld ziet u enkele para meters voor een script, maar u moet de para meters voor uw script aanpassen.

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

Wanneer u **Opslaan** selecteert, wordt de build-pijp lijn automatisch uitgevoerd. Ga terug naar de samen vatting van uw build-pijp lijn en Bekijk de status.

![Resultaten weergeven](./media/add-template-to-azure-pipelines/view-results.png)

U kunt de pijp lijn die momenteel wordt uitgevoerd selecteren om details over de taken weer te geven. Wanneer deze is voltooid, ziet u de resultaten voor elke stap.

## <a name="copy-and-deploy-tasks"></a>Taken kopiëren en implementeren

In deze sectie wordt beschreven hoe u doorlopende implementatie kunt configureren met behulp van twee taken. Met de eerste taak worden de artefacten naar een opslag account en de tweede taak voor het implementeren van de sjabloon.

Als u bestanden naar een opslag account wilt kopiëren, moet aan de service-principal voor de service verbinding de rol Storage BLOB data contributor of Storage BLOB data owner worden toegewezen. Zie [aan de slag met AzCopy](../../storage/common/storage-use-azcopy-v10.md)voor meer informatie.

In de volgende YAML wordt de [Azure File Copy-taak](/azure/devops/pipelines/tasks/deploy/azure-file-copy)weer gegeven.

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

Er zijn verschillende onderdelen van deze taak voor het herzien van uw omgeving. `SourcePath`Hiermee wordt de locatie van de artefacten ten opzichte van het pijplijn bestand aangegeven.

```yaml
SourcePath: '<path-to-artifacts>'
```

`azureSubscription`Geef voor de naam op van de service verbinding die u hebt gemaakt.

```yaml
azureSubscription: '<your-connection-name>'
```

Geef voor opslag en container naam de namen op van het opslag account en de container die u wilt gebruiken voor het opslaan van de artefacten. Het opslag account moet bestaan.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Nadat u de taak voor het kopiëren van het bestand hebt gemaakt, kunt u de taak toevoegen om de gefaseerde sjabloon te implementeren.

De volgende YAML toont de [implementatie taak voor de Azure Resource Manager sjabloon](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Er zijn verschillende onderdelen van deze taak om meer details te bekijken.

- `deploymentScope`: Selecteer het implementatie bereik in de opties: `Management Group` , `Subscription` en `Resource Group` . Zie [Implementatiebereiken](deploy-rest.md#deployment-scope) voor meer informatie over de bereiken.

- `azureResourceManagerConnection`: Geef de naam op van de service verbinding die u hebt gemaakt.

- `subscriptionId`: Geef de ID van het doel abonnement op. Deze eigenschap is alleen van toepassing op het implementatie bereik van de resource groep en het implementatie bereik van het abonnement.

- `resourceGroupName` en `location` : Geef de naam en de locatie op van de resource groep die u wilt implementeren. Met de taak wordt de resource groep gemaakt als deze nog niet bestaat.

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

- `csmFileLink`: Geef de koppeling op voor de sjabloon die wordt gefaseerd. Gebruik bij het instellen van de waarde variabelen die worden geretourneerd door de Kopieer taak voor het bestand. Het volgende voor beeld is een koppeling naar een sjabloon met de naam mainTemplate.jsop. De map met de naam **sjablonen** is opgenomen, omdat het bestand door de Kopieer taak van het bestand is gekopieerd. Geef in uw pijp lijn het pad naar uw sjabloon en de naam van uw sjabloon op.

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

Uw pijp lijn ziet er als volgt uit:

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Wanneer u **Opslaan** selecteert, wordt de build-pijp lijn automatisch uitgevoerd. Ga terug naar de samen vatting van uw build-pijp lijn en Bekijk de status.

## <a name="next-steps"></a>Volgende stappen

* Als u de bewerking What-if in een pijp lijn wilt gebruiken, raadpleegt u [arm-sjablonen testen met What-If in een pijp lijn](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/).
* Zie [Azure Resource Manager sjablonen implementeren met behulp van github-acties](deploy-github-actions.md)voor meer informatie over het gebruik van arm-sjablonen met github-acties.
