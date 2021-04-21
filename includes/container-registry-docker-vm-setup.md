---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: 429377cd50e83195cb1c3a422416fdb35644a28e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773460"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Een virtuele machine met Docker maken

Gebruik voor testdoeleinden een Ubuntu-VM met Docker-ondersteuning voor toegang tot een Azure-containerregister. Als u Azure Active Directory register wilt gebruiken, installeert u ook [de Azure CLI][azure-cli] op de VM. Als u al een virtuele Azure-machine hebt, kunt u deze stap overslaan.

U kunt dezelfde resourcegroep gebruiken voor uw virtuele machine en het containerregister. Deze installatie vereenvoudigt het ops schonen aan het einde, maar is niet vereist. Als u ervoor kiest om een afzonderlijke resourcegroep te maken voor de virtuele machine en het virtuele netwerk, voer [dan az group create uit.][az-group-create] In het volgende voorbeeld wordt ervan uitgenomen dat u omgevingsvariabelen hebt ingesteld voor de naam van de resourcegroep en de registerlocatie:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Implementeer nu een standaard virtuele Ubuntu Azure-machine [met az vm create][az-vm-create]. In het volgende voorbeeld wordt een VM met de *naam myDockerVM gemaakt.*

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt. Wanneer de opdracht is voltooid, noteert u de `publicIpAddress` die wordt weergegeven door de Azure CLI. Gebruik dit adres om SSH-verbindingen met de VM te maken.

### <a name="install-docker-on-the-vm"></a>Docker installeren op de VM

Nadat de VM wordt uitgevoerd, maakt u een SSH-verbinding met de VM. Vervang *publicIpAddress door* het openbare IP-adres van uw VM.

```bash
ssh azureuser@publicIpAddress
```

Voer de volgende opdrachten uit om Docker te installeren op de Ubuntu-VM:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Voer na de installatie de volgende opdracht uit om te controleren of Docker correct wordt uitgevoerd op de VM:

```bash
sudo docker run -it hello-world
```

Uitvoer:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure-CLI installeren

Volg de stappen in [Azure CLI installeren met apt om](/cli/azure/install-azure-cli-apt) de Azure CLI te installeren op uw virtuele Ubuntu-machine. Bijvoorbeeld:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Sluit de SSH-verbinding af.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-group-create]: /cli/azure/group