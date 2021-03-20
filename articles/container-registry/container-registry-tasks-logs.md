---
title: Taak uitvoer logboeken weer geven-taken
description: Uitvoerings logboeken weer geven en beheren die door ACR-taken worden gegenereerd.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: b2a10d4a3a2746acf38445673af994c6317c77de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027173"
---
# <a name="view-and-manage-task-run-logs"></a>Taak uitvoer logboeken weer geven en beheren

Elke taak die in [Azure container Registry taken](container-registry-tasks-overview.md) wordt uitgevoerd, genereert logboek uitvoer die u kunt controleren om te bepalen of de taak stappen zijn uitgevoerd. 

In dit artikel wordt uitgelegd hoe u taak uitvoer Logboeken kunt weer geven en beheren.

## <a name="view-streamed-logs"></a>Gestreamde logboeken weer geven

Wanneer u een taak hand matig start, wordt de logboek uitvoer rechtstreeks naar de console gestreamd. Wanneer u bijvoorbeeld een taak hand matig start met behulp van de opdracht [AZ ACR build](/cli/azure/acr#az-acr-build), [AZ ACR run](/cli/azure/acr#az-acr-run)of [AZ ACR Task run](/cli/azure/acr/task#az-acr-task-run) , ziet u de logboek uitvoer die is gestreamd naar de-console. 

In het volgende voor beeld [AZ ACR run](/cli/azure/acr#az-acr-run) wordt hand matig een taak geactiveerd waarmee een container uit hetzelfde REGI ster wordt opgehaald:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Gestreamd logboek:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Opgeslagen logboeken weer geven 

Azure Container Registry Stores-uitvoerings logboeken voor alle taken. U kunt opgeslagen uitvoerings logboeken weer geven in de Azure Portal. U kunt ook de opdracht [AZ ACR taak logs](/cli/azure/acr/task#az-acr-task-logs) gebruiken om een geselecteerd logboek weer te geven. Standaard worden logboeken 30 dagen bewaard.

Als een taak automatisch wordt geactiveerd, bijvoorbeeld door het bijwerken van de bron code, is toegang tot de opgeslagen logboeken de *enige* manier om de uitvoerings logboeken weer te geven. Automatische taak triggers zijn onder andere door voeringen van de bron code of pull-aanvragen, updates van basis installatie kopieën en timer triggers.

Uitvoerings logboeken weer geven in de portal:

1. Navigeer naar het container register.
1. In **Services** selecteert u **taken** worden  >  **uitgevoerd**.
1. Selecteer een **Run-id** om de uitvoerings status te bekijken en Logboeken uit te voeren. Het logboek bevat dezelfde informatie als een gestreamd logboek als er een wordt gegenereerd.

![Aanmeldings portal voor taak uitvoering weer geven](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Als u een logboek wilt weer geven met behulp van de Azure CLI, voert u [AZ ACR-taak logboeken](/cli/azure/acr/task#az-acr-task-logs) uit en geeft u een run-id, een taak naam of een specifieke installatie kopie op die wordt gemaakt door een build-taak. Als er een taak naam wordt opgegeven, wordt in de opdracht het logboek voor de laatst gemaakte uitvoering weer gegeven.

In het volgende voor beeld wordt het logboek uitgevoerd voor de run with ID *cf4*:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternatieve logboek opslag

U kunt de logboeken voor het uitvoeren van taken opslaan op een lokaal bestands systeem of een andere archiverings oplossing gebruiken, zoals Azure Storage.

U kunt bijvoorbeeld een lokale *tasklogs* -Directory maken en de uitvoer van [AZ ACR-taak logboeken](/cli/azure/acr/task#az-acr-task-logs) omleiden naar een lokaal bestand:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

U kunt ook lokale logboek bestanden opslaan in Azure Storage. Gebruik bijvoorbeeld de [Azure cli](../storage/blobs/storage-quickstart-blobs-cli.md), de [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)of andere methoden om bestanden te uploaden naar een opslag account.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure container Registry taken](container-registry-tasks-overview.md)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
