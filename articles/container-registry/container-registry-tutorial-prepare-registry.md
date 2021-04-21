---
title: Zelfstudie - Geo-gerepliceerd register maken
description: Een Azure-containerregister maken, geo-replicatie configureren, een Docker-installatiekopie voorbereiden en implementeren in het register. Deel één van een serie van drie.
ms.topic: tutorial
ms.date: 06/30/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: c5f5627462f016c857d956be46c276dda78e9b2b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780655"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Zelfstudie: Een Azure-containerregister met geo-replicatie voorbereiden

Een Azure-containerregister is een privé Docker-register dat is geïmplementeerd in Azure en uw netwerk dicht bij uw implementaties houdt. In deze set van drie zelfstudieartikelen leert u over het gebruik van geo-replicatie om een ASP.NET Core-webtoepassing die wordt uitgevoerd in een Linux-container te implementeren in twee [Web App for Containers](../app-service/index.yml)-instanties. U ziet hoe Azure de installatiekopie automatisch vanuit de dichtstbijzijnde opslagplaats met geo-replicatie op elke Web App-instantie implementeert.

In deze zelfstudie, die het eerste deel is van een serie van drie, leert u het volgende:

> [!div class="checklist"]
> * Een Azure-containerregister met geo-replicatie maken
> * Toepassingsbroncode klonen vanuit GitHub
> * Een Docker-containerinstallatiekopie maken uit de toepassingsbron
> * De containerinstallatiekopie naar het register pushen

In volgende zelfstudies implementeert u de container vanuit uw privéregister naar een web-app die wordt uitgevoerd in de twee Azure-regio's. Vervolgens werkt u de code in de toepassing bij en werkt u beide Web App-instanties met een één `docker push` in het register bij.

## <a name="before-you-begin"></a>Voordat u begint

Voor deze zelfstudie is een lokale installatie van de Azure CLI (versie 2.0.31 of hoger) vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

U moet bekend zijn met Docker-kernconcepten zoals containers, containerinstallatiekopieën en Docker CLI-basisopdrachten. Zie [Aan de slag met Docker]( https://docs.docker.com/get-started/) voor een uitleg van de basisprincipes van containers.

Voor het voltooien van deze zelfstudie, hebt u een lokale Docker-installatie nodig. Docker biedt installatie-instructies voor de systemen [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell bevat niet de vereiste Docker-onderdelen die nodig zijn om elke stap in deze zelfstudie te voltooien. Daarom raden we een lokale installatie van de Azure CLI en Docker-ontwikkelomgeving aan.

## <a name="create-a-container-registry"></a>Een containerregister maken

Voor deze zelfstudie hebt u een Azure-containerregister in de Premium-servicelaag nodig. Om een nieuw Azure-containerregister te maken, volgt u de stappen in deze sectie.

> [!TIP]
> Zie [Wijzigingslagen](container-registry-skus.md#changing-tiers) als u eerder een register hebt gemaakt en een upgrade wilt uitvoeren. 

Meld u aan bij de [Azure-portal](https://portal.azure.com).

Selecteer **Een resource maken** > **Containers** > **Azure Container Registry**.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-01.png" alt-text="Een containerregister maken met Azure Portal":::

Configureer uw nieuwe register met de volgende instellingen. Op het tabblad **Basis**:

* **Registernaam**: Een registernaam maken die globaal uniek is binnen Azure en 5-50 alfanumerieke tekens bevat
* **Resourcegroep**: **Nieuwe maken** > `myResourceGroup`
* **Locatie**: `West US`
* **SKU**: `Premium` (vereist voor de geo-replicatie)

Selecteer **Beoordelen en maken** en **Maken** om het exemplaar van het register te maken.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-02.png" alt-text="Een containerregister configureren in de Azure Portal":::

In de rest van deze zelfstudie gebruiken we `<acrName>` als tijdelijke aanduiding voor de gekozen **containerregisternaam**.

> [!TIP]
> Omdat Azure-containerregisters doorgaans lang meegaande resources zijn die op meerdere hosts van de container worden gebruikt, wordt u aangeraden het register in een eigen resourcegroep te maken. Als u registers met geo-replicatie en webhooks configureert, worden deze extra resources in dezelfde resourcegroep geplaatst.

## <a name="configure-geo-replication"></a>Geo-replicatie configureren

Nu u een Premium-register hebt, kunt u geo-replicatie configureren. Uw web-app, die u in de volgende zelfstudie configureert voor uitvoering in twee regio's, kan vervolgens containerinstallatiekopieën uit het dichtstbijzijnde register pullen.

Navigeer naar uw nieuwe containerregister in Azure Portal en selecteer **Replicaties** onder **Services**:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-03.png" alt-text="Replicaties in de containerregister-UI van Azure Portal":::

Er wordt een kaart weergegeven met groene zeshoeken die Azure-regio's vertegenwoordigen die beschikbaar zijn voor geo-replicatie:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-map-01.png" alt-text="Kaart met regio's in Azure Portal":::

Repliceer uw register naar de regio VS - oost door de groene zeshoek te selecteren. Selecteer vervolgens **Maken** onder **Replicatie maken**:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-04.png" alt-text="Gebruikersinterface voor het maken van een replicatie in Azure Portal":::

Wanneer de replicatie is voltooid, geeft de portal *Gereed* weer voor beide regio's. Gebruik de knop **Vernieuwen** om de status van de replicatie te vernieuwen. Het kan een paar minuten duren voordat de replica's zijn gemaakt en gesynchroniseerd.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-05.png" alt-text="Gebruikersinterface voor de replicatiestatus in Azure Portal":::


## <a name="enable-admin-account"></a>Beheerdersaccount schakelen

In volgende zelfstudies implementeert u een containerinstallatiekopie vanuit het register rechtstreeks naar Web App for Containers. Als u deze mogelijkheid wilt inschakelen, moet u ook het [beheerdersaccount](container-registry-authentication.md#admin-account) van het register inschakelen.

Navigeer naar uw nieuwe containerregister in Azure Portal en selecteer **Toegangssleutels** onder **Instellingen**: Selecteer onder **Gebruiker met beheerdersrechten** de optie **Inschakelen**.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-06.png" alt-text="Beheerdersaccount inschakelen in Azure Portal":::


## <a name="container-registry-login"></a>Aanmelden bij het containerregister

Nu u geo-replicatie hebt geconfigureerd, maakt u een containerinstallatiekopie en pusht u deze naar het register. U moet u eerst aanmelden bij uw register voordat u er installatiekopieën naar pusht.

Gebruik de opdracht [az acr login](/cli/azure/acr#az_acr_login) om de referenties voor het register te verifiëren en in de cache te plaatsen. Vervang `<acrName>` door de naam van het register dat u eerder hebt gemaakt.

```azurecli
az acr login --name <acrName>
```

De opdracht retourneert `Login Succeeded` nadat deze is voltooid.

## <a name="get-application-code"></a>Toepassingscode ophalen

Het voorbeeld in deze zelfstudie bevat een kleine webtoepassing die is gebouwd in [ASP.NET Core][aspnet-core]. De app dient voor een HTML-pagina waarop de regio wordt weergegeven waaruit de installatiekopie is geïmplementeerd door Azure Container Registry.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-app-01.png" alt-text="Zelfstudie-app weergegeven in browser":::

Gebruik Git om het voorbeeld te downloaden naar een lokale map en `cd` naar de map:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

Als u `git` niet hebt geïnstalleerd, kunt u rechtstreeks vanuit GitHub [het ZIP-archief downloaden][acr-helloworld-zip].

## <a name="update-dockerfile"></a>Dockerfile bijwerken

Het Dockerfile dat deel uitmaakt van de voorbeeldopslagplaats laat zien hoe de container wordt gebouwd. Het begint met een officiële ASP.NET Core-runtime-installatiekopie, kopieert de toepassingsbestanden naar de container, installeert afhankelijkheden, compileert de uitvoer met de officiële .NET Core SDK-installatiekopie, en maakt tot slot een geoptimaliseerde aspnetcore-installatiekopie.

De [Dockerfile][dockerfile] bevindt zich in `./AcrHelloworld/Dockerfile` in de gekloonde bron.

```Dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

De toepassing in de *acr-helloworld*-installatiekopie probeert de regio te bepalen waaruit de container is geïmplementeerd door in DNS een query uit te voeren naar informatie over de aanmeldingsserver van het register. U moet de Fully Qualified Domain Name (FQDN) van de aanmeldingsserver van het register opgeven in de omgevingsvariabele `DOCKER_REGISTRY` in de Dockerfile.

Haal eerst de aanmeldingsserver van het register op met de opdracht `az acr show`. Vervang `<acrName>` door de naam van het register dat u in de vorige stappen hebt gemaakt.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Uitvoer:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Werk vervolgens de regel `ENV DOCKER_REGISTRY` bij met de FQDN van de aanmeldingsserver van uw register. In dit voorbeeld wordt de naam van het voorbeeldregister gebruikt, *uniqueregistryname*:

```Dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Containerinstallatiekopie maken

Nu u de Dockerfile hebt bijgewerkt met de FQDN van de aanmeldingsserver van uw register, kunt u `docker build` gebruiken om de containerinstallatiekopie te maken. Voer de volgende opdracht uit om de installatiekopie te maken en te taggen met de URL van uw privéregister, waarbij u `<acrName>` opnieuw vervangt door de naam van uw register:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Terwijl de Docker-installatiekopie wordt gemaakt (hier ingekort weergegeven) worden verschillende uitvoerregels weergegeven:

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS base
2.2: Pulling from mcr.microsoft.com/dotnet/core/aspnet
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Gebruik `docker images` om de gebouwde en getagde installatiekopie te zien:

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>Installatiekopie pushen naar Azure Container Registry

Gebruik vervolgens de opdracht `docker push` om de *acr-helloworld*-installatiekopie naar het register te pushen. Vervang `<acrName>` door de naam van uw register.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Omdat u het register voor de geo-replicatie hebt geconfigureerd, wordt uw installatiekopie automatisch gerepliceerd naar zowel de regio *VS - west* als de regio *VS - oost* met deze enkele `docker push`-opdracht.

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een privécontainerregister met geo-replicatie gemaakt, een containerinstallatiekopie gemaakt en die installatiekopie vervolgens naar het register gepusht.

Ga naar de volgende zelfstudie om uw container te implementeren op meerdere Web Apps for Containers-instanties, met behulp van geo-replicatie om de installatiekopieën lokaal te gebruiken.

> [!div class="nextstepaction"]
> [Web-app implementeren vanuit Azure Container Registry](container-registry-tutorial-deploy-app.md)

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: https://dot.net
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile
