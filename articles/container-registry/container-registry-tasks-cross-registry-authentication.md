---
title: Verificatie tussen registers vanuit een ACR-taak
description: Een ACR-Azure Container Registry configureren voor toegang tot een ander privé-Azure-containerregister met behulp van een beheerde identiteit voor Azure-resources
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: a9b70a44de0cfccb9a61bc24575281e440db6e32
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781113"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Verificatie tussen registers in een ACR-taak met behulp van een door Azure beheerde identiteit 

In een [ACR-taak kunt](container-registry-tasks-overview.md)u [een beheerde identiteit inschakelen voor Azure-resources.](container-registry-tasks-authentication-managed-identity.md) De taak kan de identiteit gebruiken voor toegang tot andere Azure-resources, zonder referenties op te geven of te beheren. 

In dit artikel leert u hoe u een beheerde identiteit in een taak in staat stelt een andere afbeelding op te halen uit een register dan de identiteit die wordt gebruikt om de taak uit te voeren.

Voor het maken van de Azure-resources moet u voor dit artikel versie 2.0.68 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="scenario-overview"></a>Overzicht van scenario's

Met de voorbeeldtaak wordt een basisafbeelding uit een ander Azure-containerregister haalt om een toepassingsafbeelding te bouwen en te pushen. Als u de basisafbeelding wilt op halen, configureert u de taak met een beheerde identiteit en wijst u er de juiste machtigingen aan toe. 

In dit voorbeeld ziet u stappen met behulp van een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit. Uw identiteitskeuze is afhankelijk van de behoeften van uw organisatie.

In een echt scenario kan een organisatie een set basisafbeeldingen onderhouden die door alle ontwikkelteams worden gebruikt om hun toepassingen te bouwen. Deze basisafbeeldingen worden opgeslagen in een bedrijfsregister, en elk ontwikkelteam heeft alleen pull-rechten. 

## <a name="prerequisites"></a>Vereisten

Voor dit artikel hebt u twee Azure-containerregisters nodig:

* U gebruikt het eerste register om ACR-taken te maken en uit te voeren. In dit artikel heet dit register *myregistry.* 
* Het tweede register host een basisafbeelding die wordt gebruikt voor de taak om een -afbeelding te bouwen. In dit artikel heet het tweede register *mybaseregistry.* 

Vervang in latere stappen door uw eigen registernamen.

Als u nog niet de benodigde Azure-containerregisters hebt, gaat u naar [Quickstart: Een privécontainerregister maken met behulp van de Azure CLI.](container-registry-get-started-azure-cli.md) U hoeft nog geen afbeeldingen naar het register te pushen.

## <a name="prepare-base-registry"></a>Basisregister voorbereiden

Voor demonstratiedoeleinden kunt u [az acr import][az-acr-import] uitvoeren om een openbare Node.js-Docker Hub te importeren in uw basisregister. In de praktijk kan een ander team of proces in de organisatie afbeeldingen in het basisregister onderhouden.

```azurecli
az acr import --name mybaseregistry \
  --source docker.io/library/node:15-alpine \
  --image baseimages/node:15-alpine 
```

## <a name="define-task-steps-in-yaml-file"></a>Taakstappen definiëren in YAML-bestand

De stappen voor deze [voorbeeldtaak met meerdere stappen](container-registry-tasks-multi-step.md) worden gedefinieerd in een [YAML-bestand](container-registry-tasks-reference-yaml.md). Maak een bestand met de `helloworldtask.yaml` naam in uw lokale werkmap en plak de volgende inhoud. Werk de waarde van `REGISTRY_NAME` in de build-stap bij met de servernaam van uw basisregister.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git#main -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

De build-stap maakt gebruik van het bestand in de `Dockerfile-app` [azure-samples/acr-build-helloworld-node-repo](https://github.com/Azure-Samples/acr-build-helloworld-node.git) om een afbeelding te bouwen. De `--build-arg` verwijst naar het basisregister om de basisafbeelding op te halen. Wanneer de taak is gemaakt, wordt de -afbeelding naar het register pushen dat wordt gebruikt om de taak uit te voeren.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Optie 1: een taak maken met een door de gebruiker toegewezen identiteit

Met de stappen in deze sectie maakt u een taak en maakt u een door de gebruiker toegewezen identiteit in. Als u in plaats daarvan een door het systeem toegewezen identiteit wilt inschakelen, zie optie [2:](#option-2-create-task-with-system-assigned-identity)taak maken met een door het systeem toegewezen identiteit. 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Taak maken

Maak de taak *helloworldtask door* de volgende [az acr task create-opdracht uit te][az-acr-task-create] voeren. De taak wordt uitgevoerd zonder broncodecontext en de opdracht verwijst naar het bestand `helloworldtask.yaml` in de werkmap. De `--assign-identity` parameter geeft de resource-id van de door de gebruiker toegewezen identiteit door. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Identiteit pull-machtigingen geven voor het basisregister

In deze sectie geeft u de beheerde identiteit machtigingen om op te halen uit het basisregister, *mybaseregistry.*

Gebruik de [opdracht az acr show][az-acr-show] om de resource-id van het basisregister op te halen en op te slaan in een variabele:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Gebruik de [opdracht az role assignment create][az-role-assignment-create] om de identiteit van de rol toe te wijzen aan het `acrpull` basisregister. Deze rol heeft alleen machtigingen om afbeeldingen uit het register op te halen.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

Ga verder met [Doelregisterreferenties toevoegen aan taak](#add-target-registry-credentials-to-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Optie 2: een taak maken met een door het systeem toegewezen identiteit

Met de stappen in deze sectie maakt u een taak en maakt u een door het systeem toegewezen identiteit in. Als u in plaats daarvan een door de gebruiker toegewezen identiteit wilt inschakelen, zie optie [1:](#option-1-create-task-with-user-assigned-identity)taak maken met door de gebruiker toegewezen identiteit. 

### <a name="create-task"></a>Taak maken

Maak de taak *helloworldtask door* de volgende [az acr task create-opdracht uit te][az-acr-task-create] voeren. De taak wordt uitgevoerd zonder broncodecontext en de opdracht verwijst naar het bestand `helloworldtask.yaml` in de working directory. De `--assign-identity` parameter zonder waarde schakelt de door het systeem toegewezen identiteit voor de taak in. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Identiteit pull-machtigingen geven voor het basisregister

In deze sectie geeft u de beheerde identiteit machtigingen om op te halen uit het basisregister, *mybaseregistry.*

Gebruik de [opdracht az acr show][az-acr-show] om de resource-id van het basisregister op te halen en op te slaan in een variabele:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Gebruik de [opdracht az role assignment create][az-role-assignment-create] om de identiteit van de rol toe te wijzen aan het `acrpull` basisregister. Deze rol heeft alleen machtigingen om afbeeldingen uit het register op te halen.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Doelregisterreferenties toevoegen aan taak

Gebruik nu de [opdracht az acr task credential add][az-acr-task-credential-add] om de taak te verifiëren bij het basisregister met behulp van de referenties van de identiteit. Voer de opdracht uit die overeenkomt met het type beheerde identiteit dat u in de taak hebt ingeschakeld. Als u een door de gebruiker toegewezen identiteit hebt ingeschakeld, moet u de `--use-identity` client-id van de identiteit doorgeven. Als u een door het systeem toegewezen identiteit hebt ingeschakeld, geef dan `--use-identity [system]` door.

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

Als u wilt controleren of de taak waarin u een beheerde identiteit hebt ingeschakeld, wordt uitgevoerd, activeert u de taak handmatig met de [opdracht az acr task run.][az-acr-task-run] 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Als de taak wordt uitgevoerd, is de uitvoer vergelijkbaar met:

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

Voer de [opdracht az acr repository show-tags][az-acr-repository-show-tags] uit om te controleren of de gemaakt en naar *myregistry is pusht:*

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Voorbeelduitvoer:

```console
cf10
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [inschakelen van een beheerde identiteit in een ACR-taak](container-registry-tasks-authentication-managed-identity.md).
* Zie de [ACR-taken YAML](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-group-create]: /cli/azure/group?#az_group_create
