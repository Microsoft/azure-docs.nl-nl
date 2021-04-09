---
title: Azure PowerShell gebruiken om diagnostische gegevens in te scha kelen op een Windows-VM
description: Meer informatie over het gebruik van Power shell om Azure Diagnostics in te scha kelen op een virtuele machine met Windows
ms.topic: article
author: amjads1
ms.author: amjads
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.date: 12/15/2015
ms.openlocfilehash: e242a45e2c0f8247a1c23bf4d43a6e8b0a033d9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560082"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Power shell gebruiken om Azure Diagnostics in te scha kelen op een virtuele machine met Windows

Azure Diagnostics is de mogelijkheid binnen Azure om het verzamelen van diagnostische gegevens op een geïmplementeerde toepassing mogelijk te maken. U kunt de diagnostische extensie gebruiken om diagnostische gegevens te verzamelen, zoals toepassings Logboeken of prestatie meter items van een virtuele Azure-machine (VM) met Windows. 

 

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>De diagnostische uitbrei ding inschakelen als u het Resource Manager-implementatie model gebruikt
U kunt de diagnostische uitbrei ding inschakelen tijdens het maken van een virtuele Windows-machine via het Azure Resource Manager-implementatie model door de extensie configuratie toe te voegen aan de Resource Manager-sjabloon. Zie [een virtuele Windows-machine met controle en diagnostische gegevens maken met behulp van de sjabloon Azure Resource Manager](diagnostics-template.md).

Als u de uitbrei ding voor diagnostische gegevens wilt inschakelen op een bestaande virtuele machine die is gemaakt via het Resource Manager-implementatie model, kunt u de Power shell [-cmdlet Set-AzVMDiagnosticsExtension](/powershell/module/az.compute/set-azvmdiagnosticsextension) gebruiken zoals hieronder wordt weer gegeven.

```azurepowershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path
```


*$diagnosticsconfig _path* het pad is naar het bestand dat de diagnostische configuratie in XML bevat, zoals wordt beschreven in het onderstaande voor [beeld](#sample-diagnostics-configuration) .  

Als in het configuratie bestand voor diagnostische gegevens een **Storage account** -element met de naam van een opslag account is opgegeven, wordt door het script *set-AzVMDiagnosticsExtension* automatisch de diagnostische extensie voor het verzenden van diagnostische gegevens naar dat opslag account ingesteld. Om dit te laten werken, moet het opslag account zich in hetzelfde abonnement benemen als de VM.

Als er geen **Storage account** is opgegeven in de diagnostische configuratie, moet u de para meter *StorageAccountName* door geven aan de cmdlet. Als de para meter *StorageAccountName* is opgegeven, gebruikt de cmdlet altijd het opslag account dat is opgegeven in de para meter en niet de-naam die is opgegeven in het configuratie bestand voor diagnostische gegevens.

Als het diagnostische opslag account zich in een ander abonnement dan de virtuele machine bevindt, moet u de para meters *StorageAccountName* en *StorageAccountKey* expliciet door geven aan de cmdlet. De para meter *StorageAccountKey* is niet nodig wanneer het diagnostische opslag account zich in hetzelfde abonnement bevindt, omdat de cmdlet automatisch de sleutel waarde kan opvragen en instellen bij het inschakelen van de uitbrei ding voor diagnostische gegevens. Als het opslag account voor diagnostische gegevens echter zich in een ander abonnement bevindt, kan de cmdlet de sleutel mogelijk niet automatisch ophalen en moet u de sleutel expliciet opgeven via de para meter *StorageAccountKey* .  

```azurepowershell
Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

Zodra de diagnostische uitbrei ding is ingeschakeld op een virtuele machine, kunt u de huidige instellingen ophalen met behulp van de cmdlet [Get-AzVmDiagnosticsExtension](/powershell/module/az.compute/get-azvmdiagnosticsextension) .

```azurepowershell
Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name
```

De cmdlet retourneert *PublicSettings*, die de diagnostische configuratie bevat. Er zijn twee soorten configuratie ondersteund: WadCfg en xmlCfg. WadCfg is een JSON-configuratie en xmlCfg is XML-configuratie in een indeling met base64-code ring. Als u de XML wilt lezen, moet u deze decoderen.

```azurepowershell
$publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
$encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
$xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
Write-Host $xmlconfig
```

De cmdlet [Remove-AzVmDiagnosticsExtension](/powershell/module/az.compute/remove-azvmdiagnosticsextension) kan worden gebruikt om de diagnostische uitbrei ding van de virtuele machine te verwijderen.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>De diagnostische uitbrei ding inschakelen als u het klassieke implementatie model gebruikt

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

U kunt de cmdlet [set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) gebruiken om een uitbrei ding van diagnostische gegevens in te scha kelen op een virtuele machine die u maakt via het klassieke implementatie model. In het volgende voor beeld ziet u hoe u een nieuwe virtuele machine maakt op basis van het klassieke implementatie model waarin de diagnostische uitbrei ding is ingeschakeld.

```azurepowershell
$VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
$VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
$VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM
```

Als u de diagnostische uitbrei ding wilt inschakelen op een bestaande virtuele machine die is gemaakt via het klassieke implementatie model, gebruikt u eerst de cmdlet [Get-AzureVM](/powershell/module/servicemanagement/azure.service/get-azurevm) om de VM-configuratie op te halen. Werk vervolgens de configuratie van de virtuele machine bij met de extensie voor diagnostische gegevens met behulp van de cmdlet [set-AzureVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) . Ten slotte past u de bijgewerkte configuratie toe op de virtuele machine met behulp van [Update-AzureVM](/powershell/module/servicemanagement/azure.service/update-azurevm).

```azurepowershell
$VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
$VM_Update = Set-AzureVMDiagnosticsExtension  -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM
```

## <a name="sample-diagnostics-configuration"></a>Voorbeeld configuratie voor diagnostische gegevens
De volgende XML kan worden gebruikt voor de open bare configuratie van diagnostische gegevens met de bovenstaande scripts. Met deze voorbeeld configuratie worden verschillende prestatie meter items overgebracht naar het opslag account voor diagnostische gegevens, samen met fouten van de toepassing, de beveiliging en de systeem kanalen in de Windows-gebeurtenis logboeken en eventuele fouten uit de logboeken met diagnostische gegevens van de infra structuur.

De configuratie moet worden bijgewerkt om het volgende te kunnen bevatten:

* Het kenmerk *resourceID* van het element **Metrics** moet worden bijgewerkt met de resource-id voor de virtuele machine.
  
  * De resource-ID kan worden samengesteld met behulp van het volgende patroon: "/Subscriptions/{*-abonnements-id voor het abonnement met de VM*}/resourceGroups/{*de ResourceGroup-naam voor de VM*}/providers/Microsoft.Compute/virtualMachines/{*de VM-naam*}".
  * Als de abonnements-ID voor het abonnement waarin de virtuele machine wordt uitgevoerd, **11111111-1111-1111-1111-111111111111** is, is de naam van de resource groep voor de resource groep **MyResourceGroup** en de naam van de virtuele machine **MyWindowsVM**. vervolgens wordt de waarde voor *resourceID* :
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Zie [Azure Diagnostics metrische tabel in opslag](diagnostics-template.md#wadmetrics-tables-in-storage)voor meer informatie over hoe metrieken worden gegenereerd op basis van de configuratie van de prestatie meter items en de metrische gegevens.
* Het **Storage account** -element moet worden bijgewerkt met de naam van het diagnostische-opslag account.
  
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>Volgende stappen
* Zie voor meer informatie over het gebruik van de Azure Diagnostics mogelijkheden en andere technieken voor het oplossen van problemen [Diagnostische gegevens inschakelen in Azure Cloud Services en virtual machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* In [schema voor diagnostische configuraties](../../azure-monitor/agents/diagnostics-extension-versions.md) worden de verschillende opties voor XML-configuraties voor de uitbrei ding van diagnostische gegevens uitgelegd.