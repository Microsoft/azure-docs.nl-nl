---
title: Aanbevolen procedures voor clusterisolatie
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de aanbevolen procedures voor cluster operators voor isolatie in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cdeecabf569e3c6f9b280e6b0179e5378f5b1c95
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011367"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor cluster isolatie in azure Kubernetes service (AKS)

Wanneer u clusters beheert in azure Kubernetes service (AKS), moet u vaak teams en workloads isoleren. AKS biedt flexibiliteit bij het uitvoeren van meerdere Tenant clusters en het isoleren van bronnen. Om uw investering in Kubernetes te maximaliseren, moeten deze functies voor multitenancy en isolatie worden begrepen en geïmplementeerd.

Dit artikel Best practices is gericht op isolatie voor cluster operators. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Plan voor multi tenant clusters en schei ding van resources
> * Logische of fysieke isolatie gebruiken in uw AKS-clusters

## <a name="design-clusters-for-multi-tenancy"></a>Clusters ontwerpen voor multitenancy

Kubernetes biedt functies waarmee u teams en werk belastingen logisch kunt isoleren in hetzelfde cluster. Het doel is om het minste aantal bevoegdheden op te geven, die aangepast zijn aan de resources die nodig zijn voor elk team. Een [naam ruimte][k8s-namespaces] in Kubernetes maakt een logische isolatie grens. Aanvullende Kubernetes-functies en overwegingen voor isolatie en multitenancy bevatten de volgende gebieden:

* **Planning** omvat het gebruik van basis functies, zoals resource quota en pod-verstorings budgets. Zie [Best Practices for Basic scheduler-functies in AKS][aks-best-practices-scheduler]voor meer informatie over deze functies.
  * Meer geavanceerde functies van scheduler zijn onder andere taints en verdragen, knooppunt selecties en node-en pod-affiniteit of anti-affiniteit. Zie [Aanbevolen procedures voor geavanceerde functies van scheduler in AKS][aks-best-practices-advanced-scheduler]voor meer informatie over deze functies.
* **Netwerken** omvatten het gebruik van netwerk beleid om de stroom van verkeer in en uit te best uren.
* **Verificatie en autorisatie** omvatten de gebruiker van op rollen gebaseerd toegangs beheer (RBAC) en Azure Active Directory (AD) integratie, Pod-identiteiten en geheimen in azure Key Vault. Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS][aks-best-practices-identity]voor meer informatie over deze functies.
* **Containers** bevat de Azure Policy-invoeg toepassing voor AKS voor het afdwingen van pod-beveiliging, het gebruik van pod-beveiligings contexten en het scannen van installatie kopieën en de runtime voor beveiligings problemen. Hiervoor moet u ook app-beveiliging of Seccomp (beveiligd Computing) gebruiken om toegang tot de container te beperken tot het onderliggende knoop punt.

## <a name="logically-isolate-clusters"></a>Clusters logisch isoleren

**Richt lijnen voor best practices** : logische isolatie gebruiken om teams en projecten te scheiden. Probeer het aantal fysieke AKS-clusters dat u implementeert, te minimaliseren om teams of toepassingen te isoleren.

Met logische isolatie kan één AKS-cluster worden gebruikt voor meerdere werk belastingen, teams of omgevingen. Kubernetes- [naam ruimten][k8s-namespaces] vormen de logische isolatie grens voor werk belastingen en resources.

![Logische isolatie van een Kubernetes-cluster in AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logische schei ding van clusters biedt meestal een hogere pod-dichtheid dan fysiek geïsoleerde clusters. Er is minder dan de reken capaciteit die inactief is in het cluster. In combi natie met de Kubernetes van het cluster kunt u het aantal knoop punten omhoog of omlaag schalen om te voldoen aan de eisen. Met deze best practice methode voor automatisch schalen kunt u alleen het aantal knoop punten uitvoeren dat vereist is en de kosten minimaliseren.

Kubernetes-omgevingen, in AKS of elders, zijn niet volledig veilig voor gebruik van een vijandend multi tenant. In een omgeving met meerdere tenants werken meerdere tenants met een gemeen schappelijke, gedeelde infra structuur. Als alle tenants niet kunnen worden vertrouwd, moet u daarom extra plannen om te voor komen dat een Tenant die de beveiliging en service van een ander beïnvloedt. Aanvullende beveiligings functies, zoals *pod-beveiligings beleid* en meer nauw keuriger op rollen gebaseerd toegangs beheer (RBAC) voor knoop punten maken aanvallen moeilijker. Voor echte beveiliging bij het uitvoeren van vijandelijke multi tenant-workloads is een Hyper Visor echter het enige beveiligings niveau dat u moet vertrouwen. Het beveiligings domein voor Kubernetes wordt het hele cluster, niet een afzonderlijk knoop punt. Voor dit soort vijandelijke multi tenant-workloads moet u fysiek geïsoleerde clusters gebruiken.

## <a name="physically-isolate-clusters"></a>Clusters fysiek isoleren

**Richt lijnen voor best practices** : het gebruik van fysieke isolatie voor elke afzonderlijke team-of toepassings implementatie minimaliseren. Gebruik in plaats daarvan *logische* isolatie, zoals beschreven in de vorige sectie.

Een veelvoorkomende aanpak van cluster isolatie is het gebruik van fysiek gescheiden AKS-clusters. In dit isolatie model worden teams of workloads toegewezen aan hun eigen AKS-cluster. Deze benadering lijkt vaak op de eenvoudigste manier om workloads of teams te isoleren, maar voegt extra beheer en financiële overhead toe. U moet deze meerdere clusters nu onderhouden en afzonderlijk toegang geven en machtigingen toewijzen. U wordt ook gefactureerd voor alle afzonderlijke knoop punten.

![Fysieke isolatie van afzonderlijke Kubernetes-clusters in AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fysiek gescheiden clusters hebben doorgaans een lage pod-dichtheid. Wanneer elk team of werk belasting een eigen AKS-cluster heeft, is het cluster vaak overbelast met reken resources. Vaak wordt een klein aantal peulen op deze knoop punten gepland. Ongebruikte capaciteit op de knoop punten kan niet worden gebruikt voor toepassingen of services in ontwikkeling door andere teams. Deze overtollige resources dragen bij aan de extra kosten in fysiek gescheiden clusters.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is gericht op de isolatie van het cluster. Zie voor meer informatie over cluster bewerkingen in AKS de volgende aanbevolen procedures:

* [Functies van de Basic Kubernetes scheduler][aks-best-practices-scheduler]
* [Geavanceerde functies van Kubernetes scheduler][aks-best-practices-advanced-scheduler]
* [Verificatie en autorisatie][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
