---
title: Ontwikkelen op Azure Kubernetes service (AKS) met helm
description: Gebruik helm met AKS en Azure Container Registry om toepassings containers in een cluster te verpakken en uit te voeren.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 0ca2d7ccc863e2208db1212ef3d3f10fa709d069
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006641"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Snelstartgids: ontwikkelen op Azure Kubernetes service (AKS) met helm

[Helm][helm] is een open-source-verpakkings programma waarmee u de levens cyclus van Kubernetes-toepassingen kunt installeren en beheren. Net als Linux-pakket beheerders, zoals *apt* en *yum*, wordt helm gebruikt voor het beheren van Kubernetes-grafieken, die pakketten van vooraf geconfigureerde Kubernetes-resources zijn.

In dit artikel leest u hoe u helm kunt gebruiken om een toepassing te verpakken en uit te voeren op AKS. Zie [bestaande toepassingen met helm installeren in AKS][helm-existing]voor meer informatie over het installeren van een bestaande toepassing met behulp van helm.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free) maken.
* [Azure CLI geïnstalleerd](/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Helm v3 geïnstalleerd][helm-install].

## <a name="create-an-azure-container-registry"></a>Een Azure Container Registry maken
Als u helm wilt gebruiken om uw toepassing uit te voeren in uw AKS-cluster, hebt u een Azure Container Registry nodig om de container installatie kopieën op te slaan. In het onderstaande voor beeld wordt [AZ ACR Create][az-acr-create] gebruikt om een ACR te maken met de naam *MyHelmACR* in de resource groep *MyResourceGroup* met de *basis* -SKU. U moet een eigen unieke register naam opgeven. De registernaam moet uniek zijn binnen Azure en mag 5 tot 50 alfanumerieke tekens bevatten. De SKU *Basic* is een toegangspunt voor ontwikkelingsdoeleinden dat is geoptimaliseerd voor kosten, met een balans tussen opslag en doorvoer.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

De uitvoer lijkt op die in het volgende voorbeeld. Noteer de waarde *login server* voor uw ACR omdat deze wordt gebruikt in een latere stap. In het onderstaande voor beeld is *myhelmacr.azurecr.io* de *login server* voor *myhelmacr*.

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

## <a name="create-an-azure-kubernetes-service-cluster"></a>Een Azure Kubernetes Service-cluster maken

Maak een AKS-cluster. Met de onderstaande opdracht maakt u een AKS-cluster met de naam MyAKS en koppelt u MyHelmACR.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

Uw AKS-cluster heeft toegang tot uw ACR nodig om de container installatie kopieën op te halen en uit te voeren. Met de bovenstaande opdracht verleent u ook de *MyAKS* -cluster toegang tot uw *MyHelmACR* -ACR.

## <a name="connect-to-your-aks-cluster"></a>Verbinding maken met uw AKS-cluster

Gebruik [kubectl][kubectl], de Kubernetes-opdrachtregelclient, als u vanaf uw lokale computer verbinding wilt maken met het Kubernetes-cluster.

Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u het lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli][]:

```azurecli
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials][] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. In het volgende voor beeld worden referenties opgehaald voor het AKS-cluster met de naam *MyAKS* in de *MyResourceGroup*:

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

Maak een nieuw *Dockerfile* -bestand met behulp van het volgende:

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

Gebruik de opdracht [AZ ACR build][az-acr-build] om een installatie kopie te bouwen en te pushen naar het REGI ster, met behulp van de voor gaande Dockerfile. Met de `.` aan het einde van de opdracht wordt de locatie van de Dockerfile ingesteld, in dit geval de huidige map.

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

Voer de volgende updates uit op *webfrontend/values. yaml*. Vervang de login server van het REGI ster die u in een eerdere stap hebt genoteerd, zoals *myhelmacr.azurecr.io*:

* Wijzigen `image.repository` in `<loginServer>/webfrontend`
* Wijzigen `service.type` in `LoadBalancer`

Bijvoorbeeld:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: *myhelmacr.azurecr.io*/webfrontend
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

Gebruik de `helm install` opdracht voor het installeren van uw toepassing met behulp van uw helm-grafiek.

```console
helm install webfrontend webfrontend/
```

Het duurt enkele minuten voordat de service een openbaar IP-adres heeft geretourneerd. Als u de voortgang wilt bewaken, gebruikt u de `kubectl get service` opdracht met de para meter *Watch* :

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Navigeer naar het load balancer van uw toepassing in een browser met behulp van de `<EXTERNAL-IP>` om de voorbeeld toepassing te bekijken.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

Wanneer het cluster niet meer nodig is, gebruikt u de opdracht [AZ Group delete][az-group-delete] om de resource groep, het AKS-cluster, het container register, de container installatie kopieën die daar zijn opgeslagen en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Wanneer u het cluster verwijdert, wordt de Azure Active Directory-service-principal die door het AKS-cluster wordt gebruikt niet verwijderd. Zie [Overwegingen voor en verwijdering van AKS service-principal][sp-delete] voor stappen voor het verwijderen van de service-principal. Als u een beheerde identiteit hebt gebruikt, wordt de identiteit beheerd door het platform en hoeft deze niet te worden verwijderd.

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
