---
title: Problemen met de Linux Update agent oplossen in Azure Automation
description: In dit artikel leest u hoe u problemen oplost en oplost met de Linux Windows Update agent in Updatebeheer.
services: automation
ms.date: 01/25/2021
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: 2fd92d79a3322b17f528194b9d39c26bf4c93b0c
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896254"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Problemen met de Linux Update-agent oplossen

Er kunnen verschillende redenen zijn waarom uw computer niet wordt weer gegeven als gereed (in orde) in Updatebeheer. U kunt de status van een Linux Hybrid Runbook Worker-agent controleren om het onderliggende probleem te bepalen. Hier volgen de drie gereedheids statussen voor een machine:

* Gereed: de Hybrid Runbook Worker is gedistribueerd en is minder dan één uur geleden voor het laatst gezien.
* De verbinding is verbroken: de Hybrid Runbook Worker is geïmplementeerd en de laatste keer één uur geleden voor het laatst weer gegeven.
* Niet geconfigureerd: de Hybrid Runbook Worker is niet gevonden of de implementatie is niet voltooid.

> [!NOTE]
> Er kan een lichte vertraging optreden tussen de Azure Portal weer geven en de huidige status van een machine.

In dit artikel wordt beschreven hoe u de probleem oplosser voor Azure-machines uitvoert vanaf de Azure Portal-en niet-Azure-machines in het [offline scenario](#troubleshoot-offline).

> [!NOTE]
> Het script voor de probleem Oplosser stuurt momenteel geen verkeer via een proxy server als er een is geconfigureerd.

## <a name="start-the-troubleshooter"></a>De probleem Oplosser starten

Voor Azure-machines selecteert u de koppeling **problemen oplossen** onder de gereedheids kolom van de **Update Agent** in de portal om de pagina problemen met de Update Agent te openen. Voor niet-Azure-computers brengt de koppeling u naar dit artikel. Zie de instructies in de sectie ' offline oplossen ' voor informatie over het oplossen van problemen met een niet-Azure-machine.

![Lijst pagina met VM'S](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Voor de controles moet de virtuele machine worden uitgevoerd. Als de VM niet wordt uitgevoerd, **start u de virtuele machine** weer.

Selecteer op de pagina Update Agent voor problemen oplossen de optie **controles uitvoeren** om de probleem oplosser te starten. De probleem Oplosser gebruikt de [opdracht uitvoeren](../../virtual-machines/linux/run-command.md) om een script uit te voeren op de machine om de afhankelijkheden te controleren. Wanneer de probleem Oplosser is voltooid, wordt het resultaat van de controles geretourneerd.

![Pagina problemen oplossen](../media/update-agent-issues-linux/troubleshoot-page.png)

Wanneer de controles zijn voltooid, worden de resultaten in het venster weer gegeven. De controle secties bevatten informatie over wat elke controle zoekt.

![Pagina controles van Update-Agent](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Controles van vereisten

### <a name="operating-system"></a>Besturingssysteem

De controle van het besturings systeem controleert of op de Hybrid Runbook Worker een van de volgende besturings systemen wordt uitgevoerd.

|Besturingssysteem  |Notities  |
|---------|---------|
|CentOS 6 (x86/x64) en 7 (x64)      | Linux-agents moeten toegang hebben tot een opslagplaats voor updates. Voor op classificatie gebaseerde patches moet ' yum ' worden geretourneerd om beveiligings gegevens te retour neren, wat CentOS geen out-of-box heeft.         |
|Red Hat Enterprise 6 (x86/x64) en 7 (x64)     | Linux-agents moeten toegang hebben tot een opslagplaats voor updates.        |
|SUSE Linux Enterprise Server 11 (x86/x64) en 12 (x64)     | Linux-agents moeten toegang hebben tot een opslagplaats voor updates.        |
|Ubuntu 14,04 LTS, 16,04 LTS en 18,04 LTS (x86/x64)      |Linux-agents moeten toegang hebben tot een opslagplaats voor updates.         |

## <a name="monitoring-agent-service-health-checks"></a>Status controles van de Monitoring Agent-service

### <a name="log-analytics-agent"></a>Log Analytics-agent

Met deze controle wordt ervoor gezorgd dat de Log Analytics-agent voor Linux is geïnstalleerd. Zie voor instructies over het installeren van [de agent voor Linux installeren](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux).

### <a name="log-analytics-agent-status"></a>Status van Log Analytics agent

Met deze controle wordt ervoor gezorgd dat de Log Analytics-agent voor Linux wordt uitgevoerd. Als de agent niet wordt uitgevoerd, kunt u de volgende opdracht uitvoeren om het opnieuw te proberen. Zie [Linux-troubleshooting Hybrid Runbook worker issues](hybrid-runbook-worker.md#linux)(Engelstalig) voor meer informatie over het oplossen van problemen met de agent.

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Met deze controle wordt bepaald of de agent op meerdere werk ruimten rapporteert. Updatebeheer biedt geen ondersteuning voor multihoming.

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker

Met deze controle wordt gecontroleerd of de Log Analytics-agent voor Linux het Hybrid Runbook Worker-pakket heeft. Dit pakket is vereist om Updatebeheer te kunnen werken. Zie [log Analytics-agent voor Linux is niet actief](hybrid-runbook-worker.md#oms-agent-not-running)voor meer informatie.

Updatebeheer downloadt Hybrid Runbook Worker pakketten van het eind punt van de bewerking. Daarom kan de update mislukken als de Hybrid Runbook Worker niet wordt uitgevoerd en de controle van het [eind punt](#operations-endpoint) mislukt.

### <a name="hybrid-runbook-worker-status"></a>Hybrid Runbook Worker status

Met deze controle wordt gecontroleerd of de Hybrid Runbook Worker op de computer wordt uitgevoerd. De processen in het onderstaande voor beeld moeten aanwezig zijn als de Hybrid Runbook Worker correct wordt uitgevoerd.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Connectiviteits controles

### <a name="general-internet-connectivity"></a>Algemene Internet verbinding

Met deze controle wordt gecontroleerd of de computer toegang heeft tot internet.

### <a name="registration-endpoint"></a>Registratie-eind punt

Met deze controle wordt bepaald of de Hybrid Runbook Worker goed kan communiceren met Azure Automation in de Log Analytics-werk ruimte.

Proxy-en firewall configuraties moeten de Hybrid Runbook Worker agent toestaan te communiceren met het eind punt van de registratie. Zie [netwerk planning](../automation-hybrid-runbook-worker.md#network-planning)voor een lijst met adressen en poorten die moeten worden geopend.

### <a name="operations-endpoint"></a>Eind punt van bewerkingen

Met deze controle wordt bepaald of de Log Analytics-agent correct kan communiceren met de taak runtime data service.

Proxy-en firewall configuraties moeten de Hybrid Runbook Worker agent toestaan te communiceren met de taak runtime gegevens service. Zie [netwerk planning](../automation-hybrid-runbook-worker.md#network-planning)voor een lijst met adressen en poorten die moeten worden geopend.

### <a name="log-analytics-endpoint-1"></a>Log Analytics eind punt 1

Met deze controle wordt gecontroleerd of de computer toegang heeft tot de eind punten die nodig zijn voor de Log Analytics-agent.

### <a name="log-analytics-endpoint-2"></a>Log Analytics eind punt 2

Met deze controle wordt gecontroleerd of de computer toegang heeft tot de eind punten die nodig zijn voor de Log Analytics-agent.

### <a name="log-analytics-endpoint-3"></a>Log Analytics eind punt 3

Met deze controle wordt gecontroleerd of de computer toegang heeft tot de eind punten die nodig zijn voor de Log Analytics-agent.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Problemen met offline oplossen

U kunt de probleem Oplosser offline gebruiken op een Hybrid Runbook Worker door het script lokaal uit te voeren. Het python-script, [UM_Linux_Troubleshooter_Offline. py](https://github.com/Azure/updatemanagement/blob/main/UM_Linux_Troubleshooter_Offline.py), bevindt zich in github. In het volgende voor beeld ziet u een voor beeld van de uitvoer van dit script:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Volgende stappen

[Problemen met Hybrid Runbook worker oplossen](hybrid-runbook-worker.md).
