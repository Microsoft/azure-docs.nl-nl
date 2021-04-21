---
title: Een openbaar IP-adres maken - Azure CLI
description: Meer informatie over het maken van een openbaar IP-adres met behulp van Azure CLI
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: ff0dbf31f6f428b23e00f9366d55703416847b90
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767687"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Quickstart: Een openbaar IP-adres maken met behulp van Azure CLI

In dit artikel wordt beschreven hoe u een resource voor een openbaar IP-adres maakt met behulp van Azure CLI. Zie Openbare IP-adressen voor meer informatie over de resources waaraan dit kan worden gekoppeld, het verschil tussen de Basic- en Standard-SKU en andere [gerelateerde informatie.](./public-ip-addresses.md)  In dit voorbeeld richten we ons alleen op IPv4-adressen; Zie IPv6 voor Azure VNet voor meer informatie [over IPv6-adressen.](./ipv6-overview.md)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep met [az group create met](/cli/azure/group#az_group_create) de naam **myResourceGroup** op de **locatie eastus2.**

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>Openbaar IP-adres maken

---
# <a name="standard-sku---using-zones"></a>[**Standaard-SKU: zones gebruiken**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>De volgende opdracht werkt voor API-versie 2020-08-01 of hoger.  Raadpleeg Resourceproviders en -typen voor meer informatie over de API-versie die [momenteel wordt gebruikt.](../azure-resource-manager/management/resource-providers-and-types.md)

Gebruik [az network public-ip create om](/cli/azure/network/public-ip#az_network_public_ip_create) in **myResourceGroup** een standaard zone-redundant openbaar IP-adres te maken met de naam **myStandardZRPublicIP.**

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1 2 3
```
> [!IMPORTANT]
> Voor versies van de API die ouder zijn dan 2020-08-01, moet u de bovenstaande opdracht uitvoeren zonder een zoneparameter op te geven om een zone-redundant IP-adres te maken. 
>

Gebruik de volgende opdracht om een standaard, zonaal openbaar IP-adres te maken in Zone 2 met de naam **myStandardZonalPublicIP** in **myResourceGroup:**

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Houd er rekening mee dat de bovenstaande opties voor zones alleen geldige selecties zijn in regio's met [Beschikbaarheidszones.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)

# <a name="standard-sku---no-zones"></a>[**Standaard-SKU - Geen zones**](#tab/option-create-public-ip-standard)

>[!NOTE]
>De volgende opdracht werkt voor API-versie 2020-08-01 of hoger.  Raadpleeg Resourceproviders en -typen voor meer informatie over de API-versie die [momenteel wordt gebruikt.](../azure-resource-manager/management/resource-providers-and-types.md)

Gebruik [az network public-ip create om](/cli/azure/network/public-ip#az_network_public_ip_create) een standaard openbaar IP-adres te maken als een niet-zonale resource met de naam **myStandardPublicIP** in **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Deze selectie is geldig in alle regio's en is de standaardselectie voor standaard openbare IP-adressen in regio's [zonder Beschikbaarheidszones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="basic-sku"></a>[**Basis-SKU**](#tab/option-create-public-ip-basic)

Gebruik [az network public-ip create om in](/cli/azure/network/public-ip#az_network_public_ip_create) **myResourceGroup** een statisch statisch openbaar IP-adres te maken met de naam **myBasicPublicIP.**  Openbare BASIC-IP's hebben niet het concept beschikbaarheidszones.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
Als het acceptabel is dat het IP-adres na een bepaalde periode wordt **gewijzigd,** kan dynamische IP-toewijzing worden geselecteerd door de toewijzingsmethode te wijzigen in Dynamisch.

---

## <a name="additional-information"></a>Aanvullende informatie 

Zie Openbare IP-adressen beheren voor meer informatie over de hierboven vermelde [afzonderlijke variabelen.](./virtual-network-public-ip-address.md#create-a-public-ip-address)

## <a name="next-steps"></a>Volgende stappen
- Koppel een [openbaar IP-adres aan een virtuele machine.](./associate-public-ip-address-vm.md#azure-portal)
- Meer informatie over [openbare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Meer informatie over alle [instellingen voor openbare IP-adressen.](virtual-network-public-ip-address.md#create-a-public-ip-address)
