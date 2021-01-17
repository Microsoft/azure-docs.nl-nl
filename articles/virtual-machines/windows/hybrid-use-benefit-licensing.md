---
title: Azure Hybrid Benefit voor Windows Server
description: Lees hoe u uw voor delen voor Windows-Software Assurance kunt maximaliseren om on-premises licenties naar Azure te brengen.
author: xujing-ms
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: c13203c076378e1ff8f213971466eb5f63dfc4f4
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539171"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid Benefit voor Windows Server
Voor klanten met Software Assurance biedt Azure Hybrid Benefit voor Windows Server u de mogelijkheid om uw on-premises Windows Server-licenties te gebruiken en virtuele Windows-machines op Azure uit te voeren tegen lagere kosten. U kunt Azure Hybrid Benefit voor Windows Server gebruiken om nieuwe virtuele machines te implementeren met Windows-besturings systemen. Dit artikel gaat over de stappen voor het implementeren van nieuwe Vm's met Azure Hybrid Benefit voor Windows Server en hoe u bestaande actieve Vm's kunt bijwerken. Zie de [pagina Azure Hybrid Benefit voor Windows Server-licentie verlening](https://azure.microsoft.com/pricing/hybrid-use-benefit/)voor meer informatie over Azure Hybrid Benefit voor Windows Server-licenties en kosten besparingen.

Elke licentie voor 2 processors of elke set van 16 kern licenties heeft recht op twee exemplaren van Maxi maal 8 kern geheugens of één exemplaar van Maxi maal 16 kern geheugens. De Azure Hybrid Benefit voor Standard Edition-licenties kunnen slechts één keer worden gebruikt (on-premises of in Azure). Met de voor delen van Data Center Edition kunt u gelijktijdig gebruik maken van zowel on-premises als in Azure.

Het gebruik van Azure Hybrid Benefit voor Windows Server met virtuele machines met Windows Server-besturings systeem worden nu ondersteund in alle regio's, inclusief Vm's met aanvullende software, zoals SQL Server of Marketplace-software van derden. 


## <a name="classic-vms"></a>Klassieke VM's

Voor klassieke Vm's wordt alleen de implementatie van nieuwe VM vanuit on premises aangepaste installatie kopieën ondersteund. Als u wilt profiteren van de mogelijkheden die in dit artikel worden ondersteund, moet u eerst klassieke Vm's migreren naar het Resource Manager-model.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]
 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Manieren om Azure Hybrid Benefit te gebruiken voor Windows Server
Er zijn enkele manieren om virtuele Windows-machines te gebruiken met de Azure Hybrid Benefit:

1. U kunt Vm's implementeren vanaf een van de installatie kopieën van Windows Server op de Azure Marketplace
2. U kunt een aangepaste VM uploaden en implementeren met behulp van een resource manager-sjabloon of Azure PowerShell
3. U kunt een bestaande virtuele machine in-en uitschakelen tussen het uitvoeren van Azure Hybrid Benefit of kosten voor betalen op aanvraag voor Windows Server
4. U kunt ook Azure Hybrid Benefit voor Windows Server op de schaalset voor virtuele machines Toep assen


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Een virtuele machine maken met Azure Hybrid Benefit voor Windows Server
Alle op Windows Server-besturings systemen gebaseerde installatie kopieën worden ondersteund voor Azure Hybrid Benefit voor Windows Server. U kunt installatie kopieën van het Azure-platform ondersteunen of uw eigen aangepaste installatie kopieën van Windows Server uploaden. 

### <a name="portal"></a>Portal
Als u een virtuele machine met Azure Hybrid Benefit voor Windows Server wilt maken, bladert u naar de onderkant van het tabblad **basis beginselen** tijdens het maken en schakelt u het selectie vakje voor het gebruik van een bestaande Windows Server- **licentie in.** 

### <a name="powershell"></a>PowerShell

```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>CLI
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Sjabloon
In uw Resource Manager-sjablonen moet u een extra para meter `licenseType` opgeven. U kunt meer lezen over het [ontwerpen van Azure Resource Manager sjablonen](../../azure-resource-manager/templates/template-syntax.md)
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Een bestaande VM converteren met behulp van Azure Hybrid Benefit voor Windows Server
Als u een bestaande VM hebt die u wilt converteren om gebruik te maken van Azure Hybrid Benefit voor Windows Server, kunt u het licentie type van uw VM bijwerken door de onderstaande instructies te volgen.

> [!NOTE]
> Als u het licentie type op de VM wijzigt, wordt het systeem niet opnieuw opgestart of wordt er een service-interuption veroorzaakt.  Het is gewoon een update van een meta gegevens vlag.
> 

### <a name="portal"></a>Portal
Via de Blade Portal-VM kunt u de virtuele machine bijwerken voor het gebruik van Azure Hybrid Benefit door de optie configuratie te selecteren en de optie ' Azure Hybrid voordelen ' in te scha kelen

### <a name="powershell"></a>PowerShell
- Bestaande Vm's van Windows Server converteren naar Azure Hybrid Benefit voor Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Windows Server-Vm's converteren met voor delen van betalen naar gebruik

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- Bestaande Vm's van Windows Server converteren naar Azure Hybrid Benefit voor Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Controleren of uw virtuele machine gebruikmaakt van het voor deel van de licentie
Nadat u uw VM via Power shell, Resource Manager-sjabloon of Portal hebt geïmplementeerd, kunt u de instelling in de volgende methoden controleren.

### <a name="portal"></a>Portal
Op de Blade Portal-VM kunt u de wissel knop voor Azure Hybrid Benefit voor Windows Server weer geven door configuratie tabblad te selecteren.

### <a name="powershell"></a>PowerShell
In het volgende voor beeld wordt het licentie type voor één virtuele machine weer gegeven
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Uitvoer:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Deze uitvoer verschilt met de volgende VM die is geïmplementeerd zonder Azure Hybrid Benefit voor Windows Server-licentie verlening:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>CLI
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> Als u het licentie type op de VM wijzigt, wordt het systeem niet opnieuw opgestart of wordt er een service-interuption veroorzaakt. Het is alleen een meta gegevens licentie vlag.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Alle Vm's met Azure Hybrid Benefit voor Windows Server in een abonnement weer geven
Als u alle virtuele machines wilt zien en tellen die zijn geïmplementeerd met Azure Hybrid Benefit voor Windows Server, kunt u de volgende opdracht uitvoeren vanuit uw abonnement:

### <a name="portal"></a>Portal
Op de Blade virtuele machine of virtuele-machine schaal sets kunt u een lijst met al uw virtuele machines en licentie typen weer geven door de tabel kolom zodanig te configureren dat deze "Azure Hybrid Benefit" bevat. De VM-instelling kan de status ingeschakeld, niet ingeschakeld of niet ondersteund hebben.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Een Schaalset voor virtuele machines implementeren met Azure Hybrid Benefit voor Windows Server
In de Resource Manager-sjablonen van de virtuele-machine Scale set moet een extra para meter `licenseType` worden opgegeven in de eigenschap VirtualMachineProfile. U kunt dit doen tijdens het maken of bijwerken van uw schaalset via ARM-sjabloon, Power shell, Azure CLI of REST.

In het volgende voor beeld wordt een ARM-sjabloon gebruikt met een Windows Server 2016 Data Center-installatie kopie:
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
U kunt ook meer te weten komen over het [wijzigen van een schaalset voor virtuele machines](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) voor meer manieren om uw schaalset bij te werken.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [het besparen van geld met de Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Meer informatie over [Veelgestelde vragen over Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Meer informatie over [Azure Hybrid Benefit voor Windows Server-licentie verlening gedetailleerde richt lijnen](/windows-server/get-started/azure-hybrid-benefit)
- Meer informatie over [Azure Hybrid Benefit voor Windows Server en Azure site Recovery de migratie van toepassingen naar Azure nog rendabeler maken](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Meer informatie over [Windows 10 op Azure met multi tenant-hosting recht](./windows-desktop-multitenant-hosting-deployment.md)
- Meer informatie over het [gebruik van Resource Manager-sjablonen](../../azure-resource-manager/management/overview.md)
