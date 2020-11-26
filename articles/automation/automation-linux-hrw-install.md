---
title: Een Linux-Hybrid Runbook Worker implementeren in Azure Automation
description: In dit artikel wordt uitgelegd hoe u een Azure Automation Hybrid Runbook Worker installeert voor het uitvoeren van runbooks op Linux-computers in uw lokale Data Center of cloud omgeving.
services: automation
ms.subservice: process-automation
ms.date: 11/23/2020
ms.topic: conceptual
ms.openlocfilehash: 20683808c81b32560170b175edf1c37c332f47ad
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183614"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Een Linux-Hybrid Runbook Worker implementeren

U kunt de functie gebruikers Hybrid Runbook Worker van Azure Automation gebruiken om runbooks rechtstreeks op de Azure-of niet-Azure-machine uit te voeren, inclusief servers die zijn geregistreerd bij [servers met Azure Arc-functionaliteit](../azure-arc/servers/overview.md). Op de computer of server die als host fungeert voor de rol, kunt u runbooks rechtstreeks uitvoeren en aan resources in de omgeving om deze lokale resources te beheren.

De Linux-Hybrid Runbook Worker voert runbooks uit als een speciale gebruiker die kan worden uitgebreid voor het uitvoeren van opdrachten die moeten worden uitgebreid. Azure Automation runbooks opslaat en beheert en vervolgens aan een of meer aangewezen computers worden geleverd. In dit artikel wordt beschreven hoe u de Hybrid Runbook Worker op een Linux-computer installeert, hoe u de werk nemer verwijdert en hoe u een Hybrid Runbook Worker groep verwijdert.

Nadat u een runbook worker hebt geïmplementeerd, raadpleegt u [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md) voor meer informatie over het configureren van runbooks voor het automatiseren van processen in uw on-premises Data Center of een andere cloud omgeving.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u over het volgende beschikt.

### <a name="a-log-analytics-workspace"></a>Een Log Analytics-werk ruimte

De functie Hybrid Runbook Worker is afhankelijk van een Azure Monitor Log Analytics-werk ruimte om de rol te installeren en te configureren. U kunt dit maken via [Azure Resource Manager](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace), via [Power shell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)of in de [Azure Portal](../azure-monitor/learn/quick-create-workspace.md).

Als u geen Azure Monitor Log Analytics-werk ruimte hebt, raadpleegt u de [ontwerp richtlijnen voor Azure monitor logboek](../azure-monitor/platform/design-logs-deployment.md) voordat u de werk ruimte maakt.

### <a name="log-analytics-agent"></a>Log Analytics-agent

De Hybrid Runbook Worker-rol vereist de [log Analytics-agent](../azure-monitor/platform/log-analytics-agent.md) voor het ondersteunde Linux-besturings systeem. Voor servers of computers die buiten Azure worden gehost, kunt u de Log Analytics-agent installeren met [servers met Azure Arc-functionaliteit](../azure-arc/servers/overview.md).

>[!NOTE]
>Nadat u de Log Analytics-agent voor Linux hebt geïnstalleerd, moet u de machtigingen van de `sudoers.d` map of het eigendom niet wijzigen. De sudo-machtiging is vereist voor het **nxautomation** -account. Dit is de gebruikers context waarin de Hybrid Runbook worker wordt uitgevoerd. De machtigingen mogen niet worden verwijderd. Als u dit beperkt tot bepaalde mappen of opdrachten, kan dit leiden tot een belang rijke wijziging.
>

### <a name="supported-linux-operating-systems"></a>Ondersteunde Linux-besturingssystemen

De functie Hybrid Runbook Worker ondersteunt de volgende distributies. Er wordt ervan uitgegaan dat alle besturings systemen x64 zijn. x86 wordt niet ondersteund voor elk besturings systeem.

* Amazon Linux 2012,09 tot 2015,09
* CentOS Linux 5, 6 en 7
* Oracle Linux 5, 6 en 7
* Red Hat Enterprise Linux Server 5, 6 en 7
* Debian GNU/Linux 6, 7 en 8
* Ubuntu 12,04 LTS, 14,04 LTS, 16,04 LTS en 18,04 LTS
* SUSE Linux Enterprise Server 12

### <a name="minimum-requirements"></a>Minimale vereisten

De minimale vereisten voor een Linux-systeem en gebruikers Hybrid Runbook Worker zijn:

* Twee kernen
* 4 GB aan RAM-geheugen
* Poort 443 (uitgaand)

| **Vereist pakket** | **Beschrijving** | **Minimale versie**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotheek| 2.5-12 |
|Openssl| OpenSSL-bibliotheken | 1,0 (TLS 1,1 en TLS 1,2 worden ondersteund)|
|Curl | Krul webclient | 7.15.5|
|Python-ctypes | Python 2. x is vereist |
|PAM | Pluggable Authentication Modules|
| **Optioneel pakket** | **Beschrijving** | **Minimale versie**|
| PowerShell Core | Als u Power shell-runbooks wilt uitvoeren, moet Power shell Core zijn geïnstalleerd. Zie [Power shell core in Linux installeren](/powershell/scripting/install/installing-powershell-core-on-linux) voor meer informatie over het installeren ervan. | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Een machine toevoegen aan een Hybrid Runbook Worker groep

U kunt de werk computer toevoegen aan een Hybrid Runbook Worker groep in een van uw Automation-accounts. Voor computers die als host fungeren voor het systeem Hybrid Runbook worker die wordt beheerd door Updatebeheer, kunnen ze worden toegevoegd aan een Hybrid Runbook Worker groep. Maar u moet hetzelfde Automation-account gebruiken voor zowel Updatebeheer als het lidmaatschap van de Hybrid Runbook Worker groep.

>[!NOTE]
>Azure Automation [updatebeheer](./update-management/overview.md) installeert automatisch het systeem Hybrid Runbook worker op een Azure-of niet-Azure-machine die is ingeschakeld voor updatebeheer. Deze werk nemer is echter niet geregistreerd met Hybrid Runbook Worker groepen in uw Automation-account. Als u uw runbooks op deze computers wilt uitvoeren, moet u deze toevoegen aan een Hybrid Runbook Worker groep. Volg stap 4 onder de sectie [een Linux-Hybrid Runbook worker installeren](#install-a-linux-hybrid-runbook-worker) om het toe te voegen aan een groep.

## <a name="supported-linux-hardening"></a>Ondersteunde Linux-beveiliging

De volgende worden nog niet ondersteund:

* CIS

## <a name="supported-runbook-types"></a>Ondersteunde typen runbook

Hybrid Runbook Workers van Linux ondersteunen een beperkt aantal typen Runbook in Azure Automation en ze worden beschreven in de volgende tabel.

|Type Runbook | Ondersteund |
|-------------|-----------|
|Python 2 |Yes |
|PowerShell |Ja<sup>1</sup> |
|PowerShell-werkstroom |No |
|Grafisch |No |
|Grafische power shell-werk stroom |No |

<sup>1</sup> Power shell-runbooks vereisen dat Power shell core wordt geïnstalleerd op de Linux-machine. Zie [Power shell core in Linux installeren](/powershell/scripting/install/installing-powershell-core-on-linux) voor meer informatie over het installeren ervan.

### <a name="network-configuration"></a>Netwerkconfiguratie

Zie [uw netwerk configureren](automation-hybrid-runbook-worker.md#network-planning)voor de netwerk vereisten voor de Hybrid Runbook Worker.

## <a name="install-a-linux-hybrid-runbook-worker"></a>Een Linux-Hybrid Runbook Worker installeren

Als u een Linux-Hybrid Runbook Worker wilt installeren en configureren, voert u de volgende stappen uit.

1. Schakel de Azure Automation oplossing in uw Log Analytics-werk ruimte in door de volgende opdracht uit te voeren in een Power shell-opdracht prompt met verhoogde bevoegdheid of in Cloud Shell in de [Azure Portal](https://portal.azure.com):

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Implementeer de Log Analytics-agent op de doel computer.

    * Voor Azure-Vm's installeert u de Log Analytics-agent voor Linux met behulp [van de virtuele-machine extensie voor Linux](../virtual-machines/extensions/oms-linux.md). Met de uitbrei ding wordt de Log Analytics agent op virtuele machines van Azure geïnstalleerd en worden virtuele machines geregistreerd in een bestaande Log Analytics-werk ruimte. U kunt een Azure Resource Manager-sjabloon, de Azure CLI of de Azure Policy gebruiken om de implementatie Log Analytics agent toe te wijzen voor het ingebouwde beleid [voor *Linux* -of *Windows* -vm's](../governance/policy/samples/built-in-policies.md#monitoring) . Zodra de agent is geïnstalleerd, kan de computer worden toegevoegd aan een Hybrid Runbook Worker groep in uw Automation-account.

    * Voor niet-Azure-machines kunt u de Log Analytics-agent installeren met [servers voor Azure Arc ingeschakeld](../azure-arc/servers/overview.md). Arc ingeschakelde servers ondersteunen de implementatie van de Log Analytics agent met behulp van de volgende methoden:

        - Het Framework van VM-extensies gebruiken.

            Met deze functie in azure Arc enabled servers kunt u de VM-extensie van Log Analytics agent implementeren op een niet-Azure Windows-en/of Linux-server. VM-extensies kunnen worden beheerd met behulp van de volgende methoden op uw hybride computers of servers die worden beheerd door servers met Arc-functionaliteit:

            - De [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - De [Azure cli](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager-sjablonen](../azure-arc/servers/manage-vm-extensions-template.md)

        - Azure Policy gebruiken.

            Met deze methode gebruikt u de Azure Policy [log Analytics agent implementeren voor Linux of Windows Azure Arc machines](../governance/policy/samples/built-in-policies.md#monitoring) ingebouwd beleid om te controleren of de op de Arc ingeschakelde server de log Analytics-agent is geïnstalleerd. Als de agent niet is geïnstalleerd, wordt deze automatisch geïmplementeerd met een herstel taak. Als u van plan bent om de machines met Azure Monitor voor VM's te controleren, gebruikt u in plaats daarvan het Azure Monitor voor VM's-initiatief [inschakelen](../governance/policy/samples/built-in-initiatives.md#monitoring) en configureert u de log Analytics agent.

        U kunt het beste de Log Analytics-agent voor Windows of Linux installeren met behulp van Azure Policy.

    > [!NOTE]
    > Als u de configuratie wilt beheren van machines die ondersteuning bieden voor de Hybrid Runbook Worker rol met behulp van desired state Configuration (DSC), moet u de computers als DSC-knoop punten toevoegen.

    > [!NOTE]
    > Het [nxautomation-account](automation-runbook-execution.md#log-analytics-agent-for-linux) met de bijbehorende sudo-machtigingen moet aanwezig zijn tijdens de installatie van de Linux-Hybrid Worker. Als u de werk nemer probeert te installeren en het account niet aanwezig is of niet de juiste machtigingen heeft, mislukt de installatie.

3. Controleer of de agent wordt gerapporteerd aan de werk ruimte.

    De Log Analytics-agent voor Linux verbindt machines met een Azure Monitor Log Analytics-werk ruimte. Wanneer u de agent op de computer installeert en verbindt met uw werk ruimte, worden automatisch de onderdelen gedownload die vereist zijn voor de Hybrid Runbook Worker.

    Wanneer de agent na een paar minuten verbinding heeft gemaakt met uw Log Analytics-werk ruimte, kunt u de volgende query uitvoeren om te controleren of er heartbeat-gegevens naar de werk ruimte worden verzonden.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    In de zoek resultaten ziet u heartbeat-records voor de machine, waarmee wordt aangegeven dat deze is verbonden en dat er wordt gerapporteerd aan de service. Standaard stuurt elke agent een heartbeat-record naar de toegewezen werk ruimte.

4. Voer de volgende opdracht uit om de machine toe te voegen aan een Hybrid Runbook worker groep, waarbij u de waarden opgeeft voor de para meters,, `-w` `-k` `-g` en `-e` .

    U kunt de vereiste gegevens voor para meters `-k` en `-e` op de pagina **sleutels** in uw Automation-account ophalen. Selecteer **sleutels** onder de sectie **account instellingen** aan de linkerkant van de pagina.

    ![Pagina sleutels beheren](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * `-e`Kopieer de waarde voor **URL** voor de para meter.

    * Kopieer voor de `-k` para meter de waarde voor de **primaire toegangs sleutel**.

    * Geef voor de `-g` para meter de naam op van de Hybrid Runbook worker groep waaraan de nieuwe Linux Hybrid Runbook worker moet worden toegevoegd. Als deze groep al bestaat in het Automation-account, wordt de huidige machine hieraan toegevoegd. Als deze groep niet bestaat, wordt deze met die naam gemaakt.

    * Geef voor de `-w` para meter uw log Analytics werk ruimte-id op.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Controleer de implementatie nadat het script is voltooid. Op de pagina **Hybrid Runbook worker groepen** in uw Automation-account, onder het tabblad **gebruikers Hybrid Runbook Workers Group** , worden de nieuwe of bestaande groep en het aantal leden weer gegeven. Als het een bestaande groep is, wordt het aantal leden verhoogd. U kunt de groep selecteren in de lijst op de pagina vanuit het menu aan de linkerkant de optie **Hybrid Workers** kiezen. Op de pagina **Hybrid Workers** kunt u elk lid van de groep weer geven.

    > [!NOTE]
    > Als u de Log Analytics extensie van de virtuele machine voor Linux voor een Azure-VM gebruikt, wordt u aangeraden om in te stellen dat er `autoUpgradeMinorVersion` `false` problemen met de Hybrid Runbook Worker kunnen ontstaan door de versie van automatische upgrades. Zie [Azure cli-implementatie](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)voor meer informatie over het hand matig bijwerken van de extensie.

## <a name="turn-off-signature-validation"></a>Handtekening validatie uitschakelen

Voor Linux Hybrid Runbook Workers is standaard handtekening validatie vereist. Als u een niet-ondertekend runbook uitvoert op een werk nemer, ziet u een `Signature validation failed` fout melding. Voer de volgende opdracht uit om handtekening validatie uit te scha kelen. Vervang de tweede para meter door uw Log Analytics werk ruimte-ID.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>De Hybrid Runbook Worker verwijderen

U kunt de opdracht `ls /var/opt/microsoft/omsagent` op de Hybrid Runbook worker gebruiken om de werk ruimte-id op te halen. Er wordt een map gemaakt met de naam van de werk ruimte-ID.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Met dit script wordt de Log Analytics-agent voor Linux niet van de computer verwijderd. De functie en configuratie van de Hybrid Runbook Worker rol worden alleen verwijderd.

## <a name="remove-a-hybrid-worker-group"></a>Een Hybrid Worker-groep verwijderen

Als u een Hybrid Runbook Worker groep van Linux-machines wilt verwijderen, gebruikt u dezelfde stappen als voor een Windows Hybrid worker-groep. Zie [een Hybrid worker groep verwijderen](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Volgende stappen

* Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md)voor meer informatie over het configureren van uw runbooks om processen te automatiseren in uw on-premises Data Center of andere cloud omgeving.

* Zie [problemen met Hybrid Runbook worker problemen oplossen-Linux](troubleshoot/hybrid-runbook-worker.md#linux)voor meer informatie over het oplossen van problemen met uw Hybrid Runbook Workers.