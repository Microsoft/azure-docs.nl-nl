---
title: Een pool maken met schijfversleuteling ingeschakeld
description: Meer informatie over het gebruik van schijf versleutelings configuratie voor het versleutelen van knoop punten met een door een platform beheerde sleutel.
author: pkshultz
ms.topic: how-to
ms.date: 01/27/2021
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 41fc827459b454e2bcb120a925cdab8fcd46e310
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055311"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>Een pool maken met schijfversleuteling ingeschakeld

Wanneer u een Azure Batch groep maakt met behulp van de configuratie van de [virtuele machine](nodes-and-pools.md#virtual-machine-configuration), kunt u reken knooppunten in de groep versleutelen met een door het platform beheerde sleutel door de configuratie van de schijf versleuteling op te geven.

In dit artikel wordt uitgelegd hoe u een batch-pool maakt waarvoor schijf versleuteling is ingeschakeld.

> [!IMPORTANT]
> Ondersteuning voor versleuteling op de host met behulp van een door een platform beheerde sleutel in Azure Batch is momenteel beschikbaar als open bare Preview voor de VS-Oost, VS-West 2, Zuid-Centraal VS, US Gov-Virginia en US Gov-Arizona regio's.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>Waarom een pool met schijf versleutelings configuratie gebruiken?

Met een batch-pool kunt u gegevens op het besturings systeem en de tijdelijke schijven van het reken knooppunt openen en opslaan. Het versleutelen van de schijf aan de server zijde met een door een platform beheerde sleutel beveiligt deze gegevens met lage overhead en gebruiks gemak.

Met batch wordt een van deze schijf versleutelings technologieën toegepast op reken knooppunten, op basis van pool configuratie en regionale ondersteunings mogelijkheden.

- [Beheerde schijf versleuteling op rest met door het platform beheerde sleutels](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Versleuteling op de host met een door een platform beheerde sleutel](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

U kunt niet opgeven welke versleutelings methode wordt toegepast op de knoop punten in uw pool. In plaats daarvan geeft u de doel schijven op die u wilt versleutelen op hun knoop punten en kan batch de juiste versleutelings methode kiezen, zodat de opgegeven schijven worden versleuteld op het reken knooppunt.

> [!IMPORTANT]
> Als u een groep maakt met een [aangepaste installatie kopie](batch-sig-images.md), kunt u schijf versleuteling alleen inschakelen als u Windows-vm's gebruikt.

## <a name="azure-portal"></a>Azure Portal

Wanneer u een batch-pool maakt in de Azure Portal, selecteert u **TemporaryDisk** of **OsAndTemporaryDisk** onder **configuratie van schijf versleuteling**.

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Scherm afbeelding van de configuratie optie voor schijf versleuteling in de Azure Portal.":::

Nadat de groep is gemaakt, ziet u de configuratie doelen van de schijf versleuteling in de sectie **Eigenschappen** van de groep.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Scherm opname van de configuratie doelen van de schijf versleuteling in de Azure Portal.":::

## <a name="examples"></a>Voorbeelden

In de volgende voor beelden ziet u hoe u het besturings systeem en de tijdelijke schijven in een batch-pool kunt versleutelen met behulp van de batch .NET SDK, de batch REST API en de Azure CLI.

### <a name="batch-net-sdk"></a>Batch .NET SDK

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Batch-REST API

REST API URL:

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

- Meer informatie over [versleuteling aan de server zijde van Azure Disk Storage](../virtual-machines/disk-encryption.md).
- Zie [batch service workflow en resources](batch-service-workflow-features.md)voor een uitgebreid overzicht van batch.
