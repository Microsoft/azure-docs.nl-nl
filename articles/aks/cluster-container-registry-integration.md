---
title: Integratie Azure Container Registry met Azure Kubernetes Service
description: Meer informatie over het integreren Azure Kubernetes Service (AKS) met Azure Container Registry (ACR)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 01/08/2021
ms.openlocfilehash: ab8065a14aac9e798bfe7d632aa5b33c44706190
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775824"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Verifiëren bij Azure Container Registry vanuit Azure Kubernetes Service

Wanneer u ACR (Azure Container Registry) met Azure Kubernetes Service (AKS) gebruikt, moet er een verificatiemechanisme tot stand worden gebracht. Deze bewerking wordt geïmplementeerd als onderdeel van de CLI- en portalervaring door de vereiste machtigingen te verlenen aan uw ACR. Dit artikel bevat voorbeelden voor het configureren van verificatie tussen deze twee Azure-services. 

U kunt de integratie van AKS naar ACR instellen in een paar eenvoudige opdrachten met de Azure CLI. Met deze integratie wordt de rol AcrPull toegewezen aan de beheerde identiteit die is gekoppeld aan het AKS-cluster.

> [!NOTE]
> In dit artikel wordt automatische verificatie tussen AKS en ACR beschreven. Als u een afbeelding uit een persoonlijk extern register wilt halen, gebruikt u een [pull-geheim][Image Pull Secret]voor de afbeelding.

## <a name="before-you-begin"></a>Voordat u begint

Voor deze voorbeelden is het volgende vereist:

* **De** rol van **eigenaar of Azure-accountbeheerder** voor het **Azure-abonnement**
* Azure CLI versie 2.7.0 of hoger

Om te voorkomen  dat u de rol van eigenaar of **Azure-accountbeheerder** nodig hebt, kunt u handmatig een beheerde identiteit configureren of een bestaande beheerde identiteit gebruiken om ACR te verifiëren bij AKS. Zie Use [an Azure managed identity to authenticate to an Azure container registry (Een beheerde Azure-identiteit gebruiken om te verifiëren bij een Azure-containerregister) voor meer informatie.](../container-registry/container-registry-authentication-managed-identity.md)

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Een nieuw AKS-cluster maken met ACR-integratie

U kunt AKS- en ACR-integratie instellen tijdens het maken van uw AKS-cluster.  Om een AKS-cluster te laten communiceren met ACR, wordt Azure Active Directory **beheerde** identiteit gebruikt. Met de volgende CLI-opdracht kunt u een bestaande ACR in uw abonnement autoreren en de juiste **ACRPull-rol** configureren voor de beheerde identiteit. U kunt hieronder geldige waarden opgeven voor uw parameters.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

U kunt ook de ACR-naam opgeven met behulp van een ACR-resource-id, die de volgende indeling heeft:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> Als u een ACR gebruikt die zich in een ander abonnement van uw AKS-cluster bevindt, gebruikt u de ACR-resource-id bij het koppelen of loskoppelen van een AKS-cluster.

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Deze stap kan enkele minuten duren.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>ACR-integratie configureren voor bestaande AKS-clusters

Integreer een bestaande ACR met bestaande AKS-clusters door geldige waarden op te geven voor **acr-name** of **acr-resource-id,** zoals hieronder wordt weergegeven.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

Of

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

U kunt de integratie tussen een ACR en een AKS-cluster ook verwijderen met het volgende:

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

of

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Werken met ACR & AKS

### <a name="import-an-image-into-your-acr"></a>Een afbeelding importeren in uw ACR

Importeer een -afbeelding vanuit docker hub in uw ACR door het volgende uit te doen:


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>De voorbeeldafbeelding van ACR implementeren in AKS

Zorg ervoor dat u de juiste AKS-referenties hebt

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Maak een bestand met de **naam acr-nginx.yaml** dat het volgende bevat. Vervang de resourcenaam van het register door **acr-name**. Voorbeeld: *myContainerRegistry.*

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

Voer vervolgens deze implementatie uit in uw AKS-cluster:

```console
kubectl apply -f acr-nginx.yaml
```

U kunt de implementatie controleren door het volgende uit te uitvoeren:

```console
kubectl get pods
```

Als het goed is, hebt u twee pods die worden uitgevoerd.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>Problemen oplossen
* Voer de [opdracht az aks check-acr](/cli/azure/aks#az_aks_check_acr) uit om te controleren of het register toegankelijk is vanuit het AKS-cluster.
* Meer informatie over [ACR Diagnostics](../container-registry/container-registry-diagnostics-audit-logs.md)
* Meer informatie over [ACR Health](../container-registry/container-registry-check-health.md)

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks#az_aks_create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/