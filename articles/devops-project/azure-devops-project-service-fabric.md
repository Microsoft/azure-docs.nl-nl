---
title: 'Zelfstudie: de ASP.NET Core-app implementeren in Azure Service Fabric met Azure DevOps Starter'
description: Met Azure DevOps Starter kunt u eenvoudig aan de slag met Azure. Met Azure DevOps Projects kunt u in slechts enkele stappen een ASP.NET Core-app implementeren naar Azure Service Fabric.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 078331c35c76a3cda97398b1a487933a3f0f5ab8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559304"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-starter"></a>Zelfstudie: de ASP.NET Core-app implementeren in Azure Service Fabric met Azure DevOps Starter

Azure DevOps Starter biedt een vereenvoudigde ervaring waar u uw bestaande code en Git-opslagplaats gebruikt of een voorbeeldtoepassing kiest voor het maken van een CI- (continue integratie) en CD-pijplijn (continue levering) naar Azure. 

DevOps Starter doet ook het volgende:

* Er worden automatisch Azure-resources gemaakt, zoals Azure Service Fabric.
* In Azure DevOps wordt een release-pijplijn gemaakt en geconfigureerd zodat een CI/CD-pijplijn wordt ingesteld.
* Er wordt een Azure Application Insights-resource gemaakt voor de bewaking.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een ASP.NET Core-app maken met DevOps Starter en deze implementeren in Service Fabric
> * Azure DevOps en een Azure-abonnement configureren 
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren in Azure
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>Een ASP.NET Core-app maken met DevOps Starter en deze implementeren in Service Fabric

In DevOps Starter wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. In DevOps Starter worden ook Azure-resources gemaakt, zoals een Service Fabric-cluster, in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Typ **DevOps Starter** in het zoekvak en selecteer dit vervolgens. Klik op **Toevoegen** om een nieuw exemplaar te maken.

    ![Het DevOps Starter-dashboard](_img/azure-devops-starter-aks/search-devops-starter.png) 

1. Selecteer **.NET** en vervolgens **Volgende**.

1. Onder **Een toepassingsframework kiezen** selecteert u **ASP.NET Core**. Selecteer daarna **Volgende**.

1. Selecteer **Service Fabric-cluster** en vervolgens **Volgende**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie. 

1. Voer een naam in voor uw Azure DevOps-project. 

1. Selecteer uw Azure-abonnement.

1. Selecteer de koppeling **Wijziging** om aanvullende Azure-configuratie-instellingen weer te geven en de knooppuntgrootte van de virtuele machine en het besturingssysteem voor het Service Fabric-cluster te bepalen. In dit deelvenster vindt u verschillende opties voor het configureren van het type en de locatie van de Azure-services.
 
1. Verlaat het Azure-configuratiegebied en selecteer **Gereed**.  
    Na enkele minuten is het proces voltooid. Een voorbeeld-ASP.NET Core-app wordt in een Git-opslagplaats in uw Azure DevOps-organisatie ingesteld, een Service Fabric-cluster wordt gemaakt, een CI/CD-pijplijn wordt uitgevoerd en uw app wordt geïmplementeerd naar Azure. 

    Nadat u dit alles hebt voltooid, wordt het dashboard van DevOps Starter in Azure Portal weergegeven. U kunt ook rechtstreeks vanuit **Alle resources** in de Azure-portal naar het dashboard van DevOps Starter gaan. 

    Dit dashboard biedt meer inzicht in uw Azure DevOps-codeopslagplaats, uw CI/CD-pijplijn en uw Service Fabric-cluster. U kunt aanvullende opties voor uw CI/CD-pijplijn configureren in Azure-opslagplaatsen. Selecteer aan de rechterkant **Bladeren** om uw actieve app weer te geven.

## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken

In DevOps Starter wordt automatisch een CI/CD-pijplijn geconfigureerd in Azure Pipelines. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de pijplijn:

1. Ga naar het DevOps Starter-dashboard.

1. Selecteer boven in het DevOps Starter-dashboard de optie **Build-pijplijnen**. Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

1. Wijs het veld **Status** aan en selecteer het beletselteken (...). Er wordt een menu met verschillende opties weergegeven, bijvoorbeeld om een nieuwe build in de wachtrij te plaatsen, een build te onderbreken of de build-pijplijn te bewerken.

1. Selecteer **Bewerken**.

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. In de build worden verschillende taken uitgevoerd, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden, en het publiceren van uitvoergegevens die worden gebruikt voor implementaties.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn. 

1. Selecteer onder de naam van de build-pijplijn de optie **Geschiedenis**. In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. In Azure DevOps worden alle wijzigingen in de build-pijplijn bijgehouden en krijgt u de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**. In DevOps Starter wordt automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart. Desgewenst kunt u kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

## <a name="examine-the-cd-pipeline"></a>De CD-pijplijn onderzoeken

In DevOps Starter worden automatisch de benodigde stappen gemaakt en geconfigureerd om vanuit uw Azure DevOps-organisatie te implementeren naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch ook een release-pijplijn gemaakt, die de CD levert aan Azure. Voor meer informatie over de release-pijplijn doet u het volgende:

1. Selecteer **Build en release** en selecteer vervolgens **Releases**. In DevOps Starter wordt een release-pijplijn gemaakt om implementaties in Azure te beheren.

1. Selecteer het beletselteken (...) naast de release-pijplijn en selecteer **Bewerken**. De release-pijplijn bevat een *pijplijn* die het releaseproces definieert.

1. Onder **Artefacten** selecteert u **Neerzetten**. De build-pijplijn die u eerder hebt onderzocht, produceert de uitvoer die wordt gebruikt voor het artefact. 

1. Selecteer **Continue implementatietrigger** rechts van het pictogram **Neerzetten**. Deze release-pijplijn heeft een ingeschakelde CD-trigger die een implementatie uitvoert telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd. 

1. Selecteer aan de rechterkant **Versies weergeven** om een versiegeschiedenis weer te geven.

1. Selecteer het beletselteken (...) naast een versie en selecteer **Openen**. U kunt verschillende menu's verkennen, zoals een versieoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken**. De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure 

 > [!NOTE]
 > Met de volgende procedure wordt de CI/CD-pijplijn getest door een eenvoudige tekstwijziging aan te brengen.

U bent nu klaar om met een team samen te werken aan de app met behulp van een CI/CD-proces waarmee automatisch uw meest recente werk op uw website wordt geïmplementeerd. Bij elke wijziging van de Git-opslagplaats wordt er een build gestart, en worden uw wijzigingen naar Azure geïmplementeerd via een versie. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in de opslagplaats door te voeren. U kunt bijvoorbeeld de Git-opslagplaats in uw favoriete hulpprogramma of IDE klonen en wijzigingen vervolgens naar deze opslagplaats pushen.

1. Selecteer **Code** > **Bestanden** in het menu van Azure DevOps, en ga vervolgens naar uw opslagplaats.

1. Ga naar de map *Views\Home*, selecteer het beletselteken naast het bestand *Index.cshtml* en selecteer vervolgens **Bewerken**.

1. Breng een wijziging aan in het bestand, bijvoorbeeld door wat tekst toe te voegen in een van de div-tags. 

1. Selecteer in de rechterbovenhoek **Doorvoeren** en selecteer vervolgens nogmaals **Doorvoeren** om de wijziging te pushen.  
    Na enkele seconden wordt er een build gestart, waarna er een versie wordt uitgevoerd om de wijzigingen te implementeren. U kunt de buildstatus bewaken op het DevOps Starter-dashboard of in de browser met uw realtime Azure DevOps-logboekregistratie.

1. Nadat de versie is voltooid, vernieuwt u uw app om uw wijzigingen te controleren.

## <a name="clean-up-resources"></a>Resources opschonen

Tijdens het testen kunt u voorkomen dat de kosten oplopen door resources op te schonen. U kunt het Azure Service Fabric-cluster en de gerelateerde resources die u in deze zelfstudie hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik hiervoor de functionaliteit **Verwijderen** op het DevOps Starter-dashboard.

> [!IMPORTANT]
> Met de volgende procedure worden resources permanent verwijderd. Met de functionaliteit *Verwijderen* verwijdert u de gegevens die door het project in DevOps Starter zijn gemaakt in zowel Azure als Azure DevOps. Als ze zijn verwijderd, kunt u ze niet meer terughalen. Gebruik deze procedure pas nadat u de prompts zorgvuldig hebt gelezen.

1. Ga in Azure Portal naar het dashboard van DevOps Starter.
1. Selecteer in de rechterbovenhoek **Verwijderen**. 
1. Selecteer **Ja** bij de prompt om de resources *definitief te verwijderen*.

## <a name="next-steps"></a>Volgende stappen

U kunt de Azure CI/CD-pijplijn desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een ASP.NET Core-app maken met DevOps Starter en deze implementeren in Service Fabric
> * Azure DevOps en een Azure-abonnement configureren 
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Git en automatisch implementeren naar Azure
> * Resources opschonen

Zie voor meer informatie over Service Fabric en microservices:

> [!div class="nextstepaction"]
> [Een microservices-benadering gebruiken voor het compileren van toepassingen](/azure/devops/pipelines/release/define-multistage-release-process)