---
title: Een spot-knooppuntgroep toevoegen aan een AKS-cluster (Azure Kubernetes Service)
description: Meer informatie over het toevoegen van een spot-knooppuntgroep aan Azure Kubernetes Service cluster (AKS).
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: f46a421ae2ad1a4d9c590c7e0b47784760ebcb9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782797"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Een spot-knooppuntgroep toevoegen aan een AKS-cluster (Azure Kubernetes Service)

Een spot-knooppuntgroep is een knooppuntgroep die wordt gebruikt door een [virtuele-machineschaalset voor spot-machines.][vmss-spot] Door spot-VM's te gebruiken voor knooppunten met uw AKS-cluster kunt u profiteren van niet-gebruikte capaciteit in Azure tegen aanzienlijke kostenbesparingen. De hoeveelheid beschikbare niet-beschikbare capaciteit varieert op basis van veel factoren, waaronder knooppuntgrootte, regio en tijdstip van de dag.

Bij het implementeren van een spot-knooppuntgroep wijst Azure de spot-knooppunten toe als er capaciteit beschikbaar is. Maar er is geen SLA voor de spot-knooppunten. Een spot-schaalset die een back-van-de-spot-knooppuntgroep is, wordt geïmplementeerd in één foutdomein en biedt geen garanties voor hoge beschikbaarheid. Op elk moment dat Azure de capaciteit terug nodig heeft, worden spot-knooppunten door de Azure-infrastructuur verwijderd.

Spot-knooppunten zijn geweldig voor workloads die onderbrekingen, vroege beëindigingen of uitzettingen kunnen verwerken. Workloads zoals batchverwerkingstaken, ontwikkel- en testomgevingen en grote rekenworkloads zijn bijvoorbeeld goede kandidaten om te worden gepland op een spot-knooppuntgroep.

In dit artikel voegt u een secundaire spot-knooppuntgroep toe aan een bestaand Azure Kubernetes Service cluster (AKS).

In dit artikel wordt ervan uitgenomen dat u basiskennis heeft van Kubernetes en Azure Load Balancer concepten. Zie [Kubernetes-kernconcepten voor Azure Kubernetes Service (AKS)][kubernetes-concepts] voor meer informatie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Wanneer u een cluster maakt om een spot-knooppuntgroep te gebruiken, moet dat cluster ook Virtual Machine Scale Sets gebruiken voor knooppuntgroepen en de *Standard* SKU-load balancer. U moet ook een extra knooppuntgroep toevoegen nadat u uw cluster hebt gemaakt om een spot-knooppuntgroep te gebruiken. Het toevoegen van een extra knooppuntgroep wordt in een latere stap behandeld.

Voor dit artikel moet u Azure CLI versie 2.14 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

### <a name="limitations"></a>Beperkingen

De volgende beperkingen gelden wanneer u AKS-clusters met een spot-knooppuntgroep maakt en beheert:

* Een spot-knooppuntgroep kan niet de standaardknooppuntgroep van het cluster zijn. Een spot-knooppuntgroep kan alleen worden gebruikt voor een secundaire pool.
* U kunt een spot-knooppuntgroep niet upgraden omdat spot-knooppuntgroepen cordon en drain niet kunnen garanderen. U moet uw bestaande spot-knooppuntgroep vervangen door een nieuwe om bewerkingen uit te voeren, zoals het upgraden van de Kubernetes-versie. Als u een spot-knooppuntgroep wilt vervangen, maakt u een nieuwe spot-knooppuntgroep met een andere versie van Kubernetes, wacht u tot de status *Gereed* is en verwijdert u vervolgens de oude knooppuntgroep.
* De besturingsvlak en knooppuntgroepen kunnen niet tegelijkertijd worden bijgewerkt. U moet deze afzonderlijk upgraden of de spot-knooppuntgroep verwijderen om tegelijkertijd het besturingsvlak en de resterende knooppuntgroepen bij te werken.
* Een spot-knooppuntgroep moet de Virtual Machine Scale Sets.
* U kunt ScaleSetPriority of SpotMaxPrice niet wijzigen nadat u deze hebt gemaakt.
* Bij het instellen van SpotMaxPrice moet de waarde -1 of een positieve waarde met maximaal vijf decimalen zijn.
* Een spot-knooppuntgroep heeft het label *kubernetes.azure.com/scalesetpriority:spot*, de taint *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* en systeempods hebben anti-affiniteit.
* U moet een [overeenkomstige toleratie toevoegen om][spot-toleration] workloads in een spot-knooppuntgroep te plannen.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Een spot-knooppuntgroep toevoegen aan een AKS-cluster

U moet een spot-knooppuntgroep toevoegen aan een bestaand cluster met meerdere knooppuntgroepen ingeschakeld. Meer informatie over het maken van een AKS-cluster met meerdere knooppuntgroepen is hier [beschikbaar.][use-multiple-node-pools]

Maak een knooppuntgroep met behulp van [de az aks nodepool add.][az-aks-nodepool-add]
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

Standaard maakt u een knooppuntgroep  met de prioriteit Regelmatig *in* uw AKS-cluster wanneer u een cluster met meerdere knooppuntgroepen maakt. Met de bovenstaande opdracht wordt een hulpknooppuntgroep toegevoegd aan een bestaand AKS-cluster met *de prioriteit* *Spot*. De *prioriteit* van *Spot maakt* van de knooppuntgroep een spot-knooppuntgroep. De *parameter eviction-policy* is in het *bovenstaande* voorbeeld ingesteld op Verwijderen. Dit is de standaardwaarde. Wanneer u het verwijderbeleid [in][eviction-policy] stelt op *Verwijderen,* worden knooppunten in de onderliggende schaalset van de knooppuntgroep verwijderd wanneer ze worden verwijderd. U kunt het beleid voor het uitzettingsbeleid ook instellen op *Toewijzing van de toewijzing op .* Wanneer u het uitzettingsbeleid in stelt op Toewijzing van de toewijzing van de *toewijzing,* worden knooppunten in de onderliggende schaalset ingesteld op de status gestopte toewijzing van de toewijzing bij het verwijderen. Knooppunten met de status gestopt-de toewijzing van de toewijzing worden meegerekend in uw rekenquotum en kunnen problemen veroorzaken met het schalen of upgraden van clusters. De *waarden voor* prioriteit en het beleid voor de *uitzetting* kunnen alleen worden ingesteld tijdens het maken van de knooppuntgroep. Deze waarden kunnen later niet worden bijgewerkt.

Met de opdracht wordt ook de [automatische schaalverdeder van clusters][cluster-autoscaler]in staat gemaakt. Dit wordt aanbevolen voor gebruik met spot-knooppuntgroepen. Op basis van de workloads die in uw cluster worden uitgevoerd, wordt de automatische schaalvergroting van clusters omhoog geschaald en wordt het aantal knooppunten in de knooppuntgroep omlaag geschaald. Voor spot-knooppuntgroepen schaalt de automatische schaalvergroting van clusters het aantal knooppunten omhoog na een uitzetting als er nog extra knooppunten nodig zijn. Als u het maximum aantal knooppunten wijzigt dat een knooppuntgroep kan hebben, moet u ook de waarde aanpassen die is gekoppeld `maxCount` aan de automatische schaalverdeder voor clusters. Als u geen automatische schaalverdedering voor clusters gebruikt, wordt de spot-pool na het verwijderen uiteindelijk tot nul verkleind en is een handmatige bewerking vereist om extra spot-knooppunten te ontvangen.

> [!Important]
> Plan alleen workloads op spot-knooppuntgroepen die onderbrekingen kunnen afhandelen, zoals batchverwerkingstaken en testomgevingen. Het is raadzaam om [taints en toleraties][taints-tolerations] in te stellen op uw spot-knooppuntgroep om ervoor te zorgen dat alleen werkbelastingen die knooppuntverzettingen kunnen verwerken, worden gepland in een spot-knooppuntgroep. Met de bovenstaande opdracht ny standaard voegt u bijvoorbeeld een taint van *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* zodat alleen pods met een bijbehorende toleratie worden gepland op dit knooppunt.

## <a name="verify-the-spot-node-pool"></a>De spot-knooppuntgroep controleren

Controleren of uw knooppuntgroep is toegevoegd als een spot-knooppuntgroep:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Controleer *of scaleSetPriority* *spot* is.

Als u wilt plannen dat een pod wordt uitgevoerd op een spot-knooppunt, voegt u een toleratie toe die overeenkomt met de taint die op uw spot-knooppunt is toegepast. In het volgende voorbeeld ziet u een deel van een yaml-bestand dat een toleratie definieert die overeenkomt met een *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* taint die in de vorige stap is gebruikt.

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

Wanneer een pod met deze toleratie wordt geïmplementeerd, kan Kubernetes de pod op de knooppunten plannen waarop de taint is toegepast.

## <a name="max-price-for-a-spot-pool"></a>Maximumprijs voor een spot-pool
[Prijzen voor spot-exemplaren zijn variabel,][pricing-spot]op basis van regio en SKU. Zie Prijzen voor Linux en Windows [voor][pricing-linux] meer [informatie.][pricing-windows]

Met variabele prijzen hebt u de mogelijkheid om een maximumprijs in te stellen, in Amerikaanse dollars (USD), met maximaal 5 decimalen. De waarde *0,98765* is bijvoorbeeld een maximumprijs van $ 0,98765 USD per uur. Als u de maximumprijs in stelt *op -1,* wordt het exemplaar niet onbetaald op basis van de prijs. De prijs voor het exemplaar is de huidige prijs voor Spot of de prijs voor een standaard-instantie, wat kleiner is, zolang er capaciteit en quotum beschikbaar zijn.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een spot-knooppuntgroep toevoegt aan een AKS-cluster. Zie Best practices for advanced scheduler features in AKS (Best practices voor geavanceerde [scheduler-functies in AKS)][operator-best-practices-advanced-scheduler]voor meer informatie over het beheer van pods in knooppuntgroepen.

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
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