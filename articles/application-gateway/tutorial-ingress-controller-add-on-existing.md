---
title: 'Zelfstudie: De invoegtoepassing Ingress Controller inschakelen voor een bestaand AKS-cluster met een bestaande Azure Application Gateway'
description: Gebruik deze zelfstudie om de invoegtoepassing Ingress Controller in te schakelen voor uw bestaande AKS-cluster met een bestaande Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: 9d1aa54ba1e3f3a589df8f694e340909c4e24ecc
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183682"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway-through-azure-cli-preview"></a>Zelfstudie: De invoegtoepassing Application Gateway Ingress Controller inschakelen voor een bestaand AKS cluster met een bestaande Application Gateway via Azure CLI (preview)

U kunt Azure CLI gebruiken om de invoegtoepassing [Application Gateway Ingress Controller (AGIC)](ingress-controller-overview.md) die nu in preview is in te schakelen voor uw [AKS-cluster](https://azure.microsoft.com/services/kubernetes-service/) (Azure Kubernetes Services). In deze zelfstudie leert u hoe u de invoegtoepassing AGIC kunt gebruiken om uw Kubernetes-toepassing beschikbaar te maken in een bestaand AKS-cluster via een bestaande Application Gateway die wordt geïmplementeerd in afzonderlijke virtuele netwerken. U begint met het maken van een AKS-cluster in één virtueel netwerk en een Application Gateway in een afzonderlijk virtueel netwerk om bestaande resources te simuleren. Vervolgens schakelt u de AGIC-invoegtoepassing in, koppelt u de twee virtuele netwerken als peers en implementeert u een voorbeeldtoepassing die met behulp van de AGIC-invoegtoepassing beschikbaar wordt gemaakt via de Application Gateway. Als u de AGIC-invoegtoepassing inschakelt voor een bestaande Application Gateway en een bestaand AKS-cluster in hetzelfde virtuele netwerk, kunt u de peering-stap hieronder overslaan. De invoegtoepassing biedt een veel snellere manier om AGIC voor uw AKS-cluster te implementeren dan [eerder via Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on), en biedt ook een volledig beheerde ervaring.  

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een resourcegroep maken 
> * Een nieuw AKS-cluster maken 
> * Een nieuwe Application Gateway maken 
> * De AGIC-invoegtoepassing inschakelen in het bestaande AKS-cluster met behulp van de bestaande Application Gateway 
> * Het virtuele netwerk van Application Gateway koppelen aan het virtuele netwerk van het AKS-cluster
> * Een voorbeeldtoepassing implementeren met behulp van AGIC voor inkomend verkeer op het AKS-cluster
> * Controleren of de toepassing bereikbaar is via Application Gateway

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Voor deze zelfstudie is versie 2.0.4 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

 - Registreer de functievlag *AKS-IngressApplicationGatewayAddon* met behulp van de opdracht [az feature register](/cli/azure/feature#az-feature-register) zoals getoond in het volgende voorbeeld. Zolang de invoegtoepassing nog in preview is, hoeft u dit slechts één keer per abonnement te doen:
     ```azurecli-interactive
     az feature register --name AKS-IngressApplicationGatewayAddon --namespace microsoft.containerservice
     ```
    Het kan enkele minuten duren voordat de status Geregistreerd wordt weergegeven. U kunt de registratiestatus controleren met behulp van de opdracht [az feature list](/cli/azure/feature#az-feature-register):
     ```azurecli-interactive
     az feature list -o table --query "[?contains(name, 'microsoft.containerservice/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
     ```

 - Wanneer u klaar bent, vernieuwt u de registratie van de resourceprovider Microsoft.ContainerService met behulp van de opdracht [az provider register](/cli/azure/provider#az-provider-register):
    ```azurecli-interactive
    az provider register --namespace Microsoft.ContainerService
    ```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt in de locatie (regio) *canadacentral*. 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Een nieuw AKS-cluster implementeren

U gaat nu een nieuw AKS-cluster implementeren om te simuleren dat er een bestaand AKS-cluster is waarvoor u de AGIC-invoegtoepassing wilt inschakelen.  

In het volgende voorbeeld implementeert u een nieuw AKS-cluster met de naam *myCluster* met behulp van [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) en [beheerde identiteiten](../aks/use-managed-identity.md) in de resourcegroep die u hebt gemaakt, *myResourceGroup*.    

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Zie [hier](/cli/azure/aks?view=azure-cli-latest#az-aks-create) referentiemateriaal over het configureren van aanvullende parameters voor de opdracht `az aks create`. 

## <a name="deploy-a-new-application-gateway"></a>Een nieuwe Azure Application Gateway implementeren 

U implementeert nu een nieuwe Application Gateway om te simuleren dat er een bestaande Application Gateway is die u wilt gebruiken om het verkeer naar uw AKS-cluster te verdelen, *myCluster*. De naam van de Application Gateway wordt *myApplicationGateway*, maar u moet eerst een openbare IP-resource maken, *myPublicIp*, en een nieuw virtueel netwerk met de naam *myVnet* en adresruimte 11.0.0.0/8, alsmede een subnetmet adresruimte 11.1.0.0/16 en de naam *mySubnet*, en uw Application Gateway implementeren in *mySubnet* met behulp van *myPublicIp*. 

Wanneer u een AKS-cluster en Application Gateway in afzonderlijke virtuele netwerken gebruikt, mogen de adresruimten van de twee virtuele netwerken elkaar niet overlappen. De standaardadresruimte waarin een AKS-cluster wordt geïmplementeerd is 10.0.0.0/8; daarom stellen we het adresvoorvoegsel van het virtuele Application Gateway-netwerk in op 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> De invoegtoepassing Application Gateway Ingress Controller (AGIC) ondersteunt **alleen** Application Gateway v2-SKU's (Standard en WAF) en **niet** de Application Gateway v1-SKU's. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-with-existing-application-gateway"></a>De AGIC-invoegtoepassing inschakelen in bestaand AKS-cluster met bestaande Application Gateway 

Nu schakelt u de AGIC-invoegtoepassing in het AKS-cluster in dat u hebt gemaakt, *myCluster*, en geeft u op dat de AGIC-invoegtoepassing de bestaande Application Gateway moet gebruiken die u hebt gemaakt, *myApplicationGateway*. Zorg ervoor dat u aan het begin van deze zelfstudie de aks-preview extensie hebt toegevoegd of bijgewerkt. 

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="peer-the-two-virtual-networks-together"></a>Koppel de twee virtuele netwerken als peers

Omdat we het AKS-cluster in een eigen virtueel netwerk hebben geïmplementeerd en de Application Gateway in een ander virtueel netwerk, moet u de twee virtuele netwerken met elkaar verbinden als peers, zodat het verkeer van de Application Gateway naar de pods in het cluster kan stromen. Voor de peering van de twee virtuele netwerken moet de Azure CLI-opdracht twee keer worden uitgevoerd, om ervoor te zorgen dat de verbinding bi-directioneel is. Met de eerste opdracht maakt u een peering-verbinding van het virtuele Application Gateway netwerk naar het virtuele AKS-netwerk; met de tweede opdracht wordt een peering-verbinding in de andere richting gemaakt. 

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```
## <a name="deploy-a-sample-application-using-agic"></a>Een voorbeeldtoepassing implementeren met behulp van AGIC 

U gaat nu een voorbeeldtoepassing implementeren naar het AKS-cluster dat u hebt gemaakt en die de AGIC-invoegtoepassing gebruikt voor inkomend verkeer, en de Application Gateway verbinden met het AKS-cluster. Eerst haalt u referenties op voor het AKS-cluster dat u hebt geïmplementeerd door de opdracht `az aks get-credentials` uit te voeren. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Wanneer i de referenties hebt van het cluster dat u hebt gemaakt, voert u de volgende opdracht uit om een voorbeeldtoepassing in te stellen die gebruikmaakt van AGIC voor inkomend verkeer naar het cluster. Met AGIC wordt de Application Gateway die u eerder hebt ingesteld met de bijbehorende regels voor doorsturen, bijgewerkt naar de nieuwe voorbeeldtoepassing die u hebt geïmplementeerd.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Controleer of de toepassing bereikbaar is

Nu de Application Gateway is ingesteld om het verkeer naar het AKS-cluster af te handelen, gaan we controleren of uw toepassing bereikbaar is. U haalt eerst het IP-adres van het inkomende verkeer op. 

```azurecli-interactive
kubectl get ingress
```

Controleer of de voorbeeldtoepassing die u hebt gemaakt, actief is door het IP-adres van de Application Gateway te bezoeken dat u hebt verkregen door de bovenstaande opdracht uit te voeren, of raadpleeg `curl`. Het kan even duren voordat Application Gateway de update krijgt, dus als de Application Gateway nog de status 'Bijwerken' heeft in de portal, moet wachten voordat u probeert het IP-adres te bereiken. 

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, de toepassingsgateway en alle gerelateerde resources verwijderen als u deze niet meer nodig hebt.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het uitschakelen van de AGIC-invoegtoepassing](./ingress-controller-disable-addon.md)