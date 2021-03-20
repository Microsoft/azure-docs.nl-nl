---
title: Windows-licentie Toep assen op virtuele machines van de host-Azure
description: Hierin wordt beschreven hoe u de Windows-licentie voor virtuele bureau blad-Vm's van Windows toepast.
author: ChristianMontoya
ms.topic: how-to
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 5f3749be36f5f035e49fcb862f92180e4902101f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88010137"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Windows-licentie Toep assen op virtuele machines van de sessiehost

Klanten die een juiste licentie hebben voor het uitvoeren van virtuele Windows-Desktop werkbelastingen, komen in aanmerking om een Windows-licentie toe te passen op de virtuele machines van de host en ze uit te voeren zonder dat hiervoor een andere licentie wordt betaald. Zie [prijzen voor Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/)voor meer informatie.

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Manieren om uw Windows Virtual Desktop-licentie te gebruiken
Met Windows Virtual Desktop Licensing kunt u een licentie Toep assen op elke virtuele Windows-of Windows Server-machine die is geregistreerd als gastheer van een sessie in een hostgroep en gebruikers verbindingen ontvangt. Deze licentie is niet van toepassing op virtuele machines die worden uitgevoerd als bestands share servers, domein controllers, enzovoort.

Er zijn een aantal manieren om de Windows-licentie voor virtueel bureau blad te gebruiken:
- U kunt een hostgroep en de virtuele machines van de sessiemap maken met behulp van de [Azure Marketplace-aanbieding](./create-host-pools-azure-marketplace.md). Voor virtuele machines die op deze manier zijn gemaakt, wordt automatisch de licentie toegepast.
- U kunt een hostgroep en de virtuele machines van de sessiemap maken met behulp van de [GitHub Azure Resource Manager sjabloon](./virtual-desktop-fall-2019/create-host-pools-arm-template.md). Voor virtuele machines die op deze manier zijn gemaakt, wordt automatisch de licentie toegepast.
- U kunt een licentie Toep assen op een bestaande virtuele machine van een host. Volg hiervoor de instructies in [een hostgroep maken met Power shell](./create-host-pools-powershell.md) om een hostgroep en gekoppelde vm's te maken en ga vervolgens terug naar dit artikel voor meer informatie over het Toep assen van de licentie.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Een Windows-licentie Toep assen op een Session Host-VM
Zorg ervoor dat u [de nieuwste Azure PowerShell hebt geïnstalleerd en geconfigureerd](/powershell/azure/). Voer de volgende Power shell-cmdlet uit om de Windows-licentie toe te passen:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Controleer of de host-VM van uw sessie gebruikmaakt van het voor deel van de licentie
Nadat u uw virtuele machine hebt geïmplementeerd, voert u deze cmdlet uit en controleert u het licentie type:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Een host-VM met de toegepaste Windows-licentie ziet er ongeveer als volgt uit:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Op Vm's zonder de toegepaste Windows-licentie ziet u ongeveer het volgende:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Voer de volgende cmdlet uit om een lijst met alle host-Vm's te bekijken waarop de Windows-licentie in uw Azure-abonnement is toegepast:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
