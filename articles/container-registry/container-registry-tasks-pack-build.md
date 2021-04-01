---
title: Installatie kopie bouwen met native Buildpack in de Cloud
description: Gebruik de opdracht AZ ACR Pack build om een container installatie kopie te bouwen vanuit een app en naar Azure Container Registry te pushen, zonder een Dockerfile te gebruiken.
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 264fc7314c78088ebfefb9ddb8edbe38fa16581a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92736633"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Een installatie kopie bouwen en pushen vanuit een app met behulp van een native Buildpack in de Cloud

De Azure CLI `az acr pack build` -opdracht maakt gebruik [`pack`](https://github.com/buildpack/pack) van het CLI-hulp programma, van [Buildpacks](https://buildpacks.io/), voor het bouwen van een app en het pushen van de installatie kopie naar een Azure container Registry. Deze functie biedt een optie om snel een container installatie kopie te bouwen op basis van de bron code van uw toepassing in Node.js, Java en andere talen, zonder dat u een Dockerfile hoeft te definiëren.

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de voor beelden in dit artikel uit te voeren. Als u het lokaal wilt gebruiken, is versie 2.0.70 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="use-the-build-command"></a>De opdracht build gebruiken

Voer de opdracht [AZ ACR Pack build][az-acr-pack-build] uit om een container installatie kopie te bouwen en te pushen met behulp van de native Buildpacks van de Cloud. Terwijl u met de opdracht [AZ ACR build][az-acr-build] een installatie kopie van een Dockerfile-bron en gerelateerde code bouwt en pusht, kunt `az acr pack build` u rechtstreeks een toepassings bron structuur opgeven.

Geef ten minste het volgende op wanneer u uitvoert `az acr pack build` :

* Een Azure container Registry waarin u de opdracht uitvoert
* Een afbeeldings naam en-label voor de resulterende afbeelding
* Een van de [ondersteunde context locaties](container-registry-tasks-overview.md#context-locations) voor ACR-taken, zoals een lokale map, een github opslag plaats of een externe tarball
* De naam van een Buildpack Builder-installatie kopie die geschikt is voor uw toepassing. Azure Container Registry de installatie kopieën van de opbouw functie opslaat, zoals `cloudfoundry/cnb:0.0.34-cflinuxfs3` voor snellere builds.  

`az acr pack build` ondersteunt andere functies van ACR-taken, zoals het [uitvoeren van variabelen](container-registry-tasks-reference-yaml.md#run-variables) en [taak uitvoer logboeken](container-registry-tasks-logs.md) die zijn gestreamd en ook worden opgeslagen voor later ophalen.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Voor beeld: Node.js installatie kopie bouwen met Cloud Foundry Builder

In het volgende voor beeld wordt een container installatie kopie samengesteld uit een Node.js-app in de [Azure-samples/nodejs-docs-Hello-World-](https://github.com/Azure-Samples/nodejs-docs-hello-world) opslag plaats met behulp van de `cloudfoundry/cnb:0.0.34-cflinuxfs3` opbouw functie. Deze opbouw functie wordt opgeslagen in de cache van Azure Container Registry. Daarom `--pull` is een para meter niet vereist:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

In dit voor beeld wordt de `node-app` installatie kopie samengesteld met het `1.0` label en gepusht naar het container register *myregistry* . In dit voor beeld wordt de naam van het doel register expliciet voor de naam van de installatie kopie geplaatst. Als u niets opgeeft, wordt de naam van de aanmeldings server van het REGI ster automatisch voor de naam van de installatie kopie geplaatst.

De opdracht uitvoer toont de voortgang van het bouwen en pushen van de installatie kopie. 

Nadat de installatie kopie is gemaakt, kunt u deze uitvoeren met docker, als u deze hebt geïnstalleerd. Meld u eerst aan bij uw REGI ster:

```azurecli
az acr login --name myregistry
```

Voer de installatie kopie uit:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Blader naar `localhost:1337` in uw favoriete browser om de voor beeld-web-app te bekijken. Druk `[Ctrl]+[C]` op om de container te stoppen.

## <a name="example-build-java-image-with-heroku-builder"></a>Voor beeld: een Java-installatie kopie bouwen met heroku Builder

In het volgende voor beeld wordt een container installatie kopie samengesteld uit de Java-app in de [buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) opslag plaats met behulp van de `heroku/buildpacks:18` Builder. De `--pull` para meter geeft aan dat de opdracht de nieuwste installatie kopie van de opbouw functie moet ophalen. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

In dit voor beeld wordt de `java-app` installatie kopie samengesteld met de run-id van de opdracht en gepusht naar het container register *myregistry* .

De opdracht uitvoer toont de voortgang van het bouwen en pushen van de installatie kopie. 

Nadat de installatie kopie is gemaakt, kunt u deze uitvoeren met docker, als u deze hebt geïnstalleerd. Meld u eerst aan bij uw REGI ster:

```azurecli
az acr login --name myregistry
```

Voer de installatie kopie uit en vervang uw afbeeldings code voor *runid*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Blader naar `localhost:8080` in uw favoriete browser om de voor beeld-web-app te bekijken. Druk `[Ctrl]+[C]` op om de container te stoppen.


## <a name="next-steps"></a>Volgende stappen

Nadat u een container installatie kopie hebt gemaakt en gepusht met `az acr pack build` , kunt u deze als een wille keurige installatie kopie implementeren naar een doel van uw keuze. Azure-implementatie opties zijn onder andere uitvoeren in [app service](../app-service/tutorial-custom-container.md) of [Azure Kubernetes service](../aks/tutorial-kubernetes-deploy-cluster.md), onder andere.

Zie [builds van container installatie kopieën automatiseren en onderhoud uitvoeren met ACR-taken](container-registry-tasks-overview.md)voor meer informatie over functies van ACR-taken.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
