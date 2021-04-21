---
title: CI/CD naar aangepaste containers
description: Stel continue implementatie in op een aangepaste Windows- of Linux-container in Azure App Service.
keywords: azure app service, linux, docker, acr,oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18, devx-track-azurecli
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 6519f3fe7335ed41f4d5ef67771aaa738a33e4a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782599"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Continue implementatie met aangepaste containers in Azure App Service

In deze zelfstudie configureert u continue implementatie [](https://azure.microsoft.com/services/container-registry/) voor een aangepaste containerafbeelding vanuit Azure Container Registry opslagplaatsen of [Docker Hub](https://hub.docker.com).

## <a name="1-go-to-deployment-center"></a>1. Ga naar Implementatiecentrum

[Navigeer in Azure Portal](https://portal.azure.com)naar de beheerpagina voor uw App Service app.

Klik in het linkermenu op **Instellingen voor**  >  **implementatiecentrum.** 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. Implementatiebron kiezen

**De** implementatiebron kiezen is afhankelijk van uw scenario:
- **Container Registry** stelt CI/CD in tussen uw containerregister en App Service.
- De **GitHub Actions** optie is voor u als u de broncode voor uw containerafbeelding in GitHub onderhoudt. De implementatieactie wordt geactiveerd door nieuwe commits naar uw GitHub-opslagplaats en kan rechtstreeks naar uw containerregister worden uitgevoerd en vervolgens uw App Service-app bijwerken om de nieuwe afbeelding `docker build` `docker push` uit te voeren. Zie How [CI/CD works with GitHub Actions (Hoe CI/CD werkt met GitHub Actions) voor meer GitHub Actions.](#how-cicd-works-with-github-actions)
- Zie Deploy an Azure Web App Container from Azure Pipelines (Een Azure Web App-container implementeren vanuit Azure **Pipelines)** voor het instellen van CI/CD [met Azure Pipelines.](/azure/devops/pipelines/targets/webapp-on-container-linux)

> [!NOTE]
> Selecteer voor een Docker Compose-app **Container Registry.**

Als u een GitHub Actions, klikt **u op** **Autorisatie** en volgt u de autorisatieprompts. Als u al eerder met GitHub hebt geautoriseerd, kunt u implementeren vanuit de opslagplaats van een andere gebruiker door te klikken op **Account wijzigen.**

Zodra u uw Azure-account bij GitHub hebt geautoriseerd, **selecteert u** **de organisatie** **,** opslagplaats en **vertakking waar** u de implementatie van wilt uitvoeren.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. Registerinstellingen configureren
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. Registerinstellingen configureren

Als u een app met meerdere containers (Docker Compose) wilt **implementeren,** selecteert **u Docker Compose** in **Containertype.**

Als u de vervolgkeuzebalk **Containertype** niet ziet, schuift u terug naar **Bron** en **selecteert** **u Container Registry**.
::: zone-end

Selecteer **in Registerbron** **waar** uw containerregister zich is. Selecteer Privéregister als dit geen Azure Container Registry of  Docker Hub **is.**

::: zone pivot="container-linux"
> [!NOTE]
> Als uw app met meerdere containers (Docker Compose) meer dan één privé-afbeelding gebruikt, moet u ervoor zorgen dat de persoonlijke afbeeldingen zich in hetzelfde privéregister en toegankelijk zijn met dezelfde gebruikersreferenties. Als uw app met meerdere containers alleen openbare afbeeldingen **gebruikt,** selecteert **u Docker Hub**, zelfs als sommige afbeeldingen zich niet in de Docker Hub.
::: zone-end  

Volg de volgende stappen door het tabblad te selecteren dat overeenkomt met uw keuze.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

In **de vervolgkeuzelijst** Register worden de registers in hetzelfde abonnement als uw app weergegeven. **Selecteer** het register dat u wilt.

> [!NOTE]
> Als u wilt implementeren vanuit een register in een ander abonnement, **selecteert u** in **plaats daarvan Privéregister** in **Registerbron.**

::: zone pivot="container-windows"
**Selecteer** de te **implementeren afbeelding** **en** tag. Als u wilt, **typt u** de opstartopdracht in **Opstartbestand**. 
::: zone-end
::: zone pivot="container-linux"
Volg de volgende stap, afhankelijk van het **containertype**:
- Selecteer **voor Docker Compose** **het** register voor uw persoonlijke afbeeldingen. **Klik** **op Bestand kiezen** om uw [Docker Compose-bestand](https://docs.docker.com/compose/compose-file/)te uploaden of plak de inhoud van uw Docker Compose-bestand in **Config**. 
- Selecteer **voor Enkele container** de **te** implementeren **afbeelding** **en** tag. Als u wilt, **typt u** de opstartopdracht in **Opstartbestand**. 
::: zone-end

App Service wordt de tekenreeks **in** Het opstartbestand toegevoegd aan het einde van de opdracht [ `docker run` (als `[COMMAND] [ARG...]` het segment)](https://docs.docker.com/engine/reference/run/) bij het starten van de container.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
Selecteer **in Toegang tot** opslagplaats **of** de te implementeren afbeelding openbaar of privé is.
::: zone-end
::: zone pivot="container-linux"
Selecteer **in Toegang tot** opslagplaats **of** de te implementeren afbeelding openbaar of privé is. Voor een Docker Compose-app met een of meer persoonlijke afbeeldingen **selecteert u** **Privé.**
::: zone-end

Als u een persoonlijke afbeelding selecteert, **geeft u** **de aanmelding** (gebruikersnaam) en het **wachtwoord van** het Docker-account op.

::: zone pivot="container-windows"
**Gebruik** de naam van de afbeelding en de tag in Volledige afbeeldingsnaam **en Tag**, gescheiden door een `:` (bijvoorbeeld `nginx:latest` ). Als u wilt, **typt u** de opstartopdracht in **Opstartbestand**. 
::: zone-end
::: zone pivot="container-linux"
Volg de volgende stap, afhankelijk van het **containertype**:
- Selecteer **voor Docker Compose** **het** register voor uw persoonlijke afbeeldingen. **Klik** **op Bestand kiezen** om uw [Docker Compose-bestand](https://docs.docker.com/compose/compose-file/)te uploaden of plak de inhoud van uw Docker Compose-bestand in **Config**. 
- Voor **Enkele container** geeft u **de** naam van de afbeelding en de tag op in Volledige naam van de afbeelding en **Tag**, gescheiden door een `:` (bijvoorbeeld `nginx:latest` ). Als u wilt, **typt u** de opstartopdracht in **Opstartbestand**. 
::: zone-end

App Service wordt de tekenreeks **in** Het opstartbestand toegevoegd aan het einde van de opdracht [ `docker run` (als `[COMMAND] [ARG...]` het segment)](https://docs.docker.com/engine/reference/run/) bij het starten van de container.

# <a name="private-registry"></a>[Privéregister](#tab/private)

Typ **in Server-URL** **de** URL van de server, te beginnen **met https://**.

Typ **in Aanmelden** en **Wachtwoord** **uw** aanmeldingsreferenties voor uw privéregister.

::: zone pivot="container-windows"
**Gebruik** de naam van de afbeelding en de tag in **Volledige** afbeeldingsnaam en Tag , gescheiden door een `:` (bijvoorbeeld `nginx:latest` ). Als u wilt, **typt u** de opstartopdracht in **Opstartbestand**. 
::: zone-end
::: zone pivot="container-linux"
Volg de volgende stap, afhankelijk van het **containertype**:
- Selecteer **voor Docker Compose** **het** register voor uw persoonlijke afbeeldingen. **Klik** **op Bestand kiezen** om uw [Docker Compose-bestand](https://docs.docker.com/compose/compose-file/)te uploaden of plak de inhoud van uw Docker Compose-bestand in **Config**. 
- Voor **Enkele container** geeft u **de** naam van de afbeelding en de tag op in Volledige naam van de afbeelding en **Tag**, gescheiden door een `:` (bijvoorbeeld `nginx:latest` ). Als u wilt, **typt u** de opstartopdracht in **Opstartbestand**. 
::: zone-end

App Service wordt de tekenreeks **in** opstartbestand toegevoegd aan het einde van de opdracht [ `docker run` (als `[COMMAND] [ARG...]` het segment)](https://docs.docker.com/engine/reference/run/) bij het starten van de container.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. CI/CD inschakelen
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. CI/CD inschakelen
::: zone-end

App Service ondersteunt CI/CD-integratie met Azure Container Registry en Docker Hub. Als u dit wilt inschakelen, **selecteert** **u Aan** in **Continue implementatie.**

::: zone pivot="container-linux"
> [!NOTE]
> Als u **GitHub Actions** bron **selecteert,** krijgt u deze optie niet omdat CI/CD rechtstreeks door de GitHub Actions verwerkt. In plaats daarvan ziet u een sectie **Werkstroomconfiguratie,** waarin u kunt **klikken** op **Voorbeeldbestand om** het werkstroombestand te inspecteren. Azure legt dit bestand vast in de geselecteerde GitHub-bronopslagplaats om build- en implementatietaken te verwerken. Zie How [CI/CD works with GitHub Actions (Hoe CI/CD werkt met GitHub Actions) voor meer GitHub Actions.](#how-cicd-works-with-github-actions)
::: zone-end

Wanneer u deze optie inschakelen, App Service een webhook toegevoegd aan uw opslagplaats in Azure Container Registry of Docker Hub. Uw opslagplaats plaatst deze webhook wanneer de geselecteerde afbeelding wordt bijgewerkt met `docker push` . De webhook zorgt ervoor dat uw App Service-app opnieuw wordt gestart en uitgevoerd `docker pull` om de bijgewerkte afbeelding op te halen.

**Voor andere privéregisters kunt** u handmatig of als stap in een CI/CD-pijplijn naar de webhook posten. Klik **in Webhook-URL** **op de** knop **Kopiëren** om de webhook-URL op te halen.

::: zone pivot="container-linux"
> [!NOTE]
> Ondersteuning voor apps met meerdere containers (Docker Compose) is beperkt: 
> - Voor Azure Container Registry maakt App Service een webhook in het geselecteerde register met het register als bereik. Een naar een opslagplaats in het register (inclusief de opslagplaatsen waarnaar niet wordt verwezen door uw `docker push` Docker Compose-bestand) activeert het opnieuw starten van de app. Mogelijk wilt u [de webhook wijzigen](../container-registry/container-registry-webhook.md) in een smaller bereik.
> - Docker Hub biedt geen ondersteuning voor webhooks op registerniveau. U moet **de** webhooks handmatig toevoegen aan de afbeeldingen die zijn opgegeven in uw Docker Compose-bestand.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. Uw instellingen opslaan
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. Uw instellingen opslaan
::: zone-end

**Klik op** **Opslaan.**

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>Hoe CI/CD werkt met GitHub Actions

Als u kiest **GitHub Actions** in **Bron** (zie [Implementatiebron](#2-choose-deployment-source)kiezen), App Service u CI/CD op de volgende manieren in:

- Zet een werkstroombestand GitHub Actions uw GitHub-opslagplaats op om build- en implementatietaken naar uw App Service.
- Voegt de referenties voor uw privéregister toe als GitHub-geheimen. Het gegenereerde werkstroombestand voert de [actie Azure/docker-login](https://github.com/Azure/docker-login) uit om u aan te melden met uw privéregister en voert vervolgens uit om dit `docker push` te implementeren.
- Hiermee wordt het publicatieprofiel voor uw app toegevoegd als een GitHub-geheim. Het gegenereerde werkstroombestand gebruikt dit geheim om te verifiëren met App Service en voert vervolgens de actie [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) uit om de bijgewerkte afbeelding te configureren, waardoor een app opnieuw wordt opgestart om de bijgewerkte afbeelding op te halen.
- Legt informatie vast uit de [werkstroomrunlogboeken](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) en geeft deze weer op **het tabblad Logboeken** in het **Implementatiecentrum van uw app.**

U kunt de GitHub Actions build-provider op de volgende manieren aanpassen:

- Pas het werkstroombestand aan nadat het is gegenereerd in uw GitHub-opslagplaats. Zie Werkstroomsyntaxis [voor meer GitHub Actions.](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions) Zorg ervoor dat de werkstroom eindigt met de [actie Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) om het opnieuw starten van de app te activeren.
- Als de geselecteerde vertakking is beveiligd, kunt u nog steeds een voorbeeld van het werkstroombestand bekijken zonder de configuratie op te slaan. Voeg het bestand en de vereiste GitHub-geheimen handmatig toe aan uw opslagplaats. Deze methode geeft u geen logboekintegratie met de Azure Portal.
- Implementeer in plaats van een publicatieprofiel met behulp van [een service-principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) in Azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Verifiëren met een service-principal

Deze optionele configuratie vervangt de standaardverificatie door publicatieprofielen in het gegenereerde werkstroombestand.

**Genereer** een service-principal met de [opdracht az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) in de Azure [CLI.](/cli/azure/) Vervang in het volgende voorbeeld *\<subscription-id>* , en door uw eigen *\<group-name>* *\<app-name>* waarden. **Sla** de volledige JSON-uitvoer op voor de volgende stap, met inbegrip van het hoogste `{}` niveau.

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> Voor beveiliging verleent u de minimaal vereiste toegang tot de service-principal. Het bereik in het vorige voorbeeld is beperkt tot de specifieke App Service app en niet de hele resourcegroep.

Blader [in GitHub](https://github.com/) **naar** uw opslagplaats en **selecteer** vervolgens Instellingen > Geheimen > Een nieuw **geheim toevoegen.** **Plak** de volledige JSON-uitvoer van de Azure CLI-opdracht in het waardeveld van het geheim. **Geef** het geheim een naam zoals `AZURE_CREDENTIALS` .

Wijzig in het werkstroombestand dat is gegenereerd **door het Implementatiecentrum** **de** stap met code zoals in het `azure/webapps-deploy` volgende voorbeeld:

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
    images: '<registry-server>/${{ secrets.AzureAppService_ContainerUsername_... }}/<image>:${{ github.sha }}'
    - name: Sign out of Azure
    run: |
    az logout
```

::: zone-end

## <a name="automate-with-cli"></a>Automatiseren met CLI

Voer  [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set)uit om het containerregister en de Docker-afbeelding te configureren.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url 'https://<registry-name>.azurecr.io' --docker-registry-server-user '<username>' --docker-registry-server-password '<password>'
```

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

```azurecli-interactive
# Public image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name>

# Private image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

# <a name="private-registry"></a>[Privéregister](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
Als u een app met meerdere containers  (Docker Compose) wilt configureren, bereidt u een Docker Compose-bestand lokaal voor en voer **vervolgens** [az webapp config container set](/cli/azure/webapp/config/container#az_webapp_config_container_set) uit met de parameter `--multicontainer-config-file` . Als uw Docker Compose-bestand persoonlijke afbeeldingen bevat, voegt **u** `--docker-registry-server-*` parameters toe zoals weergegeven in het vorige voorbeeld.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Voer  [az webapp deployment container config](/cli/azure/webapp/deployment/container#az_webapp_deployment-container-config) uit met de parameter om CI/CD vanuit het containerregister te configureren voor uw `--enable-cd` app. Met de opdracht wordt de webhook-URL uitgevoerd, maar u moet de webhook in het register handmatig maken in een afzonderlijke stap. In het volgende voorbeeld wordt CI/CD in uw app in staat gemaakt en wordt vervolgens de webhook-URL in de uitvoer gebruikt om de webhook te maken in Azure Container Registry.

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>Meer bronnen

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Een .NET Core-web-app maken in App Service in Linux](quickstart-dotnetcore.md)
* [Quickstart: Een aangepaste container uitvoeren op App Service](quickstart-custom-container.md)
* [Veelgestelde vragen over App Service op Linux](faq-app-service-linux.md)
* [Aangepaste containers configureren](configure-custom-container.md)
* [Werkstromen voor acties die in Azure moeten worden geïmplementeerd](https://github.com/Azure/actions-workflow-samples)
