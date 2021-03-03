---
title: Kubernetes-validatie programma voor Azure Arc enabled
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Beschrijft het Arc-validatie programma voor Kubernetes-distributies
keywords: Kubernetes, Arc, azure, K8s, validatie
ms.openlocfilehash: ad87fe3f11c97524595a517b17bb52fa1b771c9d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662271"
---
# <a name="azure-arc-validation-program"></a>Azure Arc-validatie programma

Azure Arc enabled Kubernetes werkt met alle CNCF-gecertificeerde Kubernetes-clusters (native computing Foundation) van de Cloud. Het Azure Arc-team heeft ook gewerkt met belang rijke Kubernetes-aanbieders voor het valideren van Kubernetes met hun Kubernetes-distributies. Toekomstige primaire en secundaire versies van Kubernetes-distributies die door deze providers worden uitgebracht, worden gevalideerd voor compatibiliteit met de Kubernetes van Azure Arc ingeschakeld.

## <a name="validated-distributions"></a>Gevalideerde distributies

De volgende micro soft heeft Kubernetes-distributies en infrastructuur providers door gegeven aan de conformiteit tests voor Azure Arc enabled Kubernetes:

| Distributie-en infrastructuur provider | Versie |
| ---------------------------------------- | ------- |
| Cluster-API-provider op Azure            | Release versie: [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12); Kubernetes-versie: [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| AKS op Azure Stack HCI                   | Release versie: [December 2020 update](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012); Kubernetes-versie: [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

De volgende providers en hun bijbehorende Kubernetes-distributies hebben geslaagd de conformatie tests voor Azure Arc enabled Kubernetes:

| Provider naam | Distributie naam | Versie |
| ------------ | ----------------- | ------- |
| RedHat       | [OpenShift Container Platform](https://www.openshift.com/products/container-platform) | [4,6](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html) |
| VMware       | [Tanzu Kubernetes-raster](https://tanzu.vmware.com/kubernetes-grid) | Kubernetes-versie: v 1.17.5 |
| Canonical    | [Charm Kubernetes](https://ubuntu.com/kubernetes) | [1,19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| Rancher      | [Rancher Kubernetes-engine](https://rancher.com/products/rke/) | RKE CLI-versie: [v 1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4); Kubernetes-versies: [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6)), [1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14)), [1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))  |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | Release versie: [2,0](https://www.nutanix.com/blog/introducing-nutanix-karbon-2-kubernetes-simplicity-upgraded); Kubernetes-versie: [1.17.0](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.0) |

Het Azure Arc-team heeft ook de conforme-tests en gevalideerde Kubernetes-scenario's voor Azure Arc ingeschakeld op de volgende open bare cloud providers:

| Naam van de open bare Cloud provider | Distributie naam | Versie |
| -------------------------- | ----------------- | ------- |
| Amazon Web Services        | Elastische Kubernetes-service (EKS) | v 1.18.9  |
| Google Cloud Platform      | Google Kubernetes Engine (GKE) | v 1.17.15 |

## <a name="scenarios-validated"></a>Gevalideerde scenario's

De conforme tests worden uitgevoerd als onderdeel van de Azure Arc enabled Kubernetes-validatie bedekken de volgende scenario's:

1. Kubernetes-clusters verbinden met Azure Arc: 
    * Implementeer een Kubernetes-helm-grafiek voor Azure Arc enabled in het cluster.
    * Stel het certificaat van de beheerde systeem identiteit (MSI) in op het cluster.
    * Agents verzenden meta gegevens van het cluster naar Azure.

2. Configuratie: 
    * Maak een configuratie boven op de Kubernetes-resource van Azure Arc enabled.
    * [Stroom](https://docs.fluxcd.io/)die nodig is voor het instellen van de GitOps-werk stroom, wordt geïmplementeerd op het cluster.
    * Met stroom worden manifesten en helm-grafieken opgehaald uit de demo Git opslag plaats en geïmplementeerd in cluster.

## <a name="next-steps"></a>Volgende stappen

* [Een cluster verbinden met Azure Arc](./connect-cluster.md)
* [Configuraties maken op uw Kubernetes-cluster met Arc-functionaliteit](./use-gitops-connected-cluster.md)
* [Azure Policy gebruiken om configuraties op schaal toe te passen](./use-azure-policy.md)
