---
title: Windows-Stop fout status systeem proces beëindigd
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij het besturings systeem de Stop-fout 0xC000021A tegen komt, waarmee wordt voor komen dat een virtuele machine van Azure wordt opgestart.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: 8af656e00c457dfa3d438f0cb104d85ccc687745
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661235"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Windows-Stop fout: 0xC000021A status systeem proces beëindigd

Dit artikel bevat stappen voor het oplossen van problemen waarbij het besturings systeem (OS) de Stop-fout 0xC000021A tegen komt, waardoor een Azure virtual machine (VM) niet kan worden opgestart.

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, wordt in de scherm opname het bericht weer gegeven dat het besturings systeem een fout heeft aangetroffen tijdens het opstarten, met het volgende bericht:

**Er is een probleem opgetreden op de PC en opnieuw moet worden opgestart. Er worden alleen fout gegevens verzameld en vervolgens kunt u de computer opnieuw opstarten. (# #% voltooid) Als u meer wilt weten, kunt u later online zoeken naar deze fout: 0xC000021a**.

  ![In afbeelding 1 wordt de fout code #0xC000021A weer gegeven met het bericht ' er is een probleem opgetreden in de PC en de computer moet opnieuw worden opgestart. Er worden alleen fout gegevens verzameld en vervolgens kunt u de computer opnieuw opstarten.](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>Oorzaak

Fout 0xC000021A betekent dat **STATUS_SYSTEM_PROCESS_TERMINATED**.

Deze fout treedt op wanneer een kritiek proces, zoals WinLogon (winlogon.exe) of het Run-Time subsysteem (csrss.exe) van de client server, niet kan worden uitgevoerd. Zodra de kernel detecteert dat een van deze services is gestopt, wordt de fout **Stop 0xc000021a** gegenereerd. Deze fout kan verschillende oorzaken hebben, zoals:

- Er zijn niet-overeenkomende systeem bestanden geïnstalleerd.
- Een installatie van een service pack of KB-update is mislukt.
- Bij een back-upprogramma dat wordt gebruikt voor het herstellen van een harde schijf, zijn de bestanden die in gebruik waren mogelijk niet goed teruggezet.
- Er is een incompatibel programma van derden geïnstalleerd.

## <a name="solution"></a>Oplossing

### <a name="collect-the-memory-dump-file"></a>Het geheugen dump bestand verzamelen

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.


Om dit probleem op te lossen moet de crash dump worden geanalyseerd. Verzamel het geheugen dump bestand voor de crash en neem contact op met de ondersteuning. Voer de volgende stappen uit om het dump bestand te verzamelen:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>De besturingssysteem schijf koppelen aan een nieuwe herstel-VM

1.  Gebruik stap 1-3 van de [VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.
2.  Maak met behulp van **verbinding met extern bureaublad** verbinding met de herstel-VM.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Het dump bestand zoeken en een ondersteunings ticket verzenden

1.  Ga op de virtuele machine herstellen naar de map Windows in de gekoppelde besturingssysteem schijf. Als de stuur programma-letter die is toegewezen aan de gekoppelde besturingssysteem schijf F, gaat u naar F:\Windows.
2.  Zoek het bestand Memory. dmp en [Verzend een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) met het geheugen dump bestand.
3.  Als u problemen ondervindt bij het vinden van het bestand Memory. dmp, wilt u in plaats daarvan mogelijk [niet-maskeer bare interrupt-aanroepen (NMI) gebruiken in de seriële console](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) . U kunt de handleiding volgen om [een crashdumpbestand te genereren met behulp van NMI-aanroepen](/windows/client-management/generate-kernel-or-complete-crash-dump).

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg voor meer informatie over het oplossen van problemen [veelvoorkomende opstart fouten](./boot-error-troubleshoot.md) of het [oplossen van problemen met een Windows-VM door de besturingssysteem schijf te koppelen aan een herstel-VM](./troubleshoot-recovery-disks-windows.md). U moet ook vertrouwd zijn met [het gebruik van diagnostische gegevens over opstarten voor het oplossen van problemen met een virtuele machine](./boot-diagnostics.md).
- Zie [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md)voor meer informatie over het gebruik van Resource Manager.
- Als u geen verbinding kunt maken met uw virtuele machine, raadpleegt u [problemen met RDP-verbindingen met een virtuele machine van Azure oplossen](./troubleshoot-rdp-connection.md).