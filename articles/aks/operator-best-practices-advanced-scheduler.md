---
title: Aanbevolen procedures voor scheduler-functies
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de aanbevolen procedures voor cluster operators voor het gebruik van geavanceerde scheduler-functies, zoals taints en verdragen, knooppunt selecties en affiniteit, of inter-pod-affiniteit en anti-affiniteit in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: c0c1f587b4e52607e9466300f976a52874c9e5ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993700"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Best practices voor geavanceerde planningsfuncties in Azure Kubernetes Service (AKS)

Wanneer u clusters beheert in azure Kubernetes service (AKS), moet u vaak teams en workloads isoleren. De Kubernetes Scheduler biedt geavanceerde functies waarmee u kunt bepalen welke peulen op bepaalde knoop punten kunnen worden gepland of hoe multi-pod-toepassingen op de juiste manier kunnen worden gedistribueerd over het cluster. 

Dit artikel Best practices is gericht op geavanceerde Kubernetes plannings functies voor cluster operators. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Gebruik taints en verdragen om te beperken welke peulen kunnen worden gepland op knoop punten
> * Geef een voor keur aan van een van de knoop punten met behulp van knooppunt selecties of knooppunt affiniteit
> * Splitsen of groeperen van peulen met inter-pod-affiniteit of anti-affiniteit

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Geef toegewezen knoop punten op met behulp van taints en verdragen

**Best Practice-richt lijnen** : Beperk de toegang voor resource-intensieve toepassingen, zoals ingangs controllers, tot specifieke knoop punten. Houd knooppunt resources beschikbaar voor werk belastingen die ze nodig hebben, en sta geen planning van andere werk belastingen op de knoop punten toe.

Wanneer u uw AKS-cluster maakt, kunt u knoop punten met GPU-ondersteuning of een groot aantal krachtige Cpu's implementeren. Deze knoop punten worden vaak gebruikt voor werk belastingen voor grote gegevens verwerking, zoals machine learning (ML) of een kunst matige intelligentie (AI). Aangezien dit type hardware meestal een dure knooppunt resource is om te implementeren, beperkt u de werk belastingen die op deze knoop punten kunnen worden gepland. U kunt in plaats daarvan enkele knoop punten in het cluster reserveren om ingangs services uit te voeren en andere workloads te voor komen.

Deze ondersteuning voor verschillende knoop punten wordt geboden door gebruik te maken van meerdere knooppunt groepen. Een AKS-cluster biedt een of meer knooppunt groepen.

De Kubernetes scheduler kan taints en verdragen gebruiken om te beperken welke workloads op knoop punten kunnen worden uitgevoerd.

* Een **Taint** wordt toegepast op een knoop punt dat aangeeft dat alleen een specifiek peul kan worden gepland.
* Vervolgens wordt er een **verdragen** toegepast op een pod waarmee de Taint van een knoop punt kunnen worden *toegestaan* .

Wanneer u een pod implementeert in een AKS-cluster, plant Kubernetes alleen een Peul schema op knoop punten waar een rechts punt wordt afgestemd op de Taint. Stel dat u een knooppunt groep hebt in uw AKS-cluster voor knoop punten met GPU-ondersteuning. U definieert de naam, zoals *GPU*, vervolgens een waarde voor het plannen. Als u deze waarde instelt op geen *schema*, kan de Kubernetes scheduler geen peul plannen op het knoop punt als de pod niet de juiste verdragen definieert.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Als er een Taint wordt toegepast op knoop punten, definieert u een tolerantie in de pod-specificatie waarmee u de knoop punten kunt plannen. In het volgende voor beeld wordt de `sku: gpu` en `effect: NoSchedule` ingesteld dat de Taint die wordt toegepast op het knoop punt in de vorige stap, worden toegestaan:

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

Wanneer deze pod wordt geïmplementeerd, zoals het gebruik van `kubectl apply -f gpu-toleration.yaml` , kan Kubernetes de pod plannen op de knoop punten waarop de Taint is toegepast. Met deze logische isolatie kunt u de toegang tot resources in een cluster beheren.

Wanneer u taints toepast, moet u samen werken met de ontwikkel aars en eigen aren van uw toepassing, zodat ze de vereiste verdragen in hun implementaties kunnen definiëren.

Zie [meerdere knooppunt groepen maken en beheren voor een cluster in AKS][use-multiple-node-pools]voor meer informatie over het gebruik van meerdere knooppunt groepen in AKS.

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Gedrag van taints en verdragen in AKS

Wanneer u een groep van een knoop punt bijwerkt in AKS, volgen taints en toleranties een set patroon wanneer ze worden toegepast op nieuwe knoop punten:

- **Standaard clusters die gebruikmaken van schaal sets voor virtuele machines**
  - U kunt [een nodepool][taint-node-pool] van de AKS-API Taint, zodat er nieuwe knoop punten worden weer gegeven die API hebben opgegeven taints.
  - We gaan ervan uit dat u een cluster met twee knoop punten *Knooppunt1* en *Knooppunt2*. U werkt de knooppunt groep bij.
  - Er worden twee extra knoop punten gemaakt, *Knooppunt3* en *Knooppunt4*, en de taints worden respectievelijk door gegeven.
  - De oorspronkelijke *Knooppunt1* en *Knooppunt2* worden verwijderd.

- **Clusters zonder ondersteuning voor schaal sets voor virtuele machines**
  - We gaan ervan uit dat u een cluster met twee knoop punten hebt, *Knooppunt1* en *Knooppunt2*. Wanneer u een upgrade uitvoert, wordt er een extra knoop punt (*Knooppunt3*) gemaakt.
  - De taints van *Knooppunt1* worden toegepast op *Knooppunt3*, waarna *Knooppunt1* wordt verwijderd.
  - Er wordt een ander nieuw knoop punt gemaakt (met de naam *Knooppunt1*, omdat de vorige *Knooppunt1* is verwijderd) en de *Knooppunt2* taints worden toegepast op de nieuwe *Knooppunt1*. *Knooppunt2* wordt vervolgens verwijderd.
  - In essentie *Knooppunt1* wordt *Knooppunt3* en *Knooppunt2* wordt *Knooppunt1*.

Wanneer u een knooppunt groep in AKS schaalt, worden taints en verdragen niet door het ontwerp getransporteerd.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Pod plannen beheren met behulp van knooppunt selecties en affiniteit

**Richt lijnen voor best practices** : beheer de planning van de peuling op knoop punten met behulp van knooppunt selecties, knooppunt affiniteit of inter-pod-affiniteit. Met deze instellingen kan de Kubernetes-planner werk belastingen logisch isoleren, zoals op hardware in het knoop punt.

Taints en verdragen worden gebruikt voor het logisch isoleren van resources met een harde uitsnede-als de pod geen Taint van een knoop punt kan verdragen, maar niet is gepland op het knoop punt. Een alternatieve methode is het gebruik van knooppunt selecties. U labelt knoop punten, zoals het aanduiden van lokaal aangesloten SSD-opslag of een grote hoeveelheid geheugen en vervolgens definieert u in de pod-specificatie een knooppunt kiezer. Kubernetes plant vervolgens die peulingen op een overeenkomend knoop punt. In tegens telling tot de toleranties kan er geen overeenkomende knooppunt kiezer worden gepland op knoop punten met een label. Dit gedrag staat ongebruikte resources op de knoop punten toe om te verbruiken, maar heeft prioriteit voor het definiëren van de overeenkomende knooppunt kiezer.

Laten we eens kijken naar een voor beeld van knoop punten met een grote hoeveelheid geheugen. Deze knoop punten kunnen een voor keur geven aan de belangrijkste die een grote hoeveelheid geheugen aanvragen. Om ervoor te zorgen dat de resources niet inactief zijn, kunnen ze ook andere peulen uitvoeren.

```console
kubectl label node aks-nodepool1 hardware=highmem
```

Een pod-specificatie voegt vervolgens de `nodeSelector` eigenschap toe om een knooppunt kiezer te definiëren die overeenkomt met het label dat op een knoop punt is ingesteld:

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

Wanneer u deze scheduler-opties gebruikt, moet u samen werken met uw toepassings ontwikkelaars en-eigen aren zodat ze hun pod-specificaties correct kunnen definiëren.

Zie voor meer informatie over het gebruik van knooppunt selecties de optie [peulen aan knoop punten toewijzen][k8s-node-selector].

### <a name="node-affinity"></a>Knooppunt affiniteit

Een knooppunt selectie is een eenvoudige manier om een Peul toe te wijzen aan een bepaald knoop punt. Meer flexibiliteit is beschikbaar met behulp van de *node-affiniteit*. Met knooppunt affiniteit definieert u wat er gebeurt als de pod niet met een knoop punt kan worden gevonden. U kunt *vereisen* dat Kubernetes scheduler overeenkomt met een pod met een gelabelde host. U kunt ook de voor *keur* geven aan een overeenkomst, maar toestaan dat de pod op een andere host wordt gepland als er geen overeenkomst beschikbaar is.

In het volgende voor beeld wordt de affiniteit van het knoop punt ingesteld op *requiredDuringSchedulingIgnoredDuringExecution*. Voor deze affiniteit is het Kubernetes-schema vereist om een knoop punt met een overeenkomend label te gebruiken. Als er geen knoop punt beschikbaar is, moet de pod wachten tot de planning is voltooid. Als u wilt toestaan dat de pod op een ander knoop punt wordt gepland, kunt u in plaats daarvan de waarde instellen op *preferredDuringSchedulingIgnoreDuringExecution*:

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

Het *IgnoredDuringExecution* deel van de instelling geeft aan dat de pod niet uit het knoop punt mag worden verwijderd als de knooppunt labels worden gewijzigd. De Kubernetes scheduler maakt alleen gebruik van de bijgewerkte knooppunt labels voor nieuwe peulen die worden gepland, en niet van Peul al gepland op de knoop punten.

Zie [affiniteit en anti-affiniteit][k8s-affinity]voor meer informatie.

### <a name="inter-pod-affinity-and-anti-affinity"></a>Inter-pod-affiniteit en anti-affiniteit

Een laatste benadering voor de Kubernetes Planner om werk belastingen logisch te isoleren, is het gebruik van pod-affiniteit of anti-affiniteit. De instellingen bepalen *dat peul niet* moet worden gepland op een knoop punt dat een bestaande overeenkomende Pod heeft of dat ze *moeten* worden gepland. De Kubernetes scheduler probeert standaard meerdere peulen te plannen in een replicaset tussen knoop punten. U kunt meer specifieke regels rond dit gedrag definiëren.

Een goed voor beeld is een webtoepassing die ook een Azure-cache gebruikt voor redis. U kunt pod-regels voor anti-affiniteit gebruiken om aan te vragen dat de Kubernetes scheduler replica's van verschillende knoop punten distribueert. U kunt de affiniteits regels vervolgens gebruiken om ervoor te zorgen dat elk web-app-onderdeel is gepland op dezelfde host als een bijbehorende cache. De verdeling van de verschillende knoop punten ziet eruit als in het volgende voor beeld:

| **Knoop punt 1** | **Knoop punt 2** | **Knoop punt 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Dit voor beeld is een complexere implementatie dan het gebruik van knooppunt selecties of knooppunt affiniteit. De implementatie biedt u de controle over de manier waarop Kubernetes op knoop punten van peuling plant en kan bronnen logisch isoleren. Voor een volledig voor beeld van deze webtoepassing met Azure cache voor redis raadpleegt u [samen op hetzelfde knoop punt][k8s-pod-affinity].

## <a name="next-steps"></a>Volgende stappen

Dit artikel is gericht op geavanceerde functies van Kubernetes scheduler. Zie voor meer informatie over cluster bewerkingen in AKS de volgende aanbevolen procedures:

* [Multitenancy en clusterisolatie][aks-best-practices-scheduler]
* [Functies van de Basic Kubernetes scheduler][aks-best-practices-scheduler]
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
