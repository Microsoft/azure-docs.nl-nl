---
title: Veelgestelde vragen over Azure Arc enabled Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dit artikel bevat een lijst met veelgestelde vragen met betrekking tot Azure Arc enabled Kubernetes
keywords: Kubernetes, Arc, azure, containers, configuratie, GitOps, veelgestelde vragen
ms.openlocfilehash: 237b2629b833a63552b172636f46a1ac92e321c0
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561495"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Veelgestelde vragen: Azure Arc enabled Kubernetes

Dit artikel heeft betrekking op veelgestelde vragen over Azure Arc enabled Kubernetes.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Wat is het verschil tussen Kubernetes met Azure Arc en Azure Kubernetes Service (AKS)?

AKS is de beheerde Kubernetes die door Azure wordt aangeboden. AKS vereenvoudigt de implementatie van een beheerd Kubernetes-cluster in azure door een groot deel van de complexiteit en operationele overhead naar Azure te offloaden. Omdat de Kubernetes-Masters door Azure worden beheerd, beheert u alleen de agent knooppunten en houdt u deze bij.

Met Azure Arc enabled Kubernetes kunt u de beheer mogelijkheden van Azure (zoals Azure Monitor en Azure Policy) uitbreiden door Kubernetes-clusters te verbinden met Azure. U onderhoudt het onderliggende Kubernetes-cluster zelf.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Moet ik mijn AKS-clusters die worden uitgevoerd op Azure verbinden met Azure Arc?

Nee. Alle Azure Arc ingeschakelde Kubernetes-functies, waaronder Azure Monitor en Azure Policy (gate keeper), zijn beschikbaar op AKS (een systeem eigen bron in Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Moet ik mijn AKS-HCI-cluster en Kubernetes-clusters op Azure Stack hub en Azure Stack Edge verbinden met Azure Arc?

Ja, u kunt uw AKS-HCI-cluster of Kubernetes-clusters op Azure Stack Edge-of Azure Stack-hub met Azure Arc verbinden met clusters met resource weergave in Azure Resource Manager. Deze resource weergave breidt mogelijkheden als cluster configuratie, Azure Monitor en Azure Policy (gate keeper) uit naar de Kubernetes-clusters waarmee u verbinding maakt.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Hoe kan ik de verlopen Azure Arc-Kubernetes-resources adresseren?

Het MSI-certificaat (Managed Service Identity) dat is gekoppeld aan uw Azure Arc enabled-Kuberenetes heeft een verloop venster van 90 dagen. Zodra dit certificaat is verlopen, wordt de resource beschouwd als `Expired` alle functies, zoals configuratie, bewaking en beleid, werken niet meer op dit cluster. Voer de volgende stappen uit om uw Kubernetes-cluster opnieuw te laten werken met Azure Arc:

1. Kubernetes-resource en agents van Azure-Arc op het cluster verwijderen 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Maak de Azure Arc-Kubernetes-resource opnieuw door agents in het cluster opnieuw te implementeren.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` worden ook configuraties boven op het cluster verwijderd. Nadat u hebt uitgevoerd `az connectedk8s connect` , maakt u de configuraties op het cluster opnieuw, hetzij hand matig, hetzij met behulp van Azure Policy.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Als ik al CI/CD-pijp lijnen gebruik, kan ik dan nog steeds Azure Arc enabled Kubernetes en-configuraties gebruiken?

Ja, u kunt nog steeds configuraties gebruiken in een cluster dat implementaties ontvangt via een CI/CD-pijp lijn. Vergeleken met traditionele CI/CD-pijp lijnen, hebben configuraties functie twee extra voor delen:

**Drift-afstemming**

De CI/CD-pijp lijn past slechts eenmaal wijzigingen toe tijdens de uitvoering van de pijp lijn. De GitOps-operator in het cluster doorlopen echter voortdurend de Git-opslag plaats om de gewenste status van Kubernetes-resources op het cluster op te halen. Als de operator GitOps de gewenste status van resources detecteert die afwijkt van de werkelijke status van resources op het cluster, wordt deze drift afgestemd.

**GitOps op schaal Toep assen**

CI/CD-pijp lijnen zijn geschikt voor op gebeurtenissen gerichte implementaties van uw Kubernetes-cluster, waarbij de gebeurtenis kan een push-naar-een Git-opslag plaats zijn. Voor de implementatie van dezelfde configuratie voor al uw Kubernetes-clusters moet de CI/CD-pijp lijn echter hand matig worden geconfigureerd met referenties van elk van deze Kubernetes-clusters. In het geval van Azure Arc enabled Kubernetes, omdat Azure Resource Manager uw configuraties beheert, kunt u Azure Policy gebruiken om de toepassing van de gewenste configuratie op alle Kubernetes-clusters in een abonnement of resource groeps bereik te automatiseren. Deze mogelijkheid is zelfs van toepassing op Kubernetes-resources van Azure Arc die zijn gemaakt na de beleids toewijzing.

De configuratie functie wordt gebruikt voor het Toep assen van basislijn configuraties zoals netwerk beleid, functie bindingen en pod beveiligings beleid voor de gehele inventaris van Kubernetes-clusters voor nalevings-en governance vereisten.

## <a name="next-steps"></a>Volgende stappen

* [Een cluster verbinden met Azure Arc](./connect-cluster.md)
* [Configuraties maken op uw Kubernetes-cluster met Arc-functionaliteit](./use-gitops-connected-cluster.md)
* [Azure Policy gebruiken om configuraties op schaal toe te passen](./use-azure-policy.md)
