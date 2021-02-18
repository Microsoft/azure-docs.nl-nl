---
title: Overzicht van Kubernetes met Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: In dit artikel vindt u een overzicht van Kubernetes met Azure Arc.
keywords: Kubernetes, Arc, Azure, containers
ms.custom: references_regions
ms.openlocfilehash: 3d96c8c8764db89501da6fb9c498f0a3d20461af
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652527"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Wat is Kubernetes met Azure Arc?

Met Azure Arc enabled Kubernetes kunt u Kubernetes-clusters koppelen en configureren die zich binnen of buiten Azure bevinden. Wanneer u een Kubernetes-cluster verbindt met Azure Arc, geldt het volgende:
* Worden weer gegeven in de Azure Portal met een Azure Resource Manager-ID en een beheerde identiteit. 
* Aan standaard Azure-abonnementen zijn gekoppeld.
* In een resource groep worden geplaatst.
* Ontvang labels op dezelfde manier als andere Azure-bronnen. 

Voor het verbinden van een Kubernetes-cluster met Azure moet de clusterbeheerder agents implementeren. Deze agents:
* Voer in de `azure-arc` Kubernetes-naam ruimte uit als standaard Kubernetes-implementaties.
* Connectiviteit met Azure afhandelen.
* Verzamel Azure-Arc-logboeken en-metrische gegevens.
* Bekijk de configuratie aanvragen. 

Kubernetes met Azure Arc biedt ondersteuning voor industriestandaard SSL om gegevens te beveiligen tijdens de overdracht. Deze gegevens worden versleuteld en op rest opgeslagen in een Azure Cosmos DB-Data Base om de vertrouwelijkheid van gegevens te garanderen.
 
## <a name="supported-kubernetes-distributions"></a>Ondersteunde Kubernetes-distributies

Azure Arc enabled Kubernetes werkt met elk CNCF-cluster (native computing Foundation) van de Cloud, zoals:
* AKS-engine op Azure
* AKS-engine op Azure Stack hub
* GKE
* EKS
* VMware vSphere

De functies van Azure Arc enabled Kubernetes zijn getest door het Arc-team in de volgende distributies:
* RedHat OpenShift 4.3
* Rancher RKE 1.0.8
* Canonical Charmed Kubernetes 1.18
* AKS-engine
* AKS-engine op Azure Stack Hub
* AKS op Azure Stack HCI
* Cluster API Provider Azure

## <a name="supported-scenarios"></a>Ondersteunde scenario's 

Kubernetes met Azure Arc biedt ondersteuning voor de volgende scenario’s: 

* Verbinding maken met Kubernetes-clusters die buiten Azure worden uitgevoerd, voor inventariseren, groeperen en taggen.

* Implementeer toepassingen en pas configuratie toe met behulp van GitOps-configuratie beheer. 

* Uw clusters weer geven en bewaken met behulp van Azure Monitor voor containers. 

* Beleid Toep assen met behulp van Azure Policy voor Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Ondersteunde regio’s 

Kubernetes met Azure Arc wordt momenteel ondersteund in deze regio’s: 

* VS - oost 
* Europa -west

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met een cluster](./connect-cluster.md)
