---
title: Continue implementatie inschakelen
description: Meer informatie over het inschakelen van CI/CD Azure App Service vanuit GitHub, BitBucket, Azure-opslagplaatsen of andere opslagplaatsen. Selecteer de build-pijplijn die aan uw behoeften voldoet.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/12/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 633d62fc69c516b482d5749a07052337dc71f567
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789479"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Continue implementatie naar Azure App Service

[Azure App Service](overview.md) maakt continue implementatie vanuit [GitHub-](https://help.github.com/articles/create-a-repo), [BitBucket-](https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html)en [Azure-opslagplaatsen](/azure/devops/repos/git/creatingrepo) mogelijk door de meest recente updates binnen te halen.

> [!NOTE]
> De **pagina Development Center (klassiek)** in de Azure Portal, de oude implementatie-ervaring, wordt in maart 2021 afgeschaft. Deze wijziging heeft geen invloed op bestaande implementatie-instellingen in uw app en u kunt app-implementatie blijven beheren op de **pagina Implementatiecentrum.**

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-deployment-source"></a>Implementatiebron configureren

1. [Navigeer in Azure Portal](https://portal.azure.com)naar de beheerpagina voor uw App Service app.

1. Klik in het linkermenu op **Instellingen voor**  >  **implementatiecentrum.** 

1. Selecteer **in Bron** een van de CI/CD-opties.

    ![Laat zien hoe u implementatiebron kiest in Deployment Center voor Azure App Service](media/app-service-continuous-deployment/choose-source.png)

Kies het tabblad dat overeenkomt met uw selectie voor de stappen.

# <a name="github"></a>[GitHub](#tab/github)

4. [GitHub Actions](#how-the-github-actions-build-provider-works) is de standaard build-provider. Als u dit wilt wijzigen, klikt u **op Provider** wijzigen App Service  >  **Build Service** (Kudu) > **OK.**

    > [!NOTE]
    > Als u Azure Pipelines wilt gebruiken als de buildprovider voor uw App Service app, moet u deze niet configureren in App Service. In plaats daarvan configureert u CI/CD rechtstreeks vanuit Azure Pipelines. De **optie Azure Pipelines** wijst u in de juiste richting.

1. Als u voor het eerst implementeert vanuit GitHub, klikt u op **Autorisatie** en volgt u de autorisatieprompts. Als u wilt implementeren vanuit een andere gebruikersopslagplaats, klikt u op **Account wijzigen.**

1. Nadat u uw Azure-account bij GitHub hebt geautoriseerd, selecteert u de **Organisatie,** **Opslagplaats** en **Vertakking** om CI/CD voor te configureren.

1. Wanneer GitHub Actions de gekozen buildprovider is, kunt u het werkstroombestand selecteren dat u wilt gebruiken in de vervolgkeuzekeuzes **Runtimestack** **en** Versie. Azure legt dit werkstroombestand vast in de geselecteerde GitHub-opslagplaats om build- en implementatietaken te verwerken. Als u het bestand wilt zien voordat u de wijzigingen opgeslagen, klikt u op **Voorbeeldbestand**.

    > [!NOTE]
    > App Service detecteert de [taalstackinstelling](configure-common.md#configure-language-stack-settings) van uw app en selecteert de meest geschikte werkstroomsjabloon. Als u een andere sjabloon kiest, wordt er mogelijk een app geïmplementeerd die niet goed wordt uitgevoerd. Zie How [the GitHub Actions build provider works (Hoe GitHub Actions build-provider werkt) voor meer informatie.](#how-the-github-actions-build-provider-works)

1. Klik op **Opslaan**.
   
    Nieuwe commits in de geselecteerde opslagplaats en vertakking worden nu continu geïmplementeerd in uw App Service app. U kunt de door commits en implementaties volgen op het **tabblad Logboeken.**

# <a name="bitbucket"></a>[BitBucket](#tab/bitbucket)

De BitBucket-integratie maakt gebruik van de App Service Build Services (Kudu) voor buildautomatisering.

4. Als u voor het eerst implementeert vanuit BitBucket, klikt u op **Autorisatie** en volgt u de autorisatieprompts. Als u wilt implementeren vanuit een andere gebruikersopslagplaats, klikt u op **Account wijzigen.**

1. Voor Bitbucket selecteert u het Bitbucket-team, **de** opslagplaats en de **vertakking** die u continu wilt implementeren. 

1. Klik op **Opslaan**.
   
    Nieuwe commits in de geselecteerde opslagplaats en vertakking worden nu continu geïmplementeerd in uw App Service app. U kunt de door commits en implementaties volgen op het **tabblad Logboeken.**
   
# <a name="local-git"></a>[Lokale Git](#tab/local)

Zie [Local Git deployment to Azure App Service](deploy-local-git.md).

# <a name="azure-repos"></a>[Azure-opslagplaatsen](#tab/repos)

> [!NOTE]
> Azure-repos als implementatiebron biedt ondersteuning voor Windows-apps.
>

4. App Service Build Service (Kudu) is de standaard buildprovider.

    > [!NOTE]
    > Als u Azure Pipelines wilt gebruiken als de buildprovider voor uw App Service app, moet u deze niet configureren in App Service. In plaats daarvan configureert u CI/CD rechtstreeks vanuit Azure Pipelines. De **optie Azure Pipelines** wijst u in de juiste richting.

1. Selecteer de **Azure DevOps-organisatie,** **Project,** **Opslagplaats** en **Vertakking** die u continu wilt implementeren. 

    Als uw DevOps-organisatie niet wordt vermeld, is deze nog niet gekoppeld aan uw Azure-abonnement. Zie Een [Azure-serviceverbinding](/azure/devops/pipelines/library/connect-to-azure)maken voor meer informatie.

-----

## <a name="disable-continuous-deployment"></a>Continue implementatie uitschakelen

1. [Navigeer in Azure Portal](https://portal.azure.com)naar de beheerpagina voor uw App Service app.

1. Klik in het menu links op **Instellingen voor implementatiecentrum**  >  **Verbreking** van  >  **verbinding.** 

    ![Laat zien hoe u de synchronisatie van uw cloudmap met uw App Service-app in de Azure Portal.](media/app-service-continuous-deployment/disable.png)

1. Standaard blijft het GitHub Actions werkstroombestand behouden in uw opslagplaats, maar blijft de implementatie naar uw app worden geactiveerd. Als u het wilt verwijderen uit uw opslagplaats, selecteert **u Werkstroombestand verwijderen.**

1. Klik op **OK**.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="how-the-github-actions-build-provider-works"></a>Hoe de GitHub Actions buildprovider werkt

De GitHub Actions buildprovider is een optie voor [CI/CD van GitHub](#configure-deployment-source)en doet het volgende om CI/CD in te stellen:

- Zet een werkstroombestand GitHub Actions in uw GitHub-opslagplaats om build- en implementatietaken naar uw App Service.
- Voegt het publicatieprofiel voor uw app toe als een GitHub-geheim. Het werkstroombestand gebruikt dit geheim om te verifiëren met App Service.
- Legt informatie vast uit de [werkstroomrunlogboeken](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) en geeft deze weer op **het tabblad Logboeken** in het **Implementatiecentrum van uw app.**

U kunt de GitHub Actions build-provider op de volgende manieren aanpassen:

- Pas het werkstroombestand aan nadat het is gegenereerd in uw GitHub-opslagplaats. Zie Werkstroomsyntaxis [voor meer GitHub Actions.](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions) Zorg ervoor dat de werkstroom wordt geïmplementeerd in App Service met de [actie azure/webapps-deploy.](https://github.com/Azure/webapps-deploy)
- Als de geselecteerde vertakking is beveiligd, kunt u nog steeds een voorbeeld van het werkstroombestand bekijken zonder de configuratie op te slaan. Voeg het vervolgens handmatig toe aan uw opslagplaats. Deze methode geeft u geen logboekintegratie met de Azure Portal.
- Implementeer in plaats van een publicatieprofiel met behulp van [een service-principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) in Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Verifiëren met een service-principal

Deze optionele configuratie vervangt de standaardverificatie door publicatieprofielen in het gegenereerde werkstroombestand.

1. Genereer een service-principal met de [opdracht az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) in de Azure [CLI.](/cli/azure/) Vervang in het volgende voorbeeld *\<subscription-id>* , en door uw eigen *\<group-name>* *\<app-name>* waarden:

    ```azurecli-interactive
    az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                                --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                                --sdk-auth
    ```
    
    > [!IMPORTANT]
    > Voor beveiliging verleent u de minimaal vereiste toegang tot de service-principal. Het bereik in het vorige voorbeeld is beperkt tot de specifieke App Service app en niet de hele resourcegroep.
    
1. Sla de volledige JSON-uitvoer op voor de volgende stap, met inbegrip van het hoogste `{}` niveau.

1. Blader [in GitHub](https://github.com/)door uw opslagplaats, selecteer **Instellingen > Geheimen > Een nieuw geheim toevoegen.**

1. Plak de volledige JSON-uitvoer van de Azure CLI-opdracht in het waardeveld van het geheim. Geef het geheim een naam zoals `AZURE_CREDENTIALS` .

1. Wijzig in het werkstroombestand dat wordt gegenereerd door het **Implementatiecentrum** de stap met code zoals in het volgende voorbeeld (gewijzigd op basis van `azure/webapps-deploy` een Node.js werkstroombestand):

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
    
## <a name="deploy-from-other-repositories"></a>Implementeren vanuit andere opslagplaatsen

Voor Windows-apps kunt u handmatig continue implementatie configureren vanuit een Git- of Mercurial-opslagplaats in de cloud die niet rechtstreeks door de portal wordt [ondersteund, zoals GitLab.](https://gitlab.com/) U doet dit door Externe Git te kiezen in de **vervolgkeuzegids** Bron. Zie Continue implementatie instellen met [behulp van handmatige stappen voor meer informatie.](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)

## <a name="more-resources"></a>Meer bronnen

* [Implementeren vanuit Azure Pipelines naar Azure-app Services](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)
* [Veelvoorkomende problemen met continue implementatie onderzoeken](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell gebruiken](/powershell/azure/)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)
