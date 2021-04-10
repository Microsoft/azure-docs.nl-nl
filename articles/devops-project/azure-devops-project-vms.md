---
title: 'Zelfstudie: de ASP.NET-app implementeren op virtuele Azure-machines met behulp van Azure DevOps Starter'
description: Met DevOps Starter kunt u in Azure eenvoudig aan de slag en in slechts enkele stappen een ASP.NET-app implementeren op virtuele Azure-machines.
ms.author: mlearned
manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 3495d0bd2a446b6b3255887d9b4523eb5a70ac53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557315"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-starter"></a>Zelfstudie: de ASP.NET-app implementeren op virtuele Azure-machines met behulp van Azure DevOps Starter

Azure DevOps Starter biedt een vereenvoudigde ervaring waar u uw bestaande code en Git-opslagplaats gebruikt of een voorbeeldtoepassing kiest voor het maken van een CI- (continue integratie) en CD-pijplijn (continue levering) naar Azure. 

DevOps Starter doet ook het volgende:
* Er worden automatisch Azure-resources gemaakt, zoals een nieuwe Azure-VM (virtuele machine).
* Er wordt een release-pijplijn gemaakt en geconfigureerd in Azure DevOps die een build-pijplijn voor CI bevat.
* Er wordt een release-pijplijn voor CD ingesteld. 
* Er wordt een Azure Application Insights-resource gemaakt voor de bewaking.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * DevOps Starter gebruiken om de ASP.NET-app te implementeren
> * Azure DevOps en een Azure-abonnement configureren 
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure
> * Azure Application Insights-bewaking configureren
> * Resources opschonen

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. U kunt er een gratis krijgen via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-deploy-your-aspnet-app"></a>DevOps Starter gebruiken om de ASP.NET-app te implementeren

In DevOps Starter wordt een CI/CD-pijplijn gemaakt in Azure Pipelines. U kunt een nieuwe Azure DevOps-organisatie maken of een bestaande organisatie gebruiken. Met DevOps Projects worden ook Azure-resources gemaakt, zoals virtuele machines, in het Azure-abonnement van uw keuze.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Typ **DevOps Starter** in het zoekvak en selecteer dit vervolgens. Klik op **Toevoegen** om een nieuw exemplaar te maken.

    ![Het DevOps Starter-dashboard](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecteer **.NET** en vervolgens **Volgende**.

1. Selecteer onder **Een toepassingsframework kiezen** de optie **ASP.NET**. Selecteer vervolgens **Volgende**. Het toepassingsframework dat u in een vorige stap hebt gekozen, bepaalt welk type implementatiedoel hier beschikbaar is voor de Azure-service. 

1. Selecteer de virtuele machine en selecteer vervolgens **Volgende**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Azure DevOps en een Azure-abonnement configureren

1. Maak een nieuwe Azure DevOps-organisatie of selecteer een bestaande organisatie. 

1. Voer een naam in voor uw Azure DevOps-project. 

1. Selecteer uw Azure-abonnementsservices. U kunt eventueel ook **Wijzigen** selecteren en vervolgens meer configuratiegegevens invoeren, zoals de locatie van de Azure-resources.
 
1. Voer een naam, gebruikersnaam en wachtwoord in voor de nieuwe virtuele Azure-machine, en selecteer vervolgens **Gereed**. Na enkele minuten is de virtuele Azure-machine klaar. Er wordt een voorbeeld-ASP.NET-toepassing ingesteld in een opslagplaats in uw Azure DevOps-organisatie, er worden een build en een release uitgevoerd, en de toepassing wordt geïmplementeerd op de zojuist gemaakte Azure-VM. 

   Nadat dit is voltooid, wordt het DevOps Starter-dashboard weergegeven in Azure Portal. U kunt ook rechtstreeks vanuit **Alle resources** in de Azure-portal naar het dashboard navigeren. 

   Het dashboard biedt inzicht in uw Azure DevOps-codeopslagplaats, uw CI/CD-pijplijn, en uw actieve toepassing in Azure.   

   ![Dashboardweergave](_img/azure-devops-project-vms/vm-starter-dashboard.png)

In DevOps Starter worden automatisch een CI-build en een releasetrigger geconfigureerd waarmee codewijzigingen in de opslagplaats worden geïmplementeerd. U kunt verder aanvullende opties configureren in Azure DevOps. Selecteer **Bladeren** om de actieve toepassing weer te geven.
    
## <a name="examine-the-ci-pipeline"></a>De CI-pijplijn onderzoeken
 
In DevOps Starter is automatisch een CI/CD-pijplijn geconfigureerd in Azure Pipelines. U kunt de pijplijn verkennen en aanpassen. Ga als volgt te werk om vertrouwd te raken met de build-pijplijn:

1. Selecteer boven in het DevOps Starter-dashboard de optie **Build-pijplijnen**. Op een tabblad in de browser wordt de build-pijplijn voor het nieuwe project weergegeven.

1. Wijs het veld **Status** aan en selecteer het beletselteken (...). Er wordt een menu met verschillende opties weergegeven, bijvoorbeeld om een nieuwe build in de wachtrij te plaatsen, een build te onderbreken of de build-pijplijn te bewerken.

1. Selecteer **Bewerken**.

1. In dit deelvenster kunt u de verschillende taken voor uw build-pijplijn onderzoeken. In de build worden verschillende taken uitgevoerd, zoals het ophalen van bronnen uit de Git-opslagplaats, het herstellen van afhankelijkheden, en het publiceren van uitvoergegevens die worden gebruikt voor implementaties.

1. Selecteer bovenaan de build-pijplijn de naam van de build-pijplijn.

1. Wijzig de naam van de build-pijplijn in een gebruiksvriendelijkere naam. Selecteer **Opslaan en wachtrij** en selecteer **Opslaan**.

1. Selecteer onder de naam van de build-pijplijn de optie **Geschiedenis**. In dit deelvenster ziet u een audittrail van recente wijzigingen voor de build. In Azure DevOps worden alle wijzigingen in de build-pijplijn bijgehouden en krijgt u de mogelijkheid om versies te vergelijken.

1. Selecteer **Triggers**. In DevOps Starter wordt automatisch een CI-trigger gemaakt en met elke doorvoering naar de opslagplaats wordt een nieuwe build gestart. Desgewenst kunt u kiezen of u vertakkingen van het CI-proces wilt opnemen of uitsluiten.

1. Selecteer **Retentie**. Afhankelijk van het scenario kunt u beleidsregels opgeven om een bepaald aantal builds te behouden of te verwijderen.

## <a name="examine-the-cd-pipeline"></a>De CD-pijplijn onderzoeken

In DevOps Starter worden automatisch de benodigde stappen gemaakt en geconfigureerd om vanuit uw Azure DevOps-organisatie te implementeren naar uw Azure-abonnement. Deze stappen omvatten het configureren van een Azure-serviceverbinding om Azure DevOps te verifiëren bij uw Azure-abonnement. Er wordt ook automatisch een CD-pijplijn gemaakt die de CD aan de virtuele machine van Azure verstrekt. Voor meer informatie over de CD-pijplijn van Azure DevOps doet u het volgende:

1. Selecteer **Build en release** en selecteer vervolgens **Releases**.  In DevOps Starter wordt een release-pijplijn gemaakt om implementaties in Azure te beheren.

1. Selecteer het beletselteken (...) naast de release-pijplijn en selecteer **Bewerken**. De release-pijplijn bevat een *pijplijn* die het releaseproces definieert.

1. Onder **Artefacten** selecteert u **Neerzetten**. Met de build-pijplijn die u in de vorige stappen hebt onderzocht, wordt de uitvoer geproduceerd die wordt gebruikt voor het artefact. 

1. Selecteer naast het pictogram **Neerzetten** de optie **Continue implementatietrigger**. Deze release-pijplijn heeft een ingeschakelde CD-trigger op basis waarvan een implementatie wordt uitgevoerd telkens wanneer een nieuw build-artefact beschikbaar is. U kunt de trigger eventueel uitschakelen zodat de implementaties handmatig moeten worden uitgevoerd. 

1. Selecteer aan de linkerkant **Taken** en selecteer uw omgeving. Taken zijn de activiteiten die worden uitgevoerd tijdens het implementatieproces. Deze zijn gegroepeerd in fasen. Deze release-pijplijn bestaat uit twee fasen:

    - De eerste fase bevat een implementatietaak voor de Azure-resourcegroep waarmee twee acties worden uitgevoerd:
     
        - Configureren van de VM voor implementatie
        -   Toevoegen van de nieuwe VM in een Azure DevOps-implementatiegroep. Met de VM-implementatiegroep in Azure DevOps kunt u logische groepen met implementatiedoelcomputers beheren
     
    - In de tweede fase wordt met behulp van een IIS-web-app-beheertaak een IIS-website gemaakt op de VM. Er wordt een tweede IIS-web-app-implementatietaak gemaakt om de site te implementeren.

1. Selecteer aan de rechterkant **Versies weergeven** om een versiegeschiedenis weer te geven.

1. Selecteer het beletselteken (...) naast een versie en selecteer **Openen**. U kunt verschillende menu's verkennen, zoals een versieoverzicht, gekoppelde werkitems en tests.

1. Selecteer **Doorvoeringen**. In deze weergave worden de codedoorvoeringen weergegeven die zijn gekoppeld aan deze implementatie. Vergelijk versies om de doorvoerverschillen tussen implementaties weer te geven.

1. Selecteer **Logboeken**. De logboeken bevatten nuttige informatie over het implementatieproces. U kunt beide weergeven tijdens en na de implementaties.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure 

U bent nu klaar om met een team samen te werken aan de app met behulp van een CI/CD-proces waarmee automatisch uw meest recente werk op uw website wordt geïmplementeerd. Bij elke wijziging in de Git-opslagplaats wordt een build gestart in Azure DevOps, en met een CD-pijplijn wordt een implementatie uitgevoerd in Azure. Volg de procedure in deze sectie of gebruik een andere methode om wijzigingen in de opslagplaats door te voeren. Met de codewijzigingen wordt het CI/CD-proces gestart en worden de nieuwe wijzigingen automatisch geïmplementeerd op de IIS-website op de Azure-VM.

1. Selecteer in het linkerdeelvenster **Code** en ga naar uw opslagplaats.

1. Ga naar de map *Views\Home*, selecteer het beletselteken naast het bestand *Index.cshtml* en selecteer vervolgens **Bewerken**.

1. Breng een wijziging aan in het bestand, bijvoorbeeld door wat tekst toe te voegen in een van de div-tags. 

1. Selecteer in de rechterbovenhoek **Doorvoeren** en selecteer vervolgens nogmaals **Doorvoeren** om de wijziging te pushen. Na een paar seconden wordt er in Azure DevOps een build gestart en wordt er een versie uitgevoerd om de wijzigingen te implementeren. Bewaak de buildstatus in het DevOps Starter-dashboard, of in de browser met uw Azure DevOps-organisatie.

1. Wanneer de release is voltooid, vernieuwt u de toepassing om de wijzigingen te controleren.

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
> Met de volgende procedure worden resources permanent verwijderd. Met de functionaliteit *Verwijderen* verwijdert u de gegevens die door het project in DevOps Starter zijn gemaakt in zowel Azure als Azure DevOps. Als ze zijn verwijderd, kunt u ze niet meer terughalen. Gebruik deze procedure pas nadat u de prompts zorgvuldig hebt gelezen.

1. Ga in Azure Portal naar het dashboard van DevOps Starter.
1. Selecteer in de rechterbovenhoek **Verwijderen**. 
1. Selecteer **Ja** bij de prompt om de resources *definitief te verwijderen*.

U kunt de build- en release-pijplijn desgewenst wijzigen in overeenstemming met de behoeften van uw team. U kunt dit CI/CD-patroon ook als een sjabloon voor uw andere pijplijnen gebruiken. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * DevOps Starter gebruiken om de ASP.NET-app te implementeren
> * Azure DevOps en een Azure-abonnement configureren 
> * De CI-pijplijn onderzoeken
> * De CD-pijplijn onderzoeken
> * Wijzigingen doorvoeren in Azure Repos en automatisch implementeren naar Azure
> * Azure Application Insights-bewaking configureren
> * Resources opschonen

Voor meer informatie over de CI/CD-pijplijn raadpleegt u:

> [!div class="nextstepaction"]
> [Een CD-pijplijn (continue implementatie) met meerdere fasen definiëren](/azure/devops/pipelines/release/define-multistage-release-process)