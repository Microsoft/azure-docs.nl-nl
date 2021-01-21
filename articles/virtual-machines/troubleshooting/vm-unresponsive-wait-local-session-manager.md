---
title: De VM reageert niet tijdens het wachten op de lokale sessie beheer-service
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen met het gast besturingssysteem wanneer de lokale sessie beheerder de verwerking van een Azure-VM kan volt ooien.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: fc3bd5d2590e969db07e9dffa61b4902ea4604c3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632543"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager-service"></a>De VM reageert niet tijdens het wachten op de lokale sessie beheer-service

In dit artikel worden de stappen beschreven voor het oplossen van problemen waarbij het gast besturingssysteem (gast besturingssysteem) vastloopt op het moment dat de verwerking van een virtuele Azure-machine (VM) door de lokale sessie beheerder wordt beëindigd.

## <a name="symptoms"></a>Symptomen

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om een scherm opname van de uitvoer van de virtuele machine weer te geven, ziet u dat de scherm opname een prompt met het bericht ' wacht op het lokale sessie beheer ' wordt weer gegeven.

![Scherm opname van het vastgelopen gast besturingssysteem in Windows Server 2012 R2, met een wacht tijd voor het lokale sessie beheer bericht.](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>Oorzaak

Er kunnen meerdere redenen zijn om een virtuele machine te wachten op lokale sessie beheer. Als dit probleem zich blijft voordoen, moet u een geheugen dump voor analyse verzamelen.

## <a name="solution"></a>Oplossing

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

In sommige gevallen kan het probleem worden opgelost door te wachten tot het proces is voltooid. Als uw virtuele machine niet reageert en gedurende een uur op het wacht scherm blijft, moet u een geheugen dump verzamelen en contact opnemen met micro soft ondersteuning.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>De besturingssysteem schijf koppelen aan een nieuwe herstel-VM

1. Volg de stappen 1-3 van de [VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)om een herstel-VM voor te bereiden.
1. Maak verbinding met de herstel-VM met behulp van Verbinding met extern bureaublad.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Het dump bestand zoeken en een ondersteunings ticket verzenden

1. Ga op de reparatie-VM naar de map Windows op de gekoppelde besturingssysteem schijf. Als de stationsletter die is toegewezen aan de gekoppelde besturingssysteem schijf, bijvoorbeeld de naam *F* heeft, gaat u naar `F:\Windows` .
1. Zoek naar het bestand *Memory. dmp* en [Verzend een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) met het geheugen dump bestand bijgevoegd.
1. Als u problemen ondervindt bij het vinden van het bestand *Memory. dmp* , volgt u de hand leiding voor het [genereren van een crash dump bestand met behulp van niet-maskeer bare interrupt (NMI)-aanroepen](/windows/client-management/generate-kernel-or-complete-crash-dump).

Zie de gebruikers handleiding voor [NMI-aanroepen in de Azure Serial console](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) voor meer informatie over NMI-aanroepen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Opstart fouten van Azure Virtual Machine oplossen](boot-error-troubleshoot.md)