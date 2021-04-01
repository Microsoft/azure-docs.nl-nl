---
title: Controleren op Resource Health gebeurtenissen die van invloed zijn op uw AKS-cluster (preview-versie)
description: Controleer de integriteits status van uw AKS-cluster met behulp van Azure Resource Health.
services: container-service
author: yunjchoi
ms.topic: troubleshooting
ms.date: 08/18/2020
ms.author: yunjchoi
ms.openlocfilehash: a409ac944c51d893fc344f82ae83d97559e055ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92070653"
---
# <a name="check-for-resource-health-events-impacting-your-aks-cluster-preview"></a>Controleren op Resource Health gebeurtenissen die van invloed zijn op uw AKS-cluster (preview-versie)


Wanneer u uw container werkbelastingen uitvoert op AKS, wilt u er zeker van zijn dat u problemen kunt oplossen en oplossen zodra deze zich voordoen om de impact op de beschik baarheid van uw workloads te minimaliseren. [Azure resource Health](../service-health/resource-health-overview.md) geeft u inzicht in diverse status gebeurtenissen die ertoe kunnen leiden dat uw AKS-cluster niet beschikbaar is.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="open-resource-health"></a>Resource Health openen

### <a name="to-access-resource-health-for-your-aks-cluster"></a>Om toegang te krijgen tot Resource Health voor uw AKS-cluster:

- Navigeer naar uw AKS-cluster in de [Azure Portal](https://portal.azure.com).
- Selecteer **resource Health** in het linkernavigatievenster.

### <a name="to-access-resource-health-for-all-clusters-on-your-subscription"></a>Voor toegang tot Resource Health voor alle clusters in uw abonnement:

- Zoek naar **service Health** in het [Azure Portal](https://portal.azure.com) en navigeer ernaar.
- Selecteer **resource status** in het linkernavigatievenster.
- Selecteer uw abonnement en stel het resource type in op Azure Kubernetes service (AKS).

![Scherm afbeelding toont de resource status voor uw A K S-clusters.](./media/aks-resource-health/resource-health-check.png)

## <a name="check-the-health-status"></a>Controleer de integriteits status

Azure Resource Health helpt u bij het vaststellen en verkrijgen van ondersteuning voor service problemen die van invloed zijn op uw Azure-resources. Resource Health rapporten over de huidige en eerdere status van uw resources en helpt u te bepalen of het probleem wordt veroorzaakt door een door de gebruiker gestarte actie of een platform gebeurtenis.

Resource Health ontvangt signalen voor uw beheerde cluster om de status van het cluster te bepalen. Hiermee wordt de status van uw AKS-cluster onderzocht en worden de actie rapporten voor elk status signaal vereist. Deze signalen variëren van het automatisch oplossen van problemen, geplande updates, niet-geplande status gebeurtenissen en niet-beschik baarheid, veroorzaakt door door de gebruiker geïnitieerde acties. Deze signalen worden geclassificeerd met behulp van de status van de Azure Resource Health: *beschikbaar, niet* *beschikbaar*, *onbekend* en *gedegradeerd*.

- **Beschikbaar**: wanneer er geen bekende problemen zijn die van invloed zijn op de status van uw cluster, worden resource Health uw cluster als *beschikbaar* gerapporteerd.

- **Niet beschikbaar**: wanneer er sprake is van een platform of een niet-platform gebeurtenis die van invloed is op de status van uw cluster, resource Health rapporteert u uw cluster als niet *beschikbaar*.

- **Onbekend**: wanneer er sprake is van een tijdelijke verbinding met de status waarden van uw cluster, resource Health het cluster als *onbekend* meldt.

- **Gedegradeerd**: wanneer er een probleem is met de status die uw actie vereist, resource Health rapporteert het cluster als *gedegradeerd*.

Ga naar [resource Health Overview](../service-health/resource-health-overview.md#health-status)voor meer informatie over de status van elke status.

### <a name="view-historical-data"></a>Historische gegevens weer geven

U kunt ook de gegevens van de afgelopen 30 dagen van historische Resource Health bekijken in de sectie **status geschiedenis** .

## <a name="next-steps"></a>Volgende stappen

Voer controles uit op uw cluster om cluster problemen verder op te lossen met behulp van [AKS Diagnostics](./concepts-diagnostics.md).