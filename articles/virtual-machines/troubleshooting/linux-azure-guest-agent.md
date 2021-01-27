---
title: Problemen met de Azure Linux-agent oplossen
description: Los problemen op met de Azure Linux-agent.
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
ms.openlocfilehash: 62b462d8e75fc291ac599ac99dbe4fb3a74fde2b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878694"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>Problemen met de Azure Linux-agent oplossen

Met de [Azure Linux-agent](../extensions/agent-linux.md) kan een virtuele machine (VM) communiceren met de infrastructuur controller (de onderliggende fysieke server waarop de virtuele machine wordt gehost) op het IP-adres 168.63.129.16.

>[!NOTE]
>Dit IP-adres is een virtueel openbaar IP-adres dat communicatie vereenvoudigt en niet kan worden geblokkeerd. Zie [Wat is IP-adres 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md)voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

Controleer de agent status en-versie om er zeker van te zijn dat deze nog steeds wordt ondersteund. Zie [minimale versie ondersteuning voor virtuele-machine agenten in azure](/troubleshoot/azure/virtual-machines/support-extensions-agent-version) om de versie ondersteuning te controleren of Raadpleeg [WALinuxAgent FAQ](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output) voor stappen om de status en versie te vinden.

## <a name="troubleshoot-a-not-ready-status"></a>Problemen met de status niet gereed oplossen

1. Controleer de service status van de Azure Linux-agent om er zeker van te zijn dat deze wordt uitgevoerd. De service naam kan **walinuxagent** of **waagent** zijn.

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

   Als de service wordt uitgevoerd, start u deze opnieuw om het probleem op te lossen. Als de service is gestopt, start u deze, wacht u een paar minuten en controleer vervolgens de status opnieuw.

1. Zorg ervoor dat automatisch bijwerken is ingeschakeld. Controleer de instelling voor automatisch bijwerken in **/etc/waagent.conf**.

   ```
   AutoUpdate.Enabled=y
   ```

   Zie [How to update the Azure Linux agent op een virtuele machine](../extensions/update-linux-agent.md)voor meer informatie over het bijwerken van de Azure Linux-agent.

1. Zorg ervoor dat de virtuele machine verbinding kan maken met de infrastructuur controller. Gebruik een hulp programma zoals krul om te testen of de virtuele machine verbinding kan maken met 168.63.129.16 op de poorten 80, 443 en 32526. Als de virtuele machine niet op de verwachte manier verbinding maakt, controleert u of uitgaande communicatie via de poorten 80, 443 en 32526 is geopend in uw lokale firewall op de virtuele machine. Als dit IP-adres wordt geblokkeerd, kan de VM-agent onverwacht gedrag weer geven.

## <a name="advanced-troubleshooting"></a>Geavanceerde probleemoplossing

Gebeurtenissen voor het oplossen van problemen met de Azure Linux-agent worden vastgelegd in het **/var/log/waagent.log** -bestand.

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Kan geen verbinding maken met WireServer IP (host-IP)

De volgende fout wordt weer gegeven in het **/var/log/waagent.log** -bestand wanneer de virtuele machine het WireServer-IP-adres niet kan bereiken op de hostserver.

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

Ga als volgt te werk om het probleem op te lossen:

* Maak verbinding met de virtuele machine met behulp van SSH en probeer vervolgens vanuit krul toegang te krijgen tot de volgende URL: http://168.63.129.16/?comp=versions .
* Controleer op problemen die mogelijk worden veroorzaakt door een firewall, een proxy of een andere bron die de toegang tot het IP-adres 168.63.129.16 mogelijk blokkeert.
* Controleer of Linux IPTables of een firewall van derden de toegang blokkeert tot de poorten 80, 443 en 32526.

## <a name="next-steps"></a>Volgende stappen

[Neem contact op met micro soft ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)als u problemen met de Azure Linux-agent wilt oplossen.