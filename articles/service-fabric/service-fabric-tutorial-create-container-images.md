---
title: Containerinstallatiekopieën maken op Service Fabric in Azure
description: In deze zelfstudie leert u hoe u containerinstallatiekopieën maakt voor een Service Fabric-toepassing met meerdere containers.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 31b5f870465bc1dff9d6ff7827a4efed084bcf62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92739057"
---
# <a name="tutorial-create-container-images-on-a-linux-service-fabric-cluster"></a>Zelfstudie: Containerinstallatiekopieën maken in een Service Fabric-cluster in Linux

Deze zelfstudie is het eerste deel van een reeks zelfstudies waarin wordt gedemonstreerd hoe u containers gebruikt in een Linux Service Fabric-cluster. In deze zelfstudie wordt een toepassing met meerdere containers voorbereid voor gebruik met Service Fabric. In volgende zelfstudies worden deze installatiekopieën gebruikt als onderdeel van een Service Fabric-toepassing. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een toepassingsbron klonen vanuit GitHub
> * Een containerinstallatiekopie maken uit de toepassingsbron
> * Een ACR-exemplaar (Azure Container Registry) implementeren
> * Een containerinstallatiekopie voor ACR taggen
> * De installatiekopie uploaden naar ACR

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Containerinstallatiekopieën maken voor Service Fabric
> * [Een Service Fabric-toepassing maken en implementeren met containers](service-fabric-tutorial-package-containers.md)
> * [Hoe failover en schalen worden verwerkt in Service Fabric](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>Vereisten

* Er moet een Linux-ontwikkelomgeving zijn ingesteld voor Service Fabric. Volg [deze instructies](service-fabric-get-started-linux.md) voor het instellen van de Linux-omgeving.
* Voor deze zelfstudie moet u de versie Azure CLI 2.0.4 of later uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).
* Bovendien moet u beschikken over een Azure-abonnement. Kijk [hier](https://azure.microsoft.com/free/) voor meer informatie over een gratis evaluatieversie.

## <a name="get-application-code"></a>Toepassingscode ophalen

De voorbeeldtoepassing die wordt gebruikt in deze zelfstudie, is een app om mee te stemmen. De toepassing bestaat uit een front-endwebonderdeel en een back-end-Redis-exemplaar. De onderdelen zijn verpakt in containerinstallatiekopieën.

Gebruik git om een kopie van de toepassing te downloaden naar de ontwikkelomgeving.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

De oplossing bevat twee mappen en een bestand docker-compose.yml. De map azure-vote bevat de Python-front-endservice en ook het Dockerfile dat is gebruikt om de installatiekopie te bouwen. De map Voting bevat het Service Fabric-toepassingspakket dat is geïmplementeerd in het cluster. Deze mappen bevatten de benodigde activa voor deze zelfstudie.

## <a name="create-container-images"></a>Containerinstallatiekopieën maken

Voer in de map **azure-vote** de volgende opdracht uit om de installatiekopie voor het front-endwebonderdeel te bouwen. Deze opdracht maakt gebruik van het Dockerfile in deze map om de installatiekopie te bouwen.

```bash
docker build -t azure-vote-front .
```
> [!Note]
> Als u de toegang wordt geweigerd, volgt u [deze](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user) documentatie over het werken met docker zonder sudo.

Deze opdracht kan enige tijd in beslag nemen omdat alle noodzakelijke afhankelijkheden moeten worden opgehaald uit Docker Hub. Wanneer dit is voltooid, gebruikt u de opdracht [docker images](https://docs.docker.com/engine/reference/commandline/images/) om de gemaakte installatiekopieën te bekijken.

```bash
docker images
```

U ziet dat er twee installatiekopieën zijn gedownload of gemaakt. De installatiekopie *azure-vote-front* bevat de toepassing. Deze is afgeleid uit een *Python*-installatiekopie van Docker Hub.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Azure Container Registry implementeren

Voer eerst de opdracht **az login** uit om u aan te melden bij uw Azure-account.

```azurecli
az login
```

Gebruik vervolgens de opdracht **az account** om het abonnement te kiezen waarin het Azure Container Registry moet worden gemaakt. U moet de abonnements-id van uw Azure-abonnement invoeren op de plek <abonnements-id>.

```azurecli
az account set --subscription <subscription_id>
```

Wanneer u een Azure Container Registry implementeert, hebt u eerst een resourcegroep nodig. Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Een resourcegroep maken met de opdracht **az group create**. In dit voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt in de regio *westus*.

```azurecli
az group create --name <myResourceGroup> --location westus
```

Maak een Azure Container Registry met de opdracht **az acr create**. Vervang \<acrName> door de naam van het containerregister dat u wilt maken in het abonnement. Deze naam moet alfanumeriek en uniek zijn.

```azurecli
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

In de rest van deze zelfstudie wordt acrName gebruikt als tijdelijke aanduiding voor de naam van het gekozen containerregister. Noteer deze waarde.

## <a name="sign-in-to-your-container-registry"></a>Aanmelden bij het containerregister

Meld u aan bij het ACR-exemplaar voordat u installatiekopieën naar het exemplaar pusht. Gebruik de opdracht **az acr login** om de bewerking te voltooien. Geef de unieke naam op die u het containerregister hebt gegeven toen u het maakte.

```azurecli
az acr login --name <acrName>
```

De opdracht retourneert een bericht dat de aanmelding is gelukt.

## <a name="tag-container-images"></a>Containerinstallatiekopieën taggen

Elke containerinstallatiekopie moet worden getagd met de loginServer-naam van het register. Deze tag wordt gebruikt voor routering bij het pushen van containerinstallatiekopieën naar een installatiekopieregister.

Als u een lijst met huidige installatiekopieën wilt weergeven, gebruikt u de opdracht [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Uitvoer:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Voer de volgende opdracht uit om de loginServer-naam op te halen:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Hiermee wordt een tabel opgehaald met de volgende resultaten. Dit resultaat wordt gebruikt om de installatiekopie **azure-vote-front** te taggen voordat u deze in de volgende stap naar het containerregister pusht.

```output
Result
------------------
<acrName>.azurecr.io
```

Tag nu de installatiekopie *azure-vote-front* met de loginServer van het containerregister. Voeg bovendien `:v1` toe aan het eind van de naam van de installatiekopie. Deze tag geeft de versie van de installatiekopie aan.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

Voer na het taggen de opdracht docker images uit om de bewerking te controleren.

Uitvoer:

```output
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Installatiekopieën naar het register pushen

Push de installatiekopie *azure-vote-front* naar het register. 

In het volgende voorbeeld vervangt u de naam van de ACR-loginServer door de naam van de loginServer uit uw omgeving.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

Het duurt enkele minuten voordat de opdracht docker push is voltooid.

## <a name="list-images-in-registry"></a>Installatiekopieën vermelden in het register

Gebruik de opdracht [az acr repository list](/cli/azure/acr/repository) om een lijst met installatiekopieën te retourneren die naar het Azure Container Registry zijn gepusht. Werk de opdracht bij met de naam van het ACR-exemplaar.

```azurecli
az acr repository list --name <acrName> --output table
```

Uitvoer:

```output
Result
----------------
azure-vote-front
```

Bij het voltooien van de zelfstudie is de containerinstallatiekopie opgeslagen in een persoonlijk Azure Container Registry-exemplaar. Deze installatiekopie wordt in volgende zelfstudies vanuit ACR geïmplementeerd naar een Service Fabric-cluster.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie is een toepassing opgehaald uit GitHub en zijn containerinstallatiekopieën gemaakt en naar een register gepusht. De volgende stappen zijn voltooid:

> [!div class="checklist"]
> * Een toepassingsbron klonen vanuit GitHub
> * Een containerinstallatiekopie maken uit de toepassingsbron
> * Een ACR-exemplaar (Azure Container Registry) implementeren
> * Een containerinstallatiekopie voor ACR taggen
> * De installatiekopie uploaden naar ACR

Ga naar de volgende zelfstudie voor informatie over het verpakken van containers in een Service Fabric-toepassing met behulp van Yeoman.

> [!div class="nextstepaction"]
> [Containers verpakken en implementeren als een Service Fabric-toepassing](service-fabric-tutorial-package-containers.md)
