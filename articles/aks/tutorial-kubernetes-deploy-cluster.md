---
title: 'Zelfstudie voor Kubernetes in Azure: Een cluster implementeren'
description: In deze zelfstudie Azure Kubernetes Service (AKS) gaat u een AKS-cluster maken en kubectl gebruiken om verbinding te maken met het hoofdknooppunt van Kubernetes.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a8e0ddcd77c26a00cf784fb8c2372734314dc0bb
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250635"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Zelfstudie: Een AKS-cluster (Azure Kubernetes Service) implementeren

Kubernetes biedt een gedistribueerd platform voor toepassingen in containers. Met AKS kunt u snel een productieklaar Kubernetes-cluster maken. In deze zelfstudie, deel drie van de zeven, wordt een Kubernetes-cluster geïmplementeerd in AKS. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een Kubernetes AKS-cluster implementeren dat kan worden geverifieerd bij Azure Container Registry
> * De Kubernetes-CLI (kubectl) installeren
> * Kubectl configureren om verbinding te maken met uw AKS-cluster

In latere zelf studies wordt de Azure stem-toepassing geïmplementeerd in het cluster, geschaald en bijgewerkt.

## <a name="before-you-begin"></a>Voordat u begint

In de vorige zelfstudies is een containerinstallatiekopie gemaakt en geüpload naar een Azure Container Registry-exemplaar. Als u deze stappen niet hebt uitgevoerd en u deze zelfstudie wilt volgen, begint u bij [Zelfstudie 1: containerinstallatiekopieën maken][aks-tutorial-prepare-app].

Voor deze zelfstudie moet u Azure CLI versie 2.0.53 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-a-kubernetes-cluster"></a>Een Kubernetes-cluster maken

AKS-clusters kunnen gebruikmaken van Kubernetes-RBAC (op rollen gebaseerd toegangsbeheer voor Kubernetes). Met deze vorm van toegangsbeheer kunt u de toegang tot resources definiëren op basis van rollen die zijn toegewezen aan gebruikers. Machtigingen worden gecombineerd als aan een gebruiker meerdere rollen zijn toegewezen, en machtigingen kunnen gelden voor één enkele naamruimte of voor een heel cluster. Standaard wordt Kubernetes-RBAC automatisch ingeschakeld in Azure CLI wanneer u een AKS-cluster maakt.

Maak een AKS-cluster met behulp van [az aks create][]. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluste* gemaakt in de resourcegroep met de naam *myResourceGroup*. Deze resourcegroep is gemaakt in de [vorige zelfstudie][aks-tutorial-prepare-acr] in de regio *eastus*. In het volgende voorbeeld wordt er geen regio opgegeven zodat het AKS-cluster ook wordt gemaakt in de regio *eastus*. Zie [quota's, beperkingen voor de grootte van virtuele machines en beschik baarheid van regio's in azure Kubernetes service (AKS)][quotas-skus-regions] voor meer informatie over resource limieten en beschik baarheid van REGIO'S voor AKS.

Aangezien u geen service-principal van Azure Active Directory hebt opgegeven, wordt er automatisch een gemaakt. Een AKS-cluster heeft een service-principal nodig om met andere Azure-resources te kunnen communiceren. Hier is aan deze service-principal [het recht verleend om installatiekopieën op te halen][container-registry-integration] uit de ACR-instantie (Azure Container Registry) die u in de vorige zelfstudie hebt gemaakt. Als u de opdracht wilt uitvoeren, moet u een rol voor **eigenaar** of een **Azure-account beheerder** hebben op het Azure-abonnement.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Als u wilt voor komen dat een **eigenaar** of een rol beheerder voor een **Azure-account** nodig is, kunt u ook hand matig een Service-Principal configureren om installatie kopieën van ACR te halen. Zie [ACR-verificatie met service-principals](../container-registry/container-registry-auth-service-principal.md) of [Verifiëren vanaf Kubernetes met pullgeheim](../container-registry/container-registry-auth-kubernetes.md) voor meer informatie. U kunt ook een [beheerde identiteit](use-managed-identity.md) gebruiken in plaats van een service-principal voor eenvoudiger beheer.

Na enkele minuten is de implementatie voltooid en retourneert JSON opgemaakte informatie over de AKS-implementatie.

> [!NOTE]
> Voor een betrouwbaar functionerend cluster moet u ten minste twee (2) knooppunten uitvoeren.

## <a name="install-the-kubernetes-cli"></a>De Kubernetes-CLI installeren

Gebruik [kubectl][kubectl], de Kubernetes-opdrachtregelclient, als u vanaf uw lokale computer verbinding wilt maken met het Kubernetes-cluster.

Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u het lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Verbinding maken met cluster met behulp van kubectl

Gebruik de opdracht [az aks get-credentials][] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. In het volgende voorbeeld worden de referenties voor het AKS-cluster met de naam *myAKSCluster* in *myResourceGroup* opgehaald:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Als u de verbinding met uw cluster wilt controleren, voert u de opdracht [kubectl get nodes][kubectl-get] uit om een lijst met de clusterknooppunten te retourneren:

```
$ kubectl get nodes

NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-37463671-vmss000000   Ready    agent   2m37s   v1.18.10
aks-nodepool1-37463671-vmss000001   Ready    agent   2m28s   v1.18.10
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie is een Kubernetes-cluster geïmplementeerd in AKS, en hebt u `kubectl` geconfigureerd om er verbinding mee te maken. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Kubernetes AKS-cluster implementeren dat kan worden geverifieerd bij Azure Container Registry
> * De Kubernetes-CLI (kubectl) installeren
> * Kubectl configureren om verbinding te maken met uw AKS-cluster

Ga door naar de volgende zelfstudie voor informatie over het implementeren van toepassingen naar het cluster.

> [!div class="nextstepaction"]
> [Toepassing implementeren in Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
[quotas-skus-regions]: quotas-skus-regions.md
