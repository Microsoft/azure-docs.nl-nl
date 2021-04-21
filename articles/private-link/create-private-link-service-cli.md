---
title: 'Quickstart: Een Azure Private Link maken met behulp van Azure CLI'
description: In deze quickstart leert u hoe u een Azure Private Link service maakt met behulp van Azure CLI
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: c8e32a56148326104c3514b8a2fdb5d6bbd3f00a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778459"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>Quickstart: Een Private Link maken met behulp van Azure CLI

Aan de slag met een Private Link-service die naar uw service verwijst.  Geef Private Link toegang tot uw service of resource die achter een Azure Standard Load Balancer is geïmplementeerd.  Gebruikers van uw service hebben persoonlijke toegang via hun virtuele netwerk.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Voor deze quickstart is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep maken met [az group create](/cli/azure/group#az_group_create):

* Met **de naam CreatePrivLinkService-rg.** 
* Op de locatie **eastus**.

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>Een interne load balancer maken

In dit gedeelte maakt u een virtueel netwerken en een interne Azure Load Balancer.

### <a name="virtual-network"></a>Virtueel netwerk

In dit gedeelte maakt u een virtueel netwerk en een subnet om de load balancer te hosten die toegang heeft tot uw Private Link-service.

Maak een Virtual Network met [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create).

* Met de naam **myVNet**.
* Adresvoorvoegsel van **10.1.0.0/16**.
* Subnet met de **naam mySubnet.**
* Subnetvoorvoegsel van **10.1.0.0/24**.
* In de **resourcegroep CreatePrivLinkService-rg.**
* Locatie van **eastus2.**
* Schakel het netwerkbeleid voor de Private Link-service op het subnet uit.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Gebruik [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)om het subnet bij te werken om het private link-servicenetwerkbeleid uit te schakelen:

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Een Standard Load Balancer maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de load balancer kunt maken en configureren:

  * Een front-end-IP-pool die het binnenkomende netwerkverkeer op de load balancer ontvangt.
  * Een back-end-IP-pools waar de front-endpool het netwerkverkeer naartoe stuurt dat door de load balancer is verdeeld.
  * Een statustest die de status van de back-end-VM-instanties vaststelt.
  * Een load balancer-regel die bepaalt hoe het verkeer over de VM's wordt verdeeld.

### <a name="create-the-load-balancer-resource"></a>De load balancer-resource maken

Maak een openbare load balancer met [az network lb create](/cli/azure/network/lb#az_network_lb_create):

* Genaamd **myLoadBalancer**.
* Een front-endgroep met de naam **myFrontEnd**.
* Een back-endgroep met de naam MyBackendPool.
* Gekoppeld aan het virtuele netwerk **myVNet**.
* Gekoppeld aan het back-endsubnet **mySubnet.**

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Statustest maken

Met een statustest worden alle VM-instanties gecontroleerd om na te gaan of ze netwerkverkeer kunnen verzenden. 

Een virtuele machine met een mislukte test wordt verwijderd uit de load balancer. De virtuele machine wordt weer toegevoegd aan de load balancer wanneer de fout is opgelost.

Gebruik een statustest met [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create):

* Bewaakt de status van de virtuele machines.
* Met de naam **myHealthProbe**.
* Protocol: **TCP**.
* Controleert **poort 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Load balancer-regel maken

Met een load balancer-regel wordt het volgende gedefinieerd:

* De front-end-IP-configuratie voor het binnenkomende verkeer.
* De back-end-IP-adresgroep voor het ontvangen van verkeer.
* De vereiste bron- en doelpoort. 

Gebruik [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) om een load balancer-regel te maken:

* Naam: **myHTTPRule**
* Luistert aan **poort 80** in de front-endpool **myFrontEnd**.
* Verzendt netwerkverkeer volgens taakverdeling naar de back-endadresgroep **myBackEndPool** via **poort 80**. 
* Gebruikt statustest **myHealthProbe**.
* Protocol: **TCP**.
* Time-out voor inactiviteit: **15 minuten**.
* Schakel TCP opnieuw instellen in.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

## <a name="create-a-private-link-service"></a>Een Private Link-service maken

In deze sectie maakt u een Private Link-service die gebruikmaakt van de Azure Load Balancer in de vorige stap hebt gemaakt.

Maak een Private Link-service met behulp van een load balancer front-end-IP-configuratie [met az network private-link-service create](/cli/azure/network/private-link-service#az_network_private_link_service_create):

* Met **de naam myPrivateLinkService.**
* In virtueel netwerk **myVnet**.
* Gekoppeld aan load balancer **myLoadBalancer en** front-endconfiguratie **myFrontEnd.**
* Op de **locatie eastus2.**
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

Uw Private Link-service wordt gemaakt en kan verkeer ontvangen. Als u verkeersstromen wilt zien, configureert u uw toepassing achter uw load balancer.


## <a name="create-private-endpoint"></a>Privé-eindpunt maken

In deze sectie gaat u de Private Link-service aan een privé-eindpunt koppelen. Een virtueel netwerk bevat het privé-eindpunt voor de Private Link-service. Dit virtuele netwerk bevat de resources die toegang hebben tot uw Private Link-service.

### <a name="create-private-endpoint-virtual-network"></a>Virtueel netwerk voor privé-eindpunten maken

Maak een Virtual Network met [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create).

* Met de **naam myVNetPE.**
* Adres voorvoegsel **van 11.1.0.0/16**.
* Subnet met de **naam mySubnetPE.**
* Subnet-voorvoegsel **van 11.1.0.0/24**.
* In de **resourcegroep CreatePrivLinkService-rg.**
* Locatie van **eastus2.**

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Als u het subnet wilt bijwerken om het netwerkbeleid voor privé-eindpunten uit te schakelen, gebruikt [u az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Eindpunt en verbinding maken

* Gebruik [az network private-link-service show om de resource-id](/cli/azure/network/private-link-service#az_network_private_link_service_show) van de Private Link-service op te halen. De opdracht plaatst de resource-id in een variabele voor later gebruik.

* Gebruik [az network private-endpoint create om](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) het privé-eindpunt te maken in het virtuele netwerk dat u eerder hebt gemaakt.

* Met **de naam MyPrivateEndpoint**.
* In de **resourcegroep CreatePrivLinkService-rg.**
* Verbindingsnaam **myPEconnectiontoPLS**.
* Locatie van **eastus2.**
* In het virtuele netwerk **myVNetPE en** het subnet **mySubnetPE.**

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [az group delete](/cli/azure/group#az_group_delete) om de resourcegroep, private link-service, load balancer en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart, gaat u het volgende doen:

* Een virtueel netwerk gemaakt en een interne Azure Load Balancer.
* Een Private Link-service gemaakt

Voor meer informatie over Azure Privé-eindpunt gaat u naar:
> [!div class="nextstepaction"]
> [Quickstart: Een privé-eindpunt maken met behulp van Azure CLI](create-private-endpoint-cli.md)
