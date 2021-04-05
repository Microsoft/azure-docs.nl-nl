---
title: Zelfstudie - Docker gebruiken om groepen met meerdere containers te implementeren
description: Gebruik Docker Compose om een toepassing met meerdere containers te bouwen en uit te voeren en vervolgens de toepassing in Azure Container Instances te brengen
ms.topic: tutorial
ms.date: 10/28/2020
ms.custom: ''
ms.openlocfilehash: a71ff438feaef555a85c33d818c287c64621d40d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913837"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>Zelfstudie: Een groep met meerdere containers implementeren met behulp van Docker Compose 

In deze zelfstudie gebruikt u [Docker Compose](https://docs.docker.com/compose/) om een toepassing met meerdere containers lokaal te definiëren en uit te voeren en deze vervolgens te implementeren als een [containergroep](container-instances-container-groups.md) in Azure Container Instances. 

Voer op aanvraag containers uit in Azure Container Instances wanneer u cloud-apps ontwikkelt met Docker en u naadloos wilt kunnen overschakelen van lokaal ontwikkelen naar cloudimplementatie. Deze mogelijkheid wordt ingeschakeld door [integratie tussen Docker en Azure](https://docs.docker.com/engine/context/aci-integration/). U kunt systeemeigen Docker-opdrachten gebruiken voor het uitvoeren van een [instantie met één container](quickstart-docker-cli.md) of een groep met meerdere containers in Azure.

> [!IMPORTANT]
> Niet alle functies van Azure Container Instances worden ondersteund. Geef feedback over de integratie van Docker en Azure door een probleem aan te maken in de GitHub-opslagplaats [Docker ACI-integratie](https://github.com/docker/aci-integration-beta).

> [!TIP]
> U kunt de [Docker-extensie](https://aka.ms/VSCodeDocker) voor Visual Studio Code gebruiken om te werken met een geïntegreerde ervaring voor het ontwikkelen, uitvoeren en beheren van containers, installatiekopieën en contexten.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een Azure-containerregister maken
> * Toepassingsbroncode klonen vanuit GitHub
> * Docker Compose gebruiken om een installatiekopie te bouwen en een toepassing met meerdere containers lokaal uit te voeren
> * De installatiekopie van de toepassing naar het containerregister pushen
> * Een Azure-context maken voor Docker
> * De toepassing openen in Azure Container Instances

## <a name="prerequisites"></a>Vereisten

* **Azure CLI**: de Azure CLI moet op uw lokale computer zijn geïnstalleerd. Versie 2.10.1 of hoger wordt aanbevolen. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

* **Docker Desktop**: u moet Docker Desktop versie 2.3.0.5 of hoger gebruiken. Deze versie is beschikbaar voor [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) of [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). U kunt ook de [Docker ACI Integration CLI voor Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux) installeren.

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>Toepassingscode ophalen

De voorbeeldtoepassing die wordt gebruikt in deze zelfstudie, is een eenvoudige stem-app. De toepassing bestaat uit een front-endwebonderdeel en een back-end-Redis-exemplaar. Het webonderdeel is verpakt in een aangepaste containerinstallatiekopie. Het Redis-exemplaar gebruikt een ongewijzigde installatiekopie van Docker Hub.

Gebruik [git](https://git-scm.com/downloads) om de voorbeeldtoepassing te klonen naar uw ontwikkelomgeving:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Wijzig in de gekloonde map.

```console
cd azure-voting-app-redis
```

In de map bevindt zich de broncode van de toepassing en een vooraf gemaakt Docker-compositiebestand, docker-compose.yaml.

## <a name="modify-docker-compose-file"></a>Docker-compositiebestand wijzigen

Open docker-compose.yaml in een teksteditor. Het bestand configureert de `azure-vote-back`- en `azure-vote-front`-services.

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

Breng de volgende twee wijzigingen aan in de `azure-vote-front`-configuratie:

1. Werk de eigenschap `image` in de `azure-vote-front`-service bij. Geef de naam van de installatiekopie een voorvoegsel met de aanmeldingsservernaam van uw Azure-containerregister, \<acrName\>. azurecr.io. Als uw register bijvoorbeeld de *myregistry* heeft, wordt de naam van de aanmeldingsserver *myregistry.azurecr.io* (allemaal kleine letters). De eigenschap van de installatiekopie is dan `myregistry.azurecr.io/azure-vote-front`.
1. Wijzig de `ports`-toewijzing in `80:80`. Sla het bestand op.

Het bijgewerkte bestand moet er ongeveer als volgt uitzien:

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Als u deze vervangingen aanbrengt, wordt de `azure-vote-front`-installatiekopie die u in de volgende stap gaat maken, gelabeld voor uw Azure-containerregister en kan de installatiekopie worden opgehaald om te worden uitgevoerd in Azure Container Instances.

> [!TIP]
> U hoeft geen Azure-containerregister te gebruiken voor dit scenario. U kunt bijvoorbeeld een privé-opslagplaats in Docker Hub kiezen om uw toepassingsinstallatiekopie te hosten. Als u een ander register kiest, werkt u de eigenschap van de installatiekopie aan de hand hiervan bij.

## <a name="run-multi-container-application-locally"></a>Toepassing met meerdere containers lokaal uitvoeren

Voer [docker-compose up](https://docs.docker.com/compose/reference/up/) uit, dat het voorbeeldbestand `docker-compose.yaml` gebruikt om de containerinstallatiekopie te maken, de Redis-installatiekopie te downloaden en de toepassing te starten:

```console
docker-compose up --build -d
```

Wanneer dit is voltooid, gebruikt u de opdracht [docker images](https://docs.docker.com/engine/reference/commandline/images/) om de gemaakte installatiekopieën te bekijken. Er zijn drie installatiekopieën gedownload of gemaakt. De installatiekopie `azure-vote-front` bevat de front-endtoepassing, die de installatiekopie `uwsgi-nginx-flask` bevat als basis. De `redis`-installatiekopie wordt gebruikt om een Redis-exemplaar te starten.

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
mcr.microsoft.com/oss/bitnami/redis       6.0.8      3a54a920bb6c        4 weeks ago          103MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

Voer de opdracht [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) uit om de actieve containers te zien:

```
$ docker ps

CONTAINER ID        IMAGE                                      COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front     "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b62b47a7d313        mcr.microsoft.com/oss/bitnami/redis:6.0.8  "/opt/bitnami/script…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

Als u wilt zien hoe de toepassing wordt uitgevoerd, typt u `http://localhost:80` in een lokale browser. De voorbeeldtoepassing wordt dan geladen, zoals wordt weergegeven in het volgende voorbeeld:

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="Installatiekopie van stem-app":::

Nadat u de lokale toepassing hebt geprobeerd, voert u [docker-compose down](https://docs.docker.com/compose/reference/down/) uit om de toepassing te stoppen en de containers te verwijderen.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>Installatiekopie pushen naar containerregister

Als u de toepassing wilt implementeren op Azure Container Instances, moet u de `azure-vote-front`-installatiekopie naar het containerregister pushen. Voer [docker-compose push](https://docs.docker.com/compose/reference/push) uit om de installatiekopie te pushen:

```console
docker-compose push
```

Het kan een paar minuten duren om naar het register te pushen.

Als u wilt controleren of de installatiekopie in het register is opgeslagen, voert u de opdracht [az acr repository show](/cli/azure/acr/repository#az-acr-repository-show) uit:

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Toepassing implementeren in Azure Container Instances

Schakel vervolgens naar de ACI-context. Alle volgende Docker-opdrachten worden uitgevoerd in deze context.

```console
docker context use myacicontext
```

Voer `docker compose up` uit om de toepassing te starten in Azure Container Instances. De `azure-vote-front`-installatie kopie wordt opgehaald uit het containerregister en de containergroep wordt gemaakt in Azure Container Instances.

```console
docker compose up
```

> [!NOTE]
> Docke Compose-opdrachten die momenteel beschikbaar zijn in een ACI-context, zijn `docker compose up` en `docker compose down`. Er staat geen streepje tussen `docker` en `compose` in deze opdrachten.

In korte tijd wordt de containergroep geïmplementeerd. Voorbeelduitvoer:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

Voer `docker ps` uit om de actieve containers en het IP-adres dat is toegewezen aan de container groep weer te geven.

```console
docker ps
```

Voorbeelduitvoer:

```
CONTAINER ID                           IMAGE                                         COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8                         Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                            Running             52.179.23.131:80->80/tcp
```

Als u de actieve toepassing in de cloud wilt zien, voert u het weergegeven IP-adres in een lokale webbrowser in. In dit voorbeeld voert u `52.179.23.131` in. De voorbeeldtoepassing wordt dan geladen, zoals wordt weergegeven in het volgende voorbeeld:

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="Installatiekopie van stem-app in ACI":::

Als u de logboeken van de front-endcontainer wilt weergeven, voert u de opdracht [docker logs](https://docs.docker.com/engine/reference/commandline/logs) uit. Bijvoorbeeld:

```console
docker logs azurevotingappredis_azure-vote-front
```

U kunt ook de Azure Portal of andere Azure-hulpprogramma's gebruiken om de eigenschappen en status van de door u geïmplementeerde containergroep te bekijken.

Wanneer u klaar bent met het uitvoeren van de toepassing, stopt u de toepassing en de containers met `docker compose down`:

```console
docker compose down
```

Met deze opdracht wordt de containergroep verwijderd uit Azure Container Instances.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Docker Compose gebruikt om te schakelen van het lokaal uitvoeren van een toepassing met meerdere containers naar het uitvoeren in Azure Container Instances. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Azure-containerregister maken
> * Toepassingsbroncode klonen vanuit GitHub
> * Docker Compose gebruiken om een installatiekopie te bouwen en een toepassing met meerdere containers lokaal uit te voeren
> * De installatiekopie van de toepassing naar het containerregister pushen
> * Een Azure-context maken voor Docker
> * De toepassing openen in Azure Container Instances

U kunt ook de [Docker-extensie](https://aka.ms/VSCodeDocker) voor Visual Studio Code gebruiken om te werken met een geïntegreerde ervaring voor het ontwikkelen, uitvoeren en beheren van containers, installatiekopieën en contexten.

Als u gebruik wilt maken van meer functies in Azure Container Instances, kunt u de Azure-hulpprogramma's gebruiken om een groep met meerdere containers op te geven. Raadpleeg bijvoorbeeld de zelfstudies voor het implementeren van een containergroep met behulp van de Azure CLI met een [YAML-bestand](container-instances-multi-container-yaml.md)of implementeren met behulp van een [Azure Resource Manager-sjabloon](container-instances-multi-container-group.md). 