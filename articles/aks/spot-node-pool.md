---
title: Een spot-knooppuntgroep toevoegen aan een AKS-cluster (Azure Kubernetes Service)
description: Meer informatie over het toevoegen van een steun knooppunt groep aan een Azure Kubernetes service-cluster (AKS).
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: 7f838b2a78f1c6993aa247f2944d4f2a9b1e9556
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181122"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Een spot-knooppuntgroep toevoegen aan een AKS-cluster (Azure Kubernetes Service)

Een steun knooppunt groep is een knooppunt groep die wordt ondersteund door een [virtuele-machine schaalset][vmss-spot]. Door gebruik te maken van invoeg-Vm's voor knoop punten met uw AKS-cluster kunt u profiteren van ongebruikte capaciteit in azure tegen aanzienlijke kosten besparingen. De hoeveelheid beschik bare ongebruikte capaciteit kan variëren op basis van veel factoren, zoals de grootte van het knoop punt, de regio en het tijdstip van de dag.

Wanneer u een steun knooppunt groep implementeert, worden in azure de spot knooppunten toegewezen als de capaciteit beschikbaar is. Er is echter geen SLA voor de spot knooppunten. Een spot schaalset die een back-up maakt van de steun knooppunt groep wordt geïmplementeerd in één fout domein en biedt geen garantie voor hoge Beschik baarheid. Op elk gewenst moment waarop Azure de capaciteit nodig heeft, verwijdert de Azure-infra structuur steun knooppunten.

Spot knooppunten zijn uitstekend voor workloads die onderbrekingen, vroege beëindigingen of verwijderingen kunnen afhandelen. Werk belastingen zoals taken voor batch verwerking, ontwikkelings-en test omgevingen en grote reken werkbelastingen kunnen goede kandidaten zijn om te worden gepland op een steun knooppunt groep.

In dit artikel voegt u een secundaire steun knooppunt groep toe aan een bestaand AKS-cluster (Azure Kubernetes service).

In dit artikel wordt ervan uitgegaan dat u basis informatie krijgt over Kubernetes-en Azure Load Balancer concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service (AKS)][kubernetes-concepts] voor meer informatie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Wanneer u een cluster maakt voor het gebruik van een steun knooppunt groep, moet dat cluster ook Virtual Machine Scale Sets gebruiken voor knooppunt groepen en de *standaard* -SKU Load Balancer. U moet ook een extra knooppunt groep toevoegen nadat u het cluster hebt gemaakt voor het gebruik van een steun knooppunt groep. Het toevoegen van een extra knooppunt groep wordt in een latere stap besproken.

Voor dit artikel moet u de Azure CLI-versie 2,14 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="limitations"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u AKS-clusters maakt en beheert met een steun knooppunt groep:

* Een steun knooppunt groep kan niet de standaard knooppunt groep van het cluster zijn. Een steun knooppunt groep kan alleen worden gebruikt voor een secundaire groep.
* U kunt een steun knooppunt groep niet upgraden, omdat spot knooppunt groepen geen Cordon en afvoer kunnen garanderen. U moet uw bestaande spot-knooppunt groep vervangen door een nieuwe taak voor het uitvoeren van bewerkingen, zoals het bijwerken van de Kubernetes-versie. Als u een steun knooppunt groep wilt vervangen, maakt u een nieuwe steun knooppunt groep met een andere versie van Kubernetes, wacht u tot de status *gereed* is en verwijdert u de oude knooppunt groep.
* De besturings vlak-en knooppunt groepen kunnen op hetzelfde moment niet worden bijgewerkt. U moet deze afzonderlijk upgraden of de groep met steun knooppunten verwijderen om het besturings vlak en de resterende knooppunt groepen tegelijk te upgraden.
* Een steun knooppunt groep moet Virtual Machine Scale Sets gebruiken.
* U kunt ScaleSetPriority of SpotMaxPrice niet wijzigen nadat het is gemaakt.
* Bij het instellen van SpotMaxPrice moet de waarde-1 of een positieve waarde met Maxi maal vijf decimalen zijn.
* Een steun knooppunt groep heeft het label *kubernetes.Azure.com/scalesetpriority:spot*, de taint- *kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* en het systeem-Peul heeft een anti-affiniteit.
* U moet een [overeenkomstige tolerantie][spot-toleration] toevoegen voor het plannen van workloads op een steun knooppunt groep.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Een spot-knooppuntgroep toevoegen aan een AKS-cluster

U moet een steun knooppunt groep toevoegen aan een bestaand cluster waarvoor meerdere knooppunt groepen zijn ingeschakeld. Meer informatie over het maken van een AKS-cluster met meerdere knooppunt groepen is [hier][use-multiple-node-pools]beschikbaar.

Maak een knooppunt groep met behulp van de [AZ AKS nodepool add][az-aks-nodepool-add].
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Standaard maakt u een knooppunt groep met een *prioriteit* van *regel matig* in uw AKS-cluster wanneer u een cluster met meerdere knooppunt groepen maakt. Met de bovenstaande opdracht wordt een hulp knooppunt groep toegevoegd aan een bestaand AKS-cluster met de *prioriteit* van *Spot*. De *prioriteit* van *Spot* maakt de knooppunt groep een steun knooppunt groep. De para meter voor *verwijderings beleid* is ingesteld op *verwijderen* in het bovenstaande voor beeld. Dit is de standaard waarde. Wanneer u het *verwijderings* [beleid][eviction-policy] instelt op verwijderen, worden knoop punten in de onderliggende schaalset van de knooppunt groep verwijderd wanneer ze worden gewist. U kunt ook het verwijderings beleid zo instellen dat de *toewijzing* wordt ongedaan gemaakt. Wanneer u het verwijderings beleid instelt op *opheffen*, worden knoop punten in de onderliggende schaalset tijdens het verwijderen ingesteld op de status stopped-disallocated. Knoop punten in het aantal statussen waarbij de toewijzing is gestopt, worden vergeleken met uw reken quotum en kunnen problemen veroorzaken bij het schalen of upgraden van clusters. De waarden voor *prioriteit* en *verwijderings beleid* kunnen alleen worden ingesteld tijdens het maken van een knooppunt groep. Deze waarden kunnen later niet worden bijgewerkt.

Met de opdracht wordt ook de automatische cluster functie voor [clusters][cluster-autoscaler]ingeschakeld. deze optie wordt aanbevolen voor gebruik met een punt groep met steun knooppunten. Op basis van de werk belastingen die worden uitgevoerd in uw cluster, wordt het aantal knoop punten in de knooppunt groep omhoog en omlaag geschaald. In het geval van een spot knooppunt groep wordt het aantal knoop punten na een verwijdering door de cluster-automatische schaal aanpassing uitgebreid als extra knoop punten nog nodig zijn. Als u het maximum aantal knoop punten wijzigt dat een knooppunt groep kan hebben, moet u ook de `maxCount` waarde die is gekoppeld aan de cluster-automatische schaal aanpassen. Als u geen automatische cluster-scaleer gebruikt, wordt de spot groep uiteindelijk afgetrokken van nul en moet er een hand matige bewerking worden uitgevoerd voor het ontvangen van extra steun knooppunten.

> [!Important]
> Plan alleen werk belastingen op steun knooppunt Pools die onderbrekingen kunnen afhandelen, zoals batch verwerkings taken en test omgevingen. Het is raadzaam om [taints en toleranties][taints-tolerations] in te stellen voor uw steun knooppunt groep om ervoor te zorgen dat alleen werk belastingen die de verwijdering van knoop punten kunnen verwerken, worden gepland op een steun knooppunt groep. Met de bovenstaande opdracht NY default wordt bijvoorbeeld een Taint van *kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* toegevoegd, zodat alleen een van de waarden van het overeenkomstige aantal verdragen op dit knoop punt wordt gepland.

## <a name="verify-the-spot-node-pool"></a>De plaatsings knooppunt groep controleren

Controleren of de knooppunt groep is toegevoegd als een steun knooppunt groep:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Bevestigen dat *scaleSetPriority* is *Spot*.

Als u een pod wilt plannen om uit te voeren op een steun knooppunt, voegt u een overeenkomst toe die overeenkomt met de Taint die op uw steun knooppunt wordt toegepast. In het volgende voor beeld ziet u een deel van een yaml-bestand dat een overeenkomst definieert die overeenkomt met een *kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* Taint die in de vorige stap wordt gebruikt.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Wanneer er een pod met deze tolerantie wordt geïmplementeerd, kan Kubernetes de pod plannen op de knoop punten waarop de Taint is toegepast.

## <a name="max-price-for-a-spot-pool"></a>Maximale prijs voor een steun groep
[Prijzen voor spot instanties zijn variabel][pricing-spot], op basis van de regio en de SKU. Zie prijzen voor [Linux][pricing-linux] en [Windows][pricing-windows]voor meer informatie.

Met variabele prijzen kunt u een maximum prijs instellen, in Amerikaanse dollars (USD), met Maxi maal vijf decimalen. De waarde *0,98765* zou bijvoorbeeld een maximum prijs van $0,98765 USD per uur zijn. Als u de maximum prijs instelt op *-1*, wordt het exemplaar niet verwijderd op basis van de prijs. De prijs voor de instantie is de huidige prijs voor steun of de prijs voor een standaard exemplaar, afhankelijk van wat minder is, zolang er capaciteit en quota beschikbaar zijn.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een steun knooppunt groep kunt toevoegen aan een AKS-cluster. Zie [Aanbevolen procedures voor geavanceerde functies van scheduler in AKS][operator-best-practices-advanced-scheduler]voor meer informatie over het beheren van de verschillende knooppunt groepen.

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md