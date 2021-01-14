---
title: Algemene Power shell-opdrachten voor Azure Virtual Machines
description: Algemene Power shell-opdrachten waarmee u aan de slag met het maken en beheren van virtuele machines in Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 074a088e0fb342b5d1064d385d819c48ee089c5e
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201804"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Algemene PowerShell-opdrachten voor het maken en beheren van virtuele Azure-machines

In dit artikel worden enkele van de Azure PowerShell opdrachten beschreven die u kunt gebruiken om virtuele machines in uw Azure-abonnement te maken en te beheren.  Voor gedetailleerde hulp bij specifieke opdracht regel opties en-opties kunt u de *opdracht* **Get-Help** gebruiken.

 

Deze variabelen kunnen nuttig zijn als u meer dan een van de opdrachten in dit artikel uitvoert:

- $location: de locatie van de virtuele machine. U kunt [Get-AzLocation](/powershell/module/az.resources/get-azlocation) gebruiken om een [geografische regio](https://azure.microsoft.com/regions/) te vinden die geschikt is voor u.
- $myResourceGroup: de naam van de resource groep die de virtuele machine bevat.
- $myVM: de naam van de virtuele machine.

## <a name="create-a-vm---simplified"></a>Een virtuele machine maken-vereenvoudigd

| Taak | Opdracht |
| ---- | ------- |
| Een eenvoudige virtuele machine maken | [New-AzVM](/powershell/module/az.compute/new-azvm) -name $myVM <BR></BR><BR></BR> New-AzVM heeft een reeks *vereenvoudigde* para meters, waarbij alles wat vereist is, één naam is. De waarde voor-name wordt gebruikt als de naam van alle resources die nodig zijn voor het maken van een nieuwe virtuele machine. U kunt meer opgeven, maar dit is wel het vereiste.|
| Een VM maken van een aangepaste installatiekopie | New-AzVm-ResourceGroupName $myResourceGroup-name $myVM image naam "myImage"-locatie $location  <BR></BR><BR></BR>U moet al uw eigen [beheerde installatie kopie](capture-image-resource.md)hebben gemaakt. U kunt een installatie kopie gebruiken om meerdere, identieke Vm's te maken. |



## <a name="create-a-vm-configuration"></a>Een VM-configuratie maken

| Taak | Opdracht |
| ---- | ------- |
| Een VM-configuratie maken |$vm = [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) -VMName $MyVM-VMSize "Standard_D1_v1"<BR></BR><BR></BR>De VM-configuratie wordt gebruikt om instellingen voor de virtuele machine te definiëren of bij te werken. De configuratie wordt geïnitialiseerd met de naam van de virtuele machine en de [grootte](../sizes.md)ervan. |
| Configuratie-instellingen toevoegen |$vm = [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) -vm $VM-Windows-ComputerName $MyVM-Credential $cred-ProvisionVMAgent-EnableAutoUpdate<BR></BR><BR></BR>De instellingen van het besturings systeem, inclusief [referenties](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1&preserve-view=true) , worden toegevoegd aan het configuratie object dat u eerder hebt gemaakt met behulp van New-AzVMConfig. |
| Een netwerk interface toevoegen |$vm = [add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) -vm $VM-id $NIC. Id<BR></BR><BR></BR>Een virtuele machine moet een [netwerk interface](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json) hebben om te kunnen communiceren in een virtueel netwerk. U kunt ook [Get-AzNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) gebruiken om een bestaand netwerk interface object op te halen. |
| Een platform installatie kopie opgeven |$vm = [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) -vm $VM-uitgever ' publisher_name '-bied "publisher_offer"-sku's "product_sku"-versie "nieuwste"<BR></BR><BR></BR>[Informatie over de installatie kopie](cli-ps-findimage.md) wordt toegevoegd aan het configuratie object dat u eerder hebt gemaakt met New-AzVMConfig. Het object dat door deze opdracht wordt geretourneerd, wordt alleen gebruikt wanneer u de besturingssysteem schijf instelt op het gebruik van een platform installatie kopie. |
| Een virtuele machine maken |[New-AzVM](/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup locatie-$location-VM-$VM<BR></BR><BR></BR>Alle resources worden in een [resource groep](../../azure-resource-manager/management/manage-resource-groups-powershell.md)gemaakt. Voordat u deze opdracht uitvoert, voert u New-AzVMConfig, set-AzVMOperatingSystem, set-AzVMSourceImage, add-AzVMNetworkInterface en set-AzVMOSDisk uit. |
| Een VM bijwerken |[Update-AzVM](/powershell/module/az.compute/update-azvm) -ResourceGroupName $MYRESOURCEGROUP-vm $VM<BR></BR><BR></BR>Haal de huidige VM-configuratie op met Get-AzVM, wijzig de configuratie-instellingen voor het VM-object en voer deze opdracht uit. |

## <a name="get-information-about-vms"></a>Informatie over Vm's ophalen

| Taak | Opdracht |
| ---- | ------- |
| Vm's in een abonnement weer geven |[Get-AzVM](/powershell/module/az.compute/get-azvm) |
| Vm's in een resource groep weer geven |Get-AzVM-ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Gebruik [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup)om een lijst met resource groepen in uw abonnement op te halen. |
| Informatie over een VM ophalen |Get-AzVM-ResourceGroupName $myResourceGroup-naam $myVM |

## <a name="manage-vms"></a>Virtuele machines beheren
| Taak | Opdracht |
| --- | --- |
| Een VM starten |[Start-AzVM](/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup naam $myVM |
| Een VM stoppen |[Stop-AzVM](/powershell/module/az.compute/stop-azvm) -ResourceGroupName $MyResourceGroup-name $myVM |
| Een actieve VM opnieuw opstarten |[Restart-AzVM](/powershell/module/az.compute/restart-azvm) -ResourceGroupName $MyResourceGroup-name $myVM |
| Een VM verwijderen |[Remove-AzVM](/powershell/module/az.compute/remove-azvm) -ResourceGroupName $MyResourceGroup-name $myVM |


## <a name="next-steps"></a>Volgende stappen
* Zie de basis stappen voor het maken van een virtuele machine in [een Windows-VM maken met behulp van Resource Manager en Power shell](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json).
