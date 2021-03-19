---
title: Kies een hulpprogramma voor het verplaatsen van Azure-resources tussen verschillende regio's
description: Opties en hulpprogramma's voor het verplaatsen van Azure-resources tussen regio's bekijken
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 1b233028b52175842c73660ff116ac592d8296e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90603353"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Kies een hulpprogramma om Azure-resources te verplaatsen

U kunt als volgt resources in Azure verplaatsen:

- **Azure-resources verplaatsen tussen regio's**: Verplaats de resource vanuit de Resource Mover-hub of binnen een resourcegroep. 
- **Resources verplaatsen tussen resourcegroepen of abonnementen**: Verplaats vanuit een resourcegroep. 
- **Resources verplaatsen tussen Azure Clouds**: Gebruik de Azure Site Recovery-service om resources te verplaatsen tussen openbare clouds en clouds van de overheid.
- **Resources verplaatsen tussen beschikbaarheidszones in dezelfde regio**: Gebruik de Azure Site Recovery-service om resources te verplaatsen tussen beschikbaarheidszones in dezelfde Azure-regio.


## <a name="compare-move-tools"></a>Hulpprogramma's voor verplaatsen vergelijken

**Hulpprogramma** | **Wanneer gebruiken** | **Meer informatie**
--- | --- | ---
**Verplaatsen binnen resourcegroep** | Resources verplaatsen naar een andere resourcegroep/abonnement of tussen verschillende regio's.<br/><br/> Als u resources over verschillende regio's gaat verplaatsen, selecteert u de resources die u wilt verplaatsen in de resourcegroep en gaat u naar de Resource Mover-hub om afhankelijkheden te controleren en de resources naar de doelregio te verplaatsen. | [Resources verplaatsen naar een andere resourcegroep/abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).<br/><br/> [Resources naar een andere regio verplaatsen vanuit een resourcegroep](move-region-within-resource-group.md).
**Verplaatsen vanuit de Resource Mover-hub** | Resources verplaatsen tussen regio's. <br/><br/> U kunt resources verplaatsen naar een doelregio, of naar een specifieke beschikbaarheidszone, of een beschikbaarheidsset in de doelregio. | [Resources verplaatsen tussen regio's in de Resource Mover-hub]().
**Virtuele machines verplaatsen met Site Recovery** | Gebruik deze optie voor het verplaatsen van Azure-VM's tussen openbare clouds en clouds van de overheid.<br/><br/> Gebruik deze optie als u VM's tussen beschikbaarheidszones in dezelfde regio wilt verplaatsen. |[Resources verplaatsen tussen openbare clouds/clouds van de overheid](../site-recovery/region-move-cross-geos.md) [Resources verplaatsen naar beschikbaarheidszones in dezelfde regio](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](about-move-process.md) over Resource Mover-onderdelen en het verplaatsingsproces.
