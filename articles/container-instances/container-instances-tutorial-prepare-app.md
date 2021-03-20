---
title: 'Zelfstudie: containerinstallatiekopie voorbereiden voor implementatie'
description: Zelfstudie 1 van 3 voor Azure Container Instances - Een app voorbereiden in een containerinstallatiekopie voor implementatie in Azure Container Instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc, devx-track-js
ms.openlocfilehash: 1eca34fb5da50b15f15322a3b534556bcd3bda09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91303111"
---
# <a name="tutorial-create-a-container-image-for-deployment-to-azure-container-instances"></a>Zelfstudie: een containerinstallatiekopie voor implementatie in Azure Container Instances maken

Met Azure Container Instances kunt u Docker-containers implementeren in de Azure-infrastructuur zonder virtuele machines in te richten of gebruik te maken van een service op een hoger niveau. In deze zelfstudie verpakt u een kleine Node.js-webtoepassing in een containerinstallatiekopie die kan worden uitgevoerd met behulp van Azure Container Instances.

In dit artikel, deel één uit de reeks, voert u de volgende handelingen uit:

> [!div class="checklist"]
> * Toepassingsbroncode klonen vanuit GitHub
> * Containerinstallatiekopie maken uit de toepassingsbron
> * Installatiekopie testen in een lokale Docker-omgeving

In deel twee en drie van de zelfstudie uploadt u uw installatiekopie naar Azure Container Registry en implementeert u deze vervolgens in Azure Container Instances.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="get-application-code"></a>Toepassingscode ophalen

De voorbeeldtoepassing in deze zelfstudie is een eenvoudige webtoepassing die is gebouwd in [Node.js][nodejs]. De toepassing dient een statische HTML-pagina op en lijkt op de volgende schermafbeelding:

![Zelfstudie-app weergegeven in browser][aci-tutorial-app]

Gebruik Git om de opslagplaats van de voorbeeldtoepassing te klonen:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

U kunt het ZIP-archief ook rechtstreeks van GitHub [downloaden][aci-helloworld-zip].

## <a name="build-the-container-image"></a>De containerinstallatiekopie bouwen

Het Dockerfile in de voorbeeldtoepassing laat zien hoe de container wordt gebouwd. Het begint met een [officiële Node.js-installatiekopie][docker-hub-nodeimage] op basis van [Alpine Linux][alpine-linux], een kleine distributie die zeer geschikt is voor gebruik met containers. Vervolgens worden de toepassingsbestanden naar de container gekopieerd, afhankelijkheden geïnstalleerd met behulp van de Node Package Manager, en wordt ten slotte de toepassing gestart.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Gebruik de opdracht [docker build][docker-build] om de containerinstallatiekopie te maken en tag deze als *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

De uitvoer van de opdracht [docker build][docker-build] lijkt op (ingekort om makkelijk te kunnen lezen):

```console
$ docker build ./aci-helloworld -t aci-tutorial-app
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Gebruik de opdracht [docker images][docker-images] om de gemaakte installatiekopie te bekijken:

```bash
docker images
```

Uw pasgebouwde installatiekopie behoort in de lijst te verschijnen:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 seconds ago    68.1 MB
```

## <a name="run-the-container-locally"></a>De container lokaal uitvoeren

Voer voordat u de container implementeert naar Azure Container Instances [docker run][docker-run] uit om de container lokaal uit te voeren en te controleren of deze werkt. Met de schakeloptie `-d` wordt de container op de achtergrond uitgevoerd en met `-p` kunt u een willekeurige poort op uw computer toewijzen aan poort 80 in de container.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Uitvoer van de `docker run`-opdracht geeft de id van de actieve container weer als de opdracht geslaagd is:

```console
$ docker run -d -p 8080:80 aci-tutorial-app
a2e3e4435db58ab0c664ce521854c2e1a1bda88c9cf2fcff46aedf48df86cccf
```

Ga nu in uw browser naar `http://localhost:8080` om te bevestigen dat de container wordt uitgevoerd. U ziet een webpagina die lijkt op de volgende:

![De app lokaal uitvoeren in de browser][aci-tutorial-app-local]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een containerinstallatiekopie gemaakt die kan worden geïmplementeerd in Azure Container Instances, en geverifieerd dat deze lokaal werkt. Tot nu toe hebt u het volgende gedaan:

> [!div class="checklist"]
> * De toepassingsbron is gekloond vanuit GitHub
> * Een containerinstallatiekopie gemaakt uit de toepassingsbron
> * De container is lokaal uitgevoerd

Ga verder met de volgende zelfstudie in de reeks voor meer informatie over het opslaan van uw containerinstallatiekopie in Azure Container Registry:

> [!div class="nextstepaction"]
> [Installatiekopie pushen naar Azure Container Registry](container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[aci-helloworld-zip]: https://github.com/Azure-Samples/aci-helloworld/archive/master.zip
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
