---
title: Routerings voorkeur configureren voor een virtuele machine-Azure CLI
description: Meer informatie over het maken van een virtuele machine met een openbaar IP-adres met een routerings voorkeurs keuze met behulp van de Azure-opdracht regel interface (CLI).
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7673a42afa3b85a2aaf3f11f5e9b74fd46d48488
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945085"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Routerings voorkeur configureren voor een virtuele machine met behulp van Azure CLI

In dit artikel wordt beschreven hoe u routerings voorkeur configureert voor een virtuele machine. Het Internet gebonden verkeer van de virtuele machine wordt doorgestuurd via het ISP-netwerk wanneer u kiest voor **Internet** als de voorkeurs methode voor route ring. De standaard routering is via het wereld wijde netwerk van micro soft.

In dit artikel wordt beschreven hoe u een virtuele machine maakt met een openbaar IP-adres dat is ingesteld om verkeer via het open bare Internet te routeren met behulp van Azure CLI.

> [!IMPORTANT]
> De voor keuren voor route ring is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken
1. Als u de Cloud Shell gebruikt, gaat u verder met stap 2. Open een opdracht sessie en meld u aan bij Azure met `az login` .
2. Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voor beeld wordt een resource groep gemaakt in de regio VS Oost Azure:

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken
Als u toegang wilt krijgen tot uw virtuele machines via internet, moet u een openbaar IP-adres maken. Maak een openbaar IP-adres met [az network public-ip create](/cli/azure/network/public-ip). In het volgende voor beeld wordt een openbaar IP-adres van het type route ring voor het *Internet* in de regio *VS-Oost* gemaakt:

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>Netwerkbronnen maken

Voordat u een virtuele machine implementeert, moet u ondersteunende netwerk bronnen maken, netwerk beveiligings groep, virtueel netwerk en virtuele NIC.

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerk beveiligings groep voor de regels die de inkomende en uitgaande communicatie in uw VNet regelen, met [AZ Network NSG Create](/cli/azure/network/nsg#az-network-nsg-create)

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). In het volgende voor beeld wordt een virtueel netwerk gemaakt met de naam *myVNET* met subnetten *mySubNet*:

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>Een NIC maken

Maak een virtuele NIC voor de VM met [AZ Network NIC Create](/cli/azure/network/nic#az-network-nic-create). In het volgende voor beeld wordt een virtuele NIC gemaakt die wordt gekoppeld aan de virtuele machine.

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een VM met [az vm create](/cli/azure/vm#az-vm-create). In het volgende voor beeld worden een Windows Server 2019-VM en de vereiste onderdelen van het virtuele netwerk gemaakt als deze nog niet bestaan.

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, als deze niet meer nodig zijn:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [routerings voorkeur in open bare IP-adressen](routing-preference-overview.md).
- Meer informatie over [open bare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Meer informatie over [instellingen voor open bare IP-adressen](virtual-network-public-ip-address.md#create-a-public-ip-address).
