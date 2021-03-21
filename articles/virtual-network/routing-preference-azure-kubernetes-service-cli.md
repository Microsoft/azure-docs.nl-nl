---
title: Routerings voorkeur configureren voor een Azure Kubernetes-service met Azure CLI
titlesuffix: Azure Virtual Network
description: Meer informatie over het configureren van een AKS-cluster met behulp van route ring via de Azure CLI.
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
ms.openlocfilehash: ac70f48a3c484f8865c54e09c59662a14a259e74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679819"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Routerings voorkeur configureren voor een Kubernetes-cluster met behulp van Azure CLI

In dit artikel wordt beschreven hoe u routerings voorkeur configureert via ISP Network (**Internet** optie) voor een Kubernetes-cluster met behulp van Azure cli. Routerings voorkeur wordt ingesteld door een openbaar IP-adres van het routerings voorkeur type * * Internet * * * * te maken en het vervolgens te gebruiken tijdens het maken van het AKS-cluster.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.49 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voor beeld wordt een resource groep gemaakt in de regio **VS Oost** Azure:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Maak een openbaar IP-adres met routerings voorkeur van het **Internet** type met de opdracht [AZ Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create).

Met de volgende opdracht maakt u een nieuw openbaar IP-adres met **Internet** routering voor keur in de regio **VS Oost** Azure.

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
>  Routerings voorkeur ondersteunt momenteel alleen open bare IP-adressen van IPV4.

## <a name="get-the-id-of-public-ip-address"></a>De ID van het open bare IP-adres ophalen

De volgende opdracht retourneert de ID van het open bare IP-adres dat u in de vorige sectie hebt gemaakt:
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Een Kubernetes-cluster maken met het open bare IP-adres

Met de volgende opdracht maakt u het AKS-cluster met het open bare IP-adres dat u in de vorige sectie hebt gemaakt:

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>Het duurt enkele minuten om het AKS-cluster te implementeren.

Als u wilt valideren, zoekt u naar het open bare IP-adres dat u in de vorige stap in Azure Portal hebt gemaakt, wordt het IP-adres gekoppeld aan het load balancer dat is gekoppeld aan het Kubernetes-cluster zoals hieronder wordt weer gegeven:

 ![Bewerkings voorkeur-openbaar IP-adres voor Kubernetes](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [routerings voorkeur in open bare IP-adressen](routing-preference-overview.md). 
- [Configureer routerings voorkeur voor een virtuele machine met behulp van de Azure cli](configure-routing-preference-virtual-machine-cli.md).

