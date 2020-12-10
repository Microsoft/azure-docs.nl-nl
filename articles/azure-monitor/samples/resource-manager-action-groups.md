---
title: Resource Manager-voorbeeldsjablonen voor actiegroepen
description: Azure Resource Manager-voorbeeldsjablonen voor het implementeren van Azure Monitor-actiegroepen.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 12/03/2020
ms.openlocfilehash: 55b56d9eeb2c7b05af86c42d12e82aef6d806b0a
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600957"
---
# <a name="resource-manager-template-samples-for-action-groups-in-azure-monitor"></a>Resource Manager-voorbeeldsjablonen voor actiegroepen in Azure Monitor
Dit artikel bevat [Azure Resource Manager-sjablonen](../../azure-resource-manager/templates/template-syntax.md) die dienen als voorbeeld voor het maken van [actiegroepen](../platform/action-groups.md) in Azure Monitor. Elk voorbeeld bevat een sjabloonbestand en een parameterbestand met voorbeeldwaarden voor het sjabloon.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="create-an-action-group"></a>Een actiegroep maken
In het volgende voorbeeld wordt een actiegroep gemaakt.


### <a name="template-file"></a>Sjabloonbestand

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name within the resource group for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name up to 12 characters for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com",
            "useCommonAlertSchema": true
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com",
            "useCommonAlertSchema": true
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1",
            "useCommonAlertSchema": true
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2",
            "useCommonAlertSchema": true
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

### <a name="parameter-file"></a>Parameterbestand

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "actionGroupName": {
        "value": "My Action Group"
      },
      "actionGroupShortName": {
        "value": "mygroup"
      }
  }
}
```



## <a name="next-steps"></a>Volgende stappen

* [Meer voorbeeldsjablonen voor Azure Monitor](resource-manager-samples.md).
* [Meer informatie over actiegroepen](../platform/action-groups.md).

