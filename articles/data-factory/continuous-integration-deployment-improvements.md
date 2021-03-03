---
title: Automatische publicatie voor continue integratie en levering
description: Meer informatie over het automatisch publiceren voor continue integratie en levering.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 49ec43e59989f3fdad8f5731867953cc7cbb5757
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699705"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatische publicatie voor continue integratie en levering

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Overzicht

Continue integratie is de praktijk van het testen van elke wijziging die in uw code basis wordt doorgevoerd en zo snel mogelijk doorlopende levering volgt de tests die optreden tijdens continue integratie en worden wijzigingen naar een staging-of productie systeem gepusht.

In Azure Data Factory wordt onder continue integratie en levering (CI/CD) de mogelijkheid verstaan dat Data Factory-pijplijnen van de ene omgeving (ontwikkeling, test, productie) naar de andere worden verplaatst. Azure Data Factory gebruikt [Azure Resource Manager sjablonen](../azure-resource-manager/templates/overview.md) voor het opslaan van de configuratie van uw verschillende ADF-entiteiten (pijp lijnen, gegevens sets, data stromen, enzovoort). Er zijn twee aanbevolen methoden om een data factory te promo veren naar een andere omgeving:

- Geautomatiseerde implementatie met behulp van de integratie van Data Factory met [Azure-pijp lijnen](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Een resource manager-sjabloon hand matig uploaden met behulp van Data Factory UX-integratie met Azure Resource Manager.

Zie voor meer informatie [doorlopende integratie en levering in azure Data Factory](continuous-integration-deployment.md).

In dit artikel richten we ons op de continue implementatie verbeteringen en de functie voor het automatisch publiceren van CI/CD.

## <a name="continuous-deployment-improvements"></a>Verbeteringen doorlopende implementatie

Met de functie voor automatisch publiceren kunt u de sjabloon functies *Alles valideren* en exporteren *Azure Resource Manager (arm)* van de ADF UX gebruiken en de logica kan worden gebruikt via een openbaar beschikbaar NPM-pakket [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Zo kunt u deze acties programmatisch activeren in plaats van dat u naar de ADF-gebruikers interface hoeft te gaan en op een knop klikt. Hiermee geeft u uw CI/CD-pijp lijnen een continue integratie-ervaring waar u ook bent.

### <a name="current-cicd-flow"></a>Huidige CI/CD-stroom

1. Elke gebruiker brengt wijzigingen aan in hun privé-branches.
2. Pushen naar Master is niet toegestaan. gebruikers moeten een pull-to-Master maken om wijzigingen aan te brengen.
3. Gebruikers moeten de ADF-gebruikers interface laden en op publiceren klikken om wijzigingen in Data Factory te implementeren en de ARM-sjablonen in de publicatie vertakking te genereren.
4. DevOps release-pijp lijn is geconfigureerd voor het maken van een nieuwe release en het implementeren van de ARM-sjabloon telkens wanneer een nieuwe wijziging wordt gepusht naar de Publish-vertakking.

![Huidige CI/CD-stroom](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Hand matige stap

In de huidige CI/CD-stroom is de UX de intermediair voor het maken van de ARM-sjabloon. Daarom moet een gebruiker naar de ADF-gebruikers interface gaan en hand matig klikken op publiceren om de generatie van de ARM-sjabloon te starten en deze neer te zetten in de publicatie vertakking, wat een bit van een hack is.

### <a name="the-new-cicd-flow"></a>De nieuwe CI/CD-stroom

1. Elke gebruiker brengt wijzigingen aan in hun privé-branches.
2. Pushen naar Master is niet toegestaan. gebruikers moeten een pull-to-Master maken om wijzigingen aan te brengen.
3. **De build van de Azure DevOps-pijp lijn wordt geactiveerd wanneer een nieuwe toewijzing wordt uitgevoerd aan de Master, de resources worden gevalideerd en een ARM-sjabloon wordt gegenereerd als een artefact als de validatie slaagt.**
4. DevOps release-pijp lijn is geconfigureerd om een nieuwe release te maken en de ARM-sjabloon te implementeren telkens wanneer een nieuwe build beschikbaar is. 

![Nieuwe CI/CD-stroom](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Wat is er veranderd?

- We hebben nu een bouw proces met behulp van een DevOps build-pijp lijn.
- De build pijp lijn maakt gebruik van het ADFUtilities NPM-pakket, waarmee alle resources worden gevalideerd en de ARM-sjablonen (één en gekoppelde sjablonen) worden gegenereerd.
- De build-pijp lijn is verantwoordelijk voor het valideren van ADF-resources en het genereren van de ARM-sjabloon in plaats van een ADF-gebruikers interface (de knop publiceren).
- Met de definitie van de DevOps-release wordt deze nieuwe build-pijp lijn nu gebruikt in plaats van het git-artefact.

> [!NOTE]
> U kunt bestaande mechanismen (adf_publish Branch) blijven gebruiken of de nieuwe stroom gebruiken. Beide worden ondersteund. 

## <a name="package-overview"></a>Overzicht van pakket

Er zijn momenteel twee opdrachten beschikbaar in het pakket:
- Een ARM-sjabloon exporteren
- Valideren

### <a name="export-arm-template"></a>Een ARM-sjabloon exporteren

Voer NPM uitvoeren exporteren <rootFolder> <factoryId> [outputFolder] uit om de arm-sjabloon te exporteren met behulp van de resources van een bepaalde map. Met deze opdracht wordt ook een validatie controle uitgevoerd voordat de ARM-sjabloon wordt gegenereerd. Hieronder ziet u een voorbeeld:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- Root folder is een verplicht veld dat aangeeft waar de Data Factory resources zich bevinden.
- FactoryId is een verplicht veld dat de Data Factory-Resource-ID vertegenwoordigt in de indeling: "/Subscriptions/ <subId> /ResourceGroups/ <rgName> /providers/Microsoft.DataFactory/factories/ <dfName> ".
- OutputFolder is een optionele para meter waarmee het relatieve pad wordt opgegeven om de gegenereerde ARM-sjabloon op te slaan.
 
> [!NOTE]
> De gegenereerde ARM-sjabloon is niet gepubliceerd naar de `Live` versie van de Factory. De implementatie moet worden uitgevoerd met behulp van een CI/CD-pijp lijn. 
 

### <a name="validate"></a>Valideren

Voer NPM uit <rootFolder> <factoryId> om de resources van een bepaalde map te valideren. Hieronder ziet u een voorbeeld:
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- Root folder is een verplicht veld dat aangeeft waar de Data Factory resources zich bevinden.
- FactoryId is een verplicht veld dat de Data Factory-Resource-ID vertegenwoordigt in de indeling: "/Subscriptions/ <subId> /ResourceGroups/ <rgName> /providers/Microsoft.DataFactory/factories/ <dfName> ".


## <a name="create-an-azure-pipeline"></a>Een Azure-pijplijn maken

Hoewel NPM-pakketten op verschillende manieren kunnen worden gebruikt, wordt een van de belangrijkste voor delen gebruikt via een [Azure-pijp lijn](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). Bij elke samen voeging in uw Collaboration Branch kan een pijp lijn worden geactiveerd die eerst alle code valideert en vervolgens de ARM-sjabloon exporteert naar een [Build-artefact](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) dat kan worden gebruikt door een release pijplijn. **Hoe het verschilt van het huidige CI/CD-proces is dat u uw release pijplijn op dit artefact wilt aanwijzen in plaats van de bestaande `adf_publish` vertakking.**

Volg de onderstaande stappen om aan de slag te gaan:

1.  Open een Azure DevOps-project en ga naar pijp lijnen. Selecteer nieuwe pijp lijn.

    ![Nieuwe pijplijn](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  Selecteer de opslag plaats waarin u het YAML-script van de pijp lijn wilt opslaan. We raden u aan om deze op te slaan in een *Build* -map binnen dezelfde opslag plaats van uw ADF-resources. Zorg ervoor dat er een **package.js** in het bestand in de opslag plaats staat en dat de naam van het pakket bevat (zoals hieronder wordt weer gegeven).

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.3"
        }
    } 
    ```
    
3.  Selecteer een *starter-pijp lijn*. Als u het YAML-bestand hebt geüpload of samengevoegd (zoals in het onderstaande voor beeld), kunt u ook rechtstreeks op dat punt wijzen en dit bewerken. 

    ![Starter-pijp lijn](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  Voer uw YAML-code in. We raden u aan het YAML-bestand te maken en dit als uitgangs punt te gebruiken.
5.  Opslaan en uitvoeren. Als de YAML wordt gebruikt, wordt deze telkens geactiveerd wanneer de hoofd vertakking wordt bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over doorlopende integratie en levering in Data Factory: 

- [Continue integratie en levering in azure Data Factory](continuous-integration-deployment.md).
