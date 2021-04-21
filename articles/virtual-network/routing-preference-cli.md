---
title: Routeringsvoorkeur configureren voor een openbaar IP-adres met behulp van Azure CLI
titlesuffix: Azure Virtual Network
description: Meer informatie over het maken van een openbaar IP-adres met een routeringsvoorkeur voor internetverkeer met behulp van de Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 58b91c105ed48617b64356904942f5ab6b461cda
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776551"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Routeringsvoorkeur configureren voor een openbaar IP-adres met behulp van Azure CLI

In dit artikel wordt beschreven hoe u routeringsvoorkeur via isp-netwerk **(internetoptie)** configureert voor een openbaar IP-adres met behulp van Azure CLI. Nadat u het openbare IP-adres hebt gemaakt, kunt u dit koppelen aan de volgende Azure-resources voor inkomende en uitgaande verkeer naar internet:

* Virtuele machine
* Schaalset voor virtuele machines
* Azure Kubernetes Service (AKS)
* Internet-gerichte load balancer
* Application Gateway
* Azure Firewall

Standaard wordt de routeringsvoorkeur voor het openbare IP-adres ingesteld op het wereldwijde netwerk van Microsoft voor alle Azure-services en kan deze worden gekoppeld aan elke Azure-service.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.49 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az_group_create). In het volgende voorbeeld wordt een resourcegroep gemaakt in de **Azure-regio VS -** oost:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Maak een openbaar IP-adres met de routeringsvoorkeur **Internettype** met de opdracht [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create)met de indeling zoals hieronder wordt weergegeven.

Met de volgende opdracht maakt  u een nieuw openbaar IP-adres met internetrouteringsvoorkeur in de **Azure-regio VS -** oost.

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

> [!NOTE]
>  Op dit moment ondersteunt routeringsvoorkeur alleen openbare IPV4-IP-adressen.

U kunt het bovenstaande openbare IP-adres koppelen aan een [virtuele Windows-](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Linux-machine.](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Gebruik de CLI-sectie op de pagina zelfstudie: Koppel een openbaar [IP-adres](associate-public-ip-address-vm.md#azure-cli) aan een virtuele machine om het openbare IP-adres aan uw virtuele machine te koppelen. U kunt het hierboven gemaakte openbare IP-adres ook koppelen aan een [Azure Load Balancer](../load-balancer/load-balancer-overview.md), door het toe te wijzen aan load balancer **front-load balancer front-endconfiguratie.** Het openbare IP-adres doet dienst als een virtueel IP-adres (VIP) met taakverdeling.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [routeringsvoorkeur in openbare IP-adressen.](routing-preference-overview.md) 
- [Configureer routeringsvoorkeur voor een VM met behulp van de Azure CLI](configure-routing-preference-virtual-machine-cli.md).
