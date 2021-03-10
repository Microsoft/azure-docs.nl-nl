---
title: 'Quick Start: een persoonlijke Azure-koppelings service maken met behulp van Azure CLI'
description: In deze Quick Start leert u hoe u een persoonlijke Azure-koppelings service kunt maken met behulp van Azure CLI
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: 76fd959c28203132be4695031d96315f258cf53f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563044"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>Snelstartgids: een persoonlijke koppelings service maken met behulp van Azure CLI

Aan de slag met een Private Link-service die naar uw service verwijst.  Geef Private Link toegang tot uw service of resource die achter een Azure Standard Load Balancer is geïmplementeerd.  Gebruikers van uw service hebben persoonlijke toegang via hun virtuele netwerk.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Voor deze quickstart is versie 2.0.28 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Maak een resourcegroep maken met [az group create](/cli/azure/group#az_group_create):

* Met de naam **CreatePrivLinkService-RG**. 
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

Maak een Virtual Network met [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create).

* Met de naam **myVNet**.
* Adresvoorvoegsel van **10.1.0.0/16**.
* Subnet met de naam **mySubnet**.
* Subnetvoorvoegsel van **10.1.0.0/24**.
* In de **CreatePrivLinkService-RG-** resource groep.
* Locatie van **eastus2**.
* Schakel het netwerk beleid voor de service private link uit op het subnet.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Gebruik [AZ Network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update): voor het bijwerken van het subnet voor het uitschakelen van het netwerk beleid van een particuliere koppelings service.

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

Maak een openbare load balancer met [az network lb create](/cli/azure/network/lb#az-network-lb-create):

* Genaamd **myLoadBalancer**.
* Een front-endgroep met de naam **myFrontEnd**.
* Een back-endgroep met de naam MyBackendPool.
* Gekoppeld aan het virtuele netwerk **myVNet**.
* Gekoppeld aan het back-end-subnet **mySubnet**.

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

Gebruik een statustest met [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create):

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

Gebruik [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) om een load balancer-regel te maken:

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

## <a name="create-a-private-link-service"></a>Een persoonlijke koppelings service maken

In deze sectie maakt u een persoonlijke koppelings service die gebruikmaakt van de Azure Load Balancer gemaakt in de vorige stap.

Een persoonlijke koppelings service maken met behulp van een standaard-front-load balancer frontend-IP-configuratie met [AZ Network Private-Link-service Create](/cli/azure/network/private-link-service#az-network-private-link-service-create):

* Met de naam **myPrivateLinkService**.
* In virtueel netwerk **myVnet**.
* Gekoppeld aan standaard load balancer **myLoadBalancer** en front-end configuratie **myFrontEnd**.
* Op de locatie **eastus2** .
 
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

Uw persoonlijke koppelings service is gemaakt en kan verkeer ontvangen. Als u verkeers stromen wilt zien, configureert u uw toepassing achter uw standaard load balancer.


## <a name="create-private-endpoint"></a>Privé-eindpunt maken

In deze sectie wijst u de persoonlijke koppelings service toe aan een persoonlijk eind punt. Een virtueel netwerk bevat het persoonlijke eind punt voor de persoonlijke koppelings service. Dit virtuele netwerk bevat de resources die toegang hebben tot uw persoonlijke koppelings service.

### <a name="create-private-endpoint-virtual-network"></a>Virtueel netwerk voor een privé-eind punt maken

Maak een Virtual Network met [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create).

* Met de naam **myVNetPE**.
* Adres voorvoegsel van **11.1.0.0/16**.
* Subnet met de naam **mySubnetPE**.
* Het subnetvoorvoegsel van **11.1.0.0/24**.
* In de **CreatePrivLinkService-RG-** resource groep.
* Locatie van **eastus2**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Gebruik [AZ Network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update): voor het bijwerken van het subnet om beleid voor privé-eindpunt netwerk uit te scha kelen.

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Eind punt en verbinding maken

* Gebruik [AZ Network Private-Link-service show](/cli/azure/network/private-link-service#az_network_private_link_service_show) om de resource-id van de persoonlijke koppelings service op te halen. Met de opdracht wordt de resource-ID in een variabele geplaatst voor later gebruik.

* Gebruik [AZ Network private-endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) om het persoonlijke eind punt te maken in het virtuele netwerk dat u eerder hebt gemaakt.

* Met de naam **MyPrivateEndpoint**.
* In de **CreatePrivLinkService-RG-** resource groep.
* Verbindings naam **myPEconnectiontoPLS**.
* Locatie van **eastus2**.
* In virtueel netwerk **myVNetPE** en subnet **mySubnetPE**.

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

Als u deze niet meer nodig hebt, gebruikt u de opdracht [AZ Group delete](/cli/azure/group#az-group-delete) om de resource groep, de persoonlijke koppelings service, Load Balancer en alle gerelateerde resources te verwijderen.

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
