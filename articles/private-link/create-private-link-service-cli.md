---
title: Een persoonlijke Azure-koppelings service maken met behulp van Azure CLI
description: Meer informatie over het maken van een Azure-service voor persoonlijke koppelingen met behulp van Azure CLI
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 87fe02aed19ae7e5858715748a2b4c4da87a07b3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658567"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Een persoonlijke koppelings service maken met behulp van Azure CLI
In dit artikel wordt beschreven hoe u een persoonlijke koppelings service maakt in azure met behulp van Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Dit artikel vereist de nieuwste versie van de Azure CLI. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-private-link-service"></a>Een Private Link-service maken
### <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een virtueel netwerk kunt maken, moet u een resourcegroep maken die het virtuele netwerk host. Maak een resourcegroep maken met [az group create](/cli/azure/group). Dit voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *westcentralus*:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak een virtueel netwerk met [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). In dit voor beeld wordt een standaard virtueel netwerk gemaakt met de naam *myVirtualNetwork* met één subnet met de naam *mySubnet*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>Een subnet maken
Maak een subnet voor het virtuele netwerk met [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). In dit voor beeld maakt u een subnet met de naam *mySubnet* in het virtuele netwerk *myVirtualNetwork* :

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>Een interne Load Balancer maken 
Maak een interne load balancer met [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create). In dit voor beeld wordt een interne load balancer gemaakt met de naam *myILB* in de resource groep met de naam *myResourceGroup*. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>Een load balancer-statustest maken

Een statustest controleert alle exemplaren van de virtuele machines om ervoor te zorgen dat deze netwerkverkeer kunnen ontvangen. Het exemplaar van een virtuele machine met mislukte testcontroles wordt uit de load balancer verwijderd totdat deze weer online komt en een testcontrole bepaalt of deze in orde is. Maak met [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) een statustest om de status van de virtuele machines te bewaken. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel definieert de front-end-IP-configuratie voor het binnenkomende verkeer en de back-end-IP-pool om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Maak met [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) de regel *myHTTPRule* voor het luisteren naar poort 80 in de front-endgroep *myFrontEnd* en het verzenden van netwerkverkeer met gelijke taakverdeling naar de back-endadresgroep *myBackEndPool* waarbij ook van poort 80 gebruik wordt gemaakt. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>Back-endservers maken

In dit voor beeld hebben we geen betrekking op het maken van virtuele machines. U kunt de stappen in Quick Start volgen [: Maak een interne Load Balancer voor het laden van vm's met behulp van Azure cli](/load-balancer/quickstart-load-balancer-standard-internal-cli#create-backend-servers) om twee virtuele machines te maken die moeten worden gebruikt als back-endservers voor de Load Balancer. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>Particuliere koppeling service-netwerk beleid op subnet uitschakelen 
De service voor persoonlijke koppelingen vereist een IP-adres van elk gewenst subnet in een virtueel netwerk. Op dit moment ondersteunen we geen netwerk beleid op deze IP-adressen.  Daarom moeten we het netwerk beleid op het subnet uitschakelen. Werk het subnet bij om het netwerk beleid van de privé koppelings service uit te scha kelen met [AZ Network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service-using-standard-load-balancer"></a>Een persoonlijke koppelings service maken met behulp van Standard Load Balancer 
 
Een persoonlijke koppelings service maken met behulp van de front-end-IP-configuratie van Standard Load Balancer met [AZ Network Private-Link-service Create](/cli/azure/network/private-link-service#az-network-private-link-service-create). In dit voor beeld wordt een persoonlijke koppelings service met de naam *myPLS* gemaakt met behulp van Standard Load Balancer met de naam *myLoadBalancer* in de resource groep genaamd *myResourceGroup*. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
Noteer de service-ID van de persoonlijke koppeling nadat deze is gemaakt. U hebt dit later nodig voor het aanvragen van een verbinding met deze service.  
 
In deze fase is uw persoonlijke koppelings service gemaakt en is deze klaar om het verkeer te ontvangen. Het bovenstaande voor beeld is alleen om te demonstreren hoe u een persoonlijke koppelings service maakt met behulp van Azure CLI.  De load balancer back-end-Pools of een toepassing op de back-endservers voor het Luis teren naar het verkeer is niet geconfigureerd. Als u end-to-end verkeers stromen wilt zien, wordt u ten zeerste aangeraden uw toepassing achter uw Standard Load Balancer te configureren.  
 
Vervolgens laten we zien hoe u deze service kunt toewijzen aan een persoonlijk eind punt in een ander virtueel netwerk met behulp van Azure CLI. Het voor beeld is beperkt tot het maken van het persoonlijke eind punt en het verbinden met de persoonlijke koppelings service die hierboven is gemaakt met behulp van Azure CLI. Daarnaast kunt u virtuele machines in het virtuele netwerk maken voor het verzenden en ontvangen van verkeer naar het persoonlijke eind punt.        
 
## <a name="private-endpoints"></a>Privé-eindpunten

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken 
Maak een virtueel netwerk met [AZ Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create). In dit voor beeld wordt een virtueel netwerk gemaakt met de naam  *myPEVNet*   in de resource groep met de naam *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>Het subnet maken 
Maak een subnet in een virtueel netwerk met [AZ Network vnet subnet Create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). In dit voor beeld wordt een subnet met de naam  *mySubnet* gemaakt in het virtuele netwerk met de naam   *myPEVnet* in de resource groep met de naam *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>Beleid voor privé-eindpunt netwerk op subnet uitschakelen 
Persoonlijk eind punt kan in elk gewenst subnet binnen een virtueel netwerk worden gemaakt. Op dit moment ondersteunen we geen netwerk beleid op privé-eind punten.  Daarom moeten we het netwerk beleid op het subnet uitschakelen. Werk het subnet bij om beleid voor privé-eindpunt netwerk uit te scha kelen met [AZ Network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>Een persoonlijk eind punt maken en verbinding maken met de service voor persoonlijke koppelingen 
Maak een persoonlijk eind punt voor het gebruik van de service voor persoonlijke koppelingen die hierboven in uw virtuele netwerk is gemaakt:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
U kunt de *privé-verbinding-resource-id* ophalen met behulp `az network private-link-service show` van de service private link. De ID ziet er als volgt uit:   
/subscriptions/subID/resourceGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>Verbindingen met de persoonlijke koppelings service weer geven 
 
Zie verbindings aanvragen op uw privé koppelings service met [AZ Network Private-Link-service show](/cli/azure/network/private-link-service#az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure Private Link service](private-link-service-overview.md)
 
