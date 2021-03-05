---
title: Een open bare Load Balancer gebruiken
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het gebruik van een open bare load balancer met een standaard-SKU om uw services beschikbaar te maken met Azure Kubernetes service (AKS).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: e37c5a748a8e99f49e3535946268427139bbbf44
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184420"
---
# <a name="use-a-public-standard-load-balancer-in-azure-kubernetes-service-aks"></a>Een open bare Standard Load Balancer gebruiken in azure Kubernetes service (AKS)

Het Azure Load Balancer bevindt zich op N4 van het OSI-model (Open Systems Interconnect) dat zowel binnenkomende als uitgaande scenario's ondersteunt. Hiermee worden inkomende stromen gedistribueerd die aan de front-end van de load balancer komen voor de back-endadresgroep.

Een **open bare** Load Balancer in combi natie met AKS heeft twee doel einden:

1. Om uitgaande verbindingen te bieden met de cluster knooppunten in het virtuele AKS-netwerk. Deze doel stelling wordt bereikt door het privé IP-adres van knoop punten te vertalen naar een openbaar IP-adres dat deel uitmaakt van de *uitgaande groep*.
2. Om toegang te bieden tot toepassingen via Kubernetes services van het type `LoadBalancer` . Met IT kunt u uw toepassingen eenvoudig schalen en Maxi maal beschik bare Services maken.

Een **intern (of privé)** Load Balancer wordt gebruikt wanneer alleen privé ip's zijn toegestaan als frontend. Interne load balancers worden gebruikt voor het verdelen van verkeer binnen een virtueel netwerk. Een load balancer frontend kan ook worden geopend vanuit een on-premises netwerk in een hybride scenario.

In dit document wordt de integratie met de open bare Load Balancer beschreven. Zie de [AKS Internal Load Balancer-documentatie](internal-lb.md)voor interne integratie van Load Balancer.

## <a name="before-you-begin"></a>Voordat u begint

Azure Load Balancer is beschikbaar in twee Sku's: *Basic* en *Standard*. *Standaard-SKU* wordt gebruikt wanneer u een AKS-cluster maakt. Gebruik de *standaard* -SKU om toegang te hebben tot extra functionaliteit, zoals een grotere back-end-groep, [**meerdere knooppunt groepen**](use-multiple-node-pools.md)en [**Beschikbaarheidszones**](availability-zones.md). Het is de aanbevolen Load Balancer SKU voor AKS.

Zie voor meer informatie over de *Basic* -en *Standard* -sku's [Azure Load Balancer SKU-vergelijking][azure-lb-comparison].

In dit artikel wordt ervan uitgegaan dat u een AKS-cluster hebt met de *standaard* SKU Azure Load Balancer en wordt uitgelegd hoe u een aantal van de mogelijkheden en functies van de Load Balancer gebruikt en configureert. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Als u liever geen gebruik hoeft te maken van de Azure Load Balancer om een uitgaande verbinding te maken en u in plaats daarvan uw eigen gateway, firewall of proxy voor dat doel hebt, kunt u de aanmaak van de load balancer uitgaande groep en de respectieve frontend-IP overs laan met behulp van [**uitgaand type als UserDefinedRouting (UDR)**](egress-outboundtype.md). Het uitgaande type definieert de uitvoerings methode voor een cluster en het standaard type is: load balancer.

## <a name="use-the-public-standard-load-balancer"></a>De open bare standaard load balancer gebruiken

Na het maken van een AKS-cluster met een uitgaand type: Load Balancer (standaard), is het cluster klaar voor gebruik van de load balancer om ook services weer te geven.

U kunt een open bare service van het type maken `LoadBalancer` , zoals wordt weer gegeven in het volgende voor beeld. Maak eerst een service manifest met de naam `public-svc.yaml` :

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

Implementeer het open bare service manifest met behulp van [kubectl apply][kubectl-apply] en geef de naam van uw yaml-manifest op:

```azurecli-interactive
kubectl apply -f public-svc.yaml
```

De Azure Load Balancer wordt geconfigureerd met een nieuw openbaar IP-adres voor deze nieuwe service. Omdat de Azure Load Balancer meerdere frontend-Ip's kan hebben, krijgt elke nieuwe service een nieuw toegewezen frontend-IP-adres dat uniek kan worden geopend.

U kunt controleren of uw service is gemaakt en de load balancer is geconfigureerd door het volgende uit te voeren:

```azurecli-interactive
kubectl get service public-svc
```

```console
NAMESPACE     NAME          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
default       public-svc    LoadBalancer   10.0.39.110    52.156.88.187   80:32068/TCP    52s
```

Wanneer u de service details bekijkt, wordt het open bare IP-adres dat is gemaakt voor deze service op de load balancer weer gegeven in de kolom *extern-IP* . Het kan een paar minuten duren voordat het IP-adres is gewijzigd van *\<pending\>* naar een werkelijk openbaar IP-adres, zoals wordt weer gegeven in het bovenstaande voor beeld.

## <a name="configure-the-public-standard-load-balancer"></a>De open bare standaard load balancer configureren

Wanneer u de open bare load balancer van de standaard-SKU gebruikt, is er een set opties die kan worden aangepast tijdens de aanmaak tijd of door het cluster bij te werken. Met deze opties kunt u de Load Balancer aanpassen zodat deze voldoet aan de behoeften van uw werk belastingen en moeten ze dienovereenkomstig worden gecontroleerd. Met de standaard load balancer kunt u het volgende doen:

* Het aantal beheerde uitgaande Ip's instellen of schalen
* Uw eigen aangepaste [IP-adres of voor voegsel voor uitgaand](#provide-your-own-outbound-public-ips-or-prefixes) verkeer
* Het aantal toegewezen uitgaande poorten aanpassen aan elk knoop punt van het cluster
* De time-outinstelling voor niet-actieve verbindingen configureren

> [!IMPORTANT]
> Er kan slechts één uitgaande IP-optie (beheerde Ip's, uw eigen IP-adres of IP-voor voegsel) op een bepaald moment worden gebruikt.

### <a name="scale-the-number-of-managed-outbound-public-ips"></a>Het aantal beheerde uitgaande open bare Ip's schalen

Azure Load Balancer biedt een uitgaande verbinding van een virtueel netwerk naast binnenkomend. Met uitgaande regels kunt u de uitgaande Network Address Translation van open bare Standard Load Balancer eenvoudig configureren.

Net als alle Load Balancer regels volgen uitgaande regels dezelfde vertrouwde syntaxis als taak verdeling en binnenkomende NAT-regels:

***frontend-Ip's + para meters + back-end-pool***

Met een uitgaande regel wordt uitgaande NAT geconfigureerd voor alle virtuele machines die door de back-endadresgroep worden geïdentificeerd om te worden vertaald naar de front-end. En para meters bieden extra nauw keurige controle over de uitgaande NAT-algoritme.

Terwijl een regel voor uitgaande verbindingen met slechts één openbaar IP-adres kan worden gebruikt, wordt door uitgaande regels de configuratie belasting voor het schalen van uitgaande NAT vereenvoudigd. U kunt meerdere IP-adressen gebruiken om te plannen voor grootschalige scenario's en u kunt uitgaande regels gebruiken om te voor komen dat er gevoelige patronen voor de uitputting van SNAT zijn. Elk extra IP-adres dat wordt geleverd door een front-end biedt 64 KB tijdelijke poorten voor Load Balancer die als SNAT-poorten kunnen worden gebruikt. 

Wanneer u een *standaard* -SKU gebruikt Load Balancer met beheerde uitgaande open bare ip's, die standaard worden gemaakt, kunt u het aantal beheerde uitgaande open bare ip's schalen met behulp van de **`load-balancer-managed-ip-count`** para meter.

Voer de volgende opdracht uit om een bestaand cluster bij te werken. Deze para meter kan ook op een cluster worden ingesteld voor het maken van meerdere beheerde open bare IP-adressen.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

In het bovenstaande voor beeld wordt het aantal beheerde uitgaande open bare Ip's ingesteld op *2* voor het *myAKSCluster* -cluster in *myResourceGroup*. 

U kunt ook de **`load-balancer-managed-ip-count`** para meter gebruiken om het eerste aantal beheerde uitgaande open bare ip's in te stellen bij het maken van het cluster door de **`--load-balancer-managed-outbound-ip-count`** para meter toe te voegen en de gewenste waarde in te stellen. Het standaard aantal beheerde uitgaande open bare Ip's is 1.

### <a name="provide-your-own-outbound-public-ips-or-prefixes"></a>Uw eigen uitgaande open bare Ip's of voor voegsels opgeven

Wanneer u een *standaard* -SKU Load Balancer gebruikt, maakt het AKS-cluster standaard automatisch een openbaar IP-adres in de resource groep voor de door aks beheerde infra structuur en wijst deze toe aan de Load Balancer uitgaande groep.

Een openbaar IP-adres dat door AKS is gemaakt, wordt beschouwd als een door AKS beheerde resource. Dit betekent dat de levens cyclus van het open bare IP-adres is bedoeld om te worden beheerd door AKS en vereist geen gebruikers actie rechtstreeks op de open bare IP-resource. U kunt ook uw eigen aangepaste open bare IP-adres of openbaar IP-voor voegsel toewijzen tijdens het maken van het cluster. Uw aangepaste Ip's kunnen ook worden bijgewerkt op de load balancer eigenschappen van een bestaand cluster.

Vereisten voor het gebruik van uw eigen open bare IP of voor voegsel:

- Aangepaste open bare IP-adressen moeten worden gemaakt en het eigendom zijn van de gebruiker. Beheerde open bare IP-adressen die door AKS worden gemaakt, kunnen niet opnieuw worden gebruikt als een eigen aangepast IP-adres, omdat het beheer conflicten kan veroorzaken.
- U moet ervoor zorgen dat de AKS-cluster identiteit (Service-Principal of beheerde identiteit) machtigingen heeft voor toegang tot het uitgaande IP-adres. Volgens de [vereiste lijst met open bare IP-machtigingen](kubernetes-service-principal.md#networking).
- Zorg ervoor dat u voldoet aan de [vereisten en beperkingen](../virtual-network/public-ip-address-prefix.md#constraints) die nodig zijn voor het configureren van uitgaande Ip's of uitgaande IP-voor voegsels.

#### <a name="update-the-cluster-with-your-own-outbound-public-ip"></a>Het cluster bijwerken met uw eigen uitgaande open bare IP-adres

Gebruik de opdracht [AZ Network public-ip show][az-network-public-ip-show] om de id's van uw open bare ip's weer te geven.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

De bovenstaande opdracht toont de ID voor het open bare IP-adres *myPublicIP* in de resource groep *myResourceGroup* .

Gebruik de `az aks update` opdracht met de **`load-balancer-outbound-ips`** para meter om uw cluster bij te werken met uw open bare ip's.

In het volgende voor beeld wordt de `load-balancer-outbound-ips` para meter gebruikt met de id's van de vorige opdracht.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

#### <a name="update-the-cluster-with-your-own-outbound-public-ip-prefix"></a>Het cluster bijwerken met uw eigen uitgaande open bare IP-voor voegsel

U kunt ook open bare IP-voor voegsels gebruiken om uit te voeren met uw *standaard* SKU-Load Balancer. In het volgende voor beeld wordt de opdracht [AZ Network Public-IP prefix show][az-network-public-ip-prefix-show] gebruikt om de id's van uw open bare IP-voor voegsels weer te geven:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

De bovenstaande opdracht toont de ID voor het open bare IP-voor voegsel *myPublicIPPrefix* in de resource groep *myResourceGroup* .

In het volgende voor beeld wordt de para meter *Load Balancer-uitgaand-IP-voor voegsels* gebruikt met de id's van de vorige opdracht.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

#### <a name="create-the-cluster-with-your-own-public-ip-or-prefixes"></a>Het cluster maken met uw eigen open bare IP-adres of voor voegsels

U kunt uw eigen IP-adressen of IP-voor voegsels maken voor uitgaand verkeer tijdens het maken van het cluster ter ondersteuning van scenario's zoals het toevoegen van uitstaande eind punten aan een acceptatie lijst. Voeg de hierboven weer gegeven para meters toe aan de stap voor het maken van het cluster om uw eigen open bare Ip's en IP-voor voegsels te definiëren aan het begin van de levens cyclus van een cluster.

Gebruik de opdracht *AZ AKS Create* met de para meter *Load-Balancer-outbound-ip's* om aan het begin een nieuw cluster met uw open bare ip's te maken.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Gebruik de opdracht *AZ AKS Create* met de para meter *Load-Balancer-uitgaand-IP-voor voegsels* om een nieuw cluster te maken met uw open bare IP-voor voegsels aan het begin.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

### <a name="configure-the-allocated-outbound-ports"></a>De toegewezen uitgaande poorten configureren

> [!IMPORTANT]
> Als u toepassingen op uw cluster hebt die naar verwachting een groot aantal verbindingen tot stand brengen met een kleine set doelen, bijvoorbeeld. veel frontend-exemplaren die verbinding maken met een SQL-data base, hebt u een scenario dat kan leiden tot een SNAT-poort uitgeput (waardoor er geen poorten meer zijn om verbinding te maken). Voor deze scenario's wordt het ten zeerste aanbevolen om de toegewezen uitgaande poorten en uitgaande frontend-IP-adressen te verg Roten op de load balancer. De toename moet er rekening mee houden dat een (1) extra IP-adres 64 kB extra poorten toevoegt voor distributie over alle cluster knooppunten.


Tenzij anders aangegeven, gebruikt AKS de standaard waarde van toegewezen uitgaande poorten die Standard Load Balancer definieert bij de configuratie. Deze waarde is **Null** voor de AKS-API of **0** op de SLB API, zoals wordt weer gegeven in de onderstaande opdracht:

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

Met de vorige opdrachten wordt de uitgaande regel voor uw load balancer weer geven, bijvoorbeeld:

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

Deze uitvoer betekent niet dat u 0 poorten hebt, maar in plaats daarvan de automatische toewijzing van de poort te gebruiken op [basis van de back-endadresgroep][azure-lb-outbound-preallocatedports]. als een cluster bijvoorbeeld 50 of minder knoop punten heeft, worden er voor elk knoop punt 1024 poorten toegewezen, wanneer u het aantal knoop punten van daaruit verhoogt, worden er geleidelijk minder poorten per knoop punt ontvangen.


Als u het aantal toegewezen uitgaande poorten wilt definiëren of verhogen, kunt u het onderstaande voor beeld volgen:


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 7 \
    --load-balancer-outbound-ports 4000
```

Dit voor beeld geeft u 4000 toegewezen uitgaande poorten voor elk knoop punt in mijn cluster, en met 7 *4000 ip's per knoop punt * 100 knoop punten = 400k totale aantal poorten < = 448k totaal aantal poorten = 7 ip's * 64k poorten per IP-adres*. Hierdoor kunt u veilig schalen naar 100-knoop punten en een standaard upgrade bewerking uitvoeren. Het is essentieel dat u voldoende poorten toewijst voor extra knoop punten die nodig zijn voor de upgrade en andere bewerkingen. AKS is standaard ingesteld op één buffer knooppunt voor de upgrade. in dit voor beeld is het 4000 vrije poorten op een wille keurig moment. Als u [maxSurge-waarden](upgrade-cluster.md#customize-node-surge-upgrade)gebruikt, vermenigvuldigt u de uitgaande poorten per knoop punt met uw maxSurge-waarde.

Als u meer dan 100 knoop punten veilig wilt gaan, moet u meer Ip's toevoegen.


> [!IMPORTANT]
> U moet [uw vereiste quotum berekenen en de vereisten controleren][requirements] voordat u *allocatedOutboundPorts* aanpast om connectiviteits-of schaal problemen te voor komen.

U kunt ook de **`load-balancer-outbound-ports`** para meters gebruiken bij het maken van een cluster, maar u moet ook ofwel **`load-balancer-managed-outbound-ip-count`** of opgeven **`load-balancer-outbound-ips`** **`load-balancer-outbound-ip-prefixes`** .  Bijvoorbeeld:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku standard \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 1024 
```

### <a name="configure-the-load-balancer-idle-timeout"></a>De load balancer time-out voor inactiviteit configureren

Wanneer de SNAT-poort resources zijn uitgeput, mislukken uitgaande stromen totdat bestaande stromen van SNAT-poorten worden vrijgegeven. Load Balancer de SNAT-poorten worden hersteld wanneer de stroom wordt gesloten en de door AKS geconfigureerde load balancer een time-out van 30 minuten voor het vrijmaken van de SNAT-poorten van niet-actieve stromen.
U kunt ook Trans Port (bijvoorbeeld) gebruiken **`TCP keepalives`** of **`application-layer keepalives`** een inactieve stroom vernieuwen en deze time-out voor inactiviteit indien nodig opnieuw instellen. U kunt deze time-out configureren volgens het onderstaande voor beeld: 


```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-idle-timeout 4
```

Als u verwacht dat er veel korte verbindingen worden uitgevoerd, en geen verbindingen die lang worden bewaard en mogelijk lange tijd in beslag nemen, `kubectl proxy` zoals `kubectl port-forward` bij het gebruik of overweegt u een lage time-outwaarde, bijvoorbeeld 4 minuten, te gebruiken. Wanneer u TCP-keepalives gebruikt, is het ook voldoende om deze in te scha kelen aan één zijde van de verbinding. Het is bijvoorbeeld voldoende om ze alleen in te scha kelen op de server om de niet-actieve timer van de stroom opnieuw in te stellen en het is niet nodig voor beide zijden om TCP-keepalives te starten. Er bestaan soort gelijke concepten voor Application Layer, waaronder client-server configuraties voor data bases. Controleer de server zijde voor welke opties bestaan voor toepassingsspecifieke keepalives.

> [!IMPORTANT]
> AKS schakelt TCP reset in op inactief standaard en raadt u aan deze configuratie te blijven gebruiken voor meer voorspel bare toepassings gedrag in uw scenario's.
> EERSTE TCP wordt alleen verzonden tijdens de TCP-verbinding met de status ESTABLISHed. [Hier](../load-balancer/load-balancer-tcp-reset.md) vindt u meer informatie.

### <a name="requirements-for-customizing-allocated-outbound-ports-and-idle-timeout"></a>Vereisten voor het aanpassen van toegewezen uitgaande poorten en time-out voor inactiviteit

- De waarde die u opgeeft voor *allocatedOutboundPorts* moet ook een meervoud van 8 zijn.
- U moet voldoende uitgaande IP-capaciteit hebben op basis van het aantal knoop punt-Vm's en de vereiste toegewezen uitgaande poorten. Gebruik de volgende formule om te controleren of u voldoende uitgaande IP-capaciteit hebt: 
 
*outboundIPs* \* 64.000 \> *nodeVMs* \* *desiredAllocatedOutboundPorts*.
 
Als u bijvoorbeeld drie *nodeVMs* en 50.000 *desiredAllocatedOutboundPorts* hebt, moet u Mini maal drie *outboundIPs* hebben. Het wordt aanbevolen dat u extra uitgaande IP-capaciteit opneemt dan wat u nodig hebt. Daarnaast moet u de cluster automatisch schalen en de mogelijkheid van upgrades van knooppunt groepen voor het berekenen van de uitgaande IP-capaciteit. Bekijk het huidige aantal knoop punten en het maximum aantal knoop punten en gebruik de hogere waarde voor het cluster automatisch schalen. Voor het uitvoeren van een upgrade moet u een extra VM-knoop punt voor elke knooppunt groep waarmee een upgrade kan worden uitgevoerd.

- Wanneer u *IdleTimeoutInMinutes* instelt op een andere waarde dan de standaard instelling van 30 minuten, kunt u overwegen hoe lang uw workloads een uitgaande verbinding nodig hebben. Houd ook rekening met de standaardtime-outwaarde voor een *standaard* -SKU Load Balancer die buiten AKS wordt gebruikt, is 4 minuten. Een *IdleTimeoutInMinutes* -waarde die uw specifieke AKS-werk belasting nauw keuriger maakt, kan de SNAT-uitputting afnemen als gevolg van het koppelen van verbindingen die niet meer worden gebruikt.

> [!WARNING]
> Het wijzigen van de waarden voor *AllocatedOutboundPorts* en *IdleTimeoutInMinutes* kunnen het gedrag van de uitgaande regel voor uw Load Balancer aanzienlijk wijzigen en moet niet lichter worden uitgevoerd, zonder inzicht te krijgen in de afwegingen en verbindings patronen van uw toepassing. Controleer de [onderstaande sectie over het oplossen van problemen][troubleshoot-snat] en controleer de [Load Balancer uitgaande regels][azure-lb-outbound-rules-overview] en [uitgaande verbindingen in azure][azure-lb-outbound-connections] voordat u deze waarden bijwerkt.

## <a name="restrict-inbound-traffic-to-specific-ip-ranges"></a>Inkomend verkeer beperken tot specifieke IP-bereiken

In het volgende manifest wordt *loadBalancerSourceRanges* gebruikt om een nieuw IP-adres bereik op te geven voor binnenkomend extern verkeer:

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
> Binnenkomend, extern verkeer loopt van de load balancer naar het virtuele netwerk van uw AKS-cluster. Het virtuele netwerk heeft een netwerk beveiligings groep (NSG) waarmee al het inkomende verkeer van de load balancer wordt toegestaan. In deze NSG wordt [een servicetag][service-tags] van het type *Load Balancer* gebruikt om verkeer van de Load Balancer toe te staan.

## <a name="maintain-the-clients-ip-on-inbound-connections"></a>Het IP-adres van de client voor binnenkomende verbindingen onderhouden

Standaard zal een service van het type `LoadBalancer` [in Kubernetes](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer) en in AKS niet het IP-adres van de client behouden op de verbinding met de pod. Het bron-IP-adres van het pakket dat aan de Pod wordt geleverd, is het privé-IP-adres van het knoop punt. Als u het IP-adres van de client wilt behouden, moet u deze instellen `service.spec.externalTrafficPolicy` `local` in de service definitie. In het volgende manifest wordt een voor beeld weer gegeven:

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

## <a name="additional-customizations-via-kubernetes-annotations"></a>Aanvullende aanpassingen via Kubernetes annotaties

Hieronder vindt u een lijst met annotaties die worden ondersteund voor Kubernetes-Services met type `LoadBalancer` , deze aantekeningen zijn alleen van toepassing op **binnenkomende** stromen:

| Aantekening | Waarde | Beschrijving
| ----------------------------------------------------------------- | ------------------------------------- | ------------------------------------------------------------ 
| `service.beta.kubernetes.io/azure-load-balancer-internal`         | `true` of `false`                     | Geef op of de load balancer intern moet zijn. Het is standaard openbaar als dit niet is ingesteld.
| `service.beta.kubernetes.io/azure-load-balancer-internal-subnet`  | Naam van het subnet                    | Geef op met welk subnet de interne load balancer moet worden gebonden. Het is standaard ingesteld op het subnet dat is geconfigureerd in het Cloud configuratie bestand als dit niet is ingeschakeld.
| `service.beta.kubernetes.io/azure-dns-label-name`                 | Naam van het DNS-label op open bare Ip's   | Geef de DNS-label naam voor de **open bare** service op. Als deze is ingesteld op een lege teken reeks, wordt de DNS-vermelding in het open bare IP-adres niet gebruikt.
| `service.beta.kubernetes.io/azure-shared-securityrule`            | `true` of `false`                     | Geef op dat de service moet worden weer gegeven met een Azure-beveiligings regel die kan worden gedeeld met een andere service, handels specificiteit van regels voor een toename van het aantal services dat kan worden blootgesteld. Deze aantekening is afhankelijk van de functie uitgebreide [beveiligings regels](../virtual-network/network-security-groups-overview.md#augmented-security-rules) van netwerk beveiligings groepen van Azure. 
| `service.beta.kubernetes.io/azure-load-balancer-resource-group`   | Naam van de resource groep            | Geef de resource groep van load balancer open bare IP-adressen op die zich niet in dezelfde resource groep bevinden als de cluster infrastructuur (knooppunt resource groep).
| `service.beta.kubernetes.io/azure-allowed-service-tags`           | Lijst met toegestane service Tags          | Geef een lijst met toegestane [service Tags][service-tags] op, gescheiden door komma's.
| `service.beta.kubernetes.io/azure-load-balancer-tcp-idle-timeout` | Time-outs voor TCP-inactiviteit in minuten          | Geef op hoelang, in minuten, de time-outs voor de TCP-verbinding moeten worden uitgevoerd op het load balancer. De standaard waarde is 4. De maximum waarde is 30. Moet een geheel getal zijn.
|`service.beta.kubernetes.io/azure-load-balancer-disable-tcp-reset` | `true`                                | Uitschakelen `enableTcpReset` voor SLB


## <a name="troubleshooting-snat"></a>Problemen met SNAT oplossen

Als u weet dat u veel uitgaande TCP-of UDP-verbindingen naar hetzelfde doel-IP-adres en dezelfde poort start, en u ziet dat uitgaande verbindingen mislukken of worden aanbevolen door de ondersteuning van de SNAT-poorten (vooraf toegewezen tijdelijke poorten die door PAT worden gebruikt), hebt u verschillende algemene beperkende opties. Bekijk deze opties en beslis wat er beschikbaar en beste is voor uw scenario. Het is mogelijk dat een of meer informatie kan helpen bij het beheren van dit scenario. Raadpleeg de hand leiding voor het [oplossen van problemen met uitgaande verbindingen](../load-balancer/troubleshoot-outbound-connection.md)voor meer informatie.

De belangrijkste oorzaak van SNAT-uitputting is vaak een antipatroon voor de manier waarop uitgaande verbindingen tot stand worden gebracht, worden beheerd of de standaardwaarden van configureerbare timers worden gewijzigd. Lees deze sectie aandachtig.

### <a name="steps"></a>Stappen
1. Controleer of uw verbindingen lange tijd inactief blijven en afhankelijk zijn van de standaard time-out voor inactiviteit voor het vrijgeven van die poort. Als dit het geval is, moet de standaard time-out van 30 minuten voor uw scenario wellicht worden verminderd.
2. Ga na hoe uw app uitgaande verbindingen maakt (bijvoorbeeld door het bekijken van de code of door pakketregistratie).
3. Bepaal of deze activiteit verwacht gedrag vertoont of dat de app niet goed werkt. Gebruik [metrische gegevens](../load-balancer/load-balancer-standard-diagnostics.md) en [logboeken](../load-balancer/load-balancer-monitor-log.md) in azure monitor om uw bevindingen te bewijzen. Gebruik de categorie ' mislukt ' voor de metrische gegevens voor de SNAT-verbinding.
4. Evalueren of de juiste [patronen](#design-patterns) worden gevolgd.
5. Evalueren of de belasting van de SNAT-poort moet worden verminderd met [extra uitgaande IP-adressen + extra toegewezen uitgaande poorten](#configure-the-allocated-outbound-ports) .

### <a name="design-patterns"></a>Ontwerppatronen
Gebruik waar mogelijk verbindingen opnieuw en maak gebruik van verbindingspools. Deze patronen voorkomen dat resources worden uitgeput en leiden tot voorspelbaar gedrag. Primitieven voor deze patronen vindt u in veel ontwikkelbibliotheken en frameworks.

- Atomische aanvragen (één aanvraag per verbinding) zijn over het algemeen geen goede ontwerp keuze. Een dergelijk antipatroon beperkt de schaal, verlaagt de prestaties en vermindert de betrouwbaarheid. Hergebruik in plaats daarvan HTTP/S-verbindingen om het aantal verbindingen en de bijbehorende SNAT-poorten te verminderen. De schaal van de toepassing neemt toe en verbetert de prestaties omdat er minder Handshakes, overhead en cryptografische bewerkings kosten zijn bij het gebruik van TLS.
- Als u gebruikmaakt van niet-geclusterde DNS of aangepaste upstream-servers op coreDNS, moet u er rekening mee houdt dat DNS veel afzonderlijke stromen op volume kan introduceren wanneer de client het resultaat van de DNS-resolver niet in de cache opslaat. Zorg ervoor dat u coreDNS eerst aanpast in plaats van aangepaste DNS-servers en een goede cache waarde definieert.
- UDP-stromen (bijvoorbeeld DNS-zoekacties) wijzen SNAT-poorten toe voor de duur van de time-out voor inactiviteit. Hoe langer de time-out voor inactiviteit, hoe hoger de druk op de SNAT-poorten. Gebruik een korte duur voor de time-out voor inactiviteit (bijvoorbeeld 4 minuten).
Gebruik verbindingspools om vorm te geven aan het volume van uw verbindingen.
- Verlaat nooit zo maar een TCP-stroom en maak gebruik van TCP-timers om de stroom op te schonen. Als u de verbinding niet expliciet door TCP laat sluiten, blijft de status op Toegewezen staan bij tussenliggende systemen en eindpunten en zijn er geen SNAT-poorten beschikbaar voor andere verbindingen. Dit patroon kan fouten in de app en SNAT-uitputting veroorzaken.
- Wijzig timerwaarden voor het sluiten door TCP op besturingssysteemniveau alleen als u uitgebreide kennis hebt van de gevolgen hiervan. Terwijl de TCP-stack wordt hersteld, kunnen de prestaties van uw toepassing negatief worden beïnvloed wanneer de eind punten van een verbinding niet overeenkomen met de verwachtingen. Het willen wijzigen van timers is doorgaans een teken van een onderliggend ontwerp probleem. Bestudeer de volgende aanbevelingen.


In het bovenstaande voor beeld wordt de regel bijgewerkt zodat alleen binnenkomend extern verkeer van het *MY_EXTERNAL_IP_RANGE* bereik wordt toegestaan. Als u *MY_EXTERNAL_IP_RANGE* vervangt door het interne IP-adres van het subnet, wordt verkeer beperkt tot interne ip's van het cluster. Hiermee worden clients buiten uw Kubernetes-cluster niet toegestaan om toegang te krijgen tot de load balancer.

## <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>Verplaatsen van een basis-SKU load balancer naar standaard-SKU

Als u een bestaand cluster hebt met de basis-SKU Load Balancer, zijn er belang rijke verschillen in de gedrags bij het migreren om een cluster te gebruiken met de standaard-SKU Load Balancer.

Een voor beeld: het maken van Blue/groen-implementaties voor het migreren van clusters is een veelvoorkomende procedure `load-balancer-sku` op basis van het type van een cluster kan alleen worden gedefinieerd in een cluster create time. *Basic SKU* load balancers maken echter gebruik van *elementaire SKU* -IP-adressen, die niet compatibel zijn met *Standard SKU* load balancers, aangezien hiervoor *standaard-SKU* -IP-adressen zijn vereist. Bij het migreren van clusters om Load Balancer Sku's te upgraden, is een nieuw IP-adres met een compatibele IP-adres-SKU vereist.

Voor meer overwegingen over het migreren van clusters raadpleegt u de [documentatie over migratie overwegingen](aks-migration.md) om een lijst met belang rijke onderwerpen weer te geven waarmee u rekening moet houden bij de migratie. De onderstaande beperkingen zijn ook belang rijke verschillen in de situatie bij het gebruik van standaard SKU load balancers in AKS.

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die ondersteuning bieden voor een load balancer met de *standaard* -SKU:

* Er is ten minste één openbaar IP-of IP-voor voegsel vereist voor het toestaan van uitgaand verkeer van het AKS-cluster. Het open bare IP-of IP-voor voegsel is ook vereist voor het onderhouden van de connectiviteit tussen het besturings vlak en de agent knooppunten en voor het behoud van de compatibiliteit met eerdere versies van AKS. U hebt de volgende opties voor het opgeven van open bare Ip's of IP-voor voegsels met een *standaard* -SKU Load Balancer:
    * Geef uw eigen open bare Ip's op.
    * Geef uw eigen open bare IP-voor voegsels op.
    * Geef een waarde op van Maxi maal 100 zodat het AKS-cluster ervoor kan zorgen dat veel *standaard* -SKU open bare ip's in dezelfde resource groep zijn gemaakt als het AKS-cluster. Dit is meestal een naam met *MC_* aan het begin. AKS wijst het open bare IP-adres toe aan de *standaard* -SKU Load Balancer. Standaard wordt één openbaar IP-adres automatisch gemaakt in dezelfde resource groep als het AKS-cluster als er geen openbaar IP-adres, openbaar IP-voor voegsel of aantal Ip's is opgegeven. U moet ook open bare adressen toestaan en voor komen dat u een Azure Policy maakt waardoor het IP-adres niet kan worden gemaakt.
* Een openbaar IP-adres dat is gemaakt door AKS, kan niet opnieuw worden gebruikt als aangepaste zet uw eigen open bare IP-adressen. Alle aangepaste IP-adressen moeten worden gemaakt en beheerd door de gebruiker.
* Het definiëren van de load balancer SKU kan alleen worden uitgevoerd wanneer u een AKS-cluster maakt. U kunt de load balancer SKU niet wijzigen nadat er een AKS-cluster is gemaakt.
* U kunt slechts één type load balancer SKU (Basic of Standard) gebruiken in één cluster.
* *Standaard* Load balancers van SKU bieden alleen ondersteuning voor *standaard* -SKU-IP-adressen.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over Kubernetes Services vindt u in de [documentatie van Kubernetes Services][kubernetes-services].

Meer informatie over het gebruik van interne Load Balancer voor binnenkomend verkeer in de [interne Load Balancer-documentatie van AKS](internal-lb.md).

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
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[requirements]: #requirements-for-customizing-allocated-outbound-ports-and-idle-timeout
[use-multiple-node-pools]: use-multiple-node-pools.md
[troubleshoot-snat]: #troubleshooting-snat
[service-tags]: ../virtual-network/network-security-groups-overview.md#service-tags
