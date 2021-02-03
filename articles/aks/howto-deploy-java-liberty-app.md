---
title: Een Java-toepassing met open vrijheid of WebSphere vrijheid implementeren op een Azure Kubernetes service-cluster (AKS)
description: Implementeer een Java-toepassing met open vrijheid of WebSphere vrijheid op een Azure Kubernetes service-cluster (AKS).
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: Java, jakartaee, javaee, microprofile, open-vrijheid, WebSphere-vrijheid, AKS, kubernetes
ms.openlocfilehash: 4d6e335cd4b522593091094ac6251acc97873208
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508050"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>Een Java-toepassing met open vrijheid of WebSphere vrijheid implementeren op een Azure Kubernetes service-cluster (AKS)

In deze hand leiding wordt gedemonstreerd hoe u uw Java-, Java EE-, [Jakarta ee](https://jakarta.ee/)-of [microprofile](https://microprofile.io/) -toepassing uitvoert op de open vrijheids-of WebSphere vrijheids-runtime en vervolgens de container toepassing IMPLEMENTeert op een AKS-cluster met behulp van de open vrijheids operator. De open vrijheids operator vereenvoudigt de implementatie en het beheer van toepassingen die worden uitgevoerd op open vrijheids Kubernetes-clusters. U kunt ook geavanceerdere bewerkingen uitvoeren, zoals het verzamelen van traceringen en dumps met behulp van de operator. In dit artikel wordt stapsgewijs uitgelegd hoe u een vrijheids toepassing voorbereidt, de Application docker-installatie kopie bouwt en de container toepassing uitvoert op een AKS-cluster.  Zie voor meer informatie over open vrijheid [de open vrijheid project-pagina](https://openliberty.io/). Zie [de product pagina van WebSphere vrijheid](https://www.ibm.com/cloud/websphere-liberty)voor meer informatie over de WebSphere vrijheid van IBM.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Voor dit artikel is de nieuwste versie van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.
* Als u de opdrachten in deze hand leiding lokaal uitvoert (in plaats van Azure Cloud Shell):
  * Bereid een lokale computer voor met een UNIX-soortgelijk besturings systeem (bijvoorbeeld Ubuntu, macOS).
  * Installeer een Java-SE-implementatie (bijvoorbeeld [AdoptOpenJDK openjdk 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
  * Installeer [maven](https://maven.apache.org/download.cgi) 3.5.0 of hoger.
  * Installeer [docker](https://docs.docker.com/get-docker/) voor uw besturings systeem.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een Azure-resourcegroep is een logische groep waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resource groep, *Java-vrijheids project* met behulp van de opdracht [AZ Group Create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create) op de locatie *eastus* . Het wordt gebruikt voor het maken van het ACR-exemplaar (Azure Container Registry) en het AKS-cluster later. 

```azurecli-interactive
az group create --name java-liberty-project --location eastus
```

## <a name="create-an-acr-instance"></a>Een ACR-exemplaar maken

Gebruik de opdracht [AZ ACR Create](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az_acr_create) om het ACR-exemplaar te maken. In het volgende voor beeld wordt een ACR-exemplaar gemaakt met de naam *youruniqueacrname*. Zorg ervoor dat *youruniqueacrname* uniek is binnen Azure.

```azurecli-interactive
az acr create --resource-group java-liberty-project --name youruniqueacrname --sku Basic --admin-enabled
```

Na een korte periode ziet u een JSON-uitvoer met de volgende informatie:

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>Verbinding maken met het ACR-exemplaar

Als u een installatie kopie naar het ACR-exemplaar wilt pushen, moet u zich eerst aanmelden. Voer de volgende opdrachten uit om de verbinding te controleren:

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

U ziet `Login Succeeded` aan het einde van de opdracht uitvoer als u zich hebt aangemeld bij het ACR-exemplaar.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

Gebruik de opdracht [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create) om een AKS-cluster te maken. In het volgende voorbeeld wordt een cluster met de naam *myAKSCluster* gemaakt met één knooppunt. Dit zal enkele minuten in beslag nemen.

```azurecli-interactive
az aks create --resource-group java-liberty-project --name myAKSCluster --node-count 1 --generate-ssh-keys --enable-managed-identity
```

Na enkele minuten is de opdracht voltooid en retourneert deze informatie over de JSON-indeling over het cluster, met inbegrip van het volgende:

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>Verbinding maken met het AKS-cluster

Als u een Kubernetes-cluster wilt beheren, gebruikt u [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/), de Kubernetes-opdrachtregelclient. Als u Azure Cloud Shell gebruikt, is `kubectl` al geïnstalleerd. Als u `kubectl` lokaal wilt installeren, gebruikt u de opdracht [az aks install-cli](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_install_cli):

```azurecli-interactive
az aks install-cli
```

Gebruik de opdracht [az aks get-credentials](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials) om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli-interactive
az aks get-credentials --resource-group java-liberty-project --name myAKSCluster --overwrite-existing
```

> [!NOTE]
> De bovenstaande opdracht maakt gebruik van de standaardlocatie voor het [Kubernetes-configuratiebestand](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), namelijk `~/.kube/config`. U kunt een andere locatie voor uw Kubernetes-configuratiebestand opgeven door *--file* te gebruiken.

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) om een lijst met clusterknooppunten te retourneren.

```azurecli-interactive
kubectl get nodes
```

In de volgende voorbeelduitvoer ziet u het enkele knooppunt dat is gemaakt in de vorige stappen. Zorg ervoor dat de status van het knooppunt *Ready* is:

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Open bare-vrijheids operator installeren

Nadat u een verbinding hebt gemaakt met het cluster, installeert u de [Open vrijheids operator](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0) door de volgende opdrachten uit te voeren.

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>Toepassings installatie kopie maken

Als u uw vrijheids toepassing wilt implementeren en uitvoeren op het AKS-cluster, kunt u uw toepassing container plaatsen als een docker-installatie kopie met behulp van [Open-vrijheids container installatie kopieën](https://github.com/OpenLiberty/ci.docker) of [WebSphere vrijheids container installatie kopieën](https://github.com/WASdev/ci.docker).

1. Kloon de voorbeeld code voor deze hand leiding. Het voor beeld bevindt zich op [github](https://github.com/Azure-Samples/open-liberty-on-aks).
1. Wijzig de map in `javaee-app-simple-cluster` uw lokale kloon.
1. Voer uit `mvn clean package` om de toepassing te verpakken.
1. Voer een van de volgende opdrachten uit om de installatie kopie van de toepassing te bouwen en deze naar het ACR-exemplaar te pushen.
   * Bouw met een open vrijheids basis installatie kopie als u de voor keur geeft open vrijheid als een licht gewicht open source java-™ runtime:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * Bouw met WebSphere vrijheid-basis installatie kopie als u liever een commerciële versie van open vrijheid gebruikt:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>Toepassing implementeren op het AKS-cluster

Volg de onderstaande stappen om de vrijheids toepassing op het AKS-cluster te implementeren.

1. Maak een pull-geheim zodat het AKS-cluster wordt geverifieerd voor het ophalen van installatie kopie uit het ACR-exemplaar.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. Controleer of de huidige werkmap `javaee-app-simple-cluster` van uw lokale kloon is.
1. Voer de volgende opdrachten uit om uw vrijheids toepassing te implementeren met drie replica's naar het AKS-cluster. De uitvoer van de opdracht wordt ook inline weer gegeven.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. Wacht totdat u ziet `3/3` in de `READY` kolom en `3` onder de `AVAILABLE` kolom, gebruik `CTRL-C` om het `kubectl` controle proces te stoppen.

### <a name="test-the-application"></a>De toepassing testen

Wanneer de toepassing wordt uitgevoerd, wordt de front-end van de toepassing door een Kubernetes load balancer-service op internet weer gegeven. Het kan even duren voordat dit proces is voltooid.

Gebruik de opdracht [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) met het argument `--watch` om de voortgang te controleren.

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   9080:31732/TCP   68s
```

Wacht tot het *externe IP-* adres is gewijzigd van *in behandeling* in een openbaar IP-adres, gebruik `CTRL-C` om het `kubectl` controle proces te stoppen.

Open een webbrowser op het externe IP-adres en de poort van uw service ( `52.152.189.57:9080` voor het bovenstaande voor beeld) om de start pagina van de toepassing weer te geven. U ziet de naam van de pod van uw toepassings replica's weer gegeven in de linkerbovenhoek van de pagina. Wacht een paar minuten en vernieuw de pagina. u ziet waarschijnlijk een andere pod-naam die wordt weer gegeven als gevolg van de taak verdeling van het AKS-cluster.

:::image type="content" source="./media/howto-deploy-java-liberty-app/deploy-succeeded.png" alt-text="De Java-vrijheids toepassing is geïmplementeerd op AKS":::

>[!NOTE]
> - De toepassing maakt momenteel geen gebruik van HTTPS. Het is raadzaam om [TLS IN TE SCHAKELEN voor uw eigen certificaten](ingress-own-tls.md).

## <a name="clean-up-the-resources"></a>Resources opschonen

Om Azure-kosten te vermijden, moet u overbodige resources opschonen.  Wanneer het cluster niet meer nodig is, gebruikt u de opdracht [AZ Group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) om de resource groep, container service, container Registry en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete --name java-liberty-project --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding vindt u meer informatie over de referenties die worden gebruikt:

* [Azure Kubernetes Service](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Open vrijheid](https://openliberty.io/)
* [Open vrijheids operator](https://github.com/OpenLiberty/open-liberty-operator)
* [Configuratie van vrijheids server openen](https://openliberty.io/docs/ref/config/)
* [Vrijheid maven-invoeg toepassing](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Installatie kopieën van vrijheids containers openen](https://github.com/OpenLiberty/ci.docker)
* [WebSphere vrijheid container-installatie kopieën](https://github.com/WASdev/ci.docker)
