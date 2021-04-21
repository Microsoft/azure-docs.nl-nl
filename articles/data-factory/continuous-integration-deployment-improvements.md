---
title: Geautomatiseerde publicatie voor continue integratie en levering
description: Meer informatie over automatisch publiceren voor continue integratie en levering.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 5730b0c7e522f7496f578ffebf716957fcaa56b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788925"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Geautomatiseerde publicatie voor continue integratie en levering

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Overzicht

Continue integratie is de praktijk van het automatisch testen van elke wijziging aan de codebasis. Zo vroeg mogelijk volgt continue levering de testen die tijdens continue integratie worden uitgevoerd en pusht wijzigingen naar een faserings- of productiesysteem.

In Azure Data Factory betekent continue integratie en continue levering (CI/CD) dat Data Factory-pijplijnen van de ene omgeving, zoals ontwikkeling, test en productie, naar een andere worden verplaatst. Data Factory gebruikt [Azure Resource Manager-sjablonen (ARM-sjablonen)](../azure-resource-manager/templates/overview.md) voor het opslaan van de configuratie van uw verschillende Data Factory-entiteiten, zoals pijplijnen, gegevenssets en gegevensstromen.

Er zijn twee voorgestelde methoden om een data factory te promoveren naar een andere omgeving:

- Geautomatiseerde implementatie met behulp van de integratie van Data Factory met [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Handmatig een ARM-sjabloon uploaden met behulp van Data Factory integratie van de gebruikerservaring met Azure Resource Manager.

Zie Continue integratie en levering in Azure Data Factory voor [meer Azure Data Factory.](continuous-integration-deployment.md)

Dit artikel is gericht op de continue implementatieverbeteringen en de functie voor automatisch publiceren voor CI/CD.

## <a name="continuous-deployment-improvements"></a>Doorlopende implementatieverbeteringen

De functie voor  automatisch publiceren maakt gebruik van de functies Alle ARM-sjablonen valideren en **EXPORTEREN** uit de Data Factory-gebruikerservaring en maakt de logica bruikbaar via een openbaar beschikbaar npm-pakket. [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) Daarom kunt u deze acties programmatisch activeren in plaats van naar de Data Factory te gaan en handmatig een knop te selecteren. Deze mogelijkheid biedt uw CI/CD-pijplijnen een echtere continue integratie-ervaring.

### <a name="current-cicd-flow"></a>Huidige CI/CD-stroom

1. Elke gebruiker wijzigingen aan in zijn persoonlijke vertakkingen.
1. Pushen naar master is niet toegestaan. Gebruikers moeten een pull-aanvraag maken om wijzigingen aan te brengen.
1. Gebruikers moeten de gebruikersinterface Data Factory  laden en Publiceren selecteren om wijzigingen in de Data Factory te implementeren en de ARM-sjablonen te genereren in de publicatie-vertakking.
1. De DevOps Release-pijplijn is geconfigureerd voor het maken van een nieuwe release en het implementeren van de ARM-sjabloon telkens wanneer een nieuwe wijziging naar de publicatie-vertakking wordt pushen.

![Diagram met de huidige CI/CD-stroom.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Handmatige stap

In de huidige CI/CD-stroom is de gebruikerservaring de intermediair voor het maken van de ARM-sjabloon. Als gevolg hiervan moet een gebruiker naar de Data Factory-gebruikersinterface gaan en handmatig Publiceren selecteren om het genereren van de ARM-sjabloon te starten en deze te verwijderen in de publicatie-vertakking. 

### <a name="the-new-cicd-flow"></a>De nieuwe CI/CD-stroom

1. Elke gebruiker maakt wijzigingen in zijn persoonlijke vertakkingen.
1. Pushen naar master is niet toegestaan. Gebruikers moeten een pull-aanvraag maken om wijzigingen aan te brengen.
1. De build van de Azure DevOps-pijplijn wordt steeds geactiveerd wanneer er een nieuwe door commit wordt uitgevoerd naar de master. Het valideert de resources en genereert een ARM-sjabloon als een artefact als de validatie slaagt.
1. De DevOps Release-pijplijn is geconfigureerd voor het maken van een nieuwe release en het implementeren van de ARM-sjabloon telkens wanneer een nieuwe build beschikbaar is.

![Diagram met de nieuwe CI/CD-stroom.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Wat is er veranderd?

- We hebben nu een build-proces dat gebruikmaakt van een DevOps-build-pijplijn.
- De build-pijplijn maakt gebruik van het NPM-pakket ADFUtilities, waarmee alle resources worden gevalideerd en de ARM-sjablonen worden gegenereerd. Deze sjablonen kunnen enkel en gekoppeld zijn.
- De build-pijplijn is verantwoordelijk voor het valideren Data Factory resources en het genereren van de ARM-sjabloon in plaats van de Data Factory UI **(knop** Publiceren).
- De DevOps-releasedefinitie gebruikt nu deze nieuwe build-pijplijn in plaats van het Git-artefact.

> [!NOTE]
> U kunt het bestaande mechanisme, de vertakking, blijven gebruiken `adf_publish` of u kunt de nieuwe stroom gebruiken. Beide worden ondersteund.

## <a name="package-overview"></a>Overzicht van pakket

Er zijn momenteel twee opdrachten beschikbaar in het pakket:

- Een ARM-sjabloon exporteren
- Valideren

### <a name="export-arm-template"></a>Een ARM-sjabloon exporteren

Voer `npm run start export <rootFolder> <factoryId> [outputFolder]` uit om de ARM-sjabloon te exporteren met behulp van de resources van een bepaalde map. Met deze opdracht wordt ook een validatiecontrole uitgevoerd voordat de ARM-sjabloon wordt gegenereerd. Hier volgt een voorbeeld:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` is een verplicht veld dat vertegenwoordigt waar de Data Factory resources zich bevinden.
- `FactoryId` is een verplicht veld dat de resource Data Factory-id in de notatie `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` vertegenwoordigt.
- `OutputFolder` is een optionele parameter die het relatieve pad opgeeft voor het opslaan van de gegenereerde ARM-sjabloon.
 
> [!NOTE]
> De gegenereerde ARM-sjabloon wordt niet gepubliceerd naar de liveversie van de factory. De implementatie moet worden uitgevoerd met behulp van een CI/CD-pijplijn.
 
### <a name="validate"></a>Valideren

Voer `npm run start validate <rootFolder> <factoryId>` uit om alle resources van een bepaalde map te valideren. Hier volgt een voorbeeld:

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` is een verplicht veld dat vertegenwoordigt waar de Data Factory resources zich bevinden.
- `FactoryId` is een verplicht veld dat de resource Data Factory-id in de notatie `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` vertegenwoordigt.

## <a name="create-an-azure-pipeline"></a>Een Azure-pijplijn maken

NPM-pakketten kunnen op verschillende manieren worden gebruikt, maar een van de belangrijkste voordelen is dat ze worden gebruikt via [Azure Pipeline.](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0) Bij elke samenvoeging in uw samenwerkingstak kan een pijplijn worden geactiveerd die eerst alle code valideert en vervolgens de ARM-sjabloon exporteert naar een [build-artefact](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) dat kan worden gebruikt door een release-pijplijn. Het verschil met het huidige CI/CD-proces is dat u uw release-pijplijn naar dit artefact laat wijzen in plaats *van de bestaande `adf_publish` vertakking*.

Volg deze stappen om aan de slag te gaan:

1.  Open een Azure DevOps-project en ga naar **Pijplijnen.** Selecteer **Nieuwe pijplijn.**

    ![Schermopname van de knop Nieuwe pijplijn.](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Selecteer de opslagplaats waar u het YAML-script voor de pijplijn wilt opslaan. We raden u aan deze op te slaan in een build-map in dezelfde opslagplaats van uw Data Factory resources. Zorg ervoor dat er eenpackage.js *in* de opslagplaats staat die de pakketnaam bevat, zoals wordt weergegeven in het volgende voorbeeld:

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
    
1.  Selecteer **Starter-pijplijn**. Als u het YAML-bestand hebt geüpload of samengevoegd, zoals wordt weergegeven in het volgende voorbeeld, kunt u hier ook rechtstreeks naar wijzen en het bewerken.

    ![Schermopname van de Starter-pijplijn.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

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
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>/ArmTemplate' #replace with the package.json folder
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  Voer uw YAML-code in. U wordt aangeraden het YAML-bestand als uitgangspunt te gebruiken.
1.  Opslaan en uitvoeren. Als u de YAML hebt gebruikt, wordt deze telkens geactiveerd wanneer de main branch wordt bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over continue integratie en levering in Data Factory:

- [Continue integratie en levering in Azure Data Factory](continuous-integration-deployment.md).
