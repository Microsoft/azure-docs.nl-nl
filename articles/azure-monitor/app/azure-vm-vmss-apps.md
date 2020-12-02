---
title: Prestaties bewaken op virtuele machines van Azure-Azure-toepassing Insights
description: Bewaking van toepassings prestaties voor Azure VM en virtuele-machine schaal sets van Azure. Grafiek belasting en respons tijd, afhankelijkheids informatie en waarschuwingen instellen voor prestaties.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 6bc70b3d9a1a7a2d3fffb71ad28f2cf64cbd323b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461728"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>De Azure Monitor Application Insights-agent implementeren op virtuele machines van Azure en virtuele-machine schaal sets van Azure

Het inschakelen van bewaking op uw .NET-webtoepassingen die worden uitgevoerd op [virtuele machines van Azure](https://azure.microsoft.com/services/virtual-machines/) en [virtuele-machine schaal sets van Azure](../../virtual-machine-scale-sets/index.yml) is nu nog eenvoudiger dan ooit. Profiteer van de voor delen van het gebruik van Application Insights zonder uw code te wijzigen.

Dit artikel helpt u bij het inschakelen van Application Insights bewaking met behulp van de Application Insights agent en voorziet in voorlopige richt lijnen voor het automatiseren van het proces voor grootschalige implementaties.

> [!IMPORTANT]
> Azure-toepassing Insights-agent voor ASP.NET-toepassingen die worden uitgevoerd op **virtuele machines in Azure en VMSS** , is momenteel beschikbaar als open bare preview. Voor het bewaken van uw ASP.Net-toepassingen die **on-premises** worden uitgevoerd, gebruikt u de [Azure-toepassing Insights-agent voor on-premises servers](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-overview), die algemeen beschikbaar en volledig wordt ondersteund.
> De preview-versie voor virtuele Azure-machines en VMSS wordt zonder een service overeenkomst aangestuurd en wij raden deze niet aan voor productie werkbelastingen. Sommige functies worden mogelijk niet ondersteund en andere hebben mogelijk beperkte mogelijkheden.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="enable-application-insights"></a>Application Insights inschakelen

Er zijn twee manieren om toepassings bewaking in te scha kelen voor virtuele machines van Azure en virtuele-machine schaal sets in azure-toepassingen:

* Zonder **code** via Application Insights agent
    * Deze methode is het gemakkelijkst in te scha kelen en er is geen geavanceerde configuratie vereist. Dit wordt vaak aangeduid als runtime-bewaking.

    * Voor virtuele machines van Azure en virtuele-machine schaal sets van Azure wordt het ten minste aanbevolen dit bewakings niveau in te scha kelen. Daarna kunt u op basis van uw specifieke scenario evalueren of hand matige instrumentatie nodig is.

    * De Application Insights-agent verzamelt automatisch dezelfde afhankelijkheids signalen als de .NET-SDK. Zie de [Automatische verzameling van afhankelijkheden](./auto-collect-dependencies.md#net) voor meer informatie.
        > [!NOTE]
        > Momenteel worden alleen door .NET IIS gehoste toepassingen ondersteund. Gebruik een SDK om ASP.NET Core-, Java-en Node.js-toepassingen te instrumenteren die worden gehost op virtuele machines van Azure en virtuele-machine schaal sets.

* **Code gebaseerd** via SDK

    * Deze benadering is veel meer aanpasbaar, maar het is wel nodig om [een afhankelijkheid toe te voegen aan de Application INSIGHTS SDK NuGet-pakketten](./asp-net.md). Deze methode betekent ook dat u de updates voor de meest recente versie van de pakketten zelf moet beheren.

    * Als u aangepaste API-aanroepen wilt maken voor het bijhouden van gebeurtenissen/afhankelijkheden die niet standaard worden vastgelegd met bewaking op basis van agents, moet u deze methode gebruiken. Bekijk de [API voor het artikel aangepaste gebeurtenissen en metrische gegevens](./api-custom-events-metrics.md) voor meer informatie.

> [!NOTE]
> Als zowel bewaking op basis van de agent als hand matige instrumentatie op basis van SDK wordt gedetecteerd, worden alleen de instellingen voor hand matige instrumentatie gehonoreerd. Dit is om te voor komen dat dubbele gegevens worden verzonden. Raadpleeg de [sectie probleem oplossing](#troubleshooting) hieronder voor meer informatie.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>Application Insights agent voor .NET-toepassingen op Azure virtual machines beheren met Power shell

> [!NOTE]
> Voordat u de Application Insights-Agent installeert, hebt u een connection string nodig. [Maak een nieuwe Application Insights resource](./create-new-resource.md) of kopieer de Connection String van een bestaande Application Insights-resource.

> [!NOTE]
> Nieuw in Power shell? Bekijk de [aan de slag-hand leiding](/powershell/azure/get-started-azureps?view=azps-2.5.0).

De Application Insights-agent installeren of bijwerken als een uitbrei ding voor virtuele Azure-machines
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> U kunt de Application Insights-agent installeren of bijwerken als een uitbrei ding op meerdere Virtual Machines op schaal met een Power shell-lus.

Application Insights agent-extensie verwijderen van de virtuele machine van Azure
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Status van Application Insights agent-extensie voor de virtuele machine van Azure opvragen
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Lijst met geïnstalleerde uitbrei dingen voor Azure virtual machine ophalen
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
U kunt geïnstalleerde uitbrei dingen ook bekijken op de [Blade virtuele Azure-machine](../../virtual-machines/extensions/overview.md) in de portal.

> [!NOTE]
> Controleer de installatie door op Live Metrics Stream te klikken in de Application Insights resource die is gekoppeld aan het connection string dat u hebt gebruikt om de uitbrei ding voor de Application Insights agent te implementeren. Als u gegevens van meerdere Virtual Machines verzendt, selecteert u de virtuele Azure-doel machines onder Server naam. Het kan tot een minuut duren voordat gegevens worden gefloweerd.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Application Insights agent voor .NET-toepassingen beheren op virtuele-machine schaal sets van Azure met behulp van Power shell

De Application Insights-agent installeren of bijwerken als een uitbrei ding voor de schaalset van virtuele Azure-machines
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

De uitbrei ding toepassings bewaking verwijderen van virtuele-machine schaal sets van Azure
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Uitbrei ding van de bewakings status van de query toepassing voor virtuele-machine schaal sets van Azure
```powershell
# Not supported by extensions framework
```

Lijst met geïnstalleerde uitbrei dingen voor virtuele-machine schaal sets van Azure ophalen
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>Problemen oplossen

Zoek tips voor het oplossen van problemen met de Application Insights Monitoring Agent-extensie voor .NET-toepassingen die worden uitgevoerd op virtuele machines van Azure en virtuele-machine schaal sets.

> [!NOTE]
> .NET core-, Java-en Node.js-toepassingen worden alleen ondersteund op virtuele machines van Azure en virtuele-machine schaal sets van Azure via hand matige instrumentatie op basis van SDK. Daarom zijn de volgende stappen niet van toepassing op deze scenario's.

Uitvoer voor uitvoering van extensie wordt vastgelegd in bestanden die in de volgende directory's zijn gevonden:
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over hoe u [een toepassing implementeert in een Azure virtual machine-schaalset](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md).
* [Stel Beschik baarheid-webtesten](monitor-web-app-availability.md) in om te worden gewaarschuwd als uw eind punt niet beschikbaar is.
