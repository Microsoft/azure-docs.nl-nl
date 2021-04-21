---
title: Best practices voor scheduler-functies
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de best practices voor clusteroperator voor het gebruik van geavanceerde scheduler-functies, zoals taints en toleraties, knooppuntkiezers en affiniteit, of affiniteit tussen pods en anti-affiniteit in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 971916c3fc903ff5d69db2e0f82fd884acf807b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831578"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Best practices voor geavanceerde planningsfuncties in Azure Kubernetes Service (AKS)

Bij het beheren van clusters in Azure Kubernetes Service (AKS) moet u vaak teams en workloads isoleren. Met geavanceerde functies van de Kubernetes-scheduler kunt u het volgende bepalen:
* Welke pods kunnen worden gepland op bepaalde knooppunten.
* Hoe toepassingen met meerdere pods op de juiste wijze over het cluster kunnen worden verdeeld. 

Dit artikel met best practices richt zich op geavanceerde Kubernetes-planningsfuncties voor clusteroperators. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Gebruik taints en toleraties om te beperken welke pods op knooppunten kunnen worden gepland.
> * Geef pods de voorkeur om uit te voeren op bepaalde knooppunten met knooppunt-selectors of knooppunt-affiniteit.
> * Splitsen of groepeert u pods met affiniteit tussen pods of anti-affiniteit.

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Toegewezen knooppunten bieden met behulp van taints en toleraties

> **Richtlijnen voor best practice:** 
>
> Beperk de toegang voor resource-intensieve toepassingen, zoals toegangsbeheerscontrollers, tot specifieke knooppunten. Houd knooppuntbronnen beschikbaar voor workloads waarvoor deze nodig zijn en sta geen planning van andere workloads op de knooppunten toe.

Wanneer u uw AKS-cluster maakt, kunt u knooppunten implementeren met GPU-ondersteuning of een groot aantal krachtige CPU's. U kunt deze knooppunten gebruiken voor workloads voor grote gegevensverwerking, zoals machine learning (ML) of kunstmatige intelligentie (AI). 

Omdat de implementatie van deze knooppuntresourcehardware doorgaans duur is, beperkt u de werkbelastingen die op deze knooppunten kunnen worden gepland. In plaats daarvan moet u een aantal knooppunten in het cluster gebruiken om ingress-services uit te voeren en andere werkbelastingen te voorkomen.

Deze ondersteuning voor verschillende knooppunten wordt geboden met behulp van meerdere knooppuntgroepen. Een AKS-cluster biedt een of meer knooppuntgroepen.

De Kubernetes-scheduler maakt gebruik van taints en toleraties om te beperken welke workloads op knooppunten kunnen worden uitgevoerd.

* Pas een **taint toe** op een knooppunt om aan te geven dat alleen specifieke pods erop kunnen worden gepland.
* Pas vervolgens een **toleratie toe** op  een pod, zodat deze de taint van een knooppunt kan tolereren.

Wanneer u een pod implementeert in een AKS-cluster, worden in Kubernetes alleen pods gepland op knooppunten waarvan de taint is uitgelijnd met de -toleratie. Stel dat u een knooppuntgroep hebt toegevoegd in uw AKS-cluster voor knooppunten met GPU-ondersteuning. U definieert de naam, *zoals gpu*, en vervolgens een waarde voor planning. Als u deze waarde instelt op *NoSchedule,* wordt de Kubernetes-scheduler beperkt tot het plannen van pods met niet-gedefinieerde toleratie op het knooppunt.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

Als er een taint wordt toegepast op knooppunten in de knooppuntgroep, definieert u een toleratie in de podspecificatie die planning op de knooppunten mogelijk maakt. In het volgende voorbeeld worden de en om de taint te tolereren die in de vorige stap is toegepast op de `sku: gpu` `effect: NoSchedule` knooppuntgroep:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Wanneer deze pod wordt geïmplementeerd met behulp van , kan Kubernetes de pod op de knooppunten plannen waarop `kubectl apply -f gpu-toleration.yaml` de taint is toegepast. Met deze logische isolatie kunt u de toegang tot resources in een cluster beheren.

Wanneer u taints toe passen, moet u samenwerken met uw toepassingsontwikkelaars en -eigenaren om ze in staat te stellen de vereiste toleraties in hun implementaties te definiëren.

Zie Create [and manage multiple node pools for a cluster in AKS][use-multiple-node-pools](Meerdere knooppuntgroepen maken en beheren voor een cluster in AKS) voor meer informatie over het gebruik van meerdere knooppuntgroepen in AKS.

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Gedrag van taints en toleraties in AKS

Wanneer u een knooppuntgroep upgradet in AKS, volgen taints en toleraties een ingesteld patroon omdat ze worden toegepast op nieuwe knooppunten:

#### <a name="default-clusters-that-use-vm-scale-sets"></a>Standaardclusters die gebruikmaken van VM-schaalsets
U kunt [een knooppuntgroep van][taint-node-pool] de AKS-API tainten zodat nieuw uitgeschaalde knooppunten API-opgegeven knooppunt-taints ontvangen.

Laten we ervan uitgaan dat:
1. U begint met een cluster met twee knooppunt: *knooppunt1* en *knooppunt2.* 
1. U upgradet de knooppuntgroep.
1. Er worden twee extra knooppunten gemaakt: *node3* *en node4.* 
1. De taints worden respectievelijk doorgegeven.
1. Het oorspronkelijke *knooppunt1* en *knooppunt2* worden verwijderd.

#### <a name="clusters-without-vm-scale-set-support"></a>Clusters zonder ondersteuning voor VM-schaalsets

Laten we nogmaals het volgende aannemen:
1. U hebt een cluster met twee knooppunt: *knooppunt1* en *knooppunt2.* 
1. U upgradet vervolgens de knooppuntgroep.
1. Er wordt een extra knooppunt gemaakt: *node3.*
1. De taints van *node1* worden toegepast op *node3.*
1. *node1* wordt verwijderd.
1. Er wordt *een nieuw knooppunt1* gemaakt om te vervangen door het *oorspronkelijke knooppunt1*.
1. De *node2-taints* worden toegepast op het nieuwe *knooppunt1*. 
1. *node2* wordt verwijderd.

In wezen *wordt knooppunt1* *knooppunt3* en *knooppunt2* het nieuwe *knooppunt1.*

Wanneer u een knooppuntgroep in AKS schaalt, worden taints en toleranties niet per se overgenomen.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Podplanning bepalen met behulp van knooppunt-selectors en affiniteit

> **Richtlijnen voor best practices** 
> 
> De planning van pods op knooppunten bepalen met behulp van knooppunt-selectors, knooppunt-affiniteit of affiniteit tussen pods. Met deze instellingen kan de Kubernetes-scheduler workloads logisch isoleren, zoals hardware in het knooppunt.

Taints en toleraties isoleren resources logisch met een harde afkap. Als de pod geen taint van een knooppunt tolereert, wordt deze niet gepland op het knooppunt.

U kunt ook knooppunt selectors gebruiken. U labelt knooppunten bijvoorbeeld om lokaal gekoppelde SSD-opslag of een grote hoeveelheid geheugen aan te geven en definieert vervolgens in de podspecificatie een knooppunt selector. Kubernetes plant die pods op een overeenkomend knooppunt.

In tegenstelling tot toleraties kunnen pods zonder een overeenkomende knooppunt selector nog steeds worden gepland op gelabelde knooppunten. Met dit gedrag kunnen ongebruikte resources op de knooppunten worden verbruikt, maar wordt prioriteit gegeven aan pods die de overeenkomende knooppunt selector definiëren.

Laten we eens kijken naar een voorbeeld van knooppunten met een grote hoeveelheid geheugen. Deze knooppunten geven prioriteit aan pods die een grote hoeveelheid geheugen aanvragen. Om ervoor te zorgen dat de resources niet inactief zijn, kunnen ook andere pods worden uitgevoerd. Met de volgende voorbeeldopdracht wordt een knooppuntgroep met het label *hardware=highmem toegevoegd* aan *myAKSCluster* in *myResourceGroup.* Alle knooppunten in die knooppuntgroep hebben dit label.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels hardware=highmem \
    --no-wait
```

Een podspecificatie voegt vervolgens de eigenschap toe om een knooppunt selector te definiëren die `nodeSelector` overeenkomt met het label dat is ingesteld op een knooppunt:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  nodeSelector:
      hardware: highmem
```

Wanneer u deze scheduler-opties gebruikt, moet u samenwerken met uw toepassingsontwikkelaars en -eigenaren, zodat ze hun podspecificaties correct kunnen definiëren.

Zie Pods toewijzen aan knooppunten voor meer informatie over het gebruik [van knooppunt selectors.][k8s-node-selector]

### <a name="node-affinity"></a>Knooppunt-affiniteit

Een knooppunt selector is een basisoplossing voor het toewijzen van pods aan een bepaald knooppunt. *Knooppunt-affiniteit* biedt meer flexibiliteit, zodat u kunt definiëren wat er gebeurt als de pod niet kan worden gematcht met een knooppunt. U kunt: 
* *Vereisen* dat Kubernetes Scheduler overeenkomt met een pod met een gelabelde host. Of
* *Geef* de voorkeur aan een overeenkomst, maar sta toe dat de pod op een andere host wordt gepland als er geen overeenkomst beschikbaar is.

In het volgende voorbeeld wordt de knooppunt-affiniteit op *requiredDuringSchedulingIgnoredDuringExecution .* Deze affiniteit vereist dat het Kubernetes-schema een knooppunt met een overeenkomend label gebruikt. Als er geen knooppunt beschikbaar is, moet de pod wachten tot de planning is voortgezet. Als u wilt toestaan dat de pod wordt gepland op een ander knooppunt, kunt u in plaats daarvan de waarde instellen op ***voorkeur** DuringSchedulingIgnoreDuringExecution*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

Het *gedeelte IgnoredDuringExecution* van de instelling geeft aan dat de pod niet uit het knooppunt mag worden onbeschikbaar maken als de knooppuntlabels worden gewijzigd. De Kubernetes-scheduler gebruikt alleen de bijgewerkte knooppuntlabels voor nieuwe pods die worden gepland, niet van pods die al zijn gepland op de knooppunten.

Zie Affiniteit en [anti-affiniteit voor meer informatie.][k8s-affinity]

### <a name="inter-pod-affinity-and-anti-affinity"></a>Affiniteit tussen pods en anti-affiniteit

Een laatste benadering voor de Kubernetes-scheduler voor het logisch isoleren van workloads is het gebruik van affiniteit tussen pods of anti-affiniteit. Deze instellingen definiëren dat pods niet *of* moeten *worden* gepland op een knooppunt met een bestaande overeenkomende pod. Standaard probeert de Kubernetes-scheduler meerdere pods in een replicaset op verschillende knooppunten te plannen. U kunt specifiekere regels voor dit gedrag definiëren.

U hebt bijvoorbeeld een webtoepassing die ook gebruikmaakt van een Azure Cache voor Redis. 
1. U gebruikt anti-affiniteitsregels voor pods om aan te vragen dat de Kubernetes-scheduler replica's distribueert over knooppunten. 
1. U gebruikt affiniteitsregels om ervoor te zorgen dat elk web-app-onderdeel is gepland op dezelfde host als een bijbehorende cache. 

De distributie van pods over knooppunten ziet eruit als in het volgende voorbeeld:

| **Knooppunt 1** | **Knooppunt 2** | **Knooppunt 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Affiniteit tussen pods en anti-affiniteit bieden een complexere implementatie dan knooppunt-selectors of knooppunt-affiniteit. Met de implementatie kunt u resources logisch isoleren en bepalen hoe Kubernetes pods op knooppunten inplant. 

Zie [Co-locate pods on the same node (Pods co-locaten op hetzelfde][k8s-pod-affinity]knooppunt) voor een volledig voorbeeld van deze webtoepassing met Azure Cache voor Redis voorbeeld.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is gericht op geavanceerde Kubernetes Scheduler-functies. Zie de volgende best practices voor meer informatie over clusterbewerkingen in AKS:

* [Multitenancy en clusterisolatie][aks-best-practices-scheduler]
* [Basisfuncties van Kubernetes-scheduler][aks-best-practices-scheduler]
* [Verificatie en autorisatie][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[taint-node-pool]: use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
