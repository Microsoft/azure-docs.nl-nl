---
title: VM Insights-gast status inschakelen (preview)
description: Hierin wordt beschreven hoe u de status van een VM Insights-gast in uw abonnement kunt inschakelen en hoe u virtuele machines kunt voorbereiden.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/16/2020
ms.custom: references_regions
ms.openlocfilehash: 5d4ff622f69445880c0de8cb74dc1aeee422c89b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052157"
---
# <a name="enable-vm-insights-guest-health-preview"></a>VM Insights-gast status inschakelen (preview)
Met de VM Insights-gast status kunt u de status van een virtuele machine bekijken, zoals gedefinieerd door een set prestatie metingen die regel matig worden steek proeven. In dit artikel wordt beschreven hoe u deze functie inschakelt in uw abonnement en hoe u gast bewaking voor elke virtuele machine inschakelt.

## <a name="current-limitations"></a>Huidige beperkingen
De status van de VM Insights-gast heeft de volgende beperkingen in de open bare Preview:

- Er worden momenteel alleen virtuele Azure-machines ondersteund. Azure Arc voor servers wordt momenteel niet ondersteund.


## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
Op de virtuele machine moet een van de volgende besturings systemen worden uitgevoerd: 

  - Ubuntu 16,04 LTS, Ubuntu 18,04 LTS
  - Windows Server 2012 of hoger

## <a name="supported-regions"></a>Ondersteunde regio’s

De virtuele machine moet zich in een van de volgende regio's bevinden:

- Australië - centraal
- Australië - oost
- Australië - zuidoost
- Canada - midden
- India - centraal
- Central US
- Azië - oost
- VS - oost
- VS - oost 2
- VS-Oost 2 EUAP
- Frankrijk - centraal
- Duitsland - west-centraal
- Japan - oost
- Korea - centraal
- VS - noord-centraal
- Europa - noord
- VS - zuid-centraal
- Zuid-Afrika - noord
- Azië - zuidoost
- Zwitserland - noord
- Verenigd Koninkrijk Zuid
- Verenigd Koninkrijk West
- VS - west-centraal
- Europa -west
- VS - west
- VS - west 2


Log Analytics werk ruimte moet zich in een van de volgende regio's bevinden:

- Australië - centraal
- Australië - oost
- Australië - zuidoost
- Canada - midden
- Canada India
- Central US
- Azië - oost
- VS - oost
- VS - oost 2
- VS-Oost 2 EUAP
- Frankrijk - centraal
- Japan - oost
- VS - noord-centraal
- Europa - noord
- VS - zuid-centraal
- Azië - zuidoost
- Zwitserland - noord
- Verenigd Koninkrijk Zuid
- Europa-west regio
- VS - west
- VS - west 2

## <a name="prerequisites"></a>Vereisten

- De virtuele machine moet onboarded zijn voor VM Insights.
- De door de gebruiker uitgevoerde stappen voor het uitvoeren van de voor bereiding moeten een mini maal niveau van Inzender toegang hebben tot het abonnement waar de virtuele machine en gegevens verzamelings regel zich bevinden.
- Vereiste Azure-resource providers moeten zijn geregistreerd, zoals wordt beschreven in de volgende sectie.

## <a name="register-required-azure-resource-providers"></a>Vereiste Azure-resource providers registreren
De volgende Azure-resource providers worden geregistreerd voor uw abonnement om de gast status van de VM Insights in te scha kelen. 

- Micro soft. WorkloadMonitor
- Microsoft.Insights

U kunt een van de beschik bare methoden gebruiken om een resource provider te registreren zoals beschreven in [Azure-resource providers en-typen](../../azure-resource-manager/management/resource-providers-and-types.md). U kunt ook de volgende voorbeeld opdracht gebruiken met armclient, Postman of een andere methode om een geverifieerde oproep naar Azure Resource Manager te maken:

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Schakel een virtuele machine in met behulp van Azure Portal
Wanneer u gaststatus inschakelt voor een virtuele machine in Azure Portal, wordt de hele vereiste configuratie voor u uitgevoerd. Dit omvat het maken van de regel voor het verzamelen van gegevens, het installeren van de gast status uitbreiding op de virtuele machine en het maken van een koppeling met de regel voor het verzamelen van gegevens.

Klik in de weer gave **aan de slag** in VM Insights op de koppeling naast het upgrade bericht voor een virtuele machine en klik vervolgens op de knop **bijwerken** . U kunt ook meerdere virtuele machines selecteren om ze samen te upgraden.

![Status functie inschakelen op virtuele machine](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>Schakel een virtuele machine in met behulp van Resource Manager-sjabloon
Er zijn drie stappen vereist om virtuele machines in te scha kelen met behulp van Azure Resource Manager.

- Regel voor het verzamelen van gegevens maken.
- De gast status uitbreiding op elke virtuele machine installeren
- Maak een koppeling tussen de virtuele machine en de gegevensverzamelingsregel.

### <a name="create-data-collection-rule-dcr"></a>Een regel voor het verzamelen van gegevens maken (DCR)

> [!NOTE]
> Als u een virtuele machine inschakelt met behulp van de Azure Portal, wordt de regel voor gegevens verzameling die hier wordt beschreven, voor u gemaakt. In dit geval hoeft u deze stap niet uit te voeren.

Configuratie voor de monitors in de VM Insights-status van de gast wordt opgeslagen in de [regels voor gegevens verzameling (DCR)](../agents/data-collection-rule-overview.md). Elke virtuele machine met de gast status uitbreiding heeft een koppeling met deze regel nodig.

> [!NOTE]
> U kunt aanvullende regels voor het verzamelen van gegevens maken om de standaard configuratie van monitors te wijzigen, zoals beschreven in [bewaking configureren in VM Insights-gast status (preview)](vminsights-health-configure.md).

Voor de sjabloon zijn waarden vereist voor de volgende para meters:

- **defaultHealthDataCollectionRuleName**: behoud de standaard naam die in de sjabloon is gedefinieerd.
- **destinationWorkspaceResourceId**: Resource-id van de log Analytics-werk ruimte die wordt gebruikt voor het verzamelen van gegevens van virtuele machines.
- **dataCollectionRuleLocation**: regio van de regel voor het verzamelen van gegevens. Dit moet overeenkomen met de regio van Log Analytics werk ruimte.


Implementeer de sjabloon met een [implementatie methode voor Resource Manager-sjablonen](../../azure-resource-manager/templates/deploy-powershell.md). Met de volgende opdrachten implementeert u de sjabloon en het parameter bestand met behulp van Power shell of Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---

Met de regel voor het verzamelen van gegevens die in de Resource Manager-sjabloon hieronder is gedefinieerd, worden alle monitors voor de virtuele machines met de gast status uitbreiding ingeschakeld. Het moet gegevens bronnen bevatten voor elk van de prestatie meter items die door de monitors worden gebruikt.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data collection rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "performanceCounters": [
              {
                  "name": "VMHealthPerfCounters",
                  "streams": [ "Microsoft-Perf" ],
                  "scheduledTransferPeriod": "PT1M",
                  "samplingFrequencyInSeconds": 60,
                  "counterSpecifiers": [
                      "\\LogicalDisk(*)\\% Free Space",
                      "\\Memory\\Available Bytes",
                      "\\Processor(_Total)\\% Processor Time"
                  ]
              }
          ],
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              },
              "inputDataSources": [
                  "VMHealthPerfCounters"
              ]

            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },                  
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>Voorbeeld parameter bestand

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "destinationWorkspaceResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



### <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>Gast status uitbreiding installeren en koppelen aan gegevens verzamelings regel
Gebruik de volgende Resource Manager-sjabloon om een virtuele machine voor gast status in te scha kelen. Hiermee wordt de status uitbreiding gast geïnstalleerd en wordt de koppeling gemaakt met de regel voor het verzamelen van gegevens. U kunt deze sjabloon implementeren met behulp [van een implementatie methode voor Resource Manager-sjablonen](../../azure-resource-manager/templates/deploy-powershell.md).


Gebruik bijvoorbeeld de volgende opdrachten voor het implementeren van de sjabloon en het parameterbestand voor een resourcegroep met behulp van PowerShell of Azure CLI.


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>Sjabloonbestand

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>Voorbeeld parameter bestand

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

- [Monitors aanpassen die worden ingeschakeld door VM Insights](vminsights-health-configure.md)