---
title: Azure seriële console voor SysRq-en NMI-aanroepen | Microsoft Docs
description: Met behulp van seriële console voor SysRq-en NMI-aanroepen in azure virtual machines.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 545399e1d7941351ce861ac98d995d5e57006ea1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022850"
---
# <a name="use-the-azure-serial-console-for-sysrq-and-nmi-calls"></a>De Azure-seriële console gebruiken voor SysRq-en NMI-aanroepen

## <a name="system-request-sysrq"></a>Systeem aanvraag (SysRq)
Een SysRq is een reeks sleutels die worden begrepen door de Linux-bewerkings systeem-kernel, waarmee een reeks vooraf gedefinieerde acties kan worden geactiveerd. Deze opdrachten worden vaak gebruikt bij het oplossen van problemen met de virtuele machine of het herstel kan niet worden uitgevoerd via een traditioneel beheer (bijvoorbeeld als de VM niet reageert). Met de SysRq-functie van Azure Serial console wordt op het drukken van de SysRq-sleutel en de tekens die op een fysiek toetsen bord zijn ingevoerd, gesimuleerd.

Zodra de SysRq-reeks is geleverd, bepaalt de kernel configuratie hoe het systeem reageert. Voor informatie over het in-en uitschakelen van SYSRQ, raadpleegt u de tekst voor de *SYSRQ-beheerders handleiding* [text](https://aka.ms/kernelorgsysreqdoc)  |  [markdown](https://aka.ms/linuxsysrq).

De Azure Serial console kan worden gebruikt om een SysRq te verzenden naar een virtuele Azure-machine met behulp van het toetsenbord pictogram in de onderstaande opdracht balk.

![Scherm opname van de Azure seriële console. Het toetsenbord pictogram is gemarkeerd en het bijbehorende menu wordt weer gegeven. Dit menu bevat een opdracht Item Send SysRq.](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Als u de opdracht ' SysRq verzenden ' kiest, wordt er een dialoog venster geopend, waarmee u algemene SysRq-opties kunt opgeven, of een reeks SysRq-opdrachten die in het dialoog venster zijn ingevoerd.  Op die manier kunnen reeksen SysRq een bewerking op hoog niveau uitvoeren, zoals veilig opnieuw opstarten met behulp van: `REISUB` .

![Scherm afbeelding van het dialoog venster SysRq verzenden naar gast. De optie voor het invoeren van opdrachten is geselecteerd en het opdracht venster bevat REISUB.](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

De opdracht SysRq kan niet worden gebruikt op virtuele machines die zijn gestopt of waarvan de kernel een niet-reagerende status heeft. (bijvoorbeeld een kernel paniek).

### <a name="enable-sysrq"></a>SysRq inschakelen
Zoals beschreven in de *SYSRQ-beheer handleiding* hierboven, kan SYSRQ zodanig worden geconfigureerd dat alle, geen of alleen bepaalde opdrachten beschikbaar zijn. U kunt alle SysRq-opdrachten inschakelen met behulp van de onderstaande stap, maar deze wordt niet opnieuw opgestart:
```
echo "1" >/proc/sys/kernel/sysrq
```
Als u de SysReq-configuratie permanent wilt maken, kunt u het volgende doen om alle SysRq-opdrachten in te scha kelen:
1. Deze regel toevoegen aan */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Sysctl opnieuw opstarten of bijwerken door uit te voeren <br>
    `sysctl -p`

### <a name="command-keys"></a>Opdracht sleutels
Vanuit de SysRq-beheer handleiding hierboven:

|Opdracht| Functie
| ------| ----------- |
|``b``  |   Het systeem wordt onmiddellijk opnieuw opgestart zonder de schijven te synchroniseren of te ontkoppelen.
|``c``  |   Voert een systeem crash uit door een verwijzing naar een NULL-aanwijzer. Er wordt een crash dump genomen als deze is geconfigureerd.
|``d``  |   Toont alle vergrendelde vergren delingen.
|``e``  |   Een SIGTERM verzenden naar alle processen, met uitzonde ring van init.
|``f``  |   Roept de oom-Killer aan om een geheugen Hog proces af te breken, maar niet in paniek als niets kan worden afgebroken.
|``g``  |   Gebruikt door KGDB (kernel debugger)
|``h``  |   De Help wordt weer gegeven (een andere sleutel dan die hier wordt vermeld, maar ``h`` is gemakkelijk te onthouden:-)
|``i``  |    Een SIGKILL verzenden naar alle processen, met uitzonde ring van init.
|``j``  |    Alleen het ontdooien:-bestands systeem dat door de FIFREEZE IOCTL is geblokkeerd.
|``k``  |    Met de beveiligde Toegangs toets (SAK) worden alle Program ma's op de huidige virtuele console afbreken. Opmerking: Zie belang rijke opmerkingen hieronder in de sectie SAK.
|``l``  |    Toont een stack-backtrace voor alle actieve Cpu's.
|``m``  |    Dumpt de huidige geheugen gegevens naar uw-console.
|``n``  |    Wordt gebruikt om RT-taken goed te kunnen uitvoeren
|``o``  |    Het systeem wordt afgesloten (indien geconfigureerd en ondersteund).
|``p``  |    Hiermee worden de huidige kassa's en vlaggen voor uw console gedumpt.
|``q``  |    Zal per CPU-lijst van alle gehrtimerseerde (maar niet normale timer_list timers) dumpen en gedetailleerde informatie over alle clockevent-apparaten.
|``r``  |    Hiermee schakelt u de modus voor onbewerkte toetsaanslagen uit en stelt u deze in op XLATE.
|``s``  |    Er wordt geprobeerd om alle gekoppelde bestands systemen te synchroniseren.
|``t``  |    Er wordt een lijst met huidige taken en de bijbehorende informatie naar uw console gedumpt.
|``u``  |    Er wordt geprobeerd alle gekoppelde bestands systemen alleen-lezen te koppelen.
|``v``  |    Hiermee wordt de framebuffer-console geforceerd hersteld
|``v``  |    Veroorzaakt ETM buffer dump [ARM-specifiek]
|``w``  |    Dumpt taken die zich in de status van een nood situatie (geblokkeerd) bevinden.
|``x``  |    Wordt gebruikt door de xmon-interface op PPC/powerpc-platforms. Algemene PMU-registers weer geven op sparc64. Alle TLB-vermeldingen op MIPS dumpen.
|``y``  |    Globale CPU-registers weer geven [SPARC-64-specifiek]
|``z``  |    De ftrace-buffer dumpen
|``0``-``9`` | Hiermee stelt u het logboek niveau van de console in, waarbij wordt beheerd welke kernel-berichten worden afgedrukt op uw-console. ( ``0`` Dit kan bijvoorbeeld zodanig zijn dat alleen nood berichten, zoals een paniek of een bestand, het naar uw-console zou kunnen maken.)

### <a name="distribution-specific-documentation"></a>Distributie-specifieke documentatie ###
Zie de onderstaande koppelingen voor distributie-specifieke documentatie over SysRq en stappen voor het configureren van Linux voor het maken van een crash dump bij het ontvangen van een SysRq ' crash '.

#### <a name="ubuntu"></a>Ubuntu ####
 - [Kernel-crash dump](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Wat is de SysRq-faciliteit en hoe kan ik deze gebruiken?](https://access.redhat.com/articles/231663)
- [De SysRq-faciliteit gebruiken om informatie te verzamelen van een RHEL-server](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Kernel core-dump vastleggen configureren](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Vastlopende logboeken verzamelen](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Niet-maskeer bare interrupt (NMI)
Een niet-maskeer bare interrupt (NMI) is ontworpen om een signaal te maken dat software op een virtuele machine niet wordt genegeerd. In het verleden werden NMIs gebruikt voor het bewaken van hardwareproblemen op systemen waarop specifieke reactie tijden vereist zijn.  Tegenwoordig gebruiken programmeurs en systeem beheerders NMI vaak als een mechanisme voor het opsporen van problemen met systemen die niet reageren.

De seriële console kan worden gebruikt om een NMI te verzenden naar een virtuele Azure-machine met behulp van het toetsenbord pictogram in de onderstaande opdracht balk. Zodra de NMI is geleverd, wordt door de configuratie van de virtuele machine bepaald hoe het systeem reageert.  Linux-besturings systemen kunnen worden geconfigureerd om te crashen en een geheugen dump te maken waarbij het besturings systeem een NMI ontvangt.

![Scherm opname van de seriële console. Het toetsenbord pictogram is gemarkeerd en het bijbehorende menu wordt weer gegeven. Het menu bevat een interrupt-item dat niet kan worden gemaskeerd.](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

### <a name="enable-nmi"></a>NMI inschakelen
Voor Linux-systemen die sysctl ondersteunen voor het configureren van kernel-para meters, kunt u een paniek inschakelen wanneer deze NMI wordt ontvangen met behulp van het volgende:
1. Deze regel toevoegen aan */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Sysctl opnieuw opstarten of bijwerken door uit te voeren <br>
    `sysctl -p`

`unknown_nmi_panic` `panic_on_io_nmi` `panic_on_unrecovered_nmi` Zie de [documentatie voor/proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt)voor meer informatie over Linux-kernel-configuraties, waaronder, en. Zie de onderstaande koppelingen voor distributie-specifieke documentatie over NMI en stappen voor het configureren van Linux voor het maken van een crash dump bij het ontvangen van een NMI.

### <a name="ubuntu"></a>Ubuntu
 - [Kernel-crash dump](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat
 - [Wat is een NMI en waarvoor kan ik deze gebruiken?](https://access.redhat.com/solutions/4127)
 - [Hoe kan ik mijn systeem laten vastlopen wanneer de NMI-switch wordt gepusht?](https://access.redhat.com/solutions/125103)
 - [Beheerders handleiding voor crash dump](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE
- [Kernel core-dump vastleggen configureren](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS
- [Vastlopende logboeken verzamelen](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Volgende stappen
* De documentatie pagina voor de hoofd console Linux bevindt zich [hier](serial-console-linux.md).
* Seriële console gebruiken om op te starten in [grub en de modus voor één gebruiker](serial-console-grub-single-user-mode.md) in te voeren
* De seriële console is ook beschikbaar voor virtuele [Windows](serial-console-windows.md) -machines
* Meer informatie over [Diagnostische gegevens over opstarten](boot-diagnostics.md)