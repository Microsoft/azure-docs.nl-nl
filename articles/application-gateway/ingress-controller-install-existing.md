---
title: Een ingangs controller maken met een bestaande Application Gateway
description: Dit artikel bevat informatie over het implementeren van een Application Gateway ingangs controller met een bestaande Application Gateway.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 2f585dd80219afac7c67eebabd72cb41dce0b673
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874505"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>Een Application Gateway ingangs controller (AGIC) installeren met behulp van een bestaande Application Gateway

De Application Gateway ingangs controller (AGIC) is een pod in uw Kubernetes-cluster.
Met AGIC worden de [Kubernetes-](https://kubernetes.io/docs/concepts/services-networking/ingress/) bronnen bewaakt en worden Application Gateway config gemaakt en toegepast op basis van de status van het Kubernetes-cluster.

## <a name="outline"></a>Overzichtscode4
- [Vereisten](#prerequisites)
- [Azure Resource Manager-verificatie (ARM)](#azure-resource-manager-authentication)
    - Optie 1: [Aad-pod instellen](#set-up-aad-pod-identity) en Azure-identiteit maken op wapen
    - Optie 2: [een Service-Principal gebruiken](#using-a-service-principal)
- [De ingangs controller installeren met behulp van helm](#install-ingress-controller-as-a-helm-chart)
- [Multi-cluster/gedeelde Application Gateway](#multi-cluster--shared-application-gateway): Installeer AGIC in een omgeving, waarbij Application Gateway wordt gedeeld tussen een of meer AKS-clusters en/of andere Azure-onderdelen.

## <a name="prerequisites"></a>Vereisten
In dit document wordt ervan uitgegaan dat u de volgende hulpprogram ma's en infra structuur al hebt geïnstalleerd:
- [AKS](https://azure.microsoft.com/services/kubernetes-service/) met [geavanceerde netwerken](../aks/configure-azure-cni.md) ingeschakeld
- [Application Gateway v2](./tutorial-autoscale-ps.md) in hetzelfde virtuele netwerk als AKS
- De [Aad pod-identiteit](https://github.com/Azure/aad-pod-identity) is geïnstalleerd op uw AKS-cluster
- [Cloud shell](https://shell.azure.com/) is de Azure shell-omgeving, die `az` CLI, `kubectl` en `helm` geïnstalleerd heeft. Deze hulpprogram ma's zijn vereist voor de onderstaande opdrachten.

Maak __een back-up van de configuratie van uw Application Gateway voordat u__ AGIC installeert:
  1. [Azure Portal](https://portal.azure.com/) naar uw exemplaar navigeren `Application Gateway`
  2. van `Export template` klikken `Download`

Het zip-bestand dat u hebt gedownload, heeft JSON-sjablonen, bash-en Power shell-scripts die u kunt gebruiken om app-gateway te herstellen, die nodig is

## <a name="install-helm"></a>Helm installeren
[Helm](../aks/kubernetes-helm.md) is een pakket beheerder voor Kubernetes. We gebruiken dit om het pakket te installeren `application-gateway-kubernetes-ingress` .
Gebruik [Cloud shell](https://shell.azure.com/) om helm te installeren:

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

## <a name="azure-resource-manager-authentication"></a>Azure Resource Manager-verificatie

AGIC communiceert met de Kubernetes-API-server en de Azure Resource Manager. Hiervoor is een identiteit vereist voor toegang tot deze Api's.

## <a name="set-up-aad-pod-identity"></a>AAD pod-identiteit instellen

De [Aad pod-identiteit](https://github.com/Azure/aad-pod-identity) is een controller, VERGELIJKBAAR met AGIC, die ook wordt uitgevoerd op uw AKS. Azure Active Directory-identiteiten worden aan uw Kubernetes-peul gebonden. De identiteit van een toepassing in een Kubernetes-Pod is vereist om te kunnen communiceren met andere Azure-onderdelen. In het specifieke geval hebt u autorisatie nodig voor de AGIC-pod om HTTP-aanvragen te kunnen maken voor [arm](../azure-resource-manager/management/overview.md).

Volg de [installatie-instructies voor de Aad pod-identiteit](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) om dit onderdeel toe te voegen aan uw AKS.

Daarna moet u een Azure-identiteit maken en de IT-machtigingen ARM bieden.
Gebruik [Cloud shell](https://shell.azure.com/) om alle van de volgende opdrachten uit te voeren en een identiteit te maken:

1. Maak een Azure-identiteit **in dezelfde resource groep als de AKS-knoop punten**. Het is belang rijk dat u de juiste resource groep kiest. De resource groep die is vereist in de onderstaande opdracht, is *niet* het account dat wordt verwezen in het deel venster AKS-Portal. Dit is de resource groep van de `aks-agentpool` virtuele machines. Normaal gesp roken begint de resource groep met `MC_` en bevat de naam van uw AKS. Bijvoorbeeld: `MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. Voor de opdrachten voor roltoewijzingen die hieronder worden beschreven, moeten we `principalId` voor de zojuist gemaakte identiteit verkrijgen:

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Geef de identiteit `Contributor` toegang tot uw Application Gateway. Hiervoor hebt u de ID van de Application Gateway nodig. deze ziet er ongeveer als volgt uit: `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    De lijst met Application Gateway-Id's in uw abonnement ophalen met: `az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Geef de identiteit `Reader` toegang tot de Application Gateway resource groep. De resource groep-ID ziet er als volgt uit: `/subscriptions/A/resourceGroups/B` . U kunt alle resource groepen ophalen met: `az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>Een Service-Principal gebruiken
Het is ook mogelijk om AGIC toegang tot ARM te bieden via een Kubernetes-geheim.

1. Een Active Directory Service-Principal maken en coderen met base64. De base64-code ring is vereist voor het opslaan van de JSON-Blob in Kubernetes.

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. Voeg de met base64 gecodeerde JSON-BLOB toe aan het `helm-config.yaml` bestand. Meer informatie over `helm-config.yaml` vindt u in de volgende sectie.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>De ingangs controller installeren als een helm-grafiek
In de eerste paar stappen installeert helm de Tiller op uw Kubernetes-cluster. Gebruik [Cloud shell](https://shell.azure.com/) om het AGIC helm-pakket te installeren:

1. De `application-gateway-kubernetes-ingress` helm-opslag plaats toevoegen en een helm-update uitvoeren

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
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
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Bewerk helm-config. yaml en vul de waarden voor `appgw` en in `armAuth` .
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > De `<identity-resource-id>` en `<identity-client-id>` zijn de eigenschappen van de Azure AD-identiteit die u in de vorige sectie hebt ingesteld. U kunt deze gegevens ophalen door de volgende opdracht uit te voeren: `az identity show -g <resourcegroup> -n <identity-name>` , waarbij de `<resourcegroup>` resource groep is waarin het cluster object op het hoogste niveau AKS, Application Gateway en beheerde identiteiten worden geïmplementeerd.

1. Helm-grafiek installeren `application-gateway-kubernetes-ingress` met de `helm-config.yaml` configuratie uit de vorige stap

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    U kunt ook de `helm-config.yaml` en de opdracht helm combi neren in één stap:
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. Controleer het logboek van de zojuist gemaakte pod om te controleren of deze correct is gestart

Raadpleeg [deze hand leiding](ingress-controller-expose-service-over-http-https.md) voor informatie over hoe u een AKS-service via http of https kunt weer geven op internet met behulp van een Azure-toepassing gateway.



## <a name="multi-cluster--shared-application-gateway"></a>Multi-cluster/gedeelde Application Gateway
Standaard AGIC neemt de volledige eigendom van het Application Gateway waaraan deze is gekoppeld. AGIC versie 0.8.0 en hoger kunnen één Application Gateway delen met andere Azure-onderdelen. We kunnen bijvoorbeeld hetzelfde Application Gateway gebruiken voor een app die wordt gehost op virtuele-machine schaal sets en een AKS-cluster.

Maak __een back-up van de configuratie van uw Application Gateway voordat u__ deze instelling inschakelt:
  1. [Azure Portal](https://portal.azure.com/) naar uw exemplaar navigeren `Application Gateway`
  2. van `Export template` klikken `Download`

Het zip-bestand dat u hebt gedownload, heeft JSON-sjablonen, bash-en Power shell-scripts die u kunt gebruiken om Application Gateway te herstellen

### <a name="example-scenario"></a>Voorbeeldscenario
Laten we eens kijken naar een imaginaire Application Gateway, waarmee het verkeer voor twee websites wordt beheerd:
  - `dev.contoso.com` -gehost op een nieuwe AKS met behulp van Application Gateway en AGIC
  - `prod.contoso.com` -gehost op een [virtuele-machine-schaalset van Azure](https://azure.microsoft.com/services/virtual-machine-scale-sets/)

Met de standaard instellingen van AGIC wordt ervan uitgegaan dat 100% eigenaar is van de Application Gateway deze wordt verwijzen. AGIC overschrijft alle configuratie van de app-gateway. Als u hand matig een listener moet maken voor `prod.contoso.com` (op Application Gateway) zonder deze te definiëren in de Kubernetes-inkomen, wordt de `prod.contoso.com` configuratie binnen enkele seconden door AGIC verwijderd.

Als u AGIC wilt installeren en ook `prod.contoso.com` van onze virtuele machine Scale set machines, moet u AGIC beperken voor het configureren van `dev.contoso.com` alleen. Dit wordt vergemakkelijkt door de volgende [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)te instantiëren:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

Met de bovenstaande opdracht maakt u een `AzureIngressProhibitedTarget` object. Dit maakt AGIC (versie 0.8.0 en hoger) op de hoogte van het bestaan van Application Gateway configuratie voor `prod.contoso.com` en geeft dit expliciet aan om te voor komen dat een configuratie met betrekking tot die hostnaam wordt gewijzigd.


### <a name="enable-with-new-agic-installation"></a>Inschakelen met de nieuwe AGIC-installatie
Als u AGIC (versie 0.8.0 en hoger) wilt beperken tot een subset van de Application Gateway configuratie, wijzigt u de `helm-config.yaml` sjabloon.
Voeg onder de `appgw:` sectie `shared` sleutel toe en stel deze in op aan `true` .

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

De helm-wijzigingen Toep assen:
  1. Zorg ervoor dat de `AzureIngressProhibitedTarget` CRD is geïnstalleerd met:
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. Helm bijwerken:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

Als gevolg hiervan krijgt uw AKS een nieuw exemplaar van de `AzureIngressProhibitedTarget` naam `prohibit-all-targets` :
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

Het object `prohibit-all-targets` , zoals de naam al aangeeft, voor komt dat AGIC config voor *een* host en pad wijzigt.
Met helm Install with `appgw.shared=true` wordt AGIC geïmplementeerd, maar worden er geen wijzigingen aangebracht in Application Gateway.


### <a name="broaden-permissions"></a>Machtigingen uitbreiden
Sinds helm met `appgw.shared=true` en de standaard `prohibit-all-targets` blokken AGIC geen enkele configuratie toe te passen.

AGIC-machtigingen uitbreiden met:
1. Maak een nieuw `AzureIngressProhibitedTarget` met uw specifieke installatie:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. Pas nadat u uw eigen aangepaste verbod hebt gemaakt, kunt u de standaard instelling verwijderen. Dit is te breed:

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>Inschakelen voor een bestaande AGIC-installatie
We gaan ervan uit dat we al een werkende AKS, Application Gateway en geconfigureerde AGIC in het cluster hebben. Er is een ingang voor `prod.contosor.com` en het verkeer wordt door aks. We willen graag toevoegen `staging.contoso.com` aan onze bestaande Application Gateway, maar moeten deze hosten op een [virtuele machine](https://azure.microsoft.com/services/virtual-machines/). We gaan de bestaande Application Gateway opnieuw gebruiken en hand matig een listener en back-end-Pools configureren voor `staging.contoso.com` . Het hand matig aanpassen van Application Gateway configuratie (via [Portal](https://portal.azure.com), [arm-api's](/rest/api/resources/) of [terraform](https://www.terraform.io/)) zou echter conflicteren met de veronderstellingen van het volledige eigendom van AGIC. Kort nadat we wijzigingen hebben toegepast, worden ze door AGIC overschreven of verwijderd.

We kunnen verhinderen dat AGIC wijzigingen in een subset van de configuratie aanbrengt.

1. Een `AzureIngressProhibitedTarget` object maken:
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. Bekijk het object dat u zojuist hebt gemaakt:
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. Application Gateway configuratie wijzigen via portal: Voeg listeners, routerings regels, back-ends, enzovoort toe. Het nieuwe object dat we hebben gemaakt ( `manually-configured-staging-environment` ) voor komt dat AGIC overschreven Application Gateway configuratie met betrekking tot `staging.contoso.com` .