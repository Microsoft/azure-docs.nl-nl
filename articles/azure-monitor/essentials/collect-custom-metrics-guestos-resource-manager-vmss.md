---
title: Metrische gegevens van Windows-schaal sets verzamelen in Azure Monitor met sjabloon
description: De metrische gegevens van het gast besturingssysteem naar het Azure Monitor metrische archief verzenden met een resource manager-sjabloon voor een Windows-schaalset voor virtuele machines
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.openlocfilehash: c5f536295bce07e70606f1ff286b08f8465d59f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048927"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>De metrische gegevens van het gast besturingssysteem naar het Azure Monitor metrische archief verzenden met behulp van een Azure Resource Manager sjabloon voor een Windows-schaalset voor virtuele machines

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Met de Azure Monitor [-uitbrei ding Windows Azure Diagnostics (WAD)](../agents/diagnostics-extension-overview.md)kunt u metrische gegevens en logboeken verzamelen van het gast besturingssysteem (gast besturingssysteem) dat wordt uitgevoerd als onderdeel van een virtuele machine, Cloud service of Azure service Fabric cluster. De uitbrei ding kan telemetrie verzenden naar een groot aantal verschillende locaties die in het eerder gekoppelde artikel worden vermeld.  

In dit artikel wordt het proces beschreven voor het verzenden van de prestatie gegevens voor het gast besturingssysteem voor een Windows-schaalset voor virtuele machines naar het Azure Monitor-gegevens archief. Vanaf Windows Azure Diagnostics versie 1,11 kunt u metrische gegevens rechtstreeks naar de opslag voor metrische gegevens van Azure Monitor schrijven, waar de standaard waarden voor het platform al zijn verzameld. Door ze op deze locatie op te slaan, hebt u toegang tot dezelfde acties die beschikbaar zijn voor de metrische gegevens van het platform. Acties omvatten bijna realtime waarschuwingen, grafieken, route ring, toegang vanaf de REST API, en meer. In het verleden schreef de Windows Azure Diagnostics-uitbrei ding naar Azure Storage, maar niet voor het gegevens archief van Azure Monitor.  

Als u niet bekend bent met Resource Manager-sjablonen, kunt u meer te weten komen over [sjabloon implementaties](../../azure-resource-manager/management/overview.md) en de bijbehorende structuur en syntaxis.  

## <a name="prerequisites"></a>Vereisten

- Uw abonnement moet zijn geregistreerd bij [micro soft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- U moet [Azure PowerShell](/powershell/azure) hebben geïnstalleerd of u kunt [Azure Cloud shell](../../cloud-shell/overview.md)gebruiken. 

- De VM-resource moet zich in een regio bevinden [die aangepaste metrische gegevens ondersteunt](./metrics-custom-overview.md#supported-regions).

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor instellen als een gegevens Sink 
De uitbrei ding Azure Diagnostics gebruikt een functie met de naam **Data sinks** om metrische gegevens en logboeken naar verschillende locaties te routeren. In de volgende stappen ziet u hoe u een resource manager-sjabloon en Power shell gebruikt om een virtuele machine te implementeren met behulp van de nieuwe Azure Monitor Data sink. 

## <a name="author-a-resource-manager-template"></a>Een resource manager-sjabloon maken 
Voor dit voor beeld kunt u een openbaar beschik bare [voorbeeld sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)gebruiken:  

- **Azuredeploy.js** is een vooraf geconfigureerde Resource Manager-sjabloon voor de implementatie van een schaalset voor virtuele machines.

- **Azuredeploy.parameters.js** is een bestand met para meters waarin gegevens worden opgeslagen, zoals de gebruikers naam en het wacht woord die u voor uw virtuele machine wilt instellen. Tijdens de implementatie gebruikt de Resource Manager-sjabloon de para meters die in dit bestand zijn ingesteld. 

Down load en sla beide bestanden lokaal op. 

###  <a name="modify-azuredeployparametersjson"></a>azuredeploy.parameters.jswijzigen op
Open de **azuredeploy.parameters.jsin** het bestand:  
 
- Geef een **vmSKU** op die u wilt implementeren. We raden u aan Standard_D2_v3. 
- Geef een **windowsOSVersion** op voor de schaalset van de virtuele machine. We raden 2016-Data Center aan. 
- Geef een naam op voor de resource voor de VM-schaalset die moet worden geïmplementeerd met behulp van een **vmssName** -eigenschap. Een voor beeld is **VMSS-wad-test**.    
- Geef het aantal Vm's op dat u wilt uitvoeren op de schaalset voor virtuele machines met behulp van de eigenschap **instanceCount** .
- Voer waarden in voor **adminUsername** en **adminPassword** voor de schaalset van de virtuele machine. Deze para meters worden gebruikt voor externe toegang tot de Vm's in de schaalset. Gebruik **niet** de items in deze sjabloon om te voor komen dat uw virtuele machine wordt overgenomen. Bots scan Internet voor gebruikers namen en wacht woorden in open bare GitHub-opslag plaatsen. Waarschijnlijk worden er Vm's getest met deze standaard waarden. 


###  <a name="modify-azuredeployjson"></a>azuredeploy.jswijzigen op
Open de **azuredeploy.jsin** het bestand. 

Voeg een variabele toe om de gegevens van het opslag account in de Resource Manager-sjabloon op te slaan. Logboeken of prestatie meter items die zijn opgegeven in het configuratie bestand voor diagnostische gegevens worden geschreven naar de Azure Monitor metrische opslag en het opslag account dat u hier opgeeft: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Zoek de definitie van de virtuele-machine schaal sets in de sectie resources en voeg de sectie **identiteit** toe aan de configuratie. Dit zorgt ervoor dat Azure een systeem identiteit toewijst. Met deze stap zorgt u er ook voor dat de virtuele machines in de schaalset de metrische gegevens over de gast over zichzelf kunnen verzenden naar Azure Monitor:  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

Zoek in de resource van de virtuele-machine Scale set de sectie **virtualMachineProfile** . Voeg een nieuw profiel met de naam **extensionsProfile** toe om uitbrei dingen te beheren.  


Voeg in de **extensionProfile** een nieuwe extensie toe aan de sjabloon, zoals wordt weer gegeven in de sectie **VMSS-wad-extension** .  Dit gedeelte is de beheerde identiteiten voor de Azure-resources-extensie die ervoor zorgt dat de gegevens die worden verzonden, worden geaccepteerd door Azure Monitor. Het veld **naam** kan een wille keurige naam bevatten. 

Met de volgende code uit de MSI-extensie worden de diagnostische uitbrei dingen en configuratie ook toegevoegd als een extensie resource voor de resource van de virtuele-machine schaalset. U kunt zo nodig prestatie meter items toevoegen of verwijderen: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Voeg een **dependsOn** voor het opslag account toe om ervoor te zorgen dat deze in de juiste volg orde wordt gemaakt: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Een opslag account maken als er nog geen is gemaakt in de sjabloon: 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Sla beide bestanden op en sluit deze. 

## <a name="deploy-the-resource-manager-template"></a>De Resource Manager-sjabloon implementeren 

> [!NOTE]  
> U moet de Azure Diagnostics extensie versie 1,5 of hoger uitvoeren **en** de eigenschap **autoUpgradeMinorVersion:** ingesteld op **True** in uw Resource Manager-sjabloon. Azure laadt vervolgens de juiste extensie wanneer de virtuele machine wordt gestart. Als u deze instellingen niet in uw sjabloon hebt, wijzigt u deze en implementeert u de sjabloon opnieuw. 


Als u de Resource Manager-sjabloon wilt implementeren, gebruikt u Azure PowerShell:  

1. Start PowerShell. 
1. Meld u aan bij Azure met `Login-AzAccount`.
1. Haal uw lijst met abonnementen op met behulp van `Get-AzSubscription` .
1. Stel het abonnement in dat u maakt of werk de virtuele machine bij: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Maak een nieuwe resource groep voor de virtuele machine die wordt geïmplementeerd. Voer de volgende opdracht uit: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Vergeet niet om een Azure-regio te gebruiken waarvoor aangepaste metrische gegevens zijn ingeschakeld. Vergeet niet om een [Azure-regio te gebruiken waarvoor aangepaste metrische gegevens zijn ingeschakeld](./metrics-custom-overview.md#supported-regions).
 
1. Voer de volgende opdrachten uit om de virtuele machine te implementeren:  

   > [!NOTE]  
   > Als u een bestaande schaalset wilt bijwerken, voegt u een **incrementele modus** toe aan het einde van de opdracht. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Nadat de implementatie is voltooid, moet u de schaalset voor virtuele machines in de Azure Portal vinden. Hiermee worden metrische gegevens naar Azure Monitor geverzendd. 

   > [!NOTE]  
   > U kunt fouten rond de geselecteerde **vmSkuSize** uitvoeren. In dat geval gaat u terug naar uw **azuredeploy.jsin** het bestand en werkt u de standaard waarde van de para meter **vmSkuSize** bij. We raden u aan **Standard_DS1_v2** uit te voeren. 


## <a name="chart-your-metrics"></a>Grafieken van uw metrische gegevens 

1. Meld u aan bij Azure Portal. 

1. Selecteer in het menu links de optie **monitor**. 

1. Selecteer **metrische gegevens** op de pagina **monitor** . 

   ![Monitor-metrische gegevens pagina](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Wijzig de aggregatie periode in de **laatste 30 minuten**.  

1. Selecteer in de vervolg keuzelijst resource de schaalset voor virtuele machines die u hebt gemaakt.  

1. Selecteer in de vervolg keuzelijst naam ruimten de optie **Azure. VM. Windows. gast**. 

1. Selecteer in de vervolg keuzelijst metrische gegevens **geheugen \% toegewezen bytes in gebruik**.  

U kunt er ook voor kiezen om de dimensies op deze metrische waarde te gebruiken om deze te tekenen voor een bepaalde virtuele machine of om elke virtuele machine in de schaalset te zetten. 



## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [aangepaste metrische gegevens](./metrics-custom-overview.md).