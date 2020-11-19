---
title: De galerie met gedeelde afbeeldingen gebruiken om een aangepaste installatie kopie groep te maken
description: Aangepaste installatie kopie groepen zijn een efficiënte manier om reken knooppunten te configureren om uw batch-workloads uit te voeren.
ms.topic: conceptual
ms.date: 11/18/2020
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: f0ba6270e6b6b4fcd258d8f5b3668931706f95b5
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888348"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-image-pool"></a>De galerie met gedeelde afbeeldingen gebruiken om een aangepaste installatie kopie groep te maken

Wanneer u een Azure Batch groep maakt met behulp van de configuratie van de virtuele machine, geeft u een VM-installatie kopie op die het besturings systeem levert voor elk reken knooppunt in de pool. U kunt een groep virtuele machines maken met behulp van een ondersteunde installatie kopie van Azure Marketplace of een aangepaste installatie kopie maken met een afbeelding van de [Galerie met gedeelde installatie kopieën](../virtual-machines/windows/shared-image-galleries.md).

## <a name="benefits-of-the-shared-image-gallery"></a>Voor delen van de galerie met gedeelde afbeeldingen

Wanneer u de galerie met gedeelde afbeeldingen gebruikt voor uw aangepaste installatie kopie, hebt u controle over het type en de configuratie van het besturings systeem, evenals het type gegevens schijven. De gedeelde installatie kopie kan toepassingen en referentie gegevens bevatten die beschikbaar worden op alle knoop punten in de batch-pool zodra ze zijn ingericht.

U kunt ook meerdere versies van een installatie kopie naar behoefte hebben voor uw omgeving. Wanneer u een installatiekopieversie gebruikt om een VM te maken, wordt de installatiekopieversie gebruikt voor het maken van nieuwe schijven voor de VM.

Met een gedeelde installatie kopie bespaart u tijd bij het voorbereiden van de reken knooppunten van uw pool om uw batch-workload uit te voeren. Het is mogelijk om een Azure Marketplace-installatie kopie te gebruiken en software te installeren op elk reken knooppunt na het inrichten, maar het gebruik van een gedeelde installatie kopie is doorgaans efficiënter. Daarnaast kunt u meerdere replica's opgeven voor de gedeelde installatie kopie, dus wanneer u groepen met veel Vm's (meer dan 600 Vm's) maakt, bespaart u tijd bij het maken van de groep.

Het gebruik van een gedeelde installatie kopie die voor uw scenario is geconfigureerd, kan verschillende voor delen bieden:

- **Gebruik dezelfde installatie kopieën in de regio's.** U kunt replica's van gedeelde installatie kopieën maken in verschillende regio's zodat al uw Pools gebruikmaken van dezelfde installatie kopie.
- **Het besturings systeem (OS) configureren.** U kunt de configuratie van de besturingssysteem schijf van de installatie kopie aanpassen.
- **Installeer toepassingen vooraf.** Het vooraf installeren van toepassingen op de besturingssysteem schijf is efficiënter en minder fout gevoelig dan het installeren van toepassingen na het inrichten van de reken knooppunten met een begin taak.
- **Kopieer een grote hoeveelheid gegevens eenmaal.** Statische gegevens delen van de beheerde gedeelde installatie kopie maken door deze te kopiëren naar de gegevens schijven van een beheerde installatie kopie. Dit hoeft slechts één keer te worden gedaan en er worden gegevens beschikbaar gemaakt voor elk knoop punt van de groep.
- **Verg root Pools tot grotere grootten.** Met de galerie gedeelde afbeeldingen kunt u met uw aangepaste installatie kopieën grote groepen maken, samen met meer gedeelde afbeeldings replica's.
- **Betere prestaties dan het gebruik van alleen een beheerde installatie kopie als een aangepaste installatie kopie.** Voor een aangepaste afbeeldings groep voor een gedeelde installatie kopie is de tijd voor het bereiken van de constante status tot 25% sneller en is de latentie van de VM-inactiviteit tot wel 30% korter.
- **Installatie kopie versie en groepering voor eenvoudiger beheer.** De definitie van de groepering van installatie kopieën bevat informatie over waarom de installatie kopie is gemaakt, waarvoor het besturings systeem is en informatie over het gebruik van de installatie kopie. Door installatie kopieën te groeperen kunt u het beheer van afbeeldingen vereenvoudigen. Zie [afbeeldings definities](../virtual-machines/windows/shared-image-galleries.md#image-definitions)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

> [!NOTE]
> U moet zich verifiëren met Azure AD. Als u gedeelde-sleutel verificatie gebruikt, krijgt u een verificatie fout.  

- **Een Azure Batch-account.** Als u een batch-account wilt maken, raadpleegt u de batch-Quick starts met behulp van de [Azure Portal](quick-create-portal.md) of [Azure cli](quick-create-cli.md).

- **Een afbeelding van de galerie met gedeelde afbeeldingen**. Als u een gedeelde installatie kopie wilt maken, moet u een beheerde installatie kopie bron hebben of maken. De installatie kopie moet worden gemaakt op basis van moment opnamen van de besturingssysteem schijf van de VM en optioneel op de gekoppelde gegevens schijven.

> [!NOTE]
> Als de gedeelde installatie kopie zich niet in hetzelfde abonnement als het batch-account bevindt, moet u [de resource provider Microsoft.BatCH registreren](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) voor dat abonnement. De twee abonnementen moeten zich in dezelfde Azure AD-Tenant bezitten.
>
> De installatie kopie kan zich in een andere regio bevinden, zolang deze replica's bevat in dezelfde regio als uw batch-account.

Als u een Azure AD-toepassing gebruikt om een aangepaste installatie kopie groep te maken met een installatie kopie van een gedeelde installatie kopie, moet aan die toepassing een [ingebouwde rol van Azure](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) zijn toegewezen waarmee deze toegang krijgt tot de gedeelde installatie kopie. U kunt deze toegang verlenen in de Azure Portal door te navigeren naar de gedeelde installatie kopie, **toegangs beheer (IAM)** te selecteren en een roltoewijzing voor de toepassing toe te voegen.

## <a name="prepare-a-shared-image"></a>Een gedeelde installatie kopie voorbereiden

In azure kunt u een gedeelde installatie kopie voorbereiden op basis van een beheerde installatie kopie, die kan worden gemaakt op basis van:

- Moment opnamen van het besturings systeem en de gegevens schijven van een virtuele machine van Azure
- Een gegeneraliseerde Azure-VM met beheerde schijven
- Een gegeneraliseerde on-premises VHD die is geüpload naar de Cloud

> [!NOTE]
> Op dit moment ondersteunt batch alleen gegeneraliseerde gedeelde installatie kopieën. U kunt op dit moment geen aangepaste installatie kopie groep maken van een speciale gedeelde installatie kopie.

De volgende stappen laten zien hoe u een virtuele machine voorbereidt, een moment opname maakt en een installatie kopie van de moment opname kunt maken.

### <a name="prepare-a-vm"></a>Een virtuele machine voorbereiden

Als u een nieuwe VM voor de installatie kopie maakt, gebruikt u een Azure Marketplace-installatie kopie van de eerste partij die door batch wordt ondersteund als basis installatie kopie voor uw beheerde installatie kopie. Alleen installatie kopieën van de eerste partij kunnen worden gebruikt als basis installatie kopie. Voor een volledige lijst met voor beelden van Azure Marketplace-installatie kopieën die door Azure Batch worden ondersteund, raadpleegt u de [List node agent sku's](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) -bewerking.

> [!NOTE]
> U kunt geen afbeelding van derden gebruiken die aanvullende licentie-en aankoop voorwaarden als uw basis installatie kopie heeft. Zie de richt lijnen voor [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) -of [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms) -vm's voor meer informatie over deze installatie kopieën voor Marketplace.

- Zorg ervoor dat de virtuele machine is gemaakt met een beheerde schijf. Dit is de standaard instelling voor opslag wanneer u een VM maakt.
- Installeer geen Azure-extensies, zoals de aangepaste script extensie, op de VM. Als de installatie kopie een vooraf geïnstalleerde extensie bevat, kan Azure problemen ondervinden bij het implementeren van de batch-pool.
- Wanneer u gekoppelde gegevens schijven gebruikt, moet u de schijven koppelen en Format teren vanuit een VM om ze te gebruiken.
- Zorg ervoor dat de basis installatie kopie van het besturings systeem dat u opgeeft, gebruikmaakt van het standaard tijdelijke station. De batch-knooppunt agent verwacht momenteel het standaard tijdelijke station.
- Zodra de virtuele machine wordt uitgevoerd, maakt u verbinding met de VM via RDP (voor Windows) of SSH (voor Linux). Installeer de benodigde software of kopieer de gewenste gegevens.  

### <a name="create-a-vm-snapshot"></a>Een VM-moment opname maken

Een moment opname is een volledige, alleen-lezen kopie van een VHD. Als u een moment opname van het besturings systeem of de gegevens schijven van een virtuele machine wilt maken, kunt u de Azure Portal of opdracht regel Programma's gebruiken. Zie de richt lijnen voor [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) -of [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) -vm's voor een overzicht van de stappen en opties voor het maken van een moment opname.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Een installatie kopie maken van een of meer moment opnamen

Als u een beheerde installatie kopie wilt maken op basis van een moment opname, gebruikt u de opdracht regel Programma's van Azure, zoals de opdracht [AZ image Create](/cli/azure/image) . Een installatie kopie maken door een moment opname van de besturingssysteem schijf en eventueel een of meer moment opnamen van de gegevens schijf op te geven.

### <a name="create-a-shared-image-gallery"></a>Een gedeelde installatiekopiegalerie maken

Wanneer u de beheerde installatie kopie hebt gemaakt, moet u een galerie met gedeelde afbeeldingen maken om uw aangepaste installatie kopie beschikbaar te maken. Zie [een galerie met gedeelde afbeeldingen maken met Azure cli](../virtual-machines/shared-images-cli.md) of [een galerie met gedeelde afbeeldingen maken met behulp van de Azure Portal](../virtual-machines/linux/shared-images-portal.md)voor meer informatie over het maken van een galerie met gedeelde installatie kopieën voor uw installatie kopieën.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Een groep maken op basis van een gedeelde installatie kopie met behulp van de Azure CLI

Als u een groep wilt maken op basis van uw gedeelde installatie kopie met behulp van de Azure CLI, gebruikt u de `az batch pool create` opdracht. Geef de ID van de gedeelde installatie kopie op in het `--image` veld. Zorg ervoor dat het type besturings systeem en de SKU overeenkomen met de versies die zijn opgegeven door `--node-agent-sku-id`

> [!NOTE]
> U moet zich verifiëren met Azure AD. Als u gedeelde-sleutel verificatie gebruikt, krijgt u een verificatie fout.  

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>Een groep maken op basis van een gedeelde installatie kopie met C #

U kunt ook een groep maken op basis van een gedeelde installatie kopie met behulp van de C#-SDK.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-python"></a>Een groep maken op basis van een gedeelde installatie kopie met behulp van python

U kunt ook een groep maken op basis van een gedeelde installatie kopie met behulp van de python-SDK: 

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch as batch
import azure.batch.models as batchmodels
from azure.common.credentials import ServicePrincipalCredentials

# Specify Batch account and service principal account credentials
account = "{batch-account-name}"
batch_url = "{batch-account-url}"
ad_client_id = "{sp-client-id}"
ad_tenant = "{tenant-id}"
ad_secret = "{sp-secret}"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client with Azure AD authentication
creds = ServicePrincipalCredentials(
    client_id=ad_client_id,
    secret=ad_secret,
    tenant=ad_tenant,
    resource="https://batch.core.windows.net/"
)
client = batch.BatchServiceClient(creds, batch_url)

# Configure the start task for the pool
start_task = batchmodels.StartTask(
    command_line="printenv AZ_BATCH_NODE_STARTUP_DIR"
)
start_task.run_elevated = True

# Create an ImageReference which specifies the image from
# Shared Image Gallery to install on the nodes.
ir = batchmodels.ImageReference(
    virtual_machine_image_id="/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}"
)

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent to
# be installed on the node.
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04"
)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(
    id=pool_id,
    vm_size=vm_size,
    target_dedicated_nodes=node_count,
    virtual_machine_configuration=vmc,
    start_task=start_task
)

# Create pool in the Batch service
client.pool.add(new_pool)
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Een groep maken op basis van een gedeelde installatie kopie met behulp van de Azure Portal

Gebruik de volgende stappen om een groep te maken op basis van een gedeelde installatie kopie in de Azure Portal.

1. Open de [Azure Portal](https://portal.azure.com).
1. Ga naar **batch-accounts** en selecteer uw account.
1. Selecteer **Pools** en voeg vervolgens **toe** om een nieuwe groep te maken.
1. Selecteer in de sectie **type installatie** kopie de **Galerie gedeelde installatie kopieën**.
1. Voltooi de resterende gedeelten met informatie over uw beheerde installatie kopie.
1. Selecteer **OK**.

![Maak een pool met behulp van een gedeelde installatie kopie met de portal.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>Overwegingen voor grote Pools

Als u van plan bent om een pool met honderden of duizenden Vm's of meer te maken met behulp van een gedeelde installatie kopie, gebruikt u de volgende richt lijnen.

- **Replica nummers van de galerie met gedeelde afbeeldingen.**  Voor elke groep met Maxi maal 300 exemplaren, raden we u aan ten minste één replica te gebruiken. Als u bijvoorbeeld een groep met 3000 Vm's maakt, moet u ten minste tien replica's van uw installatie kopie gebruiken. We suggereren altijd dat er meer replica's dan minimale vereisten zijn voor betere prestaties.

- **Grootte van time-out wijzigen.** Als uw pool een vast aantal knoop punten bevat (als deze niet automatisch wordt geschaald), verhoogt u de `resizeTimeout` eigenschap van de pool, afhankelijk van de grootte van de groep. Voor elke virtuele machine van 1000 is de aanbevolen grootte-time-out ten minste 15 minuten. Zo is de aanbevolen grootte voor een groep met 2000 Vm's ten minste 30 minuten.

## <a name="next-steps"></a>Volgende stappen

- Zie [batch service workflow en resources](batch-service-workflow-features.md)voor een uitgebreid overzicht van batch.
- Meer informatie over de [Galerie met gedeelde afbeeldingen](../virtual-machines/windows/shared-image-galleries.md).
