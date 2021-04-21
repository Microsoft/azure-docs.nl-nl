---
title: Een ingress maken met automatische TLS
titleSuffix: Azure Kubernetes Service
description: Informatie over het installeren en configureren van een NGINX-controller voor verkeer dat gebruikmaakt van Let's Encrypt voor het automatisch genereren van TLS-certificaten in een Azure Kubernetes Service -cluster (AKS).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: a3f78c0777d1377c8afdc5b43b457873de395cd6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779791"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Een HTTPS-controller voor Azure Kubernetes Service (AKS) maken

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

In dit artikel wordt beschreven hoe u de [NGINX-controller][nginx-ingress] voor een Azure Kubernetes Service (AKS) implementeert. Het [certificaatbeheerproject][cert-manager] wordt gebruikt voor het automatisch genereren en configureren van [Let's][lets-encrypt] Encrypt-certificaten. Ten slotte worden twee toepassingen uitgevoerd in het AKS-cluster, die allemaal toegankelijk zijn via één IP-adres.

U kunt ook het volgende doen:

- [Een eenvoudige controller voor ingress maken met externe netwerkconnectiviteit][aks-ingress-basic]
- [De invoegtoepassing HTTP-toepassingsroutering inschakelen][aks-http-app-routing]
- [Een controller voor ingress maken die gebruikmaakt van een intern, privénetwerk en IP-adres][aks-ingress-internal]
- [Een controller voor het binnen- en weer gaan maken die gebruikmaakt van uw eigen TLS-certificaten][aks-ingress-own-tls]
- [Een controller voor verkeer maken die gebruikmaakt van Let's Encrypt om automatisch TLS-certificaten te genereren met een statisch openbaar IP-adres][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

In dit artikel wordt ook ervan uitgenomen dat u [een aangepast][custom-domain] domein hebt met een [DNS-zone][dns-zone] in dezelfde resourcegroep als uw AKS-cluster.

In dit artikel wordt [Helm 3 gebruikt][helm] om de NGINX-ingresscontroller en certificaatbeheer te installeren. Zorg ervoor dat u de nieuwste versie van Helm gebruikt en toegang hebt tot de *opslagplaatsen ingress-nginx* *en jetstack* Helm. Zie de helm-installatie docs voor [upgrade-instructies.][helm-install] Zie Toepassingen installeren met Helm in Azure Kubernetes Service [(AKS) voor][use-helm]meer informatie over het configureren en gebruiken van Helm.

Voor dit artikel moet u ook Azure CLI versie 2.0.64 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-an-ingress-controller"></a>Een controller voor ingress maken

Gebruik de opdracht om `helm` *nginx-ingress* te installeren om de controller voor ingress te maken. Voor toegevoegde redundantie worden er twee replica's van de NGINX-ingangscontrollers geïmplementeerd met de parameter `--set controller.replicaCount`. Als u volledig wilt profiteren van het uitvoeren van replica's van de controller voor ingress, moet u ervoor zorgen dat uw AKS-cluster meer dan één knooppunt heeft.

De ingangscontroller moet ook worden gepland op een Linux-knooppunt. Windows Server-knooppunten mogen de ingangscontroller niet uitvoeren. Er wordt een knooppuntselector opgegeven met behulp van de parameter `--set nodeSelector` om de Kubernetes-planner te laten weten dat de NGINX-ingangscontroller moet worden uitgevoerd op een Linux-knooppunt.

> [!TIP]
> In het volgende voorbeeld wordt een Kubernetes-naamruimte gemaakt voor de ingress-resources met de *naam ingress-basic.* Geef waar nodig een naamruimte op voor uw eigen omgeving.

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

Tijdens de installatie wordt er een openbaar IP-adres van Azure gemaakt voor de controller voor het ingress-adres. Dit openbare IP-adres is statisch voor de levensduur van de controller voor ingressen. Als u de controller voor ingress verwijdert, gaat de toewijzing van het openbare IP-adres verloren. Als u vervolgens een extra controller voor binnengangen maakt, wordt er een nieuw openbaar IP-adres toegewezen. Als u het gebruik van het openbare IP-adres wilt behouden, kunt u in plaats daarvan een controller voor ingress maken [met een statisch openbaar IP-adres.][aks-ingress-static-tls]

Gebruik de opdracht om het openbare IP-adres op `kubectl get service` te halen. Het duurt enkele minuten voordat het IP-adres is toegewezen aan de service.

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Er zijn nog geen regels voor ingressen gemaakt. Als u naar het openbare IP-adres bladert, wordt de standaardpagina 404 van de NGINX-ingresscontroller weergegeven.

## <a name="add-an-a-record-to-your-dns-zone"></a>Een A-record toevoegen aan uw DNS-zone

Voeg een *A-record* toe aan uw DNS-zone met het externe IP-adres van de NGINX-service met [az network dns record-set a add-record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Optioneel kunt u een FQDN configureren voor het IP-adres van de controller voor ingress in plaats van een aangepast domein. Dit voorbeeld is voor een Bash-shell.
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

De NGINX-ingangscontroller ondersteunt TLS-beëindiging. Er zijn verschillende manieren om certificaten voor HTTPS op te halen en te configureren. In dit artikel wordt het gebruik van [certificaatbeheer gedemonstreerd.][cert-manager]Dit biedt automatische functionaliteit voor het genereren en beheren van [Lets Encrypt-certificaten.][lets-encrypt]

De certificaatbeheercontroller installeren:

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

Zie het certificaatbeheerproject voor meer informatie over de configuratie [van certificaatbeheer.][cert-manager]

## <a name="create-a-ca-cluster-issuer"></a>Een CA-clustervergever maken

Voordat certificaten kunnen worden uitgegeven, is voor certificaatbeheer een [issuer-][cert-manager-issuer] of [ClusterIssuer-resource][cert-manager-cluster-issuer] vereist. Deze Kubernetes-resources zijn identiek qua functionaliteit, maar werken in één naamruimte en werken `Issuer` `ClusterIssuer` in alle naamruimten. Zie de documentatie voor [certificaatbeheerder voor meer][cert-manager-issuer] informatie.

Maak een clustervergever, zoals `cluster-issuer.yaml` , met behulp van het volgende voorbeeldmanifest. Werk het e-mailadres bij met een geldig adres van uw organisatie:

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

Gebruik de opdracht om de vergever te `kubectl apply` maken.

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Demotoepassingen uitvoeren

Er zijn een controller voor binnen- en certificaatbeheer geconfigureerd. We gaan nu twee demotoepassingen uitvoeren in uw AKS-cluster. In dit voorbeeld wordt Helm gebruikt om twee exemplaren van een eenvoudige *Hallo wereld-toepassing te* implementeren.

Als u de controller voor ingress in actie wilt zien, moet u twee demotoepassingen uitvoeren in uw AKS-cluster. In dit voorbeeld gebruikt u om twee exemplaren van een eenvoudige `kubectl apply` *Hallo wereld-toepassing te* implementeren.

Maak een *aks-helloworld-one.yaml-bestand* en kopieer het in het volgende yaml-voorbeeld:

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

Maak een *aks-helloworld-two.yaml-bestand* en kopieer het in het volgende yaml-voorbeeld:

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

Voer de twee demotoepassingen uit met `kubectl apply` behulp van :

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Een route voor ingress maken

Beide toepassingen worden nu uitgevoerd op uw Kubernetes-cluster. Ze zijn echter geconfigureerd met een service van het type `ClusterIP` en zijn niet toegankelijk via internet. Als u deze openbaar beschikbaar wilt maken, maakt u een Kubernetes-ingress-resource. De resource voor het binnenverkeer configureert de regels die verkeer naar een van de twee toepassingen doors groeperen.

In het volgende voorbeeld wordt verkeer naar het adres *hello-world-ingress gebruikt. MY_CUSTOM_DOMAIN* wordt doorgeleid naar de *service aks-helloworld-one.* Verkeer naar het adres *hello-world-ingress. MY_CUSTOM_DOMAIN/hello-world-two* wordt doorgeleid naar de *service aks-helloworld-two.* Verkeer naar *hello-world-ingress. MY_CUSTOM_DOMAIN/static* wordt gerouteerd naar de service met de naam *aks-helloworld-one* voor statische assets.

> [!NOTE]
> Als u een FQDN hebt geconfigureerd voor het IP-adres van de ingress-controller in plaats van een aangepast domein, gebruikt u de FQDN in plaats van *hello-world-ingress. MY_CUSTOM_DOMAIN*. Als uw FQDN bijvoorbeeld is *demo-aks-ingress.eastus.cloudapp.azure.com,* vervangt u *hello-world-ingress. MY_CUSTOM_DOMAIN* met *demo-aks-ingress.eastus.cloudapp.azure.com* in `hello-world-ingress.yaml` .

Maak een bestand met de `hello-world-ingress.yaml` naam met behulp van het onderstaande YAML-voorbeeld. Werk de *hosts en* hosts *bij naar* de DNS-naam die u in een vorige stap hebt gemaakt.

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

Maak de ingress-resource met behulp van de `kubectl apply` opdracht .

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Controleren of er een certificaatobject is gemaakt

Vervolgens moet er een certificaatresource worden gemaakt. De certificaatresource definieert het gewenste X.509-certificaat. Zie certificaten voor [certificaatbeheer voor meer informatie.][cert-manager-certificates] Certificaatbeheer heeft automatisch een certificaatobject voor u gemaakt met behulp van de ingress-shim, die automatisch wordt geïmplementeerd met certificaatbeheer sinds v0.2.2. Zie de documentatie voor [ingress-shim voor meer informatie.][ingress-shim]

Als u wilt controleren of het certificaat is gemaakt, gebruikt u de opdracht en controleert u of `kubectl get certificate --namespace ingress-basic` *READY* *true* is. Dit kan enkele minuten duren.

```console
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>De configuratie van het ingress testen

Open een webbrowser om *hello-world-ingress te openen. MY_CUSTOM_DOMAIN* van uw Kubernetes-controller voor binnengressen. U wordt omgeleid om HTTPS te gebruiken, het certificaat wordt vertrouwd en de demotoepassing wordt weergegeven in de webbrowser. Voeg het *pad /hello-world-two* toe en u ziet dat de tweede demotoepassing met de aangepaste titel wordt weergegeven.

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel is Helm gebruikt om de toegangs-onderdelen, certificaten en voorbeeld-apps te installeren. Wanneer u een Helm-grafiek implementeert, wordt er een aantal Kubernetes-resources gemaakt. Deze resources omvatten pods, implementaties en services. Als u deze resources wilt ops schonen, kunt u de volledige voorbeeldnaamruimte of de afzonderlijke resources verwijderen.

### <a name="delete-the-sample-namespace-and-all-resources"></a>De voorbeeldnaamruimte en alle resources verwijderen

Als u de volledige voorbeeldnaamruimte wilt verwijderen, gebruikt u de `kubectl delete` opdracht en geeft u de naam van uw naamruimte op. Alle resources in de naamruimte worden verwijderd.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Resources afzonderlijk verwijderen

Een meer gedetailleerde benadering is ook het verwijderen van de afzonderlijke resources die zijn gemaakt. Verwijder eerst de resources van de clustervergever:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Vermeld de Helm-releases met de `helm list` opdracht . Zoek naar grafieken met *de naam nginx* *en certificaatbeheer,* zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Verwijder de releases met de `helm uninstall` opdracht . In het volgende voorbeeld worden de NGINX-implementaties voor in- en certificaatbeheer verwijderd.

```console
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Verwijder vervolgens de twee voorbeeldtoepassingen:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Verwijder de toegangsroute die verkeer naar de voorbeeld-apps heeft geleid:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Ten slotte kunt u de naamruimte zelf verwijderen. Gebruik de `kubectl delete` opdracht en geef de naam van uw naamruimte op:

```console
kubectl delete namespace ingress-basic
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
- [Maak een controller voor verkeer dat gebruikmaakt van Let's Encrypt om automatisch TLS-certificaten te genereren met een statisch openbaar IP-adres][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/#az_network_dns_record_set_a_add_record
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
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
