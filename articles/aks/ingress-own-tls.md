---
title: Uw TLS-certificaten gebruiken voor ingress
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het installeren en configureren van een NGINX-controller voor ingressen die gebruikmaakt van uw eigen certificaten in een Azure Kubernetes Service -cluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: d3d554fbbbbb16910b76e7eb56afd13c436f8cf5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779935"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>HTTPS-controller voor inkomend verkeer maken en uw eigen TLS-certificaten gebruiken in Azure Kubernetes Service (AKS)

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

In dit artikel wordt beschreven hoe u de [NGINX-controller][nginx-ingress] voor Azure Kubernetes Service in een AKS-cluster (Azure Kubernetes Service) implementeert. U genereert uw eigen certificaten en maakt een Kubernetes-geheim voor gebruik met de ingressroute. Ten slotte worden twee toepassingen uitgevoerd in het AKS-cluster, die elk toegankelijk zijn via één IP-adres.

U kunt ook het volgende doen:

- [Een eenvoudige controller voor ingress maken met externe netwerkconnectiviteit][aks-ingress-basic]
- [De invoegtoepassing http-toepassingsroutering inschakelen][aks-http-app-routing]
- [Een controller voor ingress maken die gebruikmaakt van een intern, privénetwerk en IP-adres][aks-ingress-internal]
- Maak een controller voor verkeer dat gebruikmaakt van Let's Encrypt om automatisch TLS-certificaten te genereren met een dynamisch openbaar [IP-adres][aks-ingress-tls] of [met een statisch openbaar IP-adres][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt [Helm 3 gebruikt][helm] om de NGINX-controller voor ingressen te installeren. Zorg ervoor dat u de nieuwste versie van Helm gebruikt en toegang hebt tot de *Helm-opslagplaats ingress-nginx.* Zie de Helm-installatie docs voor [upgrade-instructies.][helm-install] Zie Toepassingen installeren met Helm in Azure Kubernetes Service [(AKS) voor][use-helm]meer informatie over het configureren en gebruiken van Helm.

Voor dit artikel moet u ook Azure CLI versie 2.0.64 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-ingress-controller"></a>Een controller voor ingress maken

Als u de controller voor het ingress-gebruik wilt maken, gebruikt `Helm` u *om nginx-ingress te installeren.* Voor toegevoegde redundantie worden er twee replica's van de NGINX-ingangscontrollers geïmplementeerd met de parameter `--set controller.replicaCount`. Zorg ervoor dat uw AKS-cluster meer dan één knooppunt heeft om volledig te profiteren van het uitvoeren van replica's van de controller voor ingress.

De ingangscontroller moet ook worden gepland op een Linux-knooppunt. Windows Server-knooppunten mogen de ingangscontroller niet uitvoeren. Er wordt een knooppuntselector opgegeven met behulp van de parameter `--set nodeSelector` om de Kubernetes-planner te laten weten dat de NGINX-ingangscontroller moet worden uitgevoerd op een Linux-knooppunt.

> [!TIP]
> In het volgende voorbeeld wordt een Kubernetes-naamruimte gemaakt voor de ingress-resources met de *naam ingress-basic.* Geef waar nodig een naamruimte op voor uw eigen omgeving. Als uw AKS-cluster niet kubernetes RBAC is ingeschakeld, voegt u toe `--set rbac.create=false` aan de Helm-opdrachten.

> [!TIP]
> Als u behoud van [ip-adressen van clientbron wilt][client-source-ip] inschakelen voor aanvragen voor containers in uw cluster, voegt u toe `--set controller.service.externalTrafficPolicy=Local` aan de Helm-installatieopdracht. Het ip-adres van de clientbron wordt opgeslagen in de aanvraagheader onder *X-Forwarded-For.* Wanneer u een toegangscontroller gebruikt waarop behoud van ip-adressen van clientbron is ingeschakeld, werkt TLS-pass-through niet.

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

Tijdens de installatie wordt een openbaar IP-adres van Azure gemaakt voor de controller voor het binnen- en weer gaan. Dit openbare IP-adres is statisch voor de levensduur van de controller voor ingress. Als u de controller voor ingress verwijdert, gaat de toewijzing van het openbare IP-adres verloren. Als u vervolgens een extra controller voor ingress maakt, wordt er een nieuw openbaar IP-adres toegewezen. Als u het gebruik van het openbare IP-adres wilt behouden, kunt u in plaats daarvan een controller voor ingress maken met [een statisch openbaar IP-adres.][aks-ingress-static-tls]

Gebruik de opdracht om het openbare IP-adres op `kubectl get service` te halen.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Het duurt enkele minuten voordat het IP-adres is toegewezen aan de service.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Noteer dit openbare IP-adres, omdat het in de laatste stap wordt gebruikt om de implementatie te testen.

Er zijn nog geen regels voor ingress gemaakt. Als u naar het openbare IP-adres bladert, wordt de standaardpagina 404 van de NGINX-ingresscontroller weergegeven.

## <a name="generate-tls-certificates"></a>TLS-certificaten genereren

Voor dit artikel gaan we een zelf-ondertekend certificaat genereren met `openssl` . Voor productiegebruik moet u een vertrouwd, ondertekend certificaat aanvragen via een provider of uw eigen certificeringsinstantie (CA). In de volgende stap genereert u  een Kubernetes-geheim met behulp van het TLS-certificaat en de persoonlijke sleutel die door OpenSSL worden gegenereerd.

In het volgende voorbeeld wordt een 2048-bits RSA X509-certificaat gegenereerd dat 365 dagen geldig is met de naam *aks-ingress-tls.crt.* Het bestand met de persoonlijke sleutel heet *aks-ingress-tls.key.* Voor een Kubernetes TLS-geheim zijn beide bestanden vereist.

Dit artikel werkt met *demo.azure.com* algemene onderwerpnaam en hoeft niet te worden gewijzigd. Geef voor productiegebruik uw eigen organisatiewaarden op voor de `-subj` parameter :

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Kubernetes-geheim maken voor TLS-certificaat

Als u Wilt toestaan dat Kubernetes het TLS-certificaat en de persoonlijke sleutel voor de toegangscontroller gebruikt, maakt en gebruikt u een geheim. Het geheim wordt eenmaal gedefinieerd en maakt gebruik van het certificaat en sleutelbestand dat u in de vorige stap hebt gemaakt. U verwijst vervolgens naar dit geheim wanneer u toegangsroutes definieert.

In het volgende voorbeeld wordt de geheime naam *aks-ingress-tls gemaakt:*

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Demotoepassingen uitvoeren

Er zijn een controller voor ingress en een geheim met uw certificaat geconfigureerd. We gaan nu twee demotoepassingen uitvoeren in uw AKS-cluster. In dit voorbeeld wordt Helm gebruikt om twee exemplaren van een eenvoudige 'Hallo wereld'-toepassing te implementeren.

Als u de controller voor ingress in actie wilt zien, moet u twee demotoepassingen uitvoeren in uw AKS-cluster. In dit voorbeeld gebruikt u om twee exemplaren van een eenvoudige `kubectl apply` *Hallo wereld-toepassing te* implementeren.

Maak een *aks-helloworld.yaml-bestand* en kopieer het in het volgende voorbeeld van YAML:

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

Maak een *ingress-demo.yaml-bestand* en kopieer het in het volgende voorbeeld van YAML:

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

Voer de twee demotoepassingen uit met `kubectl apply` behulp van :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Een ingressroute maken

Beide toepassingen worden nu uitgevoerd op uw Kubernetes-cluster, maar ze zijn geconfigureerd met een service van het type `ClusterIP` . Daarom zijn de toepassingen niet toegankelijk via internet. Als u deze openbaar beschikbaar wilt maken, maakt u een Kubernetes-ingress-resource. De resource voor het binnenverkeer configureert de regels die verkeer naar een van de twee toepassingen doors groeperen.

In het volgende voorbeeld wordt verkeer naar het adres `https://demo.azure.com/` doorgeleid naar de service met de naam `aks-helloworld` . Verkeer naar het adres `https://demo.azure.com/hello-world-two` wordt doorgeleid naar de `ingress-demo` service. Voor dit artikel hoeft u deze namen van demohosts niet te wijzigen. Geef voor productiegebruik de namen op die zijn opgegeven als onderdeel van het certificaataanvraag- en generatieproces.

> [!TIP]
> Als de hostnaam die is opgegeven tijdens het certificaataanvraagproces, de CN-naam, niet overeen komt met de host die is gedefinieerd in uw toegangsroute, geeft de controller voor toegangsgressie een waarschuwing over het valse certificaat van de *Kubernetes-toegangscontroller* weer. Zorg ervoor dat de hostnamen van uw certificaat en de ingressroute overeenkomen.

De *sectie tls* geeft aan dat de route voor binnenvoer moet worden gebruikt voor het geheim met de naam *aks-ingress-tls* voor de host *demo.azure.com*. Geef voor productiegebruik opnieuw uw eigen hostadres op.

Maak een bestand met de `hello-world-ingress.yaml` naam en kopieer het in het volgende yaml-voorbeeld.

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

Maak de ingress-resource met behulp van de `kubectl apply -f hello-world-ingress.yaml` opdracht .

```console
kubectl apply -f hello-world-ingress.yaml
```

In de voorbeelduitvoer ziet u dat de ingress-resource is gemaakt.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>De configuratie van het ingress testen

Als u de certificaten wilt testen met onze *demo.azure.com* host, gebruikt en geeft u de `curl` parameter *--resolve* op. Met deze parameter kunt u de *demo.azure.com* aan het openbare IP-adres van de controller voor objectingressen. Geef het openbare IP-adres van uw eigen controller voor ingress op, zoals wordt weergegeven in het volgende voorbeeld:

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

Er is geen extra pad opgegeven met het adres, dus de controller voor het ingress wordt standaard ingesteld op de */* route. De eerste demotoepassing wordt geretourneerd, zoals wordt weergegeven in de volgende verkorte voorbeelduitvoer:

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

De *parameter -v* in de opdracht geeft uitgebreide `curl` informatie weer, waaronder het ontvangen TLS-certificaat. Halverwege de curl-uitvoer kunt u controleren of uw eigen TLS-certificaat is gebruikt. De *parameter -k* gaat door met het laden van de pagina, ook al gebruiken we een zelf-ondertekend certificaat. In het volgende voorbeeld ziet u dat *de verzender CN=demo.azure.com; Het O=aks-ingress-tls-certificaat* is gebruikt:

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

Voeg nu */hello-world-two-pad* toe aan het adres, zoals `https://demo.azure.com/hello-world-two` . De tweede demotoepassing met de aangepaste titel wordt geretourneerd, zoals wordt weergegeven in de volgende verkorte voorbeelduitvoer:

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

In dit artikel is Helm gebruikt om de toegangsgegevens en voorbeeld-apps te installeren. Wanneer u een Helm-grafiek implementeert, wordt er een aantal Kubernetes-resources gemaakt. Deze resources omvatten pods, implementaties en services. Als u deze resources wilt ops schonen, kunt u de volledige voorbeeldnaamruimte of de afzonderlijke resources verwijderen.

### <a name="delete-the-sample-namespace-and-all-resources"></a>De voorbeeldnaamruimte en alle resources verwijderen

Als u de volledige voorbeeldnaamruimte wilt verwijderen, gebruikt u de `kubectl delete` opdracht en geeft u de naam van uw naamruimte op. Alle resources in de naamruimte worden verwijderd.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Resources afzonderlijk verwijderen

Een meer gedetailleerde benadering is ook het verwijderen van de afzonderlijke resources die zijn gemaakt. Vermeld de Helm-releases met de `helm list` opdracht . 

```console
helm list --namespace ingress-basic
```

Zoek naar een grafiek met de *naam nginx-ingress,* zoals wordt weergegeven in de volgende voorbeelduitvoer:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1 
```

Verwijder de releases met de `helm uninstall` opdracht . 

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

In het volgende voorbeeld wordt de implementatie van het NGINX-ingress verwijderd.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Verwijder vervolgens de twee voorbeeldtoepassingen:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Verwijder de toegangsroute die verkeer naar de voorbeeld-apps heeft geleid:

```console
kubectl delete -f hello-world-ingress.yaml
```

Verwijder het certificaatgeheim:

```console
kubectl delete secret aks-ingress-tls --namespace ingress-basic
```

Ten slotte kunt u de naamruimte zelf verwijderen. Gebruik de `kubectl delete` opdracht en geef de naam van uw naamruimte op:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Volgende stappen

Dit artikel bevat enkele externe onderdelen voor AKS. Zie de volgende projectpagina's voor meer informatie over deze onderdelen:

- [Helm CLI][helm-cli]
- [NGINX-controller voor ingress][nginx-ingress]

U kunt ook het volgende doen:

- [Een eenvoudige controller voor ingress maken met externe netwerkconnectiviteit][aks-ingress-basic]
- [De invoegtoepassing HTTP-toepassingsroutering inschakelen][aks-http-app-routing]
- [Een controller voor ingress maken die gebruikmaakt van een intern, privénetwerk en IP-adres][aks-ingress-internal]
- Maak een controller voor verkeer dat gebruikmaakt van Let's Encrypt om automatisch TLS-certificaten te genereren met een dynamisch openbaar [IP-adres][aks-ingress-tls] of [met een statisch openbaar IP-adres][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
