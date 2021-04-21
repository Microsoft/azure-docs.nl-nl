---
title: Meerdere knooppuntgroepen gebruiken in Azure Kubernetes Service (AKS)
description: Meer informatie over het maken en beheren van meerdere knooppuntgroepen voor een cluster in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/11/2021
ms.openlocfilehash: b7b54ccf6662e172ebfe95a84189df5e8e6e990f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832244"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Meerdere knooppuntgroepen maken en beheren voor een cluster in Azure Kubernetes Service (AKS)

In Azure Kubernetes Service (AKS) worden knooppunten van dezelfde configuratie gegroepeerd in *knooppuntgroepen.* Deze knooppuntgroepen bevatten de onderliggende VM's die uw toepassingen uitvoeren. Het aanvankelijke aantal knooppunten en hun grootte (SKU) wordt gedefinieerd wanneer u een AKS-cluster maakt, waarmee een systeemknooppuntgroep [wordt gemaakt.][use-system-pool] Als u toepassingen wilt ondersteunen die verschillende reken- of opslageisen hebben, kunt u extra *gebruikers-knooppuntgroepen maken.* Systeem-knooppuntgroepen dienen het primaire doel van het hosten van kritieke systeempods, zoals CoreDNS en tunnelfront. Gebruikers-knooppuntgroepen dienen het primaire doel van het hosten van uw toepassingspods. Toepassingspods kunnen echter worden gepland op systeemknooppuntgroepen als u slechts één groep in uw AKS-cluster wilt hebben. Gebruikers-knooppuntgroepen zijn de plaatsen waar u uw toepassingsspecifieke pods kunt plaatsen. Gebruik deze extra gebruikers-knooppuntgroepen bijvoorbeeld om GPU's te bieden voor rekenintensieve toepassingen of toegang tot SSD-opslag met hoge prestaties.

> [!NOTE]
> Met deze functie hebt u meer controle over het maken en beheren van meerdere knooppuntgroepen. Als gevolg hiervan zijn afzonderlijke opdrachten vereist voor maken/bijwerken/verwijderen. Eerder werden clusterbewerkingen `az aks create` via of de managedCluster-API gebruikt. Dit was de enige optie om uw besturingsvlak en `az aks update` één knooppuntgroep te wijzigen. Deze functie maakt een afzonderlijke bewerkingsset voor agentpools beschikbaar via de agentPool-API en vereist het gebruik van de opdrachtset om bewerkingen uit te voeren `az aks nodepool` op een afzonderlijke knooppuntgroep.

In dit artikel wordt beschreven hoe u meerdere knooppuntgroepen in een AKS-cluster maakt en beheert.

## <a name="before-you-begin"></a>Voordat u begint

Azure CLI versie 2.2.0 of hoger moet zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="limitations"></a>Beperkingen

De volgende beperkingen gelden wanneer u AKS-clusters maakt en beheert die ondersteuning bieden voor meerdere knooppuntgroepen:

* Zie [Quota, groottebeperkingen voor virtuele machines en beschikbaarheid in regio's in Azure Kubernetes Service (AKS)][quotas-skus-regions].
* U kunt systeemknooppuntgroepen verwijderen, mits u een andere systeemknooppuntgroep hebt die in het AKS-cluster moet worden gebruikt.
* Systeemgroepen moeten ten minste één knooppunt bevatten en gebruikersknooppuntgroepen kunnen nul of meer knooppunten bevatten.
* Het AKS-cluster moet de Standard SKU-load balancer om meerdere knooppuntgroepen te gebruiken. De functie wordt niet ondersteund met Basic SKU load balancers.
* Het AKS-cluster moet virtuele-machineschaalsets gebruiken voor de knooppunten.
* De naam van een knooppuntgroep mag alleen kleine alfanumerieke tekens bevatten en moet beginnen met een kleine letter. Voor Linux-knooppuntgroepen moet de lengte tussen 1 en 12 tekens lang zijn, voor Windows-knooppuntgroepen moet de lengte tussen 1 en 6 tekens zijn.
* Alle knooppuntgroepen moeten zich in hetzelfde virtuele netwerk bevinden.
* Wanneer u meerdere knooppuntgroepen maakt tijdens het maken van het cluster, moeten alle Kubernetes-versies die door knooppuntgroepen worden gebruikt, overeenkomen met de versie die is ingesteld voor het besturingsvlak. Dit kan worden bijgewerkt nadat het cluster is ingericht met behulp van bewerkingen per knooppuntgroep.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

> [!Important]
> Als u één systeemknooppuntgroep voor uw AKS-cluster in een productieomgeving hebt, raden we u aan ten minste drie knooppunten voor de knooppuntgroep te gebruiken.

Maak een AKS-cluster met één knooppuntgroep om aan de slag te gaan. In het volgende voorbeeld wordt de [opdracht az group create][az-group-create] gebruikt om een resourcegroep met de naam *myResourceGroup* te maken in de *regio eastus.* Vervolgens wordt een AKS-cluster met de naam *myAKSCluster* gemaakt met behulp van [de opdracht az aks create.][az-aks-create]

> [!NOTE]
> De *Basic* load balancer SKU **wordt niet ondersteund bij** het gebruik van meerdere knooppuntgroepen. Standaard worden AKS-clusters gemaakt met de *Standard* load balancer SKU vanuit de Azure CLI en Azure Portal.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --load-balancer-sku standard
```

Het duurt een paar minuten om het cluster te maken.

> [!NOTE]
> Om ervoor te zorgen dat uw cluster betrouwbaar werkt, moet u ten minste 2 (twee) knooppunten uitvoeren in de standaardknooppuntgroep, omdat essentiële systeemservices worden uitgevoerd in deze knooppuntgroep.

Wanneer het cluster klaar is, gebruikt u de [opdracht az aks get-credentials][az-aks-get-credentials] om de clusterreferenties op te halen voor gebruik met `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Een knooppuntgroep toevoegen

Het cluster dat in de vorige stap is gemaakt, heeft één knooppuntgroep. We gaan een tweede knooppuntgroep toevoegen met behulp van de [opdracht az aks nodepool add.][az-aks-nodepool-add] In het volgende voorbeeld wordt een knooppuntgroep met de *naam mynodepool gemaakt* waarin *drie knooppunten worden* uitgevoerd:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> De naam van een knooppuntgroep moet beginnen met een kleine letter en mag alleen alfanumerieke tekens bevatten. Voor Linux-knooppuntgroepen moet de lengte tussen 1 en 12 tekens lang zijn. Voor Windows-knooppuntgroepen moet de lengte tussen 1 en 6 tekens zijn.

Als u de status van uw knooppuntgroepen wilt bekijken, gebruikt u de opdracht [az aks node pool list][az-aks-nodepool-list] en geeft u de resourcegroep en clusternaam op:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

In de volgende voorbeelduitvoer ziet *u dat mynodepool* is gemaakt met drie knooppunten in de knooppuntgroep. Toen het AKS-cluster in de vorige stap werd gemaakt, werd er een *standaardknooppuntpool1* gemaakt met het aantal knooppunt *2*.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Als er *geen VmSize* is opgegeven wanneer u een knooppuntgroep toevoegt, is  de standaardgrootte *Standard_D2s_v3* voor Windows-knooppuntgroepen en Standard_DS2_v2 voor Linux-knooppuntgroepen. Als er *geen OrchestratorVersion* is opgegeven, wordt standaard dezelfde versie als het besturingsvlak gebruikt.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Een knooppuntgroep met een uniek subnet toevoegen (preview)

Voor een workload moeten de knooppunten van een cluster mogelijk worden gesplitst in afzonderlijke pools voor logische isolatie. Deze isolatie kan worden ondersteund met afzonderlijke subnetten die zijn toegewezen aan elke knooppuntgroep in het cluster. Dit kan voldoen aan vereisten zoals het hebben van niet-aaneengesloten adresruimte van een virtueel netwerk die moet worden gesplitst in knooppuntgroepen.

#### <a name="limitations"></a>Beperkingen

* Alle subnetten die zijn toegewezen aan knooppuntpools, moeten tot hetzelfde virtuele netwerk behoren.
* Systeempods moeten toegang hebben tot alle knooppunten/pods in het cluster om essentiële functionaliteit te bieden, zoals DNS-resolutie en tunneling van kubectl-logboeken/exec/port-forward proxy.
* Als u uw VNET uitbreidt nadat u het cluster hebt aanmaken, moet u uw cluster bijwerken (alle beheerde clsterbewerkingen uitvoeren, maar knooppuntgroepbewerkingen tellen niet) voordat u een subnet buiten de oorspronkelijke cidr toevoegt. AKS geeft nu een foutmelding voor de agentpool toevoegen, hoewel dit oorspronkelijk is toegestaan. Als u niet weet hoe u uw clusterbestand moet afstemmen, kunt u een ondersteuningsticket indienen. 
* Het netwerkbeleid van Deco wordt niet ondersteund. 
* Azure Network Policy wordt niet ondersteund.
* Kube-proxy verwacht één aaneengesloten cidr en gebruikt deze voor drie optmizations. Zie deze [K.E.P.](https://github.com/kubernetes/enhancements/tree/master/keps/sig-network/2450-Remove-knowledge-of-pod-cluster-CIDR-from-iptables-rules) en --cluster-cidr [hier](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) voor meer informatie. In azure cni wordt het subnet van uw eerste knooppuntgroep aan kube-proxy gegeven. 

Als u een knooppuntgroep met een toegewezen subnet wilt maken, geeft u de resource-id van het subnet door als een extra parameter bij het maken van een knooppuntgroep.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Een knooppuntgroep upgraden

> [!NOTE]
> Upgrade- en schaalbewerkingen op een cluster of knooppuntgroep kunnen niet tegelijkertijd worden uitgevoerd als er een fout wordt geretourneerd. In plaats daarvan moet elk bewerkingstype worden voltooid op de doelresource vóór de volgende aanvraag voor dezelfde resource. Lees meer over dit probleem in onze [gids voor probleemoplossing.](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade)

In de opdrachten in deze sectie wordt uitgelegd hoe u één specifieke knooppuntgroep bij kunt werken. De relatie tussen het upgraden van de Kubernetes-versie van het besturingsvlak en de knooppuntgroep wordt uitgelegd in de [onderstaande sectie.](#upgrade-a-cluster-control-plane-with-multiple-node-pools)

> [!NOTE]
> De versie van de besturingssysteemversie van de knooppuntgroep is gekoppeld aan de Kubernetes-versie van het cluster. U krijgt alleen upgrades van de besturingssysteemafbeelding na een clusterupgrade.

Omdat er in dit voorbeeld twee knooppuntgroepen zijn, moeten we [az aks nodepool upgrade gebruiken om][az-aks-nodepool-upgrade] een knooppuntgroep bij te werken. Gebruik [az aks get-upgrades][az-aks-get-upgrades] om de beschikbare upgrades te bekijken

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

We gaan de *mynodepool upgraden.* Gebruik de [opdracht az aks nodepool upgrade om][az-aks-nodepool-upgrade] de knooppuntgroep bij te upgraden, zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Gebruik de opdracht [az aks node pool list][az-aks-nodepool-list] om de status van uw knooppuntgroepen opnieuw weer te geven. In het volgende voorbeeld ziet u *dat mynodepool* de status *Upgrade* naar *KUBERNETES_VERSION:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Het duurt enkele minuten om de knooppunten te upgraden naar de opgegeven versie.

Als best practice moet u alle knooppuntgroepen in een AKS-cluster upgraden naar dezelfde Kubernetes-versie. Het standaardgedrag van is om alle knooppuntgroepen samen met het besturingsvlak bij te werken `az aks upgrade` om deze uitlijning te bereiken. Met de mogelijkheid om afzonderlijke knooppuntgroepen te upgraden, kunt u een rolling upgrade uitvoeren en pods plannen tussen knooppuntgroepen om de uptime van de toepassing te behouden binnen de bovenstaande beperkingen.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Een clusterbesturingsvlak upgraden met meerdere knooppuntgroepen

> [!NOTE]
> Kubernetes maakt gebruik van het standaard [semantic versioning-versieschema.](https://semver.org/) Het versienummer wordt uitgedrukt als *x.y.z,* waarbij *x* de belangrijkste versie is, *y* de secundaire versie en *z* de patchversie. In versie *1.12.6* is 1 bijvoorbeeld de belangrijkste versie, is 12 de secundaire versie en 6 de patchversie. De Kubernetes-versie van het besturingsvlak en de initiële knooppuntgroep worden ingesteld tijdens het maken van het cluster. Voor alle extra knooppuntgroepen is de Kubernetes-versie ingesteld wanneer ze aan het cluster worden toegevoegd. De Kubernetes-versies kunnen verschillen tussen knooppuntgroepen en tussen een knooppuntgroep en het besturingsvlak.

Aan een AKS-cluster zijn twee clusterresourceobjecten gekoppeld met Kubernetes-versies.

1. Een Kubernetes-versie van het clusterbesturingsvlak.
2. Een knooppuntgroep met een Kubernetes-versie.

Een besturingsvlak wordt aan een of meer knooppuntgroepen weergegeven. Het gedrag van een upgradebewerking is afhankelijk van welke Azure CLI-opdracht wordt gebruikt.

Voor het upgraden van een AKS-besturingsvlak is het gebruik van `az aks upgrade` vereist. Met deze opdracht worden de versie van het besturingsvlak en alle knooppuntgroepen in het cluster geupgraded.

Als u de opdracht `az aks upgrade` met de vlag uit `--control-plane-only` geeft, wordt alleen het clusterbesturingsvlak geupgraded. Geen van de gekoppelde knooppuntgroepen in het cluster wordt gewijzigd.

Voor het upgraden van afzonderlijke knooppuntgroepen is het gebruik van `az aks nodepool upgrade` vereist. Met deze opdracht wordt alleen de doel-knooppuntgroep met de opgegeven Kubernetes-versie geupgraded

### <a name="validation-rules-for-upgrades"></a>Validatieregels voor upgrades

De geldige Kubernetes-upgrades voor het besturingsvlak van een cluster en knooppuntgroepen worden gevalideerd door de volgende regelsets.

* Regels voor geldige versies voor het upgraden van knooppuntgroepen:
   * De versie van de knooppuntgroep moet dezelfde *hoofdversie* hebben als het besturingsvlak.
   * De secundaire versie van *de knooppuntgroep* moet binnen twee *secundaire versies* van de versie van het besturingsvlak zijn.
   * De versie van de knooppuntgroep mag niet groter zijn dan de versie van het `major.minor.patch` besturingselement.

* Regels voor het verzenden van een upgradebewerking:
   * U kunt de kubernetes-versie van het besturingsvlak of een knooppuntgroep niet downgraden.
   * Als er geen Kubernetes-versie van een knooppuntgroep is opgegeven, is het gedrag afhankelijk van de gebruikte client. Declaratie in Resource Manager-sjablonen valt terug naar de bestaande versie die is gedefinieerd voor de knooppuntgroep als deze wordt gebruikt. Als er geen is ingesteld, wordt de versie van het besturingsvlak gebruikt om op terug te vallen.
   * U kunt een besturingsvlak of een knooppuntgroep op een bepaald moment upgraden of schalen. U kunt niet meerdere bewerkingen tegelijk verzenden op één besturingsvlak of knooppuntgroep.

## <a name="scale-a-node-pool-manually"></a>Een knooppuntgroep handmatig schalen

Als de workload van uw toepassing verandert, moet u mogelijk het aantal knooppunten in een knooppuntgroep schalen. Het aantal knooppunten kan omhoog of omlaag worden geschaald.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Gebruik de opdracht [az aks node pool scale][az-aks-nodepool-scale] om het aantal knooppunten in een knooppuntgroep te schalen. In het volgende voorbeeld wordt het aantal knooppunten in *mynodepool geschaald* naar *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Gebruik de opdracht [az aks node pool list][az-aks-nodepool-list] om de status van uw knooppuntgroepen opnieuw weer te geven. In het volgende voorbeeld ziet u *dat mynodepool* de status *Schalen* heeft met een nieuw aantal *van 5 knooppunten:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Het duurt enkele minuten voordat de schaalbewerking is voltooid.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Een specifieke knooppuntgroep automatisch schalen door automatische schaalvergroting van clusters in teschakelen

AKS biedt een afzonderlijke functie voor het automatisch schalen van knooppuntgroepen met een functie genaamd [automatische schaalvergroting van clusters.](cluster-autoscaler.md) Deze functie kan worden ingeschakeld per knooppuntgroep met unieke minimale en maximale schaalaantal per knooppuntgroep. Meer informatie over het [gebruik van de automatische schaalverdeder voor clusters per knooppuntgroep.](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)

## <a name="delete-a-node-pool"></a>Een knooppuntgroep verwijderen

Als u een pool niet meer nodig hebt, kunt u deze verwijderen en de onderliggende VM-knooppunten verwijderen. Als u een knooppuntgroep wilt verwijderen, gebruikt u [de opdracht az aks node pool delete][az-aks-nodepool-delete] en geeft u de naam van de knooppuntgroep op. In het volgende voorbeeld wordt de *mynoodepool verwijderd die* in de vorige stappen is gemaakt:

> [!CAUTION]
> Er zijn geen herstelopties voor gegevensverlies die kunnen optreden wanneer u een knooppuntgroep verwijdert. Als pods niet kunnen worden gepland op andere knooppuntgroepen, zijn deze toepassingen niet beschikbaar. Zorg ervoor dat u geen knooppuntgroep verwijdert wanneer in-use toepassingen geen gegevensback-ups hebben of de mogelijkheid om uit te voeren op andere knooppuntgroepen in uw cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

In de volgende voorbeelduitvoer van [de opdracht az aks node pool list][az-aks-nodepool-list] ziet u dat *mynodepool* *de status Verwijderen* heeft:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Het duurt enkele minuten om de knooppunten en de knooppuntgroep te verwijderen.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Een VM-grootte voor een knooppuntgroep opgeven

In de vorige voorbeelden voor het maken van een knooppuntgroep werd een standaard VM-grootte gebruikt voor de knooppunten die in het cluster zijn gemaakt. Een veelvoorkomende situatie is dat u knooppuntgroepen maakt met verschillende VM-grootten en -mogelijkheden. U kunt bijvoorbeeld een knooppuntgroep maken die knooppunten bevat met grote hoeveelheden CPU of geheugen, of een knooppuntgroep die GPU-ondersteuning biedt. In de volgende stap gebruikt u [taints en toleraties](#setting-nodepool-taints) om de Kubernetes-scheduler te laten weten hoe de toegang kan worden beperkt tot pods die op deze knooppunten kunnen worden uitgevoerd.

In het volgende voorbeeld maakt u een op GPU gebaseerde knooppuntgroep die gebruikmaakt van de *Standard_NC6* VM-grootte. Deze VM's powered by de NVIDIA Tesla K80-kaart. Zie Grootten voor virtuele Linux-machines in Azure voor meer informatie over beschikbare [VM-grootten.][vm-sizes]

Maak opnieuw een knooppuntgroep met [behulp van de opdracht az aks node pool add.][az-aks-nodepool-add] Geef deze keer de naam *gpunodepool* op en gebruik de `--node-vm-size` parameter om de grootte Standard_NC6 opgeven: 

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

De volgende voorbeelduitvoer van de [opdracht az aks node pool list][az-aks-nodepool-list] laat zien dat *gpunodepool* *Knooppunten* maken is met de opgegeven *VmSize*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Het duurt enkele minuten voordat de *gpunodepool* is gemaakt.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Een taint, label of tag opgeven voor een knooppuntgroep

Wanneer u een knooppuntgroep maakt, kunt u taints, labels of tags toevoegen aan die knooppuntgroep. Wanneer u een taint, label of tag toevoegt, krijgen alle knooppunten in die knooppuntgroep ook die taint, het label of de tag.

> [!IMPORTANT]
> Het toevoegen van taints, labels of tags aan knooppunten moet worden uitgevoerd voor de hele knooppuntgroep met behulp van `az aks nodepool` . Het wordt niet aanbevolen om taints, lablels of tags toe te passen op afzonderlijke knooppunten in een knooppuntgroep met `kubectl` behulp van .  

### <a name="setting-nodepool-taints"></a>Taints voor knooppuntpool instellen

Gebruik [az aks nodepool add][az-aks-nodepool-add]om een knooppuntgroep met een taint te maken. Geef de naam *taintnp op* en gebruik de `--node-taints` parameter om *sku=gpu:NoSchedule* voor de taint op te geven.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> Een taint kan alleen worden ingesteld voor knooppuntgroepen tijdens het maken van de knooppuntgroep.

De volgende voorbeelduitvoer van de [opdracht az aks nodepool list][az-aks-nodepool-list] laat zien dat *taintnp* *Knooppunten* maken is met de opgegeven *nodeTaints:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

De taint-informatie is zichtbaar in Kubernetes voor het verwerken van planningsregels voor knooppunten. De Kubernetes-scheduler kan taints en toleraties gebruiken om te beperken welke workloads op knooppunten kunnen worden uitgevoerd.

* Een **taint** wordt toegepast op een knooppunt dat aangeeft dat er alleen specifieke pods op kunnen worden gepland.
* Vervolgens **wordt een toleratie** toegepast op  een pod waarmee ze de taint van een knooppunt kunnen tolereren.

Zie Best practices for advanced scheduler features in AKS (Best practices voor geavanceerde scheduler-functies in AKS) voor meer informatie over het gebruik van [geavanceerde geplande Kubernetes-functies.][taints-tolerations]

In de vorige stap hebt u de *taint sku=gpu:NoSchedule* toegepast toen u de knooppuntgroep maakte. In het volgende yaml-basismanifest wordt een toleratie gebruikt om de Kubernetes-scheduler toe te staan een NGINX-pod uit te voeren op een knooppunt in die knooppuntgroep.

Maak een bestand met de `nginx-toleration.yaml` naam en kopieer het in het volgende yaml-voorbeeld:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Plan de pod met behulp van de `kubectl apply -f nginx-toleration.yaml` opdracht :

```console
kubectl apply -f nginx-toleration.yaml
```

Het duurt een paar seconden om de pod te plannen en de NGINX-afbeelding op te halen. Gebruik de [opdracht kubectl describe pod][kubectl-describe] om de podstatus weer te geven. In de volgende verkorte voorbeelduitvoer ziet u *dat de toleratie sku=gpu:NoSchedule* is toegepast. In de sectie gebeurtenissen heeft de scheduler de pod toegewezen aan het knooppunt *aks-taintnp-28993262-vmss000000:*

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                Message
  ----    ------     ----   ----                -------
  Normal  Scheduled  4m48s  default-scheduler   Successfully assigned default/mypod to aks-taintnp-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet             pulling image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Pulled     4m43s  kubelet             Successfully pulled image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Created    4m40s  kubelet             Created container
  Normal  Started    4m40s  kubelet             Started container
```

Alleen pods waarin deze toleratie is toegepast, kunnen worden gepland op knooppunten in *taintnp*. Elke andere pod wordt gepland in de *knooppuntgroep nodepool1.* Als u extra knooppuntgroepen maakt, kunt u extra taints en toleranties gebruiken om te beperken welke pods kunnen worden gepland voor deze knooppuntbronnen.

### <a name="setting-nodepool-labels"></a>Labels voor knooppuntpool instellen

U kunt ook labels toevoegen aan een knooppuntgroep tijdens het maken van een knooppuntgroep. Labels die zijn ingesteld in de knooppuntgroep worden toegevoegd aan elk knooppunt in de knooppuntgroep. Deze [labels zijn zichtbaar in Kubernetes voor][kubernetes-labels] het verwerken van planningsregels voor knooppunten.

Gebruik [az aks nodepool add][az-aks-nodepool-add]om een knooppuntgroep met een label te maken. Geef de naam *labelnp op* en gebruik de `--labels` parameter om *dept=IT* en *costcenter=9999* op te geven voor labels.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Label kan alleen worden ingesteld voor knooppuntgroepen tijdens het maken van de knooppuntgroep. Labels moeten ook een sleutel-waardepaar zijn en een geldige [syntaxis hebben.][kubernetes-label-syntax]

In de volgende voorbeelduitvoer van de [opdracht az aks nodepool list][az-aks-nodepool-list] ziet u dat *labelnp* *Knooppunten* maken is met de opgegeven *nodeLabels:*

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>Azure-tags voor knooppuntpool instellen

U kunt een Azure-tag toepassen op knooppuntgroepen in uw AKS-cluster. Tags die worden toegepast op een knooppuntgroep worden toegepast op elk knooppunt in de knooppuntgroep en blijven bestaan via upgrades. Tags worden ook toegepast op nieuwe knooppunten die zijn toegevoegd aan een knooppuntgroep tijdens uitschaalbewerkingen. Het toevoegen van een tag kan helpen bij taken zoals het bijhouden van beleid of kostenraming.

Azure-tags hebben sleutels die niet hoofd- en hoofd- en hoofdincidentief zijn voor bewerkingen, zoals bij het ophalen van een tag door in de sleutel te zoeken. In dit geval wordt een tag met de opgegeven sleutel bijgewerkt of opgehaald, ongeacht de hoofdingscode. Tagwaarden zijn casegevoelig.

Als in AKS meerdere tags zijn ingesteld met identieke sleutels, maar verschillende letters bevatten, is de gebruikte tag de eerste in alfabetische volgorde. Resulteert bijvoorbeeld `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` in en wordt `Key1` `val1` ingesteld.

Maak een knooppuntgroep met behulp van [de az aks nodepool add.][az-aks-nodepool-add] Geef de naam *tagnodepool op* en gebruik de `--tag` parameter om *dept=IT* en *costcenter=9999 op* te geven voor tags.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> U kunt de parameter ook `--tags` gebruiken bij het gebruik van de opdracht az [aks nodepool update][az-aks-nodepool-update] en tijdens het maken van het cluster. Tijdens het maken van het cluster past `--tags` de parameter de tag toe op de initiële knooppuntgroep die met het cluster is gemaakt. Alle tagnamen moeten voldoen aan de beperkingen in [Tags gebruiken om uw Azure-resources te organiseren.][tag-limitation] Door een knooppuntgroep bij te werken met de parameter worden alle bestaande tagwaarden bijgewerkt en worden nieuwe `--tags` tags toegevoegd. Als uw knooppuntgroep bijvoorbeeld *dept=IT* en *costcenter=9999 voor* tags had en u deze hebt bijgewerkt met *team=dev* en *costcenter=111* voor tags, zou u nodepool *dept=IT,* *costcenter=111* en *team=dev* voor tags hebben.

In de volgende voorbeelduitvoer van [de opdracht az aks nodepool list][az-aks-nodepool-list] ziet u dat *tagnodepool* *Knooppunten* maken is met de opgegeven *tag*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Knooppuntgroepen beheren met behulp van een Resource Manager sjabloon

Wanneer u een Azure Resource Manager gebruikt om resources te maken en te beheren, kunt u doorgaans de instellingen in uw sjabloon bijwerken en opnieuw toepassen om de resource bij te werken. Met knooppuntgroepen in AKS kan het profiel van de initiële knooppuntgroep niet worden bijgewerkt zodra het AKS-cluster is gemaakt. Dit gedrag betekent dat u geen bestaande Resource Manager kunt bijwerken, de knooppuntgroepen kunt wijzigen en opnieuw kunt toepassen. In plaats daarvan moet u een afzonderlijke Resource Manager maken die alleen de knooppuntgroepen voor een bestaand AKS-cluster bij werkt.

Maak een sjabloon zoals `aks-agentpools.json` en plak het volgende voorbeeldmanifest. Met deze voorbeeldsjabloon worden de volgende instellingen geconfigureerd:

* Werkt de *Linux-knooppuntgroep* met de *naam myagentpool bij* om drie knooppunten uit te voeren.
* Hiermee stelt u de knooppunten in de knooppuntgroep in om Kubernetes-versie *1.15.7 uit te voeren.*
* Definieert de *knooppuntgrootte Standard_DS2_v2*.

Bewerk deze waarden zo nodig om knooppuntgroepen bij te werken, toe te voegen of te verwijderen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Implementeer deze sjabloon met behulp [van de opdracht az deployment group create,][az-deployment-group-create] zoals wordt weergegeven in het volgende voorbeeld. U wordt gevraagd om de naam en locatie van het bestaande AKS-cluster:

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> U kunt een tag toevoegen aan uw knooppuntgroep door de *tag-eigenschap* toe te voegen aan de sjabloon, zoals wordt weergegeven in het volgende voorbeeld.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Het kan enkele minuten duren om uw AKS-cluster bij te werken, afhankelijk van de instellingen en bewerkingen voor de knooppuntgroep die u in de sjabloon Resource Manager definiëren.

## <a name="assign-a-public-ip-per-node-for-your-node-pools"></a>Een openbaar IP-adres per knooppunt toewijzen voor uw knooppuntgroepen

AKS-knooppunten hebben geen eigen openbare IP-adressen nodig voor communicatie. In scenario's kunnen knooppunten in een knooppuntgroep echter hun eigen toegewezen openbare IP-adressen ontvangen. Een veelvoorkomende situatie is voor gamingworkloads, waarbij een console een directe verbinding moet maken met een virtuele machine in de cloud om hops te minimaliseren. Dit scenario kan worden bereikt in AKS met behulp van openbaar IP-adres van knooppunt.

Maak eerst een nieuwe resourcegroep.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Maak een nieuw AKS-cluster en koppel een openbaar IP-adres voor uw knooppunten. Elk van de knooppunten in de knooppuntgroep ontvangt een uniek openbaar IP-adres. U kunt dit controleren door te kijken naar de exemplaren van de virtuele-machineschaalset.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

Voor bestaande AKS-clusters kunt u ook een nieuwe knooppuntgroep toevoegen en een openbaar IP-adres voor uw knooppunten koppelen.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

### <a name="use-a-public-ip-prefix"></a>Een openbaar IP-voorvoegsel gebruiken

Het gebruik van een openbaar [IP-voorvoegsel][public-ip-prefix-benefits]heeft een aantal voordelen. AKS ondersteunt het gebruik van adressen van een bestaand openbaar IP-voorvoegsel voor uw knooppunten door de resource-id met de vlag door te geven bij het maken van een nieuw cluster of het toevoegen van een `node-public-ip-prefix` knooppuntgroep.

Maak eerst een openbaar IP-voorvoegsel met [az network public-ip prefix create][az-public-ip-prefix-create]:

```azurecli-interactive
az network public-ip prefix create --length 28 --location eastus --name MyPublicIPPrefix --resource-group MyResourceGroup3
```

Bekijk de uitvoer en noteer de `id` voor het voorvoegsel:

```output
{
  ...
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix",
  ...
}
```

Gebruik ten slotte bij het maken van een nieuw cluster of het toevoegen van een nieuwe knooppuntgroep de vlag en geef de resource-id van het `node-public-ip-prefix` voorvoegsel door:

```azurecli-interactive
az aks create -g MyResourceGroup3 -n MyManagedCluster -l eastus --enable-node-public-ip --node-public-ip-prefix /subscriptions/<subscription-id>/resourcegroups/MyResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix
```

### <a name="locate-public-ips-for-nodes"></a>Openbare IP's voor knooppunten zoeken

U kunt de openbare IP's voor uw knooppunten op verschillende manieren vinden:

* Gebruik de Azure [CLI-opdracht az vmss list-instance-public-ips.][az-list-ips]
* Gebruik [PowerShell- of Bash-opdrachten.][vmss-commands] 
* U kunt ook de openbare IP's in de Azure Portal door de exemplaren in de virtuele-machineschaalset te bekijken.

> [!Important]
> De [knooppuntresourcegroep bevat][node-resource-group] de knooppunten en hun openbare IP's. Gebruik de knooppuntresourcegroep bij het uitvoeren van opdrachten om de openbare VIP's voor uw knooppunten te vinden.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel hebt u een AKS-cluster gemaakt dat GPU-knooppunten bevat. Om onnodige kosten te verminderen, kunt u de *gpunodepool* of het hele AKS-cluster verwijderen.

Als u de op GPU gebaseerde knooppuntgroep wilt verwijderen, gebruikt u de [opdracht az aks nodepool delete,][az-aks-nodepool-delete] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Als u het cluster zelf wilt verwijderen, gebruikt u [de opdracht az group delete][az-group-delete] om de AKS-resourcegroep te verwijderen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

U kunt ook het extra cluster verwijderen dat u hebt gemaakt voor het openbare IP-adres voor knooppuntgroepen.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [systeem-knooppuntgroepen.][use-system-pool]

In dit artikel hebt u geleerd hoe u meerdere knooppuntgroepen in een AKS-cluster maakt en beheert. Zie Best practices for advanced scheduler features in AKS (Best practices voor geavanceerde [scheduler-functies in AKS)][operator-best-practices-advanced-scheduler]voor meer informatie over het beheer van pods in knooppuntgroepen.

Zie Een [Windows Server-container][aks-windows]maken in AKS als u Windows Server-container-knooppuntgroepen wilt maken en gebruiken.

Gebruik [nabijheidsplaatsingsgroepen][reduce-latency-ppg] om de latentie voor uw AKS-toepassingen te verminderen.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_update
[az-group-create]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create
[az-group-delete]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete
[az-deployment-group-create]: /cli/azure/deployment/group?view=azure-cli-latest&preserve-view=true#az_deployment_group_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest&preserve-view=true#az_vmss_list_instance_public_ips
[reduce-latency-ppg]: reduce-latency-ppg.md
[public-ip-prefix-benefits]: ../virtual-network/public-ip-address-prefix.md#why-create-a-public-ip-address-prefix
[az-public-ip-prefix-create]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest&preserve-view=true#az_network_public_ip_prefix_create
