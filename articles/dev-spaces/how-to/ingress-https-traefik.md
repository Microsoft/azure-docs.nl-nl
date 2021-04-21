---
title: Een aangepaste traefik-controller voor verkeer gebruiken en HTTPS configureren
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Meer informatie over het configureren van Azure Dev Spaces voor het gebruik van een aangepaste traefik-ingress-controller en het configureren van HTTPS met die controller voor ingress
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s
ms.custom: devx-track-js
ms.openlocfilehash: 76a89545b8edc700928c1c2fe0e91dfc5d3127b9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777487"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Een aangepaste traefik-controller voor verkeer gebruiken en HTTPS configureren

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

In dit artikel wordt beschreven hoe u Azure Dev Spaces configureert voor het gebruik van een aangepaste traefik-ingress-controller. In dit artikel wordt ook beschreven hoe u die aangepaste controller voor verkeer configureert voor het gebruik van HTTPS.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen account hebt, kunt u [een gratis account aanmaken][azure-account-create].
* [Azure CLI geïnstalleerd][az-cli].
* [Azure Kubernetes Service cluster (AKS) met Azure Dev Spaces ingeschakeld] [qs-cli].
* [kubectl][kubectl] geïnstalleerd.
* [Helm 3 geïnstalleerd.][helm-installed]
* [Een aangepast domein met][custom-domain] een [DNS-zone.][dns-zone] In dit artikel wordt ervan uitgenomen dat het aangepaste domein en de DNS-zone zich in dezelfde resourcegroep als uw AKS-cluster bevindt, maar het is mogelijk om een aangepast domein en een DNS-zone in een andere resourcegroep te gebruiken.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Een aangepaste traefik-controller voor verkeer configureren

Maak verbinding met uw cluster met behulp van [kubectl][kubectl], de Kubernetes-opdrachtregelclient. Gebruik de opdracht [az aks get-credentials][az-aks-get-credentials] om `kubectl` zodanig te configureren dat er verbinding wordt gemaakt met het Kubernetes-cluster. Bij deze opdracht worden referenties gedownload en wordt Kubernetes CLI geconfigureerd voor het gebruik van deze referenties.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Als u de verbinding met uw cluster wilt controleren, gebruikt u de opdracht [kubectl get][kubectl-get] om een lijst met clusterknooppunten te retourneren.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Voeg de [officiële stabiele Helm-opslagplaats toe,][helm-stable-repo]die de Helm-grafiek voor de controller voor verkeer van de traefik-ingress bevat.

```console
helm repo add stable https://charts.helm.sh/stable
```

Maak een Kubernetes-naamruimte voor de controller voor het verkeer van traefik en installeer deze met `helm` .

> [!NOTE]
> Als kubernetes RBAC niet is ingeschakeld voor uw AKS-cluster, verwijdert u de parameter *--set rbac.enabled=true.*

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> In het bovenstaande voorbeeld wordt een openbaar eindpunt gemaakt voor uw controller voor binnendingsingressen. Als u in plaats daarvan een privé-eindpunt wilt gebruiken voor uw controller voor ingress, voegt u de *--set service.annotations toe. service \\ .beta \\ .kubernetes \\ .io/azure-load-balancer-internal"=true* parameter to the *Helm install* command.
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Dit privé-eindpunt wordt zichtbaar in het virtuele netwerk waar uw AKS-cluster is geïmplementeerd.

Haal het IP-adres van de controllerservice traefik ingress op met [behulp van kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

In de voorbeelduitvoer ziet u de IP-adressen voor alle services in de *traefik-naamruimte.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Voeg een *A-record* toe aan uw DNS-zone met het externe IP-adres van de traefik-service met [az network dns record-set a add-record][az-network-dns-record-set-a-add-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

In het bovenstaande voorbeeld wordt een *A-record* toegevoegd aan *MY_CUSTOM_DOMAIN* DNS-zone.

In dit artikel gebruikt u de [Azure Dev Spaces Bike Sharing-voorbeeldtoepassing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) om het gebruik van Azure Dev Spaces te demonstreren. Kloon de toepassing uit GitHub en navigeer naar de bijbehorende map:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Open [values.yaml en][values-yaml] maak de volgende updates:
* Vervang alle exemplaren van *<REPLACE_ME_WITH_HOST_SUFFIX>* door *traefik. MY_CUSTOM_DOMAIN* uw domein gebruiken voor *MY_CUSTOM_DOMAIN*. 
* Vervang *kubernetes.io/ingress.class: traefik-azds # Dev Spaces-specific* *door kubernetes.io/ingress.class: traefik # Custom Ingress*. 

Hieronder vindt u een voorbeeld van een bijgewerkt `values.yaml` bestand:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Sla uw wijzigingen op en sluit het bestand.

Maak de *dev-ruimte* met uw voorbeeldtoepassing met behulp van `azds space select` .

```console
azds space select -n dev -y
```

Implementeer de voorbeeldtoepassing met behulp van `helm install` .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

In het bovenstaande voorbeeld wordt de voorbeeldtoepassing geïmplementeerd in *de naamruimte dev.*

Geef de URL's weer voor toegang tot de voorbeeldtoepassing met behulp van `azds list-uris` .

```console
azds list-uris
```

In de onderstaande uitvoer ziet u de voorbeeld-URL's van `azds list-uris` .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navigeer *naar de bikesharingweb-service* door de openbare URL te openen met de `azds list-uris` opdracht . In het bovenstaande voorbeeld is de openbare URL voor de *bikesharingweb-service* `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

> [!NOTE]
> Als u een foutpagina ziet in plaats van de  *bikesharingweb-service,* controleert u of u zowel de *kubernetes.io/ingress.class-aantekening* als de host in het bestand *values.yaml hebt* bijgewerkt.

Gebruik de opdracht om een onderliggende ruimte onder dev te maken en de URL's weer te geven `azds space select` voor toegang tot de onderliggende dev-ruimte. 

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

In de onderstaande uitvoer ziet u de voorbeeld-URL's van voor toegang tot de voorbeeldtoepassing in de onderliggende `azds list-uris` dev-ruimte *azureuser1.*

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navigeer *naar de bikesharingweb-service* in de onderliggende dev-ruimte *azureuser1* door de openbare URL te openen met de `azds list-uris` opdracht . In het bovenstaande voorbeeld is de openbare URL voor de *bikesharingweb-service* in de onderliggende dev-ruimte *azureuser1* `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>De traefik-ingress-controller configureren voor het gebruik van HTTPS

Gebruik [certificaatbeheer om het][cert-manager] beheer van het TLS-certificaat te automatiseren bij het configureren van uw traefik-controller voor het gebruik van HTTPS. Gebruik `helm` om de grafiek *certmanager te* installeren.

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Maak een `letsencrypt-clusterissuer.yaml` bestand en werk het e-mailveld bij met uw e-mailadres.

```yaml
apiVersion: cert-manager.io/v1alpha2
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
            class: traefik
```

> [!NOTE]
> Voor het testen is er ook een [faseringsserver][letsencrypt-staging-issuer] die u kunt gebruiken voor uw *ClusterIssuer.*

Gebruik om `kubectl` toe te `letsencrypt-clusterissuer.yaml` passen.

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Verwijder de vorige *traefik* *ClusterRole* en *ClusterRoleBinding* en upgrade vervolgens traefik om HTTPS te gebruiken met `helm` .

> [!NOTE]
> Als kubernetes RBAC niet is ingeschakeld voor uw AKS-cluster, verwijdert u de parameter *--set rbac.enabled=true.*

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Haal het bijgewerkte IP-adres van de controllerservice traefik ingress op met [behulp van kubectl get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

In de voorbeelduitvoer ziet u de IP-adressen voor alle services in de *traefik-naamruimte.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Voeg een *A-record* toe aan uw DNS-zone met het nieuwe externe IP-adres van de traefik-service met [az network dns record-set a add-record][az-network-dns-record-set-a-add-record] en verwijder de vorige *A-record* met az network [dns record-set a remove-record][az-network-dns-record-set-a-remove-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_NEW_EXTERNAL_IP

az network dns record-set a remove-record \
    --resource-group myResourceGroup \
    --zone-name  MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address PREVIOUS_EXTERNAL_IP
```

In het bovenstaande voorbeeld wordt *de A-record* in *MY_CUSTOM_DOMAIN* DNS-zone bijgewerkt voor gebruik *PREVIOUS_EXTERNAL_IP*.

Werk [values.yaml bij met][values-yaml] de details voor het gebruik van *certificaatbeheer* en HTTPS. Hieronder vindt u een voorbeeld van een bijgewerkt `values.yaml` bestand:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Upgrade de voorbeeldtoepassing met behulp van `helm` :

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Navigeer naar de voorbeeldtoepassing in *de onderliggende ruimte dev/azureuser1* en u wordt omgeleid om HTTPS te gebruiken.

> [!IMPORTANT]
> Het kan 30 minuten of meer duren voordat de DNS-wijzigingen zijn voltooid en uw voorbeeldtoepassing toegankelijk is.

U ziet ook dat de pagina wordt geladen, maar dat er fouten worden weergegeven in de browser. Als u de browserconsole opent, ziet u dat de fout is gerelateerd aan een HTTPS-pagina die HTTP-resources probeert te laden. Bijvoorbeeld:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

U kunt deze fout oplossen door [BikeSharingWeb/azds.yaml][azds-yaml] bij te werken om *traefik* te gebruiken voor *kubernetes.io/ingress.class* en uw aangepaste domein voor *$(hostSuffix)*. Bijvoorbeeld:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

Werk [BikeSharingWeb/package.jsbij][package-json] met een afhankelijkheid voor het *URL-pakket.*

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Werk de *methode getApiHostAsync* in [BikeSharingWeb/lib/helpers.js][helpers-js] bij om HTTPS te gebruiken:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Navigeer naar `BikeSharingWeb` de map en gebruik om uw `azds up` bijgewerkte BikeSharingWeb-service uit te voeren.

```console
cd ../BikeSharingWeb/
azds up
```

Navigeer naar de voorbeeldtoepassing in de onderliggende *ruimte dev/azureuser1* en u wordt zonder fouten omgeleid om HTTPS te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe Azure Dev Spaces werkt.

> [!div class="nextstepaction"]
> [Hoe Azure Dev Spaces werkt](../how-dev-spaces-works.md)


[az-cli]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a#az_network_dns_record_set_a_add_record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a#az_network_dns_record_set_a_remove_record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
