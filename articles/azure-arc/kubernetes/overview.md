---
title: Overzicht van Kubernetes met Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: In dit artikel vindt u een overzicht van Kubernetes met Azure Arc.
keywords: Kubernetes, Arc, Azure, containers
ms.custom: references_regions
ms.openlocfilehash: 54b439d73ed5dfb0709d33dea1f588b8bdf4489f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560272"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Wat is Kubernetes met Azure Arc Preview?

U kunt Kubernetes-clusters binnen of buiten Azure koppelen en configureren met behulp van Kubernetes met Azure Arc Preview. Wanneer een Kubernetes-cluster is gekoppeld aan Azure Arc, wordt het weergegeven in de Azure-portal. Het heeft dan een Azure Resource Manager-id en een beheerde identiteit. Clusters zijn gekoppeld aan standaardabonnementen in Azure, bevinden zich in een resourcegroep, en kunnen labels ontvangen zoals elke andere Azure-resource. 

Voor het verbinden van een Kubernetes-cluster met Azure moet de clusterbeheerder agents implementeren. Deze agents worden uitgevoerd in een Kubernetes-naamruimte met de naam `azure-arc`, en zijn standaardimplementaties van Kubernetes. De agents zijn verantwoordelijk voor de connectiviteit met Azure, het verzamelen van logboeken en metrische gegevens van Azure Arc, en het detecteren van configuratieaanvragen. 

Kubernetes met Azure Arc biedt ondersteuning voor industriestandaard SSL om gegevens te beveiligen tijdens de overdracht. Ook worden inactieve gegevens versleuteld opgeslagen in een Azure Cosmos DB-database om de vertrouwelijkheid van gegevens te garanderen.
 
> [!NOTE]
> Kubernetes met Azure Arc is beschikbaar als preview-versie. Dit wordt niet aanbevolen voor productieworkloads.

## <a name="supported-kubernetes-distributions"></a>Ondersteunde Kubernetes-distributies

Kubernetes met Azure Arc werkt met elk CNCF-gecertificeerd (Cloud Native Computing Foundation) Kubernetes-cluster zoals AKS-engine op Azure, AKS-engine op Azure Stack Hub, GKE, EKS en VMware vSphere.

De functies van Kubernetes met Azure Arc zijn door het Arc-team getest op de volgende distributies:
* RedHat OpenShift 4.3
* Rancher RKE 1.0.8
* Canonical Charmed Kubernetes 1.18
* AKS-engine
* AKS-engine op Azure Stack Hub
* AKS op Azure Stack HCI
* Cluster API Provider Azure

## <a name="supported-scenarios"></a>Ondersteunde scenario's 

Kubernetes met Azure Arc biedt ondersteuning voor deze scenario’s: 

* Verbinding maken met Kubernetes-clusters die buiten Azure worden uitgevoerd, voor inventariseren, groeperen en taggen.

* Toepassingen implementeren en configuratie toepassen met behulp van GitOps-configuratiebeheer. 

* Azure Monitor gebruiken voor containers om uw clusters te bekijken en te bewaken. 

* Beleid toepassen met behulp van Azure Policy voor Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Ondersteunde regio’s 

Kubernetes met Azure Arc wordt momenteel ondersteund in deze regio’s: 

* VS - oost 
* Europa -west

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met een cluster](./connect-cluster.md)
