---
title: Een CI/CD-pijplijn maken voor een PWA met GatsbyJS en Azure DevOps Starter
description: Ontdek hoe u een progressieve web-app (PWA) voor Node.js maakt met behulp van GatsbyJS en de vereenvoudigde DevOps Starter-ontwikkelervaring.
ms.prod: devops
ms.technology: devops-cicd
documentationcenter: vs-devops-build
author: mijacobs
ms.author: mijacobs
ms.workload: web
ms.topic: quickstart
ms.date: 03/24/2020
ms.custom: mvc, devx-track-js
ms.openlocfilehash: d6d8a10dffa3801fe343b3b479265fac5612b021
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556559"
---
# <a name="create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-starter"></a>Een CI/CD-pijplijn in Azure Pipelines maken voor Node.js met Azure DevOps Starter

In deze quickstart maakt u een Node.js-PWA (progressieve web-app) met behulp van [GatsbyJS](https://www.gatsbyjs.org/) en de vereenvoudigde versie van Azure DevOps Starter. Wanneer u klaar bent, hebt u een CI-pijplijn (continue integratie) en CD-pijplijn (continue levering) voor uw PWA in Azure Pipelines. Met Azure DevOps Starter kunt u instellen wat u nodig hebt voor het ontwikkelen, implementeren en bewaken.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- Een [Azure DevOps](https://azure.microsoft.com/services/devops/)-organisatie.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In DevOps Starter wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Met DevOps Starter worden ook Azure-resources gemaakt in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com), en selecteer in het linkerdeelvenster **Een resource maken**. 

   ![Een Azure-resource maken in de Azure-portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. Typ **DevOps Starter** in het zoekvak en selecteer dit vervolgens. Klik op **Toevoegen** om een nieuw exemplaar te maken.

    ![Het DevOps Starter-dashboard](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Een voorbeeldtoepassing en Azure-service selecteren

1. Selecteer de Node.js-voorbeeldtoepassing.   

    ![Het Node.js-voorbeeld selecteren](_img/azure-devops-project-nodejs/select-nodejs.png) 

1. Het standaardvoorbeeldframework is **Express.js**. Wijzig de selectie in **Eenvoudige Node.js-app**, en selecteer vervolgens **Volgende**. 

    ![De eenvoudige Node.js-app selecteren](_img/azure-devops-project-nodejs/select-nodejs-framework.png) 

1. De beschikbare implementatiedoelen die beschikbaar zijn in deze stap, worden bepaald door het toepassingsframework dat is geselecteerd in stap 2. In dit voorbeeld is **Windows-web-app** het standaardimplementatiedoel. Laat **Web App for Containers** ingesteld en selecteer **Volgende**.

    ![Het implementatiedoel selecteren](_img/azure-devops-project-nodejs/select-web-server.png)

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Een projectnaam en Azure-abonnement configureren

1. In de laatste stap van de werkstroom voor het maken van DevOps Starter wijst u een projectnaam toe, selecteert u een Azure-abonnement en selecteert u **Gereed**.  

    ![Een projectnaam toewijzen en een abonnement selecteren](_img/azure-devops-project-nodejs/assign-project-name.png)

1. Tijdens het bouwen van het project wordt een overzichtspagina weergegeven, en de toepassing wordt geïmplementeerd in Azure. Na een korte periode wordt een project gemaakt in uw [Azure DevOps-organisatie](https://dev.azure.com/). Dit project bevat een Git-opslagplaats, een kanbanbord, testplannen, en de artefacten die vereist zijn voor de app.  

## <a name="managing-your-project"></a>Uw project beheren

1. Ga naar **Alle resources** en zoek uw DevOps Starter. Selecteer uw **DevOps Starter**.

    ![Azure DevOps-dashboard in de lijst met resources](_img/azure-devops-project-nodejs/devops-starter-list.png)

1. U wordt omgeleid naar een dashboard waarop u toegang hebt tot de startpagina van uw project, de code-opslagplaats, de CI/CD-pijplijn en een koppeling naar de actieve app. Selecteer **Startpagina van project** om de toepassing te bekijken in **Azure DevOps**. Selecteer op een ander browsertabblad **Toepassingseindpunt** om de live-voorbeeld-app te bekijken. We wijzigen dit voorbeeld later om de met GatsbyJS gegenereerde PWA te gebruiken.

    ![Azure DevOps-dashboard](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

1. Vanuit uw Azure DevOps-project kunt u teamleden uitnodigen om samen te werken, en een kanbanbord maken om uw werk bij te houden. Klik [hier](/azure/devops/user-guide/what-is-azure-devops) voor meer informatie.

![Overzicht van Azure DevOps](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>De opslagplaats klonen en uw Gatsby-PWA installeren

Met DevOps Starter wordt een Git-opslagplaats gemaakt in Azure-opslagplaatsen of in GitHub. In dit voorbeeld is een Azure-opslagplaats gemaakt. De volgende stap is om de opslagplaats te klonen en wijzigingen aan te brengen.

1. Selecteer **Opslagplaatsen** in uw **DevOps-project**, en klik vervolgens op **Klonen**.  Er zijn verschillende mechanismen om de Git-opslagplaats te klonen op uw bureaublad.  Kies een optie die geschikt is voor uw ontwikkelervaring.  

    ![De opslagplaats klonen](_img/azure-devops-project-nodejs/clone-the-repo.png)

1. Nadat de opslagplaats is gekloond op het bureaublad, brengt u enkele wijzigingen aan in de Starter-sjabloon. Begin met het installeren van de GatsbyJS CLI via de terminal.

   ```powershell
    npm install -g gatsby
   ```

1. Ga in de terminal naar de hoofdmap van uw opslagplaats. Deze bevat drie mappen die er als volgt uitzien:

    ```powershell
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----        2/23/2020  10:42 PM                Application
    d-----        2/23/2020   3:05 PM                ArmTemplates
    d-----        2/23/2020   3:05 PM                Tests
    ```
    
1. We hebben niet alle bestanden in de toepassingsmap nodig, omdat we deze gaan vervangen door een Gatsby Starter. Voer de volgende opdrachten in de juiste volgorde uit om het aantal mappen te verminderen.
    
    ```powershell
    cp .\Application\Dockerfile .
    rmdir Application
    ```

1. Gebruik de Gatsby CLI om een voorbeeld-PWA te genereren. Voer `gatsby new` uit in de terminal om de PWA-wizard te starten, en selecteer `gatsby-starter-blog` voor de Starter. Dit ziet er ongeveer uit als het volgende voorbeeld:

    ```powershell
    c:\myproject> gatsby new
    √ What is your project called? ... my-gatsby-project
    ? What starter would you like to use? » - Use arrow-keys. Return to submit.
        gatsby-starter-default
        gatsby-starter-hello-world
    >   gatsby-starter-blog
        (Use a different starter)
    ```
    
1. U hebt nu een map met de naam `my-gatsby-project`. Wijzig de naam in `Application` en kopieer de `Dockerfile` hierin.
    
    ```powershell
    mv my-gatsby-project Application
    mv Dockerfile Application
    ```
    
1. Open de Dockerfile in uw favoriete editor en wijzig de eerste regel van `FROM node:8` in `FROM node:12`. Met deze wijziging zorgt u ervoor dat de container Node.js-versie 12.x gebruikt in plaats van versie 8.x. Voor GatsbyJS zijn modernere versies van Node.js vereist.

1. Open vervolgens het package.json-bestand in de toepassingsmap en bewerk het [veld Scripts](https://docs.npmjs.com/files/package.json#scripts) om ervoor te zorgen dat uw ontwikkel- en productieservers luisteren op alle beschikbare netwerkinterfaces (bijvoorbeeld 0.0.0.0) en poort 80. Zonder deze instellingen kan de container-app-service geen verkeer routeren naar de Node.js-app die wordt uitgevoerd in de container. Het veld `scripts` moet er ongeveer uitzien zoals hieronder wordt weergegeven. U moet met name de standaardwaarden voor de doelen `develop`, `serve` en `start` wijzigen.

    ```json
      "scripts": {
        "build": "gatsby build",
        "develop": "gatsby develop  -H 0.0.0.0 -p 80",
        "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
        "start": "npm run serve",
        "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
        "clean": "gatsby clean",
        "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
      }
    ```
    
## <a name="edit-your-cicd-pipelines"></a>CI/CD-pijplijnen bewerken

1. Voordat u de code in de vorige sectie doorvoert, brengt u enkele wijzigingen aan in uw build- en releasepijplijnen. Bewerk de build-pijplijn en werk de Node-taak bij om Node.js-versie 12.x te gebruiken. Stel het veld **Taakversie** in op 1.x en het veld **Versie** op 12.x.

    ![Node.js bijwerken naar 12.x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

1. In deze quickstart maken we geen eenheidstests, en de stappen hiervoor schakelen we uit in de build-pijplijn. Wanneer u tests schrijft, kunt u deze stappen opnieuw inschakelen. Klik met de rechtermuisknop om de taken te selecteren met de labels **Testafhankelijkheden installeren** en **Eenheidstests uitvoeren**, en schakel ze uit.

    ![Build-tests uitschakelen](_img/azure-devops-project-nodejs/disable-build-unittests.png)

1. Bewerk de release-pijplijn.

    ![De release-pijplijn bewerken](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

1. Net als bij de build-pijplijn wijzigt u de Node-taak om 12.x te gebruiken en schakelt u de twee testtaken uit. Uw release moet er ongeveer uitzien als deze schermopname.

    ![Release-pijplijn voltooid](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. Ga aan de linkerkant van de browser naar het bestand **views/index.pug**.

1. Selecteer **Bewerken** en breng vervolgens een wijziging aan in de h2-kop.  Open bijvoorbeeld **Direct aan de slag gaan met Azure DevOps Starter** of breng een andere wijziging aan.

1. Selecteer **Doorvoeren** en sla de wijzigingen op.

1. Ga in de browser naar het DevOps Starter-dashboard.   
Als het goed is, ziet u nu dat er een build wordt gemaakt. De aangebrachte wijzigingen worden automatisch gebouwd en geïmplementeerd via een CI/CD-pijplijn.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Uw wijzigingen doorvoeren en de Azure CI/CD-pijplijn onderzoeken

In de vorige twee stappen hebt u een met Gatsby gegenereerde PWA toegevoegd aan uw Git-opslagplaats, en uw pijplijnen bewerkt om de code te bouwen en te implementeren. We kunnen de code doorvoeren en de voortgang ervan via de build- en release-pijplijn bekijken.

1. Voer vanuit de hoofdmap van de opslagplaats van het project in een terminal de volgende opdrachten uit om de code naar uw Azure DevOps-project te pushen:

    ```powershell
    git add .
    git commit -m "My first Gatsby PWA"
    git push
    ```
    
1. Een build wordt gestart zodra `git push` is voltooid. U kunt de voortgang volgen op het **Azure DevOps-dashboard**.

3. Na enkele minuten zijn de build- en release-pijplijnen voltooid en is de PWA geïmplementeerd in een container. Klik in het dashboard hierboven op de koppeling **Toepassingseindpunt**. U ziet nu een Gatsby Starter-project voor blogs.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt Azure App Service en gerelateerde resources die u hebt gemaakt, verwijderen wanneer u de resources niet meer nodig hebt. Gebruik hiervoor de functionaliteit **Verwijderen** op het DevOps Starter-dashboard.

## <a name="next-steps"></a>Volgende stappen

De build- en release-pijplijnen worden automatisch gemaakt wanneer u het CI/CD-proces configureert. U kunt deze build- en release-pijplijnen desgewenst wijzigen in overeenstemming met de behoeften van uw team. Voor meer informatie over de CI/CD-pijplijn raadpleegt u:

> [!div class="nextstepaction"]
> [CD-proces aanpassen](/azure/devops/pipelines/release/define-multistage-release-process)
