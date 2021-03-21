---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: d699e8985a3a23b3aab87601d5298d9c8f7e34e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244676"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Een virtuele machine maken die is ingeschakeld voor docker

Gebruik voor test doeleinden een docker-Ubuntu-VM om toegang te krijgen tot een Azure container Registry. Als u Azure Active Directory verificatie wilt gebruiken voor het REGI ster, installeert u ook de [Azure cli][azure-cli] op de VM. Als u al een virtuele machine van Azure hebt, kunt u deze stap voor het maken overs Laan.

U kunt dezelfde resource groep gebruiken voor uw virtuele machine en het container register. Deze installatie vereenvoudigt aan het einde van het opschonen, maar is niet vereist. Als u ervoor kiest om een afzonderlijke resource groep te maken voor de virtuele machine en het virtuele netwerk, voert u [AZ Group Create][az-group-create]uit. In het volgende voor beeld wordt ervan uitgegaan dat u omgevings variabelen hebt ingesteld voor de naam van de resource groep en de register locatie:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Implementeer nu een standaard Ubuntu Azure-virtuele machine met [AZ VM Create][az-vm-create]. In het volgende voor beeld wordt een VM gemaakt met de naam *myDockerVM*.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt. Wanneer de opdracht is voltooid, noteert u de `publicIpAddress` weer gegeven door de Azure cli. Gebruik dit adres om SSH-verbindingen met de virtuele machine te maken.

### <a name="install-docker-on-the-vm"></a>Docker installeren op de VM

Nadat de virtuele machine is uitgevoerd, maakt u een SSH-verbinding met de VM. Vervang *publicIpAddress* door het open bare IP-adres van uw virtuele machine.

```bash
ssh azureuser@publicIpAddress
```

Voer de volgende opdrachten uit om docker te installeren op de Ubuntu-VM:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Na de installatie voert u de volgende opdracht uit om te controleren of docker correct wordt uitgevoerd op de VM:

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

Volg de stappen in [Azure cli installeren met apt](/cli/azure/install-azure-cli-apt) om de Azure CLI op uw virtuele Ubuntu-machine te installeren. Bijvoorbeeld:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Sluit de SSH-verbinding.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group