---
title: Een container & pushen
description: Docker-afbeeldingen pushen en pullen naar uw privécontainerregister in Azure met behulp van de Docker CLI
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 48f5f1707881ac8461e12212be631d3b80c16ca7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783823"
---
# <a name="push-your-first-image-to-your-azure-container-registry-using-the-docker-cli"></a>Uw eerste afbeelding naar uw Azure-containerregister pushen met behulp van de Docker CLI

In een Azure-containerregister worden privécontainer-afbeeldingen en andere [](https://hub.docker.com/) artefacten op een soortgelijke manier op de Docker Hub docker-containerafbeeldingen op slaat. U kunt de Docker-opdrachtregelinterface (Docker CLI) gebruiken voor [aanmeldings-,](https://docs.docker.com/engine/reference/commandline/login/) [push-,](https://docs.docker.com/engine/reference/commandline/cli/) [pull-](https://docs.docker.com/engine/reference/commandline/pull/)en andere container-imagebewerkingen in uw containerregister. [](https://docs.docker.com/engine/reference/commandline/push/)

In de volgende stappen downloadt u een openbare [Nginx-afbeelding,](https://store.docker.com/images/nginx)tagt u deze voor uw persoonlijke Azure-containerregister, pusht u deze naar het register en haalt u deze vervolgens op uit het register.

## <a name="prerequisites"></a>Vereisten

* **Azure-containerregister**: maak een containerregister in uw Azure-abonnement. Gebruik bijvoorbeeld de [Azure Portal](container-registry-get-started-portal.md) of de [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI:** u moet Docker ook lokaal hebben geïnstalleerd. Docker biedt pakketten die eenvoudig Docker configureren op elk [macOS][docker-mac]-, [Windows][docker-windows]- of [Linux][docker-linux]-systeem.

## <a name="log-in-to-a-registry"></a>Aanmelden bij een register

Er zijn [verschillende manieren om te verifiëren](container-registry-authentication.md) bij uw privécontainerregister. De aanbevolen methode bij het werken in een opdrachtregel is met de Azure CLI-opdracht [az acr login](/cli/azure/acr#az_acr_login). Als u zich bijvoorbeeld wilt aanmelden bij een register met de naam *myregistry,* moet u zich aanmelden bij de Azure CLI en vervolgens verifiëren bij uw register:

```azurecli
az login
az acr login --name myregistry
```

U kunt zich ook aanmelden met [docker-aanmelding.](https://docs.docker.com/engine/reference/commandline/login/) U hebt bijvoorbeeld een [service-principal aan](container-registry-authentication.md#service-principal) uw register toegewezen voor een automatiseringsscenario. Wanneer u de volgende opdracht hebt uitgevoerd, geeft u interactief de appID (gebruikersnaam) en het wachtwoord van de service-principal op wanneer u hier om wordt gevraagd. Zie de naslag voor de opdracht [docker login](https://docs.docker.com/engine/reference/commandline/login/) voor best practices voor het beheren van aanmeldingsreferenties:

```
docker login myregistry.azurecr.io
```

Beide opdrachten retourneren `Login Succeeded` zodra ze zijn voltooid.
> [!NOTE]
>* Mogelijk wilt u Visual Studio Code met Docker-extensie gebruiken voor een snellere en handigere aanmelding.

> [!TIP]
> Geef altijd de volledig gekwalificeerde registernaam (in kleine letters) op wanneer u gebruikt en wanneer u afbeeldingen tagt voor `docker login` pushen naar het register. In de voorbeelden in dit artikel is de volledig gekwalificeerde naam *myregistry.azurecr.io*.

## <a name="pull-a-public-nginx-image"></a>Een openbare Nginx-afbeelding pullen

Haal eerst een openbare Nginx-afbeelding op naar uw lokale computer. In dit voorbeeld wordt een afbeelding uit Microsoft Container Registry.

```
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

## <a name="run-the-container-locally"></a>De container lokaal uitvoeren

Voer de volgende [opdracht docker run](https://docs.docker.com/engine/reference/run/) uit om een lokaal exemplaar van de Nginx-container interactief te starten ( `-it` ) op poort 8080. Het `--rm` argument geeft aan dat de container moet worden verwijderd wanneer u deze stopt.

```
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Blader naar `http://localhost:8080` om de standaardwebpagina van Nginx weer te geven in de container die wordt uitgevoerd. Als het goed is, ziet u een pagina die er ongeveer als volgt uit ziet:

![Nginx op lokale computer](./media/container-registry-get-started-docker-cli/nginx.png)

Omdat u de container interactief hebt gestart met , ziet u de uitvoer van de Nginx-server op de opdrachtregel nadat u er `-it` in uw browser naar hebt ge navigeren.

Druk op om de container te stoppen en te `Control` + `C` verwijderen.

## <a name="create-an-alias-of-the-image"></a>Een alias van de afbeelding maken

Gebruik [docker tag om](https://docs.docker.com/engine/reference/commandline/tag/) een alias van de -afbeelding te maken met het volledig gekwalificeerde pad naar uw register. In dit voorbeeld wordt de naamruimte `samples` gespecificeerd om overbodige items in de hoofdmap van het register te voorkomen.

```
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx
```

Zie de sectie Opslagplaatsnaamruimten van [](container-registry-best-practices.md#repository-namespaces) Best practices voor Azure Container Registry voor meer informatie over [taggen met naamruimten.](container-registry-best-practices.md)

## <a name="push-the-image-to-your-registry"></a>De installatiekopie naar uw register pushen

Nu u de afbeelding hebt gelabeld met het volledig gekwalificeerde pad naar uw privéregister, kunt u deze naar het register pushen met [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>De installatiekopie vanuit uw register ophalen

Gebruik de [opdracht docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om de -afbeelding uit het register op te halen:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>De Nginx-container starten

Gebruik de [opdracht docker run](https://docs.docker.com/engine/reference/run/) om de afbeelding uit het register uit te voeren:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Blader naar `http://localhost:8080` om de container te bekijken die wordt uitgevoerd.

Druk op om de container te stoppen en te `Control` + `C` verwijderen.

## <a name="remove-the-image-optional"></a>De afbeelding verwijderen (optioneel)

Als u de Nginx-afbeelding niet meer nodig hebt, kunt u deze lokaal verwijderen met de [opdracht docker rmi.](https://docs.docker.com/engine/reference/commandline/rmi/)

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Als u afbeeldingen uit uw Azure-containerregister wilt verwijderen, kunt u de Azure CLI-opdracht [az acr repository delete gebruiken.](/cli/azure/acr/repository#az_acr_repository_delete) Met de volgende opdracht verwijdert u bijvoorbeeld het manifest waarnaar wordt verwezen door de tag, unieke laaggegevens en alle andere tags die naar het `samples/nginx:latest` manifest verwijzen.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Volgende stappen

Nu u de basisprincipes kent, kunt u uw register gaan gebruiken. Implementeer bijvoorbeeld containerafbeeldingen uit uw register om:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Installeer eventueel de [Docker-extensie voor Visual Studio-code](https://code.visualstudio.com/docs/azure/docker) en de [Azure-account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)extensie om te werken met uw Azure-containerregisters. Pull en push installatiekopieën naar een Azure-containerregister of voer ACR-taken uit, allemaal in Visual Studio-code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
