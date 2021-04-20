---
title: Een pool maken met schijfversleuteling ingeschakeld
description: Meer informatie over het gebruik van schijfversleutelingsconfiguratie om knooppunten te versleutelen met een door het platform beheerde sleutel.
author: pkshultz
ms.topic: how-to
ms.date: 04/16/2021
ms.author: peshultz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01d2ea03768a09c1ad4e019b9e8ed43a26443637
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728514"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Een pool maken met schijfversleuteling ingeschakeld

Wanneer u een Azure Batch maakt met behulp van Virtual [Machine Configuration,](nodes-and-pools.md#virtual-machine-configuration)kunt u rekenknooppunten in de pool versleutelen met een door het platform beheerde sleutel door de schijfversleutelingsconfiguratie op te geven.

In dit artikel wordt uitgelegd hoe u een Batch-pool maakt met schijfversleuteling ingeschakeld.

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Waarom een pool met schijfversleutelingsconfiguratie gebruiken?

Met een Batch-pool kunt u gegevens op het besturingssysteem en tijdelijke schijven van het reken knooppunt openen en opslaan. Door de schijf aan de serverzijde te versleutelen met een door het platform beheerde sleutel worden deze gegevens beveiligd met lage overhead en gemak.

Batch zal een van deze schijfversleutelingstechnologieën toepassen op rekenknooppunten, op basis van poolconfiguratie en regionale ondersteuning.

- [Versleuteling van beheerde schijven in rust met door het platform beheerde sleutels](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Versleuteling op de host met behulp van een door het platform beheerde sleutel](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

U kunt niet opgeven welke versleutelingsmethode wordt toegepast op de knooppunten in uw pool. In plaats daarvan geeft u de doelschijven op die u wilt versleutelen op hun knooppunten en kan Batch de juiste versleutelingsmethode kiezen, zodat de opgegeven schijven op het rekenknooppunt worden versleuteld.

> [!IMPORTANT]
> Als u uw pool met een aangepaste afbeelding [maakt,](batch-sig-images.md)kunt u schijfversleuteling alleen inschakelen als u windows-VM's gebruikt.

## <a name="azure-portal"></a>Azure Portal

Wanneer u een Batch-pool maakt in Azure Portal, selecteert u **TemporaryDisk** of **OsAndTemporaryDisk** onder **Schijfversleutelingsconfiguratie.**

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Schermopname van de optie Schijfversleutelingsconfiguratie in Azure Portal.":::

Nadat de pool is gemaakt, ziet u de schijfversleutelingsconfiguratiedoelen in de sectie **Eigenschappen van de** groep.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Schermopname van de schijfversleutelingsconfiguratiedoelen in de Azure Portal.":::

## <a name="examples"></a>Voorbeelden

De volgende voorbeelden laten zien hoe u het besturingssysteem en tijdelijke schijven in een Batch-pool versleutelt met behulp van de Batch .NET SDK, de Batch-REST API en de Azure CLI.

### <a name="batch-net-sdk"></a>Batch .NET SDK

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Batch-REST API

REST API-URL:

```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Aanvraagtekst:

```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "taskSlotsPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [versleuteling aan de serverzijde van Azure Disk Storage](../virtual-machines/disk-encryption.md).
- Zie Batch-servicewerkstroom en [-resources](batch-service-workflow-features.md)voor een uitgebreid overzicht van Batch.
