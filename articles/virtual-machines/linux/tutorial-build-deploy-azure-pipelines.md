---
title: "Zelfstudie: CI/CD naar Azure-VM's met behulp van Azure Pipelines"
description: In deze zelfstudie leert u hoe u continue integratie (CI) en continue implementatie (CD) van een Node.js-app naar Azure-VM's instelt met behulp van een op YAML gebaseerde Azure-pijplijn.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops, devx-track-js
ms.openlocfilehash: 49282bf6cbc7c24b75fbe3f1bbe68bd1fac62ae3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552487"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Zelfstudie: Uw app implementeren naar virtuele Linux-machines in Azure met behulp van Azure DevOps Services en Azure Pipelines

Continue integratie (CI) en continue implementatie (CD) vormen een pijplijn waarmee u uw code na iedere codedoorvoering kunt compileren, vrijgeven en implementeren. Dit document bevat de stappen voor het instellen van een CI/CD-pijplijn voor het uitvoeren van implementaties op meerdere machines met behulp van Azure Pipelines.

Azure Pipelines biedt een volledige set hulpprogramma's voor het automatiseren van CI/CD voor implementaties op virtuele machines, zowel on-premises als in een cloud.

In deze zelfstudie configureert u een op YAML gebaseerde CI/CD-pijplijn om uw app te implementeren naar een [omgeving](/azure/devops/pipelines/process/environments) van Azure Pipelines met virtuele Linux-machines als resources, die allemaal fungeren als webservers om de app uit te voeren.

In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een voorbeeld-app downloaden.
> * Een op YAML gebaseerde CI-pijplijn van Azure Pipelines maken voor het bouwen van de voorbeeld-app.
> * Een omgeving in Azure Pipelines maken voor de virtuele Azure-machines.
> * Een CD-pijplijn van Azure Pipelines maken.
> * Handmatige en door CI geactiveerde implementaties uitvoeren.

## <a name="before-you-begin"></a>Voordat u begint

* Meld u aan bij uw Azure DevOps Services-organisatie ( **https://dev.azure.com/** ). 
  U kunt een [gratis Azure DevOps Services-organisatie](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308) krijgen.

  > [!NOTE]
  > Zie [Verbinding maken met Azure DevOps Services](/azure/devops/organizations/projects/connect-to-projects) voor meer informatie.

*  U hebt een virtuele Linux-machine nodig voor een implementatiedoel.  Zie [Virtuele Linux-machines maken en beheren met de Azure CLI](./tutorial-manage-vm.md) voor meer informatie.

*  Open de binnenkomende poort 80 voor uw virtuele machine. Zie [Netwerkbeveiligingsgroepen maken met Azure Portal](../../virtual-network/tutorial-filter-network-traffic.md) voor meer informatie.

## <a name="get-your-sample-app-code"></a>De voorbeeld-app downloaden

Als u al een app in GitHub hebt die u wilt implementeren, kunt u proberen een pijplijn voor die code te maken.

Als u echter een nieuwe gebruiker bent, is het handiger om onze voorbeeldcode te gebruiken. In dat geval kopieert u deze opslagplaats in GitHub:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic is een [Java Spring Boot](https://spring.io/guides/gs/spring-boot)-toepassing die is gemaakt met behulp van [Maven](https://spring.io/guides/gs/maven/).

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Deze Node.js-app is gemaakt met [Yeoman](https://yeoman.io/learning/index.html). Hierin is gebruikgemaakt van Express, Bower en Grunt. Daarnaast bevat deze een aantal npm-pakketten als afhankelijkheden.
> Het voorbeeld bevat ook een script waarmee Nginx wordt ingesteld en de app wordt geïmplementeerd. Deze wordt uitgevoerd op de virtuele machines. Met name met het script:
> 1. Worden Node, Nginx en PM2 geïnstalleerd.
> 2. Worden Nginx en PM2 geconfigureerd.
> 3. Wordt de Node-app gestart.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Vereisten voor de Linux-VM

De hierboven genoemde voorbeeld-apps zijn getest met Ubuntu 16.04. Het wordt aangeraden om dezelfde versie van de Linux-VM te gebruiken voor deze quickstart.
Volg de aanvullende stappen die hieronder worden beschreven, afhankelijk van de runtime stack die voor de app wordt gebruikt.

#### <a name="java"></a>[Java](#tab/java)

- Voor het implementeren van apps die zijn gebaseerd op Java Spring Boot en Spring Cloud maakt u een Linux-VM in Azure met behulp van [deze](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) sjabloon, die een volledig ondersteunde, op OpenJDK gebaseerde runtime biedt.
- Voor het implementeren van Java-servlets op een Tomcat-server maakt u een Linux-VM met Java 8 met behulp van [deze](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) Azure-sjabloon en [configureert u Tomcat 9. x als een service](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- Voor het implementeren van een op Java EE gebaseerde app gebruikt u een Azure-sjabloon om een [Linux-VM + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) of een [Linux-VM + Java + WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) of een [Linux-VM + Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) + WildFly/JBoss 14 te maken. 


#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Als u een JavaScript-app of een Node.js-app wilt installeren, hebt u een Linux-VM met de Nginx-webserver nodig om de app te implementeren.
Als u nog geen Linux-VM met Nginx hebt, maakt u er nu een in Azure met de stappen uit [dit voorbeeld](./quick-create-cli.md).

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Een Azure Pipelines-omgeving maken met virtuele Azure-machines

Virtuele machines kunnen worden toegevoegd als resources binnen [omgevingen](/azure/devops/pipelines/process/environments) en kunnen worden geselecteerd voor implementaties op meerdere machines. Weergaven van de implementatiegeschiedenis in de omgeving bieden traceerbaarheid van VM naar pijplijn en vervolgens naar doorvoer (commit).

U maakt een omgeving in de hub **Omgevingen** in het gedeelte **Pijplijnen**.
1.  Meld u aan bij uw Azure DevOps-organisatie en navigeer naar uw project.
2.  Navigeer in het project naar de pagina **Pijplijnen**. Kies vervolgens **Omgevingen** en klik op **Een omgeving maken**. Geef een waarde op voor **Naam** (vereist) voor de omgeving en voor **Beschrijving**.
3.  Kies **Virtuele machines** bij **Resource** om dit resourcetype toe te voegen aan de omgeving en klik op **Volgende**.
4.  Kies Besturingssysteem (Windows/Linux) en **PS-registratiescript kopiëren**. 
5.  Voer nu het gekopieerde script uit vanaf een PowerShell-opdrachtprompt met beheerdersrechten op elk van de doel-VM's die u wilt registreren bij deze omgeving.
    > [!NOTE]
    > - Het persoonlijke toegangstoken van de aangemelde gebruiker wordt vooraf ingevoegd in het script. Het token is alleen geldig op de huidige dag, waardoor het gekopieerde script daarna onbruikbaar wordt.
    > - Als op uw VM al een agent wordt uitgevoerd, geeft u een unieke naam op voor 'agent' om deze te registreren bij de omgeving.
6.  Zodra de VM is geregistreerd, wordt deze weergegeven als een omgevingsresource op het tabblad Resources van de omgeving.

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Als u meer VM's wilt toevoegen, kunt u het script opnieuw weergeven en kopiëren door te klikken op Resource toevoegen en Virtuele machines te kiezen als resource. Dit script blijft hetzelfde voor alle VM's die moeten worden toegevoegd aan deze omgeving. 
8.  Elke VM communiceert met Azure Pipelines voor het coördineren van de implementatie van uw app.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. U kunt tags toevoegen aan de VM als onderdeel van het interactieve PowerShell-registratiescript (of) u kunt deze toevoegen aan/verwijderen uit de resourceweergave door te klikken op de drie puntjes aan het einde van elke VM-resource in de weergave Resources.

   Met de tags die u toewijst kunt u de implementatie beperken tot specifieke virtuele machines wanneer de omgeving wordt gebruikt in een implementatietaak. Tags mogen maximaal 256 tekens bevatten, maar er is geen limiet voor het aantal tags dat u kunt gebruiken.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>CI-build-pipeline definiëren

U hebt een build-pipeline voor continue integratie (CI) nodig die uw webtoepassing publiceert, evenals een implementatiescript dat lokaal kan worden uitgevoerd op de Ubuntu-server. Stel een CI-build-pipeline in op basis van de runtime die u wilt gebruiken. 

1. Meld u aan bij uw Azure DevOps-organisatie en navigeer naar uw project.

1. Navigeer in het project naar de pagina **Pijplijnen**. Kies vervolgens de actie om een nieuwe pijplijn te maken.

1. Doorloop de stappen van de wizard door eerst **GitHub** te selecteren als de locatie van de broncode.

1. U wordt mogelijk omgeleid naar GitHub om u aan te melden. Voer in dat geval uw GitHub-referenties in.

1. Wanneer de lijst met opslagplaatsen wordt weergegeven, selecteert u de gewenste opslagplaats met voorbeeld-apps.

1. Azure Pipelines analyseert uw opslagplaats en adviseert een geschikte pijplijnsjabloon.

#### <a name="java"></a>[Java](#tab/java)

Selecteer de **starter**-sjabloon en kopieer het onderstaande YAML-fragment voor het compileren van het Java-project en het uitvoeren van tests met Apache Maven:

```YAML
jobs:
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

Volg de stappen in [Java-app bouwen met Maven](/azure/devops/pipelines/ecosystems/java) voor meer informatie.

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Selecteer de **starter**-sjabloon en kopieer het onderstaande YAML-fragment voor het compileren van een algemeen Node.js-project met npm.

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

Volg de stappen in [Node.js-app bouwen met gulp](/azure/devops/pipelines/ecosystems/javascript) voor meer informatie.

- Bekijk de pijplijn om te zien wat die doet. Zorg ervoor dat alle standaardinvoer geschikt is voor uw code.

- Selecteer **Opslaan en uitvoeren**, selecteer vervolgens **Rechtstreeks doorvoeren naar de hoofdbranch**, en kies ten slotte opnieuw **Opslaan en uitvoeren**.

- Er wordt een nieuwe run gestart. Wacht tot de run is voltooid.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>CD-stappen definiëren voor implementatie naar de Linux-VM

1. Wijzig het YAML-bestand voor de bovenstaande pijplijn en voeg een [implementatietaak](/azure/devops/pipelines/process/deployment-jobs) toe door te verwijzen naar de omgeving en de VM-resources die u eerder hebt gedefinieerd. Gebruik hiervoor de YAML-syntaxis:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web
   ```
2. U kunt specifieke sets virtuele machines selecteren in de omgeving voor de implementatie door de **tags** op te geven die u hebt gedefinieerd voor elke virtuele machine in de omgeving.
[Hier](/azure/devops/pipelines/yaml-schema?tabs=schema#deployment-job) vindt u het volledige YAML-schema voor de implementatietaak.

3. U kunt `runOnce` of `rolling` opgeven als implementatiestrategie. 

   `runOnce` is de eenvoudigste implementatiestrategie, waarbij alle levenscyclus-hooks, te weten `preDeploy` `deploy`, `routeTraffic`en `postRouteTraffic`, eenmaal worden uitgevoerd. Daarna wordt `on:` `success` of `on:` `failure` uitgevoerd.

   Hieronder ziet u het voorbeeldfragment van YAML voor `runOnce`:
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Hieronder ziet u een voorbeeld van het YAML-fragment dat u kunt gebruiken voor het definiëren van een rolling-strategie voor updates van virtuele machines met maximaal vijf doelen in elke iteratie. `maxParallel` bepaalt het aantal doelen waarop de implementatie parallel kan worden uitgevoerd. Bij de selectie wordt rekening gehouden met een absoluut aantal of percentage doelen dat op elk gewenst moment beschikbaar moet blijven, met uitzondering van de doelen waarop wordt geïmplementeerd. De selectie wordt ook gebruikt om de voorwaarden voor een geslaagde of mislukte implementatie te bepalen.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   Bij elke uitvoering van deze taak wordt de implementatiegeschiedenis vastgelegd in de omgeving `<environment name>` die u hebt gemaakt en waarbij u de VM's hebt geregistreerd.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>De pijplijn uitvoeren en traceringsweergaven in de omgeving ophalen
Implementatieweergaven van de omgeving bieden volledige traceerbaarheid van commits en werkitems, evenals een implementatiegeschiedenis voor meerdere pijplijnen per omgeving/resource.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Volgende stappen
- U kunt nu [de pijplijn aanpassen](/azure/devops/pipelines/customize-pipeline) die u zojuist hebt gemaakt.
- Zie [YAML schema reference](/azure/devops/pipelines/yaml-schema) (Naslag YAML-schema) voor informatie over wat u nog meer kunt doen met YAML-pijplijnen.
- Ga naar de volgende zelfstudie voor meer informatie over het implementeren van een LAMP-stack (Linux, Apache, MySQL en PHP).

> [!div class="nextstepaction"]
> [LAMP-stack implementeren](tutorial-lamp-stack.md)
