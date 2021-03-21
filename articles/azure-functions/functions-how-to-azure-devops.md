---
title: Functie-app-code continu bijwerken met behulp van Azure DevOps
description: Meer informatie over het instellen van een Azure DevOps-pijp lijn die gericht is op Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: a3f423a144738fdaa4462606de6ad4a4e34d6775
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97563412"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Continue levering met behulp van Azure DevOps

U kunt uw functie automatisch implementeren in een Azure Functions-app met behulp van [Azure-pijp lijnen](/azure/devops/pipelines/).

U hebt twee opties voor het definiëren van uw pijp lijn:

- **Yaml-bestand**: in een yaml-bestand wordt de pijp lijn beschreven. Het bestand bevat mogelijk een sectie voor het maken van stappen en een release sectie. Het YAML-bestand moet zich in dezelfde opslag plaats als de app.
- **Sjabloon**: sjablonen zijn kant-en-klare taken die uw app bouwen of implementeren.

## <a name="yaml-based-pipeline"></a>YAML-pijp lijn

Als u een YAML-pijp lijn wilt maken, bouwt u eerst uw app en implementeert u vervolgens de app.

### <a name="build-your-app"></a>Uw app maken

Hoe u uw app in azure-pijp lijnen bouwt, is afhankelijk van de programmeer taal van uw app. Elke taal heeft specifieke build-stappen voor het maken van een implementatie-artefact. Een implementatie artefact wordt gebruikt om uw functie-app in azure te implementeren.

# <a name="c"></a>[G\#](#tab/csharp)

U kunt het volgende voor beeld gebruiken om een YAML-bestand te maken om een .NET-app te bouwen:

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

U kunt het volgende voor beeld gebruiken om een YAML-bestand te maken om een Java script-app te bouwen:

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

U kunt een van de volgende voor beelden gebruiken om een YAML-bestand te maken voor het bouwen van een app voor een specifieke python-versie. Python wordt alleen ondersteund voor functie-apps die worden uitgevoerd op Linux.

**Versie 3,7**

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

**Versie 3,6**

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

U kunt het volgende voor beeld gebruiken om een YAML-bestand te maken om een Power shell-app te verpakken. Power shell wordt alleen ondersteund voor Windows Azure Functions.

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

U moet een van de volgende YAML-voor beelden in uw YAML-bestand toevoegen, afhankelijk van het besturings systeem dat als host fungeert.

#### <a name="windows-function-app"></a>Windows-functie-app

U kunt het volgende code fragment gebruiken om een Windows-functie-app te implementeren:

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

U kunt het volgende code fragment gebruiken om een Linux-functie-app te implementeren:

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

## <a name="template-based-pipeline"></a>Pijp lijn op basis van een sjabloon

Sjablonen in azure DevOps zijn vooraf gedefinieerde groepen taken die een app bouwen of implementeren.

### <a name="build-your-app"></a>Uw app maken

Hoe u uw app in azure-pijp lijnen bouwt, is afhankelijk van de programmeer taal van uw app. Elke taal heeft specifieke build-stappen voor het maken van een implementatie-artefact. Een implementatie artefact wordt gebruikt om de functie-app in azure bij te werken.

Als u ingebouwde build-sjablonen wilt gebruiken, selecteert u **de klassieke editor gebruiken** om een pijp lijn te maken met behulp van ontwerp sjablonen als u een nieuwe build-pijp lijn maakt.

![De klassieke Azure pipelines-editor selecteren](media/functions-how-to-azure-devops/classic-editor.png)

Nadat u de bron van de code hebt geconfigureerd, zoekt u naar Azure Functions build-sjablonen. Selecteer de sjabloon die overeenkomt met de taal van uw app.

![Een sjabloon voor het maken van een Azure Functions selecteren](media/functions-how-to-azure-devops/build-templates.png)

In sommige gevallen hebben artefacten een specifieke mapstructuur. Mogelijk moet u het selectie vakje **laten voorafgaan door root** -mapnaam voor het archiveren van de paden inschakelen.

![De optie voor het laten voorafgaan door van de naam van de hoofdmap](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Java script-apps

Als uw Java script-app afhankelijk is van Windows systeem eigen modules, moet u de versie van de agent groep bijwerken naar **gehoste VS2017**.

![De versie van de agent groep bijwerken](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Uw app implementeren

Wanneer u een nieuwe release pijplijn maakt, zoekt u naar de Azure Functions release-sjabloon.

![Zoeken naar de sjabloon voor de Azure Functions-release](media/functions-how-to-azure-devops/release-template.png)

Implementeren naar een implementatie site wordt niet ondersteund in de release-sjabloon.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Een build-pijp lijn maken met behulp van de Azure CLI

Als u een build-pijp lijn in azure wilt maken, gebruikt u de `az functionapp devops-pipeline create` [opdracht](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). De build-pijp lijn wordt gemaakt voor het maken en vrijgeven van code wijzigingen die zijn aangebracht in uw opslag plaats. Met de opdracht wordt een nieuw YAML-bestand gegenereerd dat de pijp lijn voor Build en release definieert, waarna het wordt doorgevoerd in uw opslag plaats. De vereisten voor deze opdracht zijn afhankelijk van de locatie van uw code.

- Als uw code zich in GitHub bevindt:

    - U moet **Schrijf** machtigingen hebben voor uw abonnement.

    - U moet de Project beheerder zijn in azure DevOps.

    - U moet gemachtigd zijn om een GitHub-persoonlijk toegangs token (PAT) te maken dat voldoende machtigingen heeft. Zie [GITHUB Pat permission requirements](/azure/devops/pipelines/repos/github#repository-permissions-for-personal-access-token-pat-authentication) (Engelstalig) voor meer informatie.

    - U moet over machtigingen voor het door voeren van de hoofd vertakking in uw GitHub-opslag plaats, zodat u het automatisch gegenereerde YAML-bestand kunt door voeren.

- Als uw code zich in azure opslag plaatsen bevindt:

    - U moet **Schrijf** machtigingen hebben voor uw abonnement.

    - U moet de Project beheerder zijn in azure DevOps.

## <a name="next-steps"></a>Volgende stappen

- Bekijk het [Azure functions overzicht](functions-overview.md).
- Bekijk het [overzicht van Azure DevOps](/azure/devops/pipelines/).