---
title: De Azure Monitor-agent installeren
description: Opties voor het installeren van de Azure Monitor-agent (AMA) op Azure virtual machines en Azure Arc-servers.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: b2f91f0036a86151588c8c138dac5421ad54e18e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586417"
---
# <a name="install-the-azure-monitor-agent-preview"></a>De Azure Monitor-agent installeren (preview-versie)
In dit artikel vindt u de verschillende opties die momenteel beschikbaar zijn voor het installeren van de [Azure monitor-agent](azure-monitor-agent-overview.md) op virtuele machines van Azure en Azure Arc-servers en de opties voor het maken [van koppelingen met regels voor gegevens verzameling](data-collection-rule-azure-monitor-agent.md) waarmee wordt gedefinieerd welke gegevens de agent moet verzamelen.

## <a name="prerequisites"></a>Vereisten
De volgende vereisten zijn vereist voordat u de Azure Monitor-Agent installeert.

- De [beheerde systeem identiteit](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) moet zijn ingeschakeld op virtuele machines van Azure. Dit is niet vereist voor servers met Azure Arc ingeschakeld. De systeem identiteit wordt automatisch ingeschakeld als de agent wordt geïnstalleerd als onderdeel van het proces voor het [maken en toewijzen van een regel voor het verzamelen van gegevens met behulp van de Azure Portal](#install-with-azure-portal).
- Het [AzureResourceManager-service label](../../virtual-network/service-tags-overview.md) moet zijn ingeschakeld in het virtuele netwerk van de virtuele machine.

## <a name="virtual-machine-extension-details"></a>Details van extensie van virtuele machine
De Azure Monitor-agent wordt geïmplementeerd als een [Azure VM-extensie](../../virtual-machines/extensions/overview.md) met de details in de volgende tabel. Het kan worden geïnstalleerd met behulp van een van de methoden voor het installeren van extensies voor virtuele machines, waaronder de modules die in dit artikel worden beschreven.

| Eigenschap | Windows | Linux |
|:---|:---|:---|
| Publisher | Micro soft. Azure. monitor  | Micro soft. Azure. monitor |
| Type      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |


## <a name="install-with-azure-portal"></a>Installeren met Azure Portal
Als u de Azure Monitor agent wilt installeren met behulp van de Azure Portal, volgt u het proces voor het [maken van een regel](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) voor het verzamelen van gegevens in de Azure Portal. Hierdoor kunt u de gegevens verzamelings regel koppelen aan een of meer virtuele Azure-machines of servers met Azure Arc. De agent wordt geïnstalleerd op een van deze virtuele machines die nog niet aanwezig zijn.


## <a name="install-with-resource-manager-template"></a>Installeren met Resource Manager-sjabloon
U kunt Resource Manager-sjablonen gebruiken om de Azure Monitor-agent te installeren op Azure virtual machines en op Azure Arc-servers en om een koppeling te maken met regels voor het verzamelen van gegevens. U moet een regel voor het verzamelen van gegevens maken voordat u de koppeling kunt maken.

Bekijk voorbeeld sjablonen voor het installeren van de agent en het maken van de koppeling van de volgende: 

- [Sjabloon voor het installeren van Azure Monitor agent (Azure en Azure Arc)](../agents/resource-manager-agent.md#azure-monitor-agent-preview) 
- [Sjabloon voor het maken van een koppeling met een regel voor het verzamelen van gegevens](./resource-manager-data-collection-rules.md)

Installeer de sjablonen met behulp [van een implementatie methode voor Resource Manager-sjablonen](../../azure-resource-manager/templates/deploy-powershell.md) , zoals de volgende opdrachten.

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>Installeren met Power shell
U kunt de Azure Monitor-agent installeren op virtuele machines van Azure en op Azure Arc-servers met behulp van de Power shell-opdracht voor het toevoegen van een extensie van een virtuele machine. 

### <a name="azure-virtual-machines"></a>Azure-VM's
Gebruik de volgende Power shell-opdrachten om de Azure Monitor-agent te installeren op virtuele machines van Azure.
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
---

### <a name="azure-arc-enabled-servers"></a>Servers met Azure Arc
Gebruik de volgende Power shell-opdrachten om de onAzure-servers met Azure Monitor-Arc-agent te installeren.
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <virtual-machine-name> -Location <location>
```
---
## <a name="azure-cli"></a>Azure CLI
U kunt de Azure Monitor-agent installeren op virtuele machines van Azure en op Azure Arc-servers met behulp van de Azure CLI-opdracht voor het toevoegen van een extensie van een virtuele machine. 

### <a name="azure-virtual-machines"></a>Azure-VM's
Gebruik de volgende CLI-opdrachten om de Azure Monitor-agent te installeren op virtuele machines van Azure.
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Servers met Azure Arc
Gebruik de volgende CLI-opdrachten om de Azure Monitor-servers met onAzure-Arc te installeren.

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine machine-extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---


## <a name="next-steps"></a>Volgende stappen

- [Maak een gegevensverzamelings regel](data-collection-rule-azure-monitor-agent.md) voor het verzamelen van gegevens van de agent en verzend deze naar Azure monitor.
