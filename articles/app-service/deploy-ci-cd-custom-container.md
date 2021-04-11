---
title: CI/CD naar aangepaste containers
description: Stel doorlopende implementatie in op een aangepaste Windows-of Linux-container in Azure App Service.
keywords: Azure app service, Linux, docker, ACR, oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 654b0f842a3165926242d1ef03f2dfe4e5bacfdc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643347"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Doorlopende implementatie met aangepaste containers in Azure App Service

In deze zelf studie configureert u continue implementatie voor een aangepaste container installatie kopie van beheerde [Azure container Registry](https://azure.microsoft.com/services/container-registry/) -opslag plaatsen of [docker-hub](https://hub.docker.com).

## <a name="1-go-to-deployment-center"></a>1. Ga naar implementatie centrum

Navigeer in het [Azure Portal](https://portal.azure.com)naar de beheer pagina voor uw app service-app.

Klik in het menu links op instellingen voor het **implementatie centrum**  >  . 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2. Kies een implementatie bron

**Kies** de implementatie bron, afhankelijk van uw scenario:
- Met **container Registry** wordt CI/cd ingesteld tussen uw container register en app service.
- De **github-actie** optie is voor u als u de bron code voor de container installatie kopie in github bijhoudt. Door nieuwe door voer te laten door voeren in uw GitHub-opslag plaats, kan de actie implementeren worden uitgevoerd `docker build` en `docker push` rechtstreeks naar uw container register worden gedistribueerd. vervolgens werkt u de app service-app bij om de nieuwe installatie kopie uit te voeren. Zie [hoe CI/CD werkt met github-acties](#how-cicd-works-with-github-actions)voor meer informatie.
- Zie [een Azure web app-container implementeren vanuit Azure pipelines](/azure/devops/pipelines/targets/webapp-on-container-linux)om CI/cd met **Azure-pijp lijnen** in te stellen.

> [!NOTE]
> Selecteer **container Registry** voor een docker-app voor opstellen.

Als u GitHub-acties kiest, **klikt u op** **autoriseren** en volgt u de autorisatie prompts. Als u al eerder met GitHub hebt gemachtigd, kunt u deze implementeren vanuit de opslag plaats van een andere gebruiker door te klikken op **account wijzigen**.

Wanneer u uw Azure-account met GitHub hebt geautoriseerd, **selecteert** u de **organisatie**, **opslag plaats** en **vertakking** van waaruit u wilt implementeren.
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2. register instellingen configureren
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3. register instellingen configureren

Als u een app met meerdere containers (docker opstellen) wilt implementeren, **selecteert** u **docker opstellen** in **container type**.

Als u de vervolg keuzelijst **container type** niet ziet, bladert u terug naar **bron** en **selecteert** u **container Registry**.
::: zone-end

**Selecteer** in **register bron** de locatie waar uw container register zich bevindt. Als het geen Azure Container Registry noch docker hub, **selecteert** u **persoonlijk REGI ster**.

::: zone pivot="container-linux"
> [!NOTE]
> Als uw multi-container-app (docker opstellen) meer dan één persoonlijke installatie kopie gebruikt, moet u ervoor zorgen dat de persoonlijke installatie kopieën zich in hetzelfde persoonlijke REGI ster bevinden en toegankelijk zijn met dezelfde gebruikers referenties. Als uw app met meerdere containers alleen open bare installatie kopieën gebruikt, **selecteert u** **docker hub**, zelfs als sommige installatie kopieën zich niet in docker hub bevinden.
::: zone-end  

Volg de volgende stappen door het tabblad te selecteren dat overeenkomt met uw keuze.

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

In de vervolg keuzelijst **REGI ster** worden de registers in hetzelfde abonnement als uw app weer gegeven. **Selecteer** het gewenste REGI ster.

> [!NOTE]
> Als u vanuit een REGI ster in een ander abonnement wilt implementeren, **selecteert** u in plaats daarvan **persoonlijk REGI ster** in **register bron** .

::: zone pivot="container-windows"
**Selecteer** de **afbeelding** en de **tag** die u wilt implementeren. Als u wilt, **typt** u de opstart opdracht in het **opstart bestand**. 
::: zone-end
::: zone pivot="container-linux"
Volg de volgende stap, afhankelijk van het **type container**:
- Voor **docker opstellen** **selecteert u** het REGI ster voor uw persoonlijke installatie kopieën. **Klik op** **bestand kiezen** om het [docker-bestand](https://docs.docker.com/compose/compose-file/)te uploaden of **plak** de inhoud van uw docker-bestand in **configuratie**.
- **Selecteer** voor **één container** de **afbeelding** en de **tag** die u wilt implementeren. Als u wilt, **typt** u de opstart opdracht in het **opstart bestand**. 
::: zone-end

App Service voegt de teken reeks in het **opstart bestand** toe aan [het einde van de `docker run` opdracht (als het `[COMMAND] [ARG...]` segment)](https://docs.docker.com/engine/reference/run/) bij het starten van de container.

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
**Selecteer** in **opslag plaats toegang** of de te implementeren installatie kopie openbaar of privé is.
::: zone-end
::: zone pivot="container-linux"
**Selecteer** in **opslag plaats toegang** of de te implementeren installatie kopie openbaar of privé is. **Selecteer** **privé** voor een docker-app met een of meer persoonlijke installatie kopieën.
::: zone-end

Als u een persoonlijke installatie kopie selecteert, **geeft** u de **aanmelding** (gebruikers naam) en het **wacht woord** van het docker-account op.

::: zone pivot="container-windows"
**Geef** de naam van de installatie kopie en de tag op in **volledige naam en label van de installatie kopie**, gescheiden door een `:` (bijvoorbeeld `nginx:latest` ). Als u wilt, **typt** u de opstart opdracht in het **opstart bestand**. 
::: zone-end
::: zone pivot="container-linux"
Volg de volgende stap, afhankelijk van het **type container**:
- Voor **docker opstellen** **selecteert u** het REGI ster voor uw persoonlijke installatie kopieën. **Klik op** **bestand kiezen** om het [docker-bestand](https://docs.docker.com/compose/compose-file/)te uploaden of **plak** de inhoud van uw docker-bestand in **configuratie**.
- **Geef** voor **één container** de naam van de installatie kopie en de tag op in **volledige naam en label van de installatie kopie**, gescheiden door een `:` (bijvoorbeeld `nginx:latest` ). Als u wilt, **typt** u de opstart opdracht in het **opstart bestand**. 
::: zone-end

App Service voegt de teken reeks in het **opstart bestand** toe aan [het einde van de `docker run` opdracht (als het `[COMMAND] [ARG...]` segment)](https://docs.docker.com/engine/reference/run/) bij het starten van de container.

# <a name="private-registry"></a>[Persoonlijk REGI ster](#tab/private)

In **server-URL**, **typt** u de URL van de server, beginnend met **https://**.

Bij **Aanmelden** en **wacht woord** **Typ** uw aanmeldings referenties voor uw persoonlijke REGI ster.

::: zone pivot="container-windows"
**Geef** de naam van de installatie kopie en de tag op in **volledige naam en label van de installatie kopie**, gescheiden door een `:` (bijvoorbeeld `nginx:latest` ). Als u wilt, **typt** u de opstart opdracht in het **opstart bestand**. 
::: zone-end
::: zone pivot="container-linux"
Volg de volgende stap, afhankelijk van het **type container**:
- Voor **docker opstellen** **selecteert u** het REGI ster voor uw persoonlijke installatie kopieën. **Klik op** **bestand kiezen** om het [docker-bestand](https://docs.docker.com/compose/compose-file/)te uploaden of **plak** de inhoud van uw docker-bestand in **configuratie**.
- **Geef** voor **één container** de naam van de installatie kopie en de tag op in **volledige naam en label van de installatie kopie**, gescheiden door een `:` (bijvoorbeeld `nginx:latest` ). Als u wilt, **typt** u de opstart opdracht in het **opstart bestand**. 
::: zone-end

App Service voegt de teken reeks in het **opstart bestand** toe aan [het einde van de `docker run` opdracht (als het `[COMMAND] [ARG...]` segment)](https://docs.docker.com/engine/reference/run/) bij het starten van de container.

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3. Schakel CI/CD in
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4. Schakel CI/CD in
::: zone-end

App Service ondersteunt de integratie van CI/CD met Azure Container Registry en docker hub. Als u deze functie wilt inschakelen, **selecteert** **u in** **continue implementatie**.

::: zone pivot="container-linux"
> [!NOTE]
> Als u **github-acties** selecteert in de **bron**, wordt deze optie niet weer geven omdat CI/cd rechtstreeks door github-acties wordt verwerkt. In plaats daarvan ziet u een sectie **werk stroom configuratie** , waarin u kunt **klikken op** **Preview-bestand** om het werk stroom bestand te controleren. Dit bestand wordt door Azure door doorgevoerd in de geselecteerde GitHub-bron opslagplaats voor het afhandelen van het maken en implementeren van taken. Zie [hoe CI/CD werkt met github-acties](#how-cicd-works-with-github-actions)voor meer informatie.
::: zone-end

Wanneer u deze optie inschakelt, voegt App Service een webhook toe aan uw opslag plaats in Azure Container Registry of docker hub. Uw opslag plaats wordt naar deze webhook gepost wanneer de geselecteerde installatie kopie wordt bijgewerkt met `docker push` . De webhook zorgt ervoor dat uw App Service-app opnieuw wordt gestart en wordt uitgevoerd `docker pull` om de bijgewerkte installatie kopie op te halen.

**Voor andere persoonlijke registers** kan uw bericht hand matig worden geplaatst op de webhook of als een stap in een CI/cd-pijp lijn. Klik in de **webhook-URL** **op** de knop **kopiëren** om de webhook-URL op te halen.

::: zone pivot="container-linux"
> [!NOTE]
> Ondersteuning voor multi-container-apps (docker opstellen) is beperkt: 
> - Voor Azure Container Registry maakt App Service een webhook in het geselecteerde REGI ster met het REGI ster als bereik. Een `docker push` naar een opslag plaats in het REGI ster (met inbegrip van de bestanden waarnaar niet wordt verwezen door het docker-bestand) wordt het opnieuw starten van een app geactiveerd. Mogelijk wilt u [de webhook aanpassen](../container-registry/container-registry-webhook.md) naar een smaller bereik.
> - Docker hub biedt geen ondersteuning voor webhooks op het niveau van het REGI ster. U moet de webhooks hand matig **toevoegen** aan de installatie kopieën die zijn opgegeven in het docker-bestand.
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4. Sla de instellingen op
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5. Sla de instellingen op
::: zone-end

**Klik op** **Opslaan**.

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>Hoe CI/CD werkt met GitHub-acties

Als u **github-acties** kiest in de **bron** (Zie [implementatie bron kiezen](#2-choose-deployment-source)), app service op de volgende manieren CI/CD instellen:

- Hiermee wordt een GitHub-werk stroom bestand in uw GitHub-opslag plaats gedeponeerd om taken voor het maken en implementeren van App Service te verwerken.
- Voegt de referenties voor uw persoonlijke REGI ster toe als GitHub geheimen. Het gegenereerde werk stroom bestand voert de actie [Azure/docker-aanmelden](https://github.com/Azure/docker-login) uit om u aan te melden met uw persoonlijke REGI ster. vervolgens wordt uitgevoerd `docker push` om te implementeren.
- Hiermee voegt u het publicatie profiel voor uw app als een GitHub-geheim toe. Het gegenereerde werk stroom bestand gebruikt dit geheim om te verifiëren met App Service en voert vervolgens de actie [Azure/webapps-Deploy](https://github.com/Azure/webapps-deploy) uit om de bijgewerkte installatie kopie te configureren, waardoor het opnieuw opstarten van de app wordt geactiveerd om de bijgewerkte installatie kopie op te halen.
- Hiermee wordt informatie vastgelegd uit de [werk stroom uitvoer logboeken](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs) en wordt deze weer gegeven op het tabblad **Logboeken** in het **implementatie centrum** van uw app.

U kunt de GitHub-service voor het maken van acties op de volgende manieren aanpassen:

- Pas het werk stroom bestand aan nadat dit is gegenereerd in uw GitHub-opslag plaats. Zie [werk stroom syntaxis voor github-acties](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions)voor meer informatie. Zorg ervoor dat de werk stroom eindigt met de actie [Azure/webapps-implementeren](https://github.com/Azure/webapps-deploy) om een app opnieuw te starten.
- Als de geselecteerde vertakking is beveiligd, kunt u nog steeds een voor beeld van het werk stroom bestand bekijken zonder de configuratie op te slaan. vervolgens voegt u het en de vereiste GitHub-geheimen hand matig toe aan uw opslag plaats. Met deze methode beschikt u niet over de integratie van het logboek met de Azure Portal.
- In plaats van een publicatie profiel implementeert u met behulp van een [Service-Principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) in azure Active Directory.

#### <a name="authenticate-with-a-service-principal"></a>Verifiëren met een Service-Principal

Deze optionele configuratie vervangt de standaard verificatie met publicatie profielen in het gegenereerde werk stroom bestand.

**Genereer** een service-principal met de opdracht [AZ AD SP create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) in de [Azure cli](/cli/azure/). Vervang in het volgende voor beeld *\<subscription-id>* , *\<group-name>* en door *\<app-name>* uw eigen waarden. **Sla** de volledige JSON-uitvoer op voor de volgende stap, inclusief het hoogste niveau `{}` .

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> Verleen de minimale vereiste toegang voor de service-principal voor beveiliging. Het bereik in het vorige voor beeld is beperkt tot de specifieke App Service-app en niet de hele resource groep.

**Blader** in [github](https://github.com/)naar uw opslag plaats en **Selecteer** **instellingen > geheimen > een nieuw geheim toe te voegen**. **Plak** de volledige JSON-uitvoer van de Azure cli-opdracht in het veld waarde Value van het geheim. **Geef** het geheim een naam zoals `AZURE_CREDENTIALS` .

In het werk stroom bestand dat is gegenereerd door het **implementatie centrum**, **wijzigt** u de `azure/webapps-deploy` stap met code zoals in het volgende voor beeld:

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

**Voer** [AZ webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set)uit om het container register en de docker-installatie kopie te configureren.

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

# <a name="private-registry"></a>[Persoonlijk REGI ster](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
Als u een app met meerdere containers (docker opstellen) wilt configureren **, maakt** u een docker-bestand lokaal en **voert** u de opdracht [AZ webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set) uit met de `--multicontainer-config-file` para meter. Als uw docker-bestand persoonlijke installatie kopieën bevat, **voegt** u `--docker-registry-server-*` para meters toe, zoals wordt weer gegeven in het vorige voor beeld.

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

Als u CI/CD vanuit het container register wilt configureren voor uw app, **voert** u de opdracht [AZ webapp Deployment container config](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) uit met de `--enable-cd` para meter. Met de opdracht wordt de webhook-URL uitgevoerd, maar u moet de webhook in uw REGI ster hand matig in een afzonderlijke stap maken. In het volgende voor beeld wordt CI/CD ingeschakeld voor uw app. vervolgens gebruikt de webhook-URL in de uitvoer om de webhook in Azure Container Registry te maken.

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
* [Werk stromen voor acties die moeten worden geïmplementeerd in azure](https://github.com/Azure/actions-workflow-samples)
