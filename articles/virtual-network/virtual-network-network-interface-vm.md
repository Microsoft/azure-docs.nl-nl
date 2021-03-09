---
title: Netwerk interfaces toevoegen aan of verwijderen uit Azure Vm's
description: Meer informatie over het toevoegen van netwerk interfaces aan of het verwijderen van netwerk interfaces van virtuele machines.
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
ms.openlocfilehash: 43d3da49688a7daeb3ea4e0c1c3dba505dcd3b59
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508330"
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Netwerkinterfaces toevoegen aan of verwijderen van netwerkinterfaces van virtuele machines

Meer informatie over het toevoegen van een bestaande netwerk interface bij het maken van een virtuele Azure-machine (VM). Meer informatie over het toevoegen of verwijderen van netwerk interfaces van een bestaande virtuele machine met de status gestopt (toewijzing ongedaan gemaakt). Met een netwerk interface kan een virtuele machine van Azure communiceren met internet-, Azure-en on-premises resources. Een virtuele machine heeft een of meer netwerk interfaces. 

Zie [IP-adressen van netwerk interfaces beheren](virtual-network-network-interface-addresses.md)als u IP-adressen voor een netwerk interface wilt toevoegen, wijzigen of verwijderen. Zie [netwerk interfaces beheren](virtual-network-network-interface.md)als u netwerk interfaces wilt maken, wijzigen of verwijderen.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u er nog geen hebt, stelt u een Azure-account in met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Voer een van de volgende taken uit voordat u de rest van dit artikel start:

- **Portal gebruikers**: Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw Azure-account.

- **Power shell-gebruikers**: Voer de opdrachten uit in de [Azure Cloud shell](https://shell.azure.com/powershell)of voer Power shell uit vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Zoek de vervolg keuzelijst **omgeving selecteren** op het tabblad Azure Cloud shell browser en kies vervolgens **Power shell** als deze optie nog niet is geselecteerd.

    Als u Power shell lokaal uitvoert, gebruikt u Azure PowerShell module versie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az.Network` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Voer `Connect-AzAccount` uit om een verbinding op te zetten met Azure.

- **Azure-opdracht regel interface (CLI)-gebruikers**: Voer de opdrachten uit in de [Azure Cloud shell](https://shell.azure.com/bash)of voer de CLI uit vanaf uw computer. Gebruik Azure CLI versie 2.0.26 of hoger als u de Azure CLI lokaal uitvoert. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Voer `az login` uit om een verbinding op te zetten met Azure.

## <a name="add-existing-network-interfaces-to-a-new-vm"></a>Bestaande netwerk interfaces toevoegen aan een nieuwe virtuele machine

Wanneer u via de portal een virtuele machine maakt, maakt de portal een netwerk interface met de standaard instellingen en koppelt de netwerk interface voor u aan de VM. U kunt de portal niet gebruiken om bestaande netwerk interfaces toe te voegen aan een nieuwe virtuele machine of om een virtuele machine met meerdere netwerk interfaces te maken. U kunt beide doen met behulp van de CLI of Power shell. Zorg ervoor dat u vertrouwd bent met de [beperkingen](#constraints). Als u een virtuele machine met meerdere netwerk interfaces maakt, moet u ook het besturings systeem configureren om ze goed te gebruiken nadat u de virtuele machine hebt gemaakt. Meer informatie over het configureren van [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) of [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) voor meerdere netwerk interfaces.

### <a name="commands"></a>Opdracht

Voordat u de virtuele machine maakt, [maakt u een netwerk interface](virtual-network-network-interface.md#create-a-network-interface).

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-network-nic-create)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="add-a-network-interface-to-an-existing-vm"></a>Een netwerk interface toevoegen aan een bestaande virtuele machine

Een netwerk interface toevoegen aan de virtuele machine:

1. Ga naar de [Azure Portal](https://portal.azure.com) om een bestaande virtuele machine te vinden. Zoek en selecteer **virtuele machines**.

2. Selecteer de naam van uw VM. De virtuele machine moet ondersteuning bieden voor het aantal netwerk interfaces dat u wilt toevoegen. Als u wilt weten hoeveel netwerk interfaces elke VM-grootte ondersteunt, raadpleegt u de grootten in azure voor [Linux vm's](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Windows-vm's](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Selecteer in de opdracht balk van de virtuele machine **stoppen** en klik vervolgens op **OK** in het bevestigings venster. Wacht tot de **status** van de VM is gewijzigd in **gestopt (toewijzing opgeheven)**.

4. Kies in de menu balk van de **VM netwerk**  >  **Interface netwerk verbinding**. Kies vervolgens in **bestaande netwerk interface koppelen** de netwerk interface die u wilt toevoegen en selecteer **OK**.

    >[!NOTE]
    >Voor de netwerk interface die u selecteert, kan geen versneld netwerk zijn ingeschakeld, er kan geen IPv6-adres aan worden toegewezen en het moet zich in hetzelfde virtuele netwerk bevinden als de netwerk interface die momenteel aan de virtuele machine is gekoppeld.

    Als u geen bestaande netwerk interface hebt, moet u er eerst een maken. Hiertoe selecteert u **netwerk interface maken**. Zie [een netwerk interface maken](virtual-network-network-interface.md#create-a-network-interface)voor meer informatie over het maken van een netwerk interface. Zie voor meer informatie over aanvullende beperkingen bij het toevoegen van netwerk interfaces aan virtuele machines de [beperkingen](#constraints).

5. Kies in de menu balk van de VM **overzicht**  >  **Start** om de virtuele machine opnieuw op te starten.

Nu kunt u het VM-besturings systeem configureren om meerdere netwerk interfaces op de juiste manier te gebruiken. Meer informatie over het configureren van [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) of [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) voor meerdere netwerk interfaces.

### <a name="commands"></a>Opdracht

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[AZ VM NIC add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-add) (naslag); [gedetailleerde stappen](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (verwijzing); [gedetailleerde stappen](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="view-network-interfaces-for-a-vm"></a>Netwerkinterfaces voor een virtuele machine weergeven

U kunt de netwerk interfaces die momenteel zijn gekoppeld aan een virtuele machine weer geven voor meer informatie over de configuratie van elke netwerk interface en de IP-adressen die zijn toegewezen aan elke netwerk interface. 

1. Ga naar de [Azure Portal](https://portal.azure.com) om een bestaande virtuele machine te vinden. Zoek en selecteer **virtuele machines**.

    >[!NOTE]
    >Meld u aan met een account dat is toegewezen aan de rol eigenaar, bijdrager of netwerk bijdrager voor uw abonnement. Zie [ingebouwde rollen voor op rollen gebaseerd toegangs beheer voor Azure voor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)meer informatie over het toewijzen van rollen aan accounts.

2. Selecteer de naam van de virtuele machine waarvoor u gekoppelde netwerk interfaces wilt weer geven.

3. Selecteer in de menu balk van de VM de optie **netwerken**.

Zie [netwerk interfaces beheren](virtual-network-network-interface.md)voor meer informatie over de instellingen van de netwerk interface en hoe u deze kunt wijzigen. Zie [IP-adressen van netwerk interfaces beheren](virtual-network-network-interface-addresses.md)voor meer informatie over het toevoegen, wijzigen of verwijderen van IP-adressen die zijn toegewezen aan een netwerk interface.

### <a name="commands"></a>Opdracht

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[AZ VM NIC-lijst](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-list)|
|PowerShell|[Get-AzVM](/powershell/module/az.compute/get-azvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-a-network-interface-from-a-vm"></a>Een netwerk interface verwijderen van een virtuele machine

1. Ga naar de [Azure Portal](https://portal.azure.com) om een bestaande virtuele machine te vinden. Zoek en selecteer **virtuele machines**.

2. Selecteer de naam van de virtuele machine waarvoor u gekoppelde netwerk interfaces wilt weer geven.

3. Kies **stoppen** in de werk balk van de VM.

4. Wacht totdat de **status** van de VM is gewijzigd in **gestopt (toewijzing opgeheven)**.

5. Kies in de menu balk van de virtuele machine de netwerk interface netwerk **verbinding**  >  **verbreken**.

6. Selecteer in het dialoog venster **netwerk interface ontkoppelen** de netwerk interface die u wilt loskoppelen. Selecteer vervolgens **OK**.

    >[!NOTE]
    >Als er slechts één netwerk interface wordt weer gegeven, kunt u deze niet loskoppelen omdat er altijd ten minste één netwerk interface aan een virtuele machine is gekoppeld.

### <a name="commands"></a>Opdracht

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[AZ VM NIC Remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#az-vm-nic-remove) (naslag); [gedetailleerde stappen](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (verwijzing); [gedetailleerde stappen](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="constraints"></a>Beperkingen

- Aan een virtuele machine moet ten minste één netwerk interface zijn gekoppeld.

- Een VM kan slechts net zoveel netwerk interfaces als de VM-grootte worden gekoppeld. Zie de grootten in azure voor [Linux vm's](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Windows-vm's](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het aantal netwerk interfaces dat elke VM-grootte ondersteunt. Alle grootten ondersteunen ten minste twee netwerk interfaces.

- De netwerk interfaces die u aan een virtuele machine toevoegt, kunnen momenteel niet aan een andere virtuele machine worden gekoppeld. Zie [een netwerk interface maken](virtual-network-network-interface.md#create-a-network-interface)voor meer informatie over het maken van netwerk interfaces.

- In het verleden kon u netwerk interfaces alleen toevoegen aan Vm's die ondersteuning voor meerdere netwerk interfaces hebben en zijn gemaakt met ten minste twee netwerk interfaces. U kunt geen netwerk interface toevoegen aan een virtuele machine die is gemaakt met één netwerk interface, zelfs als de VM-grootte meer dan een netwerk interface ondersteunt. U kunt echter alleen netwerk interfaces van een virtuele machine met ten minste drie netwerk interfaces verwijderen, omdat Vm's die zijn gemaakt met ten minste twee netwerk interfaces, altijd ten minste twee netwerk interfaces hadden. Deze beperkingen zijn niet meer van toepassing. U kunt nu een virtuele machine maken met een wille keurig aantal netwerk interfaces (Maxi maal het aantal dat wordt ondersteund door de VM-grootte).

- De eerste netwerk interface die aan een virtuele machine is gekoppeld, is standaard de *primaire* netwerk interface. Alle andere netwerk interfaces in de VM zijn *secundaire* netwerk interfaces.

- U kunt bepalen met welke netwerk interface u uitgaand verkeer verzendt. Een virtuele machine verzendt standaard echter al het uitgaande verkeer naar het IP-adres dat is toegewezen aan de primaire IP-configuratie van de primaire netwerk interface.

- In het verleden moesten alle virtuele machines in dezelfde beschikbaarheidsset een enkele of meerdere netwerk interfaces hebben. Vm's met een wille keurig aantal netwerk interfaces kunnen nu in dezelfde beschikbaarheidsset bestaan, tot het aantal dat wordt ondersteund door de VM-grootte. U kunt alleen een virtuele machine toevoegen aan een beschikbaarheidsset wanneer deze wordt gemaakt. Zie [de beschik baarheid van vm's in azure beheren](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over beschikbaarheids sets.

- U kunt netwerk interfaces in dezelfde virtuele machine verbinden met verschillende subnetten binnen een virtueel netwerk. De netwerk interfaces moeten echter allemaal zijn verbonden met hetzelfde virtuele netwerk.

- U kunt elk IP-adres voor elke IP-configuratie van een primaire of secundaire netwerk interface toevoegen aan een Azure Load Balancer back-end-pool. In het verleden kon alleen het primaire IP-adres voor de primaire netwerk interface worden toegevoegd aan een back-end-pool. Zie [IP-adressen toevoegen, wijzigen of verwijderen](virtual-network-network-interface-addresses.md)voor meer informatie over IP-adressen en configuraties.

- Als u een virtuele machine verwijdert, worden de gekoppelde netwerk interfaces niet verwijderd. Wanneer u een virtuele machine verwijdert, worden de netwerk interfaces losgekoppeld van de virtuele machine. U kunt deze netwerk interfaces toevoegen aan verschillende Vm's of verwijderen.

- Het bereiken van de optimale prestaties die worden gedocumenteerd, vereist versneld netwerken. In sommige gevallen moet u versneld netwerken expliciet inschakelen voor virtuele [Windows](create-vm-accelerated-networking-powershell.md) -of [Linux](create-vm-accelerated-networking-cli.md) -machines.

## <a name="next-steps"></a>Volgende stappen

Als u een virtuele machine met meerdere netwerk interfaces of IP-adressen wilt maken, raadpleegt u:

|Taak|Hulpprogramma|
|---|---|
|Een virtuele machine met meerdere NIC's maken|[Cli](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Power shell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Eén NIC-VM met meerdere IPv4-adressen maken|[Cli](virtual-network-multiple-ip-addresses-cli.md), [Power shell](virtual-network-multiple-ip-addresses-powershell.md)|
|Een enkele NIC-VM maken met een privé-IPv6-adres (achter een Azure Load Balancer)|[Cli](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [power shell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-sjabloon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|