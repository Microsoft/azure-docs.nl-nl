---
title: Continue implementatie inschakelen
description: Meer informatie over het inschakelen van CI/CD-Azure App Service van GitHub, BitBucket, Azure opslag plaatsen of een andere opslag plaatsen. Selecteer de build-pijp lijn die aan uw behoeften voldoet.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 799699662b738804790e3fe18ce9bd579027808d
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616312"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Continue implementatie naar Azure App Service

[Azure app service](overview.md) maakt continue implementatie mogelijk van github-, BitBucket-en [Azure opslag plaatsen](https://azure.microsoft.com/services/devops/repos/) -opslag plaatsen door de nieuwste updates op te halen. In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om uw app voortdurend te implementeren via de kudu build-service of [Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/). 

Zie [een opslag plaats maken (github)], [een opslag plaats (BitBucket)]maken of [een nieuwe Git-opslag plaats maken (Azure opslag plaatsen)]voor meer informatie over de bron beheer Services.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Azure App Service autoriseren 

Als u Azure opslag plaatsen wilt gebruiken, moet u ervoor zorgen dat uw Azure DevOps-organisatie is gekoppeld aan uw Azure-abonnement. Zie [een Azure DevOps Services-account instellen zodat het kan worden geïmplementeerd in een web-app](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true)voor meer informatie.

Autoriseer Azure App Service voor bitbucket of GitHub om verbinding te maken met uw opslag plaats. U hoeft slechts één keer te autoriseren met een broncode beheer service. 

1. Zoek in het [Azure Portal](https://portal.azure.com)naar  **app Services** en selecteer.

   ![Zoeken naar app-Services.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Selecteer de App Service die u wilt implementeren.

   ![Selecteer uw app.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Selecteer op de pagina app het menu **implementatie centrum** in het linkermenu.
   
1. Selecteer op de pagina **implementatie centrum** **github** of **bitbucket**, en selecteer vervolgens **autoriseren**. 
   
   ![Selecteer broncode beheer service en selecteer vervolgens autoriseren.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Meld u indien nodig aan bij de service en volg de autorisatie prompts. 

## <a name="enable-continuous-deployment"></a>Continue implementatie inschakelen 

Nadat u een broncode beheer service hebt geautoriseerd, configureert u uw app voor continue implementatie via de ingebouwde [kudu-app service](#option-1-kudu-app-service) build-server of via [Azure-pijp lijnen](#option-2-azure-pipelines). 

### <a name="option-1-kudu-app-service"></a>Optie 1: kudu App Service

U kunt de ingebouwde kudu-App Service build-server gebruiken om voortdurend te implementeren vanuit GitHub, bitbucket of Azure opslag plaatsen. 

1. Zoek in het [Azure Portal](https://portal.azure.com)naar **app Services** en selecteer vervolgens de app service die u wilt implementeren. 
   
1. Selecteer op de pagina app het menu **implementatie centrum** in het linkermenu.
   
1. Selecteer uw geautoriseerde broncode beheer provider op de pagina **implementatie centrum** en selecteer **door gaan**. Voor GitHub of bitbucket kunt u ook **account wijzigen** selecteren om het geautoriseerde account te wijzigen. 
   
   > [!NOTE]
   > Als u Azure opslag plaatsen wilt gebruiken, moet u ervoor zorgen dat uw Azure DevOps Services-organisatie is gekoppeld aan uw Azure-abonnement. Zie [een Azure DevOps Services-account instellen zodat het kan worden geïmplementeerd in een web-app](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true)voor meer informatie.
   
1. Voor GitHub of Azure opslag plaatsen selecteert u op de pagina **Build** -provider **app service build-service** en selecteert u vervolgens **door gaan**. Bitbucket maakt altijd gebruik van de App Service build-service.
   
   ![Selecteer App Service build-service en selecteer vervolgens door gaan.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Op de pagina **configureren** :
   
   - Voor GitHub, vervolg keuzelijst en selecteer de **organisatie**, **opslag plaats** en **vertakking** die u continu wilt implementeren.
     
     > [!NOTE]
     > Als u geen opslag plaatsen ziet, moet u mogelijk Azure App Service autoriseren in GitHub. Blader naar de GitHub-opslag plaats en ga naar **instellingen**  >  **toepassingen**  >  **geautoriseerde OAuth-apps**. Selecteer **Azure app service** en selecteer vervolgens **verlenen**. Voor organisatie-opslagplaatsen moet u een eigenaar van de organisatie zijn om de machtigingen te verlenen.
     
   - Selecteer voor bitbucket het bitbucket- **team**, de **opslag plaats** en de **vertakking** die u continu wilt implementeren.
     
   - Selecteer voor Azure opslag plaatsen de **Azure DevOps-organisatie**, **het project**, de **opslag plaats** en de **vertakking** die u continu wilt implementeren.
     
     > [!NOTE]
     > Als uw Azure DevOps-organisatie niet wordt weergegeven, controleert u of deze is gekoppeld aan uw Azure-abonnement. Zie [een Azure DevOps Services-account instellen zodat het kan worden geïmplementeerd in een web-app](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true)voor meer informatie.
     
1. Selecteer **Doorgaan**.
   
   ![Vul de gegevens van de opslag plaats in en selecteer door gaan.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Nadat u de build-provider hebt geconfigureerd, controleert u de instellingen op de pagina **samen vatting** en selecteert u vervolgens **volt ooien**.
   
1. Nieuwe door voeringen in de geselecteerde opslag plaats en vertakking worden nu doorlopend geïmplementeerd in uw App Service-app. U kunt de door voeringen en implementaties volgen op de pagina **implementatie centrum** .
   
   ![Door voeringen en implementaties bijhouden in het implementatie centrum](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>Optie 2: Azure-pijp lijnen 

Als uw account over de benodigde machtigingen beschikt, kunt u Azure-pijp lijnen zo instellen dat ze voortdurend worden geïmplementeerd vanuit GitHub of Azure opslag plaatsen. Zie [een web-app implementeren voor Azure-app Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)voor meer informatie over de implementatie via Azure-pijp lijnen.

#### <a name="prerequisites"></a>Vereisten

Om Azure App Service continue levering te maken met behulp van Azure-pijp lijnen, moet uw Azure DevOps-organisatie over de volgende machtigingen beschikken: 

- Uw Azure-account moet machtigingen hebben om te schrijven naar Azure Active Directory en een app-registratie te maken. 
  
- Uw Azure-account moet de rol **eigenaar** hebben in uw Azure-abonnement.

- U moet een beheerder zijn in het Azure DevOps-project dat u wilt gebruiken.

#### <a name="github--azure-pipelines"></a>GitHub en Azure-pijp lijnen

1. Zoek in het [Azure Portal](https://portal.azure.com)naar **app Services** en selecteer vervolgens de app service die u wilt implementeren. 
   
1. Selecteer op de pagina app het menu **implementatie centrum** in het linkermenu.

1. Selecteer **github** als broncode beheer provider op de pagina **implementatie centrum** en selecteer **door gaan**. Voor **github** kunt u **account wijzigen** selecteren om het geautoriseerde account te wijzigen.

    :::image type="content" source="media/app-service-continuous-deployment/deployment-center-src-control.png" alt-text="Scherm afbeelding van de pagina App Service Deployment Center.":::
   
1. Selecteer op de pagina **Build** -provider **Azure-pijp lijnen (preview)** en selecteer vervolgens **door gaan**.

    :::image type="content" source="media/app-service-continuous-deployment/select-build-provider.png" alt-text="Scherm opname van de pagina Deployment Center met Azure-pijp lijnen (preview) geselecteerd.":::
   
1. Selecteer op de pagina **configureren** , in de sectie **code** , de **organisatie**, de **opslag plaats** en de **vertakking** die u continu wilt implementeren en selecteer **door gaan**.
     
     > [!NOTE]
     > Als u geen opslag plaatsen ziet, moet u mogelijk Azure App Service autoriseren in GitHub. Blader naar de GitHub-opslag plaats en ga naar **instellingen**  >  **toepassingen**  >  **geautoriseerde OAuth-apps**. Selecteer **Azure app service** en selecteer vervolgens **verlenen**. Voor organisatie-opslagplaatsen moet u een eigenaar van de organisatie zijn om de machtigingen te verlenen.
       
    Geef in de sectie **Build** de Azure DevOps-organisatie, het project, de taal structuur op die door Azure-pijp lijnen moet worden gebruikt om Build-taken uit te voeren, en selecteer vervolgens **door gaan**.

   :::image type="content" source="media/app-service-continuous-deployment/build-configure.png" alt-text="Scherm afbeelding van de sectie build met voorbeeld tekst in de velden.":::

1. Nadat u de build-provider hebt geconfigureerd, controleert u de instellingen op de pagina **samen vatting** en selecteert u vervolgens **volt ooien**.

   :::image type="content" source="media/app-service-continuous-deployment/summary.png" alt-text="Scherm afbeelding van de pagina implementatie centrum met de knop voor het vernieuwen van gegevens en implementaties.":::
   
1. Nieuwe door voeringen in de geselecteerde opslag plaats en vertakking worden nu doorlopend geïmplementeerd in uw App Service. U kunt de door voeringen en implementaties volgen op de pagina **implementatie centrum** .
   
   ![Door voeringen en implementaties bijhouden in het implementatie centrum](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure opslag plaatsen + Azure-pijp lijnen

1. Zoek in het [Azure Portal](https://portal.azure.com)naar **app Services** en selecteer vervolgens de app service die u wilt implementeren. 
   
1. Selecteer op de pagina app het menu **implementatie centrum** in het linkermenu.

1. Selecteer **Azure opslag plaatsen** als broncode beheer provider op de pagina **implementatie centrum** en selecteer **door gaan**.

    :::image type="content" source="media/app-service-continuous-deployment/deployment-center-src-control.png" alt-text="Scherm afbeelding van de pagina Deployment Center met de selecties voor continue implementatie (CI/CD).":::

1. Selecteer op de pagina **Build** -provider **Azure-pijp lijnen (preview)** en selecteer vervolgens **door gaan**.

    :::image type="content" source="media/app-service-continuous-deployment/azure-pipelines.png" alt-text="Scherm afbeelding van het implementatie centrum met Azure-pijp lijnen (preview).":::

1. Selecteer op de pagina **configureren** , in de sectie **code** , de **organisatie**, de **opslag plaats** en de **vertakking** die u continu wilt implementeren en selecteer **door gaan**.

   > [!NOTE]
   > Als uw bestaande Azure DevOps-organisatie niet wordt weer gegeven, moet u deze mogelijk koppelen aan uw Azure-abonnement. Zie [uw CD release-pijp lijn definiëren](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)voor meer informatie.

   Geef in de sectie **Build** de Azure DevOps-organisatie, het project, de taal structuur op die door Azure-pijp lijnen moet worden gebruikt om Build-taken uit te voeren, en selecteer vervolgens **door gaan**.

   :::image type="content" source="media/app-service-continuous-deployment/build-configure.png" alt-text="Scherm afbeelding van de sectie build waarin de velden Azure DevOps Organization en project worden weer gegeven die met voor beelden zijn gevuld.":::

1. Nadat u de build-provider hebt geconfigureerd, controleert u de instellingen op de pagina **samen vatting** en selecteert u vervolgens **volt ooien**.  
     
   :::image type="content" source="media/app-service-continuous-deployment/summary-azure-pipelines.png" alt-text="Scherm afbeelding met de geselecteerde instellingen op de pagina samen vatting.":::

1. Nieuwe door voeringen in de geselecteerde opslag plaats en vertakking worden nu doorlopend geïmplementeerd in uw App Service. U kunt de door voeringen en implementaties volgen op de pagina **implementatie centrum** .

## <a name="disable-continuous-deployment"></a>Continue implementatie uitschakelen

Als u continue implementatie wilt uitschakelen, selecteert u de **verbinding verbreken** boven aan de pagina van de **implementatie centrum** van uw app.

![Continue implementatie uitschakelen](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Niet-ondersteunde opslag plaatsen gebruiken

Voor Windows-apps kunt u een continue implementatie hand matig configureren vanuit een Git-of mercurial-opslag plaats in de cloud die niet rechtstreeks wordt ondersteund door de portal, zoals [GitLab](https://gitlab.com/). U doet dit door het externe vak op de pagina **implementatie centrum** te kiezen. Zie [continue implementatie instellen met behulp van hand matige stappen](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Veelvoorkomende problemen met doorlopende implementatie onderzoeken](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell gebruiken](/powershell/azure/)
* [Git-documentatie](https://git-scm.com/documentation)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

[Een repo maken (GitHub)]: https://help.github.com/articles/create-a-repo
[Een repo maken (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Een nieuwe Git-opslag plaats maken (Azure opslag plaatsen)]: /azure/devops/repos/git/creatingrepo