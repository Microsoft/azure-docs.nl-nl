---
title: 'Zelfstudie: ASP.NET-Apps implementeren naar Azure Functions met Azure DevOps Starter'
description: Met Azure DevOps Starter kunt u eenvoudig aan de slag met Azure. Met DevOps starter kunt u in een paar snelle stappen uw ASP.NET-app implementeren naar Azure Functions.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-csharp
ms.openlocfilehash: 3277fefb5615fd492b2859b741ef8e6f991eaf23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91355471"
---
# <a name="deploy-to-azure-functions-with-devops-starter"></a>Implementeren naar Azure Functions met DevOps Starter

Azure DevOps Starter biedt een vereenvoudigde ervaring waar u uw bestaande code en Git-opslagplaats gebruikt of een voorbeeldtoepassing kiest voor het maken van een CI- (Continue integratie) en CD-pijplijn (Continue levering) naar Azure.

DevOps Starter doet ook het volgende:

* Er worden automatisch Azure-resources gemaakt, zoals Azure Functions

* Een CI/CD-release-pijplijn maken en configureren in Azure DevOps

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>* DevOps Starter gebruiken om een ASP.NET-app te implementeren naar Azure Function
>* Azure DevOps en een Azure-abonnement configureren
>* Azure Function onderzoeken
>* De CI-pijplijn onderzoeken
>* De CD-pijplijn onderzoeken
>* Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
>* Resources opschonen

Momenteel zijn de ondersteunde runtimes voor functies **.NET** en **Node. js**. We gebruiken. NET-runtime voor deze zelfstudie om te implementeren naar Azure Functions. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-starter-to-deploy-an-aspnet-app-to-azure-functions"></a>DevOps Starter gebruiken om een ASP.NET-app te implementeren naar Azure Functions

In DevOps Starter wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. DevOps Projects maakt ook Azure-resources, zoals een IoTHub, in het Azure-abonnement van uw keuze.

1. Meld u aan bij [Azure Portal](https://portal.azure.com)

1. Typ **DevOps Starter** in het zoekvak en selecteer dit vervolgens. Klik op **Toevoegen** om een nieuw exemplaar te maken.

    ![Het DevOps Starter-dashboard](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecteer **.NET** en vervolgens **Volgende**. Selecteer bij **Een toepassingsframework kiezen** de optie **ASP.NET** en klik op **Volgende**.

1. Selecteer **Functie-app** en selecteer vervolgens **Volgende**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Azure DevOps en Azure-abonnement configureren

1. Voer een naam in voor uw Azure DevOps-project.

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie.

1. Selecteer uw Azure-abonnement.

1. Als u aanvullende Azure-configuratie-instellingen wilt weergeven en de prijscategorie en locatie wilt bepalen, klikt u op **Extra instellingen**. In dit deelvenster worden verschillende opties voor het configureren van de prijscategorie en de locatie van de Azure-services weergegeven.

1. Verlaat het Azure-configuratiegebied en selecteer **Gereed**.

1. Na enkele minuten is het proces voltooid. Een voorbeeld-ASP.NET-app wordt in een Git-opslagplaats in uw Azure DevOps-organisatie ingesteld, een Functie-app en Application Insights worden gemaakt, een CI/CD-pijplijn wordt uitgevoerd en uw app wordt geïmplementeerd naar Azure.

   Nadat u dit alles hebt voltooid, wordt het dashboard van Azure DevOps Starter in Azure Portal weergegeven. U kunt ook rechtstreeks vanuit **Alle resources** in de Azure-portal naar het dashboard van DevOps Starter gaan.

   Dit dashboard biedt meer inzicht in uw Azure DevOps-codeopslagplaats, uw CI/CD-pijplijn en uw Azure Function. U kunt meer CI/CD-opties configureren in uw Azure DevOps-pijplijn. Selecteer aan de rechterkant **Functie-app** om weer te geven.

## <a name="examine-the-function-app"></a>De Functie-app onderzoeken

DevOps Starter configureert automatisch de functie-app, die u kunt verkennen en aanpassen. Om de functie-app te leren kennen kunt u het volgende doen:

1. Ga naar het DevOps Starter-dashboard.

    ![DevOps Projects-dashboard](_img/azure-devops-project-functions/fapp-dashboard.png)

1. Selecteer aan de rechterkant de functie-app. Er wordt een deelvenster voor de functie-app geopend. In deze weergave kunt u verschillende acties uitvoeren, zoals bewerkingenbewaking, en logboeken doorzoeken.

    ![Functie-app](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

DevOps Starter configureert automatisch een Azure-CI/CD-pijplijn in uw Azure DevOps-organisatie. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de pijplijn:

1. Ga naar het DevOps Starter-dashboard.

1. Klik op de hyperlink onder **Build**. Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

    ![Schermopname van het DevOps Starter-dashboard in de Azure-portal met een pijl die naar een koppeling verwijst onder Build in de CI-/CD-pijplijn.](_img/azure-devops-project-functions/build.png)

1. Selecteer **Bewerken**. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. De build voert verschillende taken uit, zoals het ophalen van broncodes uit de Git-opslagplaats, het bouwen van de toepassing, het uitvoeren van eenheidstesten en het publiceren van uitvoer die wordt gebruikt voor implementaties.

1. Selecteer **Triggers**. In DevOps Starter wordt automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart. Desgewenst kunt u kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van uw build-pipeline in een gebruiksvriendelijkere naam en selecteer **Opslaan** uit het vervolgkeuzemenu **Opslaan en wachtrij**.

1. Selecteer onder de naam van de build-pijplijn de optie **Geschiedenis**. In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. In Azure DevOps worden alle wijzigingen in de build-pipeline bijgehouden en krijgt u de mogelijkheid om versies te vergelijken.

## <a name="examine-the-cd-release-pipeline"></a>De CD-release-pijplijn onderzoeken

In DevOps Starter worden automatisch de benodigde stappen gemaakt en geconfigureerd om vanuit uw Azure DevOps-organisatie te implementeren naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch ook een release-pijplijn gemaakt, die de CD levert aan Azure. Voor meer informatie over de release-pijplijn doet u het volgende:

1. Navigeer naar de **Pijplijnen | Releases**.

1. Klik op **Edit**.

1. Onder **Artefacten** selecteert u **Neerzetten**. Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact.

1. Selecteer **Continue implementatietrigger** rechts van het pictogram **Neerzetten**. Deze release-pijplijn heeft een ingeschakelde CD-trigger die een implementatie uitvoert telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd.

1. Selecteer aan de rechterkant **Versies weergeven** om een versiegeschiedenis weer te geven.

1. Klik op de gewenste release, waarna de pijplijn wordt weergegeven. Klik op een willekeurige omgeving om de release-**samenvatting, doorvoeringen** of gekoppelde **werkitems** te controleren.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken weergeven**. De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-code-changes-and-execute-cicd"></a>Codewijzigingen doorvoeren en CI/CD uitvoeren

> [!NOTE]
> Met de volgende procedure wordt de CI/CD-pijplijn getest door een eenvoudige tekstwijziging aan te brengen.

U bent nu klaar om met een team samen te werken aan de app met behulp van een CI/CD-proces waarmee automatisch uw meest recente werk aan uw Azure Function wordt geïmplementeerd. Bij elke wijziging in de Git-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in de opslagplaats door te voeren. U kunt bijvoorbeeld de Git-opslagplaats in uw favoriete hulpprogramma of IDE klonen en wijzigingen vervolgens naar deze opslagplaats pushen.

1. Selecteer **Opslagplaatsen | Bestanden** in het menu van Azure DevOps, en ga vervolgens naar uw opslagplaats.

1. De opslagplaats bevat al code genaamd **SampleFunctionApp** op basis van de toepassingstaal die u hebt gekozen tijdens het proces van maken. Open het bestand **Application/SampleFunctionApp/Function1. cs**.

1. Selecteer **Bewerken** en breng vervolgens een wijziging aan op **regel 31**. U kunt deze bijvoorbeeld bijwerken naar **Hallo! Welkom bij Azure Functions met gebruik van DevOps Starter**

1. Selecteer in de rechterbovenhoek **Doorvoeren** en selecteer vervolgens nogmaals **Doorvoeren** om de wijziging te pushen.

1. Open het bestand **Application/SampleFunctionApp.Test/Function1TestRunner.cs**. 

1. Selecteer **Bewerken** en breng vervolgens een wijziging aan op **regel 21**. U kunt deze bijvoorbeeld bijwerken naar **Hallo! Welkom bij Azure Functions met gebruik van Azure DevOps Starter**.

     Na een paar seconden wordt er in Azure DevOps een build gestart en wordt er een versie uitgevoerd om de wijzigingen te implementeren. Bewaak de buildstatus via het DevOps Starter-dashboard of in de browser met uw Azure DevOps-organisatie.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de gerelateerde resources die u hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik hiervoor de functionaliteit **Verwijderen** op het DevOps Starter-dashboard.

## <a name="next-steps"></a>Volgende stappen

U kunt de build- en release-pijplijn desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * DevOps Starter gebruiken om een ASP.NET Core-app te implementeren naar Azure Function
> * Azure DevOps en een Azure-abonnement configureren 
> * Azure Function onderzoeken
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
> * Resources opschonen

