---
title: Kan geen verbinding maken met RDP met een virtuele Windows-machine in azure | Microsoft Docs
description: Problemen oplossen wanneer u in azure geen verbinding kunt maken met uw virtuele Windows-machine met behulp van Extern bureaublad
keywords: Fout met extern bureau blad, fout met verbinding met extern bureau blad, kan geen verbinding maken met VM, problemen oplossen met extern bureau blad
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: akjosh
ms.openlocfilehash: 66e4ea622f546981d1f0651b20368590e59056d3
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201617"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Problemen met Extern bureaublad verbindingen met een virtuele machine van Azure oplossen
De RDP-verbinding (Remote Desktop Protocol) met uw Windows Azure VM (Virtual Machine) kan om verschillende redenen worden onderbroken, waardoor u geen toegang hebt tot uw VM. Het probleem kan in Extern bureaublad in de VM zitten, in de netwerkverbinding of in de Extern bureaublad-client in de hostcomputer. In dit artikel wordt u door sommige van de meest voorkomende methoden geleid om RDP-verbindingsfouten op te lossen. 

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Snelle probleemoplossings stappen
Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met de virtuele machine:

1. Extern bureaublad configuratie opnieuw instellen.
2. Controleer de regels voor de netwerk beveiligings groep/Cloud Services eind punten.
3. Bekijk de logboeken van de VM-console.
4. Stel de NIC voor de virtuele machine opnieuw in.
5. Controleer de Resource Health van de virtuele machine.
6. Stel uw VM-wacht woord opnieuw in.
7. Start de VM opnieuw op.
8. Implementeer uw VM opnieuw.

Blijf lezen als u gedetailleerde stappen en uitleg nodig hebt. Controleer of lokale netwerk apparatuur, zoals routers en firewalls, de uitgaande TCP-poort 3389 niet blokkeert, zoals vermeld in [gedetailleerde scenario's voor het oplossen van problemen met RDP](detailed-troubleshoot-rdp.md).

> [!TIP]
> Als de knop **verbinden** voor uw virtuele machine grijs wordt weer gegeven in de portal en u niet met Azure bent verbonden via een [snelle route](../../expressroute/expressroute-introduction.md) of een [site-naar-site-VPN-](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) verbinding, moet u een openbaar IP-adres maken en toewijzen aan uw virtuele machine voordat u RDP kunt gebruiken. Er is meer informatie over [openbare IP-adressen in Azure](../../virtual-network/public-ip-addresses.md) beschikbaar.


## <a name="ways-to-troubleshoot-rdp-issues"></a>Manieren om RDP-problemen op te lossen
U kunt virtuele machines die zijn gemaakt met het Resource Manager-implementatie model, met behulp van een van de volgende methoden oplossen:

* Azure Portal-geweldig als u de RDP-configuratie of gebruikers referenties snel opnieuw moet instellen en u de Azure-hulpprogram ma's niet hebt geïnstalleerd.
* Azure PowerShell: als u vertrouwd bent met een Power shell-prompt, stelt u de RDP-configuratie of gebruikers referenties snel opnieuw in met behulp van de Azure PowerShell-cmdlets.

U vindt hier ook stappen voor het oplossen van problemen met Vm's die zijn gemaakt met het [klassieke implementatie model](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Problemen oplossen met behulp van de Azure Portal
Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met uw VM. Als u nog steeds geen verbinding kunt maken, voert u de volgende stap uit.

1. **Stel de RDP-verbinding opnieuw** in. Met deze stap voor het oplossen van problemen wordt de RDP-configuratie opnieuw ingesteld wanneer externe verbindingen worden uitgeschakeld of door Windows Firewall regels RDP worden geblokkeerd.
   
    Selecteer uw virtuele machine in de Azure Portal. Schuif omlaag in het deel venster instellingen naar de sectie **ondersteuning en probleem oplossing** onder in de lijst. Klik op de knop **wacht woord opnieuw instellen** . Stel de **modus** in op **alleen configuratie herstellen** en klik vervolgens op de knop **bijwerken** :
   
    ![Stel de configuratie van de R D P in het Azure Portal opnieuw in.](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Controleer de regels voor de netwerk beveiligings groep**. Gebruik [IP-stroom controleren](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) om te controleren of het verkeer van of naar een virtuele machine wordt geblokkeerd door een regel in een netwerk beveiligings groep. U kunt ook de juiste regels voor beveiligings groepen controleren om ervoor te zorgen dat inkomende NSG regel bestaat en de prioriteit van de RDP-poort (standaard 3389) wordt weer gegeven. Zie [using effectief security rules to Troubleshooting VM Traffic Flow](../../virtual-network/diagnose-network-traffic-filter-problem.md)voor meer informatie.

3. **Diagnostische gegevens over opstarten van de virtuele machine bekijken**. Deze probleemoplossings stap controleert de logboeken van de VM-console om te bepalen of er een probleem is met de virtuele machine. Niet alle virtuele machines hebben diagnostische gegevens over opstarten ingeschakeld. deze stap voor probleem oplossing kan daarom optioneel zijn.
   
    Specifieke stappen voor probleem oplossing bevinden zich buiten het bereik van dit artikel, maar kunnen duiden op een breder probleem dat van invloed is op de RDP-connectiviteit. Zie [Diagnostische gegevens over opstarten voor virtuele machines](boot-diagnostics.md)voor meer informatie over het controleren van de console logboeken en de scherm afbeelding van de VM.

4. **Stel de NIC voor de virtuele machine opnieuw in**. Zie voor meer informatie [NIC opnieuw instellen voor Azure Windows VM](./reset-network-interface.md).
5. **Controleer de resource Health van de virtuele machine**. Deze stap voor probleem oplossing verifieert of er geen bekende problemen zijn met het Azure-platform die van invloed kunnen zijn op de verbinding met de virtuele machine.
   
    Selecteer uw virtuele machine in de Azure Portal. Schuif omlaag in het deel venster instellingen naar de sectie **ondersteuning en probleem oplossing** onder in de lijst. Klik op de knop **resource status** . Een in orde zijnde VM-rapporten die **beschikbaar zijn**:
   
    ![Controleer de resource status van V M in het Azure Portal.](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Gebruikers referenties opnieuw instellen**. Met deze stap voor het oplossen van problemen wordt het wacht woord opnieuw ingesteld op een lokaal beheerders account wanneer u niet zeker weet of u de referenties hebt verg eten.  Wanneer u bent aangemeld bij de virtuele machine, moet u het wacht woord voor die gebruiker opnieuw instellen.
   
    Selecteer uw virtuele machine in de Azure Portal. Schuif omlaag in het deel venster instellingen naar de sectie **ondersteuning en probleem oplossing** onder in de lijst. Klik op de knop **wacht woord opnieuw instellen** . Zorg ervoor dat de **modus** is ingesteld op **wacht woord opnieuw instellen** en voer uw gebruikers naam en een nieuw wacht woord in. Klik ten slotte op de knop **bijwerken** :
   
    ![Stel de gebruikers referenties in de Azure Portal opnieuw in.](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Start de VM opnieuw** op. Deze stap voor het oplossen van problemen kan eventuele onderliggende problemen met de VM zelf corrigeren.
   
    Selecteer uw virtuele machine in de Azure Portal en klik op het tabblad **overzicht** . Klik op de knop **opnieuw opstarten** :
   
    ![Start de V M opnieuw op in de Azure Portal.](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Implementeer uw VM** opnieuw. Met deze stap voor probleem oplossing wordt uw virtuele machine opnieuw geïmplementeerd naar een andere host in azure om eventuele onderliggende problemen met het platform of netwerk op te lossen.
   
    Selecteer uw virtuele machine in de Azure Portal. Schuif omlaag in het deel venster instellingen naar de sectie **ondersteuning en probleem oplossing** onder in de lijst. Klik op de knop opnieuw **implementeren** en klik vervolgens op opnieuw **implementeren**:
   
    ![Implementeer de virtuele machine opnieuw in de Azure Portal](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Nadat deze bewerking is voltooid, gaan tijdelijke schijf gegevens verloren en worden dynamische IP-adressen die zijn gekoppeld aan de virtuele machine, bijgewerkt.

9. **Controleer de route ring**. Gebruik de [volgende hop](../../network-watcher/diagnose-vm-network-routing-problem.md) -mogelijkheid van Network Watcher om te controleren of een route niet voor komt dat verkeer wordt gerouteerd naar of van een virtuele machine. U kunt ook efficiënte routes bekijken om alle efficiënte routes voor een netwerk interface weer te geven. Zie [het gebruik van efficiënte routes voor het oplossen van problemen met de VM-verkeers stroom](../../virtual-network/diagnose-network-routing-problem.md)voor meer informatie.

10. Zorg ervoor dat een on-premises firewall, of firewall op uw computer, uitgaande TCP 3389-verkeer naar Azure toestaat.

Als u nog steeds RDP-problemen ondervindt, kunt u [een ondersteunings aanvraag openen](https://azure.microsoft.com/support/options/) of [meer informatie over het oplossen van problemen met interacties en stappen voor probleem oplossing](detailed-troubleshoot-rdp.md)lezen.

## <a name="troubleshoot-using-azure-powershell"></a>Problemen oplossen met behulp van Azure PowerShell
Als u dat nog niet hebt [gedaan, installeert en configureert u de nieuwste Azure PowerShell](/powershell/azure/).

In de volgende voor beelden worden variabelen `myResourceGroup` , zoals, en, gebruikt `myVM` `myVMAccessExtension` . Vervang deze variabele namen en locaties door uw eigen waarden.

> [!NOTE]
> U kunt de gebruikers referenties en de RDP-configuratie opnieuw instellen met behulp van de [set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) Power shell-cmdlet. In de volgende voor beelden `myVMAccessExtension` is een naam die u opgeeft als onderdeel van het proces. Als u eerder met de extensie vmaccessagent hebt gewerkt, kunt u de naam van de bestaande uitbrei ding ophalen door `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` te gebruiken om de eigenschappen van de virtuele machine te controleren. Als u de naam wilt weer geven, kijkt u in de sectie ' uitbrei dingen ' van de uitvoer.

Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met uw VM. Als u nog steeds geen verbinding kunt maken, voert u de volgende stap uit.

1. **Stel de RDP-verbinding opnieuw** in. Met deze stap voor het oplossen van problemen wordt de RDP-configuratie opnieuw ingesteld wanneer externe verbindingen worden uitgeschakeld of door Windows Firewall regels RDP worden geblokkeerd.
   
    In het volgende voor beeld wordt de RDP-verbinding opnieuw ingesteld op een virtuele machine met de naam op `myVM` de `WestUS` locatie en in de resource groep met de naam `myResourceGroup` :
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Controleer de regels voor de netwerk beveiligings groep**. Deze stap voor het oplossen van problemen controleert of u een regel in uw netwerk beveiligings groep hebt om RDP-verkeer toe te staan. De standaard poort voor RDP is TCP-poort 3389. Een regel voor het toestaan van RDP-verkeer wordt mogelijk niet automatisch gemaakt wanneer u de virtuele machine maakt.
   
    Wijs eerst alle configuratie gegevens voor uw netwerk beveiligings groep toe aan de `$rules` variabele. In het volgende voor beeld wordt informatie opgehaald over de netwerk beveiligings groep `myNetworkSecurityGroup` met de naam in de resource groep met de naam `myResourceGroup` :
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Bekijk nu de regels die zijn geconfigureerd voor deze netwerk beveiligings groep. Controleer als volgt of er een regel bestaat om TCP-poort 3389 voor binnenkomende verbindingen toe te staan:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    In het volgende voor beeld ziet u een geldige beveiligings regel voor het toestaan van RDP-verkeer. U kunt zien `Protocol` , `DestinationPortRange` , en `Access` `Direction` correct zijn geconfigureerd:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Als u geen regel hebt waarmee RDP-verkeer is toegestaan, [maakt u een regel voor de netwerk beveiligings groep](../windows/nsg-quickstart-powershell.md). TCP-poort 3389 toestaan.
3. **Gebruikers referenties opnieuw instellen**. Deze stap voor het oplossen van problemen stelt het wacht woord voor het lokale beheerders account dat u opgeeft wanneer u niet zeker weet of u bent verg eten, de referenties.
   
    Geef eerst de gebruikers naam en het nieuwe wacht woord op door de volgende referenties aan de variabele toe te wijzen `$cred` :
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Werk nu de referenties op uw virtuele machine bij. In het volgende voor beeld worden de referenties bijgewerkt van een virtuele machine `myVM` met de naam op de `WestUS` locatie en in de resource groep met de naam `myResourceGroup` :
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Start de VM opnieuw** op. Deze stap voor het oplossen van problemen kan eventuele onderliggende problemen met de VM zelf corrigeren.
   
    In het volgende voor beeld wordt de virtuele machine `myVM` met de naam in de resource groep met de naam `myResourceGroup` :
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Implementeer uw VM** opnieuw. Met deze stap voor probleem oplossing wordt uw virtuele machine opnieuw geïmplementeerd naar een andere host in azure om eventuele onderliggende problemen met het platform of netwerk op te lossen.
   
    In het volgende voor beeld wordt de virtuele machine `myVM` met de naam op de `WestUS` locatie en in de resource groep met de naam `myResourceGroup` :
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Controleer de route ring**. Gebruik de [volgende hop](../../network-watcher/diagnose-vm-network-routing-problem.md) -mogelijkheid van Network Watcher om te controleren of een route niet voor komt dat verkeer wordt gerouteerd naar of van een virtuele machine. U kunt ook efficiënte routes bekijken om alle efficiënte routes voor een netwerk interface weer te geven. Zie [het gebruik van efficiënte routes voor het oplossen van problemen met de VM-verkeers stroom](../../virtual-network/diagnose-network-routing-problem.md)voor meer informatie.

7. Zorg ervoor dat een on-premises firewall, of firewall op uw computer, uitgaande TCP 3389-verkeer naar Azure toestaat.

Als u nog steeds RDP-problemen ondervindt, kunt u [een ondersteunings aanvraag openen](https://azure.microsoft.com/support/options/) of [meer informatie over het oplossen van problemen met interacties en stappen voor probleem oplossing](detailed-troubleshoot-rdp.md)lezen.

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Problemen met Vm's die zijn gemaakt met het klassieke implementatie model oplossen

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Probeer na elke stap voor het oplossen van problemen opnieuw verbinding te maken met de virtuele machine.

1. **Stel de RDP-verbinding opnieuw** in. Met deze stap voor het oplossen van problemen wordt de RDP-configuratie opnieuw ingesteld wanneer externe verbindingen worden uitgeschakeld of door Windows Firewall regels RDP worden geblokkeerd.
   
    Selecteer uw virtuele machine in de Azure Portal. Klik op de **...** Klik op de knop meer en vervolgens op **externe toegang opnieuw instellen**:
   
    ![De R D P-configuratie opnieuw instellen in de Azure Portal voor V M met het klassieke implementatie model.](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Controleer Cloud Services-eind punten**. Deze stap voor het oplossen van problemen controleert of u eind punten hebt in uw Cloud Services om RDP-verkeer toe te staan. De standaard poort voor RDP is TCP-poort 3389. Een regel voor het toestaan van RDP-verkeer wordt mogelijk niet automatisch gemaakt wanneer u de virtuele machine maakt.
   
   Selecteer uw virtuele machine in de Azure Portal. Klik op de knop met **eind punten** om de eind punten weer te geven die momenteel zijn geconfigureerd voor uw VM. Controleer of er eind punten bestaan die RDP-verkeer op TCP-poort 3389 toestaan.
   
   In het volgende voor beeld ziet u geldige eind punten die RDP-verkeer toestaan:
   
   ![Controleer Cloud Services-eind punten in de Azure Portal voor V M met het klassieke implementatie model.](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Als u geen eind punt hebt waarmee RDP-verkeer is toegestaan, [maakt u een Cloud Services-eind punt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints). TCP naar privé poort 3389 toestaan.
3. **Diagnostische gegevens over opstarten van de virtuele machine bekijken**. Deze probleemoplossings stap controleert de logboeken van de VM-console om te bepalen of er een probleem is met de virtuele machine. Niet alle virtuele machines hebben diagnostische gegevens over opstarten ingeschakeld. deze stap voor probleem oplossing kan daarom optioneel zijn.
   
    Specifieke stappen voor probleem oplossing bevinden zich buiten het bereik van dit artikel, maar kunnen duiden op een breder probleem dat van invloed is op de RDP-connectiviteit. Zie [Diagnostische gegevens over opstarten voor virtuele machines](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)voor meer informatie over het controleren van de console logboeken en de scherm afbeelding van de VM.
4. **Controleer de resource Health van de virtuele machine**. Deze stap voor probleem oplossing verifieert of er geen bekende problemen zijn met het Azure-platform die van invloed kunnen zijn op de verbinding met de virtuele machine.
   
    Selecteer uw virtuele machine in de Azure Portal. Schuif omlaag in het deel venster instellingen naar de sectie **ondersteuning en probleem oplossing** onder in de lijst. Klik op de knop **resource Health** . Een in orde zijnde VM-rapporten die **beschikbaar zijn**:
   
    ![Controleer de resource status V M in de Azure Portal voor V M met het klassieke implementatie model.](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Gebruikers referenties opnieuw instellen**. Met deze stap voor het oplossen van problemen wordt het wacht woord voor het lokale beheerders account dat u opgeeft, opnieuw ingesteld wanneer u niet zeker weet of u de referenties hebt verg eten.  Wanneer u bent aangemeld bij de virtuele machine, moet u het wacht woord voor die gebruiker opnieuw instellen.
   
    Selecteer uw virtuele machine in de Azure Portal. Schuif omlaag in het deel venster instellingen naar de sectie **ondersteuning en probleem oplossing** onder in de lijst. Klik op de knop **wacht woord opnieuw instellen** . Voer uw gebruikers naam en een nieuw wacht woord in. Klik ten slotte op de knop **Opslaan** :
   
    ![Stel de gebruikers referenties in de Azure Portal voor V M opnieuw in met het klassieke implementatie model.](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Start de VM opnieuw** op. Deze stap voor het oplossen van problemen kan eventuele onderliggende problemen met de VM zelf corrigeren.
   
    Selecteer uw virtuele machine in de Azure Portal en klik op het tabblad **overzicht** . Klik op de knop **opnieuw opstarten** :
   
    ![Start de V M opnieuw op in de Azure Portal voor V M met het klassieke implementatie model.](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Zorg ervoor dat een on-premises firewall, of firewall op uw computer, uitgaande TCP 3389-verkeer naar Azure toestaat.

Als u nog steeds RDP-problemen ondervindt, kunt u [een ondersteunings aanvraag openen](https://azure.microsoft.com/support/options/) of [meer informatie over het oplossen van problemen met interacties en stappen voor probleem oplossing](detailed-troubleshoot-rdp.md)lezen.

## <a name="troubleshoot-specific-rdp-errors"></a>Specifieke RDP-fouten oplossen
Er kan een specifiek fout bericht worden weer gegeven wanneer u probeert verbinding te maken met uw virtuele machine via RDP. Hier volgen de meest voorkomende fout berichten:

* [De externe sessie is beëindigd omdat er geen extern bureaublad licentie servers beschikbaar zijn om een licentie te bieden](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Extern bureaublad kan de computer naam niet vinden](troubleshoot-specific-rdp-errors.md#rdpname).
* [Er is een verificatie fout opgetreden. Kan geen verbinding maken met de lokale beveiligings autoriteit](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Windows-beveiligings fout: uw referenties werken niet](troubleshoot-specific-rdp-errors.md#wincred).
* [Deze computer kan geen verbinding maken met de externe computer](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Aanvullende resources
Lees de gedetailleerde [hand leiding voor het oplossen extern bureaublad van problemen](detailed-troubleshoot-rdp.md)als er geen van deze fouten optrad en u nog steeds geen verbinding met de virtuele machine kunt maken via Extern bureaublad.
* Zie [problemen oplossen met toegang tot een toepassing die wordt uitgevoerd op](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)een virtuele machine van Azure voor informatie over het oplossen van problemen bij het openen van toepassingen die op een VM worden uitgevoerd.
* Zie [problemen met ssh-verbindingen met een virtuele Linux-machine in azure oplossen](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)als u problemen ondervindt met het gebruik van Secure Shell (SSH) om verbinding te maken met een virtuele Linux-machine in Azure.
