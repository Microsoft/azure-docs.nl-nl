---
title: Limieten voor resources, Sku's, regio's
titleSuffix: Azure Kubernetes Service
description: Meer informatie over de standaard quota's, de beperkte VM-groottes voor knoop punten en de beschik baarheid van regio's van de Azure Kubernetes-service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 3e1e74834153584525d2093d2a1bb8ba8e991e5a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011461"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Quota’s, groottebeperkingen voor virtuele machines, en beschikbaarheid in regio’s in Azure Kubernetes Service (AKS)

Alle Azure-Services stellen standaard limieten en-quota voor bronnen en functies in, inclusief gebruiks beperkingen voor bepaalde virtuele machines van de VM.

In dit artikel worden de standaard resource limieten beschreven voor Azure Kubernetes service (AKS)-resources en de beschik baarheid van AKS in azure-regio's.

## <a name="service-quotas-and-limits"></a>Servicequota en -limieten

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Ingerichte infrastructuur

Alle andere beperkingen voor netwerk, berekening en opslag zijn van toepassing op de ingerichte infrastructuur. Zie [Azure-abonnement en service limieten](../azure-resource-manager/management/azure-subscription-service-limits.md)voor de relevante limieten.

> [!IMPORTANT]
> Wanneer u een AKS-cluster bijwerkt, worden er tijdelijk extra resources verbruikt. Deze resources omvatten beschik bare IP-adressen in een subnet van een virtueel netwerk of een vCPU-quotum van een virtuele machine. 
>
> Voor Windows Server-containers kunt u een upgrade bewerking uitvoeren om de nieuwste knooppunt updates toe te passen. Als u niet beschikt over de beschik bare IP-adres ruimte of het vCPU-quotum voor het afhandelen van deze tijdelijke bronnen, mislukt het upgrade proces van het cluster. Zie [een knooppunt groep bijwerken in AKS][nodepool-upgrade]voor meer informatie over het upgrade proces voor Windows Server-knoop punten.

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

U kunt bepaalde standaard limieten en-quota verhogen. Als uw resource een verhoging ondersteunt, vraagt u de verhoging via een [Azure-ondersteunings aanvraag][azure-support] (voor **type probleem**, selecteert u **quotum**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
