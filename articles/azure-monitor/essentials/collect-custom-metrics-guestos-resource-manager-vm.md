---
title: Metrische gegevens van Windows-VM'S verzamelen in Azure Monitor met een sjabloon
description: De metrische gegevens van het gast besturingssysteem naar het data base-archief van de Azure Monitor metriek verzenden met behulp van een resource manager-sjabloon voor een virtuele Windows-machine
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: bwren
ms.openlocfilehash: 8e510cf2e6fed9f9ffdec1dcc4dacf16a866d66b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049012"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine"></a>De metrische gegevens van het gast besturingssysteem naar het Azure Monitor metrische archief verzenden met behulp van een Azure Resource Manager sjabloon voor een virtuele Windows-machine
Prestatie gegevens van het gast besturingssysteem van de virtuele machines van Azure worden niet automatisch verzameld, zoals andere [platform metrische gegevens](./monitor-azure-resource.md#monitoring-data). Installeer de Azure Monitor [Diagnostics-extensie](../agents/diagnostics-extension-overview.md) voor het verzamelen van metrische gegevens van het gast besturingssysteem in de data base Metrics, zodat deze kan worden gebruikt met alle functies van Azure monitor metrische gegevens, waaronder bijna realtime waarschuwingen, grafieken, route ring en toegang vanaf een rest API. In dit artikel wordt het proces beschreven voor het verzenden van de prestaties van een virtuele Windows-machine naar de metrische data base met behulp van een resource manager-sjabloon. 

> [!NOTE]
> Zie [Windows Azure Diagnostics extension (WAD) installeren en configureren](../agents/diagnostics-extension-windows-install.md)voor meer informatie over het configureren van de diagnostische extensie voor het verzamelen van metrische gegevens voor het gast besturingssysteem met behulp van de Azure Portal.


Als u niet bekend bent met Resource Manager-sjablonen, kunt u meer te weten komen over [sjabloon implementaties](../../azure-resource-manager/management/overview.md) en de bijbehorende structuur en syntaxis.

## <a name="prerequisites"></a>Vereisten

- Uw abonnement moet zijn geregistreerd bij [micro soft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md).

- U moet [Azure PowerShell](/powershell/azure) of [Azure Cloud shell](../../cloud-shell/overview.md) hebben geïnstalleerd.

- De VM-resource moet zich in een regio bevinden [die aangepaste metrische gegevens ondersteunt](./metrics-custom-overview.md#supported-regions). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor instellen als een gegevens Sink
De uitbrei ding Azure Diagnostics gebruikt een functie met de naam ' gegevens-sinks ' voor het routeren van metrieken en logboeken naar verschillende locaties. De volgende stappen laten zien hoe u een resource manager-sjabloon en Power shell gebruikt om een virtuele machine te implementeren met behulp van de nieuwe gegevens Sink ' Azure Monitor '.

## <a name="author-resource-manager-template"></a>Resource Manager-sjabloon maken
Voor dit voor beeld kunt u een openbaar beschik bare voorbeeld sjabloon gebruiken. De start sjablonen bevinden zich op https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows .

- **Azuredeploy.js** is een vooraf geconfigureerde Resource Manager-sjabloon voor de implementatie van een virtuele machine.

- **Azuredeploy.parameters.js** is een bestand met para meters waarin gegevens worden opgeslagen, zoals de gebruikers naam en het wacht woord die u voor uw VM wilt instellen. Tijdens de implementatie gebruikt de Resource Manager-sjabloon de para meters die in dit bestand zijn ingesteld.

Down load en sla beide bestanden lokaal op.

### <a name="modify-azuredeployparametersjson"></a>azuredeploy.parameters.jswijzigen op
Het *azuredeploy.parameters.js* bestand openen

1. Voer waarden in voor **adminUsername** en **adminPassword** voor de virtuele machine. Deze para meters worden gebruikt voor externe toegang tot de virtuele machine. Gebruik niet de waarden in deze sjabloon om te voor komen dat uw virtuele machine wordt overgenomen. Bots scannen op internet voor gebruikers namen en wacht woorden in open bare GitHub-opslag plaatsen. Waarschijnlijk worden er Vm's getest met deze standaard waarden.

1. Maak een unieke dnsname voor de virtuele machine.

### <a name="modify-azuredeployjson"></a>azuredeploy.jswijzigen op

Het *azuredeploy.js* bestand openen

Voeg een opslag account-ID toe aan de sectie **variabelen** van de sjabloon na de vermelding voor **storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Voeg deze Managed Service Identity (MSI)-extensie toe aan de sjabloon boven aan de sectie **resources** . De uitbrei ding zorgt ervoor dat Azure Monitor de metrische gegevens accepteert die worden verzonden.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Voeg de **identiteits** configuratie toe aan de VM-resource om ervoor te zorgen dat Azure een systeem identiteit toewijst aan de MSI-extensie. Met deze stap zorgt u ervoor dat de virtuele machine de metrische gegevens over de gast over zichzelf kan verzenden naar Azure Monitor.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

Voeg de volgende configuratie toe om de diagnostische uitbrei ding op een virtuele Windows-machine in te scha kelen. Voor een eenvoudige virtuele machine op basis van Resource Manager kunt u de extensie configuratie toevoegen aan de resource matrix voor de virtuele machine. De regel "sink" &mdash; "AzMonSink" en de bijbehorende "SinksConfig" verderop in de sectie &mdash; zorgen ervoor dat de uitbrei ding de metrische gegevens rechtstreeks naar Azure monitor kan verzenden. U kunt zo nodig prestatie meter items toevoegen of verwijderen.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


Sla beide bestanden op en sluit deze.


## <a name="deploy-the-resource-manager-template"></a>De Resource Manager-sjabloon implementeren

> [!NOTE]
> U moet de Azure Diagnostics extensie versie 1,5 of hoger uitvoeren en de eigenschap **autoUpgradeMinorVersion**: ingesteld op True in uw Resource Manager-sjabloon. Azure laadt vervolgens de juiste extensie wanneer de virtuele machine wordt gestart. Als u deze instellingen niet in uw sjabloon hebt, wijzigt u deze en implementeert u de sjabloon opnieuw.


We maken gebruik van Azure PowerShell om de Resource Manager-sjabloon te implementeren.

1. Start PowerShell.
1. Meld u aan bij Azure met `Login-AzAccount` .
1. Haal uw lijst met abonnementen op met behulp van `Get-AzSubscription` .
1. Stel het abonnement in dat u gebruikt om de virtuele machine te maken of bij te werken in:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Als u een nieuwe resource groep wilt maken voor de virtuele machine die wordt geïmplementeerd, voert u de volgende opdracht uit:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Vergeet niet [een Azure-regio te gebruiken die is ingeschakeld voor aangepaste metrische gegevens](./metrics-custom-overview.md).

1. Voer de volgende opdrachten uit om de virtuele machine te implementeren met behulp van de Resource Manager-sjabloon.
   > [!NOTE]
   > Als u een bestaande virtuele machine wilt bijwerken, kunt u een *incrementele toevoeg modus* toevoegen aan het einde van de volgende opdracht.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Nadat de implementatie is voltooid, moet de virtuele machine zich in de Azure Portal bekomen, waarbij de metrische gegevens naar Azure Monitor worden verzonden.

   > [!NOTE]
   > U kunt fouten rond de geselecteerde vmSkuSize uitvoeren. Als dit het geval is, gaat u terug naar uw azuredeploy.jsin het bestand en werkt u de standaard waarde van de para meter vmSkuSize bij. In dit geval kunt u het beste ' Standard_DS1_v2 ').

## <a name="chart-your-metrics"></a>Grafieken van uw metrische gegevens

1. Meld u aan bij Azure Portal.

2. Selecteer in het menu links de optie **monitor**.

3. Selecteer **metrische gegevens** op de pagina monitor.

   ![Pagina metrische gegevens](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Wijzig de aggregatie periode in de **laatste 30 minuten**.

5. Selecteer in de vervolg keuzelijst resource de virtuele machine die u hebt gemaakt. Als u de naam in de sjabloon niet hebt gewijzigd, moet deze *SimpleWinVM2* zijn.

6. Selecteer in de vervolg keuzelijst naam ruimten de optie **Azure. VM. Windows. gast**

7. Selecteer in de vervolg keuzelijst metrische gegevens **geheugen \% toegewezen bytes in gebruik**.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [aangepaste metrische gegevens](./metrics-custom-overview.md).