---
title: De netwerk interface opnieuw instellen voor Azure Windows VM | Microsoft Docs
description: Laat zien hoe u de netwerk interface opnieuw instelt voor Azure Windows VM
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 6c4e507df0f112934979d4e59778b667743cf623
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022901"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Netwerkinterface opnieuw instellen voor virtuele Azure-machine voor Windows 

In dit artikel wordt beschreven hoe u de netwerk interface voor Azure Windows VM opnieuw instelt om problemen op te lossen wanneer u geen verbinding kunt maken met Microsoft Azure virtuele Windows-machine (VM):

* U kunt de standaard netwerk interface (NIC) uitschakelen. 
* U stelt hand matig een statisch IP-adres in voor de NIC. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Netwerk interface opnieuw instellen

### <a name="for-vms-deployed-in-resource-group-model"></a>Voor Vm's die zijn geïmplementeerd in het resource groeps model

1.  Ga naar de [Azure Portal](https://ms.portal.azure.com).
2.  Selecteer de betreffende virtuele machine.
3.  Selecteer **netwerken** en selecteer vervolgens de netwerk interface van de virtuele machine.

    ![Locatie van netwerk interface](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Selecteer **IP-configuraties**.
5.  Selecteer het IP-adres. 
6.  Als de **persoonlijke IP-toewijzing**  niet  **statisch** is, wijzigt u deze in **statisch**.
7.  Wijzig het **IP-adres** in een ander IP-adres dat beschikbaar is in het subnet.
8. De virtuele machine wordt opnieuw opgestart om de nieuwe NIC naar het systeem te initialiseren.
9.  Probeer RDP naar uw computer. Als u dit hebt gedaan, kunt u het privé-IP-adres opnieuw instellen op het origineel als u dat wilt. Als dat niet het geval is, kunt u deze blijven gebruiken. 

#### <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

1. Zorg ervoor dat u [de nieuwste Azure PowerShell](/powershell/azure/) hebt geïnstalleerd
2. Open een verhoogde Azure PowerShell-sessie (als administrator uitvoeren). Voer de volgende opdrachten uit:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ResourceGroupName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Probeer RDP naar uw computer.  Als u dit hebt gedaan, kunt u het privé-IP-adres opnieuw instellen op het origineel als u dat wilt. Als dat niet het geval is, kunt u deze blijven gebruiken.

### <a name="for-classic-vms"></a>Voor klassieke Vm's

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Voer de volgende stappen uit om de netwerk interface opnieuw in te stellen:

#### <a name="use-azure-portal"></a>Azure Portal gebruiken

1.  Ga naar de [Azure Portal]( https://ms.portal.azure.com).
2.  Selecteer **virtual machines (klassiek)**.
3.  Selecteer de betreffende virtuele machine.
4.  Selecteer **IP-adressen**.
5.  Als de **persoonlijke IP-toewijzing**  niet  **statisch** is, wijzigt u deze in **statisch**.
6.  Wijzig het **IP-adres** in een ander IP-adres dat beschikbaar is in het subnet.
7.  Selecteer **Opslaan**.
8.  De virtuele machine wordt opnieuw opgestart om de nieuwe NIC naar het systeem te initialiseren.
9.  Probeer RDP naar uw computer. Als dit lukt, kunt u ervoor kiezen om het privé-IP-adres terug te zetten naar de oorspronkelijke.  

#### <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

1. Zorg ervoor dat u [de nieuwste Azure PowerShell](/powershell/azure/) hebt geïnstalleerd.
2. Open een verhoogde Azure PowerShell-sessie (als administrator uitvoeren). Voer de volgende opdrachten uit:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ResourceGroupName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Probeer RDP naar uw computer. Als u dit hebt gedaan, kunt u het privé-IP-adres opnieuw instellen op het origineel als u dat wilt. Als dat niet het geval is, kunt u deze blijven gebruiken. 

## <a name="delete-the-unavailable-nics"></a>De niet-beschik bare Nic's verwijderen
Nadat u extern bureau blad naar de computer kunt, moet u de oude Nic's verwijderen om het potentiële probleem te voor komen:

1.  Open Apparaatbeheer.
2.  Selecteer **weer** geven  >  **verborgen apparaten** weer geven.
3.  Selecteer **netwerk adapters**. 
4.  Controleer op de adapters met de naam ' Microsoft Hyper-V netwerk adapter '.
5.  Mogelijk ziet u een niet-beschik bare adapter die grijs wordt weer gegeven. Klik met de rechter muisknop op de adapter en selecteer vervolgens verwijderen.

    ![de afbeelding van de NIC](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Verwijder alleen de niet-beschik bare adapters die de naam ' Microsoft Hyper-V-netwerk adapter ' hebben. Als u een van de andere verborgen adapters verwijdert, kan dit leiden tot extra problemen.
    >
    >

6.  Nu moeten alle niet-beschik bare adapters worden gewist van uw systeem.
