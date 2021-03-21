---
title: 'Zelfstudie: Uw Node.js-app implementeren in Azure Web App met behulp van DevOps Starter voor GitHub Actions'
description: Met DevOps Starter kunt u eenvoudig aan de slag gaan in Azure, en in slechts enkele stappen uw Node.js-app implementeren in Azure Web App.
ms.author: ninallam
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 08/25/2020
author: ninallam
ms.openlocfilehash: 4caac943df33177728997c74c9ec812105efd62c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588774"
---
# <a name="tutorial-deploy-nodejs-app-to-azure-web-app-using-devops-starter-for-github-actions"></a>Zelfstudie: Node.js-app implementeren in Azure Web App met behulp van DevOps Starter voor GitHub Actions

DevOps Starter voor GitHub Actions biedt een vereenvoudigde ervaring waarbij u een voorbeeldtoepassing kunt kiezen om een CI/CD-werkstroom (continue integratie/continue levering) te maken om te implementeren in Azure. 

DevOps Starter doet ook het volgende:
* Automatisch Azure-resources maken, zoals een nieuwe Azure-web-app.
* Een werkstroom maken en configureren in GitHub die een bouwtaak bevat voor CI.
* De werkstroom bevat ook een implementatietaak voor CD. 
* Er wordt een Azure Application Insights-resource gemaakt voor de bewaking.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * DevOps Starter gebruiken om een Node.js-app te implementeren
> * GitHub en een Azure-abonnement configureren 
> * De GitHub-werkstroom onderzoeken
> * Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure
> * Azure Application Insights-bewaking configureren
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

## <a name="use-devops-starter-to-deploy-a-nodejs-app"></a>DevOps Starter gebruiken om een Node.js-app te implementeren

Met DevOps Starter wordt een werkstroom gemaakt in GitHub. U kunt een bestaande GitHub-organisatie gebruiken. DevOps Starter maakt ook Azure-resources, zoals een web-app, in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Typ **DevOps Starter** in het zoekvak en selecteer dit vervolgens. Klik op **Toevoegen** om een nieuw exemplaar te maken.

    ![Het DevOps Starter-dashboard](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Zorg ervoor dat de CI/CD-provider is geselecteerd als **GitHub Actions**.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. Selecteer **Node.js**, en selecteer **Volgende**.

1. Selecteer onder **Een toepassingsframework kiezen** de optie **Express.js**, en selecteer **Volgende**. Het toepassingsframework dat u in een vorige stap hebt gekozen, bepaalt welk type implementatiedoel hier beschikbaar is voor de Azure-service. 

1. Selecteer de **Windows-web-app**, en selecteer **Volgende**.

## <a name="configure-github-and-an-azure-subscription"></a>GitHub en een Azure-abonnement configureren

1. **Autoriseer** GitHub, en selecteer een bestaande GitHub-organisatie. 

1. Voer een naam in voor de **GitHub-opslagplaats**. 

1. Selecteer uw Azure-abonnementsservices. U kunt eventueel ook **Wijzigen** selecteren en vervolgens meer configuratiegegevens invoeren, zoals de locatie van de Azure-resources.
 
1. Voer een web-app-naam in, en selecteer vervolgens **Gereed**. Na enkele minuten is de Azure-web-app gereed. Er is een Node.js-voorbeeldtoepassing ingesteld in een opslagplaats in uw GitHub-organisatie, er is een werkstroom geactiveerd, en de toepassing is geïmplementeerd in de zojuist gemaakte Azure-web-app.

   ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)    

   Nadat dit is voltooid, wordt het DevOps Starter-dashboard weergegeven in Azure Portal. U kunt ook rechtstreeks vanuit **Alle resources** in de Azure-portal naar het dashboard navigeren. 

   Het dashboard biedt inzicht in de GitHub-codeopslagplaats, de CI/CD-werkstroom, en de actieve toepassing in Azure.   

   ![Dashboardweergave](_img/azure-devops-project-nodejs/full-dashboard.png)

In DevOps Starter wordt automatisch een trigger geconfigureerd waarmee codewijzigingen in de opslagplaats worden geïmplementeerd.
    
## <a name="examine-the-github-workflow"></a>De GitHub-werkstroom onderzoeken

In de vorige stap werd in DevOps Starter automatisch een volledige GitHub-werkstroom geconfigureerd. Verken de werkstroom en pas deze zo nodig aan. Voer de volgende stappen uit om vertrouwd te raken met de werkstroom.

1. Selecteer aan de linkerkant van het DevOps Starter-dashboard **GitHub-werkstroom**. Met deze link worden een browsertabblad en de GitHub-werkstroom voor het nieuwe project geopend.
    > [!NOTE]
    > Wijzig de naam van het werkstroombestand niet. De naam van het werkstroombestand moet **devops-starter-workflow.yml** zijn, zodat het dashboard de wijzigingen kan weergeven

1. Het yaml-bestand van de werkstroom bevat alle GitHub Actions die nodig zijn om de toepassing te bouwen en implementeren. Klik op de optie **Bestand bewerken** om het werkstroombestand aan te passen.

1. Klik op het tabblad **Code** van de opslagplaats op **doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie.

1. Op het tabblad **Acties** van de opslagplaats kunt u de geschiedenis bekijken van alle uitvoeringen van de werkstroom van uw opslagplaats.

1. Selecteer de **meest recente uitvoering** om alle taken weer te geven die in de werkstroom zijn uitgevoerd.

1. Klik op de weergave **Taken** om de gedetailleerde logboeken van de uitvoering van de werkstroom te bekijken. De logboeken bevatten nuttige informatie over het implementatieproces. Ze kunnen worden weergegeven tijdens en na de implementaties.

1. Klik op het tabblad **Pull-aanvraag** om alle pull-aanvragen in uw opslagplaats weer te geven

## <a name="commit-code-changes-and-execute-cicd"></a>Codewijzigingen doorvoeren en CI/CD uitvoeren

Met DevOps Starter wordt een opslagplaats gemaakt in GitHub. Ga als volgt te werk om de opslagplaats weer te geven en codewijzigingen aan de brengen in de toepassing:

1. Selecteer aan de linkerkant van het DevOps Starter-dashboard de koppeling voor de hoofdvertakking. Met deze koppeling opent u een weergave met de zojuist gemaakte GitHub-opslagplaats.

1. Als u de kloon-URL van de opslagplaats wilt weergeven, selecteert u **Klonen** in de rechterbovenhoek van de browser. U kunt uw Git-opslagplaats klonen in uw favoriete IDE. In de volgende stappen kunt u de webbrowser gebruiken om codewijzigingen rechtstreeks aan te brengen en door te voeren in de hoofdvertakking.

1. Ga aan de linkerkant van de browser naar het bestand **/Application/views/index.pug**.

1. Selecteer **Bewerken** en breng een wijziging aan in de tekst.
    Wijzig bijvoorbeeld een stuk tekst voor een van de tags.

1. Selecteer **Doorvoeren** en sla de wijzigingen op.

1. Ga in de browser naar het DevOps Starter-dashboard.   
U moet nu een taak in uitvoering zien voor het bouwen van een GitHub-werkstroom. De zojuist aangebrachte wijzigingen worden automatisch aangebracht en geïmplementeerd via een GitHub-werkstroom.

1. Wanneer de implementatie is voltooid, vernieuwt u de toepassing om de wijzigingen te controleren.

## <a name="configure-azure-application-insights-monitoring"></a>Azure Application Insights-bewaking configureren

Met Azure Application Insights kunt u eenvoudig de prestaties en het gebruik van een webtoepassing controleren. Met DevOps Starter wordt automatisch een Application Insights-resource voor de toepassing geconfigureerd. U kunt verder diverse waarschuwingen en bewakingsopties configureren, indien nodig.

1. Ga in Azure Portal naar het dashboard van DevOps Starter. 

1. Selecteer rechtsonder de koppeling **Application Insights** voor de app. Het deelvenster **Application Insights** wordt geopend. Deze weergave bevat controlegegevens over gebruik, prestaties en beschikbaarheid voor uw app.

   ![Het deelvenster Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Selecteer **Tijdsbereik** en selecteer vervolgens **Afgelopen uur**. Selecteer **Bijwerken** om de resultaten te filteren. U kunt nu alle activiteiten van de afgelopen 60 minuten bekijken. 
    
1. Selecteer **x** om het tijdsbereik af te sluiten.

1. Selecteer **Waarschuwingen** en selecteer vervolgens **Metrische waarschuwing toevoegen**. 

1. Voer een naam in voor de waarschuwing.

1. Verken in de vervolgkeuzelijst **Metrische gegevens** de verschillende metrische waarschuwingsgegevens. De standaardwaarschuwing is voor een **serverreactietijd langer dan 1 seconde**. U kunt eenvoudig tal van waarschuwingen configureren om de controlemogelijkheden van uw app te verbeteren.

1. Schakel het selectievakje **Eigenaars, bijdragers en lezers op de hoogte brengen via e-mail** in. U kunt eventueel aanvullende acties uitvoeren wanneer een waarschuwing wordt weergegeven, door een logische app van Azure uit te voeren.

1. Selecteer **OK** om de waarschuwing te maken. Na enkele ogenblikken wordt de waarschuwing als actief weergegeven op het dashboard. 

1. Sluit het gebied **Waarschuwingen** af en ga terug naar het deelvenster **Application Insights**.

1. Selecteer **Beschikbaarheid** en selecteer vervolgens **Test toevoegen**. 

1. Voer een testnaam in en selecteer vervolgens **Maken**. Hiermee maakt u eenvoudige ping-test om de beschikbaarheid van uw toepassing te controleren. Na een paar minuten zijn de resultaten beschikbaar en toont het Application Insights-dashboard een beschikbaarheidsstatus.

## <a name="clean-up-resources"></a>Resources opschonen

Tijdens het testen kunt u voorkomen dat de kosten oplopen door resources op te schonen. U kunt de virtuele Azure-machine en de gerelateerde resources die u in deze zelfstudie hebt gemaakt, verwijderen wanneer u ze niet meer nodig hebt. Gebruik hiervoor de functionaliteit **Verwijderen** op het DevOps Starter-dashboard. 

> [!IMPORTANT]
> Met de volgende procedure worden resources permanent verwijderd. Met de functie *Verwijderen* verwijdert u de gegevens die met het project in DevOps Starter zijn gemaakt in Azure. Als deze zijn verwijderd, kunt u ze niet meer terughalen. Gebruik deze procedure pas nadat u de prompts zorgvuldig hebt gelezen.

1. Ga in Azure Portal naar het dashboard van DevOps Starter.
1. Selecteer in de rechterbovenhoek **Verwijderen**. 
1. Selecteer **Ja** bij de prompt om de resources *definitief te verwijderen*.

U kunt de werkstroom desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere opslagplaatsen gebruiken. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * DevOps Starter gebruiken om een Node.js-app te implementeren
> * GitHub en een Azure-abonnement configureren 
> * De GitHub-werkstroom onderzoeken
> * Wijzigingen doorvoeren in GitHub en automatisch implementeren in Azure
> * Azure Application Insights-bewaking configureren
> * Resources opschonen

Zie voor meer informatie over GitHub Actions en werkstromen:

> [!div class="nextstepaction"]
> [De GitHub-werkstroom aanpassen](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)
