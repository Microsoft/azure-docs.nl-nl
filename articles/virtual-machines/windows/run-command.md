---
title: Power shell-scripts uitvoeren in een Windows-VM in azure
description: In dit onderwerp wordt beschreven hoe u Power shell-scripts kunt uitvoeren op een virtuele machine van Azure Windows met behulp van de opdracht functie uitvoeren
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: carmonm
ms.openlocfilehash: 2388d4be0f7d51f9d5897998049b445595648c0a
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763975"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Power shell-scripts uitvoeren in uw Windows-VM met behulp van de opdracht uitvoeren

De functie voor het uitvoeren van opdrachten maakt gebruik van de virtuele machine (VM)-agent voor het uitvoeren van Power shell-scripts in een Azure Windows-VM. U kunt deze scripts gebruiken voor algemeen machine-of toepassings beheer. Ze kunnen u helpen bij het snel diagnosticeren en oplossen van problemen met de toegang tot het netwerk en het herstellen van de VM naar een goede status.



## <a name="benefits"></a>Voordelen

U kunt op verschillende manieren toegang krijgen tot uw virtuele machines. Met de opdracht uitvoeren kunt u op afstand scripts uitvoeren op uw virtuele machines met behulp van de VM-agent. U gebruikt de opdracht uitvoeren via de Azure Portal, [rest API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)of [Power shell](/powershell/module/az.compute/invoke-azvmruncommand) voor Windows-vm's.

Deze mogelijkheid is handig in alle scenario's waarin u een script wilt uitvoeren binnen een virtuele machine. Het is een van de enige manieren om problemen op te lossen en een virtuele machine te herstellen waarvoor geen RDP-of SSH-poort is geopend vanwege een onjuiste netwerk-of administratieve gebruikers configuratie.

## <a name="restrictions"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u de opdracht uitvoeren gebruikt:

* De uitvoer is beperkt tot de laatste 4.096 bytes.
* De minimale tijd voor het uitvoeren van een script is ongeveer 20 seconden.
* Scripts worden uitgevoerd als systeem in Windows.
* Eén script tegelijk kan worden uitgevoerd.
* Scripts die vragen om informatie (interactieve modus) worden niet ondersteund.
* U kunt een actief script niet annuleren.
* De maximale tijd voor het uitvoeren van een script is 90 minuten. Daarna is er een time-out opgestaan.
* Uitgaande verbinding van de virtuele machine is vereist om de resultaten van het script te retour neren.
* Het is niet raadzaam om een script uit te voeren dat de VM-agent stopt te stoppen of bij te werken. Dit kan de uitbrei ding in een overgangs status, tot een time-out leiden.

> [!NOTE]
> Voor een juiste werking moet de opdracht Run (poort 443) zijn vereist voor open bare IP-adressen van Azure. Als de extensie geen toegang tot deze eind punten heeft, kunnen de scripts worden uitgevoerd, maar niet de resultaten retour neren. Als u verkeer op de virtuele machine blokkeert, kunt u [service Tags](../../virtual-network/network-security-groups-overview.md#service-tags) gebruiken om verkeer naar open bare IP-adressen van Azure toe te staan met behulp van de- `AzureCloud` tag.

## <a name="available-commands"></a>Beschik bare opdrachten

In deze tabel ziet u de lijst met opdrachten die beschikbaar zijn voor virtuele Windows-machines. U kunt de opdracht **RunPowerShellScript** gebruiken om elk gewenst aangepast script uit te voeren. Wanneer u de Azure CLI of Power shell gebruikt om een opdracht uit te voeren, moet de waarde die u opgeeft voor de `--command-id` `-CommandId` para meter of een van de volgende vermelde waarden zijn. Wanneer u een waarde opgeeft die geen beschik bare opdracht is, wordt deze fout weer gegeven:

```error
The entity was not found in this Azure location
```

|**Naam**|**Beschrijving**|
|---|---|
|**RunPowerShellScript**|Voert een Power shell-script uit.|
|**EnableRemotePS**|Hiermee configureert u de machine om externe Power shell in te scha kelen.|
|**EnableAdminAccount**|Hiermee wordt gecontroleerd of het lokale Administrator-account is uitgeschakeld en zo ja, als dit het geval is.|
|**IP**| Geeft gedetailleerde informatie weer voor het IP-adres, subnetmasker en de standaard gateway voor elke adapter die is gebonden aan TCP/IP.|
|**RDPSettings**|Controleert register instellingen en domein beleids instellingen. Stelt beleids acties voor als de computer deel uitmaakt van een domein of de instellingen wijzigt in de standaard waarden.|
|**ResetRDPCert**|Hiermee verwijdert u het TLS/SSL-certificaat dat is gekoppeld aan de RDP-listener en herstelt u de standaard beveiliging van RDP-listener. Gebruik dit script als u problemen met het certificaat ziet.|
|**SetRDPPort**|Hiermee stelt u het standaard of door de gebruiker opgegeven poort nummer in voor Extern bureaublad verbindingen. Hiermee schakelt u firewall regels in voor inkomende toegang tot de poort.|

## <a name="azure-cli"></a>Azure CLI

In het volgende voor beeld wordt de opdracht [AZ VM Run-Command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) gebruikt om een shell script uit te voeren op een Azure Windows-VM.

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

Ga naar een virtuele machine in de [Azure Portal](https://portal.azure.com) en selecteer **opdracht uitvoeren** onder **bewerkingen**. U ziet een lijst met de beschik bare opdrachten die op de virtuele machine kunnen worden uitgevoerd.

![Lijst met opdrachten](./media/run-command/run-command-list.png)

Kies een opdracht die moet worden uitgevoerd. Sommige van de opdrachten hebben mogelijk optionele of vereiste invoer parameters. Voor deze opdrachten worden de para meters als tekst velden weer gegeven, zodat u de invoer waarden kunt opgeven. Voor elke opdracht kunt u het script bekijken dat wordt uitgevoerd door een uitgebreid **weergave script** uit te voeren. **RunPowerShellScript** wijkt af van de andere opdrachten, omdat u hiermee uw eigen aangepaste script kunt opgeven.

> [!NOTE]
> De ingebouwde opdrachten kunnen niet worden bewerkt.

Nadat u de opdracht hebt gekozen, selecteert u **uitvoeren** om het script uit te voeren. Nadat het script is voltooid, worden de uitvoer en eventuele fouten in het uitvoer venster geretourneerd. De volgende scherm afbeelding toont een voorbeeld uitvoer van het uitvoeren van de **RDPSettings** -opdracht.

![Uitvoer van opdracht script uitvoeren](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

In het volgende voor beeld wordt de cmdlet [invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) gebruikt om een Power shell-script uit te voeren op een virtuele machine van Azure. De cmdlet verwacht dat het script waarnaar wordt verwezen in de `-ScriptPath` para meter, lokaal is waar de cmdlet wordt uitgevoerd.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Toegang beperken tot de opdracht uitvoeren

Voor het weer geven van de uitvoerings opdrachten of het weer geven van de details van een opdracht is de `Microsoft.Compute/locations/runCommands/read` machtiging vereist voor het abonnements niveau. De ingebouwde rol [lezer](../../role-based-access-control/built-in-roles.md#reader) en hogere niveaus hebben deze machtiging.

Voor het uitvoeren van een opdracht is de `Microsoft.Compute/virtualMachines/runCommand/action` machtiging vereist. De rol [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en hogere niveaus hebben deze machtiging.

U kunt een van de [ingebouwde rollen](../../role-based-access-control/built-in-roles.md) gebruiken of een [aangepaste rol](../../role-based-access-control/custom-roles.md) maken voor het gebruik van de opdracht uitvoeren.

## <a name="next-steps"></a>Volgende stappen

Zie [scripts uitvoeren in uw Windows-VM](run-scripts-in-vm.md)voor meer informatie over andere manieren om scripts en opdrachten op afstand uit te voeren in uw VM.
