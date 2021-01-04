---
title: 'Quickstart: uw eerste Batch-taak uitvoeren met de Azure CLI'
description: In deze quickstart ziet u hoe u een Batch-account maakt en een Batch-taak uitvoert met behulp van Azure CLI.
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 297af47b6280381646e654eaededfe8b71a5d874
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106679"
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>Quickstart: Uw eerste Batch-taak uitvoeren met Azure CLI

Ga aan de slag met Azure Batch door de Azure CLI te gebruiken voor het maken van een Batch-account, een pool met berekeningsknooppunten (virtuele machines) en een taak waarmee taken worden uitgevoerd in de pool. Met elke voorbeeldtaak wordt een basisopdracht uitgevoerd op een van de knooppunten in de pool.

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. Nadat u deze snelstartgids hebt voltooid, begrijpt u de belangrijkste principes van de Batch-service en bent u er klaar voor om Batch op grotere schaal te gebruiken voor meer realistische workloads.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor deze quickstart is versie 2.0.20 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam *QuickstartBatch-rg* gemaakt in de locatie *eastus2*.

```azurecli-interactive
az group create \
    --name QuickstartBatch-rg \
    --location eastus2
```

## <a name="create-a-storage-account"></a>Create a storage account

U kunt een Azure-opslagaccount aan het Batch-account koppelen. Hoewel dit niet is vereist voor deze snelstartgids, is een opslagaccount handig voor het implementeren van toepassingen, en voor het opslaan van invoer- en uitvoergegevens voor de meeste workloads uit de praktijk. Maak een opslagaccount in de resourcegroep met behulp van de opdracht [az storage account create](/cli/azure/storage/account#az-storage-account-create).

```azurecli-interactive
az storage account create \
    --resource-group QuickstartBatch-rg \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Batch-account maken

Maak een Batch-account met behulp van de opdracht [az batch account create](/cli/azure/batch/account#az-batch-account-create). U hebt een account nodig om rekenresources (pools met rekenknooppunten) en Batch-taken te maken.

In het volgende voorbeeld wordt een Batch-account met de naam *mybatchaccount* gemaakt in *QuickstartBatch-rg* en wordt het door u gemaakte opslagaccount gekoppeld.  

```azurecli-interactive
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group QuickstartBatch-rg \
    --location eastus2
```

Als u rekenpools en -taken wilt maken en beheren, moet u zich verifiëren bij Batch. Meld u aan bij het account met behulp van de opdracht [az batch account login](/cli/azure/batch/account#az-batch-account-login). Nadat u zich hebt aangemeld, maken de `az batch`-opdrachten gebruik van deze accountcontext.

```azurecli-interactive
az batch account login \
    --name mybatchaccount \
    --resource-group QuickstartBatch-rg \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>Een pool met rekenknooppunten maken

Nu u beschikt over een Batch-account, maakt u een voorbeeldpool met Linux-rekenknooppunten met behulp van de opdracht [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create). In het volgende voorbeeld wordt een pool gemaakt met de naam *mypool* met *Standard_A1_v2*-knooppunten van 2 grootten waarop Ubuntu 16.04 TNS wordt uitgevoerd. De voorgestelde knooppuntgrootte in dit snelle voorbeeld biedt een goede balans tussen prestaties en kosten.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

De pool wordt onmiddellijk gemaakt in Batch, maar het duurt enkele minuten voordat de rekenknooppunten zijn toegewezen en gestart. Gedurende deze periode heeft de pool de status `resizing`. Als u de status van de pool wilt zien, voert u de opdracht [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) uit. Met deze opdracht worden alle eigenschappen van de pool weergegeven en kunt u zoeken naar specifieke eigenschappen. Met de volgende opdracht wordt de toewijzingsstatus van de pool opgehaald:

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

Ga door met de volgende stappen om een Batch-taak en taken te maken terwijl de status van de pool wordt gewijzigd. De pool is gereed voor het uitvoeren van taken als de toewijzingsstatus `steady` is en alle knooppunten worden uitgevoerd.

## <a name="create-a-job"></a>Een taak maken

Nu u beschikt over een pool, kunt u een Batch-taak maken om uit te voeren op deze pool. Een Batch-taak is een logische groep met een of meer taken. Een Batch-taak omvat instellingen die gemeenschappelijk zijn voor de taken, zoals prioriteit en de pool waarop taken moeten worden uitgevoerd. Maak een Batch-taak met behulp van de opdracht [az batch job create](/cli/azure/batch/job#az-batch-job-create). In het volgende voorbeeld wordt een taak *myjob* gemaakt in de pool *mypool*. De Batch-taak heeft in eerste instantie geen taken.

```azurecli-interactive
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>Taken maken

Gebruik nu de opdracht [az batch task create](/cli/azure/batch/task#az-batch-task-create) om taken te maken voor uitvoering in de Batch-taak. In dit voorbeeld maakt u vier identieke taken. Met elke taak wordt een `command-line` uitgevoerd om de variabelen voor de Batch-omgeving op een rekenknooppunt weer te geven. Vervolgens is er een pauze van 90 seconden. Wanneer u Batch gebruikt, geeft u uw app of script op deze opdrachtregel op. Batch biedt een aantal manieren om apps en scripts te implementeren op rekenknooppunten.

Met het volgende Bash-script worden 4 parallelle taken gemaakt (*mytask1* tot en met *mytask4*).

```azurecli-interactive
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
done
```

In de opdrachtuitvoer ziet u de instellingen voor elk van de taken. De taken worden met Batch naar de rekenknooppunten gedistribueerd.

## <a name="view-task-status"></a>Taakstatus weergeven

Nadat u een taak hebt gemaakt, wordt deze in Batch in de wachtrij geplaatst voor uitvoering op de pool. Zodra een knooppunt beschikbaar is om de taak uit te voeren, wordt de taak uitgevoerd.

Gebruik de opdracht [az batch task show](/cli/azure/batch/task#az-batch-task-show) om de status van de Batch-taken weer te geven. In het volgende voorbeeld worden details weergegeven over *mytask1* die wordt uitgevoerd op een van de knooppunten in de pool.

```azurecli-interactive
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

De opdrachtuitvoer bevat veel details, maar let vooral op de `exitCode` van de opdrachtregel voor de taak en op de `nodeId`. Een `exitCode` van 0 geeft aan dat de opdrachtregel voor de taak is voltooid. De `nodeId` geeft de id aan van het knooppunt in de pool waarop de taak is uitgevoerd.

## <a name="view-task-output"></a>Taakuitvoer weergeven

Als u de bestanden wilt weergeven die met een taak op een rekenknooppunt zijn gemaakt, gebruikt u de opdracht [az batch task file list](/cli/azure/batch/task). Met de volgende opdracht worden de bestanden weergegeven die zijn gemaakt met *mytask1*:

```azurecli-interactive
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

De uitvoer lijkt op het volgende:

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

Gebruik de opdracht [az batch task file download](/cli/azure/batch/task) om een van de uitvoerbestanden te downloaden naar een lokale map. In dit voorbeeld bevindt de taakuitvoer zich in `stdout.txt`.

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

U kunt de inhoud van `stdout.txt` weergeven in een teksteditor. De inhoud geeft de variabelen voor de Azure Batch-omgeving weer die zijn ingesteld op het knooppunt. Wanneer u uw eigen Batch-taken maakt, kunt u naar deze omgevingsvariabelen verwijzen in opdrachtregels voor taken, en in de apps en scripts die met de opdrachtregel worden uitgevoerd. Bijvoorbeeld:

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2.batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjobl
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-257509324_2-20180703t215033z
AZ_BATCH_POOL_ID=mypool
AZ_BATCH_TASK_ID=mytask1
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt doorgaan met Batch-zelfstudies en -voorbeelden, gebruikt u het Batch-account en het gekoppelde opslagaccount dat in deze snelstartgids is gemaakt. Voor het Batch-account zelf worden geen kosten in rekening gebracht.

Er worden kosten berekend voor pools zolang de knooppunten actief zijn, zelfs als er geen taken zijn gepland. Wanneer u een pool niet meer nodig hebt, verwijdert u deze met de opdracht [az batch pool delete](/cli/azure/batch/pool#az-batch-pool-delete). Wanneer u de pool verwijdert, wordt ook alle taakuitvoer op de knooppunten verwijderd.

```azurecli-interactive
az batch pool delete --pool-id mypool
```

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep, het Batch-account, de pools en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt. U verwijdert de resources als volgt:

```azurecli-interactive
az group delete --name QuickstartBatch-rg
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u een Batch-account, een Batch-pool en een Batch-taak gemaakt. Met de Batch-taak zijn voorbeeldtaken uitgevoerd, en u hebt de uitvoer van een van de knooppunten bekeken. Nu u de belangrijkste principes van de Batch-service begrijpt, bent u er klaar voor om Batch op grotere schaal te gebruiken voor meer realistische workloads. Voor meer informatie over Azure Batch gaat u naar de Azure Batch-zelfstudies.

> [!div class="nextstepaction"]
> [Azure Batch-zelfstudies](./tutorial-parallel-dotnet.md)
