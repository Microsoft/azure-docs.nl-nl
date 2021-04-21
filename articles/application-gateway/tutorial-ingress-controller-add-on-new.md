---
title: 'Zelfstudie: De invoegversie van de invoegversie inschakelen voor een nieuw AKS-cluster met een Azure Application Gateway'
description: Gebruik deze zelfstudie voor meer informatie over het inschakelen van de invoeggebruikte controller voor het inschakelen van uw nieuwe AKS-cluster met een nieuw Application Gateway-exemplaar.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: aad57c75481230db16a63aec7fb04fc5987ae8f0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772798"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Zelfstudie: De invoegversie van de invoegversie van de invoegversie inschakelen voor een nieuw AKS-cluster met een nieuw Application Gateway invoegingsinvoegprogramma

U kunt de Azure CLI gebruiken om de Application Gateway AGIC-invoegversie [(Ingress Controller)](ingress-controller-overview.md) in te stellen voor een nieuw [AKS-cluster (Azure Kubernetes Services).](https://azure.microsoft.com/services/kubernetes-service/)

In deze zelfstudie maakt u een AKS-cluster waarvoor de AGIC-invoegtoepassing is ingeschakeld. Als u het cluster maakt, wordt automatisch een Azure Application Gateway-instantie gemaakt om te gebruiken. Vervolgens implementeert u een voorbeeldtoepassing die gebruikmaakt van de invoegtoepassing om via Application Gateway de toepassing beschikbaar te maken. 

De invoegtoepassing biedt een veel snellere manier om AGIC voor uw AKS-cluster te implementeren dan [voorheen met behulp van helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Ze biedt ook een volledig beheerde ervaring.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een resourcegroep maken. 
> * Een nieuw AKS-cluster maken waarvoor de AGIC-invoegtoepassing is ingeschakeld.
> * Een voorbeeldtoepassing implementeren met behulp van AGIC voor inkomend verkeer op het AKS-cluster.
> * Controleren of de toepassing bereikbaar is via Application Gateway.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az_group_create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie (regio) *canadacentral*: 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Een AKS-cluster implementeren waarvoor de invoegtoepassing is ingeschakeld

U gaat nu een nieuw AKS-cluster implementeren waarvoor de AGIC-invoegtoepassing is ingeschakeld. Als u geen bestaande Application Gateway-instantie opgeeft om in dit proces te worden gebruikt, wordt er automatisch een nieuwe Application Gateway-instantie gemaakt en ingesteld om het verkeer naar het AKS-cluster af te handelen.  

> [!NOTE]
> De invoegtoepassing voor inkomend verkeer van Application Gateway ondersteunt *alleen* Application Gateway v2-SKU's (Standard en WAF) en *niet* de Application Gateway v1-SKU's. Wanneer u een nieuwe Application Gateway-instantie implementeert met behulp van de AGIC-invoegtoepassing, kunt u alleen een Application Gateway Standard_v2-SKU implementeren. Als u de invoegtoepassing voor een Application Gateway WAF_v2-SKU wilt inschakelen, moet u een van de volgende methoden gebruiken:
>
> - Schakel WAF in op Application Gateway via de portal. 
> - Maak eerst de Application Gateway-instantie WAF_v2 en volg daarna de instructies voor het [inschakelen van de AGIC-invoegtoepassing met een bestaand AKS-cluster en een bestaande Application Gateway-instantie](tutorial-ingress-controller-add-on-existing.md). 

In het volgende voorbeeld implementeert u een nieuw AKS-cluster met de naam *myCluster* met behulp van [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) en [beheerde identiteiten](../aks/use-managed-identity.md). De AGIC-invoegtoepassing wordt ingeschakeld in de resourcegroep *myResourceGroup* die u hebt gemaakt. 

Als u een nieuw AKS-cluster implementeert terwijl de AGIC-invoegtoepassing is ingeschakeld zonder dat u een bestaande Application Gateway-instantie opgeeft, wordt er automatisch een instantie van de Application Gateway Standard_v2-SKU gemaakt. U geeft dus ook de naam en de adresruimte van het subnet op van de Application Gateway-instantie. De naam van de Application Gateway-instantie wordt *myApplicationGateway* en de adresruimte van het subnet dat we gebruiken is 10.2.0.0/16.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-cidr "10.2.0.0/16" --generate-ssh-keys
```

Zie [dit referentiemateriaal](/cli/azure/aks#az_aks_create) voor meer informatie over het configureren van aanvullende parameters voor de opdracht `az aks create`. 

> [!NOTE]
> Het AKS-cluster dat u hebt gemaakt, wordt weergegeven in de resourcegroep *myResourceGroup* die u hebt gemaakt. De automatisch gemaakte Application Gateway-instantie bevindt zich echter in het knooppunt van de resourcegroep, waar zich de agentpools bevinden. De resourcegroep van het knooppunt heet standaard *MC_resource-group-name_cluster-name_location*, maar kan worden gewijzigd. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Een voorbeeldtoepassing implementeren met behulp van AGIC

U gaat nu een voorbeeldtoepassing implementeren naar het AKS-cluster dat u hebt gemaakt. De toepassing maakt gebruik van de AGIC-invoegtoepassing voor inkomend verkeer en verbindt de Application Gateway-instantie met het AKS-cluster. 

Haal eerst referenties op voor het AKS-cluster door de opdracht `az aks get-credentials` uit te voeren: 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Nu u referenties hebt, voert u de volgende opdracht uit om een voorbeeldtoepassing in te stellen die gebruikmaakt van AGIC voor inkomend verkeer naar het cluster. Met AGIC wordt de Application Gateway-instantie die u eerder hebt ingesteld met de bijbehorende regels voor doorsturen, bijgewerkt naar de nieuwe voorbeeldtoepassing die u hebt geïmplementeerd.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Controleer of de toepassing bereikbaar is

Nu de Application Gateway-instantie is ingesteld om het verkeer naar het AKS-cluster af te handelen, gaan we controleren of uw toepassing bereikbaar is. Haal eerst het IP-adres van het inkomende verkeer op: 

```azurecli-interactive
kubectl get ingress
```

Controleer of de voorbeeldtoepassing die u hebt gemaakt wordt uitgevoerd door een van de volgende handelingen uit te voeren:

- Ga naar het IP-adres van de Application Gateway-instantie dat u hebt verkregen door de voorgaande opdracht uit te voeren.
- Gebruik daarvoor `curl`. 

Het kan even duren voordat Application Gateway de update heeft ontvangen. Als Application Gateway op de portal nog steeds de status **Bijwerken** heeft, wacht dan tot het proces is voltooid voordat u naar het IP-adres gaat. 

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep, de Application Gateway-instantie en alle gerelateerde resources verwijderen als u deze niet meer nodig hebt:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het uitschakelen van de AGIC-invoegtoepassing](./ingress-controller-disable-addon.md)
