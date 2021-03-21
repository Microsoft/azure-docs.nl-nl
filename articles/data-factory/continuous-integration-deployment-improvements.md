---
title: Automatische publicatie voor continue integratie en levering
description: Meer informatie over het automatisch publiceren voor continue integratie en levering.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 496d2b6b3d669013c8174e9bc961d0a2f640bed3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462079"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatische publicatie voor continue integratie en levering

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Overzicht

Continue integratie is de praktijk van het automatisch testen van elke wijziging aan de codebasis. Zo snel mogelijk volgt continue levering de tests die zich voordoen tijdens een continue integratie en worden wijzigingen naar een staging-of productie systeem gepusht.

In Azure Data Factory, continue integratie en continue levering (CI/CD) betekent dat Data Factory pijp lijnen van de ene omgeving, zoals ontwikkeling, test en productie, naar een andere worden verplaatst. Data Factory gebruikt [Azure Resource Manager sjablonen (arm-sjablonen)](../azure-resource-manager/templates/overview.md) voor het opslaan van de configuratie van uw verschillende Data Factory-entiteiten, zoals pijp lijnen, gegevens sets en data stromen.

Er zijn twee aanbevolen methoden om een data factory te promo veren naar een andere omgeving:

- Geautomatiseerde implementatie met de integratie van Data Factory met [Azure-pijp lijnen](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Hand matig een ARM-sjabloon uploaden met behulp van de integratie van Data Factory gebruikers ervaring met Azure Resource Manager.

Zie voor meer informatie [doorlopende integratie en levering in azure Data Factory](continuous-integration-deployment.md).

Dit artikel richt zich op de continue implementatie verbeteringen en de functie voor het automatisch publiceren van CI/CD.

## <a name="continuous-deployment-improvements"></a>Verbeteringen doorlopende implementatie

De functie voor automatisch publiceren maakt gebruik van de functies voor het **valideren van alles** en het exporteren van **arm-sjablonen** van de Data Factory gebruikers ervaring, waardoor de logica kan worden verbruikt via een openbaar beschikbaar NPM-pakket [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Daarom kunt u deze acties programmatisch activeren in plaats van naar de Data Factory-gebruikers interface te gaan en hand matig een knop te selecteren. Deze mogelijkheid geeft uw CI/CD-pijp lijnen een continue integratie-ervaring waar u zich ook kunt voordoen.

### <a name="current-cicd-flow"></a>Huidige CI/CD-stroom

1. Elke gebruiker brengt wijzigingen aan in hun privé-branches.
1. Pushen naar Master is niet toegestaan. Gebruikers moeten een pull-aanvraag maken om wijzigingen aan te brengen.
1. Gebruikers moeten de Data Factory-gebruikers interface laden en vervolgens **publiceren** selecteren om wijzigingen in Data Factory te implementeren en de arm-sjablonen te genereren in de vertakking publiceren.
1. De DevOps release-pijp lijn is geconfigureerd voor het maken van een nieuwe release en het implementeren van de ARM-sjabloon telkens wanneer een nieuwe wijziging wordt gepusht naar de Publish-vertakking.

![Diagram waarin de huidige CI/CD-stroom wordt weer gegeven.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Hand matige stap

In de huidige CI/CD-stroom is de gebruikers ervaring de tussen persoon om de ARM-sjabloon te maken. Als gevolg hiervan moet een gebruiker naar de gebruikers interface van Data Factory gaan en hand matig **publiceren** selecteren om het genereren van arm-sjablonen te starten en deze neer te zetten in de vertakking publiceren.

### <a name="the-new-cicd-flow"></a>De nieuwe CI/CD-stroom

1. Elke gebruiker brengt wijzigingen aan in hun privé-branches.
1. Pushen naar Master is niet toegestaan. Gebruikers moeten een pull-aanvraag maken om wijzigingen aan te brengen.
1. De build van de Azure DevOps-pijp lijn wordt geactiveerd wanneer een nieuwe commit-bewerking wordt uitgevoerd op de hoofd server. De resources worden gevalideerd en er wordt een ARM-sjabloon gegenereerd als een artefact als de validatie slaagt.
1. De DevOps release-pijp lijn is geconfigureerd voor het maken van een nieuwe release en het implementeren van de ARM-sjabloon telkens wanneer een nieuwe build beschikbaar is.

![Diagram waarin de nieuwe CI/CD-stroom wordt weer gegeven.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Wat is er veranderd?

- We hebben nu een bouw proces dat gebruikmaakt van een DevOps build-pijp lijn.
- De build pijp lijn maakt gebruik van het ADFUtilities NPM-pakket, waarmee alle bronnen worden gevalideerd en de ARM-sjablonen worden gegenereerd. Deze sjablonen kunnen één en een koppeling zijn.
- De build-pijp lijn is verantwoordelijk voor het valideren van Data Factory resources en het genereren van de ARM-sjabloon in plaats van de Data Factory gebruikers interface (de knop **publiceren** ).
- In de DevOps-release definitie wordt nu deze nieuwe build-pijp lijn gebruikt in plaats van het git-artefact.

> [!NOTE]
> U kunt het bestaande mechanisme blijven gebruiken, de `adf_publish` vertakking, of u kunt de nieuwe stroom gebruiken. Beide worden ondersteund.

## <a name="package-overview"></a>Overzicht van pakket

Er zijn momenteel twee opdrachten beschikbaar in het pakket:

- Een ARM-sjabloon exporteren
- Valideren

### <a name="export-arm-template"></a>Een ARM-sjabloon exporteren

Voer uit `npm run start export <rootFolder> <factoryId> [outputFolder]` om de arm-sjabloon te exporteren met behulp van de resources van een bepaalde map. Met deze opdracht wordt ook een validatie controle uitgevoerd voordat de ARM-sjabloon wordt gegenereerd. Hier volgt een voorbeeld:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` is een verplicht veld dat aangeeft waar de Data Factory resources zich bevinden.
- `FactoryId` is een verplicht veld dat de Data Factory Resource-ID in de notatie vertegenwoordigt `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .
- `OutputFolder` is een optionele para meter waarmee het relatieve pad wordt opgegeven om de gegenereerde ARM-sjabloon op te slaan.
 
> [!NOTE]
> De gegenereerde ARM-sjabloon wordt niet gepubliceerd naar de live-versie van de Factory. De implementatie moet worden uitgevoerd met behulp van een CI/CD-pijp lijn.
 
### <a name="validate"></a>Valideren

Voer uit `npm run start validate <rootFolder> <factoryId>` om alle resources van een bepaalde map te valideren. Hier volgt een voorbeeld:

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` is een verplicht veld dat aangeeft waar de Data Factory resources zich bevinden.
- `FactoryId` is een verplicht veld dat de Data Factory Resource-ID in de notatie vertegenwoordigt `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .

## <a name="create-an-azure-pipeline"></a>Een Azure-pijplijn maken

Hoewel NPM-pakketten op verschillende manieren kunnen worden gebruikt, wordt een van de belangrijkste voor delen gebruikt via [Azure pipeline](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). Bij elke samen voeging in uw Collaboration Branch kan een pijp lijn worden geactiveerd die eerst alle code valideert en vervolgens de ARM-sjabloon exporteert naar een [Build-artefact](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) dat kan worden gebruikt door een release pijplijn. Hoe het verschilt van het huidige CI/CD-proces is dat u *uw release pijplijn op dit artefact wilt aanwijzen in plaats van de bestaande `adf_publish` vertakking*.

Volg deze stappen om aan de slag te gaan:

1.  Open een Azure DevOps-project en ga naar **pijp lijnen**. Selecteer **nieuwe pijp lijn**.

    ![Scherm opname van de knop nieuwe pijp lijn.](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Selecteer de opslag plaats waarin u het YAML-script van de pijp lijn wilt opslaan. We raden u aan om deze op te slaan in een build-map in dezelfde opslag plaats van uw Data Factory-resources. Zorg ervoor dat er een *package.jsis voor* het bestand in de opslag plaats met de pakket naam, zoals wordt weer gegeven in het volgende voor beeld:

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
    
1.  Selecteer een **starter-pijp lijn**. Als u het YAML-bestand hebt geüpload of samengevoegd, zoals wordt weer gegeven in het volgende voor beeld, kunt u ook rechtstreeks op dat punt wijzen en dit bewerken.

    ![Scherm opname van de starter-pijp lijn.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

    ```yaml
    # Sample YAML file to validate and export an ARM template into a build artifact
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
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  Voer uw YAML-code in. We raden u aan het YAML-bestand als uitgangs punt te gebruiken.
1.  Opslaan en uitvoeren. Als u de YAML hebt gebruikt, wordt deze geactiveerd wanneer de hoofd vertakking wordt bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over doorlopende integratie en levering in Data Factory:

- [Continue integratie en levering in azure Data Factory](continuous-integration-deployment.md).
