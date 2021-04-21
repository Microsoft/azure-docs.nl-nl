---
title: Problemen Azure Automation Hybrid Runbook Worker oplossen
description: In dit artikel wordt beschreven hoe u problemen kunt oplossen die zich voordoen met Azure Automation Hybrid Runbook Workers.
services: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 906e38b28015bd70cf1c97ba9323094d64a12c94
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830840"
---
# <a name="troubleshoot-hybrid-runbook-worker-issues"></a>Problemen met Hybrid Runbook Worker oplossen

Dit artikel bevat informatie over het oplossen en oplossen van problemen met Azure Automation Hybrid Runbook Workers. Zie overzicht van Hybrid Runbook Worker [voor algemene informatie.](../automation-hybrid-runbook-worker.md)

## <a name="general"></a>Algemeen

De Hybrid Runbook Worker is afhankelijk van een agent om te communiceren met uw Azure Automation-account om de werkmedewerker te registreren, runbooktaken te ontvangen en de rapportstatus te rapporteren. Voor Windows is deze agent de Log Analytics-agent voor Windows. Voor Linux is dit de Log Analytics-agent voor Linux.

### <a name="scenario-runbook-execution-fails"></a><a name="runbook-execution-fails"></a>Scenario: Uitvoeren van runbook mislukt

#### <a name="issue"></a>Probleem

De uitvoering van runbook mislukt en u ontvangt het volgende foutbericht:

`The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times.`

Uw runbook wordt kort nadat het drie keer heeft geprobeerd uit te voeren, tijdelijk tijdelijk opgeschort. Er zijn voorwaarden die het uitvoeren van het runbook kunnen onderbreken. Het gerelateerde foutbericht bevat mogelijk geen aanvullende informatie.

#### <a name="cause"></a>Oorzaak

De volgende oorzaken zijn mogelijk:

* De runbooks kunnen niet worden geverifieerd met lokale resources.
* De hybrid worker staat achter een proxy of firewall.
* De computer die is geconfigureerd om de Hybrid Runbook Worker voldoet niet aan de minimale hardwarevereisten.

#### <a name="resolution"></a>Oplossing

Controleer of de computer uitgaande toegang heeft tot **\* .azure-automation.net** op poort 443.

Computers met de Hybrid Runbook Worker moeten voldoen aan de minimale hardwarevereisten voordat de werkmedewerker is geconfigureerd om deze functie te hosten. Runbooks en het achtergrondproces dat ze gebruiken, kunnen ertoe leiden dat het systeem te veel wordt gebruikt en leiden tot vertragingen of time-outs van runbook-taak.

Controleer of de computer voor het uitvoeren van Hybrid Runbook Worker-functie voldoet aan de minimale hardwarevereisten. Als dit het geval is, controleert u het CPU- en geheugengebruik om een correlatie te bepalen tussen de prestaties van Hybrid Runbook Worker processen en Windows. Elke geheugen- of CPU-druk kan erop wijzen dat resources moeten worden geupgraded. U kunt ook een andere rekenresource selecteren die ondersteuning biedt voor de minimale vereisten en schaal wanneer de vraag naar werkbelasting aangeeft dat een toename noodzakelijk is.

Controleer het **Microsoft-SMA-gebeurtenislogboek** op een bijbehorende gebeurtenis met de beschrijving `Win32 Process Exited with code [4294967295]` . De oorzaak van deze fout is dat u geen verificatie hebt geconfigureerd in uw runbooks of de Uitvoeren als-referenties hebt opgegeven voor de Hybrid Runbook Worker groep. Controleer runbookmachtigingen in [Runbooks](../automation-hrw-run-runbooks.md) uitvoeren op een Hybrid Runbook Worker om te bevestigen dat u de verificatie voor uw runbooks correct hebt geconfigureerd.

### <a name="scenario-event-15011-in-the-hybrid-runbook-worker"></a><a name="cannot-connect-signalr"></a>Scenario: Gebeurtenis 15011 in de Hybrid Runbook Worker

#### <a name="issue"></a>Probleem

De Hybrid Runbook Worker ontvangt gebeurtenis 15011, wat aangeeft dat een queryresultaat ongeldig is. De volgende fout wordt weergegeven wanneer de werkmedewerker probeert een verbinding met de [SignalR-server te openen.](/aspnet/core/signalr/introduction)

`[AccountId={c7d22bd3-47b2-4144-bf88-97940102f6ca}]
[Uri=https://cc-jobruntimedata-prod-su1.azure-automation.net/notifications/hub][Exception=System.TimeoutException: Transport timed out trying to connect
   at System.Runtime.ExceptionServices.ExceptionDispatchInfo.Throw()
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at JobRuntimeData.NotificationsClient.JobRuntimeDataServiceSignalRClient.<Start>d__45.MoveNext()
`

#### <a name="cause"></a>Oorzaak

De Hybrid Runbook Worker is niet correct geconfigureerd voor de geautomatiseerde functie-implementatie, bijvoorbeeld voor Updatebeheer. De implementatie bevat een onderdeel dat de VM verbindt met de Log Analytics-werkruimte. Het PowerShell-script zoekt naar de werkruimte in het abonnement met de opgegeven naam. In dit geval maakt de Log Analytics-werkruimte gebruik van een ander abonnement. Het script kan de werkruimte niet vinden en probeert er een te maken, maar de naam is al gebruikt. Als gevolg hiervan mislukt de implementatie.

#### <a name="resolution"></a>Oplossing

U hebt twee opties om dit probleem op te lossen:

* Wijzig het PowerShell-script om te zoeken naar de Log Analytics-werkruimte in een ander abonnement. Dit is een goede oplossing als u van plan bent om in de toekomst Hybrid Runbook Worker machines te implementeren.

* Configureer de werkmachine handmatig om te worden uitgevoerd in een Orchestrator-sandbox. Voer vervolgens een runbook uit dat is gemaakt in het Azure Automation-account op de worker om de functionaliteit te testen.

### <a name="scenario-windows-azure-vms-automatically-dropped-from-a-hybrid-worker-group"></a><a name="vm-automatically-dropped"></a>Scenario: Windows Azure-VM's worden automatisch uit een Hybrid Worker-groep weggetrokken

#### <a name="issue"></a>Probleem

U kunt de virtuele Hybrid Runbook Worker of virtuele machines niet zien wanneer de werkmachine lange tijd is uitgeschakeld.

#### <a name="cause"></a>Oorzaak

De Hybrid Runbook Worker machine is al meer dan 30 Azure Automation niet gepingd. Als gevolg hiervan heeft Automation de Hybrid Runbook Worker of de systeemwerkergroep verwijderd. 

#### <a name="resolution"></a>Oplossing

Start de werkmachine en laat deze vervolgens opnieuw registreren met Azure Automation. Zie Deploy a Windows Hybrid Runbook Worker (Een Windows-Hybrid Runbook Worker) voor instructies over het installeren van de runbookomgeving en het maken van verbinding met [Azure Automation.](../automation-windows-hrw-install.md)

### <a name="scenario-no-certificate-was-found-in-the-certificate-store-on-the-hybrid-runbook-worker"></a><a name="no-cert-found"></a>Scenario: Er is geen certificaat gevonden in het certificaatopslag op de Hybrid Runbook Worker

#### <a name="issue"></a>Probleem

Een runbook dat wordt uitgevoerd op een Hybrid Runbook Worker mislukt met het volgende foutbericht:

`Connect-AzAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000`  
`At line:3 char:1`  
`+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...`  
`+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~`  
`    + CategoryInfo          : CloseError: (:) [Connect-AzAccount],ArgumentException`  
`    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand`

#### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer u probeert een [Uitvoeren als-account](../automation-security-overview.md#run-as-accounts) te gebruiken in een runbook dat wordt uitgevoerd op een Hybrid Runbook Worker waar het Uitvoeren als-accountcertificaat niet aanwezig is. Hybrid Runbook Workers hebben het certificaatactivum niet standaard lokaal. Voor het Uitvoeren als-account moet deze asset correct werken.

#### <a name="resolution"></a>Oplossing

Als uw Hybrid Runbook Worker een azure-VM is, kunt u in plaats daarvan [runbookverificatie gebruiken met beheerde identiteiten.](../automation-hrw-run-runbooks.md#runbook-auth-managed-identities) Dit scenario vereenvoudigt verificatie doordat u zich kunt verifiëren bij Azure-resources met behulp van de beheerde identiteit van de Azure-VM in plaats van het Uitvoeren als-account. Wanneer de Hybrid Runbook Worker een on-premises machine is, moet u het Uitvoeren als-accountcertificaat op de computer installeren. Zie de stappen voor het uitvoeren van het **PowerShell-runbook Export-RunAsCertificateToHybridWorker** in Runbooks op een Hybrid Runbook Worker voor meer informatie over het installeren [van het certificaat.](../automation-hrw-run-runbooks.md)

### <a name="scenario-error-403-during-registration-of-a-hybrid-runbook-worker"></a><a name="error-403-on-registration"></a>Scenario: Fout 403 tijdens de registratie van een Hybrid Runbook Worker

#### <a name="issue"></a>Probleem

De initiële registratiefase van de werkmedewerker mislukt en u krijgt de volgende foutmelding (403):

`Forbidden: You don't have permission to access / on this server.`

#### <a name="cause"></a>Oorzaak

De volgende problemen zijn mogelijke oorzaken:

* De instellingen van de agent hebben een verkeerd getypte werkruimte-id of werkruimtesleutel (primair). 
* De Hybrid Runbook Worker kan de configuratie niet downloaden, waardoor een fout bij het koppelen van een account wordt veroorzaakt. Wanneer Azure functies op machines in staat stelt, worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte en een Automation-account. Het is ook mogelijk dat er een onjuiste datum of tijd is ingesteld op de computer. Als de tijd +/- 15 minuten na de huidige tijd ligt, mislukt de implementatie van functies.

#### <a name="resolution"></a>Oplossing

##### <a name="mistyped-workspace-id-or-key"></a>Verkeerd getypte werkruimte-id of -sleutel
Zie Een werkruimte toevoegen of verwijderen - [Windows-agent](../../azure-monitor/platform/agent-manage.md#windows-agent) voor de Windows-agent of Een werkruimte toevoegen of verwijderen [- Linux-agent](../../azure-monitor/platform/agent-manage.md#linux-agent) voor de Linux-agent om te controleren of de werkruimte-id of werkruimtesleutel van de agent onjuist is getypt. Zorg ervoor dat u de volledige tekenreeks in de Azure Portal selecteert en kopieer en plak deze zorgvuldig.

##### <a name="configuration-not-downloaded"></a>Configuratie niet gedownload

Uw Log Analytics-werkruimte en Automation-account moeten zich in een gekoppelde regio hebben. Zie toewijzingen voor Azure Automation Log Analytics-werkruimte voor een lijst met [ondersteunde regio's.](../how-to/region-mappings.md)

Mogelijk moet u ook de datum of tijdzone van uw computer bijwerken. Als u een aangepast tijdsbereik selecteert, moet u ervoor zorgen dat het bereik zich in UTC bevindt, wat kan verschillen van uw lokale tijdzone.

### <a name="scenario-set-azstorageblobcontent-fails-on-a-hybrid-runbook-worker"></a><a name="set-azstorageblobcontent-execution-fails"></a>Scenario: Set-AzStorageBlobContent mislukt op een Hybrid Runbook Worker 

#### <a name="issue"></a>Probleem

Runbook mislukt wanneer wordt geprobeerd uit te `Set-AzStorageBlobContent` voeren en u ontvangt het volgende foutbericht:

`Set-AzStorageBlobContent : Failed to open file xxxxxxxxxxxxxxxx: Illegal characters in path`

#### <a name="cause"></a>Oorzaak

 Deze fout wordt veroorzaakt door het gedrag van de lange bestandsnaam van aanroepen `[System.IO.Path]::GetFullPath()` waaraan UNC-paden worden toegevoegd.

#### <a name="resolution"></a>Oplossing

Als tijdelijke oplossing kunt u een configuratiebestand met de naam maken `OrchestratorSandbox.exe.config` met de volgende inhoud:

```azurecli
<configuration>
  <runtime>
    <AppContextSwitchOverrides value="Switch.System.IO.UseLegacyPathHandling=false" />
  </runtime>
</configuration>
```

Plaats dit bestand in dezelfde map als het uitvoerbare bestand `OrchestratorSandbox.exe` . Bijvoorbeeld:

`%ProgramFiles%\Microsoft Monitoring Agent\Agent\AzureAutomation\7.3.702.0\HybridAgent`

>[!Note]
> Als u de agent upgradet, wordt dit configuratiebestand verwijderd en moet het opnieuw worden gemaakt.

## <a name="linux"></a>Linux

De Linux Hybrid Runbook Worker is afhankelijk van de [Log Analytics-agent](../../azure-monitor/agents/log-analytics-agent.md) voor Linux om te communiceren met uw Automation-account om de werkmedewerker te registreren, runbooktaken te ontvangen en de rapportstatus te rapporteren. Als de registratie van de werkmedewerker mislukt, zijn er enkele mogelijke oorzaken voor de fout.

### <a name="scenario-linux-hybrid-runbook-worker-receives-prompt-for-a-password-when-signing-a-runbook"></a><a name="prompt-for-password"></a>Scenario: Linux Hybrid Runbook Worker een wachtwoord wordt gevraagd bij het ondertekenen van een runbook

#### <a name="issue"></a>Probleem

Als u `sudo` de opdracht voor een Linux-Hybrid Runbook Worker wordt een onverwachte prompt voor een wachtwoord opgehaald.

#### <a name="cause"></a>Oorzaak

Het **nxautomationuser-account** voor de Log Analytics-agent voor Linux is niet correct geconfigureerd in het **sudoers-bestand.** De Hybrid Runbook Worker de juiste configuratie van accountmachtigingen en andere gegevens nodig, zodat runbooks op de Linux Runbook Worker kunnen worden ondertekenen.

#### <a name="resolution"></a>Oplossing

* Zorg ervoor dat Hybrid Runbook Worker GnuPG (GPG) het uitvoerbare bestand op de computer heeft.

* Controleer de configuratie van het **nxautomationuser-account** in het **sudoers-bestand.** Zie [Runbooks uitvoeren op een Hybrid Runbook Worker.](../automation-hrw-run-runbooks.md)

### <a name="scenario-log-analytics-agent-for-linux-isnt-running"></a><a name="oms-agent-not-running"></a>Scenario: Log Analytics-agent voor Linux wordt niet uitgevoerd

#### <a name="issue"></a>Probleem

De Log Analytics-agent voor Linux wordt niet uitgevoerd.

#### <a name="cause"></a>Oorzaak

Als de agent niet wordt uitgevoerd, wordt voorkomen dat de Linux Hybrid Runbook Worker communiceert met Azure Automation. De agent wordt mogelijk om verschillende redenen niet uitgevoerd.

#### <a name="resolution"></a>Oplossing

 Controleer of de agent wordt uitgevoerd door de opdracht in te `ps -ef | grep python` voeren. De uitvoer ziet er als volgt uit. De Python-processen met het **gebruikersaccount nxautomation.** Als de Azure Automation functie niet is ingeschakeld, worden geen van de volgende processen uitgevoerd.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

De volgende lijst bevat de processen die zijn gestart voor een Linux-Hybrid Runbook Worker. Ze bevinden zich allemaal in de map /var/opt/microsoft/omsagent/state/automationworker/.

* **oms.conf:** het werkproces van de werkbeheerder. Deze wordt rechtstreeks vanuit DSC gestart.
* **worker.conf:** het automatisch geregistreerde hybrid worker-proces. Het wordt gestart door de werkbeheerder. Dit proces wordt gebruikt door Updatebeheer en is transparant voor de gebruiker. Dit proces is niet aanwezig als Updatebeheer niet is ingeschakeld op de computer.
* **worker/worker.conf:** het HYBRID Worker-proces van DOEF. Het HYBRID Worker-proces van DOEI wordt gebruikt om runbooks van gebruikers uit te voeren op de Hybrid Runbook Worker. Het verschilt alleen van het automatisch geregistreerde hybrid worker-proces in de belangrijkste details dat er een andere configuratie wordt gebruikt. Dit proces is niet aanwezig als Azure Automation is uitgeschakeld en de LINUX-Hybrid Worker VAN LINUX niet is geregistreerd.

Als de agent niet wordt uitgevoerd, voer dan de volgende opdracht uit om de service te starten: `sudo /opt/microsoft/omsagent/bin/service_control restart` .

### <a name="scenario-the-specified-class-doesnt-exist"></a><a name="class-does-not-exist"></a>Scenario: De opgegeven klasse bestaat niet

Als u het foutbericht `The specified class does not exist..` ziet in **/var/opt/microsoft/omsconfig/omsconfig.log,** moet de Log Analytics-agent voor Linux worden bijgewerkt. Voer de volgende opdracht uit om de agent opnieuw te installeren.

```Bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

De Windows Hybrid Runbook Worker is afhankelijk van de [Log Analytics-agent](../../azure-monitor/agents/log-analytics-agent.md) voor Windows om te communiceren met uw Automation-account om de werkmedewerker te registreren, runbooktaken te ontvangen en de rapportstatus te rapporteren. Als de registratie van de werkmedewerker mislukt, bevat deze sectie enkele mogelijke redenen.

### <a name="scenario-the-log-analytics-agent-for-windows-isnt-running"></a><a name="mma-not-running"></a>Scenario: De Log Analytics-agent voor Windows wordt niet uitgevoerd

#### <a name="issue"></a>Probleem

De `healthservice` wordt niet uitgevoerd op de Hybrid Runbook Worker machine.

#### <a name="cause"></a>Oorzaak

Als de Log Analytics voor Windows-service niet wordt uitgevoerd, kan de Hybrid Runbook Worker niet communiceren met Azure Automation.

#### <a name="resolution"></a>Oplossing

Controleer of de agent wordt uitgevoerd door de volgende opdracht in PowerShell in te voeren: `Get-Service healthservice` . Als de service is gestopt, voert u de volgende opdracht in PowerShell in om de service te starten: `Start-Service healthservice` .

### <a name="scenario-event-4502-in-the-operations-manager-log"></a><a name="event-4502"></a>Scenario: Gebeurtenis 4502 in het Operations Manager logboek

#### <a name="issue"></a>Probleem

In het **gebeurtenislogboek Application and Services logs\Operations Manager** ziet u gebeurtenis 4502 en een gebeurtenisbericht met `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent` de volgende beschrijving:<br>`The certificate presented by the service \<wsid\>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.`

#### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt doordat uw proxy of netwerkfirewall de communicatie met Microsoft Azure. Controleer of de computer uitgaande toegang heeft tot **\* .azure-automation.net** op poort 443.

#### <a name="resolution"></a>Oplossing

Logboeken worden lokaal opgeslagen op elke Hybrid Worker op C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. U kunt controleren of er waarschuwings- of foutgebeurtenissen zijn in de logboeken Toepassings- en **services\Microsoft-SMA\Bewerkingen-** en toepassings- en serviceslogboeken\Operations Manager gebeurtenislogboeken.  Deze logboeken geven aan dat er een verbindingsprobleem of een ander type probleem is dat van invloed is op het inschakelen van de rol voor Azure Automation, of een probleem dat is aangetroffen bij normale bewerkingen. Zie Problemen met de Log Analytics Windows-agent oplossen voor meer hulp bij het oplossen van problemen met de [Log Analytics-agent.](../../azure-monitor/agents/agent-windows-troubleshoot.md)

Hybrid Workers verzenden [Runbook-uitvoer](../automation-runbook-output-and-messages.md) en berichten naar Azure Automation op dezelfde manier als runbooktaken die worden uitgevoerd in de cloud uitvoer en berichten verzenden. U kunt de stromen Uitgebreid en Voortgang op dezelfde manier inschakelen als voor runbooks.

### <a name="scenario-orchestratorsandboxexe-cant-connect-to-microsoft-365-through-proxy"></a>Scenario: Orchestrator.Sandbox.exe kunnen geen verbinding maken met Microsoft 365 via proxy

#### <a name="issue"></a>Probleem

Een script dat wordt uitgevoerd op een Windows-Hybrid Runbook Worker kan geen verbinding maken zoals verwacht om Microsoft 365 in een Orchestrator-sandbox. Het script maakt gebruik van [Connect-MsolService](/powershell/module/msonline/connect-msolservice) voor de verbinding. 

Als u de **Orchestrator.Sandbox.exe.config** proxy en de bypass-lijst in te stellen, maakt de sandbox nog steeds geen goede verbinding. Een **Powershell_ise.exe.config** bestand met dezelfde proxy en bypass lijst instellingen lijkt te werken zoals u verwacht. Service Management Automation (SMA)-logboeken en PowerShell-logboeken bieden geen informatie met betrekking tot proxy.

#### <a name="cause"></a>Oorzaak

De verbinding met Active Directory Federation Services (AD FS) op de server kan de proxy niet omzeilen. Onthoud dat een PowerShell-sandbox wordt uitgevoerd als de aangemelde gebruiker. Een Orchestrator-sandbox is echter sterk aangepast en kan de instellingen **voor** Orchestrator.Sandbox.exe.confignegeren. Het heeft speciale code voor het verwerken van proxy-instellingen voor machines of Log Analytics-agent, maar niet voor het verwerken van andere aangepaste proxyinstellingen. 

#### <a name="resolution"></a>Oplossing

U kunt het probleem voor de Orchestrator-sandbox oplossen door uw script te migreren om de Azure Active Directory-modules te gebruiken in plaats van de MSOnline-module voor PowerShell-cmdlets. Zie Migreren van Orchestrator naar [Azure Automation (bèta) voor meer informatie.](../automation-orchestrator-migration.md)

Als u de cmdlets van de MSOnline-module wilt blijven gebruiken, wijzigt u het script om [Invoke-Command te gebruiken.](/powershell/module/microsoft.powershell.core/invoke-command) Geef waarden op voor de `ComputerName` `Credential` parameters en . 

```powershell
$Credential = Get-AutomationPSCredential -Name MyProxyAccessibleCredential
Invoke-Command -ComputerName $env:COMPUTERNAME -Credential $Credential 
{ Connect-MsolService … }
```

Met deze codewijziging wordt een volledig nieuwe PowerShell-sessie gestart in de context van de opgegeven referenties. Het moet het verkeer in staat stellen om via een proxyserver te stromen waarmee de actieve gebruiker wordt authenticeert.

>[!NOTE]
>Deze oplossing maakt het niet nodig om het sandbox-configuratiebestand te bewerken. Zelfs als u erin slaagt om het configuratiebestand te laten werken met uw script, wordt het bestand gewist telkens wanneer de Hybrid Runbook Worker agent wordt bijgewerkt.

### <a name="scenario-hybrid-runbook-worker-not-reporting"></a><a name="corrupt-cache"></a>Scenario: Hybrid Runbook Worker rapportage niet

#### <a name="issue"></a>Probleem

Uw Hybrid Runbook Worker machine wordt uitgevoerd, maar u ziet geen heartbeat-gegevens voor de machine in de werkruimte.

De volgende voorbeeldquery toont de machines in een werkruimte en hun laatste heartbeat:

```kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt door een beschadigde cache op de Hybrid Runbook Worker.

#### <a name="resolution"></a>Oplossing

U kunt dit probleem oplossen door u aan te melden bij Hybrid Runbook Worker en het volgende script uit te voeren. Met dit script wordt de Log Analytics-agent voor Windows gestopt, wordt de cache verwijderd en wordt de service opnieuw gestart. Deze actie dwingt de Hybrid Runbook Worker de configuratie opnieuw te downloaden van Azure Automation.

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="scenario-you-cant-add-a-windows-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: u kunt geen Windows-Hybrid Runbook Worker

#### <a name="issue"></a>Probleem

U ontvangt het volgende bericht wanneer u een Hybrid Runbook Worker met behulp van de `Add-HybridRunbookWorker` cmdlet :

`Machine is already registered`

#### <a name="cause"></a>Oorzaak

Dit probleem kan worden veroorzaakt als de machine al is geregistreerd bij een ander Automation-account of als u de Hybrid Runbook Worker opnieuw wilt toevoegen nadat u deze van een computer hebt verwijderd.

#### <a name="resolution"></a>Oplossing

U kunt dit probleem oplossen door de volgende registersleutel te verwijderen, opnieuw op te `HealthService` starten en de `Add-HybridRunbookWorker` cmdlet opnieuw te proberen.

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

### <a name="scenario-you-cant-add-a-linux-hybrid-runbook-worker"></a><a name="already-registered"></a>Scenario: U kunt geen Linux-Hybrid Runbook Worker

#### <a name="issue"></a>Probleem

U ontvangt het volgende bericht wanneer u een Hybrid Runbook Worker met behulp van het `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` Python-script:

`Unable to register, an existing worker was found. Please deregister any existing worker and try again.`

Daarnaast probeert u de registratie van een Hybrid Runbook Worker met behulp van het `sudo python /opt/microsoft/omsconfig/.../onboarding.py --deregister` Python-script:

`Failed to deregister worker. [response_status=404]`

#### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als de machine al is geregistreerd bij een ander Automation-account, als de Azure Hybrid Worker-groep is verwijderd of als u de Hybrid Runbook Worker opnieuw probeert toe te voegen nadat u deze van een computer hebt verwijderd.

#### <a name="resolution"></a>Oplossing

Ga als volgt te werk om het probleem op te lossen:

1. Verwijder de agent `sudo sh onboard_agent.sh --purge` .

1. Voer deze opdrachten uit:

   ```
   sudo mv -f /home/nxautomation/state/worker.conf /home/nxautomation/state/worker.conf_old
   sudo mv -f /home/nxautomation/state/worker_diy.crt /home/nxautomation/state/worker_diy.crt_old
   sudo mv -f /home/nxautomation/state/worker_diy.key /home/nxautomation/state/worker_diy.key_old
   ```

1. Onboard de agent `sudo sh onboard_agent.sh -w <workspace id> -s <workspace key> -d opinsights.azure.com` opnieuw.

1. Wacht tot de `/opt/microsoft/omsconfig/modules/nxOMSAutomationWorker` map is ingevuld.

1. Probeer het `sudo python /opt/microsoft/omsconfig/.../onboarding.py --register` Python-script opnieuw.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem hier niet ziet of als u het probleem niet kunt oplossen, probeert u een van de volgende kanalen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums.](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de klantervaring. Ondersteuning voor Azure maakt de Azure-community verbinding met antwoorden, ondersteuning en experts.
* Een incident ondersteuning voor Azure indienen. Ga naar de [ondersteuning voor Azure site](https://azure.microsoft.com/support/options/)en selecteer **Ondersteuning krijgen.**
