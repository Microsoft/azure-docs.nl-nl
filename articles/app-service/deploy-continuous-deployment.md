---
title: Continue implementatie inschakelen
description: Meer informatie over het inschakelen van CI/CD-Azure App Service van GitHub, BitBucket, Azure opslag plaatsen of een andere opslag plaatsen. Selecteer de build-pijp lijn die aan uw behoeften voldoet.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/03/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 8dc290ed59a7738a1e2263b4203ab0d5be338ec8
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122269"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Continue implementatie naar Azure App Service

[Azure app service](overview.md) maakt continue implementatie mogelijk van [github](https://help.github.com/articles/create-a-repo)-, [BitBucket](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)-en [Azure opslag plaatsen](/azure/devops/repos/git/creatingrepo) -opslag plaatsen door de nieuwste updates op te halen.

> [!NOTE]
> De pagina **Development Center (klassiek)** in de Azure Portal, die de oude implementatie-ervaring is, wordt in maart 2021 afgeschaft. Deze wijziging is niet van invloed op bestaande implementatie-instellingen in uw app en u kunt de implementatie van apps blijven beheren op de pagina **implementatie centrum** .

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Implementatie bron configureren

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar de beheer pagina voor uw app service-app.

1. Klik in het menu links op instellingen voor het **implementatie centrum**  >  . 

1. Selecteer in **bron** een van de opties voor CI/cd.

    ![Laat zien hoe u implementatie bron kiest in implementatie centrum voor Azure App Service](media/app-service-continuous-deployment/choose-source.png)

Kies het tabblad dat overeenkomt met uw selectie voor de stappen.

# <a name="github"></a>[GitHub](#tab/github)

4. [Github-acties](#how-the-github-actions-build-provider-works) is de standaard provider voor builds. Als u deze wilt wijzigen, klikt u op **Change provider**  >  **app service build service** (kudu) > **OK**.

    > [!NOTE]
    > Als u Azure-pijp lijnen wilt gebruiken als de build-provider voor uw App Service-app, moet u deze niet configureren in App Service. Configureer in plaats daarvan de CI/CD rechtstreeks vanuit Azure-pijp lijnen. Met de optie voor **Azure-pijp lijnen** wordt u alleen naar de juiste richting verwezen.

1. Als u voor de eerste keer implementeert vanaf GitHub, klikt u op **autoriseren** en volgt u de autorisatie prompts. Als u wilt implementeren vanuit de opslag plaats van een andere gebruiker, klikt u op **account wijzigen**.

1. Wanneer u uw Azure-account met GitHub hebt geautoriseerd, selecteert u de **organisatie**, de **opslag plaats** en de **vertakking** voor het configureren van CI/cd voor.

1. Wanneer GitHub acties de gekozen build-provider is, kunt u het gewenste werk stroom bestand selecteren met de vervolg keuzelijst **runtime stack** en **versie** . Azure voert dit werk stroom bestand door in de geselecteerde GitHub-opslag plaats voor het afhandelen van het maken en implementeren van taken. Klik op **voor beeld van bestand** om het bestand te bekijken voordat u de wijzigingen opslaat.

    > [!NOTE]
    > App Service detecteert de [taal stack-instelling](configure-common.md#configure-language-stack-settings) van uw app en selecteert de meest geschikte werk stroom sjabloon. Als u een andere sjabloon kiest, kan deze een app implementeren die niet goed wordt uitgevoerd. Zie [How the github actions build provider Works](#how-the-github-actions-build-provider-works)(Engelstalig) voor meer informatie.

1. Klik op **Opslaan**.
   
    Nieuwe door voeringen in de geselecteerde opslag plaats en vertakking worden nu doorlopend geïmplementeerd in uw App Service-app. U kunt de door voeringen en implementaties volgen op het tabblad **Logboeken** .

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

De BitBucket-integratie maakt gebruik van de App Service Build Services (kudu) voor het bouwen van Automation.

4. Als u voor de eerste keer implementeert vanaf BitBucket, klikt u op **autoriseren** en volgt u de autorisatie prompts. Als u wilt implementeren vanuit de opslag plaats van een andere gebruiker, klikt u op **account wijzigen**.

1. Selecteer voor bitbucket het bitbucket- **team**, de **opslag plaats** en de **vertakking** die u continu wilt implementeren.

1. Klik op **Opslaan**.
   
    Nieuwe door voeringen in de geselecteerde opslag plaats en vertakking worden nu doorlopend geïmplementeerd in uw App Service-app. U kunt de door voeringen en implementaties volgen op het tabblad **Logboeken** .
   
# <a name="local-git"></a>[Lokale Git](#tab/local)

Zie [lokale Git-implementatie naar Azure app service](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure-opslagplaatsen](#tab/repos)

> [!NOTE]
> Azure opslag plaatsen als implementatie bron is ondersteuning voor Windows-apps.
>

4. App Service build-service (kudu) is de standaard provider voor build.

    > [!NOTE]
    > Als u Azure-pijp lijnen wilt gebruiken als de build-provider voor uw App Service-app, moet u deze niet configureren in App Service. Configureer in plaats daarvan de CI/CD rechtstreeks vanuit Azure-pijp lijnen. Met de optie voor **Azure-pijp lijnen** wordt u alleen naar de juiste richting verwezen.

1. Selecteer de **Azure DevOps-organisatie**, het **project**, de **opslag plaats** en de **vertakking** die u continu wilt implementeren. 

    Als uw DevOps-organisatie niet wordt weer gegeven, is deze nog niet gekoppeld aan uw Azure-abonnement. Zie [een Azure-service verbinding maken](/azure/devops/pipelines/library/connect-to-azure)voor meer informatie.

-----

## <a name="disable-continuous-deployment"></a>Continue implementatie uitschakelen

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar de beheer pagina voor uw app service-app.

1. Klik in het linkermenu op instellingen van het **implementatie centrum** om de  >    >  **verbinding te verbreken**. 

    ![Laat zien hoe u de synchronisatie van uw Cloud mappen verbreekt met uw App Service-app in de Azure Portal.](media/app-service-continuous-deployment/disable.png)

1. Het werk stroom bestand met GitHub-acties blijft standaard behouden in uw opslag plaats, maar de implementatie blijft geactiveerd voor uw app. Selecteer **werk stroom bestand verwijderen** om het uit de opslag plaats te verwijderen.

1. Klik op **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Hoe de GitHub acties bouwen provider werkt

De GitHub-service voor het maken van acties is een optie voor [CI/cd van github](#configure-deployment-source)en voert het volgende uit om CI/cd in te stellen:

- Hiermee wordt een GitHub-werk stroom bestand in uw GitHub-opslag plaats gedeponeerd om taken voor het maken en implementeren van App Service te verwerken.
- Hiermee voegt u het publicatie profiel voor uw app als een GitHub-geheim toe. Het werk stroom bestand gebruikt dit geheim om te verifiëren met App Service.
- Hiermee wordt informatie vastgelegd uit de [werk stroom uitvoer logboeken](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) en wordt deze weer gegeven op het tabblad **Logboeken** in het **implementatie centrum** van uw app.

U kunt de GitHub-service voor het maken van acties op de volgende manieren aanpassen:

- Pas het werk stroom bestand aan nadat dit is gegenereerd in uw GitHub-opslag plaats. Zie [werk stroom syntaxis voor github-acties](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions)voor meer informatie. Zorg er gewoon voor dat de werk stroom wordt geïmplementeerd op App Service met de actie [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) .
- Als de geselecteerde vertakking is beveiligd, kunt u nog steeds een voor beeld van het werk stroom bestand weer geven zonder de configuratie op te slaan en vervolgens hand matig toevoegen aan de opslag plaats. Met deze methode beschikt u niet over de integratie van het logboek met de Azure Portal.
- In plaats van een publicatie profiel implementeert u met behulp van een [Service-Principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) in azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Verifiëren met een Service-Principal

1. Genereer een service-principal met de opdracht [AZ AD SP create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) in de [Azure cli](/cli/azure/). Vervang in het volgende voor beeld *\<subscription-id>* , *\<group-name>* en door *\<app-name>* uw eigen waarden:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > Verleen de minimale vereiste toegang voor de service-principal voor beveiliging. Het bereik in het vorige voor beeld is beperkt tot de specifieke App Service-app en niet de hele resource groep.
    
1. Sla de volledige JSON-uitvoer op voor de volgende stap, inclusief het hoogste niveau `{}` .

1. In [github](https://github.com/)gaat u naar uw opslag plaats, selecteert u **instellingen > geheimen > een nieuw geheim toe te voegen**.

1. Plak de volledige JSON-uitvoer van de Azure CLI-opdracht in het waardeveld van het geheim. Geef het geheim een naam zoals `AZURE_CREDENTIALS` .

1. In het werk stroom bestand dat is gegenereerd door het **implementatie centrum**, wijzigt u de `azure/webapps-deploy` stap met code zoals in het volgende voor beeld (dat is gewijzigd vanuit een Node.js werk stroom bestand):

    ```yaml
    - name: Sign in to Azure 
    # Use the GitHub secret you added
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Deploy to Azure Web App
    # Remove publish-profile
    - uses: azure/webapps-deploy@v2
      with:
        app-name: '<app-name>'
        slot-name: 'production'
        package: .
    - name: Sign out of Azure
      run: |
        az logout
    ```
    
## <a name="deploy-from-other-repositories"></a>Implementeren vanuit andere opslag plaatsen

Voor Windows-apps kunt u een continue implementatie hand matig configureren vanuit een Git-of mercurial-opslag plaats in de cloud die niet rechtstreeks wordt ondersteund door de portal, zoals [GitLab](https://gitlab.com/). U doet dit door externe Git te kiezen in de vervolg keuzelijst **bron** . Zie [continue implementatie instellen met behulp van hand matige stappen](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)voor meer informatie.

## <a name="more-resources"></a>Meer bronnen

* [Implementeren van Azure-pijp lijnen naar Azure-app Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true)
* [Veelvoorkomende problemen met doorlopende implementatie onderzoeken](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell gebruiken](/powershell/azure/)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)
