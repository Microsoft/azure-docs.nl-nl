---
title: Taakrunlogboeken weergeven - Taken
description: Runlogboeken weergeven en beheren die zijn gegenereerd door ACR-taken.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: ce5f33853be2aa48bcfd1916c7f8b94b9702f38c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781051"
---
# <a name="view-and-manage-task-run-logs"></a>Logboeken voor taakruns weergeven en beheren

Elke taak die wordt uitgevoerd in [Azure Container Registry genereert](container-registry-tasks-overview.md) logboekuitvoer die u kunt inspecteren om te bepalen of de taakstappen zijn uitgevoerd. 

In dit artikel wordt uitgelegd hoe u logboeken voor taakruns kunt weergeven en beheren.

## <a name="view-streamed-logs"></a>Gestreamde logboeken weergeven

Wanneer u een taak handmatig activeert, wordt logboekuitvoer rechtstreeks naar de console gestreamd. Wanneer u een taak bijvoorbeeld handmatig activeert met de opdracht [az acr build](/cli/azure/acr#az_acr_build), [az acr run](/cli/azure/acr#az_acr_run)of az [acr task run,](/cli/azure/acr/task#az_acr_task_run) ziet u dat logboekuitvoer naar de console wordt gestreamd. 

Met het volgende voorbeeld [van de opdracht az acr run](/cli/azure/acr#az_acr_run) wordt handmatig een taak triggers uitgevoerd die een container uitvoeren die uit hetzelfde register is gehaald:

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

## <a name="view-stored-logs"></a>Opgeslagen logboeken weergeven 

Azure Container Registry slaat runlogboeken voor alle taken op. U kunt opgeslagen runlogboeken weergeven in de Azure Portal. Of gebruik de opdracht [az acr task logs om](/cli/azure/acr/task#az_acr_task_logs) een geselecteerd logboek weer te geven. Standaard worden logboeken 30 dagen bewaard.

Als een taak automatisch wordt geactiveerd, bijvoorbeeld door een update van de  broncode, is het openen van de opgeslagen logboeken de enige manier om de runlogboeken weer te geven. Automatische taaktriggers omvatten broncode-commits of pull-aanvragen, updates van basisafbeeldingen en timertriggers.

Voerlogboeken weergeven in de portal:

1. Navigeer naar uw containerregister.
1. Selecteer **in Services** de optie Taken **wordt**  >  **uitgevoerd.**
1. Selecteer een **Run Id om** de status van de run weer te geven en logboeken uit te voeren. Het logboek bevat dezelfde informatie als een gestreamd logboek, als er een wordt gegenereerd.

![Aanmeldingsportal voor taak uitvoeren weergeven](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Als u een logboek wilt weergeven met behulp van de Azure CLI, moet u [az acr task logs](/cli/azure/acr/task#az_acr_task_logs) uitvoeren en een run-id, een taaknaam of een specifieke afbeelding opgeven die door een build-taak is gemaakt. Als een taaknaam is opgegeven, toont de opdracht het logboek voor de laatst gemaakte run.

In het volgende voorbeeld wordt het logboek voor de run uitgevoerd met id *cf4*:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Alternatieve logboekopslag

Mogelijk wilt u logboeken voor taakruns opslaan in een lokaal bestandssysteem of een alternatieve archiveringsoplossing gebruiken, zoals Azure Storage.

Maak bijvoorbeeld een lokale *map tasklogs* en omleiding van de uitvoer van [az acr task logs](/cli/azure/acr/task#az_acr_task_logs) naar een lokaal bestand:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

U kunt lokale logboekbestanden ook opslaan in Azure Storage. Gebruik bijvoorbeeld de [Azure CLI,](../storage/blobs/storage-quickstart-blobs-cli.md)de [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md)of andere methoden om bestanden te uploaden naar een opslagaccount.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure Container Registry-taken](container-registry-tasks-overview.md)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-login]: /cli/azure/reference-index#az_login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
