---
title: 'Zelfstudie: Een CI/CD-pijplijn voor uw bestaande code maken met behulp van Azure DevOps Starter'
description: Met Azure DevOps Starter kunt u eenvoudig aan de slag met Azure. DevOps Projects helpt u uw eigen code en GitHub-opslagplaats te gebruiken om slechts in enkele stappen een app op een Azure-service te starten.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: mvc
ms.openlocfilehash: c03ba657ac264b72d035f28956354398421ed2f9
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566597"
---
# <a name="create-a-cicd-pipeline-for-github-repo-using-azure-devops-starter"></a>Een CI/CD-pijplijn voor een GitHub-opslagplaats maken met Azure DevOps Starter

Azure DevOps Starter bevat een vereenvoudigd proces voor het maken van een pijplijn voor continue integratie (CI) en continue levering (CD) naar Azure. U kunt uw bestaande code en Git-opslagplaats gebruiken of een voorbeeldtoepassing selecteren.

U gaat het volgende doen:

> [!div class="checklist"]
> * DevOps Starter gebruiken om een CI/CD-pijplijn te maken
> * De toegang tot uw GitHub-opslagplaats configureren en een framework kiezen
> * Azure DevOps en een Azure-abonnement configureren 
> * Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure
> * De CI/CD-pijplijn van Azure Pipelines onderzoeken
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Toegang tot een GitHub- of externe Git-opslagplaats met .NET, Java, PHP, Node.js, Python of statische webcode.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In Azure DevOps Starter wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Met Azure DevOps Starter worden ook Azure-resources gemaakt in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Typ **DevOps Starter** in het zoekvak en selecteer dit vervolgens. Klik op **Toevoegen** om een nieuw exemplaar te maken.

    ![Het DevOps Starter-dashboard](_img/azure-devops-starter-aks/search-devops-starter.png)
    
1. Selecteer **Neem uw eigen code mee** en selecteer **Volgende**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>De toegang tot uw GitHub-opslagplaats configureren en een framework selecteren

1. Selecteer **GitHub** of een externe **Git**-opslagplaats voor uw code. Voor deze zelfstudie selecteert u **GitHub**. Mogelijk moet u zich de eerste keer bij GitHub verifiëren om Azure toegang te geven tot uw GitHub-opslagplaats.

1. Selecteer een **opslagplaats** en een **vertakking** en selecteer vervolgens **Volgende**.

1. Als u Docker-containers gebruikt, wijzigt u **App geconverteerd voor Docker-uitvoering** in **Ja**. Voor deze zelfstudie laat u de instelling staan op **Nee** en selecteert u vervolgens **Volgende**. Als u meer informatie wilt over het gebruik van Docker-containers, houdt u de muisaanwijzer op het pictogram **i**.

   ![Selectie van toepassingsframework in vervolgkeuzemenu](_img/azure-devops-project-github/appframework.png)

1. Selecteer in de vervolgkeuzelijsten een **toepassingsruntime** en een **toepassingsframework** en selecteer vervolgens **Volgende**. Het toepassingsframework bepaalt welk type implementatiedoel beschikbaar is voor de Azure-service.

1. Selecteer een **Azure-service** om de toepassing te implementeren en selecteer vervolgens **Volgende**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren

1. Voer een naam in bij **Projectnaam**.

1. Maak een nieuwe gratis organisatie in **Azure DevOps-organisatie** of selecteer een bestaande organisatie in de vervolgkeuzelijst.

1. Selecteer uw abonnement in **Azure-abonnement** en voer een naam in bij **Web-app** of gebruik de standaardinstelling. Selecteer een **locatie** en selecteer vervolgens **Gereed**. Na enkele minuten wordt het DevOps Starter-implementatieoverzicht weergegeven in Azure Portal.

1. Selecteer **Ga naar resource** om het DevOps Starter-dashboard weer te geven. Maak in de rechterbovenhoek het **project** vast aan uw dashboard voor snelle toegang. Azure DevOps Starter configureert automatisch een CI-build en een versietrigger. Uw code blijft in uw GitHub-opslagplaats of een andere externe opslagplaats, en er wordt een voorbeeld-app ingesteld in een opslagplaats in de **Azure DevOps-organisatie**. Azure DevOps Starter voert de build uit en implementeert de app in Azure.

   ![Azure DevOps Projects-dashboardweergave](_img/azure-devops-project-github/projectsdashboard.png)

1. Het dashboard bevat uw codeopslagplaats, CI/CD-pijplijn en app in Azure. Selecteer aan de rechterkant onder Azure-resources de optie **Bladeren** om de actieve app weer te geven.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure

U kunt nu samen met een team aan uw app werken. Het CI/CD-proces implementeert automatisch uw laatste werk op uw website. Bij elke wijziging in de GitHub-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure.

1. Selecteer **Opslagplaatsen** in uw DevOps Starter-dashboard. Uw GitHub-opslagplaats wordt geopend in een nieuw browsertabblad. Breng een wijziging aan in de toepassing en selecteer **Wijzigingen doorvoeren**.

1. Na enkele ogenblikken wordt een build in Azure Pipelines gestart. U kunt de status van de build in het DevOps Starter-dashboard controleren. U kunt deze ook controleren in uw Azure DevOps-organisatie door het tabblad **Build-pijplijnen** te selecteren in DevOps Starter-dashboard.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>De CI/CD-pijplijn van Azure Pipelines onderzoeken

In Azure DevOps Starter wordt automatisch een CI/CD-pijplijn geconfigureerd in Azure Pipelines. U kunt de pijplijn verkennen en zo nodig aanpassen. Ga als volgt te werk om vertrouwd te raken met de build- en release-pijplijnen:

1. Selecteer in het DevOps Starter-dashboard de optie **Build-pijplijnen**.

1. Nadat uw **Azure Pipelines**-pagina is geopend, ziet u een geschiedenis van de meest recente builds en de status van elke build.

   ![Azure Pipelines-pagina met builds](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. In de rechterbovenhoek van de pagina **Builds** kunt u **Bewerken** selecteren om de huidige build te wijzigen, **Wachtrij** om een nieuwe build toe te voegen of de verticale ellips-knop ( **&#8942;** ) om een menu met meer opties te openen. Selecteer **Bewerken**.

1. In de build worden verschillende taken uitgevoerd, zoals het ophalen van bronnen uit de opslagplaats, het herstellen van afhankelijkheden, en het publiceren van uitvoergegevens voor implementaties. Aan de rechterkant, onder **Naam**, wijzigt u de naam van de build-pijplijn in een beschrijvende naam. Selecteer **Opslaan en wachtrij** en selecteer vervolgens **Opslaan**. Voer een opmerking in en selecteer nogmaals **Opslaan**.

   ![Azure DevOps-pagina met builds](_img/azure-devops-project-github/buildpage.png)

1. Als u een audittrail van recente wijzigingen voor de build wilt weergeven, selecteert u het tabblad **Geschiedenis**.  Azure DevOps houdt alle wijzigingen in de build-pijplijn bij en geeft u de mogelijkheid om versies te vergelijken.

1. Selecteer het tabblad **Triggers**. Azure DevOps Projects maakt automatisch een CI-trigger met enkele standaardinstellingen. U kunt triggers zoals **Continue integratie inschakelen** instellen om een build te maken telkens wanneer u een codewijziging doorvoert. U kunt ook triggers instellen om builds op specifieke tijdstippen uit te voeren.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt Azure App Service en de gerelateerde resources die u in deze zelfstudie hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik de functionaliteit **Verwijderen** op het DevOps Projects-dashboard.

## <a name="next-steps"></a>Volgende stappen

Toen u het CI/CD-proces in deze zelfstudie configureerde, zijn er automatisch een build- en release-pijplijn in Azure DevOps Projects gemaakt. U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team.

Voor meer informatie over de CI/CD-pijplijn raadpleegt u:

> [!div class="nextstepaction"]
> [Een CD-pijplijn (continue implementatie) met meerdere fasen definiëren](/azure/devops/pipelines/release/define-multistage-release-process)

Zie voor meer informatie over toepassingsbewaking:
  
 > [!div class="nextstepaction"]
 > [Wat is Azure Monitor?](../azure-monitor/overview.md)