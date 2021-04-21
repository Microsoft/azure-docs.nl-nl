---
title: Linux Azure VM-afbeeldingen maken met Packer
description: Meer informatie over het gebruik van Packer voor het maken van afbeeldingen van virtuele Linux-machines in Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: 1b40646109265b803945b43d7cc855688c5b47c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764653"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Packer gebruiken om virtuele Linux-machine-afbeeldingen te maken in Azure
Elke virtuele machine (VM) in Azure wordt gemaakt van een afbeelding die de Linux-distributie en de versie van het besturingssysteem definieert. Installatieafbeeldingen kunnen vooraf geïnstalleerde toepassingen en configuraties bevatten. De Azure Marketplace biedt veel eerste en externe afbeeldingen voor de meest voorkomende distributies en toepassingsomgevingen, of u kunt uw eigen aangepaste afbeeldingen maken die zijn afgestemd op uw behoeften. In dit artikel wordt beschreven hoe u het open source [Packer gebruikt om](https://www.packer.io/) aangepaste afbeeldingen in Azure te definiëren en te bouwen.

> [!NOTE]
> Azure heeft nu een service, Azure Image Builder (preview), voor het definiëren en maken van uw eigen aangepaste afbeeldingen. Azure Image Builder is gebouwd op Packer, zodat u er zelfs uw bestaande Packer Shell Provisioner-scripts mee kunt gebruiken. Zie Een virtuele Linux-Image Builder maken met Azure Image Builder om aan de slag te gaan met [Azure Image Builder.](image-builder.md)


## <a name="create-azure-resource-group"></a>Azure-resourcegroep maken
Tijdens het bouwproces maakt Packer tijdelijke Azure-resources tijdens het bouwen van de bron-VM. Als u die bron-VM wilt vastleggen voor gebruik als een afbeelding, moet u een resourcegroep definiëren. De uitvoer van het Packer-buildproces wordt opgeslagen in deze resourcegroep.

Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Azure-referenties maken
Packer wordt geverifieerd bij Azure met behulp van een service-principal. Een Azure-service-principal is een beveiligingsidentiteit die u kunt gebruiken met apps, services en automatiseringshulpprogramma's zoals Packer. U bepaalt en definieert de machtigingen voor de bewerkingen die de service-principal in Azure kan uitvoeren.

Maak een service-principal [met az ad sp create-for-rbac](/cli/azure/ad/sp) en uitvoer de referenties die Packer nodig heeft:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Een voorbeeld van de uitvoer van de voorgaande opdrachten is als volgt:

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Als u zich wilt verifiëren bij Azure, moet u ook uw Azure-abonnements-id verkrijgen [met az account show](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

In de volgende stap gebruikt u de uitvoer van deze twee opdrachten.


## <a name="define-packer-template"></a>Packer-sjabloon definiëren
Als u afbeeldingen wilt maken, maakt u een sjabloon als een JSON-bestand. In de sjabloon definieert u opbouwers en inrichtingsmakers die het daadwerkelijke bouwproces uitvoeren. Packer heeft een [inrichtingsservice](https://www.packer.io/docs/builders/azure.html) voor Azure waarmee u Azure-resources kunt definiëren, zoals de referenties voor de service-principal die u in de vorige stap hebt gemaakt.

Maak een bestand met *ubuntu.jsen* plak de volgende inhoud. Voer uw eigen waarden in voor het volgende:

| Parameter                           | Waar u kunt verkrijgen |
|-------------------------------------|----------------------------------------------------|
| *Client_id*                         | Eerste uitvoerregel van `az ad sp` de opdracht create - *appId* |
| *client_secret*                     | Tweede uitvoerregel van `az ad sp` opdracht maken - *wachtwoord* |
| *tenant_id*                         | Derde uitvoerregel van `az ad sp` de opdracht create - *tenant* |
| *subscription_id*                   | Uitvoer van `az account show` opdracht |
| *managed_image_resource_group_name* | Naam van de resourcegroep die u in de eerste stap hebt gemaakt |
| *managed_image_name*                | Naam voor de beheerde schijf die wordt gemaakt |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Met deze sjabloon wordt een Ubuntu 16.04 LTS-installatieprogramma gemaakt, wordt NGINX geïnstalleerd en wordt de installatie van de VM verwijderd.

> [!NOTE]
> Als u deze sjabloon uitbreidt voor het inrichten van gebruikersreferenties, past u de provisioner-opdracht aan die de inrichting van de Azure-agent voor lezen in plaats van `-deprovision` `deprovision+user` maakt.
> Met `+user` de vlag worden alle gebruikersaccounts van de bron-VM verwijderd.


## <a name="build-packer-image"></a>Build Packer-afbeelding
Als u Packer nog niet op uw lokale computer hebt geïnstalleerd, volgt [u de installatie-instructies van Packer.](https://www.packer.io/docs/install)

Bouw de afbeelding door uw Packer-sjabloonbestand als volgt op te geven:

```bash
./packer build ubuntu.json
```

Een voorbeeld van de uitvoer van de voorgaande opdrachten is als volgt:

```output
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Het duurt enkele minuten voordat Packer de VM heeft gebouwd, de inrichtingsmachines heeft uitgevoerd en de implementatie heeft opsschoond.


## <a name="create-vm-from-azure-image"></a>VM maken van Azure-afbeelding
U kunt nu een VM maken van uw afbeelding met [az vm create.](/cli/azure/vm) Geef de afbeelding op die u hebt gemaakt met de `--image` parameter . In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt vanuit *myPackerImage* en worden SSH-sleutels gegenereerd als deze nog niet bestaan:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Als u VM's wilt maken in een andere resourcegroep of regio dan uw Packer-afbeelding, geeft u de afbeeldings-id op in plaats van de naam van de afbeelding. U kunt de id van de afbeelding verkrijgen met [az image show](/cli/azure/image#az_image_show).

Het duurt een paar minuten om de virtuele machine te maken. Zodra de VM is gemaakt, noteer dan de die `publicIpAddress` wordt weergegeven door de Azure CLI. Dit adres wordt gebruikt voor toegang tot de NGINX-site via een webbrowser.

Open poort 80 via internet met [az vm open-port](/cli/azure/vm) zodat beveiligd webverkeer uw virtuele machine kan bereiken:

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>VM en NGINX testen
Nu kunt u een webbrowser openen en `http://publicIpAddress` in de adresbalk invoeren. Geef uw eigen openbare IP-adres op uit het creatieproces van de virtuele machine proces. De standaard NGINX-pagina wordt weergegeven zoals in het volgende voorbeeld:

![Standaardsite van NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Volgende stappen
U kunt ook bestaande Packer-inrichtingsscripts gebruiken met [Azure Image Builder.](image-builder.md)
