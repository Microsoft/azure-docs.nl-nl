---
title: Problemen met Azure Linux-gast agent oplossen
description: Problemen met de Azure Linux-gast agent oplossen werkt niet
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 9e3b376cfaf5379acaf92713c42509471200d066
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95549929"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>Problemen met Azure Linux-gast agent oplossen

[Azure Linux Guest agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) is een virtuele machine (VM)-agent. Hiermee kan de virtuele machine communiceren met de infrastructuur controller (de onderliggende fysieke server waarop de virtuele machine wordt gehost) op het IP-adres 168.63.129.16. Dit IP-adres is een virtueel openbaar IP-adres dat de communicatie vereenvoudigt. Zie [Wat is IP-adres 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)? voor meer informatie.

## <a name="checking-agent-status-and-version"></a>Agent status en-versie controleren

Zie https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Problemen met de VM-agent met de status niet gereed oplossen

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>Stap 1 Controleer of de Azure Linux Guest Agent-service wordt uitgevoerd

Afhankelijk van distributie kan de service naam walinuxagent of waagent zijn:

```
root@nam-u18:/home/nam# service walinuxagent status
● walinuxagent.service - Azure Linux Agent
   Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
 Main PID: 1036 (python3)
    Tasks: 4 (limit: 4915)
   CGroup: /system.slice/walinuxagent.service
           ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
           └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers

Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
root@nam-u18:/home/nam#
```


Als u de services ziet en deze worden uitgevoerd, start u de service opnieuw om te zien of het probleem is opgelost. Als de services zijn gestopt, start u deze en wacht u enkele minuten. Controleer vervolgens of de **agent status** **gereed** is voor rapportage. Neem contact op met [Microsoft ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)voor meer informatie over het oplossen van deze problemen.

### <a name="step-2-check-whether-auto-update-is-enabled"></a>Stap 2 controleren of automatisch bijwerken is ingeschakeld

Controleer deze instelling in/etc/waagent.conf:

```
AutoUpdate.Enabled=y
```

Raadpleeg voor meer informatie over het bijwerken van de Azure Linux-agent https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Stap 3 Controleer of de virtuele machine verbinding kan maken met de infrastructuur controller

Gebruik een hulp programma zoals krul om te testen of de virtuele machine verbinding kan maken met 168.63.129.16 op de poorten 80, 32526 en 443. Als de virtuele machine niet op de verwachte manier verbinding maakt, controleert u of uitgaande communicatie via de poorten 80, 443 en 32526 is geopend in uw lokale firewall op de virtuele machine. Als dit IP-adres wordt geblokkeerd, kan de VM-agent in verschillende scenario's onverwacht gedrag vertonen.

## <a name="advanced-troubleshooting"></a>Geavanceerde probleemoplossing

Gebeurtenissen voor het oplossen van problemen met de Azure Linux-gast agent worden vastgelegd in de volgende logboek bestanden:

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Kan geen verbinding maken met WireServer IP (host-IP) 

U ziet de volgende fout vermeldingen in/var/log/waagent.log:

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**Bepaling**

De virtuele machine kan het WireServer-IP-adres niet bereiken op de hostserver.

**Oplossing**

1. Omdat het WireServer-IP-adres niet bereikbaar is, maakt u verbinding met de virtuele machine met behulp van SSH en probeert u vervolgens de volgende URL vanuit krul te openen: http://168.63.129.16/?comp=versions 
1. Controleer of er problemen zijn die kunnen worden veroorzaakt door een firewall, een proxy of een andere bron die de toegang tot het IP-adres 168.63.129.16 kunnen blok keren.
1. Controleer of Linux IPTables of een firewall van derden de toegang blokkeert tot de poorten 80, 443 en 32526. Zie [Wat is IP-adres 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)? voor meer informatie over waarom dit adres niet moet worden geblokkeerd.


## <a name="next-steps"></a>Volgende stappen

[Neem contact op met micro soft ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)voor meer problemen met het probleem ' Windows Azure Guest agent werkt niet '.
