---
title: Containerwerkbelastingen
description: Meer informatie over het uitvoeren en schalen van apps vanuit container installatie kopieën op Azure Batch. Maak een pool van reken knooppunten die ondersteuning bieden voor het uitvoeren van container taken.
ms.topic: how-to
ms.date: 10/06/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9d8776ba8e683cd14c766fead1e7238a6c24d000
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91843444"
---
# <a name="run-container-applications-on-azure-batch"></a>Container toepassingen uitvoeren op Azure Batch

Met Azure Batch kunt u grote aantallen taken voor batch verwerking op Azure uitvoeren en schalen. Batch taken kunnen rechtstreeks worden uitgevoerd op virtuele machines (knoop punten) in een batch-pool, maar u kunt ook een batch-pool instellen om taken uit te voeren in docker-compatibele containers op de knoop punten. In dit artikel wordt beschreven hoe u een pool van reken knooppunten maakt die ondersteuning bieden voor container taken en vervolgens container taken uitvoeren in de groep.

De code voorbeelden gebruiken hier de batch .NET-en python-Sdk's. U kunt ook andere batch-Sdk's en-hulpprogram ma's, waaronder de Azure Portal, gebruiken om batch-Pools met container functionaliteit te maken en container taken uit te voeren.

## <a name="why-use-containers"></a>Redenen om containers te gebruiken

Het gebruik van containers biedt een eenvoudige manier om batch taken uit te voeren zonder dat u een omgeving en afhankelijkheden hoeft te beheren om toepassingen uit te voeren. Containers implementeren toepassingen als Lightweight, Portable, Self-toereikende eenheden die in verschillende omgevingen kunnen worden uitgevoerd. Bouw en test bijvoorbeeld een container lokaal en upload de container installatie kopie naar een REGI ster in azure of ergens anders. Het implementatie model van de container zorgt ervoor dat de runtime omgeving van uw toepassing altijd correct is geïnstalleerd en geconfigureerd, waar u de toepassing host. Op containers gebaseerde taken in batch kunnen ook profiteren van functies van niet-container taken, waaronder toepassings pakketten en beheer van bron bestanden en uitvoer bestanden.

## <a name="prerequisites"></a>Vereisten

U moet bekend zijn met de container concepten en een batch-pool en-taak maken.

- **SDK-versies**: de batch-sdk's ondersteunen container installatie kopieën vanaf de volgende versies:
  - Batch-REST API versie 2017 -09-01.6.0
  - Batch .NET SDK-versie 8.0.0
  - Batch python SDK-versie 4,0
  - Batch Java SDK-versie 3,0
  - Batch Node.js SDK-versie 3,0

- **Accounts**: in uw Azure-abonnement moet u een batch-account en optioneel een Azure Storage-account maken.

- **Een ondersteunde VM-installatie kopie**: containers worden alleen ondersteund in Pools die zijn gemaakt met de configuratie van de virtuele machine, van afbeeldingen die worden beschreven in de volgende sectie, ondersteunde installatie kopieën van virtuele machines. Als u een aangepaste installatie kopie opgeeft, raadpleegt u de overwegingen in de volgende sectie en de vereisten in [een beheerde aangepaste installatie kopie gebruiken om een pool van virtuele machines te maken](batch-custom-images.md).

Houd de volgende beperkingen in acht:

- Batch biedt RDMA-ondersteuning alleen voor containers die worden uitgevoerd op Linux-Pools.

- Voor Windows-container workloads kunt u het beste een VM-grootte van meer kernen kiezen voor uw pool.

## <a name="supported-virtual-machine-images"></a>Ondersteunde installatie kopieën van virtuele machines

Gebruik een van de volgende ondersteunde Windows-of Linux-installatie kopieën voor het maken van een pool van VM-reken knooppunten voor container werkbelastingen. Zie [lijst met installatie kopieën van virtuele machines](batch-linux-nodes.md#list-of-virtual-machine-images)voor meer informatie over Marketplace-installatie kopieën die compatibel zijn met batch.

### <a name="windows-support"></a>Windows-ondersteuning

Batch ondersteunt installatie kopieën van Windows Server die ondersteuning bieden voor containers. Doorgaans worden deze SKU-namen in een achtervoegsel met `-with-containers` of ingevuld `-with-containers-smalldisk` . Daarnaast geeft [de API voor het weer geven van alle ondersteunde installatie kopieën in batch](batch-linux-nodes.md#list-of-virtual-machine-images) een `DockerCompatible` mogelijkheid als de installatie kopie docker-containers ondersteunt.

U kunt ook aangepaste installatie kopieën maken van virtuele machines waarop docker in Windows wordt uitgevoerd.

### <a name="linux-support"></a>Linux Support

Voor de workloads van een Linux-container ondersteunt batch momenteel de volgende Linux-installatie kopieën die door Microsoft Azure Batch zijn gepubliceerd in de Azure Marketplace zonder dat er een aangepaste installatie kopie nodig is.

#### <a name="vm-sizes-without-rdma"></a>VM-grootten zonder RDMA

- Uitgever `microsoft-azure-batch`
  - Oplossingen `centos-container`
  - Oplossingen `ubuntu-server-container`

#### <a name="vm-sizes-with-rdma"></a>VM-grootten met RDMA

- Uitgever `microsoft-azure-batch`
  - Oplossingen `centos-container-rdma`
  - Oplossingen `ubuntu-server-container-rdma`

Deze installatie kopieën worden alleen ondersteund voor gebruik in Azure Batch Pools en zijn bestemd voor het uitvoeren van docker-containers. Deze functie:

- Een vooraf geïnstalleerde docker-compatibele [Moby](https://github.com/moby/moby) -container-runtime

- Vooraf geïnstalleerde NVIDIA GPU-Stuur Programma's en NVIDIA-container runtime voor het stroom lijnen van de implementatie op Vm's uit de Azure N-serie

- Vooraf geïnstalleerde, vooraf geconfigureerde installatie kopie met ondersteuning voor de grootten van Infiniband RDMA VM voor installatie kopieën met het achtervoegsel van `-rdma` . Deze afbeeldingen bieden momenteel geen ondersteuning voor SR-IOV IB/RDMA-VM-grootten.

U kunt ook aangepaste installatie kopieën maken van virtuele machines die docker uitvoeren op een van de Linux-distributies die compatibel zijn met batch. Als u ervoor kiest om uw eigen aangepaste Linux-installatie kopie op te geven, raadpleegt u de instructies in [een beheerde aangepaste installatie kopie gebruiken om een pool van virtuele machines te maken](batch-custom-images.md).

Installeer [docker Community Edition (CE)](https://www.docker.com/community-edition) of [docker Enter PRISE Edition (EE)](https://www.docker.com/enterprise-edition)voor docker-ondersteuning op een aangepaste installatie kopie.

Aanvullende overwegingen voor het gebruik van een aangepaste Linux-installatie kopie:

- Als u gebruik wilt maken van de GPU-prestaties van de Azure N-serie-grootten wanneer u een aangepaste installatie kopie gebruikt, installeert u NVIDIA-Stuur Programma's vooraf. U moet ook het hulp programma docker-engine voor NVIDIA-Gpu's, [NVIDIA docker](https://github.com/NVIDIA/nvidia-docker)installeren.

- Voor toegang tot het Azure RDMA-netwerk gebruikt u een met RDMA geschikte VM-grootte. Benodigde RDMA-Stuur Programma's worden geïnstalleerd in de CentOS HPC-en Ubuntu-installatie kopieën die door batch worden ondersteund. Er is mogelijk aanvullende configuratie nodig om MPI-workloads uit te voeren. Zie [RDMA-compatibele of GPU-compatibele instanties gebruiken in de batch-pool](batch-pool-compute-intensive-sizes.md).

## <a name="container-configuration-for-batch-pool"></a>Container configuratie voor batch-pool

Als u een batch-pool wilt inschakelen om container werkbelastingen uit te voeren, moet u [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) -instellingen opgeven in het [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) -object van de groep. (In dit artikel vindt u koppelingen naar de naslag informatie voor batch .NET API. De bijbehorende instellingen bevinden zich in de [batch-python](/python/api/overview/azure/batch) -API.)

U kunt een container groep maken met of zonder vooraf opgehaalde container installatie kopieën, zoals wordt weer gegeven in de volgende voor beelden. Met het pull-of prefetch-proces kunt u container installatie kopieën vooraf laden vanuit docker hub of een ander container register op internet. Gebruik voor de beste prestaties een [Azure container Registry](../container-registry/container-registry-intro.md) in dezelfde regio als het batch-account.

Het voor deel van het vooraf ophalen van container installatie kopieën is dat wanneer taken voor het eerst worden gestart, niet hoeven te wachten tot de container installatie kopie is gedownload. De container configuratie haalt container installatie kopieën op naar de virtuele machines wanneer de pool wordt gemaakt. Taken die worden uitgevoerd in de groep, kunnen vervolgens verwijzen naar de lijst met container installatie kopieën en de opties voor container uitvoering.

### <a name="pool-without-prefetched-container-images"></a>Groep zonder vooraf opgehaalde container installatie kopieën

Als u een container groep wilt configureren zonder vooraf opgehaalde container installatie kopieën, definieert u `ContainerConfiguration` en `VirtualMachineConfiguration` objecten, zoals wordt weer gegeven in de volgende voor beelden. In deze voor beelden wordt de Ubuntu-Server gebruikt voor de installatie kopie van Azure Batch container Pools van de Marketplace.

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration. This is required even though there are no prefetched images.
"""

container_conf = batch.models.ContainerConfiguration()

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

// Specify container configuration. This is required even though there are no prefetched images.
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 1,
    virtualMachineSize: "STANDARD_D1_V2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

### <a name="prefetch-images-for-container-configuration"></a>Prefetch-installatie kopieën voor container configuratie

Als u container installatie kopieën in de pool wilt prefetch, voegt u de lijst met container installatie kopieën ( `container_image_names` in python) toe aan de `ContainerConfiguration` .

In het volgende voor beeld van een basis python ziet u hoe u een standaard Ubuntu-container installatie kopie kunt prefetch van [docker hub](https://hub.docker.com).

```python
image_ref_to_use = batch.models.ImageReference(
    publisher='microsoft-azure-batch',
    offer='ubuntu-server-container',
    sku='16-04-lts',
    version='latest')

"""
Specify container configuration, fetching the official Ubuntu container image from Docker Hub.
"""

container_conf = batch.models.ContainerConfiguration(
    container_image_names=['ubuntu'])

new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        container_configuration=container_conf,
        node_agent_sku_id='batch.node.ubuntu 16.04'),
    vm_size='STANDARD_D1_V2',
    target_dedicated_nodes=1)
...
```

In het volgende C#-voor beeld wordt ervan uitgegaan dat u een tensor flow-installatie kopie van [docker hub](https://hub.docker.com)wilt prefetch. Dit voor beeld bevat een begin taak die wordt uitgevoerd op de VM-host op de groeps knooppunten. U kunt een begin taak uitvoeren op de host, bijvoorbeeld om een bestands server te koppelen die toegankelijk is vanuit de containers.

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "microsoft-azure-batch",
    offer: "ubuntu-server-container",
    sku: "16-04-lts",
    version: "latest");

ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "https://hub.docker.com",
    userName: "UserName",
    password: "YourPassword"                
);

// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> { "tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry };

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Set a native host command line start task
StartTask startTaskContainer = new StartTask( commandLine: "<native-host-command-line>" );

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Start the task in the pool
pool.StartTask = startTaskContainer;
...
```

### <a name="prefetch-images-from-a-private-container-registry"></a>Installatie kopieën opprefetch van een persoonlijk container register

U kunt ook container installatie kopieën prefetch door te verifiëren bij een persoonlijke container register server. In de volgende voor beelden `ContainerConfiguration` `VirtualMachineConfiguration` prefetchen de en objecten een persoonlijke tensor flow-installatie kopie van een persoonlijk Azure container Registry. De verwijzing naar de afbeelding is hetzelfde als in het vorige voor beeld.

```python
image_ref_to_use = batch.models.ImageReference(
        publisher='microsoft-azure-batch',
        offer='ubuntu-server-container',
        sku='16-04-lts',
        version='latest')

# Specify a container registry
container_registry = batch.models.ContainerRegistry(
        registry_server="myRegistry.azurecr.io",
        user_name="myUsername",
        password="myPassword")

# Create container configuration, prefetching Docker images from the container registry
container_conf = batch.models.ContainerConfiguration(
        container_image_names = ["myRegistry.azurecr.io/samples/myImage"],
        container_registries =[container_registry])

new_pool = batch.models.PoolAddParameter(
            id="myPool",
            virtual_machine_configuration=batch.models.VirtualMachineConfiguration(
                image_reference=image_ref_to_use,
                container_configuration=container_conf,
                node_agent_sku_id='batch.node.ubuntu 16.04'),
            vm_size='STANDARD_D1_V2',
            target_dedicated_nodes=1)
```

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry(
    registryServer: "myContainerRegistry.azurecr.io",
    userName: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration();
containerConfig.ContainerImageNames = new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" };
containerConfig.ContainerRegistries = new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");
virtualMachineConfiguration.ContainerConfiguration = containerConfig;

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);
...
```

## <a name="container-settings-for-the-task"></a>Container instellingen voor de taak

Als u een container taak wilt uitvoeren in een groep waarvoor container is ingeschakeld, geeft u providerspecifieke instellingen op. Instellingen zijn onder andere de installatie kopie voor gebruik, het REGI ster en de uitvoer opties voor containers.

- Gebruik de `ContainerSettings` eigenschap van de taak klassen om providerspecifieke instellingen te configureren. Deze instellingen worden gedefinieerd door de [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) -klasse. Houd er rekening mee dat voor de `--rm` container optie geen extra `--runtime` optie is vereist, omdat deze door batch wordt verwerkt.

- Als u taken uitvoert in container installatie kopieën, zijn voor de taak [Cloud taak](/dotnet/api/microsoft.azure.batch.cloudtask) en [taak beheer](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask) container instellingen nodig. De taak voor het [starten van taken](/dotnet/api/microsoft.azure.batch.starttask), taak [voorbereiding](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)en taak [release](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask) vereisen echter geen container instellingen (dat wil zeggen, ze kunnen worden uitgevoerd binnen een container context of rechtstreeks op het knoop punt).

- Voor Windows moeten taken worden uitgevoerd waarbij [ElevationLevel](/rest/api/batchservice/task/add#elevationlevel) is ingesteld op `admin` . 

- Voor Linux wijst batch de machtiging gebruiker/groep toe aan de container. Als voor toegang tot een map in de container een beheerders machtiging is vereist, moet u de taak mogelijk uitvoeren als groeps bereik met beheerders bevoegdheden. Dit zorgt ervoor dat de taak in batch wordt uitgevoerd als root in de container context. Anders is het mogelijk dat een gebruiker die geen beheerder is, geen toegang heeft tot deze mappen.

- Voor container groepen met GPU-hardware wordt met batch automatisch GPU ingeschakeld voor container taken, dus u mag het argument niet toevoegen `–gpus` .

### <a name="container-task-command-line"></a>Opdracht regel voor container taak

Wanneer u een container taak uitvoert, gebruikt batch automatisch de opdracht [docker Create](https://docs.docker.com/engine/reference/commandline/create/) om een container te maken met behulp van de installatie kopie die is opgegeven in de taak. Batch beheert vervolgens de uitvoering van de taak in de container.

Net als bij niet-container batch taken, stelt u een opdracht regel in voor een container taak. Omdat batch automatisch de container maakt, geeft de opdracht regel alleen de opdracht of opdrachten op die in de container zullen worden uitgevoerd.

Als de container installatie kopie voor een batch-taak is geconfigureerd met een [Entry Point](https://docs.docker.com/engine/reference/builder/#exec-form-entrypoint-example) -script, kunt u de opdracht regel instellen op het standaard toegangs punt of het overschrijven:

- Als u het standaard-INGANGs punt van de container installatie kopie wilt gebruiken, stelt u de opdracht regel voor de taak in op de lege teken reeks `""` .

- Als u het standaard toegangs punt wilt overschrijven of als de afbeelding geen entry point heeft, stelt u een opdracht regel in die geschikt is voor de container, bijvoorbeeld `/app/myapp` of `/bin/sh -c python myscript.py` .

Optionele [ContainerRunOptions](/dotnet/api/microsoft.azure.batch.taskcontainersettings.containerrunoptions) zijn aanvullende argumenten die u opgeeft voor de `docker create` opdracht die door batch wordt gebruikt om de container te maken en uit te voeren. Als u bijvoorbeeld een werkmap wilt instellen voor de container, stelt u de `--workdir <directory>` optie in. Raadpleeg de [docker](https://docs.docker.com/engine/reference/commandline/create/) -Naslag informatie maken voor extra opties.

### <a name="container-task-working-directory"></a>Werkmap voor container taak

Een batch-container taak wordt uitgevoerd in een werkmap in de container die vergelijkbaar is met de Directory batch is ingesteld voor een normale (niet-container) taak. Deze werkmap wijkt af van de [workdir](https://docs.docker.com/engine/reference/builder/#workdir) als deze is geconfigureerd in de installatie kopie, of de standaard container werkmap ( `C:\`  op een Windows-container of `/` in een Linux-container).

Voor een batch-container taak:

- Alle mappen recursief onder het `AZ_BATCH_NODE_ROOT_DIR` knoop punt host (de hoofdmap van Azure batch directory's) worden toegewezen aan de container
- Alle taak omgevings variabelen worden toegewezen aan de container
- De werkmap van de taak `AZ_BATCH_TASK_WORKING_DIR` op het knoop punt is ingesteld op dezelfde als voor een gewone taak en toegewezen aan de container.

Met deze toewijzingen kunt u op ongeveer dezelfde manier werken met container taken als niet-container taken. U kunt bijvoorbeeld toepassingen installeren met behulp van toepassings pakketten, bron bestanden openen vanuit Azure Storage, instellingen voor de taak omgeving gebruiken en uitvoer bestanden van taken persistent maken nadat de container is gestopt.

### <a name="troubleshoot-container-tasks"></a>Problemen met container taken oplossen

Als uw container taak niet wordt uitgevoerd zoals verwacht, moet u mogelijk informatie ophalen over de WORKDIR of de toegangs punt configuratie van de container installatie kopie. Voer de opdracht voor het [inspecteren van de docker-installatie kopie](https://docs.docker.com/engine/reference/commandline/image_inspect/) uit om de configuratie weer te geven.

Indien nodig past u de instellingen van de container taak aan op basis van de installatie kopie:

- Geef een absoluut pad op in de opdracht regel van de taak. Als het standaard toegangs punt voor de taak opdracht regel wordt gebruikt, moet u ervoor zorgen dat een absoluut pad is ingesteld.
- Wijzig in de uitvoer opties van de taak container de werkmap zodat deze overeenkomt met de WORKDIR in de installatie kopie. Stel bijvoorbeeld in `--workdir /app` .

## <a name="container-task-examples"></a>Voor beelden van container taken

Het volgende python-fragment toont een Basic-opdracht regel die wordt uitgevoerd in een container die is gemaakt op basis van een fictieve installatie kopie die wordt opgehaald uit docker hub. Hier verwijdert de `--rm` container optie de container nadat de taak is voltooid en met de `--workdir` optie wordt een werkmap ingesteld. De opdracht regel overschrijft het container-INGANGs punt met een eenvoudige shell-opdracht waarmee een klein bestand naar de werkmap van de taak op de host wordt geschreven.

```python
task_id = 'sampletask'
task_container_settings = batch.models.TaskContainerSettings(
    image_name='myimage',
    container_run_options='--rm --workdir /')
task = batch.models.TaskAddParameter(
    id=task_id,
    command_line='/bin/sh -c \"echo \'hello world\' > $AZ_BATCH_TASK_WORKING_DIR/output.txt\"',
    container_settings=task_container_settings
)
```

In het volgende C#-voor beeld ziet u basis instellingen voor de container voor een Cloud taak:

```csharp
// Simple container task command
string cmdLine = "c:\\app\\myApp.exe";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "myimage",
    containerRunOptions: "--rm --workdir c:\\app"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    commandline: cmdLine);
containerTask.ContainerSettings = cmdContainerSettings;
```

## <a name="next-steps"></a>Volgende stappen

- Zie de [batch Shipyard](https://github.com/Azure/batch-shipyard) Toolkit voor een eenvoudige implementatie van container werkbelastingen op Azure batch via [Shipyard recepten](https://github.com/Azure/batch-shipyard/tree/master/recipes).
- Raadpleeg de [docker](https://docs.docker.com/engine/installation/) -documentatie voor meer informatie over het installeren en gebruiken van docker CE op Linux.
- Meer informatie over het [gebruik van een beheerde aangepaste installatie kopie voor het maken van een groep virtuele machines](batch-custom-images.md).
- Meer informatie over het [Moby-project](https://mobyproject.org/), een framework voor het maken van op containers gebaseerde systemen.
