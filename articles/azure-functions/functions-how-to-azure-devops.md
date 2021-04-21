---
title: Functie-app-code continu bijwerken met Behulp van Azure DevOps
description: Meer informatie over het instellen van een Azure DevOps-pijplijn die is gericht op Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: a99e313a0c3fe9093137d4acaa64e789ef5e10e3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762205"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Continue levering met behulp van Azure DevOps

U kunt uw functie automatisch implementeren in een Azure Functions app met behulp van [Azure Pipelines.](/azure/devops/pipelines/)

U hebt twee opties voor het definiëren van uw pijplijn:

- **YAML-bestand:** in een YAML-bestand wordt de pijplijn beschreven. Het bestand kan een sectie met buildstappen en een releasesectie hebben. Het YAML-bestand moet zich in dezelfde repo als de app.
- **Sjabloon:** Sjablonen zijn kant-en-klare taken waarmee uw app wordt gebouwd of geïmplementeerd.

## <a name="yaml-based-pipeline"></a>Op YAML gebaseerde pijplijn

Als u een op YAML gebaseerde pijplijn wilt maken, moet u eerst uw app bouwen en vervolgens de app implementeren.

### <a name="build-your-app"></a>Uw app maken

Hoe u uw app in Azure Pipelines bouwt, is afhankelijk van de programmeertaal van uw app. Elke taal heeft specifieke buildstappen voor het maken van een implementatieartefact. Een implementatie-artefact wordt gebruikt om uw functie-app in Azure te implementeren.

# <a name="c"></a>[C\#](#tab/csharp)

U kunt het volgende voorbeeld gebruiken om een YAML-bestand te maken voor het bouwen van een .NET-app:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

U kunt het volgende voorbeeld gebruiken om een YAML-bestand te maken voor het bouwen van een JavaScript-app:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

U kunt een van de volgende voorbeelden gebruiken om een YAML-bestand te maken voor het bouwen van een app voor een specifieke Python-versie. Python wordt alleen ondersteund voor functie-apps die worden uitgevoerd op Linux.

**Versie 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**Versie 3.6**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

U kunt het volgende voorbeeld gebruiken om een YAML-bestand te maken om een PowerShell-app te verpakken. PowerShell wordt alleen ondersteund voor Windows Azure Functions.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Uw app implementeren

U moet een van de volgende YAML-voorbeelden opnemen in uw YAML-bestand, afhankelijk van het hosting-besturingssysteem.

#### <a name="windows-function-app"></a>Windows-functie-app

U kunt het volgende codefragment gebruiken om een Windows-functie-app te implementeren:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Linux-functie-app

U kunt het volgende codefragment gebruiken om een Linux-functie-app te implementeren:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Pijplijn op basis van sjablonen

Sjablonen in Azure DevOps zijn vooraf gedefinieerde groepen taken die een app bouwen of implementeren.

### <a name="build-your-app"></a>Uw app maken

Hoe u uw app in Azure Pipelines bouwt, is afhankelijk van de programmeertaal van uw app. Elke taal heeft specifieke buildstappen voor het maken van een implementatieartefact. Een implementatieartefact wordt gebruikt om uw functie-app in Azure bij te werken.

Als u ingebouwde build-sjablonen wilt gebruiken, selecteert u Bij het maken van een nieuwe build-pijplijn de optie De klassieke **editor** gebruiken om een pijplijn te maken met behulp van ontwerpsjablonen.

![Selecteer de klassieke editor van Azure Pipelines](media/functions-how-to-azure-devops/classic-editor.png)

Nadat u de bron van uw code hebt geconfigureerd, zoekt u naar Azure Functions buildsjablonen. Selecteer de sjabloon die overeenkomt met de taal van uw app.

![Een buildsjabloon Azure Functions selecteren](media/functions-how-to-azure-devops/build-templates.png)

In sommige gevallen hebben build-artefacten een specifieke mapstructuur. Mogelijk moet u het selectievakje Naam van de **hoofdmap voor** het archiveren van paden voorbereiden in.

![De optie om de naam van de hoofdmap voor te bereiden](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript-apps

Als uw JavaScript-app afhankelijk is van native Windows-modules, moet u de versie van de agentpool bijwerken **naar Hosted VS2017.**

![De versie van de agentpool bijwerken](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Uw app implementeren

Wanneer u een nieuwe release-pijplijn maakt, zoekt u de sjabloon Azure Functions release.

![Zoek naar de Azure Functions release-sjabloon](media/functions-how-to-azure-devops/release-template.png)

Implementeren naar een implementatiesite wordt niet ondersteund in de releasesjabloon.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Een build-pijplijn maken met behulp van de Azure CLI

Gebruik de opdracht om een build-pijplijn te maken in `az functionapp devops-pipeline create` [Azure.](/cli/azure/functionapp/devops-pipeline#az_functionapp_devops_pipeline_create) De build-pijplijn wordt gemaakt om codewijzigingen die in uw repo zijn aangebracht, te bouwen en vrij te geven. Met de opdracht wordt een nieuw YAML-bestand gegenereerd dat de build- en release-pijplijn definieert en deze vervolgens naar uw repo doorschrijft. De vereisten voor deze opdracht zijn afhankelijk van de locatie van uw code.

- Als uw code in GitHub staat:

    - U moet **schrijfmachtigingen** hebben voor uw abonnement.

    - U moet de projectbeheerder zijn in Azure DevOps.

    - U moet machtigingen hebben om een persoonlijk GitHub-toegangs token (PAT) te maken dat voldoende machtigingen heeft. Zie [GitHub PAT permission requirements (Machtigingsvereisten voor GitHub PAT) voor meer informatie.](/azure/devops/pipelines/repos/github#repository-permissions-for-personal-access-token-pat-authentication)

    - U moet machtigingen hebben om de gegevens door te main branch in uw GitHub-opslagplaats, zodat u het automatisch gegenereerde YAML-bestand kunt commiten.

- Als uw code zich in Azure-repos-

    - U moet **schrijfmachtigingen** hebben voor uw abonnement.

    - U moet de projectbeheerder zijn in Azure DevOps.

## <a name="next-steps"></a>Volgende stappen

- Bekijk het [Azure Functions overzicht](functions-overview.md).
- Bekijk het [Overzicht van Azure DevOps.](/azure/devops/pipelines/)