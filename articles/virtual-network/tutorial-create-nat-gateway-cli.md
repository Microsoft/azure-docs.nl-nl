---
title: 'Zelfstudie: Een NAT-gateway maken - Azure CLI'
titlesuffix: Azure Virtual Network NAT
description: Aan de slag met het maken van een NAT-gateway met behulp van de Azure CLI.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: d312702f441cfe2ad94e347cadcdfc88d4cc2a72
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479318"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Zelfstudie: Een NAT-gateway maken met de Azure CLI

In deze zelfstudie wordt uitgelegd hoe u de Azure Virtual Network NAT-service gebruikt. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een virtueel netwerk.
> * Hiermee maakt u een virtuele machine.
> * Maak een NAT-gateway en koppel deze aan het virtuele netwerk.
> * Maak verbinding met de virtuele machine en controleer het NAT IP-adres.

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

In deze sectie maken we de NAT-gateway en ondersteunende resources.

### <a name="create-public-ip-address"></a>Openbaar IP-adres maken

Voor toegang tot het internet hebt u een of meer openbare IP-adressen nodig voor de NAT-gateway. Gebruik [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) om in **myResourceGroupNAT** een openbare IP-resource te maken met de naam **myPublicIP**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>NAT-gatewayresource maken

Maak een globale Azure NAT-gateway met [az network nat gateway create](/cli/azure/network/nat#az_network_nat_gateway_create). Met deze opdracht maakt u een gatewayresource met de **naam myNATgateway** die gebruikmaakt van het openbare IP-adres **myPublicIP.** De time-out voor inactiviteit is ingesteld op 10 minuten.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Virtueel netwerk maken

Maak een virtueel netwerk met de **naam myVnet** met een subnet met de naam **mySubnet** [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) in de resourcegroep **myResourceGroup.** De IP-adresruimte voor het virtuele netwerk is **10.1.0.0/16.** Het subnet binnen het virtuele netwerk is **10.1.0.0/24.**

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

Maak een Azure Bastion met de **naam myBastionHost voor** toegang tot de virtuele machine. 

Gebruik [az network vnet subnet create om](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) een nieuw subnet Azure Bastion maken.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

Maak een openbaar IP-adres voor de bastionhost [met az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create). 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

Gebruik [az network bastion create om](/cli/azure/network/bastion#az-network-bastion-create) de bastionhost te maken. 

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

In deze sectie maakt u een virtuele machine om de NAT-gateway te testen om het openbare IP-adres van de uitgaande verbinding te verifiëren.

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

Wacht tot het maken van de virtuele machine is voltooid voordat u verder gaat met de volgende sectie.

## <a name="test-nat-gateway"></a>NAT-gateway testen

In deze sectie testen we de NAT-gateway. Eerst ontdekken we het openbare IP-adres van de NAT-gateway. Vervolgens maken we verbinding met de virtuele testmachine en verifiëren we de uitgaande verbinding via de NAT-gateway.
    
1. Meld u aan bij [Azure Portal](https://portal.azure.com)

1. Zoek het openbare IP-adres voor de NAT-gateway op het **scherm** Overzicht. Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myPublicIP**.

2. Noteer het openbare IP-adres:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Openbaar IP-adres van NAT-gateway ontdekken" border="true":::

3. Selecteer **Alle services** in het linkermenu, selecteer Alle **resources** en selecteer vervolgens in de lijst met resources **myVM** die zich in de resourcegroep **myResourceGroupNAT** bevindt.

4. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** en daarna **Bastion**.

5. Selecteer de blauwe knop **Bastion gebruiken**.

6. Voer de gebruikersnaam en het wachtwoord in die zijn ingevoerd tijdens het maken van de VM.

7. Open **Internet Explorer** op **myTestVM**.

8. Voer **https://whatsmyip.com** in de adresbalk in.

9. Controleer of het weergegeven IP-adres overeenkomt met het NAT-gatewayadres dat u in de vorige stap hebt genoteerd:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer extern uitgaand IP-adres weergeven" border="true":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijdert u het virtuele netwerk, de virtuele machine en de NAT-gateway met de volgende stappen:

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over Azure Virtual Network NAT, zie:
> [!div class="nextstepaction"]
> [Virtual Network NAT overzicht](nat-overview.md)
