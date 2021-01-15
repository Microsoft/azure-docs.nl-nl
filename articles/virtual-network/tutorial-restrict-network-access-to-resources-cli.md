---
title: Netwerk toegang tot PaaS-resources beperken-Azure CLI
description: In dit artikel leert u hoe u netwerk toegang tot Azure-resources, zoals Azure Storage en Azure SQL Database, kunt beperken en beperken met Service-eind punten van virtuele netwerken met behulp van de Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: b4f9dcf6fd43749cdf71098b3cd0da8a42020f6d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221762"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Netwerk toegang tot PaaS-resources beperken met virtuele netwerk service-eind punten met behulp van de Azure CLI

Met service-eindpunten voor virtuele netwerken kunt u de netwerktoegang tot sommige Azure-servicebronnen beperken tot een subnet van een virtueel netwerk. U kunt ook internettoegang tot de resources verwijderen. Service-eindpunten zorgen voor een rechtstreekse verbinding van uw virtuele netwerk met ondersteunde Azure-services, zodat u de privéadresruimte van uw virtuele netwerk kunt gebruiken voor toegang tot de Azure-services. Verkeer dat bestemd is voor Azure-resources via de service-eindpunten blijft altijd op het Microsoft Azure-backbone-netwerk. In dit artikel leert u het volgende:

* Een virtueel netwerk maken met één subnet
* Een subnet toevoegen en een service-eindpunt inschakelen
* Een Azure-resource maken en alleen toegang ertoe toestaan vanaf een subnet
* Een virtuele machine (VM) implementeren op elk subnet
* Toegang tot een resource vanaf een subnet bevestigen
* Bevestigen dat toegang wordt geweigerd aan een resource vanaf een subnet en internet

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Voordat u een virtueel netwerk maakt, moet u een resource groep maken voor het virtuele netwerk en alle andere resources die in dit artikel zijn gemaakt. Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Maak een virtueel netwerk met één subnet met [AZ Network vnet Create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Een service-eindpunt inschakelen 

U kunt Service-eind punten alleen inschakelen voor services die service-eind punten ondersteunen. Bekijk Services voor eind punten die beschikbaar zijn op een Azure-locatie met [AZ Network vnet List-endpoint-Services](/cli/azure/network/vnet). In het volgende voor beeld wordt een lijst geretourneerd met services die zijn ingeschakeld voor service-eind punten die beschikbaar zijn in de regio *oostus* . De lijst met services die zijn geretourneerd, zal na verloop van tijd toenemen naarmate meer Azure-Services service-eind punten worden ingeschakeld.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Maak een extra subnet in het virtuele netwerk met [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet). In dit voor beeld wordt een service-eind punt voor *micro soft. Storage* gemaakt voor het subnet: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Netwerktoegang voor een subnet beperken

Maak een netwerk beveiligings groep met [AZ Network NSG Create](/cli/azure/network/nsg). In het volgende voor beeld wordt een netwerk beveiligings groep gemaakt met de naam *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Koppel de netwerk beveiligings groep aan het *persoonlijke* subnet met [AZ Network vnet subnet update](/cli/azure/network/vnet/subnet). In het volgende voor beeld wordt de *myNsgPrivate* -netwerk beveiligings groep gekoppeld aan het *privé* -subnet:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Maak beveiligings regels met [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule). De volgende regel geeft uitgaande toegang tot de open bare IP-adressen die zijn toegewezen aan de Azure Storage-service: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Elke netwerk beveiligings groep bevat verschillende [standaard beveiligings regels](./network-security-groups-overview.md#default-security-rules). De volgende regel overschrijft een standaard beveiligings regel die uitgaande toegang tot alle open bare IP-adressen toestaat. De `destination-address-prefix "Internet"` optie weigert uitgaande toegang tot alle open bare IP-adressen. De vorige regel overschrijft deze regel vanwege de hogere prioriteit, waardoor toegang tot de open bare IP-adressen van Azure Storage mogelijk is.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

Met de volgende regel wordt SSH-verkeer vanaf elke locatie binnenkomen via het subnet. De regel overschrijft een standaardbeveiligingsregel waardoor al het inkomende verkeer van internet wordt geweigerd. SSH is toegestaan voor het subnet zodat connectiviteit in een latere stap kan worden getest.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>Netwerktoegang tot een resource beperken

De stappen die nodig zijn om netwerktoegang te beperken tot resources die zijn gemaakt met Azure-services waarvoor service-eindpunten zijn ingeschakeld, verschillen per service. Zie de documentatie voor afzonderlijke services voor specifieke stappen voor elke service. De rest van dit artikel bevat stappen voor het beperken van de netwerk toegang voor een Azure Storage-account, zoals een voor beeld.

### <a name="create-a-storage-account"></a>Een opslagaccount maken

Maak een Azure-opslag account met [AZ Storage account create](/cli/azure/storage/account). Vervang door `<replace-with-your-unique-storage-account-name>` een naam die uniek is voor alle Azure-locaties, tussen 3-24 tekens lang, met alleen cijfers en kleine letters.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Nadat het opslag account is gemaakt, haalt u de connection string voor het opslag account op in een variabele met [AZ Storage account show-Connection-String](/cli/azure/storage/account). De connection string wordt gebruikt om een bestands share in een latere stap te maken.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Bekijk de inhoud van de variabele en noteer de waarde voor **AccountKey** die in de uitvoer is geretourneerd, omdat deze in een latere stap wordt gebruikt.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Een bestandsshare maken in het opslagaccount

Maak een bestands share in het opslag account met [AZ Storage share Create](/cli/azure/storage/share). In een latere stap is deze bestands share gekoppeld om de netwerk toegang te bevestigen.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Alle netwerk toegang tot een opslag account weigeren

Standaard accepteren opslagaccounts netwerkverbindingen van clients in ieder netwerk. Als u de toegang tot geselecteerde netwerken wilt beperken, wijzigt u de standaard actie voor *weigeren* met [AZ Storage account update](/cli/azure/storage/account). Nadat de netwerktoegang is geweigerd, is het opslagaccount niet via elk netwerk toegankelijk.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Netwerktoegang vanuit een subnet inschakelen

Netwerk toegang tot het opslag account via het *privé* -subnet toestaan met [AZ Storage account netwerk-Rule add](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Virtuele machines maken

Implementeer een VM in elk subnet om de netwerktoegang tot een opslagaccount te testen.

### <a name="create-the-first-virtual-machine"></a>De eerste virtuele machine maken

Maak een virtuele machine in het *open bare* subnet met [AZ VM Create](/cli/azure/vm). Als SSH-sleutels niet al bestaan op de standaardlocatie van de sleutel, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

Het maken van de virtuele machine duurt een paar minuten. Nadat de VM is gemaakt, toont de Azure CLI informatie die lijkt op het volgende voor beeld: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Noteer de **publicIpAddress** in de geretourneerde uitvoer. Dit adres wordt gebruikt voor toegang tot de virtuele machine via internet in een latere stap.

### <a name="create-the-second-virtual-machine"></a>De tweede virtuele machine maken

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

Het maken van de virtuele machine duurt een paar minuten. Na het maken noteert u de **publicIpAddress** in de uitvoer die wordt geretourneerd. Dit adres wordt gebruikt voor toegang tot de virtuele machine via internet in een latere stap.

## <a name="confirm-access-to-storage-account"></a>Toegang tot opslagaccount bevestigen

SSH in de *VM myvmprivate* -VM. Vervang door *\<publicIpAddress>* het open bare IP-adres van uw *VM myvmprivate* -VM.

```bash 
ssh <publicIpAddress>
```

Maak een map voor een koppel punt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Koppel de Azure-bestands share aan de map die u hebt gemaakt. Voordat u de volgende opdracht uitvoert, vervangt `<storage-account-name>` u door de account naam en `<storage-account-key>` de sleutel die u hebt opgehaald in [een opslag account maken](#create-a-storage-account).

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

U ontvangt de `user@myVmPrivate:~$` prompt. De Azure-bestands share is gekoppeld aan */mnt/MyAzureFileShare*.

Controleer of de virtuele machine geen uitgaande verbinding heeft met andere open bare IP-adressen:

```bash
ping bing.com -c 4
```

U krijgt geen antwoorden, omdat de netwerkbeveiligingsgroep die is gekoppeld aan het *Privé*-subnet geen uitgaande toegang toestaat aan andere openbare IP-adressen dan de adressen die zijn toegewezen aan de Azure Storage-service.

Sluit de SSH-sessie af op de *VM myvmprivate* -VM.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bevestigen dat toegang tot opslagaccount wordt geweigerd

Gebruik de volgende opdracht om een SSH-sessie te maken met de *VM myvmpublic* -VM. Vervang door `<publicIpAddress>` het open bare IP-adres van uw *VM myvmpublic* -VM: 

```bash 
ssh <publicIpAddress>
```

Maak een map voor een koppel punt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Probeer de Azure-bestands share te koppelen aan de map die u hebt gemaakt. In dit artikel wordt ervan uitgegaan dat u de meest recente versie van Ubuntu hebt geïmplementeerd. Als u een eerdere versie van Ubuntu gebruikt, raadpleegt u [Mount on Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor aanvullende instructies over het koppelen van bestands shares. Voordat u de volgende opdracht uitvoert, vervangt `<storage-account-name>` u door de account naam en `<storage-account-key>` de sleutel die u hebt opgehaald in [een opslag account maken](#create-a-storage-account):

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

De toegang wordt geweigerd en er wordt een fout bericht weer gegeven `mount error(13): Permission denied` omdat de *VM myvmpublic* -vm in het *open bare* subnet wordt geïmplementeerd. Het *Openbare* subnet heeft geen service-eindpunt ingeschakeld voor Azure Storage en het opslagaccount staat alleen netwerktoegang toe van het *Privé*-subnet, en niet van het *Openbare* subnet.

Sluit de SSH-sessie af op de *VM myvmpublic* -VM.

Probeer vanaf uw computer de shares in uw opslag account te bekijken met de [lijst AZ Storage share](/cli/azure/storage/share?view=azure-cli-latest). Vervang `<account-name>` en door `<account-key>` de naam en sleutel van het opslag account van [een opslag account maken](#create-a-storage-account):

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

De toegang wordt geweigerd en u ontvangt een *aanvraag die niet is gemachtigd om deze bewerkings fout uit te voeren* omdat uw computer zich niet in het *particuliere* subnet van het virtuele netwerk *MyVirtualNetwork* bevindt.

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik [AZ Group delete](/cli/azure) om de resource groep en alle resources die deze bevat te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een service-eind punt voor een subnet van een virtueel netwerk ingeschakeld. U hebt geleerd dat service-eindpunten kunnen worden ingeschakeld voor met meerdere Azure-services geïmplementeerde resources. U hebt een Azure Storage-account gemaakt en netwerktoegang tot het opslagaccount beperkt tot alleen resources binnen een subnet van een virtueel netwerk. Zie voor meer informatie over service-eindpunten [Overzicht voor service-eindpunten](virtual-network-service-endpoints-overview.md) en [Subnetten beheren](virtual-network-manage-subnet.md).

Als u meerdere virtuele netwerken in uw account hebt, kunt u twee virtuele netwerken met elkaar verbinden zodat de resources in beide virtuele netwerken met elkaar kunnen communiceren. Zie [verbinding maken met virtuele netwerken](tutorial-connect-virtual-networks-cli.md)voor meer informatie.