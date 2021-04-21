---
title: Een Linux-Hybrid Runbook Worker implementeren in Azure Automation
description: In dit artikel wordt beschreven hoe u een Azure Automation Hybrid Runbook Worker runbooks kunt uitvoeren op Linux-computers in uw lokale datacenter of cloudomgeving.
services: automation
ms.subservice: process-automation
ms.date: 04/06/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 24dc0d2b243eb6c13e5670a1438876132c5e429e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833648"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Een Linux-Hybrid Runbook Worker

U kunt de gebruikersfunctie Hybrid Runbook Worker van Azure Automation gebruiken om runbooks rechtstreeks uit te voeren op de Azure- of niet-Azure-machine, inclusief servers die zijn geregistreerd [bij Azure Arc-servers.](../azure-arc/servers/overview.md) Vanaf de computer of server die als host voor de rol wordt gebruikt, kunt u runbooks rechtstreeks en op resources in de omgeving uitvoeren om deze lokale resources te beheren.

De Linux Hybrid Runbook Worker voert runbooks uit als een speciale gebruiker die kan worden verhoogd voor het uitvoeren van opdrachten die verhoogde bevoegdheden nodig hebben. Azure Automation slaat runbooks op en beheert ze en levert ze vervolgens aan een of meer aangewezen machines. In dit artikel wordt beschreven hoe u de Hybrid Runbook Worker installeert op een Linux-computer, hoe u de werkmedewerker verwijdert en hoe u een Hybrid Runbook Worker verwijdert.

Nadat u een runbook worker hebt geïmplementeerd, bekijkt u [Runbooks](automation-hrw-run-runbooks.md) uitvoeren op een Hybrid Runbook Worker voor meer informatie over het configureren van uw runbooks voor het automatiseren van processen in uw on-premises datacenter of andere cloudomgeving.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u het volgende hebt.

### <a name="a-log-analytics-workspace"></a>Een Log Analytics-werkruimte

De Hybrid Runbook Worker is afhankelijk van een Azure Monitor Log Analytics-werkruimte om de rol te installeren en te configureren. U kunt deze maken [via Azure Resource Manager](../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace), via [PowerShell](../azure-monitor/logs/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)of in [de Azure Portal](../azure-monitor/logs/quick-create-workspace.md).

Als u geen log analytics Azure Monitor werkruimte hebt, bekijkt u de richtlijnen Azure Monitor [logontwerp](../azure-monitor/logs/design-logs-deployment.md) voordat u de werkruimte maakt.

### <a name="log-analytics-agent"></a>Log Analytics-agent

De Hybrid Runbook Worker vereist de [Log Analytics-agent](../azure-monitor/agents/log-analytics-agent.md) voor het ondersteunde Linux-besturingssysteem. Voor servers of machines die buiten Azure worden gehost, kunt u de Log Analytics-agent installeren met behulp [Azure Arc ingeschakelde servers.](../azure-arc/servers/overview.md)

>[!NOTE]
>Nadat u de Log Analytics-agent voor Linux hebt geïnstalleerd, moet u de machtigingen van de map of het eigendom `sudoers.d` ervan niet wijzigen. Sudo-machtiging is vereist voor het **nxautomation-account.** Dit is de gebruikerscontext waaronder Hybrid Runbook Worker wordt uitgevoerd. De machtigingen mogen niet worden verwijderd. Als u dit beperkt tot bepaalde mappen of opdrachten, kan dit leiden tot een wijziging die kan worden gewijzigd.
>

### <a name="supported-linux-operating-systems"></a>Ondersteunde Linux-besturingssystemen

De Hybrid Runbook Worker ondersteunt de volgende distributies. Alle besturingssystemen worden verondersteld x64 te zijn. x86 wordt niet ondersteund voor een besturingssysteem.

* Amazon Linux 2012.09 tot 2015.09
* CentOS Linux 5, 6, 7 en 8
* Oracle Linux 5, 6 en 7
* Red Hat Enterprise Linux Server 5, 6, 7 en 8
* Debian GNU/Linux 6, 7 en 8
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS en 18.04 LTS
* SUSE Linux Enterprise Server 12 en 15 (SUSE heeft geen versie 13 of 14 uitgebracht)

> [!IMPORTANT]
> Voordat u de Updatebeheer inschakelen, die afhankelijk is van de systeemrol Hybrid Runbook Worker, controleert u de distributies die hier worden [ondersteund.](update-management/overview.md#supported-operating-systems)

### <a name="minimum-requirements"></a>Minimale vereisten

De minimale vereisten voor een Linux-systeem en gebruikersaccounts Hybrid Runbook Worker zijn:

* Twee kernen
* 4 GB aan RAM-geheugen
* Poort 443 (uitgaand)

| **Vereist pakket** | **Beschrijving** | **Minimumversie**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C-bibliotheek| 2.5-12 |
|Openssl| OpenSSL-bibliotheken | 1.0 (TLS 1.1 en TLS 1.2 worden ondersteund)|
|Curl | cURL-webclient | 7.15.5|
|Python-ctypes | Python 2.x of Python 3.x zijn vereist |
|PAM | Pluggable Authentication Modules|
| **Optioneel pakket** | **Beschrijving** | **Minimumversie**|
| PowerShell Core | Als u PowerShell-runbooks wilt uitvoeren, moet PowerShell Core geïnstalleerd. Zie [Installing PowerShell Core on Linux](/powershell/scripting/install/installing-powershell-core-on-linux) (Een apparaat installeren in Linux) voor meer informatie over het installeren ervan. | 6.0.0 |

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>Een computer toevoegen aan een Hybrid Runbook Worker groep

U kunt de werkmachine toevoegen aan een Hybrid Runbook Worker in een van uw Automation-accounts. Voor machines die als host voor de Hybrid Runbook Worker van het systeem worden Updatebeheer, kunnen ze worden toegevoegd aan een Hybrid Runbook Worker groep. U moet echter hetzelfde Automation-account gebruiken voor zowel Updatebeheer als Hybrid Runbook Worker groepslidmaatschap.

>[!NOTE]
>Azure Automation [Updatebeheer](./update-management/overview.md) installeert automatisch de systeem-Hybrid Runbook Worker op een Azure- of niet-Azure-machine die is ingeschakeld voor Updatebeheer. Deze werkmedewerker is echter niet geregistreerd bij Hybrid Runbook Worker in uw Automation-account. Als u uw runbooks op deze machines wilt uitvoeren, moet u ze toevoegen aan een Hybrid Runbook Worker groep. Volg stap 4 in de sectie [Een Linux-Hybrid Runbook Worker](#install-a-linux-hybrid-runbook-worker) om deze toe te voegen aan een groep.

## <a name="supported-linux-hardening"></a>Ondersteunde Linux-hardening

Het volgende wordt nog niet ondersteund:

* Gos

## <a name="supported-runbook-types"></a>Ondersteunde runbooktypen

Linux Hybrid Runbook Workers ondersteunt een beperkte set runbooktypen in Azure Automation en worden beschreven in de volgende tabel.

|Runbooktype | Ondersteund |
|-------------|-----------|
|Python 3 (preview)|Ja, alleen vereist voor deze distributies: SUSE LES 15, RHEL 8 en CentOS 8|
|Python 2 |Ja, voor elke distributie waarvoor Python 3<sup>1</sup> niet is vereist |
|PowerShell |Ja<sup>2</sup> |
|PowerShell-werkstroom |No |
|Grafisch |No |
|Grafische PowerShell-werkstroom |No |

<sup>1</sup> Zie [Ondersteunde Linux-besturingssystemen.](#supported-linux-operating-systems)

<sup>2</sup> PowerShell-runbooks moeten PowerShell Core worden geïnstalleerd op de Linux-computer. Zie [Installing PowerShell Core on Linux (Een](/powershell/scripting/install/installing-powershell-core-on-linux) apparaat installeren in Linux) voor meer informatie over het installeren ervan.

### <a name="network-configuration"></a>Netwerkconfiguratie

Zie Uw netwerk configureren Hybrid Runbook Worker voor [netwerkvereisten voor de Hybrid Runbook Worker.](automation-hybrid-runbook-worker.md#network-planning)

## <a name="install-a-linux-hybrid-runbook-worker"></a>Een Linux-Hybrid Runbook Worker

Er zijn twee methoden om een Hybrid Runbook Worker. U kunt een runbook importeren en uitvoeren vanuit de runbookgalerie in de Azure Portal, of u kunt handmatig een reeks PowerShell-opdrachten uitvoeren om dezelfde taak uit te voeren.

### <a name="importing-a-runbook-from-the-runbook-gallery"></a>Een runbook importeren uit de runbookgalerie

De importprocedure wordt gedetailleerd beschreven in [Runbooks importeren vanuit GitHub met de Azure Portal](automation-runbook-gallery.md#import-runbooks-from-github-with-the-azure-portal). De naam van het te importeren runbook is **Create Automation Linux HybridWorker**.

Het runbook maakt gebruik van de volgende parameters.

| Parameter | Status | Beschrijving |
| ------- | ----- | ----------- |
| `Location` | Verplicht | De locatie voor de Log Analytics-werkruimte. |
| `ResourceGroupName` | Verplicht | De resourcegroep voor uw Automation-account. |
| `AccountName` | Verplicht | De Automation-accountnaam waarin de Hybrid Run Worker wordt geregistreerd. |
| `CreateLA` | Verplicht | Indien waar, gebruikt de waarde van om `WorkspaceName` een Log Analytics-werkruimte te maken. Indien onwaar, moet de `WorkspaceName` waarde van verwijzen naar een bestaande werkruimte. |
| `LAlocation` | Optioneel | De locatie waar de Log Analytics-werkruimte wordt gemaakt of waar deze al bestaat. |
| `WorkspaceName` | Optioneel | De naam van de Log Analytics-werkruimte die moet worden gemaakt of gebruikt. |
| `CreateVM` | Verplicht | Indien waar, gebruikt u de waarde `VMName` van als de naam van een nieuwe VM. Als dit niet waar is, `VMName` gebruikt u om de bestaande VM te zoeken en te registreren. |
| `VMName` | Optioneel | De naam van de virtuele machine die is gemaakt of geregistreerd, afhankelijk van de waarde van `CreateVM` . |
| `VMImage` | Optioneel | De naam van de VM-afbeelding die moet worden gemaakt. |
| `VMlocation` | Optioneel | Locatie van de VM die is gemaakt of geregistreerd. Als deze locatie niet is opgegeven, wordt de waarde `LAlocation` van gebruikt. |
| `RegisterHW` | Verplicht | Indien waar, registreert u de VM als hybrid worker. |
| `WorkerGroupName` | Verplicht | Naam van de Hybrid Worker groep. |

### <a name="manually-run-powershell-commands"></a>PowerShell-opdrachten handmatig uitvoeren

Voer de volgende stappen uit om een Linux-Hybrid Runbook Worker te installeren en te configureren.

1. Schakel de Azure Automation-oplossing in uw Log Analytics-werkruimte in door de volgende opdracht uit te voeren in een PowerShell-opdrachtprompt met verhoogde bevoegdheid of in Cloud Shell in Azure Portal [:](https://portal.azure.com)

    ```powershell
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resourceGroupName> -WorkspaceName <workspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
    ```

2. Implementeer de Log Analytics-agent op de doelmachine.

    * Voor Azure-VM's installeert u de Log Analytics-agent voor Linux met behulp van [de extensie voor virtuele machines voor Linux.](../virtual-machines/extensions/oms-linux.md) Met de extensie wordt de Log Analytics-agent geïnstalleerd op virtuele Azure-machines en worden virtuele machines ingeschreven bij een bestaande Log Analytics-werkruimte. U kunt een Azure Resource Manager-sjabloon, de Azure CLI of Azure Policy gebruiken om het ingebouwde beleid Log Analytics-agent implementeren voor [ *Linux-* of Windows-VM's](../governance/policy/samples/built-in-policies.md#monitoring) toe te wijzen. Zodra de agent is geïnstalleerd, kan de computer worden toegevoegd aan een Hybrid Runbook Worker in uw Automation-account.

    * Voor niet-Azure-machines kunt u de Log Analytics-agent installeren met behulp [Azure Arc ingeschakelde servers.](../azure-arc/servers/overview.md) Arc-servers ondersteunen het implementeren van de Log Analytics-agent met behulp van de volgende methoden:

        - Het framework voor VM-extensies gebruiken.

            Met deze functie in Azure Arc ingeschakelde servers kunt u de VM-extensie van de Log Analytics-agent implementeren op een niet-Azure Windows- en/of Linux-server. VM-extensies kunnen worden beheerd met de volgende methoden op uw hybride machines of servers die worden beheerd door Arc-servers:

            - De [Azure Portal](../azure-arc/servers/manage-vm-extensions-portal.md)
            - De [Azure CLI](../azure-arc/servers/manage-vm-extensions-cli.md)
            - [Azure PowerShell](../azure-arc/servers/manage-vm-extensions-powershell.md)
            - Azure [Resource Manager sjablonen](../azure-arc/servers/manage-vm-extensions-template.md)

        - Met Azure Policy.

            Met deze methode gebruikt u het ingebouwde beleid Azure Policy Deploy Log Analytics agent to Linux or Windows Azure Arc machines (Log Analytics-agent implementeren op Linux- of [Windows Azure Arc-machines)](../governance/policy/samples/built-in-policies.md#monitoring) om te controleren of de Log Analytics-agent op de Arc-server is geïnstalleerd. Als de agent niet is geïnstalleerd, wordt deze automatisch geïmplementeerd met behulp van een hersteltaak. Als u van plan bent om de machines te bewaken met Azure Monitor voor VM's, gebruikt u in plaats daarvan het initiatief [Enable Azure Monitor voor VM's](../governance/policy/samples/built-in-initiatives.md#monitoring) om de Log Analytics-agent te installeren en configureren.

        U wordt aangeraden de Log Analytics-agent voor Windows of Linux te installeren met behulp van Azure Policy.

    > [!NOTE]
    > Voor het beheren van de configuratie van machines die de Hybrid Runbook Worker-rol met Desired State Configuration (DSC) ondersteunen, moet u de machines toevoegen als DSC-knooppunten.

    > [!NOTE]
    > Het [nxautomation-account](automation-runbook-execution.md#log-analytics-agent-for-linux) met de bijbehorende sudo-machtigingen moet aanwezig zijn tijdens de installatie van de Linux-Hybrid Worker. Als u de werkmedewerker probeert te installeren en het account niet aanwezig is of niet de juiste machtigingen heeft, mislukt de installatie.

3. Controleer of de agent rapporteert aan de werkruimte.

    De Log Analytics-agent voor Linux verbindt machines met een Azure Monitor Log Analytics-werkruimte. Wanneer u de agent op uw computer installeert en deze verbindt met uw werkruimte, worden automatisch de onderdelen gedownload die vereist zijn voor de Hybrid Runbook Worker.

    Wanneer de agent na enkele minuten verbinding heeft met uw Log Analytics-werkruimte, kunt u de volgende query uitvoeren om te controleren of deze heartbeatgegevens naar de werkruimte stuurt.

    ```kusto
    Heartbeat
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    In de zoekresultaten ziet u heartbeatrecords voor de computer, waarmee wordt aangegeven dat deze is verbonden en rapporteert aan de service. Standaard wordt door elke agent een heartbeat-record doorgestuurd naar de toegewezen werkruimte.

4. Voer de volgende opdracht uit om de machine toe te voegen aan Hybrid Runbook Worker groep, en geef de waarden voor de parameters `-w` , `-k` , en `-g` `-e` op.

    U kunt de vereiste gegevens voor parameters en op de pagina `-k` `-e` **Sleutels** in uw Automation-account ophalen. Selecteer **Sleutels** in **de sectie Accountinstellingen** aan de linkerkant van de pagina.

    ![Pagina Sleutels beheren](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * Kopieer voor `-e` de parameter de waarde voor **URL**.

    * Kopieer voor `-k` de parameter de waarde voor PRIMARY ACCESS **KEY**.

    * Geef voor de parameter de naam op van de Hybrid Runbook Worker de nieuwe Hybrid Runbook Worker van `-g` Linux moet worden lid. Als deze groep al in het Automation-account bestaat, wordt de huidige computer eraan toegevoegd. Als deze groep niet bestaat, wordt deze gemaakt met die naam.

    * Geef voor `-w` de parameter uw Log Analytics-werkruimte-id op.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

5. Controleer de implementatie nadat het script is voltooid. Op de **Hybrid Runbook Worker groepen** in uw Automation-account, onder het tabblad Gebruikers van de groep Hybrid **Runbook Workers,** ziet u de nieuwe of bestaande groep en het aantal leden. Als het een bestaande groep is, wordt het aantal leden verhoogd. U kunt de groep selecteren in de lijst op de pagina. Kies in het linkermenu **Hybrid Workers.** Op de **pagina Hybrid Workers** ziet u elk lid van de groep.

    > [!NOTE]
    > Als u de log analytics-extensie voor virtuele machines voor Linux gebruikt voor een Azure-VM, raden we u aan om deze in te stellen op , omdat versies voor automatisch bijwerken problemen kunnen veroorzaken met de `autoUpgradeMinorVersion` `false` Hybrid Runbook Worker. Zie Azure CLI-implementatie voor meer informatie over het handmatig bijwerken [van de extensie.](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)

## <a name="turn-off-signature-validation"></a>Handtekeningvalidatie uitschakelen

Standaard is voor Hybrid Runbook Workers voor Linux handtekeningvalidatie vereist. Als u een niet-ondertekend runbook op een werker hebt uitgevoerd, wordt er een `Signature validation failed` foutbericht weergegeven. Voer de volgende opdracht uit om handtekeningvalidatie uit te schakelen. Vervang de tweede parameter door uw Log Analytics-werkruimte-id.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker"></a><a name="remove-linux-hybrid-runbook-worker"></a>Verwijder de Hybrid Runbook Worker

U kunt de opdracht op de `ls /var/opt/microsoft/omsagent` Hybrid Runbook Worker de werkruimte-id op te halen. Er wordt een map gemaakt met de naam en de werkruimte-id.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Met dit script wordt de Log Analytics-agent voor Linux niet van de computer verwijderd. Hiermee worden alleen de functionaliteit en configuratie van de Hybrid Runbook Worker verwijderd.

## <a name="remove-a-hybrid-worker-group"></a>Een Hybrid Worker-groep verwijderen

Als u een Hybrid Runbook Worker Linux-machines wilt verwijderen, gebruikt u dezelfde stappen als voor een Hybrid Worker-groep in Windows. Zie [Een Hybrid Worker verwijderen.](automation-windows-hrw-install.md#remove-a-hybrid-worker-group)

## <a name="next-steps"></a>Volgende stappen

* Zie Runbooks uitvoeren op een Hybrid Runbook Worker voor meer informatie over het configureren van uw runbooks voor het automatiseren van processen in uw on-premises datacenter of andere [cloudomgeving.](automation-hrw-run-runbooks.md)

* Zie Troubleshoot Hybrid Runbook Worker issues - Linux (Problemen met hybrid runbook Workers oplossen [- Linux) voor](troubleshoot/hybrid-runbook-worker.md#linux)meer informatie over het oplossen van problemen met Hybrid Runbook Workers.
