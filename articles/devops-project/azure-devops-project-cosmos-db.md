---
title: 'Zelfstudie: Door Azure Cosmos DB aangedreven Node.js-apps implementeren met Azure DevOps Starter'
description: Met Azure DevOps Starter kunt u eenvoudig aan de slag met Azure. Met DevOps Starter kunt u in een paar snelle stappen uw door Azure Cosmos DB aangedreven Node.js-app naar Windows Web-app implementeren.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-js
ms.openlocfilehash: d1bdae976d5e5b7d98311d33bb38b5035f2557ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548696"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-starter"></a>Door Azure Cosmos DB aangedreven Node.js-apps implementeren met DevOps Starter

Azure DevOps starter biedt een gestroomlijnde ervaring waarin u een pijplijn voor doorlopende integratie (CI) en doorlopende implementatie (CD) kunt maken naar Azure. U kunt dit doen met behulp van uw bestaande code en Git-opslagplaats of door een voorbeeldtoepassing te selecteren.

DevOps Starter doet ook het volgende:

* Automatisch Azure-resources maken, zoals Azure Cosmos DB, Azure Application Insights, Azure App Service en App Service-plannen

* Een CI/CD-release-pijplijn maken en configureren in Azure DevOps

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * DevOps gebruiken om een Node.js-app te implementeren die wordt aangedreven door Azure Cosmos DB
> * Azure DevOps en een Azure-abonnement configureren
> * Azure Cosmos DB onderzoeken
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * De wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig. U kunt er gratis een krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-nodejs-app"></a>DevOps Starter gebruiken om Node.js-app te implementeren

In DevOps Starter wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. DevOps Starter maakt ook Azure-resources, zoals Azure Cosmos DB, Application Insights, App Service en App Service-plannen, in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Typ **DevOps Starter** in het zoekvak en selecteer dit vervolgens. Klik op **Toevoegen** om een nieuw exemplaar te maken.

    ![Het DevOps Starter-dashboard](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecteer **Node.js** als runtime en selecteer vervolgens **Volgende**. Selecteer bij **Een toepassingsframework kiezen** de optie **Express.js**.

1. Schakel de sectie **Een database toevoegen** in voor **Cosmos DB** en selecteer vervolgens **Volgende**.

    ![Een database toevoegen](_img/azure-devops-project-cosmos-db/add-database.png)

    Azure DevOps Starter ondersteunt verschillende toepassingsframeworks, zoals **Express.js**, **Voorbeeld-Node.js-app** en **Sail.js**. In deze zelfstudie gebruiken we **Express.js**.

1. Selecteer een Azure-service om de toepassing te implementeren en selecteer vervolgens **Volgende**. Uw opties zijn onder andere Windows Web App, Azure Kubernetes Service en Azure Web App for Containers. In deze zelfstudie gebruiken we **Windows Web App**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Azure DevOps en Azure-abonnement configureren

1. Voer een naam in voor uw Azure DevOps-project.

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie.

1. Selecteer uw Azure-abonnement.

1. Als u aanvullende Azure-configuratie-instellingen wilt weergeven of de prijscategorie en locatie wilt bepalen, selecteert u **Extra instellingen**. In dit deelvenster ziet u verschillende opties voor het configureren van de prijscategorie en de locatie van de Azure-services.

1. Verlaat het Azure-configuratiegebied en selecteer **Gereed**.

1. Het proces is na een paar minuten voltooid. Een voorbeeld van een Node.js-app is ingesteld in een Git-opslagplaats in uw Azure DevOps-organisatie. Daarna worden Azure Cosmos DB, App Service, een App Service-plan en Application Insights-resources gemaakt, evenals een CI/CD-pijplijn. Uw app wordt vervolgens geïmplementeerd naar Azure.

   Wanneer al deze processen zijn voltooid, wordt het Azure DevOps Starter-dashboard weergegeven in de Azure-portal. U kunt ook rechtstreeks vanuit **Alle resources** in de Azure-portal naar het dashboard van DevOps Starter gaan.

   Dit dashboard biedt meer inzicht in uw Azure DevOps-codeopslagplaats, uw CI/CD-pijplijn en uw Azure Cosmos DB-database. U kunt meer CI/CD-opties configureren in uw Azure DevOps-pijplijn. Selecteer aan de rechterkant van het dashboard **Azure Cosmos DB** om deze opties weer te geven.

## <a name="examine-azure-cosmos-db"></a>Azure Cosmos DB onderzoeken

DevOps Starter configureert automatisch een exemplaar van Azure Cosmos DB, dat u kunt verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met Azure Cosmos DB:

1. Ga naar het DevOps Starter-dashboard.

    ![DevOps Projects-dashboard](_img/azure-devops-project-cosmos-db/devops-starter-dashboard.png)

1. Selecteer aan de rechterkant Azure Cosmos DB. Er wordt nu een deelvenster geopend voor Azure Cosmos DB. In deze weergave kunt u verschillende acties uitvoeren, zoals bewerkingen bewaken en logboeken doorzoeken.

    ![Azure Cosmos DB-deelvenster](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

DevOps Starter configureert automatisch een Azure-CI/CD-pijplijn in uw Azure DevOps-organisatie. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de pijplijn:

1. Ga naar het DevOps Starter-dashboard.

1. Selecteer de hyperlink onder **Build**. Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

    ![Deelvenster Build](_img/azure-devops-project-cosmos-db/build.png)

1. Selecteer **Bewerken**. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. De build voert verschillende taken uit, zoals het ophalen van broncodes uit de Git-opslagplaats, het bouwen van de toepassing, het uitvoeren van eenheidstesten en het publiceren van uitvoer die wordt gebruikt voor implementaties.

1. Selecteer **Triggers**. In DevOps Starter wordt automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart. U kunt kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van uw build-pipeline in een gebruiksvriendelijkere naam en selecteer **Opslaan** uit het vervolgkeuzemenu **Opslaan en wachtrij**.

1. Selecteer onder de naam van de build-pijplijn de optie **Geschiedenis**. In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. In Azure DevOps worden alle wijzigingen in de build-pijplijn bijgehouden en krijgt u de mogelijkheid om versies te vergelijken.

## <a name="examine-the-cd-release-pipeline"></a>De CD-release-pijplijn onderzoeken

In DevOps Starter worden automatisch de benodigde stappen gemaakt en geconfigureerd om vanuit uw Azure DevOps-organisatie te implementeren naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch ook een release-pijplijn gemaakt, die de CD levert aan Azure. Voor meer informatie over de release-pijplijn doet u het volgende:

1. Ga naar **Pijplijnen** en selecteer **Releases**.

1. Selecteer **Bewerken**.

1. Onder **Artefacten** selecteert u **Neerzetten**. Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact.

1. Selecteer rechts van het pictogram **Neerzetten** de optie **Continue implementatietrigger**. Deze release-pijplijn heeft een ingeschakelde CD-trigger die een implementatie uitvoert telkens wanneer er een nieuw build-artefact beschikbaar is. U kunt de trigger uitschakelen als u de implementaties handmatig wilt uitvoeren.

1. Selecteer aan de rechterkant de sectie **Versies weergeven** om de versiegeschiedenis weer te geven.

1. Selecteer de gewenste release, waarna de pijplijn wordt weergegeven. Selecteer een willekeurige omgeving om de release-samenvatting, doorvoeringen of gekoppelde werkitems te controleren.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken weergeven**. De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>De codewijzigingen doorvoeren en de CI/CD-pijplijn uitvoeren

> [!NOTE]
> Met de volgende procedure wordt de CI/CD-pijplijn getest door een eenvoudige tekstwijziging aan te brengen.

U bent nu klaar om met een team samen te werken aan de app met behulp van een CI/CD-proces waarmee uw meest recente werk aan uw App Service wordt geïmplementeerd. Bij elke wijziging in de Git-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in de opslagplaats door te voeren. U kunt bijvoorbeeld de Git-opslagplaats in uw favoriete hulpprogramma of IDE klonen en wijzigingen vervolgens naar deze opslagplaats pushen.

1. Selecteer in het menu van Azure DevOps **Opslagplaatsen** en **Bestanden**. Ga vervolgens naar uw opslagplaats.

1. De opslagplaats bevat al code op basis van de toepassingstaal die u hebt gekozen tijdens het proces van maken. Open het bestand **Application/views/index.pug**.

1. Selecteer **Bewerken** en breng vervolgens een wijziging aan op **regel 15**. U kunt deze bijvoorbeeld wijzigen in 'Mijn eerste implementatie naar Azure App Service die wordt aangedreven door Azure Cosmos DB'.

1. Selecteer in de rechterbovenhoek **Doorvoeren** en selecteer vervolgens nogmaals **Doorvoeren** om de wijziging te pushen.

     Na een paar seconden wordt er in Azure DevOps een build gestart en wordt er een versie uitgevoerd om de wijzigingen te implementeren. Bewaak de buildstatus via het DevOps Starter-dashboard of in de browser met uw Azure DevOps-organisatie.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de gerelateerde resources die u hebt gemaakt als u deze niet meer nodig hebt. Gebruik hiervoor de functionaliteit **Verwijderen** op het DevOps Starter-dashboard.

## <a name="next-steps"></a>Volgende stappen

U kunt deze build- en release-pipelines desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * DevOps gebruiken om een Node.js-app te implementeren die wordt aangedreven door Azure Cosmos DB
> * Azure DevOps en een Azure-abonnement configureren 
> * Azure Cosmos DB onderzoeken
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
> * Resources opschonen

Raadpleeg [Een CD-pijplijn (continue implementatie) met meerdere fasen definiëren](/azure/devops/pipelines/release/define-multistage-release-process) voor meer informatie en de volgende stappen.