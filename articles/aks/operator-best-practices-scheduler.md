---
title: 'Best practices voor Opera tors: Basic scheduler-functies in azure Kubernetes Services (AKS)'
description: Meer informatie over de aanbevolen procedures voor cluster operators voor het gebruik van elementaire scheduler-functies, zoals resource quota en pod-onderbrekingen budgetten in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 8c0f1d0cda61638abe03b92c627a5ea0455c31cb
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104895"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor Basic scheduler-functies in azure Kubernetes service (AKS)

Wanneer u clusters beheert in azure Kubernetes service (AKS), moet u vaak teams en workloads isoleren. Met de Kubernetes Scheduler kunt u de distributie van reken bronnen beheren of de impact van onderhouds gebeurtenissen beperken.

Dit artikel Best practices is gericht op de basis functies voor Kubernetes planning voor cluster operators. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Resource quota gebruiken om een vaste hoeveelheid resources te bieden aan teams of workloads
> * De impact van gepland onderhoud beperken met behulp van pod-verstorings budgetten
> * Controleren op ontbrekende pod-resource aanvragen en-limieten met het `kube-advisor` hulp programma

## <a name="enforce-resource-quotas"></a>Resource quota afdwingen

> **Richtlijnen voor best practices** 
> 
> Plan en pas resource quota toe op het niveau van de naam ruimte. Als er in het Peul geen resource aanvragen en-limieten zijn gedefinieerd, moet u de implementatie afwijzen. Bewaak het resource gebruik en pas de quota's zo nodig aan.

Resource aanvragen en-limieten worden geplaatst in de pod-specificatie. Limieten worden tijdens de implementatie door de Kubernetes-planner gebruikt om een beschikbaar knoop punt in het cluster te vinden. Limieten en aanvragen werken op het niveau van de afzonderlijke pod. Zie [pod resource-aanvragen en-limieten definiëren][resource-limits] voor meer informatie over het definiëren van deze waarden.

Als u een manier wilt bieden om resources te reserveren en te beperken in een ontwikkelings team of project, moet u *resource quota* gebruiken. Deze quota's worden gedefinieerd in een naam ruimte en kunnen worden gebruikt voor het instellen van quota's op basis van het volgende:

* **Reken bronnen**, zoals CPU en geheugen, of gpu's.
* **Opslag resources**, inclusief het totale aantal volumes of de hoeveelheid schijf ruimte voor een bepaalde opslag klasse.
* **Aantal objecten**, zoals het maximum aantal geheimen, services of taken, kunnen worden gemaakt.

De resources worden door Kubernetes niet overgevoerd. Zodra het totaal van de cumulatieve resource aanvraag de toegewezen quota heeft door gegeven, worden alle verdere implementaties mislukt.

Wanneer u resource quota definieert, moeten alle in de naam ruimte gemaakte peulen limieten of aanvragen in hun pod-specificaties opgeven. Als ze deze waarden niet opgeven, kunt u de implementatie afwijzen. In plaats daarvan kunt u [standaard aanvragen en limieten voor een naam ruimte configureren][configure-default-quotas].

In het volgende voor beeld YAML-manifest met de naam *dev-app-team quota. yaml* wordt een vaste limiet ingesteld voor een totaal van *10* cpu's, *20Gi* geheugen en *10* peul:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Dit resource quotum kan worden toegepast door de naam ruimte op te geven, zoals *dev-apps*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Werk samen met de ontwikkel aars en eigen aren van uw toepassingen om inzicht te krijgen in hun behoeften en pas de juiste resource quota toe.

Zie [resource quota in Kubernetes][k8s-resource-quotas]voor meer informatie over beschik bare Resource objecten, bereiken en prioriteiten.

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Plan voor Beschik baarheid met behulp van pod-onderbrekingen budgetten

> **Richtlijnen voor best practices** 
>
> Als u de beschik baarheid van toepassingen wilt hand haven, definieert u pod-verstorings budgets (PDBs) om ervoor te zorgen dat het cluster mini maal een minimum aantal beschikbaar is.

Er zijn twee storende gebeurtenissen die ertoe leiden dat peul wordt verwijderd:

### <a name="involuntary-disruptions"></a>Onvrijwillige onderbrekingen

*Onvrijwillige onderbrekingen* zijn gebeurtenissen die verder gaan dan de standaard controle van de cluster operator of de eigenaar van de toepassing. Behoort
* Hardwarestoring op de fysieke machine
* Kernel-paniek
* Een VM-knoop punt verwijderen

Invrijwillige onderbrekingen kunnen worden verholpen door:
* Meerdere replica's van uw peul gebruiken in een implementatie. 
* Het uitvoeren van meerdere knoop punten in het AKS-cluster. 

### <a name="voluntary-disruptions"></a>Vrijwillige onderbrekingen

*Vrijwillige onderbrekingen* zijn gebeurtenissen die worden aangevraagd door de cluster operator of de eigenaar van de toepassing. Behoort
* Cluster upgrades
* Implementatie sjabloon bijgewerkt
* Een pod per ongeluk verwijderen

Kubernetes biedt *pod-verstoringen* voor vrijwillige onderbrekingen, zodat u kunt plannen hoe implementaties of replica sets reageren wanneer een vrijwillige onderbrekings gebeurtenis optreedt. Met behulp van pod-onderbrekings budgetten kunnen cluster operators een mini maal beschikbaar of Maxi maal niet-beschikbaar resource aantal definiëren. 

Als u een cluster bijwerkt of een implementatie sjabloon bijwerkt, wordt de Kubernetes scheduler gepland op andere knoop punten, waarna er vrijwillige onderbrekings gebeurtenissen kunnen worden voortgezet. De scheduler wacht op het opnieuw opstarten van een knoop punt totdat het gedefinieerde aantal peulen is gepland op andere knoop punten in het cluster.

We bekijken een voor beeld van een replicaset met vijf peulen waarop NGINX wordt uitgevoerd. Het Peul in de replicaset wordt toegewezen aan het label `app: nginx-frontend` . Tijdens een vrijwillige onderbrekings gebeurtenis, zoals een cluster upgrade, wilt u er zeker van zijn dat er ten minste drie peulen worden uitgevoerd. Het volgende YAML-manifest voor een *PodDisruptionBudget* -object definieert deze vereisten:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
    matchLabels:
      app: nginx-frontend
```

U kunt ook een percentage definiëren, zoals *60%*, waarmee u automatisch de schaal van de replicaset kunt verhogen.

U kunt een maximum aantal niet-beschik bare instanties definiëren in een replicaset. Ook hier kan een percentage van het maximum aantal niet-beschik bare peulen worden gedefinieerd. In het volgende pod-manifest van de YAML-onderbreking wordt gedefinieerd dat er niet meer dan twee peulen in de replicaset beschikbaar zijn:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
    matchLabels:
      app: nginx-frontend
```

Zodra het Pod-verstorings budget is gedefinieerd, maakt u het in uw AKS-cluster, net als bij elk ander Kubernetes-object:

```console
kubectl apply -f nginx-pdb.yaml
```

Werk samen met de ontwikkel aars en eigen aren van uw toepassingen om inzicht te krijgen in hun behoeften en pas de toepasselijke budgetten voor pod-onderbrekingen toe.

Zie voor meer informatie over het gebruik van pod-verstoringen budgetten [een onderbrekings budget voor uw toepassing opgeven][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Regel matig controleren op cluster problemen met uitvoeren-Advisor

> **Richtlijnen voor best practices** 
>
> Voer regel matig de meest recente versie van het `kube-advisor` hulp programma voor open source uit om problemen in uw cluster op te sporen. Als u resource quota toepast op een bestaand AKS-cluster, voert u `kube-advisor` eerst uit om een Peul te vinden waarvoor geen resource-aanvragen en-limieten zijn gedefinieerd.

Het hulp programma [uitvoeren Advisor][kube-advisor] is een gekoppeld AKS open source-project dat een Kubernetes-cluster scant en gevonden problemen. `kube-advisor` komt van pas bij het identificeren van een Peul zonder resource aanvragen en-limieten.

Terwijl het `kube-advisor` hulp programma kan rapporteren over de resource aanvraag en limieten die ontbreken in PodSpecs voor Windows-en Linux-toepassingen, moet het hulp programma zelf worden gepland op een Linux-pod. Plan een pod om uit te voeren op een knooppunt groep met een specifiek besturings systeem met behulp van een [knooppunt kiezer][k8s-node-selector] in de configuratie van de pod.

Het bijhouden van de peulen zonder resource aanvragen en limieten in te stellen in een AKS-cluster dat meerdere ontwikkel teams en toepassingen host, kan lastig zijn. Als best practice, die regel matig worden uitgevoerd `kube-advisor` op uw AKS-clusters, met name als u geen resource quota aan naam ruimten toewijst.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is gericht op de basis functies van Kubernetes scheduler. Zie voor meer informatie over cluster bewerkingen in AKS de volgende aanbevolen procedures:

* [Multitenancy en clusterisolatie][aks-best-practices-cluster-isolation]
* [Geavanceerde functies van Kubernetes scheduler][aks-best-practices-advanced-scheduler]
* [Verificatie en autorisatie][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
