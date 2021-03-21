---
title: Verificatie tussen het REGI ster van de ACR-taak
description: Een Azure Container Registry taak (ACR Task) configureren om toegang te krijgen tot een ander persoonlijk Azure container Registry met behulp van een beheerde identiteit voor Azure-resources
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 789d2c141f8b7c3f2eb8daa31d99090e3d028a43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98915825"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Verificatie tussen het REGI ster in een ACR-taak met behulp van een door Azure beheerde identiteit 

In een [ACR-taak](container-registry-tasks-overview.md)kunt u [een beheerde identiteit inschakelen voor Azure-resources](container-registry-tasks-authentication-managed-identity.md). De taak kan de identiteit gebruiken om toegang te krijgen tot andere Azure-resources, zonder dat hiervoor referenties moeten worden verstrekt of beheerd. 

In dit artikel leert u hoe u een beheerde identiteit in een taak kunt inschakelen voor het ophalen van een installatie kopie vanuit een ander REGI ster dan die waarmee de taak wordt uitgevoerd.

Voor het maken van de Azure-resources moet u voor dit artikel de Azure CLI-versie 2.0.68 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="scenario-overview"></a>Overzicht van scenario's

De voorbeeld taak haalt een basis installatie kopie op uit een ander Azure container Registry om een toepassings installatie kopie te bouwen en te pushen. Als u de basis installatie kopie wilt ophalen, configureert u de taak met een beheerde identiteit en wijst u de juiste machtigingen toe. 

In dit voor beeld ziet u stappen met behulp van een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit. De identiteit die u kiest, is afhankelijk van de behoeften van uw organisatie.

In een praktijk scenario kan een organisatie een set basis installatie kopieën onderhouden die door alle ontwikkel teams wordt gebruikt om hun toepassingen te bouwen. Deze basis installatie kopieën worden opgeslagen in een bedrijfs register, waarbij elk ontwikkel team alleen pull-rechten heeft. 

## <a name="prerequisites"></a>Vereisten

Voor dit artikel hebt u twee Azure-container registers nodig:

* U gebruikt het eerste REGI ster om ACR-taken te maken en uit te voeren. In dit artikel heeft dit REGI ster de naam *myregistry*. 
* Het tweede REGI ster fungeert als host voor een basis installatie kopie die wordt gebruikt voor de taak om een installatie kopie te maken. In dit artikel heeft het tweede REGI ster de naam *mybaseregistry*. 

Vervang door uw eigen register namen in latere stappen.

Als u nog niet beschikt over de benodigde Azure-container registers, raadpleegt u [Quick Start: een persoonlijk container register maken met behulp van de Azure cli](container-registry-get-started-azure-cli.md). U hoeft nog geen installatie kopieën naar het REGI ster te pushen.

## <a name="prepare-base-registry"></a>Basis register voorbereiden

Voor demonstratie doeleinden voert u als eenmalige bewerking [AZ ACR import] [AZ-ACR-import] uit om een open bare Node.js-installatie kopie van docker hub te importeren in het basis register. In de praktijk kan een ander team of proces in de organisatie installatie kopieën behouden in het basis register.

```azurecli
az acr import --name mybaseregistry \
  --source docker.io/library/node:15-alpine \
  --image baseimages/node:15-alpine 
```

## <a name="define-task-steps-in-yaml-file"></a>Taak stappen definiëren in het YAML-bestand

De stappen voor dit voor beeld van een [taken met meerdere stappen](container-registry-tasks-multi-step.md) worden gedefinieerd in een [yaml-bestand](container-registry-tasks-reference-yaml.md). Maak een bestand `helloworldtask.yaml` met de naam in uw lokale werkmap en plak de volgende inhoud. Werk de waarde van `REGISTRY_NAME` in de build-stap bij met de server naam van het basis register.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git#main -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

In de stap build wordt het `Dockerfile-app` bestand gebruikt in [Azure-samples/ACR-build-HelloWorld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) opslag plaats om een installatie kopie te bouwen. Hiermee wordt `--build-arg` verwezen naar het basis register voor het ophalen van de basis installatie kopie. Wanneer de installatie kopie is gemaakt, wordt deze naar het REGI ster gepusht om de taak uit te voeren.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Optie 1: taak maken met door gebruiker toegewezen identiteit

Met de stappen in deze sectie maakt u een taak en schakelt u een door de gebruiker toegewezen identiteit in. Als u in plaats daarvan een door het systeem toegewezen identiteit wilt inschakelen, raadpleegt u [optie 2: taak maken met door het systeem toegewezen identiteit](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Taak maken

Maak de taak *helloworldtask* door de volgende [AZ ACR Task Create][az-acr-task-create] -opdracht uit te voeren. De taak wordt uitgevoerd zonder een bron code context en de opdracht verwijst naar het bestand `helloworldtask.yaml` in de werkmap. De `--assign-identity` para meter geeft de resource-id van de door de gebruiker toegewezen identiteit door. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Machtigingen voor het verzamelen van identiteiten aan het basis register geven

In deze sectie geeft u de beheerde identiteits machtigingen die moeten worden opgehaald uit het basis register, *mybaseregistry*.

Gebruik de opdracht [AZ ACR show][az-acr-show] om de resource-id van het basis register op te halen en op te slaan in een variabele:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Gebruik de opdracht [AZ Role Assignment Create][az-role-assignment-create] om de rol-id toe `acrpull` te wijzen aan het basis register. Deze rol heeft alleen machtigingen voor het ophalen van installatie kopieën uit het REGI ster.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

Ga verder met het toevoegen van de [doel register referenties aan de taak](#add-target-registry-credentials-to-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Optie 2: een taak maken met een door het systeem toegewezen identiteit

Met de stappen in deze sectie maakt u een taak en schakelt u een door het systeem toegewezen identiteit in. Als u in plaats daarvan een door de gebruiker toegewezen identiteit wilt inschakelen, raadpleegt u [optie 1: taak maken met door de gebruiker toegewezen identiteit](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Taak maken

Maak de taak *helloworldtask* door de volgende [AZ ACR Task Create][az-acr-task-create] -opdracht uit te voeren. De taak wordt uitgevoerd zonder een bron code context en de opdracht verwijst naar het bestand `helloworldtask.yaml` in de werkmap. `--assign-identity`Met de para meter zonder waarde kan de door het systeem toegewezen identiteit voor de taak worden ingeschakeld. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Machtigingen voor het verzamelen van identiteiten aan het basis register geven

In deze sectie geeft u de beheerde identiteits machtigingen die moeten worden opgehaald uit het basis register, *mybaseregistry*.

Gebruik de opdracht [AZ ACR show][az-acr-show] om de resource-id van het basis register op te halen en op te slaan in een variabele:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Gebruik de opdracht [AZ Role Assignment Create][az-role-assignment-create] om de rol-id toe `acrpull` te wijzen aan het basis register. Deze rol heeft alleen machtigingen voor het ophalen van installatie kopieën uit het REGI ster.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Doel register referenties toevoegen aan taak

Gebruik nu de opdracht [AZ ACR taak Credential add][az-acr-task-credential-add] om de taak in te scha kelen met het basis register met de referenties van de identiteit. Voer de opdracht uit die overeenkomt met het type beheerde identiteit dat u hebt ingeschakeld in de taak. Als u een door de gebruiker toegewezen identiteit hebt ingeschakeld, geeft u `--use-identity` de client-id van de identiteit door. Als u een door het systeem toegewezen identiteit hebt ingeschakeld, geeft u door `--use-identity [system]` .

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>De taak handmatig uitvoeren

Als u wilt controleren of de taak waarin u een beheerde identiteit hebt ingeschakeld, met succes wordt uitgevoerd, moet u de taak hand matig activeren met de opdracht [AZ ACR Task run][az-acr-task-run] . 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Als de taak met succes wordt uitgevoerd, is de uitvoer vergelijkbaar met:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
15-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 15-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Voer de opdracht [AZ ACR repository show-Tags][az-acr-repository-show-tags] uit om te controleren of de installatie kopie is gebouwd en is gepusht naar *myregistry*:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Voorbeelduitvoer:

```console
cf10
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het inschakelen van een beheerde identiteit in een ACR-taak](container-registry-tasks-authentication-managed-identity.md).
* Zie de [ACR-taken yaml-Naslag informatie](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
