---
title: Problemen met de Application Gateway-controller oplossen
description: Dit artikel bevat informatie over het oplossen van veelvoorkomende vragen en/of problemen met de Application Gateway ingangs controller.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/18/2020
ms.author: caya
ms.openlocfilehash: f2b9f79f0914e645c736f8a577c46baa42587332
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874607"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Veelvoorkomende vragen of problemen met de ingangs controller oplossen

[Azure Cloud shell](https://shell.azure.com/) is de handigste manier om problemen met uw AKS-en AGIC-installatie op te lossen. Start uw shell vanuit [shell.Azure.com](https://shell.azure.com/) of door op de koppeling te klikken:

[![Starten insluiten](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell starten")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Testen met een eenvoudige Kubernetes-app

In de onderstaande stappen wordt ervan uitgegaan:
  - U hebt een AKS-cluster met geavanceerde netwerken ingeschakeld
  - AGIC is geïnstalleerd op het AKS-cluster
  - U hebt al een Application Gateway in een VNET dat is gedeeld met uw AKS-cluster

Als u wilt controleren of de installatie van Application Gateway + AKS + AGIC correct is ingesteld, implementeert u de eenvoudigste mogelijke app:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
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
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Kopieer en plak alle regels vanuit het bovenstaande script in een [Azure Cloud shell](https://shell.azure.com/). Zorg ervoor dat de volledige opdracht is gekopieerd, beginnend met `cat` en met inbegrip van de laatste `EOF` .

![toepassen](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Nadat een geslaagde implementatie van de app boven uw AKS-cluster een nieuwe Pod, dienst en ingang heeft.

De lijst met peulen ophalen met [Cloud shell](https://shell.azure.com/): `kubectl get pods -o wide` .
Er wordt verwacht dat er een pod met de naam ' test-agic-app-pod ' is gemaakt. Het heeft een IP-adres. Dit adres moet zich binnen het VNET van de Application Gateway bevindt, dat wordt gebruikt met AKS.

![Scherm opname van het bash-venster in Azure Cloud Shell met een lijst met de namen van de test-agic-app-pod in de lijst.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

De lijst met services ophalen: `kubectl get services -o wide` . Verwacht wordt dat er een service met de naam ' test-agic-app-service ' wordt weer geven.

![Scherm opname van het bash-venster in Azure Cloud Shell een lijst met Services met een test-agic-app-pod in de lijst weer gegeven.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

De lijst met ingresses ophalen: `kubectl get ingress` . Er wordt verwacht dat een ingangs bron met de naam ' test-agic-app-ingang ' is gemaakt. De resource heeft de hostnaam ' test.agic.contoso.com '.

![Scherm opname van het bash-venster in Azure Cloud Shell waarin een lijst met ingresses wordt weer gegeven met test-agic-app-ingang in de lijst.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Een van de peulen is AGIC. `kubectl get pods` Er wordt een lijst weer gegeven met een van de peulen waarvan een van deze begint met ' Inkomend-Azure '. Ontvang alle logboeken van die pod `kubectl logs <name-of-ingress-controller-pod>` om te controleren of de implementatie is geslaagd. Bij een geslaagde implementatie zijn de volgende regels aan het logboek toegevoegd:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

U kunt ook vanaf [Cloud shell](https://shell.azure.com/) alleen de regels ophalen met een geslaagde Application Gateway configuratie met `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'` , waarbij u `<ingress-azure....>` de exacte naam van de AGIC pod.

Application Gateway wordt de volgende configuratie toegepast:

- Listener: ![ listener](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Routerings regel: ![ routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Back-end-groep:
  - Er is één IP-adres in de back-endadresgroep en dit komt overeen met het IP-adres van de pod die eerder zijn waargenomen met `kubectl get pods -o wide` 
 ![ backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Ten slotte kunt u de `cURL` opdracht vanuit [Cloud shell](https://shell.azure.com/) gebruiken om een http-verbinding tot stand te brengen met de zojuist geïmplementeerde app:

1. Gebruik `kubectl get ingress` om het open bare IP-adres van Application Gateway op te halen
2. `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>` gebruiken

![Scherm opname van het bash-venster in Azure Cloud Shell waarin een krul opdracht is ingesteld, wordt een HTTP-verbinding met de test-app tot stand gebracht.](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Als gevolg hiervan wordt `HTTP/1.1 200 OK` aangegeven dat Application Gateway het AKS + AGIC-systeem werkt zoals verwacht.


## <a name="inspect-kubernetes-installation"></a>Kubernetes-installatie controleren

### <a name="pods-services-ingress"></a>Peul, services, inkomend
Application Gateway ingangs controller (AGIC) bewaakt voortdurend de volgende Kubernetes bronnen: [implementatie](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) of [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [service](https://kubernetes.io/docs/concepts/services-networking/service/), [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) inkomend verkeer


Het volgende moet worden uitgevoerd om AGIC te laten werken zoals verwacht:
  1. AKS moet een of **meer goeden** hebben.
     Controleer dit op basis van [Cloud shell](https://shell.azure.com/) `kubectl get pods -o wide --show-labels` Als u een pod hebt met een `apsnetapp` , kan uw uitvoer er als volgt uitzien:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Een of meer **Services**, die verwijzen naar de bovenstaande treffers via overeenkomende `selector` labels.
     Controleer dit in [Cloud shell](https://shell.azure.com/) met `kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. Inkomend **, met** `kubernetes.io/ingress.class: azure/application-gateway` een verwijzing naar de service hierboven verifiëren dit van [Cloud shell](https://shell.azure.com/) met`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Bekijk de aantekeningen van de bovenstaande ingangen: `kubectl get ingress aspnetapp -o yaml` (Vervang `aspnetapp` door de naam van uw ingang)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     Aan de ingangs bron moet aantekeningen worden toegevoegd `kubernetes.io/ingress.class: azure/application-gateway` .
 

### <a name="verify-observed-namespace"></a>Waargenomen naam ruimte controleren

* Haal de bestaande naam ruimten op in het Kubernetes-cluster. In welke naam ruimte wordt de app uitgevoerd? Is AGIC de naam ruimte aan het volgen? Raadpleeg de documentatie over [meerdere naam ruimte ondersteuning](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) voor het correct configureren van geobserveerde naam ruimten.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* De AGIC-pod moet zich in de `default` naam ruimte bevinden (Zie de kolom `NAMESPACE` ). `Running`In de kolom is een gezonde pod `STATUS` . Er moet ten minste één AGIC-pod zijn.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Als de AGIC pod niet in orde is (de `STATUS` kolom van de bovenstaande opdracht is niet `Running` ):
  - Logboeken ophalen om te begrijpen waarom: `kubectl logs <pod-name>`
  - voor het vorige exemplaar van de Pod: `kubectl logs <pod-name> --previous`
  - Beschrijf de pod om meer context te krijgen: `kubectl describe pod <pod-name>`


* Hebt u een Kubernetes- [service](https://kubernetes.io/docs/concepts/services-networking/service/) en [zijn](https://kubernetes.io/docs/concepts/services-networking/ingress/) er bronnen voor binnenkomend verkeer?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* Maakt uw [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) inkomen aantekeningen met: `kubernetes.io/ingress.class: azure/application-gateway` ? AGIC controleert alleen op Kubernetes ingangs resources die deze aantekening hebben.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC verzendt Kubernetes-gebeurtenissen voor bepaalde kritieke fouten. U kunt deze bekijken:
  - in uw terminal via `kubectl get events --sort-by=.metadata.creationTimestamp`
  - in uw browser met behulp van de [Kubernetes-webgebruikersinterface (dash board)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Logboekregistratieniveaus

AGIC heeft drie registratie niveaus. Niveau 1 is de standaard instelling en geeft het minimale aantal logboek regels weer.
Op niveau 5 worden daarentegen alle logboeken weer gegeven, inclusief de opgeschoonde inhoud van de configuratie die wordt toegepast op ARM.

De Kubernetes-Community heeft negen niveaus voor logboek registratie vastgesteld voor het [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) -hulp programma. In deze opslag plaats gebruiken we drie van deze, met vergelijk bare semantiek:


| Uitgebreidheid | Beschrijving |
|-----------|-------------|
|  1        | Standaard logboek niveau; opstart Details, waarschuwingen en fouten weer geven |
|  3        | Uitgebreide informatie over gebeurtenissen en wijzigingen; lijsten met gemaakte objecten |
|  5        | Gemarshalde objecten vastleggen; gezuiverde JSON-configuratie weer geven die wordt toegepast op ARM |


De uitbreidings niveaus zijn aanpasbaar via de `verbosityLevel` variabele in het bestand [helm-config. yaml](#sample-helm-config-file) . Verhoog het niveau uitgebreid tot `5` om de JSON-configuratie te verkrijgen die wordt verzonden naar [arm](../azure-resource-manager/management/overview.md):
  - `verbosityLevel: 5`op een regel zelfstandig toevoegen in [helm-config. yaml](#sample-helm-config-file) en opnieuw installeren
  - Logboeken ophalen met `kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Voor beeld van helm-configuratie bestand
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