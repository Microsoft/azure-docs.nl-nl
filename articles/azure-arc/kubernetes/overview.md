---
title: Overzicht van Kubernetes met Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: In dit artikel vindt u een overzicht van Kubernetes met Azure Arc.
keywords: Kubernetes, Arc, Azure, containers
ms.custom: references_regions
ms.openlocfilehash: b3296aa1c1d5d463cc7f4cb932b44ad76632e19e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121589"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Wat is Kubernetes met Azure Arc?

Met Azure Arc enabled Kubernetes kunt u Kubernetes-clusters koppelen en configureren die zich binnen of buiten Azure bevinden. Wanneer u een Kubernetes-cluster verbindt met Azure Arc, geldt het volgende:
* Worden weer gegeven in de Azure Portal met een Azure Resource Manager-ID en een beheerde identiteit. 
* Worden in een Azure-abonnement en resource groep geplaatst.
* Ontvang labels op dezelfde manier als andere Azure-bronnen. 

Voor het verbinden van een Kubernetes-cluster met Azure moet de clusterbeheerder agents implementeren. Deze agents:
* Voer in de `azure-arc` Kubernetes-naam ruimte uit als standaard Kubernetes-implementaties.
* Connectiviteit met Azure afhandelen.
* Verzamel Azure-Arc-logboeken en-metrische gegevens.
* Bekijk de configuratie aanvragen. 

Kubernetes met Azure Arc biedt ondersteuning voor industriestandaard SSL om gegevens te beveiligen tijdens de overdracht. Ook worden inactieve gegevens versleuteld opgeslagen in een Azure Cosmos DB-database om de vertrouwelijkheid van gegevens te garanderen.

## <a name="supported-kubernetes-distributions"></a>Ondersteunde Kubernetes-distributies

Azure Arc enabled Kubernetes werkt met alle CNCF-gecertificeerde Kubernetes-clusters (native computing Foundation) van de Cloud. Het Azure-Arc-team heeft met belang rijke partners van de branche gewerkt de [conformiteit](./validation-program.md) van hun Kubernetes-distributies te valideren met Kubernetes van Azure Arc ingeschakeld.

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
* VS - west-centraal
* VS - zuid-centraal
* Azië - zuidoost
* Verenigd Koninkrijk Zuid
* VS - west 2
* Australië - oost
* VS - oost 2
* Europa - noord

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het verbinden van een cluster met Azure Arc.
> [!div class="nextstepaction"]
> [Een cluster verbinden met Azure Arc](./quickstart-connect-cluster.md)
