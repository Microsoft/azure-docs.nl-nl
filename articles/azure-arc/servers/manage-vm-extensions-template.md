---
title: VM-extensie inschakelen met behulp Azure Resource Manager sjabloon
description: In dit artikel wordt beschreven hoe u extensies van virtuele machines implementeert op Azure Arc servers die worden uitgevoerd in hybride cloudomgevingen met behulp van een Azure Resource Manager sjabloon.
ms.date: 04/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d32be184a7e5bb713aee83cd3023f271299d3872
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832856"
---
# <a name="enable-azure-vm-extensions-by-using-arm-template"></a>Azure VM-extensies inschakelen met behulp van een ARM-sjabloon

In dit artikel wordt beschreven hoe u een AZURE RESOURCE MANAGER-sjabloon (ARM-sjabloon) gebruikt om Azure VM-extensies te implementeren, ondersteund door Azure Arc ingeschakelde servers.

VM-extensies kunnen worden toegevoegd aan Azure Resource Manager sjabloon en worden uitgevoerd met de implementatie van de sjabloon. Met de VM-extensies die worden ondersteund door Arc-servers, kunt u de ondersteunde VM-extensie implementeren op Linux- of Windows-computers met behulp van Azure PowerShell. Elk voorbeeld hieronder bevat een sjabloonbestand en een parametersbestand met voorbeeldwaarden voor de sjabloon.

>[!NOTE]
>Hoewel meerdere extensies tegelijk kunnen worden gebatcheerd en verwerkt, worden ze serieel geïnstalleerd. Zodra de eerste installatie van de extensie is voltooid, wordt geprobeerd de volgende extensie te installeren.

> [!NOTE]
> Azure Arc-servers biedt geen ondersteuning voor het implementeren en beheren van VM-extensies op virtuele Azure-machines. Zie het volgende artikel overzicht van VM-extensies voor [Azure-VM's.](../../virtual-machines/extensions/overview.md)

## <a name="deploy-the-log-analytics-vm-extension"></a>De Log Analytics VM-extensie implementeren

Als u de Log Analytics-agent eenvoudig wilt implementeren, wordt het volgende voorbeeld gegeven om de agent in Windows of Linux te installeren.

### <a name="template-file-for-linux"></a>Sjabloonbestand voor Linux

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="template-file-for-windows"></a>Sjabloonbestand voor Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
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
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Sla de sjabloon- en parameterbestanden op schijf op en bewerk het parameterbestand met de juiste waarden voor uw implementatie. U kunt de extensie vervolgens installeren op alle verbonden machines binnen een resourcegroep met de volgende opdracht. De opdracht gebruikt de *parameter TemplateFile* om de sjabloon en de parameter *TemplateParameterFile* op te geven om een bestand op te geven dat parameters en parameterwaarden bevat.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgent.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentParms.json"
```

## <a name="deploy-the-custom-script-extension"></a>De aangepaste scriptextensie implementeren

Als u de aangepaste scriptextensie wilt gebruiken, wordt het volgende voorbeeld gegeven om te worden uitgevoerd in Windows en Linux. Zie Aangepaste scriptextensie voor [Windows](../../virtual-machines/extensions/custom-script-windows.md) of Aangepaste scriptextensie voor Linux als u niet bekend bent met de aangepaste [scriptextensie.](../../virtual-machines/extensions/custom-script-linux.md) Er zijn een aantal verschillende kenmerken die u moet begrijpen bij het gebruik van deze extensie met hybride machines:

* De lijst met ondersteunde besturingssystemen met de aangepaste scriptextensie van azure-VM is niet van toepassing op Azure Arc ingeschakelde servers. De lijst met ondersteunde BES's voor Arc-servers vindt u [hier.](agent-overview.md#supported-operating-systems)

* Configuratiedetails met betrekking Virtual Machine Scale Sets Azure-VM's of klassieke VM's zijn niet van toepassing.

* Als uw computers een script extern moeten downloaden en alleen kunnen communiceren via een proxyserver, moet u de Connected [Machine-agent](manage-agent.md#update-or-remove-proxy-settings) configureren om de omgevingsvariabele voor de proxyserver in te stellen.

De configuratie van de aangepaste scriptextensie specificeert zaken als scriptlocatie en de uit te voeren opdracht. Deze configuratie wordt opgegeven in een sjabloon Azure Resource Manager, die hieronder wordt opgegeven voor zowel Linux- als Windows-hybride machines.

### <a name="template-file-for-linux"></a>Sjabloonbestand voor Linux

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

### <a name="template-file-for-windows"></a>Sjabloonbestand voor Windows

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Parameterbestand

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

## <a name="deploy-the-dependency-agent-extension"></a>De afhankelijkheidsagentextensie implementeren

Als u de extensie Azure Monitor Dependency Agent wilt gebruiken, wordt het volgende voorbeeld gegeven om te worden uitgevoerd in Windows en Linux. Als u niet bekend bent met de afhankelijkheidsagent, zie [Overzicht van Azure Monitor agents.](../../azure-monitor/agents/agents-overview.md#dependency-agent)

### <a name="template-file-for-linux"></a>Sjabloonbestand voor Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="template-file-for-windows"></a>Sjabloonbestand voor Windows

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="template-deployment"></a>Sjabloonimplementatie

Sla het sjabloonbestand op schijf op. Vervolgens kunt u de extensie implementeren op de verbonden computer met de volgende opdracht.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\DependencyAgent.json"
```

## <a name="deploy-azure-key-vault-vm-extension-preview"></a>Een Azure Key Vault VM-extensie implementeren (preview)

De volgende JSON toont het schema voor de Key Vault VM-extensie (preview). Voor de extensie zijn geen beveiligde instellingen vereist: alle instellingen worden beschouwd als openbare informatie. Voor de extensie is een lijst met bewaakte certificaten, pollingfrequentie en het doelcertificaatopslag vereist. Met name:

### <a name="template-file-for-linux"></a>Sjabloonbestand voor Linux

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "autoUpgradeMinorVersion":{
            "type": "bool"
        },
        "pollingIntervalInS":{
          "type": "int"
        },
        "certificateStoreName":{
          "type": "string"
        },
        "certificateStoreLocation":{
          "type": "string"
        },
        "observedCertificates":{
          "type": "string"
        },
        "msiEndpoint":{
          "type": "string"
        },
        "msiClientId":{
          "type": "string"
        }
},
"resources": [
   {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/KVVMExtensionForLinux')]",
      "apiVersion": "2019-12-12",
      "location": "[parameters('location')]",
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ignored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
          },
          "authenticationSettings": {
                "msiEndpoint":  <MSI endpoint e.g.: "http://localhost:40342/metadata/identity">,
                "msiClientId":  <MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
      }
    }
  }
 ]
}
```

### <a name="template-file-for-windows"></a>Sjabloonbestand voor Windows

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "autoUpgradeMinorVersion":{
            "type": "bool"
        },
        "pollingIntervalInS":{
          "type": "int"
        },
        "certificateStoreName":{
          "type": "string"
        },
        "linkOnRenewal":{
          "type": "bool"
        },
        "certificateStoreLocation":{
          "type": "string"
        },
        "requireInitialSync":{
          "type": "bool"
        },
        "observedCertificates":{
          "type": "string"
        },
        "msiEndpoint":{
          "type": "string"
        },
        "msiClientId":{
          "type": "string"
        }
},
"resources": [
   {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/KVVMExtensionForWindows')]",
      "apiVersion": "2019-12-12",
      "location": "[parameters('location')]",
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": "3600",
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net"
        },
        "authenticationSettings": {
                "msiEndpoint": <MSI endpoint e.g.: "http://localhost:40342/metadata/identity">,
                "msiClientId": <MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
      }
    }
  }
 ]
}
```

> [!NOTE]
> De URL's van uw waargenomen certificaten moeten de vorm `https://myVaultName.vault.azure.net/secrets/myCertName` hebben.
>
> Dit komt doordat het pad het volledige certificaat, met inbegrip van `/secrets` de persoonlijke sleutel, retourneert, terwijl het pad dat niet `/certificates` doet. Meer informatie over certificaten vindt u hier: [Key Vault Certificaten](../../key-vault/general/about-keys-secrets-certificates.md)

### <a name="template-deployment"></a>Sjabloonimplementatie

Sla het sjabloonbestand op schijf op. Vervolgens kunt u de extensie implementeren op de verbonden computer met de volgende opdracht.

> [!NOTE]
> Voor de VM-extensie moet een door het systeem toegewezen identiteit worden toegewezen voor verificatie bij Key Vault. Zie [How to authenticate to Key Vault using managed identity](managed-identity-authentication.md) for Windows and Linux Arc enabled servers (Verificatie met beheerde identiteit voor Servers met Windows en Linux Arc).

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\KeyVaultExtension.json"
```

## <a name="deploy-the-azure-defender-integrated-scanner"></a>De Azure Defender scanner implementeren

Als u de Azure Defender scannerextensie wilt gebruiken, wordt het volgende voorbeeld gegeven om te worden uitgevoerd in Windows en Linux. Zie Overview of Azure Defender vulnerability [assessment solution](../../security-center/deploy-vulnerability-assessment-vm.md) for hybrid machines (Overzicht van de oplossing voor Azure Defender evaluatie van beveiligingsleed voor hybride machines) als u niet bekend bent met de geïntegreerde scanner.

### <a name="template-file-for-windows"></a>Sjabloonbestand voor Windows

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "resourceType/providers/WindowsAgent.AzureSecurityCenter",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

### <a name="template-file-for-linux"></a>Sjabloonbestand voor Linux

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "resourceType/providers/LinuxAgent.AzureSecurityCenter",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

### <a name="template-deployment"></a>Sjabloonimplementatie

Sla het sjabloonbestand op schijf op. Vervolgens kunt u de extensie implementeren op de verbonden computer met de volgende opdracht.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\AzureDefenderScanner.json"
```

## <a name="next-steps"></a>Volgende stappen

* U kunt VM-extensies implementeren, beheren en verwijderen met behulp van [Azure PowerShell](manage-vm-extensions-powershell.md), vanuit [de Azure Portal](manage-vm-extensions-portal.md)of de [Azure CLI.](manage-vm-extensions-cli.md)

* Informatie over probleemoplossing vindt u in de handleiding Problemen met [VM-extensies oplossen.](troubleshoot-vm-extensions.md)
