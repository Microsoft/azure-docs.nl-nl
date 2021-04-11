---
title: Inkomend verkeer maken met automatische TLS
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het installeren en configureren van een NGINX ingress-controller die wordt gebruikt voor het automatisch genereren van TLS-certificaten in een Azure Kubernetes service-cluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: aa03558196527e320ecba249dd22af19fec7a90c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592333"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Een HTTPS ingress-controller maken in azure Kubernetes service (AKS)

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

In dit artikel wordt beschreven hoe u de [NGINX ingress-controller][nginx-ingress] implementeert in een Azure Kubernetes service-cluster (AKS). Het [CERT-beheer][cert-manager] project wordt gebruikt om certificaten automatisch te genereren en [te configureren.][lets-encrypt] Ten slotte worden er twee toepassingen uitgevoerd in het AKS-cluster, die allemaal toegankelijk zijn via één IP-adres.

U kunt ook het volgende doen:

- [Een eenvoudige ingangs controller met externe netwerk verbinding maken][aks-ingress-basic]
- [De invoeg toepassing voor het routeren van HTTP-toepassingen inschakelen][aks-http-app-routing]
- [Een ingangs controller maken die gebruikmaakt van een intern, privé netwerk en IP-adres][aks-ingress-internal]
- [Een ingangs controller maken die gebruikmaakt van uw eigen TLS-certificaten][aks-ingress-own-tls]
- [Een ingangs controller maken die gebruikmaakt van de code ring om automatisch TLS-certificaten te genereren met een statisch openbaar IP-adres][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

In dit artikel wordt ervan uitgegaan dat u [een aangepast domein][custom-domain] hebt met een [DNS-zone][dns-zone] in dezelfde resource groep als uw AKS-cluster.

In dit artikel wordt gebruikgemaakt van [helm 3][helm] voor het installeren van de NGINX ingress-controller en cert-Manager. Zorg ervoor dat u de nieuwste versie van helm gebruikt en toegang hebt tot de inkomende *-nginx* en *jetstack* helm-opslag plaatsen. Zie [helm install docs][helm-install](Engelstalig) voor upgrade-instructies. Zie [Installing Applications with helm in azure Kubernetes service (AKS) (Engelstalig)][use-helm]voor meer informatie over het configureren en gebruiken van helm.

Voor dit artikel moet u ook de Azure CLI-versie 2.0.64 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-ingress-controller"></a>Een ingangs controller maken

Als u de ingangs controller wilt maken, gebruikt u de `helm` opdracht om *nginx* te installeren. Voor toegevoegde redundantie worden er twee replica's van de NGINX-ingangscontrollers geïmplementeerd met de parameter `--set controller.replicaCount`. Om volledig te profiteren van het uitvoeren van replica's van de ingangs controller, moet u ervoor zorgen dat er meer dan één knoop punt in uw AKS-cluster is.

De ingangscontroller moet ook worden gepland op een Linux-knooppunt. Windows Server-knooppunten mogen de ingangscontroller niet uitvoeren. Er wordt een knooppuntselector opgegeven met behulp van de parameter `--set nodeSelector` om de Kubernetes-planner te laten weten dat de NGINX-ingangscontroller moet worden uitgevoerd op een Linux-knooppunt.

> [!TIP]
> In het volgende voor beeld wordt een Kubernetes-naam ruimte gemaakt voor de ingangs resources met de naam *ingress-Basic*. Geef waar nodig een naam ruimte op voor uw eigen omgeving.

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

Gebruik de opdracht om het open bare IP-adres op te halen `kubectl get service` . Het duurt enkele minuten voordat het IP-adres is toegewezen aan de service.

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Er zijn nog geen inkomende regels gemaakt. Als u naar het open bare IP-adres bladert, wordt de standaard 404-pagina van de NGINX ingress-controller weer gegeven.

## <a name="add-an-a-record-to-your-dns-zone"></a>Een A-record toevoegen aan uw DNS-zone

Voeg een *A* -record toe aan uw DNS-zone met het externe IP-adres van de NGINX-service met [AZ Network DNS record-set A add-record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Optioneel kunt u een FQDN configureren voor het IP-adres van de ingangs controller in plaats van een aangepast domein. Houd er rekening mee dat dit voor beeld is voor een bash-shell.
> 
> ```bash
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>Certificaatbeheer installeren

De NGINX-ingangscontroller ondersteunt TLS-beëindiging. Er zijn verschillende manieren om certificaten voor HTTPS op te halen en te configureren. In dit artikel wordt gedemonstreerd met behulp van [CERT-beheer][cert-manager], waarmee automatisch de functionaliteit voor het genereren en beheren van certificaten [kan worden versleuteld][lets-encrypt] .

Installeren van de CERT-Manager-controller:

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager jetstack/cert-manager \
  --namespace ingress-basic \
  --set installCRDs=true \
  --set nodeSelector."kubernetes\.io/os"=linux \
  --set webhook.nodeSelector."kubernetes\.io/os"=linux \
  --set cainjector.nodeSelector."kubernetes\.io/os"=linux
```

Zie het [project certificaat beheer][cert-manager]voor meer informatie over de configuratie van certificaat beheer.

## <a name="create-a-ca-cluster-issuer"></a>Een certificerings instantie voor een CA-cluster maken

Voordat certificaten kunnen worden verleend, vereist CERT-Manager een [verlener][cert-manager-issuer] -of [ClusterIssuer][cert-manager-cluster-issuer] -resource. Deze Kubernetes-resources zijn identiek in functionaliteit, maar `Issuer` werken in één naam ruimte en `ClusterIssuer` werken in alle naam ruimten. Zie de documentatie van de [certificaat beheerder][cert-manager-issuer] voor meer informatie.

Maak een cluster Uitgever, zoals in `cluster-issuer.yaml` het volgende voor beeld-manifest. Werk het e-mail adres bij met een geldig adres van uw organisatie:

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Gebruik de opdracht om de certificaat verlener te maken `kubectl apply` .

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Demo toepassingen uitvoeren

Er is een ingangs controller en een oplossing voor certificaat beheer geconfigureerd. We gaan nu twee demonstratie toepassingen uitvoeren in uw AKS-cluster. In dit voor beeld wordt helm gebruikt om twee exemplaren van een eenvoudige *Hello World* -toepassing te implementeren.

Als u de ingangs controller in actie wilt zien, voert u twee demonstratie toepassingen uit in uw AKS-cluster. In dit voor beeld gebruikt u `kubectl apply` om twee exemplaren van een eenvoudige *Hello World* -toepassing te implementeren.

Maak een *AKS-HelloWorld-One. yaml-* bestand en kopieer het in het volgende voor beeld YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
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
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Maak een *AKS-HelloWorld-twee. yaml-* bestand en kopieer het in het volgende voor beeld YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
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
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Voer de twee demo toepassingen uit met `kubectl apply` :

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Een ingangs route maken

Beide toepassingen worden nu uitgevoerd op uw Kubernetes-cluster. Ze zijn echter wel geconfigureerd met een service van `ClusterIP` het type en zijn niet toegankelijk via internet. Als u ze openbaar beschikbaar wilt maken, maakt u een Kubernetes-ingangs bron. De ingangs resource configureert de regels die verkeer routeren naar een van de twee toepassingen.

In het volgende voor beeld wordt verkeer naar het adres *Hello-World-ingress. MY_CUSTOM_DOMAIN* wordt doorgestuurd naar de *AKS-HelloWorld-een-* service. Verkeer naar het adres *Hello-World-ingress. MY_CUSTOM_DOMAIN/Hello-World-Two* wordt doorgestuurd naar de *AKS-HelloWorld-twee-* service. Verkeer naar *Hallo wereld. MY_CUSTOM_DOMAIN/static* wordt doorgestuurd naar de service met de naam *AKS-HelloWorld-één* voor statische activa.

> [!NOTE]
> Als u een FQDN voor het IP-adres van de ingangs controller hebt geconfigureerd in plaats van een aangepast domein, gebruikt u de FQDN in plaats van *Hallo wereld. MY_CUSTOM_DOMAIN*. Als uw FQDN bijvoorbeeld *demo-AKS-ingress.eastus.cloudapp.Azure.com* is, vervangt u *Hello-World-ingress. MY_CUSTOM_DOMAIN* met *demo-AKS-ingress.eastus.cloudapp.Azure.com* in `hello-world-ingress.yaml` .

Maak een bestand met de naam `hello-world-ingress.yaml` met behulp van het onderstaande voor beeld YAML. Werk de *hosts* en *host* bij naar de DNS-naam die u in een vorige stap hebt gemaakt.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /(.*)
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

Maak de ingangs resource met behulp van de `kubectl apply` opdracht.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Controleren of een certificaat object is gemaakt

Vervolgens moet er een certificaat bron worden gemaakt. De certificaat resource definieert het gewenste X. 509-certificaat. Zie [certificaten certificaat beheer][cert-manager-certificates]voor meer informatie. CERT-Manager heeft automatisch een certificaat object voor u gemaakt met behulp van ingress-Shim, dat automatisch wordt geïmplementeerd met CERT-Manager sinds v 0.2.2. Zie de documentatie van de inkomende [shim][ingress-shim]voor meer informatie.

Als u wilt controleren of het certificaat is gemaakt, gebruikt u de `kubectl get certificate --namespace ingress-basic` opdracht en controleert u of *ready* is ingesteld op *True*. Dit kan enkele minuten duren.

```console
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>De ingangs configuratie testen

Open een webbrowser om *Hallo werelden te openen. MY_CUSTOM_DOMAIN* van uw Kubernetes ingangs controller. U wordt omgeleid om HTTPS te gebruiken en het certificaat wordt vertrouwd en de demo toepassing wordt weer gegeven in de webbrowser. Voeg het */Hello-World-Two* -pad toe en Let op dat de tweede demo toepassing met de aangepaste titel wordt weer gegeven.

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel wordt helm gebruikt om de ingangs onderdelen, certificaten en voor beeld-apps te installeren. Wanneer u een helm-grafiek implementeert, worden er een aantal Kubernetes-resources gemaakt. Deze resources omvatten peulen, implementaties en services. Als u deze resources wilt opschonen, kunt u de volledige voorbeeld naam ruimte of de afzonderlijke resources verwijderen.

### <a name="delete-the-sample-namespace-and-all-resources"></a>De voorbeeld naam ruimte en alle resources verwijderen

Als u de volledige voorbeeld naam ruimte wilt verwijderen, gebruikt u de `kubectl delete` opdracht en geeft u de naam van de naam ruimte op. Alle resources in de naam ruimte worden verwijderd.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Resources afzonderlijk verwijderen

U kunt ook een nauw keurigere benadering van de gemaakte afzonderlijke resources verwijderen. Verwijder eerst de bronnen van de cluster Uitgever:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Vermeld de helm-releases met de `helm list` opdracht. Zoek naar grafieken met de naam *nginx* en *CERT-Manager*, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```console
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Verwijder de releases met de `helm uninstall` opdracht. In het volgende voor beeld worden de implementaties van NGINX ingress en cert-Manager verwijderd.

```console
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Verwijder vervolgens de twee voorbeeld toepassingen:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Verwijder de ingangs route die het verkeer naar de voor beeld-apps doorstuurt:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Ten slotte kunt u de zelf naam ruimte verwijderen. Gebruik de `kubectl delete` opdracht en geef de naam van de naam ruimte op:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel zijn enkele externe onderdelen opgenomen in AKS. Zie de volgende project pagina's voor meer informatie over deze onderdelen:

- [Helm CLI][helm-cli]
- [NGINX ingress-controller][nginx-ingress]
- [Certificaatbeheer][cert-manager]

U kunt ook het volgende doen:

- [Een eenvoudige ingangs controller met externe netwerk verbinding maken][aks-ingress-basic]
- [De invoeg toepassing voor het routeren van HTTP-toepassingen inschakelen][aks-http-app-routing]
- [Een ingangs controller maken die gebruikmaakt van een intern, privé netwerk en IP-adres][aks-ingress-internal]
- [Een ingangs controller maken die gebruikmaakt van uw eigen TLS-certificaten][aks-ingress-own-tls]
- [Een ingangs controller maken die gebruikmaakt van de code ring om automatisch TLS-certificaten te genereren met een statisch openbaar IP-adres][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
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
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
