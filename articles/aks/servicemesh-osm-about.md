---
title: Service Mesh openen (preview)
description: Open Service Mesh (OSM) in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 65b02ae1baef97442828de747249ab6ffeaf2417
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599468"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Een Service Mesh AKS-invoegversie openen (preview)

## <a name="overview"></a>Overzicht

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) is een lichtgewicht, extensible Cloud Native-service-mesh waarmee gebruikers op uniforme wijze waarneembaarheidsfuncties kunnen beheren, beveiligen en out-of-the-box waarneembaarheidsfuncties kunnen krijgen voor zeer dynamische microserviceomgevingen.

OSM voert een op Envoy gebaseerd besturingsvlak uit op Kubernetes, kan worden geconfigureerd met [SMI-API's](https://smi-spec.io/) en werkt door een Envoy-proxy te injecteren als een sidecar-container naast elk exemplaar van uw toepassing. De Envoy-proxy bevat regels voor toegangsbeheerbeleid en voert deze uit, implementeert routeringsconfiguratie en legt metrische gegevens vast. Het besturingsvlak configureert voortdurend -proxies om ervoor te zorgen dat beleidsregels en routeringsregels up-to-date zijn en ervoor zorgen dat de proxies in orde zijn.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Mogelijkheden en functies

OSM biedt de volgende mogelijkheden en functies om een cloudeigen service-mesh te bieden voor uw Azure Kubernetes Service (AKS)-clusters:

- Communicatie van service naar service beveiligen door mTLS in te stellen

- Eenvoudig toepassingen onboarden op de mesh door automatische sidecar-injectie van Envoy-proxy in te stellen

- Eenvoudig en transparant configuraties voor het verplaatsen van verkeer op implementaties

- De mogelijkheid om een fijnfinieerd toegangsbeheerbeleid voor services te definiëren en uit te voeren

- Waarneembaarheid en inzichten in metrische gegevens van toepassingen voor het debuggen en bewaken van services

- Integratie met externe certificaatbeheerservices/-oplossingen met een pluggable interface

## <a name="scenarios"></a>Scenario's

OSM kan uw AKS-implementaties ondersteunen met de volgende scenario's:

- Versleutelde communicatie bieden tussen service-eindpunten die in het cluster zijn geïmplementeerd

- Autorisatie van verkeer van zowel HTTP-/HTTPS- als TCP-verkeer in de mesh

- Configuratie van gewogen verkeerscontroles tussen twee of meer services voor A/B- of Canary-implementaties

- Verzameling en weergave van KPI's van toepassingsverkeer

## <a name="osm-service-quotas-and-limits-preview"></a>Quota en limieten voor OSM-service (preview)

OsM preview-beperkingen voor servicequota en -limieten vindt u op de pagina [AKS-quota en regionale limieten.](https://docs.microsoft.com/azure/aks/quotas-skus-regions)

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/install-osm-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/install-osm-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/install-osm-binary-windows.md)]

::: zone-end

> [!WARNING]
> Probeer osm niet te installeren vanuit het binaire bestand met behulp van `osm install` . Dit resulteert in een installatie van OSM die niet is geïntegreerd als een invoegproces voor AKS.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>De `AKS-OpenServiceMesh` preview-functie registreren

Als u een AKS-cluster wilt maken dat gebruik kan maken van de Open Service Mesh-invoegversie, moet u de `AKS-OpenServiceMesh` functievlag inschakelen voor uw abonnement.

Registreer de `AKS-OpenServiceMesh` functievlag met behulp van [de opdracht az feature register,][az-feature-register] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Het duurt enkele minuten voordat de status Geregistreerd _we weergeven._ Controleer de registratiestatus met behulp van [de opdracht az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de resourceprovider _Microsoft.ContainerService_ met behulp van de [opdracht az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Open Service Mesh (OSM) Azure Kubernetes Service (AKS) voor een nieuw AKS-cluster installeren

Voor een nieuw implementatiescenario voor AKS-clusters begint u met een geheel nieuwe implementatie van een AKS-cluster, waardoor de OSM-invoeg-invoegbewerking wordt uitgevoerd bij het maken van het cluster.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voorbeeld wordt een resourcegroep met de _naam myOsmAksGroup gemaakt_ op de _locatie eastus2_ (regio):

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Een AKS-cluster implementeren met de OSM-invoegversie ingeschakeld

U implementeert nu een nieuw AKS-cluster met de OSM-invoegversie ingeschakeld.

> [!NOTE]
> Houd er rekening mee dat de volgende AKS-implementatieopdracht kortstondige schijven van het besturingssysteem gebruikt. Meer informatie over kortstondige besturingssysteemschijven voor [AKS vindt](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os) u hier

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Toegangsreferenties voor AKS-cluster op halen

Haal toegangsreferenties op voor het nieuwe beheerde Kubernetes-cluster.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Open Service Mesh (OSM) Azure Kubernetes Service (AKS) inschakelen voor een bestaand AKS-cluster

Voor een bestaand AKS-clusterscenario gaat u de OSM-invoegversie inschakelen voor een bestaand AKS-cluster dat al is geïmplementeerd.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>De OSM-invoegversie inschakelen voor een bestaand AKS-cluster

Als u de AKS OSM-invoegregel wilt inschakelen, moet u de opdracht uitvoeren `az aks enable-addons --addons` die de parameter doorgeeft `open-service-mesh`

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

Als het goed is, ziet u uitvoer die vergelijkbaar is met de onderstaande uitvoer om te bevestigen dat de AKS OSM-invoeg-app is geïnstalleerd.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>De installatie van de AKS OSM-invoegproces valideren

Er moeten verschillende opdrachten worden uitgevoerd om te controleren of alle onderdelen van de AKS OSM-invoeg-on zijn ingeschakeld en worden uitgevoerd:

Eerst kunnen we een query uitvoeren op de invoegtoepassingen van het cluster om de ingeschakelde status van de geïnstalleerde invoegtoepassingen te controleren. De volgende opdracht moet 'true' retourneren.

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Met de `kubectl` volgende opdrachten wordt de status van de osm-controller rapporteren.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>Toegang tot de AKS OSM-invoeg-on

Momenteel kunt u de configuratie van de OSM-controller openen en configureren via de configmap. Als u de configuratie-instellingen voor de OSM-controller wilt weergeven, moet u een query uitvoeren op de osm-config configmap via om `kubectl` de configuratie-instellingen ervan weer te geven.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

De uitvoer van de OSM-configmap moet er als volgt uitzien:

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254/32,168.63.129.16/32,<YOUR_API_SERVER_PUBLIC_IP>/32",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

U ziet **dat permissive_traffic_policy_mode** is geconfigureerd als **true.** De modus voor het afdwingen van verkeersbeleid in OSM is een modus waarin het afdwingen van [SMI-verkeersbeleid](https://smi-spec.io/) wordt omzeild. In deze modus detecteert OSM automatisch services die deel uitmaken van de service-mesh en programma's voor verkeersbeleidsregels op elke Envoy-proxy-sidecar om te kunnen communiceren met deze services.

> [!WARNING]
> Voordat u doorgaat, controleert u of de modus voor uw verkeersbeleid is ingesteld op true. Als dat niet het geval is, wijzigt u deze in **true** met behulp van de onderstaande opdracht

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Een nieuwe toepassing implementeren die moet worden beheerd door de AKS-invoegtoepassing (Open Service Mesh) Azure Kubernetes Service (OSM)

### <a name="before-you-begin"></a>Voordat u begint

Bij de stappen die in dit scenario worden beschreven, wordt ervan uitgenomen dat u een AKS-cluster hebt gemaakt (Kubernetes en hoger, met Kubernetes RBAC ingeschakeld), verbinding hebt gemaakt met het cluster (als u hulp nodig hebt met een van deze items, bekijkt u de `1.19+` `kubectl` [AKS-quickstart](./kubernetes-walkthrough.md)en hebt u de AKS OSM-invoegversie geïnstalleerd.

U moet de volgende resources hebben geïnstalleerd:

- De Azure CLI, versie 2.20.0 of hoger
- De `aks-preview` extensieversie 0.5.5 of hoger
- OSM-versie v0.8.0 of hoger
- apt-get install jq

### <a name="create-namespaces-for-the-application"></a>Naamruimten voor de toepassing maken

In deze walkthrough gebruiken we de OSM-boekwinkeltoepassing met de volgende Kubernetes-services:

- bookbuyer
- bookthief
- Boekhandel
- bookwarehouse

Maak naamruimten voor elk van deze toepassingsonderdelen.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

U moet de volgende uitvoer zien:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Onboarding van de naamruimten die moeten worden beheerd door OSM

Wanneer u de naamruimten aan de OSM-mesh toevoegt, kan de OSM-controller automatisch de Envoy sidecar-proxycontainers met uw toepassing injecteren. Voer de volgende opdracht uit om de naamruimten van de OSM-boekwinkeltoepassing te onboarden.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

U moet de volgende uitvoer zien:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>De Bookstore-toepassing implementeren in het AKS-cluster

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Alle implementatie-uitvoer wordt hieronder samengevat.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="checkpoint-what-got-installed"></a>Controlepunt: Wat is er geïnstalleerd?

De voorbeeldtoepassing Boekwinkel is een app met meerdere lagen die uit vier services bestaat, zoals de bookbuyer, bookthief, bookstore en bookwarehouse. Zowel de bookbuyer als de bookthief-service communiceren met de boekwinkelservice om boeken op te halen uit de boekwinkelservice. De boekwinkel haalt boeken op uit de bookwarehouse-service om de bookbuyer en bookthief te leveren. Dit is een eenvoudige toepassing met meerdere lagen die goed werkt om te laten zien hoe een service-mesh kan worden gebruikt voor het beveiligen en autoreren van communicatie tussen de services van toepassingen. Terwijl we doorgaan met het scenario, zullen we SMI-beleidsregels (Service Mesh Interface) in- en uitschakelen, zodat de services wel en niet via OSM kunnen communiceren. Hieronder vindt u een architectuurdiagram van wat er is geïnstalleerd voor de boekwinkeltoepassing.

![Architectuur van osm-bookbuyer-app](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Controleer of de Bookstore-toepassing wordt uitgevoerd in het AKS-cluster

Vanaf nu hebben we de toepassing bookstore mulit-container geïmplementeerd, maar deze is alleen toegankelijk vanuit het AKS-cluster. Latere zelfstudies helpen u bij het beschikbaar maken van de toepassing buiten het cluster via een controller voor ingressen. Op dit moment gebruiken we port forwarding toegang tot de bookbuyer-toepassing in het AKS-cluster om te controleren of deze boeken koopt bij de boekwinkelservice.

Om te controleren of de toepassing in het cluster wordt uitgevoerd, gebruiken we een poort vooruit om zowel de gebruikersinterface van de bookbuyer als de bookthief-onderdelen te bekijken.

Eerst halen we de naam van de bookbuyer-pod op

```azurecli-interactive
kubectl get pod -n bookbuyer
```

De uitvoer ziet er als volgt uit. Aan uw bookbuyer-pod wordt een unieke naam toegevoegd.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Zodra we de naam van de pod hebben, kunnen we de opdracht port-forward gebruiken om een tunnel van ons lokale systeem naar de toepassing in het AKS-cluster in te stellen. Voer de volgende opdracht uit om port forward in te stellen voor de lokale systeempoort 8080. Gebruik opnieuw de opgegeven podnaam van de bookbuyer.

> [!NOTE]
> Voor alle port forwarding kunt u het beste een extra terminal gebruiken, zodat u deze walkthrough kunt blijven uitvoeren en de tunnel niet kunt loskoppelen. Het is ook het beste dat u de poort forward tunnel buiten de Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

De uitvoer ziet er ongeveer als het goed is uit.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Terwijl de port forwarding is, navigeert u vanuit een browser naar de volgende `http://localhost:8080` URL. U ziet nu de gebruikersinterface van de bookbuyer-toepassing in de browser, vergelijkbaar met de onderstaande afbeelding.

![Afbeelding van de gebruikersinterface van de OSM bookbuyer-app](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

U ziet ook dat het totale aantal boeken dat is gekocht, blijft oplopen tot de boekwinkel v1-service. De boekwinkel v2-service is nog niet geïmplementeerd. We implementeren de bookstore v2-service wanneer we het SMI-beleid voor het splitsen van verkeer demonstreren.

U kunt ook hetzelfde controleren voor de bookthief-service.

```azurecli-interactive
kubectl get pod -n bookthief
```

De uitvoer ziet er als volgt uit. Aan uw bookthief-pod wordt een unieke naam toegevoegd.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Port forward naar bookthief pod.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Navigeer naar de volgende URL vanuit een browser `http://localhost:8080` . Als het goed is, ziet u dat het boekboek momenteel boeken van de boekwinkel steelt. Later implementeren we een verkeersbeleid om de bookthief te stoppen.

![AFBEELDING van de GEBRUIKERSINTERFACE van de OSM-bookthief-app](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>OsM Permissive Traffic Mode uitschakelen voor de mesh

Zoals eerder vermeld bij het weergeven van de configuratie van het OSM-cluster, wordt in de OSM-configuratie standaard het beleid voor de verkeersmodus ingeschakeld. In deze modus wordt het afdwingen van verkeersbeleid omzeild en detecteert OSM automatisch services die deel uitmaken van de service-mesh en programma's voor verkeersbeleidsregels op elke Sidecar van de Envoy-proxy om te kunnen communiceren met deze services.

We schakelen nu het beleid voor de toegestane verkeersmodus uit en OSM heeft expliciet [SMI-beleid](https://smi-spec.io/) nodig dat op het cluster is geïmplementeerd om communicatie vanuit elke service toe te staan in de mesh. Als u de modus voor het permissieve verkeer wilt uitschakelen, moet u de volgende opdracht uitvoeren om de eigenschap configmap bij te werken en de waarde te wijzigen van `true` in `false` .

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

De uitvoer ziet er als volgt uit. Aan uw bookthief-pod wordt een unieke naam toegevoegd.

```Output
configmap/osm-config patched
```

Als u wilt controleren of de permissieve verkeersmodus is uitgeschakeld, gaat u terug naar de bookbuyer of bookthief-pod om de gebruikersinterface in de browser te bekijken en te zien of de boeken die zijn gekocht of boeken gestolen niet langer oplopend zijn. Zorg ervoor dat u de browser vernieuwt. Als het verhogen is gestopt, is het beleid correct toegepast. U hebt het boekendief gestopt van het stelen van boeken, maar noch de boekopser kan boeken kopen bij de boekwinkel, noch de boekwinkel kan boeken uit het bookwarehouse ophalen. Vervolgens implementeren we [SMI-beleid](https://smi-spec.io/) om alleen de services toe te staan in de mesh waarmee u wilt communiceren.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>SMI-beleid (Service Mesh Interface) voor toegang tot verkeer toepassen

Nu we alle communicatie in de mesh hebben uitgeschakeld, kunnen we onze bookbuyerservice laten communiceren met onze boekwinkelservice voor het kopen van boeken en onze boekwinkelservice laten communiceren met onze bookwarehouse-service om boeken op tehaalen om te verkopen.

Implementeer de volgende [SMI-beleidsregels.](https://smi-spec.io/)

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

De uitvoer ziet er als volgt uit.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

U kunt nu een port forwarding instellen op de bookbuyer- of bookstore-pods en zien dat de metrische gegevens voor zowel de gekochte boeken als de verkochte boeken weer oplopend zijn. U kunt ook hetzelfde doen voor de bookthief-pod om te controleren of er nog steeds geen boeken kunnen worden gestolen.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Beleid voor het splitsen van verkeer van Service Mesh Interface (SMI) toepassen

Voor onze laatste demonstratie maken we een [SMI-beleid](https://smi-spec.io/) voor het splitsen van verkeer om het gewicht van de communicatie van de ene service naar meerdere services als back-end te configureren. Met de functie voor het splitsen van verkeer kunt u verbindingen met de ene service progressief naar een andere service verplaatsen door het verkeer te wegen op een schaal van 0 tot 100.

In de onderstaande afbeelding wordt een diagram weergegeven van [het SMI](https://smi-spec.io/) Traffic Split-beleid dat moet worden geïmplementeerd. We implementeren een extra boekwinkel versie 2 en splitsen vervolgens het binnenkomende verkeer van de boekbuyer, 25% van het verkeer naar de boekwinkel v1-service en 75% naar de boekwinkel v2-service.

![Diagram van splitsing van OSM-bookbuyerverkeer](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Implementeer de bookstore v2-service.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
EOF
```

De volgende uitvoer wordt weergegeven.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

Implementeer nu het beleid voor het splitsen van verkeer om het verkeer van de bookbuyer te splitsen tussen de twee boekwinkel v1 en v2-service.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

De volgende uitvoer wordt weergegeven.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Stel een poort-forward tunnel in naar de bookbuyer-pod. U ziet nu boeken die worden gekocht in de bookstore v2-service. Als u de toename van aankopen blijft bekijken, ziet u een snellere toename van aankopen via de boekwinkel v2-service.

![OSM bookbuyer books boough UI](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Bestaande geïmplementeerde toepassingen beheren die moeten worden beheerd door de AKS-invoegtoepassingen (Open Service Mesh) Azure Kubernetes Service (AKS)

### <a name="before-you-begin"></a>Voordat u begint

In de stappen die in dit scenario worden beschreven, wordt ervan uitgenomen dat u de OSM AKS-invoegversie eerder hebt ingeschakeld voor uw AKS-cluster. Zo niet, bekijk dan de sectie [Enable Open Service Mesh (OSM) Azure Kubernetes Service (AKS) add-on (AKS) for an existing AKS cluster (Open Service Mesh (OSM) Azure Kubernetes Service)](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) inschakelen voor een bestaand AKS-cluster voordat u doorgaat. Uw AKS-cluster moet bovendien versie Kubernetes en hoger zijn, Kubernetes RBAC hebben ingeschakeld en verbinding hebben gemaakt met het cluster (als u hulp nodig hebt met een van deze items, bekijkt u de AKS-quickstart en hebt u de `1.19+` `kubectl` AKS OSM-invoegversie geïnstalleerd). [](./kubernetes-walkthrough.md)

U moet de volgende resources hebben geïnstalleerd:

- De Azure CLI, versie 2.20.0 of hoger
- De `aks-preview` extensieversie 0.5.5 of hoger
- OSM-versie v0.8.0 of hoger
- apt-get install jq

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Het beleid voor de modus Open Service Mesh (OSM) controleren

De modus OsM-beleid voor verkeer dat permissief is, is een modus waarin het afdwingen van [het SMI-verkeersbeleid](https://smi-spec.io/) wordt omzeild. In deze modus detecteert OSM automatisch services die deel uitmaken van de service-mesh en programma's voor verkeersbeleidsregels op elke Envoy-proxy sidecar om te kunnen communiceren met deze services.

Voer de volgende opdracht uit om de huidige verkeersmodus van OSM voor uw cluster te controleren:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

De uitvoer van de OSM-configuratie moet er als volgt uitzien:

```Output
{
  "egress": "true",
  "enable_debug_server": "true",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Als de **permissive_traffic_policy_mode** is geconfigureerd voor **waar,** kunt u uw naamruimten veilig onboarden zonder onderbreking van uw service-naar-service-communicatie. Als de **permissive_traffic_policy_mode** is geconfigureerd op **false**, moet u ervoor zorgen dat u de juiste SMI-manifesten voor het [toegangsbeleid](https://smi-spec.io/) voor verkeer hebt geïmplementeerd en dat u een serviceaccount hebt dat elke service vertegenwoordigt die in de naamruimte is geïmplementeerd. Volg de richtlijnen voor het onboarden van bestaande geïmplementeerde toepassingen met [Open Service Mesh (OSM) Permissive Traffic Policy](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false) geconfigureerd als False

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Onboarding van bestaande geïmplementeerde toepassingen met Open Service Mesh (OSM) Permissive Traffic Policy geconfigureerd als True

Het eerste wat we gaan doen, is de geïmplementeerde toepassingsnaamruimte(en) toevoegen aan OSM om te beheren.

```azurecli-interactive
osm namespace add bookstore
```

U moet de volgende uitvoer zien:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

Vervolgens kijken we naar de huidige podimplementatie in de naamruimte. Voer de volgende opdracht uit om de pods in de aangewezen naamruimte weer te geven.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Als het goed is, ziet u de volgende vergelijkbare uitvoer:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Let op **de kolom READY** met **1/1,** wat betekent dat de toepassingspod slechts één container heeft. Vervolgens moeten uw toepassingsimplementaties opnieuw worden gestart, zodat OSM de Envoy-sidecar-proxycontainer kan injecteren met uw toepassingspod. Laten we een lijst met implementaties in de naamruimte op halen.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

U moet de volgende uitvoer zien:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

Nu wordt de implementatie opnieuw gestart om de Envoy-sidecar-proxycontainer in uw toepassingspod te injecteren. Voer de volgende opdracht uit.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

U moet de volgende uitvoer zien:

```Output
deployment.apps/bookbuyer restarted
```

Als we de pods in de naamruimte opnieuw bekijken:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

U ziet nu dat in de **kolom READY** nu **2/2** containers worden weergegeven die gereed zijn voor uw pod. De tweede container is de Envoy sidecar-proxy.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

We kunnen de pod verder inspecteren om de Envoy-proxy weer te geven door de opdracht describe uit te voeren om de configuratie weer te geven.

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

Controleer of uw toepassing nog steeds functioneel is na de proxyinjectie van de Envoy-sidecar.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Onboarding van bestaande geïmplementeerde toepassingen met Open Service Mesh (OSM) Permissive Traffic Policy geconfigureerd als False

Wanneer de OSM-configuratie voor het permissieve verkeersbeleid is ingesteld op , vereist OSM expliciet SMI-toegangsbeleid voor verkeer dat is geïmplementeerd om de `false` service-naar-service-communicatie binnen uw cluster uit te voeren. [](https://smi-spec.io/) Momenteel maakt OSM ook gebruik van Kubernetes-serviceaccounts als onderdeel van het autoriseren van service-naar-service-communicatie. Om ervoor te zorgen dat uw bestaande geïmplementeerde toepassingen communiceren wanneer ze worden beheerd door de OSM-mesh, moeten we controleren of er een serviceaccount is dat moet worden gebruikt, de implementatie van de toepassing bijwerken met de serviceaccountgegevens en het toegangsbeleid voor [SMI-verkeer](https://smi-spec.io/) toepassen.

#### <a name="verify-kubernetes-service-accounts"></a>Kubernetes-serviceaccounts verifiëren

Controleer of u een kubernetes-serviceaccount hebt in de naamruimte waarin uw toepassing is geïmplementeerd.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

Hieronder staat een serviceaccount met de naam `bookbuyer` in de naamruimte bookbuyer.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Als u geen ander serviceaccount dan het standaardaccount hebt, moet u er een maken voor uw toepassing. Gebruik de volgende opdracht als voorbeeld om een serviceaccount te maken in de geïmplementeerde naamruimte van de toepassing.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>De huidige implementatiespecificatie van uw toepassing weergeven

Als u een serviceaccount uit de vorige sectie moest maken, is de kans groot dat de implementatie van uw toepassing niet is geconfigureerd met een specifiek `serviceAccountName` in de implementatiespecificatie. We kunnen de implementatiespecificatie van uw toepassing weergeven met de volgende opdrachten:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Er wordt een lijst met implementaties weergegeven in de uitvoer.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

We beschrijven de implementatie nu als een controle om te zien of er een serviceaccount wordt vermeld in de sectie Podsjabloon.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

In deze specifieke implementatie ziet u dat er een serviceaccount is gekoppeld aan de implementatie die wordt vermeld in de sectie Podsjabloon. Deze implementatie maakt gebruik van de bookbuyer van het serviceaccount. Als u de eigenschap **ServiceAccount: niet ziet,** is uw implementatie niet geconfigureerd voor het gebruik van een serviceaccount.

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

Er zijn verschillende technieken om uw implementatie bij te werken om een Kubernetes-serviceaccount toe te voegen. Bekijk de Kubernetes-documentatie over het inline bijwerken [van](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) een implementatie of [Serviceaccounts voor pods configureren.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) Nadat u de implementatiespecificatie hebt bijgewerkt met het serviceaccount, moet u uw implementatie opnieuw implementeren (kubectl apply -f your-deployment.yaml) in het cluster.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Het benodigde SMI-beleid (Service Mesh Interface) implementeren

De laatste stap voor het toestaan van geautoriseerd verkeer in de mesh is het implementeren van het benodigde [SMI-toegangsbeleid](https://smi-spec.io/) voor verkeer voor uw toepassing. De hoeveelheid configuratie die u kunt bereiken met [SMI-beleid](https://smi-spec.io/) voor verkeerstoegang valt buiten het bereik van dit scenario, maar we beschrijven enkele van de algemene onderdelen van de specificatie en laten zien hoe u een eenvoudig TrafficTarget- en HTTPRouteGroup-beleid configureert om service-naar-service-communicatie voor uw toepassing mogelijk te maken.

Met [de SMI](https://smi-spec.io/) [**Traffic Access Control**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) kunnen gebruikers het toegangscontrolebeleid voor hun toepassingen definiëren. We richten ons op de **Resources van de TrafficTarget-** **en HTTPRoutGroup-API.**

De TrafficTarget-resource bestaat uit drie hoofdconfiguratie-instellingen doel, regels en bronnen. Hieronder wordt een voorbeeld van TrafficTarget weergegeven.

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

In de bovenstaande TrafficTarget-specificatie geeft de het serviceaccount aan `destination` dat is geconfigureerd voor de doelbronservice. Vergeet niet dat het serviceaccount dat eerder aan de implementatie is toegevoegd, wordt gebruikt om toegang te verlenen tot de implementatie waar deze aan is gekoppeld. De `rules` sectie , in dit specifieke voorbeeld, definieert het type HTTP-verkeer dat via de verbinding is toegestaan. U kunt fijn grain regex patronen voor de HTTP-headers configureren om specifiek te zijn voor welk verkeer is toegestaan via HTTP. De `sources` sectie is de communicatie die afkomstig is van de service. In deze specificatie wordt gelezen dat bookbuyer moet communiceren met de boekwinkel.

De HTTPRouteGroup-resource bestaat uit een of een matrix van overeenkomsten met HTTP-headerinformatie en is een vereiste voor de TrafficTarget-specificatie. In het onderstaande voorbeeld ziet u dat de HTTPRouteGroup drie HTTP-acties autoriseert, twee GET en één POST.

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

Als u niet bekend bent met het type HTTP-verkeer dat uw front-endtoepassing maakt naar andere lagen van de toepassing, kunt u, omdat voor de TrafficTarget-specificatie een regel is vereist, het equivalent van een regel voor alles toestaan maken met behulp van de onderstaande specificatie voor HTTPRouteGroup.

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

Zodra u de specificaties voor TrafficTarget en HTTPRouteGroup hebt geconfigureerd, kunt u ze samenbrengen als één YAML en implementeren. Hieronder vindt u de configuratie van het boekwinkelvoorbeeld.

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

Ga naar [de SMI-site](https://smi-spec.io/) voor meer gedetailleerde informatie over de specificatie.

### <a name="manage-the-applications-namespace-with-osm"></a>De naamruimte van de toepassing beheren met OSM

Vervolgens configureert u OSM om de naamruimte te beheren en start u de implementaties opnieuw op om de Envoy sidecar-proxy in de toepassing te krijgen.

Voer de volgende opdracht uit om de naamruimte `azure-vote` te configureren voor het beheer van mijn OSM.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Start vervolgens de `azure-vote-front` implementaties en `azure-vote-back` opnieuw met de volgende opdrachten.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Als we de pods voor de naamruimte bekijken, zien we de READY-fase van zowel de als als 2/2, wat betekent dat de `azure-vote`  `azure-vote-front` Envoy sidecar-proxy naast de toepassing is `azure-vote-back` geïnjecteerd.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Zelfstudie: Een toepassing implementeren die wordt beheerd door Open Service Mesh (OSM) met NGINX-ingress

Open Service Mesh (OSM) is een lichtgewicht, extensible Cloud Native-service-mesh waarmee gebruikers op uniforme wijze waarneembaarheidsfuncties voor zeer dynamische microserviceomgevingen kunnen beheren, beveiligen en out-of-the-box waarneembaarheid kunnen krijgen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - De huidige OSM-clusterconfiguratie weergeven
> - De naamruimten voor OSM maken voor het beheren van geïmplementeerde toepassingen in de naamruimten
> - Onboarding van de naamruimten die moeten worden beheerd door OSM
> - De voorbeeldtoepassing implementeren
> - Controleer of de toepassing wordt uitgevoerd in het AKS-cluster
> - Een NGINX-controller voor ingress maken die wordt gebruikt voor de toepassing
> - Een service beschikbaar maken via Azure Application Gateway toegang tot internet

### <a name="before-you-begin"></a>Voordat u begint

Bij de stappen in dit artikel wordt ervan uitgenomen dat u een AKS-cluster hebt gemaakt (Kubernetes en hoger, met Kubernetes RBAC ingeschakeld), verbinding hebt gemaakt met het cluster (als u hulp nodig hebt met een van deze items, bekijkt u de AKS-quickstart en hebt u de `1.19+` `kubectl` AKS OSM-invoegversie geïnstalleerd. [](./kubernetes-walkthrough.md)

U moet de volgende resources hebben geïnstalleerd:

- De Azure CLI, versie 2.20.0 of hoger
- De `aks-preview` extensie versie 0.5.5 of hoger
- OSM-versie v0.8.0 of hoger
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>De huidige OSM-clusterconfiguratie weergeven en controleren

Zodra de OSM-invoegsel voor AKS is ingeschakeld op het AKS-cluster, kunt u de huidige configuratieparameters bekijken in de Osm-config Kubernetes ConfigMap. Voer de volgende opdracht uit om de ConfigMap-eigenschappen weer te geven:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Uitvoer toont de huidige OSM-configuratie voor het cluster.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

U ziet **dat permissive_traffic_policy_mode** is geconfigureerd als **true.** De modus voor het afdwingen van verkeersbeleid in OSM is een modus waarin het afdwingen van [SMI-verkeersbeleid](https://smi-spec.io/) wordt omzeild. In deze modus detecteert OSM automatisch services die deel uitmaken van de service-mesh en programma's voor verkeersbeleidsregels op elke Envoy-proxy-sidecar om te kunnen communiceren met deze services.

### <a name="create-namespaces-for-the-application"></a>Naamruimten voor de toepassing maken

In deze zelfstudie gebruiken we de OSM-boekwinkeltoepassing met de volgende toepassingsonderdelen:

- bookbuyer
- bookthief
- Boekhandel
- bookwarehouse

Maak naamruimten voor elk van deze toepassingsonderdelen.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

U moet de volgende uitvoer zien:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Onboarding van de naamruimten die moeten worden beheerd door OSM

Als u de naamruimten toevoegt aan de OSM-mesh, kan de OSM-controller automatisch de Envoy sidecar-proxycontainers met uw toepassing injecteren. Voer de volgende opdracht uit om de naamruimten van de OSM-boekwinkeltoepassing te onboarden.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

U moet de volgende uitvoer zien:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>De Bookstore-toepassing implementeren in het AKS-cluster

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Alle implementatie-uitvoer wordt hieronder samengevat.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>De Bookbuyer-service bijwerken

Werk de bookbuyer-service bij naar de juiste configuratie voor de binnenkomende poort met het volgende servicemanifest.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Controleer of de bookstore-toepassing wordt uitgevoerd in het AKS-cluster

Vanaf nu hebben we de toepassing bookstore mulit-container geïmplementeerd, maar deze is alleen toegankelijk vanuit het AKS-cluster. Later voegen we de controller voor Azure Application Gateway-ingress toe om de toepassing buiten het AKS-cluster weer te geven. Om te controleren of de toepassing in het cluster wordt uitgevoerd, gebruiken we een poort vooruit om de gebruikersinterface van het bookbuyer-onderdeel weer te bieden.

Eerst halen we de naam van de bookbuyer-pod op

```azurecli-interactive
kubectl get pod -n bookbuyer
```

De uitvoer ziet er als volgt uit. Aan uw bookbuyer-pod wordt een unieke naam toegevoegd.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Zodra we de naam van de pod hebben, kunnen we de opdracht port-forward gebruiken om een tunnel van ons lokale systeem naar de toepassing in het AKS-cluster in te stellen. Voer de volgende opdracht uit om port forward in te stellen voor poort 8080 van het lokale systeem. Gebruik opnieuw de naam van de opgegeven bookbuyer-pod.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

De uitvoer ziet er ongeveer als het goed is uit.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Terwijl de port forwarding is, navigeert u vanuit een browser naar de volgende `http://localhost:8080` URL. U ziet nu de gebruikersinterface van de bookbuyer-toepassing in de browser, vergelijkbaar met de onderstaande afbeelding.

![Afbeelding van OSM bookbuyer-app voor NGINX-gebruikersinterface](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Een NGINX-controller voor ingress maken in Azure Kubernetes Service (AKS)

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

We gebruiken de toegangscontroller om de toepassing die wordt beheerd door OSM, weer te geven op internet. Als u de controller voor ingress wilt maken, gebruikt u Helm om nginx-ingress te installeren. Voor toegevoegde redundantie worden er twee replica's van de NGINX-ingangscontrollers geïmplementeerd met de parameter `--set controller.replicaCount`. Als u volledig wilt profiteren van het uitvoeren van replica's van de controller voor ingress, moet u ervoor zorgen dat uw AKS-cluster meer dan één knooppunt heeft.

De ingangscontroller moet ook worden gepland op een Linux-knooppunt. Windows Server-knooppunten mogen de ingangscontroller niet uitvoeren. Er wordt een knooppuntselector opgegeven met behulp van de parameter `--set nodeSelector` om de Kubernetes-planner te laten weten dat de NGINX-ingangscontroller moet worden uitgevoerd op een Linux-knooppunt.

> [!TIP]
> In het volgende voorbeeld wordt een Kubernetes-naamruimte gemaakt voor de ingress-resources met de _naam ingress-basic._ Geef waar nodig een naamruimte op voor uw eigen omgeving.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Wanneer de Kubernetes load balancer-service wordt gemaakt voor de NGINX-controller voor ingress, wordt een dynamisch openbaar IP-adres toegewezen, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Er zijn nog geen regels voor ingressen gemaakt. Daarom wordt de standaardpagina 404 van de NGINX-controller voor het ingress weergegeven als u naar het interne IP-adres bladert. In de volgende stappen worden regels voor ingress geconfigureerd.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>De bookbuyer-service op internet maken

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

U moet de volgende uitvoer zien:

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

### <a name="view-the-nginx-logs"></a>De NGINX-logboeken weergeven

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

Uitvoer toont de status van de NGINX-controller voor het binnenvoeren wanneer de regel voor binnenvoer is toegepast:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>De NGINX-services en bookbuyer-service extern weergeven

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Omdat de hostnaam in het toegangsmanifest een psuedo-naam is die wordt gebruikt voor het testen, is de DNS-naam niet beschikbaar op internet. We kunnen ook het curl-programma gebruiken en de hostnaamheader aan het openbare IP-adres van NGINX laten liggen en een 200-code ontvangen die ons verbindt met de bookbuyer-service.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

U moet de volgende uitvoer zien:

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Zelfstudie: Een toepassing implementeren die wordt beheerd door Open Service Mesh (OSM) met Azure Application Gateway AKS-invoegtoepassing

Open Service Mesh (OSM) is een lichtgewicht, extensible Cloud Native-service-mesh waarmee gebruikers op uniforme wijze waarneembaarheidsfuncties kunnen beheren, beveiligen en out-of-the-box waarneembaarheidsfuncties kunnen krijgen voor zeer dynamische microserviceomgevingen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - De huidige OSM-clusterconfiguratie weergeven
> - Maak de naamruimte(s) voor OSM om geïmplementeerde toepassingen in de naamruimten te beheren
> - Onboarding van de naamruimten die door OSM moeten worden beheerd
> - De voorbeeldtoepassing implementeren
> - Controleer of de toepassing wordt uitgevoerd in het AKS-cluster
> - Maak een Azure Application Gateway moet worden gebruikt als de controller voor ingress voor de toepassing
> - Een service beschikbaar maken via Azure Application Gateway toegang tot internet

### <a name="before-you-begin"></a>Voordat u begint

In de stappen die in dit artikel worden beschreven, wordt ervan uitgegaan dat u een AKS-cluster hebt gemaakt (Kubernetes en hoger, met Kubernetes RBAC ingeschakeld), verbinding hebt gemaakt met het cluster (als u hulp nodig hebt met een van deze items, bekijkt u de `1.19+` `kubectl` [AKS-quickstart](./kubernetes-walkthrough.md), hebt u de OSM-invoegversie van AKS geïnstalleerd en maakt u een nieuwe Azure Application Gateway voor toegangsingressie.

U moet de volgende resources hebben geïnstalleerd:

- De Azure CLI, versie 2.20.0 of hoger
- De `aks-preview` extensieversie 0.5.5 of hoger
- AKS-clusterversie 1.19+ met Azure CNI-netwerken (gekoppeld aan een Azure-Vnet)
- OSM-versie v0.8.0 of hoger
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>De huidige OSM-clusterconfiguratie weergeven en controleren

Zodra de OSM-invoegsel voor AKS is ingeschakeld op het AKS-cluster, kunt u de huidige configuratieparameters bekijken in de Osm-config Kubernetes ConfigMap. Voer de volgende opdracht uit om de ConfigMap-eigenschappen weer te geven:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Uitvoer toont de huidige OSM-configuratie voor het cluster.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

U ziet **dat permissive_traffic_policy_mode** is geconfigureerd als **waar.** De modus voor het afdwingen van verkeersbeleid in OSM is een modus waarin het afdwingen van [het SMI-verkeersbeleid](https://smi-spec.io/) wordt omzeild. In deze modus detecteert OSM automatisch services die deel uitmaken van de service-mesh en programma's voor verkeersbeleidsregels op elke Envoy-proxy sidecar om te kunnen communiceren met deze services.

### <a name="create-namespaces-for-the-application"></a>Naamruimten voor de toepassing maken

In deze zelfstudie gebruiken we de OSM-boekwinkeltoepassing met de volgende toepassingsonderdelen:

- bookbuyer
- bookthief
- Boekhandel
- bookwarehouse

Maak naamruimten voor elk van deze toepassingsonderdelen.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

U moet de volgende uitvoer zien:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Onboarding van de naamruimten die door OSM moeten worden beheerd

Wanneer u de naamruimten toevoegt aan de OSM-mesh, kan de OSM-controller automatisch de Envoy sidecar-proxycontainers met uw toepassing injecteren. Voer de volgende opdracht uit om de naamruimten van de OSM-bookstore-toepassing te onboarden.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

U moet de volgende uitvoer zien:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>De Bookstore-toepassing implementeren in het AKS-cluster

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Alle implementatie-uitvoer wordt hieronder samengevat.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>De Bookbuyer-service bijwerken

Werk de bookbuyer-service bij naar de juiste configuratie voor de binnenkomende poort met het volgende servicemanifest.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Controleer of de Bookstore-toepassing wordt uitgevoerd in het AKS-cluster

Vanaf nu hebben we de toepassing voor meerdere containers in de boekwinkel geïmplementeerd, maar deze is alleen toegankelijk vanuit het AKS-cluster. Later voegen we de controller Azure Application Gateway om de toepassing buiten het AKS-cluster weer te geven. Om te controleren of de toepassing in het cluster wordt uitgevoerd, gebruiken we een poort vooruit om de gebruikersinterface van het bookbuyer-onderdeel weer te bieden.

Eerst halen we de naam van de bookbuyer-pod op

```azurecli-interactive
kubectl get pod -n bookbuyer
```

De uitvoer ziet er als volgt uit. Aan uw bookbuyer-pod wordt een unieke naam toegevoegd.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Zodra we de naam van de pod hebben, kunnen we de opdracht port-forward gebruiken om een tunnel van ons lokale systeem naar de toepassing in het AKS-cluster in te stellen. Voer de volgende opdracht uit om port forward in te stellen voor de lokale systeempoort 8080. Gebruik opnieuw de naam van uw specifieke bookbuyer-pod.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

De uitvoer ziet er ongeveer als het goed is uit.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Terwijl de port forwarding is, navigeert u vanuit een browser naar de volgende `http://localhost:8080` URL. U ziet nu de gebruikersinterface van de bookbuyer-toepassing in de browser, vergelijkbaar met de onderstaande afbeelding.

![OSM bookbuyer-app voor App Gateway UI-afbeelding](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>Maak een Azure Application Gateway de bookbuyer-toepassing buiten het AKS-cluster weer te geven

> [!NOTE]
> Met de volgende aanwijzingen maakt u een nieuw exemplaar van de Azure Application Gateway moet worden gebruikt voor ingress. Als u een bestaand Azure Application Gateway u wilt gebruiken, gaat u naar de sectie voor het inschakelen van Application Gateway-invoeggebruikscontroller.

#### <a name="deploy-a-new-application-gateway"></a>Een nieuwe Azure Application Gateway implementeren

> [!NOTE]
> We verwijzen naar bestaande documentatie voor het inschakelen van Application Gateway invoeg-invoegversie van de invoegversie van de toegangscontroller voor een bestaand AKS-cluster. Er zijn enkele wijzigingen aangebracht om het OSM-materiaal aan te passen. Meer gedetailleerde documentatie over dit onderwerp vindt u [hier.](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing)

U implementeert nu een nieuwe Application Gateway om te simuleren dat er een bestaande Application Gateway is die u wilt gebruiken om het verkeer naar uw AKS-cluster te verdelen, _myCluster_. De naam van de Application Gateway wordt _myApplicationGateway_, maar u moet eerst een openbare IP-resource maken, _myPublicIp_, en een nieuw virtueel netwerk met de naam _myVnet_ en adresruimte 11.0.0.0/8, alsmede een subnetmet adresruimte 11.1.0.0/16 en de naam _mySubnet_, en uw Application Gateway implementeren in _mySubnet_ met behulp van _myPublicIp_.

Wanneer u een AKS-cluster en Application Gateway in afzonderlijke virtuele netwerken gebruikt, mogen de adresruimten van de twee virtuele netwerken elkaar niet overlappen. De standaardadresruimte waarin een AKS-cluster wordt geïmplementeerd is 10.0.0.0/8; daarom stellen we het adresvoorvoegsel van het virtuele Application Gateway-netwerk in op 11.0.0.0/8.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> De invoegtoepassing Application Gateway Ingress Controller (AGIC) ondersteunt **alleen** Application Gateway v2-SKU's (Standard en WAF) en **niet** de Application Gateway v1-SKU's.

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>De AGIC-invoegversie inschakelen voor een bestaand AKS-cluster via Azure CLI

Als u Azure CLI wilt blijven gebruiken, kunt u de AGIC-invoeggebruiker blijven inschakelen in het AKS-cluster dat u hebt gemaakt, _myCluster,_ en de AGIC-invoeggebruiker opgeven om de bestaande Application Gateway te gebruiken die u hebt gemaakt, _myApplicationGateway_.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

U kunt controleren of Azure Application Gateway AKS-invoeg-on is ingeschakeld met de volgende opdracht.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

Met deze opdracht wordt de uitvoer `true` als .

#### <a name="peer-the-two-virtual-networks-together"></a>Koppel de twee virtuele netwerken als peers

Omdat we het AKS-cluster in een eigen virtueel netwerk hebben geïmplementeerd en de Application Gateway in een ander virtueel netwerk, moet u de twee virtuele netwerken met elkaar verbinden als peers, zodat het verkeer van de Application Gateway naar de pods in het cluster kan stromen. Voor de peering van de twee virtuele netwerken moet de Azure CLI-opdracht twee keer worden uitgevoerd, om ervoor te zorgen dat de verbinding bi-directioneel is. Met de eerste opdracht maakt u een peering-verbinding van het virtuele Application Gateway netwerk naar het virtuele AKS-netwerk; met de tweede opdracht wordt een peering-verbinding in de andere richting gemaakt.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>De bookbuyer-service op internet blootstellen

Pas het volgende toegangsmanifest toe op het AKS-cluster om de bookbuyer-service beschikbaar te maken op internet via de Azure Application Gateway.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

U moet de volgende uitvoer zien

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

Omdat de hostnaam in het toegangsmanifest een pseudonaam is die wordt gebruikt voor het testen, is de DNS-naam niet beschikbaar op internet. U kunt ook het curl-programma gebruiken en de hostnaamheader naar het openbare IP-adres van de Azure Application Gateway laten gaan en een 200-code ontvangen om verbinding te maken met de bookbuyer-service.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

U moet de volgende uitvoer zien

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

### <a name="troubleshooting"></a>Problemen oplossen

- [Documentatie voor het oplossen van problemen met AGIC](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [Aanvullende hulpprogramma's voor probleemoplossing zijn beschikbaar in de GitHub-opslagplaats van AGIC](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Bewaking en waarneembaarheid van Service Mesh (OSM) openen met Azure Monitor en Applications Insights

Zowel Azure Monitor als Azure-toepassing Insights helpt u de beschikbaarheid en prestaties van uw toepassingen en services te maximaliseren door een uitgebreide oplossing te bieden voor het verzamelen, analyseren en gebruiken van telemetrie vanuit uw cloud- en on-premises omgevingen.

De OSM AKS-invoegservice heeft diepe integraties in beide Azure-services en biedt een lijktloze Azure-ervaring voor het weergeven en reageren op kritieke KPI's die worden geleverd door metrische OSM-gegevens. Ga voor meer informatie over het inschakelen en configureren van deze services voor de OSM AKS-invoeg-on naar de pagina Azure Monitor voor [OSM](https://aka.ms/azmon/osmpreview) voor meer informatie.

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Zelfstudie: Prometheus, Grafana en Jaeger handmatig implementeren om metrische gegevens van Open Service Mesh (OSM) weer te geven voor waarneembaarheid

> [!WARNING]
> De installatie van Prometheus, Grafana en Jaeger wordt aangeboden als algemene richtlijnen om te laten zien hoe deze hulpprogramma's kunnen worden gebruikt om metrische OSM-gegevens weer te geven. De installatie-richtlijnen mogen niet worden gebruikt voor een productie-installatie. Raadpleeg de documentatie van elk hulpprogramma over hoe u de installaties het beste aan uw behoeften kunt voldoen. Het belangrijkste is het ontbreken van permanente opslag, wat betekent dat alle gegevens verloren gaan zodra een Prometheus Grafana en/of Jaeger-pod(s) zijn beëindigd.

Open Service Mesh (OSM) genereert gedetailleerde metrische gegevens met betrekking tot al het verkeer binnen de mesh. Deze metrische gegevens bieden inzicht in het gedrag van toepassingen in de mesh, zodat gebruikers problemen met hun toepassingen kunnen oplossen, onderhouden en analyseren.

Vanaf vandaag verzamelt OSM metrische gegevens rechtstreeks vanuit de sidecar-proxies (Envoy). OSM biedt uitgebreide metrische gegevens voor binnenkomend en uitgaand verkeer voor alle services in de mesh. Met deze metrische gegevens kan de gebruiker informatie krijgen over de totale hoeveelheid verkeer, fouten binnen het verkeer en de reactietijd voor aanvragen.

OSM gebruikt Prometheus voor het verzamelen en opslaan van consistente metrische gegevens en statistieken voor verkeer voor alle toepassingen die in de mesh worden uitgevoerd. Prometheus is een opensource toolkit voor bewaking en waarschuwingen, die vaak wordt gebruikt in (maar niet beperkt tot) Kubernetes- en Service Mesh-omgevingen.

Elke toepassing die deel uitmaakt van de mesh wordt uitgevoerd in een Pod die een Envoy-sidecar bevat die metrische gegevens (proxygegevens) beschikbaar maakt in de Prometheus-indeling. Bovendien heeft elke Pod die deel uitmaakt van de mesh Prometheus-aantekeningen, waardoor de Prometheus-server de toepassing dynamisch kan scrapen. Met dit mechanisme wordt automatisch scraping van metrische gegevens mogelijk wanneer een nieuwe naamruimte/pod/service aan de mesh wordt toegevoegd.

Metrische OSM-gegevens kunnen worden bekeken met Grafana. Dit is een opensource-visualisatie- en analysesoftware. Hiermee kunt u metrische gegevens opvragen, visualiseren, waarschuwen en verkennen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Een Prometheus-exemplaar maken en implementeren
> - OSM configureren om Prometheus-scraping toe te staan
> - De Prometheus-configuratiemap bijwerken
> - Een Grafana-exemplaar maken en implementeren
> - Grafana configureren met de Prometheus-gegevensbron
> - OSM-dashboard importeren voor Grafana
> - Een Jaeger-exemplaar maken en implementeren
> - Jaeger-tracering configureren voor OSM

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Een Prometheus-exemplaar voor OSM implementeren en configureren

We gebruiken Helm om het Prometheus-exemplaar te implementeren. Voer de volgende opdrachten uit om Prometheus via Helm te installeren:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Als de installatie is geslaagd, ziet u hieronder vergelijkbare uitvoer. Noteer de Prometheus-serverpoort en de DNS-naam van het cluster. Deze informatie wordt later gebruikt voor om Prometheus te configureren als een gegevensbron voor Grafana.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>OSM configureren om Prometheus-scraping toe te staan

Om ervoor te zorgen dat de OSM-onderdelen zijn geconfigureerd voor Prometheus-scrapes, moeten we de **prometheus_scraping-configuratie** in het configuratiebestand osm-config controleren. Bekijk de configuratie met de volgende opdracht:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

De uitvoer van de vorige opdracht moet retourneren `true` als OSM is geconfigureerd voor Prometheus-scraping. Als de geretourneerde waarde `false` is, moeten we de configuratie bijwerken naar `true` . Voer de volgende opdracht uit om **OSM** Prometheus-scraping in te zetten:

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

De volgende uitvoer wordt weergegeven.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>De Prometheus Configmap bijwerken

De standaardinstallatie van Prometheus bevat twee Kubernetes-configuratiemappen. U kunt de lijst met Prometheus-configuratiekaarten weergeven met de volgende opdracht.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

We moeten de prometheus.yml-configuratie in de **stable-prometheus-server** configmap vervangen door de volgende OSM-configuratie. Er zijn verschillende technieken voor bestandsbewerking om deze taak uit te voeren. Een eenvoudige en veilige manier is om de configmap te exporteren, er een kopie van te maken voor de back-up en deze vervolgens te bewerken met een editor zoals Visual Studio code.

> [!NOTE]
> Als u nog geen code Visual Studio, kunt u deze hier downloaden en [installeren.](https://code.visualstudio.com/Download)

We gaan eerst de **stable-prometheus-server** configmap exporteren en vervolgens een kopie maken voor de back-up.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Vervolgens openen we het bestand met behulp van Visual Studio Code om te bewerken.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Nadat u de configmap hebt geopend in de Visual Studio Code-editor, vervangt u het bestand prometheus.yml door de onderstaande OSM-configuratie en sla u het bestand op.

> [!WARNING]
> Het is zeer belangrijk dat u ervoor zorgt dat u de inspringingsstructuur van het YAML-bestand be behouden. Eventuele wijzigingen in de yaml-bestandsstructuur kunnen ertoe leiden dat de configmap niet opnieuw kan worden toegepast.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

Pas het bijgewerkte yaml-bestand configmap toe met de volgende opdracht.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Mogelijk ontvangt u een bericht over een ontbrekende kubernetes-aantekening die nodig is. Dit kan voor nu worden genegeerd.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Controleer of Prometheus is geconfigureerd voor het scrapen van de OSM-mesh en API-eindpunten

Om te controleren of Prometheus juist is geconfigureerd voor het scrapen van de OSM-mesh en API-eindpunten, sturen we door naar de Prometheus-pod en bekijken we de doelconfiguratie. Voer de volgende opdrachten uit.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Open een browser tot `http://localhost:9090/targets`

Als u omlaag schuift, ziet u dat alle SMI-eindpunten de status **UP** hebben, evenals andere OSM-metrische gegevens die zijn gedefinieerd zoals hieronder afgebeeld.

![Afbeelding van de gebruikersinterface voor metrische gegevens van OSM Prometheus Target](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Een Grafana-exemplaar voor OSM implementeren en configureren

We gebruiken Helm om het Grafana-exemplaar te implementeren. Voer de volgende opdrachten uit om Grafana via Helm te installeren:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

Vervolgens halen we het grafana-standaardwachtwoord op om ons aan te melden bij de Grafana-site.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Noteer het Grafana-wachtwoord.

Vervolgens halen we de Grafana-pod op naar de poort naar het Grafana-dashboard om u aan te melden.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Open een browser tot `http://localhost:3000`

Voer in het onderstaande aanmeldingsscherm admin **in** als de gebruikersnaam en gebruik het Grafana-wachtwoord dat u eerder hebt vastgelegd.

![AFBEELDING VAN OSM Grafana Login Page UI](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>De Grafana Prometheus-gegevensbron configureren

Nadat u zich hebt aangemeld bij Grafana, bestaat de volgende stap uit het toevoegen van Prometheus als gegevensbronnen voor Grafana. Als u dit wilt doen, gaat u naar het configuratiepictogram in het menu links en selecteert u Gegevensbronnen zoals hieronder wordt weergegeven.

![Afbeelding van de gebruikersinterface van de pagina OSM Grafana Datasources](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Klik op **de knop Gegevensbron toevoegen** en selecteer Prometheus onder tijdreeksdatabases.

![AFBEELDING VAN DE SELECTIEPAGINA VAN OSM Grafana Datasources](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

Voer op **de onderstaande pagina Uw Prometheus-gegevensbron** configureren de FQDN van het Kubernetes-cluster in voor de Prometheus-service voor de INSTELLING HTTP-URL. De standaard-FQDN moet `stable-prometheus-server.default.svc.cluster.local` zijn. Nadat u het Prometheus-service-eindpunt hebt ingevoerd, schuift u naar de onderkant van de pagina en **selecteert u Opslaan & Testen.** U ontvangt een groen selectievakje dat aangeeft dat de gegevensbron werkt.

#### <a name="importing-osm-dashboards"></a>OSM-dashboards importeren

OSM-dashboards zijn beschikbaar via:

- [Onze opslagplaats](https://github.com/grafana/grafana)en kunnen als json-blobs worden geïmporteerd via de webbeheerdersportal
- of [online op Grafana.com](https://grafana.com/grafana/dashboards/14145)

Als u een dashboard wilt importeren, gaat u naar het `+` -teken in het menu links en selecteert u `import` .
U kunt het dashboard rechtstreeks importeren op hun id in `Grafana.com` . Ons dashboard gebruikt bijvoorbeeld id , u kunt de id rechtstreeks op het formulier gebruiken `OSM Mesh Details` `14145` en `import` selecteren:

![Afbeelding van de gebruikersinterface voor de importpagina van het OSM Grafana-dashboard](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

Zodra u Importeren selecteert, wordt u automatisch naar uw geïmporteerde dashboard geïmporteerd.

![Afbeelding van de gebruikersinterface van de pagina Met details van OSM Grafana Dashboard Mesh](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Een Jaeger-operator implementeren en configureren in Kubernetes voor OSM

[Jaeger](https://www.jaegertracing.io/) is een opensource-traceringssysteem dat wordt gebruikt voor het bewaken en oplossen van problemen met gedistribueerde systemen. Het kan worden geïmplementeerd met OSM als een nieuw exemplaar of u kunt uw eigen exemplaar meenemen. Met de volgende instructies wordt een nieuwe instantie van Jaeger geïmplementeerd in `jaeger` de naamruimte op het AKS-cluster.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>Jaeger implementeren in het AKS-cluster

Pas het volgende manifest toe om Jaeger te installeren:

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

#### <a name="enable-tracing-for-the-osm-add-on"></a>Tracering inschakelen voor de OSM-invoeg-on

Vervolgens moeten we tracering inschakelen voor de OSM-invoeg-on.

> [!NOTE]
> Vanaf nu zijn de traceringseigenschappen op dit moment niet zichtbaar in de osm-config configmap. Dit wordt zichtbaar gemaakt in een nieuwe release van de OSM AKS-invoegversie.

Voer de volgende opdracht uit om tracering in te stellen voor de OSM-invoeg-on:

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>De Gebruikersinterface van Jaeger weergeven met port forwarding

De gebruikersinterface van Jaeger wordt uitgevoerd op poort 16686. Als u de webgebruikersinterface wilt weergeven, kunt u kubectl port-forward gebruiken:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

In de browser ziet u een vervolgkeuzevenster Service, waarmee u kunt kiezen uit de verschillende toepassingen die door de bookstore-demo zijn geïmplementeerd. Selecteer een service om alle omspannen ervan weer te bieden. Als u bijvoorbeeld bookbuyer selecteert met een lookback van één uur, ziet u de interacties met bookstore-v1 en bookstore-v2 gesorteerd op tijd.

![OSM Jaeger Tracing Page UI-afbeelding](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Selecteer een item om het gedetailleerder weer te geven. Selecteer meerdere items om traceringen te vergelijken. U kunt bijvoorbeeld de interacties van de boekopser met de boekwinkel en boekwinkel-v2 op een bepaald moment vergelijken.

U kunt ook het tabblad Systeemarchitectuur selecteren om een grafiek weer te geven van hoe de verschillende toepassingen hebben gecommuniceerd/gecommuniceerd. Dit geeft een idee van hoe het verkeer tussen de toepassingen stroomt.

![Afbeelding van de gebruikersinterface van OSM Jaeger System Architecture](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Open Service Mesh (OSM) AKS-invoeghulpgidsen voor probleemoplossing

Wanneer u de OSM AKS-invoeg-on implementeert, kan er af en toe een probleem zijn. De volgende handleidingen helpen u bij het oplossen van fouten en het oplossen van veelvoorkomende problemen.

### <a name="verifying-and-troubleshooting-osm-components"></a>OSM-onderdelen controleren en problemen oplossen

#### <a name="check-osm-controller-deployment"></a>Implementatie van OSM-controller controleren

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

Een goede OSM-controller zou er als volgende uitzien:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>Controleer de OSM-controllerpod

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Een gezonde OSM-pod ziet er als volgende uit:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Hoewel er op een bepaald moment één controller is uitbesteed, hebben we nog een controller die READY 1/1 is en Wordt uitgevoerd met 0 opnieuw opstarten. Als de kolom READY iets anders is dan 1/1, heeft de service-mesh een verbroken status.
Kolom GEREED met 0/1 geeft aan dat de container van het besturingsvlak vastgelopen is. We moeten logboeken op halen. Zie get OSM Controller Logs from Ondersteuning voor Azure Center (Logboeken voor OSM-controller Ondersteuning voor Azure center) hieronder. Kolom GEREED met een getal dat hoger is dan 1 na de / geeft aan dat er sidecars zijn geïnstalleerd. OSM Controller werkt waarschijnlijk niet met sidecars die eraan zijn gekoppeld.

> [!NOTE]
> Vanaf versie v0.8.2 is de OSM-controller niet in de ha-modus en wordt deze uitgevoerd in een geïmplementeerd met het aantal replica's van 1 - één pod. De pod heeft wel statustests en wordt opnieuw opgestart door de kubelet, indien nodig.

#### <a name="check-osm-controller-service"></a>OSM Controller-service controleren

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Een goede OSM Controller-service ziet er als volgende uit:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> Het CLUSTER-IP-adres zou anders zijn. De serviceNAAM en POORT(s) moeten hetzelfde zijn als in het bovenstaande voorbeeld.

#### <a name="check-osm-controller-endpoints"></a>OsM-controller-eindpunten controleren

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Een goed osm-controller-eindpunt ziet er als volgende uit:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>Controleer de implementatie van de OSM-injector

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Een goede implementatie van de OSM-injector zou er als volgende uitzien:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>OSM Injector Pod controleren

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Een goede OSM Injector-pod ziet er als volgende uit:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>OSM Injector Service controleren

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

Een goede OSM Injector-service ziet er als volgende uit:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>OSM-eindpunten controleren

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Een goed OSM-eindpunt ziet er als volgende uit:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Webhooks valideren en muteren controleren

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Een goede OSM-webhook valideren ziet er als volgende uit:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Een goede OSM-webhook voor muteren zou er als volgende uitzien:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Controleren op de service en de CA-bundel van de validatiewebhook

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Een goed geconfigureerde validatiewebhookconfiguratie ziet er precies zo uit:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Controleren op de service en de CA-bundel van de mutating-webhook

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Een goed geconfigureerde configuratie voor het muteren van webhooks ziet er precies zo uit:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Controleer of de OSM-controller de webhook valideert (of muteren) een CA-bundel heeft gegeven

> [!NOTE]
> Vanaf v0.8.2 is het belangrijk te weten dat AKS RP de validatiewebhook installeert, dat AKS-afstemming ervoor zorgt dat deze bestaat, maar osm-controller is de controller die de CA-bundel vult.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Dit getal geeft het aantal bytes of de grootte van de CA-bundel aan. Als dit leeg is, 0 of een getal onder 1000, zou dit aangeven dat de CA-bundel niet juist is ingericht. Zonder een juiste CA-bundel zou de validatiewebhook een foutmelding krijgen en wordt voorkomen dat de gebruiker wijzigingen kan aanbrengen in de osm-config ConfigMap in de kube-system-naamruimte.

Een voorbeeldfout wanneer de CA-bundel onjuist is:

- Een poging om de osm-config ConfigMap te wijzigen:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Fout:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

U moet eens kijken wanneer **de validatiewebhookconfiguratie** een ongeldig certificaat heeft:

- Optie 1: start osm-controller opnieuw op. Hiermee wordt de OSM-controller opnieuw opgestart. Aan het begin wordt de CA-bundel overschreven van zowel de webhooks muteren als valideren.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- Optie 2: optie 2. Verwijder de validatiewebhook. Als u de validatiewebhook verwijdert, worden de `osm-config` configMap-bestanden niet meer gevalideerd. Elke patch zal worden door onderkend. De AKS-afstemmingsserver zorgt er op een bepaald moment voor dat de validatiewebhook bestaat en wordt opnieuw gemaakt. De OSM-controller moet mogelijk opnieuw worden opgestart om de CA-bundel snel te herschrijven.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- Optie 3 - Verwijderen en patchen: met de volgende opdracht wordt de validatiewebhook verwijderd, zodat we waarden kunnen toevoegen en wordt onmiddellijk geprobeerd een patch toe te passen. Waarschijnlijk heeft de AKS-afstemmer niet voldoende tijd om de validatiewebhook af te stemmen en te herstellen, zodat we een wijziging als laatste redmiddel kunnen toepassen:

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>Controleer de `osm-config` **ConfigMap**

> [!NOTE]
> De OSM-controller vereist niet dat `osm-config` de ConfigMap aanwezig is in de naamruimte kube-system. De controller heeft redelijke standaardwaarden voor de configuratie en kan zonder deze werken.

Controleer of het bestaat:

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

Controleer de inhoud van de osm-config ConfigMap

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config -o json | jq '.data'
```

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.20.233",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

`osm-config` ConfigMap-waarden:

| Sleutel                              | Type   | Toegestane waarden                                          | Standaardwaarde                          | Functie                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| egress                           | booleaans   | de waarde True, false                                             | `"false"`                              | Hiermee schakelt u eengress in de mesh in.                                                                                                                                                                                                             |
| enable_debug_server              | booleaans   | de waarde True, false                                             | `"true"`                               | Hiermee schakelt u een foutopsporingspunt in op de osm-controller-pod om informatie weer te geven met betrekking tot de mesh, zoals proxyverbindingen, certificaten en SMI-beleid.                                                                                    |
| enable_privileged_init_container | booleaans   | de waarde True, false                                             | `"false"`                              | Hiermee schakelt u bevoegde init-containers in voor pods in mesh. Indien onwaar, hebben init-containers alleen NET_ADMIN.                                                                                                                                   |
| envoy_log_level                  | tekenreeks | trace, debug, info, waarschuwing, waarschuwen, fout, kritiek, uit | `"error"`                              | Hiermee stelt u de logboekverlening van de Sidecar van de Envoy-proxy in, alleen van toepassing op nieuw gemaakte pods die lid worden van de mesh. Als u het logboekniveau voor bestaande pods wilt bijwerken, start u de implementatie opnieuw met `kubectl rollout restart` .                            |
| outbound_ip_range_exclusion_list | tekenreeks | door komma's gescheiden lijst met IP-adresbereiken van de vorm a.b.c.d/x | `-`                                    | Globale lijst met IP-adresbereiken die moeten worden uitgesloten van het onderscheppen van uitgaand verkeer door de sidecar-proxy.                                                                                                                                    |
| permissive_traffic_policy_mode   | booleaans   | de waarde True, false                                             | `"false"`                              | Door in te stellen op , wordt de modus allow-all in de mesh inschakelen, dat wil zeggen geen afdwinging `true` van verkeersbeleid in de mesh. Als deze optie is `false` ingesteld op , schakelt u deny-all-verkeerbeleid in mesh in, dat wil zeggen dat een nodig is om services te laten `SMI Traffic Target` communiceren. |
| prometheus_scraping              | booleaans   | de waarde True, false                                             | `"true"`                               | Hiermee schakelt u scraping van metrische prometheus-gegevens in sidecar-proxies in.                                                                                                                                                                                 |
| service_cert_validity_duration   | tekenreeks | 24 uur, 1h30m (duur van elke tijd)                          | `"24h"`                                | Hiermee stelt u de geldigheidsduur van het servicecertificaat in, vertegenwoordigd als een reeks decimale getallen, elk met optionele breuk en een eenheidsachtervoegsel.                                                                                             |
| tracing_enable                   | booleaans   | de waarde True, false                                             | `"false"`                              | Hiermee schakelt u Jaeger-tracering voor de mesh in.                                                                                                                                                                                                    |
| tracing_address                  | tekenreeks | jaeger.mesh-namespace.svc.cluster.local                 | `jaeger.kube-system.svc.cluster.local` | Adres van de Jaeger-implementatie, als tracering is ingeschakeld.                                                                                                                                                                                |
| tracing_endpoint                 | tekenreeks | /api/v2/spans                                           | /api/v2/spans                          | Eindpunt voor het traceren van gegevens, als tracering is ingeschakeld.                                                                                                                                                                                          |
| tracing_port                     | int    | een geheel getal dat niet nul is                              | `"9411"`                               | Poort waarop tracering is ingeschakeld.                                                                                                                                                                                                       |
| use_https_ingress                | booleaans   | de waarde True, false                                             | `"false"`                              | Hiermee schakelt u HTTPS-ingress in de mesh in.                                                                                                                                                                                                      |
| config_resync_interval           | tekenreeks | met minder dan 1 minuut wordt dit uitgeschakeld                            | 0 (uitgeschakeld)                           | Wanneer een waarde van meer dan 1 m (60s) wordt opgegeven, verzendt osm-controller alle beschikbare configuraties naar elke verbonden envoy tijdens het opgegeven interval                                                                                                    |

#### <a name="check-namespaces"></a>Naamruimten controleren

> [!NOTE]
> De kube-system-naamruimte neemt nooit deel aan een service-mesh en wordt nooit gelabeld en/of voorzien van aantekeningen met de onderstaande sleutel/waarden.

We gebruiken de `osm namespace add` opdracht om naamruimten aan een bepaalde service-mesh toe te sluiten.
Wanneer een k8s-naamruimte deel uitmaakt van de mesh (of als deze deel uitmaakt van de mesh), moet het volgende waar zijn:

De aantekeningen weergeven met

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

De volgende aantekening moet aanwezig zijn:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

De labels weergeven met

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

Het volgende label moet aanwezig zijn:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Als een naamruimte niet is voorzien van aantekeningen met of niet is gelabeld met de `"openservicemesh.io/sidecar-injection": "enabled"` `"openservicemesh.io/monitored-by": "osm"` OSM-injector, worden er geen Envoy-sidecars toevoegen.

> Opmerking: Nadat `osm namespace add` is aangeroepen, **worden alleen** nieuwe pods geïnjecteerd met een Envoy-sidecar. Bestaande pods moeten opnieuw worden gestart met `kubectl rollout restart deployment ...`

#### <a name="verify-the-smi-crds"></a>Controleer de SMI-CRD's:

Controleer of het cluster de vereiste CRD's heeft:

```azurecli-interactive
kubectl get crds
```

Het volgende moet op het cluster zijn geïnstalleerd:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Haal de versies van de CRD's op die zijn geïnstalleerd met deze opdracht:

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

Verwachte uitvoer:

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

Voor OSM Controller v0.8.2 zijn de volgende versies vereist:

- traffictargets.access.smi-spec.io - [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io - [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io: niet ondersteund
- trafficsplits.split.smi-spec.io - [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io - [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Als CRD's ontbreken, gebruikt u de volgende opdrachten om deze op het cluster te installeren:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Open Service Mesh (OSM)-invoegversie uitschakelen voor uw AKS-cluster

Voer de volgende opdracht uit om de OSM-invoeging uit te schakelen:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
