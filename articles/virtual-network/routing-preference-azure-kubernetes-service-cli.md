---
title: Routeringsvoorkeur configureren voor een Azure Kubernetes-service met behulp van Azure CLI
titlesuffix: Azure Virtual Network
description: Meer informatie over het configureren van een AKS-cluster met routeringsvoorkeur met behulp van de Azure CLI.
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
ms.openlocfilehash: 9eaad12e254150109498be0fac2f285f33a5965c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776569"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Routeringsvoorkeur configureren voor een Kubernetes-cluster met behulp van Azure CLI

In dit artikel wordt beschreven hoe u routeringsvoorkeur configureert via isp-netwerk **(internetoptie)** voor een Kubernetes-cluster met behulp van Azure CLI. Routeringsvoorkeur wordt ingesteld door een openbaar IP-adres van het routeringsvoorkeurstype **Internet*** te maken en dit vervolgens te gebruiken tijdens het maken van het AKS-cluster.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.49 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az_group_create). In het volgende voorbeeld wordt een resourcegroep gemaakt in de **Azure-regio VS -** oost:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Maak een openbaar IP-adres met de routeringsvoorkeur **Internettype met** de opdracht [az network public-ip create.](/cli/azure/network/public-ip#az_network_public_ip_create)

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

## <a name="get-the-id-of-public-ip-address"></a>De id van het openbare IP-adres op halen

De volgende opdracht retourneert de id van het openbare IP-adres die in de vorige sectie is gemaakt:
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Kubernetes-cluster maken met het openbare IP-adres

Met de volgende opdracht maakt u het AKS-cluster met het openbare IP-adres dat u in de vorige sectie hebt gemaakt:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>Het implementeren van het AKS-cluster duurt enkele minuten.

Als u wilt valideren, zoekt u naar het openbare IP-adres dat u in de vorige stap in Azure Portal hebt gemaakt. U ziet dat het IP-adres is gekoppeld aan de load balancer die is gekoppeld aan het Kubernetes-cluster, zoals hieronder wordt weergegeven:

 ![Openbaar IP-adres voor routeringsvoorkeur voor Kubernetes](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [routeringsvoorkeur in openbare IP-adressen.](routing-preference-overview.md) 
- [Configureer routeringsvoorkeur voor een VM met behulp van de Azure CLI](configure-routing-preference-virtual-machine-cli.md).
