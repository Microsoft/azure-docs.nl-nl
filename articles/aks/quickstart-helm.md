---
title: Ontwikkelen op Azure Kubernetes Service (AKS) met Helm
description: Gebruik Helm met AKS en Azure Container Registry om toepassingscontainers in een cluster te verpakken en uit te voeren.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: e293d0c58f265b25f3df0a218f84888467468f59
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767489"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Quickstart: Ontwikkelen op Azure Kubernetes Service (AKS) met Helm

[Helm][helm] is een opensource-pakketprogramma waarmee u de levenscyclus van Kubernetes-toepassingen kunt installeren en beheren. Helm is vergelijkbaar met Linux-pakketbeheerders zoals *APT* en *Yum* en beheert Kubernetes-grafieken. Dit zijn pakketten van vooraf geconfigureerde Kubernetes-resources.

In deze quickstart gebruikt u Helm om een toepassing in AKS te verpakken en uit te voeren. Zie de handleiding Bestaande toepassingen installeren met [Helm in AKS][helm-existing] voor meer informatie over het installeren van een bestaande toepassing met Helm.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
* [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli).
* [Helm v3 geïnstalleerd.][helm-install]

## <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken
U moet uw containerafbeeldingen opslaan in een Azure Container Registry (ACR) om uw toepassing in uw AKS-cluster uit te voeren met behulp van Helm. Geef uw eigen registernaam op die uniek is in Azure en 5-50 alfanumerieke tekens bevat. De SKU *Basic* is een toegangspunt voor ontwikkelingsdoeleinden dat is geoptimaliseerd voor kosten, met een balans tussen opslag en doorvoer.

In het onderstaande voorbeeld [wordt az acr create gebruikt][az-acr-create] om een ACR met de naam *MyHelmACR* te maken in *MyResourceGroup* met de Basic-SKU. 

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

De uitvoer is vergelijkbaar met het volgende voorbeeld. Noteer de *waarde van loginServer* voor uw ACR, aangezien u deze in een latere stap gaat gebruiken. In het onderstaande *voorbeeld myhelmacr.azurecr.io* de *loginServer voor* *MyHelmACR.*

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Uw nieuwe AKS-cluster moet toegang hebben tot uw ACR om de container-afbeeldingen op te halen en uit te voeren. Gebruik de volgende opdracht om:
* Maak een AKS-cluster met de *naam MyAKS* en koppel *MyHelmACR.*
* Verleen het *MyAKS-cluster* toegang tot uw *MyHelmACR* ACR.


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>Verbinding maken met uw AKS-cluster

Als u een Kubernetes-cluster lokaal wilt verbinden, gebruikt u de Kubernetes-opdrachtregelclient, [kubectl][kubectl]. `kubectl` is al geïnstalleerd als u Azure Cloud Shell. 

1. Installeer `kubectl` lokaal met behulp van de opdracht `az aks install-cli` :

    ```azurecli
    az aks install-cli
    ```

2. Configureer `kubectl` om verbinding te maken met uw Kubernetes-cluster met behulp van de opdracht `az aks get-credentials` . In het volgende opdrachtvoorbeeld worden referenties voor het AKS-cluster met de *naam MyAKS* in *myResourceGroup opgeslagen:*  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

In deze quickstart wordt [een voorbeeld van Node.js toepassing uit de voorbeeldopslagplaats van Azure Dev Spaces gebruikt.][example-nodejs] Kloon de toepassing vanuit GitHub en navigeer naar de `dev-spaces/samples/nodejs/getting-started/webfrontend` map .

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Een Dockerfile maken

Maak een nieuw *Dockerfile-bestand* met behulp van de volgende opdrachten:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>De voorbeeldtoepassing bouwen en pushen naar de ACR

Voer met behulp van het voorgaande Dockerfile de [opdracht az acr build][az-acr-build] uit om een afbeelding te bouwen en naar het register te pushen. De `.` aan het einde van de opdracht stelt de locatie van de Dockerfile (in dit geval de huidige map).

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>Uw Helm-grafiek maken

Genereer uw Helm-grafiek met behulp van de `helm create` opdracht .

```console
helm create webfrontend
```

Werk *webfrontend/values.yaml bij:*
* Vervang de loginServer van uw register die u in een eerdere stap hebt genoteerd, *zoals myhelmacr.azurecr.io*.
* Wijzig `image.repository` in `<loginServer>/webfrontend`
* Wijzig `service.type` in `LoadBalancer`

Bijvoorbeeld:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: myhelmacr.azurecr.io/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Werk `appVersion` bij naar in `v1` *webfrontend/Chart.yaml*. Bijvoorbeeld

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Uw Helm-grafiek uitvoeren

Installeer uw toepassing met behulp van uw Helm-grafiek met behulp van de `helm install` opdracht .

```console
helm install webfrontend webfrontend/
```

Het duurt enkele minuten voordat de service een openbaar IP-adres retournt. Controleer de voortgang met `kubectl get service` behulp van de opdracht met het argument `--watch` .

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Navigeer naar de load balancer van uw toepassing in een browser met behulp van de `<EXTERNAL-IP>` om de voorbeeldtoepassing te bekijken.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

Gebruik de [opdracht az group delete][az-group-delete] om de resourcegroep, het AKS-cluster, het containerregister, de containerafbeeldingen die zijn opgeslagen in de ACR en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal.
> 
> Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zie de Helm-documentatie voor meer informatie over het gebruik van Helm.

> [!div class="nextstepaction"]
> [Helm-documentatie][helm-documentation]

[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-group-delete]: /cli/azure/group#az_group_delete
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
