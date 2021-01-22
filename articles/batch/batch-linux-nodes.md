---
title: Linux uitvoeren op reken knooppunten van virtuele machine
description: Meer informatie over het verwerken van werk belastingen voor parallelle berekeningen op Pools met virtuele Linux-machines in Azure Batch.
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: c711ec0d035b9b59ec7628a51fe3cff26de358bc
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683697"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>Linux-reken knooppunten inrichten in batch-Pools

U kunt Azure Batch gebruiken om werk belastingen voor parallelle berekeningen uit te voeren op virtuele machines met Linux en Windows. In dit artikel wordt beschreven hoe u Pools van Linux-reken knooppunten in de batch-service kunt maken met behulp van zowel [batch python](https://pypi.python.org/pypi/azure-batch) als [batch .net](/dotnet/api/microsoft.azure.batch) -client bibliotheken. 

## <a name="virtual-machine-configuration"></a>Configuratie van virtuele machine

Wanneer u een pool van reken knooppunten in batch maakt, hebt u twee opties waaruit u de grootte van het knoop punt en het besturings systeem kunt selecteren: Cloud Services configuratie en configuratie van de virtuele machine. [Virtuele-machine configuratie](nodes-and-pools.md#virtual-machine-configuration) Pools bestaan uit Azure vm's, die kunnen worden gemaakt op basis van Linux-of Windows-installatie kopieën. Wanneer u een pool maakt met virtuele-machine configuratie, geeft u een [beschik bare grootte van een reken knooppunt](../virtual-machines/sizes.md), de verwijzing naar de installatie kopie van de virtuele machine die op de knoop punten moet worden geïnstalleerd en de SKU van de batch-knooppunt agent (een programma dat op elk knoop punt wordt uitgevoerd en biedt een interface tussen het knoop punt en de batch-service).

### <a name="virtual-machine-image-reference"></a>Naslag informatie voor installatie kopieën van virtuele machines

De batch-service maakt gebruik van [schaal sets voor virtuele machines](../virtual-machine-scale-sets/overview.md) voor het leveren van reken knooppunten in de configuratie van de virtuele machine. U kunt een installatie kopie van de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)opgeven of [de galerie met gedeelde afbeeldingen gebruiken om een aangepaste installatie kopie voor te bereiden](batch-sig-images.md).

Wanneer u een verwijzing naar een installatie kopie van een virtuele machine maakt, moet u de volgende eigenschappen opgeven:

| **Verwijzings eigenschap voor installatie kopie** | **Voorbeeld** |
| --- | --- |
| Publisher |Canonical |
| Aanbieding |UbuntuServer |
| SKU |18.04-LTS |
| Versie |meest recente |

> [!TIP]
> Meer informatie over deze eigenschappen en hoe u Marketplace-installatie kopieën kunt opgeven in [Linux VM-installatie kopieën zoeken in azure Marketplace met de Azure cli](../virtual-machines/linux/cli-ps-findimage.md). Houd er rekening mee dat sommige installatie kopieën van Marketplace momenteel niet compatibel zijn met batch.

### <a name="list-of-virtual-machine-images"></a>Lijst met installatie kopieën van virtuele machines

Niet alle Marketplace-installatie kopieën zijn compatibel met de momenteel beschik bare batch-knooppunt agenten. Gebruik [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (python), [ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (batch .net) of de bijbehorende API in een andere taal-SDK voor een lijst met alle ondersteunde installatie kopieën voor virtuele machines van de Marketplace voor de batch-service en de bijbehorende node-agent-sku's.

### <a name="node-agent-sku"></a>SKU van knoop punt agent

De [batch-knooppunt agent](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md) is een programma dat wordt uitgevoerd op elk knoop punt in de pool en biedt de opdracht-en besturings interface tussen het knoop punt en de batch-service. Er zijn verschillende implementaties van de knooppunt agent, ook wel Sku's genoemd, voor verschillende besturings systemen. Wanneer u een virtuele-machine configuratie maakt, geeft u eerst de verwijzing naar de installatie kopie van de virtuele machine op en geeft u vervolgens de knooppunt agent op die moet worden geïnstalleerd op de installatie kopie. Normaal gesp roken is elke node agent-SKU compatibel met meerdere installatie kopieën van virtuele machines. Hier volgen enkele voor beelden van node-agent-Sku's:

- batch. node. Ubuntu 18,04
- batch. node. CentOS 7
- batch. node. Windows amd64

## <a name="create-a-linux-pool-batch-python"></a>Een Linux-groep maken: batch python

Het volgende code fragment toont een voor beeld van het gebruik van de [Microsoft Azure batch-client bibliotheek voor python](https://pypi.python.org/pypi/azure-batch) om een pool met Ubuntu-Server Compute-knoop punten te maken. Raadpleeg de [referentie documentatie](/python/api/overview/azure/batch)voor meer informatie over de batch-python-module.

Met dit code fragment wordt een [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) expliciet gemaakt en worden alle eigenschappen (uitgever, aanbieding, SKU, versie) opgegeven. In productie code wordt u echter aangeraden de methode [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) te gebruiken om een keuze te selecteren in de beschik bare combi Naties van installatie kopie en node-agent tijdens runtime.

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

Zoals eerder vermeld, kunt u het beste de methode [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) gebruiken om dynamisch te selecteren uit de momenteel ondersteunde combi Naties van knooppunt agent/Marketplace (in plaats van een [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference) expliciet te maken). Het volgende python-code fragment laat zien hoe u deze methode gebruikt.

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>Een Linux-groep maken: batch .NET

Het volgende code fragment toont een voor beeld van het gebruik van de [batch .net](https://www.nuget.org/packages/Microsoft.Azure.Batch/) -client bibliotheek voor het maken van een pool met Ubuntu-Server Compute-knoop punten. Raadpleeg de [referentie documentatie](/dotnet/api/microsoft.azure.batch)voor meer informatie over batch .net.

In het volgende code fragment wordt de methode [pool Operations. ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) gebruikt om een selectie te selecteren in de lijst met momenteel ondersteunde Marketplace-installatie kopieën en node agent-combi Naties. Deze techniek wordt aanbevolen, omdat de lijst met ondersteunde combi Naties van tijd tot tijd kan veranderen. De meeste ondersteunde combi Naties worden toegevoegd.

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

Hoewel in het vorige fragment de methode [pool Operations. istSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) wordt gebruikt voor het dynamisch weer geven en selecteren van ondersteunde installatie kopieën en combi Naties van knooppunt agent-sku's (aanbevolen), kunt u ook een [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) expliciet configureren:

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="connect-to-linux-nodes-using-ssh"></a>Verbinding maken met Linux-knoop punten met SSH

Tijdens het ontwikkelen of tijdens het oplossen van problemen kan het nodig zijn om u aan te melden bij de knoop punten in uw pool. In tegens telling tot Windows-reken knooppunten kunt u Remote Desktop Protocol (RDP) niet gebruiken om verbinding te maken met Linux-knoop punten. In plaats daarvan schakelt de batch-service op elk knoop punt SSH-toegang in voor externe verbinding.

Met het volgende python-code fragment wordt een gebruiker gemaakt op elk knoop punt in een pool, wat vereist is voor externe verbinding. Vervolgens worden de gegevens voor de Secure Shell (SSH)-verbinding voor elk knoop punt afgedrukt.

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

Deze code heeft dezelfde uitvoer als in het volgende voor beeld. In dit geval bevat de groep vier Linux-knoop punten.

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

In plaats van een wacht woord kunt u een open bare SSH-sleutel opgeven wanneer u een gebruiker op een knoop punt maakt. Gebruik in de python-SDK de para meter **ssh_public_key** op [ComputeNodeUser](/python/api/azure-batch/azure.batch.models.computenodeuser). In .NET gebruikt u de eigenschap [ComputeNodeUser. SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey) .

## <a name="pricing"></a>Prijzen

Azure Batch is gebaseerd op Azure Cloud Services en Azure Virtual Machines technologie. De batch-service zelf wordt gratis aangeboden. Dit betekent dat u alleen kosten in rekening brengt voor de reken resources (en kosten die van kracht zijn) die uw batch-oplossingen verbruiken. Wanneer u de **configuratie van de virtuele machine** kiest, worden kosten in rekening gebracht op basis van de [virtual machines prijs](https://azure.microsoft.com/pricing/details/virtual-machines/) structuur.

Als u toepassingen implementeert voor uw batch-knoop punten met behulp van [toepassings pakketten](batch-application-packages.md), worden er ook kosten in rekening gebracht voor de Azure storage resources die door uw toepassing worden gebruikt.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [python-code voorbeelden](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch) in de [github-opslag plaats Azure-batch-samples](https://github.com/Azure/azure-batch-samples) om te zien hoe u veelvoorkomende batch bewerkingen kunt uitvoeren, zoals pool, taak en het maken van taken. Het [Leesmij-bestand](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md) bij de python-voor beelden bevat informatie over het installeren van de vereiste pakketten.
- Meer informatie over [het gebruik van vm's met lage prioriteit](batch-low-pri-vms.md) met batch.
