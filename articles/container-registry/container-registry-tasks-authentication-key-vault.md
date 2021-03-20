---
title: Externe verificatie van ACR-taak
description: Configureer een Azure Container Registry taak (ACR taak) om docker hub-referenties te lezen die zijn opgeslagen in een Azure-sleutel kluis met behulp van een beheerde identiteit voor Azure-resources.
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 0bc43f958a14016146160a06372af0b36a9fff75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86058126"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Externe authenticatie in een ACR-taak met behulp van een door Azure beheerde identiteit 

In een [ACR-taak](container-registry-tasks-overview.md)kunt u [een beheerde identiteit inschakelen voor Azure-resources](container-registry-tasks-authentication-managed-identity.md). De taak kan de identiteit gebruiken om toegang te krijgen tot andere Azure-resources, zonder dat hiervoor referenties moeten worden verstrekt of beheerd. 

In dit artikel leert u hoe u een beheerde identiteit kunt inschakelen in een taak die is opgeslagen in een Azure-sleutel kluis. 

Voor het maken van de Azure-resources moet u voor dit artikel de Azure CLI-versie 2.0.68 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="scenario-overview"></a>Overzicht van scenario's

Met de voorbeeld taak worden docker hub-referenties gelezen die zijn opgeslagen in een Azure-sleutel kluis. De referenties zijn voor een docker hub-account met schrijf machtigingen (push) naar een privé-docker hub-opslag plaats. Als u de referenties wilt lezen, configureert u de taak met een beheerde identiteit en wijst u de juiste machtigingen toe. De taak die aan de identiteit is gekoppeld, bouwt een installatie kopie en meldt zich aan bij docker hub om de installatie kopie naar de persoonlijke opslag plaats te pushen. 

In dit voor beeld ziet u stappen met behulp van een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit. De identiteit die u kiest, is afhankelijk van de behoeften van uw organisatie.

In een praktijk scenario kan een bedrijf installatie kopieën publiceren naar een persoonlijke opslag plaats in docker hub als onderdeel van een bouw proces. 

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-container register nodig waarin u de taak uitvoert. In dit artikel heeft dit REGI ster de naam *myregistry*. Vervang door uw eigen register naam in latere stappen.

Als u nog geen Azure container Registry hebt, raadpleegt u [Quick Start: een persoonlijk container register maken met behulp van de Azure cli](container-registry-get-started-azure-cli.md). U hoeft nog geen installatie kopieën naar het REGI ster te pushen.

U hebt ook een privé-opslag plaats in docker hub en een docker hub-account met machtigingen voor het schrijven naar de opslag plaats. In dit voor beeld is deze opslag plaats de naam *hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Een sleutel kluis maken en geheimen opslaan

Eerst moet u, indien nodig, een resource groep met de naam *myResourceGroup* maken op de locatie *eastus* met de volgende opdracht [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Gebruik de opdracht [AZ Key kluis Create][az-keyvault-create] om een sleutel kluis te maken. Zorg ervoor dat u een unieke naam voor de sleutel kluis opgeeft. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Sla de vereiste docker hub-referenties op in de sleutel kluis met behulp van de opdracht [AZ Key kluis Secret set][az-keyvault-secret-set] . In deze opdrachten worden de waarden door gegeven in omgevings variabelen:

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

In een praktijk scenario zouden geheimen waarschijnlijk in een afzonderlijk proces worden ingesteld en onderhouden.

## <a name="define-task-steps-in-yaml-file"></a>Taak stappen definiëren in het YAML-bestand

De stappen voor deze voorbeeld taak worden gedefinieerd in een [yaml-bestand](container-registry-tasks-reference-yaml.md). Maak een bestand `dockerhubtask.yaml` met de naam in een lokale werkmap en plak de volgende inhoud. Zorg ervoor dat u de naam van de sleutel kluis in het bestand vervangt door de naam van de sleutel kluis.

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

Met de taak stappen gaat u als volgt te werk:

* Geheime referenties beheren voor verificatie met docker hub.
* Verifieer met docker hub door de geheimen door te geven aan de `docker login` opdracht.
* Bouw een installatie kopie met behulp van een voor beeld-Dockerfile in de opslag plaats van [Azure-samples/ACR-tasks](https://github.com/Azure-Samples/acr-tasks.git) .
* Push de installatie kopie naar de opslag plaats van de privé-docker-hub.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Optie 1: taak maken met door gebruiker toegewezen identiteit

Met de stappen in deze sectie maakt u een taak en schakelt u een door de gebruiker toegewezen identiteit in. Als u in plaats daarvan een door het systeem toegewezen identiteit wilt inschakelen, raadpleegt u [optie 2: taak maken met door het systeem toegewezen identiteit](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Taak maken

Maak de taak *dockerhubtask* door de volgende [AZ ACR Task Create][az-acr-task-create] -opdracht uit te voeren. De taak wordt uitgevoerd zonder een bron code context en de opdracht verwijst naar het bestand `dockerhubtask.yaml` in de werkmap. De `--assign-identity` para meter geeft de resource-id van de door de gebruiker toegewezen identiteit door. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>Identiteits toegang verlenen aan de sleutel kluis

Voer de volgende [AZ-set-Policy][az-keyvault-set-policy] opdracht uit om een toegangs beleid in te stellen op de sleutel kluis. In het volgende voor beeld kan de identiteit geheimen van de sleutel kluis lezen. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

[Voer de taak hand matig uit](#manually-run-the-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Optie 2: een taak maken met een door het systeem toegewezen identiteit

Met de stappen in deze sectie maakt u een taak en schakelt u een door het systeem toegewezen identiteit in. Als u in plaats daarvan een door de gebruiker toegewezen identiteit wilt inschakelen, raadpleegt u [optie 1: taak maken met door de gebruiker toegewezen identiteit](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Taak maken

Maak de taak *dockerhubtask* door de volgende [AZ ACR Task Create][az-acr-task-create] -opdracht uit te voeren. De taak wordt uitgevoerd zonder een bron code context en de opdracht verwijst naar het bestand `dockerhubtask.yaml` in de werkmap. `--assign-identity`Met de para meter zonder waarde kan de door het systeem toegewezen identiteit voor de taak worden ingeschakeld.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>Identiteits toegang verlenen aan de sleutel kluis

Voer de volgende [AZ-set-Policy][az-keyvault-set-policy] opdracht uit om een toegangs beleid in te stellen op de sleutel kluis. In het volgende voor beeld kan de identiteit geheimen van de sleutel kluis lezen. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>De taak handmatig uitvoeren

Als u wilt controleren of de taak waarin u een beheerde identiteit hebt ingeschakeld, met succes wordt uitgevoerd, moet u de taak hand matig activeren met de opdracht [AZ ACR Task run][az-acr-task-run] . De `--set` para meter wordt gebruikt om de naam van de persoonlijke opslag plaats door te geven aan de taak. In dit voor beeld is de naam van de tijdelijke aanduiding opslag plaats *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Wanneer de taak met succes wordt uitgevoerd, toont de uitvoer geslaagde verificatie voor docker hub en de installatie kopie is gemaakt en gepusht naar de persoonlijke opslag plaats:

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

Als u wilt bevestigen dat de installatie kopie is gepusht, controleert u op de tag ( `cf24` in dit voor beeld) in de privé docker hub opslag plaats.

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
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
