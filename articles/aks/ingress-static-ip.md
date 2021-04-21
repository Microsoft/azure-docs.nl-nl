---
title: Controller voor ingress gebruiken met statisch IP-adres
titleSuffix: Azure Kubernetes Service
description: Informatie over het installeren en configureren van een NGINX-controller voor ingress met een statisch openbaar IP-adres in een Azure Kubernetes Service (AKS)-cluster.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8de0d25bc30d53f11ddaed5ab7b53ff992a5c88c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779827"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Een controller voor inkomend verkeer maken met een statisch openbaar IP-adres in Azure Kubernetes Service (AKS)

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

In dit artikel wordt beschreven hoe u de [NGINX-controller][nginx-ingress] voor een Azure Kubernetes Service (AKS) implementeert. De controller voor ingress is geconfigureerd met een statisch openbaar IP-adres. Het [certificaatbeheerproject][cert-manager] wordt gebruikt voor het automatisch genereren en configureren van [Let's][lets-encrypt] Encrypt-certificaten. Ten slotte worden twee toepassingen uitgevoerd in het AKS-cluster, die allemaal toegankelijk zijn via één IP-adres.

U kunt ook het volgende doen:

- [Een eenvoudige controller voor ingress maken met externe netwerkconnectiviteit][aks-ingress-basic]
- [De invoegtoepassing HTTP-toepassingsroutering inschakelen][aks-http-app-routing]
- [Een controller voor het binnen- en weer gaan maken die gebruikmaakt van uw eigen TLS-certificaten][aks-ingress-own-tls]
- [Een controller voor verkeer maken die gebruikmaakt van Let's Encrypt om automatisch TLS-certificaten te genereren met een dynamisch openbaar IP-adres][aks-ingress-tls]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

In dit artikel wordt [Helm 3 gebruikt][helm] om de NGINX-ingresscontroller en certificaatbeheer te installeren. Zorg ervoor dat u de nieuwste versie van Helm gebruikt en toegang hebt tot de *opslagplaatsen ingress-nginx* *en jetstack* Helm. Zie de helm-installatie docs voor [upgrade-instructies.][helm-install] Zie Toepassingen installeren met Helm in Azure Kubernetes Service [(AKS) voor][use-helm]meer informatie over het configureren en gebruiken van Helm.

Voor dit artikel moet u ook Azure CLI versie 2.0.64 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-ingress-controller"></a>Een controller voor ingress maken

Standaard wordt er een NGINX-controller voor het ingress-adres gemaakt met een nieuwe toewijzing van een openbaar IP-adres. Dit openbare IP-adres is alleen statisch voor de levensduur van de controller voor ingress en gaat verloren als de controller wordt verwijderd en opnieuw wordt gemaakt. Een veelvoorkomende configuratievereiste is om de NGINX-inkomende controller een bestaand statisch openbaar IP-adres te bieden. Het statische openbare IP-adres blijft bestaan als de controller voor binnengangen wordt verwijderd. Op deze manier kunt u bestaande DNS-records en netwerkconfiguraties gedurende de levenscyclus van uw toepassingen op een consistente manier gebruiken.

Als u een statisch openbaar IP-adres wilt maken, moet u eerst de naam van de resourcegroep van het AKS-cluster op halen met de [opdracht az aks show:][az-aks-show]

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Maak vervolgens een openbaar IP-adres met de *statische* toewijzingsmethode met behulp van [de opdracht az network public-ip create.][az-network-public-ip-create] In het volgende voorbeeld wordt een openbaar IP-adres met de *naam myAKSPublicIP* gemaakt in de AKS-clusterresourcegroep die u in de vorige stap hebt verkregen:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> Met de bovenstaande opdrachten maakt u een IP-adres dat wordt verwijderd als u uw AKS-cluster verwijdert. U kunt ook een IP-adres maken in een andere resourcegroep die afzonderlijk van uw AKS-cluster kan worden beheerd. Als u een IP-adres in een andere resourcegroep maakt, moet u ervoor zorgen dat de clusteridentiteit die wordt gebruikt door het AKS-cluster machtigingen heeft gedelegeerd aan de andere resourcegroep, zoals Inzender voor *netwerken.* Zie Use a static public IP address and DNS label with the AKS load balancer (Een statisch openbaar IP-adres en [DNS-label gebruiken met de AKS-load balancer) voor meer load balancer.][aks-static-ip]

Implementeer nu *de nginx-ingress-grafiek* met Helm. Voor toegevoegde redundantie worden er twee replica's van de NGINX-ingangscontrollers geïmplementeerd met de parameter `--set controller.replicaCount`. Zorg ervoor dat uw AKS-cluster meer dan één knooppunt heeft om volledig te profiteren van het uitvoeren van replica's van de controller voor ingress.

U moet twee extra parameters doorgeven aan de Helm-release, zodat de controller voor toegangsbeheer wordt bewust gemaakt van het statische IP-adres van de load balancer dat moet worden toegewezen aan de controllerservice voor toegangsbeheer en van het DNS-naamlabel dat wordt toegepast op de openbare IP-adresresource. De HTTPS-certificaten werken alleen goed als er een DNS-naamlabel wordt gebruikt om een FQDN te configureren voor het IP-adres van de ingress-controller.

1. Voeg de `--set controller.service.loadBalancerIP` parameter toe. Geef uw eigen openbare IP-adres op dat in de vorige stap is gemaakt.
1. Voeg de `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` parameter toe. Geef een DNS-naamlabel op dat moet worden toegepast op het openbare IP-adres dat in de vorige stap is gemaakt.

De ingangscontroller moet ook worden gepland op een Linux-knooppunt. Windows Server-knooppunten mogen de ingangscontroller niet uitvoeren. Er wordt een knooppuntselector opgegeven met behulp van de parameter `--set nodeSelector` om de Kubernetes-planner te laten weten dat de NGINX-ingangscontroller moet worden uitgevoerd op een Linux-knooppunt.

> [!TIP]
> In het volgende voorbeeld wordt een Kubernetes-naamruimte gemaakt voor de ingress-resources met de *naam ingress-basic.* Geef waar nodig een naamruimte op voor uw eigen omgeving. Als uw AKS-cluster niet Kubernetes RBAC is ingeschakeld, voegt u toe `--set rbac.create=false` aan de Helm-opdrachten.

> [!TIP]
> Als u behoud van [ip-adressen van clientbron wilt][client-source-ip] inschakelen voor aanvragen voor containers in uw cluster, voegt u toe `--set controller.service.externalTrafficPolicy=Local` aan de Helm-installatieopdracht. Het ip-adres van de clientbron wordt opgeslagen in de aanvraagheader onder *X-Forwarded-For.* Wanneer u een toegangscontroller gebruikt waarop behoud van ip-adressen van clientbron is ingeschakeld, werkt TLS-pass-through niet.

Werk het volgende script bij met het **IP-adres** van de controller voor ingress en een unieke naam die u wilt gebruiken voor het FQDN-voorvoegsel. 

> [!IMPORTANT]
> U moet de *STATIC_IP* en *DNS_LABEL* vervangen door uw eigen IP-adres en unieke naam bij het uitvoeren van de opdracht.

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
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="DNS_LABEL"
```

Wanneer de Kubernetes load balancer-service wordt gemaakt voor de NGINX-controller voor ingress, wordt uw statische IP-adres toegewezen, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Er zijn nog geen regels voor binnengangen gemaakt, dus wordt de standaardpagina 404 van de NGINX-ingress-controller weergegeven als u naar het openbare IP-adres bladert. In de volgende stappen worden regels voor ingress geconfigureerd.

U kunt controleren of het DNS-naamlabel is toegepast door als volgt een query uit te voeren op de FQDN op het openbare IP-adres:

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?ipAddress=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

De toegangscontroller is nu toegankelijk via het IP-adres of de FQDN.

## <a name="install-cert-manager"></a>Certificaatbeheer installeren

De NGINX-ingangscontroller ondersteunt TLS-beëindiging. Er zijn verschillende manieren om certificaten voor HTTPS op te halen en te configureren. In dit artikel wordt [gedemonstreerd hoe u certificaatbeheer][cert-manager]gebruikt. Dit biedt automatische functionaliteit voor het genereren en beheren van [Certificaten][lets-encrypt] versleutelen.

> [!NOTE]
> In dit artikel wordt de `staging` omgeving voor Let's Encrypt gebruikt. Gebruik en in productie-implementaties `letsencrypt-prod` `https://acme-v02.api.letsencrypt.org/directory` in de resourcedefinities en bij het installeren van de Helm-grafiek.

Als u de certificaatbeheercontroller wilt installeren in een Kubernetes RBAC-cluster, gebruikt u de volgende `helm install` opdracht:

```console
# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

Zie het certificaatbeheerproject voor meer informatie over de configuratie [van certificaatbeheer.][cert-manager]

## <a name="create-a-ca-cluster-issuer"></a>Een CA-clustervergever maken

Voordat certificaten kunnen worden uitgegeven, is voor certificaatbeheer een [issuer-][cert-manager-issuer] of [ClusterIssuer-resource][cert-manager-cluster-issuer] vereist. Deze Kubernetes-resources zijn qua functionaliteit identiek, maar werken in één naamruimte en werken `Issuer` `ClusterIssuer` in alle naamruimten. Zie de documentatie voor [certificaatuitgevers voor meer][cert-manager-issuer] informatie.

Maak een clustervergever, zoals `cluster-issuer.yaml` , met behulp van het volgende voorbeeldmanifest. Werk het e-mailadres bij met een geldig adres van uw organisatie:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Gebruik de opdracht om de vergever te `kubectl apply` maken.

```
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

De uitvoer moet er ongeveer als het volgende voorbeeld uit zien:

```
clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Demotoepassingen uitvoeren

Een controller voor ingress en een oplossing voor certificaatbeheer zijn geconfigureerd. We gaan nu twee demotoepassingen uitvoeren in uw AKS-cluster. In dit voorbeeld wordt Helm gebruikt om twee exemplaren van een eenvoudige 'Hallo wereld'-toepassing te implementeren.

Als u de controller voor ingress in actie wilt zien, moet u twee demotoepassingen uitvoeren in uw AKS-cluster. In dit voorbeeld gebruikt u om twee exemplaren van een eenvoudige `kubectl apply` *Hallo wereld-toepassing te* implementeren.

Maak een *aks-helloworld.yaml-bestand* en kopieer dit in het volgende yaml-voorbeeld:

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

Beide toepassingen worden nu uitgevoerd op uw Kubernetes-cluster, maar ze zijn geconfigureerd met een service van het type `ClusterIP` . Daarom zijn de toepassingen niet toegankelijk via internet. Als u deze openbaar beschikbaar wilt maken, maakt u een Kubernetes-ingress-resource. De resource voor binnenverkeer configureert de regels die verkeer naar een van de twee toepassingen doors groeperen.

In het volgende voorbeeld wordt verkeer naar het adres `https://demo-aks-ingress.eastus.cloudapp.azure.com/` doorgeleid naar de service met de naam `aks-helloworld` . Verkeer naar het adres `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` wordt doorgeleid naar de `ingress-demo` service. Werk de *hosts en* host *bij naar* de DNS-naam die u in een vorige stap hebt gemaakt.

Maak een bestand met de `hello-world-ingress.yaml` naam en kopieer het in het volgende yaml-voorbeeld.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
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

Maak de ingress-resource met behulp van de `kubectl apply` opdracht .

```
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

De uitvoer moet er ongeveer als het volgende voorbeeld uit zien:

```
ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Een certificaatobject maken

Vervolgens moet er een certificaatresource worden gemaakt. De certificaatresource definieert het gewenste X.509-certificaat. Zie certificaten voor [certificaatbeheer voor meer informatie.][cert-manager-certificates]

Certificaatbeheer heeft waarschijnlijk automatisch een certificaatobject voor u gemaakt met behulp van de ingress-shim, die automatisch wordt geïmplementeerd met certificaatbeheer sinds v0.2.2. Zie de documentatie voor [ingress-shim voor meer informatie.][ingress-shim]

Gebruik de opdracht om te controleren of het certificaat is `kubectl describe certificate tls-secret --namespace ingress-basic` gemaakt.

Als het certificaat is uitgegeven, ziet u uitvoer die vergelijkbaar is met de volgende:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Als u een extra certificaatresource wilt maken, kunt u dit doen met het volgende voorbeeldmanifest. Werk de *dnsNames* en *domeinen bij naar* de DNS-naam die u in een vorige stap hebt gemaakt. Als u een controller voor alleen intern verkeer gebruikt, geeft u de interne DNS-naam voor uw service op.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Gebruik de opdracht om de certificaatresource te `kubectl apply` maken.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>De configuratie van het ingress testen

Open een webbrowser naar de FQDN van uw Kubernetes-controller voor ingress, zoals *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* .

Omdat in deze voorbeelden wordt `letsencrypt-staging` gebruikt, wordt het uitgegeven TLS/SSL-certificaat niet vertrouwd door de browser. Accepteer de waarschuwingsprompt om door te gaan naar uw toepassing. De certificaatgegevens geven aan dat *dit valse TUSSENliggende X1-certificaat voor LE* is uitgegeven door Let's Encrypt. Dit valse certificaat geeft aan `cert-manager` dat de aanvraag correct is verwerkt en een certificaat van de provider heeft ontvangen:

![Let's Encrypt staging certificate (Faseringscertificaat versleutelen)](media/ingress/staging-certificate.png)

Wanneer u Let's Encrypt wijzigt om te gebruiken in plaats van , wordt een vertrouwd certificaat gebruikt dat is uitgegeven door Let's Encrypt, zoals wordt weergegeven `prod` in het volgende `staging` voorbeeld:

![Let's Encrypt certificate](media/ingress/certificate.png)

De demotoepassing wordt weergegeven in de webbrowser:

![Voorbeeld van toepassing](media/ingress/app-one.png)

Voeg nu *het pad /hello-world-two* toe aan de FQDN, zoals *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`* . De tweede demotoepassing met de aangepaste titel wordt weergegeven:

![Toepassingsvoorbeeld twee](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel is Helm gebruikt om de toegangsgegevens, certificaten en voorbeeld-apps te installeren. Wanneer u een Helm-grafiek implementeert, wordt er een aantal Kubernetes-resources gemaakt. Deze resources omvatten pods, implementaties en services. Als u deze resources wilt ops schonen, kunt u de volledige voorbeeldnaamruimte of de afzonderlijke resources verwijderen.

### <a name="delete-the-sample-namespace-and-all-resources"></a>De voorbeeldnaamruimte en alle resources verwijderen

Als u de volledige voorbeeldnaamruimte wilt verwijderen, gebruikt u de `kubectl delete` opdracht en geeft u de naam van uw naamruimte op. Alle resources in de naamruimte worden verwijderd.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Resources afzonderlijk verwijderen

Een meer gedetailleerde benadering is ook het verwijderen van de afzonderlijke resources die zijn gemaakt. Verwijder eerst de certificaatbronnen:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Vermeld nu de Helm-releases met de `helm list` opdracht . Zoek naar grafieken met de *naam nginx-ingress* en *cert-manager,* zoals wordt weergegeven in de volgende voorbeelduitvoer:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Verwijder de releases met de `helm uninstall` opdracht . In het volgende voorbeeld worden de implementaties van de NGINX-implementatie voor binnenvoer en certificaatbeheer verwijderd.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

Verwijder vervolgens de twee voorbeeldtoepassingen:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Verwijder de naamruimte zelf. Gebruik de `kubectl delete` opdracht en geef de naam van uw naamruimte op:

```console
kubectl delete namespace ingress-basic
```

Verwijder ten slotte het statische openbare IP-adres dat is gemaakt voor de controller voor ingress. Geef de *MC_* van de clusterresourcegroep op die u in de eerste stap van dit artikel hebt verkregen, *zoals MC_myResourceGroup_myAKSCluster_eastus:*

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Volgende stappen

Dit artikel bevat enkele externe onderdelen voor AKS. Zie de volgende projectpagina's voor meer informatie over deze onderdelen:

- [Helm CLI][helm-cli]
- [NGINX-controller voor ingress][nginx-ingress]
- [Certificaatbeheer][cert-manager]

U kunt ook het volgende doen:

- [Een eenvoudige controller voor ingress maken met externe netwerkconnectiviteit][aks-ingress-basic]
- [De invoegtoepassing http-toepassingsroutering inschakelen][aks-http-app-routing]
- [Een controller voor ingress maken die gebruikmaakt van een intern, privénetwerk en IP-adres][aks-ingress-internal]
- [Een controller voor ingress maken die gebruikmaakt van uw eigen TLS-certificaten][aks-ingress-own-tls]
- [Maak een controller voor verkeer met een dynamisch openbaar IP-adres en configureer Let's Encrypt om automatisch TLS-certificaten te genereren][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-static-ip]: static-ip.md
