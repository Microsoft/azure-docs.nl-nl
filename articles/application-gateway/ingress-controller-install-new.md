---
title: Een ingangs controller maken met een nieuwe Application Gateway
description: Dit artikel bevat informatie over het implementeren van een Application Gateway ingangs controller met een nieuwe Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be5ac75e2da3eaeae300fd36e152a24c9777e64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99593736"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Een Application Gateway ingangs controller (AGIC) installeren met behulp van een nieuwe Application Gateway

In de onderstaande instructies wordt ervan uitgegaan dat Application Gateway ingangs controller (AGIC) wordt geïnstalleerd in een omgeving zonder bestaande onderdelen.

## <a name="required-command-line-tools"></a>Vereiste opdracht regel Programma's

U wordt aangeraden [Azure Cloud shell](https://shell.azure.com/) voor alle onderstaande opdracht regel bewerkingen uit te voeren. Start uw shell vanuit shell.azure.com of door op de koppeling te klikken:

[![Starten insluiten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)

U kunt ook Cloud Shell starten vanuit Azure Portal met behulp van het volgende pictogram:

![Portal starten](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

Uw [Azure Cloud shell](https://shell.azure.com/) beschikt al over alle benodigde hulpprogram ma's. Als u ervoor kiest om een andere omgeving te gebruiken, moet u ervoor zorgen dat de volgende opdracht regel Programma's zijn geïnstalleerd:

* `az` -Azure CLI: [installatie-instructies](/cli/azure/install-azure-cli)
* `kubectl` -Kubernetes opdracht regel programma: [installatie-instructies](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` -Kubernetes Package Manager: [installatie-instructies](https://github.com/helm/helm/releases/latest)
* `jq` -opdracht regel JSON-processor: [installatie-instructies](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Een identiteit maken

Volg de onderstaande stappen om een [Service-Principal-object](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)voor Azure Active Directory (Aad) te maken. Noteer de `appId` waarden, `password` en, en `objectId` deze worden in de volgende stappen gebruikt.

1. AD-service-principal maken ([meer informatie over Azure RBAC](../role-based-access-control/overview.md)):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    De `appId` `password` waarden en van de JSON-uitvoer worden in de volgende stappen gebruikt


1. Gebruik de `appId` van de vorige opdracht uit om de nieuwe service-principal op te halen `objectId` :
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    De uitvoer van deze opdracht is `objectId` , die wordt gebruikt in de onderstaande Azure Resource Manager sjabloon

1. Maak later het parameter bestand dat wordt gebruikt in de implementatie van de Azure Resource Manager sjabloon.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Als u een **KUBERNETES RBAC** -cluster wilt implementeren, stelt `aksEnableRBAC` u het veld in op `true`

## <a name="deploy-components"></a>Onderdelen implementeren
Met deze stap worden de volgende onderdelen aan uw abonnement toegevoegd:

- [Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Application Gateway](./overview.md) v2
- [Virtual Network](../virtual-network/virtual-networks-overview.md) met 2 [subnetten](../virtual-network/virtual-networks-overview.md)
- [Openbaar IP-adres](../virtual-network/virtual-network-public-ip-address.md)
- [Beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md), die wordt gebruikt door de [Aad pod-identiteit](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Down load de Azure Resource Manager sjabloon en wijzig de sjabloon naar wens.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Implementeer de Azure Resource Manager-sjabloon met `az cli` . Dit kan vijf minuten duren.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az deployment group create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. Zodra de implementatie is voltooid, downloadt u de implementatie-uitvoer naar een bestand met de naam `deployment-outputs.json` .
    ```azurecli
    az deployment group show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Application Gateway ingangs controller instellen

Met de instructies in de vorige sectie hebben we een nieuw AKS-cluster en een Application Gateway gemaakt en geconfigureerd. We zijn nu klaar om een voor beeld-app en een ingangs controller naar onze nieuwe Kubernetes-infra structuur te implementeren.

### <a name="setup-kubernetes-credentials"></a>Kubernetes-Referenties instellen
Voor de volgende stappen hebt u de opdracht [kubectl](https://kubectl.docs.kubernetes.io/) vereist, die we gebruiken om verbinding te maken met het nieuwe Kubernetes-cluster. [Cloud shell](https://shell.azure.com/) is `kubectl` al geïnstalleerd. We gebruiken `az` CLI om referenties voor Kubernetes te verkrijgen.

Referenties ophalen voor uw pas geïmplementeerde AKS ([meer informatie](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)):
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>AAD pod-identiteit installeren
  Azure Active Directory pod-identiteit biedt toegang tot [Azure Resource Manager (arm)](../azure-resource-manager/management/overview.md)op basis van tokens.

  Met de [Aad pod-identiteit](https://github.com/Azure/aad-pod-identity) worden de volgende onderdelen toegevoegd aan uw Kubernetes-cluster:
   * Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity` , `AzureAssignedIdentity` , `AzureIdentityBinding`
   * Onderdeel [Managed Identity controller (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * [NMI-onderdeel (node Managed Identity)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


De AAD pod-identiteit voor uw cluster installeren:

   - *RBAC Kubernetes ingeschakeld* AKS-cluster

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *KUBERNETES RBAC uitgeschakeld* AKS-cluster

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Helm installeren
[Helm](../aks/kubernetes-helm.md) is een pakket beheerder voor Kubernetes. We gebruiken dit om het pakket te installeren `application-gateway-kubernetes-ingress` :

1. Installeer [helm](../aks/kubernetes-helm.md) en voer het volgende uit om het helm-pakket toe te voegen `application-gateway-kubernetes-ingress` :

    - *RBAC Kubernetes ingeschakeld* AKS-cluster

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *KUBERNETES RBAC uitgeschakeld* AKS-cluster

        ```bash
        helm init
        ```

1. Voeg de AGIC helm-opslag plaats toe:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>De helm-grafiek van ingress-controller installeren

1. Gebruik het `deployment-outputs.json` bestand dat hierboven is gemaakt en maak de volgende variabelen.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Down load helm-config. yaml, waarmee AGIC wordt geconfigureerd:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    Of kopieer het onderstaande YAML-bestand: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Bewerk de zojuist gedownloade helm-config. yaml en vul de secties in `appgw` en `armAuth` .
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Waarden:
     - `verbosityLevel`: Hiermee stelt u het uitgebreidheids niveau in van de infra structuur voor AGIC-logboek registratie. Zie [registratie niveaus](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) voor mogelijke waarden.
     - `appgw.subscriptionId`: De ID van het Azure-abonnement waarin Application Gateway zich bevindt. Voorbeeld: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: De naam van de Azure-resource groep waarin Application Gateway is gemaakt. Voorbeeld: `app-gw-resource-group`
     - `appgw.name`: De naam van de Application Gateway. Voorbeeld: `applicationgatewayd0f0`
     - `appgw.shared`: Deze Booleaanse vlag moet worden standaard ingesteld op `false` . Ingesteld op `true` moet u een [gedeelde Application Gateway](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway)nodig hebben.
     - `kubernetes.watchNamespace`: Geef de naam ruimte op die AGIC moet volgen. Dit kan een enkele teken reeks waarde zijn of een door komma's gescheiden lijst met naam ruimten.
    - `armAuth.type`: mogelijk `aadPodIdentity` of `servicePrincipal`
    - `armAuth.identityResourceID`: Resource-ID van de door Azure beheerde identiteit
    - `armAuth.identityClientId`: De client-ID van de identiteit. Zie hieronder voor meer informatie over identiteiten
    - `armAuth.secretJSON`: Alleen vereist wanneer het geheim type van de Service-Principal is gekozen (wanneer `armAuth.type` is ingesteld op `servicePrincipal` ) 


   > [!NOTE]
   > De `identityResourceID` en `identityClientID` zijn waarden die zijn gemaakt tijdens de stappen voor het [implementeren van onderdelen](ingress-controller-install-new.md#deploy-components) en kunnen opnieuw worden verkregen met behulp van de volgende opdracht:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>` in de bovenstaande opdracht is de resource groep van uw Application Gateway. `<identity-name>` is de naam van de gemaakte identiteit. Alle identiteiten voor een bepaald abonnement kunnen worden weer gegeven met behulp van: `az identity list`


1. Installeer het Application Gateway ingangs controller pakket:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Een voor beeld-app installeren
Nu we Application Gateway, AKS en AGIC hebben geïnstalleerd, kunnen we een voor beeld-app installeren via [Azure Cloud shell](https://shell.azure.com/):

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

U kunt ook het volgende doen:

* Down load het bovenstaande YAML-bestand:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Het YAML-bestand Toep assen:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Andere voor beelden
In deze [hand leiding](ingress-controller-expose-service-over-http-https.md) vindt u meer voor beelden over het beschikbaar maken van een AKS-service via http of HTTPS, op Internet met Application Gateway.
