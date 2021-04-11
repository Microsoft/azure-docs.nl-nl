---
title: Service-net openen (preview-versie)
description: Open service-net (OSM) in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 0052c8d2f9b85c34d50a3e9d01253ecaf2d02bab
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106710"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Open Service Mesh AKS-invoeg toepassing (preview)

## <a name="overview"></a>Overzicht

[Open service-net (OSM)](https://docs.openservicemesh.io/) is een licht gewicht, uitbreidbaar, Cloud systeem eigen service-mesh waarmee gebruikers eenvormige functies voor de beschik baarheid van de micro service kunnen beheren, beveiligen en vervolledigen.

OSM voert een Envoy op basis van een besturings vlak op Kubernetes, kan worden geconfigureerd met [SMI](https://smi-spec.io/) -api's en kan worden gebruikt door een Envoy proxy te injecteren als een zijspan wagen naast elk exemplaar van uw toepassing. De Envoy-proxy bevat en voert regels uit rond toegangscontrole beleid, implementeert routerings configuratie en legt metrische gegevens vast. Met het besturings vlak worden proxy's voortdurend geconfigureerd om ervoor te zorgen dat beleid en routerings regels up-to-date zijn en zorgt u ervoor dat proxy's in orde zijn.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Mogelijkheden en functies

OSM biedt de volgende mogelijkheden en functies voor het maken van een native Cloud service-net voor uw Azure Kubernetes Service-clusters (AKS):

- Service voor communicatie met service beveiligen door mTLS in te scha kelen

- Eenvoudig toepassingen op het net voorbereiden door automatische injectie van Envoy-proxy in te scha kelen

- Eenvoudige en transparante configuraties voor het verplaatsen van verkeer op implementaties

- Mogelijkheid tot het definiëren en uitvoeren van nauw keurig beleid voor toegangs beheer voor services

- Waarneem bare en inzichten in metrische toepassings gegevens voor fout opsporing en bewakings Services

- Integratie met extern certificaat beheer Services/oplossingen met een pluggable interface

## <a name="scenarios"></a>Scenario's

OSM kan uw AKS-implementaties ondersteunen met de volgende scenario's:

- Versleutelde communicatie bieden tussen service-eind punten die in het cluster zijn geïmplementeerd

- Verkeers autorisatie van HTTP/HTTPS-en TCP-verkeer in het net

- Configuratie van besturings elementen met een gewogen verkeer tussen twee of meer services voor A/B-of Canarische-implementaties

- Verzamelen en weer geven van Kpi's van toepassings verkeer

## <a name="osm-service-quotas-and-limits-preview"></a>Service Quota's en limieten voor OSM (preview-versie)

OSM preview-beperkingen voor service quota en-limieten vindt u op de [pagina quota voor AKS en regionale limieten](https://docs.microsoft.com/azure/aks/quotas-skus-regions).

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
> Probeer OSM niet te installeren vanuit het binaire bestand met `osm install` . Dit leidt tot een installatie van OSM die niet is geïntegreerd als een invoeg toepassing voor AKS.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>De `AKS-OpenServiceMesh` Preview-functie registreren

Als u een AKS-cluster wilt maken dat de open Service-Mesh-invoeg toepassing kan gebruiken, moet u de `AKS-OpenServiceMesh` functie vlag inschakelen voor uw abonnement.

Registreer de `AKS-OpenServiceMesh` functie vlag met behulp van de opdracht [AZ feature REGI ster][az-feature-register] , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Het duurt enkele minuten voordat de status is _geregistreerd_. Controleer de registratie status met behulp van de opdracht [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider _micro soft. container service_ met de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Open Service Mesh (OSM) Azure Kubernetes service (AKS)-invoeg toepassing installeren voor een nieuw AKS-cluster

Voor een nieuw scenario voor het implementeren van een AKS-cluster begint u met een gloed nieuwe implementatie van een AKS-cluster, waardoor de invoeg toepassing OSM bij de bewerking cluster maken wordt ingeschakeld.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. Maak een resourcegroep met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voor beeld wordt een resource groep met de naam _myOsmAksGroup_ gemaakt op de locatie _eastus2_ (regio):

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Een AKS-cluster implementeren waarop de OSM-invoeg toepassing is ingeschakeld

U implementeert nu een nieuw AKS-cluster waarvoor de OSM-invoeg toepassing is ingeschakeld.

> [!NOTE]
> Houd rekening met de volgende AKS-implementatie opdracht om tijdelijke schijven van het besturings systeem te gebruiken. U kunt hier meer informatie vinden over [tijdelijke besturingssysteem schijven voor AKS](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os)

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Toegangs referenties voor AKS-cluster ophalen

Toegangs referenties voor het nieuwe beheerde Kubernetes-cluster ophalen.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Open Service Mesh (OSM) Azure Kubernetes service (AKS)-invoeg toepassing inschakelen voor een bestaande AKS-cluster

Als u een bestaand AKS-cluster scenario gebruikt, schakelt u de OSM-invoeg toepassing in op een bestaand AKS-cluster dat al is geïmplementeerd.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>De invoeg toepassing OSM inschakelen voor een bestaand AKS-cluster

Als u de AKS OSM-invoeg toepassing wilt inschakelen, moet u de opdracht uitvoeren om `az aks enable-addons --addons` de para meter door te geven `open-service-mesh`

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

U ziet de uitvoer zoals hieronder wordt weer gegeven om te bevestigen dat de AKS OSM-invoeg toepassing is geïnstalleerd.

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

## <a name="validate-the-aks-osm-add-on-installation"></a>De installatie van de AKS OSM-invoeg toepassing valideren

Er zijn verschillende opdrachten om uit te voeren om te controleren of alle onderdelen van de AKS OSM-invoeg toepassing zijn ingeschakeld en worden uitgevoerd:

Eerst kunnen we de invoeg toepassings profielen van het cluster opvragen om de ingeschakelde status van de geïnstalleerde invoeg toepassingen te controleren. De volgende opdracht moet ' True ' retour neren.

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Met de volgende `kubectl` opdrachten wordt de status van de OSM-controller gerapporteerd.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>De AKS OSM-invoeg toepassing openen

Op dit moment kunt u de configuratie van de OSM-controller openen en configureren via de configmap. Als u de configuratie-instellingen van de OSM-controller wilt weer geven, moet u de configuratie-instellingen van de OSM-config configmap via controleren `kubectl` .

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

U ziet dat de **permissive_traffic_policy_mode** is ingesteld op **True**. De modus voor toelaatings beleid voor verkeer in OSM is een modus waarin het [SMI](https://smi-spec.io/) traffic-verkeer wordt afgedwongen. In deze modus detecteert OSM automatisch services die deel uitmaken van het service-net en Program ma's verkeers beleids regels op elke Envoy-proxy om te kunnen communiceren met deze services.

> [!WARNING]
> Controleer voordat u doorgaat of de modus voor het beleid voor toestands verkeer is ingesteld op True. als dit niet het geval is, wijzigt u deze in **True** met de onderstaande opdracht

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Een nieuwe toepassing implementeren om te worden beheerd door de OSM-invoeg toepassing (open service mesh) Azure Kubernetes service (AKS)

### <a name="before-you-begin"></a>Voordat u begint

Bij de stappen die in dit scenario worden beschreven, wordt ervan uitgegaan dat u een AKS-cluster hebt gemaakt (Kubernetes `1.19+` en hoger, waarbij KUBERNETES RBAC is ingeschakeld), een `kubectl` verbinding hebben met het cluster (als u hulp nodig hebt bij een van deze items, raadpleegt u de [AKS Quick](./kubernetes-walkthrough.md)start en hebt u de AKS OSM-invoeg toepassing geïnstalleerd.

U moet de volgende resources hebben geïnstalleerd:

- De Azure CLI, versie 2.20.0 of hoger
- De `aks-preview` extensie versie 0.5.5 of hoger
- OSM-versie v 0.8.0 of hoger
- apt-installatie ophalen JQ

### <a name="create-namespaces-for-the-application"></a>Naam ruimten voor de toepassing maken

In dit overzicht wordt gebruikgemaakt van de OSM boek winkel-toepassing met de volgende Kubernetes-Services:

- bookbuyer
- bookthief
- Boek winkel
- bookwarehouse

Maak naam ruimten voor elk van deze toepassings onderdelen.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Onboarding van de naam ruimten die door OSM worden beheerd

Wanneer u de naam ruimten aan het OSM-net toevoegt, kan de OSM-controller de Envoy-zijspan wagen proxy containers automatisch injecteren met uw toepassing. Voer de volgende opdracht uit om de OSM boek winkel-toepassings naam ruimten toe te staan.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>De boek winkel-toepassing implementeren in het AKS-cluster

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

Alle implementatie-uitvoer vindt u hieronder.

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

### <a name="checkpoint-what-got-installed"></a>Controle punt: wat is er geïnstalleerd?

De voor beeld-boek winkel-toepassing is een app met meerdere lagen die bestaat uit vier Services, de bookbuyer, bookthief, boek winkel en bookwarehouse. Zowel de bookbuyer-als de bookthief-service communiceren met de boek winkel-service om boeken van de boek winkel-service op te halen. De boek winkel-service haalt Books uit de bookwarehouse-service op om de bookbuyer en bookthief op te geven. Dit is een eenvoudige toepassing met meerdere lagen die goed werkt om te laten zien hoe een service-net kan worden gebruikt om de communicatie tussen de toepassingen services te beveiligen en te autoriseren. Als we de procedure volgen, zullen we SMI-beleids regels (Service Mesh Interface) in-en uitschakelen om te voor komen dat de services via OSM kunnen communiceren. Hieronder vindt u een architectuur diagram van wat er is geïnstalleerd voor de toepassing boek winkel.

![OSM bookbuyer-app-architectuur](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Controleren of de boek winkel-toepassing in het AKS-cluster wordt uitgevoerd

Vanaf nu hebben we de boek winkel Mulit-container toepassing geïmplementeerd, maar deze is alleen toegankelijk vanuit het AKS-cluster. Latere zelf studies helpt u bij het beschikbaar maken van de toepassing buiten het cluster via een ingangs controller. We gebruiken nu poort door sturen om toegang te krijgen tot de bookbuyer-toepassing in het AKS-cluster om te controleren of de service wordt gekocht vanuit de boek winkel-dienst.

Om te controleren of de toepassing wordt uitgevoerd in het cluster, zullen we een poort gebruiken om de gebruikers interface van de bookbuyer-en bookthief-onderdelen weer te geven.

Eerst gaan we de naam van de bookbuyer pod ophalen

```azurecli-interactive
kubectl get pod -n bookbuyer
```

De uitvoer ziet er als volgt uit. Aan uw bookbuyer-pod wordt een unieke naam toegevoegd.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Zodra u de naam van de pod hebt, kunt u nu de opdracht voor het door sturen van een tunnel van het lokale systeem instellen op de toepassing binnen het AKS-cluster. Voer de volgende opdracht uit om de poort voor de lokale systeem poort 8080 in te stellen. Gebruik opnieuw uw opgegeven bookbuyer pod-naam.

> [!NOTE]
> Voor alle opdrachten voor het door sturen van poorten is het het beste om een extra terminal te gebruiken, zodat u deze procedure kunt blijven door lopen en de tunnel niet te verbreken. Het is ook het beste om de poort door te sturen buiten de Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

De uitvoer ziet er ongeveer als volgt uit.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Terwijl de poort voor het door sturen is ingesteld, navigeert u naar de volgende URL vanuit een browser `http://localhost:8080` . Nu moet u de gebruikers interface van de bookbuyer-toepassing in de browser kunnen zien, vergelijkbaar met de onderstaande afbeelding.

![OSM bookbuyer app UI-afbeelding](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

U ziet ook dat het totale aantal boeken dat is gekocht, nog steeds wordt verhoogd naar de boek winkel v1-service. De boek winkel v2-service is nog niet geïmplementeerd. We implementeren de boek winkel v2-service wanneer we het SMI traffic-verkeer voor gesplitste beleids regels demonstreren.

U kunt ook hetzelfde controleren voor de bookthief-service.

```azurecli-interactive
kubectl get pod -n bookthief
```

De uitvoer ziet er als volgt uit. Aan uw bookthief-pod wordt een unieke naam toegevoegd.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Poort vooruit naar bookthief pod.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Ga vanuit een browser naar de volgende URL `http://localhost:8080` . U ziet dat de bookthief momenteel boeken van de boek winkel-service stelen. Op dit moment wordt een verkeers beleid geïmplementeerd om de bookthief te stoppen.

![OSM bookthief app UI-afbeelding](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>OSM-strikte verkeers modus uitschakelen voor het net

Zoals eerder vermeld bij het weer geven van de configuratie van het OSM-cluster, wordt de OSM-configuratie standaard ingesteld op het inschakelen van het beleid voor de modus voor In deze modus wordt de afdwinging van Traffic-beleid genegeerd en OSM worden automatisch de services gedetecteerd die deel uitmaken van het service-net en Program ma's verkeers beleids regels op elke Envoy-proxy-zijspan om met deze services te kunnen communiceren.

We gaan nu het beleid voor de toelaat bare verkeers modus uitschakelen en OSM vereist een expliciet [SMI](https://smi-spec.io/) -beleid dat is geïmplementeerd op het cluster om communicatie in de mesh van elke service toe te staan. Als u de modus voor ongebruikte verkeer wilt uitschakelen, voert u de volgende opdracht uit om de eigenschap configmap bij te werken en de waarde te wijzigen van `true` in `false` .

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

De uitvoer ziet er als volgt uit. Aan uw bookthief-pod wordt een unieke naam toegevoegd.

```Output
configmap/osm-config patched
```

Als u wilt controleren of de modus voor een doorlopend verkeer is uitgeschakeld, stuurt u de poort terug naar de bookbuyer-of bookthief-pod om de gebruikers interface in de browser weer te geven en te controleren of de door u aangevraagde boeken of boeken niet langer worden verhoogd. Zorg ervoor dat u de browser vernieuwt. Als de verhoging is gestopt, is het beleid op de juiste wijze toegepast. U hebt de bookthief voor het stelen van boeken gestopt, maar de bookbuyer kan niet worden aangeschaft vanuit het boek winkel en de boek winkel kan geen boeken van de bookwarehouse ophalen. Daarna zullen de [SMI](https://smi-spec.io/) -beleids regels worden geïmplementeerd, zodat alleen de services in het net worden toegestaan die u wilt laten communiceren.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Beleids regels voor toegang tot het SMI-verkeer (service net Interface) Toep assen

Nu we alle communicatie in het net hebben uitgeschakeld, kunnen we onze bookbuyer-service laten communiceren met onze boek winkel-service voor aankoop boeken en onze boek winkel-service toestaan om te communiceren met onze bookwarehouse-service om boeken te verkrijgen om te verkopen.

Implementeer de volgende [SMI](https://smi-spec.io/) -beleids regels.

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

U kunt nu een poort forwarding-sessie instellen op de bookbuyer of boek winkel en zien dat de metrische gegevens van de verkochte boeken en boeken worden verhoogd. U kunt ook hetzelfde doen voor de bookthief pod om te controleren of deze nog steeds geen toegang meer heeft tot de boeken.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Gesplitste beleids regels van het SMI-verkeer (service net Interface) Toep assen

Voor onze laatste demonstratie maken we een beleid voor het splitsen van [SMI](https://smi-spec.io/) -verkeer om het gewicht van de communicatie van de ene service naar meerdere services te configureren als een back-end. Met de functie voor het splitsen van verkeer kunt u verbindingen naar een andere service geleidelijk verplaatsen door het verkeer op een schaal van 0 tot en met 100 te wegingen.

De onderstaande afbeelding is een diagram van het door [SMI](https://smi-spec.io/) verkeer gesplitste beleid dat moet worden geïmplementeerd. We implementeren een extra boek winkel versie 2 en splitsen vervolgens het binnenkomende verkeer van de bookbuyer, waarbij 25% van het verkeer wordt gewogen naar de boek winkel v1-service en 75% op de boek winkel v2-service.

![Gesplitste diagram van OSM bookbuyer Traffic](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Implementeer de boek winkel v2-service.

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

Implementeer nu het gesplitste beleid voor verkeer om het bookbuyer-verkeer tussen de twee boek winkel v1 en v2-service te splitsen.

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

Stel een poort forward tunnel in voor de bookbuyer pod en Bekijk nu boeken die worden gekocht bij de boek winkel v2-service. Als u doorgaat met het volgen van de verhoging van de aankopen, moet u rekening houden met een snellere toename van de aankopen tijdens de boek winkel v2-service.

![OSM bookbuyer Books boough-gebruikers interface](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Bestaande geïmplementeerde toepassingen beheren om te worden beheerd door de OSM-invoeg toepassing (Azure Kubernetes service) Open Service Mesh (AKS)

### <a name="before-you-begin"></a>Voordat u begint

Bij de stappen die in dit overzicht worden beschreven, wordt ervan uitgegaan dat u de OSM AKS-invoeg toepassing voor uw AKS-cluster eerder hebt ingeschakeld. Als dat niet het geval is, raadpleegt u de sectie [Enable Open Service Mesh (OSM) Azure Kubernetes service (AKS)-invoeg toepassing voor een bestaande AKS-cluster](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) voordat u verdergaat. Daarnaast moet uw AKS-cluster versie Kubernetes `1.19+` en hoger zijn, moet KUBERNETES RBAC zijn ingeschakeld en een verbinding hebben gemaakt `kubectl` met het cluster (als u hulp nodig hebt bij een van deze items, raadpleegt u de [AKS Quick](./kubernetes-walkthrough.md)start en hebt u de AKS OSM-invoeg toepassing geïnstalleerd.

U moet de volgende resources hebben geïnstalleerd:

- De Azure CLI, versie 2.20.0 of hoger
- De `aks-preview` extensie versie 0.5.5 of hoger
- OSM-versie v 0.8.0 of hoger
- apt-installatie ophalen JQ

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Controleer het beleid voor de strikte verkeers modus van het open service-net (OSM)

De OSM-beleid modus voor toelaat verkeer is een modus waarin het [SMI](https://smi-spec.io/) traffic-verkeer wordt afgedwongen. In deze modus detecteert OSM automatisch services die deel uitmaken van het service-net en Program ma's verkeers beleids regels op elke Envoy-proxy om te kunnen communiceren met deze services.

Voer de volgende opdracht uit om de huidige strikte verkeers modus van OSM voor uw cluster te controleren:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

De uitvoer van de OSM-configmap moet er als volgt uitzien:

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

Als de **permissive_traffic_policy_mode** is geconfigureerd als **waar**, kunt u uw naam ruimten veilig onboarden zonder onderbreking van uw service-to-service-communicatie. Als de **permissive_traffic_policy_mode** is ingesteld op **Onwaar**, moet u ervoor zorgen dat de juiste [SMI](https://smi-spec.io/) Traffic Access Policy-manifestes zijn geïmplementeerd, en moet u ervoor zorgen dat u over een service account beschikt dat elke service die in de naam ruimte is geïmplementeerd. Volg de richt lijnen voor het [Onboarden van bestaande geïmplementeerde toepassingen met een OSM-beleid (open service net) geconfigureerd als onwaar](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false) .

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Onboarding van bestaande geïmplementeerde toepassingen met OSM-beleid (open service net) geconfigureerd als waar

Eerst voegen we de naam ruimte (n) van de geïmplementeerde toepassing toe aan OSM om te beheren.

```azurecli-interactive
osm namespace add bookstore
```

U moet de volgende uitvoer zien:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

Nu gaan we kijken naar de huidige pod-implementatie in de naam ruimte. Voer de volgende opdracht uit om het Peul in de opgegeven naam ruimte weer te geven.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

De volgende vergelijk bare uitvoer wordt weer gegeven:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Let op de kolom **ready** met **1/1**, wat inhoudt dat de toepassing pod slechts één container heeft. Vervolgens moet u de implementaties van uw toepassing opnieuw opstarten zodat OSM de Envoy-invoeg toepassing kan injecteren met uw toepassings pod. We gaan een lijst met implementaties in de naam ruimte ophalen.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

U moet de volgende uitvoer zien:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

Nu worden de implementaties opnieuw gestart om de Envoy-zijspan wagen te injecteren met de pod van uw toepassing. Voer de volgende opdracht uit.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

U moet de volgende uitvoer zien:

```Output
deployment.apps/bookbuyer restarted
```

Als we het helemaal in de naam ruimte eens bekijken:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

U ziet nu dat in de kolom **gereed** nu **2/2** containers voor uw Pod worden weer gegeven. De tweede container is de Envoy-zijspan proxy.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

We kunnen de pod verder controleren om de Envoy-proxy weer te geven door de opdracht beschrijven uit te voeren om de configuratie weer te geven.

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

Controleer of uw toepassing nog steeds functioneel is na de Envoy-injectie van de zijspan-proxy.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Onboarding van bestaande geïmplementeerde toepassingen met OSM-beleid (open service net) geconfigureerd als onwaar

Wanneer de OSM-configuratie voor het beleid voor toenemend verkeer is ingesteld op `false` , is voor OSM een expliciet [SMI](https://smi-spec.io/) Traffic Access-beleid vereist dat is geïmplementeerd voor de service-to-service-communicatie binnen uw cluster. Op dit moment maakt OSM ook gebruik van Kubernetes-service accounts als onderdeel van het autoriseren van service-to-service-communicatie. Om ervoor te zorgen dat uw bestaande geïmplementeerde toepassingen communiceren wanneer ze worden beheerd door de OSM-mesh, moeten we controleren of een service account wordt gebruikt, de implementatie van de toepassing bijwerken met de service accountgegevens, het beleid voor [SMI](https://smi-spec.io/) -verkeer toegang Toep assen.

#### <a name="verify-kubernetes-service-accounts"></a>Kubernetes-service accounts controleren

Controleer of u een kubernetes-service account hebt in de naam ruimte waarin uw toepassing is geïmplementeerd.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

In de volgende bevindt zich een service account `bookbuyer` met de naam in de naam ruimte bookbuyer.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Als u geen ander service account dan het standaard account hebt, moet u er een maken voor uw toepassing. Gebruik de volgende opdracht als voor beeld voor het maken van een service account in de geïmplementeerde naam ruimte van de toepassing.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>De huidige implementatie specificatie van uw toepassing weer geven

Als u een service account uit de vorige sectie moest maken, is de implementatie van uw toepassing waarschijnlijk niet geconfigureerd met een specifieke `serviceAccountName` in de implementatie specificatie. We kunnen de implementatie specificatie van uw toepassing weer geven met de volgende opdrachten:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Er wordt een lijst met implementaties weer gegeven in de uitvoer.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

We beschrijven nu de implementatie als een controle om te zien of er een service account wordt vermeld in de pod-sjabloon sectie.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

In deze specifieke implementatie kunt u zien dat er een service account is gekoppeld aan de implementatie die wordt vermeld in de sectie pod-sjabloon. Deze implementatie maakt gebruik van de bookbuyer van het service account. Als u de **Service-account** niet ziet: eigenschap, is uw implementatie niet geconfigureerd om een service account te gebruiken.

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

Er zijn verschillende technieken om uw implementatie bij te werken om een kubernetes-service account toe te voegen. Raadpleeg de Kubernetes-documentatie voor het [bijwerken van een](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) inline-implementatie of het [configureren van service accounts voor een peuling](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/). Nadat u de implementatie specificatie hebt bijgewerkt met het service account, implementeert u opnieuw (kubectl apply-f uw-implementatie. yaml) uw implementatie naar het cluster.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Het vereiste SMI-beleid (service net Interface) implementeren

De laatste stap voor het toestaan van geautoriseerd verkeer voor stroom in het net is het implementeren van het benodigde [SMI](https://smi-spec.io/) Traffic-toegangs beleid voor uw toepassing. De hoeveelheid configuratie die u kunt bereiken met het [SMI](https://smi-spec.io/) Traffic-toegangs beleid valt buiten het bereik van deze procedure, maar we beschrijven een aantal algemene onderdelen van de specificatie en laten zien hoe u een eenvoudig beleid voor TrafficTarget en HTTPRouteGroup configureert om service-to-service-communicatie in te scha kelen voor uw toepassing.

Met het [SMI](https://smi-spec.io/) [**Traffic Access Control**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) -specificatie kunnen gebruikers het toegangs beheer beleid voor hun toepassingen definiëren. We zullen zich richten op de **TrafficTarget** -en **HTTPRoutGroup** -API-resources.

De TrafficTarget-resource bestaat uit drie hoofd configuratie-instellingen, regels en bronnen. Hieronder ziet u een voor beeld van een TrafficTarget.

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

In de bovenstaande TrafficTarget SPEC geeft het `destination` Service account aan dat is geconfigureerd voor de doel bron service. Houd er rekening mee dat het service account dat eerder is toegevoegd aan de implementatie, wordt gebruikt om toegang te verlenen tot de implementatie waaraan het is gekoppeld. De `rules` sectie, in dit specifieke voor beeld, definieert het type HTTP-verkeer dat via de verbinding is toegestaan. U kunt reguliere regex-patronen configureren voor de HTTP-headers die specifiek zijn voor welk verkeer via HTTP is toegestaan. De `sources` sectie is van de service die de communicatie verzorgt. Deze specificatie leest bookbuyer moet communiceren met de boek winkel.

De HTTPRouteGroup-resource bestaat uit een of een matrix met overeenkomsten met de HTTP-header en is een vereiste voor de TrafficTarget spec. In het onderstaande voor beeld ziet u dat de HTTPRouteGroup drie HTTP-acties, twee GET en één POST, autoriseert.

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

Als u niet bekend bent met het type HTTP-verkeer dat door uw front-endtoepassing-toepassing in andere lagen van de toepassing wordt uitgevoerd, kunt u het equivalent van een regel voor het toestaan van een regel maken met behulp van de onderstaande spec voor HTTPRouteGroup.

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

Zodra u uw TrafficTarget-en HTTPRouteGroup-spec hebt geconfigureerd, kunt u ze samen voegen als één YAML en implementeren. Hieronder ziet u de boek winkel-voorbeeld configuratie.

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

Ga naar de [SMI](https://smi-spec.io/) -site voor meer gedetailleerde informatie over de specificatie.

### <a name="manage-the-applications-namespace-with-osm"></a>De naam ruimte van de toepassing beheren met OSM

Vervolgens configureert u OSM voor het beheren van de naam ruimte en start u de implementaties opnieuw om de Envoy-zijspan proxy te verkrijgen die is geïnjecteerd met de toepassing.

Voer de volgende opdracht uit om de `azure-vote` naam ruimte te configureren voor het beheren van mijn OSM.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Start vervolgens zowel de `azure-vote-front` `azure-vote-back` implementatie als de volgende opdrachten.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Als we het peul voor de `azure-vote` naam ruimte weer geven, zien we de fase **ready** van zowel de `azure-vote-front` als de `azure-vote-back` 2/2, wat betekent dat de Envoy-zijspan proxy is geïnjecteerd naast de toepassing.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Zelf studie: een toepassing implementeren die wordt beheerd door open service net (OSM) met NGINX inkomend verkeer

Open service-net (OSM) is een licht gewicht, uitbreidbaar, Cloud systeem eigen service-mesh waarmee gebruikers eenvormige functies voor de beschik baarheid van de micro service kunnen beheren, beveiligen en vervolledigen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - De huidige OSM-cluster configuratie weer geven
> - Maak de naam ruimte (en) voor OSM voor het beheren van geïmplementeerde toepassingen in de naam ruimte (n)
> - Onboarding van de naam ruimten die door OSM worden beheerd
> - De voorbeeldtoepassing implementeren
> - Controleer of de toepassing wordt uitgevoerd in het AKS-cluster
> - Een NGINX ingress-controller maken die wordt gebruikt voor de applicatie
> - Een service beschikbaar maken via de Azure-toepassing gateway binnenkomen op Internet

### <a name="before-you-begin"></a>Voordat u begint

Voor de stappen die in dit artikel worden beschreven, wordt ervan uitgegaan dat u een AKS-cluster hebt gemaakt (Kubernetes `1.19+` en hoger, waarbij KUBERNETES RBAC is ingeschakeld), een `kubectl` verbinding hebben met het cluster (als u hulp nodig hebt bij een van deze items, raadpleegt u de [AKS Quick](./kubernetes-walkthrough.md)start en hebt u de AKS OSM-invoeg toepassing geïnstalleerd.

U moet de volgende resources hebben geïnstalleerd:

- De Azure CLI, versie 2.20.0 of hoger
- De `aks-preview` extensie versie 0.5.5 of hoger
- OSM-versie v 0.8.0 of hoger
- apt-installatie ophalen JQ

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>De huidige OSM-cluster configuratie weer geven en controleren

Zodra de OSM-invoeg toepassing voor AKS is ingeschakeld op het AKS-cluster, kunt u de huidige configuratie parameters weer geven in OSM-config Kubernetes ConfigMap. Voer de volgende opdracht uit om de eigenschappen van de ConfigMap weer te geven:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

In de uitvoer ziet u de huidige OSM-configuratie voor het cluster.

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

U ziet dat de **permissive_traffic_policy_mode** is ingesteld op **True**. De modus voor toelaatings beleid voor verkeer in OSM is een modus waarin het [SMI](https://smi-spec.io/) traffic-verkeer wordt afgedwongen. In deze modus detecteert OSM automatisch services die deel uitmaken van het service-net en Program ma's verkeers beleids regels op elke Envoy-proxy om te kunnen communiceren met deze services.

### <a name="create-namespaces-for-the-application"></a>Naam ruimten voor de toepassing maken

In deze zelf studie wordt gebruikgemaakt van de OSM boek winkel-toepassing met de volgende toepassings onderdelen:

- bookbuyer
- bookthief
- Boek winkel
- bookwarehouse

Maak naam ruimten voor elk van deze toepassings onderdelen.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Onboarding van de naam ruimten die door OSM worden beheerd

Door de naam ruimten toe te voegen aan de OSM-Mesh kan de OSM-controller de Envoy-zijspan wagen proxy containers automatisch injecteren met uw toepassing. Voer de volgende opdracht uit om de OSM boek winkel-toepassings naam ruimten toe te staan.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>De boek winkel-toepassing implementeren in het AKS-cluster

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

Alle implementatie-uitvoer vindt u hieronder.

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

Werk de bookbuyer-service bij naar de juiste binnenkomende poort configuratie met het volgende service manifest.

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

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Controleren of de boek winkel-toepassing in het AKS-cluster wordt uitgevoerd

Vanaf nu hebben we de boek winkel Mulit-container toepassing geïmplementeerd, maar deze is alleen toegankelijk vanuit het AKS-cluster. Later voegen we de Azure-toepassing gateway ingangs controller toe om de toepassing zichtbaar te maken buiten het AKS-cluster. Om te controleren of de toepassing wordt uitgevoerd in het cluster, zullen we een poort gebruiken om de gebruikers interface van het bookbuyer-onderdeel weer te geven.

Eerst gaan we de naam van de bookbuyer pod ophalen

```azurecli-interactive
kubectl get pod -n bookbuyer
```

De uitvoer ziet er als volgt uit. Aan uw bookbuyer-pod wordt een unieke naam toegevoegd.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Zodra u de naam van de pod hebt, kunt u nu de opdracht voor het door sturen van een tunnel van het lokale systeem instellen op de toepassing binnen het AKS-cluster. Voer de volgende opdracht uit om de poort voor de lokale systeem poort 8080 in te stellen. Gebruik opnieuw uw opgegeven bookbuyer pod-naam.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

De uitvoer ziet er ongeveer als volgt uit.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Terwijl de poort voor het door sturen is ingesteld, navigeert u naar de volgende URL vanuit een browser `http://localhost:8080` . Nu moet u de gebruikers interface van de bookbuyer-toepassing in de browser kunnen zien, vergelijkbaar met de onderstaande afbeelding.

![OSM bookbuyer-app voor NGINX UI-afbeelding](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Een NGINX ingress-controller maken in azure Kubernetes service (AKS)

Een controller voor inkomend verkeer is een stukje software dat omgekeerde proxy’s, configureerbare verkeersroutering en TLS-beëindiging voor Kubernetes-services biedt. Kubernetes-resources voor inkomend verkeer worden gebruikt om de regels en routes voor uitgaand verkeer worden geconfigureerd voor individuele Kubernetes-services. Met behulp van een controller en regels voor inkomend verkeer kan er één enkel IP-adres worden gebruikt voor het routeren van verkeer naar meerdere services in een Kubernetes-cluster.

We gebruiken de ingangs controller om de toepassing weer te geven die wordt beheerd door OSM op internet. Als u de ingangs controller wilt maken, gebruikt u helm om Nginx te installeren. Voor toegevoegde redundantie worden er twee replica's van de NGINX-ingangscontrollers geïmplementeerd met de parameter `--set controller.replicaCount`. Om volledig te profiteren van het uitvoeren van replica's van de ingangs controller, moet u ervoor zorgen dat er meer dan één knoop punt in uw AKS-cluster is.

De ingangscontroller moet ook worden gepland op een Linux-knooppunt. Windows Server-knooppunten mogen de ingangscontroller niet uitvoeren. Er wordt een knooppuntselector opgegeven met behulp van de parameter `--set nodeSelector` om de Kubernetes-planner te laten weten dat de NGINX-ingangscontroller moet worden uitgevoerd op een Linux-knooppunt.

> [!TIP]
> In het volgende voor beeld wordt een Kubernetes-naam ruimte gemaakt voor de ingangs resources met de naam _ingress-Basic_. Geef waar nodig een naam ruimte op voor uw eigen omgeving.

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

Wanneer de Kubernetes load balancer-service is gemaakt voor de NGINX ingress-controller, wordt een dynamisch openbaar IP-adres toegewezen, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Er zijn nog geen regels voor binnenkomend verkeer gemaakt, dus de standaard 404-pagina van de NGINX ingress-controller wordt weer gegeven als u naar het interne IP-adres bladert. Ingangs regels worden in de volgende stappen geconfigureerd.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>De bookbuyer-service op internet beschikbaar maken

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

### <a name="view-the-nginx-logs"></a>De NGINX-logboeken weer geven

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

In de uitvoer ziet u de status van de NGINX ingangs controller wanneer de ingangs regel is toegepast:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>De service NGINX Services en bookbuyer extern weer geven

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Omdat de hostnaam in het manifest van de ingang een psuedo-naam is die wordt gebruikt voor het testen, is de DNS-naam niet beschikbaar op internet. U kunt ook het krul-programma gebruiken en de hostname-header naar het open bare IP-adres van NGINX sturen en een 200-code ontvangen waarmee we verbinding kunnen maken met de bookbuyer-service.

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

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Zelf studie: een toepassing implementeren die wordt beheerd door open service net (OSM) met behulp van Azure-toepassing gateway-invoeg toepassing AKS

Open service-net (OSM) is een licht gewicht, uitbreidbaar, Cloud systeem eigen service-mesh waarmee gebruikers eenvormige functies voor de beschik baarheid van de micro service kunnen beheren, beveiligen en vervolledigen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - De huidige OSM-cluster configuratie weer geven
> - Maak de naam ruimte (en) voor OSM voor het beheren van geïmplementeerde toepassingen in de naam ruimte (n)
> - Onboarding van de naam ruimten die door OSM worden beheerd
> - De voorbeeldtoepassing implementeren
> - Controleer of de toepassing wordt uitgevoerd in het AKS-cluster
> - Een Azure-toepassing gateway maken die moet worden gebruikt als de ingangs controller voor de applicatie
> - Een service beschikbaar maken via de Azure-toepassing gateway binnenkomen op Internet

### <a name="before-you-begin"></a>Voordat u begint

Bij de stappen die in dit artikel worden beschreven, wordt ervan uitgegaan dat u een AKS-cluster hebt gemaakt (Kubernetes `1.19+` en hoger, waarbij KUBERNETES RBAC is ingeschakeld), een `kubectl` verbinding hebben met het cluster (als u hulp nodig hebt bij een van deze items, raadpleegt u de [AKS Quick](./kubernetes-walkthrough.md)start, hebt u de AKS OSM-invoeg toepassing geïnstalleerd en maakt u een nieuwe Azure-toepassing-gateway voor inkomen

U moet de volgende resources hebben geïnstalleerd:

- De Azure CLI, versie 2.20.0 of hoger
- De `aks-preview` extensie versie 0.5.5 of hoger
- AKS-cluster versie 1.19 + met Azure CNI Network (gekoppeld aan een Azure-Vnet)
- OSM-versie v 0.8.0 of hoger
- apt-installatie ophalen JQ

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>De huidige OSM-cluster configuratie weer geven en controleren

Zodra de OSM-invoeg toepassing voor AKS is ingeschakeld op het AKS-cluster, kunt u de huidige configuratie parameters weer geven in OSM-config Kubernetes ConfigMap. Voer de volgende opdracht uit om de eigenschappen van de ConfigMap weer te geven:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

In de uitvoer ziet u de huidige OSM-configuratie voor het cluster.

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

U ziet dat de **permissive_traffic_policy_mode** is ingesteld op **True**. De modus voor toelaatings beleid voor verkeer in OSM is een modus waarin het [SMI](https://smi-spec.io/) traffic-verkeer wordt afgedwongen. In deze modus detecteert OSM automatisch services die deel uitmaken van het service-net en Program ma's verkeers beleids regels op elke Envoy-proxy om te kunnen communiceren met deze services.

### <a name="create-namespaces-for-the-application"></a>Naam ruimten voor de toepassing maken

In deze zelf studie wordt gebruikgemaakt van de OSM boek winkel-toepassing met de volgende toepassings onderdelen:

- bookbuyer
- bookthief
- Boek winkel
- bookwarehouse

Maak naam ruimten voor elk van deze toepassings onderdelen.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Onboarding van de naam ruimten die door OSM worden beheerd

Wanneer u de naam ruimten aan het OSM-net toevoegt, kan de OSM-controller de Envoy-zijspan wagen proxy containers automatisch injecteren met uw toepassing. Voer de volgende opdracht uit om de OSM boek winkel-toepassings naam ruimten toe te staan.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>De boek winkel-toepassing implementeren in het AKS-cluster

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

Alle implementatie-uitvoer vindt u hieronder.

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

Werk de bookbuyer-service bij naar de juiste binnenkomende poort configuratie met het volgende service manifest.

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

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Controleren of de boek winkel-toepassing in het AKS-cluster wordt uitgevoerd

Vanaf nu hebben we de toepassing boek winkel multi-container geïmplementeerd, maar deze is alleen toegankelijk vanuit het AKS-cluster. Later voegen we de Azure-toepassing gateway ingangs controller toe om de toepassing zichtbaar te maken buiten het AKS-cluster. Om te controleren of de toepassing wordt uitgevoerd in het cluster, zullen we een poort gebruiken om de gebruikers interface van het bookbuyer-onderdeel weer te geven.

Eerst gaan we de naam van de bookbuyer pod ophalen

```azurecli-interactive
kubectl get pod -n bookbuyer
```

De uitvoer ziet er als volgt uit. Aan uw bookbuyer-pod wordt een unieke naam toegevoegd.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

Zodra u de naam van de pod hebt, kunt u nu de opdracht voor het door sturen van een tunnel van het lokale systeem instellen op de toepassing binnen het AKS-cluster. Voer de volgende opdracht uit om de poort voor de lokale systeem poort 8080 in te stellen. Gebruik opnieuw uw specifieke bookbuyer pod-naam.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

De uitvoer ziet er ongeveer als volgt uit.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

Terwijl de poort voor het door sturen is ingesteld, navigeert u naar de volgende URL vanuit een browser `http://localhost:8080` . Nu moet u de gebruikers interface van de bookbuyer-toepassing in de browser kunnen zien, vergelijkbaar met de onderstaande afbeelding.

![UI-installatie kopie van OSM bookbuyer-app voor app gateway](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>Een Azure-toepassing gateway maken om de bookbuyer-toepassing buiten het AKS-cluster beschikbaar te stellen

> [!NOTE]
> In de volgende richtingen wordt een nieuw exemplaar van de Azure-toepassing-gateway gemaakt dat voor inkomend verkeer moet worden gebruikt. Als u een bestaande Azure-toepassing gateway wilt gebruiken, gaat u naar de sectie voor het inschakelen van de invoeg toepassing voor de Application Gateway ingangs controller.

#### <a name="deploy-a-new-application-gateway"></a>Een nieuwe Azure Application Gateway implementeren

> [!NOTE]
> Er wordt verwezen naar de bestaande documentatie voor het inschakelen van de invoeg toepassing Application Gateway ingangs controller voor een bestaand AKS-cluster. Er zijn enkele wijzigingen aangebracht in de OSM-materialen. Meer gedetailleerde documentatie over het onderwerp vindt u [hier](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing).

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

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>De invoeg toepassing AGIC inschakelen voor een bestaand AKS-cluster via Azure CLI

Als u Azure CLI wilt blijven gebruiken, kunt u door gaan met het inschakelen van de invoeg toepassing AGIC in het AKS-cluster dat u hebt gemaakt, _myCluster_ en geeft u de AGIC-invoeg toepassing op om de bestaande Application Gateway te gebruiken die u hebt gemaakt, _myApplicationGateway_.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

U kunt controleren of de Azure-toepassing gateway AKS-invoeg toepassing is ingeschakeld door de volgende opdracht uit te scha kelen.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

Met deze opdracht wordt de uitvoer weer gegeven als `true` .

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

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>De bookbuyer-service op internet beschikbaar maken

Pas het volgende ingangs manifest toe aan het AKS-cluster om de bookbuyer-service beschikbaar te maken voor het Internet via de Azure-toepassing gateway.

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

Omdat de hostnaam in het manifest van de ingang een pseudo-naam is die wordt gebruikt voor het testen, is de DNS-naam niet beschikbaar op internet. U kunt ook het krul-programma gebruiken en de host-header in het open bare IP-adres van de Azure-toepassing gateway plakken en een 200-code ontvangen waarmee u verbinding kunt maken met de bookbuyer-service.

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
- [Er zijn extra hulpprogram ma's voor probleem oplossing beschikbaar op de GitHub opslag plaats van AGIC](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Open OSM-bewaking en-bewaken met behulp van Azure Monitor en toepassingen inzichten

Zowel Azure Monitor als Azure-toepassing Insights helpt u bij het maximaliseren van de beschik baarheid en prestaties van uw toepassingen en services door een uitgebreide oplossing te bieden voor het verzamelen, analyseren en uitvoeren van telemetrie in uw Cloud-en on-premises omgevingen.

De OSM AKS-invoeg toepassing heeft diep gaande integraties in beide Azure-Services en biedt een schijnloze Azure-ervaring voor het weer geven en reageren op essentiële Kpi's die worden geleverd door OSM-metrische gegevens. Ga voor meer informatie over het inschakelen en configureren van deze services voor de OSM AKS-invoeg toepassing naar de pagina [Azure monitor voor OSM](https://aka.ms/azmon/osmpreview) voor meer informatie.

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Zelf studie: Prometheus, Grafana en Jaeger hand matig implementeren om open Service Mesh (OSM) metrische gegevens weer te geven voor de naleving

> [!WARNING]
> De installatie van Prometheus, Grafana en Jaeger worden als algemene richt lijnen gegeven, zodat u kunt zien hoe deze hulpprogram ma's kunnen worden gebruikt voor het weer geven van OSM-metrische gegevens. De installatie richtlijnen kunnen niet worden gebruikt voor het instellen van een productie. Raadpleeg de documentatie van elk hulp programma voor meer informatie over de manier waarop u de installatie aan uw behoeften kunt aanpassen. De meest voorkomende is het gebrek aan permanente opslag, wat betekent dat alle gegevens verloren gaan zodra een Prometheus Grafana en/of Jaeger pod (s) zijn beëindigd.

Open service-net (OSM) genereert gedetailleerde gegevens over alle verkeer binnen het net. Deze metrische gegevens bieden inzicht in het gedrag van toepassingen in het net waarmee gebruikers hun toepassingen kunnen oplossen, onderhouden en analyseren.

Vanaf vandaag OSM verzamelen de metrische gegevens rechtstreeks van de zijspan-proxy's (Envoy). OSM biedt uitgebreide metrische gegevens voor binnenkomend en uitgaand verkeer voor alle services in het net. Met deze metrische gegevens kan de gebruiker informatie verkrijgen over het algehele verkeer, fouten in het verkeer en de reactie tijd voor aanvragen.

OSM maakt gebruik van Prometheus voor het verzamelen en opslaan van consistente metrische gegevens over verkeer en statistieken voor alle toepassingen die in het net worden uitgevoerd. Prometheus is een open source-en Alerting-Toolkit, die meestal wordt gebruikt op (maar niet beperkt tot) Kubernetes-en Service-Mesh omgevingen.

Elke toepassing die deel uitmaakt van de mesh-uitvoeringen in een pod die een Envoy-zijspan bevat waarmee metrische gegevens (metrische gegevens over de proxy) in de Prometheus-indeling worden weer gegeven. Daarnaast bevat elke pod die deel uitmaakt van het net Prometheus annotaties, waardoor het mogelijk is dat de Prometheus-server de toepassing dynamisch kan uitval. Dit mechanisme schakelt automatisch uitval van metrische gegevens in wanneer een nieuwe naam ruimte/pod/service wordt toegevoegd aan het net.

OSM-metrische gegevens kunnen worden weer gegeven met Grafana, een open-source visualisatie en analyse software. U kunt hiermee uw metrische gegevens opvragen, visualiseren, waarschuwen en verkennen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Een Prometheus-exemplaar maken en implementeren
> - OSM configureren om Prometheus-uitval toe te staan
> - De Prometheus-Configmap bijwerken
> - Een Grafana-exemplaar maken en implementeren
> - Grafana configureren met de Prometheus-gegevens bron
> - OSM-dash board importeren voor Grafana
> - Een Jaeger-exemplaar maken en implementeren
> - Jaeger tracering configureren voor OSM

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Een Prometheus-exemplaar voor OSM implementeren en configureren

We gebruiken helm om het Prometheus-exemplaar te implementeren. Voer de volgende opdrachten uit om Prometheus te installeren via helm:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Als de installatie is voltooid, ziet u de onderstaande uitvoer. Noteer de Prometheus-server poort en de cluster-DNS-naam. Deze informatie wordt later gebruikt voor om Prometheus te configureren als gegevens bron voor Grafana.

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

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>OSM configureren om Prometheus-uitval toe te staan

Om ervoor te zorgen dat de OSM-onderdelen zijn geconfigureerd voor Prometheus-schroot, willen we de **prometheus_scraping** configuratie in het configuratie bestand van OSM-config controleren. Bekijk de configuratie met de volgende opdracht:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

De uitvoer van de vorige opdracht moet worden geretourneerd `true` als OSM is geconfigureerd voor Prometheus-uitval. Als de geretourneerde waarde is `false` , moeten we de configuratie bijwerken `true` . Voer de volgende opdracht uit om OSM Prometheus-uitval **in** te scha kelen:

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

De volgende uitvoer wordt weergegeven.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>De Prometheus-Configmap bijwerken

De standaard installatie van Prometheus bevat twee Kubernetes configmaps. U kunt de lijst met Prometheus configmaps met de volgende opdracht weer geven.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

We moeten de configuratie van Prometheus. yml die zich in de **stabiele-Prometheus-server** -configmap bevindt, vervangen door de volgende OSM-configuratie. Er zijn verschillende technieken voor het bewerken van bestanden om deze taak uit te voeren. Een eenvoudige en veilige manier is om de configmap te exporteren, een kopie ervan te maken voor back-up en deze vervolgens te bewerken met een editor zoals Visual Studio code.

> [!NOTE]
> Als Visual Studio code niet is [geïnstalleerd, kunt u het downloaden](https://code.visualstudio.com/Download)en installeren.

We gaan eerst de **stabiele Prometheus-server** configmap exporteren en vervolgens een kopie maken voor back-up.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Nu kunt u het bestand openen met Visual Studio code om het te bewerken.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

Zodra u de configmap hebt geopend in Visual Studio code-editor, vervangt u het bestand Prometheus. yml door de OSM-configuratie hieronder en slaat u het bestand op.

> [!WARNING]
> Het is zeer belang rijk dat u ervoor zorgt dat u de inspring structuur van het yaml-bestand behoudt. Wijzigingen in de yaml-bestands structuur kunnen ertoe leiden dat de configmap niet opnieuw kan worden toegepast.

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

Pas het bijgewerkte bestand configmap yaml toe met de volgende opdracht.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Er wordt mogelijk een bericht weer gegeven over een ontbrekende kubernetes-aantekening. Dit kan nu worden genegeerd.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Controleer of Prometheus is geconfigureerd om de OSM mesh-en API-eind punten af te vallen

Om te controleren of Prometheus correct is geconfigureerd om de OSM mesh-en API-eind punten op te schroot, gaan we door naar de Prometheus pod en de doel configuratie weer geven. Voer de volgende opdrachten uit.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Een browser openen tot `http://localhost:9090/targets`

Als u naar beneden schuift, ziet u dat de status **van** de SMI-metriek-eind punten en andere OSM-metrische gegevens worden gedefinieerd, zoals hieronder aangegeven.

![UI-afbeelding OSM Prometheus doel](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Een Grafana-exemplaar voor OSM implementeren en configureren

We gebruiken helm om het Grafana-exemplaar te implementeren. Voer de volgende opdrachten uit om Grafana te installeren via helm:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

Vervolgens haalt u het standaard Grafana-wacht woord op om u aan te melden bij de Grafana-site.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Noteer het Grafana-wacht woord.

Vervolgens haalt u de Grafana-pod op naar de poort naar het Grafana-dash board om u aan te melden.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Een browser openen tot `http://localhost:3000`

In het aanmeldings scherm dat hieronder wordt weer gegeven, voert u **beheerder** in als de gebruikers naam en gebruikt u het Grafana-wacht woord dat u eerder hebt vastgelegd.

![UI-afbeelding van OSM Grafana-aanmeldings pagina](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>De Grafana Prometheus-gegevens bron configureren

Zodra u bent aangemeld bij Grafana, is de volgende stap het toevoegen van Prometheus als gegevens bronnen voor Grafana. Hiertoe gaat u naar het configuratie pictogram in het linkermenu en selecteert u gegevens bronnen, zoals hieronder wordt weer gegeven.

![UI-afbeelding van OSM Grafana data sources-pagina](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Klik op de knop **gegevens bron toevoegen** en selecteer Prometheus onder time series-data bases.

![UI-afbeelding van selectie pagina OSM Grafana data sources](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

Voer op de pagina **uw Prometheus-gegevens bron configureren** de FQDN-naam van het Kubernetes-cluster voor de Prometheus-service in voor de instelling van de http-URL. De standaard-FQDN moet zijn `stable-prometheus-server.default.svc.cluster.local` . Nadat u het Prometheus-service-eind punt hebt ingevoerd, schuift u naar de onderkant van de pagina en selecteert u **& test opslaan**. U ontvangt een groen selectie vakje om aan te geven dat de gegevens bron werkt.

#### <a name="importing-osm-dashboards"></a>OSM-Dash boards importeren

OSM-Dash boards zijn beschikbaar via:

- [Onze opslag plaats](/charts/osm/grafana)en zijn niet-bepoortbaar als JSON-blobs via de webbeheer Portal
- of [online op Grafana.com](https://grafana.com/grafana/dashboards/14145)

Als u een dash board wilt importeren, zoekt u naar het `+` menu links en selecteert u `import` .
U kunt dash board rechtstreeks importeren op basis van hun ID op `Grafana.com` . Het `OSM Mesh Details` dash board gebruikt bijvoorbeeld id `14145` , u kunt de id rechtstreeks op het formulier gebruiken en selecteren `import` :

![UI-afbeelding OSM Grafana-dash board importeren](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

Zodra u importeren selecteert, wordt u automatisch naar het geïmporteerde dash board gebracht.

![UI-afbeelding van de OSM Grafana-pagina met gegevens van de dash board mesh](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Een Jaeger-operator implementeren en configureren op Kubernetes voor OSM

[Jaeger](https://www.jaegertracing.io/) is een open-source tracerings systeem dat wordt gebruikt voor het bewaken en oplossen van problemen met gedistribueerde systemen. Het kan worden geïmplementeerd met OSM als een nieuw exemplaar of u kunt uw eigen exemplaar maken. De volgende instructies implementeren een nieuw exemplaar van Jaeger in de `jaeger` naam ruimte op het AKS-cluster.

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

#### <a name="enable-tracing-for-the-osm-add-on"></a>Tracering inschakelen voor de OSM-invoeg toepassing

Vervolgens moet u tracering inschakelen voor de invoeg toepassing OSM.

> [!NOTE]
> Vanaf nu worden de eigenschappen voor tracering niet zichtbaar in het OSM-configuratie configmap op dit moment. Dit wordt zichtbaar in een nieuwe release van de OSM AKS-invoeg toepassing.

Voer de volgende opdracht uit om tracering in te scha kelen voor de OSM-invoeg toepassing:

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>De Jaeger-gebruikers interface weer geven met poort door sturen

De gebruikers interface van Jaeger wordt uitgevoerd op poort 16686. Als u de Web-UI wilt weer geven, kunt u kubectl-poort-forward gebruiken:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

In de browser ziet u een vervolg keuzelijst voor services, waarmee u een keuze kunt selecteren uit de verschillende toepassingen die worden geïmplementeerd door de boek winkel-demo. Selecteer een service om alle reeksen ervan weer te geven. Als u bijvoorbeeld bookbuyer met een Lookback van één uur selecteert, ziet u de bijbehorende interacties met boek winkel-v1 en boek winkel-v2, gesorteerd op tijd.

![UI-afbeelding van OSM Jaeger tracering-pagina](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Selecteer een item om het weer te geven in meer detail. Selecteer meerdere items om traceringen te vergelijken. U kunt bijvoorbeeld de interacties van bookbuyer met boek winkel en boek winkel-v2 vergelijken op een bepaald moment.

U kunt ook het tabblad systeem architectuur selecteren om een grafiek weer te geven van de manier waarop de verschillende toepassingen onderling handelen/communiceren. Dit geeft een idee van hoe verkeer tussen de toepassingen loopt.

![UI-installatie kopie van OSM Jaeger-systeem architectuur](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Service-Mesh openen (OSM) AKS-invoeg toepassing probleemoplossings handleidingen

Wanneer u de AKS-invoeg toepassing voor OSM implementeert, kan er af en toe een probleem optreden. De volgende hand leidingen helpen u bij het oplossen van fouten en het oplossen van veelvoorkomende problemen.

### <a name="verifying-and-troubleshooting-osm-components"></a>OSM-onderdelen controleren en problemen oplossen

#### <a name="check-osm-controller-deployment"></a>Implementatie van OSM-controller controleren

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

Een gezonde OSM-controller ziet er als volgt uit:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>Controleer de OSM-controller pod

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

Een gezonde OSM-pod zou er als volgt uitzien:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Hoewel we op een bepaald moment één controller hebben verwijderd, hebben we er een een te maken die klaar is voor 1/1 en het uitvoeren van 0 opnieuw opstarten. Als de kolom gereed is voor een andere versie dan 1/1, zou het service-net een verbroken status hebben.
Kolom READY with 0/1 geeft aan dat de container van het besturings element is vastgelopen. er moeten logboeken worden opgehaald. Zie OSM-controller logboeken ophalen in het gedeelte ondersteunings centrum van Azure hieronder. Kolom gereed met een getal hoger dan 1 na de/geeft aan dat er zijspaners zijn geïnstalleerd. De OSM-controller werkt waarschijnlijk niet met alle zijspaners die eraan zijn gekoppeld.

> [!NOTE]
> Vanaf versie v 0.8.2 de OSM-controller niet in de HA-modus en wordt uitgevoerd in een geïmplementeerd met een aantal replica's van 1-single pod. De Pod heeft status controles en wordt indien nodig opnieuw opgestart door de kubelet.

#### <a name="check-osm-controller-service"></a>Controleer de OSM-controller service

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

Een gezonde OSM-controller service ziet er als volgt uit:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> Het CLUSTER-IP zou anders zijn. De service naam en poort (en) moeten gelijk zijn aan het bovenstaande voor beeld.

#### <a name="check-osm-controller-endpoints"></a>OSM-controller eindpunten controleren

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

Een gezonde OSM-controller-eind punt (en) ziet er als volgt uit:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>De implementatie van OSM-injectie controleren

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Een gezonde OSM-injectie implementatie ziet er als volgt uit:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>OSM-injectie pod controleren

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

Een gezonde OSM-injectie pod ziet er als volgt uit:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>De OSM-injectie service controleren

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

Een gezonde OSM Injecter-service ziet er als volgt uit:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>OSM-eind punten controleren

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

Een gezonde OSM-eind punt ziet er als volgt uit:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Controleren op validatie-en muteren-webhooks

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

Een gezonde OSM om de webhook te controleren ziet er als volgt uit:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

Een gezonde OSM muteren webhook ziet er als volgt uit:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Controleer op de service en de CA-bundel van de webhook valideren

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Een goed geconfigureerde configuratie van webhooks ziet er ongeveer zo uit:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Controleer op de service en de CA-bundel van de muteren-webhook

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

Een goed geconfigureerde muteren-webhook-configuratie ziet er ongeveer zo uit:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Controleer of de OSM-controller de validatie (of muteren)-webhook van een CA-bundel heeft gegeven

> [!NOTE]
> Vanaf de v-0.8.2 is het belang rijk om te weten dat AKS RP de validatie van de webhook installeert, de AKS-afstemmings functie zorgt ervoor dat deze bestaat, maar OSM-controller het is de CA-bundel.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Met dit nummer wordt het aantal bytes of de grootte van de CA-bundel aangegeven. Als dit leeg is, 0 of een getal onder 1000, zou dit aangeven dat de CA-bundel niet goed is ingericht. Zonder een juiste CA-bundel zou de validatie van de webhook niet kunnen worden uitgevoerd en wordt voor komen dat de gebruiker wijzigingen kan aanbrengen in de OSM-config ConfigMap in de uitvoeren-systeem naam ruimte.

Een voorbeeld fout wanneer de CA-bundel onjuist is:

- Er is een poging gedaan om de OSM-configuratie ConfigMap te wijzigen:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Fout:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

Tijdelijke oplossing voor wanneer de **validatie van** de webhook-configuratie een beschadigd certificaat heeft:

- Optie 1: OSM-controller opnieuw starten-Hiermee wordt de OSM-controller opnieuw opgestart. Bij het starten wordt de CA-bundel van zowel de muteren als de validatie van webhooks overschreven.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- Optie 2-optie 2. De validatie van de webhook verwijderen: door de validatie van de webhook te verwijderen, worden mutaties van de `osm-config` ConfigMap niet meer gevalideerd. Elke patch gaat door. De AKS-afstemmings functie zorgt ervoor dat de validatie van de webhook bestaat en zal deze vervolgens opnieuw maken. De OSM-controller moet mogelijk opnieuw worden opgestart om de CA-bundel snel opnieuw te schrijven.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- Optie 3-verwijderen en patch: met de volgende opdracht wordt de validatie van de webhook verwijderd, waardoor er waarden kunnen worden toegevoegd en er onmiddellijk een patch wordt toegepast. Waarschijnlijk heeft de AKS-reconciliatie onvoldoende tijd om de validatie van de webhook te maken en te herstellen, zodat u de mogelijkheid hebt om een wijziging toe te passen als laatste redmiddel:

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>Controleer de `osm-config` **ConfigMap**

> [!NOTE]
> De OSM-controller is niet vereist om de `osm-config` ConfigMap aanwezig te zijn in de uitvoeren-systeem naam ruimte. De controller heeft redelijke standaard waarden voor de configuratie en kan zonder deze worden gebruikt.

Controleren op bestaan:

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

Controleer de inhoud van de OSM-config ConfigMap

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

`osm-config` ConfigMap waarden:

| Sleutel                              | Type   | Toegestane waarden                                          | Standaardwaarde                          | Functie                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| uitgaande                           | booleaans   | de waarde True, false                                             | `"false"`                              | Hiermee maakt u een uitgangs punt in het net.                                                                                                                                                                                                             |
| enable_debug_server              | booleaans   | de waarde True, false                                             | `"true"`                               | Hiermee schakelt u een debug-eind punt in op de OSM-controller pod om informatie weer te geven over het net zoals proxy verbindingen, certificaten en SMI-beleids regels.                                                                                    |
| enable_privileged_init_container | booleaans   | de waarde True, false                                             | `"false"`                              | Hiermee kunnen geprivilegieerde init-containers voor peul in het net worden ingeschakeld. Als false is ingesteld, hebben init-containers alleen NET_ADMIN.                                                                                                                                   |
| envoy_log_level                  | tekenreeks | Trace, debug, info, Warning, Warning, fout, kritiek, uit | `"error"`                              | Hiermee stelt u de uitgebreide logboek registratie van Envoy-proxy-zijspaners in, alleen van toepassing op nieuw gemaakte peulen op het net. Als u het logboek niveau voor het bestaande peul wilt bijwerken, start u de implementatie opnieuw met `kubectl rollout restart` .                            |
| outbound_ip_range_exclusion_list | tekenreeks | een door komma's gescheiden lijst met IP-bereiken van het formulier a. b. c. d/x | `-`                                    | De algemene lijst met IP-adresbereiken die moeten worden uitgesloten van de Onderschep ping van uitgaand verkeer door de zijspan wagen.                                                                                                                                    |
| permissive_traffic_policy_mode   | booleaans   | de waarde True, false                                             | `"false"`                              | Als de instelling is ingesteld op `true` , wordt de modus Alles toestaan in het net, d.w.z. geen Traffic-beleid afgedwongen in het net. Als deze `false` optie is ingesteld op, wordt deny-allen Traffic-beleid in het net, dat wil zeggen, `SMI Traffic Target` nodig om te communiceren met Services. |
| prometheus_scraping              | booleaans   | de waarde True, false                                             | `"true"`                               | Hiermee schakelt u Prometheus metrische gegevens in op zijspaners-proxy's.                                                                                                                                                                                 |
| service_cert_validity_duration   | tekenreeks | 24 uur, 1h30m (elke tijds duur)                          | `"24h"`                                | Hiermee stelt u de geldigheids duur van het service certificaat in, weer gegeven als een reeks decimale getallen met een optionele Fractie en een achtervoegsel van een eenheid.                                                                                             |
| tracing_enable                   | booleaans   | de waarde True, false                                             | `"false"`                              | Hiermee schakelt u Jaeger tracering in voor het net.                                                                                                                                                                                                    |
| tracing_address                  | tekenreeks | Jaeger. mesh-namespace. SVC. cluster. local                 | `jaeger.kube-system.svc.cluster.local` | Het adres van de Jaeger-implementatie als tracering is ingeschakeld.                                                                                                                                                                                |
| tracing_endpoint                 | tekenreeks | /api/v2/spans                                           | /api/v2/spans                          | Eind punt voor het traceren van gegevens als tracering is ingeschakeld.                                                                                                                                                                                          |
| tracing_port                     | int    | een geheel getal dat niet gelijk is aan nul                              | `"9411"`                               | Poort waarop tracering is ingeschakeld.                                                                                                                                                                                                       |
| use_https_ingress                | booleaans   | de waarde True, false                                             | `"false"`                              | Hiermee schakelt u HTTPS-ingangen in op het net.                                                                                                                                                                                                      |
| config_resync_interval           | tekenreeks | onder 1 minuut wordt dit uitgeschakeld                            | 0 (uitgeschakeld)                           | Wanneer een waarde boven 1M (60s) wordt opgegeven, stuurt de OSM-controller alle beschik bare configuraties naar elke verbonden Envoy met het opgegeven interval                                                                                                    |

#### <a name="check-namespaces"></a>Naam ruimten controleren

> [!NOTE]
> De uitvoeren-naam ruimte neemt nooit deel aan een service-mesh en wordt nooit gelabeld en/of met de onderstaande sleutel/waarden gemarkeerd.

We gebruiken de `osm namespace add` opdracht om naam ruimten toe te voegen aan een bepaald service-net.
Wanneer een K8S-naam ruimte deel uitmaakt van het net (of voor een deel van het net), moet het volgende waar zijn:

De aantekeningen weer geven met

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

De volgende aantekening moet aanwezig zijn:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

De labels weer geven met

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

Het volgende label moet aanwezig zijn:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Als een naam ruimte geen aantekeningen bevat met `"openservicemesh.io/sidecar-injection": "enabled"` of niet is gelabeld met `"openservicemesh.io/monitored-by": "osm"` de OSM-injectie, worden geen Envoy-zijspaners toegevoegd.

> Opmerking: na `osm namespace add` wordt alleen **Nieuw** peul aangeroepen met een Envoy-zijspan wagen. Bestaande peulen moeten opnieuw worden opgestart met `kubectl rollout restart deployment ...`

#### <a name="verify-the-smi-crds"></a>Controleer de SMI CRDs:

Controleer of het cluster de vereiste CRDs heeft:

```azurecli-interactive
kubectl get crds
```

Het volgende moet zijn geïnstalleerd op het cluster:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Down load de versies van de CRDs die zijn geïnstalleerd met de volgende opdracht:

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

OSM controller v 0.8.2 vereist de volgende versies:

- traffictargets.access.smi-spec.io- [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io- [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io- [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io: niet ondersteund
- trafficsplits.split.smi-spec.io- [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*. metrics.smi-spec.io- [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Als CRDs ontbreken, gebruikt u de volgende opdrachten om deze te installeren op het cluster:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Open OSM-invoeg toepassing (Service-Mesh) voor uw AKS-cluster uitschakelen

Als u de OSM-invoeg toepassing wilt uitschakelen, voert u de volgende opdracht uit:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register