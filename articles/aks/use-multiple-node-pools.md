---
title: Meerdere knooppunt groepen gebruiken in azure Kubernetes service (AKS)
description: Meer informatie over het maken en beheren van meerdere knooppunt groepen voor een cluster in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 02/11/2021
ms.openlocfilehash: bb10e2023187c74a9e8b9a2e4c72115841e89a84
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552594"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Meerdere knooppuntgroepen maken en beheren voor een cluster in Azure Kubernetes Service (AKS)

In azure Kubernetes service (AKS) worden knoop punten van dezelfde configuratie samen in *knooppunt groepen* gegroepeerd. Deze knooppunt groepen bevatten de onderliggende virtuele machines waarop uw toepassingen worden uitgevoerd. Het eerste aantal knoop punten en hun grootte (SKU) wordt gedefinieerd wanneer u een AKS-cluster maakt, waarmee een [systeem knooppunt groep][use-system-pool]wordt gemaakt. Als u toepassingen wilt ondersteunen die verschillende reken-of opslag vereisten hebben, kunt u extra *gebruikers knooppunt groepen* maken. Systeem knooppunt groepen fungeren het primaire doel van het hosten van essentieel systeem peulen, zoals CoreDNS en tunnelfront. Gebruikers knooppunt groepen gebruiken het primaire doel om uw toepassing te hosten. Toepassing peul kan echter worden gepland op systeem knooppunt groepen als u slechts één groep in uw AKS-cluster wilt hebben. Met gebruikers knooppunt groepen kunt u uw toepassing op een specifieke locatie plaatsen. Gebruik bijvoorbeeld deze extra knooppunt groepen voor gebruikers om Gpu's te bieden voor computerintensieve toepassingen, of om toegang te krijgen tot high-performance SSD-opslag.

> [!NOTE]
> Deze functie biedt meer controle over het maken en beheren van meerdere knooppunt groepen. Als gevolg hiervan zijn afzonderlijke opdrachten vereist voor maken/bijwerken/verwijderen. Eerder cluster bewerkingen via `az aks create` of `az aks update` gebruikt de MANAGEDCLUSTER-API en waren de enige optie om uw besturings vlak en een groep met één knoop punt te wijzigen. Deze functie beschrijft een afzonderlijke bewerking voor agent groepen via de agent pool-API en vereist het gebruik van de `az aks nodepool` opdrachtset voor het uitvoeren van bewerkingen op een afzonderlijke knooppunt groep.

In dit artikel wordt beschreven hoe u meerdere knooppunt groepen kunt maken en beheren in een AKS-cluster.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.2.0 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert die ondersteuning bieden voor meerdere knooppunt groepen:

* Zie [quota's, beperkingen voor de grootte van virtuele machines en beschik baarheid van regio's in azure Kubernetes service (AKS)][quotas-skus-regions].
* U kunt systeem knooppunt groepen verwijderen. u hebt een andere groep van het systeem knooppunt nodig om in het AKS-cluster te plaatsen.
* Systeem groepen moeten ten minste één knoop punt bevatten en gebruikers knooppunt groepen kunnen nul of meer knoop punten bevatten.
* Het AKS-cluster moet het standaard SKU-load balancer gebruiken om meerdere knooppunt groepen te kunnen gebruiken. de functie wordt niet ondersteund met Basic SKU load balancers.
* Het AKS-cluster moet virtuele-machine schaal sets gebruiken voor de knoop punten.
* De naam van een knooppunt groep mag alleen kleine letters bevatten en moet beginnen met een kleine letter. Voor Linux-knooppunt Pools moet de lengte tussen 1 en 12 tekens lang zijn voor Windows-knooppunt groepen. de lengte moet tussen 1 en 6 tekens lang zijn.
* Alle knooppunt groepen moeten zich in hetzelfde virtuele netwerk bevinden.
* Bij het maken van meerdere knooppunt groepen tijdens het maken van een cluster, moeten alle Kubernetes-versies die worden gebruikt door de knooppunt groepen, overeenkomen met de versieset voor het besturings vlak. Dit kan worden bijgewerkt nadat het cluster is ingericht met behulp van de bewerkingen per knooppunt groep.

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken

> [!Important]
> Als u één systeem knooppunt groep voor uw AKS-cluster in een productie omgeving uitvoert, raden we u aan om ten minste drie knoop punten voor de knooppunt groep te gebruiken.

Om aan de slag te gaan, maakt u een AKS-cluster met één knooppunt groep. In het volgende voor beeld wordt de opdracht [AZ Group Create][az-group-create] gebruikt voor het maken van een resource groep met de naam *myResourceGroup* in de regio *eastus* . Er wordt een AKS-cluster met de naam *myAKSCluster* gemaakt met behulp van de opdracht [AZ AKS Create][az-aks-create] .

> [!NOTE]
> De *Basic* Load Balancer SKU wordt **niet ondersteund** wanneer meerdere knooppunt groepen worden gebruikt. Standaard worden AKS-clusters gemaakt met de *Standard* Load Balancer SKU van de Azure CLI en Azure Portal.

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
> Om ervoor te zorgen dat uw cluster betrouwbaar werkt, moet u ten minste twee knoop punten in de standaard knooppunt groep uitvoeren, aangezien essentiële systeem services worden uitgevoerd in deze knooppunt groep.

Wanneer het cluster gereed is, gebruikt u de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] om de cluster referenties te verkrijgen voor gebruik met `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Een knooppuntgroep toevoegen

Het cluster dat u in de vorige stap hebt gemaakt, heeft een groep met één knoop punt. We gaan een tweede groep knoop punten toevoegen met behulp van de opdracht [AZ AKS nodepool add][az-aks-nodepool-add] . In het volgende voor beeld wordt een knooppunt groep gemaakt met de naam *mynodepool* die *drie* knoop punten uitvoert:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> De naam van een knooppunt groep moet beginnen met een kleine letter en mag alleen alfanumerieke tekens bevatten. Voor Linux-knooppunt Pools moet de lengte tussen 1 en 12 tekens lang zijn voor Windows-knooppunt groepen. de lengte moet tussen 1 en 6 tekens lang zijn.

Als u de status van de knooppunt groepen wilt weer geven, gebruikt u de opdracht [AZ AKS node pool List][az-aks-nodepool-list] en geeft u de resource groep en de naam van het cluster op:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

In de volgende voorbeeld uitvoer ziet u dat *mynodepool* is gemaakt met drie knoop punten in de knooppunt groep. Wanneer het AKS-cluster in de vorige stap is gemaakt, is een standaard *nodepool1* gemaakt met het aantal knoop punten *2*.

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
> Als er geen *VmSize* is opgegeven wanneer u een knooppunt groep toevoegt, is de standaard grootte *Standard_D2s_v3* voor Windows-knooppunt groepen en *Standard_DS2_v2* voor Linux-knooppunt groepen. Als er geen *OrchestratorVersion* is opgegeven, wordt standaard dezelfde versie gebruikt als het besturings vlak.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Een knooppunt groep toevoegen met een uniek subnet (preview-versie)

Een werk belasting vereist mogelijk het splitsen van de knoop punten van een cluster in afzonderlijke groepen voor logische isolatie. Deze isolatie kan worden ondersteund met afzonderlijke subnetten die zijn toegewezen aan elke knooppunt groep in het cluster. Dit kan voldoen aan vereisten, zoals het hebben van niet-aaneengesloten virtuele netwerk adres ruimte om te splitsen over de knooppunt groepen.

#### <a name="limitations"></a>Beperkingen

* Alle subnetten die aan nodepools zijn toegewezen, moeten deel uitmaken van hetzelfde virtuele netwerk.
* Systeem-peul moet toegang hebben tot alle knoop punten/peul in het cluster om essentiële functionaliteit te bieden, zoals DNS-omzetting en tunneling kubectl-logboeken/exec/port-forward proxy.
* Als u uw VNET hebt uitgebreid nadat u het cluster hebt gemaakt, moet u uw cluster bijwerken (een beheerde clster-bewerking uitvoeren, maar de knooppunt groeps bewerkingen worden niet geteld) voordat u een subnet toevoegt buiten de oorspronkelijke cidr. AKS is een fout opgetreden bij het toevoegen van de agent pool, hoewel we deze oorspronkelijk hebben toegestaan. Als u niet weet hoe u uw cluster bestand moet afstemmen op een ondersteunings ticket. 
* Calico-netwerk beleid wordt niet ondersteund. 
* Het Azure-netwerk beleid wordt niet ondersteund.
* Uitvoeren-proxy verwacht een enkele aaneengesloten CIDR en gebruikt deze voor drie optmizations. Deze [K.E.P.](https://github.com/kubernetes/enhancements/tree/master/keps/sig-network/2450-Remove-knowledge-of-pod-cluster-CIDR-from-iptables-rules) weer geven en--cluster-CIDR [hier](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) voor meer informatie. In azure cni wordt het subnet van uw eerste groep knoop punten gegeven aan uitvoeren-proxy. 

Als u een knooppunt groep met een toegewijd subnet wilt maken, geeft u de bron-ID van het subnet door als extra para meter bij het maken van een knooppunt groep.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Een knooppunt groep upgraden

> [!NOTE]
> Upgrade-en schaal bewerkingen op een cluster of knooppunt groep kunnen niet tegelijkertijd plaatsvinden, als er een fout wordt geretourneerd. In plaats daarvan moet elk bewerkings type worden voltooid voor de doel resource vóór de volgende aanvraag op dezelfde resource. Meer informatie hierover vindt u in onze [probleemoplossings handleiding](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade).

Met de opdrachten in deze sectie wordt uitgelegd hoe u één specifieke knooppunt groep bijwerkt. De relatie tussen het upgraden van de Kubernetes-versie van het besturings vlak en de knooppunt groep wordt uitgelegd in de [volgende sectie](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> De versie van de installatie kopie van het besturings systeem van de knooppunt groep is gekoppeld aan de Kubernetes-versie van het cluster. U kunt upgrades van de installatie kopie van het besturings systeem downloaden na een upgrade van een cluster.

Omdat er in dit voor beeld twee knooppunt groepen zijn, moeten we [AZ AKS nodepool upgrade][az-aks-nodepool-upgrade] gebruiken om een knooppunt groep bij te werken. Gebruik [AZ AKS Get-upgrades][az-aks-get-upgrades] voor een overzicht van de beschik bare upgrades

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

We gaan de *mynodepool* bijwerken. Gebruik de opdracht [AZ AKS nodepool upgrade][az-aks-nodepool-upgrade] om de knooppunt groep bij te werken, zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Vermeld opnieuw de status van uw knooppunt groepen met de opdracht [AZ AKS node pool List][az-aks-nodepool-list] . In het volgende voor beeld ziet u dat *mynodepool* zich in de *upgrade* status bevindt op *KUBERNETES_VERSION*:

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

Het duurt enkele minuten om de knoop punten bij te werken naar de opgegeven versie.

Als best practice moet u alle knooppunt groepen in een AKS-cluster upgraden naar dezelfde Kubernetes-versie. Het standaard gedrag van `az aks upgrade` is het upgraden van alle knooppunt groepen samen met het besturings vlak om deze uitlijning te vervolledigen. Met de mogelijkheid om afzonderlijke knooppunt groepen bij te werken, kunt u een rolling upgrade uitvoeren en een Peul plannen tussen knooppunt groepen om de actieve tijds duur van de toepassing binnen de hierboven vermelde beperkingen te hand haven.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Een cluster besturings vlak upgraden met meerdere knooppunt groepen

> [!NOTE]
> Kubernetes maakt gebruik van het standaard versie beheer schema van [semantische versie](https://semver.org/) . Het versie nummer wordt weer gegeven als *x. y. z*, waarbij *x* de primaire versie is, *y* de secundaire versie en *z* de versie van de patch. In versie *1.12.6* is bijvoorbeeld 1 de primaire versie, 12 de secundaire versie en 6 de versie van de patch. De Kubernetes-versie van het besturings vlak en de eerste knooppunt groep worden ingesteld tijdens het maken van het cluster. Voor alle extra knooppunt groepen wordt de Kubernetes-versie ingesteld wanneer ze aan het cluster worden toegevoegd. De Kubernetes-versies kunnen verschillen tussen de knooppunt Pools en tussen een knooppunt groep en het besturings vlak.

Een AKS-cluster heeft twee cluster resource objecten waaraan Kubernetes-versies zijn gekoppeld.

1. Een Kubernetes-versie van het cluster besturings vlak.
2. Een knooppunt groep met een Kubernetes-versie.

Een besturings vlak wordt toegewezen aan een of meer knooppunt groepen. Het gedrag van een upgrade bewerking is afhankelijk van welke Azure CLI-opdracht wordt gebruikt.

Voor het bijwerken van een AKS-besturings vlak is het vereist `az aks upgrade` . Met deze opdracht worden de versie van het besturings element en alle knooppunt Pools in het cluster bijgewerkt.

Als de `az aks upgrade` opdracht met de vlag wordt uitgegeven, wordt `--control-plane-only` alleen het cluster besturings vlak bijgewerkt. Geen van de gekoppelde knooppunt Pools in het cluster is gewijzigd.

Voor het upgraden van afzonderlijke knooppunt Pools is het nodig `az aks nodepool upgrade` . Met deze opdracht wordt alleen de doel knooppunt groep met de opgegeven Kubernetes-versie bijgewerkt

### <a name="validation-rules-for-upgrades"></a>Validatie regels voor upgrades

De geldige Kubernetes-upgrades voor de besturings vlak-en knooppunt groepen van een cluster worden gevalideerd door de volgende sets regels.

* Regels voor geldige versies voor het upgraden van knooppunt groepen:
   * De versie van de knooppunt groep moet dezelfde *primaire* versie hebben als het besturings vlak.
   * De *secundaire* versie van de knooppunt groep moet binnen twee *secundaire* versies van de besturings vlak versie zijn.
   * De versie van de knooppunt groep mag niet hoger zijn dan de versie van het besturings element `major.minor.patch` .

* Regels voor het verzenden van een upgrade bewerking:
   * U kunt de Kubernetes-versie van het besturings vlak of de groep met knooppunt groepen niet verlagen.
   * Als er geen Kubernetes-versie van een knooppunt groep wordt opgegeven, is het gedrag afhankelijk van de gebruikte client. De declaratie in Resource Manager-sjablonen valt terug naar de bestaande versie die is gedefinieerd voor de knooppunt groep als deze wordt gebruikt, als er geen is ingesteld, wordt de versie van het besturings element gebruikt om terug te vallen.
   * U kunt een besturings vlak of een knooppunt groep op een bepaald moment bijwerken of schalen, u kunt niet meerdere bewerkingen tegelijkertijd verzenden op één besturings vlak of resource van een groep knoop punten.

## <a name="scale-a-node-pool-manually"></a>Een knooppunt groep hand matig schalen

Wanneer de werk belasting van uw toepassing wordt gewijzigd, moet u mogelijk het aantal knoop punten in een knooppunt groep schalen. Het aantal knoop punten kan omhoog of omlaag worden geschaald.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Als u het aantal knoop punten in een knooppunt groep wilt schalen, gebruikt u de opdracht [AZ AKS knoop punten Scale][az-aks-nodepool-scale] . In het volgende voor beeld wordt het aantal knoop punten in *mynodepool* naar *5* geschaald:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Vermeld opnieuw de status van uw knooppunt groepen met de opdracht [AZ AKS node pool List][az-aks-nodepool-list] . In het volgende voor beeld ziet u dat *mynodepool* zich in de *schaal* status bevindt met een nieuw aantal van *5* knoop punten:

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

Het duurt enkele minuten voordat de schaal bewerking is voltooid.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Een specifieke knooppunt groep automatisch schalen door de automatische cluster schaalr in te scha kelen

AKS biedt een afzonderlijke functie voor het automatisch schalen van knooppunt groepen met een functie die de automatische [cluster schaalr](cluster-autoscaler.md)wordt genoemd. Deze functie kan worden ingeschakeld per knooppunt groep met unieke minimum-en maximum schaal aantallen per knooppunt groep. Meer informatie over [het gebruik van de cluster-automatische schaal functie per knooppunt groep](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Een knooppunt groep verwijderen

Als u een pool niet meer nodig hebt, kunt u deze verwijderen en de onderliggende VM-knoop punten verwijderen. Als u een knooppunt groep wilt verwijderen, gebruikt u de opdracht [AZ AKS node pool delete][az-aks-nodepool-delete] en geeft u de naam van de knooppunt groep op. In het volgende voor beeld worden de *mynoodepool* die zijn gemaakt in de vorige stappen, verwijderd:

> [!CAUTION]
> Er zijn geen herstel opties voor gegevens verlies die kunnen optreden wanneer u een knooppunt groep verwijdert. Als er geen peulen kunnen worden gepland voor andere knooppunt groepen, zijn deze toepassingen niet beschikbaar. Zorg ervoor dat u geen knooppunt groep verwijdert wanneer in-use-toepassingen geen gegevens back-ups hebben of de mogelijkheid om uit te voeren op andere knooppunt groepen in uw cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

In de volgende voorbeeld uitvoer van de opdracht [AZ AKS node pool List][az-aks-nodepool-list] wordt aangegeven dat *Mynodepool* de status *verwijderen* heeft:

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

Het duurt enkele minuten om de knoop punten en de knooppunt groep te verwijderen.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Een VM-grootte voor een knooppunt groep opgeven

In de vorige voor beelden voor het maken van een knooppunt groep is een standaard VM-grootte gebruikt voor de knoop punten die in het cluster zijn gemaakt. Een veelvoorkomend scenario is het maken van knooppunt groepen met verschillende VM-grootten en-mogelijkheden. U kunt bijvoorbeeld een knooppunt groep maken die knoop punten bevat met grote hoeveel heden CPU of geheugen, of een knooppunt groep die GPU-ondersteuning biedt. In de volgende stap maakt u [gebruik van taints en verdragen](#setting-nodepool-taints) om de Kubernetes scheduler te laten zien hoe de toegang tot het peul kan worden beperkt op deze knoop punten.

In het volgende voor beeld maakt u een op GPU gebaseerde knooppunt groep die gebruikmaakt van de *Standard_NC6* VM-grootte. Deze Vm's worden aangedreven door de NVIDIA Tesla K80-kaart. Zie [grootten voor virtuele Linux-machines in azure][vm-sizes]voor meer informatie over de beschik bare VM-grootten.

Maak een knooppunt groep met de opdracht [AZ AKS node pool add][az-aks-nodepool-add] opnieuw. Geef deze keer de naam *gpunodepool* op en gebruik de `--node-vm-size` para meter om de grootte van de *Standard_NC6* op te geven:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

In de volgende voorbeeld uitvoer van de opdracht [AZ AKS node pool List][az-aks-nodepool-list] wordt aangegeven dat *Gpunodepool* knoop punten *maakt* met de opgegeven *VmSize*:

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

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Een Taint, label of tag voor een knooppunt groep opgeven

### <a name="setting-nodepool-taints"></a>Nodepool taints instellen

Wanneer u een knooppunt groep maakt, kunt u taints, labels of Tags toevoegen aan die knooppunt groep. Wanneer u een Taint, label of tag toevoegt, krijgen alle knoop punten in die knooppunt groep ook de Taint, het label of de tag.

Gebruik [AZ AKS nodepool add][az-aks-nodepool-add]om een knooppunt groep met een Taint te maken. Geef de naam *taintnp* op en gebruik de `--node-taints` para meter om *SKU = GPU: schema* voor de Taint op te geven.

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
> Een Taint kan alleen worden ingesteld voor knooppunt groepen tijdens het maken van een knooppunt groep.

In de volgende voorbeeld uitvoer van de opdracht [AZ AKS nodepool List][az-aks-nodepool-list] ziet u dat *Taintnp* knoop punten *maakt* met de opgegeven *nodeTaints*:

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

De Taint-informatie is zichtbaar in Kubernetes voor het afhandelen van plannings regels voor knoop punten. De Kubernetes scheduler kan taints en verdragen gebruiken om te beperken welke workloads op knoop punten kunnen worden uitgevoerd.

* Een **Taint** wordt toegepast op een knoop punt dat aangeeft dat alleen een specifiek peul kan worden gepland.
* Vervolgens wordt er een **verdragen** toegepast op een pod waarmee de Taint van een knoop punt kunnen worden *toegestaan* .

Zie [Best Practices for Advanced scheduler-functies in AKS][taints-tolerations] voor meer informatie over het gebruik van geavanceerde Kubernetes-functies.

In de vorige stap hebt u de *SKU = GPU:* Taint toegepast tijdens het maken van de knooppunt groep. In het volgende voor beeld van een YAML-manifest wordt met behulp van een tolerantie toegestaan dat de Kubernetes scheduler een NGINX pod kan uitvoeren op een knoop punt in die knooppunt groep.

Maak een bestand `nginx-toleration.yaml` met de naam en kopieer het in het volgende voor beeld YAML:

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

Plan de Pod met behulp van de `kubectl apply -f nginx-toleration.yaml` opdracht:

```console
kubectl apply -f nginx-toleration.yaml
```

Het duurt een paar seconden om de Pod te plannen en de NGINX-installatie kopie op te halen. Gebruik de [kubectl pod opdracht beschrijven][kubectl-describe] om de status van de pod weer te geven. In de volgende verkorte voorbeeld uitvoer ziet u de *SKU = GPU:* de verdragen waarbij geen schema wordt toegepast. In de sectie Events heeft de scheduler de pod toegewezen aan het knoop punt *AKS-taintnp-28993262-vmss000000* :

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

Alleen een van de peulen waarvoor deze tolerantie is toegepast, kan worden gepland op knoop punten in *taintnp*. Elk ander pod zou worden gepland in de *nodepool1* -knooppunt groep. Als u extra knooppunt groepen maakt, kunt u extra taints en toleranties gebruiken om te beperken wat er op deze knooppunt bronnen kan worden gepland.

### <a name="setting-nodepool-labels"></a>Nodepool-labels instellen

U kunt ook labels toevoegen aan een knooppunt groep tijdens het maken van de knooppunt groep. Labels die in de knooppunt groep zijn ingesteld, worden toegevoegd aan elk knoop punt in de knooppunt groep. Deze [labels zijn zichtbaar in Kubernetes][kubernetes-labels] voor het afhandelen van plannings regels voor knoop punten.

Gebruik [AZ AKS nodepool add][az-aks-nodepool-add]om een knooppunt groep met een label te maken. Geef de naam *labelnp* op en gebruik de `--labels` para meter om *Dept = it* en *CostCenter = 9999* voor labels op te geven.

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
> Label kan alleen worden ingesteld voor knooppunt groepen tijdens het maken van een knooppunt groep. Labels moeten ook een sleutel-waardepaar zijn en een [geldige syntaxis][kubernetes-label-syntax]hebben.

In de volgende voorbeeld uitvoer van de opdracht [AZ AKS nodepool List][az-aks-nodepool-list] ziet u dat *Labelnp* knoop punten *maakt* met de opgegeven *nodeLabels*:

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

### <a name="setting-nodepool-azure-tags"></a>Nodepool Azure-Tags instellen

U kunt een Azure-tag Toep assen op knooppunt groepen in uw AKS-cluster. Labels die worden toegepast op een knooppunt groep, worden toegepast op elk knoop punt in de knooppunt groep en blijven behouden via upgrades. Labels worden ook toegepast op nieuwe knoop punten die worden toegevoegd aan een knooppunt groep tijdens scale-out bewerkingen. Het toevoegen van een tag kan helpen bij taken zoals het bijhouden van het beleid of het schatten van de kosten.

Azure Tags heeft sleutels die niet hoofdletter gevoelig zijn voor bewerkingen, zoals bij het ophalen van een tag door te zoeken in de sleutel. In dit geval wordt een tag met de opgegeven sleutel bijgewerkt of opgehaald, ongeacht het hoofdletter gebruik. Label waarden zijn hoofdletter gevoelig.

Als in AKS meerdere labels zijn ingesteld met identieke sleutels, maar met een ander hoofdletter gebruik, wordt de gebruikte tag de eerste in alfabetische volg orde. Bijvoorbeeld `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` resultaten in `Key1` en `val1` worden ingesteld.

Maak een knooppunt groep met behulp van de [AZ AKS nodepool add][az-aks-nodepool-add]. Geef de naam *tagnodepool* op en gebruik de `--tag` para meter om *Dept = it* en *CostCenter = 9999* voor labels op te geven.

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
> U kunt ook de `--tags` para meter gebruiken bij het gebruik van [AZ AKS nodepool update][az-aks-nodepool-update] opdracht en tijdens het maken van het cluster. Tijdens het maken van het cluster `--tags` past de para meter de tag toe op de eerste knooppunt groep die met het cluster is gemaakt. Alle label namen moeten voldoen aan de beperkingen in [Tags gebruiken om uw Azure-resources te organiseren][tag-limitation]. Als u een knooppunt groep met de para meter bijwerkt `--tags` , worden alle bestaande label waarden bijgewerkt en worden nieuwe labels toegevoegd. Als uw knooppunt groep bijvoorbeeld *Dept = it* en *CostCenter = 9999* heeft voor Tags en u deze hebt bijgewerkt met *team = dev* en *CostCenter = 111* voor labels, hebt u nodepool *Dept = it*, *CostCenter = 111* en *team = dev* for Tags.

In de volgende voorbeeld uitvoer van de opdracht [AZ AKS nodepool List][az-aks-nodepool-list] ziet u dat *Tagnodepool* knoop punten *maakt* met de opgegeven *tag*:

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

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Knooppunt Pools beheren met een resource manager-sjabloon

Wanneer u een Azure Resource Manager sjabloon gebruikt om resources te maken en te beheren, kunt u de instellingen in uw sjabloon doorgaans bijwerken en opnieuw implementeren om de resource bij te werken. Met knooppunt Pools in AKS kan het oorspronkelijke knooppunt groeps profiel niet worden bijgewerkt nadat het AKS-cluster is gemaakt. Dit gedrag houdt in dat u een bestaande resource manager-sjabloon niet kunt bijwerken, een wijziging kunt aanbrengen in de knooppunt groepen en opnieuw moet implementeren. In plaats daarvan moet u een afzonderlijke resource manager-sjabloon maken waarmee alleen de knooppunt groepen voor een bestaand AKS-cluster worden bijgewerkt.

Maak een sjabloon, zoals `aks-agentpools.json` en plak het volgende voor beeld-manifest. Met deze voorbeeld sjabloon worden de volgende instellingen geconfigureerd:

* Hiermee werkt u de *Linux* -knooppunt groep met de naam *myagentpool* bij om drie knoop punten uit te voeren.
* Hiermee stelt u de knoop punten in de knooppunt groep in op het uitvoeren van Kubernetes-versie *1.15.7*.
* Hiermee definieert u de knooppunt grootte als *Standard_DS2_v2*.

Bewerk deze waarden als u wilt dat er knooppunt groepen worden bijgewerkt, toegevoegd of verwijderd:

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

Implementeer deze sjabloon met de opdracht [AZ Deployment Group Create][az-deployment-group-create] , zoals wordt weer gegeven in het volgende voor beeld. U wordt gevraagd om de bestaande naam en locatie van het AKS-cluster:

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> U kunt een tag toevoegen aan de knooppunt groep door de eigenschap *tag* in de sjabloon toe te voegen, zoals wordt weer gegeven in het volgende voor beeld.
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

Het kan een paar minuten duren voordat u uw AKS-cluster bijwerkt, afhankelijk van de instellingen van de knooppunt groep en de bewerkingen die u in uw Resource Manager-sjabloon definieert.

## <a name="assign-a-public-ip-per-node-for-your-node-pools"></a>Een openbaar IP-adres per knoop punt toewijzen voor uw knooppunt groepen

AKS-knoop punten vereisen geen eigen open bare IP-adressen voor communicatie. Scenario's kunnen echter knoop punten in een knooppunt groep vereisen om hun eigen toegewezen open bare IP-adressen te ontvangen. Een veelvoorkomend scenario is voor gaming werk belastingen, waarbij een-console een directe verbinding met een virtuele machine in de Cloud moet maken om de hops te minimaliseren. Dit scenario kan worden bereikt op AKS met behulp van het open bare IP-adres van het knoop punt.

Maak eerst een nieuwe resource groep.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Maak een nieuw AKS-cluster en koppel een openbaar IP-adres voor uw knoop punten. Elk knoop punt in de knooppunt groep ontvangt een uniek openbaar IP-adres. U kunt dit controleren door te kijken naar de exemplaren van de virtuele-machine schaal sets.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

Voor bestaande AKS-clusters kunt u ook een nieuwe knooppunt groep toevoegen en een openbaar IP-adres voor uw knoop punten koppelen.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

### <a name="use-a-public-ip-prefix"></a>Een openbaar IP-voor voegsel gebruiken

Er zijn een aantal [voor delen van het gebruik van een openbaar IP-voor voegsel][public-ip-prefix-benefits]. AKS ondersteunt het gebruik van adressen van een bestaand openbaar IP-voor voegsel voor uw knoop punten door de resource-ID met de vlag door te geven `node-public-ip-prefix` bij het maken van een nieuw cluster of het toevoegen van een knooppunt groep.

Maak eerst een openbaar IP-voor voegsel met [AZ Network Public-IP prefix Create][az-public-ip-prefix-create]:

```azurecli-interactive
az network public-ip prefix create --length 28 --location eastus --name MyPublicIPPrefix --resource-group MyResourceGroup3
```

Bekijk de uitvoer en noteer het `id` voor voegsel:

```output
{
  ...
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix",
  ...
}
```

Bij het maken van een nieuw cluster of het toevoegen van een nieuwe knooppunt groep, gebruikt u de vlag `node-public-ip-prefix` en geeft u de resource-id van het voor voegsel door.

```azurecli-interactive
az aks create -g MyResourceGroup3 -n MyManagedCluster -l eastus --enable-node-public-ip --node-public-ip-prefix /subscriptions/<subscription-id>/resourcegroups/MyResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix
```

### <a name="locate-public-ips-for-nodes"></a>Open bare Ip's voor knoop punten zoeken

U kunt de open bare Ip's voor uw knoop punten op verschillende manieren vinden:

* Gebruik de Azure CLI [-opdracht AZ vmss List-instance-open bare ip's][az-list-ips].
* Gebruik [Power shell-of bash-opdrachten][vmss-commands]. 
* U kunt ook de open bare Ip's weer geven in de Azure Portal door de instanties in de Schaalset voor virtuele machines te bekijken.

> [!Important]
> De [resource groep node][node-resource-group] bevat de knoop punten en hun open bare ip's. Gebruik de resource groep knoop punt bij het uitvoeren van opdrachten om de open bare Ip's voor uw knoop punten te vinden.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel hebt u een AKS-cluster gemaakt dat op GPU gebaseerde knoop punten bevat. U kunt de *gpunodepool* of het hele AKS-cluster verwijderen om onnodige kosten te verminderen.

Als u de op GPU gebaseerde knooppunt groep wilt verwijderen, gebruikt u de opdracht [AZ AKS nodepool delete][az-aks-nodepool-delete] , zoals in het volgende voor beeld wordt weer gegeven:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Als u het cluster zelf wilt verwijderen, gebruikt u de opdracht [AZ Group delete][az-group-delete] om de resource groep AKS te verwijderen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

U kunt ook het extra cluster verwijderen dat u hebt gemaakt voor het scenario voor het open bare IP-adres voor knooppunt groepen.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [systeem knooppunt groepen][use-system-pool].

In dit artikel hebt u geleerd hoe u meerdere knooppunt groepen kunt maken en beheren in een AKS-cluster. Zie [Aanbevolen procedures voor geavanceerde functies van scheduler in AKS][operator-best-practices-advanced-scheduler]voor meer informatie over het beheren van de verschillende knooppunt groepen.

Zie [een Windows Server-container maken in AKS][aks-windows]om Windows Server-container knooppunt groepen te maken en te gebruiken.

Gebruik [proximity-plaatsings groepen][reduce-latency-ppg] om de latentie voor uw AKS-toepassingen te verminderen.

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
