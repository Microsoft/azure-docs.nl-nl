---
title: De configuratie van een Azure Service Fabric-cluster upgraden
description: Meer informatie over het bijwerken van de configuratie waarmee een Service Fabric cluster in azure wordt uitgevoerd met behulp van een resource manager-sjabloon.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 692dc2162159ab61a3ac527e12dac43438084a60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842713"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>De configuratie van een cluster in azure upgraden 

In dit artikel wordt beschreven hoe u de verschillende infrastructuur instellingen voor uw Service Fabric cluster kunt aanpassen. Voor clusters die worden gehost in azure, kunt u instellingen aanpassen via de [Azure Portal](https://portal.azure.com) of met behulp van een Azure Resource Manager sjabloon.

> [!NOTE]
> Niet alle instellingen zijn beschikbaar in de portal en het is een [Best practice om deze aan te passen met behulp van een Azure Resource Manager sjabloon](./service-fabric-best-practices-infrastructure-as-code.md). Portal is alleen voor Service Fabric-scenario van Dev\Test.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Cluster instellingen aanpassen met behulp van Resource Manager-sjablonen
Azure-clusters kunnen worden geconfigureerd via de JSON Resource Manager-sjabloon. Zie [configuratie-instellingen voor clusters](service-fabric-cluster-fabric-settings.md)voor meer informatie over de verschillende instellingen. De volgende stappen laten zien hoe u een nieuwe instelling *MaxDiskQuotaInMB* kunt toevoegen aan de sectie *Diagnostische gegevens* met behulp van Azure resource Explorer.

1. Ga naar https://resources.azure.com
2. Navigeer naar uw abonnement door **abonnementen** van de  ->  **\<Your Subscription>**  ->  **resourceGroups**-  ->  **\<Your Resource Group>**  ->  **providers**  ->  **micro soft. ServiceFabric**-  ->  **clusters** uit te vouwen -> **\<Your Cluster Name>**
3. Selecteer in de rechter bovenhoek de optie **lezen/schrijven.**
4. Selecteer **bewerken** en werk het `fabricSettings` JSON-element bij en voeg een nieuw element toe:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

U kunt cluster instellingen ook aanpassen op een van de volgende manieren met Azure Resource Manager:

- Gebruik de [Azure Portal](../azure-resource-manager/templates/export-template-portal.md) om de Resource Manager-sjabloon te exporteren en bij te werken.
- Gebruik [Power shell](../azure-resource-manager/management/manage-resources-powershell.md) om de Resource Manager-sjabloon te exporteren en bij te werken.
- Gebruik de [Azure cli](../azure-resource-manager/management/manage-resources-cli.md) om de Resource Manager-sjabloon te exporteren en bij te werken.
- Gebruik de opdrachten Azure PowerShell [set-AzServiceFabricSetting](/powershell/module/az.servicefabric/set-azservicefabricsetting) en [Remove-AzServiceFabricSetting](/powershell/module/az.servicefabric/remove-azservicefabricsetting) om de instelling rechtstreeks te wijzigen.
- Gebruik de opdracht Azure CLI [AZ EB cluster setting](/cli/azure/sf/cluster/setting) om de instelling rechtstreeks te wijzigen.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [service Fabric cluster instellingen](service-fabric-cluster-fabric-settings.md).
* Meer informatie over hoe u [uw cluster in-en uitschaalt](service-fabric-cluster-scale-in-out.md).
