---
title: Problemen met Windows Update agent oplossen in Azure Automation
description: In dit artikel leest u hoe u problemen met de Windows Update Agent tijdens Updatebeheer oplost en oplost.
services: automation
ms.date: 01/25/2020
ms.topic: troubleshooting
ms.subservice: update-management
ms.openlocfilehash: 9516210021ce48f069ae3b3b4e02503527e0db24
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580886"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>Problemen met de Windows Update-agent oplossen

Er kunnen verschillende redenen zijn waarom uw computer niet wordt weer gegeven als gereed (in orde) tijdens een Updatebeheer-implementatie. U kunt de status van een Windows Hybrid Runbook Worker-agent controleren om het onderliggende probleem te bepalen. Hier volgen de drie gereedheids statussen voor een machine:

* Gereed: de Hybrid Runbook Worker is gedistribueerd en is minder dan één uur geleden voor het laatst gezien.
* De verbinding is verbroken: de Hybrid Runbook Worker is geïmplementeerd en de laatste keer één uur geleden voor het laatst weer gegeven.
* Niet geconfigureerd: de Hybrid Runbook Worker is niet gevonden of de implementatie is niet voltooid.

> [!NOTE]
> Er kan een lichte vertraging optreden tussen de Azure Portal weer geven en de huidige status van een machine.

In dit artikel wordt beschreven hoe u de probleem Oplosser uitvoert voor Azure-machines vanaf het Azure Portal en niet-Azure-machines in het [offline scenario](#troubleshoot-offline).

> [!NOTE]
> Het script voor de probleem Oplosser bevat nu controles voor Windows Server Update Services (WSUS) en voor de sleutels autodown load en install.

## <a name="start-the-troubleshooter"></a>De probleem Oplosser starten

Voor Azure-machines kunt u de pagina problemen met Update agent oplossen starten door de koppeling **problemen oplossen** te selecteren in de kolom **Update-gereedheid** in de portal. Voor niet-Azure-computers brengt de koppeling u naar dit artikel. Zie [problemen offline oplossen](#troubleshoot-offline) om problemen met een niet-Azure-computer op te lossen.

![Scherm afbeelding van de Updatebeheer lijst met virtuele machines](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> De virtuele machine moet worden uitgevoerd om de status van de Hybrid Runbook Worker te controleren. Als de VM niet wordt uitgevoerd, wordt **de knop VM starten** weer gegeven.

Selecteer op de pagina Update Agent voor problemen oplossen de optie **controles uitvoeren** om de probleem oplosser te starten. De probleem Oplosser gebruikt de [opdracht uitvoeren](../../virtual-machines/windows/run-command.md) om een script uit te voeren op de computer om afhankelijkheden te controleren. Wanneer de probleem Oplosser is voltooid, wordt het resultaat van de controles geretourneerd.

![Scherm afbeelding van de pagina Update Agent voor problemen oplossen](../media/update-agent-issues/troubleshoot-page.png)

De resultaten worden weer gegeven op de pagina wanneer ze klaar zijn. In de secties controles wordt weer gegeven wat er in elke controle is opgenomen.

![Scherm afbeelding van de controles van de Update agent oplossen](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Controles van vereisten

### <a name="operating-system"></a>Besturingssysteem

Met de controle van het besturings systeem wordt gecontroleerd of op de Hybrid Runbook Worker een van de besturings systemen wordt uitgevoerd die in de volgende tabel worden weer gegeven.

|Besturingssysteem  |Notities  |
|---------|---------|
|Windows Server 2012 en hoger |.NET Framework 4,6 of hoger is vereist. ([Down load de .NET Framework](/dotnet/framework/install/guide-for-developers).)<br/> Windows Power shell 5,1 is vereist.  ([Down load Windows Management Framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).)        |

### <a name="net-462"></a>.NET 4.6.2

Met de .NET Framework controle wordt gecontroleerd of het systeem [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) of hoger is geïnstalleerd.

### <a name="wmf-51"></a>WMF 5.1

De WMF-controle controleert of het systeem beschikt over de vereiste versie van Windows Management Framework (WMF), het [Windows Management framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="tls-12"></a>TLS 1.2

Met deze controle wordt bepaald of u gebruikmaakt van TLS 1,2 om uw communicatie te versleutelen. TLS 1,0 wordt niet meer ondersteund door het platform. Gebruik TLS 1,2 om met Updatebeheer te communiceren.

## <a name="connectivity-checks"></a>Connectiviteits controles

### <a name="registration-endpoint"></a>Registratie-eind punt

Met deze controle wordt bepaald of de agent goed kan communiceren met de Agent service.

Proxy-en firewall configuraties moeten de Hybrid Runbook Worker agent toestaan te communiceren met het eind punt van de registratie. Zie [netwerk planning](../automation-hybrid-runbook-worker.md#network-planning)voor een lijst met adressen en poorten die moeten worden geopend.

### <a name="operations-endpoint"></a>Eind punt van bewerkingen

Met deze controle wordt bepaald of de agent goed kan communiceren met de taak runtime gegevens service.

Proxy-en firewall configuraties moeten de Hybrid Runbook Worker agent toestaan te communiceren met de taak runtime gegevens service. Zie [netwerk planning](../automation-hybrid-runbook-worker.md#network-planning)voor een lijst met adressen en poorten die moeten worden geopend.

## <a name="vm-service-health-checks"></a>Status controles van de VM-service

### <a name="monitoring-agent-service-status"></a>Status van Monitoring Agent-service

Met deze controle wordt bepaald of de Log Analytics-agent voor Windows ( `healthservice` ) op de computer wordt uitgevoerd. Voor meer informatie over het oplossen van problemen met de service, Zie [de log Analytics-agent voor Windows wordt niet uitgevoerd](hybrid-runbook-worker.md#mma-not-running).

Zie [de agent voor Windows installeren](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows)als u de log Analytics-agent voor Windows opnieuw wilt installeren.

### <a name="monitoring-agent-service-events"></a>Service gebeurtenissen van monitoring agent

Met deze controle wordt bepaald of 4502 gebeurtenissen in de afgelopen 24 uur worden weer gegeven in het Azure Operations Manager-logboek op de computer.

Zie [gebeurtenis 4502 in het Operations Manager-logboek](hybrid-runbook-worker.md#event-4502) voor deze gebeurtenis voor meer informatie over deze gebeurtenis.

## <a name="access-permissions-checks"></a>Controles van toegangs machtigingen

> [!NOTE]
> De probleem Oplosser stuurt momenteel geen verkeer via een proxy server als er een is geconfigureerd.

### <a name="crypto-folder-access"></a>Toegang tot crypto-map

De toegangs controle voor de cryptografie mappen bepaalt of het lokale systeem account toegang heeft tot C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Problemen met offline oplossen

U kunt de probleem Oplosser op een Hybrid Runbook Worker offline gebruiken door het script lokaal uit te voeren. Haal het volgende script op uit GitHub: [UM_Windows_Troubleshooter_Offline.ps1](https://github.com/Azure/updatemanagement/blob/main/UM_Windows_Troubleshooter_Offline.ps1). Als u het script wilt uitvoeren, moet u WMF 4,0 of hoger hebben geïnstalleerd. Zie [verschillende versies van Power Shell installeren](/powershell/scripting/install/installing-powershell)voor informatie over het downloaden van de meest recente versie van Power shell.

De uitvoer van dit script ziet eruit als in het volgende voor beeld:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>Volgende stappen

[Problemen met Hybrid Runbook worker oplossen](hybrid-runbook-worker.md).
