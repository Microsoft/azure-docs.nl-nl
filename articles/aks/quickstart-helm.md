---
title: Ontwikkelen op Azure Kubernetes service (AKS) met helm
description: Gebruik helm met AKS en Azure Container Registry om toepassings containers in een cluster te verpakken en uit te voeren.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 03/15/2021
ms.author: zarhoads
ms.openlocfilehash: 4f5232920853908aa5ad714313ead201494caa0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103493071"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Snelstartgids: ontwikkelen op Azure Kubernetes service (AKS) met helm

[Helm][helm] is een open-source-verpakkings programma waarmee u de levens cyclus van Kubernetes-toepassingen kunt installeren en beheren. Net als Linux-pakket beheer, zoals *apt* en *yum*, beheert helm Kubernetes-grafieken, die pakketten van vooraf geconfigureerde Kubernetes-resources zijn.

In deze Snelstartgids gebruikt u helm om een toepassing te verpakken en uit te voeren op AKS. Zie voor meer informatie over het installeren van een bestaande toepassing met behulp van helm de [installatie van bestaande toepassingen met helm in][helm-existing] de hand leiding voor AKS.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
* [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli).
* [Helm v3 geïnstalleerd][helm-install].

## <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken
U moet de container installatie kopieën opslaan in een Azure Container Registry (ACR) om uw toepassing uit te voeren in uw AKS-cluster met behulp van helm. Geef uw eigen register naam op die uniek is binnen Azure en met 5-50 alfanumerieke tekens. De SKU *Basic* is een toegangspunt voor ontwikkelingsdoeleinden dat is geoptimaliseerd voor kosten, met een balans tussen opslag en doorvoer.

In het onderstaande voor beeld wordt [AZ ACR Create][az-acr-create] gebruikt om een ACR met de naam *MyHelmACR* in *MyResourceGroup* te maken met de *basis* -SKU.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

Uitvoer is vergelijkbaar met het volgende voor beeld. Noteer uw *login server* -waarde voor uw ACR omdat u deze in een latere stap gaat gebruiken. In het onderstaande voor beeld is *myhelmacr.azurecr.io* de *login server* voor *myhelmacr*.

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

Het nieuwe AKS-cluster heeft toegang tot uw ACR nodig om de container installatie kopieën te halen en uit te voeren. Gebruik de volgende opdracht om:
* Maak een AKS-cluster met de naam *MyAKS* en koppel *MyHelmACR*.
* Verleen het *MyAKS* -cluster toegang tot uw *MyHelmACR* -ACR.


```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

## <a name="connect-to-your-aks-cluster"></a>Verbinding maken met uw AKS-cluster

Als u een Kubernetes-cluster lokaal wilt verbinden, gebruikt u de Kubernetes-opdracht regel client, [kubectl][kubectl]. `kubectl` is al geïnstalleerd als u Azure Cloud Shell gebruikt. 

1. `kubectl`Lokaal installeren met behulp van de `az aks install-cli` opdracht:

    ```azurecli
    az aks install-cli
    ```

2. Configureer `kubectl` om verbinding te maken met uw Kubernetes-cluster met behulp van de `az aks get-credentials` opdracht. In het volgende voor beeld van de opdracht worden referenties opgehaald voor het AKS-cluster met de naam *MyAKS* in de *MyResourceGroup*:  

    ```azurecli
    az aks get-credentials --resource-group MyResourceGroup --name MyAKS
    ```

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

Deze Snelstartgids maakt gebruik [van een voor beeld Node.js toepassing uit de opslag plaats voor de voor beelden van Azure dev Spaces][example-nodejs]. Kloon de toepassing van GitHub en navigeer naar de `dev-spaces/samples/nodejs/getting-started/webfrontend` map.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Een Dockerfile maken

Maak een nieuw *Dockerfile* -bestand met behulp van de volgende opdrachten:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>De voorbeeld toepassing bouwen en pushen naar de ACR

Met de voor gaande Dockerfile voert u de opdracht [AZ ACR build][az-acr-build] uit om een installatie kopie te bouwen en te pushen naar het REGI ster. `.`Aan het einde van de opdracht wordt de locatie van de Dockerfile (in dit geval de huidige map) ingesteld.

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>Uw helm-grafiek maken

Genereer uw helm-grafiek met behulp van de `helm create` opdracht.

```console
helm create webfrontend
```

*Webfrontend/values. yaml* bijwerken:
* Vervang het login server van het REGI ster dat u in een eerdere stap hebt genoteerd, zoals *myhelmacr.azurecr.io*.
* Wijzigen `image.repository` in `<loginServer>/webfrontend`
* Wijzigen `service.type` in `LoadBalancer`

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

Update `appVersion` `v1` in *webfrontend/Chart. yaml*. Bijvoorbeeld

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Uw helm-grafiek uitvoeren

Installeer uw toepassing met behulp van de opdracht in uw helm-grafiek `helm install` .

```console
helm install webfrontend webfrontend/
```

Het duurt enkele minuten voordat de service een openbaar IP-adres heeft geretourneerd. Bewaak de voortgang met de `kubectl get service` opdracht met het `--watch` argument.

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Navigeer naar de load balancer van uw toepassing in een browser met behulp van de `<EXTERNAL-IP>` om de voorbeeld toepassing te bekijken.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

Gebruik de opdracht [AZ Group delete][az-group-delete] om de resource groep, het AKS-cluster, het container register, de container installatie kopieën die zijn opgeslagen in de ACR en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal.
> 
> Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

Zie de helm-documentatie voor meer informatie over het gebruik van helm.

> [!div class="nextstepaction"]
> [Helm-documentatie][helm-documentation]

[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
