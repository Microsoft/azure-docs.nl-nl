---
title: Shell scripts uitvoeren in een virtuele Linux-machine in azure
description: In dit onderwerp wordt beschreven hoe u scripts kunt uitvoeren op een virtuele Azure Linux-machine met behulp van de opdracht functie uitvoeren
services: automation
ms.service: virtual-machines
ms.collection: linux
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: 73dd15a5eed3e27d9b72bc0357e35901c04ba7a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552929"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Shell scripts uitvoeren in uw virtuele Linux-machine met behulp van de opdracht uitvoeren

De functie Opdracht uitvoeren maakt gebruik van de VM-agent (virtuele machine) om shellscripts uit te voeren binnen een Azure Linux-VM. U kunt deze scripts gebruiken voor algemeen machine- of toepassingsbeheer. Ze kunnen u helpen bij het snel diagnosticeren en oplossen van problemen met de toegang tot het netwerk en het herstellen van de VM naar een goede status.

## <a name="benefits"></a>Voordelen

U kunt op verschillende manieren toegang krijgen tot uw virtuele machines. Met de opdracht uitvoeren kunt u op afstand scripts uitvoeren op uw virtuele machines met behulp van de VM-agent. U gebruikt de opdracht uitvoeren via de Azure Portal, [rest API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)of [Azure cli](/cli/azure/vm/run-command#az_vm_run_command_invoke) voor Linux vm's.

Deze mogelijkheid is handig in alle scenario's waarin u een script wilt uitvoeren binnen een virtuele machine. Het is een van de enige manieren om problemen op te lossen en een virtuele machine te herstellen waarvoor geen RDP-of SSH-poort is geopend vanwege een onjuiste netwerk-of administratieve gebruikers configuratie.

## <a name="restrictions"></a>Beperkingen

De volgende beperkingen zijn van toepassing wanneer u de opdracht uitvoeren gebruikt:

* De uitvoer is beperkt tot de laatste 4.096 bytes.
* De minimale tijd voor het uitvoeren van een script is ongeveer 20 seconden.
* Scripts worden standaard uitgevoerd als een verhoogde gebruiker op Linux.
* U kunt één script per keer uitvoeren.
* Scripts die vragen om informatie (interactieve modus) worden niet ondersteund.
* U kunt een actief script niet annuleren.
* De maximale tijd voor het uitvoeren van een script is 90 minuten. Daarna loopt het script een time-out.
* Uitgaande verbinding van de virtuele machine is vereist om de resultaten van het script te retour neren.

> [!NOTE]
> Voor een juiste werking moet de opdracht Run (poort 443) zijn vereist voor open bare IP-adressen van Azure. Als de extensie geen toegang tot deze eind punten heeft, kunnen de scripts worden uitgevoerd, maar niet de resultaten retour neren. Als u verkeer op de virtuele machine blokkeert, kunt u [service Tags](../../virtual-network/network-security-groups-overview.md#service-tags) gebruiken om verkeer naar open bare IP-adressen van Azure toe te staan met behulp van de- `AzureCloud` tag.

## <a name="available-commands"></a>Beschikbare opdrachten

In deze tabel ziet u de lijst met opdrachten die beschikbaar zijn voor Linux-Vm's. U kunt de opdracht **RunShellScript** gebruiken om elk gewenst aangepast script uit te voeren. Wanneer u de Azure CLI of Power shell gebruikt om een opdracht uit te voeren, moet de waarde die u opgeeft voor de `--command-id` `-CommandId` para meter of een van de volgende vermelde waarden zijn. Wanneer u een waarde opgeeft die geen beschik bare opdracht is, wordt deze fout weer gegeven:

```error
The entity was not found in this Azure location
```

|**Naam**|**Beschrijving**|
|---|---|
|**RunShellScript**|Voert een Linux-shell script uit.|
|**ifconfig**| Hiermee wordt de configuratie van alle netwerk interfaces opgehaald.|

## <a name="azure-cli"></a>Azure CLI

In het volgende voor beeld wordt de opdracht [AZ VM Run-Command](/cli/azure/vm/run-command#az_vm_run_command_invoke) gebruikt om een shell script uit te voeren op een virtuele Azure Linux-machine.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "apt-get update && apt-get install -y nginx"
```

> [!NOTE]
> Om opdrachten uit te voeren als een andere gebruiker, voert `sudo -u` u in om een gebruikers account op te geven.

## <a name="azure-portal"></a>Azure Portal

Ga naar een virtuele machine in de [Azure Portal](https://portal.azure.com) en selecteer **opdracht uitvoeren** onder **bewerkingen**. U ziet een lijst met de beschik bare opdrachten die op de virtuele machine kunnen worden uitgevoerd.

![Lijst met opdrachten](./media/run-command/run-command-list.png)

Kies een opdracht die moet worden uitgevoerd. Sommige van de opdrachten hebben mogelijk optionele of vereiste invoer parameters. Voor deze opdrachten worden de para meters als tekst velden weer gegeven, zodat u de invoer waarden kunt opgeven. Voor elke opdracht kunt u het script bekijken dat wordt uitgevoerd door een uitgebreid **weergave script** uit te voeren. **RunShellScript** wijkt af van de andere opdrachten, omdat u hiermee uw eigen aangepaste script kunt opgeven.

> [!NOTE]
> De ingebouwde opdrachten kunnen niet worden bewerkt.

Nadat u de opdracht hebt gekozen, selecteert u **uitvoeren** om het script uit te voeren. Nadat het script is voltooid, worden de uitvoer en eventuele fouten in het uitvoer venster geretourneerd. De volgende scherm afbeelding toont een voorbeeld uitvoer van het uitvoeren van de **ifconfig** -opdracht.

![Uitvoer van opdracht script uitvoeren](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

In het volgende voor beeld wordt de cmdlet [invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) gebruikt om een Power shell-script uit te voeren op een virtuele machine van Azure. De cmdlet verwacht dat het script waarnaar wordt verwezen in de `-ScriptPath` para meter, lokaal is waar de cmdlet wordt uitgevoerd.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Toegang tot Opdracht uitvoeren beperken

Voor het weer geven van de uitvoerings opdrachten of het weer geven van de details van een opdracht is de `Microsoft.Compute/locations/runCommands/read` machtiging vereist. De ingebouwde rol [lezer](../../role-based-access-control/built-in-roles.md#reader) en hogere niveaus hebben deze machtiging.

Voor het uitvoeren van een opdracht is de `Microsoft.Compute/virtualMachines/runCommand/action` machtiging vereist. De rol [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en hogere niveaus hebben deze machtiging.

U kunt een van de [ingebouwde rollen](../../role-based-access-control/built-in-roles.md) gebruiken of een [aangepaste rol](../../role-based-access-control/custom-roles.md) maken voor het gebruik van de opdracht uitvoeren.

## <a name="next-steps"></a>Volgende stappen

Zie [scripts uitvoeren in uw virtuele Linux-machine](run-scripts-in-vm.md)voor meer informatie over andere manieren om scripts en opdrachten op afstand uit te voeren in uw VM.
