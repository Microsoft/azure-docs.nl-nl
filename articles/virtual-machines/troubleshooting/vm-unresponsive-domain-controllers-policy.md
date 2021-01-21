---
title: VM is niet responsief wanneer een beleid voor domeincontrollers wordt toegepast
titlesuffix: Azure Virtual Machines
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij het standaard beleid voor domein controllers het opstarten van een virtuele machine van Azure voor komt.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 34e6b765-3496-46a1-b7d7-6def00884394
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: v-miegge
ms.openlocfilehash: 6c139398182ca9d875de0d3b21c58afe503bd8a5
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632271"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>VM is niet responsief wanneer een beleid voor domeincontrollers wordt toegepast

Dit artikel bevat stappen voor het oplossen van problemen waarbij het standaard beleid voor domein controllers het opstarten van een virtuele machine (VM) van Azure voor komt.

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat het besturings systeem niet meer reageert tijdens het opstarten met het **standaard beleid voor domein controllers** van het bericht.

  ![In afbeelding 1 wordt het besturings systeem weer gegeven dat is vastgelopen met het bericht standaard domein controllers beleid](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door recente wijzigingen aan het standaard beleid voor domein controllers. Anders moet er een analyse van het geheugen dump bestand worden uitgevoerd om de hoofd oorzaak te bepalen.

## <a name="solution"></a>Oplossing

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

Als u onlangs wijzigingen hebt aangebracht in het standaard beleid voor domein controllers, kunt u deze wijzigingen ongedaan maken om het probleem op te lossen. Als u niet zeker weet wat het probleem veroorzaakt, verzamelt u een geheugen dump en verzendt u een ondersteunings ticket.

### <a name="collect-the-memory-dump-file"></a>Het geheugen dump bestand verzamelen

Als u dit probleem wilt oplossen, moet u eerst het geheugen dump bestand voor de crash verzamelen en vervolgens contact opnemen met de ondersteuning met het geheugen dump bestand. Voer de volgende stappen uit om het dump bestand te verzamelen:

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>De besturingssysteem schijf koppelen aan een nieuwe herstel-VM

1. Gebruik stap 1-3 van de [VM-reparatie opdrachten](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) om een herstel-VM voor te bereiden.

1. Gebruik Verbinding met extern bureaublad verbinding maken met de herstel-VM.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Het dump bestand zoeken en een ondersteunings ticket verzenden

1. Ga op de reparatie-VM naar de map Windows in de gekoppelde besturingssysteem schijf. Als de stationsletter die is toegewezen aan de gekoppelde besturingssysteemschijf `F` is, gaat u naar `F:\Windows`.

1. Zoek het bestand Memory. dmp en [Verzend een ondersteunings ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) met het geheugen dump bestand.

1. Als u problemen ondervindt bij het vinden van het bestand Memory. dmp, wilt u in plaats daarvan mogelijk [niet-maskeer bare interrupt-aanroepen (NMI) gebruiken in de seriële console](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) . Volg de hand leiding voor het [genereren van een crash dump bestand met behulp van NMI-aanroepen](/windows/client-management/generate-kernel-or-complete-crash-dump).