---
title: Een groep maken in verschillende beschikbaarheids zones
description: Meer informatie over het maken van een batch-pool met zonegebonden-beleid om te helpen beschermen tegen fouten.
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: 56e718bedf504b8e69598c2d99ab8b889a470b89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725285"
---
# <a name="create-an-azure-batch-pool-across-availability-zones"></a>Een Azure Batch groep maken in Beschikbaarheidszones

Azure-regio's die ondersteuning bieden aan [Beschikbaarheidszones](https://azure.microsoft.com/global-infrastructure/availability-zones/) hebben mini maal drie afzonderlijke zones, elk met een eigen onafhankelijk energie bron, netwerk en koel systeem. Wanneer u een Azure Batch groep maakt met behulp van de configuratie van de virtuele machine, kunt u ervoor kiezen om uw batch-pool in te richten op Beschikbaarheidszones. Het maken van uw pool met dit zonegebonden-beleid helpt uw batch Compute-knoop punten te beschermen tegen fouten van Azure Data Center-niveau.

U kunt bijvoorbeeld uw pool maken met zonegebonden-beleid in een Azure-regio die drie Beschikbaarheidszones ondersteunt. Als een Azure-Data Center in één beschikbaarheids zone een infrastructuur fout heeft, bevat uw batch-pool nog steeds gezonde knoop punten in de andere twee Beschikbaarheidszones, zodat de groep beschikbaar blijft voor taak planning.

## <a name="regional-support-and-other-requirements"></a>Regionale ondersteuning en andere vereisten

Batch houdt pariteit met Azure over ondersteunende Beschikbaarheidszones. Als u de optie zonegebonden wilt gebruiken, moet uw pool worden gemaakt in een [ondersteunde Azure-regio](../availability-zones/az-region.md).

De Azure-regio waarin de pool wordt gemaakt, moet de aangevraagde VM-SKU in meer dan één zone ondersteunen om ervoor te zorgen dat uw batch-pool kan worden toegewezen aan de verschillende beschikbaarheids zones. U kunt dit valideren door de [API-lijst van resource-sku's](/rest/api/compute/resourceskus/list) aan te roepen en het veld **locationInfo** van [resourceSku](/rest/api/compute/resourceskus/list#resourcesku)te controleren. Zorg ervoor dat er meer dan één zone wordt ondersteund voor de aangevraagde VM-SKU.

Zorg ervoor dat voor de batch-accounts van de [gebruikers abonnements modus](accounts.md#batch-accounts), het abonnement waarin u de pool maakt geen beperking voor de zone aanbieding heeft voor de aangevraagde VM-SKU. U kunt dit controleren door de [API-lijst van resource-sku's](/rest/api/compute/resourceskus/list) aan te roepen en de [ResourceSkuRestrictions](/rest/api/compute/resourceskus/list#resourceskurestrictions)te controleren. Als er een zone beperking bestaat, kunt u een [ondersteunings ticket](/troubleshoot/azure/general/region-access-request-process) verzenden om de zone beperking te verwijderen.

U kunt ook geen groep maken met een zonegebonden-beleid als er communicatie tussen knoop punten is ingeschakeld en gebruikmaakt van een [VM-SKU die ondersteuning biedt voor InfiniBand](../virtual-machines/workloads/hpc/enable-infiniband.md).

## <a name="create-a-batch-pool-across-availability-zones"></a>Een batch-pool maken op een Beschikbaarheidszones

In de volgende voor beelden ziet u hoe u een batch-pool maakt over Beschikbaarheidszones.

> [!NOTE]
> Wanneer u een pool maakt met een zonegebonden-beleid, probeert de batch-service uw pool toe te wijzen aan alle Beschikbaarheidszones in de geselecteerde regio. u kunt geen specifieke toewijzing in de zones opgeven.

### <a name="batch-management-client-net-sdk"></a>.NET-SDK voor Batch Management-client

```csharp
pool.DeploymentConfiguration.VirtualMachineConfiguration.NodePlacementConfiguration = new NodePlacementConfiguration()
    {
        Policy = NodePlacementPolicyType.Zonal
    };

```

### <a name="batch-rest-api"></a>Batch-REST API

REST API URL

```
POST {batchURL}/pools?api-version=2021-01-01.13.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Aanvraagbody

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "16.040-LTS"
        },
        "nodePlacementConfiguration": {
            "policy": "Zonal"
        }
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Meer informatie over het [maken van een pool in een subnet van een virtueel Azure-netwerk](batch-virtual-network.md).
- Meer informatie over [het maken van een Azure batch groep zonder open bare IP-adressen](./batch-pool-no-public-ip-address.md).