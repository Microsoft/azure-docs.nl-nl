---
title: Problemen Azure Automation Updatebeheer oplossen
description: In dit artikel wordt beschreven hoe u problemen met Azure Automation Updatebeheer.
services: automation
ms.subservice: update-management
ms.date: 04/16/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 36bfd2185cb7a192ce0113ee0722395c8a4ee928
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830300"
---
# <a name="troubleshoot-update-management-issues"></a>Problemen met Updatebeheer oplossen

In dit artikel worden problemen besproken die kunnen voorkomen bij het implementeren van de Updatebeheer-functie op uw computers. Er is een probleemoplosser voor de agent Hybrid Runbook Worker om het onderliggende probleem te bepalen. Zie Problemen met Windows [Update-agent](update-agent-issues.md) oplossen en Problemen met linux-updateagent oplossen voor meer informatie over de [probleemoplosser.](update-agent-issues-linux.md) Zie Problemen met functie-implementatie oplossen voor andere problemen met de [implementatie van functies.](onboarding.md)

>[!NOTE]
>Als u problemen hebt bij het implementeren van Updatebeheer op een Windows-computer, opent u de Windows-Logboeken en controleert u het **Operations Manager-gebeurtenislogboek** onder Toepassings- en serviceslogboeken op de lokale computer.  Zoek naar gebeurtenissen met gebeurtenis-id 4502 en gebeurtenisdetails die `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` bevatten.

## <a name="scenario-linux-updates-shown-as-pending-and-those-installed-vary"></a><a name="updates-linux-installed-different"></a>Scenario: Linux-updates die worden weergegeven als in behandeling en geïnstalleerd variëren

### <a name="issue"></a>Probleem

Voor uw Linux-computer toont Updatebeheer updates die beschikbaar zijn onder **Classificatie Beveiliging** en **Overige.** Maar wanneer een updateschema wordt uitgevoerd op de computer,  bijvoorbeeld om alleen updates te installeren die overeenkomen met de beveiligingsclassificatie, verschillen de geïnstalleerde updates van of een subset van de updates die eerder met die classificatie overeenkomen.

### <a name="cause"></a>Oorzaak

Wanneer een evaluatie van updates van het besturingssysteem die in behandeling zijn voor uw Linux-computer is uitgevoerd, worden DEP-bestanden [(Open Vulnerability and Assessment Language)](https://oval.mitre.org/) van de Linux-distributieleverancier gebruikt door Updatebeheer voor classificatie. Categorisatie wordt uitgevoerd voor  Linux-updates als Beveiliging of **Overige,** op basis van de OVN-bestanden die updates bevatten voor het oplossen van beveiligingsproblemen of beveiligingsproblemen. Maar wanneer het updateschema wordt uitgevoerd, wordt het uitgevoerd op de Linux-machine met behulp van de juiste pakketbeheerprogramma's zoals YUM, APT of ZYPPER om ze te installeren. Het pakketbeheer voor de Linux-distributie kan een ander mechanisme hebben om updates te classificeren, waarbij de resultaten kunnen verschillen van de resultaten die zijn verkregen uit OVAAL-bestanden door Updatebeheer.

### <a name="resolution"></a>Oplossing

U kunt de Linux-machine, de toepasselijke updates en de classificatie handmatig controleren volgens het pakketbeheer van de distributie. Voer de volgende opdrachten uit om te **begrijpen** welke updates door uw pakketbeheer zijn geclassificeerd als Beveiliging.

Voor YUM retourneert de volgende opdracht een niet-nul lijst met updates die zijn gecategoriseerd **als Beveiliging** door Red Hat. In het geval van CentOS retourneert deze altijd een lege lijst en vindt er geen beveiligingsclassificatie plaats.

```bash
sudo yum -q --security check-update
```

Voor ZYPPER retourneert de volgende opdracht een lijst met updates die zijn gecategoriseerd **als Beveiliging** door SUSE.

```bash
sudo LANG=en_US.UTF8 zypper --non-interactive patch --category security --dry-run
```

Voor APT retourneert de volgende opdracht een niet-nul-lijst met updates die zijn gecategoriseerd als Beveiliging door Canonical voor Ubuntu Linux distributies. 

```bash
sudo grep security /etc/apt/sources.list > /tmp/oms-update-security.list LANG=en_US.UTF8 sudo apt-get -s dist-upgrade -oDir::Etc::Sourcelist=/tmp/oms-update-security.list
```

In deze lijst kunt u vervolgens de opdracht uitvoeren om `grep ^Inst` alle wachtende beveiligingsupdates op te halen.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>Scenario: U ontvangt de fout 'Kan de updateoplossing niet inschakelen'

### <a name="issue"></a>Probleem

Wanneer u een Updatebeheer in uw Automation-account probeert in te stellen, krijgt u de volgende foutmelding:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Oorzaak

Deze fout kan om de volgende redenen optreden:

* De netwerkfirewallvereisten voor de Log Analytics-agent zijn mogelijk niet juist geconfigureerd. Deze situatie kan ertoe leiden dat de agent mislukt bij het oplossen van de DNS-URL's.

* Updatebeheer is onjuist geconfigureerd en ontvangt de computer geen updates zoals verwacht.

* U ziet mogelijk ook dat op de computer de status onder `Non-compliant` **Naleving wordt weergeven.** Tegelijkertijd rapporteert agent **Desktop Analytics** agent als `Disconnected` .

### <a name="resolution"></a>Oplossing

* Voer de probleemoplosser [voor Windows](update-agent-issues.md#troubleshoot-offline) of [Linux](update-agent-issues-linux.md#troubleshoot-offline)uit, afhankelijk van het besturingssysteem.

* Ga naar [Netwerkconfiguratie](../automation-hybrid-runbook-worker.md#network-planning) voor meer informatie over welke adressen en poorten moeten worden toegestaan Updatebeheer werken.  

* Controleer op problemen met de bereikconfiguratie. [Bereikconfiguratie](../update-management/scope-configuration.md) bepaalt welke computers zijn geconfigureerd voor Updatebeheer. Als uw computer wordt weergegeven in uw werkruimte, maar niet in Updatebeheer, moet u de bereikconfiguratie instellen op de machines. Zie Machines inschakelen in de werkruimte voor meer informatie over [de bereikconfiguratie.](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace)

* Verwijder de werkconfiguratie door de stappen te volgen in De Hybrid Runbook Worker verwijderen van een [on-premises Windows-computer](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) of De Hybrid Runbook Worker verwijderen van een [on-premises Linux-computer.](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker)

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scenario: De bijgewerkte supersed heeft aangegeven dat deze ontbreekt in Updatebeheer

### <a name="issue"></a>Probleem

Oude updates worden voor een Automation-account weergegeven als ontbrekend, ook al zijn ze versneld. Een bijgewerkte update is een update die u niet hoeft te installeren, omdat er een latere update beschikbaar is waarmee hetzelfde beveiligingsprobleem wordt gecorrigeerd. Updatebeheer de bijgewerkte update genegeerd en wordt deze niet van toepassing ten voordele van de superseding update. Zie Update [is superseded](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)voor informatie over een gerelateerd probleem.

### <a name="cause"></a>Oorzaak

Bijgewerkte updates worden niet geweigerd in Windows Server Update Services (WSUS), zodat ze als niet van toepassing kunnen worden beschouwd.

### <a name="resolution"></a>Oplossing

Wanneer een verwisselde update 100 procent niet van toepassing wordt, moet u de goedkeuringstoestand van die update wijzigen `Declined` in in WSUS. Goedkeuringstoestand voor al uw updates wijzigen:

1. Selecteer in het Automation-account de **Updatebeheer** machinestatus weer te geven. Zie [Update-evaluaties weergeven.](../update-management/view-update-assessments.md)

2. Controleer de bijgewerkte update om er zeker van te zijn dat deze 100 procent niet van toepassing is.

3. Weiger de update op de WSUS-server aan waar [de machines rapporteren.](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates)

4. Selecteer **Computers** en dwing in de **kolom Naleving** opnieuw scannen op naleving af. Zie [Updates voor VM's beheren.](../update-management/manage-updates-for-vm.md)

5. Herhaal de bovenstaande stappen voor andere versnelde updates.

6. Voor Windows Server Update Services (WSUS) schoont u alle bijgewerkte updates op om de infrastructuur te vernieuwen met behulp van de wizard [WSUS-server opschonen.](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard)

7. Herhaal deze procedure regelmatig om het weergaveprobleem te corrigeren en de hoeveelheid schijfruimte die wordt gebruikt voor updatebeheer te minimaliseren.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scenario: Machines worden niet in de portal onder Updatebeheer

### <a name="issue"></a>Probleem

Uw computers hebben de volgende symptomen:

* Uw computer wordt `Not configured` in de Updatebeheer van een virtuele machine.

* Uw machines ontbreken in de Updatebeheer weergave van uw Azure Automation account.

* U hebt machines die worden weer te geven `Not assessed` als onder **Naleving**. U ziet echter heartbeatgegevens in Azure Monitor logboeken voor de Hybrid Runbook Worker maar niet voor Updatebeheer.

### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door lokale configuratieproblemen of door een onjuist geconfigureerde bereikconfiguratie. Mogelijke specifieke oorzaken zijn:

* Mogelijk moet u de Hybrid Runbook Worker.

* Mogelijk hebt u een quotum gedefinieerd in uw werkruimte dat is bereikt en dat verdere gegevensopslag verhindert.

### <a name="resolution"></a>Oplossing

1. Voer de probleemoplosser [voor Windows](update-agent-issues.md#troubleshoot-offline) of [Linux](update-agent-issues-linux.md#troubleshoot-offline)uit, afhankelijk van het besturingssysteem.

2. Zorg ervoor dat uw computer rapporteert aan de juiste werkruimte. Zie Agentconnectiviteit met de Azure Monitor voor hulp [bij het controleren van dit Azure Monitor.](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor) Zorg er ook voor dat deze werkruimte is gekoppeld aan uw Azure Automation account. Als u dit wilt bevestigen, gaat u naar uw Automation-account en selecteert **u Gekoppelde werkruimte** **onder Gerelateerde resources.**

3. Zorg ervoor dat de machines worden weer geven in de Log Analytics-werkruimte die is gekoppeld aan uw Automation-account. Voer de volgende query uit in de Log Analytics-werkruimte.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

    Als u uw computer niet in de queryresultaten ziet, is deze niet onlangs ingecheckt. Er is waarschijnlijk een lokaal configuratieprobleem en u moet [de agent opnieuw installeren.](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows)

    Als uw computer wordt vermeld in de queryresultaten, controleert u onder de eigenschap **Oplossingen** of **updates** worden vermeld. Hiermee wordt gecontroleerd of het is geregistreerd bij Updatebeheer. Als dat niet het probleem is, controleert u op problemen met de bereikconfiguratie. De [bereikconfiguratie](../update-management/scope-configuration.md) bepaalt welke computers zijn geconfigureerd voor Updatebeheer. Zie Machines inschakelen in de werkruimte voor het configureren van de bereikconfiguratie voor [het doel van de machine.](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace)

4. Voer deze query uit in uw werkruimte.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

   Als u een resultaat krijgt, is het quotum dat voor uw werkruimte is gedefinieerd, bereikt, waardoor gegevens `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` niet meer kunnen worden opgeslagen. Ga in uw werkruimte naar **gegevensvolumebeheer onder** Gebruik en **geschatte** kosten en wijzig of verwijder het quotum.

5. Als het probleem nog steeds niet is opgelost, volgt u de stappen in [Deploy a Windows Hybrid Runbook Worker](../automation-windows-hrw-install.md) to reinstall the Hybrid Worker for Windows (Een Windows-Hybrid Runbook Worker opnieuw installeren). Voor Linux volgt u de stappen in [Een Linux-Hybrid Runbook Worker.](../automation-linux-hrw-install.md)

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: Kan automation-resourceprovider niet registreren voor abonnementen

### <a name="issue"></a>Probleem

Wanneer u met functie-implementaties in uw Automation-account werkt, treedt de volgende fout op:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Oorzaak

De Automation-resourceprovider is niet geregistreerd in het abonnement.

### <a name="resolution"></a>Oplossing

Als u de Automation-resourceprovider wilt registreren, volgt u deze stappen in Azure Portal.

1. Selecteer in de lijst Azure-service onder aan de portal **Alle services** en selecteer vervolgens **Abonnementen** in de servicegroep Algemeen.

2. Selecteer uw abonnement.

3. Selecteer **resourceproviders** **onder Instellingen.**

4. Controleer in de lijst met resourceproviders of de resourceprovider Microsoft.Automation is geregistreerd.

5. Als deze niet wordt vermeld, registreert u de Microsoft.Automation-provider door de stappen te volgen in Fouten oplossen voor registratie [van resourceproviders.](../../azure-resource-manager/templates/error-register-resource-provider.md)

## <a name="scenario-scheduled-update-did-not-patch-some-machines"></a><a name="scheduled-update-missed-machines"></a>Scenario: Er is geen patch voor een geplande update op sommige computers geïnstalleerd

### <a name="issue"></a>Probleem

Machines die zijn opgenomen in een preview-versie van een update worden niet allemaal weergegeven in de lijst met computers die zijn gepatcht tijdens een geplande run, of VM's voor geselecteerde scopes van een dynamische groep worden niet weergegeven in de lijst met updatevoorbeelden in de portal.

De updatevoorbeeldlijst bestaat uit alle machines die zijn opgehaald door een Azure Resource Graph [query](../../governance/resource-graph/overview.md) voor de geselecteerde scopes. De bereiken worden gefilterd op computers waarop een Hybrid Runbook Worker geïnstalleerd en waarvoor u toegangsmachtigingen hebt.

### <a name="cause"></a>Oorzaak

Dit probleem kan een van de volgende oorzaken hebben:

* De abonnementen die zijn gedefinieerd in het bereik van een dynamische query, worden niet geconfigureerd voor de geregistreerde Automation-resourceprovider.

* De machines waren niet beschikbaar of hadden niet de juiste tags toen het schema werd uitgevoerd.

* U hebt niet de juiste toegang tot de geselecteerde bereiken.

* Met Azure Resource Graph query worden de verwachte machines niet opgehaald.

* Het systeem Hybrid Runbook Worker niet geïnstalleerd op de computers.

### <a name="resolution"></a>Oplossing

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Abonnementen die niet zijn geconfigureerd voor de geregistreerde Automation-resourceprovider

Als uw abonnement niet is geconfigureerd voor de Automation-resourceprovider, kunt u geen gegevens opvragen of ophalen op computers in dat abonnement. Gebruik de volgende stappen om de registratie voor het abonnement te controleren.

1. In de [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)gaat u naar de lijst met Azure-service.

2. Selecteer **Alle services** en selecteer vervolgens **Abonnementen** in de groep Algemene service.

3. Zoek het abonnement dat is gedefinieerd in het bereik voor uw implementatie.

4. Kies **resourceproviders** onder **Instellingen.**

5. Controleer of de resourceprovider Microsoft.Automation is geregistreerd.

6. Als deze niet wordt vermeld, registreert u de Microsoft.Automation-provider door de stappen te volgen in Fouten oplossen voor registratie [van resourceproviders.](../../azure-resource-manager/templates/error-register-resource-provider.md)

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Machines die niet beschikbaar zijn of niet correct zijn getagd wanneer het schema wordt uitgevoerd

Gebruik de volgende procedure als uw abonnement is geconfigureerd voor de Automation-resourceprovider, maar het uitvoeren van het updateschema met de opgegeven dynamische [groepen](../update-management/configure-groups.md) enkele machines heeft gemist.

1. Open in Azure Portal Automation-account en selecteer **Updatebeheer**.

2. Controleer [Updatebeheer om](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) de exacte tijd te bepalen waarop de update-implementatie is uitgevoerd.

3. Voor machines die u vermoedt te hebben gemist door Updatebeheer, gebruikt u Azure Resource Graph (ARG) om machinewijzigingen [te vinden.](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details)

4. Zoek naar wijzigingen gedurende een aanzienlijke periode, zoals één dag, voordat de update-implementatie werd uitgevoerd.

5. Controleer de zoekresultaten op eventuele systeemwijzigingen, zoals wijzigingen voor verwijderen of bijwerken, op de computers in deze periode. Deze wijzigingen kunnen de machinestatus of -tags wijzigen, zodat machines niet worden geselecteerd in de lijst met machines wanneer updates worden geïmplementeerd.

6. Pas zo nodig de computers en resource-instellingen aan om de machinestatus of tagproblemen op te lossen.

7. U kunt het updateschema opnieuw gebruiken om ervoor te zorgen dat de implementatie met de opgegeven dynamische groepen alle computers omvat.

#### <a name="incorrect-access-on-selected-scopes"></a>Onjuiste toegang tot geselecteerde bereiken

De Azure Portal geeft alleen machines weer waarvoor u schrijftoegang hebt in een bepaald bereik. Als u niet de juiste toegang voor een bereik hebt, zie Zelfstudie: Een gebruiker toegang verlenen tot [Azure-resources](../../role-based-access-control/quickstart-assign-role-user-portal.md)met behulp van de Azure Portal.

#### <a name="resource-graph-query-doesnt-return-expected-machines"></a>Resource Graph query retournt geen verwachte machines

Volg de onderstaande stappen om erachter te komen of uw query's correct werken.

1. Voer een Azure Resource Graph query uit die is opgemaakt zoals hieronder wordt weergegeven op de blade Resource Graph Explorer in Azure Portal. Als u geen kennis hebt Azure Resource Graph, bekijkt u deze [quickstart](../../governance/resource-graph/first-query-portal.md) voor meer informatie over het werken met Resource Graph Explorer. Deze query imiteert de filters die u hebt geselecteerd bij het maken van de dynamische groep in Updatebeheer. Zie [Dynamische groepen gebruiken met Updatebeheer.](../update-management/configure-groups.md)

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Hier volgt een voorbeeld:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```

2. Controleer of de machines die u zoekt, worden vermeld in de queryresultaten.

3. Als de machines niet worden vermeld, is er waarschijnlijk een probleem met het filter dat in de dynamische groep is geselecteerd. Pas de groepsconfiguratie naar behoefte aan.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybrid Runbook Worker niet geïnstalleerd op computers

Machines worden wel weergegeven in Azure Resource Graph queryresultaten, maar worden nog steeds niet weergegeven in de preview van dynamische groepen. In dit geval zijn de machines mogelijk niet aangewezen als hybrid runbook workers van het systeem en kunnen ze dus geen Azure Automation en Updatebeheer uitvoeren. Om ervoor te zorgen dat de machines die u verwacht te zien, zijn ingesteld als Hybrid Runbook Workers van het systeem:

1. Ga in Azure Portal naar het Automation-account voor een machine die niet correct wordt weergegeven.

2. Selecteer **Hybrid Worker-groepen** onder **Procesautomatisering.**

3. Selecteer het **tabblad Hybrid Worker-groepen van het** systeem.

4. Controleer of de hybrid worker aanwezig is voor die machine.

5. Als de machine niet is ingesteld als een systeem-Hybrid Runbook Worker, controleert [](../update-management/overview.md#enable-update-management) u de methoden voor het inschakelen van de machine onder de sectie Updatebeheer inschakelen van het artikel Updatebeheer Overzicht. De in te stellen methode is gebaseerd op de omgeving waarin de machine wordt uitgevoerd.

6. Herhaal de bovenstaande stappen voor alle machines die niet in de preview worden weergegeven.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scenario: Updatebeheer ingeschakeld, terwijl de VM nog steeds wordt weer geven als geconfigureerd

### <a name="issue"></a>Probleem

U ziet 15 minuten nadat de implementatie is gestart het volgende bericht op een VM:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Oorzaak

Deze fout kan om de volgende redenen optreden:

* Communicatie met het Automation-account wordt geblokkeerd.

* Er is een dubbele computernaam met verschillende broncomputer-ID's. Dit scenario treedt op wanneer een VM met een bepaalde computernaam wordt gemaakt in verschillende resourcegroepen en rapporteert aan dezelfde Logistics Agent-werkruimte in het abonnement.

* De VM-installatie afbeelding die wordt geïmplementeerd, is mogelijk afkomstig van een gekloonde machine die niet is voorbereid met Systeemvoorbereiding (sysprep) met de Log Analytics-agent voor Windows geïnstalleerd.

### <a name="resolution"></a>Oplossing

Voer de volgende query uit in de Log Analytics-werkruimte die is gekoppeld aan uw Automation-account om het exacte probleem met de VM te bepalen.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Communicatie met Automation-account geblokkeerd

Ga naar [Netwerkplanning](../update-management/overview.md#ports) voor meer informatie over welke adressen en poorten moeten worden toegestaan Updatebeheer te werken.

#### <a name="duplicate-computer-name"></a>Dubbele computernaam

Wijzig de naam van uw VM's om unieke namen in hun omgeving te garanderen.

#### <a name="deployed-image-from-cloned-machine"></a>Geïmplementeerde afbeelding van gekloonde machine

Als u een gekloonde afbeelding gebruikt, hebben verschillende computernamen dezelfde broncomputer-id. In dat geval:

1. Verwijder in uw Log Analytics-werkruimte de VM uit de opgeslagen zoekopdracht naar de `MicrosoftDefaultScopeConfig-Updates` bereikconfiguratie als deze wordt weergegeven. Opgeslagen zoekopdrachten vindt u onder **Algemeen** in uw werkruimte.

2. Voer de volgende cmdlet uit.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Voer uit `Restart-Service HealthService` om de health-service opnieuw te starten. Met deze bewerking wordt de sleutel opnieuw gemaakt en wordt een nieuwe UUID gegenereerd.

4. Als deze methode niet werkt, moet u eerst sysprep uitvoeren op de installatie afbeelding en vervolgens de Log Analytics-agent voor Windows installeren.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scenario: U ontvangt een foutbericht over een gekoppeld abonnement wanneer u een update-implementatie maakt voor machines in een andere Azure-tenant

### <a name="issue"></a>Probleem

U krijgt de volgende fout te zien wanneer u probeert een update-implementatie te maken voor machines in een andere Azure-tenant:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een update-implementatie maakt met Azure-VM's in een andere tenant die is opgenomen in een update-implementatie.

### <a name="resolution"></a>Oplossing

Gebruik de volgende tijdelijke oplossing om deze items te plannen. U kunt de cmdlet [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) gebruiken met de `ForUpdateConfiguration` parameter om een planning te maken. Gebruik vervolgens de cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) en geef de machines in de andere tenant door aan de `NonAzureComputer` parameter . In het volgende voorbeeld ziet u hoe u dit doet:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scenario: Niet-belicht opnieuw opstarten

### <a name="issue"></a>Probleem

Hoewel u de optie Besturingselement voor **opnieuw** opstarten hebt ingesteld **op** Nooit opnieuw opstarten, worden machines nog steeds opnieuw opgestart nadat updates zijn geïnstalleerd.

### <a name="cause"></a>Oorzaak

Windows Update kunnen worden gewijzigd door verschillende registersleutels, die het gedrag voor opnieuw opstarten kunnen wijzigen.

### <a name="resolution"></a>Oplossing

Controleer de registersleutels [](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) die worden vermeld onder Configuratie [](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) van Automatische updates door het register en de registersleutels te bewerken die worden gebruikt om het opnieuw opstarten te beheren om ervoor te zorgen dat uw computers correct zijn geconfigureerd.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scenario: Machine geeft 'Kan niet starten' weer in een update-implementatie

### <a name="issue"></a>Probleem

Een machine geeft een `Failed to start` status weer. Wanneer u de specifieke details voor de machine bekijkt, ziet u de volgende fout:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Oorzaak

Deze fout kan een van de volgende oorzaken hebben:

* De machine bestaat niet meer.
* De machine is uitgeschakeld en niet bereikbaar.
* De computer heeft een probleem met de netwerkverbinding en daarom is de hybrid worker op de machine onbereikbaar.
* Er is een update van de Log Analytics-agent die de id van de broncomputer heeft gewijzigd.
* De update-run is beperkt als u de limiet van 200 gelijktijdige taken in een Automation-account hebt bereikt. Elke implementatie wordt beschouwd als een taak en elke machine in een update-implementatie telt als een taak. Elke andere automatiserings- of update-implementatie die momenteel in uw Automation-account wordt uitgevoerd, telt mee voor de gelijktijdige taaklimiet.

### <a name="resolution"></a>Oplossing

Gebruik, indien van toepassing, [dynamische groepen](../update-management/configure-groups.md) voor uw update-implementaties. Bovendien kunt u de volgende stappen volgen.

1. Controleer of uw computer of server voldoet aan de [vereisten.](../update-management/overview.md#system-requirements)
2. Controleer de verbinding met de Hybrid Runbook Worker met behulp van de probleemoplosser Hybrid Runbook Worker agent. Zie Problemen met update-agent oplossen voor meer informatie over de [probleemoplosser.](update-agent-issues.md)

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scenario: Updates worden geïnstalleerd zonder implementatie

### <a name="issue"></a>Probleem

Wanneer u een Windows-computer inschrijft Updatebeheer, ziet u dat er updates zijn geïnstalleerd zonder implementatie.

### <a name="cause"></a>Oorzaak

In Windows worden updates automatisch geïnstalleerd zodra ze beschikbaar zijn. Dit gedrag kan verwarring veroorzaken als u niet hebt gepland dat een update op de computer wordt geïmplementeerd.

### <a name="resolution"></a>Oplossing

De  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` registersleutel wordt standaard ingesteld op een instelling van 4: `auto download and install` .

Voor Updatebeheer-clients raden we u aan deze sleutel in te stellen op 3: `auto download but do not auto install` .

Zie [Configuring Automatische updates (Configuratie van Automatische updates) voor meer informatie.](/windows/deployment/update/waas-wu-settings#configure-automatic-updates)

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenario: Machine is al geregistreerd bij een ander account

### <a name="issue"></a>Probleem

U ontvangt het volgende foutbericht:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Oorzaak

De machine is al geïmplementeerd in een andere werkruimte voor Updatebeheer.

### <a name="resolution"></a>Oplossing

1. Volg de stappen onder [Machines worden niet in](#nologs) de portal onder Updatebeheer om ervoor te zorgen dat de machine rapporteert aan de juiste werkruimte.
2. Schoon artefacten op de computer op door [de hybride runbookgroep](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)te verwijderen en probeer het vervolgens opnieuw.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scenario: Machine kan niet communiceren met de service

### <a name="issue"></a>Probleem

U ontvangt een van de volgende foutberichten:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Oorzaak

Een proxy, gateway of firewall blokkeert mogelijk de netwerkcommunicatie.

### <a name="resolution"></a>Oplossing

Controleer uw netwerk en controleer of de juiste poorten en adressen zijn toegestaan. Zie [Netwerkvereisten](../automation-hybrid-runbook-worker.md#network-planning) voor een lijst met poorten en adressen die vereist zijn voor Updatebeheer en Hybrid Runbook Workers.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scenario: Kan zelf-ondertekend certificaat niet maken

### <a name="issue"></a>Probleem

U ontvangt een van de volgende foutberichten:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Oorzaak

De Hybrid Runbook Worker kan geen zelf-ondertekend certificaat genereren.

### <a name="resolution"></a>Oplossing

Controleer of het systeemaccount leestoegang heeft tot de map **C:\ProgramData\Microsoft\Crypto\RSA** en probeer het opnieuw.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scenario: De geplande update is mislukt met de fout MaintenanceWindowExceeded

### <a name="issue"></a>Probleem

Het standaard onderhoudsvenster voor updates is 120 minuten. U kunt het onderhoudsvenster verhogen naar maximaal 6 uur of 360 minuten.

### <a name="resolution"></a>Oplossing

Als u wilt weten waarom dit is gebeurd tijdens een update-run nadat deze is [gestart,](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) controleert u de taakuitvoer van de betreffende computer in de run. Mogelijk vindt u specifieke foutberichten van uw computers die u kunt onderzoeken en actie kunt ondernemen.  

Bewerk mislukte geplande update-implementaties en verhoog het onderhoudsvenster.

Zie Updates installeren voor meer informatie [over onderhoudsvensters.](../update-management/deploy-updates.md#schedule-an-update-deployment)

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scenario: Machine wordt 'Niet geëvalueerd' en toont een HRESULT-uitzondering

### <a name="issue"></a>Probleem

* U hebt machines die worden weergegeven `Not assessed` als onder **Naleving** en u ziet daaronder een uitzonderingsbericht.
* U ziet een HRESULT-foutcode in de portal.

### <a name="cause"></a>Oorzaak

De Update-agent (Windows Update Agent in Windows; het pakketbeheer voor een Linux-distributie) is niet juist geconfigureerd. Updatebeheer is afhankelijk van de updateagent van de computer om de benodigde updates, de status van de patch en de resultaten van geïmplementeerde patches op te geven. Zonder deze informatie kunnen Updatebeheer niet goed rapporteren over de patches die nodig zijn of geïnstalleerd.

### <a name="resolution"></a>Oplossing

Probeer updates lokaal uit te voeren op de computer. Als deze bewerking mislukt, betekent dit meestal dat er een configuratiefout voor de updateagent is.

Dit probleem wordt vaak veroorzaakt door netwerkconfiguratie- en firewallproblemen. Gebruik de volgende controles om het probleem op te lossen.

* Voor Linux raadpleegt u de juiste documentatie om ervoor te zorgen dat u het netwerk-eindpunt van uw pakketopslagplaats kunt bereiken.

* Controleer voor Windows of de configuratie van uw agent zoals vermeld in Updates worden niet gedownload van het [intranet-eindpunt (WSUS/SCCM).](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm)

  * Als de computers zijn geconfigureerd voor Windows Update, moet u ervoor zorgen dat u de eindpunten kunt bereiken die worden beschreven in Problemen met [http/proxy.](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)
  * Als de machines zijn geconfigureerd voor Windows Server Update Services (WSUS), moet u ervoor zorgen dat u de WSUS-server kunt bereiken die is geconfigureerd met de [WUServer-registersleutel](/windows/deployment/update/waas-wu-settings).

Als u een HRESULT-melding ziet, dubbelklikt u op de uitzondering die rood wordt weergegeven om het hele uitzonderingsbericht weer te geven. Bekijk de volgende tabel voor mogelijke oplossingen of aanbevolen acties.

|Uitzondering  |Oplossing of actie  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Zoek in de relevante foutcode in de lijst met foutcodes voor [Windows-updates](https://support.microsoft.com/help/938205/windows-update-error-code-list) naar aanvullende informatie over de oorzaak van de uitzondering.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Deze geven problemen met de netwerkverbinding aan. Zorg ervoor dat uw computer netwerkverbinding heeft met Updatebeheer. Zie de [sectie netwerkplanning](../update-management/overview.md#ports) voor een lijst met vereiste poorten en adressen.        |
|`0x8024001E`| De updatebewerking is niet voltooid omdat de service of het systeem werd afgesloten.|
|`0x8024002E`| Windows Update-service is uitgeschakeld.|
|`0x8024402C`     | Als u een WSUS-server gebruikt, moet u ervoor zorgen dat de registerwaarden voor en onder de registersleutel de juiste `WUServer` `WUStatusServer`  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` WSUS-server opgeven.        |
|`0x80072EE2`|Er is een probleem met de netwerkverbinding of een probleem bij het praten met een geconfigureerde WSUS-server. Controleer de WSUS-instellingen en controleer of de service toegankelijk is vanaf de client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Zorg ervoor dat Windows Update service (wuauserv) actief is en niet is uitgeschakeld.        |
|`0x80070005`| Een fout bij geweigerde toegang kan worden veroorzaakt door een van de volgende problemen:<br> Geïnfecteerde computer<br> Windows Update niet juist geconfigureerd<br> Bestandsmachtigingsfout met de map %WinDir%\SoftwareDistribution<br> Onvoldoende schijfruimte op het systeemstation (C:).
|Eventuele andere algemene uitzonderingen     | Voer een zoekopdracht op internet uit voor mogelijke oplossingen en werk samen met uw lokale IT-ondersteuning.         |

Door het **bestand %Windir%\Windowsupdate.log** te controleren, kunt u ook mogelijke oorzaken vaststellen. Zie Het bestand [Windowsupdate.log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file)lezen voor meer informatie over het lezen van het logboek.

U kunt ook de probleemoplosser voor Windows Update [downloaden](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) en uitvoeren om te controleren op problemen met Windows Update op de computer.

> [!NOTE]
> De [Windows Update probleemoplosser](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) geeft aan dat deze is voor gebruik op Windows-clients, maar ook werkt op Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenario: Update uitvoeren retourneert De status Mislukt (Linux)

### <a name="issue"></a>Probleem

Een update-run wordt gestart, maar er worden fouten tijdens de run aangetroffen.

### <a name="cause"></a>Oorzaak

Mogelijke oorzaken:

* Pakketbeheer is niet in orde.
* Update-agent (WUA voor Windows, distributie-specifiek pakketbeheer voor Linux) is onjuist geconfigureerd.
* Specifieke pakketten verstoren patching in de cloud.
* De machine is niet bereikbaar.
* Updates hadden afhankelijkheden die niet zijn opgelost.

### <a name="resolution"></a>Oplossing

Als er fouten optreden tijdens een update-run nadat deze is gestart, [controleert u de](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) taakuitvoer van de betreffende computer in de run. Mogelijk vindt u specifieke foutberichten van uw computers die u kunt onderzoeken en actie kunt ondernemen. Updatebeheer vereist dat pakketbeheer in orde is voor geslaagde update-implementaties.

Als er direct voordat de taak mislukt specifieke patches, pakketten [](../update-management/deploy-updates.md#schedule-an-update-deployment) of updates worden gezien, kunt u proberen deze items uit te sluiten van de volgende update-implementatie. Zie logboekbestanden voor het Windows Update van [Windows Update logboekbestanden.](/windows/deployment/update/windows-update-logs)

Als u een patchprobleem niet kunt oplossen, maakt u een kopie van het bestand **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** en bewaart u het voor probleemoplossingsdoeleinden voordat de volgende update-implementatie wordt gestart.

## <a name="patches-arent-installed"></a>Patches worden niet geïnstalleerd

### <a name="machines-dont-install-updates"></a>Updates worden niet geïnstalleerd op machines

Voer de updates rechtstreeks op de machine uit. Als de computer de updates niet kan toepassen, raadpleegt u de lijst met mogelijke [fouten in de gids voor probleemoplossing.](#hresult)

Als updates lokaal worden uitgevoerd, verwijdert u de agent en installeert u deze opnieuw op de computer aan de hand van de richtlijnen in [Een VM verwijderen uit Updatebeheer](../update-management/remove-vms.md).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Ik weet dat er updates beschikbaar zijn, maar deze worden niet als beschikbaar op mijn machines weer geven

Dit gebeurt vaak als machines zijn geconfigureerd voor het ontvangen van updates van WSUS of Microsoft Endpoint Configuration Manager maar WSUS en Configuration Manager de updates niet hebben goedgekeurd.

U kunt controleren of de machines zijn geconfigureerd voor WSUS en SCCM door te verwijzen naar de registersleutel naar de registersleutels in de sectie Configuring Automatische updates by `UseWUServer` Editing the Registry [(Register)](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s)van dit artikel te bewerken.

Als updates niet zijn goedgekeurd in WSUS, worden ze niet geïnstalleerd. U kunt controleren op niet-goedgekeurde updates in Log Analytics door de volgende query uit te voeren.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Updates verschijnen na installatie maar ik kan ze niet vinden op mijn machine

Updates worden vaak verdrongen door andere updates. Zie Update [is superseded](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) in de handleiding voor probleemoplossing Windows Update meer informatie.

### <a name="installing-updates-by-classification-on-linux"></a>Updates per classificatie installeren op Linux

Het per classificatie implementeren van updates naar Linux ('Essentiële en beveiligingsupdates'), heeft belangrijke beperkingen, met name voor CentOS. Deze beperkingen worden beschreven op [de Updatebeheer overzichtspagina](../update-management/overview.md#linux).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 ontbreekt consistent

KB2267602 is de [definitie-update voor Windows Defender](https://www.microsoft.com/wdsi/definitions). Deze wordt dagelijks bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of het probleem niet kunt oplossen, probeert u een van de volgende kanalen voor aanvullende ondersteuning.

* Krijg antwoorden van Azure-experts via [Azure-forums.](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de klantervaring.
* Een incident ondersteuning voor Azure indienen. Ga naar de [ondersteuning voor Azure site](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning krijgen.**
