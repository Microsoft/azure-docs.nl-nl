---
title: Uw TLS-certificaten gebruiken voor inkomend verkeer
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het installeren en configureren van een NGINX ingress-controller die gebruikmaakt van uw eigen certificaten in een Azure Kubernetes service-cluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: ee196bd749ad5821a1855d4549b22698c724b3f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100104923"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>HTTPS-controller voor inkomend verkeer maken en uw eigen TLS-certificaten gebruiken in Azure Kubernetes Service (AKS)

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

In dit artikel wordt beschreven hoe u de [NGINX ingress-controller][nginx-ingress] implementeert in een Azure Kubernetes service-cluster (AKS). U genereert uw eigen certificaten en maakt een Kubernetes-geheim voor gebruik met de ingangs route. Ten slotte worden er twee toepassingen uitgevoerd in het AKS-cluster, die allemaal toegankelijk zijn via één IP-adres.

U kunt ook het volgende doen:

- [Een eenvoudige ingangs controller met externe netwerk verbinding maken][aks-ingress-basic]
- [De invoeg toepassing voor het routeren van HTTP-toepassingen inschakelen][aks-http-app-routing]
- [Een ingangs controller maken die gebruikmaakt van een intern, privé netwerk en IP-adres][aks-ingress-internal]
- Een ingangs controller maken die gebruikmaakt van een versleuteling om automatisch TLS-certificaten te genereren [met een dynamisch openbaar IP-adres][aks-ingress-tls] of [met een statisch openbaar IP-adres][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt gebruikgemaakt van [helm 3][helm] om de NGINX ingress-controller te installeren. Zorg ervoor dat u de nieuwste versie van helm gebruikt en toegang hebt tot de inkomende *nginx helm-* opslag plaats. Zie [helm install docs][helm-install](Engelstalig) voor upgrade-instructies. Zie [Installing Applications with helm in azure Kubernetes service (AKS) (Engelstalig)][use-helm]voor meer informatie over het configureren en gebruiken van helm.

Voor dit artikel moet u ook de Azure CLI-versie 2.0.64 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-ingress-controller"></a>Een ingangs controller maken

Als u de ingangs controller wilt maken, gebruikt `Helm` u voor het installeren van *nginx-ingang*. Voor toegevoegde redundantie worden er twee replica's van de NGINX-ingangscontrollers geïmplementeerd met de parameter `--set controller.replicaCount`. Om volledig te profiteren van het uitvoeren van replica's van de ingangs controller, moet u ervoor zorgen dat er meer dan één knoop punt in uw AKS-cluster is.

De ingangscontroller moet ook worden gepland op een Linux-knooppunt. Windows Server-knooppunten mogen de ingangscontroller niet uitvoeren. Er wordt een knooppuntselector opgegeven met behulp van de parameter `--set nodeSelector` om de Kubernetes-planner te laten weten dat de NGINX-ingangscontroller moet worden uitgevoerd op een Linux-knooppunt.

> [!TIP]
> In het volgende voor beeld wordt een Kubernetes-naam ruimte gemaakt voor de ingangs resources met de naam *ingress-Basic*. Geef waar nodig een naam ruimte op voor uw eigen omgeving. Als voor uw AKS-cluster niet Kubernetes RBAC is ingeschakeld, voegt `--set rbac.create=false` u toe aan de helm-opdrachten.

> [!TIP]
> Als u [IP-behoud van client bronnen][client-source-ip] wilt inschakelen voor aanvragen voor containers in uw cluster, voegt u toe `--set controller.service.externalTrafficPolicy=Local` aan de helm-installatie opdracht. Het bron-IP-adres van de client wordt opgeslagen in de aanvraag header onder *X-doorgestuurd-voor*. Bij gebruik van een ingangs controller waarvoor IP-behoud door client bron is ingeschakeld, werkt TLS Pass-Through niet.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Tijdens de installatie wordt een openbaar IP-adres van Azure gemaakt voor de ingangs controller. Dit open bare IP-adres is statisch voor de levens duur van de ingangs controller. Als u de ingangs controller verwijdert, gaat de toewijzing van het open bare IP-adres verloren. Als u vervolgens een extra ingangs controller maakt, wordt er een nieuw openbaar IP-adres toegewezen. Als u het gebruik van het open bare IP-adres wilt behouden, kunt u in plaats daarvan [een ingangs controller met een statisch openbaar IP-adres maken][aks-ingress-static-tls].

Gebruik de opdracht om het open bare IP-adres op te halen `kubectl get service` .

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Het duurt enkele minuten voordat het IP-adres is toegewezen aan de service.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Noteer dit open bare IP-adres, zoals het in de laatste stap wordt gebruikt om de implementatie te testen.

Er zijn nog geen inkomende regels gemaakt. Als u naar het open bare IP-adres bladert, wordt de standaard 404-pagina van de NGINX ingress-controller weer gegeven.

## <a name="generate-tls-certificates"></a>TLS-certificaten genereren

Voor dit artikel genereren we een zelfondertekend certificaat met `openssl` . Voor productie gebruik moet u een vertrouwd, ondertekend certificaat aanvragen via een provider of uw eigen certificerings instantie (CA). In de volgende stap genereert u een Kubernetes- *geheim* met behulp van het TLS-certificaat en de persoonlijke sleutel die door openssl worden gegenereerd.

In het volgende voor beeld wordt een 2048-bits RSA x509-certificaat gegenereerd dat geldig is voor 365 dagen met de naam *AKS-ingress-TLS. CRT*. Het persoonlijke sleutel bestand heeft de naam *AKS-ingress-TLS. key*. Voor een Kubernetes TLS-geheim zijn beide bestanden vereist.

Dit artikel werkt met de algemene naam van de *demo.Azure.com* -onderwerp en hoeft niet te worden gewijzigd. Geef voor productie gebruik uw eigen organisatie waarden op voor de `-subj` para meter:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Kubernetes-geheim maken voor TLS-certificaat

U kunt een geheim maken en gebruiken om Kubernetes toe te staan het TLS-certificaat en de persoonlijke sleutel te gebruiken voor de ingangs controller. Het geheim wordt eenmaal gedefinieerd en gebruikt het certificaat en sleutel bestand dat u in de vorige stap hebt gemaakt. Vervolgens verwijst u naar dit geheim wanneer u ingangs routes definieert.

In het volgende voor beeld wordt een geheime naam *AKS-ingangs-TLS* gemaakt:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Demo toepassingen uitvoeren

Een ingangs controller en een geheim met uw certificaat zijn geconfigureerd. We gaan nu twee demonstratie toepassingen uitvoeren in uw AKS-cluster. In dit voor beeld wordt helm gebruikt om twee exemplaren van een eenvoudige ' Hallo wereld '-toepassing te implementeren.

Als u de ingangs controller in actie wilt zien, voert u twee demonstratie toepassingen uit in uw AKS-cluster. In dit voor beeld gebruikt u `kubectl apply` om twee exemplaren van een eenvoudige *Hello World* -toepassing te implementeren.

Maak een *AKS-bestand HelloWorld. yaml* en kopieer het in het volgende voor beeld YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Maak een inkomend *-demo. yaml-* bestand en kopieer het in het volgende voor beeld YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Voer de twee demo toepassingen uit met `kubectl apply` :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Een ingangs route maken

Beide toepassingen worden nu uitgevoerd op uw Kubernetes-cluster, maar ze zijn geconfigureerd met een service van het type `ClusterIP` . De toepassingen zijn dus niet toegankelijk via internet. Als u ze openbaar beschikbaar wilt maken, maakt u een Kubernetes-ingangs bron. De ingangs resource configureert de regels die verkeer routeren naar een van de twee toepassingen.

In het volgende voor beeld wordt verkeer naar het adres `https://demo.azure.com/` gerouteerd naar de service met de naam `aks-helloworld` . Verkeer naar het adres `https://demo.azure.com/hello-world-two` wordt doorgestuurd naar de `ingress-demo` service. Voor dit artikel hoeft u deze namen van de demo-hosts niet te wijzigen. Geef voor gebruik van productie de namen op die zijn opgegeven als onderdeel van de certificaat aanvraag en het generatie proces.

> [!TIP]
> Als de hostnaam die is opgegeven tijdens het proces van de certificaat aanvraag, de naam van de CN, komt niet overeen met de host die is gedefinieerd in de ingangs route, de ingangs controller geeft een valse certificaat waarschuwing voor de *Kubernetes ingress-controller* . Zorg ervoor dat uw certificaat en ingangen voor route-hostnamen overeenkomen.

De sectie *TLS* vertelt de ingangs route om het geheim met de naam *AKS-ingress-TLS* te gebruiken voor de *demo.Azure.com* van de host. Geef het adres van uw eigen host op voor productie gebruik.

Maak een bestand `hello-world-ingress.yaml` met de naam en kopieer het in het volgende voor beeld YAML.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

Maak de ingangs resource met behulp van de `kubectl apply -f hello-world-ingress.yaml` opdracht.

```console
kubectl apply -f hello-world-ingress.yaml
```

In de voorbeeld uitvoer ziet u dat de ingangs resource is gemaakt.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>De ingangs configuratie testen

Als u de certificaten met onze valse *demo.Azure.com* -host wilt testen, gebruikt u `curl` en geeft u de para meter *--Resolve* op. Met deze para meter kunt u de naam van de *demo.Azure.com* toewijzen aan het open bare IP-adres van uw ingangs controller. Geef het open bare IP-adres van uw eigen ingangs controller op, zoals wordt weer gegeven in het volgende voor beeld:

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

Er is geen extra pad met het adres meegeleverd, dus de ingangs controller wordt standaard ingesteld op de */* route. De eerste demo toepassing wordt geretourneerd, zoals wordt weer gegeven in de volgende verkorte voorbeeld uitvoer:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

De para meter *-v* in de `curl` opdracht voert uitgebreide informatie uit, met inbegrip van het ontvangen TLS-certificaat. U kunt uw eigen TLS-certificaat gebruiken om te zien hoe u in de richting van de krul in de weg bent. De para meter *-k* gaat door met het laden van de pagina, zelfs als we een zelfondertekend certificaat gebruiken. In het volgende voor beeld ziet u dat de *verlener: CN = demo. Azure. com; O = AKS-ingangs-TLS-* certificaat is gebruikt:

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Voeg nu */Hello-World-Two* -pad toe aan het adres, zoals `https://demo.azure.com/hello-world-two` . De tweede demo toepassing met de aangepaste titel wordt geretourneerd, zoals wordt weer gegeven in de volgende verkorte voorbeeld uitvoer:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel wordt helm gebruikt voor het installeren van de ingangs onderdelen en voor beeld-apps. Wanneer u een helm-grafiek implementeert, worden er een aantal Kubernetes-resources gemaakt. Deze resources omvatten Peul, implementaties en services. Als u deze resources wilt opschonen, kunt u de volledige voorbeeld naam ruimte of de afzonderlijke resources verwijderen.

### <a name="delete-the-sample-namespace-and-all-resources"></a>De voorbeeld naam ruimte en alle resources verwijderen

Als u de volledige voorbeeld naam ruimte wilt verwijderen, gebruikt u de `kubectl delete` opdracht en geeft u de naam van de naam ruimte op. Alle resources in de naam ruimte worden verwijderd.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Resources afzonderlijk verwijderen

U kunt ook een nauw keurigere benadering van de gemaakte afzonderlijke resources verwijderen. Vermeld de helm-releases met de `helm list` opdracht. 

```console
helm list --namespace ingress-basic
```

Zoek naar een grafiek met de naam *nginx-* binnenkomend, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1 
```

Verwijder de releases met de `helm uninstall` opdracht. 

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

In het volgende voor beeld wordt de implementatie van de NGINX-ingang verwijderd.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Verwijder vervolgens de twee voorbeeld toepassingen:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Verwijder de ingangs route die het verkeer naar de voor beeld-apps doorstuurt:

```console
kubectl delete -f hello-world-ingress.yaml
```

Het certificaat geheim verwijderen:

```console
kubectl delete secret aks-ingress-tls --namespace ingress-basic
```

Ten slotte kunt u de zelf naam ruimte verwijderen. Gebruik de `kubectl delete` opdracht en geef de naam van de naam ruimte op:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel zijn enkele externe onderdelen opgenomen in AKS. Zie de volgende project pagina's voor meer informatie over deze onderdelen:

- [Helm CLI][helm-cli]
- [NGINX ingress-controller][nginx-ingress]

U kunt ook het volgende doen:

- [Een eenvoudige ingangs controller met externe netwerk verbinding maken][aks-ingress-basic]
- [De invoeg toepassing voor het routeren van HTTP-toepassingen inschakelen][aks-http-app-routing]
- [Een ingangs controller maken die gebruikmaakt van een intern, privé netwerk en IP-adres][aks-ingress-internal]
- Een ingangs controller maken die gebruikmaakt van een versleuteling om automatisch TLS-certificaten te genereren [met een dynamisch openbaar IP-adres][aks-ingress-tls] of [met een statisch openbaar IP-adres][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
