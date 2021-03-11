---
title: Een virtuele Windows-machine van niet-beheerde schijven converteren naar beheerde schijven
description: Een Windows-VM van niet-beheerde schijven converteren naar beheerde schijven met behulp van Power shell in het Resource Manager-implementatie model
author: roygara
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 3f586c8907ded618b4cc4aaaadf3c87471cda0b5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550719"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Een virtuele Windows-machine van niet-beheerde schijven converteren naar beheerde schijven

Als u bestaande virtuele Windows-machines (Vm's) hebt die gebruikmaken van niet-beheerde schijven, kunt u de Vm's converteren om beheerde schijven te gebruiken via de [Azure Managed disks](../managed-disks-overview.md) -service. Dit proces converteert zowel de besturingssysteem schijf als eventuele gekoppelde gegevens schijven.

 

## <a name="before-you-begin"></a>Voordat u begint


* Controleer [het plan voor de migratie naar Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Raadpleeg [de veelgestelde vragen over migratie naar Managed disks](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* De oorspronkelijke VHD's en het opslagaccount die vóór de conversie werden gebruikt door de VM worden niet verwijderd. Hiervoor worden nog altijd kosten in rekening gebracht. Als u wilt voorkomen dat er kosten worden doorberekend voor de artefacten, verwijdert u de oorspronkelijke VHD-blobs nadat u hebt gecontroleerd of de conversie is voltooid. Als u deze niet-gekoppelde schijven wilt kunnen verwijderen, raadpleegt u ons artikel niet- [gekoppelde door Azure beheerde en onbeheerde schijven zoeken en verwijderen](find-unattached-disks.md).


## <a name="convert-single-instance-vms"></a>Vm's met één exemplaar converteren
In deze sectie wordt beschreven hoe u virtuele Azure-machines met één exemplaar converteert van niet-beheerde schijven naar beheerde schijven. (Als uw Vm's zich in een beschikbaarheidsset bevinden, raadpleegt u de volgende sectie.) 

1. Hef de toewijzing van de virtuele machine op met behulp van de cmdlet [Stop-AzVM](/powershell/module/az.compute/stop-azvm) . In het volgende voor beeld wordt de toewijzing van de virtuele machine met de naam `myVM` in de resource groep met de naam `myResourceGroup` : 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Converteer de virtuele machine naar Managed disks met behulp van de cmdlet [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk) . Met het volgende proces wordt de vorige VM geconverteerd, met inbegrip van de besturingssysteem schijf en alle gegevens schijven, en wordt de virtuele machine gestart:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Vm's in een beschikbaarheidsset converteren

Als de virtuele machines die u wilt converteren naar Managed disks zich in een beschikbaarheidsset bevinden, moet u eerst de beschikbaarheidsset converteren naar een beheerde beschikbaarheidsset.

1. Converteer de beschikbaarheidsset met behulp van de cmdlet [Update-AzAvailabilitySet](/powershell/module/az.compute/update-azavailabilityset) . In het volgende voor beeld wordt de beschikbaarheidsset bijgewerkt met de naam `myAvailabilitySet` in de resource groep met de naam `myResourceGroup` :

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Als de regio waar uw beschikbaarheidsset zich bevindt slechts 2 beheerde fout domeinen bevat, maar het aantal onbeheerde fout domeinen 3 is, wordt met deze opdracht een fout weer gegeven die vergelijkbaar is met ' het opgegeven aantal fout domeinen 3 moet in het bereik van 1 tot 2 liggen. ' U kunt de fout oplossen door het fout domein bij te werken naar 2 en als volgt bij te werken `Sku` `Aligned` :

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Toewijzing van virtuele machines in de beschikbaarheidsset ongedaan maken en converteren. Met het volgende script wordt elke virtuele machine van elkaar verwijderd met behulp van de cmdlet [Stop-AzVM](/powershell/module/az.compute/stop-azvm) , wordt deze geconverteerd met behulp van [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk)en wordt deze automatisch opnieuw gestart, net zo ver het conversie proces:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Problemen oplossen

Als er een fout optreedt tijdens de conversie, of als een virtuele machine een mislukte status heeft vanwege problemen in een eerdere conversie, voert u de `ConvertTo-AzVMManagedDisk` cmdlet opnieuw uit. Bij een eenvoudige nieuwe poging wordt de blok kering doorgaans opgeheven.
Voordat u converteert, moet u ervoor zorgen dat alle VM-extensies de status ' provisioning geslaagd ' hebben of dat de conversie mislukt met de fout code 409.

## <a name="convert-using-the-azure-portal"></a>Converteren met behulp van de Azure Portal

U kunt ook niet-beheerde schijven converteren naar Managed disks met behulp van de Azure Portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer de virtuele machine in de lijst met virtuele machines in de portal.
3. Selecteer in de Blade voor de virtuele machine **schijven** in het menu.
4. Selecteer boven aan de Blade **schijven** **migreren naar Managed disks**.
5. Als uw virtuele machine zich in een beschikbaarheidsset bevindt, wordt er een waarschuwing weer gegeven op de Blade **migreren naar Managed disks** die u eerst moet converteren van de beschikbaarheidsset. De waarschuwing moet een koppeling hebben waarop u kunt klikken om de beschikbaarheidsset te converteren. Zodra de beschikbaarheidsset is geconverteerd of als uw virtuele machine zich niet in een beschikbaarheidsset bevindt, klikt u op **migreren** om het proces van het migreren van uw schijven naar Managed disks te starten.

De virtuele machine wordt gestopt en opnieuw opgestart nadat de migratie is voltooid.

## <a name="next-steps"></a>Volgende stappen

[Standaard Managed disks converteren naar Premium](convert-disk-storage.md)

Maak een alleen-lezen kopie van een virtuele machine met behulp van [moment opnamen](snapshot-copy-managed-disk.md).