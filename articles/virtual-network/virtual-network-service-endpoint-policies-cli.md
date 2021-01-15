---
title: Data exfiltration beperken tot Azure Storage-Azure CLI
description: In dit artikel leert u hoe u de exfiltration van virtuele netwerken kunt beperken en beperken tot Azure Storage resources met een virtueel netwerk service-eindpunt beleid met behulp van de Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 9ce1e320a93a834a938ce95f3931d885d2214faa
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216866"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Gegevens exfiltration beheren om accounts te Azure Storage met het eindpunt beleid van een virtueel netwerk met behulp van de Azure CLI

Met het eindpunt beleid van de virtuele netwerk service kunt u toegangs beheer Toep assen op Azure Storage accounts vanuit een virtueel netwerk via service-eind punten. Dit is een sleutel voor het beveiligen van uw workloads, het beheren van de opslag accounts die zijn toegestaan en het toestaan van gegevens exfiltration.
In dit artikel leert u het volgende:

* Maak een virtueel netwerk en voeg een subnet toe.
* Service-eind punt inschakelen voor Azure Storage.
* Maak twee Azure Storage accounts en sta netwerk toegang toe vanuit het hierboven gemaakte subnet.
* Maak een service-eindpunt beleid om toegang tot een van de opslag accounts toe te staan.
* Een virtuele machine (VM) implementeren in het subnet.
* Controleer de toegang tot het toegestane opslag account vanuit het subnet.
* Controleren of de toegang is geweigerd voor het niet-toegestane opslag account van het subnet.

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
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Een service-eindpunt inschakelen 

In dit voor beeld wordt een service-eind punt voor *micro soft. Storage* gemaakt voor het subnet *persoonlijk*: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
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

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Netwerk toegang tot Azure Storage accounts beperken

In deze sectie vindt u de stappen voor het beperken van de netwerk toegang voor een Azure Storage account vanuit het opgegeven subnet in een virtueel netwerk via service-eind punt.

### <a name="create-a-storage-account"></a>Een opslagaccount maken

Maak twee Azure-opslag accounts met [AZ Storage account create](/cli/azure/storage/account).

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Nadat de opslag accounts zijn gemaakt, haalt u de connection string voor de opslag accounts op in een variabele met [AZ Storage account show-Connection-String](/cli/azure/storage/account). De connection string wordt gebruikt om een bestands share in een latere stap te maken.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Bekijk de inhoud van de variabele en noteer de waarde voor **AccountKey** die in de uitvoer is geretourneerd, omdat deze in een latere stap wordt gebruikt.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Een bestandsshare maken in het opslagaccount

Maak een bestands share in het opslag account met [AZ Storage share Create](/cli/azure/storage/share). In een latere stap is deze bestands share gekoppeld om de netwerk toegang te bevestigen.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Alle netwerk toegang tot het opslag account weigeren

Standaard accepteren opslagaccounts netwerkverbindingen van clients in ieder netwerk. Als u de toegang tot geselecteerde netwerken wilt beperken, wijzigt u de standaard actie voor *weigeren* met [AZ Storage account update](/cli/azure/storage/account). Nadat de netwerktoegang is geweigerd, is het opslagaccount niet via elk netwerk toegankelijk.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Netwerk toegang vanaf subnet van virtueel netwerk inschakelen

Netwerk toegang tot het opslag account via het *privé* -subnet toestaan met [AZ Storage account netwerk-Rule add](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Beleid Toep assen om toegang tot een geldig opslag account toe te staan

Azure service Endpoint-beleids regels zijn alleen beschikbaar voor Azure Storage. Daarom wordt service-eind punt voor *micro soft. Storage* op dit subnet ingeschakeld voor dit voor beeld van Setup.

Service-eindpunt beleid wordt toegepast via service-eind punten. We gaan eerst een service-eindpunt beleid maken. Vervolgens worden de beleids definities in dit beleid gemaakt voor Azure Storage accounts die moeten worden goedgekeurd voor dit subnet

Een service-eindpunt beleid maken

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Sla de resource-URI op voor het toegestane opslag account in een variabele. Voordat u de onderstaande opdracht uitvoert, vervangt *\<your-subscription-id>* u door de werkelijke waarde van uw abonnements-id.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Maak & een beleids definitie toevoegen voor het toestaan van het bovenstaande Azure Storage account aan het service-eindpunt beleid

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

En werk het subnet van het virtuele netwerk bij om het te koppelen aan het service-eindpunt beleid dat u in de vorige stap hebt gemaakt

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Toegangs beperking voor Azure Storage accounts valideren

### <a name="create-the-virtual-machine"></a>De virtuele machine maken

Als u de netwerk toegang tot een opslag account wilt testen, implementeert u een virtuele machine naar het subnet.

Maak een virtuele machine in het *privé* -subnet met [AZ VM Create](/cli/azure/vm). Als SSH-sleutels niet al bestaan op de standaardlocatie van de sleutel, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.

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

### <a name="confirm-access-to-storage-account"></a>Toegang tot opslagaccount bevestigen

SSH in de *VM myvmprivate* -VM. Vervang door *\<publicIpAddress>* het open bare IP-adres van uw *VM myvmprivate* -VM.

```bash 
ssh <publicIpAddress>
```

Maak een map voor een koppel punt:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Koppel de Azure-bestands share aan de map die u hebt gemaakt. Voordat u de onderstaande opdracht uitvoert, vervangt u door de *\<storage-account-key>* waarde *AccountKey* uit **$saConnectionString 1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

U ontvangt de `user@myVmPrivate:~$` prompt. De Azure-bestands share is gekoppeld aan */mnt/MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Bevestigen dat toegang tot opslagaccount wordt geweigerd

Maak vanuit dezelfde VM- *VM myvmprivate* een map voor een koppel punt:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Probeer de Azure-bestands share te koppelen aan de map die u hebt gemaakt in het opslag account *notallowedstorageacc* . In dit artikel wordt ervan uitgegaan dat u de meest recente versie van Ubuntu hebt geïmplementeerd. Als u een eerdere versie van Ubuntu gebruikt, raadpleegt u [Mount on Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor aanvullende instructies over het koppelen van bestands shares. 

Voordat u de onderstaande opdracht uitvoert, vervangt u door de *\<storage-account-key>* waarde *AccountKey* uit **$saConnectionString 2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

De toegang wordt geweigerd en er wordt een fout bericht weer gegeven `mount error(13): Permission denied` , omdat dit opslag account zich niet in de lijst met toegestane services van het service-eindpunt beleid bevindt dat we op het subnet hebben toegepast. 

Sluit de SSH-sessie af op de *VM myvmpublic* -VM.

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik [AZ Group delete](/cli/azure) om de resource groep en alle resources die deze bevat te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een service-eindpunt beleid toegepast op een Azure Virtual Network-Service-eind punt naar Azure Storage. U hebt Azure Storage accounts en beperkte netwerk toegang tot alleen bepaalde opslag accounts gemaakt (en dus ook andere) van een subnet van een virtueel netwerk. Zie voor meer informatie over service-eindpunt beleid [overzicht van service-eind punten](virtual-network-service-endpoint-policies-overview.md).