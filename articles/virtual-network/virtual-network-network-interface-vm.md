---
title: Netwerkinterfaces toevoegen aan of verwijderen uit Azure-VM's
description: Meer informatie over het toevoegen van netwerkinterfaces aan of het verwijderen van netwerkinterfaces van virtuele machines.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 847f8dbd2d8f4064f12333348a4f03e5c5fcc611
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774247"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Netwerkinterfaces toevoegen aan of verwijderen van netwerkinterfaces van virtuele machines

Meer informatie over het toevoegen van een bestaande netwerkinterface wanneer u een virtuele Azure-machine (VM) maakt. Leer ook hoe u netwerkinterfaces toevoegt aan of verwijdert uit een bestaande VM met de status Gestopt (toewijzing is verwijderd). Met een netwerkinterface kan een azure-VM communiceren met internet-, Azure- en on-premises resources. Een VM heeft een of meer netwerkinterfaces. 

Zie IP-adressen van de netwerkinterface beheren als u IP-adressen voor een netwerkinterface wilt toevoegen, [wijzigen of verwijderen.](virtual-network-network-interface-addresses.md) Zie Netwerkinterfaces beheren voor het maken, wijzigen of verwijderen [van netwerkinterfaces.](virtual-network-network-interface.md)

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u nog geen account hebt, stelt u een Azure-account in met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Voltooi een van deze taken voordat u aan de rest van dit artikel begint:

- **Portalgebruikers:** meld u aan bij [de Azure Portal](https://portal.azure.com) met uw Azure-account.

- **PowerShell-gebruikers:** voer de opdrachten uit in [de Azure Cloud Shell](https://shell.azure.com/powershell)of voer PowerShell uit vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Zoek op Azure Cloud Shell browsertabblad  de vervolgkeuzelijst Omgeving selecteren en kies **vervolgens PowerShell** als dit nog niet is geselecteerd.

    Als u PowerShell lokaal gebruikt, gebruikt u Azure PowerShell moduleversie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az.Network` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Voer `Connect-AzAccount` uit om een verbinding op te zetten met Azure.

- **Azure CLI-gebruikers (opdrachtregelinterface)**: voer de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of voer de CLI uit vanaf uw computer. Gebruik Azure CLI versie 2.0.26 of hoger als u de Azure CLI lokaal gebruikt. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Voer `az login` uit om een verbinding op te zetten met Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Bestaande netwerkinterfaces toevoegen aan een nieuwe VM

Wanneer u via de portal een virtuele machine maakt, maakt de portal een netwerkinterface met standaardinstellingen en koppelt de netwerkinterface voor u aan de virtuele machine. U kunt de portal niet gebruiken om bestaande netwerkinterfaces toe te voegen aan een nieuwe VM of om een VM met meerdere netwerkinterfaces te maken. U kunt beide doen met behulp van de CLI of PowerShell. Zorg ervoor dat u vertrouwd bent met [de beperkingen](#constraints). Als u een VM met meerdere netwerkinterfaces maakt, moet u het besturingssysteem ook zo configureren dat deze correct worden gebruikt nadat u de VM hebt gemaakt. Meer informatie over het [configureren van Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) of [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) voor meerdere netwerkinterfaces.

### <a name="commands"></a>Opdracht

Voordat u de VM maakt, [maakt u een netwerkinterface](virtual-network-network-interface.md#create-a-network-interface).

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Een netwerkinterface toevoegen aan een bestaande VM

Een netwerkinterface toevoegen aan uw virtuele machine:

1. Ga naar de [Azure Portal](https://portal.azure.com) een bestaande virtuele machine te zoeken. Zoek en selecteer **virtuele machines**.

2. Selecteer de naam van uw VM. De VM moet het aantal netwerkinterfaces ondersteunen dat u wilt toevoegen. Zie de grootten in Azure voor [Linux-VM's](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Windows-VM's](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het aantal netwerkinterfaces dat door elke VM-grootte wordt ondersteund.

3. Selecteer in de VM-opdrachtbalk **Stoppen** en vervolgens **OK** in het bevestigingsdialoogvenster. Wacht vervolgens tot de **status van** de VM is gewijzigd in **Gestopt (toewijzing is niet toegewezen).**

4. Kies netwerkverbinding netwerkinterface in **de** menubalk van  >  **de** VM. Kies vervolgens in **Bestaande netwerkinterface koppelen** de netwerkinterface die u wilt koppelen en selecteer **OK.**

    >[!NOTE]
    >De netwerkinterface die u selecteert, kan niet worden versneld netwerken ingeschakeld, er kan geen IPv6-adres aan worden toegewezen en moet aanwezig zijn in hetzelfde virtuele netwerk als de netwerkinterface die momenteel is gekoppeld aan de virtuele machine.

    Als u geen bestaande netwerkinterface hebt, moet u er eerst een maken. Selecteer netwerkinterface maken **om dit te doen.** Zie Een netwerkinterface maken voor meer informatie over het maken [van een netwerkinterface.](virtual-network-network-interface.md#create-a-network-interface) Zie Beperkingen voor meer informatie over aanvullende beperkingen bij het toevoegen van netwerkinterfaces [aan virtuele machines.](#constraints)

5. Kies in de menubalk van de virtuele machine **Overzicht**  >  **Starten om** de virtuele machine opnieuw op te starten.

U kunt nu het besturingssysteem van de VM configureren om meerdere netwerkinterfaces correct te gebruiken. Meer informatie over het [configureren van Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) of [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) voor meerdere netwerkinterfaces.

### <a name="commands"></a>Opdracht

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_add) (reference); [gedetailleerde stappen](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referentie); [gedetailleerde stappen](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Netwerkinterfaces voor een virtuele machine weergeven

U kunt de netwerkinterfaces bekijken die momenteel zijn gekoppeld aan een VM voor meer informatie over de configuratie van elke netwerkinterface en de IP-adressen die zijn toegewezen aan elke netwerkinterface. 

1. Ga naar de [Azure Portal](https://portal.azure.com) een bestaande virtuele machine te zoeken. Zoek en selecteer **virtuele machines**.

    >[!NOTE]
    >Meld u aan met een account aan welke de rol Eigenaar, Inzender of Netwerkbijdrager is toegewezen voor uw abonnement. Zie Ingebouwde rollen voor op rollen gebaseerd toegangsbeheer van Azure voor meer informatie over het toewijzen van [rollen aan accounts.](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

2. Selecteer de naam van de VM waarvoor u gekoppelde netwerkinterfaces wilt weergeven.

3. Selecteer netwerken in de menubalk **van** de VM.

Zie Netwerkinterfaces beheren voor meer informatie over netwerkinterface-instellingen en hoe u [deze kunt wijzigen.](virtual-network-network-interface.md) Zie IP-adressen van de netwerkinterface beheren voor meer informatie over het toevoegen, wijzigen of verwijderen van IP-adressen die zijn toegewezen aan een [netwerkinterface.](virtual-network-network-interface-addresses.md)

### <a name="commands"></a>Opdracht

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az vm nic list](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Een netwerkinterface van een VM verwijderen

1. Ga naar de [Azure Portal](https://portal.azure.com) een bestaande virtuele machine te zoeken. Zoek en selecteer **virtuele machines**.

2. Selecteer de naam van de VM waarvoor u gekoppelde netwerkinterfaces wilt weergeven.

3. Kies stoppen op de werkbalk van de **VM.**

4. Wacht totdat de **status van** de VM is gewijzigd in **Gestopt (toewijzing is niet toegewezen).**

5. Kies in de menubalk van de VM  >  **netwerkenNetwerkinterface loskoppelen.**

6. Selecteer in **het dialoogvenster Netwerkinterface** loskoppelen de netwerkinterface die u wilt loskoppelen. Selecteer vervolgens **OK**.

    >[!NOTE]
    >Als er slechts één netwerkinterface wordt vermeld, kunt u deze niet loskoppelen, omdat er altijd ten minste één netwerkinterface aan een virtuele machine moet zijn gekoppeld.

### <a name="commands"></a>Opdracht

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az_vm_nic_remove) (reference); [gedetailleerde stappen](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referentie); [gedetailleerde stappen](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Beperkingen

- Aan een VM moet ten minste één netwerkinterface zijn gekoppeld.

- Aan een VM kunnen alleen net zoveel netwerkinterfaces zijn gekoppeld als de VM-grootte ondersteunt. Zie de grootten in Azure voor [Linux-VM's](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Windows-VM's](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het aantal netwerkinterfaces dat door elke VM-grootte wordt ondersteund. Alle grootten ondersteunen ten minste twee netwerkinterfaces.

- De netwerkinterfaces die u aan een VM toevoegt, kunnen momenteel niet worden gekoppeld aan een andere VM. Zie Een netwerkinterface maken voor meer informatie over het maken [van netwerkinterfaces.](virtual-network-network-interface.md#create-a-network-interface)

- In het verleden kon u alleen netwerkinterfaces toevoegen aan VM's die meerdere netwerkinterfaces ondersteunden en die zijn gemaakt met ten minste twee netwerkinterfaces. U kunt geen netwerkinterface toevoegen aan een VM die is gemaakt met één netwerkinterface, zelfs niet als de VM-grootte meer dan één netwerkinterface ondersteunt. U kunt daarentegen alleen netwerkinterfaces verwijderen van een VM met ten minste drie netwerkinterfaces, omdat VM's die zijn gemaakt met ten minste twee netwerkinterfaces altijd ten minste twee netwerkinterfaces moesten hebben. Deze beperkingen zijn niet meer van toepassing. U kunt nu een VM maken met een groot aantal netwerkinterfaces (tot het aantal dat wordt ondersteund door de VM-grootte).

- Standaard is de eerste netwerkinterface die is gekoppeld aan een VM de *primaire* netwerkinterface. Alle andere netwerkinterfaces in de VM zijn *secundaire* netwerkinterfaces.

- U kunt bepalen naar welke netwerkinterface u uitgaand verkeer verzendt. Een VM verzendt echter standaard al het uitgaande verkeer naar het IP-adres dat is toegewezen aan de primaire IP-configuratie van de primaire netwerkinterface.

- In het verleden moesten alle VM's binnen dezelfde beschikbaarheidsset één of meerdere netwerkinterfaces hebben. VM's met een groot aantal netwerkinterfaces kunnen nu bestaan in dezelfde beschikbaarheidsset, tot het aantal dat wordt ondersteund door de VM-grootte. U kunt alleen een VM toevoegen aan een beschikbaarheidsset wanneer deze wordt gemaakt. Zie De beschikbaarheid van VM's beheren in Azure voor meer [informatie over beschikbaarheidssets.](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

- U kunt netwerkinterfaces in dezelfde VM verbinden met verschillende subnetten binnen een virtueel netwerk. De netwerkinterfaces moeten echter allemaal zijn verbonden met hetzelfde virtuele netwerk.

- U kunt elk IP-adres voor elke IP-configuratie van een primaire of secundaire netwerkinterface toevoegen aan Azure Load Balancer back-endpool. In het verleden kon alleen het primaire IP-adres voor de primaire netwerkinterface worden toegevoegd aan een back-endpool. Zie IP-adressen toevoegen, wijzigen of verwijderen voor meer informatie over [IP-adressen en configuraties.](virtual-network-network-interface-addresses.md)

- Als u een VM verwijdert, worden de netwerkinterfaces die eraan zijn gekoppeld, niet verwijderd. Wanneer u een VM verwijdert, worden de netwerkinterfaces losgekoppeld van de VM. U kunt deze netwerkinterfaces toevoegen aan verschillende VM's of deze verwijderen.

- Voor het bereiken van de optimale gedocumenteerde prestaties is versneld netwerken vereist. In sommige gevallen moet u versneld netwerken expliciet inschakelen voor [virtuele Windows-](create-vm-accelerated-networking-powershell.md) of [Linux-machines.](create-vm-accelerated-networking-cli.md)

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="next-steps"></a>Volgende stappen

Zie voor het maken van een VM met meerdere netwerkinterfaces of IP-adressen:

|Taak|Hulpprogramma|
|---|---|
|Een virtuele machine met meerdere NIC's maken|[CLI,](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Een enkele NIC-VM met meerdere IPv4-adressen maken|[CLI,](virtual-network-multiple-ip-addresses-cli.md) [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Maak één NIC-VM met een privé-IPv6-adres (achter een Azure Load Balancer)|[CLI,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)en [Azure Resource Manager sjabloon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|