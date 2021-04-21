---
title: Een openbare Load Balancer
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het gebruik van een openbare load balancer met een Standard-SKU om uw services weer te geven met Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 3f2219f5052aee0c0a9cd43aa87df8789adbcae2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783085"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Een openbare Standard Load Balancer in Azure Kubernetes Service (AKS)

De Azure Load Balancer is op L4 van het OSI-model (Open Systems Interconnection) dat ondersteuning biedt voor zowel binnenkomende als uitgaande scenario's. Het distribueert inkomende stromen die binnenkomen bij de front-end load balancer van de back-endpool naar de back-endpools.

Een **openbare** Load Balancer is geïntegreerd met AKS heeft twee doeleinden:

1. Uitgaande verbindingen naar de clusterknooppunten in het virtuele AKS-netwerk bieden. Dit doel wordt bereikt door het privé-IP-adres van de knooppunten om te vertalen naar een openbaar IP-adres dat deel uitmaakt van *de uitgaande pool*.
2. Om toegang te bieden tot toepassingen via Kubernetes-services van het type `LoadBalancer` . Met deze service kunt u eenvoudig uw toepassingen schalen en services met hoge beschikbaar maken.

Er **wordt een interne (of privé)** load balancer gebruikt waarbij alleen privé-IP's zijn toegestaan als front-end. Interne load balancers worden gebruikt voor het verdelen van verkeer binnen een virtueel netwerk. Een load balancer front-end kan ook worden gebruikt vanuit een on-premises netwerk in een hybride scenario.

Dit document bevat informatie over de integratie met een openbare load balancer. Zie de documentatie Load Balancer interne load balancer van AKS voor [interne integratie.](internal-lb.md)

## <a name="before-you-begin"></a>Voordat u begint

Azure Load Balancer is beschikbaar in twee SKU's: *Basic* en *Standard.* Standaard wordt  de Standard-SKU gebruikt wanneer u een AKS-cluster maakt. Gebruik de *Standard-SKU* om toegang te krijgen tot toegevoegde functionaliteit, zoals een grotere back-endpool, meerdere [**knooppuntgroepen**](use-multiple-node-pools.md)en [**Beschikbaarheidszones**](availability-zones.md). Dit is de aanbevolen Load Balancer SKU voor AKS.

Zie Vergelijking van Azure *load balancer-SKU* voor meer informatie over de *Basic-* [en Standard-SKU's.][azure-lb-comparison]

In dit artikel wordt ervan uitgenomen dat u een AKS-cluster met de *Standard* SKU Azure Load Balancer hebt en wordt beschreven hoe u enkele van de mogelijkheden en functies van de load balancer. Als u een AKS-cluster nodig hebt, bekijkt u de AKS-quickstart met behulp van [de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Als u liever geen gebruik wilt maken van de Azure Load Balancer om een uitgaande verbinding te bieden en in plaats daarvan uw eigen gateway, firewall of proxy voor dat doel hebt, kunt u het maken van de uitgaande load balancer-pool en de respectieve front-end-IP overslaan door uitgaand type als [**UserDefinedRouting (UDR)**](egress-outboundtype.md)te gebruiken. Het uitgaande type definieert de uitgaande methode voor een cluster en typt standaard: load balancer.

## <a name="use-the-public-standard-load-balancer"></a>De openbare standaard load balancer

Na het maken van een AKS-cluster met uitgaand type: Load Balancer (standaard), is het cluster klaar om de load balancer ook te gebruiken om services weer te geven.

U kunt een openbare service van het type `LoadBalancer` maken, zoals wordt weergegeven in het volgende voorbeeld. Maak eerst een servicemanifest met de naam `public-svc.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: public-svc
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: public-app
```

Implementeer het manifest van de openbare service met [behulp van kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

De Azure Load Balancer geconfigureerd met een nieuw openbaar IP-adres dat voor deze nieuwe service wordt gebruikt. Omdat de Azure Load Balancer meerdere front-end-IP-adressen kan hebben, krijgt elke nieuwe service een nieuw toegewezen front-end-IP-adres dat uniek toegankelijk is.

U kunt controleren of uw service is gemaakt en of load balancer is geconfigureerd door bijvoorbeeld uit te gaan:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

Wanneer u de servicedetails bekijkt, wordt het openbare IP-adres dat voor deze service is gemaakt op de load balancer weergegeven in de kolom *EXTERNAL-IP.* Het kan een paar minuten duren om het IP-adres te wijzigen van in een echt openbaar *\<pending\>* IP-adres, zoals wordt weergegeven in het bovenstaande voorbeeld.

## <a name="configure-the-public-standard-load-balancer"></a>De openbare standaardinstelling load balancer

Wanneer u de openbare SKU Standard-load balancer, is er een set opties die tijdens het maken kunnen worden aangepast of door het cluster bij te werken. Met deze opties kunt u de Load Balancer om te voldoen aan de behoeften van uw workloads en moeten deze worden gecontroleerd. Met de Standard load balancer kunt u het volgende doen:

* Het aantal beheerde uitgaande IP's instellen of schalen
* Uw eigen aangepaste [ip-adressen voor uitgaand verkeer of uitgaand IP-voorvoegsel gebruiken](#provide-your-own-outbound-public-ips-or-prefixes)
* Het aantal toegewezen uitgaande poorten aanpassen aan elk knooppunt van het cluster
* De time-outinstelling voor niet-actieve verbindingen configureren

> [!IMPORTANT]
> Er kan slechts één uitgaande IP-optie (beheerde IP-adressen, Bring Your Own IP of IP-voorvoegsel) op een bepaald moment worden gebruikt.

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Het aantal beheerde uitgaande openbare IP's schalen

Azure Load Balancer biedt naast inkomende verbindingen ook uitgaande connectiviteit vanuit een virtueel netwerk. Uitgaande regels maken het eenvoudig om openbare Standard Load Balancer van uitgaande netwerkadressen te configureren.

Net als Load Balancer regels, volgen uitgaande regels dezelfde vertrouwde syntaxis als taakverdeling en inkomende NAT-regels:

***front-end-IPs + parameters + back-endpool***

Een uitgaande regel configureert uitgaande NAT voor alle virtuele machines die worden geïdentificeerd door de back-endpool om te worden vertaald naar de front-end. En parameters bieden extra fijnse controle over het uitgaande NAT-algoritme.

Een uitgaande regel kan worden gebruikt met slechts één openbaar IP-adres, maar uitgaande regels verlagen de configuratiebelasting voor het schalen van uitgaande NAT. U kunt meerdere IP-adressen gebruiken om grootschalige scenario's te plannen en u kunt uitgaande regels gebruiken om gevoelige patronen voor SNAT-uitputting te beperken. Elk extra IP-adres dat door een front-end wordt geleverd, biedt 64.000 kortstondige poorten die Load Balancer als SNAT-poorten. 

Wanneer u  een Standard-SKU gebruikt load balancer beheerde uitgaande openbare IP's, die standaard worden gemaakt, kunt u het aantal beheerde openbare IP's voor uitgaand verkeer schalen met behulp van de **`load-balancer-managed-ip-count`** parameter .

Voer de volgende opdracht uit om een bestaand cluster bij te werken. Deze parameter kan ook worden ingesteld tijdens het maken van het cluster om meerdere beheerde uitgaande openbare IP's te hebben.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

In het bovenstaande voorbeeld wordt het aantal beheerde uitgaande openbare IP's op *2* voor het cluster *myAKSCluster* in *myResourceGroup.* 

U kunt ook de parameter gebruiken om het initiële aantal beheerde openbare UITGAANDE IP's in te stellen bij het maken van uw cluster door de parameter toe te sluiten en deze in te stellen **`load-balancer-managed-ip-count`** **`--load-balancer-managed-outbound-ip-count`** op de gewenste waarde. Het standaard aantal beheerde openbare IP's voor uitgaand verkeer is 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Geef uw eigen uitgaande openbare IP's of voorvoegsels op

Wanneer u een *Standard* SKU-load balancer gebruikt, maakt het AKS-cluster standaard automatisch een openbaar IP-adres in de resourcegroep van de door AKS beheerde infrastructuur en wijst het deze toe aan de load balancer uitgaande groep.

Een openbaar IP-adres dat door AKS is gemaakt, wordt beschouwd als een door AKS beheerde resource. Dit betekent dat de levenscyclus van dat openbare IP-adres is bedoeld om te worden beheerd door AKS en dat er geen gebruikersactie rechtstreeks op de openbare IP-resource is vereist. U kunt ook uw eigen aangepaste openbare IP of openbaar IP-voorvoegsel toewijzen tijdens het maken van het cluster. Uw aangepaste IP's kunnen ook worden bijgewerkt op basis van de eigenschappen van load balancer cluster.

Vereisten voor het gebruik van uw eigen openbare IP of voorvoegsel:

- Aangepaste openbare IP-adressen moeten worden gemaakt en eigendom zijn van de gebruiker. Beheerde openbare IP-adressen die door AKS zijn gemaakt, kunnen niet opnieuw worden gebruikt als bring your own custom IP, omdat dit beheerconflicten kan veroorzaken.
- U moet ervoor zorgen dat de AKS-clusteridentiteit (service-principal of beheerde identiteit) machtigingen heeft voor toegang tot het uitgaande IP-adres. Volgens de vereiste [lijst met openbare IP-machtigingen.](kubernetes-service-principal.md#networking)
- Zorg ervoor dat u voldoet aan de vereisten en beperkingen die nodig zijn om uitgaande IP-adressen of uitgaande [IP-voorvoegsels](../virtual-network/public-ip-address-prefix.md#constraints) te configureren.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>Het cluster bijwerken met uw eigen uitgaande openbare IP

Gebruik de [opdracht az network public-ip show om][az-network-public-ip-show] de ip-adressen van uw openbare IP-adressen weer te geven.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

De bovenstaande opdracht toont de id voor het openbare IP-adres *myPublicIP* in de resourcegroep *myResourceGroup.*

Gebruik de `az aks update` opdracht met de parameter om uw cluster bij te werken met uw openbare **`load-balancer-outbound-ips`** IP's.

In het volgende voorbeeld wordt de `load-balancer-outbound-ips` parameter gebruikt met de -ID's uit de vorige opdracht.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>Het cluster bijwerken met uw eigen uitgaande openbare IP-voorvoegsel

U kunt ook openbare IP-voorvoegsels gebruiken voor een egress met uw *Standard* SKU-load balancer. In het volgende voorbeeld wordt de [opdracht az network public-ip prefix show][az-network-public-ip-prefix-show] gebruikt om de ID's van uw openbare IP-voorvoegsels weer te geven:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

De bovenstaande opdracht toont de id voor het openbare IP-voorvoegsel *myPublicIPPrefix* in de resourcegroep *myResourceGroup.*

In het volgende voorbeeld wordt de parameter *load-balancer-outbound-ip-prefixes* gebruikt met de ID's uit de vorige opdracht.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>Maak het cluster met uw eigen openbare IP of voorvoegsels

U kunt uw eigen IP-adressen of IP-voorvoegsels gebruiken voor egressie tijdens het maken van het cluster om scenario's te ondersteunen, zoals het toevoegen van eindpunten voor een toegangslijst. Plaats dezelfde parameters die hierboven worden weergegeven bij de stap voor het maken van het cluster om uw eigen openbare IP-adressen en IP-voorvoegsels te definiëren aan het begin van de levenscyclus van een cluster.

Gebruik de *opdracht az aks create* met de parameter *load-balancer-outbound-ips* om aan het begin een nieuw cluster te maken met uw openbare IP-adressen.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Gebruik de *opdracht az aks create* met de parameter *load-balancer-outbound-ip-prefixes* om aan het begin een nieuw cluster te maken met uw openbare IP-voorvoegsels.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>De toegewezen uitgaande poorten configureren

> [!IMPORTANT]
> Als uw cluster toepassingen heeft waarvan wordt verwacht dat ze een groot aantal verbindingen met een kleine set bestemmingen tot stand brengen, bijvoorbeeld veel front-end-exemplaren die verbinding maken met een SQL-database, hebt u een scenario dat zeer vatbaar is voor uitputting van de SNAT-poort (er zijn geen poorten meer om verbinding mee te maken). Voor deze scenario's is het raadzaam om de toegewezen uitgaande poorten en uitgaande front-load balancer. Bij de toename moet rekening worden houden met het feit dat één (1) extra IP-adres 64.000 extra poorten toevoegt om te distribueren over alle clusterknooppunten.


Tenzij anders aangegeven, gebruikt AKS de standaardwaarde toegewezen uitgaande poorten die Standard Load Balancer bij het configureren ervan. Deze waarde is **null in** de AKS API of **0** in de SLB API, zoals wordt weergegeven door de onderstaande opdracht:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Met de vorige opdrachten wordt de uitgaande regel voor uw load balancer vermeld, bijvoorbeeld:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Deze uitvoer betekent niet dat u 0 poorten hebt, maar dat u in plaats daarvan gebruik maakt van de automatische uitgaande poorttoewijzing op basis van de grootte van de [back-endpool.][azure-lb-outbound-preallocatedports]Als een cluster bijvoorbeeld 50 of minder knooppunten heeft, worden er 1024 poorten toegewezen voor elk knooppunt, naarmate u het aantal knooppunten daar verhoogt, krijgt u geleidelijk minder poorten per knooppunt.


Als u het aantal toegewezen uitgaande poorten wilt definiëren of verhogen, kunt u het onderstaande voorbeeld volgen:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

In dit voorbeeld krijgt u 4000 toegewezen uitgaande poorten voor elk knooppunt in mijn cluster en met 7 IP-adressen hebt u 4000 poorten per knooppunt ** 100 knooppunten = 400.000 totale poorten < = totaal 448.000 poorten = 7 IP's * 64k* poorten per IP. Hierdoor kunt u veilig schalen naar 100 knooppunten en een standaardupgradebewerking hebben. Het is essentieel om voldoende poorten toe te wijzen voor extra knooppunten die nodig zijn voor upgraden en andere bewerkingen. AKS wordt standaard ingesteld op één buffer-knooppunt voor een upgrade. In dit voorbeeld zijn voor dit moment 4000 gratis poorten vereist. Als u [maxSstoote-waarden gebruikt,](upgrade-cluster.md#customize-node-surge-upgrade)vermenigvuldigt u de uitgaande poorten per knooppunt met uw maxS exponente-waarde.

Als u veilig meer dan 100 knooppunten wilt gaan, moet u meer IP's toevoegen.


> [!IMPORTANT]
> U moet [het vereiste quotum berekenen en de vereisten][requirements] controleren voordat u *allocatedOutboundPorts* gaat aanpassen om connectiviteits- of schaalproblemen te voorkomen.

U kunt de parameters ook gebruiken bij het maken van een cluster, maar u moet ook **`load-balancer-outbound-ports`** **`load-balancer-managed-outbound-ip-count`** , of **`load-balancer-outbound-ips`** **`load-balancer-outbound-ip-prefixes`** opgeven.  Bijvoorbeeld:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>De time-out load balancer voor inactieve instellingen configureren

Wanneer SNAT-poortbronnen zijn uitgeput, mislukken uitgaande stromen totdat bestaande stromen SNAT-poorten vrijgeven. Load Balancer maakt SNAT-poorten vrij wanneer de stroom wordt gesloten en de voor AKS geconfigureerde load balancer een time-out voor inactieve gegevens van 30 minuten gebruikt voor het vrij maken van SNAT-poorten van niet-actieve stromen.
U kunt ook transport gebruiken (bijvoorbeeld ) of om een niet-actieve stroom te vernieuwen en indien nodig deze time-out voor inactieve gegevens **`TCP keepalives`** **`application-layer keepalives`** opnieuw in te stellen. U kunt deze time-out configureren in het onderstaande voorbeeld: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Als u verwacht een groot aantal kortdende verbindingen te hebben en geen verbindingen met een lange duur en lange tijden van inactief kunnen zijn, zoals het gebruik van een lage time-outwaarde, zoals vier minuten, kunt u overwegen om een lage `kubectl proxy` `kubectl port-forward` time-outwaarde te gebruiken. Wanneer u TCP-keepalives gebruikt, is het ook voldoende om deze aan één zijde van de verbinding in te stellen. Het is bijvoorbeeld voldoende om ze alleen aan de serverzijde in te stellen om de niet-actieve timer van de stroom opnieuw in te stellen en het is niet nodig dat beide zijden TCP-keepalives starten. Er bestaan vergelijkbare concepten voor de toepassingslaag, waaronder client-serverconfiguraties voor databases. Controleer aan de serverzijde welke opties er bestaan voor toepassingsspecifieke keepalives.

> [!IMPORTANT]
> AKS schakelt TCP-opnieuw instellen standaard in bij inactief en raadt u aan deze configuratie aan te houden en deze te gebruiken voor beter voorspelbaar toepassingsgedrag in uw scenario's.
> TCP RST wordt alleen verzonden tijdens de TCP-verbinding met de status ESTABLISHED. [Hier](../load-balancer/load-balancer-tcp-reset.md) vindt u meer informatie.

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>Vereisten voor het aanpassen van toegewezen uitgaande poorten en time-out voor inactieve verkeer

- De waarde die u opgeeft *voor allocatedOutboundPorts* moet ook een veelvoud van 8 zijn.
- U moet voldoende uitgaande IP-capaciteit hebben op basis van het aantal knooppunt-VM's en de vereiste toegewezen uitgaande poorten. Gebruik de volgende formule om te controleren of u voldoende uitgaande IP-capaciteit hebt: 
 
*uitgaande IPS* \* 64.000 \> *nodeVM's* \* *gewenstAllocatedOutboundPorts.*
 
Als u bijvoorbeeld 3 *knooppunt-VM's* en 50.000 *desiredAllocatedOutboundPorts* hebt, moet u ten minste 3 uitgaande *IPs hebben.* Het is raadzaam om meer uitgaande IP-capaciteit op te nemen dan u nodig hebt. Daarnaast moet u rekening houden met de automatische schaalverbetering van clusters en de mogelijkheid om knooppuntgroepupgrades uit te voeren bij het berekenen van de uitgaande IP-capaciteit. Controleer het huidige aantal knooppunt en het maximumaantal knooppunt voor de automatische schaalverdeder voor clusters en gebruik de hogere waarde. Voor het upgraden moet u rekening houden met een extra knooppunt-VM voor elke knooppuntgroep die upgraden toestaat.

- Bij het *instellen van IdleTimeoutInMinutes* op een andere waarde dan de standaardwaarde van 30 minuten, moet u overwegen hoe lang uw workloads een uitgaande verbinding nodig hebben. Houd ook rekening met de  standaard time-outwaarde voor een Standard-SKU die load balancer buiten AKS wordt gebruikt, 4 minuten is. Een *IdleTimeoutInMinutes-waarde* die nauwkeuriger uw specifieke AKS-workload weerspiegelt, kan helpen de SNAT-uitputting te verminderen die wordt veroorzaakt door het verbinden van verbindingen die niet meer worden gebruikt.

> [!WARNING]
> Als u de waarden voor *AllocatedOutboundPorts* en *IdleTimeoutInMinutes* wijzigt, kan het gedrag van de uitgaande regel voor uw load balancer aanzienlijk worden gewijzigd. Als u de afwegingen en verbindingspatronen van uw toepassing niet begrijpt, raadpleegt u de onderstaande sectie Probleemoplossing voor [SNAT][troubleshoot-snat] en bekijkt u de [Load Balancer][azure-lb-outbound-rules-overview] uitgaande regels en uitgaande verbindingen [in Azure][azure-lb-outbound-connections] voordat u deze waarden bijwerkt om de gevolgen van uw wijzigingen volledig te begrijpen.

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Inkomende verkeer beperken tot specifieke IP-adresbereiken

In het volgende manifest wordt *loadBalancerSourceRanges gebruikt om* een nieuw IP-adresbereik op te geven voor inkomende externe verkeer:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

> [!NOTE]
> Inkomende, externe verkeersstromen van de load balancer naar het virtuele netwerk voor uw AKS-cluster. Het virtuele netwerk heeft een netwerkbeveiligingsgroep (NSG) waarmee al het inkomende verkeer van de load balancer. Deze NSG maakt gebruik van [een servicetag][service-tags] van het type *LoadBalancer om* verkeer van het load balancer.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Het IP-adres van de client op binnenkomende verbindingen onderhouden

Standaard zal een service van het type `LoadBalancer` [in Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) en in AKS het IP-adres van de client niet persistent maken voor de verbinding met de pod. Het bron-IP-adres van het pakket dat aan de pod wordt geleverd, is het privé-IP-adres van het knooppunt. Als u het IP-adres van de client wilt behouden, moet u instellen `service.spec.externalTrafficPolicy` op `local` in de servicedefinitie. In het volgende manifest ziet u een voorbeeld:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  externalTrafficPolicy: Local
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="additional-customizations-via-kubernetes-annotations"></a>Aanvullende aanpassingen via Kubernetes-aantekeningen

Hieronder vindt u een lijst met aantekeningen die worden ondersteund voor Kubernetes-services met het type . Deze aantekeningen zijn alleen van toepassing `LoadBalancer` **op INKOMENDE** stromen:

| Aantekening | Waarde | Beschrijving
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` of `false`                     | Geef op of de load balancer intern moet zijn. Deze wordt standaard ingesteld op Openbaar als deze niet is ingesteld.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Naam van het subnet                    | Geef op aan aan welk subnet de load balancer moet worden gebonden. De standaardinstelling is het subnet dat is geconfigureerd in het configuratiebestand van de cloud als dit niet is ingesteld.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | Naam van het DNS-label op openbare IP's   | Geef de DNS-labelnaam voor de **openbare** service op. Als deze is ingesteld op een lege tekenreeks, wordt de DNS-vermelding in het openbare IP-adres niet gebruikt.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` of `false`                     | Geef op dat de service moet worden blootgesteld met behulp van een Azure-beveiligingsregel die kan worden gedeeld met een andere service, en specificiteit van regels verhandelt voor een toename van het aantal services dat kan worden blootgesteld. Deze aantekening is afhankelijk van de Azure [Augmented Security Rules-functie](../virtual-network/network-security-groups-overview.md#augmented-security-rules) van netwerkbeveiligingsgroepen. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Naam van de resourcegroep            | Geef de resourcegroep van load balancer openbare IP's op die zich niet in dezelfde resourcegroep als de clusterinfrastructuur (knooppuntresourcegroep) hebben.
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Lijst met toegestane servicetags          | Geef een lijst met toegestane [servicetags op,][service-tags] gescheiden door komma's.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | Time-outs voor inactieve TCP-tijd in minuten          | Geef de tijd in minuten op voor time-outs voor inactieve TCP-verbindingen op de load balancer. De standaard- en minimumwaarde is 4. De maximumwaarde is 30. Moet een geheel getal zijn.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | Uitschakelen `enableTcpReset` voor SLB


## <a name="troubleshooting-snat"></a>Problemen met SNAT oplossen

Als u weet dat u veel uitgaande TCP- of UDP-verbindingen naar hetzelfde doel-IP-adres en dezelfde poort start en u merkt dat uitgaande verbindingen mislukken of door ondersteuning wordt geadviseerd dat u SNAT-poorten (vooraf toegewezen kortstondige poorten die worden gebruikt door PAT) uitput, hebt u verschillende algemene oplossingsopties. Bekijk deze opties en bepaal wat beschikbaar en het beste is voor uw scenario. Het is mogelijk dat een of meer u kunnen helpen bij het beheren van dit scenario. Bekijk de gids voor het oplossen [van problemen met uitgaande verbindingen voor gedetailleerde informatie.](../load-balancer/troubleshoot-outbound-connection.md)

De belangrijkste oorzaak van SNAT-uitputting is vaak een antipatroon voor de manier waarop uitgaande verbindingen tot stand worden gebracht, worden beheerd of de standaardwaarden van configureerbare timers worden gewijzigd. Lees deze sectie aandachtig.

### <a name="steps"></a>Stappen
1. Controleer of uw verbindingen lange tijd inactief blijven en vertrouw op de standaard time-out voor inactieve gegevens voor het vrijgeven van die poort. Als dit het geval is, moet de standaard time-out van 30 min mogelijk worden verlaagd voor uw scenario.
2. Ga na hoe uw app uitgaande verbindingen maakt (bijvoorbeeld door het bekijken van de code of door pakketregistratie).
3. Bepaal of deze activiteit verwacht gedrag vertoont of dat de app niet goed werkt. Gebruik [metrische](../load-balancer/load-balancer-standard-diagnostics.md) [gegevens en logboeken](../load-balancer/load-balancer-monitor-log.md) in Azure Monitor om uw bevindingen te onderbouwen. Gebruik bijvoorbeeld de categorie Mislukt voor de metrische gegevens van SNAT-verbindingen.
4. Evalueer of de juiste [patronen](#design-patterns) worden gevolgd.
5. Evalueer of SNAT-poortuitputting moet worden beperkt met extra uitgaande [IP-adressen + extra toegewezen uitgaande poorten.](#configure-the-allocated-outbound-ports)

### <a name="design-patterns"></a>Ontwerppatronen
Gebruik waar mogelijk verbindingen opnieuw en maak gebruik van verbindingspools. Deze patronen voorkomen dat resources worden uitgeput en leiden tot voorspelbaar gedrag. Primitieven voor deze patronen vindt u in veel ontwikkelbibliotheken en frameworks.

- Atomische aanvragen (één aanvraag per verbinding) zijn doorgaans geen goede ontwerpkeuze. Een dergelijk antipatroon beperkt de schaal, verlaagt de prestaties en vermindert de betrouwbaarheid. Hergebruik in plaats daarvan HTTP/S-verbindingen om het aantal verbindingen en de bijbehorende SNAT-poorten te verminderen. De schaal van de toepassing neemt toe en de prestaties worden verbeterd vanwege minder handshakes, overhead en cryptografische bewerkingskosten bij het gebruik van TLS.
- Als u buiten het cluster/aangepaste DNS gebruikt, of als u aangepaste upstream-servers op coreDNS gebruikt, moet u er rekening mee houden dat DNS veel afzonderlijke stromen op volume kan introduceren wanneer de client het resultaat van DNS-resolvers niet in de caching opgeslagen. Zorg ervoor dat u eerst coreDNS aan te passen in plaats van aangepaste DNS-servers en definieer een goede caching-waarde.
- UDP-stromen (bijvoorbeeld DNS-zoekacties) wijzen SNAT-poorten toe voor de duur van de time-out voor inactiviteit. Hoe langer de time-out voor inactiviteit, hoe hoger de druk op de SNAT-poorten. Gebruik een korte duur voor de time-out voor inactiviteit (bijvoorbeeld 4 minuten).
Gebruik verbindingspools om vorm te geven aan het volume van uw verbindingen.
- Verlaat nooit zo maar een TCP-stroom en maak gebruik van TCP-timers om de stroom op te schonen. Als u de verbinding niet expliciet door TCP laat sluiten, blijft de status op Toegewezen staan bij tussenliggende systemen en eindpunten en zijn er geen SNAT-poorten beschikbaar voor andere verbindingen. Dit patroon kan fouten in de app en SNAT-uitputting veroorzaken.
- Wijzig timerwaarden voor het sluiten door TCP op besturingssysteemniveau alleen als u uitgebreide kennis hebt van de gevolgen hiervan. Terwijl de TCP-stack wordt hersteld, kunnen de prestaties van uw toepassing negatief worden beïnvloed wanneer de eindpunten van een verbinding niet overeenkomende verwachtingen hebben. Het wijzigen van timers is meestal een teken van een onderliggend ontwerpprobleem. Bestudeer de volgende aanbevelingen.


In het bovenstaande voorbeeld wordt de regel zo bijgewerkt dat alleen inkomende externe verkeer van *het* MY_EXTERNAL_IP_RANGE is toegestaan. Als u deze *MY_EXTERNAL_IP_RANGE* door het interne IP-adres van het subnet, wordt het verkeer beperkt tot alleen interne IP-adressen van clusters. Hierdoor hebben clients van buiten uw Kubernetes-cluster geen toegang tot de load balancer.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Over van een basis-SKU-load balancer naar standaard-SKU

Als u een bestaand cluster met de Basic SKU-Load Balancer hebt, zijn er belangrijke gedragsverschillen die u moet noteren wanneer u migreert om een cluster te gebruiken met de Standard SKU-Load Balancer.

Het maken van blauwe/groene implementaties voor het migreren van clusters is bijvoorbeeld een veelgebruikte praktijk, omdat het type cluster alleen kan worden gedefinieerd tijdens het maken van `load-balancer-sku` het cluster. Basic *SKU* Load Balancers maken echter gebruik van BASIC *SKU* IP-adressen, die niet compatibel zijn met *Standard SKU* Load Balancers, omdat deze IP-adressen van *standard-SKU's* nodig hebben. Wanneer u clusters migreert voor het upgraden Load Balancer SKU's, is een nieuw IP-adres met een compatibele IP-adres-SKU vereist.

Voor meer overwegingen over het [](aks-migration.md) migreren van clusters, gaat u naar onze documentatie over migratieoverwegingen voor een lijst met belangrijke onderwerpen die u kunt overwegen bij de migratie. De onderstaande beperkingen zijn ook belangrijke gedragsverschillen die u moet noteren bij het gebruik van Standard SKU Load Balancers in AKS.

## <a name="limitations"></a>Beperkingen

De volgende beperkingen gelden wanneer u AKS-clusters maakt en  beheert die ondersteuning bieden load balancer met de Standard-SKU:

* Er is ten minste één openbaar IP- of IP-voorvoegsel vereist voor het toestaan van het verkeer van het AKS-cluster. Het openbare IP- of IP-voorvoegsel is ook vereist voor het onderhouden van connectiviteit tussen de besturingsvlak- en agentknooppunten en om compatibiliteit met eerdere versies van AKS te behouden. U hebt de volgende opties voor het opgeven van openbare IP-adressen of IP-voorvoegsels met een *Standard* SKU-load balancer:
    * Geef uw eigen openbare IP's op.
    * Geef uw eigen openbare IP-voorvoegsels op.
    * Geef een getal van maximaal 100 op zodat  het AKS-cluster zoveel openbare IP's van standard-SKU's kan maken in dezelfde resourcegroep die is gemaakt als het AKS-cluster, dat meestal aan het begin *MC_* heet. AKS wijst het openbare IP-adres toe aan de *Standard* SKU-load balancer. Standaard wordt automatisch één openbaar IP-adres gemaakt in dezelfde resourcegroep als het AKS-cluster als er geen openbaar IP-adres, openbaar IP-voorvoegsel of aantal IP-adressen is opgegeven. U moet ook openbare adressen toestaan en voorkomen dat er een Azure Policy die het maken van IP-adressen verbiedt.
* Een openbaar IP-adres dat door AKS is gemaakt, kan niet opnieuw worden gebruikt als een aangepast Bring Your Own Public IP-adres. Alle aangepaste IP-adressen moeten worden gemaakt en beheerd door de gebruiker.
* Het definiëren van load balancer SKU kan alleen worden uitgevoerd wanneer u een AKS-cluster maakt. U kunt de SKU van load balancer niet wijzigen nadat een AKS-cluster is gemaakt.
* U kunt slechts één type SKU load balancer (Basic of Standard) in één cluster.
* *Standard* SKU Load Balancers ondersteunen alleen *IP-adressen van* Standard-SKU's.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over Kubernetes-services vindt u in de [Kubernetes Services-documentatie.][kubernetes-services]

Meer informatie over het gebruik van interne Load Balancer voor inkomende verkeer kunt u vinden in de [documentatie voor interne AKS-Load Balancer.](internal-lb.md)

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule#az_network_lb_outbound_rule_list
[az-network-public-ip-show]: /cli/azure/network/public-ip#az_network_public_ip_show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/skus.md
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-connections.md#outboundrules
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
