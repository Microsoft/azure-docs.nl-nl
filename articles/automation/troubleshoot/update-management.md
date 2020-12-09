---
title: Problemen met Azure Automation Updatebeheer oplossen
description: In dit artikel leest u hoe u problemen oplost en oplost met Azure Automation Updatebeheer.
services: automation
ms.date: 12/04/2020
ms.topic: conceptual
ms.service: automation
ms.openlocfilehash: c6d0f38eaa25f2fe033a5e2cf48ee6daa51fcbe6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929273"
---
# <a name="troubleshoot-update-management-issues"></a>Problemen met Updatebeheer oplossen

In dit artikel worden problemen beschreven die u kunt uitvoeren bij het implementeren van de Updatebeheer-functie op uw computers. Er is een agent probleem oplosser voor de Hybrid Runbook Worker-agent om het onderliggende probleem te bepalen. Zie problemen [met Windows Update agent oplossen](update-agent-issues.md) en problemen [met de Linux-Update agent oplossen](update-agent-issues-linux.md)voor meer informatie over de probleem Oplosser. Zie problemen [met de implementatie van onderdelen oplossen](onboarding.md)voor meer informatie over het implementeren van problemen met functies.

>[!NOTE]
>Als u problemen ondervindt bij het implementeren van Updatebeheer op een Windows-computer, opent u de Windows-Logboeken en raadpleegt u het gebeurtenis logboek van **Operations Manager** onder **Logboeken voor toepassingen en services** op de lokale computer. Zoek naar gebeurtenissen met gebeurtenis-ID 4502 en gebeurtenis details die bevatten `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` .

## <a name="scenario-linux-updates-shown-as-pending-and-those-installed-vary"></a><a name="updates-linux-installed-different"></a>Scenario: Linux-updates die worden weer gegeven als in behandeling en die zijn geïnstalleerd, zijn afhankelijk

### <a name="issue"></a>Probleem

Voor uw Linux-machine bevat Updatebeheer specifieke updates die beschikbaar zijn onder classificatie **beveiliging** en **andere**. Maar wanneer een update planning op de computer wordt uitgevoerd, bijvoorbeeld om alleen updates te installeren die overeenkomen met de **beveiligings** classificatie, zijn de geïnstalleerde updates anders dan of een subset van de updates die eerder zijn afgestemd op die classificatie.

### <a name="cause"></a>Oorzaak

Wanneer een evaluatie van updates van het besturings systeem die in behandeling zijn voor uw Linux-machine is uitgevoerd, opent u het [beveiligings probleem en](https://oval.mitre.org/) de door de Linux distributie-leverancier verwerkte ovaal bestanden worden gebruikt door updatebeheer voor classificatie. Er wordt gecategoriseerd voor Linux-updates als **beveiliging** of **anderen**, op basis van de ovale bestanden die de updates van beveiligings problemen of beveiligings lekken afhandelen. Maar wanneer de update planning wordt uitgevoerd, wordt deze uitgevoerd op de Linux-machine met behulp van de juiste pakket manager, zoals YUM, APT of ZYPPER om ze te installeren. Pakket beheer voor de Linux-distributie kan een ander mechanisme hebben voor het classificeren van updates, waarbij de resultaten kunnen verschillen van de waarden die zijn verkregen via OVALE bestanden door Updatebeheer.

### <a name="resolution"></a>Oplossing

U kunt de Linux-computer, de toepasselijke updates en de classificatie hand matig controleren volgens de package manager van de distributie. Voer de volgende opdrachten uit om te begrijpen welke updates worden geclassificeerd als **beveiliging** door uw pakket Manager.

Voor YUM retourneert de volgende opdracht een niet-nulwaarde lijst met updates die zijn gecategoriseerd als **beveiliging** door Red Hat. Houd er rekening mee dat in het geval van CentOS altijd een lege lijst wordt geretourneerd en er geen beveiligings classificatie plaatsvindt.

```bash
sudo yum -q --security check-update
```

Voor ZYPPER retourneert de volgende opdracht een niet-nulwaarde lijst met updates die zijn gecategoriseerd als **beveiliging** door SuSE.

```bash
sudo LANG=en_US.UTF8 zypper --non-interactive patch --category security --dry-run
```

Voor APT retourneert de volgende opdracht een niet-nul lijst met updates die zijn gecategoriseerd als **beveiliging** door canoniek voor Ubuntu Linux distributies.

```bash
sudo grep security /etc/apt/sources.list > /tmp/oms-update-security.list LANG=en_US.UTF8 sudo apt-get -s dist-upgrade -oDir::Etc::Sourcelist=/tmp/oms-update-security.list
```

In deze lijst voert u de opdracht uit `grep ^Inst` om alle beveiligings updates in behandeling te ontvangen.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>Scenario: de fout ' kan de update oplossing niet inschakelen ' wordt weer gegeven

### <a name="issue"></a>Probleem

Wanneer u probeert Updatebeheer in te scha kelen in uw Automation-account, krijgt u de volgende fout melding:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Oorzaak

Deze fout kan de volgende oorzaken hebben:

* De netwerk firewall vereisten voor de Log Analytics-agent zijn mogelijk niet correct geconfigureerd. Deze situatie kan ertoe leiden dat de agent mislukt bij het omzetten van de DNS-Url's.

* Updatebeheer doel items zijn onjuist geconfigureerd en de machine ontvangt geen updates zoals verwacht.

* U kunt ook zien dat de machine de status `Non-compliant` onder **naleving** weergeeft. Tegelijkertijd rapporteert **Agent Desktop Analytics** de agent als `Disconnected` .

### <a name="resolution"></a>Oplossing

* Voer de probleem oplosser voor [Windows](update-agent-issues.md#troubleshoot-offline) of [Linux](update-agent-issues-linux.md#troubleshoot-offline)uit, afhankelijk van het besturings systeem.

* Ga naar [netwerk configuratie](../automation-hybrid-runbook-worker.md#network-planning) voor meer informatie over welke adressen en poorten moeten worden toegestaan om updatebeheer te kunnen werken.  

* Controleren op problemen met de scope configuratie. De [Scope configuratie](../update-management/scope-configuration.md) bepaalt welke machines zijn geconfigureerd voor updatebeheer. Als uw computer wordt weer gegeven in uw werk ruimte, maar niet in Updatebeheer, moet u de scope configuratie instellen op de doel computers. Zie [computers in de werk ruimte inschakelen](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace)voor meer informatie over de scope configuratie.

* Verwijder de configuratie van de werk nemer door de stappen in [de Hybrid Runbook worker verwijderen van een on-premises Windows-computer uit](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) te voeren of [de Hybrid Runbook worker te verwijderen van een on-premises Linux-computer](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker).

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Scenario: vervangen update aangegeven als ontbreekt in Updatebeheer

### <a name="issue"></a>Probleem

Oude updates worden voor een Automation-account weer gegeven, zelfs als ze zijn vervangen. Een vervangen update is één die u niet hoeft te installeren, omdat een latere update die hetzelfde beveiligings probleem verhelpt, beschikbaar is. Updatebeheer negeert de vervangen update en maakt deze niet van toepassing in de vervangende update. Zie [update is vervangen](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer)voor informatie over een verwant probleem.

### <a name="cause"></a>Oorzaak

Vervangen updates worden niet geweigerd in Windows Server Update Services (WSUS), zodat ze als niet van toepassing kunnen worden beschouwd.

### <a name="resolution"></a>Oplossing

Wanneer een vervangen update wordt 100 procent niet van toepassing is, moet u de goedkeurings status van die update wijzigen `Declined` in WSUS. Goedkeurings status wijzigen voor alle updates:

1. Selecteer in het Automation-account **updatebeheer** om de status van de machine weer te geven. Zie [Update-evaluaties weer geven](../update-management/view-update-assessments.md).

2. Controleer de vervangen update om er zeker van te zijn dat dit 100 procent niet van toepassing is.

3. Op de WSUS-server van het rapport computers, [weigert u de update](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates).

4. Selecteer **computers** en Forceer opnieuw scannen op naleving in de kolom **naleving** . Zie [updates voor virtuele machines beheren](../update-management/manage-updates-for-vm.md).

5. Herhaal de bovenstaande stappen voor andere vervangen updates.

6. Voor Windows Server Update Services (WSUS), Reinig alle vervangen updates om de infra structuur te vernieuwen met behulp van de wizard WSUS- [Server opruimen](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard).

7. Herhaal deze procedure regel matig om het weergave probleem te corrigeren en Minimaliseer de hoeveelheid schijf ruimte die wordt gebruikt voor update beheer.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Scenario: computers worden niet weer gegeven in de portal onder Updatebeheer

### <a name="issue"></a>Probleem

Uw machines hebben de volgende symptomen:

* Uw computer toont `Not configured` de weer gave van updatebeheer van een virtuele machine.

* Uw computers ontbreken in de Updatebeheer weer gave van uw Azure Automation-account.

* U hebt computers die als `Not assessed` onder **naleving** worden weer gegeven. U ziet echter heartbeat-gegevens in Azure Monitor logboeken voor de Hybrid Runbook Worker maar niet voor Updatebeheer.

### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door lokale configuratie problemen of door een onjuist geconfigureerde Scope configuratie. Mogelijke specifieke oorzaken zijn:

* Mogelijk moet u de Hybrid Runbook Worker opnieuw registreren en opnieuw installeren.

* U hebt mogelijk een quotum gedefinieerd in uw werk ruimte die is bereikt en waardoor verdere gegevens opslag wordt voor komen.

### <a name="resolution"></a>Oplossing

1. Voer de probleem oplosser voor [Windows](update-agent-issues.md#troubleshoot-offline) of [Linux](update-agent-issues-linux.md#troubleshoot-offline)uit, afhankelijk van het besturings systeem.

2. Zorg ervoor dat uw computer rapporteert aan de juiste werk ruimte. Zie [agent connectiviteit controleren op Azure monitor](../../azure-monitor/platform/agent-windows.md#verify-agent-connectivity-to-azure-monitor)voor meer informatie over hoe u dit aspect kunt controleren. Zorg er ook voor dat deze werk ruimte is gekoppeld aan uw Azure Automation-account. Als u wilt bevestigen, gaat u naar uw Automation-account en selecteert u **gekoppelde werk ruimte** onder **gerelateerde resources**.

3. Zorg ervoor dat de computers worden weer gegeven in de Log Analytics werkruimte die aan uw Automation-account is gekoppeld. Voer de volgende query uit in de werk ruimte Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

4. Als uw computer niet in de query resultaten wordt weer geven, is deze niet recent ingecheckt. Er is waarschijnlijk een probleem met de lokale configuratie en u moet [de agent opnieuw installeren](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

5. Als uw computer in de query resultaten wordt weer gegeven, controleert u of er problemen zijn met de scope configuratie. De [Scope configuratie](../update-management/scope-configuration.md) bepaalt welke machines zijn geconfigureerd voor updatebeheer.

6. Als uw computer wordt weer gegeven in uw werk ruimte, maar niet in Updatebeheer, moet u de scope configuratie configureren voor de doel computer. Zie [machines inschakelen in de werk ruimte](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace)voor meer informatie over hoe u dit doet.

7. Voer deze query uit in uw werk ruimte.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

8. Als er een `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota` resultaat wordt weer gegeven, is de in uw werk ruimte gedefinieerde quota bereikt, waardoor er geen gegevens meer kunnen worden opgeslagen. Ga in uw werk ruimte naar **gegevens volume beheer** onder **gebruik en geschatte kosten**, en wijzig of verwijder het quotum.

9. Als uw probleem nog steeds niet is opgelost, volgt u de stappen in [een Windows-Hybrid Runbook worker implementeren](../automation-windows-hrw-install.md) om de Hybrid worker voor Windows opnieuw te installeren. Volg voor Linux de stappen in [een Linux-Hybrid Runbook worker implementeren](../automation-linux-hrw-install.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Scenario: kan Automation-resource provider niet registreren voor abonnementen

### <a name="issue"></a>Probleem

Wanneer u werkt met implementaties van functies in uw Automation-account, treedt de volgende fout op:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Oorzaak

De resource provider voor Automation is niet geregistreerd in het abonnement.

### <a name="resolution"></a>Oplossing

Voer de volgende stappen uit in de Azure Portal om de resource provider Automation te registreren.

1. Selecteer in de lijst van de Azure-service aan de onderkant van de portal **alle services** en selecteer vervolgens **abonnementen** in de algemene service groep.

2. Selecteer uw abonnement.

3. Onder **instellingen** selecteert u **resource providers**.

4. Controleer in de lijst met resource providers of de resource provider micro soft. Automation is geregistreerd.

5. Als dit niet het geval is, registreert u de provider van micro soft. Automation door de stappen te volgen om [fouten bij de registratie van de resource provider op te lossen](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Scenario: geplande update met een dynamisch schema heeft sommige computers niet gemist

### <a name="issue"></a>Probleem

Computers die zijn opgenomen in een update-preview, worden niet allemaal weer gegeven in de lijst met computers die zijn patches tijdens een geplande uitvoering.

### <a name="cause"></a>Oorzaak

Dit probleem kan een van de volgende oorzaken hebben:

* De abonnementen die zijn gedefinieerd in het bereik in een dynamische query, zijn niet geconfigureerd voor de geregistreerde Automation-resource provider.

* De computers zijn niet beschikbaar of hebben niet de juiste Labels wanneer de planning wordt uitgevoerd.

### <a name="resolution"></a>Oplossing

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Abonnementen die niet zijn geconfigureerd voor de geregistreerde Automation-resource provider

Als uw abonnement niet is geconfigureerd voor de Automation-resource provider, kunt u geen gegevens opvragen of ophalen op computers in dat abonnement. Gebruik de volgende stappen om de registratie voor het abonnement te controleren.

1. Open de lijst met Azure-Services in het [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

2. Selecteer **alle services** en selecteer vervolgens **abonnementen** in de algemene service groep.

3. Zoek het abonnement dat is gedefinieerd in het bereik voor uw implementatie.

4. Kies onder **instellingen** de optie **resource providers**.

5. Controleer of de resource provider micro soft. Automation is geregistreerd.

6. Als dit niet het geval is, registreert u de provider van micro soft. Automation door de stappen te volgen om [fouten bij de registratie van de resource provider op te lossen](../../azure-resource-manager/templates/error-register-resource-provider.md).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Machines zijn niet op de juiste wijze of zijn niet juist gelabeld wanneer het schema wordt uitgevoerd

Gebruik de volgende procedure als uw abonnement is geconfigureerd voor de resource provider Automation, maar het uitvoeren van de update planning met de opgegeven [dynamische groepen](../update-management/configure-groups.md) geen enkele machine heeft gemist.

1. Open in het Azure Portal het Automation-account en selecteer **updatebeheer**.

2. Controleer [updatebeheer geschiedenis](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) om te bepalen op welke tijdstippen de update-implementatie is uitgevoerd.

3. Voor computers waarvan u vermoedt dat deze niet zijn Updatebeheer, gebruikt u Azure resource Graph (ARG) om [computer wijzigingen te vinden](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details).

4. Zoek naar wijzigingen in een belang rijke periode, zoals een dag, voordat de update-implementatie werd uitgevoerd.

5. Controleer de zoek resultaten voor eventuele systeem wijzigingen, zoals het verwijderen of bijwerken van wijzigingen, op de computers in deze periode. Deze wijzigingen kunnen de machine status of-tags wijzigen zodat computers niet worden geselecteerd in de lijst met computers wanneer er updates worden geïmplementeerd.

6. Pas de instellingen van de machines en de bron zo nodig aan om te corrigeren voor computer status-of label problemen.

7. Voer de update planning opnieuw uit om ervoor te zorgen dat de implementatie met de opgegeven dynamische groepen alle machines bevat.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Scenario: verwachte machines worden niet weer gegeven in de preview-versie van de dynamische groep

### <a name="issue"></a>Probleem

Vm's voor geselecteerde bereiken van een dynamische groep worden niet weer gegeven in de lijst Azure Portal preview. Deze lijst bestaat uit alle machines die worden opgehaald met een ARG-query voor de geselecteerde bereiken. De bereiken worden gefilterd op computers waarop Hybrid Runbook Workers zijn geïnstalleerd en waarvoor u toegangs machtigingen hebt.

### <a name="cause"></a>Oorzaak

Dit zijn mogelijke oorzaken voor dit probleem:

* U hebt niet de juiste toegang tot de geselecteerde bereiken.
* De opdracht ARG haalt de verwachte machines niet op.
* Hybrid Runbook Worker is niet geïnstalleerd op de computers.

### <a name="resolution"></a>Oplossing 

#### <a name="incorrect-access-on-selected-scopes"></a>Onjuiste toegang voor geselecteerde bereiken

De Azure Portal alleen computers weer geven waarvoor u schrijf toegang hebt binnen een bepaald bereik. Zie [zelf studie: een gebruiker toegang verlenen tot Azure-resources met behulp van de Azure Portal](../../role-based-access-control/quickstart-assign-role-user-portal.md)als u niet over de juiste toegang voor een bereik beschikt.

#### <a name="arg-query-doesnt-return-expected-machines"></a>De query ARG retourneert geen verwachte machines

Volg de onderstaande stappen om erachter te komen of uw query's goed werken.

1. Voer een ARG-query uit zoals hieronder wordt weer gegeven op de Blade resource Graph Explorer in Azure Portal. Deze query imiteert de filters die u hebt geselecteerd tijdens het maken van de dynamische groep in Updatebeheer. Zie [dynamische groepen gebruiken met updatebeheer](../update-management/configure-groups.md).

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

2. Controleer of de computers die u zoekt, worden weer gegeven in de query resultaten.

3. Als de computers niet worden weer gegeven, is er waarschijnlijk een probleem met het filter dat is geselecteerd in de dynamische groep. Pas de groeps configuratie naar wens aan.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>Hybrid Runbook Worker niet geïnstalleerd op computers

Computers worden weer gegeven in de query resultaten van ARG, maar worden nog steeds niet weer gegeven in de preview-versie van dynamische groepen. In dit geval zijn de machines mogelijk niet ingesteld als Hybrid Worker en kunnen Azure Automation en Updatebeheer taken niet worden uitgevoerd. Om ervoor te zorgen dat de computers die u verwacht te zien, worden ingesteld als Hybrid Runbook Workers:

1. Ga in het Azure Portal naar het Automation-account voor een computer die niet correct wordt weer gegeven.

2. Selecteer **Hybrid worker groups** onder **Process Automation**.

3. Selecteer het tabblad **systeem Hybrid worker-groepen** .

4. Controleer of de Hybrid worker aanwezig is voor die machine.

5. Als de computer niet is ingesteld als een Hybrid worker, neemt u aanpassingen op met behulp van instructies [in uw Data Center of in de Cloud met behulp van Hybrid Runbook worker](../automation-hybrid-runbook-worker.md).

6. Voeg de computer toe aan de Hybrid Runbook Worker groep.

7. Herhaal de bovenstaande stappen voor alle computers die niet in de preview-versie zijn weer gegeven.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Scenario: Updatebeheer onderdelen ingeschakeld, terwijl de virtuele machine wordt weer gegeven als geconfigureerd

### <a name="issue"></a>Probleem

Het volgende bericht wordt weer gegeven op een VM 15 minuten nadat de implementatie is gestart:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Oorzaak

Deze fout kan de volgende oorzaken hebben:

* De communicatie met het Automation-account wordt geblokkeerd.

* Er is een dubbele computer naam met verschillende bron computer-Id's. Dit scenario treedt op wanneer een virtuele machine met een bepaalde computer naam in verschillende resource groepen wordt gemaakt en wordt gerapporteerd aan dezelfde logistiek agent-werk ruimte in het abonnement.

* De VM-installatie kopie die wordt geïmplementeerd, is mogelijk afkomstig van een gekloonde computer die niet is voor bereid met het uitvoeren van een systeem voorbereiding (Sysprep) met de Log Analytics-agent voor Windows geïnstalleerd.

### <a name="resolution"></a>Oplossing

Voer de volgende query uit in de Log Analytics-werk ruimte die is gekoppeld aan uw Automation-account om het probleem met de virtuele machine te bepalen.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>Communicatie met het Automation-account is geblokkeerd

Ga naar [netwerk planning](../update-management/overview.md#ports) voor meer informatie over welke adressen en poorten moeten worden toegestaan om updatebeheer te kunnen werken.

#### <a name="duplicate-computer-name"></a>Dubbele computer naam

Wijzig de naam van uw Vm's zodat er unieke namen in hun omgeving worden gegarandeerd.

#### <a name="deployed-image-from-cloned-machine"></a>Geïmplementeerde installatie kopie van gekloonde computer

Als u een gekloonde installatie kopie gebruikt, hebben verschillende computer namen dezelfde bron computer-ID. In dat geval:

1. Verwijder in uw Log Analytics-werk ruimte de virtuele machine uit de opgeslagen zoek opdracht voor de `MicrosoftDefaultScopeConfig-Updates` Scope configuratie als deze wordt weer gegeven. U kunt opgeslagen Zoek opdrachten vinden onder **Algemeen** in uw werk ruimte.

2. Voer de volgende cmdlet uit.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Voer uit `Restart-Service HealthService` om de Health-Service opnieuw te starten. Met deze bewerking wordt de sleutel opnieuw gemaakt en wordt een nieuwe UUID gegenereerd.

4. Als deze methode niet werkt, voert u eerst Sysprep uit op de installatie kopie en installeert u vervolgens de Log Analytics-agent voor Windows.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Scenario: er wordt een fout bij een gekoppeld abonnement weer gegeven wanneer u een update-implementatie maakt voor computers in een andere Azure-Tenant

### <a name="issue"></a>Probleem

U ontvangt de volgende fout melding wanneer u probeert een update-implementatie te maken voor computers in een andere Azure-Tenant:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u een update-implementatie maakt met Azure-Vm's in een andere Tenant die is opgenomen in een update-implementatie.

### <a name="resolution"></a>Oplossing

Gebruik de volgende tijdelijke oplossing om deze items te laten plannen. U kunt de cmdlet [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) met de `ForUpdateConfiguration` para meter gebruiken om een schema te maken. Vervolgens gebruikt u de cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) en geeft u de computers in de andere Tenant door aan de `NonAzureComputer` para meter. In het volgende voor beeld ziet u hoe u dit doet:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Scenario: niet-uitleg bij opnieuw opstarten

### <a name="issue"></a>Probleem

Hoewel u de optie voor het **opnieuw** opstarten van het systeem hebt ingesteld op **nooit opnieuw opstarten**, worden machines nog steeds opnieuw opgestart nadat updates zijn geïnstalleerd.

### <a name="cause"></a>Oorzaak

Windows Update kunnen worden gewijzigd door verschillende register sleutels, waarmee u het gedrag voor opnieuw opstarten kunt wijzigen.

### <a name="resolution"></a>Oplossing

Controleer de register sleutels die worden vermeld onder [Automatische updates configureren door het REGI ster en de](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) [register sleutels te bewerken die worden gebruikt voor het beheren van de herstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) om ervoor te zorgen dat uw computers correct zijn geconfigureerd.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Scenario: in een update-implementatie wordt weer gegeven dat het starten van de computer is mislukt

### <a name="issue"></a>Probleem

Een machine toont een `Failed to start` status. Wanneer u de specifieke Details voor de computer bekijkt, ziet u de volgende fout:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Oorzaak

Deze fout kan een van de volgende oorzaken hebben:

* De machine bestaat niet meer.
* De computer is uitgeschakeld en kan niet worden bereikt.
* De computer heeft een probleem met de netwerk verbinding en daarom is de Hybrid worker op de computer onbereikbaar.
* Er is een update voor de Log Analytics-agent die de bron computer-ID heeft gewijzigd.
* De update-uitvoering is beperkt als u de limiet van 200 gelijktijdige taken in een Automation-account bereikt. Elke implementatie wordt beschouwd als een taak en elke computer in een update-implementatie telt als een taak. Alle andere Automation-taken of update-implementaties die momenteel worden uitgevoerd in uw Automation-account, tellen mee voor de limiet voor gelijktijdige taken.

### <a name="resolution"></a>Oplossing

Gebruik, indien van toepassing, [dynamische groepen](../update-management/configure-groups.md) voor uw update-implementaties. Daarnaast kunt u de volgende stappen uitvoeren.

1. Controleer of uw computer of server aan de [vereisten](../update-management/overview.md#client-requirements)voldoet.
2. Controleer de verbinding met de Hybrid Runbook Worker met behulp van de Hybrid Runbook Worker agent-probleem Oplosser. Zie problemen [met Update agent oplossen](update-agent-issues.md)voor meer informatie over de probleem Oplosser.

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Scenario: updates worden geïnstalleerd zonder een implementatie

### <a name="issue"></a>Probleem

Wanneer u een Windows-computer registreert in Updatebeheer, ziet u dat er updates zijn geïnstalleerd zonder een implementatie.

### <a name="cause"></a>Oorzaak

In Windows worden updates automatisch geïnstalleerd zodra ze beschikbaar zijn. Dit gedrag kan leiden tot Verwar ring als u geen update hebt gepland voor implementatie op de computer.

### <a name="resolution"></a>Oplossing

De  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` register sleutel is standaard ingesteld op een instelling van 4: `auto download and install` .

Voor Updatebeheer-clients wordt aangeraden om deze sleutel in te stellen op 3: `auto download but do not auto install` .

Zie [Automatische updates configureren](/windows/deployment/update/waas-wu-settings#configure-automatic-updates)voor meer informatie.

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Scenario: de computer is al geregistreerd voor een ander account

### <a name="issue"></a>Probleem

Het volgende fout bericht wordt weer gegeven:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Oorzaak

De machine is al geïmplementeerd in een andere werk ruimte voor Updatebeheer.

### <a name="resolution"></a>Oplossing

1. Volg de stappen onder [machines die niet worden weer gegeven in de portal onder updatebeheer](#nologs) om ervoor te zorgen dat de computer aan de juiste werk ruimte rapporteert.
2. Reinig de artefacten op de machine door [de Hybrid runbook Group te verwijderen](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group)en probeer het opnieuw.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Scenario: de computer kan niet communiceren met de service

### <a name="issue"></a>Probleem

U ontvangt een van de volgende fout berichten:

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

Een proxy, gateway of firewall blokkeert mogelijk netwerk communicatie.

### <a name="resolution"></a>Oplossing

Controleer uw netwerk en zorg ervoor dat de juiste poorten en adressen zijn toegestaan. Zie [netwerk vereisten](../automation-hybrid-runbook-worker.md#network-planning) voor een lijst met poorten en adressen die vereist zijn voor updatebeheer-en Hybrid Runbook-werk rollen.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Scenario: kan geen zelfondertekend certificaat maken

### <a name="issue"></a>Probleem

U ontvangt een van de volgende fout berichten:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Oorzaak

Het Hybrid Runbook Worker kan geen zelfondertekend certificaat genereren.

### <a name="resolution"></a>Oplossing

Controleer of het systeem account lees toegang heeft tot de map **C:\ProgramData\Microsoft\Crypto\RSA** en probeer het opnieuw.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Scenario: de geplande update is mislukt met een MaintenanceWindowExceeded-fout

### <a name="issue"></a>Probleem

Het standaard onderhouds venster voor updates is 120 minuten. U kunt het onderhouds venster tot Maxi maal 6 uur of 360 minuten verhogen.

### <a name="resolution"></a>Oplossing

Als u wilt weten waarom dit is gebeurd tijdens het uitvoeren van een update nadat deze is gestart, [controleert u de taak uitvoer](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) van de betrokken computer in de uitvoering. Mogelijk vindt u specifieke fout berichten van uw computers die u kunt onderzoeken en actie onderneemt.  

Bewerk eventuele mislukte geplande update-implementaties en verg root het onderhouds venster.

Zie [updates installeren](../update-management/deploy-updates.md#schedule-an-update-deployment)voor meer informatie over onderhouds Vensters.

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Scenario: de machine wordt weer gegeven als ' niet beoordeeld ' en toont een HRESULT-uitzonde ring

### <a name="issue"></a>Probleem

* U hebt computers die `Not assessed` onder **naleving** zien en er wordt een uitzonderings bericht weer gegeven.
* Er wordt een HRESULT-fout code weer geven in de portal.

### <a name="cause"></a>Oorzaak

De Update Agent (Windows Update-agent in Windows; de Package Manager voor een Linux-distributie) is niet correct geconfigureerd. Updatebeheer is afhankelijk van de Update Agent van de machine om de benodigde updates, de status van de patch en de resultaten van geïmplementeerde patches op te geven. Zonder deze informatie kan Updatebeheer niet op de juiste manier rapporteren over de benodigde patches of geïnstalleerd.

### <a name="resolution"></a>Oplossing

Probeer updates lokaal op de computer uit te voeren. Als deze bewerking mislukt, betekent dit doorgaans dat er een configuratie fout van de Update Agent is.

Dit probleem wordt vaak veroorzaakt door netwerk configuratie-en Firewall problemen. Gebruik de volgende controles om het probleem te verhelpen.

* Raadpleeg voor Linux de juiste documentatie om ervoor te zorgen dat u het netwerk eindpunt van uw pakket opslagplaats kunt bereiken.

* Controleer voor Windows of uw agent configuratie wordt weer gegeven in [updates die niet worden gedownload vanaf het intranet eindpunt (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Als de computers zijn geconfigureerd voor Windows Update, moet u ervoor zorgen dat u de eind punten kunt bereiken die worden beschreven in [problemen die betrekking hebben op http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Als de computers zijn geconfigureerd voor Windows Server Update Services (WSUS), moet u ervoor zorgen dat u de WSUS-server kunt bereiken die is geconfigureerd met de [register sleutel WUServer](/windows/deployment/update/waas-wu-settings).

Als u een HRESULT ziet, dubbelklikt u op de uitzonde ring die rood wordt weer gegeven om het volledige uitzonderings bericht weer te geven. Bekijk de volgende tabel voor mogelijke oplossingen of aanbevolen acties.

|Uitzondering  |Oplossing of actie  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Zoek in de [lijst met fout codes voor Windows Update](https://support.microsoft.com/help/938205/windows-update-error-code-list) naar aanvullende informatie over de oorzaak van de uitzonde ring.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Deze duiden op problemen met de netwerk verbinding. Zorg ervoor dat uw computer netwerk verbinding heeft met Updatebeheer. Zie de sectie [netwerk planning](../update-management/overview.md#ports) voor een lijst met vereiste poorten en adressen.        |
|`0x8024001E`| De update bewerking is niet voltooid omdat de service of het systeem is afgesloten.|
|`0x8024002E`| Windows Update-service is uitgeschakeld.|
|`0x8024402C`     | Als u een WSUS-server gebruikt, moet u ervoor zorgen dat de register waarden voor `WUServer` en `WUStatusServer` onder de  `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` register sleutel de juiste WSUS-server opgeven.        |
|`0x80072EE2`|Er is een probleem met de netwerk verbinding of een probleem in het praten met een geconfigureerde WSUS-server. Controleer de WSUS-instellingen en controleer of de service toegankelijk is vanaf de client.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Zorg ervoor dat de Windows Update-service (wuauserv) wordt uitgevoerd en niet is uitgeschakeld.        |
|`0x80070005`| Een fout bericht over geweigerde toegang kan een van de volgende oorzaken hebben:<br> Geïnfecteerde computer<br> De instellingen voor Windows Update zijn niet juist geconfigureerd<br> Fout bij bestands machtiging met map%WinDir%\SoftwareDistribution<br> Onvoldoende schijf ruimte op het systeem station (C:).
|Een andere algemene uitzonde ring     | Voer een zoek opdracht op Internet uit voor mogelijke oplossingen en werk samen met uw lokale IT-ondersteuning.         |

Door het **%windir%\Windowsupdate.log** -bestand te bekijken, kunt u ook mogelijke oorzaken bepalen. Zie [het bestand WindowsUpdate. log lezen](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file)voor meer informatie over het lezen van het logboek.

U kunt de [Windows Update probleemoplossings](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) programma ook downloaden en uitvoeren om te controleren of er problemen zijn met Windows Update op de computer.

> [!NOTE]
> De documentatie van de [Windows Update-probleemoplossings](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) functie geeft aan dat deze is voor gebruik op Windows-clients, maar ook werkt op Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Scenario: update uitvoering retourneert status mislukt (Linux)

### <a name="issue"></a>Probleem

Er wordt een update-uitvoering gestart, maar er zijn fouten opgetreden tijdens de uitvoering.

### <a name="cause"></a>Oorzaak

Mogelijke oorzaken:

* Pakket beheer is beschadigd.
* Update Agent (WUA voor Windows, distributie pakket beheer voor Linux) is onjuist geconfigureerd.
* Specifieke pakketten zijn van invloed op cloud-gebaseerde patches.
* De computer is onbereikbaar.
* Updates bevatten afhankelijkheden die niet zijn opgelost.

### <a name="resolution"></a>Oplossing

Als er fouten optreden tijdens het uitvoeren van een update nadat deze is gestart, [controleert u de taak uitvoer](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) van de betrokken computer in de uitvoering. Mogelijk vindt u specifieke fout berichten van uw computers die u kunt onderzoeken en actie onderneemt. Voor Updatebeheer moet pakket beheer in orde zijn voor geslaagde update-implementaties.

Als specifieke patches, pakketten of updates onmiddellijk worden weer gegeven voordat de taak mislukt, kunt u deze items [uitsluiten](../update-management/deploy-updates.md#schedule-an-update-deployment) van de volgende update-implementatie. Zie [Windows Update-logboek bestanden](/windows/deployment/update/windows-update-logs)voor informatie over het verzamelen van logboek gegevens van Windows Update.

Als u een probleem met een patches niet kunt oplossen, maakt u een kopie van het **/var/opt/Microsoft/omsagent/run/automationworker/omsupdatemgmt.log** -bestand en behoudt u dit voor het oplossen van problemen voordat de volgende update-implementatie wordt gestart.

## <a name="patches-arent-installed"></a>Patches zijn niet geïnstalleerd

### <a name="machines-dont-install-updates"></a>Updates worden niet geïnstalleerd op machines

Voer de updates rechtstreeks op de machine uit. Als de computer de updates niet kan Toep assen, raadpleegt u de [lijst met mogelijke fouten in de hand leiding voor het oplossen van problemen](#hresult).

Als updates lokaal worden uitgevoerd, kunt u de agent op de machine verwijderen en opnieuw installeren door de richt lijnen te volgen in [een VM verwijderen uit updatebeheer](../update-management/remove-vms.md).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Ik weet dat er updates beschikbaar zijn, maar ze worden niet weer gegeven als beschikbaar op mijn computers

Dit gebeurt vaak als machines zijn geconfigureerd voor het ophalen van updates van WSUS of micro soft endpoint Configuration Manager, maar WSUS en Configuration Manager de updates niet hebben goedgekeurd.

U kunt controleren of de computers zijn geconfigureerd voor WSUS en SCCM door kruis verwijzingen te maken naar de register `UseWUServer` sleutel naar de register sleutels in de [Automatische updates configureren door de register sectie van dit artikel te bewerken](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Als updates niet zijn goedgekeurd in WSUS, zijn ze niet geïnstalleerd. U kunt controleren op niet-goedgekeurde updates in Log Analytics door de volgende query uit te voeren.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>Updates verschijnen na installatie maar ik kan ze niet vinden op mijn machine

Updates worden vaak verdrongen door andere updates. Zie voor meer informatie [update is vervangen](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) in de Windows Update probleemoplossings handleiding.

### <a name="installing-updates-by-classification-on-linux"></a>Updates per classificatie installeren op Linux

Het per classificatie implementeren van updates naar Linux ('Essentiële en beveiligingsupdates'), heeft belangrijke beperkingen, met name voor CentOS. Deze beperkingen worden beschreven op de [pagina overzicht van updatebeheer](../update-management/overview.md#linux).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 ontbreekt consistent

KB2267602 is de [definitie-update voor Windows Defender](https://www.microsoft.com/wdsi/definitions). Het wordt dagelijks bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor aanvullende ondersteuning.

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
