---
title: PowerShell-scripts uitvoeren in een Windows-VM in Azure
description: In dit onderwerp wordt beschreven hoe u PowerShell-scripts in een virtuele Azure Windows-machine kunt uitvoeren met behulp van Uitvoeropdracht functie
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: carmonm
ms.openlocfilehash: 3271f5461447439772b656b8927a54057c8b0c7e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786401"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>PowerShell-scripts uitvoeren in uw Windows-VM met behulp van Uitvoeropdracht

De Uitvoeropdracht gebruikt de VM-agent (virtuele machine) om PowerShell-scripts uit te voeren binnen een Azure Windows-VM. U kunt deze scripts gebruiken voor algemeen machine- of toepassingsbeheer. Ze kunnen u helpen om snel problemen met de VM-toegang en het netwerk op te sporen en op te lossen en de VM weer in een goede staat te krijgen.



## <a name="benefits"></a>Voordelen

U hebt op meerdere manieren toegang tot uw virtuele machines. Uitvoeropdracht kunt scripts op uw virtuele machines op afstand uitvoeren met behulp van de VM-agent. U gebruikt Uitvoeropdracht de Azure Portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)of [PowerShell voor](/powershell/module/az.compute/invoke-azvmruncommand) Windows-VM's.

Deze mogelijkheid is handig in alle scenario's waarin u een script wilt uitvoeren binnen een virtuele machine. Dit is een van de enige manieren om problemen op te lossen en een virtuele machine te herstellen waarop de RDP- of SSH-poort niet is geopend vanwege een onjuiste netwerk- of beheergebruikersconfiguratie.

## <a name="restrictions"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u Uitvoeropdracht:

* De uitvoer is beperkt tot de laatste 4096 bytes.
* De minimale tijd voor het uitvoeren van een script is ongeveer 20 seconden.
* Scripts worden uitgevoerd als systeem in Windows.
* Er kan één script tegelijk worden uitgevoerd.
* Scripts die om informatie vragen (interactieve modus) worden niet ondersteund.
* U kunt een actief script niet annuleren.
* De maximale tijd die een script kan worden uitgevoerd, is 90 minuten. Daarna t mogelijk een time-out.
* Uitgaande connectiviteit van de VM is vereist om de resultaten van het script te retourneren.
* Het wordt afgeraden om een script uit te voeren dat een stop- of update van de VM-agent veroorzaakt. Hierdoor kan de extensie een overgangstoestand krijgen, wat kan leiden tot een time-out.

> [!NOTE]
> Als u goed wilt werken, Uitvoeropdracht verbinding (poort 443) met openbare IP-adressen van Azure vereist. Als de extensie geen toegang heeft tot deze eindpunten, kunnen de scripts worden uitgevoerd, maar worden de resultaten niet retourneert. Als u verkeer op de virtuele machine blokkeert, kunt u [servicetags](../../virtual-network/network-security-groups-overview.md#service-tags) gebruiken om verkeer naar openbare IP-adressen van Azure toe te staan met behulp van de `AzureCloud` tag .

## <a name="available-commands"></a>Beschikbare opdrachten

In deze tabel ziet u de lijst met opdrachten die beschikbaar zijn voor Virtuele Windows-VM's. U kunt de opdracht **RunPowerShellScript gebruiken om** een aangepast script uit te voeren dat u wilt. Wanneer u de Azure CLI of PowerShell gebruikt om een opdracht uit te voeren, moet de waarde die u opgeeft voor de parameter of een van de volgende `--command-id` `-CommandId` vermelde waarden zijn. Wanneer u een waarde opgeeft die geen beschikbare opdracht is, ontvangt u deze fout:

```error
The entity was not found in this Azure location
```

|**Naam**|**Beschrijving**|
|---|---|
|**RunPowerShellScript**|Voert een PowerShell-script uit.|
|**EnableRemotePS**|Hiermee configureert u de computer voor het inschakelen van externe PowerShell.|
|**EnableAdminAccount**|Controleert of het lokale beheerdersaccount is uitgeschakeld en als dit het doet.|
|**Ipconfig**| Geeft gedetailleerde informatie weer voor het IP-adres, het subnetmasker en de standaardgateway voor elke adapter die is gebonden aan TCP/IP.|
|**RDPSettings**|Controleert registerinstellingen en domeinbeleidsinstellingen. Stelt beleidsacties voor als de machine deel uitmaakt van een domein of de instellingen wijzigt in standaardwaarden.|
|**RESETRDPCert**|Hiermee verwijdert u het TLS/SSL-certificaat dat is gekoppeld aan de RDP-listener en herstelt u de beveiliging van de RDP-listener naar de standaardinstelling. Gebruik dit script als u problemen met het certificaat ziet.|
|**SetRDPPort**|Hiermee stelt u het standaardpoortnummer of door de gebruiker opgegeven poortnummer in Extern bureaublad verbindingen. Hiermee schakelt u firewallregels in voor binnenkomende toegang tot de poort.|

## <a name="azure-cli"></a>Azure CLI

In het volgende voorbeeld wordt de [opdracht az vm run-command](/cli/azure/vm/run-command#az_vm_run_command_invoke) gebruikt om een shellscript uit te voeren op een Azure Windows-VM.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Azure Portal

Ga naar een VM in [het Azure Portal](https://portal.azure.com) selecteer **Opdracht uitvoeren onder** **OPERATIONS**. U ziet een lijst met de beschikbare opdrachten die op de VM moeten worden uitgevoerd.

![Lijst met opdrachten](./media/run-command/run-command-list.png)

Kies een uit te voeren opdracht. Sommige opdrachten kunnen optionele of vereiste invoerparameters hebben. Voor deze opdrachten worden de parameters weergegeven als tekstvelden, waar u de invoerwaarden kunt opgeven. Voor elke opdracht kunt u het script weergeven dat wordt uitgevoerd door **Script weergeven uit te breiden.** **RunPowerShellScript** verschilt van de andere opdrachten, omdat u hiermee uw eigen aangepaste script kunt leveren.

> [!NOTE]
> De ingebouwde opdrachten kunnen niet worden bewerkt.

Nadat u de opdracht hebt gekozen, selecteert **u Uitvoeren om** het script uit te voeren. Nadat het script is uitgevoerd, worden de uitvoer en eventuele fouten in het uitvoervenster weergegeven. In de volgende schermopname ziet u een voorbeeld van de uitvoer van het uitvoeren **van de opdracht RDPSettings.**

![Uitvoer van opdrachtscript uitvoeren](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

In het volgende voorbeeld wordt de cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) gebruikt om een PowerShell-script uit te voeren op een Azure-VM. De cmdlet verwacht dat het script waarnaar wordt verwezen in de parameter lokaal is op de plaats waar de `-ScriptPath` cmdlet wordt uitgevoerd.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Toegang tot Opdracht uitvoeren beperken

Voor het weergeven van de run-opdrachten of het weergeven van de details van een opdracht is de `Microsoft.Compute/locations/runCommands/read` machtiging abonnementsniveau vereist. De ingebouwde rol [Lezer](../../role-based-access-control/built-in-roles.md#reader) en hogere niveaus hebben deze machtiging.

Voor het uitvoeren van een opdracht is de machtiging `Microsoft.Compute/virtualMachines/runCommand/action` vereist. De [rol Inzender voor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) virtuele machines en hogere niveaus hebben deze machtiging.

U kunt een van de [ingebouwde rollen gebruiken](../../role-based-access-control/built-in-roles.md) of een aangepaste rol [maken om](../../role-based-access-control/custom-roles.md) deze Uitvoeropdracht.

## <a name="next-steps"></a>Volgende stappen

Zie Scripts uitvoeren op uw [Windows-VM](run-scripts-in-vm.md)voor meer informatie over andere manieren om scripts en opdrachten op afstand uit te voeren in uw VM.
