---
title: Pull-container installatie kopie push &
description: Docker-installatie kopieën pushen en pullen naar uw persoonlijke container register in azure met behulp van de docker-CLI
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 83ef385313b035f5e5d7d993e7948725906c75a7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99987766"
---
# <a name="push-your-first-image-to-your-azure-container-registry-using-the-docker-cli"></a>Uw eerste installatie kopie naar Azure container Registry pushen met de docker-CLI

In een Azure container Registry worden persoonlijke container installatie kopieën en andere artefacten opgeslagen en beheerd, vergelijkbaar met de manier waarop [docker hub](https://hub.docker.com/) open bare docker-container installatie kopieën opslaat. U kunt de [docker-opdracht regel interface](https://docs.docker.com/engine/reference/commandline/cli/) (docker CLI) gebruiken voor de bewerkingen voor [Aanmelden](https://docs.docker.com/engine/reference/commandline/login/), [pushen](https://docs.docker.com/engine/reference/commandline/push/), [pull](https://docs.docker.com/engine/reference/commandline/pull/)en andere container installatie kopieën in het container register.

In de volgende stappen downloadt u een open bare [nginx-installatie kopie](https://store.docker.com/images/nginx), labelt u deze voor uw persoonlijke Azure-container register, pusht u deze naar uw REGI ster en haalt u het op uit het REGI ster.

## <a name="prerequisites"></a>Vereisten

* **Azure-containerregister**: maak een containerregister in uw Azure-abonnement. Gebruik bijvoorbeeld de [Azure Portal](container-registry-get-started-portal.md) of de [Azure cli](container-registry-get-started-azure-cli.md).
* **Docker cli** : u moet ook docker lokaal hebben geïnstalleerd. Docker biedt pakketten die eenvoudig Docker configureren op elk [macOS][docker-mac]-, [Windows][docker-windows]- of [Linux][docker-linux]-systeem.

## <a name="log-in-to-a-registry"></a>Aanmelden bij een register

Er zijn [verschillende manieren om te verifiëren](container-registry-authentication.md) bij uw persoonlijke container register. De aanbevolen methode voor het werken op een opdracht regel is met de Azure CLI-opdracht [AZ ACR login](/cli/azure/acr#az-acr-login). Als u zich bijvoorbeeld wilt aanmelden bij een REGI ster met de naam *myregistry*, meldt u zich aan bij de Azure CLI en voert u vervolgens een verificatie uit voor het REGI ster:

```azurecli
az login
az acr login --name myregistry
```

U kunt zich ook aanmelden met [docker-aanmelding](https://docs.docker.com/engine/reference/commandline/login/). Stel dat u [een Service-Principal hebt toegewezen](container-registry-authentication.md#service-principal) aan het REGI ster voor een automatiserings scenario. Wanneer u de volgende opdracht uitvoert, geeft u de Service-Principal appID (gebruikers naam) en het wacht woord interactief op wanneer u hierom wordt gevraagd. Zie voor aanbevolen procedures voor het beheren van aanmeldings referenties de opdracht referentie voor [docker-aanmelding](https://docs.docker.com/engine/reference/commandline/login/) :

```
docker login myregistry.azurecr.io
```

Beide opdrachten retour neren `Login Succeeded` eenmaal voltooid.
> [!NOTE]
>* Mogelijk wilt u Visual Studio code gebruiken met docker-uitbrei ding voor een snellere en handigere aanmelding.

> [!TIP]
> Geef altijd de volledig gekwalificeerde register naam op (alle kleine letters) wanneer u gebruikt `docker login` en wanneer u afbeeldingen labelt voor het naar uw REGI ster pushen. In de voor beelden in dit artikel is de volledig gekwalificeerde naam *myregistry.azurecr.io*.

## <a name="pull-a-public-nginx-image"></a>Een open bare nginx-installatie kopie verzamelen

Haal eerst een open bare nginx-installatie kopie op uw lokale computer op. In dit voor beeld wordt een afbeelding opgehaald uit micro soft Container Registry.

```
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

## <a name="run-the-container-locally"></a>De container lokaal uitvoeren

Voer de volgende opdracht [docker run uit](https://docs.docker.com/engine/reference/run/) om een lokaal exemplaar van de nginx-container interactief te starten ( `-it` ) op poort 8080. Het `--rm` argument geeft aan dat de container moet worden verwijderd wanneer u deze stopt.

```
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Blader naar `http://localhost:8080` om de standaard webpagina weer te geven die wordt aangeboden door nginx in de container die wordt uitgevoerd. Er wordt een pagina weer gegeven die er ongeveer als volgt uitziet:

![Nginx op lokale computer](./media/container-registry-get-started-docker-cli/nginx.png)

Omdat u de container interactief met hebt gestart `-it` , ziet u de uitvoer van de nginx-server op de opdracht regel nadat u deze in uw browser hebt genavigeerd.

Als u de container wilt stoppen en verwijderen, drukt u op `Control` + `C` .

## <a name="create-an-alias-of-the-image"></a>Een alias van de installatie kopie maken

Gebruik [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) om een alias van de installatie kopie te maken met het volledig gekwalificeerde pad naar uw REGI ster. In dit voorbeeld wordt de naamruimte `samples` gespecificeerd om overbodige items in de hoofdmap van het register te voorkomen.

```
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx
```

Zie de sectie [naam ruimten van opslag](container-registry-best-practices.md#repository-namespaces) plaatsen in [aanbevolen procedures voor Azure container Registry](container-registry-best-practices.md)voor meer informatie over het labelen met naam ruimten.

## <a name="push-the-image-to-your-registry"></a>De installatiekopie naar uw register pushen

Nu u de installatie kopie hebt gelabeld met het volledig gekwalificeerde pad naar uw persoonlijke REGI ster, kunt u deze naar het REGI ster pushen met [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>De installatiekopie vanuit uw register ophalen

Gebruik de opdracht [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om de installatie kopie uit het REGI ster te halen:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>De Nginx-container starten

Gebruik de opdracht [docker run](https://docs.docker.com/engine/reference/run/) voor het uitvoeren van de installatie kopie die u uit het REGI ster hebt opgehaald:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Blader naar `http://localhost:8080` om de actieve container weer te geven.

Als u de container wilt stoppen en verwijderen, drukt u op `Control` + `C` .

## <a name="remove-the-image-optional"></a>De installatie kopie verwijderen (optioneel)

Als u de nginx-installatie kopie niet meer nodig hebt, kunt u deze lokaal verwijderen met de opdracht [docker RMI](https://docs.docker.com/engine/reference/commandline/rmi/) .

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Als u installatie kopieën uit het Azure container Registry wilt verwijderen, kunt u de Azure CLI-opdracht [AZ ACR repository delete](/cli/azure/acr/repository#az-acr-repository-delete)gebruiken. Met de volgende opdracht wordt bijvoorbeeld het manifest verwijderd waarnaar wordt verwezen door de `samples/nginx:latest` tag, alle unieke laag gegevens en alle andere tags die verwijzen naar het manifest.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes kent, bent u klaar om uw REGI ster te gaan gebruiken. Implementeer bijvoorbeeld container installatie kopieën uit het REGI ster naar:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Installeer eventueel de [Docker-extensie voor Visual Studio-code](https://code.visualstudio.com/docs/azure/docker) en de [Azure-account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)extensie om te werken met uw Azure-containerregisters. Pull en push installatiekopieën naar een Azure-containerregister of voer ACR-taken uit, allemaal in Visual Studio-code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
