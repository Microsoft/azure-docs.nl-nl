---
title: Build-afbeelding met Cloud Native Buildpack
description: Gebruik de opdracht az acr pack build om een container-image te bouwen vanuit een app en push naar Azure Container Registry, zonder een Dockerfile te gebruiken.
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: 1700c8fda8ac91e7d447d35c0989da2d5fc3aefe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780925"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Een afbeelding van een app bouwen en pushen met behulp van een Cloud Native Buildpack

De Azure CLI-opdracht maakt gebruik van het CLI-hulpprogramma, van Buildpacks, om een app te bouwen en de afbeelding naar een `az acr pack build` [`pack`](https://github.com/buildpack/pack) Azure-containerregister te pushen. [](https://buildpacks.io/) Deze functie biedt een optie om snel een containerafbeelding te bouwen op basis van de broncode van uw toepassing in Node.js, Java en andere talen zonder dat u een Dockerfile hoeft te definiëren.

U kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de voorbeelden in dit artikel uit te voeren. Als u deze lokaal wilt gebruiken, is versie 2.0.70 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="use-the-build-command"></a>Gebruik de build-opdracht

Voer de opdracht [az acr pack build][az-acr-pack-build] uit om een containerafbeelding te bouwen en te pushen met behulp van Cloud Native Buildpacks. Terwijl de [opdracht az acr build][az-acr-build] een afbeelding van een Dockerfile-bron en gerelateerde code bouwt en pusht, geeft u met u rechtstreeks een bronstructuur van de toepassing `az acr pack build` op.

Geef ten minste het volgende op wanneer u uit te `az acr pack build` voeren:

* Een Azure-containerregister waarin u de opdracht kunt uitvoeren
* Een afbeeldingsnaam en -tag voor de resulterende afbeelding
* Een van de [ondersteunde contextlocaties](container-registry-tasks-overview.md#context-locations) voor ACR-taken, zoals een lokale map, een GitHub-opslagplaats of een externe tarball
* De naam van een Buildpack Builder-afbeelding die geschikt is voor uw toepassing. Azure Container Registry caches builder-afbeeldingen zoals `cloudfoundry/cnb:0.0.34-cflinuxfs3` voor snellere builds.  

`az acr pack build`ondersteunt andere functies van ACR-taken opdrachten, [](container-registry-tasks-logs.md) waaronder het uitvoeren van variabelen en logboeken voor [taakruns](container-registry-tasks-reference-yaml.md#run-variables) die worden gestreamd en opgeslagen voor later ophalen.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Voorbeeld: Een Node.js maken met Cloud Foundry Builder

In het volgende voorbeeld wordt een containerafbeelding gemaakt van een Node.js-app in de [Azure-Samples/nodejs-docs-hello-world-repo,](https://github.com/Azure-Samples/nodejs-docs-hello-world) met behulp van de `cloudfoundry/cnb:0.0.34-cflinuxfs3` opbouwer. Deze builder wordt opgeslagen in Azure Container Registry cache, dus een `--pull` parameter is niet vereist:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

In dit voorbeeld wordt de `node-app` -afbeelding gemaakt met `1.0` de tag en naar het containerregister *myregistry* pusht. In dit voorbeeld wordt de naam van het doelregister expliciet toegevoegd aan de naam van de afbeelding. Als dit niet wordt opgegeven, wordt de naam van de aanmeldingsserver voor het register automatisch toegevoegd aan de naam van de afbeelding.

Opdrachtuitvoer toont de voortgang van het bouwen en pushen van de afbeelding. 

Nadat de installatie afbeelding is gemaakt, kunt u deze uitvoeren met Docker, als u deze hebt geïnstalleerd. Meld u eerst aan bij het register:

```azurecli
az acr login --name myregistry
```

Voer de afbeelding uit:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Blader naar `localhost:1337` in uw favoriete browser om de voorbeeldweb-app te bekijken. Druk op `[Ctrl]+[C]` om de container te stoppen.

## <a name="example-build-java-image-with-heroku-builder"></a>Voorbeeld: Een Java-afbeelding bouwen met Heroku Builder

In het volgende voorbeeld wordt een containerafbeelding gemaakt van de Java-app in de [buildpack/sample-java-app-repo,](https://github.com/buildpack/sample-java-app) met behulp van de `heroku/buildpacks:18` opbouwer. De `--pull` parameter geeft aan dat de opdracht de meest recente builder-afbeelding moet halen. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

In dit voorbeeld wordt de afbeelding gebouwd die is getagd met de run-id van de opdracht en wordt deze naar het `java-app` *containerregister myregistry* pusht.

Opdrachtuitvoer toont de voortgang van het bouwen en pushen van de afbeelding. 

Nadat de installatie afbeelding is gemaakt, kunt u deze uitvoeren met Docker, als u deze hebt geïnstalleerd. Meld u eerst aan bij het register:

```azurecli
az acr login --name myregistry
```

Voer de afbeelding uit, vervang uw afbeeldingstag door *runid*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Blader naar `localhost:8080` in uw favoriete browser om de voorbeeldweb-app te bekijken. Druk op `[Ctrl]+[C]` om de container te stoppen.


## <a name="next-steps"></a>Volgende stappen

Nadat u een containerafbeelding hebt gemaakt en pusht met , kunt u deze implementeren zoals elke andere `az acr pack build` afbeelding naar een doel van uw keuze. Azure-implementatieopties omvatten het uitvoeren ervan in [App Service](../app-service/tutorial-custom-container.md) of [Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md), onder andere.

Zie [Automate container image builds and maintenance with](container-registry-tasks-overview.md)ACR-taken (Builds en onderhoud van containerafbeeldingen automatiseren met ACR-taken) voor meer informatie over ACR-taken.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
