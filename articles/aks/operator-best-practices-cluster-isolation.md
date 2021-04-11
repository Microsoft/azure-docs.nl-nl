---
title: Aanbevolen procedures voor clusterisolatie
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de aanbevolen procedures voor cluster operators voor isolatie in azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: e51689d33711f127f775c63c9d7fc8ad4c901604
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105167"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Aanbevolen procedures voor cluster isolatie in azure Kubernetes service (AKS)

Wanneer u clusters beheert in azure Kubernetes service (AKS), moet u vaak teams en workloads isoleren. AKS biedt flexibiliteit bij het uitvoeren van meerdere Tenant clusters en het isoleren van bronnen. Om uw investering in Kubernetes te maximaliseren, moet u eerst de AKS-functies voor multitenancy en-isolatie begrijpen en implementeren.

Dit artikel Best practices is gericht op isolatie voor cluster operators. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Plan voor multi tenant clusters en schei ding van resources
> * Logische of fysieke isolatie gebruiken in uw AKS-clusters

## <a name="design-clusters-for-multi-tenancy"></a>Clusters ontwerpen voor multitenancy

Met Kubernetes kunt u teams en werk belastingen logisch isoleren in hetzelfde cluster. Het doel is om het minste aantal bevoegdheden op te geven, binnen het bereik van de resources die nodig zijn voor elk team. Met een Kubernetes- [naam ruimte][k8s-namespaces] wordt een logische isolatie grens gemaakt. Aanvullende Kubernetes-functies en overwegingen voor isolatie en multitenancy bevatten de volgende gebieden:

### <a name="scheduling"></a>Planning

Bij het *plannen* worden basis functies, zoals resource quota en pod-verstorings budgetten, gebruikt. Zie [Best Practices for Basic scheduler-functies in AKS][aks-best-practices-scheduler]voor meer informatie over deze functies.

Meer geavanceerde functies van scheduler zijn onder andere:
* Taints en verdragen
* Knooppunt selecties
* Node-en pod-affiniteit of anti-affiniteit. 

Zie [Aanbevolen procedures voor geavanceerde functies van scheduler in AKS][aks-best-practices-advanced-scheduler]voor meer informatie over deze functies.

### <a name="networking"></a>Netwerken

*Netwerken* gebruiken netwerk beleid om de stroom van verkeer in en uit te best uren.

### <a name="authentication-and-authorization"></a>Verificatie en autorisatie

*Verificatie en autorisatie* maakt gebruik van:
* Toegangsbeheer op basis van rollen (RBAC)
* Integratie van Azure Active Directory (AD)
* Pod-identiteiten
* Geheimen in Azure Key Vault 

Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS][aks-best-practices-identity]voor meer informatie over deze functies.

### <a name="containers"></a>Containers
*Containers* zijn onder andere:
* De Azure Policy-invoeg toepassing voor AKS om pod-beveiliging af te dwingen.
* Het gebruik van pod-beveiligings contexten.
* Zowel installatie kopieën als runtime scannen op beveiligings problemen. 
* App-beveiliging of Seccomp (beveiligd Computing) gebruiken om toegang tot de container te beperken tot het onderliggende knoop punt.

## <a name="logically-isolate-clusters"></a>Clusters logisch isoleren

> **Richtlijnen voor best practices**
>
> Afzonderlijke teams en projecten met *logische isolatie*. Minimaliseer het aantal fysieke AKS-clusters dat u implementeert om teams of toepassingen te isoleren.

Met logische isolatie kan één AKS-cluster worden gebruikt voor meerdere werk belastingen, teams of omgevingen. Kubernetes- [naam ruimten][k8s-namespaces] vormen de logische isolatie grens voor werk belastingen en resources.

![Logische isolatie van een Kubernetes-cluster in AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logische schei ding van clusters biedt meestal een hogere pod-dichtheid dan fysiek geïsoleerde clusters, waarbij minder overmatige reken capaciteit in het cluster wordt gebruikt. In combi natie met de Kubernetes van het cluster kunt u het aantal knoop punten omhoog of omlaag schalen om te voldoen aan de eisen. Met deze best practice methode voor automatisch schalen kunt u de kosten minimaliseren door alleen het vereiste aantal knoop punten uit te voeren.

Op dit moment zijn Kubernetes omgevingen niet volledig veilig voor gebruik met meerdere tenants. In een omgeving met meerdere tenants werken meerdere tenants aan een gemeen schappelijke, gedeelde infra structuur. Als alle tenants niet kunnen worden vertrouwd, hebt u extra planning nodig om te voor komen dat tenants invloed hebben op de beveiliging en service van anderen.

Aanvullende beveiligings functies, zoals *pod-beveiligings beleid* of Kubernetes RBAC voor knoop punten, blok keren aanvallen op efficiënte wijze. Voor echte beveiliging bij het uitvoeren van vijandelijke multi tenant-workloads moet u alleen een Hyper Visor vertrouwen. Het beveiligings domein voor Kubernetes wordt het hele cluster, niet een afzonderlijk knoop punt. 

Voor dit soort vijandelijke multi tenant-workloads moet u fysiek geïsoleerde clusters gebruiken.

## <a name="physically-isolate-clusters"></a>Clusters fysiek isoleren

> **Richtlijnen voor best practices** 
>
> Minimaliseer het gebruik van fysieke isolatie voor elke afzonderlijke team-of toepassings implementatie. Gebruik in plaats daarvan *logische* isolatie, zoals beschreven in de vorige sectie.

Het fysiek scheiden van AKS-clusters is een gemeen schappelijke benadering van cluster isolatie. In dit isolatie model worden teams of workloads toegewezen aan hun eigen AKS-cluster. Fysieke isolatie kan eruit zien als de eenvoudigste manier om workloads of teams te isoleren, het beheer en de financiële overhead worden toegevoegd. Nu moet u deze meerdere clusters onderhouden en individueel toegang geven en machtigingen toewijzen. U wordt ook gefactureerd voor elk afzonderlijk knoop punt.

![Fysieke isolatie van afzonderlijke Kubernetes-clusters in AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fysiek gescheiden clusters hebben doorgaans een lage pod-dichtheid. Omdat elk team of werk belasting een eigen AKS-cluster heeft, is het cluster vaak groter dan ingericht met reken bronnen. Vaak wordt een klein aantal peulen op deze knoop punten gepland. Niet-claim capaciteit van knoop punten kan niet worden gebruikt voor toepassingen of services in ontwikkeling door andere teams. Deze overtollige resources dragen bij aan de extra kosten in fysiek gescheiden clusters.

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
