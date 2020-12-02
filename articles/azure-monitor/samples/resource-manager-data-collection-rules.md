---
title: Resource Manager-voorbeeldsjablonen voor gegevensverzamelingsregels
description: Voorbeelden van Azure Resource Manager-sjablonen voor het maken van koppelingen tussen regels voor gegevensverzameling en virtuele machines in Azure Monitor.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 1c059edb1422a572011f167f7f1c02d5e87e5da2
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324821"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Resource Manager-voorbeeldsjablonen voor gegevensverzamelingsregels in Azure Monitor
Dit artikel bevat [Azure Resource Manager-sjablonen](../../azure-resource-manager/templates/template-syntax.md) die dienen als voorbeeld voor het implementeren en configureren van de [Log Analytics-agent](../platform/log-analytics-agent.md) en [de diagnostische extensie](../platform/diagnostics-extension-overview.md) voor virtuele machines in Azure Monitor. Elk voorbeeld bevat een sjabloonbestand en een parameterbestand met voorbeeldwaarden voor het sjabloon.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-association-between-azure-vm-and-rule"></a>Koppeling maken tussen Azure-VM en regel

In het volgende voorbeeld wordt de Azure Monitor-agent op een virtuele Windows Azure-machine geïnstalleerd. Er wordt een koppeling gemaakt tussen een virtuele machine van Azure en een gegevensverzamelingsregel.

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

## <a name="create-association-between-azure-arc-and-rule"></a>Koppeling maken tussen Azure Arc en regel

In het volgende voorbeeld wordt de Azure Monitor-agent op een virtuele Windows Azure-machine geïnstalleerd. Er wordt een koppeling gemaakt tussen een Azure Arc-servermachine en een gegevensverzamelingsregel.

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
