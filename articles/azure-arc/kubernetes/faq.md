---
title: Veelgestelde vragen over Azure Arc enabled Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dit artikel bevat een lijst met veelgestelde vragen met betrekking tot Azure Arc enabled Kubernetes
keywords: Kubernetes, Arc, azure, containers, configuratie, GitOps, veelgestelde vragen
ms.openlocfilehash: dc12294b5d53372be5f2e1dd71436973fefbb194
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647860"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Veelgestelde vragen: Azure Arc enabled Kubernetes

Dit artikel heeft betrekking op veelgestelde vragen over Azure Arc enabled Kubernetes.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Wat is het verschil tussen Kubernetes met Azure Arc en Azure Kubernetes Service (AKS)?

AKS is de beheerde Kubernetes die door Azure wordt aangeboden. AKS vereenvoudigt de implementatie van een beheerd Kubernetes-cluster in azure door een groot deel van de complexiteit en operationele overhead naar Azure te offloaden. Omdat de Kubernetes-Masters door Azure worden beheerd, beheert u alleen de agent knooppunten en houdt u deze bij.

Met Azure Arc enabled Kubernetes kunt u de beheer mogelijkheden van Azure (zoals Azure Monitor en Azure Policy) uitbreiden door Kubernetes-clusters te verbinden met Azure. U onderhoudt het onderliggende Kubernetes-cluster zelf.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Moet ik mijn AKS-clusters die worden uitgevoerd op Azure verbinden met Azure Arc?

Nee. Alle Azure Arc ingeschakelde Kubernetes-functies, waaronder Azure Monitor en Azure Policy (gate keeper), zijn beschikbaar op AKS (een systeem eigen bron in Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Moet ik mijn AKS-HCI-cluster en Kubernetes-clusters op Azure Stack hub en Azure Stack Edge verbinden met Azure Arc?

Ja, u kunt uw AKS-HCI-cluster of Kubernetes-clusters op Azure Stack Edge-of Azure Stack-hub met Azure Arc verbinden met clusters met resource weergave in Azure Resource Manager. Deze resource weergave breidt mogelijkheden, zoals cluster configuratie, Azure Monitor en Azure Policy (gate keeper), uit voor verbonden Kubernetes-clusters.

Als het Kubernetes-cluster van Azure Arc zich op Azure Stack Edge bevindt, AKS op Azure Stack HCI (>= april 2021 update) of AKS op Windows Server 2019 Data Center (>= update van april 2021), is de Kubernetes-configuratie kosteloos opgenomen.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Hoe kan ik de verlopen Azure Arc-Kubernetes-resources adresseren?

Het Managed Service Identity (MSI)-certificaat dat is gekoppeld aan uw Azure Arc enabled-Kubernetes heeft een verloop venster van 90 dagen. Zodra dit certificaat is verlopen, wordt de resource beschouwd als `Expired` alle functies (zoals configuratie, bewaking en beleid) die niet meer werken op dit cluster. Uw Kubernetes-cluster opnieuw samen werken met Azure Arc:

1. Verwijder de Kubernetes-resource en-agents van Azure-arctangens op het cluster. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Maak de Azure Arc-Kubernetes-resource opnieuw door agents in het cluster te implementeren.
    
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

CI/CD-pijp lijnen zijn handig voor op gebeurtenissen gebaseerde implementaties in uw Kubernetes-cluster (bijvoorbeeld een push naar een Git-opslag plaats). Als u echter dezelfde configuratie wilt implementeren voor al uw Kubernetes-clusters, moet u de referenties van elke Kubernetes-cluster hand matig configureren voor de CI/CD-pijp lijn. 

Voor Azure Arc enabled Kubernetes, omdat Azure Resource Manager uw configuraties beheert, kunt u het maken van dezelfde configuratie automatiseren voor alle Azure-Arc-Kubernetes-resources met behulp van Azure Policy binnen het bereik van een abonnement of een resource groep. Deze mogelijkheid is zelfs van toepassing op Kubernetes-resources van Azure Arc die zijn gemaakt na de beleids toewijzing.

Deze functie past basislijn configuraties (zoals netwerk beleid, Pod en beveiligings beleid) toe in de gehele Kubernetes-cluster inventaris om te voldoen aan de vereisten voor naleving en governance.

## <a name="next-steps"></a>Volgende stappen

* [Een cluster verbinden met Azure Arc](./quickstart-connect-cluster.md)
* [Configuraties maken op uw Kubernetes-cluster met Arc-functionaliteit](./use-gitops-connected-cluster.md)
* [Azure Policy gebruiken om configuraties op schaal toe te passen](./use-azure-policy.md)
