---
title: "Zelfstudie: Windows-VM's maken en beheren met Azure PowerShell"
description: In deze zelfstudie leert u hoe u Azure PowerShell gebruikt voor het maken en beheren van virtuele Windows-machines in Azure
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: b9b7fee32ec8d844452cfcb5eba29859467105e4
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520758"
---
# <a name="tutorial-create-and-manage-windows-vms-with-azure-powershell"></a>Zelfstudie: Windows-VM's maken en beheren met Azure PowerShell

Virtuele machines in Azure bieden een volledig geconfigureerde en flexibele computeromgeving. Deze zelfstudie bevat informatie over basisconcepten voor het implementeren van virtuele Azure-machines (VM's), zoals het selecteren van een VM-grootte, het selecteren van een VM-installatiekopie en het implementeren van een VM. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een virtuele machine maken en verbinding maken met een virtuele machine
> * VM-installatiekopieën selecteren en gebruiken
> * Specifieke VM-grootten weergeven en gebruiken
> * De grootte van een virtuele machine wijzigen
> * De status van een virtuele machine weergeven en begrijpen

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="create-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Voordat een virtuele machine wordt gemaakt, moet een resourcegroep worden gemaakt. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroupVM* gemaakt in de regio *VS - Oost*:

```azurepowershell-interactive
New-AzResourceGroup `
   -ResourceGroupName "myResourceGroupVM" `
   -Location "EastUS"
```

De resourcegroep wordt opgegeven tijdens het maken of wijzigen van een virtuele machine, zoals in deze zelfstudie te zien is.

## <a name="create-a-vm"></a>Een virtuele machine maken

Wanneer u een VM maakt, zijn er diverse opties beschikbaar, zoals besturingssysteeminstallatiekopie, netwerkconfiguratie en beheerdersreferenties. In dit voorbeeld wordt een VM gemaakt met de naam *myVM*, waarop de standaardversie van Windows Server 2016 Datacenter wordt uitgevoerd.

Stel met [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) de gebruikersnaam en het wachtwoord in die nodig zijn voor het beheerdersaccount op de VM:

```azurepowershell-interactive
$cred = Get-Credential
```

Maak de VM met [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="connect-to-vm"></a>Verbinding maken met de virtuele machine

Wanneer de implementatie is voltooid, maakt u via een extern bureaublad verbinding met de VM.

Voer de volgende opdrachten uit om het openbare IP-adres van de VM te retourneren. Noteer dit IP-adres zodat u vanuit uw browser verbinding kunt maken met het adres om webverbindingen te testen.

```azurepowershell-interactive
Get-AzPublicIpAddress `
   -ResourceGroupName "myResourceGroupVM"  | Select IpAddress
```

Gebruik de volgende opdracht op uw lokale machine om een sessie met een extern bureaublad te starten voor de VM. Vervang het IP-adres door *publicIPAddress* van de VM. Wanneer u hierom wordt gevraagd, typt u de referenties die zijn gebruikt bij het maken van de VM.

```powershell
mstsc /v:<publicIpAddress>
```

Selecteer in het venster **Windows-beveiliging** **Meer opties** en vervolgens **Een ander account gebruiken**. Typ de gebruikersnaam en het wachtwoord die u hebt gemaakt voor de VM en klik vervolgens op **OK**.

## <a name="understand-marketplace-images"></a>Inzicht krijgen in Marketplace-installatiekopieën

Azure Marketplace bevat vele installatiekopieën die kunnen worden gebruikt voor het maken van een nieuwe VM. In de vorige stappen is een VM gemaakt met behulp van de Windows Server 2016 Datacenter-installatiekopie. In deze stap wordt de PowerShell-module gebruikt om op de Marketplace te zoeken naar andere Windows-installatiekopieën, die ook als basis voor nieuwe virtuele machines kunnen worden gebruikt. Dit proces bestaat uit het vinden van de uitgever, aanbieding, SKU en eventueel een versienummer om de installatiekopie te [identificeren](cli-ps-findimage.md#terminology).

Gebruik de opdracht [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) om een lijst met uitgevers van installatiekopieën te retourneren:

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

Gebruik de opdracht [Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) om een lijst met aanbiedingen van installatiekopieën te retourneren. Met deze opdracht wordt de geretourneerde lijst gefilterd op de opgegeven uitgever met de naam `MicrosoftWindowsServer`:

```azurepowershell-interactive
Get-AzVMImageOffer `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer"
```

De resultaten zien er ongeveer uit zoals in dit voorbeeld: 

```powershell
Offer             PublisherName          Location
-----             -------------          --------
Windows-HUB       MicrosoftWindowsServer EastUS
WindowsServer     MicrosoftWindowsServer EastUS
WindowsServer-HUB MicrosoftWindowsServer EastUS
```

Met de opdracht [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) wordt vervolgens gefilterd op de naam van de uitgever en de aanbieding, waarna een lijst met namen van installatiekopieën wordt geretourneerd.

```azurepowershell-interactive
Get-AzVMImageSku `
   -Location "EastUS" `
   -PublisherName "MicrosoftWindowsServer" `
   -Offer "WindowsServer"
```

De resultaten zien er ongeveer uit zoals in dit voorbeeld: 

```powershell
Skus                                      Offer         PublisherName          Location
----                                      -----         -------------          --------
2008-R2-SP1                               WindowsServer MicrosoftWindowsServer EastUS  
2008-R2-SP1-smalldisk                     WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2012-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter                        WindowsServer MicrosoftWindowsServer EastUS  
2012-R2-Datacenter-smalldisk              WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter                           WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core               WindowsServer MicrosoftWindowsServer EastUS  
2016-Datacenter-Server-Core-smalldisk     WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-smalldisk                 WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers           WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-Containers-smalldisk WindowsServer MicrosoftWindowsServer EastUS
2016-Datacenter-with-RDSH                 WindowsServer MicrosoftWindowsServer EastUS
2016-Nano-Server                          WindowsServer MicrosoftWindowsServer EastUS
```

Deze informatie kan worden gebruikt om een virtuele machine met een specifieke installatiekopie te implementeren. In dit voorbeeld wordt een VM geïmplementeerd met behulp van de nieuwste versie van Windows Server 2016 met Containers-installatiekopie.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM2" `
    -Location "EastUS" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress2" `
    -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
    -Credential $cred `
    -AsJob
```

Door de parameter `-AsJob` wordt de VM gemaakt als achtergrondtaak, zodat u weer terugkeert naar de PowerShell-prompts. U kunt details van achtergrondtaken bekijken met de cmdlet `Get-Job`.

## <a name="understand-vm-sizes"></a>Inzicht in VM-grootten

De grootte van een VM bepaalt de hoeveelheid rekenresources, zoals CPU, GPU en geheugen, die voor de VM beschikbaar zijn gesteld. Virtuele machines moeten worden gemaakt met een grootte die geschikt is voor de werkbelasting. Als een werkbelasting toeneemt, kan de grootte van een bestaande virtuele machine ook worden gewijzigd.

### <a name="vm-sizes"></a>VM-grootten

In de volgende tabel zijn grootten gecategoriseerd in use-cases.  

| Type                     | Veelgebruikte grootten           |    Beschrijving       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Algemeen gebruik](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Evenwichtige CPU-geheugenverhouding. Ideaal voor ontwikkelen/testen en in kleine tot middelgrote toepassingen en gegevensoplossingen.  |
| [Geoptimaliseerde rekenkracht](../sizes-compute.md)   | Fsv2          | Hoge CPU-geheugenverhouding. Goed voor middelgrootte verkeerstoepassingen, netwerkapparatuur en batchprocessen.        |
| [Geoptimaliseerd geheugen](../sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | Hoge geheugen-kernverhouding. Uiterst geschikt voor relationele-databases, middelgrote tot grote caches en analysefuncties in het geheugen.                 |
| [Geoptimaliseerde opslag](../sizes-storage.md)      | Lsv2, Ls              | Snelle doorvoer van schijfgegevens en IO. Ideaal voor big data-, SQL- en NoSQL-databases.                                                         |
| [GPU](../sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | Gespecialiseerde VM's bedoeld voor intensieve grafische rendering en videobewerking.       |
| [Hoge prestaties](../sizes-hpc.md) | H        | Onze krachtigste CPU-VM's met optionele netwerkinterfaces (RDMA) voor hoge doorvoer. |

### <a name="find-available-vm-sizes"></a>Beschikbare VM-grootten zoeken

Als u een lijst wilt weergeven met de VM-grootten die beschikbaar zijn in een bepaalde regio, gebruikt u de opdracht [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize).

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

## <a name="resize-a-vm"></a>De grootte van een virtuele machine wijzigen

Nadat een virtuele machine is geïmplementeerd, kan de grootte ervan worden gewijzigd om meer of minder resources toe te wijzen.

Voordat u de grootte van een virtuele machine wijzigt, moet u controleren of de grootte die u wenst, beschikbaar in het huidige VM-cluster. Met de opdracht [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) wordt een lijst met grootten geretourneerd.

```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName "myResourceGroupVM" -VMName "myVM"
```

Als de grootte beschikbaar is, kan de grootte van de virtuele machine worden gewijzigd terwijl de virtuele machine wordt uitgevoerd. De virtuele machine moet wel opnieuw worden opgestart tijdens de bewerking.

```azurepowershell-interactive
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_DS3_v2"
Update-AzVM `
   -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
```

Als de grootte die u zoekt niet beschikbaar is in het huidige cluster, moet de toewijzing van de VM ongedaan worden gemaakt voordat de grootte kan worden gewijzigd. Als de toewijzing van een VM ongedaan wordt gemaakt, worden alle gegevens op de tijdelijke schijf verwijderd en wordt het openbare IP-adres gewijzigd, tenzij een statisch IP-adres wordt gebruikt.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
$vm = Get-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -VMName "myVM"
$vm.HardwareProfile.VmSize = "Standard_E2s_v3"
Update-AzVM -VM $vm `
   -ResourceGroupName "myResourceGroupVM"
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM"  `
   -Name $vm.name
```

## <a name="vm-power-states"></a>Energiestatussen voor de virtuele machine

Een Azure VM kan op een van de vele energiestatussen worden ingesteld. 


| Energiestatus | Beschrijving
|----|----|
| Starten | De virtuele machine die wordt gestart. |
| In uitvoering | De virtuele machine die wordt uitgevoerd. |
| Stoppen | De virtuele machine die wordt gestopt. |
| Gestopt | De virtuele machine is gestopt. Virtuele machines met de status Gestopt genereren nog steeds rekenkosten.  |
| Vrijgeven | De VM waarvan de toewijzing ongedaan wordt gemaakt. |
| Toewijzing ongedaan gemaakt | Geeft aan dat de VM is verwijderd uit de hypervisor, maar nog steeds beschikbaar is in het besturingsvenster. Virtuele machines met de status `Deallocated` genereren geen rekenkosten. |
| - | De energiestatus van de VM is onbekend. |


Gebruik de opdracht [Get-AzVM](/powershell/module/az.compute/get-azvm) om de status van een bepaalde virtuele machine op te halen. Zorg ervoor dat u een geldige naam opgeeft voor een VM en een resourcegroep.

```azurepowershell-interactive
Get-AzVM `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

De uitvoer ziet er ongeveer uit zoals in dit voorbeeld:

```powershell
Status
------
PowerState/running
```

Als u de energiestatus van alle VM's in uw abonnement wilt ophalen, gebruikt u de API [Virtual Machines - List All](/rest/api/compute/virtualmachines/listall), waarbij u de parameter **statusOnly** instelt op *true*.

## <a name="management-tasks"></a>Beheertaken

Tijdens de levenscyclus van een VM wilt u wellicht beheertaken uitvoeren, zoals het starten, stoppen of verwijderen van een VM. Misschien wilt u ook scripts maken voor het automatiseren van terugkerende of complexe taken. Met Azure PowerShell kunnen veel algemene beheertaken worden uitgevoerd vanaf de opdrachtregel of in scripts.

### <a name="stop-a-vm"></a>Een VM stoppen

U kunt een VM stoppen en de toewijzing ervan ongedaan maken met de opdracht [Stop-AzVM](/powershell/module/az.compute/stop-azvm):

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM" -Force
```

Als u wilt dat de VM de status Ingericht houdt, gebruikt u de parameter -StayProvisioned.

### <a name="start-a-vm"></a>Een VM starten

```azurepowershell-interactive
Start-AzVM `
   -ResourceGroupName "myResourceGroupVM" `
   -Name "myVM"
```

### <a name="delete-resource-group"></a>Resourcegroep verwijderen

Alles binnen een resourcegroep wordt verwijderd wanneer u de resourcegroep verwijdert.

```azurepowershell-interactive
Remove-AzResourceGroup `
   -Name "myResourceGroupVM" `
   -Force
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd over basistaken voor het maken en beheren van een virtuele machine, zoals:

> [!div class="checklist"]
> * Een virtuele machine maken en verbinding maken met een virtuele machine
> * VM-installatiekopieën selecteren en gebruiken
> * Specifieke VM-grootten weergeven en gebruiken
> * De grootte van een virtuele machine wijzigen
> * De status van een virtuele machine weergeven en begrijpen

In de volgende zelfstudie leert u meer over VM-schijven.  

> [!div class="nextstepaction"]
> [VM-schijven maken en beheren](./tutorial-manage-data-disk.md)