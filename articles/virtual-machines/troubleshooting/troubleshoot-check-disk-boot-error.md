---
title: Bestands systeem controleren bij het opstarten van een Azure-VM | Microsoft Docs
description: Meer informatie over het oplossen van het probleem dat door VM wordt gecontroleerd bestands systeem controleren bij het opstarten | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 196f49a72932906e0a21b3c6c534c79d291a845f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632990"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows geeft ' controleren op bestands systeem ' bij het opstarten van een Azure VM

In dit artikel wordt de fout ' controle van het bestands systeem ' beschreven die kan optreden wanneer u een virtuele Windows-machine (VM) opstart in Microsoft Azure.


## <a name="symptom"></a>Symptoom 

Een Windows-VM start niet. Wanneer u de opstart scherm afbeeldingen in [Diagnostische gegevens over opstarten](boot-diagnostics.md)controleert, ziet u dat het proces controle schijf (chkdsk.exe) wordt uitgevoerd met een van de volgende berichten:

- Station scannen en herstellen (C:)
- Bestands systeem op C controleren:

## <a name="cause"></a>Oorzaak

Als er een NTFS-fout in het bestands systeem wordt gevonden, wordt de consistentie van de schijf bij de volgende keer opnieuw opstarten gecontroleerd en hersteld. Dit gebeurt meestal als de VM onverwacht opnieuw wordt opgestart of als het afsluit proces van de virtuele machine abrupt is onderbroken.

## <a name="solution"></a>Oplossing 

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

Windows wordt normaal opgestart nadat het proces voor het controleren van de schijf is voltooid. Als de virtuele machine is vastgelopen in het controle schijf proces, probeert u de controle schijf op de virtuele machine offline uit te voeren:
1. Maak een moment opname van de besturingssysteem schijf van de betrokken VM als back-up. Zie [snap shot a disk](../windows/snapshot-copy-managed-disk.md)(Engelstalig) voor meer informatie.
2. [Koppel de besturingssysteem schijf aan een herstel-VM](troubleshoot-recovery-disks-portal-windows.md).  
3. Op de virtuele machine voor herstel voert u schijf controleren uit op de gekoppelde besturingssysteem schijf. In het volgende voor beeld is de Stuur letter van de gekoppelde besturingssysteem schijf E: 

    ```console
    chkdsk E: /f
    ```

4. Nadat de controle schijf is voltooid, ontkoppelt u de schijf van de herstel-VM en koppelt u de schijf opnieuw aan de betrokken VM als een besturingssysteem schijf. Zie [problemen met een Windows-VM oplossen door de besturingssysteem schijf aan een herstel-VM te koppelen](troubleshoot-recovery-disks-portal-windows.md)voor meer informatie.
