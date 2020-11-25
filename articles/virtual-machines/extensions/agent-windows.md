---
title: 'Azure Virtual Machine-agent: een overzicht'
description: 'Azure Virtual Machine-agent: een overzicht'
services: virtual-machines-windows
ms.subservice: extensions
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 07/20/2019
ms.author: mimckitt
ms.openlocfilehash: 3724b8a2afb89594c73f7dae782658ec8978963a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016468"
---
# <a name="azure-virtual-machine-agent-overview"></a>Overzicht van de agent voor virtuele Azure-machines
De Microsoft Azure-agent van de virtuele machine (VM-agent) is een veilig, licht gewicht proces dat interactie van de virtuele machine (VM) beheert met de Azure Fabric-controller. De VM-agent heeft een primaire rol bij het inschakelen en uitvoeren van extensies van virtuele Azure-machines. VM-extensies maken de configuratie van de na de implementatie van de VM mogelijk, zoals het installeren en configureren van software. VM-extensies bieden ook herstel functies, zoals het opnieuw instellen van het beheerders wachtwoord van een virtuele machine. Zonder de VM-agent van Azure kunnen VM-extensies niet worden uitgevoerd.

In dit artikel vindt u informatie over de installatie en detectie van de agent van de virtuele machine van Azure.

## <a name="install-the-vm-agent"></a>De VM-agent installeren

### <a name="azure-marketplace-image"></a>Azure Marketplace-installatie kopie

De Azure VM-agent wordt standaard geïnstalleerd op een virtuele Windows-machine die is geïmplementeerd vanuit een Azure Marketplace-installatie kopie. Wanneer u een installatie kopie van Azure Marketplace implementeert vanuit de portal, Power shell, de opdracht regel interface of een Azure Resource Manager sjabloon, wordt de Azure VM-agent ook geïnstalleerd.

Het pakket voor de Windows-gast agent is onderverdeeld in twee delen:

- Inrichtings agent (PA)
- Windows-gast agent (Vleugela)

Als u een virtuele machine wilt opstarten, moet u de PA geïnstalleerd hebben op de virtuele machine. de vleugels hoeven echter niet te worden geïnstalleerd. Op de implementatie tijd van de VM kunt u de Vleugela niet installeren. In het volgende voor beeld ziet u hoe u de optie *provisionVmAgent* selecteert met een Azure Resource Manager sjabloon:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Als u de agents niet hebt geïnstalleerd, kunt u bepaalde Azure-Services, zoals Azure Backup of Azure-beveiliging, niet gebruiken. Deze services vereisen een uitbrei ding die moet worden geïnstalleerd. Als u een virtuele machine zonder Vleugela hebt geïmplementeerd, kunt u later de meest recente versie van de agent installeren.

### <a name="manual-installation"></a>Handmatige installatie
De Windows VM-agent kan hand matig worden geïnstalleerd met een Windows Installer-pakket. Hand matige installatie kan nodig zijn wanneer u een aangepaste VM-installatie kopie maakt die is geïmplementeerd in Azure. Als u de Windows VM-agent hand matig wilt installeren, [downloadt u het installatie programma van de VM-agent](https://go.microsoft.com/fwlink/?LinkID=394789). De VM-agent wordt ondersteund op Windows Server 2008 (64 bits) en hoger.

> [!NOTE]
> Het is belang rijk dat u de AllowExtensionOperations-optie bijwerkt nadat u de VMAgent hand matig hebt geïnstalleerd op een virtuele machine die is geïmplementeerd vanuit de installatie kopie zonder ProvisionVMAgent in te scha kelen.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Vereisten

- De Windows VM-agent moet ten minste Windows Server 2008 SP2 (64-bits) uitvoeren, met de .NET Framework 4,0. Bekijk de [minimale versie ondersteuning voor virtuele-machine agenten in azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

- Zorg ervoor dat uw virtuele machine toegang heeft tot het IP-adres 168.63.129.16. Zie [Wat is IP-adres 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)? voor meer informatie.

- Zorg ervoor dat DHCP is ingeschakeld in de gast-VM. Dit is vereist om de host of het infrastructuur adres van DHCP op te halen voor de IaaS-VM-agent en-extensies. Als u een statisch privé IP-adres nodig hebt, moet u dit configureren via de Azure Portal of Power shell en ervoor zorgen dat de DHCP-optie in de virtuele machine is ingeschakeld. Meer [informatie](../../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) over het instellen van een statisch IP-adres met Power shell.


## <a name="detect-the-vm-agent"></a>De VM-agent detecteren

### <a name="powershell"></a>PowerShell

De Azure Resource Manager Power shell-module kan worden gebruikt om informatie over virtuele Azure-machines op te halen. Als u informatie wilt weer geven over een virtuele machine, zoals de inrichtings status van de Azure VM-agent, gebruikt u [Get-AzVM](/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

In de volgende gecomprimeerde voorbeeld uitvoer ziet u de eigenschap *ProvisionVMAgent* geneste in `OSProfile` . Deze eigenschap kan worden gebruikt om te bepalen of de VM-agent is geïmplementeerd op de VM:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Het volgende script kan worden gebruikt om een beknopt overzicht van de VM-namen en de status van de VM-agent te retour neren:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Hand matige detectie

Wanneer u bent aangemeld bij een Windows-VM, kan taak beheer worden gebruikt voor het onderzoeken van actieve processen. Als u wilt controleren of de Azure VM-agent is, opent u taak beheer, klikt u op het tabblad *Details* en zoekt u naar een proces naam **WindowsAzureGuestAgent.exe**. De aanwezigheid van dit proces geeft aan dat de VM-agent is geïnstalleerd.


## <a name="upgrade-the-vm-agent"></a>De VM-agent bijwerken
De Azure VM-agent voor Windows wordt automatisch bijgewerkt op installatie kopieën die zijn geïmplementeerd vanuit Azure Marketplace. Wanneer er nieuwe virtuele machines worden geïmplementeerd in azure, ontvangen ze de nieuwste VM-agent op de VM-inrichtings tijd. Als u de agent hand matig hebt geïnstalleerd of aangepaste VM-installatie kopieën implementeert, moet u deze hand matig bijwerken om de nieuwe VM-agent op te nemen bij het maken van de installatie kopie.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Verzameling automatische logboeken voor Windows-gast agent
Windows Guest agent heeft een functie voor het automatisch verzamelen van Logboeken. Deze functie is controller door het CollectGuestLogs.exe proces. Deze bestaat zowel voor PaaS Cloud Services als IaaS Virtual Machines en het doel is om & snel een aantal Diagnostische logboeken te verzamelen van een VM, zodat deze kunnen worden gebruikt voor offline analyse. De verzamelde logboeken zijn gebeurtenis logboeken, logboeken van het besturings systeem, Azure-logboeken en bepaalde register sleutels. Er wordt een ZIP-bestand gegenereerd dat wordt overgedragen naar de host van de virtuele machine. Dit ZIP-bestand kan vervolgens worden bekeken door technische teams en ondersteunings medewerkers om problemen te onderzoeken op verzoek van de klant die eigenaar is van de virtuele machine.

## <a name="guest-agent-and-osprofile-certificates"></a>Gast agent en OSProfile-certificaten
De Azure VM-agent is verantwoordelijk voor het installeren van de certificaten waarnaar wordt verwezen in de `OSProfile` van een VM of virtuele-machine schaalset. Als u deze certificaten hand matig verwijdert uit de MMC-console Certificaten in de gast-VM, wordt verwacht dat de gast agent ze opnieuw gaat toevoegen.
Als u een certificaat permanent wilt verwijderen, moet u het verwijderen uit de `OSProfile` en het vervolgens verwijderen uit het gast besturingssysteem.

Gebruik voor een virtuele machine de [Remove-AzVMSecret]() om certificaten te verwijderen uit de `OSProfile` .

Zie [Virtual Machine Scale sets-hoe kan ik afgeschafte certificaten verwijderen](../../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-remove-deprecated-certificates) voor meer informatie over certificaten voor virtuele-machine schaal sets?


## <a name="next-steps"></a>Volgende stappen
Zie [overzicht van virtuele machines en functies van Azure](overview.md)voor meer informatie over VM-uitbrei dingen.