---
title: Een Windows-licentie toepassen op virtuele machines voor sessiehosts - Azure
description: Beschrijft hoe u de Windows-licentie kunt toepassen op Windows Virtual Desktop-VM's.
author: Heidilohr
ms.topic: how-to
ms.date: 08/14/2019
ms.author: helohr
ms.openlocfilehash: fa3c9f82e99536b07a27656e0143d6b2fcc89a44
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833864"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Een Windows-licentie toepassen op virtuele machines voor sessiehosts

Klanten met een juiste licentie voor het uitvoeren van Windows Virtual Desktop-workloads komen in aanmerking om een Windows-licentie toe te passen op de virtuele machines van hun sessiehost en deze uit te voeren zonder te betalen voor een andere licentie. Zie prijzen voor Windows Virtual Desktop [meer informatie.](https://azure.microsoft.com/pricing/details/virtual-desktop/)

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Manieren om uw Windows Virtual Desktop gebruiken
Windows Virtual Desktop licentieverlening kunt u een licentie toepassen op elke virtuele Windows- of Windows Server-machine die is geregistreerd als een sessiehost in een hostgroep en gebruikersverbindingen ontvangt. Deze licentie is niet van toepassing op virtuele machines die worden uitgevoerd als bestands shareservers, domeincontrollers, en meer.

Er zijn een aantal manieren om de licentie Windows Virtual Desktop gebruiken:
- U kunt een hostgroep en de virtuele machines van de sessiehost maken met behulp van [Azure Marketplace aanbieding](./create-host-pools-azure-marketplace.md). Op virtuele machines die op deze manier zijn gemaakt, wordt de licentie automatisch toegepast.
- U kunt een hostgroep en de virtuele machines van de sessiehost maken met behulp van de [GitHub Azure Resource Manager sjabloon](./virtual-desktop-fall-2019/create-host-pools-arm-template.md). Op virtuele machines die op deze manier zijn gemaakt, wordt de licentie automatisch toegepast.
- U kunt een licentie toepassen op een bestaande sessiehost-virtuele machine. Volg hiervoor eerst de instructies in Een hostgroep maken met PowerShell om een [hostgroep](./create-host-pools-powershell.md) en gekoppelde VM's te maken. Ga vervolgens terug naar dit artikel om te leren hoe u de licentie kunt toepassen.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Een Windows-licentie toepassen op een sessiehost-VM
Zorg ervoor dat u [de meest recente versie van Azure PowerShell.](/powershell/azure/) Voer de volgende PowerShell-cmdlet uit om de Windows-licentie toe te passen:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Controleer of uw sessiehost-VM gebruikmaakt van het licentievoordeel
Voer na het implementeren van uw VM deze cmdlet uit om het licentietype te controleren:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Op een sessiehost-VM met de toegepaste Windows-licentie ziet u iets als dit:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

VM's zonder de toegepaste Windows-licentie geven iets als het volgende weer:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Voer de volgende cmdlet uit om een lijst weer te geven met alle sessiehost-VM's waarin de Windows-licentie is toegepast in uw Azure-abonnement:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```

## <a name="requirements-for-deploying-windows-server-remote-desktop-services"></a>Vereisten voor het implementeren van Windows Server-Extern bureaublad-services

Als u Windows Server 2019, 2016 of 2012 R2 als Windows Virtual Desktop-hosts in uw implementatie implementeert, moet een Extern bureaublad-services-licentieserver toegankelijk zijn vanaf deze virtuele machines. De Extern bureaublad-services-licentieserver kan zich on-premises of in Azure bevinden. Zie Activate [the Extern bureaublad-services license server (De Extern bureaublad-services-licentieserver activeren) voor meer informatie.](/windows-server/remote/remote-desktop-services/rds-activate-license-server)
