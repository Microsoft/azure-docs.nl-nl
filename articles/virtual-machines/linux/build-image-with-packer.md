---
title: Linux Azure VM-installatie kopieën maken met Packer
description: Meer informatie over het gebruik van Packer voor het maken van installatie kopieën van virtuele Linux-machines in azure
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: 4d85106cb78b5f4799a78d18463d83594f54dbac
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556737"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Hoe kan ik met behulp van Packer installatie kopieën voor virtuele Linux-machines maken in azure?
Elke virtuele machine (VM) in azure wordt gemaakt op basis van een installatie kopie die de Linux-distributie-en besturingssysteem versie definieert. Installatie kopieën kunnen vooraf geïnstalleerde toepassingen en configuraties bevatten. De Azure Marketplace biedt veel kopieën van de eerste en derde partij voor de meeste gang bare distributies en toepassings omgevingen, of u kunt uw eigen aangepaste installatie kopieën maken die zijn afgestemd op uw behoeften. In dit artikel wordt beschreven hoe u met behulp van de open source tool [Packer](https://www.packer.io/) aangepaste installatie kopieën in azure definieert en bouwt.

> [!NOTE]
> Azure heeft nu een service, Azure Image Builder (preview), voor het definiëren en maken van uw eigen aangepaste installatie kopieën. Azure Image Builder is gebaseerd op Packer, dus u kunt zelfs uw bestaande scripts voor de inrichtings functie van de pakket shell gebruiken. Zie [een virtuele Linux-machine met Azure Image Builder maken](image-builder.md)om aan de slag te gaan met Azure Image Builder.


## <a name="create-azure-resource-group"></a>Azure-resourcegroep maken
Tijdens het bouw proces maakt verpakker tijdelijke Azure-resources tijdens het maken van de bron-VM. Als u wilt dat de bron-VM als een installatie kopie wordt gebruikt, moet u een resource groep definiëren. De uitvoer van het pakket voor het maken van pakketten wordt opgeslagen in deze resource groep.

Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Azure-referenties maken
De verpakker wordt geverifieerd met Azure met behulp van een service-principal. Een Azure-Service-Principal is een beveiligings identiteit die u kunt gebruiken met apps, services en Automation-hulpprogram ma's, zoals Packer. U beheert en definieert de machtigingen voor de bewerkingen die de Service-Principal in azure kan uitvoeren.

Een service-principal maken met [AZ AD SP create-for-RBAC](/cli/azure/ad/sp) en de referenties uitvoeren die door de verpakker worden vereist:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Een voor beeld van de uitvoer van de voor gaande opdrachten is als volgt:

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Als u zich wilt verifiëren bij Azure, moet u ook uw Azure-abonnements-ID verkrijgen met [AZ account show](/cli/azure/account):

```azurecli
az account show --query "{ subscription_id: id }"
```

In de volgende stap gebruikt u de uitvoer van deze twee opdrachten.


## <a name="define-packer-template"></a>Pakket sjabloon definiëren
Als u installatie kopieën wilt maken, maakt u een sjabloon als een JSON-bestand. In de sjabloon definieert u bouwers en inrichtings die het werkelijke bouw proces uitvoeren. De verpakker heeft een [inrichtings functie voor Azure](https://www.packer.io/docs/builders/azure.html) waarmee u Azure-resources kunt definiëren, zoals de referenties van de service-principal die in de voor gaande stap zijn gemaakt.

Maak een bestand met de naam *ubuntu.jsop* en plak de volgende inhoud. Voer uw eigen waarden in voor het volgende:

| Parameter                           | Waar u kunt verkrijgen |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Eerste regel van uitvoer van de `az ad sp` opdracht Create- *AppID* |
| *client_secret*                     | Tweede regel van uitvoer van `az ad sp` opdracht- *wacht woord* maken |
| *tenant_id*                         | Derde regel van uitvoer van de `az ad sp` opdracht Create- *Tenant* |
| *subscription_id*                   | Uitvoer van `az account show` opdracht |
| *managed_image_resource_group_name* | De naam van de resource groep die u in de eerste stap hebt gemaakt |
| *managed_image_name*                | Naam voor de beheerde schijf installatie kopie die wordt gemaakt |


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

Met deze sjabloon maakt u een installatie kopie van Ubuntu 16,04 LTS, installeert u NGINX en maakt u de voorzieningen van de virtuele machine on.

> [!NOTE]
> Als u deze sjabloon uitbreidt om gebruikers referenties in te richten, past u de inrichtings opdracht aan waarmee de Azure-agent kan worden gelezen in `-deprovision` plaats van `deprovision+user` .
> `+user`Met de vlag verwijdert u alle gebruikers accounts van de bron-VM.


## <a name="build-packer-image"></a>Installatie kopie van Builder
Als u Packer nog niet op uw lokale computer hebt geïnstalleerd, [volgt u de installatie-instructies van de verpakking](https://www.packer.io/docs/install).

Bouw de installatie kopie door het pakket sjabloon bestand als volgt op te geven:

```bash
./packer build ubuntu.json
```

Een voor beeld van de uitvoer van de voor gaande opdrachten is als volgt:

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

Het duurt enkele minuten voordat de virtuele machine door de pakket functie wordt gebouwd, de inrichtingen worden uitgevoerd en de implementatie is opschoont.


## <a name="create-vm-from-azure-image"></a>Een VM maken op basis van een Azure-installatie kopie
U kunt nu een VM maken op basis van uw installatie kopie met [AZ VM Create](/cli/azure/vm). Geef de installatie kopie op die u hebt gemaakt met de `--image` para meter. In het volgende voor beeld wordt een virtuele machine met de naam *myVM* van *myPackerImage* gemaakt en worden SSH-sleutels gegenereerd als deze nog niet bestaan:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Als u Vm's wilt maken in een andere resource groep of regio dan uw pakket afbeelding, geeft u de installatie kopie-ID op in plaats van de naam van de installatie kopie. U kunt de afbeeldings-ID verkrijgen met [AZ Image Show](/cli/azure/image#az-image-show).

Het duurt een paar minuten om de virtuele machine te maken. Zodra de VM is gemaakt, noteert u de `publicIpAddress` weer gegeven door de Azure cli. Dit adres wordt gebruikt om toegang te krijgen tot de NGINX-site via een webbrowser.

Open poort 80 via internet met [az vm open-port](/cli/azure/vm) zodat beveiligd webverkeer uw virtuele machine kan bereiken:

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>VM en NGINX testen
Nu kunt u een webbrowser openen en `http://publicIpAddress` in de adresbalk invoeren. Geef uw eigen openbare IP-adres op uit het creatieproces van de virtuele machine proces. De standaard NGINX-pagina wordt weer gegeven, zoals in het volgende voor beeld:

![Standaardsite van NGINX](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Volgende stappen
U kunt ook bestaande pakket inrichtings scripts gebruiken met [Azure Image Builder](image-builder.md).
