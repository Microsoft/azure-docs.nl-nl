---
title: Externe verificatie van ACR-taak
description: Configureer een Azure Container Registry-taak (ACR-taak) om Docker Hub-referenties te lezen die zijn opgeslagen in een Azure-sleutelkluis met behulp van een beheerde identiteit voor Azure-resources.
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 16404f9244818d91c5333eb5eec5944bfdd9df98
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781195"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Externe verificatie in een ACR-taak met behulp van een door Azure beheerde identiteit 

In een [ACR-taak kunt](container-registry-tasks-overview.md)u [een beheerde identiteit inschakelen voor Azure-resources.](container-registry-tasks-authentication-managed-identity.md) De taak kan de identiteit gebruiken voor toegang tot andere Azure-resources, zonder referenties op te geven of te beheren. 

In dit artikel leert u hoe u een beheerde identiteit kunt inschakelen in een taak die toegang heeft tot geheimen die zijn opgeslagen in een Azure-sleutelkluis. 

Voor het maken van de Azure-resources moet u voor dit artikel versie 2.0.68 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="scenario-overview"></a>Overzicht van scenario's

De voorbeeldtaak leest Docker Hub referenties die zijn opgeslagen in een Azure-sleutelkluis. De referenties zijn voor een Docker Hub-account met schrijfmachtigingen (push) voor een privé Docker Hub opslagplaats. Als u de referenties wilt lezen, configureert u de taak met een beheerde identiteit en wijst u er de juiste machtigingen aan toe. De taak die aan de identiteit is gekoppeld, bouwt een afbeelding en meldt zich aan bij Docker Hub de afbeelding naar de privé-repo te pushen. 

In dit voorbeeld ziet u stappen met behulp van een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit. Uw identiteitskeuze is afhankelijk van de behoeften van uw organisatie.

In een echt scenario kan een bedrijf afbeeldingen publiceren naar een privé-Docker Hub als onderdeel van een bouwproces. 

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-containerregister nodig waarin u de taak kunt uitvoeren. In dit artikel heet dit register *myregistry.* Vervang in latere stappen door uw eigen registernaam.

Als u nog geen Azure-containerregister hebt, gaat u naar [Quickstart: Een privécontainerregister maken met behulp van de Azure CLI.](container-registry-get-started-azure-cli.md) U hoeft nog geen afbeeldingen naar het register te pushen.

U hebt ook een privéopslagplaats in Docker Hub en een Docker Hub-account met machtigingen om naar de opslagplaats te schrijven. In dit voorbeeld heet deze repo *hubuser/hubrepo.* 

## <a name="create-a-key-vault-and-store-secrets"></a>Een sleutelkluis maken en geheimen opslaan

Als dat nodig is, maakt u eerst een resourcegroep met de naam *myResourceGroup* op de locatie *eastus* met de [volgende az group create-opdracht:][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Gebruik de [opdracht az keyvault create om][az-keyvault-create] een sleutelkluis te maken. Zorg ervoor dat u een unieke naam voor de sleutelkluis opgeeft. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Sla de vereiste Docker Hub op in de sleutelkluis met behulp van de [opdracht az keyvault secret set.][az-keyvault-secret-set] In deze opdrachten worden de waarden doorgegeven in omgevingsvariabelen:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

In een echt scenario worden geheimen waarschijnlijk in een afzonderlijk proces ingesteld en onderhouden.

## <a name="define-task-steps-in-yaml-file"></a>Taakstappen definiëren in YAML-bestand

De stappen voor deze voorbeeldtaak worden gedefinieerd in een [YAML-bestand](container-registry-tasks-reference-yaml.md). Maak een bestand met de `dockerhubtask.yaml` naam in een lokale werkmap en plak de volgende inhoud. Zorg ervoor dat u de naam van de sleutelkluis in het bestand vervangt door de naam van uw sleutelkluis.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

De taakstappen doen het volgende:

* Geheime referenties beheren om te verifiëren met Docker Hub.
* Verifieer Docker Hub door de geheimen door te geven aan de `docker login` opdracht .
* Bouw een afbeelding met behulp van een Voorbeeld-Dockerfile in de [Azure-Samples/acr-tasks-repo.](https://github.com/Azure-Samples/acr-tasks.git)
* Push de afbeelding naar de Docker Hub privéopslagplaats.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Optie 1: een taak maken met een door de gebruiker toegewezen identiteit

Met de stappen in deze sectie maakt u een taak en maakt u een door de gebruiker toegewezen identiteit in. Als u in plaats daarvan een door het systeem toegewezen identiteit wilt inschakelen, zie optie [2:](#option-2-create-task-with-system-assigned-identity)taak maken met een door het systeem toegewezen identiteit. 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Taak maken

Maak de taak *dockerhubtask door* de volgende [az acr task create-opdracht uit te][az-acr-task-create] voeren. De taak wordt uitgevoerd zonder broncodecontext en de opdracht verwijst naar het bestand `dockerhubtask.yaml` in de werkmap. De `--assign-identity` parameter geeft de resource-id van de door de gebruiker toegewezen identiteit door. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>Identiteit toegang verlenen tot key vault

Voer de volgende [az keyvault set-policy-opdracht][az-keyvault-set-policy] uit om een toegangsbeleid in te stellen voor de sleutelkluis. In het volgende voorbeeld kan de identiteit geheimen lezen uit de sleutelkluis. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

Ga verder [met De taak handmatig uitvoeren.](#manually-run-the-task)

## <a name="option-2-create-task-with-system-assigned-identity"></a>Optie 2: Een taak maken met een door het systeem toegewezen identiteit

Met de stappen in deze sectie maakt u een taak en wordt een door het systeem toegewezen identiteit ingeschakeld. Als u in plaats daarvan een door de gebruiker toegewezen identiteit wilt inschakelen, zie optie [1:](#option-1-create-task-with-user-assigned-identity)taak maken met een door de gebruiker toegewezen identiteit. 

### <a name="create-task"></a>Taak maken

Maak de taak *dockerhubtask door* de volgende [az acr task create-opdracht uit te][az-acr-task-create] voeren. De taak wordt uitgevoerd zonder broncodecontext en de opdracht verwijst naar het bestand `dockerhubtask.yaml` in de werkmap. De `--assign-identity` parameter zonder waarde schakelt de door het systeem toegewezen identiteit voor de taak in.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>Identiteit toegang verlenen tot key vault

Voer de volgende [az keyvault set-policy-opdracht][az-keyvault-set-policy] uit om een toegangsbeleid in te stellen voor de sleutelkluis. In het volgende voorbeeld kan de identiteit geheimen lezen uit de sleutelkluis. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>De taak handmatig uitvoeren

Als u wilt controleren of de taak waarin u een beheerde identiteit hebt ingeschakeld, succesvol wordt uitgevoerd, activeert u de taak handmatig met de [opdracht az acr task run.][az-acr-task-run] De `--set` parameter wordt gebruikt om de naam van de privé-repo door te geven aan de taak. In dit voorbeeld is de naam van de tijdelijke aanduiding voor de repo *hubuser/hubrepo.*

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Wanneer de taak wordt uitgevoerd, toont de uitvoer geslaagde verificatie voor Docker Hub en is de afbeelding gemaakt en naar de privé-repo ge pusht:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Als u wilt controleren of de afbeelding is pushed, controleert u of de tag (in dit voorbeeld) in de persoonlijke `cf24` Docker Hub is.

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
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-group-create]: /cli/azure/group?#az_group_create
[az-keyvault-create]: /cli/azure/keyvault?#az_keyvault_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
