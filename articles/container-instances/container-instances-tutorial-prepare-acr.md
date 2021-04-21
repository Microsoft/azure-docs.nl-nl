---
title: 'Zelfstudie: containerregister voorbereiden om installatiekopie te implementeren'
description: Zelfstudie voor Azure Container Instances, deel 2 van 3 - Azure-containerregister voorbereiden
ms.topic: tutorial
ms.date: 12/18/2019
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 36c2e6cda728a85ccab080bbbb6f1a0b74824f2d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786977"
---
# <a name="tutorial-create-an-azure-container-registry-and-push-a-container-image"></a>Zelfstudie: een Azure-containerregister maken en een containerinstallatiekopie pushen

Dit is deel 2 van een zelfstudie met drie delen. In [deel één](container-instances-tutorial-prepare-app.md) van de zelfstudie hebt u een Docker-containerinstallatiekopie gemaakt voor een Node.js-webtoepassing. In deze zelfstudie pusht u de installatiekopie naar Azure Container Registry. Als u nog geen containerinstallatiekopie hebt gemaakt, gaat u terug naar [Zelfstudie 1: Containerinstallatiekopie maken](container-instances-tutorial-prepare-app.md).

Azure Container Registry is uw privéregister van Docker in Azure. In deze zelfstudie, het tweede deel van de serie, leert u het volgende:

> [!div class="checklist"]
> * Een Azure Container Registry-exemplaar maken met de Azure CLI
> * Een containerinstallatiekopie taggen voor het Azure Container Registry
> * De installatiekopie uploaden naar het register

In het volgende artikel, de laatste in de reeks, implementeert u de container uit uw privéregister naar Azure Container Instances.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="tag-container-image"></a>Containerinstallatiekopieën taggen

Als u een containerinstallatiekopie naar een privéregister zoals Azure Container Registry wilt pushen, moet u de installatiekopie eerst labelen met de volledige naam van de aanmeldingsserver van het register.

Haal eerst de volledige naam van de aanmeldingsserver op voor uw Azure Container Registry. Voer de opdracht [az acr show][az-acr-show] uit en vervang `<acrName>` door de naam van het register dat u zojuist hebt gemaakt:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Bijvoorbeeld, als de naam van het register *mycontainerregistry082* is:

```azurecli
az acr show --name mycontainerregistry082 --query loginServer --output table
```

```output
Result
------------------------
mycontainerregistry082.azurecr.io
```

Geef nu de lijst met lokale installatiekopieën weer met behulp van de opdracht [docker images][docker-images]:

```bash
docker images
```

Samen met eventuele andere installatiekopieën op uw computer ziet u nu de installatiekopie *aci-tutorial-app* die u hebt gebouwd in de [vorige zelfstudie](container-instances-tutorial-prepare-app.md):

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

Label de installatiekopie *aci-tutorial-app* met de aanmeldingsserver van de containerregister. Voeg ook het label `:v1` toe aan het eind van de installatiekopienaam om het versienummer van de installatiekopie aan te geven. Vervang `<acrLoginServer>` door het resultaat van de opdracht [az acr show][az-acr-show] die u eerder hebt uitgevoerd.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Voer `docker images` opnieuw uit om het labelen te controleren:

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Installatiekopie pushen naar Azure Container Registry

Nu u de installatiekopie *aci-tutorial-app* hebt gelabeld met de volledige naam van de aanmeldingsserver voor uw privéregister, kunt u installatiekopie naar het register pushen met de opdracht [docker push][docker-push]. Vervang `<acrLoginServer>` door de volledige naam van de aanmeldingsserver die u in de vorige stap hebt verkregen.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

De bewerking `push` duurt minimaal enkele seconden tot maximaal enkele minuten afhankelijk van de internetverbinding, en de uitvoer ziet er ongeveer als volgt uit:

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Installatiekopieën vermelden in Azure Container Registry

Als u wilt controleren of de installatiekopie die u zojuist hebt gepusht, zich inderdaad in uw Azure Container Registry bevindt, geeft u de installatiekopieën in uw register weer met de opdracht [az acr repository list][az-acr-repository-list]. Vervang `<acrName>` door de naam van het containerregister.

```azurecli
az acr repository list --name <acrName> --output table
```

Bijvoorbeeld:

```azurecli
az acr repository list --name mycontainerregistry082 --output table
```

```output
Result
----------------
aci-tutorial-app
```

Gebruik de opdracht [az acr repository show-tags][az-acr-repository-show-tags] om de *labels* voor een specifieke installatiekopie te zien.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

De uitvoer ziet er als volgt uit:

```console
az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure Container Registry voorbereid voor gebruik met Azure Container Instances en hebt u een containerinstallatiekopie naar het register gepusht. De volgende stappen zijn voltooid:

> [!div class="checklist"]
> * Er is een Azure Container Registry-exemplaar gemaakt met de Azure CLI
> * Er is een containerinstallatiekopie getagd voor Azure Container Registry
> * Er is een installatiekopie naar Azure Container Registry geüpload

Ga verder met de volgende zelfstudie voor meer informatie over het implementeren van de container in Azure met behulp van Azure Container Instances:

> [!div class="nextstepaction"]
> [Container implementeren in Azure Container Instances](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
