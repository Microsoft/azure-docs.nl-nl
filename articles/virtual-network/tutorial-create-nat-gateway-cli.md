---
title: 'Zelfstudie: Een NAT-gateway maken - Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Aan de slag met het maken van een NAT-gateway met behulp van Azure CLI.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 5dd431a5a7377c409be0794511c5f402d1c5a3a9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102663275"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Zelf studie: een NAT-gateway maken met behulp van de Azure CLI

In deze zelfstudie wordt uitgelegd hoe u de Azure Virtual Network NAT-service gebruikt. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een virtueel netwerk.
> * Hiermee maakt u een virtuele machine.
> * Maak een NAT-gateway en koppel deze aan het virtuele netwerk.
> * Maak verbinding met de virtuele machine en controleer IP-adres van NAT.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor deze quickstart is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep maken met [az group create](/cli/azure/group#az_group_create). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam **myResourceGroupNAT** gemaakt op de locatie **eastus2**:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

In deze sectie maken we de NAT-gateway en ondersteunende bronnen.

### <a name="create-public-ip-address"></a>Openbaar IP-adres maken

Voor toegang tot het internet hebt u een of meer openbare IP-adressen nodig voor de NAT-gateway. Gebruik [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) om in **myResourceGroupNAT** een openbare IP-resource te maken met de naam **myPublicIP**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>NAT-gateway resource maken

Maak een globale Azure NAT-gateway met [AZ Network NAT gateway Create](/cli/azure/network/nat#az_network_nat_gateway_create). Als gevolg van deze opdracht maakt u een gateway resource met de naam **myNATgateway** die gebruikmaakt van het open bare IP-adres **myPublicIP**. De time-out voor inactiviteit is ingesteld op 10 minuten.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Virtueel netwerk maken

Maak een virtueel netwerk met de naam **myVnet** met een subnet met de naam **mySubnet** [AZ Network vnet Create](/cli/azure/network/vnet#az_network_vnet_create) in de resource groep **myResourceGroup** . De IP-adres ruimte voor het virtuele netwerk is **10.1.0.0/16**. Het subnet in het virtuele netwerk is **10.1.0.0/24**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>Bastion-host maken

Maak een Azure bastion-host met de naam **myBastionHost** om toegang te krijgen tot de virtuele machine. 

Gebruik [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) om een Azure Bastion-subnet te maken.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

Maak een openbaar IP-adres voor de bastion-host met [AZ Network Public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create). 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

Gebruik [AZ Network Bastion Create](/cli/azure/network/bastion#az-network-bastion-create) om de bastion-host te maken. 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>NAT-service voor bronsubnet configureren

Configureer het bronsubnet **mySubnet** in het virtuele netwerk **myVnet** om een specifieke NAT-gatewayresource **myNATgateway** te gebruiken met [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update). Met deze opdracht wordt de NAT-service op het opgegeven subnet geactiveerd.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Al het uitgaande verkeer naar Internetdoelen maakt nu gebruik van de NAT-gateway.  Het is niet nodig om een UDR te configureren.


## <a name="virtual-machine"></a>Virtuele machine

In deze sectie maakt u een virtuele machine om de NAT-gateway te testen om het open bare IP-adres van de uitgaande verbinding te controleren.

Maak de virtuele machine met [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive
az vm create \
    --name myVM \
    --resource-group myResourceGroupNAT  \
    --admin-username azureuser \
    --image win2019datacenter \
    --public-ip-address "" \
    --subnet mySubnet \
    --vnet-name myVNet
```

Wacht tot het maken van de virtuele machine is voltooid voordat u verdergaat met de volgende sectie.

## <a name="test-nat-gateway"></a>NAT-gateway testen

In deze sectie testen we de NAT-gateway. Eerst wordt het open bare IP-adres van de NAT-gateway gedetecteerd. Vervolgens maakt u verbinding met de virtuele test machine en controleert u de uitgaande verbinding via de NAT-gateway.
    
1. Meld u aan bij [Azure Portal](https://portal.azure.com)

1. Zoek het open bare IP-adres voor de NAT-gateway op het scherm **overzicht** . Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myPublicIP**.

2. Noteer het open bare IP-adres:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Openbaar IP-adres van NAT-gateway detecteren" border="true":::

3. Selecteer **alle services** in het linkermenu, selecteer **alle resources** en selecteer vervolgens in de lijst met resources **myVM** die zich in de resource groep **myResourceGroupNAT** bevindt.

4. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** en daarna **Bastion**.

5. Selecteer de blauwe knop **Bastion gebruiken**.

6. Voer de gebruikersnaam en het wachtwoord in die zijn ingevoerd tijdens het maken van de VM.

7. Open **Internet Explorer** op **myTestVM**.

8. Voer **https://whatsmyip.com** in de adres balk in.

9. Controleer of het weer gegeven IP-adres overeenkomt met het NAT-gateway adres dat u in de vorige stap hebt genoteerd:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer met het externe uitgaande IP-adres" border="true":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken, verwijdert u het virtuele netwerk, de virtuele machine en de NAT-gateway door de volgende stappen uit te voeren:

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Azure Virtual Network NAT:
> [!div class="nextstepaction"]
> [Overzicht van Virtual Network NAT](nat-overview.md)
