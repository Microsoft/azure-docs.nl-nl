---
title: Een NAT-gateway maken - Azure CLI
titlesuffix: Azure Virtual Network NAT
description: In deze quickstart leert u hoe u een NAT-gateway kunt maken met Azure CLI
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d505c8f363e908a43c751c6253ed0f3a14b3b51
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671554"
---
# <a name="create-a-nat-gateway-using-azure-cli"></a>Een NAT-gateway maken met Azure CLI

In deze zelfstudie wordt uitgelegd hoe u de Azure Virtual Network NAT-service gebruikt. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.71 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myResourceGroupNAT** gemaakt op de locatie **eastus2**:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Voor toegang tot het openbare internet hebt u een of meer openbare IP-adressen nodig voor de NAT-gateway. Gebruik [az network public-ip create](/cli/azure/network/public-ip) om in **myResourceGroupNAT** een openbare IP-resource te maken met de naam **myPublicIP**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard
```

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voorvoegsel maken

U kunt een of meer openbare IP-resources, openbare IP-voorvoegsels of beide met NAT-gateway gebruiken. Om dit te illustreren, voegt u een resource voor het openbare IP-voorvoegsel aan dit scenario toe. Gebruik [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-create) om in **myResourceGroupNAT** een resource voor het openbare IP-voorvoegsel te maken met de naam **myPublicIPprefix**.

```azurecli-interactive
  az network public-ip prefix create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPprefix \
    --length 31
```

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gatewayresource maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service met de NAT-gatewayresource kunt maken en configureren:
  - Een openbare IP-adresgroep en een openbaar IP-voorvoegsel die moeten worden gebruikt voor uitgaande stromen die worden vertaald door de NAT-gatewayresource.
  - Wijzig de time-out voor inactiviteit van de standaardwaarde van 4 minuten naar 10 minuten.

Maak met [az network nat gateway create](/cli/azure/network/nat) een globaal Azure NAT-gateway met de naam **myNATgateway**. De opdracht gebruikt zowel het openbare IP-adres **myPublicIP** als het openbare IP-voorvoegsel **myPublicIPprefix**. De opdracht verandert de time-out voor inactiviteit in **10** minuten.

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --public-ip-prefixes myPublicIPprefix \
    --idle-timeout 10       
  ```

Nu is de NAT-gateway functioneel. Het enige wat u nog hoeft te doen is te configureren welke subnetten van een virtueel netwerk er gebruik van moeten maken.

## <a name="configure-virtual-network"></a>Virtueel netwerk configureren

Voordat u een virtuele machine implementeert en uw NAT-gateway kunt gebruiken, moet u het virtuele netwerk maken.

Maak met [az network vnet create](/cli/azure/network/vnet) in **myResourceGroupNAT** een virtueel netwerk met de naam **myVnet** met een subnet met de naam **mySubnet**.  De IP-adresruimte voor het virtuele netwerk is **192.168.0.0/16**. Het subnet binnen het virtuele netwerk is **192.168.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.0.0/24
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT-service voor bronsubnet configureren

Configureer het bronsubnet **mySubnet** in het virtuele netwerk **myVnet** om een specifieke NAT-gatewayresource **myNATgateway** te gebruiken met [az network vnet subnet update](/cli/azure/network/vnet/subnet).  Met deze opdracht wordt de NAT-service op het opgegeven subnet geactiveerd.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Al het uitgaande verkeer naar Internetdoelen maakt nu gebruik van de NAT-gateway.  Het is niet nodig om een UDR te configureren.

## <a name="create-a-vm-to-use-the-nat-service"></a>Een VM maken voor het gebruik van de NAT-service

Nu gaat u een VM maken voor het gebruik van de NAT-service.  Deze VM heeft een openbaar IP-adres dat kan worden gebruikt als een openbaar IP-adres op exemplaarniveau om toegang te krijgen tot de virtuele machine.  De NAT-service neemt stroomrichting waar en vervangt de standaard internetbestemming in uw subnet. Het openbare IP-adres van de virtuele machine wordt niet gebruikt voor uitgaande verbindingen.

### <a name="create-public-ip-for-source-vm"></a>Een openbare IP voor de bron-VM maken

U maakt een openbare IP die gaat worden gebruikt voor toegang tot de VM.  Gebruik [az network public-ip create](/cli/azure/network/public-ip) om in **myResourceGroupNAT** een openbare IP-resource te maken met de naam **myPublicIPVM**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --sku standard
```

### <a name="create-an-nsg-for-vm"></a>Een NSG maken voor VM

Omdat standaard openbare IP-adressen een 'standaardbeveiliging' vormen, moet u een NSG maken om inkomende toegang voor SSH-toegang toe te staan. Gebruik [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) om in **myResourceGroupNAT** een NSG-resource met de naam **myNSG** te maken.

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupNAT \
    --name myNSG 
```

### <a name="expose-ssh-endpoint-on-source-vm"></a>SSH-eindpunt beschikbaar maken op de bron-VM

U maakt een regel in de NSG voor SSH-toegang tot de bron-VM. Gebruik [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) om een NSG-regel met de naam **ssh** te maken in de NSG met de naam **myNSG** in **myResourceGroupNAT**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupNAT \
    --nsg-name myNSG \
    --priority 100 \
    --name ssh \
    --description "SSH access" \
    --access allow \
    --protocol tcp \
    --direction inbound \
    --destination-port-ranges 22
```

### <a name="create-nic-for-vm"></a>NIC maken voor VM

Maak met [az network nic create](/cli/azure/network/nic#az-network-nic-create) een netwerkinterface en koppel deze aan het openbare IP-adres en de netwerkbeveiligingsgroep. 

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupNAT \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIPVM \
    --network-security-group myNSG
```

### <a name="create-vm"></a>VM maken

Maak de virtuele machine met [az vm create](/cli/azure/vm#az-vm-create).  U genereert SSH-sleutels voor deze VM en slaat de persoonlijke sleutel op voor later gebruik.

 ```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupNAT \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Wacht tot de virtuele machine is geïmplementeerd en ga vervolgens verder met de overige stappen.

## <a name="discover-the-ip-address-of-the-vm"></a>Het IP-adres van de VM ontdekken

Eerst moet het IP-adres van de VM die u hebt gemaakt, worden gedetecteerd. Gebruik [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) om het openbare IP-adres van de VM op te halen. 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupNAT \
    --name myPublicIPVM \
    --query [ipAddress] \
    --output tsv
``` 

>[!IMPORTANT]
>Kopieer de openbare IP en plak het in een Kladblok, zodat u het kunt gebruiken om toegang te krijgen tot de VM.

### <a name="sign-in-to-vm"></a>Aanmelden bij VM

De SSH-aanmeldingsgegevens moeten worden opgeslagen in uw Cloud Shell van de vorige bewerking.  Open een [Azure Cloud Shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine uit te voeren.

```bash
ssh <ip-address-destination>
```

U bent nu klaar om de NAT-service te gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep en alle gerelateerde resources daarin te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een NAT-gateway gemaakt en een VM om de gateway te gebruiken. 

Controleer de metrische gegevens in Azure Monitor om uw NAT-service weer te geven. Problemen vaststellen, zoals de resource-uitputting van de beschikbare SNAT-poorten.  Resource-uitputting van de SNAT-poorten kan worden verholpen door extra openbare IP-adresresources of openbare IP-voorvoegselresources of beide toe te voegen.


- Meer informatie over [Azure Virtual Network NAT](./nat-overview.md)
- Meer informatie over [NAT-gatewayresource](./nat-gateway-resource.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met behulp van Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quickstart voor het implementeren van [NAT-gatewayresource met Azure Portal](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]
