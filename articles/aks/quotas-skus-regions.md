---
title: Limieten voor resources, Sku's, regio's
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de standaard quota's, de beperkte VM-groottes voor knoop punten en de beschik baarheid van regio's van de Azure Kubernetes-service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 9592b03f5bd9e9436db5665e040b4afd3b68b764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87562732"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Quota’s, groottebeperkingen voor virtuele machines, en beschikbaarheid in regio’s in Azure Kubernetes Service (AKS)

Alle Azure-Services hebben standaard limieten en-quota ingesteld voor resources en functies. Bepaalde VM-Sku's (virtuele machine) zijn ook beperkt voor gebruik.

In dit artikel worden de standaard resource limieten beschreven voor Azure Kubernetes service (AKS)-resources en de beschik baarheid van AKS in azure-regio's.

## <a name="service-quotas-and-limits"></a>Servicequota en -limieten

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Ingerichte infrastructuur

Alle andere beperkingen voor netwerk, berekening en opslag zijn van toepassing op de ingerichte infrastructuur. Zie [Azure-abonnement en service limieten](../azure-resource-manager/management/azure-subscription-service-limits.md)voor de relevante limieten.

> [!IMPORTANT]
> Wanneer u een AKS-cluster bijwerkt, worden er tijdelijk extra resources verbruikt. Deze resources omvatten beschik bare IP-adressen in een subnet van een virtueel netwerk of een vCPU-quotum van een virtuele machine. Als u Windows Server-containers gebruikt, is de enige geviseerde aanpak voor het Toep assen van de meest recente updates op de knoop punten het uitvoeren van een upgrade bewerking. Een mislukte upgrade van een cluster kan erop wijzen dat u niet over de beschik bare IP-adres ruimte of het vCPU quotum beschikt om deze tijdelijke bronnen te verwerken. Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor meer informatie over het upgrade proces voor Windows Server-knoop punten.

## <a name="restricted-vm-sizes"></a>Beperkte VM-grootten

Elk knoop punt in een AKS-cluster bevat een vaste hoeveelheid reken resources, zoals vCPU en geheugen. Als een AKS-knoop punt onvoldoende Compute-bronnen bevat, kan het samen werken mogelijk niet op de juiste wijze worden uitgevoerd. **Gebruik niet de volgende VM-sku's in AKS** om ervoor te zorgen dat het vereiste *uitvoeren-systeem* en uw toepassingen betrouwbaar kunnen worden gepland:

- Standard_A0
- Standard_A1
- Standard_A1_v2
- Standard_B1s
- Standard_B1ms
- Standard_F1
- Standard_F1s

Zie [grootten voor virtuele machines in azure][vm-skus]voor meer informatie over VM-typen en hun reken resources.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Zie [AKS Gewest Availability][region-availability](Engelstalig) voor de meest recente lijst van waar u clusters kunt implementeren en uitvoeren.

## <a name="next-steps"></a>Volgende stappen

Bepaalde standaardlimieten en -quota kunnen worden verhoogd. Als uw resource een verhoging ondersteunt, vraagt u de verhoging via een [Azure-ondersteunings aanvraag][azure-support] (voor **type probleem**, selecteert u **quotum**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
