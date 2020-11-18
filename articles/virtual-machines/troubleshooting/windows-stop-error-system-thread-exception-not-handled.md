---
title: Windows-Stop fout-uitzonde ring in 0x0000007E-systeem thread niet verwerkt
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen waarbij het gast besturingssysteem een probleem aantreft en de Azure-VM wil starten. In het bericht wordt vermeld dat ' een systeem thread-uitzonde ring niet is afgehandeld '.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: 1ce594d9e3ffddf781c61717ae4534f0c7bd40f8
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681887"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows-Stop fout-uitzonde ring in 0x0000007E-systeem thread niet verwerkt

Dit artikel bevat stappen voor het oplossen van problemen waarbij het gast besturingssysteem (gast besturingssysteem) een probleem ondervindt en probeert de virtuele machine (VM) van Azure opnieuw op te starten. Het fout bericht dat wordt weer gegeven, geeft aan dat er geen systeem thread uitzondering is afgehandeld.

## <a name="symptoms"></a>Symptomen

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om een scherm opname van de VM-uitvoer weer te geven, ziet u dat Windows opnieuw moet worden opgestart met de fout Stop code van de ' systeem thread-uitzonde ring of ' 0x0000007E '.

![Scherm opname met Windows vastgelopen tijdens het opstarten, met de fout ' uw PC is in een probleem opgetreden en moet opnieuw worden opgestart. De computer wordt opnieuw opgestart. " fout bericht en de stop code van de ' systeem THREAD uitzondering ' is niet verwerkt.](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Oorzaak

De oorzaak kan niet worden bepaald totdat een geheugen dump bestand is geanalyseerd. Blijf het geheugen dump bestand verzamelen.

## <a name="solution"></a>Oplossing

Als u dit probleem wilt oplossen, moet u eerst het geheugen dump bestand voor de crash verzamelen en vervolgens het bestand naar micro soft ondersteuning verzenden. Volg de instructies in de volgende twee secties om het dump bestand te verzamelen.

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
> [Opstart fouten van Azure Virtual Machine oplossen](./boot-error-troubleshoot.md)
