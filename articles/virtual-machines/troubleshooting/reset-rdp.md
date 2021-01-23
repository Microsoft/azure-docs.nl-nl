---
title: Extern bureaublad-services of het beheerders wachtwoord opnieuw instellen in een Windows-VM | Microsoft Docs
description: Meer informatie over het opnieuw instellen van een account wachtwoord of Extern bureaublad-services op een Windows-VM met behulp van de Azure Portal of Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 720d25079e1350315c9f403a8215f650db49ceb7
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743080"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Extern bureaublad-services of het beheerders wachtwoord opnieuw instellen in een Windows-VM
Als u geen verbinding kunt maken met een virtuele Windows-machine (VM), kunt u het lokale beheerders wachtwoord opnieuw instellen of de Extern bureaublad-services configuratie opnieuw instellen (niet ondersteund op Windows-domein controllers). Voor het opnieuw instellen van het wachtwoord gebruikt u de Azure-portal of de VM-toegangsextensie in Azure PowerShell. Wanneer u zich bij de VM hebt aangemeld, stelt u het wachtwoord van die lokale beheerder opnieuw in.  
Als u Power shell gebruikt, moet u ervoor zorgen dat de [meest recente Power shell-module is geïnstalleerd en geconfigureerd](/powershell/azure/) en dat u bent aangemeld bij uw Azure-abonnement. U kunt [deze stappen ook uitvoeren voor virtuele machines die zijn gemaakt met het klassieke implementatiemodel](/previous-versions/azure/virtual-machines/windows/classic/reset-rdp).

U kunt Extern bureaublad-services en de referenties op de volgende manieren opnieuw instellen:

- [Opnieuw instellen met behulp van de Azure Portal](#reset-by-using-the-azure-portal)

- [Opnieuw instellen met behulp van de VMAccess-extensie en Power shell](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Opnieuw instellen met behulp van de Azure Portal

Meld u eerst aan bij de [Azure Portal](https://portal.azure.com) en selecteer vervolgens **virtuele machines** in het menu links. 

### <a name="reset-the-local-administrator-account-password"></a>**Het wacht woord voor het lokale Administrator-account opnieuw instellen**

1. Selecteer uw Windows-VM en selecteer vervolgens **wacht woord opnieuw instellen** onder **ondersteuning en probleem oplossing**. Het venster **wacht woord opnieuw instellen** wordt weer gegeven.

2. Selecteer **wacht woord opnieuw instellen**, voer een gebruikers naam en wacht woord in en selecteer vervolgens **bijwerken**. 

3. Probeer opnieuw verbinding te maken met uw VM.

### <a name="reset-the-remote-desktop-services-configuration"></a>**De configuratie van de Extern bureaublad-services opnieuw instellen**

Met dit proces wordt Extern bureaublad-service in de VM ingeschakeld en wordt een firewall regel gemaakt voor de standaard RDP-poort 3389.

1. Selecteer uw Windows-VM en selecteer vervolgens **wacht woord opnieuw instellen** onder **ondersteuning en probleem oplossing**. Het venster **wacht woord opnieuw instellen** wordt weer gegeven. 

2. Selecteer **alleen configuratie opnieuw instellen** en selecteer vervolgens **bijwerken**. 

3. Probeer opnieuw verbinding te maken met uw VM.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Opnieuw instellen met behulp van de VMAccess-extensie en Power shell

Controleer eerst of de [meest recente Power shell-module is geïnstalleerd en geconfigureerd](/powershell/azure/) en of u bent aangemeld bij uw Azure-abonnement met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

### <a name="reset-the-local-administrator-account-password"></a>**Het wacht woord voor het lokale Administrator-account opnieuw instellen**

- Stel het beheerders wachtwoord of de gebruikers naam opnieuw in met de Power shell [-cmdlet Set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) . De `typeHandlerVersion` instelling moet 2,0 of hoger zijn, omdat versie 1 is afgeschaft. 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > Als u een andere naam opgeeft dan het huidige lokale beheerders account op uw virtuele machine, wordt met de uitbrei ding VMAccess een lokaal beheerders account met die naam toegevoegd en wordt het opgegeven wacht woord aan dat account toegewezen. Als het lokale beheerders account op uw virtuele machine bestaat, wordt het wacht woord opnieuw ingesteld door de VMAccess-extensie. Als het account is uitgeschakeld, wordt het door de VMAccess-extensie ingeschakeld.

### <a name="reset-the-remote-desktop-services-configuration"></a>**De configuratie van de Extern bureaublad-services opnieuw instellen**

1. Externe toegang tot uw virtuele machine opnieuw instellen met de Power shell [-cmdlet Set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) . In het volgende voor beeld wordt de toegangs extensie opnieuw ingesteld `myVMAccess` op de naam van de virtuele machine `myVM` in de `myResourceGroup` resource groep:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > Een virtuele machine kan op elk gewenst moment slechts één VM-toegangs agent hebben. Gebruik de optie om de eigenschappen van de VM-toegangs agent in te stellen `-ForceRerun` . Wanneer u gebruikt `-ForceRerun` , moet u ervoor zorgen dat u dezelfde naam gebruikt als voor de VM-toegangs agent die u mogelijk in een van de vorige opdrachten hebt gebruikt.

1. Als u nog steeds geen externe verbinding met uw virtuele machine kunt maken, raadpleegt u [problemen met extern bureaublad verbindingen met een virtuele Windows-machine voor Azure oplossen](troubleshoot-rdp-connection.md). Als u de verbinding met de Windows-domein controller kwijtraakt, moet u deze herstellen vanaf een back-up van een domein controller.

## <a name="next-steps"></a>Volgende stappen


- Als de installatie van de Azure VM-toegangs extensie mislukt, kunt u problemen [met VM-extensies oplossen](../extensions/troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- Als u het wacht woord niet opnieuw kunt instellen met behulp van de extensie voor toegang tot de VM, moet u [het lokale Windows-wacht woord offline opnieuw instellen](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Deze methode is Geavanceerd en vereist dat u de virtuele harde schijf van de problematische VM verbindt met een andere virtuele machine. Volg de stappen die in dit artikel worden beschreven eerst en probeer de methode voor offline wachtwoord herstel alleen uit te voeren als deze stappen niet werken.

- [Meer informatie over Azure VM-extensies en-functies](../extensions/features-windows.md).

- [Verbinding maken met een virtuele machine van Azure met RDP of SSH](/previous-versions/azure/dn535788(v=azure.100)).


- [Problemen met extern bureaublad verbindingen met een virtuele Azure-machine op basis van Windows oplossen](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
