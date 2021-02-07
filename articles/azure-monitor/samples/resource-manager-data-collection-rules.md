---
title: Resource Manager-voorbeeldsjablonen voor gegevensverzamelingsregels
description: Voorbeelden van Azure Resource Manager-sjablonen voor het maken van koppelingen tussen regels voor gegevensverzameling en virtuele machines in Azure Monitor.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: f8664886203e32baadda5cdf993fbaf7b2a62ed7
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805915"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Resource Manager-voorbeeldsjablonen voor gegevensverzamelingsregels in Azure Monitor
Dit artikel bevat [Azure Resource Manager-sjablonen](../../azure-resource-manager/templates/template-syntax.md) die dienen als voorbeeld voor het implementeren en configureren van de [Log Analytics-agent](../platform/log-analytics-agent.md) en [de diagnostische extensie](../platform/diagnostics-extension-overview.md) voor virtuele machines in Azure Monitor. Elk voorbeeld bevat een sjabloonbestand en een parameterbestand met voorbeeldwaarden voor het sjabloon.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-association-with-azure-vm"></a>Koppeling maken met Azure VM

In het volgende voor beeld wordt een koppeling gemaakt tussen een virtuele machine van Azure en een regel voor het verzamelen van gegevens.

### <a name="template-file"></a>Sjabloonbestand

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterbestand

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-with-azure-arc"></a>Koppeling maken met Azure Arc

In het volgende voor beeld wordt een koppeling gemaakt tussen een Azure-server met Arc-functionaliteit en een gegevens verzamelings regel.

### <a name="template-file"></a>Sjabloonbestand

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('machineName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterbestand

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>Volgende stappen

* [Meer voorbeeldsjablonen voor Azure Monitor](resource-manager-samples.md).
* [Meer informatie over Log Analytics-agent](../platform/log-analytics-agent.md).
* [Meer informatie over de diagnostische extensie](../platform/diagnostics-extension-overview.md).
