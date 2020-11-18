---
title: 'Een op route gebaseerde Azure-VPN Gateway maken: CLI'
description: Maak snel een op route gebaseerde Azure VPN-gateway met behulp van de Azure CLI, voor een VPN-verbinding met een on-premises netwerk of voor het verbinden van virtuele netwerken.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 2bbd7e39ee65ba304ec62697b6fcc77bea133b41
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94737214"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Een op een route gebaseerde VPN-gateway maken met behulp van CLI

Dit artikel helpt u snel een op route gebaseerde Azure VPN-gateway te maken met behulp van de Azure CLI. Een VPN-gateway wordt gebruikt bij het maken van een VPN-verbinding met uw on-premises netwerk. U kunt ook een VPN-gateway gebruiken om verbinding te maken met VNets.

In de stappen in dit artikel wordt een VNet, een subnet, een gateway-subnet en een op route gebaseerde VPN-gateway (virtuele netwerk gateway) gemaakt. Het maken van een virtuele netwerk gateway kan 45 minuten of langer duren. Nadat het maken van de gateway is voltooid, kunt u verbindingen maken. Voor deze stappen is een Azure-abonnement vereist.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.4 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht [az group create](/cli/azure/group). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met behulp van de opdracht [AZ Network vnet Create](/cli/azure/network/vnet) . In het volgende voor beeld wordt een virtueel netwerk met de naam **VNet1** gemaakt op de locatie **eastus** :

```azurecli-interactive
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Een gatewaysubnet toevoegen

Het gateway-subnet bevat de gereserveerde IP-adressen die door de Gateway Services van het virtuele netwerk worden gebruikt. Gebruik de volgende voor beelden om een gateway-subnet toe te voegen:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Een openbaar IP-adres aanvragen

Een VPN-gateway moet een dynamisch toegewezen openbaar IP-adres hebben. Het open bare IP-adres wordt toegewezen aan de VPN-gateway die u voor uw virtuele netwerk hebt gemaakt. Gebruik het volgende voor beeld om een openbaar IP-adres aan te vragen:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>De VPN-gateway maken

Maak de VPN Gateway met behulp van de opdracht [az network vnet-gateway create](/cli/azure/group).

Als u deze opdracht uitvoert met behulp van de `--no-wait` -para meter, worden er geen feedback of uitvoer weer gegeven. `--no-wait`Met de para meter kan de gateway op de achtergrond worden gemaakt. Dit betekent niet dat de VPN-gateway onmiddellijk wordt gemaakt.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

Het maken van een VPN-gateway kan tot 45 minuten of langer duren.

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>De VPN-gateway weer geven

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Het antwoord ziet er ongeveer als volgt uit:

```output
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>Het openbare IP-adres weergeven

Gebruik het volgende voor beeld om het open bare IP-adres weer te geven dat aan uw gateway is toegewezen:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

De waarde die is gekoppeld aan het veld **ipAddress** is het open bare IP-adres van uw VPN-gateway.

Voorbeeld van een reactie:

```output
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u hebt gemaakt niet meer nodig hebt, gebruikt u [AZ Group delete](/cli/azure/group) om de resource groep te verwijderen. Hiermee verwijdert u de resourcegroep en alle resources die deze bevat.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Volgende stappen

Zodra de gateway is gemaakt, kunt u een verbinding maken tussen uw virtuele netwerk en een ander VNet. U kunt ook een verbinding maken tussen uw virtuele netwerk en een on-premises locatie.

> [!div class="nextstepaction"]
> [Een site-naar-site-verbinding maken](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Een punt-naar-site-verbinding maken](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Een verbinding met een ander VNet maken](vpn-gateway-vnet-vnet-rm-ps.md)
